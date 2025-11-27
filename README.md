# lab07

ques1 :
#include <stdio.h>
#include <pthread.h>

void* thread_func(void* arg) {
    int id = *(int*)arg;
    printf("Hello from Thread #%d!\n", id);
    pthread_exit(NULL);
}

int main() {
    printf("Name: Muhammad Shahzeb Alam\nRoll No: SE-23055\n\n");

    pthread_t threads[5];
    int ids[5];

    for (int i = 0; i < 5; i++) {
        ids[i] = i;
        pthread_create(&threads[i], NULL, thread_func, &ids[i]);
    }

    for (int i = 0; i < 5; i++)
        pthread_join(threads[i], NULL);

    printf("All threads are done.\n");
    return 0;
}



ques2:
#include <stdio.h>
#include <stdlib.h>
#include <pthread.h>

typedef struct {
    int thread_id;
} ThreadData;

void* run(void* arg) {
    ThreadData* data = (ThreadData*)arg;
    printf("Thread received ID: %d\n", data->thread_id);
    pthread_exit(NULL);
}

int main() {
    printf("Name: Muhammad Shahzeb Alam\nRoll No: SE-23055\n\n");

    pthread_t threads[4];
    ThreadData* info[4];

    for (int i = 0; i < 4; i++) {
        info[i] = malloc(sizeof(ThreadData));
        info[i]->thread_id = i;
        pthread_create(&threads[i], NULL, run, info[i]);
    }

    for (int i = 0; i < 4; i++) {
        pthread_join(threads[i], NULL);
        free(info[i]);
    }

    return 0;
}


ques3:
#include <stdio.h>
#include <pthread.h>

#define SIZE 1000000
#define THREADS 4

long long global_sum = 0;
pthread_mutex_t lock;

int arr[SIZE];

void* sum_worker(void* arg) {
    int id = *(int*)arg;

    int start = id * (SIZE / THREADS);
    int end = start + (SIZE / THREADS);

    long long local_sum = 0;

    for (int i = start; i < end; i++)
        local_sum += arr[i];

    pthread_mutex_lock(&lock);
    global_sum += local_sum;
    pthread_mutex_unlock(&lock);

    pthread_exit(NULL);
}

int main() {
    printf("Name: Muhammad Shahzeb Alam\nRoll No: SE-23055\n\n");

    for (int i = 0; i < SIZE; i++)
        arr[i] = 1;

    pthread_t threads[THREADS];
    int ids[THREADS];

    pthread_mutex_init(&lock, NULL);

    for (int i = 0; i < THREADS; i++) {
        ids[i] = i;
        pthread_create(&threads[i], NULL, sum_worker, &ids[i]);
    }

    for (int i = 0; i < THREADS; i++)
        pthread_join(threads[i], NULL);

    pthread_mutex_destroy(&lock);

    printf("Final Sum = %lld\n", global_sum);
    return 0;
}


ques4:
#include <stdio.h>
#include <stdlib.h>

typedef struct {
    double *a;
    double *b;
    double sum;
    int veclen;
} DOTDATA;

#define VECLEN 100000
DOTDATA dotstr;

void dotprod() {
    int start, end, i;
    double mysum, *x, *y;

    start = 0;
    end = dotstr.veclen;
    x = dotstr.a;
    y = dotstr.b;

    mysum = 0;
    for (i = start; i < end; i++) {
        mysum += (x[i] * y[i]);
    }
    dotstr.sum = mysum;
}

int main(int argc, char *argv[]) {

    printf("Name: Muhammad Shahzeb Alam\nRoll No: SE-23055\n\n");

    int i, len;
    double *a, *b;

    len = VECLEN;
    a = (double*) malloc(len * sizeof(double));
    b = (double*) malloc(len * sizeof(double));

    for (i = 0; i < len; i++) {
        a[i] = 1.0;
        b[i] = a[i];
    }

    dotstr.veclen = len;
    dotstr.a = a;
    dotstr.b = b;
    dotstr.sum = 0;

    dotprod();

    printf("Sum = %f\n", dotstr.sum);
    
    free(a);
    free(b);

    return 0;
}


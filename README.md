Lab 12 
 
 
# producer_consumer.py 
import threading 
import time 
import random 
from collections import deque 
 
# --- Configuration --- 
BUFFER_SIZE = 5 
PRODUCER_COUNT = 2 
CONSUMER_COUNT = 3 
 
# --- Shared Resources --- 
buffer = deque(maxlen=BUFFER_SIZE) 
 
# --- Synchronization Primitives --- 
empty = threading.Semaphore(BUFFER_SIZE)  # Tracks empty slots 
full = threading.Semaphore(0)              # Tracks filled slots 
mutex = threading.Lock()                   # Ensures mutual exclusion 
 
# --- Producer Thread --- 
def producer(producer_id): 
    global buffer 
    item_counter = 0 
    while True: 
        item = f"Item-{producer_id}-{item_counter}" 
         
        # Wait if buffer is full 
        empty.acquire() 
         
        # Critical section: add to buffer 
        mutex.acquire() 
        buffer.append(item) 
        print(f"Producer {producer_id} produced {item}. Buffer: {list(buffer)}") 
        mutex.release() 
         
        # Signal that buffer has one more item 
        full.release() 
         
        # Simulate producing an item 
        time.sleep(random.uniform(0.5, 2)) 
        item_counter += 1 
 
# --- Consumer Thread --- 
def consumer(consumer_id): 
    global buffer 
    while True: 
        # Wait if buffer is empty 
        full.acquire() 
         
        # Critical section: remove from buffer 
        mutex.acquire() 
        item = buffer.popleft() 
        print(f"Consumer {consumer_id} consumed {item}. Buffer: {list(buffer)}") 
        mutex.release() 
         
        # Signal that buffer has one more empty slot 
        empty.release() 
         
        # Simulate consuming an item 
        time.sleep(random.uniform(1, 3)) 
 
# --- Main Application --- 
if __name__ == "__main__": 
    threads = [] 
     
    print("=" * 70) 
    print("PRODUCER-CONSUMER PROBLEM SIMULATION") 
    print("=" * 70) 
    print(f"Buffer Size: {BUFFER_SIZE}") 
    print(f"Producers: {PRODUCER_COUNT}") 
    print(f"Consumers: {CONSUMER_COUNT}") 
    print("=" * 70) 
    print() 
     
    # Create and start producer threads 
    for i in range(PRODUCER_COUNT): 
        thread = threading.Thread(target=producer, args=(i,), daemon=True) 
        threads.append(thread) 
        thread.start() 
     
    # Create and start consumer threads 
    for i in range(CONSUMER_COUNT): 
        thread = threading.Thread(target=consumer, args=(i,), daemon=True) 
        threads.append(thread) 
        thread.start() 
     
    # Run for 20 seconds then exit 
    try: 
        time.sleep(20) 
        print("\n" + "=" * 70) 
        print("Simulation completed successfully!") 
        print("=" * 70) 
    except KeyboardInterrupt: 
        print("\n" + "=" * 70) 
        print("Simulation interrupted by user") 
        print("=" * 70) 
 
 
 

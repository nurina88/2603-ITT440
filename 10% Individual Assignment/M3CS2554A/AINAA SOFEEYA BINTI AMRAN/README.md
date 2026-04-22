# BANK TRANSACTION PROCESSING SYSTEM
### Student Information: ainaafeeya
### Platform: Ubuntu
### Date: April 21, 2026


# 1. Problem Statement
Banking systems process thousands of financial transactions daily, including deposits, withdrawals, and transfers. Processing these transactions efficiently is crucial for system performance. However, it is unclear whether using concurrent (threading) or parallel (multiprocessing) techniques provides better performance compared to traditional sequential processing. This project aims to compare these three approaches by processing 100,000 random bank transactions and measuring their execution times to determine which method is most efficient.
# 2. Objectives
To implement three transaction processing methods:
* Sequential processing (single-threaded)
* Concurrent processing using ThreadPoolExecutor
* Parallel processing using multiprocessing.Pool<br>


To generate 100,000 random transactions with three types: deposit, withdraw, and transfer

To measure and record the execution time for each processing method

To calculate the speedup factor of concurrent and parallel methods compared to sequential

To analyze which method performs best for this transaction processing workload
# 3. Implementation
## 3.1 Source Code 
```ssh
import random, time
from concurrent.futures import ThreadPoolExecutor
from multiprocessing import Pool
 
def gen(n):
        return [(random.choice(["deposit","withdraw","transfer"]), random.randint(1,1000))>
 
def proc(chunk):
        bal = d = w = t = 0
        for act, amt in chunk:
            if act == "deposit": bal += amt; d += 1
            elif act == "withdraw": bal -= amt; w += 1
            else: bal -= amt; t += 1
        return bal, d, w, t
 
def run(trans, mode="seq"):
        start = time.time()
        if mode == "seq":
            res = [proc(trans)]
        elif mode == "con":
            with ThreadPoolExecutor(4) as ex:
                res = ex.map(proc, [trans[i::4] for i in range(4)])
        else:
            with Pool(4) as p:
                res = p.map(proc, [trans[i::4] for i in range(4)])
        return res, time.time()-start
 
if __name__ == "__main__":
        trans = gen(3000000)
        print(f"Bank Transaction Processing {len(trans):,} transactions\n")
        res1, t1 = run(trans, "seq")
        bal = 10000 + sum(r[0] for r in res1)
        d = sum(r[1] for r in res1)
        w = sum(r[2] for r in res1)
        tr = sum(r[3] for r in res1)
 
        res2, t2 = run(trans, "con")
        res3, t3 = run(trans, "par")
 
        print(f"Deposits: {d}")
        print(f"Withdrawals: {w}")
        print(f"Transfers: {tr}")
        print(f"Final Balance: ${bal}\n")
 
        print(f"Sequential: {t1:.4f}s")
        print(f"Concurrent: {t2:.4f}s ({t1/t2:.2f}x)")
        print(f"Parallel:   {t3:.4f}s ({t1/t3:.2f}x)")
```
    

## 3.2 Implementation Description

  | Function                   | Purpose                                                           |
|--------------------------|-------------------------------------------------------------------|
| generate_transactions(n) | Creates random transactions with random amounts                   |
| process_chunk(chunk)     | Processes a batch and returns balance and counts                  |
| sequential(transactions) | Processes all transactions in one thread                          |
| concurrent(transactions) | Uses 4 threads to process 4 chunks                                |
| parallel(transactions)   | Uses 4 processes to process 4 chunks                               |

# 4. Output Results
<img width="529" height="293" alt="image" src="https://github.com/user-attachments/assets/e959cd75-5763-4865-ac33-a4749df26eb5" />

# 5. Performance Analysis
## 5.1 Results Summary
| Processing Method       | Time (seconds) | Speedup |
|------------------------|----------------|---------|
| Sequential             | 0.5479         | 1.00x   |
| Concurrent (4 threads) | 0.6608         | 0.83x   |
| Parallel (4 processes) | 2.0571         | 0.27x   |

---

## 5.2 Transaction Statistics

| Transaction Type | Count      |
|----------------|------------|
| Deposits        | 1,001,306  |
| Withdrawals     | 998,334    |
| Transfers       | 1,000,360  |
| **Total**       | **3,000,000** |

## 5.3 Analysis
* Sequential (0.5479s): Fastest method. No overhead from thread or process creation. Simple and efficient for this workload.

* Concurrent (0.6608s): 0.83x slower than sequential. Thread management and GIL contention on shared balance reduced performance.

* Parallel (2.0571s): 0.27x slower than sequential. High overhead from process creation and inter-process communication made this the slowest method despite using multiple CPU cores.

# 6. Conclusion
Based on the experimental results processing 3,000,000 bank transactions, sequential processing was the fastest method at 0.5479 seconds, outperforming both concurrent and parallel approaches. Concurrent processing using 4 threads was slower at 0.6608 seconds, achieving only 0.83x the speed of sequential due to thread management overhead and Global Interpreter Lock (GIL) contention on the shared balance. Parallel processing using 4 processes was the slowest at 2.0571 seconds, achieving just 0.27x the speed of sequential because the overhead of creating separate processes and performing inter-process communication (IPC) significantly degraded performance. Therefore, for workloads involving frequent updates to a shared state such as a bank balance, sequential processing is the most efficient choice in Python, while concurrency and parallelism introduce unnecessary overhead that reduces overall performance.

# BANK TRANSACTION PROCESSING SYSTEM
### Student Information: Ainaa Sofeeya Amran
### Date: April 24, 2026

## SYSTEM ENVIRONMENT
* OS: Ubuntu
* Python Script: bank.py
* Metrics Recorded: Execution time, transaction counts, final balance, and speedup relative to sequential execution.

# 1. PROBLEM STATEMENT
Banking systems process thousands of financial transactions daily, including deposits, withdrawals, and transfers. Processing these transactions efficiently is crucial for system performance. However, it is unclear whether using concurrent (threading) or parallel (multiprocessing) techniques provides better performance compared to traditional sequential processing. This project aims to compare these three approaches by processing 30,500,000 random bank transactions and measuring their execution times to determine which method is most efficient.

# 2. OBJECTIVES
To implement three transaction processing methods:
* Sequential processing (single-threaded)
* Concurrent processing using ThreadPoolExecutor
* Parallel processing using multiprocessing.Pool<br>


To generate 30,500,000 random transactions with three types: deposit, withdraw, and transfer.

To measure and record the execution time for each processing method.

To calculate the speedup factor of concurrent and parallel methods compared to sequential.

To analyze which method performs best for this transaction processing workload.

# 3. IMPLEMENTATION
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
        trans = gen(30500000)
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

| Function | Purpose |
|---|---|
| `gen(n)` | Generates `n` random transactions, each with a random type ("deposit", "withdraw", "transfer") and a random amount between 1 and 1000 |
| `proc(chunk)` | Processes a batch of transactions and returns the net balance change, deposit count, withdrawal count, and transfer count for that chunk |
| `run(trans, mode)` | Executes transactions using sequential, concurrent (4 threads), or parallel (4 processes) mode and measures execution time |
| Sequential Mode | Processes all transactions in one thread using a single call to `proc()` |
| Concurrent Mode | Uses `ThreadPoolExecutor` with 4 threads to split transactions into 4 chunks using list slicing `[i::4]` |
| Parallel Mode | Uses `multiprocessing.Pool` with 4 processes to split transactions into 4 chunks using the same slicing method |

# 4. OUTPUT RESULT
<img width="689" height="720" alt="image" src="https://github.com/user-attachments/assets/8cc8fef6-17bf-4e31-95b4-3d910f2e5352" />

# 5. PERFORMANCE ANALYSIS
## 5.1 Results Summary
| Transaction Volume | Sequential (s) | Concurrent (s) | Parallel (s) | Concurrent Speedup | Parallel Speedup | Status |
|---|---|---|---|---|---|---|
| 30,000,000 | 9.4899 | 12.8302 | 29.1679 | 0.74x | 0.33x | Completed |
| 30,500,000 | 9.4198 | 10.6026 | 22.7562 | 0.89x | 0.41x | Completed |
| 35,000,000 | — | — | — | — | — | Killed |
| 40,000,000 | — | — | — | — | — | Killed |
| 50,000,000 | — | — | — | — | — | Killed |

---

## 5.2 Transaction Statistics

| Transaction Type | Count (30M) | Count (30.5M) |
|---|---|---|
| Deposits | 9,999,441 | 10,166,026 |
| Withdrawals | 10,017,731 | 10,168,362 |
| Transfers | 9,998,828 | 10,165,612 |
| **Total** | **30,016,000** | **30,500,000** |

## 5.3 Analysis
* Sequential (9.42–9.49s): Fastest method. No overhead from thread or process creation. Simple and efficient for this workload.

* Concurrent (10.60–12.83s): 0.74x to 0.89x slower than sequential. Thread management and GIL contention on shared balance reduced performance. Performed better at 30.5M (0.89x) than at 30M (0.74x).

* Parallel (22.76–29.17s): 0.33x to 0.41x slower than sequential. High overhead from process creation and inter-process communication made this the slowest method despite using multiple CPU cores.

* System Limit: Maximum feasible transaction volume was approximately 30.5 million transactions. Volumes of 35 million, 40 million, and 50 million caused the process to be killed due to system resource exhaustion (likely memory-related).

# 6. CONCLUSION
| Rank | Method | Best Time | Speedup | Reliability |
|---|---|---|---|---|
| 1 | Sequential | 9.4198s | 1.00x | ✅ Up to 30.5M |
| 2 | Concurrent | 10.6026s | 0.89x | ✅ Up to 30.5M |
| 3 | Parallel | 22.7562s | 0.41x | ✅ Up to 30.5M |

Based on the experimental results processing up to 30.5 million bank transactions, sequential processing proved to be the fastest and most reliable method, outperforming both concurrent and parallel approaches. Concurrent and parallel methods introduced significant overhead that outweighed any potential benefits from multithreading or multiprocessing. The maximum feasible transaction volume on this system was approximately 30.5 million transactions, as volumes of 35 million, 40 million, and 50 million transactions caused the process to be killed due to system resource exhaustion, likely memory-related. Therefore, for this specific bank transaction processing workload under the given system constraints, sequential execution is the recommended approach.

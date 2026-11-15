# HW2: Faster Matrix Multiplication

Due 24.11.26 at 23:59, by email to: eran.b@campus.technion.ac.il

## Overview

In HW2 you improve your HW1 matrix multiplication by steering the agent: research, plan, review the plan, code, then review the code.

Two rules to remember:

- **One chat for the whole HW.**
- **Gemini 3.8 Flash only, on the free plan.**

Inside that chat, use everything L3 taught, sub-agents included.

Speed counts now: your rank in the [contest](#contest) sets your starting grade. Only correct code enters it. From there, your report decides the grade (see [Grading](#grading)).

Write your report in a Google Doc as you work, so we can see the version history (see [Report](#report)).

If the cache, SIMD or compiler flags are new to you, read the [Background](#background) first.

## Rules

- **One chat for the whole HW.** Don't start a second one.
- **Gemini 3.8 Flash only, on the free plan.** No other model.
- Use only Antigravity. Inside the chat, anything it offers is allowed, for example sub-agents, `/btw`, `/grill-me` and compaction. No other AI tools, except for background reading (see [Background](#background)).
- Work alone.
- The code is in C++, in `matmul.cpp`, and must match `matmul.h`. Single-threaded.
- The result is correct if C equals A·B of the values before the call: for 3 random vectors x, C·x matches A·(B·x) within relative error 1e-4, as in HW1. matmul must not change A or B, except by writing C.
- It must build and run on **our machine**: Intel Core Ultra 5 125H, AVX2 and FMA, no AVX-512, Ubuntu 24.04 (WSL), g++ 13.3. We build your `matmul.cpp` with your `flags.txt` and run it ourselves.
- If your code uses file paths, make them relative, using `std::filesystem`, so it runs on any computer and OS.
- Don't change `matmul.h` or `timing.cpp`.

**Forbidden:**

- **Threads or extra processes**
- **External libraries, such as BLAS**
- **Changing or tricking the timing or test program**
- **Keeping results between calls**
- **Compiling at run time**

**Allowed:** everything else, for example any flags, pragmas, intrinsics, inline `asm`, and your own buffers.

## What you get

All five are in the HW2 repo, with this assignment as `README.md`.

- `matmul.h`: the function you write. The same as in HW1.
- `matmul.cpp`: an empty function, as in HW1. Start from your HW1 code.
- `flags.txt`: your compiler flags, on one line. It starts as `-O0`, as in HW1.
- `timing.cpp`: our timing program, a new version. It runs the public tests (see [Edge cases](#edge-cases)), then checks and times every size as in HW1, and writes `results.csv`, with a preview of your contest score.

## Get started

1. Open two terminals, as in HW1: one for the Antigravity CLI, and one for building and running your C++.
- In the Antigravity IDE' start a **PowerShell** terminal to launch the Antigravity CLI.
- click the **arrow next to the + button** and pick **WSL** for the C++.
- Mac: two tabs of the Mac terminal (see [Mac students](#mac-students)).
2. Put your HW1 `matmul.cpp` in place of the empty one.
3. Before your first prompt, take a screenshot of `/usage`.
4. In the WSL terminal, build and run the timing program yourself, not through the agent, so its output doesn't fill the chat. Run the first line before every build:

   ```bash
   LC_ALL=C tr -cd '[:print:]\t\n' < flags.txt > flags.tmp && mv flags.tmp flags.txt
   g++ -std=c++17 $(cat flags.txt) timing.cpp matmul.cpp -o timing
   ./timing <your ID>
   ```

   It takes a few minutes and writes `results.csv`. Every run overwrites it, and also saves a numbered copy: `attempts/001.csv`, `attempts/002.csv`, and so on. These copies are for you, to track what each attempt gave. You can add a name after the number, for example `003_tiling.csv`. You don't submit them.
5. When you're done, take the end screenshots: `/usage`, `/context` and `/effort`.

## Context window and tokens

Note your usage of the context window. Since everything is in one chat, we highly suggest you review your system prompt and anything else that may waste precious context window. Bad practices will clutter the context window.

Use what L3 taught, inside the one chat.

We ran this HW ourselves on the free plan, in one chat with Gemini 3.8 Flash on medium effort, using what L3 taught. It gave a fast, correct result and used less than half of the weekly quota, so the free plan is enough when you use it with care.

In `agy`, take screenshots of:

- `/usage`, before you start and after you finish
- `/context`, at the end of the chat
- `/effort`, for your effort level

Put them in the report, with a short explanation of what the numbers say about your usage. This is for awareness: you aren't graded on how many tokens you use, but on how you managed the context window (see [Report](#report), item 2).

## Tips

- **A longer chat uses up your quota faster.** On every turn, the agent reads the whole chat again, so each turn costs more as the context window grows.
- **Check `/usage` after your first sub-agent turn.** Sub-agents use more of the free plan's quota than a normal turn.
- **Pick your effort level once.** Switching between the Gemini 3.8 Flash models (low, medium, high) in the middle of the chat makes the next turn read the whole chat again at full cost.
- **Start early.** The free quota refreshes once a week. If you use it up, you wait for the reset.
- **`./timing <your ID> --tests-only`** runs only the public tests, in a few seconds. Handy while you debug.

## Edge cases

HW2 tests edge cases that HW1 didn't. All of them are in the public tests, so make sure those pass.

`matmul.h` is the same as in HW1, and it is the whole contract.

## Sizes

The timing program runs the same sizes as HW1, so you can compare:

- N = 16, 32, 64, 128, 256, 512, 1000, 1023, 1024, 1025, 2048: (N×N) times (N×N)
- (2048×64) times (64×2048)
- (64×2048) times (2048×64)

The contest and the hidden tests use sizes similar to these, but we won't say exactly which :)

## results.csv

The timing program writes this file. Don't edit it. Every run overwrites it, and saves a numbered copy in `attempts/`.

At the top: your CPU, OS, compiler, your flags from `flags.txt`, any warnings, and any `optimize`, `target` or `asm` it finds in `matmul.cpp` (they're allowed, it only notes them).

Then the public tests, one line each: the test's name and `pass`, or why it failed.

Then one row per size, with HW1's columns, plus one at the end:

- `m`, `n`, `p`: the size. A is m×n, B is n×p, C is m×p.
- `time_s`: seconds per matmul call. The first call is a warm-up. If it took 2 s or more, that call is the time. Otherwise the program calls matmul again, with new matrices, for about 1 s and at least 3 times, and takes the median. A size timed this way runs in 3 rounds, each in a new process, and keeps its median round. It passes only if every round passes.
- `runs`: how many calls `time_s` comes from, in that round.
- `peak_memory_mb`: the most memory used while running this size, A, B and C included. Each size runs in its own process, so other sizes don't count.
- `extra_memory_mb`: memory your matmul used beyond A, B and C.
- `relative_error`: how far C is from the right answer, on the first call. Smaller is better.
- `result`: `pass`, or why it failed.
- `ns_per_multiply_add`: `time_s` divided by m·n·p, in nanoseconds: the average time of one multiply-add (see [Multiply-add](#multiply-add)). It should be about the same at every size. Where it jumps, ask why.

At the end: a preview of your contest score: the cost of 5 of the sizes above, chosen to be like the contest's, and their geometric mean. It's only a preview, on your machine and on these sizes. The contest numbers are what count.

## Contest

Your rank in the contest sets your starting grade (see [Grading](#grading)).

- **Who enters:** every submission that passes the hidden tests and every check in every contest run: the right answer, within relative error 1e-4 as in HW1, A and B unchanged, one thread, and no crash or timeout.
- **Cost of one size:**

  `cost = time × (1 + max(0, extra_memory − 2 MB) / (A + B + C))^0.25`

  `time` is seconds per matmul call. `extra_memory` is the memory your matmul uses beyond A, B and C (`extra_memory_mb` in `results.csv`), and A + B + C is the memory of the three matrices. The first 2 MB of extra memory are free, so small buffers cost nothing. At the large sizes, extra memory equal to A + B + C costs about ×1.19. There is no memory cap: more memory only costs more.
- **Score:** the geometric mean of cost over the contest sizes. Lower wins. The geometric mean makes every size count, not only the biggest one.
- **Timing:** on our machine, on one core, built with your `flags.txt`.

## Grading

Your grade starts from your rank in the contest, among the 20 students in the course:

| Contest rank | Starting grade |
| --- | --- |
| 1–2 | 102 |
| 3–7 | 100 |
| 8–13 | 98 |
| 14–20 | 96 |

Code that fails the hidden tests, or fails a check in a contest run, doesn't enter the contest, so it ranks after every entry that did.

From there, the grade depends on the quality of your report (see [Report](#report)):

- **Your tricks:** how well you explain each one, why you chose it, and how it works with your other tricks and your flags.
- **Context window:** what you did to manage the agent's context window throughout the chat.
- **Depth:** everything you tried, every kind of matrix, every column, and the comparison with HW1.

As in HW1, points also come off for missing deliverables, failed tests or sizes, results we can't reproduce...
## Deliverables

A zip that includes:

- `matmul.cpp`: your final code
- `flags.txt`: your flags, on one line
- `results.csv`: from the timing program, built with your `flags.txt`. Only the one from your final code, not the `attempts/` copies.
- `prompts.md`: every prompt you typed in the chat, word for word, from the first one, `/btw` questions included. The prompts the agent writes for its sub-agents don't go in. If you used a system prompt or rules file (for example `GEMINI.md` or custom instructions), paste it in as the first prompt.
- `notes.md`: your research notes
- `plan.md`: the plan
- `plan_review.md`: the findings of the plan review
- `code_review.md`: the findings of the code review

Refer to the last four in your report.

## Report

Write the report in a Google Doc, and type it there, not elsewhere and pasted in: we check the version history. Add your prompts and token screenshots as you go.

It has five parts:

1. **What the experiment is.**
2. **Resources used.** Your token screenshots with a short explanation, and memory and time from `results.csv`.
   - **Managing the context window:** what did you do, throughout the chat, to keep the agent's context window useful, and why? For example, what you kept out of the chat or in files, and where you used the things we disscussed in classs.
   Did it work? Use your `/context` screenshot.
3. **The results, and why.** Explain in your own words *why* the numbers in `results.csv` come out as they do. We want an in-depth analysis, not a summary: "it got faster" or a graph alone is not enough, and don't describe the columns. Cover:
   - **Debugging** including how you debugged the failing public tests, if there were any.
   - **HW1 against HW2:** size by size, on the same machine.
   - **Your tricks:** for each trick in your final code, what does it do, why is it faster, and why did you choose it? How do your tricks and flags work together?
   - **Everything you tried,** also what didn't work: what does it do, why did you try it, and what gain did you expect?
   - **Every kind of matrix:** where do the square sizes and the two rectangular shapes behave differently, and why?
   - **Every column:** how do they change with size and shape scaling and changing?
4. **Special problems you ran into** mainly technical issues (antigravity\timing program issues\usage limits...), or special circumestances.
5. **General comments on the exercise.**
We really value honest feedback!

End with this line, signed: "I used only Antigravity with Gemini 3.8 Flash, on one main chat only, for this HW, apart from background reading."

## Submit

By email to eran.b@campus.technion.ac.il, by 24.11.26, 23:59: the zip from [Deliverables](#deliverables).

In the email itself, write your full name, ID, and a link to your report's Google Doc, so we can see the version history.
Share it with eran.ben.elihao@gmail.com **as editor**.

## Background

Four ideas help you read your results: multiply-adds, the cache, SIMD and flags. The first paragraph of each is enough to start, and the **In depth** parts are there if you want more. You aren't expected to know how to make the code faster: that's what the research is for.

Each part ends with a prompt you can paste into your favorite browser LLM (Gemini, ChatGPT, ...) to learn more. Use a separate chat for it, with no code and no HW work in it. It doesn't go in `prompts.md`.

### Multiply-add

Think of how you compute one entry of C by hand. You go along a row of A and down a column of B, multiply each pair of numbers, and add the products up. Each "multiply one pair, and add it to the sum" is one multiply-add.

**In depth:**

- **m, n, p:** as in `matmul.h`, A is m×n and B is n×p, so C is m×p. A row of A and a column of B both have n numbers.
- **In code,** one multiply-add is one `c += a * b`.
- **One entry of C** takes n multiply-adds, one for each pair. C has m·p entries, so matmul does m·n·p multiply-adds in total. For 2048×2048 times 2048×2048, that's about 8.6 billion.
- **`ns_per_multiply_add`** in `results.csv` is the time of one matmul call (`time_s`) divided by m·n·p: the average time of one multiply-add, not of one entry of C. Example: 1000×1000 times 1000×1000 is 1 billion multiply-adds. If one call takes 1 second, that's 1 ns per multiply-add, and 1000 ns per entry of C. A bigger size alone doesn't change it, so you can compare it between sizes and shapes.

**Ask an LLM:**

```text
Explain in simple words what a multiply-add is in matrix multiplication,
with a small example. Why does an m×n matrix times an n×p matrix
take m·n·p of them? No code, please.
```

### Cache

Think of a desk in a library. Getting a book from the shelves takes a long walk, so you keep the books you used lately on your desk. For the CPU, main memory is the shelves and the cache is the desk. When the next number your code needs is already in the cache, the code is fast. When it isn't, the CPU waits, and one wait can take as long as hundreds of multiply-adds. So the order in which your code visits the numbers matters, not only how many it computes.

**In depth:**

- **Cache lines:** the CPU never fetches just one number. It fetches 64 bytes at a time, called a cache line: 8 doubles that sit next to each other in memory. So after one wait, the 7 neighbors are already in the cache.
- **Three levels:** from small and fast to big and slower. On our machine, each core has its own L1 of 48 KB and L2 of 2 MB. L3 is 18 MB, shared by all cores. Main memory is much bigger than all three, and much slower.
- **Sizes:** one double is 8 bytes, so a 2048×2048 matrix is 32 MB, more than the largest cache.

**Ask an LLM:**

```text
Explain in simple words what a CPU cache is, with an everyday analogy.
Then explain cache lines, the L1, L2 and L3 levels, and what a cache miss costs.
No code, please.
```

### SIMD

Think of a cashier who scans one item per beep, and one who scans 4 items in one beep. SIMD (single instruction, multiple data) is the second cashier: one CPU instruction that does the same thing to several numbers at once. So code that uses it can do several times more work per instruction.

**In depth:**

- **AVX2:** the SIMD instructions of our machine. They work on 4 doubles at once.
- **FMA** (fused multiply-add): one instruction that does a multiply-add, `c += a * b`, on 4 doubles at once. Fused means the multiply and the add happen in one step, with one rounding instead of two.
- **No AVX-512:** our machine doesn't have the newer instructions that work on 8 doubles at once. Code that uses them won't build or run there.
- **Two ways to get SIMD:** the compiler can turn your loops into SIMD by itself, which is called auto-vectorization (see [Flags](#flags)). Or you can ask for each instruction by name, with intrinsics: C++ functions that each become one SIMD instruction.

**Ask an LLM:**

```text
Explain in simple words what SIMD is, with an everyday analogy.
Then explain AVX2 and FMA on an Intel x86-64 CPU, and what
auto-vectorization and intrinsics are. No code, please.
```

### Flags

Think of two translators. One goes word by word. The other rewrites each sentence so it reads well, with the same meaning. The compiler is the translator of your C++ into CPU instructions, and flags tell it how hard to rewrite. At `-O0`, where `flags.txt` starts, it goes almost line by line: easy to debug, and slow.

Put your flags on one line in `flags.txt`. Any flags are fine. We build with them for everything, the public tests, the hidden tests and the contest:

`g++ -std=c++17 $(cat flags.txt) timing.cpp matmul.cpp -o timing`

**Don't spend much time on flags.** 
The better your code is, the less the flags matter.
More flags isn't always faster either: in one test, the code got slower when we added more.
Your code decides the contest, not your flags.

**In depth:** every flag is a trade-off.

| Flag | Why it can help | Why it can hurt |
| --- | --- | --- |
| `-O2` | It optimizes, but keeps your loops closer to how you wrote them. Code you tuned by hand stays as you planned it | It turns fewer loops into SIMD by itself, so plain loops can stay slow |
| `-O3` | It rewrites loops harder: more of them become SIMD (auto-vectorization) and get unrolled. Plain loops can get much faster | On code you tuned by hand, there is little left for it to do, and its rewriting can even make it slower. The compiled program also gets longer, and the CPU has to fetch those instructions too |
| `-march=native` | The compiler may use every instruction of the CPU it compiles on. On our machine that adds AVX2 and FMA: SIMD on 4 doubles at once, where the default is 2 | It changes little in code that doesn't become SIMD. On your own computer, "native" is your CPU, so your code and times can differ from ours. With FMA, the last digits of the result can change |
| `-ffast-math` | The compiler may change the order of float math, for example add up a sum in another order. That lets it turn sums into SIMD | It changes nothing in loops with no such sum. The results change slightly, and it assumes there is no NaN or inf |
| `-funroll-loops` | The loop does several steps per round, so it checks the loop condition less often. That helps short, simple loops | The compiled program gets longer. It adds nothing to loops that you or `-O3` already unrolled, and can make them slower |

- `-O2` and `-O3` are levels: pick one. The other three are extras, added next to the level.
- `-Ofast` is only a short name for `-O3 -ffast-math`. It is not a higher level.
- On our machine, `-march=native` means AVX2 and FMA, no AVX-512. Code that needs other instructions won't build or run there.
- We take only `matmul.cpp` and `flags.txt`, so a flag that needs another file or a library won't work on our machine.

**Ask an LLM:**

```text
Explain in simple words the trade-off of each of these g++ flags: when can it
make code faster, and when can it change nothing or make code slower?
-O2, -O3, -march=native, -ffast-math, -funroll-loops, -Ofast.
No code, please.
```

## Mac students

The steps are the same, in two tabs of the Mac terminal: one for `agy`, one for the C++.
Your code must still build on our machine (x86-64).

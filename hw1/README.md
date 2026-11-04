# HW1: Matrix Multiplication Baseline

Due 15.11.26 at 23:59, by email to: eran.b@campus.technion.ac.il

## Overview

In HW1 you ask the Antigravity agent to write matrix multiplication in C++. Ask the way you naturally would. Then you check and measure what you got.

HW1 is graded on correctness and your explanation ONLY, not on speed.

Throughout this entire work, write your report in a Google Doc, so we can see the version history (see [Submit](#submit)).

## Rules

- Work alone.
- Use only Antigravity. No other AI tools.
- The code is in C++, in `matmul.cpp`, and must match `matmul.h`. Single-threaded. No BLAS or other libraries.
- It must build and run on our grading machine.
- If your code uses file paths, make them relative, using `std::filesystem`, so it runs on any computer and OS.
- Don't change `matmul.h` or `timing.cpp`.

## What you get

All three are in the HW1 repo, with this assignment as `README.md`.

- `matmul.h`: the function you write.
- `matmul.cpp`: an empty function. The agent fills it in.
- `timing.cpp`: our timing program. It makes matrices from your student ID, calls your matmul several times with new matrices each time, checks every call, measures time and memory, and writes `results.csv`. A size passes if, for 3 random vectors x, C·x matches A·(B·x) within relative error 1e-4, matmul doesn't change A or B, and it uses one thread. It warns if `matmul.cpp` uses threads. We run it again when grading.

## Steps

1. Check that `g++ --version` works. Windows: build and run everything in a WSL terminal.
2. Open Antigravity CLI.
3. Ask the agent to fill `matmul.cpp` according to `matmul.h`.
4. Copy every prompt you used into `prompts.md`.
5. Build and run the timing program yourself: `g++ -std=c++17 -O0 timing.cpp matmul.cpp -o timing`, then `./timing <your ID>`. It takes a few minutes, checks every size and writes `results.csv`.
6. If a size fails, tell the agent. Paste only the failing line, not all the output.
7. Repeat until every size passes.

## Sizes

The timing program runs every size below.

- N = 16, 32, 64, 128, 256, 512, 1000, 1023, 1024, 1025, 2048: (N×N) times (N×N)
- (2048×64) times (64×2048)
- (64×2048) times (2048×64)

We also have hidden tests.

## results.csv

The timing program writes this file. Don't edit it.

At the top: your CPU, OS, compiler, and any warnings. Then one row per size:

- `m`, `n`, `p`: the size. A is m×n, B is n×p, C is m×p.
- `time_s`: seconds per matmul call. The program calls matmul several times and takes the median time.
- `runs`: how many times matmul was called for this size.
- `peak_memory_mb`: the most memory the whole program used, A, B and C included.
- `extra_memory_mb`: memory your matmul used beyond A, B and C.
- `relative_error`: how far C is from the right answer, on the first call. Smaller is better.
- `result`: `pass`, or why it failed.

## Tokens

In `agy`, take screenshots of:

- `/usage`, before you start and after you finish
- `/context`, at the end of each chat
- `/effort`, for your effort level. Also say which model you used.

Put them in the report, with a short explanation of what the numbers say about your usage. This is for awareness. You aren't graded on how many tokens you use.

## Report

Write the report in a Google Doc, with your token screenshots in it. Type it in the doc itself, not elsewhere and pasted in. We check the doc's version history.

1. What the experiment is
2. Resources used: your token screenshots with a short explanation, and memory and time from `results.csv`
3. Short description of the experiment and explanation of the results. Explain what you see in `results.csv`, in your own words, not generated text. A graph alone is not enough.
4. Special problems you ran into
5. General comments on the exercise

End with this line, signed: "I used only Antigravity for this HW."

## Submit

By email to eran.b@campus.technion.ac.il, by 15.11.26, 23:59:
A zip that includes:
- `matmul.cpp`
- `results.csv`
- `prompts.md`

In the email itself, write your full name, ID, and a link to your report's Google Doc, so we can see the version history.
Share it with eran.ben.elihao@gmail.com **as editor**. 

## Tip

- Run your program in the terminal, not through the agent. Then its output doesn't fill the agent's chat.

## Mac students

The steps are the same, in the Mac terminal. `g++` on Mac runs Apple's clang++, which is fine for HW1. Mac support is best effort.

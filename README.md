# CSGames AI 2016
This is the AI that made us win the 1st place in the 2016
[CSGames](http://csgames.org/) AI competition.

## Description
This year's AI challenge was quite different from the other years -- a mix of
OS and security, really. We had 2 days to work on our AI and could take our
code home to work on it (which we did -- an all-nighter).

We were provided a assembly manual for a custom assembly on a custom "OS"
(quoted because it was quite rudimentary on purpose).

The goal is simple: survive. Every team writes an assembly program, tournament
brackets are created, and 1v1 matches occur : both programs are put in a shared
`1Mb` memory space, taking turns at having execution time and the last one left
running wins the match.

We are given the following tools:
- `MMU` - We can set the permissions of memory pages, `256 bytes` at a time.
          This is the most efficient way to attack.
- `Threads` - We can launch threads. A program lives as long as it has a thread
              alive. The amount of instructions ran per "turn" is distributed
              among the threads of a program (i.e. you get additional 'lives',
              but it becomes much harder to predict when you'll lose control of
              the CPU). We could have a maximum of 3 threads.
- `Clock` - We can get the current seconds counter. This gives us the ability
            to generate pseudorandom numbers (although *seconds* were a bit too
            slow and we had to modify the seed to avoid collisions)

## Strategy
Our winning strategy was to *spread and conquer*:
- Replicate somewhere else in memory
  - Generate a random address
  - Copy our program over to the target address
  - Start a thread there
- Attack
  - Generate a random target address
  - Remove `R/W/X` rights at the target address through the MMU
- Repeat!


The hardest part was to replicate. To know where to start copying, we need a
start address. We can't use static assembly labels -- the emplacement of your
code is dynamic (that's the whole *purpose* of replicating). Thus, we want to
have a `MOV myaddress $C` instruction at the beginning. We needed to *craft*
this multi-bytes instruction at the target address. No magic there, just tricky.


## Results
Every fight that we didn't win ended up in a draw (no winner for too long).

We think this is thanks to our replication strategy -- it was very unlikely for
someone to kill all of our 3 threads *before we could replicate once more*.

The draws were caused by how poor our RNG was. We could clearly see the pattern
of our attacks on the memory.

Anyhow, we still managed to win 1st place and it was an amazing experience. :)

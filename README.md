<div align="center">

# 🐧 Sistemas Operativos · UAH

**Operating Systems (Sistemas Operativos, 780007) · B.S. Computer Engineering · Universidad de Alcalá · 2025–2026**

[![build](https://github.com/MiguelHernandezz/Sistemas-Operativos/actions/workflows/build.yml/badge.svg)](https://github.com/MiguelHernandezz/Sistemas-Operativos/actions/workflows/build.yml)
![C](https://img.shields.io/badge/C-00599C?style=flat-square&logo=c&logoColor=white)
![Linux](https://img.shields.io/badge/Linux-FCC624?style=flat-square&logo=linux&logoColor=black)
![POSIX](https://img.shields.io/badge/POSIX-161B22?style=flat-square)
![Team](https://img.shields.io/badge/team-2_people-00C853?style=flat-square)

</div>

Lab work on **processes, signals, pipes and threads** under Linux, using POSIX system calls in C.

> Done in a **team of 2**; my teammate's name is omitted for privacy. The minishell skeleton (parser, internal commands, prompt, memory helpers) was provided by the course; those files keep the lecturer's author header.

Every push is compiled and smoke-tested on Ubuntu by [GitHub Actions](.github/workflows/build.yml).

---

## 📁 Contents

| Folder | Practice | Key POSIX services |
|---|---|---|
| [`PL2-minishell`](PL2-minishell) | A small Unix shell: foreground/background jobs, `;` sequences, `\|` pipelines, `<` `>` redirection, built-ins | `fork` `execvp` `waitpid` `pipe` `dup2` `sigaction` |
| [`PL3-carrera-hilos`](PL3-carrera-hilos) | Car race with one POSIX thread per car and a mutex-protected classification | `pthread_create` `pthread_join` `pthread_mutex_*` `rand_r` |

---

## PL2 · Minishell

What we implemented on top of the course skeleton:

- **Command execution:** each command runs in a child created with `fork()` + `execvp()`. The shell waits with `waitpid()` unless the line ends in `&`.
- **No zombie processes:** a `SIGCHLD` handler installed with `sigaction()` (`SA_RESTART | SA_NOCLDSTOP`) reaps finished background children with `waitpid(-1, …, WNOHANG)`.
- **Pipelines:** for *N* commands the shell creates *N-1* pipes, wires each child's stdin/stdout with `dup2()`, and the parent closes every end it no longer needs so readers get EOF.
- **Sequences:** `cmd1 ; cmd2` split with the reentrant `strtok_r()`.
- **Redirection:** `<` and `>` open the file and swap it in for stdin/stdout before `exec`.
- **Build:** multi-module Makefile; `parser.o` and `internas.o` are packaged into a static library `libshell.a` with `ar`.

```bash
cd PL2-minishell
make
./minishell
minishell> ls -l | grep .c | wc -l
minishell> echo hola > salida.txt ; cat < salida.txt
minishell> sleep 5 &
minishell> cd /tmp
minishell> exit
```

## PL3 · Race simulation with threads

Eight threads (cars) start together, each sleeps a random time and then writes its position into a shared classification array.

- Two mutexes: one for the shared array and arrival counter, one so lines printed by different threads don't interleave.
- `rand_r()` with a per-thread seed instead of `rand()`, whose hidden global state is not thread-safe.
- The main thread waits for every car with `pthread_join()` and prints the final classification. The order changes on each run.

```bash
cd PL3-carrera-hilos
make
./simula_car
```

---

## ▶️ Requirements

Linux (or WSL on Windows) with `gcc` and `make`. On Debian/Ubuntu: `sudo apt install build-essential`.

---

<!-- syllabus:start -->
## 📘 Syllabus · official UAH teaching guide

> **Sistemas Operativos (780007)** · 6 ECTS · 2nd year · 1st semester · contents from the 2025-26 teaching guide · [official subject page](https://www.uah.es/es/estudios/estudios-oficiales/grados/asignatura/Sistemas-Operativos-780007/)

| Unit | Hours | Topics |
|---|---|---|
| Introduction and computer architecture background | 8 h | Batch, multiprogrammed and time-sharing systems · Interrupts, traps and dual-mode execution · Memory protection and the MMU · Mechanisms vs policies |
| Operating system structure | 12 h | Shell and system-call interfaces · Layered design and the kernel · Case studies: Linux, Windows, Mach |
| Processes and threads | 14 h | Program vs process, memory image, life cycle · Process system calls and kernel data structures · POSIX threads |
| Process synchronisation | 12 h | Race conditions, atomicity, the critical-section problem · Pipes, semaphores, mutexes and condition variables · Classic synchronisation problems |
| CPU scheduling | 10 h | Scheduling criteria · FCFS, SJF, priorities and pre-emption · Round robin, multilevel feedback queues · Linux and Windows schedulers |
<!-- syllabus:end -->

---

<div align="center">

Miguel Hernández López · [GitHub](https://github.com/MiguelHernandezz) · [LinkedIn](https://www.linkedin.com/in/miguel-hernandez-lopez/)

</div>

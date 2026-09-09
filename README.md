## Run the shell

1. Clone this directory and `cd` into it.
2. Run the command `make`.
3. Run `./Shell` to get a prompt of the form `Shell:path$ `.
4. Run any command in the shell. It can entail as many spaces, and the shell accounts for those.
5. In order to exit, run `exit` or press `CTRL+D` on empty prompt.
6. Use `make clean` to remove object files and executable file.

## Memory leaks
- Install **valgrind** using `apt install valgrind`.
- Run `make valgrind` to check for memory leaks. It runs the *./shell*  executable file in the valgrind environment.

```
valgrind --leak-check=full --track-origins=yes --show-leak-kinds=all -s ./shell
==12214== Memcheck, a memory error detector
==12214== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.
==12214== Using Valgrind-3.18.1 and LibVEX; rerun with -h for copyright info
==12214== Command: ./shell
==12214== 
Shell:~/Desktop/repos/Shell$ ls | head -n 4
bgJobs.c
bgJobs.h
bgJobs.o
builtinCommand.c
Shell:~/Desktop/repos/Shell$ g++ temp.cpp -o temp
Shell:~/Desktop/repos/Shell$ ls | grep temp
temp
temp.cpp
Shell:~/Desktop/repos/Shell$ ./temp &
[1] 12228
Shell:~/Desktop/repos/Shell$ jobs
Background job [1] 	 Running 	 Command: ./temp
Shell:~/Desktop/repos/Shell$ history
1 ls | head -n 4
2 g++ temp.cpp -o temp
3 ls | grep temp
4 ./temp
5 jobs
6 history
Shell:~/Desktop/repos/Shell$ kill %1
Background job [1] 	 Terminated 	 Command: ./temp
Shell:~/Desktop/repos/Shell$ jobs
Shell:~/Desktop/repos/Shell$ history
1 ls | head -n 4
2 g++ temp.cpp -o temp
3 ls | grep temp
4 ./temp
5 jobs
6 history
7 kill %1
8 jobs
9 history
Shell:~/Desktop/repos/Shell$ !-5
Shell:~/Desktop/repos/Shell$ !3
temp
temp.cpp
Shell:~/Desktop/repos/Shell$ exit
==12214== 
==12214== HEAP SUMMARY:
==12214==     in use at exit: 0 bytes in 0 blocks
==12214==   total heap usage: 68 allocs, 68 frees, 31,043 bytes allocated
==12214== 
==12214== All heap blocks were freed -- no leaks are possible
==12214== 
==12214== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)

```

## Features

### Commands
 
Shell can execute the command of format: `command1 [ < infile] [ | command ]* [ > outfile] [&]`.

#### Builtin Commands

I have defined these commands, which are contained within the shell.

1. `cd [absolute or relative path]`
    -   Changing the directory to the specified directory throws an error if the directory does not exist.
2. `history`
    - Displays last 10 commands.
    - The number of commands to be stored in history is determined by `HISTORY_QUEUE_SIZE` macros in [shell.c](shell.c).
    - Every input will be stored in history, given it is not same as the last input.
3. `!num`
    - It reruns the command numbered *num* in the list of commands returned by history. Otherwise throws an error. 
    - It also works with a negative number, e.g. `!-1` runs the last command.
    - It can be combined with piping, e.g. ` !3 | head -n 1`.
4. `jobs`
    - Displays all the background jobs running, terminated, or completed along with their pid.
5. `kill [ %num | pid ]`
    - Kills the process by sending *SIGKILL* signal.
    - If `%num` is used, it kills the process numbered *num* in the list of background processes returned by jobs. Otherwise throws an error.
    - If `pid` is used, it kills the process which has this *pid*.
6. `help`
    - Displays the syntax of the command accepted by the shell and all the builtin commands.
    
 

### Background Processes

- To run a process in the background, follow the command with a '&' symbol. E.g. `gedit file_name.ext &`.
- Upon termination of a background process, the shell prints its PID and exit status

### Additional Features

1. `CTRL-C`

   - Sends SIGINT signal to the current foreground job of the shell.
   - If there is no foreground job, then the signal does not have any effect.
2. `CTRL-D`

   - Logs you out of the shell without having any impact on the terminal.
   - Works only on empty prompt
3. Input-Output redirection & piping

   - Handles `<`, `>`, and `|` operators appropriately, wherever they are in the command
   - Throws error if syntax is incorrect




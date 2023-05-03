Download Link: https://assignmentchef.com/product/solved-cs3210-lab-1-processes-threads-and-synchronization-basics
<br>



<table width="601">

 <tbody>

  <tr>

   <td colspan="2" width="601"><strong><u>Learning Outcomes</u></strong>

    <ol>

     <li>Understand the differences between processes and threads</li>

     <li>Use pthread library in shared-memory parallel programming</li>

     <li>Implement critical sections in the code</li>

    </ol>Apply basic synchronization constructs in programs</td>

  </tr>

  <tr>

   <td width="70"></td>

   <td width="531"><strong><u>Logging in &amp; Getting Started</u></strong>Use the username &amp; password provided to you using LumiNUS gradebook. You can change your password running passwd in the console.<strong><u>Ways to open the command line terminal (console)</u></strong>•     Keyboard shortcut Ctrl + Alt + T•     Right click on desktop/file browser and clicking “Open in Terminal” OR•     Press “Start” (or Super) key and search for “terminal” in the application dashboard</td>

  </tr>

 </tbody>

</table>

<h1>Part 1: Processes vs. Threads</h1>

<h2>Multi-process programming on Linux with C</h2>

Let us look at a simple code which demonstrates use of processes in Linux. Download and open processes.c file and study the use of fork() system call and its return values.

Note the wait(NULL) call by the parent process. The purpose of this call is to make sure the parent process waits until all its child processes are completed. In a situation where the child continues to run after the parent process is completed (died), the child is called an orphan process.

• Compile the code in a terminal (console):

&gt; gcc -o processes processes.c

<ul>

 <li>Run the program in a terminal (console):</li>

</ul>

&gt; ./processes

<table width="601">

 <tbody>

  <tr>

   <td width="70"></td>

   <td width="531"><strong><u>Exercise 1</u></strong>Compile and run processes.c. Observe the output. Why is the line “We just cloned a process..!” printed twice? Fix the code such that the line only prints once.</td>

  </tr>

 </tbody>

</table>

<h2>Creating and terminating threads</h2>

Listing 1: Snippet of threads.c

<ul>

 <li>for(t=0;t&lt;NUM_THREADS;t++)</li>

 <li>{</li>

 <li>printf(“main thread: creating thread %ld
”, t);</li>

</ul>

31

<ul>

 <li>//pthread_create spawns a new thread and return 0 on success</li>

 <li>rc = pthread_create(&amp;threads[t], NULL, work, (void *)t);</li>

 <li>}</li>

</ul>

threads.c contains a simple example on creating (spawning) threads with pthread library and terminating them. In threads.c, the loop runs NUM_THREADS number of times and calls pthread_create function to create/spawn new threads.

pthread_create takes in four arguments:

<ol>

 <li>thread – Reference to a thread variable of type pthread_t (element in threads array in this example)</li>

 <li>attr – Thread attributes</li>

 <li>start_routine – The function to be executed by the newly spawned thread (function work in this example)</li>

 <li>arg – Arguments to be passed on to the parallel function (t in this example). Please note that instead of passing a single variable, we could pass a structure when multiple arguments are required by the parallel function.</li>

</ol>

• To find out more about different C functions, you can use the manual in command line:

&gt; man 2 pthread_create

<ul>

 <li>Compile the code in a terminal (console):</li>

</ul>

&gt; gcc -pthread -o threads threads.c

<ul>

 <li>Run the program in a terminal (console):</li>

</ul>

&gt; ./threads

<table width="601">

 <tbody>

  <tr>

   <td width="70"></td>

   <td width="531"><strong><u>Exercise 2</u></strong>Compile and run the threads.c program. Observe the output. Modify the NUM_THREADS value and observe the order of thread execution. Do threads execute in the same order they are spawned each time the program runs? Is the final value of the variable counter always the same? Explain.</td>

  </tr>

 </tbody>

</table>

<h1>Part 2: Process and Thread Synchronization</h1>

A critical section is a section of code that uses mutual exclusion to ensure that:

<ul>

 <li>Only one thread at a time can execute in the critical section</li>

 <li>All other threads have to wait on entry</li>

 <li>When a thread leaves a critical section, another can enter</li>

</ul>

A race condition happens when <strong>two concurrent threads (or processes) accessed a shared resource without any synchronization</strong>. Race conditions arise in software when an application depends on the sequence or timing of processes or threads for it to operate properly.

A race condition can also happen when the <strong>result of the program depends on the sequence of which the threads access the critical section</strong>. These inconsistencies of the result occur in critical sections due to sharing of resources by multiple processes/threads. (eg. sharing arrays, variables, files, etc.)

<h2>Process Synchronization with Semaphores</h2>

Download and study semaph.c program which illustrates the usage of semaphores for synchronizing Linux processes. To manage inter process communication, we need to create a shared memory space. This shared memory need to be destroyed at the completion of the program. Observe the use of Semaphore related function calls in the code. You may refer to man pages for each function call to learn more information.

• Compile the code in a terminal (console):

&gt; gcc -pthread -o semaph semaph.c

<ul>

 <li>Run the program in a terminal (console):</li>

</ul>

&gt; ./semaph

<h2>Thread Synchronization with Mutexes and Condition Variables</h2>

Download and study race-condition.c program which illustrates a multithreaded program with a race condition. race-condition.c demonstrates manipulation of a shared global_counter by multiple threads. There are 4 ADD threads that increment the global_counter by 1 each, and 4 SUB threads that decrement the global_counter by 1 each. At the end, the program should print the final value of the global_counter. Please note that sleep(rand() % 2) is called inside add and sub functions to delay the completion for few seconds.

• Compile the code in a terminal (console)

&gt; gcc -pthread -o race race-condition.c

<ul>

 <li>Run the program in a terminal (console)</li>

</ul>

&gt; ./race

<table width="601">

 <tbody>

  <tr>

   <td width="70"></td>

   <td width="531"><strong><u>Exercise 3</u></strong>Compile and run the race-condition.c program. Observe the output.</td>

  </tr>

 </tbody>

</table>

You should notice that the final result of the global counter is printed before completion of all threads. This is due to non-completion of threads before printing the final result.

pthread_join is a pthread library function which guarantees the caller thread that the target thread is terminated. For example, in race-condition.c program, if the main thread calls pthread_join for all the ADD threads and SUB before printing the final result of the global variable, we should see the real final value after all SUB and ADD threads are completed.

int pthread_join(pthread_t thread, void **retval);

// example

pthread_join(thread, NULL);

<table width="601">

 <tbody>

  <tr>

   <td width="61"></td>

   <td width="540"><strong><u>Exercise 4</u></strong>Modify race-condition.c (new name race-condition-ex4.c) to ensure that all ADD threads and SUB threads complete before printing the final result. Compile, run, and observe the output. (run multiple times to see if the output is consistent)</td>

  </tr>

 </tbody>

</table>

Although the threads are synchronized, you may still see a wrong final result. Each ADD thread increments the counter by 1 and each SUB thread decrements the counter by 1. Thus, the final value of the global_counter should remain at its initial value of 10. This behavior is caused due to the existence of a <strong>race condition</strong>.

<h3>Mutexes</h3>

Mutex is a synchronization construct which is used to control access to a critical section in the code. A mutex variable acts like a lock and the thread that acquires the thread gets to access the critical section. Once a thread has acquired a mutex lock to a critical section, no other thread can acquire it until the first thread releases the mutex.

<strong><u>pthread mutex example </u></strong>pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER; pthread_mutex_lock(&amp;lock); // critical section here pthread_mutex_unlock(&amp;lock);

<table width="601">

 <tbody>

  <tr>

   <td width="60"></td>

   <td width="541"><strong><u>Exercise 5</u></strong>Modify race-condition.c (new name: race-condition-ex5.c) by adding a mutex variable to control access to the global_counter. Compile, run, and observe the output. (run multiple times to see if the output is consistent)</td>

  </tr>

 </tbody>

</table>

<h3>Condition variables</h3>

Mutexes provide mechanism for controlling access to a critical section and prevent races. However, they do cannot be used for threads to wait until another thread completes some arbitrary task. Condition variables provide a mechanism for threads to be signaled by other threads rather than continuously polling to check if a certain condition has been met. Condition variables are used in association with mutex variables. Related pthreads functions:

• Create and destroy

pthread_cond_init(condition,attr) pthread_cond_destroy(condition)

<ul>

 <li>Waiting and signaling: pthread_cond_wait(condition,mutex) pthread_cond_signal(condition) pthread_cond_broadcast(condition)</li>

</ul>

Download and study cond.c which demonstrates use of condition variables. The main thread creates three threads. Two of those threads increment a “count” variable, while the third thread watches the value of “count”. When “count” reaches a predefined limit, the waiting thread is signaled by one of the incrementing threads. The waiting thread “awakens” and then modifies count. The program continues until the incrementing threads reach TCOUNT. The main program prints the final value of count.

<table width="601">

 <tbody>

  <tr>

   <td width="60"></td>

   <td width="541"><strong><u>Exercise 6</u></strong>Modify race-condition.c (new name: race-condition-ex6.c) using condition variables to prevent SUB threads from executing until all ADD threads are completed.</td>

  </tr>

 </tbody>

</table>

Further reading and examples: <a href="https://computing.llnl.gov/tutorials/pthreads/">https://computing.llnl.gov/tutorials/pthreads/</a>



<h1>Part 3: Producer-Consumer Problem</h1>

In this part we solve the producer consumer problem using (i) processes and semaphores, and, (ii) threads, mutexes and condition variables.

Recall the producer consumer problem from the lecture. Let us limit the scope of the problem as follows: There are two producers and one consumer. Each producer generates a random number between 1 and 10 (inclusive) and inserts (writes) to producer_buffer. Consumer reads (consumes) these numbers one at a time and updates the sum of all numbers consumed in consumer_sum variable. The producer_buffer can store only 10 numbers and the producers are not allowed to overwrite any unconsumed number.

<table width="601">

 <tbody>

  <tr>

   <td width="70"></td>

   <td width="531"><strong><u>Exercise 7</u></strong>prod-con-threads.c is a basic code skeleton for producer consumer problem without synchronization constructs. Implement the above-mentioned producer-consumer scenario with synchronization using pthreads, mutexes and/or condition variables starting from the code skeleton (prod-con-threads.c). You may add any number of synchronization constructs and other functions as required.</td>

  </tr>

 </tbody>

</table>

Implementing the same producer consumer logic with processes involves allocating memory from the kernel space as a means of maintaining a global variable (For inter-process communication). Refer to the example which uses shared memory with processes in semaph.c

<table width="601">

 <tbody>

  <tr>

   <td width="70"></td>

   <td colspan="2" width="531"><strong><u>Exercise 8</u></strong>Implement the above-mentioned producer-consumer scenario with synchronization using processes and semaphores. The very basic approach should be as follows:</td>

  </tr>

  <tr>

   <td width="70"></td>

   <td width="212">// allocate shared memory // allocate semaphores if (fork()) producer(); //</td>

   <td width="319">producer 1</td>

  </tr>

  <tr>

   <td width="70"></td>

   <td width="212">if (fork()) producer(); // consumer();// cleanup shared memory</td>

   <td width="319">producer 2</td>

  </tr>

  <tr>

   <td width="70"></td>

   <td colspan="2" width="531"><strong><u>Exercise 9</u></strong>Limit the number of total items (numbers) produced by the consumers to 100 and measure the time taken to complete the program for both cases (processes and pthreads). Comment on the observations.</td>

  </tr>

 </tbody>

</table>

<h1>Part 4: Debugging</h1>

<h2>Viewing Processes and Threads</h2>

To view the running processes and threads in a linux console, we can use ps and top commands. These commands should be invoked separately in a different terminal window.

To see a list of processes running on your system details, run any of the following commands in a terminal:

<ul>

 <li>&gt; ps –ef</li>

 <li>&gt; ps -A</li>

 <li>&gt; top</li>

</ul>

If too many information is printed and impossible to read at one time, you can pipe the output through the less command to scroll through them at your own pace:

&gt; ps -A | less

More information on ps: <a href="http://man7.org/linux/man-pages/man1/ps.1.html">http://man7.org/linux/man-pages/man1/ps.1.html</a> or type in man ps in the console.

To list individual threads under each process:

&gt; top -H

More information on top: <a href="http://man7.org/linux/man-pages/man1/top.1.html">http://man7.org/linux/man-pages/man1/top.1.html</a> or type in

man top in the console.

You may also try htop, an improved version of top which supports advanced visualization features.

To kill a running process use either one of these commands:

<ul>

 <li>&gt; kill –p &lt;pid&gt;</li>

 <li>&gt; pkill</li>

 <li>&gt; killall</li>

</ul>

<h2>Debugging C Programs</h2>

There are multiple debugging tools available for debugging C programs. The gdb debugger is a command line debugger for C (and many other languages). To use gdb debugger, we need to compile the source code with -g compiler flag. (When you compile with -g the compiler includes debugging information in the binary, making it easier for gdb to find bugs.) gdb provides debugging features such as adding breakpoints, step execution, and, examining the call stack.

• Compiling the code in a terminal (console)

&gt; gcc -g -o prog prog.c

<ul>

 <li>Invoke gdb</li>

</ul>

&gt; gdb prog

<ul>

 <li>Run the program inside gdb</li>

</ul>

&gt; run &lt;prog argument1&gt; &lt;prog argument 2&gt;

<h3>Resources on gdb</h3>

<ul>

 <li>Gdb tutorial from UChicago <a href="https://www.classes.cs.uchicago.edu/archive/2017/winter/51081-1/LabFAQ/lab2/gdb.html">https://www.classes.cs.uchicago.edu/archive/2017/winter/51081-1/LabFAQ/ </a><a href="https://www.classes.cs.uchicago.edu/archive/2017/winter/51081-1/LabFAQ/lab2/gdb.html">lab2/gdb.html</a></li>

 <li>Official gdb documentation <a href="https://ftp.gnu.org/old-gnu/Manuals/gdb/html_node/gdb_toc.html">https://ftp.gnu.org/old-gnu/Manuals/gdb/html_node/gdb_toc.html</a></li>

</ul>

Valgrind is a more advanced profiler which helps us debug applications as well as detect performance issues.

It includes advanced features such as detecting race conditions and false sharing.

You may read more on Valgrind at: <a href="http://valgrind.org/docs/manual/manual.html">http://valgrind.org/docs/manual/manual.html</a>


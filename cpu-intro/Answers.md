**1. Run process-run.py with the following flags: -l 5:100,5:100.
What should the CPU utilization be (e.g., the percent of time the
CPU is in use?) Why do you know this? Use the -c and -p flags to
see if you were right.**

The CPU utilization should be 100 percent since the specified process list contains two process whose instructions contains 100 percent of the CPU and thus no I/O.


**2. Now run with these flags: ./process-run.py -l 4:100,1:0.
These flags specify one process with 4 instructions (all to use the
CPU), and one that simply issues an I/O and waits for it to be done.
How long does it take to complete both processes? Use -c and -p
to find out if you were right.**

It takes 11 cpu cycles to complete both processes, 4 for the first process and 7 (one for initializing io and one for its completion and 5 cycles where it is in BLOCKED state) for the second one.


**3. Switch the order of the processes: -l 1:0,4:100. What happens
now? Does switching the order matter? Why? (As always, use -c
and -p to see if you were right)**

Yes, the order matters. When the first process initializes i/o now, it goes into BLOCKED state so second process can be run on cpu. The first process becomes ready after 5 cycles at which point the second process would be completed. Thus, the time for this ordering is only 7 cpu cycles.


**4. We’ll now explore some of the other flags. One important flag is
-S, which determines how the system reacts when a process issues an I/O. With the flag set to SWITCH ON END, the system
will NOT switch to another process while one is doing I/O, instead waiting until the process is completely finished. What happens when you run the following two processes (-l 1:0,4:100
-c -S SWITCH_ON_END), one doing I/O and the other doing CPU
work?**

Since we set SWITCH_ON_END, even when the first process is doing I/O, the second process is not ran and instead cpu waits for the completion of i/o of first process.
Thus the total time will again be 11 cycles.

**5. Now, run the same processes, but with the switching behavior set
to switch to another process whenever one is WAITING for I/O (-l
1:0,4:100 -c -S SWITCH ON IO). What happens now? Use -c
and -p to confirm that you are right.**

This is the same as question no 3 as SWITCH_ON_IO is the default option for -S.

**6. One other important behavior is what to do when an I/O completes. With -I IO RUN LATER, when an I/O completes, the process that issued it is not necessarily run right away; rather, whatever was running at the time keeps running. What happens when
you run this combination of processes? (./process-run.py -l
3:0,5:100,5:100,5:100 -S SWITCH ON IO -c -p -I
IO RUN LATER) Are system resources being effectively utilized?**

The first process initializes i/o when goes to BLOCKED state at which point the second process is run. The i/o is done after 5 cycles and also the second process completes. The third process is now run and after that the fourth. Only then the first process is ran again which has two i/o instruction left. So 10 cpu cycles are wasted waiting for i/o. Thus, the system resources are not being effecctively utilized.

**7.  Now run the same processes, but with -I IO RUN IMMEDIATE set,
which immediately runs the process that issued the I/O. How does
this behavior differ? Why might running a process that just completed an I/O again be a good idea?**

This time when the first i/o of the process complete, it will be run again and it will issue i/o once again and go to BLOCKED state so third process can run at that point. After 5 cycles, the second i/o of first process complete along with the completion of second process. The first process is run again and it issues its last io and goes to blocked state. The third process is run and complete after 5 cycles when the third i/o also completes and the first process is complete again. This takes 21 cycles and 100 cpu is utilized.

The process that just completed an I/O might have other I/O operations to perform which might waste the cpu cycles if left for the end.


**8. Now run with some randomly generated processes using flags -s
1 -l 3:50,3:50 or -s 2 -l 3:50,3:50 or -s 3 -l 3:50,
3:50. See if you can predict how the trace will turn out. What happens when you use the flag -I IO RUN IMMEDIATE versus that
flag -I IO RUN LATER? What happens when you use the flag -S
SWITCH ON IO versus -S SWITCH ON END?**

There are two processes running in each instance, each with 3 instructions and a 50 percent of chance that the instruction will use cpu.

SWITCH_ON_END
This is the easiest one to predict since there will be no switching even when I/O is initialized. At the minimum, there will be 6 cpu cycles if no I/O initialized and the the maximum (all six instructions are I/O), there will be 42 cycles. So on average 24 cpu cycles.

SWITCH_ON_IO
If every instruction is cpu, then minimum of 6 cycles.
If every instruction is i/o, then maximum of 23 cycles.
So on average 14.5 cycles.

IO_RUN_LATER
This is the default, so what ever flag for S is set determines the time.

IO_RUN_IMMEDIATE
This option has no significance when SWITCH_ON_END is set.
For SWITCH_ON_IO, the minimum is again 6 and the maximum is 24.
so average 15.

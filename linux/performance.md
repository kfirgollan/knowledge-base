# Performance

This page contains various performance topic that are relevant for optimizing applications on linux.

## Kernel configurations

1. isolate cpus: Instruct the kernel to avoid scheduling processes on the isolated cores. A process / thread that should run on that core should use set_affinity to pin itself to that core.
2. disable RCU thread: The kernel has a background thread for cleaning up RCU structures. It can be controlled so that it wont run on specific cores. Example `rcu_nocbs=14-21,33-43`.
3. disable scheduler ticks: Reduce the number of scheduler ticks on a per core basis, should be low on pinned cores. Example: `nohz_full=14-21,33-43`.
4. disable NMI watchdog: Disable the watchdog nmi on RT cores. Example `nmi_watchdog=1`
5. disable cstate: Module to control cpu frequency, reduces the frequency when it isn't required to save power. It should be disabled for better performance.

### References

1. [Cpu Affinity](http://nairobi-embedded.org/cpu_affinity.html)
2. [isolcpus](http://www.linuxtopia.org/online_books/linux_kernel/kernel_configuration/re46.html)
3. [NO_HZ](https://www.kernel.org/doc/Documentation/timers/NO_HZ.txt)
4. [Using the NMI Watchdog to detect hangs](https://www.ibm.com/support/knowledgecenter/en/linuxonibm/liaai.crashdump/liaaicrashdumpnmiwatch.htm)
5. [NMI_watchdog](http://elixir.free-electrons.com/linux/v2.6.32/source/Documentation/nmi_watchdog.txt)
6. [Intel CPUs: P-state, C-state, Turbo Boost, CPU freq etc](https://haypo.github.io/intel-cpus.html)
7. [Everything You Need to Know About the CPU C-States Power Saving Modes](http://www.hardwaresecrets.com/everything-you-need-to-know-about-the-cpu-c-states-power-saving-modes/)
8. [CPU idle state](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Power_Management_Guide/Core_Infrastructure.html)


What aspects show system health or stress?
	• CPU: utilization, load average, run queue 
	• Memory: available memory, swap usage, paging 
	• Disk I/O: latency, throughput, iowait 
	• Disk space: usage %, inodes 
	• Network: traffic, errors, drops 
	• Processes: count, zombies, resource usage 
	• System load: overall demand vs capacity 
	• Kernel logs: errors, OOM events 
 Where does Linux expose this information?
Commands
	• CPU/load: top, htop, uptime, vmstat 
	• Memory: free, vmstat 
	• Disk: iostat, df 
	• Network: ss, netstat 
	• Processes: ps, top 
	• Logs: dmesg, journalctl 
/proc files
	• CPU: /proc/stat 
	• Load: /proc/loadavg 
	• Memory: /proc/meminfo 
	• VM stats: /proc/vmstat 
	• Disk: /proc/diskstats 
	• Network: /proc/net/dev 
	• Processes: /proc/[pid]/ 
What values indicate problems?
	• CPU 
		○ Load > number of cores (warning), >2× cores (critical) 
		○ Low idle (<10–20%) 
	• Memory 
		○ Low available memory (<10–15%) 
		○ Active swapping 
		○ High swap usage (>30–50%) 
	• Disk I/O 
		○ iowait >10–20% 
		○ High latency 
	• Disk space 
			§ 80% (warn), >90% (critical)
	• Network 
		○ Increasing errors/drops 
		○ Near max bandwidth 
	• Processes 
		○ Zombies present 
		○ Sudden spikes in process count 
	• Logs 
		○ OOM killer, hardware errors




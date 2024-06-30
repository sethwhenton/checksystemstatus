# System Monitoring Tool

This project is a simple system monitoring tool written in Bash. It provides real-time monitoring of CPU usage, memory usage, disk usage, network statistics, and system uptime.

## Features

- **CPU Usage**: Monitors and displays the current CPU usage.
- **Memory Usage**: Shows the current memory usage.
- **Disk Usage**: Displays the disk usage of the root filesystem.
- **Network Usage**: Provides incoming and outgoing network statistics.
- **System Uptime**: Shows the system uptime in days, hours, and minutes.

## Requirements

- Bash
- Standard Linux utilities (`top`, `free`, `df`, `awk`, `sed`)

## Installation

1. Clone the repository:

   ```bash
   git clone https://github.com/sethwhenton/checksystemstatus.git
   cd checksystemstatus

   make this script executable
chmod +x monitor.sh

run the script
./monitor.sh

The script runs in a loop and updates the metrics every 5 seconds. It provides a real-time display of system statistics.
#!/bin/bash

# Function to get CPU usage
get_cpu_usage() {
    cpu_usage=$(top -bn1 | grep "Cpu(s)" | \
                sed "s/.*, *\([0-9.]*\)%* id.*/\1/" | \
                awk '{print 100 - $1"%"}')
    echo "CPU Usage: $cpu_usage"
}

# Function to get Memory usage
get_memory_usage() {
    memory_usage=$(free -m | awk 'NR==2{printf "Memory Usage: %.2f%%\n", $3*100/$2 }')
    echo "$memory_usage"
}

# Function to get Disk usage
get_disk_usage() {
    disk_usage=$(df -h | awk '$NF=="/"{printf "Disk Usage: %s\n", $5}')
    echo "$disk_usage"
}

# Function to get Network statistics
get_network_usage() {
    # Using /proc/net/dev to get network statistics
    network_usage=$(awk '/eth0/ {print "Network Usage: In: " $2/1024 " KB, Out: " $10/1024 " KB"}' /proc/net/dev)
    echo "$network_usage"
}

# Function to get System uptime
get_system_uptime() {
    system_start_time=$(uptime -s)
    current_time=$(date +%s)
    start_time=$(date -d "$system_start_time" +%s)
    uptime_seconds=$((current_time - start_time))
    uptime_days=$((uptime_seconds / 86400))
    uptime_hours=$(( (uptime_seconds % 86400) / 3600 ))
    uptime_minutes=$(( (uptime_seconds % 3600) / 60 ))
    echo "System Uptime: $uptime_days days, $uptime_hours hours, $uptime_minutes minutes"
}

# Function to display all metrics
display_metrics() {
    echo "<<<< System Monitoring Tool >>>>"
    get_cpu_usage
    get_memory_usage
    get_disk_usage
    get_network_usage
    get_system_uptime
}

# Main loop to refresh metrics every 5 seconds
while true; do
    clear
    display_metrics
    sleep 5
done


Contributions are welcome! Please fork this repository and submit a pull request for any features, bug fixes, or enhancements.

Fork the repository.
Create a new branch (git checkout -b feature/your-feature).
Commit your changes (git commit -am 'Add some feature').
Push to the branch (git push origin feature/your-feature).
Create a new Pull Request.


For any questions or feedback, please contact me at [whentonseth@gmail.com].

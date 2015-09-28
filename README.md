# munin-plugin-docker

The *docker_cpu* and *docker_memory* munin plugins allow us to monitor containers CPU and memory usage over time, respectively.

These plugins read the metrics exposed by control groups (cgroups) on a pseudo-filesystem. We can find the memory metrics for a Docker container under the */sys/fs/cgroup/memory/docker/container_id/* directory and the cpu metrics under the */sys/fs/cgroup/cpuacct/docker/container_id/* directory.

## **docker_memory plugin**

This plugin use the pseudo-file *memory.usage_in_bytes* to get the memory metrics. The munin data type must be defined as "gauge".

## **docker_cpu plugin**

This plugin use the pseudo-file *cpuacct.usage* and *cpuacct.usage_percpu* to get the CPU metrics. The pseudo-file *cpuacct.usage* reports the total CPU time (in nanoseconds) consumed by a container and the pseudo-file *cpuacct.usage_percpu* reports the CPU time (in nanoseconds) consumed on each CPU. This plugin gets the total number of cores available to scale the munin graph.

The munin data type must be defined as "derive" because the CPU usage is a cumulative metric. A derivative allow us to show the difference between data points and thus observe how much CPU usage was used in that period. The munin sample interval is fixed at 300 seconds.

# Configuration

1. Install the munin-node in the docker server. For more information check the following URL: [munin guide](http://guide.munin-monitoring.org/en/latest/installation/install.html).
2. Copy the *docker_cpu* and *docker_memory* plugins to the munin plugin folder: /usr/share/munin/plugins.
3. Change permission to the plugins: `chmod 755 docker_*`
4. Give root privilege to the docker plugin. Root privilege is required for munin to execute the docker command.
  1. Create a new file named "docker" inside the folder /etc/munin/plugin-conf.d/
  2. Insert the following content in the new docker file:
  ```
  [docker_*]
  user root
  ```
5. Restart the munin-node service.

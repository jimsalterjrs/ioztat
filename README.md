# ioztat
ioztat is a storage load analysis tool for OpenZFS. It provides iostat-like statistics at an individual dataset/zvol level.

The statistics offered are read and write operations per second, read and write throughput per second, and the average size (in kilobytes) of read and write operations issued in the current reporting interval. Viewing these statistics at the individual dataset level allows system administrators to identify storage "hot spots" in larger multi-tenant systems--particularly those with many VMs or containers operating essentially independent workloads.

This sample output shows activity which has taken place in the most recent second, on a the `ssd` zpool of a ZFS virtualization host:

````
root@redacted-prod0:~# ioztat -y -c1 ssd
dataset                               w/s      wMB/s        r/s      rMB/s   wareq-sz   rareq-sz
ssd                                   0.00       0.00       0.00       0.00       0.00       0.00
   images                             0.00       0.00       0.00       0.00       0.00       0.00
      DC1                            17.96       0.10       3.99       0.05       5.66      12.29
      DC2                            21.95       0.14       0.00       0.00       6.59       0.00
      QB                              2.00       0.01       0.00       0.00       7.17       0.00
      SAP-TC                          2.99       0.02       0.00       0.00       6.83       0.00
      SAP4-WIN2019                    2.99       0.05       0.00       0.00      17.07       0.00
      nagios                          0.00       0.00       0.00       0.00       0.00       0.00
      qemu                            0.00       0.00       0.00       0.00       0.00       0.00
         autostart                    0.00       0.00       0.00       0.00       0.00       0.00
   iso                                0.00       0.00       0.00       0.00       0.00       0.00
   unsnapped                          0.00       0.00       0.00       0.00       0.00       0.00
      rp9                             0.00       0.00       0.00       0.00       0.00       0.00
````

For the most part, `ioztat` behaves the same way that the system standard `iostat` tool does, with similar arguments.

````
usage: ioztat [-h] [-s {name,rps,wps,rMBps,wMBps}] [-i INTERVAL] [-c COUNT] [-y] [-b] [-n] [-z]
              [-P | -p]
              dataset [dataset ...]

iostat for ZFS datasets

positional arguments:
  dataset               ZFS dataset

optional arguments:
  -h, --help            show this help message and exit
  -s {name,rps,wps,rMBps,wMBps}
                        Field to sort by
  -i INTERVAL           Time between each report
  -c COUNT              Number of reports generated
  -y                    Skip the initial "summary" report
  -b                    Use binary (power-of-two) prefixes
  -n                    Do not recurse into child datasets
  -z                    Suppress datasets with zero activity
  -P                    Display dataset names on a single line
  -p                    Display dataset names as an abbreviated tree
  ````

The only required argument is the name of at least one dataset to monitor. Without any other arguments, `ioztat` first prints a summary record showing activity per dataset since the most recent system boot, then prints a new record showing the most recent activity once per second. The `-i` argument can be used to change the report interval, and the `-c` argument can be used to limit `ioztat` to a certain number of intervals before exiting.

For those who wish a continually-updated, easy to read summary of pool activity, `watch -n1 ioztat datasetname -c1 -y` will suit nicely on Linux systems--on FreeBSD systems, you'll need to use `gnu-watch` (available via `pkg install gnu-watch`) instead.

# ioztat
ioztat is a storage load analysis tool for OpenZFS. It provides iostat-like statistics at an individual dataset/zvol level.

The statistics offered are read and write operations per second, read and write throughput per second, and the average size of read and write operations issued in the current reporting interval. Viewing these statistics at the individual dataset level allows system administrators to identify storage "hot spots" in larger multi-tenant systems--particularly those with many VMs or containers operating essentially independent workloads.

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
usage: ioztat [-b] [-c COUNT] [-e] [-H] [-h] [-i INTERVAL] [-N] [-n] [-o] [-P | -p]
              [-s {name,io,reads,writes,bytes,nread,nwritten}] [-T {u,d}] [-V] [-y] [-z]
              [dataset [dataset ...]]

iostat for ZFS datasets

positional arguments:
  dataset               ZFS dataset

optional arguments:
  -b                    use binary (power-of-two) prefixes
  -c COUNT              number of reports generated
  -e                    show exact values without truncation or scaling
  -H                    scripted mode, skip headers and tab-separate
  -h, --help            show this help message and exit
  -i INTERVAL           interval between reports (in seconds)
  -N                    display headers at most once
  -n                    do not recurse into child datasets
  -o                    overwrite old reports in terminal
  -P                    display dataset names on a single line
  -p                    display dataset names as an abbreviated tree
  -s {name,io,reads,writes,bytes,nread,nwritten}
                        sort by the specified field
  -T {u,d}              prefix each report with a Unix timestamp or formatted date
  -V, --version         show program's version number and exit
  -y                    skip the initial "summary" report
  -z                    suppress datasets with zero activity
  ````

Without arguments, `ioztat` first prints a summary record showing activity for each mounted dataset since the most recent system boot, then prints a new record showing the most recent activity once per second. The `-i` argument can be used to change the report interval, and the `-c` argument can be used to limit `ioztat` to a certain number of intervals before exiting.

For a continually-updated, easy to read summary of pool activity, the `-o` argument will produce output similar to that of GNU watch.
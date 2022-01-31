# ioztat
ioztat is a storage load analysis tool for OpenZFS. It provides iostat-like statistics at an individual dataset/zvol level.

The statistics offered are read and write operations per second, read and write throughput per second, and the average size of read and write operations issued in the current reporting interval. Viewing these statistics at the individual dataset level allows system administrators to identify storage "hot spots" in larger multi-tenant systems--particularly those with many VMs or containers operating essentially independent workloads.

This sample output shows activity which has taken place in the most recent second, on a the `ssd` zpool of a ZFS virtualization host:

````
root@redacted-prod0:~# ioztat -y -c1 ssd
                   operations    throughput      opsize
dataset            read  write   read  write   read  write
----------------  -----  -----  -----  -----  -----  -----
ssd                   0      0      0      0      0      0
  images              0      0      0      0      0      0
    DC1               4     18  49.0K  98.0K  12.5K  5.56K
    DC2               0     22      0   137K      0  6.36K
    QB                0      2      0  9.80K      0  5.00K
    SAP-TC            0      3      0  19.6K      0  6.67K
    SAP4-WIN2019      0      3      0  49.0K      0  16.7K
    nagios            0      0      0      0      0      0
    qemu              0      0      0      0      0      0
      autostart       0      0      0      0      0      0
  iso                 0      0      0      0      0      0
  unsnapped           0      0      0      0      0      0
    rp9               0      0      0      0      0      0
----------------  -----  -----  -----  -----  -----  -----

````

For the most part, `ioztat` behaves the same way that the system standard `iostat` tool does, with similar arguments.

````
usage: ioztat [-c COUNT] [-D] [-e] [-H] [-h] [-i INTERVAL] [-N] [-n] [-o] [-P | -p]
              [-s {name,operations,reads,writes,throughput,nread,nwritten}] [-T {u,d}] [-V] [-y] [-z]
              [dataset [dataset ...]]

iostat for ZFS datasets

positional arguments:
  dataset               ZFS dataset

optional arguments:
  -c COUNT              number of reports generated
  -D                    display bytes in decimal powers of 1000 instead of 1024
  -e                    show exact values without truncation or scaling
  -H                    scripted mode, skip headers and tab-separate
  -h, --help            show this help message and exit
  -i INTERVAL           interval between reports (in seconds)
  -N                    display headers at most once
  -n                    do not recurse into child datasets
  -o                    overwrite old reports in terminal
  -P                    display dataset names on a single line
  -p                    display dataset names as an abbreviated tree
  -s {name,operations,reads,writes,throughput,nread,nwritten}
                        sort by the specified field
  -T {u,d}              prefix each report with a Unix timestamp or formatted date
  -V, --version         show program's version number and exit
  -y                    skip the initial "summary" report
  -z                    suppress datasets with zero activity
  ````

Without arguments, `ioztat` first prints a summary record showing activity for each mounted dataset since the most recent system boot, then prints a new record showing the most recent activity once per second. The `-i` argument can be used to change the report interval, and the `-c` argument can be used to limit `ioztat` to a certain number of intervals before exiting.

For a continually-updated, easy to read summary of pool activity, the `-o` argument will produce output similar to that of GNU watch.
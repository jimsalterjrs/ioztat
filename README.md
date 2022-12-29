# ioztat

`ioztat` is a storage load analysis tool for OpenZFS. It provides iostat-like statistics at an individual dataset/zvol level, complementing the pool/vdev level statistics offered by `zpool iostat`.

The statistics offered are per-second or per-interval read/write operations and throughput, plus optional average operation size and ZFS file unlink queue depths.

Viewing these statistics at the individual dataset level allows system administrators to identify storage "hot spots" in larger multi-tenant systems — particularly those with many VMs or containers operating essentially independent workloads.

This sample output shows activity which has taken place in the most recent second, on the `ssd` zpool of a ZFS virtualization host, with extended statistics showing the average I/O size:

```
root@redacted-prod0:~# ioztat -yx ssd
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
```

## Usage

`ioztat` behaves similarly to standard Unix `iostat` tools and the ZFS `zpool iostat` command:

```
usage: ioztat [-c COUNT] [-D] [-e] [-H] [-h] [-I] [-i INTERVAL] [-N] [-n] [-o]
              [-P | -p] [-S]
              [-s {name,operations,reads,writes,throughput,nread,nwritten}]
              [-T {u,d}] [-V] [-x] [-y] [-z]
              [dataset [dataset ...]] [interval [count]]

iostat for ZFS datasets

positional arguments:
  dataset               ZFS dataset
  interval [count]      seconds between reports and number of reports

optional arguments:
  -c COUNT              number of reports generated
  -D                    display size in decimal powers of 1000 instead of 1024
  -e                    display exact values without truncation or scaling
  -H                    scripted mode, omit headers and tab-separate fields
  -h, --help            display this help message and exit
  -I                    display totals since the last report rather than
                        averaged per-second
  -i INTERVAL           interval between reports in seconds
  -N                    display headers at most once
  -n                    omit child datasets when filtering
  -o                    overwrite old reports in terminal
  -P                    display dataset names on a single line
  -p                    display dataset names as an abbreviated tree
  -S                    include statistics for child datasets in parents
  -s {name,operations,reads,writes,throughput,nread,nwritten}
                        sort by the specified field
  -T {u,d}              prefix reports with a Unix timestamp or formatted date
  -V, --version         display version number and exit
  -x                    display extended statistics: once for average I/O
                        size, twice for unlink queue
  -y                    omit the initial "summary" report
  -z                    omit datasets with zero activity
```

Without arguments, `ioztat` prints a summary of activity for each mounted dataset since the most recent system boot and exits. This initial summary can be skipped with the `-y` flag.

With an optional interval, `ioztat` will repeat reports on that schedule until interrupted, or up to a specified count.  If only a count is specified, the interval defaults to one second. Interval and count can be specified with `-i` and `-c` or as positional arguments at the very end of the argument list.

The `-o` flag will overwrite prior output and limit the display to the terminal height. This can be combined with an interval and sorting options for an `iotop`-like experience.

## Examples

    ioztat -z 5

Display statistics for every dataset with activity, giving per-second averages over 5 second intervals.

    ioztat -ozs operations 5

As above, but order output by read/write operations and overwrite previous reports.

    ioztat -s ops -l "10:0:-1"  

Reverse order of top ten results.

    ioztat -I

Display sum totals of dataset activity since boot.

    ioztat -SIn rpool/USERDATA

As above, but only display rpool/USERDATA, and combine statistics for any child datasets with it.


## Requirements

* Python 3.7 or later
* Linux with OpenZFS 0.8 or later
* FreeBSD 12.2 or later (available in ports as `sysutils/py-ioztat`)

# Changelog

## 2.0.0-dev - unreleased

### Added

- An `iostat(8)` man page
- `CHANGELOG.md`, this Changelog
- `-e` and `-H` flags for use in scripting [#26]
- `-I` flag for totals since last update instead of per-second
- `-o` flag to overwrite prior reports [#9] [#20]
- `-P` and `-p` flags to override dataset name display [#19]
- `-S` flag for including child dataset statistics in parents [#32]
- `-T d|u` flag for adding a timestamp to each report [#25]
- `-V`, `--version` flags [#13] [#14]
- `-x` flag for extended statistics, including unlink counts under `-xx` [#33]
- `interval` and `count` positional arguments [#31]

### Changed

- Shebang line is now `/usr/bin/env python3` [#18]
- Formatting has changed from resembling GNU `iostat(8)` to ZFS `zpool-iostat(8)` [#26]
- Header is now printed periodically on a tty unless `-N` is specified [#26]
- Display is clamped to the terminal width by truncating dataset names if necessary [#26]
- Sort field names have been changed, with fallbacks for compatibility [#26]
- Sort options are now case-insensitive [#26]
- `count` now defaults to 1 unless an `interval` is specified [#31]
- `dataset` is now an optional argument [#20]
- Binary (1024-based) formatting is now default, with new `-D` flag for decimal
- Average I/O sizes are now hidden beyind `-x` flag by default to reduce clutter [#33]
- Exit with an error if a requested dataset does not exist or is not mounted

### Removed

- `-b` flag. Binary mode is now the default to match other iostat tools.

### Fixed

- `count` and `interval` must now be positive [#31]
- `WIFSIGNALED()` status is now propagated properly to caller
- Sleep interval now adjusts to compensate for runtime
- Unhandled `BrokenPipeError` exception on `SIGPIPE`
- Unhandled `FileNotFoundError` exception on Linux if a dataset is destroyed while enumerating files in `/proc`
- Unhandled `CalledProcessError` exception on FreeBSD if a requested pool does not exist when using `sysctl(8)` fallback

## [1.1.0] - 2022-01-20

### Added

- FreeBSD support, supporting `sysctl(8)` and `devel/py-sysctl` [#2]
- `-n` flag to omit child datasets [#11]
- `-z` flag to omit datasets with no activity [#12] [#14]

### Changed

- `pool` argument now supports an arbitrary `dataset` [#11]

### Fixed

- When printing only the last segment of a dataset name, ensure the full path is always printed [#5] [#7]

## [1.0.0] - 2022-01-12

The first release of `ioztat` builds on efforts from the Reddit r/zfs community, including:

- u/55rzs (initial creation)
- u/d1722825 (substantial refactoring and cleanup)
- u/mercenary_sysadmin (addition of the -y flag to allow for easy use with the GNU watch command)

[1.1.0]: https://github.com/jimsalterjrs/ioztat/releases/tag/v1.1.0
[1.0.0]: https://github.com/jimsalterjrs/ioztat/releases/tag/v1.0.0
[#2]: https://github.com/jimsalterjrs/ioztat/pull/2
[#5]: https://github.com/jimsalterjrs/ioztat/issues/5
[#7]: https://github.com/jimsalterjrs/ioztat/pull/7
[#9]: https://github.com/jimsalterjrs/ioztat/pull/9
[#11]: https://github.com/jimsalterjrs/ioztat/pull/11
[#12]: https://github.com/jimsalterjrs/ioztat/issues/12
[#13]: https://github.com/jimsalterjrs/ioztat/issues/13
[#14]: https://github.com/jimsalterjrs/ioztat/pull/14
[#18]: https://github.com/jimsalterjrs/ioztat/issues/18
[#19]: https://github.com/jimsalterjrs/ioztat/pull/19
[#20]: https://github.com/jimsalterjrs/ioztat/pull/20
[#25]: https://github.com/jimsalterjrs/ioztat/pull/25
[#26]: https://github.com/jimsalterjrs/ioztat/pull/26
[#31]: https://github.com/jimsalterjrs/ioztat/pull/31
[#32]: https://github.com/jimsalterjrs/ioztat/pull/32
[#33]: https://github.com/jimsalterjrs/ioztat/pull/33

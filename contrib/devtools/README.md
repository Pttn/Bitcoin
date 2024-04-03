Contents
========
This directory contains tools for developers working on this repository.

gen-manpages.py
===============

A small script to automatically create manpages in ../../doc/man by running the release binaries with the -help option.
This requires help2man which can be found at: https://www.gnu.org/software/help2man/

With in-tree builds this tool can be run from any directory within the
repository. To use this tool with out-of-tree builds set `BUILDDIR`. For
example:

```bash
BUILDDIR=$PWD/build contrib/devtools/gen-manpages.py
```

headerssync-params.py
=====================

A script to generate optimal parameters for the headerssync module (src/headerssync.cpp). It takes no command-line
options, as all its configuration is set at the top of the file. It runs many times faster inside PyPy. Invocation:

```bash
pypy3 contrib/devtools/headerssync-params.py
```

gen-bitcoin-conf.sh
===================

Generates a bitcoin.conf file in `share/examples/` by parsing the output from `bitcoind --help`. This script is run during the
release process to include a bitcoin.conf with the release binaries and can also be run by users to generate a file locally.
When generating a file as part of the release process, make sure to commit the changes after running the script.

With in-tree builds this tool can be run from any directory within the
repository. To use this tool with out-of-tree builds set `BUILDDIR`. For
example:

```bash
BUILDDIR=$PWD/build contrib/devtools/gen-bitcoin-conf.sh
```

security-check.py and test-security-check.py
============================================

Perform basic security checks on a series of executables.

symbol-check.py
===============

A script to check that release executables only contain
certain symbols and are only linked against allowed libraries.

For Linux this means checking for allowed gcc, glibc and libstdc++ version symbols.
This makes sure they are still compatible with the minimum supported distribution versions.

For macOS and Windows we check that the executables are only linked against libraries we allow.

Example usage:

    find ../path/to/executables -type f -executable | xargs python3 contrib/devtools/symbol-check.py

If no errors occur the return value will be 0 and the output will be empty.

If there are any errors the return value will be 1 and output like this will be printed:

    .../64/test_bitcoin: symbol memcpy from unsupported version GLIBC_2.14
    .../64/test_bitcoin: symbol __fdelt_chk from unsupported version GLIBC_2.15
    .../64/test_bitcoin: symbol std::out_of_range::~out_of_range() from unsupported version GLIBCXX_3.4.15
    .../64/test_bitcoin: symbol _ZNSt8__detail15_List_nod from unsupported version GLIBCXX_3.4.15

circular-dependencies.py
========================

Run this script from the root of the source tree (`src/`) to find circular dependencies in the source code.
This looks only at which files include other files, treating the `.cpp` and `.h` file as one unit.

Example usage:

    cd .../src
    ../contrib/devtools/circular-dependencies.py {*,*/*,*/*/*}.{h,cpp}

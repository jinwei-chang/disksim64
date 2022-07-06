
This directory contains release 4.0 of the DiskSim storage subsystem
simulator.  Check out the doc directory for he corresponding reference 
manual that describes the simulator and how to use it.

See the file COPYING for the copyright notice and copying conditions.

To quickly verify that you've got everything and that it works:

  1. "make" the top-level directory.
    
  2. "cd" to the subdirectory called "valid".

  3. type "runvalid".  This will execute the simulator a number of times, using
     sample configurations and workloads.  Among them are example validation
     experiments for a number of different SCSI disk drives.  To
     verify that things are working correctly, compare the result
     values from each execution to the expected value (provided on the
     preceding line), which is rounded.

If you plan to use disksim as a stand-alone simulator, these examples and
the user manual should get you started.

If you plan to incorporate disksim into a larger-scale simulator (e.g., a
full system simulator), disksim_interface.c should be very helpful in
getting it to happen quickly and relatively painlessly.  It is not compiled
into disksim for standalone operation.  Thanks to Eran Gabber at Lucent,
there is now a simple example of a system-level simulator incorporating
disksim as a slave -- check out syssim* (before and after compilation).

Please send bug reports, experiences, and problems to disksim@ece.cmu.edu.
If you find disksim useful, please let us know about it!

There are two public mailing lists for disksim:
disksim-announce@ece.cmu.edu
disksim-users@ece.cmu.edu

disksim-announce only receives official announcements about bugfixes
and new versions of DiskSim.  disksim-users is for public discussion.

Please visit one of these sites to join the mailing lists:

https://sos.ece.cmu.edu/mailman/listinfo/disksim-announce
https://sos.ece.cmu.edu/mailman/listinfo/disksim-users

# Other Note

## Environment

* 需要安裝`flex`和`bison`

```bash
sudo apt install flex bison
```

## Build 

* 直接`make`即可使用
* build完成的執行檔為`src/disksim`

```bash
make
```

* 使用`make clean`再執行`make`重新安裝

```bash
make clean
make
```

## Test

```bash
cd valid
bash runvalid
```

## DiskSim Command

* Example: Seagate-Cheetah15k5

```bash
disksim models/Seagate-Cheetah15k5.parv output_file ascii trace_file 0
grep "Disk Seek distance average" output_file
```

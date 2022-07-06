
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

## 可能需要修改的code

line 107 at `src\disksim_logorg.c` `#define MAX_QUEUE_LENGTH 20000000`

## Model修改

以`models\Seagate-Cheetah15k5-skews.layout`為例

1. TP。作用是先定義track的大小，index從0開始計算(`0, 959, 960,`是index`0`，`0, 1198, 1199,`是index`1`，以此類推)。其中`0, 959, 960,`，第1個數字為track開頭的index，第2個數字為track結尾的index，第3個數字為track的index總數(就是0到959有960個index的意思)。
2. IDX。定義區塊。最後一個區塊，作為index到不同區塊的功能。
3. 一般的區塊。如

```
[ 
  0, 0, 0.0, 1200, 1, 0.141669, 78000, 65, TRACK, 5, 0, 
  78000, 64, 0.256200, 1200, 1, 0.141666, 78000, -65, TRACK, 5, 1, 
  156000, 0, 0.514519, 1200, 1, 0.141670, 78000, 65, TRACK, 5, 2, 
  234000, 64, 0.773741, 1200, 1, 0.141666, 78000, -65, TRACK, 5, 3, 
  312000, 65, 0.211047, 1200, 1, 0.141667, 78000, 65, TRACK, 5, 3, 
  390000, 129, 0.468649, 1200, 1, 0.141674, 78000, -65, TRACK, 5, 2, 
  468000, 65, 0.727026, 1200, 1, 0.141667, 78000, 65, TRACK, 5, 1, 
  546000, 129, 0.984742, 1200, 1, 0.141661, 78000, -65, TRACK, 5, 0
], 
```

其中再解釋`0, 0, 0.0, 1200, 1, 0.141669, 78000, 65, TRACK, 5, 0, `
   1. sector(block) index開頭
   2. track index開頭
   3. offset
   4. index size (一個track有幾個index)
   5. 不確定(應該是有幾個子區塊)
   6. offset
   7. index size (有多少個index在子區塊。1200*65=78000)
   8. track結尾方向 (`0,65`就是指0~64。`64,-65`就是指64~0)
   9. TRACK或IDX。
      1.  如果是TRACK。則接著TP的index。像是這邊是指向`5`，`0, 1199, 1200`的TP。然後再接上第幾片的disk。這邊的`0`就是指第`0`片的disk
      2.  如果是IDX。則是指向第幾個區塊的意思。

### Model 修改範例

如果要修改成IMR，然後只使用單片disk。

1. 修改TP

```
TP = [ 
      0, 2049, 2050, 
      0, 1844, 1845
],
```

2. 修改IDX的一般區塊

```
IDX = [ 
[ 
    0,    0,    0.0,       2050,   1,  0.141669,   2050,   1,  TRACK,  0,  0, 
    2050, 1,    0.256200,  1845,   1,  0.141666,   1845,   1,  TRACK,  1,  0
], 
...(other)]

```

3. 修改IDX最後一個區塊

```
IDX = [ 
...(other),
[ 
    0,    0,   0.0,    3895,     2,    0.425060,   7790000,    4000,   IDX,    0
], 
]
```
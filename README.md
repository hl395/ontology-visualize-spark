# ontology-visualize-spark
Course project for CS786 Special Topics: Parallel Computing for Multicore Systems

The goal of this project is to utilize Spark’s[3] resilient distributed dataset (RDD)[2] feature to store ontology class-restriction topology information, and employ the build in batch operations to facilitate graph computations, such as multiple route identification, subgraph, group coloring. In addition, the data wrapper/holder -- RDDs can be used as input to graph plot tool to draw 3D graphs for better ontology visualization.

readlink -f /usr/bin/java | sed "s:bin/java::"

conda develop 'directory_path'


On Ubuntu 16.04, packages build-essential and python-dev should already be installed, but some other packages are missing, lxml2 and lz. Install them like so:
sudo apt install libxml2-dev libz-dev
That should be all. We can now install igraph for Python 3:
sudo pip3 install python-igraph



conda env export -n name > name.yml
conda env upload my-environment


hao@hao-OptiPlex-7010:~$ cat /proc/cpuinfo | grep 'model name' | uniq

model name	: Intel(R) Core(TM) i7-3770 CPU @ 3.40GHz

hao@hao-OptiPlex-7010:~$ cat /proc/cpuinfo | grep processor

processor	: 0
processor	: 1
processor	: 2
processor	: 3
processor	: 4
processor	: 5
processor	: 6
processor	: 7

hao@hao-OptiPlex-7010:~$ sudo lshw -C memory

  *-firmware              
       description: BIOS
       vendor: Dell Inc.
       physical id: 0
       version: A05
       date: 07/31/2012
       size: 64KiB
       capacity: 13MiB
       capabilities: pci pnp upgrade shadowing cdboot bootselect edd int13floppy1200 int13floppy720 int13floppy2880 int5printscreen int9keyboard int14serial int17printer acpi usb biosbootspecification netboot uefi
  *-cache:0
       description: L1 cache
       physical id: 38
       slot: CPU Internal L1
       size: 256KiB
       capacity: 256KiB
       capabilities: internal write-through data
       configuration: level=1
  *-cache:1
       description: L2 cache
       physical id: 39
       slot: CPU Internal L2
       size: 1MiB
       capacity: 1MiB
       capabilities: internal write-through unified
       configuration: level=2
  *-cache:2
       description: L3 cache
       physical id: 3a
       slot: CPU Internal L3
       size: 8MiB
       capacity: 8MiB
       capabilities: internal write-back unified
       configuration: level=3
  *-memory
       description: System Memory
       physical id: 3b
       slot: System board or motherboard
       size: 16GiB
     *-bank:0
          description: DIMM DDR3 Synchronous 1600 MHz (0.6 ns)
          product: M378B5273DH0-CK0
          vendor: Samsung
          physical id: 0
          serial: 1160F37D
          slot: ChannelA-DIMM0
          size: 4GiB
          width: 64 bits
          clock: 1600MHz (0.6ns)
     *-bank:1
          description: DIMM DDR3 Synchronous 1600 MHz (0.6 ns)
          product: M378B5273DH0-CK0
          vendor: Samsung
          physical id: 1
          serial: 1160F37C
          slot: ChannelA-DIMM1
          size: 4GiB
          width: 64 bits
          clock: 1600MHz (0.6ns)
     *-bank:2
          description: DIMM DDR3 Synchronous 1600 MHz (0.6 ns)
          product: M378B5273DH0-CK0
          vendor: Samsung
          physical id: 2
          serial: 1160F377
          slot: ChannelB-DIMM0
          size: 4GiB
          width: 64 bits
          clock: 1600MHz (0.6ns)
     *-bank:3
          description: DIMM DDR3 Synchronous 1600 MHz (0.6 ns)
          product: M378B5273DH0-CK0
          vendor: Samsung
          physical id: 3
          serial: 1160F381
          slot: ChannelB-DIMM1
          size: 4GiB
          width: 64 bits
          clock: 1600MHz (0.6ns)
 
hao@hao-ThinkPad-X220-Tablet:~$ cat /proc/cpuinfo | grep 'model name'

model name	: Intel(R) Core(TM) i7-2640M CPU @ 2.80GHz
model name	: Intel(R) Core(TM) i7-2640M CPU @ 2.80GHz
model name	: Intel(R) Core(TM) i7-2640M CPU @ 2.80GHz
model name	: Intel(R) Core(TM) i7-2640M CPU @ 2.80GHz 

hao@hao-ThinkPad-X220-Tablet:~$ cat /proc/cpuinfo | grep processor

processor	: 0
processor	: 1
processor	: 2
processor	: 3

hao@hao-ThinkPad-X220-Tablet:~$ sudo lshw -C memory
 
  *-cache:0               
       description: L1 cache
       physical id: 2
       slot: L1-Cache
       size: 64KiB
       capacity: 64KiB
       capabilities: synchronous internal write-through data
       configuration: level=1
  *-cache:1
       description: L2 cache
       physical id: 3
       slot: L2-Cache
       size: 256KiB
       capacity: 256KiB
       capabilities: synchronous internal write-through data
       configuration: level=2
  *-cache:2
       description: L3 cache
       physical id: 4
       slot: L3-Cache
       size: 4MiB
       capacity: 4MiB
       capabilities: synchronous internal write-back unified
       configuration: level=3
  *-memory
       description: System Memory
       physical id: 5
       slot: System board or motherboard
       size: 8GiB
     *-bank:0
          description: SODIMM DDR3 Synchronous 1333 MHz (0.8 ns)
          product: M471B5273DH0-CH9
          vendor: Samsung
          physical id: 0
          serial: 94788C86
          slot: ChannelA-DIMM0
          size: 4GiB
          width: 64 bits
          clock: 1333MHz (0.8ns)
     *-bank:1
          description: SODIMM DDR3 Synchronous 1333 MHz (0.8 ns)
          product: M471B5273DH0-CH9
          vendor: Samsung
          physical id: 1
          serial: 94788C8B
          slot: ChannelB-DIMM0
          size: 4GiB
          width: 64 bits
          clock: 1333MHz (0.8ns)
  *-firmware
       description: BIOS
       vendor: LENOVO
       physical id: d
       version: 8DET72WW (1.42 )
       date: 02/18/2016
       size: 128KiB
       capacity: 8128KiB
       capabilities: pci pnp upgrade shadowing cdboot bootselect edd int13floppy720 int5printscreen int9keyboard int14serial int17printer int10video acpi usb biosbootspecification

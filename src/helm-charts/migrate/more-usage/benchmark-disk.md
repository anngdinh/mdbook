# Benchmark with FIO

We have an operation backup in persistent volume by using restic or kopia to access file system in volume, copy to vStorage.
We need to evaluate the impact of this operation to app usage:

Use FIO tool to simulates a fixed amount of IOPS written to disk and outputs the real value it can achieve.
Assume a disk with max 200 IOPS, app normal usage is 100 IOPS, evaluate real IOPS app can achieve when normal and when backup.

Install fio:

```bash
sudo apt update
sudo apt install fio -y
```

Create `fio_test.fio` with config:

```yaml
[global]
direct=1                            ; Use direct I/O (bypass filesystem)
iodepth=1                           ; Queue depth of 1 for focused IOPS control

[job]
name=iotest                         ; Job name
rw=write                            ; Write operation
bs=4k                               ; Block size (adjust as needed)
runtime=20s                         ; Runtime (adjust for longer test)

stonewall                           ; Tell fio to maintain requested IOPS rate

size=100000M
rate_iops=10000                     ; Set your target IOPS value

group_reporting                     ; Report results grouped by job

log_avg_msec=1000                   ; Report interval

write_iolog=filename=log_output.txt
```

## Fio Output Explained

```bash
iotest: (groupid=0, jobs=1): err= 0: pid=20058: Fri May 10 10:35:52 2024
  write: IOPS=9999, BW=39.1MiB/s (41.0MB/s)(781MiB/20001msec); 0 zone resets                                                    ; The achieved IOPS and BW
    clat (usec): min=16, max=8913, avg=25.71, stdev=25.11
     lat (usec): min=17, max=8927, avg=26.90, stdev=25.26
    clat percentiles (usec):
     |  1.00th=[   19],  5.00th=[   20], 10.00th=[   21], 20.00th=[   22],
     | 30.00th=[   23], 40.00th=[   24], 50.00th=[   25], 60.00th=[   26],
     | 70.00th=[   27], 80.00th=[   29], 90.00th=[   32], 95.00th=[   36],
     | 99.00th=[   50], 99.50th=[   63], 99.90th=[   87], 99.95th=[   95],
     | 99.99th=[  255]
   bw (  KiB/s): min=40016, max=40040, per=100.00%, avg=40021.74, stdev= 5.49, samples=39
   iops        : min=10004, max=10010, avg=10005.44, stdev= 1.37, samples=39
  lat (usec)   : 20=8.58%, 50=90.46%, 100=0.92%, 250=0.03%, 500=0.01%
  lat (usec)   : 750=0.01%
  lat (msec)   : 2=0.01%, 4=0.01%, 10=0.01%
  cpu          : usr=8.96%, sys=16.40%, ctx=394451, majf=0, minf=12
  IO depths    : 1=100.0%, 2=0.0%, 4=0.0%, 8=0.0%, 16=0.0%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued rwts: total=0,200001,0,0 short=0,0,0,0 dropped=0,0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=1

Run status group 0 (all jobs):
  WRITE: bw=39.1MiB/s (41.0MB/s), 39.1MiB/s-39.1MiB/s (41.0MB/s-41.0MB/s), io=781MiB (819MB), run=20001-20001msec

Disk stats (read/write):
  nvme0n1: ios=0/198015, merge=0/122, ticks=0/2129, in_queue=2130, util=99.54%
```

`slat`: submission latency. It is pretty much what it sounds like, meaning "how long did it take to submit this IO to the kernel for processing?". Different for each device SATA, SAS,...
`clat`: completion latency. This is the time that passes between submission to the kernel and when the IO is complete, not including submission latency. In older versions of fio, this was the best metric for approximating application-level latency.
`lat`: metric starts the moment the IO struct is created in fio and is completed right after clat, making this the one that best represents what applications will experience
`clat percentiles (usec)`: Completion latency percentiles are fairly self-explanatory and probably the most useful bit of info in the output. I looked at the source code and this is not slat + clat; it is tracked in its own struct.

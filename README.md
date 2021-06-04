https://unix.stackexchange.com/questions/572187/why-doesnt-linux-automatically-clear-caches


unix.stackexchange.com
Why doesn't Linux automatically clear caches?
AttilioAttilio 27522 silver badges66 bronze badges
3 minutes

    Why doesn't Linux automatically clear caches?

It will.

ron> free -g
             total       used       free     shared    buffers     cached
Mem:           504        415         88          1          0        352
-/+ buffers/cache:         62        441
Swap:            0          0          0

Cached memory should still be considered free memory.

When free runs down to 0, then it will pull from cached.

    Could this harm the system in any way, or cause any negative effects?

harm = no. Research echo 3 > /proc/sys/vm/drop_caches and you will see it is a non-destructive operation.

the negative effect would be the performance penalty incurred by having to read something off of disk that would have been in cache. For example write a C program that reads in a 10GB data file. The first time run it will be slow because it's reading from disk but anytime after will be much quicker because that data file is cached in ram. Drop the cache and next subsequent run of program reading data file will be slow like the first time it was run. This is easily observable and repeatable.

    If the answer is no then why does not Linux run this command automatically by default?

You would have to elaborate how and where you see this happen. I am not aware of it on current linux such as RHEL/CentOS 7.7. But older linux's (such as pre 3.x kernel) it may have been a decision made by that distribution back when caching was not as robust as it is now (in my opinion). Using SLES 11.4 a few years ago I am pretty sure SuSE people did not write code to make that drop cache automatically happen, but for some work servers I had the folks we bought them from and configured SLES for us at the time has done a crontab to do periodic drop caching, so kind of a gray area as to why it's done. I suspect it's a housekeeping=good mentality and because it's never a destructive command can't really hurt. The few times I manually did echo 3 > drop_caches back on sles 11.4 was for troubleshooting as it never really solved anything where ultimately a reboot was needed.

Look into RHEL performance tuning guide, and tuning virtual memory. I don't know how much of this is RHEL specific versus being low level enough to be all linux kernel related and not specific to a linux distribution. Also what has changed from kernel 2.6 to 3.x to 4.x which I am sure is significant in this regard, so consider reading in to linux kernel release notes.

+++
date = "2019-07-29T18:10:01+02:00"
title = "Killing node zombies"

+++

# Killing node zombies

When you are running a lot of watchers and particularly using `concurrently` to run multiple tasks at the same time, my WSL terminal tends to loose track of the processes.

The worst is when one of these processes is using one of your network ports, well, not _really_ the worst... Anyway, I often want to list all node processes running on my Ubuntu.

How to know, in Linux Ubuntu, which nodejs processes are running, even on the background?

By listing the processes and _grepping_ to filter the one with `node` in the name.

```
ps -aux | grep node
```

Then just kill them by ID... with `-9` if needed

Check the man page of `ps` to get an idea of what it is actually doing and what other magic you can achieve with it (`man ps`).

I had searched for this several times, only to find it now in [Stack Overflow](https://stackoverflow.com/questions/21575808/find-node-js-instance-on-ubuntu), before I miss it again, I post it here.

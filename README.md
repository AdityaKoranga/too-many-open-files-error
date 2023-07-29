# too-many-open-files-error


```
$ kubectl -n omec logs amf-6dd746b9cd-2mk2j
...
...
} (resolver returned new addresses)
2023/01/24 17:24:56 INFO: [core] [Channel #1] Channel switches to new LB policy "pick_first"
2023/01/24 17:24:56 INFO: [core] [Channel #1 SubChannel #2] Subchannel created
2023/01/24 17:24:56 too many open files
As the message shows, the problem is due to “too many open files”.
```

To resolve this issue, the user can increase the maximum number of available watches and the maximum number of inotify instances (e.g., 10x). To do so, first, see the current maximum numbers:

```bash
sysctl fs.inotify.max_user_instances
```
the output will be like this:

fs.inotify.max_user_instances = 128

```bash
sysctl fs.inotify.max_user_watches
```
output:
fs.inotify.max_user_watches = 1048576


Then, increase these values by executing:

```bash
sudo sysctl fs.inotify.max_user_instances=1280
sudo sysctl fs.inotify.max_user_watches=10485760
```

The above setting gets reset to their original values when the machine is rebooted. You can make this change permanent by creating an override file:

```bash
sudo nano /etc/sysctl.d/90-override.conf
```

and add these things

```bash
    fs.inotify.max_user_instances=1280
    fs.inotify.max_user_watches=10485760
```

Now run the command:

```bash
sudo sysctl --system
```

The last command is to load the changes without having to reboot the machine

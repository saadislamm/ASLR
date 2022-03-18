# ASLR
This simple tutorial shows the effect of Address Space Layout Randomization (ASLR).

## Compile and Execute

```
$ make
$ ./virtual_addr
```

### Output 1
```
Virtual address: 0x7ffe4c7c10d4 
```

If you execute the same program again, you will see different virtual addresses:

### Output 2
```
Virtual address: 0x7ffdf47c4be4
```

This may be happening because of the ASLR.

Let's see if it is enabled or disabled using the following command:

```
$ cat /proc/sys/kernel/randomize_va_space
2
```
The value of "2" indicates that the ASLR is enabled[^1].

Now temporarily disable the ASLR by setting this value to "0" using the following command and run the same program twice again:
```
$ sudo sysctl -w kernel.randomize_va_space=0
```

### Output 1 (ASLR Disabled)
```
Virtual address: 0x7fffffffde74
```
### Output 2 (ASLR Disabled)
```
Virtual address: 0x7fffffffde74
```

Here, with ASLR disabled, we see that the virtual address remain the same on each execution.

Enabling back the ASLR using the following command:
```
$ sudo sysctl -w kernel.randomize_va_space=2
```

## gdb
Here, we will see if the ASLR is enabled or disabled by default in gdb debugger.

Execute the same program inside the debugger:
```
$ gdb ./virtual_addr
(gdb) run
```

### Output 1 (gdb)
```
Virtual address: 0x7fffffffdd94
```

### Output 2 (gdb)
```
Virtual address: 0x7fffffffdd94 
```

From the outputs, it looks like the ASLR is disabled by default in gdb.

We can confirm this by running the following command inside the gdb:

```
(gdb) show disable-randomization 
Disabling randomization of debuggee's virtual address space is on.
```
and it confirms that the ASLR is disabled.

However, we can turn it on using the following command[^2] inside gdb and see the outputs again:
```
(gdb) set disable-randomization off
```
### Output 1 (gdb - ASLR Disabled)
```
Virtual address: 0x7fff79925d64
```

### Output 2 (gdb - ASLR Disabled)
```
Virtual address: 0x7ffdcdd05504
```

[^1]: https://docs.oracle.com/cd/E37670_01/E36387/html/ol_aslr_sec.html
[^2]: https://visualgdb.com/gdbreference/commands/set_disable-randomization

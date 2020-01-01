linux/config.h
===========
Problem: Not found
Solution: Remove include

asm/system.h
============
Problem: Not found
Solution: Remove include

copy_from_user \ copy_to_user
==============================
Replace #include <asm/uaccess.h>
With #include <linux/uaccess.h>

ioctl
=====
Replace .ioctl with .unlocked_ioctl

Change ioctl signature: remove inode, change return type to long

https://lwn.net/Articles/119652/


Semaphore
=========
Problem: init_MUTEX undefined (or up or down or down_interruptible) 
Solution:
Replace 'structure semaphore' with 'structure mutex'
Replace init_MUTEX() with mutex_init()
Replace up() with mutex_unlock()
Replace down() with mutex_lock()
Replace down_interruptible() with mutex_lock_interruptible()

https://stackoverflow.com/questions/27801529/where-has-init-mutex-gone-in-linux-kernel-version-3-2


signal_pending
==============
Add #include <linux/sched/signal.h>

SPIN_LOCK_UNLOCKED
==================
Problem: SPIN_LOCK_UNLOCKED undefined
Replace with macro DEFINE_SPINLOCK(spinlock_name)
https://stackoverflow.com/questions/36413864/what-is-the-linux-3-16-equivalent-of-spin-lock-unlocked

task_struct.uid
===============
Add 
const struct cred *cred = current_cred();
Then cred->uid
From <https://stackoverflow.com/questions/39229639/how-to-get-current-processs-uid-and-euid-in-linux-kernel-4-2> 
Result: error: invalid operands to binary != (have ‘uid_t {aka unsigned int}’ and ‘kuid_t {aka const struct <anonymous>}’)
Replace: uid_t var_name with kuid_t struct_var_name = { .val = 0 };
Replace comparison with uid_eq(struct_var_name, sec)
Replace: assignments with .val assignments (struct_var_name.val = cred->uid.val); //Should I use builtin function like: make_kuid https://lwn.net/Articles/491310/?

tty
===
error: dereferencing pointer to incomplete type ‘struct signal_struct’ 
...current->signal->tty
Replace with tty = get_current_tty()
https://stackoverflow.com/questions/20719713/how-can-i-write-to-tty-from-a-kernel-module


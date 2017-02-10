# Microcorruption Whitehorse Write-up

An initial survey of the important functions -

* *INT* Runs the interrupt of value pointed to by the stack pointer. Arguments are found just past on the stack.
* *CONDITIONAL_UNLOCK_DOOR* Basically calls *INT* with 0x7e in sp and r15 in sp+2. The INT 0x7e, according to the manual, triggers the deadbolt to unlock if the password is correct. It takes one argument, the password.
* *GETSN* Reads r14 bytes into address r15.

Finally,
* *LOGIN* prints the necessary info, and says we can only enter a password between 8 and 16 characters. However, it reads 48 bytes into sp before calling *UNLOCK*. That's never a good idea. If we try overflowing input, we see that after it finishes with everything, it's return address is located at our 17th byte of input. Then we've got 31 arbitrary bytes of our own that we can execute. What all can we do with these? 

The manual has an interesting alternative interrupt, that unlocks the deadbolt without taking a password, 0x7f. If we pass this value into *INT*, we should be good to go. 

```
push	#0x7f
call	#0x4532
jmp	$+0x0
```

This is what we want to execute, which the assembler says is 30127f00b0123245ff3f. Our input looks like (16 bytes of trash)(address to our exploit - which is the next word)(exploit), which is **FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF8a3e30127f00b0123245ff3f**.




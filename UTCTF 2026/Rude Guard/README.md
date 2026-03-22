# Rude Guard

There's a guard that's protecting the flag! How do I sneak past him?

*By Joel (@jayscx on discord)*

---

## Solution

**1.** Viewing assembly:
```bash
$ r2 -w pwnable
[0x00401080]> aaa ; afl
```

<pre>
[x] Analyze all flags starting with sym. and entry0 (aa)
[x] Analyze function calls (aac)
[x] Analyze len bytes of instructions for references (aar)
[x] Finding and parsing C++ vtables (avrr)
[x] Type matching analysis for all functions (aaft)
[x] Propagate noreturn information (aanr)
[x] Use -AA or aaaa to perform additional experimental analysis.
0x00401080    1 38           entry0
0x004010c0    4 33   -> 31   sym.deregister_tm_clones
0x004010f0    4 49           sym.register_tm_clones
0x00401130    3 33   -> 32   sym.__do_global_dtors_aux
0x00401160    1 6            entry.init0
0x0040124f    4 140          sym.secret_function
0x004012dc    1 13           sym._fini
0x004010b0    1 5            sym._dl_relocate_static_pie
0x00401166    6 135          main
0x00401000    3 27           sym._init
0x004011ed    4 98           sym.read_input
0x00401030    1 6            sym.imp.putchar
0x00401040    1 6            sym.imp.puts
0x00401050    1 6            sym.imp.read
0x00401060    1 6            sym.imp.strcmp
0x00401070    1 6            sym.imp.atoi
</pre>

**2.** There is `sym.secret_function` which is being called nowhere in main:

<pre>
0x004011b0      7416           je 0x4011c8
|      ||   0x004011b2      488d056f0e00.  lea rax, str.Hi._Go_away.   ; 0x402028 ; "Hi. Go away."
|      ||   0x004011b9      4889c7         mov rdi, rax                ; const char *s
|      ||   0x004011bc      e87ffeffff     call sym.imp.puts           ; int puts(const char *s)
|      ||   0x004011c1      b800000000     mov eax, 0
|     ,===< 0x004011c6      eb23           jmp 0x4011eb
|     |||   ; CODE XREF from main @ 0x4011b0
|     ||`-> 0x004011c8      488d05660e00.  lea rax, str.Hi._What_do_you_want. ; 0x402035 ; "Hi. What do you want."
|     ||    0x004011cf      4889c7         mov rdi, rax                ; const char *s
|     ||    0x004011d2      e869feffff     call sym.imp.puts           ; int puts(const char *s)
|     ||    0x004011d7      8b45fc         mov eax, dword [var_4h]
|     ||    0x004011da      89c7           mov edi, eax                ; int64_t arg1
|     ||    0x004011dc      b800000000     mov eax, 0
|     ||    0x004011e1      e807000000     call sym.read_input
|     ||    0x004011e6      b800000000     mov eax, 0
|     ||    ; CODE XREFS from main @ 0x40118f, 0x4011c6
|     ``--> 0x004011eb      c9             leave
\           0x004011ec      c3             ret
</pre>

**3.** Modify main:
```
[0x00401166]> s 0x004011b0 ; wa jne 0x4011c8 ; s 0x004011e1 ; wa call sym.secret_function
```

**4.** Now simply run with `argc > 1`:
```bash
$ ./pwnable 1
Hi. What do you want.
utflag{gu4rd_w4s_w34ker_th4n_i_th0ught}
```

# GABARITO (privado, NAO versionar no repo publico)

Para cada exercicio: o que fazer, o comando pronto, a entrada pronta e a saida
pronta. Rode no terminal do pwn.college (usuario hacker).

---

## Exercicio 1 - log-firewall

O que fazer: escrever as 5 linhas de resposta e rodar o check.

Comando pronto:
```bash
cat > ~/resposta.txt <<'EOF'
192.168.10.45 INTERNO
192.168.11.5 EXTERNO
10.0.0.15 EXTERNO
192.168.10.254 INTERNO
11000000.10101000.00001011.00000101
EOF
/challenge/check
```

Saida esperada: `Correto! Flag:` seguido da flag.

---

## Exercicio 2 - gdb-registradores

O que fazer: montar o binario, abrir no gdb, parar no `nop`, ler RAX, RDX e [rsp].

Montar e ligar:
```bash
cat > reg.asm <<'EOF'
global _start
section .text
_start:
    mov   rax, 0x1122334455667788
    push  rax
    mov   rax, 0x2A
    mov   rdx, 0x3C7
    nop
    pop   rax
    mov   rax, 0x3C
    xor   rdi, rdi
    syscall
EOF
nasm -f elf64 reg.asm -o reg.o
ld reg.o -o reg
```

Comandos no gdb:
```bash
gdb ./reg
```
```
starti
stepi 3
info registers rax rdx
x/gx $rsp
quit
```

Saida do gdb (valores): rax = 0x2a, rdx = 0x3c7, [rsp] = 0x1122334455667788

Resposta e check:
```bash
cat > ~/resposta.txt <<'EOF'
0x2A
0x3C7
0x1122334455667788
EOF
/challenge/check
```

---

## Exercicio 3 - gdb-rastreio

O que fazer: montar o binario e seguir o RAX em 3 pontos (antes do call, depois
do ret, depois do add).

Montar e ligar:
```bash
cat > rastreio.asm <<'EOF'
global _start
section .text
_start:
    xor   rax, rax
    mov   rdi, 0x14
    call  dobro
    add   rax, 0x7
    mov   rax, 0x3C
    xor   rdi, rdi
    syscall
dobro:
    mov   rax, rdi
    shl   rax, 1
    ret
EOF
nasm -f elf64 rastreio.asm -o rastreio.o
ld rastreio.o -o rastreio
```

Comandos no gdb:
```bash
gdb ./rastreio
```
```
starti
stepi 2
info registers rax
nexti
info registers rax
nexti
info registers rax
quit
```

Saida do gdb (valores): 0x0 (antes do call), 0x28 (depois do ret), 0x2f (depois do add)

Resposta e check:
```bash
cat > ~/resposta.txt <<'EOF'
0x0
0x28
0x2F
EOF
/challenge/check
```

---

## Exercicio 4 - overflow-pilha

O que fazer: escrever as 3 linhas de resposta e rodar o check.

Comando pronto:
```bash
cat > ~/resposta.txt <<'EOF'
56
0x38
76 11 40 00 00 00 00 00
EOF
/challenge/check
```

Saida esperada: `Correto! Flag:` seguido da flag.

---

## Exercicio 5 - format-string

Comando pronto:
```bash
cat > ~/resposta.txt <<'EOF'
5
6
0x7FFFF7A1D2E0
EOF
/challenge/check
```

Saida esperada: `Correto! Flag:` seguido da flag.

---

## Exercicio 6 - inteiro-estouro

Comando pronto:
```bash
cat > ~/resposta.txt <<'EOF'
-1
4294967295
4
EOF
/challenge/check
```

Saida esperada: `Correto! Flag:` seguido da flag.

---

## Exercicio 7 - cadastro32

O que fazer: escrever o programa em NASM de 32 bits, montar e ligar como 32 bits.

Montar e ligar:
```bash
cat > cadastro32.asm <<'EOF'
section .data
    p1 db "Digite o usuario: "
    p1len equ $-p1
    p2 db "Digite o setor: "
    p2len equ $-p2
    u  db "Usuario: "
    ulen equ $-u
    s  db "Setor: "
    slen equ $-s
section .bss
    buf1 resb 32
    buf2 resb 32
    len1 resd 1
    len2 resd 1
section .text
global _start
_start:
    mov eax, 4
    mov ebx, 1
    mov ecx, p1
    mov edx, p1len
    int 0x80

    mov eax, 3
    mov ebx, 0
    mov ecx, buf1
    mov edx, 32
    int 0x80
    mov [len1], eax

    mov eax, 4
    mov ebx, 1
    mov ecx, p2
    mov edx, p2len
    int 0x80

    mov eax, 3
    mov ebx, 0
    mov ecx, buf2
    mov edx, 32
    int 0x80
    mov [len2], eax

    mov eax, 4
    mov ebx, 1
    mov ecx, u
    mov edx, ulen
    int 0x80

    mov eax, 4
    mov ebx, 1
    mov ecx, buf1
    mov edx, [len1]
    int 0x80

    mov eax, 4
    mov ebx, 1
    mov ecx, s
    mov edx, slen
    int 0x80

    mov eax, 4
    mov ebx, 1
    mov ecx, buf2
    mov edx, [len2]
    int 0x80

    mov eax, 1
    mov ebx, 0
    int 0x80
EOF
nasm -f elf32 cadastro32.asm -o cadastro32.o
ld -m elf_i386 cadastro32.o -o cadastro32
```

Entrada de teste (usuario e setor):
```bash
printf 'Ana\nSOC\n' | ./cadastro32
```

Saida esperada com essa entrada:
```
Digite o usuario: Digite o setor: Usuario: Ana
Setor: SOC
```

Rodar o check:
```bash
/challenge/check
```

---

## Exercicio 8 - banner64

O que fazer: escrever o programa em NASM x86-64, montar e ligar como 64 bits.

Montar e ligar:
```bash
cat > banner64.asm <<'EOF'
section .data
    msg db "== Servidor FIAP-SEC ==", 10, "Acesso monitorado", 10
    msglen equ $-msg
section .text
global _start
_start:
    mov rax, 1
    mov rdi, 1
    mov rsi, msg
    mov rdx, msglen
    syscall

    mov rax, 60
    mov rdi, 0
    syscall
EOF
nasm -f elf64 banner64.asm -o banner64.o
ld banner64.o -o banner64
```

Teste:
```bash
./banner64
```

Saida esperada:
```
== Servidor FIAP-SEC ==
Acesso monitorado
```

Rodar o check:
```bash
/challenge/check
```

---

## Exercicio 9 - off-by-one

Comando pronto:
```bash
cat > ~/resposta.txt <<'EOF'
65
0x00
0x00007FFFFFFFE300
EOF
/challenge/check
```

Saida esperada: `Correto! Flag:` seguido da flag.

---

## Exercicio 10 - syscall-shellcode

Comando pronto:
```bash
cat > ~/resposta.txt <<'EOF'
59
0x3B
RAX
RDI
EOF
/challenge/check
```

Saida esperada: `Correto! Flag:` seguido da flag.

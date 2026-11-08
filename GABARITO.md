# GABARITO (privado, NAO versionar no repo publico)

Para cada exercicio: o raciocinio (como o aluno chega em cada valor), o comando
pronto, a entrada pronta e a saida pronta. Rode no terminal do pwn.college
(usuario hacker).

---

## Exercicio 1 - log-firewall

Como chegar:
Cada IP do log tem 4 bytes em hex, 1 byte por octeto. Converta cada par hex em
decimal. A rede interna 192.168.10.0/24 fixa os 3 primeiros octetos em
192.168.10; o 4o octeto e livre. Quem nao tiver exatamente 192.168.10 nos tres
primeiros octetos esta fora.

- C0 A8 0A 2D = 192.168.10.45  -> 192.168.10 bate -> INTERNO
- C0 A8 0B 05 = 192.168.11.5   -> terceiro octeto 11, nao 10 -> EXTERNO (a armadilha: comeca com 192.168 mas nao e a rede .10)
- 0A 00 00 0F = 10.0.0.15       -> EXTERNO
- C0 A8 0A FE = 192.168.10.254 -> INTERNO

Linha 5: o unico 192.168.x.x que ficou EXTERNO e o 192.168.11.5. Em binario,
cada octeto com 8 bits:
192 = 11000000, 168 = 10101000, 11 = 00001011, 5 = 00000101.

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

Como chegar:
Siga as instrucoes ate o `nop`. `mov rax, 0x1122334455667788` carrega esse valor
em RAX; `push rax` coloca esse valor de 8 bytes no topo da pilha, entao [rsp]
passa a conter 0x1122334455667788; `mov rax, 0x2A` sobrescreve RAX com 0x2A;
`mov rdx, 0x3C7` poe 0x3C7 em RDX. No `nop`, portanto: RAX = 0x2A, RDX = 0x3C7,
[rsp] = 0x1122334455667788. O gdb so confirma isso.

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

No gdb (`starti` para no 1o opcode; sao 4 instrucoes ate o nop, entao `stepi 4`):
```bash
gdb ./reg
```
```
starti
stepi 4
info registers rax rdx
x/gx $rsp
quit
```

Saida do gdb: rax = 0x2a, rdx = 0x3c7, [rsp] = 0x1122334455667788

Resposta e check (hex em MAIUSCULAS):
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

Como chegar:
`xor rax, rax` zera RAX; `mov rdi, 0x14` poe 20 em RDI. A funcao `dobro` faz
`mov rax, rdi` (RAX = 20) e `shl rax, 1` (desloca 1 bit a esquerda = multiplica
por 2 = 40 = 0x28) e retorna. Logo:
- Ponto (A), antes do `call`: RAX ainda esta 0 -> 0x0.
- Ponto (B), logo depois do `ret`: RAX = 0x28.
- Ponto (C), depois do `add rax, 0x7`: 0x28 + 0x7 = 0x2F.

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

No gdb (`stepi 2` para no `call`; `nexti` passa por cima da chamada sem entrar):
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

Saida do gdb: 0x0 (antes do call), 0x28 (depois do ret), 0x2f (depois do add)

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

Como chegar:
Olhe o prologo da funcao na desmontagem:
```
push rbp            ; empilha o RBP salvo
mov  rbp, rsp
sub  rsp, 0x40
lea  rax, [rbp-0x30] ; buf comeca em rbp-0x30
```
Na pilha, de baixo para cima:
```
rbp-0x30  -> inicio de buf
...
rbp+0x00  -> RBP salvo (8 bytes)
rbp+0x08  -> endereco de retorno salvo (RIP)
```
Distancia de buf ate o RBP salvo = 0x30 bytes. Do RBP salvo ate o endereco de
retorno = mais 8 bytes. Logo, de buf ate o endereco de retorno:
0x30 + 0x8 = 0x38 = 56 em decimal.
- Linha 1: 56
- Linha 2: 0x38 (56 em hex)
- Linha 3: o endereco de win e 0x401176. Na memoria ele aparece com os bytes na
  ordem invertida (byte mais baixo primeiro) e completado ate 8 bytes:
  0x401176 tem os bytes 40 11 76; invertendo e completando com zeros:
  76 11 40 00 00 00 00 00.

Dica pratica: da para confirmar os 56 no gdb com um padrao ciclico
(`cyclic 100` na entrada, ver qual trecho caiu no RIP), mas pela desmontagem ja
sai direto.

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

Como chegar:
Na ABI System V x86-64, a `printf` recebe a string de formato em RDI. Os
argumentos que os `%p` consomem usam, nesta ordem: RSI, RDX, RCX, R8, R9 (5
registradores) e, a partir do 6o, a pilha. Mapeando os `%N$p`:
```
%1$p -> RSI
%2$p -> RDX
%3$p -> RCX
%4$p -> R8
%5$p -> R9
%6$p -> primeiro valor da pilha
```
- Linha 1: 5 (argumentos variadicos em registradores).
- Linha 2: 6 (o primeiro `%N$p` que le da pilha).
- Linha 3: o valor na posicao 6 e o primeiro da pilha, que no quadro do enunciado
  esta em +0x00 = 0x00007FFFF7A1D2E0. Em hex MAIUSCULO e sem zeros a esquerda:
  0x7FFFF7A1D2E0.

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

Como chegar:
A checagem `if (tam <= 64)` usa `int` (com sinal). Qualquer numero negativo e
menor que 64, entao passa na checagem. O maior negativo (mais perto de zero) e
-1.
No `memcpy(dst, origem, tam)`, o `tam` e convertido para `size_t` (sem sinal, 64
bits). -1 vira um numero gigantesco, causando uma copia enorme.
- Linha 1: -1 (o maior negativo que passa).
- Linha 2: -1 reinterpretado como unsigned de 32 bits = 4294967295
  (ou seja, 2^32 - 1).
- Linha 3: o contador uint8_t comeca em 250 e soma 10 = 260; como uint8_t so vai
  ate 255, da a volta: 260 - 256 = 4.

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

Como chegar:
O programa precisa, em 32 bits (int 0x80):
1. escrever o prompt do usuario (sys_write, eax=4);
2. ler a entrada (sys_read, eax=3); a leitura devolve em EAX quantos bytes leu,
   entao esse numero precisa ser guardado ANTES da proxima syscall, que vai
   sobrescrever EAX;
3. mesmo para o setor;
4. escrever "Usuario: " e, em seguida, exatamente os bytes lidos (incluindo o
   Enter que o usuario digitou, que e o que quebra a linha);
5. escrever "Setor: " e os bytes lidos;
6. encerrar com sys_exit (eax=1).
Por isso o programa salva [len1] e [len2]: sao os tamanhos devolvidos por cada
read.

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

Entrada de teste (usuario e setor, um por linha):
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

Como chegar:
Em x86-64 os numeros e registradores de syscall mudam em relacao ao de 32 bits:
sys_write = 1 (nao 4), sys_exit = 60 (nao 1), e a instrucao e `syscall` (nao
int 0x80). Os argumentos vao em RDI, RSI, RDX. Basta um unico write com a
mensagem de 2 linhas (cada linha terminada por 10 = \n) e depois exit 0.

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

Como chegar:
O laco `for (i = 0; i <= 64; i++)` roda com i = 0, 1, ..., 64, ou seja, 65
vezes, escrevendo de buf[0] ate buf[64]. Mas buf tem so 64 posicoes (0 a 63),
entao buf[64] escreve 1 byte alem do fim.
A entrada e "A" repetida terminada por um byte NUL (0x00). Esse NUL e o byte que
cai na posicao extra (buf[64]), logo acima do fim do buffer.
Logo acima de buf esta o RBP salvo. O byte extra sobrescreve o byte mais baixo
do RBP salvo com 0x00. O RBP salvo valia 0x00007FFFFFFFE3F0; trocando o byte
mais baixo (F0) por 00, fica 0x00007FFFFFFFE300.
- Linha 1: 65 (bytes escritos pelo laco).
- Linha 2: 0x00 (o byte que vaza, o NUL).
- Linha 3: 0x00007FFFFFFFE300 (RBP salvo apos o NUL).

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

Como chegar:
Convencao de syscalls do Linux x86-64:
- o NUMERO da syscall vai em RAX;
- os argumentos vao, nesta ordem, em RDI, RSI, RDX, R10, R8, R9;
- a instrucao `syscall` dispara a chamada.
A syscall execve tem numero 59 (decimal). 59 em hexadecimal = 0x3B. O primeiro
argumento de execve (path) vai no primeiro registrador de argumento, RDI.
- Linha 1: 59
- Linha 2: 0x3B
- Linha 3: RAX (registrador do numero da syscall)
- Linha 4: RDI (registrador do 1o argumento)

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

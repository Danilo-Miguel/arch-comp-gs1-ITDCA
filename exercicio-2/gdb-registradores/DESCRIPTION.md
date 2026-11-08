<h2 align="center">Exercício 2: GDB, lendo registradores</h2>

> **Atenção: você só pode errar UMA vez.** No segundo erro o exercício fica
> **invalidado** e a flag não é mais liberada. Arquivo faltando ou fora do
> formato não conta como tentativa, mas resposta errada conta.

## Cenário

O binário de 64 bits `/challenge/registradores` prepara alguns registradores e
só então faz uma syscall. O código-fonte (NASM, x86-64) é exatamente este:

```nasm
global _start
section .text
_start:
    mov   rax, 0x1122334455667788
    push  rax                     ; empilha o valor de 64 bits
    mov   rax, 0x2A
    mov   rdx, 0x3C7
    nop                           ; <-- pare AQUI
    pop   rax
    mov   rax, 0x3C
    xor   rdi, rdi
    syscall
```

## Sua missão

Abra o programa no `gdb`, deixe a execução parar **exatamente no `nop`** e
inspecione o estado da CPU naquele instante. Descubra como colocar o
breakpoint certo, como ler os registradores e como examinar a memória apontada
por `rsp`. Nada disso está escrito aqui: faz parte do exercício.

Responda com três valores **no instante do `nop`**:

1. o conteúdo de `RAX`;
2. o conteúdo de `RDX`;
3. os 8 bytes no topo da pilha, isto é, o *qword* em `[rsp]`.

## Formato da resposta

Arquivo `/home/hacker/resposta.txt`, com **3 linhas**. Todo valor em
hexadecimal, com prefixo `0x`, **dígitos maiúsculos** e **sem zeros à
esquerda** (exceto o item 3, que tem exatamente 16 dígitos):

| Linha | Conteúdo |
|---|---|
| 1 | `RAX` no `nop`, em hex (`0x…`) |
| 2 | `RDX` no `nop`, em hex (`0x…`) |
| 3 | o *qword* em `[rsp]` no `nop`, em hex com **16 dígitos** (`0x` + 16 dígitos) |

Exemplo de formato, com valores fictícios:

```
0x7B
0x1F4
0x00000000DEADBEEF
```

## Como conferir

Quando tiver certeza, rode `/challenge/check`.

<h2 align="center">Exercício 3: GDB — rastreando um registrador</h2>

> **Atenção: você só pode errar UMA vez.** No segundo erro o exercício fica
> **invalidado** e a flag não é mais liberada. Arquivo faltando ou fora do
> formato não conta como tentativa, mas resposta errada conta.

## Cenário

Ler um registrador uma vez é fácil. Agora o desafio é **acompanhar como ele
muda ao longo da execução**. O binário de 64 bits `/challenge/rastreio` zera o
`RAX`, chama uma função e depois mexe no resultado. O código-fonte (NASM,
x86-64) é exatamente este:

```nasm
global _start
section .text
_start:
    xor   rax, rax        ; RAX comeca zerado
    mov   rdi, 0x14        ; argumento = 20
    call  dobro            ; (A) imediatamente ANTES desta chamada
    add   rax, 0x7         ; (B) imediatamente DEPOIS do 'ret' desta chamada
    mov   rax, 0x3C        ; (C) depois que o 'add' acima terminou
    xor   rdi, rdi
    syscall

dobro:
    mov   rax, rdi
    shl   rax, 1           ; RAX = rdi * 2
    ret
```

## Sua missão

Use o `gdb` para seguir o valor de `RAX` em três momentos distintos, marcados
no código como `(A)`, `(B)` e `(C)`:

- **(A)** imediatamente **antes** da instrução `call dobro`;
- **(B)** imediatamente **depois** de a função retornar (logo após o `ret`),
  **antes** do `add`;
- **(C)** logo **depois** da próxima instrução que altera `RAX` (o `add`).

Descobrir onde parar em cada ponto — e como avançar a execução instrução por
instrução sem pular a chamada — faz parte do exercício. O mesmo registrador,
três valores: mostre que você entendeu por que cada um é o que é.

## Formato da resposta

Arquivo `/home/hacker/resposta.txt`, com **3 linhas**, cada uma em hexadecimal,
com `0x`, **dígitos maiúsculos** e **sem zeros à esquerda**:

| Linha | Conteúdo |
|---|---|
| 1 | `RAX` no ponto `(A)` |
| 2 | `RAX` no ponto `(B)` |
| 3 | `RAX` no ponto `(C)` |

Exemplo de formato, com valores fictícios:

```
0x0
0x63
0x9
```

## Como conferir

Quando tiver certeza, rode `/challenge/check`.

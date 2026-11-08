<h2 align="center">Exercício 6: Controlar o RIP (sobrescrever o retorno)</h2>

> **Atenção: você só pode errar UMA vez.** No segundo erro o exercício fica
> **invalidado** e a flag não é mais liberada. Arquivo faltando ou fora do
> formato não conta como tentativa, mas resposta errada conta.

## Cenário

O serviço `/challenge/ret2win` (x86-64) transborda um buffer e tem uma função
`win` que nunca é chamada pelo fluxo normal:

```asm
vuln:
    push rbp
    mov  rbp, rsp
    sub  rsp, 0x40
    lea  rax, [rbp-0x30]    ; buf
    mov  rdi, rax
    call gets
    leave
    ret

0000000000401205 <win>:
```

## Guia (o que acontece)

Até aqui você só viu o `RIP` virar lixo controlado ('BBBB...'). Agora o objetivo
é fazer o `RIP` apontar para um lugar útil: a função `win`. Para isso, encha de
`buf` até o endereço de retorno e escreva, por cima do retorno, o endereço de
`win` em little-endian. Depois do `ret`, o `RIP` passa a ser o endereço de `win`.
Confirme isso no GDB.

## Sua missão

Injete e **mostre no GDB** que, depois do `ret`, o `RIP` virou o endereço de
`win` (o mostrar é com você). Responda:

1. o enchimento, em bytes, até o endereço de retorno;
2. o endereço de `win` escrito em little-endian (8 bytes);
3. o valor do `RIP` depois do `ret`.

## Formato da resposta

Arquivo `/home/hacker/resposta.txt`, com **3 linhas**:

| Linha | Conteúdo |
|---|---|
| 1 | enchimento até o retorno (**decimal**) |
| 2 | endereço de `win` em little-endian: **8 bytes** hex maiúsculos de 2 dígitos, separados por **um espaço** |
| 3 | valor do `RIP` depois do `ret`, em hex (`0x`, dígitos maiúsculos) |

## Como conferir

Quando tiver certeza, rode `/challenge/check`.

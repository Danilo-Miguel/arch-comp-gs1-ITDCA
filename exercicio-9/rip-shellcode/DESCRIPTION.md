<h2 align="center">Exercício 9: Controlar o RIP (desviar para o shellcode)</h2>

> **Atenção: você só pode errar UMA vez.** No segundo erro o exercício fica
> **invalidado** e a flag não é mais liberada. Arquivo faltando ou fora do
> formato não conta como tentativa, mas resposta errada conta.

## Cenário

O serviço `/challenge/jmpbuf` (x86-64) tem o buffer em memória **executável** e,
ao iniciar, imprime o endereço do buffer:

```
buf @ 0x7fffffffe2a0
```

Na desmontagem, `buf[64]` começa em `rbp-0x40`.

## Guia (o que acontece)

Agora você não desvia para uma função pronta: você coloca o **seu shellcode** no
começo do `buf` e faz o `RIP` apontar para ele. Encha de `buf` até o endereço de
retorno e escreva, por cima do retorno, o endereço do próprio `buf` (que o
programa vazou) em little-endian. Depois do `ret`, o `RIP` cai dentro do `buf`,
no início do seu shellcode. Confirme no GDB que o `RIP` está dentro do `buf`.

## Sua missão

Injete o shellcode, desvie o `RIP` para ele e **mostre no GDB** que o `RIP` caiu
no início do `buf` (o mostrar é com você). Responda:

1. o enchimento, em bytes, até o endereço de retorno;
2. o endereço que você coloca no retorno para cair no início do shellcode;
3. esse mesmo endereço em little-endian (8 bytes).

## Formato da resposta

Arquivo `/home/hacker/resposta.txt`, com **3 linhas**:

| Linha | Conteúdo |
|---|---|
| 1 | enchimento até o retorno (**decimal**) |
| 2 | endereço colocado no retorno, em hex (`0x`, dígitos maiúsculos) |
| 3 | esse endereço em little-endian: **8 bytes** hex maiúsculos de 2 dígitos, separados por **um espaço** |

## Como conferir

Quando tiver certeza, rode `/challenge/check`.

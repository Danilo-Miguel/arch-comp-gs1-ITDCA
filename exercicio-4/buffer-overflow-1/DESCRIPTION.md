<h2 align="center">Exercício 4: Buffer overflow 1</h2>

> **Atenção: você só pode errar UMA vez.** No segundo erro o exercício fica
> **invalidado** e a flag não é mais liberada. Arquivo faltando ou fora do
> formato não conta como tentativa, mas resposta errada conta.

## Cenário

O serviço `/challenge/overflow1` (x86-64) lê a entrada sem checar o tamanho:

```c
char buf[32];
read(0, buf, 200);      // le muito mais do que buf comporta
```

Na desmontagem, `buf` começa em `rbp-0x20`.

## Guia (o que acontece)

Quando você manda mais bytes do que `buf` comporta, o excesso continua
escrevendo na pilha, por cima do `RBP` salvo e, logo depois, por cima do
endereço de retorno salvo. Se você encher até o retorno e colocar 8 bytes ali,
esses 8 bytes viram o novo `RIP` na hora do `ret`. Pare no `ret` no GDB e observe.

## Sua missão

Monte a entrada, injete e **mostre no GDB** que o `RIP` foi sobrescrito. O
"mostrar" (a evidência no GDB) é com você. Para registrar a resposta, envie
**40 bytes `0x41` ('A')** seguidos de **8 bytes `0x42` ('B')** e veja o `RIP`
no momento do `ret`. Responda:

1. o tamanho de `buf`, em bytes;
2. o enchimento, em bytes, até o endereço de retorno;
3. o valor que o `RIP` assume no `ret` com essa entrada.

## Formato da resposta

Arquivo `/home/hacker/resposta.txt`, com **3 linhas**:

| Linha | Conteúdo |
|---|---|
| 1 | tamanho de `buf` (**decimal**) |
| 2 | enchimento até o endereço de retorno (**decimal**) |
| 3 | valor do `RIP` no `ret`, em hex (`0x`, dígitos maiúsculos) |

## Como conferir

Quando tiver certeza, rode `/challenge/check`.

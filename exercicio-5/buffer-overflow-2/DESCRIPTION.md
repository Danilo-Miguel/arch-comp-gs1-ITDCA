<h2 align="center">Exercício 5: Buffer overflow 2</h2>

> **Atenção: você só pode errar UMA vez.** No segundo erro o exercício fica
> **invalidado** e a flag não é mais liberada. Arquivo faltando ou fora do
> formato não conta como tentativa, mas resposta errada conta.

## Cenário

O serviço `/challenge/overflow2` (x86-64) usa um buffer maior e uma leitura sem
limite:

```c
char buf[128];
gets(buf);              // sem limite de tamanho
```

Na desmontagem, `buf` começa em `rbp-0x80`.

## Guia (o que acontece)

É o mesmo princípio do exercício anterior, mas agora o buffer é maior, então o
enchimento até o endereço de retorno é outro. Encha de `buf` até o retorno e
coloque 8 bytes por cima do retorno: eles viram o `RIP` no `ret`. Confirme no GDB.

## Sua missão

Injete e **mostre no GDB** o `RIP` sobrescrito (o mostrar é com você). Para
registrar a resposta, envie **136 bytes `0x41` ('A')** seguidos de **8 bytes
`0x43` ('C')** e observe o `RIP` no `ret`. Responda:

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

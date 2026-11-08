<h2 align="center">Exercício 9: Erro de um byte</h2>

> **Atenção: você só pode errar UMA vez.** No segundo erro o exercício fica
> **invalidado** e a flag não é mais liberada. Arquivo faltando ou fora do
> formato não conta como tentativa, mas resposta errada conta.

## Cenário

O serviço `/challenge/offbyone` (x86-64) copia a entrada do usuário para um
buffer com um laço que erra a condição de parada por **um**:

```c
char buf[64];                   // buf fica logo abaixo do RBP salvo
for (int i = 0; i <= 64; i++)   // repare: <= , nao <
    buf[i] = entrada[i];        // entrada termina com um byte NUL (0x00)
```

Na pilha, imediatamente **acima** de `buf[63]`, está o `RBP` salvo. No momento
da cópia, o `RBP` salvo vale:

```
0x00007FFFFFFFE3F0
```

A entrada do usuário é a string `"A"` repetida, terminada pelo byte NUL que o
`read`/`strcpy` coloca no fim.

## Sua missão

O laço escreve um byte a mais do que `buf` comporta. Esse byte extra cai sobre
o **byte menos significativo** do `RBP` salvo. Pense em quantos bytes o laço
realmente escreve, qual byte vaza para fora de `buf` e como ele altera o `RBP`
salvo — lembrando que x86-64 é **little-endian**. **Não escreva exploit
nenhum**: mostre que entende o layout e a ordem dos bytes. Responda:

1. quantos bytes, no total, o laço escreve;
2. o valor do **único** byte que extrapola `buf` e cai sobre o `RBP` salvo;
3. o novo valor do `RBP` salvo depois que esse byte o atinge.

## Formato da resposta

Arquivo `/home/hacker/resposta.txt`, com **3 linhas**:

| Linha | Conteúdo |
|---|---|
| 1 | número total de bytes escritos pelo laço (**decimal**) |
| 2 | o byte que vaza, em hex de **2 dígitos** (`0x`, maiúsculo) |
| 3 | o novo valor do `RBP` salvo, em hex (`0x`, dígitos maiúsculos) |

Exemplo de formato, com valores fictícios:

```
40
0x00
0x00007FFFFFFFEA00
```

## Como conferir

Quando tiver certeza, rode `/challenge/check`.

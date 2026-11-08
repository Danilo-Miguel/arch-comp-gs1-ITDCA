<h2 align="center">Exercício 10: Corrupção de variável adjacente</h2>

> **Atenção: você só pode errar UMA vez.** No segundo erro o exercício fica
> **invalidado** e a flag não é mais liberada. Arquivo faltando ou fora do
> formato não conta como tentativa, mas resposta errada conta.

## Cenário

O serviço `/challenge/adjacente` (x86-64) guarda uma variável `chave` perto do
buffer e só libera o acesso se ela tiver um valor específico:

```c
int  chave = 0;
char buf[24];
read(0, buf, 100);
if (chave == 0x1337) {
    /* acesso liberado */
}
```

Na desmontagem, `buf` começa em `rbp-0x20` e `chave` fica em `rbp-0x08`.

## Guia (o que acontece)

Aqui você não precisa chegar ao endereço de retorno. A variável `chave` fica na
pilha logo **depois** de `buf`, então o transbordo de `buf` alcança e sobrescreve
`chave` antes de chegar ao `RIP`. Encha de `buf` até `chave` e escreva ali o
valor que o `if` exige, em little-endian. No GDB, observe a variável `chave`
mudar de 0 para o valor que você escreveu.

## Sua missão

Transborde `buf`, corrompa a `chave` e **mostre no GDB** que ela mudou para o
valor esperado (o mostrar é com você). Responda:

1. o deslocamento, em bytes, do início de `buf` até a variável `chave`;
2. o valor que `chave` precisa ter;
3. os 4 bytes que você grava em `chave`, em little-endian.

## Formato da resposta

Arquivo `/home/hacker/resposta.txt`, com **3 linhas**:

| Linha | Conteúdo |
|---|---|
| 1 | deslocamento de `buf` até `chave` (**decimal**) |
| 2 | valor exigido de `chave`, em hex (`0x`, dígitos maiúsculos) |
| 3 | os 4 bytes em little-endian: **4 bytes** hex maiúsculos de 2 dígitos, separados por **um espaço** |

## Como conferir

Quando tiver certeza, rode `/challenge/check`.

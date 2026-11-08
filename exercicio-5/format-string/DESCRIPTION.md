<h2 align="center">Exercício 5: String de formato</h2>

> **Atenção: você só pode errar UMA vez.** No segundo erro o exercício fica
> **invalidado** e a flag não é mais liberada. Arquivo faltando ou fora do
> formato não conta como tentativa, mas resposta errada conta.

## Cenário

O serviço `/challenge/formato` (x86-64, ABI System V) recebe uma linha do
usuário e a entrega **diretamente** como string de formato:

```c
char entrada[128];
read(0, entrada, sizeof entrada);
printf(entrada);              // a entrada do usuario vira o formato
```

Como `printf` é variádica, cada especificador `%p` consome o próximo argumento
na ordem definida pela ABI. Com `%N$p` você escolhe diretamente o N-ésimo
argumento.

No momento exato da chamada a `printf`, os argumentos que ficam **na pilha**
(a partir do primeiro que não coube em registrador), do mais baixo para o mais
alto endereço, são:

```
+0x00: 0x00007FFFF7A1D2E0
+0x08: 0x4242424242424242
+0x10: 0x0000000000000001
```

## Sua missão

Lembre que, na ABI System V x86-64, a string de formato vai em `RDI` e os
argumentos variádicos seguintes usam primeiro os registradores e só depois a
pilha. Com base nisso, responda:

1. quantos argumentos variádicos (fora a string de formato) são passados em
   **registradores** antes de a passagem começar a usar a pilha;
2. a posição `N` do **primeiro** `%N$p` que lê um valor **da pilha** (e não de
   um registrador);
3. o valor que esse `%N$p` imprimiria, ou seja, o conteúdo que está na posição
   `N`, conforme o quadro acima.

Mapear os especificadores para registradores e depois para a pilha é o
exercício. Não é preciso rodar nada contra um alvo real.

## Formato da resposta

Arquivo `/home/hacker/resposta.txt`, com **3 linhas**:

| Linha | Conteúdo |
|---|---|
| 1 | número de argumentos variádicos em registradores (**decimal**) |
| 2 | a posição `N` do primeiro `%N$p` que lê da pilha (**decimal**) |
| 3 | o valor lido nessa posição, em hex (`0x`, dígitos maiúsculos, sem zeros à esquerda) |

Exemplo de formato, com valores fictícios:

```
4
5
0xCAFEBABE
```

## Como conferir

Quando tiver certeza, rode `/challenge/check`.

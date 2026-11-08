<h2 align="center">Exercício 6: Estouro de inteiro</h2>

> **Atenção: você só pode errar UMA vez.** No segundo erro o exercício fica
> **invalidado** e a flag não é mais liberada. Arquivo faltando ou fora do
> formato não conta como tentativa, mas resposta errada conta.

## Cenário

O serviço `/challenge/inteiro` valida o tamanho de uma cópia, mas confunde
tipos com e sem sinal:

```c
int  tam;                       // inteiro de 32 bits COM sinal
read(0, &tam, 4);               // 4 bytes, little-endian
char origem[256];
if (tam <= 64) {                // comparacao COM sinal
    read(0, origem, 256);
    memcpy(dst, origem, tam);   // tam vira size_t (SEM sinal) aqui
}
```

Em outra parte do código há um contador de pacotes de 1 byte:

```c
uint8_t contador = 250;
contador += 10;                 // o que acontece aqui?
```

## Sua missão

A checagem `tam <= 64` é feita com sinal, mas `memcpy` interpreta `tam` como
`size_t` sem sinal. Pense em qual valor atravessa a checagem e, mesmo assim,
vira uma cópia gigantesca — e no que acontece com o contador de 1 byte quando
ele passa de 255. **Não é preciso explorar nada**; é preciso mostrar que você
entende complemento de dois e a largura dos tipos. Responda:

1. o **maior** valor de `tam` (o mais próximo de zero) que é **negativo** e
   ainda passa pela checagem `tam <= 64`;
2. esse mesmo padrão de bits reinterpretado como `unsigned int` de 32 bits;
3. o valor final de `contador` depois do `contador += 10`.

## Formato da resposta

Arquivo `/home/hacker/resposta.txt`, com **3 linhas**, todas em **decimal**:

| Linha | Conteúdo |
|---|---|
| 1 | o valor de `tam` com sinal (pode ser negativo) |
| 2 | o mesmo valor como `unsigned int` de 32 bits |
| 3 | o valor de `contador` (0 a 255) após o wrap |

Exemplo de formato, com valores fictícios:

```
-8
4294967288
12
```

## Como conferir

Quando tiver certeza, rode `/challenge/check`.

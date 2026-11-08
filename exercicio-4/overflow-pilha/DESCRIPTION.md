<h2 align="center">Exercício 4: Transbordo de pilha</h2>

> **Atenção: você só pode errar UMA vez.** No segundo erro o exercício fica
> **invalidado** e a flag não é mais liberada. Arquivo faltando ou fora do
> formato não conta como tentativa, mas resposta errada conta.

## Cenário

O serviço `/challenge/overflow` (x86-64, sem proteção de pilha) lê uma linha
para um buffer de tamanho fixo sem checar o tamanho. A função vulnerável, vista
na desmontagem, tem este prólogo e este corpo:

```asm
vuln:
    push rbp
    mov  rbp, rsp
    sub  rsp, 0x40
    lea  rax, [rbp-0x30]     ; buf comeca aqui
    mov  rdi, rax
    call gets                ; le sem limite de tamanho
    leave
    ret
```

Existe também, no mesmo binário, uma função nunca chamada pelo fluxo normal:

```
0000000000401176 <win>:
```

## Sua missão

Entenda o *layout* do quadro de pilha dessa função e o que seria preciso para
desviar a execução para `win`. **Você não precisa escrever nenhum exploit nem
derrubar nada**, precisa demonstrar que entende a memória. Responda:

1. o deslocamento, em bytes, do início de `buf` até o **endereço de retorno
   salvo** na pilha;
2. esse mesmo deslocamento em hexadecimal;
3. o endereço de `win` escrito como ele apareceria na memória, ou seja, em
   **little-endian**, byte a byte (8 bytes de um ponteiro de 64 bits).

Deduzir onde ficam o `RBP` salvo e o endereço de retorno em relação a `buf` é o
cerne do exercício.

## Formato da resposta

Arquivo `/home/hacker/resposta.txt`, com **3 linhas**:

| Linha | Conteúdo |
|---|---|
| 1 | o deslocamento em **decimal** |
| 2 | o mesmo deslocamento em hexadecimal (`0x`, dígitos maiúsculos, sem zeros à esquerda) |
| 3 | o endereço de `win` em little-endian: **8 bytes** em hex maiúsculo de 2 dígitos, separados por **um espaço** |

Exemplo de formato, com valores fictícios:

```
72
0x48
EF BE AD DE 00 00 00 00
```

## Como conferir

Quando tiver certeza, rode `/challenge/check`.

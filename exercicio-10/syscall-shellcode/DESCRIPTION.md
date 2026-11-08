<h2 align="center">Exercício 10: Syscall e injeção de código</h2>

> **Atenção: você só pode errar UMA vez.** No segundo erro o exercício fica
> **invalidado** e a flag não é mais liberada. Arquivo faltando ou fora do
> formato não conta como tentativa, mas resposta errada conta.

## Cenário

O serviço `/challenge/injecao` (x86-64, Linux) lê bytes do usuário para uma
região de memória marcada como **executável** e, em seguida, desvia a execução
para o início dela:

```c
void *buf = mmap(..., PROT_READ | PROT_WRITE | PROT_EXEC...);
read(0, buf, 4096);
((void(*)())buf)();      // executa os bytes recebidos
```

Para entender o que um código injetado precisaria fazer para pedir ao núcleo a
execução de outro programa, você precisa conhecer a **interface de chamadas de
sistema** do x86-64, não escrever código de ataque.

## Sua missão

Este é um exercício de **arquitetura**: a convenção de syscalls do Linux em
x86-64. Sem montar nenhum payload, responda sobre a syscall `execve`, que tem a
assinatura `execve(const char *path, char *const argv[], char *const envp[])`:

1. o **número** da syscall `execve` no Linux x86-64;
2. esse número em **hexadecimal**;
3. o **registrador** que carrega o número da syscall, por convenção, antes da
   instrução `syscall`;
4. o **registrador** que carrega o **primeiro argumento** (`path`) segundo a
   ABI System V x86-64.

Saber de cor (ou saber onde consultar) a tabela de syscalls e a ordem dos
registradores de argumento é o ponto do exercício.

## Formato da resposta

Arquivo `/home/hacker/resposta.txt`, com **4 linhas**:

| Linha | Conteúdo |
|---|---|
| 1 | número da syscall `execve` (**decimal**) |
| 2 | o mesmo número em hexadecimal (`0x`, dígitos maiúsculos, sem zeros à esquerda) |
| 3 | o registrador do número da syscall, em **MAIÚSCULAS** (ex.: `RAX`) |
| 4 | o registrador do 1º argumento (`path`), em **MAIÚSCULAS** |

Exemplo de formato, com valores fictícios:

```
1
0x1
RBX
RSI
```

## Como conferir

Quando tiver certeza, rode `/challenge/check`.

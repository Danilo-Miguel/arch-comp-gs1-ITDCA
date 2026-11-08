<h2 align="center">Exercício 7: Cadastro de acesso (32 bits)</h2>

> **Atenção: você só pode errar UMA vez.** No segundo erro o exercício fica
> **invalidado** e a flag não é mais liberada. Arquivo faltando ou programa não
> montado/ligado não conta como tentativa, mas programa com saída errada conta.
> **Teste o seu programa antes de rodar o `check`.**

## Cenário

O sistema de controle de acesso, de 32 bits, registra o usuário e o setor de quem
entra e depois mostra o que foi registrado.

## Sua missão

Crie `~/cadastro32.asm` em NASM, para x86 de 32 bits, que:

1. escreva `Digite o usuario: `, sem quebra de linha, e leia até 32 bytes;
2. escreva `Digite o setor: `, sem quebra de linha, e leia até 32 bytes;
3. escreva `Usuario: ` e, na mesma linha, o usuário digitado;
4. escreva `Setor: ` e, na mesma linha, o setor digitado;
5. encerre com código de saída 0.

As únicas instruções necessárias são `mov` e `int 0x80`. Consulte a tabela de
syscalls de 32 bits da aula. Cada syscall usa o registrador de retorno de novo:
guarde na memória a quantidade de bytes de cada leitura antes de seguir adiante.

## Formato da resposta

Monte e ligue o programa você mesmo. No seu home devem existir:

| Arquivo | O que é |
|---|---|
| `cadastro32.asm` | o código-fonte |
| `cadastro32.o` | o objeto, ELF de 32 bits |
| `cadastro32` | o executável, ELF de 32 bits |

Exemplo no terminal, em que `Ana` e `SOC` foram digitados:

```
Digite o usuario: Ana
Digite o setor: SOC
Usuario: Ana
Setor: SOC
```

A saída é comparada byte a byte com usuários e setores sorteados.

## Como conferir

Quando tiver certeza, rode `/challenge/check`.

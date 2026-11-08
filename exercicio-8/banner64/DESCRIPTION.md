<h2 align="center">Exercício 8: Banner do servidor (64 bits)</h2>

> **Atenção: você só pode errar UMA vez.** No segundo erro o exercício fica
> **invalidado** e a flag não é mais liberada. Arquivo faltando ou programa não
> montado/ligado não conta como tentativa, mas programa com saída errada conta.
> **Teste o seu programa antes de rodar o `check`.**

## Cenário

O servidor de monitoramento foi migrado para 64 bits, e o aviso legal do exercício 5
precisa continuar aparecendo a cada conexão.

## Sua missão

Crie `~/banner64.asm` em NASM, para x86-64, que escreva na tela exatamente as 2 linhas
abaixo, cada uma terminada em quebra de linha, e encerre com código de saída 0.

As únicas instruções necessárias são `mov` e `syscall`. Consulte a tabela de syscalls
de 64 bits da aula: os números e os registradores não são os mesmos de 32 bits.

## Formato da resposta

Monte e ligue o programa você mesmo. No seu home devem existir:

| Arquivo | O que é |
|---|---|
| `banner64.asm` | o código-fonte |
| `banner64.o` | o objeto, ELF de 64 bits |
| `banner64` | o executável, ELF de 64 bits |

Saída esperada, byte a byte, sem acentos:

```
== Servidor FIAP-SEC ==
Acesso monitorado
```

## Como conferir

Quando tiver certeza, rode `/challenge/check`.

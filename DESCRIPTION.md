**GS Computer Architecture** é a avaliação de Arquitetura de Computadores em formato
de dojo pwn.college, com 10 exercícios, cada um em seu **próprio módulo**, para
que a conclusão de cada aluno seja medida um a um.

Os três primeiros blocos retomam o que já foi treinado, **binário/hexadecimal**
e **Assembly x86** (32 e 64 bits), e são idênticos aos do dojo de referência.
Em seguida vêm dois exercícios de **GDB**: ler registradores e, depois,
**rastrear um registrador** ao longo da execução, vendo como o `RAX` muda antes
e depois de uma chamada. Por fim, cinco desafios de **análise de
vulnerabilidades** (transbordo de pilha, string de formato, estouro de inteiro,
erro de um byte e a interface de syscalls), cada um sobre um tipo diferente.

Como é **prova**, os enunciados dizem **o que** entregar e **em que formato**,
mas **não dão a resposta**: descobrir **como**, lendo o código, usando o GDB,
raciocinando sobre memória, ABI e endianness, é o exercício.

**Respostas** vão em `/home/hacker/resposta.txt`: uma por linha, sem linhas
extras e sem espaços sobrando. Hexadecimal sempre em **MAIÚSCULAS**; quando o
formato pede `0x`, o prefixo é obrigatório.

**Regra das tentativas:** em cada exercício você pode errar **uma vez**. No
segundo erro o exercício fica **invalidado** e a flag não é mais liberada.
Arquivo faltando ou resposta fora do formato **não** contam como tentativa.
Confira tudo antes de rodar o `/challenge/check`.

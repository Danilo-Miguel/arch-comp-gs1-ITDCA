# GS Computer Architecture

Dojo do pwn.college para a **GS (Global Solution) de Arquitetura de
Computadores**, definido em [dojo.yml](./dojo.yml), com 10 exercícios. Cada
exercício é um **módulo separado**, com `/challenge/check` e flag próprios, para
que dê para medir, aluno a aluno, **quem fez e quem não fez**.

Diferente do dojo de referência, estes enunciados são de **prova**: dizem o que
entregar e em que formato, mas **não dão a resposta**. Descobrir o *como* é o
exercício.

| Módulo | Desafio | Tema | Origem |
|---|---|---|---|
| `exercicio-1` | `log-firewall` | IPs em hexadecimal e máscara de rede | cópia idêntica (ref. exercício 1) |
| `exercicio-2` | `gdb-registradores` | GDB: ler registradores | novo (nível do dojo *example*) |
| `exercicio-3` | `gdb-rastreio` | GDB: rastrear o `RAX` numa chamada | novo (GDB, mais difícil) |
| `exercicio-4` | `buffer-overflow-1` | Buffer overflow 1: transbordar até o `RIP` | novo (estilo shellcode) |
| `exercicio-5` | `buffer-overflow-2` | Buffer overflow 2: buffer maior, mesmo alvo | novo (estilo shellcode) |
| `exercicio-6` | `controlar-rip` | Sobrescrever o retorno e controlar o `RIP` | novo (estilo shellcode) |
| `exercicio-7` | `cadastro32` | Duas leituras e escrita (Assembly 32 bits) | cópia idêntica (ref. exercício 7) |
| `exercicio-8` | `banner64` | Escrita (Assembly 64 bits) | cópia idêntica (ref. exercício 8) |
| `exercicio-9` | `rip-shellcode` | Controlar o `RIP` e desviar para o shellcode | novo (estilo shellcode) |
| `exercicio-10` | `variavel-adjacente` | Corrupção de variável adjacente | novo (estilo shellcode) |

Os módulos 1, 7 e 8 foram **copiados byte a byte** do dojo de referência
(binário/hexadecimal e Assembly x86). Os módulos 2 e 3 cobrem o uso do **GDB**:
ler registradores e, no 3, acompanhar um registrador ao longo da execução. Os
módulos 4, 5, 6, 9 e 10 são de **corrupção de memória**, no nível e no estilo do
material de *shellcode*, cada um de um tipo: buffer overflow (1 e 2),
sobrescrever o retorno e controlar o `RIP`, desviar o `RIP` para o shellcode
injetado e corromper uma variável adjacente. Em cada um o aluno **injeta ou
transborda e demonstra no GDB o que mudou** (por exemplo, o `RIP` sobrescrito);
o enunciado dá só um guia curto, a demonstração é do aluno.

## Regra das tentativas

Igual ao dojo de referência: cada `check` permite **um erro**. No segundo erro
o exercício fica **invalidado** para aquele aluno e o `check` não libera mais a
flag. O registro fica em `/home/hacker/.tentativas`, um arquivo por exercício,
**assinado** com um segredo derivado do `/flag` (que só o root lê). O dojo usa
`allow_privileged: false` para que o aluno não entre no modo *practice* e apague
o registro. Não contam como tentativa: arquivo ausente, número de linhas errado
ou linha fora do formato. Os checks guardam só o SHA-256 das respostas.

## Gabarito

Os checks já vêm com o **hash da resposta embutido** (como no dojo de
referência; o hash é de mão única e não revela a resposta). O gabarito legível,
com o passo a passo e as respostas de cada exercício, está em
[GABARITO.md](./GABARITO.md), que é **local e não deve ir para um repositório
público**.

Os módulos 2, 3, 4, 5, 6, 9 e 10 assumem que os binários `/challenge/<nome>`
existam na imagem do dojo (compilados a partir das listagens dos enunciados)
para o aluno usar o GDB. O `check` em si compara apenas o `~/resposta.txt`.

## Ambiente

A imagem `pwncollege/challenge-simple` é mínima. As ferramentas (`gdb`, NASM,
`objdump` etc.) vêm do workspace do pwn.college, em `/run/dojo/bin`.

# GS Computer Actuated

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
| `exercicio-4` | `overflow-pilha` | Transbordo de pilha: offset e endianness | novo |
| `exercicio-5` | `format-string` | String de formato: ABI e pilha | novo |
| `exercicio-6` | `inteiro-estouro` | Estouro de inteiro: sinal e largura | novo |
| `exercicio-7` | `cadastro32` | Duas leituras e escrita (Assembly 32 bits) | cópia idêntica (ref. exercício 7) |
| `exercicio-8` | `banner64` | Escrita (Assembly 64 bits) | cópia idêntica (ref. exercício 8) |
| `exercicio-9` | `off-by-one` | Erro de um byte: NUL e little-endian | novo |
| `exercicio-10` | `syscall-shellcode` | Interface de syscalls x86-64 | novo |

Os módulos 1, 7 e 8 foram **copiados byte a byte** do dojo de referência
(binário/hexadecimal e Assembly x86). Os módulos 2 e 3 cobrem o uso do **GDB**,
no espírito do módulo de registradores do dojo *example* — o 3 sobe o nível,
pedindo o acompanhamento de um registrador específico ao longo da execução. Os
cinco restantes variam o **tipo de vulnerabilidade** e ficam no nível de
dificuldade do material de *shellcode*, cada um proposto como desafio.

## Regra das tentativas

Igual ao dojo de referência: cada `check` permite **um erro**. No segundo erro
o exercício fica **invalidado** para aquele aluno e o `check` não libera mais a
flag. O registro fica em `/home/hacker/.tentativas`, um arquivo por exercício,
**assinado** com um segredo derivado do `/flag` (que só o root lê). O dojo usa
`allow_privileged: false` para que o aluno não entre no modo *practice* e apague
o registro. Não contam como tentativa: arquivo ausente, número de linhas errado
ou linha fora do formato. Os checks guardam só o SHA-256 das respostas.

## Gabarito e geração dos hashes (IMPORTANTE antes de publicar)

Este repositório é **público** e, por isso, **não traz o gabarito**. Os checks
dos módulos novos (2, 3, 4, 5, 6, 9, 10) já têm toda a lógica e a validação de
formato, mas o hash esperado está como **marcador**:

```
ESPERADO_SHA256="PREENCHER_COM_O_GERADOR_PRIVADO"
```

Para cada um desses módulos, calcule as respostas corretas a partir do
enunciado (o código de cada desafio é determinístico) e gere o hash **fora deste
repositório**, com:

```sh
printf '%s\n' "<id-do-desafio>" "<linha1>" "<linha2>" ... | sha256sum
```

usando o `id` do desafio (ex.: `gdb-rastreio`) seguido de cada linha da
resposta, na ordem do enunciado. Cole os 64 caracteres hex em
`ESPERADO_SHA256`. Os módulos 1, 7 e 8 já vêm com o hash pronto, pois são cópias
idênticas.

Os desafios de GDB (2 e 3) assumem que os binários `/challenge/registradores` e
`/challenge/rastreio` existam, compilados exatamente a partir das listagens NASM
dos enunciados. Os desafios 4 a 6, 9 e 10 são de **análise**: o aluno responde a
partir das listagens e de raciocínio sobre memória/ABI, sem precisar de um alvo
de verdade.

## Ambiente

A imagem `pwncollege/challenge-simple` é mínima. As ferramentas (`gdb`, NASM,
`objdump` etc.) vêm do workspace do pwn.college, em `/run/dojo/bin`. O gabarito,
as soluções e o gerador dos checks **não** ficam neste repositório.

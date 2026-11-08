<h2 align="center">Exercício 1: Log do firewall em hexadecimal</h2>

> **Atenção: você só pode errar UMA vez.** No segundo erro o exercício fica
> **invalidado** e a flag não é mais liberada. Arquivo faltando ou fora do
> formato não conta como tentativa, mas resposta errada conta.

## Cenário

O firewall da empresa gravou os IPs de origem de 4 conexões em hexadecimal, com
4 bytes por IP, um byte por octeto. A rede interna é `192.168.10.0/24`. Um
analista precisa saber quais conexões vieram de dentro da rede.

Log, na ordem em que foi gravado:

```
C0A80A2D
C0A80B05
0A00000F
C0A80AFE
```

## Sua missão

Converta cada IP para decimal pontuado e classifique-o como `INTERNO` ou
`EXTERNO` à rede `192.168.10.0/24`. Um dos IPs é uma armadilha: começa como um
IP da empresa, mas está fora da rede interna. Conclua qual é e escreva-o em
binário.

## Formato da resposta

Arquivo `/home/hacker/resposta.txt`, com **5 linhas**:

| Linha | Conteúdo |
|---|---|
| 1 a 4 | IP em decimal pontuado, um espaço, `INTERNO` ou `EXTERNO`, na ordem do log |
| 5 | o IP `192.168.x.x` que é EXTERNO, em binário: 4 octetos de 8 bits separados por ponto |

Exemplo de formato, com valores fictícios:

```
172.16.5.9 EXTERNO
172.16.0.1 INTERNO
8.8.4.4 EXTERNO
172.16.0.200 INTERNO
10101100.00010000.00000101.00001001
```

## Como conferir

Quando tiver certeza, rode `/challenge/check`.

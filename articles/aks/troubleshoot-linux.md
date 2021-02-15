---
title: Ferramentas de desempenho do Linux
titleSuffix: Azure Kubernetes Service
description: Saiba como usar as ferramentas de desempenho do Linux para solucionar problemas comuns ao usar o AKS (serviço kubernetes do Azure).
services: container-service
author: alexeldeib
ms.service: container-service
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: aleldeib
ms.openlocfilehash: 74f65780594c7bc938ed6d59437473c4363e5848
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90982028"
---
# <a name="linux-performance-troubleshooting"></a>Solução de problemas de desempenho do Linux

O esgotamento de recursos em computadores Linux é um problema comum e pode ser manifestado por meio de uma ampla variedade de sintomas. Este documento fornece uma visão geral de alto nível das ferramentas disponíveis para ajudar a diagnosticar esses problemas.

Muitas dessas ferramentas aceitam um intervalo no qual produzir saída sem interrupção. Esse formato de saída geralmente torna os padrões de descobrir muito mais fáceis. Quando aceito, a invocação de exemplo incluirá `[interval]` .

Muitas dessas ferramentas têm um amplo histórico e amplo conjunto de opções de configuração. Esta página fornece apenas um subconjunto simples de invocações para destacar problemas comuns. A fonte de informações canônica é sempre a documentação de referência para cada ferramenta específica. Essa documentação será muito mais completa do que a fornecida aqui.

## <a name="guidance"></a>Diretrizes

Seja sistemático em sua abordagem para investigar problemas de desempenho. Duas abordagens comuns são usar (utilização, saturação, erros) e vermelho (taxa, erros, duração). Normalmente, o vermelho é usado no contexto de serviços para monitoramento baseado em solicitação. O uso normalmente é usado para monitorar recursos: para cada recurso em um computador, monitore a utilização, a saturação e os erros. Os quatro tipos principais de recursos em qualquer computador são CPU, memória, disco e rede. Alta utilização, saturação ou taxas de erro para qualquer um desses recursos indica um possível problema com o sistema. Quando houver um problema, investigue a causa raiz: por que a latência de e/s de disco é alta? Os discos ou a SKU da máquina virtual foram restringidos? Quais processos estão gravando nos dispositivos e em quais arquivos?

Alguns exemplos de problemas comuns e indicadores para diagnostique-los:
- Limitação de IOPS: Use iostat para medir o IOPS por dispositivo. Certifique-se de que nenhum disco individual esteja acima do limite e que a soma de todos os discos seja menor que o limite para a máquina virtual.
- Limitação de largura de banda: Use iostat como para IOPS, mas medindo a taxa de transferência de leitura/gravação. Certifique-se de que a taxa de transferência por dispositivo e agregada estejam abaixo dos limites de largura de banda.
- Esgotamento de SNAT: isso pode ser manifestado como conexões de alta (saída) ativas em SAR. 
- Perda de pacotes: isso pode ser medido por proxy por meio da contagem de retransmissão TCP relativa à contagem enviada/recebida. `sar`E `netstat` podem mostrar essas informações.

## <a name="general"></a>Geral

Essas ferramentas são de uso geral e abrangem informações básicas do sistema. Eles são um bom ponto de partida para uma investigação mais aprofundada.

### <a name="uptime"></a>atividade

```
$ uptime
 19:32:33 up 17 days, 12:36,  0 users,  load average: 0.21, 0.77, 0.69
```

o tempo de atividade fornece tempo de atividade do sistema e uma média de carga de 15 minutos. Essas médias de carga correspondem aproximadamente aos threads que estão trabalhando ou aguardando a conclusão do trabalho ininterrupto. Em absoluto, esses números podem ser difíceis de interpretar, mas medidos ao longo do tempo eles podem nos dizer informações úteis:

- média de 1 minuto > média de 5 minutos significa que a carga está aumentando.
- média de 1 minuto < média de 5 minutos significa que a carga está diminuindo.

o tempo de atividade também pode iluminar por que as informações não estão disponíveis: o problema pode ter sido resolvido por conta própria ou por uma reinicialização antes que o usuário possa acessar o computador.

Médias de carga maiores que o número de threads de CPU disponíveis podem indicar um problema de desempenho com uma determinada carga de trabalho.

### <a name="dmesg"></a>dmesg

```
$ dmesg | tail 
$ dmesg --level=err | tail
```

dmesg despeja o buffer do kernel. Eventos como OOMKill adicionam uma entrada ao buffer do kernel. Encontrar um OOMKill ou outras mensagens de esgotamento de recursos nos logs do dmesg é um indicador forte de um problema.

### <a name="top"></a>top

```
$ top
Tasks: 249 total,   1 running, 158 sleeping,   0 stopped,   0 zombie
%Cpu(s):  2.2 us,  1.3 sy,  0.0 ni, 95.4 id,  1.0 wa,  0.0 hi,  0.2 si,  0.0 st
KiB Mem : 65949064 total, 43415136 free,  2349328 used, 20184600 buff/cache
KiB Swap:        0 total,        0 free,        0 used. 62739060 avail Mem

   PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
116004 root      20   0  144400  41124  27028 S  11.8  0.1 248:45.45 coredns
  4503 root      20   0 1677980 167964  89464 S   5.9  0.3   1326:25 kubelet
     1 root      20   0  120212   6404   4044 S   0.0  0.0  48:20.38 systemd
     ...
```

`top` fornece uma visão geral ampla do estado atual do sistema. Os cabeçalhos fornecem algumas informações de agregação úteis:

- estado das tarefas: em execução, em suspensão, parado.
- Utilização da CPU, nesse caso, quase sempre mostrando o tempo ocioso.
- memória do sistema total, livre e usada.

`top` pode perder processos de curta duração; alternativas como `htop` e `atop` fornecem interfaces semelhantes ao corrigir algumas dessas deficiências.

## <a name="cpu"></a>CPU

Essas ferramentas fornecem informações de utilização da CPU. Isso é especialmente útil na saída sem interrupção, onde os padrões se tornam fáceis de detectar.

### <a name="mpstat"></a>mpstat

```
$ mpstat -P ALL [interval]
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

19:49:03     CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
19:49:04     all    1.01    0.00    0.63    2.14    0.00    0.13    0.00    0.00    0.00   96.11
19:49:04       0    1.01    0.00    1.01   17.17    0.00    0.00    0.00    0.00    0.00   80.81
19:49:04       1    1.98    0.00    0.99    0.00    0.00    0.00    0.00    0.00    0.00   97.03
19:49:04       2    1.01    0.00    0.00    0.00    0.00    1.01    0.00    0.00    0.00   97.98
19:49:04       3    0.00    0.00    0.99    0.00    0.00    0.99    0.00    0.00    0.00   98.02
19:49:04       4    1.98    0.00    1.98    0.00    0.00    0.00    0.00    0.00    0.00   96.04
19:49:04       5    1.00    0.00    1.00    0.00    0.00    0.00    0.00    0.00    0.00   98.00
19:49:04       6    1.00    0.00    1.00    0.00    0.00    0.00    0.00    0.00    0.00   98.00
19:49:04       7    1.98    0.00    0.99    0.00    0.00    0.00    0.00    0.00    0.00   97.03
```

`mpstat` Imprime informações de CPU semelhantes para a parte superior, mas dividida por thread de CPU. Ver todos os núcleos de uma vez pode ser útil para detectar o uso de CPU altamente desequilibrado, por exemplo, quando um aplicativo de thread único usa um núcleo com 100% de utilização. Esse problema pode ser mais difícil de detectar quando agregado em todas as CPUs no sistema.

### <a name="vmstat"></a>vmstat

```
$ vmstat [interval]
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 2  0      0 43300372 545716 19691456    0    0     3    50    3    3  2  1 95  1  0
```

`vmstat` fornece informações semelhantes `mpstat` e `top` , enumerando o número de processos aguardando pela CPU (coluna de r), estatísticas de memória e porcentagem do tempo de CPU gasto em cada Estado de trabalho.

## <a name="memory"></a>Memória

A memória é um recurso muito importante e, felizmente, fácil de controlar. Algumas ferramentas podem relatar CPU e memória, como `vmstat` . Mas ferramentas como `free` ainda podem ser úteis para a depuração rápida.

### <a name="free"></a>free

```
$ free -m
              total        used        free      shared  buff/cache   available
Mem:          64403        2338       42485           1       19579       61223
Swap:             0           0           0
```

`free` apresenta informações básicas sobre memória total, bem como memória usada e livre. `vmstat` pode ser mais útil mesmo para análise de memória básica devido à sua capacidade de fornecer saída sem interrupção.

## <a name="disk"></a>Disco

Essas ferramentas medem o IOPS de disco, as filas de espera e a taxa de transferência total. 

### <a name="iostat"></a>iostat

```
$ iostat -xy [interval] [count]
$ iostat -xy 1 1
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           3.42    0.00    2.92    1.90    0.00   91.76

Device:         rrqm/s   wrqm/s     r/s     w/s    rkB/s    wkB/s avgrq-sz avgqu-sz   await r_await w_await  svctm  %util
loop0             0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
sdb               0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
sda               0.00    56.00    0.00   65.00     0.00   504.00    15.51     0.01    3.02    0.00    3.02   0.12   0.80
scd0              0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
```

`iostat` fornece informações aprofundadas sobre a utilização do disco. Essa invocação passa `-x` por estatísticas estendidas, `-y` para ignorar a média inicial do sistema de impressão de saída desde a inicialização e `1 1` para especificar que desejamos um intervalo de 1 segundo, terminando após um bloco de saída. 

`iostat` o expõe muitas estatísticas úteis:

- `r/s` e `w/s` são leituras por segundo e gravações por segundo. A soma desses valores é IOPS.
- `rkB/s` e `wkB/s` são lidos/gravados por segundo. A soma desses valores é taxa de transferência.
- `await` é o tempo médio de iowait em milissegundos para solicitações em fila.
- `avgqu-sz` é o tamanho médio da fila no intervalo fornecido.

Em uma VM do Azure:

- a soma de `r/s` e `w/s` para um dispositivo de bloco individual não pode exceder os limites de SKU do disco.
- a soma de `rkB/s` e `wkB/s`  para um dispositivo de bloco individual não pode exceder os limites de SKU do disco
- a soma de `r/s` e `w/s` para todos os dispositivos de bloco pode não exceder os limites para a SKU de VM.
- a soma de  `rkB/s` e ' wkB/s para todos os dispositivos de bloco pode não exceder os limites para a SKU de VM.

Observe que o disco do sistema operacional conta como um disco gerenciado da menor SKU correspondente à sua capacidade. Por exemplo, um disco de sistema operacional 1024GB corresponde a um disco p30. Discos do sistema operacional efêmero e discos temporários não têm limites de disco individuais; Eles são limitados apenas pelos limites completos da VM.

Valores diferentes de zero de Await ou avgqu-sz também são bons indicadores de contenção de e/s.

## <a name="network"></a>Rede

Essas ferramentas medem estatísticas de rede como taxa de transferência, falhas de transmissão e utilização. Análise mais profunda pode expor estatísticas TCP refinadas sobre congestionamento e pacotes ignorados.

### <a name="sar"></a>Kong

```
$ sar -n DEV [interval]
22:36:57        IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s   %ifutil
22:36:58      docker0      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
22:36:58    azv604be75d832      1.00      9.00      0.06      1.04      0.00      0.00      0.00      0.00
22:36:58       azure0     68.00     79.00     27.79     52.79      0.00      0.00      0.00      0.00
22:36:58    azv4a8e7704a5b    202.00    207.00     37.51     21.86      0.00      0.00      0.00      0.00
22:36:58    azve83c28f6d1c     21.00     30.00     24.12      4.11      0.00      0.00      0.00      0.00
22:36:58         eth0    314.00    321.00     70.87    163.28      0.00      0.00      0.00      0.00
22:36:58    azva3128390bff     12.00     20.00      1.14      2.29      0.00      0.00      0.00      0.00
22:36:58    azvf46c95ddea3     10.00     18.00     31.47      1.36      0.00      0.00      0.00      0.00
22:36:58       enP1s1     74.00    374.00     29.36    166.94      0.00      0.00      0.00      0.00
22:36:58           lo      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
22:36:58    azvdbf16b0b2fc      9.00     19.00      3.36      1.18      0.00      0.00      0.00      0.00
```

`sar` é uma ferramenta poderosa para uma ampla gama de análises. Embora este exemplo use sua capacidade de medir as estatísticas de rede, é igualmente eficiente para medir a CPU e o consumo de memória. Este exemplo invoca `sar` com `-n` sinalizador para especificar a `DEV` palavra-chave (dispositivo de rede), exibindo a taxa de transferência de rede por dispositivo.

- A soma de `rxKb/s` e `txKb/s` é a taxa de transferência total para um determinado dispositivo. Quando esse valor exceder o limite para a NIC provisionada do Azure, as cargas de trabalho no computador passarão por latência de rede aumentada.
- `%ifutil` mede a utilização de um determinado dispositivo. À medida que esse valor se aproximar 100%, as cargas de trabalho sofrerão maior latência de rede.

```
$ sar -n TCP,ETCP [interval]
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

22:50:08     active/s passive/s    iseg/s    oseg/s
22:50:09         2.00      0.00     19.00     24.00

22:50:08     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
22:50:09         0.00      0.00      0.00      0.00      0.00

Average:     active/s passive/s    iseg/s    oseg/s
Average:         2.00      0.00     19.00     24.00

Average:     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
Average:         0.00      0.00      0.00      0.00      0.00
```

Essa invocação do `sar` usa as `TCP,ETCP` palavras-chave para examinar as conexões TCP. A terceira coluna da última linha, "retrans", é o número de retransmissãos de TCP por segundo. Valores altos para este campo indicam uma conexão de rede não confiável. Na primeira e terceira linhas, "ativa" significa que uma conexão foi originada do dispositivo local, enquanto "Remote" indica uma conexão de entrada.  Um problema comum no Azure é o esgotamento de porta SNAT, que `sar` pode ajudar a detectar. O esgotamento da porta SNAT seria manifestado como valores "ativos" altos, pois o problema ocorre devido a uma alta taxa de conexões TCP de saída, iniciadas localmente.

Como `sar` usa um intervalo, ele imprime a saída sem interrupção e, em seguida, imprime as linhas finais da saída que contém os resultados médios da invocação.

### <a name="netstat"></a>netstat

```
$ netstat -s
Ip:
    71046295 total packets received
    78 forwarded
    0 incoming packets discarded
    71046066 incoming packets delivered
    83774622 requests sent out
    40 outgoing packets dropped
Icmp:
    103 ICMP messages received
    0 input ICMP message failed.
    ICMP input histogram:
        destination unreachable: 103
    412802 ICMP messages sent
    0 ICMP messages failed
    ICMP output histogram:
        destination unreachable: 412802
IcmpMsg:
        InType3: 103
        OutType3: 412802
Tcp:
    11487089 active connections openings
    592 passive connection openings
    1137 failed connection attempts
    404 connection resets received
    17 connections established
    70880911 segments received
    95242567 segments send out
    176658 segments retransmited
    3 bad segments received.
    163295 resets sent
Udp:
    164968 packets received
    84 packets to unknown port received.
    0 packet receive errors
    165082 packets sent
UdpLite:
TcpExt:
    5 resets received for embryonic SYN_RECV sockets
    1670559 TCP sockets finished time wait in fast timer
    95 packets rejects in established connections because of timestamp
    756870 delayed acks sent
    2236 delayed acks further delayed because of locked socket
    Quick ack mode was activated 479 times
    11983969 packet headers predicted
    25061447 acknowledgments not containing data payload received
    5596263 predicted acknowledgments
    19 times recovered from packet loss by selective acknowledgements
    Detected reordering 114 times using SACK
    Detected reordering 4 times using time stamp
    5 congestion windows fully recovered without slow start
    1 congestion windows partially recovered using Hoe heuristic
    5 congestion windows recovered without slow start by DSACK
    111 congestion windows recovered without slow start after partial ack
    73 fast retransmits
    26 retransmits in slow start
    311 other TCP timeouts
    TCPLossProbes: 198845
    TCPLossProbeRecovery: 147
    480 DSACKs sent for old packets
    175310 DSACKs received
    316 connections reset due to unexpected data
    272 connections reset due to early user close
    5 connections aborted due to timeout
    TCPDSACKIgnoredNoUndo: 8498
    TCPSpuriousRTOs: 1
    TCPSackShifted: 3
    TCPSackMerged: 9
    TCPSackShiftFallback: 177
    IPReversePathFilter: 4
    TCPRcvCoalesce: 1501457
    TCPOFOQueue: 9898
    TCPChallengeACK: 342
    TCPSYNChallenge: 3
    TCPSpuriousRtxHostQueues: 17
    TCPAutoCorking: 2315642
    TCPFromZeroWindowAdv: 483
    TCPToZeroWindowAdv: 483
    TCPWantZeroWindowAdv: 115
    TCPSynRetrans: 885
    TCPOrigDataSent: 51140171
    TCPHystartTrainDetect: 349
    TCPHystartTrainCwnd: 7045
    TCPHystartDelayDetect: 26
    TCPHystartDelayCwnd: 862
    TCPACKSkippedPAWS: 3
    TCPACKSkippedSeq: 4
    TCPKeepAlive: 62517
IpExt:
    InOctets: 36416951539
    OutOctets: 41520580596
    InNoECTPkts: 86631440
    InECT0Pkts: 14
```

`netstat` pode introspecção uma grande variedade de estatísticas de rede, aqui invocada com saída de resumo. Há muitos campos úteis aqui, dependendo do problema. Um campo útil na seção TCP é "tentativas de conexão com falha". Isso pode ser uma indicação de esgotamento de porta SNAT ou outros problemas para fazer conexões de saída. Uma alta taxa de segmentos retransmitidos (também na seção TCP) pode indicar problemas com a entrega de pacotes. 

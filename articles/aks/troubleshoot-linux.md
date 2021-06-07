---
title: Ferramentas de desempenho do Linux
titleSuffix: Azure Kubernetes Service
description: Saiba como usar as ferramentas de desempenho do Linux para solucionar problemas comuns ao usar o AKS (Serviço de Kubernetes do Azure).
services: container-service
author: alexeldeib
ms.service: container-service
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: aleldeib
ms.openlocfilehash: 74f65780594c7bc938ed6d59437473c4363e5848
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90982028"
---
# <a name="linux-performance-troubleshooting"></a>Solução de problemas de desempenho do Linux

O esgotamento de recursos em computadores Linux é um problema comum e pode se manifestar por meio de uma ampla variedade de sintomas. Este documento fornecerá uma visão geral de alto nível sobre ferramentas disponíveis e usadas para ajudar a diagnosticar esses problemas.

Grande parte dessas ferramentas aceitam um intervalo em que é possível criar uma saída sem interrupção. Esse formato de saída geralmente facilita bastante a detecção de padrões. A invocação de exemplo incluirá um `[interval]` no local em que for aceita.

Grande parte dessas ferramentas têm um histórico extenso, bem como um amplo conjunto de opções de configuração. Esta página fornecerá somente um subconjunto simples de invocações para destacar problemas comuns. A fonte canônica de informações sempre será a documentação de referência de cada ferramenta específica. Essa documentação será muito mais detalhada do que a fornecida aqui.

## <a name="guidance"></a>Diretrizes

Seja sistemático em sua abordagem para investigar problemas de desempenho. Há duas abordagens comuns: USE (utilização, saturação e erros) e RED (taxa, erros e duração). O método RED geralmente é usado no contexto de serviços para executar um monitoramento baseado em solicitação. O método USE geralmente é usado para monitorar recursos: monitore a utilização, a saturação e os erros de cada recurso em um computador. Os quatro principais tipos de recursos em um computador são: a CPU, a memória, o disco e a rede. Altas taxas de utilização, saturação ou erro em um desses recursos indicam um possível problema no sistema. Quando ocorrer um problema, investigue a causa raiz: por que a latência de E/S do disco está alta? Os discos ou o SKU da máquina virtual foram limitados? Quais processos estão executando uma gravação nos dispositivos? Em quais arquivos?

Veja abaixo alguns exemplos de problemas comuns e indicadores usados para diagnosticá-los:
- Limitação de IOPS: use o iostat para medir a IOPS por dispositivo. Verifique se nenhum disco individual está acima do limite, bem como se a soma de todos os discos é menor do que o limite da máquina virtual.
- Limitação da largura de banda: use o iostat do mesmo modo que a IOPS, porém medindo a taxa de transferência de leitura/gravação. Verifique se as taxas de transferência de agregação e por dispositivo estão abaixo dos limites da largura de banda.
- Esgotamento de SNAT: este problema pode se manifestar como conexões (saídas) altamente ativas na ferramenta SAR. 
- Perda de pacotes: é possível medir este problema usando um proxy por meio de uma contagem de retransmissão TCP relativa à contagem enviada/recebida. As ferramentas `sar` e `netstat` podem mostrar essas informações.

## <a name="general"></a>Geral

Estas ferramentas são de uso geral e abrangem informações básicas sobre o sistema. Elas são um ponto de partida adequado para executar uma investigação mais detalhada.

### <a name="uptime"></a>Ferramenta uptime

```
$ uptime
 19:32:33 up 17 days, 12:36,  0 users,  load average: 0.21, 0.77, 0.69
```

A ferramenta uptime fornece um tempo de atividade do sistema e média de carregamento de um, cinco e 15 minutos. Essa média de carregamento correspondem de modo aproximado aos threads em execução ou aguardando a conclusão do trabalho ininterrupto. Esses números podem ser difíceis de interpretar na íntegra. Contudo, quando avaliados ao longo do tempo, eles podem apresentar informações úteis:

- Média de 1 minuto > média de 5 minutos significa que o carregamento está aumentando.
- Média de 1 minuto < média de 5 minutos significa que o carregamento está diminuindo.

A ferramenta uptime também pode ilustrar por que as informações não estão disponíveis: o problema talvez tenha sido resolvido por conta própria ou por uma reinicialização executada antes que usuário pudesse acessar o computador.

A média de carregamento maior do que o número de threads da CPU disponíveis poderá indicar um problema de desempenho com uma determinada carga de trabalho.

### <a name="dmesg"></a>Ferramenta dmesg

```
$ dmesg | tail 
$ dmesg --level=err | tail
```

A ferramenta dmesg despeja o buffer do kernel. Eventos semelhantes ao OOMKill adicionam uma entrada ao buffer do kernel. Encontrar um OOMKill ou demais mensagens de esgotamento de recursos nos logs da ferramenta dmesg é um forte indicativo de problemas.

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

A ferramenta `top` fornece uma visão geral abrangente sobre o estado atual do sistema. Os cabeçalhos fornecem informações úteis sobre agregação:

- Estado das tarefas: em execução, suspensas e interrompidas.
- Utilização da CPU: neste caso mostra principalmente o tempo ocioso.
- Memória do sistema: total, livre e usada.

A ferramenta `top` poderá ignorar processos de curta duração. Além disso, alternativas como `htop` e `atop` fornecem interfaces semelhantes ao corrigir algumas dessas deficiências.

## <a name="cpu"></a>CPU

Essas ferramentas fornecem informações sobre a utilização da CPU. Isso será útil principalmente em uma saída sem interrupção, em que os padrões facilitam a detecção.

### <a name="mpstat"></a>Ferramenta mpstat

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

A ferramenta `mpstat` exibe informações semelhantes à ferramenta top sobre a CPU. No entanto, as informações são divididas por thread da CPU. Ver todos os núcleos de uma vez pode ser útil para detectar o uso altamente desequilibrado da CPU. Por exemplo, no caso de um aplicativo de thread único com 100% de utilização do núcleo. Talvez seja mais difícil detectar esse problema quando ele for agregado a todas as CPUs do sistema.

### <a name="vmstat"></a>Ferramenta vmstat

```
$ vmstat [interval]
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 2  0      0 43300372 545716 19691456    0    0     3    50    3    3  2  1 95  1  0
```

A ferramenta `vmstat` fornece informações semelhantes às ferramentas `mpstat` e `top`, enumerando a quantidade de processos em espera na CPU (coluna r), as estatísticas de memória e o percentual de tempo da CPU gasto em cada estado de trabalho.

## <a name="memory"></a>Memória

A memória é um recurso muito importante e fácil de rastrear. Além disso, algumas ferramentas podem relatar o funcionamento da CPU e da memória, como a ferramenta `vmstat`. No entanto, outras ferramentas ainda podem ser úteis para executar uma depuração rápida, como a ferramenta `free`.

### <a name="free"></a>free

```
$ free -m
              total        used        free      shared  buff/cache   available
Mem:          64403        2338       42485           1       19579       61223
Swap:             0           0           0
```

A ferramenta `free` apresenta informações básicas sobre a memória total, bem como a memória usada e a livre. A ferramenta `vmstat` poderá ser mais útil até mesmo para executar uma análise básica de memória devido à capacidade de fornecer uma saída sem interrupção.

## <a name="disk"></a>Disco

Essas ferramentas medem a IOPS, as filas de espera e a taxa de transferência total do disco. 

### <a name="iostat"></a>Ferramenta iostat

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

A ferramenta `iostat` fornece insights detalhados sobre a utilização do disco. Essa invocação aprova o `-x` para obter estatísticas estendidas, o `-y` para ignorar a média inicial do sistema de impressão de saída da inicialização em diante e o `1 1` para especificar que desejamos obter um intervalo de um segundo, terminando após um bloco de saída. 

A ferramenta `iostat` expõe várias estatísticas úteis:

- O `r/s` e o `w/s` representam leituras por segundo e gravações por segundo. A soma desses valores representa a IOPS.
- O `rkB/s` e o `wkB/s` representam a leitura/gravação em quilobytes por segundo. A soma desses valores representa a taxa de transferência.
- O `await` representa o tempo médio de iowait em milissegundos para executar solicitações na fila.
- O `avgqu-sz` representa o tamanho médio da fila no intervalo fornecido.

Em uma VM do Azure:

- A soma de `r/s` e `w/s` de um dispositivo de blocos individual não poderá exceder os limites de SKU desse disco.
- A soma de `rkB/s` e `wkB/s` de um dispositivo de blocos individual não poderá exceder os limites de SKU desse disco
- A soma de `r/s` e `w/s` de todos os dispositivos de blocos não poderá exceder limites para o SKU da VM.
- A soma de `rkB/s` e wkB/s de todos os dispositivos de blocos não poderá exceder limites para o SKU da VM.

Observe que o disco do sistema operacional conta como um disco gerenciado do menor SKU correspondente à respectiva capacidade. Por exemplo, um disco de SO de 1024 GB corresponde a um disco P30. Os discos de SO efêmero e os discos temporários não têm limites de disco individuais. Eles têm somente os limites completos da VM.

Os valores diferentes de zero de await ou avgqu-sz também são indicadores adequados de contenção de E/S.

## <a name="network"></a>Rede

Essas ferramentas medem as estatísticas de rede, como a taxa de transferência, as falhas de transmissão e a utilização. Uma análise mais detalhada pode expor estatísticas refinadas do protocolo TCP sobre o congestionamento e os pacotes removidos.

### <a name="sar"></a>Ferramenta sar

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

A ferramenta `sar` é avançada, além de ser usada em uma ampla variedade de análises. Embora este exemplo use a capacidade da ferramenta de medir estatísticas de rede, ela também é potente para medir o consumo da CPU e da memória. Este exemplo invoca a ferramenta `sar` com o sinalizador `-n` para especificar a palavra-chave `DEV` (dispositivo de rede), exibindo a taxa de transferência de rede por dispositivo.

- A soma de `rxKb/s` e `txKb/s` representa a taxa de transferência total de um determinado dispositivo. Quando esse valor exceder o limite do adaptador de rede do Azure que foi provisionado, as cargas de trabalho apresentarão um aumento da latência de rede no computador.
- O `%ifutil` mede a utilização de um determinado dispositivo. Conforme esse valor se aproximar de 100%, as cargas de trabalho apresentarão um aumento da latência de rede.

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

Essa invocação da ferramenta `sar` usa as palavras-chave `TCP,ETCP` para examinar conexões TCP. A terceira coluna da última linha ("retrans") representa o número de retransmissões TCP por segundo. Valores altos neste campo indicam uma conexão de rede não confiável. Na primeira e na terceira linha, "active" significa que uma conexão foi criada no dispositivo local, enquanto "remote" indica uma conexão de entrada.  Um problema comum no Azure é o esgotamento da porta SNAT, que a ferramenta `sar` pode ajudar a detectar. O esgotamento da porta SNAT se manifestará como valores "active" altos, pois o problema ocorrerá devido a uma alta taxa de conexões TCP de saída iniciadas localmente.

Como a ferramenta `sar` usa um intervalo, ela exibirá uma saída sem interrupção, bem como as linhas finais da saída com os resultados médios da invocação.

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

A ferramenta `netstat` pode analisar uma ampla variedade de estatísticas de rede, invocadas aqui com a saída de resumo. Há vários campos úteis aqui que podem ser usados de acordo com cada problema. Um campo útil mostrado na seção do protocolo TCP é a opção "tentativas de conexão com falha". Ele poderá ser um indicativo de problemas durante a execução de conexões de saída ou do esgotamento da porta SNAT. Uma alta taxa de segmentos retransmitidos (também mostrada na seção do protocolo TCP) poderá indicar problemas na entrega de pacotes. 

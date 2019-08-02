---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: include
ms.date: 06/25/2019
ms.author: azcspmt;ayshak;cynthn
ms.custom: include file
ms.openlocfilehash: 6a3e2034792fdc0a4a8fed7885c7d5ad78ea24d9
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67501207"
---
A família de VMs da série B permite que você escolha o tamanho de VM que oferece o nível necessário da linha de base de desempenho para sua carga de trabalho, com a capacidade de aumentar o desempenho da CPU em até 100% de um Intel® Broadwell E5-2673 v4 2.3 GHz ou vCPU do processador do Intel® Haswell 2.4 GHz E5-2673 v3.

As VMs da série B são ideais para cargas de trabalho que não precisam ter o desempenho total da CPU continuamente, como servidores Web, provas de conceito, bancos de dados pequenos e ambientes de build de desenvolvimento. Normalmente, essas cargas de trabalho têm requisitos de desempenho expansíveis. A série B permite a compra de um tamanho de VM com a linha de base de desempenho, e a instância da VM criará créditos quando estiver usando menos que a linha de base. Quando a VM acumular crédito, ela poderá ultrapassar a linha de base usando até 100% da vCPU quando seu aplicativo exigir um melhor desempenho de CPU.

A série B possui os seguintes tamanhos VM:

| Tamanho             | vCPU  | Memória: GiB | Armazenamento temporário (SSD) GiB | Base de desempenho da CPU da VM | Máximo desempenho da CPU da VM | Créditos iniciais | Créditos armazenados/hora | Máximo de créditos armazenados | Discos de dados máximos | Taxa de transferência máxima de armazenamento temporário: IOPS / MBps | Taxa de transferência de disco sem cache: IOPS / MBps | Máximo de NICs |          
|---------------|-------------|----------------|----------------------------|-----------------------|--------------------|--------------------|--------------------|----------------|----------------------------------------|-------------------------------------------|-------------------------------------------|----------|
| Standard_B1ls<sup>1</sup>  | 1           | 0,5              | 4                          | 5%                   | 100%                   | 30                   | 3                  | 72            | 2                                      | 200 / 10                                  | 160 / 10                                  | 2  |
| Standard_B1s  | 1           | 1              | 4                          | 10%                   | 100%                   | 30                   | 6                  | 144            | 2                        | 400 / 10                                  | 320 / 10                                  | 2  |
| Standard_B1ms | 1           | 2              | 4                          | 20%                   | 100%                   | 30                   | 12                 | 288           | 2                         | 800 / 10                                  | 640 / 10                                  | 2  |
| Standard_B2s  | 2           | 4              | 8                          | 40%                   | 200%                   | 60                   | 24                 | 576            | 4                                      | 1600 / 15                                 | 1280 / 15                                 | 3  |
| Standard_B2ms | 2           | 8              | 16                         | 60%                   | 200%                   | 60                   | 36                 | 864            | 4                                      | 2400 / 22.5                               | 1920 / 22.5                               | 3  |
| Standard_B4ms | 4           | 16             | 32                         | 90%                   | 400%                   | 120                   | 54                 | 1296           | 8                                      | 3600 / 35                                 | 2880 / 35                                 | 4  |
| Standard_B8ms | 8           | 32             | 64                         | 135%                  | 800%                   | 240                   | 81                 | 1944           | 16                                     | 4320 / 50                                 | 4320 / 50                                 | 4  |
| Standard_B12ms | 12           | 48             | 96                         | 202%                  | 1200%                   | 360                   | 121                 | 2909           | 16                                     | 6480 / 75                                 | 4320 / 50                                  | 6  |
| Standard_B16ms | 16           | 64             | 128                         | 270%                  | 1600%                   | 480                   | 162                 | 3888           | 32                                     | 8640 / 100                                 | 4320 / 50                                 | 8  |
| Standard_B20ms | 20           | 80             | 160                         | 337%                  | 2000%                   | 600                   | 203                 | 4860           | 32                                     | 10800 / 125                                 | 4320 / 50                                 | 8  |

<sup>1</sup> B1ls só tem suporte no Linux

## <a name="workload-example"></a>Exemplo de carga de trabalho

Considere um aplicativo do office check-in/out. O aplicativo precisa picos de CPU durante o horário comercial, mas não muita potência de computação durante horários inativos. Neste exemplo, a carga de trabalho requer uma máquina virtual de 16vCPU com 64GiB de RAM para trabalhar com eficiência.

A tabela mostra os dados de tráfego por hora e o gráfico é uma representação visual de que o tráfego.

B16 características:

Desempenho máximo de CPU: 16vCPU * 100% = 1600%

Linha de base: 270%

![Gráfico de dados de tráfego por hora](./media/virtual-machines-common-b-series-burstable/office-workload.png)

| Cenário | Hora | Uso da CPU (%) | Créditos acumulados<sup>1</sup> | Créditos disponíveis |
| --- | --- | --- | --- | --- |
| Implantação de B16ms | Implantação | Implantação  | 480 (créditos inicias) | 480 |
| Nenhum tráfego | 0:00 | 0 | 162 | 642 |
| Nenhum tráfego | 1:00 | 0 | 162 | 804 |
| Nenhum tráfego | 2:00 | 0 | 162 | 966 |
| Nenhum tráfego | 3:00 | 0 | 162 | 1128 |
| Nenhum tráfego | 4:00 | 0 | 162 | 1290 |
| Nenhum tráfego | 5:00 | 0 | 162 | 1452 |
| Baixo tráfego | 6:00 | 270 | 0 | 1452 |
| Os funcionários são fornecidos para office (o aplicativo precisa de 80% vCPU) | 7:00 | 1280 | -606 | 846 |
| Os funcionários continuarem as novidades do office (o aplicativo precisa de 80% vCPU) | 8:00 | 1280 | -606 | 240 |
| Baixo tráfego | 9:00 | 270 | 0 | 240 |
| Baixo tráfego | 10:00 | 100 | 102 | 342 |
| Baixo tráfego | 11:00 | 50 | 132 | 474 |
| Baixo tráfego | 12:00 | 100 | 102 | 576 |
| Baixo tráfego | 13:00 | 100 | 102 | 678 |
| Baixo tráfego | 14:00 | 50 | 132 | 810 |
| Baixo tráfego | 15:00 | 100 | 102 | 912 |
| Baixo tráfego | 16:00 | 100 | 102 | 1014 |
| Fazendo check-out (vCPU de 100% de necessidades do aplicativo) de funcionários | 17:00 | 1600 | -798 | 216 |
| Baixo tráfego | 18:00 | 270 | 0 | 216 |
| Baixo tráfego | 19:00 | 270 | 0 | 216 |
| Baixo tráfego | 20:00 | 50 | 132 | 348 |
| Baixo tráfego | 21:00 | 50 | 132 | 480 |
| Nenhum tráfego | 22:00 | 0 | 162 | 642 |
| Nenhum tráfego | 23:00 | 0 | 162 | 804 |

<sup>1</sup> créditos acumulados/créditos usados em uma hora é equivalente a: `((Base CPU perf of VM - CPU Usage) / 100) * 60 minutes`.  

Para um D16s_v3 que tem 16 vCPUs e de 64 GiB de memória a taxa por hora é US $0,936 por hora (mensal US $673.92) e para B16ms com 16 vCPUs e de 64 GiB de memória a taxa é de US $0.794 por hora (mensal US $547.86). <b> Isso resulta em 15% de economia!</b>

## <a name="q--a"></a>Perguntas e respostas

### <a name="q-how-do-you-get-135-baseline-performance-from-a-vm"></a>P: Como obter 135% desempenho de linha de base de uma VM?
**R**: A 135% são compartilhados entre as 8 Vcpus que compõem o tamanho da VM. Por exemplo, se seu aplicativo utiliza 4 dos 8 núcleos trabalhando em processamento de lotes e cada uma das 4 vCPUs estão sendo executadas a 30% da utilização, o desempenho total da CPU da VM será de 120%.  Isso significa que a VM estaria criando créditos de tempo com base no delta de 15% da sua linha de base de desempenho.  Mas isso também significa que quando você tem créditos disponíveis, a mesma VM pode usar 100% de todas as 8 vCPUs, o que daria à VM um desempenho máximo de CPU de 800%.


### <a name="q-how-can-i-monitor-my-credit-balance-and-consumption"></a>P: Como posso monitorar meu saldo e consumo
**R**: Apresentaremos 2 novas métricas nas próximas semanas, o **crédito** métrica permitirá ver quantos créditos de sua VM acumulou e a **ConsumedCredit** métrica mostrará quantos créditos de CPU sua VM tiver consumido pelo banco.    Você poderá exibir essas métricas no painel de métricas no portal ou programaticamente pelas APIs do Azure Monitor.

Para saber mais sobre como acessar os dados de métrica do Azure, confira [Visão geral das métricas no Microsoft Azure](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="q-how-are-credits-accumulated"></a>P: Como os créditos são acumulados?
**R**: As taxas de consumo e Acumulação da VM são definidas, de modo que uma VM em execução em exatamente seu nível de desempenho de base terá nem acúmulo ou consumo de créditos.  Uma VM terá um aumento de créditos sempre que estiver em execução abaixo da linha de base de desempenho e terá uma redução nos créditos sempre que a VM estiver usando a CPU acima da sua linha de base de desempenho.

**Exemplo**:  Posso implantar uma VM usando o tamanho B1ms para meu pequeno e o aplicativo de banco de dados de presença. Esse tamanho permite que o meu aplicativo use até 20% de uma vCPU como minha linha de base, o que significa 0,2 de crédito por minuto que posso usar ou acumular. 

Meu aplicativo está ocupado no início e no final do dia de trabalho dos meus funcionários, de 7h às 9h e de 16h às 18h. Durante as outras 20 horas do dia, meu aplicativo está normalmente ocioso, usando apenas 10% da vCPU. No horário fora de pico, acumulo 0,2 de crédito por minuto, mas consumo 0,1 de crédito por minuto, ou seja, minha VM acumulará 0,1 x 60 = 6 créditos por hora.  Nas 20 horas em que estou fora de pico, acumularei 120 créditos.  

Durante o horário de pico, meu aplicativo aproveita 60% de utilização de vCPU, ainda acumulo 0,2 de crédito por minuto, mas consumo 0,6 de crédito por minuto, com um custo líquido de 0,4 de crédito por minuto, ou 0,4 x 60 = 24 créditos por hora. Tenho 4 horas por dia de pico, ou seja, meu uso em hora de pico é de 4 x 24 = 96 créditos.

Se eu usar os 120 créditos acumulados fora de pico e subtrair os 96 créditos que usei para meu horário de pico, acumulo mais 24 créditos por dia para usar em outras atividades.

### <a name="q-how-can-i-calculate-credits-accumulated-and-used"></a>P: Como é possível calcular créditos acumulados e usado?
**R**: Você pode usar a seguinte fórmula: 

(Base de desempenho da CPU da VM – uso da CPU) / 100 = bank créditos ou use por minuto

Por exemplo, em acima da instância de sua linha de base é 20% e se você usar 10% da CPU você está se acumulando (20% a 10%) / 100 = 0,1 de crédito por minuto.

### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>P: A série B dá suporte a discos de dados do armazenamento Premium?
**R**: Sim, todos os tamanhos da série B dão suporte a discos de dados de armazenamento Premium.   
    
### <a name="q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart"></a>P: Por que meu crédito restante é definido como 0 após uma reimplantação ou um parar/iniciar?
**A** : Quando uma VM é "REDPLOYED" e a VM for movida para outro nó, o crédito acumulado será perdido. Se a VM for iniciada/interrompida, mas permanecer no mesmo nó, a VM retém o crédito acumulado. Sempre que a VM iniciar de novo em um nó, ele recebe uma crédito inicial, para Standard_B8ms de 240 minutos.
    
### <a name="q-what-happens-if-i-deploy-an-unsupported-os-image-on-b1ls"></a>P: O que acontece se eu implantar uma imagem de SO sem suporte no B1ls?
**A** : B1ls só dá suporte a imagens do Linux e se você implantar qualquer outra imagem do sistema operacional não poderá obter a melhor experiência do cliente.

---
title: Série B expansível-máquinas virtuais do Azure
description: Descreve a série B de tamanhos expansíveis de VM do Azure.
services: virtual-machines
ms.subservice: vm-sizes-general
author: styli365
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: sttsinar
ms.openlocfilehash: c19c47ac64f1e41fa998a29d3ea0480bf0d92a63
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102554455"
---
# <a name="b-series-burstable-virtual-machine-sizes"></a>Tamanhos expansíveis da máquina virtual da série B

As VMs da série B são ideais para cargas de trabalho que não precisam do desempenho total da CPU continuamente, como servidores Web, prova de conceitos, bancos de dados pequenos e ambientes de compilação de desenvolvimento. Normalmente, essas cargas de trabalho têm requisitos de desempenho expansíveis. A série B fornece a capacidade de comprar um tamanho de VM com desempenho de linha de base que pode criar créditos quando ele estiver usando menos de sua linha de base. Quando a VM tiver acumulado créditos, a VM poderá ultrapassar a linha de base usando até 100% do vCPU quando seu aplicativo exigir um desempenho de CPU maior.

A série B vem nos seguintes tamanhos de VM:

[ACU (unidade de computação do Azure)](./acu.md): varia *<br>
[Armazenamento Premium](premium-storage-performance.md): com suporte<br>
[Armazenamento em cache Premium](premium-storage-performance.md): sem suporte<br>
[Migração ao vivo](maintenance-and-updates.md): com suporte<br>
[Atualizações de preservação de memória](maintenance-and-updates.md): com suporte<br>
[Suporte à geração de VM](generation-2.md): geração 1 e 2<br>
[Rede acelerada](../virtual-network/create-vm-accelerated-networking-cli.md): com suporte * *<br>
[Discos do sistema operacional efêmero](ephemeral-os-disks.md): com suporte <br>

* As VMs da série B são intermitentes e, portanto, os números do ACU variam dependendo das cargas de trabalho e do uso do núcleo.<br>
* * A rede acelerada só tem suporte para *Standard_B12ms*, *Standard_B16ms* e *Standard_B20ms*.
<br>
<br>

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Base de desempenho da CPU da VM | Máximo desempenho da CPU da VM | Créditos iniciais | Créditos bancários/hora | Máximo de créditos armazenados | Discos de dados máximos | Taxa de transferência máxima de armazenamento temporário: IOPS/MBps | Taxa de transferência de disco sem cache: IOPS/MBps | Máximo de NICs |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Standard_B1ls<sup>1</sup> | 1  | 0,5 | 4   | 5%   | 100%  | 30  | 3   | 72   | 2  | 200/10    | 160/10    | 2  |
| Standard_B1s              | 1  | 1   | 4   | 10%  | 100%  | 30  | 6   | 144  | 2  | 400/10    | 320/10    | 2  |
| Standard_B1ms             | 1  | 2   | 4   | 20%  | 100%  | 30  | 12  | 288  | 2  | 800/10    | 640/10    | 2  |
| Standard_B2s              | 2  | 4   | 8   | 40%  | 200%  | 60  | 24  | 576  | 4  | 1600/15   | 1280/15   | 3  |
| Standard_B2ms             | 2  | 8   | 16  | 60%  | 200%  | 60  | 36  | 864  | 4  | 2400/22,5 | 1920/22,5 | 3  |
| Standard_B4ms             | 4  | 16  | 32  | 90%  | 400%  | 120 | 54  | 1296 | 8  | 3600/35   | 2880/35   | 4  |
| Standard_B8ms             | 8  | 32  | 64  | 135% | 800%  | 240 | 81  | 1944 | 16 | 4320/50   | 4320/50   | 4  |
| Standard_B12ms            | 12 | 48  | 96  | 202% | 1200% | 360 | 121 | 2909 | 16 | 6480/75   | 4320/50   | 6  |
| Standard_B16ms            | 16 | 64  | 128 | 270% | 1600% | 480 | 162 | 3888 | 32 | 8640/100  | 4320/50   | 8  |
| Standard_B20ms            | 20 | 80  | 160 | 337% | 2000% | 600 | 203 | 4860 | 32 | 10800/125 | 4320/50   | 8  |

<sup>1</sup> B1ls tem suporte apenas no Linux

## <a name="workload-example"></a>Exemplo de carga de trabalho

Considere um aplicativo de check-in/saída do Office. O aplicativo precisa de picos de CPU durante o horário comercial, mas não muito poder de computação fora do horário de expediente. Neste exemplo, a carga de trabalho requer uma máquina virtual 16vCPU com 64GiB de RAM para trabalhar com eficiência.

A tabela mostra os dados de tráfego por hora e o gráfico é uma representação visual desse tráfego.

Características do B16:

Desempenho máximo da CPU: 16vCPU * 100% = 1600%

Linha de base: 270%

![Gráfico de dados de tráfego por hora](./media/b-series-burstable/office-workload.png)

| Cenário | Hora | Uso da CPU (%) | Créditos acumulados<sup>1</sup> | Créditos disponíveis |
| --- | --- | --- | --- | --- |
| Implantação do B16ms | Implantação | Implantação  | 480 (créditos iniciais) | 480 |
| Nenhum tráfego | 0:00 | 0 | 162 | 642 |
| Nenhum tráfego | 1:00 | 0 | 162 | 804 |
| Nenhum tráfego | 2:00 | 0 | 162 | 966 |
| Nenhum tráfego | 3:00 | 0 | 162 | 1128 |
| Nenhum tráfego | 4:00 | 0 | 162 | 1290 |
| Nenhum tráfego | 5:00 | 0 | 162 | 1452 |
| Baixo tráfego | 6:00 | 270 | 0 | 1452 |
| Os funcionários chegam ao Office (o aplicativo precisa de 80% vCPU) | 7:00 | 1280 | -606 | 846 |
| Os funcionários continuam chegando ao Office (as necessidades do aplicativo são de 80% vCPU) | 8:00 | 1280 | -606 | 240 |
| Baixo tráfego | 9:00 | 270 | 0 | 240 |
| Baixo tráfego | 10:00 | 100 | 102 | 342 |
| Baixo tráfego | 11:00 | 50 | 132 | 474 |
| Baixo tráfego | 12:00 | 100 | 102 | 576 |
| Baixo tráfego | 13:00 | 100 | 102 | 678 |
| Baixo tráfego | 14:00 | 50 | 132 | 810 |
| Baixo tráfego | 15:00 | 100 | 102 | 912 |
| Baixo tráfego | 16:00 | 100 | 102 | 1014 |
| Os funcionários estão fazendo check-out (o aplicativo precisa de 100% vCPU) | 17:00 | 1600 | -798 | 216 |
| Baixo tráfego | 18:00 | 270 | 0 | 216 |
| Baixo tráfego | 19:00 | 270 | 0 | 216 |
| Baixo tráfego | 20:00 | 50 | 132 | 348 |
| Baixo tráfego | 21:00 | 50 | 132 | 480 |
| Nenhum tráfego | 22:00 | 0 | 162 | 642 |
| Nenhum tráfego | 23:00 | 0 | 162 | 804 |

<sup>1</sup> os créditos acumulados/créditos usados em uma hora são equivalentes a: `((Base CPU perf of VM - CPU Usage) / 100) * 60 minutes` .  

Para um D16s_v3 que tem 16 vCPUs e 64 GiB de memória, a taxa horária é $0.936 por hora (mensalmente $673.92) e para B16ms com 16 vCPUs e 64 GiB de memória a taxa é $0.794 por hora (mensalmente $547.86). <b> Isso resulta em uma economia de 15%!</b>

## <a name="q--a"></a>Perguntas e Respostas

### <a name="q-what-happens-when-my-credits-run-out"></a>P: o que acontece quando meus créditos acabam?
**R**: quando os créditos são esgotados, a VM retorna ao desempenho da linha de base.

### <a name="q-how-do-you-get-135-baseline-performance-from-a-vm"></a>P: como obter 135% da linha de base de desempenho de uma VM?

**R**: os 135% são compartilhados entre as 8 vCPUs que compõem o tamanho da VM. Por exemplo, se seu aplicativo utiliza 4 dos 8 núcleos trabalhando em processamento de lotes e cada uma das 4 vCPUs estão sendo executadas a 30% da utilização, o desempenho total da CPU da VM será de 120%.  Isso significa que a VM estaria criando créditos de tempo com base no delta de 15% da sua linha de base de desempenho.  Mas isso também significa que quando você tem créditos disponíveis, a mesma VM pode usar 100% de todas as 8 vCPUs, o que daria à VM um desempenho máximo de CPU de 800%.

### <a name="q-how-can-i-monitor-my-credit-balance-and-consumption"></a>P: como posso monitorar meu saldo de crédito e consumo?

**R**: a métrica de **crédito** permite que você veja quantos créditos sua VM foi bancária e a métrica de **ConsumedCredit** mostrará quantos créditos de CPU sua VM consumiu do banco.    Você poderá exibir essas métricas no painel de métricas no portal ou programaticamente pelas APIs do Azure Monitor.

Para saber mais sobre como acessar os dados de métrica do Azure, confira [Visão geral das métricas no Microsoft Azure](../azure-monitor/data-platform.md).

### <a name="q-how-are-credits-accumulated-and-consumed"></a>P: como os créditos são acumulados e consumidos?

**R**: as taxas de consumo e acumulação da VM estão definidas de modo que uma VM em execução na sua linha de base de desempenho não terá acúmulo ou consumo de créditos.  Uma VM terá um aumento de créditos sempre que estiver em execução abaixo da linha de base de desempenho e terá uma redução nos créditos sempre que a VM estiver usando a CPU acima da sua linha de base de desempenho.

**Exemplo**: implantei uma VM usando o tamanho B1ms para meu aplicativo de banco de dados pequeno. Esse tamanho permite que o meu aplicativo use até 20% de uma vCPU como minha linha de base, o que significa 0,2 de crédito por minuto que posso usar ou acumular.

Meu aplicativo está ocupado no início e no final do dia de trabalho dos meus funcionários, de 7h às 9h e de 16h às 18h. Durante as outras 20 horas do dia, meu aplicativo está normalmente ocioso, usando apenas 10% da vCPU. Para o horário fora do pico, eu recebi 0,2 créditos por minuto, mas consumindo apenas 0,1 créditos por minuto, de modo que minha VM terá o banco 0,1 x 60 = 6 créditos por hora.  Nas 20 horas em que estou fora de pico, acumularei 120 créditos.  

Durante o horário de pico, meu aplicativo aproveita 60% de utilização de vCPU, ainda acumulo 0,2 de crédito por minuto, mas consumo 0,6 de crédito por minuto, com um custo líquido de 0,4 de crédito por minuto, ou 0,4 x 60 = 24 créditos por hora. Tenho 4 horas por dia de pico, ou seja, meu uso em hora de pico é de 4 x 24 = 96 créditos.

Se eu usar os 120 créditos acumulados fora de pico e subtrair os 96 créditos que usei para meu horário de pico, acumulo mais 24 créditos por dia para usar em outras atividades.

### <a name="q-how-can-i-calculate-credits-accumulated-and-used"></a>P: como posso calcular os créditos acumulados e usados?

**R**: você pode usar a seguinte fórmula:

(Desempenho base da CPU da VM-uso da CPU)/100 = banco de créditos ou uso por minuto

por exemplo, na instância acima, sua linha de base é de 20% e se você usar 10% da CPU que está acumulando (20%-10%)/100 = 0,1 crédito por minuto.

### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>P: a série B dá suporte a discos de dados de Armazenamento Premium?

**R**: sim, todos os tamanhos da série B dão suporte a discos de dados de Armazenamento Premium.

### <a name="q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart"></a>P: Por que meu conjunto de créditos restantes está definido como 0 após uma reimplantação ou parar/iniciar?

**R** : quando uma VM é reimplantada e a VM é movida para outro nó, o crédito acumulado é perdido. Se a VM for iniciada/interrompida, mas permanecer no mesmo nó, a VM retém o crédito acumulado. Sempre que a VM começa a ser atualizada em um nó, ela obtém um crédito inicial, por Standard_B8ms é 240.

### <a name="q-what-happens-if-i-deploy-an-unsupported-os-image-on-b1ls"></a>P: o que acontecerá se eu implantar uma imagem de sistema operacional sem suporte no B1ls?

**R: o** B1ls só dá suporte a imagens do Linux e, se você implantar qualquer outra imagem do sistema operacional, talvez não obtenha a melhor experiência do cliente.

## <a name="other-sizes-and-information"></a>Outros tamanhos e informações

- [Propósito geral](sizes-general.md)
- [Computação otimizada](sizes-compute.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)

Calculadora de preços: [calculadora de preços](https://azure.microsoft.com/pricing/calculator/)

Mais informações sobre tipos de discos: [tipos de disco](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.

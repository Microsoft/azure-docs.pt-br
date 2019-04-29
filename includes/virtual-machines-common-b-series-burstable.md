---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;ayshak;cynthn
ms.custom: include file
ms.openlocfilehash: d76a3bb463452207e4b5b023cfe07dfd156e95f4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60731781"
---
A família de VMs da série B permite que você escolha o tamanho de VM que oferece o nível necessário da linha de base de desempenho para sua carga de trabalho, com a capacidade de aumentar o desempenho da CPU em até 100% de um Intel® Broadwell E5-2673 v4 2.3 GHz ou vCPU do processador do Intel® Haswell 2.4 GHz E5-2673 v3.

As VMs da série B são ideais para cargas de trabalho que não precisam ter o desempenho total da CPU continuamente, como servidores Web, provas de conceito, bancos de dados pequenos e ambientes de build de desenvolvimento. Normalmente, essas cargas de trabalho têm requisitos de desempenho expansíveis. A série B permite a compra de um tamanho de VM com a linha de base de desempenho, e a instância da VM criará créditos quando estiver usando menos que a linha de base. Quando a VM acumular crédito, ela poderá ultrapassar a linha de base usando até 100% da vCPU quando seu aplicativo exigir um melhor desempenho de CPU.

A série B tem seis tamanhos de VM:

| Tamanho             | vCPU  | Memória: GiB | Armazenamento temporário (SSD) GiB | Base de desempenho da CPU da VM | Máximo desempenho da CPU da VM | Créditos armazenados/hora | Máximo de créditos armazenados | Discos de dados máximos | Taxa de transferência máxima de armazenamento temporário: IOPS / MBps | Taxa de transferência de disco sem cache: IOPS / MBps | Máximo de NICs |          
|---------------|-------------|----------------|----------------------------|-----------------------|--------------------|--------------------|----------------|----------------------------------------|-------------------------------------------|-------------------------------------------|----------|
| Standard_B1ls<sup>1</sup>  | 1           | 0,5              | 4                          | 5%                   | 100%                   | 3                  | 72            | 2                                      | 200 / 10                                  | 160 / 10                                  | 2  |
| Standard_B1s  | 1           | 1              | 4                          | 10%                   | 100%                   | 6                  | 144            | 2                       | 400 / 10                                  | 320 / 10                                  | 2  |
| Standard_B1ms | 1           | 2              | 4                          | 20%                   | 100%                   | 12                 | 288           | 2                        | 800 / 10                                  | 640 / 10                                  | 2  |
| Standard_B2s  | 2           | 4              | 8                          | 40%                   | 200%                   | 24                 | 576            | 4                                      | 1600 / 15                                 | 1280 / 15                                 | 3  |
| Standard_B2ms | 2           | 8              | 16                         | 60%                   | 200%                   | 36                 | 864            | 4                                      | 2400 / 22.5                               | 1920 / 22.5                               | 3  |
| Standard_B4ms | 4           | 16             | 32                         | 90%                   | 400%                   | 54                 | 1296           | 8                                      | 3600 / 35                                 | 2880 / 35                                 | 4  |
| Standard_B8ms | 8           | 32             | 64                         | 135%                  | 800%                   | 81                 | 1944           | 16                                     | 4320 / 50                                 | 4320 / 50                                 | 4  |

<sup>1</sup> B1ls só tem suporte no Linux

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


### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>P: A série B dá suporte a discos de dados do armazenamento Premium?
**R**: Sim, todos os tamanhos da série B dão suporte a discos de dados de armazenamento Premium.   
    
### <a name="q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart"></a>P: Por que meu crédito restante é definido como 0 após uma reimplantação ou um parar/iniciar?
**A** : Quando uma VM é "REDPLOYED" e a VM for movida para outro nó, o crédito acumulado será perdido. Se a VM for iniciada/interrompida, mas permanecer no mesmo nó, a VM retém o crédito acumulado. Sempre que a VM iniciar de novo em um nó, ele recebe uma crédito inicial, para Standard_B8ms de 240 minutos.
    
### <a name="q-what-happens-if-i-deploy-an-unsupported-os-image-on-b1ls"></a>P: O que acontece se eu implantar uma imagem de SO sem suporte no B1ls?
**A** : B1ls só dá suporte a imagens do Linux e se você implantar qualquer outra imagem do sistema operacional não poderá obter a melhor experiência do cliente.
    
### <a name="q-why-is-there-no-pricing-information-for-b1ls-windows"></a>P: Por que não há nenhuma informação de preço para o windows B1ls?
**A** : B1ls só dá suporte a imagens do Linux e se você implantar qualquer outra imagem do sistema operacional talvez não obtenha a melhor experiência do cliente, mas você será cobrado.


    

    

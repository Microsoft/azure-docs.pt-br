---
title: Parâmetro de comparação de testes de desempenho do volume e métricas de uso de arquivos do Azure NetApp | Microsoft Docs
description: Fornece recomendações para métricas de uso de arquivos do Azure NetApp e de desempenho do volume de testes de benchmark.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: b-juche
ms.openlocfilehash: 12ae9e313655924f11799152b5e58b77776c135c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478804"
---
# <a name="benchmark-testing-for-volume-performance-and-metrics-using-azure-netapp-files"></a>Teste de parâmetro de comparação para desempenho de volume e métricas usando o Azure NetApp Files

Este artigo fornece recomendações para métricas de uso de arquivos do Azure NetApp e de desempenho do volume de testes de benchmark.

## <a name="overview"></a>Visão Geral

Para entender as características de desempenho de um volume de arquivos do Azure NetApp, você pode usar a ferramenta de código-fonte aberto [FIO](https://github.com/axboe/fio) para executar uma série de parâmetros de comparação para simular uma variedade de cargas de trabalho. FIO pode ser instalado em ambos os Linux e sistemas operacionais baseados em Windows.  É uma ferramenta excelente para obter um instantâneo rápido de IOPS e taxa de transferência para um volume.

### <a name="vm-instance-sizing"></a>Dimensionamento de instância VM

Para obter melhores resultados, certifique-se de que você está usando uma instância de máquina virtual (VM) que é dimensionada apropriadamente para realizar os testes. Os exemplos seguintes usam uma instância de Standard_D32s_v3. Para obter mais informações sobre tamanhos de instância VM, consulte [máquinas virtuais de tamanhos para Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) para VMs baseadas no Windows, e [tamanhos para máquinas virtuais Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para VMs baseadas em Linux.

### <a name="azure-netapp-files-volume-sizing"></a>Dimensionamento de volume de arquivos NetApp do Azure

Certifique-se de que você escolha o serviço correto nível e o volume de tamanho da cota para o nível de desempenho esperados. Ver [níveis de serviço arquivos do Azure NetApp](azure-netapp-files-service-levels.md) para obter mais informações.

### <a name="virtual-network-vnet-recommendations"></a>Recomendações de rede virtual (VNet)

Você deve executar o teste na mesma rede virtual como arquivos do Azure NetApp de desempenho. O exemplo a seguir demonstra a recomendação:

![Recomendações de rede virtual](../media/azure-netapp-files/azure-netapp-files-benchmark-testing-vnet.png)

## <a name="installation-of-fio"></a>Instalação de FIO

FIO está disponível em formato binário para Linux e Windows. Siga a seção pacotes binário [FIO](https://github.com/axboe/fio) instalar para a plataforma de sua escolha.

## <a name="fio-examples-for-iops"></a>Exemplos de FIO de IOPS 

Os exemplos FIO nesta seção usam a seguinte configuração:
* Tamanho da instância VM: D32s_v3
* Nível de serviço do pool de capacidade e o tamanho: Premium / 50 TiB
* Tamanho da cota do volume: 48 TiB

Os exemplos a seguir mostram o FIO aleatório leituras e gravações.

### <a name="fio-8k-block-size-100-random-reads"></a>FIO: leituras aleatórias de 100% de tamanho de bloco de 8k

`fio --name=8krandomreads --rw=randread --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128 --size=4G --runtime=600 --group_reporting`

### <a name="output-68k-read-iops-displayed"></a>Saída: ler de 68 mil IOPS exibido

`Starting 4 processes`  
`Jobs: 4 (f=4): [r(4)][84.4%][r=537MiB/s,w=0KiB/s][r=68.8k,w=0 IOPS][eta 00m:05s]`

### <a name="fio-8k-block-size-100-random-writes"></a>FIO: gravações aleatórias de 100% de tamanho de bloco de 8k

`fio --name=8krandomwrites --rw=randwrite --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-73k-write-iops-displayed"></a>Saída: exibido de IOPS de gravação de 73k

`Starting 4 processes`  
`Jobs: 4 (f=4): [w(4)][26.7%][r=0KiB/s,w=571MiB/s][r=0,w=73.0k IOPS][eta 00m:22s]`

## <a name="fio-examples-for-bandwidth"></a>Exemplos de FIO de largura de banda

Os exemplos nesta apresentação seção o FIO sequencial leituras e gravações.

### <a name="fio-64k-block-size-100-sequential-reads"></a>FIO: Leituras sequenciais de 100% de tamanho de bloco de 64k

`fio --name=64kseqreads --rw=read --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-118-gbits-throughput-displayed"></a>Saída: 11.8 taxa de transferência Gbit/s exibida

`Starting 4 processes`  
`Jobs: 4 (f=4): [R(4)][40.0%][r=1313MiB/s,w=0KiB/s][r=21.0k,w=0 IOPS][eta 00m:09s]`

### <a name="fio-64k-block-size-100-sequential-writes"></a>FIO: gravações sequenciais de 100% de tamanho de bloco de 64k

`fio --name=64kseqwrites --rw=write --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-122-gbits-throughput-displayed"></a>Saída: 12.2 taxa de transferência Gbit/s exibida

`Starting 4 processes`  
`Jobs: 4 (f=4): [W(4)][85.7%][r=0KiB/s,w=1356MiB/s][r=0,w=21.7k IOPS][eta 00m:02s]`

## <a name="volume-metrics"></a>Métricas de volume

Dados de desempenho de arquivos NetApp do Azure estão disponíveis por meio de contadores do Monitor do Azure. Os contadores estão disponíveis por meio do portal do Azure e as solicitações GET de API REST. 

Você pode exibir dados históricos para as seguintes informações:
* Latência média de leitura 
* Latência média de gravação 
* IOPS de leitura (em média)
* (Em média) de IOPS de gravação
* Tamanho de volume lógico (em média)
* Tamanho do instantâneo de volume (em média)

### <a name="using-azure-monitor"></a>Como usar o Azure Monitor 

Você pode acessar os contadores de arquivos do Azure NetApp em uma base por volume na página de métricas, conforme mostrado abaixo:

![Métricas do Azure Monitor](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-metrics.png)

Você também pode criar um painel no Azure Monitor para arquivos do Azure NetApp indo para a página de métricas, filtrando para NetApp e especificando os contadores de volume de interesse: 

![Painel do Azure Monitor](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-dashboard.png)

### <a name="azure-monitor-api-access"></a>Acesso de API do Monitor do Azure

Você pode acessar os contadores de arquivos do Azure NetApp usando chamadas à API REST. Consulte [métricas com suporte no Azure Monitor: Microsoft.NetApp/netAppAccounts/capacityPools/Volumes](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftnetappnetappaccountscapacitypoolsvolumes) para os contadores para pools de capacidade e volumes.

O exemplo a seguir mostra um obter a URL para exibir o tamanho do volume lógico:

`#get ANF volume usage`  
`curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/ANFACCOUNTGOESHERE/capacityPools/ANFPOOLGOESHERE/Volumes/ANFVOLUMEGOESHERE/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=VolumeLogicalSize`


## <a name="next-steps"></a>Próximas etapas

- [Níveis de serviço do Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Avaliações de desempenho do arquivos do Azure NetApp](azure-netapp-files-performance-benchmarks.md)
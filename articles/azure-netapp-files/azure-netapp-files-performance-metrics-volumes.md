---
title: Testes de benchmark de desempenho recomendados-Azure NetApp Files
description: Saiba mais sobre as recomendações de teste de benchmark para desempenho e métricas de volume usando Azure NetApp Files.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 08/07/2019
ms.openlocfilehash: f73091552a78760024189b173897913edca724bb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100593405"
---
# <a name="performance-benchmark-test-recommendations-for-azure-netapp-files"></a>Recomendações do teste de parâmetros de comparação de desempenho para o Azure NetApp Files

Este artigo fornece recomendações de teste de benchmark para desempenho e métricas de volume usando Azure NetApp Files.

## <a name="overview"></a>Visão geral

Para entender as características de desempenho de um volume Azure NetApp Files, você pode usar a ferramenta de código-fonte aberto [fio](https://github.com/axboe/fio) para executar uma série de benchmarks para simular uma variedade de cargas de trabalho. O FIO pode ser instalado em sistemas operacionais baseados em Linux e Windows.  É uma excelente ferramenta para obter um instantâneo rápido de IOPS e taxa de transferência para um volume.

### <a name="vm-instance-sizing"></a>Dimensionamento da instância de VM

Para obter melhores resultados, verifique se você está usando uma instância de máquina virtual (VM) que é dimensionada adequadamente para executar os testes. Os exemplos a seguir usam uma instância de Standard_D32s_v3. Para obter mais informações sobre tamanhos de instância de VM, consulte [tamanhos de máquinas virtuais do Windows no Azure](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para VMs baseadas no Windows e [tamanhos para máquinas virtuais do Linux no Azure](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para VMs baseadas em Linux.

### <a name="azure-netapp-files-volume-sizing"></a>Azure NetApp Filesndo o dimensionamento de volume

Certifique-se de escolher o nível de serviço e o tamanho da cota de volume corretos para o nível de desempenho esperado. Consulte [níveis de serviço para Azure NetApp files](azure-netapp-files-service-levels.md) para obter mais informações.

### <a name="virtual-network-vnet-recommendations"></a>Recomendações de rede virtual (VNet)

Você deve executar o teste de parâmetro de comparação na mesma VNet que Azure NetApp Files. O exemplo a seguir demonstra a recomendação:

![Recomendações de VNet](../media/azure-netapp-files/azure-netapp-files-benchmark-testing-vnet.png)

## <a name="installation-of-fio"></a>Instalação do FIO

O FIO está disponível em formato binário para Linux e Windows. Siga a seção de pacotes binários em [fio](https://github.com/axboe/fio) para instalar para a plataforma de sua escolha.

## <a name="fio-examples-for-iops"></a>Exemplos de FIO para IOPS 

Os exemplos de FIO nesta seção usam a seguinte configuração:
* Tamanho da instância de VM: D32s_v3
* Nível de serviço e tamanho do pool de capacidade: Premium/50 TiB
* Tamanho da cota de volume: 48 TiB

Os exemplos a seguir mostram as leituras e gravações aleatórias do FIO.

### <a name="fio-8k-block-size-100-random-reads"></a>FIO: tamanho do bloco de 8K 100% de leituras aleatórias

`fio --name=8krandomreads --rw=randread --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128 --size=4G --runtime=600 --group_reporting`

### <a name="output-68k-read-iops-displayed"></a>Saída: IOPS de leitura de 68k exibido

`Starting 4 processes`  
`Jobs: 4 (f=4): [r(4)][84.4%][r=537MiB/s,w=0KiB/s][r=68.8k,w=0 IOPS][eta 00m:05s]`

### <a name="fio-8k-block-size-100-random-writes"></a>FIO: tamanho do bloco de 8K 100% de gravações aleatórias

`fio --name=8krandomwrites --rw=randwrite --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-73k-write-iops-displayed"></a>Saída: IOPS de gravação de 73k exibido

`Starting 4 processes`  
`Jobs: 4 (f=4): [w(4)][26.7%][r=0KiB/s,w=571MiB/s][r=0,w=73.0k IOPS][eta 00m:22s]`

## <a name="fio-examples-for-bandwidth"></a>Exemplos de FIO de largura de banda

Os exemplos nesta seção mostram as leituras e gravações sequenciais do FIO.

### <a name="fio-64k-block-size-100-sequential-reads"></a>FIO: tamanho do bloco de 64K 100% de leituras sequenciais

`fio --name=64kseqreads --rw=read --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-118-gbits-throughput-displayed"></a>Saída: taxa de transferência de 11,8 Gbit/s exibida

`Starting 4 processes`  
`Jobs: 4 (f=4): [R(4)][40.0%][r=1313MiB/s,w=0KiB/s][r=21.0k,w=0 IOPS][eta 00m:09s]`

### <a name="fio-64k-block-size-100-sequential-writes"></a>FIO: tamanho do bloco de 64K 100% de gravações sequenciais

`fio --name=64kseqwrites --rw=write --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-122-gbits-throughput-displayed"></a>Saída: taxa de transferência de 12,2 Gbit/s exibida

`Starting 4 processes`  
`Jobs: 4 (f=4): [W(4)][85.7%][r=0KiB/s,w=1356MiB/s][r=0,w=21.7k IOPS][eta 00m:02s]`

## <a name="volume-metrics"></a>Métricas de volume

Azure NetApp Files dados de desempenho estão disponíveis por meio de contadores de Azure Monitor. Os contadores estão disponíveis por meio do portal do Azure e das solicitações GET da API REST. 

Você pode exibir dados históricos para as seguintes informações:
* Latência média de leitura 
* Latência média de gravação 
* IOPS de leitura (média)
* IOPS de gravação (média)
* Tamanho lógico do volume (média)
* Tamanho do instantâneo de volume (média)

### <a name="using-azure-monitor"></a>Como usar o Azure Monitor 

Você pode acessar contadores de Azure NetApp Files por volume na página de métricas, conforme mostrado abaixo:

![Métricas do Azure Monitor](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-metrics.png)

Você também pode criar um painel em Azure Monitor para Azure NetApp Files acessando a página métricas, filtrando o NetApp e especificando os contadores de volume de interesse: 

![Painel do Azure Monitor](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-dashboard.png)

### <a name="azure-monitor-api-access"></a>Acesso Azure Monitor API

Você pode acessar contadores de Azure NetApp Files usando chamadas à API REST. Consulte [métricas com suporte com Azure monitor: Microsoft. NetApp/netAppAccounts/capacityPools/volumes](../azure-monitor/essentials/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) para contadores para pools de capacidade e volumes.

O exemplo a seguir mostra uma URL GET para exibição do tamanho do volume lógico:

`#get ANF volume usage`  
`curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/ANFACCOUNTGOESHERE/capacityPools/ANFPOOLGOESHERE/Volumes/ANFVOLUMEGOESHERE/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=VolumeLogicalSize`


## <a name="next-steps"></a>Próximas etapas

- [Níveis de serviço do Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Parâmetros de comparação de desempenho para o Linux](performance-benchmarks-linux.md)
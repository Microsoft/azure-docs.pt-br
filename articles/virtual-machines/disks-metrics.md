---
title: Métricas de disco
description: Exemplos de métricas de intermitência de disco
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 60486c41ad843cf193ee0648dfcfef66f7668e47
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101674975"
---
# <a name="disk-performance-metrics"></a>Métricas de desempenho de disco
O Azure oferece métricas no portal do Azure que fornecem informações sobre como as máquinas virtuais (VM) e os discos são executados. As métricas também podem ser recuperadas por meio de uma chamada à API. Este artigo é dividido em três subseções:

- **Métricas de e/s de disco, produtividade e profundidade de fila** -essas métricas permitem que você veja o desempenho do armazenamento da perspectiva de um disco e de uma máquina virtual.
- **Métricas de intermitência de disco** -essas são as métricas fornecem a observação sobre nosso recurso de [disparo](disk-bursting.md) em nossos discos Premium.
- **Métricas de utilização de e/s de armazenamento** -essas métricas ajudam a diagnosticar afunilamentos em seu desempenho de armazenamento com discos. 

Todas as métricas são emitidas a cada minuto, exceto pela métrica de porcentagem de crédito de intermitência, emitida a cada 5 minutos.

## <a name="disk-io-throughput-and-queue-depth-metrics"></a>Métricas de e/s de disco, produtividade e profundidade de fila
As métricas a seguir estão disponíveis para obter informações sobre a VM e a e/s de disco, taxa de transferência e desempenho de profundidade de fila:

- **Profundidade da fila de disco do so**: o número de solicitações de e/s pendentes atuais que estão aguardando para serem lidas ou gravadas no disco do sistema operacional.
- **Bytes de leitura do disco do so/s**: o número de bytes lidos em um segundo do disco do sistema operacional.
- **Operações de leitura de disco do so/s**: o número de operações de entrada que são lidas em um segundo do disco do sistema operacional.
- **Bytes de gravação de disco do sistema operacional/s**: o número de bytes gravados em um segundo do disco do sistema operacional.
- **Operações de gravação de disco do so/s**: o número de operações de saída que são gravadas em um segundo do disco do sistema operacional.
- **Profundidade da fila do disco de dados**: o número de solicitações de e/s pendentes atuais que estão aguardando para serem lidas ou gravadas nos discos de dados.
- **Bytes de leitura do disco de dados/s**: o número de bytes lidos em um segundo dos discos de dados.
- **Operações de leitura de disco de dados/s**: o número de operações de entrada que são lidas em um segundo de discos de dados.
- **Bytes de gravação do disco de dados/s**: o número de bytes gravados em um segundo dos discos de dados.
- **Operações de gravação do disco de dados/s**: o número de operações de saída que são gravadas em um segundo a partir de disco (s) de dados.
- **Bytes de leitura de disco/s**: o número total de bytes lidos em um segundo de todos os discos anexados a uma VM.
- **Operações de leitura de disco/s**: o número de operações de entrada que são lidas em um segundo de todos os discos anexados a uma VM.
- **Bytes de gravação de disco/s**: o número de bytes gravados em um segundo de todos os discos anexados a uma VM.
- **Operações de gravação de disco/s**: o número de operações de saída que são gravadas em um segundo de todos os discos anexados a uma VM.

## <a name="bursting-metrics"></a>Métricas de intermitência
As métricas a seguir ajudam a observar a observação em nosso recurso de [disparo](disk-bursting.md) em nossos discos Premium:

- **Largura de banda máxima de intermitência do disco de dados**: o limite de taxa de transferência para o qual os discos de dados podem se estourar.
- **Largura de banda máxima de intermitência do disco do so**: o limite de taxa de transferência que o disco do sistema operacional pode aumentar até.
- **IOPS de intermitência máxima do disco de dados**: o limite de IOPS para o qual os discos de dados podem se estourar.
- **IOPS de intermitência máxima do disco do so**: o limite de IOPS para o qual o disco do sistema operacional pode ser intermitente.
- **Largura de banda de destino do disco de dados**: o limite de taxa de transferência que o disco de dados pode atingir sem intermitência.
- **Largura de banda de destino do disco do so**: o limite de taxa de transferência que o disco do sistema operacional pode atingir sem intermitência.
- **IOPS de destino do disco de dados**: o limite de IOPS que os discos de dados podem alcançar sem intermitência.
- **IOPS de destino de disco do so**: o limite de IOPS que os discos de dados podem alcançar sem intermitência.
- **Percentual de créditos de intermitência de bps usados no disco de dados**: a porcentagem acumulada da intermitência de taxa de transferência usada para os discos de dados. Emitido em um intervalo de 5 minutos.
- **Percentual de créditos de disparo de bps do disco do sistema operacional**: a porcentagem acumulada da intermitência de taxa de transferência usada para o disco do sistema operacional. Emitido em um intervalo de 5 minutos.
- **Porcentagem de créditos de e/s intermitentes do disco de dados usada**: a porcentagem acumulada da intermitência de IOPS usada para os discos de dados. Emitido em um intervalo de 5 minutos.
- **Percentual de créditos de e/s de disco do so usados**: a porcentagem acumulada da intermitência de IOPS usada para o disco do sistema operacional. Emitido em um intervalo de 5 minutos.

## <a name="storage-io-utilization-metrics"></a>Métricas de utilização de e/s de armazenamento
As métricas a seguir ajudam a diagnosticar afunilamento na sua combinação de disco e máquina virtual. Essas métricas só estão disponíveis ao usar a VM habilitada Premium. Essas métricas estão disponíveis para todos os tipos de disco, exceto para ultra. 

Métricas que ajudam a diagnosticar a e/s de disco com limitação:

- **Percentual de IOPS consumido de disco de dados**: a porcentagem calculada pelo IOPS de disco de dados foi concluída sobre o IOPS de disco de dados provisionado. Se esse valor for em 100%, seu aplicativo em execução será e/s limitada do limite de IOPS do disco de dados.
- **Porcentagem consumida da largura de banda do disco de dados**: a porcentagem calculada pela taxa de transferência do disco de dados foi concluída na taxa de transferência do disco de dados provisionado Se esse valor for em 100%, seu aplicativo em execução será e/s limitada do limite de largura de banda do disco de dados.
- **Porcentagem consumida de IOPS de disco do so**: a porcentagem calculada pelo IOPS de disco do sistema operacional concluída em relação à IOPS de disco do sistema operacional provisionado. Se esse valor for em 100%, seu aplicativo em execução será e/s limitada do limite de IOPS do disco do sistema operacional.
- **Porcentagem consumida da largura de banda do disco do so**: a porcentagem calculada pela taxa de transferência do disco do so concluída sobre a taxa de transferência do disco do so provisionado Se esse valor for em 100%, seu aplicativo em execução será e/s limitada do limite de largura de banda do disco do sistema operacional.

Métricas que ajudam a diagnosticar a e/s de VM com limitação:

- **Porcentagem consumida de IOPS em cache da VM**: a porcentagem calculada pelo total de IOPS concluída em relação ao limite de IOPS máximo em cache da máquina virtual. Se esse valor for em 100%, seu aplicativo em execução será e/s limitada do limite de IOPS em cache da VM.
- **Porcentagem consumida da largura de banda em cache da VM**: a porcentagem calculada pela taxa de transferência total do disco concluída na taxa de transferência máxima de máquina virtual em cache. Se esse valor for em 100%, seu aplicativo em execução será e/s limitada do limite de largura de banda em cache da VM.
- **Percentual de IOPS consumido de VM não armazenado em cache**: a porcentagem calculada pelo IOPS total em uma máquina virtual foi concluída com o limite máximo de IOPS de máquina virtual sem cache. Se esse valor for em 100%, seu aplicativo em execução será e/s limitada do limite de IOPS sem cache da sua VM.
- **Porcentagem consumida da largura de banda não armazenada em cache da VM**: a porcentagem calculada pela taxa de transferência total do disco em uma máquina virtual foi concluída com a taxa de transferência máxima da máquina virtual provisionada. Se esse valor for em 100%, seu aplicativo em execução será e/s limitada do limite de largura de banda sem cache da sua VM.

## <a name="storage-io-metrics-example"></a>Exemplo de métrica de e/s de armazenamento

Vamos executar um exemplo de como usar essas novas métricas de utilização de e/s de armazenamento para nos ajudar a depurar onde há um afunilamento em nosso sistema. A configuração do sistema é igual ao exemplo anterior, exceto que, desta vez, o disco do sistema operacional anexado *não* é armazenado em cache.

**Instalação**

- Standard_D8s_v3
  - IOPS em cache: 16.000
  - IOPS não armazenado em cache: 12.800
- Disco do sistema operacional p30
  - IOPS: 5.000
  - Cache de host: **desabilitado**
- Dois discos de dados p30 × 2
  - IOPS: 5.000
  - Cache de host: **leitura/gravação**
- Dois discos de dados p30 × 2
  - IOPS: 5.000
  - Cache de host: **desabilitado**

Vamos executar um teste de benchmark nessa máquina virtual e na combinação de disco que cria a atividade de e/s. Para saber como avaliar o parâmetro de e/s de armazenamento no Azure, confira [avaliar seu aplicativo no armazenamento em disco do Azure](disks-benchmarks.md). Na ferramenta de benchmarking, você pode ver que a combinação de VM e disco pode atingir 22.800 IOPS:

![Captura de tela da saída de f i o mostrando r = 22.8 k realçado.](media/disks-metrics/utilization-metrics-example/fio-output.jpg)



O Standard_D8s_v3 pode alcançar um total de 28.600 IOPS. Usando as métricas, vamos investigar o que está acontecendo e identificar nosso afunilamento de e/s de armazenamento. No painel esquerdo, selecione **métricas**:

![Captura de tela mostrando métricas realçadas no painel esquerdo.](media/disks-metrics/utilization-metrics-example/metrics-menu.jpg)

Primeiro, vamos dar uma olhada em nossa métrica **percentual consumida de IOPS em cache de VM** :

![Captura de tela mostrando a porcentagem consumida de V M em cache s t.](media/disks-metrics/utilization-metrics-example/vm-cached.jpg)

Essa métrica nos informa que 61% dos 16.000 IOPS alocados para o IOPS armazenado em cache na VM estão sendo usados. Essa porcentagem significa que o afunilamento de e/s de armazenamento não está com os discos armazenados em cache porque não está em 100%. Agora, vamos examinar nossa métrica de **percentual consumida de IOPS sem cache de VM** :

![Captura de tela mostrando a porcentagem consumida de V M não armazenado em cache.](media/disks-metrics/utilization-metrics-example/vm-uncached.jpg)

Esta métrica está em 100%. Ele nos informa que todos os 12.800 IOPS alocados para o IOPS não armazenado em cache na VM estão sendo usados. Uma maneira de corrigir esse problema é alterar o tamanho de nossa VM para um tamanho maior que possa lidar com a e/s adicional. Mas antes de fazermos isso, vamos examinar o disco anexado para descobrir quantos IOPS eles estão vendo. Verifique o disco do sistema operacional examinando a **porcentagem consumida de IOPS de disco do so**:

![Captura de tela mostrando O percentual de e/s do disco consumido.](media/disks-metrics/utilization-metrics-example/os-disk.jpg)

Essa métrica nos informa que cerca de 90% dos 5.000 IOPS provisionados para esse disco de so p30 está sendo usado. Essa porcentagem significa que não há nenhum afunilamento no disco do sistema operacional. Agora, vamos verificar os discos de dados que estão anexados à VM examinando a **porcentagem consumida IOPS do disco de dados**:

![Captura de tela mostrando porcentagem de e/s de disco de dados consumida.](media/disks-metrics/utilization-metrics-example/data-disks-no-splitting.jpg)

Essa métrica nos informa que a porcentagem média de IOPS consumidas em todos os discos anexados está em cerca de 42%. Esse percentual é calculado com base no IOPS usado pelos discos e não está sendo servido pelo cache do host. Vamos aprofundar essa métrica aplicando a *divisão* nessas métricas e dividindo pelo valor de LUN:

![Captura de tela mostrando porcentagem de e/s de disco de dados consumida com divisão.](media/disks-metrics/utilization-metrics-example/data-disks-splitting.jpg)

Essa métrica nos informa que os discos de dados anexados no LUN 3 e 2 estão usando cerca de 85% de seus IOPS provisionados. Aqui está um diagrama de como a e/s se parece da arquitetura de VM e de discos:

![Diagrama de exemplo de métricas de e/s de armazenamento.](media/disks-metrics/utilization-metrics-example/metrics-diagram.jpg)

## <a name="next-steps"></a>Próximas etapas

- [Visão geral das métricas de Azure Monitor](../azure-monitor/essentials/data-platform-metrics.md)
- [Agregação de métricas explicada](../azure-monitor/essentials/metrics-aggregation-explained.md)
- [Criar, exibir e gerenciar alertas de métrica usando o Azure Monitor](../azure-monitor/alerts/alerts-metric.md)
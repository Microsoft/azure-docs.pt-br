---
title: Configurações de nó com suporte do Azure HDInsight
description: Conheça as configurações mínimas e recomendadas para nós de cluster do HDInsight.
keywords: tamanhos de VM, tamanhos de cluster, configuração de cluster
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 05/14/2020
ms.openlocfilehash: d41ee2554d30a56bc2e025bbe2c93aee143d75e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98931646"
---
# <a name="what-are-the-default-and-recommended-node-configurations-for-azure-hdinsight"></a>Quais são as configurações de nó padrão e recomendadas do Azure HDInsight?

Este artigo discute as configurações de nó padrão e recomendadas para clusters do Azure HDInsight.

## <a name="default-and-minimum-recommended-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Configurações de nó e tamanhos de máquina virtual padrão e mínimos recomendados para clusters

As tabelas a seguir listam os tamanhos de VM (máquina virtual) padrão e recomendados para clusters do HDInsight.  Essas informações são necessárias para entender quais tamanhos de VM usar durante a criação de scripts do PowerShell ou da CLI do Azure para implantar clusters do HDInsight.

Se você precisa de mais de 32 nós de trabalho em um cluster, selecione um tamanho de nó de cabeçalho com pelo menos 8 núcleos e 14 GB de RAM.

Os únicos tipos de cluster que têm discos de dados são os clusters Kafka e HBase com o recurso de Gravações Aceleradas habilitado. O HDInsight dá suporte aos tamanhos de disco P30 e S30 nesses cenários. Para todos os outros tipos de cluster, o HDInsight fornece o espaço em disco gerenciado com o cluster. A partir de 11/07/2019, o tamanho do disco gerenciado de cada nó no cluster recém-criado é de 128 GB. Isso não pode ser alterado.

As especificações de todos os tipos de VM mínimos recomendados usados neste documento são resumidas na tabela a seguir.

| Tamanho              | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Taxa de transferência máxima de armazenamento temporário: IOPS / MBps de leitura / MBps de gravação | Discos de dados máximos / taxa de transferência: IOPS | Máximo de NICs/Largura de banda de rede esperado (Mbps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D3_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 16             / 16x500           | 4 / 3000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 32            / 32x500           | 8 / 6000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48000 / 750 / 375                                          | 64             / 64x500           | 8 / 12000                                    |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16 x 500                         | 4 / 3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32 x 500                       | 8 / 6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000          |
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2 / 2 x 500               | 2 / 250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4 / 4 x 500               | 2 / 500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8 / 8 x 500               | 4 / 1000                     |

Para obter mais informações sobre as especificações de cada tipo de VM, confira os seguintes documentos:

* [Tamanhos das máquinas virtuais de uso geral: Série Dv2 1-5](../virtual-machines/dv2-dsv2-series.md)
* [Tamanhos de máquina virtual otimizada para memória: Série Dv2 11-15](../virtual-machines/dv2-dsv2-series-memory.md)
* [Tamanhos das máquinas virtuais de uso geral: Série Av2 1-8](../virtual-machines/av2-series.md)

### <a name="all-supported-regions-except-brazil-south-and-japan-west"></a>Todas as regiões com suporte, exceto sul do Brasil e oeste do Japão

> [!Note]
> Para obter o identificador de SKU para uso no PowerShell e outros scripts, adicione `Standard_` ao início de todas as SKUs de VM nas tabelas abaixo. Por exemplo, `D12_v2` se tornaria `Standard_D12_v2`.

| Tipo de cluster | O Hadoop | HBase | Consulta Interativa | Storm | Spark | ML Server | Kafka |
|---|---|---|---|---|---|---|---|
| Cabeçalho: tamanho padrão da VM | D12_v2 | D12_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2* | D12_v2 | D3_v2 |
| Cabeçalho: tamanhos de VM mínimos recomendados | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2* | D12_v2 | D3_v2 |
| Trabalho: tamanho de VM padrão | D4_v2 | D4_v2 | D14_v2 | D3_v2 | D13_v2 | D4_v2 | 4 D12_v2 com 2 discos S30 por agente |
| Trabalho: tamanhos de VM mínimos recomendados | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 | D3_v2 |
| Zookeeper: tamanho de VM padrão |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 | A4_v2 |
| ZooKeeper: tamanhos de VM mínimos recomendados |  | A4_v2 | A4_v2 | A2_v2 |  | A2_v2 | A4_v2 |
| Serviços de ML: tamanho de VM padrão |  |  |  |  |  | D4_v2 |  |
| Serviços de ML: tamanhos de VM mínimos recomendados |  |  |  |  |  | D4_v2 |  |

\* = Tamanhos de VM para clusters do ESP (Spark Enterprise Security Package)

### <a name="brazil-south-and-japan-west-only"></a>Apenas sul do Brasil e oeste do Japão

| Tipo de cluster | O Hadoop | HBase | Consulta Interativa | Storm | Spark | ML Services |
|---|---|---|---|---|---|---|
| Cabeçalho: tamanho padrão da VM | D12 | D12 | D13 | A4_v2 | D12 | D12 |
| Cabeçalho: tamanhos de VM mínimos recomendados | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2 | D12_v2 |
| Trabalho: tamanho de VM padrão | D4 | D4 | D14 | D3 | D13 | D4 |
| Trabalho: tamanhos de VM mínimos recomendados | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 |
| Zookeeper: tamanho de VM padrão |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ZooKeeper: tamanhos de VM mínimos recomendados |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| Serviços de ML: tamanhos de VM padrão |  |  |  |  |  | D4 |
| Serviços de ML: tamanhos de VM mínimos recomendados |  |  |  |  |  | D4_v2 |

> [!NOTE]
> - O cabeçalho é conhecido como *Nimbus* para o tipo de cluster Storm.
> - A função de trabalho é conhecida como *Supervisor* para o tipo de cluster Storm.
> - A função de trabalho é conhecida como *Região* para o tipo de cluster HBase.

## <a name="next-steps"></a>Próximas etapas

* [Quais são os componentes e versões do Apache Hadoop disponíveis com o HDInsight?](hdinsight-component-versioning.md)

---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: ad21dbb356a6630cec0a6f5658b20cb8568890f9
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171882"
---
<!-- Not used for Ls-series -->

## <a name="size-table-definitions"></a>Definições da tabela de tamanhos

- A capacidade de armazenamento é mostrada em unidades de GiB ou de 1024^3 bytes. Ao comparar os discos medidos em GB (1000^3 bytes) com os discos medidos em GiB (1024^3), lembre-se de que os números de capacidade fornecidos em GiB poderão parecer menores. Por exemplo, 1023 GiB = 1098,4 GB
- A taxa de transferência do disco é medida em IOPS (operações de entrada/saída por segundo) e em MBps, em que MBps = 10^6 bytes/s.
- Os discos de dados podem operar nos modos em cache ou não armazenado em cache. Para a operação do disco de dados armazenados em cache, o modo de cache do host é definido como **ReadOnly** ou **ReadWrite**.  Para as operação do disco de dados não armazenados em cache, o modo de cache do host é definido como **Nenhum**.
-   Se você quiser obter o melhor desempenho para suas VMs, será necessário limitar o número de discos de dados a 2 discos por vCPU.
- **Largura de banda de rede esperada** é a [largura de banda agregada máxima alocada por tipo de VM](../articles/virtual-network/virtual-machine-network-throughput.md) em todas as NICs para todos os destinos. Os limites superiores não são garantidos, mas devem fornecer diretrizes para selecionar o tipo de VM correto para o aplicativo desejado. O desempenho real da rede dependerá de uma variedade de fatores, incluindo cargas de rede e aplicativos, bem como configurações de rede. Para saber mais sobre como otimizar a taxa de transferência de rede, veja [Otimização da taxa de transferência de rede para Windows e Linux](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md). Para obter o desempenho de rede esperado no Linux ou no Windows, poderá necessário selecionar uma versão específica ou otimizar sua VM. Para saber mais, veja [Como testar a taxa de transferência da máquina virtual de forma confiável](../articles/virtual-network/virtual-network-bandwidth-testing.md).




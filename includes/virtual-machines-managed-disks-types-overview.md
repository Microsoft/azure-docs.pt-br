---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6eae536bd19a2c0e5707d8e0b379774b6eb2707a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60618008"
---
# <a name="what-disk-types-are-available-in-azure"></a>Quais tipos de disco estão disponíveis no Azure?

Atualmente, os discos gerenciados do Azure oferecem quatro tipos de disco, três dos quais estão em GA (disponibilidade geral) e outro que está atualmente em versão prévia. Esses quatro tipos de disco têm seus próprios cenários de cliente de destino.

## <a name="disk-comparison"></a>Comparação de disco

A tabela a seguir oferece uma comparação entre discos ultra SSD (unidade de estado sólido) (versão prévia), SSD Premium, SSD Standard e HD (unidades de disco rígido) padrão de discos gerenciados, para ajudá-lo a decidir o que usar.

|   | SSD Ultra (versão prévia)   | SSD Premium   | SSD Standard   | HDD Standard   |
|---------|---------|---------|---------|---------|
|Tipo de disco   |SSD   |SSD   |SSD   |HDD   |
|Cenário   |Cargas de trabalho de E/S intensiva, como SAP HANA, bancos de dados de camada superior (por exemplo, SQL, Oracle) e outras cargas de trabalho de transações pesadas.   |Cargas de trabalho confidenciais produção e desempenho   |Servidores Web, aplicativos empresariais pouco usados e desenvolvimento/teste   |Backup, não crítico, acesso não frequente   |
|Tamanho do disco   |65.536 GiB (GibiByte) (versão prévia)   |32,767 GiB    |32,767 GiB   |32,767 GiB   |
|Taxa de transferência máxima   |2.000 MiB/s (versão prévia)   |900 MiB/s   |750 MiB/s   |500 MiB/s   |
|IOPS Máxima   |160.000 (versão prévia)   |20.000   |6.000   |2.000   |

## <a name="ultra-ssd-preview"></a>SSD Ultra (versão prévia)

O SSD Ultra do Azure (versão prévia) proporciona alta taxa de transferência, alta IOPS e armazenamento em disco de baixa latência consistente para VMs de IaaS do Azure. Alguns benefícios adicionais do SSD Ultra incluem a capacidade de alterar dinamicamente o desempenho do disco, juntamente com suas cargas de trabalho, sem a necessidade de reiniciar as máquinas virtuais. Os SSDs Ultra são adequados para cargas de trabalho de grande volume de dados, como SAP HANA, bancos de dados de camada superior e cargas de trabalho de transações pesadas. Os SSDs Ultra só podem ser usados como discos de dados. É recomendável usar SSDs Premium como discos de sistema operacional.

### <a name="performance"></a>Desempenho

Ao provisionar um disco Ultra, você pode configurar a capacidade e o desempenho do disco de forma independente. Os SSDs Ultra vêm em vários tamanhos fixos, de 4 GiB até 64 TiB e oferecem um modelo de configuração de desempenho flexível que permite que você configure IOPS e taxa de transferência de modo independente.

Alguns recursos importantes do SSD Ultra são:

- Capacidade do disco: A capacidade do SSD Ultra varia de 4 GiB até 64 TiB.
- IOPS do disco: O SSD Ultra é compatível com limites de IOPS de 300 IOPS/GiB, até um máximo de 160 mil IOPS por disco. Para alcançar o IOPS que você provisionou, certifique-se de que o IOPS do disco selecionado seja menor que o IOPS da VM. O IOPS de disco mínimo é de 100 IOPS.
- Taxa de transferência do disco: com o SSD Ultra, o limite de taxa de transferência de um único disco é de 256 KiB/s para cada IOPS provisionado, até um máximo de 2.000 MBps por disco (em que MBps = 10^6 Bytes por segundo). A taxa de transferência mínima de disco é de 1 MiB.

### <a name="disk-size"></a>Tamanho do disco

|Tamanho do Disco (GiB)  |Limites de IOPS  |Limite de taxa de transferência (MBps)  |
|---------|---------|---------|
|4     |1.200         |300         |
|8     |2.400         |600         |
|16     |4.800         |1.200         |
|32     |9.600         |2.000         |
|64     |19.200         |2.000         |
|128     |38.400         |2.000         |
|256     |76.800         |2.000         |
|512     |80.000         |2.000         |
|1.024 a 65.536 (os tamanhos nesse intervalo aumentam em incrementos de 1 TiB)     |160.000         |2.000         |

### <a name="preview-scope-and-limitations"></a>Limitações e escopo da versão prévia

Durante a versão prévia, SSDs Ultra:

- Terão suporte no Leste dos EUA 2 em uma única zona de disponibilidade  
- Só poderão ser usados com zonas de disponibilidade (conjuntos de disponibilidade e implantações de VM única fora das zonas não terão a possibilidade de anexar um disco Ultra)
- Serão compatíveis somente com VMs ES/DS v3
- Só estarão disponíveis como discos de dados e serão compatíveis somente com tamanho de setor físico de 4k  
- Só podem ser criados como discos vazios  
- Atualmente, só podem ser implantados usando os modelos do Azure Resource Manager, a CLI e o SDK do Python.
- Não são compatíveis ainda com instantâneos de disco, imagens de VM, conjuntos de disponibilidade, conjuntos de dimensionamento de máquinas virtuais nem com a criptografia de disco do Azure.
- Não são compatíveis ainda com a integração com o Backup do Azure nem com o Azure Site Recovery.
- Assim como acontece com a [maioria das versões prévias](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), esse recurso não deve ser usado em cargas de trabalho de produção até a GA (disponibilidade geral).

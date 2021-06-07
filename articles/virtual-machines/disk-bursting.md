---
title: Intermitência de disco gerenciado
description: Saiba mais sobre a intermitência de disco para discos do Azure e máquinas virtuais do Azure.
author: albecker1
ms.author: albecker
ms.date: 03/02/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 9758b026ef205e6608f7fc4110219dc5f267369e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105568708"
---
# <a name="managed-disk-bursting"></a>Intermitência de disco gerenciado
[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting.md)]

O [SSDs Premium](disks-types.md#premium-ssd) do Azure oferece dois modelos de intermitência:

- Um modelo de intermitência sob demanda (versão prévia), em que o disco é estourado sempre que suas necessidades excedem sua capacidade atual. Esse modelo incorre em encargos adicionais sempre que o disco for rompido. A intermitência de não crédito só está disponível em discos com mais de 512 GiB de tamanho.
- Um modelo baseado em crédito, onde o disco será estourado somente se tiver créditos de intermitência acumulados em seu Bucket de crédito. Esse modelo não incorrerá em encargos adicionais quando o disco for estourado. A intermitência baseada em crédito só está disponível em discos 512 GiB e menores.

Além disso, o [nível de desempenho dos discos gerenciados pode ser alterado](disks-change-performance.md), o que pode ser ideal se sua carga de trabalho fosse executada em intermitência.

|  |Intermitência com base em crédito  |Intermitência sob demanda  |Alterando o nível de desempenho  |
|---------|---------|---------|---------|
| **Cenários**|Ideal para dimensionamento de curto prazo (30 minutos ou menos).|Ideal para dimensionamento de curto prazo (sem restrições de tempo).|Ideal se sua carga de trabalho seria continuamente executada em intermitência.|
|**Custo**     |Gratuita         |Custo é variável, consulte a seção de [cobrança](#billing) para obter detalhes.        |O custo de cada nível de desempenho é fixo, consulte [preços de Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/) para obter detalhes.         |
|**Disponibilidade**     |Disponível somente para o SSDs Premium 512 GiB e menor.         |Disponível somente para o SSDs Premium maior que 512 GiB.         |Disponível para todos os tamanhos de SSD Premium.         |
|**Habilitação**     |Habilitado por padrão em discos qualificados.         |Deve ser habilitado pelo usuário.         |O usuário deve alterar manualmente sua camada.         |

## <a name="common-scenarios"></a>Cenários comuns
Os cenários a seguir podem se beneficiar muito da intermitência:
- **Melhorar os tempos de inicialização**  – com a intermitência, sua instância será inicializada a uma taxa significativamente mais rápida. Por exemplo, o disco do sistema operacional padrão para VMs com habilitação Premium é o disco P4, que é um desempenho provisionado de até 120 IOPS e 25 MB/s. Com a intermitência, o P4 pode ir até 3500 IOPS e 170 MB/s, permitindo que a inicialização Acelere até 6 vezes.
- **Manipular trabalhos em lotes** – algumas cargas de trabalho de aplicativo são cíclicas por natureza. Eles exigem um desempenho de linha de base na maior parte do tempo e melhor desempenho por curtos períodos de tempo. Um exemplo disso é um programa de contabilidade que processa transações diárias que exigem uma pequena quantidade de tráfego de disco. No final do mês, esse programa concluiria a reconciliação de relatórios que precisam de uma quantidade muito maior de tráfego de disco.
- **Picos de tráfego** – os servidores Web e seus aplicativos podem enfrentar sobretensões de tráfego a qualquer momento. Se o seu servidor Web for apoiado por VMs ou discos que usam intermitência, os servidores seriam mais bem equipados para lidar com picos de tráfego. 

[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting-2.md)]

## <a name="next-steps"></a>Próximas etapas

Para habilitar a intermitência sob demanda, consulte [habilitar intermitência sob demanda](disks-enable-bursting.md).
Para saber como obter informações sobre os recursos de intermitência, confira [métricas de intermitência de disco](disks-metrics.md).

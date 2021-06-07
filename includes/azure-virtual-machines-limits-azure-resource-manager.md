---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: 96c476d1724f9475eb28675fc24e21192935e883
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104719635"
---
| Recurso | Limite |
| --- | --- |
| VMs por [assinatura](https://azure.microsoft.com/pricing/) |25 mil<sup>1</sup> por região. |
| Total de núcleos da VM por [assinatura](https://azure.microsoft.com/pricing/) |20<sup>1</sup> por região. Entre em contato com o Suporte para aumentar o limite. |
| Total de núcleos de VM Spot do Azure por [assinatura](https://azure.microsoft.com/pricing/) |20<sup>1</sup> por região. Entre em contato com o Suporte para aumentar o limite. |
| Núcleos por série de VM, como Dv2 e F, por [assinatura](https://azure.microsoft.com/pricing/) |20<sup>1</sup> por região. Entre em contato com o Suporte para aumentar o limite. |
| [Conjuntos de disponibilidade](../articles/virtual-machines/availability-set-overview.md) por assinatura |2\.500 por região. |
| Máquinas virtuais por conjunto de disponibilidade | 200 |
| [Grupos de posicionamento de proximidade](../articles/virtual-machines/windows/proximity-placement-groups-portal.md) por [grupo de recursos](../articles/azure-resource-manager/management/overview.md#resource-groups) | 800 |
| Certificados por conjunto de disponibilidade | 199<sup>2</sup> |
| Certificados por assinatura |Ilimitado<sup>3</sup> |

<sup>1</sup> Os limites padrão variam de acordo com o tipo de categoria de oferta, como Avaliação Gratuita e Pagamento Conforme o Uso, e por série, como Dv2, F e G. Por exemplo, o padrão de assinaturas Contrato Enterprise é 350. Por questões de segurança, as assinaturas usam como padrão 20 núcleos para impedir implantações básicas grandes. Caso você precise de mais núcleos, envie um tíquete de suporte.

<sup>2</sup> Propriedades como as chaves públicas SSH também são enviadas por push como certificados e contam para esse limite. Para ignorar esse limite, use a [extensão do Azure Key Vault para o Windows](../articles/virtual-machines/extensions/key-vault-windows.md) ou a [extensão do Azure Key Vault para Linux](../articles/virtual-machines/extensions/key-vault-linux.md) para instalar certificados.

<sup>3</sup> Com o Azure Resource Manager, os certificados são armazenados no Azure Key Vault. O número de certificados é ilimitado para uma assinatura. Há um limite de certificados de 1 MB por implantação, que consiste em uma VM ou um conjunto de disponibilidade.

> [!NOTE]
> Os núcleos de máquina virtual têm um limite total regional. Eles também têm um limite para séries regionais por tamanho, como Dv2 e F. Esses limites são aplicados separadamente. Por exemplo, considere uma assinatura com uma VM do Leste dos EUA com um limite total de núcleos de 30, um limite de núcleos da série A de 30 e um limite de núcleos da série D de 30. Esta assinatura pode implantar 30 VMs A1, 30 VMs D1 ou uma combinação das duas a fim de não exceder um total de 30 núcleos. Um exemplo de uma combinação é de 10 VMs A1 e 20 VMs D1.
> <!-- -->
>

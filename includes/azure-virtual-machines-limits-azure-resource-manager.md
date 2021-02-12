---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: 61af1f1a97e0ffad0332a34034f5dd1d007d1a5c
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900758"
---
| Recurso | Limite |
| --- | --- |
| VMs por [assinatura](https://azure.microsoft.com/pricing/) |25 mil<sup>1</sup> por região. |
| Total de núcleos da VM por [assinatura](https://azure.microsoft.com/pricing/) |20<sup>1</sup> por região. Entre em contato com o Suporte para aumentar o limite. |
| Total de núcleos de VM Spot do Azure por [assinatura](https://azure.microsoft.com/pricing/) |20<sup>1</sup> por região. Entre em contato com o Suporte para aumentar o limite. |
| Núcleos por série de VM, como Dv2 e F, por [assinatura](https://azure.microsoft.com/pricing/) |20<sup>1</sup> por região. Entre em contato com o Suporte para aumentar o limite. |
| [Conjuntos de disponibilidade](../articles/virtual-machines/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) por assinatura |2\.500 por região. |
| Máquinas virtuais por conjunto de disponibilidade | 200 |
| [Grupos de posicionamento de proximidade](../articles/virtual-machines/windows/proximity-placement-groups-portal.md) por [grupo de recursos](../articles/azure-resource-manager/management/overview.md#resource-groups) | 800 | 
| Certificados por conjunto de disponibilidade | 199<sup>2</sup> |
| Certificados por assinatura |Ilimitado<sup>3</sup> |

<sup>1</sup> Os limites padrão variam de acordo com o tipo de categoria de oferta, como Avaliação Gratuita e Pagamento Conforme o Uso, e por série, como Dv2, F e G. Por exemplo, o padrão de assinaturas Contrato Enterprise é 350.

<sup>2</sup> Propriedades como as chaves públicas SSH também são enviadas por push como certificados e contam para esse limite. Para ignorar esse limite, use a [extensão do Azure Key Vault para o Windows](../articles/virtual-machines/extensions/key-vault-windows.md) ou a [extensão do Azure Key Vault para Linux](../articles/virtual-machines/extensions/key-vault-linux.md) para instalar certificados.

<sup>3</sup> Com o Azure Resource Manager, os certificados são armazenados no Azure Key Vault. O número de certificados é ilimitado para uma assinatura. Há um limite de certificados de 1 MB por implantação, que consiste em uma VM ou um conjunto de disponibilidade.



> [!NOTE]
> Os núcleos de máquina virtual têm um limite total regional. Eles também têm um limite para séries regionais por tamanho, como Dv2 e F. Esses limites são aplicados separadamente. Por exemplo, considere uma assinatura com uma VM do Leste dos EUA com um limite total de núcleos de 30, um limite de núcleos da série A de 30 e um limite de núcleos da série D de 30. Esta assinatura pode implantar 30 VMs A1, 30 VMs D1 ou uma combinação das duas a fim de não exceder um total de 30 núcleos. Um exemplo de uma combinação é de 10 VMs A1 e 20 VMs D1.  
> <!-- -->
>
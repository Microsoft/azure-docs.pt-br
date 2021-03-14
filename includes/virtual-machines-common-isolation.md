---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: rishabv90
ms.service: virtual-machines
ms.topic: include
ms.date: 11/05/2020
ms.author: risverma
ms.custom: include file
ms.openlocfilehash: fc61c111291f9862f71f9a81828fa0fa828ab2ad
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102511098"
---
A Computação do Azure oferece tamanhos de máquina virtual Isolada, para um tipo de hardware específico e dedicada a um único cliente. Os tamanhos isolados residem e operam em uma geração de hardware específica e serão preteridos quando a geração de hardware for desativada.

Os tamanhos de máquinas virtuais isoladas são mais adequados para cargas de trabalho que exigem um alto grau de isolamento de cargas de trabalho de outros clientes por motivos que incluem requisitos de conformidade e regulamentação de atendimento.  Utilizar um tamanho isolado garante que sua máquina virtual será apenas sendo executada na instância de servidor específico. 


Além disso, como as VMs de tamanho isolado são grandes, os clientes podem optar por subdividir os recursos dessas VMs usando o [suporte do Azure para máquinas virtuais aninhadas](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).

As ofertas atuais da máquina virtual isolada incluem:
* Standard_E80ids_v4
* Standard_E80is_v4
* Standard_F72s_v2
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5


> [!NOTE]
> Os tamanhos de VM isolados têm um ciclo de vida limitado por hardware. Consulte abaixo para obter detalhes

## <a name="deprecation-of-isolated-vm-sizes"></a>Substituição de tamanhos de VM isoladas

Os tamanhos de VM isolados têm um ciclo de vida limitado por hardware. O Azure emitirá lembretes 12 meses antes da data oficial de substituição dos tamanhos e fornecerá uma oferta de isolamento atualizada para sua consideração.

| Tamanho | Data de retirada do isolamento | 
| --- | --- |
| Standard_DS15_v2 | 15 de maio de 2021 |
| Standard_D15_v2  | 15 de maio de 2021 |
| Standard_G5  | 15 de fevereiro de 2022 |
| Standard_GS5  | 15 de fevereiro de 2022 |
| Standard_E64i_v3  | 15 de fevereiro de 2022 |
| Standard_E64is_v3  | 15 de fevereiro de 2022 |


## <a name="faq"></a>Perguntas frequentes
### <a name="q-is-the-size-going-to-get-retired-or-only-its-isolation-feature"></a>P: o tamanho será desativado ou apenas seu recurso de "isolamento"?
**R**: atualmente, somente o recurso de isolamento dos tamanhos de VM está sendo desativado. Os tamanhos isolados preteridos continuarão a existir no estado não isolado. Se o isolamento não for necessário, não haverá nenhuma ação a ser tomada e a VM continuará funcionando conforme o esperado.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>P: há um tempo de inatividade quando minha VM chega em um hardware não isolado?
**R**: se não houver necessidade de isolamento, nenhuma ação será necessária e não haverá nenhum tempo de inatividade. Ao contrário se o isolamento for necessário, nosso comunicado incluirá o tamanho de substituição recomendado. A seleção do tamanho de substituição exigirá que nossos clientes redimensionem suas VMs.  

### <a name="q-is-there-any-cost-delta-for-moving-to-a-non-isolated-virtual-machine"></a>P: há algum Delta de custo para mover para uma máquina virtual não isolada?
**R**: não

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>P: quando os outros tamanhos isolados serão desativados?
**R**: forneceremos lembretes 12 meses antes da reprovação oficial do tamanho isolado. Nosso anúncio mais recente inclui desativação de recursos de isolamento de Standard_G5, Standard_GS5, Standard_E64i_v3 e Standard_E64i_v3.  

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>P: sou um cliente do Azure Service Fabric contando com as camadas de durabilidade prata ou ouro. Essa alteração me afeta?
**R**: não. As garantias fornecidas pelas camadas de [durabilidade](../articles/service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) de Service Fabric continuarão a funcionar mesmo após essa alteração. Se você precisar de isolamento de hardware físico por outros motivos, talvez ainda precise executar uma das ações descritas acima. 
 
### <a name="q-what-are-the-milestones-for-d15_v2-or-ds15_v2-isolation-retirement"></a>P: quais são as etapas para D15_v2 ou desativação de isolamento de DS15_v2? 
**A**: 
 
| Data | Ação |
|---|---| 
| 15 de maio de 2020<sup>1</sup> | Aviso de desativação de isolamento D/DS15_v2| 
| 15 de maio de 2021 | Garantia de isolamento D/DS15_v2 removida| 

<sup>1</sup> o cliente existente usando esses tamanhos receberá um email de comunicado com instruções detalhadas sobre as próximas etapas.  

### <a name="q-what-are-the-milestones-for-g5-gs5-e64i_v3-and-e64is_v3-isolation-retirement"></a>P: quais são as etapas para o G5, o Gs5, o E64i_v3 e a desativação do isolamento de E64is_v3? 
**A**: 
 
| Data | Ação |
|---|---|
| 15 de fevereiro de 2021<sup>1</sup> | Notificação de desativação do isolamento G5/GS5/E64i_v3/E64is_v3 |
| 15 de fevereiro de 2022 | G5/GS5/E64i_v3/E64is_v3 garantia de isolamento removido |

<sup>1</sup> o cliente existente usando esses tamanhos receberá um email de comunicado com instruções detalhadas sobre as próximas etapas.  

## <a name="next-steps"></a>Próximas etapas

Os clientes também podem optar por subdividir ainda mais os recursos dessas máquinas virtuais Isoladas usando [o Suporte do Azure para máquinas virtuais aninhadas](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).

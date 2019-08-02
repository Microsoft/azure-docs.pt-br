---
title: Solicitações de aumento de cota regional do Azure | Microsoft Docs
description: Solicitações de aumento de cota regional
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 7ac6dce9aec1c363fa9772caabad9ce542d43888
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310647"
---
# <a name="total-regional-vcpu-limit-increase"></a>Aumento de limite de vCPU regional total 

Cotas de vCPU do Resource Manager para conjuntos de dimensionamento de máquinas virtuais e máquinas virtuais são aplicadas em duas camadas para cada assinatura, em cada região. 

A primeira camada é a **limite de vCPUs regionais totais** (em todas as séries de VM), e a segunda camada é a **por VM série vCPUs limitar** (por exemplo, os vCPUs da série D). Sempre que uma nova VM deve ser implantada, a soma do uso de vCPUs novos e existentes para essa série de VM não deve exceder a cota de vCPU aprovada para essa série específica de VM. Além disso, a contagem de vCPU total de novas e existentes implantada em todas as séries de VM não deve exceder a cota de vCPUs regionais totais aprovada para a assinatura. Se qualquer uma das cotas é excedida, a implantação de VM não será permitida.
Você pode solicitar um aumento de limite de cota de vCPUs para a série VM do portal do Azure. Um aumento na cota de séries de VM automaticamente aumenta o limite de vCPUs regionais totais no mesmo valor. 

Quando uma nova assinatura é criada, os vCPUs regionais totais do padrão pode não ser iguais à soma das cotas de vCPU de padrão para todas as séries de VM individuais. Isso pode resultar em uma assinatura com a cota suficiente para cada série de VM individuais que você deseja implantar, mas não há cota suficiente de vCPUs regionais totais para todas as implantações. Nesse caso, você precisará enviar uma solicitação para aumentar o limite de vCPUs regionais totais explicitamente. Limite total de vCPUs regionais não pode exceder a soma da cota aprovada em todas as séries VM para a região.

Saiba mais sobre cotas na [página de cotas de vCPU de máquina Virtual](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) e [assinatura do Azure e limites de serviço](https://aka.ms/quotalimits) página. 

Agora você pode solicitar um aumento pela **ajuda + suporte** folha ou o **usos + cota** folha no portal. 

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Solicitar aumento de cota de vCPUs regionais totais no nível de assinatura usando o **ajuda + suporte** folha

Siga as instruções abaixo para criar uma solicitação de suporte por meio da folha de 'Ajuda + suporte' do Azure disponível no portal do Azure. 

1. Partir https://portal.azure.com, selecione **ajuda + suporte**.

![Ajuda + suporte](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Selecione **Nova solicitação de suporte**. 

![Nova solicitação de suporte](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. No menu suspenso tipo de problema, escolha **limites de serviço e assinatura (cotas)** .

![Lista suspensa de tipo de problema](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Selecione a assinatura que precisa de uma cota maior.

![Selecione a assinatura newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Selecione **Other Requests** na **tipo de cota** lista suspensa.

![QuotaType](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. Na **detalhes** painel, fornecem informações adicionais, de acordo com o exemplo a seguir para ajudar a processar sua solicitação e continuar com a criação de casos. 
    1.  **Modelo de implantação** – especifique o ' Gerenciador de recursos '
    2.  **Região solicitada** – especifique sua região necessária, por exemplo, Leste dos EUA 2
    3.  **Novo valor de limite** – especificar novo limite de região. Isso não deve exceder a soma da cota aprovada de famílias de SKU individuais para essa assinatura

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-usages--quota-blade"></a>Solicitar aumento de cota de vCPUs regionais totais no nível de assinatura usando o **usos + cota** folha

Siga as instruções abaixo usar para criar uma solicitação de suporte por meio do Azure "uso + cota" folha disponível no portal do Azure. 

1. Em https://portal.azure.com, selecione **Assinaturas**.

![Assinaturas](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Selecione a assinatura que precisa de uma cota maior.

![Escolha a assinatura](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Selecione **Uso + cotas**

![Selecione uso e cotas](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. No canto superior direito, selecione **Solicitar Aumento**.

![Solicitar Aumento](./media/resource-manager-core-quotas-request/request-increase.png)

5. Selecione **Other Requests** na **tipo de cota** lista suspensa.

![QuotaType](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. Na **detalhes** painel, fornecem informações adicionais, de acordo com o exemplo a seguir para ajudar a processar sua solicitação e continuar com a criação de casos. 
    1.  **Modelo de implantação** – especifique o ' Gerenciador de recursos '
    2.  **Região solicitada** – especifique sua região necessária, por exemplo, Leste dos EUA 2
    3.  **Novo valor de limite** – especificar novo limite de região. Isso não deve exceder a soma da cota aprovada de famílias de SKU individuais para essa assinatura

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)




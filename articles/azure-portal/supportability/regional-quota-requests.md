---
title: Solicitar um aumento nos limites de cota do vCPU regional do Azure
description: Como solicitar um aumento no limite de cota de vCPU para uma região no portal do Azure.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: eadf740c6b5caccbf678a1238f993d4ec0b34095
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96745410"
---
# <a name="standard-quota-increase-limits-by-region"></a>Cota padrão: aumentar os limites por região

O Azure Resource Manager dá suporte a dois tipos de cotas do vCPU para máquinas virtuais:

* *As VMs pagas conforme o uso* e as *instâncias de VM reservadas* estão sujeitas a uma *cota de vCPU padrão*.
* As *VMs pontuais* estão sujeitas a uma *cota de vCPU Spot*.

A cota de vCPU padrão para instâncias de máquina virtual pagas conforme o uso e reservada é imposta em duas camadas para cada assinatura em cada região:

* A primeira camada é o *limite de vCPUs regional total*, em toda a série de VMs.
* A segunda camada é o *limite de vCPUs por VM da série*, como a vCPUs da série D.

Sempre que você implantar uma nova VM Spot, o uso total de vCPU novo e existente para essa série de VMs não deverá exceder a cota de vCPU aprovada para essa série de VMs específica. Além disso, o número total de vCPUs novas e existentes implantadas em toda a série de VMs não deve exceder a cota de vCPU regional total aprovada para a assinatura. Se uma dessas cotas for excedida, a implantação da VM não será permitida.

Você pode solicitar um aumento no limite de cota de vCPU para a série de VMs usando o portal do Azure. Um aumento na cota da série de VMs aumenta automaticamente o limite de vCPU regional total pelo mesmo valor.

Quando você cria uma nova assinatura, o número total padrão de vCPUs regionais pode não ser igual à cota de vCPU padrão total para todas as séries de VMs individuais. Essa discrepância pode resultar em uma assinatura com cota suficiente para cada série de VM individual que você deseja implantar. Mas pode não haver cota suficiente para acomodar o total regional de vCPUs para todas as implantações. Nesse caso, você deve enviar uma solicitação para aumentar explicitamente o limite do número total de vCPUs regionais. O limite de vCPU regional total não pode exceder a cota total aprovada em todas as séries de VM para a região.

Para saber mais sobre as cotas de vCPU padrão, consulte [cotas de vCPU de máquina virtual](../../virtual-machines/windows/quotas.md) e [limites, cotas e restrições de serviço e assinatura do Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md).

Para saber mais sobre como aumentar os limites de vCPU de VM Spot, confira [cota de spot: aumentar os limites para todas as séries de VM](low-priority-quota.md).

Você pode solicitar um aumento no limite de cota padrão do vCPU por região de uma das duas maneiras.

## <a name="request-a-quota-increase-by-region-from-help--support"></a>Solicitar um aumento de cota por região de ajuda + suporte

Para solicitar um aumento de cota de vCPU por região de **ajuda + suporte**:

1. No menu do [Portal do Azure](https://portal.azure.com), selecione **Ajuda + suporte**.

   ![O link "ajuda + suporte"](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. Em **Ajuda + suporte**, selecione **Nova solicitação de suporte**.

    ![Nova solicitação de suporte](./media/resource-manager-core-quotas-request/new-support-request.png)

1. Para **tipo de problema**, selecione **limites de serviço e de assinatura (cotas)**.

   ![Selecione um tipo de problema](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. Para **assinatura**, selecione a assinatura cuja cota você deseja aumentar.

   ![Selecionar uma assinatura](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. Para o **tipo de cota**, selecione **outras solicitações**.

   ![Selecionar um tipo de cota](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Selecione **Avançar: soluções** para abrir **detalhes do problema**. Em **Descrição**, forneça as seguintes informações:

    1. Para o **modelo de implantação**, especifique **Resource Manager**.  
    1. Para **região**, especifique a região necessária, por exemplo, **leste dos EUA 2**.  
    1. Para o **novo limite**, especifique um novo limite de vCPU para a região. Esse valor não deve exceder a soma das cotas aprovadas para a série SKU individual desta assinatura.

    ![Inserir detalhes para a solicitação de cota](./media/resource-manager-core-quotas-request/regional-details.png)

1. Selecione **examinar + criar** para continuar criando a solicitação de suporte.

## <a name="request-a-quota-increase-by-region-from-subscriptions"></a>Solicitar um aumento de cota por região de assinaturas

Para solicitar um aumento de cota de vCPU por região de **assinaturas**:

1. No [portal do Azure](https://portal.azure.com), procure e selecione **assinaturas**.

   ![Ir para assinaturas no portal do Azure](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Selecione a assinatura cuja cota você deseja aumentar.

   ![Selecione uma assinatura para modificar](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. No painel esquerdo, selecione **uso + cotas**.

   ![Seguir link de uso e cotas](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. No canto superior direito, selecione **solicitar aumento**.

   ![Selecione para aumentar a cota](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Em **tipo de cota**, selecione **outras solicitações**.

   ![Selecione o tipo de cota](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Selecione **Avançar: soluções** para abrir **detalhes do problema**. Na caixa **Descrição** , forneça as seguintes informações adicionais:

    1. Para o **modelo de implantação**, especifique **Resource Manager**.  
    1. Para **região**, especifique a região necessária, por exemplo, **leste dos EUA 2**.  
    1. Para o **novo limite**, especifique um novo limite de vCPU para a região. Esse valor não deve exceder a soma das cotas aprovadas para a série SKU individual desta assinatura.

    ![Inserir informações em detalhes](./media/resource-manager-core-quotas-request/regional-details.png)

1. Selecione **examinar + criar** para continuar criando a solicitação de suporte.

---
title: Solicitar a cota de host e habilitar a Solução VMware no Azure
description: Saiba como solicitar a cota/capacidade do host e habilitar o provedor de recursos da solução VMware do Azure. Você também pode solicitar mais hosts em uma nuvem privada da solução Azure VMware existente.
ms.topic: how-to
ms.custom: contperf-fy21q3
ms.date: 02/17/2021
ms.openlocfilehash: 5d154f5c63ffccdbf1729e641133b54be478d884
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100653152"
---
# <a name="request-host-quota-and-enable-azure-vmware-solution"></a>Solicitar a cota de host e habilitar a Solução VMware no Azure

Neste "como", você aprenderá como solicitar a cota/capacidade do host e habilitar o provedor de recursos da [solução VMware do Azure](introduction.md) , que habilita o serviço. Para poder habilitar a solução Azure VMware, você precisará enviar um tíquete de suporte para que seus hosts sejam alocados. Se você tiver uma nuvem privada da solução Azure VMware existente e quiser mais hosts alocados, você seguirá o mesmo processo.

>[!IMPORTANT]
>Pode levar alguns dias para alocar os hosts dependendo do número solicitado.  Portanto, solicite o que é necessário para o provisionamento, para que você não precise solicitar um aumento de cota com frequência.


O processo geral é simples e inclui duas etapas:
- Solicitar cota/capacidade de host adicional para [clientes do ea](#request-host-quota-for-ea-customers) ou do [CSP](#request-host-quota-for-csp-customers) 
- Habilitar o provedor de recursos Microsoft. AVS

## <a name="eligibility-criteria"></a>Critérios de qualificação

Você precisará de uma conta do Azure em uma assinatura do Azure. A assinatura do Azure deve seguir com um dos seguintes critérios:

- Uma assinatura em um [ea (contrato Enterprise do Azure)](../cost-management-billing/manage/ea-portal-agreements.md) com a Microsoft.
- Uma assinatura gerenciada do CSP (provedor de soluções na nuvem) em um CSP existente do Azure oferece contrato ou um plano do Azure.

## <a name="request-host-quota-for-ea-customers"></a>Solicitar cota de host para clientes do EA

1. Em seu portal do Azure, em **ajuda + suporte**, crie uma **[nova solicitação de suporte](https://rc.portal.azure.com/#create/Microsoft.Support)** e forneça as seguintes informações para o tíquete:
   - **Tipo de problema:** técnico
   - **Assinatura:** Selecione sua assinatura
   - **Serviço:** Todos os serviços > solução VMware do Azure
   - **Recurso:** Pergunta geral 
   - **Resumo:** Capacidade necessária
   - **Tipo de problema:** Problemas de Gerenciamento de Capacidade
   - **Subtipo do problema:** solicitação do cliente para cota/capacidade de host adicional

1. Na **Descrição** do tíquete de suporte, na guia **detalhes** , forneça:

   - POC ou produção 
   - Nome da Região
   - Número de hosts
   - Quaisquer outros detalhes

   >[!NOTE]
   >A solução Azure VMware recomenda um mínimo de três hosts para criar sua nuvem privada e para redundância N + 1 hosts. 

1. Selecione **revisão + criar** para enviar a solicitação.


## <a name="request-host-quota-for-csp-customers"></a>Solicitar cota de host para clientes do CSP 

Os CSPs devem usar [o Microsoft Partner Center](https://partner.microsoft.com) para habilitar a solução Azure VMware para seus clientes. Este artigo usa o [plano do Azure CSP](/partner-center/azure-plan-lp) como um exemplo para ilustrar o procedimento de compra para parceiros.

Acesse o portal do Azure usando o procedimento **admin em nome do** (Aobo) do Partner Center.

>[!IMPORTANT] 
>O serviço de solução do Azure VMware não fornece uma exigência de multilocação. Não há suporte para parceiros de hospedagem que exigem isso. 

1. Configure o plano do CSP do Azure:

   1. No **Partner Center**, selecione **CSP** para acessar a área **clientes** .
   
      :::image type="content" source="media/enable-azure-vmware-solution/csp-customers-screen.png" alt-text="Área de clientes do Microsoft Partner Center" lightbox="media/enable-azure-vmware-solution/csp-customers-screen.png":::
   
   1. Selecione o cliente e, em seguida, selecione **Adicionar produtos**.
   
      :::image type="content" source="media/enable-azure-vmware-solution/csp-partner-center.png" alt-text="Microsoft Partner Center" lightbox="media/enable-azure-vmware-solution/csp-partner-center.png":::
   
   1. Selecione **plano do Azure** e, em seguida, selecione **Adicionar ao carrinho**. 
   
   1. Examine e conclua a configuração geral da assinatura do plano do Azure para seu cliente. Para obter mais informações, consulte a [documentação do Microsoft Partner Center](/partner-center/azure-plan-manage).

1. Depois de configurar o plano do Azure e ter as [permissões do RBAC do Azure](/partner-center/azure-plan-manage) necessárias em vigor para a assinatura, você solicitará a cota para sua assinatura do plano do Azure. 

   1. Acesse portal do Azure do [Microsoft Partner Center](https://partner.microsoft.com) usando o procedimento **admin em nome do** (Aobo).
   
   1. Selecione **CSP** para acessar a área **clientes** .
   
   1. Expanda detalhes do cliente e selecione **portal de gerenciamento do Microsoft Azure**.
   
   1. No portal do Azure, em **ajuda + suporte**, crie uma **[nova solicitação de suporte](https://rc.portal.azure.com/#create/Microsoft.Support)** e forneça as seguintes informações para o tíquete:
      - **Tipo de problema:** técnico
      - **Assinatura:** Selecione sua assinatura
      - **Serviço:** Todos os serviços > solução VMware do Azure
      - **Recurso:** Pergunta geral 
      - **Resumo:** Capacidade necessária
      - **Tipo de problema:** Problemas de Gerenciamento de Capacidade
      - **Subtipo do problema:** solicitação do cliente para cota/capacidade de host adicional
   
   1. Na **Descrição** do tíquete de suporte, na guia **detalhes** , forneça:
   
      - POC ou produção 
      - Nome da Região
      - Número de hosts
      - Quaisquer outros detalhes
      - O destina-se a hospedar vários clientes?
   
      >[!NOTE]
      >A solução Azure VMware recomenda um mínimo de três hosts para criar sua nuvem privada e para redundância N + 1 hosts. 
   
   1. Selecione **revisão + criar** para enviar a solicitação.

## <a name="register-the-microsoftavs-resource-provider"></a>Registrar o provedor de recursos **Microsoft. AVS**

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="next-steps"></a>Próximas etapas

Depois de habilitar o recurso e a rede adequada em vigor, você pode [criar uma nuvem privada](tutorial-create-private-cloud.md).

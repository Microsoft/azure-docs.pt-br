---
title: Criar e associar políticas de ponto de extremidade de serviço – portal do Azure
titlesuffix: Azure Virtual Network
description: Neste artigo, saiba como configurar e associar políticas de ponto de extremidade de serviço usando o portal do Azure.
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: bdf0e87c92a55d0dbb5bbe34334a6de4580cb350
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96004952"
---
# <a name="create-change-or-delete-service-endpoint-policy-using-the-azure-portal"></a>Criar, alterar ou excluir a política de ponto de extremidade de serviço usando o portal do Azure

As políticas de ponto de extremidade de serviço permitem que você filtre o tráfego de rede virtual para recursos específicos do Azure em pontos de extremidade de serviço. Se você não estiver familiarizado com as políticas de ponto de extremidade de serviço, confira [visão geral das políticas de ponto de extremidade de serviço](virtual-network-service-endpoint-policies-overview.md) para saber mais.

 Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma política de ponto de extremidade de serviço
> * Criar uma definição da política de ponto de extremidade de serviço
> * Criar uma rede virtual com uma sub-rede
> * Associar uma política de ponto de extremidade de serviço a uma sub-rede

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Entrar no Azure 

Entre no Portal do Azure em https://portal.azure.com.

## <a name="create-a-service-endpoint-policy"></a>Criar uma política de ponto de extremidade de serviço

1. Selecione **Criar um recurso** no canto superior esquerdo do Portal do Azure.
2. No painel Pesquisar, digite "política de ponto de extremidade de serviço" e selecione **política de ponto de extremidade de serviço** e, em seguida, selecione **criar**.

![Criar política de ponto de extremidade de serviço](./media/virtual-network-service-endpoint-policies-portal/create-sep-resource.png)

3. Insira, ou selecione, as seguintes informações em **Básico** 

   - Assinatura: selecione sua assinatura para a política
   - Grupo de recursos: selecione **criar novo** e digite *MyResource* Group
   - Nome: myEndpointPolicy
   - Local: EUA Central
 
   ![Criar o básico da política de ponto de extremidade de serviço](./media/virtual-network-service-endpoint-policies-portal/create-sep-basics.png)

4. Selecione **+ Adicionar** em **recursos** e insira ou selecione as seguintes informações no painel **Adicionar um recurso**

   - Serviço: somente **o Microsoft. Storage** está disponível com políticas de ponto de extremidade de serviço
   - Escopo: selecione um de **conta única**, **todas as contas na assinatura** e **todas as contas no grupo de recursos**
   - Assinatura: selecione sua assinatura para a conta de armazenamento. A política e as contas de armazenamento podem estar em diferentes assinaturas.
   - Grupo de recursos: selecione seu grupo de recursos. Obrigatório, se o escopo estiver definido como "Todas as contas no grupo de recursos" ou "Conta individual".  
   - Recurso: selecione o recurso de armazenamento do Azure na assinatura ou grupo de recursos selecionado
   - Clique no botão **Adicionar** na parte inferior para concluir a adição do recurso

   ![Definição de política de ponto de extremidade de serviço – recurso](./media/virtual-network-service-endpoint-policies-portal/create-sep-add-resource.png)

   - Adicione mais recursos repetindo as etapas acima conforme necessário

5. Opcional: insira ou selecione as seguintes informações em **Tags**:
   
   - Chave: selecione sua chave para a política. Por exemplo: Depto     
   - Valor: insira um par de valor para a chave. Por exemplo: Finanças

6. Selecione **Examinar + criar**. Valide as informações e clique em **Criar**. Para fazer mais edições, clique em **Anterior**. 

   ![Criar validações definitivas da política de ponto de extremidade de serviço](./media/virtual-network-service-endpoint-policies-portal/create-sep-review-create.png)
  
## <a name="view-endpoint-policies"></a>Exibir políticas de ponto de extremidade 

1. Na caixa *Todos os serviços* no portal, comece digitando *políticas de ponto de extremidade de serviço*. Selecione **políticas de ponto de extremidade de serviço**.
2. Em **Assinaturas**, selecione sua assinatura e grupo de recursos, conforme mostrado na imagem a seguir

   ![Mostrar política](./media/virtual-network-service-endpoint-policies-portal/sep-view.png)
       
3. Selecione a política e clique em **Definições de política** para exibir ou adicionar mais definições de política.

   ![Mostrar definições de política](./media/virtual-network-service-endpoint-policies-portal/sep-policy-definition.png)

4. Selecione **Sub-redes associadas** para exibir as sub-redes às quais a política está associada. Se nenhuma sub-rede estiver associada ainda, siga as instruções na próxima etapa.

   ![Sub-redes associadas](./media/virtual-network-service-endpoint-policies-portal/sep-associated-subnets.png)
 
5. Associar uma política a uma sub-rede

>[!WARNING] 
> Certifique-se de que todos os recursos acessados da sub-rede sejam adicionados à definição de política antes de associar a política à sub-rede determinada. Depois que a política estiver associada, somente o acesso aos recursos *listados de permitir* será permitido em pontos de extremidade de serviço. 
>
> Verifique também se não existem serviços gerenciados do Azure na sub-rede que está sendo associada à política de ponto de extremidade de serviço

- Antes de poder associar uma política a uma sub-rede, você precisa criar uma rede virtual e uma sub-rede. Veja o artigo [criar uma rede virtual](./quick-create-portal.md) para obter ajuda com isso.

- Depois que a rede virtual e a sub-rede forem configuradas, você precisará configurar pontos de extremidade de serviço de rede virtual para o armazenamento do Azure. Na folha rede virtual, selecione **pontos de extremidade de serviço** e, no próximo painel, selecione **Microsoft. Storage** e, em sub- **redes** , selecione a VNet ou sub-rede desejada

- Agora, você pode optar por selecionar a política de ponto de extremidade de serviço na lista suspensa no painel acima se já tiver criado políticas de ponto de extremidade de serviço antes de configurar o ponto de extremidade de serviço para a sub-rede, conforme mostrado abaixo

    ![Associar sub-rede ao criar ponto de extremidade de serviço](./media/virtual-network-service-endpoint-policies-portal/vnet-config-service-endpoint-add-sep.png)

- OU, se você estiver associando políticas de ponto de extremidade de serviço depois que os pontos de extremidades de serviço já estiverem configurados, você poderá optar por associar a sub-rede de dentro da folha de política de ponto de extremidade de serviço navegando até o painel de **sub-redes associado** , conforme mostrado abaixo

    ![Associar sub-rede via SEP](./media/virtual-network-service-endpoint-policies-portal/sep-edit-subnet-association.png)

>[!WARNING] 
>O acesso aos recursos de armazenamento do Azure em todas as regiões será restrito de acordo com a política de ponto de extremidade de serviço desta sub-rede.

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você criou uma política de ponto de extremidade de serviço e a associou a uma sub-rede. Para saber mais sobre políticas de ponto de extremidade de serviço, confira [visão geral de políticas de ponto de extremidade de serviço.](virtual-network-service-endpoint-policies-overview.md)

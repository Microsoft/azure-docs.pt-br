---
title: Criar e associar políticas de ponto de extremidade de serviço – portal do Azure
titlesuffix: Azure Virtual Network
description: Neste artigo, saiba como configurar e associar políticas de ponto de extremidade de serviço usando o portal do Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 09/18/2018
ms.author: kumud
ms.openlocfilehash: aaa07759ed8b10578d024d5838ac1d2658778695
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61034535"
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
2. No painel de pesquisa, digite "política de ponto de extremidade de serviço" e selecione **Política de ponto de extremidade de serviço (Versão prévia)** e, em seguida, selecione **Criar**.
3. Insira, ou selecione, as seguintes informações em **Básico** 

   - Assinatura: Selecione sua assinatura para a política.    
   - Grupo de recursos: Selecione **Criar novo** e insira *myResourceGroup*.     
   - Nome: myEndpointPolicy
   - Local: Centro-Oeste dos EUA     
 
   ![Criar o básico da política de ponto de extremidade de serviço](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-create-startpane.PNG)
   
4. Insira, ou selecione, as seguintes informações em **Definições de política**

   - Em **+ Adicionar um recurso**, insira, ou selecione, as informações a seguir, aceite o padrão para as configurações restantes e clique em **Adicionar**.  
   - Escopo: Selecione **Conta individual** ou **Todas as contas na assinatura** ou **Todas as contas no grupo de recursos**.    
   - Assinatura: Selecione sua assinatura para a conta de armazenamento. A política e as contas de armazenamento podem estar em diferentes assinaturas.   
   - Grupo de recursos: Selecione o grupo de recursos. Obrigatório, se o escopo estiver definido como "Todas as contas no grupo de recursos" ou "Conta individual".  
   - Recurso: mystorageaccountportal    
   - Clique em **+ Adicionar um recurso** para continuar adicionando mais recursos.
   
   ![Criar definições da política de ponto de extremidade de serviço](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-create-policydefinitionspane.PNG)
   
5. Opcional: Insira, ou selecione, as seguintes informações em **Marcas**:
   
   - Chave: Selecione sua chave para a política. Por exemplo: Dept     
   - Valor: Insira um par de valor para a chave. Por exemplo: Finanças

6. Selecione **Examinar + criar**. Valide as informações e clique em **Criar**. Para fazer mais edições, clique em **Anterior**. 

   ![Criar validações definitivas da política de ponto de extremidade de serviço](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-create-finalcreatereview.PNG)
  
 
## <a name="view-endpoint-policies"></a>Exibir políticas de ponto de extremidade 

1. Na caixa *Todos os serviços* no portal, comece digitando *políticas de ponto de extremidade de serviço*. Selecione **Políticas de ponto de extremidade de serviço (Versão prévia)**.
2. Em **Assinaturas**, selecione sua assinatura e grupo de recursos, conforme mostrado na imagem a seguir

   ![Mostrar política](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-viewpolicies.PNG)
       
3. Selecione a política e clique em **Definições de política** para exibir ou adicionar mais definições de política.

   ![Mostrar definições de política](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-viewpolicy-adddefinitions.PNG)

4. Selecione **Sub-redes associadas** para exibir as sub-redes às quais a política está associada. Para associar a política a uma sub-rede, clique em "Navegar até a rede virtual na mesma região".

   ![Mostrar sub-redes associadas](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-view-associatedsubnets.PNG)
 
## <a name="associate-a-policy-to-a-subnet"></a>Associar uma política a uma sub-rede

>[!WARNING] 
> Certifique-se de que todos os recursos acessados da sub-rede, para o serviço selecionado, sejam adicionados à política antes de associá-la. Depois que a política for associada, apenas o acesso aos recursos listados na política serão permitidos, para regiões de ponto de extremidade do serviço. 

Antes de poder associar uma política a uma sub-rede, é necessário criar uma rede virtual e uma sub-rede e, em seguida, é possível associar a política à sub-rede:

1. Selecione **Criar um recurso** no canto superior esquerdo do Portal do Azure.
2. Selecione **Rede** e, sem seguida, selecione **Rede Virtual**.
3. Em **Criar rede virtual**, insira, ou selecione, as informações a seguir, aceite o padrão para as configurações restantes e, em seguida, selecione **Criar**:
   - Nome: myVirtualNetwork      
   - Espaço de endereço: 10.0.0.0/16      
   - Assinatura: Selecione sua assinatura. A política deve estar na mesma assinatura que a VNet     
   - Grupo de recursos: Selecione **Usar existente** e depois *myResourceGroup*     
   - Local: Centro-Oeste dos EUA     
   - Nome da sub-rede: privado     
   - Intervalo de endereços: 10.0.0.0/24
     
4. Na caixa **Pesquisar recursos, serviços e documentos** na parte superior do portal, comece digitando *myVirtualNetwork*. Quando **myVirtualNetwork** aparecer nos resultados da pesquisa, selecione-o.
5. Em **CONFIGURAÇÕES**, selecione **Sub-redes** e, em seguida, selecione **privado**.
6. Conforme mostrado na imagem a seguir, selecione **Pontos de extremidade de serviço**, **Microsoft.Storage**, **Políticas de ponto de extremidade de serviço**, **myEndpointPolicy** e, em seguida, **Salvar**:

   ![Associar política](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-associatepolicies.PNG)

>[!WARNING] 
>O acesso aos recursos de serviço em outras regiões será permitido desta sub-rede, com base em NSGs (Grupos de Segurança de Rede). Para restringir o acesso a apenas regiões de ponto de extremidade, limite os NSGs a apenas tráfego de serviço nas regiões de ponto de extremidade. Para obter mais informações sobre como criar NSGs com marcas de serviço por região, confira [Marcas de serviço do Azure de NSG.](manage-network-security-group.md?toc=%2fcreate-a-security-rule%2f.json)

No exemplo abaixo, o NSG é restrito para acessar apenas recursos de Armazenamento do Azure em WestCentralUS e WestUS2, com uma regra "Negar todos" como uma regra de prioridade mais baixa.

![Negar todo NSG](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-nsg-rules.PNG)


## <a name="next-steps"></a>Próximos passos
Neste tutorial, você criou uma política de ponto de extremidade de serviço e a associou a uma sub-rede. Para saber mais sobre políticas de ponto de extremidade de serviço, confira [visão geral de políticas de ponto de extremidade de serviço.](virtual-network-service-endpoint-policies-overview.md)


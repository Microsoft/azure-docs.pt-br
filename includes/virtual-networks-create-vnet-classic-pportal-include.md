---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: aa88bf3bd6c5037b41c09e9ffe47921f1b9dc9be
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171836"
---
## <a name="how-to-create-a-classic-vnet-in-the-azure-portal"></a>Como criar uma rede virtual clássica no portal do Azure
Para criar uma VNet clássica com base no cenário anterior, siga estas etapas.

1. Em um navegador, navegue até http://portal.azure.com e, se necessário, entre com sua conta do Azure.
2. Clique em **Criar um recurso** > **Rede** > **Rede virtual**. Observe que a lista **Selecionar um modelo de implantação** já mostra **Clássico**. 3. Clique em **Criar**, conforme mostrado na figura a seguir.
   
    ![Criar rede virtual no portal do Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)
4. No painel **Rede virtual**, digite o **Nome** da VNet e, em seguida, clique em **Espaço de endereço**. Defina as configurações de espaço de endereço para a rede virtual e sua primeira sub-rede e clique em **OK**. A figura a seguir mostra as configurações do bloco CIDR para o nosso cenário.
   
    ![Painel Espaço de endereço](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)
5. Clique em **Grupo de Recursos** e selecione um grupo de recursos ao qual adicionar a rede virtual, ou clique em **Criar novo grupo de recursos** para adicionar a rede virtual a um novo grupo de recursos. A figura a seguir mostra as configurações do grupo de recursos para um novo grupo de recursos chamado **TestRG**. Para saber mais sobre grupos de recursos, visite [Visão geral do Gerenciador de Recursos do Azure](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).
   
    ![Painel Criar grupo de recursos](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)
6. Se necessário, altere as configurações de **Assinatura** e **Local** da sua rede virtual. 
7. Se não deseja ver a rede virtual como um bloco no **Quadro Inicial**, desabilite **Fixar no Quadro Inicial**. 
8. Clique em **Criar** e observe o bloco nomeado **Criando a rede virtual**, conforme mostrado na figura a seguir.
   
    ![Criar rede virtual no portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)
9. Aguarde até que a VNet seja criada, e quando você visualizar o bloco, clique nele para adicionar mais sub-redes.
   
    ![Criar rede virtual no portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)
10. Você deverá ver a **Configuração** para sua VNet, conforme mostrado. 
   
    ![Criar rede virtual no portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)
11. Clique em **Sub-redes** > **Adicionar**. Em seguida, digite um **Nome** e especifique um **Intervalo de endereços (bloco CIDR)** para a sub-rede e clique em **OK**. A figura a seguir mostra as configurações do nosso cenário atual.
    
    ![Criar rede virtual no portal do Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)


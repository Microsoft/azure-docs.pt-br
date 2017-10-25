---
title: Criar um servidor do Analysis Services no Azure | Microsoft Docs
description: "Saiba como criar uma instância do servidor do Analysis Services no Azure."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 7f560216-8a9a-4d06-852e-48cf24deab19
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/15/2017
ms.author: owend
ms.openlocfilehash: 95b367e7cd74405088190c1fe19cf92990759d90
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-analysis-services-server-in-azure-portal"></a>Criar um servidor do Azure Analysis Services no portal do Azure
Este artigo orienta você pela criação de um recurso de servidor do Analysis Services em sua assinatura do Azure.

## <a name="before-you-begin"></a>Antes de começar
Para concluir este início rápido, você precisa de:

* **Assinatura do Azure**: visite a [Avaliação Gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) para criar uma conta.
* **Azure Active Directory**: sua assinatura deve estar associada a um locatário do Azure Active Directory. Também é preciso estar conectado ao Azure com uma conta no Azure Active Directory em questão. Não há suporte para contas da Microsoft. Para obter mais informações, confira [Autenticação e permissões de usuário](analysis-services-manage-users.md).
* **Grupo de recursos**: use um grupo de recursos que você já tem ou [crie um novo](../azure-resource-manager/resource-group-overview.md).

> [!NOTE]
> Criar um servidor pode resultar em um novo serviço faturável. Para saber mais, veja [Preços do Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).
> 
> 

## <a name="to-create-a-server-in-azure-portal"></a>Para criar um servidor no portal do Azure
1. Entre no [Portal do Azure](https://portal.azure.com).  
2. Clique em **+ Novo** > **Dados + Análise** > **Analysis Services**.
3. Na folha **Analysis Services**, preencha os campos obrigatórios e pressione **Criar**.
   
    ![Criar servidor](./media/analysis-services-create-server/aas-create-server-blade.png)
   
   * **Nome do servidor**: digite um nome exclusivo usado para fazer referência ao servidor.
   * **Assinatura**: selecione a assinatura para a qual este servidor é cobrado.
   * **Grupo de recursos**: esses contêineres são projetados para ajudar você a gerenciar uma coleção de recursos do Azure. Para saber mais, veja [grupos de recursos](../azure-resource-manager/resource-group-overview.md).
   * **Local**: local do datacenter do Azure que hospeda o servidor. Escolha um local mais próximo da sua maior base de usuários.
   * **Tipo de preço**: selecione um tipo de preço. Há suporte para modelos tabulares até de 400 GB. Para saber mais, veja [Preços do Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).
4. Clique em **Criar**.

Normalmente, a criação demora menos de um minuto; com frequência, apenas alguns segundos. Se você tiver selecionado **Adicionar ao Portal**, navegue até o portal para ver o novo servidor. Ou navegue até **Mais serviços** > **Analysis Services** para ver se o servidor está pronto.

 ![Painel](./media/analysis-services-create-server/aas-create-server-dashboard.png)


## <a name="next-steps"></a>Próximas etapas
Depois de ter criado seu servidor, você poderá [implantar um modelo](analysis-services-deploy.md) a ele usando o SSDT ou com o SSMS.

Se um modelo implantado em seu servidor se conectar às fontes de dados locais, você precisará instalar um [Gateway de dados local](analysis-services-gateway.md) em um computador em sua rede.


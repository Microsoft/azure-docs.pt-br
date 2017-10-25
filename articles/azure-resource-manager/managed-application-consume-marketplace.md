---
title: Consumir Aplicativo Gerenciado do Azure no Marketplace | Microsoft Docs
description: "Descreve como criar um Aplicativo Gerenciado do Azure disponível por meio do Marketplace."
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/11/2017
ms.author: gauravbh; tomfitz
ms.openlocfilehash: baf456740bddd562391ed64d707f990c8921d710
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="consume-azure-managed-applications-in-the-marketplace"></a>Consumir aplicativos gerenciados do Azure no Marketplace

Conforme discutido no artigo [Visão geral de aplicativos gerenciados](managed-application-overview.md), há dois cenários na experiência de ponta a ponta. Um é o publicador ou fornecedor que deseja criar um aplicativo gerenciado para uso pelos clientes. O outro é o cliente final ou o cliente do aplicativo gerenciado. Este artigo aborda o segundo cenário. Descreve como você pode implantar um aplicativo gerenciado do Microsoft Azure Marketplace.

## <a name="create-from-the-marketplace"></a>Criar do Marketplace

Implantar um aplicativo gerenciado do Marketplace é semelhante a implantar de qualquer tipo de recursos do Marketplace. 

No portal, selecione **+ Novo** e pesquise o tipo de solução a implantar. Entre as opções disponíveis, selecione aquela de que você precisa.

![pesquisar soluções](./media/managed-application-consume-marketplace/search-apps.png)

Examine o resumo do aplicativo e selecione **Criar**.

![criar aplicativo gerenciado](./media/managed-application-consume-marketplace/create-marketplace-managed-app.png)

Como qualquer outra solução, você verá os campos para os quais fornecer valores. Esses campos variam de acordo com o tipo de aplicativo gerenciado que você cria. 

## <a name="view-support-information"></a>Exibir informações de suporte

Após a implantação do seu aplicativo gerenciado, exiba as informações de suporte para o aplicativo. Na folha do aplicativo gerenciado, as informações de suporte estão listadas.

![suporte](./media/managed-application-consume-marketplace/support.png)

## <a name="view-publisher-permissions"></a>Exibir permissões de publicador

O fornecedor que gerencia seu aplicativo terá acesso aos seus recursos. Para ver essas permissões, selecione **Autorizações**.

![autorizações](./media/managed-application-consume-marketplace/authorizations.png)

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre como publicar um aplicativo gerenciado no Marketplace, consulte [Aplicativos Gerenciados do Azure no Marketplace](managed-application-author-marketplace.md).
* Para publicar aplicativos gerenciados disponíveis somente para usuários em sua organização, consulte [Criar e publicar o Aplicativo Gerenciado do Catálogo do Serviços](managed-application-publishing.md).
* Para consumir aplicativos gerenciados disponíveis somente para usuários em sua organização, consulte [Consumir um Aplicativo Gerenciado do Catálogo do Serviços](managed-application-consumption.md).
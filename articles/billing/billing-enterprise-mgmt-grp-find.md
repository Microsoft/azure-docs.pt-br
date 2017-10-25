---
title: "Localizar uma assinatura do Azure ou um grupo de gerenciamento – Azure | Microsoft Docs"
description: "Como navegar entre vários diretórios para ver seus grupos de gerenciamento e assinaturas"
author: rthorn17
manager: rithorn
editor: 
ms.assetid: 
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: rithorn
ms.openlocfilehash: df4d85d0556a62311c112f24431b54d042333c7f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="find-an-azure-subscription-or-management-group"></a>Localizar a assinatura do Azure ou grupo de gerenciamento

Se você estiver com dificuldade para localizar uma assinatura ou um grupo de gerenciamento no Azure, talvez esteja pesquisando no diretório errado. Essa situação pode acontecer quando sua conta existe em vários Azure Active Directories. Cada [diretório ativo é independente](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-licensing-directory-independence) e o acesso não é herdado entre diretórios.      

![Mudar menu de diretório](media/billing-enterprise-mgmt-groups/mgempty.png)



## <a name="switch-directories"></a>Mudar diretórios 
Você pode mudar facilmente de diretórios no Portal do Azure.
1.  Faça logon no [Portal do Azure](https://portal.azure.com).
2.  Selecione seu nome no canto superior direito da tela. 
3.  A parte inferior do menu lista todos os diretórios aos quais você tem acesso.
4.  Selecione um nome de diretório para acessar. 

![Mudar menu de diretório](media/billing-enterprise-mgmt-groups/switch-directory.png)

## <a name="asset-is-unavailable"></a>O ativo não está disponível? 
Se você estiver recebendo uma mensagem de erro "Este ativo está indisponível" ao tentar acessar uma assinatura ou um grupo de gerenciamento, significa que você não tem a função correta para exibir este item.  

![asset-not-found](media/billing-enterprise-mgmt-groups/asset-not-found.png)

Entre em contato com o administrador da assinatura ou dos grupos de gerenciamento para obter acesso.  
* Para assinaturas, consulte o documento [RBAC (Controle de acesso baseado em função) do Azure](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-configure) para obter ajuda sobre qual função é necessária.
* Para os grupos de gerenciamento, o acesso RBAC não está disponível e estará disponível em breve. Entre em contato com o administrador do portal empresarial para ter o acesso atribuído.   

## <a name="improve-your-experience-with-management-groups-and-subscriptions-in-the-same-directory"></a>Melhorar sua experiência com grupos de gerenciamento e assinaturas no mesmo diretório 
Você pode transferir suas assinaturas ou seus grupos de gerenciamento para o diretório que desejar para que tudo fique no mesmo local.  A consolidação de assinaturas e grupos de gerenciamento no mesmo diretório ajuda a reduzir a necessidade de mudar de diretórios e permite que as políticas sejam herdadas.  


### <a name="transfer-your-subscriptions"></a>Transferir suas assinaturas 
Você pode mover uma assinatura para o diretório associado com os grupos de gerenciamento. Essa mudança pode ser realizada solicitando ao administrador de registro que transfira sua assinatura para uma conta no diretório de destino. Para saber mais, entre no [Enterprise Portal](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription).


 







---
title: "Solucionar problemas de exibições de custo empresarial – Azure | Microsoft Docs"
description: "Saiba como resolver quaisquer problemas com modos de exibição de custo organizacional no Portal do Azure."
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
ms.openlocfilehash: eca1ac9ed51e6c2243be451a074792fbec2840d2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-enterprise-cost-views"></a>Solucionar problemas de exibições de custo empresarial 

Nas inscrições empresariais, há várias configurações que podem fazer com que os usuários do registro não consigam exibir os custos.  Essas configurações são gerenciadas pelo administrador de registro ou pelo parceiro, se o registro não foi adquirido diretamente da Microsoft.  Este artigo ajuda você a entender quais são as configurações e como elas afetam o registro. Essas configurações são independentes das [Funções de RBAC do Azure](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-configure). 

> [!Note]
> Esse recurso está atualmente em uma versão prévia particular. [Inscreva-se aqui](https://forms.office.com/Pages/DesignPage.aspx#FormId=v4j5cvGGr0GRqy180BHbR0YtfU6ham9OsGsPPYdu2xdUNk1BQUwzTkUyOVc5NUpCTFcwR0pIOVFETS4u) para ingressar seu registro na versão prévia.     

## <a name="enabling-access-to-costs"></a>Habilitando o acesso aos custos

Você está vendo uma mensagem de Não autorizado ou *"As exibições de custo estão desabilitadas em seu registro."* ao procurar informações sobre custo? ![não autorizado](media/billing-enterprise-mgmt-groups/unauthorized.png)

Isso pode acontecer devido a um dos seguintes motivos:

1. Você adquiriu o Azure por meio de um parceiro empresarial e o parceiro ainda não liberou o preço. Para liberar o preço, entre em contato com seu parceiro para realizar a atualização da configuração no [Enterprise Portal](https://ea.azure.com).
2. Como alternativa, se você é um cliente direto de EA, há algumas possibilidades:
    * Você é um Proprietário de Conta e seu Administrador de registro desabilitou a configuração “Encargos de exibição do AO”.  
    * Você é um Administrador de Departamento e seu Administrador de registro desabilitou a configuração “Encargos de exibição do DA”.
    * Entre em contato com o Administrador de registro para obter acesso. O administrador de registro pode visitar o [Enterprise Portal](https://ea.azure.com/manage/enrollment) e atualizar as configurações, como visto aqui:

![Configurações do Enterprise Portal](media/billing-enterprise-mgmt-groups/ea-portal-settings.png)


## <a name="asset-is-unavailable"></a>O ativo não está disponível? 
Se você estiver recebendo uma mensagem de erro "Este ativo está indisponível" ao tentar acessar uma assinatura ou um grupo de gerenciamento, significa que você não tem a função correta para exibir este item.  

![asset-not-found](media/billing-enterprise-mgmt-groups/asset-not-found.png)

Entre em contato com o administrador da assinatura ou dos grupos de gerenciamento para obter acesso.  
* Para assinaturas, consulte o documento [RBAC (Controle de acesso baseado em função) do Azure](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-configure) para obter ajuda sobre qual função é necessária.
* Para os grupos de gerenciamento, o acesso RBAC não está disponível e estará disponível em breve. Entre em contato com o administrador do portal empresarial para ter o acesso atribuído.   

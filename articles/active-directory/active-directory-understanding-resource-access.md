---
title: "Noções básicas sobre o acesso aos recursos no Azure | Microsoft Docs"
description: "Este tópico explica os conceitos sobre como usar os administradores de assinatura para controlar o acesso aos recursos no Portal do Azure completo"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
ms.assetid: 174f1706-b959-4230-9a75-bf651227ebf6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2017
ms.author: curtand
ms.custom: oldportal;it-pro;
ms.openlocfilehash: e5ac068e37d921530272e5eb3dc76d976a86a742
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="understanding-resource-access-in-azure"></a>Noções básicas sobre o acesso aos recursos do Azure

O controle de acesso no Azure parte de uma perspectiva de cobrança. O proprietário de uma conta do Azure, acessada pela visitação ao [Centro de Contas do Azure](https://account.windowsazure.com/subscriptions), é o Administrador da Conta (AA). As assinaturas são um contêiner para cobrança, mas também atuam como um limite de segurança: cada assinatura tem um Administrador de Serviços (SA) que pode adicionar, remover e modificar recursos do Azure nessa assinatura usando o [Portal do Azure](https://portal.azure.com/). O SA padrão de uma nova assinatura é o AA, mas o AA pode alterar o SA no Centro de contas do Azure.

<br><br>![Contas do Azure][1]

As assinaturas também têm uma associação com um diretório. O diretório define um conjunto de usuários. Estes podem ser usuários do trabalho ou da escola que criou o diretório ou podem ser usuários externos (ou seja, Contas da Microsoft). As assinaturas são acessíveis por um subconjunto desses usuários do diretório que foram atribuídos como um serviço SA (Administrador) ou CA (Coadministrador); a única exceção é que, por motivos de herança, Contas da Microsoft (anteriormente chamadas de Windows Live ID) podem ser atribuídas como SA ou CA sem estarem presentes no diretório.

<br><br>![Controle de acesso no Azure][2]

A funcionalidade no Portal clássico do Azure permite que os SAs que se conectaram usando uma Conta da Microsoft alterem o diretório ao qual uma assinatura está associada usando o comando **Editar Diretório** na página **Assinaturas** em **Configurações**. Observe que esta operação tem implicações no controle de acesso da assinatura.

> [!NOTE]
> O comando **Editar Diretório** no Portal clássico do Azure não está disponível para os usuários que se conectaram usando uma conta corporativa ou de estudante, pois essas contas podem se conectar apenas ao diretório ao qual pertencem.
> 
> 

<br><br>![Fluxo de Logon de Usuário Simples][3]

No caso mais simples, uma organização (como por exemplo, Contoso) fará a cobrança e o controle de acesso no mesmo conjunto de assinaturas. Ou seja, o diretório está associado às assinaturas pertencentes a uma única conta do Azure. Após o logon bem-sucedido no Portal clássico do Azure, os usuários veem duas coleções de recursos (representadas em laranja na ilustração anterior):

* Diretórios onde sua conta de usuário existe (originados ou adicionados como uma entidade externa). Observe que o diretório usado para logon não é relevante para esse cálculo, portanto os diretórios serão sempre mostrados independentemente de onde você fez logon.
* Recursos que fazem parte das assinaturas que estão associados com o diretório usado para fazer logon E que o usuário pode acessar (seja um SA ou CA).

<br><br>![Usuário com Várias Assinaturas e Diretórios][4]

Os usuários com assinaturas em vários diretórios têm a capacidade de alternar o contexto atual do Portal clássico do Azure usando o filtro de assinatura. Internamente, isso resulta em um logon separado para um diretório diferente, mas isso é feito diretamente usando logon único (SSO).

Operações como mover recursos entre as assinaturas podem ser mais difíceis como resultado desta visão única de diretório de assinaturas. Para executar a transferência de recursos, talvez seja necessário usar primeiro o comando **Editar Diretório** na página Assinaturas em **Configurações** para associar as assinaturas ao mesmo diretório.

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre como alterar administradores para uma assinatura do Azure, veja [Como adicionar ou alterar as funções de administrador do Azure](../billing/billing-add-change-azure-subscription-administrator.md)
* Para saber mais sobre como o Azure Active Directory está relacionado à sua assinatura do Azure, consulte [Como as assinaturas do Azure estão associadas ao Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* Para saber sobre como atribuir funções no AD do Azure, veja [Atribuindo funções de administrador no Active Directory do Azure](active-directory-assign-admin-roles.md)

<!--Image references-->
[1]: ./media/active-directory-understanding-resource-access/IC707931.png
[2]: ./media/active-directory-understanding-resource-access/IC707932.png
[3]: ./media/active-directory-understanding-resource-access/IC707933.png
[4]: ./media/active-directory-understanding-resource-access/IC707934.png

---
title: "Como as assinaturas do Azure estão associadas ao Azure Active Directory | Microsoft Docs"
description: Entrada no Microsoft Azure e problemas relacionados, como o relacionamento entre uma assinatura do Azure e o Azure Active Directory.
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: bc4773c2-bc4a-4d21-9264-2267065f0aea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/24/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 081cf2bde44a0b55508cc7f0197fa7f8e378189b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-azure-subscriptions-are-associated-with-azure-active-directory"></a>Como as assinaturas do Azure são associadas ao Azure Active Directory
Este artigo aborda informações sobre a relação entre uma assinatura do Azure e o Azure AD (Azure Active Directory) e como adicionar uma assinatura existente ao diretório do Azure AD.

## <a name="your-azure-subscriptions-relationship-to-azure-ad"></a>Relação da sua assinatura do Azure com o Azure AD
A sua assinatura do Azure tem uma relação de confiança com o Azure AD, o que significa que ela confia no diretório para autenticar usuários, serviços e dispositivos. Várias assinaturas podem confiar no mesmo diretório, mas cada assinatura confia em apenas um diretório. 

Essa relação de confiança que uma assinatura tem com um diretório é diferente da relação que uma assinatura tem com todos os outros recursos no Azure (sites, bancos de dados e assim por diante). Se uma assinatura expira, o acesso aos outros recursos associados à assinatura também é interrompido. Mas um diretório do Azure AD permanece no Azure e você pode associar outra assinatura a ele e gerenciar os usuários do diretório usando a nova assinatura.

![diagrama de como as assinaturas são associadas](./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png)

Todos os usuários têm um único diretório inicial que os autentica, mas eles também podem ser convidados em outros diretórios. No Azure AD, você pode ver os diretórios dos quais a sua conta de usuário é um membro ou convidado.

## <a name="azure-ad-and-cloud-service-subscriptions"></a>Azure AD e assinaturas do serviço de nuvem
O Azure AD oferece os principais recursos de gerenciamento de diretório e de identidade por trás da maioria dos serviços de nuvem da Microsoft, incluindo:

* As tabelas
* Microsoft Office 365
* Microsoft Dynamics CRM Online
* Microsoft Intune

Você obtém o serviço gratuito do Azure AD quando se inscreve em qualquer um desses serviços de nuvem da Microsoft. Se você deseja adicionar uma assinatura do Azure adicional para um diretório do Azure AD, precisa estar conectado com uma conta da Microsoft. Se você entrar no Azure com uma conta corporativa ou de estudante, você não pode adicionar uma assinatura do Azure a um diretório existente, porque sua conta corporativa ou de estudante não pode ser autenticada diretamente pelo Azure. 

## <a name="to-add-an-existing-subscription-to-your-azure-ad-directory"></a>Para adicionar uma assinatura existente ao diretório do Azure AD
Você deve entrar com uma conta existente tanto no diretório atual ao qual a assinatura está associada quanto no diretório ao qual deseja adicioná-la. 

1. Entre no [Centro de Contas do Azure](https://account.azure.com/Subscriptions) com uma conta que seja o Administrador da Conta da assinatura cuja propriedade você deseja transferir.
2. Certifique-se de que o usuário que você deseja que seja o proprietário da assinatura esteja no diretório de destino.
3. Clique em **Transferir assinatura**.
4. Especifique o destinatário. O destinatário recebe automaticamente um email com um link de aceitação.
5. O destinatário clica no link e segue as instruções, incluindo inserir suas informações de pagamento. Quando o destinatário for bem-sucedido, a assinatura será transferida. 
6. O diretório padrão da assinatura é alterado para o diretório no qual o usuário está.

Para saber mais, consulte [Transferir a propriedade de assinatura do Azure para outra conta](../billing/billing-subscription-transfer.md)

## <a name="suggestions-to-manage-both-a-subscription-and-a-directory"></a>Sugestões para gerenciar uma assinatura e um diretório
As funções administrativas para uma assinatura do Azure gerenciam recursos vinculados à assinatura do Azure. Esta seção explica as diferenças entre os administradores de assinatura do Azure e os administradores de diretório do Azure AD. Funções administrativas e outras sugestões para usá-las para gerenciar sua assinatura estão descritas em [Atribuindo funções de administrador no Azure Active Directory](active-directory-assign-admin-roles.md).

Por padrão, a função de administrador de serviços é atribuída a você quando você se inscreve. Se outros usuários precisarem entrar e acessar serviços usando a mesma assinatura, você pode adicioná-los como coadministradores. 

O AD do Azure tem um conjunto diferente de funções administrativas para gerenciar o diretório e os recursos relacionados à identidade. Por exemplo, o administrador global de um diretório pode adicionar usuários e grupos ao diretório ou exigir autenticação multifator para usuários. A função de administrador global é atribuída a um usuário que cria um diretório e ele pode atribuir funções administrativas a outros usuários. Funções administrativas do AD do Azure também são usadas por outros serviços, como o Office 365 e Microsoft Intune. 

Mas o ponto importante aqui é que os administradores de assinatura do Azure e os administradores de diretório do Azure AD são duas funções separadas. 
* Os administradores de assinatura do Azure podem gerenciar recursos no Azure e podem usar o Azure AD no portal do Azure (porque o portal do Azure em si é um recurso do Azure). 
* Os administradores de diretório podem gerenciar propriedades somente no diretório do Azure AD.

Uma pessoa pode desempenhar ambas as funções, mas isso não é obrigatório. Um administrador global do diretório não pode receber a atribuição de administrador de serviços ou coadministrador de uma assinatura do Azure, ou vice-versa. Sem ser um administrador da assinatura, o usuário pode entrar no portal do Azure, mas não é possível gerenciar os diretórios para essa assinatura no portal. No entanto, esse usuário pode gerenciar diretórios usando outras ferramentas como o Azure AD PowerShell ou o Centro de Administração do Office 365.

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre como alterar administradores para uma assinatura do Azure, consulte [Transferir a propriedade de uma assinatura do Azure para outra conta](../billing/billing-subscription-transfer.md)
* Para saber mais sobre como o acesso aos recursos é controlado no Microsoft Azure, confira [Noções básicas sobre o acesso aos recursos do Azure](active-directory-understanding-resource-access.md)
* Para saber sobre como atribuir funções no AD do Azure, veja [Atribuindo funções de administrador no Active Directory do Azure](active-directory-assign-admin-roles-azure-portal.md)

<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG

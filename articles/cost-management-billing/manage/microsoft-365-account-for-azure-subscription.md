---
title: Inscrever-se no Azure com uma conta do Microsoft 365
description: Saiba como criar uma assinatura do Azure usando uma conta do Microsoft 365. Você também pode associar contas existentes do Azure e do Microsoft 365 entre si.
author: JiangChen79
ms.reviewer: adwise
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 255fbe4036c83618dd0131b262d13709ab7f9c2f
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131880"
---
# <a name="sign-up-for-an-azure-subscription-with-your-microsoft-365-account"></a>Inscreva-se para obter uma assinatura do Azure com sua conta do Microsoft 365

Se tiver uma assinatura do Microsoft 365, você poderá usar sua conta do Microsoft 365 para criar uma assinatura do Azure. Entre no [Portal do Azure](https://portal.azure.com/) usando seu nome de usuário e a senha do Microsoft 365. No entanto, se você desejar configurar máquinas virtuais ou usar outros serviços do Azure, deverá inscrever-se para uma assinatura do Azure. Compartilhe sua assinatura do Azure com outras pessoas e [use o Azure RBAC (controle de acesso baseado em função) para gerenciar o acesso à sua assinatura e aos seus recursos do Azure](../../role-based-access-control/role-assignments-portal.md).

Se já tiver uma conta do Microsoft 365 e uma assinatura do Azure, confira [Associar um locatário do Microsoft 365 a uma assinatura do Azure](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="get-an-azure-subscription-using-your-microsoft-365-account"></a>Obter uma assinatura do Azure usando sua conta do Microsoft 365

Economize tempo e evite a proliferação de contas inscrevendo-se no Azure usando seu nome de usuário e senha do Microsoft 365.

1. Inscreva-se em [Azure.com](https://account.azure.com/signup?offer=MS-AZR-0044p&appId=docs).
2. Conecte-se usando seu nome de usuário e a senha do Microsoft 365. A conta usada não precisa ter permissões de administrador. Caso tenha mais de uma conta do Microsoft 365, lembre-se de usar as credenciais da conta que deseja associar à sua assinatura do Azure.

   ![Captura de tela que mostra a página de conexão.](./media/microsoft-365-account-for-azure-subscription/billing-sign-in-with-office-365-account.png)

3. Insira as informações necessárias e conclua o processo de inscrição. Se você já tiver uma conta do Microsoft 365, talvez algumas informações não sejam necessárias.

    ![Captura de tela que mostra o formulário de inscrição.](./media/microsoft-365-account-for-azure-subscription/billing-azure-sign-up-fill-information.png)

- Se precisar adicionar outras pessoas de sua organização à assinatura do Azure, consulte [Introdução ao gerenciamento de acesso no Portal do Azure](../../role-based-access-control/overview.md).

## <a name=""></a><a id="more-about-subs">Mais informações sobre as assinaturas do Azure e do Microsoft 365</a>

O Microsoft 365 e o Azure usam o serviço do Azure AD para gerenciar usuários e assinaturas. O diretório do Azure é como um contêiner no qual você pode agrupar usuários e assinaturas. Para usar as mesmas contas de usuário para suas assinaturas do Azure e do Microsoft 365, as assinaturas do Azure precisam ter sido criadas no mesmo diretório que as do Microsoft 365. Tenha em mente os seguintes pontos:

* Uma assinatura é criada em um diretório
* Os usuários pertencem aos diretórios
* Uma assinatura chega ao diretório do usuário que cria a assinatura. Portanto, a assinatura do Microsoft 365 está vinculada à mesma conta da assinatura do Azure.
* As assinaturas do Azure pertencem a usuários individuais no diretório
* As assinaturas do Microsoft 365 pertencem o próprio diretório. Os usuários com as permissões corretas no diretório podem gerenciar essas assinaturas.

![Captura de tela que mostra a relação do diretório, dos usuários e das assinaturas.](./media/microsoft-365-account-for-azure-subscription/19-background-information.png)

Para saber mais sobre, confira [Como as assinaturas do Azure são associadas ao Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Caso tenha dúvidas ou precise de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

- Compartilhe sua assinatura do Azure com outras pessoas e [use o Azure RBAC (controle de acesso baseado em função) para gerenciar o acesso à sua assinatura e aos seus recursos do Azure](../../role-based-access-control/role-assignments-portal.md).
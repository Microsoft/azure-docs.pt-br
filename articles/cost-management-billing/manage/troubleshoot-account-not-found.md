---
title: Solucionar problemas de exibição da sua conta de cobrança no portal do Azure
description: Este artigo ajuda a solucionar problemas ao tentar ver sua conta de cobrança no portal do Azure.
author: amberbhargava
ms.reviewer: banders
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 71561f6ecdde09f259e54260e7bab4dcc0e85c98
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132628"
---
# <a name="troubleshoot-viewing-your-billing-account-in-the-azure-portal"></a>Solucionar problemas de exibição da sua conta de cobrança no portal do Azure

Uma conta de cobrança é criada quando você se inscreve para usar o Azure. Você usa a conta de cobrança para gerenciar faturas, pagamentos e acompanhar os custos. Você pode ter acesso a várias contas de cobrança. Por exemplo, você pode ter se inscrito no Azure para uso pessoal. Você também pode ter acesso ao Azure por meio do Contrato Enterprise de sua organização ou do Contrato de Cliente da Microsoft. Para cada um desses cenários, você terá uma conta de cobrança separada. Este artigo ajuda a solucionar problemas ao tentar ver sua conta de cobrança no portal do Azure.

Você pode ver suas contas de cobrança em uma página de [Gerenciamento de Custos do Azure + Cobrança](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade).

Para saber mais sobre contas de cobrança e identificar seu tipo conta de cobrança, confira [Exibir contas de cobrança no portal do Azure](view-all-accounts.md).

Se não for possível ver sua conta de cobrança no portal do Azure, tente as seguintes opções:

## <a name="sign-in-to-a-different-tenant"></a>Entrar em um locatário diferente

Sua conta de cobrança está associada a um só locatário do Azure Active Directory. Você não verá sua conta de cobrança na página Gerenciamento de Custos + Cobrança se você estiver conectado a um locatário incorreto. Use as etapas a seguir para alternar para outro locatário no portal do Azure e ver suas contas de cobrança nesse locatário.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione seu perfil (endereço de email) na parte superior direita da página.
1. Selecione **Mudar diretório**.  
    ![Captura de tela que mostra a seleção do diretório de comutador no portal](./media/troubleshoot-account-not-found/select-switch-directory.png)
1. Selecione um diretório na seção **Todos os diretórios**.  
    ![Captura de tela que mostra a seleção de um diretório no portal](./media/troubleshoot-account-not-found/select-directory.png)

## <a name="sign-in-with-a-different-email-address"></a>Entrar com um endereço de email diferente

Alguns usuários têm vários endereços de email para entrar no [portal do Azure](https://portal.azure.com). Nem todos os endereços de email têm acesso a uma conta de cobrança. Se você entrar com um endereço de email que tenha permissões para gerenciar recursos, mas não tiver permissões para exibir uma conta de cobrança, não verá a conta de cobrança na página [Gerenciamento de Custos + Cobrança](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade) no portal do Azure.

Entre no portal do Azure com um endereço de email que tenha permissão para a conta de cobrança para acessar sua conta de cobrança.

## <a name="sign-in-with-a-different-identity"></a>Entrar com uma identidade diferente

Alguns usuários têm duas identidades com o mesmo endereço de email: uma conta corporativa ou de estudante e uma conta pessoal. Normalmente, apenas uma das identidades tem permissões para exibir uma conta de cobrança. Você pode ter duas identidades com um endereço de email. Quando você entra com uma identidade que não tem permissão para ver uma conta de cobrança, você não verá a conta de cobrança na página [Gerenciamento de Custos + Cobrança](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade). Use as seguintes etapas para alternar sua identidade:

1. Entre no [portal do Azure](https://portal.azure.com) em uma janela InPrivate/Incognito.
1. Se o endereço de email tiver duas identidades, você verá uma opção para selecionar uma conta pessoal ou uma conta corporativa ou de estudante. Selecione uma das contas.
1. Se você não conseguir ver a conta de cobrança na página Gerenciamento de Custos + Cobrança no portal do Azure, repita as etapas 1 e 2 e selecione a outra identidade.

## <a name="contact-us-for-help"></a>Entrar em contato conosco para obter ajuda

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Próximas etapas

Leia os artigos de cobrança e assinatura a seguir para ajudar a solucionar problemas.

- [Cartão recusado](./troubleshoot-declined-card.md)
- [Problemas de entrada de assinatura](./troubleshoot-sign-in-issue.md)
- [Nenhuma assinatura encontrada](./no-subscriptions-found.md)
- [Exibição do custo da empresa desabilitado](./enterprise-mgmt-grp-troubleshoot-cost-view.md)
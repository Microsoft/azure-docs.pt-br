---
title: Não há mais suporte para SMS bidirecional-Azure Active Directory
description: Explica como habilitar outro método para usuários que ainda usam SMS bidirecional.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/02/2021
ms.author: justinha
author: rhicock
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: d25ed1e46823ec6d820addf3944c96c97fcabcb8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101689021"
---
# <a name="two-way-sms-unsupported"></a>SMS bidirecional sem suporte

O SMS bidirecional para o servidor de autenticação multifator (MFA) do Azure AD foi originalmente preterido em 2018 e não tem mais suporte após 24 de fevereiro de 2021. Os administradores devem habilitar outro método para os usuários que ainda usam SMS bidirecional.

Notificações por email e notificações de integridade do serviço de portal do Azure (torradeiras do Portal) foram enviadas para administradores afetados em 8 de dezembro de 2020 e 28 de janeiro de 2021. Os alertas foram enviados para as funções proprietário, coproprietário, administrador e RBAC do administrador de serviço vinculadas às assinaturas. Se você já tiver concluído as etapas a seguir, nenhuma ação será necessária.

## <a name="required-actions"></a>Ações necessárias

1. Habilite o aplicativo móvel para seus usuários, caso ainda não tenha feito isso. Para obter mais informações, consulte [habilitar a autenticação de aplicativo móvel com o servidor MFA](howto-mfaserver-deploy-mobileapp.md).
1. Notifique seus usuários finais para visitar o portal do [usuário](howto-mfaserver-deploy-userportal.md) do servidor MFA para ativar o aplicativo móvel. O [aplicativo Microsoft Authenticator](https://www.microsoft.com/en-us/account/authenticator) é a opção de verificação recomendada, pois é mais seguro do que o SMS de duas vias. Para obter mais informações, consulte [a hora de desligar os transportes de telefone para autenticação](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/it-s-time-to-hang-up-on-phone-transports-for-authentication/ba-p/1751752).
1. Altere as configurações de usuário da mensagem de texto bidirecional para o aplicativo móvel como o método padrão.

## <a name="faq"></a>Perguntas frequentes

### <a name="what-if-i-dont-change-the-default-method-from-two-way-sms-to-the-mobile-app"></a>E se eu não alterar o método padrão do SMS bidirecional para o aplicativo móvel?
O SMS bidirecional falha após 24 de fevereiro de 2021. Os usuários verão um erro ao tentar entrar e passar MFA.

### <a name="how-do-i-change-the-user-settings-from-two-way-text-message-to-mobile-app"></a>Como fazer alterar as configurações de usuário de uma mensagem de texto bidirecional para o aplicativo móvel?

Você deve alterar as configurações do usuário seguindo estas etapas:

1. No servidor MFA, filtre a lista de usuários para a mensagem de texto bidirecional.
1. Selecione todos os usuários.
1. Abra a caixa de diálogo Editar usuários.
1. Alterar usuários de mensagem de texto para aplicativo móvel.

   ![Captura de tela de usuários finais](media/how-to-authentication-two-way-sms-unsupported/end-users.png)

### <a name="do-my-users-need-to-take-any-action-if-yes-how"></a>Meus usuários precisam realizar qualquer ação? Em caso afirmativo, como?
Sim. Os usuários finais precisam visitar seu portal de usuário do servidor MFA específico para ativar o aplicativo móvel, caso ainda não tenham feito isso. Depois de concluir a etapa 3, todos os usuários que não visitaram o portal do usuário para configurar o aplicativo móvel iniciarão a falha de entrada até que visitem o portal do usuário para se registrar novamente.

### <a name="what-if-my-users-cant-install-the-mobile-app-what-other-options-do-they-have"></a>E se meus usuários não conseguirem instalar o aplicativo móvel? O que as outras opções têm?
A alternativa ao SMS bidirecional ou ao aplicativo móvel é uma chamada telefônica. No entanto, o aplicativo Microsoft Authenticator é o método de verificação recomendado.

### <a name="will-one-way-sms-be-deprecated-as-well"></a>Um SMS unidirecional será preterido também?
Não, apenas o SMS de duas vias está sendo preterido. Para o servidor MFA, o SMS unidirecional funciona para um subconjunto de cenários:

- Adaptador de AD FS
- Autenticação do IIS (requer o portal do usuário e a configuração)
- RADIUS (exige que os clientes RADIUS ofereçam suporte ao desafio de acesso e que o protocolo PAP seja usado)

Há limitações para quando o SMS unidirecional pode ser usado para tornar o aplicativo móvel uma alternativa melhor, pois ele não requer o prompt de código de verificação.
Se você ainda quiser usar o SMS unidirecional em alguns cenários, poderá deixar os seguintes marcados, mas alterar a seção de **configurações da empresa** , a guia **geral** , o **usuário usará como padrão a mensagem de texto** para **uma forma** unidirecional, em vez de **bidirecional**. Por fim, se você usar a sincronização de diretório que usa como padrão o SMS, precisaria alterá-lo para One-Way em vez de bidirecional.

### <a name="how-can-i-check-which-users-are-still-using-two-way-sms"></a>Como posso verificar quais usuários ainda estão usando SMS bidirecional?
Para listar esses usuários, inicie o **servidor MFA**, selecione a seção **usuários** , clique em **Filtrar lista de usuários** e filtre a **mensagem de texto duas vias**.

### <a name="how-do-we-hide-two-way-sms-as-an-option-in-the-mfa-portal-to-prevent-users-from-selecting-it-in-the-future"></a>Como é possível ocultar o SMS bidirecional como uma opção no portal do MFA para impedir que os usuários os selecionem no futuro?
No portal do usuário do servidor MFA, clique em **configurações**, você pode limpar a **mensagem de texto** para que ela não esteja disponível. O mesmo é verdadeiro na seção **AD FS** se você estiver usando AD FS para o registro de usuário.


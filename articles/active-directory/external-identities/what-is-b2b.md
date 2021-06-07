---
title: O que é a Colaboração B2B do Azure Active Directory?
description: A colaboração B2B do Azure Active Directory é compatível com o acesso de usuários convidados. Dessa maneira, é possível compartilhar recursos com segurança e colaborar com parceiros externos.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 03/19/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 231d3ab81c2376aa8a2a4bf182e1b5e9799e51d5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669773"
---
# <a name="what-is-guest-user-access-in-azure-active-directory-b2b"></a>O que é o acesso de usuários convidados na colaboração B2B do Azure Active Directory?

A colaboração B2B (entre empresas) do Azure AD (Azure Active Directory) é um recurso em identidades externas que permite convidar usuários convidados a colaborar com a sua organização. Com a colaboração B2B, você pode compartilhar com segurança os aplicativos e serviços da sua empresa com usuários convidados de qualquer outra organização, mantendo o controle sobre seus próprios dados corporativos. Trabalhe de forma segura com parceiros externos, de grande ou pequeno porte, mesmo se eles não tiverem o Azure AD ou um departamento de TI. Um processo de convite e resgate simples permite que os parceiros usem suas próprias credenciais para acessar recursos da empresa. Os desenvolvedores podem usar as APIs entre empresas do Azure AD para personalizar o processo de convite ou escrever aplicativos como portais de inscrição de autoatendimento. Para obter informações sobre licenciamento e preços relacionadas a usuários convidados, confia [preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).  

> [!IMPORTANT]
> - **A partir de 4 de janeiro de 2021**, o Google está [preterindo o suporte de entrada do WebView](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Se estiver usando a federação do Google ou a inscrição por autoatendimento com o Gmail, você deverá [testar seus aplicativos nativos de linha de negócios para garantir a compatibilidade](google-federation.md#deprecation-of-webview-sign-in-support).
> - **A partir de outubro de 2021**, a Microsoft não dará mais suporte ao resgate de convites criando contas e locatários do Azure AD não gerenciados para cenários de colaboração B2B. Durante a preparação, incentivamos os clientes a aceitarem a [autenticação de senha avulsa por email](one-time-passcode.md). Agradecemos seus comentários sobre essa versão prévia do recurso pública e estamos empolgados em criar ainda mais maneiras de colaborar.

## <a name="collaborate-with-any-partner-using-their-identities"></a>Colabore com qualquer parceiro usando as identidades deles

Com o Azure AD B2B, o parceiro usa sua própria solução de gerenciamento de identidades, portanto, não há nenhuma sobrecarga administrativa externa para a organização. Os usuários convidados entram nos aplicativos e serviços com as próprias identidades empresariais, estudantis ou sociais.

- O parceiro usa suas próprias identidades e credenciais; o Azure AD não é necessário.
- Não é necessário gerenciar contas ou senhas externas.
- Não é necessário sincronizar contas ou gerenciar ciclos de vida de contas.  

## <a name="easily-invite-guest-users-from-the-azure-ad-portal"></a>Convide facilmente os usuários convidados do portal do Azure AD

Como administrador, é possível adicionar facilmente usuários convidados à sua organização no portal do Azure.

- [Crie um usuário convidado](b2b-quickstart-add-guest-users-portal.md) no Azure AD, do mesmo modo que você adicionaria um novo usuário.
- Atribua usuários convidados a aplicativos ou grupos.
- Envie um email de convite que contém um link de resgate ou envie um link direto para um aplicativo que você deseja compartilhar.

![Captura de tela mostrando a página de entrada de convite Novo Usuário Convidado](media/what-is-b2b/add-a-b2b-user-to-azure-portal.png)

- Os usuários convidados precisam seguir algumas [etapas de resgate](redemption-experience.md) simples para entrar.

![Captura de tela mostrando a página Analisar as permissões](media/what-is-b2b/consentscreen.png)


## <a name="use-policies-to-securely-share-your-apps-and-services"></a>Usar políticas para compartilhar aplicativos e serviços com segurança

É possível usar as políticas de autorização para proteger seu conteúdo corporativo. Políticas de Acesso Condicional, como a autenticação multifator, podem ser aplicadas:

- No nível do locatário.
- No nível do aplicativo.
- Para que usuários convidados específicos protejam dados e aplicativos corporativos.

![Captura de tela mostrando a opção Acesso condicional](media/what-is-b2b/tutorial-mfa-policy-2.png)



## <a name="let-application-and-group-owners-manage-their-own-guest-users"></a>Permitir que o aplicativo e os proprietários de grupos gerenciem os próprios usuários convidados

É possível delegar o gerenciamento de usuários convidados para proprietários de aplicativos. Dessa forma, eles poderão adicionar os usuários convidados diretamente a qualquer aplicativo que quiserem compartilhar, seja da Microsoft ou não.

- Os administradores configuram o aplicativo de autoatendimento e o gerenciamento de grupos.
- Os não administradores usam o [Painel de Acesso](https://myapps.microsoft.com) para adicionar usuários convidados a aplicativos ou grupos.

![Captura de tela mostrando o painel de acesso para um usuário convidado](media/what-is-b2b/access-panel-manage-app.png)

## <a name="customize-the-onboarding-experience-for-b2b-guest-users"></a>Personalizar a experiência de integração para usuários convidados B2B

Integre seus parceiros externos de modo personalizado às necessidades de sua organização.

- Use o [gerenciamento de direitos do Azure AD](../governance/entitlement-management-overview.md) para configurar políticas que [gerenciam o acesso de usuários externos](../governance/entitlement-management-external-users.md#how-access-works-for-external-users).
- Use as [APIs de convite de colaboração B2B](/graph/api/resources/invitation) para personalizar suas experiências de integração.

## <a name="integrate-with-identity-providers"></a>Integrar-se aos Provedores de identidade

O Azure AD dá suporte a provedores de identidade externos como Facebook, contas da Microsoft, Google ou provedores de identidade corporativa. Você pode configurar a federação com provedores de identidade para que os usuários externos possam entrar com suas contas sociais ou corporativas existentes, em vez de criar uma nova conta apenas para seu aplicativo. Saiba mais sobre os [provedores de identidade para Identidades Externas](identity-providers.md).

![Captura de tela mostrando a página Provedores de identidade](media/what-is-b2b/identity-providers.png)


## <a name="create-a-self-service-sign-up-user-flow"></a>Criar um fluxo do usuário de inscrição para autoatendimento

Com um fluxo de usuários com inscrição por autoatendimento, você pode criar uma experiência de inscrição para usuários externos que desejam acessar seus aplicativos. Como parte do fluxo de inscrição, é possível fornecer opções para diferentes provedores de identidade social ou corporativa, e coletar informações sobre o usuário. Saiba mais sobre a [inscrição por autoatendimento e como configurá-la](self-service-sign-up-overview.md).

Você também pode usar [conectores de API](api-connectors-overview.md) para integrar seus fluxos dos usuários de inscrição por autoatendimento a sistemas de nuvem externos. Você pode se conectar a fluxos de trabalho de aprovação personalizados, executar a verificação de identidade, validar informações fornecidas pelo usuário e muito mais.

![Captura de tela mostrando a página Fluxos dos usuários](media/what-is-b2b/self-service-sign-up-user-flow-overview.png)
<!--TODO: Add screenshot with API connectors -->

## <a name="next-steps"></a>Próximas etapas

- [Preço de identidades externas](external-identities-pricing.md)
- [Adicionar usuários convidados de colaboração B2B ao portal](add-users-administrator.md)
- [Entender o processo de resgate de convites](redemption-experience.md)
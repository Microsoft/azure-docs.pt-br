---
title: Compartilhando contas e credenciais - Azure Active Directory | Microsoft Docs
description: Descreve como o Active Directory do Azure permite que as organizações compartilhem com segurança contas para aplicativos locais e serviços de nuvem do consumidor.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5638710c4c01d361d108cfef69869fad1a7dad1a
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304367"
---
# <a name="sharing-accounts-with-azure-ad"></a>Compartilhar contas com o AD do Azure
## <a name="overview"></a>Visão geral
Às vezes, as organizações precisam usar um único nome de usuário e senha para várias pessoas, o que normalmente acontece em dois casos:

* Ao acessar aplicativos que exigem um logon único e uma senha para cada usuário, seja em aplicativos locais ou em serviços de nuvem do consumidor (por exemplo, contas corporativas de mídias sociais).
* Durante a criação de ambientes de vários usuários. Você pode ter uma conta local única que tenha privilégios elevados e que seja usada para fazer atividades de instalação, administração e recuperação de núcleo. Por exemplo, a conta local do "administrador global" do Office 365 ou a conta raiz no Salesforce.

Tradicionalmente, essas contas são compartilhadas distribuindo as credenciais (nome de usuário e senha) para os indivíduos certos ou armazenando-as em um local compartilhado onde vários agentes confiáveis podem acessá-las.

O modelo tradicional de compartilhamento tem várias desvantagens:

* Habilitar o acesso a novos aplicativos exige que você distribua credenciais para qualquer pessoa que precise de acesso.
* Cada aplicativo compartilhado pode exigir seu próprio conjunto exclusivo de credenciais compartilhadas, exigindo que os usuários se lembrem de vários conjuntos de credenciais. Quando os usuários precisam lembrar de várias credenciais, o risco de eles recorrerem a práticas arriscadas aumenta. (Por exemplo, anotar senhas).
* Você não pode determinar quem tem acesso a um aplicativo.
* Você não pode determinar quem *acessou* um aplicativo.
* Quando você quiser remover o acesso a um aplicativo, é necessário atualizar as credenciais e redistribuí-las para todos que precisarem acessar o aplicativo.

## <a name="azure-active-directory-account-sharing"></a>Compartilhamento de contas do Active Directory do Azure
O AD do Azure fornece uma nova abordagem para usar contas compartilhadas que elimina essas desvantagens.

O administrador do AD do Azure configura quais aplicativos um usuário pode acessar usando o Painel de Acesso e escolhendo o tipo de logon único mais adequado para o aplicativo. Um desses tipos, *logon único baseado em senha*, permite ao AD do Azure agir como um tipo de “agente” durante o processo de logon do aplicativo.

Os usuários fazem logon uma vez com sua conta institucional. Essa conta é a mesma usada regularmente para acessar a área de trabalho ou o e-mail. Eles podem descobrir e acessar apenas os aplicativos aos quais eles estão atribuídos. Com contas compartilhadas, essa lista de aplicativos pode incluir qualquer número de credenciais compartilhadas. O usuário final não precisa se lembrar ou anotar as diversas contas que ele pode estar utilizando.

As contas compartilhadas não apenas aumentam a supervisão e melhoram a utilização, como também aumentam a segurança. Os usuários com permissões para usar as credenciais não vejam a senha compartilhada, mas em vez disso, obtém as permissões para usar a senha como parte de um fluxo de autenticação orquestrado. Além disso, alguns aplicativos de SSO de senha oferecem a opção de usar o Azure Active Directory para substituir (atualizar) periodicamente as senhas. O sistema usa senhas grandes e complexas, o que aumenta a segurança da conta. O administrador pode conceder ou revogar o acesso a um aplicativo facilmente, e também sabe quem tem acesso à conta e quem a acessou no passado.

O Azure Active Directory oferece suporte a contas compartilhadas para qualquer usuário licenciado do Enterprise Mobility Suite (EMS), Premium ou Basic, em todos os tipos de aplicativos de logon único com senha. Você pode compartilhar as contas para qualquer um dos milhares de aplicativos previamente integrados na galeria de aplicativos e pode adicionar seu próprio aplicativo de autenticação de senha com os [aplicativos personalizados de SSO](../manage-apps/configure-single-sign-on-portal.md).

Recursos do AD do Azure que permitem o compartilhamento de contas incluem:

* [Logon único com senha](../manage-apps/what-is-single-sign-on.md#password-based-sso)
* Agente de logon único com senha
* [Atribuição de grupo](groups-self-service-management.md)
* Aplicativos com senha personalizada
* [Painel/relatórios de uso de aplicativo](../active-directory-passwords-get-insights.md)
* Portais de acesso do usuário final
* [Proxy do aplicativo](../manage-apps/application-proxy.md)
* [Marketplace o Active Directory](https://azure.microsoft.com/marketplace/active-directory/all/)

## <a name="sharing-an-account"></a>Compartilhamento de uma conta
Para usar o Azure Active Directory para compartilhar uma conta, você precisará:

* Adicionar uma [galeria de aplicativos](https://azure.microsoft.com/marketplace/active-directory/) do aplicativo ou um [aplicativo personalizado](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/)
* Configurar o aplicativo para logon único (SSO) com senha
* Usar a [atribuição baseada em grupo](groups-saasapps.md) e selecionar a opção para inserir uma credencial compartilhada

Você também pode tornar sua conta compartilhada mais segura com a MFA (Autenticação Multifator) (saiba mais sobre [como proteger os aplicativos com o Azure AD](../authentication/concept-mfa-whichversion.md)) e delegar a capacidade de gerenciar quem tem acesso ao aplicativo usando o gerenciamento de grupos de [autoatendimento do Azure AD](groups-self-service-management.md).

## <a name="related-articles"></a>Artigos relacionados
* [Gerenciamento de aplicativos no Microsoft Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Protegendo aplicativos com acesso condicional](../active-directory-conditional-access-azure-portal.md)
* [Gerenciamento de grupo de autoatendimento/SSAA](groups-self-service-management.md)

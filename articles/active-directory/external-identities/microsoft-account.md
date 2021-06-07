---
title: Provedor de identidade MSA (conta da Microsoft) no Azure AD
description: Use o Azure AD para habilitar um usuário externo (convidado) para entrar em seus aplicativos do Azure AD com seus conta Microsoft (MSA).
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25d3380277d0e653fd5cb03069b7d91cb363dd95
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101692978"
---
# <a name="microsoft-account-msa-identity-provider-for-external-identities-preview"></a>Provedor de identidade MSA (conta da Microsoft) para identidades externas (versão prévia)

> [!NOTE]
> O provedor de identidade da conta da Microsoft é um recurso de visualização pública do Azure Active Directory. Para saber mais sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Seus usuários convidados B2B podem usar suas próprias contas pessoais da Microsoft para colaboração B2B sem configuração adicional. Os usuários convidados podem resgatar seus convites de colaboração B2B ou concluir seus fluxos de usuário de inscrição usando seus conta Microsoft pessoais.

As contas da Microsoft são configuradas por um usuário para obter acesso a produtos e serviços de nuvem da Microsoft orientados ao consumidor, como Outlook, OneDrive, Xbox LIVE ou Microsoft 365. A conta é criada e armazenada no sistema de conta de identidade do consumidor da Microsoft que é executado pela Microsoft.

## <a name="guest-sign-in-using-microsoft-accounts"></a>Entrada de convidado usando contas da Microsoft

A conta da Microsoft está disponível na lista de provedores de identidade de identidades externas por padrão. Nenhuma configuração adicional é necessária para permitir que os usuários convidados entrem com seus conta Microsoft usando o fluxo de convite ou um fluxo de usuário de inscrição de autoatendimento.

![Conta da Microsoft na lista de provedores de identidade](media/microsoft-account/microsoft-account-identity-provider.png)

### <a name="microsoft-account-in-the-invitation-flow"></a>Conta da Microsoft no fluxo de convite

Ao [convidar um usuário convidado](add-users-administrator.md) para a colaboração B2B, você pode especificar seus conta Microsoft como o endereço de email que eles usarão para entrar.

![Convidar usando um conta Microsoft](media/microsoft-account/microsoft-account-invite.png)

### <a name="microsoft-account-in-self-service-sign-up-user-flows"></a>Conta da Microsoft em fluxos de usuário de inscrição de autoatendimento

A conta da Microsoft é uma opção de provedor de identidade para seus fluxos de usuário de inscrição de autoatendimento. Os usuários podem se inscrever para seus aplicativos usando suas próprias contas da Microsoft. Primeiro, você precisará [habilitar a inscrição de autoatendimento](self-service-sign-up-user-flow.md) para seu locatário. Em seguida, você pode configurar um fluxo de usuário para o aplicativo e selecionar conta da Microsoft como uma das opções de entrada.

![conta Microsoft em um fluxo de usuário de inscrição de autoatendimento](media/microsoft-account/microsoft-account-user-flow.png)

## <a name="next-steps"></a>Próximas etapas

- [Adicionar Azure Active Directory usuários de colaboração B2B](add-users-administrator.md)
- [Adicionar inscrição por autoatendimento a um aplicativo](self-service-sign-up-user-flow.md)
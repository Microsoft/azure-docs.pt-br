---
title: Provedores de identidade para Identidades Externas – Azure AD
description: Saiba como usar o Azure AD como seu provedor de identidade padrão para compartilhamento com usuários externos.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdef929b27c636b3908dd7a88eb93adc2382a53f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101687729"
---
# <a name="identity-providers-for-external-identities"></a>Provedores de identidade para Identidades Externas

> [!NOTE]
> Alguns dos recursos mencionados neste artigo são recursos de visualização pública do Azure Active Directory. Para saber mais sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Um *provedor de identidade* cria, mantém e gerencia as informações de identidade, fornecendo serviços de autenticação para aplicativos. Ao compartilhar seus aplicativos e recursos com usuários externos, o Azure AD é o provedor de identidade padrão de compartilhamento. Isso significa que, quando você convida usuários externos que já têm uma conta do Azure AD ou da Microsoft, eles podem entrar automaticamente sem precisar de configuração adicional.

Além das contas do Azure AD, as identidades externas oferecem uma variedade de provedores de identidade.

- **Contas da Microsoft** (versão prévia): os usuários convidados podem usar seus próprios conta Microsoft pessoais (MSA) para resgatar seus convites de colaboração B2B. Ao configurar um fluxo de usuário de inscrição de autoatendimento, você pode adicionar uma [conta da Microsoft (versão prévia)](microsoft-account.md) como um dos provedores de identidade permitidos. Nenhuma configuração adicional é necessária para disponibilizar esse provedor de identidade para fluxos de usuário.

- **Senha de uso único de email** (versão prévia): ao resgatar um convite ou acessar um recurso compartilhado, um usuário convidado pode solicitar um código temporário, que é enviado para seu endereço de email. Em seguida, ele digita esse código para continuar o processo de entrada. O recurso de senha de email de uso único autentica os usuários convidados B2B quando eles não podem ser autenticados por outros meios. Ao configurar um fluxo de usuário de inscrição de autoatendimento, você pode adicionar **Email One-Time senha (versão prévia)** como um dos provedores de identidade permitidos. Algumas configurações são necessárias; consulte [autenticação de senha de uso único de email](one-time-passcode.md).

- **Google**: a Federação do Google permite que usuários externos resgatem convites de você entrando em seus aplicativos com as próprias contas do Gmail. A federação do Google também pode ser usada em seus fluxos de usuário de inscrição para autoatendimento. Consulte como [Adicionar o Google como um provedor de identidade](google-federation.md).
   > [!IMPORTANT]
   > **A partir de 4 de janeiro de 2021**, o Google está [preterindo o suporte de entrada do WebView](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Se estiver usando a federação do Google ou a inscrição por autoatendimento com o Gmail, você deverá [testar seus aplicativos nativos de linha de negócios para garantir a compatibilidade](google-federation.md#deprecation-of-webview-sign-in-support).

- **Facebook**: ao criar um aplicativo, você pode configurar a inscrição para autoatendimento e habilitar a federação do Facebook para que os usuários possam se inscrever no seu aplicativo usando as próprias contas do Facebook. O Facebook só pode ser usado para fluxos de usuário de inscrição para autoatendimento e não está disponível como uma opção de entrada quando os usuários estão resgatando convites de você. Consulte como [Adicionar o Facebook como um provedor de identidade](facebook-federation.md).

- **Federação direta**: você também pode configurar a federação direta com qualquer provedor de identidade externo que dá suporte aos protocolos SAML ou WS-Fed. A federação direta permite que usuários externos resgatem convites de você entrando em seus aplicativos com as contas sociais ou corporativas existentes. Consulte como [Configurar a Federação direta](direct-federation.md).
   > [!NOTE]
   > Os provedores de identidade de federação direta não podem ser usados em seus fluxos de usuário de inscrição para autoatendimento.

## <a name="adding-social-identity-providers"></a>Adicionando provedores de identidade social

O Azure AD é habilitado por padrão para inscrição para autoatendimento, portanto os usuários sempre têm a opção de se inscrever usando uma conta do Azure AD. No entanto, você pode habilitar outros provedores de identidade, incluindo provedores de identidade social, como Google ou Facebook. Para configurar provedores de identidade social em seu locatário do Azure AD, você criará um aplicativo no provedor de identidade e configurará as credenciais. Você obterá uma ID de cliente ou de aplicativo e um segredo de cliente ou de aplicativo, que poderá então adicionar ao seu locatário do Azure AD.

Depois de adicionar um provedor de identidade ao seu locatário do Azure AD:

- Quando você convidar um usuário externo para aplicativos ou recursos em sua organização, o usuário externo pode entrar usando a própria desse provedor de identidade.
- Quando você habilitar a [inscrição por autoatendimento](self-service-sign-up-overview.md) para seus aplicativos, os usuários externos poderão se inscrever em seus aplicativos usando as próprias contas junto aos provedores de identidade que você adicionou. Eles poderão selecionar entre as opções de provedores de identidade social que você fez disponíveis na página de inscrição:

   ![Captura de tela mostrando as opções de credenciais do Google e do Facebook](media/identity-providers/sign-in-with-social-identity.png)

Para obter uma experiência de entrada ideal, faça a federação com provedores de identidade sempre que possível para que você possa dar aos seus convidados uma experiência de conexão perfeita ao acessar seus aplicativos.  

## <a name="next-steps"></a>Próximas etapas

Para saber como adicionar provedores de identidade para entrar em seus aplicativos, consulte os seguintes artigos:

- [Adicionar autenticação de senha de uso único de email](one-time-passcode.md)
- [Adicionar o Google](google-federation.md) como um provedor de identidade social permitido
- [Adicionar o Facebook](facebook-federation.md) como um provedor de identidade social permitido
- [Configure a federação direta](direct-federation.md) com qualquer organização cujo provedor de identidade dê suporte ao protocolo SAML 2.0 ou WS-Fed. A federação direta não é uma opção para fluxos de usuário com inscrição por autoatendimento.

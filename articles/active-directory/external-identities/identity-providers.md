---
title: Provedores de identidade para Identidades Externas – Azure AD
description: Saiba como usar o Azure AD como seu provedor de identidade padrão para compartilhamento com usuários externos.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ead05598c6ca4d096e1a68c8d640938ecd771c2
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2020
ms.locfileid: "97355504"
---
# <a name="identity-providers-for-external-identities"></a>Provedores de identidade para Identidades Externas

Um *provedor de identidade* cria, mantém e gerencia as informações de identidade, fornecendo serviços de autenticação para aplicativos. Ao compartilhar seus aplicativos e recursos com usuários externos, o Azure AD é o provedor de identidade padrão de compartilhamento. Isso significa que, quando você convida usuários externos que já têm uma conta do Azure AD ou da Microsoft, eles podem entrar automaticamente sem precisar de configuração adicional.

No entanto, você pode permitir que os usuários entrem com vários provedores de identidade.

- **Google**: a Federação do Google permite que usuários externos resgatem convites de você entrando em seus aplicativos com as próprias contas do Gmail. A federação do Google também pode ser usada em seus fluxos de usuário de inscrição para autoatendimento.
   > [!IMPORTANT]
   > A **partir de 4 de janeiro de 2021**, o Google está [preterindo o suporte de entrada do WebView](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Se você estiver usando a inscrição do Google Federation ou autoatendimento com o Gmail, deverá [testar seus aplicativos nativos de linha de negócios para compatibilidade](google-federation.md#deprecation-of-webview-sign-in-support).

   > [!NOTE]
   > Na versão prévia de inscrição para autoatendimento atual, se um fluxo de usuário estiver associado a um aplicativo e você enviar um convite desse aplicativo para um usuário, ele não poderá usar uma conta do Gmail para resgatar o convite. Como alternativa, o usuário pode passar pelo processo de inscrição de autoatendimento. Ou ele pode resgatar o convite acessando um aplicativo diferente ou usando o portal Meus Aplicativos em https://myapps.microsoft.com.

- **Facebook**: ao criar um aplicativo, você pode configurar a inscrição para autoatendimento e habilitar a federação do Facebook para que os usuários possam se inscrever no seu aplicativo usando as próprias contas do Facebook. O Facebook só pode ser usado para fluxos de usuário de inscrição para autoatendimento e não está disponível como uma opção de entrada quando os usuários estão resgatando convites de você.

- **Federação direta**: você também pode configurar a federação direta com qualquer provedor de identidade externo que dá suporte aos protocolos SAML ou WS-Fed. A federação direta permite que usuários externos resgatem convites de você entrando em seus aplicativos com as contas sociais ou corporativas existentes. 
   > [!NOTE]
   > Os provedores de identidade de federação direta não podem ser usados em seus fluxos de usuário de inscrição para autoatendimento.


## <a name="how-it-works"></a>Como ele funciona

O recurso de inscrição para autoatendimento de identidades externas do Azure AD permite que os usuários se inscrevam com as conta do Azure AD, Google ou Facebook deles. Para configurar provedores de identidade social em seu locatário do Azure AD, você criará um aplicativo em cada provedor de identidade e vai configurar as credenciais. Você obterá uma ID de cliente ou de aplicativo e um segredo de cliente ou de aplicativo, que poderá então adicionar ao seu locatário do Azure AD.

Depois de adicionar um provedor de identidade ao seu locatário do Azure AD:

- Quando você convidar um usuário externo para aplicativos ou recursos em sua organização, o usuário externo pode entrar usando a própria desse provedor de identidade.
- Quando você habilitar a [inscrição por autoatendimento](self-service-sign-up-overview.md) para seus aplicativos, os usuários externos poderão se inscrever em seus aplicativos usando as próprias contas junto aos provedores de identidade que você adicionou.

> [!NOTE]
> O Azure AD é habilitado por padrão para inscrição para autoatendimento, portanto os usuários sempre têm a opção de se inscrever usando uma conta do Azure AD.

Ao resgatar seu convite ou se inscrever no seu aplicativo, o usuário externo terá a opção de entrar e se autenticar no provedor de identidade social:

![Captura de tela mostrando as opções de credenciais do Google e do Facebook](media/identity-providers/sign-in-with-social-identity.png)

Para obter uma experiência de entrada ideal, faça a federação com provedores de identidade sempre que possível para que você possa dar aos seus convidados uma experiência de conexão perfeita ao acessar seus aplicativos.  

## <a name="next-steps"></a>Próximas etapas

Para saber como adicionar provedores de identidade para entrar em seus aplicativos, consulte os seguintes artigos:

- [Adicionar o Google](google-federation.md) à sua lista de provedores de identidade social
- [Adicionar o Facebook](facebook-federation.md) à sua lista de provedores de identidade social
- [Configure a federação direta](direct-federation.md) com qualquer organização cujo provedor de identidade dê suporte ao protocolo SAML 2.0 ou WS-Fed. A federação direta não é uma opção para fluxos de usuário com inscrição por autoatendimento.

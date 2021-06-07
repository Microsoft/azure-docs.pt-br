---
title: Problemas no Internet Explorer e no Microsoft Edge (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre os problemas conhecidos ao usar a Biblioteca de Autenticação da Microsoft para JavaScript (MSAL.js) com os navegadores Internet Explorer e Microsoft Edge.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/18/2020
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 2a471504b88791b5bfb6ce6cc7c81d60bfbe5028
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "83772073"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>Problemas conhecidos nos navegadores Internet Explorer e Microsoft Edge (MSAL.js)

## <a name="issues-due-to-security-zones"></a>Problemas devido a zonas de segurança
Recebemos vários relatórios de problemas com a autenticação no IE e no Microsoft Edge (desde a atualização da *versão do navegador Microsoft Edge para 40.15063.0.0*). Estamos monitorando esses problemas e já informamos a equipe do Microsoft Edge. Embora o Microsoft Edge funcione em uma resolução, veja uma descrição dos problemas mais frequentes e as possíveis soluções que podem ser implementadas.

### <a name="cause"></a>Causa
Veja a seguir a causa da maioria desses problemas. Os armazenamentos de sessão e local são particionados por zonas de segurança no navegador Microsoft Edge. Nesta versão específica do Microsoft Edge, quando o aplicativo é redirecionado entre zonas, os armazenamentos de sessão e local são limpos. Especificamente, o armazenamento de sessão é limpo na navegação normal, e tanto o armazenamento de sessão quanto o local são limpos na navegação InPrivate. A MSAL.js salva um determinado estado no armazenamento de sessão e verifica esse estado durante os fluxos de autenticação. Quando o armazenamento de sessão é limpo, esse estado é perdido e, portanto, resulta em experiências ruins.

### <a name="issues"></a>Problemas

- **Loops de redirecionamento infinitos e recarregamentos de página durante a autenticação**. Quando os usuários entram no aplicativo no Microsoft Edge, são redirecionados de volta da página de entrada do AAD e ficam presos em um loop de redirecionamento infinito, que faz com que a página seja recarregada repetidamente. Normalmente, também ocorre um erro `invalid_state` no armazenamento de sessão.

- **Loops de token de aquisição infinitos e erro AADSTS50058**. Quando um aplicativo em execução no Microsoft Edge tenta adquirir um token para um recurso, o aplicativo pode ficar preso em um loop infinito da chamada de aquisição de token junto com o seguinte erro do AAD no rastreamento de rede:

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- **A janela pop-up não fecha ou fica presa ao usar o logon por meio de Pop-up para autenticar**. Ao autenticar por meio da janela pop-up no Microsoft Edge ou IE (InPrivate), depois de inserir as credenciais e entrar, se vários domínios entre zonas de segurança estiverem envolvidos na navegação, a janela pop-up não fechará porque a MSAL.js perde o identificador para a janela pop-up.  

### <a name="update-fix-available-in-msaljs-023"></a>Atualização: Correção disponível na MSAL.js 0.2.3
Correções para os problemas de loop de redirecionamento de autenticação foram liberadas na [MSAL.js 0.2.3](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases). Habilite o sinalizador `storeAuthStateInCookie` na configuração da MSAL.js para usar a correção. Por padrão, esse sinalizador é definido como false.

Quando o sinalizador `storeAuthStateInCookie` está habilitado, a MSAL.js usa os cookies do navegador para armazenar o estado de solicitação exigido para validação dos fluxos de autenticação.

> [!NOTE]
> Essa correção ainda não está disponível para os wrappers msal-angular e msal-angularjs. Essa correção não resolve o problema das janelas pop-up.

Use as soluções alternativas a seguir.

#### <a name="other-workarounds"></a>Outras soluções alternativas
Verifique se o problema ocorre somente na versão específica do navegador Microsoft Edge e se funciona nos outros navegadores antes de usar estas soluções alternativas.  
1. A primeira etapa para solucionar esses problemas é garantir que o domínio do aplicativo e outros sites envolvidos nos redirecionamentos do fluxo de autenticação sejam adicionados como sites confiáveis às configurações de segurança do navegador. Assim, eles pertencerão à mesma zona de segurança.
Para fazer isso, siga estas etapas:
    - Abra o **Internet Explorer** e clique em **Configurações** (ícone de engrenagem) no canto superior direito
    - Clique em **Opções da Internet**
    - Selecione a guia **Segurança**
    - Na opção **Sites confiáveis**, clique no botão **Sites** e adicione as URLs à caixa de diálogo que se abre.

2. Já mencionamos que como somente o armazenamento de sessão é limpo durante a navegação normal, você pode configurar a MSAL.js para usar o armazenamento local. Isso pode ser definido como o parâmetro configuração `cacheLocation` ao inicializar a MSAL.

Isso não resolverá o problema da navegação InPrivate, pois ambos os armazenamentos estão limpos, de sessão e local.

## <a name="issues-due-to-popup-blockers"></a>Problemas devido a bloqueadores de pop-ups

Existem casos em que os pop-ups são bloqueados no IE ou no Microsoft Edge. Por exemplo, quando um segundo pop-up ocorre durante a [autenticação multifator](../authentication/concept-mfa-howitworks.md). Você receberá um alerta no navegador para permitir o pop-up uma vez ou sempre. Se você optar por permitir, o navegador abrirá a janela pop-up automaticamente e retornará um identificador `null` para ele. Como resultado, a biblioteca não tem um identificador para a janela e não há como fechar a janela pop-up. O mesmo problema não acontece no Chrome quando ele solicita que você permita pop-ups, porque ele não abre uma janela pop-up automaticamente.

Como **solução alternativa**, os desenvolvedores precisarão permitir pop-ups no IE e no Microsoft Edge antes de começarem a usar o aplicativo para evitar esse problema.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como [Usar a MSAL.js no Internet Explorer](msal-js-use-ie-browser.md).

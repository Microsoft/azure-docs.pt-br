---
title: Como lidar com a ITP (proteção de rastreamento inteligente) no Safari | Azure
titleSuffix: Microsoft identity platform
description: Autenticação do aplicativo de página única (SPA) quando cookies de terceiros não são mais permitidos.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.author: hirsin
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: eed4e919684575bb2c63170d91517b661fac4acf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98753964"
---
# <a name="handle-itp-in-safari-and-other-browsers-where-third-party-cookies-are-blocked"></a>Controle o ITP no Safari e em outros navegadores em que os cookies de terceiros são bloqueados

Atualmente, muitos navegadores estão bloqueando cookies de terceiros, cookies em solicitações para domínios que não são iguais aos mostrados na barra de navegadores. Isso interrompe o fluxo implícito e requer novos padrões de autenticação para conectar os usuários com êxito. Na plataforma de identidade da Microsoft, usamos o fluxo de autorização com PKCE e tokens de atualização para manter os usuários conectados quando cookies de terceiros são bloqueados.

## <a name="what-is-intelligent-tracking-protection-itp"></a>O que é ITP (proteção de rastreamento inteligente)?

O Apple Safari tem um recurso de proteção de privacidade por padrão chamado de [Proteção de rastreamento inteligente](https://webkit.org/tracking-prevention-policy/) ou *ITP*. O ITP bloqueia cookies de "terceiros", cookies em solicitações entre domínios.

Uma forma comum de rastreamento de usuário é feita carregando um iframe para um site de terceiros em segundo plano e usando cookies para correlacionar o usuário pela Internet. Infelizmente, esse padrão também é a maneira padrão de implementar o [fluxo implícito](v2-oauth2-implicit-grant-flow.md) em aplicativos de página única (SPAs). Quando um navegador bloqueia cookies de terceiros para impedir o rastreamento de usuários, os SPAs também são quebrados.

O Safari não está sozinho no bloqueio de cookies de terceiros para aprimorar a privacidade do usuário. O Brave bloqueou cookies de terceiros por padrão e o Chromium (a plataforma por trás do Google Chrome e do Microsoft Edge) anunciou que eles também deixarão de oferecer suporte a cookies de terceiros no futuro.

A solução descrita neste artigo funciona em todos esses navegadores ou em qualquer lugar em que os cookies de terceiros sejam bloqueados.

## <a name="overview-of-the-solution"></a>Visão geral da solução

Para continuar autenticando os usuários no SPAs, os desenvolvedores de aplicativos devem usar o [fluxo de código de autorização](v2-oauth2-auth-code-flow.md). No fluxo do código de autenticação, o provedor de identidade emite um código, e o SPA resgata o código para um token de acesso e um token de atualização. Quando o aplicativo requer tokens adicionais, ele pode usar o [fluxo de token de atualização](v2-oauth2-auth-code-flow.md#refresh-the-access-token) para obter novos tokens. O MSAL.js 2.0, a biblioteca da plataforma de identidade da Microsoft para SPAs, implementa o fluxo do código de autorização para o SPAs e, com atualizações secundárias, é uma substituição pronta para o MSAL.js 1. x.

Para a plataforma de identidade da Microsoft, SPAs e clientes nativos seguem diretrizes de protocolo semelhantes:

* Uso de um [desafio de código PKCE](https://tools.ietf.org/html/rfc7636)
    * O PKCE é *necessário* para SPAs na plataforma de identidade da Microsoft. O PKCE é *recomendado* para clientes nativos e confidenciais.
* Sem uso de um segredo do cliente

Os SPAs têm duas restrições adicionais:

* [O URI de redirecionamento deve ser marcado como o tipo `spa`](v2-oauth2-auth-code-flow.md#redirect-uri-setup-required-for-single-page-apps) para habilitar CORS em pontos de extremidade de logon.
* Os tokens de atualização emitidos pelo fluxo de código de autorização para URIs de redirecionamento `spa` têm um tempo de vida de 24 horas em vez de um tempo de vida de 90 dias.

:::image type="content" source="media/v2-oauth-auth-code-spa/active-directory-oauth-code-spa.svg" alt-text="Diagrama mostrando o fluxo do código de autorização OAuth 2 entre um aplicativo de página única e o ponto de extremidade do serviço de token de segurança." border="false":::

## <a name="performance-and-ux-implications"></a>Implicações de desempenho e UX

Alguns aplicativos que usam o fluxo implícito tentam entrar sem redirecionamento abrindo um iframe de logon usando `prompt=none`. Na maioria dos navegadores, essa solicitação responderá com tokens para o usuário conectado no momento (supondo que o consentimento já tenha sido concedido). Esse padrão significava que os aplicativos não precisavam de um redirecionamento de página completa para conectar o usuário, melhorando o desempenho e a experiência do usuário: o usuário visita a página da Web e já está conectado. Como `prompt=none` em um iframe não é mais uma opção quando cookies de terceiros são bloqueados, os aplicativos devem visitar a página de logon em um quadro de nível superior para ter um código de autorização emitido.

Há duas maneiras de realizar a entrada:

* **Redirecionamentos de página completa**
    * No primeiro carregamento do SPA, redirecione o usuário para a página de entrada se não houver sessão (ou se a sessão estiver expirada). O navegador do usuário visitará a página de logon, apresentará os cookies que contêm a sessão do usuário e redirecionará de volta para o aplicativo com o código e os tokens em um fragmento.
    * O redirecionamento faz o SPA ser carregado duas vezes. Siga as práticas recomendadas para cache de SPAs para que o aplicativo não seja baixado completamente duas vezes.
    * Tenha uma sequência de pré-carregamento no aplicativo que verifica uma sessão de logon e redireciona para a página de logon antes que o aplicativo seja totalmente descompactado e execute a carga de JavaScript.
* **Pop-ups**
    * Se a experiência do usuário (UX) de um redirecionamento completo de página não funcionar para o aplicativo, use um pop-up para lidar com a autenticação.
    * Quando o pop-up termina o redirecionamento para o aplicativo após a autenticação, o código no manipulador de redirecionamento armazenará o código e os tokens no armazenamento local para o aplicativo usar. O MSAL.js dá suporte a pop-ups para autenticação, como a maioria das bibliotecas.
    * Os navegadores estão diminuindo o suporte para pop-ups; portanto, eles podem não ser a opção mais confiável. A interação do usuário com o SPA antes de criar o pop-up pode ser necessária para atender aos requisitos do navegador.

>[!NOTE]
> A Apple [descreve um método pop-up](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/) como uma correção de compatibilidade temporária para dar à janela original o acesso a cookies de terceiros. Embora a Apple possa remover essa transferência de permissões no futuro, ela não afetará as diretrizes aqui. Aqui, o pop-up está sendo usado como uma navegação de primeira entidade para a página de logon para que uma sessão seja encontrada e um código de autenticação possa ser fornecido. Isso deve continuar funcionando no futuro.

### <a name="a-note-on-iframe-apps"></a>Uma observação sobre os aplicativos iframe

Um padrão comum em aplicativos Web é usar um iframe para inserir um aplicativo dentro de outro. O quadro de nível superior manipula a autenticação do usuário, e o aplicativo hospedado no iframe pode confiar que o usuário está conectado, buscando os tokens silenciosamente usando o fluxo implícito. A aquisição de token silenciosa não funciona mais quando cookies de terceiros são bloqueados – o aplicativo inserido no iframe deve alternar para o uso de pop-ups para acessar a sessão do usuário, pois ele não pode navegar até a página de logon.

## <a name="security-implications-of-refresh-tokens-in-the-browser"></a>Implicações de segurança de tokens de atualização no navegador

A emissão de tokens de atualização para o navegador é considerada um problema de segurança. Os ataques XSS (script entre sites) ou pacotes JS comprometidos podem roubar o token de atualização e usá-lo remotamente até que ele expire ou seja revogado. Para minimizar o risco de tokens de atualização roubado, os SPAs serão emitidos tokens válidos por apenas 24 horas. Após 24 horas, o aplicativo deve adquirir um novo código de autorização por meio de uma visita de quadro de nível superior para a página de logon.

Esse padrão de token de atualização de tempo de vida limitado foi escolhido como um equilíbrio entre a segurança e a UX degradada. Sem tokens de atualização ou cookies de terceiros, o fluxo do código de autorização (conforme recomendado pelo [rascunho de práticas recomendadas de segurança do OAuth mais recentes](https://tools.ietf.org/html/draft-ietf-oauth-security-topics-14)) torna-se oneroso quando são necessários tokens novos ou adicionais. Um redirecionamento de página completo ou um pop-up é necessário para cada token único, sempre que um token expira (a cada hora, em geral, para os tokens da plataforma Microsoft Identity).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [fluxo do código de autorização](v2-oauth2-auth-code-flow.md).

Experimente o fluxo do código de autorização com o [início rápido do MSAL.js 2.0](quickstart-v2-javascript-auth-code.md).

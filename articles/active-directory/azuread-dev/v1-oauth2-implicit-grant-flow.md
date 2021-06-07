---
title: Noções básicas sobre o fluxo de concessão implícita OAuth2 no Azure AD | Microsoft Docs
description: Saiba mais sobre a implementação do fluxo de concessão implícita OAuth2 pelo Azure Active Directory do OAuth2 e se ele é adequado para o aplicativo.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 08/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: eaa3844bfbbef8cb71dbe8691cab894c921ce00a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "80154501"
---
# <a name="understanding-the-oauth2-implicit-grant-flow-in-azure-active-directory-ad"></a>Noções básicas sobre o fluxo de concessão implícita OAuth2 no Azure AD (Active Directory)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

A concessão implícita OAuth2 é famosa por ser a concessão com a lista mais longa de questões de segurança na especificação OAuth2. Apesar disso, é a abordagem implementada pelo ADAL JS e a que recomendamos ao se escrever aplicativos SPA. O que acontece? É uma questão de compensações: e como acontece, a concessão implícita é a melhor abordagem que você pode buscar para aplicativos que consomem uma API Web via JavaScript de um navegador.

## <a name="what-is-the-oauth2-implicit-grant"></a>O que é a concessão implícita OAuth2?

A [concessão de código de autorização OAuth2](https://tools.ietf.org/html/rfc6749#section-1.3.1) por excelência é a concessão de autorização que usa dois pontos de extremidade separados. O ponto de extremidade de autorização é usado para a fase de interação do usuário, que resulta em um código de autorização. O ponto de extremidade de token é usado pelo cliente para trocar o código para um token de acesso e, com frequência, um token de atualização também. Os aplicativos Web devem apresentar suas próprias credenciais de aplicativo ao ponto de extremidade de token, para que o servidor de autorização possa autenticar o cliente.

A [concessão implícita OAuth2](https://tools.ietf.org/html/rfc6749#section-1.3.2) é uma variante de outras concessões de autorização. Ele permite que um cliente obtenha um token de acesso (e id_token ao usar [OpenId Connect](https://openid.net/specs/openid-connect-core-1_0.html)) diretamente do ponto de extremidade de autorização, sem entrar em contato com o ponto de extremidade de token ou autenticar o cliente. Essa variante foi projetada para aplicativos baseados em JavaScript em execução em um navegador da Web: na especificação original OAuth2, os tokens são retornados em um fragmento de URI. Isso torna os bits de token disponíveis para o código JavaScript no cliente, mas garante que eles não serão incluídos em redirecionamentos para o servidor. Na concessão implícita OAuth2, o ponto de extremidade de autorização emite tokens de acesso diretamente ao cliente usando um URI de redirecionamento fornecido anteriormente. Também tem a vantagem de eliminar requisitos para chamadas entre origens, que são necessárias se o aplicativo JavaScript precisa entrar em contato com o ponto de extremidade de token.

Uma característica importante da concessão implícita OAuth2 é o fato de que esses fluxos nunca retornam tokens de atualização ao cliente. A próxima seção mostra como isso não é necessário e, na verdade, seria um problema de segurança.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Cenários adequados para a concessão implícita OAuth2

A especificação OAuth2 declara que a concessão implícita foi concebida para habilitar aplicativos de agente do usuário, ou seja, aplicativos JavaScript em execução em um navegador. A característica que define esses aplicativos é que o código JavaScript é usado para acessar recursos de servidor (normalmente, uma API Web) e para atualizar a experiência do usuário do aplicativo de maneira adequada. Pense em aplicativos como Gmail ou Outlook Web Access: quando você seleciona uma mensagem da caixa de entrada, apenas o painel de visualização da mensagem muda para exibir a nova seleção, enquanto o restante da página permanece inalterado. Essa característica difere de aplicativos Web tradicionais baseados em redirecionamento, em que cada interação do usuário resulta em um postback de página inteira e em uma renderização de página inteira da nova resposta do servidor.

Os aplicativos que levam a abordagem baseada em JavaScript ao extremo são chamados de SPAs ou aplicativos de página única. A ideia é que esses aplicativos servem apenas uma página HTML inicial e o JavaScript associado, com todas as interações posteriores sendo conduzidas por chamadas à API Web realizadas por JavaScript. No entanto, as abordagens híbridas, em que o aplicativo é principalmente orientado por postback, mas executa chamadas JS ocasionais, não são incomuns. A discussão sobre o uso de fluxo implícitos também é relevante para eles.

Aplicativos baseados em redirecionamento normalmente protegem suas solicitações por meio de cookies. No entanto, essa abordagem não funciona bem para aplicativos JavaScript. Cookies só funcionam em relação ao domínio em que foram gerados, enquanto chamadas JavaScript podem ser direcionadas para outros domínios. De fato, frequentemente esse será o caso: pense em aplicativos que invocam a API do Microsoft Graph, a API do Office e a API do Azure. Todos eles residem fora do domínio de onde o aplicativo é fornecido. Uma tendência crescente para aplicativos JavaScript é não ter um back-end, recorrendo totalmente a APIs Web de terceiros para implementar sua função de negócios.

Atualmente, o método preferencial para proteger chamadas para uma API Web é usar a abordagem de token de portador de OAuth2, em que cada chamada é acompanhada por um token de acesso OAuth2. A API Web examina o token de acesso de entrada e, se encontrar nele os escopos necessários, concederá acesso à operação solicitada. O fluxo implícito fornece um mecanismo conveniente para que aplicativos JavaScript obtenham tokens de acesso para uma API Web, com diversas vantagens em relação aos cookies:

* Os tokens podem ser obtidos de forma confiável sem a necessidade de chamadas entre origens; o registro obrigatório do URI de redirecionamento ao qual os tokens são retornados garante que os tokens não sejam substituídos
* Aplicativos JavaScript podem obter tantos tokens de acesso quantos forem necessários, para todas as APIs Web que direcionam, sem restrição quanto aos domínios
* Recursos de HTML5, como armazenamento local ou por sessão, concedem controle total sobre o cache de tokens e o gerenciamento de tempo de vida, enquanto o gerenciamento de cookies é opaco para o aplicativo
* Tokens de acesso não são suscetíveis a ataques CSRF (solicitação entre sites forjada)

O fluxo de concessão implícita não emite tokens de atualização, principalmente por motivos de segurança. Um token de atualização não tem um escopo restrito como tokens de acesso, concedendo muito mais energia, provocando muito mais danos caso ele seja vazado. No fluxo implícito, os tokens são entregues na URL, portanto, o risco de interceptação é maior do que na concessão de código de autorização.

No entanto, um aplicativo JavaScript tem outro mecanismo à sua disposição para renovar tokens de acesso sem solicitar repetidamente as credenciais ao usuário. O aplicativo pode usar um iframe oculto para executar novas solicitações de token em relação ao ponto de extremidade de autorização do Azure AD: enquanto o navegador ainda tiver uma sessão ativa (ou seja, tiver um cookie de sessão) em relação ao domínio do Azure AD, a solicitação de autenticação poderá ocorrer com êxito sem necessidade de interação do usuário.

Esse modelo concede ao aplicativo JavaScript a capacidade de renovar independentemente os tokens de acesso e até mesmo adquirir novos tokens para uma nova API (desde que o usuário os tenha consentido anteriormente). Isso evita a sobrecarga adicional de aquisição, manutenção e proteção de um artefato de alto valor, como um token de atualização. O artefato que possibilita a renovação silenciosa, o cookie de sessão do Azure AD, é gerenciado fora do aplicativo. Outra vantagem dessa abordagem é que um usuário pode sair do Azure AD, usando qualquer um dos aplicativos conectados ao Azure AD, em execução em qualquer uma das guias do navegador. Isso resulta na exclusão do cookie de sessão do Azure AD, e o aplicativo JavaScript automaticamente perde a capacidade de renovar tokens para o usuário desconectado.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>A concessão implícita é adequada para meu aplicativo?

A concessão implícita apresenta mais riscos do que outras concessões e as áreas que você precisa prestar atenção estão bem documentadas (por exemplo, [Token de Acesso para Representar o Proprietário do Recurso em Fluxo Implícito][OAuth2-Spec-Implicit-Misuse]e [Modelo de Ameaça do OAuth 2.0 e Considerações de Segurança][OAuth2-Threat-Model-And-Security-Implications]). No entanto, o perfil de risco mais alto é amplamente devido ao fato de que seu objetivo é habilitar aplicativos que executam código ativo, fornecido por um recurso remoto a um navegador. Se você estiver planejando uma arquitetura SPA, não tem um componente de back-end ou pretende invocar uma API Web por meio de JavaScript, é recomendável usar o fluxo implícito para aquisição de token.

Se seu aplicativo for um cliente nativo, o fluxo implícito não será uma ótima opção. A ausência do cookie de sessão do Azure AD no contexto de um cliente nativo priva o aplicativo dos meios de manter uma sessão com vida útil longa. Isso significa que o aplicativo se dirigirá repetidamente ao usuário ao obter tokens de acesso para novos recursos.

Se você está desenvolvendo um aplicativo Web que inclui um back-end e que consome uma API de seu código de back-end, o fluxo implícito também não é uma boa opção. Outras concessões oferecem capacidade muito maior. Por exemplo, a concessão de credenciais de cliente OAuth2 fornece a capacidade de obter tokens que refletem as permissões atribuídas ao próprio aplicativo, em vez de delegações de usuário. Isso significa que o cliente tem a capacidade de manter o acesso programático a recursos mesmo quando um usuário não está ativamente envolvido em uma sessão e assim por diante. Não apenas isso, mas essas concessões dão garantias de segurança mais alta. Por exemplo, os tokens de acesso nunca passam pelo navegador do usuário, eles não têm o risco de serem salvos no histórico do navegador e assim por diante. O aplicativo cliente também pode executar a autenticação forte ao solicitar um token.

## <a name="next-steps"></a>Próximas etapas

* Veja [Como integrar um aplicativo ao Azure AD][ACOM-How-To-Integrate] para saber mais sobre o processo de integração de aplicativos.

<!--Image references-->

<!--Reference style links in use-->
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: ../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819

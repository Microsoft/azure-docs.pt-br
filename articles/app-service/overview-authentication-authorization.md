---
title: Autenticação e autorização - Serviço de Aplicativo do Azure | Microsoft Docs
description: Referência conceitual e visão geral do recurso Autenticação/Autorização para o Serviço de Aplicativo do Azure
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/24/2018
ms.author: mahender,cephalin
ms.custom: seodec18
ms.openlocfilehash: d914e3ad3043b2671e154d1616c6800f34415c11
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835561"
---
# <a name="authentication-and-authorization-in-azure-app-service"></a>Autenticação e autorização no Serviço de Aplicativo do Azure

> [!NOTE]
> Neste momento, o AAD V2 (incluindo MSAL) não há suporte para serviços de aplicativo do Azure e o Azure Functions. Verifique se há atualizações.
>

O Serviço de Aplicativo do Azure dá suporte interno à autenticação e autorização para que você possa fazer login de usuários e acessar dados, gravando o mínimo ou nenhum código no aplicativo Web, na API RESTful e no back-end para dispositivos móveis, e também [Azure Functions](../azure-functions/functions-overview.md). Este artigo descreve como o Serviço de Aplicativo ajuda a simplificar a autenticação e autorização do aplicativo. 

A autenticação e autorização seguras exigem um profundo entendimento de segurança, incluindo federação, criptografia, [gerenciamento JWT (Token Web JSON)](https://wikipedia.org/wiki/JSON_Web_Token), [tipos de concessão](https://oauth.net/2/grant-types/), e assim por diante. O Serviço de Aplicativo disponibiliza esses utilitários para que você possa investir mais tempo e energia no fornecimento de valor comercial ao seu cliente.

> [!NOTE]
> Você não é necessário usar o Serviço de Aplicativo para autenticação e autorização. Muitas estruturas da Web são agrupadas com recursos de segurança e você poderá utilizá-las, se desejar. Caso precise de mais flexibilidade do que o Serviço de Aplicativo fornece, você também poderá gravar seus próprios utilitários.  
>

Para informações específicas sobre aplicativos móveis nativos, consulte [Autenticação e autorização de usuários para aplicativos móveis com Serviço de Aplicativo do Azure ](../app-service-mobile/app-service-mobile-auth.md).

## <a name="how-it-works"></a>Como ele funciona

O módulo de autenticação e autorização executa na mesma caixa restrita que o código do aplicativo. Quando habilitado, toda solicitação HTTP de entrada passa por ele antes de ser manipulada pelo código do aplicativo.

![](media/app-service-authentication-overview/architecture.png)

Esse módulo trata diversos pontos do aplicativo:

- Autentica usuários com o provedor especificado
- Valida, armazena e atualiza tokens
- Gerencia a sessão autenticada
- Injeta informações de identidade em cabeçalhos da solicitação

O módulo executa separadamente do código do aplicativo e é configurado usando as configurações do aplicativo. Não há necessidade de SDKs, idiomas específicos ou alterações no código do aplicativo. 

### <a name="user-claims"></a>Declarações de usuário

Para todas as estruturas de linguagem, o Serviço de Aplicativo disponibiliza as declarações de usuário para o código, injetando-as nos cabeçalhos da solicitação. Para aplicativos ASP.NET 4.6, o Serviço de Aplicativo preenche [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current) com as declarações do usuário autenticado, de modo que seja possível seguir o padrão de código .NET Standard, incluindo o atributo `[Authorize]`. Da mesma forma, para aplicativos PHP, o Serviço de Aplicativo preenche a variável `_SERVER['REMOTE_USER']`.

Para [Azure Functions](../azure-functions/functions-overview.md), `ClaimsPrincipal.Current` não é hidratado para código .NET, mas ainda é possível localizar as declarações de usuário nos cabeçalhos da solicitação.

Para mais informações, consulte [Acessar declarações de usuário](app-service-authentication-how-to.md#access-user-claims).

### <a name="token-store"></a>Token Store

O Serviço de Aplicativo fornece um armazenamento de token integrado, que é um repositório de tokens associados aos usuários dos aplicativos Web, APIs ou aplicativos móveis nativos. Ao habilitar a autenticação com qualquer provedor, esse armazenamento de token ficará imediatamente disponível para o aplicativo. Se o código do aplicativo precisar acessar dados desses provedores em nome do usuário, como: 

- postar na linha do tempo do Facebook do usuário autenticado
- ler os dados corporativos do usuário da API do Graph do Azure Active Directory ou até mesmo do Microsoft Graph

Normalmente, é necessário gravar código para coletar, armazenar e atualizar esses tokens no aplicativo. Com o Token Store, você apenas [recupera os tokens](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) quando precisar deles e [informa ao Serviço de Aplicativo para atualizá-los](app-service-authentication-how-to.md#refresh-identity-provider-tokens) quando tornarem-se inválidos. 

Os tokens de ID, tokens de acesso e tokens de atualização são armazenados em cache para a sessão autenticada e são acessíveis apenas pelo usuário associado.  

Se você não precisar trabalhar com tokens no aplicativo, poderá desabilitar o Token Store.

### <a name="logging-and-tracing"></a>Log e rastreamento

Se você [habilitar o log de aplicativo](troubleshoot-diagnostic-logs.md), verá os rastreamentos de autenticação e autorização diretamente nos arquivos de log. Se você visualizar um erro de autenticação que não esperava, será possível localizar convenientemente todos os detalhes examinando os logs do aplicativo existente. Se você habilitar [rastreamento de solicitação com falha](troubleshoot-diagnostic-logs.md), será possível visualizar exatamente qual função o módulo de autenticação e autorização pode ter desempenhado em uma solicitação com falha. Nos logs de rastreamento, procure referências para um módulo nomeado `EasyAuthModule_32/64`. 

## <a name="identity-providers"></a>Provedores de identidade

O Serviço de Aplicativo usa [identidade federada](https://en.wikipedia.org/wiki/Federated_identity), na qual um provedor de identidade de terceiros gerencia as identidades do usuário e o fluxo de autenticação para você. Cinco provedores de identidade estão disponíveis por padrão: 

| Provedor | Ponto de extremidade de logon |
| - | - |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` |
| [Conta da Microsoft](../active-directory/develop/v2-overview.md) | `/.auth/login/microsoftaccount` |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` |
| [Google](https://developers.google.com/+/web/api/rest/oauth) | `/.auth/login/google` |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` |

Ao habilitar a autenticação e autorização com um desses provedores, seu ponto de extremidade de logon estará disponível para autenticação de usuário e validação de tokens de autenticação do provedor. Você pode fornecer aos usuários qualquer número dessas opções de entrada com facilidade. Você também pode integrar outro provedor de identidade ou [sua própria solução de identidade personalizada][custom-auth].

## <a name="authentication-flow"></a>Fluxo de autenticação

O fluxo de autenticação é o mesmo para todos os provedores, mas difere dependendo se você deseja entrar com o SDK do provedor:

- Sem SDK do provedor: O aplicativo delega o logon federado ao Serviço de Aplicativo. Esse é tipicamente o caso de aplicativos do navegador que podem apresentar a página de logon do provedor ao usuário. O código do servidor gerencia o processo de logon, portanto, ele também é chamado de _fluxo direcionado ao servidor_ ou _fluxo do servidor_. Este caso aplica-se a aplicativos de navegador. É aplicável também aos aplicativos nativos que conectam usuários usando o SDK de cliente dos Aplicativos Móveis porque o SDK abre um modo de exibição da Web para conectar usuários com a autenticação do Serviço de Aplicativo. 
- Com SDK do provedor: O aplicativo assina os usuários no provedor manualmente e, em seguida, envia o token de autenticação para o Serviço de Aplicativo para validação. Esse é tipicamente o caso de aplicativos sem navegador, que não podem apresentar a página de entrada do provedor ao usuário. O código do aplicativo gerencia o processo de entrada, portanto, ele também é chamado de _fluxo direcionado ao cliente_ ou _fluxo do cliente_. Esse caso aplica-se a APIs REST, [Azure Functions](../azure-functions/functions-overview.md) e clientes do navegador JavaScript, bem como aplicativos de navegador que precisam de mais flexibilidade no processo de entrada. Aplica-se também a aplicativos móveis nativos que conectam usuários usando o SDK do provedor.

> [!NOTE]
> Chamadas de um aplicativo de navegador confiável no Serviço de Aplicativo chamam outra API REST no Serviço de Aplicativo ou o [Azure Functions](../azure-functions/functions-overview.md) pode ser autenticado usando o fluxo direcionado ao servidor. Para obter mais informações, consulte [Personalizar autenticação e autorização no serviço de aplicativos](app-service-authentication-how-to.md).
>

A tabela abaixo mostra as etapas do fluxo de autenticação.

| Etapa | Sem SDK do provedor | Com SDK do provedor |
| - | - | - |
| 1. Conectar usuário | Redireciona o cliente para `/.auth/login/<provider>`. | O código do cliente coneta o usuário diretamente no SDK do provedor e recebe um token de autenticação. Para obter informações, consulte a documentação do provedor. |
| 2. Pós-autenticação | Provedor redireciona o cliente para `/.auth/login/<provider>/callback`. | O código do cliente [envia o token do provedor](app-service-authentication-how-to.md#validate-tokens-from-providers) para `/.auth/login/<provider>` para a validação. |
| 3. Estabelecer sessão autenticada | O Serviço de Aplicativo adiciona um cookie autenticado à resposta. | O Serviço de Aplicativo retorna o próprio token de autenticação para o código do cliente. |
| 4. Atender conteúdo autenticado | O cliente inclui o cookie de autenticação em solicitações subsequentes (manipuladas automaticamente pelo navegador). | O código do cliente apresenta o token de autenticação no cabeçalho `X-ZUMO-AUTH` (manipulado automaticamente pelos SDKs de cliente dos Aplicativos Móveis). |

Para navegadores do cliente, o Serviço de Aplicativo pode direcionar automaticamente todos os usuários não autenticados para `/.auth/login/<provider>`. Você também pode apresentar aos usuários um ou mais links `/.auth/login/<provider>` para entrar no aplicativo usando o provedor escolhido.

<a name="authorization"></a>

## <a name="authorization-behavior"></a>Comportamento de autorização

No [Portal do Azure](https://portal.azure.com), é possível configurar a autorização do Serviço de Aplicativo com vários comportamentos.

![](media/app-service-authentication-overview/authorization-flow.png)

Os cabeçalhos a seguir descrevem as opções.

### <a name="allow-all-requests-default"></a>Permitir todas as solicitações (padrão)

Autenticação e autorização não são gerenciadas pelo Serviço de Aplicativo (desativado). 

Escolha essa opção se você não precisar de autenticação e autorização ou se quiser gravar seu próprio código de autenticação e autorização.

### <a name="allow-only-authenticated-requests"></a>Permitir apenas solicitações autenticadas

A opção é **Logon com \<provedor>**. O Serviço de Aplicativo redireciona todas as solicitações anônimas para `/.auth/login/<provider>` do provedor escolhido. Se a solicitação anônima originar-se de um aplicativo móvel nativo, a resposta retornada será `HTTP 401 Unauthorized`.

Com essa opção, você não precisa gravar nenhum código de autenticação no aplicativo. Uma autorização mais precisa, como autorização específica de função, pode ser tratada inspecionando as declarações do usuário (consulte [Acessar declarações do usuário](app-service-authentication-how-to.md#access-user-claims)).

### <a name="allow-all-requests-but-validate-authenticated-requests"></a>Permitir todas as solicitações, mas validar solicitações autenticadas

A opção é **Permitir solicitações anônimas**. Essa opção ativa a autenticação e autorização no Serviço de Aplicativo, mas adia decisões de autorização para o código do aplicativo. Para solicitações autenticadas, o Serviço de Aplicativo também passa informações de autenticação nos cabeçalhos HTTP. 

Essa opção oferece mais flexibilidade no processamento de solicitações anônimas. Por exemplo, permite que você [apresente vários provedores de entrada](app-service-authentication-how-to.md#use-multiple-sign-in-providers) aos usuários. No entanto, é necessário gravar o código. 

## <a name="more-resources"></a>Mais recursos

[Tutorial: Autenticar e autorizar usuários de ponta a ponta no Serviço de Aplicativo do Azure (Windows)](app-service-web-tutorial-auth-aad.md)  
[Tutorial: Autenticar e autorizar usuários de ponta a ponta no Serviço de Aplicativo do Azure para Linux](containers/tutorial-auth-aad.md)  
[Personalizar autenticação e autorização no Serviço de Aplicativo](app-service-authentication-how-to.md)

Guias de instruções específicas do provedor:

* [Como configurar seu aplicativo para usar o logon do Azure Active Directory][AAD]
* [Como configurar seu aplicativo para usar o logon do Facebook][Facebook]
* [Como configurar seu aplicativo para usar o logon do Google][Google]
* [Como configurar seu aplicativo para usar o logon da Conta da Microsoft][MSA]
* [Como configurar seu aplicativo para usar o logon do Twitter][Twitter]
* [Como: Usar autenticação personalizada para o aplicativo][custom-auth]

[AAD]: configure-authentication-provider-aad.md
[Facebook]: configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: configure-authentication-provider-microsoft.md
[Twitter]: configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal

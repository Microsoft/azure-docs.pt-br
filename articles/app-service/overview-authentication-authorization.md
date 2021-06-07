---
title: Autenticação e autorização
description: Saiba mais sobre o suporte interno de autenticação e autorização em Azure App serviço e Azure Functions e como ele pode ajudar a proteger seu aplicativo contra acesso não autorizado.
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.topic: article
ms.date: 07/08/2020
ms.reviewer: mahender
ms.custom: seodec18, fasttrack-edit, has-adal-ref
ms.openlocfilehash: 35513abdfb61d889abdbd4af7125b1fbb556d7b8
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612748"
---
# <a name="authentication-and-authorization-in-azure-app-service-and-azure-functions"></a>Autenticação e autorização no serviço Azure App e Azure Functions

O serviço de Azure App fornece recursos internos de autenticação e autorização (às vezes chamados de "autenticação fácil"), para que você possa conectar usuários e acessar dados escrevendo um mínimo ou nenhum código em seu aplicativo Web, API RESTful e back-end móvel e também [Azure Functions](../azure-functions/functions-overview.md). Este artigo descreve como o Serviço de Aplicativo ajuda a simplificar a autenticação e autorização do aplicativo.

## <a name="why-use-the-built-in-authentication"></a>Por que usar a autenticação interna?

Não é necessário usar esse recurso para autenticação e autorização. Você pode usar os recursos de segurança agrupados em sua estrutura da Web de sua escolha ou pode escrever seus próprios utilitários. No entanto, você precisará garantir que sua solução permaneça atualizada com as mais recentes atualizações de segurança, protocolo e navegador.

A implementação de uma solução segura para autenticação (usuários de entrada) e autorização (fornecendo acesso a dados seguros) pode levar a um esforço significativo. Você deve se certificar de seguir as práticas recomendadas e os padrões do setor e manter sua implementação atualizada. O recurso de autenticação interna para serviço de aplicativo e Azure Functions pode poupar tempo e esforço fornecendo autenticação integrada com provedores de identidade federada, permitindo que você se concentre no restante do seu aplicativo.

- Azure App serviço permite que você integre uma variedade de recursos de autenticação em seu aplicativo Web ou API sem implementá-los por conta própria.
- Ele é criado diretamente na plataforma e não requer qualquer linguagem, SDK, conhecimento de segurança ou até mesmo qualquer código a ser utilizado.
- Você pode integrar com vários provedores de logon. Por exemplo, Azure AD, Facebook, Google, Twitter.

## <a name="identity-providers"></a>Provedores de identidade

O Serviço de Aplicativo usa [identidade federada](https://en.wikipedia.org/wiki/Federated_identity), na qual um provedor de identidade de terceiros gerencia as identidades do usuário e o fluxo de autenticação para você. Os provedores de identidade a seguir estão disponíveis por padrão:

| Provedor | Ponto de extremidade de logon | Diretrizes de How-To |
| - | - | - |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` | [Logon do Azure AD no serviço de aplicativo](configure-authentication-provider-aad.md) |
| [Conta da Microsoft](../active-directory/develop/v2-overview.md) | `/.auth/login/microsoftaccount` | [Logon da conta da Microsoft do serviço de aplicativo](configure-authentication-provider-microsoft.md) |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` | [Logon do Facebook do serviço de aplicativo](configure-authentication-provider-facebook.md) |
| [Google](https://developers.google.com/identity/choose-auth) | `/.auth/login/google` | [Logon do Google do serviço de aplicativo](configure-authentication-provider-google.md) |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` | [Logon do Twitter do serviço de aplicativo](configure-authentication-provider-twitter.md) |
| Qualquer provedor do [OpenID Connect](https://openid.net/connect/) (versão prévia) | `/.auth/login/<providerName>` | [Logon do OpenID Connect do serviço de aplicativo](configure-authentication-provider-openid-connect.md) |

Ao habilitar a autenticação e autorização com um desses provedores, seu ponto de extremidade de logon estará disponível para autenticação de usuário e validação de tokens de autenticação do provedor. Você pode fornecer aos seus usuários qualquer número dessas opções de entrada.

## <a name="considerations-for-using-built-in-authentication"></a>Considerações para usar a autenticação interna

Habilitar esse recurso fará com que todas as solicitações para seu aplicativo sejam redirecionadas automaticamente para HTTPS, independentemente da definição de configuração do serviço de aplicativo para impor HTTPS. Você pode desabilitar isso com a  `requireHttps` configuração na configuração v2. No entanto, recomendamos ficar com HTTPS e você deve garantir que nenhum token de segurança seja transmitido por conexões HTTP não seguras.

O serviço de aplicativo pode ser usado para autenticação com ou sem restringir o acesso ao conteúdo e às APIs do site. Para restringir o acesso do aplicativo somente a usuários autenticados, defina **a ação a ser tomada quando a solicitação não for autenticada** para fazer logon com um dos provedores de identidade configurados. Para autenticar, mas não restringir o acesso, defina **a ação a ser tomada quando a solicitação não for autenticada** para "permitir solicitações anônimas (nenhuma ação)".

> [!NOTE]
> Você deve dar a cada aplicativo registro sua própria permissão e consentimento. Evite o compartilhamento de permissão entre ambientes usando registros de aplicativo separados para slots de implantação separados. Essa prática pode ajudar a evitar problemas que afetam o aplicativo de produção durante o teste do novo código.

## <a name="how-it-works"></a>Como ele funciona

[Arquitetura de recursos no Windows (implantação sem contêiner)](#feature-architecture-on-windows-non-container-deployment))

[Arquitetura de recursos no Linux e contêineres](#feature-architecture-on-linux-and-containers)

[Fluxo de autenticação](#authentication-flow)

[Comportamento de autorização](#authorization-behavior)

[Declarações de usuário e aplicativo](#user-and-application-claims)

[Token Store](#token-store)

[Registro em log e rastreamento](#logging-and-tracing)

#### <a name="feature-architecture-on-windows-non-container-deployment"></a>Arquitetura de recursos no Windows (implantação sem contêiner)

O módulo de autenticação e autorização executa na mesma caixa restrita que o código do aplicativo. Quando habilitado, toda solicitação HTTP de entrada passa por ele antes de ser manipulada pelo código do aplicativo.

:::image type="content" source="media/app-service-authentication-overview/architecture.png" alt-text="Um diagrama de arquitetura que mostra solicitações sendo interceptadas por um processo na área restrita do site que interage com provedores de identidade antes de permitir o tráfego para o site implantado" lightbox="media/app-service-authentication-overview/architecture.png":::

Esse módulo trata diversos pontos do aplicativo:

- Autentica usuários com o provedor especificado
- Valida, armazena e atualiza tokens
- Gerencia a sessão autenticada
- Injeta informações de identidade em cabeçalhos da solicitação

O módulo executa separadamente do código do aplicativo e é configurado usando as configurações do aplicativo. Não há necessidade de SDKs, idiomas específicos ou alterações no código do aplicativo. 

#### <a name="feature-architecture-on-linux-and-containers"></a>Arquitetura de recursos no Linux e contêineres

O módulo de autenticação e autorização é executado em um contêiner separado, isolado do código do aplicativo. Usando o que é conhecido como o [padrão embaixador](/azure/architecture/patterns/ambassador), ele interage com o tráfego de entrada para executar uma funcionalidade semelhante à do Windows. Como ele não é executado em processo, nenhuma integração direta com estruturas de linguagem específicas é possível; no entanto, as informações relevantes de que seu aplicativo precisa são passadas usando cabeçalhos de solicitação, conforme explicado abaixo.

#### <a name="authentication-flow"></a>Fluxo de autenticação

O fluxo de autenticação é o mesmo para todos os provedores, mas difere dependendo se você deseja entrar com o SDK do provedor:

- Sem SDK do provedor: o aplicativo delega o logon federado ao Serviço de Aplicativo. Esse é tipicamente o caso de aplicativos do navegador que podem apresentar a página de logon do provedor ao usuário. O código do servidor gerencia o processo de logon, portanto, ele também é chamado de _fluxo direcionado ao servidor_ ou _fluxo do servidor_. Este caso aplica-se a aplicativos de navegador. É aplicável também aos aplicativos nativos que conectam usuários usando o SDK de cliente dos Aplicativos Móveis porque o SDK abre um modo de exibição da Web para conectar usuários com a autenticação do Serviço de Aplicativo.
- Com o provedor SDK: o aplicativo assina os usuários no provedor manualmente e, em seguida, envia o token de autenticação para o Serviço de Aplicativo para validação. Esse é tipicamente o caso de aplicativos sem navegador, que não podem apresentar a página de entrada do provedor ao usuário. O código do aplicativo gerencia o processo de entrada, portanto, ele também é chamado de _fluxo direcionado ao cliente_ ou _fluxo do cliente_. Esse caso aplica-se a APIs REST, [Azure Functions](../azure-functions/functions-overview.md) e clientes do navegador JavaScript, bem como aplicativos de navegador que precisam de mais flexibilidade no processo de entrada. Aplica-se também a aplicativos móveis nativos que conectam usuários usando o SDK do provedor.

Chamadas de um aplicativo de navegador confiável no serviço de aplicativo para outra API REST no serviço de aplicativo ou [Azure Functions](../azure-functions/functions-overview.md) podem ser autenticadas usando o fluxo direcionado ao servidor. Para obter mais informações, consulte [Personalizar autenticação e autorização no serviço de aplicativos](app-service-authentication-how-to.md).

A tabela abaixo mostra as etapas do fluxo de autenticação.

| Etapa | Sem SDK do provedor | Com SDK do provedor |
| - | - | - |
| 1. conectar o usuário | Redireciona o cliente para `/.auth/login/<provider>`. | O código do cliente coneta o usuário diretamente no SDK do provedor e recebe um token de autenticação. Para obter informações, consulte a documentação do provedor. |
| 2. pós-autenticação | Provedor redireciona o cliente para `/.auth/login/<provider>/callback`. | O código [do cliente posta o token do provedor](app-service-authentication-how-to.md#validate-tokens-from-providers) para `/.auth/login/<provider>` para validação. |
| 3. estabelecer sessão autenticada | O Serviço de Aplicativo adiciona um cookie autenticado à resposta. | O Serviço de Aplicativo retorna o próprio token de autenticação para o código do cliente. |
| 4. fornecer conteúdo autenticado | O cliente inclui o cookie de autenticação em solicitações subsequentes (manipuladas automaticamente pelo navegador). | O código do cliente apresenta o token de autenticação no cabeçalho `X-ZUMO-AUTH` (manipulado automaticamente pelos SDKs de cliente dos Aplicativos Móveis). |

Para navegadores do cliente, o Serviço de Aplicativo pode direcionar automaticamente todos os usuários não autenticados para `/.auth/login/<provider>`. Você também pode apresentar aos usuários um ou mais links `/.auth/login/<provider>` para entrar no aplicativo usando o provedor escolhido.

<a name="authorization"></a>

#### <a name="authorization-behavior"></a>Comportamento de autorização

Na [portal do Azure](https://portal.azure.com), você pode configurar a autorização do serviço de aplicativo com vários comportamentos quando a solicitação de entrada não for autenticada.

![Uma captura de tela mostrando a lista suspensa "ação a ser tomada quando a solicitação não está autenticada"](media/app-service-authentication-overview/authorization-flow.png)

Os cabeçalhos a seguir descrevem as opções.

**Permitir solicitações anônimas (nenhuma ação)**

Essa opção adia a autorização de tráfego não autenticado para o código do aplicativo. Para solicitações autenticadas, o Serviço de Aplicativo também passa informações de autenticação nos cabeçalhos HTTP.

Essa opção oferece mais flexibilidade no processamento de solicitações anônimas. Por exemplo, permite que você [apresente vários provedores de entrada](app-service-authentication-how-to.md#use-multiple-sign-in-providers) aos usuários. No entanto, é necessário gravar o código.

**Permitir apenas solicitações autenticadas**

A opção é **fazer logon com \<provider>** o. O Serviço de Aplicativo redireciona todas as solicitações anônimas para `/.auth/login/<provider>` do provedor escolhido. Se a solicitação anônima originar-se de um aplicativo móvel nativo, a resposta retornada será `HTTP 401 Unauthorized`.

Com essa opção, você não precisa gravar nenhum código de autenticação no aplicativo. Uma autorização mais precisa, como autorização específica de função, pode ser tratada inspecionando as declarações do usuário (consulte [Acessar declarações do usuário](app-service-authentication-how-to.md#access-user-claims)).

> [!CAUTION]
> Restringir o acesso dessa maneira se aplica a todas as chamadas para seu aplicativo, o que pode não ser desejável para aplicativos que querem um home page publicamente disponível, como em muitos aplicativos de página única.

> [!NOTE]
> Por padrão, qualquer usuário em seu locatário do Azure AD pode solicitar um token para seu aplicativo do Azure AD. Você pode [Configurar o aplicativo no Azure ad](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) se quiser restringir o acesso ao seu aplicativo a um conjunto definido de usuários.


#### <a name="user-and-application-claims"></a>Declarações de usuário e aplicativo

Para todas as estruturas de linguagem, o serviço de aplicativo faz as declarações no token de entrada (seja por meio de um usuário final autenticado ou um aplicativo cliente) disponíveis para seu código injetando-as nos cabeçalhos de solicitação. Para aplicativos ASP.NET 4.6, o Serviço de Aplicativo preenche [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current) com as declarações do usuário autenticado, de modo que seja possível seguir o padrão de código .NET Standard, incluindo o atributo `[Authorize]`. Da mesma forma, para aplicativos PHP, o Serviço de Aplicativo preenche a variável `_SERVER['REMOTE_USER']`. Para aplicativos Java, as declarações podem [ser acessadas do servlet Tomcat](configure-language-java.md#authenticate-users-easy-auth).

Por [Azure Functions](../azure-functions/functions-overview.md), `ClaimsPrincipal.Current` o não é populado para o código .net, mas você ainda pode encontrar as declarações do usuário nos cabeçalhos da solicitação ou obter o `ClaimsPrincipal` objeto do contexto da solicitação ou mesmo por meio de um parâmetro de associação. Consulte [trabalhando com identidades de cliente](../azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities) para obter mais informações.

Para mais informações, consulte [Acessar declarações de usuário](app-service-authentication-how-to.md#access-user-claims).

No momento, ASP.NET Core atualmente não dá suporte ao preenchimento do usuário atual com o recurso de autenticação/autorização. No entanto, há [componentes de middleware](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth) de software livre de terceiros que existem para ajudar a preencher essa lacuna.

#### <a name="token-store"></a>Token Store

O Serviço de Aplicativo fornece um armazenamento de token integrado, que é um repositório de tokens associados aos usuários dos aplicativos Web, APIs ou aplicativos móveis nativos. Ao habilitar a autenticação com qualquer provedor, esse armazenamento de token ficará imediatamente disponível para o aplicativo. Se o código do aplicativo precisar acessar dados desses provedores em nome do usuário, como:

- postar na linha do tempo do Facebook do usuário autenticado
- ler os dados corporativos do usuário usando a API de Microsoft Graph

Normalmente, é necessário gravar código para coletar, armazenar e atualizar esses tokens no aplicativo. Com o Token Store, você apenas [recupera os tokens](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) quando precisar deles e [informa ao Serviço de Aplicativo para atualizá-los](app-service-authentication-how-to.md#refresh-identity-provider-tokens) quando tornarem-se inválidos. 

Os tokens de ID, tokens de acesso e tokens de atualização são armazenados em cache para a sessão autenticada e são acessíveis somente pelo usuário associado.  

Se você não precisar trabalhar com tokens em seu aplicativo, poderá desabilitar o repositório de token na página **autenticação/autorização** do seu aplicativo.

#### <a name="logging-and-tracing"></a>Registro em log e rastreamento

Se você [habilitar o log de aplicativo](troubleshoot-diagnostic-logs.md), verá os rastreamentos de autenticação e autorização diretamente nos arquivos de log. Se você vir um erro de autenticação que não esperava, poderá encontrar convenientemente todos os detalhes examinando os logs de aplicativo existentes. Se você habilitar [rastreamento de solicitação com falha](troubleshoot-diagnostic-logs.md), será possível visualizar exatamente qual função o módulo de autenticação e autorização pode ter desempenhado em uma solicitação com falha. Nos logs de rastreamento, procure referências para um módulo nomeado `EasyAuthModule_32/64`.

## <a name="more-resources"></a>Mais recursos

- [Como: configurar seu serviço de aplicativo ou Azure Functions aplicativo para usar o logon do Azure AD](configure-authentication-provider-aad.md)
- [Uso avançado de autenticação e autorização no Serviço de Aplicativo do Azure](app-service-authentication-how-to.md)

Exemplos:
- [Tutorial: Adicionar a autenticação ao seu aplicativo Web em execução no Serviço de Aplicativo do Azure](scenario-secure-app-authentication-app-service.md)
- [Tutorial: autenticar e autorizar usuários de ponta a ponta no serviço de Azure App (Windows ou Linux)](tutorial-auth-aad.md)
- [Integração do .NET Core do Azure AppService EasyAuth (terceiros)](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth)
- [Obtendo Azure App a autenticação do serviço funcionando com o .NET Core (terceiros)](https://github.com/kirkone/KK.AspNetCore.EasyAuthAuthentication)

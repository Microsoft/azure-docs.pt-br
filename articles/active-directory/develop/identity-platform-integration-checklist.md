---
title: Práticas recomendadas para a plataforma Microsoft Identity | Azure
description: Saiba mais sobre as práticas recomendadas, recomendações e supervisões comuns ao integrar com a plataforma de identidade da Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: a6a7bf24571660d8e728c1acba29af2504539a18
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99219953"
---
# <a name="microsoft-identity-platform-best-practices-and-recommendations"></a>Melhores práticas e recomendações da plataforma de identidade da Microsoft

Este artigo destaca as práticas recomendadas, recomendações e supervisões comuns ao integrar com a plataforma de identidade da Microsoft.  Esta lista de verificação orientará você para uma integração segura e de alta qualidade. Revise essa lista regularmente para garantir que você mantenha a qualidade e a segurança da integração do seu aplicativo com a plataforma de identidade. A lista de verificação não se destina a examinar todo o aplicativo. O conteúdo da lista de verificação está sujeito a alterações à medida que fizermos melhorias na plataforma.

Se você estiver apenas começando, confira a [documentação da plataforma de identidade da Microsoft](index.yml) para saber mais sobre noções básicas de autenticação, cenários de aplicativos na plataforma de identidade da Microsoft e muito mais.

Use a seguinte lista de verificação para garantir que seu aplicativo seja efetivamente integrado à [plataforma de identidade da Microsoft](./index.yml).

> [!TIP]
> O *Assistente de integração* no portal do Azure pode ajudá-lo a aplicar muitas dessas práticas recomendadas e recomendações. Selecione qualquer um dos seus [registros de aplicativo](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) no portal do Azure e, em seguida, selecione o item de menu assistente de **integração** para começar a usar o assistente.

## <a name="basics"></a>Noções básicas

![caixa ](./media/active-directory-integration-checklist/checkbox-two.svg) de seleção ler e entender as [políticas da plataforma Microsoft](/legal/microsoft-identity-platform/terms-of-use). Verifique se seu aplicativo segue os termos descritos como foram projetados para proteger os usuários e a plataforma.

## <a name="ownership"></a>Propriedade

![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) Verifique se as informações associadas à conta que você usou para registrar e gerenciar aplicativos estão atualizadas.

## <a name="branding"></a>Identidade visual

![a caixa de seleção ](./media/active-directory-integration-checklist/checkbox-two.svg) adere às [diretrizes de identidade visual para aplicativos](howto-add-branding-in-azure-ad-apps.md).

![](./media/active-directory-integration-checklist/checkbox-two.svg)forneça um nome e um logotipo significativos para seu aplicativo. Essas informações são exibidas no [prompt de consentimento do seu aplicativo](application-consent-experience.md). Certifique-se de que seu nome e logotipo sejam representativos da sua empresa/produto para que os usuários possam tomar decisões informadas. Certifique-se de que você não está violando marcas comerciais.

## <a name="privacy"></a>Privacidade

![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) Forneça links para os termos de serviço e a política de privacidade do seu aplicativo.

## <a name="security"></a>Segurança

![caixa ](./media/active-directory-integration-checklist/checkbox-two.svg) de seleção gerenciar seus URIs de redirecionamento: <ul><li>Mantenha a propriedade de todos os URIs de redirecionamento e mantenha os registros DNS para eles atualizados.</li><li>Não use caracteres curinga (*) em seus URIs.</li><li>Para aplicativos Web, verifique se todos os URIs estão seguros e criptografados (por exemplo, usando esquemas HTTPS).</li><li>Para clientes públicos, use URIs de redirecionamento específicos da plataforma, se aplicável (principalmente para iOS e Android). Caso contrário, use URIs de redirecionamento com uma grande quantidade de aleatoriedade para evitar colisões ao chamar de volta para seu aplicativo.</li><li>Se seu aplicativo estiver sendo usado de um agente Web isolado, você poderá usar o `https://login.microsoftonline.com/common/oauth2/nativeclient` .</li><li>Revise e corte todos os URIs de redirecionamento não utilizados ou desnecessários regularmente.</li></ul>

![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) Se seu aplicativo estiver registrado em um diretório, minimize e monitore manualmente a lista de proprietários de registro de aplicativo.

![a caixa ](./media/active-directory-integration-checklist/checkbox-two.svg) de seleção não habilita o suporte para o [fluxo de concessão implícita OAuth2](v2-oauth2-implicit-grant-flow.md) , a menos que seja explicitamente necessário. Saiba mais sobre o cenário válido [aqui](v2-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant).

![a caixa de seleção ](./media/active-directory-integration-checklist/checkbox-two.svg) vai além do nome de usuário/senha. Não use o [ROPC (fluxo de credenciais de senha) do proprietário do recurso](v2-oauth-ropc.md), que lida diretamente com as senhas dos usuários. Esse fluxo requer um alto grau de confiança e exposição do usuário e deve ser usado somente quando outros fluxos mais seguros não podem ser usados. Esse fluxo ainda é necessário em alguns cenários (como DevOps), mas lembre-se de que seu uso imporá restrições ao aplicativo.  Para abordagens mais modernas, leia [fluxos de autenticação e cenários de aplicativos](authentication-flows-app-scenarios.md).

![caixa de seleção ](./media/active-directory-integration-checklist/checkbox-two.svg) proteger e gerenciar suas credenciais de aplicativo confidenciais para aplicativos Web, APIs da Web e aplicativos de daemon. Use [credenciais de certificado](active-directory-certificate-credentials.md), não credenciais de senha (segredos de cliente). Se você precisar usar uma credencial de senha, não a defina manualmente. Não armazene credenciais no código ou na configuração e nunca permita que elas sejam manipuladas por seres humanos. Se possível, use [identidades gerenciadas para recursos do Azure](../managed-identities-azure-resources/overview.md) ou [Azure Key Vault](../../key-vault/general/basic-concepts.md) para armazenar e girar suas credenciais regularmente.

![caixa ](./media/active-directory-integration-checklist/checkbox-two.svg) de seleção Verifique se seu aplicativo solicita as permissões de privilégios mínimos. Solicite apenas permissões que seu aplicativo precisa, e somente quando você precisar deles. Entenda os diferentes [tipos de permissões](v2-permissions-and-consent.md#permission-types). Use permissões de aplicativo somente se necessário; Use permissões delegadas sempre que possível. Para obter uma lista completa de permissões de Microsoft Graph, consulte esta [referência de permissões](/graph/permissions-reference).

![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) Se você estiver protegendo uma API usando a plataforma de identidade da Microsoft, pense cuidadosamente nas permissões que ele deve expor. Considere qual é a granularidade certa para sua solução e quais permissões exigem o consentimento do administrador. Verifique as permissões esperadas nos tokens de entrada antes de tomar decisões de autorização.

## <a name="implementation"></a>Implementação

![](./media/active-directory-integration-checklist/checkbox-two.svg)a caixa de seleção usa soluções de autenticação modernas (OAuth 2,0, [OpenID Connect](v2-protocols-oidc.md)) para conectar usuários com segurança.

![a caixa de seleção ](./media/active-directory-integration-checklist/checkbox-two.svg) não programa diretamente em protocolos como o OAuth 2,0 e o Open ID. Em vez disso, aproveite a [MSAL (biblioteca de autenticação da Microsoft)](msal-overview.md). As bibliotecas MSAL encapsulam com segurança protocolos de segurança em uma biblioteca fácil de usar e você obtém suporte interno para cenários de [acesso condicional](../conditional-access/overview.md) , [SSO (logon único)](../manage-apps/what-is-single-sign-on.md)de todo o dispositivo e suporte interno a cache de tokens. Para obter mais informações, consulte a lista de bibliotecas de [cliente](reference-v2-libraries.md)com suporte da Microsoft. Se você precisar codificar manualmente os protocolos de autenticação, deverá seguir o SDL da [Microsoft](https://www.microsoft.com/sdl/default.aspx) ou a metodologia de desenvolvimento semelhante. Preste muita atenção às considerações de segurança nas especificações de padrões para cada protocolo.

![a caixa ](./media/active-directory-integration-checklist/checkbox-two.svg) de seleção migrar aplicativos existentes da [Adal (biblioteca de autenticação Azure Active Directory)](../azuread-dev/active-directory-authentication-libraries.md) para a [biblioteca de autenticação da Microsoft](msal-overview.md). A MSAL é a mais recente solução de plataforma de identidade da Microsoft e é preferida à ADAL. Ele está disponível em .NET, JavaScript, Android, iOS, macOS e também está em visualização pública para Python e Java. Leia mais sobre como migrar aplicativos [Adal.net](msal-net-migration.md), [ADAL.js](msal-compare-msal-js-and-adal-js.md)e [Adal.net e Ios Broker](msal-net-migration-ios-broker.md) .

![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) Para aplicativos móveis, configure cada plataforma usando a experiência de registro do aplicativo. Para que seu aplicativo aproveite o Microsoft Authenticator ou o Microsoft Portal da Empresa para logon único, seu aplicativo precisa de um "URI de redirecionamento do Broker" configurado. Isso permite que a Microsoft retorne o controle ao seu aplicativo após a autenticação. Ao configurar cada plataforma, a experiência de registro do aplicativo orientará você pelo processo. Use o guia de início rápido para baixar um exemplo de trabalho. No iOS, use agentes e WebView do sistema sempre que possível.

![caixa ](./media/active-directory-integration-checklist/checkbox-two.svg) de seleção em aplicativos Web ou APIs Web, mantenha um cache de token por conta.  Para aplicativos Web, o cache de token deve ser codificado pela ID da conta.  Para APIs Web, a conta deve ser codificada pelo hash do token usado para chamar a API. O MSAL.NET fornece a serialização de cache de token personalizada nas subplataformas .NET Framework e .NET Core. Por motivos de segurança e desempenho, nossa recomendação é serializar um cache por usuário. Para obter mais informações, leia sobre [serialização de cache de token](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application).

![caixa de seleção ](./media/active-directory-integration-checklist/checkbox-two.svg) se os dados necessários para o seu aplicativo estiverem disponíveis por meio de [Microsoft Graph](https://developer.microsoft.com/graph), solicite permissões para esses dados usando o ponto de extremidade Microsoft Graph em vez da API individual.

![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) Não examine o valor do token de acesso ou tente analisá-lo como um cliente.  Eles podem alterar valores, formatos ou até mesmo serem criptografados sem aviso-sempre use a id_token se o cliente precisar aprender algo sobre o usuário ou chamar Microsoft Graph.  Somente as APIs da Web devem analisar os tokens de acesso (já que são aqueles que definem o formato e definindo as chaves de criptografia).

## <a name="end-user-experience"></a>Experiência do usuário final

![a caixa de seleção ](./media/active-directory-integration-checklist/checkbox-two.svg) [entende a experiência de consentimento](application-consent-experience.md) e configura as partes do prompt de consentimento do seu aplicativo para que os usuários finais e administradores tenham informações suficientes para determinar se confiam no seu aplicativo.

![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) Minimize o número de vezes que um usuário precisa inserir credenciais de logon ao usar seu aplicativo tentando a autenticação silenciosa (aquisição de token silenciosa) antes dos fluxos interativos.

![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) Não use "prompt = consentimento" para cada entrada. Use avisar = consentimento somente se você tiver determinado que precisa pedir permissões adicionais (por exemplo, se tiver alterado as permissões necessárias do aplicativo).

![caixa de seleção ](./media/active-directory-integration-checklist/checkbox-two.svg) quando aplicável, enriquecer seu aplicativo com os dados do usuário. Usar a [API de Microsoft Graph](https://developer.microsoft.com/graph) é uma maneira fácil de fazer isso. A ferramenta [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) que pode ajudá-lo a começar.

![a caixa de seleção ](./media/active-directory-integration-checklist/checkbox-two.svg) registra o conjunto completo de permissões que seu aplicativo requer para que os administradores possam conceder o consentimento facilmente ao seu locatário. Use o [consentimento incremental](../azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) em tempo de execução para ajudar os usuários a entender por que seu aplicativo está solicitando permissões que podem se preocupar ou confundir os usuários quando solicitado na primeira inicialização.

![](./media/active-directory-integration-checklist/checkbox-two.svg)a caixa de seleção implementa uma [experiência de logoff único limpa](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut). Trata-se de uma privacidade e um requisito de segurança, e possibilita uma boa experiência do usuário.

## <a name="testing"></a>Testando

![](./media/active-directory-integration-checklist/checkbox-two.svg)teste de caixa de seleção para [políticas de acesso condicional](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) que podem afetar a capacidade dos usuários de usar seu aplicativo.

![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) Teste seu aplicativo com todas as contas possíveis às quais você planeja dar suporte (por exemplo, contas corporativas ou de estudante, contas pessoais da Microsoft, contas filho e contas soberanas).

## <a name="additional-resources"></a>Recursos adicionais

Explore informações detalhadas sobre a v2.0:

* [Plataforma Microsoft Identity (visão geral)](v2-overview.md)
* [Referência de protocolos da plataforma Microsoft Identity](active-directory-v2-protocols.md)
* [Referência de tokens de acesso](access-tokens.md)
* [Referência de tokens de ID](id-tokens.md)
* [Referência de bibliotecas de autenticação](reference-v2-libraries.md)
* [Permissões e consentimento na plataforma de identidade da Microsoft](v2-permissions-and-consent.md)
* [API do Microsoft Graph](https://developer.microsoft.com/graph)

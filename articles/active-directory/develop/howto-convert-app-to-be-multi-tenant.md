---
title: Compilar aplicativos que se conectam a usuários do Azure AD
titleSuffix: Microsoft identity platform
description: Mostra como criar um aplicativo multilocatário que pode conectar um usuário de qualquer locatário Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 10/27/2020
ms.author: ryanwi
ms.reviewer: marsma, jmprieur, lenalepa, sureshja, kkrishna
ms.custom: aaddev
ms.openlocfilehash: 825a7d8c53552120a861657c7f3df7ae8f488c18
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581713"
---
# <a name="how-to-sign-in-any-azure-active-directory-user-using-the-multi-tenant-application-pattern"></a>Como: entrar em qualquer usuário do Azure Active Directory usando o padrão de aplicativo de vários inquilinos

Se você oferecer um aplicativo de Software como Serviço (SaaS) para muitas organizações, poderá configurar seu aplicativo para aceitar logins de qualquer locatário do Azure AD (Azure Active Directory). Essa configuração é chamada *de tornar seu aplicativo multilocatário*. Os usuários em qualquer locatário do Azure AD poderão entrar em seu aplicativo após o consentimento para usar sua conta com o aplicativo.

Se você tiver um aplicativo que tem seu próprio sistema de contas ou que dá suporte a outros tipos de conexão por meio de outros provedores de nuvem, a adição da conexão do Azure AD em qualquer locatário será simples. Basta registrar seu aplicativo, adicionar código de entrada via OAuth2, OpenID Connect ou SAML e colocar um [botão "entrar com a conta da Microsoft"][AAD-App-Branding] em seu aplicativo.

> [!NOTE]
> Este artigo pressupõe que você já esteja familiarizado com a criação de um aplicativo de locatário único para o Azure AD. Se você não estiver, comece com uma das iniciações rápidas na [página inicial do guia do desenvolvedor][AAD-Dev-Guide].

Há quatro etapas para converter seu aplicativo em um aplicativo multilocatário do Azure AD:

1. [Atualizar seu registro de aplicativo para ser multilocatário](#update-registration-to-be-multi-tenant)
2. [Atualizar seu código para enviar solicitações para o ponto de extremidade /common](#update-your-code-to-send-requests-to-common)
3. [Atualizar seu código para lidar com vários valores de emissor](#update-your-code-to-handle-multiple-issuer-values)
4. [Entender o consentimento do usuário e administrador e fazer as alterações de código apropriadas](#understand-user-and-admin-consent)

Vamos examinar cada etapa detalhadamente. Você também pode saltar diretamente para o exemplo [criar um aplicativo Web SaaS multilocatário que chama Microsoft Graph usando o Azure AD e o OpenID Connect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md).

## <a name="update-registration-to-be-multi-tenant"></a>Atualizar o registro para ser multilocatário

Por padrão, os registros de aplicativo/API Web no Azure AD são de locatário único. Você pode tornar seu registro multilocatário localizando a opção de **tipos de conta com suporte** no painel de **autenticação** do registro do aplicativo no [portal do Azure][AZURE-portal] e definindo-o como **contas em qualquer diretório organizacional**.

Antes de um aplicativo poder ser definido como multilocatário, o Azure AD requer que o URI da ID do Aplicativo seja globalmente exclusivo. O URI da ID do Aplicativo é uma das maneiras que um aplicativo é identificado em mensagens de protocolo. Para um aplicativo de locatário único, é suficiente que o URI da ID do Aplicativo seja exclusivo nesse locatário. Para um aplicativo multilocatário, ele deve ser globalmente exclusivo para que o Azure AD possa localizar os aplicativos em todos os locatários. A exclusividade global é imposta exigindo o URI da ID do Aplicativo com um nome de host que corresponda a um domínio verificado do locatário do Azure AD.

Por padrão, aplicativos criados por meio do portal do Azure têm um URI da ID do Aplicativo globalmente exclusivo definido na criação do aplicativo, mas é possível alterar esse valor. Por exemplo, se o nome do seu locatário fosse contoso.onmicrosoft.com, um URI da ID do Aplicativo válido seria `https://contoso.onmicrosoft.com/myapp`. Se seu locatário tivesse um domínio verificado de `contoso.com`, então um URI da ID do Aplicativo também seria `https://contoso.com/myapp`. A configuração de um aplicativo como multilocatário falhará se o URI da ID do Aplicativo não seguir esse padrão.

## <a name="update-your-code-to-send-requests-to-common"></a>Atualizar seu código para enviar solicitações para /common

Em um aplicativo de locatário único, as solicitações de entrada são enviadas para o ponto de extremidade de entrada do locatário. Por exemplo, para contoso.onmicrosoft.com, o ponto de extremidade seria: `https://login.microsoftonline.com/contoso.onmicrosoft.com`. Solicitações enviadas para o ponto de extremidade de um locatário podem realizar a entrada de usuários (ou convidados) naquele locatário para aplicativos nele.

Com um aplicativo multilocatário, o aplicativo não sabe com antecedência de qual locatário o usuário é, portanto, você não pode enviar solicitações para o ponto de extremidade de um locatário. Em vez disso, as solicitações são enviadas para um ponto de extremidade que multiplexa entre todos os locatários do Azure AD: `https://login.microsoftonline.com/common`

Quando a plataforma de identidade da Microsoft recebe uma solicitação no ponto de extremidade/Common, ela assina o usuário em e, como consequência, descobre de qual locatário o usuário é. O ponto de extremidade/Common funciona com todos os protocolos de autenticação compatíveis com o Azure AD: OpenID Connect, OAuth 2,0, SAML 2,0 e WS-Federation.

Em seguida, a resposta de conexão para o aplicativo conterá um token que representa o usuário. O valor do emissor no token diz a um aplicativo de qual locatário o usuário é. Quando uma resposta retorna do ponto de extremidade /common, o valor do emissor no token corresponde ao locatário do usuário.

> [!IMPORTANT]
> O ponto de extremidade /common não é um locatário e não é um emissor, ele é apenas um multiplexador. Ao usar /common, a lógica em seu aplicativo para validar tokens precisa ser atualizada para considerar isso.

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>Atualizar seu código para lidar com vários valores de emissor

Aplicativos Web e APIs Web recebem e validam tokens da plataforma Microsoft Identity.

> [!NOTE]
> Embora aplicativos cliente nativos solicitem e recebam tokens da plataforma Microsoft Identity, eles fazem isso para enviá-los para APIs, onde eles são validados. Os aplicativos nativos não validam tokens de acesso e devem tratá-los como opacos.

Vejamos como um aplicativo valida tokens que recebe da plataforma de identidade da Microsoft. Um aplicativo de locatário único normalmente usa um valor de ponto de extremidade como:

```http
https://login.microsoftonline.com/contoso.onmicrosoft.com
```

... e o usa para construir uma URL de metadados (neste caso, OpenID Connect) como:

```http
https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration
```

para baixar duas partes críticas de informações que são usadas para validar tokens: as chaves de assinatura do locatário e o valor de emissor.

Cada locatário do Azure AD tem um valor de emissor exclusivo do formato:

```http
https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/
```

... em que o valor de GUID é a versão de renomeação segura da ID de locatário do locatário. Se você clicar no link de metadados anterior para `contoso.onmicrosoft.com`, poderá ver esse valor de emissor no documento.

Quando um aplicativo de locatário único valida um token, ele verifica a assinatura do token em relação às chaves de assinatura do documento de metadados. Esse teste permite garantir que o valor do emissor no token corresponde ao que foi encontrado no documento de metadados.

Como o ponto de extremidade /common não corresponde a um locatário e não é um emissor, ao examinar o valor do emissor nos metadados para /common, ele tem uma URL de modelo em vez de um valor real:

```http
https://sts.windows.net/{tenantid}/
```

Portanto, um aplicativo multilocatário não pode validar tokens apenas combinando o valor do emissor nos metadados com o valor `issuer` no token. Um aplicativo multilocatário precisa de lógica para decidir quais valores de emissor são válidos e quais não são baseados na parte da ID do locatário do valor do emissor.

Por exemplo, se um aplicativo multilocatário permite apenas a conexão em locatários específicos que se inscreveram para seu serviço, ele deve verificar o valor do emissor ou o valor da declaração `tid` no token para ter certeza de que o locatário está em sua lista de assinantes. Se um aplicativo multilocatário lida apenas com pessoas e não toma nenhuma decisão de acesso com base em locatários, ele pode ignorar o valor de emissor completamente.

Nas [amostras de multilocatário][AAD-Samples-MT], a validação do emissor está desabilitada para permitir a conexão de qualquer locatário do Azure AD.

## <a name="understand-user-and-admin-consent"></a>Entenda o consentimento do usuário e do administrador

Para um usuário entrar em um aplicativo no Azure AD, o aplicativo deve estar representado no locatário do usuário. Isso permite que a organização realize ações como aplicar políticas exclusivas quando usuários de seu locatário entrarem no aplicativo. Para um aplicativo de locatário único, esse registro é mais fácil; é aquele que acontece quando você registra o aplicativo no [portal do Azure][AZURE-portal].

Para um aplicativo multilocatário, o registro inicial para o aplicativo reside no locatário do Azure AD usado pelo desenvolvedor. Quando um usuário de um locatário diferente entra no aplicativo pela primeira vez, o Azure AD solicita que ele consinta com as permissões solicitadas pelo aplicativo. Se ele fornecer o consentimento, uma representação do aplicativo chamada uma *entidade de serviço* será criada no locatário do usuário e o processo de conexão poderá continuar. Uma delegação também é criada no diretório que registra o consentimento do usuário para o aplicativo. Para obter detalhes sobre o aplicativo e os objetos do serviço de aplicativo e como eles se relacionam entre si, consulte [objetos de aplicativo e objetos de entidade de segurança][AAD-App-SP-Objects].

![Ilustra o consentimento para o aplicativo de camada única][Consent-Single-Tier]

Essa experiência de consentimento é afetada pelas permissões solicitadas pelo aplicativo. A plataforma de identidade da Microsoft dá suporte a dois tipos de permissões, somente de aplicativo e delegadas.

* Uma permissão delegada concede a um aplicativo a capacidade de atuar como um usuário conectado para um subconjunto das ações que o usuário pode realizar. Por exemplo, você pode conceder a um aplicativo a permissão delegada para ler o calendário do usuário conectado.
* Uma permissão somente do aplicativo é concedida diretamente à identidade do aplicativo. Por exemplo, você pode conceder a permissão somente do aplicativo a um aplicativo para ler a lista de usuários em um locatário, independentemente de quem estiver conectado ao aplicativo.

Algumas permissões podem ser consentidas por um usuário normal, enquanto outras exigem o consentimento de um administrador de locatários.

Para saber mais sobre o consentimento do usuário e do administrador, consulte [Configurar o fluxo de trabalho de consentimento do administrador](../manage-apps/configure-admin-consent-workflow.md).

### <a name="admin-consent"></a>Consentimento do administrador

As permissões somente do aplicativo sempre exigem o consentimento do administrador de locatários. Se o aplicativo solicitar uma permissão somente do aplicativo e um usuário tentar entrar no aplicativo, uma mensagem de erro será exibida informando que o usuário não pode fornecer o consentimento.

Algumas permissões delegadas também exigem o consentimento do administrador de locatários. Por exemplo, a capacidade de gravar no Azure AD como o usuário conectado requer o consentimento de um administrador de locatários. Semelhante às permissões somente do aplicativo, se um usuário comum tenta entrar em um aplicativo que solicita uma permissão delegada que exige o consentimento do administrador, seu aplicativo recebe um erro. Uma permissão exigir ou não o consentimento do administrador é determinado pelo desenvolvedor que publicou o recurso e pode ser encontrado na documentação do recurso. A documentação de permissões para a [API Microsoft Graph][MSFT-Graph-permission-scopes] indica quais permissões exigem o consentimento do administrador.

Se seu aplicativo usa permissões que exigem o consentimento do administrador, tenha um gesto como um botão ou link no qual o administrador pode iniciar a ação. A solicitação que seu aplicativo envia para essa ação é uma solicitação de autorização do OAuth2/OpenID Connect normal, que também inclui o parâmetro de cadeia de caracteres de consulta `prompt=admin_consent`. Depois que o administrador fornecer seu consentimento e a entidade de serviço for criada no locatário do cliente, as próximas solicitações de conexão não precisarão do parâmetro `prompt=admin_consent`. Uma vez que o administrador tiver decidido que as permissões solicitadas forem aceitáveis, não será solicitado o consentimento de nenhum outro usuário no locatário daquele ponto em diante.

Um administrador de locatários pode desabilitar a capacidade dos usuários regulares consentirem aplicativos. Se essa funcionalidade estiver desabilitada, o consentimento do administrador sempre será necessário para o aplicativo a ser usado no locatário. Se você quiser testar seu aplicativo com o consentimento do usuário final desabilitado, poderá encontrar a opção de configuração no [portal do Azure][AZURE-portal] na seção **[configurações do usuário](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)** em **aplicativos empresariais**.

O parâmetro `prompt=admin_consent` também pode ser usado por aplicativos que exigem permissões que não necessitam do consentimento do administrador. Um exemplo de como isso seria usado é quando o aplicativo exige uma experiência na qual o administrador de locatários “se inscreve” uma vez e não é solicitado o consentimento de nenhum outro usuário desse momento em diante.

Se um aplicativo exigir o consentimento do administrador e um administrador fizer logon, mas o parâmetro `prompt=admin_consent` não for enviado, o administrador fornecerá consentimento ao aplicativo **somente para a conta de usuário dele**. Os usuários normais ainda não poderão entrar ou dar consentimento ao aplicativo. Esse recurso é útil se você quiser conceder ao administrador de locatários a capacidade de explorar seu aplicativo antes de permitir o acesso de outros usuários.

### <a name="consent-and-multi-tier-applications"></a>Aplicativos de várias camadas e consentimento

Seu aplicativo pode ter várias camadas, cada uma representada por seu próprio registro no Azure AD. Por exemplo, um aplicativo nativo que chama uma API Web ou um aplicativo Web que chama uma API Web. Em ambos os casos, o cliente (aplicativo nativo ou aplicativo Web) solicita permissões para chamar o recurso (API Web). Para o cliente ter o consentimento com êxito em um locatário do cliente, todos os recursos aos quais ele solicita permissões já devem existir no locatário do cliente. Se essa condição não for atendida, o Azure AD retornará um erro de que o recurso deve ser adicionado primeiro.

#### <a name="multiple-tiers-in-a-single-tenant"></a>Várias camadas em um único locatário

Isso poderá ser um problema se seu aplicativo lógico consistir em dois ou mais registros de aplicativo, por exemplo, um cliente e um recurso separados. Como você obtém o recurso no locatário do cliente primeiro? O Azure AD abrange neste caso permitindo que o cliente e o recurso recebam o consentimento em uma única etapa. O usuário vê a soma total das permissões solicitadas pelo cliente e pelo recurso na página de consentimento. Para permitir esse comportamento, o registro do aplicativo do recurso deve incluir a ID do aplicativo do cliente como um `knownClientApplications` no [manifesto do aplicativo][AAD-App-Manifest]. Por exemplo:

```json
"knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]
```

Isso é demonstrado em uma amostra de chamada de cliente nativo de várias camadas à API da Web na seção [Conteúdo relacionado](#related-content) ao final deste artigo. O diagrama a seguir fornece uma visão geral de consentimento para um aplicativo de várias camadas registrado em um único locatário.

![Ilustra o consentimento para o aplicativo cliente conhecido de várias camadas][Consent-Multi-Tier-Known-Client]

#### <a name="multiple-tiers-in-multiple-tenants"></a>Várias camadas em vários locatários

Um caso semelhante acontecerá se as diferentes camadas de um aplicativo forem registradas em locatários diferentes. Por exemplo, considere o caso da criação de um aplicativo cliente nativo que chama a API do Exchange Online. Para desenvolver o aplicativo e posteriormente para o aplicativo nativo ser executado no locatário de um cliente, a entidade de serviço do Exchange Online deve estar presente. Nesse caso, o desenvolvedor e o cliente devem adquirir o Exchange Online para que a entidade de serviço seja criada em seus locatários.

Se for uma API criada por uma organização que não seja a Microsoft, o desenvolvedor da API precisará fornecer uma maneira para seus clientes consentirem o aplicativo nos locatários de seus clientes. O design recomendado é que o desenvolvedor de terceiros crie a API de modo que ela também possa funcionar como um cliente Web para implementar a inscrição. Para fazer isso:

1. Siga as seções anteriores para garantir que a API implemente os requisitos de código/registro do aplicativo multilocatário.
2. Além de expor os escopos/funções da API, verifique se o registro inclui a permissão "entrar e ler perfil do usuário" (fornecido por padrão).
3. Implemente uma página de login / inscrição no cliente da Web e siga as orientações do [consentimento do administrador](#admin-consent).
4. Depois que o usuário fornecer consentimento para o aplicativo, a entidade de serviço e os links de delegação de consentimento serão criados em seu locatário e o aplicativo nativo poderá obter tokens para a API.

O seguinte diagrama fornece uma visão geral de consentimento para um aplicativo de várias camadas registrado em diferentes locatários.

![Ilustra o consentimento para um aplicativo multicamadas de várias camadas][Consent-Multi-Tier-Multi-Party]

### <a name="revoking-consent"></a>Revogando o consentimento

Os usuários e administradores podem revogar o consentimento para seu aplicativo a qualquer momento:

* Os usuários revogam o acesso a aplicativos individuais removendo-os da lista de [Aplicativos do Painel de Acesso][AAD-Access-Panel].
* Os administradores revogam o acesso a aplicativos removendo-os usando a seção [aplicativos empresariais](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) do [portal do Azure][AZURE-portal].

Se um administrador der o consentimento a um aplicativo para todos os usuários em um locatário, os usuários não poderão revogar o acesso individualmente. Somente o administrador pode revogar o acesso e somente para o aplicativo inteiro.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Aplicativos multilocatários e caching de tokens de acesso

Os aplicativos multilocatários também podem obter tokens de acesso para chamar APIs que são protegidas pelo Azure AD. Um erro comum ao usar a MSAL (biblioteca de autenticação da Microsoft) com um aplicativo multilocatário é solicitar inicialmente um token para um usuário usando/Common, receber uma resposta e solicitar um token subsequente para o mesmo usuário também usando/Common. Como a resposta do Azure AD vem de um locatário, não/Common, o MSAL armazena em cache o token como sendo do locatário. A chamada subsequente para /common para obter um token de acesso para o usuário perde a entrada de cache e o usuário é solicitado a entrar novamente. Para evitar a perda de cache, certifique-se de que as chamadas subsequentes para um usuário já conectado sejam feitas para o ponto de extremidade do locatário.

## <a name="related-content"></a>Conteúdo relacionado

* [Exemplo de aplicativo multilocatário](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md)
* [Diretrizes de identidade visual para aplicativos][AAD-App-Branding]
* [Objetos de aplicativo e objetos de entidade de serviço][AAD-App-SP-Objects]
* [Integrando aplicativos ao Azure Active Directory][AAD-Integrating-Apps]
* [Visão geral da estrutura de consentimento][AAD-Consent-Overview]
* [Escopos de permissão da API do Microsoft Graph][MSFT-Graph-permission-scopes]

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a criar um aplicativo que pode conectar um usuário por meio de qualquer locatário do Azure AD. Depois de habilitar o SSO (Sign-On único) entre seu aplicativo e o Azure AD, você também pode atualizar seu aplicativo para acessar as APIs expostas por recursos da Microsoft, como Microsoft 365. Portanto, é possível oferecer uma experiência personalizada no aplicativo, por exemplo, mostrando informações contextuais para os usuários, como suas imagens de perfil ou seus próximos compromissos no calendário.

Para saber mais sobre como fazer chamadas à API para o Azure AD e Microsoft 365 serviços como o Exchange, SharePoint, OneDrive, OneNote e muito mais, visite [Microsoft Graph API][MSFT-Graph-overview].

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]:howto-add-branding-in-azure-ad-apps.md
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Consent-Overview]:consent-framework.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Samples-MT]: /samples/browse/?products=azure-active-directory
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-portal]: https://portal.azure.com
[MSFT-Graph-overview]: /graph/
[MSFT-Graph-permission-scopes]: /graph/permissions-reference

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-single-tier.svg
[Consent-Multi-Tier-Known-Client]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-multi-tier-known-clients.svg
[Consent-Multi-Tier-Multi-Party]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-multi-tier-multi-party.svg

<!--Reference style links -->
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AAD-V2-Dev-Guide]: v2-overview.md
[AZURE-portal]: https://portal.azure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: /graph/permissions-reference
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken
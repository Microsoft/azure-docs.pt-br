---
title: Como criar um aplicativo que pode conectar qualquer usuário do Azure AD
description: Mostra como criar um aplicativo multilocatário que pode conectar um usuário de qualquer locatário do Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 35af95cb-ced3-46ad-b01d-5d2f6fd064a3
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviewer: jmprieur, lenalepa, sureshja
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d53ed0c9a8ae63c2cb0ced635c6f0a8e8a3222fd
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482735"
---
# <a name="how-to-sign-in-any-azure-active-directory-user-using-the-multi-tenant-application-pattern"></a>Como: Entrar em qualquer usuário do Azure Active Directory usando o padrão de aplicativo multilocatário

Se você oferecer um aplicativo de Software como Serviço (SaaS) para muitas organizações, poderá configurar seu aplicativo para aceitar logins de qualquer locatário do Azure AD (Azure Active Directory). Essa configuração é chamada de *, tornando seu aplicativo multilocatário*. Os usuários em qualquer locatário do Azure AD poderão entrar em seu aplicativo após o consentimento para usar sua conta com o aplicativo.

Se você tiver um aplicativo que tem seu próprio sistema de contas ou que dá suporte a outros tipos de conexão por meio de outros provedores de nuvem, a adição da conexão do Azure AD em qualquer locatário será simples. Registrar seu aplicativo, adicione o código de entrada por meio de OAuth2, OpenID Connect ou SAML e colocar apenas um [botão "Entrar com a Microsoft"][AAD-App-Branding] em seu aplicativo.

> [!NOTE]
> Este artigo pressupõe que você já está familiarizado com a criação de um aplicativo de locatário único do Azure AD. Se você não tiver, começar com uma das guias de início rápido sobre o [homepage do guia do desenvolvedor][AAD-Dev-Guide].

Há quatro etapas simples para converter seu aplicativo em um aplicativo multilocatário do Azure AD:

1. [Atualizar seu registro de aplicativo para ser multilocatário](#update-registration-to-be-multi-tenant)
2. [Atualizar seu código para enviar solicitações para o ponto de extremidade /common](#update-your-code-to-send-requests-to-common)
3. [Atualizar seu código para lidar com vários valores de emissor](#update-your-code-to-handle-multiple-issuer-values)
4. [Entender o consentimento do usuário e administrador e fazer as alterações de código apropriadas](#understand-user-and-admin-consent)

Vamos examinar cada etapa detalhadamente. Você também pode ir diretamente para [essa lista de exemplos de multilocatário][AAD-Samples-MT].

## <a name="update-registration-to-be-multi-tenant"></a>Atualizar o registro para ser multilocatário

Por padrão, os registros de API/aplicativo Web no Azure AD são de locatário único. Pode tornar o registro multilocatário Localizando a **suporte para tipos de conta** ative o **autenticação** painel do registro do aplicativo na [doportaldoAzure][AZURE-portal] e configurando-a como **contas em qualquer diretório organizacional**.

Antes de um aplicativo poder ser definido como multilocatário, o Azure AD requer que o URI da ID do Aplicativo seja globalmente exclusivo. O URI da ID do Aplicativo é uma das maneiras que um aplicativo é identificado em mensagens de protocolo. Para um aplicativo de locatário único, é suficiente que o URI da ID do Aplicativo seja exclusivo nesse locatário. Para um aplicativo multilocatário, ele deve ser globalmente exclusivo para que o Azure AD possa localizar os aplicativos em todos os locatários. A exclusividade global é imposta exigindo o URI da ID do Aplicativo com um nome de host que corresponda a um domínio verificado do locatário do Azure AD.

Por padrão, aplicativos criados por meio do portal do Azure têm um URI da ID do Aplicativo globalmente exclusivo definido na criação do aplicativo, mas é possível alterar esse valor. Por exemplo, se o nome do seu locatário fosse contoso.onmicrosoft.com, um URI da ID do Aplicativo válido seria `https://contoso.onmicrosoft.com/myapp`. Se seu locatário tivesse um domínio verificado de `contoso.com`, então um URI da ID do Aplicativo também seria `https://contoso.com/myapp`. A configuração de um aplicativo como multilocatário falhará se o URI da ID do Aplicativo não seguir esse padrão.

> [!NOTE]
> Registros de cliente nativo, bem como [aplicativos da plataforma Microsoft identity](./active-directory-appmodel-v2-overview.md) são multilocatários por padrão. Não é necessário realizar ações para transformar esses registros de aplicativo em multilocatários.

## <a name="update-your-code-to-send-requests-to-common"></a>Atualizar seu código para enviar solicitações para /common

Em um aplicativo de locatário único, as solicitações de conexão são enviadas para o ponto de extremidade de conexão do locatário. Por exemplo, para contoso.onmicrosoft.com, o ponto de extremidade seria: `https://login.microsoftonline.com/contoso.onmicrosoft.com`. Solicitações enviadas para o ponto de extremidade de um locatário podem realizar a entrada de usuários (ou convidados) naquele locatário para aplicativos nele.

Com um aplicativo multilocatário, o aplicativo não sabe com antecedência de qual locatário o usuário é, portanto, você não pode enviar solicitações para o ponto de extremidade de um locatário. Em vez disso, as solicitações são enviadas para um ponto de extremidade que multiplexa entre todos os locatários do Azure AD: `https://login.microsoftonline.com/common`

Quando a plataforma de identidade Microsoft recebe uma solicitação em /common ponto de extremidade, ele conecta o usuário e, como consequência, descobre qual locatário o usuário é. O ponto de extremidade comum funciona com todos os protocolos de autenticação suportados pelo Azure AD:  OpenID Connect, OAuth 2.0, SAML 2.0 e WS-Federation.

Em seguida, a resposta de conexão para o aplicativo conterá um token que representa o usuário. O valor do emissor no token diz a um aplicativo de qual locatário o usuário é. Quando uma resposta retorna do ponto de extremidade /common, o valor do emissor no token corresponde ao locatário do usuário.

> [!IMPORTANT]
> O ponto de extremidade /common não é um locatário e não é um emissor, ele é apenas um multiplexador. Ao usar /common, a lógica em seu aplicativo para validar tokens precisa ser atualizada para considerar isso.

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>Atualizar seu código para lidar com vários valores de emissor

Aplicativos da Web e APIs web recebem e validam os tokens da plataforma do Microsoft identity.

> [!NOTE]
> Enquanto os aplicativos cliente nativos solicitem e recebam tokens da plataforma do Microsoft identity, eles fazem isso para enviá-los para APIs, onde eles são validados. Os aplicativos nativos não validam os tokens e devem tratá-los como opacos.

Vamos dar uma olhada em como um aplicativo valida os tokens que ele recebe da plataforma do Microsoft identity. Um aplicativo de locatário único normalmente tem um valor de ponto de extremidade como:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

e o usará para criar uma URL de metadados (nesse caso, OpenID Connect) como:

    https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration

para baixar duas partes críticas de informações que são usadas para validar tokens: as chaves de assinatura do locatário e o valor de emissor. Cada locatário do Azure AD tem um valor de emissor exclusivo do formato:

    https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/

em que o valor de GUID é a versão à prova de renomeação da ID de locatário. Se você clicar no link de metadados anterior para `contoso.onmicrosoft.com`, poderá ver esse valor de emissor no documento.

Quando um aplicativo de locatário único valida um token, ele verifica a assinatura do token em relação às chaves de autenticação do documento de metadados. Esse teste permite garantir que o valor do emissor no token corresponde ao que foi encontrado no documento de metadados.

Como o ponto de extremidade /common não corresponde a um locatário e não é um emissor, ao examinar o valor do emissor nos metadados para /common, ele tem uma URL de modelo em vez de um valor real:

    https://sts.windows.net/{tenantid}/

Portanto, um aplicativo multilocatário não pode validar tokens apenas combinando o valor do emissor nos metadados com o valor `issuer` no token. Um aplicativo multilocatário precisa de lógica para decidir quais valores de emissor são válidos e quais não são baseados na parte da ID do locatário do valor do emissor. 

Por exemplo, se um aplicativo multilocatário permite apenas a conexão em locatários específicos que se inscreveram para seu serviço, ele deve verificar o valor do emissor ou o valor da declaração `tid` no token para ter certeza de que o locatário está em sua lista de assinantes. Se um aplicativo multilocatário lida apenas com pessoas e não toma nenhuma decisão de acesso com base em locatários, ele pode ignorar o valor de emissor completamente.

No [exemplos de multilocatário][AAD-Samples-MT], validação do emissor está desabilitada para habilitar qualquer locatário do Azure AD entrar.

## <a name="understand-user-and-admin-consent"></a>Entenda o consentimento do usuário e do administrador

Para um usuário entrar em um aplicativo no Azure AD, o aplicativo deve estar representado no locatário do usuário. Isso permite que a organização realize ações como aplicar políticas exclusivas quando usuários de seu locatário entrarem no aplicativo. Para um aplicativo de locatário único esse registro é simple; ele é o que acontece quando você registra o aplicativo na [portal do Azure][AZURE-portal].

Para um aplicativo multilocatário, o registro inicial para o aplicativo reside no locatário do Azure AD usado pelo desenvolvedor. Quando um usuário de um locatário diferente entra no aplicativo pela primeira vez, o Azure AD solicita que ele consinta com as permissões solicitadas pelo aplicativo. Se ele fornecer o consentimento, uma representação do aplicativo chamada uma *entidade de serviço* será criada no locatário do usuário e o processo de conexão poderá continuar. Uma delegação também é criada no diretório que registra o consentimento do usuário para o aplicativo. Para obter detalhes sobre objetos Application e ServicePrincipal do aplicativo e como elas se relacionam entre si, consulte [objetos de aplicativo e objetos de entidade de serviço][AAD-App-SP-Objects].

![Ilustra o consentimento ao aplicativo de camada única][Consent-Single-Tier]

Essa experiência de consentimento é afetada pelas permissões solicitadas pelo aplicativo. Plataforma de identidade da Microsoft dá suporte a dois tipos de permissões, somente de aplicativo e delegadas.

* Uma permissão delegada concede a um aplicativo a capacidade de atuar como um usuário conectado para um subconjunto das ações que o usuário pode realizar. Por exemplo, você pode conceder a um aplicativo a permissão delegada para ler o calendário do usuário conectado.
* Uma permissão somente do aplicativo é concedida diretamente à identidade do aplicativo. Por exemplo, você pode conceder a permissão somente do aplicativo a um aplicativo para ler a lista de usuários em um locatário, independentemente de quem estiver conectado ao aplicativo.

Algumas permissões podem ser consentidas por um usuário normal, enquanto outras exigem o consentimento de um administrador de locatários. 

### <a name="admin-consent"></a>Consentimento do administrador

As permissões somente do aplicativo sempre exigem o consentimento do administrador de locatários. Se o aplicativo solicitar uma permissão somente do aplicativo e um usuário tentar entrar no aplicativo, uma mensagem de erro será exibida informando que o usuário não pode fornecer o consentimento.

Algumas permissões delegadas também exigem o consentimento do administrador de locatários. Por exemplo, a capacidade de gravar no Azure AD como o usuário conectado requer o consentimento de um administrador de locatários. Semelhante às permissões somente do aplicativo, se um usuário comum tenta entrar em um aplicativo que solicita uma permissão delegada que exige o consentimento do administrador, seu aplicativo recebe um erro. Uma permissão exigir ou não o consentimento do administrador é determinado pelo desenvolvedor que publicou o recurso e pode ser encontrado na documentação do recurso. A documentação de permissões para o [API do Azure AD Graph][AAD-Graph-Perm-Scopes] and [Microsoft Graph API][MSFT-Graph-permission-scopes] indicar quais permissões exigem o consentimento do administrador.

Se o aplicativo usar permissões que exigem o consentimento do administrador, você precisará ter um gesto como um botão ou link, em que o administrador pode iniciar a ação. A solicitação que seu aplicativo envia para essa ação é uma solicitação de autorização do OAuth2/OpenID Connect normal, que também inclui o parâmetro de cadeia de caracteres de consulta `prompt=admin_consent`. Depois que o administrador fornecer seu consentimento e a entidade de serviço for criada no locatário do cliente, as próximas solicitações de conexão não precisarão do parâmetro `prompt=admin_consent`. Uma vez que o administrador tiver decidido que as permissões solicitadas forem aceitáveis, não será solicitado o consentimento de nenhum outro usuário no locatário daquele ponto em diante.

Um administrador de locatários pode desabilitar a capacidade dos usuários regulares consentirem aplicativos. Se essa funcionalidade estiver desabilitada, o consentimento do administrador sempre será necessário para o aplicativo a ser usado no locatário. Se você quiser testar seu aplicativo com o consentimento do usuário final desabilitado, você poderá encontrar a opção de configuração na [portal do Azure][AZURE-portal] na **[configurações do usuário](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)** seção sob **Aplicativos empresariais**.

O parâmetro `prompt=admin_consent` também pode ser usado por aplicativos que exigem permissões que não necessitam do consentimento do administrador. Um exemplo de como isso seria usado é quando o aplicativo exige uma experiência na qual o administrador de locatários “se inscreve” uma vez e não é solicitado o consentimento de nenhum outro usuário desse momento em diante.

Se um aplicativo exigir o consentimento do administrador e um administrador fizer logon, mas o parâmetro `prompt=admin_consent` não for enviado, o administrador fornecerá consentimento ao aplicativo **somente para a conta de usuário dele**. Os usuários normais ainda não poderão entrar ou dar consentimento ao aplicativo. Esse recurso é útil se você quiser conceder ao administrador de locatários a capacidade de explorar seu aplicativo antes de permitir o acesso de outros usuários.

> [!NOTE]
> Alguns aplicativos desejam uma experiência em que os usuários normais podem consentir inicialmente e posteriormente o aplicativo pode envolver o administrador e solicitar permissões que exigem o consentimento do administrador. Não é possível fazer isso com um registro de aplicativo v 1.0 no Azure AD hoje. No entanto, o ponto de extremidade do Microsoft identity platform (v2.0) permite que os aplicativos para solicitar permissões em tempo de execução, em vez de no momento do registro, que permite que esse cenário. Para obter mais informações, consulte [ponto de extremidade de plataforma de identidade Microsoft][AAD-V2-Dev-Guide].

### <a name="consent-and-multi-tier-applications"></a>Aplicativos de várias camadas e consentimento

Seu aplicativo pode ter várias camadas, cada uma representada por seu próprio registro no Azure AD. Por exemplo, um aplicativo nativo que chama uma API Web ou um aplicativo Web que chama uma API Web. Em ambos os casos, o cliente (aplicativo nativo ou aplicativo Web) solicita permissões para chamar o recurso (API Web). Para o cliente ter o consentimento com êxito em um locatário do cliente, todos os recursos aos quais ele solicita permissões já devem existir no locatário do cliente. Se essa condição não for atendida, o Azure AD retornará um erro de que o recurso deve ser adicionado primeiro.

#### <a name="multiple-tiers-in-a-single-tenant"></a>Várias camadas em um único locatário

Isso poderá ser um problema se seu aplicativo lógico consistir em dois ou mais registros de aplicativo, por exemplo, um cliente e um recurso separados. Como você obtém o recurso no locatário do cliente primeiro? O Azure AD abrange neste caso permitindo que o cliente e o recurso recebam o consentimento em uma única etapa. O usuário vê a soma total das permissões solicitadas pelo cliente e pelo recurso na página de consentimento. Para habilitar esse comportamento, o registro de aplicativo do recurso deve incluir a ID de aplicativo do cliente como um `knownClientApplications` em seu [manifesto do aplicativo][AAD-App-Manifest]. Por exemplo:

    knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]

Isso é demonstrado em uma amostra de chamada de cliente nativo de várias camadas à API da Web na seção [Conteúdo relacionado](#related-content) ao final deste artigo. O diagrama a seguir fornece uma visão geral de consentimento para um aplicativo de várias camadas registrado em um único locatário.

![Ilustra o consentimento ao aplicativo cliente conhecido de várias camadas][Consent-Multi-Tier-Known-Client]

#### <a name="multiple-tiers-in-multiple-tenants"></a>Várias camadas em vários locatários

Um caso semelhante acontecerá se as diferentes camadas de um aplicativo forem registradas em locatários diferentes. Por exemplo, considere o caso da criação de um aplicativo cliente nativo que chama a API do Office 365 Exchange Online. Para desenvolver o aplicativo e posteriormente para o aplicativo nativo ser executado no locatário de um cliente, a entidade de serviço do Exchange Online deve estar presente. Nesse caso, o desenvolvedor e o cliente devem adquirir o Exchange Online para que a entidade de serviço seja criada em seus locatários.

Se for uma API criada por uma organização diferente da Microsoft, o desenvolvedor da API precisa fornecer uma maneira para seus clientes a consentir o aplicativo em locatários de seus clientes. O design recomendado é para o desenvolvedor de terceiros criar a API, de modo que ele também pode funcionar como um cliente web para implementar a inscrição. Para fazer isso:

1. Siga as seções anteriores para garantir que a API implemente os requisitos de código/registro do aplicativo multilocatário.
2. Além de expor escopos e as funções da API, verifique se o registro inclui a "entrar e ler o perfil de usuário" permissão (fornecido por padrão).
3. Implemente uma página de login / inscrição no cliente da Web e siga as orientações do [consentimento do administrador](#admin-consent).
4. Depois que o usuário fornecer consentimento para o aplicativo, a entidade de serviço e os links de delegação de consentimento serão criados em seu locatário e o aplicativo nativo poderá obter tokens para a API.

O seguinte diagrama fornece uma visão geral de consentimento para um aplicativo de várias camadas registrado em diferentes locatários.

![Ilustra o consentimento ao aplicativo de vários fornecedores de várias camado][Consent-Multi-Tier-Multi-Party]

### <a name="revoking-consent"></a>Revogando o consentimento

Os usuários e administradores podem revogar o consentimento para seu aplicativo a qualquer momento:

* Os usuários revogam o acesso a aplicativos individuais removendo-os da sua [aplicativos do painel de acesso][AAD-Access-Panel] lista.
* Os administradores revogam o acesso a aplicativos removendo-os usando o [aplicativos empresariais](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) seção o [portal do Azure][AZURE-portal].

Se um administrador der o consentimento a um aplicativo para todos os usuários em um locatário, os usuários não poderão revogar o acesso individualmente. Somente o administrador pode revogar o acesso e somente para o aplicativo inteiro.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Aplicativos multilocatários e caching de tokens de acesso

Os aplicativos multilocatários também podem obter tokens de acesso para chamar APIs que são protegidas pelo Azure AD. Um erro comum ao usar a ADAL (Biblioteca de Autenticação do Active Directory) com um aplicativo multilocatário é solicitar inicialmente um token para um usuário usando /common, receber uma resposta e, depois, solicitar um próximo token para o mesmo usuário também usando /common. Como a resposta do Azure AD vem de um locatário, e não de /common, a ADAL armazena em cache o token como sendo do locatário. A chamada subsequente para /common para obter um token de acesso para o usuário perde a entrada de cache e o usuário é solicitado a entrar novamente. Para evitar a perda de cache, certifique-se de que as chamadas subsequentes para um usuário já conectado sejam feitas para o ponto de extremidade do locatário.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a criar um aplicativo que pode conectar um usuário por meio de qualquer locatário do Azure AD. Depois de habilitar o SSO (Logon Único) entre o aplicativo e o Azure AD, também é possível atualizar o aplicativo para acessar as APIs expostas por recursos da Microsoft, como o Office 365. Portanto, é possível oferecer uma experiência personalizada no aplicativo, por exemplo, mostrando informações contextuais para os usuários, como suas imagens de perfil ou seus próximos compromissos no calendário. Para saber mais sobre como fazer a API chama o Azure AD e Office 365 de serviços como Exchange, SharePoint, OneDrive, OneNote e obter mais informações, visite [API do Microsoft Graph][MSFT-Graph-overview].

## <a name="related-content"></a>Conteúdo relacionado

* [Exemplos de aplicativo multilocatário][AAD-Samples-MT]
* [Diretrizes de identidade visual para aplicativos][AAD-App-Branding]
* [Objetos de aplicativo e objetos de entidade de serviço][AAD-App-SP-Objects]
* [Integrando aplicativos com o Active Directory do Azure][AAD-Integrating-Apps]
* [Visão geral da estrutura de consentimento][AAD-Consent-Overview]
* [Escopos de permissão da API do Microsoft Graph][MSFT-Graph-permission-scopes]
* [Escopos de permissão da API do Graph do AD do Azure][AAD-Graph-Perm-Scopes]

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]:howto-add-branding-in-azure-ad-apps.md
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Consent-Overview]:consent-framework.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Graph-Overview]: https://azure.microsoft.com/documentation/articles/active-directory-graph-api/
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Samples-MT]: https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multitenant
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-portal]: https://portal.azure.com
[MSFT-Graph-overview]: https://developer.microsoft.com/graph/docs/overview/overview
[MSFT-Graph-permission-scopes]: https://developer.microsoft.com/graph/docs/concepts/permissions_reference

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
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AAD-V2-Dev-Guide]: v2-overview.md
[AZURE-portal]: https://portal.azure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: https://msdn.microsoft.com/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Code-Grant-Flow]: https://msdn.microsoft.com/library/azure/dn645542.aspx
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3 
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken

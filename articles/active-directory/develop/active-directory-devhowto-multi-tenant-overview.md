---
title: "Como criar um aplicativo que pode conectar qualquer usuário do Azure AD"
description: "Instruções passo a passo para a criação de um aplicativo que pode conectar um usuário de qualquer locatário do Azure Active Directory, também conhecido como aplicativo multilocatário."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 35af95cb-ced3-46ad-b01d-5d2f6fd064a3
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2017
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: b409aa762b60c6bed0ee26f4b9fa7c347d9eb997
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-sign-in-any-azure-active-directory-ad-user-using-the-multi-tenant-application-pattern"></a>Como entrar em qualquer usuário do Azure AD (Active Directory) usando o padrão de aplicativo multilocatário
Se você oferecer um aplicativo Software como Serviço para muitas organizações, poderá configurar seu aplicativo para aceitar entradas de qualquer locatário do Azure AD.  No Azure AD, essa configuração é chamada de tornar seu aplicativo multilocatário.  Os usuários em qualquer locatário do Azure AD poderão entrar em seu aplicativo após o consentimento para usar sua conta com o aplicativo.  

Se você tiver um aplicativo que tem seu próprio sistema de contas ou que dá suporte a outros tipos de conexão por meio de outros provedores de nuvem, a adição da conexão do Azure AD em qualquer locatário será simples. Basta registrar o aplicativo, adicionar o código de conexão pelo OAuth2, OpenID Connect ou SAML e colocar um botão “Entrar com a Microsoft” em seu aplicativo. Clique no botão a seguir para saber mais sobre a identidade visual de seu aplicativo.

[![Botão Entrar][AAD-Sign-In]][AAD-App-Branding]

Este artigo pressupõe que você já está familiarizado com a criação de um aplicativo de locatário único do Azure AD.  Se não estiver, retorne à [home page do guia do desenvolvedor][AAD-Dev-Guide] e experimente um de nossos inícios rápidos!

Há quatro etapas simples para converter seu aplicativo em um aplicativo multilocatário do Azure AD:

1. Atualizar seu registro de aplicativo para ser multilocatário
2. Atualizar seu código para enviar solicitações para o ponto de extremidade /common 
3. Atualizar seu código para lidar com vários valores de emissor
4. Entender o consentimento do usuário e administrador e fazer as alterações de código apropriadas

Vamos examinar cada etapa detalhadamente. Você também pode ir diretamente para [esta lista de exemplos de multilocatário][AAD-Samples-MT].

## <a name="update-registration-to-be-multi-tenant"></a>Atualizar o registro para ser multilocatário
Por padrão, os registros de API/aplicativo Web no Azure AD são de locatário único.  Você pode tornar o registro multilocatário localizando a opção “Multilocatário” na página de propriedades do registro do aplicativo no [Portal do Azure][AZURE-portal] e configurando-a como “Sim”.

Observe também que antes de um aplicativo poder ser definido como multilocatário, o Azure AD requer que o URI da ID do Aplicativo seja globalmente exclusivo. O URI da ID do Aplicativo é uma das maneiras que um aplicativo é identificado em mensagens de protocolo.  Para um aplicativo de locatário único, é suficiente que o URI da ID do Aplicativo seja exclusivo nesse locatário.  Para um aplicativo multilocatário, ele deve ser globalmente exclusivo para que o Azure AD possa localizar os aplicativos em todos os locatários.  A exclusividade global é imposta exigindo o URI da ID do Aplicativo com um nome de host que corresponda a um domínio verificado do locatário do Azure AD.  Por exemplo, se o nome do seu locatário fosse contoso.onmicrosoft.com, um URI da ID do Aplicativo válido seria `https://contoso.onmicrosoft.com/myapp`.  Se seu locatário tivesse um domínio verificado de `contoso.com`, então um URI da ID do Aplicativo também seria `https://contoso.com/myapp`.  A configuração de um aplicativo como multilocatário falhará se o URI da ID do Aplicativo não seguir esse padrão.

Registros de cliente nativos são multilocatários por padrão.  Você não precisa realizar nenhuma ação para tornar um registro de aplicativo cliente nativo multilocatário.

## <a name="update-your-code-to-send-requests-to-common"></a>Atualizar seu código para enviar solicitações para /common
Em um aplicativo de locatário único, as solicitações de conexão são enviadas para o ponto de extremidade de conexão do locatário. Por exemplo, para contoso.onmicrosoft.com o ponto de extremidade seria:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

Solicitações enviadas para o ponto de extremidade de um locatário podem realizar a entrada de usuários (ou convidados) naquele locatário para aplicativos nele.  Com um aplicativo multilocatário, o aplicativo não sabe com antecedência de qual locatário o usuário é, portanto, você não pode enviar solicitações para o ponto de extremidade de um locatário.  Em vez disso, as solicitações são enviadas para um ponto de extremidade que multiplexa entre todos os locatários do Azure AD:

    https://login.microsoftonline.com/common

Quando o Azure AD recebe uma solicitação no ponto de extremidade /common, ele realiza a entrada do usuário e como consequência descobre de qual locatário o usuário é.  O ponto de extremidade /common funciona com todos os protocolos de autenticação com suporte pelo Azure AD: OpenID Connect, OAuth 2.0, SAML 2.0 e WS-Federation.

Em seguida, a resposta de conexão para o aplicativo conterá um token que representa o usuário.  O valor do emissor no token diz a um aplicativo de qual locatário o usuário é.  Quando uma resposta retorna do ponto de extremidade /common, o valor do emissor no token corresponde ao locatário do usuário.  É importante observar que o ponto de extremidade /common não é um locatário e não é um emissor, ele é apenas um multiplexador.  Ao usar /common, a lógica em seu aplicativo para validar tokens precisa ser atualizada para considerar isso. 

Como mencionado anteriormente, os aplicativos multilocatário também devem fornecer uma experiência de entrada consistente para usuários, seguindo as diretrizes de identidade visual do aplicativo do Azure AD. Clique no botão a seguir para saber mais sobre a identidade visual de seu aplicativo.

[![Botão Entrar][AAD-Sign-In]][AAD-App-Branding]

Vamos examinar o uso do ponto de extremidade /common e a sua implementação de código em mais detalhes.

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>Atualizar seu código para lidar com vários valores de emissor
Os aplicativos Web e as APIs Web recebem e validam os tokens do Azure AD.  

> [!NOTE]
> Embora os aplicativos cliente nativos solicitem e recebam tokens do Azure AD, eles fazem isso para enviá-los para APIs, nas quais eles são validados.  Os aplicativos nativos não validam os tokens e devem tratá-los como opacos.
> 
> 

Vamos examinar como um aplicativo valida os tokens que ele recebe do Azure AD.  Um aplicativo de locatário único normalmente tem um valor de ponto de extremidade como:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

e o usará para criar uma URL de metadados (nesse caso, OpenID Connect) como:

    https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration

para baixar duas partes críticas de informações que são usadas para validar tokens: as chaves de assinatura do locatário e o valor de emissor.  Cada locatário do Azure AD tem um valor de emissor exclusivo do formato:

    https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/

em que o valor de GUID é a versão à prova de renomeação da ID de locatário.  Se você clicar no link de metadados anterior para `contoso.onmicrosoft.com`, poderá ver esse valor de emissor no documento.

Quando um aplicativo de locatário único valida um token, ele verifica a assinatura do token em relação às chaves de autenticação do documento de metadados. Esse teste permite garantir que o valor do emissor no token corresponde ao que foi encontrado no documento de metadados.

Como o ponto de extremidade /common não corresponde a um locatário e não é um emissor, ao examinar o valor do emissor nos metadados para /common, ele tem uma URL de modelo em vez de um valor real:

    https://sts.windows.net/{tenantid}/

Portanto, um aplicativo multilocatário não pode validar tokens apenas combinando o valor do emissor nos metadados com o valor `issuer` no token.  Um aplicativo multilocatário precisa de lógica para decidir quais valores de emissor são válidos e quais não são com base na parte da ID do locatário do valor do emissor.  

Por exemplo, se um aplicativo multilocatário permite apenas a conexão em locatários específicos que se inscreveram para seu serviço, ele deve verificar o valor do emissor ou o valor da declaração `tid` no token para ter certeza de que o locatário está em sua lista de assinantes.  Se um aplicativo multilocatário lida apenas com pessoas e não toma nenhuma decisão de acesso com base em locatários, ele pode ignorar o valor de emissor completamente.

Nas amostras de multilocatário da seção [Conteúdo relacionado](#related-content) ao final desse artigo, a validação do emissor está desabilitada para permitir a conexão de qualquer locatário do Azure AD.

Agora vamos examinar a experiência do usuário para usuários que estão entrando em aplicativos multilocatários.

## <a name="understanding-user-and-admin-consent"></a>Entendendo o consentimento do usuário e do administrador
Para um usuário entrar em um aplicativo no Azure AD, o aplicativo deve estar representado no locatário do usuário.  Isso permite que a organização realize ações como aplicar políticas exclusivas quando usuários de seu locatário entrarem no aplicativo.  Para um aplicativo de locatário único, esse registro é simples; é aquele feito quando você registra o aplicativo no [portal do Azure][AZURE-portal].

Para um aplicativo multilocatário, o registro inicial para o aplicativo reside no locatário do Azure AD usado pelo desenvolvedor.  Quando um usuário de um locatário diferente entra no aplicativo pela primeira vez, o Azure AD solicita que ele consinta com as permissões solicitadas pelo aplicativo.  Se ele fornecer o consentimento, uma representação do aplicativo chamada uma *entidade de serviço* será criada no locatário do usuário e o processo de conexão poderá continuar. Uma delegação também é criada no diretório que registra o consentimento do usuário para o aplicativo. Consulte [Objetos de aplicativo e de entidade de serviço][AAD-App-SP-Objects] para obter detalhes sobre objetos Application e ServicePrincipal do aplicativo e como eles se relacionam entre si.

![Consentimento ao aplicativo de camada única][Consent-Single-Tier] 

Essa experiência de consentimento é afetada pelas permissões solicitadas pelo aplicativo.  O Azure AD dá suporte a dois tipos de permissões, somente do aplicativo e delegadas:

* Uma permissão delegada concede a um aplicativo a capacidade de atuar como um usuário conectado para um subconjunto das ações que o usuário pode realizar.  Por exemplo, você pode conceder a um aplicativo a permissão delegada para ler o calendário do usuário conectado.
* Uma permissão somente do aplicativo é concedida diretamente à identidade do aplicativo.  Por exemplo, você pode conceder a permissão somente do aplicativo a um aplicativo para ler a lista de usuários em um locatário, independentemente de quem estiver conectado ao aplicativo.

Algumas permissões podem ser consentidas por um usuário normal, enquanto outras exigem o consentimento de um administrador de locatários. 

### <a name="admin-consent"></a>Consentimento do administrador
As permissões somente do aplicativo sempre exigem o consentimento do administrador de locatários.  Se o aplicativo solicitar uma permissão somente do aplicativo e um usuário tentar entrar no aplicativo, uma mensagem de erro será exibida informando que o usuário não pode fornecer o consentimento.

Algumas permissões delegadas também exigem o consentimento do administrador de locatários.  Por exemplo, a capacidade de gravar no Azure AD como o usuário conectado requer o consentimento de um administrador de locatários.  Semelhante às permissões somente do aplicativo, se um usuário comum tenta entrar em um aplicativo que solicita uma permissão delegada que exige o consentimento do administrador, seu aplicativo recebe um erro.  Se uma permissão exige ou não o consentimento do administrador é determinado pelo desenvolvedor que publicou o recurso e pode ser encontrado na documentação do recurso.  Os links para tópicos que descrevem as permissões disponíveis para a API do Azure AD Graph e a API do Microsoft Graph estão na seção [Conteúdo relacionado](#related-content) desse artigo.

Se o aplicativo usar permissões que exigem o consentimento do administrador, você precisará ter um gesto como um botão ou link, em que o administrador pode iniciar a ação.  A solicitação que seu aplicativo envia para essa ação é uma solicitação de autorização do OAuth2/OpenID Connect normal, mas ela também inclui o parâmetro de cadeia de caracteres de consulta `prompt=admin_consent` .  Depois que o administrador fornecer seu consentimento e a entidade de serviço for criada no locatário do cliente, as próximas solicitações de conexão não precisarão do parâmetro `prompt=admin_consent`. Uma vez que o administrador tiver decidido que as permissões solicitadas forem aceitáveis, não será solicitado o consentimento de nenhum outro usuário no locatário daquele ponto em diante.

O parâmetro `prompt=admin_consent` também pode ser usado por aplicativos que exigem permissões que não necessitam do consentimento do administrador. Isso é feito quando o aplicativo exige uma experiência na qual o administrador de locatários “se inscreve” uma vez e não é solicitado o consentimento de nenhum outro usuário desse ponto em diante.

Se um aplicativo exigir o consentimento do administrador e um administrador fizer logon, mas o parâmetro `prompt=admin_consent` não for enviado, o administrador fornecerá consentimento ao aplicativo **somente para a conta de usuário dele**.  Os usuários normais ainda não poderão fazer entrar e dar seu consentimento ao aplicativo.  Esse recurso é útil se você quiser conceder ao administrador de locatários a capacidade de explorar seu aplicativo antes de permitir o acesso de outros usuários.

Um administrador de locatários pode desabilitar a capacidade dos usuários regulares consentirem aplicativos.  Se essa funcionalidade estiver desabilitada, o consentimento do administrador sempre será necessário para o aplicativo a ser configurado no locatário.  Se você quiser testar seu aplicativo com o consentimento do usuário normal desabilitado, encontrará a opção de configuração na seção de configuração do locatário do Azure AD do [portal do Azure][AZURE-portal].

> [!NOTE]
> Alguns aplicativos desejam uma experiência em que os usuários normais podem consentir inicialmente e posteriormente o aplicativo pode envolver o administrador e solicitar permissões que exigem o consentimento do administrador.  Não é possível fazer isso com um registro de aplicativo único no Azure AD atualmente.  O futuro ponto de extremidade do modelo de implantação do Azure AD Resource Manager permitirá que os aplicativos solicitem permissões no tempo de execução em vez de no momento do registro, o que possibilitará esse cenário.  Para saber mais, consulte o [Guia do desenvolvedor do modelo de implantação do Resource Manager do Modelo de Aplicativo Azure AD][AAD-V2-Dev-Guide].
> 
> 

### <a name="consent-and-multi-tier-applications"></a>Aplicativos de várias camadas e consentimento
Seu aplicativo pode ter várias camadas, cada uma representada por seu próprio registro no Azure AD.  Por exemplo, um aplicativo nativo que chama uma API Web ou um aplicativo Web que chama uma API Web.  Em ambos os casos, o cliente (aplicativo nativo ou aplicativo Web) solicita permissões para chamar o recurso (API Web).  Para o cliente ter o consentimento com êxito em um locatário do cliente, todos os recursos aos quais ele solicita permissões já devem existir no locatário do cliente.  Se essa condição não for atendida, o Azure AD retornará um erro de que o recurso deve ser adicionado primeiro.

**Várias camadas em um único locatário**

Isso poderá ser um problema se seu aplicativo lógico consistir em dois ou mais registros de aplicativo, por exemplo, um cliente e um recurso separados.  Como você obtém o recurso no locatário do cliente primeiro?  O Azure AD abrange neste caso permitindo que o cliente e o recurso recebam o consentimento em uma única etapa. O usuário vê a soma total das permissões solicitadas pelo cliente e pelo recurso na página de consentimento.  Para permitir esse comportamento, o registro do aplicativo do recurso deve incluir a ID do aplicativo do cliente como um `knownClientApplications` no manifesto do aplicativo.  Por exemplo:

    knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]

Essa propriedade pode ser atualizada por meio do [manifesto do aplicativo][AAD-App-Manifest] do recurso. Isso é demonstrado em uma amostra de chamada de cliente nativo de várias à API Web na seção [Conteúdo relacionado](#related-content) ao final deste artigo. O diagrama a seguir fornece uma visão geral de consentimento para um aplicativo de várias camadas registrado em um único locatário:

![Consentimento ao aplicativo cliente conhecido de várias camadas][Consent-Multi-Tier-Known-Client] 

**Várias camadas em vários locatários**

Um caso semelhante acontecerá se as diferentes camadas de um aplicativo forem registradas em locatários diferentes.  Por exemplo, considere o caso da criação de um aplicativo cliente nativo que chama a API do Office 365 Exchange Online.  Para desenvolver o aplicativo e posteriormente para o aplicativo nativo ser executado no locatário de um cliente, a entidade de serviço do Exchange Online deve estar presente.  Nesse caso, o desenvolvedor e o cliente devem adquirir o Exchange Online para que a entidade de serviço seja criada em seus locatários.  

No caso de uma API criada por uma organização que não seja a Microsoft, o desenvolvedor da API precisa fornecer uma maneira para seus clientes fornecerem consentimento para o aplicativo nos locatários de seus clientes. O design recomendado é que o desenvolvedor de terceiros crie a API, de forma que ele também possa funcionar como um cliente Web para implementar a inscrição:

1. Siga as seções anteriores para garantir que a API implementa os requisitos de código/registro do aplicativo multilocatário
2. Além de expor os escopos e as funções da API, garanta que o registro inclui a permissão “Entrar e ler o perfil de usuário” do Azure AD (fornecido por padrão)
3. Implemente uma página de conexão/inscrição no cliente Web seguindo as diretrizes de [consentimento do administrador](#admin-consent) abordadas anteriormente 
4. Depois que o usuário fornecer consentimento para o aplicativo, a entidade de serviço e os links de delegação de consentimento serão criados em seu locatário e o aplicativo nativo poderá obter tokens para a API

O seguinte diagrama fornece uma visão geral de consentimento para um aplicativo de várias camadas registrado em diferentes locatários:

![Consentimento ao aplicativo de vários fornecedores de várias camadas][Consent-Multi-Tier-Multi-Party] 

### <a name="revoking-consent"></a>Revogando o consentimento
Os usuários e administradores podem revogar o consentimento para seu aplicativo a qualquer momento:

* Os usuários revogam o acesso a aplicativos individuais removendo-os da lista de [Aplicativos do Painel de Acesso][AAD-Access-Panel].
* Os administradores revogam o acesso a aplicativos removendo-os do Azure AD usando a seção de gerenciamento do Azure AD do [portal do Azure][AZURE-portal].

Se um administrador der o consentimento a um aplicativo para todos os usuários em um locatário, os usuários não poderão revogar o acesso individualmente.  Somente o administrador pode revogar o acesso e somente para o aplicativo inteiro.

### <a name="consent-and-protocol-support"></a>Suporte a protocolo e consentimento
O consentimento tem suporte no Azure AD por meio dos protocolos OAuth, OpenID Connect, Web Services Federation e SAML.  Os protocolos SAML e WS-Federation não dão suporte ao parâmetro `prompt=admin_consent` , portanto, o consentimento do administrador é possível apenas por meio do OAuth e do OpenID Connect.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Aplicativos multilocatários e caching de tokens de acesso
Os aplicativos multilocatários também podem obter tokens de acesso para chamar APIs que são protegidas pelo Azure AD.  Um erro comum ao usar a ADAL (Biblioteca de Autenticação do Active Directory) com um aplicativo multilocatário é solicitar inicialmente um token para um usuário usando /common, receber uma resposta e, depois, solicitar um próximo token para o mesmo usuário também usando /common.  Uma vez que a resposta do Azure AD vem de um locatário, não de /common, a ADAL armazena em cache o token como sendo do locatário. A chamada subsequente para /common para obter um token de acesso para o usuário perde a entrada de cache e o usuário é solicitado a entrar novamente.  Para evitar a perda de cache, certifique-se de que as chamadas subsequentes para um usuário já conectado sejam feitas para o ponto de extremidade do locatário.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a criar um aplicativo que pode conectar um usuário por meio de qualquer locatário do Azure Active Directory. Depois de habilitar o Logon Único entre o aplicativo e o Azure Active Directory, você também pode atualizar o aplicativo para acessar as APIs expostas por recursos da Microsoft, como o Office 365. Portanto, é possível oferecer uma experiência personalizada no aplicativo, por exemplo, mostrando informações contextuais para os usuários, como suas imagens de perfil ou seus próximos compromissos no calendário. Para saber mais sobre como fazer chamadas à API para serviços do Azure Active Directory e do Office 365, como o Exchange, SharePoint, OneDrive, OneNote, Planner, Excel e muitos outros, acesse [API do Microsoft Graph][MSFT-Graph-overview].


## <a name="related-content"></a>Conteúdo relacionado
* [Exemplos de aplicativos multilocatários][AAD-Samples-MT]
* [Diretrizes de identidade visual para aplicativos][AAD-App-Branding]
* [Guia do Desenvolvedor do Azure AD][AAD-Dev-Guide]
* [Objetos de Aplicativo e de Entidade de Serviço][AAD-App-SP-Objects]
* [Integrando aplicativos com o Azure Active Directory][AAD-Integrating-Apps]
* [Visão geral da estrutura de consentimento][AAD-Consent-Overview]
* [Escopos de permissão da API do Microsoft Graph][MSFT-Graph-permision-scopes]
* [Escopos de permissão da API do Graph do Azure AD][AAD-Graph-Perm-Scopes]

Use a seção de comentários a seguir para fornecer seus comentários e nos ajudar a aprimorar e adaptar nosso conteúdo.

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]: ./active-directory-branding-guidelines.md
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Consent-Overview]: ./active-directory-integrating-applications.md#overview-of-the-consent-framework
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Overview]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-graph-api/
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Samples-MT]: https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multitenant
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-portal]: https://portal.azure.com
[MSFT-Graph-overview]: https://graph.microsoft.io/en-us/docs/overview/overview
[MSFT-Graph-permision-scopes]: https://graph.microsoft.io/en-us/docs/authorization/permission_scopes

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-single-tier.png
[Consent-Multi-Tier-Known-Client]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-known-clients.png
[Consent-Multi-Tier-Multi-Party]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-multi-party.png

<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
[AAD-V2-Dev-Guide]: ../active-directory-appmodel-v2-overview.md
[AZURE-portal]: https://portal.azure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: https://msdn.microsoft.com/en-us/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Code-Grant-Flow]: https://msdn.microsoft.com/library/azure/dn645542.aspx
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3 
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken















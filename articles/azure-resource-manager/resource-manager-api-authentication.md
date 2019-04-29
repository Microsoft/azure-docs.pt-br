---
title: Autenticação do Azure Active Directory e Resource Manager | Microsoft Docs
description: Guia do desenvolvedor para autenticação com a API do Azure Resource Manager e o Azure Active Directory para integrar um aplicativo a outras assinaturas do Azure.
services: azure-resource-manager,active-directory
documentationcenter: na
author: dushyantgill
ms.assetid: 17b2b40d-bf42-4c7d-9a88-9938409c5088
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.author: dugill
ms.openlocfilehash: ae405d5dd99a0e2acced924ccccab292b4489cde
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61063197"
---
# <a name="use-resource-manager-authentication-api-to-access-subscriptions"></a>Usar API de autenticação do Resource Manager para acessar assinaturas

Se você for um desenvolvedor de software que precisa para criar um aplicativo que gerencia os recursos do Azure de um cliente, este artigo mostra como autenticar com as APIs do Gerenciador de recursos do Azure e obter acesso aos recursos em outras assinaturas.

Seu aplicativo pode acessar as APIs do Gerenciador de Recursos de duas maneiras:

1. **Acesso de aplicativo + usuário**: para aplicativos que acessam os recursos para um usuário conectado. Essa abordagem funciona para aplicativos, como aplicativos Web e ferramentas de linha de comando, que lidam apenas com "gerenciamento interativo" dos recursos do Azure.
2. **Acesso somente a aplicativos**: para aplicativos que executam serviços daemon e trabalhos agendados. A identidade do aplicativo recebe acesso direto aos recursos. Essa abordagem funciona para aplicativos que precisam de acesso de administração remota (autônomo) de longo prazo ao Azure.

Este artigo fornece instruções passo a passo para criar um aplicativo que utiliza ambos os métodos de autorização. Ele mostra como executar cada etapa com a API REST ou C#. O aplicativo ASP.NET MVC completo está disponível em [https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense](https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense).

## <a name="what-the-web-app-does"></a>O que o aplicativo Web faz

O aplicativo Web:

1. Conecta um usuário do Azure.
2. Solicita que o usuário conceda ao Resource Manager acesso ao aplicativo Web.
3. Obtém o token de acesso de usuário + aplicativo acessando o Resource Manager.
4. Usa o token (da etapa 3) para atribuir a entidade de serviço do aplicativo a uma função na assinatura. Essa etapa fornece ao aplicativo o acesso de longo prazo à assinatura.
5. Obtém o token de acesso somente ao aplicativo.
6. Usa o token (obtido na etapa 5) para gerenciar recursos na assinatura por meio do Resource Manager.

Veja a seguir o fluxo do aplicativo Web.

![Fluxo de autenticação do Resource Manager](./media/resource-manager-api-authentication/Auth-Swim-Lane.png)

Como usuário, você fornece a ID da assinatura que deseja usar:

![fornecer ID da assinatura](./media/resource-manager-api-authentication/sample-ux-1.png)

Escolha a conta a ser usada para fazer logon.

![escolher conta](./media/resource-manager-api-authentication/sample-ux-2.png)

Forneça suas credenciais.

![fornecer credenciais](./media/resource-manager-api-authentication/sample-ux-3.png)

Conceda às suas assinaturas do Azure acesso ao aplicativo:

![Conceder acesso](./media/resource-manager-api-authentication/sample-ux-4.png)

Gerencie as assinaturas conectadas:

![Conectar assinatura](./media/resource-manager-api-authentication/sample-ux-7.png)

## <a name="register-application"></a>Registrar aplicativo
Antes de iniciar a codificação, registre o aplicativo Web com o Azure AD (Active Directory). O registro do aplicativo cria uma identidade central para seu aplicativo no Azure AD. Ele contém informações básicas sobre seu aplicativo, como ID do Cliente OAuth, URLs de Resposta e credenciais que o aplicativo usa para autenticar e acessar as APIs do Azure Resource Manager. O registro do aplicativo também registra as várias permissões delegadas que seu aplicativo precisa para acessar APIs da Microsoft para o usuário.

Para registrar seu aplicativo, consulte [guia de início rápido: Registrar um aplicativo com a plataforma de identidade Microsoft](../active-directory/develop/quickstart-register-app.md). Dê um nome de seu aplicativo e, em seguida, selecione **contas em qualquer diretório organizacional** para os tipos de conta com suporte. Para a URL de redirecionamento, forneça um domínio associado com o Azure Active Directory.

Para entrar como o aplicativo do AD, é necessário a ID do aplicativo e um segredo. A ID do aplicativo é exibida na visão geral do aplicativo. Para criar um segredo e solicitar permissões de API, consulte [guia de início rápido: Configurar um aplicativo cliente para acessar APIs web](../active-directory/develop/quickstart-configure-app-access-web-apis.md). Forneça um novo segredo do cliente. Para permissões de API, selecione **gerenciamento de serviços do Azure**. Selecione **permissões delegadas** e **user_impersonation**.

### <a name="optional-configuration---certificate-credential"></a>Configuração opcional - credenciais de certificado
O Azure AD também dá suporte a credenciais de certificado para aplicativos: crie um certificado autoassinado, mantenha a chave privada e adicione a chave pública a seu registro de aplicativo do Azure AD. Para autenticação, seu aplicativo envia uma pequena carga ao Azure AD assinada usando sua chave privada e o Azure AD valida a assinatura usando a chave pública que você registrou.

Para saber mais sobre como criar um aplicativo do AD com um certificado, confira [Use Azure PowerShell to create a service principal to access resources](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority) (Usar o Azure PowerShell para criar uma entidade de serviço a fim de acessar recursos) ou [Use Azure CLI to create a service principal to access resources](resource-group-authenticate-service-principal-cli.md) (Usar a CLI do Azure para criar uma entidade de serviço a fim de acessar recursos).

## <a name="get-tenant-id-from-subscription-id"></a>Obter a ID do locatário da ID da assinatura
Para solicitar um token que possa ser usado para chamar o Resource Manager, seu aplicativo precisa saber a ID do locatário do Azure AD que hospeda a assinatura do Azure. É bem provável que os usuários saibam suas IDs de assinatura, mas talvez não saibam suas IDs de locatário do Azure Active Directory. Para obter a ID do locatário do usuário, selecione ao usuário a ID da assinatura. Forneça essa ID da assinatura ao enviar uma solicitação sobre a assinatura:

    https://management.azure.com/subscriptions/{subscription-id}?api-version=2015-01-01

A solicitação falha porque o usuário não fez ainda, mas você pode recuperar a ID de locatário na resposta. Nessa exceção, recupere a ID do locatário no valor do cabeçalho de resposta de **WWW-Authenticate**. Você vê essa implementação no método [GetDirectoryForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L20) .

## <a name="get-user--app-access-token"></a>Obter token de acesso ao aplicativo + usuário
Seu aplicativo redireciona o usuário para o Azure AD com uma Solicitação de Autorização OAuth 2.0 a fim de autenticar as credenciais do usuário e obter um código de autorização. O aplicativo usa o código de autorização a fim de obter um token de acesso para o Resource Manager. O método [ConnectSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/Controllers/HomeController.cs#L42) cria a solicitação de autorização.

Este artigo mostra as solicitações da API REST para autenticar o usuário. Você também pode usar bibliotecas auxiliares para autenticar em seu código. Para saber mais sobre essas bibliotecas, confira [Bibliotecas de autenticação do Azure Active Directory](../active-directory/active-directory-authentication-libraries.md). Para obter instruções sobre como integrar o gerenciamento de identidades em um aplicativo, confira [Guia do desenvolvedor do Azure Active Directory](../active-directory/develop/v1-overview.md).

### <a name="auth-request-oauth-20"></a>Solicitação de autorização (OAuth 2.0)
Emita uma solicitação de autorização Open ID Connect/OAuth 2.0 para o ponto de extremidade de autorização do Azure AD:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize

Os parâmetros de cadeia de consulta disponíveis para essa solicitação são descritos no artigo [Solicitar um código de autorização](../active-directory/develop/v1-protocols-oauth-code.md#request-an-authorization-code).

O exemplo abaixo mostra como solicitar autorização OAuth 2.0:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=query&response_type=code&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&domain_hint=live.com

O Azure AD autentica o usuário e, se necessário, pede a ele para conceder permissão ao aplicativo. Ele retorna o código de autorização para a URL de resposta do seu aplicativo. Dependendo do response_mode solicitado, o Azure AD envia de volta os dados na cadeia de caracteres de consulta ou como dados de postagem.

    code=AAABAAAAiL****FDMZBUwZ8eCAA&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="auth-request-open-id-connect"></a>Solicitação de autorização (Open ID Connect)
Se você não só deseja acessar o Azure Resource Manager para o usuário, mas também permitem que o usuário fazer logon seu aplicativo usando sua conta do Azure AD, emita uma solicitação Open ID Connect autorizar. Com o Open ID Connect, seu aplicativo também recebe um id_token do Azure AD que pode ser usado para conectar o usuário.

Os parâmetros de cadeia de consulta disponíveis para essa solicitação são descritos no artigo [Enviar a solicitação de conexão](../active-directory/develop/v1-protocols-openid-connect-code.md#send-the-sign-in-request).

Eis um exemplo de solicitação Open ID Connect:

     https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=form_post&response_type=code+id_token&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&scope=openid+profile&nonce=63567Dc4MDAw&domain_hint=live.com&state=M_12tMyKaM8

O Azure AD autentica o usuário e, se necessário, pede a ele para conceder permissão ao aplicativo. Ele retorna o código de autorização para a URL de resposta do seu aplicativo. Dependendo do response_mode solicitado, o Azure AD envia de volta os dados na cadeia de caracteres de consulta ou como dados de postagem.

Eis um exemplo de resposta Open ID Connect:

    code=AAABAAAAiL*****I4rDWd7zXsH6WUjlkIEQxIAA&id_token=eyJ0eXAiOiJKV1Q*****T3GrzzSFxg&state=M_12tMyKaM8&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="token-request-oauth20-code-grant-flow"></a>Solicitação de token (Fluxo de concessão de código OAuth 2.0)
Agora que seu aplicativo recebeu o código de autorização do Azure AD, é hora de obter o token de acesso do Azure Resource Manager.  Poste uma solicitação de token de concessão de código OAuth 2.0 no ponto de extremidade de token do Azure AD:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Os parâmetros de cadeia de consulta disponíveis para essa solicitação são descritos no artigo [Usar o código de autorização](../active-directory/develop/v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

O exemplo abaixo mostra uma solicitação de token de concessão de código com credenciais de senha:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Ao trabalhar com as credenciais de certificado, crie um JWT (Token Web JSON) e assine (RSA SHA256) usando a chave particular de credenciais de certificado do seu aplicativo. A criação desse token é mostrada no [fluxo de credenciais do cliente](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#second-case-access-token-request-with a-certificate).  Para referência, confira o [Código da biblioteca de autenticação do Active Directory (.NET)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/dev/src/ADAL.PCL.Desktop/CryptographyHelper.cs) para assinar tokens JWT de asserção de cliente.

Confira as [especificações do Open ID Connect](https://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication) para obter detalhes sobre a autenticação de cliente.

O exemplo abaixo mostra uma solicitação de token de concessão de código com credenciais de certificado:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbG*****Y9cYo8nEjMyA

Um exemplo de resposta de token de concessão de código:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039858","not_before":"1432035958","resource":"https://management.core.windows.net/","access_token":"eyJ0eXAiOiJKV1Q****M7Cw6JWtfY2lGc5A","refresh_token":"AAABAAAAiL9Kn2Z****55j-sjnyYgAA","scope":"user_impersonation","id_token":"eyJ0eXAiOiJKV*****-drP1J3P-HnHi9Rr46kGZnukEBH4dsg"}

#### <a name="handle-code-grant-token-response"></a>Manipular resposta de token de concessão de código
Uma resposta bem-sucedida de token contém o token de acesso (usuário e aplicativo) para o Azure Resource Manager. Seu aplicativo usa esse token de acesso para acessar o Resource Manager para o usuário. A vida útil dos tokens de acesso emitidos pelo Azure AD é de uma hora. É improvável que seu aplicativo web precisa renovar (usuário + aplicativo) token de acesso. Se ele precisa renovar o token de acesso, use o token de atualização que o aplicativo recebe na resposta de token. Poste uma solicitação de token OAuth 2.0 no ponto de extremidade de token do Azure AD:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Os parâmetros a serem usados com a solicitação de atualização são descrito em [Atualização dos tokens de acesso](../active-directory/develop/v1-protocols-oauth-code.md#refreshing-the-access-tokens).

O exemplo abaixo mostra como usar o token de atualização:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=refresh_token&refresh_token=AAABAAAAiL9Kn2Z****55j-sjnyYgAA&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Embora os tokens de atualização podem ser usados para obter novos tokens de acesso para o Azure Resource Manager, eles não são adequados para o acesso offline ao seu aplicativo. O tempo de vida dos tokens de atualização é limitado e os tokens de atualização estão associados ao usuário. Se o usuário sair da organização, o aplicativo que usa o token de atualização perderá o acesso. Essa abordagem não é adequada para aplicativos que são usados pelas equipes para gerenciar seus recursos do Azure.

## <a name="check-if-user-can-assign-access-to-subscription"></a>Verificar se o usuário pode atribuir acesso à assinatura
Seu aplicativo agora tem um token para acessar o Azure Resource Manager para o usuário. A próxima etapa é conectar o aplicativo à assinatura. Depois de se conectar, o aplicativo pode gerenciar as assinaturas mesmo quando o usuário não estiver presente (acesso offline de longo prazo).

Para conectar cada assinatura, chame a API das [permissões da lista do Resource Manager](https://docs.microsoft.com/rest/api/authorization/permissions) para determinar se o usuário tem direitos de gerenciamento de acesso para a assinatura.

O método [UserCanManagerAccessForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L44) do aplicativo de exemplo do ASP.NET MVC implementa essa chamada.

O exemplo a seguir mostra como solicitar permissões de um usuário em uma assinatura. 83cfe939-2402-4581-b761-4f59b0a041e4 é a ID da assinatura.

    GET https://management.azure.com/subscriptions/83cfe939-2402-4581-b761-4f59b0a041e4/providers/microsoft.authorization/permissions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

Veja um exemplo de resposta para obter permissões do usuário na assinatura:

    HTTP/1.1 200 OK

    {"value":[{"actions":["*"],"notActions":["Microsoft.Authorization/*/Write","Microsoft.Authorization/*/Delete"]},{"actions":["*/read"],"notActions":[]}]}

A API de permissões retorna várias permissões. Cada permissão consiste em ações permitidas (**actions**) e ações não permitidas (**notactions**). Se uma ação estiver presente em ações permitidas de qualquer permissão e não está presente nas ações não permitidas dessa permissão, o usuário tem permissão para fazer essa ação. **microsoft.authorization/roleassignments/write** é a ação que concede direitos de gerenciamento de acesso. Seu aplicativo deve analisar o resultado de permissões e procurar uma correspondência de regex nessa cadeia de caracteres de ação em **actions** e **notactions** de cada permissão.

## <a name="get-app-only-access-token"></a>Obter token de acesso somente ao aplicativo
Agora, você sabe se o usuário pode atribuir acesso à assinatura do Azure. As próximas etapas são:

1. Atribuir a função RBAC apropriada à identidade do aplicativo na assinatura.
2. Validar a atribuição de acesso consultando a permissão do aplicativo na assinatura ou acessando o Resource Manager usando o token somente de aplicativo.
3. Registrar a conexão na estrutura de dados de “assinaturas conectadas” dos aplicativos, persistindo a ID da assinatura.

Vamos examinar mais detalhadamente a primeira etapa. Para atribuir a função RBAC apropriada à identidade do aplicativo, você deve determinar:

* A ID de objeto da identidade do aplicativo no Azure Active Directory do usuário
* O identificador da função RBAC que seu aplicativo requer na assinatura

Quando o aplicativo autentica um usuário em um Azure AD, ele cria um objeto de entidade de serviço para o aplicativo nesse Azure AD. O Azure permite que as funções RBAC sejam atribuídas a entidades de serviço para conceder acesso direto a aplicativos correspondentes nos recursos do Azure. Essa ação é exatamente o que você deseja fazer. Consulte a API do Azure AD Graph para determinar o identificador da entidade de serviço do seu aplicativo no Azure AD do usuário conectado.

Você tem apenas um token de acesso para o Azure Resource Manager. É necessário um novo token de acesso para chamar a API do Azure AD Graph. Cada aplicativo no Azure AD tem permissão para consultar seu próprio objeto de entidade de serviço, de modo que um token de acesso somente de aplicativo é suficiente.

<a id="app-azure-ad-graph" />

### <a name="get-app-only-access-token-for-azure-ad-graph-api"></a>Obter o token de acesso somente de aplicativo para a API do Azure AD Graph

Para autenticar seu aplicativo e obter um token para a API do Graph do Azure AD, emita uma solicitação de token de fluxo do OAuth 2.0 de concessão de credencial de cliente para ponto de extremidade de token do AD do Azure (**https:\//login.microsoftonline.com/{directory_domain_name}/OAuth2/Token** ).

O método [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs) do aplicativo de exemplo do ASP.NET MVC obtém um token de acesso somente de aplicativo para API do Graph usando a Biblioteca de Autenticação do Active Directory para .NET.

Os parâmetros de cadeia de consulta disponíveis para essa solicitação são descritos no artigo [Solicitar um token de acesso](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#request-an-access-token).

Um exemplo de solicitação de token de concessão de credencial de cliente:

    POST https://login.microsoftonline.com/62e173e9-301e-423e-bcd4-29121ec1aa24/oauth2/token HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 187</pre>
    <pre>grant_type=client_credentials&client_id=a0448380-c346-4f9f-b897-c18733de9394&resource=https%3A%2F%2Fgraph.windows.net%2F &client_secret=olna8C*****Og%3D

Um exemplo de resposta de token de concessão de credencial de cliente:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039862","not_before":"1432035962","resource":"https://graph.windows.net/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRv****G5gUTV-kKorR-pg"}

### <a name="get-objectid-of-application-service-principal-in-user-azure-ad"></a>Obter a ID de objeto da entidade de serviço de aplicativo no Azure AD do usuário
Agora, use o token de acesso somente de aplicativo para consultar a API [Entidades de Serviço do Azure AD Graph](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity) para determinar a ID de Objeto da entidade de serviço do aplicativo no diretório.

O método [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#) do aplicativo de exemplo do ASP.NET MVC implementa essa chamada.

O exemplo a seguir mostra como solicitar a entidade de serviço do aplicativo. a0448380-c346-4f9f-b897-c18733de9394 é a ID do cliente do aplicativo.

    GET https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/servicePrincipals?api-version=1.5&$filter=appId%20eq%20'a0448380-c346-4f9f-b897-c18733de9394' HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJK*****-kKorR-pg

O exemplo a seguir mostra uma resposta à solicitação da entidade de serviço do aplicativo

    HTTP/1.1 200 OK

    {"odata.metadata":"https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/$metadata#directoryObjects/Microsoft.DirectoryServices.ServicePrincipal","value":[{"odata.type":"Microsoft.DirectoryServices.ServicePrincipal","objectType":"ServicePrincipal","objectId":"9b5018d4-6951-42ed-8a92-f11ec283ccec","deletionTimestamp":null,"accountEnabled":true,"appDisplayName":"CloudSense","appId":"a0448380-c346-4f9f-b897-c18733de9394","appOwnerTenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24","appRoleAssignmentRequired":false,"appRoles":[],"displayName":"CloudSense","errorUrl":null,"homepage":"http://www.vipswapper.com/cloudsense","keyCredentials":[],"logoutUrl":null,"oauth2Permissions":[{"adminConsentDescription":"Allow the application to access CloudSense on behalf of the signed-in user.","adminConsentDisplayName":"Access CloudSense","id":"b7b7338e-683a-4796-b95e-60c10380de1c","isEnabled":true,"type":"User","userConsentDescription":"Allow the application to access CloudSense on your behalf.","userConsentDisplayName":"Access CloudSense","value":"user_impersonation"}],"passwordCredentials":[],"preferredTokenSigningKeyThumbprint":null,"publisherName":"vipswapper"quot;,"replyUrls":["http://www.vipswapper.com/cloudsense","http://www.vipswapper.com","http://vipswapper.com","http://vipswapper.azurewebsites.net","http://localhost:62080"],"samlMetadataUrl":null,"servicePrincipalNames":["http://www.vipswapper.com/cloudsense","a0448380-c346-4f9f-b897-c18733de9394"],"tags":["WindowsAzureActiveDirectoryIntegratedApp"]}]}

### <a name="get-azure-rbac-role-identifier"></a>Obter o identificador de função RBAC do Azure
Para atribuir a função RBAC apropriada à entidade de serviço, é preciso determinar o identificador da função RBAC do Azure.

A função RBAC certa para seu aplicativo:

* Se o aplicativo monitora apenas a assinatura, sem fazer alterações, ele exigirá apenas permissões de leitura na assinatura. Atribua a função **Leitor** .
* Se o aplicativo gerencia a assinatura do Azure, criando/modificando/excluindo entidades, será necessária uma das permissões de colaborador.
  * Para gerenciar um determinado tipo de recurso, atribua as funções de colaborador específicas do recurso (Colaborador da Máquina Virtual, Colaborador de Rede Virtual, Colaborador da Conta de Armazenamento, etc.)
  * Para gerenciar qualquer tipo de recurso, atribua a função **Colaborador** .

A atribuição de função para seu aplicativo está visível para os usuários. Portanto, escolha o privilégio mínimo exigido.

Chame o [definição de função do Resource Manager API](https://docs.microsoft.com/rest/api/authorization/roledefinitions) para listar todas as funções de RBAC do Azure e, em seguida, iterar sobre o resultado para encontrar a definição de função pelo nome.

O método [GetRoleId](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L246) do aplicativo de exemplo do ASP.NET MVC implementa essa chamada.

O exemplo de solicitação a seguir mostra como obter o identificador de função RBAC do Azure. 09cbd307-aa71-4aca-b346-5f253e6e3ebb é a ID da assinatura.

    GET https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV*****fY2lGc5

A resposta está no seguinte formato:

    HTTP/1.1 200 OK

    {"value":[{"properties":{"roleName":"API Management Service Contributor","type":"BuiltInRole","description":"Lets you manage API Management services, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.ApiManagement/Services/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c","type":"Microsoft.Authorization/roleDefinitions","name":"312a565d-c81f-4fd8-895a-4e21e48d571c"},{"properties":{"roleName":"Application Insights Component Contributor","type":"BuiltInRole","description":"Lets you manage Application Insights components, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.Insights/components/*","Microsoft.Insights/webtests/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e","type":"Microsoft.Authorization/roleDefinitions","name":"ae349356-3a1b-4a5e-921d-050484c6347e"}]}

Você não precisa chamar essa API em uma base contínua. Depois de determinar o GUID conhecido da definição da função, você poderá construir a ID de definição de função como:

    /subscriptions/{subscription_id}/providers/Microsoft.Authorization/roleDefinitions/{well-known-role-guid}

Estes são os identificadores de funções internas geralmente usadas:

| Função | GUID |
| --- | --- |
| Leitor |acdd72a7-3385-48ef-bd42-f606fba81ae7 |
| Colaborador |b24988ac-6180-42a0-ab88-20f7382dd24c |
| Colaborador de Máquina Virtual |d73bb868-a0df-4d4d-bd69-98a00b01fccb |
| Colaborador de Rede Virtual |b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
| Colaborador da Conta de Armazenamento |86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
| Colaborador do Site |de139f84-1756-47ae-9be6-808fbbe84772 |
| Colaborador do Plano de Web |2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
| Colaborador do SQL Server |6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
| Colaborador do banco de dados SQL |9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |

### <a name="assign-rbac-role-to-application"></a>Atribuir função RBAC ao aplicativo
Você tem tudo o que é necessário para atribuir a função RBAC apropriada à entidade de serviço usando a API [Criar atribuição de função do Resource Manager](https://docs.microsoft.com/rest/api/authorization/roleassignments) .

O método [GrantRoleToServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L170) do aplicativo de exemplo ASP.NET MVC implementa essa chamada.

Um exemplo de solicitação para atribuir função RBAC ao aplicativo:

    PUT https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/microsoft.authorization/roleassignments/4f87261d-2816-465d-8311-70a27558df4c?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiL*****FlwO1mM7Cw6JWtfY2lGc5
    Content-Type: application/json
    Content-Length: 230

    {"properties": {"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2"}}

Na solicitação, são usados os seguintes valores:

| Guid | DESCRIÇÃO |
| --- | --- |
| 09cbd307-aa71-4aca-b346-5f253e6e3ebb |a ID da assinatura |
| c3097b31-7309-4c59-b4e3-770f8406bad2 |a ID de objeto da entidade de serviço do aplicativo |
| b24988ac-6180-42a0-ab88-20f7382dd24c |a ID da função de Colaborador |
| 4f87261d-2816-465d-8311-70a27558df4c |um novo GUID criado para a nova atribuição de função |

A resposta está no seguinte formato:

    HTTP/1.1 201 Created

    {"properties":{"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2","scope":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb"},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleAssignments/4f87261d-2816-465d-8311-70a27558df4c","type":"Microsoft.Authorization/roleAssignments","name":"4f87261d-2816-465d-8311-70a27558df4c"}

### <a name="get-app-only-access-token-for-azure-resource-manager"></a>Obter token de acesso somente de aplicativo para o Azure Resource Manager
Para confirmar que o aplicativo pode acessar a assinatura, faça um teste de tarefa na assinatura usando um token somente de aplicativo.

Para obter um token de acesso somente de aplicativo, siga as instruções da seção [Obter o token de acesso somente de aplicativo para a API do Azure AD Graph](#app-azure-ad-graph)com um valor diferente para o parâmetro de recurso:

    https://management.core.windows.net/

O método [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) do aplicativo de exemplo do ASP.NET MVC obtém um token de acesso somente de aplicativo para o Azure Resource Manager usando a Biblioteca de Autenticação do Active Directory para .NET.

#### <a name="get-applications-permissions-on-subscription"></a>Obter permissões do aplicativo na assinatura
Para verificar que seu aplicativo pode acessar uma assinatura do Azure, você também pode chamar o [permissões do Resource Manager](https://docs.microsoft.com/rest/api/authorization/permissions) API. Essa abordagem é semelhante a como você determinou se o usuário tem direitos de Gerenciamento de Acesso para a assinatura. No entanto, desta vez, chame a API de permissões com o token de acesso somente de aplicativo que foi recebido na etapa anterior.

O método [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) do aplicativo de exemplo do ASP.NET MVC implementa essa chamada.

## <a name="manage-connected-subscriptions"></a>Gerenciar assinaturas conectadas
Quando a função RBAC apropriada é atribuída à entidade de serviço do aplicativo na assinatura, seu aplicativo pode continuar monitorando-a/gerenciando-a usando tokens de acesso somente de aplicativo para o Azure Resource Manager.

Se um proprietário de assinatura remover a atribuição de função do aplicativo usando o portal ou as ferramentas de linha de comando, o aplicativo não poderá mais acessar essa assinatura. Nesse caso, você deve informar ao usuário que a conexão com a assinatura foi cortada fora do aplicativo e dar-lhe uma opção para "reparar" a conexão. A opção “Reparar” recriará a atribuição de função que foi excluída offline.

Assim como você habilitou o usuário para conectar as assinaturas ao aplicativo, também é preciso permitir que o usuário desconecte as assinaturas. Do ponto de vista do gerenciamento de acesso, desconectar significa remover a atribuição da função que a entidade do serviço do aplicativo tem na assinatura. Se desejar, o estado da assinatura no aplicativo também pode ser removido.
Somente os usuários com permissão de gerenciamento de acesso na assinatura podem desconectar a assinatura.

O método [RevokeRoleFromServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L200) do aplicativo de exemplo do ASP.NET MVC implementa essa chamada.

Pronto! Agora os usuários podem agora se conectar e gerenciar suas assinaturas do Azure com seu aplicativo facilmente.

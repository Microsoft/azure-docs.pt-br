---
title: Usar o Azure Active Directory para autenticar as soluções de gerenciamento do lote | Microsoft Docs
description: Aplicativos criados com Azure Resource Manager e o provedor de recursos do lote são autenticados com o Azure AD.
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/27/2017
ms.author: lahugh
ms.openlocfilehash: 18cb7433de81ddf6733a494778d0a7c82afb5677
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323974"
---
# <a name="authenticate-batch-management-solutions-with-active-directory"></a>Autenticar soluções de gerenciamento do lote com o Active Directory

Os aplicativos que chamam o serviço de gerenciamento do lote do Azure se autenticam com o [Azure Active Directory][aad_about] (Azure AD). O Azure AD é o serviço de gerenciamento de identidade e diretório multilocatário com base em nuvem da Microsoft. O Azure em si usa o AD do Azure (Active Directory do Azure) para a autenticação de seus clientes, administradores de serviços e usuários organizacionais.

A biblioteca .NET do Gerenciamento do Lote expõe os tipos para trabalhar com contas, chaves de conta, aplicativos e pacotes de aplicativos do Lote. A biblioteca .NET de gerenciamento do lote é um cliente do provedor de recursos do Azure e é usada junto com [Azure Resource Manager][resman_overview] para gerenciar esses recursos programaticamente. O Azure AD é necessário para autenticar solicitações feitas por meio de qualquer cliente do provedor de recursos do Azure, incluindo a biblioteca .NET de gerenciamento do lote e por meio de [Azure Resource Manager][resman_overview].

Neste artigo, exploraremos como usar o Azure AD para autenticar aplicativos que usem a biblioteca do Batch Management .NET. Mostramos como usar o Azure AD para autenticar um administrador ou coadministrador de assinatura usando a autenticação integrada. Usamos o projeto de exemplo [AccountManagement][acct_mgmt_sample] , disponível no GitHub, para percorrer o uso do Azure AD com a biblioteca .net de gerenciamento do lote.

Para saber mais sobre como usar a biblioteca .NET do Gerenciamento do Lote e o exemplo AccountManagement, veja [Gerenciar contas e cotas do Lote com a biblioteca de cliente do Gerenciamento do Lote para .NET](batch-management-dotnet.md).

## <a name="register-your-application-with-azure-ad"></a>Registrar seu aplicativo no Azure AD

O Adal ( [biblioteca de autenticação do Active Directory][aad_adal] do Azure) fornece uma interface programática para o Azure ad para uso em seus aplicativos. Para chamar a ADAL de seu aplicativo, você deve registrar seu aplicativo em um locatário do Azure AD. Quando você registra seu aplicativo, fornece ao Azure AD informações sobre seu aplicativo, incluindo um nome para ele dentro do locatário do Azure AD. Em seguida, o Azure AD fornece uma ID de aplicativo que você usa para associar seu aplicativo ao Azure AD em tempo de execução. Para saber mais sobre a ID do aplicativo, veja [Objetos de aplicativo e de entidade de serviço no Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

Para registrar o aplicativo de exemplo AccountManagement, siga as etapas na seção [adicionando um aplicativo](../active-directory/develop/quickstart-register-app.md) em [integrando aplicativos com o Azure Active Directory][aad_integrate]. Especifique **aplicativo cliente nativo** para o tipo de aplicativo. O URI do OAuth 2.0 padrão do setor para o **URI de Redirecionamento** é `urn:ietf:wg:oauth:2.0:oob`. Porém, você pode especificar qualquer URI válido (como `http://myaccountmanagementsample`) para o **URI de Redirecionamento**, pois ele não precisa ser um ponto de extremidade real:

![](./media/batch-aad-auth-management/app-registration-management-plane.png)

Depois de concluir o processo de registro, você verá a ID do aplicativo e a ID de objeto (entidade de serviço) listados para seu aplicativo.  

![](./media/batch-aad-auth-management/app-registration-client-id.png)

## <a name="grant-the-azure-resource-manager-api-access-to-your-application"></a>Conceder à API do Azure Resource Manager acesso a seu aplicativo

Em seguida, você precisará delegar o acesso ao seu aplicativo para a API do Azure Resource Manager. O identificador do Azure AD para a API do Resource Manager é **API de Gerenciamento de Serviços do Microsoft Azure**.

Siga estas etapas no portal do Azure:

1. No painel de navegação esquerdo do Portal do Azure, escolha **Todos os serviços**, clique em **Registros de Aplicativo** e clique em **Adicionar**.
2. Pesquise pelo nome do seu aplicativo na lista de registros do aplicativo:

    ![Procure o nome do aplicativo](./media/batch-aad-auth-management/search-app-registration.png)

3. Exibição de **configurações** folha. Na seção **Acesso à API**, selecione **Permissões necessárias**.
4. Clique em **adicionar** para adicionar uma nova permissão necessária. 
5. Na etapa 1, digite **API de gerenciamento de serviços do Microsoft Azure**, selecione essa API da lista de resultados e clique no **selecione** botão.
6. Na etapa 2, selecione a caixa de seleção Avançar ao **Aceder a modelo de implantação clássico do Azure enquanto usuários da organização**e clique no **selecione** botão.
7. Clique no botão **Concluído**.

O **permissões necessárias** folha agora mostra que as permissões para o seu aplicativo são concedidas ao ADAL e APIs do Gerenciador de recursos. Permissões são concedidas a ADAL por padrão, quando você primeiro registra seu aplicativo com o Azure AD.

![Delegar permissões para a API do Azure Resource Manager](./media/batch-aad-auth-management/required-permissions-management-plane.png)

## <a name="azure-ad-endpoints"></a>Pontos de extremidade do Azure AD

Para autenticar suas soluções de gerenciamento do lote com o Azure AD, você precisará de dois pontos de extremidade conhecidos.

- O **ponto de extremidade comum do Azure AD** fornece uma interface de coleta de credenciais genérica quando um locatário específico não é fornecido, como no caso de autenticação integrada:

    `https://login.microsoftonline.com/common`

- O **ponto de extremidade do Azure Resource Manager** é usado para adquirir um token para autenticar solicitações no serviço de gerenciamento do lote:

    `https://management.core.windows.net/`

O aplicativo de exemplo AccountManagement define constantes para esses pontos de extremidade. Deixe essas constantes inalterados:

```csharp
// Azure Active Directory "common" endpoint.
private const string AuthorityUri = "https://login.microsoftonline.com/common";
// Azure Resource Manager endpoint 
private const string ResourceUri = "https://management.core.windows.net/";
```

## <a name="reference-your-application-id"></a>Consultar a ID do aplicativo 

O aplicativo cliente usa a ID do aplicativo (também conhecida como a ID do cliente) para acessar o Azure AD em tempo de execução. Depois de registrar seu aplicativo no portal do Azure, atualize seu código para usar a ID do aplicativo fornecida pelo Azure AD para seu aplicativo registrado. No aplicativo de exemplo de AccountManagement, copie a ID do aplicativo do portal do Azure à constante apropriada:

```csharp
// Specify the unique identifier (the "Client ID") for your application. This is required so that your
// native client application (i.e. this sample) can access the Microsoft Azure AD Graph API. For information
// about registering an application in Azure Active Directory, please see "Adding an Application" here:
// https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
private const string ClientId = "<application-id>";
```
Também copie o URI que você especificou durante o processo de registro de redirecionamento. O URI de redirecionamento especificado no código deve corresponder ao URI de redirecionamento que você forneceu quando registrou o aplicativo.

```csharp
// The URI to which Azure AD will redirect in response to an OAuth 2.0 request. This value is
// specified by you when you register an application with AAD (see ClientId comment). It does not
// need to be a real endpoint, but must be a valid URI (e.g. https://accountmgmtsampleapp).
private const string RedirectUri = "http://myaccountmanagementsample";
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Adquirir um token de autenticação do Azure AD

Depois de registrar o exemplo AccountManagement no locatário do Azure AD e de atualizar o código-fonte de exemplo com os valores, o exemplo estará pronto para se autenticar usando o Azure AD. Quando você executar o exemplo, o ADAL tenta adquirir um token de autenticação. Nesta etapa, ele solicitará suas credenciais da Microsoft: 

```csharp
// Obtain an access token using the "common" AAD resource. This allows the application
// to query AAD for information that lies outside the application's tenant (such as for
// querying subscription information in your Azure account).
AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
AuthenticationResult authResult = authContext.AcquireToken(ResourceUri,
                                                        ClientId,
                                                        new Uri(RedirectUri),
                                                        PromptBehavior.Auto);
```

Depois de fornecer suas credenciais, o aplicativo de exemplo poderá emitir solicitações autenticadas para o serviço de Gerenciamento do Lote. 

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como executar o [aplicativo de exemplo AccountManagement][acct_mgmt_sample], consulte [gerenciar contas e cotas do lote com a biblioteca de cliente de gerenciamento do lote para .net](batch-management-dotnet.md).

Para saber mais sobre o Azure AD, veja a [documentação do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/). Exemplos mais detalhados que mostram como usar o ADAL estão disponíveis na biblioteca [Amostras de código do Azure](https://azure.microsoft.com/resources/samples/?service=active-directory).

Para autenticar aplicativos de serviço do lote usando o Azure AD, consulte [Autenticar soluções de serviço do lote no Active Directory](batch-aad-auth.md). 


[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "O que é o Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]:../active-directory/develop/authentication-scenarios.md "Cenários de autenticação do Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrando aplicativos com o Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[azure_portal]: https://portal.azure.com
[resman_overview]: ../azure-resource-manager/resource-group-overview.md

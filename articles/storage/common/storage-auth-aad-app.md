---
title: Adquirir um token do Azure AD para autorizar solicitações de um aplicativo cliente
titleSuffix: Azure Storage
description: Use Azure Active Directory para autenticar de dentro de um aplicativo cliente, adquirir um token OAuth 2,0 e autorizar solicitações para armazenamento de BLOBs do Azure e armazenamento de filas.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/07/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: baad1a0b173ae89fec9d160572224c6cb0aa615d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103574621"
---
# <a name="acquire-a-token-from-azure-ad-for-authorizing-requests-from-a-client-application"></a>Adquirir um token do Azure AD para autorizar solicitações de um aplicativo cliente

Uma vantagem importante de usar o Azure Active Directory (Azure AD) com o armazenamento de BLOBs do Azure ou o armazenamento de filas é que suas credenciais não precisam mais ser armazenadas em seu código. Em vez disso, você pode solicitar um token de acesso OAuth 2,0 da plataforma de identidade da Microsoft. O Azure AD autentica a entidade de segurança (um usuário, grupo ou entidade de serviço) que executa o aplicativo. Se a autenticação for bem sucedido, o Azure AD retornará o token de acesso para o aplicativo e o aplicativo poderá usar o token de acesso para autorizar solicitações ao armazenamento de BLOBs do Azure ou armazenamento de filas.

Este artigo mostra como configurar seu aplicativo nativo ou aplicativo Web para autenticação com a plataforma de identidade da Microsoft usando um aplicativo de exemplo que está disponível para download. O aplicativo de exemplo apresenta o .NET, mas outras linguagens usam uma abordagem semelhante. Para obter mais informações sobre a plataforma de identidade da Microsoft, consulte [visão geral da plataforma Microsoft Identity](../../active-directory/develop/v2-overview.md).

Para obter uma visão geral do fluxo de concessão do código do OAuth 2.0, consulte [Autorizar acesso a aplicativos Web do Azure Active Directory usando o fluxo de concessão do código do OAuth 2.0](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

## <a name="about-the-sample-application"></a>Sobre o aplicativo de exemplo

O aplicativo de exemplo fornece uma experiência de ponta a ponta que mostra como configurar um aplicativo Web para autenticação com o Azure AD em um ambiente de desenvolvimento local. Para exibir e executar o aplicativo de exemplo, primeiro clone ou baixe-o no [GitHub](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal). Em seguida, siga as etapas descritas no artigo para configurar um registro de aplicativo do Azure e atualizar o aplicativo para seu ambiente.

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>Atribuir uma função a uma entidade de segurança do Azure AD

Para autenticar uma entidade de segurança de seu aplicativo de armazenamento do Azure, primeiro configure as configurações do Azure RBAC (controle de acesso baseado em função) para essa entidade de segurança. O armazenamento do Azure define funções internas que abrangem permissões para contêineres e filas. Quando a função do Azure é atribuída a uma entidade de segurança, essa entidade de segurança recebe acesso a esse recurso. Para obter mais informações, consulte [gerenciar direitos de acesso ao blob do Azure e dados de fila com o RBAC do Azure](./storage-auth-aad-rbac-portal.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Registre o aplicativo com um locatário do Azure AD

A primeira etapa no uso do Azure AD para autorizar o acesso aos recursos de armazenamento é registrar seu aplicativo cliente com um locatário do Azure AD do [portal do Azure](https://portal.azure.com). Ao registrar seu aplicativo cliente, você fornece informações sobre o aplicativo para o Azure AD. O Azure AD, em seguida, fornece uma ID do cliente (também chamado de *ID do aplicativo*) que você usa para associar o aplicativo ao Azure AD no runtime. Para saber mais sobre a ID do cliente, consulte [Objetos de entidade de serviço e aplicativo no Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md). Para registrar seu aplicativo de armazenamento do Azure, siga as etapas mostradas em [início rápido: registrar um aplicativo com a plataforma de identidade da Microsoft](../../active-directory/develop/quickstart-configure-app-access-web-apis.md). 

A imagem a seguir mostra as configurações comuns para o registro de um aplicativo Web. Observe que, neste exemplo, o URI de redirecionamento é definido como `http://localhost:5000/signin-oidc` para testar o aplicativo de exemplo no ambiente de desenvolvimento. Você pode modificar essa configuração posteriormente na configuração de **autenticação** para seu aplicativo registrado no portal do Azure:

:::image type="content" source="media/storage-auth-aad-app/app-registration.png" alt-text="Captura de tela mostrando como registrar seu aplicativo de armazenamento com o Azure AD":::

> [!NOTE]
> Se você registrar seu aplicativo como um aplicativo nativo, poderá especificar qualquer URI válido para o **URI de redirecionamento**. Para aplicativos nativos, esse valor não precisa ser uma URL real. Para aplicativos Web, o URI de redirecionamento deve ser um URI válido, pois ele especifica a URL para a qual os tokens são fornecidos.

Depois de registrar o aplicativo, você verá a ID do aplicativo (ou ID do cliente) em **Configurações**:

:::image type="content" source="media/storage-auth-aad-app/app-registration-client-id.png" alt-text="Captura de tela mostrando a ID do cliente":::

Para obter mais informações sobre como registrar um aplicativo no Azure AD, consulte [Integrando aplicativos com o Azure Active Directory](../../active-directory/develop/quickstart-register-app.md).

### <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Conceder as permissões de aplicativo registradas ao Armazenamento do Microsoft Azure

Em seguida, conceda ao seu aplicativo permissões para chamar as APIs de armazenamento do Azure. Esta etapa permite que seu aplicativo autorize solicitações ao armazenamento do Azure com o Azure AD.

1. Na página **permissões de API** para seu aplicativo registrado, selecione **Adicionar uma permissão**.
1. Na guia **APIs da Microsoft** , selecione **armazenamento do Azure**.
1. No painel **solicitar permissões de API** , sob **que tipo de permissões seu aplicativo requer?**, observe que o tipo de permissão disponível é **permissões delegadas**. Essa opção é selecionada por padrão.
1. Em **permissões**, marque a caixa de seleção ao lado de **user_impersonation** e, em seguida, selecione o botão **adicionar permissões** .

    :::image type="content" source="media/storage-auth-aad-app/registered-app-permissions-1.png" alt-text="Captura de tela mostrando permissões para a API de armazenamento":::

1. Em seguida, conceda consentimento de administrador para essas permissões clicando em **conceder consentimento do administrador para o diretório padrão**.

O painel **permissões de API** agora mostra que o aplicativo registrado do Azure ad tem acesso às APIs de armazenamento do Microsoft Graph e do Azure e que o consentimento é concedido para o diretório padrão. As permissões são concedidas ao Microsoft Graph automaticamente quando você registra o aplicativo no Azure Active Directory pela primeira vez.

:::image type="content" source="media/storage-auth-aad-app/registered-app-permissions-2.png" alt-text="Captura de tela mostrando permissões de API para o aplicativo registrado":::

### <a name="create-a-client-secret"></a>Criar um segredo do cliente

O aplicativo precisa de um segredo do cliente para provar sua identidade ao solicitar um token. Para adicionar o segredo do cliente, siga estas etapas:

1. Navegue até o registro do aplicativo no portal do Azure.
1. Selecione a configuração **certificados & segredos** .
1. Em **segredos do cliente**, clique em **novo segredo do cliente** para criar um novo segredo.
1. Forneça uma descrição para o segredo e escolha o intervalo de expiração desejado.
1. Copie imediatamente o valor do novo segredo para um local seguro. O valor completo é exibido apenas uma vez.

    ![Captura de tela mostrando o segredo do cliente](media/storage-auth-aad-app/client-secret.png)

### <a name="enable-implicit-grant-flow"></a>Habilitar fluxo de concessão implícito

Em seguida, configure o fluxo de concessão implícita para seu aplicativo. Siga estas etapas:

1. Navegue até o registro do aplicativo no portal do Azure.
1. Na seção **gerenciar** , selecione a configuração de **autenticação** .
1. Na seção **concessão implícita** , marque a caixa de seleção para habilitar tokens de ID, conforme mostrado na imagem a seguir:

    :::image type="content" source="media/storage-auth-aad-app/enable-implicit-grant-flow.png" alt-text="Captura de tela mostrando como habilitar as configurações para o fluxo de concessão implícita":::

## <a name="client-libraries-for-token-acquisition"></a>Bibliotecas de cliente para aquisição de token

Depois de registrar seu aplicativo e conceder permissões de ti para acessar dados no armazenamento de BLOBs do Azure ou armazenamento de filas, você pode adicionar código ao seu aplicativo para autenticar uma entidade de segurança e adquirir um token 2,0 do OAuth. Para autenticar e adquirir o token, você pode usar uma das [bibliotecas de autenticação da plataforma de identidade da Microsoft](../../active-directory/develop/reference-v2-libraries.md) ou outra biblioteca de software livre que ofereça suporte a OpenID Connect 1,0. Seu aplicativo pode, então, usar o token de acesso para autorizar uma solicitação no armazenamento de BLOBs do Azure ou no armazenamento de filas.

Para obter uma lista de cenários para os quais há suporte para tokens de aquisição, consulte a seção [fluxos de autenticação](../../active-directory/develop/msal-authentication-flows.md) da documentação da [MSAL (biblioteca de autenticação da Microsoft)](../../active-directory/develop/msal-overview.md) .

## <a name="well-known-values-for-authentication-with-azure-ad"></a>Valores conhecidos para autenticação com Azure AD

Para autenticar uma entidade de segurança com Azure AD, você precisa incluir alguns valores conhecidos no código.

### <a name="azure-ad-authority"></a>Autoridade do Azure AD

Para a nuvem pública da Microsoft, a autoridade do Azure AD básica é a seguinte, em que *tenant-id* é a ID do locatário do Active Directory (ou ID de diretório):

`https://login.microsoftonline.com/<tenant-id>/`

A ID do locatário identifica o locatário do Azure AD a ser usado para autenticação. Ele também é conhecido como a ID do diretório. Para recuperar a ID do locatário, navegue até a página **visão geral** do registro do aplicativo no portal do Azure e copie o valor de lá.

### <a name="azure-storage-resource-id"></a>ID de recurso de armazenamento do Azure

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>Exemplo de código .NET: crie um blob de blocos

O exemplo de código mostra como obter um token de acesso do Azure AD. O token de acesso é usado para autenticar o usuário especificado e, em seguida, autoriza uma solicitação para criar um blob de blocos. Para que esse exemplo funcione, primeiro siga as etapas descritas nas seções anteriores.

Para solicitar o token, você precisará dos seguintes valores do registro do aplicativo:

- O nome do seu domínio do Azure AD. Recupere esse valor da página **visão geral** do seu Azure Active Directory.
- A ID do locatário (ou diretório). Recupere esse valor da página **visão geral** do registro do aplicativo.
- A ID do cliente (ou aplicativo). Recupere esse valor da página **visão geral** do registro do aplicativo.
- O URI de redirecionamento de cliente. Recupere esse valor das configurações de **autenticação** para o registro do aplicativo.
- O valor do segredo do cliente. Recupere esse valor do local para o qual você o copiou anteriormente.

### <a name="create-a-storage-account-and-container"></a>Criar uma conta e um contêiner de armazenamento

Para executar o exemplo de código, crie uma conta de armazenamento na mesma assinatura que o Azure Active Directory. Em seguida, crie um contêiner dentro dessa conta de armazenamento. O código de exemplo criará um blob de blocos neste contêiner.

Em seguida, atribua explicitamente a função de **colaborador de dados de blob de armazenamento** à conta de usuário sob a qual você executará o código de exemplo. Para obter instruções sobre como atribuir essa função no portal do Azure, consulte [usar o portal do Azure para atribuir uma função do Azure para acesso a dados de BLOB e de fila](storage-auth-aad-rbac-portal.md).

> [!NOTE]
> Ao criar uma conta de armazenamento do Azure, você não recebe automaticamente permissões para acessar dados por meio do Azure AD. Você deve atribuir explicitamente a si mesmo uma função do Azure para o armazenamento do Azure. Você pode atribuí-la no nível de assinatura, grupo de recursos, conta de armazenamento ou contêiner ou fila.
>
> Antes de atribuir a si mesmo uma função para acesso a dados, você poderá acessar dados em sua conta de armazenamento por meio do portal do Azure porque o portal do Azure também pode usar a chave de conta para acesso a dados. Para obter mais informações, consulte [escolher como autorizar o acesso a dados de blob no portal do Azure](../blobs/authorize-data-operations-portal.md).

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>Criar um aplicativo Web que autorize o acesso ao armazenamento de BLOBs com o Azure AD

Quando seu aplicativo acessa o armazenamento do Azure, ele faz isso em nome do usuário, o que significa que os recursos de BLOB ou fila são acessados usando as permissões do usuário que está conectado. Para testar este exemplo de código, você precisa de um aplicativo Web que solicita ao usuário para entrar usando uma identidade do Azure AD. Você pode criar seu próprio ou usar o aplicativo de exemplo fornecido pela Microsoft.

Um aplicativo Web de exemplo completo que adquire um token e o usa para criar um blob no armazenamento do Azure está disponível no [GitHub](https://aka.ms/aadstorage). Revisar e executar o exemplo completo pode ser útil para entender os exemplos de código. Para obter instruções sobre como executar o exemplo concluído, consulte a seção intitulada [Exibir e executar o exemplo concluído](#view-and-run-the-completed-sample).

#### <a name="add-references-and-using-statements"></a>Adicionar referências e usar instruções  

No Visual Studio, instale a biblioteca de cliente do armazenamento do Azure. No menu **ferramentas** , selecione **Gerenciador de pacotes NuGet** e **console do Gerenciador de pacotes**. Digite os seguintes comandos na janela do console para instalar os pacotes necessários da biblioteca de cliente do armazenamento do Azure para .NET:

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

```console
Install-Package Azure.Storage.Blobs
Install-Package Microsoft.Identity.Web -Version 0.4.0-preview
```

Em seguida, adicione as seguintes instruções using ao arquivo HomeController. cs:

```csharp
using Microsoft.Identity.Web; //MSAL library for getting the access token
using Azure.Storage.Blobs;
```

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Identity.Web -Version 0.4.0-preview //or a later version
```

Em seguida, adicione as seguintes instruções using ao arquivo HomeController. cs:

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
```

---

#### <a name="create-a-block-blob"></a>criar um blob de blocos

Adicione o seguinte trecho de código para criar um blob de blocos. Lembre-se de substituir os valores entre colchetes angulares pelos seus próprios valores:

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

```csharp
private static async Task<string> CreateBlob(TokenAcquisitionTokenCredential tokenCredential)
{
    Uri blobUri = new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt");
    BlobClient blobClient = new BlobClient(blobUri, tokenCredential);

    string blobContents = "Blob created by Azure AD authenticated user.";
    byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

    using (MemoryStream stream = new MemoryStream(byteArray))
    {
        await blobClient.UploadAsync(stream);
    }
    return "Blob successfully created";
}
```

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

```csharp
private static async Task<string> CreateBlob(string accessToken)
{
    // Create a blob on behalf of the user.
    TokenCredential tokenCredential = new TokenCredential(accessToken);
    StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

    // Replace the URL below with the URL to your blob.
    Uri blobUri = new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt");
    CloudBlockBlob blob = new CloudBlockBlob(blobUri, storageCredentials);
    await blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
    return "Blob successfully created";
}
```

---

> [!NOTE]
> Para autorizar operações de BLOB e de fila com um token OAuth 2,0, você deve usar HTTPS.

No exemplo acima, a biblioteca de cliente .NET lida com a autorização da solicitação para criar o blob de blocos. As bibliotecas de cliente de armazenamento do Azure para outras linguagens também lidam com a autorização da solicitação para você. No entanto, se você estiver chamando uma operação de armazenamento do Azure com um token OAuth usando a API REST, você precisará construir o cabeçalho de **autorização** usando o token OAuth.

Para chamar as operações de serviço Blob e Queue usando tokens de acesso OAuth, passe o token de acesso no cabeçalho **Authorization** usando o esquema **Bearer** e especifique uma versão de serviço de 2017-11-09 ou superior, conforme mostrado no exemplo a seguir:

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-access-token-from-azure-ad"></a>Obter um token de acesso do Azure AD

Em seguida, adicione um método que solicite um token do Azure AD em nome do usuário. Esse método define o escopo para o qual as permissões devem ser concedidas. Para obter mais informações sobre permissões e escopos, consulte [permissões e consentimento no ponto de extremidade da plataforma Microsoft Identity](../../active-directory/develop/v2-permissions-and-consent.md).

Use a ID do recurso para construir o escopo para o qual adquirir o token. O exemplo constrói o escopo usando a ID do recurso junto com o `user_impersonation` escopo interno, que indica que o token está sendo solicitado em nome do usuário.

Tenha em mente que talvez seja necessário apresentar ao usuário uma interface que permita ao usuário consentir que solicite o token de seu nome:

```csharp
[AuthorizeForScopes(Scopes = new string[] { "https://storage.azure.com/user_impersonation" })]
public async Task<IActionResult> Blob()
{
    string message = await CreateBlob(new TokenAcquisitionTokenCredential(_tokenAcquisition));
    ViewData["Message"] = message;
    return View();
}
```

Consentimento é o processo de um usuário conceder autorização a um aplicativo para acessar recursos protegidos em seu nome. A plataforma de identidade da Microsoft dá suporte ao consentimento incremental, o que significa que uma entidade de segurança pode solicitar um conjunto mínimo de permissões inicialmente e adicionar permissões ao longo do tempo, conforme necessário. Quando o código solicitar um token de acesso, especifique o escopo das permissões de que seu aplicativo precisa. Para obter mais informações sobre o consentimento incremental, consulte [consentimento incremental e dinâmico](../../active-directory/azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent).

## <a name="view-and-run-the-completed-sample"></a>Exibir e executar o exemplo concluído

Para executar o aplicativo de exemplo, primeiro clone ou baixe-o do [GitHub](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal). Em seguida, atualize o aplicativo conforme descrito nas seções a seguir.

### <a name="provide-values-in-the-settings-file"></a>Forneça valores no arquivo de configurações

Atualize o *appsettings.jsno* arquivo com seus próprios valores, da seguinte maneira:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "<azure-ad-domain-name>.onmicrosoft.com",
    "TenantId": "<tenant-id>",
    "ClientId": "<client-id>",
    "ClientSecret": "<client-secret>",
    "ClientCertificates": [
    ],
    "CallbackPath": "/signin-oidc"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="update-the-storage-account-and-container-name"></a>Atualizar a conta de armazenamento e o nome do contêiner

No arquivo *HomeController. cs* , atualize o URI que faz referência ao blob de blocos para usar o nome da sua conta de armazenamento e do contêiner, substituindo os valores entre colchetes angulares com seus próprios valores:

```html
https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt
```

## <a name="next-steps"></a>Próximas etapas

- [Plataforma de identidade da Microsoft](../../active-directory/develop/index.yml)
- [Gerenciar direitos de acesso aos dados de armazenamento com o Azure RBAC](./storage-auth-aad-rbac-portal.md)
- [Autenticar o acesso a BLOBs e filas com Azure Active Directory e identidades gerenciadas para recursos do Azure](storage-auth-aad-msi.md)

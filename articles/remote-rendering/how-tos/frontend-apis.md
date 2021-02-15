---
title: APIs de front-end do Azure para autenticação
description: Explica como usar a API de front-end C# para autenticação
author: florianborn71
ms.author: flborn
ms.date: 02/12/2010
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 7149d7ac2625eb60a1d0d22253b93b68a99475de
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592085"
---
# <a name="use-the-azure-frontend-apis-for-authentication"></a>Usar as APIs de front-end do Azure para autenticação

Nesta seção, descreveremos como usar a API para autenticação e gerenciamento de sessão.

> [!CAUTION]
> As funções descritas neste capítulo emitem as chamadas REST no servidor internamente. Para todas as chamadas REST, o envio desses comandos com muita frequência fará com que o servidor seja limitado e retorne a falha eventualmente. O valor do membro, nesse `SessionGeneralContext.HttpResponseCode` caso, é 429 ("muitas solicitações"). Como regra geral, deve haver um atraso de **5 a 10 segundos entre as chamadas subsequentes**.


## <a name="sessionconfiguration"></a>SessionConfiguration

SessionConfiguration é usado para configurar as informações de autenticação para uma ```RemoteRenderingClient``` instância no SDK.

Os campos importantes são:

```cs
public class SessionConfiguration
{
    // Domain that will be used for account authentication for the Azure Remote Rendering service, in the form [region].mixedreality.azure.com.
    // [region] should be set to the domain of the Azure Remote Rendering account.
    public string AccountDomain;
    // Domain that will be used to generate sessions for the Azure Remote Rendering service, in the form [region].mixedreality.azure.com.
    // [region] should be selected based on the region closest to the user. For example, westus2.mixedreality.azure.com or westeurope.mixedreality.azure.com.
    public string RemoteRenderingDomain;

    // Can use one of:
    // 1) ID and Key.
    // 2) ID and AuthenticationToken.
    // 3) ID and AccessToken.
    public string AccountId = Guid.Empty.ToString();
    public string AccountKey = string.Empty;
    public string AuthenticationToken = string.Empty;
    public string AccessToken = string.Empty;
}
```

O equivalente C++ é semelhante a:

```cpp
struct SessionConfiguration
{
    std::string AccountDomain{};
    std::string RemoteRenderingDomain{};
    std::string AccountId{};
    std::string AccountKey{};
    std::string AuthenticationToken{};
    std::string AccessToken{};
};
```

Para o bloco _região_ no domínio, use uma [região perto de você](../reference/regions.md).

As informações da conta podem ser obtidas no portal, conforme descrito no parágrafo sobre como [recuperar informações da conta](create-an-account.md#retrieve-the-account-information).

## <a name="azure-frontend"></a>Azure Frontend

As classes relevantes são ```RemoteRenderingClient``` e ```RenderingSession```. ```RemoteRenderingClient``` é usado para gerenciamento de conta e funcionalidade de nível de conta, o que inclui: conversão de ativos e criação de sessão de renderização. ```RenderingSession``` é usado para a funcionalidade no nível de sessão e inclui: atualização de sessão, consultas, renovação e encerramento.

Cada ```RenderingSession``` aberta/criada manterá uma referência ao front-end que o criou. Para desligar corretamente, todas as sessões devem ser desalocadas antes que o front-end seja desalocado.

A desalocação de uma sessão não interromperá o servidor no Azure, `RenderingSession.StopAsync` deve ser chamada explicitamente.

Depois que uma sessão tiver sido criada e seu estado tiver sido marcado como pronto, ele poderá se conectar ao runtime do Remote Rendering com `RenderingSession.ConnectAsync`.

### <a name="threading"></a>Threading

Todas as chamadas assíncronas RenderingSession e RemoteRenderingClient são concluídas em um thread em segundo plano, não no thread do aplicativo principal.

### <a name="conversion-apis"></a>APIs de conversão

Para obter mais informações sobre o serviço de conversão, veja [API REST de conversão de modelo](conversion/conversion-rest-api.md).

#### <a name="start-asset-conversion"></a>Iniciar conversão de ativo

```cs
async void StartAssetConversion(RemoteRenderingClient client, string storageContainer, string blobinputpath, string bloboutpath, string modelName, string outputName)
{
    var result = await client.StartAssetConversionAsync(
        new AssetConversionInputOptions(storageContainer, blobinputpath, "", modelName),
        new AssetConversionOutputOptions(storageContainer, bloboutpath, "", outputName)
        );
}
```

```cpp
void StartAssetConversion(ApiHandle<RemoteRenderingClient> client, std::string storageContainer, std::string blobinputpath, std::string bloboutpath, std::string modelName, std::string outputName)
{
    AssetConversionInputOptions input;
    input.BlobContainerInformation.BlobContainerName = blobinputpath;
    input.BlobContainerInformation.StorageAccountName = storageContainer;
    input.BlobContainerInformation.FolderPath = "";
    input.InputAssetPath = modelName;

    AssetConversionOutputOptions output;
    output.BlobContainerInformation.BlobContainerName = blobinputpath;
    output.BlobContainerInformation.StorageAccountName = storageContainer;
    output.BlobContainerInformation.FolderPath = "";
    output.OutputAssetPath = outputName;

    client->StartAssetConversionAsync(input, output, [](Status status, ApiHandle<AssetConversionResult> result) {
        if (status == Status::OK)
        {
            //use result
        }
        else
        {
            printf("Failed to start asset conversion!");
        }
    });
}
```

#### <a name="get-conversion-status"></a>Obter status de conversão

```cs
async void GetConversionStatus(RemoteRenderingClient client, string assetId)
{
    AssetConversionStatusResult status = await client.GetAssetConversionStatusAsync(assetId);
    // do something with status (e.g. check current status etc.)
}
```

```cpp
void GetConversionStatus(ApiHandle<RemoteRenderingClient> client, std::string assetId)
{
    client->GetAssetConversionStatusAsync(assetId, [](Status status, ApiHandle<AssetConversionStatusResult> result) {
        if (status == Status::OK)
        {
            // do something with result (e.g. check current status etc.)
        }
        else
        {
            printf("Failed to get status of asset conversion!");
        }
    });
}

```


### <a name="rendering-apis"></a>APIs de renderização

Veja [API REST de gerenciamento de sessão](session-rest-api.md) para ver detalhes sobre o gerenciamento de sessão.

Uma sessão de renderização pode ser criada dinamicamente no serviço ou uma ID de sessão já existente pode ser "aberta" em um objeto RenderingSession.

#### <a name="create-rendering-session"></a>Criar uma sessão de renderização

```cs
async void CreateRenderingSession(RemoteRenderingClient client, RenderingSessionVmSize vmSize, int maxLeaseInMinutes)
{
    CreateRenderingSessionResult result = await client.CreateNewRenderingSessionAsync(
        new RenderingSessionCreationOptions(vmSize, maxLeaseInMinutes / 60, maxLeaseInMinutes % 60));

    // if the call was successful, result.Session holds a valid session reference, otherwise check result.Context for error information
}
```

```cpp
void CreateRenderingSession(ApiHandle<RemoteRenderingClient> client, RenderingSessionVmSize vmSize, int maxLeaseInMinutes)
{
    RenderingSessionCreationOptions params;
    params.MaxLeaseInMinutes = maxLeaseInMinutes;
    params.Size = vmSize;
    client->CreateNewRenderingSessionAsync(params, [](Status status, ApiHandle<CreateRenderingSessionResult> result) {
        if (status == Status::OK && result->GetErrorCode() == Result::Success)
        {
            result->GetSession();
            //use res->Result
        }
        else
        {
            printf("Failed to create session!");
        }
    });
}
```

#### <a name="open-an-existing-rendering-session"></a>Abrir uma sessão de renderização existente

A abertura de uma sessão existente é uma chamada síncrona.

```cs
async void CreateRenderingSession(RemoteRenderingClient client, string sessionId)
{
    CreateRenderingSessionResult result = await client.OpenRenderingSessionAsync(sessionId);
    if (result.ErrorCode == Result.Success)
    {
        RenderingSession session = result.Session;
        // Query session status, etc.
    }
}
```

```cpp
void CreateRenderingSession(ApiHandle<RemoteRenderingClient> client, std::string sessionId)
{
    client->OpenRenderingSessionAsync(sessionId, [](Status status, ApiHandle<CreateRenderingSessionResult> result) {
        if (status == Status::OK && result->GetErrorCode()==Result::Success)
        {
            ApiHandle<RenderingSession> session = result->GetSession();
            // Query session status, etc.
        }
    });
}
```


#### <a name="get-current-rendering-sessions"></a>Obter sessões de renderização atuais

```cs
async void GetCurrentRenderingSessions(RemoteRenderingClient client)
{
    RenderingSessionPropertiesArrayResult result = await client.GetCurrentRenderingSessionsAsync();
    if (result.ErrorCode == Result.Success)
    {
        RenderingSessionProperties[] properties = result.SessionProperties;
        // Query session status, etc.
    }
}
```

```cpp
void GetCurrentRenderingSessions(ApiHandle<RemoteRenderingClient> client)
{
    client->GetCurrentRenderingSessionsAsync([](Status status, ApiHandle<RenderingSessionPropertiesArrayResult> result) {
        if (status == Status::OK && result->GetErrorCode() == Result::Success)
        {
            std::vector<RenderingSessionProperties> properties;
            result->GetSessionProperties(properties);
        }
        else
        {
            printf("Failed to get current rendering sessions!");
        }
    });
}
```

### <a name="session-apis"></a>APIs de sessão

#### <a name="get-rendering-session-properties"></a>Obter propriedades da sessão de renderização

```cs
async void GetRenderingSessionProperties(RenderingSession session)
{
    RenderingSessionPropertiesResult result = await session.GetPropertiesAsync();
    if (result.ErrorCode == Result.Success)
    {
        RenderingSessionProperties properties = result.SessionProperties;
    }
    else
    {
        Console.WriteLine("Failed to get properties of session!");
    }
}
```

```cpp
void GetRenderingSessionProperties(ApiHandle<RenderingSession> session)
{
    session->GetPropertiesAsync([](Status status, ApiHandle<RenderingSessionPropertiesResult> result) {
        if (status == Status::OK && result->GetErrorCode() == Result::Success)
        {
            RenderingSessionProperties properties = result->GetSessionProperties();
        }
        else
        {
            printf("Failed to get properties of session!");
        }
    });
}
```

#### <a name="update-rendering-session"></a>Atualizar sessão de renderização

```cs
async void UpdateRenderingSession(RenderingSession session, int updatedLeaseInMinutes)
{
    SessionContextResult result = await session.RenewAsync(
        new RenderingSessionUpdateOptions(updatedLeaseInMinutes / 60, updatedLeaseInMinutes % 60));
    if (result.ErrorCode == Result.Success)
    {
        Console.WriteLine("Rendering session renewed succeeded!");
    }
    else
    {
        Console.WriteLine("Failed to renew rendering session!");
    }
}
```

```cpp
void UpdateRenderingSession(ApiHandle<RenderingSession> session, int updatedLeaseInMinutes)
{
    RenderingSessionUpdateOptions params;
    params.MaxLeaseInMinutes = updatedLeaseInMinutes;
    session->RenewAsync(params, [](Status status, ApiHandle<SessionContextResult> result) {
        if (status == Status::OK && result->GetErrorCode() == Result::Success)
        {
            printf("Rendering session renewed succeeded!");
        }
        else
        {
            printf("Failed to renew rendering session!");
        }
    });
}
```

#### <a name="stop-rendering-session"></a>Parar a renderização da sessão

```cs
async void StopRenderingSession(RenderingSession session)
{
    SessionContextResult result = await session.StopAsync();
    if (result.ErrorCode == Result.Success)
    {
        Console.WriteLine("Rendering session stopped successfully!");
    }
    else
    {
        Console.WriteLine("Failed to stop rendering session!");
    }
}
```

```cpp
void StopRenderingSession(ApiHandle<RenderingSession> session)
{
    session->StopAsync([](Status status, ApiHandle<SessionContextResult> result) {
        if (status == Status::OK && result->GetErrorCode() == Result::Success)
        {
            printf("Rendering session stopped successfully!");
        }
        else
        {
            printf("Failed to stop rendering session!");
        }
    });
}
```

#### <a name="connect-to-arr-inspector"></a>Conectar-se ao inspetor de ARR

```cs
async void ConnectToArrInspector(RenderingSession session)
{
    string htmlPath = await session.ConnectToArrInspectorAsync();
#if WINDOWS_UWP
    UnityEngine.WSA.Application.InvokeOnUIThread(async () =>
    {
        var file = await Windows.Storage.StorageFile.GetFileFromPathAsync(htmlPath);
        await Windows.System.Launcher.LaunchFileAsync(file);
    }, true);
#else
    InvokeOnAppThreadAsync(() =>
        {
            System.Diagnostics.Process.Start("file:///" + htmlPath);
        });
#endif
}
```

```cpp
void ConnectToArrInspector(ApiHandle<RenderingSession> session)
{
    session->ConnectToArrInspectorAsync([](Status status, std::string result) {
        if (status == Status::OK)
        {
            // Launch the html file with default browser
            std::string htmlPath = "file:///" + result;
            ShellExecuteA(NULL, "open", htmlPath.c_str(), NULL, NULL, SW_SHOWDEFAULT);
        }
        else
        {
            printf("Failed to connect to ARR inspector!");
        }
    });
}
```

## <a name="next-steps"></a>Próximas etapas

* [Criar uma conta](create-an-account.md)
* [Scripts de exemplo do PowerShell](../samples/powershell-example-scripts.md)

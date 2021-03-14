---
ms.openlocfilehash: b552629c23991880a2f9cfc6f9e96376daecc1a0
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103439294"
---
## <a name="add-managed-identity-to-your-communication-services-solution-net"></a>Adicionar identidade gerenciada à sua solução de serviços de comunicação (.NET)

### <a name="install-the-client-library-packages"></a>Instalar os pacotes de biblioteca de cliente

```console
dotnet add package Azure.Communication.Identity  --version 1.0.0-beta.5
dotnet add package Azure.Communication.Sms  --version 1.0.0-beta.4
dotnet add package Azure.Identity
```

### <a name="use-the-client-library-packages"></a>Usar os pacotes de biblioteca de cliente

Adicione as seguintes `using` diretivas ao seu código para usar as bibliotecas de cliente de armazenamento do Azure e identidade do Azure.

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Sms;
using Azure.Core;
```

Os exemplos a seguir estão usando o [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential). Essa credencial é adequada para ambientes de produção e desenvolvimento.

`AZURE_CLIENT_SECRET``AZURE_CLIENT_ID`e as `AZURE_TENANT_ID` variáveis de ambiente são necessárias para criar um `DefaultAzureCredential` objeto. Para criar um aplicativo registrado no ambiente de desenvolvimento e configurar variáveis de ambiente, consulte [autorizar o acesso com identidade gerenciada](../managed-identity-from-cli.md).

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Criar uma identidade e emitir um token com identidade gerenciada

O exemplo de código a seguir mostra como criar um objeto de cliente de serviço com tokens de Azure Active Directory.

Em seguida, use o cliente para emitir um token para um novo usuário:

```csharp
     public Response<AccessToken> CreateIdentityAndGetTokenAsync(Uri resourceEndpoint)
     {
          TokenCredential credential = new DefaultAzureCredential();

          // You can find your endpoint and access key from your resource in the Azure portal
          // "https://<RESOURCE_NAME>.communication.azure.com";

          var client = new CommunicationIdentityClient(resourceEndpoint, credential);
          var identityResponse = client.CreateUser();
          var identity = identityResponse.Value;

          var tokenResponse = client.GetToken(identity, scopes: new[] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

### <a name="send-an-sms-with-managed-identity"></a>Enviar um SMS com identidade gerenciada

O exemplo de código a seguir mostra como criar um objeto de cliente de serviço do SMS com identidade gerenciada e, em seguida, usar o cliente para enviar uma mensagem SMS:

```csharp
     public SmsSendResult SendSms(Uri resourceEndpoint, string from, string to, string message)
     {
          TokenCredential credential = new DefaultAzureCredential();
          // You can find your endpoint and access key from your resource in the Azure portal
          // "https://<RESOURCE_NAME>.communication.azure.com";

          SmsClient smsClient = new SmsClient(resourceEndpoint, credential);
          SmsSendResult sendResult = smsClient.Send(
               from: from,
               to: to,
               message: message,
               new SmsSendOptions(enableDeliveryReport: true) // optional
          );

          return sendResult;
      }
```


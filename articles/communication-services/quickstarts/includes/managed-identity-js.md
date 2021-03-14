---
ms.openlocfilehash: 3626ca4cc3e7377f1c6778bc77e5e48ef0dcad0c
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103439035"
---
## <a name="add-managed-identity-to-your-communication-services-solution-js"></a>Adicionar identidade gerenciada à sua solução de serviços de comunicação (JS)

### <a name="install-the-client-library-packages"></a>Instalar os pacotes de biblioteca de cliente

```console
npm install @azure/communication-identity
npm install @azure/communication-common
npm install @azure/communication-sms
npm install @azure/identity
```

### <a name="use-the-client-library-packages"></a>Usar os pacotes de biblioteca de cliente

Adicione as seguintes `import` diretivas ao seu código para usar as bibliotecas de cliente de armazenamento do Azure e identidade do Azure.

```typescript
import { DefaultAzureCredential } from "@azure/identity";
import { CommunicationIdentityClient, CommunicationUserToken } from "@azure/communication-identity";
import { SmsClient, SmsSendRequest } from "@azure/communication-sms";
```

Os exemplos a seguir estão usando o [DefaultAzureCredential](/javascript/api/azure.identity.defaultazurecredential). Essa credencial é adequada para ambientes de produção e desenvolvimento.

Para registrar o aplicativo no ambiente de desenvolvimento e configurar variáveis de ambiente, consulte [autorizar o acesso com identidade gerenciada](../managed-identity-from-cli.md)  

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Criar uma identidade e emitir um token com identidade gerenciada

O exemplo de código a seguir mostra como criar um objeto de cliente de serviço com identidade gerenciada e, em seguida, usar o cliente para emitir um token para um novo usuário:

```JavaScript
export async function createIdentityAndIssueToken(resourceEndpoint: string): Promise<CommunicationUserToken> {
     let credential = new DefaultAzureCredential();
     const client = new CommunicationIdentityClient(resourceEndpoint, credential);
     return await client.createUserAndToken(["chat"]);
}
```

### <a name="send-an-sms-with-managed-identity"></a>Enviar um SMS com identidade gerenciada

O exemplo de código a seguir mostra como criar um objeto de cliente de serviço com identidade gerenciada e, em seguida, usar o cliente para enviar uma mensagem SMS:

```JavaScript
export async function sendSms(resourceEndpoint: string, fromNumber: any, toNumber: any, message: string) {
     let credential = new DefaultAzureCredential();
     const smsClient = new SmsClient(resourceEndpoint, credential);
     const sendRequest: SmsSendRequest = { 
          from: fromNumber, 
          to: [toNumber], 
          message: message 
     };

     const response = await smsClient.send(
          sendRequest, 
          {} //Optional SendOptions
          );
}
```


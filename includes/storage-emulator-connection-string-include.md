---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/28/2020
ms.author: tamram
ms.openlocfilehash: 85cfe3b062d7d9ef3a7bdcf29ef7d2125f8f3ae4
ms.sourcegitcommit: 31d242b611a2887e0af1fc501a7d808c933a6bf6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/29/2020
ms.locfileid: "97812795"
---
O emulador dá suporte a uma única conta fixa e uma chave de autenticação conhecida para autenticação de chave compartilhada. Essa conta e a chave são as únicas credenciais de chave compartilhada permitidas para uso com o emulador. Elas são:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> A chave de autenticação com suporte pelo emulador destina-se apenas ao teste da funcionalidade do seu código de autenticação de cliente. Ela não serve para fins de segurança. Você não pode usar sua conta de armazenamento de produção e a chave com o emulador. Você não deve usar a conta de desenvolvimento com dados de produção.
>
> O emulador dá suporte à conexão somente via HTTP. No entanto, o HTTPS é o protocolo recomendado para acessar os recursos em uma conta de armazenamento de produção do Azure.
>

#### <a name="connect-to-the-emulator-account-using-the-shortcut"></a>Conectar-se à conta do emulador usando o atalho

A maneira mais fácil de se conectar ao emulador de seu aplicativo é configurar uma cadeia de conexão no arquivo de configuração do aplicativo que faz referência ao atalho `UseDevelopmentStorage=true` . O atalho é equivalente à cadeia de conexão completa para o emulador, que especifica o nome da conta, a chave da conta e os pontos de extremidade do emulador para cada um dos serviços de armazenamento do Azure:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
TableEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

O trecho de código .NET a seguir mostra como você pode usar o atalho de um método que usa uma cadeia de conexão. Por exemplo, o construtor [BlobContainerClient (cadeia de caracteres, Cadeia de caracteres)](/dotnet/api/azure.storage.blobs.blobcontainerclient.-ctor#Azure_Storage_Blobs_BlobContainerClient__ctor_System_String_System_String_) usa uma cadeia de conexão.

```csharp
BlobContainerClient blobContainerClient = new BlobContainerClient("UseDevelopmentStorage=true", "sample-container");
blobContainerClient.CreateIfNotExists();
```

Verifique se o emulador está em execução antes de chamar o código no trecho.

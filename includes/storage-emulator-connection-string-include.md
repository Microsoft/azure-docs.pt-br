---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 8c577db3e9f2bff9e86c3a7c37274630f90dd680
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172126"
---
O emulador de armazenamento dá suporte a uma única conta fixa e uma chave de autenticação conhecida para a autenticação da Chave Compartilhada. Essa conta e a chave são as únicas credenciais de Chave Compartilhada permitidas para uso com o emulador de armazenamento. Eles são:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> A chave de autenticação suportada pelo emulador de armazenamento destina-se somente para testar a funcionalidade do seu código de autenticação de cliente. Ela não serve para fins de segurança. Você não pode usar sua conta de armazenamento de produção e a chave com o emulador de armazenamento. Você não deve usar a conta de desenvolvimento com dados de produção.
> 
> O emulador de armazenamento oferece suporte à conexão via HTTP apenas. No entanto, o HTTPS é o protocolo recomendado para acessar os recursos em uma conta de armazenamento de produção do Azure.
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Conectar-se à conta do emulador usando um atalho
A maneira mais fácil de conectar o emulador de armazenamento do seu aplicativo é configurar uma cadeia de conexão no arquivo de configuração do aplicativo que faz referência ao atalho `UseDevelopmentStorage=true`. Aqui está um exemplo de uma cadeia de conexão para o emulador de armazenamento em um arquivo *app.config*: 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="connect-to-the-emulator-account-using-the-well-known-account-name-and-key"></a>Conectar-se à conta do emulador usando o nome de conta e a chave conhecidos
Para criar uma cadeia de conexão que referencia o nome da conta do emulador e a chave, você deve especificar os pontos de extremidade para cada um dos serviços do emulador na cadeia de conexão que você deseja usar. Isso é necessário para que a cadeia de conexão faça referência aos pontos de extremidade do emulador, que são diferentes daqueles para uma conta de armazenamento de produção. Por exemplo, o valor da sua cadeia de conexão terá esta aparência:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

Esse valor é idêntico ao atalho mostrado acima, `UseDevelopmentStorage=true`.

#### <a name="specify-an-http-proxy"></a>Especificar um proxy HTTP
Você também pode especificar um proxy HTTP para usar quando estiver testando seu serviço no emulador de armazenamento. Isso pode ser útil para observar solicitações e respostas HTTP enquanto você estiver depurando operações nos serviços de armazenamento. Para especificar um proxy, adicione a opção `DevelopmentStorageProxyUri` à cadeia de conexão e defina o seu valor para o URI de proxy. Por exemplo, aqui está uma cadeia de conexão que aponta para o emulador de armazenamento e configura um proxy HTTP:

```
UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri
```


---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: e319356d555f26354ea29dc7be068bf6168abb17
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455158"
---
## <a name="local-settings-file"></a>Arquivo de configurações local

O arquivo Settings armazena configurações do aplicativo, cadeias de caracteres de conexão e as configurações usadas por ferramentas de desenvolvimento local. As configurações no arquivo Settings são usadas somente quando em execução localmente. O arquivo de configurações local tem a seguinte estrutura:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*",
    "CORSCredentials": false
  },
  "ConnectionStrings": {
    "SQLConnectionString": "<sqlclient-connection-string>"
  }
}
```

As configurações a seguir são suportadas quando executados localmente:

| Configuração      | DESCRIÇÃO                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | Quando definido como `true`, todos os valores são criptografados usando uma chave do computador local. Usado com `func settings` comandos. O valor padrão é `false`. |
| **`Values`** | Matriz de configurações do aplicativo e cadeias de caracteres de conexão usadas ao executar localmente. Esses pares chave-valor (cadeia de cadeia de caracteres) correspondem às configurações do aplicativo em seu aplicativo de função no Azure, como [ `AzureWebJobsStorage` ]. Vários gatilhos e associações têm uma propriedade que se refere a uma configuração de aplicativo de cadeia de caracteres de conexão, como `Connection` para o [gatilho do armazenamento de Blob](../articles/azure-functions/functions-bindings-storage-blob.md#trigger---configuration). Para essas propriedades, você precisa de uma configuração de aplicativo definida na `Values` matriz. <br/>[`AzureWebJobsStorage`] um aplicativo necessário é a configuração de gatilhos diferentes de HTTP. <br/>Versão 2.x do tempo de execução de funções exige o [`FUNCTIONS_WORKER_RUNTIME`] configuração, que é gerada para seu projeto pelas ferramentas básicas. <br/> Quando você tiver o [emulador de armazenamento do Azure](../articles/storage/common/storage-use-emulator.md) instalado localmente, você pode definir [ `AzureWebJobsStorage` ] para `UseDevelopmentStorage=true` e ferramentas principais usa o emulador. Isso é útil durante o desenvolvimento, mas você deve testar com uma conexão de armazenamento real antes da implantação.<br/> Valores devem ser cadeias de caracteres e não os objetos JSON ou matrizes. Nomes de configuração não podem incluir dois-pontos (`:`) ou um sublinhado duplo (`__`); são reservados pelo tempo de execução.  |
| **`Host`** | As configurações nesta seção personalizam o processo de host do Functions quando executadas localmente. Esses são separadas das configurações de host. JSON, que também se aplicam quando em execução no Azure. |
| **`LocalHttpPort`** | Define a porta padrão usada ao executar o host local do Functions (`func host start` e `func run`). A opção de linha de comando `--port` tem precedência sobre esse valor. |
| **`CORS`** | Define as origens permitidas para [CORS (Compartilhamento de recurso entre origens)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). As origens são fornecidas como uma lista separada por vírgulas, sem espaços. Há suporte para o valor do caractere curinga (\*), que permite solicitações de qualquer origem. |
| **`CORSCredentials`** |  Defini-lo como true para permitir que `withCredentials` solicitações. |
| **`ConnectionStrings`** | Não use esta coleção para as cadeias de conexão usadas por suas associações de função. Essa coleção é usada somente por estruturas que normalmente obtêm cadeias de caracteres de conexão das `ConnectionStrings` seção de configuração do arquivo, como [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). As cadeias de caracteres de conexão neste objeto são adicionadas ao ambiente com o tipo de provedor de [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Os itens nesta coleção não são publicados no Azure com outras configurações de aplicativo. Você deve adicionar explicitamente esses valores para o `Connection strings` coleção de configurações do aplicativo de função. Se você estiver criando um [ `SqlConnection` ](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) em seu código de função, você deve armazenar o valor de cadeia de caracteres de conexão na **configurações do aplicativo** no portal com as outras conexões. |

[`AzureWebJobsStorage`]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage

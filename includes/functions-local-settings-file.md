---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: d944d1d3e9c72471fab2435430a7d13e1770e807
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010430"
---
## <a name="local-settings-file"></a>Arquivo de configurações local

O arquivo local.settings.json armazena as configurações do aplicativo, as cadeias de conexão e as configurações usadas pelas ferramentas de desenvolvimento locais. As configurações no arquivo local.settings.json são usadas somente quando você executa projetos localmente. O arquivo de configurações local tem esta estrutura:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>",
    "AzureWebJobs.HttpExample.Disabled": "true"
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

Essas configurações têm suporte quando você executa projetos localmente:

| Configuração      | Descrição                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | Quando essa configuração é definida como `true`, todos os valores são criptografados com uma chave do computador local. Usado com `func settings` comandos. O valor padrão é `false`. Talvez você queira criptografar o arquivo local.settings.json no computador local quando ele contiver segredos, como cadeias de conexão de serviço. O host descriptografa automaticamente as configurações quando ele é executado. Use o comando `func settings decrypt` antes de tentar ler as configurações criptografadas localmente. |
| **`Values`** | Matriz de configurações de aplicativos e cadeias de conexão usadas quando um projeto é executado localmente. Esses pares chave-valor (cadeia de caracteres– cadeia de caracteres) correspondem às configurações de aplicativos no aplicativo de funções no Azure, como [`AzureWebJobsStorage`]. Muitos gatilhos e associações têm uma propriedade que se refere a uma configuração de aplicativo de cadeia de conexão, como `Connection` para o [gatilho do Armazenamento de blobs](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#configuration). Para essas propriedades, você precisa de uma configuração de aplicativo definida na matriz `Values`. Confira a tabela subsequente para obter uma lista de configurações comumente usadas. <br/>Os valores devem ser cadeias de caracteres e não matrizes ou objetos JSON. Os nomes de configuração não podem incluir dois-pontos (`:`) ou um sublinhado duplo (`__`). Os caracteres de sublinhado duplo são reservados pelo runtime e os dois-pontos são reservados para dar suporte à [injeção de dependência](../articles/azure-functions/functions-dotnet-dependency-injection.md#working-with-options-and-settings). |
| **`Host`** | As configurações nesta seção personalizam o processo de host do Functions quando você executa projetos localmente. Essas configurações são separadas das configurações de host.json, que também se aplicam quando você executa projetos no Azure. |
| **`LocalHttpPort`** | Define a porta padrão usada ao executar o host local do Functions (`func host start` e `func run`). A opção de linha de comando `--port` tem precedência sobre essa configuração. |
| **`CORS`** | Define as origens permitidas para [CORS (Compartilhamento de recurso entre origens)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). As origens são fornecidas como uma lista separada por vírgulas, sem espaços. Há suporte para o valor do caractere curinga (\*), que permite solicitações de qualquer origem. |
| **`CORSCredentials`** |  Quando definido como `true`, `withCredentials` solicitações são permitidas. |
| **`ConnectionStrings`** | Uma coleção. Não use essa coleção para as cadeias de conexão usadas por suas associações de função. Ela só é usada por estruturas que devem obter cadeias de conexão da seção `ConnectionStrings` de um arquivo de configuração, como o [Entity Framework](/ef/ef6/). As cadeias de caracteres de conexão neste objeto são adicionadas ao ambiente com o tipo de provedor de [System.Data.SqlClient](/dotnet/api/system.data.sqlclient). Os itens nesta coleção não são publicados no Azure com outras configurações de aplicativo. É necessário adicionar explicitamente esses valores à coleção `Connection strings` das configurações do aplicativo de funções. Se estiver criando um [`SqlConnection`](/dotnet/api/system.data.sqlclient.sqlconnection) no código de função, você deverá armazenar o valor da cadeia de conexão com as outras conexões nas **Configurações de Aplicativos** no portal. |

As seguintes configurações de aplicativo podem ser incluídas na matriz **`Values`** ao serem executadas localmente:

| Configuração | Valores | Descrição |
|-----|-----|-----|
|**`AzureWebJobsStorage`**| Cadeia de conexão da conta de armazenamento, ou<br/>`UseDevelopmentStorage=true`| Contém a cadeia de conexão para uma conta de Armazenamento do Azure. Necessário quando são usados gatilhos diferentes de HTTP. Para obter mais informações, confira a referência do [`AzureWebJobsStorage`].<br/>Quando o [Emulador de Armazenamento do Azure](../articles/storage/common/storage-use-emulator.md) tiver sido instalado localmente e você configurar [`AzureWebJobsStorage`] em `UseDevelopmentStorage=true`, o Core Tools usará o emulador. O emulador é útil durante o desenvolvimento, mas você deve testar com uma conexão de armazenamento real antes da implantação.| 
|**`AzureWebJobs.<FUNCTION_NAME>.Disabled`**| `true`\|`false` | Para desabilitar uma função durante a execução local, adicione `"AzureWebJobs.<FUNCTION_NAME>.Disabled": "true"` à coleção, em que `<FUNCTION_NAME>` é o nome da função. Para saber mais, confira [Como desabilitar funções no Azure Functions](../articles/azure-functions/disable-function.md#localsettingsjson) |
|**`FUNCTIONS_WORKER_RUNTIME`** | `dotnet`<br/>`node`<br/>`java`<br/>`powershell`<br/>`python`| Indica a linguagem de destino do runtime do Functions. Necessário para a versão 2.x e versões superiores do runtime do Functions. Essa configuração é gerada para seu projeto pelo Core Tools. Para saber mais, confira a referência do [`FUNCTIONS_WORKER_RUNTIME`](../articles/azure-functions/functions-app-settings.md#functions_worker_runtime).|
| **`FUNCTIONS_WORKER_RUNTIME_VERSION`** | `~7` |Indica que o PowerShell 7 será usado durante a execução local. Se não estiver definido, o PowerShell Core 6 será usado. Essa configuração é usada apenas durante a execução local. Durante a execução no Azure, a versão do runtime do PowerShell é determinada pela configuração de site do `powerShellVersion`, que pode ser [definida no portal](../articles/azure-functions/functions-reference-powershell.md#changing-the-powershell-version). | 

[`AzureWebJobsStorage`]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
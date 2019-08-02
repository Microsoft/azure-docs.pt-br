---
title: Adicionar uma associação de fila do Armazenamento do Azure à sua função do Python
description: Saiba como adicionar uma associação de saída da fila do Armazenamento do Azure à sua função do Python usando a CLI do Azure e o Functions Core Tools.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 04/24/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: c2565a5549cbca08b987883e5905f09070b5ab2c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443194"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Adicionar uma associação de fila do Armazenamento do Azure à sua função do Python

O Azure Functions lhe permite conectar os serviços do Azure e outros recursos às funções sem precisar escrever seu próprio código de integração. Essas *associações*, que representam a entrada e a saída, são declaradas na definição de função. Dados de associações são fornecidos à função como parâmetros. Um gatilho é um tipo especial de associação de entrada. Embora uma função tenha apenas um gatilho, ela pode ter várias associações de entrada e de saída. Para saber mais, confira [Conceitos de gatilhos e de associações do Azure Functions](functions-triggers-bindings.md).

Este artigo mostra como integrar a função criada no [artigo de início rápido anterior](functions-create-first-function-python.md) com uma fila de Armazenamento do Azure. A associação de saída que você adiciona a essa função escreve dados da solicitação HTTP em uma mensagem na fila. 

A maioria das associações requer uma cadeia de conexão armazenada que o Functions usa para acessar o serviço vinculado. Para facilitar, use a Conta de armazenamento que você criou com o seu aplicativo de funções. A conexão com essa conta já está armazenada em uma configuração de aplicativo chamada `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este artigo, conclua as etapas na [parte 1 do início rápido do Python](functions-create-first-function-python.md).

## <a name="download-the-function-app-settings"></a>Baixar as configurações do aplicativo de funções

No artigo de início rápido anterior, você criou um aplicativo de funções no Azure, juntamente com a Conta de armazenamento necessária. A cadeia de conexão dessa conta é armazenada com segurança nas configurações do aplicativo no Azure. Neste artigo, você escreverá mensagens em uma Fila de armazenamento na mesma conta. Para se conectar à sua Conta de armazenamento ao executar a função localmente, é necessário baixar as configurações do aplicativo para o arquivo local.settings.json. Execute o seguinte comando do Azure Functions Core Tools para baixar as configurações para local.settings.json, substituindo `<APP_NAME>` pelo nome do seu aplicativo de funções do artigo anterior:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Talvez seja necessário entrar em sua conta do Azure.

> [!IMPORTANT]  
> Como ela contém segredos, o arquivo local.settings.json nunca é publicado e deve ser excluído do controle do código-fonte.

É necessário ter o valor `AzureWebJobsStorage`, que é a cadeia de conexão da Conta de armazenamento. Use esta conexão para verificar se a associação de saída funciona conforme o esperado.

## <a name="enable-extension-bundles"></a>Habilitar pacotes de extensão

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Agora, você pode adicionar uma associação de saída do armazenamento ao seu projeto.

## <a name="add-an-output-binding"></a>Adicionar uma associação de saída

No Functions, cada tipo de associação requer que um `direction`, `type` e um `name` exclusivo seja definido no arquivo functions.json. Dependendo do tipo de associação, outras propriedades podem ser necessárias. A [configuração de saída da fila](functions-bindings-storage-queue.md#output---configuration) descreve os campos obrigatórios para uma associação de fila do Armazenamento do Azure.

Para criar uma associação, adicione um objeto de configuração de associação ao arquivo `function.json`. Edite o arquivo function.json em sua pasta HttpTrigger para adicionar um objeto à matriz `bindings` que tem as seguintes propriedades:

| Propriedade | Valor | DESCRIÇÃO |
| -------- | ----- | ----------- |
| **`name`** | `msg` | Nome que identifica o parâmetro de associação referenciado em seu código. |
| **`type`** | `queue` | A associação é uma associação de fila do Armazenamento do Azure. |
| **`direction`** | `out` | A associação é uma associação de saída. |
| **`queueName`** | `outqueue` | O nome da fila na qual a associação escreve. Quando o *queueName* não existe, a associação o cria no primeiro uso. |
| **`connection`** | `AzureWebJobsStorage` | O nome de uma configuração de aplicativo que contém a cadeia de conexão da Conta de armazenamento. A configuração `AzureWebJobsStorage` contém a cadeia de conexão para a Conta de armazenamento criada com o aplicativo de funções. |

Seu arquivo function.json agora deve ter a aparência do exemplo a seguir:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
  {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

## <a name="add-code-that-uses-the-output-binding"></a>Adicionar o código que usa a associação de saída

Depois que ele estiver configurado, será possível começar a usar o `name` da associação para acessá-lo como um atributo de método na assinatura de função. No exemplo a seguir, `msg` é uma instância do [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest).

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> str:

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        msg.set(name)
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```

Ao usar uma associação de saída, não é necessário usar o código do SDK do Armazenamento do Azure para se autenticar, para obter uma referência de fila ou para escrever dados. O tempo de execução do Functions e a associação de saída da fila fazem essas tarefas para você.

## <a name="run-the-function-locally"></a>Executar a função localmente

Como anteriormente, use o seguinte comando para iniciar o tempo de execução do Functions localmente:

```bash
func host start
```

> [!NOTE]  
> Como o artigo anterior exigia que você habilitasse os pacotes de extensões no host.json, a [Extensão de associação de armazenamento](functions-bindings-storage-blob.md#packages---functions-2x) foi baixada e instalada para você durante a inicialização, juntamente com outras extensões de associação da Microsoft.

Copie a URL da função `HttpTrigger` da saída do tempo de execução de função e cole-a na barra de endereços do navegador. Acrescente o valor de cadeia de consulta `?name=<yourname>` a essa URL e execute a solicitação. Você deve ver a mesma resposta no navegador como você viu no artigo anterior.

Dessa vez, a associação de saída também cria uma fila denominada `outqueue` em sua Conta de armazenamento e adiciona uma mensagem com essa mesma cadeia de caracteres.

Em seguida, use a CLI do Azure para exibir a nova fila e verifique se uma mensagem foi adicionada. Também é possível exibir sua fila usando o [Gerenciador de Armazenamento do Microsoft Azure][Azure Storage Explorer] ou no [portal do Azure](https://portal.azure.com).

### <a name="set-the-storage-account-connection"></a>Definir a conexão da Conta de armazenamento

Abra o arquivo local.settings.json e copie o valor de `AzureWebJobsStorage`, que é a cadeia de conexão da Conta de armazenamento. Defina a variável de ambiente `AZURE_STORAGE_CONNECTION_STRING` como a cadeia de conexão usando o seguinte comando do Bash:

```azurecli-interactive
export AZURE_STORAGE_CONNECTION_STRING=<STORAGE_CONNECTION_STRING>
```

Com a cadeia de conexão definida na variável de ambiente `AZURE_STORAGE_CONNECTION_STRING`, é possível acessar sua Conta de armazenamento sem ter que fornecer a autenticação toda vez.

### <a name="query-the-storage-queue"></a>Consultar a Fila de armazenamento

É possível usar o comando [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) para exibir as Filas de armazenamento em sua conta, como no exemplo a seguir:

```azurecli-interactive
az storage queue list --output tsv
```

A saída desse comando inclui uma fila denominada `outqueue`, que é a fila que foi criada quando a função foi executada.

Em seguida, use o comando [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) para exibir as mensagens nesta fila, como no exemplo a seguir.

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

A cadeia de caracteres retornada deve ser a mesma que a mensagem enviada para testar a função.

> [!NOTE]  
> O exemplo anterior decodifica a cadeia de caracteres retornada de base64. Isso ocorre porque as associações de Armazenamento de fila gravam e leem do Armazenamento do Azure como [cadeias de caracteres base64](functions-bindings-storage-queue.md#encoding).

Agora, chegou a hora de republicar o aplicativo de funções atualizado no Azure.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

Novamente, é possível usar cURL ou um navegador para testar a função implantada. Como antes, acrescente a cadeia de caracteres de consulta `&name=<yourname>` à URL, como mostrado no exemplo a seguir:

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

É possível [Examinar a Mensagem da fila de armazenamento](#query-the-storage-queue) para verificar se a associação de saída gera novamente uma nova mensagem na fila.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Você atualizou sua função disparada por HTTP para gravar dados em uma Fila de armazenamento. Para saber mais sobre como desenvolver o Azure Functions usando o Python, confira o [Guia do desenvolvedor de Python para o Azure Functions](functions-reference-python.md) e [Gatilhos e associações do Azure Functions](functions-triggers-bindings.md).

Em seguida, você deve habilitar o monitoramento do Application Insights para seu aplicativo de funções:

> [!div class="nextstepaction"]
> [Habilitar a integração do Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/
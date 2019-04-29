---
title: Referência de desenvolvedor do Python para o Azure Functions
description: Saiba como desenvolver funções usando Python
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
keywords: azure functions, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor, python
ms.service: azure-functions
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/16/2018
ms.author: glenga
ms.openlocfilehash: 28f2b395c7f9be1b194b500ef20456be8ff405b0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61021254"
---
# <a name="azure-functions-python-developer-guide"></a>Guia do desenvolvedor de Python para o Azure Functions

Este artigo é uma introdução ao desenvolvimento do Azure Functions usando Python. O conteúdo abaixo pressupõe que você já tenha lido o [Guia de desenvolvedores do Azure Functions](functions-reference.md).

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

## <a name="programming-model"></a>Modelo de programação

Uma função do Azure deve ser um método sem estado no script de Python que processa a entrada e produz a saída. Por padrão, o tempo de execução espera que ela seja implementada como um método global chamado `main()` no arquivo `__init__.py`.

Você pode alterar a configuração padrão especificando as propriedades `scriptFile` e `entryPoint` no arquivo `function.json`. Por exemplo, o _function.json_ a seguir diz ao tempo de execução para usar o método _customentry()_ no arquivo _main.py_ como o ponto de entrada para sua função do Azure.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  ...
}
```

Os dados de gatilhos e associações estão vinculados à função por meio de atributos de método usando a propriedade `name` definida no arquivo de configuração `function.json`. Por exemplo, o _function.json_ a seguir descreve uma função simples disparada por uma solicitação HTTP denominada `req`:

```json
{
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

O arquivo `__init__.py` contém o seguinte código de função:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Opcionalmente, você também pode declarar os tipos de parâmetro e de retorno na função usando anotações de tipo do Python. Por exemplo, a mesma função pode ser escrita usando anotações, da seguinte maneira:

```python
import azure.functions

def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```  

Use as anotações do Python incluídas no pacote [azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python) para associar a entrada e as saídas a seus métodos. 

## <a name="folder-structure"></a>Estrutura de pastas

A estrutura de pastas para um projeto Python do Functions é semelhante à seguinte:

```
 FunctionApp
 | - MyFirstFunction
 | | - __init__.py
 | | - function.json
 | - MySecondFunction
 | | - __init__.py
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.py
 | | - mySecondHelperFunction.py
 | - host.json
 | - requirements.txt
 | - extensions.csproj
 | - bin
```

Há um arquivo [host.json](functions-host-json.md) compartilhado que pode ser usado para configurar o aplicativo de funções. Cada função possui seu próprio arquivo de código e arquivo de configuração de associação (function.json). 

O código compartilhado deve ser mantido em uma pasta separada. Para fazer referência a módulos na pasta SharedCode, você pode usar a seguinte sintaxe:

```
from ..SharedCode import myFirstHelperFunction
```

As extensões de associação usadas pelo tempo de execução do Functions são definidas no arquivo `extensions.csproj`, com os arquivos de biblioteca reais na pasta `bin`. Ao desenvolver localmente, você deverá [registrar as extensões de associação](./functions-bindings-register.md#local-development-azure-functions-core-tools) usando o Azure Functions Core Tools. 

Ao implantar um projeto do Functions em seu aplicativo de funções no Azure, todo o conteúdo da pasta FunctionApp deverá ser incluído no pacote, mas não a pasta em si.

## <a name="inputs"></a>Entradas

As entradas são divididas em duas categorias no Azure Functions: entrada do gatilho e entrada adicional. Embora elas sejam diferentes em `function.json`, seu uso é idêntico no código Python. Como exemplo, vejamos o snippet de código a seguir:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "route": "items/{id}"
    },
    {
      "name": "obj",
      "direction": "in",
      "type": "blob",
      "path": "samples/{id}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```python
import azure.functions as func
import logging

def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

Quando a função é invocada, a solicitação HTTP é transmitida para a função como `req`. Uma entrada será recuperada do Armazenamento de Blobs do Azure com base na _id_ na URL da rota e disponibilizada como `obj` no corpo da função.

## <a name="outputs"></a>outputs

A saída pode ser expressa em parâmetros de saída e em valores retornados. Se houver apenas uma saída, recomendamos usar o valor retornado. Para múltiplas saídas, você precisará usar parâmetros de saída.

Para usar o valor retornado de uma função como o valor de uma associação de saída, a propriedade `name` da associação deve ser definida como `$return` em `function.json`.

Para produzir várias saídas, use o método `set()` fornecido pela interface `azure.functions.Out` a fim de atribuir um valor para a associação. Por exemplo, a função a seguir pode enviar uma mensagem para uma fila e também retornar uma resposta HTTP.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "msg",
      "direction": "out",
      "type": "queue",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

```python
import azure.functions as func

def main(req: func.HttpRequest,
         msg: func.Out[func.QueueMessage]) -> str:

    message = req.params.get('body')
    msg.set(message)
    return message
```

## <a name="logging"></a>Registro em log

O acesso ao agente de tempo de execução do Azure Functions está disponível por meio de um manipulador [`logging`](https://docs.python.org/3/library/logging.html#module-logging) raiz no seu aplicativo de funções. Esse agente é vinculado ao Application Insights e permite sinalizar avisos e erros encontrados durante a execução da função.

O exemplo a seguir registra uma mensagem de informações quando a função é invocada por meio de um gatilho HTTP.

```python
import logging

def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

Há outros métodos de registro em log disponíveis que permitem a gravação no console em níveis de rastreamento diferentes:

| Método                 | DESCRIÇÃO                                |
| ---------------------- | ------------------------------------------ |
| logging.**critical(_message_)**   | Grava uma mensagem com nível CRÍTICO no agente raiz.  |
| logging.**error(_message_)**   | Grava uma mensagem com nível ERRO no agente raiz.    |
| logging.**warning(_message_)**    | Grava uma mensagem com nível AVISO no agente raiz.  |
| logging.**info(_message_)**    | Grava uma mensagem com nível INFORMAÇÕES no agente raiz.  |
| logging.**debug(_message_)** | Grava uma mensagem com nível DEPURAR no agente raiz.  |

## <a name="importing-shared-code-into-a-function-module"></a>Importando o código compartilhado para um módulo de função

Os módulos de Python publicados juntamente com os módulos de função devem ser importados usando a sintaxe de importação relativa:

```python
from . import helpers  # Use more dots to navigate up the folder structure.
def main(req: func.HttpRequest):
    helpers.process_http_request(req)
```

Como alternativa, coloque o código compartilhado em um pacote autônomo, publique-o em uma instância PyPI pública ou particular e especifique-o como uma dependência normal.

## <a name="async"></a>Assíncrono

Como apenas um único processo de Python pode existir por aplicativo de funções, é recomendável implementar a função do Azure como uma rotina conjunta assíncrona usando a instrução `async def`.

```python
# Will be run with asyncio directly
async def main():
    await some_nonblocking_socket_io_op()
```

Se a função main() é síncrona (sem qualificador `async`), podemos executá-la automaticamente em um pool de threads `asyncio`.

```python
# Would be run in an asyncio thread-pool
def main():
    some_blocking_socket_io()
```

## <a name="context"></a>Contexto

Para obter o contexto de invocação de uma função durante a execução, inclua o argumento `context` em sua assinatura. 

Por exemplo: 

```python
import azure.functions

def main(req: azure.functions.HttpRequest,
            context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

A classe **Context** tem os seguintes métodos:

`function_directory`  
O diretório no qual a função está em execução.

`function_name`  
Nome da função.

`invocation_id`  
ID da invocação de função atual.

## <a name="python-version-and-package-management"></a>Versão do Python e gerenciamento de pacote

Atualmente, o Azure Functions dá suporte apenas a Python 3.6.x (distribuição oficial do CPython).

Ao desenvolver localmente usando o Azure Functions Core Tools ou o Visual Studio Code, adicione os nomes e as versões dos pacotes necessários para os arquivos `requirements.txt` e instale-os usando `pip`.

Por exemplo, o arquivo de requisitos e comando pip a seguir podem ser usados para instalar o pacote `requests` do PyPI.

```bash
pip install requests
```

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

Quando estiver pronto para publicação, verifique se todas as dependências estão listadas no arquivo `requirements.txt`, localizado na raiz do diretório do projeto. Para executar o Azure Functions com êxito, o arquivo de requisitos deverá conter, no mínimo, os seguintes pacotes:

```txt
azure-functions
azure-functions-worker
grpcio==1.14.1
grpcio-tools==1.14.1
protobuf==3.6.1
six==1.11.0
```

## <a name="publishing-to-azure"></a>Publicando no Azure

Se você estiver usando um pacote que precisa de um compilador e não dá suporte à instalação de rodas do PyPI compatíveis com manylinux, a publicação no Azure falhará com o seguinte erro: 

```
There was an error restoring dependencies.ERROR: cannot install <package name - version> dependency: binary dependencies without wheels are not supported.  
The terminal process terminated with exit code: 1
```

Para criar e configurar os binários necessários automaticamente, [instale o Docker](https://docs.docker.com/install/) em seu computador local e execute o comando a seguir para publicar usando o [Azure Functions Core Tools](functions-run-local.md#v2) (func). Lembre-se de substituir `<app name>` pelo nome do aplicativo de funções no Azure. 

```bash
func azure functionapp publish <app name> --build-native-deps
```

Nos bastidores, o Core Tools usará Docker para executar a imagem [mcr.microsoft.com/azure-functions/python](https://hub.docker.com/r/microsoft/azure-functions/) como um contêiner em seu computador local. Usando esse ambiente, ele compilará e instalará os módulos necessários da distribuição de origem antes de empacotá-las para a implantação final no Azure.

> [!NOTE]
> O Core Tools (func) usa o programa PyInstaller para congelar o código e as dependências do usuário em um único executável autônomo para ser executado no Azure. Essa funcionalidade está atualmente em versão prévia e pode não se estender a todos os tipos de pacotes de Python. Se você não puder importar os módulos, tente publicá-los novamente usando a opção `--no-bundler`. 
> ```
> func azure functionapp publish <app_name> --build-native-deps --no-bundler
> ```
> Se você continuar a ter problemas, informe-nos [abrindo um caso de problema](https://github.com/Azure/azure-functions-core-tools/issues/new) e incluindo uma descrição do problema. 


Para criar suas dependências e publicar usando um sistema de CI/CD (integração contínua/entrega contínua), você pode usar um [Pipeline do Azure](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=vsts) ou um [script personalizado de CI do Travis](https://docs.travis-ci.com/user/deployment/script/). 

Abaixo temos um exemplo de script `azure-pipelines.yml` para o processo de compilação e publicação.
```yml
pool:
  vmImage: 'Ubuntu 16.04'

steps:
- task: NodeTool@0
  inputs:
    versionSpec: '8.x'

- script: |
    set -e
    echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ wheezy main" | sudo tee /etc/apt/sources.list.d/azure-cli.list
    curl -L https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
    sudo apt-get install -y apt-transport-https
    echo "install Azure CLI..."
    sudo apt-get update && sudo apt-get install -y azure-cli
    npm i -g azure-functions-core-tools --unsafe-perm true
    echo "installing dotnet core"
    curl -sSL https://dot.net/v1/dotnet-install.sh | bash /dev/stdin --channel 2.0
- script: |
    set -e
    az login --service-principal --username "$(APP_ID)" --password "$(PASSWORD)" --tenant "$(TENANT_ID)" 
    func settings add FUNCTIONS_WORKER_RUNTIME python
    func extensions install
    func azure functionapp publish $(APP_NAME) --build-native-deps
```

Abaixo temos um exemplo de script `.travis.yaml` para o processo de compilação e publicação.

```yml
sudo: required

language: node_js

node_js:
  - "8"

services:
  - docker

before_install:
  - echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ wheezy main" | sudo tee /etc/apt/sources.list.d/azure-cli.list
  - curl -L https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
  - sudo apt-get install -y apt-transport-https
  - sudo apt-get update && sudo apt-get install -y azure-cli
  - npm i -g azure-functions-core-tools --unsafe-perm true


script:
  - az login --service-principal --username "$APP_ID" --password "$PASSWORD" --tenant "$TENANT_ID"
  - az account get-access-token --query "accessToken" | func azure functionapp publish $APP_NAME --build-native-deps

```

## <a name="known-issues-and-faq"></a>Problemas conhecidos e perguntas frequentes

Todos os problemas conhecidos e solicitações de recursos são controlados usando a lista [Problemas do GitHub](https://github.com/Azure/azure-functions-python-worker/issues). Se você tiver um problema e não for possível localizá-lo no GitHub, abra um novo problema e inclua uma descrição detalhada dele.

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte os recursos a seguir:

* [Práticas recomendadas para o Azure Functions](functions-best-practices.md)
* [Gatilhos e associações de Azure Functions](functions-triggers-bindings.md)
* [Associações do armazenamento de blobs](functions-bindings-storage-blob.md)
* [Associações HTTP e webhook](functions-bindings-http-webhook.md)
* [Associações de Armazenamento de Filas](functions-bindings-storage-queue.md)
* [Gatilho de temporizador](functions-bindings-timer.md)

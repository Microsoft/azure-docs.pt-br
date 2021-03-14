---
title: Referência de desenvolvedor do Python para o Azure Functions
description: Saiba como desenvolver funções usando Python
ms.topic: article
ms.date: 11/4/2020
ms.custom: devx-track-python
ms.openlocfilehash: 3eb3b3b015f401e872a879c46ec6f8c69df5f87f
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102455409"
---
# <a name="azure-functions-python-developer-guide"></a>Guia do desenvolvedor de Python para o Azure Functions

Este artigo é uma introdução ao desenvolvimento do Azure Functions usando Python. O conteúdo abaixo pressupõe que você já tenha lido o [Guia de desenvolvedores do Azure Functions](functions-reference.md).

Como desenvolvedor de Python, você também pode estar interessado em um dos seguintes artigos:

| Introdução | Conceitos| Cenários/exemplos |
| -- | -- | -- | 
| <ul><li>[Função do Python usando Visual Studio Code](./create-first-function-vs-code-csharp.md?pivots=programming-language-python)</li><li>[Função do Python com terminal/prompt de comando](./create-first-function-cli-csharp.md?pivots=programming-language-python)</li></ul> | <ul><li>[Guia do desenvolvedor](functions-reference.md)</li><li>[Opções de hospedagem](functions-scale.md)</li><li>[&nbsp;Considerações sobre desempenho](functions-best-practices.md)</li></ul> | <ul><li>[Classificação de imagens com o PyTorch](machine-learning-pytorch.md)</li><li>[Exemplo de automação do Azure](/samples/azure-samples/azure-functions-python-list-resource-groups/azure-functions-python-sample-list-resource-groups/)</li><li>[Aprendizado de máquina com o TensorFlow](functions-machine-learning-tensorflow.md)</li><li>[Procurar exemplos do Python](/samples/browse/?products=azure-functions&languages=python)</li></ul> |

> [!NOTE]
> Embora você possa [desenvolver seu Azure Functions baseado em Python localmente no Windows, o](create-first-function-vs-code-python.md#run-the-function-locally)Python só tem suporte em um plano de hospedagem baseado em Linux quando executado no Azure. Consulte a lista de combinações de [sistema operacional/tempo de execução](functions-scale.md#operating-systemruntime) com suporte.

## <a name="programming-model"></a>Modelo de programação

O Azure Functions espera que a função seja um método sem estado no script de Python que processa a entrada e produz a saída. Por padrão, o runtime espera que ela seja implementada como um método global chamado `main()` no arquivo `__init__.py`. Você também pode [especificar um ponto de entrada alternativo](#alternate-entry-point).

Os dados de gatilhos e associações estão vinculados à função por meio de atributos de método que usam a propriedade `name` definida no arquivo *function.json*. Por exemplo, o _function.json_ abaixo descreve uma função simples disparada por uma solicitação HTTP denominada `req`:

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

Com base nessa definição, o arquivo `__init__.py` que contém o código de função pode ser semelhante a este exemplo:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Você também pode declarar os tipos de atributo e de retorno na função por meio de anotações de tipo do Python. Isso ajuda você a usar o IntelliSense e os recursos de preenchimento automático fornecidos por vários editores de código Python.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Use as anotações do Python incluídas no pacote [azure.functions.*](/python/api/azure-functions/azure.functions) para associar a entrada e as saídas a seus métodos.

## <a name="alternate-entry-point"></a>Ponto de entrada alternativo

Você poderá alterar o comportamento padrão de uma função se especificar opcionalmente as propriedades `scriptFile` e `entryPoint` no arquivo *function.json*. Por exemplo, o _function.json_ a seguir diz ao runtime para usar o método `customentry()` no arquivo _main.py_ como o ponto de entrada da sua função do Azure.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  "bindings": [
      ...
  ]
}
```

## <a name="folder-structure"></a>Estrutura de pastas

A estrutura de pastas recomendada a um projeto Python do Functions é semelhante a este exemplo:

```
 <project_root>/
 | - .venv/
 | - .vscode/
 | - my_first_function/
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - my_second_function/
 | | - __init__.py
 | | - function.json
 | - shared_code/
 | | - __init__.py
 | | - my_first_helper_function.py
 | | - my_second_helper_function.py
 | - tests/
 | | - test_my_second_function.py
 | - .funcignore
 | - host.json
 | - local.settings.json
 | - requirements.txt
 | - Dockerfile
```
A pasta principal do projeto (<project_root>) pode conter os seguintes arquivos:

* *local.settings.json*: usado para armazenar as configurações do aplicativo e as cadeias de conexão durante a execução local. Esse arquivo não é publicado no Azure. Para saber mais, confira [local.settings.file](functions-run-local.md#local-settings-file).
* *requirements.txt*: contém a lista de pacotes do Python que o sistema instala ao publicar no Azure.
* *host.json*: contém opções de configuração global que afetam todas as funções em um aplicativo de funções. Esse arquivo é publicado do Azure. Nem todas as opções têm suporte quando executadas localmente. Para saber mais, confira [host.json](functions-host-json.md).
* *. vscode/*: (opcional) contém a configuração do Store vscode. Para saber mais, consulte [configuração de VSCode](https://code.visualstudio.com/docs/getstarted/settings).
* *. venv/*: (opcional) contém um ambiente virtual Python usado pelo desenvolvimento local.
* *Dockerfile*: (opcional) usado ao publicar seu projeto em um [contêiner personalizado](functions-create-function-linux-custom-image.md).
* *tests/*: (opcional) contém os casos de teste do seu aplicativo de funções.
* *. funcignore*: (opcional) declara os arquivos que não devem ser publicados no Azure. Normalmente, esse arquivo contém `.vscode/` para ignorar a configuração do editor, `.venv/` ignorar o ambiente virtual Python local, `tests/` ignorar casos de teste e `local.settings.json` impedir que as configurações do aplicativo local sejam publicadas.

Cada função possui seu próprio arquivo de código e arquivo de configuração de associação (function.json).

Ao implantar seu projeto em um aplicativo de funções no Azure, todo o conteúdo da pasta principal do projeto (*<project_root>*) deve ser incluído no pacote, mas não a própria pasta, o que significa que `host.json` deve estar na raiz do pacote. Recomendamos que você mantenha seus testes em uma pasta junto com outras funções, neste exemplo `tests/` . Para obter mais informações, confira [Teste de Unidade](#unit-testing).

## <a name="import-behavior"></a>Comportamento de importação

Você pode importar módulos em seu código de função usando referências absolutas e relativas. Com base na estrutura de pastas mostrada acima, as importações a seguir funcionam de dentro do arquivo de função *<project_root> \Meus \_ primeira \_ função \\ _ \_ init \_ \_ . py*:

```python
from shared_code import my_first_helper_function #(absolute)
```

```python
import shared_code.my_second_helper_function #(absolute)
```

```python
from . import example #(relative)
```

> [!NOTE]
>  A *shared_code/* pasta precisa conter um \_ \_ arquivo init \_ \_ . py para marcá-lo como um pacote do Python ao usar a sintaxe de importação absoluta.

A importação de aplicativo a seguir \_ \_ \_ \_ e além da importação relativa de nível superior foram preteridas, pois não há suporte para ele no verificador de tipo estático e não há suporte para as estruturas de teste do Python:

```python
from __app__.shared_code import my_first_helper_function #(deprecated __app__ import)
```

```python
from ..shared_code import my_first_helper_function #(deprecated beyond top-level relative import)
```

## <a name="triggers-and-inputs"></a>Gatilhos e entradas

As entradas são divididas em duas categorias no Azure Functions: entrada do gatilho e entrada adicional. Embora elas sejam diferentes no arquivo `function.json`, seu uso é idêntico no código Python.  As cadeias de conexão ou os segredos para fontes de entrada e gatilho são mapeados para valores no arquivo `local.settings.json` quando são executados localmente, e as configurações do aplicativo quando são executados no Azure.

Por exemplo, o código abaixo demonstra a diferença entre os dois:

```json
// function.json
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

```json
// local.settings.json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "<azure-storage-connection-string>"
  }
}
```

```python
# __init__.py
import azure.functions as func
import logging


def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

Quando a função é invocada, a solicitação HTTP é transmitida para a função como `req`. Uma entrada será recuperada do Armazenamento de Blobs do Azure com base na _ID_ na URL da rota e disponibilizada como `obj` no corpo da função.  Aqui, a conta de armazenamento especificada é a cadeia de conexão encontrada na configuração do aplicativo AzureWebJobsStorage, que é a mesma conta de armazenamento usada pelo aplicativo de funções.


## <a name="outputs"></a>outputs

A saída pode ser expressa em parâmetros de saída e em valores retornados. Se houver apenas uma saída, recomendamos usar o valor retornado. Para múltiplas saídas, você precisará usar parâmetros de saída.

Para usar o valor retornado de uma função como o valor de uma associação de saída, a propriedade `name` da associação deve ser definida como `$return` em `function.json`.

Para produzir várias saídas, use o método `set()` fornecido pela interface [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out) a fim de atribuir um valor à associação. Por exemplo, a função a seguir pode enviar uma mensagem para uma fila e também retornar uma resposta HTTP.

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

O acesso ao agente do Azure Functions runtime está disponível por meio de um manipulador [`logging`](https://docs.python.org/3/library/logging.html#module-logging) raiz no seu aplicativo de funções. Esse agente é vinculado ao Application Insights e permite sinalizar avisos e erros encontrados durante a execução da função.

O exemplo a seguir registra uma mensagem de informações quando a função é invocada por meio de um gatilho HTTP.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

Há outros métodos de registro em log disponíveis que permitem a gravação no console em níveis de rastreamento diferentes:

| Método                 | Descrição                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | Grava uma mensagem com nível CRÍTICO no agente raiz.  |
| **`error(_message_)`**   | Grava uma mensagem com nível ERRO no agente raiz.    |
| **`warning(_message_)`**    | Grava uma mensagem com nível AVISO no agente raiz.  |
| **`info(_message_)`**    | Grava uma mensagem com nível INFORMAÇÕES no agente raiz.  |
| **`debug(_message_)`** | Grava uma mensagem com nível DEPURAR no agente raiz.  |

Para saber mais sobre como registrar em log, confira [Monitorar o Azure Functions](functions-monitoring.md).

## <a name="http-trigger-and-bindings"></a>Gatilhos e associações HTTP

O gatilho HTTP é definido no function.jsno arquivo. O `name` da associação deve corresponder ao parâmetro nomeado na função.
Nos exemplos anteriores, um nome de associação `req` é usado. Esse parâmetro é um objeto [HttpRequest], e um objeto [HttpResponse] é retornado.

No objeto [HttpRequest], você pode obter cabeçalhos de solicitação, parâmetros de consulta, parâmetros de rota e o corpo da mensagem.

O exemplo a seguir é do [modelo de gatilho HTTP para Python](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python).

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    headers = {"my-http-header": "some-value"}

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!", headers=headers)
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             headers=headers, status_code=400
        )
```

Nessa função, o valor do parâmetro de consulta `name` é obtido do parâmetro `params` do objeto [HttpRequest]. O corpo da mensagem codificada em JSON é lido pelo método `get_json`.

Da mesma forma, você pode definir `status_code` e `headers` para a mensagem de resposta no objeto retornado [HttpResponse].

## <a name="scaling-and-performance"></a>Dimensionamento e desempenho

Para obter as práticas recomendadas de dimensionamento e desempenho para aplicativos de funções do Python, consulte o [artigo escala e desempenho do Python](python-scale-performance-reference.md).

## <a name="context"></a>Contexto

Para obter o contexto de invocação de uma função durante a execução, inclua o argumento [`context`](/python/api/azure-functions/azure.functions.context) em sua assinatura.

Por exemplo:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

a classe [**Context**](/python/api/azure-functions/azure.functions.context) tem os seguintes atributos de cadeia de caracteres:

`function_directory` O diretório no qual a função está em execução.

`function_name` O nome da função.

`invocation_id` A ID da invocação de função atual.

## <a name="global-variables"></a>Variáveis globais

Não há garantia de que o estado do seu aplicativo será preservado para execuções futuras. No entanto, o runtime do Azure Functions geralmente reutiliza o mesmo processo para várias execuções do mesmo aplicativo. Para armazenar em cache os resultados de uma computação cara, declare-os como uma variável global.

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>Variáveis de ambiente

No Functions, as [configurações de aplicativo](functions-app-settings.md), como cadeias de conexão de serviço, são expostas como variáveis de ambiente durante a execução. Você pode acessar essas configurações com a declaração de `import os` e o uso de `setting = os.environ["setting-name"]`.

O exemplo abaixo obtém a [configuração de aplicativo](functions-how-to-use-azure-function-app-settings.md#settings), com a chave denominada `myAppSetting`:

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

Para o desenvolvimento local, as configurações de aplicativo são [mantidas no arquivo local.settings.json](functions-run-local.md#local-settings-file).

## <a name="python-version"></a>Versão do Python

O Azure Functions dá suporte às seguintes versões do Python:

| Versão do Functions | Versões do<sup>*</sup> Python |
| ----- | ----- |
| 3.x | 3,9 (versão prévia) <br/> 3.8<br/>3.7<br/>3.6 |
| 2. x | 3.7<br/>3.6 |

<sup>*</sup>Distribuições oficiais do CPython

Para solicitar uma versão específica do Python quando você cria seu aplicativo de funções no Azure, use a opção `--runtime-version` do comando [`az functionapp create`](/cli/azure/functionapp#az-functionapp-create). A versão de runtime do Functions é definida pela opção `--functions-version`. A versão do Python é definida quando o aplicativo de funções é criado e não pode ser alterada.

Durante a execução local, o runtime usa a versão disponível do Python.

## <a name="package-management"></a>Gerenciamento de pacotes

Ao desenvolver localmente usando o Azure Functions Core Tools ou o Visual Studio Code, adicione os nomes e as versões dos pacotes necessários para os arquivos `requirements.txt` e instale-os usando `pip`.

Por exemplo, o arquivo de requisitos e comando pip a seguir podem ser usados para instalar o pacote `requests` do PyPI.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Publicando no Azure

Quando você estiver pronto para publicar, verifique se todas as dependências disponíveis publicamente estão listadas no arquivo Requirements.txt, localizado na raiz do diretório do projeto.

Os arquivos e as pastas de projeto que são excluídos da publicação, incluindo a pasta de ambiente virtual, são listados no arquivo .funcignore.

Há três ações de compilação com suporte para publicar seu projeto Python no Azure: compilação remota, compilação local e compilações usando dependências personalizadas.

Você também pode usar Azure Pipelines para criar suas dependências e publicar usando a entrega contínua (CD). Para saber mais, consulte [entrega contínua usando o Azure DevOps](functions-how-to-azure-devops.md).

### <a name="remote-build"></a>Build remoto

Ao usar a compilação remota, as dependências restauradas no servidor e as dependências nativas correspondem ao ambiente de produção. Isso resulta em um pacote de implantação menor a ser carregado. Use a compilação remota ao desenvolver aplicativos Python no Windows. Se o seu projeto tiver dependências personalizadas, você poderá [usar a compilação remota com a URL de índice extra](#remote-build-with-extra-index-url).

As dependências são obtidas remotamente com base no conteúdo do arquivo de requirements.txt. O [Build remoto](functions-deployment-technologies.md#remote-build) é o método de build recomendado. Por padrão, o Azure Functions Core Tools solicita um build remoto quando você usa o comando [func azure functionapp publish](functions-run-local.md#publish) a seguir para publicar seu projeto Python no Azure.

```bash
func azure functionapp publish <APP_NAME>
```

Lembre-se de substituir `<APP_NAME>` pelo nome do aplicativo de funções no Azure.

A [extensão do Azure Functions para Visual Studio Code](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure) também solicita um build remoto por padrão.

### <a name="local-build"></a>Build local

As dependências são obtidas localmente com base no conteúdo do arquivo de requirements.txt. Você pode evitar fazer um build remoto com o uso do comando [func azure functionapp publish](functions-run-local.md#publish) a seguir para publicar com um build local.

```command
func azure functionapp publish <APP_NAME> --build local
```

Lembre-se de substituir `<APP_NAME>` pelo nome do aplicativo de funções no Azure.

Com a opção `--build local`, as dependências do projeto são lidas no arquivo requirements.txt e esses pacotes dependentes são baixados e instalados localmente. Os arquivos e as dependências do projeto são implantados de seu computador local no Azure. Isso faz com que um pacote de implantação maior seja carregado no Azure. Se, por algum motivo, as dependências no arquivo requirements.txt não puderem ser adquiridas pelo Core Tools, você precisará usar a opção de dependências personalizadas para publicar.

Não recomendamos o uso de compilações locais ao desenvolver localmente no Windows.

### <a name="custom-dependencies"></a>Dependências personalizadas

Quando o projeto tem dependências não encontradas no [índice do pacote do Python](https://pypi.org/), há duas maneiras de compilar o projeto. O método de compilação depende de como você cria o projeto.

#### <a name="remote-build-with-extra-index-url"></a>Build remoto com URL de índice extra

Quando os pacotes estiverem disponíveis em um índice de pacote personalizado acessível, use uma compilação remota. Antes de publicar, certifique-se de [criar uma configuração de aplicativo](functions-how-to-use-azure-function-app-settings.md#settings) chamada `PIP_EXTRA_INDEX_URL` . O valor dessa configuração é a URL do seu índice de pacote personalizado. Usar essa configuração instrui a compilação remota a ser executada `pip install` usando a `--extra-index-url` opção. Para saber mais, confira a [documentação de instalação do Python Pip](https://pip.pypa.io/en/stable/reference/pip_install/#requirements-file-format).

Você também pode usar credenciais básicas de autenticação com suas URLs de índice de pacote extra. Para saber mais, consulte [credenciais de autenticação básica](https://pip.pypa.io/en/stable/user_guide/#basic-authentication-credentials) na documentação do Python.

#### <a name="install-local-packages"></a>Instalar pacotes locais

Se seu projeto usa pacotes que não estão disponíveis publicamente para nossas ferramentas, você poderá disponibilizá-los para seu aplicativo se os colocar no diretório \_app \_\_\_/.python_packages. Antes de publicar, execute o seguinte comando para instalar as dependências localmente:

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

Ao usar dependências personalizadas, você deve usar a `--no-build` opção de publicação, já que já instalou as dependências na pasta do projeto.

```command
func azure functionapp publish <APP_NAME> --no-build
```

Lembre-se de substituir `<APP_NAME>` pelo nome do aplicativo de funções no Azure.

## <a name="unit-testing"></a>Teste de Unidade

As funções escritas em Python podem ser testadas como qualquer outro código Python por meio de estruturas de teste padrão. Para a maioria das associações, é possível criar um objeto de entrada fictício com a criação de uma instância de uma classe apropriada do pacote `azure.functions`. Já que o pacote [`azure.functions`](https://pypi.org/project/azure-functions/) não fica disponível imediatamente, instale-o por meio do arquivo `requirements.txt` conforme descrito na seção [Gerenciamento de pacotes](#package-management) acima.

Veja *my_second_function* como exemplo, a seguir está um teste fictício de uma função disparada por http:

Primeiro, precisamos criar *<project_root>/my_second_function/function.jsno* arquivo e definir essa função como um gatilho http.

```json
{
  "scriptFile": "__init__.py",
  "entryPoint": "main",
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
    }
  ]
}
```

Agora, podemos implementar o *my_second_function* e o *shared_code. My _second_helper_function*.

```python
# <project_root>/my_second_function/__init__.py
import azure.functions as func
import logging

# Use absolute import to resolve shared_code modules
from shared_code import my_second_helper_function

# Define an http trigger which accepts ?value=<int> query parameter
# Double the value and return the result in HttpResponse
def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Executing my_second_function.')

    initial_value: int = int(req.params.get('value'))
    doubled_value: int = my_second_helper_function.double(initial_value)

    return func.HttpResponse(
      body=f"{initial_value} * 2 = {doubled_value}",
      status_code=200
    )
```

```python
# <project_root>/shared_code/__init__.py
# Empty __init__.py file marks shared_code folder as a Python package
```

```python
# <project_root>/shared_code/my_second_helper_function.py

def double(value: int) -> int:
  return value * 2
```

Podemos começar a escrever casos de teste para nosso gatilho http.

```python
# <project_root>/tests/test_my_second_function.py
import unittest

import azure.functions as func
from my_second_function import main

class TestFunction(unittest.TestCase):
    def test_my_second_function(self):
        # Construct a mock HTTP request.
        req = func.HttpRequest(
            method='GET',
            body=None,
            url='/api/my_second_function',
            params={'value': '21'})

        # Call the function.
        resp = main(req)

        # Check the output.
        self.assertEqual(
            resp.get_body(),
            b'21 * 2 = 42',
        )
```

Dentro de seu `.venv` ambiente virtual Python, instale sua estrutura de teste do Python favorita (por exemplo, `pip install pytest` ). Basta executar `pytest tests` para verificar o resultado do teste.

## <a name="temporary-files"></a>Arquivos temporários

O método `tempfile.gettempdir()` retorna uma pasta temporária, que no Linux é a `/tmp`. Seu aplicativo pode usar esse diretório para armazenar arquivos temporários gerados e usados por suas funções durante a execução.

> [!IMPORTANT]
> Os arquivos gravados no diretório temporário podem não persistir entre invocações. Durante o processo de escalar horizontalmente, os arquivos temporários não são compartilhados entre instâncias.

O exemplo abaixo cria um arquivo temporário nomeado no diretório temporário (`/tmp`):

```python
import logging
import azure.functions as func
import tempfile
from os import listdir

#---
   tempFilePath = tempfile.gettempdir()
   fp = tempfile.NamedTemporaryFile()
   fp.write(b'Hello world!')
   filesDirListInTemp = listdir(tempFilePath)
```

Recomendamos que você mantenha os testes em uma pasta separada da pasta do projeto. Isso impede que você implante o código de teste com seu aplicativo.

## <a name="preinstalled-libraries"></a>Bibliotecas pré-instaladas

Há algumas bibliotecas fornecidas com o tempo de execução de funções do Python.

### <a name="python-standard-library"></a>Biblioteca padrão do Python

A biblioteca padrão do Python contém uma lista de módulos do Python internos que são fornecidos com cada distribuição do Python. A maioria dessas bibliotecas ajuda você a acessar a funcionalidade do sistema, como e/s de arquivo. Em sistemas Windows, essas bibliotecas são instaladas com o Python. Nos sistemas baseados em UNIX, eles são fornecidos por coleções de pacotes.

Para exibir os detalhes completos da lista dessas bibliotecas, visite os links abaixo:

* [Biblioteca padrão do Python 3,6](https://docs.python.org/3.6/library/)
* [Biblioteca padrão do Python 3,7](https://docs.python.org/3.7/library/)
* [Biblioteca padrão do Python 3,8](https://docs.python.org/3.8/library/)
* [Biblioteca padrão do Python 3,9](https://docs.python.org/3.9/library/)

### <a name="azure-functions-python-worker-dependencies"></a>Azure Functions dependências de trabalho do Python

O trabalho do Python do Functions requer um conjunto específico de bibliotecas. Você também pode usar essas bibliotecas em suas funções, mas elas não fazem parte do padrão Python. Se suas funções dependem de qualquer uma dessas bibliotecas, elas podem não estar disponíveis para seu código durante a execução fora do Azure Functions. Você pode encontrar uma lista detalhada de dependências na seção **instalar \_ requer** no arquivo [Setup.py](https://github.com/Azure/azure-functions-python-worker/blob/dev/setup.py#L282) .

> [!NOTE]
> Se o requirements.txt do seu aplicativo de funções contiver uma `azure-functions-worker` entrada, remova-a. O operador do Functions é gerenciado automaticamente pela plataforma Azure Functions e nós os atualizamos regularmente com novos recursos e correções de bugs. A instalação manual de uma versão antiga do Worker no requirements.txt pode causar problemas inesperados.

### <a name="azure-functions-python-library"></a>Azure Functions biblioteca do Python

Cada atualização de trabalho do Python inclui uma nova versão da [biblioteca Azure Functions Python (Azure. Functions)](https://github.com/Azure/azure-functions-python-library). Essa abordagem torna mais fácil atualizar continuamente seus aplicativos de funções do Python, pois cada atualização é compatível com versões anteriores. Uma lista de versões dessa biblioteca pode ser encontrada no [Azure-Functions PyPi](https://pypi.org/project/azure-functions/#history).

A versão da biblioteca de tempo de execução é corrigida pelo Azure e não pode ser substituída por requirements.txt. A `azure-functions` entrada no requirements.txt é apenas para refiapor e conscientização do cliente.

Use o código a seguir para acompanhar a versão real da biblioteca de funções do Python em seu tempo de execução:

```python
getattr(azure.functions, '__version__', '< 1.2.1')
```

### <a name="runtime-system-libraries"></a>Bibliotecas do sistema de tempo de execução

Para obter uma lista de bibliotecas do sistema pré-instalado em imagens do Docker de trabalho do Python, siga os links abaixo:

|  runtime do Functions  | Versão do Debian | Versões do Python |
|------------|------------|------------|
| Versão 2.x | Stretch  | [Python 3.6](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python36/python36.Dockerfile)<br/>[Python 3,7](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python37/python37.Dockerfile) |
| Versão 3.x | Buster | [Python 3.6](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python36/python36.Dockerfile)<br/>[Python 3,7](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python37/python37.Dockerfile)<br />[Python 3.8](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python38/python38.Dockerfile)<br/> [Python 3,9](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python39/python39.Dockerfile)|

## <a name="cross-origin-resource-sharing"></a>Compartilhamento de recursos entre origens

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

O CORS tem suporte total a aplicativos de funções do Python.

## <a name="known-issues-and-faq"></a>Problemas conhecidos e perguntas frequentes

Veja a seguir uma lista de guias de solução de problemas comuns:

* [ModuleNotFoundError e ImportError](recover-python-functions.md#troubleshoot-modulenotfounderror)
* [Não é possível importar ' cygrpc '](recover-python-functions.md#troubleshoot-cannot-import-cygrpc)

Todos os problemas conhecidos e solicitações de recursos são controlados usando a lista [Problemas do GitHub](https://github.com/Azure/azure-functions-python-worker/issues). Se você tiver um problema e não for possível localizá-lo no GitHub, abra um novo problema e inclua uma descrição detalhada dele.

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte os recursos a seguir:

* [Documentação da API do pacote do Azure Functions](/python/api/azure-functions/azure.functions)
* [Práticas recomendadas para o Azure Functions](functions-best-practices.md)
* [Gatilhos e associações de Azure Functions](functions-triggers-bindings.md)
* [Associações do armazenamento de blobs](functions-bindings-storage-blob.md)
* [Associações HTTP e webhook](functions-bindings-http-webhook.md)
* [Associações de Armazenamento de Filas](functions-bindings-storage-queue.md)
* [Gatilho de temporizador](functions-bindings-timer.md)

[Está com problemas? Fale conosco.](https://aka.ms/python-functions-ref-survey)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse
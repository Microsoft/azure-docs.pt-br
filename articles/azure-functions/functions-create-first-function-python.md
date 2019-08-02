---
title: Criar uma função disparada por HTTP no Azure
description: Aprenda a criar sua primeira função Python no Azure usando o Azure Functions Core Tools e a CLI do Azure.
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
ms.openlocfilehash: 5ef30fbf647492f79c64508d8306868aa1f6b278
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444571"
---
# <a name="create-an-http-triggered-function-in-azure"></a>Criar uma função disparada por HTTP no Azure

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

Este artigo mostra como usar ferramentas de linha de comando para criar um projeto do Python executado no Azure Functions. A função que será criada é disparada por solicitações HTTP. Por fim, você publicará seu projeto para ser executado como uma [função sem servidor](functions-scale.md#consumption-plan) no Azure.

Este artigo é o primeiro de dois inícios rápidos do Azure Functions. Depois de concluir este artigo, você [adicionará uma associação de saída de fila do Armazenamento do Azure](functions-add-output-binding-storage-queue-python.md) à sua função.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, é necessário ter o seguinte:

+ Instale o [Python 3.6](https://www.python.org/downloads/).

+ Instale o [Azure Functions Core Tools](./functions-run-local.md#v2) versão 2.6.1071 ou posterior.

+ Instalar a [CLI do Azure](/cli/azure/install-azure-cli) versão 2.x ou posterior.

+ Uma assinatura ativa do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment"></a>Criar e ativar um ambiente virtual

Para desenvolver e testar as funções do Python localmente, é necessário trabalhar em um ambiente do Python 3.6. Execute os seguintes comandos para criar e ativar um ambiente virtual chamado `.env`.

### <a name="bash"></a>Bash:

```bash
python3.6 -m venv .venv
source .venv/bin/activate
```

### <a name="powershell-or-a-windows-command-prompt"></a>PowerShell ou um prompt de comando do Windows:

```powershell
py -3.6 -m venv .venv
.venv\scripts\activate
```

Os comandos restantes são executados no ambiente virtual.

## <a name="create-a-local-functions-project"></a>Criar um projeto de funções local

Um projeto do Functions é o equivalente a um aplicativo de funções no Azure. Ele pode conter várias funções que compartilham as mesmas configurações locais e de hospedagem.

No ambiente virtual, execute o comando a seguir, escolhendo **Python** como o tempo de execução do trabalho.

```console
func init MyFunctionProj
```

Uma pasta chamada _MyFunctionProj_ será criada, que contém os três seguintes arquivos:

* `local.settings.json` é usado para armazenar as configurações do aplicativo e as cadeias de conexão durante a execução local. Esse arquivo não é publicado no Azure.
* `requirements.txt` contém a lista de pacotes a serem instalados durante a publicação no Azure.
* `host.json` contém opções de configuração global que afetam todas as funções em um aplicativo de funções. Esse arquivo é publicado do Azure.

Navegue até a nova pasta MyFunctionProj:

```console
cd MyFunctionProj
```

Em seguida, você atualizará o arquivo host.json para habilitar pacotes de extensão.  

## <a name="create-a-function"></a>Criar uma função

Para adicionar uma função ao projeto, execute o seguinte comando:

```console
func new
```

Escolha o modelo **gatilho HTTP**, digite `HttpTrigger` como o nome da função e, em seguida, pressione Enter.

Uma subpasta chamada _HttpTrigger_ será criada, que contém os seguintes arquivos:

* **function.json**: arquivo de configuração que define a função, o gatilho e outras associações. Examine esse arquivo e veja que o valor de `scriptFile` aponta para o arquivo que contém a função, enquanto o gatilho de invocação e as associações são definidos na matriz `bindings`.

  Cada associação exige uma direção, um tipo e um nome exclusivo. O gatilho HTTP tem uma associação de entrada do tipo [`httpTrigger`](functions-bindings-http-webhook.md#trigger) e uma associação de saída do tipo [`http`](functions-bindings-http-webhook.md#output).

* **\_\_init\_\_.py**: arquivo de script que é a função disparada por HTTP. Examine esse script e veja que ele contém um `main()` padrão. Os dados HTTP do gatilho são passados para essa função usando o parâmetro de associação nomeada `req`. Definido em function.json, `req` é uma instância da [classe azure.functions.HttpRequest](/python/api/azure-functions/azure.functions.httprequest). 

    O objeto de retorno, definido como `$return` em function.json, é uma instância da [classe azure.functions.HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Para saber mais, confira [Gatilhos e associações HTTP do Azure Functions](functions-bindings-http-webhook.md).

## <a name="run-the-function-locally"></a>Executar a função localmente

O comando a seguir inicia o aplicativo de funções, que é executado localmente usando o mesmo Azure Functions Runtime encontrado no Azure.

```bash
func host start
```

Quando o host do Functions é iniciado, ele escreve algo parecido com a seguinte saída, que foi truncada para facilitar a leitura:

```output

                  %%%%%%
                 %%%%%%
            @   %%%%%%    @
          @@   %%%%%%      @@
       @@@    %%%%%%%%%%%    @@@
     @@      %%%%%%%%%%        @@
       @@         %%%%       @@
         @@      %%%       @@
           @@    %%      @@
                %%
                %

...

Content root path: C:\functions\MyFunctionProj
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

...

Http Functions:

        HttpTrigger: http://localhost:7071/api/MyHttpTrigger

[8/27/2018 10:38:27 PM] Host started (29486ms)
[8/27/2018 10:38:27 PM] Job host started
```

Copie a URL da função `HttpTrigger` da saída do tempo de execução de função e cole-a na barra de endereços do navegador. Acrescente o valor de cadeia de consulta `?name=<yourname>` a essa URL e execute a solicitação. O exemplo a seguir mostra a resposta no navegador à solicitação GET retornada pela função local:

![Testar localmente no navegador](./media/functions-create-first-function-python/function-test-local-browser.png)

Agora que você executou a função localmente, poderá criar o aplicativo de funções e outros recursos necessários no Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app-in-azure"></a>Criar um aplicativo de funções no Azure

Um aplicativo de funções fornece um ambiente para execução do código de função. Ele permite que você agrupe funções como uma unidade lógica para facilitar o gerenciamento, a implantação e o compartilhamento de recursos.

Execute o comando a seguir usando um nome de aplicativo de funções exclusivo no lugar do espaço reservado `<APP_NAME>` e o nome da conta de armazenamento de `<STORAGE_NAME>`. O `<APP_NAME>` também é o domínio do DNS padrão para o aplicativo de funções. O nome precisa ser exclusivo em todos os aplicativos no Azure.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <APP_NAME> --storage-account  <STORAGE_NAME>
```

> [!NOTE]
> O Azure Functions, plano de Consumo para Linux, está atualmente em versão prévia e disponível apenas nas seguintes regiões: Oeste dos EUA, Leste dos EUA, Oeste da Europa, Asia Oriental. Além disso, os aplicativos do Linux e do Windows não podem ser hospedados no mesmo grupo de recursos. Se você tiver um grupo de recursos chamado `myResourceGroup` com um aplicativo de funções ou um aplicativo Web do Windows, você precisará usar um grupo de recursos diferente.

Agora você está pronto para publicar seu projeto de funções local no aplicativo de funções no Azure.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

## <a name="next-steps"></a>Próximas etapas

Você criou um projeto de funções do Python com uma função disparada por HTTP, executou-a no computador local e implantou-a no Azure. Agora, estenda sua função por meio da...

> [!div class="nextstepaction"]
> [Adição de uma associação de saída de fila do Armazenamento do Azure](functions-add-output-binding-storage-queue-python.md)

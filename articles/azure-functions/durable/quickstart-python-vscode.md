---
title: Criar sua primeira Durable Function no Azure usando Python
description: Criar e publicar uma Durable Function do Azure em Python usando o Visual Studio Code.
author: anthonychu
ms.topic: quickstart
ms.date: 12/23/2020
ms.reviewer: azfuncdf, antchu
ms.openlocfilehash: cccb42041ece65ad4f245c28dd7c5cf356d5e63c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104775146"
---
# <a name="create-your-first-durable-function-in-python"></a>Criar sua primeira Durable Function em Python

*Durable Functions* são uma extensão do [Azure Functions](../functions-overview.md) que permitem que você escreva funções com estado em um ambiente sem servidor. A extensão gerencia estado, pontos de verificação e reinicializações para você.

Neste artigo, você aprenderá a usar a extensão do Azure Functions do Visual Studio Code para criar e testar localmente uma função durável "hello world".  Essa função orquestrará e encadeará chamadas para outras funções. Em seguida, você publicará o código de função no Azure.

![Como executar funções duráveis no Azure](./media/quickstart-python-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* Instale o [Visual Studio Code](https://code.visualstudio.com/download).

* Instale a extensão do VS Code do [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions).

* Verifique se tem a versão mais recente do [Azure Functions Core Tools](../functions-run-local.md).

* As Durable Functions requerem uma conta de armazenamento do Azure. É necessária uma assinatura do Azure.

* Verifique se você tem a versão 3.6, 3.7 ou 3.8 do [Python](https://www.python.org/) instalada.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Criar seu projeto local 

Nesta seção, você usará o Visual Studio Code para criar um projeto local do Azure Functions. 

1. No Visual Studio Code, pressione F1 (ou <kbd>Ctrl/Cmd+Shift+P</kbd>) para abrir a paleta de comandos. Na paleta de comandos, pesquise e selecione `Azure Functions: Create New Project...`.

    ![Criar função](media/quickstart-python-vscode/functions-create-project.png)

1. Escolha um local em uma pasta vazia para o projeto e escolha **Selecionar**.

1. Após os prompts, forneça as seguintes informações:

    | Prompt | Valor | Descrição |
    | ------ | ----- | ----------- |
    | Selecione uma linguagem de programação para o seu projeto de aplicativo de função. | Python | Criar um projeto local do Functions em Python. |
    | Selecione uma versão | Azure Functions v3 | Você verá essa opção somente quando o Core Tools ainda não estiver instalado. Nesse caso, o Core Tools é instalado na primeira vez em que você executa o aplicativo. |
    | Versão do Python | Python 3.6, 3.7 ou 3.8 | O VS Code criará um ambiente virtual com a versão selecionada. |
    | Selecione um modelo para a primeira função do projeto | Ignorar por enquanto | |
    | Selecione como você gostaria de abrir seu projeto | Abrir na janela atual | Reabre o VS Code na pasta selecionada. |

O Visual Studio Code instalará o Azure Functions Core Tools se necessário. Ele também cria o projeto de aplicativo de funções em uma pasta. Este projeto contém os arquivos de configuração [host.json](../functions-host-json.md) e [local.settings.json](../functions-run-local.md#local-settings-file).

Um arquivo *requirements.txt* também será criado na pasta raiz. Ele especifica os pacotes do Python necessários para a execução do seu aplicativo de funções.

## <a name="install-azure-functions-durable-from-pypi"></a>Instalar o azure-functions-durable de PyPI

Quando você criou o projeto, a extensão de VS Code do Azure Functions criou automaticamente um ambiente virtual com a versão do Python selecionada. Você ativará o ambiente virtual em um terminal e instalará algumas dependências exigidas pelo Azure Functions e pelas Durable Functions.

1. Abra o arquivo *requirements.txt* no editor e altere o conteúdo dele do seguinte modo:

    ```
    azure-functions
    azure-functions-durable
    ```

1. Abra o terminal integrado do editor na pasta atual (<kbd>Ctrl+Shift+`</kbd>).

1. No terminal integrado, ative o ambiente virtual na pasta atual:

    **Linux ou macOS**

    ```bash
    source .venv/bin/activate
    ```

    **Windows**

    ```powershell
    .venv\scripts\activate
    ```

    ![Ativar ambiente virtual](media/quickstart-python-vscode/activate-venv.png)

1. No terminal integrado em que o ambiente virtual está ativado, use pip para instalar os pacotes que você acabou de definir:

    ```bash
    python -m pip install -r requirements.txt
    ```

## <a name="create-your-functions"></a>Criar suas funções

Um aplicativo básico de Durable Functions contém três funções:

* *Função de orquestrador* – descreve um fluxo de trabalho que orquestra outras funções.
* *Função de atividade* – chamada pela função de orquestrador, executa o trabalho e, opcionalmente, retorna um valor.
* *Função de cliente* – uma Função do Azure regular que inicia uma função de orquestrador. Este exemplo usa uma função disparada por HTTP.

### <a name="orchestrator-function"></a>Função de orquestrador

Use um modelo para criar o código da função durável no projeto.

1. Na paleta de comandos, pesquise e selecione `Azure Functions: Create Function...`.

1. Após os prompts, forneça as seguintes informações:

    | Prompt | Valor | Descrição |
    | ------ | ----- | ----------- |
    | Selecione um modelo para a função | Orquestrador das Durable Functions | Crie uma orquestração das Durable Functions |
    | Forneça um nome de função | HelloOrchestrator | Nome da função durável |

Você adicionou um orquestrador para coordenar as funções de atividade. Abra *HelloOrchestrator/\_\_init__.py* para ver a função de orquestrador. Cada chamada para `context.call_activity` invoca uma função de atividade chamada `Hello`.

A seguir, vamos adicionar a função de atividade `Hello` referenciada.

### <a name="activity-function"></a>Função de atividade

1. Na paleta de comandos, pesquise e selecione `Azure Functions: Create Function...`.

1. Após os prompts, forneça as seguintes informações:

    | Prompt | Valor | Descrição |
    | ------ | ----- | ----------- |
    | Selecione um modelo para a função | Atividade das Durable Functions | Crie uma função de atividade |
    | Forneça um nome de função | Olá | Nome da função de atividade |

Você adicionou a função de atividade `Hello`, que é invocada pelo orquestrador. Abra *Hello/\_\_init__.py* para ver que ele aceita um nome como entrada e retorna uma saudação. Uma função de atividade é onde você executará ações como fazer uma chamada de banco de dados ou executar um cálculo.

Por fim, você adicionará uma função disparada por HTTP que inicia a orquestração.

### <a name="client-function-http-starter"></a>Função de cliente (iniciador de HTTP)

1. Na paleta de comandos, pesquise e selecione `Azure Functions: Create Function...`.

1. Após os prompts, forneça as seguintes informações:

    | Prompt | Valor | Descrição |
    | ------ | ----- | ----------- |
    | Selecione um modelo para a função | Iniciador de HTTP das Durable Functions | Crie uma função de iniciador de HTTP |
    | Forneça um nome de função | DurableFunctionsHttpStart | Nome da função de atividade |
    | Nível de autorização | Anônima | Para fins de demonstração, permita que a função seja chamada sem autenticação |

Você adicionou uma função disparada por HTTP que inicia uma orquestração. Abra *DurableFunctionsHttpStart/\_\_init__.py* para ver que ele usa `client.start_new` para iniciar uma nova orquestração. Em seguida, ela usa `client.create_check_status_response` para retornar uma resposta HTTP contendo URLs que podem ser usadas para monitorar e gerenciar a nova orquestração.

Agora, você tem um aplicativo das Durable Functions que pode ser executado localmente e implantado no Azure.

## <a name="test-the-function-locally"></a>Testar a função localmente

As Ferramentas Principais do Azure Functions permitem executar um projeto do Azure Functions no seu computador de desenvolvimento local. Se você não as instalou ainda, precisará instalar essas ferramentas na primeira vez em que iniciar uma função no Visual Studio Code.

1. Para testar a função, defina um ponto de interrupção no código da função de atividade `Hello` (*Hello/\_\_init__.py*). Pressione F5 ou selecione `Debug: Start Debugging` na paleta de comandos para iniciar o projeto do aplicativo de funções. A saída do Core Tools é exibida no painel **Terminal**.

    > [!NOTE]
    > Confira o [Diagnóstico das Durable Functions](durable-functions-diagnostics.md#debugging) para saber mais sobre a depuração.

1. As Durable Functions requerem uma conta de Armazenamento do Azure para serem executadas. Quando o VS Code solicitar que você selecione uma conta de armazenamento, escolha **Selecionar conta de armazenamento**.

    ![Criar Conta de Armazenamento](media/quickstart-python-vscode/functions-select-storage.png)

1. Seguindo os prompts, forneça as seguintes informações para criar uma conta de armazenamento no Azure.

    | Prompt | Valor | Descrição |
    | ------ | ----- | ----------- |
    | Selecionar uma assinatura | *nome da sua assinatura* | Selecionar sua assinatura do Azure |
    | Selecione uma conta de armazenamento | Criar uma nova conta de armazenamento |  |
    | Insira o nome da nova conta de armazenamento | *nome exclusivo* | Nome da conta de armazenamento a ser criada |
    | Selecionar um grupo de recursos | *nome exclusivo* | Nome do grupo de recursos a ser criado |
    | Selecione um local | *região* | Selecione uma região próxima de você |

1. No painel **Terminal**, copie o ponto de extremidade de URL da sua função disparada por HTTP.

    ![Saída local do Azure](media/quickstart-python-vscode/functions-f5.png)

1. Usando o navegador ou uma ferramenta como [Postman](https://www.getpostman.com/) ou [cURL](https://curl.haxx.se/), envie uma solicitação HTTP para o ponto de extremidade de URL. Substitua o último segmento pelo nome da função de orquestrador (`HelloOrchestrator`). A URL deve ser semelhante a `http://localhost:7071/api/orchestrators/HelloOrchestrator`.

   A resposta é o resultado inicial da função HTTP informando que a orquestração durável foi iniciada com êxito. A resposta ainda não é o resultado final da orquestração. A resposta inclui algumas URLs úteis. Por enquanto, vamos consultar o status da orquestração.

1. Copie o valor da URL para `statusQueryGetUri` e cole-o na barra de endereços do navegador e execute a solicitação. Como alternativa, você também pode continuar usando o Postman para emitir a solicitação GET.

   A solicitação consultará a instância de orquestração do status. Você obtém uma resposta eventualmente, o que nos mostra que a instância foi concluída e inclui as saídas ou os resultados da durable function. Ele tem esta aparência: 

    ```json
    {
        "name": "HelloOrchestrator",
        "instanceId": "9a528a9e926f4b46b7d3deaa134b7e8a",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2020-03-18T21:54:49Z",
        "lastUpdatedTime": "2020-03-18T21:54:54Z"
    }
    ```

1. Pressione <kbd>Shift+F5</kbd> no VS Code para interromper a depuração.

Após verificar se a função foi executada corretamente no computador local, é hora de publicar o projeto no Azure.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>Testar sua função no Azure

1. Copie a URL do gatilho de HTTP do painel **Saída**. A URL que chama a função disparada por HTTP deve estar neste formato: `http://<functionappname>.azurewebsites.net/api/orchestrators/HelloOrchestrator`

2. Cole essa nova URL para a solicitação HTTP na barra de endereços do navegador. Ao usar o aplicativo publicado, você deve obter a mesma resposta de status como antes.

## <a name="next-steps"></a>Próximas etapas

Você usou o Visual Studio Code para criar e publicar um aplicativo de funções duráveis em Python.

> [!div class="nextstepaction"]
> [Saiba mais sobre os padrões comuns de função durável](durable-functions-overview.md#application-patterns)

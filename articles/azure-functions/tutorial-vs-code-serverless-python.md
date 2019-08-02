---
title: Criar e implantar Azure Functions em Python com Visual Studio Code
description: Como usar a extensão de Visual Studio Code para Azure Functions para criar funções sem servidor no Python e implantá-las no Azure.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: glenga
ms.openlocfilehash: 587d8040039080e15c61292279c5e5637d296b08
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68351471"
---
# <a name="deploy-python-to-azure-functions-with-visual-studio-code"></a>Implantar o Python para Azure Functions com Visual Studio Code

Neste tutorial, você usa Visual Studio Code e a extensão Azure Functions para criar um ponto de extremidade HTTP sem servidor com Python e também adicionar uma conexão (ou "Associação") ao armazenamento. O Azure Functions executa seu código em um ambiente sem servidor, independentemente de precisar provisionar uma máquina virtual ou publicar um aplicativo Web. A extensão de Azure Functions para Visual Studio Code simplifica muito o processo de usar funções ao lidar automaticamente com muitas questões de configuração.

Se você encontrar problemas com qualquer uma das etapas deste tutorial, adoraríamos saber mais sobre os detalhes. Use o botão entrar **em um problema** no final de cada seção para enviar comentários detalhados.

## <a name="prerequisites"></a>Pré-requisitos

- Uma [assinatura do Azure](#azure-subscription).
- [Visual Studio Code com a extensão Azure Functions](#visual-studio-code-python-and-the-azure-functions-extension) .
- O [Azure Functions Core Tools](#azure-functions-core-tools).

### <a name="azure-subscription"></a>Assinatura do Azure

Se você não tiver uma assinatura do Azure, [Inscreva-se agora](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension) para obter uma conta gratuita de 30 dias com $200 em créditos do Azure para experimentar qualquer combinação de serviços.

### <a name="visual-studio-code-python-and-the-azure-functions-extension"></a>Visual Studio Code, Python e a extensão de Azure Functions

Instale o seguinte software:

- Python 3.6. x, conforme exigido pelo Azure Functions. [Python 3.6.8](https://www.python.org/downloads/release/python-368/) é a versão mais recente do 3.6. x.
- [Visual Studio Code](https://code.visualstudio.com/).
- A [extensão do Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) , conforme descrito em [Visual Studio Code tutorial do Python – pré-requisitos](https://code.visualstudio.com/docs/python/python-tutorial).
- A [extensão de Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions). Para obter informações gerais, visite o [repositório GitHub vscode-azurefunctions](https://github.com/Microsoft/vscode-azurefunctions).

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Siga as instruções para o seu sistema operacional em [trabalhar com Azure Functions Core Tools](functions-run-local.md#v2). As próprias ferramentas são escritas no .NET Core e o pacote de ferramentas principais é melhor instalado usando o Gerenciador de pacotes do node. js, NPM, que é por isso que você precisa instalar o .NET Core e o Node. js no momento, mesmo para o código Python. No entanto, você pode ignorar o requisito do .NET Core usando "pacotes de extensão", conforme descrito na documentação mencionada anteriormente. Seja qual for o caso, você precisa instalar esses componentes apenas uma vez, após o qual Visual Studio Code solicita automaticamente que você instale as atualizações.

### <a name="sign-in-to-azure"></a>Entrar no Azure

Depois que a extensão de funções for instalada, entre em sua conta do Azure navegando até o **Azure: Gerenciador** de funções, selecione **entrar no Azure**e siga os prompts.

![Entre no Azure por meio do Visual Studio Code](media/tutorial-vs-code-serverless-python/azure-sign-in.png)

Depois de entrar, verifique se a conta de email da sua assinatura do Azure aparece na barra de status:

![Barra de status Visual Studio Code mostrando a conta do Azure](media/tutorial-vs-code-serverless-python/azure-account-status-bar.png)

O nome que você atribuiu à sua assinatura também aparece no **Azure: Gerenciador** de funções ("primário" na imagem abaixo):

![Visual Studio Code Azure App o Gerenciador de serviços mostrando assinaturas](media/tutorial-vs-code-serverless-python/azure-subscription-view.png)

> [!NOTE]
> Se você encontrar o erro **"não é possível encontrar a assinatura com o nome [ID da assinatura]"** , isso pode ser porque você está protegido por um proxy e não consegue acessar a API do Azure. Configurar `HTTP_PROXY` e`HTTPS_PROXY` variáveis de ambiente com suas informações de proxy em seu terminal:
>
> ```bash
> # macOS/Linux
> export HTTPS_PROXY=https://username:password@proxy:8080
> export HTTP_PROXY=http://username:password@proxy:8080
> ```
>
> ```ps
> # Windows
> set HTTPS_PROXY=https://username:password@proxy:8080
> set HTTP_PROXY=http://username:password@proxy:8080
> ```

### <a name="verify-prerequisites"></a>Verificar pré-requisitos

Para verificar se todas as ferramentas de Azure Functions estão instaladas, abra a Visual Studio Code paleta de comandos (F1) **, selecione o terminal: Crie um novo comando** de terminal integrado e, depois que o terminal for aberto `func`, execute o comando:

![Verificando Azure Functions os pré-requisitos das ferramentas principais](media/tutorial-vs-code-serverless-python/check-prereqs.png)

A saída que começa com o logotipo Azure Functions (você precisa rolar a saída para cima) indica que as Azure Functions Core Tools estão presentes.

Se o `func` comando não for reconhecido, verifique se a pasta onde você instalou o Azure Functions Core Tools está incluída na variável de ambiente Path.

> [!div class="nextstepaction"]
> [Ocorreu um problema](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=01-verify-prerequisites)

## <a name="create-the-function"></a>Criar a função

1. O código para Azure Functions é gerenciado em um _projeto_do functions, que você cria primeiro antes de criar o código. No **Azure: Gerenciador** de funções (aberto usando o ícone do Azure no lado esquerdo), selecione o ícone de comando **novo projeto** ou abra a paleta de comandos (F1) **e selecione Azure Functions: Criar Novo Projeto**.

    ![Botão Criar novo projeto no Gerenciador de funções](media/tutorial-vs-code-serverless-python/project-create-new.png)

1. Nos prompts a seguir:

    | Prompt | Valor | Descrição | 
    | --- | --- | --- |
    | Especifique uma pasta para o projeto | Pasta aberta atual | A pasta na qual criar o projeto. Talvez você queira criar o projeto em uma subpasta. |
    | Selecione uma linguagem de programação para o seu projeto de aplicativo de função. | **Python** | O idioma a ser usado para a função, que determina o modelo usado para o código. |
    | Selecione um modelo para a primeira função do projeto | **Gatilho HTTP** | Uma função que usa um gatilho HTTP é executada sempre que há uma solicitação HTTP feita ao ponto de extremidade da função. (Há uma variedade de outros gatilhos para Azure Functions. Para saber mais, veja [o que posso fazer com as funções?](functions-overview.md#what-can-i-do-with-functions).) |
    | Forneça um nome de função | HttpExample | O nome é usado para uma subpasta que contém o código da função junto com os dados de configuração e também define o nome do ponto de extremidade HTTP. Use "HttpExample" em vez de aceitar o padrão "HTTPTrigger" para distinguir a função em si do gatilho. |
    | Nível de autorização | **Modo** | A autorização anônima torna a função publicamente acessível a qualquer pessoa. |
    | Selecione como você gostaria de abrir seu projeto | **Abrir na janela atual** | Abre o projeto na janela de Visual Studio Code atual. |

1. Após um curto período de tempo, uma mensagem para indicar que o novo projeto foi criado. No **Gerenciador**, há a subpasta criada para a função e Visual Studio Code abre o  *\_ \_arquivo\_Init\_. py* que contém o código de função padrão:

    [![Resultado da criação de um novo projeto de funções do Python](media/tutorial-vs-code-serverless-python/project-create-results.png)](media/tutorial-vs-code-serverless-python/project-create-results.png)

    > [!NOTE]
    > Se Visual Studio Code informar que você não tem um intérprete Python **selecionado ao abrir  *\_ \_\_Init\_. py*, abra a paleta de comandos (F1), selecione o Python: Selecione o** comando interpretador e, em seguida, selecione o ambiente `.env` virtual na pasta local (que foi criada como parte do projeto). O ambiente deve ser baseado em Python 3.6 x especificamente, conforme observado anteriormente em [pré-requisitos](#prerequisites).
    >
    > ![Selecionando o ambiente virtual criado com o projeto](media/tutorial-vs-code-serverless-python/select-venv-interpreter.png)

> [!TIP]
> Sempre que desejar criar outra função no mesmo projeto, use o comando **criar função** no **Azure: Gerenciador** de funções, ou abra a paleta de comandos (F1) e **selecione o Azure Functions: Criar Função**. Ambos os comandos solicitam a você um nome de função (que é o nome do ponto de extremidade) e, em seguida, cria uma subpasta com os arquivos padrão.
>
> ![Novo comando de função no Azure: Gerenciador de funções](media/tutorial-vs-code-serverless-python/function-create-new.png)

> [!div class="nextstepaction"]
> [Ocorreu um problema](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=02-create-function)

## <a name="examine-the-code-files"></a>Examinar os arquivos de código

Na subpasta da função recém-criada há três arquivos:  *\_ \_Init\_\_. py* contém o código da função, *Function. JSON* descreve a função para Azure Functions e *Sample. dat* é um arquivo de dados de exemplo. Você pode excluir *Sample. dat* se desejar, pois ele existe apenas para mostrar que você pode adicionar outros arquivos à subpasta.

Vamos examinar primeiro *Function. JSON* e, em seguida, o código  *\_em\_ \_\_init. py*.

### <a name="functionjson"></a>function.json

O arquivo function. JSON fornece as informações de configuração necessárias para o ponto de extremidade Azure Functions:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "anonymous",
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

A `scriptFile` propriedade identifica o arquivo de inicialização para o código e esse código deve conter uma função Python chamada `main`. Você pode considerar o código em vários arquivos, contanto que o arquivo especificado aqui contenha uma `main` função.

O `bindings` elemento contém dois objetos, um para descrever as solicitações de entrada e o outro para descrever a resposta http. Para solicitações de entrada`"direction": "in"`(), a função responde a solicitações HTTP Get ou post e não requer autenticação. A resposta (`"direction": "out"`) é uma resposta http que retorna qualquer valor retornado `main` da função Python.

### <a name="initpy"></a>\_\_init.py\_\_

Quando você cria uma nova função, Azure Functions fornece o código Python padrão  *\_em\_ \_\_init. py*:

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

As partes importantes do código são as seguintes:

- Você deve importar `func` de `azure.functions`; a importação do módulo de registro em log é opcional, mas recomendada.
- A função `main` Python necessária recebe um `func.request` objeto chamado `req`e retorna um valor do tipo `func.HttpResponse`. Você pode saber mais sobre os recursos desses objetos no [Func. HttpRequest](/python/api/azure-functions/azure.functions.httprequest?view=azure-python) e [Func. ](/python/api/azure-functions/azure.functions.httpresponse?view=azure-python)Referências de HttpResponse.
- O corpo de `main` , em seguida, processa a solicitação e gera uma resposta. Nesse caso, o código procura um `name` parâmetro na URL. Falhando, ele verifica se o corpo da solicitação contém JSON ( `func.HttpRequest.get_json`usando) e se o JSON contém `name` um valor (usando `get` o método do objeto JSON retornado por `get_json`).
- Se um nome for encontrado, o código retornará a cadeia de caracteres "Olá" com o nome anexado; caso contrário, ele retornará uma mensagem de erro.

> [!div class="nextstepaction"]
> [Ocorreu um problema](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=03-examine-code-files)

## <a name="debug-locally"></a>Depurar localmente

1. Quando você cria o projeto do functions, a extensão Visual Studio Code também cria uma configuração `.vscode/launch.json` de inicialização no que contém uma única configuração chamada **Attach to Python Functions**. Essa configuração significa que você pode apenas pressionar F5 ou usar o Gerenciador de depuração para iniciar o projeto:

    ![Gerenciador de depuração mostrando a configuração de inicialização das funções](media/tutorial-vs-code-serverless-python/launch-configuration.png)

1. Quando você inicia o depurador, um terminal é aberto mostrando a saída de Azure Functions, incluindo um resumo dos pontos de extremidade disponíveis. Sua URL poderá ser diferente se você tiver usado um nome diferente de "HttpExample":

    ```output
    Http Functions:

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ```

1. Use **Ctrl + clique** ou **cmd + clique** na URL na janela Visual Studio Code **saída** para abrir um navegador para esse endereço ou inicie um navegador e cole a mesma URL. Em ambos os casos, o ponto `api/<function_name>`de extremidade é, `api/HttpExample`nesse caso. No entanto, como essa URL não inclui um parâmetro de nome, a janela do navegador deve apenas mostrar "passe um nome na cadeia de caracteres de consulta ou no corpo da solicitação", conforme apropriado para esse caminho no código.

1. Agora, tente adicionar um parâmetro de nome ao uso, como `http://localhost:7071/api/HttpExample?name=VS%20Code`, e a janela do navegador deverá exibir a mensagem "Olá Visual Studio Code!", demonstrando que você executou esse caminho de código.

1. Para passar o valor de nome em um corpo de solicitação JSON, você pode usar uma ferramenta como a rotação com o JSON embutido:

    ```bash
    # Mac OS/Linux: modify the URL if you're using a different function name
    curl --header "Content-Type: application/json" --request POST \
        --data {"name":"Visual Studio Code"} http://localhost:7071/api/HttpExample
    ```

    ```ps
    # Windows (escaping on the quotes is necessary; also modify the URL
    # if you're using a different function name)
    curl --header "Content-Type: application/json" --request POST \
        --data {"""name""":"""Visual Studio Code"""} http://localhost:7071/api/HttpExample
    ```

    Como alternativa, crie um arquivo como *Data. JSON* que contenha `{"name":"Visual Studio Code"}` e use o `curl --header "Content-Type: application/json" --request POST --data @data.json http://localhost:7071/api/HttpExample`comando.

1. Para testar a depuração da função, defina um ponto de interrupção na linha `name = req.params.get('name')` que lê e faça uma solicitação para a URL novamente. O depurador de Visual Studio Code deve parar nessa linha, permitindo que você examine as variáveis e percorra o código. (Para obter uma breve explicação da depuração básica, consulte [Visual Studio Code tutorial – configurar e executar o depurador](https://code.visualstudio.com/docs/python/python-tutorial.md#configure-and-run-the-debugger).)

1. Quando você estiver satisfeito de testar completamente a função localmente, pare o depurador (com o comando **debug** > **Stop Debugging** menu ou o comando **Disconnect** na barra de ferramentas Debugging).

> [!div class="nextstepaction"]
> [Ocorreu um problema](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=04-test-debug)

## <a name="deploy-to-azure-functions"></a>Implantar no Azure Functions

Nestas etapas, você usa a extensão Functions para criar um aplicativo de funções no Azure, juntamente com outros recursos do Azure necessários. Um aplicativo de funções permite a você agrupar funções como uma unidade lógica para facilitar o gerenciamento, implantação e compartilhamento de recursos. Ele também requer uma conta de armazenamento do Azure para dados e um [plano de hospedagem](functions-scale.md#hosting-plan-support). Todos esses recursos são organizados em um único grupo de recursos.

1. Na área **Azure: Gerenciador** de funções, selecione o comando **implantar no aplicativo de funções** ou abra a paleta de comandos (F1) e selecione **o Azure Functions: Implante no comando** aplicativo de funções. Novamente, o aplicativo de funções é onde seu projeto Python é executado no Azure.

    ![Implantar no comando Aplicativo de funções](media/tutorial-vs-code-serverless-python/deploy-command.png)

1. Quando solicitado, selecione **criar novo aplicativo de funções no Azure**e forneça um nome que seja exclusivo no Azure (normalmente usando o nome pessoal ou da empresa junto com outros identificadores exclusivos; você pode usar letras, números e hifens). Se você criou um Aplicativo de funções anteriormente, seu nome aparecerá nesta lista de opções.

1. A extensão executa as seguintes ações, que você pode observar em Visual Studio Code mensagens pop-up e a janela **saída** (o processo leva alguns minutos):

    - Crie um grupo de recursos usando o nome que você deu (removendo hifens).
    - Nesse grupo de recursos, crie a conta de armazenamento, o plano de hospedagem e o aplicativo de funções. Por padrão, um [plano de consumo](functions-scale.md#consumption-plan) é criado. Para executar suas funções em um plano dedicado, você precisa [habilitar a publicação com opções de criação avançadas](functions-develop-vs-code.md#enabled-publishing-with-advanced-create-options).
    - Implante seu código no aplicativo de funções.

    O **Azure: O** Gerenciador de funções também mostra o progresso:

    ![Indicador de progresso de implantação no Azure: Gerenciador de funções](media/tutorial-vs-code-serverless-python/deploy-progress.png)

1. Depois que a implantação for concluída, a extensão Azure Functions exibirá uma mensagem com botões para três ações adicionais:

    ![Mensagem indicando uma implantação bem-sucedida com ações adicionais](media/tutorial-vs-code-serverless-python/deployment-popup.png)

    Para **logs de fluxo** e **configurações de carregamento**, consulte as próximas seções. Para **exibir a saída**, consulte a etapa 5 a seguir.

1. Após a implantação, a janela de **saída** também mostra o ponto de extremidade público no Azure:

    ```output
    HTTP Trigger Urls:
      HttpExample: https://vscode-azure-functions.azurewebsites.net/api/HttpExample
    ```

    Use esse ponto de extremidade para executar os mesmos testes que você fez localmente, usando parâmetros de URL e/ou solicitações com dados JSON no corpo da solicitação. Os resultados do ponto de extremidade público devem corresponder aos resultados do ponto de extremidade local que você testou anteriormente.

> [!div class="nextstepaction"]
> [Ocorreu um problema](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=05-deploy)

### <a name="stream-logs"></a>Transmitir logs

O suporte para streaming de log está atualmente em desenvolvimento, conforme descrito no [problema 589](https://github.com/microsoft/vscode-azurefunctions/issues/589) para a extensão Azure functions. O botão **logs de fluxo** no pop-up mensagem de implantação eventualmente conectará a saída de log no Azure para Visual Studio Code. Você também poderá iniciar e parar o fluxo de log no **Azure Functions** Explorer clicando com o botão direito do mouse no projeto de funções e selecionando **Iniciar logs de streaming** ou **parar logs de streaming**.

No presente, no entanto, esses comandos ainda não estão operacionais. O streaming de log está disponível em um navegador executando o comando a seguir, `<app_name>` substituindo pelo nome do seu aplicativo do Functions no Azure:

```bash
# Replace <app_name> with the name of your Functions app on Azure
func azure functionapp logstream <app_name> --browser
```

### <a name="sync-local-settings-to-azure"></a>Sincronizar configurações locais para o Azure

O botão **carregar configurações** no pop-up mensagem de implantação aplica as alterações feitas no arquivo *local. Settings. JSON* ao Azure. Você também pode invocar o comando no **Azure Functions** Explorer expandindo o nó do projeto do functions, clicando com o botão direito do mouse em **configurações do aplicativo**e selecionando **carregar configurações locais...** . Você também pode usar a paleta de comandos para selecionar **o Azure Functions: Comando carregar configurações** locais.

O carregamento das configurações atualiza as configurações existentes e adiciona as novas configurações definidas em *local. Settings. JSON*. O carregamento não remove nenhuma configuração do Azure que não esteja listada no arquivo local. Para remover essas configurações, expanda o nó **configurações de aplicativos** no **Azure Functions** Explorer, clique com o botão direito do mouse na configuração e selecione **Excluir configuração...** . Você também pode editar as configurações diretamente na portal do Azure.

Para aplicar as alterações feitas por meio do portal ou por meio do **Azure Explorer** ao arquivo *local. Settings. JSON* , clique com o botão direito do mouse no nó **configurações do aplicativo** e selecione o comando **baixar configurações remotas...** . Você também pode usar a paleta de comandos para selecionar **o Azure Functions: Comando baixar configurações** remotas.

## <a name="add-a-second-function"></a>Adicionar uma segunda função

Após sua primeira implantação, você pode fazer alterações no código, como adicionar funções adicionais e reimplantar no mesmo aplicativo de funções.

1. Na área **Azure: Gerenciador** de funções, selecione o comando **criar função** ou **use Azure Functions: Crie a** função na paleta de comandos. Especifique os seguintes detalhes para a função:

    - Modelo: Gatilho HTTP
    - Nome: "DigitsOfPi"
    - Nível de autorização: Anônimo

1. No Visual Studio Code explorador de arquivos é uma subpasta com o nome da função que, novamente, contém os arquivos chamados  *\_ \_\_Init\_. py*, *Function. JSON*e *Sample. dat*.

1. Substitua o conteúdo de  *\_ \_Init.py\_para corresponder ao código a seguir, que gera uma cadeia de caracteres contendo o valor de PI para um número de dígitos especificado na URL (esse código usa apenas um parâmetro de URL)\_*

    ```python
    import logging

    import azure.functions as func

    """ Adapted from the second, shorter solution at http://www.codecodex.com/wiki/Calculate_digits_of_pi#Python
    """

    def pi_digits_Python(digits):
        scale = 10000
        maxarr = int((digits / 4) * 14)
        arrinit = 2000
        carry = 0
        arr = [arrinit] * (maxarr + 1)
        output = ""

        for i in range(maxarr, 1, -14):
            total = 0
            for j in range(i, 0, -1):
                total = (total * j) + (scale * arr[j])
                arr[j] = total % ((j * 2) - 1)
                total = total / ((j * 2) - 1)

            output += "%04d" % (carry + (total / scale))
            carry = total % scale

        return output;

    def main(req: func.HttpRequest) -> func.HttpResponse:
        logging.info('DigitsOfPi HTTP trigger function processed a request.')

        digits_param = req.params.get('digits')

        if digits_param is not None:
            try:
                digits = int(digits_param)
            except ValueError:
                digits = 10   # A default

            if digits > 0:
                digit_string = pi_digits_Python(digits)

                # Insert a decimal point in the return value
                return func.HttpResponse(digit_string[:1] + '.' + digit_string[1:])

        return func.HttpResponse(
             "Please pass the URL parameter ?digits= to specify a positive number of digits.",
             status_code=400
        )
    ```

1. Como o código dá suporte apenas a http Get, modifique *Function. JSON* para `"methods"` que a coleção `"get"` contenha apenas (ou `"post"`seja, remove). O arquivo inteiro deve aparecer da seguinte maneira:

    ```json
    {
      "scriptFile": "__init__.py",
      "bindings": [
        {
          "authLevel": "anonymous",
          "type": "httpTrigger",
          "direction": "in",
          "name": "req",
          "methods": [
            "get"
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

1. Inicie o depurador pressionando F5 ou selecionando o comando **depurar** > menu**Iniciar Depuração** . A janela **saída** agora deve mostrar os dois pontos de extremidade em seu projeto:

    ```output
    Http Functions:

            DigitsOfPi: [GET] http://localhost:7071/api/DigitsOfPi

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ```

1. Em um navegador, ou de ondulação, faça uma solicitação `http://localhost:7071/api/DigitsOfPi?digits=125` para e observe a saída. (Você pode notar que o algoritmo de código não é totalmente preciso, mas deixaremos os aprimoramentos para você!) Pare o depurador quando tiver terminado.

1. Reimplante o código usando o **aplicativo de funções implantar** no **Azure: Gerenciador** de funções. Se solicitado, selecione o Aplicativo de funções criado anteriormente.

1. Após a conclusão da implantação (demora alguns minutos!), a janela de **saída** mostra os pontos de extremidade públicos com os quais você pode repetir seus testes.

> [!div class="nextstepaction"]
> [Ocorreu um problema](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=06-second-function)

## <a name="add-a-binding-to-write-messages-to-azure-storage"></a>Adicionar uma associação para gravar mensagens no armazenamento do Azure

Uma _Associação_ permite que você conecte seu código de função a recursos, como o armazenamento do Azure, sem gravar nenhum código de acesso a dados. Uma associação é definida no arquivo *Function. JSON* e pode representar entrada e saída. Uma função pode usar várias associações de entrada e saída, mas apenas um gatilho. Para saber mais, confira [Conceitos de gatilhos e de associações do Azure Functions](functions-triggers-bindings.md).

Nesta seção, você adicionará uma associação de armazenamento à função HttpExample criada anteriormente neste tutorial. A função usa essa associação para gravar mensagens no armazenamento com cada solicitação. O armazenamento em questão usa a mesma conta de armazenamento padrão usada pelo aplicativo de funções. No entanto, se você planeja fazer uso intenso do armazenamento, convém considerar a criação de uma conta separada.

1. Sincronize as configurações remotas para seu projeto Azure Functions no arquivo *local. Settings. JSON* abrindo a paleta de comandos e selecionando **Azure Functions: Baixar configurações**remotas. Abra *local. Settings. JSON* e verifique se ele contém um valor para `AzureWebJobsStorage`. Esse valor é a cadeia de conexão para a conta de armazenamento.

1. Na pasta, clique com o botão direito do mouse em *Function. JSON*, selecione **Adicionar associação:** `HttpExample`

    ![Adicionar comando de associação no Gerenciador de Visual Studio Code](media/tutorial-vs-code-serverless-python/add-binding-command.png)

1. Nos prompts que seguem no Visual Studio Code, selecione ou forneça os seguintes valores:

    | Prompt | Valor a ser fornecido |
    | --- | --- |
    | Definir direção de vinculação | out |
    | Selecionar associação com direção | Armazenamento de Filas do Azure |
    | O nome usado para identificar esta associação em seu código | MSG |
    | A fila para a qual a mensagem será enviada | outqueue |
    | Selecione configuração de *local. Settings. JSON* (solicitando a conexão de armazenamento) | AzureWebJobsStorage |

1. Depois de fazer essas seleções, verifique se a seguinte associação foi adicionada ao seu arquivo *Function. JSON* :

    ```json
        {
          "type": "queue",
          "direction": "out",
          "name": "msg",
          "queueName": "outqueue",
          "connection": "AzureWebJobsStorage"
        }
    ```

1. Agora que você configurou a associação, você pode usá-la em seu código de função. Novamente, a associação recentemente definida aparece em seu código como um argumento para a `main` função em  *\_ \_Init\_\_. py*. Por exemplo, você pode modificar o  *\_ \_arquivo\_Init\_. py* em HttpExample para corresponder ao seguinte, que mostra o uso `msg` do argumento para gravar uma mensagem com carimbo de data/hora com o nome usado no Quest. Os comentários explicam as alterações específicas:

    ```python
    import logging
    import datetime  # MODIFICATION: added import
    import azure.functions as func

    # MODIFICATION: the added binding appears as an argument; func.Out[func.QueueMessage]
    # is the appropriate type for an output binding with "type": "queue" (in function.json).
    def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> func.HttpResponse:
        logging.info('Python HTTP trigger function processed a request.')

        name = req.params.get('name')
        if not name:
            try:
                req_body = req.get_json()
            except ValueError:
                pass
            else:
                name = req_body.get('name')

        if name:
            # MODIFICATION: write the a message to the message queue, using msg.set
            msg.set(f"Request made for {name} at {datetime.datetime.now()}")

            return func.HttpResponse(f"Hello {name}!")
        else:
            return func.HttpResponse(
                 "Please pass a name on the query string or in the request body",
                 status_code=400
            )
    ```

1. Para testar essas alterações localmente, inicie o depurador novamente no Visual Studio Code pressionando F5 ou selecionando o comando **depurar** > menu**Iniciar Depuração** . Como antes, a janela de **saída** deve mostrar os pontos de extremidade em seu projeto.

1. Em um navegador, visite a URL `http://localhost:7071/api/HttpExample?name=VS%20Code` para criar uma solicitação ao ponto de extremidade HttpExample, que também deve gravar uma mensagem na fila.

1. Para verificar se a mensagem foi gravada na fila "perqueue" (como nomeada na associação), você pode usar um dos três métodos:

    1. Entre no [portal do Azure](https://portal.azure.com)e navegue até o grupo de recursos que contém o projeto do functions. Dentro desse grupo de recursos, local e navegue até a conta de armazenamento do projeto e, em seguida, navegue até **filas**. Nessa página, navegue até "perqueue", que deve exibir todas as mensagens registradas.

    1. Navegue e examine a fila com o Gerenciador de Armazenamento do Azure, que se integra ao Visual Studio, conforme descrito em [funções de conexão para o armazenamento do Azure usando Visual Studio Code](functions-add-output-binding-storage-queue-vs-code.md), especialmente a seção [examinar a fila de saída](functions-add-output-binding-storage-queue-vs-code.md#examine-the-output-queue) .

    1. Use o CLI do Azure para consultar a fila de armazenamento, conforme descrito em [consultar a fila de armazenamento](functions-add-output-binding-storage-queue-python.md#query-the-storage-queue).
    
1. Para testar na nuvem, reimplante o código usando **implantar para aplicativo de funções** no **Azure: Gerenciador** de funções. Se solicitado, selecione o Aplicativo de funções criado anteriormente. Após a conclusão da implantação (leva alguns minutos!), a janela de **saída** mostra novamente os pontos de extremidade públicos com os quais você pode repetir seus testes.

> [!div class="nextstepaction"]
> [Ocorreu um problema](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=07-storage-binding)

## <a name="clean-up-resources"></a>Limpar recursos

A Aplicativo de funções que você criou inclui recursos que podem incorrer em custos mínimos (consulte o [preço das funções](https://azure.microsoft.com/pricing/details/functions/)). Para limpar os recursos, clique com o botão direito do mouse no **aplicativo de funções no Azure: Gerenciador** de funções e selecione **excluir aplicativo de funções**. Você também pode visitar a [portal do Azure](https://portal.azure.com), selecionar **grupos de recursos** no painel de navegação do lado esquerdo, selecionar o grupo de recursos que foi criado no processo deste tutorial e, em seguida, usar o comando **excluir grupo de recursos** .

## <a name="next-steps"></a>Próximas etapas

Parabéns por concluir este passo a passos da implantação do código Python no Azure Functions! Agora você está pronto para criar muitas funções sem servidor.

Conforme observado anteriormente, você pode aprender mais sobre a extensão de funções visitando seu repositório GitHub, [vscode-azurefunctions](https://github.com/Microsoft/vscode-azurefunctions). Os problemas e as contribuições também são bem-vindos.

Leia o [Azure Functions visão geral](functions-overview.md) para explorar os diferentes gatilhos que você pode usar.

Para saber mais sobre os serviços do Azure que você pode usar do Python, incluindo o armazenamento de dados junto com o ia e os serviços de Machine Learning, visite o [centro de desenvolvedores do Python do Azure](/azure/python/?view=azure-python).

Também há outras extensões do Azure para Visual Studio Code que podem ser úteis. Basta pesquisar "Azure" no Gerenciador de extensões:

![Extensões do Azure para Visual Studio Code](media/tutorial-vs-code-serverless-python/azure-extensions.png)

Algumas extensões populares são:

- [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
- [Serviço de Aplicativo do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)
- [Ferramentas de CLI do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
- [Ferramentas do Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

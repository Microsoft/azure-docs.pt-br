---
title: Criar uma função TypeScript da linha de comando – Azure Functions
description: Saiba como criar uma função TypeScript da linha de comando e publicar o projeto local por meio da hospedagem sem servidor no Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: 488ef9fa3fd5b6c09ed435483dbf8f6fa3eb5bef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97937188"
---
# <a name="quickstart-create-a-typescript-function-in-azure-from-the-command-line"></a>Início rápido: criar uma função TypeScript no Azure da linha de comando

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

Neste artigo, você usará ferramentas de linha de comando para criar uma função do TypeScript que responde a solicitações HTTP. Após testar o código localmente, implante-o no ambiente sem servidor do Azure Functions.

A realização deste início rápido gera um pequeno custo de alguns centavos de dólar ou menos em sua conta do Azure.

Há também uma [versão baseada no Visual Studio Code](create-first-function-vs-code-typescript.md) deste artigo.

## <a name="configure-your-local-environment"></a>Configurar o ambiente local

Antes de começar, você deverá ter o seguinte:

+ Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#v2), versão 3.x.

+ Uma das seguintes ferramentas para a criação de recursos do Azure:

    + [CLI do Azure](/cli/azure/install-azure-cli), versão 2.4 ou posterior.

    + [Azure PowerShell](/powershell/azure/install-az-ps), versão 5.0 ou posterior.

+ [Node.js](https://nodejs.org/), versões Active LTS e Maintenance LTS (8.11.1 e 10.14.1 recomendadas).

### <a name="prerequisite-check"></a>Verificação de pré-requisitos

Verifique os pré-requisitos, que dependem se você está usando a CLI do Azure ou o Azure PowerShell para criar recursos do Azure:

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

+ Em um terminal ou uma janela Comando, execute `func --version` para verificar se a versão do Azure Functions Core Tools é a 3.x.

+ Execute `az --version` para verificar se a versão da CLI do Azure é a 2.4 ou posterior.

+ Execute `az login` para entrar no Azure e verifique se a assinatura está ativa.

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

+ Em um terminal ou uma janela Comando, execute `func --version` para verificar se a versão do Azure Functions Core Tools é a 3.x.

+ Execute `(Get-Module -ListAvailable Az).Version` e verifique se é a versão 5.0 ou posterior. 

+ Execute `Connect-AzAccount` para entrar no Azure e verifique se a assinatura está ativa.

---

## <a name="create-a-local-function-project"></a>Criar um projeto de função local

No Azure Functions, um projeto de função é um contêiner para uma ou mais funções individuais que respondem, cada uma, a um gatilho específico. Todas as funções em um projeto compartilham as configurações locais e de hospedagem. Nesta seção, você cria um projeto de função que contém apenas uma função.

1. Execute o comando `func init` da seguinte maneira para criar um projeto de funções em uma pasta chamada *LocalFunctionProj* com o runtime especificado:  

    ```console
    func init LocalFunctionProj --typescript
    ```

1. Navegue até a pasta do projeto:

    ```console
    cd LocalFunctionProj
    ```
    
    Essa pasta contém vários arquivos do projeto, incluindo arquivos de configuração chamados [local.settings.json](functions-run-local.md#local-settings-file) e [host.json](functions-host-json.md). Como *local.settings.json* pode conter segredos baixados do Azure, o arquivo é excluído do controle do código-fonte por padrão no arquivo *.gitignore*.

1. Adicione uma função ao projeto usando o comando a seguir, em que o argumento `--name` é o nome exclusivo da função (HttpExample) e o argumento `--template` especifica o gatilho da função (HTTP).

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 
    
    `func new` cria uma subpasta correspondente ao nome da função que contém um arquivo de código apropriado para a linguagem escolhida do projeto, bem como um arquivo de configuração chamado *function.json*.

### <a name="optional-examine-the-file-contents"></a>(Opcional) Examinar o conteúdo do arquivo

Se preferir, você poderá ir diretamente para [Executar a função localmente](#run-the-function-locally) e examinar o conteúdo do arquivo mais tarde.

#### <a name="indexts"></a>index.ts

*index.ts* exporta uma função que é disparada de acordo com a configuração em *function.json*.

:::code language="typescript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-TypeScript/index.ts":::

Para um gatilho HTTP, a função recebe dados de solicitação na variável `req` do tipo **HttpRequest**, conforme definido em *function.json*. O objeto de retorno, definido como `$return` em *function.json*, é a resposta. 

#### <a name="functionjson"></a>function.json

*function.json* é um arquivo de configuração que define as `bindings` de entrada e de saída da função, incluindo o tipo de gatilho. 

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::

Cada associação exige uma direção, um tipo e um nome exclusivo. O gatilho HTTP tem uma associação de entrada do tipo [`httpTrigger`](functions-bindings-http-webhook-trigger.md) e uma associação de saída do tipo [`http`](functions-bindings-http-webhook-output.md).

## <a name="run-the-function-locally"></a>Executar a função localmente

1. Execute sua função iniciando o host de runtime do Azure Functions local da pasta *LocalFunctionProj*:

    ```console
    npm install
    npm start
    ```
    
    Perto do fim da saída, devem aparecer as seguintes linhas:
    
    <pre>
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    >[!NOTE]  
    > Se HttpExample não aparece conforme mostrado abaixo, é provável que você tenha iniciado o host de fora da pasta raiz do projeto. Nesse caso, use **Ctrl**+**C** para parar o host, navegue até a pasta raiz do projeto e execute o comando anterior novamente.

1. Copie a URL da função `HttpExample` dessa saída para um navegador e acrescente a cadeia de caracteres de consulta `?name=<your-name>`, fazendo com que a URL completa seja `http://localhost:7071/api/HttpExample?name=Functions`. O navegador deve exibir uma mensagem como `Hello Functions`:

    ![Resultado da execução local da função no navegador](./media/functions-create-first-azure-function-azure-cli/function-test-local-browser.png)
    
    O terminal em que você iniciou seu projeto também mostra a saída do log conforme você faz solicitações.

1. Quando você estiver pronto, use **Ctrl**+**C** e escolha `y` para interromper o host de funções.

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. Criar o aplicativo de funções no Azure:

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime node --runtime-version 12 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    O comando [az functionapp create](/cli/azure/functionapp#az_functionapp_create) cria o aplicativo de funções no Azure. Se você estiver usando o Node.js 10, altere também `--runtime-version` para `10`.
    
    # <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime node -RuntimeVersion 12 -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    O cmdlet [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) cria o aplicativo de funções no Azure. Se você estiver usando o Node.js 10, altere `-RuntimeVersion` para `10`.

    ---
        
    No exemplo anterior, substitua `<STORAGE_NAME>` pelo nome da conta usada na etapa anterior e substitua `<APP_NAME>` por um nome globalmente exclusivo que seja apropriado para você. O `<APP_NAME>` também é o domínio do DNS padrão para o aplicativo de funções. 
    
    Este comando cria um aplicativo de funções que executa o runtime da linguagem especificada no [Plano de Consumo do Azure Functions](consumption-plan.md), que é gratuito para o uso que você fará aqui. O comando também provisiona uma instância associada do Azure Application Insights no mesmo grupo de recursos, com a qual você pode monitorar seu aplicativo de funções e exibir logs. Para saber mais, consulte [Monitorar Azure Functions](functions-monitoring.md). A instância não gera nenhum custo até você ativá-la.

## <a name="deploy-the-function-project-to-azure"></a>Implantar o projeto de funções no Azure

Antes de usar o Core Tools para implantar seu projeto no Azure, você criará um build pronto para produção de arquivos JavaScript com base nos arquivos de origem TypeScript.

1. Use o seguinte comando a fim de preparar o projeto TypeScript para implantação:

    ```console
    npm run build:production 
    ```

1. Com os recursos necessários configurados, você está pronto para implantar seu projeto de funções local no aplicativo de funções no Azure usando o comando [func azure functionapp publish](functions-run-local.md#project-file-deployment). No exemplo a seguir, substitua `<APP_NAME>` pelo nome do aplicativo.

    ```console
    func azure functionapp publish <APP_NAME>
    ```
    
    Se vir o erro "Não é possível encontrar o aplicativo com o nome...", aguarde alguns segundos e tente novamente, pois o Azure pode não ter inicializado totalmente o aplicativo após o comando `az functionapp create` anterior.
    
    O comando de publicação mostra resultados semelhantes à seguinte saída (truncado para fins de simplicidade):
    
    <pre>
    ...
    
    Getting site publishing info...
    Creating archive for current directory...
    Performing remote build for functions project.
    
    ...
    
    Deployment successful.
    Remote build succeeded!
    Syncing triggers...
    Functions in msdocs-azurefunctions-qs:
        HttpExample - [httpTrigger]
            Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample?code=KYHrydo4GFe9y0000000qRgRJ8NdLFKpkakGJQfC3izYVidzzDN4gQ==
    </pre>

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Conectar-se a uma fila do Armazenamento do Azure](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-typescript)

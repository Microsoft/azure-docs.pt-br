---
title: Criar uma função do PowerShell da linha de comando – Azure Functions
description: Saiba como criar uma função do PowerShell da linha de comando e publicar o projeto local por meio da hospedagem sem servidor no Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-powershell
- devx-track-azurecli
ms.openlocfilehash: abbe3b9ed4d9a8c9bf30c6be3e6980228d319090
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97937222"
---
# <a name="quickstart-create-a-powershell-function-in-azure-from-the-command-line"></a>Início rápido: criar uma função do PowerShell no Azure da linha de comando

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

Neste artigo, você usará ferramentas de linha de comando para criar uma função do PowerShell que responde a solicitações HTTP. Após testar o código localmente, implante-o no ambiente sem servidor do Azure Functions.

A realização deste início rápido gera um pequeno custo de alguns centavos de dólar ou menos em sua conta do Azure.

Há também uma [versão baseada no Visual Studio Code](create-first-function-vs-code-powershell.md) deste artigo.

## <a name="configure-your-local-environment"></a>Configurar o ambiente local

Antes de começar, você deverá ter o seguinte:

+ Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#v2), versão 3.x.

+ Uma das seguintes ferramentas para a criação de recursos do Azure:

    + [Azure PowerShell](/powershell/azure/install-az-ps), versão 5.0 ou posterior.

    + [CLI do Azure](/cli/azure/install-azure-cli), versão 2.4 ou posterior.

+ O [SDK do .NET Core 3.1](https://www.microsoft.com/net/download)

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
    func init LocalFunctionProj --powershell
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

#### <a name="runps1"></a>run.ps1

*run.ps1* contém um script de função que é disparado de acordo com a configuração em *function.json*.

:::code language="powershell" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/run.ps1":::

Para um gatilho HTTP, a função recebe dados de solicitação transmitidos para o parâmetro `$Request` definido em *function.json*. O objeto de retorno, definido como `Response` em *function.json*, é transmitido para o cmdlet `Push-OutputBinding` como a resposta. 

#### <a name="functionjson"></a>function.json

*function.json* é um arquivo de configuração que define as `bindings` de entrada e de saída da função, incluindo o tipo de gatilho. 

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json":::

Cada associação exige uma direção, um tipo e um nome exclusivo. O gatilho HTTP tem uma associação de entrada do tipo [`httpTrigger`](functions-bindings-http-webhook-trigger.md) e uma associação de saída do tipo [`http`](functions-bindings-http-webhook-output.md).

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. Criar o aplicativo de funções no Azure:

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime powershell --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    O comando [az functionapp create](/cli/azure/functionapp#az_functionapp_create) cria o aplicativo de funções no Azure. 
    
    # <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime PowerShell -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    O cmdlet [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) cria o aplicativo de funções no Azure. 
    
    ---
    
    No exemplo anterior, substitua `<STORAGE_NAME>` pelo nome da conta usada na etapa anterior e substitua `<APP_NAME>` por um nome globalmente exclusivo que seja apropriado para você. O `<APP_NAME>` também é o domínio do DNS padrão para o aplicativo de funções. 
    
    Este comando cria um aplicativo de funções que executa o runtime da linguagem especificada no [Plano de Consumo do Azure Functions](consumption-plan.md), que é gratuito para o uso que você fará aqui. O comando também provisiona uma instância associada do Azure Application Insights no mesmo grupo de recursos, com a qual você pode monitorar seu aplicativo de funções e exibir logs. Para saber mais, consulte [Monitorar Azure Functions](functions-monitoring.md). A instância não gera nenhum custo até você ativá-la.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Conectar-se a uma fila do Armazenamento do Azure]

[Conectar-se a uma fila do Armazenamento do Azure]: functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-powershell

---
title: Criar uma função Python na linha de comando para o Azure Functions
description: Saiba como criar uma função Python da linha de comando e publicar o projeto local por meio da hospedagem sem servidor no Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-python
- devx-track-azurecli
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 626cff867a336880689373c289087e2332a816ee
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787441"
---
# <a name="quickstart-create-a-python-function-in-azure-from-the-command-line"></a>Início Rápido: criar uma função Python no Azure da linha de comando

> [!div class="op_single_selector" title1="Selecione a linguagem da função: "]
> - [Python](create-first-function-cli-python.md)
> - [C#](create-first-function-cli-csharp.md)
> - [Java](create-first-function-cli-java.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [TypeScript](create-first-function-cli-typescript.md)

Neste artigo, você usa ferramentas de linha de comando para criar uma função em Python que responde a solicitações HTTP. Após testar o código localmente, você o implanta no <abbr title="Um ambiente de computação de runtime no qual todos os detalhes do servidor são transparentes para os desenvolvedores de aplicativos, o que simplifica o processo de implantação e gerenciamento de código.">sem servidor</abbr> ambiente do <abbr title="Um serviço do Azure que fornece um ambiente de computação sem servidor de baixo custo para os aplicativos.">Funções do Azure</abbr>.

A realização deste início rápido gera um pequeno custo de alguns centavos de dólar ou menos em sua conta do Azure.

Há também uma [versão baseada no Visual Studio Code](create-first-function-vs-code-python.md) deste artigo.

## <a name="1-configure-your-environment"></a>1. Configurar seu ambiente

Antes de começar, você deverá ter o seguinte:

+ Uma conta do Azure <abbr title="O perfil que mantém as informações de cobrança pelo uso do Azure.">account</abbr> com uma assinatura ativa <abbr title="A estrutura organizacional básica na qual você gerencia recursos no Azure, normalmente associada a um indivíduo ou departamento da organização.">subscription</abbr>. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#v2), versão 3.x. 
  
+ A <abbr title="Um conjunto de ferramentas de linha de comando multiplataforma para trabalhar com recursos do Azure no computador de desenvolvimento local, como uma alternativa ao uso do portal do Azure.">CLI do Azure</abbr> ou <abbr title="Um módulo do PowerShell que fornece comandos para trabalhar com recursos do Azure no computador de desenvolvimento local, como uma alternativa ao uso do portal do Azure.">Azure PowerShell</abbr> para criar recursos do Azure:

    + [CLI do Azure](/cli/azure/install-azure-cli), versão 2.4 ou posterior.

    + [Azure PowerShell](/powershell/azure/install-az-ps), versão 5.0 ou posterior.

+ [Python 3.8 (64 bits)](https://www.python.org/downloads/release/python-382/), [Python 3.7 (64 bits)](https://www.python.org/downloads/release/python-375/) e [Python 3.6 (64 bits)](https://www.python.org/downloads/release/python-368/), todos compatíveis com a versão 3.x do Azure Functions.

### <a name="11-prerequisite-check"></a>1.1 Verificação de pré-requisitos

Verifique os pré-requisitos, que dependem de você estar usando a CLI do Azure ou o Azure PowerShell para criar recursos do Azure:

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

+ Em um terminal ou janela de comando, execute `func --version` para verificar se o <abbr title="O conjunto de ferramentas de linha de comando para trabalhar com o Azure Functions no computador local.">Azure Functions Core Tools</abbr> é a versão 3.x.

+ Execute `az --version` para verificar se a versão da CLI do Azure é a 2.4 ou posterior.

+ Execute `az login` para entrar no Azure e verifique se a assinatura está ativa.

+ Execute `python --version` (Linux/macOS) ou `py --version` (Windows) para verificar se a versão do Python é 3.8.x, 3.7.x ou 3.6.x.

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

+ Em um terminal ou janela de comando, execute `func --version` para verificar se o <abbr title="O conjunto de ferramentas de linha de comando para trabalhar com o Azure Functions no computador local.">Azure Functions Core Tools</abbr> é a versão 3.x.

+ Execute `(Get-Module -ListAvailable Az).Version` e verifique se é a versão 5.0 ou posterior. 

+ Execute `Connect-AzAccount` para entrar no Azure e verifique se a assinatura está ativa.

+ Execute `python --version` (Linux/macOS) ou `py --version` (Windows) para verificar se a versão do Python é 3.8.x, 3.7.x ou 3.6.x.

---

<br/>

---

## <a name="2-create-and-activate-a-virtual-environment"></a>2. <a name="create-venv"></a>Criar e ativar um ambiente virtual

Em uma pasta adequada, execute os comandos a seguir para criar e ativar um ambiente virtual chamado `.venv`. Lembre-se de usar as versões 3.8, 3.7 ou 3.6 do Python, que são compatíveis com o Azure Functions.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Se o Python não instalou o pacote venv na distribuição do Linux, execute o seguinte comando:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

Você executará todos os comandos posteriores neste ambiente virtual ativado. 

<br/>

---

## <a name="3-create-a-local-function-project"></a>3. Criar um projeto de função local

Nesta seção, você cria um <abbr title="Um contêiner lógico para uma ou mais funções individuais que podem ser implantadas e gerenciadas juntas.">projeto local do Azure Functions</abbr> em Python. Cada função no projeto responde a um determinado <abbr title="O tipo de evento que invoca o código da função, por exemplo, uma solicitação HTTP, uma mensagem da fila ou uma hora específica.">gatilho</abbr>.

1. Execute o comando `func init` para criar um projeto de funções em uma pasta chamada *LocalFunctionProj* com o runtime especificado:  

    ```console
    func init LocalFunctionProj --python
    ```

1. Navegue até a pasta do projeto:

    ```console
    cd LocalFunctionProj
    ```
    
    <br/>
    <details>
    <summary><strong>O que é criado na pasta LocalFunctionProj?</strong></summary>
    
    Essa pasta contém vários arquivos do projeto, incluindo arquivos de configuração chamados [local.settings.json](functions-run-local.md#local-settings-file) e [host.json](functions-host-json.md). Como *local.settings.json* pode conter segredos baixados do Azure, o arquivo é excluído do controle do código-fonte por padrão no arquivo *.gitignore*.
    </details>

1. Adicione uma função ao projeto usando o seguinte comando:

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```   
    O argumento `--name` é o nome exclusivo de sua função (HttpExample).

    O argumento `--template` especifica o gatilho da função (HTTP).
    
    `func new` cria uma subpasta correspondente ao nome da função que contém um arquivo *\_\_init\_\_.py* com o código da função e um arquivo de configuração chamado *function.json*.

    <br/>    
    <details>
    <summary><strong>Código para __init__.py</strong></summary>
    
    *\_\_init\_\_.py* contém uma função de Python `main()` que é disparada de acordo com a configuração em *function.json*.
    
    :::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::
    
    Para um gatilho HTTP, a função recebe dados de solicitação na variável `req`, conforme definido em *function.json*. `req` é uma instância da [classe azure.functions.HttpRequest](/python/api/azure-functions/azure.functions.httprequest). O objeto de retorno, definido como `$return` em *function.json*, é uma instância da [classe azure.functions.HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Para saber mais, confira [Gatilhos e associações HTTP do Azure Functions](./functions-bindings-http-webhook.md?tabs=python).
    </details>

    <br/>
    <details>
    <summary><strong>Código para function.json</strong></summary>

    *function.json* é um arquivo de configuração que define as <abbr title="Conexões declarativas entre uma função e outros recursos. Uma associação de entrada fornece dados à função; uma associação de saída fornece dados da função para outros recursos.">associações de entrada e saída</abbr> para a função, incluindo o tipo de gatilho.
    
    Se preferir, você poderá alterar `scriptFile` para invocar um arquivo Python diferente.
    
    :::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
    
    Cada associação exige uma direção, um tipo e um nome exclusivo. O gatilho HTTP tem uma associação de entrada do tipo [`httpTrigger`](functions-bindings-http-webhook-trigger.md) e uma associação de saída do tipo [`http`](functions-bindings-http-webhook-output.md).    
    </details>

<br/>

---

## <a name="4-run-the-function-locally"></a>4. Executar a função localmente

1. Execute sua função iniciando o host de runtime do Azure Functions local da pasta *LocalFunctionProj*:

    ```
    func start
    ```

    Perto do fim da saída, devem aparecer as seguintes linhas: 
    
    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    <br/>
    <details>
    <summary><strong>Não vejo HttpExample na saída</strong></summary>

    Se HttpExample não aparece, é provável que você tenha iniciado o host fora da pasta raiz do projeto. Nesse caso, use <kbd>Ctrl+C</kbd> para parar o host, navegue até a pasta raiz do projeto e execute o comando anterior novamente.
    </details>

1. Copie a URL da função **HttpExample** dessa saída para um navegador e acrescente a cadeia de caracteres de consulta **?name=<SEU_NOME>** , fazendo com que a URL completa seja **http://localhost:7071/api/HttpExample?name=Functions** . O navegador deve exibir uma mensagem como **Olá, Functions**:

    ![Resultado da execução local da função no navegador](../../includes/media/functions-run-function-test-local-cli/function-test-local-browser.png)

1. O terminal em que você iniciou seu projeto também mostra a saída do log conforme você faz solicitações.

1. Quando terminar, use <kbd>Ctrl+C</kbd> e escolha <kbd>y</kbd> para interromper o host de funções.

<br/>

---

## <a name="5-create-supporting-azure-resources-for-your-function"></a>5. Criar recursos de suporte do Azure para a função

Para implantar o código da função no Azure, você precisa criar um <abbr title="Um contêiner lógico para recursos do Azure relacionados que você pode gerenciar como uma unidade.">grupo de recursos</abbr>, uma <abbr title="Uma conta com todos os seus objetos de dados do armazenamento do Azure. A conta de armazenamento fornece um namespace exclusivo para seus dados do armazenamento.">do Azure</abbr>e um <abbr title="O recurso de nuvem que hospeda funções sem servidor no Azure, que fornece o ambiente de computação subjacente no qual as funções são executadas.">aplicativo de funções</abbr> usando os seguintes comandos:

1. Se você ainda não tiver feito isso, entre no Azure:

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
    ```azurecli
    az login
    ```

    O comando [az login](/cli/azure/reference-index#az_login) conecta você à conta do Azure.

    # <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    O cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) conecta você à conta do Azure.

    ---

1. Crie um grupo de recursos chamado `AzureFunctionsQuickstart-rg` na região `westeurope`. 

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    O comando [az group create](/cli/azure/group#az_group_create) cria um grupo de recursos. Normalmente, você cria o grupo de recursos e os recursos em uma <abbr title="Uma referência geográfica a um datacenter específico do Azure no qual os recursos estão alocados.">region</abbr> perto de você usando uma região disponível retornada pelo comando `az account list-locations`.

    # <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    O comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cria um grupo de recursos. De modo geral, você cria o grupo de recursos e os recursos em uma região próxima a você usando uma região disponível retornada do cmdlet [Get-AzLocation](/powershell/module/az.resources/get-azlocation).

    ---

    Você não pode hospedar aplicativos Windows e Linux no mesmo grupo de recursos. Se você tiver um grupo de recursos chamado `AzureFunctionsQuickstart-rg` com um aplicativo de funções ou um aplicativo Web do Windows, você precisará usar um grupo de recursos diferente.

1. Crie uma conta do Armazenamento do Azure para uso geral no grupo de recursos e na região:

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    O comando [az storage account create](/cli/azure/storage/account#az_storage_account_create) cria a conta de armazenamento. 

    # <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```

    O cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) cria a conta de armazenamento.

    ---

    Substitua `<STORAGE_NAME>` por um nome que for adequado para você e <abbr title="O nome deve ser exclusivo entre todas as contas de armazenamento usadas por todos os clientes do Azure globalmente. Por exemplo, você pode usar uma combinação de seu nome ou do nome da empresa, o nome do aplicativo e um identificador numérico, por exemplo, contosobizappstorage20.">exclusivo no Armazenamento do Azure</abbr>. Os nomes devem conter de 3 a 24 caracteres e podem conter somente números e letras minúsculas. `Standard_LRS` especifica uma conta de uso geral, que é [compatível com o Functions](storage-considerations.md#storage-account-requirements).
    
    A conta de armazenamento gera apenas alguns centavos de dólar para este início rápido.

1. Criar o aplicativo de funções no Azure:

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime python --runtime-version 3.8 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME> --os-type linux
    ```
    
    O comando [az functionapp create](/cli/azure/functionapp#az_functionapp_create) cria o aplicativo de funções no Azure. Se você estiver usando o Python 3.7 ou 3.6, altere `--runtime-version` para `3.7` ou `3.6`, respectivamente.
    
    # <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -FunctionsVersion 3 -RuntimeVersion 3.8 -Runtime python -Location 'West Europe'
    ```
    
    O cmdlet [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) cria o aplicativo de funções no Azure. Se você estiver usando o Python 3.7 ou 3.6, altere `-RuntimeVersion` para `3.7` ou `3.6`, respectivamente.

    ---
    
    Substitua `<STORAGE_NAME>` pelo nome da conta usada na etapa anterior.

    Substitua `<APP_NAME>` por um <abbr title="Um nome que deve ser globalmente exclusivo entre todos os clientes do Azure. Por exemplo, você pode usar uma combinação de seu nome ou do nome da organização, o nome do aplicativo e um identificador numérico, por exemplo, contoso-bizapp-func-20.">nome globalmente exclusivo apropriado para você</abbr>. O `<APP_NAME>` também é o domínio do DNS padrão para o aplicativo de funções. 
    
    <br/>
    <details>
    <summary><strong>Qual é o custo dos recursos provisionados no Azure?</strong></summary>

    Este comando cria um aplicativo de funções que executa o runtime da linguagem especificada no [Plano de Consumo do Azure Functions](functions-scale.md#overview-of-plans), que é gratuito para o uso que você fará aqui. O comando também provisiona uma instância associada do Azure Application Insights no mesmo grupo de recursos, com a qual você pode monitorar seu aplicativo de funções e exibir logs. Para saber mais, consulte [Monitorar Azure Functions](functions-monitoring.md). A instância não gera nenhum custo até você ativá-la.
    </details>

<br/>

---

## <a name="6-deploy-the-function-project-to-azure"></a>6. Implantar o projeto de funções no Azure

Depois de criar com sucesso o aplicativo de funções no Azure, você estará pronto para **implantar o projeto de funções local** usando o comando [func azure functionapp publish](functions-run-local.md#project-file-deployment).  

No exemplo a seguir, substitua `<APP_NAME>` pelo nome do aplicativo.

```console
func azure functionapp publish <APP_NAME>
```

O comando `publish` mostra resultados semelhantes à seguinte saída (truncado para fins de simplicidade):

<pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
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
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample
</pre>

<br/>

---

## <a name="7-invoke-the-function-on-azure"></a>7. Invocar a função no Azure

Como a função usa um gatilho HTTP, você a invoca fazendo uma solicitação HTTP para sua URL no navegador ou usando uma ferramenta como <abbr title="Uma ferramenta de linha de comando para gerar solicitações HTTP para uma URL; confira https://curl.se/">curl</abbr>. 

# <a name="browser"></a>[Navegador](#tab/browser)

Copie a **URL de Invocação** completa mostrada na saída do comando `publish` na barra de endereços de um navegador, acrescentando o parâmetro de consulta **&name=Functions**. O navegador deverá exibir uma saída semelhante à que foi exibida quando você executou a função localmente.

![A saída da função executada no Azure em um navegador](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

Execute [`curl`](https://curl.haxx.se/) com a **URL de Invocação**, acrescentando o parâmetro **&name=Functions**. A saída do comando deverá ser o texto, "Olá, Functions".

![A saída da função executada no Azure usando curl](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---

### <a name="71-view-real-time-streaming-logs"></a>7.1 Exibir logs de streaming em tempo real

Execute o seguinte comando para ver os [logs de streaming](functions-run-local.md#enable-streaming-logs) quase em tempo real no Application Insights no portal do Azure:

```console
func azure functionapp logstream <APP_NAME> --browser
```

Substitua `<APP_NAME>` pelo nome do aplicativo de funções.

Em uma janela de terminal separada ou no navegador, chame a função remota novamente. Um log detalhado da execução da função no Azure é mostrado no terminal. 

<br/>

---

## <a name="8-clean-up-resources"></a>8. Limpar os recursos

Se você prosseguir para a [próxima etapa](#next-steps) e adicionar uma <abbr title="Uma forma de associar uma função a uma fila de armazenamento para que ela possa criar mensagens na fila. "> Associação de saída de fila do Armazenamento do Azure</abbr>mantenha todos os seus recursos no local para dar continuidade ao que já fez.

Caso contrário, use o comando a seguir para excluir o grupo de recursos e todos os recursos contidos nele para evitar custos adicionais.

 # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name AzureFunctionsQuickstart-rg
```

<br/>

---

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Conectar-se a uma fila do Armazenamento do Azure](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-python)

[Está com problemas? Fale conosco.](https://aka.ms/python-functions-qs-survey)

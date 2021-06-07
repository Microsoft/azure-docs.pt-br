---
title: Criar uma função C# da linha de comando – Azure Functions
description: Saiba como criar uma função C# da linha de comando e publicar o projeto local por meio da hospedagem sem servidor no Azure Functions.
ms.date: 10/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-csharp
- devx-track-azurecli
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 2d03f8c820e0a8b6a19394649db66f8028b62781
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768787"
---
# <a name="quickstart-create-a-c-function-in-azure-from-the-command-line"></a>Início rápido: criar uma função C# no Azure da linha de comando

> [!div class="op_single_selector" title1="Selecione a linguagem da função: "]
> - [C#](create-first-function-cli-csharp-ieux.md)
> - [Java](create-first-function-cli-java.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [Python](create-first-function-cli-python.md)
> - [TypeScript](create-first-function-cli-typescript.md)

Neste artigo, você usará ferramentas de linha de comando para criar uma função baseada em biblioteca de classes C# que responde a solicitações HTTP. Após testar o código localmente, você o implanta no <abbr title="Um ambiente de computação de runtime no qual todos os detalhes do servidor são transparentes para os desenvolvedores de aplicativos, o que simplifica o processo de implantação e gerenciamento de código.">sem servidor</abbr> ambiente do <abbr title="O serviço do Azure que fornece um ambiente de computação sem servidor de baixo custo para os aplicativos.">Funções do Azure</abbr>.

A realização deste início rápido gera um pequeno custo de alguns centavos de dólar ou menos em sua conta do Azure.

Há também uma [versão baseada no Visual Studio Code](create-first-function-vs-code-csharp.md) deste artigo.

## <a name="1-prepare-your-environment"></a>1. Prepare o seu ambiente

+ Obter uma conta do Azure <abbr title="O perfil que mantém as informações de cobrança pelo uso do Azure.">account</abbr> com uma assinatura ativa <abbr title="A estrutura organizacional básica na qual você gerencia recursos no Azure, normalmente associada a um indivíduo ou departamento da organização.">subscription</abbr>. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ Instalar o [SDK do .NET Core 3.1](https://www.microsoft.com/net/download)

+ Instalar o [Azure Functions Core Tools](functions-run-local.md#v2) versão 3x.

+ A <abbr title="Um conjunto de ferramentas de linha de comando multiplataforma para trabalhar com recursos do Azure no computador de desenvolvimento local, como uma alternativa ao uso do portal do Azure.">CLI do Azure</abbr> ou <abbr title="Um módulo do PowerShell que fornece comandos para trabalhar com recursos do Azure no computador de desenvolvimento local, como uma alternativa ao uso do portal do Azure.">Azure PowerShell</abbr> para criar recursos do Azure:

    + [CLI do Azure](/cli/azure/install-azure-cli), versão 2.4 ou posterior.

    + [Azure PowerShell](/powershell/azure/install-az-ps), versão 5.0 ou posterior.

---

### <a name="2-verify-prerequisites"></a>2. Verificar pré-requisitos

Verifique os pré-requisitos, que dependem de você estar usando a CLI do Azure ou o Azure PowerShell para criar recursos do Azure:

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

+ Em um terminal ou janela de comando, execute `func --version` para verificar se o <abbr title="O conjunto de ferramentas de linha de comando para trabalhar com o Azure Functions no computador local.">Azure Functions Core Tools</abbr> é a versão 3.x.

+ **Execute** `az --version` para verificar se a versão da CLI do Azure é a 2.4 ou posterior.

+ **Execute** `az login` para entrar no Azure e verifique se a assinatura está ativa.

+ **Execute** `dotnet --list-sdks` para verificar se o SDK do .NET Core versão 3.1.x está instalado

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

+**Execute** `func --version` para verificar se a versão do Azure Functions Core Tools é a 3.x.

+ **Execute** `(Get-Module -ListAvailable Az).Version` e verifique se a versão é a 5.0 ou posterior. 

+ **Execute** `Connect-AzAccount` para entrar no Azure e verifique se a assinatura está ativa.

+ **Execute** `dotnet --list-sdks` para verificar se o SDK do .NET Core versão 3.1.x está instalado

---

## <a name="3-create-a-local-function-project"></a>3. Criar um projeto de função local

Nesta seção, você cria um <abbr title="Um contêiner lógico para uma ou mais funções individuais que podem ser implantadas e gerenciadas juntas.">projeto local do Azure Functions</abbr> em C#. Cada função no projeto responde a um determinado <abbr title="Um evento que invoca o código da função, por exemplo, uma solicitação HTTP, uma mensagem da fila ou uma hora específica.">gatilho</abbr>.

1. Execute o comando `func init` para criar um projeto de funções em uma pasta chamada *LocalFunctionProj* com o runtime especificado:  

    ```csharp
    func init LocalFunctionProj --dotnet
    ```

1. **Execute** 'cd LocalFunctionProj' para navegar até a <abbr title="Essa pasta contém vários arquivos do projeto, incluindo arquivos de configuração chamados local.settings.json e host.json. Como local.settings.json pode conter segredos baixados do Azure, o arquivo é excluído do controle do código-fonte por padrão no arquivo .gitignore.">pasta do projeto</abbr>.

    ```console
    cd LocalFunctionProj
    ```
    <br/>

1. Adicione uma função ao projeto usando o seguinte comando:
    
    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 
    O argumento `--name` é o nome exclusivo de sua função (HttpExample).

    O argumento `--template` especifica o gatilho da função (HTTP).

    
    <br/>   
    <details>  
    <summary><strong>Opcional: código para HttpExample.cs</strong></summary>  
    
    *HttpExample.cs* contém um método `Run` que recebe dados de solicitação na variável `req` é uma [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest) decorada com o **HttpTriggerAttribute**, que define o comportamento do gatilho.

    :::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::
        
    O objeto de retorno é um [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) que retorna uma mensagem de resposta como um [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) ou um [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). Para saber mais, confira [Gatilhos e associações HTTP do Azure Functions](./functions-bindings-http-webhook.md?tabs=csharp).  
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


1. Selecione  <kbd>Ctrl + C</kbd> e escolha <kbd>y</kbd> para parar o host de funções.

<br/>

---
    
## <a name="5-create-supporting-azure-resources-for-your-function"></a>5. Criar recursos de suporte do Azure para a função

Para implantar o código da função no Azure, você precisa criar um <abbr title="Um contêiner lógico para recursos do Azure relacionados que você pode gerenciar como uma unidade.">grupo de recursos</abbr>, uma <abbr title="Uma conta com todos os seus objetos de dados do armazenamento do Azure. A conta de armazenamento fornece um namespace exclusivo para seus dados do armazenamento.">do Azure</abbr>e um <abbr title="O recurso de nuvem que hospeda funções sem servidor no Azure, que fornece o ambiente de computação subjacente no qual as funções são executadas.">aplicativo de funções</abbr> usando os seguintes comandos:

1. Se você ainda não tiver feito isso, entre no Azure:

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
    ```azurecli
    az login
    ```


    # <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```


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


    ---

    Você não pode hospedar aplicativos Windows e Linux no mesmo grupo de recursos. Se você tiver um grupo de recursos chamado `AzureFunctionsQuickstart-rg` com um aplicativo de funções ou um aplicativo Web do Windows, você precisará usar um grupo de recursos diferente.
    
1. Crie uma conta do Armazenamento do Azure para uso geral no grupo de recursos e na região:

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```


    # <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```


    ---

    Substitua `<STORAGE_NAME>` por um nome que for adequado para você e <abbr title="O nome deve ser exclusivo entre todas as contas de armazenamento usadas por todos os clientes do Azure globalmente. Por exemplo, você pode usar uma combinação de seu nome ou do nome da empresa, o nome do aplicativo e um identificador numérico, por exemplo, contosobizappstorage20">exclusivo no Armazenamento do Azure</abbr>. Os nomes devem conter de 3 a 24 caracteres e podem conter somente números e letras minúsculas. `Standard_LRS` especifica uma conta de uso geral, que é [compatível com o Functions](storage-considerations.md#storage-account-requirements).


1. Crie o aplicativo de funções no Azure.
**Substitua** '<STORAGE_NAME>** pelo nome na etapa anterior.
**Substitua** '<APP_NAME>' por um nome globalmente exclusivo.

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    
    # <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime dotnet -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    
    ---
    
    Substitua `<STORAGE_NAME>` pelo nome da conta usada na etapa anterior.

    Substitua `<APP_NAME>` por um <abbr title="Um nome que deve ser globalmente exclusivo entre todos os clientes do Azure. Por exemplo, você pode usar uma combinação de seu nome ou do nome da organização, o nome do aplicativo e um identificador numérico, por exemplo, contoso-bizapp-func-20.">nome exclusivo</abbr>. O `<APP_NAME>` também é o domínio do DNS padrão para o aplicativo de funções. 

    <br/>
    <details>
    <summary><strong>Qual é o custo dos recursos provisionados no Azure?</strong></summary>

    Este comando cria um aplicativo de funções que é executado no runtime da linguagem especificada no [Plano de Consumo do Azure Functions](consumption-plan.md), que é gratuito para o uso que você fará aqui. O comando também provisiona uma instância associada do Azure Application Insights no mesmo grupo de recursos, com a qual você pode monitorar seu aplicativo de funções e exibir logs. Para saber mais, consulte [Monitorar Azure Functions](functions-monitoring.md). A instância não gera nenhum custo até você ativá-la.
    </details>

<br/>

---

## <a name="6-deploy-the-function-project-to-azure"></a>6. Implantar o projeto de funções no Azure


**Copie** 'func azure funtionapp publish <APP_NAME> no terminal **Substitua** `<APP_NAME>` pelo nome do aplicativo.
**Executar**

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

Copie a **URL de Invocação** completa mostrada na saída do comando `publish` na barra de endereços de um navegador. **Acrescente** o parâmetro de consulta **&name=Functions**. 

![A saída da função executada no Azure em um navegador](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

<br/>

---

## <a name="8-clean-up-resources"></a>8. Limpar os recursos

Se você prosseguir para a [próxima etapa](#next-steps) e adicionar uma saída da fila do Armazenamento do Azure <abbr title="Uma conexão declarativa entre uma função e outros recursos. Uma associação de entrada fornece dados à função; uma associação de saída fornece dados da função para outros recursos.">associação</abbr>mantenha todos os seus recursos no local para dar continuidade ao que já fez.

Caso contrário, use o comando a seguir para excluir o grupo de recursos e todos os recursos contidos nele para evitar custos adicionais.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name AzureFunctionsQuickstart-rg
```

---

<br/>

---

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Conectar-se a uma fila do Armazenamento do Azure](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-csharp)

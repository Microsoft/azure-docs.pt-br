---
title: Desenvolver e publicar funções do .NET 5 usando o Azure Functions
description: Saiba como criar e depurar funções C# usando o .NET 5,0 e, em seguida, implantar o projeto local em hospedagem sem servidor no Azure Functions.
ms.date: 03/03/2021
ms.topic: how-to
zone_pivot_groups: development-environment-functions
ms.openlocfilehash: 9751d5d354d738a64d6b02e8153464491d78ec14
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103201359"
---
# <a name="develop-and-publish-net-5-functions-using-azure-functions"></a>Desenvolver e publicar funções do .NET 5 usando o Azure Functions 

Este artigo mostra como trabalhar com funções C# usando o .NET 5,0, que é executado fora do processo do tempo de execução de Azure Functions. Você aprenderá a criar, depurar localmente e publicar essas funções de processo isolado do .NET no Azure. No Azure, essas funções são executadas em um processo isolado que dá suporte ao .NET 5,0. Para saber mais, consulte o [guia para executar funções no .net 5,0 no Azure](dotnet-isolated-process-guide.md).

Se você não precisar dar suporte ao .NET 5,0 ou executar suas funções fora do processo, convém [criar uma função de biblioteca de classes C#](functions-create-your-first-function-visual-studio.md).

>[!NOTE]
>No momento, não há suporte para o desenvolvimento de funções de processo isoladas do .NET no portal do Azure. Você deve usar a publicação CLI do Azure ou Visual Studio Code para criar um aplicativo de funções no Azure que ofereça suporte à execução de aplicativos .NET 5,0 fora do processo.   

## <a name="prerequisites"></a>Pré-requisitos

+ Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [.NET SDK 5.0](https://www.microsoft.com/net/download)

+ [Azure Functions Core Tools](functions-run-local.md#v2) versão 3.0.3381 ou uma versão posterior.

+ [CLI do Azure](/cli/azure/install-azure-cli) versão 2,20 ou uma versão posterior.  
::: zone pivot="development-environment-vscode"
+ [Visual Studio Code](https://code.visualstudio.com/) em uma das [plataformas compatíveis](https://code.visualstudio.com/docs/supporting/requirements#_platforms).  

+ A [Extensão do C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) para Visual Studio Code.  

+ A [extensão Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para Visual Studio Code, versão 1.3.0 ou mais recente.
::: zone-end
::: zone pivot="development-environment-vs"
+ [Visual Studio 2019](https://azure.microsoft.com/downloads/), incluindo a carga de trabalho de **desenvolvimento do Azure** .  
Os modelos de projeto de funções isoladas do .NET e a publicação não estão disponíveis no Visual Studio.
::: zone-end

## <a name="create-a-local-function-project"></a>Criar um projeto de função local

No Azure Functions, um projeto de função é um contêiner para uma ou mais funções individuais que respondem, cada uma, a um gatilho específico. Todas as funções em um projeto compartilham as configurações locais e de hospedagem. Nesta seção, você cria um projeto de função que contém apenas uma função.

::: zone pivot="development-environment-vs"

>[!NOTE]  
> Neste momento, não há nenhum modelo de projeto do Visual Studio que ofereça suporte à criação de projetos de funções isoladas .NET. Este artigo mostra como usar as ferramentas básicas para criar seu projeto C#, que você pode executar localmente e depurar no Visual Studio.  

::: zone-end

::: zone pivot="development-environment-vscode"  
1. Escolha o ícone do Azure na Barra de atividade e, em seguida, na área **Azure: Functions** e selecione o ícone **Criar projeto...** .

    ![Escolher Criar um projeto](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Escolha um local de diretório para o workspace do projeto e escolha **Selecionar**.

    > [!NOTE]
    > Estas etapas foram projetadas para serem concluídas fora de um workspace. Nesse caso, não selecione uma pasta de projeto que faz parte de um workspace.

1. Forneça as seguintes informações nos prompts:

    + **Selecione uma linguagem de programação para o seu projeto de função**: Escolha `C#`.

    + **Selecione um tempo de execução .net**: escolha `.NET 5 isolated` .

    + **Selecione um modelo para a primeira função do projeto**: Escolha `HTTP trigger`.

    + **Forneça um nome de função**: Digite `HttpExample`.

    + **Forneça um namespace**: Digite `My.Functions`.

    + **Nível de autorização**: Escolha `Anonymous`, que permite que qualquer pessoa chame seu ponto de extremidade de função. Para saber mais sobre o nível de autorização, confira [Chaves de autorização](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Selecione como você gostaria de abrir seu projeto**: Escolha `Add to workspace`.

1. Usando essas informações, o Visual Studio Code gera um projeto do Azure Functions com um gatilho HTTP. Você pode exibir os arquivos de projeto locais no Explorer. Para saber mais sobre os arquivos criados, confira [Arquivos de projeto gerados](functions-develop-vs-code.md#generated-project-files).
::: zone-end  
::: zone pivot="development-environment-cli,development-environment-vs"  

1. Execute o `func init` comando, da seguinte maneira, para criar um projeto do Functions em uma pasta chamada *LocalFunctionProj*:  

    ```console
    func init LocalFunctionProj --worker-runtime dotnetisolated
    ```
    
    Especificar `dotnetisolated` cria um projeto que é executado no .net 5,0.


1. Navegue até a pasta do projeto:

    ```console
    cd LocalFunctionProj
    ```

    Essa pasta contém vários arquivos para o projeto, incluindo o [local.settings.js](functions-run-local.md#local-settings-file) e [host.jsem arquivos de](functions-host-json.md) configuração. Como *local.settings.json* pode conter segredos baixados do Azure, o arquivo é excluído do controle do código-fonte por padrão no arquivo *.gitignore*.

1. Adicione uma função ao projeto usando o comando a seguir, em que o argumento `--name` é o nome exclusivo da função (HttpExample) e o argumento `--template` especifica o gatilho da função (HTTP).

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 

    `func new` Cria um arquivo de código HttpExample. cs.
::: zone-end  

::: zone pivot="development-environment-vscode"  

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end  
::: zone pivot="development-environment-cli" 

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

::: zone-end

::: zone pivot="development-environment-vs"

## <a name="run-the-function-locally"></a>Executar a função localmente

Neste ponto, você pode executar o `func start` comando da raiz da pasta do projeto para compilar e executar o projeto de funções isoladas em C#. No momento, se você quiser depurar seu código de função fora do processo no Visual Studio, você precisará anexar manualmente um depurador ao processo de tempo de execução do Functions em execução usando as seguintes etapas:  

1. Abra o arquivo de projeto (. csproj) no Visual Studio. Você pode revisar e modificar o código do projeto e definir os pontos de quebra desejados no código. 

1. Na pasta do projeto raiz, use o seguinte comando do terminal ou de um prompt de comando para iniciar o host de tempo de execução:

    ```console
    func start --dotnet-isolated-debug
    ```

    A `--dotnet-isolated-debug` opção informa o processo para aguardar a anexação de um depurador antes de continuar. Em direção ao final da saída, você verá algo semelhante às seguintes linhas: 
    
    <pre>
    ...
    
    Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample

    For detailed output, run func with --verbose flag.
    [2021-03-09T08:41:41.904Z] Azure Functions .NET Worker (PID: 81720) initialized in debug mode. Waiting for debugger to attach...
    ...
    
    </pre> 

    O `PID: XXXXXX` indica a ID do processo (PID) do processo de dotnet.exe que é o host do Functions em execução.
 
1. Na saída do Azure Functions Runtime, anote a ID do processo do host, à qual você anexará um depurador. Observe também a URL de sua função local.

1. No menu **depurar** no Visual Studio, selecione **anexar ao processo...**, localize o processo que corresponde à ID do processo e selecione **anexar**. 
    
    :::image type="content" source="media/dotnet-isolated-process-developer-howtos/attach-to-process.png" alt-text="Anexar o depurador ao processo de host do Functions":::    

    Com o depurador anexado, você pode depurar seu código de função normalmente.

1. Na barra de endereços do navegador, digite a URL da função local, que é semelhante ao seguinte e execute a solicitação. 

    <http://localhost:7071/api/HttpExample>

    Você deverá ver a saída de rastreamento da solicitação gravada no terminal em execução. A execução de código é interrompida em qualquer ponto de interrupção que você definir em seu código de função.

1. Quando terminar, vá para o terminal e pressione CTRL + C para interromper o processo de host.
 
Após verificar se a função foi executada corretamente no computador local, é hora de publicar o projeto no Azure.

> [!NOTE]  
> A publicação do Visual Studio não está disponível atualmente para aplicativos de processo isolado do .NET. Depois de terminar de desenvolver seu projeto no Visual Studio, você deve usar o CLI do Azure para criar os recursos remotos do Azure. Em seguida, você pode usar novamente Azure Functions Core Tools da linha de comando para publicar seu projeto no Azure.
::: zone-end

::: zone pivot="development-environment-cli,development-environment-vs" 
## <a name="create-supporting-azure-resources-for-your-function"></a>Criar recursos de suporte do Azure para a função

Antes de poder implantar o código da função no Azure, você precisa criar três recursos:

- Um [grupo de recursos](../azure-resource-manager/management/overview.md), que é um contêiner lógico para recursos relacionados.
- Uma [conta de armazenamento](../storage/common/storage-account-create.md), que é usada para manter o estado e outras informações sobre suas funções.
- Um aplicativo de funções, que fornece o ambiente para a execução do código de função. Um aplicativo de funções é mapeado para seu projeto de função local e permite agrupar funções como uma unidade lógica para facilitar o gerenciamento, a implantação e o compartilhamento de recursos.

Use os comandos a seguir para criar esses itens.

1. Se você ainda não tiver feito isso, entre no Azure:

    ```azurecli
    az login
    ```

    O comando [az login](/cli/azure/reference-index#az_login) conecta você à conta do Azure.

1. Crie um grupo de recursos chamado `AzureFunctionsQuickstart-rg` na região `westeurope`:

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    O comando [az group create](/cli/azure/group#az_group_create) cria um grupo de recursos. De modo geral, você cria o grupo de recursos e os recursos em uma região próxima a você usando uma região disponível retornada do comando `az account list-locations`.

1. Crie uma conta de armazenamento para uso geral no grupo de recursos e na região:

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    O comando [az storage account create](/cli/azure/storage/account#az_storage_account_create) cria a conta de armazenamento. 

    No exemplo anterior, substitua `<STORAGE_NAME>` por um nome que seja apropriado para você e exclusivo no Armazenamento do Azure. Os nomes devem conter de 3 a 24 caracteres e podem conter somente números e letras minúsculas. `Standard_LRS` especifica uma conta de uso geral, que é [compatível com o Functions](storage-considerations.md#storage-account-requirements).

1. Criar o aplicativo de funções no Azure:
   
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet-isolated --runtime-version 5.0 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```

    O comando [az functionapp create](/cli/azure/functionapp#az_functionapp_create) cria o aplicativo de funções no Azure. 
    
    No exemplo anterior, substitua `<STORAGE_NAME>` pelo nome da conta usada na etapa anterior e substitua `<APP_NAME>` por um nome globalmente exclusivo que seja apropriado para você. O `<APP_NAME>` também é o domínio do DNS padrão para o aplicativo de funções. 
    
    Este comando cria um aplicativo de funções executando o .NET 5,0 no [plano de consumo Azure Functions](consumption-plan.md). Esse plano deve ser gratuito para a quantidade de uso que você incorre neste artigo. O comando também provisiona uma instância do Aplicativo Azure insights associada no mesmo grupo de recursos. Use esta instância para monitorar seu aplicativo de funções e exibir logs. Para saber mais, consulte [Monitorar Azure Functions](functions-monitoring.md). A instância não gera nenhum custo até você ativá-la.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

::: zone-end

::: zone pivot="development-environment-vscode"

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-the-project-to-azure"></a>Publicar o projeto no Azure

Nesta seção, você criará um aplicativo de funções e os recursos relacionados em sua assinatura do Azure e, em seguida, implantará seu código.

> [!IMPORTANT]
> Publicar em um aplicativo de funções existente substitui o conteúdo desse aplicativo no Azure.


1. Escolha o ícone do Azure na Barra de atividade e, em seguida, na área **Azure: Functions**, escolha o botão **Implantar no aplicativo de funções...** .

    ![Publicar seu projeto no Azure](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Forneça as seguintes informações nos prompts:

    - **Selecione a pasta**: escolha uma pasta do seu workspace ou navegue até uma que contenha seu aplicativo de funções. Você não verá esse prompt quando já tiver um aplicativo de funções válido aberto.

    - **Selecione a assinatura**: Escolha a assinatura a ser usada. Você não verá esse prompt quando tiver apenas uma assinatura.

    - **Selecione o aplicativo de funções no Azure**: Escolha `- Create new Function App`. (Não escolha a opção `Advanced`, que não é abordada neste artigo.)
      
    - **Insira um nome exclusivo globalmente para o aplicativo de funções**: Digite um nome que seja válido em um caminho de URL. O nome que você digitar é validado para ter certeza de que ele é exclusivo no Azure Functions.
    
    - **Selecione uma pilha de runtime**: Escolha `.NET 5 (non-LTS)`. 
    
    - **Selecione uma localização para novos recursos**:  Para obter um melhor desempenho, escolha uma [região](https://azure.microsoft.com/regions/) perto de você. 
    
    Na área de notificação, você vê o status de recursos individuais à medida que eles são criados no Azure.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Notificação de criação de recurso do Azure":::
    
1.  Quando concluído, os seguintes recursos do Azure serão criados em sua assinatura, usando nomes baseados em seu nome do aplicativo de funções:
    
    [!INCLUDE [functions-vs-code-created-resources](../../includes/functions-vs-code-created-resources.md)]

    Uma notificação é exibida depois que seu aplicativo de funções é criado e o pacote de implantação é aplicado. 

    [!INCLUDE [functions-vs-code-create-tip](../../includes/functions-vs-code-create-tip.md)]

4. Escolha **Exibir Saída** nessa notificação para exibir a criação e os resultados da implantação, incluindo os recursos do Azure que você criou. Se você perder a notificação, selecione o ícone de sino no canto inferior direito para vê-lo novamente.

    ![Criar notificação completa](../../includes/media/functions-publish-project-vscode/function-create-notifications.png)

::: zone-end

::: zone pivot="development-environment-cli"  
[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]  
::: zone-end  

::: zone pivot="development-environment-vscode"  
[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]  
::: zone-end  

## <a name="clean-up-resources"></a>Limpar recursos

Você criou recursos para concluir este artigo. Você pode ser cobrado por esses recursos, dependendo do [status de conta](https://azure.microsoft.com/account/) e [preços do serviço](https://azure.microsoft.com/pricing/). 

::: zone pivot="development-environment-cli"  
Use o comando a seguir para excluir o grupo de recursos e todos os seus recursos contidos para evitar custos adicionais.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```
::: zone-end  

::: zone pivot="development-environment-vscode"  
Use as etapas a seguir para excluir o aplicativo de funções e seus recursos relacionados para evitar incorrer em custos adicionais.

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]  
::: zone-end  
::: zone pivot="development-environment-vs"   
Use as etapas a seguir para excluir o aplicativo de funções e seus recursos relacionados para evitar incorrer em custos adicionais.

1. No Cloud Explorer, expanda sua assinatura > **Serviços de Aplicativos**, clique com o botão direito do mouse no aplicativo de funções e escolha **Abrir no Portal**. 

1. Na página do aplicativo de funções, selecione a guia **Visão geral** e depois selecione o link em **Grupo de recursos**.

   :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-app-delete-resource-group.png" alt-text="Selecione o grupo de recursos a ser excluído da página do aplicativo de funções":::

2. Na página **Grupo de recursos**, examine a lista de recursos incluídos e verifique se eles são aqueles que deseja excluir.
 
3. Selecione **Excluir grupo de recursos** e siga as instruções.

   A exclusão poderá levar alguns minutos. Ao ser concluída, uma notificação será exibida por alguns segundos. Também é possível selecionar o ícone de sino na parte superior da página para exibir a notificação.
::: zone-end

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções isoladas do .NET](dotnet-isolated-process-guide.md)


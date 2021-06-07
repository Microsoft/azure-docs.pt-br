---
title: Crie Azure Functions no Linux usando uma imagem personalizada
description: Saiba como criar Azure Functions em execução em uma imagem personalizada do Linux.
ms.date: 12/2/2020
ms.topic: tutorial
ms.custom: devx-track-csharp, mvc, devx-track-python, devx-track-azurepowershell, devx-track-azurecli
zone_pivot_groups: programming-languages-set-functions-full
ms.openlocfilehash: 7950bfb4a57db812da87f4e5f76f3075d50a8293
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782257"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>Criar uma função no Linux usando um contêiner personalizado

Neste tutorial, você cria e implanta seu código no Azure Functions como um contêiner do Docker personalizado usando uma imagem base do Linux. Normalmente, você usa uma imagem personalizada quando suas funções exigem uma versão de linguagem específica ou têm uma configuração ou dependência específica que não é fornecida pela imagem interna.

::: zone pivot="programming-language-other"
O Azure Functions dá suporte a qualquer linguagem ou runtime usando [manipuladores personalizados](functions-custom-handlers.md). Para algumas linguagens, como a linguagem de programação R usada neste tutorial, você precisará instalar o runtime ou bibliotecas adicionais como dependências que exigem o uso de um contêiner personalizado.
::: zone-end

Para implantar seu código de função em um contêiner Linux personalizado, você precisa ter a hospedagem em um [Plano Premium](functions-premium-plan.md) ou em um [Plano dedicado (Serviço de Aplicativo)](dedicated-plan.md). A conclusão deste tutorial gera custos de alguns dólares dos EUA em sua conta do Azure, que você pode minimizar [limpando os recursos](#clean-up-resources) quando terminar.

Você também pode usar um contêiner padrão do Serviço de Aplicativo do Azure, conforme descrito em [Criar sua primeira função hospedada em Linux](./create-first-function-cli-csharp.md?pivots=programming-language-python). Imagens base com suporte para Azure Functions encontram-se no [repositório de imagens de base do Azure Functions](https://hub.docker.com/_/microsoft-azure-functions-base).

Neste tutorial, você aprenderá como:

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
> [!div class="checklist"]
> * Criar um aplicativo de funções e um Dockerfile usando o Azure Functions Core Tools.
> * Compile uma imagem personalizada usando o Docker.
> * Publique uma imagem personalizada em um registro de contêiner.
> * Criar recursos de suporte no Azure para o aplicativo de funções
> * Implante um aplicativo de funções do Hub do Docker.
> * Adicione configurações de aplicativo ao aplicativo de funções.
> * Habilitar a implantação contínua.
> * Habilitar conexões SSH para o contêiner.
> * Adicionar uma associação de saída de armazenamento de filas. 
::: zone-end
::: zone pivot="programming-language-other"
> [!div class="checklist"]
> * Criar um aplicativo de funções e um Dockerfile usando o Azure Functions Core Tools.
> * Compile uma imagem personalizada usando o Docker.
> * Publique uma imagem personalizada em um registro de contêiner.
> * Criar recursos de suporte no Azure para o aplicativo de funções
> * Implante um aplicativo de funções do Hub do Docker.
> * Adicione configurações de aplicativo ao aplicativo de funções.
> * Habilitar a implantação contínua.
> * Habilitar conexões SSH para o contêiner.
::: zone-end

Você pode seguir este tutorial em qualquer computador que execute o Windows, o macOS ou o Linux. 

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

<!---Requirements specific to Docker --->
+ [Docker](https://docs.docker.com/install/)  

+ Uma [ID do Docker](https://hub.docker.com/signup)

[!INCLUDE [functions-cli-verify-prereqs](../../includes/functions-cli-verify-prereqs.md)]

+ Execute `docker login` para entrar no Docker. Esse comando falhará se o Docker não estiver em execução; nesse caso, inicie o Docker e tente o comando novamente.

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-and-test-the-local-functions-project"></a>Criar e testar o projeto de funções local

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Em um terminal ou prompt de comando, execute o comando a seguir referente à linguagem escolhida para criar um projeto de aplicativo de funções em uma pasta chamada `LocalFunctionsProject`.  
::: zone-end  
::: zone pivot="programming-language-csharp"  
```console
func init LocalFunctionsProject --worker-runtime dotnet --docker
```
::: zone-end  
::: zone pivot="programming-language-javascript"  
```console
func init LocalFunctionsProject --worker-runtime node --language javascript --docker
```
::: zone-end  
::: zone pivot="programming-language-powershell"  
```console
func init LocalFunctionsProject --worker-runtime powershell --docker
```
::: zone-end  
::: zone pivot="programming-language-python"  
```console
func init LocalFunctionsProject --worker-runtime python --docker
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```console
func init LocalFunctionsProject --worker-runtime node --language typescript --docker
```
::: zone-end
::: zone pivot="programming-language-java"  
Em uma pasta vazia, execute o seguinte comando para gerar o projeto do Functions a partir de um [arquétipo Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

# <a name="bash"></a>[Bash](#tab/bash)
```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=8 -Ddocker
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" "-Ddocker"
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" "-Ddocker"
```
---

O parâmetro `-DjavaVersion` informa ao runtime do Functions a versão do Java a ser usada. Use `-DjavaVersion=11` se desejar que as funções sejam executadas no Java 11. Quando você não especifica `-DjavaVersion`, o Maven assume o Java 8 como padrão. Para obter mais informações, confira [Versões do Java](functions-reference-java.md#java-versions).

> [!IMPORTANT]
> A variável de ambiente `JAVA_HOME` precisa ser definida como a localização de instalação da versão correta do JDK para concluir este artigo.

O Maven solicita os valores necessários para concluir a geração do projeto na implantação.   
Forneça os seguintes valores quando solicitado:

| Prompt | Valor | Descrição |
| ------ | ----- | ----------- |
| **groupId** | `com.fabrikam` | Um valor que identifica exclusivamente o projeto em todos os projetos, seguindo as [regras de nomenclatura do pacote](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) para Java. |
| **artifactId** | `fabrikam-functions` | Um valor que é o nome do jar, sem um número de versão. |
| **version** | `1.0-SNAPSHOT` | Escolha o valor padrão. |
| **package** | `com.fabrikam.functions` | Um valor que é o pacote Java para o código de função gerado. Use o padrão. |

Digite `Y` ou pressione Enter para confirmar.

O Maven cria os arquivos de projeto em uma nova pasta com o nome _artifactId_, que, neste exemplo, é `fabrikam-functions`. 
::: zone-end

::: zone pivot="programming-language-other"  
```console
func init LocalFunctionsProject --worker-runtime custom --docker
```
::: zone-end

A opção `--docker` gera um `Dockerfile` para o projeto, que define um contêiner personalizado adequado para uso com o Azure Functions e com o runtime selecionado.

Navegue até a pasta do projeto:
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-other"  
```console
cd LocalFunctionsProject
```
::: zone-end  
::: zone pivot="programming-language-java"  
```console
cd fabrikam-functions
```
::: zone-end  
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
Adicione uma função ao projeto usando o comando a seguir, em que o argumento `--name` é o nome exclusivo da função e o argumento `--template` especifica o gatilho da função. `func new` cria uma subpasta correspondente ao nome da função que contém um arquivo de código apropriado para a linguagem escolhida do projeto, bem como um arquivo de configuração chamado *function.json*.

```console
func new --name HttpExample --template "HTTP trigger"
```
::: zone-end

::: zone pivot="programming-language-other" 
Adicione uma função ao projeto usando o comando a seguir, em que o argumento `--name` é o nome exclusivo da função e o argumento `--template` especifica o gatilho da função. `func new` cria uma subpasta correspondente ao nome da função que contém um arquivo de configuração chamado *function.json*.

```console
func new --name HttpExample --template "HTTP trigger"
```

Em um editor de texto, crie um arquivo na pasta do projeto chamado *handler.R*. Adicione o trecho mostrado a seguir como o conteúdo.

```r
library(httpuv)

PORTEnv <- Sys.getenv("FUNCTIONS_CUSTOMHANDLER_PORT")
PORT <- strtoi(PORTEnv , base = 0L)

http_not_found <- list(
  status=404,
  body='404 Not Found'
)

http_method_not_allowed <- list(
  status=405,
  body='405 Method Not Allowed'
)

hello_handler <- list(
  GET = function (request) {
    list(body=paste(
      "Hello,",
      if(substr(request$QUERY_STRING,1,6)=="?name=") 
        substr(request$QUERY_STRING,7,40) else "World",
      sep=" "))
  }
)

routes <- list(
  '/api/HttpExample' = hello_handler
)

router <- function (routes, request) {
  if (!request$PATH_INFO %in% names(routes)) {
    return(http_not_found)
  }
  path_handler <- routes[[request$PATH_INFO]]

  if (!request$REQUEST_METHOD %in% names(path_handler)) {
    return(http_method_not_allowed)
  }
  method_handler <- path_handler[[request$REQUEST_METHOD]]

  return(method_handler(request))
}

app <- list(
  call = function (request) {
    response <- router(routes, request)
    if (!'status' %in% names(response)) {
      response$status <- 200
    }
    if (!'headers' %in% names(response)) {
      response$headers <- list()
    }
    if (!'Content-Type' %in% names(response$headers)) {
      response$headers[['Content-Type']] <- 'text/plain'
    }

    return(response)
  }
)

cat(paste0("Server listening on :", PORT, "...\n"))
runServer("0.0.0.0", PORT, app)
```

Em *host.json*, modifique a seção `customHandler` para configurar o comando de inicialização do manipulador personalizado.

```json
"customHandler": {
  "description": {
      "defaultExecutablePath": "Rscript",
      "arguments": [
      "handler.R"
    ]
  },
  "enableForwardingHttpRequest": true
}
```
::: zone-end

Para testar a função localmente, inicie o host de runtime local do Azure Functions na raiz da pasta do projeto: 
::: zone pivot="programming-language-csharp"  
```console
func start --build  
```
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-powershell,programming-language-python"   
```console
func start  
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```console
npm install
npm start
```
::: zone-end  
::: zone pivot="programming-language-java"  
```console
mvn clean package  
mvn azure-functions:run
```
::: zone-end
::: zone pivot="programming-language-other"
```console
R -e "install.packages('httpuv', repos='http://cran.rstudio.com/')"
func start
```
::: zone-end 

Quando vir o ponto de extremidade `HttpExample` na saída, navegue até `http://localhost:7071/api/HttpExample?name=Functions`. O navegador deve exibir uma mensagem "olá" que ecoa de volta `Functions`, o valor fornecido para o parâmetro de consulta `name`.

Use **CTRL**-**C** para interromper o host.

## <a name="build-the-container-image-and-test-locally"></a>Compilar a imagem de contêiner e testá-la localmente

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-powershell,programming-language-python,programming-language-java,programming-language-typescript"
(Opcional) Examine o *Dockerfile* na raiz da pasta do projeto. O Dockerfile descreve o ambiente necessário para executar o aplicativo de funções no Linux.  A lista completa de imagens base com suporte para Azure Functions encontram-se na [página de imagens de base do Azure Functions](https://hub.docker.com/_/microsoft-azure-functions-base).
::: zone-end

::: zone pivot="programming-language-other"
Examine o *Dockerfile* na raiz da pasta do projeto. O Dockerfile descreve o ambiente necessário para executar o aplicativo de funções no Linux. Os aplicativos do manipulador personalizado usam a imagem `mcr.microsoft.com/azure-functions/dotnet:3.0-appservice` como base.

Modifique o *Dockerfile* para instalar o R. Substitua o conteúdo do *Dockerfile* pelo mostrado a seguir.

```dockerfile
FROM mcr.microsoft.com/azure-functions/dotnet:3.0-appservice 
ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
    AzureFunctionsJobHost__Logging__Console__IsEnabled=true

RUN apt update && \
    apt install -y r-base && \
    R -e "install.packages('httpuv', repos='http://cran.rstudio.com/')"

COPY . /home/site/wwwroot
```
::: zone-end

Na pasta do projeto raiz, execute o comando [docker build](https://docs.docker.com/engine/reference/commandline/build/) e forneça um nome, `azurefunctionsimage`, e uma tag, `v1.0.0`. Substitua `<DOCKER_ID>` pela ID da conta do Hub do Docker. Esse comando compila a imagem do Docker para o contêiner.

```console
docker build --tag <DOCKER_ID>/azurefunctionsimage:v1.0.0 .
```

Quando o comando for concluído, você poderá executar o novo contêiner localmente.
    
Para testar o build, execute a imagem em um contêiner local usando o comando [docker run](https://docs.docker.com/engine/reference/commandline/run/), substituindo novamente `<DOCKER_ID` pela ID do Docker e adicionando o argumento de portas, `-p 8080:80`:

```console
docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
```

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-other"  
Quando a imagem estiver em execução em um contêiner local, abra um navegador para `http://localhost:8080`, o que deve exibir a imagem de espaço reservado mostrada abaixo. A imagem aparece neste ponto porque a função está sendo executada no contêiner local, como aconteceria no Azure, o que significa que ela está protegida por uma chave de acesso, conforme definido em *function.json* com a propriedade `"authLevel": "function"`. No entanto, o contêiner ainda não foi publicado em um aplicativo de funções no Azure, de modo que a chave ainda não está disponível. Se desejar testar no contêiner local, pare o Docker, altere a propriedade de autorização para `"authLevel": "anonymous"`, recompile a imagem e reinicie o Docker. Em seguida, redefina `"authLevel": "function"` em *function.json*. Para obter mais informações, confira [chaves de autorização](functions-bindings-http-webhook-trigger.md#authorization-keys).

![Imagem de espaço reservado indicando que o contêiner está sendo executado localmente](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

::: zone-end
::: zone pivot="programming-language-java"  
Quando a imagem estiver em execução em um contêiner local, navegue até `http://localhost:8080/api/HttpExample?name=Functions`, que deverá exibir a mesma mensagem "olá" que anteriormente. Como o arquétipo Maven gera uma função disparada por HTTP que usa autorização anônima, você ainda poderá chamar a função mesmo se ela estiver em execução no contêiner. 
::: zone-end  

Depois de verificar o aplicativo de funções no contêiner, pare o Docker pressionando **Ctrl**+**C**.

## <a name="push-the-image-to-docker-hub"></a>Enviar a imagem por push para o Docker Hub

Um Docker Hub é um Registro de Contêiner que hospeda imagens e fornece serviços de contêiner e imagem. Para compartilhar a imagem, que inclui sua implantação no Azure, você precisa enviá-la por push para um Registro.

1. Se ainda não tiver entrado no Docker, faça isso usando o comando [docker login](https://docs.docker.com/engine/reference/commandline/login/), substituindo `<docker_id>` pela ID do Docker. Esse comando solicita seu nome de usuário e sua senha. A mensagem “Logon com Êxito” confirma que você está conectado.

    ```console
    docker login
    ```
    
1. Depois de conectado, envie a imagem por push para o Docker Hub usando o comando [docker push](https://docs.docker.com/engine/reference/commandline/push/), mais uma vez substituindo `<docker_id>` pela ID do Docker.

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```

1. Dependendo da velocidade da rede, o primeiro envio da imagem por push pode levar alguns minutos (o envio de alterações posteriores é muito mais rápido). Enquanto estiver aguardando, você pode prosseguir para a próxima seção e criar recursos do Azure em outro terminal.

## <a name="create-supporting-azure-resources-for-your-function"></a>Criar recursos de suporte do Azure para a função

Para implantar o código da função no Azure, você precisa criar três recursos:

- Um grupo de recursos, que é um contêiner lógico para recursos relacionados.
- Uma conta de Armazenamento do Azure, que mantém o estado e outras informações sobre seus projetos.
- Um aplicativo de funções, que fornece o ambiente para a execução do código de função. Um aplicativo de funções é mapeado para seu projeto de função local e permite agrupar funções como uma unidade lógica para facilitar o gerenciamento, a implantação e o compartilhamento de recursos.

Use comandos da CLI do Azure para criar esses itens. Cada comando fornece uma saída em JSON após a conclusão.

1. Entre no Azure usando o comando [az login](/cli/azure/reference-index#az_login):

    ```azurecli
    az login
    ```
    
1. Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az_group_create). O exemplo a seguir cria um grupo de recursos chamado `AzureFunctionsContainers-rg` na região `westeurope`. (De modo geral, você cria o grupo de recursos e os recursos em uma região próxima a você usando uma região disponível com o comando `az account list-locations`.)

    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location westeurope
    ```
    
    > [!NOTE]
    > Você não pode hospedar aplicativos Windows e Linux no mesmo grupo de recursos. Se você tiver um grupo de recursos chamado `AzureFunctionsContainers-rg` com um aplicativo de funções ou um aplicativo Web do Windows, você precisará usar um grupo de recursos diferente.
    
1. Crie uma conta de armazenamento para uso geral em seu grupo de recursos e região usando o comando [az storage account create](/cli/azure/storage/account#az_storage_account_create). No exemplo a seguir, substitua `<storage_name>` por um nome exclusivo globalmente que for adequado para você. Os nomes devem conter de 3 a 24 caracteres e podem conter somente números e letras minúsculas. `Standard_LRS` especifica uma conta para uso geral típica.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```
    
    Para este tutorial, a conta de armazenamento gera um custo de apenas alguns centavos de dólar.
    
1. Use o comando para criar um plano Premium do Azure Functions chamado `myPremiumPlan`, com o tipo de preço **Elástico Premium 1** (`--sku EP1`), na região Europa Ocidental (`-location westeurope`, ou use uma região adequada próxima a você) e em um contêiner Linux (`--is-linux`).

    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location westeurope --number-of-workers 1 --sku EP1 --is-linux
    ```   

    Aqui, usamos o plano Premium, que pode ser dimensionado conforme necessário. Para saber mais sobre hospedagem, confira [Comparação de planos de hospedagem do Azure Functions](functions-scale.md). Para calcular os custos, confira a [Página de preços do Functions](https://azure.microsoft.com/pricing/details/functions/).

    O comando também provisiona uma instância associada do Azure Application Insights no mesmo grupo de recursos, com a qual você pode monitorar seu aplicativo de funções e exibir logs. Para saber mais, consulte [Monitorar Azure Functions](functions-monitoring.md). A instância não gera nenhum custo até você ativá-la.

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>Criar e configurar um aplicativo de funções no Azure usando a imagem

Um aplicativo de funções no Azure gerencia a execução das funções em seu plano de hospedagem. Nesta seção, você usa os recursos do Azure da seção anterior para criar um aplicativo de funções baseado em uma imagem no Docker Hub e configurá-lo com uma cadeia de conexão para o Armazenamento do Azure.

1. Crie o aplicativo do Functions usando o comando [az functionapp create](/cli/azure/functionapp#az_functionapp_create). No exemplo a seguir, substitua `<storage_name>` pelo nome usado na seção anterior para a conta de armazenamento. Além disso, substitua `<app_name>` por um nome exclusivo globalmente que for adequado para você e `<docker_id>` pela ID do Docker.

    ::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --runtime <functions runtime stack> --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    ::: zone-end
    ::: zone pivot="programming-language-other"
    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --runtime custom --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    ::: zone-end
    
    O parâmetro *deployment-container-image-name* especifica a imagem a ser usada para o aplicativo de funções. Você pode usar o comando [az functionapp config container show](/cli/azure/functionapp/config/container#az_functionapp_config_container_show) para exibir informações sobre a imagem usada para a implantação. Use também o comando [az functionapp config container set](/cli/azure/functionapp/config/container#az_functionapp_config_container_set) para implantação com base em outra imagem.

1. Exiba a cadeia de conexão da conta de armazenamento que você criou usando o comando [az storage account show-connection-string](/cli/azure/storage/account). Substitua `<storage-name>` pelo nome da conta de armazenamento criada acima:

    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv
    ```
    
1. Adicione essa configuração ao aplicativo de funções usando o comando [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az_functionapp_config_ppsettings_set). No seguinte comando, substitua `<app_name>` pelo nome do aplicativo de funções e substitua `<connection_string>` pela cadeia de conexão da etapa anterior (uma cadeia de caracteres longa codificada que começa com "DefaultEndpointProtocol="):
 
    ```azurecli
    az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=<connection_string>
    ```

    > [!TIP]
    > No Bash, você pode usar uma variável de shell para capturar a cadeia de conexão em vez de usar a área de transferência. Primeiro, use o seguinte comando para criar uma variável com a cadeia de conexão:
    > 
    > ```bash
    > storageConnectionString=$(az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv)
    > ```
    > 
    > Em seguida, faça referência à variável no segundo comando:
    > 
    > ```azurecli
    > az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=$storageConnectionString
    > ```

1. Agora, a função pode usar essa cadeia de conexão para acessar a conta de armazenamento.

> [!NOTE]    
> Se publicar a imagem personalizada em uma conta de contêiner particular, você deverá usar as variáveis de ambiente no Dockerfile para a cadeia de conexão. Para obter mais informações, confira a [Instrução ENV](https://docs.docker.com/engine/reference/builder/#env). Você também precisa definir as variáveis `DOCKER_REGISTRY_SERVER_USERNAME` e `DOCKER_REGISTRY_SERVER_PASSWORD`. Em seguida, para usar os valores, você deve recompilar a imagem, enviá-la por push para o Registro e, então, reiniciar o aplicativo de funções no Azure.

## <a name="verify-your-functions-on-azure"></a>Verificar as funções no Azure

Com a imagem implantada no aplicativo de funções no Azure, você pode invocar a função usando solicitações HTTP. Como a definição de *function.json* inclui a propriedade `"authLevel": "function"`, primeiro você precisa obter a chave de acesso (também chamada de "chave de função") e incluí-la como um parâmetro de URL em qualquer solicitação para o ponto de extremidade.

1. Recupere a URL da função com a chave de acesso (função) usando o portal do Azure ou a CLI do Azure, com o comando `az rest`.)

    # <a name="portal"></a>[Portal](#tab/portal)

    1. Entre no portal do Azure, em seguida, procure e selecione **Aplicativo de Funções**.

    1. Selecione a função que você deseja verificar.

    1. No painel de navegação esquerdo, selecione **Funções** e, em seguida, selecione a função que você deseja verificar.

        ![Escolher a sua função no portal do Azure](./media/functions-create-function-linux-custom-image/functions-portal-select-function.png)   

    
    1. Selecione **Obter a URL da função**.

        ![Obter a URL da função no portal do Azure](./media/functions-create-function-linux-custom-image/functions-portal-get-function-url.png)   

    
    1. Na janela pop-up, selecione **padrão (chave de função)** e, em seguida, copie a URL para a área de transferência. A chave é a cadeia de caracteres que segue `?code=`.

        ![Escolha a chave de acesso da função padrão](./media/functions-create-function-linux-custom-image/functions-portal-copy-url.png)   


    > [!NOTE]  
    > Já que seu aplicativo de funções é implantado como um contêiner, você não pode fazer alterações no seu código de função no portal. Em vez disso, você precisa atualizar o projeto na imagem local, enviar a imagem por push para o Registro novamente e reimplantar no Azure. Você pode configurar a implantação contínua em uma seção posterior.
    
    # <a name="azure-cli"></a>[CLI do Azure](#tab/azurecli)

    1. Construa uma cadeia de caracteres de URL no seguinte formato, substituindo `<subscription_id>`, `<resource_group>` e `<app_name>` pela ID de assinatura do Azure, pelo grupo de recursos do aplicativo de funções e pelo nome do aplicativo de funções, respectivamente:

        ```
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Web/sites/<app_name>/host/default/listKeys?api-version=2018-11-01"
        ```

        Por exemplo, a URL pode se parecer com o seguinte endereço:

        ```
        "/subscriptions/1234aaf4-1234-abcd-a79a-245ed34eabcd/resourceGroups/AzureFunctionsContainers-rg/providers/Microsoft.Web/sites/msdocsfunctionscontainer/host/default/listKeys?api-version=2018-11-01"
        ```

        > [!TIP]
        > Para fins de conveniência, você pode atribuir a URL a uma variável de ambiente e usá-la no comando `az rest`.
    
    1. Execute o seguinte comando `az rest` (disponível na CLI do Azure versão 2.0.77 e posterior), substituindo `<uri>` pela cadeia de caracteres do URI da última etapa, incluindo as aspas:

        ```azurecli
        az rest --method post --uri <uri> --query functionKeys.default --output tsv
        ```

    1. A saída do comando é a chave de função. A URL da função completa, então, é `https://<app_name>.azurewebsites.net/api/<function_name>?code=<key>`, substituindo `<app_name>`, `<function_name>` e `<key>` pelos valores específicos.
    
        > [!NOTE]
        > A chave recuperada aqui é a chave de *host*, que funciona para todas as funções no aplicativo de funções. O método mostrado para o portal recupera somente a chave da única função.

    ---

1. Cole a URL da função na barra de endereços do navegador, adicionando o parâmetro `&name=Azure` ao final da URL. Um texto semelhante a"Olá, Azure" deve aparecer no navegador.

    ![Resposta da função no navegador.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

1. Para testar a autorização, remova o parâmetro `code=` da URL e verifique se você não recebe nenhuma resposta da função.


## <a name="enable-continuous-deployment-to-azure"></a>Habilitar a implantação contínua no Azure

Você pode habilitar o Azure Functions para atualizar automaticamente sua implantação de uma imagem sempre que você atualizar a imagem no Registro.

1. Habilite a implantação contínua usando o comando [az functionapp deployment container config](/cli/azure/functionapp/deployment/container#az_functionapp_deployment_container_config), substituindo `<app_name>` pelo nome do aplicativo de funções:

    ```azurecli
    az functionapp deployment container config --enable-cd --query CI_CD_URL --output tsv --name <app_name> --resource-group AzureFunctionsContainers-rg
    ```
    
    Esse comando habilita a implantação contínua e retorna a URL do webhook de implantação. (Você pode recuperar essa URL a qualquer momento usando o comando [az functionapp deployment container show-cd-url](/cli/azure/functionapp/deployment/container#az_functionapp_deployment_container_show_cd_url).)

1. Copie a URL do webhook de implantação para a área de transferência.

1. Abra o [Docker Hub](https://hub.docker.com/), entre e selecione **Repositórios** na barra de navegação. Localize e selecione a imagem, selecione a guia **Webhooks**, especifique um **Nome do webhook**, cole a URL em **URL do Webhook** e, em seguida, selecione **Criar**:

    ![Adicione o webhook em seu repositório DockerHub](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

1. Com o webhook definido, o Azure Functions reimplanta sua imagem sempre que você a atualiza no Docker Hub.

## <a name="enable-ssh-connections"></a>Habilitar conexões SSH

O SSH permite a comunicação segura entre um contêiner e um cliente. Com o SSH habilitado, você pode se conectar ao contêiner usando as Ferramentas Avançadas do Serviço de Aplicativo (Kudu). Para facilitar a conexão com o contêiner usando SSH, o Azure Functions fornece uma imagem base que já tem o SSH habilitado. Você só precisa editar o Dockerfile e, em seguida, recompilar e reimplantar a imagem. Em seguida, você pode se conectar ao contêiner usando as Ferramentas Avançadas (Kudu)

1. No Dockerfile, acrescente a cadeia de caracteres `-appservice` à imagem base na instrução `FROM`:

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/dotnet:3.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/powershell:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end
    
1. Recompile a imagem usando o comando `docker build` novamente, substituindo `<docker_id>` pela ID do Docker:

    ```console
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. Envie por push a imagem atualizada para o Docker Hub, o que deve levar bem menos tempo do que o primeiro push, pois somente os segmentos atualizados da imagem precisam ser carregados.

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. O Azure Functions reimplanta automaticamente a imagem em seu aplicativo de funções. O processo leva menos de um minuto.

1. Em um navegador, abra `https://<app_name>.scm.azurewebsites.net/`, substituindo `<app_name>` por seu nome exclusivo. Essa URL é o ponto de extremidade das Ferramentas Avançadas (Kudu) para seu contêiner do aplicativo de funções.

1. Entre em sua conta do Azure e selecione **SSH** para estabelecer uma conexão com o contêiner. A conexão poderá levar algum tempo se o Azure ainda estiver atualizando a imagem de contêiner.

1. Depois que a conexão com o contêiner for estabelecida, execute o comando `top` para exibir os processos em execução. 

    ![Comando top do Linux em execução em uma sessão de SSH](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="write-to-an-azure-storage-queue"></a>Gravar em uma fila do Armazenamento do Azure

O Azure Functions permite conectar suas funções a outros serviços e recursos do Azure sem precisar escrever um código de integração próprio. Essas *associações*, que representam a entrada e a saída, são declaradas na definição de função. Dados de associações são fornecidos à função como parâmetros. Um *gatilho* é um tipo especial de associação de entrada. Embora uma função tenha apenas um gatilho, ela pode ter várias associações de entrada e de saída. Para saber mais, confira [Conceitos de gatilhos e de associações do Azure Functions](functions-triggers-bindings.md).

Esta seção mostra como integrar sua função a uma fila do Armazenamento do Azure. A associação de saída que você adiciona a essa função escreve dados de uma solicitação HTTP em uma mensagem na fila.

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]
::: zone-end

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end  

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="add-code-to-use-the-output-binding"></a>Adicionar código para usar a associação de saída

Com a associação de fila definida, agora você pode atualizar sua função para receber o parâmetro de saída `msg` e gravar mensagens na fila.
::: zone-end

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

::: zone pivot="programming-language-java"
[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]
::: zone-end

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
### <a name="update-the-image-in-the-registry"></a>Atualizar a imagem no Registro

1. Na pasta raiz, execute `docker build` novamente e, desta vez, atualize a versão na marca para `v1.0.1`. Assim como antes, substitua `<docker_id>` pela ID da conta do Docker Hub:

    ```console
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1 .
    ```
    
1. Envie a imagem atualizada por push de volta para o repositório usando `docker push`:

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. Como você configurou a entrega contínua, atualizar a imagem no Registro novamente atualiza automaticamente seu aplicativo de funções no Azure.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Exibir a mensagem na fila do Armazenamento do Azure

Em um navegador, use a mesma URL que antes para invocar a função. O navegador deve exibir a mesma resposta que antes, porque você não modificou essa parte do código da função. O código adicionado, no entanto, gravou uma mensagem usando o parâmetro de URL `name` na fila de armazenamento `outqueue`.

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

::: zone-end

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser continuar trabalhando com o Azure Functions usando os recursos criados neste tutorial, mantenha todos esses recursos. Como criou um Plano Premium para o Azure Functions, você terá um custo contínuo de USD 1 ou 2 por dia.

Para evitar os custos contínuos, exclua o grupo de recursos `AzureFunctionsContainer-rg` para limpar todos os recursos que estão nele: 

```azurecli
az group delete --name AzureFunctionsContainer-rg
```

## <a name="next-steps"></a>Próximas etapas

+ [Funções de monitoramento](functions-monitoring.md)
+ [Opções de escala e de hospedagem](functions-scale.md)
+ [Hospedagem sem servidor baseada em Kubernetes](functions-kubernetes-keda.md)

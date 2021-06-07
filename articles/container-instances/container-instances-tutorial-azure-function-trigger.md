---
title: Tutorial – Disparar grupo de contêineres por função do Azure
description: Criar uma função do PowerShell sem servidor disparada por HTTP para automatizar a criação de Instâncias de Contêiner do Azure
ms.topic: tutorial
ms.date: 06/10/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e7315796f2f7f89800b58f5fa607e69cd7ae3447
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98935411"
---
# <a name="tutorial-use-an-http-triggered-azure-function-to-create-a-container-group"></a>Tutorial: Usar uma função do Azure disparada por HTTP para criar um grupo de contêineres

O [Azure Functions](../azure-functions/functions-overview.md) é um serviço de computação sem servidor que pode executar scripts ou código em resposta a uma variedade de eventos, como uma solicitação HTTP, um temporizador ou uma mensagem em uma fila do Armazenamento do Azure.

Neste tutorial, você criará uma função do Azure que usa uma solicitação HTTP e dispara a implantação de um [grupo de contêineres](container-instances-container-groups.md). Este exemplo mostra os conceitos básicos do uso do Azure Functions para a criação automática de recursos nas Instâncias de Contêiner do Azure. Modifique ou estenda o exemplo para cenários mais complexos ou outros gatilhos de evento. 

Você aprenderá como:

> [!div class="checklist"]
> * Usar o Visual Studio Code com a [extensão Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para criar uma função básica do PowerShell disparada por HTTP.
> * Habilitar uma identidade no aplicativo de funções e conceder a ela permissões para criar recursos do Azure.
> * Modificar e publicar novamente a função do PowerShell para automatizar a implantação de um grupo de contêineres de contêiner único.
> * Verificar a implantação disparada por HTTP do contêiner.

## <a name="prerequisites"></a>Pré-requisitos

Confira [Criar a sua primeira função no Azure usando o Visual Studio Code](../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-powershell#configure-your-environment) para obter os pré-requisitos para instalar e usar o Visual Studio Code com a extensão do Azure Functions no sistema operacional.

As etapas adicionais neste artigo usam o Azure PowerShell. Se você precisar instalá-lo ou atualizá-lo, confira [Instalar o Azure PowerShell][azure-powershell-install] e [Entrar no Azure](/powershell/azure/get-started-azureps#sign-in-to-azure).

## <a name="create-a-basic-powershell-function"></a>Criar uma função básica do PowerShell

Siga as etapas em [Criar sua primeira função do PowerShell no Azure](../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-powershell) para criar uma função do PowerShell usando o modelo de gatilho HTTP. Use o nome da função padrão do Azure **HttpTrigger**. Conforme mostrado no início rápido, teste a função localmente e publique o projeto em um aplicativo de funções no Azure. Este exemplo é uma função básica disparada por HTTP que retorna uma cadeia de texto. Nas etapas posteriores deste artigo, você modificará a função para criar um grupo de contêineres.

Este artigo pressupõe que você publique o projeto usando o nome *myfunctionapp*, em um grupo de recursos do Azure automaticamente nomeado de acordo com o nome do aplicativo de funções (também *myfunctionapp*). Substitua os nomes exclusivos do aplicativo de funções e do grupo de recursos nas etapas posteriores.

## <a name="enable-an-azure-managed-identity-in-the-function-app"></a>Habilitar uma identidade gerenciada do Azure no aplicativo de funções

Os comandos a seguir habilitam uma [identidade gerenciada](../app-service/overview-managed-identity.md?toc=/azure/azure-functions/toc.json#add-a-system-assigned-identity) atribuída ao sistema no aplicativo de funções. O host do PowerShell que executa o aplicativo pode se autenticar automaticamente no Azure usando essa identidade, permitindo que as funções executem ações nos serviços do Azure aos quais a identidade recebeu acesso. Neste tutorial, você concederá as permissões de identidade gerenciada para criar recursos no grupo de recursos do aplicativo de funções. 

[Adicione uma identidade](../app-service/overview-managed-identity.md?tabs=dotnet#using-azure-powershell-1) ao aplicativo de funções:

```powershell
Update-AzFunctionApp -Name myfunctionapp `
    -ResourceGroupName myfunctionapp `
    -IdentityType SystemAssigned
```

Atribua à identidade da função do colaborador com escopo no grupo de recursos:

```powershell
$SP=(Get-AzADServicePrincipal -DisplayName myfunctionapp).Id
$RG=(Get-AzResourceGroup -Name myfunctionapp).ResourceId
New-AzRoleAssignment -ObjectId $SP -RoleDefinitionName "Contributor" -Scope $RG
```

## <a name="modify-httptrigger-function"></a>Modificar a função HttpTrigger

Modifique o código do PowerShell na função **HttpTrigger** para criar um grupo de contêineres. No arquivo `run.ps1` da função, localize o bloco de código a seguir. Esse código exibirá um valor de nome, caso um seja passado como uma cadeia de consulta na URL da função:

```powershell
[...]
if ($name) {
    $body = "Hello, $name. This HTTP triggered function executed successfully."
}
[...]
```

Substitua esse código pelo bloco de exemplo a seguir. Aqui, se um valor de nome for passado na cadeia de consulta, ele será usado para nomear e criar um grupo de contêineres usando o cmdlet [New-AzContainerGroup][new-azcontainergroup]. Lembre-se de substituir o nome do grupo de recursos *myfunctionapp* pelo nome do grupo de recursos do aplicativo de funções:

```powershell
[...]
if ($name) {
    New-AzContainerGroup -ResourceGroupName myfunctionapp -Name $name `
        -Image alpine -OsType Linux `
        -Command "echo 'Hello from an Azure container instance triggered by an Azure function'" `
        -RestartPolicy Never
    if ($?) {
        $body = "This HTTP triggered function executed successfully. Started container group $name"
    }
    else  {
        $body = "There was a problem starting the container group."
    }
[...]
```

Este exemplo cria um grupo de contêineres que consiste em uma única instância de contêiner que executa a imagem `alpine`. O contêiner executa um único comando `echo` e, em seguida, é encerrado. Em um exemplo do mundo real, você pode disparar a criação de um ou mais grupos de contêineres para executar um trabalho em lotes.
 
## <a name="test-function-app-locally"></a>Testar o aplicativo de funções localmente

Verifique se a função é executada localmente antes de publicar novamente o projeto de aplicativo de funções no Azure. Quando executada localmente, a função não criará recursos do Azure. No entanto, você pode testar o fluxo de funções com e sem passar um valor de nome em uma cadeia de caracteres de consulta. Para depurar a função, confira [Depurar o Azure Functions para PowerShell localmente](../azure-functions/functions-debug-powershell-local.md).

## <a name="republish-azure-function-app"></a>Publicar novamente o aplicativo de funções do Azure

Depois de verificar se a função é executada localmente, publique o projeto novamente no aplicativo de funções existente no Azure.

1. No Visual Studio Code, abra a paleta de comandos. Pesquise e selecione `Azure Functions: Deploy to Function App...`.
1. Selecione a pasta de trabalho atual como zip e realize a implantação.
1. Selecione a assinatura e, em seguida, o nome do aplicativo de funções existente (*myfunctionapp*). Confirme que você deseja substituir a implantação anterior.

Uma notificação é exibida depois que seu aplicativo de funções é criado e o pacote de implantação é aplicado. Selecione **Exibir Saída** nessa notificação para exibir a criação e os resultados da implantação, incluindo os recursos do Azure atualizados.

## <a name="run-the-function-in-azure"></a>Executar a função no Azure

Depois que a implantação for concluída com êxito, obtenha a URL da função. Por exemplo, use a área **Azure: Functions** no Visual Studio Code para copiar a URL da função **HttpTrigger** ou obtenha a URL da função no [portal do Azure](../azure-functions/functions-get-started.md).

A URL da função está no formato:

```
https://myfunctionapp.azurewebsites.net/api/HttpTrigger
```

### <a name="run-function-without-passing-a-name"></a>Executar a função sem passar um nome

Como um primeiro teste, execute o comando `curl` e passe a URL da função sem acrescentar uma cadeia de consulta `name`. 

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger"
```

A função retorna o código de status 200 e o texto `This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response`:

```
[...]
> GET /api/HttpTrigger? HTTP/1.1
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.64.1
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/1.1 200 OK
< Content-Length: 135
< Content-Type: text/plain; charset=utf-8
< Request-Context: appId=cid-v1:d0bd0123-f713-4579-8990-bb368a229c38
< Date: Wed, 10 Jun 2020 17:50:27 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.* Closing connection 0
```

### <a name="run-function-and-pass-the-name-of-a-container-group"></a>Executar a função e passar o nome de um grupo de contêineres

Agora, execute o comando `curl` e acrescente o nome de um grupo de contêineres (*mycontainergroup*) como uma cadeia de consulta `?name=mycontainergroup`:

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?name=mycontainergroup"
```

A função retorna o código de status 200 e dispara a criação do grupo de contêineres:

```
[...]
> GET /api/HttpTrigger1?name=mycontainergroup HTTP/1.1
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.64.1
> Accept: */*
> 
< HTTP/1.1 200 OK
< Content-Length: 92
< Content-Type: text/plain; charset=utf-8
< Request-Context: appId=cid-v1:d0bd0123-f713-4579-8990-bb368a229c38
< Date: Wed, 10 Jun 2020 17:54:31 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
This HTTP triggered function executed successfully. Started container group mycontainergroup* Closing connection 0
```

Verifique se o contêiner foi executado com o comando [Get-AzContainerInstanceLog][get-azcontainerinstancelog]:

```azurecli
Get-AzContainerInstanceLog -ResourceGroupName myfunctionapp `
  -ContainerGroupName mycontainergroup 
```

Saída de exemplo:

```console
Hello from an Azure container instance triggered by an Azure function
```

## <a name="clean-up-resources"></a>Limpar os recursos

Caso não precise mais de nenhum dos recursos criados neste tutorial, execute o comando [az group delete][az-group-delete] para remover o grupo de recursos e todos os recursos que ele contém. Esse comando exclui o aplicativo de funções que você criou, bem como o contêiner em execução e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myfunctionapp
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou uma função do Azure que usa uma solicitação HTTP e dispara a implantação de um grupo de contêineres. Você aprendeu a:

> [!div class="checklist"]
> * Usar o Visual Studio Code com a extensão Azure Functions para criar uma função básica do PowerShell disparada por HTTP.
> * Habilitar uma identidade no aplicativo de funções e conceder a ela permissões para criar recursos do Azure.
> * Modificar o código da função do PowerShell para automatizar a implantação de um grupo de contêineres de contêiner único.
> * Verificar a implantação disparada por HTTP do contêiner.

Para obter um exemplo detalhado para iniciar e monitorar um trabalho em contêiner, confira a postagem no blog [Contêineres sem servidor controlados por evento com o PowerShell Azure Functions e as Instâncias de Contêiner do Azure](https://dev.to/azure/event-driven-serverless-containers-with-powershell-azure-functions-and-azure-container-instances-e9b) e o [exemplo de código](https://github.com/anthonychu/functions-powershell-run-aci) complementar.

Confira a [documentação do Azure Functions](../azure-functions/index.yml) para obter diretrizes detalhadas sobre como criar funções do Azure e publicar um projeto de funções. 

<!-- IMAGES -->


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-powershell-install]: /powershell/azure/install-az-ps
[new-azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[get-azcontainerinstancelog]: /powershell/module/az.containerinstance/get-azcontainerinstancelog

---
title: Desenvolver com o .NET Core no Kubernetes usando o Azure Dev Spaces (Visual Studio Code)
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: quickstart
description: Desenvolvimento rápido de Kubernetes com contêineres e microsserviços no Azure
keywords: Docker, Kubernetes, Azure, AKS, Serviço de Kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, k8s
manager: gwallace
ms.openlocfilehash: cc41e268678872910113c8e198bdaaac34232458
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706318"
---
# <a name="quickstart-develop-with-net-core-on-kubernetes-using-azure-dev-spaces-visual-studio-code"></a>Início Rápido: Desenvolver com o .NET Core no Kubernetes usando o Azure Dev Spaces (Visual Studio Code)

Neste guia, você aprenderá a:

- Configure o Azure Dev Spaces com um cluster Kubernetes gerenciado no Azure.
- Desenvolva o código em contêineres de maneira iterativa usando o Visual Studio Code.
- Depure o código no seu espaço de desenvolvimento do Visual Studio Code.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Se você não tiver uma, poderá [criar uma conta gratuita](https://azure.microsoft.com/free).
- [O Visual Studio Code instalado](https://code.visualstudio.com/download).
- As extensões [Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) e [C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) para o Visual Studio Code instaladas.
- A [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) instalada.

## <a name="create-an-azure-kubernetes-service-cluster"></a>Criar um cluster do Serviço de Kubernetes do Azure

Você precisa criar um cluster do AKS em uma [região com suporte][supported-regions]. Os comandos a seguir criam um grupo de recursos chamado *MyResourceGroup* e um cluster do AKS chamado *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Habilitar o Azure Dev Spaces no cluster do AKS

Use o comando `use-dev-spaces` para habilitar o Dev Spaces no cluster do AKS e siga os prompts. O comando abaixo habilita o Dev Spaces no cluster *MyAKS*, no grupo *MyResourceGroup* e cria um espaço de desenvolvimento *padrão*.

```cmd
$ az aks use-dev-spaces -g MyResourceGroup -n MyAKS

'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="get-sample-application-code"></a>Obter o código do aplicativo de exemplo

Neste artigo, você usa o [Aplicativo de exemplo do Azure Dev Spaces](https://github.com/Azure/dev-spaces) para demonstrar o uso do Azure Dev Spaces.

Clone o aplicativo do GitHub.

```cmd
git clone https://github.com/Azure/dev-spaces
```

## <a name="prepare-the-sample-application-in-visual-studio-code"></a>Preparar o aplicativo de exemplo no Visual Studio Code

Abra o Visual Studio Code, clique em *Arquivo*, *Abrir...* e, em seguida, navegue até o diretório *dev-spaces/samples/dotnetcore/getting-started/webfrontend* e clique em *Abrir*.

Agora você tem o projeto *webfrontend* aberto no Visual Studio Code. Para executar o aplicativo em seu espaço de desenvolvimento, gere os ativos de gráfico do Helm e do Docker usando a extensão Azure Dev Spaces na paleta de comando.

Para abrir a paleta de comandos no Visual Studio Code, clique em *Exibir* e, em seguida, *Paleta de Comandos*. Comece digitando `Azure Dev Spaces` e clique em `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`.

![Preparar arquivos de configuração para o Azure Dev Spaces](./media/common/command-palette.png)

Quando o Visual Studio Code solicitar também que você configure o ponto de extremidade público, escolha `Yes` para habilitar um ponto de extremidade público.

![Selecionar um ponto de extremidade público](media/common/select-public-endpoint.png)

Esse comando prepara o projeto para ser executado no Azure Dev Spaces por meio da geração de um Dockerfile e um gráfico do Helm. Ele também gera um diretório *.vscode* com a configuração de depuração na raiz do seu projeto.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio"></a>Compilar e executar o código no Kubernetes por meio do Visual Studio

Clique no ícone *Depurar* à esquerda e clique em *Iniciar .NET Core (AZDS)* na parte superior.

![](media/get-started-netcore/debug-configuration.png)

Este comando compila e executa o serviço no Azure Dev Spaces no modo de depuração. A janela *Terminal*, na parte inferior, mostra a saída do build e as URLs do seu serviço em execução no Azure Dev Spaces. O *Console de Depuração* mostra a saída de log.

> [!Note]
> Se você não vir comandos do Azure Dev Spaces na *Paleta de Comandos*, verifique se instalou a [extensão do Visual Studio Code para o Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Também verifique se você abriu o diretório *dev-spaces/samples/dotnetcore/getting-started/webfrontend* no Visual Studio Code.

Você poderá ver o serviço em execução abrindo a URL pública.

Clique em *Depurar* e, em seguida, *Parar Depuração* para parar o depurador.

## <a name="update-code"></a>Atualizar código

Para implantar uma versão atualizada do serviço, atualize qualquer arquivo no projeto e execute *Iniciar .NET Core (AZDS)* novamente. Por exemplo: 

1. Se o aplicativo ainda estiver em execução, clique em *Depurar* e, em seguida, em *Parar Depuração* para interrompê-lo.
1. Atualize a [linha 22 em `Controllers/HomeController.cs`](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/webfrontend/Controllers/HomeController.cs#L22) para:
    
    ```csharp
    ViewData["Message"] = "Your application description page in Azure.";
    ```

1. Salve suas alterações.
1. Execute *Iniciar .NET Core (AZDS)* novamente.
1. Navegue até o serviço em execução e clique em *Sobre*.
1. Observe as alterações.
1. Clique em *Depurar* e, em seguida, em *Parar Depuração* para interromper o aplicativo.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Definir e usar pontos de interrupção para depuração

Inicie o serviço no modo de depuração usando *Iniciar .NET Core (AZDS)*.

Navegue de volta para a exibição *Explorer* clicando em *Exibir* e, em seguida, *Explorer*. Abra `Controllers/HomeController.cs` e clique em algum lugar da linha 22 para colocar o cursor nela. Para definir um ponto de interrupção, pressione *F9* ou clique em *Depurar* e, em seguida, *Ativar/Desativar Pontos de Interrupção*.

Abra o serviço em um navegador e observe que nenhuma mensagem foi exibida. Retorne ao Visual Studio Code e observe a linha 20 realçada. O ponto de interrupção definido colocou o serviço em pausa na linha 20. Para retomar o serviço, pressione *F5* ou clique em *Depurar* e, em seguida, *Continuar*. Volte para o navegador e observe que agora a mensagem foi exibida.

Durante a execução de seu serviço no Kubernetes com o depurador anexado, você tem acesso completo às informações de depuração como a pilha de chamadas, as variáveis locais e as informações de exceção.

Remova o ponto de interrupção colocando o cursor na linha 22 em `Controllers/HomeController.cs` e pressionando *F9*.

## <a name="update-code-from-visual-studio-code"></a>Atualizar o código no Visual Studio Code

Enquanto o serviço estiver em execução no modo de depuração, atualize a linha 22 em `Controllers/HomeController.cs`. Por exemplo: 

```csharp
ViewData["Message"] = "Your application description page in Azure while debugging!";
```

Salve o arquivo. Clique em *Depurar* e, em seguida, *Reiniciar Depuração* ou, na *barra de ferramentas Depurar*, clique no botão *Reiniciar Depuração*.

![](media/common/debug-action-refresh.png)

Abra o serviço em um navegador e observe a mensagem atualizada exibida.

Em vez de recompilar e reimplantar uma nova imagem de contêiner sempre que houver edições de código, o Azure Dev Spaces recompilará incrementalmente o código dentro do contêiner existente para fornecer um loop de edição/depuração mais rápido.

## <a name="clean-up-your-azure-resources"></a>Limpar os recursos do Azure

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Próximas etapas

Saiba como o Azure Dev Spaces ajuda você a desenvolver aplicativos mais complexos em vários contêineres e como você pode simplificar o desenvolvimento colaborativo trabalhando com versões diferentes ou branches do seu código em diferentes espaços. 

> [!div class="nextstepaction"]
> [Trabalhando com vários contêineres e desenvolvimento em equipe](multi-service-netcore.md)


[supported-regions]: about.md#supported-regions-and-configurations
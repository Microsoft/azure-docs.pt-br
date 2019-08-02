---
title: Criar um espaço de desenvolvimento do Kubernetes na nuvem usando .NET Core e VS Code
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 09/26/2018
ms.topic: tutorial
description: Desenvolvimento rápido de Kubernetes com contêineres e microsserviços no Azure
keywords: Docker, Kubernetes, Azure, AKS, Serviço de Kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, k8s
ms.openlocfilehash: e05dbc570836741a69ed229fc93eb32a7dfd01dd
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503153"
---
# <a name="get-started-on-azure-dev-spaces-with-net-core"></a>Introdução ao Azure Dev Spaces com .NET Core

Neste guia, você aprenderá a:

- Crie um ambiente baseado em Kubernetes no Azure que seja otimizado para desenvolvimento – um _espaço de desenvolvimento_.
- Desenvolver código em containers iterativamente usando o VS Code e a linha de comando.
- Desenvolva e teste o código produtivamente em um ambiente de equipe.

> [!Note]
> **Caso tenha problemas** a qualquer momento, veja a seção [Solução de problemas](troubleshooting.md).

## <a name="install-the-azure-cli"></a>Instalar a CLI do Azure
O Azure Dev Spaces requer somente uma configuração mínima do computador local. A maior parte da configuração do seu espaço de desenvolvimento é armazenada na nuvem e pode ser compartilhada com outros usuários. Comece baixando e executando a [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="sign-in-to-azure-cli"></a>Entrar na CLI do Azure
Entre no Azure. Digite o seguinte comando em uma janela de terminal:

```cmd
az login
```

> [!Note]
> Caso não tenha uma assinatura do Azure, é possível criar uma [conta gratuita](https://azure.microsoft.com/free).

#### <a name="if-you-have-multiple-azure-subscriptions"></a>Caso tenha várias assinaturas do Azure...
Você pode exibir suas assinaturas, executando: 

```cmd
az account list
```
Localize a assinatura que tem `isDefault: true`na saída do JSON.
Se não for a assinatura que você deseja usar, é possível alterar a assinatura padrão:

```cmd
az account set --subscription <subscription ID>
```

## <a name="create-a-kubernetes-cluster-enabled-for-azure-dev-spaces"></a>Criar um cluster Kubernetes habilitado para Azure Dev Spaces

No prompt de comando, crie o grupo de recursos em uma [região que dá suporte ao Azure Dev Spaces][supported-regions].

```cmd
az group create --name MyResourceGroup --location <region>
```

Crie um cluster Kubernetes com o seguinte comando:

```cmd
az aks create -g MyResourceGroup -n MyAKS --location <region> --disable-rbac --generate-ssh-keys
```

São necessários alguns minutos para criar o cluster.

### <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>Configurar o cluster AKS para usar Azure Dev Spaces

Insira o seguinte comando da CLI do Azure, usando o grupo de recursos que contém o cluster do AKS e o nome do cluster do AKS. O comando configura o cluster com suporte para o Azure Dev Spaces.

   ```cmd
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```
   
> [!IMPORTANT]
> O processo de configuração do Azure Dev Spaces remove o namespace `azds` no cluster, se existir.

## <a name="get-kubernetes-debugging-for-vs-code"></a>Obter depuração do Kubernetes para VS Code
Recursos avançados como depuração do Kubernetes estão disponíveis para desenvolvedores .NET Core e Node.js usando VS Code.

1. Caso não o tenha, instale o [VS Code](https://code.visualstudio.com/Download).
1. Baixe e instale a [extensão Azure Dev Spaces do VS](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Clique em Instalar uma vez na página do Marketplace da extensão e novamente no VS Code. 

## <a name="create-a-web-app-running-in-a-container"></a>Criar um aplicativo Web em execução em um contêiner

Nesta seção, você criará um aplicativo Web ASP.NET Core e o executará em um contêiner no Kubernetes.

### <a name="create-an-aspnet-core-web-app"></a>Criar um aplicativo Web ASP.NET Core
Clone ou baixe o [aplicativo de exemplo do Azure Dev Spaces](https://github.com/Azure/dev-spaces). Este artigo usa o código no diretório *samples/dotnetcore/getting-started/webfrontend*.

## <a name="preparing-code-for-docker-and-kubernetes-development"></a>Preparar código para desenvolvimento Docker e Kubernetes
Até o momento, você tem um aplicativo Web básico que pode ser executado localmente. Agora, você colocará o aplicativo em contêiner criando ativos que definem o contêiner do aplicativo e como ele será implantado no Kubernetes. Isso é fácil de fazer usando o Azure Dev Spaces: 

1. Inicialize o VS Code e abra a pasta `webfrontend`. (Você pode ignorar todos os prompts padrão para adicionar ativos de depuração ou restaurar o projeto.)
1. Abra o Terminal Integrado no VS Code (usando o menu **Exibir > Terminal Integrado**).
1. Execute esse comando (certifique-se de que **webfrontend** seja a pasta atual):

    ```cmd
    azds prep --public
    ```

O comando `azds prep` da CLI do Azure gera ativos de Docker e Kubernetes com configurações padrão:
* `./Dockerfile` descreve a imagem de contêiner do aplicativo e como o código-fonte é compilado e executado no contêiner.
* Um [Gráfico Helm](https://docs.helm.sh) em `./charts/webfrontend` descreve como implantar o contêiner no Kubernetes.

Por enquanto, não é necessário entender o conteúdo completo desses arquivos. Vale ressaltar, no entanto, que **os mesmos ativos de configuração como código do Kubernetes e do Docker podem ser usados desde o desenvolvimento até a produção, fornecendo assim melhor consistência em diferentes ambientes.**
 
Um arquivo nomeado `./azds.yaml` também é gerado pelo comando `prep` e é o arquivo de configuração dos Azure Dev Spaces. Ele complementa os artefatos do Docker e do Kubernetes com configuração adicional que permite uma experiência de desenvolvimento iterativo no Azure.

## <a name="build-and-run-code-in-kubernetes"></a>Compilar e executar um código no Kubernetes
Vamos executar nosso código! Na janela do terminal, execute este comando na **pasta de código raiz**, webfrontend:

```cmd
azds up
```

Fique atento à saída do comando; você verá várias coisas durante sua execução:
- O código-fonte é sincronizado com o espaço de desenvolvimento no Azure.
- Uma imagem de contêiner é criada no Azure, conforme especificado pelos ativos do Docker na pasta de código.
- Os objetos do Kubernetes que utilizam a imagem de contêiner são criados, conforme especificado pelo gráfico do Helm na pasta de código.
- As informações sobre os pontos de extremidade do contêiner são exibidas. Em nosso caso, estamos esperando uma URL HTTP pública.
- Supondo que os estágios acima foram concluídos com êxito, você deverá começar a ver a saída de `stdout` (e `stderr`) conforme o contêiner é iniciado.

> [!Note]
> Essas etapas levarão mais tempo na primeira vez em que o comando `up` for executado, mas as execuções seguintes deverão ser mais rápidas.

### <a name="test-the-web-app"></a>Testar o aplicativo Web
Examine a saída do console em busca da mensagem *Aplicativo iniciado*, confirmando que o comando `up` foi concluído:

```
Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
Service 'webfrontend' port 'http' is available at http://webfrontend.1234567890abcdef1234.eus.azds.io/
Microsoft (R) Build Engine version 15.9.20+g88f5fadfbe for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  webfrontend -> /src/bin/Debug/netcoreapp2.2/webfrontend.dll
  webfrontend -> /src/bin/Debug/netcoreapp2.2/webfrontend.Views.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:00.94
[...]
webfrontend-5798f9dc44-99fsd: Now listening on: http://[::]:80
webfrontend-5798f9dc44-99fsd: Application started. Press Ctrl+C to shut down.
```

Identifique a URL pública do serviço na saída do comando `up`. Termina em `.azds.io`. No exemplo acima, a URL pública é `http://webfrontend.1234567890abcdef1234.eus.azds.io/`.

Para ver seu aplicativo Web, abra a URL pública em um navegador. Além disso, observe que a saída de `stdout` e `stderr` é transmitida para a janela do terminal *azds trace* enquanto você interage com o aplicativo Web. Também é possível conferir informações de acompanhamento das solicitações HTTP à medida que elas passam pelo sistema. Isso facilita o acompanhamento de chamadas complexas de vários serviços durante o desenvolvimento. A instrumentação adicionada pelo Dev Spaces fornece esse acompanhamento de solicitação.

![janela do terminal de rastreamento azds](media/get-started-netcore/azds-trace.png)


> [!Note]
> Além da URL pública, você pode usar a URL alternativa `http://localhost:<portnumber>` exibida na saída do console. Se você usar a URL de host local, poderá parecer que o contêiner está sendo executado localmente, mas, na verdade, ele está sendo executado no AKS. O Azure Dev Spaces usa a funcionalidade de *encaminhamento de porta* do Kubernetes para mapear a porta do host local para o contêiner em execução no AKS. Isso facilita a interação com o serviço em seu computador local.

### <a name="update-a-content-file"></a>Atualizar um arquivo de conteúdo
O Azure Dev Spaces não serve apenas para executar o código em Kubernetes; ele também serve para permitir que você veja as alterações de código entrarem em vigor de forma rápida e iterativa em um ambiente Kubernetes na nuvem.

1. Localize o arquivo `./Views/Home/Index.cshtml` e edite o HTML. Por exemplo, altere a [linha 73 que diz `<h2>Application uses</h2>`](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/webfrontend/Views/Home/Index.cshtml#L73) para algo como: 

    ```html
    <h2>Hello k8s in Azure!</h2>
    ```

1. Salve o arquivo. Em seguida, na janela do Terminal, você verá uma mensagem informando que um arquivo no contêiner em execução foi atualizado.
1. Volte para o navegador e atualize a página. Você verá a página da Web exibir o HTML atualizado.

O que aconteceu? Edições em arquivos de conteúdo, como HTML e CSS, não exigem a recompilação em um aplicativo Web do .NET Core e, portanto um comando `azds up` ativo sincroniza automaticamente os arquivos de conteúdo modificados no contêiner em execução no Azure. Assim, você pode ver suas edições de conteúdo imediatamente.

### <a name="update-a-code-file"></a>Atualizar um arquivo de código
A atualização dos arquivos de código requer um pouco mais de trabalho, pois um aplicativo .NET Core precisa recompilar e produzir os binários do aplicativo atualizado.

1. Na janela do terminal, pressione `Ctrl+C` (para parar `azds up`).
1. Abra o arquivo de código chamado `Controllers/HomeController.cs` e edite a mensagem exibida na página Sobre: `ViewData["Message"] = "Your application description page.";`
1. Salve o arquivo.
1. Execute `azds up` na janela do terminal. 

Esse comando recompila a imagem de contêiner e reimplanta o gráfico de Helm. Para ver as alterações de código entrarem em vigor no aplicativo em execução, vá ao menu Sobre no aplicativo Web.

Mas existe um *método ainda mais rápido* para desenvolver código, que você irá explorar na próxima seção. 

## <a name="debug-a-container-in-kubernetes"></a>Depurar um contêiner no Kubernetes

Nesta seção, você usará o VS Code para depurar diretamente depurar nosso contêiner em execução no Azure. Você também aprenderá a obter um loop de edição, execução e teste mais rápido.

![](media/common/edit-refresh-see.png)

> [!Note]
> **Caso tenha problemas** a qualquer momento, consulte a seção [Solução de problemas](troubleshooting.md) ou poste um comentário nesta página.

### <a name="initialize-debug-assets-with-the-vs-code-extension"></a>Inicialize os recursos de depuração com a extensão do VS Code
Primeiro, é necessário configurar o projeto de código para que o VS Code comunique-se com o espaço de desenvolvimento no Azure. A extensão do VS Code para o Azure Dev Spaces fornece um comando auxiliar para configurar a configuração de depuração. 

Abra a **Paleta de Comandos** (usando o menu **Exibir | Paleta de Comandos**) e use o preenchimento automático para digitar e selecionar este comando: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. 

Isso adiciona a configuração de depuração para o Azure Dev Spaces na pasta `.vscode`. Esse comando não é deve ser confundido com o `azds prep`, que configura o projeto para a implantação.

![](media/common/command-palette.png)


### <a name="select-the-azds-debug-configuration"></a>Selecionar a configuração de depuração AZDS
1. Para abrir o modo de exibição Depuração, clique no ícone Depuração na **Barra de Atividades** do lado do VS Code.
1. Selecione **Inicialização do .NET Core (AZDS)** como a configuração de depuração ativa.

![](media/get-started-netcore/debug-configuration.png)

> [!Note]
> Se você não vir comandos do Azure Dev Spaces na Paleta de Comandos, verifique se instalou a extensão do VS Code para o Azure Dev Spaces. Verifique se o workspace que você abriu no VS Code é a pasta que contém azds.yaml.


### <a name="debug-the-container-in-kubernetes"></a>Depurar o contêiner no Kubernetes
Pressione **F5** para depurar seu código no Kubernetes.

Assim como acontece com o comando `up`, o código está sincronizado com o espaço de desenvolvimento e um contêiner é criado e implantado no Kubernetes. Desta vez, é claro, o depurador está anexado ao contêiner remoto.

> [!Tip]
> A barra de status do VS Code ficará laranja, indicando que o depurador foi anexado. Ela também exibirá uma URL clicável, que pode ser usada para abrir o site.

![](media/common/vscode-status-bar-url.png)

Defina um ponto de interrupção em um arquivo de código do lado do servidor, por exemplo, dentro da função `About()` no arquivo de origem `Controllers/HomeController.cs`. A atualização da página do navegador gera o ponto de interrupção.

Você tem acesso completo às informações de depuração exatamente como teria se o código fosse executado localmente, por exemplo, a pilha de chamadas, as variáveis locais, as informações de exceção etc.

### <a name="edit-code-and-refresh"></a>Editar código e atualizar
Com o depurador ativo, edite o código. Por exemplo, modifique a mensagem da página Sobre em `Controllers/HomeController.cs`. 

```csharp
public IActionResult About()
{
    ViewData["Message"] = "My custom message in the About page.";
    return View();
}
```

Salve o arquivo e, no **painel Ações de depuração**, clique no botão **Reiniciar**. 

![](media/common/debug-action-refresh.png)

Em vez de recompilar e reimplantar uma nova imagem de contêiner sempre que houver edições de código, o que geralmente levará um tempo considerável, o Azure Dev Spaces recompilará incrementalmente o código dentro do contêiner existente para fornecer um loop de edição/depuração mais rápido.

Atualize o aplicativo Web no navegador e vá para a página Sobre. Sua mensagem personalizada deverá aparecer na interface de usuário.

**Agora você tem um método para iterar em código rapidamente e depurar diretamente no Kubernetes!** Em seguida, você verá como você pode criar e chamar um segundo contêiner.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre o desenvolvimento de vários serviços](multi-service-netcore.md)


[supported-regions]: about.md#supported-regions-and-configurations
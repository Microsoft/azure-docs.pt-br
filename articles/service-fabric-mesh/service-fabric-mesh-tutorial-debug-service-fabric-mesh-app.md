---
title: Depurar um aplicativo Web da Malha do Azure Service Fabric em execução localmente
description: Neste tutorial, depure um aplicativo da Malha do Azure Service Fabric em execução em seu cluster local.
author: georgewallace
ms.topic: tutorial
ms.date: 10/31/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: 9e3dc16481340c0266cd398d0970e2147648e17f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625442"
---
# <a name="tutorial-debug-a-service-fabric-mesh-application-running-in-your-local-development-cluster"></a>Tutorial: Depurar um aplicativo da Malha do Service Fabric em execução em seu cluster de desenvolvimento local

> [!IMPORTANT]
> A versão prévia da Malha do Azure Service Fabric foi desativada. Não serão mais permitidas novas implantações por meio da API da Malha do Service Fabric. O suporte para as implantações existentes continuará até 28 de abril de 2021.
> 
> Para obter detalhes, confira [Desativação da versão prévia da Malha do Azure Service Fabric](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Este tutorial é a parte dois de uma série e mostra como criar e depurar um aplicativo da Malha do Azure Service Fabric em seu cluster de desenvolvimento local.

Neste tutorial, você aprenderá:

> [!div class="checklist"]
> * O que acontece quando você compila um aplicativo da Malha do Azure Service Fabric
> * Como definir um ponto de interrupção para observar uma chamada de serviço a serviço

Nesta série de tutoriais, você aprenderá a:
> [!div class="checklist"]
> * [Criar um aplicativo da Malha do Service Fabric no Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * Depurar um aplicativo da Malha do Service Fabric em execução no cluster de desenvolvimento local
> * [Implantar um aplicativo da Malha do Service Fabric](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [Atualizar um aplicativo da Malha do Service Fabric](service-fabric-mesh-tutorial-upgrade.md)
> * [Limpar os recursos da Malha do Service Fabric](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* [Configure o ambiente de desenvolvimento](service-fabric-mesh-howto-setup-developer-environment-sdk.md), o que inclui a instalação do runtime do Service Fabric, do SDK, do Docker e do Visual Studio 2017.

## <a name="download-the-to-do-sample-application"></a>Faça o download do aplicativo de exemplo de tarefas pendentes

Se você não criou o aplicativo de exemplo de tarefas pendentes na [parte um dessa série de tutoriais](service-fabric-mesh-tutorial-create-dotnetcore.md), baixe-o. Em uma janela de comando, execute o comando a seguir para clonar o repositório de aplicativos de exemplo no computador local.

```
git clone https://github.com/azure-samples/service-fabric-mesh
```

O aplicativo está no diretório `src\todolistapp`.

## <a name="build-and-debug-on-your-local-cluster"></a>Compilar e depurar em seu cluster local

Uma imagem do Docker será compilada e implantada automaticamente no cluster local assim que o projeto for carregado. Esse processo pode demorar um pouco. Para monitorar o progresso no painel de **Saída** do Visual Studio, defina a lista suspensa do painel Saída **Mostrar saída de:** como **Ferramentas do Service Fabric**.

Pressione **F5** para compilar e executar seu serviço localmente. Quando o projeto for executado e depurado localmente, o Visual Studio irá:

* Verificar se o Docker para Windows está em execução e definido para usar o Windows como o sistema operacional de contêiner.
* Baixar qualquer imagem base ausente do Docker. Essa parte pode demorar algum tempo
* Compilar (ou recompilar) a imagem do Docker usada para hospedar seu projeto de código.
* Implantar e executar o contêiner no cluster de desenvolvimento do Service Fabric local.
* Executar seus serviços e atingir qualquer ponto de interrupção definido por você.

Após a conclusão da implantação local, e quando o Visual Studio estiver executando seu aplicativo, uma janela do navegador será aberta com uma página da Web de exemplo padrão.

## <a name="debugging-tips"></a>Dicas de depuração

Torne sua primeira operação de depuração (F5) muito mais rápida seguindo as instruções em [Otimizar o desempenho do Visual Studio](service-fabric-mesh-howto-optimize-vs.md).

Atualmente, há um problema que faz com que a chamada para `using (HttpResponseMessage response = client.GetAsync("").GetAwaiter().GetResult())` não se conecte ao serviço. Isso pode ocorrer sempre que seu endereço IP do host for alterado. Para resolver esse problema:

1. Remova o aplicativo do cluster local (no Visual Studio, **Compilar** > **Limpar Solução**).
2. No Gerenciador de Cluster Local do Service Fabric, selecione **Parar Cluster Local** e depois **Iniciar Cluster Local**.
3. Reimplante o aplicativo (no Visual Studio, **F5**).

Se você receber o erro **Não há um cluster local do Service Fabric em execução**, verifique se o LCM (Gerenciador de Cluster Local) do Service Fabric está em execução e clique com o botão direito no ícone do LCM na barra de tarefas, depois clique em **Iniciar Cluster Local**. Depois de iniciado, retorne ao Visual Studio e pressione **F5**.

Se você receber um erro **404** quando o aplicativo for iniciado, talvez suas variáveis de ambiente em **service.yaml** estejam incorretas. Certifique-se de que `ApiHostPort` e `ToDoServiceName` estejam definidas corretamente de acordo com as instruções em [Criar variáveis de ambiente](./service-fabric-mesh-tutorial-create-dotnetcore.md#create-environment-variables).

Se você receber erros de compilação em **service.yaml**, use espaços, e não guias, para recuar linhas. Além disso, por enquanto, você deve compilar o aplicativo usando a localidade de inglês.

### <a name="debug-in-visual-studio"></a>Depuração no Visual Studio

Ao depurar um aplicativo da Malha do Service Fabric no Visual Studio, você usa um cluster de desenvolvimento local do Service Fabric. Para ver como os itens de tarefas pendentes são recuperados do serviço de back-end, depure-os no método OnGet().
1. No projeto **WebFrontEnd**, abra **Páginas** > **Index.cshtml** > **Index.cshtml.cs** e defina um ponto de interrupção no método **OnGet** (linha 17).
2. No projeto **ToDoService**, abra **TodoController.cs** e defina um ponto de interrupção no método **Get** (linha 15).
3. Volte para o navegador e atualize a página. Você chegou ao ponto de interrupção no método `OnGet()` front-end da Web. Você pode inspecionar a variável `backendUrl` para ver como as variáveis de ambiente que você definiu no arquivo **service.yaml** são combinadas na URL usada para contatar o serviço de back-end.
4. Avance (F10) a chamada `client.GetAsync(backendUrl).GetAwaiter().GetResult())` e você atingirá o ponto de interrupção `Get()` do controlador. Nesse método, você pode ver como a lista de itens de tarefas pendentes é recuperada da lista na memória.
5. Quando terminar, pare a depuração de seu projeto no Visual Studio pressionando **Shift+F5**.

## <a name="next-steps"></a>Próximas etapas

Nesta parte do tutorial, você aprendeu a:

> [!div class="checklist"]
> * O que acontece quando você compila um aplicativo da Malha do Azure Service Fabric
> * Como definir um ponto de interrupção para observar uma chamada de serviço a serviço

Prosseguir para o próximo tutorial:
> [!div class="nextstepaction"]
> [Implantar um aplicativo da Malha do Service Fabric](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)

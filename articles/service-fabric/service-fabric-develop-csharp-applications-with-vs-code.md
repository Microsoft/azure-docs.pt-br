---
title: Desenvolver aplicativos do Microsoft Azure Service Fabric .NET Core com Visual Studio Code | Microsoft Docs
description: Este artigo mostra como compilar, implantar e depurar aplicativos do Microsoft Azure Service Fabric .Net Core usando o Visual Studio Code.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 60b634b0b927804249148737ee7a99c0e86dd7d6
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537760"
---
# <a name="develop-c-service-fabric-applications-with-visual-studio-code"></a>Desenvolver aplicativos C# do Microsoft Azure Service Fabric com o Visual Studio Code

A [extensão de serviços confiáveis do Service Fabric para VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) facilita a criação de aplicativos .NET do Microsoft Azure Service Fabric em sistemas operacionais Windows, Linux e macOS.

Este artigo mostra como compilar, implantar e depurar aplicativos do .NET do Microsoft Azure Service Fabric usando o Visual Studio Code.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você já instalou todas as dependências necessárias para seu ambiente de desenvolvimento, a extensão de serviços confiáveis da extensão Service Fabric Reliable Services e quaisquer dependências necessárias para o seu ambiente de desenvolvimento. Saiba mais, veja [Guia de Introdução](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>Baixar o exemplo
Este artigo usa o aplicativo de CounterService na [repositório GitHub de exemplos do .NET Core da introdução do Microsoft Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started). 

Para clonar o repositório em seu computador de desenvolvimento, execute o seguinte comando em uma janela de terminal (janela de comando no Windows):

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started.git
```

## <a name="open-the-application-in-vs-code"></a>Abra o aplicativo no Visual Studio Code

### <a name="windows"></a>Windows
Clique com o botão direito do mouse no ícone do Visual Studio Code no Menu Iniciar e escolha **Executar como administrador**. Para anexar o depurador aos serviços, você precisa executar o Visual Studio Code como administrador.

### <a name="linux"></a>Linux
Usando o terminal, navegue até o caminho /service-fabric-dotnet-core-getting-started/Services/CounterService do diretório que o aplicativo foi clonado localmente.

Execute o seguinte comando para abrir o código do VS como um usuário raiz para que o depurador pode ser anexado aos seus serviços.
```
sudo code . --user-data-dir='.'
```

O aplicativo agora deve aparecer no seu workspace do Visual Studio Code.

![Aplicativo de serviço de contador no workspace](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-application-in-workspace.png)

## <a name="build-the-application"></a>Compilar o aplicativo
1. Aperte (Ctrl + Shift + p) para abrir a **Paleta de comandos** no visual Studio Code.
2. Pesquise e selecione o comando **Service Fabric: Compilar Aplicativo**. A saída de compilação é enviada para o terminal integrado.

   ![Compilar comando de aplicativo no Visual Studio Code](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Implantar um aplicativo no cluster local
Após a compilação do aplicativo, você pode implantá-lo no cluster local. 

1. Na **Paleta de comandos**, selecione o comando **Service Fabric: Implantar Aplicativo (Localhost)** . A saída do processo de instalação é enviada para o terminal integrado.

   ![Implantar o comando de aplicativo no Visual Studio Code](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-deploy-application.png)

4. Quando a implantação for concluída, inicie um navegador e abra o Service Fabric Explorer: http:\//localhost:19080 / Explorer. Você deve ver que o aplicativo está em execução. Isso pode levar algum tempo, portanto seja paciente. 

   ![Aplicativo de serviço de contador no Service Fabric Explorer](./media/service-fabric-develop-csharp-applications-with-vs-code/sfx-verify-deploy.png)

4. Depois de verificar o aplicativo está em execução, inicie um navegador e abra esta página: http:\//localhost:31002. Esta é a web front-end do aplicativo. Atualize a página para ver o valor atual do contador conforme ele é incrementado.

   ![Aplicativo de serviço de contador no navegador](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-running.png)

## <a name="publish-the-application-to-an-azure-service-fabric-cluster"></a>Publicar o aplicativo em um cluster do Azure Service Fabric
Junto com a implantação do aplicativo no cluster local, você também pode publicar o aplicativo em um cluster remoto do Azure Service Fabric. 

1. Certifique-se de que você criou seu aplicativo usando as instruções acima. Atualizar o arquivo de configuração gerado `Cloud.json` com os detalhes do cluster remoto que você deseja publicar.

2. Na **Paleta de comandos**, selecione o comando **Service Fabric: Comando de aplicativo publicar**. A saída do processo de instalação é enviada para o terminal integrado.

   ![Comando de aplicativo de publicação no VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-publish-application.png)

3. Quando a implantação for concluída, inicie um navegador e abra o Service Fabric Explorer: `https:<clusterurl>:19080/Explorer`. Você deve ver que o aplicativo está em execução. Isso pode levar algum tempo, portanto seja paciente. 

## <a name="debug-the-application"></a>Depurar o aplicativo
Ao depurar aplicativos no Visual Studio Code, o aplicativo deve estar executando em um cluster local. Pontos de interrupção, em seguida, podem ser adicionados ao código.

Para definir um ponto de interrupção, conclua as seguintes etapas:
1. No Explorer, abra o arquivo */src/CounterServiceApplication/CounterService/CounterService.cs* e defina um ponto de interrupção na linha 62 dentro do `RunAsync` método.
3. Clique no ícone de depuração na **barra de atividade** para abrir o modo de exibição do depurador no Visual Studio Code. Clique no ícone de engrenagem na parte superior do modo de exibição do depurador e selecione **.NET Core** no menu suspenso de ambiente. Abre o arquivo launch.json. Você pode fechar este arquivo. Agora você deve ver as opções de configuração no menu de configuração de depuração localizado ao lado do botão Executar (seta verde).

   ![Depurar o ícone no workspace de código VS](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-icon-workspace.png)

2. Selecione **anexar .NET Core** do menu de configuração de depuração.

   ![Depurar o ícone no workspace de código VS](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-start.png)

3. Abra o Explorador do Service Fabric em um navegador: http:\//localhost:19080 / Explorer. Clique em **Aplicativos** e faça uma busca detalhada para determinar o nó primário que o CounterService está executando. Na imagem abaixo, o nó primário para o CounterService é o nó 0.

   ![Nó principal para CounterService](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-primary-node.png)

4. No Visual Studio Code, clique no ícone de execução (seta verde) ao lado de **.NET Core Attach** configuração de depuração. Na caixa de diálogo da seleção do processo, selecione o processo de CounterService que está em execução no nó primário que você identificou na etapa 4.

   ![Processo principal](./media/service-fabric-develop-csharp-applications-with-vs-code/select-process.png)

5. O ponto de interrupção no arquivo *CounterService.cs* será atingido muito rapidamente. Em seguida, você pode explorar os valores das variáveis locais. Use a barra de ferramentas de depuração na parte superior do Visual Studio Code para continuar a execução, depuração parcial linhas, intervir em métodos, ou sair do método atual. 

   ![Valores de depuração](./media/service-fabric-develop-csharp-applications-with-vs-code/breakpoint-hit.png)

6. Para encerrar a sessão de depuração, clique no ícone de plug na barra de ferramentas de depuração na parte superior do Visual Studio Code.
   
   ![Desconectado do depurador](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-bar-disconnect.png)
       
7. Quando terminar a depuração, você poderá usar o comando **Service Fabric: Remover aplicativo** para remover o aplicativo do CounterService de seu cluster local. 

## <a name="next-steps"></a>Próximas etapas

* Saiba como [desenvolver aplicativos Microsoft Azure Service Fabric Java com Visual Studio Code](./service-fabric-develop-java-applications-with-vs-code.md).




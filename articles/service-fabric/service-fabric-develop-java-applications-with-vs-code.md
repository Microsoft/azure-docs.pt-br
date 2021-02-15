---
title: Desenvolva aplicativos Java com Visual Studio Code
description: Este artigo mostra como compilar, implantar e depurar aplicativos do Java do Microsoft Azure Service Fabric usando o Visual Studio Code.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.custom: devx-track-java
ms.author: pepogors
ms.openlocfilehash: cc65deb924a9f3367c2ea1d7c71544743ccf2697
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87327354"
---
# <a name="develop-java-service-fabric-applications-with-visual-studio-code"></a>Desenvolver aplicativos Java do Microsoft Azure Service Fabric com o Visual Studio Code

A [extensão de Reliable ServicesFabric para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) facilita a criação de aplicativos Java do Microsoft Azure Service Fabric em sistemas operacionais Windows, Linux e macOS.

Este artigo mostra como compilar, implantar e depurar aplicativos do Java do Microsoft Azure Service Fabric usando o Visual Studio Code.

> [!IMPORTANT]
> Aplicativos do Java do Microsoft Service Fabric podem ser desenvolvidos em máquinas Windows, mas podem ser implantados nos clusters do Linux do Azure apenas. Não há suporte para a depuração de aplicativos Java no Windows.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você já instalou todas as dependências necessárias para seu ambiente de desenvolvimento, a extensão de serviços confiáveis da extensão Service Fabric Reliable Services e quaisquer dependências necessárias para o seu ambiente de desenvolvimento. Saiba mais, veja [Guia de Introdução](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>Baixar o exemplo
Este artigo usa o aplicativo de votação no [repositório GitHub de amostra de início rápido do aplicativo Java do Microsoft Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-java-quickstart). 

Para clonar o repositório em seu computador de desenvolvimento, execute o seguinte comando em uma janela de terminal (janela de comando no Windows):

```sh
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="open-the-application-in-vs-code"></a>Abra o aplicativo no Visual Studio Code

Abra o VS Code.  Clique no ícone do Explorer na **Barra de atividade** e clique em **Abrir pasta**, ou clique em **arquivo -> Abrir pasta**. Navegue até o diretório *./service-fabric-java-quickstart/Voting* na pasta onde você clonou o repositório, em seguida, clique em **Ok**. O workspace deve conter os mesmos arquivos mostrados na captura de tela abaixo.

![Aplicativo de votação Java no workspace](./media/service-fabric-develop-java-applications-with-vs-code/java-voting-application.png)

## <a name="build-the-application"></a>Compilar o aplicativo

1. Aperte (Ctrl + Shift + p) para abrir a **Paleta de comandos** no visual Studio Code.
2. Pesquise e selecione o **Service Fabric: criar comando do Aplicativo**. A saída de compilação é enviada para o terminal integrado.

   ![Criar comando de aplicativo no Visual Studio Code](./media/service-fabric-develop-java-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Implantar um aplicativo no cluster local
Após a compilação do aplicativo, você pode implantá-lo no cluster local. 

> [!IMPORTANT]
> Não há suporte para a implantação de aplicativos Java para o cluster local nos computadores do Windows.

1. Da **Paleta de Comandos**, selecione o **Service Fabric: comando implantar o aplicativo (Localhost)**. A saída do processo de instalação é enviada para o terminal integrado.

   ![Implantar o comando de aplicativo no Visual Studio Code](./media/service-fabric-develop-java-applications-with-vs-code/sf-deploy-application.png)

4. Quando a implantação for concluída, inicie um navegador e abra o Service Fabric Explorer: `http://localhost:19080/Explorer`. Você deve ver que o aplicativo está em execução. Isso pode levar algum tempo, portanto seja paciente. 

   ![Votar aplicativo no Service Fabric Explorer](./media/service-fabric-develop-java-applications-with-vs-code/sfx-localhost-java.png)

4. Depois de verificar se o aplicativo está em execução, inicie um navegador e abra esta página: `http://localhost:8080`. Esta é a web front-end do aplicativo. Você pode adicionar itens e clique para votar.

   ![Aplicativo de votação no navegador](./media/service-fabric-develop-java-applications-with-vs-code/voting-sample-in-browser.png)

5. Para remover o aplicativo do cluster, selecione o comando **Service Fabric: Remover Aplicativo** da **paleta de comandos**. A saída do processo de desinstalação é enviada para o terminal integrado. Você pode usar o Service Fabric Explorer para verificar que o aplicativo foi removido do cluster local.

## <a name="debug-the-application"></a>Depurar o aplicativo
Ao depurar aplicativos no Visual Studio Code, o aplicativo deve estar executando em um cluster local. Pontos de interrupção, em seguida, podem ser adicionados ao código.

> [!IMPORTANT]
> Não há suporte para a depuração de aplicativos Java nos computadores Windows.

Para preparar o VotingDataService e o aplicativo de votação para depuração, conclua as seguintes etapas:

1. Atualizar o arquivo *Voting/VotingApplication/VotingDataServicePkg/Code/entryPoint.sh*.
Comente o comando na linha 6 (use “#”) e adicione o seguinte comando na parte inferior do arquivo:

   ```
   java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar VotingDataService.jar
   ```

2. Atualize o arquivo *Voting/VotingApplication/ApplicationManifest.xml*. Defina as **MinReplicaSetSize** e os atributos **TargetReplicaSetSize** como "1" no elemento **StatefulService**:
   
   ```xml
         <StatefulService MinReplicaSetSize="1" ServiceTypeName="VotingDataServiceType" TargetReplicaSetSize="1">
   ```

3. Clique no ícone de depuração na **barra de atividade** para abrir o modo de exibição do depurador no Visual Studio Code. Clique no ícone de engrenagem na parte superior do modo de exibição do depurador e selecione **Java** no menu suspenso de ambiente. Abre o arquivo launch.json. 

   ![Depurar o ícone no workspace de código VS](./media/service-fabric-develop-java-applications-with-vs-code/debug-icon-workspace.png)

3. No arquivo launch.json f, defina o valor da porta na configuração denominada **Depurar (anexar)** a **8001**. Salve o arquivo.

   ![Configuração de depuração para a launch.json](./media/service-fabric-develop-java-applications-with-vs-code/launch-json-java.png)

4. Implantar o aplicativo no cluster local usando o **Service Fabric: implantar o comando do aplicativo (Localhost)**. Verifique se o aplicativo está em execução no Service Fabric Explorer. Seu aplicativo agora está pronto para ser depurado.

Para definir um ponto de interrupção, conclua as seguintes etapas:

1. No Explorer, abra o arquivo */Voting/VotingDataService/src/statefulservice/VotingDataService.java*. Definir um ponto de interrupção na primeira linha de código no `try` bloco no `addItem` método (linha 80).
   
   ![Definir o ponto de interrupção no serviço de dados de votação](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-set.png)

   > [!IMPORTANT]
   > Certifique-se de definir pontos de interrupção nas linhas de código executável. Por exemplo, os pontos de interrupção definidos em declarações de método, `try` instruções, ou `catch` instruções não serão detectadas pelo depurador.
2. Para iniciar a depuração, clique no ícone de depuração na **Barra de Atividade**, selecione a configuração **Depurar (anexar)** no menu Depurar e clique no botão Executar (seta verde).

   ![Configuração Depurar (anexar)](./media/service-fabric-develop-java-applications-with-vs-code/debug-attach-java.png)

3. Em um navegador da Web, acesse `http://localhost:8080`. Digite um novo item na caixa de texto e clique em **+ Adicionar**. Seu ponto de interrupção deve ser atingido. Você pode usar a barra de ferramentas de depuração na parte superior do Visual Studio Code para continuar a execução, depuração parcial linhas, intervir em métodos, ou sair do método atual. 
   
   ![Definir ponto de interrupção](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-hit.png)
       
4. Para encerrar a sessão de depuração, clique no ícone de plug na barra de ferramentas de depuração na parte superior do Visual Studio Code.
   
   ![Desconectado do depurador](./media/service-fabric-develop-java-applications-with-vs-code/debug-bar-disconnect.png)
       
5. Quando você terminar a depuração, você pode usar o **Service Fabric: remover o comando do aplicativo** para remover o aplicativo de votação do seu cluster local. 

## <a name="next-steps"></a>Próximas etapas

* Saiba como [Desenvolver aplicativos Service Fabric do C# com Visual Studio Code](./service-fabric-develop-csharp-applications-with-vs-code.md).

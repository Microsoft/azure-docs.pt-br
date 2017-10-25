---
title: Criar um aplicativo .NET do Service Fabric no Azure | Microsoft Docs
description: "Crie um aplicativo .NET para o Azure usando a amostra de início rápido do Service Fabric."
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/02/2017
ms.author: mikhegn
ms.custom: mvc, devcenter
ms.openlocfilehash: 3be8836ae6b877bc4caa98f0467147b008c42aa2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-net-service-fabric-application-in-azure"></a>Criar um aplicativo .NET do Service Fabric no Azure
O Azure Service Fabric é uma plataforma de sistemas distribuídos para implantação e gerenciamento de contêineres e microsserviços escalonáveis e confiáveis. 

Este guia de início rápido mostra como implantar seu primeiro aplicativo .NET no Service Fabric. Quando terminar, você terá um aplicativo de votação com um front-end da Web do ASP.NET Core que salva os resultados da votação em um serviço de back-end com estado do cluster.

![Captura de tela do aplicativo](./media/service-fabric-quickstart-dotnet/application-screenshot.png)

Com esse aplicativo, você aprenderá a:
> [!div class="checklist"]
> * Criar um aplicativo usando o .NET e o Service Fabric
> * Usar o ASP.NET Core como um front-end da Web
> * Armazenar dados de aplicativo em um serviço com estado
> * Depurar o aplicativo localmente
> * Implantar o aplicativo em um cluster no Azure
> * Expandir o aplicativo para vários nós
> * Executar um upgrade sem interrupção do aplicativo

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia de início rápido:
1. [Instale o Visual Studio 2017](https://www.visualstudio.com/) com as cargas de trabalho de **desenvolvimento do Azure** e de **desenvolvimento para a Web e ASP.NET**.
2. [Instalar o Git](https://git-scm.com/)
3. [Instalar o SDK do Microsoft Azure Service Fabric](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK)
4. Execute o seguinte comando para habilitar o Visual Studio a implantar no cluster local do Service Fabric:
    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
    ```

## <a name="download-the-sample"></a>Baixar o exemplo
Em uma janela de comando, execute o comando a seguir para clonar o repositório de aplicativos de exemplo no computador local.
```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="run-the-application-locally"></a>Executar o aplicativo localmente
Clique com o botão direito do mouse no ícone do Visual Studio no Menu Iniciar e escolha **Executar como administrador**. Para anexar o depurador aos serviços, você precisa executar o Visual Studio como administrador.

Abra a solução **Voting.sln** do Visual Studio no repositório clonado.

Para implantar o aplicativo, pressione **F5**.

> [!NOTE]
> Na primeira vez que você executar e implantar o aplicativo, o Visual Studio cria um cluster local para depuração. Essa operação pode levar algum tempo. O status de criação do cluster é exibido na janela de saída do Visual Studio.

Quando a implantação for concluída, inicie um navegador e abra esta página: `http://localhost:8080` – o front-end da Web do aplicativo.

![Front-end do aplicativo](./media/service-fabric-quickstart-dotnet/application-screenshot-new.png)

Agora, você pode adicionar um conjunto de opções de votação e começar a votar. O aplicativo é executado e armazena todos os dados no cluster do Service Fabric, sem a necessidade de um banco de dados separado.

## <a name="walk-through-the-voting-sample-application"></a>Percorrer o aplicativo de exemplo de votação
O aplicativo de votação consiste em dois serviços:
- Serviço de front-end da Web (VotingWeb) – um serviço de front-end da Web do ASP.NET Core, que fornece a página da Web e expõe APIs Web para se comunicar com o serviço de back-end.
- Serviço de back-end (VotingData) – um serviço Web do ASP.NET Core, que expõe uma API para armazenar os resultados da votação em um dicionário confiável persistido em disco.

![Diagrama de aplicativo](./media/service-fabric-quickstart-dotnet/application-diagram.png)

Quando você vota no aplicativo, os seguintes eventos ocorrem:
1. Um JavaScript envia a solicitação de votação para a API Web no serviço de front-end da Web como uma solicitação HTTP PUT.

2. O serviço de front-end da Web usa um proxy para localizar e encaminhar uma solicitação HTTP PUT para o serviço de back-end.

3. O serviço de back-end recebe a solicitação de entrada e armazena o resultado atualizado em um dicionário confiável, que é replicado em vários nós no cluster e persistido em disco. Todos os dados do aplicativo são armazenados no cluster e, portanto, nenhum banco de dados é necessário.

## <a name="debug-in-visual-studio"></a>Depuração no Visual Studio
Ao depurar o aplicativo no Visual Studio, você usa um cluster de desenvolvimento local do Service Fabric. Você tem a opção de ajustar sua experiência de depuração para seu cenário. Neste aplicativo, armazenamos dados em nosso serviço de back-end, usando um dicionário confiável. O Visual Studio remove o aplicativo por padrão quando você interrompe o depurador. A remoção do aplicativo faz com que os dados no serviço de back-end também sejam removidos. Para persistir os dados entre as sessões de depuração, altere o **Modo de Depuração de Aplicativo** como uma propriedade no projeto **Votação** do Visual Studio.

Para ver o que acontece no código, conclua as seguintes etapas:
1. Abra o arquivo **VotesController.cs** e defina um ponto de interrupção no método **Put** da API Web (linha 47). Pesquise o arquivo no Gerenciador de Soluções no Visual Studio.

2. Abra o arquivo **VoteDataController.cs** e defina um ponto de interrupção no método **Put** dessa API Web (linha 50).

3. Volte para o navegador e clique em uma opção de votação ou adicione uma nova opção de votação. Você chegou ao primeiro ponto de interrupção no controlador de API do front-end da Web.
    - Esse é o local em que o JavaScript no navegador envia uma solicitação para o controlador da API Web no serviço de front-end.
    
    ![Adicionar Serviço de Front-end de Voto](./media/service-fabric-quickstart-dotnet/addvote-frontend.png)

    - Primeiro, construímos a URL para o ReverseProxy para nosso serviço de back-end **(1)**.
    - Então, podemos enviar a Solicitação PUT HTTP para o ReverseProxy **(2)**.
    - Por fim, retornamos a resposta do serviço de back-end para o cliente **(3)**.

4. Pressione **F5** para continuar
    - Agora você está no ponto de interrupção no serviço de back-end.
    
    ![Adicionar Serviço de Back-End de Voto](./media/service-fabric-quickstart-dotnet/addvote-backend.png)

    - Na primeira linha do método **(1)**, estamos usando o `StateManager` para obter ou adicionar um dicionário confiável chamado `counts`.
    - Todas as interações com valores em um dicionário confiável exigem uma transação e, portanto, o uso da instrução **(2)** cria essa transação.
    - Na transação, depois, atualizamos o valor da chave relevante para a opção de votação e confirmamos a operação **(3)**. Depois que o método de confirmação for retornado, os dados serão atualizados no dicionário e replicados em outros nós no cluster. Os dados agora estão armazenados com segurança no cluster e o serviço de back-end pode fazer failover para outros nós, ainda tendo os dados disponíveis.
5. Pressione **F5** para continuar

Para interromper a sessão de depuração, pressione **Shift + F5**.

## <a name="deploy-the-application-to-azure"></a>Implantar o aplicativo no Azure
Para implantar o aplicativo em um cluster do Azure, você pode optar por criar seu próprio cluster ou usar um Cluster de Entidade.

Os clusters de entidade são clusters gratuitos de duração limitada do Service Fabric, hospedados no Azure e executados pela equipe do Service Fabric, nos quais qualquer pessoa pode implantar aplicativos e aprender mais sobre a plataforma. Para obter acesso a um Cluster de Terceiros, [siga as instruções](http://aka.ms/tryservicefabric). 

Para obter informações sobre como criar seu próprio cluster, consulte [Criar seu primeiro cluster do Service Fabric no Azure](service-fabric-get-started-azure-cluster.md).

> [!Note]
> O serviço de front-end da Web está configurado para escutar o tráfego de entrada na porta 8080. Verifique se a porta está aberta no cluster. Se você estiver usando o Cluster de Entidade, essa porta estará aberta.
>

### <a name="deploy-the-application-using-visual-studio"></a>Implantar o aplicativo usando o Visual Studio
Agora que o aplicativo está pronto, você poderá implantá-lo no cluster diretamente por meio do Visual Studio.

1. Clique com o botão direito do mouse em **Votação** no Gerenciador de Soluções e escolha **Publicar**. A caixa de diálogo Publicar será exibida.

    ![Caixa de diálogo Publicar](./media/service-fabric-quickstart-dotnet/publish-app.png)

2. Digite o Ponto de Extremidade de Conexão do cluster no campo **Ponto de Extremidade de Conexão** e clique em **Publicar**. Ao se inscrever no Cluster de Entidade, o Ponto de Extremidade de Conexão será fornecido no navegador. – por exemplo, `winh1x87d1d.westus.cloudapp.azure.com:19000`.

3. Abra um navegador e insira o endereço do cluster seguido por ':8080' para alcançar o aplicativo no cluster – por exemplo, `http://winh1x87d1d.westus.cloudapp.azure.com:8080`. Agora, você deverá ver o aplicativo em execução no cluster no Azure.

![Front-end do aplicativo](./media/service-fabric-quickstart-dotnet/application-screenshot-new-azure.png)

## <a name="scale-applications-and-services-in-a-cluster"></a>Dimensionar aplicativos e serviços em um cluster
Os serviços do Service Fabric podem ser facilmente dimensionados em um cluster para acomodar uma alteração na carga dos serviços. Dimensione um serviço alterando o número de instâncias em execução no cluster. Você tem vários modos de dimensionar seus serviços usando scripts ou comandos do PowerShell ou a CLI do Service Fabric (sfctl). Neste exemplo, estamos usando o Service Fabric Explorer.

O Service Fabric Explorer é executado em todos os clusters do Service Fabric e pode ser acessado em um navegador, navegando para a porta de gerenciamento HTTP dos de clusters (19080), por exemplo, `http://winh1x87d1d.westus.cloudapp.azure.com:19080`.

Para dimensionar o serviço de front-end da Web, realize as seguintes etapas:

1. Abra o Service Fabric Explorer no cluster – por exemplo, `http://winh1x87d1d.westus.cloudapp.azure.com:19080`.
2. Clique nas reticências (três pontos) ao lado do nó **fabric:/Voting/VotingWeb** no modo de exibição de árvore e escolha **Dimensionar Serviço**.

    ![Service Fabric Explorer](./media/service-fabric-quickstart-dotnet/service-fabric-explorer-scale.png)

    Agora, você pode optar por dimensionar o número de instâncias do serviço de front-end da Web.

3. Altere o número para **2** e clique em **Dimensionar Serviço**.
4. Clique no nó **fabric:/Voting/VotingWeb** do modo de exibição de árvore e expanda o nó de partição (representado por um GUID).

    ![Dimensionar Serviço do Service Fabric Explorer](./media/service-fabric-quickstart-dotnet/service-fabric-explorer-scaled-service.png)

    Agora, você pode ver que o serviço tem duas instâncias e no modo de exibição de árvore, você vê em quais nós as instâncias são executadas.

Com essa tarefa de gerenciamento simples, dobramos o número de recursos disponíveis para nosso serviço de front-end para processar a carga do usuário. É importante entender que você não precisa de várias instâncias de um serviço para que ele seja executado de forma confiável. Se um serviço falhar, o Service Fabric garantirá que uma nova instância de serviço seja executada no cluster.

## <a name="perform-a-rolling-application-upgrade"></a>Executar um upgrade sem interrupção do aplicativo
Ao implantar novas atualizações no aplicativo, o Service Fabric distribui a atualização com segurança. As atualizações sem interrupção fornecem tempo de inatividade zero durante a atualização, bem como a reversão automática no caso de erros.

Para fazer upgrade do aplicativo, faça o seguinte:

1. Abra o arquivo **Index.cshtml** no Visual Studio – pesquise o arquivo no Gerenciador de Soluções no Visual Studio.
2. Altere o cabeçalho da página adicionando um texto – por exemplo.
    ```html
        <div class="col-xs-8 col-xs-offset-2 text-center">
            <h2>Service Fabric Voting Sample v2</h2>
        </div>
    ```
3. Salve o arquivo.
4. Clique com o botão direito do mouse em **Votação** no Gerenciador de Soluções e escolha **Publicar**. A caixa de diálogo Publicar será exibida.
5. Clique no botão **Versão do Manifesto** para alterar a versão do serviço e do aplicativo.
6. Altere a versão do elemento **Código** sob **VotingWebPkg** para “2.0.0”, por exemplo, e clique em **Salvar**.

    ![Caixa de diálogo Alterar Versão](./media/service-fabric-quickstart-dotnet/change-version.png)
7. Na caixa de diálogo **Publicar Aplicativo do Service Fabric**, marque a caixa de seleção Fazer Upgrade do Aplicativo e clique em **Publicar**.

    ![Configuração de Upgrade da caixa de diálogo Publicar](./media/service-fabric-quickstart-dotnet/upgrade-app.png)
8. Abra o navegador e navegue para o endereço do cluster na porta 19080 – por exemplo, `http://winh1x87d1d.westus.cloudapp.azure.com:19080`.
9. Clique no nó **Aplicativos** do modo de exibição de árvore e, em seguida, em **Upgrades em Andamento** no painel à direita. Você verá como o upgrade é distribuído pelos domínios de upgrade no cluster, garantindo que cada domínio está íntegro antes de continuar com o próximo.
    ![Modo de Exibição de Upgrade no Service Fabric Explorer](./media/service-fabric-quickstart-dotnet/upgrading.png)

    O Service Fabric faz upgrades com segurança, aguardando dois minutos após o upgrade do serviço em cada nó no cluster. Espere que toda a atualização leve aproximadamente oito minutos.

10. Enquanto o upgrade está em execução, você ainda poderá usar o aplicativo. Como você tem duas instâncias do serviço em execução no cluster, algumas das solicitações poderão obter uma versão atualizada do aplicativo, enquanto outras ainda poderão obter a versão antiga.

## <a name="next-steps"></a>Próximas etapas
Neste guia de início rápido, você aprendeu a:

> [!div class="checklist"]
> * Criar um aplicativo usando o .NET e o Service Fabric
> * Usar o ASP.NET Core como um front-end da Web
> * Armazenar dados de aplicativo em um serviço com estado
> * Depurar o aplicativo localmente
> * Implantar o aplicativo em um cluster no Azure
> * Expandir o aplicativo para vários nós
> * Executar um upgrade sem interrupção do aplicativo

Para saber mais sobre o Service Fabric e o .NET, confira este tutorial:
> [!div class="nextstepaction"]
> [Aplicativo .NET no Service Fabric](service-fabric-tutorial-create-dotnet-app.md)
---
title: "Implantar e atualizar microsserviços do Azure localmente | Microsoft Docs"
description: Aprenda a configurar um cluster local do Service Fabric, implante um aplicativo existente nele e atualize esse aplicativo.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 60a1f6a5-5478-46c0-80a8-18fe62da17a8
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/13/2017
ms.author: ryanwi;mikhegn
ms.openlocfilehash: 359677972c7e1fa3f7435052021ddfae5b1ed85e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-deploying-and-upgrading-applications-on-your-local-cluster"></a>Introdução à implantação e à atualização de aplicativos em seu cluster local
O SDK do Service Fabric do Azure inclui um ambiente de desenvolvimento local completo que você pode usar para rapidamente começar a implantar e a gerenciar aplicativos em um cluster local. Neste artigo, você cria um cluster local, implantará um aplicativo existente nele e atualizará o aplicativo para uma nova versão, tudo do Windows PowerShell.

> [!NOTE]
> Este artigo pressupõe que você já tenha [configurado seu ambiente de desenvolvimento](service-fabric-get-started.md).
> 
> 

## <a name="create-a-local-cluster"></a>Criar um cluster local
Um cluster do Service Fabric representa um conjunto de recursos de hardware em que você pode implantar aplicativos. Normalmente, um cluster é composto, em qualquer lugar, de cinco a muitos milhares de computadores. No entanto, o SDK do Service Fabric inclui uma configuração de cluster que pode ser executada em um único computador.

O cluster local do Service Fabric não é um simulador ou um emulador. Ele executa o mesmo código de plataforma encontrado em clusters com vários computadores. A única diferença é que ele executa os processos de plataforma normalmente espalhados por cinco computadores em um só.

O SDK fornece duas maneiras de configurar um cluster local: um script do Windows PowerShell e o aplicativo de bandeja do sistema Gerenciador de Cluster Local. Neste tutorial, usamos o script do PowerShell.

> [!NOTE]
> Se você já tiver criado um cluster local ao implantar um aplicativo do Visual Studio, poderá ignorar esta seção.
> 
> 

1. Inicie uma nova janela do PowerShell como administrador.
2. Execute o script de instalação do cluster da pasta do SDK:
   
    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
    ```
   
    A instalação do cluster leva alguns minutos. Após a conclusão da instalação, você verá uma saída como esta:
   
    ![Saída da instalação do cluster][cluster-setup-success]
   
    Agora você está pronto para tentar implantar um aplicativo em seu cluster.

## <a name="deploy-an-application"></a>Implantar um aplicativo
O SDK do Service Fabric inclui um conjunto avançado de estruturas e de ferramentas de desenvolvedor para a criação de aplicativos. Se você estiver interessado em aprender a criar aplicativos no Visual Studio, veja [Criar seu primeiro aplicativo do Service Fabric no Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).

Neste tutorial, usaremos um aplicativo de exemplo existente (chamado WordCount) para que possamos nos concentrar nos aspectos de gerenciamento da plataforma: implantação, monitoramento e atualização.

1. Inicie uma nova janela do PowerShell como administrador.
2. Importe o módulo do PowerShell do SDK do Service Fabric.
   
    ```powershell
    Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
    ```
3. Crie um diretório para armazenar o aplicativo baixado e implantado, como C:\ServiceFabric.
   
    ```powershell
    mkdir c:\ServiceFabric\
    cd c:\ServiceFabric\
    ```
4. [Baixe o aplicativo WordCount](http://aka.ms/servicefabric-wordcountapp) para o local que você criou.  Observação: o navegador Microsoft Edge salva o arquivo com uma extensão *.zip* .  Altere a extensão do arquivo para *.sfpkg*.
5. Conecte-se ao cluster local:
   
    ```powershell
    Connect-ServiceFabricCluster localhost:19000
    ```
6. Crie um novo aplicativo usando o comando de implantação do SDK com um nome e um caminho para o pacote de aplicativos.
   
    ```powershell  
   Publish-NewServiceFabricApplication -ApplicationPackagePath c:\ServiceFabric\WordCountV1.sfpkg -ApplicationName "fabric:/WordCount"
    ```
   
    Se tudo transcorrer bem, você verá uma saída como a seguinte:
   
    ![Implantar um aplicativo no cluster local][deploy-app-to-local-cluster]
7. Para ver o aplicativo em ação, inicie o navegador e navegue até [http://localhost:8081/wordcount/index.html](http://localhost:8081/wordcount/index.html). Você deverá ver:
   
    ![IU do aplicativo implantado][deployed-app-ui]
   
    O aplicativo WordCount é simples. Ele inclui código JavaScript do lado do cliente para gerar "palavras", de cinco caracteres aleatórias, que serão transmitidas para o aplicativo por meio da API Web ASP.NET. Um serviço com estado controla o número de palavras contadas. Elas serão particionadas com base no primeiro caractere da palavra. Você pode encontrar o código-fonte do aplicativo WordCount nos [exemplos clássicos de introdução](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/WordCount).
   
    O aplicativo que implantamos contém quatro partições. Dessa forma, as palavras de A a G são armazenadas na primeira partição, as palavras iniciadas com H a N são armazenadas na segunda partição e assim por diante.

## <a name="view-application-details-and-status"></a>Exibir detalhes e o status do aplicativo
Agora que implantamos o aplicativo, vamos examinar alguns dos detalhes do aplicativo no PowerShell.

1. Consulte todos os aplicativos implantados no cluster:
   
    ```powershell
    Get-ServiceFabricApplication
    ```
   
    Supondo que tenha implantado somente o aplicativo WordCount, você verá algo assim:
   
    ![Consultar todos os aplicativos implantados no PowerShell][ps-getsfapp]
2. Vá para o próximo nível ao consultar o conjunto de serviços incluído no aplicativo WordCount.
   
    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```
   
    ![Listar serviços para o aplicativo no PowerShell][ps-getsfsvc]
   
    O aplicativo é composto de dois serviços, o front-end da Web e o serviço com estado que gerencia as palavras.
3. Por fim, observe a lista de partições para o WordCountService:
   
    ```powershell
    Get-ServiceFabricPartition 'fabric:/WordCount/WordCountService'
    ```
   
    ![Exibir as partições de serviço no PowerShell][ps-getsfpartitions]
   
    O conjunto de comandos que você usou, como todos os comandos do PowerShell do Service Fabric, estão disponíveis para qualquer cluster ao qual você queira se conectar, local ou remoto.
   
    Para obter uma maneira mais visual de interagir com o cluster, você poderá usar a ferramenta Service Fabric Explorer baseada na Web ao acessar [http://localhost:19080/Explorer](http://localhost:19080/Explorer) no navegador.
   
    ![Exibir detalhes do aplicativo no Service Fabric Explorer][sfx-service-overview]
   
   > [!NOTE]
   > Para saber mais sobre o Service Fabric Explorer, consulte [Visualizando o cluster com o Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
   > 
   > 

## <a name="upgrade-an-application"></a>Atualizar um aplicativo
O Service Fabric oferece atualizações sem tempo de inatividade por meio do monitoramento da integridade do aplicativo à medida que ele percorre o cluster. Faça uma atualização do aplicativo WordCount.

A nova versão do aplicativo agora conta somente as palavras que comecem por vogal. À medida que a atualização for distribuída, veremos duas alterações no comportamento do aplicativo. Primeiro, a taxa na qual a contagem aumenta deve ficar mais lenta, já que menos palavras serão contadas. Em segundo lugar, como a primeira partição tem duas vogais (A e E) e todas as outras partições contêm apenas uma, sua contagem eventualmente deverá começar a ultrapassar as outras.

1. [Baixe o pacote do WordCount versão 2](http://aka.ms/servicefabric-wordcountappv2) no mesmo local onde você baixou o pacote versão 1.
2. Volte para a janela do PowerShell e use o comando de atualização do SDK para registrar a nova versão do cluster. Em seguida, comece a atualização do aplicativo fabric:/WordCount.
   
    ```powershell
    Publish-UpgradedServiceFabricApplication -ApplicationPackagePath C:\ServiceFabric\WordCountV2.sfpkg -ApplicationName "fabric:/WordCount" -UpgradeParameters @{"FailureAction"="Rollback"; "UpgradeReplicaSetCheckTimeout"=1; "Monitored"=$true; "Force"=$true}
    ```
   
    Você deverá ver a seguinte saída no PowerShell quando a atualização for iniciada.
   
    ![Andamento da atualização no PowerShell][ps-appupgradeprogress]
3. Durante a atualização, talvez você ache mais fácil monitorar seu status do Service Fabric Explorer. Inicie uma janela de navegador e navegue até [http://localhost:19080/Explorer](http://localhost:19080/Explorer). Expanda **Aplicativos** na árvore à esquerda, escolha **WordCount** e, por fim, **fabric:/WordCount**. Na guia essentials, você verá o status da atualização conforme ela prossegue pelos domínios de atualização do cluster.
   
    ![Andamento da atualização no Service Fabric Explorer][sfx-upgradeprogress]
   
    À medida que a atualização prosseguir em cada domínio, serão executadas verificações de integridade para garantir que o aplicativo está se comportando adequadamente.
4. Se você executar novamente a consulta anterior para o conjunto de serviços no aplicativo fabric:/WordCount, observará que a versão do WordCountService foi alterada, mas a versão do WordCountWebService não foi:
   
    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```
   
    ![Consultar serviços de aplicativos após a atualização][ps-getsfsvc-postupgrade]
   
    Este exemplo destaca como o Service Fabric gerencia atualizações de aplicativo. Isso toca apenas o conjunto de serviços (ou pacotes de código/configuração nesses serviços) que foram alterados, tornando o processo de atualização mais rápido e confiável.
5. Por fim, retorne ao navegador para observar o comportamento da nova versão do aplicativo. Como esperado, a contagem é mais lenta e a primeira partição termina com um volume ligeiramente maior.
   
    ![Exibir a nova versão do aplicativo no navegador][deployed-app-ui-v2]

## <a name="cleaning-up"></a>Limpando
Antes da conclusão, é importante lembrar que o cluster local é real. Os aplicativos continuam sendo executados em segundo plano até você removê-los.  Dependendo da natureza de seus aplicativos, um aplicativo em execução pode consumir recursos significativos em seu computador. Você tem várias opções para gerenciar aplicativos e o cluster:

1. Para remover um aplicativo individual e todos os seus dados, execute o seguinte comando:
   
    ```powershell
    Unpublish-ServiceFabricApplication -ApplicationName "fabric:/WordCount"
    ```
   
    Como alternativa, exclua o aplicativo do menu **AÇÕES** do Service Fabric Explorer ou do menu de contexto na exibição de lista do aplicativo do painel esquerdo.
   
    ![Excluir um aplicativo no Service Fabric Explorer][sfe-delete-application]
2. Depois de excluir o aplicativo do cluster, cancele o registro das versões 1.0.0 e 2.0.0 do tipo de aplicativo WordCount. A exclusão remove os pacotes de aplicativos, inclusive o código e a configuração, do armazenamento de imagens do cluster.
   
    ```powershell
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 2.0.0
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 1.0.0
    ```
   
    Ou, no Service Fabric Explorer, escolha **Remover Provisionamento do Tipo** para o aplicativo.
3. Para desativar o cluster, mas manter os dados e os rastreamentos do aplicativo, clique em **Parar Cluster Local** no aplicativo da bandeja do sistema.
4. Para excluir totalmente o cluster, clique em **Remover Cluster Local** no aplicativo da bandeja do sistema. Essa opção resultará em outra implantação lenta na próxima vez que você pressionar F5 no Visual Studio. Só remova o cluster local se você não pretender usá-lo por algum tempo ou se precisar recuperar recursos.

## <a name="one-node-and-five-node-cluster-mode"></a>Modo de um nó e de cinco nós do cluster
Ao desenvolver aplicativos, você geralmente acaba fazendo iterações rápidas entre a produção do código, depuração, alteração do código e depuração. Para ajudar a otimizar esse processo, o cluster local pode ser executado em dois modos: um nó ou cinco nós. Os dois modos de cluster têm seus benefícios. O modo de cluster de cinco nós permite que você trabalhe com um cluster real. Você pode testar os cenários de failover, trabalhar com mais instâncias e réplicas de seus serviços. O modo de cluster de um nó é otimizado para fazer implantação e registro rápidos de serviços, a fim de ajudar você a validar rapidamente o código usando o tempo de execução do Service Fabric.

Tanto o modo de um nó como o de cinco nós do cluster não são um emulador ou simulador. O cluster local de desenvolvimento executa o mesmo código de plataforma encontrado em clusters com vários computadores.

> [!WARNING]
> Quando você altera o modo de cluster, o cluster atual é removido do seu sistema e um novo cluster é criado. Os dados armazenados no cluster são excluídos quando você altera o modo de cluster.
> 
> 

Para alterar o modo para o modo de um nó de cluster, selecione **Alternar Modo de Cluster** no Gerenciador de Cluster Local do Service Fabric.

![Alternar o modo de cluster][switch-cluster-mode]

Como alternativa, altere o modo de cluster usando o PowerShell:

1. Inicie uma nova janela do PowerShell como administrador.
2. Execute o script de instalação do cluster da pasta do SDK:
   
    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateOneNodeCluster
    ```
   
    A instalação do cluster leva alguns minutos. Após a conclusão da instalação, você verá uma saída como esta:
   
    ![Saída da instalação do cluster][cluster-setup-success-1-node]

## <a name="next-steps"></a>Próximas etapas
* Agora que você implantou e atualizou alguns aplicativos pré-compilados, poderá [tentar compilar seu próprio aplicativo no Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).
* Todas as ações executadas no cluster local neste artigo também podem ser executadas em um [cluster do Azure](service-fabric-cluster-creation-via-portal.md) .
* A atualização que executamos neste artigo foi básica. Consulte a [documentação de atualização](service-fabric-application-upgrade.md) para saber mais sobre a eficiência e a flexibilidade das atualizações do Service Fabric.

<!-- Images -->

[cluster-setup-success]: ./media/service-fabric-get-started-with-a-local-cluster/LocalClusterSetup.png
[extracted-app-package]: ./media/service-fabric-get-started-with-a-local-cluster/ExtractedAppPackage.png
[deploy-app-to-local-cluster]: ./media/service-fabric-get-started-with-a-local-cluster/DeployAppToLocalCluster.png
[deployed-app-ui]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-v1.png
[deployed-app-ui-v2]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-PostUpgrade.png
[sfx-app-instance]: ./media/service-fabric-get-started-with-a-local-cluster/SfxAppInstance.png
[sfx-two-app-instances-different-partitions]: ./media/service-fabric-get-started-with-a-local-cluster/SfxTwoAppInstances-DifferentPartitionCount.png
[ps-getsfapp]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFApp.png
[ps-getsfsvc]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc.png
[ps-getsfpartitions]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFPartitions.png
[ps-appupgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/PS-AppUpgradeProgress.png
[ps-getsfsvc-postupgrade]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc-PostUpgrade.png
[sfx-upgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/SfxUpgradeOverview.png
[sfx-service-overview]: ./media/service-fabric-get-started-with-a-local-cluster/sfx-service-overview.png
[sfe-delete-application]: ./media/service-fabric-get-started-with-a-local-cluster/sfe-delete-application.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[switch-cluster-mode]: ./media/service-fabric-get-started-with-a-local-cluster/switch-cluster-mode.png

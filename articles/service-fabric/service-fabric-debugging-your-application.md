---
title: Depurar seu aplicativo no Visual Studio | Microsoft Docs
description: "Melhore a confiabilidade e o desempenho dos seus serviços desenvolvendo e depurando-os no Visual Studio em um cluster de desenvolvimento local."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: cb888532-bcdb-4e47-95e4-bfbb1f644da4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: vturecek;mikhegn
ms.openlocfilehash: 2459025899a7f5ffebf44fa104ed112c0eb99dfa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>Depurar seu aplicativo do Service Fabric usando o Visual Studio
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
>


## <a name="debug-a-local-service-fabric-application"></a>Depurar um aplicativo local do Service Fabric
Você pode economizar tempo e dinheiro implantando e depurando seu aplicativo do Service Fabric do Azure no cluster de desenvolvimento de um computador local. O Visual Studio 2015 ou 2017 pode implantar o aplicativo no cluster local e conectar o depurador automaticamente a todas as instâncias do seu aplicativo.

1. Inicie um cluster de desenvolvimento local seguindo as etapas em [Configurando o ambiente de desenvolvimento do Service Fabric](service-fabric-get-started.md).
2. Pressione **F5** ou clique em **Depurar** > **Iniciar Depuração**.
   
    ![Iniciar depuração de um aplicativo][startdebugging]
3. Defina os pontos de interrupção em seu código e explore o aplicativo clicando nos comandos do menu **Depurar** .
   
   > [!NOTE]
   > O Visual Studio se conecta a todas as instâncias do seu aplicativo. Ao percorrer o código, os pontos de interrupção podem ser atingidos por vários processos, resultando em sessões simultâneas. Tente desabilitar os pontos de interrupção depois que eles forem atingidos, tornando o ponto de interrupção condicional na ID do thread, ou usando eventos de diagnóstico.
   > 
   > 
4. A janela **Eventos de Diagnóstico** abrirá automaticamente para exibir eventos de diagnóstico em tempo real.
   
    ![Exibir eventos de diagnóstico em tempo real][diagnosticevents]
5. Você também pode abrir a janela **Eventos de Diagnóstico** no Gerenciador de Nuvem.  Em **Service Fabric**, clique com o botão direito do mouse em qualquer nó e escolha **Exibir Rastreamentos de Streaming**.
   
    ![Abrir a janela de eventos de diagnóstico][viewdiagnosticevents]
   
    Se quiser filtrar os rastreamentos para um serviço ou aplicativo específico, basta habilitar a transmissão de rastreamentos nesse serviço ou aplicativo específico.
6. Os eventos de diagnóstico podem ser vistos no arquivo **ServiceEventSource.cs** gerado automaticamente e são chamados do código do aplicativo.
   
    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```
7. A janela **Eventos de Diagnóstico** permite filtragem, pausa e eventos de inspeção em tempo real.  O filtro é uma pesquisa simples de cadeia de caracteres da mensagem do evento, incluindo seu conteúdo.
   
    ![Filtrar, pausar e retomar ou inspecionar eventos em tempo real][diagnosticeventsactions]
8. A depuração de serviços é semelhante à depuração de qualquer outro aplicativo. Normalmente, você define pontos de interrupção por meio do Visual Studio para fácil depuração. Embora as Reliable Collections sejam replicadas em vários nós, elas ainda implementam IEnumerable. Isso significa que você pode usar o Modo de Exibição Resultados no Visual Studio durante a depuração para ver o que foi armazenado. Basta definir um ponto de interrupção em qualquer lugar em seu código.
   
    ![Iniciar depuração de um aplicativo][breakpoint]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>Depurar um aplicativo remoto do Service Fabric
Se seus aplicativos do Service Fabric estiverem em execução em um cluster do Service Fabric no Azure, é possível depurá-los remotamente, diretamente do Visual Studio.

> [!NOTE]
> O recurso requer [SDK do Service Fabric 2.0](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) e [SDK do Azure para .NET 2.9](https://azure.microsoft.com/downloads/).    
> 
> 

<!-- -->
> [!WARNING]
> A depuração remota é destinada a cenários de desenvolvimento/teste e não para uso em ambientes de produção, devido ao impacto nos aplicativos em execução.
> 
> 

1. Navegue até o cluster em **Gerenciador de Nuvem**, clique com o botão direito do mouse e escolha **Habilitar Depuração**
   
    ![Habilitar depuração remota][enableremotedebugging]
   
    Isso iniciará o processo de habilitar a extensão de depuração remota em seus nós de cluster, bem como as configurações de rede necessárias.
2. Clique com o botão direito do mouse no nó de cluster em **Gerenciador de Nuvem** e escolha **Anexar Depurador**
   
    ![Anexar Depurador][attachdebugger]
3. Na caixa de diálogo **Anexar ao processo**, escolha o processo que deseja depurar e, em seguida, clique em **Anexar**
   
    ![Escolher processo][chooseprocess]
   
    O nome do processo ao qual você quer anexar é igual ao nome do assembly do seu projeto de serviço.
   
    O depurador vai anexar a todos os nós que estão executando o processo.
   
   * No caso em que você estiver depurando um serviço sem estado, todas as instâncias do serviço em todos os nós farão parte da sessão de depuração.
   * Se estiver depurando um serviço com estado, somente a réplica primária de qualquer partição estará ativa e, portanto, a que será capturada pelo depurador. Se a réplica primária for movida durante a sessão de depuração, o processamento de tal réplica ainda fará parte da sessão de depuração.
   * Para capturar apenas as partições ou instâncias relevantes de um determinado serviço, você pode usar pontos de interrupção condicionais para interromper apenas uma partição ou instância específica.
     
     ![Ponto de interrupção condicional][conditionalbreakpoint]
     
     > [!NOTE]
     > Atualmente, não permitimos a depuração de um cluster do Service Fabric com várias instâncias de mesmo nome do executável de serviço.
     > 
     > 
4. Depois de concluir a depuração do aplicativo, você pode desabilitar a extensão de depuração remota clicando com o botão direito do mouse em **Gerenciador de Nuvem** e escolhendo **Desabilitar Depuração**
   
    ![Desabilitar depuração remota][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>Transmitindo rastreamentos de um nó de cluster remoto
Você também pode transmitir rastreamentos diretamente de um nó de cluster remoto para o Visual Studio. Esse recurso permite transmitir eventos de rastreamento ETW gerados em um nó de cluster do Service Fabric.

> [!NOTE]
> O recurso requer o [SDK do Service Fabric 2.0](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) e o [SDK do Azure para .NET 2.9](https://azure.microsoft.com/downloads/).
> Esse recurso só oferece suporte a clusters em execução no Azure.
> 
> 

<!-- -->
> [!WARNING]
> A transmissão de rastreamentos destina-se a cenários de desenvolvimento/teste e não ao uso em ambientes de produção, devido ao impacto nos aplicativos em execução.
> Em um cenário de produção, você deve confiar nos eventos de encaminhamento usando o Diagnóstico do Azure.
> 
> 

1. Navegue até seu cluster no **Gerenciador de Nuvem**, clique com o botão direito do mouse e escolha **Habilitar Transmissão de Rastreamentos**
   
    ![Habilitar transmissão remota de rastreamentos][enablestreamingtraces]
   
    Isso iniciará o processo de habilitar a extensão de rastreamentos de streaming em seus nós de cluster, bem como as configurações de rede necessárias.
2. Expanda o elemento **Nós** no **Gerenciador de Nuvem**, clique com o botão direito do mouse no nó do qual deseja transmitir rastreamentos e escolha **Exibir Transmissão de Rastreamentos**
   
    ![Exibir transmissão remota de rastreamentos][viewremotestreamingtraces]
   
    Repita a etapa 2 para o número de nós dos quais você quer ver os rastreamentos. Cada fluxo de nó será mostrado em uma janela dedicada.
   
    Agora você poderá ver os rastreamentos emitidos pelo Service Fabric e seus serviços. Se quiser filtrar os eventos para mostrar somente um aplicativo específico, basta digitar o nome do aplicativo no filtro.
   
    ![Exibindo transmissão de rastreamentos][viewingstreamingtraces]
3. Depois de terminar a transmissão de rastreamentos do seu cluster, é possível desabilitar a transmissão remota de rastreamentos clicando com o botão direito do mouse no cluster em **Gerenciador de Nuvem** e escolhendo **Desabilitar Rastreamentos de Streaming**
   
    ![Desabilitar transmissão remota de rastreamentos][disablestreamingtraces]

## <a name="next-steps"></a>Próximas etapas
* [Testar um serviço da Malha do Serviço](service-fabric-testability-overview.md).
* [Gerenciar seu aplicativo do Service Fabric no Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png
[enableremotedebugging]: ./media/service-fabric-debugging-your-application/enableremotedebugging.png
[attachdebugger]: ./media/service-fabric-debugging-your-application/attachdebugger.png
[chooseprocess]: ./media/service-fabric-debugging-your-application/chooseprocess.png
[conditionalbreakpoint]: ./media/service-fabric-debugging-your-application/conditionalbreakpoint.png
[disableremotedebugging]: ./media/service-fabric-debugging-your-application/disableremotedebugging.png
[enablestreamingtraces]: ./media/service-fabric-debugging-your-application/enablestreamingtraces.png
[viewingstreamingtraces]: ./media/service-fabric-debugging-your-application/viewingstreamingtraces.png
[viewremotestreamingtraces]: ./media/service-fabric-debugging-your-application/viewremotestreamingtraces.png
[disablestreamingtraces]: ./media/service-fabric-debugging-your-application/disablestreamingtraces.png

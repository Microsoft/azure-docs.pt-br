---
title: Monitoramento e diagnóstico dos serviços ASP.NET Core no Service Fabric no Azure | Microsoft Docs
description: Neste tutorial, você aprenderá como configurar monitoramento e diagnóstico para um aplicativo ASP.NET Core do Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/10/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 1f18aef12978b3df1ba1fd654ea4a0e9548a4b46
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68228087"
---
# <a name="tutorial-monitor-and-diagnose-an-aspnet-core-application-on-service-fabric-using-application-insights"></a>Tutorial: Monitorar e diagnosticar um aplicativo ASP.NET Core no Service Fabric usando o Application Insights

Este tutorial é a parte cinco de uma série. Ele passa pelas etapas para configurar o monitoramento e o diagnóstico para um aplicativo ASP.NET Core em execução em um cluster do Service Fabric usando o Application Insights. Coletaremos a telemetria do aplicativo desenvolvido na primeira parte do tutorial, [Criar um aplicativo .NET do Service Fabric](service-fabric-tutorial-create-dotnet-app.md).

Na parte quatro da série de tutoriais, você aprenderá a:
> [!div class="checklist"]
> * Configurar o Application Insights para seu aplicativo
> * Coletar telemetria de resposta para rastrear comunicação baseada em HTTP entre serviços
> * Usar o recurso Mapa de aplicativos no Application Insights
> * Adicionar eventos personalizados usando a API do Application Insights

Nesta série de tutoriais, você aprenderá a:
> [!div class="checklist"]
> * [Criar um aplicativo .NET do Service Fabric](service-fabric-tutorial-create-dotnet-app.md)
> * [Implantar o aplicativo em um cluster remoto](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Adicionar um ponto de extremidade HTTPS a um serviço de front-end do ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [Configurar CI/CD usando o Azure Pipelines](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * Configurar o monitoramento e o diagnóstico do aplicativo

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se você não tem uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Instale o Visual Studio 2019](https://www.visualstudio.com/) e as cargas de trabalho **Desenvolvimento do Azure** e **Desenvolvimento para a Web e ASP.NET**.
* [Instalar o SDK do Service Fabric](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>Baixar o aplicativo de exemplo Votação

Se você não tiver criado o aplicativo de exemplo Votação na [parte um esta série de tutoriais](service-fabric-tutorial-create-dotnet-app.md), poderá baixá-lo. Em um terminal ou em uma janela de comando, execute o comando a seguir para clonar o repositório de aplicativos de exemplo no computador local.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="set-up-an-application-insights-resource"></a>Configurar um recurso do Application Insights

O Application Insights é a plataforma de gerenciamento de desempenho de aplicativo do Azure e a plataforma recomendada do Service Fabric para monitoramento e diagnóstico de aplicativos.

Para criar um recurso do Application Insights navegue até o [Portal do Azure](https://portal.azure.com). Clique em **Criar um recurso** no menu de navegação à esquerda para abrir o Azure Marketplace. Clique em **Monitoramento + gerenciamento** e, em seguida, em **Application Insights**.

![Criar novo recurso do AI](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource.png)

Agora será necessário preencher as informações necessárias sobre os atributos do recurso a ser criado. Insira um *Nome*, *Grupo de recursos* e *Assinatura* adequados. Defina o *Local* no qual você implantaria seu cluster do Service Fabric no futuro. Neste tutorial, implantaremos o aplicativo em um cluster local, então o campo *Local* é irrelevante. O *Tipo de aplicativo* deve ser deixado como "Aplicativo Web ASP.NET."

![Atributos de recursos do AI](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource-attrib.png)

Depois de preencher as informações necessárias, clique em **Criar** para provisionar o recurso – deve levar cerca de um minuto.
<!-- When completed, navigate to the newly deployed resource, and find the "Instrumentation Key" (visible in the "Essentials" drop down section). Copy it to clipboard, since we will need it in the next step. -->

## <a name="add-application-insights-to-the-applications-services"></a>Adicionar o Application Insights aos serviços do aplicativo

Inicie o Visual Studio 2019 com privilégios elevados clicando com o botão direito do mouse no ícone do Visual Studio no Menu Iniciar e escolhendo **Executar como administrador**. Clique em **Arquivo** > **Abrir** > **Projeto/solução** e navegue até o aplicativo de votação (criado na parte um do tutorial ou clonado pelo git). Abra *Voting.sln*. Caso precise restaurar os pacotes NuGet do aplicativo, clique em **Sim**.

Siga estas etapas para configurar o Application Insights para os serviços VotingWeb e VotingData:

1. Clique com o botão direito do mouse no nome do serviço e clique em **Adicionar > Serviços Conectados > Monitoramento com o Application Insights**.

    ![Configurar o AI](./media/service-fabric-tutorial-monitoring-aspnet/configure-ai.png)
>[!NOTE]
>Dependendo do tipo de projeto, quando você clica com o botão direito no nome do serviço, talvez tenha que clicar em Adicionar -> Application Insights Telemetry...

2. Clique em **Introdução**.
3. Entre na conta que você usou para configurar sua assinatura do Azure e selecione a assinatura na qual você criou o recurso do Application Insights. Localize o recurso em *Recurso do Application Insights existente* no menu suspenso "Recurso". Clique em **Registrar** para adicionar o Application Insights ao seu serviço.

    ![Registrar o AI](./media/service-fabric-tutorial-monitoring-aspnet/register-ai.png)

4. Clique em **Concluir** quando a caixa de diálogo que surgir concluir a ação.

> [!NOTE]
> Certifique-se de seguir as etapas acima para **ambos** os serviços no aplicativo concluírem a configuração do Application Insights para o aplicativo.
> O mesmo recurso do Application Insights é usado para ambos os serviços a fim de ver solicitações e comunicação de entrada e de saída entre os serviços.

## <a name="add-the-microsoftapplicationinsightsservicefabricnative-nuget-to-the-services"></a>Adicionar o NuGet do Microsoft.ApplicationInsights.ServiceFabric.Native aos serviços

O Application Insights tem dois NuGets específicos do Service Fabric que podem ser usados dependendo do cenário. Um é usado com serviços nativos do Service Fabric e o outro, com contêineres e executáveis do convidado. Nesse caso, usaremos o NuGet do Microsoft.ApplicationInsights.ServiceFabric.Native para aproveitar a compreensão do contexto do serviço que ele oferece. Para ler mais sobre o SDK do Application Insights e os NuGets específicos do Service Fabric, consulte [Microsoft Application Insights for Service Fabric](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md) (Microsoft Application Insights para Service Fabric).

Estas são as etapas para configurar o pacote NuGet:

1. Clique com o botão direito do mouse na **Solução 'Voting'** na parte superior do Gerenciador de Soluções e clique em **Gerenciar Pacotes NuGet para a Solução...**
2. Clique em **Procurar** no menu de navegação superior da janela "NuGet – Solução" e marque a caixa **Incluir pré-lançamento** ao lado da barra de pesquisa.
>[!NOTE]
>Antes de instalar o pacote do Application Insights, talvez você precise instalar o pacote Microsoft.ServiceFabric.Diagnostics.Internal de maneira semelhante se ele já não estiver instalado

3. Pesquise `Microsoft.ApplicationInsights.ServiceFabric.Native` e clique no pacote NuGet adequado.
4. À direita, clique nas duas caixas de seleção ao lado dos dois serviços no aplicativo, **VotingWeb** e **VotingData** e clique em **Instalar**.
    ![Nuget do sdk AI](./media/service-fabric-tutorial-monitoring-aspnet/ai-sdk-nuget-new.png)
5. Clique em **OK** na caixa de diálogo *Visualizar Alterações* exibida e aceite a *Aceitação da Licença*. Isso concluirá a adição do NuGet aos serviços.
6. Agora, é necessário configurar o inicializador de telemetria nos dois serviços. Para fazer isso, abra *VotingWeb.cs* e *VotingData.cs*. Para ambos, siga as duas etapas a seguir:
    1. Adicione estas duas instruções *using* na parte superior de cada *\<NomeServiço>.cs*, após as instruções *using* existentes:

    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.ServiceFabric;
    ```

    2. Em ambos os arquivos, na instrução *return* aninhada de *CreateServiceInstanceListeners()* ou *CreateServiceReplicaListeners()* , em *ConfigureServices* > *serviços*, com os outros serviços singleton declarados, adicione:
    ```csharp
    .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext))
    ```
    Isso adicionará o *Contexto de serviço* à sua telemetria, permitindo a você entender melhor a origem da sua telemetria no Application Insights. Sua instrução *return* aninhada em *VotingWeb.cs* deve ter esta aparência:

    ```csharp
    return new WebHostBuilder()
        .UseKestrel()
        .ConfigureServices(
            services => services
                .AddSingleton<HttpClient>(new HttpClient())
                .AddSingleton<FabricClient>(new FabricClient())
                .AddSingleton<StatelessServiceContext>(serviceContext)
                .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext)))
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseStartup<Startup>()
        .UseApplicationInsights()
        .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
        .UseUrls(url)
        .Build();
    ```

    Da mesma forma, em *VotingData.cs*, você deverá ter:

    ```csharp
    return new WebHostBuilder()
        .UseKestrel()
        .ConfigureServices(
            services => services
                .AddSingleton<StatefulServiceContext>(serviceContext)
                .AddSingleton<IReliableStateManager>(this.StateManager)
                .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext)))
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseStartup<Startup>()
        .UseApplicationInsights()
        .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
        .UseUrls(url)
        .Build();
    ```

Verifique se o método `UseApplicationInsights()` é chamado em *VotingWeb.cs* e *VotingData.cs*, conforme mostrado acima.

>[!NOTE]
>Esse aplicativo de exemplo usa http para a comunicação dos serviços. Se você desenvolver um aplicativo com o Serviço de Comunicação Remota V2, também será preciso adicionar as seguintes linhas de código no mesmo local, como feito acima

```csharp
ConfigureServices(services => services
    ...
    .AddSingleton<ITelemetryModule>(new ServiceRemotingDependencyTrackingTelemetryModule())
    .AddSingleton<ITelemetryModule>(new ServiceRemotingRequestTrackingTelemetryModule())
)
```

Neste ponto, você está pronto para implantar o aplicativo. Clique em **Iniciar** na parte superior (ou **F5**) e o Visual Studio compilará e empacotará o aplicativo, configurará seu cluster local e implantará o aplicativo nele.

>[!NOTE]
>Você poderá receber um erro de build se não tiver uma versão atualizada do SDK do .NET Core instalada.

Quando o aplicativo terminar a implantação, vá para [localhost:8080](localhost:8080), em que você deverá conseguir ver o aplicativo de página única de votação de exemplo. Votar em alguns itens diferentes de sua escolha para criar alguns dados e a telemetria de exemplo – fui pelas sobremesas!

![Votos de exemplo do AI](./media/service-fabric-tutorial-monitoring-aspnet/vote-sample.png)

Fique à vontade para *Remover* algumas opções de votação também quando você terminar de adicionar alguns votos.

## <a name="view-telemetry-and-the-app-map-in-application-insights"></a>Exibir a telemetria e o mapa de aplicativos no Application Insights

Vá até o recurso do Application Insights no Portal do Azure.

Clique em **Visão geral** para voltar para a página de aterrissagem do seu recurso. Em seguida, clique em **Pesquisar** na parte superior para ver os rastreamentos sendo recebidos. Levará alguns minutos para que os rastreamentos sejam exibidos no Application Insights. Caso você não tenha visto nenhum, aguarde um minuto e pressione o botão **Atualizar** na parte superior.
![Ver rastreamentos no AI](./media/service-fabric-tutorial-monitoring-aspnet/ai-search.png)

Rolar a janela *Pesquisar* para baixo mostrará toda a telemetria de entrada que você recebe pronta para usar com o Application Insights. Para cada ação que você executou no aplicativo de votação, deve haver uma solicitação PUT de saída de *VotingWeb* (PUT Votes/Put [nome]), uma solicitação PUT de entrada de *VotingData* (PUT VoteData/Put [nome]), seguida por um par de solicitações GET para atualizar os dados que estão sendo exibidos. Também haverá um rastreamento de dependência para HTTP no localhost, já que eles são solicitações HTTP. Este é um exemplo do que você verá para a maneira como um voto é adicionado:

![Rastreamento de solicitação de exemplo de IA](./media/service-fabric-tutorial-monitoring-aspnet/sample-request.png)

É possível clicar em um dos rastreamentos para exibir mais detalhes sobre ele. Há informações úteis sobre a solicitação fornecidas pelo Application Insights, incluindo o *Tempo de resposta* e a *URL de solicitação*. Além disso, como você adicionou o NuGet específico do Service Fabric, você também receberá dados sobre o seu aplicativo no contexto de um cluster do Service Fabric na seção *Dados personalizados* abaixo. Isso inclui o contexto de serviço para que você possa ver o *PartitionID* e o *ReplicaId* da origem da solicitação e localizar melhor problemas durante o diagnóstico de erros em seu aplicativo.

![Detalhes de rastreamento do AI](./media/service-fabric-tutorial-monitoring-aspnet/trace-details.png)

Além disso, você pode clicar no *Mapa do aplicativo* no menu à esquerda na página Visão Geral ou clicar no ícone do **Mapa da aplicativo** para ir até o mapa que mostra os dois serviços conectados.

![Detalhes de rastreamento do AI](./media/service-fabric-tutorial-monitoring-aspnet/app-map-new.png)

O mapa de aplicativos pode ajudar a entender melhor a topologia do seu aplicativo, principalmente quando você começa a adicionar vários serviços diferentes que funcionam em conjunto. Ele também fornece dados básicos sobre taxas de sucesso de solicitação e pode ajudá-lo a diagnosticar a solicitação com falha para entender onde as coisas podem ter dado errado. Para saber mais sobre como usar o mapa de aplicativos, consulte [Mapa de aplicativos no Azure Application Insights](../azure-monitor/app/app-map.md).

## <a name="add-custom-instrumentation-to-your-application"></a>Adicionar instrumentação personalizada ao seu aplicativo

Embora o Application Insights forneça muita telemetria pronta para usar, convém adicionar ainda mais instrumentação personalizada. Isso pode ocorrer com base em suas necessidades de negócios ou para melhorar o diagnóstico quando as coisas dão errado em seu aplicativo. O Application Insights tem uma API para ingerir métricas e eventos personalizados, sobre a qual você pode ler mais [aqui](../azure-monitor/app/api-custom-events-metrics.md).

Vamos adicionar alguns eventos personalizados a *VoteDataController.cs* (em *VotingData* > *Controladores*) para controlar quando os votos estão sendo adicionados e excluídos do *votesDictionary* subjacente.

1. Adicione `using Microsoft.ApplicationInsights;` no final do outro usando as instruções.
2. Declare um novo *TelemetryClient* no início da classe, na criação do *IReliableStateManager*: `private TelemetryClient telemetry = new TelemetryClient();`.
3. Na função *Put()* , adicione um evento que confirma que um voto foi adicionado. Adicione `telemetry.TrackEvent($"Added a vote for {name}");` depois que a transação tiver sido concluída, antes da instrução *OkResult* de retorno.
4. Em *Delete()* , há um "if/else" baseado na condição se *votesDictionary* contém votos para uma determinada opção de votação.
    1. Adicione um evento que confirma a exclusão de um voto na instrução *if*, após *await tx.CommitAsync()* :`telemetry.TrackEvent($"Deleted votes for {name}");`
    2. Adicione um evento para mostrar que a exclusão não aconteceu na instrução *else* antes da instrução de retorno: `telemetry.TrackEvent($"Unable to delete votes for {name}, voting option not found");`

Veja um exemplo da aparência de suas funções *Put()* e *Delete()* após adicionar os eventos:

```csharp
// PUT api/VoteData/name
[HttpPut("{name}")]
public async Task<IActionResult> Put(string name)
{
    var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

    using (ITransaction tx = this.stateManager.CreateTransaction())
    {
        await votesDictionary.AddOrUpdateAsync(tx, name, 1, (key, oldvalue) => oldvalue + 1);
        await tx.CommitAsync();
    }

    telemetry.TrackEvent($"Added a vote for {name}");
    return new OkResult();
}

// DELETE api/VoteData/name
[HttpDelete("{name}")]
public async Task<IActionResult> Delete(string name)
{
    var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

    using (ITransaction tx = this.stateManager.CreateTransaction())
    {
        if (await votesDictionary.ContainsKeyAsync(tx, name))
        {
            await votesDictionary.TryRemoveAsync(tx, name);
            await tx.CommitAsync();
            telemetry.TrackEvent($"Deleted votes for {name}");
            return new OkResult();
        }
        else
        {
            telemetry.TrackEvent($"Unable to delete votes for {name}, voting option not found");
            return new NotFoundResult();
        }
    }
}
```

Quando terminar de fazer essas alterações, **Inicie** o aplicativo para ele compilar e implantar a versão mais recente dele. Quando o aplicativo terminar a implantação, vá para [localhost:8080](localhost:8080), adicione e exclua algumas opções de votação. Em seguida, volte para o recurso do Application Insights para ver os rastreamentos para a execução mais recente (como antes, os rastreamentos podem levar de 1 a 2 min para serem exibidos no Application Insights). Para todos os votos adicionados e excluídos, agora você deve ver um "Evento personalizado* junto com a telemetria de resposta.

![eventos personalizados](./media/service-fabric-tutorial-monitoring-aspnet/custom-events.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:
> [!div class="checklist"]
> * Configurar o Application Insights para seu aplicativo
> * Coletar telemetria de resposta para rastrear comunicação baseada em HTTP entre serviços
> * Usar o recurso Mapa de aplicativos no Application Insights
> * Adicionar eventos personalizados usando a API do Application Insights

Agora que você concluiu a configuração do monitoramento e do diagnóstico do seu aplicativo ASP.NET, tente o seguinte:

* [Explorar mais o monitoramento e o diagnóstico no Service Fabric](service-fabric-diagnostics-overview.md)
* [Análise de eventos do Service Fabric com o Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* Para saber mais sobre o Application Insights, consulte [Documentação do Application Insights](https://docs.microsoft.com/azure/application-insights/)

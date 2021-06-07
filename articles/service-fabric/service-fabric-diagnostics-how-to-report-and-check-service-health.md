---
title: Relatar e verificar a integridade com o Azure Service Fabric
description: Saiba como enviar relatórios de integridade do seu código de serviço e como verificar a integridade do serviço usando as ferramentas de monitoramento de integridade fornecidas pelo Azure Service Fabric.
ms.topic: conceptual
ms.date: 02/25/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: dc15ea7f062d3912c297050ffac755990fdd3b7a
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625935"
---
# <a name="report-and-check-service-health"></a>Relatar e verificar a integridade de serviço
Quando seus serviços enfrentam problemas, sua capacidade de reagir e corrigir os incidentes e as interrupções depende da sua capacidade de detectar os problemas rapidamente. Se relatar problemas e falhas ao gerenciador de integridade do Azure Service Fabric usando seu código de serviço, você pode usar ferramentas padrão de monitoramento de integridade fornecidas pelo Service Fabric para verificar o status de integridade.

Há três maneiras de relatar a integridade no serviço:

* Usar os objetos [Partition](/dotnet/api/system.fabric.istatefulservicepartition) ou [CodePackageActivationContext](/dotnet/api/system.fabric.codepackageactivationcontext).  
  Você pode usar os objetos `Partition` e `CodePackageActivationContext` para relatar a integridade de elementos que fazem parte do contexto atual. Por exemplo, o código que é executado como parte de uma réplica pode relatar a integridade apenas dessa réplica, da partição a qual pertence e do aplicativo do qual faz parte.
* Use `FabricClient`.   
  Você poderá usar o `FabricClient` para relatar a integridade do código do serviço se o cluster não for [seguro](service-fabric-cluster-security.md) ou se o serviço estiver sendo executado com privilégios de administrador. A maioria dos cenários do mundo real não usa clusters inseguros ou fornece privilégios de administrador. Com o `FabricClient`, você poderá relatar a integridade de qualquer entidade que faz parte do cluster. No entanto, o ideal é que o código do serviço envie apenas relatórios relacionados à sua própria integridade.
* Use as APIs REST no cluster, no aplicativo, no aplicativo implantado, no serviço, no pacote de serviço, na partição, na réplica ou nos níveis de nó. Isso pode ser usado para relatar a integridade no interior de um contêiner.

Este artigo apresenta um exemplo que relata a integridade do código de serviço. O exemplo também mostra como as ferramentas fornecidas pelo Service Fabric podem ser usadas para verificar o status de integridade. Este artigo é uma rápida introdução aos recursos de monitoramento de integridade do Service Fabric. Para obter informações mais detalhadas, você pode ler a série de artigos explicativos sobre integridade, começando com o link no fim deste artigo.

## <a name="prerequisites"></a>Pré-requisitos
Você deve ter o seguinte instalado:

* Visual Studio 2015 ou Visual Studio 2019
* SDK da Malha do Serviço

## <a name="to-create-a-local-secure-dev-cluster"></a>Para criar um cluster de desenvolvimento local seguro
* Abra o PowerShell com privilégios de administrador e execute os comandos a seguir:

![Comandos que mostram como criar um cluster de desenvolvimento seguro](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## <a name="to-deploy-an-application-and-check-its-health"></a>Para implantar um aplicativo e verificar sua integridade
1. Abra o Visual Studio como administrador.
1. Crie um projeto usando o modelo **Serviço com Estado** .
   
    ![Criar um aplicativo do Service Fabric com serviço com estado](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)
1. Pressione **F5** para executar o aplicativo no modo de depuração. O aplicativo é implantado no cluster local.
1. Depois que o aplicativo estiver em execução, clique com o botão direito do mouse no ícone do Gerenciador de Cluster Local na área de notificação e selecione **Gerenciar Cluster Local** no menu de atalho para abrir o Service Fabric Explorer.
   
    ![Abra o Service Fabric Explorer na área de notificação](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)
1. A integridade do aplicativo deve ser exibida como nesta imagem. Neste momento, o aplicativo deve estar íntegro, sem erros.
   
    ![Aplicativo de integridade no Gerenciador do Service Fabric](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)
1. Você também pode verificar a integridade usando o PowerShell. Você pode usar ```Get-ServiceFabricApplicationHealth``` para verificar a integridade de um aplicativo e ```Get-ServiceFabricServiceHealth``` para verificar a integridade de um serviço. O relatório de integridade para o mesmo aplicativo no PowerShell está nesta imagem.
   
    ![Aplicativo de integridade no PowerShell](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## <a name="to-add-custom-health-events-to-your-service-code"></a>Para adicionar eventos de integridade personalizados ao seu código de serviço
Os modelos de projeto do Service Fabric no Visual Studio contêm código de exemplo. As etapas a seguir mostram como você pode relatar eventos de integridade personalizados do seu código de serviço. Esses relatórios serão exibidos automaticamente nas ferramentas padrão para monitoramento de integridade fornecidas pelo Service Fabric, como o Service Fabric Explorer, a exibição de integridade do Portal do Azure e o PowerShell.

1. Abra novamente o aplicativo que você criou anteriormente no Visual Studio ou crie um novo usando o modelo de **Serviço com Estado** do Visual Studio.
1. Abra o arquivo Stateful1.cs e encontre a chamada `myDictionary.TryGetValueAsync` no método `RunAsync`. Você pode ver que esse método retorna um `result` que mantém o valor atual do contador, pois a lógica principal desse aplicativo é manter uma contagem em execução. Se esse aplicativo era um aplicativo real e, se a falta de resultados representasse uma falha, você desejaria sinalizar esse evento.
1. Para relatar um evento de integridade quando a falta de resultado representa uma falha, adicione as etapas a seguir.
   
    a. Adicione o namespace `System.Fabric.Health` ao arquivo Stateful1.cs.
   
    ```csharp
    using System.Fabric.Health;
    ```
   
    b. Adicione o seguinte código após a chamada `myDictionary.TryGetValueAsync`
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
    Relatamos a integridade da réplica porque ela está sendo relatada de um serviço com estado. O parâmetro `HealthInformation` armazena informações sobre o problema de integridade que está sendo relatado.
   
    Se você tiver criado um serviço sem estado, use o código a seguir
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```
1. Se o serviço estiver sendo executado com privilégios de administrador ou se o cluster não for [seguro](service-fabric-cluster-security.md), também será possível usar `FabricClient` para relatar a integridade conforme mostrado nas etapas a seguir.  
   
    a. Crie a instância do `var myDictionary` após a declaração `FabricClient`.
   
    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```
   
    b. Adicione o seguinte código após a chamada `myDictionary.TryGetValueAsync` :
   
    ```csharp
    if (!result.HasValue)
    {
       var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.Context.PartitionId,
            this.Context.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```
1. Vamos simular essa falha e ver como ela é exibida nas ferramentas de monitoramento de integridade. Para simular a falha, comente a primeira linha no código de relatório de integridade adicionado anteriormente. Depois de comentar a primeira linha, o código se parecerá com este exemplo:
   
    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
   Esse código aciona o relatório de integridade sempre que o `RunAsync` é executado. Depois de fazer a alteração, pressione **F5** para executar o aplicativo.
1. Depois que o aplicativo estiver em execução, abra o Service Fabric Explorer para verificar a integridade do aplicativo. Desta vez, o Service Fabric Explorer mostra que o aplicativo não está íntegro. O aplicativo é exibido como não íntegro porque o erro relatado a partir do código que adicionamos anteriormente.
   
    ![Aplicativo não íntegro no Gerenciador do Service Fabric](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)
1. Se você selecionar a réplica primária na exibição de árvore do Service Fabric Explorer, verá que **Estado de Integridade** indica um erro também. O Service Fabric Explorer também exibe os detalhes do relatório de integridade que foram adicionados ao parâmetro `HealthInformation` no código. Você pode ver os mesmos relatórios de integridade no PowerShell e no Portal do Azure.
   
    ![Integridade da réplica no Gerenciador do Service Fabric](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Esse relatório permanece no gerenciador de integridade até que seja substituído por outro relatório ou até que essa réplica seja excluída. Como nós não definimos `TimeToLive` para este relatório de integridade no objeto `HealthInformation`, o relatório nunca expira.

Recomendamos que a integridade seja relatada no nível mais granular. Neste caso, é a réplica. Você também pode relatar a integridade em `Partition`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

Para relatar a integridade em `Application`, `DeployedApplication` e `DeployedServicePackage`, use `CodePackageActivationContext`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## <a name="next-steps"></a>Próximas etapas
* [Aprofunde-se na integridade do Service Fabric](service-fabric-health-introduction.md)
* [API REST para relatar a integridade do serviço](/rest/api/servicefabric/report-the-health-of-a-service)
* [API REST para relatar a integridade do aplicativo](/rest/api/servicefabric/report-the-health-of-an-application)

---
title: "Agregação de Eventos do Azure Service Fabric com Diagnóstico do Windows Azure | Microsoft Docs"
description: "Saiba mais sobre a agregação e coleta de eventos utilizando o WAD para monitoramento e diagnóstico de clusters do Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/17/2017
ms.author: dekapur
ms.openlocfilehash: 5773361fdec4cb8ee54fa2856f6aa969d5dac4e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="event-aggregation-and-collection-using-windows-azure-diagnostics"></a>Coleta e agregação de eventos utilizando o Diagnóstico do Windows Azure
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Quando você estiver executando um cluster de Service Fabric do Azure, é uma boa ideia coletar os logs de todos os nós em um local central. Ter os logs em um local central ajuda a analisar e solucionar problemas no cluster ou nos aplicativos e serviços em execução nesse cluster.

Uma maneira de fazer upload e coletar logs é utilizar a extensão WAD (Diagnóstico do Windows Azure) que faz upload dos logs no Armazenamento do Azure e, além disso, possui a opção de enviar os logs para o Azure Application Insights ou Hubs de Evento. Também é possível utilizar um processo externo para ler os eventos do armazenamento e colocá-los em um produto da plataforma de análise, como [OMS Log Analytics](../log-analytics/log-analytics-service-fabric.md) ou outra solução de análise de logs.

## <a name="prerequisites"></a>Pré-requisitos
Essas ferramentas são usadas para executar algumas das operações neste documento:

* [Diagnóstico do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) (relacionado aos Serviços de Nuvem do Azure, mas tem boas informações e exemplos)
* [Gerenciador de Recursos do Azure](../azure-resource-manager/resource-group-overview.md)
* [PowerShell do Azure](/powershell/azure/overview)
* [Cliente do Azure Resource Manager](https://github.com/projectkudu/ARMClient)
* [Modelo do Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="log-and-event-sources"></a>Origem do evento e log

### <a name="service-fabric-platform-events"></a>Eventos de plataforma do Service Fabric
Conforme discutido [neste artigo](service-fabric-diagnostics-event-generation-infra.md), o Service Fabric estabelece alguns canais de registro prontos, dos quais os seguintes canais facilmente são configurados com o WAD para enviar dados de monitoramento e diagnóstico para uma tabela de armazenamento ou outro lugar:
  * Eventos operacionais: operações de nível superior que a plataforma do Service Fabric executa. Os exemplos incluem criação de aplicativos e serviços, alterações de estado do nó e informações de atualização. Eles são emitidos como eventos de Rastreamento de Eventos para Windows (ETW)
  * [Eventos do modelo de programação Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
  * [Eventos do modelo de programação Reliable Services](service-fabric-reliable-services-diagnostics.md)

### <a name="application-events"></a>Eventos de aplicativo
 Os eventos emitidos a partir do código de serviços e aplicativos, e gravados usando a classe auxiliar EventSource fornecida nos modelos do Visual Studio. Para obter mais informações sobre como gravar logs EventSource do seu aplicativo, consulte [Monitorar e diagnosticar serviços em uma configuração de desenvolvimento do computador local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).

## <a name="deploy-the-diagnostics-extension"></a>Implantar a extensão de Diagnóstico
A primeira etapa para coletar logs será implantar a extensão Diagnóstico em cada uma das VMs no cluster do Service Fabric. A extensão de Diagnóstico coleta logs em cada VM e os carrega para a conta de armazenamento especificada. As etapas variam um pouco com base em seu uso do Portal do Azure ou do Azure Resource Manager. As etapas também variam com base em se a implantação faz parte da criação do cluster ou é para um cluster que já existe. Vejamos as etapas para cada cenário.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-azure-portal"></a>Implantar a extensão de Diagnóstico como parte da criação de cluster por meio do portal do Azure
Para implantar a extensão de Diagnóstico nas VMs do cluster como parte da criação do cluster, você usa o painel de configurações de Diagnóstico mostrado na imagem a seguir - certifique-se de que esse Diagnóstico foi definido como **Habilitado** (a configuração padrão). Depois de criar o cluster, você não poderá alterar essas configurações por meio do portal.

![Configuração de Diagnóstico do Azure no portal para a criação do cluster](media/service-fabric-diagnostics-event-aggregation-wad/azure-enable-diagnostics.png)

Ao criar um cluster usando o portal, é altamente recomendável que você baixe o modelo **antes de clicar em OK** para criar o cluster. Para obter detalhes, confira [Setup a Service Fabric cluster by using an Azure Resource Manager template](service-fabric-cluster-creation-via-arm.md) (Configurar um cluster do Service Fabric usando um modelo do Azure Resource Manager). Você precisará do modelo para fazer alterações posteriormente, porque não é possível fazer algumas alterações usando o portal.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-by-using-azure-resource-manager"></a>Implantar a extensão de diagnóstico como parte da criação de cluster usando o Gerenciador de Recursos do Azure
Para criar um cluster usando o Resource Manager, você precisa adicionar a configuração de Diagnóstico JSON para o modelo do Resource Manager completo do cluster antes de criar o cluster. Fornecemos um exemplo de modelo de Gerenciador de Recursos de cluster de cinco VMs com configuração de Diagnóstico adicionada a ele como parte dos exemplos do modelo de Gerenciador de Recursos. Você pode vê-lo nesse local na Galeria de exemplos do Azure: [cluster cinco nós com exemplo de modelo do Gerenciador de Recursos de Diagnóstico](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad).

Para ver a configuração do Diagnóstico no modelo do Resource Manager, abra o arquivo azuredeploy.json e pesquise **IaaSDiagnostics**. Para criar um cluster usando este modelo, selecione o botão **Implantar no Azure** disponível no link anterior.

Como alternativa você pode baixar o exemplo de Gerenciador de Recursos, fazer suas alterações e criar um cluster com o modelo modificado usando o comando `New-AzureRmResourceGroupDeployment` em uma janela do Azure PowerShell. Consulte no código a seguir para ver os parâmetros que você passa para o comando. Para obter informações detalhadas sobre como implantar um grupo de recursos usando o PowerShell, consulte o artigo [Deploy a resource group with the Azure Resource Manager template](../azure-resource-manager/resource-group-template-deploy.md) (Implantar o grupo de recursos com o modelo do Azure Resource Manager).

### <a name="deploy-the-diagnostics-extension-to-an-existing-cluster"></a>Implantar a extensão de diagnóstico em um cluster existente
Se você tiver um cluster existente que não tenha o Diagnóstico implantado ou se você quiser modificar uma configuração existente, você poderá adicioná-lo ou atualizá-lo. Modifique o modelo do Resource Manager usado para criar o cluster existente ou baixe o modelo do portal, conforme descrito anteriormente. Modifique o arquivo template.json executando as seguintes tarefas.

Adicione um novo recurso de armazenamento ao modelo, adicionando à seção de recursos.

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "properties": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 Em seguida, adicione à seção de parâmetros logo após as definições da conta de armazenamento, entre `supportLogStorageAccountName` e `vmNodeType0Name`. Substitua o texto do espaço reservado *nome da conta de armazenamento aqui* pelo nome da conta de armazenamento.

```json
    "applicationDiagnosticsStorageAccountType": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "Replication option for the application diagnostics storage account"
      }
    },
    "applicationDiagnosticsStorageAccountName": {
      "type": "string",
      "defaultValue": "storage account name goes here",
      "metadata": {
        "description": "Name for the storage account that contains application diagnostics data from the cluster"
      }
    },
```
Em seguida, atualize a seção `VirtualMachineProfile` do arquivo template.json adicionando o conteúdo a seguir dentro da matriz extensions. Adicione uma vírgula no início ou no fim, dependendo de onde será inserido.

```json
{
    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
    "properties": {
        "type": "IaaSDiagnostics",
        "autoUpgradeMinorVersion": true,
        "protectedSettings": {
        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
        "storageAccountEndPoint": "https://core.windows.net/"
        },
        "publisher": "Microsoft.Azure.Diagnostics",
        "settings": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": "50000",
            "EtwProviders": {
                "EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
                {
                    "provider": "Microsoft-ServiceFabric-Services",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                    }
                }
                ],
                "EtwManifestProviderConfiguration": [
                {
                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                    "scheduledTransferLogLevelFilter": "Information",
                    "scheduledTransferKeywordFilter": "4611686018427387904",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricSystemEventTable"
                    }
                }
                ]
            }
            }
        },
        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
        },
        "typeHandlerVersion": "1.5"
    }
}
```

Após modificar o arquivo template.json conforme descrito, republique o modelo do Resource Manager. Se o modelo tiver sido exportado, a execução do arquivo deploy.ps1 republicará o modelo. Após implantar, verifique se o status de **ProvisioningState** é **Com êxito**.

## <a name="collect-health-and-load-events"></a>Coletar eventos de integridade e de carga

Começando com a versão 5.4 do Service Fabric, eventos de métrica de carga e integridade estão disponíveis para coleta. Esses eventos refletir os eventos gerados pelo sistema ou seu código usando a integridade ou APIs de relatórios de carga como [ReportPartitionHealth](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportpartitionhealth.aspx) ou [ReportLoad](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportload.aspx). Isso permite agregar e exibir a integridade do sistema ao longo do tempo e para alertas com base em eventos de integridade ou de carga. Para exibir esses eventos no Visualizador de Eventos de Diagnóstico do Visual Studio, adicione "Microsoft-ServiceFabric:4:0x4000000000000008" à lista de provedores de ETW.

Para coletar os eventos, modifique o modelo do Resource Manager para incluir

```json
  "EtwManifestProviderConfiguration": [
    {
      "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
      "scheduledTransferLogLevelFilter": "Information",
      "scheduledTransferKeywordFilter": "4611686018427387912",
      "scheduledTransferPeriod": "PT5M",
      "DefaultEvents": {
        "eventDestination": "ServiceFabricSystemEventTable"
      }
    }
```

## <a name="collect-reverse-proxy-events"></a>Coletar eventos de proxy reverso

A partir da versão 5.7 do Service Fabric, os eventos de [proxy reverso](service-fabric-reverseproxy.md) estão disponíveis para coleta.
O proxy reverso emite eventos em dois canais, um que contém eventos de erro refletindo falhas e o outro que contém eventos detalhados sobre todas as solicitações de processamento de solicitações processados no proxy reverso. 

1. Coletar eventos de erro: para exibir esses eventos no Visualizador de Eventos de Diagnóstico do Visual Studio, adicione "Microsoft-ServiceFabric:4:0x4000000000000010" à lista de provedores de ETW.
Para coletar os eventos de clusters do Azure, modifique o modelo do Resource Manager para incluir

```json
  "EtwManifestProviderConfiguration": [
    {
      "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
      "scheduledTransferLogLevelFilter": "Information",
      "scheduledTransferKeywordFilter": "4611686018427387920",
      "scheduledTransferPeriod": "PT5M",
      "DefaultEvents": {
        "eventDestination": "ServiceFabricSystemEventTable"
      }
    }
```

2. Coletar todos os eventos de processamento de solicitação: no Visualizador de Eventos de Diagnóstico no Visual Studio, atualize a entrada Microsoft-ServiceFabric na lista de provedores do ETW para "Microsoft-ServiceFabric:4:0x4000000000000020".
Para clusters do Azure Service Fabric, modifique o modelo do resource manager para incluir

```json
  "EtwManifestProviderConfiguration": [
    {
      "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
      "scheduledTransferLogLevelFilter": "Information",
      "scheduledTransferKeywordFilter": "4611686018427387936",
      "scheduledTransferPeriod": "PT5M",
      "DefaultEvents": {
        "eventDestination": "ServiceFabricSystemEventTable"
      }
    }
```
> É recomendável habilitar criteriosamente a coleta de eventos deste canal pois isso coleta todo o tráfego por meio do proxy reverso e pode consumir rapidamente a capacidade de armazenamento.

Para os clusters de Azure Service Fabric, os eventos de todos os nós são coletados e agregados em SystemEventTable.
Para obter a solução de problemas dos eventos de proxy reverso, veja o [guia de diagnóstico de proxy reverso](service-fabric-reverse-proxy-diagnostics.md).

## <a name="collect-from-new-eventsource-channels"></a>Coletar a partir de novos canais EventSource

Para atualizar o Diagnóstico para coletar logs a partir de novos canais EventSource que representam um novo aplicativo que você está prestes a implantar, execute as mesmas etapas anteriores para configurar o Diagnóstico para um cluster existente.

Atualize a seção `EtwEventSourceProviderConfiguration` no arquivo template.json para adicionar entradas para novos canais de EventSource antes de aplicar a atualização de configuração usando o comando `New-AzureRmResourceGroupDeployment` do PowerShell. O nome da origem do evento é definido como parte do seu código no arquivo Visual Studio-generated ServiceEventSource.cs.

Por exemplo, se a origem do evento for denominada My-Eventsource, adicione o seguinte código para colocar os eventos de My-Eventsource em uma tabela chamada MyDestinationTableName.

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

Para coletar contadores de desempenho ou logs de eventos, modifique o modelo do Resource Manager usando os exemplos fornecidos em [Criar uma máquina virtual do Windows com monitoramento e diagnóstico usando um modelo do Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Em seguira, republique o modelo do Resource Manager.

## <a name="collect-performance-counters"></a>Coletar contadores de desempenho

Para coletar métricas de desempenho do seu cluster, adicione os contadores de desempenho para o "WadCfg > DiagnosticMonitorConfiguration" no modelo do Resource Manager para o cluster. Consulte [Contadores de desempenho do Service Fabric](service-fabric-diagnostics-event-generation-perf.md) para contadores de desempenho que recomendamos para coleta.

Por exemplo, aqui vamos definir um contador de desempenho, amostras a cada 15 segundos (isso pode ser alterado e segue o formato de "PT\<time>\<unit>", por exemplo, o PT3M seria a amostra em intervalos de três minutos) e transferências para a tabela de armazenamento apropriada a cada um minuto.

  ```json
  "PerformanceCounters": {
      "scheduledTransferPeriod": "PT1M",
      "PerformanceCounterConfiguration": [
          {
              "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
              "sampleRate": "PT15S",
              "unit": "Percent",
              "annotation": [
              ],
              "sinks": ""
          }
      ]
  }
  ```
  
Se você estiver usando um coletor do Application Insights, conforme descrito na seção abaixo e deseja que essas métricas sejam exibidas no Application Insights, certifique-se de adicionar o nome do coletor na seção "coletores", como mostrado acima. Além disso, considere a criação de uma tabela separada para enviar seus Contadores de desempenho, para que eles não fiquem cheios de dados provenientes de outros canais de log que você habilitou.


## <a name="send-logs-to-application-insights"></a>Enviar logs para o Application Insights

Enviar dados de monitoramento e diagnóstico para o Application Insights (AI) pode ser feito como parte da configuração do WAD. Se você decidir usar o AI para visualização e análise de eventos, leia [Visualização e análise de eventos com o Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) para configurar um coletor do AI como parte do "WadCfg".

## <a name="next-steps"></a>Próximas etapas

Depois de configurar corretamente o diagnóstico do Azure, você verá os dados nas Tabelas de armazenamento dos logs do ETW e EventSource. Se você optar por usar o OMS, Kibana ou qualquer outra plataforma de análise e visualização de dados que não foi configurada diretamente no modelo do Resource Manager , certifique-se de configurar a plataforma de sua escolha para ler os dados dessas tabelas de armazenamento. Fazer isso para OMS é relativamente simples e é explicado em [Análise de eventos e logs no OMS](service-fabric-diagnostics-event-analysis-oms.md). O Application Insights é um pouco diferente nesse sentido, pois ele pode ser configurado como parte da configuração da extensão de diagnóstico, então consulte o [artigo apropriado](service-fabric-diagnostics-event-analysis-appinsights.md) se optar por usar o AI.

>[!NOTE]
>Atualmente, não há nenhuma maneira de filtrar ou limpar os eventos que são enviados para a tabela. Se você não implantar um processo para remover eventos da tabela, a tabela continuará crescendo. Atualmente, há um exemplo de um serviço de limpeza de dados em execução no [exemplo Watchdog](https://github.com/Azure-Samples/service-fabric-watchdog-service), e é recomendável que você grave um para você mesmo, a menos que haja uma boa razão para armazenar logs além de um período de 30 ou 90 dias.

* [Aprenda a coletar contadores de desempenho ou logs usando a extensão de Diagnóstico](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Visualização e Análise de Eventos com o Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Visualização e Análise de Eventos com o OMS](service-fabric-diagnostics-event-analysis-oms.md)
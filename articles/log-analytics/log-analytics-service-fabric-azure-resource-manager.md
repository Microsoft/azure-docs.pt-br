---
title: Avaliar aplicativos do Service Fabric com o Log Analytics usando o Portal do Azure | Microsoft Docs
description: "Você pode usar a solução do Service Fabric no Log Analytics usando o Portal do Azure para avaliar o risco e a integridade dos aplicativos do Service Fabric, microsserviços, nós e clusters."
services: log-analytics
documentationcenter: 
author: niniikhena
manager: jochan
editor: 
ms.assetid: 9c91aacb-c48e-466c-b792-261f25940c0c
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: nini
ms.openlocfilehash: 8c564c0dcbb2f9be286917b2f4d8a40da5406fae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="assess-service-fabric-applications-and-micro-services-with-the-azure-portal"></a>Avaliar aplicativos do Service Fabric e microsserviços com o Portal do Azure

> [!div class="op_single_selector"]
> * [Gerenciador de Recursos](log-analytics-service-fabric-azure-resource-manager.md)
> * [PowerShell](log-analytics-service-fabric.md)
>
>

![Símbolo do Service Fabric](./media/log-analytics-service-fabric/service-fabric-assessment-symbol.png)

Este artigo descreve como usar a solução de Service Fabric no Log Analytics para ajudar a identificar e solucionar problemas em seu cluster do Service Fabric.

A solução de Service Fabric usa dados de Diagnóstico do Azure das suas VMs do Service Fabric, coletando esses dados de suas tabelas do Azure WAD. O Log Analytics, em seguida, lê eventos da estrutura do Service Fabric, incluindo **Eventos de Serviço Confiável**, **Eventos de Ator**, **Eventos Operacionais** e **Eventos de ETW Personalizados**. Com o painel de solução, é possível exibir problemas importantes e eventos relevantes no seu ambiente do Service Fabric.

Para começar a usar a solução, você precisa conectar seu cluster do Service Fabric a um espaço de trabalho do Log Analytics. Há três cenários a serem considerados:

1. Se você não tiver implantado o cluster do Service Fabric, use as etapas em ***Deploy a Service Fabric Cluster connected to a Log Analytics workspace*** (Implantar um cluster do Service Fabric conectado a um espaço de trabalho do Log Analytics) para implantar um novo cluster e configurá-lo para relatar para o Log Analytics.
2. Se você precisar coletar contadores de desempenho de seus hosts para usar outras soluções do OMS, como Segurança no Cluster do Service Fabric, siga as etapas em ***Implantar um Cluster do Service Fabric conectado a um espaço de trabalho do Log Analytics com a Extensão de VM instalada.***
3. Se você já tiver implantado o cluster do Service Fabric e quiser conectá-lo ao Log Analytics, siga as etapas em ***Adding an existing storage account to Log Analytics*** (Adicionar uma conta de armazenamento existente para Log Analytics).

## <a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace"></a>Implantar um Cluster do Service Fabric conectado a um espaço de trabalho do Log Analytics.
O modelo faz o seguinte:

1. implanta um cluster do Azure Service Fabric conectado a um espaço de trabalho do Log Analytics. Você tem a opção de criar um novo espaço de trabalho durante a implantação do modelo ou inserir o nome de um espaço de trabalho do Log Analytics já existente.
2. adiciona a conta de armazenamento do diagnóstico ao espaço de trabalho do Log Analytics.
3. habilita a solução do Service Fabric em seu espaço de trabalho do Log Analytics.

[![Implantar no Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-oms%2F%2Fazuredeploy.json)

Depois de selecionar o botão de implantação acima, o portal do Azure será aberto com os parâmetros para edição. Lembre-se de criar um novo grupo de recursos se você inserir um novo nome de espaço de trabalho do Log Analytics:

![Service Fabric](./media/log-analytics-service-fabric/2.png)

![Service Fabric](./media/log-analytics-service-fabric/3.png)

Aceite os termos legais e clique em **Criar** para iniciar a implantação. Após a conclusão da implantação, você verá o novo espaço de trabalho e o cluster criado e as tabelas WADServiceFabric*Event, WADWindowsEventLogs e WADETWEvent são adicionadas:

![Service Fabric](./media/log-analytics-service-fabric/4.png)

## <a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace-with-vm-extension-installed"></a>Implante um Cluster do Service Fabric conectado a um espaço de trabalho do Log Analytics com a Extensão de VM instalada.

O modelo faz o seguinte:

1. implanta um cluster do Azure Service Fabric conectado a um espaço de trabalho do Log Analytics. Você pode criar um novo espaço de trabalho ou usar um existente.
2. adiciona as contas de armazenamento do diagnóstico ao espaço de trabalho do Log Analytics.
3. habilita a solução do Service Fabric no espaço de trabalho do Log Analytics.
4. Instala a extensão do agente de MMA em cada conjunto de dimensionamento de máquinas virtuais no cluster do Service Fabric. Com o agente de MMA instalado, é possível exibir métricas de desempenho sobre os nós.

[![Implantar no Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-vmss-oms%2F%2Fazuredeploy.json)

Seguindo as mesmas etapas acima, insira os parâmetros necessários e inicie uma implantação. Mais uma vez, você verá o novo espaço de trabalho, o cluster e tabelas de WAD criados:

![Service Fabric](./media/log-analytics-service-fabric/5.png)

### <a name="viewing-performance-data"></a>Exibindo dados de desempenho

Para exibir dados de desempenho de seus nós:


[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

- inicie o espaço de trabalho de Log Analytics no portal do Azure.
  ![Service Fabric](./media/log-analytics-service-fabric/6.png)
- Vá para Configurações no painel esquerdo e selecione Dados >> Contadores de Desempenho do Windows >> "Adicionar os contadores de desempenho selecionados": ![Service Fabric](./media/log-analytics-service-fabric/7.png)
- Na Pesquisa de Log, use as seguintes consultas se aprofundar nas principais métricas sobre seus nós:

    a. Compare a média de Utilização da CPU em todos os nós na última hora para ver os nós que estão tendo problemas e o intervalo em que um nó teve um pico:

    ```
    Type=Perf ObjectName=Processor CounterName="% Processor Time"|measure avg(CounterValue) by Computer Interval 1HOUR.
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/10.png)

    b. Exibir gráficos de linhas semelhantes para a memória disponível em cada nó com esta consulta:

    ```
    Type=Perf ObjectName=Memory CounterName="Available MBytes Memory" | measure avg(CounterValue) by Computer Interval 1HOUR.
    ```

    Para exibir uma lista de todos os nós, mostrando o valor médio exato de megabytes disponíveis para cada nó, use esta consulta:

    ```
    Type=Perf (ObjectName=Memory) (CounterName="Available MBytes") | measure avg(CounterValue) by Computer
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/11.png)

    c. Caso queira fazer uma busca detalhada em um nó específico, examinando a média de uso da CPU por hora, mínimo, máximo e do percentil 75, você pode fazer isso usando esta consulta (substitua o campo Computador):

    ```
    Type=Perf CounterName="% Processor Time" InstanceName=_Total Computer="BaconDC01.BaconLand.com"| measure min(CounterValue), avg(CounterValue), percentile75(CounterValue), max(CounterValue) by Computer Interval 1HOUR
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/12.png)

Para obter mais informações sobre as métricas de desempenho na análise de Log no [Operations Management Suite blog](https://blogs.technet.microsoft.com/msoms/tag/metrics/).


## <a name="adding-an-existing-storage-account-to-log-analytics"></a>Adicionar uma conta de armazenamento existente ao Log Analytics

Este modelo simplesmente adiciona suas contas de armazenamento existentes a um espaço de trabalho novo ou existente do Log Analytics.

[![Implantar no Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Foms-existing-storage-account%2Fazuredeploy.json)

> [!NOTE]
> Ao selecionar um Grupo de Recursos, se você estiver trabalhando com um espaço de trabalho já existente do Log Analytics, selecione “Usar Existente” e pesquise o grupo de recursos que contém o espaço de trabalho do Log Analytics. Caso contrário, crie um novo.
> ![Service Fabric](./media/log-analytics-service-fabric/8.png)
>
>

Após esse modelo ter sido implantado, você poderá ver a conta de armazenamento conectada ao seu espaço de trabalho do Log Analytics. Neste exemplo, eu adicionei mais uma conta de armazenamento ao espaço de trabalho do Exchange que criei acima.
![Service Fabric](./media/log-analytics-service-fabric/9.png)

## <a name="view-service-fabric-events"></a>Exibir eventos do Service Fabric

Depois que as implantações estiverem concluídas e a solução do Service Fabric tiver sido habilitada no seu espaço de trabalho, selecione o bloco **Service Fabric** no portal do Log Analytics para iniciar o painel do Service Fabric. O painel inclui as colunas na tabela a seguir. Cada coluna lista os 10 principais eventos por contagem que correspondem aos critérios da coluna para o intervalo de tempo especificado. É possível executar uma pesquisa de log que fornece a lista inteira clicando em **Ver todos** no canto inferior direito de cada coluna ou clicando no cabeçalho da coluna.

| **Evento do Service Fabric** | **description** |
| --- | --- |
| Problemas importantes |Uma exibição de problemas como RunAsyncFailures RunAsynCancellations e nós com falha. |
| Eventos operacionais |Eventos operacionais importantes, como atualização de aplicativos e implantações. |
| Eventos de serviço confiável |Eventos importantes de serviços confiáveis como Runasyncinvocations. |
| Eventos de ator |Eventos de ator importantes gerados pelos seus microsserviços, como exceções lançadas por um método de ator, ativações e desativações de ator e assim por diante. |
| Eventos de aplicativo |Todos os eventos de ETW personalizados gerados por seus aplicativos. |

![Painel do Service Fabric](./media/log-analytics-service-fabric/sf3.png)

![Painel do Service Fabric](./media/log-analytics-service-fabric/sf4.png)

A tabela a seguir mostra os métodos de coleta de dados e outros detalhes sobre como os dados são coletados para o Service Fabric.

| plataforma | Agente direto | Agente do Operations Manager | Armazenamento do Azure | Operations Manager necessário? | Dados de agente do Operations Manager enviados por meio do grupo de gerenciamento | frequência de coleta |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |  |  | &#8226; |  |  |10 minutos |

> [!NOTE]
> Você pode alterar o escopo desses eventos na solução de Service Fabric clicando em **Dados baseados nos últimos 7 dias** na parte superior do painel. Você também pode mostrar os eventos gerados nos últimos sete dias, no último dia ou nas últimas seis horas. Ou você pode selecionar **Personalizado** e especificar um intervalo de datas personalizado.
>
>

## <a name="next-steps"></a>Próximas etapas

* Use [Pesquisas de log no Log Analytics](log-analytics-log-searches.md) para exibir dados detalhados dos eventos do Service Fabric.

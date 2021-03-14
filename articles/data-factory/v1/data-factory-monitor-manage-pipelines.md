---
title: Monitorar e gerenciar pipelines usando o portal do Azure e o PowerShell
description: Saiba como usar o Portal do Azure e o Azure PowerShell para monitorar e gerenciar as data factories e os pipelines do Azure que você criou.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: c728654e868bcb8213e6a4039fa1e2e169b0078c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100576386"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-azure-portal-and-powershell"></a>Monitorar e gerenciar os pipelines do Azure Data Factory usando o Portal do Azure e o PowerShell
> [!div class="op_single_selector"]
> * [Usando o Portal do Azure/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Usando o aplicativo de Monitoramento e Gerenciamento](data-factory-monitor-manage-app.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [Monitorar e gerenciar pipelines do Data Factory em](../monitor-visually.md).

Este artigo descreve como monitorar, gerenciar e depurar seus pipelines usando o portal do Azure e o PowerShell.

> [!IMPORTANT]
> O aplicativo de monitoramento e gerenciamento fornece um melhor suporte para monitorar e gerenciar seus pipelines de dados e solucionar os problemas. Para obter detalhes sobre como usar o aplicativo, consulte [Monitorar e gerenciar os pipelines do Data Factory usando o aplicativo de Monitoramento e Gerenciamento](data-factory-monitor-manage-app.md). 

> [!IMPORTANT]
> O Azure Data Factory versão 1 agora usa a nova [infraestrutura de alerta do Azure Monitor](../../azure-monitor/alerts/alerts-metric.md). A infraestrutura de alerta antiga foi preterida. Como resultado, os alertas existentes configurados para data factories versão 1 deixará de funcionar. Os alertas existentes para data factories v1 não são migrados automaticamente. Você precisa recriar esses alertas na nova infraestrutura de alerta. Faça logon no portal do Azure e selecione **Monitor** para criar novos alertas em métricas (como execuções com falha ou execuções com êxito) para a data factories versão 1.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="understand-pipelines-and-activity-states"></a>Entenda os pipelines e os estados de atividade
No Portal do Azure, você pode:

* Exibir sua data factory como um diagrama.
* Exibir atividades dentro de um pipeline.
* Exibir conjuntos de dados de entrada e saída.

Esta seção também descreve como uma fatia do conjunto de dados faz a transição de um estado para outro.   

### <a name="navigate-to-your-data-factory"></a>Navegue até sua data factory
1. Entre no [portal do Azure](https://portal.azure.com).
2. Clique em **Data factories** no menu à esquerda. Se você não o vir, clique em **Mais serviços >** e clique em **Data factories** na categoria **INTELIGÊNCIA + ANÁLISE**.

   ![Procurar tudo > Data factories](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)
3. Na folha **Data factories**, selecione o data factory no qual você está interessado.

    ![Selecionar o data factory](./media/data-factory-monitor-manage-pipelines/select-data-factory.png)

   Você deverá ver a home page do data factory.

   ![Folha Data factory](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### <a name="diagram-view-of-your-data-factory"></a>Modo de exibição de diagrama de uma data factory
A exibição de **diagrama** de uma data Factory fornece um único painel de vidro para monitorar e gerenciar o data Factory e seus ativos. Para ver o modo de exibição de **Diagrama** de seu data factory, clique em **Diagrama** na home page do data factory.

![Modo de Exibição de Diagrama](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

Você pode ampliar, reduzir, ajustar o nível de zoom, aplicar zoom para 100%, bloquear o layout do diagrama e posicionar pipelines e conjuntos de dados automaticamente. Você também pode ver as informações de linhagem de dados (ou seja, mostrar itens upstream e downstream dos itens selecionados).

### <a name="activities-inside-a-pipeline"></a>Atividades dentro de um pipeline
1. Clique com o botão direito do mouse no pipeline e depois clique em **Abrir pipeline** para ver todas as atividades no pipeline junto com conjuntos de dados de entrada e saída para as atividades. Esse recurso é útil quando o pipeline é composto por mais de uma atividade, e você deseja compreender a linhagem operacional de um único pipeline.

    ![Menu do pipeline aberto](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)     
2. No exemplo a seguir, você vê uma atividade de cópia no pipeline com uma entrada e uma saída. 

    ![Atividades dentro de um pipeline](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png)
3. Você pode voltar à home page do data factory clicando no link **Data factory** na trilha no canto superior esquerdo.

    ![Navegue de volta para a data factory](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### <a name="view-the-state-of-each-activity-inside-a-pipeline"></a>Exibir o estado de cada atividade dentro de um pipeline
Veja o estado atual de uma atividade exibindo o status de qualquer um dos conjuntos de dados produzidos pela atividade.

Clicar duas vezes em **OutputBlobTable** no **Diagrama** exibirá todas as fatias produzidas por diferentes execuções de atividade dentro de um pipeline. Você pode ver que a atividade de cópia foi executada com êxito pelas últimas oito horas e produziu as fatias com estado **Pronto**.  

![Estado do pipeline](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

As fatias do conjunto de dados no data factory podem ter um dos seguintes status:

<table>
<tr>
    <th align="left">Estado</th><th align="left">Subestado</th><th align="left">Descrição</th>
</tr>
<tr>
    <td rowspan="8">Aguardando</td><td>ScheduleTime</td><td>Não chegou o momento de execução da fatia.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>As dependências de upstream não estão prontas.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Os recursos de computação não estão disponíveis.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Todas as instâncias de atividade estão ocupadas executando outras fatias.</td>
</tr>
<tr>
<td>ActivityResume</td><td>A atividade está em pausa e não pode executar as fatias até que a atividades seja retomada.</td>
</tr>
<tr>
<td>Repetir</td><td>A execução da atividade está sendo repetida.</td>
</tr>
<tr>
<td>Validação</td><td>A validação ainda não foi iniciada.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>A validação está aguardando para ser repetida.</td>
</tr>
<tr>
<tr>
<td rowspan="2">InProgress</td><td>Validando</td><td>Validação em andamento.</td>
</tr>
<td>-</td>
<td>A fatia está sendo processada.</td>
</tr>
<tr>
<td rowspan="4">Failed (Falha)</td><td>TimedOut</td><td>A execução demorou mais do que o permitido pela atividade.</td>
</tr>
<tr>
<td>Canceled</td><td>A fatia foi cancelada por ação do usuário.</td>
</tr>
<tr>
<td>Validação</td><td>A validação falhou.</td>
</tr>
<tr>
<td>-</td><td>Não foi possível gerar e/ou validar a fatia.</td>
</tr>
<td>Ready</td><td>-</td><td>A fatia está pronta para consumo.</td>
</tr>
<tr>
<td>Ignorado</td><td>Nenhum</td><td>A fatia não está sendo processada.</td>
</tr>
<tr>
<td>Nenhum</td><td>-</td><td>Uma fatia costumava existir com um status diferente, mas foi redefinida.</td>
</tr>
</table>



Veja os detalhes de uma fatia clicando em uma entrada de fatia na folha **Fatias Atualizadas Recentemente** .

![Detalhes da fatia](./media/data-factory-monitor-manage-pipelines/slice-details.png)

Se a fatia tiver sido executada várias vezes, você verá várias linhas na lista **Execuções de atividade** . Você pode exibir detalhes sobre uma execução de atividade clicando na entrada da execução na lista **Execuções de atividade** . A lista mostra todos os arquivos de log, junto com uma mensagem de erro, se houver. Esse recurso é útil para exibir e depurar logs sem precisar sair de sua data factory.

![Detalhes da execução da atividade](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Quando a fatia não está no estado **Pronto**, você pode ver as fatias upstream que não estão prontas e estão impedindo a execução da fatia atual na lista **Fatias upstream que não estão prontas**. Esse recurso é útil quando a fatia estiver no estado **Aguardando** e você quiser entender as dependências de upstream em que a fatia está aguardando.

![Fatias de upstream que não estão prontas](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### <a name="dataset-state-diagram"></a>Diagrama de estado do conjunto de dados
Após a implantação de uma data factory e a atribuição de um período de atividade válido para os pipelines, as fatias do conjunto de dados fazem a transição de um estado para outro. Atualmente, o status da fatia segue o seguinte diagrama de estado:

![Diagrama de estado](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

O fluxo de transição de estado do conjunto de dados no data factory é o seguinte: Waiting-> In-Progress/In-Progress (Validating) -> Ready/Failed.

A fatia começa em um estado de **Aguardando**, esperando pelo atendimento de pré-condições antes da execução. Depois disso, a atividade começa a ser executada e a fatia passa para um estado **Em Andamento** . A execução da atividade pode ser bem-sucedida ou falhar. A fatia é marcada como **pronta** ou **com falha**, com base no resultado da execução.

Você pode redefinir a fatia para voltar do estado **Pronto** ou **Falha** para o estado **Aguardando**. Você também pode marcar o estado da fatia como **Ignorar**, o que impedirá a execução da atividade e não processará a fatia.

## <a name="pause-and-resume-pipelines"></a>Pausar e retomar pipelines
Você pode gerenciar seus pipelines usando o Azure PowerShell. Por exemplo, você pode pausar e retomar pipelines executando cmdlets do Azure PowerShell. 

> [!NOTE] 
> A exibição de diagrama não dá suporte à pausa e continuação de pipelines. Se você desejar usar uma interface do usuário, use o aplicativo de monitoramento e gerenciamento. Para obter detalhes sobre como usar o aplicativo, consulte o artigo [Monitorar e gerenciar os pipelines do Data Factory usando o aplicativo de Monitoramento e Gerenciamento](data-factory-monitor-manage-app.md). 

Você pode pausar/suspender pipelines usando o cmdlet **Suspend-AzDataFactoryPipeline** do PowerShell. Esse cmdlet é útil quando você não quiser executar o pipeline até que um problema seja corrigido. 

```powershell
Suspend-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Por exemplo: 

```powershell
Suspend-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

Depois que o problema com o pipeline for corrigido, você poderá retomar o pipeline suspenso executando o seguinte comando do PowerShell:

```powershell
Resume-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Por exemplo: 

```powershell
Resume-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

## <a name="debug-pipelines"></a>Depurar pipelines
O Azure Data Factory fornece recursos avançados para depurar e solucionar problemas com pipelines usando do Portal do Azure e o Azure PowerShell.

> [!NOTE] 
> É muito mais fácil resolver erros usando o Aplicativo de Monitoramento e Gerenciamento. Para obter detalhes sobre como usar o aplicativo, consulte o artigo [Monitorar e gerenciar os pipelines do Data Factory usando o aplicativo de Monitoramento e Gerenciamento](data-factory-monitor-manage-app.md). 

### <a name="find-errors-in-a-pipeline"></a>Localizar erros em um pipeline
Se a execução da atividade falhar em um pipeline, o conjunto de dados produzido pelo pipeline ficará em um estado de erro devido à falha. Você pode depurar e solucionar erros no Azure Data Factory usando os seguintes métodos.

#### <a name="use-the-azure-portal-to-debug-an-error"></a>Usar o Portal do Azure para depurar um erro
1. Na folha **Tabela**, clique na fatia de problema com o **Status** definido como **Falha**.

   ![Folha de tabela com fatia com problema](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
2. Na folha **Fatia de dados**, clique na execução de atividade com falha.

   ![Fatia de dados com erro](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
3. Na folha **Detalhes de execução da atividade**, você pode baixar os arquivos associados ao processamento do HDInsight. Clique em **baixar** para status/stderr para baixar o arquivo de log de erros que contém detalhes sobre o erro.

   ![Folha de detalhes da execução da atividade com erro](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)     

#### <a name="use-powershell-to-debug-an-error"></a>Usar o PowerShell para depurar um erro
1. Inicie o **PowerShell**.
2. Execute o comando **Get-AzDataFactorySlice** para ver as fatias e seus status. Você deve ver uma fatia com o status **Falha**.        

    ```powershell   
    Get-AzDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```   
   Por exemplo: 

    ```powershell   
    Get-AzDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00
    ```

   Substitua **StartDateTime** pela hora de início do pipeline. 
3. Agora, execute o cmdlet **Get-AzDataFactoryRun** para obter detalhes sobre a execução da atividade para a fatia.

    ```powershell   
    Get-AzDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime]
    <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```

    Por exemplo: 

    ```powershell   
    Get-AzDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"
    ```

    O valor de StartDateTime é a hora de início do erro/fatia com problema observada na etapa anterior. A data e hora devem ser colocadas entre aspas duplas.
4. Você deve ver uma saída semelhante à seguinte com detalhes sobre o erro:

    ```   
    Id                      : 841b77c9-d56c-48d1-99a3-8c16c3e77d39
    ResourceGroupName       : ADF
    DataFactoryName         : LogProcessingFactory3
    DatasetName               : EnrichedGameEventsTable
    ProcessingStartTime     : 10/10/2014 3:04:52 AM
    ProcessingEndTime       : 10/10/2014 3:06:49 AM
    PercentComplete         : 0
    DataSliceStart          : 5/5/2014 12:00:00 AM
    DataSliceEnd            : 5/6/2014 12:00:00 AM
    Status                  : FailedExecution
    Timestamp               : 10/10/2014 3:04:52 AM
    RetryAttempt            : 0
    Properties              : {}
    ErrorMessage            : Pig script failed with exit code '5'. See wasb://        adfjobs@spestore.blob.core.windows.net/PigQuery
                                    Jobs/841b77c9-d56c-48d1-99a3-
                8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for
                more details.
    ActivityName            : PigEnrichLogs
    PipelineName            : EnrichGameLogsPipeline
    Type                    :
    ```
5. Você pode executar o cmdlet **Save-AzDataFactoryLog** com o valor de ID que você vê na saída e baixar os arquivos de log usando o **-DownloadLogsoption** para o cmdlet.

    ```powershell
    Save-AzDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
    ```

## <a name="rerun-failures-in-a-pipeline"></a>Executar novamente as falhas em um pipeline

> [!IMPORTANT]
> É mais fácil resolver erros e executar novamente as fatias com falha usando o Aplicativo de Monitoramento e Gerenciamento. Para obter detalhes sobre como usar o aplicativo, consulte [Monitorar e gerenciar os pipelines do Data Factory usando o aplicativo de Monitoramento e Gerenciamento](data-factory-monitor-manage-app.md). 

### <a name="use-the-azure-portal"></a>Use o Portal do Azure
Depois de solucionar problemas e depurar falhas em um pipeline, você pode executar as falhas novamente navegando até a fatia com erro e clicando no botão **Executar** na barra de comandos.

![Executar novamente uma fatia com falha](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

Em caso de falha na validação da fatia devido a uma falha de política (por exemplo, se nenhum dado estiver disponível), é possível corrigir a falha e validar novamente clicando no botão **Validar** na barra de comandos.

![Corrigir os erros e validar](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### <a name="use-azure-powershell"></a>Usar PowerShell do Azure
Você pode executar novamente as falhas usando o cmdlet **set-AzDataFactorySliceStatus** . Consulte o tópico [set-AzDataFactorySliceStatus](/powershell/module/az.datafactory/set-azdatafactoryslicestatus) para obter a sintaxe e outros detalhes sobre o cmdlet.

**Exemplo:**

O exemplo a seguir define o status de todas as fatias da tabela "DAWikiAggregatedData" como "Aguardando" no Azure Data Factory "WikiADF".

UpdateType é definido como UpstreamInPipeline, o que significa que o status de cada fatia da tabela e todas as tabelas dependentes (upstream) é definido como "Aguardando". O outro valor possível para esse parâmetro é "Individual".

```powershell
Set-AzDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -DatasetName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00
```
## <a name="create-alerts-in-the-azure-portal"></a>Criar alertas no portal do Azure

1.  Faça logon no portal do Azure e selecione **Monitor -> alertas** para abrir a página Alertas.

    ![Abra a página Alertas.](media/data-factory-monitor-manage-pipelines/v1alerts-image1.png)

2.  Selecione **+ Nova regra de alerta** para criar um novo alerta.

    ![Criar um novo alerta](media/data-factory-monitor-manage-pipelines/v1alerts-image2.png)

3.  Definir a **Condição de alerta**. (Certifique-se de selecionar **Data factories** no campo **Filtrar por tipo de recurso** .) Você também pode especificar valores para **dimensões**.

    ![Definir a condição de alerta - Selecionar destino](media/data-factory-monitor-manage-pipelines/v1alerts-image3.png)

    ![Definir a condição de alerta - Adicionar critérios de alerta](media/data-factory-monitor-manage-pipelines/v1alerts-image4.png)

    ![Definir a condição de alerta - Adicionar lógica de alerta](media/data-factory-monitor-manage-pipelines/v1alerts-image5.png)

4.  Defina os **detalhes do Alerta**.

    ![Defina os Detalhes do Alerta](media/data-factory-monitor-manage-pipelines/v1alerts-image6.png)

5.  Defina o **Grupo de Ação**.

    ![Definir o Grupo de Ação - criar um novo Grupo de Ação](media/data-factory-monitor-manage-pipelines/v1alerts-image7.png)

    ![Definir o Grupo de Ação - definir prioridades](media/data-factory-monitor-manage-pipelines/v1alerts-image8.png)

    ![Definir o Grupo de Ação - novo grupo de ação criado](media/data-factory-monitor-manage-pipelines/v1alerts-image9.png)

## <a name="move-a-data-factory-to-a-different-resource-group-or-subscription"></a>Mover um data factory para outro grupo de recursos ou assinatura
Você pode mover um data factory para outro grupo de recursos ou assinatura diferente usando o botão **Mover** da barra de comandos na home page do seu data factory.

![Mover o Data Factory](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

Você também pode mover todos os recursos relacionados (como alertas associados ao data factory) juntamente com ele.

![Caixa de diálogo Mover recursos](./media/data-factory-monitor-manage-pipelines/MoveResources.png)
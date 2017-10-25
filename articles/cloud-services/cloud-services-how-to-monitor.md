---
title: "Como monitorar um serviço de nuvem | Microsoft Docs"
description: "Saiba como monitorar serviços de nuvem usando o portal clássico do Azure."
services: cloud-services
documentationcenter: 
author: thraka
manager: timlt
editor: 
ms.assetid: 5c48d2fb-b8ea-420f-80df-7aebe2b66b1b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2015
ms.author: adegeo
ms.openlocfilehash: c369b22cf068a473343b006eb1b06fdd350d31db
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-monitor-cloud-services"></a>Como monitorar serviços de nuvem
[!INCLUDE [disclaimer](../../includes/disclaimer.md)]

Você pode monitorar as `key` métricas de desempenho de seus serviços de nuvem no portal clássico do Azure. Você pode definir o nível de monitoramento como mínimo e detalhado para cada função do serviço, além de poder personalizar as exibições de monitoramento. Os dados de monitoramento detalhados são armazenados em uma conta do armazenamento, que você poderá acessar fora do portal. 

O monitoramento das exibições no portal clássico do Azure são altamente configuráveis. Você pode escolher as métricas que você deseja monitorar na lista de métricas na página **Monitoramento** e pode escolher quais métricas para plotar em gráficos de métricas na página **Monitoramento** e no painel. 

## <a name="concepts"></a>Conceitos
Por padrão, o monitoramento mínimo é fornecido para um novo serviço de nuvem usando os contadores do desempenho coletados do sistema operacional do host para as instâncias de função (máquinas virtuais). As métricas mínimas estão limitadas a percentual de CPU, entrada de dados, saída de dados, transferência de leitura e gravação de disco. Configurando o monitoramento detalhado, você pode receber métricas adicionais com base nos dados de desempenho em máquinas virtuais (instâncias de função). As métricas detalhadas permitem uma análise mais próxima dos problemas que ocorrem durante operações de aplicativo.

Por padrão, os dados do contador de desempenho das instâncias de função passam por amostragem e são transferidos da instância de função em intervalos de 3 minutos. Quando você habilita o monitoramento detalhado, os dados brutos do contador de desempenho são agregados para cada instância de função e por instâncias de função para cada função em intervalos de 5 minutos, 1 hora e 12 horas. Os dados agregados são limpos após 10 dias.

Após habilitar o monitoramento detalhado, os dados de monitoramento agregados são armazenados nas tabelas em sua conta de armazenamento. Para habilitar o monitoramento detalhado para uma função, você deve configurar uma cadeia de conexão de diagnóstico vinculada à sua conta de armazenamento. Você pode usar contas de armazenamento diferentes para funções diferentes.

Habilitar o monitoramento detalhado aumentará os custos de armazenamento relacionados ao armazenamento de dados, transferência de dados e às transações do armazenamento. O monitoramento mínimo não requer uma conta de armazenamento. Os dados para as métricas, expostos no nível mínimo do monitoramento, não são armazenados na sua conta de armazenamento, mesmo que você defina o nível de monitoramento para detalhado.

## <a name="how-to-configure-monitoring-for-cloud-services"></a>Tutorial: Configurar o monitoramento para serviços de nuvem
Use os procedimentos a seguir para configurar o monitoramento mínimo ou detalhado no portal clássico do Azure. 

### <a name="before-you-begin"></a>Antes de começar
* Crie uma conta de armazenamento *clássico* para armazenar os dados de monitoramento. Você pode usar contas de armazenamento diferentes para funções diferentes. Para obter mais informações, consulte [Como criar contas de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account).
* Habilite o Diagnóstico do Azure para as funções do seu serviço de nuvem. Consulte [Configurando os diagnósticos para os Serviços de Nuvem](cloud-services-dotnet-diagnostics.md).

Certifique-se de que a cadeia de conexão de diagnóstico esteja presente na configuração da Função. Você não pode ativar o monitoramento detalhado até que habilite o Diagnóstico do Azure e inclua uma cadeia de conexão de diagnóstico na configuração da Função.   

> [!NOTE]
> Projetos direcionados ao Azure SDK 2.5 não incluíam automaticamente a cadeia de conexão de diagnóstico no modelo do projeto. Para esses projetos, você precisa adicionar manualmente a cadeia de conexão de diagnóstico à configuração da Função.
> 
> 

**Para adicionar manualmente a cadeia de conexão de diagnóstico à configuração da Função**

1. Abra o projeto Serviço de Nuvem no Visual Studio.
2. Clique duas vezes em **Função** para abrir o designer de Função e selecione a guia **Configurações**
3. Procure por uma configuração chamada **Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString**. 
4. Se essa configuração não estiver presente, clique no botão **Adicionar Configuração** para adicioná-la à configuração e altere o tipo para a nova configuração **ConnectionString**
5. Definir o valor da cadeia de conexão clicando no botão **...** . Isso abrirá uma caixa de diálogo que permite a seleção de uma conta de armazenamento.
   
    ![Configurações do Visual Studio](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioDiagnosticsConnectionString.png)

### <a name="to-change-the-monitoring-level-to-verbose-or-minimal"></a>Para alterar o nível de monitoramento para detalhado ou mínimo
1. No [portal clássico do Azure](https://manage.windowsazure.com/), abra a página **Configurar** para implantar o serviço de nuvem.
2. Em **Nível**, clique em **Detalhado** ou **Mínimo**. 
3. Clique em **Salvar**.

Após ativar o monitoramento detalhado, deve ser possível ver os dados de monitoramento no portal clássico do Azure dentro de uma hora.

Os dados brutos do contador de desempenho e os dados de monitoramento agregados são armazenados na conta de armazenamento, em tabelas qualificadas pela ID da implantação para as funções. 

## <a name="how-to-receive-alerts-for-cloud-service-metrics"></a>Tutorial: Receber alertas para métricas dos serviços de nuvem
Você pode receber alertas baseados nas métricas de monitoramento do serviço de nuvem. Na página **Serviços de Gerenciamento** do portal clássico do Azure, você pode criar uma regra para disparar um alerta quando a métrica escolhida alcançar um valor especificado. Você também pode escolher que um email seja enviado quando o alerta é disparado. Para saber mais, confira [Como: receber notificações de alerta e gerenciar regras de alerta no Azure](http://go.microsoft.com/fwlink/?LinkId=309356).

## <a name="how-to-add-metrics-to-the-metrics-table"></a>Tutorial: Adicionar métricas à tabela de métricas
1. No [portal clássico do Azure](http://manage.windowsazure.com/), abra a página **Monitorar** para o serviço de nuvem.
   
    Por padrão, a tabela de métricas exibe um subconjunto de métricas disponíveis. A ilustração mostra as métricas detalhadas padrão para um serviço de nuvem, que estão limitadas à contador de desempenho de memória\MBytes disponíveis, com os dados agregados à nível de função. Use **Adicionar Métricas** para selecionar métricas agregadas e de nível de função adicionais para monitorar o portal clássico do Azure.
   
    ![Exibição detalhada](./media/cloud-services-how-to-monitor/CloudServices_DefaultVerboseDisplay.png)
2. Para adicionar métricas à tabela de métricas:
   
   1. Clique em **Adicionar Métricas** para abrir **Escolher Métricas**, conforme mostrado abaixo.
      
       A primeira métrica disponível é expandida para mostrar as opções disponíveis. Para cada métrica, a primeira opção exibe os dados agregados de monitoramento para todas as funções. Além disso, você pode escolher funções individuais para a exibição de dados.
      
       ![Adicionar Métricas](./media/cloud-services-how-to-monitor/CloudServices_AddMetrics.png)
   2. Para selecionar as métricas a exibir
      
      * Clique na seta para baixo próxima à métrica para expandir as opções de monitoramento.
      * Marque a caixa de seleção para cada opção de monitoramento que você deseja exibir.
        
        Você pode exibir até 50 métricas na tabela de métricas.
        
        > [!TIP]
        > No monitoramento detalhado, a lista de métricas pode conter dezenas de métricas. Para exibir uma barra de rolagem, passe o cursor do mouse pelo lado direito da caixa de diálogo. Para filtrar a lista, clique no ícone da busca e digite o texto na caixa da busca, conforme mostrado abaixo.
        > 
        > 
        
        ![Pesquisa de métricas adicionadas](./media/cloud-services-how-to-monitor/CloudServices_AddMetrics_Search.png)
3. Após concluir a seleção de métricas, clique em OK (marca de seleção).
   
    As métricas selecionadas são adicionadas à tabela de métricas, conforme mostrado abaixo.
   
    ![métricas de monitoramento](./media/cloud-services-how-to-monitor/CloudServices_Monitor_UpdatedMetrics.png)
4. Para excluir uma métrica da tabela de métricas, clique na métrica para selecioná-la e clique em **Excluir Métricas**. (Você poderá ver **Excluir Métricas** somente quando uma métrica for selecionada).

### <a name="to-add-custom-metrics-to-the-metrics-table"></a>Para adicionar métricas personalizadas à tabela de métricas
O nível de monitoramento **detalhado** fornece uma lista de métricas padrão que você pode monitorar no portal. Além delas, você pode monitorar quaisquer métricas personalizadas ou contadores de desempenho definidos por seu aplicativo por meio do portal.

As etapas a seguir pressupõem que você ativou nível de monitoramento **Detalhado** e configurou seu aplicativo para coletar e transferir os contadores de desempenho personalizados. 

Para exibir os contadores de desempenho personalizados no portal, você precisa atualizar a configuração no wad-control-container:

1. Abra o blob wad-control-container em sua conta de armazenamento de diagnóstico. Você pode usar o Visual Studio ou qualquer outro gerenciador de armazenamento para fazer isso.
   
    ![Gerenciador de Servidores do Visual Studio](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioBlobExplorer.png)
2. Navegue pelo caminho do blob usando o padrão **DeploymentId/RoleName/RoleInstance** para localizar a configuração para sua instância de função. 
   
    ![Gerenciador de Armazenamento do Visual Studio](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioStorage.png)
3. Baixe o arquivo de configuração em sua instância de função e atualize-o para incluir todos os contadores de desempenho personalizados. Por exemplo, para monitorar *Bytes de Gravação de Disco/s* para a *unidade C* adicione o seguinte no nó **PerformanceCounters\Subscriptions**
   
    ```xml
    <PerformanceCounterConfiguration>
    <CounterSpecifier>\LogicalDisk(C:)\Disk Write Bytes/sec</CounterSpecifier>
    <SampleRateInSeconds>180</SampleRateInSeconds>
    </PerformanceCounterConfiguration>
    ```
4. Salve as alterações e carregue o arquivo de configuração no mesmo local, substituindo o arquivo existente no blob.
5. Alterne para modo Detalhado na configuração do portal clássico do Azure. Se você já estivesse no modo Detalhado, teria que alternar para o modo mínimo e voltar ao detalhado.
6. O contador de desempenho personalizados estará disponível na caixa de diálogo **Adicionar Métricas** . 

## <a name="how-to-customize-the-metrics-chart"></a>Tutorial: Personalizar o gráfico de métricas
1. Na tabela de métricas, selecione até 6 métricas para plotar no gráfico de métricas. Para selecionar uma métrica, clique na caixa de seleção à esquerda. Para remover uma métrica do gráfico de métricas, desmarque a caixa de seleção na tabela de métricas.
   
    Conforme você seleciona métricas da tabela de métricas, elas são adicionadas ao gráfico de métricas. Em uma exibição estreita, uma lista suspensa de **n mais** contém os cabeçalhos de métrica que não caberão na exibição.
2. Para alternar entre a exibição dos valores relativos (somente valor final para cada métrica) e os valores absolutos (eixo Y exibido), selecione Relativo ou Absoluto na parte superior do gráfico.
   
    ![Relativo ou Absoluto](./media/cloud-services-how-to-monitor/CloudServices_Monitor_RelativeAbsolute.png)
3. Para alterar o intervalo de tempo de exibição do gráfico de métricas, selecione 1 hora, 24 horas ou 7 dias na parte superior do gráfico.
   
    ![Período de exibição do monitoramento](./media/cloud-services-how-to-monitor/CloudServices_Monitor_DisplayPeriod.png)
   
    No gráfico de métricas do painel, o método para plotar métricas é diferente. Um conjunto padrão de métricas está disponível e métricas são adicionadas ou removidas selecionando o cabeçalho da métrica.

### <a name="to-customize-the-metrics-chart-on-the-dashboard"></a>Para personalizar o gráfico de métricas no painel
1. Abra o painel do serviço de nuvem.
2. Adicionar ou remover métricas do gráfico:
   
   * Para plotar uma nova métrica, marque a caixa de seleção da métrica nos cabeçalhos do gráfico. Em uma exibição estreita, clique na seta para baixo ***n*??metrics** para plotar uma métrica que a área de cabeçalho do gráfico não pode exibir.
   * Para excluir uma métrica que seja plotada no gráfico, desmarque a caixa de seleção próxima ao cabeçalho.
   
3. Alterne entre exibições **Relativas** e **Absoluta**.
4. Escolha 1 hora, 24, horas ou 7 dias para exibição dos dados.

## <a name="how-to-access-verbose-monitoring-data-outside-the-azure-classic-portal"></a>Como acessar dados do monitoramento detalhado fora do portal clássico do Azure
Os dados do monitoramento detalhado são armazenados em tabelas nas contas de armazenamento que você especifica para cada função. Para cada implantação do serviço de nuvem, seis tabelas são criadas para a função. Duas tabelas são criadas para cada (5 minutos, 1 hora e 12 horas). Uma dessas tabelas armazena as agregações de nível de função; outra tabela armazena as agregações para as instâncias de função. 

Os nomes das tabelas têm o formato a seguir:

```
WAD*deploymentID*PT*aggregation_interval*[R|RI]Table
```

onde:

* *deploymentID* é a GUID atribuída à implantação do serviço de nuvem
* *aggregation_interval* = 5M, 1H ou 12H
* agregações de nível de função = R
* agregações para instâncias de função = RI

Por exemplo, as tabelas a seguir armazenariam os dados agregados do monitoramento detalhado em intervalos de 1 hora:

```
WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRTable (hourly aggregations for the role)

WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRITable (hourly aggregations for role instances)
```

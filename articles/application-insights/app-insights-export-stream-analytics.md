---
title: Exportar usando o Stream Analytics no Azure Application Insights | Microsoft Docs
description: O Stream Analytics pode transformar, filtrar e rotear continuamente os dados exportados do Application Insights.
services: application-insights
documentationcenter: 
author: noamben
manager: carmonm
ms.assetid: 31594221-17bd-4e5e-9534-950f3b022209
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: bwren
ms.openlocfilehash: 6a84d8ff67c420ce712de905ab1172632502a863
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-stream-analytics-to-process-exported-data-from-application-insights"></a>Usar o Stream Analytics para processar os dados exportados do Application Insights
[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) é a ferramenta ideal para processar dados [exportados do Application Insights](app-insights-export-telemetry.md). O Stream Analytics pode extrair dados de várias fontes. Ele pode transformar e filtrar os dados e depois roteá-los a uma variedade de coletores.

Neste exemplo, vamos criar um adaptador que usa dados do Application Insights, renomeia e processa alguns dos campos e os direciona ao Power BI.

> [!WARNING]
> Há [maneiras recomendadas para exibir os dados do Application Insights no Power BI](app-insights-export-power-bi.md) muito mais fáceis e eficientes. O caminho ilustrado aqui é apenas um exemplo para mostrar como processar os dados exportados.
> 
> 

![Diagrama de bloco para exportação por meio do SA para PBI](./media/app-insights-export-stream-analytics/020.png)

## <a name="create-storage-in-azure"></a>Criar armazenamento no Azure
Exportação contínua sempre gera dados para uma conta de armazenamento do Azure, por isso você precisa primeiro criar o armazenamento.

1. Crie uma conta de armazenamento “clássica” na sua assinatura do [portal do Azure](https://portal.azure.com).
   
   ![No portal do Azure, escolha Novo, Dados e Armazenamento](./media/app-insights-export-stream-analytics/030.png)
2. Criar um contêiner
   
    ![No novo armazenamento, selecione Contêineres, clique no bloco Contêineres e, em seguida, Adicionar](./media/app-insights-export-stream-analytics/040.png)
3. Copie a chave de acesso de armazenamento
   
    Você precisará dela em breve para configurar a entrada para o serviço do Stream Analytics.
   
    ![No armazenamento, abra Configurações, Chaves e faça uma cópia da Chave de Acesso Primária](./media/app-insights-export-stream-analytics/045.png)

## <a name="start-continuous-export-to-azure-storage"></a>Iniciar exportação contínua no armazenamento do Azure
[exportação contínua](app-insights-export-telemetry.md) move os dados do Application Insights para o armazenamento do Azure.

1. No portal do Azure, navegue até o recurso do Application Insights que você criou para seu aplicativo.
   
    ![Selecione Navegar, Application Insights e o nome do seu projeto.](./media/app-insights-export-stream-analytics/050.png)
2. Crie uma exportação contínua.
   
    ![Escolha as Configurações, Exportação Contínua e Adicionar](./media/app-insights-export-stream-analytics/060.png)

    Selecione a conta de armazenamento criada anteriormente:

    ![Definir o destino de exportação](./media/app-insights-export-stream-analytics/070.png)

    Defina os tipos de eventos que você deseja ver:

    ![Escolher os tipos de evento](./media/app-insights-export-stream-analytics/080.png)

1. Deixe que alguns dados sejam acumulados. Agora relaxe e deixe as pessoas usarem seu aplicativo por um tempo. A telemetria chegará e você verá os gráficos estatísticos no [gerenciador de métricas](app-insights-metrics-explorer.md) e eventos individuais na [pesquisa de diagnóstico](app-insights-diagnostic-search.md). 
   
    E, além disso, os dados serão exportados para seu armazenamento. 
2. Inspecione os dados exportados. No Visual Studio, escolha **Exibir/Cloud Explorer**e abra Azure/Armazenamento. (Se você não tiver essa opção de menu, precisará instalar o Azure SDK: abra o diálogo Novo Projeto e abra Visual C#/Nuvem/Obter Microsoft Azure SDK para .NET.)
   
    ![](./media/app-insights-export-stream-analytics/04-data.png)
   
    Anote a parte comum do nome do caminho, que deriva do nome do aplicativo e da chave de instrumentação. 

Os eventos são gravados em arquivos blob formato JSON. Cada arquivo pode conter um ou mais eventos. Portanto, gostaríamos de escrever um código para ler os dados de evento e filtrar os campos desejados. Podemos fazer todos os tipos de coisas com os dados, mas nosso plano para hoje é usar o Stream Analytics para redirecionar os dados ao Power BI.

## <a name="create-an-azure-stream-analytics-instance"></a>Criar uma instância do Azure Stream Analytics
No [Portal do Azure Clássico](https://manage.windowsazure.com/), selecione o serviço do Azure Stream Analytics e crie um novo trabalho do Stream Analytics:

![](./media/app-insights-export-stream-analytics/090.png)

![](./media/app-insights-export-stream-analytics/100.png)

Quando o novo trabalho for criado, expanda seus detalhes:

![](./media/app-insights-export-stream-analytics/110.png)

### <a name="set-blob-location"></a>Definir local de blob
Defina a entrada do seu blob de Exportação Contínua:

![](./media/app-insights-export-stream-analytics/120.png)

Agora, você precisará da Chave de Acesso Primária da sua Conta de Armazenamento, previamente anotada. Defina isso como a chave da conta de armazenamento.

![](./media/app-insights-export-stream-analytics/130.png)

### <a name="set-path-prefix-pattern"></a>Definir padrão de prefixo de caminho
![](./media/app-insights-export-stream-analytics/140.png)

**Defina o Formato de Data como AAAA-MM-DD (com traços).**

O Padrão de Prefixo de Caminho especifica como o Stream Analytics encontra os arquivos de entrada no armazenamento. Você precisa configurá-lo para corresponder à maneira como a Exportação Contínua armazena os dados. Defina-o assim:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

Neste exemplo:

* `webapplication27` é o nome do recurso do Application Insights **todo em minúsculas**.
* `1234...` é a chave de instrumentação do recurso do Application Insights **sem traços**. 
* `PageViews` é o tipo de dados que você deseja analisar. Os tipos disponíveis dependem do filtro definido na Exportação Contínua. Examine os dados exportados para ver os outros tipos disponíveis e veja o [modelo de exportação de dados](app-insights-export-data-model.md).
* `/{date}/{time}` um padrão escrito literalmente.

> [!NOTE]
> Inspecione o armazenamento para garantir que o caminho está certo.
> 
> 

### <a name="finish-initial-setup"></a>Concluir a configuração inicial
Confirme o formato de serialização:

![Confirme e feche o assistente](./media/app-insights-export-stream-analytics/150.png)

Feche o assistente e aguarde até que a instalação seja concluída.

> [!TIP]
> Use o comando Sample para baixar alguns dados. Mantenha-os como exemplo de teste para depurar sua consulta.
> 
> 

## <a name="set-the-output"></a>Definir a saída
Agora, selecione seu trabalho e defina a saída.

![Selecione o novo canal, clique em Saídas, Adicionar, Power BI](./media/app-insights-export-stream-analytics/160.png)

Forneça sua **conta corporativa ou de estudante** para autorizar o Stream Analytics a acessar seu recurso do Power BI. Em seguida, crie um nome para a saída, bem como para a tabela e o conjunto de dados do Power BI de destino.

![Crie três nomes](./media/app-insights-export-stream-analytics/170.png)

## <a name="set-the-query"></a>Definir a consulta
A consulta controla a conversão de entrada para a saída.

![Selecione o trabalho e clique em Consulta. Cole o exemplo a seguir.](./media/app-insights-export-stream-analytics/180.png)

Use a função Test para verificar se você obteve a saída certa. Atribua a ela os exemplos de dados que você obteve da página de entradas. 

### <a name="query-to-display-counts-of-events"></a>Consulta para exibir contagens de eventos
Cole esta consulta:

```SQL

    SELECT
      flat.ArrayValue.name,
      count(*)
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.[event]) as flat
    GROUP BY TumblingWindow(minute, 1), flat.ArrayValue.name
```

* export-input é o alias que atribuímos à entrada do fluxo
* pbi-output é o alias de saída que definimos
* Usamos [OUTER APPLY GetElements](https://msdn.microsoft.com/library/azure/dn706229.aspx) porque o nome do evento está em uma matriz JSON aninhada. Em seguida, o Select seleciona o nome do evento, juntamente com uma contagem do número de instâncias com esse nome no período de tempo. A cláusula [Agrupar Por](https://msdn.microsoft.com/library/azure/dn835023.aspx) agrupa os elementos em períodos de tempo de 1 minuto.

### <a name="query-to-display-metric-values"></a>Consulta para exibir valores de métricas
```SQL

    SELECT
      A.context.data.eventtime,
      avg(CASE WHEN flat.arrayvalue.myMetric.value IS NULL THEN 0 ELSE  flat.arrayvalue.myMetric.value END) as myValue
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.context.custom.metrics) as flat
    GROUP BY TumblingWindow(minute, 1), A.context.data.eventtime

``` 

* Essa consulta detalha a telemetria de métricas para obter a hora do evento e o valor da métrica. Os valores de métrica estão dentro de uma matriz, por isso usamos o padrão OUTER APPLY GetElements para extrair as linhas. "myMetric" é o nome da métrica nesse caso. 

### <a name="query-to-include-values-of-dimension-properties"></a>Consulta para incluir valores das propriedades de dimensão
```SQL

    WITH flat AS (
    SELECT
      MySource.context.data.eventTime as eventTime,
      InstanceId = MyDimension.ArrayValue.InstanceId.value,
      BusinessUnitId = MyDimension.ArrayValue.BusinessUnitId.value
    FROM MySource
    OUTER APPLY GetArrayElements(MySource.context.custom.dimensions) MyDimension
    )
    SELECT
     eventTime,
     InstanceId,
     BusinessUnitId
    INTO AIOutput
    FROM flat

```

* Essa consulta inclui os valores das propriedades de dimensão sem a necessidade de ter uma determinada dimensão em um índice fixo na matriz de dimensão.

## <a name="run-the-job"></a>Executar o trabalho
Você pode selecionar uma data no passado a partir da qual iniciar o trabalho. 

![Selecione o trabalho e clique em Consulta. Cole o exemplo a seguir.](./media/app-insights-export-stream-analytics/190.png)

Aguarde até o trabalho estar Em execução.

## <a name="see-results-in-power-bi"></a>Ver os resultados no Power BI
> [!WARNING]
> Há [maneiras recomendadas para exibir os dados do Application Insights no Power BI](app-insights-export-power-bi.md) muito mais fáceis e eficientes. O caminho ilustrado aqui é apenas um exemplo para mostrar como processar os dados exportados.
> 
> 

Abra o Power BI com sua conta corporativa ou de estudante e selecione o conjunto de dados e a tabela que você definiu como a saída do trabalho do Stream Analytics.

![No Power BI, selecione o conjunto de dados e os campos.](./media/app-insights-export-stream-analytics/200.png)

Agora você pode usar esse conjunto de dados em relatórios e painéis no [Power BI](https://powerbi.microsoft.com).

![No Power BI, selecione o conjunto de dados e os campos.](./media/app-insights-export-stream-analytics/210.png)

## <a name="no-data"></a>Não há dados?
* Verifique se você [definiu o formato de data](#set-path-prefix-pattern) corretamente para AAAA-MM-DD (com traços).

## <a name="video"></a>Vídeo
Noam Ben Zeev mostra como processar dados exportados usando o Stream Analytics.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Export-to-Power-BI-from-Application-Insights/player]
> 
> 

## <a name="next-steps"></a>Próximas etapas
* [Exportação contínua](app-insights-export-telemetry.md)
* [Referência de modelo de dados detalhados para os tipos de propriedades e valores.](app-insights-export-data-model.md)
* [Application Insights](app-insights-overview.md)


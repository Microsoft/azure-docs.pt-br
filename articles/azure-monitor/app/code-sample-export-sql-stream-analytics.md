---
title: Exportar para o SQL do Azure Application Insights | Microsoft Docs
description: Exportar dados continuamente do Application Insights para o SQL usando o Stream Analytics
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 48903032-2c99-4987-9948-d6e4559b4a63
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: mbullwin
ms.openlocfilehash: 0166622539a439a99aef7e476519881518baa0b0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60904419"
---
# <a name="walkthrough-export-to-sql-from-application-insights-using-stream-analytics"></a>Passo a passo: Exportar para SQL do Application Insights usando o Stream Analytics
Este artigo mostra como mover os dados de telemetria do [Azure Application Insights][start] em um banco de dados SQL do Azure usando [Exportação Contínua][export] e [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). 

A exportação contínua move os dados de telemetria no Armazenamento do Azure no formato JSON. Vamos analisar objetos JSON usando o Azure Stream Analytics e criando linhas em uma tabela de banco de dados.

(Normalmente, a Exportação Contínua é a maneira de fazer sua própria análise da telemetria enviada pelos seus aplicativos ao Application Insights. Você pode adaptar este exemplo de código para fazer outras coisas com a telemetria exportada, como agregação de dados).

Vamos começar supondo que você já tenha o aplicativo que você deseja monitorar.

Neste exemplo, usaremos os dados de exibição de página, mas o mesmo padrão pode ser facilmente ampliado para outros tipos de dados como exceções e eventos personalizados. 

## <a name="add-application-insights-to-your-application"></a>Adicione o Application Insights ao seu aplicativo
Introdução:

1. [Configurar o Application Insights para sua página da Web](../../azure-monitor/app/javascript.md). 
   
    (Neste exemplo, vamos nos concentrar no processamento de dados de exibição de página de navegadores do cliente, mas você também pode configurar o Application Insights do lado do servidor do seu aplicativo [Java](../../azure-monitor/app/java-get-started.md) ou [ASP.NET](../../azure-monitor/app/asp-net.md) e processar telemetrias de solicitações, dependências e outras telemetrias do servidor.)
2. Publicar seu aplicativo e observar os dados de telemetria que aparecem em seu recurso Application Insights.

## <a name="create-storage-in-azure"></a>Criar armazenamento no Azure
Exportação contínua sempre gera dados para uma conta de armazenamento do Azure, por isso você precisa primeiro criar o armazenamento.

1. Crie uma conta de armazenamento na sua assinatura do [Portal do Azure][portal].
   
    ![No portal do Azure, escolha Novo, Dados e Armazenamento. Selecione Clássica, escolha Criar. Forneça um nome de armazenamento.](./media/code-sample-export-sql-stream-analytics/040-store.png)
2. Criar um contêiner
   
    ![No novo armazenamento, selecione Contêineres, clique no bloco Contêineres e, em seguida, Adicionar](./media/code-sample-export-sql-stream-analytics/050-container.png)
3. Copie a chave de acesso de armazenamento
   
    Você precisará dela em breve para configurar a entrada para o serviço do Stream Analytics.
   
    ![No armazenamento, abra Configurações, Chaves e faça uma cópia da Chave de Acesso Primária](./media/code-sample-export-sql-stream-analytics/21-storage-key.png)

## <a name="start-continuous-export-to-azure-storage"></a>Iniciar exportação contínua no armazenamento do Azure
1. No portal do Azure, navegue até o recurso do Application Insights que você criou para seu aplicativo.
   
    ![Selecione Navegar, Application Insights e o nome do seu projeto.](./media/code-sample-export-sql-stream-analytics/060-browse.png)
2. Crie uma exportação contínua.
   
    ![Escolha as Configurações, Exportação Contínua e Adicionar](./media/code-sample-export-sql-stream-analytics/070-export.png)

    Selecione a conta de armazenamento criada anteriormente:

    ![Definir o destino de exportação](./media/code-sample-export-sql-stream-analytics/080-add.png)

    Defina os tipos de eventos que você deseja ver:

    ![Escolher os tipos de evento](./media/code-sample-export-sql-stream-analytics/085-types.png)


1. Deixe que alguns dados sejam acumulados. Agora relaxe e deixe as pessoas usarem seu aplicativo por um tempo. A telemetria chegará e você verá os gráficos estatísticos no [gerenciador de métricas](../../azure-monitor/app/metrics-explorer.md) e eventos individuais na [pesquisa de diagnóstico](../../azure-monitor/app/diagnostic-search.md). 
   
    E, além disso, os dados serão exportados para seu armazenamento. 
2. Inspecione os dados exportados no portal – escolha **Procurar**, selecione sua conta de armazenamento e depois **Contêineres** – ou no Visual Studio. No Visual Studio, escolha **Exibir/Cloud Explorer**e abra Azure/Armazenamento. (se você não tiver essa opção de menu, será necessário instalar o SDK do Azure: Abra a caixa de diálogo Novo Projeto e abra o Visual C# / Cloud / Get Microsoft Azure SDK for .NET.)
   
    ![No Visual Studio, abra o Navegador do Servidor, Azure e Armazenamento](./media/code-sample-export-sql-stream-analytics/087-explorer.png)
   
    Anote a parte comum do nome do caminho, que deriva do nome do aplicativo e da chave de instrumentação. 

Os eventos são gravados em arquivos blob formato JSON. Cada arquivo pode conter um ou mais eventos. Portanto, gostaríamos de escrever um código para ler os dados de evento e filtrar os campos desejados. Podemos fazer todos os tipos de coisas com os dados, mas nosso plano para hoje é escrever um código para mover os dados para um banco de dados SQL. Isso nos permitirá executar diversas consultas interessantes.

## <a name="create-an-azure-sql-database"></a>Criar um Banco de Dados SQL do Azure
Mais uma vez, na sua assinatura no [portal do Azure][portal], crie o banco de dados (e um novo servidor, a menos que você já tenha um) onde você vai gravar os dados.

![Novo, Dados, SQL](./media/code-sample-export-sql-stream-analytics/090-sql.png)

Verifique se o servidor de banco de dados permite o acesso aos serviços do Azure:

![Navegar, Servidores, seu servidor, Configurações, Firewall, Permitir Acesso ao Azure](./media/code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## <a name="create-a-table-in-azure-sql-db"></a>Criar uma tabela no banco de dados do Azure SQL
Conecte-se ao banco de dados criado na seção anterior com sua ferramenta de gerenciamento preferida. Neste passo a passo, usaremos as SSMS ( [Ferramentas de Gerenciamento do SQL Server](https://msdn.microsoft.com/ms174173.aspx) ).

![](./media/code-sample-export-sql-stream-analytics/31-sql-table.png)

Crie uma nova consulta e execute o T-SQL a seguir:

```SQL

CREATE TABLE [dbo].[PageViewsTable](
    [pageName] [nvarchar](max) NOT NULL,
    [viewCount] [int] NOT NULL,
    [url] [nvarchar](max) NULL,
    [urlDataPort] [int] NULL,
    [urlDataprotocol] [nvarchar](50) NULL,
    [urlDataHost] [nvarchar](50) NULL,
    [urlDataBase] [nvarchar](50) NULL,
    [urlDataHashTag] [nvarchar](max) NULL,
    [eventTime] [datetime] NOT NULL,
    [isSynthetic] [nvarchar](50) NULL,
    [deviceId] [nvarchar](50) NULL,
    [deviceType] [nvarchar](50) NULL,
    [os] [nvarchar](50) NULL,
    [osVersion] [nvarchar](50) NULL,
    [locale] [nvarchar](50) NULL,
    [userAgent] [nvarchar](max) NULL,
    [browser] [nvarchar](50) NULL,
    [browserVersion] [nvarchar](50) NULL,
    [screenResolution] [nvarchar](50) NULL,
    [sessionId] [nvarchar](max) NULL,
    [sessionIsFirst] [nvarchar](50) NULL,
    [clientIp] [nvarchar](50) NULL,
    [continent] [nvarchar](50) NULL,
    [country] [nvarchar](50) NULL,
    [province] [nvarchar](50) NULL,
    [city] [nvarchar](50) NULL
)

CREATE CLUSTERED INDEX [pvTblIdx] ON [dbo].[PageViewsTable]
(
    [eventTime] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON)

```

![](./media/code-sample-export-sql-stream-analytics/34-create-table.png)

Neste exemplo, estamos usando dados de modos de exibição de página. Para ver os outros dados disponíveis, inspecione a saída JSON e veja o [modelo de exportação de dados](../../azure-monitor/app/export-data-model.md).

## <a name="create-an-azure-stream-analytics-instance"></a>Criar uma instância do Azure Stream Analytics
No [Portal do Azure](https://portal.azure.com/), selecione o serviço do Azure Stream Analytics e crie um novo trabalho do Stream Analytics:

![Configurações do Stream analytics](./media/code-sample-export-sql-stream-analytics/SA001.png)

![](./media/code-sample-export-sql-stream-analytics/SA002.png)

Após a criação do novo trabalho, escolha **Ir para o recurso**.

![Configurações do Stream analytics](./media/code-sample-export-sql-stream-analytics/SA003.png)

#### <a name="add-a-new-input"></a>Adicionar uma nova entrada

![Configurações do Stream analytics](./media/code-sample-export-sql-stream-analytics/SA004.png)

Defina a entrada do seu blob de Exportação Contínua:

![Configurações do Stream analytics](./media/code-sample-export-sql-stream-analytics/SA0005.png)

Agora, você precisará da Chave de Acesso Primária da sua Conta de Armazenamento, previamente anotada. Defina isso como a chave da conta de armazenamento.

#### <a name="set-path-prefix-pattern"></a>Definir padrão de prefixo de caminho

**Defina o Formato de Data como AAAA-MM-DD (com traços).**

O Padrão de Prefixo de Caminho especifica como o Stream Analytics encontra os arquivos de entrada no armazenamento. Você precisa configurá-lo para corresponder à maneira como a Exportação Contínua armazena os dados. Defina-o assim:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

Neste exemplo:

* `webapplication27` é o nome do recurso do Application Insights, **todo em minúsculas**. 
* `1234...` é a chave de instrumentação do recurso do Application Insights **com traços removidos**. 
* `PageViews` é o tipo de dados que desejamos analisar. Os tipos disponíveis dependem do filtro definido na Exportação Contínua. Examine os dados exportados para ver os outros tipos disponíveis e veja o [modelo de exportação de dados](../../azure-monitor/app/export-data-model.md).
* `/{date}/{time}` um padrão escrito literalmente.

Para obter o nome e iKey do seu recurso do Application Insights, abra Essentials na sua página de visão geral ou abra as Configurações.

> [!TIP]
> Use a função Amostra para verificar se configurou corretamente o caminho de entrada. Se isso falhar: Verifique se há dados no armazenamento para o intervalo de tempo de amostra escolhido. Edite a definição de entrada e verifique se definiu corretamente a conta de armazenamento, o prefixo de caminho e o formato de data.
> 
> 
> ## <a name="set-query"></a>Definir a consulta
> Abra a seção de consulta:

Substitua a consulta padrão por:

```SQL

    SELECT flat.ArrayValue.name as pageName
    , flat.ArrayValue.count as viewCount
    , flat.ArrayValue.url as url
    , flat.ArrayValue.urlData.port as urlDataPort
    , flat.ArrayValue.urlData.protocol as urlDataprotocol
    , flat.ArrayValue.urlData.host as urlDataHost
    , flat.ArrayValue.urlData.base as urlDataBase
    , flat.ArrayValue.urlData.hashTag as urlDataHashTag
      ,A.context.data.eventTime as eventTime
      ,A.context.data.isSynthetic as isSynthetic
      ,A.context.device.id as deviceId
      ,A.context.device.type as deviceType
      ,A.context.device.os as os
      ,A.context.device.osVersion as osVersion
      ,A.context.device.locale as locale
      ,A.context.device.userAgent as userAgent
      ,A.context.device.browser as browser
      ,A.context.device.browserVersion as browserVersion
      ,A.context.device.screenResolution.value as screenResolution
      ,A.context.session.id as sessionId
      ,A.context.session.isFirst as sessionIsFirst
      ,A.context.location.clientip as clientIp
      ,A.context.location.continent as continent
      ,A.context.location.country as country
      ,A.context.location.province as province
      ,A.context.location.city as city
    INTO
      AIOutput
    FROM AIinput A
    CROSS APPLY GetElements(A.[view]) as flat


```

Observe que as primeiras propriedades são específicas aos dados de exibição da página. Exportações de outros tipos de telemetria terão propriedades diferentes. [Referência de modelo de dados detalhados para os tipos de propriedades e valores.](../../azure-monitor/app/export-data-model.md)

## <a name="set-up-output-to-database"></a>Configurar a saída para o banco de dados
Selecione SQL como a saída.

![No Stream Analytics, selecione Saídas](./media/code-sample-export-sql-stream-analytics/SA006.png)

Especifique o Banco de Dados SQL.

![Preencha os detalhes do seu banco de dados](./media/code-sample-export-sql-stream-analytics/SA007.png)

Feche o assistente e aguarde uma notificação de que a saída foi configurada.

## <a name="start-processing"></a>Iniciar o processamento
Inicie o trabalho na barra de ação:

![No Stream Analytics, clique em Iniciar](./media/code-sample-export-sql-stream-analytics/SA008.png)

Você pode optar por iniciar o processamento de dados neste momento ou iniciar com dados anteriores. O último é útil se você tiver Exportação Contínua já em execução por um tempo.

Depois de alguns minutos, volte para as Ferramentas de Gerenciamento do SQL Server e observe os dados entrando. Por exemplo, use uma consulta como esta:

    SELECT TOP 100 *
    FROM [dbo].[PageViewsTable]


## <a name="related-articles"></a>Artigos relacionados
* [Exportar para Power BI usando o Stream Analytics](../../azure-monitor/app/export-power-bi.md )
* [Referência de modelo de dados detalhados para os tipos de propriedades e valores.](../../azure-monitor/app/export-data-model.md)
* [Exportação Contínua no Application Insights](../../azure-monitor/app/export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[export]: ../../azure-monitor/app/export-telemetry.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[portal]: https://portal.azure.com/
[start]: ../../azure-monitor/app/app-insights-overview.md


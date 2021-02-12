---
title: Conecte seu ambiente ao Power BI-Azure Time Series Insights | Microsoft Docs
description: Saiba como conectar Azure Time Series Insights a Power BI para compartilhar, exibir gráficos e mostrar dados em sua organização.
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: b4ed5a419df97f98b883a07825184122945e092e
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879554"
---
# <a name="visualize-data-from-azure-time-series-insights-in-power-bi"></a>Visualizar dados de Azure Time Series Insights no Power BI

Azure Time Series Insights é uma plataforma para armazenar, gerenciar, consultar e Visualizar dados de série temporal na nuvem. [Power bi](https://powerbi.microsoft.com) é uma ferramenta de análise de negócios com recursos de visualização avançados que permite que você compartilhe informações e resultados em toda a sua organização. Ambos os serviços agora podem ser integrados, permitindo que você aumente a poderosa análise de Azure Time Series Insights com a visualização forte de dados e os recursos de compartilhamento fácil do Power BI.

Você aprenderá a:

* Conectar Azure Time Series Insights ao Power BI usando o conector do Azure Time Series Insights nativo
* Crie visuais com seus dados de série temporal no Power BI
* Publicar o relatório para Power BI e compartilhá-lo com o restante da sua organização


## <a name="prerequisites"></a>Pré-requisitos

* Inscreva-se em uma [assinatura do Azure gratuita](https://azure.microsoft.com/free/) se já não tiver uma.
* Baixe e instale a versão mais recente do [Power bi desktop](https://powerbi.microsoft.com/downloads/)
* Ter ou criar um [ambiente de Gen2 de Azure Time Series insights](./how-to-provision-manage.md)

Examine [as políticas de acesso do ambiente](./concepts-access-policies.md) e verifique se você tem acesso direto ou de convidado ao ambiente do Azure Time Series insights Gen2. 

> [!IMPORTANT]
> * Baixe e instale a versão mais recente do [Power bi desktop](https://powerbi.microsoft.com/downloads/). Para acompanhar as etapas neste artigo, verifique se você tem pelo menos a versão de dezembro de 2020 (2.88.321.0) do Power BI Desktop instalada. 

## <a name="connect-data-from-azure-time-series-insights-to-power-bi"></a>Conectar dados de Azure Time Series Insights para Power BI

### <a name="1-export-data-into-power-bi-desktop"></a>1. exportar dados para o Power BI desktop

Introdução:

1. Abra o Azure Time Series Insights Gen2 Explorer e organizar seus dados. Esses são os dados que serão exportados para Power BI.
1. Depois de criar uma exibição com a qual você está satisfeito, navegue até o menu suspenso **mais ações** e selecione **conectar-se a Power bi**.

    [![Azure Time Series Insights a exportação do Gen2 Explorer](media/how-to-connect-power-bi/export-from-explorer.jpg)](media/how-to-connect-power-bi/export-from-explorer.jpg#lightbox)

1. Defina seus parâmetros para exportação:

   * **Formato de dados**: escolha se deseja exportar **dados agregados** ou **eventos brutos** para Power bi. 

       > [!NOTE]
       > * Se você exportar eventos brutos, poderá agregar esses dados posteriormente em Power BI. No entanto, se você exportar dados agregados, não poderá reverter para dados brutos no Power BI. 
       > * Há um limite de contagem de eventos de 250.000 para dados brutos de nível de evento.

   * **Intervalo de tempo**: escolha se deseja ver um intervalo de tempo **fixo** ou os dados **mais recentes** em Power bi. Escolher o intervalo de tempo fixo significa que os dados no intervalo de pesquisa que você gráficou serão exportados para Power BI. Escolher o intervalo de tempo mais recente significa que Power BI obterá os dados mais recentes para o intervalo de pesquisa que você escolheu (por exemplo, se você criar o gráfico de 1 hora de dados e escolher a configuração "mais recente", o conector de Power BI sempre fará consultas para as mais recentes 1 hora dos dados.)
  
   * **Tipo de repositório**: escolha se você deseja executar a consulta selecionada na **loja a quente** ou na **loja a frio**. 

    > [!TIP]
    > * Azure Time Series Insights Explorer selecionará automaticamente os parâmetros recomendados dependendo dos dados que você escolheu exportar. 

1. Depois de definir as configurações, selecione **copiar consulta para área de transferência**.

    [![Janela restrita de exportação do Azure Time Series Insights Explorer](media/how-to-connect-power-bi/choose-explorer-parameters.jpg)](media/how-to-connect-power-bi/choose-explorer-parameters.jpg#lightbox)

2. Inicie o Power BI Desktop.
   
3. Em Power BI Desktop na guia **início** , selecione **obter dados** no canto superior esquerdo e, em seguida, **mais**.

    [![Obter dados no Power BI](media/how-to-connect-power-bi/get-data-power-bi.jpg)](media/how-to-connect-power-bi/get-data-power-bi.jpg#lightbox)

4. Procure **Azure Time Series insights**, selecione **Azure Time Series insights (beta)** e **Conecte-se**.

    [![Conectar Power BI ao Azure Time Series Insights](media/how-to-connect-power-bi/select-tsi-connector.jpg)](media/how-to-connect-power-bi/select-tsi-connector.jpg#lightbox)

    Como alternativa, navegue até a guia **Azure** , selecione **Azure Time Series insights (beta)** e **Conecte-se**.

5. Cole a consulta copiada do Azure Time Series Insights Explorer no campo de **consulta personalizada** e pressione **OK**.

    [![Cole a consulta personalizada e selecione OK](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

6.  A tabela de dados será carregada agora. Pressione **carregar** para carregar no Power bi. Se desejar fazer transformações nos dados, você poderá fazer isso agora clicando em **transformar dados**. Você também pode transformar seus dados depois que eles são carregados.

    [![Examine os dados na tabela e selecione carregar](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

## <a name="create-a-report-with-visuals"></a>Criar um relatório com elementos visuais

Agora que você importou os dados para Power BI, é hora de criar um relatório com visuais.

1. No lado esquerdo da janela, verifique se você selecionou o modo de exibição de **relatório** .

    [![Captura de tela mostra o ícone de exibição de relatório.](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1. Na coluna **visualizações** , selecione seu visual de sua escolha. Por exemplo, selecione **gráfico de linhas**. Isso adicionará um gráfico de linhas em branco à tela.

1.  Na lista **campos** , selecione **_Timestamp** e arraste-o para o campo **eixo** para exibir a hora ao longo do eixo X. Certifique-se de mudar para **_Timestamp** como o valor do **eixo** (o padrão é a **hierarquia de data**).

    [![Selecionar _Timestamp](media/how-to-connect-power-bi/select-timestamp.png)](media/how-to-connect-power-bi/select-timestamp.png#lightbox)

1.  Novamente, na lista **campos** , selecione a variável que você deseja Plotar e arraste-a para o campo **valores** para exibir valores ao longo do eixo Y. Selecione o valor da ID de série temporal e arraste-o para o campo **legenda** para criar várias linhas no gráfico, uma por ID de série temporal. Isso processará uma exibição semelhante à fornecida pelo Azure Time Series Insights Explorer! 

    [![Criar um gráfico de linhas básico](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1. Para adicionar outro gráfico à tela, selecione qualquer lugar na tela fora do gráfico de linhas e repita esse processo.

    [![Criar gráficos adicionais para compartilhar](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

Depois de criar o relatório, você poderá publicá-lo no Power BI Reporting Services e compartilhá-lo com outras pessoas em sua organização.

## <a name="advanced-editing"></a>Edição avançada
Se você já carregou um conjunto de dados em Power BI mas deseja modificar a consulta (como os parâmetros de ID de ambiente ou de data/hora ou), você pode fazer isso por meio da funcionalidade de Editor Avançado do Power BI. Consulte a [documentação do Power bi](/power-bi/desktop-query-overview) para saber mais sobre como fazer alterações usando o **Editor de Power Query**. 

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [Power bi desktop](/power-bi/desktop-query-overview).

* Saiba mais sobre como [consultar dados](concepts-query-overview.md) no Azure Time Series insights Gen2.
---
title: "Visualização de dados em tempo real de dados de sensor do Hub IoT do Azure – Power BI | Microsoft Docs"
description: "Use o Power BI para visualizar dados de temperatura e umidade que são coletados do sensor e enviados para o Hub IoT do Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "visualização de dados em tempo real, visualização de dados dinâmicos, visualização de dados de sensor"
ms.assetid: e67c9c09-6219-4f0f-ad42-58edaaa74f61
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: xshi
ms.openlocfilehash: b190fea06ffc2406d781c7edad091f097cca9c2d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Visualizar dados de sensor em tempo real do Hub IoT usando o Power BI

![Diagrama de ponta a ponta](media/iot-hub-get-started-e2e-diagram/4.png)


[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>O que você aprenderá

Você aprenderá a visualizar dados do sensor em tempo real que recebe o hub IoT do Azure pelo Power BI. Se você quiser tentar visualizar os dados em seu Hub IoT com Aplicativos Web, consulte [Usar Aplicativos Web do Azure para visualizar dados de sensor em tempo real do Hub IoT do Azure](iot-hub-live-data-visualization-in-web-apps.md).

## <a name="what-you-do"></a>O que fazer

- Preparar seu Hub IoT para acesso a dados, adicionando um grupo de consumidores.
- Criar, configurar e executar um trabalho do Stream Analytics para transferência de dados do seu hub IoT à sua conta do Power BI.
- Crie e publique um relatório do Power BI para visualizar os dados.

## <a name="what-you-need"></a>O que você precisa

- Tutorial [Configurar seu dispositivo](iot-hub-raspberry-pi-kit-node-get-started.md) concluído que aborda os seguintes requisitos:
  - Uma assinatura ativa do Azure.
  - Um hub IoT do Azure em sua assinatura.
  - O aplicativo cliente que envia mensagens para o hub IoT do Azure.
- Uma conta do Power BI. ([Experimente gratuitamente o Power BI](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Criar, configurar e executar um trabalho do Stream Analytics

### <a name="create-a-stream-analytics-job"></a>Criar um trabalho de Stream Analytics

1. No portal do Azure, clique em novo > Internet das coisas > trabalho do Stream Analytics.
1. Insira as seguintes informações para o trabalho.

   **Nome do trabalho**: o nome do trabalho. O nome deve ser globalmente exclusivo.

   **Grupo de recursos**: use o mesmo grupo de recursos usado pelo seu hub IoT.

   **Local**: use o mesmo local do que o grupo de recursos.

   **Fixar no painel**: marque essa opção para facilitar o acesso ao seu hub IoT do painel.

   ![Criar um trabalho do Stream Analytics no Azure](media/iot-hub-live-data-visualization-in-power-bi/2_create-stream-analytics-job-azure.png)

1. Clique em **Criar**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Adicionar uma entrada ao trabalho do Stream Analytics

1. Abra o trabalho do Stream Analytics.
1. Em **Topologia do Trabalho**, clique em **Entradas**.
1. No **entradas** painel, clique em **adicionar**e, em seguida, insira as seguintes informações:

   **Alias de entrada**: o alias exclusivo para a entrada.

   **Origem**: selecione **hub IoT**.

   **Grupo de consumidores**: selecione o grupo de consumidores que você acabou de criar.
1. Clique em **Criar**.

   ![Adicionar uma entrada a um trabalho do Stream Analytics no Azure](media/iot-hub-live-data-visualization-in-power-bi/3_add-input-to-stream-analytics-job-azure.png)

### <a name="add-an-output-to-the-stream-analytics-job"></a>Adicionar uma saída ao trabalho do Stream Analytics

1. Em **Topologia do Trabalho**, clique em **Saídas**.
1. No **saídas** painel, clique em **adicionar**e, em seguida, insira as seguintes informações:

   **Alias de saída**: o alias exclusivo para a saída.

   **Coletor**: selecione **Power BI**.
1. Clique em **Autorizar** e depois entre na sua conta do Power BI.
1. Uma vez autorizado, insira as seguintes informações:

   **Espaço de trabalho de grupo**: selecione o espaço de trabalho do grupo de destino.

   **Nome do Conjunto de Dados**: insira um nome de conjunto de dados.

   **Nome da Tabela**: insira um nome de tabela.
1. Clique em **Criar**.

   ![Adicionar uma saída a um trabalho do Stream Analytics no Azure](media/iot-hub-live-data-visualization-in-power-bi/4_add-output-to-stream-analytics-job-azure.png)

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configurar a consulta do trabalho do Stream Analytics

1. Em **Topologia do Trabalho**, clique em **Consulta**.
1. Substitua `[YourInputAlias]` pelo alias de entrada do trabalho.
1. Substitua `[YourOutputAlias]` pelo alias de saída do trabalho.
1. Clique em **Salvar**.

   ![Adicionar uma consulta a um trabalho do Stream Analytics no Azure](media/iot-hub-live-data-visualization-in-power-bi/5_add-query-stream-analytics-job-azure.png)

### <a name="run-the-stream-analytics-job"></a>Executar o trabalho do Stream Analytics

No trabalho do Stream Analytics, clique em **Iniciar** > **Agora** > **Iniciar**. Depois que o trabalho é iniciado com êxito, o status do trabalho muda de **parado** para **executando**.

![Executar um trabalho do Stream Analytics no Azure](media/iot-hub-live-data-visualization-in-power-bi/6_run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Criar e publicar um relatório do Power BI para visualizar os dados

1. Verifique se o aplicativo de exemplo está em execução em seu dispositivo. Se não, você pode consultar os tutoriais em [Configure seu dispositivo](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started).
1. Entre na sua conta do [Power BI](https://powerbi.microsoft.com/en-us/).
1. Vá para o espaço de trabalho de grupo que você definiu quando criou a saída para o trabalho do Stream Analytics.
1. Clique em **Conjuntos de dados de streaming**.

   Você deve ver o conjunto de dados relacionado que você especificou quando criou a saída para o trabalho do Stream Analytics.
1. Em **AÇÕES**, clique no primeiro ícone para criar um relatório.

   ![Criar um relatório do Microsoft Power BI](media/iot-hub-live-data-visualization-in-power-bi/7_create-power-bi-report-microsoft.png)

1. Crie um gráfico de linhas para mostrar a temperatura em tempo real ao longo do tempo.
   1. Na página de criação de relatório, adicione um gráfico de linhas.
   1. Sobre o **campos** painel, expanda a tabela que você especificou quando criou a saída para o trabalho do Stream Analytics.
   1. Arraste **EventEnqueuedUtcTime** para **eixo** sobre o **visualizações** painel.
   1. Arraste **temperatura** para **Valores**.

      Agora, um gráfico de linhas é criado. O eixo x exibe a data e a hora no fuso horário UTC. O eixo y mostra a temperatura do sensor.

      ![Adicionar um gráfico de linhas para a temperatura a um relatório do Microsoft Power BI](media/iot-hub-live-data-visualization-in-power-bi/8_add-line-chart-for-temperature-to-power-bi-report-microsoft.png)

1. Crie outro gráfico de linhas para mostrar umidade em tempo real ao longo do tempo. Para fazer isso, siga as mesmas etapas acima e coloque **EventEnqueuedUtcTime** no eixo x e **umidade** no eixo y.

   ![Adicionar um gráfico de linhas para umidade a um relatório do Microsoft Power BI](media/iot-hub-live-data-visualization-in-power-bi/9_add-line-chart-for-humidity-to-power-bi-report-microsoft.png)

1. Clique em **Salvar** para salvar o relatório.
1. Clique em **Arquivo** > **Publicar na Web**.
1. Clique em **Criar código incorporado** e clique em **Publicar**.

São fornecidas no link do relatório que você pode compartilhar com ninguém para acesso a relatórios e um trecho de código para integrar o relatório em seu blog ou site.

![Publicar um relatório do Microsoft Power BI](media/iot-hub-live-data-visualization-in-power-bi/10_publish-power-bi-report-microsoft.png)

A Microsoft também oferece o [aplicativos móveis do Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) para exibir e interagir com seus relatórios e painéis do Power BI em seu dispositivo móvel.

## <a name="next-steps"></a>Próximas etapas

Você usou com êxito o Power BI para visualizar dados do sensor em tempo real do seu Hub IoT do Azure.
Há uma maneira alternativa para visualizar dados do Hub IoT do Azure. Veja [Usar Aplicativos Web do Azure para visualizar dados de sensor em tempo real do Hub IoT do Azure](iot-hub-live-data-visualization-in-web-apps.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

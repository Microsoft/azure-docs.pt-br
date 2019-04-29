---
title: Visualização de dados em tempo real de dados de sensor do Hub IoT do Azure – Power BI | Microsoft Docs
description: Use o Power BI para visualizar dados de temperatura e umidade que são coletados do sensor e enviados para o Hub IoT do Azure.
author: robinsh
keywords: visualização de dados em tempo real, visualização de dados dinâmicos, visualização de dados de sensor
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 4/11/2018
ms.author: robinsh
ms.openlocfilehash: 7c770aced36e4c90f654de8d31c12d55ad80c8d0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60779943"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Visualizar dados de sensor em tempo real do Hub IoT usando o Power BI

![Diagrama de ponta a ponta](./media/iot-hub-live-data-visualization-in-power-bi/1_end-to-end-diagram.png)


[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>O que você aprenderá

Você aprenderá a visualizar dados do sensor em tempo real que recebe o hub IoT do Azure usando o Power BI. Se você quiser tentar visualizar os dados em seu Hub IoT com Aplicativos Web, consulte [Usar Aplicativos Web do Azure para visualizar dados de sensor em tempo real do Hub IoT do Azure](iot-hub-live-data-visualization-in-web-apps.md).

## <a name="what-you-do"></a>O que fazer

* Preparar seu Hub IoT para acesso a dados, adicionando um grupo de consumidores.

* Criar, configurar e executar um trabalho do Stream Analytics para transferência de dados do seu hub IoT à sua conta do Power BI.

* Crie e publique um relatório do Power BI para visualizar os dados.

## <a name="what-you-need"></a>O que você precisa

* Conclua o [simulador online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) tutorial ou um dos tutoriais dispositivo; por exemplo, [Raspberry Pi com Node. js](iot-hub-raspberry-pi-kit-node-get-started.md). Estes abrangem os seguintes requisitos:
  
  * Uma assinatura ativa do Azure.
  * Um hub IoT do Azure em sua assinatura.
  * O aplicativo cliente que envia mensagens para o hub IoT do Azure.

* Uma conta do Power BI. ([Experimente gratuitamente o Power BI](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Criar, configurar e executar um trabalho do Stream Analytics

Vamos começar criando um trabalho do Stream Analytics. Depois de criar o trabalho, você deve definir as entradas, saídas e a consulta usada para recuperar os dados.

### <a name="create-a-stream-analytics-job"></a>Criar um trabalho de Stream Analytics

1. No [Portal do Azure](https://portal.azure.com), clique em **Criar um recurso** > **Internet das Coisas** > **Trabalho do Stream Analytics**.

2. Insira as seguintes informações para o trabalho.

   **Nome do trabalho**: O nome do trabalho. O nome deve ser globalmente exclusivo.

   **Grupo de recursos**: Use o mesmo grupo de recursos usado pelo hub IoT.

   **Localização**: Use o mesmo local do grupo de recursos.

   **Fixar no painel**: Marque essa opção para facilitar o acesso ao hub IoT no painel.

   ![Criar um trabalho do Stream Analytics no Azure](./media/iot-hub-live-data-visualization-in-power-bi/2_create-stream-analytics-job-azure.png)

3. Clique em **Criar**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Adicionar uma entrada ao trabalho do Stream Analytics

1. Abra o trabalho do Stream Analytics.

2. Em **Topologia do Trabalho**, clique em **Entradas**.

3. No painel **Entradas**, clique em **Adicionar entrada de fluxo** e, em seguida, insira as seguintes informações:

   **Alias de entrada**: O alias exclusivo da entrada; selecione **Fornecer configurações do Hub IoT manualmente** abaixo.

   **Fonte**: Selecione **Hub IoT**.
   
   **Ponto de extremidade**: Clique em **Mensagens**.

   **Grupo de consumidores**: Selecione o grupo de consumidores recém-criado.

4. Clique em **Criar**.

   ![Adicionar uma entrada a um trabalho do Stream Analytics no Azure](./media/iot-hub-live-data-visualization-in-power-bi/3_add-input-to-stream-analytics-job-azure.png)

### <a name="add-an-output-to-the-stream-analytics-job"></a>Adicionar uma saída ao trabalho do Stream Analytics

1. Em **Topologia do Trabalho**, clique em **Saídas**.

2. No painel **Saídas**, clique em **Adicionar** e em **Power BI** e, em seguida, insira as seguintes informações:

   **Alias de saída**: o alias exclusivo para a saída.

   **Workspace de Grupo**: Selecione o workspace do grupo de destino.

   **Nome do conjunto de dados**: Insira um nome de conjunto de dados.

   **Nome da tabela**: Insira um nome de tabela.

3. Clique em **Autorizar** e depois entre na sua conta do Power BI.

4. Clique em **Criar**.

   ![Adicionar uma saída a um trabalho do Stream Analytics no Azure](./media/iot-hub-live-data-visualization-in-power-bi/4_add-output-to-stream-analytics-job-azure.png)

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configurar a consulta do trabalho do Stream Analytics

1. Em **Topologia do Trabalho**, clique em **Consulta**.

2. Substitua `[YourInputAlias]` pelo alias de entrada do trabalho.

3. Substitua `[YourOutputAlias]` pelo alias de saída do trabalho.

4. Clique em **Salvar**.

   ![Adicionar uma consulta a um trabalho do Stream Analytics no Azure](./media/iot-hub-live-data-visualization-in-power-bi/5_add-query-stream-analytics-job-azure.png)

### <a name="run-the-stream-analytics-job"></a>Executar o trabalho do Stream Analytics

No trabalho do Stream Analytics, clique em **Iniciar** > **Agora** > **Iniciar**. Depois que o trabalho é iniciado com êxito, o status do trabalho muda de **parado** para **executando**.

![Executar um trabalho do Stream Analytics no Azure](./media/iot-hub-live-data-visualization-in-power-bi/6_run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Criar e publicar um relatório do Power BI para visualizar os dados

1. Verifique se o aplicativo de exemplo está em execução em seu dispositivo. Se não, você pode consultar os tutoriais em [Configure seu dispositivo](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started).

2. Entre na sua conta do [Power BI](https://powerbi.microsoft.com/en-us/).

3. Clique no workspace usado, **Meu Workspace**.

4. Clique em **Conjuntos de dados**.

   Você deverá ver o conjunto de dados especificado quando você criou a saída para o trabalho do Stream Analytics.

5. Para obter o conjunto de dados criado, clique em **Adicionar Relatório** (o primeiro ícone à direita do nome do conjunto de dados).

   ![Criar um relatório do Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/7_create-power-bi-report-microsoft.png)

6. Crie um gráfico de linhas para mostrar a temperatura em tempo real ao longo do tempo.

   1. Na página de criação de relatório, adicione um gráfico de linhas.

   2. Sobre o **campos** painel, expanda a tabela que você especificou quando criou a saída para o trabalho do Stream Analytics.
   
   3. Arraste **EventEnqueuedUtcTime** para **eixo** sobre o **visualizações** painel.
   
   4. Arraste **temperatura** para **Valores**.

      Um gráfico de linhas é criado. O eixo x exibe a data e a hora no fuso horário UTC. O eixo y mostra a temperatura do sensor.

      ![Adicionar um gráfico de linhas para a temperatura a um relatório do Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/8_add-line-chart-for-temperature-to-power-bi-report-microsoft.png)

7. Crie outro gráfico de linhas para mostrar umidade em tempo real ao longo do tempo. Para fazer isso, siga as mesmas etapas acima e coloque **EventEnqueuedUtcTime** no eixo x e **umidade** no eixo y.

   ![Adicionar um gráfico de linhas para umidade a um relatório do Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/9_add-line-chart-for-humidity-to-power-bi-report-microsoft.png)

8. Clique em **Salvar** para salvar o relatório.

9. Clique em **Relatórios** no painel esquerdo e, em seguida, clique no relatório recém-criado.

10. Clique em **Arquivo** > **Publicar na Web**.

11. Clique em **Criar código incorporado** e clique em **Publicar**.

São fornecidas no link do relatório que você pode compartilhar com ninguém para acesso a relatórios e um snippet de código para integrar o relatório em seu blog ou site.

![Publicar um relatório do Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/10_publish-power-bi-report-microsoft.png)

A Microsoft também oferece o [aplicativos móveis do Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) para exibir e interagir com seus relatórios e painéis do Power BI em seu dispositivo móvel.

## <a name="next-steps"></a>Próximas etapas

Você usou com êxito o Power BI para visualizar dados do sensor em tempo real do seu Hub IoT do Azure.

Há uma maneira alternativa para visualizar dados do Hub IoT do Azure. Veja [Usar Aplicativos Web do Azure para visualizar dados de sensor em tempo real do Hub IoT do Azure](iot-hub-live-data-visualization-in-web-apps.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

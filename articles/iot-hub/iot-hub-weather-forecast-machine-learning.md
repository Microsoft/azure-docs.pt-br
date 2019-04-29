---
title: Previsão do tempo usando o Azure Machine Learning com os dados do Hub IoT | Microsoft Docs
description: Use o Azure Machine Learning para prever a possibilidade de chuva com base nos dados de temperatura e umidade coletados pelo Hub IoT de um sensor.
author: robinsh
manager: philmea
keywords: Machine Learning de previsão do tempo
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: robinsh
ms.openlocfilehash: ffc2e5fb588ce6861f5df6cefdf810c1a015c043
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61440884"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning"></a>Previsão do tempo usando os dados do sensor do Hub IoT no Azure Machine Learning

![Diagrama de ponta a ponta](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

O Machine Learning é uma técnica da ciência de dados que ajuda os computadores a aprenderem com os dados existentes para prever tendências, resultados e comportamentos futuros. O Azure Machine Learning é um serviço de análise preditiva na nuvem que permite criar rapidamente modelos preditivos e implantá-los como soluções de análise.

## <a name="what-you-learn"></a>O que você aprenderá

Saiba como usar o Azure Machine Learning para fazer uma previsão do tempo (possibilidade de chuva) usando os dados de temperatura e umidade do Hub IoT do Azure. A possibilidade de chuva é o resultado de um modelo de previsão de clima preparado. O modelo se baseia em dados históricos para prever a possibilidade de chuva com base na temperatura e na umidade.

## <a name="what-you-do"></a>O que fazer

- Implante o modelo de previsão do tempo como um serviço Web.
- Preparar seu Hub IoT para acesso a dados, adicionando um grupo de consumidores.
- Crie um trabalho do Stream Analytics e configure o trabalho para:
  - Leia dados de temperatura e umidade no Hub IoT.
  - Chame o serviço Web para obter a possibilidade de chuva.
  - Salve o resultado em um armazenamento de blobs do Azure.
- Use o Gerenciador de Armazenamento do Microsoft Azure para exibir a previsão do tempo.

## <a name="what-you-need"></a>O que você precisa

- Conclua o [simulador online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) tutorial ou um dos tutoriais dispositivo; por exemplo, [Raspberry Pi com Node. js](iot-hub-raspberry-pi-kit-node-get-started.md). Estes abrangem os seguintes requisitos:
  - Uma assinatura ativa do Azure.
  - Um hub IoT do Azure em sua assinatura.
  - O aplicativo cliente que envia mensagens para o hub IoT do Azure.
- Uma conta do Azure Machine Learning Studio. ([Experimente o Machine Learning Studio gratuitamente](https://studio.azureml.net/)).

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Implantar o modelo de previsão do tempo como um serviço Web

1. Acesse a [página do modelo de previsão do tempo](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1).
1. Clique em **Abrir no Studio** no Microsoft Azure Machine Learning Studio.
   ![Abrir a página do modelo de previsão do tempo na Galeria do Cortana Intelligence](media/iot-hub-weather-forecast-machine-learning/2_weather-prediction-model-in-cortana-intelligence-gallery.png)
1. Clique em **Executar** para validar as etapas no modelo. Esta etapa pode levar 2 minutos para ser concluída.
   ![Abrir o modelo de previsão do tempo no Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/3_open-weather-prediction-model-in-azure-machine-learning-studio.png)
1. Clique em **CONFIGURAR SERVIÇO WEB** > **Serviço Web Preditivo**.
   ![Implantar o modelo de previsão do tempo no Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/4-deploy-weather-prediction-model-in-azure-machine-learning-studio.png)
1. No diagrama, arraste o módulo **entrada do serviço Web** em algum lugar próximo ao módulo **Modelo de Pontuação**.
1. Conecte o módulo **entrada do serviço Web** ao módulo **Modelo de Pontuação**.
   ![Conectar dois módulos no Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/13_connect-modules-azure-machine-learning-studio.png)
1. Clique em **EXECUTAR** para validar as etapas no modelo.
1. Clique em **IMPLANTAR SERVIÇO WEB** para implantar o modelo como um serviço Web.
1. No painel do modelo, baixe o **Excel 2010 ou a pasta de trabalho anterior** de **SOLICITAÇÃO/RESPOSTA**.

   > [!Note]
   > Lembre-se de baixar o **Excel 2010 ou a pasta de trabalho anterior** mesmo se estiver executando uma versão posterior do Excel no computador.

   ![Baixar o Excel para obter o ponto de extremidade SOLICITAÇÃO/RESPOSTA](media/iot-hub-weather-forecast-machine-learning/5_download-endpoint-app-excel-for-request-response.png)

1. Abra a pasta de trabalho do Excel, anote a **URL DO SERVIÇO WEB** e a **TECLA DE ACESSO**.

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Criar, configurar e executar um trabalho do Stream Analytics

### <a name="create-a-stream-analytics-job"></a>Criar um trabalho de Stream Analytics

1. No [Portal do Azure](https://portal.azure.com/), clique em **Criar um recurso** > **Internet das Coisas** > **Trabalho do Stream Analytics**.
1. Insira as seguintes informações para o trabalho.

   **Nome do trabalho**: O nome do trabalho. O nome deve ser globalmente exclusivo.

   **Grupo de recursos**: Use o mesmo grupo de recursos usado pelo hub IoT.

   **Localização**: Use o mesmo local do grupo de recursos.

   **Fixar no painel**: Marque essa opção para facilitar o acesso ao hub IoT no painel.

   ![Criar um trabalho do Stream Analytics no Azure](media/iot-hub-weather-forecast-machine-learning/7_create-stream-analytics-job-azure.png)

1. Clique em **Criar**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Adicionar uma entrada ao trabalho do Stream Analytics

1. Abra o trabalho do Stream Analytics.
1. Em **Topologia do Trabalho**, clique em **Entradas**.
1. No **entradas** painel, clique em **adicionar**e, em seguida, insira as seguintes informações:

   **Alias de entrada**: O alias exclusivo para a entrada.

   **Fonte**: Selecione **Hub IoT**.

   **Grupo de consumidores**: Selecione o grupo de consumidores que você criou.

   ![Adicionar uma entrada ao trabalho do Stream Analytics no Azure](media/iot-hub-weather-forecast-machine-learning/8_add-input-stream-analytics-job-azure.png)

1. Clique em **Criar**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Adicionar uma saída ao trabalho do Stream Analytics

1. Em **Topologia do Trabalho**, clique em **Saídas**.
1. No **saídas** painel, clique em **adicionar**e, em seguida, insira as seguintes informações:

   **Alias de saída**: o alias exclusivo para a saída.

   **Coletor**: Selecione **armazenamento de BLOBs**.

   **Conta de armazenamento**: A conta de armazenamento para seu armazenamento de BLOBs. Você pode criar uma conta de armazenamento ou usar uma existente.

   **Contêiner**: O contêiner em que o blob foi salvo. Você pode criar um contêiner ou usar um existente.

   **Formato de serialização de evento**: Selecione **CSV**.

   ![Adicionar uma saída ao trabalho do Stream Analytics no Azure](media/iot-hub-weather-forecast-machine-learning/9_add-output-stream-analytics-job-azure.png)

1. Clique em **Criar**.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Adicionar uma função ao trabalho do Stream Analytics para chamar o serviço Web implantado

1. Em **Topologia de Trabalho**, clique em **Funções** > **Adicionar**.
1. Insira as seguintes informações:

   **Alias da função**: Digite `machinelearning`.

   **Tipo de função**: Selecione **Azure ML**.

   **Opção de importação**: Selecione **importação de uma assinatura diferente**.

   **URL**: Insira a URL do serviço WEB que você anotou da pasta de trabalho do Excel.

   **Chave**: Insira a chave de acesso que você anotou da pasta de trabalho do Excel.

   ![Adicionar uma função ao trabalho do Stream Analytics no Azure](media/iot-hub-weather-forecast-machine-learning/10_add-function-stream-analytics-job-azure.png)

1. Clique em **Criar**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configurar a consulta do trabalho do Stream Analytics

1. Em **Topologia do Trabalho**, clique em **Consulta**.
1. Substitua o código existente pelo seguinte código:

   ```sql
   WITH machinelearning AS (
      SELECT EventEnqueuedUtcTime, temperature, humidity, machinelearning(temperature, humidity) as result from [YourInputAlias]
   )
   Select System.Timestamp time, CAST (result.[temperature] AS FLOAT) AS temperature, CAST (result.[humidity] AS FLOAT) AS humidity, CAST (result.[Scored Probabilities] AS FLOAT ) AS 'probabalities of rain'
   Into [YourOutputAlias]
   From machinelearning
   ```

   Substitua `[YourInputAlias]` pelo alias de entrada do trabalho.

   Substitua `[YourOutputAlias]` pelo alias de saída do trabalho.

1. Clique em **Salvar**.

### <a name="run-the-stream-analytics-job"></a>Executar o trabalho do Stream Analytics

No trabalho do Stream Analytics, clique em **Iniciar** > **Agora** > **Iniciar**. Depois que o trabalho é iniciado com êxito, o status do trabalho muda de **parado** para **executando**.

![Executar o trabalho do Stream Analytics](media/iot-hub-weather-forecast-machine-learning/11_run-stream-analytics-job-azure.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Usar o Gerenciador de Armazenamento do Microsoft Azure para exibir a previsão do tempo

Execute o aplicativo cliente para iniciar a coleta e o envio de dados de temperatura e umidade para o Hub IoT. Para cada mensagem recebida pelo Hub IoT, o trabalho do Stream Analytics chama o serviço Web de previsão do tempo para produzir a possibilidade de chuva. O resultado é então salvo no armazenamento de blobs do Azure. O Gerenciador de Armazenamento do Azure é uma ferramenta que pode ser usada para exibir o resultado.

1. [Baixe e instale o Gerenciador de Armazenamento do Microsoft Azure](https://storageexplorer.com/).
1. Abra o Gerenciador de Armazenamento do Azure.
1. Entre na sua conta do Azure.
1. Selecione sua assinatura.
1. Clique em sua assinatura > **Contas de Armazenamento** > sua conta de armazenamento > **Contêineres de Blobs** > seu contêiner.
1. Abra um arquivo .csv para ver o resultado. A última coluna registra a possibilidade de chuva.

   ![Obter resultados da previsão do tempo com o Azure Machine Learning](media/iot-hub-weather-forecast-machine-learning/12_get-weather-forecast-result-azure-machine-learning.png)

## <a name="summary"></a>Resumo

Você usou bem o Azure Machine Learning para produzir a possibilidade de chuva com base nos dados de temperatura e umidade recebidos pelo Hub IoT.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
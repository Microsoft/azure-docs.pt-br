---
title: Integrar a solução de Monitoramento Remoto com o Data Lake Store - Azure | Microsoft Docs
description: Saiba como integrar a solução de Monitoramento Remoto com o Azure Data Lake Store usando um trabalho do Azure Stream Analytics.
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 021f18f588613817110539d408f9260fb9247895
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61449217"
---
# <a name="integrate-the-remote-monitoring-solution-with-azure-data-lake-store"></a>Integrar a solução de Monitoramento Remoto com o Azure Data Lake Store

Talvez você tenha necessidades de análise avançada além do que é oferecido na solução de Monitoramento Remoto. O Azure Data Lake Store é ideal para esse aplicativo, pois pode armazenar dados de conjuntos de dados grandes e diversos, bem como integrar com o Azure Data Lake Analytics a fim de fornecer análise sob demanda.

Neste manual, você usará um trabalho do Azure Stream Analytics para transmitir dados do hub IoT em sua solução de Monitoramento Remoto para um Azure Data Lake Store.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir estas instruções, você precisará do seguinte:

* [Implantar o acelerador da solução de monitoramento remoto](quickstart-remote-monitoring-deploy.md).
  * A solução de Monitoramento Remoto implantará o hub IoT e o trabalho do Azure Stream Analytics usado neste artigo em sua assinatura do Azure.
* [Implantar um Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md)
  * O Data Lake Store deve ser implantado na mesma região que sua solução de Monitoramento Remoto.
  * [Crie uma pasta](../data-lake-store/data-lake-store-get-started-portal.md#createfolder) chamada "streaming" em sua conta.

## <a name="create-a-consumer-group"></a>Criar um grupo de consumidores

Crie um grupo de consumidores dedicados no hub IoT de sua solução de Monitoramento Remoto. Isso será usado pelo trabalho do Stream Analytics para transmissão de dados para seu Data Lake Store.

> [!NOTE]
> Os grupos de consumidores são utilizados pelas aplicações para obter dados do Hub IoT do Azure. Você deve criar um novo grupo de consumidores para cada cinco consumidores. Crie grupos de até 32 consumidores.

1. Entre no Portal do Azure.

1. No Portal do Azure, clique no botão do **Cloud Shell**.

    ![Ícone de Inicialização do Portal](./media/iot-accelerators-integrate-data-lake/portal-launch-icon.png)

1. Execute este comando para criar um novo grupo de consumidores:

```azurecli-interactive
az iot hub consumer-group create --hub-name contoso-rm30263 --name streamanalyticsjob --resource-group contoso-rm
```

> [!NOTE]
> Use os nomes de grupo de recursos e hub IoT de sua solução de Monitoramento Remoto.

## <a name="create-stream-analytics-job"></a>Criar um trabalho do Stream Analytics

Crie um trabalho do Azure Stream Analytics para transmitir os dados de seu hub IoT para o Azure Data Lake Store.

1. Clique em **Criar um recurso**, selecione Internet das Coisas no Marketplace e clique em **Trabalho do Stream Analytics**.

    ![Novo trabalho do Stream Analytics](./media/iot-accelerators-integrate-data-lake/new-stream-analytics-job.png)

1. Insira um nome de trabalho e selecione a assinatura e o grupo de recursos apropriados.

1. Selecione um Local na mesma região do Data Lake Store, ou perto dela. Estamos usando Leste dos EUA.

1. Deixe o ambiente de hospedagem com o padrão de **Nuvem**.

1. Clique em **Criar**.

    ![Criar um trabalho do Stream Analytics](./media/iot-accelerators-integrate-data-lake/create-stream-analytics-job.png)

## <a name="configure-the-stream-analytics-job"></a>Configurar o trabalho do Stream Analytics

1. Acesse o **trabalho do Stream Analytics** em seu grupo de recursos de solução de Monitoramento Remoto.

1. Na página Visão geral, clique em **Entradas**.

    ![Página de visão geral](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview.png)

1. Clique em **Adicionar entrada de fluxo** e selecione o **Hub IoT**.

    ![Adicionar entrada](./media/iot-accelerators-integrate-data-lake/stream-analytics-add-input.png)

1. Na guia Nova entrada, insira um alias de entrada de **IoTHub**.

1. Na lista suspensa Grupo de consumidores, selecione o grupo de consumidores criado anteriormente. Aqui, estamos usando **streamanalyticsjob**.

    ![Selecione a entrada](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-input.png)

1. Clique em **Salvar**.

1. Na página Visão geral, clique em **Saídas**.

    ![Adicionar o Data Lake Store](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview-2.png)

1. Clique em **Adicionar** e selecione **Data Lake Store** na lista suspensa.

    ![Adicionar saída](./media/iot-accelerators-integrate-data-lake/stream-analytics-output.png)

1. Na guia Nova saída, insira um alias de saída de **DataLakeStore**.

1. Selecione a conta do Data Lake Store que você criou nas etapas anteriores e forneça a estrutura de pastas para transmitir dados para o armazenamento.

1. No campo Formato de data, insira **/streaming/ {date}/{time}**. Deixe o formato de Data padrão de AAAA/MM/DD e de hora de HH.

    ![Fornecer a estrutura de pastas](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-output.png)

1. Clique em **Autorizar**.

    Será necessário autorizar com o Data Lake Store para conceder acesso de gravação ao trabalho do Stream Analytics para o sistema de arquivos.

    ![Autorizar o Stream Analytics no Data Lake Store](./media/iot-accelerators-integrate-data-lake/stream-analytics-out-authorize.png)

    Você verá um pop-up e, assim que o fechar, o botão Autorizar ficará esmaecido após a conclusão da autorização.

    > [!NOTE]
    > Se você vir um erro na janela pop-up, abra uma nova janela de navegador no Modo Anônimo e tente novamente.

1. Clique em **Salvar**.

## <a name="edit-the-stream-analytics-query"></a>Editar a consulta do Stream Analytics

O Azure Stream Analytics usa uma linguagem de consulta do tipo SQL para especificar uma fonte de entrada que transmite dados, transformar esses dados conforme o desejado e pode gerar uma saída para vários destinos de armazenamento ou processamento.

1. Na guia Visão Geral, clique em **Editar consulta**.

    ![Editar Consulta](./media/iot-accelerators-integrate-data-lake/stream-analytics-edit-query.png)

1. No Editor de consultas, substitua os espaços reservados [YourOutputAlias] e [YourInputAlias] pelos valores que você definiu anteriormente.

    ```sql
    SELECT
        *, System.Timestamp as time
    INTO
        DataLakeStore
    FROM
        IoTHub
    ```

    ![Consulta do Stream Analytics](./media/iot-accelerators-integrate-data-lake/stream-analytics-query.png)

1. Clique em **Salvar**.
1. Clique em **Sim** para aceitar as alterações.

## <a name="start-the-stream-analytics-job"></a>Iniciar o trabalho do Stream Analytics

1. Na guia Visão Geral, clique em **Iniciar**.

    ![Iniciar trabalho do Stream Analytics](./media/iot-accelerators-integrate-data-lake/stream-analytics-start.png)

1. Na guia Iniciar trabalho, clique em **Personalizado**.

1. Defina uma hora personalizada a fim de retornar algumas horas para selecionar os dados do início do streaming do seu dispositivo.

1. Clique em **Iniciar**.

    ![Escolha Data Personalizada](./media/iot-accelerators-integrate-data-lake/stream-analytics-start-custom.png)

    Aguarde até que o trabalho entre em execução. Se você encontrar erros, pode ser devido à sua consulta. Verifique se a sintaxe está correta.

    ![Trabalho em execução](./media/iot-accelerators-integrate-data-lake/stream-analytics-running.png)

    O trabalho de streaming começará a ler dados de seu Hub IoT e a armazenar os dados em seu Data Lake Store. Talvez demore alguns minutos para que os dados comecem a aparecer em seu Data Lake Store.

## <a name="explore-the-streaming-data"></a>Explorar os dados de streaming

1. Acesse seu Data Lake Store.

1. Na guia Visão Geral, clique em **Data Explorer**.

1. No Data Explorer, faça drill down até a pasta **/streaming**. Você verá as pastas criadas com o formato AAAA/MM/DD/HH.

    ![Explorar os dados de streaming](./media/iot-accelerators-integrate-data-lake/data-lake-store-data-explorer.png)

    Você verá os arquivos json com um arquivo por hora.

    ![Explorar os dados de streaming](./media/iot-accelerators-integrate-data-lake/data-lake-store-file-preview.png)

## <a name="next-steps"></a>Próximas etapas

O Azure Data Lake Analytics pode ser usado para executar análise de big data em seus conjuntos de dados do Data Lake Store. Saiba mais na [Documentação do Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics).

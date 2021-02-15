---
title: Visualização de dados em tempo real do Hub IoT do Azure de frm de dados – Power BI
description: Use o Power BI para visualizar dados de temperatura e umidade que são coletados do sensor e enviados para o Hub IoT do Azure.
author: robinsh
keywords: visualização de dados em tempo real, visualização de dados dinâmicos, visualização de dados de sensor
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 6/08/2020
ms.author: robinsh
ms.openlocfilehash: 6d10d0202e9e4c813cffe6373acfb5200ebb3266
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92146782"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Visualizar dados de sensor em tempo real do Hub IoT usando o Power BI

![Diagrama de ponta a ponta](./media/iot-hub-live-data-visualization-in-power-bi/end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>O que você aprenderá

Você aprenderá a visualizar dados do sensor em tempo real que recebe o hub IoT do Azure usando o Power BI. Se você quiser tentar visualizar os dados em seu hub IoT com um aplicativo Web, consulte [usar um aplicativo Web para visualizar dados de sensor em tempo real do Hub IOT do Azure](iot-hub-live-data-visualization-in-web-apps.md).

## <a name="what-you-do"></a>O que fazer

* Preparar seu Hub IoT para acesso a dados, adicionando um grupo de consumidores.

* Criar, configurar e executar um trabalho do Stream Analytics para transferência de dados do seu hub IoT à sua conta do Power BI.

* Crie e publique um relatório do Power BI para visualizar os dados.

## <a name="what-you-need"></a>O que você precisa

* Conclua o tutorial do [simulador online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) ou um dos tutoriais do dispositivo; por exemplo, [Raspberry Pi com Node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Esses artigos abordam os seguintes requisitos:
  
  * Uma assinatura ativa do Azure.
  * Um hub IoT do Azure em sua assinatura.
  * O aplicativo cliente que envia mensagens para o hub IoT do Azure.

* Uma conta do Power BI. ([Tente Power bi gratuitamente](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Criar, configurar e executar um trabalho do Stream Analytics

Vamos começar criando um trabalho do Stream Analytics. Depois de criar o trabalho, você deve definir as entradas, saídas e a consulta usada para recuperar os dados.

### <a name="create-a-stream-analytics-job"></a>Criar um trabalho de Stream Analytics

1. No [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Internet das Coisas** > **Trabalho do Stream Analytics**.

2. Insira as seguintes informações para o trabalho.

   **Nome do trabalho**: O nome do trabalho. O nome deve ser globalmente exclusivo.

   **Grupo de recursos**: Use o mesmo grupo de recursos usado pelo hub IoT.

   **Localização**: Use o mesmo local do grupo de recursos.

   ![Criar um trabalho do Stream Analytics no Azure](./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job.png)

3. Selecione **Criar**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Adicionar uma entrada ao trabalho do Stream Analytics

1. Abra o trabalho do Stream Analytics.

2. Em **Topologia do trabalho**, selecione **Entradas**.

3. No painel **entradas** , selecione **Adicionar entrada de fluxo**e, em seguida, selecione **Hub IOT** na lista suspensa. No painel novo entrada, insira as seguintes informações:

   **Alias de entrada**: Insira um alias exclusivo para a entrada.

   **Selecione Hub IOT em sua assinatura**: Selecione este botão de opção.

   **Assinatura**: Selecione a assinatura do Azure que você está usando para este tutorial.

   **Hub IOT**: selecione o Hub IOT que você está usando para este tutorial.

   **Ponto de extremidade**: selecione **Mensagens**.

   **Nome da política de acesso compartilhado**: selecione o nome da política de acesso compartilhado que você deseja que o trabalho de Stream Analytics use para o Hub IOT. Para este tutorial, você pode selecionar *serviço*. A política de *serviço* é criada por padrão em novos hubs IOT e concede permissão para enviar e receber em pontos de extremidade do lado da nuvem expostos pelo Hub IOT. Para saber mais, consulte [permissões e controle de acesso](iot-hub-devguide-security.md#access-control-and-permissions).

   **Chave de política de acesso compartilhado**: esse campo é preenchido automaticamente com base na sua seleção para o nome da política de acesso compartilhado.

   **Grupo de consumidores**: selecione o grupo de consumidores que você criou anteriormente.

   Deixe todos os outros campos em seus padrões.

   ![Adicionar uma entrada a um trabalho do Stream Analytics no Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job.png)

4. Clique em **Salvar**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Adicionar uma saída ao trabalho do Stream Analytics

1. Em **Topologia do trabalho**, selecione **Saídas**.

2. No painel **saídas** , selecione **Adicionar** e **Power bi**.

3. No painel **Power bi-nova saída** , selecione **autorizar** e siga os prompts para entrar em sua conta do Power bi.

4. Depois de entrar no Power BI, insira as seguintes informações:

   **Alias de saída**: um alias exclusivo para a saída.

   **Espaço de trabalho de grupo**: Selecione seu espaço de trabalho do grupo de destino.

   **Nome do conjunto**de um: Insira um nome de conjunto de um.

   **Nome da tabela**: Insira um nome de tabela.

   **Modo de autenticação**: deixe no padrão.

   ![Adicionar uma saída a um trabalho do Stream Analytics no Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job.png)

5. Clique em **Salvar**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configurar a consulta do trabalho do Stream Analytics

1. Em **Topologia do trabalho**, selecione **Consulta**.

2. Substitua `[YourInputAlias]` pelo alias de entrada do trabalho.

3. Substitua `[YourOutputAlias]` pelo alias de saída do trabalho.

   ![Adicionar uma consulta a um trabalho do Stream Analytics no Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-query-to-stream-analytics-job.png)

4. Selecione **Salvar consulta**.

### <a name="run-the-stream-analytics-job"></a>Executar o trabalho do Stream Analytics

No trabalho Stream Analytics, selecione **visão geral**e, em seguida, selecione **Iniciar**  >  **agora**  >  **Iniciar**. Depois que o trabalho é iniciado com êxito, o status do trabalho muda de **parado** para **executando**.

![Executar um trabalho do Stream Analytics no Azure](./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Criar e publicar um relatório do Power BI para visualizar os dados

As etapas a seguir mostram como criar e publicar um relatório usando o serviço do Power BI. Você pode seguir estas etapas, com algumas modificações, se quiser usar a "nova aparência" em Power BI. Para entender as diferenças e como navegar na "nova aparência", consulte [a ' nova aparência ' do serviço do Power bi](/power-bi/consumer/service-new-look).

1. Verifique se o aplicativo de exemplo está em execução em seu dispositivo. Se não, você pode consultar os tutoriais em [Configure seu dispositivo](./iot-hub-raspberry-pi-kit-node-get-started.md).

2. Entre na sua conta do [Power BI](https://powerbi.microsoft.com/en-us/).

3. Selecione o espaço de trabalho usado, **meu espaço de trabalho**.

4. Selecione **Conjuntos de dados**.

   Você deverá ver o conjunto de dados especificado quando você criou a saída para o trabalho do Stream Analytics.

5. Para o conjunto de um que você criou, selecione **Adicionar relatório** (o primeiro ícone à direita do nome do conjunto de um).

   ![Criar um relatório do Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-create-report.png)

6. Crie um gráfico de linhas para mostrar a temperatura em tempo real ao longo do tempo.

   1. No painel **visualizações** da página de criação de relatório, selecione o ícone de gráfico de linhas para adicionar um gráfico de linhas.

   2. Sobre o **campos** painel, expanda a tabela que você especificou quando criou a saída para o trabalho do Stream Analytics.

   3. Arraste **EventEnqueuedUtcTime** para **eixo** sobre o **visualizações** painel.

   4. Arraste **temperatura** para **Valores**.

      Um gráfico de linhas é criado. O eixo x exibe a data e a hora no fuso horário UTC. O eixo y mostra a temperatura do sensor.

      ![Adicionar um gráfico de linhas para a temperatura a um relatório do Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temperature.png)

7. Crie outro gráfico de linhas para mostrar umidade em tempo real ao longo do tempo. Para fazer isso, clique em uma parte em branco da tela e siga as mesmas etapas acima para inserir **EventEnqueuedUtcTime** no eixo x e **umidade** no eixo y.

   ![Adicionar um gráfico de linhas para umidade a um relatório do Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-humidity.png)

8. Selecione **Salvar** para salvar o relatório.

9. Selecione **relatórios** no painel esquerdo e, em seguida, selecione o relatório que você acabou de criar.

10. Selecione **arquivo**  >  **publicar na Web**.

    ![Selecione publicar na Web para o relatório do Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-select-publish-to-web.png)

    > [!NOTE]
    > Se você receber uma notificação para entrar em contato com o administrador para habilitar a criação de código de inserção, talvez seja necessário contatá-las. A criação de código de inserção deve ser habilitada para que você possa concluir esta etapa.
    >
    > ![Contate a notificação do administrador](./media/iot-hub-live-data-visualization-in-power-bi/contact-admin.png)

11. Selecione **criar código de inserção**e, em seguida, selecione **publicar**.

Você forneceu o link de relatório que pode compartilhar com qualquer pessoa para acesso de relatório e um trecho de código que você pode usar para integrar o relatório em seu blog ou site.

![Publicar um relatório do Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-web-output.png)

A Microsoft também oferece o [aplicativos móveis do Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) para exibir e interagir com seus relatórios e painéis do Power BI em seu dispositivo móvel.

## <a name="next-steps"></a>Próximas etapas

Você usou com êxito o Power BI para visualizar dados do sensor em tempo real do seu Hub IoT do Azure.

Para outra maneira de Visualizar dados do Hub IoT do Azure, consulte [usar um aplicativo Web para visualizar dados de sensor em tempo real do Hub IOT do Azure](iot-hub-live-data-visualization-in-web-apps.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
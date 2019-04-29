---
title: Integração do painel de controle do Power BI com o Azure Stream Analytics
description: Este artigo descreve como usar um painel do Power BI em tempo real para visualizar dados de um trabalho do Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 487c142400dc2bfa6f44e17963535051af017196
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60818019"
---
# <a name="tutorial-stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Tutorial: Stream Analytics e Power BI: Um painel de análise em tempo real para dados de streaming
O Stream Analytics do Azure permite aproveitar uma das principais ferramentas de business intelligence, o [Microsoft Power BI](https://powerbi.com/). Neste artigo, você saberá como criar ferramentas de business intelligence usando o Power BI como uma saída de seus trabalhos do Stream Analytics do Azure. Você também aprenderá a criar e usar um painel em tempo real.

Este artigo continua no tutorial [Detecção de fraude em tempo real](stream-analytics-real-time-fraud-detection.md) do Stream Analytics. Ele amplia o fluxo de trabalho criado neste tutorial e adiciona uma saída do Power BI para que você pode visualizar chamadas telefônicas fraudulentas que são detectadas por um trabalho do Stream Analytics. 

Você pode assistir a [um vídeo](https://www.youtube.com/watch?v=SGUpT-a99MA) que ilustra esse cenário.


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você tem o seguinte:

* Uma conta do Azure.
* Uma conta para o Power BI. Você pode usar uma conta corporativa ou de estudante.
* Uma versão concluída do tutorial [Detecção de fraudes em tempo real](stream-analytics-real-time-fraud-detection.md). O tutorial inclui um aplicativo que gera metadados de chamada telefônica fictícios. No tutorial, você cria um hub de eventos e envia os dados de streaming de chamada telefônica para o hub de eventos. Você escreve uma consulta que detecta chamadas fraudulentas (chamadas simultâneas do mesmo número em diferentes locais). 


## <a name="add-power-bi-output"></a>Adicionar saída do Power BI
No tutorial de detecção de fraudes em tempo real, a saída é enviada para o Armazenamento de Blobs do Azure. Nesta seção, você deve adicionar uma saída que envia informações ao Power BI.

1. No Portal do Azure, abra o trabalho do Stream Analytics criado anteriormente. Se você usou o nome sugerido, o trabalho é nomeado `sa_frauddetection_job_demo`.

2. Marque a caixa **Saídas** no meio do painel do trabalho e então selecione **+ Adicionar**.

3. Para **Alias de Saída**, digite `CallStream-PowerBI`. Você pode usar um nome diferente. Se você fizer isso, anote-o, pois você precisará desse nome mais tarde. 

4. Em **Coletor**, selecione **Power BI**.

   ![Criar uma saída para o Power BI](./media/stream-analytics-power-bi-dashboard/create-power-bi-ouptut.png)

5. Clique em **Autorizar**.

    É aberta uma janela em que você pode fornecer suas credenciais do Azure para uma conta corporativa ou de estudante. 

    ![Insira as credenciais para acesso ao Power BI](./media/stream-analytics-power-bi-dashboard/power-bi-authorization-credentials.png)

6. Insira suas credenciais. Lembre-se que quando você inserir suas credenciais, você também estará concedendo permissão para o trabalho do Stream Analytics para acessar a área do Power BI.

7. Quando você retornar para a folha **Nova saída**, insira as seguintes informações:

   * **Workspace de Grupo**: Selecione um workspace no seu locatário do Power BI em que você deseja criar o conjunto de dados.
   * **Nome do conjunto de dados**:  Digite `sa-dataset`. Você pode usar um nome diferente. Se você fizer isso, anote-o para mais tarde.
   * **Nome da tabela**: Digite `fraudulent-calls`. Atualmente, a saída do Power BI de trabalhos do Stream Analytics só podem ter uma tabela em um conjunto de dados.

     ![Tabela e conjunto de dados do workspace do Power BI](./media/stream-analytics-power-bi-dashboard/create-pbi-ouptut-with-dataset-table.png)

     > [!WARNING]
     > Se o Power BI tem um conjunto de dados e uma tabela que tem os mesmo nomes daquelas especificadas por você no trabalho do Stream Analytics, os existentes são substituídos.
     > Recomendamos que você não crie explicitamente esse conjunto de dados e a tabela em sua conta do Power BI. Ele serão automaticamente criados quando você iniciar o trabalho do Stream Analytics e ele começar a enviar saídas para o Power BI. Se a consulta de trabalho não gerar resultados, o conjunto de dados e a tabela não serão criados.
     >

8. Clique em **Criar**.

O conjunto de dados é criado com as seguintes configurações:

* **defaultRetentionPolicy: BasicFIFO**: Os dados são FIFO, com um máximo de 200.000 linhas.
* **defaultMode: pushStreaming**: O conjunto de dados dá suporte aos blocos de streaming e visuais com base em relatórios tradicionais (também conhecido como push).

Atualmente, não é possível criar conjuntos de dados com outros sinalizadores.

Para saber mais sobre conjuntos de dados do Power BI, consulte a referência à [API REST do Power BI](https://msdn.microsoft.com/library/mt203562.aspx).


## <a name="write-the-query"></a>Gravar a consulta

1. Feche a folha **Saídas** e retorne para a folha de trabalho.

2. Clique na caixa **Consulta**. 

3. Insira a consulta a seguir. Essa consulta é semelhante à consulta de autojunção que você criou no tutorial de detecção de fraudes. A diferença é que essa consulta envia resultados para a nova saída criada por você (`CallStream-PowerBI`). 

    >[!NOTE]
    >Se você não nomear a entrada `CallStream` no tutorial de detecção de fraudes, substitua o seu nome para `CallStream` nas cláusulas **FROM** e **JOIN** na consulta.

        ```SQL
        /* Our criteria for fraud:
        Calls made from the same caller to two phone switches in different locations (for example, Australia and Europe) within five seconds */

        SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
        INTO "CallStream-PowerBI"
        FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
        JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime

        /* Where the caller is the same, as indicated by IMSI (International Mobile Subscriber Identity) */
        ON CS1.CallingIMSI = CS2.CallingIMSI

        /* ...and date between CS1 and CS2 is between one and five seconds */
        AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5

        /* Where the switch location is different */
        WHERE CS1.SwitchNum != CS2.SwitchNum
        GROUP BY TumblingWindow(Duration(second, 1))
        ```

4. Clique em **Salvar**.


## <a name="test-the-query"></a>Testar a consulta
Esta etapa é opcional, mas recomendada. 

1. Se o aplicativo TelcoStreaming não estiver em execução, inicie-o seguindo estas etapas:

    * Abra uma janela de comando.
    * Vá para a pasta em que os arquivos telcodatagen.exe.config e telcogenerator.exe.config modificado estão.
    * Execute o comando a seguir:

       `telcodatagen.exe 1000 .2 2`

2. Na folha **Consulta**, clique nos pontos ao lado da entrada `CallStream` e, em seguida, selecione **Dados de exemplo da entrada**.

3. Especifique que você deseja dados equivalentes a três minutos e clique em **OK**. Aguarde até ser notificado de que a amostragem dos dados foi realizada.

4. Clique em **Teste** e verifique se você está obtendo resultados.


## <a name="run-the-job"></a>Executar o trabalho

1. Verifique se o aplicativo TelcoStreaming está em execução.

2. Feche a folha **Consulta**.

3. Na folha do trabalho, clique em **Iniciar**.

    ![Iniciar o trabalho do Stream Analytics](./media/stream-analytics-power-bi-dashboard/stream-analytics-sa-job-start-output.png)

O trabalho do Stream Analytics começa procurando chamadas fraudulentas no fluxo de entrada. O trabalho também cria o conjunto de dados e a tabela no Power BI e começa a enviar dados sobre as chamadas fraudulentas para eles.


## <a name="create-the-dashboard-in-power-bi"></a>Criar o painel no Power BI

1. Acesse [Powerbi.com](https://powerbi.com) e entre com a sua conta corporativa ou de estudante. Se a consulta do trabalho do Stream Analytics gera resultados como saída, você vê que o conjunto de dados já está criado:

    ![Localização do conjunto de dados de streaming no Power BI](./media/stream-analytics-power-bi-dashboard/stream-analytics-streaming-dataset.png)

2. No workspace, clique em **+&nbsp;Criar**.

    ![O botão Criar no workspace do Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard.png)

3. Crie um novo painel e nomeie-o `Fraudulent Calls`.

    ![Crie um painel e dê a ele um nome no workspace do Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard-name.png)

4. Na parte superior da janela, clique em **Adicionar bloco**, selecione **DADOS DE STREAMING PERSONALIZADOS** e, em seguida, clique em **Avançar**.

    ![Bloco do conjunto de dados de streaming no Power BI](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

5. Em **SEUS CONJUNTOS DE DADOS**, selecione o conjunto de dados e, em seguida, clique em **Avançar**.

    ![Seu conjunto de dados de streaming no Power BI](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

6. Em **Tipo de Visualização**, selecione **Cartão** e, em seguida, na lista **Campos**, selecione **fraudulentcalls**.

    ![Detalhes da visualização para o novo bloco](./media/stream-analytics-power-bi-dashboard/add-fraudulent-calls-tile.png)

7. Clique em **Avançar**.

8. Preencha os detalhes do bloco, tais como um título e subtítulo.

    ![Título e subtítulo para o novo bloco](./media/stream-analytics-power-bi-dashboard/pbi-new-tile-details.png)

9. Clique em **Aplicar**.

    Agora você tem um contador de fraudes!

    ![Contador de fraudes no painel do Power BI](./media/stream-analytics-power-bi-dashboard/power-bi-fraud-counter-tile.png)

8. Siga as etapas novamente para adicionar um bloco (começando pela etapa 4). Dessa vez, faça o seguinte:

    * Quando você chegar em **Tipo de Visualização**, selecione **Gráfico de linhas**. 
    * Adicionar um eixo e selecione **windowend**. 
    * Adicione um valor e selecione **fraudulentcalls**.
    * Para **Janela de tempo para exibir**, selecione os últimos 10 minutos.

      ![Criar um bloco para o gráfico de linhas no Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-tile-line-chart.png)

9. Clique em **Avançar**, adicione um título e subtítulo e clique em **Aplicar**.

     O painel do Power BI agora oferece dois modos de exibição de dados sobre chamadas fraudulentas conforme detectado nos dados de streaming.

     ![O painel do Power BI foi concluído mostrando dois blocos para chamadas fraudulentas](./media/stream-analytics-power-bi-dashboard/pbi-dashboard-fraudulent-calls-finished.png)


## <a name="learn-more-about-power-bi"></a>Saiba mais sobre o Power BI

Este tutorial demonstra como criar somente alguns tipos de visualizações para um conjunto de dados. O Power BI pode ajudá-lo a criar outras ferramentas de cliente business intelligence para sua organização. Para obter mais ideias, consulte os recursos a seguir:

* Para obter outro exemplo de um painel do Power BI, assista ao vídeo [Introdução ao Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s) .
* Para obter mais informações sobre como configurar uma saída do trabalho do Stream Analytics para o Power BI e usar grupos do Power BI, examine a seção [Power BI](stream-analytics-define-outputs.md#power-bi) do artigo [Saídas do Stream Analytics](stream-analytics-define-outputs.md). 
* Para obter informações sobre como usar o Power BI em geral, consulte [Painéis no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).


## <a name="learn-about-limitations-and-best-practices"></a>Saiba mais sobre limitações e práticas recomendadas
Atualmente, o Power BI pode ser chamado aproximadamente uma vez por segundo. A transmissão de elementos visuais oferece suporte a pacotes de 15 KB. Além disso, o streaming de elementos visuais falha (mas o push continua a funcionar). Por causa dessas limitações, o Power BI se ajusta bem mais naturalmente nos casos em que a Stream Analytics do Azure faz uma significativa redução de carga de dados. É recomendável usar uma janela em cascata ou janela de salto para garantir que o push de dados seja de no máximo um push por segundo e também que sua consulta esteja dentro dos requisitos de produtividade.

Você pode usar a seguinte equação para calcular o valor em segundos dar sua janela:

![Equação para calcular o valor da janela em segundos](./media/stream-analytics-power-bi-dashboard/compute-window-seconds-equation.png)  

Por exemplo: 

* Você tem 1.000 dispositivos que enviam dados em intervalos de um segundo.
* Você está usando o SKU do Power BI Pro que dá suporte a 1.000.000 linhas por hora.
* Você deseja publicar a quantidade de dados médios por dispositivo no Power BI.

Como resultado, a equação torna-se:

![Equação com base em critérios de exemplo](./media/stream-analytics-power-bi-dashboard/power-bi-example-equation.png)  

Dada essa configuração, você pode alterar a consulta original para o seguinte:

```SQL
    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO "CallStream-PowerBI"
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,4),
        dspl
```

### <a name="renew-authorization"></a>Renovar autorização
Caso sua senha tenha sido alterada depois de seu trabalho ser criado ou autenticado pela última vez, será necessário autenticar novamente sua conta do Power BI. Se a Autenticação Multifator estiver configurada no locatário do Azure Active Directory (Azure AD) também será necessário renovar a autorização do Power BI a cada duas semanas. Se você não renovar, você poderá ver os sintomas, como falta de saída do trabalho ou um `Authenticate user error` nos logs de operação.

De modo similar, se um trabalho iniciar depois que o token tiver expirado, ocorrerá um erro e o trabalho falhará. Para resolver esse problema, pare o trabalho em execução e vá para a saída do Power BI. Para evitar a perda de dados, selecione **Renovar autorização** e reinicie o trabalho a partir da Hora da **Última Interrupção**.

Depois que a autorização foi atualizada com o Power BI, um alerta verde é exibida na área de autorização para refletir se o problema foi resolvido.

## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de linguagem de consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

---
title: Integração do Azure Stream Analytics com o Azure Machine Learning Studio (clássico)
description: Este artigo descreve como configurar rapidamente um trabalho simples de Azure Stream Analytics que integra Azure Machine Learning Studio (clássico), usando uma função definida pelo usuário.
ms.service: stream-analytics
author: sidramadoss
ms.author: sidram
ms.topic: how-to
ms.date: 08/12/2020
ms.custom: seodec18
ms.openlocfilehash: 1ebe62c1b90e09b36dd75b5bda4054cca08d5759
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102441203"
---
# <a name="do-sentiment-analysis-with-azure-stream-analytics-and-azure-machine-learning-studio-classic"></a>Fazer análise de sentimentos com Azure Stream Analytics e Azure Machine Learning Studio (clássico)

Este artigo mostra como configurar um trabalho simples de Azure Stream Analytics que usa Azure Machine Learning Studio (clássico) para análise de sentimentos. Você usa um modelo de análise de sentimentos de estúdio (clássico) do Cortana Intelligence Gallery para analisar dados de texto de streaming e determinar a pontuação de sentimentos.

> [!TIP]
> É altamente recomendado usar [UDFs do Azure Machine Learning](machine-learning-udf.md) em vez de UDFs do Azure Machine Learning Studio (clássico) para melhorar o desempenho e a confiabilidade.

Você pode aplicar o que aprendeu com este artigo a cenários como estes:

* Analisar sentimento em tempo real no streaming de dados do Twitter.
* Analisar registros de conversas do cliente com a equipe de suporte.
* Avaliar comentários em fóruns, blogs e vídeos.
* Muitos outros cenários de pontuação preditiva em tempo real.

O trabalho de Stream Analytics que você cria aplica o modelo de análise de sentimento como uma UDF (função definida pelo usuário) nos dados de texto de exemplo do repositório de blob. A saída (o resultado da análise de sentimento) é gravada no mesmo repositório de blob em um arquivo CSV diferente. 

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você tem:

* Uma assinatura ativa do Azure.

* Um arquivo CSV com alguns dados do Twitter. Você pode baixar um arquivo de exemplo do [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv)ou pode criar seu próprio arquivo. Em um cenário do mundo real, você deve obter os dados diretamente de um fluxo de dados do Twitter.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Criar um contêiner de armazenamento e carregar o arquivo de entrada CSV

Nesta etapa, você carrega um arquivo CSV em seu contêiner de armazenamento.

1. Na portal do Azure, selecione **criar um recurso**  >  **armazenamento**  >  **conta de armazenamento**.

2. Preencha a guia *noções básicas* com os seguintes detalhes e deixe os valores padrão para os campos restantes:

   |Campo  |Valor  |
   |---------|---------|
   |Subscription|Escolha sua assinatura.|
   |Resource group|Escolha seu grupo de recursos.|
   |Nome da conta de armazenamento|Insira um nome para a conta de armazenamento. O nome deve ser exclusivo em todo o Azure.|
   |Location|Escolha um local. Todos os recursos devem usar o mesmo local.|
   |Tipo de conta|BlobStorage|

   ![informe os detalhes da conta de armazenamento](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account1.png)

3. Selecione **Examinar + criar**. Em seguida, selecione **criar** para implantar sua conta de armazenamento.

4. Quando a implantação for concluída, navegue até sua conta de armazenamento. No **Serviço Blob**, selecione **Contêineres**. Em seguida, selecione **+ contêiner** para criar um novo contêiner.

   ![Criar um contêiner de armazenamento de blob para entrada](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account2.png)

5. Forneça um nome para o contêiner e verifique se o **nível de acesso público** está definido como **privado**. Quando terminar, selecione **Criar**.

   ![especifique os detalhes do contêiner de blob](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account3.png)

6. Navegue até o contêiner recém-criado e selecione **carregar**. Carregue o arquivo de **sampleinput.csv** que você baixou anteriormente.

   ![Botão “Carregar” para um contêiner](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Adicione o modelo de análise de sentimento da Galeria do Cortana Intelligence

Agora que os dados de exemplo estão em um blob, você pode habilitar o modelo de análise de sentimento na Galeria do Cortana Intelligence.

1. Acesse a página [modelo preditivo de análise de sentimento](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) da Galeria do Cortana Intelligence.  

2. Selecione **abrir no Studio (clássico)**.  
   
   ![Stream Analytics Azure Machine Learning Studio (clássico), Open Studio (clássico)](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Entre para acessar o workspace. Selecione uma localização.

4. Selecione **executar** na parte inferior da página. O processo é executado, o que leva cerca de um minuto.

   ![Executar experimento no estúdio (clássico)](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. Depois que o processo tiver sido executado com êxito, selecione **Implantar serviço Web** na parte inferior da página.

   ![Implantar experimento no Studio (clássico) como um serviço Web](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Para validar que o modelo de análise de sentimentos está pronto para uso, selecione o botão **testar** . Forneça entrada de texto, como "Eu amo a Microsoft".

   ![Teste de experimento no estúdio (clássico)](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

   Se o teste funcionar, você verá um resultado semelhante ao exemplo a seguir:

   ![Resultados de teste no estúdio (clássico)](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. Na coluna **aplicativos** , selecione o link do **Excel 2010 ou da pasta de trabalho anterior** para baixar uma pasta de trabalho do Excel. A pasta de trabalho contém a chave de API e a URL de que você precisará mais tarde para configurar o trabalho do Stream Analytics.

    ![Stream Analytics Azure Machine Learning Studio (clássico), visão rápida](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  

## <a name="create-a-stream-analytics-job-that-uses-the-studio-classic-model"></a>Criar um trabalho de Stream Analytics que usa o modelo de estúdio (clássico)

Agora você pode criar um trabalho do Stream Analytics que leia os tweets de exemplo do arquivo CSV no armazenamento de blobs.

### <a name="create-the-job"></a>Criar o trabalho

Vá para a [portal do Azure](https://portal.azure.com) e crie um trabalho de Stream Analytics. Se você não estiver familiarizado com esse processo, consulte [criar um Stream Analytics trabalho usando o portal do Azure](stream-analytics-quick-create-portal.md).

### <a name="configure-the-job-input"></a>Configurar a entrada do trabalho

O trabalho obtém sua entrada do arquivo CSV que você carregou anteriormente para o armazenamento de blobs.

1. Navegue até o trabalho do Stream Analytics. Em **topologia do trabalho**, selecione a opção **entradas** . Selecione **Adicionar fluxo**  > **armazenamento de blob** de entrada.

2. Preencha os detalhes do **armazenamento de blob** com os seguintes valores:

   |Campo  |Valor  |
   |---------|---------|
   |Alias de entrada|Dê um nome à sua entrada. Lembre-se de que esse alias é quando você escreve sua consulta.|
   |Subscription|Selecione sua assinatura.|
   |Conta de armazenamento|Selecione a conta de armazenamento que você criou na etapa anterior.|
   |Contêiner|Selecione o contêiner que você criou na etapa anterior.|
   |Formato de serialização do evento|CSV|

3. Clique em **Salvar**.

### <a name="configure-the-job-output"></a>Configurar a saída do trabalho

O trabalho envia resultados para o mesmo armazenamento de blobs do qual ele obtém a entrada.

1. Navegue até o trabalho do Stream Analytics. Em **topologia do trabalho**, selecione a opção **saídas** . Selecione **Adicionar**  >  **armazenamento de BLOBs**.

2. Preencha o formulário de **armazenamento de blob** com estes valores:

   |Campo  |Valor  |
   |---------|---------|
   |Alias de entrada|Dê um nome à sua entrada. Lembre-se de que esse alias é quando você escreve sua consulta.|
   |Subscription|Selecione sua assinatura.|
   |Conta de armazenamento|Selecione a conta de armazenamento que você criou na etapa anterior.|
   |Contêiner|Selecione o contêiner que você criou na etapa anterior.|
   |Formato de serialização do evento|CSV|

3. Clique em **Salvar**.

### <a name="add-the-studio-classic-function"></a>Adicionar a função Studio (clássico)

Anteriormente, você publicou um modelo de estúdio (clássico) em um serviço Web. Nesse cenário, quando o trabalho de Stream Analysis é executado, ele envia cada tweet de exemplo da entrada para o serviço Web para análise de sentimento. O serviço Web Studio (clássico) retorna um sentimentos ( `positive` , `neutral` ou `negative` ) e uma probabilidade de o tweet ser positivo.

Nesta seção, você define uma função no trabalho de análise de fluxo. A função pode ser invocada para enviar um tweet ao serviço Web e retornar a resposta.

1. Verifique se que você tem a URL do serviço Web e a chave de API baixada anteriormente na pasta de trabalho do Excel.

2. Navegue até o trabalho do Stream Analytics. Em seguida, selecione **funções**  >  **+ Adicionar**  >  **Azure ml Studio**

3. Preencha o formulário de **função Azure Machine Learning** com os seguintes valores:

   |Campo  |Valor  |
   |---------|---------|
   | Alias da função | Use o nome `sentiment` e selecione **fornecer Azure Machine Learning configurações de função manualmente**, o que lhe dá uma opção para inserir a URL e a chave.      |
   | URL| Cole a URL do serviço Web.|
   |Chave | Cole a chave de API. |

4. Selecione **Salvar**.

### <a name="create-a-query-to-transform-the-data"></a>Criar uma consulta para transformar os dados

O Stream Analytics usa uma consulta declarativa baseada em SQL para examinar a entrada e processá-la. Nesta seção, você cria uma consulta que lê cada tweet da entrada e, em seguida, invoca a função Studio (clássico) para executar a análise de sentimentos. A consulta então envia o resultado para a saída definida (armazenamento de blobs).

1. Retorne para a visão geral do trabalho de Stream Analytics.

2. Em **Topologia do trabalho**, selecione **Consulta**.

3. Insira a consulta a seguir:

    ```SQL
    WITH sentiment AS (  
    SELECT text, sentiment1(text) as result 
    FROM <input>  
    )  

    SELECT text, result.[Score]  
    INTO <output>
    FROM sentiment  
    ```    

   A consulta invoca a função que você criou anteriormente ( `sentiment` ) para executar a análise de sentimentos em cada tweet na entrada.

4. Selecione **salvar** para salvar a consulta.

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Iniciar o trabalho do Stream Analytics e verificar a saída

Agora você pode iniciar o trabalho do Stream Analytics.

### <a name="start-the-job"></a>Iniciar o trabalho

1. Retorne para a visão geral do trabalho de Stream Analytics.

2. Selecione **Iniciar** na parte superior da página.

3. Em **Iniciar trabalho**, selecione **personalizado** e, em seguida, selecione um dia antes de quando você carregou o arquivo CSV no armazenamento de BLOBs. Quando terminar, selecione **Iniciar**.  

### <a name="check-the-output"></a>Verifique a saída

1. Deixe o trabalho ser executado por alguns minutos até ver a atividade na caixa **Monitoramento**.

2. Se você tiver uma ferramenta que normalmente usa para examinar o conteúdo do armazenamento de BLOBs, use essa ferramenta para examinar o contêiner. Como alternativa, siga estas etapas no portal do Azure:

      1. No portal do Azure, localize sua conta de armazenamento e, dentro da conta, localize o contêiner. Você ver dois arquivos no contêiner: o arquivo que contém os tweets de exemplo e um arquivo CSV gerado pelo trabalho do Stream Analytics.
      2. Clique com o botão direito do mouse no arquivo gerado e, em seguida, selecione **Baixar**.

3. Abra o arquivo CSV gerado. Você verá algo parecido com o exemplo a seguir:  

   ![Stream Analytics Azure Machine Learning Studio (clássico), exibição de CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  

### <a name="view-metrics"></a>Métricas de exibição

Você também pode exibir as métricas relacionadas à função do Studio (clássico). As seguintes métricas relacionadas à função são exibidas na caixa **monitoramento** da visão geral do trabalho:

* **Solicitações de função** indica o número de solicitações enviadas a um serviço Web de estúdio (clássico).  
* **Eventos de função** indica o número de eventos na solicitação. Por padrão, cada solicitação para um serviço Web Studio (clássico) contém até 1.000 eventos.

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](/stream-analytics-query/stream-analytics-query-language-reference)
* [Integrar API REST e Machine Learning Studio (clássico)](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](/rest/api/streamanalytics/)
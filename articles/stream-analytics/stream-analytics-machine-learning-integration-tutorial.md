---
title: "Integração do Azure Stream Analytics e do Machine Learning | Microsoft Docs"
description: "Como usar uma função definida pelo usuário e o Machine Learning em um trabalho do Stream Analytics"
keywords: 
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: cfced01f-ccaa-4bc6-81e2-c03d1470a7a2
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 07/06/2017
ms.author: samacha
ms.openlocfilehash: 243ee799d2cddb1baf5b8046eee6eaf182463d2e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="performing-sentiment-analysis-by-using-azure-stream-analytics-and-azure-machine-learning"></a>Como realizar uma análise de sentimento usando o Azure Stream Analytics e o Azure Machine Learning
Este artigo descreve como configurar rapidamente um trabalho do Azure Stream Analytics simples que se integre ao Azure Machine Learning. Você usa um modelo de análise de sentimento de Machine Learning da Galeria do Cortana Intelligence para analisar dados de texto de streaming e determinar a pontuação de sentimento em tempo real. Usar o Cortana Intelligence Suite permite realizar essa tarefa sem se preocupar com as complexidades de criar um modelo de análise de sentimento.

Você pode aplicar o que aprendeu com este artigo a cenários como estes:

* Analisar sentimento em tempo real no streaming de dados do Twitter.
* Analisar registros de conversas do cliente com a equipe de suporte.
* Avaliar comentários em fóruns, blogs e vídeos. 
* Muitos outros cenários de pontuação preditiva em tempo real.

Em um cenário do mundo real, você deve obter os dados diretamente de um fluxo de dados do Twitter. Para simplificar o tutorial, ele foi escrito de modo que o trabalho de Stream Analytics receba tweets de um arquivo CSV no armazenamento de Blobs do Azure. Você pode criar seu próprio arquivo CSV ou pode usar um arquivo CSV de exemplo, conforme mostra na imagem a seguir:

![tweets de exemplo em um arquivo CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

O trabalho de Stream Analytics que você cria aplica o modelo de análise de sentimento como uma UDF (função definida pelo usuário) nos dados de texto de exemplo do repositório de blob. A saída (o resultado da análise de sentimento) é gravada no mesmo repositório de blob em um arquivo CSV diferente. 

A figura a seguir demonstra essa configuração. Conforme observado, para um cenário mais realista, você pode substituir o armazenamento de blobs pelo streaming de dados do Twitter de uma entrada de Hubs de Eventos do Azure. Além disso, você pode compilar uma visualização em tempo real do [Microsoft Power BI](https://powerbi.microsoft.com/) do sentimento agregado.    

![Visão geral de integração do Machine Learning do Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, verifique se você tem:

* Uma assinatura ativa do Azure.
* Um arquivo CSV com alguns dados. Você pode baixar o arquivo mostrado anteriormente do [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv) ou pode criar seu próprio arquivo. Neste artigo, vamos supor que você esteja usando o arquivo do GitHub.

Em um nível alto, para concluir as tarefas demonstradas neste artigo, você fará o seguinte:

1. Criar uma conta de armazenamento do Azure e um contêiner de armazenamento de blobs e carregar um arquivo de entrada formatado em CSV para o contêiner.
3. Adicionar um modelo de análise de sentimento da Galeria Cortana Intelligence ao seu espaço de trabalho do Azure Machine Learning e implantar esse modelo como um serviço Web no espaço de trabalho do Machine Learning.
5. Criar um trabalho do Stream Analytics que chame esse serviço Web como uma função para determinar o sentimento da entrada de texto.
6. Iniciar o trabalho do Stream Analytics e verificar a saída.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Criar um contêiner de armazenamento e carregar o arquivo de entrada CSV
Nesta etapa, você pode usar qualquer arquivo CSV, como o disponível no GitHub.

1. No portal do Azure, clique em **Novo** &gt; **Armazenamento** &gt; **Conta de armazenamento**.

   ![criar nova conta de armazenamento](./media/stream-analytics-machine-learning-integration-tutorial/azure-portal-create-storage-account.png)

2. Forneça um nome (`samldemo` no exemplo). O nome pode ter apenas letras minúsculas e números e deve ser exclusivo no Azure. 

3. Especifique um grupo de recursos existente e um local. Para o local, é recomendável que todos os recursos criados neste tutorial usem o mesmo local.

    ![informe os detalhes da conta de armazenamento](./media/stream-analytics-machine-learning-integration-tutorial/create-sa1.png)

4. No portal do Azure, selecione a conta de armazenamento. Na folha da conta de armazenamento, clique em **Contêineres** e, em seguida, clique em **+&nbsp;Contêiner** para criar o armazenamento de blobs.

    ![criar contêiner de blobs](./media/stream-analytics-machine-learning-integration-tutorial/create-sa2.png)

5. Forneça um nome para o contêiner (`azuresamldemoblob` no exemplo) e verifique se **Tipo de acesso** está definido como **Blob**. Quando terminar, clique em **OK**.

    ![especifique os detalhes do contêiner de blob](./media/stream-analytics-machine-learning-integration-tutorial/create-sa3.png)

6. Na folha **Contêineres**, selecione o novo contêiner, que abre a folha para esse contêiner.

7. Clique em **Carregar**.

    ![Botão “Carregar” para um contêiner](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

8. Na folha **Carregar blob**, especifique o arquivo CSV que você deseja usar para este tutorial. Para **Tipo de blob**, selecione **Blob de blocos** e defina o tamanho de bloco como 4 MB, que é suficiente para este tutorial.

    ![carregar arquivo de blob](./media/stream-analytics-machine-learning-integration-tutorial/create-sa4.png)

9. Clique no botão **Carregar** na parte inferior da folha.

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Adicione o modelo de análise de sentimento da Galeria do Cortana Intelligence

Agora que os dados de exemplo estão em um blob, você pode habilitar o modelo de análise de sentimento na Galeria do Cortana Intelligence.

1. Acesse a página [modelo preditivo de análise de sentimento](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) da Galeria do Cortana Intelligence.  

2. Clique em **Abrir no Studio**.  
   
   ![Stream Analytics Machine Learning, abrir o Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Entre para acessar o espaço de trabalho. Selecione um local.

4. Clique em **Executar** na parte inferior da página. O processo é executado, o que leva cerca de um minuto.

   ![executar experimento no Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. Depois que o processo tiver sido executado com êxito, selecione **Implantar serviço Web** na parte inferior da página.

   ![implantar experimento no Machine Learning Studio como um serviço Web](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Para validar que o modelo de análise de sentimento está pronto para uso, clique no botão **Teste**. Forneça entrada de texto, como "Eu amo a Microsoft". 

   ![testar experimento no Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

    Se o teste funcionar, você verá um resultado semelhante ao exemplo a seguir:

   ![testar os resultados no Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. Na coluna **Aplicativos**, clique no link **Excel 2010 ou pasta de trabalho anterior** para baixar uma pasta de trabalho do Excel. A pasta de trabalho contém a chave de API e a URL de que você precisará mais tarde para configurar o trabalho do Stream Analytics.

    ![Stream Analytics Machine Learning, visão rápida](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  


## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Criar um trabalho do Stream Analytics que usa o modelo do Machine Learning

Agora você pode criar um trabalho do Stream Analytics que leia os tweets de exemplo do arquivo CSV no armazenamento de blobs. 

### <a name="create-the-job"></a>Criar o trabalho

1. Vá para o [Portal do Azure](https://portal.azure.com).  

2. Clique em **Novo** > **Internet das Coisas** > **Trabalho do Stream Analytics**. 

   ![Caminho do portal do Azure para obter um novo trabalho do Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/azure-portal-new-iot-sa-job.png)
   
3. Dê ao trabalho o nome de `azure-sa-ml-demo`, especifique uma assinatura, especifique um grupo de recursos existente ou crie um novo e selecione o local para o trabalho.

   ![especifique as configurações para o novo trabalho do Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-job-1.png)
   

### <a name="configure-the-job-input"></a>Configurar a entrada do trabalho
O trabalho obtém sua entrada do arquivo CSV que você carregou anteriormente para o armazenamento de blobs.

1. Depois que o trabalho tiver sido criado, em **Topologia do Trabalho** na folha de trabalho, clique na caixa **Entradas**.  
   
   ![Caixa “Entradas” na folha do trabalho do Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-job-add-input.png)  

2. Na folha **Entradas**, clique em **+ Adicionar**.

   ![Botão “Adicionar” para adicionar uma entrada ao trabalho Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-job-add-input-button.png)  

3. Preencha a folha **Nova entrada** com estes valores:

    * **Alias de entrada**: Use o nome `datainput`.
    * **Tipo de Origem**: Selecione **Fluxo de Dados**.
    * **Fontes**: selecione **Armazenamento de blobs**.
    * **Opção de importação**: selecione **Usar armazenamento de blobs da assinatura atual**. 
    * **Conta de armazenamento**. Selecione a conta de armazenamento criada anteriormente.
    * **Contêiner**. Selecione o contêiner criado anteriormente (`azuresamldemoblob`).
    * **Formato de serialização do evento**. Selecione **CSV**.

    ![Configurações para a nova entrada de trabalho](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-create-sa-input-new-portal.png)

4. Clique em **Criar**.

### <a name="configure-the-job-output"></a>Configurar a saída do trabalho
O trabalho envia resultados para o mesmo armazenamento de blobs do qual ele obtém a entrada. 

1. Em **Topologia do trabalho** na folha de trabalho, clique na caixa **Saídas**.  
  
   ![Criar nova saída para o trabalho do Streaming Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-output.png)  

2. Na folha **Saídas**, clique em **+ Adicionar** e, em seguida, adicione uma saída com o alias `datamloutput`. 

3. Para **Coletor**, selecione **Armazenamento de blobs**. Em seguida, preencha o restante das configurações de saída usando os mesmos valores usados para o armazenamento de blobs para entrada:

    * **Conta de armazenamento**. Selecione a conta de armazenamento criada anteriormente.
    * **Contêiner**. Selecione o contêiner criado anteriormente (`azuresamldemoblob`).
    * **Formato de serialização do evento**. Selecione **CSV**.

   ![Configurações para a nova saída de trabalho](./media/stream-analytics-machine-learning-integration-tutorial/create-output2.png) 

4. Clique em **Criar**.   


### <a name="add-the-machine-learning-function"></a>Adicionar a função de Machine Learning 
Anteriormente, você publicou um modelo de Machine Learning a um serviço Web. Em nosso cenário, quando o trabalho de Stream Analysis é executado, ele envia cada tweet de exemplo da entrada para o serviço Web para análise de sentimento. O serviço Web do Machine Learning retorna um sentimento (`positive`, `neutral` ou `negative`) e a probabilidade de o tweet ser positivo. 

Nesta seção do tutorial, você define uma função do trabalho de Stream Analysis. A função pode ser invocada para enviar um tweet ao serviço Web e retornar a resposta. 

1. Verifique se que você tem a URL do serviço Web e a chave de API baixada anteriormente na pasta de trabalho do Excel.

2. Retornar à folha de visão geral do trabalho.

3. Em **Configurações**, selecione **Funções** e, em seguida, clique em **+ Adicionar**.

   ![Adicionar uma função ao trabalho do Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-function1.png) 

4. Digite `sentiment` como o alias da função e preencha o restante da folha usando estes valores:

    * **Tipo de função**: selecione **Azure ML**.
    * **Opção de importação**: selecione **Importar de outra assinatura**. Isso lhe dá a oportunidade de inserir a URL e a chave.
    * **URL**: cole a URL do serviço Web.
    * **Chave**: cole a chave de API.
  
    ![Configurações para adicionar uma função de Machine Learning para o trabalho de Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/add-function.png)  
    
5. Clique em **Criar**.

### <a name="create-a-query-to-transform-the-data"></a>Criar uma consulta para transformar os dados

O Stream Analytics usa uma consulta declarativa baseada em SQL para examinar a entrada e processá-la. Nesta seção, você cria uma consulta que lê cada tweet da entrada e, em seguida, invoca a função de Machine Learning para executar a análise de sentimento. A consulta então envia o resultado para a saída definida (armazenamento de blobs).

1. Retornar à folha de visão geral do trabalho.

2.  Em **Topologia do trabalho**, clique na caixa **Consulta**.

    ![Criar uma consulta para o trabalho de Análise de Streaming](./media/stream-analytics-machine-learning-integration-tutorial/create-query.png)  

3. Insira a consulta a seguir:

    ```
    WITH sentiment AS (  
    SELECT text, sentiment(text) as result from datainput  
    )  

    Select text, result.[Score]  
    Into datamloutput
    From sentiment  
    ```    

    A consulta invoca a função que você criou anteriormente (`sentiment`) para executar a análise de sentimento em cada tweet na entrada. 

4. Clique em **Salvar** para salvar a consulta.


## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Iniciar o trabalho do Stream Analytics e verificar a saída

Agora você pode iniciar o trabalho do Stream Analytics.

### <a name="start-the-job"></a>Iniciar o trabalho
1. Retornar à folha de visão geral do trabalho.

2. Clique em **iniciar** na parte superior da folha.

    ![Criar uma consulta para o trabalho de Análise de Streaming](./media/stream-analytics-machine-learning-integration-tutorial/start-job.png)  

3. Em **Iniciar trabalho**, selecione **Personalizado** e, em seguida, selecione um dia antes de quando você carregou o arquivo CSV para o armazenamento de blobs. Quando terminar, clique em **Iniciar**.  


### <a name="check-the-output"></a>Verifique a saída
1. Deixe o trabalho ser executado por alguns minutos até ver a atividade na caixa **Monitoramento**. 

2. Se você tiver uma ferramenta que use normalmente para examinar o conteúdo do armazenamento de blobs, use essa ferramenta para examinar o contêiner `azuresamldemoblob`. Como alternativa, siga estas etapas no portal do Azure:

    1. No portal, localize a conta de armazenamento `samldemo` e, dentro da conta, localize o contêiner `azuresamldemoblob`. Você ver dois arquivos no contêiner: o arquivo que contém os tweets de exemplo e um arquivo CSV gerado pelo trabalho do Stream Analytics.
    2. Clique com o botão direito do mouse no arquivo gerado e, em seguida, selecione **Baixar**. 

   ![Baixar a saída do trabalho CSV do armazenamento de blobs](./media/stream-analytics-machine-learning-integration-tutorial/download-output-csv-file.png)  

3. Abra o arquivo CSV gerado. Você verá algo parecido com o exemplo a seguir:  
   
   ![Stream Analytics Machine Learning, exibição de CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  


### <a name="view-metrics"></a>Métricas de exibição
Você também pode exibir as métricas relacionadas à função do Azure Machine Learning. As seguintes métricas relacionados à função são exibidas na caixa **Monitoramento** na folha de trabalho:

* **Solicitações de Função** indica o número de solicitações enviadas para o serviço Web Machine Learning.  
* **Eventos de função** indica o número de eventos na solicitação. Por padrão, cada solicitação para um serviço Web Machine Learning contém até 1.000 eventos.  


## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Integrar API REST e Machine Learning](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)




---
title: Transmitir dados do Stream Analytics para o Data Lake Store | Microsoft Docs
description: "Usar o Stream Analytics do Azure para transmitir dados para o Repositório Azure Data Lake"
services: data-lake-store,stream-analytics
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: edb58e0b-311f-44b0-a499-04d7e6c07a90
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/03/2017
ms.author: nitinme
ms.openlocfilehash: 92ddf9619a0db398f7866aab60e834f09add3e7a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="stream-data-from-azure-storage-blob-into-data-lake-store-using-azure-stream-analytics"></a>Dados de transmissão do Blob de Armazenamento do Azure para o Repositório Data Lake usando o Stream Analytics do Azure
Neste artigo, você aprenderá como usar o Repositório Azure Data Lake como uma saída para um trabalho do Stream Analytics do Azure. Este artigo demonstra um cenário simples que lê dados de um blob de armazenamento do Azure (entrada) e grava os dados no Repositório Data Lake (saída).

> [!NOTE]
> No momento, há suporte para a criação e a configuração das saídas do Data Lake Store para o Stream Analytics apenas no [Portal Clássico do Azure](https://manage.windowsazure.com). Portanto, algumas partes deste tutorial usarão o Portal Clássico do Azure.
>
>

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, você deve ter o seguinte:

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Conta de Armazenamento do Azure**. Você usará um contêiner de blob desta conta para os dados de entrada para um trabalho do Stream Analytics. Para esse tutorial, suponha que você tem uma conta de armazenamento chamada **storageforasa** e um contêiner na conta chamado **storageforasacontainer**. Depois de criar o contêiner, carregue um arquivo de dados de exemplo nele. 
  
* **Conta do Repositório Azure Data Lake**. Siga as instruções em [Introdução ao Repositório Azure Data Lake usando o Portal do Azure](data-lake-store-get-started-portal.md). Vamos supor que você tenha uma conta do Data Lake Store chamada **asadatalakestore**. 

## <a name="create-a-stream-analytics-job"></a>Criar um trabalho do Stream Analytics
Você começa ao criar um trabalho do Stream Analytics, que inclui uma fonte de entrada e um destino de saída. Para este tutorial, o código-fonte é um contêiner de blob do Azure e o destino é o Repositório Data Lake.

1. Entre no [Portal do Azure](https://portal.azure.com).

2. No painel à esquerda, clique em **Trabalhos do Stream Analytics** e, em seguida, clique em **Adicionar**.

    ![Criar um trabalho do Stream Analytics](./media/data-lake-store-stream-analytics/create.job.png "Criar um trabalho do Stream Analytics")

    > [!NOTE]
    > Certifique-se de criar o trabalho na mesma região que a conta de armazenamento, ou você estará sujeito a pagar pelos custos adicionais de mover os dados entre regiões.
    >

## <a name="create-a-blob-input-for-the-job"></a>Criar uma entrada de blob para o trabalho

1. Abra a página do trabalho do Stream Analytics, clique na guia **Entradas** no painel à esquerda e, em seguida, clique em **Adicionar**.

    ![Adicionar uma entrada ao trabalho](./media/data-lake-store-stream-analytics/create.input.1.png "Adicionar uma entrada ao trabalho")

2. Na folha **Nova entrada**, forneça os valores a seguir.

    ![Adicionar uma entrada ao trabalho](./media/data-lake-store-stream-analytics/create.input.2.png "Adicionar uma entrada ao trabalho")

    * Para **Alias de entrada**, insira um nome exclusivo para essa entrada de trabalho.
    * Para **Tipo de fonte**, selecione **luxo de dados**.
    * Para **Fonte**, selecione **Armazenamento de Blobs**.
    * Para **Assinatura**, selecione **Usar armazenamento de blobs da assinatura atual**.
    * Para **Conta de armazenamento**, selecione a conta de armazenamento que você criou como parte dos pré-requisitos. 
    * Para **Contêiner**, selecione o contêiner que você criou na conta de armazenamento selecionada.
    * Para **Formato de Serialização de Evento**, clique em **CSV**.
    * Para **Delimitador**, selecione **guia**.
    * Para **Codificação**, selecione **UTF-8**.

    Clique em **Criar**. O portal agora adiciona a entrada e testa a conexão.


## <a name="create-a-data-lake-store-output-for-the-job"></a>Criar uma saída do Repositório Data Lake para o trabalho

1. Abra a página do trabalho do Stream Analytics, clique na guia **Saídas** e, em seguida, clique em **Adicionar**.

    ![Adicionar uma saída ao trabalho](./media/data-lake-store-stream-analytics/create.output.1.png "Adicionar uma saída ao trabalho")

2. Na folha **Nova saída**, forneça os valores a seguir.

    ![Adicionar uma saída ao trabalho](./media/data-lake-store-stream-analytics/create.output.2.png "Adicionar uma saída ao trabalho")

    * Para **Alias de saída**, insira um nome exclusivo para essa saída de trabalho. Esse é um nome amigável utilizado em consultas para direcionar a saída da consulta para esse Repositório Data Lake.
    * Para **Coletor**, selecione **Data Lake Store**.
    * Você precisará autorizar o acesso à conta do Data Lake Store. Clique em **Autorizar**.

3. Na folha **Nova saída**, forneça os valores a seguir.

    ![Adicionar uma saída ao trabalho](./media/data-lake-store-stream-analytics/create.output.3.png "Adicionar uma saída ao trabalho")

    * Para **Nome da conta**, selecione a conta do Data Lake Store criada para o qual você deseja que o trabalho de saída seja enviado.
    * Para **Padrão de prefixo do caminho**, insira um caminho de arquivo usado para gravar seus arquivos na conta do Data Lake Store especificada.
    * Para **Formato de data**, se você usou um token de data no caminho do prefixo, pode selecionar o formato de data em que os arquivos estão organizados.
    * Para **Formato de hora**, se você usou um token de hora no caminho do prefixo, especifique o formato de hora em que os arquivos estão organizados.
    * Para **Formato de Serialização de Evento**, clique em **CSV**.
    * Para **Delimitador**, selecione **guia**.
    * Para **Codificação**, selecione **UTF-8**.
    
    Clique em **Criar**. O portal agora adiciona a saída e testa a conexão.
    
## <a name="run-the-stream-analytics-job"></a>Executar o trabalho do Stream Analytics

1. Para executar um trabalho do Stream Analytics, você deve executar uma consulta da guia **Consulta**. Para este tutorial, você pode executar a consulta de exemplo, substituindo os espaços reservados pelos aliases de entrada e saída de trabalho, conforme mostrado na captura de tela abaixo.

    ![Executar consulta](./media/data-lake-store-stream-analytics/run.query.png "Executar consulta")

2. Clique em **Salvar** na parte superior da tela e, depois, em **Visão geral**, clique em **Iniciar**. Na caixa de diálogo, selecione **Hora Personalizada** e, em seguida, defina a data e a hora atuais.

    ![Definir o tempo de trabalho](./media/data-lake-store-stream-analytics/run.query.2.png "Definir o tempo de trabalho")

    Clique em **Iniciar** para iniciar o teste. Pode levar até dois minutos para o trabalho ser iniciado.

3. Para acionar o trabalho de forma a escolher a os dados do blob, copie um arquivo de dados de exemplo para o contêiner de blob. Você pode obter um arquivo de dados de exemplo do [Repositório Git do Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt). Para este tutorial, vamos copiar o arquivo **vehicle1_09142014.csv**. Você pode usar vários clientes, como o [Gerenciador de Armazenamento do Azure](http://storageexplorer.com/), para carregar dados em um contêiner de blob.

4. Na guia **Visão geral**, em **Monitoramento**, veja como os dados foram processados.

    ![Monitorar o trabalho](./media/data-lake-store-stream-analytics/run.query.3.png "Monitorar o trabalho")

5. Por fim, você pode verificar se os dados de saída do trabalho estão disponíveis na conta do Data Lake Store. 

    ![Verificar a saída](./media/data-lake-store-stream-analytics/run.query.4.png "Verificar a saída")

    No painel Data Explorer, observe que a saída é gravada em uma pasta, conforme especificado nas configurações de saída do Data Lake Store (`streamanalytics/job/output/{date}/{time}`).  

## <a name="see-also"></a>Consulte também
* [Criar um cluster do HDInsight para usar o Repositório Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)

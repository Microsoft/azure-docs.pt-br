---
title: Transmitir dados de Stream Analytics para Data Lake Storage Gen1-Azure
description: Saiba como usar Azure Data Lake Storage Gen1 como uma saída para um trabalho de Azure Stream Analytics, com um cenário simples que lê dados de um blob de armazenamento do Azure.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/30/2018
ms.author: twooley
ms.openlocfilehash: 4c289ecb1d8471a7b99f1d4c85a0163de4d0c593
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91576209"
---
# <a name="stream-data-from-azure-storage-blob-into-azure-data-lake-storage-gen1-using-azure-stream-analytics"></a>Dados do Stream do Blob de armazenamento do Azure em Gen1 de armazenamento do Azure Data Lake usando o Azure Stream Analytics
Neste artigo, você aprenderá a usar Azure Data Lake Storage Gen1 como uma saída para um trabalho de Azure Stream Analytics. Este artigo demonstra um cenário simples que lê dados de um blob de armazenamento do Azure (entrada) e grava os dados no Data Lake armazenamento Gen1 (saída).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, você deve ter o seguinte:

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Conta de Armazenamento do Azure**. Você usará um contêiner de blob desta conta para os dados de entrada para um trabalho do Stream Analytics. Para esse tutorial, suponha que você tem uma conta de armazenamento chamada **storageforasa** e um contêiner na conta chamado **storageforasacontainer**. Depois de criar o contêiner, carregue um arquivo de dados de exemplo nele. 
  
* **Uma conta do Data Lake Storage Gen1**. Siga as instruções em [Introdução ao Azure Data Lake Storage Gen1 usando o portal do Azure](data-lake-store-get-started-portal.md). Vamos supor que você tenha uma conta do Data Lake Storage Gen1 chamada **myadlsg1**. 

## <a name="create-a-stream-analytics-job"></a>Criar um trabalho do Stream Analytics
Você começa ao criar um trabalho do Stream Analytics, que inclui uma fonte de entrada e um destino de saída. Para este tutorial, a fonte é um contêiner de BLOBs do Azure e o destino é Gen1 de armazenamento do Data Lake.

1. Entre no [Portal do Azure](https://portal.azure.com).

2. No painel à esquerda, clique em **Trabalhos do Stream Analytics** e, em seguida, clique em **Adicionar**.

    ![Criar um trabalho de Stream Analytics](./media/data-lake-store-stream-analytics/create.job.png "Criar um trabalho de Stream Analytics")

    > [!NOTE]
    > Certifique-se de criar o trabalho na mesma região que a conta de armazenamento, ou você estará sujeito a pagar pelos custos adicionais de mover os dados entre regiões.
    >

## <a name="create-a-blob-input-for-the-job"></a>Criar uma entrada de blob para o trabalho

1. Abra a página do trabalho do Stream Analytics, clique na guia **Entradas** no painel à esquerda e, em seguida, clique em **Adicionar**.

    ![Captura de tela da folha Stream Analytics trabalho com a opção entradas e a opção Adicionar entrada de fluxo chamada out.](./media/data-lake-store-stream-analytics/create.input.1.png "Adicionar uma entrada ao seu trabalho")

2. Na folha **Nova entrada**, forneça os valores a seguir.

    ![Captura de tela da folha armazenamento de BLOBs-nova entrada.](./media/data-lake-store-stream-analytics/create.input.2.png "Adicionar uma entrada ao seu trabalho")

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


## <a name="create-a-data-lake-storage-gen1-output-for-the-job"></a>Criar uma saída Gen1 de armazenamento do Data Lake para o trabalho

1. Abra a página do trabalho do Stream Analytics, clique o **saídas** , clique em **Add** e selecione **Gen1 de armazenamento do Data Lake**.

    ![Captura de tela da folha de trabalho Stream Analytics com a opção Outputs, Add Option e Data Lake Storage opção Gen 1 chamada out.](./media/data-lake-store-stream-analytics/create.output.1.png "Adicionar uma saída ao seu trabalho")

2. Na folha **Nova saída**, forneça os valores a seguir.

    ![Captura de tela da folha Data Lake Storage Gen 1 – nova saída com a opção autorizar chamada out.](./media/data-lake-store-stream-analytics/create.output.2.png "Adicionar uma saída ao seu trabalho")

    * Para **Alias de saída**, insira um nome exclusivo para essa saída de trabalho. Isso é um nome amigável utilizado em consultas para direcionar a saída de consulta para essa conta do Data Lake armazenamento Gen1.
    * Você será solicitado a autorizar o acesso à conta do Data Lake armazenamento Gen1. Clique em **autorizar**.

3. Na folha **Nova saída**, forneça os valores a seguir.

    ![Captura de tela da folha Data Lake Storage Gen 1 – nova saída.](./media/data-lake-store-stream-analytics/create.output.3.png "Adicionar uma saída ao seu trabalho")

   * Para **nome da conta**, selecione a conta Data Lake armazenamento Gen1 você já criou onde você deseja que o trabalho de saída a serem enviados ao.
   * Para **padrão de prefixo de caminho**, insira um caminho de arquivo usado para gravar seus arquivos na conta do Data Lake armazenamento Gen1 especificada.
   * Para **Formato de data**, se você usou um token de data no caminho do prefixo, pode selecionar o formato de data em que os arquivos estão organizados.
   * Para **Formato de hora**, se você usou um token de hora no caminho do prefixo, especifique o formato de hora em que os arquivos estão organizados.
   * Para **Formato de Serialização de Evento**, clique em **CSV**.
   * Para **Delimitador**, selecione **guia**.
   * Para **Codificação**, selecione **UTF-8**.
    
     Clique em **Criar**. O portal agora adiciona a saída e testa a conexão.
    
## <a name="run-the-stream-analytics-job"></a>Executar o trabalho do Stream Analytics

1. Para executar um trabalho de Stream Analytics, você deve executar uma consulta na guia **consulta** . Para este tutorial, você pode executar a consulta de exemplo substituindo os espaços reservados pelos aliases de entrada e saída do trabalho, conforme mostrado na captura de tela abaixo.

    ![Executar consulta](./media/data-lake-store-stream-analytics/run.query.png "Executar consulta")

2. Clique em **Salvar** na parte superior da tela e, depois, em **Visão geral**, clique em **Iniciar**. Na caixa de diálogo, selecione **Hora Personalizada** e, em seguida, defina a data e a hora atuais.

    ![Definir o tempo de trabalho](./media/data-lake-store-stream-analytics/run.query.2.png "Definir o tempo de trabalho")

    Clique em **Iniciar** para iniciar o teste. Pode levar até dois minutos para o trabalho ser iniciado.

3. Para acionar o trabalho de forma a escolher a os dados do blob, copie um arquivo de dados de exemplo para o contêiner de blob. Você pode obter um arquivo de dados de exemplo do [Repositório Git do Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt). Para este tutorial, vamos copiar o arquivo **vehicle1_09142014.csv**. Você pode usar vários clientes, como o [Gerenciador de Armazenamento do Azure](https://storageexplorer.com/), para carregar dados em um contêiner de blob.

4. Na guia **Visão geral**, em **Monitoramento**, veja como os dados foram processados.

    ![Monitorar o trabalho](./media/data-lake-store-stream-analytics/run.query.3.png "Monitorar o trabalho")

5. Por fim, você pode verificar que os dados de saída de trabalho estão disponíveis na conta do Data Lake armazenamento Gen1. 

    ![Verificar a saída](./media/data-lake-store-stream-analytics/run.query.4.png "Verificar a saída")

    No painel Data Explorer, observe que a saída é gravada em um caminho de pasta conforme especificado nas configurações de saída do Data Lake Storage Gen1 (`streamanalytics/job/output/{date}/{time}`).  

## <a name="see-also"></a>Confira também
* [Criar um cluster de HDInsight para usar o Data Lake armazenamento Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

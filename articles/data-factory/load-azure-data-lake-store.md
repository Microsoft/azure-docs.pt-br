---
title: Carregar dados no Azure Data Lake Storage Gen1 usando o Azure Data Factory | Microsoft Docs
description: Usar o Azure Data Factory para copiar dados no Azure Data Lake Storage Gen1
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: jingwang
ms.openlocfilehash: 522b9743af28dedb2aec5682a1ae95b9d52ad2d9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60548358"
---
# <a name="load-data-into-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Carregar dados no Azure Data Lake Storage Gen1, usando o Azure Data Factory

O [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) (conhecido anteriormente como Azure Data Lake Store) é um repositório de hiperescala que abrange toda a empresa para cargas de trabalho analíticas de big data. O Data Lake Storage Gen1 permite que você capture dados de qualquer tamanho, tipo e velocidade de ingestão. Os dados são capturados em um único local para análise operacional e exploratória.

O Azure Data Factory é um serviço de integração de dados baseado em nuvem completamente gerenciado. Você pode usar o serviço para preencher o lago com dados do sistema existente e economizar tempo ao criar soluções de análise.

O Azure Data Factory oferece os seguintes benefícios para carregar dados no Data Lake Storage Gen1:

* **Fácil de configurar**: Um assistente intuitivo de 5 etapas sem nenhum script necessário.
* **Suporte de armazenamento de dados avançados**: Suporte interno para um conjunto avançado de armazenamentos de dados baseado em nuvem e locais. Para obter uma lista detalhada, consulte a tabela de [Suporte para repositórios de dados](copy-activity-overview.md#supported-data-stores-and-formats).
* **Seguro e em conformidade**: Dados são transferidos por HTTPS ou ExpressRoute. A presença do serviço global garante que os dados nunca saiam do limite geográfico.
* **Alto desempenho**: Até a velocidade de carregamento de dados de 1 GB/s em Gen1 de armazenamento do Data Lake. Para obter detalhes, consulte [Desempenho da atividade de cópia](copy-activity-performance.md).

Este artigo mostra como usar a ferramenta Copiar Dados do Data Factory para _carregar dados do Amazon S3 no Data Lake Storage Gen1_. Você pode seguir as etapas semelhantes para copiar dados de outros tipos de armazenamentos de dados.

> [!NOTE]
> Confira mais informações em [Copiar dados para ou do Data Lake Storage Gen1 usando o Azure Data Factory](connector-azure-data-lake-store.md).

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure: Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Conta do data Lake armazenamento Gen1: Se você não tiver uma conta do Data Lake armazenamento Gen1, consulte as instruções em [criar uma conta do Data Lake armazenamento Gen1](../data-lake-store/data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account).
* Amazon S3: Este artigo mostra como copiar dados do Amazon S3. Você pode usar outros repositórios de dados seguindo as etapas semelhantes.

## <a name="create-a-data-factory"></a>Criar uma data factory

1. No menu à esquerda, selecione **Criar um recurso** > **Analytics** > **Data Factory**:
   
   ![Seleção de Data Factory no painel "Novo"](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na página **Novo data factory**, forneça os valores para os campos que estão mostrados na imagem a seguir: 
      
   ![Página de novo data factory](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
    * **Nome**: Insira um nome globalmente exclusivo para o Azure Data Factory. Se você receber o erro "Nome do data factory \"LoadADLSG1Demo\" não está disponível", digite outro nome para o data factory. Por exemplo, use o nome _**seunome**_**ADFTutorialDataFactory**. Tente criar o data factory novamente. Para ver as regras de nomenclatura de artefatos do Data Factory, confira [Regras de nomenclatura do Data Factory](naming-rules.md).
    * **Assinatura**: Selecione a assinatura do Azure para criar o Data Factory. 
    * **Grupo de recursos**: Selecione um grupo de recursos existente na lista suspensa ou selecione a opção **Criar novo** e insira o nome de um grupo de recursos. Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Versão**: Selecione **V2**.
    * **Localização**: Selecione o local para o data factory. Somente os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados que são usados pela data factory podem estar em outros locais e regiões. Esses armazenamentos de dados incluem Azure Data Lake Storage Gen1, Azure Storage, Banco de Dados SQL do Azure e assim por diante.

3. Selecione **Criar**.
4. Após a conclusão da criação, vá para o seu data factory. Você verá a home page **Data Factory** conforme mostrado na imagem a seguir: 
   
   ![Página inicial do data factory](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

   Selecione o bloco **Autor & Monitor** para iniciar o aplicativo de integração de dados em uma guia separada.

## <a name="load-data-into-data-lake-storage-gen1"></a>Carregar dados no Data Lake Storage Gen1

1. Na página **Introdução**, selecione o bloco **Copy Data** para iniciar a ferramenta Copy Data: 

   ![Bloco Ferramenta Copy Data](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. Na página **Propriedades**, especifique Copy Data, especifique **CopyFromAmazonS3ToADLS** para o campo **Nome da tarefa** e clique em **Avançar**:

    ![Página Propriedades](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. Na página **Armazenamento de dados de origem**, clique em **+Criar nova conexão**:

    ![Página Armazenamento de dados de origem](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
    
    Selecione **Amazon S3** e **Continuar**
    
    ![Página Armazenamento de dados de origem s3](./media/load-data-into-azure-data-lake-store/source-data-store-page-s3.png)
    
4. Na página **Especificar conexão Amazon S3**, faça o seguinte: 
   1. Especifique o valor da **ID da chave de acesso**.
   2. Especifique o valor da **chave de acesso secreta**.
   3. Selecione **Concluir**.
   
      ![Especifique a conta do Amazon S3](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
   
   4. Você verá uma nova conexão. Selecione **Avançar**.
   
   ![Especifique a conta do Amazon S3](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account-created.png)
   
5. Na página **Escolher arquivo de entrada ou pasta** página, navegue até a pasta e o arquivo que você deseja copiar. Selecione a pasta/arquivo, selecione **Escolher**e, em seguida, selecione **Avançar**:

    ![Escolha a pasta ou arquivo de entrada](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. Escolha o comportamento de cópia marcando as opções **Copiar arquivos recursivamente** e **Cópia binária** (copiar arquivos como-são). Selecione **Avançar**:

    ![Especifique a pasta de saída](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)
    
7. Na pagina **Armazenamento de dados de destino**, clique em **+ Criar nova conexão**, selecione **Azure Data Lake Storage Gen1** e depois **Continuar**:

    ![Página Armazenamento de dados de destino](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

8. Na página **Novo Serviço Vinculado (Azure Data Lake Storage Gen1)**, execute as seguintes etapas: 

   1. Selecione sua conta do Data Lake Storage Gen1 para o **nome da conta do Data Lake Store**.
   2. Especifique o **Locatário** e selecione Concluir.
   3. Selecione **Avançar**.
   
   > [!IMPORTANT]
   > Neste passo a passo, você deve usar uma identidade gerenciada para recursos do Azure para autenticar a conta do Data Lake Storage Gen1. Verifique se você concedeu as permissões corretas no Data Lake Store Gen1 ao MSI seguindo [estas instruções](connector-azure-data-lake-store.md#managed-identity).
   
   ![Especifique a conta do Data Lake Storage Gen1](./media/load-data-into-azure-data-lake-store/specify-adls.png)
9. Em **Escolher o arquivo de saída ou pasta** insira **copyfroms3** como o nome da pasta de saída e selecione **Avançar**: 

    ![Especifique a pasta de saída](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)

10. Na página **Configurações**, selecione **Avançar**:

    ![Página Configurações](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. Na página **Resumo**, examine as configurações e selecione **Avançar**:

    ![Página Resumo](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. Na **Página Implantação**, selecione **Monitor** para monitorar o pipeline (tarefa):

    ![Página Implantação](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. Observe que a guia **Monitor** à esquerda é selecionada automaticamente. A coluna **Ações** inclui links para exibir detalhes da execução da atividade e executar o pipeline novamente:

    ![Monitorar execuções de pipeline](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. Para exibir as execuções de atividade associadas com a execução do pipeline, selecione o link **Exibir as Execuções de Atividade** na coluna **Ações**. Há apenas uma atividade (atividade de cópia) no pipeline, assim, você vê apenas uma entrada. Para voltar à exibição de execuções de pipeline, selecione o link **Pipelines** na parte superior. Selecione **Atualizar** para atualizar a lista. 

    ![Monitorar execuções de atividade](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. Para monitorar os detalhes de execução de cada atividade de cópia, selecione o link **Detalhes** em **Ações** no modo de exibição de monitoramento de atividades. Você pode monitorar detalhes como o volume de dados copiados da fonte para o coletor, taxa de transferência de dados, etapas de execução com duração correspondente e configurações usadas:

    ![Monitorar detalhes da execução da atividade](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. Verifique se os dados são copiados para a conta do Data Lake Storage Gen1: 

    ![Verifique a saída do Data Lake Storage Gen1](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>Próximas etapas

Confira o seguinte artigo para saber mais sobre o suporte do Data Lake Storage Gen1: 

> [!div class="nextstepaction"]
>[Conector do Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)

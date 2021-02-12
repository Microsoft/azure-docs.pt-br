---
title: Carregar dados no Azure Data Lake Store Gen2
description: Usar o Azure Data Factory para copiar dados para o Azure Data Lake Storage Gen2
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/09/2020
ms.openlocfilehash: ca9ca495f2b3449b5aeb933bbd8d312fc9341fd9
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94554110"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-with-azure-data-factory"></a>Carregar dados no Azure Data Lake Storage Gen2 com o Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

O Azure Data Lake Storage Gen2 é um conjunto de recursos dedicados à análise de Big Data, criado no [armazenamento de Blobs do Azure](../storage/blobs/storage-blobs-introduction.md). Ele permite que você faça interface com seus dados usando os paradigmas de sistema de arquivos e armazenamento de objetos.

Azure Data Factory (ADF) é um serviço de integração de dados baseado em nuvem totalmente gerenciado. É possível usar o serviço para preencher o Data Lake com dados de um conjunto avançado de armazenamentos de dados locais e baseados em nuvem e economizar tempo ao compilar as soluções de análise. Para obter uma lista detalhada de conectores com suporte, consulte a tabela de [Suporte para repositórios de dados](copy-activity-overview.md#supported-data-stores-and-formats).

O Azure Data Factory oferece uma solução de movimentação de dados gerenciados de expansão. Devido à arquitetura de expansão do ADF, é possível ingerir dados com alta taxa de transferência. Para obter detalhes, consulte [Desempenho da atividade de cópia](copy-activity-performance.md).

Este artigo descreve como usar a ferramenta Copiar Dados do Data Factory para carregar dados do _serviço Amazon Web Services S3_ no _Azure Data Lake Store Gen2_. Você pode seguir as etapas semelhantes para copiar dados de outros tipos de armazenamentos de dados.

>[!TIP]
>Para copiar dados do Azure Data Lake Storage Gen1 para o Gen2, consulte [este passo a passo específico](load-azure-data-lake-storage-gen2-from-gen1.md).

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure: Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Conta de armazenamento do Azure com Data Lake Storage Gen2 habilitado: se você não tiver uma conta de armazenamento, [crie uma conta](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).
* Conta de AWS com um bucket S3 que contém dados: este artigo mostra como copiar dados do Amazon S3. Você pode usar outros repositórios de dados seguindo as etapas semelhantes.

## <a name="create-a-data-factory"></a>Criar uma data factory

1. No menu à esquerda, selecione **criar um recurso**  >  **integração**  >  **Data Factory** :
   
   ![Seleção de Data Factory no painel "Novo"](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Na página **novo data Factory** , forneça valores para os seguintes campos:
 
    * **Name** : Insira um nome globalmente exclusivo para o Azure Data Factory. Se você receber o erro "o nome do data Factory *nomedoseudatafactory* não está disponível", insira um nome diferente para o data Factory. Por exemplo, use o nome _**seunome**_**ADFTutorialDataFactory**. Tente criar o data factory novamente. Para ver as regras de nomenclatura de artefatos do Data Factory, confira [Regras de nomenclatura do Data Factory](naming-rules.md).
    * **Assinatura** : Selecione a assinatura do Azure para criar o Data Factory. 
    * **Grupo de Recursos** : Selecione um grupo de recursos existente na lista suspensa ou selecione a opção **Criar novo** e insira o nome de um grupo de recursos. Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/management/overview.md).  
    * **Versão** : Selecione **V2**.
    * **Localização** : Selecione o local para o data factory. Somente os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados que são usados pela data factory podem estar em outros locais e regiões. 

3. Selecione **Criar**.

4. Após a conclusão da criação, vá para o seu data factory. Você verá a home page **Data Factory** conforme mostrado na imagem a seguir: 
   
   ![Página inicial do data factory](./media/doc-common-process/data-factory-home-page.png)

   Selecione o bloco **Autor & Monitor** para iniciar o aplicativo de integração de dados em uma guia separada.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Carregar dados no Azure Data Lake Store Gen2

1. Na página de **introdução** , selecione o bloco **copiar dados** para iniciar a ferramenta de copiar dados.

2. Na página **Propriedades** , especifique **CopyFromAmazonS3ToADLS** para o campo **nome da tarefa** e selecione **Avançar**.

    ![Página Propriedades](./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png)
3. Na página **armazenamento de dados de origem** , clique em **+ criar nova conexão**. Selecione **Amazon S3** na galeria do conector e selecione **continuar**.
    
    ![Página Armazenamento de dados de origem s3](./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png)
    
4. Na página **novo serviço vinculado (Amazon S3)** , execute as seguintes etapas:

   1. Especifique o valor da **ID da chave de acesso**.
   2. Especifique o valor da **chave de acesso secreta**.
   3. Clique em **Testar conectividade** para validar as configurações e selecione **Criar**.

      ![Especifique a conta do Amazon S3](./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png)
   4. Você verá que uma nova conexão AmazonS3 será criada. Selecione **Avançar**. 

5. Na página **Escolher arquivo de entrada ou pasta** página, navegue até a pasta e o arquivo que você deseja copiar. Selecione a pasta/arquivo e selecione **escolher**.

    ![Escolha a pasta ou arquivo de entrada](./media/load-azure-data-lake-storage-gen2/choose-input-folder.png)

6. Especifique o comportamento de cópia verificando as opções de cópia **recursiva** e **binária** . Selecione **Avançar**.

    ![Captura de tela mostra a escolha do arquivo ou da pasta de entrada onde você pode selecionar cópia binária e recursivamente.](./media/load-azure-data-lake-storage-gen2/specify-binary-copy.png)
    
7. Na página **armazenamento de dados de destino** , clique em **+ criar nova conexão** e selecione **Azure data Lake Storage Gen2** e selecione **continuar**.

    ![Página Armazenamento de dados de destino](./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png)

8. Na página **novo serviço vinculado (Azure data Lake Storage Gen2)** , execute as seguintes etapas:

   1. Selecione sua conta do Data Lake Storage Gen2 compatível na lista suspensa "Nome da conta de armazenamento".
   2. Selecione **criar** para criar a conexão. Em seguida, selecione **Avançar**.   

        ![Especificar a conta do Azure Data Lake Storage Gen2](./media/load-azure-data-lake-storage-gen2/specify-azure-data-lake-storage.png)

9. Na página **escolher o arquivo de saída ou a pasta** , insira **copyfroms3** como o nome da pasta de saída e selecione **Avançar**. O ADF criará o sistema de arquivos ADLS Gen2 e as subpastas correspondentes durante a cópia, se ela não existir.

    ![Captura de tela mostra o caminho da pasta que você inseriu.](./media/load-azure-data-lake-storage-gen2/specify-adls-path.png)

10. Na página **configurações** , selecione **Avançar** para usar as configurações padrão.

    ![Página de configurações](./media/load-azure-data-lake-storage-gen2/copy-settings.png)

11. Na página **Resumo** , examine as configurações e selecione **Avançar**.

    ![Página Resumo](./media/load-azure-data-lake-storage-gen2/copy-summary.png)

12. Na **página Implantação** , selecione **Monitorar** para monitorar o pipeline (tarefa). 
 
13. Quando a execução do pipeline for concluída com êxito, você verá uma execução de pipeline disparada por um gatilho manual. Você pode usar os links na coluna **PIPELINE NAME** para ver detalhes da atividade e executar o pipeline novamente.

    ![Monitorar execuções de pipeline](./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png)

14. Para ver as execuções de atividade associadas à execução do pipeline, selecione o link **CopyFromAmazonS3ToADLS** na coluna nome do pipeline. Para obter detalhes sobre a operação de cópia, selecione o link **detalhes** (ícone de óculos) na coluna nome da atividade. Você pode monitorar detalhes como o volume de dados copiados da origem para o coletor, a taxa de transferência de dados, as etapas de execução com a duração correspondente e a configuração usada.
 
    ![Monitorar execuções de atividade](./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png)
    
    ![Monitorar detalhes da execução da atividade](./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png)

15. Para atualizar a exibição, selecione Atualizar. Selecione **Todas as execuções de pipeline** na parte superior para voltar à exibição Execuções de Pipeline.

16. Verifique se os dados são copiados para a conta do Data Lake Store Gen2.

## <a name="next-steps"></a>Próximas etapas

* [Visão geral da atividade de cópia](copy-activity-overview.md)
* [Conector do Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md)

---
title: Carregar dados no Azure Synapse Analytics
description: Usar Azure Data Factory para copiar dados para o Azure Synapse Analytics
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/29/2020
ms.openlocfilehash: 94f5884cb9d1886a9e7aa6b09817b26bba6d47f7
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100370709"
---
# <a name="load-data-into-azure-synapse-analytics-by-using-azure-data-factory"></a>Carregar dados no Azure Synapse Analytics usando Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A [análise de Synapse do Azure](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) é um banco de dados baseado em nuvem e escalável que é capaz de processar grandes volumes de data, relacionais e não relacionais. O Azure Synapse Analytics é criado com base na arquitetura MPP (processamento paralelo maciço) otimizada para cargas de trabalho de data warehouse empresarial. Ele oferece a elasticidade da nuvem com a flexibilidade de dimensionar o armazenamento e a computação de modo independente.

A introdução ao Azure Synapse Analytics agora está mais fácil do que nunca quando você usa Azure Data Factory. O Azure Data Factory é um serviço de integração de dados baseado em nuvem completamente gerenciado. Você pode usar o serviço para popular uma análise de Synapse do Azure com dados do seu sistema existente e economizar tempo ao criar soluções de análise.

O Azure Data Factory oferece os seguintes benefícios para carregar dados no Azure Synapse Analytics:

* **Fácil de configurar**: um assistente intuitivo de cinco etapas sem necessidade de script.
* **Suporte avançado de armazenamento de dados**: suporte interno para um conjunto avançado de armazenamentos de dados locais e baseados em nuvem. Para obter uma lista detalhada, consulte a tabela de [Suporte para repositórios de dados](copy-activity-overview.md#supported-data-stores-and-formats).
* **Seguro e em conformidade**: os dados são transferidos via HTTPS ou ExpressRoute. A presença do serviço global garante que os dados nunca saiam do limite geográfico.
* **Desempenho inigualável usando o polybase**: o polybase é a maneira mais eficiente de mover dados para a análise de Synapse do Azure. Use o recurso de objeto binário em etapas para obter velocidades de alta carga de todos os tipos de armazenamentos de dados, incluindo armazenamento Azure Blob e Data Lake Store. (O polybase dá suporte ao armazenamento de BLOBs do Azure e Azure Data Lake Store por padrão.) Para obter detalhes, consulte [desempenho da atividade de cópia](copy-activity-performance.md).

Este artigo mostra como usar a ferramenta de Copiar Dados de Data Factory para _carregar dados do banco de dados SQL do Azure para o Azure Synapse Analytics_. Você pode seguir as etapas semelhantes para copiar dados de outros tipos de armazenamentos de dados.

> [!NOTE]
> Para obter mais informações, consulte [copiar dados de ou para o Azure Synapse Analytics usando Azure data Factory](connector-azure-sql-data-warehouse.md).

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure: Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Análise de Synapse do Azure: a data warehouse contém os dados copiados do SQL Database. Se você não tiver uma análise de Synapse do Azure, consulte as instruções em [criar uma análise de Synapse do Azure](../synapse-analytics/sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md).
* Banco de Dados SQL do Azure: Este tutorial copia os dados do conjunto de dados de amostra do Adventure Works LT no Banco de Dados SQL do Azure. Você pode criar um banco de dados de exemplo no Banco de Dados SQL seguindo as instruções em [Criar um banco de dados de exemplo no Banco de Dados SQL do Azure](../azure-sql/database/single-database-create-quickstart.md).
* Conta de armazenamento do Azure: armazenamento do Azure é usado como _blob_ de processo de reparo na operação de cópia em massa. Se você não tiver uma conta de armazenamento do Azure, confira as instruções em [Criar uma conta de armazenamento](../storage/common/storage-account-create.md).

## <a name="create-a-data-factory"></a>Criar um data factory

1. No menu à esquerda, selecione **Criar um recurso** > **Dados + Análise** > **Data Factory**:

2. Na página **novo data Factory** , forneça valores para os seguintes itens:

    * **Nome**: insira *LoadSQLDWDemo* para o nome. O nome do seu data factory deve ser * globalmente exclusivo. Se você receber o erro "o nome do data Factory ' LoadSQLDWDemo ' não está disponível", insira um nome diferente para o data factory. Por exemplo, use o nome _**seunome**_**ADFTutorialDataFactory**. Tente criar o data factory novamente. Para ver as regras de nomenclatura de artefatos do Data Factory, confira [Regras de nomenclatura do Data Factory](naming-rules.md).
    * **Assinatura**: Selecione a assinatura do Azure para criar o Data Factory. 
    * **Grupo de Recursos**: Selecione um grupo de recursos existente na lista suspensa ou selecione a opção **Criar novo** e insira o nome de um grupo de recursos. Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/management/overview.md).  
    * **Versão**: Selecione **V2**.
    * **Localização**: Selecione o local para o data factory. Somente os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados que são usados pela data factory podem estar em outros locais e regiões. Esses armazenamentos de dados incluem Azure Data Lake Store, Azure Storage, Banco de Dados SQL do Azure e assim por diante.

3. Selecione **Criar**.
4. Após a conclusão da criação, vá para o seu data factory. Você verá a home page **Data Factory** conforme mostrado na imagem a seguir:

   ![Página inicial do data factory](./media/doc-common-process/data-factory-home-page.png)

   Selecione o bloco **Autor & Monitor** para iniciar o aplicativo de integração de dados em uma guia separada.

## <a name="load-data-into-azure-synapse-analytics"></a>Carregar dados no Azure Synapse Analytics

1. Na página de **introdução** , selecione o bloco **copiar dados** para iniciar a ferramenta de copiar dados.

2. Na página **Propriedades** , especifique **CopyFromSQLToSQLDW** para o campo **nome da tarefa** e selecione **Avançar**.

    ![Página Propriedades](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)

3. Na página **Armazenamento de dados de origem**, conclua as etapas a seguir:
    >[!TIP]
    >Neste tutorial, você usa a *autenticação do SQL* como o tipo de autenticação para o armazenamento de dados de origem, mas pode escolher outros métodos de autenticação com suporte:*entidade de serviço* e *identidade gerenciada* , se necessário. Veja as seções correspondentes [neste artigo](./connector-azure-sql-database.md#linked-service-properties) para obter detalhes.
    >Para armazenar segredos de armazenamentos de dados com segurança, também é recomendável usar um Azure Key Vault. Veja [este artigo](./store-credentials-in-key-vault.md) para obter ilustrações detalhadas.

    a. clique em **+ criar nova conexão**.

    b. Selecione **Banco de Dados SQL do Azure** da galeria e selecione **Continuar**. Você pode digitar "SQL" na caixa de pesquisa para filtrar os conectores.

    ![Selecione o BD SQL do Azure](./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png)

    c. Na página **novo serviço vinculado** , selecione o nome do servidor e o nome do BD na lista suspensa e especifique o nome de usuário e a senha. Clique em **Testar conectividade** para validar as configurações e selecione **Criar**.

    ![Configure o Banco de Dados SQL do Azure](./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png)

    d. Selecione o serviço vinculado recém-criado como fonte, depois clique **Avançar**.

4. Em **Selecione as tabelas das quais copiar os dados ou use uma página de consulta personalizada**, insira **SalesLT** para filtrar as tabelas. Escolha a caixa **(selecionar tudo)** para usar todas as tabelas da cópia e, em seguida, selecione **Avançar**.

    ![Selecionar tabelas de origem](./media/load-azure-sql-data-warehouse/select-source-tables.png)

5. Na página **aplicar filtro** , especifique as configurações ou selecione **Avançar**.

6. Na página **Armazenamento de dados de destino**, conclua as etapas a seguir:
    >[!TIP]
    >Neste tutorial, você usa a *autenticação do SQL* como o tipo de autenticação para o armazenamento de dados de destino, mas pode escolher outros métodos de autenticação com suporte:*entidade de serviço* e *identidade gerenciada* , se necessário. Veja as seções correspondentes [neste artigo](./connector-azure-sql-data-warehouse.md#linked-service-properties) para obter detalhes.
    >Para armazenar segredos de armazenamentos de dados com segurança, também é recomendável usar um Azure Key Vault. Veja [este artigo](./store-credentials-in-key-vault.md) para obter ilustrações detalhadas.

    a. Clique em **+ Criar nova conexão** para adicionar uma conexão

    b. Selecione **Azure Synapse Analytics** na galeria e selecione **continuar**.

    ![Selecionar o Azure Synapse Analytics](./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png)

    c. Na página **novo serviço vinculado** , selecione o nome do servidor e o nome do BD na lista suspensa e especifique o nome de usuário e a senha. Clique em **Testar conectividade** para validar as configurações e selecione **Criar**.

    ![Configurar o Azure Synapse Analytics](./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png)

    d. Selecione o serviço vinculado criado recentemente como coletor e clique em **Avançar**.

7. Na página **Mapeamento de tabela**, examine o conteúdo e selecione **Avançar**. Um mapeamento de tabela inteligente é exibido. As tabelas de origem são mapeadas para as tabelas de destino com base nos nomes de tabela. Se a tabela de origem não existir no destino, por padrão o Azure Data Factory cria uma tabela de destino com o mesmo nome por padrão. Você também pode mapear uma tabela de origem para uma tabela de destino existente.

   ![Página Mapeamento de tabela](./media/load-azure-sql-data-warehouse/table-mapping.png)

8. Na página **mapeamento de coluna** , examine o conteúdo e selecione **Avançar**. O mapeamento de tabela inteligente é baseado no nome da coluna. Se você deixar o Data Factory criar automaticamente as tabelas, a conversão do tipo de dados pode ocorrer quando houver incompatibilidades entre a origem e os armazenamentos de destino. Se houver uma conversão de tipo de dados sem suporte entre a coluna de origem e de destino, você verá uma mensagem de erro próximo à tabela correspondente.

    ![Página Mapeamento de coluna](./media/load-azure-sql-data-warehouse/schema-mapping.png)

9. Na página **Configurações**, execute as seguintes etapas:

    a. Na seção **Configurações de preparo**, clique em **+ Novo** para criar um novo armazenamento de preparo. O armazenamento é usado para preparar os dados antes de serem carregados no Azure Synapse Analytics usando o PolyBase. Depois que a cópia for concluída, os dados provisórios no armazenamento de BLOBs do Azure serão limpos automaticamente.

    b. Na página **novo serviço vinculado** , selecione sua conta de armazenamento e selecione **criar** para implantar o serviço vinculado.

    c. Desmarque a opção **usar tipo padrão** e, em seguida, selecione **Avançar**.

    ![Configure o PolyBase](./media/load-azure-sql-data-warehouse/configure-polybase.png)

10. Na página **Resumo** , examine as configurações e selecione **Avançar**.

    ![Página Resumo](./media/load-azure-sql-data-warehouse/summary-page.png)

11. Na **página Implantação**, selecione **Monitorar** para monitorar o pipeline (tarefa). 
 
12. Observe que a guia **Monitor** à esquerda é selecionada automaticamente. Quando a execução do pipeline for concluída com êxito, selecione o link **CopyFromSQLToSQLDW** na coluna **nome do pipeline** para exibir os detalhes da execução da atividade ou para executar novamente o pipeline.

    [![Monitorar execuções de pipeline](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png)](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png#lightbox)

13. Para voltar à exibição de execuções de pipeline, selecione o link **Todos os pipelines são executados** na parte superior. Selecione **Atualizar** para atualizar a lista.

    ![Monitorar execuções de atividade](./media/load-azure-sql-data-warehouse/activity-monitoring.png)

14. Para monitorar os detalhes de execução de cada atividade de cópia, selecione o link **detalhes** (ícone de óculos) em **nome da atividade** na exibição execuções de atividade. Você pode monitorar detalhes como o volume de dados copiados da fonte para o coletor, taxa de transferência de dados, etapas de execução com duração correspondente e configurações usadas.
    ![Monitorar detalhes da execução da atividade primeiro](./media/load-azure-sql-data-warehouse/monitor-activity-run-details-1.png)

    ![Detalhes da execução de atividade do monitor em segundo lugar](./media/load-azure-sql-data-warehouse/monitor-activity-run-details-2.png)

## <a name="next-steps"></a>Próximas etapas

Vá para o seguinte artigo para saber mais sobre o suporte do Azure Synapse Analytics:

> [!div class="nextstepaction"]
>[Conector do Azure Synapse Analytics](connector-azure-sql-data-warehouse.md)
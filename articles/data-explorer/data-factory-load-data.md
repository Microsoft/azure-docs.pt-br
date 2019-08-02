---
title: Copiar dados do Azure Data Factory para o Data Explorer do Azure
description: Neste tópico, você aprenderá a ingestão de dados (carga) no Data Explorer do Azure usando a ferramenta de cópia do Azure Data Factory
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 2142fbf03daa6667b20db43f9212a2b5e6d7dd44
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657528"
---
# <a name="copy-data-to-azure-data-explorer-using-azure-data-factory"></a>Copiar dados para o Data Explorer do Azure usando o Azure Data Factory 

Gerenciador de dados do Azure é um serviço de análise de dados rápido, totalmente gerenciado para análise em tempo real em grandes volumes de dados de streaming de várias fontes, como aplicativos, sites e dispositivos IoT. Explorar dados e identificar padrões e anomalias para melhorar os produtos, aprimorar as experiências do cliente, de forma iterativa monitorar dispositivos e melhorar as operações. Explore novas perguntas e obtenha respostas em minutos. O Azure Data Factory é um serviço de integração de dados baseado em nuvem completamente gerenciado. Você pode usar o serviço para preencher seu banco de dados do Data Explorer do Azure com os dados do seu sistema existente e economizar tempo ao criar suas soluções de análise.

O Azure Data Factory oferece os seguintes benefícios para carregar dados no Data Explorer do Azure:

* **Fácil de configurar**: Um assistente intuitivo de cinco etapas sem nenhum script necessário.
* **Suporte de armazenamento de dados avançados**: Suporte interno para um conjunto avançado de armazenamentos de dados baseado em nuvem e locais. Para obter uma lista detalhada, consulte a tabela de [Suporte para repositórios de dados](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats).
* **Seguro e em conformidade**: Dados são transferidos por HTTPS ou ExpressRoute. A presença do serviço global garante que os dados nunca saiam do limite geográfico.
* **Alto desempenho**: Até a velocidade de carregamento de dados de 1 GB/s no Data Explorer do Azure. Para obter detalhes, consulte [Desempenho da atividade de cópia](/azure/data-factory/copy-activity-performance).

Este artigo mostra como usar a ferramenta Copiar dados do Data Factory para carregar dados do Amazon S3 no Data Explorer do Azure. Você pode seguir etapas semelhantes para copiar dados de outros armazenamentos de dados, como [armazenamento de BLOBs do Azure](/azure/data-factory/connector-azure-blob-storage), [banco de dados SQL](/azure/data-factory/connector-azure-sql-database), [Azure SQL Data Warehouse](/azure/data-factory/connector-azure-sql-data-warehouse), [Google BigQuery](/azure/data-factory/connector-google-bigquery),[Oracle](/azure/data-factory/connector-oracle), e [sistema de arquivos](/azure/data-factory/connector-file-system).

## <a name="prerequisites"></a>Pré-requisitos

* Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* [Um cluster do Gerenciador de dados do Azure e o banco de dados](create-cluster-database-portal.md)
* Fonte de dados.

## <a name="create-a-data-factory"></a>Criar uma data factory

1. Selecione o **criar um recurso** botão (+) no canto superior esquerdo do portal > **Analytics** > **Data Factory**.

   ![Cria um novo data factory](media/data-factory-load-data/create-adf.png)

1. No **novo data factory** página, forneça valores para os campos a seguir e, em seguida, selecione **criar**.

    ![Página de novo data factory](media/data-factory-load-data/my-new-data-factory.png)

    **Configuração**  | **Descrição do campo**
    |---|---|
    | **Nome** | Insira um nome globalmente exclusivo para seu data factory. Se você receber o erro *"nome do Data factory \"LoadADXDemo\" não está disponível"* , insira um nome diferente para o data factory. Para regras de nomenclatura de artefatos do Data Factory, consulte [regras de nomenclatura do Data Factory](/azure/data-factory/naming-rules).|
    | **Assinatura** | Selecione a assinatura do Azure para criar o Data Factory. |
    | **Grupo de recursos** | Selecione **criar novo** e insira o nome de um novo grupo de recursos. Selecione **usar existente**, se você tiver um grupo de recursos. |
    | **Versão** | Selecione **V2** |
    | **Localidade** | Selecione o local para o data factory. Somente os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados que são usados pelo data factory podem estar em outros locais ou regiões. |
    | | |

1. Selecione as notificações na barra de ferramentas para monitorar o processo de criação. Após a criação estiver concluída, vá para a fábrica de dados que você criou. O **Data Factory** é aberta a página inicial.

   ![Página inicial do data factory](media/data-factory-load-data/data-factory-home-page.png)

1. Selecione o **autor & Monitor** lado a lado para iniciar o aplicativo em uma guia separada.

## <a name="load-data-into-azure-data-explorer"></a>Carregar dados no Data Explorer do Azure

Dados podem ser carregados de muitos tipos de [armazenamentos de dados](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) no Data Explorer do Azure. Este tópico fornece detalhes sobre o carregamento de dados do Amazon S3.

Há duas maneiras de carregar dados no Data Explorer do Azure usando o Azure Data Factory:

* Interface de usuário do Azure Data Factory - [ **autor** guia](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)
* [O Azure Data Factory **Copy Data** ferramenta](/azure/data-factory/quickstart-create-data-factory-copy-data-tool) usados neste artigo.

### <a name="copy-data-from-amazon-s3-source"></a>Copiar dados do Amazon S3 (origem)

1. No **vamos começar** página, selecione o **copiar dados** lado a lado para iniciar a ferramenta Copy Data.

   ![Bloco ferramenta Copy data](media/data-factory-load-data/copy-data-tool-tile.png)

1. No **propriedades** , especifique **nome da tarefa** e selecione **próxima**.

    ![Copiar de propriedades da fonte](media/data-factory-load-data/copy-from-source.png)

1. No **armazenamento de dados de origem** , clique em **+ criar nova conexão**.

    ![Fonte de dados cria conexão](media/data-factory-load-data/source-create-connection.png)

1. Selecione **Amazon S3**e, em seguida, selecione **continuar**

    ![Novo serviço vinculado](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. No **novo serviço vinculado do (Amazon S3)** página, faça as seguintes etapas:

    ![Especifique um serviço vinculado do Amazon S3](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    * Especificar **nome** do novo serviço vinculado.
    * Selecione **conectar por meio do tempo de execução de integração** valor na lista suspensa.
    * Especifique o valor da **ID da chave de acesso**.
    * Especifique o valor da **chave de acesso secreta**.
    * Selecione **Testar Conexão** para testar a conexão de serviço vinculado que você criou.
    * Selecione **Concluir**.

1. No **armazenamento de dados de origem** página, você verá sua nova conexão de AmazonS31. Selecione **Avançar**.

   ![Conexão criada de armazenar dados de origem](media/data-factory-load-data/source-data-store-created-connection.png)

1. No **escolha o arquivo de entrada ou a pasta** página:

    1. Navegue até o arquivo/pasta que você deseja copiar. Selecione a pasta/arquivo.
    1. Selecione o comportamento de cópia conforme necessário. Manter **cópia binária** desmarcada.
    1. Selecione **Avançar**.

    ![Escolha a pasta ou arquivo de entrada](media/data-factory-load-data/source-choose-input-file.png)

1. No **configurações de formatos de arquivo** página Selecione as configurações relevantes do arquivo e clique em **próxima**.

   ![Escolha a pasta ou arquivo de entrada](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Copiar dados para o Gerenciador de dados do Azure (destino)

Azure Data Explorer novo serviço vinculado é criado para copiar os dados na tabela de destino Data Explorer do Azure (coletor) especificados abaixo.

1. No **armazenamento de dados de destino** página, você pode usar os dados existentes armazenar a conexão ou especificar um novo repositório de dados, clique em **+ criar nova conexão**.

    ![Página Armazenamento de dados de destino](media/data-factory-load-data/destination-create-connection.png)

1. No **novo serviço vinculado** página, selecione **Data Explorer do Azure**e, em seguida, selecione **continuar**

    ![Selecione um Gerenciador de dados do Azure - novo serviço vinculado](media/data-factory-load-data/adx-select-new-linked-service.png)

1. No **novo serviço vinculado (Gerenciador de dados do Azure)** página, faça as seguintes etapas:

    ![Novo serviço vinculado de ADX](media/data-factory-load-data/adx-new-linked-service.png)

   * Selecione **nome** Data Explorer do Azure para o serviço vinculado.
   * Na **método de seleção de conta**: 
        * Selecione o **assinatura do Azure** botão de opção e selecione sua **assinatura do Azure** conta. Em seguida, selecione suas **Cluster**. Observe na lista suspensa será listar somente os clusters que pertencem ao usuário.
        * Como alternativa, selecione **inserir manualmente** botão de opção e digite sua **ponto de extremidade**.
    * Especifique o **locatário**.
    * Insira **ID da entidade de serviço**.
    * Selecione **chave da entidade de serviço** botão e insira **chave de entidade de serviço**.
    * Selecione suas **banco de dados** no menu suspenso. Como alternativa, selecione **editar** caixa de seleção e insira seu nome de banco de dados.
    * Selecione **Testar Conexão** para testar a conexão de serviço vinculado que você criou. Se você pode se conectar à sua instalação, uma marca de seleção verde **Conexão bem-sucedida** será exibida.
    * Selecione **concluir** para concluir a criação do serviço vinculado.

    > [!NOTE]
    > A entidade de serviço é usado pelo Azure Data Factory para acessar o serviço de Gerenciador de dados do Azure. Para a entidade de serviço [criar um Azure Active Directory (Azure AD) de entidade de serviço](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal). Não use o **Azure Key Vault** método.

1. O **armazenamento de dados de destino** é aberta. A conexão de dados do Data Explorer do Azure criado está disponível para uso. Selecione **próxima** para configurar a conexão.

    ![Armazenamento de dados de destino ADX](media/data-factory-load-data/destination-data-store.png)

1. Na **mapeamento de tabela**, defina o nome da tabela de destino e selecione **próxima**.

    ![Mapeamento de tabela do conjunto de dados de destino](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. No **mapeamento de coluna** página:
    * O primeiro mapeamento é executado pelo AAD de acordo com a [mapeamento de esquema do ADF](/azure/data-factory/copy-activity-schema-and-type-mapping)
        * Defina as **mapeamentos de coluna** para a tabela de destino do Azure Data Factory. O mapeamento padrão é exibido de origem à tabela de destino do ADF.
        * Desmarque as colunas que você não precisa definir o mapeamento de coluna.
    * O segundo mapeamento ocorre quando esses dados tabulares são incluídos no Data Explorer do Azure. O mapeamento é executado de acordo com a [regras de mapeamento de CSV](/azure/kusto/management/mappings#csv-mapping). Observe que, mesmo se a fonte de dados não estava no formato CSV, ADF converteu os dados em um formato tabular, portanto, o mapeamento de CSV é o mapeamento relevante apenas neste estágio.
        * Sob **propriedades do Gerenciador de dados do Azure (Kusto) coletor** adicionar relevante **nome do mapeamento de ingestão** (opcional) então esse mapeamento de coluna pode ser usado.
        * Se **nome do mapeamento de ingestão** não for especificado, ordem de mapeamento de "por nome" definida no **mapeamentos de coluna** seção ocorrerá. Se o mapeamento de "por nome" falhar, o Data Explorer do Azure tentará ingestão dos dados em uma ordem de "posição de coluna por" (maps por posição como padrão).
    * Selecione **Avançar**

    ![Mapeamento de coluna do conjunto de dados de destino](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. Na página de **Configurações** :
    * Defina o relevantes **as configurações de tolerância de falhas**.
    * **As configurações de desempenho**: Habilitar preparo não é aplicável. **Configurações avançadas** incluir considerações de custo. Deixe como está não se precisa de nenhuma específicos.
    * Selecione **Avançar**.

    ![Configurações de cópia de dados](media/data-factory-load-data/copy-data-settings.png)

1. Na **resumo**, examine as configurações e selecione **próxima**.

    ![Copiar dados de resumidos](media/data-factory-load-data/copy-data-summary.png)

1. No **página implantação**:
    * Selecione **Monitor** para alternar para o **Monitor** guia e ver o status do pipeline (fluxo de dados, erros e progresso).
    * Selecione **Pipeline editar** editar serviços vinculados, conjuntos de dados e pipelines.
    * Selecione **concluir** a tarefa de cópia completa de dados

    ![Página Implantação](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [conector do Gerenciador de dados do Azure](/azure/data-factory/connector-azure-data-explorer) no Azure Data Factory.

* Saiba mais sobre como editar serviços vinculados, conjuntos de dados e pipelines na [IU do Data Factory](/azure/data-factory/quickstart-create-data-factory-portal).

* Saiba mais sobre [consultas de Data Explorer do Azure](/azure/data-explorer/web-query-data) para consultar os dados.

---
title: Carregar terabytes de dados no Azure Synapse Analytics
description: Demonstra como 1 TB de dados podem ser carregados no Azure Synapse Analytics em 15 minutos com Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: a6c133c0-ced2-463c-86f0-a07b00c9e37f
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 68c9e594201f0d0689a289e13f2c4ebf909c2f87
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96457100"
---
# <a name="load-1-tb-into-azure-synapse-analytics-under-15-minutes-with-data-factory"></a>Carregue 1 TB no Azure Synapse Analytics em 15 minutos com Data Factory
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [copiar dados de ou para a análise de Synapse do Azure usando data Factory](../connector-azure-sql-data-warehouse.md).


O [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) é um banco de dados baseado em nuvem, escalável e escalonável capaz de processar grandes volumes de data, relacionais e não relacionais.  Baseado na arquitetura MPP (processamento paralelo maciço), o Azure Synapse Analytics é otimizado para cargas de trabalho de data warehouse empresarial.  Ele oferece a elasticidade da nuvem com a flexibilidade de dimensionar o armazenamento e a computação de modo independente.

A introdução ao Azure Synapse Analytics agora está mais fácil do que nunca usando **Azure data Factory**.  Azure Data Factory é um serviço de integração de dados baseado em nuvem totalmente gerenciado, que pode ser usado para preencher a análise de Synapse do Azure com os dados do seu sistema existente e economizar tempo valioso ao avaliar a análise de Synapse do Azure e criar soluções de análise. Aqui estão os principais benefícios do carregamento de dados no Azure Synapse Analytics usando Azure Data Factory:

* **Fácil de configurar**: assistente intuitivo de 5 etapas sem nenhum script necessário.
* **Suporte a armazenamento de dados avançado**: suporte interno para um conjunto avançado de armazenamentos de dados locais e baseados em nuvem.
* **Seguro e compatível**: os dados são transferidos por HTTPS ou ExpressRoute e a presença global do serviço garante que os dados nunca saiam do limite geográfico
* O **desempenho inigualável usando o polybase** – usar o polybase é a maneira mais eficiente de mover dados para a análise de Synapse do Azure. Usando o recurso de blob de preparo, você pode obter velocidades de alta carga de todos os tipos de armazenamentos de dados além do Armazenamento de Blobs do Azure, ao qual o Polybase dá suporte por padrão.

Este artigo mostra como usar Data Factory assistente de cópia para carregar dados de 1 TB do armazenamento de BLOBs do Azure para a análise de Synapse do Azure em menos de 15 minutos, com mais de 1,2 GBps de taxa de transferência.

Este artigo fornece instruções passo a passo para mover dados para o Azure Synapse Analytics usando o assistente de cópia.

> [!NOTE]
>  Para obter informações gerais sobre os recursos de Data Factory na movimentação de dados de/para o Azure Synapse Analytics, consulte [mover dados de e para a análise de Synapse do Azure usando](data-factory-azure-sql-data-warehouse-connector.md) o artigo Azure data Factory.
>
> Você também pode criar pipelines usando o Visual Studio, o PowerShell, etc. Consulte [tutorial: copiar dados do blob do Azure para o banco](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) de dados SQL do Azure para obter uma rápida explicação com instruções passo a passo para usar a atividade de cópia no Azure data Factory.  
>
>

## <a name="prerequisites"></a>Pré-requisitos
* Armazenamento de Blobs do Azure: esse teste usa o Armazenamento de Blobs do Azure (GRS) para armazenar o conjunto de dados de teste do TPC-H.  Se você não tiver uma conta de armazenamento do Azure, aprenda [como criar uma conta de armazenamento](../../storage/common/storage-account-create.md).
* Dados do [TPC-H](http://www.tpc.org/tpch/): usaremos o TPC-H como o conjunto de dados de teste.  Para fazer isso, você precisa usar `dbgen` do kit de ferramentas do TPC-H, o que ajuda você a gerar o conjunto de dados.  Você pode baixar código-fonte para `dbgen` de [Ferramentas TPC](http://www.tpc.org/tpc_documents_current_versions/current_specifications5.asp) e compilá-lo por conta própria ou então baixar o binário compilado de [GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TPCHTools).  Execute dbgen.exe com os comandos a seguir para gerar um arquivo simples de 1 TB para a tabela `lineitem` dividido em 10 arquivos:

  * `Dbgen -s 1000 -S **1** -C 10 -T L -v`
  * `Dbgen -s 1000 -S **2** -C 10 -T L -v`
  * …
  * `Dbgen -s 1000 -S **10** -C 10 -T L -v`

    Agora, copie os arquivos gerados para o Blob do Azure.  Consulte [Mover dados de e para um sistema de arquivos local usando o Azure Data Factory](data-factory-onprem-file-system-connector.md) para saber como fazer isso usando a Cópia do ADF.    
* Azure Synapse Analytics: esse experimento carrega dados no Azure Synapse Analytics criado com 6.000 DWUs

    Consulte [criar uma análise de Synapse do Azure](../../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md) para obter instruções detalhadas sobre como criar um banco de dados de análise do Azure Synapse.  Para obter o melhor desempenho de carga possível na análise de Synapse do Azure usando o polybase, escolhemos o número máximo de unidades de data warehouse (DWUs) permitido na configuração de desempenho, que é 6.000 DWUs.

  > [!NOTE]
  > Ao carregar do blob do Azure, o desempenho do carregamento de dados é diretamente proporcional ao número de DWUs que você configura para a análise Synapse do Azure:
  >
  > Carregando 1 TB em 1.000 DWU o Azure Synapse Analytics leva 87 minutos (~ 200 MBps de taxa de transferência) carregando 1 TB em 2.000 DWU a análise de Synapse do Azure leva de 46 minutos (~ 380 de taxa de transferência) carregando 1 TB em 6.000 DWU o Azure Synapse Analytics leva 14 minutos (~ 1,2 de taxa de transferência)
  >
  >

    Para criar um pool SQL dedicado com 6.000 DWUs, mova o controle deslizante de desempenho até o lado direito:

    ![Controle deslizante de Desempenho](media/data-factory-load-sql-data-warehouse/performance-slider.png)

    Um banco de dados existente que não está configurado com 6.000 DWUs poderá ser escalado verticalmente por você usando o Portal do Azure.  Navegue até o banco de dados no Portal do Azure e há um botão **Dimensionar** no painel **Visão Geral** mostrado na imagem a seguir:

    ![Botão Dimensionar](media/data-factory-load-sql-data-warehouse/scale-button.png)    

    Clique no botão **Dimensionar** para abrir o painel seguinte, mova o controle deslizante para o valor máximo e clique no botão **Salvar**.

    ![Caixa de diálogo Dimensionar](media/data-factory-load-sql-data-warehouse/scale-dialog.png)

    Esse experimento carrega dados na análise de Synapse do Azure usando a `xlargerc` classe de recurso.

    Para obter a melhor taxa de transferência possível, a cópia precisa ser executada usando um usuário do Azure Synapse Analytics pertencente à `xlargerc` classe de recurso.  Saiba como fazer isso seguindo [Alterar um exemplo de classe de recurso de usuário](../../synapse-analytics/sql-data-warehouse/resource-classes-for-workload-management.md).  
* Crie um esquema de tabela de destino no banco de dados do Azure Synapse Analytics, executando a seguinte instrução DDL:

    ```SQL  
    CREATE TABLE [dbo].[lineitem]
    (
        [L_ORDERKEY] [bigint] NOT NULL,
        [L_PARTKEY] [bigint] NOT NULL,
        [L_SUPPKEY] [bigint] NOT NULL,
        [L_LINENUMBER] [int] NOT NULL,
        [L_QUANTITY] [decimal](15, 2) NULL,
        [L_EXTENDEDPRICE] [decimal](15, 2) NULL,
        [L_DISCOUNT] [decimal](15, 2) NULL,
        [L_TAX] [decimal](15, 2) NULL,
        [L_RETURNFLAG] [char](1) NULL,
        [L_LINESTATUS] [char](1) NULL,
        [L_SHIPDATE] [date] NULL,
        [L_COMMITDATE] [date] NULL,
        [L_RECEIPTDATE] [date] NULL,
        [L_SHIPINSTRUCT] [char](25) NULL,
        [L_SHIPMODE] [char](10) NULL,
        [L_COMMENT] [varchar](44) NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    ```
  Com as etapas de pré-requisito concluídas, agora estamos prontos para configurar a atividade de cópia usando o Assistente de Cópia.

## <a name="launch-copy-wizard"></a>Iniciar o Assistente de cópia
1. Faça logon no [Portal do Azure](https://portal.azure.com).
2. Clique em **Criar um recurso** no canto superior esquerdo, clique em **Inteligência + análise** e clique em **Data Factory**.
3. No painel **Novo data factory**:

   1. Insira **LoadIntoSQLDWDataFactory** para o **nome**.
       O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber o erro: o **nome do data Factory "LoadIntoSQLDWDataFactory" não está disponível**, altere o nome do data Factory (por exemplo, yournameLoadIntoSQLDWDataFactory) e tente criar novamente. Veja o tópico [Data Factory - regras de nomenclatura](data-factory-naming-rules.md) para ver as regras de nomenclatura para artefatos do Data Factory.  
   2. Selecione sua **assinatura** do Azure.
   3. Em relação ao Grupo de Recursos, execute uma das seguintes etapas:
      1. Selecione **Usar existente** para selecionar um grupo de recursos existente.
      2. Selecione **Criar novo** e insira um nome para um grupo de recursos.
   4. Selecione um **local** para o data factory.
   5. Marque a caixa de seleção **Fixar no painel** na parte inferior da folha.  
   6. Clique em **Criar**.
4. Depois que a criação for concluída, você verá a folha **Data Factory**, conforme mostrado na seguinte imagem:

   ![Página inicial do data factory](media/data-factory-load-sql-data-warehouse/data-factory-home-page-copy-data.png)
5. Na home page do Data Factory, clique no bloco **Copiar dados** para iniciar o **Assistente de Cópia**.

   > [!NOTE]
   > Se você vir que o navegador da Web está bloqueado em "Autorizando...", desabilite/desmarque a configuração **Bloquear cookies de terceiros e dados de site** (ou) mantenha-a habilitada, crie uma exceção para **login.microsoftonline.com** e tente iniciar o assistente novamente.
   >
   >

## <a name="step-1-configure-data-loading-schedule"></a>Etapa 1: configurar o cronograma de carregamento de dados
A primeira etapa é configurar o cronograma de carregamento de dados.  

Na página **Propriedades** :

1. Insira **CopyFromBlobToAzureSqlDataWarehouse** para o **Nome da tarefa**
2. Selecione opção **Executar uma vez agora**.   
3. Clique em **Próximo**.  

    ![Assistente de Cópia – página Propriedades](media/data-factory-load-sql-data-warehouse/copy-wizard-properties-page.png)

## <a name="step-2-configure-source"></a>Etapa 2: Configurar a origem
Esta seção mostra as etapas para configurar a origem: Blob do Azure contendo os arquivos de item de linha de 1 TB TPC-H.

1. Selecione o **Armazenamento de Blobs do Azure** como o armazenamento de dados e clique em **Próximo**.

    ![Assistente de Cópia – selecionar página de origem](media/data-factory-load-sql-data-warehouse/select-source-connection.png)

2. Preencha as informações de conexão para a conta de Armazenamento de Blobs do Azure e, em seguida, clique em **Próximo**.

    ![Assistente de Cópia – informações de conexão de origem](media/data-factory-load-sql-data-warehouse/source-connection-info.png)

3. Escolha a **pasta** que contém os arquivos de item de linha TPC-H e clique em **Próximo**.

    ![Assistente de Cópia – selecionar pasta de entrada](media/data-factory-load-sql-data-warehouse/select-input-folder.png)

4. Ao clicar em **Próximo**, as configurações de formato de arquivo são detectadas automaticamente.  Verifique se o delimitador de coluna é ' | ' em vez da vírgula padrão ', '.  Clique em **Próximo** depois de ter visualizado os dados.

    ![Assistente de Cópia – configurações de formato de arquivo](media/data-factory-load-sql-data-warehouse/file-format-settings.png)

## <a name="step-3-configure-destination"></a>Etapa 3: Configurar o destino
Esta seção mostra como configurar a tabela Destination: `lineitem` no banco de dados do Azure Synapse Analytics.

1. Escolha **Azure Synapse Analytics** como o repositório de destino e clique em **Avançar**.

    ![Assistente de Cópia – selecionar o armazenamento de dados de destino](media/data-factory-load-sql-data-warehouse/select-destination-data-store.png)

2. Preencha as informações de conexão do Azure Synapse Analytics.  Certifique-se de especificar o usuário que é membro da função `xlargerc` (consulte a seção **pré-requisitos** para obter instruções detalhadas) e clique em **Próximo**.

    ![Assistente de Cópia – informações de conexão de destino](media/data-factory-load-sql-data-warehouse/destination-connection-info.png)

3. Escolha a tabela de destino e clique em **Próximo**.

    ![Assistente de Cópia – página de mapeamento de tabela](media/data-factory-load-sql-data-warehouse/table-mapping-page.png)

4. Na página de mapeamento de esquema, deixe a opção "Aplicar o mapeamento de coluna" desmarcada e clique em **Avançar**.

## <a name="step-4-performance-settings"></a>Etapa 4: Configurações de desempenho

A opção **Permitir polybase** é marcada por padrão.  Clique em **Próximo**.

![Assistente de Cópia – página de mapeamento de esquema](media/data-factory-load-sql-data-warehouse/performance-settings-page.png)

## <a name="step-5-deploy-and-monitor-load-results"></a>Etapa 5: Implantar e monitorar os resultados de carga
1. Clique no botão **Concluir** para implantar.

    ![Assistente de cópia – página Resumo 1](media/data-factory-load-sql-data-warehouse/summary-page.png)

2. Depois que a implantação for concluída, clique em `Click here to monitor copy pipeline` para monitorar o andamento da execução da cópia. Selecione o pipeline de cópia criado na lista **Janelas de Atividade**.

    ![Assistente de cópia – página de resumo 2](media/data-factory-load-sql-data-warehouse/select-pipeline-monitor-manage-app.png)

    Você pode exibir os detalhes de execução da cópia no **Gerenciador de Janelas de Atividade** no painel direito, incluindo o volume de dados lidos de origem e gravado no destino, a duração e a taxa de transferência média da execução.

    Como você pode ver na captura de tela a seguir, a cópia de 1 TB do armazenamento de BLOBs do Azure para o Azure Synapse Analytics levou 14 minutos, atingindo efetivamente 1,22 GBps de taxa de transferência!

    ![Assistente de Cópia – caixa de diálogo de êxito](media/data-factory-load-sql-data-warehouse/succeeded-info.png)

## <a name="best-practices"></a>Práticas recomendadas
Aqui estão algumas práticas recomendadas para executar seu banco de dados do Azure Synapse Analytics:

* Use uma classe de recurso maior durante o carregamento em um ÍNDICE COLUMNSTORE CLUSTERIZADO.
* Para junções mais eficientes, considere usar a distribuição de hash por uma coluna selecionada em vez da distribuição round robin padrão.
* Para velocidades de carga, considere usar o heap para dados transitórios.
* Crie estatísticas depois de concluir o carregamento para o Azure Synapse Analytics.

Consulte [práticas recomendadas para o Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-best-practices.md) para obter detalhes.

## <a name="next-steps"></a>Próximas etapas
* [Assistente de Cópia do Data Factory](data-factory-copy-wizard.md) – este artigo fornece detalhes sobre o Assistente de Cópia.
* [Guia de ajuste e desempenho da Atividade de Cópia](data-factory-copy-activity-performance.md) – este artigo contém as medições de desempenho de referência e o guia de ajuste.
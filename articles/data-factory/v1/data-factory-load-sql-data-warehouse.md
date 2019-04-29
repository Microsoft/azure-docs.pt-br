---
title: Carregar terabytes de dados no SQL Data Warehouse | Microsoft Docs
description: Demonstra como 1 TB de dados podem ser carregado no Azure SQL Data Warehouse em 15 minutos com o Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: a6c133c0-ced2-463c-86f0-a07b00c9e37f
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: e275411f9fd9dfb672bb0815e83e37bcd5d1dda9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60825175"
---
# <a name="load-1-tb-into-azure-sql-data-warehouse-under-15-minutes-with-data-factory"></a>Carregar 1 TB no SQL Data Warehouse do Azure em menos de 15 minutos com o Data Factory
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [Copiar dados para ou do SQL Data Warehouse do Azure usando o Data Factory](../connector-azure-sql-data-warehouse.md).


O [Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) é um banco de dados baseado em nuvem e expansível com capacidade de processar volumes imensos de dados, relacionais e não relacionais.  Criado em arquitetura MPP (processamento paralelo maciço), o SQL Data Warehouse é otimizado para cargas de trabalho do data warehouse corporativas.  Ele oferece a elasticidade da nuvem com a flexibilidade de dimensionar o armazenamento e a computação de modo independente.

A introdução ao Azure SQL Data Warehouse agora é mais fácil do que nunca, usando o **Azure Data Factory**.  O Azure Data Factory é um serviço de integração de dados baseado em nuvem totalmente gerenciado, que pode ser usado para popular um SQL Data Warehouse com os dados de seu sistema existente e economizar tempo valioso ao avaliar o SQL Data Warehouse e criar suas soluções de análise. Aqui estão os principais benefícios de carregar dados no Azure SQL Data Warehouse usando o Azure Data Factory:

* **Fácil de configurar**: Assistente intuitivo de 5 etapas, sem nenhum script necessário.
* **Suporte de armazenamento de dados avançados**: suporte interno para um conjunto avançado de armazenamentos de dados locais e baseados em nuvem.
* **Seguro e compatível**: os dados são transferidos por HTTPS ou ExpressRoute e a presença global do serviço garante que os dados nunca saiam do limite geográfico
* **Desempenho incomparável usando PolyBase** – usar o Polybase é a maneira mais eficiente para mover dados para o SQL Data Warehouse. Usando o recurso de blob de preparo, você pode obter velocidades de alta carga de todos os tipos de armazenamentos de dados além do Armazenamento de Blobs do Azure, ao qual o Polybase dá suporte por padrão.

Este artigo mostra como usar o Assistente de Cópia do Data Factory para carregar 1 TB de dados do Armazenamento de Blobs do Azure no SQL Data Warehouse do Azure em menos de 15 minutos, com uma vazão de dados superior a 1,2 GBps.

Este artigo fornece instruções passo a passo para mover dados no Azure SQL Data Warehouse usando o Assistente de Cópia.

> [!NOTE]
>  Para obter informações gerais sobre as funcionalidades do Data Factory para movimentação de dados bidirecionalmente no SQL Data Warehouse do Azure, consulte o artigo [Mover dados bidirecionalmente no SQL Data Warehouse do Azure usando o Azure Data Factory](data-factory-azure-sql-data-warehouse-connector.md).
>
> Você também pode criar pipelines usando o Portal do Azure, Visual Studio, PowerShell, etc. Consulte [Tutorial: Copiar dados do Blob do Azure para o Banco de Dados SQL do Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter um breve passo a passo com instruções sobre como usar a Atividade de cópia na Azure Data Factory.  
>
>

## <a name="prerequisites"></a>Pré-requisitos
* Armazenamento de Blobs do Azure: esse teste usa o Armazenamento de Blobs do Azure (GRS) para armazenar o conjunto de dados de teste do TPC-H.  Se você não tiver uma conta de armazenamento do Azure, aprenda [como criar uma conta de armazenamento](../../storage/common/storage-quickstart-create-account.md).
* Dados do [TPC-H](http://www.tpc.org/tpch/): usaremos o TPC-H como o conjunto de dados de teste.  Para fazer isso, você precisa usar `dbgen` do kit de ferramentas do TPC-H, o que ajuda você a gerar o conjunto de dados.  Você pode baixar código-fonte para `dbgen` de [Ferramentas TPC](http://www.tpc.org/tpc_documents_current_versions/current_specifications.asp) e compilá-lo por conta própria ou então baixar o binário compilado de [GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TPCHTools).  Execute dbgen.exe com os comandos a seguir para gerar um arquivo simples de 1 TB para a tabela `lineitem` dividido em 10 arquivos:

  * `Dbgen -s 1000 -S **1** -C 10 -T L -v`
  * `Dbgen -s 1000 -S **2** -C 10 -T L -v`
  * …
  * `Dbgen -s 1000 -S **10** -C 10 -T L -v`

    Agora, copie os arquivos gerados para o Blob do Azure.  Consulte [Mover dados de e para um sistema de arquivos local usando o Azure Data Factory](data-factory-onprem-file-system-connector.md) para saber como fazer isso usando a Cópia do ADF.    
* Azure SQL Data Warehouse: este experimento carrega dados no Azure SQL Data Warehouse criado com 6.000 DWUs

    Consulte [Criar um Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) para obter instruções detalhadas sobre como criar um banco de dados do SQL Data Warehouse.  Para obter o melhor desempenho de carregamento possível no SQL Data Warehouse usando o Polybase, escolhemos o número máximo de DWUs (Unidades de Data Warehouse) permitido na configuração Desempenho, que é de 6.000 DWUs.

  > [!NOTE]
  > Ao carregar do Blob do Azure, o desempenho de carregamento de dados será diretamente proporcional ao número de DWUs que você configurar no SQL Data Warehouse:
  >
  > Carregar 1 TB em um SQL Data Warehouse com 1.000 DWUs leva 87 minutos (vazão de dados de cerca de 200 MBps) Carregar 1 TB em um SQL Data Warehouse com 2.000 DWUs leva 46 minutos (vazão de dados de cerca de 380 MBps) Carregar 1 TB em um SQL Data Warehouse com 6.000 DWUs leva 14 minutos (vazão de dados de cerca de 1,2 GBps)
  >
  >

    Para criar um SQL Data Warehouse com 6.000 DWUs, mova o controle deslizante de desempenho para a direita, até o final:

    ![Controle deslizante de Desempenho](media/data-factory-load-sql-data-warehouse/performance-slider.png)

    Um banco de dados existente que não está configurado com 6.000 DWUs poderá ser escalado verticalmente por você usando o Portal do Azure.  Navegue até o banco de dados no Portal do Azure e há um botão **Dimensionar** no painel **Visão Geral** mostrado na imagem a seguir:

    ![Botão Dimensionar](media/data-factory-load-sql-data-warehouse/scale-button.png)    

    Clique no botão **Dimensionar** para abrir o painel seguinte, mova o controle deslizante para o valor máximo e clique no botão **Salvar**.

    ![Caixa de diálogo Dimensionar](media/data-factory-load-sql-data-warehouse/scale-dialog.png)

    Este experimento carrega dados no Azure SQL Data Warehouse usando a classe de recurso `xlargerc`.

    Para obter a melhor taxa de transferência possível, a cópia precisa ser executada usando um usuário do SQL Data Warehouse pertencente à classe de recurso `xlargerc`.  Saiba como fazer isso seguindo [Alterar um exemplo de classe de recurso de usuário](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md).  
* Crie um esquema de tabela de destino no banco de dados do Azure SQL Data Warehouse executando a instrução DDL a seguir:

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
       O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber o erro: **O nome da data factory “LoadIntoSQLDWDataFactory” não está disponível**, altere o nome da data factory (por exemplo, yournameLoadIntoSQLDWDataFactory) e tente criá-la novamente. Veja o tópico [Data Factory - regras de nomenclatura](data-factory-naming-rules.md) para ver as regras de nomenclatura para artefatos do Data Factory.  
   2. Selecione sua **assinatura**do Azure.
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

## <a name="step-1-configure-data-loading-schedule"></a>Etapa 1: Configurar o cronograma de carregamento de dados
A primeira etapa é configurar o cronograma de carregamento de dados.  

Na página **Propriedades** :

1. Insira **CopyFromBlobToAzureSqlDataWarehouse** para o **Nome da tarefa**
2. Selecione opção **Executar uma vez agora**.   
3. Clique em **Avançar**.  

    ![Assistente de Cópia – página Propriedades](media/data-factory-load-sql-data-warehouse/copy-wizard-properties-page.png)

## <a name="step-2-configure-source"></a>Etapa 2: Configurar fonte
Esta seção mostra as etapas para configurar a origem: Blob do Azure que contém de 1 TB de arquivos de item de linha TPC-H.

1. Selecione o **Armazenamento de Blobs do Azure** como o armazenamento de dados e clique em **Próximo**.

    ![Assistente de Cópia – selecionar página de origem](media/data-factory-load-sql-data-warehouse/select-source-connection.png)

2. Preencha as informações de conexão para a conta de Armazenamento de Blobs do Azure e, em seguida, clique em **Próximo**.

    ![Assistente de Cópia – informações de conexão de origem](media/data-factory-load-sql-data-warehouse/source-connection-info.png)

3. Escolha a **pasta** que contém os arquivos de item de linha TPC-H e clique em **Próximo**.

    ![Assistente de Cópia – selecionar pasta de entrada](media/data-factory-load-sql-data-warehouse/select-input-folder.png)

4. Ao clicar em **Próximo**, as configurações de formato de arquivo são detectadas automaticamente.  Certifique-se de que delimitador de coluna é ' | 'em vez da vírgula ',' usada como padrão.  Clique em **Próximo** depois de ter visualizado os dados.

    ![Assistente de Cópia – configurações de formato de arquivo](media/data-factory-load-sql-data-warehouse/file-format-settings.png)

## <a name="step-3-configure-destination"></a>Etapa 3: Configurar o destino
Esta seção mostra como configurar o destino: tabela `lineitem` no banco de dados do Azure SQL Data Warehouse.

1. Escolha **Azure SQL Data Warehouse** como o repositório de destino e clique em **Próximo**.

    ![Assistente de Cópia – selecionar o armazenamento de dados de destino](media/data-factory-load-sql-data-warehouse/select-destination-data-store.png)

2. Preencha as informações de conexão do SQL Data Warehouse.  Certifique-se de especificar o usuário que é membro da função `xlargerc` (consulte a seção **pré-requisitos** para obter instruções detalhadas) e clique em **Próximo**.

    ![Assistente de Cópia – informações de conexão de destino](media/data-factory-load-sql-data-warehouse/destination-connection-info.png)

3. Escolha a tabela de destino e clique em **Próximo**.

    ![Assistente de Cópia – página de mapeamento de tabela](media/data-factory-load-sql-data-warehouse/table-mapping-page.png)

4. Na página de mapeamento de esquema, deixe a opção "Aplicar o mapeamento de coluna" desmarcada e clique em **Avançar**.

## <a name="step-4-performance-settings"></a>Etapa 4: Configurações de desempenho

A opção **Permitir polybase** é marcada por padrão.  Clique em **Avançar**.

![Assistente de Cópia – página de mapeamento de esquema](media/data-factory-load-sql-data-warehouse/performance-settings-page.png)

## <a name="step-5-deploy-and-monitor-load-results"></a>Etapa 5: Implantar e monitorar os resultados de carga
1. Clique no botão **Concluir** para implantar.

    ![Assistente de Cópia – página de resumo](media/data-factory-load-sql-data-warehouse/summary-page.png)

2. Depois que a implantação for concluída, clique em `Click here to monitor copy pipeline` para monitorar o andamento da execução da cópia. Selecione o pipeline de cópia criado na lista **Janelas de Atividade**.

    ![Assistente de Cópia – página de resumo](media/data-factory-load-sql-data-warehouse/select-pipeline-monitor-manage-app.png)

    Você pode exibir os detalhes de execução da cópia no **Gerenciador de Janelas de Atividade** no painel direito, incluindo o volume de dados lidos de origem e gravado no destino, a duração e a taxa de transferência média da execução.

    Como você pode ver na captura de tela a seguir, copiar 1 TB de armazenamento de BLOBs do Azure no SQL Data Warehouse levou 14 minutos, atingindo efetivamente a taxa de transferência de 1,22 GBps!

    ![Assistente de Cópia – caixa de diálogo de êxito](media/data-factory-load-sql-data-warehouse/succeeded-info.png)

## <a name="best-practices"></a>Práticas recomendadas
Aqui estão algumas práticas recomendadas para a execução de seu banco de dados do Azure SQL Data Warehouse:

* Use uma classe de recurso maior durante o carregamento em um ÍNDICE COLUMNSTORE CLUSTERIZADO.
* Para junções mais eficientes, considere usar a distribuição de hash por uma coluna selecionada em vez da distribuição round robin padrão.
* Para velocidades de carga, considere usar o heap para dados transitórios.
* Crie estatísticas depois de terminar de carregar o Azure SQL Data Warehouse.

Veja [Práticas Recomendadas para o SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-best-practices.md) para obter detalhes.

## <a name="next-steps"></a>Próximas etapas
* [Assistente de Cópia do Data Factory](data-factory-copy-wizard.md) – este artigo fornece detalhes sobre o Assistente de Cópia.
* [Guia de ajuste e desempenho da Atividade de Cópia](data-factory-copy-activity-performance.md) – este artigo contém as medições de desempenho de referência e o guia de ajuste.

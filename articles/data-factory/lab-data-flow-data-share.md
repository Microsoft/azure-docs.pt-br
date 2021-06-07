---
title: Integração de dados usando o Azure Data Factory e o Azure Data Share
description: Copiar, transformar e compartilhar dados usando o Azure Data Factory e o Azure Data Share
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 12/09/2020
ms.openlocfilehash: fa424f7e1f5e1f885dd433b8abc8aae1dc1bc206
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97006118"
---
# <a name="data-integration-using-azure-data-factory-and-azure-data-share"></a>Integração de dados usando o Azure Data Factory e o Azure Data Share

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

À medida que os clientes embarcam em seus projetos modernos de data warehouse e de análise, eles precisam não só de mais dados, como também de mais visibilidade sobre seus dados em todo o acervo de dados deles. Este workshop se aprofunda em como os aprimoramentos do Azure Data Factory e do Azure Data Share simplificam a integração e o gerenciamento de dados no Azure. 

Desde a habilitação do ETL/ELT sem código até a criação de uma exibição abrangente sobre seus dados, os aprimoramentos no Azure Data Factory capacitarão seus engenheiros de dados a introduzir mais dados com segurança e, portanto, mais valor, à sua empresa. Com o Azure Data Share, você poderá fazer compartilhamento business to business de uma maneira controlada.

Neste workshop, você usará o ADF (Azure Data Factory) para ingerir dados do Banco de Dados SQL do Azure no ADLS Gen2 (Azure Data Lake Storage Gen2). Após colocar os dados no lake, você os transformará por meio de fluxos de dados de mapeamento e do serviço de transformação nativo do data factory e os introduzirá no Azure Synapse Analytics. Em seguida, você compartilhará a tabela com os dados transformados junto com alguns dados adicionais usando o Azure Data Share. 

Os dados usados nesse laboratório são os dados de táxi da cidade de Nova York. Para importá-los em seu banco de dados no Banco de Dados SQL, baixe o [arquivo taxi-data bacpac](https://github.com/djpmsft/ADF_Labs/blob/master/sample-data/taxi-data.bacpac).

## <a name="prerequisites"></a>Pré-requisitos

* **Assinatura do Azure**: Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

* **Banco de Dados SQL do Azure**: Se você não tiver um BD SQL, aprenda a [criar uma conta do BD SQL](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal)

* **Conta de armazenamento do Azure Data Lake Storage Gen2**: se você não tiver uma conta de armazenamento do ADLS Gen2, aprenda a [criar uma conta de armazenamento do ADLS Gen2](../storage/common/storage-account-create.md).

* **Azure Synapse Analytics**: caso você não tenha um Azure Synapse Analytics, aprenda a [criar uma instância do Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md).

* **Azure Data Factory**: se você não tiver criado um data factory, confira como [criar um data factory](./quickstart-create-data-factory-portal.md).

* **Azure Data Share**: se você não tiver criado um compartilhamento de dados, confira como [criar um compartilhamento de dados](../data-share/share-your-data.md#create-a-data-share-account).

## <a name="set-up-your-azure-data-factory-environment"></a>Configurar seu ambiente do Azure Data Factory

Nesta seção, você aprenderá a acessar a ADF UX (experiência de usuário do Azure Data Factory) no portal do Azure. Quando estiver na ADF UX, você configurará três serviços vinculados para cada um dos armazenamentos de dados que estamos usando: Azure SQL DB, ADLS Gen2 e Azure Synapse Analytics.

Nos serviços vinculados do Azure Data Factory, defina as informações de conexão como recursos externos. No momento, o Azure Data Factory é compatível com mais de 85 conectores.

### <a name="open-the-azure-data-factory-ux"></a>Abrir a UX do Azure Data Factory

1. Abra o [portal do Azure](https://portal.azure.com) no Microsoft Edge ou no Google Chrome.
1. Usando a barra de pesquisa na parte superior da página, pesquise "Data Factories"

    ![Portal 1](media/lab-data-flow-data-share/portal1.png)
1. Clique no recurso do data factory para abrir sua folha de recurso.

    ![Portal 2](media/lab-data-flow-data-share/portal2.png)
1. Clique em **Criar e Monitorar** para abrir a UX do ADF. A UX do ADF também pode ser acessada em adf.azure.com.

    ![Portal 3](media/lab-data-flow-data-share/portal3.png)
1. Você será redirecionado à página inicial da UX do ADF. Essa página contém inícios rápidos, vídeos instrutivos e links para tutoriais para aprender conceitos de data factory. Para começar a criar, clique no ícone de lápis na barra lateral esquerda.

    ![Configuração do portal](media/lab-data-flow-data-share/configure1.png)

### <a name="create-an-azure-sql-database-linked-service"></a>Criar um serviço vinculado do Banco de Dados SQL do Azure

1. A página de criação é o lugar em que você cria recursos de data factory, como pipelines, conjuntos de dados, fluxos de dados, gatilhos e serviços vinculados. Para criar um serviço vinculado, clique no botão **Conexões** no canto inferior direito.

    ![Configuração do portal 2](media/lab-data-flow-data-share/configure2.png)
1. Na guia de conexões, clique em **Novo** para adicionar um novo serviço vinculado.

    ![Configuração do portal 3](media/lab-data-flow-data-share/configure3.png)
1. O primeiro serviço vinculado que você configurará é um BD SQL do Azure. Você pode usar a barra de pesquisa para filtrar a lista de armazenamento de dados. Clique no bloco **Banco de Dados SQL do Azure** e em continuar.

    ![Configuração do portal 4](media/lab-data-flow-data-share/configure-4.png)
1. No painel de configuração do BD SQL, insira "SQLDB" como o nome do serviço vinculado. Insira suas credenciais para permitir que o data factory se conecte ao seu banco de dados. Se estiver usando a autenticação SQL, insira o nome do servidor, o banco de dados, o nome de usuário e a senha. Você pode verificar se as informações de conexão estão corretas clicando em **Testar conexão**. Clique em **Criar** quando terminar.

    ![Configuração do portal 5](media/lab-data-flow-data-share/configure5.png)

### <a name="create-an-azure-synapse-analytics-linked-service"></a>Criar um serviço vinculado do Azure Synapse Analytics

1. Repita o mesmo processo para adicionar um serviço vinculado do Azure Synapse Analytics. Na guia de conexões, clique em **Novo**. Selecione o bloco do **Azure Synapse Analytics** e clique em Continuar.

    ![Configuração do portal 6](media/lab-data-flow-data-share/configure-6.png)
1. No painel de configuração do serviço vinculado, insira "SQLDW" como o nome do serviço vinculado. Insira suas credenciais para permitir que o data factory se conecte ao seu banco de dados. Se estiver usando a autenticação SQL, insira o nome do servidor, o banco de dados, o nome de usuário e a senha. Você pode verificar se as informações de conexão estão corretas clicando em **Testar conexão**. Clique em **Criar** quando terminar.

    ![Configuração do portal 7](media/lab-data-flow-data-share/configure-7.png)

### <a name="create-an-azure-data-lake-storage-gen2-linked-service"></a>Criar um serviço vinculado do Azure Data Lake Storage Gen2

1. O último serviço vinculado necessário para este laboratório é um Azure Data Lake Storage Gen2.  Na guia de conexões, clique em **Novo**. Selecione o bloco **Azure Data Lake Storage Gen2** e clique em continuar.

    ![Configuração do portal 8](media/lab-data-flow-data-share/configure8.png)
1. No painel de configuração do serviço vinculado, insira "ADLSGen2" como o nome do serviço vinculado. Se estiver usando a autenticação da Chave de conta, selecione a conta de armazenamento do ADLS Gen2 no menu suspenso **Nome da conta de armazenamento**. Você pode verificar se as informações de conexão estão corretas clicando em **Testar conexão**. Clique em **Criar** quando terminar.

    ![Configuração do portal 9](media/lab-data-flow-data-share/configure9.png)

### <a name="turn-on-data-flow-debug-mode"></a>Ativar modo de depuração do fluxo de dados

Na seção *Transformar dados usando o fluxo de dados de mapeamento*, você criará fluxos de dados de mapeamento. Uma melhor prática antes de criar fluxos de dados de mapeamento é ativar o modo de depuração, o que permite testar a lógica de transformação em segundos em um cluster do spark ativo.

Para ativar a depuração, clique no controle deslizante **Depuração do fluxo de dados** na barra superior da fábrica. Clique em OK quando a caixa de diálogo de confirmação for exibida. Levará cerca de 5 a 7 minutos para o cluster ser iniciado. Prossiga para *Ingerir dados do BD SQL do Azure para o ADLS Gen2 usando a atividade de cópia* enquanto ele está inicializando.

![Configuração do portal 10](media/lab-data-flow-data-share/configure10.png)

## <a name="ingest-data-using-the-copy-activity"></a>Ingerir dados usando a atividade de cópia

Nesta seção, você criará um pipeline com uma atividade de cópia que ingere uma tabela de um BD SQL do Azure para uma conta de armazenamento do ADLS Gen2. Você aprenderá a adicionar um pipeline, configurar um conjunto de dados e depurar um pipeline por meio da UX do ADF. O padrão de configuração usado nesta seção pode ser aplicado à cópia de um armazenamento de dados relacional para um armazenamento de dados baseado em arquivo.

No Azure Data Factory, um pipeline é um agrupamento lógico de atividades que juntas executam uma tarefa. Uma atividade define uma operação a ser executada em seus dados. Um conjunto de dados aponta para os dados que você deseja usar em um serviço vinculado.

### <a name="create-a-pipeline-with-a-copy-activity"></a>Criar um pipeline com uma atividade de cópia

1. No painel recursos de fábrica, clique no ícone de adição para abrir o novo menu de recurso. Selecione **Pipeline**.

    ![Cópia do portal 1](media/lab-data-flow-data-share/copy1.png)
1. Na guia **Geral** da tela do pipeline, dê ao pipeline um nome descritivo como "IngestAndTransformTaxiData".

    ![Cópia do portal 2](media/lab-data-flow-data-share/copy2.png)
1. No painel de atividades da tela do pipeline, abra a seção **Mover e Transformar** e arraste a atividade **Copiar dados** para a tela. Dê à atividade de cópia um nome descritivo como "IngestIntoADLS".

    ![Cópia do portal 3](media/lab-data-flow-data-share/copy3.png)

### <a name="configure-azure-sql-db-source-dataset"></a>Configurar o conjunto de dados de origem do BD SQL do Azure

1. Clique na guia **Origem** da atividade de cópia. Para criar um conjunto de dados, clique em **Novo**. Sua origem será a tabela "dbo.TripData" localizada no serviço vinculado "SQLDB" configurado anteriormente.

    ![Cópia do portal 4](media/lab-data-flow-data-share/copy4.png)
1. Pesquise **Banco de Dados SQL do Azure** e clique em continuar.

    ![Cópia do portal 5](media/lab-data-flow-data-share/copy-5.png)
1. Chame seu conjunto de dados de "TripData". Selecione "SQLDB" como seu serviço vinculado. Selecione o nome da tabela "dbo.TripData" no menu suspenso do nome da tabela. Importe o esquema **Da conexão/do repositório**. Clique em OK quando terminar.

    ![Cópia do portal 6](media/lab-data-flow-data-share/copy6.png)

Você criou com êxito o conjunto de dados de origem. Verifique se, nas configurações de origem, o valor padrão **Tabela** foi selecionado no campo de consulta de uso.

### <a name="configure-adls-gen2-sink-dataset"></a>Configurar conjunto de dados do coletor do ADLS Gen2

1. Clique na guia **Coletor** da atividade de cópia. Para criar um conjunto de dados, clique em **Novo**.

    ![Cópia do portal 7](media/lab-data-flow-data-share/copy7.png)
1. Pesquise **Azure Data Lake Storage Gen2** e clique em continuar.

    ![Cópia do portal 8](media/lab-data-flow-data-share/copy8.png)
1. No painel de formato selecionado, selecione **DelimitedText** enquanto você está escrevendo em um arquivo csv. Clique em continuar.

    ![Cópia do portal 9](media/lab-data-flow-data-share/copy9.png)
1. Dê ao conjunto de dados de coletor o nome "TripDataCSV". Selecione "ADLSGen2" como seu serviço vinculado. Insira onde você deseja gravar seu arquivo csv. Por exemplo, você pode gravar seus dados no arquivo `trip-data.csv` no contêiner `staging-container`. Defina **Primeira linha como cabeçalho** como true porque convém que seus dados de saída tenham cabeçalhos. Como ainda não existe um arquivo no destino, defina **Importar esquema** como **Nenhum**. Clique em OK quando terminar.

    ![Cópia do portal 10](media/lab-data-flow-data-share/copy10.png)

### <a name="test-the-copy-activity-with-a-pipeline-debug-run"></a>Testar a atividade de cópia com uma execução de depuração de pipeline

1. Para verificar se sua atividade de cópia está funcionando corretamente, clique em **Depurar** na parte superior da tela do pipeline para executar uma execução de depuração. Uma execução de depuração permite que você teste seu pipeline de ponta a ponta ou até um ponto de interrupção antes de publicá-lo no serviço de data factory.

    ![Cópia do portal 11](media/lab-data-flow-data-share/copy11.png)
1. Para monitorar a execução de depuração, acesse a guia **Saída** da tela do pipeline. A tela de monitoramento será atualizada automaticamente a cada 20 segundos ou quando você clicar manualmente no botão atualizar. A atividade de cópia tem uma exibição de monitoramento especial, que você pode acessar clicando no ícone de óculos na coluna **Ações**.

    ![Cópia do portal 12](media/lab-data-flow-data-share/copy12.png)
1. A exibição de monitoramento da cópia fornece os detalhes de execução e as características de desempenho da atividade. Você pode ver informações como os dados lidos/gravados, linhas lidas/gravadas, arquivos lidos/gravados e a taxa de transferência. Se você tiver configurado tudo corretamente, deverá ver 49.999 linhas gravadas em um arquivo em seu coletor do ADLS.

    ![Cópia do portal 13](media/lab-data-flow-data-share/copy13.png)
1. Antes de passar para a próxima seção, sugerimos que você publique suas alterações no serviço do data factory clicando em **Publicar tudo** na barra superior da fábrica. Embora não seja abordado neste laboratório, o Azure Data Factory dá suporte à integração completa do git. A integração do Git permite o controle de versão, o salvamento iterativo em um repositório e a colaboração em um data factory. Para obter mais informações, confira [controle do código-fonte no Azure Data Factory](./source-control.md#troubleshooting-git-integration).

    ![Publicação do portal 1](media/lab-data-flow-data-share/publish1.png)

## <a name="transform-data-using-mapping-data-flow"></a>Transformar dados usando o fluxo de dados de mapeamento

Agora que você copiou com êxito os dados para o Azure Data Lake Storage, é hora de ingressar e agregar esses dados em um data warehouse. Usaremos o fluxo de dados de mapeamento, o serviço de transformação projetado visualmente do Azure Data Factory. Os fluxos de dados de mapeamento permitem que os usuários desenvolvam uma lógica de transformação sem código e execute-a em clusters do spark gerenciados pelo serviço do ADF.

O fluxo de dados criado nesta etapa interna ingressa o conjunto de dados "TripDataCSV" criado na seção anterior com um "dbo.TripFares" de tabela armazenado no "SQLDB" baseado em quatro colunas principais. Em seguida, os dados são agregados com base na coluna `payment_type` para calcular a média de determinados campos e escritos em uma tabela do Azure Synapse Analytics.

### <a name="add-a-data-flow-activity-to-your-pipeline"></a>Adicionar uma atividade de fluxo de dados ao seu pipeline

1. No painel de atividades da tela do pipeline, abra a seção **Mover e Transformar** e arraste a atividade **Fluxo de dados** para a tela.

    ![Fluxo de dados do portal 1](media/lab-data-flow-data-share/dataflow1.png)
1. No painel lateral que é aberto, selecione **Criar fluxo de dados** e escolha **Fluxo de dados de mapeamento**. Clique em **OK**.

    ![Fluxo de dados do portal 2](media/lab-data-flow-data-share/dataflow2.png)
1. Você será direcionado à tela do fluxo de dados em que você criará sua lógica de transformação. Na guia geral, dê ao fluxo de dados o nome "JoinAndAggregateData".

    ![Fluxo de dados do portal 3](media/lab-data-flow-data-share/dataflow3.png)

### <a name="configure-your-trip-data-csv-source"></a>Configurar sua origem de csv de dados de viagem

1. A primeira coisa que convém fazer é configurar suas duas transformações de origem. A primeira origem apontará para o conjunto de dados DelimitedText de "TripDataCSV". Para adicionar uma transformação de origem, clique na caixa **Adicionar origem** na tela.

    ![Fluxo de dados do portal 4](media/lab-data-flow-data-share/dataflow4.png)
1. Dê à sua origem o nome "TripDataCSV" e selecione o conjunto de dados "TripDataCSV" no menu suspenso de origem. Se você se lembra, você não importou um esquema inicialmente ao criar esse conjunto de dados, pois não havia nenhum dado lá. Como agora `trip-data.csv` existe, clique em **Editar** para ir para a guia Configurações do conjunto de dados.

    ![Fluxo de dados do portal 5](media/lab-data-flow-data-share/dataflow5.png)
1. Acesse a guia **Esquema** e clique em **Importar esquema**. Selecione **Da conexão/armazenamento** para importar diretamente do armazenamento de arquivos. 14 colunas do tipo cadeia de caracteres devem aparecer.

    ![Fluxo de dados do portal 6](media/lab-data-flow-data-share/dataflow6.png)
1. Volte para o fluxo de dados "JoinAndAggregateData". Se o cluster de depuração tiver iniciado (indicado por um círculo verde ao lado do controle deslizante de depuração), você poderá obter um instantâneo dos dados na guia **Visualização de Dados**. Clique em **Atualizar** para buscar uma visualização de dados.

    ![Fluxo de dados do portal 7](media/lab-data-flow-data-share/dataflow7.png)

> [!Note]
> A visualização de dados não grava dados.

### <a name="configure-your-trip-fares-sql-db-source"></a>Configurar a origem do BD SQL de tarifas de viagem

1. A segunda origem que você está adicionando apontará para a tabela do BD SQL "dbo.TripFares". Na origem "TripDataCSV", haverá outra caixa **Adicionar Origem**. Clique nela para adicionar uma nova transformação de origem.

    ![Fluxo de dados do portal 8](media/lab-data-flow-data-share/dataflow8.png)
1. Dê a esta origem o nome "TripFaresSQL". Clique em **Novo** ao lado do campo do conjunto de dados de origem para criar um conjunto de dados do BD SQL.

    ![Fluxo de dados do portal 9](media/lab-data-flow-data-share/dataflow9.png)
1. Selecione o bloco **Banco de Dados SQL do Azure** e clique em continuar. *Observação: você pode observar que não há suporte para muitos conectores no data factory no fluxo de dados de mapeamento. Para transformar dados de uma dessas origens, ingira-os em uma origem com suporte usando a atividade de cópia*.

    ![Fluxo de dados do portal 10](media/lab-data-flow-data-share/dataflow-10.png)
1. Chame seu conjunto de dados de "TripFares". Selecione "SQLDB" como seu serviço vinculado. Selecione o nome da tabela "dbo.TripFares" no menu suspenso do nome da tabela. Importe o esquema **Da conexão/do repositório**. Clique em OK quando terminar.

    ![Fluxo de dados do portal 11](media/lab-data-flow-data-share/dataflow11.png)
1. Para verificar seus dados, busque uma visualização de dados na guia **Visualização de Dados**.

    ![Fluxo de dados do portal 12](media/lab-data-flow-data-share/dataflow12.png)

### <a name="inner-join-tripdatacsv-and-tripfaressql"></a>TripDataCSV e TripFaresSQL da junção interna

1. Para adicionar uma nova transformação, clique no ícone de adição no canto inferior direito de "TripDataCSV". Em **Várias entradas/saídas**, selecione **Junção**.

    ![Ingresso no portal 1](media/lab-data-flow-data-share/join1.png)
1. Dê à sua transformação de junção o nome "InnerJoinWithTripFares". Selecione "TripFaresSQL" no menu suspenso do fluxo direito. Selecione **Interno** como o tipo de junção. Para saber mais sobre os diferentes tipos de junção no fluxo de dados de mapeamento, confira [tipos de junção](./data-flow-join.md#join-types).

    Selecione quais colunas você deseja corresponder de cada fluxo por meio do menu suspenso **Condições de junção**. Para adicionar uma condição de junção adicional, clique no ícone de adição ao lado de uma condição existente. Por padrão, todas as condições de junção são combinadas com um operador AND, o que significa que todas as condições precisam ser atendidas para obter uma correspondência. Neste laboratório, desejamos corresponder às colunas `medallion`, `hack_license`, `vendor_id` e `pickup_datetime`

    ![Ingresso no portal 2](media/lab-data-flow-data-share/join2.png)
1. Verifique se você ingressou 25 colunas com êxito com uma visualização de dados.

    ![Ingresso no portal 3](media/lab-data-flow-data-share/join3.png)

### <a name="aggregate-by-payment_type"></a>Agregar por payment_type

1. Depois de concluir a transformação de junção, adicione uma transformação de agregação clicando no ícone de adição ao lado de "InnerJoinWithTripFares. Escolha **Agregação** em **Modificador de esquema**.

    ![Agregação do portal 1](media/lab-data-flow-data-share/agg1.png)
1. Dê à sua transformação de agregação o nome "AggregateByPaymentType". Selecione `payment_type` como a coluna agrupar por.

    ![Agregação do portal 2](media/lab-data-flow-data-share/agg2.png)
1. Vá para a guia **Agregações**. Aqui, você especificará duas agregações:
    * A tarifa média agrupada por tipo de pagamento
    * A distância total da viagem agrupada por tipo de pagamento

    Primeiro, você criará a expressão de tarifa média. Na caixa de texto rotulada **Adicionar ou selecionar uma coluna**, insira "average_fare".

    ![Agregação do portal 3](media/lab-data-flow-data-share/agg3.png)
1. Para inserir uma expressão de agregação, clique na caixa azul rotulada **Inserir expressão**. Isso abrirá o Construtor de Expressões de fluxo de dados, uma ferramenta usada para criar visualmente expressões de fluxo de dados usando o esquema de entrada, funções e operações internas e parâmetros definidos pelo usuário. Para saber mais sobre as funcionalidades do Construtor de Expressões, confira a [documentação do Construtor de Expressões](./concepts-data-flow-expression-builder.md).

    Para obter a tarifa média, use a função de agregação `avg()` para agregar a conversão da coluna `total_amount` em um inteiro com `toInteger()`. Na linguagem de expressão do fluxo de dados, isso é definido como `avg(toInteger(total_amount))`. Clique em **Salvar e concluir** quando terminar.

    ![Agregação do portal 4](media/lab-data-flow-data-share/agg4.png)
1. Para adicionar mais uma expressão de agregação, clique no ícone de adição ao lado de `average_fare`. Selecione **Adicionar coluna**.

    ![Agregação do portal 5](media/lab-data-flow-data-share/agg5.png)
1. Na caixa de texto rotulada **Adicionar ou selecionar uma coluna**, insira "total_trip_distance". Assim como na última etapa, abra o Construtor de Expressões para inserir a expressão.

    Para obter a distância total da viagem, use a função de agregação `sum()` para agregar a conversão da coluna `trip_distance` em um inteiro com `toInteger()`. Na linguagem de expressão do fluxo de dados, isso é definido como `sum(toInteger(trip_distance))`. Clique em **Salvar e concluir** quando terminar.

    ![Agregação do portal 6](media/lab-data-flow-data-share/agg6.png)
1. Teste a lógica de transformação na guia **Visualização de Dados**. Como você pode ver, há significativamente menos linhas e colunas do que antes. Apenas as três colunas agrupar por e agregação definidas nessa transformação continuam downstream. Como há apenas cinco grupos de tipo de pagamento no exemplo, apenas cinco linhas são emitidas.

    ![Agregação do portal 7](media/lab-data-flow-data-share/agg7.png)

### <a name="configure-you-azure-synapse-analytics-sink"></a>Configurar seu coletor do Azure Synapse Analytics

1. Agora que concluímos nossa lógica de transformação, estamos prontos para coletar nossos dados em uma tabela do Azure Synapse Analytics. Adicione uma transformação de coletor na seção **Destino**.

    ![Coletor do portal 1](media/lab-data-flow-data-share/sink1.png)
1. Dê ao seu coletor o nome "SQLDWSink". Clique em **Novo** ao lado do campo do conjunto de dados do coletor para criar um conjunto de dados do Azure Synapse Analytics.

    ![Coletor do portal 2](media/lab-data-flow-data-share/sink2.png)

1. Selecione o bloco do **Azure Synapse Analytics** e clique em Continuar.

    ![Coletor do portal 3](media/lab-data-flow-data-share/sink-3.png)
1. Chame o conjunto de dados de "AggregatedTaxiData". Selecione "SQLDW" como seu serviço vinculado. Selecione **Criar tabela** e dê à nova tabela o nome dbo.AggregateTaxiData. Clique em OK após a conclusão

    ![Coletor do portal 4](media/lab-data-flow-data-share/sink4.png)
1. Acesse a guia **Configurações** do coletor. Como estamos criando uma tabela, precisamos selecionar **Recriar tabela** em ação de tabela. Desmarque **Habilitar preparo**, que alternará se estivermos inserindo linha por linha ou em lote.

    ![Coletor do portal 5](media/lab-data-flow-data-share/sink5.png)

Você criou seu fluxo de dados com êxito. Agora é hora de executá-lo em uma atividade de pipeline.

### <a name="debug-your-pipeline-end-to-end"></a>Depurar o pipeline de ponta a ponta

1. Volte para a guia do pipeline **IngestAndTransformData**. Observe a caixa verde na atividade de cópia "IngestIntoADLS". Arraste-a para a atividade de fluxo de dados "JoinAndAggregateData". Isso cria um "em caso de êxito", o que fará a atividade de fluxo de dados ser executada apenas se a cópia tiver êxito.

    ![Pipeline do portal 1](media/lab-data-flow-data-share/pipeline1.png)
1. Como fizemos para a atividade de cópia, clique em **Depurar** para executar uma execução de depuração. Para execuções de depuração, a atividade de fluxo de dados usará o cluster de depuração ativo em vez de criar um. Esse pipeline levará pouco mais de um minuto para ser executado.

    ![Pipeline do portal 2](media/lab-data-flow-data-share/pipeline2.png)
1. Assim como a atividade de cópia, o fluxo de dados tem uma exibição de monitoramento especial acessada pelo ícone de óculos após a conclusão da atividade.

    ![Pipeline do portal 3](media/lab-data-flow-data-share/pipeline3.png)
1. Na exibição de monitoramento, você pode ver um grafo de fluxo de dados simplificado juntamente com os tempos de execução e as linhas em cada estágio de execução. Se for feito corretamente, você deverá ter 49.999 linhas agregadas em cinco linhas nessa atividade.

    ![Pipeline do portal 4](media/lab-data-flow-data-share/pipeline4.png)
1. Você pode clicar em uma transformação para obter detalhes adicionais sobre sua execução, como informações sobre particionamento e colunas novas/atualizadas/removidas.

    ![Pipeline do portal 5](media/lab-data-flow-data-share/pipeline5.png)

Agora você concluiu a parte do data factory deste laboratório. Publique seus recursos se desejar operacionalizá-los com gatilhos. Você executou com êxito um pipeline que ingeriu dados do Banco de Dados SQL do Azure para o Azure Data Lake Storage usando a atividade de cópia e, em seguida, agregou esses dados em um Azure Synapse Analytics. Você pode verificar se os dados foram gravados com êxito examinando o SQL Server em si.

## <a name="share-data-using-azure-data-share"></a>Compartilhar dados usando o Azure Data Share

Nesta seção, você aprenderá a configurar um novo compartilhamento de dados usando o portal do Azure. Isso envolverá a criação de um compartilhamento de dados que conterá conjuntos de dados do Azure Data Lake Storage Gen2 e do Azure Synapse Analytics. Em seguida, você configurará um agendamento de instantâneo, que fornecerá aos consumidores de dados uma opção para atualizar automaticamente os dados que estão sendo compartilhados com eles. Em seguida, você convidará os destinatários para seu compartilhamento de dados. 

Após criar um compartilhamento de dados, você trocará de lugar e se tornará o *consumidor de dados*. Como consumidor de dados, você percorrerá o fluxo de aceitação de um convite do compartilhamento de dados, da configuração de onde você deseja que os dados fossem recebidos e do mapeamento de conjuntos de dados para diferentes locais de armazenamento. Em seguida, você disparará um instantâneo, que copiará o compartilhamento de dados com você para o destino especificado. 

### <a name="sharing-data-data-provider-flow"></a>Compartilhar dados (fluxo de Provedor de Dados)

1. Abra o portal do Azure no Microsoft Edge ou no Google Chrome.

1. Usando a barra de pesquisa na parte superior da página, pesquise **Compartilhamentos de Dados**

    ![Anúncios do portal](media/lab-data-flow-data-share/portal-ads.png)

1. Selecione a conta do compartilhamento de dados com "Provedor" no nome. Por exemplo, **DataProvider0102**. 

1. Selecione **Começar a compartilhar seus dados**

    ![Iniciar compartilhamento](media/lab-data-flow-data-share/ads-start-sharing.png)

1. Selecione **+Criar** para começar a configurar seu novo compartilhamento de dados. 

1. Em **Nome do compartilhamento**, especifique um nome de sua escolha. Esse é o nome do compartilhamento que será visto por seu consumidor de dados. Portanto, dê a ele um nome descritivo como TaxiData.

1. Em **Descrição**, insira uma frase que descreva o conteúdo do compartilhamento de dados. O compartilhamento de dados conterá dados de corridas de táxi de todo o mundo armazenados em vários repositórios, incluindo o Azure Synapse Analytics e o Azure Data Lake Storage. 

1. Em **Termos de uso**, especifique um conjunto de termos que você gostaria que seu consumidor de dados obedecesse. Alguns exemplos incluem "Não distribua esses dados fora de sua organização" ou "Veja o contrato legal". 

    ![Detalhes do compartilhamento](media/lab-data-flow-data-share/ads-details.png)

1. Selecione **Continuar**. 

1. Selecione **Adicionar conjuntos de dados** 

    ![Adicionar conjunto de dados 1](media/lab-data-flow-data-share/add-dataset.png)

1. Selecione **Azure Synapse Analytics** para selecionar uma tabela do Azure Synapse Analytics na qual suas transformações do ADF foram descarregadas.

    ![Adicionar sql do conjunto de dados](media/lab-data-flow-data-share/add-dataset-sql.png)


1. Você receberá um script para executar antes de poder continuar. O script fornecido cria um usuário no Banco de Dados SQL para permitir que o MSI do Azure Data Share seja autenticado em seu nome. 

> [!IMPORTANT]
> Antes de iniciar o script, você deve se definir como o Administrador do Active Directory do SQL Server. 

1. Abra uma nova guia e navegue até o portal do Azure. Copie o script fornecido para criar um usuário no banco de dados do qual você deseja compartilhar os dados. Faça isso fazendo logon no banco de dados do EDW usando o Gerenciador de Consultas (versão prévia) usando a autenticação do AAD. 

    Você precisará modificar o script para que o usuário criado esteja contido nos colchetes. Ex.:
    
    create user [dataprovider-xxxx] from external login; exec sp_addrolemember db_owner, [dataprovider-xxxx];
    
1. Volte para o Azure Data Share em que você estava adicionando conjunto de dados ao seu compartilhamento de dados. 

1. Selecione **EDW** e, em seguida, **AggregatedTaxiData** para a tabela. 

1. Selecione **Adicionar conjunto de dados**

    Agora temos uma tabela SQL que faz parte do nosso conjunto de dados. Em seguida, adicionaremos mais conjuntos de dados do Azure Data Lake Storage. 

1. Selecione **Adicionar conjunto de dados** e **Azure Data Lake Storage Gen2**

    ![Adicionar adls do conjunto de dados](media/lab-data-flow-data-share/add-dataset-adls.png)

1. Selecione **Avançar**

1. Expanda *wwtaxidata*. Expanda *Dados de Táxi de Boston*. Observe que você pode compartilhar até o nível do arquivo. 

1. Selecione a pasta *Dados de Táxi de Boston* para adicionar a pasta inteira ao seu compartilhamento de dados. 

1. Selecione **Adicionar conjuntos de dados**

1. Examine os conjuntos de dados que foram adicionados. Você deve ter uma tabela SQL e uma pasta ADLS Gen2 adicionadas ao seu compartilhamento de dados. 

1. Selecione **Continuar**

1. Nessa tela, você pode adicionar destinatários ao seu compartilhamento de dados. Os destinatários adicionados receberão convites para o seu compartilhamento de dados. Para fins deste laboratório, você deve adicionar dois endereços de email:

    1. o endereço de email da assinatura do Azure em que você está. 

        ![Adicionar destinatários](media/lab-data-flow-data-share/add-recipients.png)

    1. Adicione o consumidor de dados fictício denominado *janedoe@fabrikam.com* .

1. Nessa tela, você pode definir uma Configuração de Instantâneo para o consumidor de dados. Com isso, ele receberá atualizações regulares dos seus dados em um intervalo definido por você. 

1. Verifique o **Agendamento de Instantâneos** e configure uma atualização por hora de seus dados usando a lista suspensa *Recorrência*.  

1. Selecione **Criar**.

    Agora você tem um compartilhamento de dados ativo. Vamos examinar o que você pode ver como provedor de dados quando cria um compartilhamento de dados. 

1. Selecione o compartilhamento de dados criado, intitulado **DataProvider**. Você pode navegar até ele selecionando **Compartilhamentos Enviados** em **Compartilhamento de Dados**. 

1. Clique em Agendamento de instantâneo. Você poderá desabilitar o agendamento de instantâneos se escolher. 

1. Em seguida, selecione a guia **Conjuntos de Dados**. Você poderá adicionar outros conjuntos de dados a esse compartilhamento de dados depois que ele tiver sido criado. 

1. Selecione a guia **Assinaturas de compartilhamento**. Ainda não existe nenhuma assinatura de compartilhamento porque seu consumidor de dados ainda não aceitou seu convite.

1. Navegue até a guia **Convites**. Aqui você verá uma lista de convites pendentes. 

    ![Convites pendentes](media/lab-data-flow-data-share/pending-invites.png)

1. Selecione o convite para *janedoe@fabrikam.com* . Selecione Excluir. Se o destinatário ainda não tiver aceitado o convite, ele não poderá mais fazê-lo. 

1. Selecione a guia **Histórico** . Nada é exibido ainda porque seu consumidor de dados ainda não aceitou seu convite nem disparou um instantâneo. 

### <a name="receiving-data-data-consumer-flow"></a>Receber dados (Fluxo do consumidor de dados)

Agora que examinamos nosso compartilhamento de dados, estamos prontos para mudar o contexto e voltar para o consumidor de dados que era nosso. 

Agora você deve ter um convite do Azure Data Share na caixa de entrada do Microsoft Azure. Inicie o Outlook Web Access (outlook.com) e faça logon usando as credenciais fornecidas para sua assinatura do Azure.

No email que você deve ter recebido, clique em "Exibir convite >". Neste ponto, você simulará a experiência do consumidor de dados ao aceitar um convite de provedores de dados para o compartilhamento de dados dele. 

![Convite por email](media/lab-data-flow-data-share/email-invite.png)

Talvez você precise selecionar uma assinatura. Selecione a assinatura que na qual você veio trabalhando para este laboratório. 

1. Clique no convite intitulado *DataProvider*. 

1. Nesta tela de convite, você observará vários detalhes sobre o compartilhamento de dados que você configurou anteriormente como um provedor de dados. Examine os detalhes e aceite os termos de uso se forem fornecidos.

1. Selecione a Assinatura e o Grupo de Recursos que já existe para seu laboratório. 

1. Para a **Conta do compartilhamento de dados**, selecione **DataConsumer**. Você também pode criar uma conta do compartilhamento de dados. 

1. Ao lado de **Nome do compartilhamento recebido**, você observará que o nome do compartilhamento padrão é o que foi especificado pelo provedor de dados. Dê ao compartilhamento um nome amigável que descreva os dados que você está prestes a receber, por exemplo, **TaxiDataShare**.

    ![Aceitações de convite](media/lab-data-flow-data-share/consumer-accept.png)

1. Você pode optar por **Aceitar e configurar agora** ou **Aceitar e configurar depois**. Se optar por aceitar e configurar agora, você especificará uma conta de armazenamento em que todos os dados devem ser copiados. Se optar por aceitar e configurar depois, os conjuntos de dados no compartilhamento serão desmapeados e você precisará mapeá-los manualmente. Optaremos por isso mais tarde. 

1. Selecione **Aceitar e configurar depois**. 

    Ao configurar essa opção, uma assinatura de compartilhamento é criada, mas não há nenhum lugar para os dados serem descarregados, pois nenhum destino foi mapeado. 

    Em seguida, configuraremos mapeamentos de conjuntos de dados para o compartilhamento de dados. 

1. Selecione o Compartilhamento Recebido (o nome especificado na etapa 5).

    O **Instantâneo do gatilho** está esmaecido, mas o compartilhamento está Ativo. 

1. Selecione a guia **Conjuntos de Dados**. Observe que cada conjunto de dados está Desmapeado, o que significa que ele não tem nenhum destino para o qual copiar os dados. 

    ![conjuntos de dados desmapeados](media/lab-data-flow-data-share/unmapped.png)

1. Selecione a tabela do Azure Synapse Analytics e selecione **+ Mapear para o Destino**.

1. No lado direito da tela, selecione o menu suspenso **Tipo de Dados de Destino**. 

    Você pode mapear os dados SQL para uma ampla variedade de armazenamentos de dados. Nesse caso, mapearemos para um Banco de Dados SQL do Azure.

    ![mapping](media/lab-data-flow-data-share/mapping-options.png)
    
    (Opcional) Selecione **Azure Data Lake Storage Gen2** como o tipo de dados de destino. 
    
    (Opcional) Selecione a Assinatura, o Grupo de Recursos e a Conta de armazenamento nos quais você está trabalhando. 
    
    (Opcional) Você pode optar por receber os dados em seu data lake no formato csv ou parquet. 

1. Ao lado de **Tipo de dados de destino**, selecione Banco de Dados SQL do Azure. 

1. Selecione a Assinatura, o Grupo de Recursos e a Conta de armazenamento nos quais você está trabalhando. 

    ![mapear para sql](media/lab-data-flow-data-share/map-to-sqldb.png)

1. Antes de continuar, será necessário criar um usuário no SQL Server executando o script fornecido. Primeiro, copie o script fornecido para sua área de transferência. 

1. Abra uma guia do portal do Azure. Não feche sua guia existente, pois você precisará voltar a ela daqui a pouco. 

1. Na nova guia que você abriu, navegue até **Bancos de dados SQL**.

1. Selecione o Banco de Dados SQL (deve haver apenas um em sua assinatura). Tenha cuidado para não selecionar o data warehouse. 

1. Selecione **Editor de consultas (versão prévia)**

1. Use a autenticação do AAD para fazer logon no Editor de consultas. 

1. Execute a consulta fornecida em seu compartilhamento de dados (copiada para a área de transferência na etapa 14). 

    Com esse comando, o serviço Azure Data Share pode usar Identidades Gerenciadas para os Serviços do Azure a serem autenticados no SQL Server poderem copiar os dados para ele. 

1. Volte para a guia original e selecione **Mapear para destino**.

1. Em seguida, selecione a pasta Azure Data Lake Gen2 que faz parte do conjunto de dados e mapeie-a para uma conta de Armazenamento de Blobs do Azure. 

    ![armazenamento](media/lab-data-flow-data-share/storage-map.png)

    Com todos os conjuntos de dados mapeados, agora você está pronto para começar a receber dados do provedor de dados. 

    ![mapeado](media/lab-data-flow-data-share/all-mapped.png)
    
1. Selecione **Detalhes**. 

    Observe que **Instantâneo de Gatilho** não está mais esmaecido, pois o compartilhamento de dados agora tem destinos para os quais copiar.

1. Selecione Instantâneo de Gatilho -> Cópia Completa. 

    ![gatilho](media/lab-data-flow-data-share/trigger-full.png)

    Isso iniciará a cópia de dados em sua nova conta de compartilhamento de dados. Em um cenário do mundo real, esses dados viriam de um terceiro. 

    Levará aproximadamente de 3 a 5 minutos para os dados atravessarem. Você pode monitorar o andamento clicando na guia **Histórico**. 

    Enquanto você aguarda, navegue até o compartilhamento de dados original (DataProvider) e exiba o status da guia **Assinaturas de Compartilhamento** e **Histórico**. Observe que agora há uma assinatura ativa e, como provedor de dados, você também pode monitorar quando o consumidor de dados começou a receber os dados compartilhados com ele. 

1. Volte para o compartilhamento de dados do Consumidor de dados. Depois que o status do gatilho for bem-sucedido, navegue até o Banco de Dados SQL e o data lake de destino para ver se os dados foram descarregados para os respectivos armazenamentos. 

Parabéns. Você concluiu o laboratório!
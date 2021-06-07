---
title: Transformar dados com um Azure Data Factory fluxo de dados de mapeamento de rede virtual gerenciado
description: Este tutorial fornece instruções passo a passo para usar Azure Data Factory para transformar dados com fluxos de dados de mapeamento.
author: dcstwh
ms.author: weetok
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/15/2021
ms.openlocfilehash: a5c93244862d72f9c8ea2928c41e699302b1752b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98249424"
---
# <a name="transform-data-securely-by-using-mapping-data-flow"></a>Transformar dados com segurança usando o fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Se estiver se familiarizando com o Azure Data Factory, confira [Introdução ao Azure Data Factory](./introduction.md).

Neste tutorial, você usará o Data Factory interface do usuário para criar um pipeline que copia e transforma dados *de uma fonte de Azure data Lake Storage Gen2 para um coletor data Lake Storage Gen2 (permitindo o acesso somente a redes selecionadas)* usando o fluxo de dados de mapeamento em [Data Factory rede virtual gerenciada](managed-virtual-network-private-endpoint.md). Você pode expandir o padrão de configuração neste tutorial ao transformar dados usando o fluxo de dados de mapeamento.

Neste tutorial, você executa as seguintes etapas:

> [!div class="checklist"]
>
> * Criar um data factory.
> * Crie um pipeline com uma atividade de fluxo de dados.
> * Crie um fluxo de dados de mapeamento com quatro transformações.
> * Executar teste do pipeline.
> * Monitorar uma atividade de fluxo de dados.

## <a name="prerequisites"></a>Pré-requisitos

* **Assinatura do Azure**. Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* **Conta de Armazenamento do Azure**. Você usa Data Lake Storage como armazenamentos de dados de *origem* e de *coletor* . Se você não tiver uma conta de armazenamento, confira [Criar uma conta de armazenamento do Azure](../storage/common/storage-account-create.md?tabs=azure-portal) a fim de conhecer as etapas para criar uma. *Verifique se a conta de armazenamento permite acesso somente de redes selecionadas.* 

O arquivo que iremos transformar neste tutorial é moviesDB.csv, que pode ser encontrado neste [site de conteúdo do GitHub](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv). Para recuperar o arquivo do GitHub, copie o conteúdo para um editor de texto de sua escolha para salvá-lo localmente como um arquivo. csv. Para carregar o arquivo em sua conta de armazenamento, consulte [carregar BLOBs com o portal do Azure](../storage/blobs/storage-quickstart-blobs-portal.md). Os exemplos referenciarão um contêiner denominado **Data-Sample**.

## <a name="create-a-data-factory"></a>Criar uma data factory

Nesta etapa, você cria um data factory e abre a interface do usuário do Data Factory para criar um pipeline no data factory.

1. Abra o Microsoft Edge ou Google Chrome. Atualmente apenas os navegadores da Web Microsoft Edge e Google Chrome são compatíveis com a interface do usuário do Data Factory.
1. No menu à esquerda, selecione **Criar um recurso** > **Analytics** > **Data Factory**.
1. Na página **Novo data factory**, em **Nome**, insira **ADFTutorialDataFactory**.

   O nome do data factory deve ser *globalmente exclusivo*. Se você receber uma mensagem de erro sobre o valor do nome, insira um nome diferente para o data factory (por exemplo, yournameADFTutorialDataFactory). Para ver as regras de nomenclatura para artefatos do Data Factory, confira [Data Factory – Regras de nomenclatura](naming-rules.md).

1. Selecione a **assinatura** do Azure na qual deseja criar o data factory.
1. Em **Grupo de Recursos**, use uma das seguintes etapas:

    * Selecione **Usar existente** e selecione um grupo de recursos existente na lista suspensa.
    * Selecione **Criar novo** e insira o nome de um grupo de recursos. 
         
    Para saber mais sobre grupos de recursos, confira [Usar grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/management/overview.md). 
1. Em **Versão**, selecione **V2**.
1. Em **Local**, informe uma localização para o data factory. Apenas os locais com suporte aparecem na lista suspensa. Os armazenamentos de dados (por exemplo, o armazenamento do Azure e o Azure SQL Database) e as computações (por exemplo, Azure HDInsight) usados pelo data factory podem estar em outras regiões.

1. Selecione **Criar**.
1. Depois que a criação for concluída, você verá o aviso no Centro de notificações. Selecione **Ir para o recurso** para ir até a página do **Data Factory**.
1. Clique em **Criar e Monitorar** para iniciar a IU do Azure Data Factory em uma guia separada.

## <a name="create-an-azure-ir-in-data-factory-managed-virtual-network"></a>Criar um Azure IR em Data Factory rede virtual gerenciada

Nesta etapa, você cria um Azure IR e habilita Data Factory rede virtual gerenciada.

1. No portal de Data Factory, vá para **gerenciar** e selecione **novo** para criar um novo Azure ir.

   ![Captura de tela que mostra a criação de uma nova Azure IR.](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
1. Na página **Instalação do runtime de integração**, escolha o runtime de integração a ser criado com base nas funcionalidades necessárias. Neste tutorial, selecione **Azure, Auto-hospedado** e clique em **Continuar**. 
1. Selecione **Azure** e clique em **Continuar** para criar um Azure Integration Runtime.

   ![Captura de tela que mostra um novo Azure IR.](./media/tutorial-copy-data-portal-private/azure-ir.png)

1. Em **Configuração de rede virtual (versão prévia)** , selecione **Habilitar**.

   ![Captura de tela que mostra a habilitação de uma nova Azure IR.](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)

1. Selecione **Criar**.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Criar um pipeline com uma atividade de fluxo de dados

Nesta etapa, você criará um pipeline que contém uma atividade de fluxo de dados.

1. Na página **Introdução**, selecione **Criar pipeline**.

   ![Captura de tela que mostra a criação de um pipeline.](./media/doc-common-process/get-started-page.png)

1. No painel Propriedades do pipeline, insira **TransformMovies** para o nome do pipeline.
1. Na barra superior da fábrica, deslize o controle deslizante de **depuração do fluxo de dados** em. O modo de depuração permite o teste interativo da lógica de transformação em um cluster do Spark ao vivo. Os clusters de fluxo de dados levam de cinco a sete minutos para serem ativados. Ative a **depuração de fluxo de dados** primeiro se você planeja fazer o desenvolvimento de fluxo de dados. Para obter mais informações, consulte [modo de depuração](./concepts-data-flow-debug-mode.md).

    ![Captura de tela que mostra o controle deslizante de depuração do fluxo de dados.](media/tutorial-data-flow-private/dataflow-debug.png)
1. No painel **atividades** , expanda **mover e transformar**. Arraste a atividade **fluxo de dados** do painel para a tela do pipeline.

1. No pop-up **adicionando fluxo de dados** , selecione **criar novo fluxo de dados** e, em seguida, selecione **mapear fluxo de dados**. Selecione **OK** quando tiver terminado.

    ![Captura de tela que mostra o fluxo de dados de mapeamento.](media/tutorial-data-flow-private/mapping-dataflow.png)

1. Nomeie o fluxo de dados **TransformMovies** no painel Propriedades.

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Criar lógica de transformação na tela de fluxo de dados

Depois de criar o fluxo de dados, você será enviado automaticamente para a tela fluxo de dados. Nesta etapa, você criará um fluxo de dados que usa o arquivo de moviesDB.csv em Data Lake Storage e agrega a classificação média de Comedies de 1910 a 2000. Em seguida, você escreverá esse arquivo novamente para Data Lake Storage.

### <a name="add-the-source-transformation"></a>Adicionar a transformação origem

Nesta etapa, você configura Data Lake Storage Gen2 como uma origem.

1. Na tela fluxo de dados, adicione uma fonte selecionando a caixa **Adicionar origem** .

1. Nomeie sua fonte **MoviesDB**. Selecione **novo** para criar um novo conjunto de fonte de origem.

1. Selecione **Azure data Lake Storage Gen2** e, em seguida, selecione **continuar**.

1. Selecione **DelimitedText** e, em seguida, selecione **continuar**.

1. Nomeie seu DataSet **MoviesDB**. Na lista suspensa serviço vinculado, selecione **novo**.

1. Na tela de criação de serviço vinculado, nomeie seu Data Lake Storage Gen2 serviço vinculado **ADLSGen2** e especifique o método de autenticação. Em seguida, insira suas credenciais de conexão. Neste tutorial, estamos usando a **chave de conta** para se conectar à nossa conta de armazenamento. 

1. Habilite a **Criação interativa**. Pode levar um minuto para ser habilitado.

    ![Captura de tela que mostra a Criação interativa.](./media/tutorial-data-flow-private/interactive-authoring.png)

1. Selecione **Testar conexão**. Ele deve falhar porque a conta de armazenamento não habilita o acesso a ele sem a criação e a aprovação de um ponto de extremidade privado. Na mensagem de erro, você verá um link para criar um ponto de extremidade privado que poderá seguir para criar um ponto de extremidade privado gerenciado. Uma alternativa é ir diretamente para a guia **gerenciar** e seguir as instruções nesta [seção](#create-a-managed-private-endpoint) para criar um ponto de extremidade privado gerenciado.

1. Mantenha a caixa de diálogo aberta e acesse sua conta de armazenamento.

1. Siga as instruções [desta seção](#approval-of-a-private-link-in-a-storage-account) para aprovar o link privado.

1. Volte à caixa de diálogo. Selecione novamente **Testar conectividade** e selecione **Criar** para implantar o serviço vinculado.

1. Na tela de criação do conjunto de arquivos, insira onde o arquivo está localizado no campo **caminho do arquivo** . Neste tutorial, o arquivo moviesDB.csv está localizado no contêiner **Sample-data**. Como o arquivo tem cabeçalhos, marque a caixa de seleção **primeira linha como cabeçalho** . Selecione **do repositório/conexão** para importar o esquema de cabeçalho diretamente do arquivo no armazenamento. Selecione **OK** quando tiver terminado.

    ![Captura de tela que mostra o caminho de origem.](media/tutorial-data-flow-private/source-file-path.png)

1. Se o cluster de depuração for iniciado, vá para a guia **visualização de dados** da transformação origem e selecione **Atualizar** para obter um instantâneo dos dados. Você pode usar a visualização de dados para verificar se a transformação está configurada corretamente.

    ![Captura de tela que mostra a guia Visualização de dados.](media/tutorial-data-flow-private/data-preview.png)

#### <a name="create-a-managed-private-endpoint"></a>Criar um ponto de extremidade privado gerenciado

Se você não usou o hiperlink ao testar a conexão anterior, siga o caminho. Agora você precisará criar um ponto de extremidade privado gerenciado que será conectado ao serviço vinculado que você criou.

1. Acesse a guia **Gerenciar**.

   > [!NOTE]
   > É possível que a guia **Gerenciar** não fique disponível para todas as instâncias do Data Factory. Se você não a vir, acesse os pontos de extremidade privados selecionando **Criação** > **Conexões** > **Ponto de Extremidade Privado**.

1. Acesse a seção **Pontos de extremidade privados** gerenciados.
1. Selecione **+ Novo** em **Pontos de extremidade privados gerenciados**.

    ![Captura de tela que mostra o novo botão Pontos de extremidade privados gerenciados.](./media/tutorial-data-flow-private/new-managed-private-endpoint.png) 

1. Selecione o bloco **Azure data Lake Storage Gen2** na lista e selecione **continuar**.
1. Insira o nome da conta de armazenamento criada.
1. Selecione **Criar**.
1. Depois de alguns segundos, você verá que o link privado criado precisa de uma aprovação.
1. Selecione o ponto de extremidade privado que você criou. Você verá um hiperlink que vai levar você à aprovação do ponto de extremidade privado no nível da conta de armazenamento.

    ![Captura de tela que mostra o painel gerenciar ponto de extremidade privado.](./media/tutorial-data-flow-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-a-storage-account"></a>Aprovação de um link privado em uma conta de armazenamento

1. Na conta de armazenamento, acesse **Conexões do ponto de extremidade privado** na seção **Configurações**.

1. Marque a caixa de seleção pelo ponto de extremidade privado que você criou e selecione **aprovar**.

    ![Captura de tela que mostra o botão de aprovação do ponto de extremidade privado.](./media/tutorial-data-flow-private/approve-private-endpoint.png)

1. Adicione uma descrição e selecione **sim**.
1. Volte à seção **Pontos de extremidade privados gerenciados** da guia **Gerenciar** no Data Factory.
1. Após cerca de um minuto, você deve ver que a aprovação é exibida para seu ponto de extremidade particular.

### <a name="add-the-filter-transformation"></a>Adicionar a transformação de filtro

1. Ao lado do nó de origem na tela fluxo de dados, selecione o ícone de adição para adicionar uma nova transformação. A primeira transformação que você adicionará é um **filtro**.

    ![Captura de tela que mostra a adição de um filtro.](media/tutorial-data-flow-private/add-filter.png)
1. Nomeie sua transformação de filtro **FilterYears**. Selecione a caixa expressão ao lado de **filtrar em** para abrir o construtor de expressões. Aqui você especificará sua condição de filtragem.

    ![Captura de tela que mostra FilterYears.](media/tutorial-data-flow-private/filter-years.png)
1. O construtor de expressões de fluxo de dados permite criar expressões interativamente para usar em várias transformações. As expressões podem incluir funções internas, colunas do esquema de entrada e parâmetros definidos pelo usuário. Para obter mais informações sobre como criar expressões, consulte [Construtor de expressões de fluxo de dados](./concepts-data-flow-expression-builder.md).

    * Neste tutorial, você deseja filtrar filmes no gênero comédia que surgiu entre os anos 1910 e 2000. Como o ano é atualmente uma cadeia de caracteres, você precisa convertê-lo em um inteiro usando a ```toInteger()``` função. Use os operadores maior ou igual a (>=) e menor ou igual a (<=) para comparar com os valores de ano literal 1910 e 2000. Union essas expressões junto com o operador and (&&). A expressão é exibida como:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    * Para descobrir quais filmes são Comedies, você pode usar a ```rlike()``` função para localizar o padrão ' comédia ' nos gêneros de coluna. Union a expressão RLIKE com a comparação de anos para obter:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    * Se você tiver um cluster de depuração ativo, poderá verificar sua lógica selecionando **Atualizar** para ver a saída da expressão em comparação com as entradas usadas. Há mais de uma resposta certa sobre como você pode realizar essa lógica usando a linguagem de expressão de fluxo de dados.

        ![Captura de tela que mostra a expressão de filtro.](media/tutorial-data-flow-private/filter-expression.png)

    * Selecione **salvar e concluir** depois de concluir a expressão.

1. Busque uma **visualização de dados** para verificar se o filtro está funcionando corretamente.

    ![Captura de tela que mostra a visualização de dados filtrados.](media/tutorial-data-flow-private/filter-data.png)

### <a name="add-the-aggregate-transformation"></a>Adicionar a transformação Agregação

1. A próxima transformação que você adicionará é uma transformação **agregação** em **modificador de esquema**.

    ![Captura de tela que mostra a adição da agregação.](media/tutorial-data-flow-private/add-aggregate.png)
1. Nomeie sua transformação agregada **AggregateComedyRating**. Na guia **Agrupar por** , selecione **ano** na caixa suspensa para agrupar as agregações pelo ano em que o filme surgiu.

    ![Captura de tela que mostra o grupo de agregação.](media/tutorial-data-flow-private/group-by-year.png)
1. Vá para a guia **agregações** . Na caixa de texto à esquerda, nomeie a coluna de agregação **AverageComedyRating**. Selecione a caixa expressão à direita para inserir a expressão de agregação por meio do construtor de expressões.

    ![Captura de tela que mostra o nome da coluna agregada.](media/tutorial-data-flow-private/name-column.png)
1. Para obter a média de **classificação** de coluna, use a ```avg()``` função de agregação. Como a **classificação** é uma cadeia de caracteres e ```avg()``` usa uma entrada numérica, devemos converter o valor em um número por meio da ```toInteger()``` função. Esta expressão é semelhante a:

    ```avg(toInteger(Rating))```

1. Selecione **salvar e concluir** depois que terminar.

    ![Captura de tela que mostra a gravação da agregação.](media/tutorial-data-flow-private/save-aggregate.png)
1. Vá para a guia **visualização de dados** para exibir a saída da transformação. Observe que apenas duas colunas estão lá, **year** e **AverageComedyRating**.

### <a name="add-the-sink-transformation"></a>Adicionar a transformação do coletor

1. Em seguida, você deseja adicionar uma transformação de **coletor** em **destino**.

    ![Captura de tela que mostra a adição de um coletor.](media/tutorial-data-flow-private/add-sink.png)
1. Nomeie o **coletor** do coletor. Selecione **novo** para criar o conjunto de seus conjuntos de seu coletor.

    ![Captura de tela que mostra a criação de um coletor.](media/tutorial-data-flow-private/create-sink.png)
1. Na página **novo conjunto de novas** , selecione **Azure data Lake Storage Gen2** e, em seguida, selecione **continuar**.

1. Na página **selecionar formato** , selecione **DelimitedText** e, em seguida, selecione **continuar**.

1. Nomeie seu conjunto de **MoviesSink** de banco de seu coletor. Para o serviço vinculado, escolha o mesmo serviço vinculado do **ADLSGen2** que você criou para a transformação de origem. Insira uma pasta de saída na qual os dados são gravados. Neste tutorial, estamos gravando na **saída** da pasta no contêiner **Sample-data**. A pasta não precisa existir com antecedência e pode ser criada dinamicamente. Marque a caixa de seleção **primeira linha como cabeçalho** e selecione **nenhum** para **importar o esquema**. Selecione **OK**.

    ![Captura de tela que mostra o caminho do coletor.](media/tutorial-data-flow-private/sink-file-path.png)

Agora você concluiu a criação do fluxo de dados. Você está pronto para executá-lo em seu pipeline.

## <a name="run-and-monitor-the-data-flow"></a>Executar e monitorar o fluxo de dados

Você pode depurar um pipeline antes de publicá-lo. Nesta etapa, você dispara uma execução de depuração do pipeline de fluxo de dados. Embora a visualização de dados não grave dados, uma execução de depuração gravará dados no destino do coletor.

1. Vá para a tela do pipeline. Selecione **depurar** para disparar uma execução de depuração.

1. A depuração de pipeline de atividades de fluxo de dados usa o cluster de depuração ativo, mas ainda leva pelo menos um minuto para ser inicializado. Você pode acompanhar o progresso por meio da guia **saída** . Depois que a execução for bem-sucedida, selecione o ícone de óculos para obter detalhes de execução.

1. Na página de detalhes, você pode ver o número de linhas e o tempo gasto em cada etapa de transformação.

    ![Captura de tela que mostra uma execução de monitoramento.](media/tutorial-data-flow-private/run-details.png)
1. Selecione uma transformação para obter informações detalhadas sobre as colunas e o particionamento dos dados.

Se você seguiu este tutorial corretamente, deve ter escrito 83 linhas e 2 colunas na pasta do coletor. Você pode verificar se os dados estão corretos verificando seu armazenamento de BLOBs.

## <a name="summary"></a>Resumo

Neste tutorial, você usou a interface do usuário do Data Factory para criar um pipeline que copia e transforma dados de uma fonte de Data Lake Storage Gen2 para um coletor Data Lake Storage Gen2 (permitindo o acesso somente a redes selecionadas) usando o fluxo de dados de mapeamento em [Data Factory rede virtual gerenciada](managed-virtual-network-private-endpoint.md).
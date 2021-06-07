---
title: 'Início rápido: transformar dados usando um fluxo de dados de mapeamento'
description: Este tutorial fornece instruções passo a passo para usar o Azure Synapse Analytics para transformar dados com o fluxo de dados de mapeamento.
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/03/2020
ms.openlocfilehash: cb0fd5464f7025d71dd384c56233aefa6a6cd364
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98218715"
---
# <a name="quickstart-transform-data-using-mapping-data-flows"></a>Início rápido: transformar dados usando mapeamento de fluxos de dados

Neste guia de início rápido, você usará o Azure Synapse Analytics para criar um pipeline que transforma dados de uma fonte de Azure Data Lake Storage Gen2 (ADLS Gen2) em um coletor ADLS Gen2 usando o fluxo de dados de mapeamento. O padrão de configuração neste guia de início rápido pode ser expandido ao transformar dados usando o fluxo de dados de mapeamento

Neste guia de início rápido, você executa as seguintes etapas:

> [!div class="checklist"]
> * Crie um pipeline com uma atividade de fluxo de dados no Azure Synapse Analytics.
> * Crie um fluxo de dados de mapeamento com quatro transformações.
> * Executar teste do pipeline.
> * Monitorar uma atividade de fluxo de dados

## <a name="prerequisites"></a>Pré-requisitos

* **Assinatura do Azure**: Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Espaço de trabalho Synapse do Azure**: Crie um espaço de trabalho do Synapse usando o portal do Azure seguindo as instruções em [início rápido: criar um espaço de trabalho do Synapse](quickstart-create-workspace.md).
* **Conta de armazenamento do Azure**: Use o armazenamento ADLS como armazenamento de dados de *origem* e de *coletor* . Se você não tiver uma conta de armazenamento, confira [Criar uma conta de armazenamento do Azure](../storage/common/storage-account-create.md) a fim de conhecer as etapas para criar uma.

    O arquivo que estamos transformando neste tutorial é MoviesDB.csv, que pode ser encontrado [aqui](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv). Para recuperar o arquivo do GitHub, copie o conteúdo para um editor de texto de sua escolha para salvar localmente como um arquivo. csv. Para carregar o arquivo em sua conta de armazenamento, consulte [carregar BLOBs com o portal do Azure](../storage/blobs/storage-quickstart-blobs-portal.md). Os exemplos referenciarão um contêiner chamado ' sample-data '.

### <a name="navigate-to-the-synapse-studio"></a>Navegar até o Synapse Studio

Após criar o workspace do Azure Synapse, você tem duas maneiras de abrir o Synapse Studio:

* Abra o workspace do Synapse no [portal do Azure](https://ms.portal.azure.com/#home). Selecione **Abrir** no cartão Abrir o Synapse Studio em Introdução.
* Abra o [Azure Synapse Analytics](https://web.azuresynapse.net/) e entre no seu workspace.

Neste guia de início rápido, usamos o workspace chamado "adftest2020" como um exemplo. Você será direcionado automaticamente para a home page do Synapse Studio.

![Home page do Synapse Studio](media/doc-common-process/synapse-studio-home.png)

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Criar um pipeline com uma atividade de fluxo de dados

Um pipeline contém o fluxo lógico para uma execução de um conjunto de atividades. Nesta seção, você criará um pipeline que contém uma atividade de fluxo de dados.

1. Vá para a guia **Integrar**. Selecione o ícone de adição ao lado do cabeçalho de pipelines e selecione Pipeline.

   ![Criar um novo pipeline](media/doc-common-process/new-pipeline.png)

1. Na página Configurações de **Propriedades** do pipeline, digite **TransformMovies** para o **nome**.

1. Em *mover e transformar* no painel *atividades* , arraste **fluxo de dados** para a tela do pipeline.

1. No pop-up adicionando página de **fluxo de dados** , selecione **criar novo**  ->  **fluxo** de dados de fluxo de dados. Toque em **OK** quando terminar.

   ![Criar um fluxo de dados](media/quickstart-data-flow/new-data-flow.png)

1. Nomeie o fluxo de dados **TransformMovies** na página **Propriedades** .

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Criar lógica de transformação na tela de fluxo de dados

Depois de criar o fluxo de dados, você será enviado automaticamente para a tela fluxo de dados. Nesta etapa, você criará um fluxo de dados que usa o MoviesDB.csv no armazenamento ADLS e agrega a classificação média de Comedies de 1910 a 2000. Em seguida, você escreverá esse arquivo de volta para o armazenamento ADLS.

1. Acima da tela fluxo de dados, deslize o controle deslizante de **depuração fluxo de dados** em. O modo de depuração permite o teste interativo da lógica de transformação em um cluster do Spark ao vivo. Os clusters de fluxo de dados levam de 5-7 minutos para ser ativados e os usuários são recomendados para ativar a depuração primeiro se planejam realizar o desenvolvimento de fluxo de dados. Para saber mais, consulte [Modo de depuração](../data-factory/concepts-data-flow-debug-mode.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).

    ![Deslize a depuração em](media/quickstart-data-flow/debug-on.png)

1. Na tela fluxo de dados, adicione uma fonte clicando na caixa **Adicionar origem** .

1. Nomeie sua fonte **MoviesDB**. Clique em **novo** para criar um novo conjunto de fonte de origem.

    ![Criar um novo conjunto de fonte de origem](media/quickstart-data-flow/new-source-dataset.png)

1. Escolha **Azure data Lake Storage Gen2**. Clique em Continuar.

    ![Escolha Azure Data Lake Storage Gen2](media/quickstart-data-flow/select-source-dataset.png)

1. Escolha **DelimitedText**. Clique em Continuar.

1. Nomeie seu DataSet **MoviesDB**. Na lista suspensa serviço vinculado, escolha **novo**.

1. Na tela de criação de serviço vinculado, nomeie seu ADLS Gen2 serviço vinculado **ADLSGen2** e especifique o método de autenticação. Em seguida, insira suas credenciais de conexão. Neste guia de início rápido, estamos usando a chave de conta para se conectar à nossa conta de armazenamento. Você pode clicar em **testar conexão** para verificar se suas credenciais foram inseridas corretamente. Clique em **Criar** quando terminar.

    ![Criar um serviço vinculado de origem](media/quickstart-data-flow/adls-gen2-linked-service.png)

1. Depois de voltar à tela de criação do conjunto de arquivos, no campo **caminho do arquivo** , insira onde o arquivo está localizado. Neste guia de início rápido, o arquivo "MoviesDB.csv" está localizado no contêiner "dados de exemplo". Como o arquivo tem cabeçalhos, marque **a primeira linha como cabeçalho**. Selecione **do repositório/conexão** para importar o esquema de cabeçalho diretamente do arquivo no armazenamento. Toque em **OK** quando terminar.

    ![Configurações do conjunto de dados de origem](media/quickstart-data-flow/source-dataset-properties.png)

1. Se o cluster de depuração for iniciado, vá para a guia **visualização de dados** da transformação origem e clique em **Atualizar** para obter um instantâneo dos dados. Você pode usar a visualização de dados para verificar se a transformação está configurada corretamente.

    ![Visualização dos dados](media/quickstart-data-flow/data-preview.png)

1. Ao lado do nó de origem na tela fluxo de dados, clique no ícone de adição para adicionar uma nova transformação. A primeira transformação que você está adicionando é um **filtro**.

    ![Adicionar um filtro](media/quickstart-data-flow/add-filter.png)

1. Nomeie sua transformação de filtro **FilterYears**. Clique na caixa expressão ao lado de **filtrar em** para abrir o construtor de expressões. Aqui você especificará sua condição de filtragem.

1. O construtor de expressões de fluxo de dados permite criar expressões interativamente para usar em várias transformações. As expressões podem incluir funções internas, colunas do esquema de entrada e parâmetros definidos pelo usuário. Para obter mais informações sobre como criar expressões, consulte [Construtor de expressões de fluxo de dados](../data-factory/concepts-data-flow-expression-builder.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).

    Neste guia de início rápido, você deseja filtrar filmes de gênero comédia que passaram entre os anos 1910 e 2000. Como ano, atualmente é uma cadeia de caracteres, você precisa convertê-lo em um inteiro usando a ```toInteger()``` função. Use os operadores maior que ou igual a (>=) e menor ou igual a (<=) para comparar com os valores de ano literal 1910 e 200-. Union essas expressões junto com o operador and (&&). A expressão é exibida como:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    Para descobrir quais filmes são Comedies, você pode usar a ```rlike()``` função para localizar o padrão ' comédia ' nos gêneros de coluna. Union a expressão RLIKE com a comparação de anos para obter:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    ![Especificar condição de filtragem](media/quickstart-data-flow/visual-expression-builder.png)

    Se você tiver um cluster de depuração ativo, poderá verificar sua lógica clicando em **Atualizar** para ver a saída da expressão em comparação com as entradas usadas. Há mais de uma resposta certa sobre como você pode realizar essa lógica usando a linguagem de expressão de fluxo de dados.

    Clique em **salvar e concluir** quando terminar com sua expressão.

1. Busque uma **visualização de dados** para verificar se o filtro está funcionando corretamente.

1. A próxima transformação que você adicionará é uma transformação **agregação** em **modificador de esquema**.

    ![Adicionar uma agregação](media/quickstart-data-flow/add-aggregate.png)

1. Nomeie sua transformação agregada **AggregateComedyRatings**. Na guia **Agrupar por** , selecione **ano** na lista suspensa para agrupar as agregações pelo ano em que o filme surgiu.

    ![Configurações de agregação 1](media/quickstart-data-flow/aggregate-settings.png)

1. Vá para a guia **agregações** . Na caixa de texto à esquerda, nomeie a coluna de agregação **AverageComedyRating**. Clique na caixa de expressão à direita para inserir a expressão de agregação por meio do construtor de expressões.

    ![Configurações de agregação 2](media/quickstart-data-flow/aggregate-settings-2.png)

1. Para obter a média de **classificação** de coluna, use a ```avg()``` função de agregação. Como a **classificação** é uma cadeia de caracteres e ```avg()``` usa uma entrada numérica, devemos converter o valor em um número por meio da ```toInteger()``` função. Esta expressão é semelhante a:

    ```avg(toInteger(Rating))```

    Clique em **salvar e concluir** quando terminar.

    ![Classificação média de comédia](media/quickstart-data-flow/average-comedy-rating.png)

1. Vá para a guia **visualização de dados** para exibir a saída da transformação. Observe que apenas duas colunas estão lá, **year** e **AverageComedyRating**.

    ![Visualização de dados agregados](media/quickstart-data-flow/transformation-output.png)

1. Em seguida, você deseja adicionar uma transformação de **coletor** em **destino**.

    ![Adicionar um coletor](media/quickstart-data-flow/add-sink.png)

1. Nomeie o **coletor** do coletor. Clique em **novo** para criar o conjunto de seus conjuntos de coleta.

1. Escolha **Azure data Lake Storage Gen2**. Clique em Continuar.

1. Escolha **DelimitedText**. Clique em Continuar.

1. Nomeie seu conjunto de **MoviesSink** de banco de seu coletor. Para o serviço vinculado, escolha o ADLS Gen2 serviço vinculado que você criou na etapa 7. Insira uma pasta de saída na qual os dados são gravados. Neste guia de início rápido, estamos gravando na pasta ' output ' no contêiner ' sample-data '. A pasta não precisa existir com antecedência e pode ser criada dinamicamente. Defina **a primeira linha como o cabeçalho** como verdadeiro e selecione **nenhum** para o **esquema de importação**. Toque em **OK** quando terminar.

    ![Propriedades do conjunto de banco de coleta](media/quickstart-data-flow/sink-dataset-properties.png)

Agora você concluiu a criação do fluxo de dados. Você está pronto para executá-lo em seu pipeline.

## <a name="running-and-monitoring-the-data-flow"></a>Executando e monitorando o fluxo de dados

Você pode depurar um pipeline antes de publicá-lo. Nesta etapa, você vai disparar uma execução de depuração do pipeline de fluxo de dados. Embora a visualização de dados não grave dados, uma execução de depuração gravará dados no destino do coletor.

1. Vá para a tela do pipeline. Clique em **depurar** para disparar uma execução de depuração.

    ![Depurar pipeline](media/quickstart-data-flow/debug-pipeline.png)

1. A depuração de pipeline de atividades de fluxo de dados usa o cluster de depuração ativo, mas ainda levará pelo menos um minuto para ser inicializado. Você pode acompanhar o progresso por meio da guia **saída** . Quando a execução for bem-sucedida, clique no ícone de óculos para abrir o painel Monitoramento.

    ![Saída de depuração](media/quickstart-data-flow/debugging-output.png)

1. No painel Monitoramento, você pode ver o número de linhas e o tempo gasto em cada etapa de transformação.

    ![Monitoramento de transformação](media/quickstart-data-flow/4-transformations.png)

1. Clique em uma transformação para obter informações detalhadas sobre as colunas e o particionamento dos dados.

    ![Detalhes da transformação](media/quickstart-data-flow/transformation-details.png)

Se você seguiu este guia de início rápido corretamente, deve ter escrito 83 linhas e 2 colunas na pasta do coletor. Você pode verificar os dados verificando seu armazenamento de BLOBs.


## <a name="next-steps"></a>Próximas etapas

Avance para os seguintes artigos para saber mais sobre o suporte do Azure Synapse Analytics:

> [!div class="nextstepaction"]
> [Pipeline e atividades](../data-factory/concepts-pipelines-activities.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) 
>  [Visão geral](../data-factory/concepts-data-flow-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) 
>  do fluxo de dados de mapeamento [Linguagem de expressão de fluxo de dados](../data-factory/data-flow-expression-functions.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)
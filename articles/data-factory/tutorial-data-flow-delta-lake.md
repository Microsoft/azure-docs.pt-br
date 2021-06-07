---
title: Delta Lake ETL com fluxos de dados
description: Este tutorial fornece instruções passo a passo para usar fluxos de dados para transformar e analisar dados no Delta Lake
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 02/09/2021
ms.openlocfilehash: cb8d44353e826df14ed3baab2c4ca66ffed4a569
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100416955"
---
# <a name="transform-data-in-delta-lake-using-mapping-data-flows"></a>Transformar dados no Delta Lake usando fluxos de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Se estiver se familiarizando com o Azure Data Factory, confira [Introdução ao Azure Data Factory](introduction.md).

Neste tutorial, você usará a tela de fluxo de dados para criar fluxos de dados que permitem analisar e transformar dados em Azure Data Lake Storage (ADLS) Gen2 e armazená-los no Delta Lake.

## <a name="prerequisites"></a>Pré-requisitos
* **Assinatura do Azure**. Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* **Conta de Armazenamento do Azure**. Você usa o armazenamento ADLS como um repositório de dados de *origem* e de *coletor* . Se você não tiver uma conta de armazenamento, confira [Criar uma conta de armazenamento do Azure](../storage/common/storage-account-create.md) a fim de conhecer as etapas para criar uma.

O arquivo que estamos transformando neste tutorial é MoviesDB.csv, que pode ser encontrado [aqui](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/moviesDB2.csv). Para recuperar o arquivo do GitHub, copie o conteúdo para um editor de texto de sua escolha para salvar localmente como um arquivo. csv. Para carregar o arquivo em sua conta de armazenamento, consulte [carregar BLOBs com o portal do Azure](../storage/blobs/storage-quickstart-blobs-portal.md). Os exemplos referenciarão um contêiner chamado ' sample-data '.

## <a name="create-a-data-factory"></a>Criar uma data factory

Nesta etapa, você cria um data factory e abre o Data Factory UX para criar um pipeline no data factory.

1. Abra o **Microsoft Edge** ou **Google Chrome**. Atualmente, a interface do usuário Data Factory tem suporte apenas nos navegadores da Web Microsoft Edge e Google Chrome.
1. No menu à esquerda, selecione **criar um recurso**  >  **integração**  >  **Data Factory**
1. Na página **novo data Factory** , em **nome**, insira **ADFTutorialDataFactory**
1. Selecione a **assinatura** do Azure na qual deseja criar o data factory.
1. Em **Grupo de Recursos**, use uma das seguintes etapas:

    a. Selecione **Usar existente** e selecione um grupo de recursos existente na lista suspensa.

    b. Selecione **Criar novo** e insira o nome de um grupo de recursos. 
         
    Para saber mais sobre grupos de recursos, confira [Usar grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/management/overview.md). 
1. Em **Versão**, selecione **V2**.
1. Em **Local**, informe uma localização para o data factory. Apenas os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados (por exemplo, o armazenamento do Azure e o SQL Database) e as computações (por exemplo, Azure HDInsight) usados pelo data factory podem estar em outras regiões.
1. Selecione **Criar**.
1. Depois que a criação for concluída, você verá o aviso no centro de notificações. Selecione **Ir para o recurso** para navegar até a página do Data Factory.
1. Clique em **Criar e Monitorar** para iniciar a IU do Azure Data Factory em uma guia separada.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Criar um pipeline com uma atividade de fluxo de dados

Nesta etapa, você criará um pipeline que contém uma atividade de fluxo de dados.

1. Na página **Introdução**, selecione **Criar pipeline**.

   ![Criar um pipeline](./media/doc-common-process/get-started-page.png)

1. Na guia **geral** do pipeline, digite **DeltaLake** para o **nome** do pipeline.
1. Na barra superior da fábrica, deslize o controle deslizante de **depuração do fluxo de dados** em. O modo de depuração permite o teste interativo da lógica de transformação em um cluster do Spark ao vivo. Os clusters de fluxo de dados levam de 5-7 minutos para ser ativados e os usuários são recomendados para ativar a depuração primeiro se planejam realizar o desenvolvimento de fluxo de dados. Para saber mais, consulte [Modo de depuração](concepts-data-flow-debug-mode.md).

    ![Atividade de fluxo de dados](media/tutorial-data-flow/dataflow1.png)
1. No painel **atividades** , expanda o acorde **e a transformação** . Arraste e solte a atividade **fluxo de dados** do painel para a tela do pipeline.

    ![Captura de tela que mostra as telas de pipeline em que é possível descartar a atividade de fluxo de dados.](media/tutorial-data-flow/activity1.png)
1. No pop-up **adicionando fluxo de dados** , selecione **criar novo fluxo de dados** e, em seguida, nomeie o fluxo de dados **DeltaLake**. Clique em Concluir quando tiver terminado.

    ![Captura de tela que mostra onde você nomeou o fluxo de dados ao criar um novo fluxo de dados.](media/tutorial-data-flow/activity2.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Criar lógica de transformação na tela de fluxo de dados

Você vai gerar dois fluxos de dados neste tutorial. O único fluxo de dados é uma fonte simples de coletar para gerar um novo Lago Delta a partir do arquivo CSV de filmes acima. Por fim, você criará esse design de fluxo abaixo para atualizar dados no Delta Lake.

![Fluxo final](media/data-flow/data-flow-tutorial-6.png "Fluxo final")

### <a name="tutorial-objectives"></a>Objetivos do tutorial

1. Pegar a origem do conjunto de MoviesCSV do banco de data acima, formar um novo Lago Delta a partir dele
1. Criar a lógica para classificações atualizadas de 1988 filmes para ' 1 '
1. Excluir todos os filmes de 1950
1. Insira novos filmes para 2021 duplicando os filmes de 1960

### <a name="start-from-a-blank-data-flow-canvas"></a>Iniciar de uma tela de fluxo de dados em branco

1. Clique na transformação origem
1. Clique em novo ao lado de conjunto de entrada no painel inferior 1 criar um novo serviço vinculado para ADLS Gen2
1. Escolher texto delimitado para o tipo de conjunto de tipos
1. Nomeie o conjunto de "MoviesCSV" 
1. Aponte para o arquivo MoviesCSV que você carregou para o armazenamento acima
1. Defina-o para ser delimitado por vírgula e incluir o cabeçalho na primeira linha 
1. Vá para a guia projeção de origem e clique em "detectar tipos de dados"
1. Depois de definir sua projeção, você pode continuar 
1. Adicionar uma transformação de coletor
1. Delta é um tipo de conjunto de linhas embutido. Você precisará apontar para sua conta de armazenamento ADLS Gen2.
   
   ![Conjunto de linhas embutido](media/data-flow/data-flow-tutorial-5.png "Conjunto de linhas embutido")

1. Escolha um nome de pasta no contêiner de armazenamento no qual você gostaria que o ADF criasse o Delta Lake
1. Volte para o designer de pipeline e clique em Depurar para executar o pipeline no modo de depuração com apenas essa atividade de fluxo de dados na tela. Isso gerará o novo data Lake Delta no ADLS Gen2.
1. Em recursos de fábrica, clique em novo > fluxo de dados 
1. Use o MoviesCSV novamente como uma origem e clique em "detectar tipos de dados" novamente
1. Adicionar uma transformação de filtro à sua transformação de origem no grafo
1. Permitir somente linhas de filme que correspondam aos três anos com os quais você vai trabalhar, que será 1950, 1988 e 1960
1. Atualizar classificações para cada filme 1988 para ' 1 ' adicionando agora uma transformação de coluna derivada à sua transformação de filtro
1. Na mesma coluna derivada, Crie filmes para 2021 ao levar um ano existente e alterar o ano para 2021. Vamos escolher 1960.
1. É assim que as três colunas derivadas se parecerão

   ![Coluna derivada](media/data-flow/data-flow-tutorial-2.png "Coluna derivada")
   
1. ```Update, insert, delete, and upsert``` as políticas são criadas na transformação alterar linha. Adicione uma transformação alterar linha após a coluna derivada.
1. As políticas de alteração de linha devem ter esta aparência.

   ![Alterar linha](media/data-flow/data-flow-tutorial-3.png "Alterar linha")
   
1. Agora que você definiu a política apropriada para cada tipo de alteração de linha, verifique se as regras de atualização apropriadas foram definidas na transformação do coletor

   ![Coletor](media/data-flow/data-flow-tutorial-4.png "Coletor")
   
1. Aqui estamos usando o coletor Delta Lake para seu ADLS Gen2 data Lake e permitindo inserções, atualizações, exclusões. 
1. Observe que as colunas de chave são uma chave composta composta da coluna de chave primária do filme e da coluna year. Isso ocorre porque criamos filmes 2021 falsos duplicando as 1960 linhas. Isso evita colisões ao pesquisar as linhas existentes fornecendo exclusividade.

### <a name="download-completed-sample"></a>Baixar exemplo concluído
[Aqui está um exemplo de solução para o pipeline Delta com um fluxo de dados para atualizar/excluir linhas no Lake:](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/DeltaPipeline.zip)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a [linguagem de expressão de fluxo de dados](data-flow-expression-functions.md).

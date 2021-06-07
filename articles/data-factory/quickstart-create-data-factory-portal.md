---
title: Criar um data factory do Azure usando a interface do usuário do Azure Data Factory
description: Crie um data factory com um pipeline que copia dados de uma localização no Armazenamento de Blobs do Azure para outro local.
author: linda33wj
ms.service: data-factory
ms.topic: quickstart
ms.date: 12/14/2020
ms.author: jingwang
ms.openlocfilehash: 8b33005b7f0d08e6da963457813b012199d89f5f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104654984"
---
# <a name="quickstart-create-a-data-factory-by-using-the-azure-data-factory-ui"></a>Início Rápido: Criar um data factory usando a interface do usuário do Azure Data Factory 

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está sendo usada:"]
> * [Versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versão atual](quickstart-create-data-factory-portal.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este guia de início rápido descreve como usar a interface do usuário do Azure Data Factory para criar e monitorar um data factory. O pipeline que você criar nesse data factory *copia* dados de uma pasta para outra no Armazenamento de Blobs do Azure. Para *transformar* dados usando o Azure Data Factory, confira [Fluxo de dados de mapeamento](concepts-data-flow-overview.md).

> [!NOTE]
> Se você for novo no Azure Data Factory, confira a [Introdução ao Azure Data Factory](introduction.md) antes de fazer este guia de início rápido. 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="video"></a>Vídeo 
Assistir a este vídeo o ajuda a entender a interface do usuário do Data Factory: 
>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Visually-build-pipelines-for-Azure-Data-Factory-v2/Player]

## <a name="create-a-data-factory"></a>Criar uma data factory

1. Iniciar o navegador da Web **Microsoft Edge** ou **Google Chrome**. Atualmente, a interface do usuário do Data Factory tem suporte apenas nos navegadores da Web Microsoft Edge e Google Chrome.
1. Vá para o [Portal do Azure](https://portal.azure.com). 
1. No menu do portal do Azure, selecione **Criar um recurso**.
1. Selecione **Integração** e escolha **Data Factory**. 
   
   :::image type="content" source="./media/doc-common-process/new-azure-data-factory-menu.png" alt-text="Seleção do Data Factory no painel Novo.":::

1. Na página **Criar Data Factory**, na guia **Informações Básicas**, selecione sua **Assinatura** do Azure na qual deseja criar o data factory.
1. Em **Grupo de Recursos**, use uma das seguintes etapas:

    a. Selecione um grupo de recursos existente na lista suspensa.

    b. Escolha **Criar** e insira o nome de um grupo de recursos.
    
    Para saber mais sobre grupos de recursos, confira [Usar grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/management/overview.md). 

1. Em **Região**, selecione a localização para o data factory.

   A lista mostra somente os locais aos quais o Data Factory dá suporte e em que os metadados do Azure Data Factory serão armazenados. Os armazenamentos de dados associados (como o Armazenamento do Azure e o Banco de Dados SQL do Azure) e os serviços de computação (como o Azure HDInsight) usados pelo Data Factory podem ser executados em outras regiões.
 
1. Em **Nome**, insira **ADFTutorialDataFactory**.
   O nome do Azure Data Factory deve ser *globalmente exclusivo*. Se você visualizar o seguinte erro, altere o nome de data factory (por exemplo, **&lt;yourname&gt;ADFTutorialDataFactory**) e tente criar novamente. Para ver as regras de nomenclatura para artefatos do Data Factory consulte o artigo [Data Factory - regras de nomenclatura](naming-rules.md).

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Mensagem de erro para o nome duplicado do novo data factory.":::

1. Para **Versão**, selecione **V2**.

1. Selecione **Avançar: Configuração do Git** e marque a caixa de seleção **Configurar o Git mais tarde**.

1. Selecione **Examinar + criar** e escolha **Criar** depois que a validação for aprovada. Após a conclusão da criação, selecione **Ir para o recurso** para navegar até a página do **Data Factory**. 

1. Selecione o bloco **Criar e Monitorar** para iniciar o aplicativo de interface do usuário do Azure Data Factory em uma guia separada do navegador.
   
    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Home page do Azure Data Factory com o bloco Criar e Monitorar.":::
   
   > [!NOTE]
   > Se você vir que o navegador da Web está preso em "Autorizando", desmarque a caixa de seleção **Bloquear cookies de terceiros e dados do site**. Ou a mantenha selecionada, crie uma exceção para **login.microsoftonline.com** e tente abrir o aplicativo novamente.
   

## <a name="create-a-linked-service"></a>Criar um serviço vinculado
Neste procedimento, você criará um serviço vinculado para vincular sua Conta de Armazenamento do Azure ao Data Factory. O serviço vinculado tem as informações de conexão que o serviço do Data Factory usa no runtime para se conectar a ele.

1. Na página da interface do usuário do Azure Data Factory, abra a guia [**Gerenciar**](./author-management-hub.md) no painel esquerdo.

1. Na página Serviços vinculados, selecione **+Novo** para criar um serviço vinculado.

   :::image type="content" source="./media/doc-common-process/new-linked-service.png" alt-text="Novo serviço vinculado.":::
   
1. Na página **Novo Serviço Vinculado**, selecione **Armazenamento de Blobs do Azure** e selecione **Continuar**. 

1. Na página Novo Serviço Vinculado (Armazenamento de Blobs do Azure), conclua as seguintes etapas: 

   a. Para o campo **Nome**, insira **AzureStorageLinkedService**.

   b. Selecione o nome da sua conta de Armazenamento do Azure em **Nome da conta de armazenamento**.

   c. Selecione **Testar conectividade** para confirmar se o serviço do Data Factory pode se conectar à conta de armazenamento. 

   d. Selecione **Criar** para salvar o serviço vinculado. 

      :::image type="content" source="./media/quickstart-create-data-factory-portal/linked-service.png" alt-text="Serviço vinculado.":::


## <a name="create-datasets"></a>Criar conjuntos de dados
Neste procedimento, você criará dois conjuntos de dados: **InputDataset** e **OutputDataset**. Esses conjuntos de dados são do tipo **AzureBlob**. Eles se referem ao Serviço vinculado do Armazenamento do Azure que você criou na seção anterior. 

O conjunto de dados de entrada representa os dados de origem na pasta de entrada. Na definição de conjunto de dados de entrada, especifique o contêiner de blob (**adftutorial**), a pasta (**entrada**) e o arquivo (**emp.txt**) que contém os dados de origem. 

Esse conjunto de dados de saída representa os dados que são copiados para o destino. Na definição de conjunto de dados de saída, especifique o contêiner de blob (**adftutorial**), a pasta (**saída**) e o arquivo para o qual os dados são copiados. Cada execução de um pipeline tem uma ID exclusiva associada a ele. Você pode acessar essa ID, usando a variável do sistema **RunId**. O nome do arquivo de saída é avaliado dinamicamente com base na ID de execução do pipeline.   

Nas configurações de serviço vinculado, você especificou a conta de Armazenamento do Azure que contém os dados de origem. Nas configurações do conjunto de dados de origem, especifique onde exatamente os dados de origem residem (contêiner de blob, pasta e arquivo). Nas configurações de conjunto de dados do coletor, especifique para onde os dados são copiados (contêiner de blob, pasta e arquivo). 

1. Selecione a guia **Criar** no painel esquerdo.

1. Selecione o botão **+** (mais) adição e, em seguida, selecione **Conjunto de Dados**.

   :::image type="content" source="./media/quickstart-create-data-factory-portal/new-dataset-menu.png" alt-text="Menu para criação um conjunto de dados.":::

1. Na página **Novo Conjunto de Dados**, selecione **Armazenamento de Blobs do Azure** e, em seguida, selecione **Concluir**. 

1. Na página **Selecionar Formato**, escolha o tipo de formato dos dados e, em seguida, selecione **Continuar**. Nesse caso, selecione **Binário** ao copiar arquivos no estado em que se encontram sem analisar o conteúdo.

   :::image type="content" source="./media/quickstart-create-data-factory-portal/select-format.png" alt-text="Selecionar o formato.":::   
   
1. Na página **Definir Propriedades**, conclua as seguintes etapas:

    a. Em **Nome**, insira **InputDataset**. 

    b. Selecione **AzureStorageLinkedService** para **Serviço vinculado**.

    c. Para **Caminho do arquivo**, selecione o botão **Procurar**.

    d. Na janela **Escolher um arquivo ou uma pasta**, navegue até a pasta **entrada** no contêiner **adftutorial**, selecione o arquivo **emp.txt** e selecione **OK**.
    
    e. Selecione **OK**.   

    :::image type="content" source="./media/quickstart-create-data-factory-portal/set-properties-for-inputdataset.png" alt-text="Definir as propriedades de InputDataset.":::

1. Repita as etapas para criar o conjunto de dados de saída:  

    a. Selecione o botão **+** (mais) adição e, em seguida, selecione **Conjunto de Dados**.

    b. Na página **Novo Conjunto de Dados**, selecione **Armazenamento de Blobs do Azure** e, em seguida, selecione **Concluir**.

    c. Na página **Selecionar Formato**, escolha o tipo de formato dos dados e, em seguida, selecione **Continuar**.

    d. Na página **Definir Propriedades**, especifique **OutputDataset** para o nome. Selecione **AzureStorageLinkedService** como serviço vinculado.

    e. Em **Caminho do arquivo**, insira **adftutorial/output**. Se a pasta **output** não existir, a atividade de cópia a criará em runtime.

    f. Selecione **OK**.   

    :::image type="content" source="./media/quickstart-create-data-factory-portal/set-properties-for-outputdataset.png" alt-text="Definir as propriedades de OutputDataset.":::    

## <a name="create-a-pipeline"></a>Criar um pipeline 
Neste procedimento, você criará e validará um pipeline com uma atividade Copiar que usa o conjunto de dados de entrada e saída. A Atividade de cópia copia os dados do arquivo especificado por você nas configurações do conjunto de dados de entrada para o arquivo especificado por você nas configurações do conjunto de dados de saída. Se o conjunto de dados de entrada especifica apenas uma pasta (não o nome de arquivo), a Atividade de cópia copia todos os arquivos da pasta de origem para o destino. 

1. Selecione o botão **+** (mais) adição e, em seguida, selecione **Pipeline**. 

1. No painel geral, em **Propriedades**, especifique **CopyPipeline** para **Nome**. Em seguida, recolha o painel clicando no ícone Propriedades no canto superior direito.

1. Na caixa de ferramentas **Atividades**, expanda **Mover e transformar**. Arraste a atividade **Copiar Dados** da caixa de ferramentas **Atividades** para a superfície do Designer de Pipeline. Você também pode pesquisar atividades na caixa de ferramentas **Atividades**. Especifique **CopyFromBlobToBlob** para o **Nome**.

    :::image type="content" source="./media/quickstart-create-data-factory-portal/copy-activity.png" alt-text="Criação de uma atividade de cópia de dados.":::   

1. Alterne para a guia **Fonte** nas configurações da atividade de cópia e selecione **InputDataset** para o **Conjunto de dados de origem**.

1. Alterne para a guia **Coletor** nas configurações da atividade de cópia e selecione **OutputDataset** para o **Conjunto de dados do coletor**.

1. Clique em **Validar** na barra de ferramentas do pipeline sobre a tela para validar as configurações de pipeline. Confirme se esse pipeline foi validado com êxito. Para fechar a saída de validação, selecione o botão Validação no canto superior direito. 

   :::image type="content" source="./media/quickstart-create-data-factory-portal/pipeline-validate.png"   alt-text="Validar um pipeline.":::

## <a name="debug-the-pipeline"></a>Depurar o pipeline
Nesta etapa, você depura o pipeline antes de implantá-lo no Data Factory. 

1. Na barra de ferramentas do pipeline acima da tela, clique em **Depurar** para disparar uma execução de teste. 
    
1. Confirme se você vê o status da execução do pipeline na guia **Saída** das configurações do pipeline na parte inferior. 
 
    :::image type="content" source="./media/quickstart-create-data-factory-portal/pipeline-output.png" alt-text="Saída da execução do pipeline":::

1. Confirme que você vê um arquivo de saída na pasta **saída** do contêiner **adftutorial**. O serviço Data Factory cria a pasta de saída automaticamente caso ela não exista. 

## <a name="trigger-the-pipeline-manually"></a>Disparar o pipeline manualmente
Nesta procedimento, você implanta entidades (serviços vinculados, conjuntos de dados, pipelines) ao Azure Data Factory. Depois, dispare manualmente a execução do pipeline. 

1. Antes de disparar um pipeline, você deve publicar as entidades no Data Factory. Para publicar, selecione **Publicar tudo** na parte superior. 

    :::image type="content" source="./media/quickstart-create-data-factory-portal/publish-all.png" alt-text="Publicar tudo.":::    

1. Para disparar o pipeline manualmente, selecione **Adicionar Gatilho** na barra de ferramentas do pipeline e, em seguida, selecione **Disparar Agora**. Na página **Execução de pipeline**, selecione **OK**.

## <a name="monitor-the-pipeline"></a>Monitorar o Pipeline

1. Alterne para a guia **Monitorar** à esquerda. Use o botão **Atualizar** para atualizar a lista.

    :::image type="content" source="./media/quickstart-create-data-factory-portal/monitor-trigger-now-pipeline.png" alt-text="Guia para monitoramento de execuções de pipeline"::: 
1. Selecione o link **CopyPipeline**; você verá o status de execução da atividade de cópia nesta página. 

1. Para exibir detalhes sobre a operação de cópia, selecione o link **Detalhes** (imagem de óculos). Para obter detalhes sobre as propriedades, confira [Visão geral da atividade de cópia](copy-activity-overview.md). 

   :::image type="content" source="./media/quickstart-create-data-factory-portal/copy-operation-details.png" alt-text="Detalhes da operação de cópia.":::
1. Confirme que você vê um arquivo novo na pasta de **saída**. 
1. Alterne novamente para a exibição **Execuções de pipeline** do modo de exibição **Execuções de atividade** selecionando o link **Todas as execuções de pipeline**. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Disparar o pipeline em um cronograma
Este procedimento é opcional neste tutorial. Você pode criar um *agendador de gatilho* para agendar a execução periódica do pipeline (por hora, diariamente, e assim por diante). Nesta procedimento, você cria um gatilho para ser executado a cada minuto até a data e hora de término especificadas. 

1. Alterne para a guia **Autor**. 

1. Acesse o pipeline, selecione **Adicionar Gatilho** na barra de ferramentas do pipeline e, em seguida, selecione **Novo/Editar**. 

1. Na página **Adicionar gatilhos**, selecione **Escolher gatilho** e, em seguida, selecione **Novo**. 

1. Na página **Novo Gatilho**, no campo **Final**, selecione **Na Data**, especifique como hora de término alguns minutos após a hora atual e selecione **OK**. 

   Um custo associado a cada execução de pipeline, então, especifique o a hora de término como apenas alguns minutos após a hora de início. Verifique se está como o mesmo dia. No entanto, verifique se há tempo suficiente para a execução do pipeline entre a hora da publicação e a hora de término. O gatilho só entra em vigor depois de você publicar a solução no Data Factory, e não ao salvar o gatilho na interface do usuário. 

1. Na página **Novo Gatilho**, marque a caixa de seleção **Ativado** e, em seguida, selecione **OK**. 

    :::image type="content" source="./media/quickstart-create-data-factory-portal/trigger-settings-next.png" alt-text="Nova configuração de gatilho.":::   
1. Examine a mensagem de aviso e selecione **OK**.

1. Selecione **Publicar tudo** para publicar as alterações no Data Factory. 

1. Alterne para a guia **Monitorar** à esquerda. Selecione **Atualizar** para atualizar a lista. Você verá que o pipeline é executado uma vez por minuto desde o momento da publicação até hora de término. 

   Observe os valores na coluna **DISPARADO POR**. A execução do gatilho manual foi feita em uma etapa anterior (**Disparar agora**). 

1. Alterne para o modo de exibição **Execuções de gatilho**. 

1. Confirme que um arquivo de saída é criado para cada execução de pipeline até a data e hora de término especificadas na pasta **saída**. 

## <a name="next-steps"></a>Próximas etapas
O pipeline neste exemplo copia dados de uma localização para outra em um Armazenamento de Blobs do Azure. Percorra os [tutoriais](tutorial-copy-data-portal.md) para saber mais sobre o uso do Data Factory em mais cenários.

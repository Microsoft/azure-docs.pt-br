---
title: Copiar dados usando a ferramenta de Copiar Dados do Azure
description: Crie um Azure Data Factory e use a ferramenta Copiar Dados para copiar dados de uma localização no Armazenamento de Blobs do Azure para outra.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: quickstart
ms.date: 11/09/2020
ms.openlocfilehash: 78bc815aff195124d3e79a83445b8d12506fc70d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104613083"
---
# <a name="quickstart-use-the-copy-data-tool-to-copy-data"></a>Início Rápido: Use a ferramenta Copy Data para copiar dados

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está sendo usada:"]
> * [Versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versão atual](quickstart-create-data-factory-copy-data-tool.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Neste guia de início rápido, você usa o Portal do Azure para criar um data factory. Em seguida, você usa a ferramenta Copy Data para criar um pipeline que copia dados de uma pasta no armazenamento de Blobs do Azure para outra pasta. 

> [!NOTE]
> Se você for novo no Azure Data Factory, confira a [Introdução ao Azure Data Factory](introduction.md) antes de fazer este guia de início rápido. 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

## <a name="create-a-data-factory"></a>Criar uma data factory

1. Iniciar o navegador da Web **Microsoft Edge** ou **Google Chrome**. Atualmente, a interface do usuário do Data Factory tem suporte apenas nos navegadores da Web Microsoft Edge e Google Chrome.
1. Vá para o [Portal do Azure](https://portal.azure.com). 
1. No menu do portal do Azure, selecione **Criar um recurso** > **Integração** > **Data Factory**:

    ![Criação do novo data factory](./media/doc-common-process/new-azure-data-factory-menu.png)

1. Na página **Novo data factory**, insira **ADFTutorialDataFactory** no campo **Nome**. 
 
   O nome do Azure Data Factory precisa ser *globalmente exclusivo*. Se você visualizar o seguinte erro, altere o nome de data factory (por exemplo, **&lt;yourname&gt;ADFTutorialDataFactory**) e tente criar novamente. Para ver as regras de nomenclatura para artefatos do Data Factory consulte o artigo [Data Factory - regras de nomenclatura](naming-rules.md).
  
   ![Erro quando um nome não está disponível](./media/doc-common-process/name-not-available-error.png)
1. Para **Assinatura**, selecione a assinatura do Azure na qual você deseja criar o data factory. 
1. Para o **Grupo de Recursos**, use uma das seguintes etapas:
     
   - Selecione **Usar existente** e selecione um grupo de recursos existente na lista. 
   - Selecione **Criar novo** e insira o nome de um grupo de recursos.   
         
   Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/management/overview.md).  
1. Para **Versão**, selecione **V2**.
1. Em **Local**, selecione uma localização para o data factory.

   A lista mostra somente os locais aos quais o Data Factory dá suporte e em que os metadados do Azure Data Factory serão armazenados. Os armazenamentos de dados associados (como o Armazenamento do Azure e o Banco de Dados SQL do Azure) e os serviços de computação (como o Azure HDInsight) usados pelo Data Factory podem ser executados em outras regiões.

1. Selecione **Criar**.

1. Após a criação, a página do **Data Factory** será exibida. Clique no bloco **Criar e Monitorar** para iniciar o aplicativo de interface do usuário (IU) do Azure Data Factory em uma guia separada.
   
:::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Home page do Azure Data Factory com o bloco Criar e Monitorar.":::

## <a name="start-the-copy-data-tool"></a>Iniciar a ferramenta Copy Data

1. Na página de **Introdução**, selecione o bloco **Copy Data** para iniciar a ferramenta Copy Data. 

   ![Bloco “Copy Data”](./media/doc-common-process/get-started-page.png)

1. Na página **Propriedades** da ferramenta de Cópia de dados, você pode especificar um nome para o pipeline e sua descrição e, depois, selecionar **Avançar**. 

   ![Página “Propriedades”](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-properties-page.png)
1. Na página **Configurações do armazenamento de dados**, conclua as seguintes etapas:

    a. Clique em **+ Criar nova conexão** para adicionar uma conexão.

    b. Selecione o tipo de serviço vinculado que deseja criar para a conexão de origem. Neste tutorial, usamos o **Armazenamento de Blobs do Azure**. Selecione-o na galeria e, em seguida, selecione **Continuar**.
    
    ![Selecionar o Blob](./media/quickstart-create-data-factory-copy-data-tool/select-blob-source.png)

    c. Na página **Novo Serviço Vinculado (Armazenamento de Blobs do Azure)** , especifique um nome para o serviço vinculado. Selecione a conta de armazenamento na lista **Nome da conta de armazenamento**, teste a conexão e, em seguida, selecione **Criar**. 

    ![Configurar a conta de armazenamento de Blobs do Azure](./media/quickstart-create-data-factory-copy-data-tool/configure-blob-storage.png)

    d. Selecione o serviço vinculado recém-criado como fonte e, em seguida, clique em **Avançar**.


1. Na página **Escolha o arquivo de entrada ou a pasta**, execute as seguintes etapas:

   a. Clique em **Navegar** para navegar até a pasta **adftutorial/input**, selecione o arquivo **emp.txt** e, em seguida, clique em **Escolher**. 

   d. Marque a caixa de seleção **Cópia binária** para copiar o arquivo no estado em que se encontra e, em seguida, selecione **Avançar**. 

   ![Na página “Escolher o arquivo de entrada ou a pasta”](./media/quickstart-create-data-factory-copy-data-tool/select-binary-copy.png)


1. Na página **Armazenamento de dados de destino**, selecione o serviço vinculado do **Armazenamento de Blobs do Azure** que você criou e selecione **Avançar**. 

1. Na página **Escolher o arquivo ou a pasta de saída**, insira **adftutorial/output** no caminho de pasta e, em seguida, selecione **Avançar**. 

   ![Página “Escolha o arquivo ou a pasta de saída”](./media/quickstart-create-data-factory-copy-data-tool/configure-sink-path.png) 

1. Na página **Configurações**, selecione **Avançar** para usar as configurações padrão. 

1. Na página **Resumo**, revise todas as configurações e selecione **Avançar**. 

1. Na página **Implantação concluída**, selecione **Monitor** para monitorar o pipeline que você criou. 

    ![Página “Implantação concluída”](./media/quickstart-create-data-factory-copy-data-tool/deployment-page.png)

1. O aplicativo alterna para a guia **Monitor**. Confira o status do pipeline nesta guia. Selecione **Atualizar** para atualizar a lista. Clique no link em **NOME DO PIPELINE** para ver os detalhes da execução de atividade ou execute o pipeline novamente. 
   
    ![Atualizar o pipeline](./media/quickstart-create-data-factory-copy-data-tool/refresh-pipeline.png)

1. Na página Execuções de atividade, selecione o link **Detalhes** (ícone de óculos) na coluna **NOME DA ATIVIDADE** para obter mais detalhes sobre a operação de cópia. Para obter detalhes sobre as propriedades, confira [Visão geral da atividade de cópia](copy-activity-overview.md). 

1. Para voltar à exibição Execuções de Pipeline, selecione o link **TODAS as execuções de pipeline** no menu de navegação estrutural. Para atualizar a exibição, selecione **Atualizar**. 

1. Verifique que o arquivo **emp.txt** foi criado na pasta de **saída** do contêiner **adftutorial**. O serviço Data Factory cria a pasta de saída automaticamente caso ela não exista. 

1. Alterne para a guia **Autor** acima da guia **Monitor** no painel esquerdo para que você possa editar serviços vinculados, conjuntos de dados e pipelines. Para saber mais sobre editá-los na IU do Data Factory, consulte [Criar um Data Factory usando o portal do Azure](quickstart-create-data-factory-portal.md).

    ![Selecionar a guia Autor](./media/quickstart-create-data-factory-copy-data-tool/select-author.png)

## <a name="next-steps"></a>Próximas etapas
O pipeline neste exemplo copia dados de uma localização para outra em um Armazenamento de Blobs do Azure. Percorra os [tutoriais](tutorial-copy-data-portal.md) para saber mais sobre o uso do Data Factory em mais cenários. 

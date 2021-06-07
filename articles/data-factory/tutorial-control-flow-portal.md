---
title: Como ramificar e encadear atividades em um pipeline usando portal do Azure
description: Saiba como controlar o fluxo de dados no pipeline do Azure Data Factory no portal do Azure.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/11/2018
ms.openlocfilehash: 034b0400190cadd750e7420ab37800783afa050c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783414"
---
# <a name="branching-and-chaining-activities-in-an-azure-data-factory-pipeline-using-the-azure-portal"></a>Como ramificar e encadear atividades em um pipeline do Azure Data Factory usando o portal do Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Neste tutorial, você deve criar um pipeline de Data Factory que apresente alguns dos recursos de fluxo de controle. Esse pipeline faz uma cópia simples de um contêiner no Armazenamento de Blobs do Azure para outro contêiner na mesma conta de armazenamento. Se a atividade de cópia for bem-sucedida, o pipeline enviará detalhes da operação de cópia bem-sucedida (tais como a quantidade de dados gravados) em um email de êxito. Se a atividade de cópia falhar, o pipeline enviará detalhes da falha de cópia (por exemplo, a mensagem de erro) em um email de falha. Ao longo do tutorial, você verá como passar parâmetros.

Uma visão geral de alto nível do cenário: ![O diagrama mostra o Armazenamento de Blobs do Azure, que é o destino de uma cópia. Em caso de êxito, ele envia um email com detalhes e, em caso de falha, ele envia um email com detalhes do erro.](media/tutorial-control-flow-portal/overview.png)

Neste tutorial, você realizará os seguintes procedimentos:

> [!div class="checklist"]
> * Criar um data factory.
> * Criar um serviço vinculado do Armazenamento do Azure.
> * Criar um conjunto de dados do Blob do Azure
> * Criar um pipeline que contém uma atividade de Cópia e uma atividade da Web
> * Enviar saídas de atividades para as atividades subsequentes
> * Utilizar passagem de parâmetros e variáveis do sistema
> * Iniciar uma execução de pipeline
> * Monitorar as execuções de pipeline e de atividade

Este tutorial usa o portal do Azure. Você pode usar outros mecanismos para interagir com o Azure Data Factory, consulte "Guias de início rápido" no sumário.

## <a name="prerequisites"></a>Pré-requisitos

* **Assinatura do Azure**. Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Conta de Armazenamento do Azure**. Você usa o Armazenamento de Blobs como um armazenamento de dados de **origem**. Se você não tiver uma conta de Armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../storage/common/storage-account-create.md) para conhecer as etapas para criar uma.
* **Banco de dados SQL do Azure**. Você usa o banco de dados como um armazenamento de dados de **coletor**. Se você não tiver um banco de dados no Banco de Dados SQL do Azure, confira o artigo [Criar um banco de dados no Banco de Dados SQL do Azure](../azure-sql/database/single-database-create-quickstart.md) para ver as etapas para a criação de um.

### <a name="create-blob-table"></a>Criar tabela de blob

1. Inicie o Bloco de notas. Copie o texto a seguir e salve-o como um arquivo **input.txt** no disco.

    ```
    John,Doe
    Jane,Doe
    ```
2. Use ferramentas como o [Gerenciador de Armazenamento do Azure](https://storageexplorer.com/) para executar estas etapas:
    1. Crie o contêiner **adfv2branch**.
    2. Crie a pasta **input** no contêiner **adfv2branch**.
    3. Carregue o arquivo **input.txt** no contêiner.

## <a name="create-email-workflow-endpoints"></a>Criar pontos de extremidade do fluxo de trabalho de email
Para disparar o envio de um email desde o pipeline, use os [Aplicativos Lógicos](../logic-apps/logic-apps-overview.md) para definir o fluxo de trabalho. Para obter detalhes sobre como criar um fluxo de trabalho de Aplicativos Lógicos, consulte [Como criar um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

### <a name="success-email-workflow"></a>Fluxo de trabalho de email de êxito
Criar um fluxo de trabalho dos Aplicativos Lógicos chamado `CopySuccessEmail`. Defina o gatilho do fluxo de trabalho como `When an HTTP request is received` e adicione uma ação de `Office 365 Outlook – Send an email`.

![Fluxo de trabalho de email de êxito](media/tutorial-control-flow-portal/success-email-workflow.png)

Para o gatilho de solicitação, preencha o `Request Body JSON Schema` com o JSON a seguir:

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```

A Solicitação no Designer do Aplicativo Lógico deve se parecer com a imagem a seguir:

![Designer de Aplicativo Lógico – solicitação](media/tutorial-control-flow-portal/logic-app-designer-request.png)

Para a ação **Enviar Email**, personalize o modo como você deseja formatar o email, utilizando as propriedades passadas no esquema JSON do corpo da solicitação. Veja um exemplo:

![Designer de Aplicativo Lógico – ação de enviar email](media/tutorial-control-flow-portal/send-email-action-2.png)

Salve o fluxo de trabalho. Anote a URL da solicitação HTTP Post para o fluxo de trabalho de email de êxito:

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

### <a name="fail-email-workflow"></a>Fluxo de trabalho de email de falha
Siga as mesmas etapas para criar outro fluxo de trabalho de Aplicativos Lógicos de **CopyFailEmail**. No gatilho de solicitação, o `Request Body JSON schema` é o mesmo. Altere o formato do seu email como o `Subject` para ajustá-lo, tornando-o um email de falha. Veja um exemplo:

![Designer de Aplicativo Lógico – fluxo de trabalho de email de falha](media/tutorial-control-flow-portal/fail-email-workflow-2.png)

Salve o fluxo de trabalho. Anote a URL da solicitação HTTP Post para o fluxo de trabalho de email de falha:

```
//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

Agora você deve ter duas URL de fluxo de trabalho:

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="create-a-data-factory"></a>Criar uma data factory

1. Iniciar o navegador da Web **Microsoft Edge** ou **Google Chrome**. Atualmente, a interface do usuário do Data Factory tem suporte apenas nos navegadores da Web Microsoft Edge e Google Chrome.
1. No menu à esquerda, selecione **Criar um recurso** > **Dados + Análise** > **Data Factory**:

   ![Seleção de Data Factory no painel "Novo"](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na página **Novo data factory**, insira **ADFTutorialDataFactory** no campo **nome**.

     ![Página de novo data factory](./media/tutorial-control-flow-portal/new-azure-data-factory.png)

   O nome do Azure Data Factory deve ser **globalmente exclusivo**. Se você receber o seguinte erro, altere o nome de data factory (por exemplo, yournameADFTutorialDataFactory) e tente criar novamente. Confira o artigo [Data Factory - regras de nomenclatura](naming-rules.md) para ver as regras de nomenclatura para artefatos do Data Factory.

   *O nome do data factory “ADFTutorialDataFactory” não está disponível.*

3. Selecione a **assinatura** do Azure na qual você deseja criar o data factory.
4. Para o **Grupo de Recursos**, execute uma das seguintes etapas:

      - Selecione **Usar existente** e selecione um grupo de recursos existente na lista suspensa.
      - Selecione **Criar novo** e insira o nome de um grupo de recursos.   
         
        Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/management/overview.md).  
4. Selecione **V2** para a **versão**.
5. Selecione o **local** do data factory. Apenas os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados (Armazenamento do Azure, Banco de Dados SQL do Azure, etc.) e serviços de computação (HDInsight, etc.) usados pelo data factory podem estar em outras regiões.
6. Selecione **Fixar no painel**.     
7. Clique em **Criar**.      
8. No painel, você deve ver o seguinte bloco com status: **Implantando data factory**.

    ![implantando bloco data factory](media/tutorial-control-flow-portal/deploying-data-factory.png)
9. Após a criação, a página do **Data Factory** será exibida conforme mostrado na imagem.

   ![Página inicial do data factory](./media/tutorial-control-flow-portal/data-factory-home-page.png)
10. Clique no bloco **Criar e Monitorar** para iniciar a interface do usuário do Azure Data Factory em uma guia separada.


## <a name="create-a-pipeline"></a>Criar um pipeline
Nesta etapa, você pode criar um pipeline com uma atividade de Cópia e duas atividades da Web. Você pode usar os recursos a seguir para criar o pipeline:

- Parâmetros para o pipeline acessado por conjuntos de dados.
- Atividade de Web para invocar os fluxos de trabalho de aplicativos lógicos para enviar emails de êxito/falha.
- Conexão a uma atividade com outra atividade (no caso de sucesso e falha)
- Usando a saída de uma atividade como uma entrada para a atividade subsequente

1. Na página **introdução** da interface do usuário do Data Factory, clique no bloco **Criar pipeline**.  

   ![Página Introdução](./media/tutorial-control-flow-portal/get-started-page.png)
3. Na janela de propriedades do pipeline, alterne para a guia **Parâmetros** e use o botão **Novo** para adicionar o três parâmetros a seguir do tipo String: sourceBlobContainer, sinkBlobContainer e receptor.

    - **sourceBlobContainer** – parâmetro no pipeline consumido pelo conjunto de dados de blob de origem.
    - **sinkBlobContainer** – parâmetro no pipeline consumido pelo conjunto de dados de blob de coletor
    - **receiver** – este parâmetro é usado pelas duas atividades Web no pipeline que enviam emails de êxito ou falha para o receptor cujos endereços de email são especificados por esse parâmetro.

   ![Menu do novo pipeline](./media/tutorial-control-flow-portal/pipeline-parameters.png)
4. Na caixa de ferramentas **Atividades**, expanda **Fluxo de Dados** e arraste e solte a atividade **Cópia** para a superfície do designer de pipeline.

   ![Atividade de cópia arrastar e soltar](./media/tutorial-control-flow-portal/drag-drop-copy-activity.png)
5. Na janela **Propriedades** da atividade de **Cópia**, na parte inferior, alterne para a guia **Origem** guia e, em seguida, clique em **+ Novo**. Você cria um conjunto de dados de origem para a atividade de cópia nesta etapa.

   ![Captura de tela que mostra como criar um conjunto de dados de origem para a atividade de cópia.](./media/tutorial-control-flow-portal/new-source-dataset-button.png)
6. Na janela **Novo serviço vinculado**, selecione **Armazenamento de Blobs do Azure** e clique em **Continuar**.

   ![Selecionar Armazenamento de Blobs do Azure](./media/tutorial-control-flow-portal/select-azure-blob-storage.png)
7. Você verá uma nova **guia** intitulada **AzureBlob1**. Altere o nome do conjunto de dados para **SourceBlobDataset**.

   ![Configurações gerais do conjunto de dados](./media/tutorial-control-flow-portal/dataset-general-page.png)
8. Alterne para a guia **Conexão** na janela **Propriedades** e clique em Novo para o **Serviço vinculado**. Você cria um serviço vinculado para vincular sua conta do Armazenamento do Azure ao data factory nesta etapa.

   ![Conexão de conjunto de dados - novo serviço vinculado](./media/tutorial-control-flow-portal/dataset-connection-new-button.png)
9. Na janela **Novo Serviço Vinculado**, execute estas etapas:

    1. Insira **AzureStorageLinkedService** como o **Nome**.
    2. Selecione sua conta de armazenamento do Azure cmoo o **Nome da conta de armazenamento**.
    3. Clique em **Save** (Salvar).

   ![Novo serviço vinculado de Armazenamento do Azure](./media/tutorial-control-flow-portal/new-azure-storage-linked-service.png)
12. Insira `@pipeline().parameters.sourceBlobContainer` como a pasta e `emp.txt` como o nome do arquivo. Você pode usar o parâmetro de pipeline sourceBlobContainer para definir o caminho da pasta para o conjunto de dados.

    ![Configurações do conjunto de dados de origem](./media/tutorial-control-flow-portal/source-dataset-settings.png)

13. Alterne para a guia **pipeline** guia (ou) clique no pipeline na exibição em árvore. Confirme se **SourceBlobDataset** está selecionado para **Conjunto de Dados de Origem**.
      
   ![Conjunto de dados de origem](./media/tutorial-control-flow-portal/pipeline-source-dataset-selected.png)

13. Na janela de propriedades, alterne para a guia **Coletor** e, em seguida, clique em **+ Novo** para **Conjunto de Dados do Coletor**. Você cria um conjunto de dados do coletor para a atividade de cópia nesta etapa de forma semelhante a como você criou o conjunto de dados de origem.

    ![Botão Novo conjunto de dados do coletor](./media/tutorial-control-flow-portal/new-sink-dataset-button.png)
14. Na janela **Novo serviço vinculado**, selecione **Armazenamento de Blobs do Azure** e clique em **Continuar**.
15. Na página de configurações **Geral** do conjunto de dados, insira **SinkBlobDataset** como **Nome**.
16. Alterne para a guia **Conexão** e siga estas etapas:

    1. Selecione **AzureStorageLinkedService** como **LinkedService**.
    2. Insira `@pipeline().parameters.sinkBlobContainer` como a pasta.
    1. Insira `@CONCAT(pipeline().RunId, '.txt')` como o nome do arquivo. A expressão usa a ID da execução do pipeline atual como o nome do arquivo. Para obter a lista de expressões e variáveis do sistema com suporte, consulte [Variáveis de sistema](control-flow-system-variables.md) e [Linguagem de expressão](control-flow-expression-language-functions.md).

        ![Configurações do conjunto de dados do coletor](./media/tutorial-control-flow-portal/sink-dataset-settings.png)
17. Alterne para a guia **pipeline** na parte superior. Expanda **Geral** na caixa de ferramentas **Atividades** e arraste e solte uma atividade **Web** para a superfície do designer do pipeline. Defina o nome da atividade como **SendSuccessEmailActivity**. A atividade da Web permite uma chamada para qualquer ponto de extremidade REST. Para obter mais informações sobre a atividade, consulte o artigo [Atividade da Web](control-flow-web-activity.md). Esse pipeline usa uma atividade de Web para chamar o fluxo de trabalho de email de Aplicativos Lógicos.

    ![Arraste e solte a primeira atividade da Web](./media/tutorial-control-flow-portal/success-web-activity-general.png)
18. Alterne para a guia **Configurações** da guia **Geral** e siga as etapas a seguir:
    1. Como a **URL**, especifique a URL para o fluxo de trabalho de aplicativos lógicos que envia o email de êxito.  
    2. Selecione **POST** para **Método**.
    3. Clique no link **+ Adicionar cabeçalho** na seção **Cabeçalhos**.
    4. Adicione um cabeçalho **Content-Type** e defina-a como **application/json**.
    5. Especifique o JSON a seguir para **Corpo**.

        ```json
        {
            "message": "@{activity('Copy1').output.dataWritten}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
        }
        ```
        O corpo da mensagem contém as seguintes propriedades:

       - Mensagem – passagem do valor de `@{activity('Copy1').output.dataWritten`. Acessa uma propriedade da atividade de cópia anterior e passa o valor de dataWritten. Em caso de falha, passe a saída de erro em vez de `@{activity('CopyBlobtoBlob').error.message`.
       - Nome do Data Factory – passagem do valor de `@{pipeline().DataFactory}` Essa é uma variável de sistema, permitindo que você acesse o nome do data factory correspondente. Para obter uma lista das variáveis de sistema, consulte o artigo [Variáveis de Sistema](control-flow-system-variables.md).
       - Nome do Pipeline – passagem do valor de `@{pipeline().Pipeline}`. Essa também é uma variável de sistema, permitindo que você acesse o nome do pipeline correspondente.
       - Destinatário – passagem do valor de "\@pipeline().parameters.receiver"). Acessando os parâmetros de pipeline.

         ![Configurações para a primeira atividade da Web](./media/tutorial-control-flow-portal/web-activity1-settings.png)         
19. Conecte a atividade **Cópia** à atividade da **Web** ao arrastar o botão verde ao lado da atividade de Cópia e soltar na atividade de Web.

    ![Conectar a atividade Cópia com a primeira atividade da Web](./media/tutorial-control-flow-portal/connect-copy-web-activity1.png)
20. Arraste e solte outra atividade da **Web** da caixa de ferramentas Atividades para a superfície do designer do pipeline e defina o **nome** como **SendFailureEmailActivity**.

    ![Nome da segunda atividade da Web](./media/tutorial-control-flow-portal/web-activity2-name.png)
21. Alterne para a guia **Configurações** e siga estas etapas:

    1. Como a **URL**, especifique a URL para o fluxo de trabalho de aplicativos lógicos que envia o email de falha.  
    2. Selecione **POST** para **Método**.
    3. Clique no link **+ Adicionar cabeçalho** na seção **Cabeçalhos**.
    4. Adicione um cabeçalho **Content-Type** e defina-a como **application/json**.
    5. Especifique o JSON a seguir para **Corpo**.

        ```json
        {
            "message": "@{activity('Copy1').error.message}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
        }
        ```

        ![Configurações para a segunda atividade da Web](./media/tutorial-control-flow-portal/web-activity2-settings.png)         
22. Selecione a atividade **Cópia** no designer do pipeline e clique no botão **+->** e, em seguida, selecione **Erro**.  

    ![Captura de tela que mostra como selecionar "Erro" na atividade de cópia no designer do pipeline.](./media/tutorial-control-flow-portal/select-copy-failure-link.png)
23. Arraste o botão **vermelho** próximo à atividade Cópia para a segunda atividade Web **SendFailureEmailActivity**. Você pode mover as atividades de forma que o pipeline se pareça com a imagem a seguir:

    ![Pipeline completo com todas as atividades](./media/tutorial-control-flow-portal/full-pipeline.png)
24. Para validar o pipeline, clique em **Validar** na barra de ferramentas. Feche a janela **Saída da Validação do Pipeline** clicando no botão **>>** .

    ![Validar o pipeline](./media/tutorial-control-flow-portal/validate-pipeline.png)
24. Para publicar as entidades (conjuntos de dados, pipelines etc.) no serviço Data Factory, selecione **Publicar tudo**. Aguarde até que você veja a mensagem **Publicado com êxito**.

    ![Publicar](./media/tutorial-control-flow-portal/publish-button.png)

## <a name="trigger-a-pipeline-run-that-succeeds"></a>Disparar uma execução do pipeline com êxito
1. Para **disparar** uma execução de pipeline, clique em **Disparar** na barra de ferramentas e em **Disparar Agora**.

    ![Disparar uma execução de pipeline](./media/tutorial-control-flow-portal/trigger-now-menu.png)
2. Na janela **Execução de Pipeline**, siga estas etapas:

    1. Insira **adftutorial/adfv2branch/input** no parâmetro **sourceBlobContainer**.
    2. Insira **adfv2branch/adftutorial/saída** como o parâmetro **sinkBlobContainer**.
    3. Insira um **endereço de email** do **receptor**.
    4. Clique em **Concluir**

        ![Parâmetros de execução do pipeline](./media/tutorial-control-flow-portal/pipeline-run-parameters.png)

## <a name="monitor-the-successful-pipeline-run"></a>Monitorar a execução do pipeline com êxito

1. Para monitorar a execução do pipeline, alterne para a guia **Monitorar** à esquerda. Você verá a execução do pipeline disparada manualmente por você. Use o botão **Atualizar** para atualizar a lista.

    ![Execução do pipeline bem-sucedida](./media/tutorial-control-flow-portal/monitor-success-pipeline-run.png)
2. Para **exibir execuções de atividade** associadas a esta execução de pipeline, clique no primeiro link na coluna **Ações**. Você pode alternar para o modo de exibição anterior clicando em **Pipelines** na parte superior. Use o botão **Atualizar** para atualizar a lista.

    ![Captura de tela que mostra como ver a lista de execuções de atividade.](./media/tutorial-control-flow-portal/activity-runs-success.png)

## <a name="trigger-a-pipeline-run-that-fails"></a>Gatilho de uma execução do pipeline com falha
1. Alterne para a guia **Editar** à esquerda.
2. Para **disparar** uma execução de pipeline, clique em **Disparar** na barra de ferramentas e em **Disparar Agora**.
3. Na janela **Execução de Pipeline**, siga estas etapas:

    1. Insira **adftutorial/dummy/inputt** no parâmetro **sourceBlobContainer**. Certifique-se de que a pasta fictícia não existe no contêiner adftutorial.
    2. Insira **adftutorial/dummy/output** no parâmetro **sinkBlobContainer**.
    3. Insira um **endereço de email** do **receptor**.
    4. Clique em **Concluir**.

## <a name="monitor-the-failed-pipeline-run"></a>Monitorar a execução do pipeline com falha

1. Para monitorar a execução do pipeline, alterne para a guia **Monitorar** à esquerda. Você verá a execução do pipeline disparada manualmente por você. Use o botão **Atualizar** para atualizar a lista.

    ![Falha na execução do pipeline](./media/tutorial-control-flow-portal/monitor-failure-pipeline-run.png)
2. Clique no link **Erro** para a execução do pipeline para ver detalhes sobre o erro.

    ![Erro de pipeline](./media/tutorial-control-flow-portal/pipeline-error-message.png)
2. Para **exibir execuções de atividade** associadas a esta execução de pipeline, clique no primeiro link na coluna **Ações**. Use o botão **Atualizar** para atualizar a lista. Observe que a atividade Cópia no pipeline falhou. A atividade da Web foi bem-sucedida ao enviar o email de falha para o destinatário especificado.

    ![Execuções de atividade](./media/tutorial-control-flow-portal/activity-runs-failure.png)
4. Clique no link **Erro** na coluna **Ações** para ver os detalhes sobre o erro.

    ![Erro de execução da atividade](./media/tutorial-control-flow-portal/activity-run-error.png)

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você realizará os seguintes procedimentos:

> [!div class="checklist"]
> * Criar um data factory.
> * Criar um serviço vinculado do Armazenamento do Azure.
> * Criar um conjunto de dados do Blob do Azure
> * Criar um pipeline que contém uma atividade de cópia e uma atividade da Web
> * Enviar saídas de atividades para as atividades subsequentes
> * Utilizar passagem de parâmetros e variáveis do sistema
> * Iniciar uma execução de pipeline
> * Monitorar as execuções de pipeline e de atividade

Agora você pode seguir para a seção Conceitos para obter mais informações sobre o Azure Data Factory.
> [!div class="nextstepaction"]
>[Pipelines e atividades](concepts-pipelines-activities.md)

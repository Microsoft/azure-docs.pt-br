---
title: Carregar dados do Office 365 usando o Azure Data Factory | Microsoft Docs
description: Usar o Azure Data Factory para copiar dados do Office 365
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: jingwang
ms.openlocfilehash: fe3a3b673f6512856f3640b3e103db8623570a88
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60547787"
---
# <a name="load-data-from-office-365-by-using-azure-data-factory"></a>Carregar dados do Office 365 usando o Azure Data Factory

Este artigo mostra como usar o Data Factory para _carregar dados do Office 365 no Armazenamento de Blobs do Azure_. Você pode seguir etapas semelhantes para copiar dados para o Azure Data Lake Gen1 ou Gen2. Consulte o [artigo de Conector do Office 365](connector-office-365.md) sobre como copiar dados do Office 365 em geral.

## <a name="create-a-data-factory"></a>Criar uma data factory

1. No menu à esquerda, selecione **Criar um recurso** > **Dados + Análise** > **Data Factory**: 
   
   ![Seleção de Data Factory no painel "Novo"](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na página **Novo data factory**, forneça os valores para os campos que estão mostrados na imagem a seguir:
      
   ![Página de novo data factory](./media/load-office-365-data/new-azure-data-factory.png)
 
    * **Nome**: Insira um nome globalmente exclusivo para o Azure Data Factory. Se você receber o erro "O nome do Data factory \"LoadFromOffice365Demo\" não está disponível," digite um nome diferente para o data factory. Por exemplo, você pode usar o nome _**seunome**_**LoadFromOffice365Demo**. Tente criar o data factory novamente. Para ver as regras de nomenclatura de artefatos do Data Factory, confira [Regras de nomenclatura do Data Factory](naming-rules.md).
    * **Assinatura**: Selecione a assinatura do Azure para criar o Data Factory. 
    * **Grupo de recursos**: Selecione um grupo de recursos existente na lista suspensa ou selecione a opção **Criar novo** e insira o nome de um grupo de recursos. Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Versão**: Selecione **V2**.
    * **Localização**: Selecione o local para o data factory. Somente os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados que são usados pela data factory podem estar em outros locais e regiões. Esses armazenamentos de dados incluem Azure Data Lake Store, Azure Storage, Banco de Dados SQL do Azure e assim por diante.

3. Selecione **Criar**.
4. Após a conclusão da criação, vá para o seu data factory. Você verá a home page **Data Factory** conforme mostrado na imagem a seguir:
   
   ![Página inicial do data factory](./media/load-office-365-data/data-factory-home-page.png)

5. Selecione o bloco **Autor & Monitor** para iniciar o aplicativo de integração de dados em uma guia separada.

## <a name="create-a-pipeline"></a>Criar um pipeline

1. Na página "Introdução", selecione **Criar pipeline**.
 
    ![Criar um pipeline](./media/load-office-365-data/create-pipeline-entry.png)

2. Na **guia Geral** do pipeline, insira "CopyPipeline" para o **Nome** do pipeline.

3. Na caixa de ferramentas de Atividades > categoria Mover e Transformar > arraste e solte **Copiar atividade** da caixa de ferramentas para a superfície do designer de pipeline. Especifique "CopyFromOffice365ToBlob" como nome da atividade.

### <a name="configure-source"></a>Configurar fonte

1. Vá para o pipeline > **guia Origem**, clique em **+New** para criar um conjunto de dados de origem. 

2. Na janela Novo Conjunto de dados, selecione **Office 365** e depois selecione **Concluir**.

    ![Novo conjunto de dados do Office 365](./media/load-office-365-data/new-office-365-dataset.png)
 
3. Você vê uma nova guia aberta para o conjunto de dados do Office 365. Na **guia Geral** na parte inferior da janela Propriedades, insira "SourceOffice365Dataset" para o Nome.

    ![Configurar o conjunto de dados do Office 365 – Guia Geral](./media/load-office-365-data/config-office-365-dataset-general.png)
 
4. Vá para a **guia Conexão** na janela Propriedades. Clique em **+ Novo**, ao lado da caixa de texto Serviço vinculado.
 
    ![Configurar conjunto de dados do Office 365 – Conexão](./media/load-office-365-data/config-office-365-dataset-connection.png)

5. Na janela Novo serviço vinculado, insira "Office365LinkedService" como nome, insira a ID da entidade de serviço e a chave dessa entidade e, depois, selecione Salvar para implantar o serviço vinculado.

    ![Novo serviço vinculado do Office 365](./media/load-office-365-data/new-office-365-linked-service.png)
 
6. Depois que o serviço vinculado for criado, você retornará para as configurações do conjunto de dados. Ao lado de "Tabela", escolha a seta para baixo para expandir a lista de conjuntos de dados do Office 365 disponíveis e escolha "BasicDataSet_v0.Contact_v0" na lista suspensa:

    ![Configurar conjunto de dados do Office 365 – Tabela](./media/load-office-365-data/config-office-365-dataset-table.png)
 
7. Vá para a **guia Esquema** da janela Propriedades e selecione **Importar Esquema**.  Observe que os valores de exemplo e de esquema do conjunto de dados de Contato são exibidos.

    ![Configurar conjunto de dados do Office 365 – Esquema](./media/load-office-365-data/config-office-365-dataset-schema.png)

8. Agora, volte para o pipeline > guia Origem, confirme se SourceBlobDataset está selecionado.
 
### <a name="configure-sink"></a>Configurar coletor

1. Vá para o pipeline > **guia Coletor** e selecione **+ Novo** para criar um conjunto de dados do coletor.
 
2. Na janela Novo conjunto de dados, observe que apenas os destinos compatíveis são selecionados ao copiar do Office 365. Selecione **Armazenamento de Blobs do Azure** e depois selecione **Concluir**.  Neste tutorial, você pode copiar dados do Office 365 em um Armazenamento de Blobs do Azure.

    ![Novo conjunto de dados de blob](./media/load-office-365-data/new-blob-dataset.png)

4. Na **guia Geral** da janela Propriedades, em Nome, insira "OutputBlobDataset".

5. Vá para a **guia Conexão** na janela Propriedades. Selecione **+ Novo** ao lado da caixa de texto Serviço vinculado.

    ![Configurar conjunto de dados de Blob – Conexão](./media/load-office-365-data/config-blob-dataset-connection.png) 

6. Na janela Novo serviço vinculado, insira "AzureStorageLinkedService" como nome, selecione "entidade de serviço" na lista suspensa dos métodos de autenticação, preencha o Ponto de extremidade de serviço, a ID de entidade de serviço de locatário e a Chave da entidade de serviço e, em seguida, selecione Salvar para implantar o serviço vinculado.  Consulte [aqui](connector-azure-blob-storage.md#service-principal-authentication) para saber como configurar a autenticação de entidade de serviço para o Armazenamento de Blobs do Azure.

    ![Novo serviço vinculado de Blob](./media/load-office-365-data/new-blob-linked-service.png)

7. Depois que o serviço vinculado for criado, você retornará para as configurações do conjunto de dados. Ao lado do caminho do arquivo, selecione **Procurar** para escolher a pasta de saída em que os dados do Office 365 serão extraídos.  Em "Configurações de formato de arquivo", ao lado de Formato de arquivo, escolha "**Formato JSON**" e, ao lado de Padrão de arquivo, escolha "**Conjunto de objetos**".

    ![Configurar conjunto de dados de Blob – Caminho e formato](./media/load-office-365-data/config-blob-dataset-path-and-format.png) 

8. Retorne ao pipeline > guia Coletor, confirme se OutputBlobDataset está selecionado.

## <a name="validate-the-pipeline"></a>Validar o pipeline

Selecione **Validar** na barra de ferramentas para validar as configurações de pipeline.

Você também pode ver o código JSON associado ao pipeline clicando em Código no canto superior direito.

## <a name="publish-the-pipeline"></a>Publicar o pipeline

Na barra de ferramentas superior, selecione **Publicar tudo**. Esta ação publica as entidades (conjuntos de dados e pipelines) criadas por você anteriormente no Data Factory.

![Publicar alterações](./media/load-office-365-data/publish-changes.png) 

## <a name="trigger-the-pipeline-manually"></a>Disparar o pipeline manualmente

Selecione **Gatilho** na barra de ferramentas e selecione **Disparar Agora**. Na página Execução de Pipeline, selecione **Concluir**. 

## <a name="monitor-the-pipeline"></a>Monitorar o Pipeline

Vá para a **guia Monitor** à esquerda. Você verá uma execução do pipeline que é disparada por um gatilho manual. Você pode usar os links na **coluna Ações** para exibir detalhes da atividade e executar o pipeline novamente.

![Monitorar o pipeline](./media/load-office-365-data/pipeline-monitoring.png) 

Para ver a atividade em execução associada à execução do pipeline, selecione o link **Exibir atividades em execução** na coluna Ações. Neste exemplo, há apenas uma atividade, então você vê apenas uma entrada na lista. Para obter detalhes sobre a operação de cópia, selecione o link **Detalhes** (ícone de óculos) na coluna Ações.

![Monitorar a atividade](./media/load-office-365-data/activity-monitoring.png) 

Se esta for a primeira vez que você estiver solicitando dados para este contexto (uma combinação de qual tabela de dados está sendo acessada, em qual conta de destino os dados estão sendo carregados e qual identidade de usuário está fazendo a solicitação de acesso a dados), você verá o status da atividade de cópia como "**Em andamento**", e somente quando você clicar no link "Detalhes", em Ações, você verá o status como "**RequestingConsent**".  Um membro do grupo de aprovadores de acesso a dados precisa aprovar a solicitação no Privileged Access Management antes que seja possível prosseguir com a extração de dados.

_Status como solicitando consentimento:_
![Detalhes de execução de atividade – solicitar consentimento](./media/load-office-365-data/activity-details-request-consent.png) 

_Status como extraindo dados:_

![Detalhes da execução de atividade – extrair dados](./media/load-office-365-data/activity-details-extract-data.png) 

Depois que o consentimento for fornecido, a extração de dados continuará e, depois de algum tempo, a execução do pipeline será mostrada como concluída.

![Monitorar pipeline – bem-sucedido](./media/load-office-365-data/pipeline-monitoring-succeeded.png) 

Agora vá para o Armazenamento de Blobs do Azure de destino e verifique se os dados do Office 365 foram extraídos no formato JSON.

## <a name="next-steps"></a>Próximas etapas

Ir para o seguinte artigo para saber mais sobre o suporte do SQL Data Warehouse do Azure: 

> [!div class="nextstepaction"]
>[Conector do Office 365](connector-office-365.md)
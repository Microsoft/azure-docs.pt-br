---
title: 'Tutorial: Criar um pipeline usando o Assistente de Cópia | Microsoft Docs'
description: Neste tutorial, você cria um pipeline do Azure Data Factory com uma Atividade de Cópia usando o Assistente de Cópia com suporte do Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: b87afb8e-53b7-4e1b-905b-0343dd096198
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0d67e18182d44dc640c75d982ccb40f1d22f8b41
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836605"
---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-data-factory-copy-wizard"></a>Tutorial: Criar um pipeline com a Atividade de Cópia usando o Assistente de Cópia do Data Factory
> [!div class="op_single_selector"]
> * [Visão geral e pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Modelo do Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [API do .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte o [tutorial de atividade de cópia](../quickstart-create-data-factory-dot-net.md). 


Este tutorial mostra como usar o **assistente de cópia** para copiar dados de um armazenamento de blobs do Azure para um Banco de Dados SQL do Azure. 

O **assistente de cópia** permite que você crie rapidamente um pipeline de dados que copia dados de um armazenamento de dados de origem com suporte para um armazenamento de dados de destino com suporte. Portanto, recomendamos que você use o assistente como uma primeira etapa para criar um pipeline de exemplo no cenário de movimentação de dados. Para obter uma lista de armazenamentos de dados com suporte como origens e destinos, consulte [Armazenamentos de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats).  

Este tutorial mostra como criar um Azure Data Factory, iniciar o Assistente de Cópia, seguir uma série de etapas para fornecer detalhes sobre seu cenário de ingestão/movimentação de dados. Quando você concluir as etapas no assistente, ele criará um pipeline com Atividade de Cópia a fim de copiar dados de um armazenamento de blobs do Azure para um banco de dados SQL do Azure automaticamente. Para saber mais sobre a atividade de cópia, confira [Atividades de movimentação de dados](data-factory-data-movement-activities.md).

## <a name="prerequisites"></a>Pré-requisitos
Conclua os pré-requisitos listados no artigo [Visão geral do tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) antes de executar este tutorial.

## <a name="create-data-factory"></a>Criar um data factory
Nesta etapa, você usa o Portal do Azure para criar um data factory do Azure denominado **ADFTutorialDataFactory**.

1. Faça logon no [portal do Azure](https://portal.azure.com).
2. Clique em **Criar um recurso** no canto superior esquerdo, clique em **Dados + análise** e clique em **Data Factory**. 
   
   ![Novo -> DataFactory](./media/data-factory-copy-data-wizard-tutorial/new-data-factory-menu.png)
2. Na folha **Nova data factory** :
   
   1. Digite **ADFTutorialDataFactory** como **nome**.
       O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber o seguinte erro, `Data factory name “ADFTutorialDataFactory” is not available`, altere o nome do data factory (por exemplo, seunomeADFTutorialDataFactoryDDMMAAAA) e tente criá-lo novamente. Veja o tópico [Data Factory - regras de nomenclatura](data-factory-naming-rules.md) para ver as regras de nomenclatura para artefatos do Data Factory.  
      
       ![Nome da data factory indisponível](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-not-available.png)    
   2. Selecione sua **assinatura**do Azure.
   3. Em relação ao Grupo de Recursos, execute uma das seguintes etapas: 
      
      - Selecione **Usar existente** para selecionar um grupo de recursos existente.
      - Selecione **Criar novo** e insira um nome para um grupo de recursos.
          
        Algumas das etapas deste tutorial pressupõem que você esteja usando o nome: **ADFTutorialResourceGroup** para o grupo de recursos. Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../../azure-resource-manager/resource-group-overview.md).
   4. Selecione um **local** para o data factory.
   5. Marque a caixa de seleção **Fixar no painel** na parte inferior da folha.  
   6. Clique em **Criar**.
      
       ![Folha Nova data factory](media/data-factory-copy-data-wizard-tutorial/new-data-factory-blade.png)            
3. Depois que a criação for concluída, você verá a folha **Data Factory**, conforme mostrado na seguinte imagem:
   
   ![Página inicial do data factory](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-home-page.png)

## <a name="launch-copy-wizard"></a>Iniciar o Assistente de cópia
1. Na folha Data Factory, clique em **Copiar dados** para iniciar o **Assistente de cópia**. 
   
   > [!NOTE]
   > Se você vir que o navegador da Web está bloqueado em "Autorizando...", desmarque a configuração **Bloquear cookies de terceiros e dados de site** nas configurações do navegador (ou) mantenha-a habilitada, crie uma exceção para **login.microsoftonline.com** e tente iniciar o assistente novamente.
2. Na página **Propriedades** :
   
   1. Insira **CopyFromBlobToAzureSql** para o **Nome da tarefa**
   2. Insira uma **descrição** (opcional).
   3. Altere a **data/hora de início** e a **data/hora de término** para que a data de término seja definida como a data de hoje e a de início, cinco dias antes.  
   4. Clique em **Próximo**.  
      
      ![Ferramenta de Cópia - página Propriedades](./media/data-factory-copy-data-wizard-tutorial/copy-tool-properties-page.png) 
3. Na página **Repositório de dados de origem**, clique no bloco **Armazenamento de Blobs do Azure**. Use essa página para especificar o repositório de dados de origem para a tarefa de cópia. 
   
    ![Ferramenta de Cópia - página de repositório de dados de origem](./media/data-factory-copy-data-wizard-tutorial/copy-tool-source-data-store-page.png)
4. Na página **Especificar a conta de armazenamento de Blobs do Azure** :
   
   1. Insira **AzureStorageLinkedService** para o **Nome do serviço vinculado**.
   2. Confirme se a opção **De assinaturas do Azure** foi selecionada em **Método de seleção de conta**.
   3. Selecione sua **assinatura**do Azure.  
   4. Selecione uma **Conta de armazenamento do Azure** na lista de contas de armazenamento do Azure disponíveis na assinatura selecionada. Você também pode inserir as configurações de conta de armazenamento manualmente selecionando a opção **Inserir manualmente** para o **Método de seleção de conta** e clicando em **Avançar**. 
      
      ![Ferramenta de Cópia - especifique a conta de armazenamento de Blobs do Azure](./media/data-factory-copy-data-wizard-tutorial/copy-tool-specify-azure-blob-storage-account.png)
5. Na página **Escolher o arquivo de entrada ou a pasta** :
   
   1. Clique duas vezes em **adftutorial** (pasta).
   2. Selecione **emp.txt** e clique em **Escolher**
      
      ![Ferramenta de Cópia - escolha a pasta ou o arquivo de entrada](./media/data-factory-copy-data-wizard-tutorial/copy-tool-choose-input-file-or-folder.png)
6. Na página **Escolha o arquivo ou a pasta de entrada**, clique em **Avançar**. Não selecione **Cópia binária**. 
   
    ![Ferramenta de Cópia - escolha a pasta ou o arquivo de entrada](./media/data-factory-copy-data-wizard-tutorial/chose-input-file-folder.png) 
7. Na página **Configurações de formato de arquivo**, você vê os delimitadores e o esquema é detectado automaticamente pelo assistente na análise do arquivo. Você também pode inserir os delimitadores manualmente para que o assistente de cópia pare de detectar automaticamente ou substitua. Clique em **Avançar** depois de revisar os delimitadores e visualizar os dados. 
   
    ![Ferramenta de Cópia - configurações de formato de arquivo](./media/data-factory-copy-data-wizard-tutorial/copy-tool-file-format-settings.png)  
8. Na página Repositório de dados de destino, selecione **Banco de Dados SQL do Azure** e clique em **Avançar**.
   
    ![Ferramenta de Cópia - escolha o repositório de destino](./media/data-factory-copy-data-wizard-tutorial/choose-destination-store.png)
9. Na página **Especificar o banco de dados SQL do Azure** :
   
   1. Digite **AzureSqlLinkedService** no campo **Nome da conexão**.
   2. Confirme se a opção **De assinaturas do Azure** foi selecionada em **Método de seleção de servidor/banco de dados**.
   3. Selecione sua **assinatura**do Azure.  
   4. Selecione **Nome do servidor** e **Banco de Dados**.
   5. Insira o **Nome de usuário** e a **Senha**.
   6. Clique em **Próximo**.  
      
      ![Ferramenta de Cópia - especifique o banco de dados SQL do Azure](./media/data-factory-copy-data-wizard-tutorial/specify-azure-sql-database.png)
10. Na página **Mapeamento de tabela**, selecione **emp** para o campo **Destino** na lista suspensa e clique em **seta para baixo** (opcional) para ver o esquema e visualizar os dados.
    
     ![Ferramenta de Cópia - mapeamento de tabela](./media/data-factory-copy-data-wizard-tutorial/copy-tool-table-mapping-page.png) 
11. Na página **Mapeamento de esquema**, clique em **Avançar**.
    
    ![Ferramenta de Cópia - mapeamento de esquema](./media/data-factory-copy-data-wizard-tutorial/schema-mapping-page.png)
12. Na página **Configurações de desempenho**, clique em **Avançar**. 
    
    ![Ferramenta de cópia - configurações de desempenho](./media/data-factory-copy-data-wizard-tutorial/performance-settings.png)
13. Examine as informações na página **Resumo** e clique em **Concluir**. Esse assistente cria dois serviços vinculados, dois conjuntos de dados (entrada e saída) e um pipeline no data factory (de onde você iniciou o Assistente de Cópia). 
    
    ![Ferramenta de cópia - configurações de desempenho](./media/data-factory-copy-data-wizard-tutorial/summary-page.png)

## <a name="launch-monitor-and-manage-application"></a>Iniciar o monitor e gerenciar aplicativo
1. Na página **Implantação**, clique no link: `Click here to monitor copy pipeline`.
   
   ![Ferramenta de Cópia - implantação bem-sucedida](./media/data-factory-copy-data-wizard-tutorial/copy-tool-deployment-succeeded.png)  
2. O aplicativo de monitoramento é iniciado em uma guia separada no navegador da Web.   
   
   ![Aplicativo de Monitoramento](./media/data-factory-copy-data-wizard-tutorial/monitoring-app.png)   
3. Para ver o status mais recente das fatias de cada hora, clique no botão **Atualizar** da lista **JANELAS DE ATIVIDADE** na parte inferior. Você vê cinco janelas de atividade para cinco dias entre as horas de início e de término do pipeline. A lista não é atualizada automaticamente e, portanto, talvez seja necessário clicar em Atualizar algumas vezes para poder ver todas as janelas de atividade com status Pronto. 
4. Selecione uma janela de atividade na lista. Consulte os detalhes sobre ela no **Gerenciador da janela de atividade** à direita.

    ![Detalhes da janela Atividade](media/data-factory-copy-data-wizard-tutorial/activity-window-details.png)    

    Observe que as datas 11, 12, 13, 14 e 15 estão na cor verde, que significa que as fatias de saída diária para essas datas já foram produzidas. Você também verá essa codificação de cor no pipeline e o conjunto de dados de saída na exibição de diagrama. Na etapa anterior, observe que duas fatias já foram produzidas, uma está sendo processada e os outras duas estão aguardando processamento (com base na codificação de cores). 

    Para saber mais sobre como usar o aplicativo, confira [Monitorar e gerenciar o pipeline usando o aplicativo de monitoramento](data-factory-monitor-manage-app.md) artigo.

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você usou o armazenamento de blobs do Azure como um armazenamento de dados de origem e um banco de dados SQL do Azure como um armazenamento de dados de destino em uma operação de cópia. A tabela a seguir fornece uma lista de armazenamentos de dados com suporte como origens ou destinos na atividade de cópia: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Para obter detalhes sobre campos/propriedades que você vê no assistente de cópia de um armazenamento de dados, clique no link para o armazenamento de dados na tabela. 
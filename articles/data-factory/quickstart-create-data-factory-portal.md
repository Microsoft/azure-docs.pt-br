---
title: Criar um Azure Data Factory usando o Portal do Azure | Microsoft Docs
description: Crie um Azure Data Factory para copiar dados de um armazenamento de dados de nuvem (Armazenamento de Blobs do Azure) para outro armazenamento de dados de nuvem (Banco de Dados SQL do Azure).
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 09/19/2017
ms.author: jingwang
ms.openlocfilehash: b884d7f08311cc60dc3af500f552d525d23b91e6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-data-factory-using-the-azure-portal"></a>Criar um data factory usando o Portal do Azure
O Azure Data Factory é um serviço de integração de dados baseado em nuvem que permite que você crie fluxos de trabalho controlados por dados na nuvem para orquestrar e automatizar a movimentação e a transformação de dados. Usando o Azure Data Factory, você pode criar e agendar fluxos de trabalho orientados a dados (chamados de pipelines) que podem ingerir dados de repositórios de dados diferentes, processar/transformr os dados usando serviços de computação como o Hadoop do Azure HDInsight, Spark, Azure Data Lake Analytics e Azure Machine Learning e publicar os dados de saída em repositórios de dados como o SQL Data Warehouse do Azure para consumo pelos aplicativos de business intelligence (BI). 

Neste guia de início rápido, você usa o Portal do Azure para criar um data factory. Depois de criar o data factory, você precisa usar o PowerShell, o SDK do .NET, o SDK do Python ou a API REST e, como em outros guias de início rápido, um pipeline de dados que copia dados de um armazenamento de dados de origem para um armazenamento de dados de destino. No momento, você não pode criar pipelines em um data factory usando o Portal do Azure.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-the-azure-portal"></a>Faça logon no Portal do Azure
Faça logon no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-data-factory"></a>Criar uma data factory
Aqui estão as etapas executadas como parte deste guia de início rápido:
1. Clique em **Novo** no menu à esquerda, clique em **Dados + Análise** e clique em **Data Factory**. 
   
   ![Novo -> DataFactory](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)
2. Na folha **Novo data factory**, insira **ADFTutorialDataFactory** como o **nome**. 
      
     ![Folha Nova data factory](./media/quickstart-create-data-factory-portal/new-azure-data-factory.png)
 
   O nome do Azure Data Factory deve ser **globalmente exclusivo**. Se você receber o seguinte erro, altere o nome de data factory (por exemplo, yournameADFTutorialDataFactory) e tente criar novamente. Consulte o tópico [Data Factory - regras de nomenclatura](naming-rules.md) para ver as regras de nomenclatura para artefatos de Data Factory.
  
       `Data factory name “ADFTutorialDataFactory” is not available`
3. Selecione a **assinatura** do Azure na qual você deseja criar o data factory. 
4. Para o **Grupo de Recursos**, execute uma das seguintes etapas:
     
      - Selecione **Usar existente**e selecione um grupo de recursos existente na lista suspensa. 
      - Selecione **Criar novo**e insira o nome de um grupo de recursos.   
         
      Algumas das etapas neste guia de início rápido supõem que você usa o nome **ADFTutorialResourceGroup** para o grupo de recursos. Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
4. Selecione **V2 (Versão Prévia)** para a **versão**.
5. Selecione o **local** do data factory. No momento, você pode criar Data Factories V2 somente na região **Leste dos EUA**. No entanto, os serviços de computação e de armazenamento de dados usados por data factories podem estar em outras regiões. 
6. Selecione **Fixar no painel**.     
7. Clique em **Criar**.
      
      > [!IMPORTANT]
      > Para criar instâncias de Data Factory, você deve ser um membro da função [Colaborador de Data Factory](../active-directory/role-based-access-built-in-roles.md#data-factory-contributor) no nível de assinatura/grupo de recursos.
      > 
      > O nome do data factory pode ser registrado futuramente como um nome DNS e tornar-se publicamente visível.             
3. No painel, você vê o seguinte bloco com status: **Implantando data factory**. 

    ![implantando bloco data factory](media//quickstart-create-data-factory-portal/deploying-data-factory.png)
1. Depois que a criação estiver concluída, você verá a folha **DATA FACTORY** , conforme mostrado na imagem.
   
   ![Página inicial da data factory](./media/quickstart-create-data-factory-portal/data-factory-home-page.png)


## <a name="next-steps"></a>Próximas etapas
O pipeline nessa amostra copia dados de uma localização para outra em um Armazenamento de Blobs do Azure. Percorra os [tutoriais](tutorial-copy-data-dot-net.md) para saber mais sobre o uso do Data Factory em mais cenários. 
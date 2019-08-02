---
title: 'Tutorial do Data Factory: primeiro pipeline de dados | Microsoft Docs'
description: Este tutorial do Azure Data Factory mostra como criar e agendar um data factory que processa os dados usando o script Hive em um cluster Hadoop.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.assetid: 81f36c76-6e78-4d93-a3f2-0317b413f1d0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 552c89cd3294567e8203b69f81c1ac24716a8b1b
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839448"
---
# <a name="tutorial-build-your-first-pipeline-to-transform-data-using-hadoop-cluster"></a>Tutorial: Crie seu primeiro pipeline para transformar dados usando um cluster Hadoop
> [!div class="op_single_selector"]
> * [Visão geral e pré-requisitos](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Modelo do Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
> * [API REST](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Caso esteja usando a versão atual do serviço Data Factory, consulte [Início Rápido: criar um data factory usando o Azure Data Factory](../quickstart-create-data-factory-dot-net.md).

Neste tutorial, você deve criar sua Azure Data Factory com um pipeline de dados. O pipeline transforma dados de entrada, executando o script do Hive em um cluster Azure HDInsight (Hadoop) para gerar dados de saída.  

Este artigo fornece uma visão geral e pré-requisitos para o tutorial. Depois de concluir os pré-requisitos, siga o tutorial usando uma das seguintes ferramentas/SDKs: Visual Studio, PowerShell, modelo do Resource Manager, API REST. Selecione uma das opções na lista suspensa no início (ou) links no final deste artigo para fazer o tutorial usando uma das seguintes opções.    

## <a name="tutorial-overview"></a>Visão geral do tutorial
Neste tutorial, você executa as seguintes etapas:

1. Criar uma **data factory**. Um data factory pode conter um ou mais pipelines de dados que movem e transformam dados. 

    Neste tutorial, você deve criar um pipeline na data factory. 
2. Criar um **pipeline**. Um pipeline pode ter uma ou mais atividades (exemplos: Atividade de Cópia, Atividade de Hive do HDInsight). Este exemplo usa a atividade de Hive do HDInsight que executa um script do Hive em um cluster de Hadoop do HDInsight. Primeiro, o script cria uma tabela que faz referência aos dados brutos de log da Web colocados no armazenamento de blobs do Azure, então, particiona os dados brutos por ano e mês.

    Neste tutorial, o pipeline usa a atividade do Hive para transformar dados executando uma consulta de Hive em um cluster do Hadoop do Azure HDInsight. 
3. Crie **serviços vinculados**. Crie um serviço vinculado para vincular um armazenamento de dados ou um serviço de computação ao data factory. Um armazenamento de dados, como o Armazenamento do Azure, armazena dados de entrada/saída de atividades no pipeline. Um serviço de computação, como o cluster de Hadoop do HDInsight, processa/transforma os dados.

    Neste tutorial, você aprende a criar dois serviços vinculados: **Armazenamento do Microsoft Azure** e **Azure HDInsight**. O serviço vinculado de armazenamento do Azure vincula uma conta de armazenamento do Azure que contém os dados de entrada/saída para a data factory. O serviço vinculado do Azure HDInsight vincula um cluster Azure HDInsight que é usado para transformar dados em data factory. 
3. Criar **conjuntos de dados**de entrada e saída. Um conjunto de dados de entrada representa a entrada de uma atividade no pipeline e um conjunto de dados de saída representa a saída da atividade.

    Neste tutorial, os conjuntos de dados de entrada e saída especificam locais de entrada e saída de dados no armazenamento de blobs do Azure. O serviço vinculado de armazenamento do Azure especifica qual conta de armazenamento do Azure é usada. Um conjunto de dados de entrada especifica o local em que os arquivos de entrada estão localizados e um conjunto de dados de saída especifica o local em que os arquivos de saída são colocados. 


Para obter uma visão geral detalhada do Azure Data Factory, confira o artigo [Introdução à Azure Data Factory](data-factory-introduction.md).
  
Aqui está a **exibição de diagrama** do data factory de exemplo que você compila neste tutorial. **MyFirstPipeline** tem uma atividade de Hive que consome o conjunto de dados **AzureBlobInput** como uma entrada e produz o conjunto de dados **AzureBlobOutput** como uma saída. 

![Exibição de diagrama no tutorial do Data Factory](media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)


Neste tutorial, a pasta **inputdata** do contêiner de blobs do Azure **adfgetstarted** contém um arquivo denominado input.log. Esse arquivo de log tem entradas de três meses: janeiro, fevereiro e março de 2016. Veja as linhas de exemplo para cada mês no arquivo de entrada. 

```
2016-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871 
2016-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2016-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

Quando o arquivo é processado pelo pipeline com a Atividade do Hive do HDInsight, a atividade executa um script do Hive no cluster do HDInsight que particiona dados de entrada por ano e por mês. O script cria três pastas de saída com um arquivo com entradas de cada mês.  

```
adfgetstarted/partitioneddata/year=2016/month=1/000000_0
adfgetstarted/partitioneddata/year=2016/month=2/000000_0
adfgetstarted/partitioneddata/year=2016/month=3/000000_0
```

Das linhas de exemplo mostradas acima, a primeira (com 2016-01-01) é gravada no arquivo 000000_0 na pasta month=1. Da mesma forma, a segunda é gravada no arquivo na pasta do mês = 2 e a terceira é gravada no arquivo na pasta do mês = 3.  

## <a name="prerequisites"></a>Pré-requisitos
Antes de iniciar este tutorial, você deverá ter os seguintes pré-requisitos:

1. **Uma assinatura do Azure** - se você não tiver uma, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Consulte o artigo [Avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/) para ver como você pode obter uma conta de avaliação gratuita.
2. **Armazenamento do Azure** – você usa uma conta de armazenamento do Azure para armazenar os dados neste tutorial. Se você não tiver uma conta de armazenamento do Azure, consulte o artigo [Criar uma conta de armazenamento](../../storage/common/storage-quickstart-create-account.md) . Depois de você ter criado a conta de armazenamento, anote o **nome da conta** e a **chave de acesso**. Consulte [Exibir, copiar e regenerar chaves de acesso de armazenamento](../../storage/common/storage-account-manage.md#access-keys).
3. Faça o download e leia o arquivo de consulta de Hive (**HQL**) localizado em: [https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql](https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql). Essa consulta transforma os dados de entrada para gerar dados de saída. 
4. Faça o download e leia o arquivo de entrada de exemplo (**HQL**) localizado em: [https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log](https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log)
5. Crie um contêiner de blobs denominado **adfgetstarted** em seu armazenamento de blobs do Azure. 
6. Execute o arquivo **partitionweblogs.hql** para a pasta **script** do contêiner **adfgetstarted**. Use ferramentas como o [Gerenciador de Armazenamento do Microsoft Azure](https://storageexplorer.com/). 
7. Carregue o arquivo **input.log** para a pasta **inputdata** do contêiner **adfgetstarted**. 

Depois de concluir os pré-requisitos, selecione uma das seguintes ferramentas/SDKs para fazer o tutorial: 

- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Modelo do Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
- [API REST](data-factory-build-your-first-pipeline-using-rest-api.md)

Visual Studio fornece uma maneira de GUI de criar data factories. Enquanto isso, as opções do PowerShell, do modelo do Resource Manager e a API REST permitem criar data factories com script/programação.

> [!NOTE]
> O pipeline de dados neste tutorial transforma os dados de entrada para gerar dados de saída. Ele não copia dados de um armazenamento de dados de origem para um armazenamento de dados de destino. Para ver um tutorial sobre como copiar dados usando o Azure Data Factory, consulte [Tutorial: Copiar dados do Armazenamento de Blobs para o Banco de Dados SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> É possível encadear duas atividades (executar uma atividade após a outra) definindo o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra atividade. Confira [Agendamento e execução no Data Factory](data-factory-scheduling-and-execution.md) para obter informações detalhadas. 





  

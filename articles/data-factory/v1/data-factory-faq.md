---
title: Azure Data Factory - Perguntas frequentes
description: Perguntas frequentes sobre o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: f33a59105d1be94ccd4fb24ae3d962b36c2efbdd
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839348"
---
# <a name="azure-data-factory---frequently-asked-questions"></a>Azure Data Factory - Perguntas frequentes
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [perguntas frequentes - Data Factory](../frequently-asked-questions.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="general-questions"></a>Perguntas gerais
### <a name="what-is-azure-data-factory"></a>O que é o Data Factory do Azure?
O Data Factory é um serviço de integração de dados baseado em nuvem que **automatiza a movimentação e a transformação dos dados**. Assim como uma fábrica que usa equipamentos para pegar matérias-primas e transformá-las em produtos, o Data Factory orquestra serviços existentes que coletam dados brutos e os transformam em informações para uso imediato.

O Data Factory permite que você crie fluxos de trabalho orientados a dados para mover dados entre armazenamentos de dados local e na nuvem, além de processar/transformar dados usando serviços de computação, como Azure HDInsight e Azure Data Lake Analytics. Depois de criar um pipeline que executa a ação que você precisa, você pode programá-lo para ser executado periodicamente (por hora, diariamente, semanalmente etc).   

Para obter mais informações, consulte [Visão geral e principais conceitos](data-factory-introduction.md).

### <a name="where-can-i-find-pricing-details-for-azure-data-factory"></a>Onde posso encontrar detalhes de preços do Data Factory do Azure?
Ver [página de detalhes de preços do Data Factory][adf-pricing-details] para os detalhes de preços para o Azure Data Factory.  

### <a name="how-do-i-get-started-with-azure-data-factory"></a>Como faço para começar a utilizar o Azure Data Factory?
* Para obter uma visão geral do Azure Data Factory, confira [Introdução ao Azure Data Factory](data-factory-introduction.md).
* Para obter um tutorial sobre como **copiar/mover dados** usando a Atividade de Cópia, consulte [Copiar dados do Armazenamento de Blob do Azure para o Banco de Dados SQL do Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Para obter um tutorial sobre como **transformar dados** usando a atividade Hive do HDInsight. Consulte [Processar dados executando o script do Hive no cluster do Hadoop](data-factory-build-your-first-pipeline.md)

### <a name="what-is-the-data-factorys-region-availability"></a>Qual é a disponibilidade de região do Data Factory?
O Data Factory está disponível no **Oeste dos EUA** e no **Europa Setentrional**. Os serviços de computação e armazenamento utilizados por data factories podem estar em outras regiões. Consulte [Regiões com suporte](data-factory-introduction.md#supported-regions).

### <a name="what-are-the-limits-on-number-of-data-factoriespipelinesactivitiesdatasets"></a>Quais são os limites de número de fábricas/pipelines/atividades/conjuntos de dados?
Veja a seção **Limites do Azure Data Factory** do artigo [Limites, cotas e restrições da assinatura e do serviço do Azure](../../azure-subscription-service-limits.md#data-factory-limits) .

### <a name="what-is-the-authoringdeveloper-experience-with-azure-data-factory-service"></a>O que é a experiência de criação/desenvolvedor com o serviço Azure Data Factory?
Você pode criar fábricas de dados usando um dos SDKs/ferramentas a seguir:

* **Visual Studio** Você pode usar o Visual Studio para criar uma fábrica de dados do Azure. Consulte [Criar seu primeiro pipeline de dados usando o Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) para obter detalhes.
* **Azure PowerShell** Veja [Criar e monitorar o Azure Data Factory usando o Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md) para ver um tutorial/passo a passo da criação de uma fábrica de dados usando o PowerShell. Ver [referência de Cmdlet do Data Factory][adf-powershell-reference] conteúdo na biblioteca do MSDN para obter uma documentação abrangente de cmdlets do Data Factory.
* **Biblioteca de classes .NET** Você pode criar fábricas de dados programaticamente usando o SDK do .NET de Data Factory. Confira [Criar, monitorar e gerenciar fábricas de dados usando o SDK do .NET](data-factory-create-data-factories-programmatically.md) para obter uma explicação sobre a criação de uma fábrica de dados usando o SDK do .NET. Ver [referência da biblioteca de classe do Data Factory][msdn-class-library-reference] para obter uma documentação abrangente do SDK do .NET do Data Factory.
* **REST API** Você também pode usar a API REST exposta pelo serviço do Azure Data Factory para criar e implantar fábricas de dados. Ver [referência de API REST do Data Factory][msdn-rest-api-reference] para obter uma documentação abrangente da API REST do Data Factory.
* **Modelo do Azure Resource Manager** Para obter detalhes, veja [Tutorial: Criar a sua primeira Azure Data Factory usando o modelo do Azure Resource Manager](data-factory-build-your-first-pipeline-using-arm.md).

### <a name="can-i-rename-a-data-factory"></a>Posso renomear um Data Factory?
Nº Como outros recursos do Azure, o nome de uma Data Factory do Azure não pode ser alterado.

### <a name="can-i-move-a-data-factory-from-one-azure-subscription-to-another"></a>Posso mover uma fábrica de dados de uma assinatura do Azure para outra?
Sim. Use o botão **Mover** na folha do data factory, conforme mostrado no diagrama a seguir:

![Mover o Data Factory](media/data-factory-faq/move-data-factory.png)

### <a name="what-are-the-compute-environments-supported-by-data-factory"></a>Quais são os ambientes de computação com suporte do Data Factory?
A tabela a seguir fornece uma lista dos ambientes de computação com suporte do Data Factory e as atividades que podem ser executadas neles.

| Ambiente de computação | activities |
| --- | --- |
| [Cluster HDInsight sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) ou [seu próprio cluster HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) |[DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |
| [Lote do Azure](data-factory-compute-linked-services.md#azure-batch-linked-service) |[DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](data-factory-compute-linked-services.md#azure-machine-learning-linked-service) |[Atividades de Machine Learning: Execução de lote e Recurso de atualização](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](data-factory-compute-linked-services.md#azure-data-lake-analytics-linked-service) |[U-SQL da Análise Data Lake](data-factory-usql-activity.md) |
| [Azure SQL](data-factory-compute-linked-services.md#azure-sql-linked-service), [Azure SQL Data Warehouse](data-factory-compute-linked-services.md#azure-sql-data-warehouse-linked-service), [SQL Server](data-factory-compute-linked-services.md#sql-server-linked-service) |[Procedimento armazenado](data-factory-stored-proc-activity.md) |

### <a name="how-does-azure-data-factory-compare-with-sql-server-integration-services-ssis"></a>Como o Azure Data Factory se compara com o SSIS (SQL Server Integration Services)? 
Veja a apresentação [Azure Data Factory vs. SSIS](https://www.sqlbits.com/Sessions/Event15/Azure_Data_Factory_vs_SSIS) de um dos nossos MVPs (Most Valued Professionals): Reza Rad. Algumas das alterações recentes no Data Factory podem não estar listadas no conjunto de slides. Estamos continuamente adicionando mais recursos ao Azure Data Factory. Estamos continuamente adicionando mais recursos ao Azure Data Factory. Nós incorporaremos essas atualizações à comparação das tecnologias de integração de dados da Microsoft, mais tarde neste ano.   

## <a name="activities---faq"></a>Atividades - Perguntas frequentes
### <a name="what-are-the-different-types-of-activities-you-can-use-in-a-data-factory-pipeline"></a>Quais são os tipos diferentes de atividades que você pode usar em um pipeline do Azure Data Factory?
* [Atividades de Movimentação de Dados](data-factory-data-movement-activities.md) para mover dados.
* [Atividades de Transformação de Dados](data-factory-data-transformation-activities.md) para processar/transformar dados.

### <a name="when-does-an-activity-run"></a>Quando uma atividade é executada?
A configuração **disponibilidade** na tabela de dados de saída determina quando a atividade é executada. Se conjuntos de dados de entrada forem especificados, a atividade verificará se todas as dependências de dados de entrada foram atendidas (ou seja, estado **Pronto** ) antes de começar a ser executada.

## <a name="copy-activity---faq"></a>Atividade de Cópia - Perguntas frequentes
### <a name="is-it-better-to-have-a-pipeline-with-multiple-activities-or-a-separate-pipeline-for-each-activity"></a>É melhor ter um pipeline com várias atividades ou um pipeline separado para cada atividade?
Pipelines devem agrupar atividades relacionadas. Se os conjuntos de dados que os conectam não forem consumidos por nenhuma outra atividade fora do pipeline, você poderá manter as atividades em um pipeline. Desse modo, você não precisaria encadear períodos ativos do pipeline para que eles se alinhem uns com os outros. Além disso, a integridade dos dados nas tabelas internas ao pipeline é melhor preservada ao atualizar o pipeline. A atualização de pipeline interrompe essencialmente todas as atividades no pipeline, remove-as e cria essas atividades novamente. Da perspectiva de criação, também pode ser mais fácil ver o fluxo de dados nas atividades relacionadas em um arquivo JSON para o pipeline.

### <a name="what-are-the-supported-data-stores"></a>Quais são os armazenamentos de dados com suporte?
A Atividade de Cópia no Data Factory copia os dados de um repositório de dados de origem para um repositório de dados de coletor. A Data Factory dá suporte aos repositórios de dados a seguir. Os dados de qualquer origem podem ser gravados em qualquer coletor. Clique em um repositório de dados para saber como copiar dados dentro e fora do repositório.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Os repositórios de dados com * podem estar no local ou no Azure IaaS e exigem a instalação do [Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md) em um computador Azure IaaS/local.

### <a name="what-are-the-supported-file-formats"></a>Quais são os formatos de arquivo com suporte?
[!INCLUDE [data-factory-file-format](../../../includes/data-factory-file-format.md)]

### <a name="where-is-the-copy-operation-performed"></a>Onde a operação de cópia é executada?
Consulte [Movimento de dados globalmente disponível](data-factory-data-movement-activities.md#global) seção para obter detalhes. Em resumo, quando um armazenamento de dados local estiver envolvido, a operação de cópia é executada pelo Gateway de gerenciamento de dados em seu ambiente local. E, quando a movimentação de dados está entre dois armazenamentos de nuvem, a operação de cópia será executada na região mais próxima para o local de coletor na mesma região geográfica.

## <a name="hdinsight-activity---faq"></a>Atividade de HDInsight - Perguntas frequentes
### <a name="what-regions-are-supported-by-hdinsight"></a>O HDInsight dá suporte a quais regiões?
Consulte a seção disponibilidade geográfica no seguinte artigo: ou [detalhes de preços do HDInsight][hdinsight-supported-regions].

### <a name="what-region-is-used-by-an-on-demand-hdinsight-cluster"></a>Qual região é utilizada por um cluster HDInsight sob demanda?
O cluster HDInsight sob demanda é criado na mesma região onde existe o armazenamento especificado para ser utilizado com o cluster existente.    

### <a name="how-to-associate-additional-storage-accounts-to-your-hdinsight-cluster"></a>Como associar contas de armazenamento adicionais ao cluster HDInsight?
Se você estiver usando seu próprio cluster HDInsight (BYOC – traga seu próprio Cluster), confira os tópicos a seguir:

* [Usando um Cluster de HDInsight com Metastores e contas de armazenamento alternativo][hdinsight-alternate-storage]
* [Usar contas de armazenamento adicionais com o Hive do HDInsight][hdinsight-alternate-storage-2]

Se você estiver usando um cluster sob demanda que é criado pelo serviço Data Factory, deverá especificar contas de armazenamento adicionais para o serviço vinculado HDInsight para que o serviço Data Factory possa registrá-los em seu nome. Na definição de JSON para o serviço vinculado sob demanda, use a propriedade **additionalLinkedServiceNames** para especificar contas de armazenamento alternativas, como mostrado no snippet de JSON a seguir:

```JSON
{
    "name": "MyHDInsightOnDemandLinkedService",
    "properties":
    {
        "type": "HDInsightOnDemandLinkedService",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "LinkedService-SampleData",
            "additionalLinkedServiceNames": [ "otherLinkedServiceName1", "otherLinkedServiceName2" ]
        }
    }
}
```
No exemplo acima, otherLinkedServiceName1 e otherLinkedServiceName2 representam serviços vinculados cujas definições contêm as credenciais de que o cluster HDInsight precisa para acessar contas de armazenamento alternativas.

## <a name="slices---faq"></a>Fatias - Perguntas frequentes
### <a name="why-are-my-input-slices-not-in-ready-state"></a>Por que minhas fatias de entrada não estão no estado Pronto?
Um erro comum é não configurar a propriedade **external** como **true** no conjunto de dados de entrada quando os dados de entrada são externos ao data factory (não produzidos pelo data factory).

No exemplo a seguir, você só precisa definir **external** como true no **dataset1**.  

**DataFactory1** Pipeline 1: dataset1 -> activity1 -> dataset2 -> activity2 -> dataset3 Pipeline 2: dataset3-> activity3 -> dataset4

Se tiver outro data factory com um pipeline que use o dataset4 (produzido pelo pipeline 2 no data factory 1), você precisará marcar o dataset4 como um conjunto de dados externo, pois o conjunto de dados é produzido por uma fábrica de dados diferente (DataFactory1, e não DataFactory2).  

**DataFactory2**    
Pipeline 1: dataset4->activity4->dataset5

Se a propriedade external estiver definida corretamente, verifique se os dados de entrada existem no local especificado na definição de conjunto de dados de entrada.

### <a name="how-to-run-a-slice-at-another-time-than-midnight-when-the-slice-is-being-produced-daily"></a>Como executar uma fatia em outro horário que não à meia-noite quando a fatia é produzida diariamente?
Use a propriedade **offset** para especificar a hora em que você deseja que a fatia seja produzida. Confira a seção [Disponibilidade do conjunto de dados](data-factory-create-datasets.md#dataset-availability) para obter detalhes sobre essa propriedade. Aqui está um exemplo rápido:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
Fatias diárias que se iniciam às **6h** , em vez da meia-noite do padrão.     

### <a name="how-can-i-rerun-a-slice"></a>Como executo novamente uma fatia?
Você pode executar novamente uma fatia de uma das seguintes maneiras:

* Use o Aplicativo Monitorar e Gerenciar para executar uma janela de atividade ou fatia novamente. Veja [Executar novamente as janelas de atividades selecionadas](data-factory-monitor-manage-app.md#perform-batch-actions) para obter instruções.   
* Clique em **Executar** na barra de comando na folha **FATIA DE DADOS** para a fatia no portal do Azure.
* Execute **AzDataFactorySliceStatus conjunto** cmdlet com o Status definido como **aguardando** da fatia.   

    ```powershell
    Set-AzDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00"
    ```
  Ver [AzDataFactorySliceStatus conjunto][set-azure-datafactory-slice-status] para obter detalhes sobre o cmdlet.

### <a name="how-long-did-it-take-to-process-a-slice"></a>Quanto tempo levou para processar uma fatia?
Use o Gerenciador de Janela de Atividade no Aplicativo Monitorar e Gerenciar para saber quanto tempo foi necessário para processar uma fatia de dados. Confira [Gerenciador de Janelas de Atividades](data-factory-monitor-manage-app.md#activity-window-explorer) para obter detalhes.

Você também pode fazer o seguinte no portal do Azure:  

1. Clique no bloco **Conjuntos de dados** da folha **DATA FACTORY** da sua fábrica de dados.
2. Clique no conjunto de dados específico na folha **Conjuntos de dados** .
3. Selecione a fatia em que você está interessado na lista **Fatias recentes** na folha **TABELA**.
4. Clique na execução da lista **Execuções de Atividade** na folha **FATIA DE DADOS**.
5. Clique no bloco **Propriedades** da folha **DETALHES DE EXECUÇÃO DA ATIVIDADE**.
6. Você deve ver o campo **DURAÇÃO** com um valor. Esse valor é o tempo necessário para processar a fatia.   

### <a name="how-to-stop-a-running-slice"></a>Como parar uma fatia em execução?
Se você precisar interromper o execução do pipeline, você pode usar [AzDataFactoryPipeline Suspend](/powershell/module/az.datafactory/suspend-azdatafactorypipeline) cmdlet. Atualmente, suspender o pipeline não interrompe as execuções de fatia que estão em andamento. Depois de concluir as execuções em andamento, nenhuma fatia extra é coletada.

Se você realmente desejar parar todas as execuções imediatamente, a única maneira seria excluir o pipeline e criá-lo novamente. Se você optar por excluir o pipeline, não é necessário excluir tabelas e serviços vinculados usados pelo pipeline.

[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: /dotnet/api/microsoft.azure.management.datafactories.models
[msdn-rest-api-reference]: /rest/api/datafactory/

[adf-powershell-reference]: /powershell/module/az.datafactory/
[azure-portal]: https://portal.azure.com
[set-azure-datafactory-slice-status]: /powershell/module/az.datafactory/set-Azdatafactoryslicestatus

[adf-pricing-details]: https://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: https://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: https://blogs.msdn.com/b/cindygross/archive/2014/05/05/use-additional-storage-accounts-with-hdinsight-hive.aspx

---
title: Azure Data Factory - Exemplos
description: Fornece detalhes sobre os exemplos fornecidos com o serviço Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: c0538b90-2695-4c4c-a6c8-82f59111f4ab
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: f7ebc57e09691af1583815ae9672e1afb820145b
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462073"
---
# <a name="azure-data-factory---samples"></a>Azure Data Factory - Exemplos
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [Exemplos do PowerShell no Data Factory](../samples-powershell.md) e [exemplos de código na galeria de exemplos do código do Azure](https://azure.microsoft.com/resources/samples/?service=data-factory).


## <a name="samples-on-github"></a>Exemplos no GitHub
O [repositório GitHub Azure-DataFactory](https://github.com/azure/azure-datafactory) contém vários exemplos que ajudam você a utilizar rapidamente o serviço Azure Data Factory ou modificar os scripts e usá-los no próprio aplicativo. A pasta Samples\JSON contém snippets de código JSON para cenários comuns.

| Amostra | DESCRIÇÃO |
|:--- |:--- |
| [Passo a passo do ADF](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFWalkthrough) |Este exemplo fornece uma explicação de ponta a ponta para o processamento de arquivos de log usando o Azure Data Factory para transformar dados de arquivos de log em informações. <br/><br/>Neste passo a passo, o pipeline do Data Factory coleta logs de exemplo, processa e enriquece os dados dos logs com dados de referência, além de transformar os dados para avaliar a eficiência de uma campanha de marketing lançada recentemente. |
| [Exemplos JSON](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSON) |Este exemplo fornece exemplos JSON para cenários comuns. |
| [Exemplo de HTTP Data Downloader](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HttpDataDownloaderSample) |Este exemplo apresenta o download de dados de um ponto de extremidade HTTP no Armazenamento de Blobs do Azure usando a atividade personalizada do .NET. |
| [Exemplo de atividade AppDomain Dot Net](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) |Este exemplo permite que você crie uma atividade personalizada do .NET que não esteja restrita a versões de assembly usadas pelo iniciador do ADF (por exemplo, WindowsAzure.Storage v4.3.0, Newtonsoft.Json v6.0.x etc.). |
| [Executar script R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample) |Este exemplo inclui a atividade personalizada do Data Factory que pode ser usada para invocar RScript.exe. Esse exemplo funciona apenas no seu próprio cluster HDInsight (não sob demanda) que já tenha o R instalado nele. |
| [Invocar trabalhos Spark no cluster Hadoop do HDInsight](https://docs.microsoft.com/azure/data-factory/tutorial-transform-data-spark-portal) |Esse exemplo mostra como usar a atividade MapReduce para invocar um programa Spark. O programa Spark apenas copia dados de um contêiner de Blob do Azure para outro. |
| [Análise do Twitter usando a Atividade de Pontuação em Lotes do Azure Machine Learning](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-AzureMLBatchScoringActivity) |Esse exemplo mostra como usar AzureMLBatchScoringActivity para invocar um modelo de Azure Machine Learning que executa análise de opiniões no twitter, pontuação, previsão, etc. |
| [Análise do Twitter usando a atividade personalizada](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-CustomC%23Activity) |Esse exemplo mostra como usar uma atividade .NET personalizada para invocar um modelo de Azure Machine Learning que executa análise de opiniões no twitter, pontuação, previsão, etc. |
| [Pipelines com parâmetros para o Azure Machine Learning](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ParameterizedPipelinesForAzureML) |O exemplo fornece um código C# de ponta a ponta para a implantação de N pipelines para classificação e treinamento, cada um com um parâmetro de região diferente, em que a lista de regiões está vindo de um arquivo parameters.txt que acompanha esse exemplo. |
| [Atualização de dados de referência para trabalhos de Stream Analytics do Azure](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ReferenceDataRefreshForASAJobs) |Esse exemplo mostra como utilizar o Azure Data Factory e o Stream Analytics do Azure para executar as consultas com dados de referência e configurar a atualização de dados de referência em uma agenda. |
| [Pipeline híbrido com Hadoop Hortonworks local](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HybridPipelineWithOnPremisesHortonworksHadoop) |O exemplo usa um cluster Hadoop local como um destino de computação para executar trabalhos de Data Factory, exatamente como você adicionaria outros destinos de computação, como um HDInsight baseado em cluster Hadoop na nuvem. |
| [Ferramenta de conversão JSON](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSONConversionTool) |Essa ferramenta permite que você converta JSONs de versões anteriores a 2015-07-01-preview para a mais recente ou 2015-07-01-preview (padrão). |
| [Arquivo de entrada de exemplo U-SQL](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/U-SQL%20Sample%20Input%20File) |Este é um arquivo de exemplo usado por uma atividade U-SQL. |
| [Excluir arquivo de blob](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/DeleteBlobFileFolderCustomActivity) | Esta amostra apresenta um arquivo em C# que pode ser usado como parte da atividade do .net personalizada do ADF para excluir arquivos do local do Blob do Azure de origem depois da cópia dos arquivos.|

## <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager
Você pode encontrar os seguintes modelos do Azure Resource Manager para Data Factory no GitHub.

| Modelo | DESCRIÇÃO |
| --- | --- |
| [Copiar do Armazenamento de Blobs do Azure para o Banco de Dados SQL do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy) |A implantação desse modelo cria um data factory do Azure, com um pipeline que copia dados do Armazenamento de Blobs do Azure especificado para o Banco de Dados SQL do Azure |
| [Copiar do Salesforce para o Blob de Armazenamento do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy) |A implantação desse modelo cria um data factory do Azure, com um pipeline que copia dados da conta do Salesforce especificada para o Armazenamento de blobs do Azure. |
| [Transformar dados executando o script Hive em um cluster do Azure HDInsight](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation) |A implantação desse modelo cria um data factory no Azure com um pipeline que transforma dados executando um script do Hive em um cluster Hadoop do Azure HDInsight. |

## <a name="samples-in-azure-portal"></a>Exemplos no portal do Azure
Você pode usar o bloco **Pipelines de exemplo** na home page de sua data factory para implantar os pipelines de exemplo e suas entidades associadas (conjuntos de dados e serviços vinculados) em sua data factory.

1. Crie uma data factory ou abra uma existente. Consulte [Copiar dados do Armazenamento de Blobs para o Banco de Dados SQL usando o Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter as etapas para criar um data factory.
2. Na folha **DATA FACTORY** para a data factory em questão, clique no bloco **Pipelines de exemplo**.

    ![Bloco Pipelines de exemplo](./media/data-factory-samples/SamplePipelinesTile.png)
3. Na folha **Pipelines de exemplo**, clique no **exemplo** que você deseja implantar.

    ![Folha Pipelines de exemplo](./media/data-factory-samples/SampleTile.png)
4. Especifique definições de configuração para o exemplo. Por exemplo, sua chave e nome da conta de armazenamento do Azure, o nome do SQL Server do Azure, banco de dados, ID de usuário e senha etc.

    ![Folha Exemplo](./media/data-factory-samples/SampleBlade.png)
5. Depois de terminar a especificação de configurações, clique em **Criar** para criar/implantar os pipelines de exemplo e serviços vinculados/tabelas usados pelos pipelines.
6. Você verá o status da implantação do bloco de exemplo em que você clicou anteriormente na folha **Pipelines de exemplo** .

    ![Status da Implantação](./media/data-factory-samples/DeploymentStatus.png)
7. Quando você vir a mensagem **Implantação bem-sucedida** no bloco para o exemplo, feche a folha **Pipelines de exemplo**.  
8. Na folha **DATA FACTORY**, você verá que os pipelines, conjuntos de dados e serviços vinculados são adicionados à sua data factory.  

    ![Folha Data Factory](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="samples-in-visual-studio"></a>Exemplos no Visual Studio
### <a name="prerequisites"></a>Pré-requisitos
Você deve ter os seguintes itens instalados no seu computador:

* Visual Studio 2013 ou Visual Studio 2015
* Baixe o SDK do Azure para Visual Studio 2013 ou Visual Studio de 2015. Navegue até a [Página de Download do Azure](https://azure.microsoft.com/downloads/) e clique em **VS 2013** ou **VS 2015** na seção **.NET**.
* Baixe o último plug-in do Azure Data Factory para o Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) ou [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Se você está usando o Visual Studio 2013, também é possível atualizar o plug-in executando as seguintes etapas: No menu, clique em **Ferramentas** -> **Extensões e Atualizações** -> **Online** -> **Galeria do Visual Studio** -> **Ferramentas do Microsoft Azure Data Factory para Visual Studio** -> **Atualizar**.

### <a name="use-data-factory-templates"></a>Use modelos de Data Factory
1. Clique em **Arquivo** no menu, aponte para **Novo** e clique em **Projeto**.
2. Na caixa de diálogo **Novo Projeto**, faça o seguinte:

   1. Selecione **DataFactory** em **Modelos**.
   2. Selecione **Modelos de Data Factory** no painel à direita.
   3. Insira um **nome** para o projeto.
   4. Selecione um **local** para o projeto.
   5. Clique em **OK**.

      ![Caixa de diálogo Novo projeto](./media/data-factory-samples/vs-new-project-adf-templates.png)
3. Na caixa de diálogo **Modelos de Data Factory**, selecione o modelo de exemplo da seção **Modelos de caso de uso** seção e clique em **Avançar**. As etapas a seguir vão orientá-lo usando o modelo **Criação de Perfil de Clientes** . As etapas são semelhantes para os outros modelos.

    ![Caixa de diálogo Modelos de Data Factory](./media/data-factory-samples/vs-data-factory-templates-dialog.png)
4. Na caixa de diálogo **Configuração de Data Factory**, clique em **Avançar** na página **Noções básicas de Data Factory**.
5. Na página **Configurar data factory**, execute as seguintes etapas:
   1. Selecione **Criar Nova Fábrica de Dados**. Você também pode selecionar **Usar data factory existente**.
   2. Insira um **nome** para o armazenamento de dados.
   3. Escolha a **assinatura do Azure** em que você deseja que o data factory seja criado.
   4. Selecione o **grupo de recursos** para o data factory a ser criado.
   5. Selecione o **Oeste dos EUA**, **Leste dos EUA** ou **Europa Setentrional** como a **região**.
   6. Clique em **Avançar**.
6. Na página **Configurar armazenamentos de dados**, especifique um **Banco de dados SQL do Azure** e **Conta de armazenamento do Azure** (ou) crie banco de dados/armazenamento e clique em Avançar.
7. Na página **Configurar computação**, selecione os padrões e clique em **Avançar**.
8. Na página **Resumo** examine as configurações e clique em **Avançar**.
9. Na página **Status da Implantação**, aguarde até que a implantação seja concluída e clique em **Concluir**.
10. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto e clique em **Publicar**.
11. Se a caixa de diálogo **Entrar na sua conta da Microsoft** for exibida, insira as credenciais da conta com a assinatura do Azure e clique em **entrar**.
12. Você deve ver a caixa de diálogo a seguir:

    ![Caixa de diálogo Publicar](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
13. Na página **Configurar data factory**, execute as seguintes etapas:

    1. Confirme a opção **Usar data factory existente** .
    2. Selecione a **da data factory** que tinha selecionado ao usar o modelo.
    3. Clique em **Avançar** para alternar para a página **Publicar Itens**. (Pressione **TAB** para sair do campo Nome se o botão **Avançar** estiver desabilitado).
14. Na página **Publicar Itens**, verifique se todas as entidades de Data Factories estão selecionadas e clique em **Avançar** para alternar para a página **Resumo**.     
15. Examine o resumo e clique em **Avançar** para iniciar o processo de implantação e exibir o **Status da Implantação**.
16. Na página **Status da Implantação** , você deve ver o status do processo de implantação. Clique em Concluir depois que a implantação tiver terminado.

Confira [Criar sua primeira data factory (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md) para obter detalhes sobre como usar o Visual Studio para criar entidades de Data Factory e publicá-las no Azure.          

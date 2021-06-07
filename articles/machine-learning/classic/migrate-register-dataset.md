---
title: 'ML Studio (clássico): migrar para o conjunto de Azure Machine Learning-Rebuild'
description: Recompilar conjuntos de DataStudio (clássicos) no designer de Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 02/04/2021
ms.openlocfilehash: 4c04dd5a2b41b3db54b20c9e514767453951cc35
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103564771"
---
# <a name="migrate-a-studio-classic-dataset-to-azure-machine-learning"></a>Migrar um conjunto de um estúdio (clássico) para Azure Machine Learning

Neste artigo, você aprenderá a migrar um conjunto de um estúdio (clássico) para Azure Machine Learning. Para obter mais informações sobre como migrar do Studio (clássico), consulte [o artigo Visão geral da migração](migrate-overview.md).

Você tem três opções para migrar um conjunto de uma para Azure Machine Learning. Leia cada seção para determinar qual é a melhor opção para seu cenário.


|Onde estão os dados? | Opção de migração  |
|---------|---------|
|No estúdio (clássico)     |  Opção 1: [baixar o conjunto de os do Studio (clássico) e carregá-lo no Azure Machine Learning](#download-the-dataset-from-studio-classic).      |
|Armazenamento em nuvem     | Opção 2: [registrar um conjunto de registros de uma fonte de nuvem](#import-data-from-cloud-sources). <br><br>  Opção 3: [usar o módulo importar dados para obter dados de uma fonte de nuvem](#import-data-from-cloud-sources).        |

> [!NOTE]
> Azure Machine Learning também dá suporte [a fluxos de trabalho de primeiro código](../how-to-create-register-datasets.md) para criar e gerenciar conjuntos de os. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Um Workspace do Azure Machine Learning. [Criar um workspace do Azure Machine Learning](../how-to-manage-workspace.md#create-a-workspace).
- Um conjunto de um estúdio (clássico) para migrar.


## <a name="download-the-dataset-from-studio-classic"></a>Baixar o conjunto de os do estúdio (clássico)

A maneira mais simples de migrar um conjunto de um estúdio (clássico) para Azure Machine Learning é baixar seu conjunto de registros e registrá-lo no Azure Machine Learning. Isso cria uma nova cópia do conjunto de seus conjuntos de seus e carrega-o em um repositório de armazenamento Azure Machine Learning.

Você pode baixar diretamente os seguintes tipos de conjuntos de conjunto de estúdio (clássico).

* Texto sem formatação (.txt)
* Valores separados por vírgulas (CSV) com cabeçalho (.csv) ou sem (.nh.csv)
* Valores separados por tabulação (TSV) com cabeçalho (.tsv) ou sem (.nh.tsv)
* Arquivo do Excel
* Arquivo zip (.zip)

Para baixar conjuntos de itens diretamente:
1. Vá para seu espaço de trabalho do estúdio (clássico) ( [https://studio.azureml.net](https://studio.azureml.net) ).
1. Na barra de navegação à esquerda, selecione a guia **conjuntos de valores** .
1. Selecione os conjuntos de (s) que você deseja baixar.
1. Na barra de ação inferior, selecione **baixar**.

    ![Captura de tela mostrando como baixar um conjunto de um DataSet no Studio (clássico)](./media/migrate-register-dataset/download-dataset.png)

Para os tipos de dados a seguir, você deve usar o módulo **converter para CSV** para baixar conjuntos.

* Dados de SVMLight (. SVMLight) 
* Dados de formato de arquivo de relação de atributo (ARFF) (. ARFF) 
* Arquivo de workspace ou objeto R (.RData)
* Tipo de conjunto de dados (. Data). O tipo de conjunto de dados é um tipo de dado interno Studio (clássico) para a saída do módulo.

Para converter o conjunto de um CSV e baixar os resultados:

1. Vá para seu espaço de trabalho do estúdio (clássico) ( [https://studio.azureml.net](https://studio.azureml.net) ).
1. Criar um novo teste.
1. Arraste e solte o conjunto de um que você deseja baixar na tela.
1. Adicione um módulo **converter em CSV** .
1. Conecte a porta de entrada **converter para CSV** à porta de saída do conjunto de dados.
1. Execute o experimento.
1. Clique com o botão direito do mouse no módulo **converter para CSV** .
1. Selecione **resultados download do conjunto de resultado**  >  .

    ![Captura de tela mostrando como configurar um pipeline converter para CSV](./media/migrate-register-dataset/csv-download-dataset.png)

### <a name="upload-your-dataset-to-azure-machine-learning"></a>Carregar seu conjunto de seus conjuntos de Azure Machine Learning

Depois de baixar o arquivo de dados, você pode registrar o DataSet no Azure Machine Learning:

1. Vá para Azure Machine Learning Studio ([ml.Azure.com](https://ml.azure.com)).
1. No painel de navegação esquerdo, selecione a guia **conjuntos de valores** .
1. Selecione **criar conjunto**  >  **de conjuntos de arquivos locais**.
    ![Captura de tela mostrando a guia DataSets e o botão para criar um arquivo local](./media/migrate-register-dataset/register-dataset.png)
1. Insira um nome e uma descrição.
1. Para **tipo de conjunto de texto**, selecione **tabular**.

    > [!NOTE]
    > Você também pode carregar arquivos ZIP como conjuntos de os. Para carregar um arquivo ZIP, selecione **arquivo** para **tipo de conjunto de texto**.

1. **Para repositório de armazenamento e seleção de arquivo**, selecione o repositório de armazenamento no qual você deseja carregar o arquivo de conjunto de arquivos.

    Por padrão, o Azure Machine Learning armazena o conjunto de blobstore no espaço de trabalho padrão. Para obter mais informações sobre armazenamentos de dados, consulte [conectar-se aos serviços de armazenamento](../how-to-access-data.md).

1. Defina as configurações de análise de dados e o esquema para seu conjunto. Em seguida, confirme suas configurações.

## <a name="import-data-from-cloud-sources"></a>Importar dados de fontes de nuvem

Se os dados já estiverem em um serviço de armazenamento em nuvem e você quiser manter seus dados em seu local nativo. Você pode usar uma das seguintes opções:

|Método de ingestão|Descrição|
|---| --- |
|Registrar um conjunto de Azure Machine Learning|Ingerir dados de fontes de dados locais e online (BLOB, ADLS Gen1, ADLS Gen2, compartilhamento de arquivos, banco de dados SQL). <br><br>Cria uma referência à fonte de dados, que é avaliada lentamente em tempo de execução. Use esta opção se você acessar este conjunto de dados repetidamente e desejar habilitar recursos de recurso avançados, como controle de versão e monitoramento de dados.
|Importar módulo de dados|Ingerir dados de fontes de dados online (BLOB, ADLS Gen1, ADLS Gen2, compartilhamento de arquivos, banco de dados SQL). <br><br> O conjunto de conjuntos só é importado para a execução de pipeline do designer atual.


>[!Note]
> Os usuários do estúdio (clássico) devem observar que as seguintes fontes de nuvem não têm suporte nativo no Azure Machine Learning:
> - Consulta de Hive
> - tabela do Azure
> - Azure Cosmos DB
> - Banco de dados SQL local
>
> Recomendamos que os usuários migrem seus dados para um serviço de armazenamento com suporte usando Azure Data Factory.  

### <a name="register-an-azure-machine-learning-dataset"></a>Registrar um conjunto de Azure Machine Learning

Use as etapas a seguir para registrar um conjunto de Azure Machine Learning de um serviço de nuvem: 

1. [Crie um repositório](../how-to-connect-data-ui.md#create-datastores)de armazenamento, que vincula o serviço de armazenamento em nuvem ao seu espaço de trabalho Azure Machine Learning. 

1. [Registrar um conjunto de uma](../how-to-connect-data-ui.md#create-datasets). Se você estiver migrando um conjunto de um estúdio (clássico) do Studio, selecione a configuração do conjunto de **tabelas tabular** .

Depois de registrar um conjunto de um DataSet no Azure Machine Learning, você pode usá-lo no designer:
 
1. Crie um novo rascunho do pipeline do designer.
1. Na paleta de módulo à esquerda, expanda a seção **conjuntos de valores** .
1. Arraste o conjunto de seus DataSets para a tela. 

### <a name="use-the-import-data-module"></a>Usar o módulo importar dados

Use as etapas a seguir para importar dados diretamente para o pipeline do designer:

1. [Crie um repositório](https://github.com/MicrosoftDocs/azure-docs-pr/blob/master/articles/machine-learning/how-to-connect-data-ui.md#create-datastores)de armazenamento, que vincula o serviço de armazenamento em nuvem ao seu espaço de trabalho Azure Machine Learning. 

Depois de criar o armazenamento de dados, você pode usar o módulo [**importar data**](../algorithm-module-reference/import-data.md) no designer para ingerir dados dele:

1. Crie um novo rascunho do pipeline do designer.
1. Na paleta de módulo à esquerda, localize o módulo **importar dados** e arraste-o para a tela.
1. Selecione o módulo **importar dados** e use as configurações no painel direito para configurar sua fonte de dados.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como migrar um conjunto de um estúdio (clássico) para Azure Machine Learning. A próxima etapa é [recriar um pipeline de treinamento do Studio (clássico)](migrate-rebuild-experiment.md).


Consulte os outros artigos da série de migração do Studio (clássico):

1. [Visão geral da migração](migrate-overview.md).
1. **Migre conjuntos** de os.
1. [Recompile um pipeline de treinamento do Studio (clássico)](migrate-rebuild-experiment.md).
1. [Recompile um serviço Web Studio (clássico)](migrate-rebuild-web-service.md).
1. [Integre um serviço web Azure Machine Learning com aplicativos cliente](migrate-rebuild-integrate-with-client-app.md).
1. [Migrar executar script R](migrate-execute-r-script.md).

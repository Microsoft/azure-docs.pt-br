---
title: Identificar cenários para o Azure Machine Learning – Processo de Ciência de Dados da Equipe
description: Escolha os cenários apropriados para realizar a análise preditiva avançada com o Processo de Ciência de Dados de Equipe.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 2d589d6c3394556499daf033c4c1d528a214b0e3
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93319294"
---
# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>Cenários para análises avançadas no Azure Machine Learning
Este artigo descreve as diversas fontes de dados de exemplo e os cenários de destino que podem ser manipulados pelo [TDSP (Processo de Ciência de Dados de Equipe)](overview.md). O TDSP fornece uma abordagem sistemática para que as equipes colaborem na criação de aplicativos inteligentes. Os cenários apresentados aqui ilustram as opções disponíveis no fluxo de trabalho de processamento de dados que dependem das características de dados, de locais de origem e de repositórios de destino no Azure.

A **árvore de decisão** para selecionar os cenários de exemplo apropriados para seus dados e objetivo é apresentada na última seção.

Cada uma das seções a seguir apresenta um cenário de exemplo. Para cada cenário, são listados uma possível ciência de dados ou fluxo de análise avançada e os recursos de suporte do Azure.

> [!NOTE]
> **Para todos os cenários a seguir, você precisa:**
> <br/>
> 
> * [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md)
>   <br/>
> * [Criar um workspace de Azure Machine Learning](../classic/create-workspace.md)
> 
> 

## <a name="scenario-1-small-to-medium-tabular-dataset-in-local-files"></a><a name="smalllocal"></a>Cenário \# 1: conjunto de conjuntos de tabela pequeno a médio em arquivos locais
![Arquivos locais pequenos a médios][1]

#### <a name="additional-azure-resources-none"></a>Recursos adicionais do Azure: nenhum
1. Faça logon no [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Carregue um conjunto de dados.
1. Crie um fluxo experimental do Azure Machine Learning começando com os conjuntos de dados carregados.

## <a name="scenario-2-small-to-medium-dataset-of-local-files-that-require-processing"></a><a name="smalllocalprocess"></a>Cenário \# 2: conjunto de pequenos a médios de arquivos locais que exigem processamento
![Arquivos locais pequenos a médios com processamento][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Recursos adicionais do Azure: Máquina Virtual do Azure (servidor IPython Notebook)
1. Crie uma Máquina Virtual do Azure que executa o IPython Notebook.
1. Carregar dados em um contêiner de armazenamento do Azure.
1. Pré-processe e limpe dados no IPython notebook, acessando dados do contêiner de armazenamento do Azure.
1. Transformar dados em um formulário tabular limpo.
1. Salve os dados transformados nos blobs do Azure.
1. Faça logon no [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Leia os dados dos blobs do Azure usando o módulo [Importar Dados][import-data].
1. Crie um fluxo experimental do Azure Machine Learning começando com os conjuntos de dados ingeridos.

## <a name="scenario-3-large-dataset-of-local-files-targeting-azure-blobs"></a><a name="largelocal"></a>Cenário \# 3: conjunto de grandes conjuntos de arquivos locais, direcionando BLOBs do Azure
![Arquivos locais grandes][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Recursos adicionais do Azure: Máquina Virtual do Azure (servidor IPython Notebook)
1. Crie uma Máquina Virtual do Azure que executa o IPython Notebook.
1. Carregar dados em um contêiner de armazenamento do Azure.
1. Pré-processe e limpe dados no IPython Notebook, acessando dados nos blobs do Azure.
1. Transforme dados em um formulário tabular limpo, se necessário.
1. Explore dados e crie recursos conforme for necessário.
1. Extraia um exemplo de dados de pequeno a médio porte.
1. Salve os dados do exemplo nos blobs do Azure.
1. Faça logon no [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Leia os dados dos blobs do Azure usando o módulo [Importar Dados][import-data].
1. Crie um fluxo experimental do Azure Machine Learning, começando com os conjuntos de dados ingeridos.

## <a name="scenario-4-small-to-medium-dataset-of-local-files-targeting-sql-server-in-an-azure-virtual-machine"></a><a name="smalllocaltodb"></a>Cenário \#4: conjunto de dados pequeno a médio em arquivos locais, com o SQL Server na Máquina Virtual do Azure como destino
![Arquivos locais pequenos a médios para o Banco de Dados SQL no Azure][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Recursos adicionais do Azure: Máquina Virtual do Azure (SQL Server/ servidor IPython Notebook)
1. Crie uma Máquina Virtual do Azure que executa SQL Server + IPython Notebook.
1. Carregar dados em um contêiner de armazenamento do Azure.
1. Pré-processe e limpe dados no contêiner de armazenamento do Azure usando o notebook IPython.
1. Transforme dados em um formulário tabular limpo, se necessário.
1. Salve dados em arquivos locais da VM (o IPython Notebook está em execução na VM, as unidades locais se referem às unidades da VM).
1. Carregue dados para o banco de dados do SQL Server em execução em uma VM do Azure.
   
   Opção \#1: usar o SQL Server Management Studio.
   
   * Fazer logon na VM SQL Server
   * Execute o SQL Server Management Studio.
   * Crie o banco de dados e as tabelas de destino.
   * Use um dos métodos de importação em massa para carregar os dados dos arquivos locais da VM.
   
   Opção \#2: usar o IPython Notebook – não é aconselhável para conjuntos de dados médios e maiores
   
   <!-- -->    
   * Use uma cadeia de conexão ODBC para acessar o SQL Server na VM.
   * Crie o banco de dados e as tabelas de destino.
   * Use um dos métodos de importação em massa para carregar os dados dos arquivos locais da VM.
1. Explore dados e crie recursos conforme necessário. Os recursos não precisam ser materializados nas tabelas do banco de dados. Anote somente a consulta necessária para criá-los.
1. Escolha um tamanho de amostra de dados, se necessário e/ou desejado.
1. Faça logon no [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Leia os dados diretamente do SQL Server usando o módulo [Importar Dados][import-data]. Cole a consulta necessária que extrai campos, cria recursos e amostras de dados, se necessário, diretamente na consulta [importar dados][import-data] .
1. Crie um fluxo experimental do Azure Machine Learning, começando com os conjuntos de dados ingeridos.

## <a name="scenario-5-large-dataset-in-local-files-target-sql-server-in-azure-vm"></a><a name="largelocaltodb"></a>Cenário \# 5: DataSet grande em arquivos locais, SQL Server de destino na VM do Azure
![Arquivos locais grandes para o Banco de Dados SQL no Azure][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Recursos adicionais do Azure: Máquina Virtual do Azure (SQL Server/ servidor IPython Notebook)
1. Crie uma Máquina Virtual do Azure que executa SQL Server e o servidor IPython Notebook.
1. Carregar dados em um contêiner de armazenamento do Azure.
1. (Opcional) Pré-processe e limpe os dados.
   
    a.  Pré-processe e limpe dados no IPython Notebook, acessando dados nos blobs do Azure.
   
    b.  Transforme dados em um formulário tabular limpo, se necessário.
   
    c.  Salve dados em arquivos locais da VM (o IPython Notebook está em execução na VM, as unidades locais se referem às unidades da VM).
1. Carregue dados para o banco de dados do SQL Server em execução em uma VM do Azure.
   
    a.  Faça logon na VM SQL Server.
   
    b.  Se os dados ainda não foram salvos, baixe os arquivos de dados do contêiner de armazenamento do Azure para a pasta local da VM.
   
    c.  Execute o SQL Server Management Studio.
   
    d.  Crie o banco de dados e as tabelas de destino.
   
    e.  Use um dos métodos de importação em massa para carregar os dados.
   
    f.  Se junções de tabelas forem necessárias, crie índices para agilizá-las.
   
   > [!NOTE]
   > Para um carregamento mais rápido de grandes quantidades de dados, é recomendável criar tabelas particionadas e importar em massa os dados em paralelo. Para saber mais, consulte [Importação de Dados em Paralelo para Tabelas Particionadas do SQL](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Explore dados e crie recursos conforme necessário. Os recursos não precisam ser materializados nas tabelas do banco de dados. Anote somente a consulta necessária para criá-los.
1. Escolha um tamanho de amostra de dados, se necessário e/ou desejado.
1. Faça logon no [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Leia os dados diretamente do SQL Server usando o módulo [Importar Dados][import-data]. Cole a consulta necessária que extrai campos, cria recursos e amostras de dados, se necessário, diretamente na consulta [importar dados][import-data] .
1. Fluxo experimental simples do Azure Machine Learning começando com o conjunto de dados carregado

## <a name="scenario-6-large-dataset-in-a-sql-server-database-on-premises-targeting-sql-server-in-an-azure-virtual-machine"></a><a name="largedbtodb"></a>Cenário \# 6: DataSet grande em um banco de dados SQL Server local, direcionando SQL Server em uma máquina virtual do Azure
![Banco de Dados SQL local grande para o Banco de Dados SQL no Azure][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Recursos adicionais do Azure: Máquina Virtual do Azure (SQL Server/ servidor IPython Notebook)
1. Crie uma Máquina Virtual do Azure que executa SQL Server e o servidor IPython Notebook.
1. Use um dos métodos de exportação de dados para exportar os dados do SQL Server para arquivos de despejo.
   
   > [!NOTE]
   > Se você decidir mover todos os dados do banco de dado local, um método alternativo (mais rápido) para mover o banco de dados completo para a instância de SQL Server no Azure. Ignore as etapas para exportar dados, criar o banco de dados e carregar/importar dados para o banco de dados de destino e execute o método alternativo.
   > 
   > 
1. Carregar arquivos de despejo para o contêiner de armazenamento do Azure.
1. Carregue os dados para um banco de dados do SQL Server em execução em uma Máquina Virtual do Azure.
   
   a.  Faça logon na VM SQL Server.
   
   b.  Baixe arquivos de dados de um contêiner de armazenamento do Azure para a pasta local da VM.
   
   c.  Execute o SQL Server Management Studio.
   
   d.  Crie o banco de dados e as tabelas de destino.
   
   e.  Use um dos métodos de importação em massa para carregar os dados.
   
   f.  Se junções de tabelas forem necessárias, crie índices para agilizá-las.
   
   > [!NOTE]
   > Para um carregamento mais rápido de grandes quantidades de dados, crie tabelas particionadas e importe em massa os dados em paralelo. Para saber mais, consulte [Importação de Dados em Paralelo para Tabelas Particionadas do SQL](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Explore dados e crie recursos conforme necessário. Os recursos não precisam ser materializados nas tabelas do banco de dados. Anote somente a consulta necessária para criá-los.
1. Escolha um tamanho de amostra de dados, se necessário e/ou desejado.
1. Faça logon no [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Leia os dados diretamente do SQL Server usando o módulo [Importar Dados][import-data]. Cole a consulta necessária que extrai campos, cria recursos e amostras de dados, se necessário, diretamente na consulta [importar dados][import-data] .
1. Fluxo experimental simples do Azure Machine Learning começando com o conjunto de dados carregado.

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>Método alternativo para copiar um banco de dados completo de um SQL Server local para o Banco de Dados SQL do Azure
![Desanexar o banco de dados local e anexar ao Banco de Dados SQL no Azure][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Recursos adicionais do Azure: Máquina Virtual do Azure (SQL Server/ servidor IPython Notebook)
Para replicar todo o banco de dados do SQL Server na sua VM do SQL Server, você deve copiar um banco de dados de um local/servidor para outro, supondo que o banco de dados possa ficar offline temporariamente. Você pode usar SQL Server Management Studio pesquisador de objetos ou usando os comandos Transact-SQL equivalentes.

1. Desanexe o banco de dados no local de origem. Para obter mais informações, consulte [desanexar um banco de dados](https://technet.microsoft.com/library/ms191491\(v=sql.110\).aspx).
1. Na janela de prompt de comando do Windows Explorer ou Windows, copie os arquivos do banco de dados e os arquivos de log desanexados para o local de destino na VM do SQL Server no Azure.
1. Anexe os arquivos copiados à instância de destino do SQL Server. Para obter mais informações, consulte [Attach a Database](https://technet.microsoft.com/library/ms190209\(v=sql.110\).aspx).

[Mover um banco de dados usando desanexar e anexar (Transact-SQL)](https://technet.microsoft.com/library/ms187858\(v=sql.110\).aspx)

## <a name="scenario-7-big-data-in-local-files-target-hive-database-in-azure-hdinsight-hadoop-clusters"></a><a name="largedbtohive"></a>Cenário \# 7: Big data em arquivos locais, banco de dados do hive de destino em clusters Azure HDInsight Hadoop
![Big Data no Hive local de destino][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>Recursos adicionais do Azure: cluster Hadoop do Azure HDInsight e Máquina Virtual do Azure (servidor IPython Notebook)
1. Crie uma Máquina Virtual do Azure que executa o servidor IPython Notebook.
1. Personalize um cluster Hadoop do Azure HDInsight.
1. (Opcional) Pré-processe e limpe os dados.
   
   a.  Pré-processe e limpe dados no IPython Notebook, acessando dados nos blobs do Azure.
   
   b.  Transforme dados em um formulário tabular limpo, se necessário.
   
   c.  Salve dados em arquivos locais da VM (o IPython Notebook está em execução na VM, as unidades locais se referem às unidades da VM).
1. Carregue dados no contêiner padrão do cluster Hadoop selecionado na etapa 2.
1. Carregue dados para o banco de dados Hive no cluster Hadoop do Azure HDInsight.
   
   a.  Faça logon no nó principal do cluster Hadoop
   
   b.  Abra a linha de comando do Hadoop.
   
   c.  Insira o diretório raiz do Hive pelo comando `cd %hive_home%\bin` na linha de comando do Hadoop.
   
   d.  Execute as consultas do Hive para criar o banco de dados e tabelas e carregue dados do armazenamento de blob para as tabelas do Hive.
   
   > [!NOTE]
   > Se o volume de dados for grande, os usuários podem criar a tabela do Hive com partições. Em seguida, os usuários podem usar um loop `for` na linha de comando do Hadoop no nó principal para carregar dados na tabela do Hive particionada, por partição.
   > 
   > 
1. Explore dados e crie recursos conforme necessário na linha de comando do Hadoop. Os recursos não precisam ser materializados nas tabelas do banco de dados. Anote somente a consulta necessária para criá-los.
   
   a.  Faça logon no nó principal do cluster Hadoop
   
   b.  Abra a linha de comando do Hadoop.
   
   c.  Insira o diretório raiz do Hive pelo comando `cd %hive_home%\bin` na linha de comando do Hadoop.
   
   d.  Execute as consultas do Hive na linha de comando do Hadoop no nó principal do cluster do Hadoop para explorar os dados e criar recursos conforme necessário.
1. Se necessário e/ou desejado, faça amostras dos dados para que eles caibam no Azure Machine Learning Studio.
1. Faça logon no [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Leia os dados diretamente do `Hive Queries` usando o módulo [Importar Dados][import-data]. Cole a consulta necessária que extrai campos, cria recursos e amostras de dados, se necessário, diretamente na consulta [importar dados][import-data] .
1. Fluxo experimental simples do Azure Machine Learning começando com o conjunto de dados carregado.

## <a name="decision-tree-for-scenario-selection"></a><a name="decisiontree"></a>Árvore de decisão para a seleção do cenário
---
O diagrama a seguir resume os cenários descritos acima e as opções do Processo e Tecnologia de Análise Avançada selecionadas que o levam a cada um dos cenários detalhados. O processamento de dados, a exploração, a engenharia de recursos e a amostragem podem ocorrer em um ou mais métodos/ambiente – nos ambientes de origem, intermediário e/ou de destino – e podem continuar iterativamente conforme necessário. O diagrama só serve para ilustrar alguns dos fluxos de possíveis e não fornece uma enumeração completa.

![Cenários de passo a passo de exemplo do processo de Ciência de Dados][8]

### <a name="advanced-analytics-in-action-examples"></a>Exemplos de análise avançada em ação
Para obter um passo a passo do Azure Machine Learning que emprega o Processo e Tecnologia de Análise Avançada usando conjuntos de dados públicos, consulte:

* [Processo de Ciência de Dados de Equipe em ação: usando o SQL Server](sql-walkthrough.md).
* [Processo de Ciência de Dados de Equipe em ação: usando clusters Hadoop do HDInsight](hive-walkthrough.md).

[1]: ./media/plan-sample-scenarios/dsp-plan-small-in-aml.png
[2]: ./media/plan-sample-scenarios/dsp-plan-local-with-processing.png
[3]: ./media/plan-sample-scenarios/dsp-plan-local-large.png
[4]: ./media/plan-sample-scenarios/dsp-plan-local-to-db.png
[5]: ./media/plan-sample-scenarios/dsp-plan-large-to-db.png
[6]: ./media/plan-sample-scenarios/dsp-plan-db-to-db.png
[7]: ./media/plan-sample-scenarios/dsp-plan-attach-db.png
[8]: ./media/plan-sample-scenarios/dsp-plan-sample-scenarios.png
[9]: ./media/plan-sample-scenarios/dsp-plan-local-to-hive.png


<!-- Module References -->
[import-data]: /azure/machine-learning/studio-module-reference/import-data
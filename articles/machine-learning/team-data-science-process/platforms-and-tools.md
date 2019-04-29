---
title: Plataformas e ferramentas para projetos de ciência de dados – Processo de Ciência de Dados da Equipe
description: Discrimina e discute os recursos de análise e os dados disponíveis para as empresas com padronização do processo de Ciência de Dados da Equipe.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 31c6c1258b6d20eb2d2802cf23244cb59df682c0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61428608"
---
# <a name="platforms-and-tools-for-data-science-projects"></a>Plataformas e ferramentas para projetos de ciência de dados

A Microsoft fornece uma gama completa de dados e serviços de análise, bem como recursos para plataformas na nuvem ou locais. Eles podem ser implantados para tornar a execução de seus projetos de ciência de dados eficiente e escalonável. Diretrizes para equipes que estão implementando projetos de ciência de dados de modo rastreável, com controle de versão e colaborativo são fornecidas pelo TDSP [(Processo de Ciência de Dados de Equipe)](overview.md).  Para obter uma descrição das funções pessoais e das tarefas associadas que são tratadas por uma equipe de ciência de dados com padronização nesse processo, consulte [Tarefas e funções do Processo de Ciência de Dados da Equipe](roles-tasks.md).

Os serviços de análise e dados disponíveis para as equipes de ciência de dados usando o TDSP incluem:

- Máquinas Virtuais de Ciência de Dados (Windows e Linux CentOS)
- Clusters do HDInsight Spark
- SQL Data Warehouse
- Azure Data Lake
- Clusters do HDInsight Hive
- Armazenamento de Arquivos do Azure
- Serviços de R do SQL Server 2016

Neste documento, descrevemos brevemente os recursos e fornecemos links para tutoriais e passo a passo que as equipes de TDSP publicaram. Eles podem ajudar você a aprender a como usá-los passo a passo e começar a usá-los para criar seus aplicativos inteligentes. Mais informações sobre esses recursos estão disponíveis nas páginas do produto. 

## <a name="data-science-virtual-machine-dsvm"></a>DSVM (Máquina Virtual de Ciência de Dados)

A máquina virtual de ciência de dados, oferecida no Windows e no Linux pela Microsoft, contém ferramentas populares de atividades de modelagem e desenvolvimento de ciência de dados. Ela inclui ferramentas como:

- Microsoft R Server Developer Edition 
- Distribuição do Anaconda Python
- Notebooks Jupyter para Python e R 
- Visual Studio Community Edition com Python e ferramentas de R no Windows/Eclipse no Linux
- Área de trabalho do Power BI para Windows
- SQL Server 2016 Developer Edition no Windows/Postgres no Linux

Ele também inclui **ferramentas ML e AI** como xgboost, mxnet e Vowpal Wabbit.

No momento, a DSVM está disponível nos sistemas operacionais **Windows** e **Linux CentOS**. Escolha o tamanho da sua DSVM (número de núcleos de CPU e quantidade de memória) com base nas necessidades dos projetos de ciência de dados que você planeja executar nela. 

Para obter mais informações sobre a edição do Windows da DSVM, consulte [Máquina Virtual de Ciência de Dados da Microsoft](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) no Azure Marketplace. Para a edição do Linux da DSVM, consulte [Máquina Virtual de Ciência de Dados do Linux](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

Para saber como executar algumas das tarefas comuns de ciência de dados na DSVM com eficiência, consulte [Dez coisas que você pode fazer na Máquina Virtual de Ciência de Dados](../data-science-virtual-machine/vm-do-ten-things.md)


## <a name="azure-hdinsight-spark-clusters"></a>Clusters do Azure HDInsight Spark

O Apache Spark é uma estrutura de processamento paralelo de software livre que dá suporte ao processamento na memória para melhorar o desempenho dos aplicativos analíticos de big data. O mecanismo de processamento do Spark foi desenvolvido para velocidade, facilidade de uso e análise sofisticada. Os recursos de computação na memória do Spark fazem dele uma boa escolha para algoritmos iterativos em cálculos de aprendizado e grafo de máquina. O Spark também é compatível com o WASB (Armazenamento de Blobs do Azure) para possibilitar o fácil processamento dos dados existentes armazenados no Azure por meio do Spark.

Quando você cria um cluster do Spark no HDInsight, cria recursos de computação do Azure com o Spark instalado e configurado. Demora cerca de dez minutos para criar um cluster do Spark no HDInsight. Armazene os dados a serem processados no Armazenamento de Blobs do Azure. Para mais informações sobre como usar o Armazenamento de Blobs do Azure com o cluster, consulte [Usar o Armazenamento de Blobs do Azure compatível com HDFS com o Hadoop no HDInsight](../../hdinsight/hdinsight-hadoop-use-blob-storage.md).

A equipe de TDSP da Microsoft publicou duas explicações passo a passo completas sobre como usar os clusters do Azure HDInsight Spark para criar soluções de ciência de dados, uma usando Python e outra usando o Scala. Para saber mais sobre os **clusters do Azure HDInsight Spark**, confira [Visão geral: Apache Spark no HDInsight Linux](../../hdinsight/spark/apache-spark-overview.md). Para aprender a criar uma solução de ciência de dados usando **Python** em um cluster do Azure HDInsight Spark, consulte [Visão geral de Ciência de Dados usando o Spark no Azure HDInsight](spark-overview.md). Para aprender a criar uma solução de ciência de dados usando **Scala** em um cluster do Azure HDInsight Spark, consulte [Ciência de Dados usando o Scala e o Spark no Azure](scala-walkthrough.md). 


##  <a name="azure-sql-data-warehouse"></a>SQL Data Warehouse do Azure

O SQL Data Warehouse do Azure permite que você dimensione recursos de computação facilmente e em segundos sem excesso de provisionamento ou excesso de pagamento. Ele também oferece a opção exclusiva de fazer uma pausa nos recursos de computação, dando mais liberdade para você gerenciar melhor seus custos de nuvem. A capacidade de implantar recursos de computação escalonáveis torna possível colocar todos os seus dados no SQL Data Warehouse do Azure. Os custos de armazenamento são mínimos e você pode executar a computação somente nas partes dos conjuntos de dados que você deseja analisar. 

Para mais informações sobre o SQL Data Warehouse do Azure, confira o site [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse). Para aprender a criar soluções de análise avançada de ponta a ponta com o SQL Data Warehouse, consulte [O Processo de Ciência de Dados de Equipe em ação: usando o SQL Data Warehouse](sqldw-walkthrough.md).


## <a name="azure-data-lake"></a>Azure Data Lake

O Azure Data Lake é um repositório para toda a empresa de todos os tipos de dados coletados em um único lugar antes de qualquer definição formal de requisitos ou esquema. Essa flexibilidade permite que todos os tipos de dados sejam mantidos em um data lake, independentemente de seu tamanho ou da estrutura ou da velocidade de ingestão. As organizações podem usar o Hadoop ou a análise avançada para localizar padrões nesses data lakes. Data lakes também podem servir como um repositório de preparação de dados de baixo custo antes de coletar dados e movê-los a um data warehouse.

Para obter mais informações sobre o Azure Data Lake, consulte [Introdução ao Azure Data Lake](https://azure.microsoft.com/blog/introducing-azure-data-lake/). Para aprender a criar uma solução de ciência de dados escalonável de ponta a ponta com o Azure Data Lake, confira [Ciência de Dados Escalonável no Azure Data Lake: um passo a passo completo](data-lake-walkthrough.md)


## <a name="azure-hdinsight-hive-hadoop-clusters"></a>Clusters do Azure HDInsight Hive (Hadoop)

O Apache Hive é um sistema de data warehouse para Hadoop, que permite resumo, consulta e análise de dados usando o HiveQL, uma linguagem de consulta semelhante a SQL. O Hive pode ser usado para explorar os dados interativamente ou para criar trabalhos de processamento de lote reutilizáveis.

O Hive permite que você projete estrutura em grandes volumes de dados sem estrutura. Depois de definir a estrutura, você pode usar o Hive para consultar os dados no cluster do Hadoop sem ter que usar ou conhecer Java ou MapReduce. O HiveQL (a linguagem de consulta Hive) permite gravar consultas com declarações que são semelhantes a T-SQL.

Para cientistas de dados, o Hive pode executar UDFs (Funções Definidas pelo Usuário do Python) em consultas de Hive para processar registros. Essa capacidade estende a funcionalidade de consultas de Hive na análise de dados consideravelmente. Especificamente, ela permite que cientistas de dados conduzam engenharia de recurso escalonável em linguagens com as quais estão mais familiarizados: HiveQL tipo SQL e Python. 

Para obter mais informações sobre clusters do Azure HDInsight Hive, consulte [Usar Hive e HiveQL com Hadoop no HDInsight](../../hdinsight/hadoop/hdinsight-use-hive.md). Para saber como criar uma solução de ciência de dados de ponta a ponta escalonável com clusters do Azure HDInsight Hive, consulte [O Processo de Ciência de Dados de Equipe em ação: usar clusters do HDInsight Hadoop](hive-walkthrough.md).


## <a name="azure-file-storage"></a>Armazenamento de Arquivos do Azure 

O Armazenamento de Arquivos do Azure é um serviço que oferece compartilhamentos de arquivos na nuvem usando o Protocolo SMB padrão. Há suporte ao SMB 2.1 e ao 3.0 SMB. Com o armazenamento de Arquivos do Azure, você pode migrar aplicativos herdados que dependem de compartilhamentos de arquivos para o Azure rapidamente e sem regravações caras. Os aplicativos executados em máquinas virtuais do Azure ou serviços de nuvem ou em clientes locais podem montar um compartilhamento de arquivos na nuvem, exatamente como um aplicativo de desktop monta um compartilhamento SMB típico. Qualquer quantidade de componentes de aplicativos pode montar e acessar o compartilhamento de armazenamento de arquivos simultaneamente.

Especialmente útil para projetos de ciência de dados é a capacidade de criar um armazenamento de arquivo do Azure como o lugar para compartilhar dados do projeto com os membros da equipe de projeto. Cada um deles tem acesso à mesma cópia dos dados no armazenamento de arquivos do Azure. Eles também podem usar esse armazenamento de arquivo para compartilhar conjuntos de recursos gerados durante a execução do projeto. Se o projeto é uma interação de cliente, os clientes podem criar um armazenamento de arquivo do Azure em sua própria assinatura do Azure para compartilhar os dados do projeto e os recursos com você. Dessa forma, o cliente tem controle total sobre os ativos de dados do projeto. Para obter mais informações sobre o Armazenamento de Arquivos do Azure, confira [Introdução ao Armazenamento de Arquivos do Azure no Windows](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files) e [Como usar o Armazenamento de Arquivos do Azure com o Linux](../../storage/files/storage-how-to-use-files-linux.md).


## <a name="sql-server-2016-r-services"></a>Serviços de R do SQL Server 2016

Os Serviços de R (no banco de dados) fornecem uma plataforma para desenvolver e implantar aplicativos inteligentes que podem descobrir novos insights. Você pode usar a linguagem avançada e poderosa do R e os vários pacotes fornecidos pela comunidade de R para criar modelos e gerar previsões para seus dados do SQL Server. Como os Serviços de R (no banco de dados) integram a linguagem R com o SQL Server, a análise é mantida próxima aos dados, o que elimina os custos e os riscos de segurança associados à movimentação de dados.

Os Serviços de R (no banco de dados) dão suporte à linguagem R de software livre com um conjunto abrangente de tecnologias e ferramentas do SQL Server. Eles oferecem capacidade de gerenciamento, segurança, confiabilidade e desempenho superiores. Você pode implantar soluções de R usando ferramentas familiares e convenientes. Os aplicativos de produção podem chamar o tempo de execução de R e recuperar previsões e visuais usando Transact-SQL. Você também pode usar as bibliotecas de ScaleR para melhorar a escala e o desempenho de suas soluções de R. Para obter mais informações, confira [SQL Server R Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

A equipe de TDSP da Microsoft publicou duas orientações de ponta a ponta que mostram como criar soluções de ciência de dados nos Serviços de R do SQL Server 2016: uma para programadores de R e outra para desenvolvedores de SQL. Para **programadores de R**, consulte [Passo a passo de ponta a ponta de Ciência de Dados](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough). Para **desenvolvedores de SQL**, consulte [Análise avançada no banco de dados para desenvolvedores de SQL (tutorial)](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers).


## <a name="appendix"></a>Apêndice: Ferramentas para configurar projetos de ciência de dados

### <a name="install-git-credential-manager-on-windows"></a>Instalar o Gerenciador de credenciais do Git no Windows

Se você estiver seguindo o TDSP no **Windows**, você precisará instalar o **GCM (Gerenciador de Credenciais do Git)** para se comunicar com os repositórios do Git. Para instalar o GCM, primeiramente você precisa instalar o **Chocolaty**. Para instalar o Chocolaty e o GCM, execute os seguintes comandos no Windows PowerShell como um **administrador**:  

    iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
    choco install git-credential-manager-for-windows -y
    

### <a name="install-git-on-linux-centos-machines"></a>Instale o Git em computadores Linux (CentOS)

Execute o seguinte comando de busca para instalar o Git em computadores Linux (CentOS):

    sudo yum install git


### <a name="generate-public-ssh-key-on-linux-centos-machines"></a>Gere a chave SSH pública em computadores Linux (CentOS)

Se estiver usando computadores Linux (CentOS) para executar os comandos git, você precisará adicionar a chave SSH pública do seu computador ao servidor do Azure DevOps Services para que esse computador seja reconhecido pelo Azure DevOps Services. Primeiramente, você precisa gerar uma chave SSH pública e adicionar a chave às chaves públicas SSH em sua página de configuração de segurança do Azure DevOps Services. 

- Para gerar a chave SSH, execute os dois comandos a seguir: 

        ssh-keygen
        cat .ssh/id_rsa.pub

![Comandos para gerar a chave SSH](./media/platforms-and-tools/resources-1-generate_ssh.png)

- Copie toda a chave SSH, incluindo *ssh-rsa*. 
- Faça logon no Azure DevOps Services. 
- No canto superior direito da página, clique em **<Seu nome\>** e, em seguida, clique em **segurança**. 
    
    ![Clique no nome e em segurança](./media/platforms-and-tools/resources-2-user-setting.png)

- Clique em **Chaves públicas SSH**e clique em **+Adicionar**. 

    ![Clique nas chaves públicas SSH e em +Adicionar](./media/platforms-and-tools/resources-3-add-ssh.png)

- Cole a chave SSH que acabou de copiar na caixa de texto e salve.


## <a name="next-steps"></a>Próximas etapas

Também serão fornecidos passo a passos completos que demonstram todas as etapas do processo para **cenários específicos** . Eles serão listados e vinculados a descrições em miniatura no tópico [Instruções passo a passo de exemplo](walkthroughs.md). Eles ilustram como combinar a nuvem, as ferramentas locais e os serviços em um fluxo de trabalho ou pipeline para criar um aplicativo inteligente. 

Para obter exemplos da execução das etapas no Processo de Ciência de Dados de Equipe que usam o Microsoft Azure Machine Learning Studio, consulte o roteiro de aprendizagem [Com o Azure ML](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

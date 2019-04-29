---
title: Usar um PC com Windows com Hadoop no HDInsight - Azure
description: Trabalhe em um PC com Windows no Hadoop no HDInsight. Gerencie e consulte clusters com as ferramentas do PowerShell, Visual Studio e Linux. Desenvolva soluções de Big Data com .NET.
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 05/17/2017
ms.openlocfilehash: 4d9d1ef6b7906ecebc399948a1ca0dcd590d5910
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63765825"
---
# <a name="work-in-the-apache-hadoop-ecosystem-on-hdinsight-from-a-windows-pc"></a>Trabalhar no ecossistema Apache Hadoop no HDInsight por meio de um computador com Windows

Conheça as opções de desenvolvimento e de gerenciamento no computador com Windows para trabalhar no ecossistema Apache Hadoop no HDInsight. 

O HDInsight tem base em componentes do Apache Hadoop e do Hadoop, tecnologias de código-fonte aberto desenvolvidas no Linux. O HDInsight versão 3.4 ou superior usa a distribuição do Ubuntu Linux como o SO subjacente para o cluster. No entanto, você pode trabalhar com o HDInsight de um cliente Windows ou ambiente de desenvolvimento do Windows.

## <a name="use-powershell-for-deployment-and-management-tasks"></a>Usar o PowerShell para as tarefas de implantação e gerenciamento
O Azure PowerShell é um ambiente de geração de script que você pode usar para controlar e automatizar as tarefas de implantação e gerenciamento no HDInsight no Windows.

Exemplos de tarefas que você pode fazer com o PowerShell:

* [Criar clusters usando o PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md).
* [Executar consultas do Apache Hive usando o PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).
* [Gerenciar clusters com o PowerShell](hdinsight-administer-use-powershell.md).

Execute as etapas para [instalar e configurar o Azure Powershell](https://docs.microsoft.com/powershell/azure/install-az-ps) para obter a versão mais recente. Se você tiver scripts que precisam ser modificados para usar os novos cmdlets para o Azure Resource Manager, confira [Migrar para as ferramentas de desenvolvimento baseadas no Azure Resource Manager dos clusters de HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md).

## <a name="utilities-you-can-run-in-a-browser"></a>Utilitários que você pode executar em um navegador
Os utilitários a seguir tem uma interface de usuário na Web que é executada em um navegador Web:
* **[Azure Cloud Shell (versão prévia)](https://docs.microsoft.com/azure/cloud-shell/quickstart)** é um shell interativo de linha de comando executado no navegador e no Portal do Azure.
* **[Interface do usuário da Web do Apache Ambari](hdinsight-hadoop-manage-ambari.md)** é um utilitário de gerenciamento e monitoramento disponível no portal do Azure que pode ser usado para gerenciar tipos diferentes de trabalho, como:
    * [Usar o Apache Ambari com a API REST](hdinsight-hadoop-manage-ambari-rest-api.md)
    * [Exibição do Apache Hive no Apache Ambari](hadoop/apache-hadoop-use-hive-ambari-view.md)
    * [Exibição do Apache Tez no Apache Ambari](hdinsight-debug-ambari-tez-view.md)

## <a name="data-lake-hadoop-tools-for-visual-studio"></a>Ferramentas do Data Lake (Hadoop) para Visual Studio
Use o Data Lake Tools para Visual Studio para implantar e gerenciar topologias Storm. O Data Lake Tools também instala o SDK do SCP.NET, que permite o desenvolvimento de topologias Storm em C# com o Visual Studio.

Antes de passar para os exemplos a seguir, [instale e experimente o Data Lake Tools para Visual Studio](hadoop/apache-hadoop-visual-studio-tools-get-started.md). 

Exemplos de tarefas que você pode realizar com o Visual Studio e o Data Lake Tools para Visual Studio:
* [Implantar e gerenciar topologias Storm no Visual Studio](storm/apache-storm-deploy-monitor-topology-linux.md)
* [Desenvolver topologias em C# para Storm usando o Visual Studio](storm/apache-storm-develop-csharp-visual-studio-topology.md). Os bits incluem exemplos de modelos para topologias Storm os quais você pode conectar a bancos de dados, como o BD Cosmos do Azure e o Banco de Dados SQL.

## <a name="visual-studio-and-the-net-sdk"></a>Visual Studio e o SDK do .NET 

Você pode usar o Visual Studio com o SDK do .NET para gerenciar clusters e desenvolver aplicativos de Big Data. Você pode usar outros IDEs para as seguintes tarefas, mas os exemplos são mostrados no Visual Studio.

Exemplos de tarefas que podem ser realizadas com o SDK do .NET no Visual Studio:
* [Criar clusters e trabalhar no HDInsight de um aplicativo .NET Framework](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md).
* [Executar consultas do Apache Hive usando o SDK do .NET](hadoop/apache-hadoop-use-hive-dotnet-sdk.md).
* [Usar as funções definidas pelo usuário do C# com o streaming do Apache Hive e o Apache Pig no Apache Hadoop](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md).

> [!TIP]
> Se você estiver executando soluções .NET com clusters HDInsight baseados no Windows, é um bom momento para planejar uma migração para clusters baseados em Linux. Para saber mais, confira [Migrar solução .NET do HDInsight baseado em Windows para o HDInsight baseado em Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md).

## <a name="intellij-idea-and-eclipse-ide-for-spark-clusters"></a>IDEA do IntelliJ e IDE do Eclipse para clusters Spark
[IDEA do Intellij](https://www.jetbrains.com/idea/download) e o [IDE do Eclipse](https://www.eclipse.org/downloads/) podem ser usados para:
* Desenvolver e enviar um aplicativo Scala Spark em um cluster HDInsight Spark.
* Acessar os recursos em cluster Spark.
* Desenvolver e executar um aplicativo Scala Spark localmente.

Esses artigos mostram como: 
* IntelliJ IDEA: [Criar aplicativos do Apache Spark usando o Azure Toolkit para o plug-in do IntelliJ e o SDK de Skala.](spark/apache-spark-intellij-tool-plugin.md)
* IDE Eclipse ou IDE Scala para Eclipse: [Criar aplicativos do Apache Spark e do Azure Toolkit for Eclipse](spark/apache-spark-eclipse-tool-plugin.md) 


## <a name="notebooks-on-spark-for-data-scientists"></a>Notebooks no Spark para os cientistas de dados 
Os clusters do Apache Spark no HDInsight incluem notebooks e kernels Zeppelin que podem ser usados com os notebooks do Jupyter. 

* [Saiba como usar kernels em clusters Apache Spark com notebooks do Jupyter para testar aplicativos Spark](spark/apache-spark-zeppelin-notebook.md)
* [Saiba como usar notebooks do Apache Zeppelin em clusters Apache Spark para executar trabalhos do Spark](spark/apache-spark-jupyter-notebook-kernels.md) 


## <a name="run-linux-based-tools-and-technologies-on-windows"></a>Executar ferramentas e tecnologias baseadas em Linux no Windows

Se você encontrar uma situação na qual deve usar uma ferramenta ou tecnologia que só está disponível no Linux, considere as seguintes opções:

* **Bash (beta) no Windows 10** fornece um subsistema Linux no Windows. O Bash permite que você execute diretamente os utilitários Linux sem a necessidade de manter uma instalação dedicada do Linux. [Instalar e executar o beta Bash no Windows 10](https://msdn.microsoft.com/commandline/wsl/install_guide)
* **Docker para Windows** fornece acesso às muitas ferramentas baseadas em Linux e pode ser executado diretamente do Windows. Por exemplo, você pode usar o Docker para executar o cliente Beeline para diretamente do Windows. Você pode também usar o Docker para executar um notebook local do Jupyter e se conectar remotamente ao Spark no HDInsight. [Introdução ao Docker para Windows](https://docs.docker.com/docker-for-windows/)
* **[MobaXTerm](https://mobaxterm.mobatek.net/)** permite que você navegue graficamente no sistema de arquivos de cluster em uma conexão SSH.

## <a name="next-steps"></a>Próximos passos
Se você for um usuário novo com relação ao trabalho em clusters baseados em Linux, consulte os artigos a seguir:
* [Configurar o Apache Hadoop, Apache Kafka, Apache Spark ou outros clusters](hdinsight-hadoop-provision-linux-clusters.md)
* [Dicas para clusters HDInsight no Linux](hdinsight-hadoop-linux-information.md)

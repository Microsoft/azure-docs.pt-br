---
title: Usar um PC com Windows com Hadoop no HDInsight - Azure | Microsoft Docs
description: "Trabalhe em um PC com Windows no Hadoop no HDInsight. Gerencie e consulte clusters com as ferramentas do PowerShell, Visual Studio e Linux. Desenvolva soluções de Big Data com .NET."
services: hdinsight
keywords: hadoop no windows,hadoop para windows
author: cjgronlund
manager: jhubbard
ms.author: cgronlun
ms.date: 05/17/2017
ms.topic: article
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.openlocfilehash: e4f231c1f9b903d6cc7f2b062b30d2a072be8493
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="work-in-the-hadoop-ecosystem-on-hdinsight-from-a-windows-pc"></a>Trabalhar no ecossistema Hadoop no HDInsight por meio de um PC com Windows

Conheça as opções de desenvolvimento e de gerenciamento no PC com Windows para trabalhar no ecossistema Hadoop no HDInsight. 

O HDInsight tem base em componentes do Apache Hadoop e do Hadoop, tecnologias de código-fonte aberto desenvolvidas no Linux. O HDInsight versão 3.4 ou superior usa a distribuição do Ubuntu Linux como o SO subjacente para o cluster. No entanto, você pode trabalhar com o HDInsight de um cliente Windows ou ambiente de desenvolvimento do Windows.

## <a name="use-powershell-for-deployment-and-management-tasks"></a>Usar o PowerShell para as tarefas de implantação e gerenciamento
O Azure PowerShell é um ambiente de geração de script que você pode usar para controlar e automatizar as tarefas de implantação e gerenciamento no HDInsight no Windows.

Exemplos de tarefas que você pode fazer com o PowerShell:

* [Criar clusters usando o PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Executar consultas Hive usando o PowerShell](hdinsight-hadoop-use-hive-powershell.md)
* [Gerenciar clusters com o PowerShell](hdinsight-administer-use-powershell.md)

Execute as etapas para [instalar e configurar o Azure Powershell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) para obter a versão mais recente. Se você tiver scripts que precisam ser modificados para usar os novos cmdlets para o Azure Resource Manager, confira [Migrar para as ferramentas de desenvolvimento baseadas no Azure Resource Manager dos clusters de HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md).

## <a name="utilities-you-can-run-in-a-browser"></a>Utilitários que você pode executar em um navegador
Os utilitários a seguir tem uma interface de usuário na Web que é executada em um navegador Web:
* **[Azure Cloud Shell (versão prévia)](https://docs.microsoft.com/azure/cloud-shell/quickstart)** é um shell interativo de linha de comando executado no navegador e no Portal do Azure.
* **[Interface do usuário da Web do Ambari](hdinsight-hadoop-manage-ambari.md)** é um utilitário de gerenciamento e monitoramento disponível no Portal do Azure que pode ser usado para gerenciar tipos diferentes de trabalho, como:
    * [Usar o Ambari com a API REST](hdinsight-hadoop-manage-ambari-rest-api.md)
    * [Modo de Exibição do Hive no Ambari](hdinsight-hadoop-use-hive-ambari-view.md)
    * [Modo de Exibição do Tez no Ambari](hdinsight-debug-ambari-tez-view.md)

## <a name="data-lake-hadoop-tools-for-visual-studio"></a>Ferramentas do Data Lake (Hadoop) para Visual Studio
Use o Data Lake Tools para Visual Studio para implantar e gerenciar topologias Storm. O Data Lake Tools também instala o SDK do SCP.NET, que permite o desenvolvimento de topologias Storm em C# com o Visual Studio.

Antes de passar para os exemplos a seguir, [instale e experimente o Data Lake Tools para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md). 

Exemplos de tarefas que você pode realizar com o Visual Studio e o Data Lake Tools para Visual Studio:
* [Implantar e gerenciar topologias Storm no Visual Studio](hdinsight-storm-deploy-monitor-topology-linux.md)
* [Desenvolver topologias em C# para Storm usando o Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md). Os bits incluem exemplos de modelos para topologias Storm os quais você pode conectar a bancos de dados, como o BD Cosmos do Azure e o Banco de Dados SQL.

## <a name="visual-studio-and-the-net-sdk"></a>Visual Studio e o SDK do .NET 

Você pode usar o Visual Studio com o SDK do .NET para gerenciar clusters e desenvolver aplicativos de Big Data. Você pode usar outros IDEs para as seguintes tarefas, mas os exemplos são mostrados no Visual Studio.

Exemplos de tarefas que podem ser realizadas com o SDK do .NET no Visual Studio:
* [Criar clusters e trabalhar no HDInsight de um aplicativo do .NET Framework](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)
* [Executar consultas do Hive usando o SDK do .NET](hdinsight-hadoop-use-hive-dotnet-sdk.md)
* [Usar funções definidas pelo usuário do C# com streaming de Hive e Pig no Hadoop](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

> Dica Se você estiver executando soluções .NET com clusters HDInsight baseados no Windows, é um bom momento para planejar uma migração para clusters baseados em Linux. Para saber mais, confira [Migrar solução .NET do HDInsight baseado em Windows para o HDInsight baseado em Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md).

## <a name="intellij-idea-and-eclipse-ide-for-spark-clusters"></a>IDEA do IntelliJ e IDE do Eclipse para clusters Spark
[IDEA do Intellij](https://www.jetbrains.com/idea/download) e o [IDE do Eclipse](https://www.eclipse.org/downloads/) podem ser usados para:
* Desenvolver e enviar um aplicativo Scala Spark em um cluster HDInsight Spark.
* Acessar os recursos em cluster Spark.
* Desenvolver e executar um aplicativo Scala Spark localmente.

Esses artigos mostram como: 
* IDEA do IntelliJ: [Criar aplicativos Spark usando o plug-in do Kit de Ferramentas do Azure para Intellij e o SDK de Scala.](hdinsight-apache-spark-intellij-tool-plugin.md)
* IDE do Eclipse ou IDE do Scala para Eclipse: [Criar aplicativos Spark e o Kit de Ferramentas do Azure para Eclipse](hdinsight-apache-spark-eclipse-tool-plugin.md) 


## <a name="notebooks-on-spark-for-data-scientists"></a>Notebooks no Spark para os cientistas de dados 
Os clusters do Apache Spark no HDInsight incluem notebooks e kernels Zeppelin que podem ser usados com notebooks do Jupyter. 

* [Saiba como usar kernels em clusters Spark com notebooks do Jupyter para testar aplicativos Spark](hdinsight-apache-spark-zeppelin-notebook.md)
* [Saiba como usar notebooks do Zeppelin em clusters Spark para executar trabalhos do Spark](hdinsight-apache-spark-jupyter-notebook-kernels.md) 


## <a name="run-linux-based-tools-and-technologies-on-windows"></a>Executar ferramentas e tecnologias baseadas em Linux no Windows

Se você encontrar uma situação na qual deve usar uma ferramenta ou tecnologia que só está disponível no Linux, considere as seguintes opções:

* **Bash (beta) no Windows 10** fornece um subsistema Linux no Windows. O Bash permite que você execute diretamente os utilitários Linux sem a necessidade de manter uma instalação dedicada do Linux. [Instalar e executar o beta Bash no Windows 10](https://msdn.microsoft.com/commandline/wsl/install_guide)
* **Docker para Windows** fornece acesso às muitas ferramentas baseadas em Linux e pode ser executado diretamente do Windows. Por exemplo, você pode usar o Docker para executar o cliente Beeline para diretamente do Windows. Você pode também usar o Docker para executar um notebook local do Jupyter e se conectar remotamente ao Spark no HDInsight. [Introdução ao Docker para Windows](https://docs.docker.com/docker-for-windows/)
* **[MobaXTerm](http://mobaxterm.mobatek.net/)** permite que você navegue graficamente no sistema de arquivos de cluster em uma conexão SSH.

## <a name="next-steps"></a>Próximas etapas
Se você for um usuário novo com relação ao trabalho em clusters baseados em Linux, consulte os artigos a seguir:
* [Configurar o Hadoop, Kafka, Spark ou outros clusters](hdinsight-hadoop-provision-linux-clusters.md)
* [Dicas para clusters HDInsight no Linux](hdinsight-hadoop-linux-information.md)
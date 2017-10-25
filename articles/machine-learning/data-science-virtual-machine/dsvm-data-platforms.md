---
title: "Plataformas de dados pra a Máquina Virtual de Ciência de Dados – Azure | Microsoft Docs"
description: "Plataformas de dados para a Máquina Virtual de Ciência de Dados."
keywords: "ferramentas de ciência de dados, máquina virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: 9b8beb51c555c6125fa3b0abbad892d627a180b9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="data-platforms"></a>Plataformas de dados

A DSVM (Máquina Virtual de Ciência de Dados) permite que você crie sua análise em relação a uma ampla variedade de plataformas de dados. Além das interfaces para plataformas de dados remotas, a DSVM oferece uma instância local para rápido desenvolvimento e criação de protótipos. 

A seguir estão as ferramentas de plataforma de dados com suporte na DSVM. 

## <a name="sql-server-2016-developer-edition"></a>SQL Server 2016 Developer Edition

| | |
| ------------- | ------------- |
| O que é?   | Uma instância de banco de dados relacional local      |
| Edições do DSVM com suporte      | Windows      |
| Usos típicos      | Desenvolvimento rápido localmente com o menor conjunto de dados <br/> Executar R no banco de dados   |
| Links para exemplos      |    Uma pequena amostra do Conjunto de Dados de Nova Iorque é carregada para o banco de dados SQL `nyctaxi`. <br/> Exemplo do Jupyter mostrando Microsoft R e análise no banco de dados pode ser encontrado em:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| Ferramentas relacionadas ao DSVM       | SQL Server Management Studio <br/> Drivers ODBC/JDBC<br/> pyodbc, RODBC<br />Análise do Apache      |

> [!NOTE]
> A SQL Server 2016 Developer Edition só pode ser usada para fins de teste e desenvolvimento. Você precisa de uma licença ou de uma das VMs do SQL Server para executá-lo em produção. 


### <a name="setup"></a>Configuração

O servidor de banco de dados já está pré-configurado e os serviços do Windows relacionados ao SQL Server (como `SQL Server (MSSQLSERVER)`) são definidos para execução automática. A única etapa manual a ser executado é para habilitar a análise no banco de dados usando Microsoft R. Você pode fazer isso executando o comando a seguir como uma ação única no SSMS (SQL Server Management Studio); depois de efetuar logon como administrador do computador, abra uma "Nova Consulta" no SSMS, verifique se o banco de dados selecionado é `master` e, em seguida, execute: 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)
       
Para executar o SQL Server Management Studio, você pode pesquisar "SQL Server Management Studio" na lista de programas ou usar a pesquisa do Windows para localizá-lo e executá-lo. Quando solicitado a fornecer credenciais, escolha "Autenticação do Windows" e use o nome do computador ou ```localhost``` no Nome do SQL Server. 

### <a name="how-to-use--run-it"></a>Como usar/executar?  

O servidor de banco de dados com a instância de banco de dados padrão é executado automaticamente por padrão. Você pode usar ferramentas como o SQL Server Management Studio na VM para acessar o banco de dados do SQL Server localmente. Conta de administradores locais têm acesso de administrador no banco de dados. 

Ainda, a DSVM vem com Drivers ODBC e JDBC drivers para se comunicar com o SQL Server, bancos de dados SQL do Azure e o SQL Data Warehouse do Azure de aplicativos escritos em várias linguagens, inclusive Python, R. 

### <a name="how-is-it-configured--installed-on-the-dsvm"></a>Como é configurado/instalado no DSVM? 

O SQL Server está instalado da maneira padrão. Ele pode ser encontrado em `C:\Program Files\Microsoft SQL Server`. A instância R no banco de dados é encontrada em `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`. A DSVM também tem uma instância de R Server autônoma separada instalada em `C:\Program Files\Microsoft\R Server\R_SERVER`. Essas duas instâncias de R não compartilham as bibliotecas.


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (autônomo)

| | |
| ------------- | ------------- |
| O que é?   | Uma instância autônoma (único nó em processo) da plataforma do Apache Spark popular, um sistema de aprendizado de máquina e processamento rápido de dados em grande escala     |
| Edições do DSVM com suporte      | Linux <br /> Windows (experimental)      |
| Usos típicos      | * Rápido desenvolvimento de aplicativos Spark/PySpark localmente com o menor conjunto de dados e posterior implantação em grandes clusters do Spark, como HDInsight do Azure<br/> * Teste o contexto do Microsoft R Server Spark <br />* Use a biblioteca [MMLSpark](https://github.com/Azure/mmlspark) de software livre da Microsoft ou do SparkML para criar aplicativos ML  |
| Links para exemplos      |    Exemplo de Jupyter: <br />&nbsp;&nbsp;* ~/notebooks/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/notebooks/MMLSpark <br /> Microsoft R Server (contexto do Spark): /dsvm/samples/MRS/MRSSparkContextSample.R |
| Ferramentas relacionadas ao DSVM       | PySpark, Scala<br/>Jupyter (kernels Spark/PySpark)<br/>Microsoft R Server, SparkR, Sparklyr <br />Análise do Apache      |

### <a name="how-to-use-it"></a>Como usar
Você pode executar o Spark enviando trabalhos do Spark na linha de comando com comandos `spark-submit` ou `pyspark`. Você também pode criar um bloco de anotações do Jupyter ao criar um novo bloco de anotações com o kernel Spark. 

Você pode usar Spark do R usando bibliotecas como SparkR, Sparklyr ou Microsoft R Server que estão disponíveis na DSVM. Consulte ponteiros para exemplos na tabela anterior. 

> [!NOTE]
> A execução do Microsoft R Server no contexto do Spark da DSVM só tem suporte na edição da DSVM Linux Ubuntu. 



### <a name="setup"></a>Configuração
Antes de executar no contexto do Spark no Microsoft R Server, na edição da DSVM Linux Ubuntu, é preciso realizar uma etapa de configuração única para habilitar uma instância local de HDFS Hadoop e Yarn de nó único. Por padrão, os serviços do Hadoop serão instalados, mas desabilitados no DSVM. Para habilitá-los, execute os seguintes comandos como raiz na primeira vez:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

Você pode interromper os serviços Hadoop relacionados quando não precisar mais deles executando ````systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```` Um exemplo que demonstra como desenvolver e testar o MRS no contexto de Spark remoto (que é a instância de Spark autônoma no DSVM) é apresentado e disponibilizado no diretório `/dsvm/samples/MRS`. 


### <a name="how-is-it-configured--installed-on-the-dsvm"></a>Como é configurado/instalado no DSVM? 
|Plataforma|Local de instalação ($SPARK_HOME)|
|:--------|:--------|
|Windows | c:\dsvm\tools\spark-X.X.X-bin-hadoopX.X|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Bibliotecas para acessar dados do Blob do Azure ou do armazenamento do ADLS (Azure Data Lake) e usando bibliotecas de aprendizado de máquina do MMLSpark da Microsoft são pré-instaladas em $SPARK_HOME/jars. Esses JARs são carregados automaticamente quando o Spark é inicializado. Por padrão, o Spark usa os dados no disco local. Para a instância do Spark na DSVM acessar dados armazenados em blobs do Azure ou ADLS, é necessário criar/configurar o arquivo `core-site.xml` baseado no modelo encontrado em $SPARK_HOME/conf/core-site.xml.template (em que há espaços reservados para configurações de Blob e ADLS) com as credenciais apropriadas para blob do Azure e Armazenamento do Azure Data Lake. Encontre etapas mais detalhadas sobre como criar as credenciais de serviço do ADLS [aqui](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory#create-an-active-directory-application). Depois que as credenciais para o blob do Azure ou ADLS são inseridas no arquivo core-site.xml, você pode consultar os dados armazenados nessas fontes com o prefixo do URI de wasb:// ou adl://. 


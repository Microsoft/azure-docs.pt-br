---
title: Plataformas de dados com compatíveis
titleSuffix: Azure Data Science Virtual Machine
description: Saiba mais sobre as plataformas de dados e as ferramentas com suporte para o Máquina Virtual de Ciência de Dados do Azure.
keywords: ferramentas de ciência de dados, máquina virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 927e945a0d045abcd1caa2951dbd484224b2f425
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519534"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Plataformas de dados compatíveis com a Máquina Virtual de Ciência de Dados

Com um Máquina Virtual de Ciência de Dados (DSVM), você pode criar sua análise em uma ampla variedade de plataformas de dados. Além das interfaces para plataformas de dados remotas, a DSVM oferece uma instância local para rápido desenvolvimento e criação de protótipos.

As ferramentas de plataforma de dados a seguir têm suporte no DSVM.

## <a name="sql-server-developer-edition"></a>SQL Server Developer Edition

| Categoria | Valor |
| ------------- | ------------- |
| O que é?   | Uma instância de banco de dados relacional local      |
| Edições DSVM com suporte      | Windows 2016: SQL Server 2017, Windows 2019: SQL Server 2019      |
| Usos típicos      | <ul><li>Desenvolvimento rápido localmente com o menor conjunto de dados</li><li>Executar R no banco de dados</li></ul> |
| Links para exemplos      | <ul><li>Um pequeno exemplo de um conjunto de dados de cidade de Nova York é carregado no SQL Database:<br/>  `nyctaxi`</li><li>O exemplo de Jupyter mostrando Microsoft Machine Learning Server e análise no banco de dados pode ser encontrado em:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`</li></ul> |
| Ferramentas relacionadas no DSVM       | <ul><li>O SQL Server Management Studio</li><li>Drivers ODBC/JDBC</li><li>pyodbc, RODBC</li><li>Análise do Apache</li></ul> |

> [!NOTE]
> A edição SQL Server Developer pode ser usada somente para fins de desenvolvimento e teste. Você precisa de uma licença ou de uma das VMs do SQL Server para executá-lo em produção.


### <a name="setup"></a>Instalação

O servidor de banco de dados já está pré-configurado e os serviços do Windows relacionados ao SQL Server (como `SQL Server (MSSQLSERVER)` ) são definidos para serem executados automaticamente. A única etapa manual envolve habilitar a análise no banco de dados usando Microsoft Machine Learning Server. Você pode habilitar a análise executando o comando a seguir como uma ação única no SQL Server Management Studio (SSMS). Execute este comando depois de fazer logon como o administrador do computador, abra uma nova consulta no SSMS e verifique se o banco de dados selecionado é `master` :

```sql
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 
```

(Substitua% ComputerName% pelo nome da VM.)

Para executar SQL Server Management Studio, você pode pesquisar por "SQL Server Management Studio" na lista de programas ou usar o Windows Search para localizá-lo e executá-lo. Quando solicitado a fornecer credenciais, selecione **autenticação do Windows** e use o nome do computador ou ```localhost``` no campo **nome do SQL Server** .

### <a name="how-to-use-and-run-it"></a>Como usá-lo e executá-lo

Por padrão, o servidor de banco de dados com a instância de banco de dados padrão é executado automaticamente. Você pode usar ferramentas como o SQL Server Management Studio na VM para acessar o banco de dados do SQL Server localmente. As contas de administrador local têm acesso de administrador no banco de dados.

Além disso, o DSVM vem com os drivers ODBC e JDBC para se comunicar com SQL Server, bancos de dados SQL do Azure e análise de Synapse do Azure de aplicativos escritos em várias linguagens, incluindo Python e Machine Learning Server.

### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Como ele é configurado e instalado no DSVM? 

 O SQL Server é instalado da maneira padrão. Ele pode ser encontrado em `C:\Program Files\Microsoft SQL Server`. A instância Machine Learning Server no banco de dados é encontrada em `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES` . O DSVM também tem uma instância de Machine Learning Server autônoma separada, que é instalada em `C:\Program Files\Microsoft\R Server\R_SERVER` . Essas duas instâncias de Machine Learning Server não compartilham bibliotecas.


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (autônomo)

| Categoria | Valor |
| ------------- | ------------- |
| O que é?   | Uma instância autônoma (nó único em processo) da plataforma popular do Apache Spark; um sistema para processamento de dados rápido e em grande escala e aprendizado de máquina     |
| Edições DSVM com suporte      | Linux     |
| Usos típicos      | <ul><li>Desenvolvimento rápido de aplicativos Spark/PySpark localmente com um DataSet menor e implantação posterior em clusters Spark grandes, como o Azure HDInsight</li><li>Testar Microsoft Machine Learning Server contexto do Spark</li><li>Use o SparkML ou a biblioteca [MMLSpark](https://github.com/Azure/mmlspark) de software livre da Microsoft para criar aplicativos ml</li></ul> |
| Links para exemplos      |    Exemplo de Jupyter:<ul><li>~/notebooks/SparkML/pySpark</li><li>~/notebooks/MMLSpark</li></ul><p>Microsoft Machine Learning Server (contexto do Spark):/dsvm/samples/MRS/MRSSparkContextSample.R</p> |
| Ferramentas relacionadas no DSVM       | <ul><li>PySpark, Scala</li><li>Jupyter (kernels Spark/PySpark)</li><li>Microsoft Machine Learning Server, Sparkr, Sparklyr</li><li>Análise do Apache</li></ul> |

### <a name="how-to-use-it"></a>Como usá-lo
Você pode enviar trabalhos do Spark na linha de comando executando o `spark-submit` `pyspark` comando ou. Você também pode criar um bloco de anotações do Jupyter ao criar um novo bloco de anotações com o kernel Spark.

Você pode usar o Spark do R usando bibliotecas como Sparkr, Sparklyr e Microsoft Machine Learning Server, que estão disponíveis no DSVM. Consulte ponteiros para exemplos na tabela anterior.

### <a name="setup"></a>Instalação
Antes de executar em um contexto do Spark em Microsoft Machine Learning Server na edição Ubuntu Linux DSVM, você deve concluir uma etapa de configuração única para habilitar um HDFS e uma instância yarn do Hadoop de nó único local. Por padrão, os serviços do Hadoop serão instalados, mas desabilitados no DSVM. Para habilitá-los, execute os seguintes comandos como raiz na primeira vez:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Você pode interromper os serviços relacionados ao Hadoop quando não precisar mais deles executando ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn``` .

Um exemplo que demonstra como desenvolver e testar o Sra em um contexto do Spark remoto (que é a instância autônoma do Spark no DSVM) é fornecido e está disponível no `/dsvm/samples/MRS` diretório.


### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Como ele é configurado e instalado no DSVM? 
|Plataforma|Local de instalação ($SPARK_HOME)|
|:--------|:--------|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


As bibliotecas para acessar dados do armazenamento de BLOBs do Azure ou Azure Data Lake Storage, usando as bibliotecas de aprendizado de máquina do Microsoft MMLSpark, são pré-instalados no $SPARK _HOME/JARs. Esses JARs são carregados automaticamente quando o Spark é inicializado. Por padrão, o Spark usa dados no disco local. 

Para que a instância do Spark no DSVM acesse dados armazenados no armazenamento de BLOBs ou Azure Data Lake Storage, você deve criar e configurar o `core-site.xml` arquivo com base no modelo encontrado em $SPARK _HOME/conf/core-site.xml. Template. Você também deve ter as credenciais apropriadas para acessar o armazenamento de BLOBs e Azure Data Lake Storage. (Observe que os arquivos de modelo usam espaços reservados para o armazenamento de BLOBs e configurações de Azure Data Lake Storage.)

Para obter informações mais detalhadas sobre como criar Azure Data Lake Storage credenciais de serviço, consulte [autenticação com Azure data Lake Storage Gen1](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md). Depois que as credenciais para armazenamento de BLOB ou Azure Data Lake Storage são inseridas no arquivo core-site.xml, você pode fazer referência aos dados armazenados nessas fontes por meio do prefixo URI de wasb://ou adl://.
---
title: Hive Warehouse Connector – Apache Zeppelin usando Livy - Azure HDInsight
description: Saiba como integrar o Hive Warehouse Connector ao Apache Zeppelin no Azure HDInsight.
author: nis-goel
ms.author: nisgoel
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/28/2020
ms.openlocfilehash: 4859ff098bf3fdffbecc70608cb147d17d9d7f59
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98941158"
---
# <a name="integrate-apache-zeppelin-with-hive-warehouse-connector-in-azure-hdinsight"></a>Integrar o Apache Zeppelin com o Hive Warehouse Connector no Azure HDInsight

Os clusters do HDInsight Spark incluem notebooks do Apache Zeppelin com interpretadores diferentes. Neste artigo, vamos nos concentrar apenas no intérprete Livy para acessar tabelas do Hive do Spark usando o Hive Warehouse Connector.

> [!NOTE]
> Este artigo contém referências ao termo *lista de permissões*, um termo que a Microsoft não usa mais. Quando o termo for removido do software, também o removeremos deste artigo.

## <a name="prerequisite"></a>Pré-requisito

Conclua as etapas da [instalação do Hive Warehouse Connector](apache-hive-warehouse-connector.md#hive-warehouse-connector-setup).

## <a name="getting-started"></a>Introdução

1. Use o [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se conectar ao cluster do Apache Spark. Edite o comando abaixo substituindo CLUSTERNAME pelo nome do cluster e, em seguida, insira o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Em sua sessão ssh, execute o seguinte comando para anotar as versões de `hive-warehouse-connector-assembly` e `pyspark_hwc`:

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

    Salve o resultado para uso posterior ao configurar o Apache Zeppelin.

## <a name="configure-livy"></a>Configurar o Livy

É necessário seguir as configurações para acessar tabelas do Hive do Zeppelin com o interpretador do Livy.

### <a name="interactive-query-cluster"></a>Cluster de Consulta Interativa

1. Em um navegador da Web, navegue para `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HDFS/configs`, onde LLAPCLUSTERNAME é o nome do cluster de Consulta Interativa.

1. Navegue até **Site de núcleo personalizado** > **avançado**. Selecione **Adicionar propriedade...** para adicionar as seguintes configurações:

    | Configuração                 | Valor |
    | ----------------------------- |-------|
    | hadoop.proxyuser.livy.groups  | *     |
    | hadoop.proxyuser.livy.hosts   | *     |

1. Salve as alterações e reinicie todos os componentes afetados.

### <a name="spark-cluster"></a>Cluster do Spark

1. Em um navegador da Web, navegue para `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs`, onde CLUSTERNAME é o nome do cluster do Apache Spark.

1. Expanda **livy2-conf personalizado**. Selecione **Adicionar propriedade...** para adicionar as seguintes configurações:

    | Configuração                 | Valor                                      |
    | ----------------------------- |------------------------------------------  |
    | livy.file.local-dir-whitelist | /usr/hdp/current/hive_warehouse_connector/ |

1. Salve as alterações e reinicie todos os componentes afetados.

### <a name="configure-livy-interpreter-in-zeppelin-ui-spark-cluster"></a>Configurar o interpretador Livy na interface do usuário do Zeppelin (cluster do Spark)

1. Em um navegador da Web, navegue para `https://CLUSTERNAME.azurehdinsight.net/zeppelin/#/interpreter`, onde `CLUSTERNAME` é o nome do cluster do Apache Spark.

1. Navegue até o **livy2**.

1. Adicione as seguintes configurações:

    | Configuração                 | Valor                                      |
    | ----------------------------- |:------------------------------------------:|
    | livy.spark.hadoop.hive.llap.daemon.service.hosts | @llap0 |
    | livy.spark.security.credentials.hiveserver2.enabled | true |
    | livy.spark.sql.hive.llap | true |
    | livy.spark.yarn.security.credentials.hiveserver2.enabled | true |
    | livy.superusers | livy,zeppelin |
    | livy.spark.jars | `file:///usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-VERSION.jar`.<br>Substitua VERSION pelo valor obtido de [Introdução](#getting-started), anteriormente. |
    | livy.spark.submit.pyFiles | `file:///usr/hdp/current/hive_warehouse_connector/pyspark_hwc-VERSION.zip`.<br>Substitua VERSION pelo valor obtido de [Introdução](#getting-started), anteriormente. |
    | livy.spark.sql.hive.hiveserver2.jdbc.url | Defina-o como a URL JDBC interativa do HiveServer2 do cluster de Consulta Interativa. |
    | spark.security.credentials.hiveserver2.enabled | true |

1. Somente em clusters do ESP, adicione a seguinte configuração:

    | Configuração| Valor|
    |---|---|
    | livy.spark.sql.hive.hiveserver2.jdbc.url.principal | `hive/<llap-headnode>@<AAD-Domain>` |

    * Em um navegador da Web, navegue até `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary` onde ClusterName é o nome do seu cluster de consulta interativa. Clique em **HiveServer2 Interactive**. Você verá o FQDN (nome de domínio totalmente qualificado) do nó principal no qual o LLAP está em execução, conforme mostrado na captura de tela. Substituir `<llap-headnode>` pelo valor.

        ![Nó principal do conector do depósito do hive](./media/apache-hive-warehouse-connector/head-node-hive-server-interactive.png)

    * Use o [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se conectar ao seu cluster de consulta interativa. Procure `default_realm` o parâmetro no `/etc/krb5.conf` arquivo. Substitua `<AAD-DOMAIN>` por esse valor como uma cadeia de caracteres em maiúsculas, caso contrário, a credencial não será encontrada.

        ![Domínio AAD do conector do depósito do hive](./media/apache-hive-warehouse-connector/aad-domain.png)

    * Por exemplo, `hive/hn0-ng36ll.mjry42ikpruuxgs2qy2kpg4q5e.cx.internal.cloudapp.net@PKRSRVUQVMAE6J85.D2.INTERNAL.CLOUDAPP.NET` .

1. Salve as alterações e reinicie o interpretador do Livy.

Se o interpretador do Livy não estiver acessível, modifique o arquivo `shiro.ini` presente no componente Zeppelin no Ambari. Para obter mais informações, consulte [Configurar a segurança do Apache Zeppelin](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.0.1/configuring-zeppelin-security/content/enabling_access_control_for_interpreter__configuration__and_credential_settings.html).  


## <a name="running-queries-in-zeppelin"></a>Executar consultas no Zeppelin 

Inicie um notebook do Zeppelin usando o interpretador do Livy e execute o seguinte

```python
%livy2

import com.hortonworks.hwc.HiveWarehouseSession
import com.hortonworks.hwc.HiveWarehouseSession._
import org.apache.spark.sql.SaveMode

# Initialize the hive context
val hive = HiveWarehouseSession.session(spark).build()

# Create a database
hive.createDatabase("hwc_db",true)
hive.setDatabase("hwc_db")

# Create a Hive table
hive.createTable("testers").ifNotExists().column("id", "bigint").column("name", "string").create()

val dataDF = Seq( (1, "foo"), (2, "bar"), (8, "john")).toDF("id", "name")

# Validate writes to the table
dataDF.write.format("com.hortonworks.spark.sql.hive.llap.HiveWarehouseConnector").mode("append").option("table", "hwc_db.testers").save()

# Validate reads
hive.executeQuery("select * from testers").show()

```

## <a name="next-steps"></a>Próximas etapas

* [Operações do HWC e do Apache Spark](./apache-hive-warehouse-connector-operations.md)
* [Integração do HWC com o Apache Spark e o Apache Hive](./apache-hive-warehouse-connector.md)
* [Usar a Consulta Interativa com o HDInsight](./apache-interactive-query-get-started.md)

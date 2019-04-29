---
title: Proteger os fluxos de trabalho do Apache Oozie com o Enterprise Security Package – Azure HDInsight
description: Proteja os fluxos de trabalho do Apache Oozie usando o Enterprise Security Package do Azure HDInsight. Saiba como definir um fluxo de trabalho do Oozie e enviar um trabalho do Oozie.
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: mamccrea
ms.custom: hdinsightactive,seodec18
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: 7d7fbf5d72654c26edf09ab27f024eaf39f8c387
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62112878"
---
# <a name="run-apache-oozie-in-hdinsight-hadoop-clusters-with-enterprise-security-package"></a>Executar o Apache Oozie em clusters do HDInsight Hadoop com o Enterprise Security Package

O Apache Oozie é um sistema de fluxo de trabalho e coordenação que gerencia os trabalhos do Apache Hadoop. O Oozie é integrado à pilha do Hadoop e dá suporte aos seguintes trabalhos:
- Apache MapReduce
- Apache Pig
- Apache Hive
- Apache Sqoop

Você também pode usar o Oozie para agendar trabalhos específicos para um sistema, como programas Java ou scripts de shell.

## <a name="prerequisite"></a>Pré-requisito

- Um cluster do Azure HDInsight Hadoop com o ESP (Enterprise Security Package). Confira [Configurar clusters do HDInsight com ESP](./apache-domain-joined-configure-using-azure-adds.md).

    > [!NOTE]  
    > Para obter instruções detalhadas sobre como usar o Oozie em clusters que não são do ESP, confira [Usar fluxos de trabalho do Apache Oozie do Hadoop no Azure HDInsight baseado em Linux](../hdinsight-use-oozie-linux-mac.md).

## <a name="connect-to-an-esp-cluster"></a>Conectar a um cluster ESP

Para obter mais informações sobre Secure Shell (SSH), consulte [Conectar ao HDInsight (Hadoop) usando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Conecte-se ao cluster HDInsight usando SSH:  
   ```bash
   ssh [DomainUserName]@<clustername>-ssh.azurehdinsight.net
   ```

2. Para verificar se a autenticação Kerberos foi bem-sucedida, use o comando `klist`. Caso contrário, use `kinit` para iniciar a autenticação Kerberos.

3. Faça logon no gateway de HDInsight para registrar o token OAuth necessário para acessar o ADLS (Azure Data Lake Store):   
     ```bash
     curl -I -u [DomainUserName@Domain.com]:[DomainUserPassword] https://<clustername>.azurehdinsight.net
     ```

    Um código de resposta de status **200 OK** indica um registro bem-sucedido. Verifique o nome de usuário e a senha, caso uma resposta não autorizada seja recebida, como 401.

## <a name="define-the-workflow"></a>Definir o fluxo de trabalho
As definições de fluxos de trabalho do Oozie estão gravadas em Apache Hadoop Process Definition Language (hPDL). hPDL é uma linguagem de definição de processo XML. Realize as etapas a seguir para definir o fluxo de trabalho:

1. Configurando um workspace de um usuário de domínio:
   ```bash
   hdfs dfs -mkdir /user/<DomainUser>
   cd /home/<DomainUserPath>
   cp /usr/hdp/<ClusterVersion>/oozie/doc/oozie-examples.tar.gz .
   tar -xvf oozie-examples.tar.gz
   hdfs dfs -put examples /user/<DomainUser>/
   ```
   Substitua `DomainUser` pelo nome de usuário de domínio. 
   Substitua `DomainUserPath` pelo caminho do diretório base do usuário de domínio. 
   Substitua `ClusterVersion` pela sua versão do Hortonworks Data Platform (HDP) de cluster.

2. Use a instrução a seguir para criar e editar um novo arquivo:
   ```bash
   nano workflow.xml
   ```

3. Quando o editor nano for aberto, insira o seguinte XML como conteúdo do arquivo:
   ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <workflow-app xmlns="uri:oozie:workflow:0.4" name="map-reduce-wf">
       <credentials>
          <credential name="metastore_token" type="hcat">
             <property>
                <name>hcat.metastore.uri</name>
                <value>thrift://hn0-<clustername>.<Domain>.com:9083</value>
             </property>
             <property>
                <name>hcat.metastore.principal</name>
                <value>hive/_HOST@<Domain>.COM</value>
             </property>
          </credential>
          <credential name="hs2-creds" type="hive2">
             <property>
                <name>hive2.server.principal</name>
                <value>${jdbcPrincipal}</value>
             </property>
             <property>
                <name>hive2.jdbc.url</name>
                <value>${jdbcURL}</value>
             </property>
          </credential>
       </credentials>
       <start to="mr-test" />
       <action name="mr-test">
          <map-reduce>
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <prepare>
                <delete path="${nameNode}/user/${wf:user()}/examples/output-data/mrresult" />
             </prepare>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
                <property>
                   <name>mapred.mapper.class</name>
                   <value>org.apache.oozie.example.SampleMapper</value>
                </property>
                <property>
                   <name>mapred.reducer.class</name>
                   <value>org.apache.oozie.example.SampleReducer</value>
                </property>
                <property>
                   <name>mapred.map.tasks</name>
                   <value>1</value>
                </property>
                <property>
                   <name>mapred.input.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/input-data/text</value>
                </property>
                <property>
                   <name>mapred.output.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/output-data/mrresult</value>
                </property>
             </configuration>
          </map-reduce>
          <ok to="myHive2" />
          <error to="fail" />
       </action>
       <action name="myHive2" cred="hs2-creds">
          <hive2 xmlns="uri:oozie:hive2-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <jdbc-url>${jdbcURL}</jdbc-url>
             <script>${hiveScript2}</script>
             <param>hiveOutputDirectory2=${hiveOutputDirectory2}</param>
          </hive2>
          <ok to="myHive" />
          <error to="fail" />
       </action>
       <action name="myHive" cred="metastore_token">
          <hive xmlns="uri:oozie:hive-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
             </configuration>
             <script>${hiveScript1}</script>
             <param>hiveOutputDirectory1=${hiveOutputDirectory1}</param>
          </hive>
          <ok to="end" />
          <error to="fail" />
       </action>
       <kill name="fail">
          <message>Oozie job failed, error message[${wf:errorMessage(wf:lastErrorNode())}]</message>
       </kill>
       <end name="end" />
    </workflow-app>
   ```
4. Substitua `clustername` pelo nome do cluster. 

5. Para salvar o arquivo, selecione Ctrl+X. Digite `Y`. Em seguida, selecione **Enter**.

    O fluxo de trabalho é dividido em duas partes:
   * **Seção de credencial.** Essa seção de credenciais usa as credenciais que serão usadas para autenticar as ações do Oozie:

     Esse exemplo usa autenticação para as ações do Hive. Para saber mais, veja [Autenticação de Usuário](https://oozie.apache.org/docs/4.2.0/DG_ActionAuthentication.html).

     O serviço de credenciais permite que as ações do Oozie representem o usuário para acessar os serviços do Hadoop.

   * **Seção de ação.** Esta seção tem três ações: redução de mapa, Hive server 2 e o servidor Hive 1:

     - A ação do map-reduce executa um exemplo de um pacote Oozie para o map-reduce que sai da contagem de palavra agregada.

     - As ações do servidor do Hive 2 e do servidor do Hive 1 executam uma consulta simples na tabela hivesample fornecida com o HDInsight.

     As ações do Hive usam as credenciais definidas na seção de credenciais para autenticação usando a palavra-chave `cred` no elemento action.

6. Use o seguinte comando para copiar o arquivo `workflow.xml` para `/user/<domainuser>/examples/apps/map-reduce/workflow.xml`:
     ```bash
    hdfs dfs -put workflow.xml /user/<domainuser>/examples/apps/map-reduce/workflow.xml
     ```

7. Substitua `domainuser` por seu nome de usuário para o domínio.

## <a name="define-the-properties-file-for-the-oozie-job"></a>Definir o arquivo de propriedades para o trabalho do Oozie

1. Use a seguinte instrução para criar e editar as propriedades de trabalho de um novo arquivo:

   ```bash
   nano job.properties
   ```

2. Quando o editor nano for aberto, use o seguinte XML como conteúdo do arquivo:

   ```bash
       nameNode=adl://home
       jobTracker=headnodehost:8050
       queueName=default
       examplesRoot=examples
       oozie.wf.application.path=${nameNode}/user/[domainuser]/examples/apps/map-reduce/workflow.xml
       hiveScript1=${nameNode}/user/${user.name}/countrowshive1.hql
       hiveScript2=${nameNode}/user/${user.name}/countrowshive2.hql
       oozie.use.system.libpath=true
       user.name=[domainuser]
       jdbcPrincipal=hive/hn0-<ClusterShortName>.<Domain>.com@<Domain>.COM
       jdbcURL=[jdbcurlvalue]
       hiveOutputDirectory1=${nameNode}/user/${user.name}/hiveresult1
       hiveOutputDirectory2=${nameNode}/user/${user.name}/hiveresult2
   ```

   * Use o URI `adl://home` para a propriedade `nameNode` se você tiver o Azure Data Lake Storage Gen1 como armazenamento de cluster principal. Se você estiver usando o Armazenamento de Blobs do Azure, altere isso para `wasb://home`. Se você estiver usando o Azure Data Lake Storage Gen2, altere isso para `abfs://home`.
   * Substitua `domainuser` por seu nome de usuário para o domínio.  
   * Substitua `ClusterShortName` pelo nome curto para o cluster. Por exemplo, se o nome do cluster for https:// *[link do exemplo]* sechadoopcontoso.azurehdisnight.net, o `clustershortname` é os primeiro de seis caracteres do cluster: **sechad**.  
   * Substitua `jdbcurlvalue` pela URL JDBC da configuração do Hive. Por exemplo, jdbc:hive2://headnodehost:10001/;transportMode=http.      
   * Para salvar o arquivo, selecione Ctrl+X, digite `Y`e selecione **Enter**.

   Esse arquivo de propriedades precisa estar presente localmente durante a execução de trabalhos do Oozie.

## <a name="create-custom-hive-scripts-for-oozie-jobs"></a>Crie scripts personalizados do Hive para os trabalhos do Oozie

Você pode criar os dois scripts de Hive para o servidor Hive 1 e Servidor Hive 2 conforme exibido nas seções a seguir.

### <a name="hive-server-1-file"></a>Arquivo do Servidor do Hive 1

1.  Criar e editar um arquivo para ações de servidor Hive 1:
    ```bash
    nano countrowshive1.hql
    ```

2.  Criar o script:
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemake from hivesampletable limit 2;
    ```

3.  Salve o arquivo para o Sistema de Arquivos Distribuído Apache Hadoop (HDFS):
    ```bash
    hdfs dfs -put countrowshive1.hql countrowshive1.hql
    ```

### <a name="hive-server-2-file"></a>Arquivo do Servidor do Hive 2

1.  Criar e editar um campo para ações do Hive server 2:
    ```bash
    nano countrowshive2.hql
    ```

2.  Criar o script:
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemodel from hivesampletable limit 2;
    ```

3.  Salvar o arquivo no HDFS:
    ```bash
    hdfs dfs -put countrowshive2.hql countrowshive2.hql
    ```

## <a name="submit-oozie-jobs"></a>Enviar trabalhos de Oozie

O envio de trabalhos do Oozie para clusters ESP é semelhante ao envio de trabalhos do Oozie em clusters que não são ESP.

Para obter mais informações, consulte [Usar o Apache Oozie com o Apache Hadoop para definir e executar um fluxo de trabalho no Azure HDInsight baseado no Linux](../hdinsight-use-oozie-linux-mac.md).

## <a name="results-from-an-oozie-job-submission"></a>Resultados do envio de trabalhos do Oozie
Os trabalhos do Oozie são executados para o usuário. Então, o Apache Hadoop YARN e o Apache Ranger auditam logs mostram os trabalhos que estão sendo executados como o usuário representado. A saída de interface de linha de comando de um trabalho do Oozie se parece com o código a seguir:



```bash
    Job ID : 0000015-180626011240801-oozie-oozi-W
    ------------------------------------------------------------------------------------------------
    Workflow Name : map-reduce-wf
    App Path      : adl://home/user/alicetest/examples/apps/map-reduce/wf.xml
    Status        : SUCCEEDED
    Run           : 0
    User          : alicetest
    Group         : -
    Created       : 2018-06-26 19:25 GMT
    Started       : 2018-06-26 19:25 GMT
    Last Modified : 2018-06-26 19:30 GMT
    Ended         : 2018-06-26 19:30 GMT
    CoordAction ID: -
    
    Actions
    ------------------------------------------------------------------------------------------------
    ID                      Status  Ext ID          ExtStatus   ErrCode
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@:start:    OK  -           OK      -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@mr-test    OK  job_1529975666160_0051  SUCCEEDED   -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@myHive2    OK  job_1529975666160_0053  SUCCEEDED   -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@myHive OK  job_1529975666160_0055  SUCCEEDED   -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@end    OK  -           OK      -
    -----------------------------------------------------------------------------------------------
```

Os logs de auditoria do Ranger para as ações do servidor do Hive 2 mostram o Oozie executando a ação para o usuário. As exibições do Ranger e do YARN só estão visíveis para o administrador do cluster.

## <a name="configure-user-authorization-in-oozie"></a>Configurar a autorização de usuário no Oozie

O Oozie por si só tem uma configuração de autorização do usuário, que pode impedir a interrupção dos usuários, encerrando os trabalhos de outro usuário. Para habilitar essa configuração, defina as `oozie.service.AuthorizationService.security.enabled` para `true`. 

Para obter mais informações, consulte [Instalação e Configuração do Apache Oozie](https://oozie.apache.org/docs/3.2.0-incubating/AG_Install.html).

Para componentes como o servidor do Hive 1, no qual o plug-in do Ranger não está disponível ou é compatível, é possível apenas realizar a autorização de alta granularidade do HDFS. A autorização refinada está disponível por meio dos plug-ins do Ranger.

## <a name="get-the-oozie-web-ui"></a>Obtenha a IU web do Oozie

A IU da Web do Oozie fornece um modo de exibição baseado na web sobre o status dos trabalhos do Oozie no cluster. Para obter a interface do usuário da Web, execute as seguintes etapas em clusters ESP:

1. Adicionar um [nó de borda](../hdinsight-apps-use-edge-node.md) e habilitar a [autenticação SSH Kerberos](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Siga as etapas da [interface do usuário da Web do Oozie](../hdinsight-use-oozie-linux-mac.md) para habilitar o túnel SSH no nó de borda e acessar a interface do usuário da Web.

## <a name="next-steps"></a>Próximas etapas
* [Usar o Apache Oozie com o Apache Hadoop para definir e executar um fluxo de trabalho no Azure HDInsight baseado no Linux](../hdinsight-use-oozie-linux-mac.md).
* [Conectar ao HDInsight (Apache Hadoop) usando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

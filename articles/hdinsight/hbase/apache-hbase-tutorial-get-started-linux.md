---
title: 'Tutorial: Usar o Apache HBase no Azure HDInsight'
description: Siga este tutorial do Apache HBase para começar a usar o hadoop no HDInsight. Criar tabelas a partir do shell do HBase e consultá-las usando o Hive.
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 01/22/2021
ms.openlocfilehash: 05e40dd38fc7111521b600908cda38084249e4de
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98936062"
---
# <a name="tutorial-use-apache-hbase-in-azure-hdinsight"></a>Tutorial: Usar o Apache HBase no Azure HDInsight

Este tutorial demonstra como criar um cluster do Apache HBase no Azure HDInsight, criar tabelas do HBase e consultar tabelas usando o Apache Hive.  Para saber mais sobre o HBase, confira [Visão geral do HBase do HDInsight](./apache-hbase-overview.md).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar cluster do Apache HBase
> * Criar tabelas do HBase e inserir dados
> * Usar o Apache Hive para consultar o Apache HBase
> * Usar APIs de REST do HBase usando Curl
> * Verificar o status do cluster

## <a name="prerequisites"></a>Pré-requisitos

* Um cliente SSH. Para saber mais, confira [Conectar-se ao HDInsight (Apache Hadoop) usando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Bash. Os exemplos neste artigo usam o shell Bash no Windows 10 para os comandos de cURL. Confira o [Guia de instalação do subsistema do Windows para Linux para o Windows 10](/windows/wsl/install-win10) para conhecer as etapas de instalação.  Outros [shells do Unix](https://www.gnu.org/software/bash/) também funcionarão.  Os exemplos de cURL, com algumas pequenas modificações, podem funcionar em um prompt de comando do Windows.  Ou é possível usar o cmdlet do Windows PowerShell [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod).

## <a name="create-apache-hbase-cluster"></a>Criar cluster do Apache HBase

O procedimento a seguir usa um modelo do Azure Resource Manager para criar um cluster HBase. O modelo também cria a conta de Armazenamento do Azure padrão dependente. Para compreender os parâmetros usados no procedimento e em outros métodos de criação de cluster, consulte [Criar clusters Hadoop baseados em Linux no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

1. Selecione a imagem a seguir para abrir o modelo no portal do Azure. O modelo está localizado em [Modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-tutorial-get-started-linux/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Na caixa de diálogo **Implantação personalizada**, insira os seguintes valores:

    |Propriedade |Descrição |
    |---|---|
    |Subscription|Selecione sua assinatura do Azure que é usada para criar o cluster.|
    |Resource group|Crie um grupo de gerenciamento de recursos do Azure ou use um existente.|
    |Location|Especifique o local do grupo de recursos. |
    |ClusterName|Insira um nome para o cluster HBase.|
    |Nome e senha de logon do cluster|O nome padrão de logon é **admin**.|
    |Nome de usuário e senha SSH|O nome de usuário padrão é **sshuser**.|

    Outros parâmetros são opcionais.  

    Cada cluster tem uma dependência de conta de Armazenamento do Azure. Depois que você excluir um cluster, os dados ficarão na conta de armazenamento. O nome de conta de armazenamento padrão do cluster é o nome do cluster com "store" acrescentado. Eles são embutidos no código na seção de variáveis do modelo.

3. Selecione **Concordo com os termos e condições declarados acima** e selecione **Comprar**. Demora cerca de 20 minutos para criar um cluster.

Depois que um cluster HBase for excluído, você pode criar outro cluster HBase usando o mesmo contêiner de blob padrão. O novo cluster seleciona as tabelas HBase criadas por você no cluster original. É recomendável desabilitar as tabelas HBase antes de excluir o cluster para evitar inconsistências.

## <a name="create-tables-and-insert-data"></a>Criar tabelas e inserir dados

Você pode usar o SSH para se conectar a clusters do HBase e, em seguida, usar o [Shell do Apache HBase](https://hbase.apache.org/0.94/book/shell.html) para criar tabelas do HBase, inserir dados e consultar dados.

Para a maioria das pessoas, os dados aparecem no formato de tabela:

![Dados de tabela do HBase do HDInsight Apache](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png)

No HBase (uma implementação do [Cloud BigTable](https://cloud.google.com/bigtable/)), os mesmos dados se parecem com:

![Dados de BigTable do HBase do HDInsight Apache](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png)

**Para usar o shell HBase**

1. Use o comando `ssh` para se conectar ao cluster HBase. Edite o comando abaixo substituindo `CLUSTERNAME` pelo nome do cluster e, em seguida, digite o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Use o comando `hbase shell` para iniciar o shell interativo do HBase. Digite o seguinte comando em sua conexão de SSH:

    ```bash
    hbase shell
    ```

1. Use o comando `create` para criar uma tabela do HBase com famílias de duas colunas. Os nomes de coluna e tabela diferenciam maiúsculas de minúsculas. Insira o seguinte comando:

    ```hbaseshell
    create 'Contacts', 'Personal', 'Office'
    ```

1. Use o comando `list` para listar todas as tabelas no HBase. Insira o seguinte comando:

    ```hbase
    list
    ```

1. Use o comando `put` para inserir valores em uma coluna e linha especificadas em uma determinada tabela. Digite os seguintes comandos:

    ```hbaseshell
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

1. Use o comando `scan` para verificar e retornar os dados da tabela `Contacts`. Insira o seguinte comando:

    ```hbase
    scan 'Contacts'
    ```

    ![Shell do Apache Hadoop HBase do HDInsight](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png)

1. Use o comando `get` para buscar o conteúdo de uma linha. Insira o seguinte comando:

    ```hbaseshell
    get 'Contacts', '1000'
    ```

    Você verá resultados semelhantes usando o comando `scan` porque há apenas uma linha.

    Para saber mais sobre o esquema da tabela HBase, confira [Introdução ao projeto de esquema do Apache HBase](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf). Para obter mais comandos HBase, confira [Guia de referência do Apache HBase](https://hbase.apache.org/book.html#quickstart).

1. Use o comando `exit` para interromper o shell interativo do HBase. Insira o seguinte comando:

    ```hbaseshell
    exit
    ```

**Para carregar dados em massa na tabela de contatos HBase**

O HBase inclui vários métodos de carregamento de dados em tabelas.  Para obter mais informações, consulte [Carregamento em massa](https://hbase.apache.org/book.html#arch.bulk.load).

Um arquivo de dados de exemplo pode ser encontrado em um contêiner de blobs público, `wasb://hbasecontacts\@hditutorialdata.blob.core.windows.net/contacts.txt`.  O conteúdo do arquivo de dados é:

`8396    Calvin Raji      230-555-0191    230-555-0191    5415 San Gabriel Dr.`

`16600   Karen Wu         646-555-0113    230-555-0192    9265 La Paz`

`4324    Karl Xie         508-555-0163    230-555-0193    4912 La Vuelta`

`16891   Jonn Jackson     674-555-0110    230-555-0194    40 Ellis St.`

`3273    Miguel Miller    397-555-0155    230-555-0195    6696 Anchor Drive`

`3588    Osa Agbonile     592-555-0152    230-555-0196    1873 Lion Circle`

`10272   Julia Lee        870-555-0110    230-555-0197    3148 Rose Street`

`4868    Jose Hayes       599-555-0171    230-555-0198    793 Crawford Street`

`4761    Caleb Alexander  670-555-0141    230-555-0199    4775 Kentucky Dr.`

`16443   Terry Chander    998-555-0171    230-555-0200    771 Northridge Drive`

Você pode, opcionalmente, criar um arquivo de texto e carregá-lo na sua própria conta de armazenamento se desejar. Para obter instruções, confira [Carregar dados para trabalhos do Apache Hadoop no HDInsight](../hdinsight-upload-data.md).

Este procedimento usa a tabela `Contacts` do HBase que você criou no último procedimento.

1. Na conexão ssh aberta, execute o seguinte comando para transformar o arquivo de dados para StoreFiles e armazene em um caminho relativo especificado por `Dimporttsv.bulk.output`.

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
    ```

2. Execute o seguinte comando para carregar os dados de `/example/data/storeDataFileOutput` na tabela do HBase:

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
    ```

3. Você pode abrir o shell do HBase e usar o comando `scan` para listar o conteúdo da tabela.

## <a name="use-apache-hive-to-query-apache-hbase"></a>Usar o Apache Hive para consultar o Apache HBase

Você pode consultar os dados nas tabelas do HBase usando [Apache Hive](https://hive.apache.org/). Nesta seção você cria uma tabela Hive que faz o mapeamento para a tabela do HBase e usa-a para consultar os dados em sua tabela do HBase.

1. Na sua conexão ssh aberta, use o seguinte comando para iniciar o Beeline:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    Para saber mais sobre o Beeline, consulte [Usar o Hive com Hadoop no HDInsight com Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).

1. Execute o script do [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) a seguir para criar uma tabela Hive que é mapeada para a tabela do HBase. Verifique se você criou a tabela de exemplo referenciada anteriormente neste artigo, usando o shell do HBase antes de executar essa instrução.

    ```hiveql
    CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
    STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
    TBLPROPERTIES ('hbase.table.name' = 'Contacts');
    ```

1. Execute o seguinte script do HiveQL para consultar os dados na tabela do HBase:

    ```hiveql
    SELECT count(rowkey) AS rk_count FROM hbasecontacts;
    ```

1. Para sair do Beeline, use `!exit`.

1. Para sair da sua conexão ssh, use `exit`.

### <a name="separate-hive-and-hbase-clusters"></a>Separar clusters do Hive e do HBase

A consulta de Hive para acessar dados do HBase não precisa ser executada do cluster do HBase. Qualquer cluster fornecido com o Hive (incluindo Spark, Hadoop, HBase ou Interactive Query) pode ser usado para consultar dados do HBase, desde que as seguintes etapas sejam concluídas:

1. Ambos os clusters devem ser anexados à mesma Rede Virtual e Sub-rede
2. Copie `/usr/hdp/$(hdp-select --version)/hbase/conf/hbase-site.xml` dos nós de cabeçalho do cluster do HBase para os nós de cabeçalho do cluster do Hive

### <a name="secure-clusters"></a>Clusters seguros

Os dados do HBase também podem ser consultados no Hive usando o HBase habilitado para ESP: 

1. Ao seguir um padrão com vários clusters, ambos os clusters devem ser habilitados para ESP. 
2. Para permitir que o Hive consulte dados do HBase, verifique se o usuário `hive` recebeu permissões para acessar dados do HBase por meio do plug-in HBase Apache Ranger
3. Ao usar clusters separados habilitados para ESP, o conteúdo de `/etc/hosts` do cluster dos nós de cabeçalho do HBase deve ser acrescentado a `/etc/hosts` dos nós de cabeçalho do cluster do Hive. 
> [!NOTE]
> Após o dimensionamento dos clusters, `/etc/hosts` deve ser acrescentado novamente

## <a name="use-hbase-rest-apis-using-curl"></a>Usar APIs de REST do HBase usando Curl

A API REST é protegida por meio de [autenticação básica](https://en.wikipedia.org/wiki/Basic_access_authentication). Você deve sempre fazer solicitações usando HTTPS (HTTP seguro) para ajudar a garantir que suas credenciais sejam enviadas com segurança para o servidor.

1. Para habilitar as APIs REST do HBase no cluster do HDInsight, adicione o script de inicialização personalizado a seguir à seção **Ação de Script**. Você pode adicionar o script de inicialização ao criar o cluster ou após ele ter sido criado. Para **Tipo de Nó**, selecione **Servidores de Região** para garantir que o script seja executado somente nos Servidores de Região do HBase.


    ```bash
    #! /bin/bash

    THIS_MACHINE=`hostname`

    if [[ $THIS_MACHINE != wn* ]]
    then
        printf 'Script to be executed only on worker nodes'
        exit 0
    fi

    RESULT=`pgrep -f RESTServer`
    if [[ -z $RESULT ]]
    then
        echo "Applying mitigation; starting REST Server"
        sudo python /usr/lib/python2.7/dist-packages/hdinsight_hbrest/HbaseRestAgent.py
    else
        echo "Rest server already running"
        exit 0
    fi
    ```

1. Defina a variável de ambiente para facilitar o uso. Edite os comandos abaixo substituindo `MYPASSWORD` pela senha de logon do cluster. Substitua `MYCLUSTERNAME` pelo nome do cluster do HBase. Em seguida, insira os comandos.

    ```bash
    export password='MYPASSWORD'
    export clustername=MYCLUSTERNAME
    ```

1. Use o seguinte comando para listar as tabelas HBase:

    ```bash
    curl -u admin:$password \
    -G https://$clustername.azurehdinsight.net/hbaserest/
    ```

1. Use o seguinte comando para criar uma nova tabela HBase com famílias de duas colunas:

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/schema" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
    -v
    ```

    O esquema é fornecido no formato JSon.
1. Use o comando a seguir para inserir alguns dados:

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"Row\":[{\"key\":\"MTAwMA==\",\"Cell\": [{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}]}]}" \
    -v
    ```

    Codifique em Base64 os valores especificados na opção -d. No exemplo:

   * MTAwMA==: 1000
   * UGVyc29uYWw6TmFtZQ==: Pessoal: Nome
   * Sm9obiBEb2xl: Julio Dole

     [false-row-key](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) permite inserir diversos valores (em lote).

1. Use o comando a seguir para obter uma linha:

    ```bash
    curl -u admin:$password \
    GET "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/1000" \
    -H "Accept: application/json" \
    -v
    ```

Para saber mais sobre o Rest HBase, veja [Guia de referência do Apache HBase](https://hbase.apache.org/book.html#_rest).

> [!NOTE]  
> Não há suporte para thrift pelo HBase no HDInsight.
>
> Ao usar o Curl ou qualquer outra comunicação do REST com WebHCat, você deve autenticar as solicitações, fornecendo o nome de usuário e a senha para o administrador do cluster HDInsight. Você também deve usar o nome do cluster como parte do Uniform Resource Identifier (URI) usado para enviar as solicitações ao servidor:
>
> `curl -u <UserName>:<Password> \`
>
> `-G https://<ClusterName>.azurehdinsight.net/templeton/v1/status`
>
> Você deve receber uma resposta semelhante à resposta a seguir:
>
> `{"status":"ok","version":"v1"}`

## <a name="check-cluster-status"></a>Verificar o status do cluster

O HBase em HDInsight é fornecido com uma interface do usuário da Web para monitorar clusters. Usando a interface do usuário da Web, você pode solicitar estatísticas ou informações sobre regiões.

**Para acessar a interface do usuário mestre HBase**

1. Entre na interface do usuário do Ambari Web em `https://CLUSTERNAME.azurehdinsight.net`, em que `CLUSTERNAME` é o nome do seu cluster do HBase.

1. Selecione **HBase** no menu à esquerda.

1. Selecione **Links Rápidos** no topo da página, aponte para o link do nó ativo do Zookeeper e, em seguida, selecione **Interface do usuário HBase Master**.  A interface do usuário é aberta em outra guia do navegador:

   ![Interface do usuário do HBase HMaster do HDInsight Apache](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

   A Interface do Usuário Mestre HBase contém as seguintes seções:

   - servidores de região
   - mestres de backup
   - tabelas
   - tarefas
   - atributos de software

## <a name="cluster-recreation"></a>Recriação de clusters

Depois que um cluster HBase for excluído, você pode criar outro cluster HBase usando o mesmo contêiner de blob padrão. O novo cluster seleciona as tabelas HBase criadas por você no cluster original. No entanto, é recomendável desabilitar as tabelas HBase antes de excluir o cluster para evitar inconsistências. 

Use o comando `disable 'Contacts'` do HBase. 

## <a name="clean-up-resources"></a>Limpar os recursos

Se não for continuar usando este aplicativo, exclua o cluster do HBase que você criou seguindo estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com/).
1. Na caixa **Pesquisar** na parte superior, digite **HDInsight**.
1. Selecione **Clusters do HDInsight** em **Serviços**.
1. Na lista de clusters do HDInsight que aparece, clique em **…** ao lado do cluster que você criou para este tutorial.
1. Clique em **Excluir**. Clique em **Sim**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como criar um cluster do Apache HBase. Além disso, também aprendeu a criar tabelas e ver os dados nessas tabelas no shell do HBase. Você também aprendeu a usar uma consulta do Hive dos dados em tabelas do HBase. E aprendeu a usar as APIs REST C# do HBase para criar uma tabela HBase e recuperar dados da tabela. Para obter mais informações, consulte:

> [!div class="nextstepaction"]
> [Visão geral do HDInsight HBase](./apache-hbase-overview.md)

---
title: Dicas para usar Hadoop no HDInsight baseado em Linux
description: Obtenha dicas de implementação para usar clusters do HDInsight baseados em Linux (Hadoop) em um ambiente Linux familiar, em execução na nuvem do Azure.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 2a7af59495966c76a47ea84311ab073eb594f82e
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63765718"
---
# <a name="information-about-using-hdinsight-on-linux"></a>Informações sobre o uso do HDInsight no Linux

Os clusters do Azure HDInsight disponibilizam o Apache Hadoop em um ambiente Linux conhecido, em execução na nuvem do Azure. Para a maioria da coisas, ele deve funcionar exatamente como qualquer outra instalação do Hadoop no Linux. Este documento indica diferenças específicas que você deve estar atento.

> [!IMPORTANT]  
> O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Pré-requisitos

Muitas das etapas neste documento usam os seguintes utilitários, que talvez precisem ser instalados em seu sistema.

* [cURL](https://curl.haxx.se/) - usado para comunica-se com serviços baseados na Web.
* **jq**, um processador JSON de linha de comando.  Veja [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).
* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) - usada para gerenciar remotamente os serviços do Azure.
* **Um cliente SSH**. Para saber mais, confira [Conectar-se ao HDInsight (Apache Hadoop) usando SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="users"></a>Usuários

A menos que tenha [ingressado no domínio](./domain-joined/apache-domain-joined-introduction.md), o HDInsight deve ser considerado como um sistema de **usuário único**. Uma única conta de usuário do SSH é criada com o cluster, com permissões de nível de administrador. As contas SSH adicionais podem ser criadas, mas elas também têm acesso de administrador para o cluster.

O domínio HDInsight dá suporte para vários usuários e configurações de função e de permissão mais granulares. Para obter mais informações, consulte [Gerenciar clusters HDInsight ingressados em domínio](./domain-joined/apache-domain-joined-manage.md).

## <a name="domain-names"></a>Nomes de domínio

É o nome de domínio totalmente qualificado (FQDN) para usar ao se conectar ao cluster pela internet `CLUSTERNAME.azurehdinsight.net` ou `CLUSTERNAME-ssh.azurehdinsight.net` (só para SSH).

Internamente, cada nó no cluster tem um nome que é atribuído durante a configuração do cluster. Para localizar os nomes dos clusters, consulte a página **Hosts** na interface do usuário do Ambari Web. Para retornar uma lista de hosts da API REST do Ambari, você também pode usar o seguinte:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

Substitua `CLUSTERNAME` pelo nome do cluster. Quando solicitado, insira a senha para a conta do administrador. Este comando retorna ao documento JSON que contém uma lista de hosts do cluster. [jq](https://stedolan.github.io/jq/) é usado para extrair o `host_name` valor do elemento para cada host.

Se for necessário localizar o nome do nó para um serviço específico, você pode consultar o Ambari desse componente. Por exemplo, para localizar os hosts do nó do nome HDFS, use o seguinte comando:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

Esse comando retorna um documento JSON que descreve o serviço e, em seguida [jq](https://stedolan.github.io/jq/) extrai apenas o `host_name` valor para os hosts.

## <a name="remote-access-to-services"></a>Acesso remoto aos serviços

* **Ambari (web)** - https://CLUSTERNAME.azurehdinsight.net

    Autenticar usando o usuário do administrador de cluster e a senha e, em seguida, entre no Ambari.

    A autenticação é texto sem formatação - sempre usar HTTPS para ajudar a garantir que a conexão seja segura.

    > [!IMPORTANT]  
    > Algumas das interfaces do usuário da web disponíveis por meio de dos nós de acesso do Ambari usando um nome de domínio interno. Nomes de domínio internos não são acessíveis publicamente na Internet. Você poderá receber erros de "servidor não encontrado" ao tentar acessar alguns recursos pela Internet.
    >
    > Para usar a funcionalidade completa da interface do usuário do Ambari Web, use um túnel SSH para tráfego Web de proxy para nó de cabeçalho do cluster. Consulte [Usar Túnel SSH para acessar a interface do usuário da Web do Apache Ambari, ResourceManager, JobHistory, NameNode, Oozie e outras interfaces do usuário da Web](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari (REST)** - https://CLUSTERNAME.azurehdinsight.net/ambari

    > [!NOTE]  
    > Autentique usando o usuário e a senha de administrador do cluster.
    >
    > A autenticação é texto sem formatação - sempre usar HTTPS para ajudar a garantir que a conexão seja segura.

* **WebHCat (Templeton)** - https://CLUSTERNAME.azurehdinsight.net/templeton

    > [!NOTE]  
    > Autentique usando o usuário e a senha de administrador do cluster.
    >
    > A autenticação é texto sem formatação - sempre usar HTTPS para ajudar a garantir que a conexão seja segura.

* **SSH** -CLUSTERNAME-ssh.azurehdinsight.net na porta 22 ou 23. A porta 22 é usada para a conexão com o nó de cabeçalho primário, enquanto a 23 é usada para a conexão com o secundário. Para obter mais informações sobre os nós de cabeçalho, consulte [Disponibilidade e confiabilidade de clusters do Apache Hadoop no HDInsight](hdinsight-high-availability-linux.md).

    > [!NOTE]  
    > Você só pode acessar os nós de cabeçalho do cluster por meio de SSH de uma máquina cliente. Uma vez conectado, você pode acessar os nós de trabalho usando SSH em um nó de cabeçalho.

Para obter mais informações, consulte o documento [Portas usadas pelos serviços do Apache Hadoop no HDInsight](hdinsight-hadoop-port-settings-for-services.md).

## <a name="file-locations"></a>Locais de arquivos

Arquivos relacionados ao Hadoop encontram-se nos nós de cluster em `/usr/hdp`. O diretório raiz contém os seguintes subdiretórios:

* **2.6.5.3006-29**: O nome do diretório é a versão do Hortonworks Data Platform usada pelo HDInsight. O número em seu cluster pode ser diferente do listado aqui.
* **atual**: Esse diretório contém links para subdiretórios na **2.6.5.3006-29** directory. Esse diretório existe para que não seja necessário lembrar do número da versão.

Dados de exemplo e arquivos JAR podem ser encontrados no Sistema de Arquivos Distribuído Hadoop em `/example` e `/HdiSamples`.

## <a name="hdfs-azure-storage-and-data-lake-storage"></a>HDFS, Armazenamento do Azure e Data Lake Storage

Na maioria das distribuições do Hadoop, os dados são armazenados em HDFS, que tem suporte pelo armazenamento local nos computadores no cluster. Utilizar armazenamento local pode ser dispendioso para uma solução baseada em nuvem onde você é cobrado por hora ou minuto por recursos cibernéticos.

Ao usar o HDInsight, os arquivos de dados são armazenados de maneira escalonável e resiliente na nuvem usando o Armazenamento de Blobs do Azure e, opcionalmente, o Azure Data Lake Storage. Esses serviços oferecem os seguintes benefícios:

* Armazenamento de longo prazo econômico.
* Acessibilidade de serviços externos como sites, utilitários de upload/download de arquivos, vários SDKs de idioma e navegadores da Web.
* Grande capacidade de arquivos e grande armazenamento escalonável.

Para saber mais, confira [Noções básicas sobre blobs](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) e [Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/).

Ao usar o Armazenamento do Azure ou o Data Lake Storage, você não precisará fazer nada especial no HDInsight para acessar os dados. Por exemplo, o comando abaixo lista arquivos na pasta `/example/data` independentemente se ela está armazenada no Armazenamento do Azure ou no Data Lake Storage:

    hdfs dfs -ls /example/data

No HDInsight, os recursos de armazenamento de dados (Armazenamento de Blobs do Azure e Azure Data Lake Storage) são separados dos recursos de computação. Portanto, é possível criar clusters do HDInsight para fazer cálculos conforme necessário e, posteriormente, excluir o cluster quando o trabalho estiver concluído, mantendo os arquivos de dados persistentemente em segurança no armazenamento em nuvem, enquanto for necessário.


### <a name="URI-and-scheme"></a>URI e esquema

Alguns comandos podem exigir que você especifique o esquema como parte do URI ao acessar um arquivo. Por exemplo, o componente de Storm HDFS requer que o esquema seja especificado. Ao usar um armazenamento não padrão (armazenamento incluído como “adicional” ao cluster), você sempre deve usar o esquema como parte do URI.

Ao usar o __Armazenamento do Azure__, use um dos seguintes esquemas de URI:

* `wasb:///`: Acesse o armazenamento padrão usando comunicação não criptografada.

* `wasbs:///`: Acesse o armazenamento padrão usando comunicação criptografada.  O esquema wasbs tem suporte somente da versão 3.6 do HDInsight em diante.

* `wasb://<container-name>@<account-name>.blob.core.windows.net/`: Usado ao comunicar-se com uma conta de armazenamento não padrão. Por exemplo, se você tiver uma conta de armazenamento adicional ou ao acessar dados armazenados em uma conta de armazenamento com acesso público.

Ao usar o __Azure Data Lake Storage Gen2__, use um dos seguintes esquemas de URI:

* `abfs:///`: Acesse o armazenamento padrão usando comunicação não criptografada.

* `abfss:///`: Acessar o armazenamento padrão usando comunicação criptografada.  O esquema abfss tem suporte somente da versão 3.6 do HDInsight em diante.

* `abfs://<container-name>@<account-name>.dfs.core.windows.net/`: Usado ao comunicar-se com uma conta de armazenamento não padrão. Por exemplo, se você tiver uma conta de armazenamento adicional ou ao acessar dados armazenados em uma conta de armazenamento com acesso público.

Ao usar o __Azure Data Lake Storage Gen1__, use um dos seguintes esquemas de URI:

* `adl:///`: Acessar o Data Lake Storage padrão para o cluster.

* `adl://<storage-name>.azuredatalakestore.net/`: Utilizado ao se comunicar com uma conta do Data Lake Storage não padrão. Também é utilizado para acessar dados fora do diretório raíz do seu cluster HDInsight.

> [!IMPORTANT]  
> Ao usar o Data Lake Storage como o repositório padrão para o HDInsight, você deve especificar um caminho dentro do repositório para usar como a raiz de armazenamento do HDInsight. O caminho padrão é `/clusters/<cluster-name>/`.
>
> Ao usar `/` ou `adl:///` para acessar dados, você só pode acessar dados armazenados na raiz (por exemplo, `/clusters/<cluster-name>/`) do cluster. Para acessar dados em qualquer lugar do repositório, use o formato `adl://<storage-name>.azuredatalakestore.net/`.

### <a name="what-storage-is-the-cluster-using"></a>Que armazenamento o cluster está usando

Você pode usar o Ambari para recuperar a configuração de armazenamento padrão para o cluster. Use o comando a seguir para recuperar informações de configuração do HDFS usando o curl e as filtre usando o [jq](https://stedolan.github.io/jq/):

```bash
curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

> [!NOTE]  
> Esse comando retorna a primeira configuração aplicada ao servidor (`service_config_version=1`) que contém essas informações. Talvez seja necessário listar todas as versões de configuração para localizar a mais recente.

Esse comando retorna um valor semelhante às URIs a seguir:

* `wasb://<container-name>@<account-name>.blob.core.windows.net`, se estiver usando uma conta de armazenamento do Azure.

    O nome da conta é o nome da conta de Armazenamento do Microsoft Azure. O nome do contêiner é o contêiner de blob que é a raiz do armazenamento de cluster.

* `adl://home` se usar o Azure Data Lake Storage. Para obter o nome do Data Lake Storage, use a seguinte chamada REST:

     ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    Esse comando retorna o seguinte nome de host: `<data-lake-store-account-name>.azuredatalakestore.net`.

    Para obter o diretório no repositório, ou seja, a raiz para o HDInsight, use a seguinte chamada REST:

    ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```

    Esse comando retorna uma resposta semelhante à seguinte: `/clusters/<hdinsight-cluster-name>/`.

Você também pode encontrar as informações de armazenamento usando o portal do Azure e as seguintes etapas:

1. No [Portal do Azure](https://portal.azure.com/), escolha o cluster HDInsight.

2. Na seção **Propriedades**, selecione **Contas de armazenamento**. As informações de armazenamento para o cluster são exibidas.

### <a name="how-do-i-access-files-from-outside-hdinsight"></a>Como acessar arquivos fora do HDInsight

Há várias maneiras de acessar dados de fora do cluster do HDInsight. A seguir, há alguns links para utilitários e SDKs que podem ser usados para trabalhar com seus dados:

Se estiver usando o __armazenamento do Azure__, consulte os links a seguir para ver algumas maneiras de acessar os seus dados:

* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-az-cli2): Comandos de interface de linha de comando para trabalhar com Azure. Depois de instalar, use o comando `az storage` para obter ajuda sobre o uso do armazenamento ou `az storage blob` para comandos específicos do blob.
* [blobxfer.py](https://github.com/Azure/blobxfer): Um Script do Python para trabalhar com blobs no Armazenamento do Microsoft Azure.
* Vários SDKs:

    * [Java](https://github.com/Azure/azure-sdk-for-java)
    * [Node.js](https://github.com/Azure/azure-sdk-for-node)
    * [PHP](https://github.com/Azure/azure-sdk-for-php)
    * [Python](https://github.com/Azure/azure-sdk-for-python)
    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
    * [.NET](https://github.com/Azure/azure-sdk-for-net)
    * [API REST de armazenamento](https://msdn.microsoft.com/library/azure/dd135733.aspx)

Se estiver usando o __Azure Data Lake Storage__, confira os links a seguir para ver algumas maneiras de acessar os seus dados:

* [Navegador da Web](../data-lake-store/data-lake-store-get-started-portal.md)
* [PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)
* [CLI do Azure](../data-lake-store/data-lake-store-get-started-cli-2.0.md)
* [API de REST WebHDFS](../data-lake-store/data-lake-store-get-started-rest-api.md)
* [Ferramentas do Data Lake para Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)
* [.NET](../data-lake-store/data-lake-store-get-started-net-sdk.md)
* [Java](../data-lake-store/data-lake-store-get-started-java-sdk.md)
* [Python](../data-lake-store/data-lake-store-get-started-python.md)

## <a name="scaling"></a>Dimensionar o cluster

O recurso de dimensionamento de clusters permite que você altere a quantidade de nós de dados utilizados por um cluster. Você pode executar operações de dimensionamento enquanto outros trabalhos ou processos estão sendo executados em um cluster.  Consulte também, [clusters do HDInsight de escala](./hdinsight-scaling-best-practices.md)

Os diferentes tipos de cluster são afetados pelo dimensionamento da seguinte maneira:

* **Hadoop**: Ao reduzir verticalmente o número de nós em um cluster, alguns dos serviços no cluster são reiniciados. Operações de dimensionamento podem causar erros em trabalhos em execução ou pendentes após a conclusão da operação de dimensionamento. Você pode reenviar os trabalhos quando a operação for concluída.
* **HBase**: Servidores regionais são balanceados automaticamente em alguns minutos após o término da operação de dimensionamento. Para balancear manualmente servidores regionais, use as seguintes etapas:

    1. Conecte-se ao cluster HDInsight usando SSH. Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

    2. Use o seguinte para iniciar o shell do HBase:

            hbase shell

    3. Depois que o shell do HBase tiver sido carregado, use o seguinte para balancear manualmente os servidores regionais:

            balancer

* **Storm**: Você deverá redistribuir qualquer topologia do Storm em execução, após realizar uma operação de dimensionamento. O rebalanceameno permite que a topologia reajuste as configurações de paralelismo com base no novo número de nós no cluster. Para rebalancear topologias em execução, use uma das seguintes opções:

    * **SSH**: Conecte-se ao servidor e use o seguinte comando para redistribuir uma topologia:

            storm rebalance TOPOLOGYNAME

        Você também pode especificar parâmetros para substituir as dicas de paralelismo fornecidas originalmente pela topologia. Por exemplo, `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` reconfigurará a topologia para 5 processos de trabalho, 3 executores para o componente blue-spout e 10 executores para o componente yellow-bolt.

    * **Interface do usuário do Storm**: Use as etapas a seguir para redistribuir uma topologia usando a interface do usuário do Storm.

        1. Abra `https://CLUSTERNAME.azurehdinsight.net/stormui` no navegador da web, onde `CLUSTERNAME` é o nome do cluster Storm. Se solicitado, insira o nome de administrador (admin) do cluster HDInsight e a senha que você especificou ao criar o cluster.
        2. Selecione a topologia que você quer rebalancear e selecione o botão **Rebalancear** . Insira o atraso antes de a operação de rebalanceamento ser executada.

* **Kafka**: Você deverá redistribuir as réplicas de partições após as operações de dimensionamento. Para obter mais informações, consulte o documento [Alta disponibilidade de dados com o Apache Kafka no HDInsight](./kafka/apache-kafka-high-availability.md).

Para obter informações específicas sobre como dimensionar o cluster HDInsight, consulte:

* [Gerenciar clusters do Apache Hadoop no HDInsight usando o portal do Azure](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Gerenciar clusters do Apache Hadoop no HDInsight usando o Azure PowerShell](hdinsight-administer-use-command-line.md#scale-clusters)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Como instalo o Hue (ou outro componente do Hadoop)?

O HDInsight é um serviço gerenciado. Se o Azure detectar um problema com o cluster, ele poderá excluir o nó com falha e criar um nó para substituí-lo. Se você instalar coisas no cluster manualmente, elas não persistirão durante a operação. Em vez disso, use as [Ações de Script HDInsight](hdinsight-hadoop-customize-cluster-linux.md). Uma ação de script pode ser usada para fazer as seguintes alterações:

* Instale e configure um serviço ou um site da Web.
* Instalar e configurar um componente que requer alterações de configuração em vários nós no cluster.

Ações de script são scripts Bash. Os scripts são executados durante a criação do cluster e são usados para instalar e configurar componentes adicionais. São fornecidos scripts de exemplo para instalar os seguintes componentes:

* [Apache Giraph](hdinsight-hadoop-giraph-install-linux.md)

Para saber mais sobre como desenvolver suas próprias ações de script, consulte [Desenvolvimento de ação de script com o HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="jar-files"></a>Arquivos Jar

Algumas tecnologias do Hadoop são fornecidas em arquivos jar independentes que contêm funções usadas como parte de um trabalho do MapReduce ou de dentro de Pig ou Hive. Geralmente, elas não exigem nenhuma configuração e podem ser carregadas no cluster após a criação e usadas diretamente. Se você deseja verificar se o componente resistirá ao refazer a imagem do cluster, armazene o arquivo jar no armazenamento padrão para o cluster (WASB ou ADL).

Por exemplo, para usar a versão mais recente do [Apache DataFu](https://datafu.incubator.apache.org/), faça o download de um jar que contém o projeto e carregue-o no cluster HDInsight. Siga a documentação do DataFu sobre como usá-lo do Pig ou Hive.

> [!IMPORTANT]  
> Alguns componentes que são arquivos jar autônomos são fornecidos com o HDInsight, mas não estão no caminho. Se você estiver procurando por um componente específico, você pode usar o acompanhamento para procurá-lo em seu cluster:
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> Esse comando retornará o caminho de arquivos jar correspondentes.

Para utilizar uma versão diferente de um componente, carregue a versão necessária e utilize-a em seus trabalhos.

> [!IMPORTANT]
> Há suporte total a componentes fornecidos com o cluster do HDInsight e o Suporte da Microsoft ajudará a isolar e resolver problemas relacionados a esses componentes.
>
> Componentes personalizados recebem suporte comercialmente razoável para ajudá-lo a solucionar o problema. Isso pode resultar na resolução do problema ou na solicitação de você buscar nos canais disponíveis as tecnologias de código-fonte aberto, onde é possível encontrar conhecimento aprofundado sobre essa tecnologia. Por exemplo, há muitos sites da comunidade que podem ser usados, como: [Fórum do MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Além disso, os projetos Apache têm sites de projeto em [https://apache.org](https://apache.org), por exemplo: [Hadoop](https://hadoop.apache.org/), [Spark](https://spark.apache.org/).

## <a name="next-steps"></a>Próximos passos

* [Migrar do HDInsight baseado em Windows para baseado em Linux](hdinsight-migrate-from-windows-to-linux.md)
* [Gerenciar clusters de HDInsight usando a API REST do Apache Ambari](./hdinsight-hadoop-manage-ambari-rest-api.md)
* [Usar o Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Usar o Apache Pig com o HDInsight](hadoop/hdinsight-use-pig.md)
* [Usar trabalhos do MapReduce com o HDInsight](hadoop/hdinsight-use-mapreduce.md)

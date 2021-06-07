---
title: Conectar-se ou instalar o Apache beeline-Azure HDInsight
description: Saiba como se conectar ao cliente Apache beeline para executar consultas Hive com Hadoop no HDInsight. Beeline é um utilitário para trabalhar com HiveServer2 sobre JDBC.
ms.service: hdinsight
ms.topic: how-to
ms.custom: contperf-fy21q1
ms.date: 05/27/2020
ms.openlocfilehash: ab5dedf6718dad4f16fde59d905e2e59be5c495f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944463"
---
# <a name="connect-to-apache-beeline-on-hdinsight-or-install-it-locally"></a>Conectar-se ao Apache beeline no HDInsight ou instalá-lo localmente

O [Apache beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) é um cliente do hive que está incluído nos nós de cabeçalho do seu cluster HDInsight. Este artigo descreve como se conectar ao cliente do beeline instalado em seu cluster HDInsight entre diferentes tipos de conexões. Ele também aborda como [instalar o cliente beeline localmente](#install-beeline-client). 

## <a name="types-of-connections"></a>Tipos de conexões

### <a name="from-an-ssh-session"></a>De uma sessão SSH

Ao conectar-se de uma sessão SSH a um cabeçalho de cluster, você pode conectar-se ao `headnodehost` endereço na porta `10001` :

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

### <a name="over-an-azure-virtual-network"></a>Em uma rede virtual do Azure

Ao conectar-se de um cliente ao HDInsight em uma rede virtual do Azure, você deve fornecer o FQDN (nome de domínio totalmente qualificado) de um nó de cabeçalho do cluster. Desde que essa conexão seja feita diretamente para os nós de cluster, a conexão usa a porta `10001`:

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Substitua `<headnode-FQDN>` pelo nome de domínio totalmente qualificado de um cabeçalho de cluster. Para localizar o nome de domínio totalmente qualificado de um nó principal, use as informações do documento [Gerenciar HDInsight usando a API de REST do Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes).

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>Para o cluster Enterprise Security Package do HDInsight (ESP) usando Kerberos

Ao conectar-se de um cliente a um cluster Enterprise Security Package (ESP) ingressado no Azure Active Directory (AAD) – DS em um computador no mesmo realm do cluster, você também deve especificar o nome `<AAD-Domain>` de domínio e o nome de uma conta de usuário de domínio com permissões para acessar o cluster `<username>` :

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Substitua `<username>` pelo nome de uma conta no domínio com permissões para acessar o cluster. Substitua `<AAD-DOMAIN>` pelo nome do AAD (Azure Active Directory) ao qual o cluster está associado. Use uma cadeia de caracteres em maiúsculas para o valor `<AAD-DOMAIN>`, caso contrário, a credencial não será encontrada. Marque `/etc/krb5.conf` para os nomes de realm, se necessário.

Para localizar a URL JDBC de Ambari:

1. Em um navegador da Web, navegue até `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary`, em que `CLUSTERNAME` é o nome do cluster. Verifique se o HiveServer2 está em execução.

1. Use a área de transferência para copiar a URL JDBC do HiveServer2.

### <a name="over-public-or-private-endpoints"></a>Em pontos de extremidade públicos ou privados

Ao se conectar a um cluster usando os pontos de extremidade públicos ou privados, você deve fornecer o nome da conta de logon do cluster (padrão `admin` ) e a senha. Por exemplo, usando Beeline de um sistema de cliente para conectar-se para o `clustername.azurehdinsight.net` endereço. Essa conexão é feita pela porta `443` e é criptografada usando TLS/SSL.

Substitua `clustername` pelo nome do cluster HDInsight. Substitua `admin` pela conta de logon do cluster de seu cluster. Para clusters ESP, use o UPN completo (por exemplo, user@domain.com ). Substitua `password` pela senha da conta de logon do cluster.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

ou para o ponto de extremidade privado:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

Pontos de extremidade privados apontam para um balanceador de carga básico, que só pode ser acessado por meio do VNETs emparelhado na mesma região. Consulte [restrições em emparelhamento VNet global e balanceadores de carga](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) para obter mais informações. Você pode usar o `curl` comando com a `-v` opção para solucionar problemas de conectividade com pontos de extremidade públicos ou privados antes de usar o beeline.

### <a name="use-beeline-with-apache-spark"></a>Usar Beeline com Apache Spark

O Apache Spark fornece sua própria implementação de HiveServer2, que, às vezes, é referenciado como o servidor Spark Thrift. Esse serviço usa o Spark SQL para resolver consultas em vez de Hive. E pode fornecer melhor desempenho dependendo da sua consulta.

#### <a name="through-public-or-private-endpoints"></a>Por meio de pontos de extremidade públicos ou privados

A cadeia de conexão usada é ligeiramente diferente. Em vez de conter `httpPath=/hive2` uso `httpPath/sparkhive2` . Substitua `clustername` pelo nome do cluster HDInsight. Substitua `admin` pela conta de logon do cluster de seu cluster. Para clusters ESP, use o UPN completo (por exemplo, user@domain.com ). Substitua `password` pela senha da conta de logon do cluster.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

ou para o ponto de extremidade privado:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

Pontos de extremidade privados apontam para um balanceador de carga básico, que só pode ser acessado por meio do VNETs emparelhado na mesma região. Consulte [restrições em emparelhamento VNet global e balanceadores de carga](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) para obter mais informações. Você pode usar o `curl` comando com a `-v` opção para solucionar problemas de conectividade com pontos de extremidade públicos ou privados antes de usar o beeline.

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>Do cabeçalho do cluster ou dentro da rede virtual do Azure com Apache Spark

Ao conectar diretamente do nó principal do cluster ou de um recurso dentro da mesma Rede Virtual do Azure que o cluster HDInsight, a porta `10002` deve ser usada para o servidor do Spark Thrift em vez de `10001`. O exemplo a seguir mostra como se conectar diretamente ao nó principal:

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

## <a name="install-beeline-client"></a>Instalar o cliente beeline

Embora o beeline esteja incluído nos nós de cabeçalho, talvez você queira instalá-lo localmente.  As etapas de instalação para um computador local são baseadas em um [subsistema do Windows para Linux](/windows/wsl/install-win10).

1. Atualizar listas de pacotes. Digite o seguinte comando em seu shell bash:

    ```bash
    sudo apt-get update
    ```

1. Instale o Java se não estiver instalado. Você pode verificar com o `which java` comando.

    1. Se nenhum pacote Java estiver instalado, digite o seguinte comando:

        ```bash
        sudo apt install openjdk-11-jre-headless
        ```

    1. Abra o arquivo bashrc (geralmente encontrado em ~/.bashrc): `nano ~/.bashrc` .

    1. Modifique o arquivo bashrc. Adicione a seguinte linha ao final do arquivo:

        ```bash
        export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
        ```

        Em seguida, pressione **Ctrl + X**, em seguida, **Y** e, em seguida, Enter.

1. Baixe os arquivos mortos Hadoop e beeline, insira os seguintes comandos:

    ```bash
    wget https://archive.apache.org/dist/hadoop/core/hadoop-2.7.3/hadoop-2.7.3.tar.gz
    wget https://archive.apache.org/dist/hive/hive-1.2.1/apache-hive-1.2.1-bin.tar.gz
    ```

1. Descompacte os arquivos mortos, insira os seguintes comandos:

    ```bash
    tar -xvzf hadoop-2.7.3.tar.gz
    tar -xvzf apache-hive-1.2.1-bin.tar.gz
    ```

1. Modifique ainda mais o arquivo bashrc. Você precisará identificar o caminho para onde os arquivos mortos foram desempacotados. Se você estiver usando o [subsistema do Windows para Linux](/windows/wsl/install-win10)e seguiu as etapas exatamente, o caminho será `/mnt/c/Users/user/` , onde `user` é seu nome de usuário.

    1. Abra o arquivo: `nano ~/.bashrc`

    1. Modifique os comandos abaixo com o caminho apropriado e insira-os no final do arquivo bashrc:

        ```bash
        export HADOOP_HOME=/path_where_the_archives_were_unpacked/hadoop-2.7.3
        export HIVE_HOME=/path_where_the_archives_were_unpacked/apache-hive-1.2.1-bin
        PATH=$PATH:$HIVE_HOME/bin
        ```

    1. Em seguida, pressione **Ctrl + X**, em seguida, **Y** e, em seguida, Enter.

1. Feche e reabra a sessão de bash.

1. Teste sua conexão. Use o formato de conexão de [pontos de extremidade públicos ou privados](#over-public-or-private-endpoints)acima.

## <a name="next-steps"></a>Próximas etapas

* Para obter exemplos de como usar o cliente beeline com Apache Hive, consulte [usar o Apache beeline com Apache Hive](apache-hadoop-use-hive-beeline.md)
* Para obter mais informações gerais sobre o hive no HDInsight, consulte [usar Apache Hive com Apache Hadoop no HDInsight](hdinsight-use-hive.md)
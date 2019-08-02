---
title: Configurar a replicação de cluster HBase nas redes virtuais do Azure – Azure HDInsight
description: Saiba como configurar a replicação de HBase de uma versão do HDInsight para outra para balanceamento de carga, alta disponibilidade, migração sem tempo de inatividade, atualizações e recuperação de desastre.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/15/2018
ms.openlocfilehash: 38d3c61acee9dca18ab1f863d878e02f7437a600
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67433729"
---
# <a name="set-up-apache-hbase-cluster-replication-in-azure-virtual-networks"></a>Configurar a replicação de cluster do Apache HBase em redes virtuais do Azure

Saiba como configurar a replicação do [Apache HBase](https://hbase.apache.org/) em uma rede virtual ou entre duas redes virtuais no Azure.

A replicação de cluster usa uma metodologia de envio de origem. Um cluster HBase pode ser uma fonte, um destino ou pode atender a ambas as funções de uma vez. A replicação é assíncrona. A meta da replicação é a consistência eventual. Quando a origem recebe uma edição para uma família de coluna com replicação habilitada, essa edição é propagada para todos os clusters de destino. Quando os dados são replicados de um cluster para outro, o cluster de origem e todos os clusters que já consumiram os dados são rastreados para evitar loops de replicação.

Neste artigo, você configurar uma replicação de origem / destino. Para obter outras topologias de cluster, consulte o [Guia de referência do Apache HBase](https://hbase.apache.org/book.html#_cluster_replication).

Casos de uso de replicação de HBase para uma única rede virtual:

* Balanceamento de carga. Por exemplo, é possível executar verificações ou trabalhos MapReduce no cluster de destino e ingerir de dados no cluster de origem.
* Adicionar alta disponibilidade.
* Migrar dados de um cluster de HBase para outro.
* Atualizar um cluster HDInsight do Azure de uma versão para outra.

Casos de uso de replicação de HBase para duas redes virtuais:

* Configurar uma recuperação de desastre.
* Balanceamento de carga e particionamento do aplicativo.
* Adicionar alta disponibilidade.

É possível replicar clusters usando os scripts [ação de script](../hdinsight-hadoop-customize-cluster-linux.md) do [GitHub](https://github.com/Azure/hbase-utils/tree/master/replication).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, você deve ter uma assinatura do Azure. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="set-up-the-environments"></a>Configurar os ambientes

Há três opções de configuração:

- Dois clusters do Apache HBase em uma rede virtual do Azure.
- Dois clusters do Apache HBase em duas redes virtuais diferentes na mesma região.
- Dois clusters do Apache HBase em duas redes virtuais diferentes em duas regiões diferentes (replicação geográfica).

Este artigo aborda o cenário de replicação geográfica.

Para facilitar a configuração dos ambientes, alguns [modelos do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) foram criados. Se você preferir configurar os ambientes usando outros métodos, consulte:

- [Criar clusters do Apache Hadoop no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
- [Criar clusters do Apache HBase na rede Virtual do Azure](apache-hbase-provision-vnet.md)

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>Configurar duas redes virtuais em duas regiões diferentes

Para usar um modelo que cria duas redes virtuais em duas regiões diferentes e a conexão VPN entre as VNETs, selecione o botão **Implantar no Azure** a seguir. A definição de modelo está armazenada em um [armazenamento de blobs público](https://hditutorialdata.blob.core.windows.net/hbaseha/azuredeploy.json).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

Alguns dos valores embutidos em código no modelo:

**VNet 1**

| Propriedade | Value |
|----------|-------|
| Location | Oeste dos EUA |
| Nome da VNet | &lt;ClusterNamePrevix>-vnet1 |
| Prefixo de espaço de endereço | 10.1.0.0/16 |
| Nome da sub-rede | Sub-rede 1 |
| Prefixo de sub-rede | 10.1.0.0/24 |
| Nome da sub-rede (gateway) | GatewaySubnet (não pode ser alterado) |
| Prefixo da sub-rede (gateway) | 10.1.255.0/27 |
| Nome do Gateway | vnet1gw |
| Tipo de gateway | Vpn |
| Tipo de VPN de gateway | RouteBased |
| SKU de gateway | Basic |
| IP do gateway | vnet1gwip |

**VNet 2**

| Propriedade | Value |
|----------|-------|
| Location | East US |
| Nome da VNet | &lt;ClusterNamePrevix>-vnet2 |
| Prefixo de espaço de endereço | 10.2.0.0/16 |
| Nome da sub-rede | Sub-rede 1 |
| Prefixo de sub-rede | 10.2.0.0/24 |
| Nome da sub-rede (gateway) | GatewaySubnet (não pode ser alterado) |
| Prefixo da sub-rede (gateway) | 10.2.255.0/27 |
| Nome do Gateway | vnet2gw |
| Tipo de gateway | Vpn |
| Tipo de VPN de gateway | RouteBased |
| SKU de gateway | Basic |
| IP do gateway | vnet1gwip |

## <a name="setup-dns"></a>Instalação do DNS

Na seção anterior, o modelo cria uma máquina virtual de Ubuntu em cada uma das duas redes virtuais.  Nesta seção, você instala o Bind nas duas máquinas virtuais DNS e, em seguida, configura o encaminhamento de DNS nas duas máquinas virtuais.

Para instalar ao Bind, você precisa localizar o endereço IP público das duas máquinas virtuais DNS.

1. Abra o [Portal do Azure](https://portal.azure.com).
2. Abra a máquina virtual DNS selecionando **Grupos de recursos > [nome do grupo de recursos] > [vnet1DNS]** .  O nome do grupo de recursos é o que você criar no último procedimento. Os nomes de máquina virtual DNS padrão são *vnet1DNS* e *vnet2NDS*.
3. Selecione **Propriedades** para abrir a página de propriedades da rede virtual.
4. Anote o **endereço IP público**e também verifique a **endereço IP privado**.  O endereço IP privado será **10.1.0.4** para vnet1DNS e **10.2.0.4** para vnet2DNS.  
5. Altere os servidores DNS das duas redes virtuais para usar servidores DNS padrão (fornecidos pelo Azure) para permitir o acesso de entrada e de saída para baixar pacotes para instalar o Bind nas etapas a seguir.

Para instalar o Bind, use o seguinte procedimento:

1. Use SSH para conexão com o __endereço IP público__ da máquina virtual DNS. O exemplo a seguir se conecta a uma máquina virtual em 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

    Substitua `sshuser` pela conta de usuário SSH que você especificou ao criar o cluster na sua máquina virtual DNS.

    > [!NOTE]  
    > Há uma variedade de maneiras para obter o utilitário `ssh`. No Linux, Unix e macOS, ele é fornecido como parte do sistema operacional. Se você estiver usando o Windows, considere uma das seguintes opções:
    >
    > * [Azure Cloud Shell](../../cloud-shell/quickstart.md)
    > * [Bash no Ubuntu no Windows 10](https://msdn.microsoft.com/commandline/wsl/about)
    > * [Git (https://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSH (https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. Para instalar o Bind, use os seguintes comandos da sessão SSH:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Configure o Bind para encaminhar solicitações de resolução de nome para seu servidor DNS local. Para fazer isso, use o seguinte texto como o conteúdo do arquivo `/etc/bind/named.conf.options`:

    ```
    acl goodclients {
        10.1.0.0/16; # Replace with the IP address range of the virtual network 1
        10.2.0.0/16; # Replace with the IP address range of the virtual network 2
        localhost;
        localhost;
    };
    
    options {
        directory "/var/cache/bind";
        recursion yes;
        allow-query { goodclients; };

        forwarders {
            168.63.129.16; #This is the Azure DNS server
        };

        dnssec-validation auto;

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
    };
    ```
    
    > [!IMPORTANT]  
    > Substitua os valores na seção `goodclients` com o intervalo de endereços IP das duas redes virtuais. Esta seção define os endereços dos quais esse servidor DNS aceita solicitações.

    Para editar esse arquivo, use o seguinte comando:

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    Para salvar o arquivo, use __Ctrl+X__, __Y__ e, em seguida, __Enter__.

4. Na sessão do SSH, use o comando a seguir:

    ```bash
    hostname -f
    ```

    Esse comando retorna um valor semelhante ao texto a seguir:

        vnet1DNS.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net

    O texto `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` é o __sufixo DNS__ para essa rede virtual. Salve esse valor, pois ele é usado mais tarde.

    Você também deve descobrir o sufixo DNS do outro servidor DNS. Você precisará disso na próxima etapa.

5. Para configurar o Bind para resolver nomes DNS para os recursos na rede virtual, use o seguinte texto como o conteúdo do arquivo `/etc/bind/named.conf.local`:

    ```
    // Replace the following with the DNS suffix for your virtual network
    zone "v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net" {
            type forward;
            forwarders {10.2.0.4;}; # The Azure recursive resolver
    };
    ```

    > [!IMPORTANT]  
    > Substitua o `v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` pelo sufixo DNS da outra rede virtual. E o IP de encaminhador é o endereço IP do servidor DNS na outra rede virtual.

    Para editar esse arquivo, use o seguinte comando:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Para salvar o arquivo, use __Ctrl+X__, __Y__ e, em seguida, __Enter__.

6. Para iniciar o Bind, use o seguinte comando:

    ```bash
    sudo service bind9 restart
    ```

7. Para verificar se o Bind pode resolver os nomes de recursos em sua rede virtual, use os seguintes comandos:

    ```bash
    sudo apt install dnsutils
    nslookup vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    ```

    > [!IMPORTANT]  
    > Substituir `vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` pelo nome de domínio totalmente qualificado (FQDN) da máquina virtual DNS na outra rede.
    >
    > Substitua `10.2.0.4` pelo __endereço IP interno__ do seu servidor DNS personalizado na rede virtual.

    A resposta aparece semelhante ao seguinte texto:

    ```
    Server:         10.2.0.4
    Address:        10.2.0.4#53
    
    Non-authoritative answer:
    Name:   vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    Address: 10.2.0.4
    ```

    Até agora, você não pode verificar o endereço IP da rede sem o endereço IP do servidor DNS especificado.

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>Configurar a rede virtual para usar o servidor DNS personalizado

Para configure a rede virtual para usar o servidor DNS personalizado em vez do resolvedor recursivo do Azure, use as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com), selecione a rede virtual e, em seguida, selecione __Servidores DNS__.

2. Selecione __Personalizado__e insira o __endereço IP interno__ do servidor DNS personalizado. Por fim, selecione __Salvar__.

6. Abra a máquina virtual do servidor DNS em vnet1 e, em seguida, clique em **Reiniciar**.  Você deve reiniciar todas as máquinas virtuais na rede virtual para fazer com que configuração de DNS entre em vigor.
7. Repita a configuração de etapas do servidor DNS personalizado para vnet2.

Para testar a configuração do DNS, você pode conectar-se às duas máquinas virtuais DNS usando o SSH e executando o ping no servidor DNS de outra rede virtual usando seu nome de host. Se não funcionar, use o seguinte comando para verificar o status DNS:

```bash
sudo service bind9 status
```

## <a name="create-apache-hbase-clusters"></a>Criar clusters do Apache HBase

Crie um cluster do [Apache HBase](https://hbase.apache.org/) em cada uma das duas redes virtuais com a seguinte configuração:

- **Nome do grupo de recursos**: use o mesmo nome de grupo de recursos que você criou as redes virtuais.
- **Tipo de cluster**: HBase
- **Versão**: HBase 1.1.2 (HDI 3.6)
- **Localização**: Use a mesmo localização da rede virtual.  Por padrão, vnet1 é *Oeste dos EUA*, e a vnet2 é *Leste dos EUA*.
- **Armazenamento**: Crie uma nova conta de armazenamento para o cluster.
- **Rede virtual** (nas Configurações avançadas no portal): Selecione vnet1, que você criou no último procedimento.
- **Sub-rede**: O nome padrão usado no modelo é **subnet1**.

Para garantir que o ambiente está configurado corretamente, você deve ser capaz de executar ping do FQDN do nó principal entre os dois clusters.

## <a name="load-test-data"></a>Carregar dados de teste

Ao replicar um cluster, é necessário especificar as tabelas a serem replicadas. Nesta seção, você carrega alguns dados no cluster de origem. Na próxima seção, você habilitará a replicação entre os dois clusters.

Para criar uma tabela de **Contatos** e inserir alguns dados na tabela, siga as instruções em [Tutorial Apache HBase: Introdução ao uso do Apache HBase no HDInsight](apache-hbase-tutorial-get-started-linux.md).

## <a name="enable-replication"></a>Habilitar a replicação

As etapas a seguir mostram como chamar o script de ação de script no Portal do Azure. Para obter informações de como executar uma ação de script usando o Azure PowerShell e a CLI Clássica do Azure, confira [Personalizar clusters do HDInsight usando a ação de script](../hdinsight-hadoop-customize-cluster-linux.md).

**Para habilitar a replicação de HBase no Portal do Azure**

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Abra o cluster HBase de origem.
3. No menu do cluster, selecione **Ações de Script**.
4. Na parte superior da página, selecione **Enviar Novo**.
5. Selecione ou insira as seguintes informações:

   1. **Nome**: Insira **Habilitar a replicação**.
   2. **URL do script Bash**: Digite **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh** .
   3. **Cabeçalho**: Verifique se essa opção está selecionada. Desmarque os outros tipos de nós.
   4. **Parâmetros**: Os seguintes parâmetros de exemplo habilitam a replicação de todas as tabelas existentes e copiam todos os dados do cluster de origem para o cluster de destino:

          -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata
    
      > [!NOTE]
      > Use o nome do host em vez de FQDN para o nome DNS do cluster de origem e de destino.

6. Selecione **Criar**. O script pode demorar, especialmente quando o argumento **-copydata** for usado.

Argumentos necessários:

|NOME|DESCRIÇÃO|
|----|-----------|
|-s, --src-cluster | Especifica o nome DNS do cluster HBase de origem. Por exemplo: -s hbsrccluster, --src-cluster=hbsrccluster |
|-d, --dst-cluster | Especifica o nome DNS do cluster HBase de destino (réplica). Por exemplo: -s dsthbcluster, --src-cluster=dsthbcluster |
|-sp, --src-ambari-password | Especifica a senha de administrador para Ambari no cluster HBase de origem. |
|-dp, --dst-ambari-password | Especifica a senha de administrador para Ambari no cluster HBase de destino.|

Argumentos opcionais:

|NOME|DESCRIÇÃO|
|----|-----------|
|-su, --src-ambari-user | Especifica o nome de usuário administrador para Ambari no cluster HBase de origem. O valor padrão é **admin**. |
|-du, --dst-ambari-user | Especifica o nome de usuário administrador para Ambari no cluster HBase de destino. O valor padrão é **admin**. |
|-t, --table-list | Especificas as tabelas a serem replicadas. Por exemplo: --table-list="table1;table2;table3". Se você não especificar tabelas, todas as tabelas HBase existentes serão replicadas.|
|-m, --machine | Especifica o nó de cabeçalho em que a ação de script é executada. O valor é **hn0** ou **hn1** e deve ser escolhida com base em que é o nó principal ativo. Use essa opção quando estiver executando o script de $0 como uma ação de script do portal do HDInsight ou do Azure PowerShell.|
|-cp, -copydata | Habilita a migração dos dados existentes nas tabelas em que a replicação está habilitada. |
|-rpm, -replicate-phoenix-meta | Habilita a replicação nas tabelas do sistema Phoenix. <br><br>*Use esta opção com cuidado.* É recomendável que você recrie tabelas Phoenix em clusters de réplica antes de usar esse script. |
|-h, --help | Exibe informações de uso. |

A seção `print_usage()` do [script](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) fornece uma explicação detalhada dos parâmetros.

Após a ação de script ser implantada com êxito, é possíve usar o SSH para se conectar ao cluster HBase de destino e verificar se os dados foram replicados.

### <a name="replication-scenarios"></a>Cenários de replicação

A lista a seguir mostra alguns casos de uso geral e suas configurações de parâmetro:

- **Habilitar a replicação em todas as tabelas entre os dois clusters**. Esse cenário não requer a cópia ou migração dos dados existentes nas tabelas e não usa tabelas Phoenix. Use os seguintes parâmetros:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>  

- **Habilitar a replicação em tabelas específicas**. Use os parâmetros a seguir para habilitar a replicação em table1, table2 e table3:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"

- **Habilitar a replicação em tabelas específicas e copiar os dados existentes**. Use os parâmetros a seguir para habilitar a replicação em table1, table2 e table3:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata

- **Habilitar a replicação em todas as tabelas e replicar metadados Phoenix da origem para o destino**. A replicação de metadados Phoenix não é perfeita. Use-a com atenção. Use os seguintes parâmetros:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta

## <a name="copy-and-migrate-data"></a>Copiar e migrar dados

Há dois scripts de ação de script separados disponíveis para copiar ou migrar dados após a replicação ser habilitada:

- [Script para tabelas pequenas](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (tabelas com apenas alguns gigabytes de tamanho, a cópia leva menos de uma hora para terminar)

- [Script para tabelas grandes](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) (tabelas que devem levar mais de uma hora para copiar)

É possível seguir o mesmo procedimento descrito em [Habilitar replicação](#enable-replication) para chamar a ação de script. Use os seguintes parâmetros:

    -m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]

A seção `print_usage()` do [script](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh) fornece uma descrição detalhada dos parâmetros.

### <a name="scenarios"></a>Cenários

- **Copiar tabelas específicas (test1, test2 e test3) para todas as linhas editadas até o momento (carimbo de hora atual)** :

        -m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow
  Ou:

        -m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow


- **Copiar tabelas específicas com intervalo de tempo especificado**:

        -m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"


## <a name="disable-replication"></a>Desabilitar a replicação

Para desabilitar a replicação, use outro script de ação de script do [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh). É possível seguir o mesmo procedimento descrito em [Habilitar replicação](#enable-replication) para chamar a ação de script. Use os seguintes parâmetros:

    -m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> <-all|-t "table1;table2;...">  

A seção `print_usage()` do [script](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) fornece uma explicação detalhada dos parâmetros.

### <a name="scenarios"></a>Cenários

- **Desabilitar a replicação em todas as tabelas**:

        -m hn1 -s <source hbase cluster name> -sp Mypassword\!789 -all
  ou o

        --src-cluster=<source hbase cluster name> --dst-cluster=<destination hbase cluster name> --src-ambari-user=<source cluster Ambari user name> --src-ambari-password=<source cluster Ambari password>

- **Desabilitar a replicação em tabelas específicas (table1, table2 e table3)** :

        -m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> -t "table1;table2;table3"

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a configurar a replicação do Apache HBase em uma rede virtual ou entre duas redes virtuais. Para saber mais sobre o HDInsight e o Apache HBase, consulte estes artigos:

* [Introdução ao Apache HBase no HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Visão geral do Apache HBase do HDInsight](./apache-hbase-overview.md)
* [Criar clusters do Apache HBase na rede Virtual do Azure](./apache-hbase-provision-vnet.md)


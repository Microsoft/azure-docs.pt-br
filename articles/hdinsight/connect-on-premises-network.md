---
title: "Conecte o HDInsight à sua rede local – Azure HDInsight | Microsoft Docs"
description: "Saiba como criar um cluster HDInsight em uma Rede Virtual do Azure e, em seguida, conecte-o à sua rede local. Saiba como configurar a resolução de nomes entre HDInsight e sua rede local usando um servidor DNS personalizado."
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/21/2017
ms.author: larryfr
ms.openlocfilehash: 27a5d0e69ec9c47feab2b23d7c79fe2547edfc08
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connect-hdinsight-to-your-on-premise-network"></a>Conectar o HDInsight à sua rede local

Saiba como conectar o HDInsight à sua rede local por meio de redes virtuais do Azure e de um gateway de VPN. Este documento fornece informações para planejamento de como:

* usar o HDInsight em uma Rede Virtual do Azure que se conecta à sua rede local.

* configurar a resolução de nome DNS entre a rede virtual e sua rede local.

* configurar grupos de segurança de rede para restringir o acesso à Internet para o HDInsight.

* Portas fornecidas pelo HDInsight na rede virtual.

## <a name="create-the-virtual-network-configuration"></a>Criar a configuração de rede Virtual

Use os documentos a seguir para aprender a criar uma Rede Virtual do Azure conectada à sua rede local:
    
* [Usando o portal do Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

* [Usando o PowerShell do Azure](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

* [Usar a CLI do Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="configure-name-resolution"></a>Configurar a resolução de nomes

Para permitir que o HDInsight e os recursos na rede ingressada se comuniquem por nome, você deve executar as seguintes ações:

* Crie um servidor DNS personalizado na Rede Virtual do Azure.

* Configure a rede virtual para usar o servidor DNS personalizado em vez do resolvedor recursivo padrão do Azure.

* Configure o encaminhamento entre o servidor DNS personalizado e seu servidor DNS local.

Essa configuração habilita o seguinte comportamento:

* Solicitações de nomes de domínio totalmente qualificados que têm o sufixo DNS __para a rede virtual__ são encaminhados para o servidor DNS personalizado. O servidor DNS personalizado, em seguida, encaminha essas solicitações para o resolvedor recursivo do Azure, que retorna o endereço IP.

* Todas as outras solicitações são encaminhadas ao servidor DNS local. Até mesmo solicitações para recursos da Internet pública como microsoft.com são encaminhadas ao servidor DNS local para a resolução de nomes.

No diagrama a seguir, linhas verdes são solicitações de recursos que terminam com o sufixo DNS da rede virtual. Linhas azuis são as solicitações para recursos na rede local ou na Internet pública.

![Diagrama de como as solicitações DNS são resolvidas na configuração usada neste documento](./media/connect-on-premises-network/on-premises-to-cloud-dns.png)

### <a name="create-a-custom-dns-server"></a>Criar um servidor DNS personalizado

> [!IMPORTANT]
> Você deve criar e configurar o servidor DNS antes de instalar o HDInsight na rede virtual.

Para criar uma VM Linux que usa o software DNS [Bind](https://www.isc.org/downloads/bind/), use as seguintes etapas:

> [!NOTE]
> Use as etapas a seguir no [Portal do Azure](https://portal.azure.com) para criar uma Máquina Virtual do Azure. Para outras maneiras de criar uma máquina virtual, consulte os seguintes documentos:
>
> * [Criar VM - CLI do Azure](../virtual-machines/linux/quick-create-cli.md)
> * [Criar VM – Azure PowerShell](../virtual-machines/linux/quick-create-portal.md)

1. Do [portal do Azure](https://portal.azure.com), selecione __+__, __Computação__ e __Ubuntu Server 16.04 LTS__.

    ![Criar uma máquina virtual do Ubuntu](./media/connect-on-premises-network/create-ubuntu-vm.png)

2. Na seção __Básico__, insira as seguintes informações:

    * __Nome__: um nome amigável que identifica esta máquina virtual. Por exemplo, __DNSProxy__.
    * __Nome de usuário__: o nome da conta SSH.
    * __Chave pública de SSH__ ou __Senha__: o método de autenticação para a conta SSH. É recomendável usar as chaves públicas, pois elas são mais seguras. Para obter mais informações, consulte o documento [Criar e usar chaves SSH para VMs Linux](../virtual-machines/linux/mac-create-ssh-keys.md).
    * __Grupo de recursos__: selecione __usar existente__ e, em seguida, selecione o grupo de recursos que contém a rede virtual criada anteriormente.
    * Para __Local__, selecione o mesmo local da rede virtual.

    ![Configuração básica da máquina virtual](./media/connect-on-premises-network/vm-basics.png)

    Deixe outras entradas com os valores padrão e, em seguida, selecione __OK__.

3. Na seção __Escolher um tamanho__, selecione o tamanho da VM. Para este tutorial, selecione a opção menor e com custo mais baixo. Para continuar, use o botão __Selecionar__.

4. Na seção __Configurações__, insira as seguintes informações:

    * __Rede virtual__: selecione a rede virtual que você criou anteriormente.

    * __Sub-rede__: selecione a sub-rede padrão para a rede virtual. __Não__ selecione a sub-rede usada pelo gateway de VPN.

    * __Conta de armazenamento de diagnóstico__: selecione uma conta de armazenamento existente ou crie uma nova.

    ![Configurações de rede virtual](./media/connect-on-premises-network/virtual-network-settings.png)

    Deixe as outras entradas com os valores padrão e, em seguida, selecione __OK__ para continuar.

5. Na seção __Comprar__, selecione o botão __Comprar__ para criar a máquina virtual.

6. Quando a máquina virtual tiver sido criada, sua seção __Visão geral__ será exibida. Na lista à esquerda, selecione __Propriedades__. Salve os valores de __Endereço IP público__ e __Endereço IP privado__. Eles serão usados na próxima seção.

    ![Endereços IP públicos e privados](./media/connect-on-premises-network/vm-ip-addresses.png)

### <a name="install-and-configure-bind-dns-software"></a>Instalar e configurar o Bind (software DNS)

1. Use SSH para conexão com o __endereço IP público__ da máquina virtual. O exemplo a seguir se conecta a uma máquina virtual em 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

    Substitua `sshuser` pela conta de usuário SSH que você especificou ao criar o cluster.

    > [!NOTE]
    > Há uma variedade de maneiras para obter o utilitário `ssh`. No Linux, Unix e macOS, ele é fornecido como parte do sistema operacional. Se você estiver usando o Windows, considere uma das seguintes opções:
    >
    > * [Azure Cloud Shell](../cloud-shell/quickstart.md)
    > * [Bash no Ubuntu no Windows 10](https://msdn.microsoft.com/commandline/wsl/about)
    > * [Git (https://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSH (https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. Para instalar o Bind, use os seguintes comandos da sessão SSH:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Para configurar o Bind para encaminhar solicitações de resolução de nomes para o servidor DNS local, use o seguinte texto como o conteúdo do arquivo `/etc/bind/named.conf.options`:

        acl goodclients {
            10.0.0.0/16; # Replace with the IP address range of the virtual network
            10.1.0.0/16; # Replace with the IP address range of the on-premises network
            localhost;
            localnets;
        };

        options {
                directory "/var/cache/bind";

                recursion yes;

                allow-query { goodclients; };

                forwarders {
                192.168.0.1; # Replace with the IP address of the on-premises DNS server
                };

                dnssec-validation auto;

                auth-nxdomain no;    # conform to RFC1035
                listen-on { any; };
        };

    > [!IMPORTANT]
    > Substitua os valores na seção `goodclients` com o intervalo de endereços IP da rede virtual e rede local. Esta seção define os endereços dos quais esse servidor DNS aceita solicitações.
    >
    > Substitua a entrada `192.168.0.1` na seção `forwarders` pelo endereço IP do servidor DNS local. Essa entrada roteia solicitações DNS para o servidor DNS local para resolução.

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

        dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net

    O texto `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` é o __sufixo DNS__ para essa rede virtual. Salve esse valor, pois ele é usado mais tarde.

5. Para configurar o Bind para resolver nomes DNS para os recursos na rede virtual, use o seguinte texto como o conteúdo do arquivo `/etc/bind/named.conf.local`:

        // Replace the following with the DNS suffix for your virtual network
        zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
            type forward;
            forwarders {168.63.129.16;}; # The Azure recursive resolver
        };

    > [!IMPORTANT]
    > Você deve substituir o `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` com o sufixo DNS que você recuperou anteriormente.

    Para editar esse arquivo, use o seguinte comando:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Para salvar o arquivo, use __Ctrl+X__, __Y__ e, em seguida, __Enter__.

6. Para iniciar o Bind, use o seguinte comando:

    ```bash
    sudo service bind9 restart
    ```

7. Para verificar se o Bind pode resolver os nomes de recursos em sua rede local, use os seguintes comandos:

    ```bash
    sudo apt install dnsutils
    nslookup dns.mynetwork.net 10.0.0.4
    ```

    > [!IMPORTANT]
    > Substitua `dns.mynetwork.net` pelo FQDN (nome de domínio totalmente qualificado) de um recurso em sua rede local.
    >
    > Substitua `10.0.0.4` pelo __endereço IP interno__ do seu servidor DNS personalizado na rede virtual.

    A resposta aparece semelhante ao seguinte texto:

        Server:         10.0.0.4
        Address:        10.0.0.4#53

        Non-authoritative answer:
        Name:   dns.mynetwork.net
        Address: 192.168.0.4

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>Configurar a rede virtual para usar o servidor DNS personalizado

Para configure a rede virtual para usar o servidor DNS personalizado em vez do resolvedor recursivo do Azure, use as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com), selecione a rede virtual e, em seguida, selecione __Servidores DNS__.

2. Selecione __Personalizado__e insira o __endereço IP interno__ do servidor DNS personalizado. Por fim, selecione __Salvar__.

    ![Definir o servidor DNS personalizado para a rede](./media/connect-on-premises-network/configure-custom-dns.png)

### <a name="configure-the-on-premises-dns-server"></a>Configurar o servidor DNS local

Na seção anterior, você configurou o servidor DNS personalizado para encaminhar solicitações para o servidor DNS local. Em seguida, você deve configurar o servidor DNS local para encaminhar solicitações para o servidor DNS personalizado.

Para etapas específicas sobre como configurar seu servidor DNS, consulte a documentação do seu software para servidores DNS. Procure as etapas sobre como configurar um __encaminhador condicional__.

Um encaminhador condicional só encaminha solicitações para um sufixo DNS específico. Nesse caso, você deve configurar um encaminhador para o sufixo DNS da rede virtual. Solicitações para esse sufixo devem ser encaminhadas para o endereço IP do servidor DNS personalizado. 

O texto a seguir é um exemplo de uma configuração de encaminhador condicional para o software DNS do **Bind**:

    zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The custom DNS server's internal IP address
    };

Para obter informações sobre como usar o DNS no **Windows Server 2016**, consulte a documentação [Add-DnsServerConditionalForwarderZone](https://technet.microsoft.com/itpro/powershell/windows/dnsserver/add-dnsserverconditionalforwarderzone)...

Depois de configurar o servidor DNS local, você pode usar `nslookup` da rede local para verificar se você pode resolver nomes na rede virtual. O exemplo a seguir 

```bash
nslookup dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net 196.168.0.4
```

Este exemplo usa o servidor DNS local em 196.168.0.4 para resolver o nome do servidor DNS personalizado. Substitua o endereço IP por aquele para o servidor DNS local. Substitua o endereço `dnsproxy` pelo nome de domínio totalmente qualificado do servidor DNS personalizado.

## <a name="optional-control-network-traffic"></a>Opcional: controlar o tráfego de rede

Você pode usar NSGs (grupos de segurança de rede) ou UDRs (rotas definidas pelo usuário) para controlar o tráfego de rede. As NSGs permitem filtrar o tráfego de entrada e de saída e permitem ou negam o tráfego. As UDRs permitem que você controle como o tráfego flui entre os recursos da rede virtual, a Internet e a rede local.

> [!WARNING]
> O HDInsight requer acesso de entrada de endereços IP específicos na nuvem do Azure e acesso irrestrito de saída. Ao usar as NSGs ou UDRs para controlar o tráfego, você deve executar as seguintes etapas:
>
> 1. Localize os endereços IP para a localização que contém sua rede virtual. Para obter uma lista de IPs necessários por localização, consulte [Endereços IP necessários](./hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip).
>
> 2. Permitir tráfego de entrada de endereços IP.
>
>    * __NSG__: permitir tráfego de __entrada__ na porta __443__ proveniente da __Internet__.
>    * __UDR__: definir o tipo do __Próximo Salto__ da rota para a __Internet__.

Para obter um exemplo de como usar o Azure PowerShell ou a CLI do Azure para criar as NSGs, consulte o documento [Estender o HDInsight com Redes Virtuais do Azure](./hdinsight-extend-hadoop-virtual-network.md#hdinsight-nsg).

## <a name="create-the-hdinsight-cluster"></a>Criar o cluster HDInsight

> [!WARNING]
> Você deve criar e configurar o servidor DNS personalizado antes de instalar o HDInsight na rede virtual.

Use as etapas no documento [Criar um cluster HDInsight usando o portal do Azure](./hdinsight-hadoop-create-linux-clusters-portal.md) para criar um cluster HDInsight.

> [!WARNING]
> * Durante a criação do cluster, você deve escolher a localização que contém sua rede virtual.
>
> * Na parte __Configurações avançadas__ da configuração, você deve selecionar a rede virtual e a sub-rede que você criou anteriormente.

## <a name="connecting-to-hdinsight"></a>Conectando-se ao HDInsight

A maior parte da documentação no HDInsight supõe que você tenha acesso ao cluster via Internet. Por exemplo, você pode se conectar ao cluster em https://NOMEDOCLUSTER.azurehdinsight.net. Esse endereço usa o gateway público, que não estará disponível se você tiver usado NSGs ou UDRs para restringir o acesso da Internet.

Alguns documentos também fazem referência `headnodehost` ao se conectarem ao cluster de uma sessão SSH. Esse endereço só está disponível de nós em um cluster e não é utilizável em clientes conectados através da rede virtual.

Para conectar-se diretamente ao HDInsight por meio da rede virtual, use as seguintes etapas:

1. Para descobrir os nomes de domínio totalmente qualificado internos dos nós do cluster HDInsight, use um dos seguintes métodos:

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

2. Para determinar a porta na qual um serviço está disponível, consulte o documento [Portas usadas por serviços do Hadoop no HDInsight](./hdinsight-hadoop-port-settings-for-services.md).

    > [!IMPORTANT]
    > Alguns serviços hospedados em nós de cabeçalho ficam ativos apenas em um nó por vez. Se você tentar acessar um serviço em um nó de cabeçalho e ele falhar, mude para o outro nó de cabeçalho.
    >
    > Por exemplo, o Ambari só está ativo em um nó de cabeçalho por vez. Se você tentar acessar o Ambari em um nó de cabeçalho e ele retornar um erro 404, então ele estará em execução no outro nó de cabeçalho.

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre como usar o HDInsight em uma rede virtual, confira [Estender o HDInsight usando Redes Virtuais do Azure](./hdinsight-extend-hadoop-virtual-network.md).

* Para obter mais informações sobre redes virtuais do Azure, consulte a [Visão geral da Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md).

* Para obter mais informações sobre os Grupos de Segurança de Rede, veja [Grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md).

* Para saber mais sobre rotas definidas pelo usuário, confira [Rotas definidas pelo usuário e encaminhamento IP](../virtual-network/virtual-networks-udr-overview.md).

---
title: Erro InvalidNetworkConfigurationErrorCode-Azure HDInsight
description: Vários motivos para as criações de cluster com falha com o InvalidNetworkConfigurationErrorCode no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/12/2021
ms.openlocfilehash: 83d4819ecb1da91bda5fb4f1cb445bbc34fd007f
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98927006"
---
# <a name="cluster-creation-fails-with-invalidnetworkconfigurationerrorcode-in-azure-hdinsight"></a>Falha na criação do cluster com InvalidNetworkConfigurationErrorCode no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

Se você vir o código de erro `InvalidNetworkConfigurationErrorCode` com a descrição "a configuração de rede virtual não é compatível com o requisito do HDInsight", isso geralmente indica um problema com a [configuração de rede virtual](../hdinsight-plan-virtual-network-deployment.md) para o cluster. Com base no restante da descrição do erro, siga as seções abaixo para resolver o problema.

## <a name="hostname-resolution-failed"></a>"Falha na resolução do nome do host"

### <a name="issue"></a>Problema

A descrição do erro contém "falha na resolução do nome do host".

### <a name="cause"></a>Causa

Esse erro aponta para um problema com a configuração de DNS personalizada. Os servidores DNS em uma rede virtual podem encaminhar consultas DNS para resolvedores recursivos do Azure para resolver nomes de host dentro dessa rede virtual (consulte [resolução de nomes em redes virtuais](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) para obter detalhes). O acesso a resolvedores recursivos do Azure é fornecido por meio da IP virtual 168.63.129.16. Esse IP só pode ser acessado por meio das VMs do Azure. Portanto, ele não funcionará se você estiver usando um servidor DNS local ou o servidor DNS for uma VM do Azure, que não faz parte da rede virtual do cluster.

### <a name="resolution"></a>Resolução

1. Ssh na VM que faz parte do cluster e execute o comando `hostname -f` . Isso retornará o nome de domínio totalmente qualificado do host (conhecido como `<host_fqdn>` nas instruções abaixo).

1. Em seguida, execute o comando `nslookup <host_fqdn>` (por exemplo, `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net` ). Se esse comando resolver o nome para um endereço IP, significa que o servidor DNS está funcionando corretamente. Nesse caso, gere um caso de suporte com o HDInsight e investigaremos seu problema. Em seu caso de suporte, inclua as etapas de solução de problemas que você executou. Isso nos ajudará a resolver o problema mais rapidamente.

1. Se o comando acima não retornar um endereço IP, execute `nslookup <host_fqdn> 168.63.129.16` (por exemplo, `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net 168.63.129.16` ). Se esse comando for capaz de resolver o IP, significa que o servidor DNS não está encaminhando a consulta para o DNS do Azure ou não é uma VM que faça parte da mesma rede virtual que o cluster.

1. Se você não tiver uma VM do Azure que possa atuar como um servidor DNS personalizado na rede virtual do cluster, será necessário adicionar isso primeiro. Crie uma VM na rede virtual, que será configurada como encaminhador DNS.

1. Depois de ter uma VM implantada em sua rede virtual, configure as regras de encaminhamento de DNS nessa VM. Encaminhe todas as solicitações de resolução de nomes de iDNS para 168.63.129.16 e o restante para o servidor DNS. [Aqui](../hdinsight-plan-virtual-network-deployment.md) está um exemplo dessa configuração para um servidor DNS personalizado.

1. Adicione o endereço IP dessa VM como a primeira entrada DNS para a configuração de DNS da rede virtual.

---

## <a name="failed-to-connect-to-azure-storage-account"></a>"Falha ao conectar-se à conta de armazenamento do Azure"

### <a name="issue"></a>Problema

A descrição do erro contém "falha ao se conectar à conta de armazenamento do Azure" ou "falha ao conectar-se ao SQL do Azure".

### <a name="cause"></a>Causa

O armazenamento do Azure e o SQL não têm endereços IP fixos, portanto, precisamos permitir conexões de saída para todos os IPs para permitir o acesso a esses serviços. As etapas exatas de resolução dependem de se você configurou um NSG (grupo de segurança de rede) ou UDR (regras de User-Defined). Consulte a seção sobre como [controlar o tráfego de rede com o HDInsight com grupos de segurança de rede e rotas definidas pelo usuário](../control-network-traffic.md) para obter detalhes sobre essas configurações.

### <a name="resolution"></a>Resolução

* Se o cluster usar um [NSG (grupo de segurança de rede)](../../virtual-network/virtual-network-vnet-plan-design-arm.md).

    Vá para a portal do Azure e identifique o NSG associado à sub-rede em que o cluster está sendo implantado. Na seção **regras de segurança de saída** , permita o acesso de saída à Internet sem limitação (Observe que um número de **prioridade** menor aqui significa prioridade mais alta). Além disso, na seção **sub-redes** , confirme se esse NSG é aplicado à sub-rede do cluster.

* Se o cluster usar uma [UDR (rotas definidas pelo usuário)](../../virtual-network/virtual-networks-udr-overview.md).

    Vá para a portal do Azure e identifique a tabela de rotas associada à sub-rede em que o cluster está sendo implantado. Depois de encontrar a tabela de rotas para a sub-rede, inspecione a seção **rotas** nela.

    Se houver rotas definidas, verifique se há rotas para endereços IP para a região em que o cluster foi implantado e o **NextHopType** para cada rota é **Internet**. Deve haver uma rota definida para cada endereço IP necessário documentado no artigo mencionado anteriormente.

## <a name="failed-to-establish-an-outbound-connection-from-the-cluster-for-the-communication-with-the-hdinsight-resource-provider-please-ensure-that-outbound-connectivity-is-allowed"></a>"Falha ao estabelecer uma conexão de saída do cluster para a comunicação com o provedor de recursos do HDInsight. Verifique se a conectividade de saída é permitida. "

### <a name="issue"></a>Problema

A descrição do erro contém "falha ao estabelecer uma conexão de saída do cluster para a comunicação com o provedor de recursos do HDInsight. Verifique se a conectividade de saída é permitida. "

### <a name="cause"></a>Causa

Ao usar clusters HDInsight vinculados privados, o acesso de saída do cluster deve ser configurado para permitir que conexões sejam feitas ao provedor de recursos do HDInsight.

### <a name="resolution"></a>Resolução

* Para resolver esse problema, consulte as etapas de configuração do link privado do HDInsight em [configuração do link privado](../hdinsight-private-link.md)
---

## <a name="virtual-network-configuration-is-not-compatible-with-hdinsight-requirement"></a>"A configuração de rede virtual não é compatível com o requisito do HDInsight"

### <a name="issue"></a>Problema

As descrições de erro contêm mensagens semelhantes da seguinte maneira:

```
ErrorCode: InvalidNetworkConfigurationErrorCode
ErrorDescription: Virtual Network configuration is not compatible with HDInsight Requirement. Error: 'Failed to connect to Azure Storage Account; Failed to connect to Azure SQL; HostName Resolution failed', Please follow https://go.microsoft.com/fwlink/?linkid=853974 to fix it.
```

### <a name="cause"></a>Causa

Provavelmente um problema com a configuração de DNS personalizada.

### <a name="resolution"></a>Resolução

Valide se 168.63.129.16 está na cadeia DNS personalizada. Os servidores DNS em uma rede virtual podem encaminhar consultas DNS para os resolvedores recursivos do Azure a fim de resolver nomes de host dentro da rede virtual. Para obter mais informações, consulte [resolução de nomes em redes virtuais](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). O acesso a resolvedores recursivos do Azure é fornecido por meio da IP virtual 168.63.129.16.

1. Use o [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se conectar ao cluster. Edite o comando abaixo substituindo CLUSTERNAME pelo nome do cluster e, em seguida, insira o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Execute o seguinte comando:

    ```bash
    cat /etc/resolv.conf | grep nameserver*
    ```

    Você deverá ver algo como:

    ```output
    nameserver 168.63.129.16
    nameserver 10.21.34.43
    nameserver 10.21.34.44
    ```

    Com base no resultado-escolha uma das seguintes etapas a seguir:

#### <a name="1686312916-is-not-in-this-list"></a>168.63.129.16 não está nesta lista

**Opção 1**  
Adicione 168.63.129.16 como o primeiro DNS personalizado para a rede virtual usando as etapas descritas em [planejar uma rede virtual para o Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md). Essas etapas serão aplicáveis somente se o servidor DNS personalizado for executado no Linux.

**Opção 2**  
Implante uma VM do servidor DNS para a rede virtual. Isso envolve as seguintes etapas:

* Crie uma VM na rede virtual, que será configurada como encaminhador DNS (pode ser uma VM Linux ou Windows).
* Configure as regras de encaminhamento de DNS nessa VM (encaminhe todas as solicitações de resolução de nomes de iDNS para 168.63.129.16 e o restante para o servidor DNS).
* Adicione o endereço IP dessa VM como primeira entrada DNS para a configuração de DNS da rede virtual.

#### <a name="1686312916-is-in-the-list"></a>168.63.129.16 está na lista

Nesse caso, crie um caso de suporte com o HDInsight e nós investigaremos seu problema. Inclua o resultado dos comandos abaixo em seu caso de suporte. Isso nos ajudará a investigar e resolver o problema mais rapidamente.

Em uma sessão SSH no nó principal, edite e, em seguida, execute o seguinte:

```bash
hostname -f
nslookup <headnode_fqdn> (e.g.nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
dig @168.63.129.16 <headnode_fqdn> (e.g. dig @168.63.129.16 hn0-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
```
### <a name="cause"></a>Causa

Outra causa desse `InvalidNetworkConfigurationErrorCode` código de erro pode ser o uso do parâmetro preterido `EnableVmProtection` no PowerShell ou em um runbook do Azure.

### <a name="resolution"></a>Resolução

Use os parâmetros válidos do `Get-AzVirtualNetwork` conforme documentado no [SDK AZ PowerShell](/powershell/module/az.network/get-azvirtualnetwork)

---

### <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [Suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando-se à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o hub **Ajuda + suporte**. Para obter informações mais detalhadas, consulte [Como criar uma solicitação de Suporte do Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso ao Gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [Planos de suporte do Azure](https://azure.microsoft.com/support/plans/).

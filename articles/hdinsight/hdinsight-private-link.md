---
title: Proteger e isolar clusters do Azure HDInsight com o link privado (visualização)
description: Saiba como isolar clusters do Azure HDInsight em uma rede virtual usando o link privado do Azure.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: bc7834a0f8272da3f8954c7dd9f3e18163795cba
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98939357"
---
# <a name="secure-and-isolate-azure-hdinsight-clusters-with-private-link-preview"></a>Proteger e isolar clusters do Azure HDInsight com o link privado (visualização)

Na [arquitetura de rede virtual padrão](./hdinsight-virtual-network-architecture.md)do Azure hdinsight, o provedor de recursos do HDINSIGHT (RP) se comunica com o cluster usando endereços IP públicos. Alguns cenários exigem isolamento de rede completo sem uso de endereços IP públicos. Neste artigo, você aprenderá sobre os controles avançados que você pode usar para criar um cluster HDInsight privado. Para obter informações sobre como restringir o tráfego de e para o cluster sem o isolamento de rede completo, consulte [controlar o tráfego de rede no Azure HDInsight](./control-network-traffic.md).

Você pode criar clusters HDInsight privados Configurando propriedades de rede específicas em um modelo de Azure Resource Manager (ARM). Há duas propriedades que você usa para criar clusters HDInsight privados:

* Remova os endereços IP públicos definindo `resourceProviderConnection` para saída.
* Habilite o link privado do Azure e use [pontos de extremidade privados](../private-link/private-endpoint-overview.md) definindo `privateLink` como habilitado.

## <a name="remove-public-ip-addresses"></a>Remover endereços IP públicos

Por padrão, o RP do HDInsight usa uma conexão de *entrada* para o cluster usando IPS públicos. Quando a `resourceProviderConnection` propriedade de rede é definida como *saída*, ela reverte as conexões para o RP do HDInsight para que as conexões sejam sempre iniciadas de dentro do cluster para o RP. Sem uma conexão de entrada, não há necessidade de marcas de serviço de entrada ou endereços IP públicos.

Os balanceadores de carga básicos usados na arquitetura de rede virtual padrão fornecem automaticamente o NAT público (conversão de endereços de rede) para acessar as dependências de saída necessárias, como o HDInsight RP. Se você quiser restringir a conectividade de saída à Internet pública, poderá [configurar um firewall](./hdinsight-restrict-outbound-traffic.md), mas isso não é um requisito.

Configurar `resourceProviderConnection` para saída também permite que você acesse recursos específicos do cluster, como Azure data Lake Storage Gen2 ou metastores externos, usando pontos de extremidade privados. O uso de pontos de extremidade privados para esses recursos não é obrigatório, mas se você planeja ter pontos de extremidade privados para esses recursos, deverá configurar os pontos de extremidade privados e as entradas DNS `before` que criará o cluster HDInsight. Recomendamos que você crie e forneça todos os bancos de dados do SQL externos necessários, como Apache Ranger, Ambari, Oozie e metastores do hive, no momento da criação do cluster. O requisito é que todos esses recursos devem estar acessíveis de dentro da sub-rede do cluster, seja por meio de seu próprio ponto de extremidade privado ou de outra forma.

Não há suporte para o uso de pontos de extremidade privados para Azure Key Vault. Se você estiver usando Azure Key Vault para criptografia CMK em repouso, o ponto de extremidade de Azure Key Vault deverá ser acessível de dentro da sub-rede do HDInsight sem nenhum ponto de extremidade privado.

O diagrama a seguir mostra a aparência de uma possível arquitetura de rede virtual do HDInsight quando `resourceProviderConnection` é definido como de saída:

:::image type="content" source="media/hdinsight-private-link/outbound-resource-provider-connection-only.png" alt-text="Diagrama da arquitetura do HDInsight usando uma conexão do provedor de recursos de saída":::

Depois de criar o cluster, você deve configurar a resolução de DNS apropriada. O seguinte registro DNS de nome canônico (CNAME) é criado na zona DNS pública gerenciada pelo Azure: `azurehdinsight.net` .

```dns
<clustername>    CNAME    <clustername>-int
```

Para acessar o cluster usando FQDNs de cluster, você pode usar os IPs privados do balanceador de carga interno diretamente ou usar sua própria zona de DNS privado para substituir os pontos de extremidade do cluster conforme apropriado para suas necessidades. Por exemplo, você pode ter uma zona de DNS privado, `azurehdinsight.net` . e adicione seus IPs privados, conforme necessário:

```dns
<clustername>        A   10.0.0.1
<clustername-ssh>    A   10.0.0.2
```

## <a name="enable-private-link"></a>Habilitar link privado

O link privado, que é desabilitado por padrão, requer um amplo conhecimento de rede para configurar UDR (rotas definidas pelo usuário) e regras de firewall corretamente antes de criar um cluster. O uso dessa configuração é opcional, mas só estará disponível quando a `resourceProviderConnection` propriedade de rede estiver definida como de *saída* , conforme descrito na seção anterior.

Quando `privateLink` é definido como *habilitar*, os [balanceadores de carga Standard](../load-balancer/load-balancer-overview.md) internos (SLB) são criados e um serviço de vínculo privado do Azure é provisionado para cada SLB. O serviço de vínculo privado é o que permite que você acesse o cluster HDInsight de pontos de extremidade privados.

Os balanceadores de carga padrão não fornecem automaticamente o [NAT de saída público](../load-balancer/load-balancer-outbound-connections.md) como os balanceadores de carga básicos. Você deve fornecer sua própria solução NAT, como [NAT de rede virtual](../virtual-network/nat-overview.md) ou [Firewall](./hdinsight-restrict-outbound-traffic.md), para dependências de saída. Seu cluster HDInsight ainda precisa de acesso às suas dependências de saída. Se essas dependências de saída não forem permitidas, a criação do cluster poderá falhar.

### <a name="prepare-your-environment"></a>Prepare o seu ambiente

Para a criação bem-sucedida de serviços de vínculo privado, você deve [desabilitar explicitamente as políticas de rede para o serviço de vínculo privado](../private-link/disable-private-link-service-network-policy.md).

O diagrama a seguir mostra um exemplo da configuração de rede necessária antes de criar um cluster. Neste exemplo, todo o tráfego de saída é [forçado](../firewall/forced-tunneling.md) ao firewall do Azure usando UDR e as dependências de saída necessárias devem ser "permitidas" no firewall antes de criar um cluster. Para Enterprise Security Package clusters, a conectividade de rede com Azure Active Directory Domain Services pode ser fornecida pelo emparelhamento VNet.

:::image type="content" source="media/hdinsight-private-link/before-cluster-creation.png" alt-text="Diagrama de ambiente de link privado antes da criação do cluster":::

Depois de configurar a rede, você pode criar um cluster com a conexão do provedor de recursos de saída e o link privado habilitado, conforme mostrado na figura a seguir. Nessa configuração, não há nenhum IPs público e o serviço de vínculo privado é provisionado para cada balanceador de carga padrão.

:::image type="content" source="media/hdinsight-private-link/after-cluster-creation.png" alt-text="Diagrama de ambiente de link privado após a criação do cluster":::

### <a name="access-a-private-cluster"></a>Acessar um cluster privado

Para acessar clusters privados, você pode usar diretamente os IPs privados do Load balancer interno ou pode usar extensões de DNS de link privado e pontos de extremidade privados. Quando a `privateLink` configuração estiver definida como habilitada, você poderá criar seus próprios pontos de extremidade privados e configurar a resolução DNS por meio de zonas DNS privadas.

As entradas de link privado criadas na zona DNS pública gerenciada pelo Azure `azurehdinsight.net` são as seguintes:

```dns
<clustername>        CNAME    <clustername>.privatelink
<clustername>-int    CNAME    <clustername>-int.privatelink
<clustername>-ssh    CNAME    <clustername>-ssh.privatelink
```

A imagem a seguir mostra um exemplo das entradas DNS privadas necessárias para acessar o cluster de uma rede virtual que não está emparelhada ou não tem uma linha de visão direta para os balanceadores de carga do cluster. Você pode usar a zona privada do Azure para substituir `*.privatelink.azurehdinsight.net` os FQDNs e resolver seus próprios endereços IP de pontos de extremidade privados.

:::image type="content" source="media/hdinsight-private-link/access-private-clusters.png" alt-text="Diagrama da arquitetura de link privado":::

## <a name="how-to-create-clusters"></a>Como criar clusters?
### <a name="use-arm-template-properties"></a>Usar propriedades do modelo ARM

O trecho de código JSON a seguir inclui as duas propriedades de rede que você precisa configurar em seu modelo do ARM para criar um cluster HDInsight privado.

```json
networkProperties: {
    "resourceProviderConnection": "Inbound" | "Outbound",
    "privateLink": "Enabled" | "Disabled"
}
```

Para obter um modelo completo com muitos dos recursos de segurança do HDInsight Enterprise, incluindo o link privado, consulte [modelo de segurança do hdinsight Enterprise](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template).

### <a name="use-azure-powershell"></a>Usar PowerShell do Azure

Para usar o PowerShell, consulte o exemplo [aqui](/powershell/module/az.hdinsight/new-azhdinsightcluster#example-4--create-an-azure-hdinsight-cluster-with-relay-outbound-and-private-link-feature).

### <a name="use-azure-cli"></a>Usar a CLI do Azure
Para usar CLI do Azure, consulte o exemplo [aqui](/cli/azure/hdinsight#az_hdinsight_create-examples).

## <a name="next-steps"></a>Próximas etapas

* [Enterprise Security Package para o Azure HDInsight](enterprise-security-package.md)
* [Informações gerais e diretrizes de segurança corporativa no Azure HDInsight](./domain-joined/general-guidelines.md)

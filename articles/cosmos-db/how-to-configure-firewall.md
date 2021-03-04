---
title: Configurar um firewall IP para sua conta do Azure Cosmos DB
description: Saiba como configurar políticas de controle de acesso de IP para suporte ao firewall em contas do Azure Cosmos.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/03/2021
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: a7796b70d4d32e7023fbc88086a737dd76ae7723
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122707"
---
# <a name="configure-ip-firewall-in-azure-cosmos-db"></a>Configurar o firewall de IP no Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Para proteger os dados armazenados em sua conta, o Azure Cosmos DB dá suporte a um modelo de autorização baseado em segredo que utiliza um HMAC (Message Authentication Code baseado em hash) forte. Além disso, o Azure Cosmos DB dá suporte a controles de acesso baseados em IP para suporte de firewall de entrada. Esse modelo é semelhante às regras de firewall de um sistema de banco de dados tradicional e fornece um nível de segurança adicional à conta. Com firewalls, é possível configurar a conta do Azure Cosmos para ser acessível somente de um conjunto aprovado de computadores e/ou serviços de nuvem. O acesso aos dados armazenados no banco de dados do Azure Cosmos desses conjuntos de computadores e serviços aprovados exigirá que o chamador apresente um token de autorização válido.

## <a name="ip-access-control"></a><a id="ip-access-control-overview"></a>Controle de acesso IP

Por padrão, a conta do Azure Cosmos pode ser acessada da Internet, desde que a solicitação seja acompanhada por um token de autorização válido. Para configurar o controle de acesso baseado em política IP, o usuário deverá fornecer o conjunto de endereços IP ou intervalos de endereços IP no formulário CIDR (Roteamento entre Domínios sem Classificação) a ser incluído como a lista permitida de IPs do cliente para acessar uma determinada conta do Azure Cosmos. Depois que essa configuração for aplicada, todas as solicitações originadas de computadores fora dessa lista permitida receberão uma resposta 403 (Proibido). Ao usar o firewall de IP, é recomendável permitir que o portal do Azure acesse a conta. O acesso é necessário para permitir o uso do Data Explorer, bem como para recuperar as métricas da conta que aparecem no portal do Azure. Ao usar o data Explorer, além de permitir que portal do Azure acesse sua conta, você também precisa atualizar suas configurações de firewall para adicionar seu endereço IP atual às regras de firewall. Observe que as alterações de firewall podem levar até 15 minutos para serem propagadas e o firewall pode exibir um comportamento inconsistente durante esse período.

É possível combinar firewall baseado em IP com sub-rede e controle de acesso VNET. Ao combiná-los, você poderá limitar o acesso a qualquer fonte que tenha um IP público e/ou de uma sub-rede específica dentro da VNET. Para saber mais sobre como usar o controle de acesso baseado em sub-rede e VNET, consulte [Acessar recursos do Azure Cosmos DB a partir de redes virtuais](./how-to-configure-vnet-service-endpoint.md).

Para resumir, o token de autorização sempre será necessário para acessar uma conta do Azure Cosmos. Se o firewall de IP e as ACLs (listas de controle de acesso) de VNET não estiverem configurados, a conta do Azure Cosmos poderá ser acessada com o token de autorização. Depois que o firewall de IP ou as ACLs de VNET, ou ambos, estiverem configurados na conta do Azure Cosmos, somente as solicitações originadas das fontes especificadas (e com o token de autorização) receberão respostas válidas. 

Você pode proteger os dados armazenados na sua conta do Azure Cosmos DB usando firewalls de IP. O Azure Cosmos DB é compatível com controles de acesso baseados em IP para suporte de firewall de entrada. Você pode definir um firewall IP na conta do Azure Cosmos DB usando uma das seguintes maneiras:

* No portal do Azure
* Declarativamente usando um modelo do Azure Resource Manager
* Programaticamente por meio do CLI do Azure ou Azure PowerShell atualizando a propriedade **ipRangeFilter**

## <a name="configure-an-ip-firewall-by-using-the-azure-portal"></a><a id="configure-ip-policy"></a> Configurar um firewall IP usando o portal do Azure

Para definir a política de controle de acesso IP no portal do Azure, acesse a página da conta do Azure Cosmos DB e selecione **Firewall e redes virtuais** no menu de navegação. Altere o valor **Permitir acesso de** para **Redes selecionadas** e, em seguida, selecione **Salvar**.

![Captura de tela mostrando como abrir a página do Firewall no Portal do Azure](./media/how-to-configure-firewall/azure-portal-firewall.png)

Quando o controle de acesso IP está ativado, o portal do Azure fornece a capacidade de especificar endereços IP, intervalos de endereços IP e comutadores. Opções de habilitam o acesso a outros serviços do Azure e o portal do Azure. As seções a seguir fornecem detalhes sobre esses switches.

> [!NOTE]
> Depois de habilitar uma diretiva de controle de acesso IP para sua conta do Azure Cosmos DB, todas as solicitações para sua conta do Azure Cosmos DB de máquinas fora da lista permitida de intervalos de endereços IP serão rejeitadas. A navegação nos recursos do Azure Cosmos DB do portal também é bloqueada para garantir a integridade do controle de acesso.

### <a name="allow-requests-from-the-azure-portal"></a>Permitir solicitações do portal do Azure

Quando você habilita uma política de controle de acesso IP de forma programática, você precisa adicionar o endereço IP do portal do Azure à propriedade **ipRangeFilter** para manter o acesso. Os endereços IP do portal são:

|Região|Endereço IP|
|------|----------|
|Alemanha|51.4.229.218|
|China|139.217.8.252|
|Gov dos EUA|52.244.48.71|
|Todas as outras regiões|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|

Você pode habilitar solicitações para acessar o portal do Azure selecionando a opção **permitir acesso de portal do Azure** , conforme mostrado na seguinte captura de tela:

![Captura de tela mostrando como habilitar o acesso ao portal do Azure](./media/how-to-configure-firewall/enable-azure-portal.png)

### <a name="allow-requests-from-global-azure-datacenters-or-other-sources-within-azure"></a>Permitir solicitações de datacenters do Azure global ou de outras fontes dentro do Azure

Se você acessar sua conta do Azure Cosmos DB a partir de serviços que não fornecem um IP estático (por exemplo, o Azure Stream Analytics e o Azure Functions), ainda é possível usar o firewall de IP para limitar o acesso. Você pode habilitar o acesso de outras fontes no Azure selecionando a opção **aceitar conexões de dentro de datacenters do Azure** , conforme mostrado na seguinte captura de tela:

![Captura de tela mostrando como aceitar conexões de data centers do Azure](./media/how-to-configure-firewall/enable-azure-services.png)

Quando você habilita essa opção, o endereço IP `0.0.0.0` é adicionado à lista de endereços IP permitidos. O `0.0.0.0` endereço IP restringe as solicitações para sua conta de Azure Cosmos DB do intervalo de IP do datacenter do Azure. Essa configuração não permite o acesso de nenhum outro intervalo de IP à sua conta do Azure Cosmos DB.

> [!NOTE]
> Essa opção configura o firewall para permitir todas as solicitações do Azure, incluindo solicitações de assinaturas de outros clientes implantados no Azure. A lista de IPs permitidos por essa opção é ampla, limitando a eficácia de uma política de firewall. Use essa opção somente se suas solicitações não forem originadas de IPs ou sub-redes estáticas em redes virtuais. A escolha desta opção permite automaticamente o acesso do portal do Azure, porque o portal do Azure é implantado no Azure.

### <a name="requests-from-your-current-ip"></a>Solicitações do seu IP atual

Para simplificar o desenvolvimento, o portal do Azure ajuda a identificar e adicionar o IP de sua máquina cliente à lista permitida. Os aplicativos que executam sua máquina podem acessar sua conta do Azure Cosmos DB.

O portal detecta automaticamente o endereço IP do cliente. Pode ser o endereço IP do cliente da sua máquina ou o endereço IP do seu gateway de rede. Certifique-se de remover este endereço IP antes de levar suas cargas de trabalho para produção.

Para adicionar seu IP atual à lista de IPs, selecione **Adicionar me IP atual**. Em seguida, selecione **Salvar**.

:::image type="content" source="./media/how-to-configure-firewall/enable-current-ip.png" alt-text="Captura de tela mostrando como definir as configurações do firewall para o IP atual":::

### <a name="requests-from-cloud-services"></a>Solicitações dos serviços de nuvem

No Azure, os serviços de nuvem são uma maneira comum de hospedar a lógica do serviço de camada intermediária usando o Azure Cosmos DB. Para habilitar o acesso à sua conta do Azure Cosmos DB a partir de um serviço de nuvem, você deve adicionar o endereço IP público do serviço de nuvem à lista permitida de endereços IP associados à sua conta do Azure Cosmos DB [ configurando a política de controle de acesso IP](#configure-ip-policy). Isso garante que todas as instâncias de função dos serviços de nuvem tenham acesso à sua conta do Azure Cosmos DB.

Você pode recuperar endereços IP de seus serviços de nuvem no Portal do Azure, conforme mostra a captura de tela a seguir:

:::image type="content" source="./media/how-to-configure-firewall/public-ip-addresses.png" alt-text="Captura de tela mostrando o endereço IP público de um serviço de nuvem exibido no Portal do Azure":::

Quando você dimensiona seu serviço de nuvem adicionando instâncias de função, essas novas instâncias automaticamente terão acesso à conta do Azure Cosmos DB, pois fazem parte do mesmo serviço de nuvem.

### <a name="requests-from-virtual-machines"></a>Solicitações de máquinas virtuais

Você também pode usar [máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/) ou [conjuntos de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/overview.md) para hospedar serviços da camada intermediária usando o Azure Cosmos DB. Para configurar sua conta do Cosmos DB de forma que ela permita o acesso de máquinas virtuais, você deve configurar o endereço IP público da máquina virtual e/ou escala da máquina virtual definida como um dos endereços IP permitidos para sua conta do Azure Cosmos DB ao [Configurar a política de controle de acesso IP](#configure-ip-policy).

Você pode recuperar endereços IP de máquinas virtuais no Portal do Azure, conforme mostra a captura de tela a seguir:

:::image type="content" source="./media/how-to-configure-firewall/public-ip-addresses-dns.png" alt-text="Captura de tela mostrando o endereço IP público de uma máquina virtual exibida no Portal do Azure":::

Quando você adiciona instâncias de máquina virtual ao grupo, elas recebem automaticamente acesso à sua conta do Azure Cosmos DB.

### <a name="requests-from-the-internet"></a>Solicitações da Internet

Quando você acessa sua conta do Azure Cosmos DB a partir de um computador na Internet, o endereço IP do cliente ou o intervalo de endereços IP da máquina deve ser adicionado à lista permitida de endereços IP da sua conta.

### <a name="add-outbound-rules-to-the-firewall"></a>Adicionar regras de saída ao firewall

Para acessar uma lista atual de intervalos de IP de saída para adicionar às suas configurações de firewall, consulte [baixar intervalos de IP do Azure e marcas de serviço](https://www.microsoft.com/download/details.aspx?id=56519).

Para automatizar a lista, consulte [usar a API de descoberta de marca de serviço (visualização pública)](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#use-the-service-tag-discovery-api-public-preview).

## <a name="configure-an-ip-firewall-by-using-a-resource-manager-template"></a><a id="configure-ip-firewall-arm"></a>Configurar um firewall IP usando um modelo do Resource Manager

Para configurar o controle de acesso à sua conta do Azure Cosmos DB, verifique se o modelo do Resource Manager especifica a propriedade **ipRules** com uma matriz de intervalos de IP permitidos. Se estiver configurando o Firewall de IP para uma conta do Cosmos já implantada, verifique se a matriz `locations` corresponde ao que está implantado atualmente. Não é possível modificar simultaneamente a matriz `locations` e outras propriedades. Para obter mais informações e exemplos de modelos de Azure Resource Manager para Azure Cosmos DB consulte [Azure Resource Manager modelos para Azure Cosmos DB](./templates-samples-sql.md)

> [!IMPORTANT]
> A propriedade **ipRules** foi introduzida com a versão de API 2020-04-01. As versões anteriores expuseram uma propriedade **ipRangeFilter** , que é uma lista de endereços IP separados por vírgulas.

O exemplo a seguir mostra como a propriedade **ipRules** é exposta na versão de API 2020-04-01 ou posterior:

```json
{
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "name": "[variables('accountName')]",
  "apiVersion": "2020-04-01",
  "location": "[parameters('location')]",
  "kind": "GlobalDocumentDB",
  "properties": {
    "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
    "locations": "[variables('locations')]",
    "databaseAccountOfferType": "Standard",
    "enableAutomaticFailover": "[parameters('automaticFailover')]",
    "ipRules": [
      {
        "ipAddressOrRange": "40.76.54.131"
      },
      {
        "ipAddressOrRange": "52.176.6.30"
      },
      {
        "ipAddressOrRange": "52.169.50.45"
      },
      {
        "ipAddressOrRange": "52.187.184.26"
      }
    ]
  }
}
```

Este é o mesmo exemplo para qualquer versão de API anterior a 2020-04-01:

```json
{
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "name": "[variables('accountName')]",
  "apiVersion": "2019-08-01",
  "location": "[parameters('location')]",
  "kind": "GlobalDocumentDB",
  "properties": {
    "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
    "locations": "[variables('locations')]",
    "databaseAccountOfferType": "Standard",
    "enableAutomaticFailover": "[parameters('automaticFailover')]",
    "ipRangeFilter":"40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
  }
}
```

## <a name="configure-an-ip-access-control-policy-by-using-the-azure-cli"></a><a id="configure-ip-firewall-cli"></a>Configurar uma política de controle de acesso IP usando a CLI do Azure

O comando a seguir mostra como criar uma conta do Azure Cosmos DB com o controle de acesso IP:

```azurecli-interactive
# Create a Cosmos DB account with default values and IP Firewall enabled
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
ipRangeFilter='192.168.221.17,183.240.196.255,40.76.54.131'

# Make sure there are no spaces in the comma-delimited list of IP addresses or CIDR ranges.
az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --locations regionName='East US 2' failoverPriority=1 isZoneRedundant=False \
    --ip-range-filter $ipRangeFilter
```

## <a name="configure-an-ip-access-control-policy-by-using-powershell"></a><a id="configure-ip-firewall-ps"></a>Configurar uma política de controle de acesso IP usando o PowerShell

O script a seguir mostra como criar uma conta do Azure Cosmos DB com o controle de acesso IP:

```azurepowershell-interactive
# Create a Cosmos DB account with default values and IP Firewall enabled
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$ipRules = @("192.168.221.17","183.240.196.255","40.76.54.131")

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0; "isZoneRedundant"=False },
    @{ "locationName"="East US 2"; "failoverPriority"=1, "isZoneRedundant"=False }
)

# Make sure there are no spaces in the comma-delimited list of IP addresses or CIDR ranges.
$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "ipRules"=$ipRules
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2020-04-01" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a name="troubleshoot-issues-with-an-ip-access-control-policy"></a><a id="troubleshoot-ip-firewall"></a>Solucione problemas com uma política de controle de acesso IP

Você pode solucionar problemas com uma política de controle de acesso IP usando as seguintes opções:

### <a name="azure-portal"></a>Portal do Azure

Ao ativar uma política de controle de acesso IP para sua conta do Azure Cosmos DB, você bloqueia todas as solicitações para sua conta de máquinas fora da lista permitida de intervalos de endereços IP. Para habilitar operações de plano de dados do portal, como navegar em contêineres e consultar documentos, você precisa permitir explicitamente o acesso ao portal do Azure usando o painel **Firewall** no portal.

### <a name="sdks"></a>SDKs

Quando você acessa recursos do Microsoft Azure Cosmos DB usando SDKs de computadores que não estão na lista permitida, uma resposta genérica **403 Proibido** é retornada sem detalhes adicionais. Verifique a lista de IPs permitidos para sua conta e verifique se a configuração de política correta está aplicada à sua conta do Azure Cosmos DB.

### <a name="source-ips-in-blocked-requests"></a>IPs de origem em solicitações bloqueadas

Ative o log de diagnóstico na sua conta do Azure Cosmos DB. Esses logs mostram cada solicitação e resposta. As mensagens relacionadas ao firewall são registradas com um código de retorno 403. Ao filtrar essas mensagens, você pode ver os IPs de origem das solicitações bloqueadas. Confira [Log de diagnósticos do Azure Cosmos DB](./monitor-cosmos-db.md).

### <a name="requests-from-a-subnet-with-a-service-endpoint-for-azure-cosmos-db-enabled"></a>Solicitações de uma sub-rede com um ponto de extremidade de serviço para o Azure Cosmos DB ativado

Solicitações de uma sub-rede em uma rede virtual que possui um ponto de extremidade de serviço para o Azure Cosmos DB habilitado envia a identidade da rede virtual e da sub-rede às contas do Azure Cosmos DB. Essas solicitações não têm o IP público da origem, portanto, os filtros IP as rejeitam. Para permitir acesso de sub-redes específicas em redes virtuais, adicione uma lista de controle de acesso, conforme descrito em [Como configurar a rede virtual e o acesso baseado em sub-rede para sua conta do Azure Cosmos DB](how-to-configure-vnet-service-endpoint.md). Pode levar até 15 minutos para as regras de firewall serem aplicadas e o firewall pode exibir um comportamento inconsistente durante esse período.

### <a name="private-ip-addresses-in-list-of-allowed-addresses"></a>Endereços IP privados na lista de endereços permitidos

A criação ou atualização de uma conta do Azure cosmos com uma lista de endereços permitidos contendo endereços IP privados falhará. Certifique-se de que nenhum endereço IP privado esteja especificado na lista.

## <a name="next-steps"></a>Próximas etapas

Para configurar um ponto de extremidade de serviço de rede virtual para sua conta do Azure Cosmos DB, consulte os seguintes artigos:

* [Controle de acesso à rede virtual e sub-rede para sua conta do Azure Cosmos DB](how-to-configure-vnet-service-endpoint.md)
* [Configurar rede virtual e sub-rede com base em acesso para sua conta do Azure Cosmos DB](how-to-configure-vnet-service-endpoint.md)
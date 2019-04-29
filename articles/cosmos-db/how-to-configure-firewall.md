---
title: Configurar um firewall IP para sua conta do Azure Cosmos DB
description: Saiba como configurar políticas de controle de acesso de IP para suporte ao firewall em contas de banco de dados do Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: 26f2131fd62ddc83c2a6d93c4cff557402a88463
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61060770"
---
# <a name="configure-ip-firewall-in-azure-cosmos-db"></a>Configurar o firewall de IP no Azure Cosmos DB

Você pode proteger os dados armazenados na sua conta do Azure Cosmos DB usando firewalls de IP. O Azure Cosmos DB é compatível com controles de acesso baseados em IP para suporte de firewall de entrada. Você pode definir um firewall IP na conta do Azure Cosmos DB usando uma das seguintes maneiras:

* No portal do Azure
* Declarativamente usando um modelo do Azure Resource Manager
* Programaticamente por meio da CLI do Azure ou do Azure PowerShell atualizando a propriedade **ipRangeFilter**

## <a id="configure-ip-policy"></a> Configurar um firewall IP usando o portal do Azure

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

Você pode habilitar o acesso ao portal do Azure selecionando a opção **Permitir acesso do Portal do Azure**, conforme mostrado na captura de tela a seguir: 

![Captura de tela mostrando como habilitar o acesso ao portal do Azure](./media/how-to-configure-firewall/enable-azure-portal.png)

### <a name="allow-requests-from-global-azure-datacenters-or-other-sources-within-azure"></a>Permitir solicitações de datacenters do Azure global ou de outras fontes dentro do Azure

Se você acessar sua conta do Azure Cosmos DB a partir de serviços que não fornecem um IP estático (por exemplo, o Azure Stream Analytics e o Azure Functions), ainda é possível usar o firewall de IP para limitar o acesso. Para permitir o acesso à conta do Azure Cosmos DB desses serviços, adicione o endereço IP 0.0.0.0 à lista de endereços IP permitidos. O endereço 0.0.0.0 restringe as solicitações à sua conta do Azure Cosmos DB do intervalo IP do datacenter do Azure. Essa configuração não permite o acesso de nenhum outro intervalo de IP à sua conta do Azure Cosmos DB.

> [!NOTE]
> Essa opção configura o firewall para permitir todas as solicitações do Azure, incluindo solicitações de assinaturas de outros clientes implantados no Azure. A lista de IPs permitidos por essa opção é ampla, limitando a eficácia de uma política de firewall. Use essa opção somente se suas solicitações não forem originadas de IPs ou sub-redes estáticas em redes virtuais. A escolha desta opção permite automaticamente o acesso do portal do Azure, porque o portal do Azure é implantado no Azure.

Você pode habilitar o acesso ao portal do Azure selecionando a opção **Aceitar conexões de dentro do datacenter público do Azure**, conforme mostrado na captura de tela a seguir: 

![Captura de tela mostrando como abrir a página do Firewall no Portal do Azure](./media/how-to-configure-firewall/enable-azure-services.png)

### <a name="requests-from-your-current-ip"></a>Solicitações do seu IP atual

Para simplificar o desenvolvimento, o portal do Azure ajuda a identificar e adicionar o IP de sua máquina cliente à lista permitida. Os aplicativos que executam sua máquina podem acessar sua conta do Azure Cosmos DB. 

O portal detecta automaticamente o endereço IP do cliente. Pode ser o endereço IP do cliente da sua máquina ou o endereço IP do seu gateway de rede. Certifique-se de remover este endereço IP antes de levar suas cargas de trabalho para produção. 

Para adicionar seu IP atual à lista de IPs, selecione **Adicionar me IP atual**. Em seguida, selecione **Salvar**.

![Captura de tela mostrando como definir as configurações do firewall para o IP atual](./media/how-to-configure-firewall/enable-current-ip.png)

### <a name="requests-from-cloud-services"></a>Solicitações dos serviços de nuvem

No Azure, os serviços de nuvem são uma maneira comum de hospedar a lógica do serviço de camada intermediária usando o Azure Cosmos DB. Para habilitar o acesso à sua conta do Azure Cosmos DB a partir de um serviço de nuvem, você deve adicionar o endereço IP público do serviço de nuvem à lista permitida de endereços IP associados à sua conta do Azure Cosmos DB [ configurando a política de controle de acesso IP](#configure-ip-policy). Isso garante que todas as instâncias de função dos serviços de nuvem tenham acesso à sua conta do Azure Cosmos DB. 

Você pode recuperar endereços IP de seus serviços de nuvem no Portal do Azure, conforme mostra a captura de tela a seguir:

![Captura de tela mostrando o endereço IP público de um serviço de nuvem exibido no Portal do Azure](./media/how-to-configure-firewall/public-ip-addresses.png)

Quando você dimensiona seu serviço de nuvem adicionando instâncias de função, essas novas instâncias automaticamente terão acesso à conta do Azure Cosmos DB, pois fazem parte do mesmo serviço de nuvem.

### <a name="requests-from-virtual-machines"></a>Solicitações de máquinas virtuais

Você também pode usar [máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/) ou [conjuntos de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) para hospedar serviços da camada intermediária usando o Azure Cosmos DB. Para configurar sua conta do Cosmos DB para permitir acesso de máquinas virtuais, você deve configurar o endereço IP público da máquina virtual e / ou escala da máquina virtual definida como um dos endereços IP permitidos para sua conta do Azure Cosmos DB [configurando o Política de controle de acesso IP](#configure-ip-policy). 

Você pode recuperar endereços IP de máquinas virtuais no Portal do Azure, conforme mostra a captura de tela a seguir:

![Captura de tela mostrando o endereço IP público de uma máquina virtual exibida no Portal do Azure](./media/how-to-configure-firewall/public-ip-addresses-dns.png)

Quando você adiciona instâncias de máquina virtual ao grupo, elas recebem automaticamente acesso à sua conta do Azure Cosmos DB.

### <a name="requests-from-the-internet"></a>Solicitações da Internet

Quando você acessa sua conta do Azure Cosmos DB a partir de um computador na Internet, o endereço IP do cliente ou o intervalo de endereços IP da máquina deve ser adicionado à lista permitida de endereços IP da sua conta.

## <a id="configure-ip-firewall-arm"></a>Configurar um firewall IP usando um modelo do Resource Manager

Para configurar o controle de acesso à sua conta do Azure Cosmos DB, certifique-se de que o modelo do Gerenciador de Recursos especifique o atributo **ipRangeFilter** com uma lista de intervalos de IP permitidos. Por exemplo, adicione o seguinte código JSON ao seu modelo:

```json
   {
     "apiVersion": "2015-04-08",
     "type": "Microsoft.DocumentDB/databaseAccounts",
     "kind": "GlobalDocumentDB",
     "name": "[parameters('databaseAccountName')]",
     "location": "[resourceGroup().location]",
     "properties": {
       "databaseAccountOfferType": "Standard",
       "name": "[parameters('databaseAccountName')]",
       "ipRangeFilter":"183.240.196.255,104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
     }
   }
```

## <a id="configure-ip-firewall-cli"></a>Configurar uma política de controle de acesso IP usando a CLI do Azure

O comando a seguir mostra como criar uma conta do Azure Cosmos DB com o controle de acesso IP: 

```azurecli-interactive

name="<Azure Cosmos DB account name>"
resourceGroupName="<Resource group name>"

az cosmosdb create \
  --name $name \
  --kind GlobalDocumentDB \
  --resource-group $resourceGroupName \
  --max-interval 10 \
  --max-staleness-prefix 200 \
  --ip-range-filter "183.240.196.255,104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
```

Para atualizar as configurações de firewall para uma conta existente, execute o seguinte comando:

```azurecli-interactive
az cosmosdb update \
      --name $name \
      --resource-group $resourceGroupName \
      --ip-range-filter "183.240.196.255,104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
```

## <a id="troubleshoot-ip-firewall"></a>Solucione problemas com uma política de controle de acesso IP

Você pode solucionar problemas com uma política de controle de acesso IP usando as seguintes opções: 

### <a name="azure-portal"></a>Portal do Azure 
Ao ativar uma política de controle de acesso IP para sua conta do Azure Cosmos DB, você bloqueia todas as solicitações para sua conta de máquinas fora da lista permitida de intervalos de endereços IP. Para habilitar operações de plano de dados do portal, como navegar em contêineres e consultar documentos, você precisa permitir explicitamente o acesso ao portal do Azure usando o painel **Firewall** no portal.

### <a name="sdks"></a>SDKs 
Quando você acessa recursos do Microsoft Azure Cosmos DB usando SDKs de computadores que não estão na lista permitida, uma resposta genérica **403 Proibido** é retornada sem detalhes adicionais. Verifique a lista de IPs permitidos para sua conta e verifique se a configuração de política correta está aplicada à sua conta do Azure Cosmos DB. 

### <a name="source-ips-in-blocked-requests"></a>IPs de origem em solicitações bloqueadas
Ative o log de diagnóstico na sua conta do Azure Cosmos DB. Esses logs mostram cada solicitação e resposta. As mensagens relacionadas ao firewall são registradas com um código de retorno 403. Ao filtrar essas mensagens, você pode ver os IPs de origem das solicitações bloqueadas. Confira [Log de diagnósticos do Azure Cosmos DB](logging.md).

### <a name="requests-from-a-subnet-with-a-service-endpoint-for-azure-cosmos-db-enabled"></a>Solicitações de uma sub-rede com um ponto de extremidade de serviço para o Azure Cosmos DB ativado
Solicitações de uma sub-rede em uma rede virtual que possui um ponto de extremidade de serviço para o Azure Cosmos DB habilitado envia a identidade da rede virtual e da sub-rede às contas do Azure Cosmos DB. Essas solicitações não têm o IP público da origem, portanto, os filtros IP as rejeitam. Para permitir acesso de sub-redes específicas em redes virtuais, adicione uma lista de controle de acesso, conforme descrito em [Como configurar a rede virtual e o acesso baseado em sub-rede para sua conta do Azure Cosmos DB](how-to-configure-vnet-service-endpoint.md). Pode levar até 15 minutos para as regras de firewall serem aplicadas.


## <a name="next-steps"></a>Próximos passos

Para configurar um ponto de extremidade de serviço de rede virtual para sua conta do Azure Cosmos DB, consulte os seguintes artigos:

* [Controle de acesso à rede virtual e sub-rede para sua conta do Azure Cosmos DB](vnet-service-endpoint.md)
* [Configurar rede virtual e sub-rede com base em acesso para sua conta do Azure Cosmos DB](how-to-configure-vnet-service-endpoint.md)



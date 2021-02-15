---
title: Usar pontos de extremidade privados
titleSuffix: Azure SignalR Service
description: Visão geral de pontos de extremidade privados para acesso seguro ao serviço de Signaler do Azure de redes virtuais.
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: 92e93c3746308d2d6c1a489efc6b5c866b0ad2d9
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98682623"
---
# <a name="use-private-endpoints-for-azure-signalr-service"></a>Usar pontos de extremidade privados para o serviço de Signaler do Azure

Você pode usar [pontos de extremidade privados](../private-link/private-endpoint-overview.md) para o serviço de Signaler do Azure para permitir que clientes em uma rede virtual (VNet) acessem dados com segurança por meio de um [link privado](../private-link/private-link-overview.md). O ponto de extremidade privado usa um endereço IP do espaço de endereço de VNet para seu serviço de Signaler do Azure. O tráfego de rede entre os clientes na VNet e o serviço de Signaler do Azure atravessa um link privado na rede de backbone da Microsoft, eliminando a exposição da Internet pública.

O uso de pontos de extremidade privados para o serviço de Signaler do Azure permite que você:

- Proteja seu serviço de Signaler do Azure usando o controle de acesso à rede para bloquear todas as conexões no ponto de extremidade público do serviço de Signaler do Azure.
- Aumente a segurança para a VNet (rede virtual), permitindo que você bloqueie vazamento de dados da VNet.
- Conecte-se com segurança aos serviços de Signaler do Azure de redes locais que se conectam à VNet usando [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [expressroute ao qual](../expressroute/expressroute-locations.md) com emparelhamento privado.

## <a name="conceptual-overview"></a>Visão geral conceitual

![Visão geral de pontos de extremidade privados para o serviço do Azure Signalr](media/howto-private-endpoints/private-endpoint-overview.png)

Um ponto de extremidade privado é uma interface de rede especial para um serviço do Azure em sua [rede virtual](../virtual-network/virtual-networks-overview.md) (VNet). Quando você cria um ponto de extremidade privado para o serviço de Signaler do Azure, ele fornece conectividade segura entre clientes em sua VNet e seu serviço. O ponto de extremidade privado recebe um endereço IP do intervalo de endereços IP de sua VNet. A conexão entre o ponto de extremidade privado e o serviço de Signaler do Azure usa um link privado seguro.

Os aplicativos na VNet podem se conectar ao serviço de Signaler do Azure por meio do ponto de extremidade privado diretamente, **usando as mesmas cadeias de conexão e mecanismos de autorização que eles usariam de outra forma**. Os pontos de extremidade privados podem ser usados com todos os protocolos compatíveis com o serviço de Signaler do Azure, incluindo a API REST.

Quando você cria um ponto de extremidade privado para um serviço de Signaler do Azure em sua VNet, uma solicitação de consentimento é enviada para aprovação para o proprietário do serviço de Signaler do Azure. Se o usuário que solicita a criação do ponto de extremidade privado também for um proprietário do serviço de Signaler do Azure, essa solicitação de consentimento será aprovada automaticamente.

Os proprietários do serviço de Signaler do Azure podem gerenciar solicitações de consentimento e os pontos de extremidade privados, por meio da guia '*pontos de extremidade particulares*' para o serviço de signaler do Azure no [portal do Azure](https://portal.azure.com).

> [!TIP]
> Se você quiser restringir o acesso ao serviço de Signaler do Azure por meio do ponto de extremidade privado somente, [Configure o controle de acesso à rede](howto-network-access-control.md#managing-network-access-control) para negar ou controlar o acesso por meio do ponto de extremidade público.

### <a name="connecting-to-private-endpoints"></a>Conectando-se a pontos de extremidade privados

Os clientes em uma VNet usando o ponto de extremidade privado devem usar a mesma cadeia de conexão para o serviço de Signaler do Azure, como clientes que se conectam ao ponto de extremidade público. Nós confiamos na resolução DNS para rotear automaticamente as conexões da VNet para o serviço de Signalr do Azure por meio de um link privado.

> [!IMPORTANT]
> Use a mesma cadeia de conexão para se conectar ao serviço de Signaler do Azure usando pontos de extremidade privados, como você usaria o contrário. Não se conecte ao serviço de sinalizador do Azure usando sua `privatelink` URL de subdomínio.

Criamos uma [zona DNS privada](../dns/private-dns-overview.md) anexada à VNet com as atualizações necessárias para os pontos de extremidade privados, por padrão. No entanto, se você estiver usando seu próprio servidor DNS, talvez seja necessário fazer alterações adicionais na configuração do DNS. A seção sobre o [DNS é alterada](#dns-changes-for-private-endpoints) abaixo descreve as atualizações necessárias para pontos de extremidade privados.

## <a name="dns-changes-for-private-endpoints"></a>Alterações de DNS para pontos de extremidade particulares

Quando você cria um ponto de extremidade privado, o registro de recurso DNS CNAME para o serviço de Signaler do Azure é atualizado para um alias em um subdomínio com o prefixo `privatelink` . Por padrão, também criamos uma [zona DNS privada](../dns/private-dns-overview.md), correspondente ao `privatelink` subdomínio, com os registros de recurso de DNS a para os pontos de extremidade privados.

Quando você resolve o nome de domínio do serviço de Signaler do Azure de fora da VNet com o ponto de extremidade privado, ele é resolvido para o ponto de extremidade público do serviço de Signaler do Azure. Quando resolvido da VNet que hospeda o ponto de extremidade privado, o nome de domínio é resolvido para o endereço IP do ponto de extremidade privado.

Para o exemplo ilustrado acima, os registros de recurso de DNS para o serviço de Signaler do Azure ' foobar ', quando resolvidos de fora da VNet que hospeda o ponto de extremidade privado, serão:

| Nome                                                  | Type  | Valor                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``foobar.service.signalr.net``                        | CNAME | ``foobar.privatelink.service.signalr.net``            |
| ``foobar.privatelink.service.signalr.net``            | Um     | \<Azure SignalR Service public IP address\>           |

Como mencionado anteriormente, você pode negar ou controlar o acesso para clientes fora da VNet por meio do ponto de extremidade público usando o controle de acesso à rede.

Os registros de recurso DNS para ' foobar ', quando resolvidos por um cliente na VNet que hospeda o ponto de extremidade privado, serão:

| Nome                                                  | Type  | Valor                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``foobar.service.signalr.net``                        | CNAME | ``foobar.privatelink.service.signalr.net``            |
| ``foobar.privatelink.service.signalr.net``            | Um     | 10.1.1.5                                              |

Essa abordagem habilita o acesso ao serviço de Signaler do Azure **usando a mesma cadeia de conexão** para clientes na vnet que hospedam os pontos de extremidade privados, bem como clientes fora da vnet.

Se você estiver usando um servidor DNS personalizado em sua rede, os clientes deverão ser capazes de resolver o FQDN do ponto de extremidade do serviço de Signaler do Azure para o endereço IP do ponto de extremidade privado. Você deve configurar o servidor DNS para delegar seu subdomínio de vínculo privado à zona DNS privada para a VNet ou configurar os registros a para `foobar.privatelink.service.signalr.net` com o endereço IP do ponto de extremidade privado.

> [!TIP]
> Ao usar um servidor DNS personalizado ou local, você deve configurar o servidor DNS para resolver o nome do serviço de Signaler do Azure no `privatelink` subdomínio para o endereço IP do ponto de extremidade privado. Você pode fazer isso delegando o `privatelink` subdomínio à zona DNS privada da VNet ou configurando a zona DNS no servidor DNS e adicionando os registros a do DNS.

O nome de zona DNS recomendado para pontos de extremidade privados para o serviço de Signaler do Azure é: `privatelink.service.signalr.net` .

Para obter mais informações sobre como configurar seu próprio servidor DNS para dar suporte a pontos de extremidade privados, consulte os seguintes artigos:

- [Resolução de nomes para recursos em redes virtuais do Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [Configuração de DNS para pontos de extremidade privados](../private-link/private-endpoint-overview.md#dns-configuration)

## <a name="create-a-private-endpoint"></a>Criar um ponto de extremidade privado

### <a name="create-a-private-endpoint-along-with-a-new-azure-signalr-service-in-the-azure-portal"></a>Criar um ponto de extremidade privado junto com um novo serviço de Signaler do Azure no portal do Azure

1. Ao criar um novo serviço de Signaler do Azure, selecione a guia **rede** . Escolha **ponto de extremidade privado** como método de conectividade.

    ![Criar serviço do Azure Signalr – guia rede](media/howto-private-endpoints/portal-create-blade-networking-tab.png)

1. Clique em **Adicionar**. Preencha a assinatura, o grupo de recursos, o local e o nome do novo ponto de extremidade privado. Escolha uma rede virtual e uma sub-rede.

    ![Criar serviço de Signaler do Azure-Adicionar ponto de extremidade privado](media/howto-private-endpoints/portal-create-blade-add-private-endpoint.png)

1. Clique em **Revisar + Criar**.

### <a name="create-a-private-endpoint-for-an-existing-azure-signalr-service-in-the-azure-portal"></a>Criar um ponto de extremidade privado para um serviço de Signaler do Azure existente no portal do Azure

1. Vá para o serviço do Azure Signalr.

1. Clique no menu configurações chamado **conexões de ponto de extremidade privadas**.

1. Clique no botão **+ ponto de extremidade privado** na parte superior.

    ![Folha conexões de ponto de extremidade privado](media/howto-private-endpoints/portal-private-endpoint-connections-blade.png)

1. Preencha a assinatura, o grupo de recursos, o nome do recurso e a região para o novo ponto de extremidade privado.
    
    ![Criar ponto de extremidade privado-noções básicas](media/howto-private-endpoints/portal-create-private-endpoint-basics.png)

1. Escolha o recurso do serviço de Signaler do Azure de destino.

    ![Criar ponto de extremidade privado-recurso](media/howto-private-endpoints/portal-create-private-endpoint-resource.png)

1. Escolher rede virtual de destino

    ![Criar ponto de extremidade privado-configuração](media/howto-private-endpoints/portal-create-private-endpoint-configuration.png)

1. Clique em **Revisar + Criar**.

### <a name="create-a-private-endpoint-using-azure-cli"></a>Criar um ponto de extremidade privado usando a CLI do Azure

1. Fazer logon na CLI do Azure
    ```azurecli
    az login
    ```
1. Selecionar sua Assinatura do Azure
    ```azurecli
    az account set --subscription {AZURE SUBSCRIPTION ID}
    ```
1. Criar um grupo de recursos
    ```azurecli
    az group create -n {RG} -l {AZURE REGION}
    ```
1. Registrar Microsoft. SignalRService como um provedor
    ```azurecli
    az provider register -n Microsoft.SignalRService
    ```
1. Criar um novo serviço de Signaler do Azure
    ```azurecli
    az signalr create --name {NAME} --resource-group {RG} --location {AZURE REGION} --sku Standard_S1
    ```
1. Criar uma rede virtual
    ```azurecli
    az network vnet create --resource-group {RG} --name {vNet NAME} --location {AZURE REGION}
    ```
1. Adicionar uma sub-rede
    ```azurecli
    az network vnet subnet create --resource-group {RG} --vnet-name {vNet NAME} --name {subnet NAME} --address-prefixes {addressPrefix}
    ```
1. Desabilitar Políticas de Rede Virtual
    ```azurecli
    az network vnet subnet update --name {subnet NAME} --resource-group {RG} --vnet-name {vNet NAME} --disable-private-endpoint-network-policies true
    ```
1. Adicionar uma Zona DNS Privado
    ```azurecli
    az network private-dns zone create --resource-group {RG} --name privatelink.service.signalr.net
    ```
1. Vincular Zona DNS Privado à Rede Virtual
    ```azurecli
    az network private-dns link vnet create --resource-group {RG} --virtual-network {vNet NAME} --zone-name privatelink.service.signalr.net --name {dnsZoneLinkName} --registration-enabled true
    ```
1. Criar um Ponto de Extremidade Privado (Aprovar Automaticamente)
    ```azurecli
    az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.SignalRService/SignalR/{NAME}" --group-ids signalr --connection-name {Private Link Connection Name} --location {AZURE REGION}
    ```
1. Criar um Ponto de Extremidade Privado (Solicitar Aprovação Manualmente)
    ```azurecli
    az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.SignalRService/SignalR/{NAME}" --group-ids signalr --connection-name {Private Link Connection Name} --location {AZURE REGION} --manual-request
    ```
1. Mostrar Status de Conexão
    ```azurecli
    az network private-endpoint show --resource-group {RG} --name {Private Endpoint Name}
    ```

## <a name="pricing"></a>Preços

Para obter detalhes de preço, confira [Preço do Link Privado do Azure](https://azure.microsoft.com/pricing/details/private-link).

## <a name="known-issues"></a>Problemas conhecidos

Tenha em mente os seguintes problemas conhecidos sobre pontos de extremidade privados para o serviço de Signaler do Azure

### <a name="free-tier"></a>Camada gratuita

Você não pode criar nenhum ponto de extremidade privado para a camada gratuita do serviço de Signaler do Azure.

### <a name="access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Restrições de acesso para clientes no VNets com pontos de extremidade privados

Os clientes no VNets com pontos de extremidade privados existentes enfrentam restrições ao acessar outras instâncias do serviço de Signaler do Azure que têm pontos de extremidade privados. Por exemplo, suponha que uma VNet N1 tenha um ponto de extremidade privado para uma instância do serviço de Signaler do Azure S1. Se o serviço de Signaler do Azure S2 tiver um ponto de extremidade privado em um N2 de VNet, os clientes na VNet N1 também deverão acessar o serviço de Signaler do Azure S2 usando um ponto de extremidade privado. Se o serviço de Signaler do Azure S2 não tiver nenhum ponto de extremidade privado, os clientes na VNet N1 poderão acessar o serviço de Signaler do Azure nessa conta sem um ponto de extremidade privado.

Essa restrição é um resultado das alterações de DNS feitas quando o serviço de sinalizador do Azure S2 cria um ponto de extremidade privado.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>Regras de grupo de segurança de rede para sub-redes com pontos de extremidade privados

No momento, não é possível configurar as regras do NSG ( [grupo de segurança de rede](../virtual-network/network-security-groups-overview.md) ) e as rotas definidas pelo usuário para pontos de extremidade privados. As regras de NSG aplicadas à sub-rede que hospeda o ponto de extremidade privado são aplicadas ao ponto de extremidade privado. Uma solução alternativa limitada para esse problema é implementar suas regras de acesso para pontos de extremidade privados nas sub-redes de origem, embora essa abordagem possa exigir uma sobrecarga de gerenciamento maior.

## <a name="next-steps"></a>Próximas etapas

- [Configurar o controle de acesso à rede](howto-network-access-control.md)
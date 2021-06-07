---
title: Integração dos Hubs de Eventos do Azure com o Serviço de Link Privado do Azure
description: Saiba como integrar os Hubs de Eventos do Azure com o Serviço de Link Privado do Azure
ms.date: 08/22/2020
ms.topic: article
ms.openlocfilehash: 996779e103dae2d2d950f447d2ac72667fc9e754
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94427744"
---
# <a name="allow-access-to-azure-event-hubs-namespaces-via-private-endpoints"></a>Permitir acesso aos namespaces dos hubs de eventos do Azure por meio de pontos de extremidade privados 
O Serviço de Link Privado do Azure permite acessar os Serviços do Azure (por exemplo, os Hubs de Eventos do Azure, o Armazenamento do Azure e o Azure Cosmos DB) e serviços de parceiros/clientes hospedados no Azure em um **ponto de extremidade privado** em sua rede virtual.

O ponto de extremidade privado é uma interface de rede que conecta você de forma privada e segura a um serviço com tecnologia do Link Privado do Azure. O ponto de extremidade privado usa um endereço IP privado de sua rede virtual, efetivamente colocando o serviço em sua rede virtual. Todo o tráfego para o serviço pode ser roteado por meio do ponto de extremidade privado; assim, nenhum gateway, nenhum dispositivo NAT, nenhuma conexão ExpressRoute ou VPN e nenhum endereço IP público é necessário. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Você pode se conectar a uma instância de um recurso do Azure, fornecendo o nível mais alto de granularidade no controle de acesso.

Para obter mais informações, confira [O que é o Link Privado do Azure?](../private-link/private-link-overview.md)

> [!WARNING]
> Implementar pontos de extremidade privados pode impedir que outros serviços do Azure interajam com os Hubs de Eventos.  Solicitações que estão bloqueadas incluem as de outros serviços do Azure, do portal do Azure, de registro em log e serviços de métricas e assim por diante. Como exceção, você pode permitir o acesso a recursos de hubs de eventos de determinados serviços confiáveis, mesmo quando os pontos de extremidade privados estiverem habilitados. Para obter uma lista de serviços confiáveis, consulte [serviços confiáveis](#trusted-microsoft-services).

>[!NOTE]
> Esse recurso tem suporte para as camadas **Standard** e **dedicada** . Não há suporte na camada **básica** .

## <a name="add-a-private-endpoint-using-azure-portal"></a>Inclusão de um ponto de extremidade privado através do portal do Azure

### <a name="prerequisites"></a>Pré-requisitos

Para integrar um namespace de Hubs de Eventos com o Link Privado do Azure, você precisará das seguintes entidades ou permissões:

- Um namespace dos Hubs de Eventos.
- Uma rede virtual do Azure.
- Uma sub-rede na rede virtual. Você pode usar a sub-rede **padrão** . 
- Permissões de proprietário ou colaborador para o namespace e a rede virtual.

Seu ponto de extremidade privado e a rede virtual devem estar na mesma região. Quando você selecionar uma região para o ponto de extremidade privado usando o portal, ele filtrará automaticamente apenas as redes virtuais que estiverem nessa região. O namespace pode estar em uma região diferente.

Seu ponto de extremidade privado usa um endereço IP privado em sua rede virtual.

### <a name="steps"></a>Etapas
Se você já tem um namespace do Hubs de Eventos, crie uma conexão de link privado seguindo estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com). 
2. Na barra de pesquisa, digite **hubs de eventos**.
3. Selecione o **namespace** na lista à qual você deseja adicionar um ponto de extremidade privado.
4. Selecione **rede** em **configurações** no menu à esquerda.

    > [!NOTE]
    > Você vê a guia **rede** somente para namespaces **padrão** ou **dedicados** . 

    :::image type="content" source="./media/private-link-service/selected-networks-page.png" alt-text="Guia redes – opção redes selecionadas" lightbox="./media/private-link-service/selected-networks-page.png":::    

    > [!NOTE]
    > Por padrão, a opção **redes selecionadas** é selecionada. Se você não especificar uma regra de firewall IP ou adicionar uma rede virtual, o namespace poderá ser acessado via Internet pública. 
1. Selecione a guia **Conexões de ponto de extremidade privado** na parte superior da página. 
1. Selecione o botão **+ Ponto de Extremidade Privado**, na parte superior da página.

    :::image type="content" source="./media/private-link-service/private-link-service-3.png" alt-text="Página rede – guia conexões de ponto de extremidade privado – adicionar link de ponto de extremidade privado":::
7. Na página **Básico**, siga estas etapas: 
    1. Selecione a **Assinatura do Azure** na qual você quer criar o ponto de extremidade privado. 
    2. Selecione o **grupo de recursos** para o recurso de ponto de extremidade privado.
    3. Insira um **nome** para o ponto de extremidade privado. 
    5. Insira uma **região** para o ponto de extremidade privado. Seu ponto de extremidade privado deve estar na mesma região que a sua rede virtual, mas pode estar em uma região diferente do recurso de link privado ao qual você está se conectando. 
    6. Selecione **Avançar: Botão Recurso >** na parte inferior da página.

        ![Criação de um Ponto de Extremidade Privado – Página Básico](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Na página **Recurso**, siga estas etapas:
    1. Para o método de conexão, se selecionar **Conectar-se a um recurso do Azure em meu diretório**, siga estas etapas: 
        1. Selecione a **Assinatura do Azure** na qual existe o seu **namespace de Hubs de Eventos**. 
        2. Em **Tipo de recurso**, selecione **Microsoft.EventHub/namespaces** para o **Tipo de recurso**.
        3. Para **Recurso**, selecione um namespace de Hubs de Eventos na lista suspensa. 
        4. Confirme se o **Sub-recurso de destino** está definido como **namespace**.
        5. Selecione **Avançar: Botão Configuração >** na parte inferior da página. 
        
            ![Criação de um Ponto de Extremidade Privado – Página Recurso](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. Se selecionar **Conectar-se a um recurso do Azure com ID do recurso ou alias**, siga estas etapas:
        1. Insira a **ID do recurso** ou **alias**. Pode ser a ID do recurso ou o alias que alguém compartilhou com você. A maneira mais fácil de obter a ID do recurso é navegar até o namespace do Hubs de Eventos no portal do Azure e copiar a parte do URI a partir de `/subscriptions/`. Veja a imagem a seguir para obter um exemplo. 
        2. Para **Sub-recurso de destino**, digite **namespace**. É o tipo de sub-recurso que seu ponto de extremidade privado pode acessar.
        3. (opcional) Insira uma **mensagem de solicitação**. O proprietário do recurso vê essa mensagem enquanto gerencia a conexão de ponto de extremidade privado.
        4. Em seguida, selecione **Avançar: Botão Configuração >** na parte inferior da página.

            ![Criação de Ponto de Extremidade Privado – Conexão usando a ID do recurso](./media/private-link-service/connect-resource-id.png)
9. Na página **Configuração**, selecione a sub-rede em uma rede virtual na qual você deseja implantar o ponto de extremidade privado. 
    1. Selecione uma **rede virtual**. São listadas somente as redes virtuais na assinatura e na localização selecionadas no momento na lista suspensa. 
    2. Selecione uma **sub-rede** na rede virtual selecionada. 
    3. Selecione **Avançar: Botão Marcas >** na parte inferior da página. 

        ![Criação de Ponto de Extremidade Privado – Página Configuração](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. Na página **Marcas**, crie quaisquer marcas (nomes e valores) que você deseja associar ao recurso de ponto de extremidade privado. Em seguida, selecione o botão **Revisar + criar** na parte inferior da página. 
11. Em **Revisar + criar**, examine todas as configurações e selecione **Criar** para criar o ponto de extremidade privado.
    
    ![Criação de Ponto de Extremidade Privado – Página Revisar e Criar](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Verifique se a conexão de ponto de extremidade privado que você criou aparece na lista de pontos de extremidade. Neste exemplo, o ponto de extremidade privado é aprovado automaticamente porque você se conectou a um recurso do Azure em seu diretório e tem permissões suficientes. 

    ![Ponto de extremidade privado criado](./media/private-link-service/private-endpoint-created.png)

[!INCLUDE [event-hubs-trusted-services](../../includes/event-hubs-trusted-services.md)]

Para permitir que os serviços confiáveis acessem seu namespace, alterne para a guia **firewalls e redes virtuais** na página **rede** e selecione **Sim** para **permitir que os serviços confiáveis da Microsoft ignorem esse firewall?**. 

## <a name="add-a-private-endpoint-using-powershell"></a>Inclusão de um ponto de extremidade privado através do PowerShell
O exemplo a seguir mostra como usar o Azure PowerShell para criar uma conexão de ponto de extremidade privado. Ele não cria um cluster dedicado. Siga as etapas [neste artigo](event-hubs-dedicated-cluster-create-portal.md) para criar um cluster dedicado do Hubs de Eventos. 

```azurepowershell-interactive
# create resource group

$rgName = "<RESOURCE GROUP NAME>"
$vnetlocation = "<VIRTUAL NETWORK LOCATION>"
$vnetName = "<VIRTUAL NETWORK NAME>"
$subnetName = "<SUBNET NAME>"
$namespaceLocation = "<NAMESPACE LOCATION>"
$namespaceName = "<NAMESPACE NAME>"
$peConnectionName = "<PRIVATE ENDPOINT CONNECTION NAME>"

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName $rgName `
                    -Location $vnetlocation `
                    -Name $vnetName `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name $subnetName `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork

# create an event hubs namespace in a dedicated cluster
$namespaceResource = New-AzResource -Location $namespaceLocation `
                                    -ResourceName $namespaceName `
                                    -ResourceGroupName $rgName `
                                    -Sku @{name = "Standard"; capacity = 1} `
                                    -Properties @{clusterArmId = "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/clusters/<EVENT HUBS CLUSTER NAME>"} `
                                    -ResourceType "Microsoft.EventHub/namespaces" -ApiVersion "2018-01-01-preview"

# create private endpoint connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name $peConnectionName `
                                -PrivateLinkServiceId $namespaceResource.ResourceId `
                                -GroupId "namespace"

# get subnet object that you will use later
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $rgName -Name $vnetName
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                                | Where-Object  {$_.Name -eq $subnetName}  
   
# create a private endpoint   
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName  `
                                -Name $vnetName   `
                                -Location $vnetlocation `
                                -Subnet  $subnet   `
                                -PrivateLinkServiceConnection $privateEndpointConnection

(Get-AzResource -ResourceId $namespaceResource.ResourceId -ExpandProperties).Properties


```

### <a name="configure-the-private-dns-zone"></a>Configuração da zona DNS privada
Crie uma zona DNS Privada para o domínio do Hubs de Eventos e crie um link de associação com a rede virtual:

```azurepowershell-interactive
$zone = New-AzPrivateDnsZone -ResourceGroupName $rgName `
                            -Name "privatelink.servicebus.windows.net" 
 
$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $rgName `
                                            -ZoneName "privatelink.servicebus.windows.net" `
                                            -Name "mylink" `
                                            -VirtualNetworkId $virtualNetwork.Id  
 
$networkInterface = Get-AzResource -ResourceId $privateEndpoint.NetworkInterfaces[0].Id -ApiVersion "2019-04-01" 
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
    foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
        Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
        $recordName = $fqdn.split('.',2)[0] 
        $dnsZone = $fqdn.split('.',2)[1] 
        New-AzPrivateDnsRecordSet -Name $recordName -RecordType A -ZoneName "privatelink.servicebus.windows.net"  `
                                -ResourceGroupName $rgName -Ttl 600 `
                                -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $ipconfig.properties.privateIPAddress)  
    } 
}
```

## <a name="manage-private-endpoints-using-azure-portal"></a>Gerenciamento de pontos de extremidade privados usando o portal do Azure

Quando você cria um ponto de extremidade privado, a conexão deve ser aprovada. Se o recurso para o qual você está criando um ponto de extremidade privado estiver em seu diretório, você pode aprovar a solicitação de conexão desde que tenha permissões suficientes. Se estiver se conectando a um recurso do Azure em outro diretório, você deve aguardar o proprietário desse recurso aprovar a sua solicitação de conexão.

Há quatro estados de provisionamento:

| Ação de serviço | Estado de ponto de extremidade privado do consumidor do serviço | Descrição |
|--|--|--|
| Nenhum | Pendente | A conexão é criada manualmente e está pendente de aprovação do proprietário do recurso do Link Privado. |
| Aprovar | Aprovado | A conexão foi aprovada automaticamente ou manualmente e está pronta para ser usada. |
| Rejeitar | Rejeitado | A conexão foi rejeitada pelo proprietário do recurso do link privado. |
| Remover | Desconectado | A conexão foi removida pelo proprietário do recurso do link privado, o ponto de extremidade privado se torna informativo e deve ser excluído para limpeza. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>Aprovação, rejeição ou remoção de uma conexão de ponto de extremidade privado

1. Entre no portal do Azure.
2. Na barra de pesquisa, digite **hubs de eventos**.
3. Selecione o **namespace** que você deseja gerenciar.
4. Selecione a guia **Rede**.
5. Vá até a seção apropriada abaixo conforme a operação desejada: aprovar, rejeitar ou remover.

### <a name="approve-a-private-endpoint-connection"></a>Aprovação de uma conexão de ponto de extremidade privado
1. Se houver conexões pendentes, você verá uma lista de conexão com **Pendente** no estado de provisionamento. 
2. Selecione o **ponto de extremidade privado** que você deseja aprovar
3. Selecione o botão **Aprovar**.

    ![Aprovar ponto de extremidade privado](./media/private-link-service/approve-private-endpoint.png)
4. Na página **Aprovar conexão**, adicione um comentário (opcional) e selecione **Sim**. Se selecionar **Não**, nada acontecerá. 
5. O status da conexão de ponto de extremidade privado exibido na lista muda para **Aprovado**. 

### <a name="reject-a-private-endpoint-connection"></a>Rejeição de uma conexão de ponto de extremidade privado

1. Se houver conexões de ponto de extremidade privado que você deseja rejeitar, seja uma solicitação pendente ou uma conexão existente, selecione a conexão e clique no botão **Rejeitar**.

    ![Rejeitar ponto de extremidade privado](./media/private-link-service/private-endpoint-reject-button.png)
2. Na página **Rejeitar conexão**, adicione um comentário (opcional) e selecione **Sim**. Se selecionar **Não**, nada acontecerá. 
3. O status da conexão de ponto de extremidade privado exibido na lista muda para **Rejeitado**. 

### <a name="remove-a-private-endpoint-connection"></a>Remoção de uma conexão de ponto de extremidade privado

1. Para remover uma conexão de ponto de extremidade privado, selecione-a na lista e selecione **Remover** na barra de ferramentas.
2. Na página **Excluir conexão**, selecione **Sim** para confirmar a exclusão do ponto de extremidade privado. Se selecionar **Não**, nada acontecerá.
3. O status muda para **Desconectado**. Em seguida, o ponto de extremidade desaparecerá da lista.

## <a name="validate-that-the-private-link-connection-works"></a>Validar se a conexão de link privado funciona

Você deve validar se os recursos dentro da rede virtual do ponto de extremidade privado estão se conectando ao namespace de seus hubs de eventos por um endereço IP privado e se eles têm a integração de zona DNS privada correta.

Primeiro, crie uma máquina virtual seguindo as etapas em [Criar uma máquina virtual do Windows no portal do Azure](../virtual-machines/windows/quick-create-portal.md)

Na guia **Rede**: 

1. Especifique a **Rede virtual** e a **Sub-rede**. Selecione a Rede Virtual na qual você implantou o ponto de extremidade privado.
2. Especifique um recurso de **IP Público**.
3. Para o **Grupo de segurança de rede da NIC**, selecione **Nenhum**.
4. Para o **Balanceamento de carga**, selecione **Não**.

Conecte-se à VM, abra a linha de comando e execute o seguinte comando:

```console
nslookup <event-hubs-namespace-name>.servicebus.windows.net
```

Você verá um resultado parecido com o mostrado a seguir. 

```console
Non-authoritative answer:
Name:    <event-hubs-namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <event-hubs-namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Limitações e considerações de design

**Preço**: Para obter informações sobre preço, confira [Preço do Link Privado do Azure](https://azure.microsoft.com/pricing/details/private-link/).

**Limitações**:  O recurso está disponível em todas as regiões públicas do Azure.

**Número máximo de pontos de extremidade privados por namespace do Hubs de Eventos**: 120.

Para saber mais, confira [Serviço de Link Privado do Azure: Limitações](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Link Privado do Azure](../private-link/private-link-service-overview.md)
- Saiba mais sobre o [Hubs de Eventos do Azure](event-hubs-about.md)

---
title: Integração do Barramento de Serviço do Azure com o Serviço de Link Privado do Azure
description: Saiba como integrar o Barramento de Serviço do Azure com o Serviço de Link Privado do Azure
author: spelluru
ms.author: spelluru
ms.date: 10/07/2020
ms.topic: article
ms.openlocfilehash: 66de9a4ff65c73264257cb6f7f215fc15820c95f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94427140"
---
# <a name="allow-access-to-azure-service-bus-namespaces-via-private-endpoints"></a>Permitir acesso aos namespaces do barramento de serviço do Azure por meio de pontos de extremidade privados
O Serviço de Link Privado do Azure permite acessar os Serviços do Azure (por exemplo, o Barramento de Serviço do Azure, o Armazenamento do Azure e o Azure Cosmos DB) e serviços de parceiros/clientes hospedados no Azure em um **ponto de extremidade privado** da sua rede virtual.

> [!IMPORTANT]
> Esse recurso é compatível com a camada **Premium** do Barramento de Serviço do Azure. Para saber mais sobre a camada Premium, confira [Camadas de mensagens Premium e Standard do Barramento de Serviço](service-bus-premium-messaging.md).

O ponto de extremidade privado é uma interface de rede que conecta você de forma privada e segura a um serviço com tecnologia do Link Privado do Azure. O ponto de extremidade privado usa um endereço IP privado de sua VNet, colocando efetivamente em sua VNet. Todo o tráfego para o serviço pode ser roteado por meio do ponto de extremidade privado; assim, nenhum gateway, nenhum dispositivo NAT, nenhuma conexão ExpressRoute ou VPN e nenhum endereço IP público é necessário. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Você pode se conectar a uma instância de um recurso do Azure, fornecendo o nível mais alto de granularidade no controle de acesso.

Para obter mais informações, confira [O que é o Link Privado do Azure?](../private-link/private-link-overview.md)

>[!WARNING]
> Implementar pontos de extremidade privados pode impedir que outros serviços do Azure interajam com Barramento de Serviço. Como exceção, você pode permitir o acesso aos recursos do barramento de serviço de determinados serviços confiáveis, mesmo quando os pontos de extremidade privados estiverem habilitados. Para obter uma lista de serviços confiáveis, consulte [serviços confiáveis](#trusted-microsoft-services).
>
> Os serviços da Microsoft a seguir devem estar em uma rede virtual
> - Serviço de aplicativo do Azure
> - Funções do Azure



## <a name="add-a-private-endpoint-using-azure-portal"></a>Inclusão de um ponto de extremidade privado através do portal do Azure

### <a name="prerequisites"></a>Pré-requisitos

Para integrar um namespace do Barramento de Serviço com o Link Privado do Azure, você precisará das seguintes entidades ou permissões:

- Um namespace do barramento de serviço.
- Uma rede virtual do Azure.
- Uma sub-rede na rede virtual. Você pode usar a sub-rede **padrão** . 
- Permissões de proprietário ou colaborador para o namespace e a rede virtual do Barramento de Serviço.

Seu ponto de extremidade privado e a rede virtual devem estar na mesma região. Quando você selecionar uma região para o ponto de extremidade privado usando o portal, ele filtrará automaticamente apenas as redes virtuais que estiverem nessa região. O namespace do Barramento de Serviço pode estar em uma região diferente. Além disso, seu ponto de extremidade privado usa um endereço IP privado em sua rede virtual.

### <a name="steps"></a>etapas

Se você já tem um namespace, crie um ponto de extremidade privado seguindo estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com). 
2. Na barra de pesquisa, digite **Barramento de Serviço**.
3. Selecione o **namespace** na lista à qual você deseja adicionar um ponto de extremidade privado.
2. No menu à esquerda, selecione a opção **rede** em **configurações**. 

    > [!NOTE]
    > Você vê a guia **rede** somente para namespaces **Premium** .  
    
    Por padrão, a opção **redes selecionadas** é selecionada. Se você não adicionar pelo menos uma regra de firewall IP ou uma rede virtual nessa página, o namespace poderá ser acessado pela Internet pública (usando a chave de acesso).

    :::image type="content" source="./media/service-bus-ip-filtering/default-networking-page.png" alt-text="Página de rede-padrão" lightbox="./media/service-bus-ip-filtering/default-networking-page.png":::
    
    Se você selecionar a opção **todas as redes** , o namespace do barramento de serviço aceitará conexões de qualquer endereço IP (usando a tecla de acesso). Essa configuração padrão é equivalente a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0. 

    ![Firewall – opção "Todas as redes" selecionada](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
5. Para permitir o acesso ao namespace por meio de pontos de extremidade privados, selecione a guia **conexões de ponto final privado** na parte superior da página
6. Selecione o botão **+ Ponto de Extremidade Privado**, na parte superior da página.

    ![Botão Adicionar ponto de extremidade privado](./media/private-link-service/private-link-service-3.png)
7. Na página **Básico**, siga estas etapas: 
    1. Selecione a **Assinatura do Azure** na qual você quer criar o ponto de extremidade privado. 
    2. Selecione o **grupo de recursos** para o recurso de ponto de extremidade privado.
    3. Insira um **nome** para o ponto de extremidade privado. 
    5. Insira uma **região** para o ponto de extremidade privado. Seu ponto de extremidade privado deve estar na mesma região que a sua rede virtual, mas pode estar em uma região diferente do recurso de link privado ao qual você está se conectando. 
    6. Selecione **Avançar: Botão Recurso >** na parte inferior da página.

        ![Criação de um Ponto de Extremidade Privado – Página Básico](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Na página **Recurso**, siga estas etapas:
    1. Para o método de conexão, se selecionar **Conectar-se a um recurso do Azure em meu diretório**, siga estas etapas:   
        1. Selecione a **Assinatura do Azure** na qual está o seu **namespace de Barramento de Serviço**. 
        2. Em **Tipo de recurso**, selecione **Microsoft.ServiceBus/namespaces** para o **Tipo de recurso**.
        3. Para **Recurso**, selecione um namespace de Barramento de Serviço na lista suspensa. 
        4. Confirme se o **Sub-recurso de destino** está definido como **namespace**.
        5. Selecione **Avançar: Botão Configuração >** na parte inferior da página. 
        
            ![Criação de um Ponto de Extremidade Privado – Página Recurso](./media/private-link-service/create-private-endpoint-resource-page.png)
    2. Se selecionar **Conectar-se a um recurso do Azure com ID do recurso ou alias**, siga estas etapas:
        1. Insira a **ID do recurso** ou **alias**. Pode ser a ID do recurso ou o alias que alguém compartilhou com você. A maneira mais fácil de obter a ID do recurso é navegar até o namespace do Barramento de Serviço no portal do Azure e copiar a parte do URI a partir de `/subscriptions/`. Veja a imagem a seguir para obter um exemplo. 
        2. Para **Sub-recurso de destino**, digite **namespace**. É o tipo de sub-recurso que seu ponto de extremidade privado pode acessar. 
        3. (opcional) Insira uma **mensagem de solicitação**. O proprietário do recurso vê essa mensagem enquanto gerencia a conexão de ponto de extremidade privado. 
        4. Em seguida, selecione **Avançar: Botão Configuração >** na parte inferior da página. 

            ![Criação de ponto de extremidade privado – conexão usando a ID do recurso](./media/private-link-service/connect-resource-id.png)
9. Na página **Configuração**, selecione a sub-rede em uma rede virtual na qual você deseja implantar o ponto de extremidade privado. 
    1. Selecione uma **rede virtual**. São listadas somente as redes virtuais na assinatura e na localização selecionadas no momento na lista suspensa. 
    2. Selecione uma **sub-rede** na rede virtual selecionada. 
    3. Selecione **Avançar: Botão Marcas >** na parte inferior da página. 

        ![Criação de Ponto de Extremidade Privado – Página Configuração](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. Na página **Marcas**, crie quaisquer marcas (nomes e valores) que você deseja associar ao recurso de ponto de extremidade privado. Em seguida, selecione o botão **Revisar + criar** na parte inferior da página. 
11. Em **Revisar + criar**, examine todas as configurações e selecione **Criar** para criar o ponto de extremidade privado.
    
    ![Criação de Ponto de Extremidade Privado – Página Revisar e Criar](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Confirme se o ponto de extremidade privado foi criado. Se você for o proprietário do recurso e tiver selecionado a opção **Conectar a um recurso do Azure no meu diretório** como **Método de conexão**, a conexão do ponto de extremidade deverá ser **aprovada automaticamente**. Se estado no estado **pendente**, consulte a seção [Gerenciar pontos de extremidade privados usando o portal do Azure](#manage-private-endpoints-using-azure-portal).

    ![Ponto de extremidade privado criado](./media/private-link-service/private-endpoint-created.png)

[!INCLUDE [service-bus-trusted-services](../../includes/service-bus-trusted-services.md)]

## <a name="add-a-private-endpoint-using-powershell"></a>Inclusão de um ponto de extremidade privado através do PowerShell
O exemplo a seguir mostra como usar o Azure PowerShell para criar uma conexão de ponto de extremidade privado com um namespace de Barramento de Serviço.

Seu ponto de extremidade privado e a rede virtual devem estar na mesma região. O namespace do Barramento de Serviço pode estar em uma região diferente. Além disso, seu ponto de extremidade privado usa um endereço IP privado em sua rede virtual.

```azurepowershell-interactive

$rgName = "<RESOURCE GROUP NAME>"
$vnetlocation = "<VNET LOCATION>"
$vnetName = "<VIRTUAL NETWORK NAME>"
$subnetName = "<SUBNET NAME>"
$namespaceLocation = "<NAMESPACE LOCATION>"
$namespaceName = "<NAMESPACE NAME>"
$peConnectionName = "<PRIVATE ENDPOINT CONNECTION NAME>"

# create resource group
az group create -l $vnetLocation -n $rgName

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

# create premium service bus namespace
$namespaceResource = New-AzResource -Location $namespaceLocation -ResourceName $namespaceName -ResourceGroupName $rgName -Sku @{name = "Premium"; capacity = 1} -Properties @{} -ResourceType "Microsoft.ServiceBus/namespaces" -

# create a private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name $peConnectionName `
                                -PrivateLinkServiceId $namespaceResource.ResourceId `
                                -GroupId "namespace"

# get subnet object that you will use in the next step                                
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $rgName -Name $vnetName
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                                | Where-Object  {$_.Name -eq $subnetName}  
   
# now, create private endpoint   
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName  `
                                -Name $vnetName   `
                                -Location $vnetlocation `
                                -Subnet  $subnet   `
                                -PrivateLinkServiceConnection $privateEndpointConnection

(Get-AzResource -ResourceId $namespaceResource.ResourceId -ExpandProperties).Properties


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
1. Na barra de pesquisa, digite **Barramento de Serviço**.
1. Selecione o **namespace** que você deseja gerenciar.
1. Selecione a guia **Rede**.
5. Vá até a seção apropriada abaixo conforme a operação desejada: aprovar, rejeitar ou remover. 

### <a name="approve-a-private-endpoint-connection"></a>Aprovação de uma conexão de ponto de extremidade privado

1. Se houver conexões pendentes, você verá uma lista de conexão com **Pendente** no estado de provisionamento. 
2. Selecione o **ponto de extremidade privado** que você deseja aprovar
3. Selecione o botão **Aprovar**.

    ![Aprovar ponto de extremidade privado](./media/private-link-service/private-endpoint-approve.png)
4. Na página **Aprovar conexão**, digite um **comentário** opcional e selecione **Sim**. Se selecionar **Não**, nada acontecerá. 

    ![Página Aprovar conexão](./media/private-link-service/approve-connection-page.png)
5. O status da conexão exibido na lista muda para **Aprovado**. 

    ![Status da conexão - aprovado](./media/private-link-service/connection-status-approved.png)

### <a name="reject-a-private-endpoint-connection"></a>Rejeição de uma conexão de ponto de extremidade privado

1. Se houver conexões de ponto de extremidade privado que você deseja rejeitar, seja uma solicitação pendente ou uma conexão existente que foi aprovada anteriormente, selecione a conexão do ponto de extremidade e clique no botão **Rejeitar**.

    ![Botão Rejeitar](./media/private-link-service/private-endpoint-reject.png)
2. Na página **Rejeitar conexão**, digite um comentário opcional e selecione **Sim**. Se selecionar **Não**, nada acontecerá. 

    ![Página Rejeitar conexão](./media/private-link-service/reject-connection-page.png)
3. O status da conexão exibido na lista muda para **Rejeitado**. 

    ![Ponto de extremidade rejeitado](./media/private-link-service/endpoint-rejected.png)


### <a name="remove-a-private-endpoint-connection"></a>Remoção de uma conexão de ponto de extremidade privado

1. Para remover uma conexão de ponto de extremidade privado, selecione-a na lista e selecione **Remover** na barra de ferramentas. 

    ![Botão Remover](./media/private-link-service/remove-endpoint.png)
2. Na página **Excluir conexão**, selecione **Sim** para confirmar a exclusão do ponto de extremidade privado. Se selecionar **Não**, nada acontecerá. 

    ![Página Excluir a conexão](./media/private-link-service/delete-connection-page.png)
3. O status muda para **Desconectado**. Em seguida, o ponto de extremidade desaparecerá da lista. 

## <a name="validate-that-the-private-link-connection-works"></a>Validar se a conexão de link privado funciona

Você deve validar se os recursos dentro da rede virtual do ponto de extremidade privado estão se conectando ao namespace do barramento de serviço por meio de um endereço IP privado e se eles têm a integração de zona DNS privada correta.

Primeiro, crie uma máquina virtual seguindo as etapas em [Criar uma máquina virtual do Windows no portal do Azure](../virtual-machines/windows/quick-create-portal.md)

Na guia **Rede**: 

1. Especifique a **Rede virtual** e a **Sub-rede**. Selecione a Rede Virtual na qual você implantou o ponto de extremidade privado.
2. Especifique um recurso de **IP Público**.
3. Para o **Grupo de segurança de rede da NIC**, selecione **Nenhum**.
4. Para o **Balanceamento de carga**, selecione **Não**.

Conecte-se à VM, abra a linha de comando e execute o seguinte comando:

```console
nslookup <service-bus-namespace-name>.servicebus.windows.net
```

Você verá um resultado parecido com o mostrado a seguir. 

```console
Non-authoritative answer:
Name:    <service-bus-namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <service-bus-namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Limitações e considerações de design

**Preço**: Para obter informações sobre preço, confira [Preço do Link Privado do Azure](https://azure.microsoft.com/pricing/details/private-link/).

**Limitações**:  O recurso está disponível em todas as regiões públicas do Azure.

**Número máximo de pontos de extremidade privados por namespace do Barramento de Serviço**: 120.

Para saber mais, confira [Serviço de Link Privado do Azure: Limitações](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Link Privado do Azure](../private-link/private-link-service-overview.md)
- Saiba mais sobre o [Barramento de Serviço do Azure](service-bus-messaging-overview.md).

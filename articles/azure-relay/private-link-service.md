---
title: Integrar a Retransmissão do Azure ao Serviço de Link Privado do Azure
description: Saiba como integrar a Retransmissão do Azure ao Serviço de Link Privado do Azure
ms.date: 09/24/2020
ms.topic: article
ms.openlocfilehash: 13644082160704ba9918e6bd6257fa314bb463a6
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98134374"
---
# <a name="integrate-azure-relay-with-azure-private-link"></a>Integrar a retransmissão do Azure com o link privado do Azure 
O Serviço de Link Privado do Azure permite acessar os Serviços do Azure (por exemplo, Retransmissão do Azure, Barramento de Serviço do Azure, Hubs de Eventos do Azure, Armazenamento do Microsoft Azure e Azure Cosmos DB) e serviços de parceiros/clientes hospedados no Azure em um **ponto de extremidade privado** da sua rede virtual. Para obter mais informações, confira [O que é o Link Privado do Azure?](../private-link/private-link-overview.md)

Um **ponto de extremidade privado**  é uma interface de rede que permite que suas cargas de trabalho em execução em uma rede virtual se conectem de forma privada e segura a um serviço que tenha um **recurso de link privado** (por exemplo, um namespace de Retransmissão). O ponto de extremidade privado usa um endereço IP privado de sua VNet, colocando efetivamente em sua VNet. Todo o tráfego para o serviço pode ser roteado por meio do ponto de extremidade privado; assim, nenhum gateway, nenhum dispositivo NAT, nenhum ExpressRoute, nenhuma conexão VPN e nenhum endereço IP público é necessário. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Você pode fornecer um nível de granularidade ao controle de acesso que permite conexões a namespaces específicos da Retransmissão do Azure. 


## <a name="add-a-private-endpoint-using-azure-portal"></a>Inclusão de um ponto de extremidade privado através do portal do Azure

### <a name="prerequisites"></a>Pré-requisitos
Para integrar um namespace de retransmissão do Azure com o link privado do Azure, você precisará das seguintes entidades ou permissões:

- Um namespace da Retransmissão do Azure.
- Uma rede virtual do Azure.
- Uma sub-rede na rede virtual.
- Permissões de proprietário ou colaborador na rede virtual.

Seu ponto de extremidade privado e a rede virtual devem estar na mesma região. Quando você selecionar uma região para o ponto de extremidade privado usando o portal, ele filtrará automaticamente apenas as redes virtuais que estiverem nessa região. O namespace pode estar em uma região diferente.

Seu ponto de extremidade privado usa um endereço IP privado em sua rede virtual.

### <a name="steps"></a>Etapas
Para obter instruções detalhadas sobre como criar um novo namespace de Retransmissão do Azure e entidades nele contidas, consulte [Criar um namespace de Retransmissão do Azure usando o portal do Azure](relay-create-namespace-portal.md).

1. Entre no [portal do Azure](https://portal.azure.com). 
2. Na barra de pesquisa, digite **Retransmissões**.
3. Selecione o **namespace** na lista à qual você deseja adicionar um ponto de extremidade privado.
4. Selecione a guia **Rede** em **Configurações**.
5. Selecione a guia **Conexões de ponto de extremidade privado**, na parte superior da página.
6. Selecione o botão **+ Ponto de Extremidade Privado**, na parte superior da página.

    ![Botão Adicionar ponto de extremidade privado](./media/private-link-service/add-private-endpoint-button.png)
7. Na página **Básico**, siga estas etapas: 
    1. Selecione a **Assinatura do Azure** na qual você quer criar o ponto de extremidade privado. 
    2. Selecione o **grupo de recursos** para o recurso de ponto de extremidade privado.
    3. Insira um **nome** para o ponto de extremidade privado. 
    5. Insira uma **região** para o ponto de extremidade privado. Seu ponto de extremidade privado deve estar na mesma região que a sua rede virtual, mas pode estar em uma região diferente do namespace da Retransmissão do Azure ao qual você está se conectando. 
    6. Selecione **Avançar: Botão Recurso >** na parte inferior da página.

        ![Criação de um Ponto de Extremidade Privado – Página Básico](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Na página **Recurso**, siga estas etapas:
    1. Ao selecionar o método de conexão **Conectar-se a um recurso do Azure em meu diretório**, você terá acesso de proprietário ou colaborador ao namespace e, se esse namespace estiver no mesmo diretório que o ponto de extremidade privado, siga estas etapas: 
        1. Selecione a **Assinatura do Azure** na qual está o seu **namespace de Retransmissão do Azure**. 
        2. Em **Tipo de recurso**, selecione **Microsoft.Relay/namespaces** para o **Tipo de recurso**.
        3. Para **Recurso**, selecione um namespace de Retransmissão na lista suspensa. 
        4. Confirme se o **Sub-recurso de destino** está definido como **namespace**.
        5. Selecione **Avançar: Botão Configuração >** na parte inferior da página. 
        
            ![Criação de um Ponto de Extremidade Privado – Página Recurso](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. Se você selecionar **Conectar-se a um recurso do Azure com ID do recurso ou alias** porque o namespace não está no mesmo diretório que o ponto de extremidade privado, siga estas etapas:
        1. Insira a **ID do recurso** ou **alias**. Pode ser a ID do recurso ou o alias que alguém compartilhou com você. A maneira mais fácil de obter a ID do recurso é navegar até o namespace da Retransmissão do Azure no portal do Azure e copiar a parte do URI a partir de `/subscriptions/`. Veja um exemplo: `/subscriptions/000000000-0000-0000-0000-000000000000000/resourceGroups/myresourcegroup/providers/Microsoft.Relay/namespaces/myrelaynamespace.` 
        2. Para **Sub-recurso de destino**, digite **namespace**. É o tipo de sub-recurso que seu ponto de extremidade privado pode acessar.
        3. (opcional) Insira uma **mensagem de solicitação**. O proprietário do recurso vê essa mensagem enquanto gerencia a conexão de ponto de extremidade privado.
        4. Em seguida, selecione **Avançar: Botão Configuração >** na parte inferior da página.

            ![Criação de Ponto de Extremidade Privado – Conexão usando a ID do recurso](./media/private-link-service/connect-resource-id.png)
9. Na página **Configuração**, selecione a sub-rede em uma rede virtual na qual você deseja implantar o ponto de extremidade privado. 
    1. Selecione uma **rede virtual**. São listadas somente as redes virtuais na assinatura e na localização selecionadas no momento na lista suspensa. 
    2. Selecione uma **sub-rede** na rede virtual selecionada. 
    3. Habilite **Integrar com a zona DNS privada** se quiser integrar seu ponto de extremidade privado a uma zona DNS privada. 
    
        Para se conectar em particular com o seu ponto de extremidade privado, você precisa de um registro DNS. Recomendamos que você integre seu ponto de extremidade privado a uma **zona DNS privada**. Você também pode utilizar seus próprios servidores DNS ou criar registros DNS usando os arquivos host em suas máquinas virtuais. Para obter mais informações, consulte [Configuração de DNS do ponto de extremidade privado do Azure](../private-link/private-endpoint-dns.md). Neste exemplo, a opção **Integrar com a zona DNS privada** está selecionada e uma zona DNS privada será criada para você. 
    3. Selecione **Avançar: Botão Marcas >** na parte inferior da página. 

        ![Criação de Ponto de Extremidade Privado – Página Configuração](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. Na página **Marcas**, crie marcas (nomes e valores) que você deseja associar ao ponto de extremidade privado e à zona DNS privada (se você tiver habilitado a opção). Em seguida, selecione o botão **Revisar + criar** na parte inferior da página. 
11. Em **Revisar + criar**, examine todas as configurações e selecione **Criar** para criar o ponto de extremidade privado.
    
    ![Criação de Ponto de Extremidade Privado – Página Revisar e Criar](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Na página **Ponto de extremidade privado**, você pode ver o status da conexão do ponto de extremidade particular. Se você for o proprietário ou tiver acesso de gerenciamento ao namespace de Retransmissão e tiver selecionado a opção **Conectar a um recurso do Azure no meu diretório** como **Método de conexão**, a conexão do ponto de extremidade deverá ser **aprovada automaticamente**. Se estado no estado **pendente**, consulte a seção [Gerenciar pontos de extremidade privados usando o portal do Azure](#manage-private-endpoints-using-azure-portal).

    ![Página do ponto de extremidade privado](./media/private-link-service/private-endpoint-page.png)
13. Navegue de volta para a página de **rede** do **namespace** e alterne para a guia conexões de **ponto de extremidade privado** . Você deve ver o ponto de extremidade privado que você criou. 

    ![Ponto de extremidade privado criado](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>Inclusão de um ponto de extremidade privado através do PowerShell
O exemplo a seguir mostra como usar o Azure PowerShell para criar uma conexão de ponto de extremidade privado com um namespace da Retransmissão do Azure.

Seu ponto de extremidade privado e a rede virtual devem estar na mesma região. O namespace da Retransmissão do Azure pode estar em uma região diferente. Além disso, seu ponto de extremidade privado usa um endereço IP privado em sua rede virtual.

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

# create a relay namespace
$namespaceResource = New-AzResource -Location $namespaceLocation -ResourceName $namespaceName -ResourceGroupName $rgName -Properties @{} -ResourceType "Microsoft.Relay/namespaces" 

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

Quando você cria um ponto de extremidade privado, a conexão deve ser aprovada. Se o recurso (namespace de Retransmissão) para o qual você está criando um ponto de extremidade privado estiver no seu diretório, você poderá aprovar a solicitação de conexão, desde que tenha privilégios de gerenciamento sobre o namespace de Retransmissão. Se estiver se conectando a um namespace de Retransmissão para o qual você não tem acesso de gerenciamento, deverá aguardar que o proprietário aprove a sua solicitação de conexão.

Há quatro estados de provisionamento:

| Ação de serviço | Estado de ponto de extremidade privado do consumidor do serviço | Descrição |
|--|--|--|
| Nenhum | Pendente | A conexão é criada manualmente e está aguardando aprovação do proprietário do namespace de Retransmissão do Azure. |
| Aprovar | Aprovado | A conexão foi aprovada automaticamente ou manualmente e está pronta para ser usada. |
| Rejeitar | Rejeitado | A conexão foi rejeitada pelo proprietário do namespace da Retransmissão do Azure. |
| Remover | Desconectado | A conexão foi removida pelo proprietário do namespace da Retransmissão do Azure, o ponto de extremidade privado se torna informativo e deve ser excluído para limpeza. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>Aprovação, rejeição ou remoção de uma conexão de ponto de extremidade privado

1. Entre no portal do Azure.
1. Na barra de pesquisa, digite **Retransmissão**.
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

### <a name="reject-a-private-endpoint-connection"></a>Rejeição de uma conexão de ponto de extremidade privado

1. Se houver conexões de ponto de extremidade privado que você deseja rejeitar, seja uma solicitação pendente ou uma conexão existente que foi aprovada anteriormente, selecione a conexão do ponto de extremidade e clique no botão **Rejeitar**.

    ![Botão Rejeitar](./media/private-link-service/private-endpoint-reject.png)
2. Na página **Rejeitar conexão**, digite um comentário opcional e selecione **Sim**. Se selecionar **Não**, nada acontecerá. 

    ![Página Rejeitar conexão](./media/private-link-service/reject-connection-page.png)
3. O status da conexão exibido na lista muda para **Rejeitado**.


### <a name="remove-a-private-endpoint-connection"></a>Remoção de uma conexão de ponto de extremidade privado

1. Para remover uma conexão de ponto de extremidade privado, selecione-a na lista e selecione **Remover** na barra de ferramentas. 

    ![Botão Remover](./media/private-link-service/remove-endpoint.png)
2. Na página **Excluir conexão**, selecione **Sim** para confirmar a exclusão do ponto de extremidade privado. Se selecionar **Não**, nada acontecerá. 

    ![Página Excluir a conexão](./media/private-link-service/delete-connection-page.png)
3. O status muda para **Desconectado**. Em seguida, o ponto de extremidade desaparecerá da lista. 

## <a name="validate-that-the-private-link-connection-works"></a>Validar se a conexão de link privado funciona
Você deve validar que os recursos dentro da rede virtual do ponto de extremidade privado estão se conectando ao namespace de retransmissão do Azure por meio de seu endereço IP privado.

Para este teste, crie uma máquina virtual seguindo as etapas em [Criar uma máquina virtual do Windows no portal do Azure](../virtual-machines/windows/quick-create-portal.md)

Na guia **Rede**: 

1. Especifique a **Rede virtual** e a **Sub-rede**. Selecione a Rede Virtual na qual você implantou o ponto de extremidade privado.
2. Especifique um recurso de **IP Público**.
3. Para o **Grupo de segurança de rede da NIC**, selecione **Nenhum**.
4. Para o **Balanceamento de carga**, selecione **Não**.

Conecte-se à VM e abra a linha de comando e execute o seguinte comando:

```console
nslookup <your-relay-namespace-name>.servicebus.windows.net
```

Você verá um resultado parecido com o mostrado a seguir. 

```console
Non-authoritative answer:
Name:    <namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Limitações e considerações de design

### <a name="design-considerations"></a>Considerações sobre o design
- Para obter informações sobre preços, consulte [preços do link privado do Azure](https://azure.microsoft.com/pricing/details/private-link/).

### <a name="limitations"></a>Limitações 
- Número máximo de pontos de extremidade privados por namespace de Retransmissão do Azure: 64.
- Número máximo de namespaces de Retransmissão do Azure com pontos de extremidade privados por assinatura: 64.
- As regras do grupo de segurança de rede (NSG) e as rotas definidas pelo usuário não se aplicam ao ponto de extremidade privado. Para obter mais informações, confira [Serviço do Link Privado do Azure: Limitações](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Link Privado do Azure](../private-link/private-link-service-overview.md)
- Saiba mais sobre [Retransmissão do Azure](relay-what-is-it.md)

---
title: Configurar pontos de extremidade privados para os tópicos ou domínios da grade de eventos do Azure
description: Este artigo descreve como configurar pontos de extremidade privados para o domínio ou tópicos da grade de eventos do Azure.
ms.topic: how-to
ms.date: 11/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: f8e0cfc0a850ae15ea6d03ff6ca8b90003adbfc9
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94916897"
---
# <a name="configure-private-endpoints-for-azure-event-grid-topics-or-domains"></a>Configurar pontos de extremidade privados para os tópicos ou domínios da grade de eventos do Azure
Você pode usar [pontos de extremidade privados](../private-link/private-endpoint-overview.md) para permitir a entrada de eventos diretamente de sua rede virtual para seus tópicos e domínios com segurança por meio de um [link privado](../private-link/private-link-overview.md) sem passar pela Internet pública. O ponto de extremidade privado usa um endereço IP do espaço de endereço de VNet para seu tópico ou domínio. Para obter mais informações conceituais, consulte [segurança de rede](network-security.md).

Este artigo descreve como configurar pontos de extremidade privados para tópicos ou domínios.

## <a name="use-azure-portal"></a>Usar o portal do Azure 
Esta seção mostra como usar o portal do Azure para criar um ponto de extremidade privado para um tópico ou um domínio.

> [!NOTE]
> As etapas mostradas nesta seção são principalmente para tópicos. Você pode usar etapas semelhantes para criar pontos de extremidade privados para **domínios**. 

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até seu tópico ou domínio.
2. Alterne para a guia **rede** da página do tópico. Selecione **+ ponto de extremidade privado** na barra de ferramentas.

    ![Adicionar ponto de extremidade privado](./media/configure-private-endpoints/add-button.png)
2. Uma das páginas **básicas** , siga estas etapas: 
    1. Selecione uma **assinatura do Azure** na qual você deseja criar o ponto de extremidade privado. 
    2. Selecione um **grupo de recursos do Azure** para o ponto de extremidade privado. 
    3. Insira um **nome** para o ponto de extremidade. 
    4. Selecione a **região** para o ponto de extremidade. Seu ponto de extremidade privado deve estar na mesma região que sua rede virtual, mas pode ser em uma região diferente do recurso de link privado (neste exemplo, um tópico de grade de eventos). 
    5. Em seguida, selecione o botão **>de recursos** na parte inferior da página. 

      ![Ponto de extremidade privado-página noções básicas](./media/configure-private-endpoints/basics-page.png)
3. Na página **Recurso**, siga estas etapas: 
    1. Para o método de conexão, se você selecionar **conectar a um recurso do Azure em meu diretório**, siga estas etapas. Este exemplo mostra como se conectar a um recurso do Azure em seu diretório. 
        1. Selecione a **assinatura do Azure** na qual seu **tópico/domínio** existe. 
        1. Para **tipo de recurso**, selecione **Microsoft. EventGrid/topics** ou **Microsoft. EventGrid/Domains** para o **tipo de recurso**.
        2. Para **recurso**, selecione um tópico/domínio na lista suspensa. 
        3. Confirme se o **subrecurso de destino** está definido como **tópico** ou **domínio** (com base no tipo de recurso selecionado).    
        4. Selecione **Avançar: Botão Configuração >** na parte inferior da página. 

            ![Captura de tela que mostra a página "criar um ponto de extremidade privado-recurso".](./media/configure-private-endpoints/resource-page.png)
    2. Se você selecionar **conectar-se a um recurso usando uma ID de recurso ou um alias**, siga estas etapas:
        1. Insira a ID do recurso. Por exemplo: `/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>`.  
        2. Para **recurso**, insira o **tópico** ou o **domínio**. 
        3. adicional Adicione uma mensagem de solicitação. 
        4. Selecione **Avançar: Botão Configuração >** na parte inferior da página. 

            ![Ponto de extremidade privado – página de recursos](./media/configure-private-endpoints/connect-azure-resource-id.png)
4. Na página **Configuração**, selecione a sub-rede em uma rede virtual na qual você deseja implantar o ponto de extremidade privado. 
    1. Selecione uma **rede virtual**. São listadas somente as redes virtuais na assinatura e na localização selecionadas no momento na lista suspensa. 
    2. Selecione uma **sub-rede** na rede virtual selecionada. 
    3. Selecione **Avançar: Botão Marcas >** na parte inferior da página. 

    ![Ponto de extremidade privado – página de configuração](./media/configure-private-endpoints/configuration-page.png)
5. Na página **Marcas**, crie quaisquer marcas (nomes e valores) que você deseja associar ao recurso de ponto de extremidade privado. Em seguida, selecione o botão **Revisar + criar** na parte inferior da página. 
6. Em **Revisar + criar**, examine todas as configurações e selecione **Criar** para criar o ponto de extremidade privado. 

    ![Ponto de extremidade privado – examinar & criar página](./media/configure-private-endpoints/review-create-page.png)
    

### <a name="manage-private-link-connection"></a>Gerenciar conexão de link privado

Quando você cria um ponto de extremidade privado, a conexão deve ser aprovada. Se o recurso para o qual você está criando um ponto de extremidade privado estiver em seu diretório, você pode aprovar a solicitação de conexão desde que tenha permissões suficientes. Se estiver se conectando a um recurso do Azure em outro diretório, você deve aguardar o proprietário desse recurso aprovar a sua solicitação de conexão.

Há quatro estados de provisionamento:

| Ação de serviço | Estado de ponto de extremidade privado do consumidor do serviço | Descrição |
|--|--|--|
| Nenhum | Pendente | A conexão é criada manualmente e está aguardando aprovação do proprietário do recurso de link privado. |
| Aprovar | Aprovado | A conexão foi aprovada automaticamente ou manualmente e está pronta para ser usada. |
| Rejeitar | Rejeitado | A conexão foi rejeitada pelo proprietário do recurso do link privado. |
| Remover | Desconectado | A conexão foi removida pelo proprietário do recurso do link privado, o ponto de extremidade privado se torna informativo e deve ser excluído para limpeza. |
 
###  <a name="how-to-manage-a-private-endpoint-connection"></a>Como gerenciar uma conexão de ponto de extremidade privada
As seções a seguir mostram como aprovar ou rejeitar uma conexão de ponto de extremidade privada. 

1. Entre no [portal do Azure](https://portal.azure.com).
1. Na barra de pesquisa, digite **Tópicos da grade de eventos** ou domínios da grade de **eventos**.
1. Selecione o **tópico** ou **domínio** que você deseja gerenciar.
1. Selecione a guia **Rede**.
1. Se houver conexões pendentes, você verá uma conexão listada com **pendente** no estado de provisionamento. 

### <a name="to-approve-a-private-endpoint"></a>Para aprovar um ponto de extremidade privado
Você pode aprovar um ponto de extremidade privado no estado pendente. Para aprovar, siga estas etapas: 

> [!NOTE]
> As etapas mostradas nesta seção são principalmente para tópicos. Você pode usar etapas semelhantes para aprovar pontos de extremidade privados para **domínios**. 

1. Selecione o **ponto de extremidade privado** que você deseja aprovar e selecione **aprovar** na barra de ferramentas.

    ![Ponto de extremidade privado-estado pendente](./media/configure-private-endpoints/pending.png)
1. Na caixa de diálogo **aprovar conexão** , insira um comentário (opcional) e selecione **Sim**. 

    ![Ponto de extremidade privado-aprovar](./media/configure-private-endpoints/approve.png)
1. Confirme que você vê o status do ponto de extremidade como **aprovado**. 

    ![Ponto de extremidade privado – Estado aprovado](./media/configure-private-endpoints/approved-status.png)

### <a name="to-reject-a-private-endpoint"></a>Para rejeitar um ponto de extremidade privado
Você pode rejeitar um ponto de extremidade privado que está no estado pendente ou aprovado. Para rejeitar, siga estas etapas: 

> [!NOTE]
> As etapas mostradas nesta seção são para tópicos. Você pode usar etapas semelhantes para rejeitar pontos de extremidade privados para **domínios**. 

1. Selecione o **ponto de extremidade privado** que você deseja rejeitar e selecione **rejeitar** na barra de ferramentas.

    ![Captura de tela que mostra a "rede – conexões de ponto de extremidade privadas" com "rejeitar" selecionado.](./media/configure-private-endpoints/reject-button.png)
1. Na caixa de diálogo **rejeitar conexão** , insira um comentário (opcional) e selecione **Sim**. 

    ![Ponto de extremidade privado-rejeitar](./media/configure-private-endpoints/reject.png)
1. Confirme que você vê o status do ponto de extremidade como **rejeitado**. 

    ![Ponto de extremidade privado – estado rejeitado](./media/configure-private-endpoints/rejected-status.png)

    > [!NOTE]
    > Você não pode aprovar um ponto de extremidade privado no portal do Azure depois que ele for rejeitado. 


## <a name="use-azure-cli"></a>Usar a CLI do Azure
Para criar um ponto de extremidade privado, use o método [AZ Network Private-Endpoint Create](/cli/azure/network/private-endpoint?#az-network-private-endpoint-create) , conforme mostrado no exemplo a seguir:

```azurecli-interactive
az network private-endpoint create \
    --resource-group <RESOURECE GROUP NAME> \
    --name <PRIVATE ENDPOINT NAME> \
    --vnet-name <VIRTUAL NETWORK NAME> \
    --subnet <SUBNET NAME> \
    --private-connection-resource-id "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME> \
    --connection-name <PRIVATE LINK SERVICE CONNECTION NAME> \
    --location <LOCATION> \
    --group-ids topic
```

Para obter descrições dos parâmetros usados no exemplo, consulte a documentação para [AZ Network Private-Endpoint Create](/cli/azure/network/private-endpoint?#az-network-private-endpoint-create). Alguns pontos a serem observados neste exemplo são: 

- Para `private-connection-resource-id` , especifique a ID de recurso do **tópico** ou do **domínio**. O exemplo anterior usa o tópico Type:.
- para `group-ids` , especifique `topic` ou `domain` . No exemplo anterior, `topic` é usado. 

Para excluir um ponto de extremidade privado, use o método [AZ Network Private-Endpoint Delete](/cli/azure/network/private-endpoint?#az-network-private-endpoint-delete) , conforme mostrado no exemplo a seguir:

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> As etapas mostradas nesta seção são para tópicos. Você pode usar etapas semelhantes para criar pontos de extremidade privados para **domínios**. 



### <a name="prerequisites"></a>Pré-requisitos
Atualize a extensão da grade de eventos do Azure para a CLI executando o seguinte comando: 

```azurecli-interactive
az extension update -n eventgrid
```

Se a extensão não estiver instalada, execute o seguinte comando para instalá-la: 

```azurecli-interactive
az extension add -n eventgrid
```

### <a name="create-a-private-endpoint"></a>Criar um ponto de extremidade privado
Para criar um ponto de extremidade privado, use o método [AZ Network Private-Endpoint Create](/cli/azure/network/private-endpoint?#az-network-private-endpoint-create) , conforme mostrado no exemplo a seguir:

```azurecli-interactive
az network private-endpoint create \
    --resource-group <RESOURECE GROUP NAME> \
    --name <PRIVATE ENDPOINT NAME> \
    --vnet-name <VIRTUAL NETWORK NAME> \
    --subnet <SUBNET NAME> \
    --private-connection-resource-id "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME> \
    --connection-name <PRIVATE LINK SERVICE CONNECTION NAME> \
    --location <LOCATION> \
    --group-ids topic
```

Para obter descrições dos parâmetros usados no exemplo, consulte a documentação para [AZ Network Private-Endpoint Create](/cli/azure/network/private-endpoint?#az-network-private-endpoint-create). Alguns pontos a serem observados neste exemplo são: 

- Para `private-connection-resource-id` , especifique a ID de recurso do **tópico** ou do **domínio**. O exemplo anterior usa o tópico Type:.
- para `group-ids` , especifique `topic` ou `domain` . No exemplo anterior, `topic` é usado. 

Para excluir um ponto de extremidade privado, use o método [AZ Network Private-Endpoint Delete](/cli/azure/network/private-endpoint?#az-network-private-endpoint-delete) , conforme mostrado no exemplo a seguir:

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> As etapas mostradas nesta seção são para tópicos. Você pode usar etapas semelhantes para criar pontos de extremidade privados para **domínios**. 

#### <a name="sample-script"></a>Exemplo de script
Aqui está um script de exemplo que cria os seguintes recursos do Azure:

- Grupo de recursos
- Rede virtual
- Sub-rede na rede virtual
- Tópico da grade de eventos do Azure
- Ponto de extremidade privado para o tópico

> [!NOTE]
> As etapas mostradas nesta seção são para tópicos. Você pode usar etapas semelhantes para criar pontos de extremidade privados para domínios.

```azurecli-interactive
subscriptionID="<AZURE SUBSCRIPTION ID>"
resourceGroupName="<RESOURCE GROUP NAME>"
location="<LOCATION>"
vNetName="<VIRTUAL NETWORK NAME>"
subNetName="<SUBNET NAME>"
topicName = "<TOPIC NAME>"
connectionName="<ENDPOINT CONNECTION NAME>"
endpointName=<ENDPOINT NAME>

# resource ID of the topic. replace <SUBSCRIPTION ID>, <RESOURCE GROUP NAME>, and <TOPIC NAME>
topicResourceID="/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME>"

# select subscription
az account set --subscription $subscriptionID

# create resource group
az group create --name $resourceGroupName --location $location

# create vnet 
az network vnet create \
    --resource-group $resourceGroupName \
    --name $vNetName \
    --address-prefix 10.0.0.0/16

# create subnet
az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --address-prefixes 10.0.0.0/24

# disable private endpoint network policies for the subnet
az network vnet subnet update \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --disable-private-endpoint-network-policies true

# create event grid topic. update <LOCATION>
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location

# verify that the topic was created.
az eventgrid topic show \
    --resource-group $resourceGroupName \
    --name $topicName

# create private endpoint for the topic you created
az network private-endpoint create 
    --resource-group $resourceGroupName \
    --name $endpointName \
    --vnet-name $vNetName \
    --subnet $subNetName \
    --private-connection-resource-id $topicResourceID \
    --connection-name $connectionName \
    --location $location \
    --group-ids topic

# get topic 
az eventgrid topic show \
    --resource-group $resourceGroupName \
    --name $topicName

```

### <a name="approve-a-private-endpoint"></a>Aprovar um ponto de extremidade privado
O trecho de código da CLI de exemplo a seguir mostra como aprovar uma conexão de ponto de extremidade privada. 

```azurecli-interactive
az eventgrid topic private-endpoint-connection approve \
    --resource-group $resourceGroupName \
    --topic-name $topicName \
    --name  $endpointName \
    --description "connection approved"
```


### <a name="reject-a-private-endpoint"></a>Rejeitar um ponto de extremidade privado
O trecho de código da CLI de exemplo a seguir mostra como rejeitar uma conexão de ponto de extremidade privada. 

```azurecli-interactive
az eventgrid topic private-endpoint-connection reject \
    --resource-group $resourceGroupName \
    --topic-name $topicName \
    --name $endpointName \
    --description "Connection rejected"
```

### <a name="disable-public-network-access"></a>Desabilitar o acesso de redes públicas
Por padrão, o acesso à rede pública é habilitado para um tópico ou domínio da grade de eventos. Para permitir o acesso somente por meio de pontos de extremidade privados, desabilite o acesso à rede pública executando o seguinte comando:  

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access disabled
```


## <a name="use-powershell"></a>Usar o PowerShell
Esta seção mostra como criar um ponto de extremidade privado para um tópico ou domínio usando o PowerShell. 

### <a name="prerequisite"></a>Pré-requisito
Siga as instruções de [como: usar o portal para criar um aplicativo do Azure AD e uma entidade de serviço que pode acessar recursos](../active-directory/develop/howto-create-service-principal-portal.md) para criar um aplicativo de Azure Active Directory e anote os valores de **ID de diretório (locatário)**, **ID de aplicativo (cliente)** e **segredo de aplicativo (cliente)**. 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Preparar token e cabeçalhos para chamadas à API REST 
Execute os seguintes comandos de pré-requisito para obter um token de autenticação para usar com as chamadas da API REST e a autorização e outras informações de cabeçalho. 

```azurepowershell-interactive
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get authentication token
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token  `
    -Body $body  `
    -ContentType 'application/x-www-form-urlencoded' 

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
```

### <a name="create-a-subnet-with-endpoint-network-policies-disabled"></a>Criar uma sub-rede com as políticas de rede do ponto de extremidade desabilitadas

```azurepowershell-interactive

# create resource group
New-AzResourceGroup -ResourceGroupName <RESOURCE GROUP NAME>  -Location <LOCATION>

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName <RESOURCE GROUP NAME> `
                    -Location <LOCATION> `
                    -Name <VIRTUAL NETWORK NAME> `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name example-privatelinksubnet `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork
```

### <a name="create-an-event-grid-topic-with-a-private-endpoint"></a>Criar um tópico de grade de eventos com um ponto de extremidade privado

> [!NOTE]
> As etapas mostradas nesta seção são para tópicos. Você pode usar etapas semelhantes para criar pontos de extremidade privados para **domínios**. 


```azurepowershell-interactive
$body = @{"location"="<LOCATION>"; "properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json

# create topic
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $body

# verify that the topic was created
$topic=Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01"   `
    -Headers $Headers  

# create private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name "<PRIVATE LINK SERVICE CONNECTION NAME>" `
                                -PrivateLinkServiceId $topic.Id `
                                -GroupId "topic"

# get virtual network info 
$virtualNetwork = Get-AzVirtualNetwork  `
                    -ResourceGroupName  <RESOURCE GROUP NAME> `
                    -Name <VIRTUAL NETWORK NAME>

# get subnet info
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                             | Where-Object  {$_.Name -eq <SUBNET NAME>}  

# now, you are ready to create a private endpoint 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName <RESOURCE GROUP NAME>  `
                                        -Name <PRIVATE ENDPOINT NAME>   `
                                        -Location <LOCATION> `
                                        -Subnet  $subnet   `
                                        -PrivateLinkServiceConnection $privateEndpointConnection

# verify that the endpoint was created
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections?api-version=2020-06-01"   `
    -Headers $Headers   `
    | ConvertTo-Json -Depth 5

```

Ao verificar se o ponto de extremidade foi criado, você verá o resultado semelhante ao seguinte:

```json

{
  "value": [
    {
      "properties": {
        "privateEndpoint": {
          "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Network/privateEndpoints/<PRIVATE ENDPOINT NAME>"
        },
        "groupIds": [
          "topic"
        ],
        "privateLinkServiceConnectionState": {
          "status": "Approved",
          "description": "Auto-approved",
          "actionsRequired": "None"
        },
        "provisioningState": "Succeeded"
      },
      "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>",
      "name": "myConnection",
      "type": "Microsoft.EventGrid/topics/privateEndpointConnections"
    }
  ]
}
```

### <a name="approve-a-private-endpoint-connection"></a>Aprovação de uma conexão de ponto de extremidade privado
O trecho do PowerShell de exemplo a seguir mostra como aprovar um ponto de extremidade privado. 

> [!NOTE]
> As etapas mostradas nesta seção são para tópicos. Você pode usar etapas semelhantes para aprovar pontos de extremidade privados para **domínios**. 

```azurepowershell-interactive
$approvedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="approved";"description"="connection approved";"actionsRequired"="none"}}} | ConvertTo-Json

# approve endpoint connection
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AzuRE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $approvedBody

# confirm that the endpoint connection was approved
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers

```

### <a name="reject-a-private-endpoint-connection"></a>Rejeição de uma conexão de ponto de extremidade privado
O exemplo a seguir mostra como rejeitar um ponto de extremidade privado usando o PowerShell. Você pode obter o GUID para o ponto de extremidade privado do resultado do comando GET anterior. 

> [!NOTE]
> As etapas mostradas nesta seção são para tópicos. Você pode usar etapas semelhantes para rejeitar pontos de extremidade privados para **domínios**. 


```azurepowershell-interactive
$rejectedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="rejected";"description"="connection rejected";"actionsRequired"="none"}}} | ConvertTo-Json

# reject private endpoint
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $rejectedBody

# confirm that endpoint was rejected
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01" ` 
    -Headers $Headers
```

Você pode aprovar a conexão mesmo depois que ela for rejeitada via API. Se você usar portal do Azure, não poderá aprovar um ponto de extremidade que foi rejeitado. 

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre como definir as configurações de firewall IP, consulte [Configurar o firewall de IP para os tópicos ou domínios da grade de eventos do Azure](configure-firewall.md).
* Para solucionar problemas de conectividade de rede, consulte [solucionar](troubleshoot-network-connectivity.md) problemas de conectividade de rede

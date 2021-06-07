---
title: Configurar o firewall IP para tópicos ou domínios da grade de eventos do Azure
description: Este artigo descreve como definir as configurações de firewall para os tópicos ou os domínios da grade de eventos.
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 178b9d84ea8b2e0f764f7584526db8dbcf5284f3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031830"
---
# <a name="configure-ip-firewall-for-azure-event-grid-topics-or-domains"></a>Configurar o firewall IP para tópicos ou domínios da grade de eventos do Azure 
Por padrão, o tópico e o domínio podem ser acessados pela Internet, desde que a solicitação venha com autenticação e autorização válidas. Com o firewall de IP, você pode restringir ainda mais a um conjunto de endereços IPv4 ou intervalos de endereços IPv4 na notação [CIDR (roteamento entre domínios sem classificação)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). Os Publicadores provenientes de qualquer outro endereço IP serão rejeitados e receberão uma resposta 403 (proibido). Para obter mais informações sobre os recursos de segurança de rede com suporte na grade de eventos, consulte [segurança de rede para a grade de eventos](network-security.md).

Este artigo descreve como definir as configurações de firewall IP para os tópicos ou domínios da grade de eventos do Azure.

## <a name="use-azure-portal"></a>Usar o portal do Azure
Esta seção mostra como usar o portal do Azure para criar regras de firewall de IP de entrada. As etapas mostradas nesta seção são para tópicos. Você pode usar etapas semelhantes para criar regras de IP de entrada para **domínios**. 

1. No [portal do Azure](https://portal.azure.com), navegue até o seu tópico ou domínio da grade de eventos e alterne para a guia **rede** .
2. Selecione **redes públicas** para permitir que toda a rede, incluindo a Internet, acesse o recurso. 

    Você pode restringir o tráfego usando regras de firewall baseadas em IP. Especifique um único endereço IPv4 ou um intervalo de endereços IP na notação CIDR (roteamento entre domínios sem classificação). 

    ![Captura de tela que mostra a página "acesso à rede pública" com "redes públicas" selecionada.](./media/configure-firewall/public-networks-page.png)
3. Selecione **pontos de extremidade privados somente** para permitir que somente as conexões de ponto final privado acessem esse recurso. Use a guia **conexões de ponto de extremidade privado** nesta página para gerenciar conexões. 

    ![Página redes públicas](./media/configure-firewall/private-endpoints-page.png)
4. Selecione **Salvar** na barra de ferramentas. 



## <a name="use-azure-cli"></a>Usar a CLI do Azure
Esta seção mostra como usar CLI do Azure comandos para criar tópicos com regras de IP de entrada. As etapas mostradas nesta seção são para tópicos. Você pode usar etapas semelhantes para criar regras de IP de entrada para **domínios**. 


### <a name="enable-or-disable-public-network-access"></a>Habilitar ou desabilitar o acesso à rede pública
Por padrão, o acesso à rede pública está habilitado para tópicos e domínios. Você também pode habilitá-la explicitamente ou desabilitá-la. Você pode restringir o tráfego Configurando regras de firewall de IP de entrada. 

#### <a name="enable-public-network-access-while-creating-a-topic"></a>Habilitar o acesso à rede pública ao criar um tópico

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access enabled
```


#### <a name="disable-public-network-access-while-creating-a-topic"></a>Desabilitar o acesso à rede pública ao criar um tópico

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access disabled
```

> [!NOTE]
> Quando o acesso à rede pública está desabilitado para um tópico ou domínio, o tráfego pela Internet pública não é permitido. Somente as conexões de ponto de extremidade privado terão permissão para acessar esses recursos. 


#### <a name="enable-public-network-access-for-an-existing-topic"></a>Habilitar o acesso à rede pública para um tópico existente

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled 
```

#### <a name="disable-public-network-access-for-an-existing-topic"></a>Desabilitar o acesso à rede pública para um tópico existente 

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access disabled
```

### <a name="create-a-topic-with-single-inbound-ip-rule"></a>Criar um tópico com uma regra de IP de entrada única
O comando da CLI de exemplo a seguir cria um tópico de grade de eventos com regras de IP de entrada. 

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR or CIDR MASK> allow 
```

### <a name="create-a-topic-with-multiple-inbound-ip-rules"></a>Criar um tópico com várias regras de IP de entrada

O seguinte exemplo de comando da CLI cria um tópico de grade de eventos duas regras de IP de entrada em uma etapa: 

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR 1 or CIDR MASK 1> allow \
    --inbound-ip-rules <IP ADDR 2 or CIDR MASK 2> allow
```

### <a name="update-an-existing-topic-to-add-inbound-ip-rules"></a>Atualizar um tópico existente para adicionar regras de IP de entrada
Este exemplo cria um tópico de grade de eventos primeiro e, em seguida, adiciona regras de IP de entrada para o tópico em um comando separado. Ele também atualiza as regras de IP de entrada que foram definidas no segundo comando. 

```azurecli-interactive

# create the event grid topic first
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location

# add inbound IP rules to an existing topic
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR or CIDR MASK> allow

# later, update topic with additional ip rules
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR 1 or CIDR MASK 1> allow \
    --inbound-ip-rules <IP ADDR 2 or CIDR MASK 2> allow
```

### <a name="remove-an-inbound-ip-rule"></a>Remover uma regra de IP de entrada
O comando a seguir remove a segunda regra criada na etapa anterior especificando somente a primeira regra durante a atualização da configuração. 

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR 1 or CIDR MASK 1> allow
```


## <a name="use-powershell"></a>Usar o PowerShell
Esta seção mostra como usar Azure PowerShell comandos para criar tópicos de grade de eventos do Azure com regras de firewall de IP de entrada. As etapas mostradas nesta seção são para tópicos. Você pode usar etapas semelhantes para criar regras de IP de entrada para **domínios**. 

### <a name="prerequisites"></a>Pré-requisitos
Siga as instruções de [como: usar o portal para criar um aplicativo do Azure AD e uma entidade de serviço que pode acessar recursos](../active-directory/develop/howto-create-service-principal-portal.md) para criar um aplicativo Azure Active Directory e anote os seguintes valores:

- ID do diretório (locatário)
- ID do aplicativo (cliente)
- Segredo de aplicativo (cliente)

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Preparar token e cabeçalhos para chamadas à API REST 
Execute os seguintes comandos de pré-requisito para obter um token de autenticação para usar com chamadas à API REST e autorização e outras informações de cabeçalho. 

```azurepowershell-interactive
# replace <CLIENT ID> and <CLIENT SECRET>
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get the authentication token. Replace <TENANT ID>
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token `
    -Body $body `
    -ContentType 'application/x-www-form-urlencoded'

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
$Headers.Add("Content-Type","application/json")
```

### <a name="enable-public-network-access-for-an-existing-topic"></a>Habilitar o acesso à rede pública para um tópico existente
Por padrão, o acesso à rede pública está habilitado para tópicos e domínios. Você pode restringir o tráfego Configurando regras de firewall de IP de entrada. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="enabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>Desabilitar o acesso à rede pública para um tópico existente
Quando o acesso à rede pública está desabilitado para um tópico ou domínio, o tráfego pela Internet pública não é permitido. Somente as conexões de ponto de extremidade privado terão permissão para acessar esses recursos. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="create-an-event-grid-topic-with-inbound-rules-in-one-step"></a>Criar um tópico de grade de eventos com regras de entrada em uma etapa

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that inbound IP rules are included. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDR or CIDR MASK>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# create the event grid topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5
```


### <a name="create-event-grid-topic-first-and-then-add-inbound-ip-rules"></a>Criar tópico de grade de eventos primeiro e, em seguida, adicionar regras de IP de entrada

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that no inbound IP rules are specified. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled";}} | ConvertTo-Json -Depth 5

# create the Event Grid topic
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body`

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

# prepare the body for REST PUT method. Notice that it includes inbound IP rules now. This feature is available in both basic and premium tiers.
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDR or CIDR MASK>";"action"="allow"}, @{"ipmask"="<IP ADDR or CIDR MASK>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# update the topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body

# verify that the topic was updated
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AzURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

```

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre o monitoramento de entregas de evento, consulte [Entrega de mensagens da Grade de Eventos do Monitor](monitor-event-delivery.md).
* Para saber mais sobre a chave de autenticação, confira [Event Grid security and authentication](security-authentication.md) (Segurança e autenticação da Grade de Eventos).
* Para obter mais informações sobre como criar uma assinatura da Grade de Eventos do Azure, confira [Event Grid subscription schema](subscription-creation-schema.md) (Esquema de assinatura da Grade de Eventos).
* Para solucionar problemas de conectividade de rede, consulte [solucionar](troubleshoot-network-connectivity.md) problemas de conectividade de rede

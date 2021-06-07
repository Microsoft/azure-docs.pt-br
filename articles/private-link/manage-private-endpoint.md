---
title: Gerenciar uma conexão de ponto de extremidade privado no Azure
description: Saiba como gerenciar conexões de ponto de extremidade privadas no Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 2987cd1ff8c678f7079e13e8b9bc657817c066f1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95531365"
---
# <a name="manage-a-private-endpoint-connection"></a>Gerenciar uma conexão de ponto de extremidade privado
O link privado do Azure funciona em um modelo de fluxo de chamadas de aprovação no qual o consumidor do serviço de vínculo privado pode solicitar uma conexão com o provedor de serviços para consumir o serviço. O provedor de serviços pode decidir se deseja permitir que o consumidor se conecte ou não. O link privado do Azure permite que os provedores de serviços gerenciem a conexão de ponto de extremidade particular em seus recursos. Este artigo fornece instruções sobre como gerenciar as conexões de ponto de extremidade privadas.

![Gerenciar pontos de extremidade privados](media/manage-private-endpoint/manage-private-endpoint.png)

Há dois métodos de aprovação de conexão que um consumidor de serviço de vínculo privado pode escolher:
- **Automático**: se o consumidor de serviço tiver permissões RBAC do Azure no recurso provedor de serviço, o consumidor poderá escolher o método de aprovação automática. Nesse caso, quando a solicitação atinge o recurso do provedor de serviços, nenhuma ação é necessária do provedor de serviços e a conexão é aprovada automaticamente. 
- **Manual**: por contrário, se o consumidor de serviço não tiver permissões RBAC do Azure no recurso provedor de serviços, o consumidor poderá escolher o método de aprovação manual. Nesse caso, a solicitação de conexão é exibida nos recursos de serviço como **pendentes**. O provedor de serviços deve aprovar manualmente a solicitação antes que as conexões possam ser estabelecidas. Em casos manuais, o consumidor de serviço também pode especificar uma mensagem com a solicitação para fornecer mais contexto ao provedor de serviços. O provedor de serviços tem as seguintes opções para escolher para todas as conexões de ponto de extremidade particulares: **aprovada**, **rejeitar**, **remover**.

A tabela abaixo mostra as várias ações do provedor de serviços e os Estados de conexão resultantes para pontos de extremidade privados.  O provedor de serviços também pode alterar o estado de conexão da conexão de ponto de extremidade particular em um momento posterior, sem intervenção do consumidor. A ação atualizará o estado do ponto de extremidade no lado do consumidor. 


|Ação do provedor de serviço   |Estado do ponto de extremidade particular do consumidor de serviço   |Descrição   |
|---------|---------|---------|
|Nenhum    |    Pendente     |    A conexão é criada manualmente e está pendente para aprovação pelo proprietário do recurso de link privado.       |
|Aprovar    |  Aprovado       |  A conexão foi aprovada automaticamente ou manualmente e está pronta para ser usada.     |
|Rejeitar     | Rejeitado        | A conexão foi rejeitada pelo proprietário do recurso do link privado.        |
|Remover    |  Desconectado       | A conexão foi removida pelo proprietário do recurso de link privado, o ponto de extremidade privado torna-se informativo e deve ser excluído para limpeza.        |
|   |         |         |
   
## <a name="manage-private-endpoint-connections-on-azure-paas-resources"></a>Gerenciar conexões de ponto de extremidade privado nos recursos de PaaS do Azure
O portal é o método preferencial para o gerenciamento de conexões de ponto de extremidade privado nos recursos de PaaS do Azure. No momento, não temos suporte do PowerShell/CLI para gerenciar conexões nos recursos de PaaS do Azure.
1. Entre no Portal do Azure em https://portal.azure.com.
2. Navegue até o centro de links privado.
3. Em **recursos**, selecione o tipo de recurso para o qual você deseja gerenciar as conexões de ponto de extremidade privado.
4. Para cada tipo de recurso, você pode exibir o número de conexões de ponto de extremidade privadas associadas a ela. Você pode filtrar os recursos conforme necessário.
5. Selecione as conexões de ponto de extremidade privado.  Nas conexões listadas, selecione a conexão que você deseja gerenciar. 
6. Você pode alterar o estado da conexão selecionando as opções na parte superior.

## <a name="manage-private-endpoint-connections-on-a-customerpartner-owned-private-link-service"></a>Gerenciar conexões de ponto de extremidade privado em um serviço de link privado de cliente/parceiro pertencente

Azure PowerShell e CLI do Azure são os métodos preferenciais para gerenciar conexões de ponto de extremidade privadas nos serviços de parceiro da Microsoft ou serviços de Propriedade do cliente. No momento, não temos nenhum suporte ao portal para gerenciar conexões em um serviço de vínculo privado.  
 
### <a name="powershell"></a>PowerShell 
  
Use os comandos do PowerShell a seguir para gerenciar conexões de ponto de extremidade privadas.  
#### <a name="get-private-link-connection-states"></a>Obter Estados de conexão de link privado 
Use o `Get-AzPrivateLinkService` cmdlet para obter as conexões de ponto de extremidade particulares e seus Estados.  
```azurepowershell
Get-AzPrivateLinkService -Name myPrivateLinkService -ResourceGroupName myResourceGroup 
 ```
 
#### <a name="approve-a-private-endpoint-connection"></a>Aprovar uma conexão de ponto de extremidade particular 
 
Use o `Approve-AzPrivateEndpointConnection` cmdlet para aprovar uma conexão de ponto de extremidade privada. 
 
```azurepowershell
Approve-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
#### <a name="deny-private-endpoint-connection"></a>Negar conexão de ponto de extremidade privado 
 
Use o `Deny-AzPrivateEndpointConnection` cmdlet para rejeitar uma conexão de ponto de extremidade privada. 
```azurepowershell
Deny-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService 
```
#### <a name="remove-private-endpoint-connection"></a>Remover conexão de ponto de extremidade particular 
 
Use o `Remove-AzPrivateEndpointConnection` cmdlet para remover uma conexão de ponto de extremidade privada. 
```azurepowershell
Remove-AzPrivateEndpointConnection -Name myPrivateEndpointConnection1 -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkServiceName 
```
 
### <a name="azure-cli"></a>CLI do Azure 
 
Use `az network private-link-service update` para gerenciar suas conexões de ponto de extremidade privadas. O estado da conexão é especificado no ```azurecli connection-status``` parâmetro. 
```azurecli
az network private-link-service connection update -g myResourceGroup -n myPrivateEndpointConnection1 --service-name myPLS --connection-status Approved 
```

   

## <a name="next-steps"></a>Próximas etapas
- [Saiba mais sobre ponto de extremidade privado](private-endpoint-overview.md)
 

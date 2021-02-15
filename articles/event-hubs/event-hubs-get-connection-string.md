---
title: Obter cadeia de conexão - Hubs de Eventos do Azure | Microsoft Docs
description: Este artigo fornece instruções para obter uma cadeia de conexão que os clientes podem usar para conectar Hubs de Eventos do Azure.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 5ae6c66ddbbf4b9946e7037e1a7723043bf60507
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86537166"
---
# <a name="get-an-event-hubs-connection-string"></a>Obter uma cadeia de conexão dos Hubs de Eventos

Para usar os Hubs de Eventos, você precisa criar um namespace dos Hubs de Eventos. Um namespace é um contêiner de escopo para vários tópicos de hubs de eventos ou do Kafka. Esse namespace fornece a você um [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) exclusivo. Após a criação de um namespace, você pode obter a cadeia de conexão necessária para se comunicar com os Hubs de Eventos.

A cadeia de conexão para os Hubs de Eventos do Azure tem os seguintes componentes inseridos nela

* FQDN = o FQDN do namespace de Hubs de Eventos que você criou (inclui o nome de namespace EventHubs seguido por servicebus.windows.net)
* SharedAccessKeyName = o nome que você escolheu para as chaves SAS do seu aplicativo
* SharedAccessKey = o valor gerado da chave.

O modelo de cadeia de conexão tem esta aparência
```
Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>
```

Um exemplo de cadeia de conexão pode parecer com `Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=`

Este artigo orienta você pelas diversas maneiras de obter a cadeia de conexão.

## <a name="get-connection-string-from-the-portal"></a>Obter a cadeia de conexão do portal
1. Entre no [Portal do Azure](https://portal.azure.com). 
2. Selecione **Todos os serviços** no menu esquerdo de navegação. 
3. Selecione **Hubs de Eventos** na seção **Análise**. 
4. Na lista de hubs de eventos, selecione seu hub de eventos.
6. Na página **Namespace de Hubs de Eventos**, selecione **Políticas de Acesso Compartilhado** no menu à esquerda.

    ![Item de menu Políticas de Acesso Compartilhado](./media/event-hubs-get-connection-string/event-hubs-get-connection-string1.png)
7. Selecione uma **política de acesso compartilhado** na lista de políticas. O padrão é chamado: **RootManageSharedAccessPolicy**. Você pode adicionar uma política com permissões apropriadas (leitura, gravação) e usar essa política. 

    ![Políticas de acesso compartilhado de hubs de eventos](./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png)
8. Selecione o botão **Copiar** ao lado do campo **Chave primária da cadeia de conexão**. 

    ![Hubs de Eventos - obter a cadeia de conexão](./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png)

## <a name="getting-the-connection-string-with-azure-powershell"></a>Obter a cadeia de conexão com o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Você pode usar o [Get-AzEventHubKey](/powershell/module/az.eventhub/get-azeventhubkey) para obter a cadeia de conexão para o nome de política/regra específico, conforme mostrado abaixo:

```azurepowershell-interactive
Get-AzEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

## <a name="getting-the-connection-string-with-azure-cli"></a>Obter a cadeia de conexão com a CLI do Azure
Use o seguinte para obter a cadeia de conexão para o namespace:

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --name RootManageSharedAccessKey
```

Ou você pode usar o seguinte para obter a cadeia de conexão para uma entidade do EventHub:

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --eventhub-name dummyeventhub --name RootManageSharedAccessKey
```

Para obter mais informações sobre os comandos da CLI do Azure para Hubs de Eventos, consulte [CLI do Azure para Hubs de Eventos](/cli/azure/eventhubs).

## <a name="next-steps"></a>Próximas etapas

Você pode saber mais sobre Hubs de Eventos visitando os links abaixo:

* [Visão geral de Hubs de Eventos](./event-hubs-about.md)
* [Criar um Hub de Eventos](event-hubs-create.md)

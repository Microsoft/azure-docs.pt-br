---
title: Barramento de serviço do Azure – suspender entidades de mensagens
description: Este artigo explica como suspender e reativar temporariamente as entidades de mensagem do barramento de serviço do Azure (filas, tópicos e assinaturas).
ms.topic: article
ms.date: 09/29/2020
ms.openlocfilehash: ea1acab3d0a86b0064f8b3eef7bfd1496bd17041
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94543044"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Suspender e reativar as entidades de mensagens (desabilitar)

Filas, tópicos e assinaturas podem ser suspensos temporariamente. A suspensão coloca a entidade em um estado desabilitado em que todas as mensagens são mantidas no armazenamento. No entanto, as mensagens não podem ser removidas ou adicionadas e as respectivas operações de protocolo geram erros.

Talvez você queira suspender uma entidade por motivos administrativos urgentes. Por exemplo, um receptor com falha retira as mensagens da fila, o processamento de falha e, ainda, conclui incorretamente as mensagens e as remove. Nesse caso, talvez você queira desabilitar a fila de recebimentos até que você corrija e implante o código. 

Uma suspensão ou reativação pode ser executada pelo usuário ou pelo sistema. O sistema suspende apenas as entidades devido a graves motivos administrativos, como atingir o limite de gastos da assinatura. As entidades desabilitadas pelo sistema não podem ser reativadas pelo usuário, mas serão restauradas quando a causa a suspensão tiver sido resolvida.

## <a name="queue-status"></a>Status da fila 
Os Estados que podem ser definidos para uma **fila** são:

-   **Active**: a fila está ativa. Você pode enviar mensagens para e receber mensagens da fila. 
-   **Disabled**: a fila está suspensa. É equivalente a definir **SendDisabled** e **ReceiveDisabled**. 
-   **SendDisabled**: não é possível enviar mensagens para a fila, mas você pode receber mensagens dela. Você receberá uma exceção se tentar enviar mensagens para a fila. 
-   **ReceiveDisabled**: você pode enviar mensagens para a fila, mas não pode receber mensagens dela. Você receberá uma exceção se tentar receber mensagens para a fila.


### <a name="change-the-queue-status-in-the-azure-portal"></a>Altere o status da fila no portal do Azure: 

1. No portal do Azure, acesse o namespace do Barramento de Serviço. 
1. Selecione a fila para a qual você deseja alterar o status. Você vê as filas no painel inferior no meio. 
1. Na página **fila do barramento de serviço** , consulte o status atual da fila como um hiperlink. Se a **visão geral** não estiver selecionada no menu à esquerda, selecione-a para ver o status da fila. Selecione o status atual da fila para alterá-la. 

    :::image type="content" source="./media/entity-suspend/select-state.png" alt-text="Selecione o estado da fila":::
4. Selecione o novo status para a fila e selecione **OK**. 

    :::image type="content" source="./media/entity-suspend/entity-state-change.png" alt-text="Definir estado da fila":::
    
Você também pode desabilitar as operações de envio e recebimento usando as APIs [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) do barramento de serviço no SDK do .net ou usando um modelo de Azure Resource Manager por meio de CLI do Azure ou Azure PowerShell.

### <a name="change-the-queue-status-using-azure-powershell"></a>Alterar o status da fila usando Azure PowerShell
O comando do PowerShell para desabilitar uma fila é mostrado no exemplo a seguir. O comando de reativação é equivalente, definindo `Status` como **Active**.

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="topic-status"></a>Status do tópico
Você pode alterar o status do tópico na portal do Azure. Selecione o status atual do tópico para ver a página a seguir, que permite alterar o status. 

:::image type="content" source="./media/entity-suspend/topic-state-change.png" alt-text="Alterar status do tópico":::

Os Estados que podem ser definidos para um **tópico** são:
- **Ativo**: o tópico está ativo. Você pode enviar mensagens para o tópico. 
- **Desabilitado**: o tópico é suspenso. Você não pode enviar mensagens para o tópico. 
- **SendDisabled**: mesmo efeito como **desabilitado**. Você não pode enviar mensagens para o tópico. Você receberá uma exceção se tentar enviar mensagens para o tópico. 

## <a name="subscription-status"></a>Status da assinatura
Você pode alterar o status da assinatura no portal do Azure. Selecione o status atual da assinatura para ver a página a seguir, que permite alterar o status. 

:::image type="content" source="./media/entity-suspend/subscription-state-change.png" alt-text="Alterar status da assinatura":::

Os Estados que podem ser definidos para uma **assinatura** são:
- **Ativo**: a assinatura está ativa. Você pode receber mensagens de frm da assinatura.
- **Desabilitado**: a assinatura está suspensa. Você não pode receber mensagens da assinatura. 
- **ReceiveDisabled**: mesmo efeito como **desabilitado**. Você não pode receber mensagens da assinatura. Você receberá uma exceção se tentar receber mensagens para a assinatura.

| Status do tópico | Status da assinatura | Comportamento | 
| ------------ | ------------------- | -------- | 
| Ativo | Ativo | Você pode enviar mensagens para o tópico e receber mensagens da assinatura. | 
| Ativo | Desabilitado ou recebimento desabilitado | Você pode enviar mensagens para o tópico, mas não pode receber mensagens da assinatura | 
| Desabilitado ou envio desabilitado | Ativo | Não é possível enviar mensagens para o tópico, mas você pode receber mensagens que já estão na assinatura. | 
| Desabilitado ou envio desabilitado | Desabilitado ou recebimento desabilitado | Você não pode enviar mensagens para o tópico e não pode receber da assinatura também. | 

## <a name="other-statuses"></a>Outros status
A enumeração [EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) também define um conjunto de estados de transição que podem ser definidos apenas pelo sistema. 


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as mensagens do Barramento de Serviço, consulte os seguintes tópicos:

* [Filas, tópicos e assinaturas do Barramento de Serviço](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
* [Como usar tópicos e assinaturas do Barramento de Serviço](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/entity-state-change.png


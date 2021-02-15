---
title: Revogar uma assinatura de compartilhamento no compartilhamento de dados do Azure
description: Saiba como revogar uma assinatura de compartilhamento de um destinatário usando o compartilhamento de dados do Azure.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 102a0099c95073793ba6a1a85f518c7539327bf3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87511846"
---
# <a name="how-to-revoke-a-consumers-share-subscription-in-azure-data-share"></a>Como revogar a assinatura de compartilhamento de um consumidor no compartilhamento de dados do Azure

Este artigo explica como revogar uma assinatura de compartilhamento de um ou mais dos seus consumidores usando o compartilhamento de dados do Azure. Isso impede que um consumidor acione mais instantâneos. Se o consumidor ainda não tiver disparado um instantâneo, ele nunca receberá os dados depois que a assinatura de compartilhamento for revogada. Se eles tiverem disparado um instantâneo anteriormente, os dados mais recentes que eles permanecerão em sua conta.

## <a name="navigate-to-a-sent-data-share"></a>Navegar até um compartilhamento de dados enviado

No compartilhamento de dados do Azure, navegue até o compartilhamento enviado e selecione a guia **compartilhar assinaturas** .

![Revogar assinatura de compartilhamento](./media/how-to/how-to-revoke-share-subscription/revoke-share-subscription.png) 

Marque as caixas ao lado dos destinatários cujas assinaturas de compartilhamento você gostaria de excluir e, em seguida, clique em **revogar**. O consumidor não receberá mais atualizações de seus dados.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como [monitorar seus compartilhamentos de dados](how-to-monitor.md).
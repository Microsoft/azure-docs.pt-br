---
title: Como monitorar a visualização de compartilhamento de dados do Azure
description: Como monitorar a visualização de compartilhamento de dados do Azure
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 869c1ed41d7f78df184461bc1d8cab6c6eb8d426
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789092"
---
# <a name="monitor-azure-data-share-preview"></a>Visualização de compartilhamento de dados do Azure Monitor 

Este artigo explica como você pode monitorar seus compartilhamentos de dados usando a visualização de compartilhamento de dados do Azure. Como um provedor de dados, você é capaz de monitorar vários aspectos do seu compartilhamento de relações de dados. Detalhes de como se seus consumidores de dados tiverem aceitado o convite para o compartilhamento de dados, bem como se tiver criado uma assinatura de compartilhamento e começado a usar seus dados estão disponíveis para monitorar. 

Como um consumidor de dados, você pode monitorar os instantâneos que foram acionados em sua assinatura do Azure. 

## <a name="monitor-invitation-status"></a>Monitorar o status do convite

Exibir o status de seus convites de compartilhamento de dados navegando para ações de enviadas -> de convites. 

![Status do convite](./media/invitation-status.png "status do convite") 

Há três estados seu convite pode estar em:

* Pendente - destinatário do compartilhamento de dados ainda não aceitou o convite.
* Aceito - destinatário do compartilhamento de dados já aceitou o convite.
* Rejeitado - destinatário do compartilhamento de dados rejeitou o convite.

> [!IMPORTANT]
> Se você excluir um convite depois que ele já foi aceito, não é equivalente a revogar o acesso. Se você quiser interromper snapshots futuras seja copiado para sua conta de armazenamento de consumidores de dados, você deve revogar o acesso por meio de *assinaturas de compartilhamento* guia. 

## <a name="monitor-share-subscriptions"></a>Assinaturas de compartilhamento do monitor

Exibir o status de suas assinaturas de compartilhamento, navegando até ações de enviadas -> assinaturas de compartilhamento. Isso dará a você detalhes sobre as assinaturas do Active Directory criadas por seus consumidores de dados depois de aceitar o convite. Você pode interromper as atualizações futuras para o consumidor de dados selecionando a assinatura de compartilhamento e selecionando *revogar*. 

## <a name="snapshot-history"></a>Histórico do instantâneo 

Na guia Histórico, são capazes de exibir os instantâneos que foram copiados no locatário do consumidor de seus dados. É possível monitorar a frequência e duração de cada intervalo de instantâneo. 

![Instantâneo de histórico](./media/sent-shares.png "histórico do instantâneo") 

Você pode exibir mais detalhes sobre cada instantâneo executado clicando na data de início da execução. 

## <a name="next-steps"></a>Próximas etapas 

Saiba mais sobre [terminologia de compartilhamento de dados do Azure](terminology.md)
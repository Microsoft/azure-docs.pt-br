---
title: Como monitorar o compartilhamento de dados do Azure
description: Saiba como monitorar o status do convite, assinaturas de compartilhamento e histórico de instantâneos no compartilhamento de dados do Azure
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: cc400922115f348090677661cd1b30434be19b72
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92910467"
---
# <a name="monitor-azure-data-share"></a>Monitorar o Azure Data Share  

Este artigo explica como você pode monitorar seus compartilhamentos de dados usando o compartilhamento de dados do Azure. Como um provedor de dados, você pode monitorar vários aspectos de suas relações de compartilhamento de dados. Detalhes, como se os consumidores de dados tiverem aceitado seu convite para o compartilhamento de dados, bem como se eles criaram uma assinatura de compartilhamento e começarem a usar seus dados estão disponíveis para monitoramento. 

Como um consumidor de dados, você pode monitorar os instantâneos que foram disparados em sua assinatura do Azure. 

## <a name="monitor-invitation-status"></a>Monitorar o status do convite

Exiba o status de seus convites de compartilhamento de dados navegando para compartilhamentos enviados-> convites. 

![Status do convite](./media/invitation-status.png "Status do convite") 

Há três Estados em que seu convite pode estar:

* O destinatário de compartilhamento de dados pendentes ainda não aceitou o convite.
* Aceito-o destinatário do compartilhamento de dados aceitou o convite.
* Rejeitado-o destinatário do compartilhamento de dados rejeitou o convite.

> [!IMPORTANT]
> Se você excluir um convite depois que ele já tiver sido aceito, não será equivalente a revogar o acesso. Se você deseja impedir que instantâneos futuros sejam copiados para sua conta de armazenamento de consumidores de dados, você deve revogar o acesso por meio da guia *assinaturas de compartilhamento* . 

## <a name="monitor-share-subscriptions"></a>Monitorar assinaturas de compartilhamento

Exiba o status de suas assinaturas de compartilhamento navegando para compartilhamentos enviados-> compartilhar assinaturas. Isso fornecerá detalhes sobre as assinaturas ativas criadas por seus consumidores de dados depois de aceitar seu convite. Você pode parar as atualizações futuras para o consumidor de dados selecionando a assinatura de compartilhamento e selecionando *revogar*. 

## <a name="snapshot-history"></a>Histórico de instantâneos 

Na guia **histórico** de um compartilhamento, você pode exibir quando os dados são copiados do provedor de dados para o armazenamento de dados do consumidor de dados. Você pode monitorar a frequência, a duração e o status de cada instantâneo. 

![A captura de tela mostra os compartilhamentos enviados no portal do Azure.](./media/sent-shares.png "Histórico de instantâneos") 

Você pode exibir mais detalhes sobre cada execução de instantâneo clicando na data de início da execução. Em seguida, clique no status de cada conjunto de dados para exibir a quantidade de data de transferência, o número de arquivos/registros copiados, a duração do instantâneo, o número de vCores usadas e a mensagem de erro, se houver algum. 

São exibidos até 30 dias de histórico de instantâneo. Se você precisar salvar e ver mais de 30 dias de histórico, poderá aproveitar a configuração de diagnóstico.

## <a name="diagnostic-setting"></a>Configuração de diagnóstico

Você pode definir a configuração de diagnóstico para salvar dados de log ou eventos. Navegue até monitoramento-> configurações de diagnóstico e selecione **Adicionar configuração de diagnóstico**. Selecione os dados de log ou eventos nos quais você está interessado e onde deseja armazená-los ou enviá-los. 

![Captura de tela mostra a página Configurações de diagnóstico no portal do Azure.](./media/diagnostic-settings.png "Configurações de Diagnóstico") 

## <a name="next-steps"></a>Próximas etapas 

Saiba mais sobre a [terminologia do compartilhamento de dados do Azure](terminology.md)
---
title: Terminologia da visualização do compartilhamento de dados do Azure
description: Terminologia da visualização do compartilhamento de dados do Azure
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 01025acfa2fd939db8134a1e61c7902034a1d2c3
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424224"
---
# <a name="azure-data-share-preview-concepts"></a>Conceitos da visualização do compartilhamento de dados do Azure 

A visualização do compartilhamento de dados do Azure apresenta uma nova terminologia relacionada ao compartilhamento de dados. Este artigo explica alguns termos usados com frequência que você pode ver em todo o serviço. 

## <a name="data-provider"></a>Provedor de dados

Um provedor de dados é a organização que está compartilhando dados com seus consumidores. Normalmente, o provedor de dados pode ser um proprietário ou um curador dos dados. Os provedores de dados desejam compartilhar dados de vários tipos. Alguns exemplos de dados que um provedor de dados talvez queira compartilhar incluem dados brutos, como ponto de vendas ou dados de série temporal. Um provedor de dados também pode querer compartilhar dados pré-configurados previamente processados que já contenham análises e ideias. 

## <a name="data-consumer"></a>Consumidor de dados 

Um consumidor de dados é a organização que está recebendo dados de um provedor de dados. O consumidor de dados pode querer unir os dados compartilhados com seus próprios dados para obter informações. Em alguns casos, o consumidor de dados pode estar recebendo dados que já foram processados. 

## <a name="data-share"></a>Compartilhamento de dados

Um compartilhamento de dados é um grupo de DataSets que são compartilhados como uma única entidade. Conjuntos de dados podem ser de várias fontes de dados do Azure que são compatíveis com o Azure data share. Atualmente, o compartilhamento de dados do Azure dá suporte ao armazenamento de BLOBs do Azure e Azure Data Lake Store. 

## <a name="share-subscription"></a>Compartilhar assinatura 

Uma assinatura de compartilhamento é criada quando um consumidor de dados aceita um convite de compartilhamento de dados de um provedor de dados. Os provedores de dados podem exibir as assinaturas de compartilhamento ativas navegando até os compartilhamentos **enviados** em sua conta de compartilhamento de dados do Azure e selecionando **assinaturas de compartilhamento**.

Um consumidor de dados pode verificar se eles têm uma assinatura de compartilhamento ativa navegando até os compartilhamentos **recebidos** e exibindo o status de seus compartilhamentos recebidos. 

## <a name="snapshot"></a>Instantâneo

Um instantâneo pode ser criado por um consumidor de dados quando eles aceitam um convite de compartilhamento de dados. Quando eles aceitam um convite, eles podem disparar um instantâneo completo dos dados compartilhados com eles. O instantâneo é uma cópia dos dados no momento em que o consumidor de dados gerou o instantâneo. 

Há dois tipos de instantâneos – completo e incremental. Um instantâneo completo contém todos os dados dentro do compartilhamento de dados. Um instantâneo incremental contém todos os dados que foram atualizados/adicionados desde que o último instantâneo foi disparado. 

## <a name="snapshot-settings-in-azure-data-share"></a>Configurações de instantâneo no compartilhamento de dados do Azure
 
Um provedor de dados pode habilitar uma configuração de instantâneo para um compartilhamento de dados. Essa configuração permite que os consumidores de dados recebam atualizações incrementais conforme elas ocorrem. Essa configuração deve ser habilitada se o provedor de dados quiser que seus consumidores de dados recebam atualizações para os dados que foram compartilhados. 

Se um provedor de dados habilitar essa configuração, um intervalo de recorrência poderá ser selecionado. O intervalo de recorrência pode ser por hora ou por dia. 

Um consumidor de dados tem a opção de aceitar esse agendamento de instantâneo para receber atualizações incrementais, que inclui todos os dados que foram alterados desde a primeira geração de um novo instantâneo. 

## <a name="invitation"></a>Convite

Um provedor de dados pode convidar vários destinatários para seu compartilhamento de dados. Eles podem fazer isso adicionando destinatários ao compartilhamento de dados. Os convites também podem ser adicionados após a criação de um compartilhamento de dados. 

Um provedor de dados pode excluir um convite após ele ser enviado. Observe que, se um provedor de dados excluir um convite depois que ele tiver sido aceito, o consumidor de dados ainda poderá ter uma assinatura de compartilhamento ativa. Se o provedor de dados excluir um convite e ele ainda não tiver sido aceito, o consumidor de dados não poderá aceitá-lo. 

## <a name="recipient"></a>Destinatário

Um destinatário é alguém que recebe um convite para um compartilhamento de dados. Normalmente, um provedor de dados adicionará destinatários ao compartilhamento de dados que eles criam. Depois que o destinatário de um convite aceitar o convite, ele se tornará um consumidor de dados.  

## <a name="next-steps"></a>Próximas etapas

Para saber como iniciar o compartilhamento de dados, continue com o tutorial sobre como [compartilhar seus dados](share-your-data.md).


---
title: Impedir exclusão acidental – Compartilhamentos de arquivo do Azure
description: Saiba mais sobre a exclusão temporária nos compartilhamentos de arquivo do Azure e como você pode usá-la para a recuperação de dados e para impedir a exclusão acidental.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 0fecc9fc954a1ac648e8f60badf69ad1d2e8f1cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93126933"
---
# <a name="prevent-accidental-deletion-of-azure-file-shares"></a>Impedir exclusão acidental de compartilhamentos de arquivo do Azure

O Armazenamento do Azure agora oferece exclusão temporária para compartilhamentos de arquivo. A exclusão reversível permite recuperar o compartilhamento de arquivos quando ele é excluído erroneamente por um aplicativo ou outro usuário da conta de armazenamento.

## <a name="how-soft-delete-works"></a>Como funciona a exclusão temporária

Quando a exclusão reversível para compartilhamentos de arquivos do Azure estiver habilitada, se um compartilhamento de arquivos for excluído, ele faz a transição para um estado de exclusão reversível em vez de ser apagado permanentemente. Você pode configurar a quantidade de tempo que os dados com exclusão reversível são recuperáveis antes de serem excluídos permanentemente e restaurar o compartilhamento a qualquer momento durante esse período de retenção. Após a exclusão, o compartilhamento e todo o conteúdo, incluindo instantâneos, serão restaurados para o estado em que estava antes da exclusão. A exclusão reversível funciona apenas em um nível de compartilhamento de arquivos-arquivos individuais que são excluídos ainda serão apagados permanentemente.

A exclusão reversível pode ser habilitada em compartilhamentos de arquivos novos ou existentes. A exclusão temporária também é compatível com versões anteriores. Você não precisa alterar nada nos aplicativos para aproveitar as proteções da exclusão temporária. 

Para excluir permanentemente um compartilhamento de arquivos em um estado de exclusão reversível antes da hora de expiração, você deve restaurar o compartilhamento, desabilitar a exclusão reversível e, em seguida, excluir o compartilhamento novamente. Em seguida, você deve reabilitar a exclusão reversível, pois quaisquer outros compartilhamentos de arquivos nessa conta de armazenamento ficarão vulneráveis à exclusão acidental enquanto a exclusão reversível estiver desativada.

Para compartilhamentos de arquivo premium excluídos de maneira temporária, a cota de compartilhamento de arquivo (o tamanho provisionado de um compartilhamento de arquivos) é usada no cálculo da cota de conta de armazenamento total até a data de expiração do compartilhamento com exclusão temporária, quando o compartilhamento é totalmente excluído.

## <a name="configuration-settings"></a>Definições de configuração

### <a name="enabling-or-disabling-soft-delete"></a>Habilitando ou desabilitando a exclusão reversível

A exclusão reversível para compartilhamentos de arquivos está habilitada no nível da conta de armazenamento, por isso, as configurações de exclusão reversível se aplicam a todos os compartilhamentos de arquivos em uma conta de armazenamento. Você pode habilitar ou desabilitar a exclusão reversível a qualquer momento. Quando você cria uma nova conta de armazenamento, a exclusão reversível para compartilhamentos de arquivos é desabilitada por padrão, você pode habilitá-la durante a implantação ou a qualquer momento depois. A exclusão reversível permanecerá desabilitada por padrão para contas de armazenamento existentes. Se você tiver configurado o [backup de compartilhamento de arquivos do Azure](../../backup/azure-file-share-backup-overview.md) para um compartilhamento de arquivos do Azure, a exclusão reversível para compartilhamentos de arquivos do Azure será habilitada automaticamente na conta de armazenamento desse compartilhamento.

Se você habilitar a exclusão temporária para compartilhamentos de arquivo, excluir alguns compartilhamentos de arquivo e desabilitar a exclusão temporária, você ainda poderá acessar e recuperar esses compartilhamentos se eles tiverem sido salvos nesse período. Quando habilitar a exclusão temporária, você precisará também configurar o período de retenção.

### <a name="retention-period"></a>Período de retenção

O período de retenção é a quantidade de tempo que os compartilhamentos de arquivos com exclusão reversível são armazenados e estão disponíveis para recuperação. Para compartilhamentos de arquivo que são excluídos explicitamente, a contagem do período de retenção se inicia quando os dados são excluídos. No momento, você pode especificar um período de retenção entre 1 e 365 dias. Você pode alterar o período de retenção de exclusão reversível a qualquer momento. Um período de retenção atualizado será aplicado somente a compartilhamentos excluídos depois que o período de retenção tiver sido atualizado. Os compartilhamentos excluídos antes da atualização do período de retenção expirarão com base no período de retenção que foi configurado quando esses dados foram excluídos.

## <a name="pricing-and-billing"></a>Preços e cobrança

Os compartilhamentos de arquivos Standard e Premium são cobrados na capacidade usada quando são excluídos de maneira temporária, em vez da capacidade provisionada. Além disso, os compartilhamentos de arquivos premium são cobrados na taxa de instantâneos durante o estado de exclusão temporária. Os compartilhamentos de arquivos Standard são cobrados na taxa regular durante o estado de exclusão temporária. Você não será cobrado pelos dados que são excluídos permanentemente após o período de retenção configurado.

Para obter mais informações sobre os preços do armazenamento de arquivos do Azure em geral, confira a [Página de Preços do Armazenamento de Arquivos do Azure](https://azure.microsoft.com/pricing/details/storage/files/).

Quando você habilita inicialmente a exclusão temporária, recomendamos usar um período de retenção pequeno para entender melhor como o recurso afetará sua fatura.

## <a name="next-steps"></a>Próximas etapas

Para saber como habilitar e usar a exclusão reversível, continue a [habilitar a exclusão reversível](storage-files-enable-soft-delete.md).

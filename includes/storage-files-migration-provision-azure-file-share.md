---
title: Considerações para provisionamento de compartilhamentos de arquivos do Azure
description: Provisione compartilhamentos de arquivos do Azure para uso com Sincronização de Arquivos do Azure. Um bloco de texto comum, compartilhado entre documentos de migração.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: d48baba5ee60a2bf5a4cb5e4d1ce840fce8eec43
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "82143618"
---
Um compartilhamento de arquivos do Azure é armazenado na nuvem em uma conta de armazenamento do Azure.
Há outro nível de considerações de desempenho aqui.

Se você tiver compartilhamentos altamente ativos (compartilhamentos usados por muitos usuários e/ou aplicativos), dois compartilhamentos de arquivos do Azure poderão alcançar o limite de desempenho de uma conta de armazenamento.

Uma prática recomendada é implantar contas de armazenamento com um compartilhamento de arquivos cada.
Você pode agrupar vários compartilhamentos de arquivos do Azure na mesma conta de armazenamento, caso você tenha compartilhamentos de arquivamento ou você espera uma atividade de baixa diária neles.

Essas considerações se aplicam mais ao acesso direto à nuvem (por meio de uma VM do Azure) do que para Sincronização de Arquivos do Azure. Se você planeja usar Sincronização de Arquivos do Azure apenas nesses compartilhamentos, o agrupamento de vários em uma única conta de armazenamento do Azure é bom.

Se você tiver feito uma lista de seus compartilhamentos, deverá mapear cada compartilhamento para a conta de armazenamento na qual ele residirá.

Na fase anterior, você determinou o número apropriado de compartilhamentos. Nesta etapa, você criou um mapeamento de contas de armazenamento para compartilhamentos de arquivos. Agora, implante o número apropriado de contas de armazenamento do Azure com o número apropriado de compartilhamentos de arquivos do Azure neles.

Verifique se a região de cada uma de suas contas de armazenamento é a mesma e corresponde à região do recurso de serviço de sincronização de armazenamento que você já implantou.

> [!CAUTION]
> Se você criar um compartilhamento de arquivos do Azure que tenha um limite de 100 a TiB, esse compartilhamento só poderá usar opções de redundância de armazenamento com redundância local ou armazenamento com redundância de zona. Considere suas necessidades de redundância de armazenamento antes de usar os compartilhamentos de arquivos 100-TiB.

Os compartilhamentos de arquivos do Azure ainda são criados com um limite de 5 TiB por padrão. Como você está criando novas contas de armazenamento, certifique-se de seguir as [diretrizes para criar contas de armazenamento que permitem compartilhamentos de arquivos do Azure com limites 100-Tib](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Outra consideração quando você está implantando uma conta de armazenamento é a redundância do armazenamento do Azure. Consulte [Opções de redundância do armazenamento do Azure](../articles/storage/common/storage-redundancy.md).

Os nomes de seus recursos também são importantes. Por exemplo, se você agrupar vários compartilhamentos para o departamento de RH em uma conta de armazenamento do Azure, deverá nomear a conta de armazenamento adequadamente. Da mesma forma, ao nomear seus compartilhamentos de arquivos do Azure, você deve usar nomes semelhantes àqueles usados para suas contrapartes locais.
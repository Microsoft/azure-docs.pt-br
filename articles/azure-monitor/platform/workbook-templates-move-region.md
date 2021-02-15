---
title: Modelos de pasta de trabalho Azure Monitor – mover regiões
description: Como mover um modelo de pasta de trabalho para uma região diferente
services: azure-monitor
author: gardnerjr
manager: acearun
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/12/2020
ms.author: jgardner
ms.openlocfilehash: f37550d914cf7efb0c75ed3dfa8854e1ec7be7e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88875553"
---
# <a name="move-an-azure-workbook-template-to-another-region"></a>Mover um modelo de pasta de trabalho do Azure para outra região

Este artigo descreve como mover recursos de modelo de pasta de trabalho do Azure para uma região diferente do Azure. Você pode mover seus recursos para outra região por vários motivos. Por exemplo, para aproveitar uma nova região do Azure, para implantar recursos ou serviços disponíveis somente em regiões específicas, para atender aos requisitos internos de políticas e governança, ou em resposta aos requisitos de planejamento de capacidade.

Atualmente, não há nenhuma interface do usuário do portal para criar recursos de modelo de pasta de trabalho, a única maneira atual de criá-los é [por meio de implantações de modelo de Azure Resource Manager (modelo ARM)](./workbooks-automate.md). Dessa forma, a maneira mais fácil de mover um modelo é reutilizar o modelo ARM anterior e atualizá-lo para ser implantado na nova região.

## <a name="prerequisites"></a>Pré-requisitos

* Certifique-se de que os modelos de pasta de trabalho têm suporte na região de destino.

## <a name="prepare"></a>Preparar

* Identifique o modelo ARM usado anteriormente para o modelo de pasta de trabalho.

## <a name="move"></a>Mover

1. Atualize o modelo usado anteriormente para fazer referência à nova região.

   > [!NOTE]
   > Talvez seja necessário usar um novo nome para o modelo de pasta de trabalho para evitar nomes duplicados.

2. Implante o modelo atualizado por meio da implantação de modelo ARM para criar um novo modelo de pasta de trabalho na região desejada.

## <a name="verify"></a>Verificar

Use as pastas de trabalho de navegação do Azure para localizar o modelo de pasta de trabalho implantado recentemente. Verifique se o local é o local de destino.

## <a name="clean-up"></a>Limpeza

Depois que o modelo de pasta de trabalho tiver sido criado na nova região, exclua o modelo de pasta de trabalho original na região anterior.
1. Localize o modelo de pasta de trabalho na interface do usuário de navegação de pastas de trabalho do Azure.
2. Selecione o modelo de pasta de trabalho a ser excluído.
3. Selecione o comando "excluir".

Se você renomeou o modelo de pasta de trabalho para importá-lo para uma nova região, poderá renomear o modelo de pasta de trabalho para o nome anterior depois que o item original tiver sido excluído usando o comando "Renomear" no modo de exibição de recurso de pasta de trabalho do Azure.

## <a name="next-steps"></a>Próximas etapas

Precisa mover uma pasta de trabalho em vez de um modelo? Consulte como [mover uma pasta de trabalho do Azure para outra região](./workbooks-move-region.md).


---
title: Azure Monitor pastas de trabalho – mover regiões
description: Como mover uma pasta de trabalho para uma região diferente
services: azure-monitor
author: gardnerjr
manager: acearun
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/12/2020
ms.author: jgardner
ms.openlocfilehash: d3b8bfbef544e754f684421daa847f1724435d53
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88875554"
---
# <a name="move-an-azure-workbook-to-another-region"></a>Mover uma pasta de trabalho do Azure para outra região

Este artigo descreve como mover os recursos da pasta de trabalho do Azure para uma região diferente do Azure. Você pode mover seus recursos para outra região por vários motivos. Por exemplo, para aproveitar uma nova região do Azure, para implantar recursos ou serviços disponíveis somente em regiões específicas, para atender aos requisitos internos de políticas e governança, ou em resposta aos requisitos de planejamento de capacidade.

## <a name="prerequisites"></a>Pré-requisitos

* Certifique-se de que as pastas de trabalho têm suporte na região de destino.

* Essas instruções se aplicam a pastas de trabalho compartilhadas ( `microsoft.insights/workbooks` ) e pastas de trabalho privadas ( `microsoft.insights/myworkbooks` ) salvas em Azure monitor e na maioria dos tipos de recursos.

  No entanto, para pastas de trabalho especificamente vinculadas ao tipo de recurso Application Insights, essas pastas de trabalho são armazenadas na região do Azure onde o recurso de Application Insights é salvo.

  Essas pastas de trabalho não podem ser movidas individualmente para outra região.

## <a name="move"></a>Mover

A maneira mais simples de mover uma pasta de trabalho do Azure é usar "salvar como" na ferramenta de pastas de trabalho na interface do usuário do portal:

1. Abra a pasta de trabalho de destino no Visualizador de pasta de trabalho.
2. Use o botão de barra de ferramentas "Editar" para entrar no modo de edição.
3. Use o botão da barra de ferramentas "salvar como".
4. No formulário salvar, escolha um nome e a região desejada para a pasta de trabalho. Verifique se os outros campos de assinatura, grupo de recursos e compartilhamento são apropriados.

   > [!NOTE]
   > Talvez seja necessário usar um novo nome para a pasta de trabalho para evitar nomes duplicados.

5. Salve. 

## <a name="verify"></a>Verificar

Use a interface do usuário de navegação de pastas de trabalho do Azure para localizar a nova pasta de trabalho. Verifique se o local é o local de destino.

## <a name="clean-up"></a>Limpeza

Depois que a pasta de trabalho tiver sido criada na nova região, exclua a pasta de trabalho original na região anterior.
1. Abra a pasta de trabalho original no Visualizador de pasta de trabalho.
2. Use o botão de barra de ferramentas "Editar" para entrar no modo de edição.
3. No menu suspenso editar ferramentas (ícone de lápis), escolha "Excluir pasta de trabalho".

Se você renomeou a pasta de trabalho para importá-la para uma nova região, poderá renomear a pasta de trabalho para o nome anterior depois que a pasta de trabalho original tiver sido excluída usando o item "Renomear" da barra de ferramentas do modo de edição menu suspenso.

## <a name="next-steps"></a>Próximas etapas

Precisa mover um modelo de pasta de trabalho em vez de uma pasta de trabalho? Consulte como [mover um modelo de pasta de trabalho do Azure para outra região](./workbook-templates-move-region.md).

Consulte como [implantar pastas de trabalho e modelos de pasta de trabalho](./workbooks-automate.md) por meio de modelos ARM.

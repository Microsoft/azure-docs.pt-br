---
title: Configurar a alta disponibilidade-Citus (hiperescala)-banco de dados do Azure para PostgreSQL
description: Como habilitar ou desabilitar a alta disponibilidade
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 46b842994cbcf7efe66d5992c79246d77626e268
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90907392"
---
# <a name="configure-hyperscale-citus-high-availability"></a>Configurar alta disponibilidade de Citus (hiperescala)

Banco de dados do Azure para PostgreSQL-o Citus (hiperescala) fornece alta disponibilidade (HA) para evitar tempo de inatividade do banco de dados. Com a HA habilitada, cada nó em um grupo de servidores receberá um modo de espera. Se o nó original se tornar não íntegro, seu modo de espera será promovido para substituí-lo.

> [!IMPORTANT]
> Como a HA duplica o número de servidores no grupo, ele também dobrará o custo.

Habilitar a HA é possível durante a criação do grupo de servidores ou posteriormente na guia **computação + armazenamento** do grupo de servidores na portal do Azure. A interface do usuário é semelhante em ambos os casos. Arraste o controle deslizante para **alta disponibilidade** de não para Sim:

:::image type="content" source="./media/howto-hyperscale-high-availability/01-ha-slider.png" alt-text="controle deslizante de alta disponibilidade":::

Clique no botão **salvar** para aplicar sua seleção. Habilitar a HA pode levar algum tempo, pois o grupo de servidores provisiona em espera e transmite dados para eles.

A guia **visão geral** do grupo de servidores listará todos os nós e suas esperas, juntamente com uma coluna de **alta disponibilidade** que indica se a ha está habilitada com êxito para cada nó.

:::image type="content" source="./media/howto-hyperscale-high-availability/02-ha-column.png" alt-text="controle deslizante de alta disponibilidade":::

### <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a [alta disponibilidade](concepts-hyperscale-high-availability.md).

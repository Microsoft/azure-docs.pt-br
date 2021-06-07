---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7151c110fd50f7485aa0b130832aace4f3143ad9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101750033"
---
- Atualmente, esse recurso tem suporte apenas para SSDs Premium.
- Você deve desalocar sua VM ou desanexar o disco de uma VM em execução antes de poder alterar a camada do disco.
- Os níveis de desempenho P60, P70 e P80 só podem ser usados por discos maiores que 4.096 GiB.
- O nível de desempenho de um disco pode ser rebaixado apenas uma vez a cada 12 horas.

## <a name="change-performance-tier-without-downtime-preview"></a>Alterar o nível de desempenho sem tempo de inatividade (versão prévia)

Normalmente, você teria que desalocar sua VM ou desanexar o disco para alterar o nível de desempenho. Mas se você habilitar esse recurso de visualização, não precisará desalocar sua VM ou desanexar o disco para alterar a camada. Você pode se inscrever para ver a versão prévia [aqui](https://aka.ms/liveperftiersignup).

A visualização tem as seguintes limitações:
- Disponível somente na região EastUS2EUAP.
- Não disponível atualmente para discos compartilhados
- Deve usar modelos de Azure Resource Manager com a `2020-12-01` API para alterar as camadas de desempenho sem tempo de inatividade.
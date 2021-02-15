---
title: Conectar ao GitHub
description: Use o GitHub para especificar suas referências de entidade de modelo de dados comuns
author: djpmsft
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: daperlov
ms.openlocfilehash: 5d555d7bc4d3aae9c016cacbe17b68c30859d99a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100372273"
---
# <a name="use-github-to-read-common-data-model-entity-references"></a>Usar o GitHub para ler referências de entidade de modelo de dados comuns

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

O conector do GitHub no Azure Data Factory é usado somente para receber o esquema de referência de entidade para o formato de [modelo de dados comum](format-common-data-model.md) no fluxo de dados de mapeamento.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do GitHub.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade Type deve ser definida como **GitHub**. | sim
| userName | Nome de usuário do GitHub | sim |
| password | Senha do GitHub | sim |

## <a name="next-steps"></a>Próximas etapas

Crie um [conjunto](data-flow-source.md) de dados de origem em mapear o fluxo.
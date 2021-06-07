---
title: Atividade Anexar Variável no Azure Data Factory
description: Saiba como definir a atividade Anexar Variável para adicionar um valor a uma variável de matriz existente definida em um pipeline do Data Factory
ms.service: data-factory
ms.topic: conceptual
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.date: 10/09/2018
ms.openlocfilehash: 1ca58fc208bb02d137b977e0b18857e8c87a5440
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783839"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Atividade Anexar Variável no Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Use a atividade Anexar Variável para adicionar um valor a uma variável de matriz existente definida em um pipeline do Data Factory.

## <a name="type-properties"></a>Propriedades de tipo

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
name | Nome da atividade no pipeline | Sim
descrição | Texto descrevendo o que a atividade realiza | não
type | O tipo de atividade é AppendVariable | sim
value | Literal de cadeia de caracteres ou valor do objeto de expressão usado para acrescentar à variável especificada | sim
variableName | Nome da variável que será modificada por atividade, a variável deve ser do tipo "Matriz" | sim

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre uma atividade de fluxo de controle relacionada com suporte pelo Data Factory: 

- [Definir Atividade Variável](control-flow-set-variable-activity.md)

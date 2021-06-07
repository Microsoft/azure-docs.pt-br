---
title: Regras para nomear entidades de Azure Data Factory
description: Descreve as regras de nomenclatura para entidades de Data Factory.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: a1a0622c0736bad5f6c205fab01f4405577ed67a
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104783346"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory - regras de nomenclatura

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A tabela a seguir fornece regras de nomenclatura para artefatos Data Factory.

| Nome | Exclusividade do nome | Verificações de validação |
|:--- |:--- |:--- |
| Fábrica de dados | Exclusivo em todo o Microsoft Azure. Os nomes não diferenciam maiúsculas de minúsculas, isto é, `MyDF` e `mydf` referem-se ao mesmo data factory. |<ul><li>Cada data factory está vinculado a exatamente uma assinatura do Azure.</li><li>Os nomes do objeto devem começar com uma letra ou número e podem conter apenas letras, números e o caractere traço (-).</li><li>Cada caractere traço (-) deve ser imediatamente precedido e seguido por uma letra ou um número. Não há permissão para traços consecutivos em nomes de contêiner.</li><li>O nome pode ter de 3 a 63 caracteres.</li></ul> |
| Serviços vinculados/conjuntos de dados/pipelines/fluxos de dados | Exclusivo em um data factory. Os nomes não diferenciam maiúsculas de minúsculas. |<ul><li>Os nomes de objeto devem começar com uma letra.</li><li>Os seguintes caracteres não são permitidos: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", " \\ "</li><li>Os traços ("-") não são permitidos nos nomes de serviços vinculados, fluxos de dados e conjuntos de dados.</li></ul>  |
| Integration Runtime |Exclusivo em um data factory. Os nomes não diferenciam maiúsculas de minúsculas. |<ul><li>O nome do tempo de execução de integração pode conter apenas letras, números e o caractere de traço (-).</li><li>O primeiro e o último caracteres devem ser uma letra ou um número. Cada caractere traço (-) deve ser imediatamente precedido e seguido por uma letra ou um número.</li><li>Traços consecutivos não são permitidos no nome do tempo de execução de integração. </li></ul> |
| Transformações do fluxo de dados | Exclusivo em um fluxo de dados. Os nomes não diferenciam maiúsculas de minúsculas | <ul><li>Os nomes de transformação de fluxo de dados só podem conter letras e números</li><li>O primeiro caractere deve ser uma letra. </li></ul> |
| Grupo de recursos |Exclusivo em todo o Microsoft Azure. Os nomes não diferenciam maiúsculas de minúsculas. | Para saber mais, veja [Restrições e regras de nomenclatura do Azure](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming). |
| Parâmetros de pipeline & variável  |Exclusivo no pipeline. Os nomes não diferenciam maiúsculas de minúsculas. | <ul><li>A verificação de validação em nomes de parâmetros e nomes de variáveis é limitada à exclusividade devido ao motivo da compatibilidade com versões anteriores.</li><li>Ao usar parâmetros ou variáveis para referenciar nomes de entidade, por exemplo, serviço vinculado, as regras de nomenclatura de entidade se aplicam.</li><li>Uma prática recomendada é seguir as regras de nomenclatura de transformação de fluxo de dados para nomear seus parâmetros e variáveis de pipeline.</li></ul> |

## <a name="next-steps"></a>Próximas etapas

Saiba como criar data factories, seguindo as instruções passo a passo no artigo [Início rápido: criar um data factory](quickstart-create-data-factory-powershell.md). 

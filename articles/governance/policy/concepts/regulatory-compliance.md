---
title: Conformidade regulatória em definições de iniciativa
description: Descreve como usar uma definição de iniciativa para agrupar políticas por domínio normativo, como controle de acesso, gerenciamento de configuração e outros.
ms.date: 08/17/2020
ms.topic: conceptual
ms.openlocfilehash: 15b03b5a40cba1290859b02883df70e3df54c5cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89645521"
---
# <a name="regulatory-compliance-in-azure-policy"></a>Conformidade regulatória no Azure Policy

A conformidade regulatória no Azure Policy fornece definições de iniciativa internas para exibir uma lista dos **controles** e **domínios de conformidade** com base na responsabilidade (_cliente_, _Microsoft_, _compartilhado_).
Para controles responsáveis pela Microsoft, fornecemos detalhes adicionais de nossos resultados de auditoria com base em atestado de terceiros e nossos detalhes de implementação para atingir essa conformidade.
Os controles responsáveis pela Microsoft são `type` [estáticos](./definition-structure.md#type).

> [!NOTE]
> A conformidade regulatória é um recurso de visualização. Para os internos atualizados, os controles de iniciativas são mapeados para o padrão de conformidade correspondente. As iniciativas de conformidade padrão existentes estão em processo de atualização para dar suporte à conformidade regulatória.

## <a name="regulatory-compliance-defined"></a>Conformidade regulatória definida

A conformidade regulatória baseia-se na porção de [agrupamento](./initiative-definition-structure.md#policy-definition-groups) de uma definição de iniciativa. Em entradas internas, cada agrupamento na definição de iniciativa define um nome (**controle**), uma categoria (**domínio de conformidade**) e fornece uma referência ao objeto [policyMetadata](./initiative-definition-structure.md#metadata-objects) que tem informações sobre esse **controle**. Uma definição de iniciativa de conformidade regulatória deve ter a `category` propriedade definida como **conformidade regulatória**. Como uma definição de iniciativa padrão, as iniciativas de conformidade regulatória dão suporte a [parâmetros](./initiative-definition-structure.md#parameters) para criar atribuições dinâmicas.

Os clientes podem criar suas próprias iniciativas de conformidade regulatória. Essas definições podem ser originais ou copiadas de definições internas existentes. Se estiver usando uma definição de iniciativa de conformidade regulatória interna como referência, é recomendável monitorar a origem das definições de conformidade regulatória no [repositório GitHub Azure Policy](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance).

Para vincular uma iniciativa de conformidade regulatória personalizada ao painel da central de segurança do Azure, consulte [central de segurança do Azure-usando políticas de segurança personalizadas](../../../security-center/custom-security-policies.md).

## <a name="regulatory-compliance-in-portal"></a>Conformidade regulatória no portal

Quando uma definição de iniciativa foi criada com [grupos](./initiative-definition-structure.md#policy-definition-groups), a página de detalhes de **conformidade** no portal dessa iniciativa tem informações adicionais. 

Uma nova guia, os **controles** são adicionados à página. A filtragem está disponível pelo **domínio de conformidade** e as definições de política são agrupadas pelo `title` campo do objeto **policyMetadata** . Cada linha representa um **controle** que mostra seu estado de conformidade, o **domínio de conformidade** do qual faz parte, informações de responsabilidade e quantas definições de política em conformidade e sem conformidade compõem esse **controle**.

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-overview.png" alt-text="Captura de tela da visão geral da conformidade regulatória para a definição interna do NIST SP 800-53 R4 mostrando controles em conformidade e sem conformidade.":::

A seleção de um **controle** abre uma página de detalhes sobre esse controle. A **visão geral** contém as informações de `description` e `requirements` . Na guia **políticas** , há todas as definições de política individuais na iniciativa que contribuem com esse **controle**. A guia **conformidade de recursos** fornece uma exibição granular de cada recurso que é avaliado por uma política de membro do **controle**exibido no momento.

> [!NOTE]
> Um tipo de avaliação do **gerenciado pela Microsoft** é para uma definição de política [estática](./definition-structure.md#type) `type` .

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-policies.png" alt-text="Captura de tela da visão geral da conformidade regulatória para a definição interna do NIST SP 800-53 R4 mostrando controles em conformidade e sem conformidade.":::

Na mesma página de **controle** , a alteração para a guia **conformidade de recursos** mostra todos os recursos que as definições de política do **controle**incluem. Os filtros estão disponíveis para nome ou ID, estado de conformidade, tipo de recurso e local.

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-resources.png" alt-text="Captura de tela da visão geral da conformidade regulatória para a definição interna do NIST SP 800-53 R4 mostrando controles em conformidade e sem conformidade.":::

## <a name="regulatory-compliance-in-sdk"></a>Conformidade regulatória no SDK

Se a conformidade regulatória estiver habilitada em uma definição de iniciativa, o registro de verificação de avaliação, os eventos e o SDK de Estados de política retornarão propriedades adicionais. Essas propriedades adicionais são agrupadas por estado de conformidade e fornecem informações sobre quantos grupos estão em cada Estado.

O código a seguir é um exemplo de resultados adicionados de uma `summarize` chamada:

```json
"policyGroupDetails": [{
        "complianceState": "noncompliant",
        "count": 4
    },
    {
        "complianceState": "compliant",
        "count": 76
    }
]
```

## <a name="next-steps"></a>Próximas etapas

- Consulte a [estrutura de definição de iniciativa](./initiative-definition-structure.md)
- Examine os exemplos em [Amostras do Azure Policy](../samples/index.md).
- Revisar [Compreendendo os efeitos da política](./effects.md).
- Saiba como [corrigir recursos fora de conformidade](../how-to/remediate-resources.md).

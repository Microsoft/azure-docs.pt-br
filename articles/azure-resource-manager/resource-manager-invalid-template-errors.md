---
title: "Erros de modelo inválido do Azure | Microsoft Docs"
description: "Descreve como resolver erros de modelo inválido."
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: e58c10cfb4cdd4ba49945e6c19845cbc957d6326
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-errors-for-invalid-template"></a>Resolva erros de modelo inválido

Este artigo descreve como resolver erros de modelo inválido.

## <a name="symptom"></a>Sintoma

Ao implantar um modelo, você recebe um erro indicando que:

```
Code=InvalidTemplate
Message=<varies>
```

A mensagem de erro depende do tipo de erro.

## <a name="cause"></a>Causa

Esse erro pode resultar de vários tipos diferentes de erros. Normalmente, ele envolve um erro de sintaxe ou estrutural no modelo.

## <a name="solution"></a>Solução

### <a name="solution-1---syntax-error"></a>Solução 1 – erro de sintaxe

Se você receber uma mensagem de erro que indica a validação do modelo falhou, você poderá ter um problema de sintaxe em seu modelo.

```
Code=InvalidTemplate
Message=Deployment template validation failed
```

Esse erro é fácil de cometer porque as expressões do modelo podem ser complexas. Por exemplo, a atribuição de nome a seguir para uma conta de armazenamento contém um par de colchetes, três funções, três pares de parênteses, um par de aspas simples e uma propriedade:

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

Se você não fornecer a sintaxe correspondente, o modelo produzirá um valor diferente do que era sua intenção.

Quando você receber esse tipo de erro, examine cuidadosamente a sintaxe da expressão. Considere usar um editor JSON como o [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) ou o [Visual Studio Code](resource-manager-vs-code.md) que pode avisá-lo sobre os erros de sintaxe.

### <a name="solution-2---incorrect-segment-lengths"></a>Solução 2 – comprimentos de segmento incorretos

Outro erro de modelo inválido ocorre quando o nome do recurso não está no formato correto.

```
Code=InvalidTemplate
Message=Deployment template validation failed: 'The template resource {resource-name}'
for type {resource-type} has incorrect segment lengths.
```

Um recurso no nível-raiz deve ter um segmento a menos no nome que no tipo de recurso. Cada segmento é diferenciado por uma barra. No exemplo a seguir, o tipo tem dois segmentos e o nome tem um segmento, portanto, é um **nome válido**.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "myHostingPlanName",
  ...
}
```

Mas o exemplo a seguir não é **um nome válido** porque ele tem o mesmo número de segmentos do tipo.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "appPlan/myHostingPlanName",
  ...
}
```

Para os recursos-filho, o tipo e o nome têm o mesmo número de segmentos. Esse número de segmentos faz sentido, porque o nome completo e o tipo para o filho incluem o nome do pai e o tipo. Portanto, o nome completo ainda tem um segmento a menos que o tipo completo.

```json
"resources": [
    {
        "type": "Microsoft.KeyVault/vaults",
        "name": "contosokeyvault",
        ...
        "resources": [
            {
                "type": "secrets",
                "name": "appPassword",
                ...
            }
        ]
    }
]
```

Ter os segmentos corretos pode ser difícil com os tipos de Resource Manager que são aplicados aos provedores de recursos. Por exemplo, aplicar um bloqueio de recurso a um site da Web requer um tipo com quatro segmentos. Portanto, o nome tem três segmentos:

```json
{
    "type": "Microsoft.Web/sites/providers/locks",
    "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
    ...
}
```

### <a name="solution-3---parameter-is-not-valid"></a>Solução 3 – o parâmetro não é válido

Se o modelo especificar os valores permitidos para um parâmetro e você fornecer um valor que não é um deles, você receberá uma mensagem semelhante ao erro a seguir:

```
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

Verifique uma segunda vez os valores permitidos no modelo e forneça um durante a implantação.

### <a name="solution-4---circular-dependency-detected"></a>Solução 4 – dependência circular detectada

Você recebe esse erro quando recursos dependem entre si de uma maneira que impede que a implantação seja iniciado. Uma combinação de interdependências faz com que dois ou mais recursos aguardar para outros recursos que também estão aguardando. Por exemplo, recurso1 depende resource3 resource2 depende recurso1 e resource3 depende resource2. Geralmente você pode resolver esse problema removendo dependências desnecessárias.

---
title: Erros de modelo inválido do Azure | Microsoft Docs
description: Descreve como resolver erros de modelo inválido.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: troubleshooting
ms.date: 03/08/2018
ms.author: tomfitz
ms.openlocfilehash: 0417a975ffbbe0acd94ceac6c0f8e1de27a27aa6
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206253"
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

<a id="syntax-error" />

## <a name="solution-1---syntax-error"></a>Solução 1 – erro de sintaxe

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

<a id="incorrect-segment-lengths" />

## <a name="solution-2---incorrect-segment-lengths"></a>Solução 2 – comprimentos de segmento incorretos

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

<a id="parameter-not-valid" />

## <a name="solution-3---parameter-is-not-valid"></a>Solução 3 – o parâmetro não é válido

Se você fornecer um valor de parâmetro que não seja um dos valores permitidos, receberá uma mensagem semelhante ao erro a seguir:

```
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

Verifique uma segunda vez os valores permitidos no modelo e forneça um durante a implantação. Para saber mais sobre os valores de parâmetro permitidos, confira a [seção Parâmetros dos modelos do Azure Resource Manager](resource-group-authoring-templates.md#parameters).

<a id="too-many-resource-groups" />

## <a name="solution-4---too-many-target-resource-groups"></a>Solução 4 – Muitos grupos de recursos de destino

Se especificar mais de cinco grupos de recursos de destino em uma única implantação, você receberá esse erro. Considere a possibilidade de consolidar o número de grupos de recursos em sua implantação ou implantar alguns dos modelos como implantações separadas. Para saber mais, consulte [Implantar recursos do Azure em mais de uma assinatura ou grupo de recursos](resource-manager-cross-resource-group-deployment.md).

<a id="circular-dependency" />

## <a name="solution-5---circular-dependency-detected"></a>Solução 5 – Dependência circular detectada

Você recebe esse erro quando recursos dependem entre si de uma maneira que impede que a implantação seja iniciado. Uma combinação de interdependências faz com que dois ou mais recursos aguardar para outros recursos que também estão aguardando. Por exemplo, recurso1 depende resource3 resource2 depende recurso1 e resource3 depende resource2. Geralmente você pode resolver esse problema removendo dependências desnecessárias.

Para resolver uma dependência circular:

1. No modelo, localize o recurso identificado na dependência circular. 
2. Para esse recurso, examine o **dependsOn** propriedade e quaisquer usos da **referência** função para ver quais recursos ele depende. 
3. Examine os recursos para ver quais recursos eles dependem. Siga as dependências até você perceber um recurso depende do recurso original.
5. Para os recursos envolvidos na dependência circular, examine cuidadosamente todas as funções de **dependsOn** propriedade para identificar quaisquer dependências que não são necessários. Remova essas dependências. Se você não tiver certeza de que uma dependência é necessária, tente removê-lo. 
6. Reimplante o modelo.

Removendo valores do **dependsOn** propriedade pode causar erros quando você implanta o modelo. Se você receber um erro, adicione a dependência de volta ao modelo. 

Se essa abordagem não resolver a dependência circular, considere mover parte de sua lógica de implantação para recursos filho (como extensões ou definições de configuração). Configure os recursos filho para implantar após os recursos envolvidos na dependência circular. Por exemplo, suponha que você estiver implantando duas máquinas virtuais, mas você deve definir propriedades em cada um deles que se referem a outro. Você pode implantá-los na seguinte ordem:

1. vm1
2. vm2
3. Extensão na vm1 depende vm1 e vm2. A extensão define valores na vm1 que ele obtém da vm2.
4. Extensão da vm2 depende vm1 e vm2. A extensão define valores de vm2 obtido do vm1.

A mesma abordagem funciona para aplicativos de serviço de aplicativo. Considere a mudança de valores de configuração em um recurso filho de recurso de aplicativo. Você pode implantar dois aplicativos web na seguinte ordem:

1. webapp1
2. webapp2
3. a configuração para webapp1 depende de webapp1 e webapp2. Ele contém configurações do aplicativo com os valores do webapp2.
4. a configuração para webapp2 depende de webapp1 e webapp2. Ele contém configurações do aplicativo com os valores do webapp1.

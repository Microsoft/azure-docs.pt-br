---
title: O tamanho do trabalho excedeu o erro
description: Descreve como solucionar erros quando o tamanho ou o modelo do trabalho é muito grande.
ms.topic: troubleshooting
ms.date: 03/23/2021
ms.openlocfilehash: b39a0bba15e73bab1a85cbd9e36efebf82d6cf42
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889358"
---
# <a name="resolve-errors-for-job-size-exceeded"></a>Resolução de erros para o tamanho do trabalho excedido

Este artigo descreve como resolver os erros **JobSizeExceededException** e **DeploymentJobSizeExceededException** .

## <a name="symptom"></a>Sintoma

Ao implantar um modelo, você receberá um erro informando que a implantação excedeu os limites.

## <a name="cause"></a>Causa

Você receberá esse erro quando a implantação exceder um dos limites permitidos. Normalmente, você vê esse erro quando o modelo ou o trabalho que executa a implantação é muito grande.

O trabalho de implantação não pode exceder 1 MB. O trabalho inclui metadados sobre a solicitação. Para modelos grandes, os metadados combinados com o modelo podem exceder o tamanho permitido para um trabalho.

O modelo não pode exceder 4 MB. O limite de 4 MB se aplica ao estado final do modelo depois que ele é expandido para definições de recursos que usam [Copy](copy-resources.md) para criar várias instâncias. O estado final também inclui os valores resolvidos para variáveis e parâmetros.

Outros limites para o modelo são:

* 256 parâmetros
* 256 variáveis
* 800 recursos (incluindo a contagem de cópias)
* 64 valores de saída
* 24.576 caracteres em uma expressão de modelo

Ao usar loops de cópia para implantar o recurso, não use o nome do loop como uma dependência:

```json
dependsOn: [ "nicLoop" ]
```

Em vez disso, use a instância do recurso do loop do qual você precisa depender. Por exemplo:

```json
dependsOn: [
    "[resourceId('Microsoft.Network/networkInterfaces', concat('nic-', copyIndex()))]"
]
```

## <a name="solution-1---simplify-template"></a>Solução 1 – simplificar o modelo

Sua primeira opção é simplificar o modelo. Essa opção funciona quando o modelo implanta muitos tipos diferentes de recursos. Considere dividir o modelo em [modelos vinculados](linked-templates.md). Divida os tipos de recursos em grupos lógicos e adicione um modelo vinculado para cada grupo. Por exemplo, se você precisar implantar muitos recursos de rede, poderá mover esses recursos para um modelo vinculado.

Você pode definir outros recursos como dependentes do modelo vinculado e [obter valores da saída do modelo vinculado](linked-templates.md#get-values-from-linked-template).

## <a name="solution-2---reduce-name-size"></a>Solução 2 – reduzir o tamanho do nome

Tente reduzir o comprimento dos nomes que você usa para [parâmetros](template-parameters.md), [variáveis](template-variables.md)e [saídas](template-outputs.md). Quando esses valores são repetidos por meio de loops de cópia, um nome grande é multiplicado muitas vezes.
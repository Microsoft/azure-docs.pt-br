---
title: O tamanho do trabalho excedeu o erro
description: Descreve como solucionar erros quando o tamanho ou o modelo do trabalho é muito grande.
ms.topic: troubleshooting
ms.date: 01/19/2021
ms.openlocfilehash: 1fde4918aff6e3bf494876f83c5b4313b3c5f3d2
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98610396"
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

## <a name="solution-1---simplify-template"></a>Solução 1 – simplificar o modelo

Sua primeira opção é simplificar o modelo. Essa opção funciona quando o modelo implanta muitos tipos diferentes de recursos. Considere dividir o modelo em [modelos vinculados](linked-templates.md). Divida os tipos de recursos em grupos lógicos e adicione um modelo vinculado para cada grupo. Por exemplo, se você precisar implantar muitos recursos de rede, poderá mover esses recursos para um modelo vinculado.

Você pode definir outros recursos como dependentes do modelo vinculado e [obter valores da saída do modelo vinculado](linked-templates.md#get-values-from-linked-template).

## <a name="solution-2---reduce-name-size"></a>Solução 2 – reduzir o tamanho do nome

Tente reduzir o comprimento dos nomes que você usa para [parâmetros](template-parameters.md), [variáveis](template-variables.md)e [saídas](template-outputs.md). Quando esses valores são repetidos por meio de loops de cópia, um nome grande é multiplicado muitas vezes.

## <a name="solution-3---use-serial-copy"></a>Solução 3 – usar a cópia serial

Considere alterar o loop de cópia do [processamento paralelo para o serial](copy-resources.md#serial-or-parallel). Use esta opção somente quando você suspeitar de que o erro vem da implantação de um grande número de recursos por meio de cópia. Essa alteração pode aumentar significativamente o tempo de implantação porque os recursos não são implantados em paralelo.

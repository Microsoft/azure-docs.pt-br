---
title: Tipos de runbook da Automação do Azure
description: 'Descreve os diferentes tipos de runbook que você pode usar na Automação do Azure e as considerações que devem ser levadas em conta ao determinar qual tipo usar. '
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 7a3990366814beda83852fc7c07c896445388c23
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60737152"
---
# <a name="azure-automation-runbook-types"></a>Tipos de runbook da Automação do Azure

A Automação do Azure dá suporte a vários tipos de runbooks descritos brevemente na tabela a seguir.  As seções abaixo fornecem mais informações sobre cada tipo, incluindo considerações sobre quando usar cada um.

| Type | DESCRIÇÃO |
|:--- |:--- |
| [Gráfico](#graphical-runbooks)|Com base no Windows PowerShell e criado e editado completamente no editor gráfico do portal do Azure. |
| [Fluxo de Trabalho Gráfico do PowerShell](#graphical-runbooks)|Com base no Fluxo de Trabalho do Windows PowerShell e criado e editado completamente no editor gráfico do portal do Azure. |
| [PowerShell](#powershell-runbooks) |Runbook de texto com base no script do Windows PowerShell. |
| [Fluxo de Trabalho do PowerShell](#powershell-workflow-runbooks)|Runbook de texto com base no Fluxo de Trabalho do Windows PowerShell. |
| [Python](#python-runbooks) |Runbook de texto com base em Python. |

## <a name="graphical-runbooks"></a>Runbooks gráficos

[Gráfico](automation-runbook-types.md#graphical-runbooks) and Gráfico PowerShell Workflow runbooks are created and edited with the graphical editor in the Azure portal.  Você pode exportá-los para um arquivo e importá-los em outra conta de automação. Porém, não é possível criar nem editá-los com outra ferramenta. Os runbooks gráficos geram código do PowerShell, mas você não pode exibir nem modificar diretamente o código. Os runbooks gráficos não podem ser convertidos em um dos [formatos de texto](automation-runbook-types.md), nem um runbook de texto pode ser convertido em formato gráfico. Runbooks gráficos podem ser convertidos para runbooks de Fluxo de Trabalho Gráfico do PowerShell durante a importação e vice-versa.

### <a name="advantages"></a>Vantagens

* Modelo visual de criação para configurar link de inserção  
* Foco em como os dados fluem no processo  
* Representar visualmente os processos de gerenciamento  
* Incluir outros runbooks como runbooks-filho para criar fluxos de trabalho de alto nível  
* Incentiva a programação modular  

### <a name="limitations"></a>Limitações

* Não é possível editar um runbook fora do portal do Azure.
* Pode exigir uma atividade de Código que contenha código do PowerShell para executar lógicas complexas.
* Não é possível exibir ou editar diretamente o código do PowerShell criado pelo fluxo de trabalho gráfico. Você pode exibir o código criado em todas as atividades de Código.
* Não pode ser executada em um Hybrid Runbook Worker do Linux

## <a name="powershell-runbooks"></a>Runbooks do PowerShell

Os runbooks do PowerShell se baseiam no Windows PowerShell.  Você edita o código do runbook diretamente usando o editor de texto do portal do Azure.  Também é possível usar qualquer editor de texto offline e [importar o runbook](manage-runbooks.md) na Automação do Azure.

### <a name="advantages"></a>Vantagens

* Implemente toda a lógica complexa com o código do PowerShell sem as complexidades adicionais do Fluxo de Trabalho do PowerShell.
* O runbook inicia mais rapidamente do que os runbooks do Fluxo de Trabalho do PowerShell, uma vez que não precisa ser compilado antes da execução.
* Pode ser executado no Azure ou no Linux e Windows Hybrid Runbook Workers

### <a name="limitations"></a>Limitações

* Exige familiarização com a criação de script do PowerShell.
* Não é possível usar o [processamento paralelo](automation-powershell-workflow.md#parallel-processing) para executar várias ações paralelamente.
* Não é possível usar os [pontos de verificação](automation-powershell-workflow.md#checkpoints) para retomar o runbook em caso de erro.
* Os runbooks de Fluxo de Trabalho e Gráficos do PowerShell só podem ser incluídos como runbooks filho usando o cmdlet Start-AzureAutomationRunbook, que cria um novo trabalho.

### <a name="known-issues"></a>Problemas conhecidos

Veja a seguir problemas conhecidos atuais com os runbooks do PowerShell.

* Os runbooks do PowerShell não podem recuperar um [ativo variável](automation-variables.md) não criptografado com um valor nulo.
* Os runbooks do PowerShell não podem recuperar um [ativo variável](automation-variables.md) com *~* no nome.
* Get-Process em um loop em um runbook do PowerShell pode falhar após cerca de 80 iterações.
* Um runbook do PowerShell poderá falhar se tentar gravar uma grande quantidade de dados no fluxo de saída de uma vez.   Geralmente, você pode contornar esse problema emitindo apenas as informações que precisa ao trabalhar com objetos grandes.  Por exemplo, em vez de produzir algo como *Get-Process*, você pode exibir apenas os campos obrigatórios com *Get-Process | Selecionar ProcessName, CPU*.

## <a name="powershell-workflow-runbooks"></a>Runbooks do Fluxo de Trabalho do PowerShell

Os runbooks do Fluxo de Trabalho do PowerShell são runbooks de texto baseados no [Fluxo de Trabalho do Windows PowerShell](automation-powershell-workflow.md).  Você edita o código do runbook diretamente usando o editor de texto do portal do Azure.  Também é possível usar qualquer editor de texto offline e [importar o runbook](manage-runbooks.md) na Automação do Azure.

### <a name="advantages"></a>Vantagens

* Implemente toda a lógica complexa com o código do Fluxo de Trabalho do PowerShell.
* Use os [pontos de verificação](automation-powershell-workflow.md#checkpoints) para retomar o runbook em caso de erro.
* Use o [processamento paralelo](automation-powershell-workflow.md#parallel-processing) para executar várias ações paralelamente.
* Pode incluir runbooks Gráficos e runbooks de Fluxo de Trabalho do PowerShell como runbooks filho para criar fluxos de trabalho de alto nível.

### <a name="limitations"></a>Limitações

* O autor deve estar familiarizado com o Fluxo de Trabalho do PowerShell.
* O runbook deve lidar com a complexidade adicional do Fluxo de Trabalho do PowerShell, como [objetos desserializados](automation-powershell-workflow.md#code-changes).
* O runbook demora mais para iniciar do que os runbooks do PowerShell pois precisa ser compilado antes da execução.
* Os runbooks do PowerShell só podem ser incluídos como runbooks filho usando o cmdlet Start-AzureAutomationRunbook, que cria um novo trabalho.
* Não pode ser executada em um Hybrid Runbook Worker do Linux

## <a name="python-runbooks"></a>Runbooks Python

Compilar runbooks Python em no Python 2.  Você pode editar diretamente o código do runbook usando o editor de texto do portal do Azure ou com qualquer editor de texto offline e [importar o runbook](manage-runbooks.md) para a Automação do Azure.

### <a name="advantages"></a>Vantagens

* Utilize as robustas bibliotecas do Python.
* Pode ser executado no Azure ou em ambos os Hybrid Runbook Workers do Linux. Windows Hybrid Runbook Workers são compatíveis com [Python 2.7](https://www.python.org/downloads/release/latest/python2) instalado.

### <a name="limitations"></a>Limitações

* Você deve estar familiarizado com a criação de scripts do Python.
* Somente o Python 2 tem suporte no momento, o que significa que as funções específicas do Python 3 falharão.
* Para utilizar bibliotecas de terceiros, você deve [importar o pacote](python-packages.md) para a Conta de Automação para que ele seja usado.

## <a name="considerations"></a>Considerações

Leve em conta as considerações adicionais a seguir ao determinar qual tipo usar para um runbook específico.

* Você não pode converter runbooks de tipo de gráfico para texto ou vice-versa.
* Há limitações ao usar runbooks de tipos diferentes, como um runbook filho. Para obter mais informações, confira [Runbooks filho na Automação do Azure](automation-child-runbooks.md).

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre a criação de runbooks Gráficos, veja [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md)
* Para entender as diferenças entre o PowerShell e o os fluxos de trabalho do PowerShell para runbooks, consulte [Aprendendo sobre o fluxo de trabalho do Windows PowerShell](automation-powershell-workflow.md)
* Para obter mais informações sobre como criar ou importar um Runbook, consulte [Criando ou Importando um Runbook](manage-runbooks.md)


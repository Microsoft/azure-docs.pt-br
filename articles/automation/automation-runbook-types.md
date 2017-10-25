---
title: "Tipos de Runbook de Automação do Azure | Microsoft Docs"
description: "Descreve os diferentes tipos de runbook que você pode usar na Automação do Azure e as considerações que devem ser levadas em conta ao determinar qual tipo usar. "
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: 9265c975-4281-4819-a84f-d86641277f36
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/01/2017
ms.author: bwren
ms.openlocfilehash: 4bf4a3d755afeee9930204a2dbae9ff9fada3517
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-automation-runbook-types"></a>Tipos de runbook da Automação do Azure
A Automação do Azure dá suporte a vários tipos de runbooks descritos brevemente na tabela a seguir.  As seções abaixo fornecem mais informações sobre cada tipo, incluindo considerações sobre quando usar cada um.

| Tipo | Descrição |
|:--- |:--- |
| [Gráfico](#graphical-runbooks) |Com base no Windows PowerShell e criado e editado completamente no editor gráfico do portal do Azure. |
| [Fluxo de Trabalho Gráfico do PowerShell](#graphical-runbooks) |Com base no Fluxo de Trabalho do Windows PowerShell e criado e editado completamente no editor gráfico do portal do Azure. |
| [PowerShell](#powershell-runbooks) |Runbook de texto com base no script do Windows PowerShell. |
| [Fluxo de Trabalho do PowerShell](#powershell-workflow-runbooks) |Runbook de texto com base no Fluxo de Trabalho do Windows PowerShell. |
| [Python](#python-runbooks) |Runbook de texto com base em Python. |
| [Bash](#bash-runbooks) |Runbook de texto com base no Bash. |

## <a name="graphical-runbooks"></a>Runbooks gráficos
[Gráfico](automation-runbook-types.md#graphical-runbooks) and Gráfico PowerShell Workflow runbooks are created and edited with the graphical editor in the Azure portal.  Você pode exportá-los para um arquivo e depois importá-los em outra conta de automação, mas não pode criá-los nem editá-los com outra ferramenta.  Os runbooks gráficos geram código do PowerShell, mas você não pode exibir nem modificar diretamente o código. Os runbooks gráficos não podem ser convertidos em um dos [formatos de texto](automation-runbook-types.md), nem um runbook de texto pode ser convertido em formato gráfico. Runbooks gráficos podem ser convertidos para runbooks de Fluxo de Trabalho Gráfico do PowerShell durante a importação e vice-versa.

### <a name="advantages"></a>Vantagens
* Modelo visual de criação para configurar link de inserção  
* Foco em como os dados fluem no processo  
* Representar visualmente os processos de gerenciamento  
* Incluir outros runbooks como runbooks-filho para criar fluxos de trabalho de alto nível  
* Incentiva a programação modular  


### <a name="limitations"></a>Limitações
* Não é possível editar um runbook fora do portal do Azure.
* Pode exigir uma atividade de Código que contenha código do PowerShell para executar lógicas complexas.
* Não é possível exibir ou editar diretamente o código do PowerShell criado pelo fluxo de trabalho gráfico. Observe que você pode exibir o código criado em todas as atividades de Código.

## <a name="powershell-runbooks"></a>Runbooks do PowerShell
Os runbooks do PowerShell se baseiam no Windows PowerShell.  Você edita o código do runbook diretamente usando o editor de texto do portal do Azure.  Também é possível usar qualquer editor de texto offline e [importar o runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) na Automação do Azure.

### <a name="advantages"></a>Vantagens
* Implemente toda a lógica complexa com o código do PowerShell sem as complexidades adicionais do Fluxo de Trabalho do PowerShell. 
* O runbook inicia mais rapidamente do que os runbooks do Fluxo de Trabalho do PowerShell, uma vez que não precisa ser compilado antes da execução.

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
Os runbooks do Fluxo de Trabalho do PowerShell são runbooks de texto baseados no [Fluxo de Trabalho do Windows PowerShell](automation-powershell-workflow.md).  Você edita o código do runbook diretamente usando o editor de texto do portal do Azure.  Também é possível usar qualquer editor de texto offline e [importar o runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) na Automação do Azure.

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

## <a name="python-runbooks"></a>Runbooks Python

## <a name="bash-runbooks"></a>Runbooks do Bash

## <a name="considerations"></a>Considerações
Você deve levar em conta as considerações adicionais a seguir ao determinar qual tipo usar para um runbook específico.

* Você não pode converter runbooks de tipo de gráfico para texto ou vice-versa.
* Há limitações ao usar runbooks de tipos diferentes, como um runbook filho.  Veja [Runbooks filho na Automação do Azure](automation-child-runbooks.md) para obter mais informações.

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre a criação de runbooks Gráficos, veja [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md)
* Para entender as diferenças entre o PowerShell e o os fluxos de trabalho do PowerShell para runbooks, consulte [Aprendendo sobre o fluxo de trabalho do Windows PowerShell](automation-powershell-workflow.md)
* Para obter mais informações sobre como criar ou importar um Runbook, consulte [Criando ou Importando um Runbook](automation-creating-importing-runbook.md)


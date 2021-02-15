---
title: Tipos de runbook da Automação do Azure
description: Este artigo descreve os diferentes tipos de runbook que você pode usar na Automação do Azure e as considerações para determinar qual tipo usar.
services: automation
ms.subservice: process-automation
ms.date: 01/08/2021
ms.topic: conceptual
ms.openlocfilehash: c1398d25b6d2540abea0012acd69555e5e53e25c
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98050962"
---
# <a name="azure-automation-runbook-types"></a>Tipos de runbook da Automação do Azure

O recurso de Automação de Processo da Automação do Azure dá suporte a vários tipos de runbooks, conforme definido na tabela a seguir. Para saber mais sobre o ambiente de automação de processo, veja [Execução de runbook na Automação do Azure](automation-runbook-execution.md).

| Type | Descrição |
|:--- |:--- |
| [Gráfico](#graphical-runbooks)|Runbook gráfico baseado no Windows PowerShell e criado e editado totalmente no editor gráfico do portal do Azure. |
| [Fluxo de Trabalho Gráfico do PowerShell](#graphical-runbooks)|Runbook gráfico baseado no Fluxo de Trabalho do Windows PowerShell e criado e editado totalmente no editor gráfico do portal do Azure. |
| [PowerShell](#powershell-runbooks) |Runbook textual baseado em scripts do Windows PowerShell. |
| [Fluxo de Trabalho do PowerShell](#powershell-workflow-runbooks)|Runbook textual com base no script de fluxo de trabalho do Windows PowerShell. |
| [Python](#python-runbooks) |Runbook textual baseado em scripts Python. |

Considere os fatores a seguir ao determinar qual tipo usar para um runbook específico.

* Não é possível converter runbooks do tipo gráfico para texto ou vice-versa.
* Existem limitações para o uso de runbooks de tipos diferentes, como runbooks filhos. Para obter mais informações, confira [Runbooks filho na Automação do Azure](automation-child-runbooks.md).

## <a name="graphical-runbooks"></a>Runbooks gráficos

Você pode criar e editar runbooks gráficos e runbooks gráficos de Fluxo de Trabalho do PowerShell usando o editor gráfico no portal do Azure. No entanto, não é possível criar ou editar este tipo de runbook com outra ferramenta. Principais recursos dos runbooks gráficos:

* Exportado para arquivos em sua conta de automação e, em seguida, importado para outra conta de automação.
* Gerar código do PowerShell.
* Convertidos de ou para runbooks gráficos do fluxo de trabalho do PowerShell durante a importação.

### <a name="advantages"></a>Vantagens

* Uso do modelo visual de criação para configurar link de inserção.
* Foco em como os dados fluem no processo.
* Represente visualmente os processos de gerenciamento.
* Inclui outros runbooks como runbooks filhos para criar fluxos de trabalho de alto nível.
* Incentiva a programação modular.

### <a name="limitations"></a>Limitações

* Não é possível criar ou editar fora do portal do Azure.
* Pode exigir uma atividade de código que contenha código do PowerShell para executar lógica complexa.
* Não é possível converter para dos [formatos de texto](automation-runbook-types.md), nem converter um runbook de texto para formato gráfico. 
* Não é possível exibir ou editar diretamente o código do PowerShell que o fluxo de trabalho gráfico cria. É possível exibir o código criado em todas as atividades do código.
* Não é possível executar runbooks em um Hybrid Runbook Worker do Linux. Veja [Automação de recursos em seu datacenter ou nuvem usando o Hybrid Runbook Worker](automation-hybrid-runbook-worker.md).

## <a name="powershell-runbooks"></a>Runbooks do PowerShell

Os runbooks do PowerShell se baseiam no Windows PowerShell. Você edita o código do runbook diretamente usando o editor de texto do portal do Azure.  Também é possível usar qualquer editor de texto offline e [importar o runbook](manage-runbooks.md) na Automação do Azure.

### <a name="advantages"></a>Vantagens

* Implemente toda a lógica complexa com o código do PowerShell sem as outras complexidades do fluxo de trabalho do PowerShell.
* Inicia mais rápido que os runbooks de Fluxo de Trabalho do PowerShell, já que não precisa ser compilado antes da execução.
* Pode ser executado no Azure e em Hybrid Runbook Workers para Windows e Linux.

### <a name="limitations"></a>Limitações

* Exige familiarização com script do PowerShell.
* Os runbooks não usam [processamento paralelo](automation-powershell-workflow.md#use-parallel-processing) para executar várias ações paralelamente.
* Os runbooks não usam [pontos de verificação](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) para retomar o runbook em caso de erro.
* Os runbooks de Fluxo de Trabalho do PowerShell e os runbooks gráficos só podem ser incluídos como runbooks filhos usando o cmdlet [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook), que cria um novo trabalho.

### <a name="known-issues"></a>Problemas conhecidos

A seguir estão os problemas atualmente conhecidos com os runbooks do PowerShell:

* Os runbooks do PowerShell não conseguem recuperar um [ativo variável](./shared-resources/variables.md) não criptografado com um valor nulo.
* Os runbooks do PowerShell não conseguem recuperar um ativo variável com `*~*` no nome.
* Uma operação [Get-Process](/powershell/module/microsoft.powershell.management/get-process) em um loop dentro de um runbook do PowerShell pode falhar após cerca de 80 iterações.
* Um runbook do PowerShell pode falhar se tentar gravar uma grande quantidade de dados no fluxo de saída de uma só vez. Geralmente, você pode contornar esse problema fazendo com que o runbook emita apenas as informações necessárias para trabalhar com objetos grandes. Por exemplo, em vez de usar `Get-Process` sem limitações, faça com que o cmdlet gere apenas os parâmetros necessários como em `Get-Process | Select ProcessName, CPU`.

## <a name="powershell-workflow-runbooks"></a>Runbooks do Fluxo de Trabalho do PowerShell

Os runbooks do Fluxo de Trabalho do PowerShell são runbooks de texto baseados no [Fluxo de Trabalho do Windows PowerShell](automation-powershell-workflow.md). Você edita o código do runbook diretamente usando o editor de texto do portal do Azure. Também é possível usar qualquer editor de texto offline e [importar o runbook](manage-runbooks.md) na Automação do Azure.

### <a name="advantages"></a>Vantagens

* Implemente toda a lógica complexa com o código do Fluxo de Trabalho do PowerShell.
* Utiliza os [pontos de verificação](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) para retomar a operação caso ocorra um erro.
* Utiliza o [processamento paralelo](automation-powershell-workflow.md#use-parallel-processing) para executar várias ações paralelamente.
* Pode incluir outros runbooks gráficos e runbooks de Fluxo de Trabalho do PowerShell como runbooks filhos para criar fluxos de trabalho de alto nível.

### <a name="limitations"></a>Limitações

* Você deve estar familiarizado com o Fluxo de Trabalho do PowerShell.
* Os runbooks devem lidar com a complexidade adicional do Fluxo de Trabalho do PowerShell, como [objetos desserializados](automation-powershell-workflow.md#deserialized-objects).
* Os runbooks demoram mais para iniciar do que os runbooks do PowerShell, pois precisam ser compilados antes da execução.
* Você só pode incluir unbooks do PowerShell como runbooks filhos usando o cmdlet `Start-AzAutomationRunbook`.
* Os runbooks não podem ser executados em um Hybrid Runbook Worker do Linux.

## <a name="python-runbooks"></a>Runbooks Python

Os runbooks do Python são compilados em Python 2 e Python 3. Atualmente, os runbooks do Python 3 estão em versão prévia. Você pode editar o código do runbook diretamente usando o editor de texto do portal do Azure. Também é possível usar um editor de texto offline e [importar o runbook](manage-runbooks.md) na Automação do Azure.

### <a name="advantages"></a>Vantagens

* As bibliotecas robustas do Python podem ser usadas.
* Pode ser executado no Azure ou em Hybrid runbook Workers.
* Para o Python 2, há suporte para Hybrid runbook Workers do Windows com o [python 2,7](https://www.python.org/downloads/release/latest/python2) instalado.
* Para trabalhos em nuvem do Python 3, há suporte para a versão Python 3,8. Os scripts e pacotes de qualquer versão 3. x podem funcionar se o código for compatível entre diferentes versões.  
* Para trabalhos híbridos do Python 3 em computadores Windows, você pode optar por instalar qualquer versão 3. x que talvez queira usar.  
* Para trabalhos híbridos do Python 3 em computadores Linux, dependemos da versão do Python 3 instalada no computador para executar o programa omsconfig de DSC e o Linux Hybrid Worker. É recomendável instalar o 3,6 em computadores Linux. No entanto, versões diferentes também devem funcionar se não houver alterações significativas em assinaturas de método ou contratos entre versões do Python 3.

### <a name="limitations"></a>Limitações

* Você deve estar familiarizado com script do Python.
* Para utilizar bibliotecas de terceiros, você deve [importar os pacotes](python-packages.md) para a conta de Automação.
* O uso do cmdlet **Start-AutomationRunbook**   no fluxo de trabalho do PowerShell/PowerShell para iniciar um runbook do Python 3 (versão prévia) não funciona. Você pode usar o cmdlet **Start-AzAutomationRunbook** do módulo AZ. Automation ou o cmdlet **Start-AzureRmAutomationRunbook** do módulo AzureRm. Automation para contornar essa limitação.  
* Os runbooks do Python 3 (versão prévia) e os pacotes não funcionam com o PowerShell.
* A automação do Azure não dá suporte a **Sys. stderr**.

### <a name="known-issues"></a>Problemas conhecidos

Os trabalhos do Python 3 às vezes falham com uma mensagem de exceção *caminho executável do interpretador inválido*. Você poderá ver essa exceção se um trabalho estiver atrasado, iniciando mais de 10 minutos ou usando **Start-AutomationRunbook** para iniciar Runbooks do Python 3. Se o trabalho estiver atrasado, reiniciar o runbook deverá ser suficiente.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre os runbooks do PowerShell, veja o [Tutorial: Criação de um runbook do PowerShell](learn/automation-tutorial-runbook-textual-powershell.md).
* Para saber mais sobre os runbooks de Fluxo de Trabalho do PowerShell, veja o [Tutorial: Criação de um runbook de Fluxo de Trabalho do PowerShell](learn/automation-tutorial-runbook-textual.md).
* Para saber mais sobre runbooks gráficos, veja o [Tutorial: Criação de um runbook gráfico](learn/automation-tutorial-runbook-graphical.md).
* Para saber mais sobre os runbooks do Python, veja o [Tutorial: Criação de um runbook do Python](learn/automation-tutorial-runbook-textual-python2.md).

---
title: Visão geral do SDK do runbook Grpahical do Automação do Azure
description: Este artigo descreve como usar o SDK do Runbook gráfico de automação do Azure
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 07/20/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e4229079a1fa50295eef85b42f91bbc1b4a21fc3
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478599"
---
# <a name="use-the-azure-automation-graphical-runbook-sdk"></a>Use o SDK do runbook gráfico de automação do Azure

[Os runbooks gráficos](automation-graphical-authoring-intro.md) são runbooks que ajudam a gerenciar as complexidades do código subjacente do Windows PowerShell ou PowerShell Workflow. O SDK de criação gráfica de automação do Microsoft Azure permite que os desenvolvedores criem e editem Runbooks gráficos para uso com o serviço de automação do Azure. Os snippets de código a seguir mostram o fluxo básico da criação de um runbook gráfico a partir do seu código.

## <a name="pre-requisites"></a>Pré-requisitos

Para começar, importe o pacote `Microsoft.Azure.Automation.GraphicalRunbook.Model` para o seu projeto.

## <a name="create-a-runbook-object-instance"></a>Criar uma instância do objeto de runbook

Faça referência à montagem `Orchestrator.GraphRunbook.Model` e crie uma instância da classe `Orchestrator.GraphRunbook.Model.GraphRunbook`:

```csharp
using Orchestrator.GraphRunbook.Model;
using Orchestrator.GraphRunbook.Model.ExecutableView;

var runbook = new GraphRunbook();
```

## <a name="add-runbook-parameters"></a>Adicionar parâmetros de runbook

Instancie `Orchestrator.GraphRunbook.Model.Parameter` objetos e adicione-os ao runbook:

```csharp
runbook.AddParameter(
 new Parameter("YourName") {
  TypeName = typeof(string).FullName,
   DefaultValue = "World",
   Optional = true
 });

runbook.AddParameter(
 new Parameter("AnotherParameter") {
  TypeName = typeof(int).FullName, ...
 });
```

## <a name="add-activities-and-links"></a>Adicionar atividades e links

Instancie atividades de tipos apropriados e adicione-os ao runbook:

```csharp
var writeOutputActivityType = new CommandActivityType {
 CommandName = "Write-Output",
  ModuleName = "Microsoft.PowerShell.Utility",
 InputParameterSets = new [] {
  new ParameterSet {
   Name = "Default",
    new [] {
     new Parameter("InputObject"), ...
    }
  },
  ...
 }
};

var outputName = runbook.AddActivity(
 new CommandActivity("Output name", writeOutputActivityType) {
  ParameterSetName = "Default",
   Parameters = new ActivityParameters {
    {
     "InputObject",
     new RunbookParameterValueDescriptor("YourName")
    }
   },
   PositionX = 0,
   PositionY = 0
 });

var initializeRunbookVariable = runbook.AddActivity(
 new WorkflowScriptActivity("Initialize variable") {
  Process = "$a = 0",
   PositionX = 0,
   PositionY = 100
 });
```

As atividades são implementadas pelas seguintes classes no namespace `Orchestrator.GraphRunbook.Model`:

|Classe  |Atividade  |
|---------|---------|
|CommandActivity     | Invoca um comando do PowerShell (cmdlet, função, etc.).        |
|InvokeRunbookActivity     | Chama outro runbook embutido.        |
|JunctionActivity     | Espera que todos os ramos recebidos terminem.        |
|WorkflowScriptActivity     | Executa um bloco de código PowerShell ou PowerShell Workflow (dependendo do tipo de runbook) no contexto do runbook. Essa é uma ferramenta poderosa, mas não exagere: a interface do usuário mostrará esse bloco de script como texto; o mecanismo de execução tratará o bloco fornecido como uma caixa preta e não tentará analisar seu conteúdo, exceto por uma verificação básica de sintaxe. Se você precisar apenas chamar um único comando do PowerShell, prefira CommandActivity.        |

> [!NOTE]
> Não derive suas próprias atividades das classes fornecidas: A automação do Azure não poderá usar runbooks com tipos de atividades personalizadas.

Os parâmetros CommandActivity e InvokeRunbookActivity devem ser fornecidos como descritores de valor, não como valores diretos. Os descritores de valor especificam como os valores reais dos parâmetros devem ser produzidos. Os descritores de valor a seguir são fornecidos atualmente:


|Descritor  |Definição  |
|---------|---------|
|ConstantValueDescriptor     | Se refere como um valor constante embutido em código.        |
|RunbookParameterValueDescriptor     | Se refere a um parâmetro de runbook por nome.        |
|ActivityOutputValueDescriptor     | Se refere a uma saída de atividade upstream, permitindo que uma atividade "se inscreva" em dados produzidos por outra atividade.        |
|AutomationVariableValueDescriptor     | Se refere a um ativo variável de automação por nome.         |
|AutomationCredentialValueDescriptor     | Se refere a um ativo de certificado de automação por nome.        |
|AutomationConnectionValueDescriptor     | Se refere a um ativo de Conexão de automação por nome.        |
|PowerShellExpressionValueDescriptor     | Especifica uma expressão do PowerShell de formato livre que será avaliada antes de invocar a atividade.  <br/>Esta é uma ferramenta poderosa, mas não exagere: a interface do usuário mostrará essa expressão como texto; o mecanismo de execução tratará o bloco fornecido como uma caixa preta e não tentará analisar seu conteúdo, exceto por uma verificação básica de sintaxe. Quando possível, prefira descritores de valor mais específicos.      |

> [!NOTE]
> Não derive seus próprios descritores de valor das classes fornecidas: A Automação do Azure não poderá usar runbooks com tipos de descritores de valor personalizado.

Instancie os links que conectam atividades e adicione-os ao runbook:

```csharp
runbook.AddLink(new Link(activityA, activityB, LinkType.Sequence));

runbook.AddLink(
 new Link(activityB, activityC, LinkType.Pipeline) {
  Condition = string.Format("$ActivityOutput['{0}'] -gt 0", activityB.Name)
 });
```

## <a name="save-the-runbook-to-a-file"></a>Salve o runbook em um arquivo

Use `Orchestrator.GraphRunbook.Model.Serialization.RunbookSerializer` para serializar um runbook para uma cadeia de caracteres:

```csharp
var serialized = RunbookSerializer.Serialize(runbook);
```

Essa sequência pode ser salva em um arquivo com a extensão **.graphrunbook** e esse arquivo pode ser importado para a Automação do Azure.
O formato serializado pode mudar nas futuras versões de `Orchestrator.GraphRunbook.Model.dll`. Nós prometemos compatibilidade com versões anteriores: qualquer runbook serializado com uma versão anterior de `Orchestrator.GraphRunbook.Model.dll` pode ser desserializado por qualquer versão mais recente. A compatibilidade com versões futuras não é garantida: um runbook serializado com uma versão mais nova pode não ser deserializável por versões mais antigas.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre Runbooks Gráficos na Automação do Azure, consulte [Introdução à Criação Gráfica](automation-graphical-authoring-intro.md)


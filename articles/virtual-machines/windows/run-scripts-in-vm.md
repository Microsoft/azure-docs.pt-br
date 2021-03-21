---
title: Executar scripts em uma VM Windows do Azure
description: Este tópico descreve como executar scripts dentro de uma máquina virtual Windows
services: automation
ms.service: virtual-machines
ms.collection: windows
author: bobbytreed
ms.author: robreed
ms.date: 05/02/2018
ms.topic: how-to
manager: carmonm
ms.openlocfilehash: ab7d20a83c4cb8cb7a36c4603a5b0b2e69b0662f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560660"
---
# <a name="run-scripts-in-your-windows-vm"></a>Executar scripts na sua VM Windows

Para automatizar tarefas ou solucionar problemas, talvez seja necessário executar comandos em uma VM. O artigo a seguir oferece uma breve visão geral dos recursos disponíveis para executar scripts e comandos dentro de suas VMs.

## <a name="custom-script-extension"></a>Extensão de script personalizado

A [extensão de Script personalizada](../extensions/custom-script-windows.md) é usada principalmente para configuração pós-implantação e instalação de software.

* Baixe e execute scripts em máquinas virtuais do Azure.
* Pode ser executada usando modelos do Azure Resource Manager, CLI do Azure, API REST, PowerShell ou Portal do Azure.
* Os arquivos de script podem ser baixados do armazenamento do Azure ou do GitHub ou fornecidos do seu PC quando são executados no Portal do Azure.
* Execute o script do PowerShell em computadores Windows e o script Bash em computadores Linux.
* Útil para configuração pós-implantação, instalação de software e outras tarefas de configuração ou de gerenciamento.

## <a name="run-command"></a>Executar comando

O recurso [Executar Comando](run-command.md) permite o gerenciamento de máquinas virtuais e de aplicativos, a solução de problemas usando scripts e está disponível mesmo quando o computador não pode ser acessado, por exemplo, se o firewall convidado não tem a porta RDP ou SSH aberta.

* Execute scripts em máquinas virtuais do Azure.
* Podem ser executados usando o [Portal do Azure](run-command.md), a [API REST](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), a [CLI do Azure](/cli/azure/vm/run-command#az-vm-run-command-invoke) ou o [PowerShell](/powershell/module/az.compute/invoke-azvmruncommand)
* Execute rapidamente um script e exiba a saída e repetição conforme necessário no Portal do Azure.
* O script pode ser digitado diretamente ou você pode executar um dos scripts internos.
* Execute o script do PowerShell em computadores Windows e o script Bash em computadores Linux.
* Útil para gerenciamento de máquinas virtuais e de aplicativos e para executar scripts em máquinas virtuais que estão inacessíveis.

## <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker

O [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) fornece gerenciamento geral de computadores, de aplicativos e de ambiente com scripts personalizados do usuário armazenados em uma conta de automação.

* Execute scripts em computadores do Azure e não Azure.
* Podem ser executados usando o Portal do Azure, a CLI do Azure, a API REST, o PowerShell e o webhook.
* Scripts armazenados e gerenciados em uma Conta de automação.
* Executar runbooks do PowerShell, do Fluxo de trabalho do PowerShell, do Python ou gráficos
* Nenhum limite de tempo no tempo de execução do script.
* Vários scripts podem ser executados simultaneamente.
* A saída de script completa é retornada e armazenada.
* Histórico de trabalhos disponível por 90 dias.
* Scripts podem ser executados como sistema local ou com credenciais fornecidas pelo usuário.
* Requer [instalação manual](../../automation/automation-windows-hrw-install.md)

## <a name="serial-console"></a>Console serial

O [Console serial](../troubleshooting/serial-console-windows.md) fornece acesso direto a uma VM, semelhante a ter um teclado conectado à VM.

* Execute comandos em máquinas virtuais do Azure.
* Pode ser executado usando um console baseado em texto para o computador no portal do Azure.
* Faça logon no computador com uma conta de usuário local.
* Útil quando o acesso à máquina virtual é necessário, independentemente do estado do sistema operacional ou da rede do computador.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os diferentes recursos disponíveis para executar scripts e comandos dentro de suas VMs.

* [Extensão de script personalizado](../extensions/custom-script-windows.md)
* [Executar Comando](run-command.md)
* [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md)
* [Console serial](../troubleshooting/serial-console-windows.md)

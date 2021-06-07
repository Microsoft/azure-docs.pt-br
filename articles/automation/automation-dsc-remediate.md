---
title: Corrigir servidores do State Configuration da Automação do Azure sem conformidade
description: Este artigo informa como reaplicar as configurações sob demanda nos servidores em que o estado da configuração foi descompassado.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: 98164c20af2a5d6ae7ccac43331a2876c23ccfb7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102500165"
---
# <a name="remediate-noncompliant-azure-automation-state-configuration-servers"></a>Corrigir servidores do State Configuration da Automação do Azure sem conformidade

Quando os servidores são registrados com State Configuration de Automação do Azure, o modo de configuração é definido como `ApplyOnly`, `ApplyandMonitor` ou `ApplyAndAutoCorrect`. Se o modo não estiver definido como `ApplyAndAutoCorrect`, os servidores que ficarem descompassados de um estado compatível por qualquer motivo permanecerão não compatíveis até que sejam corrigidos manualmente.

A computação do Azure oferece um recurso chamado Executar Comando, que permite aos clientes executar scripts dentro de máquinas virtuais.
Este documento fornece scripts de exemplo para esse recurso ao corrigir manualmente a descompasso de configuração.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Corrigir descompasso das máquinas virtuais do Windows usando o PowerShell

Você pode corrigir o descompasso de máquinas virtuais do Windows usando o recurso de comando `Run`. Consulte [Executar scripts do PowerShell em sua VM do Windows com o comando Run](../virtual-machines/windows/run-command.md).

Para forçar um nó de State Configuration da Automação do Azure a baixar a configuração mais recente e aplicá-la, use o cmdlet [Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration).

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Corrigir descompasso das máquinas virtuais do Linux

Para máquinas virtuais do Linux, você não tem a opção de usar o comando `Run`. Você só pode corrigir o descompasso desses computadores repetindo o processo de registro. 

Para nós do Azure, você pode corrigir o descompasso do portal do Azure ou usando cmdlets de módulo Az. Os detalhes sobre esse processo estão documentados em [Habilitar uma VM usando o portal do Azure](automation-dsc-onboarding.md#enable-a-vm-using-azure-portal).

Para nós híbridos, você pode corrigir o descompasso usando os scripts do Python. Consulte [Executar operações de DSC no computador Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Próximas etapas

- Para obter uma referência de cmdlet do PowerShell, confira [Az.Automation](/powershell/module/az.automation/#automation).
- Para ver um exemplo de uso do serviço State Configuration da Automação do Azure em um pipeline de implantação contínua, veja [Configuração de implantação contínua com o Chocolatey](automation-dsc-cd-chocolatey.md).

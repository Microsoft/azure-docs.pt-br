---
title: Como atualizar o agente de dependência do Revisions da VM
description: Este artigo descreve como atualizar o agente de dependência do VM insights usando a linha de comando, o assistente de instalação e outros métodos.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/16/2020
ms.openlocfilehash: acd1b6c8e5c1be76b29c93fddc57c799aef0a526
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102046696"
---
# <a name="how-to-upgrade-the-vm-insights-dependency-agent"></a>Como atualizar o agente de dependência do Revisions da VM

Após a implantação inicial do agente de dependência do insights de VM, são lançadas atualizações que incluem correções de bugs ou suporte a novos recursos ou funcionalidades.  Este artigo ajuda você a entender os métodos disponíveis e como executar a atualização manualmente ou por meio da automação.

## <a name="upgrade-options"></a>Opções de atualização 

O Dependency Agent para Windows e Linux pode ser atualizado para a versão mais recente manualmente ou automaticamente, dependendo do cenário de implantação e do ambiente no qual o computador está sendo executado. Os métodos a seguir podem ser usados para atualizar o agente.

|Ambiente |Método de instalação |Método de atualização |
|------------|--------------------|---------------|
|VM do Azure | Extensão de VM do agente de dependência para [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) e [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) | O Agent é atualizado automaticamente por padrão, a menos que você tenha configurado seu modelo de Azure Resource Manager para recusar definindo a propriedade *autoUpgradeMinorVersion* como **false**. A atualização para a versão secundária em que a atualização automática está desabilitada e uma atualização de versão principal segue o mesmo método – desinstale e reinstale a extensão. |
| Imagens personalizadas de VM do Azure | Instalação manual do agente de dependência para Windows/Linux | A atualização de VMs para a versão mais recente do agente precisa ser executada na linha de comando que executa o pacote do Windows Installer ou o grupo de script de shell instalável e de extração automática do Linux.|
| VMs não Azure | Instalação manual do agente de dependência para Windows/Linux | A atualização de VMs para a versão mais recente do agente precisa ser executada na linha de comando que executa o pacote do Windows Installer ou o grupo de script de shell instalável e de extração automática do Linux. |

## <a name="upgrade-windows-agent"></a>Atualizar o agente do Windows 

Para atualizar o agente em uma VM do Windows para a versão mais recente não instalada usando a extensão de VM do agente de dependência, você pode executar no prompt de comando, script ou outra solução de automação ou usando o assistente de instalação InstallDependencyAgent-Windows.exe.  

Você pode baixar a versão mais recente do agente do Windows [aqui](https://aka.ms/dependencyagentwindows).

### <a name="using-the-setup-wizard"></a>Usando o assistente de instalação

1. Faça logon no computador com uma conta que tenha direitos administrativos.

2. Execute **InstallDependencyAgent-Windows.exe** para iniciar o assistente de instalação.
   
3. Siga o assistente de **instalação do Dependency Agent** para desinstalar a versão anterior do agente de dependência e, em seguida, instale a versão mais recente.


### <a name="from-the-command-line"></a>Da linha de comando

1. Faça logon no computador com uma conta que tenha direitos administrativos.

2. Execute o comando a seguir.

    ```dos
    InstallDependencyAgent-Windows.exe /S /RebootMode=manual
    ```

    O `/RebootMode=manual` parâmetro impede que a atualização reinicie automaticamente o computador se alguns processos estiverem usando arquivos da versão anterior e tiverem um bloqueio neles. 

3. Para confirmar se a atualização foi bem-sucedida, verifique as `install.log` informações de configuração detalhadas. O diretório de log é *%Programfiles%\Microsoft Dependency Agent\logs*.

## <a name="upgrade-linux-agent"></a>Atualizar agente do Linux 

A atualização de versões anteriores do Dependency Agent no Linux é suportada e executada seguindo o mesmo comando que uma nova instalação.

Você pode baixar a versão mais recente do agente do Linux [aqui](https://aka.ms/dependencyagentlinux).

1. Faça logon no computador com uma conta que tenha direitos administrativos.

2. Execute o comando a seguir como raiz `sh InstallDependencyAgent-Linux64.bin -s` . 

Se o Agente de Dependência não for iniciado, verifique os logs para obter informações de erro detalhadas. Em agentes do Linux, o diretório de log é */var/opt/Microsoft/Dependency-Agent/log*. 

## <a name="next-steps"></a>Próximas etapas

Se você quiser interromper o monitoramento de suas VMs por um período de tempo ou remover totalmente as informações de VM, consulte [desabilitar o monitoramento de suas VMs no insights de VM](../vm/vminsights-optout.md).

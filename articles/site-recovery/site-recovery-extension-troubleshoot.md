---
title: Solucionar problemas com agentes do Azure Site Recovery | Microsoft Docs’
description: Fornece informações sobre os sintomas, causas e resoluções de falhas do agente do Azure Site Recovery.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: 5ea701682c03370cea46f9126ecf78427a776371
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61280664"
---
# <a name="troubleshoot-issues-with-the-azure-site-recovery-agent"></a>Solucionar problemas com o agente de recuperação de Site do Azure

Este artigo fornece etapas de solução de problema que podem ajudar você a resolver erros do Azure Site Recovery relacionados ao agente e extensão VM.


## <a name="azure-site-recovery-extension-time-out"></a>Tempo limite atingido do Azure Site Recovery  

Mensagem de erro: "A execução da tarefa atingiu o tempo limite durante o rastreamento para a operação de extensão seja iniciada"<br>
Código de erro: "151076"

 O Azure Site Recovery instala uma extensão na máquina virtual como parte do trabalho de habilitação de proteção. Qualquer uma das condições a seguir pode impedir a proteção de ser disparo e o trabalho falhar. Conclua as seguintes etapas de solução de problemas e, depois, repita a operação:

**Causa 1: [O agente está instalado na VM, mas sem resposta (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**Causa 2: [O agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Causa 3: [A extensão de Site Recovery falha ao ser atualizada ou carregada](#the-site-recovery-extension-fails-to-update-or-load)**  

Mensagem de erro: "A operação de extensão de recuperação do site anterior está demorando mais que o esperado."<br>
Código de erro: "150066"<br>

**Causa 1: [O agente está instalado na VM, mas sem resposta (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**Causa 2: [O agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Causa 3: [O status da extensão do Site Recovery está incorreto](#the-site-recovery-extension-fails-to-update-or-load)**  

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>Falha na proteção porque o agente de VM está sem resposta

Mensagem de erro: "A execução da tarefa atingiu o tempo limite durante o rastreamento para a operação de extensão seja iniciada."<br>
Código de erro: "151099"<br>

Esse erro pode ocorrer se o agente convidado do Microsoft Azure na máquina virtual não está no estado pronto.
Você pode verificar o status do agente convidado do Microsoft Azure no [portal do Azure](https://portal.azure.com/). Vá para a máquina virtual que você está tentando proteger e verifique o status "VM > Configurações > Propriedades > Status do agente". Na maioria das vezes o status do agente ficam prontos após a reinicialização da máquina virtual. No entanto, se a reinicialização não for uma opção possível ou que ainda está enfrentando o problema, conclua as seguintes etapas de solução de problemas.

**Causa 1: [O agente está instalado na VM, mas sem resposta (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**Causa 2: [O agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  


Mensagem de erro: "A execução da tarefa atingiu o tempo limite durante o rastreamento para a operação de extensão seja iniciada."<br>
Código de erro: "151095"<br>

Isso ocorrer quando a versão do agente no computador Linux é antiga. Conclua as etapas de solução de problemas a seguir.<br>
  **Causa 1: [O agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
## <a name="causes-and-solutions"></a>Causas e soluções

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>O agente está instalado na VM, mas sem resposta (para VMs do Windows)

#### <a name="solution"></a>Solução
O agente de VM pode ter sido corrompido ou o serviço pode ter sido interrompido. A reinstalação do agente de VM permite obter a versão mais recente. Ela também ajuda a reiniciar a comunicação com o serviço.

1. Determine se o “Serviço de Agente Convidado Microsoft Azure do Windows” está funcionando nos serviços de VM (services.msc). Tente reiniciar o "serviço do Agente Convidado Microsoft Azure do Windows”.    
2. Se o serviço Agente Convidado Microsoft Azure do Windows não estiver visível nos serviços, no Painel de Controle, acesse **Programas e Recursos** para determinar se o serviço Agente Convidado do Windows está instalado.
4. Se o Agente Convidado Microsoft Azure do Windows aparecer em **Programas e Recursos**, desinstale o Agente Convidado do Windows.
5. Baixe e instale a [versão mais recente do MSI do agente](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Você precisa ter direitos de Administrador para concluir a instalação.
6. Verifique se os serviços do Agente Convidado Microsoft Azure do Windows aparecem nos serviços.
7. Reinicie o trabalho de proteção.

Verifique também se o [Microsoft .NET 4.5 está instalado](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) na VM. O .NET 4.5 é necessário para que o agente de VM se comunique com o serviço.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>O agente instalado na VM está desatualizado (para VMs Linux)

#### <a name="solution"></a>Solução
A maioria das falhas relacionadas ao agente ou relacionadas à extensão para VMs do Linux é causada por problemas que afetam um agente de VM desatualizado. Para solucionar esse problema, siga estas diretrizes gerais:

1. Siga as instruções para [atualizar o agente de VM do Linux ](../virtual-machines/linux/update-agent.md).

   > [!NOTE]
   > Estamos *altamente recomendável* que você atualizar o agente apenas por meio de um repositório de distribuição. Não é recomendável baixar o código do agente diretamente do GitHub e atualizá-lo. Se o agente mais recente para a sua distribuição não está disponível, entre em contato com o suporte da distribuição para obter instruções de como instalá-lo. Para verificar o agente mais recente, vá para a página [agente Linux do Windows Azure](https://github.com/Azure/WALinuxAgent/releases) no repositório do GitHub.

2. Verifique se o agente do Azure está em execução na VM, executando o seguinte comando: `ps -e`

   Se o processo não estiver em execução, reinicie-o usando os seguintes comandos:

   * Para o Ubuntu: `service walinuxagent start`
   * Para outras distribuições: `service waagent start`

3. [Configure o agente de reinicialização automática](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Habilitar a proteção para as máquina virtuais.



### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>A extensão de Site Recovery falha ao ser atualizada ou carregada
Se o status das extensões for "Vazio”, “Não pronto” ou em transição.

#### <a name="solution"></a>Solução

Desinstale a extensão e reinicie a operação novamente.

Para desinstalar a extensão:

1. No [Portal do Azure](https://portal.azure.com/), acesse a VM que está apresentando falha de backup.
2. Escolha a opção **Configurações**.
3. Selecione **Extensões**.
4. Selecione **Extensão Site Recovery**.
5. Selecione **Desinstalar**.

Para VM Linux, se a extensão VMSnapshot não for mostrada no Portal do Azure, [atualize o Agente Linux do Azure](../virtual-machines/linux/update-agent.md) e, em seguida, execute a proteção. 

A realização dessas etapas faz com que a extensão seja reinstalada durante a próxima proteção.



---
title: Solucionar problemas da extensão de VM do Azure para recuperação de desastre com Azure Site Recovery
description: Solucionar problemas com a extensão de VM do Azure para recuperação de desastre com Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.openlocfilehash: c1915d108bf9465d3e5b8d6a55053b583ee4f580
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009697"
---
# <a name="troubleshoot-azure-vm-extension-issues"></a>Solucionar problemas de extensão de VM do Azure

Este artigo fornece etapas de solução de problemas que podem ajudá-lo a resolver erros de Azure Site Recovery relacionados ao agente de VM e à extensão.

## <a name="low-system-resources"></a>Recursos do sistema insuficientes

Esse problema ocorre quando o sistema tem pouca memória disponível e não é capaz de alocar memória para a instalação do serviço de mobilidade. Certifique-se de que memória suficiente tenha sido liberada para que a instalação prossiga e seja concluída com êxito.

## <a name="azure-site-recovery-extension-time-out"></a>Tempo limite da extensão de Azure Site Recovery  

Mensagem de erro: "o tempo limite de execução da tarefa foi atingido ao passo que o acompanhamento da operação da extensão iniciou”<br>
Código do erro: "151076"

 Azure Site Recovery instalou uma extensão na máquina virtual como parte de um trabalho de habilitação de proteção. Qualquer uma das condições a seguir pode impedir que a proteção seja disparada e faça com que o trabalho falhe. Conclua as seguintes etapas de solução de problemas e, depois, repita a operação:

- [O agente é instalado na VM, mas não responde (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [O agente instalado na VM está desatualizado (para VMs Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [A extensão de Site Recovery falha ao ser atualizada ou carregada](#the-site-recovery-extension-fails-to-update-or-load)

Mensagem de erro: "a operação de extensão de Site Recovery anterior está levando mais tempo do que o esperado".<br>
Código de erro: "150066"

- [O agente é instalado na VM, mas não responde (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [O agente instalado na VM está desatualizado (para VMs Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [O status da extensão do Site Recovery está incorreto](#the-site-recovery-extension-fails-to-update-or-load)

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>Falha na proteção porque o agente de VM está sem resposta

Mensagem de erro: "o tempo limite de execução da tarefa foi atingido ao passo que o acompanhamento da operação da extensão iniciou”.<br>
Código de erro: "151099"

Esse erro pode ocorrer se o agente convidado do Azure na máquina virtual não estiver no estado pronto.

Você pode verificar o status do agente convidado do Azure na [portal do Azure](https://portal.azure.com/). Vá para a máquina virtual que você está tentando proteger e verifique o status em configurações da **VM**  >  **Settings**  >  **Properties**  >  **status do agente** de propriedades. Na maioria das vezes, o status do agente estará pronto após a reinicialização da máquina virtual. No entanto, se você não puder reinicializar ou ainda estiver enfrentando o problema, conclua as seguintes etapas de solução de problemas:

- [O agente é instalado na VM, mas não responde (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [O agente instalado na VM está desatualizado (para VMs Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)


Mensagem de erro: "o tempo limite de execução da tarefa foi atingido ao passo que o acompanhamento da operação da extensão iniciou”.<br>
Código de erro: "151095"

Esse erro ocorre quando a versão do agente no computador Linux está desatualizada. Conclua a seguinte etapa de solução de problemas:

- [O agente instalado na VM está desatualizado (para VMs Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)  

## <a name="causes-and-solutions"></a>Causas e soluções

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>O agente é instalado na VM, mas não responde (para VMs do Windows)

#### <a name="solution"></a>Solução
O agente de VM pode ter sido corrompido ou o serviço pode ter sido interrompido. A reinstalação do agente de VM permite obter a versão mais recente. Ela também ajuda a reiniciar a comunicação com o serviço.

1. Determine se o serviço de agente convidado do Microsoft Azure está executando nos serviços de VM (services.msc). Reinicie o serviço do agente convidado do Windows Azure.    
1. Se o serviço do agente convidado do Windows Azure não estiver visível em serviços, abra o painel de controle. Acesse **programas e recursos** para ver se o serviço do agente convidado do Windows está instalado.
1. Se o serviço de agente convidado do Microsoft Azures aparecer em **Programas e Recursos**, desinstale o serviço de agente convidado do Microsoft Azure.
1. Baixe e instale a [versão mais recente do MSI do agente](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Você precisa de direitos de administrador para concluir a instalação.
1. Verifique se o serviço do agente convidado do Windows Azure aparece em serviços.
1. Reinicie o trabalho de proteção.

Verifique também se o [Microsoft .NET 4.5 está instalado](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) na VM. Você precisa do .NET 4,5 para que o agente de VM se comunique com o serviço.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>O agente instalado na VM está desatualizado (para VMs Linux)

#### <a name="solution"></a>Solução
A maioria das falhas relacionadas ao agente ou relacionadas à extensão para VMs do Linux é causada por problemas que afetam um agente de VM desatualizado. Para solucionar esse problema, siga estas diretrizes gerais:

1. Siga as instruções para [atualizar o agente de VM do Linux ](../virtual-machines/extensions/update-linux-agent.md).

   > [!NOTE]
   > Estamos *altamente recomendável* que você atualizar o agente apenas por meio de um repositório de distribuição. Não é recomendável baixar o código do agente diretamente do GitHub e atualizá-lo. Se o agente mais recente para sua distribuição não estiver disponível, entre em contato com o suporte de distribuição para obter instruções sobre como instalá-lo. Para verificar o agente mais recente, vá para a página [agente Linux do Windows Azure](https://github.com/Azure/WALinuxAgent/releases) no repositório do GitHub.

1. Verifique se o agente do Azure está em execução na VM, executando o seguinte comando: `ps -e`

   Se o processo não estiver em execução, reinicie-o usando os seguintes comandos:

   - Para o Ubuntu: `service walinuxagent start`
   - Para outras distribuições: `service waagent start`

1. [Configure o agente de reinicialização automática](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
1. Habilitar a proteção para as máquina virtuais.

### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>A extensão de Site Recovery falha ao ser atualizada ou carregada

O status da extensão é mostrado como "vazio", "não legível" ou "em transição".

#### <a name="solution"></a>Solução

Desinstale a extensão e reinicie a operação novamente.

Para desinstalar a extensão:

1. Na [portal do Azure](https://portal.azure.com/), vá para a VM que está apresentando falha de backup.
1. Selecione **Configurações**.
1. Selecione **Extensões**.
1. Selecione **Extensão Site Recovery**.
1. Selecione **Desinstalar**.

Para a VM do Linux, se a extensão VMSnapshot não for mostrada no portal do Azure, [atualize o agente Linux do Azure](../virtual-machines/extensions/update-linux-agent.md). Em seguida, execute a proteção.

Quando você concluir essas etapas, isso fará com que a extensão seja reinstalada durante a proteção.

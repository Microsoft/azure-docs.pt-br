---
title: Introdução ao agente de área de trabalho virtual do Windows
description: Uma visão geral do agente de área de trabalho virtual do Windows e dos processos de atualização.
author: Sefriend
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: sefriend
manager: clarkn
ms.openlocfilehash: 325502255e84e38a39ca5b90ee4126354c0d425b
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98601231"
---
# <a name="get-started-with-the-windows-virtual-desktop-agent"></a>Introdução ao agente de área de trabalho virtual do Windows

Na estrutura de serviços de área de trabalho virtual do Windows, há três componentes principais: o Área de Trabalho Remota cliente, o serviço e as máquinas virtuais. Essas máquinas virtuais residem na assinatura do cliente em que o agente de área de trabalho virtual do Windows e o carregador de ativação do agente estão instalados. O agente atua como o Communicator intermediário entre o serviço e as máquinas virtuais, permitindo a conectividade. Portanto, se você estiver enfrentando problemas com a instalação, atualização ou configuração do agente, suas máquinas virtuais não poderão se conectar ao serviço. O carregador de carga do agente é o executável que carrega o agente. 

Este artigo fornecerá uma breve visão geral dos processos de instalação e atualização do agente.

>[!NOTE]
>Esta documentação não é para o agente do FSLogix ou o agente cliente do Área de Trabalho Remota.


## <a name="initial-installation-process"></a>Processo de instalação inicial

Inicialmente, o agente de área de trabalho virtual do Windows é instalado de duas maneiras. Se você provisionar máquinas virtuais (VMs) no portal do Azure e no Azure Marketplace, o agente e o carregador de vida do agente serão instalados automaticamente. Se você provisionar VMs usando o PowerShell, deverá baixar manualmente os arquivos. msi do agente e do agente do carregador de [janelas ao criar um pool de hosts da área de trabalho virtual do Windows com o PowerShell](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool). Quando o agente é instalado, a pilha lado a lado da área de trabalho virtual do Windows e o agente de monitoramento Geneva também são instalados simultaneamente. O componente de pilha lado a lado é necessário para que os usuários estabeleçam com segurança conexões inversas de servidor para cliente. O agente de monitoramento Geneva monitora a integridade do agente. Todos esses três componentes são essenciais para que a conectividade do usuário de ponta a ponta funcione corretamente.

>[!IMPORTANT]
>Para instalar com êxito o agente de área de trabalho virtual do Windows, pilha lado a lado e agente de monitoramento Geneva, você deve desbloquear todas as URLs listadas na [lista de URL necessária](safe-url-list.md#virtual-machines). É necessário desbloquear essas URLs para usar o serviço de área de trabalho virtual do Windows.

## <a name="agent-update-process"></a>Processo de atualização do agente

O serviço de área de trabalho virtual do Windows atualiza automaticamente o agente sempre que uma atualização é disponibilizada. As atualizações de agente podem incluir nova funcionalidade ou corrigir problemas anteriores. Depois que a versão inicial do agente de área de trabalho virtual do Windows é instalada, o agente consulta regularmente o serviço de área de trabalho virtual do Windows para determinar se há uma versão mais recente do agente e de seus componentes disponíveis. Se houver uma nova versão, o carregador de borda do agente baixará automaticamente a versão mais recente do agente, a pilha lado a lado e o agente de monitoramento Geneva.

>[!NOTE]
>- Quando o agente de monitoramento Geneva atualiza para a versão mais recente, a tarefa antiga do GenevaTask está localizada e desabilitada antes de criar uma nova tarefa para o novo agente de monitoramento. A versão anterior do agente de monitoramento não é excluída caso a versão mais recente do agente de monitoramento tenha um problema que exija a reversão para a versão anterior para correção. Se a versão mais recente tiver um problema, o agente de monitoramento antigo será habilitado novamente para continuar a fornecer dados de monitoramento. Todas as versões do monitor anteriores ao último que você instalou antes da atualização serão excluídas da VM.
>- Sua VM mantém três versões da pilha lado a lado por vez. Isso permitirá a recuperação rápida se algo der errado com a atualização. A versão mais antiga da pilha é removida da VM sempre que a pilha é atualizada.

Essa instalação de atualização normalmente dura de 2-3 minutos em uma nova VM e não deve fazer com que sua VM perca a conexão ou desligada. Esse processo de atualização se aplica à área de trabalho virtual do Windows (clássico) e à versão mais recente da área de trabalho virtual do Windows com Azure Resource Manager.

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma melhor compreensão do agente de área de trabalho virtual do Windows, aqui estão alguns recursos que podem ajudá-lo a:

- Se você estiver enfrentando problemas relacionados ao agente ou à conectividade, confira o guia de solução de problemas do [agente de área de trabalho virtual do Windows](troubleshoot-agent.md).
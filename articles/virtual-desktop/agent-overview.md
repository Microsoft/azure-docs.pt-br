---
title: Introdução ao Agente da Área de Trabalho Virtual do Windows
description: Uma visão geral do Agente da Área de Trabalho Virtual do Windows e dos processos de atualização.
author: Sefriend
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: sefriend
manager: clarkn
ms.openlocfilehash: 371cc78f3ebad638008f4195f164b66a64948c65
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504542"
---
# <a name="get-started-with-the-windows-virtual-desktop-agent"></a>Introdução ao Agente da Área de Trabalho Virtual do Windows

Na estrutura de Serviços da Área de Trabalho Virtual do Windows, há três componentes principais: o cliente da Área de Trabalho Remota, o serviço e as máquinas virtuais. Essas máquinas virtuais residem na assinatura do cliente em que o agente de Área de Trabalho Virtual do Windows e o carregador de inicialização do agente estão instalados. O agente funciona como o comunicador intermediário entre o serviço e as máquinas virtuais, permitindo a conectividade. Portanto, se você estiver enfrentando problemas com a instalação, a atualização ou a configuração do agente, suas máquinas virtuais não poderão se conectar ao serviço. O carregador de inicialização do agente é o executável que carrega o agente. 

Este artigo fornecerá uma breve visão geral dos processos de instalação e atualização do agente.

>[!NOTE]
>Esta documentação não serve para o agente do FSLogix ou o agente cliente da Área de Trabalho Remota.


## <a name="initial-installation-process"></a>Processo de instalação inicial

Inicialmente, o agente da Área de Trabalho Virtual do Windows é instalado de duas maneiras. Se você provisionar VMs (máquinas virtuais) no portal do Azure e no Azure Marketplace, o agente e o carregador de inicialização do agente serão instalados automaticamente. Se você provisionar VMs usando o PowerShell, deverá baixar manualmente os arquivos .msi do agente e do agente do carregador de inicialização [criando um pool de host da Área de Trabalho Virtual do Windows com o PowerShell](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool). Depois que o agente é instalado, ele instala a pilha lado a lado da Área de Trabalho Virtual do Windows e o agente do Geneva Monitoring. O componente de pilha lado a lado é necessário para que os usuários estabeleçam conexões inversas de servidor para cliente com segurança. O agente do Geneva Monitoring monitora a integridade do agente. Todos esses três componentes são essenciais para que a conectividade do usuário funcione corretamente, de ponta a ponta.

>[!IMPORTANT]
>Para instalar com êxito o agente da Área de Trabalho Virtual do Windows, a pilha lado a lado e agente do Geneva Monitoring, você deve desbloquear todas as URLs listadas na [lista de URLs necessárias](safe-url-list.md#virtual-machines). É necessário desbloquear essas URLs para usar o serviço da Área de Trabalho Virtual do Windows.

## <a name="agent-update-process"></a>Processo de atualização do agente

O serviço da Área de Trabalho Virtual do Windows atualiza o agente sempre que uma atualização é disponibilizada. As atualizações de agente podem incluir novas funcionalidades ou correções para problemas anteriores. Depois que a versão inicial do agente da Área de Trabalho Virtual do Windows estiver instalada, o agente consultará regularmente o serviço da Área de Trabalho Virtual do Windows para determinar se há uma versão mais recente do agente, da pilha ou do componente de monitoramento disponível. Se uma versão mais recente de qualquer um dos componentes já tiver sido implantada, o componente atualizado será instalado automaticamente.

As novas versões do agente são implantadas em intervalos regulares em períodos semanais para todas as assinaturas do Azure. Esses períodos de atualização são chamados de "disponibilizar versão piloto". Quando ocorrer uma disponibilização de versão piloto, você verá que as VMs em seu pool de host receberão a atualização do agente em momentos diferentes. Todos os agentes de VM em todas as assinaturas serão atualizados no final do período de implantação. O sistema de disponibilização de versão piloto da Área de Trabalho Virtual do Windows aprimora a confiabilidade do serviço, garantindo a estabilidade e a qualidade da atualização do agente.


>[!NOTE]
>Como as VMs do seu pool de host podem receber atualizações do agente em momentos diferentes, você precisará ser capaz de distinguir os problemas de disponibilização de versão piloto e as atualizações do agente com falha. Caso você acesse os logs de eventos de sua VM em **Visualizador de Eventos** > **Logs do Windows** > **Aplicativo** e observe um evento com o rótulo "ID 3277", isso significa que a atualização do agente não funcionou. Se você não vir esse evento, a VM estará em uma disponibilização de versão piloto diferente e será atualizada posteriormente.
>- Quando o agente do Geneva Monitoring for atualizado para a versão mais recente, a tarefa antiga GenevaTask será localizada e desabilitada antes da criação de uma nova tarefa para o novo agente de monitoramento. A versão mais recente do agente de monitoramento não será excluída caso a versão mais recente do agente de monitoramento tenha um problema que exija a reversão para a versão anterior para correção. Se a versão mais recente tiver um problema, o agente de monitoramento antigo será habilitado novamente para continuar a fornecer dados de monitoramento. Todas as versões do monitor anteriores à última que você instalou antes da atualização serão excluídas da VM.
>- Sua VM mantém três versões da pilha lado a lado ao mesmo tempo. Isso permitirá a recuperação rápida se algo der errado com a atualização. A versão mais antiga da pilha é removida da VM sempre que a pilha é atualizada.

Essa instalação de atualização normalmente dura de 2 a 3 minutos em uma nova VM e não deve fazer com que sua VM perca a conexão ou seja desligada. Esse processo de atualização se aplica à Área de Trabalho Virtual do Windows (clássica) e à versão mais recente da Área de Trabalho Virtual do Windows com o Azure Resource Manager.

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma compreensão melhor do agente da Área de Trabalho Virtual do Windows, confira alguns recursos que podem ajudá-lo:

- Se você estiver enfrentando problemas relacionados ao agente ou à conectividade, confira o [Guia de solução de problemas do Agente da Área de Trabalho Virtual do Windows](troubleshoot-agent.md).

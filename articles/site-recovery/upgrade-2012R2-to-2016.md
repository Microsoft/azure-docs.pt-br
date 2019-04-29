---
title: Atualizar hosts do Windows Server 2012 R2 e o SCVMM configurados com o Azure Site Recovery para o Windows Server 2016
description: Saiba como configurar a recuperação de desastre para VMs do Azure Stack com o serviço do Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.topic: conceptual
ms.service: site-recovery
ms.date: 12/03/2018
ms.author: rajanaki
ms.openlocfilehash: b67290f72f762331a6d699fb79aef0c0d7f9fb65
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61275516"
---
# <a name="upgrade-windows-server-2012-r2-hosts-scvmm-2012-r2-configured-with-azure-site-recovery-to-windows-server-2016--scvmm-2016"></a>Atualizar hosts do Windows Server 2012 R2 e o SCVMM 2012 R2 configurados com o Azure Site Recovery para o Windows Server 2016 e o SCVMM 2016

Este artigo mostra como atualizar hosts do Windows Server 2012 R2 e o SCVMM 2012 R2 que são configurados com o Azure Site Recovery para o Windows Server 2016 e o SCVMM 2016

O Azure Site Recovery contribui para a estratégia de BCDR (continuidade dos negócios e recuperação de desastres). O serviço garante que as cargas de trabalho da VM permaneçam disponíveis quando ocorrerem interrupções esperadas e inesperadas.

> [!IMPORTANT]
> Ao atualizar os hosts do Windows Server 2012 R2 que já estão configurados para replicação com o Azure Site Recovery, você precisará seguir as etapas mencionadas neste documento. Qualquer caminho alternativo escolhido para a atualização poderá resultar em estados sem suporte e em uma interrupção na replicação ou na capacidade de execução do failover.


Neste artigo, você aprenderá a atualizar as seguintes configurações em seu ambiente:

> [!div class="checklist"]
> * **Hosts do Windows Server 2012 R2 que não são gerenciados pelo SCVMM** 
> * **Hosts do Windows Server 2012 R2 que são gerenciados por um servidor do SCVMM 2012 R2 independente** 
> * **Hosts do Windows Server 2012 R2 que são gerenciados por um servidor do SCVMM 2012 R2 altamente disponível**


## <a name="prerequisites--factors-to-consider"></a>Pré-requisitos e fatores a serem considerados

Antes da atualização, observe o seguinte:

- Se você tiver hosts do Windows Server 2012 R2 que não são gerenciados pelo SCVMM e essa for uma configuração de ambiente independente, haverá uma interrupção na replicação, caso você tente executar a atualização.
- Se, a princípio, você tiver selecionado "*Não armazenar minhas Chaves no Active Directory no Gerenciamento de Chaves Distribuídas*" durante a instalação do SCVMM 2012 R2, as atualizações não serão concluídas com êxito.

- Se você estiver usando o System Center 2012 R2 VMM, 

    - Verifique as informações de banco de dados no VMM: **Console do VMM** -> **Configurações** -> **Geral** -> **Conexão de banco de dados**
    - Verificar as contas de serviço que estão sendo usadas para o serviço de agente do System Center Virtual Machine Manager
    - Verifique se você tem um backup do Banco de Dados do VMM.
    - Anote o nome do banco de dados dos servidores do SCVMM envolvidos. Isso pode ser feito navegando para **Console do VMM** -> **Configurações** -> **Geral** -> **Conexão de banco de dados**
    - Anote a ID do VMM dos servidores do VMM 2012 R2 primário e de recuperação. A ID do VMM pode ser encontrada no Registro "HKLM:\SOFTWARE\Microsoft\Microsoft System Center Virtual Machine Manager Server\Setup”.
    - Verifique se os novos SCVMMs que você adicionar ao cluster tem os mesmos nomes de antes. 

- Se estiver fazendo a replicação entre dois dos sites gerenciados pelos SCVMMs em ambos os lados, atualize o lado de recuperação primeiro antes de atualizar o lado primário.
  > [!WARNING]
  > Ao atualizar o SCVMM 2012 R2, em Gerenciamento de Chaves Distribuídas, selecione **Armazenar chaves de criptografia no Active Directory**. Escolha as configurações para a conta de serviço e o gerenciamento de chaves distribuídas com cuidado. De acordo com sua seleção, os dados criptografados, como senhas em modelos, poderão não estar disponíveis após a atualização e você poderá potencialmente afetar a replicação com o Azure Site Recovery

> [!IMPORTANT]
> Consulte a documentação detalhada do SCVMM sobre [pré-requisitos](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#requirements-and-limitations)

## <a name="windows-server-2012-r2-hosts-which-arent-managed-by-scvmm"></a>Hosts do Windows Server 2012 R2 que não são gerenciados pelo SCVMM 
A lista de etapas mencionadas abaixo aplica-se à configuração de usuário dos [hosts do Hyper-V para o Azure](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-architecture) executada seguindo este [tutorial](https://docs.microsoft.com/azure/site-recovery/hyper-v-prepare-on-premises-tutorial)

> [!WARNING]
> Conforme mencionado nos pré-requisitos, essas etapas se aplicam somente a um cenário de ambiente clusterizado e não a uma configuração de host do Hyper-V independente.

1. Siga as etapas para executar a [atualização de cluster sem interrupção.](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) para executar o processo de atualização de cluster sem interrupção.
2. A cada novo host do Windows Server 2016 introduzido no cluster, remova a referência de um host do Windows Server 2012 R2 do Azure Site Recovery seguindo as etapas mencionadas [aqui]. Esse deverá ser o host que você escolheu esvaziar e remover do cluster.
3. Após a execução do comando *Update-VMVersion* em todas as máquinas virtuais, as atualizações serão concluídas. 
4. Use as etapas mencionadas [aqui](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-tutorial#set-up-the-source-environment) para registrar o novo host do Windows Server 2016 no Azure Site Recovery. Observe que o site do Hyper-V já está ativo e você só precisa registrar o novo host no cluster. 
5.  Acesse o portal do Azure e verifique o status da integridade replicada nos Serviços de Recuperação

## <a name="upgrade-windows-server-2012-r2-hosts-managed-by-stand-alone-scvmm-2012-r2-server"></a>Atualizar hosts do Windows Server 2012 R2 gerenciados por um servidor do SCVMM 2012 R2 independente
Antes de atualizar os hosts do Windows Server 2012 R2, você precisa atualizar o SCVMM 2012 R2 para o SCVMM 2016. Siga as etapas abaixo:

**Atualizar o SCVMM 2012 R2 independente para o SCVMM 2016**

1.  Desinstale o provedor do ASR navegando para o Painel de Controle -> Programas -> Programas e Recursos -> Microsoft Azure Site Recovery e clique em Desinstalar
2. [Manter o banco de dados do SCVMM e atualizar o sistema operacional](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#back-up-and-upgrade-the-operating-system)
3. Em **Adicionar ou remover programas**, selecione **VMM** > **Desinstalar**. b. Selecione **Remover Recursos** e, em seguida, selecione **Servidor de gerenciamento do VMM e Console do VMM**. c. Em **Opções de Banco de Dados**, selecione **Manter banco de dados**. d. Examine o resumo e clique em **Desinstalar**.

4. [Instalar o VMM 2016](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#install-vmm-2016)
5. Inicie o SCVMM e verifique o status de cada host na guia **Malhas**. Clique em **Atualizar** para obter o status mais recente. Você deverá ver o status como “Precisa de Atenção”. 
17. Instale o último [Provedor do Microsoft Azure Site Recovery](https://aka.ms/downloaddra) no SCVMM.
16. Instale o último [agente do MARS (Serviço de Recuperação do Microsoft Azure)](https://aka.ms/latestmarsagent) em cada host do cluster. Faça a atualização para garantir que o SCVMM possa consultar os hosts com êxito.

**Atualizar hosts do Windows Server 2012 R2 para o Windows Server 2016**

1. Siga as etapas mencionadas [aqui](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) para executar o processo de atualização sem interrupção do cluster. 
2. Depois de adicionar o novo host ao cluster, atualize o host no console do SCVMM para instalar o Agente do VMM nesse host atualizado.
3. Execute *Update-VMVersion* para atualizar as versões de VM das Máquinas virtuais. 
4.  Acesse o portal do Azure e verifique o status da integridade replicada das máquinas virtuais no Cofre dos Serviços de Recuperação. 

## <a name="upgrade-windows-server-2012-r2-hosts-are-managed-by-highly-available-scvmm-2012-r2-server"></a>Atualizar hosts do Windows Server 2012 R2 gerenciados por um servidor do SCVMM 2012 R2 altamente disponível
Antes de atualizar os hosts do Windows Server 2012 R2, você precisa atualizar o SCVMM 2012 R2 para o SCVMM 2016. Há suporte para os modos de atualização a seguir durante a atualização de servidores do SCVMM 2012 R2 configurados com o Azure Site Recovery – Modo misto sem nenhum servidor do VMM adicional e Modo misto com servidores do VMM adicionais.

**Atualizar o SCVMM 2012 R2 para o SCVMM 2016**

1.  Desinstale o provedor do ASR navegando para o Painel de Controle -> Programas -> Programas e Recursos -> Microsoft Azure Site Recovery e clique em Desinstalar
2. Siga as etapas mencionadas [aqui](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#upgrade-a-standalone-vmm-server) com base no modo de atualização que deseja executar.
3. Inicie o console do SCVMM e verifique o status de cada hosts na guia **Malhas**. Clique em **Atualizar** para obter o status mais recente. Você deverá ver o status como “Precisa de Atenção”.
4. Instale o último [Provedor do Microsoft Azure Site Recovery](https://aka.ms/downloaddra) no SCVMM.
5. Atualize o último [agente do MARS (Serviço de Recuperação do Microsoft Azure)](https://aka.ms/latestmarsagent) em cada host do cluster. Faça a atualização para garantir que o SCVMM possa consultar os hosts com êxito.


**Atualizar hosts do Windows Server 2012 R2 para o Windows Server 2016**

1. Siga as etapas mencionadas [aqui](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) para executar o processo de atualização sem interrupção do cluster.
2. Depois de adicionar o novo host ao cluster, atualize o host no console do SCVMM para instalar o Agente do VMM nesse host atualizado.
3. Execute *Update-VMVersion* para atualizar as versões de VM das Máquinas virtuais. 
4.  Acesse o portal do Azure e verifique o status da integridade replicada das máquinas virtuais no Cofre dos Serviços de Recuperação. 

## <a name="next-steps"></a>Próximas etapas
Depois que a atualização dos hosts for executada, você poderá executar um [failover de teste](tutorial-dr-drill-azure.md) para testar a integridade do status de replicação e recuperação de desastre.


---
title: "Configurar a recuperação de desastre para VMs do Azure para uma região do Azure secundária com o Azure Site Recovery (versão prévia)"
description: "Saiba como configurar a recuperação de desastre para VMs do Azure em uma região do Azure diferente usando o serviço Azure Site Recovery."
services: site-recovery
author: rajani-janaki-ram
manager: carmonm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/18/2017
ms.author: rajanaki
ms.custom: mvc
ms.openlocfilehash: 9a92bf402863fd1ee688fba7c20d3a2cd3e299da
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-disaster-recovery-for-azure-vms-to-a-secondary-azure-region-preview"></a>Configurar a recuperação de desastre para VMs do Azure para uma região do Azure secundária (versão prévia)

O serviço [Azure Site Recovery](site-recovery-overview.md) contribui para sua estratégia de recuperação de desastre ao gerenciar e orquestrar a replicação, o failover e o failback de computadores locais e de VMs (máquinas virtuais) do Azure.

Este tutorial mostra como configurar a recuperação de desastre em uma região secundária do Azure para VMs do Azure. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um cofre dos Serviços de Recuperação
> * Verificar as configurações de recursos de destino
> * Configurar o acesso de saída para VMs
> * Habilitar a replicação para uma VM

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- Verifique se você entende os [componentes e a arquitetura do cenário](concepts-azure-to-azure-architecture.md).
- Examine os [requisitos de suporte](site-recovery-support-matrix-azure-to-azure.md) de todos os componentes.

## <a name="create-a-vault"></a>Criar um cofre

Crie o cofre em qualquer região, exceto a região de origem.

1. Entre no [portal do Azure](https://portal.azure.com) > **Serviços de Recuperação**.
2. Clique em **Novo** > **Monitoramento e Gerenciamento** > **Backup e Site Recovery**.
3. Em **Nome**, especifique um nome amigável para identificar o cofre. Se você tiver mais de uma assinatura, selecione uma delas.
4. Crie um grupo de recursos ou selecione um existente. Especifique uma região do Azure. Para verificar as regiões com suporte, confira a disponibilidade geográfica nos [Detalhes dos Preços de Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
5. Para acessar rapidamente o cofre no painel, clique em **Fixar no painel** e em **Criar**.

   ![Novo cofre](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

   O novo cofre é adicionado ao **Painel** em **Todos os recursos** e na página principal **Cofres dos Serviços de Recuperação**.

## <a name="verify-target-resources"></a>Verifique os recursos de destino

1. Verifique se a sua assinatura do Azure permite criar VMs na região de destino usada para recuperação de desastre. Contate o suporte para habilitar a cota necessária.

2. Verifique se a sua assinatura tem recursos suficientes para dar suporte a VMs com tamanhos que correspondem às VMs de origem. O Site Recovery escolhe o mesmo tamanho ou o tamanho mais próximo possível para a VM de destino.

## <a name="configure-outbound-network-connectivity"></a>Configurar a conectividade de rede de saída

Para o Site Recovery funcionar conforme o esperado, você precisa fazer algumas alterações na conectividade de rede de saída das VMs que você deseja replicar.

- O Site Recovery não dá suporte ao uso de um proxy de autenticação para controlar a conectividade de rede.
- Se você tem um proxy de autenticação, a replicação não pode ser habilitada.

### <a name="outbound-connectivity-for-urls"></a>Conectividade de saída para URLs

Se você está usando um proxy de firewall baseado em URL para controlar a conectividade de saída, permita o acesso às URLs a seguir, usadas pelo Site Recovery.

| **URL** | **Detalhes** |
| ------- | ----------- |
| *.blob.core.windows.net | Permite que os dados sejam gravados da VM para a conta de armazenamento de cache da região de origem. |
| login.microsoftonline.com | Fornece autorização e autenticação para as URLs do serviço Site Recovery. |
| *.hypervrecoverymanager.windowsazure.com | Permite que a VM se comunique com o serviço Site Recovery. |
| *.servicebus.windows.net | Permite que a VM grave o monitoramento do Site Recovery e os dados de diagnóstico. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Conectividade de saída para intervalos de endereços IP

Ao usar quaisquer regras de firewall, de proxy ou de NSG baseadas em IP para controlar a conectividade de saída, os intervalos de endereços IP a seguir precisam estar na lista de permissões. Baixe uma lista de intervalos nos links a seguir:

  - [Intervalos de IP do datacenter do Microsoft Azure](http://www.microsoft.com/en-us/download/details.aspx?id=41653)
  - [Intervalos de IP do datacenter do Microsoft Azure na Alemanha](http://www.microsoft.com/en-us/download/details.aspx?id=54770)
  - [Intervalos de IP do datacenter do Microsoft Azure na China](http://www.microsoft.com/en-us/download/details.aspx?id=42064)
  - [Intervalos de endereços IP e URLs do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)
  - [Endereços IP de ponto de extremidade de serviço do Site Recovery](https://aka.ms/site-recovery-public-ips)

Use essas listas para configurar os controles de acesso de rede em sua rede. Você pode usar este [script](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702) para criar as regras de NSG necessárias.

## <a name="verify-azure-vm-certificates"></a>Verifique os certificados de VM do Azure

Verifique se todos os certificados raiz mais recentes estão presentes nas VMs do Windows ou Linux que deseja replicar. Se os certificados raiz mais recentes não estiverem, a VM não poderá ser registrada no Site Recovery devido a restrições de segurança.

- Para VMs Windows, instale todas as atualizações do Windows mais recentes na VM para que todos os certificados raiz confiáveis estejam no computador. Em um ambiente desconectado, siga os processos padrão do Windows Update e de atualização de certificado para sua organização.

- Para VMs Linux, siga as diretrizes fornecidas pelo seu distribuidor Linux para obter os certificados raiz confiáveis mais recentes e a lista de certificados revogados na VM.

## <a name="set-permissions-on-the-account"></a>Definir permissões na conta

O Azure Site Recovery fornece três funções internas para controlar as operações de gerenciamento do Site Recovery.

- **Colaborador do Site Recovery** -essa função tem todas as permissões necessárias para gerenciar operações do Azure Site Recovery em um cofre dos Serviços de Recuperação. No entanto, um usuário com essa função não pode criar ou excluir um cofre dos Serviços de Recuperação ou atribuir direitos de acesso a outros usuários. Essa função é ideal para os administradores de recuperação de desastre que podem habilitar e gerenciar a recuperação de desastre para aplicativos ou organizações inteiras.

- **Operador do Site Recovery** - essa função tem permissões para executar e gerenciar operações de Failover e Failback. Um usuário com essa função não pode habilitar ou desabilitar a replicação, criar ou excluir cofres, registrar nova infraestrutura ou atribuir direitos de acesso a outros usuários. Essa função é ideal para um operador de recuperação de desastre que pode executar failover em máquinas virtuais ou aplicativos quando instruído por administradores de TI ou proprietários do aplicativo em uma situação de desastre real ou simulada. Após a resolução do desastre, o operador de recuperação de desastre pode proteger e fazer failback das máquinas virtuais novamente.

- **Leitor do Site Recovery**: essa função tem permissões para exibir todas as operações de gerenciamento do Site Recovery. Essa função é ideal para um executivo de monitoramento de TI que possa monitorar o estado atual de proteção e gerar tíquetes de suporte.

Saiba mais sobre as [funções internas do RBAC do Azure](../active-directory/role-based-access-built-in-roles.md)

## <a name="enable-replication"></a>Habilitar a replicação

### <a name="select-the-source"></a>Selecione a origem

1. Em cofres dos Serviços de Recuperação, clique no nome do cofre > **+Replicar**.
2. Em **fonte**, selecione **Azure - VISUALIZAÇÃO**.
3. Em **Local de origem**, selecione a fonte de região do Azure em que suas VMs estão sendo executados.
4. Selecione o **modelo de implantação de máquina virtual do Azure** para VMs: **Gerenciador de Recursos** ou **Clássico**.
5. Selecione o **Grupo de recursos de origem** para VMs do Gerenciador de Recursos ou **serviço de nuvem** para VMs clássicas.
6. Clique em **OK** para salvar as configurações.

### <a name="select-the-vms"></a>Selecione as VMs

O Site Recovery recupera uma lista das VMs associadas ao serviço de nuvem/grupo de recursos e assinatura.

1. Em **Máquinas Virtuais**, selecione as VMs que deseja replicar.
2. Clique em **OK**.

### <a name="configure-replication-settings"></a>Definir configurações de replicação

O Site Recovery cria as configurações padrão e a política de replicação para a região de destino. Você pode alterar as configurações com base nas suas necessidades.

![Configurar definições](./media/azure-to-azure-tutorial-enable-replication/settings.png)

- **Localização de destino**: a região de destino usada para recuperação de desastre. É recomendável que a localização de destino corresponda à localização do cofre do Site Recovery.

- **Grupo de recursos de destino**: o grupo de recursos na região de destino que contém as VMs do Azure após o failover. Por padrão, o Site Recovery cria um novo grupo de recursos na região de destino com um sufixo "asr".

- **Rede virtual de destino**: a rede na região de destino na qual as VMs estarão localizadas após o failover.
  Por padrão, o Site Recovery cria uma nova rede virtual (e sub-redes) na região de destino com um sufixo "asr".

- **Contas de armazenamento de cache**: o Site Recovery utiliza uma conta de armazenamento na região de origem. As alterações às VMs de origem são enviadas para essa conta, antes da replicação para a localização de destino.

- **Contas de armazenamento de destino**: por padrão, o Site Recovery cria uma nova conta de armazenamento na região de destino, para espelhar a conta de armazenamento da VM de origem.

- **Conjuntos de disponibilidade de destino**: por padrão, o Site Recovery cria um novo conjunto de disponibilidade na região de destino, com o sufixo "asr".

- **Nome da política de replicação**: nome da política.

- **Retenção de ponto de recuperação**: por padrão, o Site Recovery mantém os pontos de recuperação por 24 horas. É possível configurar um valor entre 1 e 72 horas.

- **Frequência de instantâneo consistente com o aplicativo**: por padrão, o Site Recovery obtém um instantâneo consistente com o aplicativo a cada 4 horas. É possível configurar qualquer valor entre 1 e 12 horas. Um instantâneo consistente com o aplicativo é um instantâneo em um ponto no tempo dos dados do aplicativo dentro da VM. O VSS (Serviço de Cópias de Sombra de Volume) garante que o aplicativo na VM esteja em um estado consistente quando o instantâneo for criado.

### <a name="track-replication-status"></a>Acompanhar o status de replicação

1. Em **Configurações**, clique em **Atualizar** para obter o status mais recente.

2. Você pode acompanhar o progresso do trabalho **Habilitar proteção** em **Configurações** > **Trabalhos** > **Trabalhos de do Site Recovery**.

3. Em **Configurações** > **Itens Replicados**, você pode exibir o status das máquinas virtuais e o andamento da replicação inicial. Clique em VM para fazer uma busca detalhada em suas configurações.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou a recuperação de desastre para uma VM do Azure. Próxima etapa é testar sua configuração.

> [!div class="nextstepaction"]
> [Realizar uma simulação de recuperação de desastre](azure-to-azure-tutorial-dr-drill.md)

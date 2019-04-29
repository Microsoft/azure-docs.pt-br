---
title: Configurar a recuperação de desastre de servidores locais físicos para o Azure com o Azure Site Recovery | Microsoft Docs
description: Saiba como configurar a recuperação de desastre de servidores Windows e Linux locais para o Azure, com o serviço Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 12/31/2018
ms.author: raynew
ms.openlocfilehash: a6be2fbbf946f13213f95b8876cfdddfa2ffbe67
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098131"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-physical-servers"></a>Configurar a recuperação de desastre de servidores físicos locais para o Azure

O serviço [Azure Site Recovery](site-recovery-overview.md) contribui para sua estratégia de recuperação de desastre ao gerenciar e orquestrar a replicação, o failover e o failback de computadores locais e de VMs (máquinas virtuais) do Azure.

Este tutorial mostra como configurar a recuperação de desastre de servidores Windows e Linux físicos locais para o Azure. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar os pré-requisitos locais e do Azure
> * Criar um cofre de Serviços de Recuperação para o Site Recovery 
> * Configurar os ambientes de replicação de origem e destino
> * Criar uma política de replicação
> * Habilitar a replicação para um servidor

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- Verifique se você entende os [componentes e a arquitetura](physical-azure-architecture.md) para este cenário.
- Examine os [requisitos de suporte](vmware-physical-secondary-support-matrix.md) de todos os componentes.
- Verifique se os servidores que você deseja replicar estão em conformidade com os [requisitos de VM do Azure](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
- Prepare o Azure. Você precisa de uma assinatura do Azure, de uma rede virtual do Azure e de uma conta de armazenamento.
- Prepare uma conta para a instalação automática do serviço de Mobilidade em cada servidor que você deseja replicar.

Antes de começar, observe se:

- Após fazer failover para o Azure, os servidores físicos não poderão fazer failback para computadores físicos locais. Somente é possível fazer failback para VMs VMware. 
- Este tutorial configura a recuperação de desastres do servidor físico no Azure com as configurações mais simples. Se você quiser saber mais sobre outras opções, leia nosso guias:
    - Configure a [origem da replicação](physical-azure-set-up-source.md), incluindo o servidor de configuração de recuperação de Site.
    - Configure o [destino de replicação](physical-azure-set-up-target.md).
    - Configure uma [política de replicação](vmware-azure-set-up-replication.md) e [habilite a replicação](vmware-azure-enable-replication.md).


### <a name="set-up-an-azure-account"></a>Configurar uma conta do Azure

Obtenha uma [conta do Microsoft Azure](https://azure.microsoft.com/).

- Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
- Saiba mais sobre os [preços do Site Recovery](site-recovery-faq.md#pricing) e obtenha [detalhes dos preços](https://azure.microsoft.com/pricing/details/site-recovery/).
- Descubra quais [regiões têm suporte](https://azure.microsoft.com/pricing/details/site-recovery/) do Site Recovery.

### <a name="verify-azure-account-permissions"></a>Verificar as permissões de conta do Azure

Verifique se sua conta do Azure tem permissões para a replicação de VMs para o Azure.

- Examine as [permissões](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) você precisa para replicar máquinas para o Azure.
- Verifique e modifique as permissões de [acesso baseado em função](../role-based-access-control/role-assignments-portal.md). 



### <a name="set-up-an-azure-network"></a>Configure uma rede do Azure

Configurar uma [rede do Azure](../virtual-network/quick-create-portal.md).

- As VMs do Azure são colocadas nessa rede quando são criadas após o failover.
- A rede deve estar na mesma região que o cofre dos Serviços de Recuperação


## <a name="set-up-an-azure-storage-account"></a>Configure uma conta de armazenamento do Azure

Configure uma [conta de armazenamento do Azure](../storage/common/storage-quickstart-create-account.md).

- O Site Recovery replica máquinas locais para o armazenamento do Azure. As VMs do Azure são criadas a partir do armazenamento após o failover.
- A conta de armazenamento deve estar na mesma região do cofre de Serviços de Recuperação.


### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparar uma conta para a instalação do serviço de Mobilidade

O serviço de mobilidade deve ser instalado em cada servidor que você deseja replicar. O Site Recovery instala esse serviço automaticamente quando você habilita a replicação para o servidor. Para instalar automaticamente, você precisa preparar uma conta que o Site Recovery usará para acessar o servidor.

- Você pode usar uma conta local ou de domínio
- Para VMs do Windows, se você não estiver usando uma conta de domínio, desabilite o controle de Acesso de Usuários Remotos no computador local. Para fazer isso, no registro, em **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, adicione a entrada DWORD **LocalAccountTokenFilterPolicy** com um valor de 1.
- Para adicionar a entrada do Registro para desabilitar a configuração de uma CLI, digite:       ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Para Linux, a conta deve ser a raiz no servidor Linux de origem.


## <a name="create-a-vault"></a>Criar um cofre

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Selecionar uma meta de proteção

Selecione o que será replicado e para onde será replicado.

1. Clique em **Cofres dos Serviços de Recuperação** > cofre.
2. No Menu Recursos, clique em **Site Recovery** > **Preparar Infraestrutura** > **Meta de proteção**.
3. Em **Meta de proteção**, selecione **Para o Azure** > **Não virtualizado/Outro**.

## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Defina o servidor de configuração, registre-o no cofre e descubra VMs.

1. Clique em **Site Recovery** > **Preparar a infraestrutura** > **Origem**.
2. Se não tiver um servidor de configuração, clique em **+Servidor de configuração**.
3. Em **Adicionar Servidor**, verifique se **Servidor de Configuração** aparece em **Tipo de servidor**.
4. Baixe o arquivo de instalação Configuração Unificada da Recuperação de Site.
5. Baixe a chave do registro do cofre. Você precisará dela quando executar a Configuração Unificada. A chave é válida por cinco dias após ser gerada.

   ![Configurar origem](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>Registrar o servidor de configuração no cofre

Antes de começar, faça o seguinte: 

#### <a name="verify-time-accuracy"></a>Verificar a precisão de tempo
Na máquina do servidor de configuração, certifique-se de que o relógio do sistema esteja sincronizado com um [Servidor de Horário](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service). Ele deve ser correspondente. Se ele estiver 15 minutos adiantado ou atrasado, a instalação poderá falhar.

#### <a name="verify-connectivity"></a>Verificar conectividade
Verifique se o computador pode acessar estas URLs com base no ambiente: 

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

As regras de firewall baseadas em endereço IP devem permitir a comunicação com todas as URLs do Azure listadas acima pela porta HTTPS (443). Para simplificar e limitar os Intervalos de IP, é recomendável fazer a filtragem de URL.

- **IPs comerciais** – permita os [Intervalos de IP do Datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e a porta HTTPS (443). Permita que os intervalos de endereços IP para a região do Azure de sua assinatura deem suporte ao AAD, ao Backup, à Replicação e às URLs de Armazenamento.  
- **IPs Governamentais** – permita os [Intervalos de IP do Datacenter do Azure Governamental](https://www.microsoft.com/en-us/download/details.aspx?id=57063) e a porta HTTPS (443) em todas as regiões US Gov (Virgínia, Texas, Arizona e Iowa) para dar suporte ao AAD, ao Backup, à Replicação e às URLs de Armazenamento.  

#### <a name="run-setup"></a>Executar a instalação
Execute a Instalação Unificada como Administrador Local, para instalar o servidor de configuração. O servidor de processo e o servidor de destino mestre também são instalados, por padrão, no servidor de configuração.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

Após a conclusão do registro, o servidor de configuração será exibido na página **Configurações** > **Servidores** no cofre.

## <a name="set-up-the-target-environment"></a>Configurar o ambiente de origem

Selecione e verifique os recursos de destino.

1. Clique em **Preparar infraestrutura** > **Destino** e selecione a assinatura do Azure que você deseja usar.
2. Especifique o modelo de implantação de destino.
3. A Recuperação de Site verifica se você tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

   ![Destino](./media/physical-azure-disaster-recovery/network-storage.png)


## <a name="create-a-replication-policy"></a>Criar uma política de replicação

1. Para criar uma nova política de replicação, clique em **Infraestrutura de Site Recovery** > **Políticas de Replicação** > **+Política de Replicação**.
2. Em **Criar política de replicação**, especifique um nome de política.
3. Em **Limite de RPO**, especifique o limite de RPO (objetivo de pontos de recuperação). Esse valor especifica com que frequência os pontos de recuperação de dados são criados. Um alerta será gerado se a replicação contínua exceder esse limite.
4. Em **Retenção do ponto de recuperação**, especifique qual será a duração (em horas) da janela de retenção para cada ponto de recuperação. VMs replicadas podem ser recuperadas para qualquer ponto em uma janela. Há suporte para retenção de até 24 horas para máquinas replicadas para armazenamento premium e 72 horas para o armazenamento padrão.
5. Em **Frequência do instantâneo consistente com o aplicativo**, especifique com que frequência (em minutos) os pontos de recuperação contendo instantâneos consistentes com aplicativos serão criados. Clique em **OK** para criar a política.

    ![Política de replicação](./media/physical-azure-disaster-recovery/replication-policy.png)


A política é associada automaticamente ao servidor de configuração. Por padrão, uma política de correspondência é criada automaticamente para failback. Por exemplo, se a política de replicação for **rep-policy**, uma política de failback **rep-policy-failback** será criada. Essa política não é usada até você iniciar um failback do Azure.

## <a name="enable-replication"></a>Habilitar a replicação

Habilite a replicação para cada servidor.

- O Site Recovery instalará o serviço de Mobilidade quando a replicação for habilitada.
- Quando você habilitar a replicação para um servidor, poderá levar 15 minutos ou mais para que as alterações entrem em vigor e apareçam no portal.

1. Clique em **Replicar aplicativo** > **Origem**.
2. Em **Origem**, selecione o servidor de configuração.
3. Em **Tipo de computador**, selecione **Máquinas físicas**.
4. Selecione o servidor de processo (o servidor de configuração). Em seguida, clique em **OK**.
5. No **Destino**, selecione a assinatura e o grupo de recursos em que você deseja criar as VMs do Azure após o failover. Escolha o modelo de implantação que você deseja usar no Azure (clássico ou gerenciamento de recurso).
6. Selecione a conta de armazenamento do Azure que você deseja usar para replicar os dados. 
7. Selecione a rede e a sub-rede do Azure às quais as VMs do Azure se conectarão quando forem criadas após o failover.
8. Selecione **Configurar agora para computadores selecionados** para aplicar a configuração de rede a todos os computadores selecionados para proteção. Selecione **Configurar mais tarde** para selecionar a rede do Azure por computador. 
9. Em **Computadores físicos**, clique em **+ Computador físico**. Especifique o nome e o endereço IP. Selecione o sistema operacional do computador que você deseja replicar. Levará alguns minutos para que os servidores sejam descobertos e listados. 
10. Em **Propriedades** > **Configurar propriedades**, selecione a conta que será usada pelo servidor de processo para instalar automaticamente o serviço de Mobilidade no computador.
11. Em **Configurações de replicação** > **Definir configurações de replicação**, verifique se a política de replicação correta está selecionada. 
12. Clique em **Habilitar a Replicação**. Você pode acompanhar o progresso do trabalho **Habilitar Proteção** em **Configurações** > **Trabalhos** > **Trabalhos de Recuperação de Site**. Após o trabalho de **Finalizar Proteção** ser executado, o computador estará pronto para failover.


Para monitorar os servidores adicionados, você pode verificar o horário da última descoberta em **Servidores de Configuração** > **Último Contato Em**. Para adicionar computadores sem esperar por um período de descoberta agendada, realce o servidor de configuração (não clique nele) e clique em **Atualizar**.

## <a name="next-steps"></a>Próximas etapas

[Execute uma simulação de recuperação de desastre](tutorial-dr-drill-azure.md).

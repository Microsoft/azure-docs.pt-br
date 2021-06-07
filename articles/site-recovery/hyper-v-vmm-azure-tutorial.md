---
title: Configurar a recuperação de desastre do Hyper-V (com VMM) usando o Azure Site Recovery
description: Saiba como configurar a recuperação de desastre de VMs locais do Hyper-V em nuvens do System Center VMM para o Azure usando o Site Recovery.
ms.topic: tutorial
ms.date: 03/19/2020
ms.custom: MVC
ms.openlocfilehash: c806f968bc6530879f64ddbf6fd4c7d45aa7a8d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89442813"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Configurar a recuperação de desastre de VMs locais do Hyper-V em nuvens do VMM para o Azure

Este tutorial descreve como habilitar a replicação de VMs (máquinas virtuais) Hyper-V locais gerenciadas pelo System Center VMM (Virtual Machine Manager) para o Azure, usando o [Azure Site Recovery](site-recovery-overview.md). Se você não estiver usando o VMM, [siga este tutorial](hyper-v-azure-tutorial.md).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Selecionar a origem e o destino de replicação.
> * Configurar o ambiente de replicação de origem, incluindo componentes de Site Recovery locais e o ambiente de replicação de destino.
> * Configurar o mapeamento de rede entre as redes de VM do VMM e as redes virtuais do Azure.
> * Criar uma política de replicação.
> * Habilitar a replicação para uma VM.

> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário. Eles usam opções padrão quando possível e não mostram todas as possíveis configurações e caminhos. Para obter instruções detalhadas, leia o artigo na seção **Guias de Instruções** da [documentação do Site Recovery](./index.yml).

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial supõe que você já concluiu as seguintes etapas:

1. [Preparar o Azure](tutorial-prepare-azure.md)
1. [Preparar servidores Hyper-V locais](hyper-v-prepare-on-premises-tutorial.md)

## <a name="select-a-replication-goal"></a>Selecione uma meta de replicação

1. No portal do Azure, acesse **Cofres dos Serviços de Recuperação** e selecione o cofre **ContosoVMVault** que foi criado no tutorial [Preparar o Azure](tutorial-prepare-azure.md#create-a-recovery-services-vault).
1. Em **Introdução**, selecione **Site Recovery** > **Preparar Infraestrutura** e defina as seguintes configurações:
    1. Em **Meta de proteção** > **Em que local os seus computadores estão?** , selecione **Local**.
    1. Em **Para que local deseja replicar os seus computadores?** , selecione **Para o Azure**.
    1. Em **Os seus computadores estão virtualizados?** , selecione **Sim, com o Hyper-V**.
    1. Em **Você está usando o VMM do System Center para gerenciar seus hosts Hyper-V**, selecione **Sim**.
1. Selecione **OK**.

   ![Meta de replicação](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Confirmar planejamento de implantação

1. Em **Planejamento de implantação**, se você estiver planejando uma implantação grande, baixe o Planejador de Implantações do Hyper-V usando o link na página. [Saiba mais](hyper-v-deployment-planner-overview.md) sobre o planejamento de implantação do Hyper-V.
1. Para este tutorial, não precisamos do Planejador de Implantações. Em **Você concluiu o planejamento de implantação?** , selecione **Farei isso mais tarde** e, em seguida, selecione **OK**.

## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Quando configurar o ambiente de origem, instale o Provedor do Azure Site Recovery no servidor do VMM e registre o servidor no cofre. Você instala o agente de Serviços de Recuperação do Azure em cada host Hyper-V.

1. **Preparar a Infraestrutura**. Selecione **Origem**.
1. **Preparar a origem**. Selecione **+VMM** para adicionar um servidor do VMM. Em **Adicionar Servidor**, verifique se o **Servidor do System Center VMM** aparece em **Tipo de servidor**.
1. Baixe o instalador do Provedor do Microsoft Azure Site Recovery.
1. Baixe a chave do registro do cofre. Você precisará dessa chave quando executar a instalação do Provedor. A chave é válida por cinco dias após ser gerada.
1. Baixe o instalador do agente dos Serviços de Recuperação do Microsoft Azure.

   ![Baixar o Provedor, a chave de Registro e o agente](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>Instalar o Provedor no servidor VMM

1. No **Microsoft Update** do assistente de instalação do Provedor do Azure Site Recovery. Escolher usar o Microsoft Update para verificar atualizações do Provedor.
1. **Instalação**. Aceite a localização de instalação padrão do Provedor e selecione **Instalar**.
1. Após a instalação, no Assistente de Registro do Microsoft Azure Site Recovery, selecione **Configurações do Cofre**, **Procurar** e, em **Arquivo de chave**, selecione a chave do cofre do arquivo que você baixou.
1. Especifique a assinatura do Azure Site Recovery e o nome do cofre (**ContosoVMVault**). Especifique um nome amigável para o servidor de VMM para identificá-lo no cofre.
1. **Configurações de Proxy**. Selecione **Conectar-se diretamente ao Azure Site Recovery sem um proxy**.
1. Aceite o local padrão para o certificado que é usado para criptografar dados. Dados criptografados serão descriptografados quando você executar o failover.
1. **Sincronizar metadados da nuvem**. Selecione **Sincronizar os metadados da nuvem com o portal do Site Recovery**. Esta ação só precisa acontecer uma vez em cada servidor. Em seguida, selecione **Registrar**.
1. Depois que o servidor foi registrado no cofre, selecione **Concluir**.

Após a conclusão do registro, os metadados do servidor são recuperados pelo Azure Site Recovery e o servidor VMM é exibido em **Infraestrutura do Site Recovery**.

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>Instalar o agente dos Serviços de Recuperação em hosts do Hyper-V

Instale o agente em cada host Hyper-V que contém máquinas virtuais que você deseja replicar.

No Assistente de Instalação do Agente de Serviços de Recuperação do Microsoft Azure, defina estas configurações:

1. **Verificação de Pré-requisitos**. Selecione **Avançar**. Pré-requisitos faltantes serão instalados automaticamente.
1. **Configurações de Instalação**. Aceite o local da instalação. Em seguida, selecione **Instalar**.

    >[!NOTE]
    >Pra o Azure Site Recovery, o **Local do Cache** não é necessário.

1. **Instalação**. Quando a instalação for concluída, selecione **Fechar** para concluir o assistente.

   ![Instalar o agente](./media/hyper-v-vmm-azure-tutorial/mars-install.png)

## <a name="set-up-the-target-environment"></a>Configurar o ambiente de origem

1. Selecione **Preparar infraestrutura** > **Destino**.
1. Selecione a assinatura e o grupo de recursos **ContosoRG**, em que as VMs do Azure serão criadas após o failover.
1. Selecione o modelo de implantação do **Resource Manager**.

O Site Recovery verifica se você tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

## <a name="configure-network-mapping"></a>Configurar o mapeamento de rede

1. Acesse **Infraestrutura do Site Recovery** > **Mapeamentos de rede** > **Mapeamento de Rede**. Selecione o ícone **+ Mapeamento de Rede**.
1. **Adicionar mapeamento de rede**. Selecione o servidor de **Origem do System Center VMM**. Em **Destino**, selecione Azure.
1. Verifique a assinatura e o modelo de implantação após o failover.
1. **Rede de origem**. Selecione a rede de VMs locais de origem.
1. **Rede de destino**. Selecione a rede do Azure na qual as VMs do Azure de réplica estarão localizadas quando criadas após o failover. Depois, selecione **OK**.

   ![Mapeamento de rede](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Configurar uma política de replicação

1. Selecione **Preparar a Infraestrutura** > **Configurações de Replicação** >  **+Criar e associar**.
1. Em **Criar e associar política**, especifique um nome de política. Estamos usando **ContosoReplicationPolicy**.
1. Aceite as configurações padrão e selecione **OK**:
   - **Frequência de cópia** indica que, após a replicação inicial, os dados delta serão replicadas a cada cinco minutos.
   - **Retenção do ponto de recuperação** indica que cada ponto de recuperação será mantido por duas horas.
   - **Frequência do instantâneo consistente com o aplicativo** indica que os pontos de recuperação contendo instantâneos consistentes com aplicativos serão criados a cada hora.
   - **Hora de início para replicação inicial** indica que a replicação inicial deve começar imediatamente.
   - **Criptografar dados armazenados no Azure** é definido para o padrão (**Desativado**) e indica que dados em repouso no Azure não são criptografados.
1. Depois que a política for criada, selecione **OK**. Quando você cria uma nova política, ela é automaticamente associada à nuvem do VMM.

## <a name="enable-replication"></a>Habilitar a replicação

1. **Replicar aplicativo**. Selecione **Origem**.
1. **Fonte**. Selecione a nuvem do VMM. Depois, selecione **OK**.
1. **Destino**. Verifique o destino (Azure), a assinatura do cofre e selecione o modelo do **Resource Manager**.
1. Selecione a conta de armazenamento **contosovmsacct1910171607** e a rede do Azure **ContosoASRnet**.
1. **Máquinas virtuais** > **Selecionar**. Selecione a VM que você deseja replicar. Depois, selecione **OK**.

   É possível acompanhar o progresso da ação **Habilitar Proteção** em **Trabalhos** > **Trabalhos do Site Recovery**. Depois que o trabalho **Finalizar Proteção** for concluído, a replicação inicial será concluída e a VM estará pronta para failover.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Realizar uma simulação de recuperação de desastre](tutorial-dr-drill-azure.md)

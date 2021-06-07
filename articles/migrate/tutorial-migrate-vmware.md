---
title: Migrar VMs VMware sem agente com a Migração de Servidor das Migrações para Azure
description: Saiba como executar uma migração sem agente de VMs VMware com as Migrações para Azure.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: tutorial
ms.date: 06/09/2020
ms.custom: mvc
ms.openlocfilehash: a1d745c95b89efefabbd0b83061f9dcd9fe13911
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567111"
---
# <a name="migrate-vmware-vms-to-azure-agentless"></a>Migrar VMs VMware para o Azure (sem agente)

Este artigo mostra como migrar VMs VMware locais para o Azure usando a ferramenta de [Migração de Servidor das Migrações para Azure](migrate-services-overview.md#azure-migrate-server-migration-tool) com a migração sem agente. Você também pode migrar VMs VMware usando a migração baseada em agente. [Compare](server-migrate-overview.md#compare-migration-methods) os métodos.

Este tutorial é o terceiro de uma série que demonstra como avaliar e migrar VMs do VMware para o Azure. 

> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário para que você possa configurar rapidamente uma prova de conceito. Os tutoriais usam opções padrão quando possível e não mostram todas as configurações e todos os caminhos possíveis. 


Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Adicionar a ferramenta de Migração de Servidor das Migrações para Azure.
> * Descobrir VMs que você deseja migrar.
> * Iniciar a replicação de VMs.
> * Executar uma migração de teste para verificar se tudo está funcionando conforme o esperado.
> * Executar uma migração de VM completa.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, você deverá:

1. [Concluir o primeiro tutorial](./tutorial-discover-vmware.md) para preparar o Azure e o VMware para migração.
2. É recomendável que você conclua o segundo tutorial para [avaliar as VMs VMware](./tutorial-assess-vmware-azure-vm.md) antes de migrá-las para o Azure, mas isso não é obrigatório. 
3. Vá para o projeto criado ou [crie um projeto](./create-manage-projects.md)
4. Verifique as permissões para sua conta do Azure. Sua conta do Azure precisa de permissões para criar uma VM e gravar em um disco gerenciado do Azure.

## <a name="set-up-the-azure-migrate-appliance"></a>Configurar o dispositivo das Migrações para Azure

A Migração de Servidor das Migrações para Azure executa um dispositivo de VM VMware leve que é usado para descoberta, avaliação e migração sem agente de VMs VMware. Se você tiver seguido o [tutorial de avaliação](./tutorial-assess-vmware-azure-vm.md), já terá definido o dispositivo. Se você não tiver feito isso, configure-o agora, usando um destes métodos:

- **Modelo OVA**: [Configurar](how-to-set-up-appliance-vmware.md) em uma VM do VMware usando um modelo OVA baixado.
- **Script**: [configurar](deploy-appliance-script.md) em uma VM do VMware ou computador físico usando um script do instalador do PowerShell. Esse método deverá ser usado se você não puder configurar uma VM usando um modelo OVA ou se você estiver no Azure Government.

Depois de criar o dispositivo, você verifica se é possível conectá-lo ao Migrações para Azure: Avaliação do Servidor, configurá-lo pela primeira vez e registrá-lo com o projeto de Migrações para Azure.

## <a name="replicate-vms"></a>Replicar VMs

Depois de configurar o dispositivo e concluir a descoberta, você pode começar a replicação de VMs VMware no Azure. 

- Você pode executar até 500 replicações simultaneamente.
- No portal, é possível selecionar até dez VMs simultaneamente para migração. Para migrar mais computadores, adicione-os a grupos em lotes de dez.

Habilite a replicação da seguinte maneira:

1. No projeto das Migrações para Azure > **Servidores**, **Migrações para Azure: Migração de Servidor**, clique em **Replicar**.

    ![Replicar VMs](./media/tutorial-migrate-vmware/select-replicate.png)

2. Em **Replicar**, > **Configurações de origem** > **Seus computadores estão virtualizados?** , selecione **Sim, com o VMware vSphere**.
3. Em **Dispositivo local**, selecione o nome do dispositivo de Migrações para Azure que você configurou > **OK**. 

    ![Configurações de origem](./media/tutorial-migrate-vmware/source-settings.png)

4. Em **Máquinas virtuais**, selecione os computadores que deseja replicar. Para aplicar o dimensionamento de VMs e o tipo de disco de uma avaliação caso você tenha executado uma, em **Importar configurações de migração de uma avaliação das Migrações para Azure?** , selecione **Sim** e selecione o grupo de VMs e o nome da avaliação. Se você não estiver usando as configurações de avaliação, selecione **Não**.
   
    ![Selecionar avaliação](./media/tutorial-migrate-vmware/select-assessment.png)

5. Em **Máquinas virtuais**, selecione as VMs que você deseja migrar. Em seguida, clique em **Próximo: configurações de destino**.

    ![Selecione as máquinas virtuais](./media/tutorial-migrate-vmware/select-vms.png)

6. Em **Configurações de destino**, selecione a assinatura e a região de destino. Especifique o grupo de recursos no qual as VMs do Azure residem após a migração.
7. Em **Rede Virtual**, selecione a VNet/sub-rede do Azure em que as VMs do Azure ingressarão após a migração.
8. Em **Opções de disponibilidade**, selecione:
    -  Zona de Disponibilidade para fixar o computador migrado para uma Zona de Disponibilidade específica na região. Use essa opção para distribuir servidores que formam uma camada de aplicativo de vários nós entre Zonas de Disponibilidade diferentes. Se você selecionar essa opção, precisará especificar a zona de disponibilidade a ser usada para cada computador selecionado na guia Computação. Essa opção só estará disponível se a região de destino selecionada para a migração der suporte a Zonas de Disponibilidade
    -  Conjunto de Disponibilidade para colocar o computador migrado em um conjunto de disponibilidade. O grupo de recursos de destino selecionado precisa ter um ou mais conjuntos de disponibilidade para que possa usar essa opção.
    - Nenhuma opção de redundância de infraestrutura será necessária se você não precisar de nenhuma dessas configurações de disponibilidade para os computadores migrados.
9. Em **Tipo de criptografia de disco**, selecione:
    - Criptografia em repouso com chave de criptografia gerenciada pela plataforma
    - Criptografia em repouso com a chave gerenciada pelo cliente
    - Criptografia dupla com chaves gerenciadas por plataforma e gerenciadas pelo cliente

   > [!NOTE]
   > Para replicar VMs com a CMK, você precisará [criar um conjunto de criptografia de disco](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set) no grupo de recursos de destino. Um objeto de conjunto de criptografia de disco mapeia o Managed Disks para um Key Vault que contém a CMK a ser usada para a SSE.
  
10. Em **Benefício Híbrido do Azure**:

    - Selecione **Não** se não desejar aplicar o Benefício Híbrido do Azure. Em seguida, clique em **Próximo**.
    - Selecione **Sim** se você tiver computadores Windows Server cobertos com assinaturas ativas do Software Assurance ou do Windows Server e quiser aplicar o benefício aos computadores que estão sendo migrados. Em seguida, clique em **Próximo**.

    ![Configurações de destino](./media/tutorial-migrate-vmware/target-settings.png)

11. Em **Computação**, examine o nome da VM, o tamanho, o tipo de disco do SO e a configuração de disponibilidade (se selecionado na etapa anterior). As VMs devem estar em conformidade com os [requisitos do Azure](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).

    - **Tamanho da VM**: se você estiver usando recomendações de avaliação, o menu suspenso de tamanho da VM mostrará o tamanho recomendado. Caso contrário, as Migrações para Azure escolherão um tamanho com base na correspondência mais próxima na assinatura do Azure. Como alternativa, escolha um tamanho manual em **Tamanho da VM do Azure**. 
    - **Disco do SO**: especifique o disco do sistema operacional (inicialização) para a VM. O disco do sistema operacional é o disco que tem o carregador de inicialização e o instalador do sistema operacional. 
    - **Zona de Disponibilidade**: especifique a zona de disponibilidade a ser usada.
    - **Conjunto de disponibilidade**: especifique o conjunto de disponibilidade a ser usado.

    > [!NOTE]
    > Se você quiser selecionar uma opção de disponibilidade diferente para um conjunto de máquinas virtuais, vá para a etapa 1 e repita as etapas selecionando opções de disponibilidade diferentes depois de iniciar a replicação para um conjunto de máquinas virtuais.




12. Em **Discos**, especifique se os discos da VM devem ser replicados no Azure e selecione o tipo de disco (discos gerenciados Premium ou HDD/SSD Standard) no Azure. Em seguida, clique em **Próximo**.
   
    ![Captura de tela mostrando a guia Discos da caixa de diálogo Replicar.](./media/tutorial-migrate-vmware/disks.png)

13. Em **Examinar e iniciar a replicação**, examine as configurações e clique em **Replicar** para iniciar a replicação inicial dos servidores.

> [!NOTE]
> É possível atualizar as configurações de replicação a qualquer momento antes do início da replicação em (**Gerenciar** > **Computadores em replicação**). Não é possível alterar as configurações após o início da replicação.

### <a name="provisioning-for-the-first-time"></a>Provisionar pela primeira vez

Se esta for a primeira VM que você estiver replicando no projeto, a Migração de Servidor provisionará automaticamente esses recursos no mesmo grupo de recursos do projeto.

- **Barramento de serviço**: Migração de Servidor usa o Barramento de Serviço para enviar mensagens de orquestração de replicação para o dispositivo.
- **Conta de armazenamento de gateway**: a Migração de Servidor usa a conta de armazenamento de gateway para armazenar informações de estado de armazenamento sobre as VMs que estão sendo replicadas.
- **Conta de armazenamento de log**: o dispositivo das Migrações para Azure carrega os logs de replicação das VMs em uma conta de armazenamento de log. As Migrações para Azure aplicam as informações de replicação aos discos gerenciados de réplica.
- **Cofre de chaves**: O dispositivo das Migrações para Azure usa o cofre de chaves para gerenciar as cadeias de conexão do barramento de serviço e as chaves de acesso das contas de armazenamento usadas na replicação.

## <a name="track-and-monitor"></a>Acompanhar e monitorar

1. Acompanhe o status do trabalho nas notificações do portal.
2. Monitore o status de replicação clicando em **Replicando servidores** em **Migrações para Azure: Migração de Servidor**.

     ![Monitorar a replicação](./media/tutorial-migrate-vmware/replicating-servers.png)

A replicação ocorre da seguinte maneira:
- Quando o trabalho Iniciar Replicação é concluído com êxito, os computadores começam sua replicação inicial para o Azure.
- Durante a replicação inicial, um instantâneo de VM é criado. Os dados do disco do instantâneo são replicados para discos gerenciados de réplica no Azure.
- Depois que a replicação inicial é concluída, a replicação delta é iniciada. As alterações incrementais em discos locais são replicadas periodicamente para os discos de réplica no Azure.

## <a name="run-a-test-migration"></a>Execute um teste de migração


Quando a replicação delta é iniciada, é possível executar uma migração de teste para as VMs antes de executar uma migração completa para o Azure. É altamente recomendável que você faça isso pelo menos uma vez para cada computador antes de migrá-lo.

- A execução de uma migração de teste verifica se a migração funcionará conforme o esperado, sem afetar os computadores locais, que permanecem operacionais e continuam sendo replicados. 
- A migração de teste simula a migração criando uma VM do Azure com o uso dos dados replicados (geralmente, fazendo a migração para uma VNet de não produção em sua assinatura do Azure).
- É possível usar a VM do Azure de teste replicada para validar a migração, executar testes de aplicativo e resolver problemas antes da migração completa.

Faça uma migração de teste da seguinte maneira:


1. Em **Metas de migração** > **Servidores** > **Migrações para Azure: Migração de Servidor**, clique em **Testar servidores migrados**.

     ![Testar servidores migrados](./media/tutorial-migrate-vmware/test-migrated-servers.png)

2. Clique com o botão direito do mouse na VM a ser testada e clique em **Migração de teste**.

    ![Migração de teste](./media/tutorial-migrate-vmware/test-migrate.png)

3. Em **Migração de Teste**, selecione a VNet do Azure na qual a VM do Azure estará localizada após a migração. Recomendamos que você use uma VNet que não seja de produção.
4. O trabalho **Migração de teste** é iniciado. Monitore o trabalho nas notificações do portal.
5. Após a conclusão da migração, veja a VM do Azure migrada em **Máquinas Virtuais** no portal do Azure. O nome do computador tem o sufixo **-Test**.
6. Depois que o teste for realizado, clique com o botão direito do mouse na VM do Azure em **Replicando computadores** e clique em **Limpar migração de teste**.

    ![Limpar migração](./media/tutorial-migrate-vmware/clean-up.png)


## <a name="migrate-vms"></a>Migrar VMs

Depois de verificar se a migração de teste funciona conforme o esperado, você poderá migrar os computadores locais.

1. No projeto das Migrações para Azure > **Servidores** > **Migrações para Azure: Migração de Servidor**, clique em **Replicando servidores**.

    ![Replicando servidores](./media/tutorial-migrate-vmware/replicate-servers.png)

2. Em **Replicando computadores**, clique com o botão direito do mouse na VM > **Migrar**.
3. Em **Migrar** > **Desligar máquinas virtuais e realizar uma migração planejada sem perda de dados**, selecione **Sim** > **OK**.
    - Por padrão, as Migrações para Azure desligam a VM local e executam uma replicação sob demanda para sincronizar as alterações de VM ocorridas desde a última replicação. Isso garante que não haja nenhuma perda de dados.
    - Se você não quiser desligar a VM, selecione **Não**
4. Um trabalho de migração é iniciado para a VM. Acompanhe o trabalho nas notificações do Azure.
5. Após a conclusão do trabalho, você poderá exibir e gerenciar a VM na página **Máquinas Virtuais**.

## <a name="complete-the-migration"></a>Concluir a migração

1. Depois que a migração for concluída, clique com o botão direito do mouse na VM > **Interromper Replicação**. Isso interromperá a replicação para o computador local e limpará as informações de estado da replicação da VM.
2. Instalamos automaticamente o agente de VM para as VMs Windows e Linux durante a migração. Examine os [requisitos](../virtual-machines/extensions/agent-linux.md#requirements) do agente Linux da VM do Azure nos computadores migrados, se o computador tiver o sistema operacional Linux, para garantir que a instalação do agente de VM Linux tenha sido feita corretamente. 
3. Execute todos os ajustes no aplicativo após a migração, como atualizar as cadeias de conexão de banco de dados e as configurações do servidor Web.
4. Execute o aplicativo final e o teste de aceitação da migração no aplicativo migrado que está sendo executado no Azure.
5. Transfira o tráfego para a instância migrada da VM do Azure.
6. Remova as VMs locais do inventário local de VMs.
7. Remova as VMs locais dos backups locais.
8. Atualize todas as documentações internas para mostrar o novo local e o endereço IP das VMs do Azure. 

## <a name="post-migration-best-practices"></a>Melhores práticas pós-migração

- Para aumentar a resiliência:
    - Proteja os dados fazendo backup das VMs do Azure por meio do serviço Backup do Azure. [Saiba mais](../backup/quick-backup-vm-portal.md).
    - Mantenha as cargas de trabalho em execução e continuamente disponíveis ao replicar as VMs do Azure em uma região secundária com o Site Recovery. [Saiba mais](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Para desempenho superior:
    - Por padrão, os discos de dados são criados com o cache de host definido como "Nenhum". Examine e ajuste o cache de disco de dados de acordo com suas necessidades de carga de trabalho. [Saiba mais](../virtual-machines/premium-storage-performance.md#disk-caching).  
- Para aumentar a segurança:
    - Bloqueie e limite o acesso ao tráfego de entrada com a [Central de Segurança do Azure – Administração just-in-time](../security-center/security-center-just-in-time.md).
    - Restrinja o tráfego de rede a pontos de extremidade com os [Grupos de Segurança de Rede](../virtual-network/network-security-groups-overview.md).
    - Implante o [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) para manter os discos em segurança e proteger os dados contra roubo e acesso não autorizado.
    - Leia mais sobre [como proteger recursos IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) e acesse a [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/).
- Para monitoramento e gerenciamento:
-  Considere implantar o [Gerenciamento de Custos do Azure](../cost-management-billing/cloudyn/overview.md) para monitorar o uso de recursos e os gastos.


## <a name="next-steps"></a>Próximas etapas

Investigue a [jornada de migração na nuvem](/azure/architecture/cloud-adoption/getting-started/migrate) no Cloud Adoption Framework do Azure.
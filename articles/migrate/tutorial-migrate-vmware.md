---
title: Migrar VMs VMware locais para o Azure com a Migração de Servidor das Migrações para Azure sem agente | Microsoft Docs
description: Descreve como executar uma migração sem agente de VMs VMware locais para o Azure, usando as Migrações para Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/08/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 885c877f219f59ab5049cf7b8e01243077d6d3eb
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348391"
---
# <a name="migrate-vmware-vms-to-azure-agentless"></a>Migrar VMs VMware para o Azure (sem agente)

Este artigo mostra como migrar VMs VMware locais para o Azure usando a migração sem agente com a ferramenta de Migração de Servidor das Migrações para Azure.

As [Migrações para Azure](migrate-services-overview.md) oferecem um hub central para acompanhar a descoberta, a avaliação e a migração de seus aplicativos e cargas de trabalho locais, bem como de instâncias de VM AWS/GCP, para o Azure. O hub fornece as ferramentas das Migrações para Azure para avaliação e migração, além de ofertas de ISV (fornecedor independente de software) de terceiros.

Este tutorial é o terceiro de uma série que demonstra como avaliar e migrar VMs VMware para o Azure usando a Migração e a Avaliação de Servidor das Migrações para Azure. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Preparar VMs para migração.
> * Adicionar a ferramenta de Migração de Servidor das Migrações para Azure.
> * Descobrir VMs que você deseja migrar.
> * Iniciar a replicação de VMs.
> * Executar uma migração de teste para verificar se tudo está funcionando conforme o esperado.
> * Executar uma migração de VM completa.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="migration-methods"></a>Métodos de migração

É possível migrar VMs VMware para o Azure usando a ferramenta de Migração de Servidor de Migrações para Azure. Essa ferramenta oferece algumas opções para a migração de VM VMware:

- Migração usando a replicação sem agente. Migre VMs sem precisar instalar nada nelas.
- Migração com um agente para replicação. Instale um agente na VM para replicação.

Para decidir se deseja usar a migração sem agente ou baseada em agente, leia estes artigos:

- [Saiba como](server-migrate-overview.md) funciona a migração sem agente e [examine as limitações](server-migrate-overview.md#agentless-migration-limitations).
- [Leia este artigo](tutorial-migrate-vmware-agent.md) se desejar usar o método baseado em agente.

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, você deverá:

1. [Entender](migrate-architecture.md) a arquitetura de migração do VMware.
2. [Concluir o primeiro tutorial](tutorial-prepare-vmware.md) desta série para configurar o Azure e o VMware para migração. Especificamente neste tutorial, é necessário:
    - [Preparar o Azure](tutorial-prepare-vmware.md#prepare-azure) para migração.
    - [Preparar o ambiente local](tutorial-prepare-vmware.md#prepare-for-agentless-vmware-migration) para migração.
    
3. Recomendamos que você tente avaliar as VMs VMware com a Avaliação de Servidor das Migrações para Azure antes de migrá-las para o Azure. Para configurar a avaliação, [conclua o segundo tutorial](tutorial-assess-vmware.md) desta série. Se não desejar avaliar VMs, será possível ignorar este tutorial. Embora seja recomendável experimentar uma avaliação, não é necessário executar uma avaliação antes de tentar uma migração.



## <a name="add-the-azure-migrate-server-migration-tool"></a>Adicionar a ferramenta Migração de Servidor das Migrações para Azure

Se você não seguiu o segundo tutorial para avaliar as VMs VMware, é necessário [seguir estas instruções](how-to-add-tool-first-time.md) para configurar um projeto das Migrações para Azure e selecionar a ferramenta de Migração de Servidor das Migrações para Azure. 

Se você seguiu o segundo tutorial e já tem uma configuração de projeto das Migrações para Azure, adicione a ferramenta de Migração de Servidor das Migrações para Azure da seguinte maneira:

1. No projeto das Migrações para Azure, clique em **Visão Geral**. 
2. Em **Descobrir, avaliar e migrar servidores**, clique em **Avaliar e migrar servidores**.

     ![Avaliar e migrar servidores](./media/tutorial-migrate-vmware/assess-migrate.png)

3. Em **Ferramentas de migração**, selecione **Clique aqui para adicionar uma ferramenta de migração quando você estiver pronto para fazer a migração**.

    ![Selecionar uma ferramenta](./media/tutorial-migrate-vmware/select-migration-tool.png)

4. Na lista de ferramentas, selecione **Migrações para Azure: Migração de Servidor** > **Adicionar ferramenta**

    ![Ferramenta Migração de Servidor](./media/tutorial-migrate-vmware/server-migration-tool.png)

## <a name="set-up-the-azure-migrate-appliance"></a>Configurar o dispositivo das Migrações para Azure

A Migração de Servidor das Migrações para Azure executa um dispositivo leve de VM VMware. O dispositivo executa a descoberta de VM e envia metadados de VM e dados de desempenho para a Migração de Servidor das Migrações para Azure. O mesmo dispositivo também é usado pela ferramenta Avaliação de Servidor das Migrações para Azure.

Se você seguiu o segundo tutorial para avaliar as VMs VMware, você já configurou o dispositivo durante o tutorial. Se você não seguiu esse tutorial, configure o dispositivo agora. Para fazer isto: 

- Baixe um arquivo de modelo OVA e importe-o para o vCenter Server.
- Crie o dispositivo e verifique se ele pode se conectar à Avaliação de Servidor das Migrações para Azure. 
- Configure o dispositivo pela primeira vez e registre-o com o projeto de Migrações para Azure.

Siga as instruções [deste artigo](how-to-set-up-appliance-vmware.md) para configurar o dispositivo.


## <a name="prepare-vms-for-migration"></a>Preparar VMs para migração

As Migrações para Azure requer algumas alterações de VM para verificar se elas podem ser migradas para o Azure.

- Para alguns [sistemas operacionais](server-migrate-overview.md#agentless-migration-limitations), as Migrações para Azure fazem essas alterações automaticamente.
- Se estiver migrando uma VM que não tem nenhum desses sistemas operacionais, siga as instruções para prepará-la.
- É importante fazer essas alterações antes de iniciar a migração. Se você migrar a VM antes de fazer a alteração, ela não poderá ser inicializada no Azure.
- As mudanças de configuração que você faz em VMs locais são replicadas no Azure após a replicação da VM ser habilitada. Para que as alterações sejam replicadas, verifique se o ponto de recuperação para o qual você migrará é posterior à hora na qual as alterações de configuração foram feitas localmente.


### <a name="prepare-windows-server-vms"></a>Preparar as VMs do Windows Server

**Ação** | **Detalhes** | **Instruções**
--- | --- | ---
Verifique se os volumes do Windows na VM do Azure usam as mesmas atribuições de letra da unidade que a VM local. | Configure a política de SAN como Todos Online. | 1. Entre na VM com uma conta do administrador e abra uma janela de comando.<br/> 2. Digite **diskpart** para executar o utilitário Diskpart.<br/> 3. Digite **SAN POLICY=OnlineAll**<br/> 4. Digite Exit para sair do Diskpart e feche o prompt de comando.
Habilitar o console de acesso serial do Azure para a VM do Azure | Isso ajuda a solucionar problemas. Não é necessário reinicializar a VM. A VM do Azure será reinicializada usando a imagem de disco, e isso é equivalente à reinicialização da nova VM. | Siga [estas instruções](https://docs.microsoft.com/azure/virtual-machines/windows/serial-console) para habilitar.
Instalar integração convidada do Hyper-V | Se você estiver migrando computadores que executam o Windows Server 2003, instale os Serviços de Integração de Convidado do Hyper-V no sistema operacional da VM. | [Saiba mais](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).
Área de Trabalho Remota | Habilite a Área de Trabalho Remota na VM e verifique se o Firewall do Windows não está bloqueando o acesso Área de Trabalho Remota em nenhum perfil de rede. | [Saiba mais](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).

### <a name="prepare-linux-vms"></a>Preparar VMs Linux

**Ação** | **Detalhes** 
--- | --- | ---
Instalar Integration Services do Linux para Hyper-V | A maioria das novas versões das distribuições do Linux tem isso incluído por padrão.
Recriar a imagem de inicialização do Linux para conter os drivers necessários do Hyper-V | Com isso, a VM será inicializada no Azure e só será necessária em algumas distribuições.
Habilitar registro em log do console serial do Azure | Isso ajuda a solucionar problemas. Não é necessário reinicializar a VM. A VM do Azure será reinicializada usando a imagem de disco, e isso é equivalente à reinicialização da nova VM.<br/> Siga [estas instruções](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console) para habilitar.
Atualizar arquivo do mapa do dispositivo | Atualize o arquivo do mapa do dispositivo que tem o nome do dispositivo para associações de volume, a fim de usar identificadores de dispositivo persistentes
Atualizar entradas fstab | Atualize entradas para usar identificadores de volume persistentes.
Remover regra udev | Remova as regras udev que reservam os nomes de interface em endereços MAC etc.
Atualizar adaptadores de rede | Atualize os adaptadores de rede para receber endereço IP baseado em DHCP.
Habilitar ssh | Verifique se o ssh está habilitado e se o serviço sshd foi definido para ser iniciado automaticamente na reinicialização.<br/> Verifique se as solicitações de conexão ssh de entrada não estão bloqueadas pelo firewall do sistema operacional ou por regras programáveis.

[Siga este artigo](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic) que aborda estas etapas para executar uma VM Linux no Azure e que inclui instruções para algumas das distribuições populares do Linux.  


## <a name="replicate-vms"></a>Replicar VMs

Com a descoberta concluída, é possível começar a replicação de VMs do VMware no Azure.

1. No projeto das Migrações para Azure > **Servidores**, **Migrações para Azure: Migração de Servidor**, clique em **Replicar**.

    ![Replicar VMs](./media/tutorial-migrate-vmware/select-replicate.png)

2. Em **Replicar**, > **Configurações de origem** > **Seus computadores estão virtualizados?** , selecione **Sim, com o VMware vSphere**.
3. Em **Dispositivo local**, selecione o nome do dispositivo de Migrações para Azure que você configurou > **OK**. 

    ![Configurações de origem](./media/tutorial-migrate-vmware/source-settings.png)

    - Esta etapa pressupõe que você já configurou um dispositivo quando concluiu o tutorial.
    - Se você ainda não configurou um dispositivo, siga as instruções [neste artigo](how-to-set-up-appliance-vmware.md).

4. Em **Máquinas virtuais**, selecione os computadores que deseja replicar.
    - Se você tiver executado uma avaliação das VMs, poderá aplicar recomendações de dimensionamento de VM e de tipo de disco (Premium/Standard) dos resultados da avaliação. Para fazer isso, em **Importar configurações de migração de uma avaliação de Migrações para Azure?** , selecione a opção **Sim**.
    - Se você não tiver executado uma avaliação ou não quiser usar as configurações de avaliação, selecione a opção **Não**.
    - Se você optou por usar a avaliação, selecione o grupo de VMs e o nome da avaliação.

    ![Selecionar avaliação](./media/tutorial-migrate-vmware/select-assessment.png)

5. Em **Máquinas virtuais**, pesquise as VMs conforme necessário e marque cada VM que você deseja migrar. Em seguida, clique em **Próximo: configurações de destino**.

    ![Selecione as máquinas virtuais](./media/tutorial-migrate-vmware/select-vms.png)

6. Em **Configurações de destino**, selecione a assinatura e a região de destino para a qual você migrará e especifique o grupo de recursos no qual as VMs do Azure residirão após a migração. Em **Rede Virtual**, selecione a VNet/sub-rede do Azure na qual as VMs do Azure serão ingressadas após a migração.
7. Em **Benefício Híbrido do Azure**:

    - Selecione **Não** se não desejar aplicar o Benefício Híbrido do Azure. Em seguida, clique em **Próximo**.
    - Selecione **Sim** se você tiver computadores Windows Server cobertos com assinaturas ativas do Software Assurance ou do Windows Server e quiser aplicar o benefício aos computadores que estão sendo migrados. Em seguida, clique em **Próximo**.

    ![Configurações de destino](./media/tutorial-migrate-vmware/target-settings.png)

8. Em **Computação**, examine o nome da VM, o tamanho, o tipo de disco do sistema operacional e o conjunto de disponibilidade. As VMs devem estar em conformidade com os [requisitos do Azure](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements).

    - **Tamanho da VM**: se você estiver usando recomendações de avaliação, o menu suspenso de tamanho da VM conterá o tamanho recomendado. Caso contrário, as Migrações para Azure escolherão um tamanho com base na correspondência mais próxima na assinatura do Azure. Como alternativa, escolha um tamanho manual em **Tamanho da VM do Azure**. 
    - **Disco do SO**: especifique o disco do sistema operacional (inicialização) para a VM. O disco do sistema operacional é o disco que tem o carregador de inicialização e o instalador do sistema operacional. 
    - **Conjunto de disponibilidade**: se a VM deve estar em um conjunto de disponibilidade do Azure após a migração, especifique-o. O conjunto precisa estar no grupo de recursos de destino especificado para a migração.

    ![Configurações de computação da VM](./media/tutorial-migrate-vmware/compute-settings.png)

9. Em **Discos**, especifique se os discos de VM devem ser replicados no Azure e selecione o tipo de disco (discos gerenciados Premium ou HDD/SSD Standard) no Azure. Em seguida, clique em **Próximo**.
    - Você pode excluir discos da replicação.
    - Se você excluir os discos, eles não estarão presentes na VM do Azure após a migração. 

    ![Discos](./media/tutorial-migrate-vmware/disks.png)

10. Em **Examinar e iniciar a replicação**, examine as configurações e clique em **Replicar** para iniciar a replicação inicial dos servidores.

> [!NOTE]
> É possível atualizar configurações de replicação a qualquer momento antes que a replicação seja iniciada em **Gerenciar** > **Replicando computadores**. Não é possível alterar as configurações após o início da replicação.

### <a name="provisioning-for-the-first-time"></a>Provisionar pela primeira vez

Se esta for a primeira VM que você estiver replicando no projeto das Migrações para Azure, a Migração de Servidor das Migrações para Azure provisionará automaticamente esses recursos no mesmo grupo de recursos do projeto.

- **Barramento de serviço**: a Migração de Servidor das Migrações para Azure usa o barramento de serviço para enviar mensagens de orquestração de replicação para o dispositivo.
- **Conta de armazenamento de gateway**: a Migração de Servidor usa a conta de armazenamento de gateway para armazenar informações de estado de armazenamento sobre as VMs que estão sendo replicadas.
- **Conta de armazenamento de log**: o dispositivo das Migrações para Azure carrega os logs de replicação das VMs em uma conta de armazenamento de log. As Migrações para Azure aplicam as informações de replicação aos discos gerenciados de réplica.
- **Cofre de chaves**: O dispositivo das Migrações para Azure usa o cofre de chaves para gerenciar as cadeias de conexão do barramento de serviço e as chaves de acesso das contas de armazenamento usadas na replicação. Você deve ter configurado as permissões de que o cofre de chaves precisa para acessar a conta de armazenamento que você preparou. [Examine essas permissões](tutorial-prepare-vmware.md#assign-role-assignment-permissions).   


## <a name="track-and-monitor"></a>Acompanhar e monitorar


- Quando você clica em **Replicar**, um trabalho Iniciar Replicação é iniciado. 
- Quando o trabalho Iniciar Replicação é concluído com êxito, os computadores começam sua replicação inicial para o Azure.
- Durante a replicação inicial, um instantâneo de VM é criado. Os dados do disco do instantâneo são replicados para discos gerenciados de réplica no Azure.
- Depois que a replicação inicial é concluída, a replicação delta é iniciada. As alterações incrementais em discos locais são replicadas periodicamente para os discos de réplica no Azure.

É possível acompanhar o status do trabalho nas notificações do portal.

É possível monitorar o status de replicação clicando em **Replicando servidores** em **Migrações para Azure: Migração de Servidor**.
![Monitorar a replicação](./media/tutorial-migrate-vmware/replicating-servers.png)




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

1. Depois que a migração for concluída, clique com o botão direito do mouse na VM > **Interromper migração**. Isso interromperá a replicação para o computador local e limpará as informações de estado da replicação da VM.
2. Instale o agente do [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) ou do [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) da VM do Azure nos computadores migrados.
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
- Para aumentar a segurança:
    - Bloqueie e limite o acesso ao tráfego de entrada com a [Central de Segurança do Azure – Administração just-in-time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Restrinja o tráfego de rede a pontos de extremidade com os [Grupos de Segurança de Rede](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Implante o [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) para manter os discos em segurança e proteger os dados contra roubo e acesso não autorizado.
    - Leia mais sobre [como proteger recursos IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) e acesse a [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/).
- Para monitoramento e gerenciamento:
-  Considere implantar o [Gerenciamento de Custos do Azure](https://docs.microsoft.com/azure/cost-management/overview) para monitorar o uso de recursos e os gastos.


## <a name="next-steps"></a>Próximas etapas

Investigue a [jornada de migração na nuvem](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) no Cloud Adoption Framework do Azure.

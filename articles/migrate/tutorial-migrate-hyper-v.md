---
title: Migrar VMs locais do Hyper-V para o Azure com a Migração de Servidor das Migrações para Azure | Microsoft Docs
description: Este artigo descreve como migrar VMs locais do Hyper-V para o Azure com a Migração de Servidor das Migrações para Azure
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 4a88e9dddd492d5c24698bcde8c3a1fd942eaf66
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854187"
---
# <a name="migrate-hyper-v-vms-to-azure"></a>Migrar VMs do Hyper-V para o Azure 

Este artigo mostra como migrar VMs locais do Hyper-V para o Azure usando a migração sem agente com a ferramenta Migração de Servidor das Migrações para Azure.

As [Migrações para Azure](migrate-services-overview.md) oferecem um hub central para acompanhar a descoberta, a avaliação e a migração de aplicativos e cargas de trabalho locais, bem como VMs da nuvem pública/privada para o Azure. O hub fornece as ferramentas das Migrações para Azure para avaliação e migração, além de ofertas de ISV (fornecedor independente de software) de terceiros.

Este tutorial é o terceiro de uma série que demonstra como avaliar e migrar o Hyper-V para o Azure usando a Avaliação e a Migração de Servidor das Migrações para Azure. Neste tutorial, você aprenderá como:


> [!div class="checklist"]
> * Preparar o Azure e o ambiente local do Hyper-V
> * Configurar o ambiente de origem e implante um dispositivo de replicação.
> * Configurar o ambiente de destino.
> * Habilite a replicação.
> * Executar uma migração de teste para verificar se tudo está funcionando conforme o esperado.
> * Executar uma migração completa para o Azure.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, você deverá:

1. [Examinar](migrate-architecture.md) a arquitetura de migração do Hyper-V.
2. [Concluir o primeiro tutorial](tutorial-prepare-hyper-v.md) desta série para configurar o Azure e o Hyper-V para migração. No primeiro tutorial, você vai:
    - [Preparar o Azure](tutorial-prepare-hyper-v.md#prepare-azure) para migração.
    - [Preparar o ambiente local](tutorial-prepare-hyper-v.md#prepare-for-hyper-v-migration) para migração.
3. Recomendamos que você tente avaliar as VMs do Hyper-V usando a Avaliação de Servidor das Migrações para Azure antes de migrá-las para o Azure. Para fazer isso, [conclua o segundo tutorial](tutorial-assess-hyper-v.md) desta série. Embora recomendemos experimentar uma avaliação, não é necessário executar uma avaliação antes de migrar as VMs.
4. Verifique se a sua conta do Azure foi atribuída à função Colaborador da Máquina Virtual, para que você tenha permissões para:

    - Criar uma VM no grupo de recursos selecionado.
    - Criar uma VM na rede virtual selecionada.
    - Gravar em um disco gerenciado do Azure.   
5. [Configure uma rede do Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Quando você faz a migração para o Azure, as VMs do Azure criadas são ingressadas em uma rede do Azure especificada durante a configuração da migração.


## <a name="add-the-azure-migrate-server-migration-tool"></a>Adicionar a ferramenta Migração de Servidor das Migrações para Azure

Se você não seguiu o segundo tutorial para avaliar as VMs do Hyper-V, [siga estas instruções](how-to-add-tool-first-time.md) para configurar um projeto das Migrações para Azure e adicionar a ferramenta Migração de Servidor das Migrações para Azure ao projeto.

Se você seguiu o segundo tutorial e já tem uma configuração de projeto das Migrações para Azure, adicione a ferramenta Migração de Servidor das Migrações para Azure da seguinte maneira:

1. No projeto das Migrações para Azure, clique em **Visão Geral**. 
2. Em **Descobrir, avaliar e migrar servidores**, clique em **Avaliar e migrar servidores**.
3. Em **Ferramentas de migração**, selecione **Clique aqui para adicionar uma ferramenta de migração quando você estiver pronto para fazer a migração**.

    ![Selecionar uma ferramenta](./media/tutorial-migrate-hyper-v/select-migration-tool.png)

4. Na lista de ferramentas, selecione **Migrações para Azure: Migração de Servidor** > **Adicionar ferramenta**

    ![Ferramenta Migração de Servidor](./media/tutorial-migrate-hyper-v/server-migration-tool.png)


## <a name="set-up-the-azure-migrate-appliance"></a>Configurar o dispositivo das Migrações para Azure

A Migração de Servidor das Migrações para Azure executa um dispositivo leve de VM do Hyper-V.

- O dispositivo executa a descoberta de VM e envia metadados de VM e dados de desempenho para a Migração de Servidor das Migrações para Azure.
- O mesmo dispositivo também é usado pela ferramenta Avaliação de Servidor das Migrações para Azure.

Para configurar o dispositivo:
- Se você seguiu o segundo tutorial para avaliar as VMs do Hyper-V, você já configurou o dispositivo durante o tutorial.
- Se você não seguiu esse tutorial, configure o dispositivo agora. Para fazer isto: 

    - Baixe um VHD compactado do Hyper-V no portal do Azure.
    - Crie o dispositivo e verifique se ele pode se conectar à Avaliação de Servidor das Migrações para Azure. 
    - Configure o dispositivo pela primeira vez e registre-o com o projeto de Migrações para Azure.

    Siga as instruções [deste artigo](how-to-set-up-appliance-hyper-v.md) para configurar o dispositivo.

## <a name="prepare-hyper-v-hosts"></a>Prepare os hosts do Hyper-V

1. No projeto das Migrações para Azure > **Servidores**, em **Migrações para Azure: Migração de Servidor**, clique em **Descobrir**.
2. Em **Descobrir computadores** > **Os computadores estão virtualizados?** , selecione **Sim, com o Hyper-V**.
3. Em **Região de destino**, selecione a região do Azure para a qual deseja migrar os computadores.
6. Selecione **Confirme se a região de destino da migração é nome-da-região**.
7. Clique em **Criar recursos**. Isso criará um cofre do Azure Site Recovery em segundo plano.
    - Se você já configurou a migração com a Migração de Servidor das Migrações para Azure, essa opção não é exibida, pois os recursos foram configurados anteriormente.
    - Você não poderá alterar a região de destino desse projeto depois de clicar nesse botão.
    - Todas as migrações seguintes serão feitas para essa região.
    
8. Em **Preparar servidores host do Hyper-V**, baixe o provedor de Replicação do Hyper-V e o arquivo de chave de registro.
    - A chave de registro é necessária para registrar o host do Hyper-V na Migração de Servidor das Migrações para Azure.
    - A chave é válida por cinco dias após ser gerada.

    ![Baixar o provedor e a chave](./media/tutorial-migrate-hyper-v/download-provider-hyper-v.png)

4. Copie o arquivo de configuração do provedor e o arquivo de chave de registro para cada host do Hyper-V (ou nó de cluster) que executa as VMs que deseja replicar.
5. Execute o arquivo de configuração do provedor em cada host, conforme descrito no próximo procedimento.
6. Depois de instalar o provedor nos hosts, em **Descobrir computadores**, clique em **Finalizar registro**.

    ![Finalizar registro](./media/tutorial-migrate-hyper-v/finalize-registration.png)

Podem ser necessários até 15 minutos após a finalização do registro até que as VMs descobertas sejam exibidas na Migração de Servidor das Migrações para Azure. À medida que as VMs são descobertas, a contagem de **Servidores descobertos** aumenta.

![Servidores descobertos](./media/tutorial-migrate-hyper-v/discovered-servers.png)

### <a name="register-hyper-v-hosts"></a>Registrar hosts do Hyper-V

Instale o arquivo de configuração baixado (AzureSiteRecoveryProvider.exe) em cada host do Hyper-V relevante.

1. Execute o arquivo de configuração do provedor em cada host ou nó de cluster.
2. No assistente de Configuração do Provedor > **Microsoft Update**, aceite o uso do Microsoft Update para verificar se há atualizações do provedor.
3. Em **Instalação**, aceite a localização de instalação padrão do Provedor e do agente e selecione **Instalar**.
4. Após a instalação, no Assistente de Registro > **Configurações do Cofre**, selecione **Procurar** e, em **Arquivo de Chave**, selecione o arquivo de chave do cofre baixado.
5. Em **Configurações de Proxy**, especifique como o provedor em execução no host se conectará à Internet.
    - Se o dispositivo estiver localizado atrás de um servidor proxy, você precisará especificar as configurações de proxy.
    - Especifique o nome do proxy como **http://ip-address** ou **http://FQDN** . Não há suporte para servidores proxy HTTPS.
   

6. Verifique se o provedor pode acessar as [URLs necessárias](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-url-access).
7. Em **Registro**, depois que o host for registrado, clique em **Concluir**.

## <a name="replicate-hyper-v-vms"></a>Replicar VMs do Hyper-V

Com a descoberta concluída, você poderá iniciar a replicação de VMs do Hyper-V para o Azure.

1. No projeto das Migrações para Azure > **Servidores**, **Migrações para Azure: Migração de Servidor**, clique em **Replicar**.
2. Em **Replicar**, > **Configurações de origem** > **Os computadores estão virtualizados?** , selecione **Sim, com o Hyper-V**. Em seguida, clique em **Próximo: Máquinas virtuais**.
3. Em **Máquinas virtuais**, selecione os computadores que deseja replicar.
    - Se você tiver executado uma avaliação das VMs, poderá aplicar recomendações de dimensionamento de VM e de tipo de disco (Premium/Standard) dos resultados da avaliação. Para fazer isso, em **Importar configurações de migração de uma avaliação de Migrações para Azure?** , selecione a opção **Sim**.
    - Se você não tiver executado uma avaliação ou não quiser usar as configurações de avaliação, selecione a opção **Não**.
    - Se você optou por usar a avaliação, selecione o grupo de VMs e o nome da avaliação.

        ![Selecionar avaliação](./media/tutorial-migrate-hyper-v/select-assessment.png)

4. Em **Máquinas virtuais**, pesquise as VMs conforme necessário e marque cada VM que você deseja migrar. Em seguida, clique em **Avançar: configurações de destino**.

    ![Selecione as máquinas virtuais](./media/tutorial-migrate-hyper-v/select-vms.png)

5. Em **Configurações de destino**, selecione a região de destino para a qual você fará a migração, a assinatura e o grupo de recursos no qual as VMs do Azure residirão após a migração.
7. Em **Conta de Armazenamento de Replicação**, selecione a conta de armazenamento do Azure na qual os dados replicados serão armazenados no Azure.
8. Em **Rede Virtual**, selecione a VNet/sub-rede do Azure na qual as VMs do Azure serão ingressadas após a migração.
9. Em **Benefício Híbrido do Azure**:

    - Selecione **Não** se não desejar aplicar o Benefício Híbrido do Azure. Em seguida, clique em **Avançar**.
    - Selecione **Sim** se você tiver computadores Windows Server cobertos com assinaturas ativas do Software Assurance ou do Windows Server e quiser aplicar o benefício aos computadores que estão sendo migrados. Em seguida, clique em **Próximo**.

    ![Configurações de destino](./media/tutorial-migrate-hyper-v/target-settings.png)

10. Em **Computação**, examine o nome da VM, o tamanho, o tipo de disco do sistema operacional e o conjunto de disponibilidade. As VMs devem estar em conformidade com os [requisitos do Azure](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements).

    - **Tamanho da VM**: se você estiver usando recomendações de avaliação, o menu suspenso de tamanho da VM conterá o tamanho recomendado. Caso contrário, as Migrações para Azure escolherão um tamanho com base na correspondência mais próxima na assinatura do Azure. Como alternativa, escolha um tamanho manual em **Tamanho da VM do Azure**. 
    - **Disco do SO**: especifique o disco do sistema operacional (inicialização) para a VM. O disco do sistema operacional é o disco que tem o carregador de inicialização e o instalador do sistema operacional. 
    - **Conjunto de disponibilidade**: se a VM deve estar em um conjunto de disponibilidade do Azure após a migração, especifique-o. O conjunto precisa estar no grupo de recursos de destino especificado para a migração.

    ![Configurações de computação da VM](./media/tutorial-migrate-hyper-v/compute-settings.png)

11. Em **Discos**, especifique se os discos de VM devem ser replicados no Azure e selecione o tipo de disco (discos gerenciados Premium ou HDD/SSD Standard) no Azure. Em seguida, clique em **Próximo**.
    - Você pode excluir discos da replicação.
    - Se você excluir os discos, eles não estarão presentes na VM do Azure após a migração. 

    ![Discos](./media/tutorial-migrate-hyper-v/disks.png)

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
- Depois que a replicação inicial é concluída, a replicação delta é iniciada. As alterações incrementais em discos locais são replicadas periodicamente para o Azure.

É possível acompanhar o status do trabalho nas notificações do portal.

É possível monitorar o status de replicação clicando em **Replicando servidores** em **Migrações para Azure: Migração de Servidor**.
![Monitorar a replicação](./media/tutorial-migrate-hyper-v/replicating-servers.png)



## <a name="run-a-test-migration"></a>Execute um teste de migração


Quando a replicação delta é iniciada, é possível executar uma migração de teste para as VMs antes de executar uma migração completa para o Azure. É altamente recomendável que você faça isso pelo menos uma vez para cada computador antes de migrá-lo.

- A execução de uma migração de teste verifica se a migração funcionará conforme o esperado, sem afetar os computadores locais, que permanecem operacionais e continuam sendo replicados. 
- A migração de teste simula a migração criando uma VM do Azure com o uso dos dados replicados (geralmente, fazendo a migração para uma VNet de não produção em sua assinatura do Azure).
- É possível usar a VM do Azure de teste replicada para validar a migração, executar testes de aplicativo e resolver problemas antes da migração completa.

Faça uma migração de teste da seguinte maneira:


1. Em **Metas de migração** > **Servidores** > **Migrações para Azure: Migração de Servidor**, clique em **Testar servidores migrados**.

     ![Testar servidores migrados](./media/tutorial-migrate-hyper-v/test-migrated-servers.png)

2. Clique com o botão direito do mouse na VM a ser testada e clique em **Migração de teste**.

    ![Migração de teste](./media/tutorial-migrate-hyper-v/test-migrate.png)

3. Em **Migração de Teste**, selecione a VNet do Azure na qual a VM do Azure estará localizada após a migração. Recomendamos que você use uma VNet que não seja de produção.
4. O trabalho **Migração de teste** é iniciado. Monitore o trabalho nas notificações do portal.
5. Após a conclusão da migração, veja a VM do Azure migrada em **Máquinas Virtuais** no portal do Azure. O nome do computador tem o sufixo **-Test**.
6. Depois que o teste for realizado, clique com o botão direito do mouse na VM do Azure em **Replicando computadores** e clique em **Limpar migração de teste**.

    ![Limpar migração](./media/tutorial-migrate-hyper-v/clean-up.png)


## <a name="migrate-vms"></a>Migrar VMs

Depois de verificar se a migração de teste funciona conforme o esperado, você poderá migrar os computadores locais.

1. No projeto das Migrações para Azure > **Servidores** > **Migrações para Azure: Migração de Servidor**, clique em **Replicando servidores**.

    ![Replicando servidores](./media/tutorial-migrate-hyper-v/replicate-servers.png)

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

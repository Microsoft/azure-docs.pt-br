---
title: Migrar computadores físicos locais ou computadores virtualizados para o Azure com Migração de Servidor de Migrações para Azure | Microsoft Docs
description: Este artigo descreve como migrar computadores físicos locais ou computadores virtualizados para o Azure com a Migração de Servidor de Migrações para Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 728bf785edebcd17599b6a56edea1e26ed2d2fbc
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311783"
---
# <a name="migrate-physical-or-virtualized-servers-to-azure"></a>Migrar servidores físicos ou virtualizados para o Azure 

Este artigo mostra como migrar servidores físicos ou virtualizados para o Azure. A ferramenta de Migração de Servidor de Migrações para Azure oferece migração de servidores físicos e virtualizados usando a replicação baseada em agente. Usando essa ferramenta, você pode migrar uma ampla variedade de computadores para o Azure:

- Migrar servidores físicos locais.
- Migrar VMs virtualizadas por plataformas, como Xen, KVM.
- Migrar VMs VMware ou Hyper-V. Isso é útil se, por alguma razão, você não consegue usar o fluxo de migração padrão que a Migração de Servidor de Migrações para Azure oferece para [Hyper-V](tutorial-migrate-hyper-v.md), migração [Sem agente do VMware](tutorial-migrate-vmware.md) ou migração [baseada em agente do VMware](tutorial-migrate-vmware-agent.md).
- Migre VMs em execução em nuvens privadas.
- Migre VMs em execução em nuvens públicas, como AWS (Amazon Web Services) ou GCP (Google Cloud Platform).


As [Migrações para Azure](migrate-services-overview.md) oferecem um hub central para acompanhar a descoberta, a avaliação e a migração de seus aplicativos e cargas de trabalho locais, bem como de instâncias de VM de nuvem, para o Azure. O hub fornece as ferramentas das Migrações para Azure para avaliação e migração, além de ofertas de ISV (fornecedor independente de software) de terceiros.


Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Preparar o Azure para migração com a ferramenta de Migração de Servidor de Migrações para Azure.
> * Verificar os requisitos de computadores que você deseja migrar e prepare um computador para o dispositivo de replicação de Migrações para Azure usado para descobrir e migrar computadores para o Azure.
> * Adicionar a ferramenta de Migração de Servidor de Migrações para Azure no hub Migrações para Azure.
> * Configurar o aplicativo de replicação.
> * Instalar o serviço de mobilidade nos computadores que você deseja migrar.
> * Habilite a replicação.
> * Executar uma migração de teste para verificar se tudo está funcionando conforme o esperado.
> * Executar uma migração completa para o Azure.

> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário para que você possa configurar rapidamente uma prova de conceito. Os tutoriais usam opções padrão quando possível e não mostram todas as configurações e todos os caminhos possíveis. Para obter informações detalhadas, examine as instruções para Migrações para Azure.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, você deverá:

1. [Examinar](migrate-architecture.md) a arquitetura de migração.
2. Verificar se a sua conta do Azure foi atribuída à função Colaborador da Máquina Virtual para ter permissões para:

    - Criar uma VM no grupo de recursos selecionado.
    - Criar uma VM na rede virtual selecionada.
    - Gravar em um disco gerenciado do Azure. 

3. [Configure uma rede do Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Quando você replica para o Azure, as VMs do Azure são criadas e ingressadas em uma rede do Azure especificada durante ao configurar a migração.


## <a name="prepare-azure"></a>Preparar o Azure

Configure as permissões do Azure antes de migrar com a Migração de Servidor de Migrações para Azure.

- **Criar um projeto**: sua conta do Azure precisa de permissões para criar um projeto de Migrações para Azure. 
- **Registrar o dispositivo de replicação de Migrações para Azure**: o dispositivo de replicação cria e registra um aplicativo do Azure Active Directory em sua conta do Azure. Delegue permissões para isso.
- **Criar um Cofre de Chaves**: para migrar computadores, as Migrações para Azure criam um cofre de chaves no grupo de recursos para gerenciar chaves de acesso para a conta de armazenamento de replicação em sua assinatura. Para criar o cofre, você precisa de permissões de atribuição de função no grupo de recursos no qual o projeto de Migrações para Azure reside. 


### <a name="assign-permissions-to-create-project"></a>Atribuir permissões para criar o projeto

1. No portal do Azure, abra a assinatura e selecione **Controle de acesso (IAM).**
2. Em **Verificar acesso**, localize a conta relevante e clique nela para exibir as permissões.
3. Você deve ter permissões de **Colaborador** e **Proprietário**.
    - Se você acaba de criar uma conta gratuita do Azure, você é o proprietário da assinatura.
    - Se você não for o proprietário da assinatura, trabalhe com o proprietário para atribuir a função.

### <a name="assign-permissions-to-register-the-replication-appliance"></a>Atribuir permissões para registrar o dispositivo de replicação

Para a migração baseada em agente, delegue permissões para Migração de Servidor de Migrações para Azure para criar e registrar um aplicativo do Azure AD em sua conta. Você pode atribuir permissões usando um dos seguintes métodos:

- Um locatário/administrador global pode conceder permissões a usuários no locatário para criar e registrar aplicativos do Azure AD.
- Um locatário/administrador global pode atribuir a função de Desenvolvedor de Aplicativos (que tem as permissões) à conta.

Vale a pena observar que:

- Os aplicativos não têm nenhuma outra permissão de acesso na assinatura diferente daquelas descritas acima.
- Você só precisa dessas permissões ao registrar um novo dispositivo de replicação. Você pode remover as permissões depois que o dispositivo de replicação está configurado. 


#### <a name="grant-account-permissions"></a>Conceder permissões da conta

O locatário/administrador global pode conceder permissões da seguinte maneira

1. No Azure AD, o administrador de locatário/global deve navegar até **Azure Active Directory** > **Usuários** > **Configurações do Usuário**.
2. O administrador deve definir **Registros de aplicativo** como **Sim**.

    ![Permissões do Azure AD](./media/tutorial-migrate-physical-virtual-machines/aad.png)

> [!NOTE]
> Essa é uma configuração padrão que não é confidencial. [Saiba mais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Atribuir função de Desenvolvedor de Aplicativos 

O locatário/administrador global pode atribuir a função de Desenvolvedor de Aplicativos a uma conta. [Saiba mais](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="assign-permissions-to-create-key-vault"></a>Atribuir permissões para criar o cofre de chaves

Atribua permissões de atribuição de função no grupo de recursos no qual o projeto de Migrações para Azure reside, da seguinte maneira:

1. No grupo de recursos no portal do Azure, selecione **Controle de acesso (IAM).**
2. Em **Verificar acesso**, localize a conta relevante e clique nela para exibir as permissões. Você precisa das permissões de **Proprietário** (ou **Colaborador** e **Administrador de Acesso do Usuário**).
3. Se você não tiver as permissões necessárias, solicite-as do proprietário do grupo de recursos. 

## <a name="prepare-for-migration"></a>Preparar para a migração

### <a name="check-machine-requirements-for-migration"></a>Verificar os requisitos do computador para a migração

Verifique se os computadores estão em conformidade com os requisitos da migração para o Azure. 

> [!NOTE]
> A migração baseada em agente com a Migração de Servidor de Migrações para Azure baseia-se nos recursos do serviço do Azure Site Recovery. Alguns requisitos podem ser vinculados à documentação do Site Recovery.

1. [Verifique](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) os requisitos do servidor VMware.
2. [Verifique](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) os requisitos de suporte da VM para a migração.
3. Verifique as configurações da VM. As VMs locais que você replica para o Azure devem cumprir os [requisitos de VM do Azure](migrate-support-matrix-vmware.md#azure-vm-requirements).


### <a name="prepare-a-machine-for-the-replication-appliance"></a>Preparar um computador para o dispositivo de replicação

A Migração de Servidor de Migrações para Azure usa um dispositivo de replicação para replicar computadores para o Azure. O dispositivo de replicação executa os seguintes componentes.

- **Servidor de configuração**: O servidor de configuração coordena a comunicação entre o ambiente local e o Azure e gerencia a replicação de dados.
- **Servidor de processo**: O servidor de processo atua como um gateway de replicação. Ele recebe dados de replicação, otimiza-os com caching, compactação e criptografia e os envia para uma conta de armazenamento em cache no Azure. 

Antes de começar, você precisa preparar um computador com Windows Server 2016 para hospedar o dispositivo de replicação. O computador deve estar em conformidade com [estes requisitos](migrate-support-matrix-vmware.md#agent-based-migration-replication-appliance-requirements).


## <a name="add-the-azure-migrate-server-migration-tool"></a>Adicionar a ferramenta Migração de Servidor das Migrações para Azure

Configure um projeto de Migrações para Azure e adicione a ferramenta de Migração de Servidor de Migrações para Azure para ele.

1. No portal do Azure > **Todos os serviços**, pesquise **Migrações para Azure**.
2. Em **Serviços**, selecione **Migrações para Azure**.
3. Em **Visão Geral**, clique em **Avaliar emigrar servidores**.
4. Em **Descobrir, avaliar e migrar servidores**, clique em **Avaliar e migrar servidores**.

    ![Descobrir e avaliar servidores](./media/tutorial-migrate-physical-virtual-machines/assess-migrate.png)

5. Em **Descobrir, avaliar e migrar servidores**, clique em **Adicionar ferramentas**.
6. Em **Migrar projeto**, selecione sua assinatura do Azure e crie um grupo de recursos, caso não tenha um.
7. Em **Detalhes do Projeto**, especifique o nome do projeto e a geografia em que você deseja criar o projeto e clique em **Avançar**

    ![Criar um projeto das Migrações para Azure](./media/tutorial-migrate-physical-virtual-machines/migrate-project.png)

    Você pode criar um projeto de Migrações para Azure em qualquer uma dessas regiões.

    **Geografia** | **Região**
    --- | ---
    Ásia | Sudeste Asiático
    Europa | Europa Setentrional ou Europa Ocidental
    Estados Unidos | Leste dos EUA ou Centro-oeste dos EUA

    A localização geográfica especificada para o projeto só é usada para armazenar os metadados coletados das VMs locais. Você pode selecionar qualquer região de destino para a migração real.
8. Em **Selecionar ferramenta de avaliação**, selecione **Ignorar a adição de uma ferramenta de avaliação por enquanto** > **Avançar**.
9. Em **Selecionar ferramenta de migração**, selecione **Migrações para Azure: Migração de Servidor** > **Avançar**.
10. Em **Examinar + adicionar ferramentas**, examine as configurações e clique em **Adicionar ferramentas**
11. Depois de adicionar a ferramenta, ela aparecerá no projeto de Migrações para Azure > **Servidores** > **Ferramentas de migração**.

## <a name="set-up-the-replication-appliance"></a>Configurar o dispositivo de replicação

A primeira etapa da migração é configurar o dispositivo de replicação. Você baixa o arquivo do instalador para o dispositivo e o executa no [computador que você preparou.](#prepare-a-machine-for-the-replication-appliance) Depois de instalar o dispositivo, registre-o com a Migração de Servidor de Migrações para Azure.


### <a name="download-the-replication-appliance-installer"></a>Baixar o instalador do dispositivo de replicação

1. No projeto de Migrações para Azure > **Servidores**, em ***Migrações para Azure: Migração de Servidor**, clique em **Descobrir**.

    ![Descobrir VMs](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

3. Em **Descobrir computadores** > **São seus computadores virtualizados?** , clique em **Não virtualizado/outro.**
4. Em **Região de destino**, selecione a região do Azure para a qual deseja migrar os computadores.
5. Selecione **Confirme se a região de destino da migração é nome-da-região**.
6. Clique em **Criar recursos**. Isso criará um cofre do Azure Site Recovery em segundo plano.
    - Se você já configurou a migração com a Migração de Servidor de Migrações para Azure, a opção de destino não poderá ser configurada, pois os recursos foram configurados anteriormente.
    - Você não poderá alterar a região de destino desse projeto depois de clicar nesse botão.
    - Todas as migrações seguintes serão feitas para essa região.

7. Em **Deseja instalar um novo dispositivo de replicação?** , selecione **Instalar um dispositivo de replicação**.
9. Em **baixar e instalar o software do dispositivo de replicação**, baixe o instalador do dispositivo e a chave de registro. Você precisa da chave para registrar o dispositivo. A chave é válida por cinco dias após ser baixada.

    ![Baixar provedor](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

10. Copie o arquivo de instalação do dispositivo e o arquivo de chave para o computador Windows Server 2016 que você criou para o dispositivo.
11. Execute o arquivo de instalação do dispositivo de replicação conforme descrito no próximo procedimento.
12. Depois que o dispositivo for reiniciado após a instalação, em **Descobrir computadores**, selecione o novo dispositivo em **Selecionar Servidor de Configuração** e clique em **Finalizar o registro**. Finalizar o registro executa algumas tarefas finais para preparar o dispositivo de replicação.

    ![Finalizar registro](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

Podem ser necessários até 15 minutos após a finalização do registro até que os computadores descobertos sejam exibidos na Migração de Servidor das Migrações para Azure. À medida que as VMs são descobertas, a contagem de **Servidores descobertos** aumenta.

![Servidores descobertos](./media/tutorial-migrate-physical-virtual-machines/discovered-servers.png)


## <a name="install-the-mobility-service"></a>Instalar o serviço de Mobilidade

Nos computadores que você deseja migrar, você precisa instalar o agente do serviço de Mobilidade. Os instaladores do agente estão disponíveis no dispositivo de replicação. Você encontra o instalador correto e instala o agente em cada computador que deseja migrar. Faça isso da seguinte forma:

1. Entre no dispositivo de replicação.
2. Navegue até **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository**.
3. Localize o instalador para a versão e o sistema operacional do computador. Examine os [sistemas operacionais compatíveis](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines). 
4. Copie o arquivo do instalador para o computador que você deseja migrar.
5. Verifique se você tem a frase secreta gerada ao implantar o dispositivo.
    - Armazene o arquivo em um arquivo de texto temporário no computador.
    - Você pode obter a frase secreta no dispositivo de replicação. Na linha de comando, execute **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe-v** para exibir a frase secreta atual.
    - Não gere novamente a frase secreta. Isso interromperá a conectividade e você precisará registrar o dispositivo de replicação novamente.


### <a name="install-on-windows"></a>Instalar no Windows

1. Extraia o conteúdo do arquivo do instalador para uma pasta local (por exemplo, C:\Temp) no computador da seguinte maneira:

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```
2. Execute o Instalador do Serviço de Mobilidade:
    ```
   UnifiedAgent.exe /Role "MS" /Silent
    ```
3. Registre o agente com o dispositivo de replicação:
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="install-on-linux"></a>Instalar no Linux

1. Extraia o conteúdo do tarball do instalador para uma pasta local (por exemplo, /tmp/MobSvcInstaller) no computador da seguinte maneira:
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```
2. Execute o script do instalador:
    ```
    sudo ./install -r MS -q
    ```
3. Registre o agente com o dispositivo de replicação:
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="replicate-machines"></a>Replicar computadores

1. No projeto das Migrações para Azure > **Servidores**, **Migrações para Azure: Migração de Servidor**, clique em **Replicar**.

    ![Replicar VMs](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. Em **Replicar**, > **Configurações de origem** > **Seus computadores estão virtualizados?** , selecione **Sim, com o VMware vSphere**.
3. Em **Dispositivo local**, selecione o nome do dispositivo de Migrações para Azure que você configurou.
4. No **vCenter Server**, especifique o nome do vCenter Server que gerencia as VMs ou do servidor vSphere no qual as VMs estão hospedadas.
5. Em **Servidor de Processo**, selecione o nome do dispositivo de replicação.
6. Em **Credenciais de convidado**, especifique uma conta do administrador de VM que será usada para a instalação por push do serviço de Mobilidade. Neste tutorial, estamos instalando o serviço de Mobilidade manualmente, assim, você pode adicionar qualquer conta fictícia. Em seguida, clique em **Próximo: Máquinas virtuais**.

    ![Replicar VMs](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)

7. Em **Máquinas Virtuais**, em **Importar configurações de migração de uma avaliação?** , deixe a configuração padrão, **Não, vou especificar as configurações de migração manualmente**.
8. Verifique cada VM que você deseja migrar. Em seguida, clique em **Próximo: configurações de destino**.

    ![Selecione as máquinas virtuais](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)


9. Em **Configurações de destino**, selecione a assinatura e a região de destino para a qual você migrará e especifique o grupo de recursos no qual as VMs do Azure residirão após a migração.
10. Em **Rede Virtual**, selecione a VNet/sub-rede do Azure na qual as VMs do Azure serão ingressadas após a migração.
11. Em **Benefício Híbrido do Azure**:

    - Selecione **Não** se não desejar aplicar o Benefício Híbrido do Azure. Em seguida, clique em **Próximo**.
    - Selecione **Sim** se você tiver computadores Windows Server cobertos com assinaturas ativas do Software Assurance ou do Windows Server e quiser aplicar o benefício aos computadores que estão sendo migrados. Em seguida, clique em **Próximo**.

    ![Configurações de destino](./media/tutorial-migrate-physical-virtual-machines/target-settings.png)

12. Em **Computação**, examine o nome da VM, o tamanho, o tipo de disco do sistema operacional e o conjunto de disponibilidade. As VMs devem estar em conformidade com os [requisitos do Azure](migrate-support-matrix-vmware.md#azure-vm-requirements).

    - **Tamanho da VM**: Por padrão, a Migração de Servidor de Migrações para Azure escolhe um tamanho com base na correspondência mais próxima na assinatura do Azure. Como alternativa, escolha um tamanho manual em **Tamanho da VM do Azure**. 
    - **Disco do SO**: especifique o disco do sistema operacional (inicialização) para a VM. O disco do sistema operacional é o disco que tem o carregador de inicialização e o instalador do sistema operacional. 
    - **Conjunto de disponibilidade**: se a VM deve estar em um conjunto de disponibilidade do Azure após a migração, especifique-o. O conjunto precisa estar no grupo de recursos de destino especificado para a migração.

    ![Configurações de computação](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

13. Em **Discos**, especifique se os discos da VM devem ser replicados para o Azure e selecione o tipo de disco (discos gerenciados HDD/SSD standard ou premium) no Azure. Em seguida, clique em **Próximo**.
    - Você pode excluir discos da replicação.
    - Se você excluir os discos, eles não estarão presentes na VM do Azure após a migração. 

    ![Configurações de disco](./media/tutorial-migrate-physical-virtual-machines/disks.png)


14. Em **Examinar e iniciar a replicação**, examine as configurações e clique em **Replicar** para iniciar a replicação inicial dos servidores.

> [!NOTE]
> É possível atualizar as configurações de replicação a qualquer momento antes do início da replicação em **Gerenciar** > **Computadores em replicação**. Não é possível alterar as configurações após o início da replicação.



## <a name="track-and-monitor"></a>Acompanhar e monitorar

- Quando você clica em **Replicar**, um trabalho Iniciar Replicação é iniciado. 
- Quando o trabalho Iniciar Replicação é concluído com êxito, os computadores começam sua replicação inicial para o Azure.
- Depois que a replicação inicial é concluída, a replicação delta é iniciada. As alterações incrementais em discos locais são replicadas periodicamente para os discos de réplica no Azure.


É possível acompanhar o status do trabalho nas notificações do portal.

É possível monitorar o status de replicação clicando em **Replicando servidores** em **Migrações para Azure: Migração de Servidor**.
![Monitorar a replicação](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>Execute um teste de migração


Quando a replicação delta é iniciada, é possível executar uma migração de teste para as VMs antes de executar uma migração completa para o Azure. É altamente recomendável que você faça isso pelo menos uma vez para cada computador antes de migrá-lo.

- A execução de uma migração de teste verifica se a migração funcionará conforme o esperado, sem afetar os computadores locais, que permanecem operacionais e continuam sendo replicados. 
- A migração de teste simula a migração criando uma VM do Azure com o uso dos dados replicados (geralmente, fazendo a migração para uma VNet de não produção em sua assinatura do Azure).
- É possível usar a VM do Azure de teste replicada para validar a migração, executar testes de aplicativo e resolver problemas antes da migração completa.

Faça uma migração de teste da seguinte maneira:


1. Em **Metas de migração** > **Servidores** > **Migrações para Azure: Migração de Servidor**, clique em **Testar servidores migrados**.

     ![Testar servidores migrados](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. Clique com o botão direito do mouse na VM a ser testada e clique em **Migração de teste**.

    ![Migração de teste](./media/tutorial-migrate-physical-virtual-machines/test-migrate.png)

3. Em **Migração de Teste**, selecione a VNet do Azure na qual a VM do Azure estará localizada após a migração. Recomendamos que você use uma VNet que não seja de produção.
4. O trabalho **Migração de teste** é iniciado. Monitore o trabalho nas notificações do portal.
5. Após a conclusão da migração, veja a VM do Azure migrada em **Máquinas Virtuais** no portal do Azure. O nome do computador tem o sufixo **-Test**.
6. Depois que o teste for realizado, clique com o botão direito do mouse na VM do Azure em **Replicando computadores** e clique em **Limpar migração de teste**.

    ![Limpar migração](./media/tutorial-migrate-physical-virtual-machines/clean-up.png)


## <a name="migrate-vms"></a>Migrar VMs

Depois de verificar se a migração de teste funciona conforme o esperado, você poderá migrar os computadores locais.

1. No projeto das Migrações para Azure > **Servidores** > **Migrações para Azure: Migração de Servidor**, clique em **Replicando servidores**.

    ![Replicando servidores](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

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
    - Considere implantar o [Gerenciamento de Custos do Azure](https://docs.microsoft.com/azure/cost-management/overview) para monitorar o uso de recursos e os gastos.


## <a name="next-steps"></a>Próximas etapas

Investigue a [jornada de migração na nuvem](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) no Cloud Adoption Framework do Azure.

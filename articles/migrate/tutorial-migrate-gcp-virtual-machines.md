---
title: Descobrir, avaliar e migrar instâncias de VM do GCP (Google Cloud Platform) para o Azure
description: Este artigo descreve como migrar VMs do GCP para o Azure com as Migrações para Azure.
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: tutorial
ms.date: 08/19/2020
ms.custom: MVC
ms.openlocfilehash: 12b8e30b0107b6b008cbd6467ada7c2d44f5e6d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98871631"
---
# <a name="discover-assess-and-migrate-google-cloud-platform-gcp-vms-to-azure"></a>Descobrir, avaliar e migrar VMs do GCP (Google Cloud Platform) para o Azure

Este tutorial mostra como descobrir, avaliar e migrar VMs (máquinas virtuais) do GCP (Google Cloud Platform) para VMs do Azure usando as Migrações para Azure: Avaliação de Servidor e Migrações para Azure: Ferramentas de Migração do Servidor.

Neste tutorial, você aprenderá a:
> [!div class="checklist"]
>
> * Verificar os pré-requisitos para a migração.
> * Preparar recursos do Azure com as Migrações para Azure: Migração de Servidor. Configurar permissões para sua conta do Azure e recursos para trabalhar com as Migrações para Azure.
> * Preparar instâncias de VM do GCP para migração.
> * Adicionar a ferramenta Migrações para Azure: Migração de Servidor no hub de Migrações para Azure.
> * Configurar o dispositivo de replicação e implantar o servidor de configuração.
> * Instalar o serviço Mobilidade nas VMs do GCP que você deseja migrar.
> * Habilite a replicação para VMs.
> * Acompanhe e monitore o status da replicação. 
> * Executar uma migração de teste para verificar se tudo está funcionando conforme o esperado.
> * Executar uma migração completa para o Azure.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="discover-and-assess"></a>Descobrir e avaliar

Antes de migrar para o Azure, recomendamos que você execute uma descoberta e avaliação de migração da VM. Essa avaliação ajuda a dimensionar o tamanho correto de suas VMs do GCP que serão migradas para o Azure, bem como estimar os possíveis custos de execução do Azure.

Configure uma avaliação da seguinte maneira:

1. Siga o [tutorial](./tutorial-discover-gcp.md) para configurar o Azure e preparar suas VMs do GCP para uma avaliação. Observe que:

    - As Migrações para Azure usam a autenticação de senha ao descobrir instâncias de VM do GCP. As instâncias do GCP não dão suporte para a autenticação de senha por padrão. Para descobrir, habilite a autenticação de senha.
        - Para computadores Windows, permita a porta 5985 (HTTP) do WinRM. Isso autorizará as chamadas remotas do WMI.
        - Para computadores Linux:
            1. Entre em cada computador Linux.
            2. Abra o arquivo sshd_config: vi /etc/ssh/sshd_config
            3. No arquivo, localize a linha **PasswordAuthentication** e altere o valor para **yes**.
            4. Salve o arquivo e feche-o. Reinicie o serviço ssh.
    - Se você estiver usando um usuário raiz para descobrir suas VMs do Linux, verifique se o logon raiz é permitido nas VMs.
        1. Entrar em cada computador Linux
        2. Abra o arquivo sshd_config: vi /etc/ssh/sshd_config
        3. No arquivo, localize a linha **PermitRootLogin** e altere o valor para **yes**.
        4. Salve o arquivo e feche-o. Reinicie o serviço ssh.

2. Então, siga este [tutorial](./tutorial-assess-gcp.md) para configurar um projeto e um dispositivo das Migrações para Azure a fim de descobrir e avaliar suas VMs do GCP.

Embora seja recomendável experimentar uma avaliação, executar uma avaliação não é uma etapa obrigatória para poder migrar VMs.



## <a name="prerequisites"></a>Pré-requisitos 

- Verifique se as VMs do GCP que você quer migrar estão executando uma versão de sistema operacional compatível. As VMs do GCP são tratadas como computadores físicos para fins de migração. Examine os [sistemas operacionais e as versões de kernel com suporte](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) para o fluxo de trabalho de migração do servidor físico. Você pode usar comandos padrão como *hostnamectl* ou *uname -a* para verificar as versões do sistema operacional e do kernel para suas VMs do Linux.  Recomendamos que você execute uma migração de teste para validar se a VM funciona conforme o esperado antes de prosseguir com a migração propriamente dita.
- Verifique se suas VMs do GCP estão em conformidade com as [configurações compatíveis](./migrate-support-matrix-physical-migration.md#physical-server-requirements) com a migração para o Azure.
- Verifique se as VMs do GCP que você replica para o Azure estão em conformidade com os [requisitos de VM do Azure.](./migrate-support-matrix-physical-migration.md#azure-vm-requirements)
- É necessário fazer algumas alterações às VMs antes de migrá-las para o Azure.
    - Para alguns sistemas operacionais, as Migrações para Azure fazem essas alterações automaticamente.
    - É importante fazer essas alterações antes de iniciar a migração. Se você migrar a VM antes de fazer a alteração, ela não poderá ser inicializada no Azure.
Examine as alterações para [Windows](prepare-for-migration.md#windows-machines) e [Linux](prepare-for-migration.md#linux-machines) que você precisa fazer.

### <a name="prepare-azure-resources-for-migration"></a>Preparar recursos do Azure para migração

Preparar o Azure para a migração com a ferramenta Migrações para Azure: Ferramenta de migração de servidor.

**Tarefa** | **Detalhes**
--- | ---
**Criar um projeto de Migrações para Azure** | Sua conta do Azure precisa de permissões de Colaborador ou de Proprietário para [criar um projeto](./create-manage-projects.md).
**Verificar as permissões para sua conta do Azure** | Sua conta do Azure precisa de permissões para criar uma VM e gravar em um disco gerenciado do Azure.

### <a name="assign-permissions-to-create-project"></a>Atribuir permissões para criar o projeto

1. No portal do Azure, abra a assinatura e selecione **Controle de acesso (IAM).**
2. Em **Verificar acesso**, localize a conta relevante e clique nela para exibir as permissões.
3. Você deve ter permissões de **Colaborador** e **Proprietário**.
    - Se você acaba de criar uma conta gratuita do Azure, você é o proprietário da assinatura.
    - Se você não for o proprietário da assinatura, trabalhe com o proprietário para atribuir a função.

### <a name="assign-azure-account-permissions"></a>Atribuir permissões de conta do Azure

Atribua a função Colaborador da Máquina Virtual à conta do Azure. Isso fornece permissões para:

- Criar uma VM no grupo de recursos selecionado.
- Criar uma VM na rede virtual selecionada.
- Gravar em um disco gerenciado do Azure. 

### <a name="create-an-azure-network"></a>Criar uma rede do Azure

[Configurar](../virtual-network/manage-virtual-network.md#create-a-virtual-network) uma VNet (rede virtual) do Azure. Quando você replica para o Azure, as VMs do Azure são criadas e ingressadas na VNet do Azure que você especifica ao configurar a migração.

## <a name="prepare-gcp-instances-for-migration"></a>Preparar instâncias do GCP para migração

Para se preparar para a migração do GCP para o Azure, você precisa preparar e implantar um dispositivo de replicação para migração.

### <a name="prepare-a-machine-for-the-replication-appliance"></a>Preparar um computador para o dispositivo de replicação

Migrações para Azure: Migração de Servidor usa um dispositivo de replicação para replicar computadores para o Azure. O dispositivo de replicação executa os seguintes componentes.

- **Servidor de configuração**: o servidor de configuração coordena a comunicação entre as VMs do GCP e o Azure, além de gerenciar a replicação de dados.
- **Servidor de processo**: O servidor de processo atua como um gateway de replicação. Ele recebe dados de replicação, otimiza-os com caching, compactação e criptografia e envia-os para uma conta de armazenamento em cache no Azure.

Prepare-se para implantação do dispositivo, conforme mostrado a seguir:

- Configure uma VM do GCP separada para hospedar o dispositivo de replicação. Essa instância precisa estar executando o Windows Server 2012 R2 ou Windows Server 2016. [Examine](./migrate-replication-appliance.md#appliance-requirements) os requisitos de hardware, software e rede para o dispositivo.
- O dispositivo não deve ser instalado em uma VM de origem que você deseja replicar nem no dispositivo de avaliação e descoberta das Migrações para Azure que pode ter sido instalado antes. Ele deve ser implantado em uma VM diferente.
- As VMs do GCP de origem a serem migradas devem ter uma linha de visão de rede para o dispositivo de replicação. Configure as regras de firewall necessárias para habilitar isso. É recomendável que o dispositivo de replicação seja implantado na mesma rede de VPC que as VMs de origem a serem migradas. Se o dispositivo de replicação precisar estar em um VPC diferente, os VPCs precisarão ser conectados por meio de emparelhamento de VPC.
- As VMs do GCP de origem se comunicam com o dispositivo de replicação nas portas HTTPS 443 (orquestração de canal de controle) e TCP 9443 (transporte de dados) de entrada para gerenciamento de replicação e transferência de dados de replicação. O dispositivo de replicação, por sua vez, orquestra e envia dados de replicação para o Azure pela porta HTTPS 443 de saída. Para configurar essas regras, edite as regras de entrada/saída do grupo de segurança com as informações de IP de origem e as portas apropriadas.

   ![Regras de firewall do GCP](./media/tutorial-migrate-gcp-virtual-machines/gcp-firewall-rules.png)
     
 
   ![Editar regras de firewall](./media/tutorial-migrate-gcp-virtual-machines/gcp-edit-firewall-rule.png)

- O dispositivo de replicação usa o MySQL. Examine as [opções](migrate-replication-appliance.md#mysql-installation) para instalar o MySQL no dispositivo.
- Examine as URLs do Azure necessárias para que o dispositivo de replicação acesse as nuvens [públicas](migrate-replication-appliance.md#url-access) e [governamentais](migrate-replication-appliance.md#azure-government-url-access).

## <a name="set-up-the-replication-appliance"></a>Configurar o dispositivo de replicação

A primeira etapa da migração é configurar o dispositivo de replicação. Para configurar o dispositivo para migração de VMs do GCP, é necessário baixar o arquivo do instalador do dispositivo e executá-lo na [VM que você preparou](#prepare-a-machine-for-the-replication-appliance).

### <a name="download-the-replication-appliance-installer"></a>Baixar o instalador do dispositivo de replicação

1. No projeto das Migrações para Azure > **Servidores**, em **Migrações para Azure: Migração de Servidor**, clique em **Descobrir**.

    ![Descobrir VMs](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

2. Em **Descobrir computadores** > **São seus computadores virtualizados?** , clique em **Não virtualizado/outro.**
3. Em **Região de destino**, selecione a região do Azure para a qual deseja migrar os computadores.
4. Selecione **Confirme se a região de destino da migração é <nome-da-região>** .
5. Clique em **Criar recursos**. Isso criará um cofre do Azure Site Recovery em segundo plano.
    - Se você já configurou a migração com a Migração de Servidor de Migrações para Azure, a opção de destino não poderá ser configurada, pois os recursos foram configurados anteriormente.
    - Você não poderá alterar a região de destino desse projeto depois de clicar nesse botão.
    - Para migrar suas VMs para uma região diferente, você precisará criar um projeto diferente das Migrações para Azure.

6. Em **Deseja instalar um novo dispositivo de replicação?** , selecione **Instalar um dispositivo de replicação**.
7. Em **baixar e instalar o software do dispositivo de replicação**, baixe o instalador do dispositivo e a chave de registro. Você precisa da chave para registrar o dispositivo. A chave é válida por cinco dias após ser baixada.

    ![Baixar provedor](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

8. Copie o arquivo de instalação do dispositivo e o arquivo de chave para a VM do GCP do Windows Server 2012 ou 2016 que você criou para o dispositivo de replicação.
9. Execute o arquivo de instalação do dispositivo de replicação conforme descrito no próximo procedimento.  
    9.1. Em **Antes de começar**, selecione **Instalar o servidor de configuração e o servidor de processo** e, em seguida, selecione **Avançar**.   
    9.2 Em **Licença de Software de Terceiros**, selecione **Aceito o contrato de licença de terceiros** e clique em **Avançar**.   
    9.3 Em **Registro**, selecione **Procurar** e navegue até o local em que você colocou o arquivo da chave de registro do cofre. Selecione **Avançar**.  
    9.4 Em **Configurações da Internet**, selecione **Conectar ao Azure Site Recovery sem um servidor proxy** e clique em **Avançar**.  
    9.5 A página **Verificação de Pré-requisitos** executa verificações de vários itens. Quando terminar, selecione **Avançar**.  
    9.6 Em **Configuração do MySQL**, forneça as senhas necessárias e clique em **Avançar**.  
    9.7 Em **Detalhes do ambiente**, selecione **Não**. Não é necessário proteger suas VMs. Em seguida, selecione **Avançar**.  
    9.8 Em **Localização de Instalação**, selecione **Avançar** para aceitar o padrão.  
    9.9 Em **Seleção de Rede**, selecione **Avançar** para aceitar o padrão.  
    9.10 Em **Resumo**, selecione **Instalar**.   
    9.11 O **Progresso da Instalação** mostra informações sobre o processo de instalação. Quando terminar, selecione **Concluir**. Uma janela exibe uma mensagem sobre uma reinicialização. Selecione **OK**.   
    9.12 Em seguida, uma janela exibe uma mensagem sobre a frase secreta de conexão do servidor de configuração. Copie a frase secreta para a área de transferência e salve-a em um arquivo de texto temporário nas VMs de origem. Você precisará dessa frase secreta mais tarde, durante o processo de instalação do Serviço de Mobilidade.
10. Após a conclusão da instalação, o Assistente de configuração de dispositivo será iniciado automaticamente (você também poderá iniciar o assistente manualmente usando o atalho cspsconfigtool criado na área de trabalho do dispositivo). Use a guia Gerenciar Contas do assistente para adicionar detalhes de conta a serem usados para a instalação por push do serviço de Mobilidade. Neste tutorial, vamos instalar manualmente o Serviço de Mobilidade nas VMs de origem a serem replicadas, portanto, crie uma conta fictícia nesta etapa e continue. É possível fornecer os seguintes detalhes para criação de uma conta fictícia: "convidado" como o nome amigável, "nome de usuário" como o nome de usuário e "senha" como a senha da conta. Você usará essa conta fictícia no estágio Habilitar Replicação. 
11. Depois que o dispositivo for reiniciado após a instalação, em **Descobrir computadores**, selecione o novo dispositivo em **Selecionar Servidor de Configuração** e clique em **Finalizar o registro**. Finalizar o registro executa algumas tarefas finais para preparar o dispositivo de replicação.

    ![Finalizar registro](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

## <a name="install-the-mobility-service"></a>Instalar o serviço de Mobilidade

Um agente do serviço Mobilidade deve ser instalado nas VMs do GCP de origem a serem migradas. Os instaladores do agente estão disponíveis no dispositivo de replicação. Você encontra o instalador correto e instala o agente em cada computador que deseja migrar. Proceda da seguinte maneira:

1. Entre no dispositivo de replicação.
2. Navegue até **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository**.
3. Localize o instalador para a versão e o sistema operacional das VMs do GCP de origem. Examine os [sistemas operacionais compatíveis](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines).
4. Copie o arquivo do instalador para a VM do GCP de origem que você deseja migrar.
5. É preciso que você mantenha o arquivo de texto com a frase secreta salvo que foi criado quando você instalou o dispositivo de replicação.
    - Se você tiver esquecido de salvar a frase secreta, poderá vê-la no dispositivo de replicação com esta etapa. Na linha de comando, execute **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe-v** para exibir a frase secreta atual.
    - Agora, copie esta frase secreta para a área de transferência e salve-a em um arquivo de texto temporário nas VMs de origem.

### <a name="installation-guide-for-windows-gcp-vms"></a>Guia de instalação para VMs do GCP do Windows

1. Extraia o conteúdo do arquivo do instalador para uma pasta local (por exemplo, C:\Temp) na VM do GCP, da seguinte maneira:

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

### <a name="installation-guide-for-linux-gcp-vms"></a>Guia de instalação para VMs do Linux do GCP

1. Extraia o conteúdo do tarball do instalador para uma pasta local (por exemplo, /tmp/MobSvcInstaller) na VM do GCP, da seguinte maneira:
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

## <a name="enable-replication-for-gcp-vms"></a>Habilitar a replicação para VMs do GCP

> [!NOTE]
> Por meio do portal, você pode adicionar até dez VMs para replicação de uma só vez. Para replicar mais VMs simultaneamente, você pode adicioná-las em lotes de dez.

1. No projeto das Migrações para Azure > **Servidores**, **Migrações para Azure: Migração de Servidor**, clique em **Replicar**.

    ![Replicar VMs](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. Em **Replicar**, > **Configurações de origem** > **Os computadores estão virtualizados?** , selecione **Não virtualizados/Outro**.
3. Em **Dispositivo local**, selecione o nome do dispositivo de Migrações para Azure que você configurou.
4. Em **Servidor de Processo**, selecione o nome do dispositivo de replicação. 
5. Em **Credenciais de convidado**, selecione a conta fictícia criada anteriormente durante a [configuração do instalador de replicação](#download-the-replication-appliance-installer) para instalar o serviço Mobilidade manualmente (a instalação por push não é compatível). Em seguida, clique em **Próximo: Máquinas virtuais**.   
 
    ![Replicar configurações](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)
6. Em **Máquinas Virtuais**, em **Importar configurações de migração de uma avaliação?** , deixe a configuração padrão, **Não, vou especificar as configurações de migração manualmente**.
7. Verifique cada VM que você deseja migrar. Em seguida, clique em **Próximo: configurações de destino**.

    ![Selecione as máquinas virtuais](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)

8. Em **Configurações de destino**, selecione a assinatura e a região de destino para a qual você migrará e especifique o grupo de recursos no qual as VMs do Azure residirão após a migração.
9. Em **Rede Virtual**, selecione a VNet/sub-rede do Azure na qual as VMs do Azure serão ingressadas após a migração.
10. Em **Opções de disponibilidade**, selecione:
    -  Zona de Disponibilidade para fixar o computador migrado para uma Zona de Disponibilidade específica na região. Use essa opção para distribuir servidores que formam uma camada de aplicativo de vários nós entre Zonas de Disponibilidade diferentes. Se você selecionar essa opção, precisará especificar a zona de disponibilidade a ser usada para cada computador selecionado na guia Computação. Essa opção só estará disponível se a região de destino selecionada para a migração der suporte a Zonas de Disponibilidade
    -  Conjunto de Disponibilidade para colocar o computador migrado em um conjunto de disponibilidade. O grupo de recursos de destino selecionado precisa ter um ou mais conjuntos de disponibilidade para que possa usar essa opção.
    - Nenhuma opção de redundância de infraestrutura será necessária se você não precisar de nenhuma dessas configurações de disponibilidade para os computadores migrados.
11. Em **Tipo de criptografia de disco**, selecione:
    - Criptografia em repouso com chave de criptografia gerenciada pela plataforma
    - Criptografia em repouso com a chave gerenciada pelo cliente
    - Criptografia dupla com chaves gerenciadas por plataforma e gerenciadas pelo cliente

   > [!NOTE]
   > Para replicar VMs com a CMK, você precisará [criar um conjunto de criptografia de disco](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set) no grupo de recursos de destino. Um objeto de conjunto de criptografia de disco mapeia o Managed Disks para um Key Vault que contém a CMK a ser usada para a SSE.
  
12. Em **Benefício Híbrido do Azure**:

    - Selecione **Não** se não desejar aplicar o Benefício Híbrido do Azure. Em seguida, clique em **Próximo**.
    - Selecione **Sim** se você tiver computadores Windows Server cobertos com assinaturas ativas do Software Assurance ou do Windows Server e quiser aplicar o benefício aos computadores que estão sendo migrados. Em seguida, clique em **Próximo**.

    ![Configurações de destino](./media/tutorial-migrate-vmware/target-settings.png)

13. Em **Computação**, examine o nome da VM, o tamanho, o tipo de disco do SO e a configuração de disponibilidade (se selecionado na etapa anterior). As VMs devem estar em conformidade com os [requisitos do Azure](migrate-support-matrix-physical-migration.md#azure-vm-requirements).

    - **Tamanho da VM**: se você estiver usando recomendações de avaliação, o menu suspenso de tamanho da VM mostrará o tamanho recomendado. Caso contrário, as Migrações para Azure escolherão um tamanho com base na correspondência mais próxima na assinatura do Azure. Como alternativa, escolha um tamanho manual em **Tamanho da VM do Azure**.
    - **Disco do SO**: especifique o disco do sistema operacional (inicialização) para a VM. O disco do sistema operacional é o disco que tem o carregador de inicialização e o instalador do sistema operacional.
    - **Zona de Disponibilidade**: especifique a zona de disponibilidade a ser usada.
    - **Conjunto de disponibilidade**: especifique o conjunto de disponibilidade a ser usado.

![Configurações de computação](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

14. Em **Discos**, especifique se os discos da VM devem ser replicados para o Azure e selecione o tipo de disco (discos gerenciados HDD/SSD standard ou premium) no Azure. Em seguida, clique em **Próximo**.
    - Você pode excluir discos da replicação.
    - Se você excluir os discos, eles não estarão presentes na VM do Azure após a migração. 

    ![Configurações de disco](./media/tutorial-migrate-physical-virtual-machines/disks.png)

15. Em **Examinar e iniciar a replicação**, examine as configurações e clique em **Replicar** para iniciar a replicação inicial dos servidores.

> [!NOTE]
> É possível atualizar as configurações de replicação a qualquer momento antes do início da replicação em **Gerenciar** > **Computadores em replicação**. Não é possível alterar as configurações após o início da replicação.

## <a name="track-and-monitor-replication-status"></a>Acompanhar e monitorar o status da replicação

- Quando você clica em **Replicar**, um trabalho Iniciar Replicação é iniciado.
- Quando o trabalho Iniciar Replicação é concluído com êxito, as VMs começam sua replicação inicial para o Azure.
- Depois que a replicação inicial é concluída, a replicação delta é iniciada. As alterações incrementais em discos de VMs do GCP são replicadas periodicamente para os discos de réplica no Azure.

É possível acompanhar o status do trabalho nas notificações do portal.

É possível monitorar o status de replicação clicando em **Replicando servidores** em **Migrações para Azure: Migração de Servidor**.  

![Monitorar a replicação](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>Execute um teste de migração

Quando a replicação delta é iniciada, é possível executar uma migração de teste para as VMs antes de executar uma migração completa para o Azure. É altamente recomendável realizar a migração de teste, que fornece uma oportunidade para descobrir possíveis problemas e corrigi-los antes de prosseguir com a migração propriamente dita. É recomendável que você faça isso pelo menos uma vez para cada VM antes de migrá-la.

- A execução de uma migração de teste verifica se a migração funcionará conforme o esperado, sem afetar as VMs do GCP, que permanecem operacionais e continuam sendo replicadas.
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


## <a name="migrate-gcp-vms"></a>Migrar VMs do GCP

Depois de verificar se a migração de teste funciona conforme o esperado, você poderá migrar as VMs do GCP.

1. No projeto das Migrações para Azure > **Servidores** > **Migrações para Azure: Migração de Servidor**, clique em **Replicando servidores**.

    ![Replicando servidores](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. Em **Replicando computadores**, clique com o botão direito do mouse na VM > **Migrar**.
3. Em **Migrar** > **Desligar máquinas virtuais e realizar uma migração planejada sem perda de dados**, selecione **Sim** > **OK**.
    - Se você não quiser desligar a VM, selecione **Não**.
4. Um trabalho de migração é iniciado para a VM. Você pode ver o status do trabalho clicando no ícone de sino de notificação no canto superior direito da página do portal ou acessando a página de trabalhos da ferramenta de Migração de Servidor (clique em Visão geral no bloco de ferramentas e depois em Selecionar Trabalhos no menu à esquerda).
5. Após a conclusão do trabalho, você poderá ver e gerenciar a VM na página Máquinas Virtuais.

### <a name="complete-the-migration"></a>Concluir a migração

1. Depois que a migração for concluída, clique com o botão direito do mouse na VM > **Interromper migração**. Isso faz o seguinte:
    - Interrompe a replicação da VM do GCP.
    - Remove a VM do GCP da contagem de **Servidores de replicação** nas Migrações para Azure: Migração de Servidor.
    - Limpa as informações de estado de replicação da VM.
2. Instale o agente do [Windows](../virtual-machines/extensions/agent-windows.md) ou do [Linux](../virtual-machines/extensions/agent-linux.md) da VM do Azure nos computadores migrados.
3. Execute todos os ajustes no aplicativo após a migração, como atualizar as cadeias de conexão de banco de dados e as configurações do servidor Web.
4. Execute o aplicativo final e o teste de aceitação da migração no aplicativo migrado que está sendo executado no Azure.
5. Transfira o tráfego para a instância migrada da VM do Azure.
6. Atualize todas as documentações internas para mostrar o novo local e o endereço IP das VMs do Azure. 




## <a name="post-migration-best-practices"></a>Melhores práticas pós-migração

- Para aumentar a resiliência:
    - Proteja os dados fazendo backup das VMs do Azure por meio do serviço Backup do Azure. [Saiba mais](../backup/quick-backup-vm-portal.md).
    - Mantenha as cargas de trabalho em execução e continuamente disponíveis ao replicar as VMs do Azure em uma região secundária com o Site Recovery. [Saiba mais](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Para aumentar a segurança:
    - Bloqueie e limite o acesso ao tráfego de entrada com a [Central de Segurança do Azure – Administração just-in-time](../security-center/security-center-just-in-time.md).
    - Restrinja o tráfego de rede a pontos de extremidade com os [Grupos de Segurança de Rede](../virtual-network/network-security-groups-overview.md).
    - Implante o [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) para manter os discos em segurança e proteger os dados contra roubo e acesso não autorizado.
    - Leia mais sobre [como proteger recursos IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) e acesse a [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/).
- Para monitoramento e gerenciamento:
    - Considere implantar o [Gerenciamento de Custos do Azure](../cost-management-billing/cloudyn/overview.md) para monitorar o uso de recursos e os gastos.



## <a name="troubleshooting--tips"></a>Solução de problemas/Dicas

**Pergunta:** Não consigo ver minha VM do GCP na lista descoberta de servidores para migração   
**Resposta:** verifique se o dispositivo de replicação atende aos requisitos. Verifique se o agente de Mobilidade está instalado na VM de origem a ser migrada e se está registrado no Servidor de Configuração. Verifique as regras de firewall para habilitar um caminho de rede entre o dispositivo de replicação e as VMs do GCP de origem.  

**Pergunta:** como posso saber se minha VM foi migrada com êxito?   
**Resposta:** após a migração, você poderá ver e gerenciar a VM na página Máquinas Virtuais. Conecte-se à VM migrada para validá-la.  

**Pergunta:** não consigo usar os meus resultados de Avaliação de Servidor criados anteriormente para importar VMs   
**Resposta:** no momento, a importação da avaliação para esse fluxo de trabalho não é um procedimento compatível. Como solução alternativa, você pode exportar a avaliação e selecionar manualmente a recomendação de VM durante a etapa Habilitar Replicação.
  
**Pergunta:** estou recebendo o erro "Falha ao recuperar o GUID da BIOS" ao tentar descobrir minhas VMs do GCP   
**Resposta:** Use o logon raiz para autenticação e não qualquer pseudousuário. Se não for possível usar um usuário raiz, verifique se os recursos necessários estão definidos no usuário, de acordo com as instruções fornecidas [matriz de suporte](migrate-support-matrix-physical.md#physical-server-requirements). Além disso, examine os sistemas operacionais compatíveis para VMs do GCP.  

**Pergunta:** o status da minha replicação não está avançando   
**Resposta:** verifique se o dispositivo de replicação atende aos requisitos. Verifique se você habilitou as portas necessárias em seu dispositivo de replicação (porta TCP 9443 e HTTPS 443) para o transporte de dados. Verifique se não há versões duplicadas obsoletas do dispositivo de replicação conectadas ao mesmo projeto.   

**Pergunta:** Não consigo descobrir instâncias do GCP usando as Migrações para Azure devido ao código de status HTTP 504 do serviço de gerenciamento remoto do Windows    
**Resposta:** examine os requisitos de dispositivo para as Migrações para Azure e as necessidades de acesso à URL. Verifique se nenhuma configuração de proxy está bloqueando o registro do dispositivo.

**Pergunta:** Preciso fazer alterações antes de migrar minhas VMs do GCP para o Azure   
**Resposta:** Talvez seja necessário fazer essas alterações antes de migrar suas VMs EC2 para o Azure:

- Se você estiver usando cloud-init para o provisionamento de VM, desabilite o cloud-init na VM antes de replicá-la no Azure. As etapas de provisionamento executadas por cloud-init na VM podem ser específicas do GCP e não serão válidas após a migração para o Azure.  
- Examine a seção [pré-requisitos](#prerequisites) para determinar se é necessário fazer alterações no sistema operacional antes de migrá-las para o Azure.
- Sempre recomendamos que você execute uma migração de teste antes da migração final.  

## <a name="next-steps"></a>Próximas etapas

Investigue a [jornada de migração na nuvem](/azure/architecture/cloud-adoption/getting-started/migrate) no Cloud Adoption Framework do Azure.
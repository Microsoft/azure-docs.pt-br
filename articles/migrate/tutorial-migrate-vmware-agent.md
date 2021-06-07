---
title: Migrar VMs VMware com a migração baseada em agente das Migrações para Azure
description: Saiba como executar uma migração baseada em agente de VMs VMware com as Migrações para Azure.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: tutorial
ms.date: 06/09/2020
ms.custom: MVC
ms.openlocfilehash: 15bf8f4fde2128181664fa7b94f2479bac7ad5b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98881510"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>Migrar VMs VMware para o Azure (com base em agente)

Este artigo mostra como migrar as VMs VMware locais para o Azure usando a ferramenta [Migrações para Azure: Migração de Servidor](migrate-services-overview.md#azure-migrate-server-migration-tool), com a migração baseada em agente.  Também é possível migrar VMs VMware usando uma migração sem agente. [Compare](server-migrate-overview.md#compare-migration-methods) os métodos.


 Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Preparar o Azure para trabalhar com as Migrações para Azure.
> * Prepare para migração baseada em agente. Configure uma conta do VMware para que as Migrações para Azure possam descobrir computadores para migração. Configure uma conta para que o agente do Serviço de Mobilidade possa instalar em computadores que você deseja migrar e prepare um computador para funcionar como o dispositivo de replicação.
> * Adicionar a ferramenta Migração de Servidor das Migrações para Azure
> * Configurar o dispositivo de replicação.
> * Replicar VMs.
> * Executar uma migração de teste para verificar se tudo está funcionando conforme o esperado.
> * Executar uma migração completa para o Azure.

> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário para que você possa configurar rapidamente uma prova de conceito. Os tutoriais usam opções padrão quando possível e não mostram todas as configurações e todos os caminhos possíveis. 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, [examine](./agent-based-migration-architecture.md) a arquitetura de migração baseada no agente do VMware.

## <a name="prepare-azure"></a>Preparar o Azure

Conclua as tarefas na tabela para preparar o Azure para a migração baseada em agente.

**Tarefa** | **Detalhes**
--- | ---
**Criar um projeto de Migrações para Azure** | Sua conta do Azure precisa de permissões de Colaborador ou de Proprietário para criar um projeto.
**Verificar as permissões de conta do Azure** | Sua conta do Azure precisa de permissões para criar uma VM e gravar em um disco gerenciado do Azure.
**Configure uma rede do Azure** | Configure uma rede em que as VMs do Azure ingressarão após a migração.

### <a name="assign-permissions-to-create-project"></a>Atribuir permissões para criar o projeto
Se você não tiver um projeto de Migrações para Azure, verifique as permissões para criar um.


1. No portal do Azure, abra a assinatura e selecione **Controle de acesso (IAM).**
2. Em **Verificar acesso**, localize a conta relevante e clique nela para exibir as permissões.
3. Verifique se você tem as permissões de **Colaborador** e de **Proprietário**.

    - Se você acaba de criar uma conta gratuita do Azure, você é o proprietário da assinatura.
    - Se você não for o proprietário da assinatura, trabalhe com o proprietário para atribuir a função.
    
### <a name="assign-azure-account-permissions"></a>Atribuir permissões de conta do Azure

Atribua a função de Colaborador da Máquina Virtual à conta para que você tenha permissões para:

- Criar uma VM no grupo de recursos selecionado.
- Criar uma VM na rede virtual selecionada.
- Gravar em um disco gerenciado do Azure. 


### <a name="set-up-an-azure-network"></a>Configure uma rede do Azure

[Configure uma rede do Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Os computadores locais são replicados para discos gerenciados do Azure. Quando você faz failover para o Azure para migração, as VMs do Azure são criadas usando esses discos gerenciados e ingressadas na rede do Azure que você configura.

## <a name="prepare-for-migration"></a>Preparar para a migração

Verifique os requisitos de suporte e as permissões e prepare-se para implantar um dispositivo de replicação. 

### <a name="prepare-an-account-to-discover-vms"></a>Preparar uma conta para descobrir VMs

A Migração de Servidor de Migrações para Azure precisa acessar servidores VMware para descobrir VMs que você deseja migrar. Crie a conta da seguinte maneira:

1. Para usar uma conta dedicada, crie uma função no nível do vCenter. Dê um nome à função como **Azure_Migrate**.
2. Atribua à função as permissões resumidas na tabela a seguir.
3. Crie um usuário no vCenter Server ou no host do vSphere. Atribua a função ao usuário.

#### <a name="vmware-account-permissions"></a>Permissões de conta do VMware

**Tarefa** | **Função/Permissões** | **Detalhes**
--- | --- | ---
**Descoberta VM** | Pelo menos um usuário somente leitura<br/><br/> Objeto de data center –> Propagar para o objeto filho, função = somente leitura | Usuário atribuído no nível de datacenter e tem acesso a todos os objetos no datacenter.<br/><br/> Para restringir o acesso, atribua aos objetos filho a função **Nenhum acesso** com a opção **Propagar para objeto filho** marcada (hosts vSphere, datastores, VMs e redes).
**Replicação** |  Crie uma função (Azure_Site_Recovery) com as permissões necessárias e, em seguida, atribua a função a um usuário ou grupo do VMware<br/><br/> Objeto de Data Center –> Propagar para o Objeto Filho, função = Azure_Site_Recovery<br/><br/> Armazenamento de dados -> alocar espaço, procurar armazenamento de dados, operações de arquivo de baixo nível, remover arquivo, atualizar arquivos de máquina virtual<br/><br/> Rede -> Atribuição de rede<br/><br/> Recurso -> Atribuir VM ao pool de recursos, migrar VM desligada, migrar VM ligada<br/><br/> Tarefas -> Criar tarefa, atualizar tarefa<br/><br/> Máquina virtual -> Configuração<br/><br/> Máquina virtual -> Interagir -> responder à pergunta, conexão de dispositivo, configurar mídia de CD, configurar mídia de disquete, desligar, ligar, instalação de ferramentas VMware<br/><br/> Máquina virtual -> Inventário -> Criar, registrar, cancelar registro<br/><br/> Máquina virtual -> Provisionamento -> Permitir download de máquina virtual, permitir upload de arquivos de máquina virtual<br/><br/> Máquina virtual -> Instantâneos -> Remover instantâneos | Usuário atribuído no nível de datacenter e tem acesso a todos os objetos no datacenter.<br/><br/> Para restringir o acesso, atribua aos objetos filho a função **Nenhum acesso** com a opção **Propagar para objeto filho** marcada (hosts vSphere, datastores, VMs e redes).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparar uma conta para a instalação do serviço de Mobilidade

O serviço de Mobilidade deve ser instalado em todas as VMs que você deseja replicar.

- O dispositivo de replicação de Migrações para Azure pode efetuar push da instalação desse serviço quando você habilita a replicação para um computador. Outra opção é instalá-lo manualmente ou usando ferramentas de instalação.
- Neste tutorial, vamos instalar o serviço de Mobilidade com a instalação por push.
- Para efetuar push da instalação, você precisa preparar uma conta que a Migração de Servidor de Migrações para Azure possa usar para acessar a VM. Essa conta será usada apenas para a instalação por push se você não instalar o serviço Mobilidade manualmente.

Prepare a conta da seguinte maneira:

1. Prepare um domínio ou uma conta local com permissões para instalar na VM.
2. Para VMs Windows, se você não estiver usando uma conta de domínio, desabilite o controle de Acesso de Usuário Remoto no computador local adicionando a entrada DWORD **LocalAccountTokenFilterPolicy** com um valor no Registro, em **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**
3. Para VMs do Linux, prepare uma conta raiz no servidor Linux de origem.


### <a name="prepare-a-machine-for-the-replication-appliance"></a>Preparar um computador para o dispositivo de replicação

O dispositivo é usado para máquinas de replicação no Azure. O dispositivo é uma VM VMware local única e altamente disponível que hospeda estes componentes:

- **Servidor de configuração**: O servidor de configuração coordena a comunicação entre o ambiente local e o Azure e gerencia a replicação de dados.
- **Servidor de processo**: O servidor de processo atua como um gateway de replicação. Ele recebe dados de replicação, otimiza-os com caching, compactação e criptografia e os envia para uma conta de armazenamento em cache no Azure. O servidor de processo também instala o agente do Serviço de Mobilidade nas VMs que você deseja replicar e executa a descoberta automática de VMs em VMware locais.

Prepare-se para o dispositivo da seguinte maneira:

- [Examinar os requisitos do dispositivo](migrate-replication-appliance.md#appliance-requirements). Em geral, você configura o dispositivo de replicação como uma VM VMware usando um arquivo OVA baixado. O modelo cria um dispositivo que está em conformidade com todos os requisitos.
- MySQL deve estar instalado no dispositivo. [Examine](migrate-replication-appliance.md#mysql-installation) métodos de instalação.
- Examine as URLs de [nuvem pública](migrate-replication-appliance.md#url-access) e do [Azure Government](migrate-replication-appliance.md#azure-government-url-access) que o computador do dispositivo precisa acessar.
- [Examine as portas](migrate-replication-appliance.md#port-access) que o computador do dispositivo de replicação precisa acessar.



### <a name="check-vmware-requirements"></a>Verificar requisitos de VMware

Verifique se os servidores e as VMs VMware cumprem os requisitos para a migração para Azure. 

1. [Verifique](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based) os requisitos do servidor VMware.
2. [Verifique](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based) os requisitos de VM para a migração.
3. Verifique as configurações do Azure. As VMs locais que você replica para o Azure devem cumprir os [requisitos de VM do Azure](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).
4. Há algumas alterações necessárias nas VMs antes de migrá-las para o Azure.
    - É importante fazer essas alterações antes de iniciar a migração. Se você migrar a VM antes de fazer a alteração, ela não poderá ser inicializada no Azure.
    - Examine as alterações para [Windows](prepare-for-migration.md#windows-machines) e [Linux](prepare-for-migration.md#linux-machines) que você precisa fazer.

> [!NOTE]
> A migração baseada em agente com a Migração de Servidor de Migrações para Azure baseia-se nos recursos do serviço do Azure Site Recovery. Alguns requisitos podem ser vinculados à documentação do Site Recovery.

## <a name="set-up-the-replication-appliance"></a>Configurar o dispositivo de replicação

Este procedimento descreve como configurar o dispositivo com um modelo do OVA (Open Virtualization Application) baixado. Se você não puder usar esse método, poderá configurar o dispositivo [usando um script](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance). 

### <a name="download-the-replication-appliance-template"></a>Baixar o modelo do dispositivo de replicação

Baixe o modelo da seguinte maneira:

1. No projeto Migrações para Azure, clique em **Servidores** em **Metas de Migração**.
2. Na página **Migrações para Azure – Servidores** > **Migrações para Azure: Migração de Servidor**, clique em **Descobrir**.

    ![Descobrir VMs](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. Em **Descobrir computadores** > **As suas máquinas são virtualizadas?** , clique em **Sim, com o hipervisor do VMware vSphere**.
4. Em **Como você deseja migrar?** , selecione **Usando a replicação baseada em agente**.
5. Em **Região de destino**, selecione a região do Azure para a qual deseja migrar os computadores.
6. Selecione **Confirme se a região de destino da migração é nome-da-região**.
7. Clique em **Criar recursos**. Isso criará um cofre do Azure Site Recovery em segundo plano. Você não poderá alterar a região de destino desse projeto depois de clicar nesse botão, e todas as migrações subsequentes serão para essa região.

    ![Criar cofre de Serviços de Recuperação](./media/tutorial-migrate-vmware-agent/create-resources.png)

8. Em **Deseja instalar um novo dispositivo de replicação?** , selecione **Instalar um dispositivo de replicação**.
9. Clique em **Download**. Isso baixa um modelo OVF.
    ![Baixar OVA](./media/tutorial-migrate-vmware-agent/download-ova.png)
10. Observe o nome do grupo de recursos e o cofre dos Serviços de Recuperação. Você precisa deles durante a implantação do dispositivo.


### <a name="import-the-template-in-vmware"></a>Importar o modelo para a VMware

Depois de baixar o modelo OVF, você o importa para o VMware para criar o aplicativo de replicação em uma VM VMware que executa o Windows Server 2016.

1. Entre no VMware vCenter Server ou no host vSphere ESXi com o VMware vSphere Client.
2. No menu **Arquivo**, selecione **Implantar Modelo de OVF** para iniciar o **Implante o assistente de modelo de OVF**. 
3. Em **Selecionar origem**, insira o local do OVF baixado.
4. Em **Revisar detalhes**, selecione **Avançar**.
5. Em **Selecionar nome e pasta** e **Selecionar configuração**, aceite as configurações padrão.
6. Em **Selecionar armazenamento** > **Selecionar formato de disco virtual**, para obter o melhor desempenho, selecione **Thick Provision Eager Zeroed**.
7. No restante das páginas do assistente, aceite as configurações padrão.
8. Em **Pronto para concluir**, para configurar a VM com as configurações padrão, selecione **Ligar após a implantação** > **Concluir**.

   > [!TIP]
   > Se você quiser incluir um NIC adicional, limpe **Ligar após a implantação** > **Concluir**. Por padrão, o modelo contém um único NIC. É possível incluir NICs adicionais após a implantação.

### <a name="start-appliance-setup"></a>Iniciar configuração do dispositivo

1. No console do VMware vSphere Client, ative a VM. A VM será inicializada com uma experiência de instalação do Windows Server 2016.
2. Aceite o contrato de licença e insira uma senha de administrador.
3. Após a conclusão da instalação, entre na VM como administrador usando a senha de administrador. Na primeira vez que você entrar, a ferramenta de instalação do dispositivo de replicação (Ferramenta de Configuração do Azure Site Recovery) iniciará em alguns segundos.
5. Insira um nome a ser usado para registrar o dispositivo com a Migração de Servidor. Em seguida, clique em **Próximo**.
6. A ferramenta verifica se a VM pode se conectar ao Azure. Depois que a conexão for estabelecida, selecione **Entrar** para fazer logon na sua assinatura do Azure.
7. Aguarde até a ferramenta terminar de registrar um aplicativo do Azure AD para identificar o dispositivo. O dispositivo é reinicializado.
1. Entre novamente no computador. Em alguns segundos, o Assistente de Gerenciamento do Servidor de Configuração iniciará automaticamente.

### <a name="register-the-replication-appliance"></a>Registrar o dispositivo de replicação

Conclua a configuração e o registro do dispositivo de replicação.

1. Em configuração do dispositivo, selecione **Configurar conectividade**.
2. Selecione a NIC (por padrão, há apenas uma NIC) que o dispositivo de replicação usa para a descoberta da VM e para fazer efetuar push de uma instalação do serviço de Mobilidade em computadores de origem.
3. Selecione a NIC que o dispositivo de replicação usa para conectividade com o Azure. Em seguida, selecione **Salvar**. Não é possível alterar essa configuração depois de ela ter sido definida.
4. Se o dispositivo estiver localizado atrás de um servidor proxy, você precisará especificar as configurações de proxy.
    - Especifique o nome do proxy como **http://ip-address** ou **http://FQDN** . Não há suporte para servidores proxy HTTPS.
5. Quando for solicitada a assinatura, os grupos de recursos e os detalhes do cofre, adicione os detalhes que você anotou ao baixar o modelo do dispositivo.
6. Em **Instalar software de terceiros**, aceite o contrato de licença. Selecione **Baixar e Instalar** para instalar o MySQL Server.
7. Selecione **Instalar VMware PowerCLI**. Verifique se todas as janelas de navegador estão fechadas antes de fazer isso. Depois selecione **Continuar**.
8. Em **Validar configuração de dispositivo**, os pré-requisitos serão verificados antes de continuar.
9. Em **Configurar vCenter Server/servidor vSphere ESXi**, insira o FQDN ou endereço IP do vCenter Server ou o host vSphere, onde as VMs que deseja replicar estão localizadas. Insira a porta na qual o servidor está escutando. Insira um nome amigável a ser usado para o servidor VMware no cofre.
10. Insira as credenciais para a conta que você [criou](#prepare-an-account-to-discover-vms) para a descoberta do VMware. Selecione **Adicionar** > **Continuar**.
11. Em **Configurar credenciais da máquina virtual**, insira as credenciais que você [criou](#prepare-an-account-for-mobility-service-installation) para a instalação por push do serviço de mobilidade ao habilitar a replicação para VMs.  
    - Para computadores do Windows, a conta precisa de privilégios de administrador local nos computadores que você deseja replicar.
    - Para o Linux, forneça detalhes para a conta raiz.
12. Selecione **Finalizar configuração** para concluir o registro.


Depois que o dispositivo de replicação é registrado, a Avaliação de Servidor de Migrações para Azure conecta-se aos servidores VMware usando as configurações especificadas e descobre as VMs. Você pode exibir as VMs descobertas em **Gerenciar** > **Itens descobertos** na guia **Outro**.



## <a name="replicate-vms"></a>Replicar VMs

Selecione as VMs para migração.

> [!NOTE]
> No portal, você pode selecionar até 10 computadores ao mesmo tempo para replicação. Se você precisar replicar mais, agrupe-as em lotes de 10.

1. No projeto das Migrações para Azure > **Servidores**, **Migrações para Azure: Migração de Servidor**, clique em **Replicar**.

    ![Captura de tela da tela Servidores nas Migrações para Azure. O botão Replicar está selecionado nas Migrações para Azure: Migração de Servidor nas ferramentas de Migração.](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. Em **Replicar**, > **Configurações de origem** > **Seus computadores estão virtualizados?** , selecione **Sim, com o VMware vSphere**.
3. Em **Dispositivo local**, selecione o nome do dispositivo de Migrações para Azure que você configurou.
4. No **vCenter Server**, especifique o nome do vCenter Server que gerencia as VMs ou do servidor vSphere no qual as VMs estão hospedadas.
5. Em **Servidor de Processo**, selecione o nome do dispositivo de replicação.
6. Em **Credenciais de convidado**, especifique a conta do administrador de VM que será usada para a instalação por push do serviço de Mobilidade. Em seguida, clique em **Próximo: Máquinas virtuais**.

    ![Captura de tela da guia Configurações de origem na tela Replicar. O campo Credenciais de convidado está realçado e o valor está definido como VM-admin-account.](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. Em **Máquinas Virtuais**, selecione os computadores que você deseja replicar.

    - Se você tiver executado uma avaliação das VMs, poderá aplicar recomendações de dimensionamento de VM e de tipo de disco (Premium/Standard) dos resultados da avaliação. Para fazer isso, em **Importar configurações de migração de uma avaliação de Migrações para Azure?** , selecione a opção **Sim**.
    - Se você não tiver executado uma avaliação ou não quiser usar as configurações de avaliação, selecione a opção **Não**.
    - Se você optou por usar a avaliação, selecione o grupo de VMs e o nome da avaliação.
8. Em **Opções de disponibilidade**, selecione:
    -  Zona de Disponibilidade para fixar o computador migrado para uma Zona de Disponibilidade específica na região. Use essa opção para distribuir servidores que formam uma camada de aplicativo de vários nós entre Zonas de Disponibilidade diferentes. Se você selecionar essa opção, precisará especificar a zona de disponibilidade a ser usada para cada computador selecionado na guia Computação. Essa opção só estará disponível se a região de destino selecionada para a migração der suporte a Zonas de Disponibilidade
    -  Conjunto de Disponibilidade para colocar o computador migrado em um conjunto de disponibilidade. O grupo de recursos de destino selecionado precisa ter um ou mais conjuntos de disponibilidade para que possa usar essa opção.
    - Nenhuma opção de redundância de infraestrutura será necessária se você não precisar de nenhuma dessas configurações de disponibilidade para os computadores migrados.
9. Verifique cada VM que você deseja migrar. Em seguida, clique em **Próximo: configurações de destino**.
10. Em **Configurações de destino**, selecione a assinatura e a região de destino para a qual você migrará e especifique o grupo de recursos no qual as VMs do Azure residirão após a migração.
11. Em **Rede Virtual**, selecione a VNet/sub-rede do Azure na qual as VMs do Azure serão ingressadas após a migração.
12. Em **Opções de disponibilidade**, selecione:
    -  Zona de Disponibilidade para fixar o computador migrado para uma Zona de Disponibilidade específica na região. Use essa opção para distribuir servidores que formam uma camada de aplicativo de vários nós entre Zonas de Disponibilidade diferentes. Se você selecionar essa opção, precisará especificar a zona de disponibilidade a ser usada para cada computador selecionado na guia Computação. Essa opção só estará disponível se a região de destino selecionada para a migração der suporte a Zonas de Disponibilidade
    -  Conjunto de Disponibilidade para colocar o computador migrado em um conjunto de disponibilidade. O grupo de recursos de destino selecionado precisa ter um ou mais conjuntos de disponibilidade para que possa usar essa opção.
    - Nenhuma opção de redundância de infraestrutura será necessária se você não precisar de nenhuma dessas configurações de disponibilidade para os computadores migrados.
13. Em **Tipo de criptografia de disco**, selecione:
    - Criptografia em repouso com chave de criptografia gerenciada pela plataforma
    - Criptografia em repouso com a chave gerenciada pelo cliente
    - Criptografia dupla com chaves gerenciadas por plataforma e gerenciadas pelo cliente

   > [!NOTE]
   > Para replicar VMs com a CMK, você precisará [criar um conjunto de criptografia de disco](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set) no grupo de recursos de destino. Um objeto de conjunto de criptografia de disco mapeia o Managed Disks para um Key Vault que contém a CMK a ser usada para a SSE.
  
14. Em **Benefício Híbrido do Azure**:

    - Selecione **Não** se não desejar aplicar o Benefício Híbrido do Azure. Em seguida, clique em **Próximo**.
    - Selecione **Sim** se você tiver computadores Windows Server cobertos com assinaturas ativas do Software Assurance ou do Windows Server e quiser aplicar o benefício aos computadores que estão sendo migrados. Em seguida, clique em **Próximo**.

    ![Configurações de destino](./media/tutorial-migrate-vmware/target-settings.png)

15. Em **Computação**, examine o nome da VM, o tamanho, o tipo de disco do SO e a configuração de disponibilidade (se selecionado na etapa anterior). As VMs devem estar em conformidade com os [requisitos do Azure](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).

   - **Tamanho da VM**: se você estiver usando recomendações de avaliação, o menu suspenso de tamanho da VM mostrará o tamanho recomendado. Caso contrário, as Migrações para Azure escolherão um tamanho com base na correspondência mais próxima na assinatura do Azure. Como alternativa, escolha um tamanho manual em **Tamanho da VM do Azure**. 
    - **Disco do SO**: especifique o disco do sistema operacional (inicialização) para a VM. O disco do sistema operacional é o disco que tem o carregador de inicialização e o instalador do sistema operacional. 
    - **Zona de Disponibilidade**: especifique a zona de disponibilidade a ser usada.
    - **Conjunto de disponibilidade**: especifique o conjunto de disponibilidade a ser usado.

16. Em **Discos**, especifique se os discos da VM devem ser replicados para o Azure e selecione o tipo de disco (discos gerenciados HDD/SSD standard ou premium) no Azure. Em seguida, clique em **Próximo**.
    - Você pode excluir discos da replicação.
    - Se você excluir os discos, eles não estarão presentes na VM do Azure após a migração. 

17. Em **Examinar e iniciar a replicação**, examine as configurações e clique em **Replicar** para iniciar a replicação inicial dos servidores.

> [!NOTE]
> É possível atualizar as configurações de replicação a qualquer momento antes do início da replicação em **Gerenciar** > **Computadores em replicação**. Não é possível alterar as configurações após o início da replicação.


## <a name="track-and-monitor"></a>Acompanhar e monitorar

1. Acompanhe o status do trabalho nas notificações do portal. 

    ![Acompanhar o trabalho](./media/tutorial-migrate-vmware-agent/jobs.png)
    
2. Para monitorar o status de replicação, clique em **Servidores de replicação** em **Migrações para Azure: Migração de Servidor**.

    ![Monitorar a replicação](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

A replicação ocorre da seguinte maneira:
- Quando o trabalho Iniciar Replicação é concluído com êxito, os computadores começam sua replicação inicial para o Azure.
- Depois que a replicação inicial é concluída, a replicação delta é iniciada. As alterações incrementais em discos locais são replicadas periodicamente para os discos de réplica no Azure.


## <a name="run-a-test-migration"></a>Execute um teste de migração


Quando a replicação delta é iniciada, é possível executar uma migração de teste para as VMs antes de executar uma migração completa para o Azure. É altamente recomendável que você faça isso pelo menos uma vez para cada computador antes de migrá-lo.

- A execução de uma migração de teste verifica se a migração funcionará conforme o esperado, sem afetar os computadores locais, que permanecem operacionais e continuam sendo replicados. 
- A migração de teste simula a migração criando uma VM do Azure com o uso dos dados replicados (geralmente, fazendo a migração para uma VNet de não produção em sua assinatura do Azure).
- É possível usar a VM do Azure de teste replicada para validar a migração, executar testes de aplicativo e resolver problemas antes da migração completa.

Faça uma migração de teste da seguinte maneira:


1. Em **Metas de migração** > **Servidores** > **Migrações para Azure: Migração de Servidor**, clique em **Testar servidores migrados**.

     ![Testar servidores migrados](./media/tutorial-migrate-vmware-agent/test-migrated-servers.png)

2. Clique com o botão direito do mouse na VM a ser testada e clique em **Migração de teste**.

    ![Migração de teste](./media/tutorial-migrate-vmware-agent/test-migrate.png)

3. Em **Migração de Teste**, selecione a VNet do Azure na qual a VM do Azure estará localizada após a migração. Recomendamos que você use uma VNet que não seja de produção.
4. O trabalho **Migração de teste** é iniciado. Monitore o trabalho nas notificações do portal.
5. Após a conclusão da migração, veja a VM do Azure migrada em **Máquinas Virtuais** no portal do Azure. O nome do computador tem o sufixo **-Test**.
6. Depois que o teste for realizado, clique com o botão direito do mouse na VM do Azure em **Replicando computadores** e clique em **Limpar migração de teste**.

    ![Limpar migração](./media/tutorial-migrate-vmware-agent/clean-up.png)


## <a name="migrate-vms"></a>Migrar VMs

Depois de verificar se a migração de teste funciona conforme o esperado, você poderá migrar os computadores locais.

1. No projeto das Migrações para Azure > **Servidores** > **Migrações para Azure: Migração de Servidor**, clique em **Replicando servidores**.

    ![Replicando servidores](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

2. Em **Replicando computadores**, clique com o botão direito do mouse na VM > **Migrar**.
3. Em **Migrar** > **Desligar máquinas virtuais e realizar uma migração planejada sem perda de dados**, selecione **Sim** > **OK**.
    - Por padrão, as Migrações para Azure desligam a VM local para garantir a perda mínima de dados. 
    - Se você não quiser desligar a VM, selecione **Não**
4. Um trabalho de migração é iniciado para a VM. Acompanhe o trabalho nas notificações do Azure.
5. Após a conclusão do trabalho, você poderá exibir e gerenciar a VM na página **Máquinas Virtuais**.

## <a name="complete-the-migration"></a>Concluir a migração

1. Depois que a migração for concluída, clique com o botão direito do mouse na VM > **Interromper migração**. Isso faz o seguinte:
    - Interrompe a replicação no computador local.
    - Remove o computador da contagem de **Servidores de replicação** nas Migrações para Azure: Migração de Servidor.
    - Limpa as informações de estado de replicação da VM.
2. Instale o agente do [Windows](../virtual-machines/extensions/agent-windows.md) ou do [Linux](../virtual-machines/extensions/agent-linux.md) da VM do Azure nos computadores migrados.
3. Execute todos os ajustes no aplicativo após a migração, como atualizar as cadeias de conexão de banco de dados e as configurações do servidor Web.
4. Execute o aplicativo final e o teste de aceitação da migração no aplicativo migrado que está sendo executado no Azure.
5. Transfira o tráfego para a instância migrada da VM do Azure.
6. Remova as VMs locais do inventário local de VMs.
7. Remova as VMs locais dos backups locais.
8. Atualize todas as documentações internas para mostrar o novo local e o endereço IP das VMs do Azure. 

## <a name="post-migration-best-practices"></a>Melhores práticas pós-migração

- Local
    - Mova o tráfego do aplicativo para o aplicativo em execução na instância migrada da VM do Azure.
    - Remova as VMs locais do inventário local de VMs.
    - Remova as VMs locais dos backups locais.
    - Atualize todas as documentações internas para mostrar o novo local e o endereço IP das VMs do Azure.
- Ajuste as configurações da VM do Azure após a migração:
    - O [agente de VM do Azure](../virtual-machines/extensions/agent-windows.md) gerencia a interação entre uma VM do Azure e o controlador de malha do Azure. É necessário para alguns serviços do Azure, como o Backup do Azure, Site Recovery e Segurança do Azure. Ao migrar VMs de VMare com migração baseada em agente, o instalador do Serviço de Mobilidade instala o agente de VM do Azure em computadores Windows. Em VMs do Linux, é recomendável instalar o agente após a migração.
    - Desinstale manualmente o Serviço de Mobilidade da VM do Azure após a migração.
    - Desinstale manualmente as ferramentas de VMware após a migração.
- No Azure:
    - Execute todos os ajustes no aplicativo após a migração, como atualizar as cadeias de conexão de banco de dados e as configurações do servidor Web.
    - Execute o aplicativo final e o teste de aceitação da migração no aplicativo migrado que está sendo executado no Azure.
- Continuidade de negócios/recuperação de desastre
    - Proteja os dados fazendo backup das VMs do Azure por meio do serviço Backup do Azure. [Saiba mais](../backup/quick-backup-vm-portal.md).
    - Mantenha as cargas de trabalho em execução e continuamente disponíveis ao replicar as VMs do Azure em uma região secundária com o Site Recovery. [Saiba mais](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Para aumentar a segurança:
    - Bloqueie e limite o acesso ao tráfego de entrada com a [Central de Segurança do Azure – Administração just-in-time](../security-center/security-center-just-in-time.md).
    - Restrinja o tráfego de rede a pontos de extremidade com os [Grupos de Segurança de Rede](../virtual-network/network-security-groups-overview.md).
    - Implante o [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) para manter os discos em segurança e proteger os dados contra roubo e acesso não autorizado.
    - Leia mais sobre [como proteger recursos IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) e acesse a [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/).
- Para monitoramento e gerenciamento:
    - Considere implantar o [Gerenciamento de Custos do Azure](../cost-management-billing/cloudyn/overview.md) para monitorar o uso de recursos e os gastos.




 ## <a name="next-steps"></a>Próximas etapas

Investigue a [jornada de migração na nuvem](/azure/architecture/cloud-adoption/getting-started/migrate) no Cloud Adoption Framework do Azure.
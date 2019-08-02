---
title: Migrar VMs VMware locais para o Azure com a Migração de Servidor de Migrações para Azure com base em agente | Microsoft Docs
description: Este artigo descreve como executar uma migração baseada em agente de computadores locais para o Azure com a Migração de Servidor de Migrações para Azure
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: d222936f93f90573a46cd7f6216fbde8043332c7
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68261403"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>Migrar VMs VMware para o Azure (com base em agente)

Este artigo mostra como migrar as VMs VMware locais para o Azure usando a migração com base em agente com a ferramenta de Migração de Servidor das Migrações para Azure.

As [Migrações para Azure](migrate-services-overview.md) oferecem um hub central para acompanhar a descoberta, a avaliação e a migração de seus aplicativos e cargas de trabalho locais, bem como de instâncias de VM AWS/GCP, para o Azure. O hub fornece as ferramentas das Migrações para Azure para avaliação e migração, além de ofertas de ISV (fornecedor independente de software) de terceiros.


Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Configure o ambiente de origem e implante um dispositivo de replicação de Migrações para Azure para a migração com base em agente.
> * Configurar o ambiente de destino para migração.
> * Configurar uma política de replicação.
> * Habilite a replicação.
> * Executar uma migração de teste para verificar se tudo está funcionando conforme o esperado.
> * Executar uma migração completa para o Azure.

> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário para que você possa configurar rapidamente uma prova de conceito. Os tutoriais usam opções padrão quando possível e não mostram todas as configurações e todos os caminhos possíveis. Para obter instruções detalhadas, examine as instruções para avaliação e migração do VMware.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

Recomendamos que você experimente a avaliação de VM VMware com a Avaliação de Servidor de Migrações para Azure antes de migrar as VMs para o Azure. Configure uma avaliação da seguinte maneira:

1. Siga o tutorial para [preparar o Azure e o VMware](tutorial-prepare-vmware.md) para avaliação.
2. Então, siga [este tutorial](tutorial-assess-vmware.md) para configurar um dispositivo de Migrações para Azure para avaliação e descobrir e avaliar VMS.


Embora recomendemos experimentar uma avaliação, não é necessário executar uma avaliação antes de migrar as VMs.

## <a name="migration-methods"></a>Métodos de migração

É possível migrar VMs VMware para o Azure usando a ferramenta de Migração de Servidor de Migrações para Azure. Essa ferramenta oferece algumas opções para a migração de VM VMware:

- Replicação sem agente. Migre VMs sem precisar instalar nada nelas.
- Migração baseada em agente. ou replicação. Instale um agente (o agente de serviços de Mobilidade) na VM para replicação.

Para decidir se deseja usar a migração sem agente ou baseada em agente, leia estes artigos:

- [Saiba mais](server-migrate-overview.md) sobre as opções de migração do VMware.
- [Examine as limitações](server-migrate-overview.md#agentless-migration-limitations) da migração sem agente.
- [Siga este artigo](tutorial-migrate-vmware.md) para experimentar a migração sem agente.



## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, você deverá:

1. [Examinar](migrate-architecture.md) a arquitetura de migração do VMware.
2. Verificar se a sua conta do Azure foi atribuída à função Colaborador da Máquina Virtual para ter permissões para:

    - Criar uma VM no grupo de recursos selecionado.
    - Criar uma VM na rede virtual selecionada.
    - Gravar em um disco gerenciado do Azure. 

3. [Configure uma rede do Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Os computadores locais são replicados para discos gerenciados do Azure. Quando você faz failover para o Azure para migração, as VMs do Azure são criadas usando esses discos gerenciados e ingressadas em uma rede do Azure que você especifica ao configurar a migração.


## <a name="prepare-azure"></a>Preparar o Azure

Se você já tiver executado uma avaliação com a Avaliação de Servidor de Migrações para Azure, poderá ignorar as instruções nesta seção, pois você já concluiu essas etapas. 

Se você não tiver executado uma avaliação, precisará configurar as permissões do Azure antes de migrar com a Migração de Servidor de Migrações para Azure.

- **Criar um projeto**: sua conta do Azure precisa de permissões para criar um projeto de Migrações para Azure. 
- **Registrar o dispositivo de replicação de Migrações para Azure**: o dispositivo de replicação cria e registra um aplicativo do Azure Active Directory em sua conta do Azure. Você precisa delegar permissões para isso.
- **Criar um Cofre de Chaves**: para migrar VMs VMware usando a Migração de Servidor de Migrações para Azure, as Migrações para Azure criam um cofre de chaves no grupo de recursos para gerenciar chaves de acesso para a conta de armazenamento de replicação em sua assinatura. Para criar o cofre, você precisa de permissões de atribuição de função no grupo de recursos no qual o projeto de Migrações para Azure reside. 


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

    ![Permissões do Azure AD](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> Essa é uma configuração padrão que não é confidencial. [Saiba mais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Atribuir função de Desenvolvedor de Aplicativos 

O locatário/administrador global pode atribuir a função de Desenvolvedor de Aplicativos a uma conta. [Saiba mais](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="assign-permissions-to-create-key-vault"></a>Atribuir permissões para criar o cofre de chaves

Atribua permissões de atribuição de função no grupo de recursos no qual o projeto de Migrações para Azure reside, da seguinte maneira:

1. No grupo de recursos no portal do Azure, selecione **Controle de acesso (IAM).**
2. Em **Verificar acesso**, localize a conta relevante e clique nela para exibir as permissões. Você precisa das permissões de **Proprietário** (ou **Colaborador** e **Administrador de Acesso do Usuário**).
3. Se você não tiver as permissões necessárias, solicite-as do proprietário do grupo de recursos. 


## <a name="prepare-on-premises-vmware"></a>Preparar o VMware local

### <a name="prepare-an-account-for-automatic-discovery"></a>Preparar uma conta para a descoberta automática

A Migração de Servidor de Migrações para Azure precisa acessar servidores VMware para:

- Descobrir VMs automaticamente. Pelo menos uma conta somente leitura é necessária.
- Orquestrar a replicação, o failover e o failback. Você precisa de uma conta que possa executar operações como criar e remover discos e ativar VMs.

Crie a conta da seguinte maneira:

1. Para usar uma conta dedicada, crie uma função no nível do vCenter. Dê um nome à função como **Azure_Site_Recovery**.
2. Atribua à função as permissões resumidas na tabela a seguir.
3. Crie um usuário no vCenter Server ou no host do vSphere. Atribua a função ao usuário.

#### <a name="vmware-account-permissions"></a>Permissões de conta do VMware

**Tarefa** | **Função/Permissões** | **Detalhes**
--- | --- | ---
**Descoberta VM** | Pelo menos um usuário somente leitura<br/><br/> Objeto de data center –> Propagar para o objeto filho, função = somente leitura | Usuário atribuído no nível de datacenter e tem acesso a todos os objetos no datacenter.<br/><br/> Para restringir o acesso, atribua a função **Nenhum acesso** com o objeto **Propagar para filho** aos objetos filho (hosts vSphere, armazenamentos de dados, VMs e redes).
**Replicação, failover, failback totais** |  Crie uma função (Azure_Site_Recovery) com as permissões necessárias e, em seguida, atribua a função a um usuário ou grupo do VMware<br/><br/> Objeto de Data Center –> Propagar para o Objeto Filho, função = Azure_Site_Recovery<br/><br/> Armazenamento de dados -> alocar espaço, procurar armazenamento de dados, operações de arquivo de baixo nível, remover arquivo, atualizar arquivos de máquina virtual<br/><br/> Rede -> Atribuição de rede<br/><br/> Recurso -> Atribuir VM ao pool de recursos, migrar VM desligada, migrar VM ligada<br/><br/> Tarefas -> Criar tarefa, atualizar tarefa<br/><br/> Máquina virtual -> Configuração<br/><br/> Máquina virtual -> Interagir -> responder à pergunta, conexão de dispositivo, configurar mídia de CD, configurar mídia de disquete, desligar, ligar, instalação de ferramentas VMware<br/><br/> Máquina virtual -> Inventário -> Criar, registrar, cancelar registro<br/><br/> Máquina virtual -> Provisionamento -> Permitir download de máquina virtual, permitir upload de arquivos de máquina virtual<br/><br/> Máquina virtual -> Instantâneos -> Remover instantâneos | Usuário atribuído no nível de datacenter e tem acesso a todos os objetos no datacenter.<br/><br/> Para restringir o acesso, atribua a função **Nenhum acesso** com o objeto **Propagar para filho** aos objetos filho (hosts vSphere, armazenamentos de dados, VMs e redes).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparar uma conta para a instalação do serviço de Mobilidade

O serviço de Mobilidade deve ser instalado em todas as VMs que você deseja replicar.

- O dispositivo de replicação de Migrações para Azure pode efetuar push da instalação desse serviço quando você habilita a replicação para um computador. Outra opção é instalá-lo manualmente ou usando ferramentas de instalação.
- Neste tutorial, vamos instalar o serviço de Mobilidade com a instalação por push.
- Para efetuar push da instalação, você precisa preparar uma conta que a Migração de Servidor de Migrações para Azure possa usar para acessar a VM.

Prepare a conta da seguinte maneira:

1. Prepare um domínio ou uma conta local com permissões para instalar na VM.
2. Para VMs Windows, se você não estiver usando uma conta de domínio, desabilite o controle de Acesso de Usuário Remoto no computador local adicionando a entrada DWORD **LocalAccountTokenFilterPolicy** com um valor no Registro, em **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**
3. Para VMs do Linux, prepare uma conta raiz no servidor Linux de origem.


### <a name="check-vmware-requirements"></a>Verificar requisitos de VMware

Verifique se os servidores e as VMs VMware cumprem os requisitos para a migração para Azure. 


> [!NOTE]
> A migração baseada em agente com a Migração de Servidor de Migrações para Azure baseia-se nos recursos do serviço do Azure Site Recovery. Alguns requisitos podem ser vinculados à documentação do Site Recovery.

1. [Verifique](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) os requisitos do servidor VMware.
2. [Verifique](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) os requisitos de suporte da VM para a migração.
3. Verifique as configurações da VM. As VMs locais que você replica para o Azure devem cumprir os [requisitos de VM do Azure](migrate-support-matrix-vmware.md#azure-vm-requirements).



## <a name="add-the-azure-migrate-server-migration-tool"></a>Adicionar a ferramenta Migração de Servidor das Migrações para Azure

Se você não seguir o tutorial para avaliar as VMs VMware, configure um projeto de Migrações para Azure e adicione a ferramenta de Migração de Servidor de Migrações para Azure:

1. No portal do Azure > **Todos os serviços**, pesquise **Migrações para Azure**.
2. Em **Serviços**, selecione **Migrações para Azure**.

    ![Configurar Migrações para Azure](./media/tutorial-migrate-vmware-agent/azure-migrate-search.png)

3. Em **Visão Geral**, clique em **Avaliar emigrar servidores**.
4. Em **Descobrir, avaliar e migrar servidores**, clique em **Avaliar e migrar servidores**.

    ![Descobrir e avaliar servidores](./media/tutorial-migrate-vmware-agent/assess-migrate.png)

1. Em **Descobrir, avaliar e migrar servidores**, clique em **Adicionar ferramentas**.
2. Em **Migrar projeto**, selecione sua assinatura do Azure e crie um grupo de recursos, caso não tenha um.
3. Em **Detalhes do Projeto**, especifique o nome do projeto e a geografia em que você deseja criar o projeto e clique em **Avançar**

    ![Criar um projeto das Migrações para Azure](./media/tutorial-migrate-vmware-agent/migrate-project.png)

    Você pode criar um projeto de Migrações para Azure em qualquer uma dessas regiões.

    **Geografia** | **Região**
    --- | ---
    Ásia | Sudeste Asiático
    Europa | Europa Setentrional ou Europa Ocidental
    Estados Unidos | Leste dos EUA ou Centro-oeste dos EUA

    A localização geográfica especificada para o projeto só é usada para armazenar os metadados coletados das VMs locais. Você pode selecionar qualquer região de destino para a migração real.
4. Em **Selecionar ferramenta de avaliação**, selecione **Ignorar a adição de uma ferramenta de avaliação por enquanto** > **Avançar**.
5. Em **Selecionar ferramenta de migração**, selecione **Migrações para Azure: Migração de Servidor** > **Avançar**.
6. Em **Examinar + adicionar ferramentas**, examine as configurações e clique em **Adicionar ferramentas**
7. Depois de adicionar a ferramenta, ela aparecerá no projeto de Migrações para Azure > **Servidores** > **Ferramentas de migração**.

## <a name="set-up-the-replication-appliance"></a>Configurar o dispositivo de replicação

A primeira etapa da migração é configurar o dispositivo de replicação. O dispositivo de replicação é uma VM VMware local única e altamente disponível que hospeda estes componentes:

- **Servidor de configuração**: O servidor de configuração coordena a comunicação entre o ambiente local e o Azure e gerencia a replicação de dados.
- **Servidor de processo**: O servidor de processo atua como um gateway de replicação. Ele recebe dados de replicação, otimiza-os com caching, compactação e criptografia e os envia para uma conta de armazenamento em cache no Azure. O servidor de processo também instala o agente do Serviço de Mobilidade nas VMs que você deseja replicar e executa a descoberta automática de VMs em VMware locais.


Para configurar o dispositivo de replicação, baixe um modelo OVA (Open Virtualization Application) preparado. Você importa o modelo para o VMware e cria a VM do dispositivo de replicação. 

### <a name="download-the-replication-appliance-template"></a>Baixar o modelo do dispositivo de replicação

Baixe o modelo da seguinte maneira:

1. No projeto Migrações para Azure, clique em **Servidores** em **Metas de Migração**.
2. Em **Migrações para Azure – Servidores** > **Migrações para Azure: Migração de Servidor**, clique em **Descobrir**.

    ![Descobrir VMs](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. Em **Descobrir computadores** > **Os computadores estão virtualizados?** , clique em **Sim, com o hipervisor do VMware vSphere**.
4. Em **Como você deseja migrar?** , selecione **Usando a replicação baseada em agente**.
5. Em **Região de destino**, selecione a região do Azure para a qual deseja migrar os computadores.
6. Selecione **Confirme se a região de destino da migração é nome-da-região**.
7. Clique em **Criar recursos**. Isso criará um cofre do Azure Site Recovery em segundo plano.
    - Você não poderá alterar a região de destino desse projeto depois de clicar nesse botão.
    - Todas as migrações seguintes serão feitas para essa região.

    ![Criar cofre de Serviços de Recuperação](./media/tutorial-migrate-vmware-agent/create-resources.png)

8. Em **Deseja instalar um novo dispositivo de replicação?** , selecione **Instalar um dispositivo de replicação**.
9. Clique em **Baixar** para baixar o dispositivo de replicação. Isso baixa um modelo OVF que você usa para criar uma VM VMware que executa o dispositivo.
    ![Baixar OVA](./media/tutorial-migrate-vmware-agent/download-ova.png)
10. Observe o nome do grupo de recursos e o cofre dos Serviços de Recuperação. Você precisa deles durante a implantação do dispositivo.


### <a name="import-the-template-in-vmware"></a>Importar o modelo para a VMware

Depois de baixar o modelo OVF, você o importa para o VMware para criar o aplicativo de replicação em uma VM VMware que executa o Windows Server 2016.

1. Entre no servidor VMware vCenter ou no host vSphere ESXi com o VMWare vSphere Client.
2. No menu **Arquivo**, selecione **Implantar Modelo de OVF** para iniciar o **Implante o assistente de modelo de OVF**. 
3. Em **Selecionar origem**, insira o local do OVF baixado.
4. Em **Revisar detalhes**, selecione **Avançar**.
5. Em **Selecionar nome e pasta** e **Selecionar configuração**, aceite as configurações padrão.
6. Em **Selecionar armazenamento** > **Selecionar formato de disco virtual**, para obter o melhor desempenho, selecione **Thick Provision Eager Zeroed**.
7. No restante das páginas do assistente, aceite as configurações padrão.
8. Em **Pronto para concluir**, para configurar a VM com as configurações padrão, selecione **Ligar após a implantação** > **Concluir**.

   > [!TIP]
   > Se você quiser incluir um NIC adicional, limpe **Ligar após a implantação** > **Concluir**. Por padrão, o modelo contém um único NIC. É possível incluir NICs adicionais após a implantação.

### <a name="kick-off-replication-appliance-setup"></a>Iniciar a instalação do dispositivo de replicação

1. No console do cliente VMWare vSphere Client, ative a VM.
2. A VM será inicializada com uma experiência de instalação do Windows Server 2016. Aceite o contrato de licença e insira uma senha de administrador.
3. Após a conclusão da instalação, entre na VM como administrador usando a senha de administrador.
4. Na primeira vez que você entrar, a ferramenta de instalação do dispositivo de replicação (Ferramenta de Configuração do Azure Site Recovery) iniciará em alguns segundos.
5. Insira um nome a ser usado para registrar o dispositivo com a Migração de Servidor de Migrações para Azure. Em seguida, clique em **Próximo**.
6. A ferramenta verifica se a VM pode se conectar ao Azure. Depois que a conexão for estabelecida, selecione **Entrar** para fazer logon na sua assinatura do Azure.
7. Aguarde até a ferramenta terminar de registrar um aplicativo do Azure AD para identificar o dispositivo. O dispositivo é reinicializado.
1. Entre novamente no computador. Em alguns segundos, o Assistente de Gerenciamento do Servidor de Configuração iniciará automaticamente.

### <a name="register-the-replication-appliance"></a>Registrar o dispositivo de replicação

Conclua a configuração e o registro do dispositivo de replicação.

1. No Assistente de Gerenciamento do Servidor de Configuração, selecione **Configurar conectividade**.
2. Selecione a NIC (por padrão, há apenas uma NIC) que o dispositivo de replicação usa para a descoberta da VM e para fazer efetuar push de uma instalação do serviço de Mobilidade em computadores de origem.
3. Selecione a NIC que o dispositivo de replicação usa para conectividade com o Azure. Em seguida, selecione **Salvar**. Não é possível alterar essa configuração depois de ela ter sido definida.
4. Se o dispositivo estiver localizado atrás de um servidor proxy, você precisará especificar as configurações de proxy.
    - Especifique o nome do proxy como **http://ip-address** ou **http://FQDN** . Não há suporte para servidores proxy HTTPS.
5. Quando for solicitada a assinatura, os grupos de recursos e os detalhes do cofre, adicione os detalhes que você anotou ao baixar o modelo do dispositivo.
6. Em **Instalar software de terceiros**, aceite o contrato de licença. Selecione **Baixar e Instalar** para instalar o MySQL Server.
7. Selecione **Instalar VMware PowerCLI**. Verifique se todas as janelas de navegador estão fechadas antes de fazer isso. Depois selecione **Continuar**.
8. Em **Validar configuração de dispositivo**, os pré-requisitos serão verificados antes de continuar.
9. Em **Configurar vCenter Server/servidor vSphere ESXi**, insira o FQDN ou endereço IP do vCenter Server ou o host vSphere, onde as VMs que deseja replicar estão localizadas. Insira a porta na qual o servidor está escutando. Insira um nome amigável a ser usado para o servidor VMware no cofre.
10. Insira as credenciais para a conta que você [criou](#prepare-an-account-for-automatic-discovery) para a descoberta do VMware. Selecione **Adicionar** > **Continuar**.
11. Em **Configurar credenciais da máquina virtual**, insira as credenciais que você [criou](#prepare-an-account-for-mobility-service-installation) para a instalação por push do serviço de mobilidade ao habilitar a replicação para VMs.  
    - Para computadores do Windows, a conta precisa de privilégios de administrador local nos computadores que você deseja replicar.
    - Para o Linux, forneça detalhes para a conta raiz.
12. Selecione **Finalizar configuração** para concluir o registro.


Depois que o dispositivo de replicação é registrado, a Avaliação de Servidor de Migrações para Azure conecta-se aos servidores VMware usando as configurações especificadas e descobre as VMs. Você pode exibir as VMs descobertas em **Gerenciar** > **Itens descobertos** na guia **Outro**.


## <a name="replicate-vms"></a>Replicar VMs

1. No projeto das Migrações para Azure > **Servidores**, **Migrações para Azure: Migração de Servidor**, clique em **Replicar**.

    ![Replicar VMs](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. Em **Replicar**, > **Configurações de origem** > **Seus computadores estão virtualizados?** , selecione **Sim, com o VMware vSphere**.
3. Em **Dispositivo local**, selecione o nome do dispositivo de Migrações para Azure que você configurou.
4. No **vCenter Server**, especifique o nome do vCenter Server que gerencia as VMs ou do servidor vSphere no qual as VMs estão hospedadas.
5. Em **Servidor de Processo**, selecione o nome do dispositivo de replicação.
6. Em **Credenciais de convidado**, especifique a conta do administrador de VM que será usada para a instalação por push do serviço de Mobilidade. Em seguida, clique em **Próximo: Máquinas virtuais**.

    ![Replicar VMs](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. Em **Máquinas Virtuais**, selecione os computadores que você deseja replicar.

    - Se você tiver executado uma avaliação das VMs, poderá aplicar recomendações de dimensionamento de VM e de tipo de disco (Premium/Standard) dos resultados da avaliação. Para fazer isso, em **Importar configurações de migração de uma avaliação de Migrações para Azure?** , selecione a opção **Sim**.
    - Se você não tiver executado uma avaliação ou não quiser usar as configurações de avaliação, selecione a opção **Não**.
    - Se você optou por usar a avaliação, selecione o grupo de VMs e o nome da avaliação.

8. Verifique cada VM que você deseja migrar. Em seguida, clique em **Próximo: configurações de destino**.
9. Em **Configurações de destino**, selecione a assinatura e a região de destino para a qual você migrará e especifique o grupo de recursos no qual as VMs do Azure residirão após a migração.
10. Em **Rede Virtual**, selecione a VNet/sub-rede do Azure na qual as VMs do Azure serão ingressadas após a migração.
11. Em **Benefício Híbrido do Azure**:

    - Selecione **Não** se não desejar aplicar o Benefício Híbrido do Azure. Em seguida, clique em **Próximo**.
    - Selecione **Sim** se você tiver computadores Windows Server cobertos com assinaturas ativas do Software Assurance ou do Windows Server e quiser aplicar o benefício aos computadores que estão sendo migrados. Em seguida, clique em **Próximo**.

12. Em **Computação**, examine o nome da VM, o tamanho, o tipo de disco do sistema operacional e o conjunto de disponibilidade. As VMs devem estar em conformidade com os [requisitos do Azure](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements).

    - **Tamanho da VM**: se você estiver usando recomendações de avaliação, o menu suspenso de tamanho da VM conterá o tamanho recomendado. Caso contrário, as Migrações para Azure escolherão um tamanho com base na correspondência mais próxima na assinatura do Azure. Como alternativa, escolha um tamanho manual em **Tamanho da VM do Azure**. 
    - **Disco do SO**: especifique o disco do sistema operacional (inicialização) para a VM. O disco do sistema operacional é o disco que tem o carregador de inicialização e o instalador do sistema operacional. 
    - **Conjunto de disponibilidade**: se a VM deve estar em um conjunto de disponibilidade do Azure após a migração, especifique-o. O conjunto precisa estar no grupo de recursos de destino especificado para a migração.

13. Em **Discos**, especifique se os discos da VM devem ser replicados para o Azure e selecione o tipo de disco (discos gerenciados HDD/SSD standard ou premium) no Azure. Em seguida, clique em **Próximo**.
    - Você pode excluir discos da replicação.
    - Se você excluir os discos, eles não estarão presentes na VM do Azure após a migração. 

14. Em **Examinar e iniciar a replicação**, examine as configurações e clique em **Replicar** para iniciar a replicação inicial dos servidores.

> [!NOTE]
> É possível atualizar as configurações de replicação a qualquer momento antes do início da replicação em **Gerenciar** > **Computadores em replicação**. Não é possível alterar as configurações após o início da replicação.




## <a name="track-and-monitor"></a>Acompanhar e monitorar

- Quando você clica em **Replicar**, um trabalho Iniciar Replicação é iniciado. 
- Quando o trabalho Iniciar Replicação é concluído com êxito, os computadores começam sua replicação inicial para o Azure.
- Depois que a replicação inicial é concluída, a replicação delta é iniciada. As alterações incrementais em discos locais são replicadas periodicamente para os discos de réplica no Azure.


É possível acompanhar o status do trabalho nas notificações do portal.

![Acompanhar o trabalho](./media/tutorial-migrate-vmware-agent/jobs.png)

É possível monitorar o status de replicação clicando em **Replicando servidores** em **Migrações para Azure: Migração de Servidor**.
![Monitorar a replicação](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

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

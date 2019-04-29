---
title: Hospede novamente um aplicativo Contoso com a migração para máquinas virtuais do Azure com o Azure Site Recovery | Microsoft Docs
description: Saiba como reutilizar um aplicativo local com uma migração de migração de máquinas locais para o Azure, usando o serviço Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 4a6ed900753747c1d5bf394aced54da11177320f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60668077"
---
# <a name="contoso-migration-rehost-an-on-premises-app-to-azure-vms"></a>Migração para Contoso: Hospedar novamente um aplicativo local para VMs do Azure


Este artigo demonstra como a Contoso hospeda novamente o aplicativo SmartHotel360 no Azure, migrando as VMs do aplicativo para as VMs do Azure.


Este documento é um de uma série de artigos que mostra como a empresa fictícia Contoso migra os recursos locais para a nuvem do Microsoft Azure. A série inclui informações básicas e cenários que ilustram a configuração de uma infraestrutura de migração, avaliando recursos locais para migração e executando diferentes tipos de migrações. Cenários aumentam em complexidade. Adicionaremos mais artigos com o tempo.

**Artigo** | **Detalhes** | **Status**
--- | --- | ---
[Artigo 1: Visão geral](contoso-migration-overview.md) | Visão geral da série de artigos, da estratégia de migração da Contoso e dos aplicativos de exemplo usados na série. | Disponível
[Artigo 2: Implantar a infraestrutura do Azure](contoso-migration-infrastructure.md) | A Contoso prepara a infraestrutura local e a infraestrutura do Azure para migração. A mesma infraestrutura é usada para todos os artigos de migração da série. | Disponível
[Artigo 3: Avaliar recursos locais para migração para o Azure](contoso-migration-assessment.md)  | A Contoso executa uma avaliação do aplicativo SmartHotel360 local em execução no VMware. A Contoso avalia as VMs do aplicativo usando o serviço de Migrações para Azure e o banco de dados do SQL Server do aplicativo usando o Assistente de Migração de Dados. | Disponível
[Artigo 4: Hospedar novamente um aplicativo em uma VM do Azure e uma Instância Gerenciada do Banco de Dados SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | A Contoso executa uma migração lift-and-shift para o Azure no aplicativo SmartHotel360 local. A Contoso migra a VM front-end do aplicativo usando o [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). A Contoso migra o banco de dados do aplicativo para uma Instância Gerenciada do Banco de Dados SQL do Azure usando o [Serviço de Migração de Banco de Dados do Azure](https://docs.microsoft.com/azure/dms/dms-overview). | Disponível
Artigo 5: Hospedar novamente um aplicativo em VMs do Azure | A Contoso migra as VMs de aplicativos SmartHotel360 para VMs do Azure usando o serviço do Site Recovery. | Este artigo
[Artigo 6: Hospedar novamente um aplicativo em VMs do Azure e em um grupo de disponibilidade AlwaysOn do SQL Server](contoso-migration-rehost-vm-sql-ag.md) | A Contoso migra o aplicativo SmartHotel360. A Contoso usa o Site Recovery para migrar as VMs do aplicativo. Ele usa o Serviço de Migração de Banco de Dados para migrar o banco de dados do aplicativo para um cluster SQL Server protegido por um grupo de disponibilidade AlwaysOn. | Disponível
[Artigo 7: Hospedar novamente um aplicativo do Linux em VMs do Azure](contoso-migration-rehost-linux-vm.md) | A Contoso conclui uma migração lift-and-shift do aplicativo osTicket do Linux para as VMs do Azure, usando o Azure Site Recovery | Disponível
[Artigo 8: Hospedar novamente um aplicativo Linux em VMs do Azure e MySQL do Azure](contoso-migration-rehost-linux-vm-mysql.md) | A Contoso migra o aplicativo osTicket do Linux para as VMs do Azure usando o Azure Site Recovery e migra o banco de dados do aplicativo para uma instância do MySQL Server no Azure usando o MySQL Workbench. | Disponível
[Artigo 9: Refatorar um aplicativo em aplicativos Web do Azure e no Banco de Dados SQL do Azure](contoso-migration-refactor-web-app-sql.md) | A Contoso migra o aplicativo SmartHotel360 para um aplicativo Web do Azure e migra o banco de dados de aplicativo para uma instância do Azure SQL Server com o Assistente de Migração de Dados | Disponível
[Artigo 10: Refatorar um aplicativo Linux em aplicativos Web do Azure e no MySQL do Azure](contoso-migration-refactor-linux-app-service-mysql.md) | A Contoso migra seu aplicativo osTicket do Linux para um aplicativo Web do Azure em várias regiões do Azure usando o Gerenciador de Tráfego do Azure, integrado ao GitHub para entrega contínua. A Contoso migra o banco de dados do aplicativo para uma instância do Banco de Dados do Azure para MySQL. | Disponível
[Artigo 11: Refatorar o TFS no Azure DevOps Services](contoso-migration-tfs-vsts.md) | A Contoso migra a implantação do Team Foundation Server local para o Azure DevOps Services no Azure. | Disponível
[Artigo 12: Recriar um aplicativo nos contêineres do Azure e no Banco de Dados SQL do Azure](contoso-migration-rearchitect-container-sql.md) | A Contoso migra o aplicativo SmartHotel para o Azure. Em seguida, ela cria novamente a camada da Web do aplicativo como um contêiner do Windows em execução no Azure Service Fabric e o banco de dados com o Banco de Dados SQL do Azure. | Disponível
[Artigo 13: Recompilar um aplicativo no Azure](contoso-migration-rebuild.md) | A Contoso recompila o aplicativo SmartHotel usando diversas funcionalidades e serviços do Azure, incluindo o Serviço de Aplicativo do Azure, o AKS (Serviço de Kubernetes do Azure), o Azure Functions, os Serviços Cognitivos do Azure e o Azure Cosmos DB. | Disponível
[Artigo 14: Escalar uma migração para o Azure](contoso-migration-scale.md) | Depois de experimentar combinações de migração, a Contoso se prepara para dimensionar para uma migração completa para o Azure. | Disponível


Neste artigo, a Contoso vai migrar do Windows de duas camadas. NET SmartHotel360 aplicativo executado em VMs VMware, para o Azure. Se você quiser usar esse aplicativo, ele é fornecido como software livre e você pode baixá-lo do [GitHub](https://github.com/Microsoft/SmartHotel360).



## <a name="business-drivers"></a>Geradores de negócios

A equipe de liderança de TI trabalhou em conjunto com parceiros comerciais para entender o que eles desejam obter com essa migração:

- **Lidando com o crescimento da empresa**: A Contoso está crescendo e, como resultado, há pressão sobre seus sistemas e infraestrutura locais.
- **Limitar riscos**: O aplicativo SmartHotel360 é crítico para o negócio da Contoso. Ela deseja passar o aplicativo para o Azure sem nenhum risco.
- **Estender**: A Contoso não quer modificar o aplicativo, mas deseja garantir que ele fique estável.


## <a name="migration-goals"></a>Metas de migração

A equipe de nuvem da Contoso fixou metas para esta migração. Essas metas são usadas para determinar o melhor método de migração:

- Após a migração, o aplicativo no Azure deve ter os mesmos recursos de desempenho de hoje no VMware. O aplicativo permanecerá essencial tanto na nuvem quanto localmente.
- A Contoso não quer investir nesse aplicativo. Ele é importante para os negócios, mas, em sua forma atual, a Contoso apenas deseja passá-lo com segurança para a nuvem.
- A Contoso não quer alterar o modelo de operações para este aplicativo. A Contoso deseja interagir com ele na nuvem da mesma forma que faz agora.
- A Contoso não quer alterar nenhuma funcionalidade do aplicativo. Somente o local do aplicativo será alterado.


## <a name="solution-design"></a>Design da solução

Depois de fixar metas e requisitos, a Contoso cria e examina uma solução de implantação e identifica o processo de migração, incluindo os serviços do Azure que ela usará para a migração.

### <a name="current-app"></a>Aplicativo atual

- O aplicativo está em camadas em duas VMs (**WEBVM** e **SQLVM**).
- As VMs estão localizadas no host VMware ESXi **contosohost1.contoso.com** (versão 6.5).
- O ambiente VMware é gerenciado pelo vCenter Server 6.5 (**vcenter.contoso.com**) em execução em uma VM.
- A Contoso tem um datacenter local (contoso-datacenter), com um controlador de domínio local (**contosodc1**).

### <a name="proposed-architecture"></a>Arquitetura proposta

- Como o aplicativo é uma carga de trabalho de produção, as VMs no Azure residirão no grupo de recursos de produção ContosoRG.
- As VMs do aplicativo serão migradas para a região principal do Azure (Leste dos EUA 2) e colocadas na rede de produção (VNET-PROD-EUS2).
- A VM do front-end da Web residirá na sub-rede do front-end (PROD-FE-EUS2), na rede de produção.
- A VM do banco de dados residirá na sub-rede do banco de dados (PROD-DB-EUS2) na rede de produção.
- As VMs locais no datacenter da Contoso serão descomissionadas após a migração.

![Arquitetura de cenário](./media/contoso-migration-rehost-vm/architecture.png)

### <a name="database-considerations"></a>Considerações do banco de dados

Como parte do processo para criar a solução, a Contoso fez uma comparação de recursos entre o Banco de Dados SQL do Azure e o SQL Server. As seguintes considerações os ajudaram a optar pelo SQL Server em execução em uma VM de IaaS do Azure:

- Usar uma VM do Azure que executa o SQL Server parece ser uma solução ideal se a Contoso precisa personalizar o sistema operacional ou o servidor de banco de dados ou se ela deseja colocar a executar aplicativos de terceiros na mesma VM.
- Com o Software Assurance, futuramente, a Contoso poderá trocar suas licenças existentes por taxas com desconto em uma Instância Gerenciada do Banco de Dados SQL usando o Benefício Híbrido do Azure para SQL Server. Com essa opção, é possível economizar até 30% na Instância Gerenciada.



### <a name="solution-review"></a>Análise de solução

A Contoso avalia o design proposto reunindo uma lista de prós e contras.

**Consideração** | **Detalhes**
--- | ---
**Prós** | As duas VMs do aplicativo serão movidas para o Azure sem alterações, simplificando a migração.<br/><br/> Como a Contoso está usando o lift-and-shift para as duas VMs do aplicativo, não é necessária nenhuma ferramenta de configuração ou de migração especial para o banco de dados do aplicativo.<br/><br/> A Contoso pode usar seu investimento em Software Assurance usando o Benefício Híbrido do Azure.<br/><br/> A Contoso manterá o controle total sobre as VMs do aplicativo no Azure.
**Contras** | A WEBVM e a SQLVM estão em execução no Windows Server 2008 R2. O sistema operacional é compatível com o Azure para funções específicas (julho de 2018). [Saiba mais](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).<br/><br/> As camadas da Web e de dados do aplicativo continuarão sendo um ponto único de failover.</br><br/> A SQLVM está em execução no SQL Server 2008 R2, que não faz parte do suporte base. No entanto, há suporte para ele nas VMs do Azure (julho de 2018). [Saiba mais](https://support.microsoft.com/en-us/help/956893).<br/><br/> A Contoso precisará continuar dando suporte ao aplicativo como VMs do Azure, em vez de passar para um serviço gerenciado, como o Serviço de Aplicativo do Azure e o Banco de Dados SQL do Azure.



### <a name="migration-process"></a>Processo de migração

A Contoso migrará o front-end do aplicativo e as VMs do banco de dados para as VMs do Azure usando o Site Recovery:

- Como uma primeira etapa, a Contoso prepara e configura os componentes do Azure para o Site Recovery e prepara a infraestrutura do VMware local.
- A Contoso já tem a [infraestrutura do Azure](contoso-migration-infrastructure.md) pronta, portanto, basta adicionar alguns dos componentes do Azure especificamente para o Site Recovery.
- Com tudo preparado, a Contoso pode iniciar a replicação das VMs.
- Depois que a replicação estiver habilitada e funcionando, a Contoso migrará a VM efetuando failover para o Azure.

![Processo de migração](./media/contoso-migration-rehost-vm/migraton-process.png)



### <a name="azure-services"></a>Serviços do Azure

**Serviço** | **Descrição** | **Custo**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | O serviço coordena e gerencia a migração e a recuperação de desastre para VMs do Azure e servidores físicos e de VMs locais.  | Durante a replicação para o Azure, são gerados encargos do Armazenamento do Azure. As VMs do Azure são criadas e incorrem em encargos quando ocorre failover. [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre encargos e preços.


## <a name="prerequisites"></a>Pré-requisitos

Aqui está o que a Contoso precisa para executar esse cenário.

**Requisitos** | **Detalhes**
--- | ---
**Assinatura do Azure** | A Contoso criou assinaturas em um artigo anterior desta série. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Se você criar uma conta gratuita, será o administrador da assinatura e poderá executar todas as ações.<br/><br/> Se você usar uma assinatura existente e não for o administrador, será necessário trabalhar com o administrador para receber permissões de Proprietário ou de Colaborador.<br/><br/> Se forem necessárias permissões mais granulares, leia [este artigo](../site-recovery/site-recovery-role-based-linked-access-control.md).
**Infraestrutura do Azure** | [ Saiba como ](contoso-migration-infrastructure.md) a Contoso configurou uma infraestrutura do Azure.<br/><br/> Saiba mais sobre os requisitos de [rede](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) e [armazenamento](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) específicos para o Site Recovery.
**Servidores locais** | vCenter Servers locais devem estar executando a versão 5.5, 6.0 ou 6.5<br/><br/> Hosts ESXi devem executar a versão 5.5, 6.0 ou 6.5<br/><br/> Uma ou mais VMs do VMware devem estar em execução no host ESXi.
**VMs locais** | As VMs devem atender aos [requisitos do Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>Etapas do cenário

Aqui está como os administradores da Contoso executarão a migração:

> [!div class="checklist"]
> * **Etapa 1: Preparar o Azure para Site Recovery**: Criam uma conta de armazenamento do Azure para armazenar dados replicados e um cofre dos Serviços de Recuperação.
> * **Etapa 2: Preparar VMware local para Site Recovery**: Preparam contas para a descoberta de VMs e a instalação de agentes e preparam para conectar VMs do Azure após failover.
> * **Etapa 3: Replicar VMs**: Configuram a replicação e começam a replicar as VMs para armazenamento do Azure.
> * **Etapa 4: Migrar VMs com o Site Recovery**: Executam um failover de teste para certificar-se de que tudo está funcionando e, em seguida, executam um failover completo para migrar as VMs para o Azure.




## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>Etapa 1: Preparar o Azure para o serviço do Site Recovery

Aqui estão os componentes do Azure que a Contoso precisa para migrar as máquinas virtuais para o Azure:

- Uma rede virtual na qual as VMs do Azure serão localizadas quando forem criadas durante o failover.
- Uma conta de armazenamento do Azure para armazenar os dados replicados.
- Um cofre do Recovery Services no Azure.

Eles configuram estes da seguinte forma:

1. Configurar uma rede – a Contoso já configurou uma rede que pode ser usada para o Site Recovery quando [implantou a infraestrutura do Azure](contoso-migration-infrastructure.md)

    - O aplicativo SmartHotel360 é um aplicativo de produção e as VMs serão migradas para a rede de produção do Azure (VNET-PROD-EUS2) na região primária do leste dos EUA 2.
    - As duas máquinas virtuais serão colocadas no grupo de recursos ContosoRG, que é usado para recursos de produção.
    - A VM do front-end do aplicativo (WEBVM) será migrada para a sub-rede do front-end (PROD-FE-EUS2), na rede de produção.
    - A VM do banco de dados do aplicativo (SQLVM) será migrada para a sub-rede do banco de dados (PROD-DB-EUS2), na rede de produção.

2. Configurar uma conta de armazenamento – a Contoso cria uma conta de armazenamento do Azure (contosovmsacc20180528) na região primária.
   - A conta de armazenamento deve estar na mesma região do cofre de Serviços de Recuperação.
   - Eles usam uma conta de propósito geral, com armazenamento padrão e replicação de LRS.

     ![Armazenamento de recuperação de site](./media/contoso-migration-rehost-vm/asr-storage.png)

3. Criar um cofre – com a rede e a conta de armazenamento prontas, a Contoso agora cria um cofre dos Serviços de Recuperação (ContosoMigrationVault) e coloca-o no grupo de recursos ContosoFailoverRG na região Leste dos EUA 2 primária.

    ![Cofre dos Serviços de Recuperação](./media/contoso-migration-rehost-vm/asr-vault.png)

**Precisa de mais ajuda?**

[Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) como configurar o Azure para o Site Recovery.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>Etapa 2: Preparar VMware local para Site Recovery

Aqui está o que a Contoso prepara localmente:

- Uma conta no vCenter Server ou no host ESXi do vSphere para automatizar a descoberta de VM.
- Uma conta que permite a instalação automática do serviço de Mobilidade nas VMs VMware.
- Configurações de VM locais, para que a Contoso possa se conectar a máquinas virtuais do Azure replicadas após o failover.


### <a name="prepare-an-account-for-automatic-discovery"></a>Preparar uma conta para a descoberta automática

O Site Recovery precisa de acesso aos servidores VMware para:

- Descobrir VMs automaticamente.
- Orquestrar a replicação, o failover e o failback para VMs.
- Pelo menos uma conta somente leitura é necessária. A conta deve ser capaz de executar operações como criação e remoção de discos e ativação de VMs.

Os administradores da Contoso configuram a conta da seguinte maneira:

1. Eles criam uma função no nível do vCenter.
2. Eles atribuir as permissões necessárias a essa função.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparar uma conta para a instalação do serviço de Mobilidade

O serviço de Mobilidade deve ser instalado em cada VM.

- O Site Recovery pode fazer uma instalação automática por push do serviço de Mobilidade quando a replicação da VM for habilitada.
- Uma conta é necessária, para que o Site Recovery possa acessar as máquinas virtuais para a instalação por push. Especifique essa conta ao configurar a replicação.
- A conta pode ser de domínio ou local, com permissões para instalação nas VMs.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar para conectar VMs do Azure após o failover

Após o failover, a Contoso deseja se conectar a máquinas virtuais do Azure. Para fazer isso, os administradores da Contoso fazem o seguinte antes da migração:

1. Para acesso pela internet eles:

   - Habilitam o RDP na VM local antes do failover.
   - Assegure-se de que as regras TCP e UDP sejam incluídas para o perfil **Público**.
   - Verifique se o RDP é permitido no **Firewall do Windows**  >  **Aplicativos permitidos** para todos os perfis.

2. Para acesso por VPN site-to-site, eles:

   - Ative o RDP na máquina no local.
   - Permitir o RDP no **Firewall do Windows**  ->  **Aplicativos e recursos permitidos**, para **Domínio e redes particulares**.
   - Defina a política de SAN do sistema operacional na VM local para **OnlineAll**.

Além disso, quando eles executam um failover, precisam verificar o seguinte:

- Não deve haver atualizações do Windows pendentes na VM ao acionar um failover. Se houver, eles não poderão efetuar login na VM até que a atualização seja concluída.
- Após o failover, eles podem verificar **Diagnósticos de inicialização** para exibir uma captura de tela da VM. Se isso não funcionar, eles devem verificar se a VM está em execução e revisar essas [ dicas de solução de problemas ](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


**Precisa de mais ajuda?**

- [Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) como criar e atribuir uma função para a descoberta automática.
- [Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) como criar uma conta para a instalação do serviço de mobilidade por push.


## <a name="step-3-replicate-the-on-premises-vms"></a>Etapa 3: Replicar as VMs locais

Antes que os administradores da Contoso possam executar uma migração para o Azure, eles precisam configurar e habilitar a replicação.

### <a name="set-a-replication-goal"></a>Definir uma meta de replicação

1. No cofre, sob o nome do vault (ContosoVMVault), eles selecionam uma meta de replicação (**Introdução**  >  **Recuperação do site**  >  **Preparar a infraestrutura**.
2. Eles especificam que suas máquinas estão localizadas no local, sendo executadas no VMware e replicadas no Azure.

    ![Meta de replicação](./media/contoso-migration-rehost-vm/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Confirmar planejamento de implantação

Para continuar, eles confirmam que concluíram o planejamento da implantação, selecionando **Sim, eu fiz isso**. Nesse cenário, a Contoso só está migrando duas máquinas virtuais e não é necessário um planejamento de implantação.


### <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Os administradores da Contoso precisam configurar o ambiente de origem. Para fazer isso, eles baixam um modelo OVF e o utilizam para implantar o servidor de configuração do Site Recovery como uma VM VMware local, altamente disponível. Depois que a configuração do servidor tiver sido finalizada, registre-o no cofre.

O servidor de configuração executa vários componentes:

- O componente de servidor de configuração que coordena a comunicação entre o ambiente local e o Azure e gerencia a replicação de dados.
- Servidor em processo que age como um gateway de replicação. Ele recebe dados de replicação, otimiza-os com caching, compactação e criptografia e os envia para o Armazenamento do Azure.
- O servidor de processo também instala o Serviço de Mobilidade nas VMs que você deseja replicar e executa a descoberta automática de VMs VMware locais.



Os administradores da Contoso executam as seguintes etapas:

1. No cofre, eles baixam o modelo OVF em **Preparar a infraestrutura** > **Fonte** > **Servidor de configuração**.
    
    ![Baixe o OVF](./media/contoso-migration-rehost-vm/add-cs.png)

2. Eles importam o modelo para o VMware para criar e implantar a VM.

    ![Modelo de OVF](./media/contoso-migration-rehost-vm/vcenter-wizard.png)

3. Ao ligar a máquina virtual pela primeira vez, ela é inicializada como uma experiência de instalação do Windows Server 2016. Eles aceitam o contrato de licença e inserem uma senha de administrador.
4. Após a conclusão da instalação, entram na VM como administrador. No primeiro logon, a Ferramenta de Configuração do Azure Site Recovery é iniciada por padrão.
5. Na ferramenta, eles especificam um nome para registrar o servidor de configuração no cofre.
6. A ferramenta verifica se a VM pode se conectar ao Azure. Depois que a conexão é estabelecida, entram na assinatura do Azure. As credenciais devem ter acesso ao cofre no qual você deseja registrar o servidor de configuração.

    ![Registrar servidor de configuração](./media/contoso-migration-rehost-vm/config-server-register2.png)

7. A ferramenta executa algumas tarefas de configuração e, em seguida, é reinicializada.
8. Entram na máquina novamente e o Assistente de Gerenciamento do Servidor de Configuração é iniciado automaticamente.
9. No assistente, selecionam o NIC que vai receber o tráfego de replicação. Essa configuração não pode ser alterada depois de definida.
10. Eles selecionam a assinatura, o grupo de recursos e o cofre no qual será registrado o servidor de configuração.
        ![vault](./media/contoso-migration-rehost-vm/cswiz1.png)

10. Eles baixam e instalam o MySQL Server e VMWare PowerCLI.
11. Após a validação, ela especifica o FQDN ou endereço IP do host do servidor vSphere ou vCenter. Eles não alteram a porta padrão e especificam um nome amigável para o servidor no Azure.
12. Eles especificam a conta que criaram para descoberta automática e as credenciais que são usadas para instalar automaticamente o Serviço de Mobilidade. Para computadores do Windows, a conta precisa de privilégios de administrador local nas VMs.

    ![vCenter](./media/contoso-migration-rehost-vm/cswiz2.png)

7. Após a conclusão do registro, no portal do Azure, eles verificam novamente se o servidor de configuração e o VMware Server estão listados na página **Origem** no cofre. A descoberta pode levar 15 minutos ou mais.
8. O Site Recovery então conecta-se aos servidores VMware usando as configurações especificadas e descobre VMs.

### <a name="set-up-the-target"></a>Configurar o destino

Agora os administradores da Contoso especificam as configurações de replicação de destino.

1. Em **Preparar infraestrutura** > **Destino**, selecionam as configurações de destino.
2. O Site Recovery verifica se há uma conta de armazenamento do Azure e rede no local de destino especificado.

### <a name="create-a-replication-policy"></a>Criar uma política de replicação

Agora os administradores da Contoso podem criar uma política de replicação.

1. Em **Preparar infraestrutura** > **Configurações de Replicação** > **Política de Replicação** >  **Criar e Associar**, criam uma política **ContosoMigrationPolicy**.
2. Usam as configurações padrão:
    - **Limite de RPO**: Padrão de 60 minutos. Esse valor define a frequência em que são criados os pontos de recuperação. Um alerta será gerado se a replicação contínua exceder esse limite.
    - **Retenção do ponto de recuperação**. Padrão de 24 horas. Esse valor especifica qual é o tempo da janela de retenção para cada ponto de recuperação. VMs replicadas podem ser recuperadas para qualquer ponto em uma janela.
    - **Frequência de instantâneos consistentes com o aplicativo**. O padrão é de uma hora. Esse valor especifica a frequência com que são criados instantâneos consistentes com o aplicativo.

        ![Criar política de replicação](./media/contoso-migration-rehost-vm/replication-policy.png)

5. A política é associada automaticamente ao servidor de configuração.

    ![Associar política de replicação](./media/contoso-migration-rehost-vm/replication-policy2.png)

### <a name="enable-replication-for-webvm"></a>Habilitar a replicação para WEBVM

Com tudo pronto, os administradores da Contoso agora podem habilitar a replicação para as VMs. Eles começam com a WebVM.

1. Em **Replicar aplicativo** > **Fonte** > **+Replicar**, ela seleciona as configurações de fonte.
2. Eles indicam que desejam ativar as VMs, selecionar o servidor vCenter e o servidor de configuração.

    ![Habilitar a replicação](./media/contoso-migration-rehost-vm/enable-replication1.png)

3. Eles selecionam as configurações de destino, incluindo a conta de armazenamento, a rede do Azure e o grupo de recursos.

    ![Habilitar a replicação](./media/contoso-migration-rehost-vm/enable-replication2.png)

4. Eles selecionam a **WebVM** para replicação, verificam a política de replicação e habilitam a replicação.

   - Neste estágio, eles apenas selecionam a WEBVM porque a VNET e a sub-rede precisam ser selecionadas e as VMs do aplicativo serão colocadas em sub-redes diferentes.
   - O Site Recovery instala automaticamente o serviço de Mobilidade na VM quando a replicação é habilitada.

     ![Habilitar a replicação](./media/contoso-migration-rehost-vm/enable-replication3.png)

5. Eles rastreiam o progresso da replicação em **Trabalhos**. Após o trabalho de **Finalizar Proteção** ser executado, o computador estará pronto para failover.
6. Em **Essentials** no portal do Azure, eles podem ver a estrutura das VMs que estão sendo replicadas para o Azure.


### <a name="enable-replication-for-sqlvm"></a>Habilitar a replicação para SQLVM

Agora os administradores podem começar a replicar a máquina SQLVM, usando o mesmo processo apresentado acima.

1. Eles selecionam as configurações da fonte.

    ![Habilitar a replicação](./media/contoso-migration-rehost-vm/enable-replication1.png)

2. Eles, em seguida, especificam as configurações de destino.

     ![Habilitar a replicação](./media/contoso-migration-rehost-vm/enable-replication2-sqlvm.png)

3. Eles selecionam SQLVM para replicação.

    ![Habilitar a replicação](./media/contoso-migration-rehost-vm/enable-replication3-sqlvm.png)

4. Eles aplicam a mesma política de replicação que foi usada para WEBVM e habilitam a replicação.

    ![Modo de exibição de infraestrutura](./media/contoso-migration-rehost-vm/essentials.png)

**Precisa de mais ajuda?**

- Você pode ler uma explicação completa de todas essas etapas em [Configurar a recuperação de desastres para máquinas virtuais do VMware locais](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- As instruções detalhadas estão disponíveis para ajudá-lo a [configurar o ambiente da fonte](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [implantar o servidor de configuração](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), e [definir configurações de replicação](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- Você pode saber mais sobre a [ habilitação da replicação](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-4-migrate-the-vms"></a>Etapa 4: Migrar as VMs

Os administradores da Contoso executam um failover de teste rápido e, em seguida, um failover completo para migrar as máquinas virtuais.

### <a name="run-a-test-failover"></a>Execute um teste de failover

Um failover de teste ajuda a garantir que tudo está funcionando como esperado.

1. Eles executam um failover de teste, até o último momento disponível (**Último processamento**).
2. Selecionam **Desligar o computador antes do início do failover**, para que o Site Recovery tente desligar a VM da fonte antes de disparar o failover. O failover continuará mesmo o desligamento falhar.
3. O failover de teste é executado:

    - Uma verificação de pré-requisitos será executada para conferir se todas as condições exigidas para a migração foram cumpridas.
    - O failover processa os dados para que uma VM do Azure possa ser criada. Se o ponto de recuperação mais recente for selecionado, um ponto de recuperação será criado nos dados.
    - Uma VM do Azure é criada usando os dados processados na etapa anterior.

3. Após o failover ser concluído, a réplica da VM do Azure aparecerá no portal do Azure. Verificam se a VM tem o tamanho apropriado, está conectada à rede correta e está em execução.
4. Depois de verificar o failover de teste, eles limpam o failover e registram e salvam todas as observações.

### <a name="create-and-customize-a-recovery-plan"></a>Criar e personalizar um plano de recuperação

 Depois de verificar se o failover de teste funciona como esperado, os administradores da Contoso criam um plano de recuperação para a migração.

- Um plano de recuperação especifica a ordem na qual o failover ocorre e indica como as VMs do Azure ficarão online no Azure.
- Como o aplicativo é de duas camadas, eles personalizam o plano de recuperação para que os dados de VM (SQLVM) sejam iniciados antes do front-end (WEBVM).

1. Em **planos de recuperação (recuperação de Site)** > **+ plano de recuperação**, criam um plano e adicione as máquinas virtuais a ele.

    ![Plano de recuperação](./media/contoso-migration-rehost-vm/recovery-plan.png)

2. Depois de criar o plano, eles o personalizam (**Planos de recuperação** > **SmartHotelMigrationPlan** > **Personalizar**).
2.  Removem a WEBVM do **Grupo 1: Iniciar**. Isso garante que a primeira ação de início afete somente a SQLVM.
3.  Em **+Grupo** > **Adicionar itens protegidos**, aumentam a WEBVM para Grupo 2: Iniciar. As VMs precisam estar em dois grupos diferentes.


### <a name="migrate-the-vms"></a>Migrar as VMs


Agora os administradores da Contoso podem executar um failover completo para concluir a migração.

1. Eles selecionam o plano de recuperação > **Failover**.
2. Eles selecionam fazer o failover para o último ponto de recuperação e que o Site Recovery deve tentar desligar a VM local antes de acionar o failover. Podem acompanhar o progresso do failover na página **Trabalhos**.

    ![Failover](./media/contoso-migration-rehost-vm/failover1.png)


3. Após o failover, eles verificam se a VM do Azure aparece conforme o esperado no portal do Azure.

    ![Failover](./media/contoso-migration-rehost-vm/failover2.png)

3. Após a verificação, eles concluem a migração para cada VM. Isso interrompe a replicação da VM e interrompe a cobrança do Site Recovery para a VM.

    ![Failover](./media/contoso-migration-rehost-vm/failover3.png)

**Precisa de mais ajuda?**

- [Saiba mais](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) sobre como executar failovers de teste.
- [Saiba](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) como criar um plano de recuperação.
- [Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) como fazer failover para o Azure.

## <a name="clean-up-after-migration"></a>Limpar após a migração

Com a migração concluída, as camadas de aplicativos do SmartHotel360 agora estão em execução nas VMs do Azure.

Now, Contoso needs to complete these cleanup steps:

- Remover a máquina WEBVM do inventário do vCenter.
- Remova a máquina do SQLVM do inventário do vCenter.
- Remover WEBVM e SQLVM dos trabalhos de backup locais.
- Atualizar a documentação interna para mostrar o novo local e endereços IP para as máquinas virtuais.
- Examinar todos os recursos que interagem com as máquinas virtuais e atualizar configurações relevantes ou documentação para refletir a nova configuração.

## <a name="review-the-deployment"></a>Revisar a implantação

Com o aplicativo em execução, a Contoso agora precisa utilizá-lo totalmente e protegê-lo no Azure.

### <a name="security"></a>Segurança

A equipe de segurança da Contoso revisa as VMs do Azure, para determinar os problemas de segurança.

- Para controlar o acesso, a equipe examina os NSGs (Grupos de Segurança de Rede) das VMs. Os NSGs são usados para garantir que somente o tráfego permitido para o aplicativo pode acessá-lo.
- A equipe também considera a possibilidade de proteger os dados no disco usando o Azure Disk Encryption e o KeyVault.

[Leia mais](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms) sobre as práticas recomendadas de segurança para máquinas virtuais.

## <a name="bcdr"></a>BCDR

Para a BCDR (continuidade dos negócios e recuperação de desastres), a Contoso executa as seguintes ações:

- Manter dados seguros: A Contoso faz backup dos dados nas VMs usando o serviço de Backup do Azure. [Saiba mais](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- Manter os aplicativos e executar: A Contoso replica as VMs do aplicativo no Azure para uma região secundária usando o Site Recovery. [Saiba mais](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart).



### <a name="licensing-and-cost-optimization"></a>Licenciamento e otimização de custo

1. A Contoso possui licenças existentes para suas VMs e aproveitará o Benefício Híbrido do Azure. A Contoso converterá as VMs do Azure existentes para aproveitar esses preços.
2. A Contoso habilitará o Gerenciamento de Custos do Azure licenciado pela Cloudyn, uma subsidiária da Microsoft. Essa é uma solução de gerenciamento de custos de várias nuvens que ajuda a usar e gerenciar o Azure e outros recursos de nuvem. [Saiba mais](https://docs.microsoft.com/azure/cost-management/overview) sobre o Gerenciamento de Custos do Azure.

## <a name="conclusion"></a>Conclusão

Neste artigo, a Contoso hospedou novamente o aplicativo SmartHotel360 no Azure, migrando as VMs do aplicativo para VMs do Azure usando o serviço de recuperação do site.


## <a name="next-steps"></a>Próximas etapas

No [próximo artigo](contoso-migration-rehost-vm-sql-ag.md) da série, mostraremos como a Contoso reencontra a VM frontend do aplicativo SmartHotel360 em uma VM do Azure e migra o banco de dados para um grupo de disponibilidade AlwaysOn do SQL Server no Azure.


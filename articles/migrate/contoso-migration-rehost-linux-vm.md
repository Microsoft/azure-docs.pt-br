---
title: Migrar o novo host e o novo host de um aplicativo local Linux VMs do Azure | Microsoft Docs
description: Saiba como Contoso novo host a um aplicativo do Linux local migrando para máquinas virtuais do Azure.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: raynew
ms.openlocfilehash: 200f9c5df0d4165341e38ca9d4dd85ad75c8403c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60675516"
---
# <a name="contoso-migration-rehost-an-on-premises-linux-app-to-azure-vms"></a>Migração para Contoso: Hospede novamente um aplicativo Linux local para VMs do Azure

Este artigo mostra como a Contoso é nova hospedagem um aplicativo de suporte técnico de serviço baseado em Linux local (**osTicket**), para VMs Azure IaaS.

Este documento é um em uma série de artigos que documentam como a empresa fictícia Contoso migra seus recursos locais para a nuvem do Microsoft Azure. A série inclui informações básicas e um conjunto de cenários que ilustra como configurar uma infraestrutura de migração e executar diferentes tipos de migrações. Cenários aumentam em complexidade. Adicionaremos mais artigos com o tempo.

**Artigo** | **Detalhes** | **Status**
--- | --- | ---
[Artigo 1: Visão geral](contoso-migration-overview.md) | Visão geral da série de artigos, da estratégia de migração da Contoso e dos aplicativos de exemplo usados na série. | Disponível
[Artigo 2: Implantar a infraestrutura do Azure](contoso-migration-infrastructure.md) | A Contoso prepara a infraestrutura local e a infraestrutura do Azure para migração. A mesma infraestrutura é usada para todos os artigos de migração da série. | Disponível
[Artigo 3: Avaliar recursos locais para migração para o Azure](contoso-migration-assessment.md)  | A Contoso executa uma avaliação do aplicativo SmartHotel360 local em execução no VMware. A Contoso avalia as VMs do aplicativo usando o serviço de Migrações para Azure e o banco de dados do SQL Server do aplicativo usando o Assistente de Migração de Dados. | Disponível
[Artigo 4: Hospedar novamente um aplicativo em uma VM do Azure e uma Instância Gerenciada do Banco de Dados SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | A Contoso executa uma migração lift-and-shift para o Azure no aplicativo SmartHotel360 local. A Contoso migra a VM front-end do aplicativo usando o [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). A Contoso migra o banco de dados do aplicativo para uma Instância Gerenciada do Banco de Dados SQL do Azure usando o [Serviço de Migração de Banco de Dados do Azure](https://docs.microsoft.com/azure/dms/dms-overview). | Disponível   
[Artigo 5: Hospedar novamente um aplicativo em VMs do Azure](contoso-migration-rehost-vm.md) | A Contoso migra as VMs de aplicativos SmartHotel360 para VMs do Azure usando o serviço do Site Recovery. | Disponível
[Artigo 6: Hospedar novamente um aplicativo em VMs do Azure e em um grupo de disponibilidade AlwaysOn do SQL Server](contoso-migration-rehost-vm-sql-ag.md) | A Contoso migra o aplicativo SmartHotel360. A Contoso usa o Site Recovery para migrar as VMs do aplicativo. Ele usa o Serviço de Migração de Banco de Dados para migrar o banco de dados do aplicativo para um cluster SQL Server protegido por um grupo de disponibilidade AlwaysOn. | Disponível 
Artigo 7: Hospedar novamente um aplicativo do Linux em VMs do Azure | A Contoso conclui uma migração lift-and-shift do aplicativo osTicket do Linux para as VMs do Azure, usando o Azure Site Recovery | Este artigo
[Artigo 8: Hospedar novamente um aplicativo Linux em VMs do Azure e MySQL do Azure](contoso-migration-rehost-linux-vm-mysql.md) | A Contoso migra o aplicativo osTicket do Linux para as VMs do Azure usando o Azure Site Recovery e migra o banco de dados do aplicativo para uma instância do MySQL Server no Azure usando o MySQL Workbench. | Disponível
[Artigo 9: Refatorar um aplicativo em aplicativos Web do Azure e no Banco de Dados SQL do Azure](contoso-migration-refactor-web-app-sql.md) | A Contoso migra o aplicativo SmartHotel360 para um aplicativo Web do Azure e migra o banco de dados de aplicativo para uma instância do Azure SQL Server com o Assistente de Migração de Dados | Disponível
[Artigo 10: Refatorar um aplicativo Linux em aplicativos Web do Azure e no MySQL do Azure](contoso-migration-refactor-linux-app-service-mysql.md) | A Contoso migra seu aplicativo osTicket do Linux para um aplicativo Web do Azure em várias regiões do Azure usando o Gerenciador de Tráfego do Azure, integrado ao GitHub para entrega contínua. A Contoso migra o banco de dados do aplicativo para uma instância do Banco de Dados do Azure para MySQL. | Disponível 
[Artigo 11: Refatorar o TFS no Azure DevOps Services](contoso-migration-tfs-vsts.md) | A Contoso migra a implantação do Team Foundation Server local para o Azure DevOps Services no Azure. | Disponível
[Artigo 12: Recriar um aplicativo nos contêineres do Azure e no Banco de Dados SQL do Azure](contoso-migration-rearchitect-container-sql.md) | A Contoso migra o aplicativo SmartHotel para o Azure. Em seguida, ela cria novamente a camada da Web do aplicativo como um contêiner do Windows em execução no Azure Service Fabric e o banco de dados com o Banco de Dados SQL do Azure. | Disponível
[Artigo 13: Recompilar um aplicativo no Azure](contoso-migration-rebuild.md) | A Contoso recompila o aplicativo SmartHotel usando diversas funcionalidades e serviços do Azure, incluindo o Serviço de Aplicativo do Azure, o AKS (Serviço de Kubernetes do Azure), o Azure Functions, os Serviços Cognitivos do Azure e o Azure Cosmos DB. | Disponível
[Artigo 14: Escalar uma migração para o Azure](contoso-migration-scale.md) | Depois de experimentar combinações de migração, a Contoso se prepara para dimensionar para uma migração completa para o Azure. | Disponível



Neste artigo, a Contoso migrará duas camadas **osTicket** aplicativo, em execução no Linux Apache MySQL PHP (LÂMPADA) para o Azure. O aplicativo VMs será migrado usando o serviço do Azure Site Recovery. Se você quiser usar este aplicativo de código-fonte aberto, você pode baixá-lo do [GitHub](https://github.com/osTicket/osTicket).

## <a name="business-drivers"></a>Geradores de negócios

A equipe de liderança de TI trabalhou em conjunto com parceiros comerciais para entender o que eles desejam obter com essa migração:

- **Lidando com o crescimento da empresa**: A Contoso está crescendo e, como resultado, há pressão sobre os sistemas e a infraestrutura locais.
- **Limitar riscos**: O aplicativo da Central de Serviços é essencial para o negócio da Contoso. A Contoso quer movê-lo para o Azure com zero risco.
- **Estender**:  Atualmente, a Contoso não quer alterar o aplicativo. Simplesmente deseja garantir que o aplicativo esteja estável.


## <a name="migration-goals"></a>Metas de migração

A equipe de nuvem Contoso foi fixado para baixo objetivos para a migração, para determinar o melhor método de migração:

- Após a migração, o aplicativo no Azure deve ter os mesmos recursos de desempenho de hoje em seu ambiente de VMWare no local.  O aplicativo permanecerá essencial tanto na nuvem quanto localmente. 
- A Contoso não quer investir nesse aplicativo.  Ele é importante para os negócios, mas, em sua forma atual, a Contoso apenas deseja passá-lo com segurança para a nuvem.
- A Contoso não quer alterar o modelo de operações para este aplicativo. Ela deseja interagir com o aplicativo na nuvem da mesma maneira que faz agora.
- A Contoso não quer alterar a funcionalidade do aplicativo. Somente o local do aplicativo será alterado.
- Depois de concluir algumas migrações de aplicativo do Windows, a Contoso deseja aprender a usar uma infraestrutura baseada em Linux no Azure.


## <a name="solution-design"></a>Design da solução

Depois de fixar metas e requisitos, a Contoso cria e examina uma solução de implantação e identifica o processo de migração, incluindo os serviços do Azure que ela usará para a migração.

### <a name="current-app"></a>Aplicativo atual

- O aplicativo OSTicket é em camadas em duas VMs (**OSTICKETWEB** e **OSTICKETMYSQL**).
- As VMs estão localizadas no host VMware ESXi **contosohost1.contoso.com** (versão 6.5).
- O ambiente VMware é gerenciado pelo vCenter Server 6.5 (**vcenter.contoso.com**) em execução em uma VM.
- A Contoso tem um datacenter local (**contoso-datacenter**), com um controlador de domínio local ( **contosodc1**)

### <a name="proposed-architecture"></a>Arquitetura proposta

- Como o aplicativo é uma carga de trabalho de produção, as VMs no Azure residirão no grupo de recursos de produção **ContosoRG**.
- As máquinas virtuais serão migradas para a região principal (Leste dos EUA 2) e colocadas na rede de produção (VNET-PROD-EUS2):
    - Web VM residem na sub-rede front-end (FE-PROD-EUS2).
    - O banco de dados de máquina virtual residem na sub-rede de banco de dados (PROD-DB-EUS2).
- As VMs locais no datacenter da Contoso serão descomissionadas após a migração.

![Arquitetura de cenário](./media/contoso-migration-rehost-linux-vm/architecture.png) 

### <a name="solution-review"></a>Análise de solução

A Contoso avalia o design proposto reunindo uma lista de prós e contras.

**Consideração** | **Detalhes**
--- | ---
**Prós** | As duas VMs do aplicativo serão movidas para o Azure sem alterações, simplificando a migração.<br/><br/> Como a Contoso está usando o lift-and-shift para as duas VMs do aplicativo, não é necessária nenhuma ferramenta de configuração ou de migração especial para o banco de dados do aplicativo.<br/><br/> A Contoso manterá o controle total sobre as VMs do aplicativo no Azure. </br>/ br > o aplicativo de VMs estão executando o Ubuntu 16.04-TLS, que é uma distribuição Linux endossada. [Saiba mais](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).
**Contras** | A camada da Web e de dados do aplicativo continuará sendo um ponto único de failover. <br/><br/> A Contoso precisará continuar dando suporte ao aplicativo como VMs do Azure, em vez de mover para um serviço gerenciado, como o Serviço de Aplicativo do Azure e o Banco de Dados do Azure para MySQL.<br/><br/> A Contoso está ciente de que, se mantiver as coisas simples com uma migração de VM lift-and-shift, não estará aproveitando por completo os recursos fornecidos pelo [Banco de Dados do Azure para MySQL](https://docs.microsoft.com/azure/mysql/overview) (alta disponibilidade interna, desempenho previsível, dimensionamento simples, backups automáticos e segurança interna).

### <a name="migration-process"></a>Processo de migração

A Contoso migrará da seguinte maneira:

1. Como uma primeira etapa, a Contoso configura o Azure e a infraestrutura local necessária para implantar o Site Recovery.
2. Depois de preparar componentes do Azure e locais, a Contoso configura e habilita a replicação para as VMs.
3. Depois que a replicação estiver funcionando, a Contoso migrará as VMs por failover para o Azure.

![Processo de migração](./media/contoso-migration-rehost-linux-vm/migration-process.png)

### <a name="azure-services"></a>Serviços do Azure

**Serviço** | **Descrição** | **Custo**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | O serviço coordena e gerencia a migração e a recuperação de desastre para VMs do Azure e servidores físicos e de VMs locais.  | Durante a replicação para o Azure, são gerados encargos do Armazenamento do Azure.  As VMs do Azure são criadas e incorrem em encargos quando ocorre failover. [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre encargos e preços.

 
## <a name="prerequisites"></a>Pré-requisitos

Veja o que a Contoso precisa para esse cenário.

**Requisitos** | **Detalhes**
--- | ---
**Assinatura do Azure** | A Contoso criou assinaturas em um artigo anterior nesta série. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Se você criar uma conta gratuita, será o administrador da assinatura e poderá executar todas as ações.<br/><br/> Se você usar uma assinatura existente e não for o administrador, será necessário trabalhar com o administrador para receber permissões de Proprietário ou de Colaborador.<br/><br/> Se forem necessárias permissões mais granulares, leia [este artigo](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Infraestrutura do Azure** | A Contoso configura a infraestrutura do Azure conforme descrito em [Infraestrutura do Azure para migração](contoso-migration-infrastructure.md).<br/><br/> Saiba mais sobre os requisitos de [rede](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) e [armazenamento](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) específicos para o Site Recovery.
**Servidores locais** | O servidor vCenter local deve estar executando a versão 5.5, 6.0 ou 6.5<br/><br/> Um host ESXi que executa a versão 5.5, 6.0 ou 6.5<br/><br/> Uma ou mais VMs do VMware em execução no host ESXi.
**VMs locais** | [Examine máquinas Linux](https://docs.microsoft.com//azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines) que têm suporte para migração com a recuperação de Site.<br/><br/> Verifique os [sistemas de arquivo e armazenamento Linux](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#linux-file-systemsguest-storage) que têm suporte.<br/><br/> As VMs devem atender aos [requisitos do Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>Etapas do cenário

É assim que a Contoso concluirá a migração:

> [!div class="checklist"]
> * **Etapa 1: Preparar o Azure para Site Recovery**: A Contoso cria uma conta de armazenamento do Azure para armazenar dados replicados e cria uma área segura do Recovery Services.
> * **Etapa 2: Preparar VMware local para Site Recovery**: A Contoso prepara as contas a serem usadas para a descoberta de VMs e a instalação do agente e se preparar para conectar VMs do Azure após o failover.
> * **Etapa 3: Replicar VMs**: A Contoso configura o ambiente de migração de origem e de destino, cria uma política de replicação e inicia a replicação de VMs no armazenamento do Azure.
> * **Etapa 4: Migrar VMs com o Site Recovery**: A Contoso executa um failover de teste para certificar-se de que tudo está funcionando e, em seguida, executa um failover completo para migrar as VMs para o Azure.


## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>Etapa 1: Preparar o Azure para o serviço do Site Recovery

A Contoso precisa de alguns componentes do Azure para o Site Recovery:

- Uma nova conta de armazenamento do Azure para armazenar dados replicados. 
- Um cofre dos Serviços de Recuperação no Azure.
- Uma VNet na qual os recursos com failover estão localizados. A Contoso já criou a VNet durante a [implantação da infraestrutura do Azure](contoso-migration-infrastructure.md) e, portanto, precisam apenas criar uma conta de armazenamento e um cofre.

1. Os administradores da Contoso criam uma conta de armazenamento do Azure (contosovmsacc20180528) na região Leste dos EUA 2.

    - A conta de armazenamento deve estar na mesma região do cofre de Serviços de Recuperação.
    - Trata-se de uma conta de uso geral, com armazenamento padrão e replicação de LRS.

      ![Armazenamento do Site Recovery](./media/contoso-migration-rehost-linux-vm/asr-storage.png)

2. Com a rede e a conta de armazenamento prontas, eles criam um cofre (ContosoMigrationVault) e colocam-no grupo de recursos **ContosoFailoverRG**, na região Leste dos EUA 2 primária.

    ![Cofre dos Serviços de Recuperação](./media/contoso-migration-rehost-linux-vm/asr-vault.png)


**Precisa de mais ajuda?**

[Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) como configurar o Azure para o Site Recovery.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>Etapa 2: Preparar VMware local para Site Recovery

A Contoso administra a infraestrutura de VMware local da seguinte maneira:

- Ela cria uma conta no vCenter server ou vSphere ESXi host para automatizar a descoberta da VM.
- Cria uma conta que permite a instalação automática do serviço de mobilidade de VMs do VMware que você deseja replicar.
- Prepara VMs locais para que possam se conectar a VMs do Azure quando forem criadas após a migração.


### <a name="prepare-an-account-for-automatic-discovery"></a>Preparar uma conta para a descoberta automática

O Site Recovery precisa de acesso aos servidores VMware para:

- Descobrir VMs automaticamente. Pelo menos uma conta somente leitura é necessária.
- Orquestrar a replicação, o failover e o failback. É necessária uma conta que possa executar operações como criação e remoção de discos e ativação de VMs.

Os administradores da Contoso configuram a conta da seguinte maneira:

1. Eles criam uma função no nível do vCenter.
2. Eles atribuir as permissões necessárias a essa função.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparar uma conta para a instalação do serviço de Mobilidade

O serviço de mobilidade deve ser instalado nas VMs do Linux que serão migradas.

- O Site Recovery pode fazer uma instalação automática por push desse componente quando a replicação está habilitada para VMs.
- Para instalação automática por push, é necessário preparar uma conta que o Site Recovery usará para acessar as VMs.
- Detalhes de contas são inseridos durante a instalação de replicação. 
- A conta pode ser o domínio ou conta local, com permissões para instalar nas máquinas virtuais.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar para conectar VMs do Azure após o failover

Após o failover no Azure, a Contoso deseja ser capaz de se conectar às VMs replicadas no Azure. Para fazer isso, os administradores da Contoso precisam executar algumas ações:

- Para acessar as VMs do Azure pela Internet, eles habilitam SSH na VM Linux local antes da migração.  Para Ubuntu, essa etapa pode ser concluída usando o comando a seguir: **Sudo apt-get ssh install -y**.
- Depois de executar a migração (failover), eles devem verificar os **Diagnósticos de inicialização** para exibir uma captura de tela da VM.
- Se isso não funcionar, será necessário verificar se a VM está em execução e revisar estas [dicas de solução de problemas](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


**Precisa de mais ajuda?**

- [Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) como criar e atribuir uma função para a descoberta automática.
- [Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) como criar uma conta para a instalação do serviço de mobilidade por push.


## <a name="step-3-replicate-the-on-premises-vms"></a>Etapa 3: Replicar as VMs locais

Antes de poder migrar a VM Web para o Azure, os administradores da Contoso configuram e habilitam a replicação.

### <a name="set-a-protection-goal"></a>Definir uma meta de proteção

1. No cofre, sob o nome do cofre (ContosoVMVault), ela define uma meta de replicação (**Introdução** > **Site Recovery** > **Preparar infraestrutura**.
2. Eles especificam que suas máquinas estão localizados no local, eles são VMs VMware e que deseja replicar no Azure.
    ![Meta de replicação](./media/contoso-migration-rehost-linux-vm/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Confirmar planejamento de implantação

Para continuar, ela confirma que concluiu o planejamento da implantação selecionando **Sim, fiz isso**. A Contoso só está migrando uma VM neste cenário e não precisa de planejamento de implantação.

### <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Agora, os administradores da Contoso precisam configurar o ambiente de origem. Para fazer isso, eles baixam um modelo OVF e o utilizam para implantar o servidor de configuração do Site Recovery como uma VM VMware local, altamente disponível. Depois que a configuração do servidor tiver sido finalizada, registre-o no cofre.

O servidor de configuração executa vários componentes:

- O componente de servidor de configuração que coordena a comunicação entre o ambiente local e o Azure e gerencia a replicação de dados.
- Servidor em processo que age como um gateway de replicação. Ele recebe dados de replicação, otimiza-os com caching, compactação e criptografia e os envia para o Armazenamento do Azure.
- O servidor de processo também instala o Serviço de Mobilidade nas VMs que você deseja replicar e executa a descoberta automática de VMs VMware locais.

Os administradores da Contoso executam as seguintes etapas:

1. Ela baixam o modelo OVF de **Preparar infraestrutura** > **Fonte** > **Servidor de Configuração**.
    
    ![Baixe o OVF](./media/contoso-migration-rehost-linux-vm/add-cs.png)

2. Ela importa o modelo para o VMware a fim de criar e implantar a VM.

    ![Modelo de OVF](./media/contoso-migration-rehost-linux-vm/vcenter-wizard.png)

3. Ao ligar a máquina virtual pela primeira vez, ela é inicializada como uma experiência de instalação do Windows Server 2016. Eles aceitam o contrato de licença e inserir uma senha de administrador.
4. Depois que a instalação for concluída, entrarem na VM como um administrador. Na primeira entrada, a ferramenta de configuração de recuperação de Site do Azure é executado por padrão.
5. Na ferramenta, ela especifica um nome a ser usado para registrar o servidor de configuração no cofre.
6. A ferramenta verifica se a VM pode se conectar ao Azure. Depois que a conexão é estabelecida, entram na assinatura do Azure. As credenciais devem ter acesso ao cofre no qual você deseja registrar o servidor de configuração.

    ![Registrar servidor de configuração](./media/contoso-migration-rehost-linux-vm/config-server-register2.png)

7. A ferramenta executa algumas tarefas de configuração e, em seguida, é reinicializada.
8. Entram na máquina novamente e o Assistente de Gerenciamento do Servidor de Configuração é iniciado automaticamente.
9. No assistente, selecionam o NIC que vai receber o tráfego de replicação. Essa configuração não pode ser alterada depois de definida.
10. Ela seleciona a assinatura, o grupo de recursos e o cofre no qual registrar o servidor de configuração.

    ![cofre](./media/contoso-migration-rehost-linux-vm/cswiz1.png) 

11. Eles Baixe e instale o MySQL Server e VMWare PowerCLI. 
12. Após a validação, eles especificar o FQDN ou endereço IP do host de servidor ou vSphere do vCenter. Eles deixam a porta padrão e especifique um nome amigável para o servidor do vCenter.
13. Eles especificam a conta que criaram para descoberta automática e as credenciais que devem ser usadas para instalar automaticamente o serviço de mobilidade.

    ![vCenter](./media/contoso-migration-rehost-linux-vm/cswiz2.png)

14. Após a conclusão do registro, no portal do Azure, eles verificam se o servidor de configuração e o VMware Server estão listados na página **Origem** no cofre. A descoberta pode levar 15 minutos ou mais. 
15. Recuperação de site conecta-se a servidores VMware e descobre máquinas virtuais.

### <a name="set-up-the-target"></a>Configurar o destino

Agora, os administradores da Contoso definem as configurações de replicação de destino.

1. Em **Preparar infraestrutura** > **Destino**, selecionam as configurações de destino.
2. O Site Recovery verifica se há uma rede e uma conta de armazenamento do Azure no destino especificado.

### <a name="create-a-replication-policy"></a>Criar uma política de replicação

Após a configuração da origem e do destino, a Contoso está pronta para criar uma política de replicação.

1. Em **Preparar infraestrutura** > **Configurações de Replicação** > **Política de Replicação** >  **Criar e Associar**, criam uma política **ContosoMigrationPolicy**.
2. Usam as configurações padrão:
    - **Limite de RPO**: Padrão de 60 minutos. Esse valor define a frequência em que são criados os pontos de recuperação. Um alerta será gerado se a replicação contínua exceder esse limite.
    - **Retenção do ponto de recuperação**. Padrão de 24 horas. Esse valor especifica qual é o tempo da janela de retenção para cada ponto de recuperação. VMs replicadas podem ser recuperadas para qualquer ponto em uma janela.
    - **Frequência de instantâneos consistentes com o aplicativo**. O padrão é de uma hora. Esse valor especifica a frequência com que são criados instantâneos consistentes com o aplicativo.
 
        ![Criar política de replicação](./media/contoso-migration-rehost-linux-vm/replication-policy.png)

5. A política é associada automaticamente ao servidor de configuração. 

    ![Associar política de replicação](./media/contoso-migration-rehost-linux-vm/replication-policy2.png)

**Precisa de mais ajuda?**

- Você pode ler uma explicação completa de todas essas etapas em [Configurar a recuperação de desastres para máquinas virtuais do VMware locais](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- As instruções detalhadas estão disponíveis para ajudá-lo a [configurar o ambiente da fonte](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [implantar o servidor de configuração](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), e [definir configurações de replicação](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- [Saiba mais](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) sobre o agente convidado do Azure para Linux.

**Precisa de mais ajuda?**

- Você pode ler uma explicação completa de todas essas etapas em [Configurar a recuperação de desastres para máquinas virtuais do VMware locais](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- As instruções detalhadas estão disponíveis para ajudá-lo a [configurar o ambiente da fonte](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [implantar o servidor de configuração](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), e [definir configurações de replicação](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- [Saiba mais](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) sobre o agente convidado do Azure para Linux.



### <a name="enable-replication-for-osticketweb"></a>Ativar replicação para o OSTICKETWEB

Agora os administradores da Contoso podem iniciar a replicação da VM **OSTICKETWEB**.

1. Em **Replicar aplicativo** > **Fonte** > **+Replicar**, ela seleciona as configurações de fonte.
2. Eles selecionam que deseja habilitar as máquinas virtuais, selecione as configurações de fonte, incluindo o servidor do vCenter e o servidor de configuração.

    ![Habilitar a replicação](./media/contoso-migration-rehost-linux-vm/enable-replication-source.png)

3. Eles especificam as configurações de destino, incluindo o grupo de recursos e redes em que a VM do Azure será localizada após o failover e a conta de armazenamento no qual serão armazenados os dados replicados.

     ![Habilitar a replicação](./media/contoso-migration-rehost-linux-vm/enable-replication2.png)

3. Eles selecionam a VM **OSTICKETWEB** para replicação. 

   - Neste estágio, selecionam somente **OSTICKETWEB** porque a rede virtual e a sub-rede devem ser selecionados e as VMs não estão na mesma sub-rede.
   - O Site Recovery instala automaticamente o serviço Mobility quando a replicação está habilitada para a VM.

     ![Habilitar a replicação](./media/contoso-migration-rehost-linux-vm/enable-replication3.png)

4. Nas propriedades da VM, selecionam a conta que é usada pelo servidor de processo para instalar automaticamente o serviço de mobilidade no computador.

     ![Serviço de mobilidade](./media/contoso-migration-rehost-linux-vm/linux-mobility.png)

5. Em **Configurações de Replicação** > **Definir Configurações de Replicação**, ela verifica se a política de replicação correta está aplicada e seleciona **Habilitar Replicação**.
6. Eles rastreiam o progresso da replicação em **Trabalhos**. Após o trabalho de **Finalizar Proteção** ser executado, o computador estará pronto para failover.



### <a name="enable-replication-for-osticketmysql"></a>Habilitar a replicação para OSTICKETMYSQL

Agora, os administradores da Contoso podem iniciar a replicação de **OSTICKETMYSQL**.

1. Em **replicar aplicativo** > **fonte** > **+ replicar** eles selecionar as configurações de origem e destino.
2. Eles selecionam a VM **OSTICKETMYSQL** para replicação e selecionam a conta a ser usada para a instalação do serviço de mobilidade.

    ![Habilitar a replicação](./media/contoso-migration-rehost-linux-vm/mysql-enable.png)

3. Aplicam a mesma política de replicação que foi usada para OSTICKETWEB e habilitam a replicação.  

**Precisa de mais ajuda?**

Você pode ler um passo a passo de todas essas etapas em [Habilitar replicação](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-4-migrate-the-vms"></a>Etapa 4: Migrar as VMs 

Os administradores da Contoso executam um failover de teste rápido e, em seguida, migram as VMs.

### <a name="run-a-test-failover"></a>Execute um teste de failover

Executar um failover de teste ajuda a garantir que tudo está funcionando conforme o esperado antes da migração. 

1. Eles executam um failover de teste, até o último momento disponível (**Último processamento**).
2. Selecionam **Desligar o computador antes do início do failover**, para que o Site Recovery tente desligar a VM da fonte antes de disparar o failover. O failover continuará mesmo o desligamento falhar. 
3. O failover de teste é executado: 
    - Uma verificação de pré-requisitos será executada para conferir se todas as condições exigidas para a migração foram cumpridas.
    - O failover processa os dados para que uma VM do Azure possa ser criada. Se o ponto de recuperação mais recente for selecionado, um ponto de recuperação é criado dos dados.
    - Uma VM do Azure é criada usando os dados processados na etapa anterior.
3. Após o failover ser concluído, a réplica da VM do Azure aparecerá no portal do Azure. Eles Verifique se a VM é o tamanho apropriado, o que ele está conectado à rede certa, e se ele está em execução. 
4. Depois de verificar, eles limpar o failover e registram e salvar todas as observações.

### <a name="create-and-customize-a-recovery-plan"></a>Criar e personalizar um plano de recuperação

 Depois de verificar se o failover de teste funciona como esperado, os administradores da Contoso criam um plano de recuperação para a migração. 

- Um plano de recuperação Especifica a ordem na qual o failover ocorre, como VMs do Azure serão ativadas no Azure.
- Desde que desejam migrar um aplicativo de duas camadas, eles personalizará o plano de recuperação para que os dados de VM (SQLVM) é iniciado antes do front-end (WEBVM).


1. Em **planos de recuperação (recuperação de Site)** > **+ plano de recuperação**, criam um plano e adicione as máquinas virtuais a ele.

    ![Plano de recuperação](./media/contoso-migration-rehost-linux-vm/recovery-plan.png)

2. Depois de criar o plano, eles selecionarão-lo para personalização (**planos de recuperação** > **OsTicketMigrationPlan** > **personalizar**.
3.  Eles removem **OSTICKETWEB** do **Grupo 1: Iniciar**.  Isso garante que a primeira ação de início afeta **OSTICKETMYSQL** somente.

    ![Grupo de recuperação](./media/contoso-migration-rehost-linux-vm/recovery-group1.png)

4.  Em **+Grupo** > **Adicionar itens protegidos**, eles adicionam **OSTICKETWEB** ao **Grupo 2: Iniciar**.  Precisam deles em dois grupos diferentes.

    ![Grupo de recuperação](./media/contoso-migration-rehost-linux-vm/recovery-group2.png)


### <a name="migrate-the-vms"></a>Migrar as VMs


Os administradores da Contoso já estão prontos para executar um failover no plano de recuperação a fim de migrar as máquinas virtuais.

1. Eles selecionam o plano > **Failover**.
2.  Eles selecionam o failover para o último ponto de recuperação e especificar que a recuperação de Site deve tentar desligar a VM do local antes de acionar o failover. Podem acompanhar o progresso do failover na página **Trabalhos**.

    ![Failover](./media/contoso-migration-rehost-vm/failover1.png)

3. Durante o failover, o vCenter Server emite comandos para parar as duas VMs em execução no host ESXi.

    ![Failover](./media/contoso-migration-rehost-linux-vm/vcenter-failover.png)

3. Após o failover, eles verificam se a VM do Azure aparece conforme o esperado no portal do Azure.

    ![Failover](./media/contoso-migration-rehost-linux-vm/failover2.png)  

3. Depois de verificar se a VM no Azure, eles concluírem a migração para concluir o processo de migração para cada VM. Isso interrompe a replicação para a VM e interrompe a cobrança do Site Recovery para a VM.

    ![Failover](./media/contoso-migration-rehost-linux-vm/failover3.png)


### <a name="connect-the-vm-to-the-database"></a>Conectar a VM ao banco de dados

Como a etapa final no processo de migração, os administradores da Contoso atualizar a cadeia de caracteres de conexão do aplicativo para apontar para o banco de dados do aplicativo em execução no **OSTICKETMYSQL** VM. 

1. Eles fazer uma conexão SSH para a **OSTICKETWEB** VM usando Putty ou outro cliente SSH. A VM é particular para que se conectar usando o endereço IP privado.

    ![Conectar ao banco de dados](./media/contoso-migration-rehost-linux-vm/db-connect.png)  

    ![Conectar ao banco de dados](./media/contoso-migration-rehost-linux-vm/db-connect2.png)  

2. Eles precisam para se certificar de que o **OSTICKETWEB** VM pode se comunicar com o **OSTICKETMYSQL** VM. Atualmente, a configuração é codificado com o endereço IP local 172.16.0.43.

    **Antes da atualização**
    
    ![Atualizar IP](./media/contoso-migration-rehost-linux-vm/update-ip1.png)  

    **Após a atualização**
    
    ![Atualizar IP](./media/contoso-migration-rehost-linux-vm/update-ip2.png) 
    
3. Ela reinicia o serviço com **systemctl restart apache2**.

    ![Reiniciar](./media/contoso-migration-rehost-linux-vm/restart.png) 

4. Por fim, eles atualizar os registros DNS para **OSTICKETWEB** e **OSTICKETMYSQL**, em um dos controladores de domínio Contoso.

    ![Atualizar o DNS](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 

    ![Atualizar o DNS](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 

**Precisa de mais ajuda?**

- [Saiba mais](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) sobre como executar failovers de teste. 
- [Saiba](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) como criar um plano de recuperação.
- [Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) como fazer failover para o Azure.

## <a name="clean-up-after-migration"></a>Limpeza após a migração

Migração concluída, as camadas de aplicativo osTicket agora estão em execução em VMs do Azure.

Agora, a Contoso precisa fazer uma limpeza, da seguinte maneira: 

- Remova as VMs locais do inventário do vCenter.
- Remover as VMs locais de trabalhos de backup locais.
- Atualizar sua documentação interna para mostrar o novo local e endereços IP para OSTICKETWEB e OSTICKETMYSQL.
- Examinar todos os recursos que interagem com as máquinas virtuais e atualizar configurações relevantes ou documentação para refletir a nova configuração.
- Contoso usado o serviço de migração do Azure com o mapeamento de dependência para avaliar as máquinas virtuais para migração. Os administradores devem remover o Microsoft Monitoring Agent, e o Dependency Agent que foi instalado para essa finalidade, da VM.

## <a name="review-the-deployment"></a>Revisar a implantação

Com o aplicativo em execução, a Contoso precisa totalmente colocar e proteger sua infraestrutura de novo.

### <a name="security"></a>Segurança

A equipe de segurança da Contoso examine o OSTICKETWEB e OSTICKETMYSQLVMs para determinar os problemas de segurança.

- A equipe revisa os NSGs (Grupos de Segurança de Rede) para que as VMs controlem o acesso. Os NSGs são usados para garantir que somente o tráfego permitido para o aplicativo pode passar.
- A equipe também planeja proteger os dados nos discos da VM usando a criptografia de disco e o Azure KeyVault.

[Leia mais](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms) sobre as práticas recomendadas de segurança para máquinas virtuais.

### <a name="bcdr"></a>BCDR

Para continuidade dos negócios e recuperação de desastres, a Contoso executa as seguintes ações:

- **Manter dados seguros**: A Contoso faz backup dos dados nas VMs usando o serviço de Backup do Azure. [Saiba mais](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- **Manter os aplicativos em execução**: A Contoso replica as VMs do aplicativo no Azure para uma região secundária usando o Site Recovery. [Saiba mais](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart).

### <a name="licensing-and-cost-optimization"></a>Licenciamento e otimização de custo

- Após a implantação de recursos, a Contoso atribui marcas do Azure conforme definido durante a [implantação de infraestrutura do Azure](contoso-migration-infrastructure.md#set-up-tagging).
- A Contoso não tem nenhum problema de licenciamento com os servidores do Ubuntu.
- A Contoso habilitará o Gerenciamento de Custos do Azure licenciado pela Cloudyn, uma subsidiária da Microsoft. É uma solução de gerenciamento de custo de multi-nuvem que ajuda você a usar e gerenciar o Azure e outros recursos de nuvem.  [Saiba mais](https://docs.microsoft.com/azure/cost-management/overview) sobre o Gerenciamento de Custos do Azure. 


## <a name="next-steps"></a>Próximas etapas

Neste artigo, mostramos como Contoso migrados um local de suporte técnico do serviço de aplicativo em camadas em duas VMs do Linux para VMs de IaaS do Azure, usando o Azure Site Recovery.

No próximo artigo na série, mostraremos como a Contoso migra o mesmo serviço de aplicativo de suporte técnico para o Azure. Neste momento, a Contoso usa o Site Recovery para migrar a VM front-end para o aplicativo e migra o banco de dados do aplicativo usando backup e restauração para o Banco de Dados do Azure para MySQL, com a ferramenta MySQL Workbench. [Introdução](contoso-migration-rehost-linux-vm-mysql.md).

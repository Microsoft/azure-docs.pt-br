---
title: Gerenciar servidores VMware vCenter no Azure Site Recovery
description: Este artigo descreve como adicionar e gerenciar o VMware vCenter para recuperação de desastre de VMs VMware no Azure com o Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/24/2019
ms.author: ramamill
ms.openlocfilehash: 01aef3aca4f6967b1681bff9598c7dd7a24739cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84692512"
---
# <a name="manage-vmware-vcenter-server"></a>Gerenciar VMware vCenter Server

Este artigo resume as ações de gerenciamento em um VMware vCenter Server no [Azure site Recovery](site-recovery-overview.md).

## <a name="verify-prerequisites-for-vcenter-server"></a>Verificar pré-requisitos para vCenter Server

Os pré-requisitos para servidores vCenter e VMs durante a recuperação de desastre de VMs VMware para o Azure são listados na [matriz de suporte](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="set-up-an-account-for-automatic-discovery"></a>Configurar uma conta para a descoberta automática

Quando você configura a recuperação de desastre para VMs VMware locais, Site Recovery precisa acessar o host vCenter Server/vSphere. O Site Recovery servidor de processo pode, então, descobrir automaticamente as VMs e fazer o failover delas conforme necessário. Por padrão, o servidor de processo é executado no servidor de configuração Site Recovery. Adicione uma conta para o servidor de configuração para se conectar ao host vCenter Server/vSphere da seguinte maneira:

1. Entre no servidor de configuração.
1. Abra a ferramenta servidor de configuração (_cspsconfigtool.exe_) usando o atalho da área de trabalho.
1. Na guia **gerenciar conta** , clique em **adicionar conta**.

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)

1. Forneça os detalhes da conta e clique em **OK** para adicioná-la. A conta deve ter os privilégios resumidos na tabela de permissões de conta.

   > [!NOTE]
   > É necessário cerca de 15 minutos para sincronizar as informações da conta com Site Recovery.

### <a name="account-permissions"></a>Permissões de conta

|**Tarefa** | **Conta** | **Permissões** | **Detalhes**|
|--- | --- | --- | ---|
|**Descoberta/migração de VM (sem failback)** | Pelo menos uma conta de usuário somente leitura. | Objeto de data center –> Propagar para o objeto filho, função = somente leitura | Usuário atribuído no nível de datacenter e tem acesso a todos os objetos no datacenter.<br/><br/> Se desejar restringir o acesso, atribua a função **Nenhum acesso** com o objeto **Propagar para objeto filho** aos objetos filho (hosts vSphere, armazenamentos de dados, máquinas virtuais e redes).|
|**Replicação/failover** | Pelo menos uma conta de usuário somente leitura. | Objeto de data center –> Propagar para o objeto filho, função = somente leitura | Usuário atribuído no nível de datacenter e tem acesso a todos os objetos no datacenter.<br/><br/> Para restringir o acesso, atribua a função **sem acesso** com o objeto **propagar para filho** aos objetos filho (hosts vSphere, repositórios de armazenamento, máquinas virtuais e redes).<br/><br/> É útil para fins de migração, mas não para replicação completa, failover ou failback.|
|**Replicação/failover/failback** | Sugerimos que você crie uma função (AzureSiteRecoveryRole) com as permissões necessárias e, em seguida, atribua a função a um usuário ou grupo do VMware. | Objeto de Data Center –> Propagar para o Objeto Filho, role=AzureSiteRecoveryRole<br/><br/> Armazenamento de dados -> alocar espaço, procurar armazenamento de dados, operações de arquivo de baixo nível, remover arquivo, atualizar arquivos de máquina virtual<br/><br/> Rede -> Atribuição de rede<br/><br/> Recurso -> Atribuir VM ao pool de recursos, migrar VM desligada, migrar VM ligada<br/><br/> Tarefas -> Criar tarefa, atualizar tarefa<br/><br/> Máquina virtual -> Configuração<br/><br/> Máquina virtual -> Interagir -> responder à pergunta, conexão de dispositivo, configurar mídia de CD, configurar mídia de disquete, desligar, ligar, instalação de ferramentas VMware<br/><br/> Máquina virtual -> Inventário -> Criar, registrar, cancelar registro<br/><br/> Máquina virtual -> Provisionamento -> Permitir download de máquina virtual, permitir upload de arquivos de máquina virtual<br/><br/> Máquina virtual -> Instantâneos -> Remover instantâneos | Usuário atribuído no nível de datacenter e tem acesso a todos os objetos no datacenter.<br/><br/> Se desejar restringir o acesso, atribua a função **Nenhum acesso** com o objeto **Propagar para objeto filho** aos objetos filho (hosts vSphere, armazenamentos de dados, máquinas virtuais e redes).|

## <a name="add-vmware-server-to-the-vault"></a>Adicionar um servidor VMware ao cofre

Ao configurar a recuperação de desastre para VMs VMware locais, você adiciona o host vCenter Server/vSphere no qual está descobrindo VMs para o cofre de Site Recovery, da seguinte maneira:

1. No cofre > **site Recovery**  >  **servidores de configuração**de infraestrutura, abra o servidor de configuração.
1. Na página de **detalhes** , clique em **vCenter**.
1. Em **Adicionar vCenter**, especifique um nome amigável para o host vSphere ou servidor vCenter.
1. Especifique o endereço IP ou o FQDN do servidor.
1. Deixe a porta como 443, a menos que os servidores do VMware estejam configurados para escutar solicitações em uma porta diferente.
1. Selecione a conta usada para se conectar ao servidor VMware vCenter ou vSphere ESXi. Em seguida, clique em **OK**.

## <a name="modify-credentials"></a>Modificar as credenciais

Se necessário, você pode modificar as credenciais usadas para se conectar ao host vCenter Server/vSphere da seguinte maneira:

1. Entre no servidor de configuração.
1. Abra a ferramenta servidor de configuração (_cspsconfigtool.exe_) usando o atalho da área de trabalho.
1. Na guia **Gerenciar Conta**, clique em **Adicionar Conta**.

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)

1. Forneça os detalhes da nova conta e clique em **OK**. A conta precisa das permissões listadas na tabela de [permissões de conta](#account-permissions) .
1. No cofre > **site Recovery**  >  **servidores de configuração**de infraestrutura, abra o servidor de configuração.
1. Em **detalhes**, clique em **Atualizar servidor**.
1. Após a conclusão do trabalho de atualização do servidor, selecione o vCenter Server.
1. Em **Resumo**, selecione a conta recém-adicionada no **vCenter Server/conta de host vSphere**e clique em **salvar**.

   ![modify-account](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>Excluir um vCenter Server

1. No cofre > **site Recovery**  >  **servidores de configuração**de infraestrutura, abra o servidor de configuração.
1. Na página **Detalhes**, selecione o servidor vCenter.
1. Clique no botão **excluir** .

   ![delete-account](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-ip-address-and-port"></a>Modificar o endereço IP e a porta

Você pode modificar o endereço IP do vCenter Server ou as portas usadas para comunicação entre o servidor e o Site Recovery. Por padrão, o Site Recovery acessa informações de host vCenter Server/vSphere por meio da porta 443.

1. No cofre > **site Recovery**  >  **servidores de configuração**de infraestrutura, clique no servidor de configurações ao qual o vCenter Server é adicionado.
1. Em **servidores vCenter**, clique no vCenter Server que você deseja modificar.
1. Em **Resumo**, atualize o endereço IP e a porta e salve as alterações.

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

1. Para que as alterações se tornem efetivas, aguarde 15 minutos ou [atualize o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="migrate-all-vms-to-a-new-server"></a>Migrar todas as VMs para um novo servidor

Se você quiser migrar todas as VMs para usar uma nova vCenter Server, basta atualizar o endereço IP atribuído à vCenter Server. Não adicione outra conta do VMware, pois isso pode levar a entradas duplicadas. Atualize o endereço da seguinte maneira:

1. No cofre > **site Recovery**  >  **servidores de configuração**de infraestrutura, clique no servidor de configurações ao qual o vCenter Server é adicionado.
1. Na seção **servidores vCenter** , clique no vCenter Server do qual você deseja migrar.
1. Em **Resumo**, atualize o endereço IP para o novo vCenter Server e salve as alterações.
1. Assim que o endereço IP for atualizado, Site Recovery começará a receber informações de descoberta de VM do novo vCenter Server. Isso não afeta as atividades de replicação contínuas.

## <a name="migrate-a-few-vms-to-a-new-server"></a>Migrar algumas VMs para um novo servidor

Se você quiser migrar apenas algumas de suas VMs de replicação para um novo vCenter Server, faça o seguinte:

1. [Adicione](#add-vmware-server-to-the-vault) o novo vCenter Server ao servidor de configuração.
1. [Desabilite a replicação](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) para VMs que serão movidas para o novo servidor.
1. No VMware, migre as VMs para o novo vCenter Server.
1. [Habilite a replicação](vmware-azure-tutorial.md#enable-replication) para as VMs migradas novamente, selecionando a nova vCenter Server.

## <a name="migrate-most-vms-to-a-new-server"></a>Migrar a maioria das VMs para um novo servidor

Se o número de VMs que você deseja migrar para um novo vCenter Server for maior do que o número de VMs que permanecerão no vCenter Server original, faça o seguinte:

1. [Atualize o endereço IP](#modify-the-ip-address-and-port) atribuído ao vCenter Server nas configurações do servidor de configuração para o endereço do novo vCenter Server.
1. [Desabilite a replicação](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) para as poucas VMs que permanecem no servidor antigo.
1. [Adicione o vCenter Server antigo](#add-vmware-server-to-the-vault) e seu endereço IP ao servidor de configuração.
1. [Habilite novamente a replicação](vmware-azure-tutorial.md#enable-replication) para as VMs que permanecem no servidor antigo.

## <a name="next-steps"></a>Próximas etapas

Se você tiver problemas, consulte [solucionar problemas de falhas de descoberta vCenter Server](vmware-azure-troubleshoot-vcenter-discovery-failures.md).

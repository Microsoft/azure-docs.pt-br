---
title: " Gerenciar um servidor VMware vCenter no Azure Site Recovery | Microsoft Docs"
description: Este artigo descreve como adicionar e gerenciar o VMware vCenter no Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 06/29/2017
ms.author: anoopkv
ms.openlocfilehash: 091f0884417535427c52beee7bcdc5ed1dd83315
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-vmware-vcenter-server-in-azure-site-recovery"></a>Gerenciar um VMware vCenter Server no Azure Site Recovery
Este artigo discute as várias operações do Site Recovery que podem ser executadas em um VMware vCenter.

## <a name="prerequisites"></a>Pré-requisitos

**Suporte a VMware vCenter e o Host ESX do VMware vSphere** | **Detalhes** |
|--- | --- |
|**Servidores VMware locais** | Um ou mais servidores VMware vSphere, executando 6.0, 5.5, 5.1 com as últimas atualizações. Os servidores devem estar localizados na mesma rede que o servidor de configuração (ou servidor de processo separado).<br/><br/> Recomendamos um servidor vCenter para gerenciar hosts, executando 6.0 ou 5.5 com as atualizações mais recentes. Somente recursos que estão disponíveis no 5.5 têm suporte quando você implanta a versão 6.0.|

## <a name="prepare-an-account-for-automatic-discovery"></a>Preparar uma conta para a descoberta automática
A Site Recovery precisa de acesso ao VMware para que o servidor de processo descubra automaticamente as máquinas virtuais e para failover e failback de máquinas virtuais.

* **Migrar**: se você quiser migrar máquinas virtuais do VMware para o Azure, sem nunca realizar failback novamente, poderá usar uma conta do VMware com uma função somente leitura. Essa função pode executar o failover, mas não pode desligar máquinas de origem protegidas. Isso não é necessário para a migração.
* **Replicação/recuperação**: se você desejar implantar a replicação completa (replicar, failover e failback) a conta deverá ser capaz de executar operações como criar e remover discos, ativar máquinas virtuais, etc.
* **Descoberta automática**: é necessário ter pelo menos uma conta somente leitura.


|**Tarefas** | **Conta/função necessária** | **Permissões** | **Detalhes**|
|--- | --- | --- | ---|
|**Servidor de processo descobre automaticamente as máquinas virtuais do VMware** | Você precisa de pelo menos um usuário somente leitura | Objeto de data center –> Propagar para o objeto filho, função = somente leitura | Usuário atribuído ao nível de datacenter e tem acesso a todos os objetos no datacenter.<br/><br/> Se desejar restringir o acesso, atribua a função **Nenhum acesso** com o objeto **Propagar para objeto filho** aos objetos filho (hosts vSphere, armazenamentos de dados, máquinas virtuais e redes).|
|**Failover** | Você precisa de pelo menos um usuário somente leitura | Objeto de data center –> Propagar para o objeto filho, função = somente leitura | Usuário atribuído ao nível de datacenter e tem acesso a todos os objetos no datacenter.<br/><br/> Se desejar restringir o acesso, atribua a função **Sem acesso** com o objeto **Propagar para filho** aos objetos filho (hosts vSphere, armazenamentos de dados, máquinas virtuais e redes).<br/><br/> É útil para fins de migração, mas não para replicação completa, failover ou failback.|
|**Failover e failback** | Sugerimos que você crie uma função (AzureSiteRecoveryRole) com as permissões necessárias e atribua a função a um usuário ou grupo do VMware | Objeto de Data Center –> Propagar para o Objeto Filho, role=AzureSiteRecoveryRole<br/><br/> Armazenamento de dados -> alocar espaço, procurar armazenamento de dados, operações de arquivo de baixo nível, remover arquivo, atualizar arquivos de máquina virtual<br/><br/> Rede -> Atribuição de rede<br/><br/> Recurso -> Atribuir VM ao pool de recursos, migrar VM desligada, migrar VM ligada<br/><br/> Tarefas -> Criar tarefa, atualizar tarefa<br/><br/> Máquina virtual -> Configuração<br/><br/> Máquina virtual -> Interagir -> responder à pergunta, conexão de dispositivo, configurar mídia de CD, configurar mídia de disquete, desligar, ligar, instalação de ferramentas VMware<br/><br/> Máquina virtual -> Inventário -> Criar, registrar, cancelar registro<br/><br/> Máquina virtual -> Provisionamento -> Permitir download de máquina virtual, permitir upload de arquivos de máquina virtual<br/><br/> Máquina virtual -> Instantâneos -> Remover instantâneos | Usuário atribuído ao nível de datacenter e tem acesso a todos os objetos no datacenter.<br/><br/> Se desejar restringir o acesso, atribua a função **Nenhum acesso** com o objeto **Propagar para objeto filho** aos objetos filho (hosts vSphere, armazenamentos de dados, máquinas virtuais e redes).|

## <a name="create-an-account-to-connect-to-vmware-vcenter-server-vmware-vsphere-exsi-host"></a>Criar uma conta para se conectar ao VMware vCenter Server/host EXSi do VMware vSphere
1. Faça logon no servidor de configuração e inicie o cspsconfigtool.exe usando o atalho colocado na área de trabalho.
2. Na guia **Gerenciar Conta**, clique em **Adicionar Conta**.

  ![add-account](./media/site-recovery-vmware-to-azure-manage-vcenter/addaccount.png)
3. Forneça os detalhes da conta e clique em OK para adicionar a conta. A conta deve ter os privilégios listados na seção [Preparar uma conta para a descoberta automática](#prepare-an-account-for-automatic-discovery).

  >[!NOTE]
  Demora cerca de 15 minutos para que as informações de conta sejam sincronizadas com o serviço do Site Recovery.


## <a name="associate-a-vmware-vcenter-vmware-vsphere-esx-host-add-vcenter"></a>Associar um VMware vCenter/host ESX do VMware vSphere (adicionar vCenter)
* No Portal do Azure, navegue até *YourRecoveryServicesVault* > **Infraestrutura do Site Recovery** > **Servidores de Configuração** > *ConfigurationServer*
* Na página de detalhes do servidor de configuração, clique no botão +vCenter.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials-used-to-connect-to-the-vcenter-server-vsphere-esxi-host"></a>Modificar as credenciais usadas para se conectar ao vCenter Server/host ESXi do vSphere

1. Fazer logon no servidor de configuração e iniciar o cspsconfigtool.exe
2. Na guia **Gerenciar Conta**, clique em **Adicionar Conta**.

  ![add-account](./media/site-recovery-vmware-to-azure-manage-vcenter/addaccount.png)
3. Forneça os detalhes da nova conta e clique em OK para adicionar a conta. A conta deve ter os privilégios listados na seção [Preparar uma conta para a descoberta automática](#prepare-an-account-for-automatic-discovery).
4. No Portal do Azure, navegue até *YourRecoveryServicesVault* > **Infraestrutura do Site Recovery** > **Servidores de Configuração** > *ConfigurationServer*
5. Na página de detalhes do servidor de configuração, clique no botão **Atualizar Servidor**.
6. Depois de concluído o trabalho de atualização de servidor, selecione o vCenter Server para abrir a página de resumo do vCenter.
7. Selecione a conta adicionada recentemente no campo **Conta de host do vCenter server/vSphere** e clique no botão **Salvar**.

  ![modify-account](./media/site-recovery-vmware-to-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-in-azure-site-recovery"></a>Excluir um vCenter no Azure Site Recovery
1. No Portal do Azure, navegue até *YourRecoveryServicesVault* > **Infraestrutura do Site Recovery** > **Servidores de Configuração** > *ConfigurationServer*
2. Na página de detalhes do servidor de configuração, selecione o vCenter Server para abrir a página Resumo do vCenter.
3. Clique no botão **Excluir** para excluir o vCenter

  ![delete-account](./media/site-recovery-vmware-to-azure-manage-vcenter/delete-vcenter.png)

> [!NOTE]
Se você precisar modificar o endereço IP/FQDN ou detalhes de porta dos vCenters, você precisará excluir o vCenter Server e adicioná-lo novamente.

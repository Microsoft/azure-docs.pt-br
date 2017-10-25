---
title: "Backup e restauração de VMs criptografadas usando o Backup do Azure"
description: "Este artigo aborda a experiência de backup e restauração para VMs criptografadas usando o Azure Disk Encryption."
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 8387f186-7d7b-400a-8fc3-88a85403ea63
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/18/2017
ms.author: pajosh;markgal;trinadhk
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1fbcde5af7668971bbeafc41b237aa89c593c4a6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-and-restore-encrypted-virtual-machines-with-azure-backup"></a>Backup e restauração de máquinas virtuais criptografadas usando o Backup do Azure
Este artigo descreve as etapas para fazer backup e restaurar VMs (máquinas virtuais) usando o Backup do Azure. Ele também oferece detalhes sobre os cenários com suporte, os pré-requisitos e as etapas de solução de problemas para casos de erro.

## <a name="supported-scenarios"></a>Cenários com suporte

 * O backup e a restauração de VMs criptografadas têm suporte somente para VMs que usam o modelo de implantação do Azure Resource Manager. Não há suporte para VMs que usam o modelo de implantação clássico. <br>
 * O backup e restauração de VMs criptografadas tem suporte para VMs Windows e Linux que usam o Azure Disk Encryption. O Disk Encryption usa o BitLocker, um recurso do Windows padrão da indústria e o recurso dm-crypt do Linux para fornecer a criptografia de discos. <br>
 
 A tabela a seguir mostra os cenários com suporte para VMs criptografadas somente por BEK (chave de criptografia BitLocker) e por KEK (chave de criptografia de chave):
 
 
   |  | VMs BEK + KEK | VMs somente com BEK |
   | --- | --- | --- |
   | **VMs não gerenciadas**  | Sim | Sim  |
   | **VMs gerenciadas**  | Sim | Não  |

## <a name="prerequisites"></a>Pré-requisitos
* A VM foi criptografada usando o [Azure Disk Encryption](../security/azure-security-disk-encryption.md).

* O cofre dos Serviços de Recuperação foi criado e a replicação de armazenamento foi definida seguindo as etapas em [Preparar seu ambiente para backup](backup-azure-arm-vms-prepare.md).

* O backup recebeu [permissões para acessar o cofre de chaves](#provide-permissions-to-azure-backup) que contém chaves e segredos para VMs criptografadas.

## <a name="backup-encrypted-vm"></a>Backup de VM criptografada
Use as etapas a seguir para definir a meta de backup, definir uma política, configurar itens e disparar um backup.

### <a name="configure-backup"></a>Configurar o backup
1. Se já tiver um cofre dos Serviços de Recuperação aberto, vá para a próxima etapa. Se não tiver um cofre dos Serviços de Recuperação aberto, mas estiver no portal do Azure, no menu **Hub**, clique em **Procurar**.

   a. Na lista de recursos, digite **Serviços de Recuperação**.

   b. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Quando você vir a opção **cofres de Serviços de Recuperação**, selecione-a.

      ![Cofre dos Serviços de Recuperação](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

    c. A lista de cofres de Serviços de Recuperação aparecerá. Selecione um cofre na lista.

     O painel de cofres selecionados será aberto.
2. Na lista de itens que aparece no cofre, selecione **Backup** para começar a fazer backup da VM criptografada.

      ![Folha Backup](./media/backup-azure-vms-encryption/select-backup.png)
3. No bloco **Backup**, selecione **Meta de backup**.

      ![Folha Cenário](./media/backup-azure-vms-encryption/select-backup-goal-one.png)
4. Em **Onde sua carga de trabalho é executada?**, selecione **Azure**. Em **Do que você deseja fazer backup?**, selecione **Máquina virtual**. Depois, selecione **OK**.

   ![Abrir a folha Cenário](./media/backup-azure-vms-encryption/select-backup-goal-two.png)
5. Em **Escolher política de backup**, selecione a política de backup que você deseja aplicar ao cofre. Depois, selecione **OK**.

      ![Selecionar a política de backup](./media/backup-azure-vms-encryption/setting-rs-backup-policy-new.png)

    Os detalhes da política padrão estão listados. Se quiser criar uma política, selecione **Criar Nova** na lista suspensa. Após você selecionar **OK**, a política de backup será associada ao cofre.

6. Escolha as VMs criptografadas para associar à política especificada e selecione **OK**.

      ![Selecionar VMs criptografadas](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)
7. Essa página mostra uma mensagem sobre os cofres de chaves associados às VMs criptografadas selecionadas. O backup requer acesso somente leitura às chaves e segredos no cofre de chaves. Ele usa essas permissões para fazer backup das chaves e segredos, junto com as VMs associadas. *Você deve fornecer permissões para que o serviço de backup acesse o cofre de chaves para que os backups funcionem*. Você pode fornecer essas permissões seguindo as [etapas mencionadas na seção abaixo](#provide-permissions-to-azure-backup).

      ![Mensagem de VMs criptografada](./media/backup-azure-vms-encryption/encrypted-vm-warning-message.png)

      Agora que você definiu todas as configurações para o cofre, selecione **Habilitar Backup** na parte inferior da página. **Habilitar Backup** implanta a política para o cofre e as VMs.
8. A próxima fase de preparação será instalar o Agente de VM ou verificar se ele está instalado. Para fazer o mesmo, siga as etapas em [Preparar o ambiente para backup](backup-azure-arm-vms-prepare.md).

### <a name="trigger-a-backup-job"></a>Disparar um trabalho de backup
Siga as etapas em [Fazer backup das VMs do Azure no cofre dos Serviços de Recuperação](backup-azure-arm-vms.md) para disparar um trabalho de backup.

### <a name="continue-backups-of-already-backed-up-vms-with-encryption-enabled"></a>Continuar os backups de VMs cujo backup já foi realizado com a criptografia habilitada  
Se você tiver VMs cujo backup já estiver sendo feito em um cofre dos Serviços de Recuperação e que foram habilitadas para criptografia posteriormente, você deverá conceder permissões para Backup acessar o cofre de chaves para que os backups continuem. Você pode fornecer essas permissões seguindo as [etapas mencionadas na seção abaixo](#provide-permissions-to-azure-backup). Ou pode seguir as etapas do PowerShell na seção "Habilitar backup" da [Documentação do PowerShell](backup-azure-vms-automation.md). 

## <a name="provide-permissions-to-backup"></a>Fornecer permissões para o Backup
Use as etapas a seguir para fornecer as permissões relevantes para o Backup acessar o cofre de chaves e fazer backup de VMs criptografadas.
1. Selecione **Mais serviços** e pesquise **Cofres de chaves**.

    ![Cofres de chaves](./media/backup-azure-vms-encryption/search-key-vault.png)
    
2. Na lista de cofres de chaves, selecione o cofre de chaves associado à VM criptografada cujo backup precisa ser feito.

     ![Seleção de cofre de chaves](./media/backup-azure-vms-encryption/select-key-vault.png)
     
3. Selecione **Políticas de acesso** e selecione **Adicionar nova**.

    ![Adicionar nova](./media/backup-azure-vms-encryption/select-key-vault-access-policy.png)
    
4. Selecione **Selecionar entidade** e digite **Serviço de Gerenciamento de Backup** na caixa de pesquisa. 

    ![Pesquisa de serviço de backup](./media/backup-azure-vms-encryption/search-backup-service.png)
    
5. Selecione **Serviço de Gerenciamento de Backup** e clique em **Selecionar**.

    ![Seleção de serviço de backup](./media/backup-azure-vms-encryption/select-backup-service.png)
    
6. Selecione **Backup do Azure** em **Configurar usando o modelo (opcional)**. As permissões necessárias para **Permissões de chave** e **Permissões de segredo** são preenchidas. Se sua VM for criptografada usando **somente BEK**, serão necessárias apenas as permissões para segredos, portanto você deve remover a seleção de **Permissões de chave**.

    ![Seleção de backup do Azure](./media/backup-azure-vms-encryption/select-backup-template.png)
    
7. Selecione **OK**. Observe que o **Serviço de Gerenciamento de Backup** é adicionado em **Políticas de acesso**. 

    ![Políticas de acesso](./media/backup-azure-vms-encryption/backup-service-access-policy.png)
    
8. Selecione **Salvar** para conceder as permissões necessárias ao Backup.

    ![Política de acesso de backup](./media/backup-azure-vms-encryption/save-access-policy.png)

Após as permissões terem sido concedidas com êxito, você poderá continuar com a habilitação do backup para VMs criptografadas.

## <a name="restore-an-encrypted-vm"></a>Restaurar uma VM criptografada
Para restaurar uma VM criptografada, primeiro restaure os discos seguindo as etapas na seção "Restaurar discos com backup" em [Escolha uma configuração de restauração de VM](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration). Depois disso, você pode usar uma das seguintes opções:

* Use as etapas do PowerShell em [Criar uma VM de discos restaurados](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) para criar uma VM completa de discos restaurados.
* Ou, [use modelos para personalizar uma VM restaurada](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm) para criar VMs de discos restaurados. Modelos podem ser usados somente para pontos de recuperação criados após 26 de abril de 2017.

## <a name="troubleshooting-errors"></a>Solucionar erros
| Operação | Detalhes do erro | Resolução |
| --- | --- | --- |
|Backup | O backup não tem permissões suficientes para o cofre de chaves para fazer backup de VMs criptografadas. | A VM deve ser criptografada usando uma BEK e uma KEK. Depois disso, o backup deve ser habilitado. O backup deve receber essas permissões seguindo [as etapas na seção anterior](#provide-permissions-to-azure-backup). Ou você pode seguir as etapas do PowerShell na seção "Habilitar proteção" da documentação do PowerShell em [Usar os cmdlets AzureRM.RecoveryServices.Backup para fazer backup de máquinas virtuais](backup-azure-vms-automation.md#back-up-azure-vms). |  
| Backup |A validação falhou porque a VM foi criptografada somente com uma BEK. Os backups podem ser habilitados somente para VMs criptografadas com uma BEK e uma KEK. |A VM deve ser criptografada usando uma BEK e uma KEK. Primeiro, descriptografe a VM e criptografe-a usando uma BEK e uma KEK. Habilite o backup após a VM ser criptografada usando uma BEK e uma KEK. Saiba mais sobre como você pode [descriptografar e criptografar a VM](../security/azure-security-disk-encryption.md).  |
| Restaurar |Você não poderá restaurar essa VM criptografada pois o cofre de chaves associado a ela não existe. |Crie o cofre de chaves usando [Introdução ao Azure Key Vault](../key-vault/key-vault-get-started.md). Consulte [Restaurar chave e segredo do cofre de chaves usando o Backup do Azure](backup-azure-restore-key-secret.md) para restaurar a chave e o segredo, se eles não estiverem presentes. |
| Restaurar |Você não poderá restaurar essa VM criptografada pois a chave e o segredo associado a ela não existem. |Consulte [Restaurar chave e segredo do cofre de chaves usando o Backup do Azure](backup-azure-restore-key-secret.md) para restaurar a chave e o segredo, se eles não estiverem presentes. |
| Restaurar |O backup não tem autorização para acessar recursos em sua assinatura. |Conforme mencionado anteriormente, primeiro restaure os discos seguindo as etapas na seção "Restaurar discos com backup" em [Escolha uma configuração de restauração de VM](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration). Depois disso, use o PowerShell para [criar uma VM de discos restaurados](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). |


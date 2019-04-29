---
title: Fazer backup e restaurar VMs criptografadas do Azure com o Backup do Azure
description: Descreve como fazer backup e restaurar VMs criptografadas do Azure com o serviço de Backup do Azure.
services: backup
author: geetha
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 4/3/2019
ms.author: geetha
ms.openlocfilehash: 893a22fb9f325625707869c8f6571d572b8f6b33
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61216880"
---
# <a name="back-up-and-restore-encrypted-azure-vm"></a>Fazer backup e restaurar a VM do Azure criptografada

Este artigo descreve como fazer backup e restaurar máquinas virtuais Windows ou Linux do Azure (VMs) com discos criptografados usando o [Backup do Azure](backup-overview.md) service.

Se você quiser saber mais sobre como o Backup do Azure interage com VMs do Azure antes de começar, revise estes recursos:

- [Revisão](backup-architecture.md#architecture-direct-backup-of-azure-vms) a arquitetura de backup de VM do Azure.
- [Saiba mais sobre](backup-azure-vms-introduction.md) backup de VM do Azure e a extensão de Backup do Azure.

## <a name="encryption-support"></a>Suporte à criptografia

O Backup do Azure dá suporte ao backup de VMs do Azure que têm seus discos de sistema operacional/dados criptografados com o Azure Disk Encryption (ADE). ADE usa o BitLocker para criptografia de VMs do Windows e o recurso dm-crypt para VMs do Linux. ADE se integra ao Azure Key Vault para gerenciar chaves de criptografia de disco e segredos. Chaves de criptografia de chave de Cofre de chave (KEKs) pode ser usadas para adicionar uma camada adicional de segurança, criptografia de segredos de criptografia antes de gravá-los em um cofre de chaves.

O Backup do Azure pode fazer backup e restaurar VMs do Azure usando o ADE com e sem o aplicativo do Azure AD, conforme resumido na tabela a seguir.

**Tipo de disco da VM** | **ADE (BEK/dm-crypt)** | **ADE e KEK**
--- | --- | ---
**não gerenciado** | Sim | Sim
**Gerenciado**  | Sim | Sim

- Saiba mais sobre [ADE](../security/azure-security-disk-encryption-overview.md), [Key Vault](../key-vault/key-vault-overview.md), e [KEKs](https://blogs.msdn.microsoft.com/cclayton/2017/01/03/creating-a-key-encrypting-key-kek/).
- Leia as [perguntas frequentes sobre](../security/azure-security-disk-encryption-faq.md) para criptografia de disco de VM do Azure.



### <a name="limitations"></a>Limitações

- Você pode fazer backup e restaurar VMs criptografadas na mesma assinatura e região.
- O Backup do Azure dá suporte a VMs criptografadas usando chaves autônomo. Atualmente, não há suporte para qualquer chave que é uma parte de um certificado usado para criptografar uma VM.
- Você pode fazer backup e restaurar VMs criptografadas na mesma assinatura e região que o Cofre de Backup de serviços de recuperação.
- As VMs criptografadas não podem ser recuperadas no nível de arquivo/pasta. Você precisa recuperar toda a VM para restaurar arquivos e pastas.
- Ao restaurar uma VM, você não pode usar o [substituir a VM existente](backup-azure-arm-restore-vms.md#restore-options) opção para VMs criptografadas. Essa opção só há suporte para discos gerenciados não criptografados.




## <a name="before-you-start"></a>Antes de começar

Antes de começar, faça o seguinte:

1. Verifique se você tem um ou mais [Windows](../security/azure-security-disk-encryption-windows.md) ou [Linux](../security/azure-security-disk-encryption-linux.md) VMs com ADE habilitado.
2. [Examine a matriz de suporte](backup-support-matrix-iaas.md) para backup de VM do Azure
3. [Criar](backup-azure-arm-vms-prepare.md#create-a-vault) um cofre de Backup de serviços de recuperação se você não tiver uma.
4. Se você habilitar a criptografia para VMs que já estão habilitados para backup, basta fornecer o Backup com permissões para acessar o Cofre de chaves para que os backups continuem sem interrupção. [Saiba mais](#provide-permissions) sobre como atribuir essas permissões.

Além disso, há algumas coisas que você talvez precise fazer em algumas circunstâncias:

- **Instalar o agente de VM na VM**: O Backup do Azure faz backup de VMs do Azure instalando uma extensão para o agente de VM do Azure em execução no computador. Se sua VM foi criada a partir de uma imagem do marketplace do Azure, o agente é instalado e em execução. Se você cria uma VM personalizada ou se você migrar uma máquina local, talvez você precise [instale manualmente o agente](backup-azure-arm-vms-prepare.md#install-the-vm-agent).
- **Permitir o acesso de saída explicitamente**: Em geral, você não precisa permitir o acesso de rede de saída explicitamente para uma VM do Azure para que ele se comunicar com o Backup do Azure. No entanto, algumas VMs podem apresentar problemas de conexão, mostrando os **ExtensionSnapshotFailedNoNetwork** erro ao tentar se conectar. Se isso acontecer, você deve [permitir o acesso de saída explicitamente](backup-azure-arm-vms-prepare.md#explicitly-allow-outbound-access), portanto, a extensão de Backup do Azure pode se comunicar com os endereços IP públicos do Azure para o tráfego de backup.



## <a name="configure-a-backup-policy"></a>Configurar uma política de backup

1. Se você ainda não criou um cofre de backup de serviços de recuperação, execute [estas instruções](backup-azure-arm-vms-prepare.md#create-a-vault)
2. Abra o cofre no portal e selecione **Backup** na **Introdução** seção.

    ![Folha Backup](./media/backup-azure-vms-encryption/select-backup.png)

3. Na **meta de Backup** > **onde sua carga de trabalho é executada?** selecione **Azure**.
4. Na **o que você deseja fazer backup?** selecionar **Máquina Virtual** > **Okey**.

      ![Folha Cenário](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

5. Na **política de Backup** > **escolher política de backup**, selecione a política que você deseja associar ao cofre. Em seguida, clique em **OK**.
    - Uma política de backup Especifica quando backups são feitos e por quanto tempo eles são armazenados.
    - Os detalhes da política padrão estão listados no menu suspenso.

    ![Abrir a folha Cenário](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

6. Se você não quiser usar a política padrão, selecione **criar novo**, e [criar uma política personalizada](backup-azure-arm-vms-prepare.md#create-a-custom-policy).


7. Escolha as VMs criptografadas que você deseja fazer backup usando a política de seleção e selecione **Okey**.

      ![Selecionar VMs criptografadas](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

8. Se você estiver usando o Azure Key Vault, na página do cofre, você verá uma mensagem que o Backup do Azure precisa de acesso somente leitura às chaves e segredos no cofre de chaves.

    - Se você receber essa mensagem, nenhuma ação é necessária.

        ![Acesso Okey](./media/backup-azure-vms-encryption/access-ok.png)

    - Se você receber essa mensagem, você precisará definir permissões, conforme descrito na [procedimento a seguir](#provide-permissions).

        ![Aviso de acesso](./media/backup-azure-vms-encryption/access-warning.png)

9. Clique em **habilitar Backup** para implantar a política de backup no cofre e habilitar o backup para as VMs selecionadas.


## <a name="trigger-a-backup-job"></a>Disparar um trabalho de backup

O backup inicial será executado de acordo com o agendamento, mas você pode executá-lo imediatamente da seguinte maneira:

1. No menu do cofre, clique em **Itens de backup**.
2. Em **Itens de Backup**, clique em **Máquina Virtual do Azure**.
3. No **itens de Backup** lista, clique nas reticências (...).
4. Clique em **Fazer backup agora**.
5. Na **fazer Backup agora**, use o controle de calendário para selecionar o último dia em que o ponto de recuperação deve ser mantido. Em seguida, clique em **OK**.
6. Monitorar as notificações do portal. Você pode monitorar o andamento do trabalho no painel do cofre > **Trabalhos de Backup** > **Em Andamento**. Dependendo do tamanho da VM, a criação do backup inicial pode demorar um pouco.


## <a name="provide-permissions"></a>Forneça permissões

VM do Azure precisa de acesso somente leitura para fazer backup das chaves e segredos, junto com as VMs associadas.

- Seu Cofre de chaves está associado com o locatário do AD do Azure da assinatura do Azure. Se você for um **usuário membro**, o Backup do Azure adquire acesso ao Cofre de chave sem fazer mais nada.
- Se você for um **usuário convidado**, você deve fornecer permissões para o Backup do Azure acessar o Cofre de chaves.

Para definir permissões:

1. No portal do Azure, selecione **todos os serviços**e procure **cofres de chaves**.
2. Selecione o Cofre de chaves associado à VM criptografada estiver fazendo backup.
3. Selecione **políticas de acesso** > **adicionar novo**.
4. Selecione **selecionar entidade**e, em seguida, digite **gerenciamento de Backup**.
5. Selecione **serviço de gerenciamento de Backup** > **selecione**.

    ![Seleção de serviço de backup](./media/backup-azure-vms-encryption/select-backup-service.png)

6. Na **adicionar política de acesso** > **configurar usando o modelo (opcional)**, selecione **Backup do Azure**.
    - As permissões necessárias para **Permissões de chave** e **Permissões de segredo** são preenchidas.
    - Se sua VM é criptografada usando **somente BEK**, remova a seleção para **permissões de chave** , pois você só precisa de permissões para segredos.

    ![Seleção de backup do Azure](./media/backup-azure-vms-encryption/select-backup-template.png)

6. Clique em **OK**. **Serviço de gerenciamento de backup** é adicionado ao **políticas de acesso**.

    ![Políticas de acesso](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

7. Clique em **salvar** para fornecer o Backup do Azure com as permissões.

## <a name="restore-an-encrypted-vm"></a>Restaurar uma VM criptografada

Você pode restaurar VMs criptografadas da seguinte maneira:

1. [Restaurar o disco VM](backup-azure-arm-restore-vms.md#restore-disks).
2. Em seguida, faça o seguinte:
    - Use o modelo gerado durante a operação de restauração para personalizar as configurações da VM e disparar a implantação da VM. [Saiba mais](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm).
    - Crie uma nova VM de discos restaurados usando o Powershell. [Saiba mais](backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

## <a name="next-steps"></a>Próximas etapas

Se você tiver algum problema, examine

- [Erros comuns de](backup-azure-vms-troubleshoot.md) quando fazendo backup e restaurando VMs do Azure criptografadas.
- [Extensão de agente/backup VM do Azure](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) problemas.

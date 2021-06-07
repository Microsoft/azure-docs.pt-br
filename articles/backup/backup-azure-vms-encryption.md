---
title: Fazer backup e restaurar VMs do Azure criptografadas
description: Descreve como fazer backup e restaurar VMs do Azure criptografadas com o serviço de backup do Azure.
ms.topic: conceptual
ms.date: 08/18/2020
ms.openlocfilehash: db06b64fba203fb3d2ed54d34235504ac6aa4e2d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99223450"
---
# <a name="back-up-and-restore-encrypted-azure-virtual-machines"></a>Fazer backup e restaurar máquinas virtuais do Azure criptografadas

Este artigo descreve como fazer backup e restaurar VMs (máquinas virtuais) do Azure do Windows ou Linux com discos criptografados usando o serviço de [backup do Azure](backup-overview.md) . Para obter mais informações, consulte [criptografia de backups de VM do Azure](backup-azure-vms-introduction.md#encryption-of-azure-vm-backups).

## <a name="encryption-using-platform-managed-keys"></a>Criptografia usando chaves gerenciadas pela plataforma

Por padrão, todos os discos em suas VMs são automaticamente criptografados em repouso usando chaves gerenciadas por plataforma (PMK) que usam a [criptografia do serviço de armazenamento](../storage/common/storage-service-encryption.md). Você pode fazer backup dessas VMs usando o backup do Azure sem nenhuma ação específica necessária para dar suporte à criptografia no seu fim. Para obter mais informações sobre criptografia com chaves gerenciadas por plataforma, [consulte este artigo](../virtual-machines/disk-encryption.md#platform-managed-keys).

![Discos criptografados](./media/backup-encryption/encrypted-disks.png)

## <a name="encryption-using-customer-managed-keys"></a>Criptografia usando chaves gerenciadas pelo cliente

Quando você criptografa discos com chaves gerenciadas pelo cliente (CMK), a chave usada para criptografar os discos é armazenada no Azure Key Vault e é gerenciada por você. Criptografia do Serviço de Armazenamento (SSE) usando CMK difere da criptografia Azure Disk Encryption (ADE). O ADE usa as ferramentas de criptografia do sistema operacional. A SSE criptografa os dados no serviço de armazenamento, permitindo que você use qualquer sistema operacional ou imagens para suas VMs.

Você não precisa executar ações explícitas para backup ou restauração de VMs que usam chaves gerenciadas pelo cliente para criptografar seus discos. Os dados de backup para essas VMs armazenados no cofre serão criptografados com os mesmos métodos que a [criptografia usada no cofre](encryption-at-rest-with-cmk.md).

Para obter mais informações sobre criptografia de discos gerenciados com chaves gerenciadas pelo cliente, consulte [Este artigo](../virtual-machines/disk-encryption.md#customer-managed-keys).

## <a name="encryption-support-using-ade"></a>Suporte à criptografia usando ADE

O backup do Azure dá suporte ao backup de VMs do Azure que têm seus discos de sistema operacional/dados criptografados com o Azure Disk Encryption (ADE). O ADE usa o BitLocker para criptografia de VMs do Windows e o recurso DM-cript para VMs do Linux. O ADE integra-se com Azure Key Vault para gerenciar chaves e segredos de criptografia de disco. Key Vault KEKs (chaves de criptografia de chave) podem ser usadas para adicionar uma camada adicional de segurança, criptografando segredos de criptografia antes de gravá-los no Key Vault.

O backup do Azure pode fazer backup e restaurar VMs do Azure usando ADE com e sem o aplicativo do Azure AD, conforme resumido na tabela a seguir.

**Tipo de disco da VM** | **ADE (BEK/DM-cript.)** | **ADE e KEK**
--- | --- | ---
**Não gerenciado** | Sim | Sim
**Gerenciado**  | Sim | Sim

- Saiba mais sobre [Ade](../security/fundamentals/azure-disk-encryption-vms-vmss.md), [Key Vault](../key-vault/general/overview.md)e [KEKs](../virtual-machine-scale-sets/disk-encryption-key-vault.md#set-up-a-key-encryption-key-kek).
- Leia as [perguntas frequentes](../security/fundamentals/azure-disk-encryption-vms-vmss.md) sobre a criptografia de disco de VM do Azure.

### <a name="limitations"></a>Limitações

- Você pode fazer backup e restaurar VMs criptografadas do ADE dentro da mesma assinatura e região.
- O backup do Azure dá suporte a VMs criptografadas usando chaves autônomas. Qualquer chave que faz parte de um certificado usado para criptografar uma VM não tem suporte no momento.
- Você pode fazer backup e restaurar VMs criptografadas em ADE na mesma assinatura e região que o cofre de backup dos serviços de recuperação.
- As VMs criptografadas em ADE não podem ser recuperadas no nível de arquivo/pasta. Você precisa recuperar toda a VM para restaurar arquivos e pastas.
- Ao restaurar uma VM, você não pode usar a opção [substituir VM existente](backup-azure-arm-restore-vms.md#restore-options) para VMs criptografadas em Ade. Essa opção só tem suporte para discos gerenciados não criptografados.

## <a name="before-you-start"></a>Antes de começar

Antes de começar, faça o seguinte:

1. Verifique se você tem uma ou mais VMs do [Windows](../virtual-machines/linux/disk-encryption-overview.md) ou [Linux](../virtual-machines/linux/disk-encryption-overview.md) com o Ade habilitado.
2. [Examinar a matriz de suporte para o](backup-support-matrix-iaas.md) backup de VM do Azure
3. [Crie](backup-create-rs-vault.md) um cofre de backup dos serviços de recuperação se você não tiver um.
4. Se você habilitar a criptografia para VMs que já estão habilitadas para backup, bastará fornecer backup com permissões para acessar o Key Vault para que os backups possam continuar sem interrupções. [Saiba mais](#provide-permissions) sobre como atribuir essas permissões.

Além disso, há algumas ações que talvez você precise realizar em algumas circunstâncias:

- **Instale o agente de VM na VM**: O Backup do Azure faz backup de VMs do Azure instalando uma extensão para o agente de VM do Azure em execução no computador. Se a sua VM foi criada a partir de uma imagem do Azure Marketplace, o agente está instalado e em execução. Caso você crie uma VM personalizada ou migre um computador local, talvez seja necessário [instalar o agente manualmente](backup-azure-arm-vms-prepare.md#install-the-vm-agent).

## <a name="configure-a-backup-policy"></a>Configurar uma política de backup

1. Se você ainda não criou um cofre de backup dos serviços de recuperação, siga [estas instruções](backup-create-rs-vault.md).
1. Abra o cofre no portal e selecione **+ backup** na seção **visão geral** .

    ![Painel de backup](./media/backup-azure-vms-encryption/select-backup.png)

1. Em **meta de backup**  >  **em que sua carga de trabalho está em execução?** selecione **Azure**.
1. Em **o que você deseja fazer backup?** selecione a **máquina virtual**. Em seguida, selecione **backup**.

      ![Painel cenário](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

1. Em **política de backup**  >  ,**escolha política de backup**, selecione a política que você deseja associar ao cofre. Depois, selecione **OK**.
    - Uma política de backup especifica quando os backups são feitos e por quanto tempo eles são armazenados.
    - Os detalhes da política padrão estão listados no menu suspenso.

    ![Escolher política de backup](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

1. Se você não quiser usar a política padrão, selecione **criar nova** e [criar uma política personalizada](backup-azure-arm-vms-prepare.md#create-a-custom-policy).

1. Em **Máquinas virtuais**, selecione **Adicionar**.

    ![Adicionar máquinas virtuais](./media/backup-azure-vms-encryption/add-virtual-machines.png)

1. Escolha as VMs criptografadas que você deseja fazer backup usando a política selecionar e selecione **OK**.

      ![Selecionar VMs criptografadas](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

1. Se estiver usando Azure Key Vault, na página do cofre, você verá uma mensagem informando que o backup do Azure precisa de acesso somente leitura às chaves e aos segredos no Key Vault.

    - Se você receber essa mensagem, nenhuma ação será necessária.

        ![Acesso OK](./media/backup-azure-vms-encryption/access-ok.png)

    - Se você receber essa mensagem, precisará definir permissões conforme descrito no [procedimento abaixo](#provide-permissions).

        ![Aviso de acesso](./media/backup-azure-vms-encryption/access-warning.png)

1. Selecione **habilitar backup** para implantar a política de backup no cofre e habilite o backup para as VMs selecionadas.

## <a name="trigger-a-backup-job"></a>Disparar um trabalho de backup

O backup inicial será executado de acordo com o agendamento, mas você poderá executá-lo imediatamente da seguinte maneira:

1. No menu do cofre, selecione **itens de backup**.
2. Em **itens de backup**, selecione **máquina virtual do Azure**.
3. Na lista **itens de backup** , selecione as reticências (...).
4. Selecione **fazer backup agora**.
5. Em **Fazer Backup Agora**, use o controle de calendário para selecionar o último dia em que o ponto de recuperação deve ser mantido. Depois, selecione **OK**.
6. Monitorar as notificações do portal. Você pode monitorar o andamento do trabalho no painel do cofre > **Trabalhos de Backup** > **Em Andamento**. Dependendo do tamanho da VM, a criação do backup inicial pode demorar um pouco.

## <a name="provide-permissions"></a>Fornecer permissões

O backup do Azure precisa de acesso somente leitura para fazer backup das chaves e dos segredos, juntamente com as VMs associadas.

- Seu Key Vault está associado ao locatário do Azure AD da assinatura do Azure. Se você for um **usuário membro**, o backup do Azure adquire acesso ao key Vault sem ação adicional.
- Se você for um **usuário convidado**, deverá fornecer permissões para o backup do Azure acessar o cofre de chaves.

Para definir permissões:

1. Na portal do Azure, selecione **todos os serviços** e procure por **cofres de chaves**.
1. Selecione o cofre de chaves associado à VM criptografada que você está fazendo backup.

    >[!TIP]
    >Para identificar o cofre de chaves associado de uma VM, use o comando do PowerShell a seguir. Substitua o nome do grupo de recursos e o nome da VM:
    >
    >`Get-AzVm -ResourceGroupName "MyResourceGroup001" -VMName "VM001" -Status`
    >
    > Procure o nome do cofre de chaves nesta linha:
    >
    >`SecretUrl            : https://<keyVaultName>.vault.azure.net`
    >

1. Selecione **políticas de acesso**  >  **Adicionar política de acesso**.

    ![Adicionar política de acesso](./media/backup-azure-vms-encryption/add-access-policy.png)

1. Em **Adicionar política**  >  **de acesso configurar do modelo (opcional)**, selecione **backup do Azure**.
    - As permissões necessárias para **Permissões de chave** e **Permissões de segredo** são preenchidas.
    - Se sua VM for criptografada usando **apenas Bek**, remova a seleção de **permissões de chave** , pois você só precisa de permissões para segredos.

    ![Seleção de backup do Azure](./media/backup-azure-vms-encryption/select-backup-template.png)

1. Selecione **Adicionar**. O **serviço de gerenciamento de backup** é adicionado às políticas de **acesso**.

    ![Políticas de acesso](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

1. Selecione **salvar** para fornecer o backup do Azure com as permissões.

## <a name="restore-an-encrypted-vm"></a>Restaurar uma VM criptografada

As VMs criptografadas só podem ser restauradas restaurando o disco da VM, conforme explicado abaixo. Não há suporte para a substituição de VM **existente** e de **restauração** .

Restaure as VMs criptografadas da seguinte maneira:

1. [Restaure o disco da VM](backup-azure-arm-restore-vms.md#restore-disks).
2. Recrie a instância de máquina virtual executando uma das seguintes ações:
    1. Use o modelo gerado durante a operação de restauração para personalizar as configurações da VM e disparar a implantação da VM. [Saiba mais](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm).
    2. Crie uma nova VM com base nos discos restaurados usando o PowerShell. [Saiba mais](backup-azure-vms-automation.md#create-a-vm-from-restored-disks).
3. Para VMs do Linux, reinstale a extensão ADE para que os discos de dados sejam abertos e montados.

## <a name="next-steps"></a>Próximas etapas

Se você tiver problemas, leia estes artigos:

- [Erros comuns](backup-azure-vms-troubleshoot.md) ao fazer backup e restaurar VMs do Azure criptografadas.
- Problemas de [extensão de backup/agente de VM do Azure](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) .

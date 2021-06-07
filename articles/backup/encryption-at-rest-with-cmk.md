---
title: Criptografia de dados de backup usando chaves gerenciadas pelo cliente
description: Saiba como o backup do Azure permite que você criptografe seus dados de backup usando chaves gerenciadas pelo cliente (CMK).
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 474f4238276f460abde3d600422e309171875a0c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101716730"
---
# <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Criptografia de dados de backup usando chaves gerenciadas pelo cliente

O backup do Azure permite que você criptografe seus dados de backup usando chaves gerenciadas pelo cliente (CMK) em vez de usar chaves gerenciadas por plataforma, que são habilitadas por padrão. As chaves que são usadas para criptografar os dados de backup devem ser armazenadas em [Azure Key Vault](../key-vault/index.yml).

A chave de criptografia usada para criptografar backups pode ser diferente da usada para a origem. Os dados são protegidos usando uma chave de criptografia de dados baseada em AES 256 (DEK), que, por sua vez, é protegido usando suas chaves (KEK). Isso lhe dá controle total sobre os dados e as chaves. Para permitir a criptografia, é necessário que o cofre dos serviços de recuperação receba acesso à chave de criptografia no Azure Key Vault. Você pode alterar a chave como e quando necessário.

Este artigo discute o seguinte:

- Criando um cofre dos serviços de recuperação
- Configurando seu cofre de serviços de recuperação para criptografar dados de backup usando chaves gerenciadas pelo cliente
- Executando backup em cofres criptografados usando chaves gerenciadas pelo cliente
- Restaurando dados de backups

## <a name="before-you-start"></a>Antes de começar

- Esse recurso permite que você criptografe **somente os novos cofres dos serviços de recuperação**. Todos os cofres que contêm itens existentes registrados ou que tentaram ser registrados nele não têm suporte.

- Uma vez habilitado para um cofre dos serviços de recuperação, a criptografia usando chaves gerenciadas pelo cliente não pode ser revertida para usar chaves gerenciadas por plataforma (padrão). Você pode alterar as chaves de criptografia de acordo com seus requisitos.

- Atualmente, esse recurso **não dá suporte ao backup usando o agente Mars**, e você não poderá usar um cofre criptografado por CMK para o mesmo. O agente MARS usa uma criptografia baseada em senha do usuário. Esse recurso também não dá suporte ao backup de VMs clássicas.

- Esse recurso não está relacionado a [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md), que usa a criptografia baseada em convidado dos discos de uma VM usando o BitLocker (para Windows) e DM-Crypt (para Linux)

- O cofre dos serviços de recuperação pode ser criptografado somente com chaves armazenadas em um Azure Key Vault, localizado na **mesma região**. Além disso, as chaves devem ser apenas **chaves RSA 2048** e devem estar no estado **habilitado** .

- A movimentação do cofre dos serviços de recuperação criptografada CMK entre grupos de recursos e assinaturas não tem suporte no momento.
- Ao mover um cofre dos serviços de recuperação já criptografado com chaves gerenciadas pelo cliente para um novo locatário, você precisará atualizar o cofre dos serviços de recuperação para recriar e reconfigurar a identidade gerenciada do cofre e o CMK (que deve estar no novo locatário). Se isso não for feito, as operações de backup e restauração começarão a falhar. Além disso, qualquer permissão de RBAC (controle de acesso baseado em função) configurada na assinatura precisará ser reconfigurada.

- Esse recurso pode ser configurado por meio do portal do Azure e do PowerShell.

    >[!NOTE]
    >Use AZ Module 5.3.0 ou superior para usar chaves gerenciadas pelo cliente para backups no cofre dos serviços de recuperação.

Se você ainda não criou e configurou seu cofre de serviços de recuperação, você pode [ler como fazer isso aqui](backup-create-rs-vault.md).

## <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>Configurando um cofre para criptografar usando chaves gerenciadas pelo cliente

Esta seção envolve as seguintes etapas:

1. Habilitar identidade gerenciada para seu cofre de serviços de recuperação

1. Atribua permissões ao cofre para acessar a chave de criptografia no Azure Key Vault

1. Habilitar a proteção de exclusão e limpeza reversível no Azure Key Vault

1. Atribuir a chave de criptografia ao cofre dos serviços de recuperação

É necessário que todas essas etapas sejam seguidas na ordem mencionada acima para atingir os resultados pretendidos. Cada etapa é discutida em detalhes abaixo.

### <a name="enable-managed-identity-for-your-recovery-services-vault"></a>Habilitar identidade gerenciada para seu cofre de serviços de recuperação

O backup do Azure usa a identidade gerenciada atribuída pelo sistema para autenticar o cofre dos serviços de recuperação para acessar as chaves de criptografia armazenadas no Azure Key Vault. Para habilitar a identidade gerenciada para seu cofre de serviços de recuperação, siga as etapas mencionadas abaixo.

>[!NOTE]
>Uma vez habilitada, a identidade gerenciada **não** deve ser desabilitada (até mesmo temporariamente). Desabilitar a identidade gerenciada pode levar a um comportamento inconsistente.

**No Portal:**

1. Vá para o cofre dos serviços de recuperação-> **identidade**

    ![Configurações de identidade](./media/encryption-at-rest-with-cmk/managed-identity.png)

1. Altere o **status** para **ativado** e selecione **salvar**.

1. Uma ID de objeto é gerada, que é a identidade gerenciada atribuída pelo sistema do cofre.

**Com o PowerShell:**

Use o comando [Update-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/update-azrecoveryservicesvault) para habilitar a identidade gerenciada atribuída pelo sistema para o cofre dos serviços de recuperação.

Exemplo:

```AzurePowerShell
$vault=Get-AzRecoveryServicesVault -ResourceGroupName "testrg" -Name "testvault"

Update-AzRecoveryServicesVault -IdentityType SystemAssigned -VaultId $vault.ID

$vault.Identity | fl
```

Saída:

```output
PrincipalId : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
TenantId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Type        : SystemAssigned
```

### <a name="assign-permissions-to-the-recovery-services-vault-to-access-the-encryption-key-in-the-azure-key-vault"></a>Atribua permissões ao cofre dos serviços de recuperação para acessar a chave de criptografia no Azure Key Vault

Agora você precisa permitir que o cofre dos serviços de recuperação acesse o Azure Key Vault que contém a chave de criptografia. Isso é feito ao permitir que a identidade gerenciada do cofre dos serviços de recuperação acesse o Key Vault.

**No portal**:

1. Acesse suas **políticas de acesso** de Azure Key Vault >. Continue para **+ Adicionar políticas de acesso**.

    ![Adicionar políticas de acesso](./media/encryption-at-rest-with-cmk/access-policies.png)

1. Em **permissões de chave**, **selecione obter**, **listar**, **desencapsular** as operações de chave e **encapsular chave** . Isso especifica as ações na chave que será permitida.

    ![Atribuir permissões de chave](./media/encryption-at-rest-with-cmk/key-permissions.png)

1. Vá para **selecionar entidade de segurança** e pesquise seu cofre na caixa de pesquisa usando seu nome ou identidade gerenciada. Depois de aparecer, selecione o cofre e escolha **selecionar** na parte inferior do painel.

    ![Selecionar entidade de segurança](./media/encryption-at-rest-with-cmk/select-principal.png)

1. Depois de terminar, selecione **Adicionar** para adicionar a nova política de acesso.

1. Selecione **salvar** para salvar as alterações feitas na política de acesso do Azure Key Vault.

### <a name="enable-soft-delete-and-purge-protection-on-the-azure-key-vault"></a>Habilitar a proteção de exclusão e limpeza reversível no Azure Key Vault

Você precisa **habilitar a exclusão reversível e limpar a proteção** no Azure Key Vault que armazena sua chave de criptografia. Você pode fazer isso na interface do usuário do Azure Key Vault, conforme mostrado abaixo. (Como alternativa, essas propriedades podem ser definidas ao criar o Key Vault). Leia mais sobre essas propriedades de Key Vault [aqui](../key-vault/general/soft-delete-overview.md).

![Habilitar a exclusão reversível e a proteção contra limpeza](./media/encryption-at-rest-with-cmk/soft-delete-purge-protection.png)

Você também pode habilitar a exclusão reversível e limpar a proteção por meio do PowerShell usando as etapas abaixo:

1. Entre em sua Conta do Azure.

    ```azurepowershell
    Login-AzAccount
    ```

1. Selecione a assinatura que contém o cofre.

    ```azurepowershell
    Set-AzContext -SubscriptionId SubscriptionId
    ```

1. Habilitar exclusão reversível

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

1. Habilitar a proteção de limpeza

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

### <a name="assign-encryption-key-to-the-rs-vault"></a>Atribuir chave de criptografia ao cofre RS

>[!NOTE]
> Antes de continuar, verifique o seguinte:
>
> - Todas as etapas mencionadas acima foram concluídas com êxito:
>   - A identidade gerenciada do cofre dos serviços de recuperação foi habilitada e recebeu as permissões necessárias
>   - A Azure Key Vault tem exclusão reversível e limpeza-proteção habilitada
> - O cofre dos serviços de recuperação para o qual você deseja habilitar **a criptografia CMK não tem** nenhum item protegido ou registrado nele

Depois que as versões acima tiverem sido verificadas, Continue selecionando a chave de criptografia para seu cofre.

#### <a name="to-assign-the-key-in-the-portal"></a>Para atribuir a chave no portal

1. Vá para o cofre dos serviços de recuperação-> **Propriedades**

    ![Configurações de criptografia](./media/encryption-at-rest-with-cmk/encryption-settings.png)

1. Selecione **Atualizar** em **configurações de criptografia**.

1. No painel configurações de criptografia, selecione **usar sua própria chave** e continue para especificar a chave usando uma das seguintes maneiras. **Verifique se a chave que você deseja usar é uma chave RSA 2048, que está em um estado habilitado.**

    1. Insira o **URI de chave** com o qual você deseja criptografar os dados neste cofre dos serviços de recuperação. Você também precisa especificar a assinatura na qual o Azure Key Vault (que contém essa chave) está presente. Esse URI de chave pode ser obtido da chave correspondente no seu Azure Key Vault. Verifique se o URI da chave foi copiado corretamente. É recomendável que você use o botão **copiar para área de transferência** fornecido com o identificador de chave.

        >[!NOTE]
        >Ao especificar a chave de criptografia usando o URI de chave, a chave não será girada automaticamente. Portanto, as principais atualizações precisarão ser feitas manualmente, especificando a nova chave quando necessário.

        ![Inserir URI de chave](./media/encryption-at-rest-with-cmk/key-uri.png)

    1. Procure e selecione a chave na Key Vault no painel seletor de chave.

        >[!NOTE]
        >Ao especificar a chave de criptografia usando o painel seletor de chave, a chave será girada automaticamente sempre que uma nova versão para a chave estiver habilitada.

        ![Selecionar chave do Key Vault](./media/encryption-at-rest-with-cmk/key-vault.png)

1. Clique em **Salvar**.

1. **Acompanhamento do progresso e do status da atualização da chave de criptografia**: você pode acompanhar o progresso e o status da atribuição de chave de criptografia usando a exibição **trabalhos de backup** na barra de navegação à esquerda. O status deve ser alterado em breve para **concluído**. Agora, seu cofre criptografará todos os dados com a chave especificada como KEK.

    ![Status concluído](./media/encryption-at-rest-with-cmk/status-succeeded.png)

    As atualizações da chave de criptografia também são registradas no log de atividades do cofre.

    ![Log de atividades](./media/encryption-at-rest-with-cmk/activity-log.png)

#### <a name="to-assign-the-key-with-powershell"></a>Para atribuir a chave com o PowerShell

Use o comando [set-AzRecoveryServicesVaultProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultproperty) para habilitar a criptografia usando chaves gerenciadas pelo cliente e para atribuir ou atualizar a chave de criptografia a ser usada.

Exemplo:

```azurepowershell
$keyVault = Get-AzKeyVault -VaultName "testkeyvault" -ResourceGroupName "testrg" 
$key = Get-AzKeyVaultKey -VaultName $keyVault -Name "testkey" 
Set-AzRecoveryServicesVaultProperty -EncryptionKeyId $key.ID -KeyVaultSubscriptionId "xxxx-yyyy-zzzz"  -VaultId $vault.ID


$enc=Get-AzRecoveryServicesVaultProperty -VaultId $vault.ID
$enc.encryptionProperties | fl
```

Saída:

```output
EncryptionAtRestType          : CustomerManaged
KeyUri                        : testkey
SubscriptionId                : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx 
LastUpdateStatus              : Succeeded
InfrastructureEncryptionState : Disabled
```

>[!NOTE]
> Esse processo permanece o mesmo quando você deseja atualizar ou alterar a chave de criptografia. Se você quiser atualizar e usar uma chave de outra Key Vault (diferente da que está sendo usada no momento), verifique se:
>
> - O cofre de chaves está localizado na mesma região que o cofre dos serviços de recuperação
>
> - O cofre de chaves tem a proteção de exclusão e limpeza reversível habilitada
>
> - O cofre dos serviços de recuperação tem as permissões necessárias para acessar o cofre de chaves.

## <a name="backing-up-to-a-vault-encrypted-with-customer-managed-keys"></a>Fazendo backup em um cofre criptografado com chaves gerenciadas pelo cliente

Antes de prosseguir com a configuração de proteção, é altamente recomendável garantir que a seguinte lista de verificação esteja em conformidade. Isso é importante, uma vez que um item foi configurado para ser submetido a backup (ou tentou ser configurado) para um cofre não CMK criptografado, a criptografia usando chaves gerenciadas pelo cliente não pode ser habilitada nela e continuará a usar chaves gerenciadas por plataforma.

>[!IMPORTANT]
> Antes de prosseguir com a configuração de proteção, você deve ter concluído **com êxito** as seguintes etapas:
>
>1. Criou seu cofre de backup
>1. Habilitou a identidade gerenciada atribuída pelo sistema do cofre de backup
>1. Permissões atribuídas ao cofre de backup para acessar as chaves de criptografia do seu Key Vault
>1. Habilitada a exclusão reversível e a proteção de limpeza para seu Key Vault
>1. Atribuída uma chave de criptografia válida para seu cofre de backup
>
>Se todas as etapas acima tiverem sido confirmadas, continue com a configuração de backup.

O processo para configurar e executar backups em um cofre de serviços de recuperação criptografado com chaves gerenciadas pelo cliente é o mesmo de um cofre que usa chaves gerenciadas por plataforma, **sem alterações na experiência**. Isso se aplica ao [backup de VMs do Azure](./quick-backup-vm-portal.md) , bem como ao backup de cargas de trabalho em execução dentro de uma VM (por exemplo, [SAP Hana](./tutorial-backup-sap-hana-db.md), [SQL Server](./tutorial-sql-backup.md) bancos de dados).

## <a name="restoring-data-from-backup"></a>Restaurando dados do backup

### <a name="vm-backup"></a>Backup da VM

Os dados armazenados no cofre dos serviços de recuperação podem ser restaurados de acordo com as etapas descritas [aqui](./backup-azure-arm-restore-vms.md). Ao restaurar de um cofre dos serviços de recuperação criptografado usando chaves gerenciadas pelo cliente, você pode optar por criptografar os dados restaurados com um conjunto de criptografia de disco (DES).

#### <a name="restoring-vm--disk"></a>Restaurando a VM/disco

1. Ao recuperar o disco/VM de um ponto de recuperação de "instantâneo", os dados restaurados serão criptografados com o DES usado para criptografar os discos da VM de origem.

1. Ao restaurar o disco/VM de um ponto de recuperação com o tipo de recuperação como "cofre", você pode optar por ter os dados restaurados criptografados usando um DES, especificado no momento da restauração. Como alternativa, você pode optar por continuar com a restauração dos dados sem especificar um DES; nesse caso, ele será criptografado usando chaves gerenciadas pela Microsoft.

Você pode criptografar o disco/VM restaurado após a conclusão da restauração, independentemente da seleção feita durante a inicialização da restauração.

![Pontos de restauração](./media/encryption-at-rest-with-cmk/restore-points.png)

#### <a name="select-a-disk-encryption-set-while-restoring-from-vault-recovery-point"></a>Selecione um conjunto de criptografia de disco durante a restauração do ponto de recuperação do cofre

**No portal**:

O conjunto de criptografia de disco é especificado em configurações de criptografia no painel restaurar, conforme mostrado abaixo:

1. Em **criptografar disco (s) usando sua chave**, selecione **Sim**.

1. No menu suspenso, selecione o DES que você deseja usar para os discos restaurados. **Verifique se você tem acesso ao DES.**

>[!NOTE]
>A capacidade de escolher um DES durante a restauração não estará disponível se você estiver restaurando uma VM que usa Azure Disk Encryption.

![Criptografar disco usando sua chave](./media/encryption-at-rest-with-cmk/encrypt-disk-using-your-key.png)

**Com o PowerShell**:

Use o comando [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) com o parâmetro [ `-DiskEncryptionSetId <string>` ] para [especificar o des](/powershell/module/az.compute/get-azdiskencryptionset) a ser usado para criptografar o disco restaurado. Para obter mais informações sobre como restaurar discos do backup de VM, consulte [Este artigo](./backup-azure-vms-automation.md#restore-an-azure-vm).

Exemplo:

```azurepowershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $vault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $vault.ID
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $vault.ID
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -DiskEncryptionSetId “testdes1” -VaultId $vault.ID
```

#### <a name="restoring-files"></a>Restaurando arquivos

Ao executar uma restauração de arquivo, os dados restaurados serão criptografados com a chave usada para criptografar o local de destino.

### <a name="restoring-sap-hanasql-databases-in-azure-vms"></a>Restaurando bancos de dados SAP HANA/SQL em VMs do Azure

Ao fazer a restauração de um banco de dados SAP HANA/SQL com backup em execução em uma VM do Azure, eles serão criptografados usando a chave de criptografia usada no local de armazenamento de destino. Pode ser uma chave gerenciada pelo cliente ou uma chave gerenciada por plataforma usada para criptografar os discos da VM.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="can-i-encrypt-an-existing-backup-vault-with-customer-managed-keys"></a>Posso criptografar um cofre de backup existente com chaves gerenciadas pelo cliente?

Não, a criptografia CMK pode ser habilitada somente para novos cofres. Portanto, o cofre nunca deve ter tido itens protegidos. Na verdade, nenhuma tentativa de proteger itens para o cofre deve ser feita antes de habilitar a criptografia usando chaves gerenciadas pelo cliente.

### <a name="i-tried-to-protect-an-item-to-my-vault-but-it-failed-and-the-vault-still-doesnt-contain-any-items-protected-to-it-can-i-enable-cmk-encryption-for-this-vault"></a>Tentei proteger um item ao meu cofre, mas ele falhou e o cofre ainda não contém itens protegidos. Posso habilitar a criptografia CMK para este cofre?

Não, o cofre não deve ter nenhuma tentativa de proteger nenhum item para ele no passado.

### <a name="i-have-a-vault-thats-using-cmk-encryption-can-i-later-revert-to-encryption-using-platform-managed-keys-even-if-i-have-backup-items-protected-to-the-vault"></a>Tenho um cofre que está usando a criptografia CMK. Posteriormente, posso reverter para a criptografia usando chaves gerenciadas por plataforma mesmo se eu tiver itens de backup protegidos ao cofre?

Não, depois de habilitar a criptografia CMK, ela não pode ser revertida para usar chaves gerenciadas por plataforma. Você pode alterar as chaves usadas de acordo com seus requisitos.

### <a name="does-cmk-encryption-for-azure-backup-also-apply-to-azure-site-recovery"></a>A criptografia CMK para o backup do Azure também se aplica a Azure Site Recovery?

Não, este artigo aborda apenas a criptografia de dados de backup. Por Azure Site Recovery, você precisa definir a propriedade separadamente como disponível no serviço.

### <a name="i-missed-one-of-the-steps-in-this-article-and-went-on-to-protect-my-data-source-can-i-still-use-cmk-encryption"></a>Eu perdi uma das etapas neste artigo e passei para proteger minha fonte de dados. Ainda posso usar a criptografia CMK?

Não seguir as etapas no artigo e continuar a proteger os itens pode levar o cofre a não conseguir usar a criptografia usando chaves gerenciadas pelo cliente. Portanto, é recomendável que você consulte [esta lista de verificação](#backing-up-to-a-vault-encrypted-with-customer-managed-keys) antes de continuar a proteger os itens.

### <a name="does-using-cmk-encryption-add-to-the-cost-of-my-backups"></a>O uso de CMK-Encryption adiciona ao custo de meus backups?

O uso da criptografia CMK para backup não incorre em custos adicionais. No entanto, você pode continuar a incorrer em custos para usar seu Azure Key Vault onde a chave está armazenada.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral dos recursos de segurança no Backup do Azure](security-overview.md)

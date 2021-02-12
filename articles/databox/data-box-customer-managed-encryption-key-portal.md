---
title: Use o portal do Azure para gerenciar chaves gerenciadas pelo cliente para Azure Data Box
description: Saiba como usar portal do Azure para criar e gerenciar chaves gerenciadas pelo cliente com Azure Key Vault para uma Azure Data Box. Chaves gerenciadas pelo cliente permitem criar, girar, desabilitar e revogar controles de acesso.
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 11/19/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: e6b588ddea5bf4b4c92e89d9cebb37b09b9a86af
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791537"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-azure-data-box"></a>Usar chaves gerenciadas pelo cliente no Azure Key Vault para Azure Data Box

Azure Data Box protege a chave de desbloqueio do dispositivo (também conhecida como a senha do dispositivo), que é usada para bloquear um dispositivo, por meio de uma chave de criptografia. Por padrão, essa chave de criptografia é uma chave gerenciada da Microsoft. Para obter controle adicional, você pode usar uma chave gerenciada pelo cliente.

O uso de uma chave gerenciada pelo cliente não afeta a forma como os dados no dispositivo são criptografados. Ele afeta apenas como a chave de desbloqueio do dispositivo é criptografada.

Para manter esse nível de controle ao longo do processo de pedido, use uma chave gerenciada pelo cliente ao criar seu pedido. Para obter mais informações, consulte [tutorial: Order Azure data Box](data-box-deploy-ordered.md).

Este artigo mostra como habilitar uma chave gerenciada pelo cliente para sua ordem de Data Box existente no [portal do Azure](https://portal.azure.com/). Você descobrirá como alterar o cofre de chaves, a chave, a versão ou a identidade de sua chave atual gerenciada pelo cliente ou voltar a usar uma chave gerenciada da Microsoft.

Este artigo se aplica a dispositivos Azure Data Box e Azure Data Box Heavy.

## <a name="requirements"></a>Requisitos

A chave gerenciada pelo cliente para uma ordem de Data Box deve atender aos seguintes requisitos:

- A chave deve ser criada e armazenada em um Azure Key Vault que tenha **exclusão reversível** e **não a limpeza** seja habilitada. Para obter mais informações, veja [O que é o Cofre da Chave do Azure?](../key-vault/general/overview.md). Você pode criar um cofre de chaves e uma chave ao criar ou atualizar seu pedido.

- A chave deve ser uma chave RSA de tamanho de 2048 ou maior.

## <a name="enable-key"></a>Habilitar chave

Para habilitar uma chave gerenciada pelo cliente para sua ordem de Data Box existente no portal do Azure, siga estas etapas:

1. Vá para a tela de **visão geral** de sua ordem de data box.

    ![Tela de visão geral de um Data Box ordem-1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-1.png)

2. Vá para **configurações > criptografia** e selecione **chave gerenciada pelo cliente**. Em seguida, selecione **selecionar uma chave e um cofre de chaves**.

    ![Selecione a opção de criptografia de chave gerenciada pelo cliente](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3.png)

   Na tela **selecionar chave de Azure Key Vault** , sua assinatura é preenchida automaticamente.

 3. Para o **cofre de chaves**, você pode selecionar um cofre de chaves existente na lista suspensa ou selecionar **criar novo** e criar um novo cofre de chaves.

     ![Opções do Key Vault ao selecionar uma chave gerenciada pelo cliente](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3-a.png)

     Para criar um novo cofre de chaves, insira a assinatura, o grupo de recursos, o nome do cofre de chaves e outras informações na tela **criar novo cofre de chaves** . Em **Opções de recuperação**, verifique se a **exclusão reversível** e a **proteção de limpeza** estão habilitadas. Em seguida, selecione **Examinar + Criar**.

      ![Revisar e criar Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-4.png)

      Examine as informações do seu cofre de chaves e selecione **Criar**. Aguarde alguns minutos até que a criação do cofre de chaves seja concluída.

       ![Criar Azure Key Vault com suas configurações](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-5.png)

4. Na tela **selecionar chave de Azure Key Vault** , você pode selecionar uma chave existente no cofre de chaves ou criar uma nova.

    ![Selecionar chave de Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6.png)

   Se você desejar criar uma chave, selecione **Criar**. Você precisa usar uma chave RSA. O tamanho pode ser 2048 ou maior.

    ![Criar nova chave no Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6-a.png)

    Insira um nome para a nova chave, aceite os outros padrões e selecione **Criar**. Você será notificado de que uma chave foi criada no cofre de chaves.

    ![Nomear nova chave](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-7.png)

5. Para a **versão**, você pode selecionar uma versão de chave existente na lista suspensa.

    ![Selecionar versão para nova chave](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8.png)

    Se você quiser gerar uma nova versão de chave, selecione **criar nova**.

    ![Abrir uma caixa de diálogo para criar uma versão de chave](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-a.png)

    Escolha configurações para a nova versão de chave e selecione **Criar**.

    ![Criar uma nova versão da chave](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-b.png)

6. Quando você tiver selecionado um cofre de chaves, uma chave e uma versão de chave, escolha **selecionar**.

    ![Uma chave em um Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-c.png)

    As configurações de **tipo de criptografia** mostram o cofre de chaves e a chave que você escolheu.

    ![Chave e cofre de chaves para uma chave gerenciada pelo cliente](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-9.png)

7. Selecione o tipo de identidade a ser usado para gerenciar a chave gerenciada pelo cliente para este recurso. Você pode usar a identidade **atribuída pelo sistema** que foi gerada durante a criação do pedido ou escolher uma identidade atribuída pelo usuário.

    Uma identidade atribuída pelo usuário é um recurso independente que você pode usar para gerenciar o acesso aos recursos. Para obter mais informações, confira [Tipos de identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md).

    ![Selecione o tipo de identidade](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-13.png)

    Para atribuir uma identidade de usuário, selecione **usuário atribuído**. Em seguida, selecione **selecionar uma identidade de usuário** e selecione a identidade gerenciada que você deseja usar.

    ![Selecione uma identidade a ser usada](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-14.png)

    Você não pode criar uma nova identidade de usuário aqui. Para saber como criar um, consulte [criar, listar, excluir ou atribuir uma função a uma identidade gerenciada atribuída pelo usuário usando o portal do Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

    A identidade de usuário selecionada é mostrada nas configurações de **tipo de criptografia** .

    ![Uma identidade de usuário selecionada mostrada nas configurações Tipo de criptografia](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-15.png)

 9. Selecione **salvar** para salvar as configurações de **tipo de criptografia** atualizadas.

     ![Salve sua chave gerenciada pelo cliente](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-10.png)

    A URL da chave é exibida em **tipo de criptografia**.

    ![URL de chave gerenciada pelo cliente](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-11.png)<!--Probably need new screen from recent order. Can you provide one? I can't create an order using CMK with the subscription I'm using.-->

## <a name="change-key"></a>Alterar chave

Para alterar o cofre de chaves, a chave e/ou a versão de chave para a chave gerenciada pelo cliente que você está usando no momento, siga estas etapas:

1. Na tela de **visão geral** de sua ordem de data Box, acesse **configurações**  >  **criptografia** e clique em **alterar chave**.

    ![Tela de visão geral de um pedido de Data Box com a chave gerenciada pelo cliente-1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-16.png)

2. Escolha **selecionar um cofre de chaves e chave diferentes**.

    ![Tela de visão geral de um pedido de Data Box, selecione uma chave diferente e a opção Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-16-a.png)

3. A tela **selecionar chave da chave do cofre de chaves** mostra a assinatura, mas nenhum cofre de chaves, chave ou versão de chave. Você pode fazer qualquer uma das seguintes alterações:

   - Selecione uma chave diferente do mesmo cofre de chaves. Você precisará selecionar o cofre de chaves antes de selecionar a chave e a versão.

   - Selecione um cofre de chaves diferente e atribua uma nova chave.

   - Altere a versão da chave atual.
   
    Ao concluir as alterações, escolha **selecionar**.

    ![Escolha a opção de criptografia-2](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17.png)

4. Clique em **Salvar**.

    ![Salvar configurações de criptografia atualizadas-1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17-a.png)

## <a name="change-identity"></a>Alterar identidade

Para alterar a identidade usada para gerenciar o acesso à chave gerenciada pelo cliente para essa ordem, siga estas etapas:

1. Na tela de **visão geral** de sua ordem de data Box concluída, vá para **configurações**  >  **criptografia**.

2. Faça uma das seguintes alterações:

     - Para alterar para uma identidade de usuário diferente, clique em **selecionar uma identidade de usuário diferente**. Em seguida, selecione uma identidade diferente no painel no lado direito da tela e escolha **selecionar**.

       ![Opção para alterar a identidade atribuída pelo usuário para uma chave gerenciada pelo cliente](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-18.png)

   - Para alternar para a identidade atribuída pelo sistema gerada durante a criação do pedido, selecione **sistema atribuído** por **Selecionar tipo de identidade**.

     ![Opção para alterar para um atribuído pelo sistema para uma chave gerenciada pelo cliente](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-19.png)

3. Clique em **Salvar**.

    ![Salvar configurações de criptografia atualizadas-2](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17-a.png)

## <a name="use-microsoft-managed-key"></a>Usar a chave gerenciada da Microsoft

Para alterar o uso de uma chave gerenciada pelo cliente para a chave gerenciada da Microsoft para seu pedido, siga estas etapas:

1. Na tela de **visão geral** de sua ordem de data Box concluída, vá para **configurações**  >  **criptografia**.

2. Em **Selecionar tipo**, selecione **chave gerenciada da Microsoft**.

    ![Tela de visão geral de um Data Box ordem-5](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-20.png)

3. Clique em **Salvar**.

    ![Salvar configurações de criptografia atualizadas para uma chave gerenciada da Microsoft](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-21.png)

## <a name="troubleshoot-errors"></a>Solucionar problemas de erros

Se você receber erros relacionados à chave gerenciada pelo cliente, use a tabela a seguir para solucionar problemas.

| Código de erro| Detalhes do erro| Recuperado?|
|-------------|--------------|---------|
| SsemUserErrorEncryptionKeyDisabled| Não foi possível obter a chave de acesso porque a chave gerenciada pelo cliente está desabilitada.| Sim, habilitando a versão da chave.|
| SsemUserErrorEncryptionKeyExpired| Não foi possível buscar a chave de acesso, pois a chave gerenciada pelo cliente expirou.| Sim, habilitando a versão da chave.|
| SsemUserErrorKeyDetailsNotFound| Não foi possível buscar a chave de acesso porque a chave gerenciada pelo cliente não pôde ser encontrada.| Se você excluiu o cofre de chaves, não é possível recuperar a chave gerenciada pelo cliente.  Se você migrou o cofre de chaves para um locatário diferente, consulte [alterar uma ID de locatário do cofre de chaves após a movimentação de uma assinatura](../key-vault/general/move-subscription.md). Se você excluiu o cofre de chaves:<ol><li>Sim, se estiver na duração da proteção de limpeza, usando as etapas em [recuperar um cofre de chaves](../key-vault/general/key-vault-recovery.md?tabs=azure-powershell#key-vault-powershell).</li><li>Não, se estiver além da duração da proteção de limpeza.</li></ol><br>Caso contrário, se o cofre de chaves sofreu uma migração de locatário, sim, ele poderá ser recuperado usando uma das etapas a seguir: <ol><li>Reverter o cofre de chaves de volta para o locatário antigo.</li><li>Defina `Identity = None` e, em seguida, defina o valor de volta para `Identity = SystemAssigned` . Isso exclui e recria a identidade depois que a nova identidade é criada. Habilite `Get` `Wrap` `Unwrap` as permissões, e para a nova identidade na política de acesso do Key Vault.</li></ol> |
| SsemUserErrorKeyVaultBadRequestException | Uma chave gerenciada pelo cliente foi aplicada, mas o acesso à chave não foi concedido ou foi revogado ou não é possível acessar o cofre de chaves devido ao firewall estar habilitado. | Adicione a identidade selecionada ao cofre de chaves para habilitar o acesso à chave gerenciada pelo cliente. Se o Key Vault tiver o firewall habilitado, alterne para uma identidade atribuída pelo sistema e adicione uma chave gerenciada pelo cliente. Para obter mais informações, consulte como [habilitar a chave](#enable-key). |
| SsemUserErrorKeyVaultDetailsNotFound| Não foi possível buscar a chave de acesso porque o cofre de chaves associado à chave gerenciada pelo cliente não foi encontrado. | Se você excluiu o cofre de chaves, não é possível recuperar a chave gerenciada pelo cliente.  Se você migrou o cofre de chaves para um locatário diferente, consulte [alterar uma ID de locatário do cofre de chaves após a movimentação de uma assinatura](../key-vault/general/move-subscription.md). Se você excluiu o cofre de chaves:<ol><li>Sim, se estiver na duração da proteção de limpeza, usando as etapas em [recuperar um cofre de chaves](../key-vault/general/key-vault-recovery.md?tabs=azure-powershell#key-vault-powershell).</li><li>Não, se estiver além da duração da proteção de limpeza.</li></ol><br>Caso contrário, se o cofre de chaves sofreu uma migração de locatário, sim, ele poderá ser recuperado usando uma das etapas a seguir: <ol><li>Reverter o cofre de chaves de volta para o locatário antigo.</li><li>Defina `Identity = None` e, em seguida, defina o valor de volta para `Identity = SystemAssigned` . Isso exclui e recria a identidade depois que a nova identidade é criada. Habilite `Get` `Wrap` `Unwrap` as permissões, e para a nova identidade na política de acesso do Key Vault.</li></ol> |
| SsemUserErrorSystemAssignedIdentityAbsent  | Não foi possível buscar a chave de acesso porque a chave gerenciada pelo cliente não pôde ser encontrada.| Sim, verifique se: <ol><li>O cofre de chaves ainda tem o MSI na política de acesso.</li><li>A identidade é do tipo atribuído pelo sistema.</li><li>Habilite as permissões obter, encapsular e desencapsular para a identidade na política de acesso do cofre de chaves.</li></ol>|
| SsemUserErrorUserAssignedLimitReached | Falha ao Adicionar nova identidade atribuída ao usuário, pois você atingiu o limite do número total de identidades atribuídas ao usuário que podem ser adicionadas. | Repita a operação com menos identidades de usuário ou remova algumas identidades atribuídas pelo usuário do recurso antes de tentar novamente. |
| SsemUserErrorCrossTenantIdentityAccessForbidden | Falha na operação de acesso de identidade gerenciada. <br> Observação: isso é para o cenário quando a assinatura é movida para um locatário diferente. O cliente precisa mover manualmente a identidade para o novo locatário. PFA mail para obter mais detalhes. | Mova a identidade selecionada para o novo locatário no qual a assinatura está presente. Para obter mais informações, consulte como [habilitar a chave](#enable-key). |
| SsemUserErrorKekUserIdentityNotFound | Uma chave gerenciada pelo cliente foi aplicada, mas a identidade atribuída ao usuário que tem acesso à chave não foi encontrada no Active Directory. <br> Observação: esse é o caso quando a identidade do usuário é excluída do Azure.| Tente adicionar uma identidade diferente atribuída ao usuário selecionada para o cofre de chaves para habilitar o acesso à chave gerenciada pelo cliente. Para obter mais informações, consulte como [habilitar a chave](#enable-key). |
| SsemUserErrorUserAssignedIdentityAbsent | Não foi possível buscar a chave de acesso porque a chave gerenciada pelo cliente não pôde ser encontrada. | Não foi possível acessar a chave gerenciada pelo cliente. A identidade atribuída pelo usuário (UAI) associada à chave foi excluída ou o tipo UAI foi alterado. |
| SsemUserErrorCrossTenantIdentityAccessForbidden | Falha na operação de acesso de identidade gerenciada. <br> Observação: isso é para o cenário quando a assinatura é movida para um locatário diferente. O cliente precisa mover manualmente a identidade para o novo locatário. PFA mail para obter mais detalhes. | Tente adicionar uma identidade diferente atribuída ao usuário selecionada para o cofre de chaves para habilitar o acesso à chave gerenciada pelo cliente. Para obter mais informações, consulte como [habilitar a chave](#enable-key).|
| SsemUserErrorKeyVaultBadRequestException | Uma chave gerenciada pelo cliente foi aplicada, mas o acesso à chave não foi concedido ou foi revogado ou não é possível acessar o cofre de chaves devido ao firewall estar habilitado. | Adicione a identidade selecionada ao cofre de chaves para habilitar o acesso à chave gerenciada pelo cliente. Se o Key Vault tiver o firewall habilitado, alterne para uma identidade atribuída pelo sistema e adicione uma chave gerenciada pelo cliente. Para obter mais informações, consulte como [habilitar a chave](#enable-key). |
| Erro genérico  | Não foi possível buscar a chave de acesso.| Esse é um erro genérico. Entre em contato com Suporte da Microsoft para solucionar o erro e determinar as próximas etapas.|

## <a name="next-steps"></a>Próximas etapas

- [O que é o Cofre da Chave do Azure?](../key-vault/general/overview.md)
- [Início Rápido: definir e recuperar um segredo do Azure Key Vault usando o portal do Azure](../key-vault/secrets/quick-create-portal.md)

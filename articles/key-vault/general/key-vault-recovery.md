---
title: Visão geral da recuperação de Azure Key Vault | Microsoft Docs
description: Key Vault recursos de recuperação são projetados para impedir a exclusão acidental ou mal-intencionada do cofre de chaves e segredos, chaves e certificados armazenados no cofre de chaves.
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.author: mbaldwin
author: msmbaldwin
manager: rkarlin
ms.date: 09/30/2020
ms.openlocfilehash: a8e8e791f0dbe18322ad43364ae4ffd09b430caf
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790377"
---
# <a name="azure-key-vault-recovery-management-with-soft-delete-and-purge-protection"></a>Gerenciamento de recuperação do Azure Key Vault com exclusão reversível e proteção de limpeza

Este artigo aborda dois recursos de recuperação do Azure Key Vault, exclusão reversível e proteção de limpeza. Este documento fornece uma visão geral desses recursos e mostra como gerenciá-los por meio do portal do Azure, CLI do Azure e Azure PowerShell.

Para obter mais informações sobre Key Vault, consulte
- [Visão geral de Key Vault](overview.md)
- [Visão geral de chaves, segredos e certificados de Azure Key Vault](about-keys-secrets-certificates.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/dotnet)
* [Módulo do PowerShell](/powershell/azure/install-az-ps).
* [CLI do Azure](/cli/azure/install-azure-cli)
* Um Key Vault – crie um usando o [portal do Azure](../general/quick-create-portal.md), a [CLI do Azure](../general/quick-create-cli.md) ou o [Azure PowerShell](../general/quick-create-powershell.md)
* O usuário precisará das seguintes permissões (no nível da assinatura) para executar operações em cofres excluídos de forma reversível:

  | Permissão | Descrição |
  |---|---|
  |Microsoft.KeyVault/locations/deletedVaults/read|Exibir as propriedades de um cofre de chaves com exclusão reversível|
  |Microsoft.KeyVault/locations/deletedVaults/purge/action|Limpar um cofre de chaves com exclusão reversível|


## <a name="what-are-soft-delete-and-purge-protection"></a>O que é exclusão reversível e limpar a proteção

A [exclusão reversível](soft-delete-overview.md) e a proteção de limpeza são dois recursos diferentes de recuperação do cofre de chaves.

> [!IMPORTANT]
> A ativação da exclusão reversível é essencial para garantir que seus cofres de chaves e credenciais sejam protegidos contra exclusão acidental. No entanto, ativar a exclusão reversível é considerado uma alteração significativa porque pode exigir que você altere a lógica do aplicativo ou forneça permissões adicionais para suas entidades de serviço. Antes de ativar a exclusão reversível usando as instruções abaixo, verifique se seu aplicativo é compatível com a alteração usando este documento [ **aqui**.](soft-delete-change.md)

A **exclusão reversível** foi projetada para impedir a exclusão acidental do cofre de chaves e chaves, segredos e certificados armazenados no Key Vault. Pense na exclusão reversível como uma lixeira. Quando você excluir um cofre de chaves ou um objeto de cofre de chaves, ele permanecerá recuperável para um período de retenção configurável pelo usuário ou um padrão de 90 dias. Os cofres de chaves no estado de exclusão reversível também podem ser **limpos** , o que significa que eles são excluídos permanentemente. Isso permite recriar cofres de chaves e objetos do cofre de chaves com o mesmo nome. A recuperação e a exclusão de cofres de chaves e objetos exigem permissões elevadas de política de acesso. **Depois que a exclusão reversível tiver sido habilitada, ela não poderá ser desabilitada.**

É importante observar que os **nomes do cofre de chaves são globalmente exclusivos**, portanto, você não poderá criar um cofre de chaves com o mesmo nome que um cofre de chaves no estado de exclusão reversível. Da mesma forma, os nomes de chaves, segredos e certificados são exclusivos em um cofre de chaves. Você não poderá criar um segredo, uma chave ou um certificado com o mesmo nome que outro no estado de exclusão reversível.

A **proteção de limpeza** é projetada para impedir a exclusão de seu cofre de chaves, chaves, segredos e certificados por um insider mal-intencionado. Pense nisso como uma lixeira com um bloqueio baseado em tempo. Você pode recuperar itens em qualquer ponto durante o período de retenção configurável. **Você não poderá excluir ou limpar permanentemente um cofre de chaves até que o período de retenção tenha decorrido.** Depois que o período de retenção expirar, o cofre de chaves ou o objeto do cofre de chaves será limpo automaticamente.

> [!NOTE]
> A proteção de limpeza é projetada para que nenhuma função de administrador ou permissão possa substituir, desabilitar ou evitar a proteção de limpeza. **Quando a proteção de limpeza estiver habilitada, ela não poderá ser desabilitada nem substituída por ninguém, incluindo a Microsoft.** Isso significa que você deve recuperar um cofre de chaves excluído ou aguardar até que o período de retenção decorra antes de reutilizar o nome do cofre de chaves.

Para obter mais informações sobre a exclusão reversível, consulte [visão geral da exclusão de Azure Key Vault reversível](soft-delete-overview.md)

# <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

## <a name="verify-if-soft-delete-is-enabled-on-a-key-vault-and-enable-soft-delete"></a>Verifique se a exclusão reversível está habilitada em um cofre de chaves e habilite a exclusão reversível

1. Faça logon no Portal do Azure.
1. Selecione seu cofre de chaves.
1. Clique na folha "Propriedades".
1. Verifique se o botão de opção ao lado de exclusão reversível está definido como "Habilitar recuperação".
1. Se a exclusão reversível não estiver habilitada no cofre de chaves, clique no botão de opção para habilitar a exclusão reversível e clique em "salvar".

:::image type="content" source="../media/key-vault-recovery-1.png" alt-text="Em Propriedades, a exclusão reversível é realçada, assim como o valor para habilitá-la.":::

## <a name="grant-access-to-a-service-principal-to-purge-and-recover-deleted-secrets"></a>Conceder acesso a uma entidade de serviço para limpar e recuperar segredos excluídos

1. Faça logon no Portal do Azure.
1. Selecione seu cofre de chaves.
1. Clique na folha "política de acesso".
1. Na tabela, localize a linha da entidade de segurança à qual você deseja conceder acesso (ou adicione uma nova entidade de segurança).
1. Clique na lista suspensa para chaves, certificados e segredos.
1. Role até a parte inferior da lista suspensa e clique em "recuperar" e "limpar"
1. As entidades de segurança também precisarão de funcionalidade Get e List para executar a maioria das operações.

:::image type="content" source="../media/key-vault-recovery-2.png" alt-text="No painel de navegação à esquerda, as políticas de acesso são realçadas. Em políticas de acesso, a lista suspensa de posições secretas é mostrada e quatro itens são selecionados: obter, listar, recuperar e limpar.":::

## <a name="list-recover-or-purge-a-soft-deleted-key-vault"></a>Listar, recuperar ou limpar um cofre de chaves com exclusão reversível

1. Faça logon no Portal do Azure.
1. Clique na barra de pesquisa na parte superior da página.
1. Em "serviços recentes", clique em "Key Vault". Não clique em um cofre de chaves individual.
1. Na parte superior da tela, clique na opção "gerenciar cofres excluídos"
1. Um painel de contexto será aberto no lado direito da tela.
1. Selecione sua assinatura.
1. Se o cofre de chaves foi excluído de forma reversível, ele aparecerá no painel de contexto à direita.
1. Se houver muitos cofres, você poderá clicar em "carregar mais" na parte inferior do painel de contexto ou usar a CLI ou o PowerShell para obter os resultados.
1. Quando encontrar o cofre que deseja recuperar ou limpar, marque a caixa de seleção ao lado dele.
1. Selecione a opção recuperar na parte inferior do painel de contexto se desejar recuperar o cofre de chaves.
1. Selecione a opção limpar se desejar excluir permanentemente o cofre de chaves.

:::image type="content" source="../media/key-vault-recovery-3.png" alt-text="Em cofres de chaves, a opção gerenciar cofres excluídos é realçada.":::

:::image type="content" source="../media/key-vault-recovery-4.png" alt-text="Em gerenciar cofres de chaves excluídos, o único cofre de chaves listado é realçado e selecionado e o botão recuperar é realçado.":::

## <a name="list-recover-or-purge-soft-deleted-secrets-keys-and-certificates"></a>Listar, recuperar ou limpar segredos, chaves e certificados com exclusão reversível

1. Faça logon no Portal do Azure.
1. Selecione seu cofre de chaves.
1. Selecione a folha correspondente ao tipo de segredo que você deseja gerenciar (chaves, segredos ou certificados).
1. Na parte superior da tela, clique em "gerenciar excluídos (chaves, segredos ou certificados)
1. Um painel de contexto será exibido no lado direito da tela.
1. Se seu segredo, chave ou certificado não aparecer na lista, ele não estará no estado de exclusão reversível.
1. Selecione o segredo, a chave ou o certificado que você deseja gerenciar.
1. Selecione a opção para recuperar ou limpar na parte inferior do painel de contexto.

:::image type="content" source="../media/key-vault-recovery-5.png" alt-text="Em chaves, a opção gerenciar chaves excluídas é realçada.":::

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

## <a name="key-vault-cli"></a>Key Vault (CLI)

* Verificar se um cofre de chaves tem exclusão reversível habilitada

    ```azurecli
    az keyvault show --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* Habilitar exclusão reversível no cofre de chaves

    Todos os novos cofres de chaves têm exclusão reversível habilitada por padrão. Se você tiver atualmente um cofre de chaves que não tenha a exclusão reversível habilitada, use o comando a seguir para habilitar a exclusão reversível.

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-soft-delete true
    ```

* Excluir cofre de chaves (recuperável se a exclusão reversível estiver habilitada)

    ```azurecli
    az keyvault delete --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* Listar todos os cofres de chaves com exclusão reversível

    ```azurecli
    az keyvault list-deleted --subscription {SUBSCRIPTION ID} --resource-type vault
    ```

* Recuperar o cofre de chaves com exclusão reversível

    ```azurecli
    az keyvault recover --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* Limpar cofre de chaves com exclusão reversível **(aviso! Esta operação EXCLUIRá permanentemente o cofre de chaves)**

    ```azurecli
    az keyvault purge --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* Habilitar limpeza-proteção no cofre de chaves

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-purge-protection true
    ```

## <a name="certificates-cli"></a>Certificados (CLI)

* Conceder acesso para limpar e recuperar certificados

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --certificate-permissions recover purge
    ```

* Excluir Certificado

    ```azurecli
    az keyvault certificate delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* Listar certificados excluídos

    ```azurecli
    az keyvault certificate list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Recuperar certificado excluído

    ```azurecli
    az keyvault certificate recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* Limpar certificado excluído de forma reversível **(aviso! Esta operação EXCLUIRá permanentemente seu certificado)**

    ```azurecli
    az keyvault certificate purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

## <a name="keys-cli"></a>Chaves (CLI)

* Conceder acesso para limpar e recuperar chaves

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --key-permissions recover purge
    ```

* Tecla Delete

    ```azurecli
    az keyvault key delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* Listar chaves excluídas

    ```azurecli
    az keyvault key list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Recuperar chave excluída

    ```azurecli
    az keyvault key recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* Limpar chave excluída por software **(aviso! Esta operação EXCLUIRá permanentemente sua chave)**

    ```azurecli
    az keyvault key purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

## <a name="secrets-cli"></a>Segredos (CLI)

* Conceder acesso para limpar e recuperar segredos

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --secret-permissions recover purge
    ```

* Excluir segredo

    ```azurecli
    az keyvault secret delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* Listar segredos excluídos

    ```azurecli
    az keyvault secret list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Recuperar segredo excluído

    ```azurecli
    az keyvault secret recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* Limpar segredo excluído por software **(aviso! Esta operação EXCLUIRá permanentemente seu segredo)**

    ```azurecli
    az keyvault secret purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

## <a name="key-vault-powershell"></a>Key Vault (PowerShell)

* Verificar se um cofre de chaves tem exclusão reversível habilitada

    ```powershell
    Get-AzKeyVault -VaultName "ContosoVault"
    ```

* Excluir cofre de chaves

    ```powershell
    Remove-AzKeyVault -VaultName 'ContosoVault'
    ```

* Listar todos os cofres de chaves com exclusão reversível

    ```powershell
    Get-AzKeyVault -InRemovedState
    ```

* Recuperar o cofre de chaves com exclusão reversível

    ```powershell
    Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
    ```

* Limpar o cofre de chaves com exclusão reversível **(aviso! Esta operação EXCLUIRá permanentemente o cofre de chaves)**

    ```powershell
    Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
    ```

* Habilitar limpeza-proteção no cofre de chaves

    ```powershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"

    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

## <a name="certificates-powershell"></a>Certificados (PowerShell)

* Conceder permissões para recuperar e limpar certificados

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToCertificates recover,purge
    ```

* Excluir um certificado

  ```powershell
  Remove-AzKeyVaultCertificate -VaultName ContosoVault -Name 'MyCert'
  ```

* Listar todos os certificados excluídos em um cofre de chaves

  ```powershell
  Get-AzKeyVaultCertificate -VaultName ContosoVault -InRemovedState
  ```

* Recuperar um certificado no estado excluído

  ```powershell
  Undo-AzKeyVaultCertificateRemoval -VaultName ContosoVault -Name 'MyCert'
  ```

* Limpar um certificado excluído por software **(aviso! Esta operação EXCLUIRá permanentemente seu certificado)**

  ```powershell
  Remove-AzKeyVaultcertificate -VaultName ContosoVault -Name 'MyCert' -InRemovedState
  ```

## <a name="keys-powershell"></a>Chaves (PowerShell)

* Conceder permissões para recuperar e limpar chaves

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys recover,purge
    ```

* Excluir uma chave

  ```powershell
  Remove-AzKeyVaultKey -VaultName ContosoVault -Name 'MyKey'
  ```

* Listar todos os certificados excluídos em um cofre de chaves

  ```powershell
  Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
  ```

* Para recuperar uma chave excluída de maneira reversível

    ```powershell
    Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
    ```

* Limpar uma chave excluída por software **(aviso! Esta operação EXCLUIRá permanentemente sua chave)**

    ```powershell
    Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
    ```

## <a name="secrets-powershell"></a>Segredos (PowerShell)

* Conceder permissões para recuperar e limpar segredos

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToSecrets recover,purge
    ```

* Excluir um segredo denominado SQLPassword

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

* Listar todos os segredos excluídos em um cofre de chaves

  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

* Recuperar um segredo no estado excluído

  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

* Limpar um segredo no estado excluído **(aviso! Esta operação EXCLUIRá permanentemente sua chave)**

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```
---

## <a name="next-steps"></a>Próximas etapas

- [Azure Key Vault cmdlets do PowerShell](/powershell/module/az.keyvault)
- [Comandos de CLI do Azure Key Vault](/cli/azure/keyvault)
- [Backup do Azure Key Vault](backup.md)
- [Como habilitar o registro em log do Key Vault](howto-logging.md)
- [Proteger o acesso a um cofre de chaves](secure-your-key-vault.md)
- [Guia do desenvolvedor do Azure Key Vault](developers-guide.md)
- [Práticas recomendadas para usar um cofre de chaves](security-overview.md)
---
title: Azure Key Vault mover um cofre para uma assinatura diferente | Microsoft Docs
description: Orientação sobre como mover um cofre de chaves para uma assinatura diferente.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 05/05/2020
ms.author: mbaldwin
Customer intent: As a key vault administrator, I want to move my vault to another subscription.
ms.openlocfilehash: 23be8e667d435c2d91d32ebeac30b1e96b45a77e
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790284"
---
# <a name="moving-an-azure-key-vault-to-another-subscription"></a>Como migrar um Azure Key Vault para outra assinatura

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Visão geral

> [!IMPORTANT]
> **Mover um cofre de chaves para outra assinatura causará uma alteração significativa no seu ambiente.**
> Verifique se você entendeu o impacto dessa alteração e siga as orientações neste artigo cuidadosamente antes de decidir mover o cofre de chaves para uma nova assinatura.
> Se você estiver usando o MSI (identidades de serviço gerenciado), leia as instruções de pós-atualização no final do documento. 

[Azure Key Vault](overview.md) é automaticamente vinculado à ID de locatário de [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) padrão para a assinatura na qual ela é criada. Você pode encontrar a ID de locatário associada à sua assinatura seguindo este [guia](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md). Todas as entradas de política de acesso e atribuições de funções também estão ligadas a essa ID de locatário.  Se você mover sua assinatura do Azure do locatário A para o locatário B, seus cofres de chaves existentes ficarão inacessíveis pelas entidades de serviço (usuários e aplicativos) no locatário B. Para corrigir esse problema, você precisa:

* Alterar a ID do locatário associada a todos os cofres de chaves existentes na assinatura para o locatário B.
* Remover todas as entradas de política de acesso existentes.
* Adicionar novas entradas de política de acesso associadas ao locatário B.

Para obter mais informações sobre Azure Key Vault e Azure Active Directory, consulte
- [Sobre o Azure Key Vault](overview.md)
- [O que é Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)
- [Como localizar a ID do locatário](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md)

## <a name="limitations"></a>Limitações

> [!IMPORTANT]
> Os **cofres de chaves usados para a criptografia de disco não podem ser movidos** Se você estiver usando o Key Vault com criptografia de disco para uma VM, o cofre de chaves não poderá ser movido para um grupo de recursos diferente ou uma assinatura enquanto a criptografia de disco estiver habilitada. Você deve desabilitar a criptografia de disco antes de mover o cofre de chaves para um novo grupo de recursos ou assinatura. 

Algumas entidades de serviço (usuários e aplicativos) estão associadas a um locatário específico. Se você mover o cofre de chaves para uma assinatura em outro locatário, haverá uma chance de que você não consiga restaurar o acesso a uma entidade de serviço específica. Verifique se todas as entidades de serviço essenciais existem no locatário em que você está movendo o cofre de chaves.

## <a name="prerequisites"></a>Pré-requisitos

* Acesso de nível de [colaborador](../../role-based-access-control/built-in-roles.md#contributor) ou superior à assinatura atual em que o cofre de chaves existe. Você pode atribuir função usando [portal do Azure](../../role-based-access-control/role-assignments-portal.md), [CLI do Azure](../../role-based-access-control/role-assignments-cli.md)ou [PowerShell](../../role-based-access-control/role-assignments-powershell.md).
* Acesso de nível de [colaborador](../../role-based-access-control/built-in-roles.md#contributor) ou superior à assinatura em que você deseja mover o cofre de chaves. Você pode atribuir função usando [portal do Azure](../../role-based-access-control/role-assignments-portal.md), [CLI do Azure](../../role-based-access-control/role-assignments-cli.md)ou [PowerShell](../../role-based-access-control/role-assignments-powershell.md).
* Um grupo de recursos na nova assinatura. Você pode criar um usando [portal do Azure](../../azure-resource-manager/management/manage-resource-groups-portal.md), [PowerShell](../../azure-resource-manager/management/manage-resource-groups-powershell.md)ou [CLI do Azure](../../azure-resource-manager/management/manage-resource-groups-cli.md).

Você pode verificar as funções existentes usando [portal do Azure](../../role-based-access-control/role-assignments-list-portal.md), [PowerShell](../../role-based-access-control/role-assignments-list-powershell.md), [CLI do Azure](../../role-based-access-control/role-assignments-list-cli.md)ou [API REST](../../role-based-access-control/role-assignments-list-rest.md).


## <a name="moving-a-key-vault-to-a-new-subscription"></a>Movendo um cofre de chaves para uma nova assinatura

1. Entre no Portal do Azure em https://portal.azure.com.
2. Navegue até o [cofre de chaves](overview.md)
3. Clique na guia "visão geral"
4. Selecione o botão "mover"
5. Selecione "mover para outra assinatura" nas opções de lista suspensa
6. Selecione o grupo de recursos para o qual você deseja mover o cofre de chaves
7. Reconhecer o aviso sobre a movimentação de recursos
8. Selecione "OK"

## <a name="additional-steps-when-subscription-is-in-a-new-tenant"></a>Etapas adicionais quando a assinatura está em um novo locatário

Se você moveu o cofre de chaves para uma assinatura em um novo locatário, precisa atualizar manualmente a ID do locatário e remover políticas de acesso antigas e atribuições de função. Aqui estão os tutoriais para essas etapas no PowerShell e CLI do Azure. Se você estiver usando o PowerShell, talvez seja necessário executar o comando Clear-AzContext documentado abaixo para permitir que você veja os recursos fora do escopo selecionado atual. 

### <a name="update-tenant-id-in-a-key-vault"></a>Atualizar a ID de locatário em um cofre de chaves

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource -ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.

Clear-AzContext                                                            #Clear the context from PowerShell
Connect-AzAccount                                                          #Log in again to confirm you have the correct tenant id
````

```azurecli
az account set -s <your-subscriptionId>                                    # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```
### <a name="update-access-policies-and-role-assignments"></a>Atualizar políticas de acesso e atribuições de função

> [!NOTE]
> Se Key Vault estiver usando o modelo de permissão [RBAC do Azure](../../role-based-access-control/overview.md) . Você também precisa remover as atribuições de função do cofre de chaves. Você pode remover atribuições de função usando o [portal do Azure](../../role-based-access-control/role-assignments-portal.md), [CLI do Azure](../../role-based-access-control/role-assignments-cli.md)ou [PowerShell](../../role-based-access-control/role-assignments-powershell.md). 

Agora que seu cofre está associado à ID de locatário correta e às entradas de política de acesso antigas ou a atribuições de função foram removidas, defina novas entradas de política de acesso ou atribuições de função.

Para atribuir políticas, consulte:
- [Atribuir uma política de acesso usando o portal](assign-access-policy-portal.md)
- [Atribuir uma política de acesso usando CLI do Azure](assign-access-policy-cli.md)
- [Atribuir uma política de acesso usando o PowerShell](assign-access-policy-powershell.md)

Para adicionar atribuições de função, consulte:
- [Adicionar atribuição de função usando o portal](../../role-based-access-control/role-assignments-portal.md)
- [Adicionar atribuição de função usando CLI do Azure](../../role-based-access-control/role-assignments-cli.md)
- [Adicionar atribuição de função usando o PowerShell](../../role-based-access-control/role-assignments-powershell.md)


### <a name="update-managed-identities"></a>Atualizar identidades gerenciadas

Se você estiver transferindo uma assinatura inteira e usando uma identidade gerenciada para recursos do Azure, será necessário atualizá-la para o novo locatário Azure Active Directory também. Para obter mais informações sobre identidades gerenciadas, [visão geral da identidade gerenciada](../../active-directory/managed-identities-azure-resources/overview.md).

Se você estiver usando a identidade gerenciada, também precisará atualizar a identidade porque a identidade antiga não estará mais no locatário de Azure Active Directory correto. Consulte os documentos a seguir para ajudar a resolver esse problema. 

* [Atualizando MSI](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)
* [Transferir assinatura para o novo diretório](../../role-based-access-control/transfer-subscription.md)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [chaves, segredos e certificados](about-keys-secrets-certificates.md)
- Para obter informações conceituais, incluindo como interpretar logs de Key Vault, consulte [log de Key Vault](logging.md)
- [Guia do Desenvolvedor do Cofre de Chaves](../general/developers-guide.md)
- [Proteger seu cofre de chaves](secure-your-key-vault.md)
- [Configurar redes virtuais e firewalls do Azure Key Vault](network-security.md)
---
title: 'Azure ExpressRoute: configurar o MACsec'
description: Este artigo ajuda você a configurar o MACsec para proteger as conexões entre os roteadores de borda e os roteadores de borda da Microsoft.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 10/22/2019
ms.author: duau
ms.openlocfilehash: 8ee7f42a4b05def7c2239c7a1e5bcef54f6f7e50
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89395767"
---
# <a name="configure-macsec-on-expressroute-direct-ports"></a>Configurar o MACsec em portas do ExpressRoute Direct

Este artigo ajuda você a configurar o MACsec para proteger as conexões entre os roteadores de borda e os roteadores de borda da Microsoft usando o PowerShell.

## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a configuração, confirme o seguinte:

* Você compreende os [fluxos de trabalho de provisionamento direto do ExpressRoute](expressroute-erdirect-about.md).
* Você criou um [recurso de porta direta do ExpressRoute](expressroute-howto-erdirect.md).
* Se você quiser executar o PowerShell localmente, verifique se a versão mais recente do Azure PowerShell está instalada em seu computador.

### <a name="working-with-azure-powershell"></a>Trabalhando com o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="sign-in-and-select-the-right-subscription"></a>Entre e selecione a assinatura correta

Para iniciar a configuração, entre em sua conta do Azure e selecione a assinatura que você deseja usar.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

## <a name="1-create-azure-key-vault-macsec-secrets-and-user-identity"></a>1. criar Azure Key Vault, segredos MACsec e identidade do usuário

1. Crie uma instância de Key Vault para armazenar segredos do MACsec em um novo grupo de recursos.

    ```azurepowershell-interactive
    New-AzResourceGroup -Name "your_resource_group" -Location "resource_location"
    $keyVault = New-AzKeyVault -Name "your_key_vault_name" -ResourceGroupName "your_resource_group" -Location "resource_location" -EnableSoftDelete 
    ```

    Se você já tiver um cofre de chaves ou um grupo de recursos, poderá reutilizá-los. No entanto, é essencial que você habilite o recurso de [ **exclusão reversível** ](../key-vault/general/soft-delete-overview.md) em seu cofre de chaves existente. Se a exclusão reversível não estiver habilitada, você poderá usar os seguintes comandos para habilitá-la:

    ```azurepowershell-interactive
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "your_existing_keyvault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```
2. Crie uma identidade de usuário.

    ```azurepowershell-interactive
    $identity = New-AzUserAssignedIdentity  -Name "identity_name" -Location "resource_location" -ResourceGroupName "your_resource_group"
    ```

    Se New-AzUserAssignedIdentity não for reconhecido como um cmdlet válido do PowerShell, instale o módulo a seguir (no modo de administrador) e execute novamente o comando acima.

    ```azurepowershell-interactive
    Install-Module -Name Az.ManagedServiceIdentity
    ```
3. Crie uma CAK (chave de associação de conectividade) e um CKN (nome da chave de associação de conectividade) e armazene-as no cofre de chaves.

    ```azurepowershell-interactive
    $CAK = ConvertTo-SecureString "your_key" -AsPlainText -Force
    $CKN = ConvertTo-SecureString "your_key_name" -AsPlainText -Force
    $MACsecCAKSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CAK_name" -SecretValue $CAK
    $MACsecCKNSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CKN_name" -SecretValue $CKN
    ```
4. Atribua a permissão GET à identidade do usuário.

    ```azurepowershell-interactive
    Set-AzKeyVaultAccessPolicy -VaultName "your_key_vault_name" -PermissionsToSecrets get -ObjectId $identity.PrincipalId
    ```

   Agora essa identidade pode obter os segredos, por exemplo, CAK e CKN, do cofre de chaves.
5. Defina essa identidade de usuário para ser usada pelo ExpressRoute.

    ```azurepowershell-interactive
    $erIdentity = New-AzExpressRoutePortIdentity -UserAssignedIdentityId $identity.Id
    ```
 
## <a name="2-configure-macsec-on-expressroute-direct-ports"></a>2. configurar o MACsec nas portas diretas do ExpressRoute

### <a name="to-enable-macsec"></a>Para habilitar MACsec

Cada instância direta do ExpressRoute tem duas portas físicas. Você pode optar por habilitar o MACsec em ambas as portas ao mesmo tempo ou habilitar o MACsec em uma porta por vez. Fazer uma porta no tempo (alternando o tráfego para uma porta ativa durante a manutenção da outra porta) pode ajudar a minimizar a interrupção se o ExpressRoute Direct já estiver em serviço.

1. Defina os segredos e a codificação do MACsec e associe a identidade do usuário à porta para que o código de gerenciamento do ExpressRoute possa acessar os segredos do MACsec, se necessário.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[0]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[1]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.identity = $erIdentity
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```
2. Adicional Se as portas estiverem no estado de administração inativo, você poderá executar os comandos a seguir para abrir as portas.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0].AdminState = "Enabled"
    $erDirect.Links[1].AdminState = "Enabled"
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```

    Neste ponto, o MACsec está habilitado nas portas diretas do ExpressRoute no lado da Microsoft. Se você ainda não o tiver configurado em seus dispositivos de borda, poderá continuar a configurá-los com os mesmos segredos e codificação do MACsec.

### <a name="to-disable-macsec"></a>Para desabilitar o MACsec

Se MACsec não for mais desejado em sua instância do ExpressRoute Direct, você poderá executar os comandos a seguir para desabilitá-lo.

```azurepowershell-interactive
$erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
$erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.identity = $null
Set-AzExpressRoutePort -ExpressRoutePort $erDirect
```

Neste ponto, o MACsec está desabilitado nas portas diretas do ExpressRoute no lado da Microsoft.

### <a name="test-connectivity"></a>Testar a conectividade
Depois de configurar o MACsec (incluindo a atualização de chave do MACsec) nas portas do ExpressRoute Direct, [Verifique](expressroute-troubleshooting-expressroute-overview.md) se as sessões BGP dos circuitos estão ativas e em execução. Se você ainda não tiver nenhum circuito nas portas, crie um primeiro e configure o emparelhamento privado do Azure ou o emparelhamento da Microsoft do circuito. Se MACsec estiver configurado incorretamente, incluindo incompatibilidade de chave MACsec, entre os dispositivos de rede e os dispositivos de rede da Microsoft, você não verá a resolução ARP na camada 2 e no estabelecimento de BGP na camada 3. Se tudo estiver configurado corretamente, você deverá ver as rotas BGP anunciadas corretamente em ambas as direções e o fluxo de dados do aplicativo de acordo com o ExpressRoute.

## <a name="next-steps"></a>Próximas etapas
1. [Criar um circuito do ExpressRoute no ExpressRoute Direct](expressroute-howto-erdirect.md)
2. [Vincular um circuito do ExpressRoute a uma rede virtual do Azure](expressroute-howto-linkvnet-arm.md)
3. [Verificar a conectividade do ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)

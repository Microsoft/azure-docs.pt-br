---
title: Configurar redes virtuais e firewalls do Azure Key Vault - Azure Key Vault
description: Instruções passo a passo para configurar redes virtuais e firewalls do Key Vault
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: sudbalas
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3639237a0dc34c521fd3fa52631fdb19c26ec284
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936338"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Configurar redes virtuais e firewalls do Azure Key Vault

Este artigo fornece diretrizes sobre como configurar o firewall do Azure Key Vault. Este documento aborda as diferentes configurações do firewall do Key Vault com detalhes e fornece instruções passo a passo de como configurar o Azure Key Vault para trabalhar com outros aplicativos e serviços do Azure.

Para obter mais informações, confira [Pontos de extremidade de serviço de rede virtual para o Azure Key Vault](overview-vnet-service-endpoints.md).

## <a name="firewall-settings"></a>Configurações do Firewall

Esta seção aborda as diferentes maneiras como o firewall do Azure Key Vault pode ser configurado.

### <a name="key-vault-firewall-disabled-default"></a>Firewall do Key Vault desabilitado (padrão)

Por padrão, quando você cria um cofre de chaves, o firewall do Azure Key Vault está desabilitado. Todos os aplicativos e serviços do Azure podem acessar o cofre de chaves e enviar solicitações para ele. Observe que essa configuração não significa que qualquer usuário poderá executar operações em seu cofre de chaves. O cofre de chaves ainda restringe os segredos, as chaves e os certificados armazenados nele exigindo as permissões de autenticação e de política de acesso do Azure Active Directory. Para entender a autenticação do cofre de chaves com mais detalhes, confira o documento de conceitos básicos sobre a autenticação do cofre de chaves [aqui](./authentication-fundamentals.md).

### <a name="key-vault-firewall-enabled-trusted-services-only"></a>Firewall do Key Vault habilitado (somente serviços confiáveis)

Quando habilitar o Firewall do Key Vault, você terá a opção de "Permitir que Serviços Confiáveis da Microsoft ignorem este firewall". A lista de serviços confiáveis não abrange todos os serviços do Azure. Por exemplo, o Azure DevOps não está na lista de serviços confiáveis. **Isso não significa que os serviços que não aparecem na lista de serviços confiáveis não sejam confiáveis ou seguros.** A lista de serviços confiáveis abrange os serviços em que a Microsoft controla todo o código executado no serviço. Como os usuários podem escrever código personalizado em serviços do Azure como o Azure DevOps, a Microsoft não fornece a opção de criar uma aprovação ampla para o serviço. Além disso, só porque um serviço aparece na lista de serviços confiáveis, não significa que ele seja permitido em todos os cenários.

Para determinar se um serviço que você está tentando usar está na lista de serviços confiáveis, confira o documento a seguir [aqui](./overview-vnet-service-endpoints.md#trusted-services).

### <a name="key-vault-firewall-enabled-ipv4-addresses-and-ranges---static-ips"></a>Firewall do Key Vault habilitado (intervalos e endereços IPv4 – IPs estáticos)

Se quiser autorizar um serviço específico a acessar o cofre de chaves por meio do Firewall do Key Vault, você poderá adicionar o endereço IP dele à lista de permissões do firewall do cofre de chaves. Essa configuração é melhor para serviços que usam endereços IP estáticos ou intervalos bem conhecidos.

Para permitir um intervalo ou um endereço IP de um recurso do Azure, como um Aplicativo Web ou Aplicativo Lógico, execute as etapas a seguir.

1. Faça logon no Portal do Azure
1. Selecione o recurso (instância específica do serviço)
1. Clique na folha 'Propriedades' em 'Configurações'
1. Procure o campo "Endereço IP".
1. Copie esse valor ou intervalo e insira-o na lista de permissões do firewall do cofre de chaves.

Para permitir que todo um serviço do Azure passe pelo firewall do Key Vault, use a lista de endereços IP de data centers documentados publicamente para o Azure [aqui](https://www.microsoft.com/download/details.aspx?id=41653). Localize os endereços IP associados ao serviço que deseja na região que deseja e adicione-os ao firewall do cofre de chaves usando as etapas acima.

### <a name="key-vault-firewall-enabled-virtual-networks---dynamic-ips"></a>Firewall do Key Vault habilitado (redes virtuais – IPs dinâmicos)

Se estiver tentando permitir que um recurso do Azure, como uma máquina virtual, passe pelo cofre de chaves, você não poderá usar endereços IP estáticos e talvez não queira permitir que todos os endereços IP das Máquinas Virtuais do Azure acessem o cofre de chaves.

Nesse caso, você deve criar o recurso em uma rede virtual e permitir que o tráfego da rede virtual específica e da sub-rede acesse o cofre de chaves. Para fazer isso, execute as seguintes etapas.

1. Faça logon no Portal do Azure
1. Selecione o cofre de chaves que deseja configurar
1. Selecione a folha "Rede"
1. Selecione '+ Adicionar rede virtual existente'
1. Selecione a rede virtual e a sub-rede que deseja permitir que passem pelo firewall do cofre de chaves.

### <a name="key-vault-firewall-enabled-private-link"></a>Firewall do Key Vault habilitado (Link Privado)

Para saber como configurar uma conexão de link privado em seu cofre de chaves, confira o documento [aqui](./private-link-service.md).

> [!IMPORTANT]
> Depois que as regras de firewall estiverem ativas, os usuários podem realizar apenas operações de [plano de dados](secure-your-key-vault.md#data-plane-access-control) do Key Vault quando as solicitações deles originarem-se de redes virtuais ou intervalos de endereços IPv4 permitidos. Isso também aplica-se ao acessar o Key Vault a partir do portal do Azure. Embora os usuários possam navegar em um cofre de chaves a partir do portal do Azure, eles talvez não consigam listar chaves, segredos ou certificados se o computador cliente deles não estiver na lista permitida. Isso também afeta o Seletor do Cofre de Chaves de outros serviços do Azure. Os usuários poderão ver a lista de cofres de chaves, mas não listar chaves, se as regras de firewall impedirem o computador cliente.

> [!NOTE]
> Esteja ciente das seguintes limitações de configuração:
> * Um máximo de 127 regras da rede virtual e 127 regras de IPv4 são permitidas. 
> * Regras de rede IP somente são permitidas para endereços IP públicos. Os intervalos de endereços IP reservados para redes privadas (conforme definido na RFC 1918) não são permitidos em regras de IP. Redes privadas incluem endereços que começam com **10.** , **172.16-31** e **192.168.** . 
> * Atualmente, somente há suporte para endereços IPv4.

## <a name="use-the-azure-portal"></a>Use o Portal do Azure

Segue como configurar redes virtuais e firewalls do Key Vault usando o portal do Azure:

1. Navegue até o cofre de chaves que você quer proteger.
2. Selecione **Rede** e, em seguida, a guia **Firewalls e redes virtuais**.
3. Em **Permitir acesso de**, clique em **Redes selecionadas**.
4. Para adicionar redes virtuais existentes a firewalls e regras da rede virtual, selecione **+ Adicionar redes virtuais existentes**.
5. Na nova folha que é aberta, selecione a assinatura, as redes virtuais e as sub-redes que você quer permitir acesso a esse cofre de chaves. Se as redes virtuais e sub-redes que você selecionar não tiverem pontos de extremidade de serviço habilitados, confirme que você quer habilitar pontos de extremidade de serviço e, em seguida, selecione **Habilitar**. Pode demorar até 15 minutos para entrar em vigor.
6. Em **Redes IP**, adicione intervalos de endereços IPv4, digitando intervalos de endereços IPv4 na [notação CIDR (Roteamento Entre Domínios Sem Classe)](https://tools.ietf.org/html/rfc4632) ou endereços IP individuais.
7. Caso deseje permitir que os Serviços Confiáveis da Microsoft ignorem o Firewall do Key Vault, selecione 'Sim'. Para obter uma lista completa dos Serviços Confiáveis do Key Vault atuais, confira o link a seguir. [Serviços Confiáveis do Azure Key Vault](./overview-vnet-service-endpoints.md#trusted-services)
7. Clique em **Salvar**.

Também é possível adicionar novas redes virtuais e sub-redes e, em seguida, habilitar pontos de extremidade de serviço para as redes virtuais e sub-redes recém-criadas, selecionando **+ Adicionar nova rede virtual**. Em seguida, siga os prompts.

## <a name="use-the-azure-cli"></a>Usar a CLI do Azure 

Segue como configurar redes virtuais e firewalls do Key Vault usando a CLI do Azure

1. [Instale a CLI do Azure](/cli/azure/install-azure-cli) e [conecte-se](/cli/azure/authenticate-azure-cli).

2. Liste as regras da rede virtual disponíveis. Se você ainda não definiu regras para esse cofre de chaves, a lista estará vazia.
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. Habilite um ponto de extremidade de serviço para Key Vault em uma rede virtual e sub-rede existentes.
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. Adicionar uma regra de rede para uma rede virtual e sub-rede.
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. Adicione um intervalo de endereços IP que permite tráfego.
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. Se o cofre de chaves precisar ser acessível por qualquer serviço confiável, defina `bypass` para `AzureServices`.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. Ative as regras de rede definindo a ação padrão como `Deny`.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

## <a name="use-azure-powershell"></a>Usar PowerShell do Azure

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Segue como configurar redes virtuais e firewalls do Key Vault usando o PowerShell:

1. Instale o [Azure PowerShell](/powershell/azure/install-az-ps) mais recente e [conecte](/powershell/azure/authenticate-azureps).

2. Liste as regras da rede virtual disponíveis. Se você ainda não definiu regras para esse cofre de chaves, a lista estará vazia.
   ```powershell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. Habilitar ponto de extremidade de serviço para Key Vault em uma rede virtual e sub-rede existentes.
   ```powershell
   Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
   ```

4. Adicionar uma regra de rede para uma rede virtual e sub-rede.
   ```powershell
   $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. Adicione um intervalo de endereços IP que permite tráfego.
   ```powershell
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. Se o cofre de chaves precisar ser acessível por qualquer serviço confiável, defina `bypass` para `AzureServices`.
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. Ative as regras de rede definindo a ação padrão como `Deny`.
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>Referências
* Referência de modelo ARM: [Referência de modelo ARM do Azure Key Vault](/azure/templates/Microsoft.KeyVault/vaults)
* Comandos da CLI do Azure: [az keyvault network-rule](/cli/azure/keyvault/network-rule)
* Cmdlets do Azure PowerShell: [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Próximas etapas

* [Pontos de extremidade de serviço de rede virtual para Key Vault](overview-vnet-service-endpoints.md)
* [Visão geral da segurança do Azure Key Vault](security-overview.md)
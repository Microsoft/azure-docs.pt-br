---
title: 'Tutorial: Proteger um servidor Web do Windows com certificados TLS/SSL no Azure'
description: Neste tutorial, você aprenderá a usar o Azure PowerShell para proteger uma máquina virtual do Windows que executa o servidor Web do IIS com certificados TLS/SSL armazenados no Azure Key Vault.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 4e5bbe179c43508033e0747bc2c03089f283b6e2
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98873072"
---
# <a name="tutorial-secure-a-web-server-on-a-windows-virtual-machine-in-azure-with-tlsssl-certificates-stored-in-key-vault"></a>Tutorial: Proteger um servidor Web em uma máquina virtual do Windows no Azure com certificados TLS/SSL armazenados no Key Vault

> [!NOTE]
> Atualmente, este documento funciona apenas para imagens Generalizadas. Se tentar realizar este tutorial usando um disco Especializado, você receberá um erro. 

Para proteger servidores Web, um certificado de protocolo TLS, anteriormente conhecido como protocolo SSL, pode ser usado para criptografar o tráfego da Web. Esses certificados TLS/SSL podem ser armazenados no Azure Key Vault e permitem implantações seguras de certificados em VMs (máquinas virtuais) do Windows no Azure. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Criar um Cofre de chaves do Azure
> * Gerar ou carregar um certificado para o Cofre da Chave
> * Criar uma VM e instalar o servidor Web do IIS
> * Inserir o certificado na VM e configurar o IIS com uma associação de TLS


## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Você também pode iniciar o Cloud Shell em uma guia separada do navegador indo até [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selecione **Copiar** para copiar os blocos de código, cole o código no Cloud Shell e depois pressione Enter para executá-lo.


## <a name="overview"></a>Visão geral
O cofre da chave do Azure protege chaves criptográficas e segredos, esses certificados ou senhas. O Key Vault simplifica o processo de gerenciamento de certificados e permite que você mantenha o controle das chaves que acessam esses certificados. Você pode criar um certificado autoassinado no Key Vault ou carregar um certificado confiável existente que você já tenha.

Em vez de usar uma imagem de VM personalizada que inclui certificados incorporados, você injeta certificados em uma VM em execução. Esse processo garante que os certificados mais recentes sejam instalados em um servidor Web durante a implantação. Se você renova ou substitui um certificado, também não precisa criar uma nova imagem de VM personalizada. Os certificados mais recentes são inseridos automaticamente, conforme você cria outras VMs. Durante todo o processo, os certificados nunca deixam a plataforma do Azure ou são expostos em um script, no histórico da linha de comando ou no modelo.


## <a name="create-an-azure-key-vault"></a>Criar um Cofre de chaves do Azure
Antes de criar um Key Vault e certificados, crie um grupo de recursos com [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroupSecureWeb* no local *Leste dos EUA*:

```azurepowershell-interactive
$resourceGroup = "myResourceGroupSecureWeb"
$location = "East US"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

Em seguida, crie um Key Vault com [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Cada Cofre de Chave requer um nome exclusivo e deve estar escrito com todas as letras minúsculas. Substitua `mykeyvault` no exemplo a seguir com seu próprio nome exclusivo de Cofre da Chave:

```azurepowershell-interactive
$keyvaultName="mykeyvault"
New-AzKeyVault -VaultName $keyvaultName `
    -ResourceGroup $resourceGroup `
    -Location $location `
    -EnabledForDeployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>Gerar um certificado e armazenar no Key Vault
Para uso em produção, você deve importar um certificado válido assinado por um fornecedor confiável com [Import-AzKeyVaultCertificate](/powershell/module/az.keyvault/import-azkeyvaultcertificate). Para este tutorial, o exemplo a seguir mostra como você pode gerar um certificado autoassinado com [Add-AzKeyVaultCertificate](/powershell/module/az.keyvault/add-azkeyvaultcertificate), que usa a política de certificado padrão de [New-AzKeyVaultCertificatePolicy](/powershell/module/az.keyvault/new-azkeyvaultcertificatepolicy). 

```azurepowershell-interactive
$policy = New-AzKeyVaultCertificatePolicy `
    -SubjectName "CN=www.contoso.com" `
    -SecretContentType "application/x-pkcs12" `
    -IssuerName Self `
    -ValidityInMonths 12

Add-AzKeyVaultCertificate `
    -VaultName $keyvaultName `
    -Name "mycert" `
    -CertificatePolicy $policy 
```


## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
Defina o nome de usuário e a senha de um administrador para a VM com [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Agora você pode criar a VM com [New-AzVM](/powershell/module/az.compute/new-azvm). O exemplo a seguir cria uma VM chamada *myVM* na localização *EastUs*. Se ainda não existirem, os recursos de rede de suporte são criados. Para permitir o tráfego seguro da Web, o cmdlet também abre a porta *443*.

```azurepowershell-interactive
# Create a VM
New-AzVm `
    -ResourceGroupName $resourceGroup `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred `
    -OpenPorts 443

# Use the Custom Script Extension to install IIS
Set-AzVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server -IncludeManagementTools"}'
```

A criação da VM demora alguns minutos. A última etapa usa a Extensão de Script Personalizado do Azure para instalar o servidor Web do IIS com [Set-AzVmExtension](/powershell/module/az.compute/set-azvmextension).


## <a name="add-a-certificate-to-vm-from-key-vault"></a>Adicionar um certificado à VM a partir do Key Vault
Para adicionar o certificado a partir do Key Vault para uma VM, obtenha a ID de seu certificado com [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret). Adicione o certificado à VM com [Add-AzVMSecret](/powershell/module/az.compute/add-azvmsecret):

```azurepowershell-interactive
$certURL=(Get-AzKeyVaultSecret -VaultName $keyvaultName -Name "mycert").id

$vm=Get-AzVM -ResourceGroupName $resourceGroup -Name "myVM"
$vaultId=(Get-AzKeyVault -ResourceGroupName $resourceGroup -VaultName $keyVaultName).ResourceId
$vm = Add-AzVMSecret -VM $vm -SourceVaultId $vaultId -CertificateStore "My" -CertificateUrl $certURL

Update-AzVM -ResourceGroupName $resourceGroup -VM $vm
```


## <a name="configure-iis-to-use-the-certificate"></a>Configurar o IIS para usar o certificado
Use a Extensão de Script Personalizado novamente com [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) para atualizar a configuração do IIS. Esta atualização aplica o certificado inserido do Key Vault para o IIS e configura a associação da Web:

```azurepowershell-interactive
$PublicSettings = '{
    "fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/secure-iis.ps1"],
    "commandToExecute":"powershell -ExecutionPolicy Unrestricted -File secure-iis.ps1"
}'

Set-AzVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString $publicSettings
```


### <a name="test-the-secure-web-app"></a>Testar o aplicativo Web protegido
Obtenha o endereço IP público da VM com [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). O exemplo a seguir obtém o endereço IP para `myPublicIP` criado anteriormente:

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName $resourceGroup -Name "myPublicIPAddress" | select "IpAddress"
```

Agora, abra um navegador da Web e digite `https://<myPublicIP>` na barra de endereços. Para aceitar o aviso de segurança se você usou um certificado autoassinado, selecione **Detalhes** e **Prosseguir para a página da Web**:

![Aceite o aviso de segurança do navegador da web](./media/tutorial-secure-web-server/browser-warning.png)

Seu site de IIS protegido é exibido, como no exemplo a seguir:

![Exibir o site de IIS protegido em execução](./media/tutorial-secure-web-server/secured-iis.png)


## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você protegeu um servidor Web do IIS com um certificado TLS/SSL armazenado no Azure Key Vault. Você aprendeu a:

> [!div class="checklist"]
> * Criar um Cofre de chaves do Azure
> * Gerar ou carregar um certificado para o Cofre da Chave
> * Criar uma VM e instalar o servidor Web do IIS
> * Inserir o certificado na VM e configurar o IIS com uma associação de TLS

Siga este link para ver exemplos de script de máquina virtual predefinido.

> [!div class="nextstepaction"]
> [Exemplos de script de máquina virtual do Windows](https://github.com/Azure/azure-docs-powershell-samples/tree/master/virtual-machine)
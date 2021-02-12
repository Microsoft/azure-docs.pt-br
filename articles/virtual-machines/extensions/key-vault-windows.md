---
title: Azure Key Vault extensão de VM para Windows
description: Implante um agente que execute a atualização automática de segredos de Cofre de Chaves em máquinas virtuais usando uma extensão da máquina virtual.
services: virtual-machines-windows
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: e1a9f5d08168841d7651a17e2de4995b7a7cf38b
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820714"
---
# <a name="key-vault-virtual-machine-extension-for-windows"></a>Extensão da máquina virtual de Key Vault para Windows

A extensão de VM de Key Vault fornece a atualização automática dos certificados armazenados no cofre de chaves do Azure. Especificamente, os monitores de extensão observado de uma lista de certificados armazenados no cofre de chaves e, ao detectar uma alteração, recupera e instala os certificados correspondentes. Este documento detalha as plataformas com opções de plataformas, configurações e implantação com suporte para a extensão da VM de Key Vault para Windows. 

### <a name="operating-system"></a>Sistema operacional

A extensão de VM Key Vault dá suporte a versões anteriores do Windows:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012

A extensão de VM Key Vault também tem suporte na VM local Personalizada que é carregada e convertida em uma imagem especializada para uso no Azure usando a instalação básica do Windows Server 2019.

### <a name="supported-certificate-content-types"></a>Tipos suportados de conteúdo de certificado

- PKCS #12
- PEM

## <a name="prerequisities"></a>Pré-requisitos
  - Key Vault instância com certificado. Consulte [criar um Key Vault](../../key-vault/general/quick-create-portal.md)
  - A VM deve ter a [identidade gerenciada](../../active-directory/managed-identities-azure-resources/overview.md) atribuída
  - A política de acesso de Key Vault deve ser definida com segredos `get` e `list` permissão para a identidade gerenciada VM/VMSS para recuperar a parte de um segredo do certificado. Consulte [como autenticar para Key Vault](../../key-vault/general/authentication.md) e [atribuir uma política de acesso de Key Vault](../../key-vault/general/assign-access-policy-cli.md).
  -  VMSS deve ter a seguinte configuração de identidade: ` 
  "identity": {
  "type": "UserAssigned",
  "userAssignedIdentities": {
  "[parameters('userAssignedIdentityResourceId')]": {}
  }
  }
  `
  
- A extensão AKV deve ter essa configuração: `
                  "authenticationSettings": {
                    "msiEndpoint": "[parameters('userAssignedIdentityEndpoint')]",
                    "msiClientId": "[reference(parameters('userAssignedIdentityResourceId'), variables('msiApiVersion')).clientId]"
                  }
   `
## <a name="extension-schema"></a>Esquema de extensão

O JSON a seguir mostra o esquema para a extensão da VM de Key Vault. A extensão não requer configurações protegidas. todas as suas configurações são consideradas informações públicas. A extensão requer uma lista de certificados monitorados, a frequência de sondagem e o repositório de certificados de destino. Especificamente:  

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "linkOnRenewal": <Only Windows. This feature ensures s-channel binding when certificate renews, without necessitating a re-deployment.  e.g.: false>,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        },
        "authenticationSettings": {
                "msiEndpoint":  <Optional MSI endpoint e.g.: "http://169.254.169.254/metadata/identity">,
                "msiClientId":  <Optional MSI identity e.g.: "c7373ae5-91c2-4165-8ab6-7381d6e75619">
        }
       }
      }
    }
```

> [!NOTE]
> Suas URLs de certificado observadas devem estar no formato `https://myVaultName.vault.azure.net/secrets/myCertName`.
> 
> Isso porque o caminho `/secrets` retorna todo o certificado, incluindo a chave privada, enquanto o caminho `/certificates` não faz isso. Mais informações sobre certificados podem ser encontradas aqui: [Certificados do Key Vault](../../key-vault/general/about-keys-secrets-certificates.md)

> [!IMPORTANT]
> A propriedade ' authenticationSettings ' é **necessária** somente para VMs com **identidades atribuídas pelo usuário**.
> Especifica a identidade a ser usada para autenticação para Key Vault.


### <a name="property-values"></a>Valores de propriedade

| Nome | Valor/Exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| publicador | Microsoft.Azure.KeyVault | string |
| type | KeyVaultForWindows | string |
| typeHandlerVersion | 1.0 | INT |
| pollingIntervalInS | 3600 | string |
| certificateStoreName | MY | string |
| linkOnRenewal | false | booleano |
| certificateStoreLocation  | LocalMachine ou CurrentUser (diferencia maiúsculas de minúsculas) | string |
| requireInitialSync | true | booleano |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate","https://myvault.vault.azure.net/secrets/mycertificate2"] | Matriz de cadeia de caracteres
| msiEndpoint | http://169.254.169.254/metadata/identity | string |
| msiClientId | c7373ae5-91c2-4165-8ab6-7381d6e75619 | string |


## <a name="template-deployment"></a>Implantação de modelo

Extensões de VM do Azure podem ser implantadas com modelos do Azure Resource Manager. Modelos são ideais ao implantar uma ou mais máquinas virtuais que exigem renovação de certificados pós-implantação. A extensão pode ser implantada em VMs individuais ou conjunto de dimensionamento de máquinas virtuais. O esquema e a configuração são comuns a ambos os tipos de modelo. 

A configuração JSON para uma extensão de máquina virtual deve ser aninhada dentro do fragmento do recurso de máquina virtual do modelo, especificamente o objeto `"resources": []` para o modelo de máquina virtual e, no caso de conjunto de dimensionamento de máquinas virtuais, no objeto `"virtualMachineProfile":"extensionProfile":{"extensions" :[]`.

 > [!NOTE]
> A extensão de VM exigiria que a identidade gerenciada do sistema ou do usuário fosse atribuída para autenticar no Key Vault.  Consulte [como autenticar para Key Vault e atribuir uma política de acesso de Key Vault.](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
> 

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KeyVaultForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: ["https://myvault.vault.azure.net/secrets/mycertificate", "https://myvault.vault.azure.net/secrets/mycertificate2"]>
        }      
      }
      }
    }
```

### <a name="extension-dependency-ordering"></a>Ordenação de dependência de extensão
A extensão de VM Key Vault dá suporte à ordenação de extensão, se configurada. Por padrão, a extensão relata que ela foi iniciada com êxito assim que começou a sondagem. No entanto, ele pode ser configurado para aguardar até que tenha baixado com êxito a lista completa de certificados antes de relatar um início bem-sucedido. Se outras extensões dependerem de ter o conjunto completo de certificados instalados antes de serem iniciadas, habilitar essa configuração permitirá que essas extensões declarem uma dependência na extensão de Key Vault. Isso impedirá que essas extensões sejam iniciadas até que todos os certificados dos quais dependem tenham sido instalados. A extensão tentará novamente o download inicial de forma indefinida e permanecerá em um `Transitioning` estado.

Para ativar isso, defina o seguinte:
```
"secretsManagementSettings": {
    "requireInitialSync": true,
    ...
}
```
> Anotações O uso desse recurso não é compatível com um modelo ARM que cria uma identidade atribuída pelo sistema e atualiza uma política de acesso de Key Vault com essa identidade. Isso resultará em um deadlock, uma vez que a política de acesso do cofre não pode ser atualizada até que todas as extensões sejam iniciadas. Em vez disso, você deve usar uma *identidade de MSI atribuída por um único usuário* e pré-ACL de seus cofres com essa identidade antes da implantação.

## <a name="azure-powershell-deployment"></a>Implantação do Azure PowerShell
> [!WARNING]
> Os clientes do PowerShell geralmente se adicionam `\` ao `"` no settings.js, o que caakvvm_service usará falha com o erro: `[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.`

O Azure PowerShell pode ser usado para implantar a extensão da VM do Diagnóstico de Key Vault em uma máquina virtual existente ou em um conjunto de dimensionamento de máquina virtual. 

* Para implantar a extensão em uma VM:
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCert1> + '","' + <observedCert2> + '"] } }'
        $extName =  "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
       
    
        # Start the deployment
        Set-AzVmExtension -TypeHandlerVersion "1.0" -ResourceGroupName <ResourceGroupName> -Location <Location> -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* Para implantar a extensão em um conjunto de dimensionamento de máquinas virtuais:

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCert1> + '","' + <observedCert2> + '"] } }'
        $extName = "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
        
        # Add Extension to VMSS
        $vmss = Get-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName>
        Add-AzVmssExtension -VirtualMachineScaleSet $vmss  -Name $extName -Publisher $extPublisher -Type $extType -TypeHandlerVersion "1.0" -Setting $settings

        # Start the deployment
        Update-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName> -VirtualMachineScaleSet $vmss 
    
    ```

## <a name="azure-cli-deployment"></a>Implantação da CLI do Azure

A CLI do Azure pode ser usada para implantar a extensão da VM do Key Vault em uma máquina virtual existente ou em um conjunto de dimensionamento de máquinas virtuais. 
 
* Para implantar a extensão em uma VM:
    
    ```azurecli
       # Start the deployment
         az vm extension set --name "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         --resource-group "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```

* Para implantar a extensão em um conjunto de dimensionamento de máquinas virtuais:

   ```azurecli
        # Start the deployment
        az vmss extension set --name "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         --resource-group "<resourcegroup>" `
         --vmss-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```

Por favor esteja ciente das seguintes restrições/exigências:
- Restrições de Key Vault:
  - Ele deve existir no momento da implantação 
  - A política de acesso de Key Vault deve ser definida para a identidade VM/VMSS usando uma identidade gerenciada. Consulte [como autenticar para Key Vault](../../key-vault/general/authentication.md) e [atribuir uma política de acesso de Key Vault](../../key-vault/general/assign-access-policy-cli.md).

## <a name="troubleshoot-and-support"></a>Solução de problemas e suporte

### <a name="frequently-asked-questions"></a>Perguntas frequentes

* Há um limite no número de observedCertificates que você pode configurar?
  Não, Key Vault extensão de VM não tem limite no número de observedCertificates.

### <a name="troubleshoot"></a>Solucionar problemas

Os dados sobre o estado das implantações de extensão podem ser recuperados no Portal do Azure usando o Azure PowerShell. Para ver o estado da implantação das extensões de uma determinada VM, execute o comando a seguir usando o Azure PowerShell.

**PowerShell do Azure**
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

**CLI do Azure**
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```

#### <a name="logs-and-configuration"></a>Logs e configuração

```
%windrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.KeyVault.KeyVaultForWindows\<version>\akvvm_service_<date>.log
```

### <a name="support"></a>Suporte

Caso precise de mais ajuda em qualquer ponto deste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/forums/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes sobre o suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).

---
title: Extensão de VM do Azure Key Vault para Linux
description: Implante um agente que execute a atualização automática de certificados de Key Vault em máquinas virtuais usando uma extensão da máquina virtual.
services: virtual-machines
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: linux
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 9032bfca30ead56c91d7904e18b76753cf3b6dfc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582163"
---
# <a name="key-vault-virtual-machine-extension-for-linux"></a>Extensão da máquina virtual de Key Vault para Linux

A extensão de VM de Key Vault fornece a atualização automática dos certificados armazenados no cofre de chaves do Azure. Especificamente, a extensão monitora uma lista de certificados observados armazenados em cofres de chaves.  Após detectar uma alteração, a extensão recupera e instala os certificados correspondentes. A extensão de VM do Key Vault é publicada e suportada pela Microsoft, no momento em VMs do Linux. Este documento detalha as plataformas com opções de plataformas, configurações e implantação com suporte para a extensão da VM de Key Vault para Linux. 

### <a name="operating-system"></a>Sistema operacional

A extensão de VM do Key Vault dá suporte a essas distribuições do Linux:

- Ubuntu-1804
- Suse-15 

> [!NOTE]
> Para obter recursos de segurança estendidos, prepare-se para atualizar os sistemas Ubuntu-1604 e Debian-9, pois essas versões estão atingindo o fim do período de suporte designado.
> 

### <a name="supported-certificate-content-types"></a>Tipos suportados de conteúdo de certificado

- PKCS #12
- PEM


## <a name="prerequisities"></a>Pré-requisitos
  - Key Vault instância com certificado. Consulte [criar um Key Vault](../../key-vault/general/quick-create-portal.md)
  - VM/VMSS devem ter uma [identidade gerenciada](../../active-directory/managed-identities-azure-resources/overview.md) atribuída
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
## <a name="key-vault-vm-extension-version"></a>Versão da extensão de VM Key Vault
* Os usuários do Ubuntu-18, 4 e SUSE-15 podem optar por atualizar a versão da extensão de VM do cofre de chaves para o `V2.0` recurso de download completo da cadeia de certificados. Os certificados do emissor (intermediário e raiz) serão anexados ao certificado de folha no arquivo PEM.

* Se preferir atualizar para o `v2.0` , você precisará excluir `v1.0` primeiro e depois instalar o `v2.0` .
```
  az vm extension delete --name KeyVaultForLinux --resource-group ${resourceGroup} --vm-name ${vmName}
  az vm extension set -n "KeyVaultForLinux" --publisher Microsoft.Azure.KeyVault --resource-group "${resourceGroup}" --vm-name "${vmName}" –settings .\akvvm.json –version 2.0
```  
  O sinalizador--versão 2,0 é opcional porque a versão mais recente será instalada por padrão.   

* Se a VM tiver certificados baixados por v 1.0, a exclusão da extensão AKVVM v 1.0 não excluirá os certificados baixados.  Depois de instalar o v 2.0, os certificados existentes não serão modificados.  Você precisará excluir os arquivos de certificado ou fazer o registro do certificado para obter o arquivo PEM com cadeia total na VM.




## <a name="extension-schema"></a>Esquema de extensão

O JSON a seguir mostra o esquema para a extensão da VM de Key Vault. A extensão não requer configurações protegidas - todas as suas configurações são consideradas informações sem impacto de segurança. A extensão requer uma lista dos segredos monitorados, a frequência de sondagem e o repositório de certificados de destino. Especificamente:  
```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForLinux",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "2.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <It is ignored on Linux>,
          "linkOnRenewal": <Not available on Linux e.g.: false>,
          "certificateStoreLocation": <disk path where certificate is stored, default: "/var/lib/waagent/Microsoft.Azure.KeyVault">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: ["https://myvault.vault.azure.net/secrets/mycertificate", "https://myvault.vault.azure.net/secrets/mycertificate2"]>
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
| type | KeyVaultForLinux | string |
| typeHandlerVersion | 2,0 | INT |
| pollingIntervalInS | 3600 | string |
| certificateStoreName | Ele é ignorado no Linux | string |
| linkOnRenewal | false | booleano |
| certificateStoreLocation  | /var/lib/waagent/Microsoft.Azure.KeyVault | string |
| requireInitialSync | true | booleano |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate", "https://myvault.vault.azure.net/secrets/mycertificate2"] | Matriz de cadeia de caracteres
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
      "name": "KeyVaultForLinux",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "2.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
          "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <ingnored on linux>,
          "certificateStoreLocation": <disk path where certificate is stored, default: "/var/lib/waagent/Microsoft.Azure.KeyVault">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
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
        $extName =  "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
       
    
        # Start the deployment
        Set-AzVmExtension -TypeHandlerVersion "2.0" -ResourceGroupName <ResourceGroupName> -Location <Location> -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* Para implantar a extensão em um conjunto de dimensionamento de máquinas virtuais:

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCert1> + '","' + <observedCert2> + '"] } }'
        $extName = "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
        
        # Add Extension to VMSS
        $vmss = Get-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName>
        Add-AzVmssExtension -VirtualMachineScaleSet $vmss  -Name $extName -Publisher $extPublisher -Type $extType -TypeHandlerVersion "2.0" -Setting $settings

        # Start the deployment
        Update-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName> -VirtualMachineScaleSet $vmss 
    
    ```

## <a name="azure-cli-deployment"></a>Implantação da CLI do Azure

A CLI do Azure pode ser usada para implantar a extensão da VM do Key Vault em uma máquina virtual existente ou em um conjunto de dimensionamento de máquinas virtuais. 
 
* Para implantar a extensão em uma VM:
    
    ```azurecli
       # Start the deployment
         az vm extension set -n "KeyVaultForLinux" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vm-name "<vmName>" `
         --version 2.0 `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```

* Para implantar a extensão em um conjunto de dimensionamento de máquinas virtuais:

   ```azurecli
        # Start the deployment
        az vmss extension set -n "KeyVaultForLinux" `
        --publisher Microsoft.Azure.KeyVault `
        -g "<resourcegroup>" `
        --vmss-name "<vmssName>" `
        --version 2.0 `
        --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```
Por favor esteja ciente das seguintes restrições/exigências:
- Restrições de Key Vault:
  - Ele deve existir no momento da implantação 
  - A política de acesso de Key Vault deve ser definida para a identidade VM/VMSS usando uma identidade gerenciada. Consulte [como autenticar para Key Vault](../../key-vault/general/authentication.md) e [atribuir uma política de acesso de Key Vault](../../key-vault/general/assign-access-policy-cli.md).

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
/var/log/waagent.log
/var/log/azure/Microsoft.Azure.KeyVault.KeyVaultForLinux/*
/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-<most recent version>/config/*
```
### <a name="using-symlink"></a>Usando symlink

Links simbólicos ou symlinks são basicamente atalhos avançados. Para evitar o monitoramento da pasta e obter o certificado mais recente automaticamente, você pode usar esse symlink `([VaultName].[CertificateName])` para obter a versão mais recente do certificado no Linux.

### <a name="frequently-asked-questions"></a>Perguntas frequentes

* Há um limite no número de observedCertificates que você pode configurar?
  Não, Key Vault extensão de VM não tem limite no número de observedCertificates.

### <a name="support"></a>Suporte

Caso precise de mais ajuda em qualquer ponto deste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/forums/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes sobre o suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).

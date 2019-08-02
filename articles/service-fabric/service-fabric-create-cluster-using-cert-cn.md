---
title: Criar um cluster do Azure Service Fabric usando o nome comum do certificado | Microsoft Docs
description: Saiba como criar um cluster do Service Fabric usando o nome comum do certificado a partir de um modelo.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: aljo
ms.openlocfilehash: fe1adc0aef80066721ce0b80419c787fe25346a9
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190811"
---
# <a name="deploy-a-service-fabric-cluster-that-uses-certificate-common-name-instead-of-thumbprint"></a>Implantar um cluster do Service Fabric que usa o nome comum do certificado em vez de impressão digital
Dois certificados não podem ter a mesma impressão digital, o que dificulta a substituição ou gerenciamento de certificados de cluster. Vários certificados, no entanto, podem ter o mesmo nome comum ou assunto.  Um cluster usando nomes comuns do certificado simplifica muito o gerenciamento de certificados. Este artigo descreve como implantar um cluster do Service Fabric para usar o nome comum do certificado em vez da impressão digital do certificado.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-a-certificate"></a>Obter um certificado
Primeiro, obtenha um [certificado de uma autoridade de certificação (CA)](https://wikipedia.org/wiki/Certificate_authority).  O nome comum do certificado deve ser para o domínio personalizado de sua propriedade e comprado de um registrador de domínios. Por exemplo, "azureservicefabricbestpractices.com"; aqueles que não são funcionários da Microsoft não podem provisionar certificados para domínios da MS, portanto, você não poderá usar os nomes DNS do seu LB ou Gerenciador de Tráfego como nomes comuns para o seu certificado e precisará provisionar uma [Zona DNS do Azure](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns) se o domínio personalizado precisar ser resolvido no Azure. Declare também o domínio personalizado de sua propriedade como o "managementEndpoint" do cluster se você quiser que o portal reflita o alias do domínio personalizado para seu cluster.

Para fins de teste, você pode obter um certificado assinado CA de uma autoridade de certificação livre ou aberta.

> [!NOTE]
> Não há suporte para certificados autoassinados, inclusive os gerados durante a implantação de um cluster do Service Fabric no Portal do Azure. 

## <a name="upload-the-certificate-to-a-key-vault"></a>Carregar o certificado em um cofre de chaves
No Azure, um cluster do Service Fabric é um conjunto de dimensionamento de máquinas virtuais.  Carregue o certificado em um cofre de chaves.  Quando o cluster é implantado, o certificado é instalado no conjunto de dimensionamento de máquina virtual em que o cluster está em execução.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    
```

## <a name="download-and-update-a-sample-template"></a>Fazer o download e atualizar um modelo de exemplo
Este artigo usa o modelo de exemplo e parâmetros de modelo do [cluster seguro de cinco nós](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure). Faça o download dos arquivos *azuredeploy.json* e *azuredeploy.parameters.json* no seu computador.

### <a name="update-parameters-file"></a>Atualizar arquivo de parâmetros
Primeiro, abra o arquivo *azuredeploy.parameters.json* em um editor de texto e adicione o seguinte valor de parâmetro:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"certificateIssuerThumbprint": {
    "value": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
},
```

Em seguida, defina os valores de parâmetro *certificateCommonName*, *sourceVaultValue* e *certificateUrlValue* para aqueles retornados pelo script anterior:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"certificateIssuerThumbprint": {
    "value": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
},
"sourceVaultValue": {
  "value": "/subscriptions/<subscription>/resourceGroups/testvaultgroup/providers/Microsoft.KeyVault/vaults/testvault"
},
"certificateUrlValue": {
  "value": "https://testvault.vault.azure.net:443/secrets/testcert/5c882b7192224447bbaecd5a46962655"
},
```

### <a name="update-the-template-file"></a>Atualizar o arquivo de modelo
Em seguida, abra o arquivo *azuredeploy.json* em um editor de texto e faça três atualizações para dar suporte ao nome comum do certificado.

1. Na seção **Parâmetros**, adicione um parâmetro *certificateCommonName*:
    ```json
    "certificateCommonName": {
      "type": "string",
      "metadata": {
        "description": "Certificate Commonname"
      }
    },
    "certificateIssuerThumbprint": {
      "type": "string",
      "metadata": {
        "description": "Certificate Authority Issuer Thumpbrint for Commonname cert"
      }
    },
    ```

    Também Considere remover o *certificateThumbprint*, ele pode no ser mais necessário.

2. Defina o valor da variável *sfrpApiVersion* para "2018-02-01":
    ```json
    "sfrpApiVersion": "2018-02-01",
    ```

3. No recurso **Microsoft.Compute/virtualMachineScaleSets**, atualize a extensão de máquina virtual para usar o nome comum em configurações de certificado em vez da impressão digital.  Em **virtualMachineProfile**->**extensionProfile**->**extensões**->**propriedades**->**configurações**->**certificado**, adicionar 
    ```json
       "commonNames": [
        "[parameters('certificateCommonName')]"
       ],
    ```

    e remover `"thumbprint": "[parameters('certificateThumbprint')]",`.

    ```json
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              "type": "ServiceFabricNode",
              "autoUpgradeMinorVersion": true,
              "protectedSettings": {
                "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
              },
              "publisher": "Microsoft.Azure.ServiceFabric",
              "settings": {
                "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                "nodeTypeRef": "[variables('vmNodeType0Name')]",
                "dataPath": "D:\\SvcFab",
                "durabilityLevel": "Bronze",
                "enableParallelJobs": true,
                "nicPrefixOverride": "[variables('subnet0Prefix')]",
                "certificate": {
                  "commonNames": [
                     "[parameters('certificateCommonName')]"
                  ],
                  "x509StoreName": "[parameters('certificateStoreValue')]"
                }
              },
              "typeHandlerVersion": "1.0"
            }
          },
    ```

4. No recurso **Microsoft.ServiceFabric/clusters**, atualize a versão da API para "2018-02-01".  Adicione também uma configuração **certificateCommonNames** com uma propriedade **commonNames** e remova a configuração **certificado** (com a propriedade de impressão digital), como no exemplo a seguir:
   ```json
   {
       "apiVersion": "2018-02-01",
       "type": "Microsoft.ServiceFabric/clusters",
       "name": "[parameters('clusterName')]",
       "location": "[parameters('clusterLocation')]",
       "dependsOn": [
       "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
       ],
       "properties": {
       "addonFeatures": [
           "DnsService",
           "RepairManager"
       ],        
       "certificateCommonNames": {
           "commonNames": [
           {
               "certificateCommonName": "[parameters('certificateCommonName')]",
               "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
           }
           ],
           "x509StoreName": "[parameters('certificateStoreValue')]"
       },
       ...
   ```
   > [!NOTE]
   > O campo 'certificateIssuerThumbprint' permite especificar os emissores de certificados esperados com um nome comum da entidade especificada. Este campo aceita uma enumeração separada por vírgula de impressões digitais SHA1. Observe que este é um fortalecimento da validação de certificado – no caso, quando o emissor não for especificado ou estiver vazio, o certificado será aceito para autenticação se a cadeia puder ser criada e terminará em uma raiz de confiança do validador. Se o emissor for especificado, o certificado será aceito se a impressão digital de seu emissor direto corresponder a qualquer um dos valores especificados neste campo – independentemente de a raiz ser confiável ou não. Observe que uma PKI pode usar diferentes autoridades de certificação para emitir certificados para a mesma entidade e, portanto, é importante especificar todas as impressões digitais do emissor esperadas para uma determinada entidade.
   >
   > Especificar o emissor é considerado uma melhor prática. Embora sua omissão continue funcionando – para certificados com uma cadeia confiável até uma raiz confiável –, esse comportamento tem limitações e poderá ser desativado em breve. Observe também que clusters implantados no Azure e protegidos por certificados X509 emitidos por uma PKI privada e declarados por uma entidade talvez não possam ser validados pelo serviço do Azure Service Fabric (para comunicação de cluster com serviço) se a política de certificação da PKI não for localizável, disponível e acessível. 

## <a name="deploy-the-updated-template"></a>Implantar o modelo atualizado
Reimplante o modelo atualizado após fazer as alterações.

```powershell
# Variables.
$groupname = "testclustergroup"
$clusterloc="southcentralus"  
$id="<subscription ID"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $id 

# Create a new resource group and deploy the cluster.
New-AzResourceGroup -Name $groupname -Location $clusterloc

New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\AzureDeploy.Parameters.json" -TemplateFile "C:\temp\cluster\AzureDeploy.json" -Verbose
```

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [segurança de cluster](service-fabric-cluster-security.md).
* Saiba como [substituir um certificado de cluster](service-fabric-cluster-rollover-cert-cn.md)
* [Atualizar e gerenciar certificados do cluster](service-fabric-cluster-security-update-certs-azure.md)
* Simplifique o gerenciamento de certificados [Alterando o cluster da impressão digital do certificado para o nome comum](service-fabric-cluster-change-cert-thumbprint-to-cn.md)

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png

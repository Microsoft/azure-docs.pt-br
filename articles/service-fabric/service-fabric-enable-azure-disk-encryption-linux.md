---
title: Habilitar a criptografia de disco para clusters do Linux
description: Este artigo descreve como habilitar a criptografia de disco para nós de Cluster Service Fabric do Azure no Linux usando Azure Resource Manager e Azure Key Vault.
ms.topic: article
ms.date: 03/22/2019
ms.openlocfilehash: c600d822d20b0e5a0ca613935b1dfa4be838fcec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "78252827"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-linux"></a>Habilitar a criptografia de disco para nós de Cluster Service Fabric do Azure no Linux 
> [!div class="op_single_selector"]
> * [Criptografia de Disco para Linux](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Criptografia de Disco para Windows](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

Neste tutorial, você aprenderá a habilitar a criptografia de disco no Azure Service Fabric nós de cluster no Linux. Você precisará seguir estas etapas para cada um dos tipos de nó e conjuntos de dimensionamento de máquinas virtuais. Para criptografar os nós, usaremos o recurso de Azure Disk Encryption em conjuntos de dimensionamento de máquinas virtuais.

O guia aborda os seguintes tópicos:

* Conceitos principais a serem considerados ao habilitar a criptografia de disco em Service Fabric conjuntos de dimensionamento de máquinas virtuais de cluster no Linux.
* Etapas a serem seguidas antes de habilitar a criptografia de disco em Service Fabric nós de cluster no Linux.
* Etapas a serem seguidas para habilitar a criptografia de disco em Service Fabric nós de cluster no Linux.



[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

 **Registro automático**

A visualização de criptografia de disco para o conjunto de dimensionamento de máquinas virtuais requer o auto-registro. Use as seguintes etapas:

1. Execute o comando a seguir: 
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Aguarde cerca de 10 minutos até que o status leia *registrado*. Você pode verificar o status executando o seguinte comando:
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Cofre da Chave do Azure**

1. Crie um cofre de chaves na mesma assinatura e região como conjunto de dimensionamento. Em seguida, selecione a política de acesso **EnabledForDiskEncryption** no cofre de chaves usando seu cmdlet do PowerShell. Você também pode definir a política usando a interface do usuário do Key Vault no portal do Azure com o seguinte comando:
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. Instale a versão mais recente do [CLI do Azure](/cli/azure/install-azure-cli), que tem os novos comandos de criptografia.

3. Instale a versão mais recente do [SDK do Azure da versão Azure PowerShell](https://github.com/Azure/azure-powershell/releases) . Veja a seguir o conjunto de dimensionamento de máquinas virtuais Azure Disk Encryption cmdlets para habilitar ([set](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) a criptografia, recuperar ([Get](/powershell/module/az.compute/get-azvmssvmdiskencryption)) o status de criptografia e remover ([desabilitar](/powershell/module/az.compute/disable-azvmssdiskencryption)) a criptografia na instância do conjunto de dimensionamento.


| Comando | Versão |  Fonte  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 ou posterior | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 ou posterior | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 ou posterior | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 ou posterior | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 ou posterior | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 ou posterior | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Cenários com suporte para criptografia de disco
* A criptografia para conjuntos de dimensionamento de máquinas virtuais tem suporte apenas para conjuntos de dimensionamento criados com discos gerenciados. Não é suportado para conjuntos de escala de discos nativos (ou não gerenciados).
* Há suporte para criptografia e desabilitação de criptografia para volumes do sistema operacional e de dados em conjuntos de dimensionamento de máquinas virtuais no Linux. 
* As operações de refazer imagem e atualização da VM (máquina virtual) para conjuntos de dimensionamento de máquinas virtuais não têm suporte na visualização atual.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>Criar um novo cluster e habilitar a criptografia de disco

Use os comandos a seguir para criar um cluster e habilitar a criptografia de disco usando um modelo de Azure Resource Manager e um certificado autoassinado.

### <a name="sign-in-to-azure"></a>Entrar no Azure  

Entre com os seguintes comandos:

```powershell

Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>Usar um modelo personalizado que você já tem 

Se você precisar criar um modelo personalizado, é altamente recomendável usar um dos modelos na página de exemplos do [modelo de criação de cluster do Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) . 

Se você já tiver um modelo personalizado, verifique se todos os três parâmetros relacionados ao certificado no modelo e no arquivo de parâmetro são nomeados da seguinte maneira. Verifique também se os valores são nulos da seguinte maneira:

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```

Como apenas a criptografia de disco de dados tem suporte para conjuntos de dimensionamento de máquinas virtuais no Linux, você deve adicionar um disco de dados usando um modelo do Resource Manager. Atualize seu modelo para o provisionamento de disco de dados da seguinte maneira:

```Json
   
   "storageProfile": { 
            "imageReference": { 
              "publisher": "[parameters('vmImagePublisher')]", 
              "offer": "[parameters('vmImageOffer')]", 
              "sku": "[parameters('vmImageSku')]", 
              "version": "[parameters('vmImageVersion')]" 
            }, 
            "osDisk": { 
              "caching": "ReadOnly", 
              "createOption": "FromImage", 
              "managedDisk": { 
                "storageAccountType": "[parameters('storageAccountType')]" 
              } 
           }, 
                "dataDisks": [ 
                { 
                    "diskSizeGB": 1023, 
                    "lun": 0, 
                    "createOption": "Empty" 
   
```
 

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\templates\templateparam.json"
$templateFilePath="c:\templates\template.json"


New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```

Aqui está o comando equivalente da CLI. Altere os valores nas instruções Declare para os valores apropriados. A CLI dá suporte a todos os outros parâmetros que o comando anterior do PowerShell suporta.

```azurecli
declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath

```

### <a name="mount-a-data-disk-to-a-linux-instance"></a>Montar um disco de dados para uma instância do Linux
Antes de continuar com a criptografia em um conjunto de dimensionamento de máquinas virtuais, verifique se o disco de dados adicionado está montado corretamente. Entre na VM do cluster do Linux e execute o comando **LSBLK** . A saída deve mostrar que o disco de dados foi adicionado na coluna de **ponto de montagem** .


### <a name="deploy-application-to-a-service-fabric-cluster-in-linux"></a>Implantar o aplicativo em um Cluster Service Fabric no Linux
Para implantar um aplicativo em seu cluster, siga as etapas e orientações em [início rápido: implantar contêineres do Linux para Service Fabric](service-fabric-quickstart-containers-linux.md).


### <a name="enable-disk-encryption-for-the-virtual-machine-scale-sets-created-previously"></a>Habilitar a criptografia de disco para os conjuntos de dimensionamento de máquinas virtuais criados anteriormente
Para habilitar a criptografia de disco para os conjuntos de dimensionamento de máquinas virtuais criados por meio das etapas anteriores, execute os seguintes comandos:
 
```powershell
$VmssName = "nt1vm"
$vaultName = "mykeyvault"
$resourceGroupName = "mycluster"
$KeyVault = Get-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName
$DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
$KeyVaultResourceId = $KeyVault.ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType All

```

```azurecli

az vmss encryption enable -g <resourceGroupName> -n <VMSS name> --disk-encryption-keyvault <KeyVaultResourceId>

```

### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-linux"></a>Validar se a criptografia de disco está habilitada para um conjunto de dimensionamento de máquinas virtuais no Linux
Para obter o status de um conjunto de dimensionamento de máquinas virtuais inteiro ou de qualquer instância em um conjunto de dimensionamento, execute os comandos a seguir.
Além disso, você pode entrar na VM do cluster do Linux e executar o comando **LSBLK** . A saída deve mostrar o disco de dados adicionado na coluna de **ponto de montagem** e a coluna de **tipo** deve ler *cript*.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Desabilitar a criptografia de disco para um conjunto de dimensionamento de máquinas virtuais em um Cluster Service Fabric
Desabilite a criptografia de disco para um conjunto de dimensionamento de máquinas virtuais executando os comandos a seguir. Observe que desabilitar a criptografia de disco se aplica a todo o conjunto de dimensionamento de máquinas virtuais e não a uma instância individual.

```powershell
$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli
az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Próximas etapas
Neste ponto, você deve ter um cluster seguro e saber como habilitar e desabilitar a criptografia de disco para Service Fabric nós de cluster e conjuntos de dimensionamento de máquinas virtuais. Para obter diretrizes semelhantes sobre Service Fabric nós de cluster no Linux, consulte [criptografia de disco para Windows](service-fabric-enable-azure-disk-encryption-windows.md). 

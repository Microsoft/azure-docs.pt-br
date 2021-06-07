---
title: Habilitar a criptografia de disco para clusters do Windows
description: Este artigo descreve como habilitar a criptografia de disco para nós de Cluster Service Fabric do Azure usando Azure Key Vault no Azure Resource Manager.
ms.topic: article
ms.date: 03/22/2019
ms.openlocfilehash: b08cdb63aa6f334c5a6f7c230b1624d232206c3b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "78251813"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-windows"></a>Habilitar a criptografia de disco para nós de Cluster Service Fabric do Azure no Windows 
> [!div class="op_single_selector"]
> * [Criptografia de Disco para Windows](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Criptografia de Disco para Linux](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

Neste tutorial, você aprenderá a habilitar a criptografia de disco em nós de Cluster Service Fabric no Windows. Você precisará seguir estas etapas para cada um dos tipos de nó e conjuntos de dimensionamento de máquinas virtuais. Para criptografar os nós, usaremos o recurso de Azure Disk Encryption em conjuntos de dimensionamento de máquinas virtuais.

O guia aborda os seguintes tópicos:

* Conceitos principais a serem considerados ao habilitar a criptografia de disco em conjuntos de dimensionamento de máquinas virtuais de Cluster Service Fabric no Windows.
* Etapas a serem seguidas antes de habilitar a criptografia de disco em Service Fabric nós de cluster no Windows.
* Etapas a serem seguidas para habilitar a criptografia de disco em nós de Cluster Service Fabric no Windows.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

**Registro automático** 

A visualização de criptografia de disco para o conjunto de dimensionamento de máquinas virtuais requer o auto-registro. Use as seguintes etapas: 

1. Primeiro, execute o seguinte comando:
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Aguarde cerca de 10 minutos até que o status leia *registrado*. Você pode verificar o status executando o seguinte comando: 
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure Key Vault** 

1. Crie um cofre de chaves na mesma assinatura e região que o conjunto de dimensionamento e, em seguida, selecione a política de acesso **EnabledForDiskEncryption** no cofre de chaves usando seu cmdlet do PowerShell. Você também pode definir a política usando a interface do usuário do Key Vault no portal do Azure com o seguinte comando:
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
* Há suporte para criptografia em volumes do sistema operacional e de dados em conjuntos de dimensionamento de máquinas virtuais no Windows. Também há suporte para desabilitar a criptografia em volumes do sistema operacional e de dados para conjuntos de dimensionamento de máquinas virtuais no Windows.
* As operações de reimagem e atualização de máquina virtual para conjuntos de dimensionamento de máquinas virtuais não têm suporte na visualização atual.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>Criar um novo cluster e habilitar a criptografia de disco

Use os comandos a seguir para criar um cluster e habilitar a criptografia de disco usando um modelo do Azure Resource Manager e um certificado autoassinado.

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

Se você precisar criar um modelo personalizado para atender às suas necessidades, é altamente recomendável que você comece com um dos modelos que estão disponíveis na página de [exemplos do modelo de criação de cluster do Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) . Para [Personalizar a seção modelo de cluster][customize-your-cluster-template] , consulte as diretrizes a seguir.

Se você já tiver um modelo personalizado, verifique se todos os três parâmetros relacionados ao certificado no modelo e no arquivo de parâmetro são nomeados da seguinte maneira e se os valores são nulos da seguinte maneira:

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

### <a name="deploy-an-application-to-a-service-fabric-cluster-in-windows"></a>Implantar um aplicativo em um Cluster Service Fabric no Windows
Para implantar um aplicativo em seu cluster, siga as etapas e orientações em [implantar e remover aplicativos usando o PowerShell](service-fabric-deploy-remove-applications.md).


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


### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-windows"></a>Validar se a criptografia de disco está habilitada para um conjunto de dimensionamento de máquinas virtuais no Windows
Obtenha o status de um conjunto de dimensionamento de máquinas virtuais inteiro ou de qualquer instância em um conjunto de dimensionamento executando os comandos a seguir.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


Além disso, você pode entrar no conjunto de dimensionamento de máquinas virtuais e verificar se as unidades estão criptografadas.

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
Neste ponto, você deve ter um cluster seguro e saber como habilitar e desabilitar a criptografia de disco para Service Fabric nós de cluster e conjuntos de dimensionamento de máquinas virtuais. Para obter diretrizes semelhantes sobre Service Fabric nós de cluster no Linux, consulte [criptografia de disco para Linux](service-fabric-enable-azure-disk-encryption-linux.md).

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template

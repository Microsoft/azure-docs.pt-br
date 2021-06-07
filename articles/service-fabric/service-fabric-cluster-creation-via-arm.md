---
title: Criar um cluster de Service Fabric do Azure
description: Saiba como configurar um cluster do Service Fabric seguro no Azure usando o Azure Resource Manager.  Você pode criar um cluster usando um modelo padrão ou seu próprio modelo de cluster.
ms.topic: conceptual
ms.date: 08/16/2018
ms.openlocfilehash: d4daa75fd9383bc19da1b09104ebddff5712c5b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791804"
---
# <a name="create-a-service-fabric-cluster-using-azure-resource-manager"></a>Criar um cluster do Service Fabric usando o Azure Resource Manager 
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure portal](service-fabric-cluster-creation-via-portal.md)
>
>

Um [cluster do Azure Service Fabric](service-fabric-deploy-anywhere.md) é um conjunto conectado por rede de máquinas virtuais no qual os microsserviços são implantados e gerenciados.  Um cluster do Service Fabric em execução no Azure é um recurso do Azure e é implantado usando o Azure Resource Manager. Este artigo descreve como implantar um cluster seguro do Service Fabric no Azure usando o Gerenciador de Recursos. Você pode usar um modelo de cluster padrão ou um modelo personalizado.  Se você ainda não tiver um modelo personalizado, poderá [aprender como criar um](service-fabric-cluster-creation-create-template.md).

O tipo de segurança escolhido para proteger o cluster (ou seja,: Windows Identity, X509 etc.) deve ser especificado para a criação inicial do cluster e não pode ser alterado depois disso. Antes de configurar um cluster, leia [Cenários de segurança de cluster do Service Fabric][service-fabric-cluster-security]. No Azure, o Service Fabric usa o certificado x509 para proteger o cluster e os pontos de extremidade, autenticar clientes e criptografar dados. O Azure Active Directory também é recomendado para proteger o acesso a pontos de extremidade de gerenciamento. Para mais informações, leia [Configurar o Microsoft Azure AD para autenticar clientes](service-fabric-cluster-creation-setup-aad.md).

Antes de implantar um cluster de produção para executar cargas de trabalho de produção, recomendamos que você primeiro leia a [lista de verificação de preparação para produção](service-fabric-production-readiness-checklist.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos 
Neste artigo, use os módulos da CLI do Azure ou PowerShell do RM do Service Fabric para implantar um cluster:

* [Azure PowerShell 4.1 e acima][azure-powershell]
* [CLI do Azure versão 2.0 e acima][azure-CLI]

Você pode encontrar a documentação de referência para os módulos do Service Fabric aqui:
* [Az.ServiceFabric](/powershell/module/az.servicefabric)
* [módulo de CLI az SF](/cli/azure/sf)

### <a name="sign-in-to-azure"></a>Entrar no Azure

Antes de executar qualquer um dos comandos neste artigo, primeiro entre no Azure.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subscriptionId>
```

```azurecli
az login
az account set --subscription $subscriptionId
```

## <a name="create-a-new-cluster-using-a-system-generated-self-signed-certificate"></a>Criar um novo cluster usando um certificado autoassinado gerado pelo sistema

Use os comandos a seguir para criar um cluster protegido com um certificado autoassinado gerado pelo sistema. Este comando configura um certificado de cluster primário que é usado para segurança do cluster e para configurar o acesso de administrador para executar operações de gerenciamento usando esse certificado.  Os certificados autoassinados são úteis para proteger os clusters de teste.  Os clusters de produção devem ser protegidos com um certificado de uma autoridade de certificação (CA).

### <a name="use-the-default-cluster-template-that-ships-in-the-module"></a>Use o modelo de cluster padrão que acompanha o módulo

Use o seguinte comando para criar um cluster rapidamente especificando os parâmetros mínimos e usando o modelo padrão.

O modelo usado está disponível nos [exemplos de modelo do Service Fabric do Azure: modelo do Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) e [modelo do Ubuntu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

O comando a seguir pode criar qualquer um dos clusters Windows ou Linux, você precisa especificar o sistema operacional adequadamente. Os comandos do PowerShell / CLI também exibem o certificado no *CertificateOutputFolder* especificado; no entanto, verifique se a pasta de certificados já foi criada. O comando aceita outros parâmetros, como VM SKU.

> [!NOTE]
> O comando do PowerShell a seguir funciona apenas com o `Az` módulo Azure PowerShell. Para verificar a versão atual do Azure Resource Manager versão do PowerShell, execute o seguinte comando do PowerShell "Get-Module AZ". Siga [este link](/powershell/azure/install-Az-ps) para atualizar sua versão do PowerShell do Azure Resource Manager. 
>
>

Implantar o cluster usando o PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password123!@#" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword="Password4321!@#" | ConvertTo-SecureString -AsPlainText -Force
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"
$certOutputFolder="c:\certificates"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -OS $os -VmPassword $vmpassword -VmUserName $vmuser
```

Implantar o cluster usando a CLI do Azure:

```azurecli
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare CertSubjectName="mylinux.westus.cloudapp.azure.com"
declare vmpassword="Password!1"
declare certpassword="Password!4321"
declare vmuser="myadmin"
declare vmOs="UbuntuServer1604"
declare certOutputFolder="c:\certificates"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certpassword  \
    --vault-name $vaultName --vault-resource-group $resourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath --vm-os $vmOs  \
    --vm-password $vmpassword --vm-user-name $vmuser
```

### <a name="use-your-own-custom-template"></a>Use seu próprio modelo personalizado

Se você precisar criar um modelo personalizado para atender às suas necessidades, é altamente recomendável que você comece com um dos modelos que estão disponíveis nos exemplos de [modelo do Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Saiba como [personalizar o modelo de cluster][customize-your-cluster-template].

Se você já tiver um modelo personalizado, verifique novamente se todos os três parâmetros relacionados ao certificado no modelo e no arquivo de parâmetro são nomeados da seguinte maneira e se os valores são nulos da maneira a seguir:

```json
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

Implantar o cluster usando o PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Implantar o cluster usando a CLI do Azure:

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

## <a name="create-a-new-cluster-using-your-own-x509-certificate"></a>Criar um novo cluster usando seu próprio certificado X.509

Use o seguinte comando para criar o cluster, se você tiver um certificado que deseja usar para proteger o cluster.

Caso esse seja um certificado assinado pela autoridade de certificação que você acabará usando para outras finalidades também, é recomendável que você forneça um grupo de recursos distintos especificamente para seu cofre de chaves. Recomendamos que você coloque o cofre de chaves em seu próprio grupo de recursos. Essa ação permite que você remova os grupos de recursos de computação e armazenamento, incluindo o grupo de recursos que contém o cluster do Service Fabric sem perder suas chaves e seus segredos. **O grupo de recursos que contém o cofre de chaves *deve estar na mesma região* que o cluster que o está usando.**

### <a name="use-the-default-five-node-one-node-type-template-that-ships-in-the-module"></a>Use o modelo do tipo cinco nós, um nó padrão que acompanha o módulo
O modelo usado está disponível nos [exemplos do Azure: modelo do Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) e [modelo do Ubuntu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Implantar o cluster usando o PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword=("Password!4321" | ConvertTo-SecureString -AsPlainText -Force) 
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile C:\MyCertificates\chackocertificate3.pfx -CertificatePassword $certPassword -OS $os -VmPassword $vmpassword -VmUserName $vmuser 
```

Implantar o cluster usando a CLI do Azure:

```azurecli
declare vmPassword="Password!1"
declare certPassword="Password!1"
declare vmUser="myadmin"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare certificate-file="c:\certificates\mycert.pem"
declare vmOs="UbuntuServer1604"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $certPassword  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --vm-os vmOs \
    --vm-password $vmPassword --vm-user-name $vmUser
```

### <a name="use-your-own-custom-cluster-template"></a>Use seu próprio modelo de cluster personalizado
Se você precisar criar um modelo personalizado para atender às suas necessidades, é altamente recomendável que você comece com um dos modelos que estão disponíveis nos exemplos de [modelo do Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Saiba como [personalizar o modelo de cluster][customize-your-cluster-template].

Se você já tem um modelo personalizado, certifique-se de verificar novamente se todos os três parâmetros relacionados ao certificado no modelo e no arquivo de parâmetro são nomeados da seguinte maneira e se os valores são nulos da maneira a seguir.

```json
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

Implantar o cluster usando o PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$os="WindowsServer2016DatacenterwithContainers"
$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"
$certificateFile="C:\MyCertificates\chackonewcertificate3.pem"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -TemplateFile $templateFilePath -ParameterFile $parameterFilePath -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile $certificateFile -CertificatePassword $certPassword
```

Implantar o cluster usando a CLI do Azure:

```azurecli
declare certPassword="Password!1"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $password  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

### <a name="use-a-pointer-to-a-secret-uploaded-into-a-key-vault"></a>Usar um ponteiro para um segredo carregado em um cofre de chaves

Para usar um cofre de chaves existente, o cofre de chaves deve ser [habilitado para implantação](../key-vault/general/manage-with-cli2.md#bkmk_KVperCLI) para permitir que o provedor de recursos de computação obtenha certificados dele e instale-os em nós de cluster.

Implantar o cluster usando o PowerShell:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -SecretIdentifier $secretID -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Implantar o cluster usando a CLI do Azure:

```azurecli
declare $resourceGroupName = "testRG"
declare $parameterFilePath="c:\mytemplates\mytemplate.json"
declare $templateFilePath="c:\mytemplates\mytemplateparm.json"
declare $secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --secret-identifier $secretID  \
    --template-file $templateFilePath --parameter-file $parameterFilePath 
```

## <a name="next-steps"></a>Próximas etapas
Neste ponto, você tem um cluster seguro em execução no Azure. Em seguida, [conecte-se ao cluster](service-fabric-connect-to-secure-cluster.md) e saiba como [gerenciar segredos do aplicativo](service-fabric-application-secret-management.md).

Para obter a sintaxe JSON e as propriedades a serem usadas em um modelo, confira a referência de modelo [Microsoft.ServiceFabric/clusters](/azure/templates/microsoft.servicefabric/clusters).

<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-Az-ps
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[customize-your-cluster-template]: service-fabric-cluster-creation-create-template.md

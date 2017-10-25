---
title: Atualizar um cluster do Azure Service Fabric de um modelo | Microsoft Docs
description: "Este artigo descreve como configurar um cluster seguro do Service Fabric no Azure usando o Azure Resource Manager, o Azure Key Vault e o Azure Active Directory (Azure AD) para autenticação do cliente."
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2017
ms.author: chackdan
ms.openlocfilehash: 47152d05eb7e31e7fe1f35e33a10fe8e903e21e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-service-fabric-cluster-by-using-azure-resource-manager"></a>Criar um cluster do Service Fabric usando o Azure Resource Manager
> [!div class="op_single_selector"]
> * [Gerenciador de Recursos do Azure](service-fabric-cluster-creation-via-arm.md)
> * [Portal do Azure](service-fabric-cluster-creation-via-portal.md)
>
>

Este guia passo a passo orienta você pela configuração de um cluster do Azure Service Fabric seguro no Azure usando o Azure Resource Manager. Sabemos que o artigo é longo. No entanto, a menos que você já esteja totalmente familiarizado com o conteúdo, não deixe de seguir cada etapa com cuidado.

O guia aborda os seguintes procedimentos:

* Como configurar um Azure Key Vault para carregar certificados de segurança de aplicativos e cluster
* Como criar um cluster seguro no Azure usando o Azure Resource Manager
* Como autenticar usuários usando o Azure AD (Azure Active Directory) para gerenciamento de cluster

Um cluster seguro é um cluster que impede o acesso não autorizado às operações de gerenciamento. Isso inclui implantação, atualização e exclusão de aplicativos, serviços e dados neles contidos. Um cluster não seguro é um cluster ao qual qualquer pessoa pode se conectar a qualquer momento e realizar operações de gerenciamento. Embora seja possível criar um cluster não seguro, recomendamos que você crie um cluster seguro desde o início. Um cluster não seguro não pode ser protegido posteriormente; um novo cluster deverá ser criado.

O conceito para criar clusters seguros é o mesmo, sejam clusters do Linux ou do Windows. Para obter mais informações e scripts auxiliares para criar clusters do Linux seguras, consulte [Criando clusters seguros no Linux](#secure-linux-clusters).

## <a name="sign-in-to-your-azure-account"></a>Entre na sua conta do Azure
Este guia usa o [Azure PowerShell][azure-powershell]. Ao iniciar uma nova sessão do PowerShell, entre em sua conta do Azure e selecione sua assinatura antes de executar comandos do Azure.

Entre na sua conta do Azure:

```powershell
Login-AzureRmAccount
```

Selecione sua assinatura:

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="set-up-a-key-vault"></a>Configurar um cofre de chaves
Esta seção trata da criação de um cofre de chaves para um cluster do Service Fabric no Azure e para aplicativos do Service Fabric. Para obter um guia completo sobre o Azure Key Vault, veja o [Guia de introdução ao Key Vault][key-vault-get-started].

O Service Fabric usa certificados x.509 para proteger um cluster e fornecer recursos de segurança do aplicativo. O Key Vault é usado para gerenciar certificados de clusters do Service Fabric no Azure. Quando um cluster é implantado no Azure, o provedor de recursos do Azure responsável pela criação de clusters do Service Fabric recebe certificados do Key Vault e os instala nas VMs do cluster.

O diagrama a seguir ilustra o relacionamento entre o Azure Key Vault, um cluster do Service Fabric e o provedor de recursos do Azure que usa certificados armazenados em um cofre de chaves quando ele cria um cluster:

![Diagrama da instalação do certificado][cluster-security-cert-installation]

### <a name="create-a-resource-group"></a>Criar um grupo de recursos
A primeira etapa é criar um grupo de recursos especificamente para o cofre de chaves. Recomendamos que você coloque o cofre de chaves em seu próprio grupo de recursos. Essa ação permite que você remova os grupos de recursos de computação e armazenamento, incluindo o grupo de recursos que contém o cluster do Service Fabric sem perder suas chaves e seus segredos. O grupo de recursos que contém o cofre de chaves _tem que estar na mesma região_ que o cluster que está sendo usado.

Se você planeja implantar clusters em várias regiões, sugerimos que você nomeie o grupo de recursos e o cofre de chaves de forma a indicar a qual região eles pertencem.  

```powershell

    New-AzureRmResourceGroup -Name westus-mykeyvault -Location 'West US'
```
O resultado deve ser assim:

```powershell

    WARNING: The output object type of this cmdlet is going to be modified in a future release.

    ResourceGroupName : westus-mykeyvault
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<guid>/resourceGroups/westus-mykeyvault

```
<a id="new-key-vault"></a>

### <a name="create-a-key-vault-in-the-new-resource-group"></a>Crie um cofre de chaves no novo grupo de recursos
O cofre de chaves _tem que estar habilitado para implantação_ para permitir que o provedor de recursos de computação obtenha certificados e os instale em instâncias de máquina virtual:

```powershell

    New-AzureRmKeyVault -VaultName 'mywestusvault' -ResourceGroupName 'westus-mykeyvault' -Location 'West US' -EnabledForDeployment

```

O resultado deve ser assim:

```powershell

    Vault Name                       : mywestusvault
    Resource Group Name              : westus-mykeyvault
    Location                         : West US
    Resource ID                      : /subscriptions/<guid>/resourceGroups/westus-mykeyvault/providers/Microsoft.KeyVault/vaults/mywestusvault
    Vault URI                        : https://mywestusvault.vault.azure.net
    Tenant ID                        : <guid>
    SKU                              : Standard
    Enabled For Deployment?          : False
    Enabled For Template Deployment? : False
    Enabled For Disk Encryption?     : False
    Access Policies                  :
                                       Tenant ID                :    <guid>
                                       Object ID                :    <guid>
                                       Application ID           :
                                       Display Name             :    
                                       Permissions to Keys      :    get, create, delete, list, update, import, backup, restore
                                       Permissions to Secrets   :    all


    Tags                             :
```
<a id="existing-key-vault"></a>

## <a name="use-an-existing-key-vault"></a>Usar um cofre de chave existente

Para usar um cofre de chaves existente, você _tem habilitá-lo para implantação_ a fim de permitir que o provedor de recursos de computação obtenha certificados e os instale em nós de cluster:

```powershell

Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

```

<a id="add-certificate-to-key-vault"></a>

## <a name="add-certificates-to-your-key-vault"></a>Adicionar certificados ao cofre de chaves

Os certificados são usados no Service Fabric para fornecer autenticação e criptografia para proteger vários aspectos de um cluster e de seus aplicativos. Para obter mais informações sobre como os certificados são usados no Service Fabric, consulte [Cenários de segurança do cluster do Service Fabric][service-fabric-cluster-security].

### <a name="cluster-and-server-certificate-required"></a>Certificado de cluster e de servidor (necessário)
Esse certificado é necessário para proteger um cluster e impedir o acesso não autorizado a ele. Ele fornece segurança de cluster de duas maneiras:

* Autenticação do cluster: autentica a comunicação de nó para nó para a federação de cluster. Somente os nós que podem provar sua identidade com esse certificado podem ingressar no cluster.
* Autenticação de servidor: autentica os pontos de extremidade de gerenciamento de cluster para um cliente de gerenciamento, para que o gerenciamento do cliente saiba que está se comunicando com o cluster real. Esse certificado também fornece um SSL para a API de gerenciamento de HTTPS e para o Service Fabric Explorer sobre HTTPS.

Para servir a essas finalidades, o certificado deverá atender a estes requisitos:

* O certificado deve conter uma chave privada.
* O certificado deve ser criado para troca de chaves, que deve ser exportável para um arquivo Troca de Informações Pessoais (.pfx).
* O nome da referência do certificado deve corresponder ao domínio usado para acessar o cluster do Service Fabric. Essa correspondência é necessária para fornecer um SSL para os pontos de extremidade de gerenciamento de HTTPS e o Service Fabric Explorer do cluster. Você não pode obter um certificado SSL de uma AC (autoridade de certificação) para o domínio cloudapp.azure.com. Você deve obter um nome de domínio personalizado para seu cluster. Quando você solicitar um certificado de uma autoridade de certificação, o nome de assunto do certificado deve corresponder ao nome de domínio personalizado usado para seu cluster.

### <a name="application-certificates-optional"></a>Certificados de aplicativo (opcionais)
Qualquer número de certificados adicionais pode ser instalado em um cluster para fins de segurança do aplicativo. Antes de criar o cluster, considere os cenários de segurança de aplicativos que exigem um certificado a ser instalado em nós, como:

* Criptografia e descriptografia de valores de configuração de aplicativo.
* Criptografia de dados entre nós durante a replicação.

### <a name="formatting-certificates-for-azure-resource-provider-use"></a>Formatação de certificados para uso do provedor de recursos do Azure
Você pode adicionar e usar arquivos de chave privada (.pfx) diretamente pelo cofre de chaves. No entanto, o provedor de recursos de computação do Azure requer que as chaves sejam armazenadas em um formato especial JSON (JavaScript Object Notation). O formato inclui o arquivo .pfx como uma cadeia de caracteres em codificação de base 64 e a senha da chave privada. Para acomodar esses requisitos, as chaves deverão ser colocadas em uma cadeia de caracteres JSON e então armazenadas como “segredos” no cofre de chaves.

Para facilitar esse processo, um [módulo do PowerShell está disponível no GitHub][service-fabric-rp-helpers]. Para usar o módulo, faça o seguinte:

1. Baixe todo o conteúdo do repositório em um diretório local.
2. Vá para o diretório local.
2. Importe o módulo ServiceFabricRPHelpers na janela do PowerShell:

```powershell

 Import-Module "C:\..\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"

```

O comando `Invoke-AddCertToKeyVault` neste módulo do PowerShell formata automaticamente uma chave privada do certificado em uma cadeia de caracteres JSON e a carrega no cofre de chaves. Use o comando para adicionar o certificado do cluster e todos os certificados adicionais de aplicativos ao cofre de chaves. Repita esta etapa para todos os certificados adicionais que deseja instalar em seu cluster.

#### <a name="uploading-an-existing-certificate"></a>Carregando um certificado existente

```powershell

 Invoke-AddCertToKeyVault -SubscriptionId <guid> -ResourceGroupName westus-mykeyvault -Location "West US" -VaultName mywestusvault -CertificateName mycert -Password "<password>" -UseExistingCertificate -ExistingPfxFilePath "C:\path\to\mycertkey.pfx"

```

Se você receber um erro como o mostrado aqui, provavelmente tem um conflito de URL de recurso. Para resolver o conflito, altere o nome do cofre de chaves.

```
Set-AzureKeyVaultSecret : The remote name could not be resolved: 'westuskv.vault.azure.net'
At C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1:440 char:11
+ $secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $Certif ...
+           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzureKeyVaultSecret], WebException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.KeyVault.SetAzureKeyVaultSecret

```

Depois que o conflito for resolvido, o resultado deverá ser assim:

```

    Switching context to SubscriptionId <guid>
    Ensuring ResourceGroup westus-mykeyvault in West US
    WARNING: The output object type of this cmdlet is going to be modified in a future release.
    Using existing value mywestusvault in West US
    Reading pfx file from C:\path\to\key.pfx
    Writing secret to mywestusvault in vault mywestusvault


Name  : CertificateThumbprint
Value : E21DBC64B183B5BF355C34C46E03409FEEAEF58D

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/westus-mykeyvault/providers/Microsoft.KeyVault/vaults/mywestusvault

Name  : CertificateURL
Value : https://mywestusvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47

```

>[!NOTE]
>Você precisa das três cadeias de caracteres anteriores, CertificateThumbprint, SourceVault e CertificateURL, para configurar um cluster do Service Fabric seguro e obter os certificados de aplicativo que estejam sendo usados na segurança do aplicativo. Se você não salvar as cadeias de caracteres, poderá ter dificuldade em recuperá-los consultando o cofre da chave mais tarde.

<a id="add-self-signed-certificate-to-key-vault"></a>

#### <a name="creating-a-self-signed-certificate-and-uploading-it-to-the-key-vault"></a>Criando um certificado autoassinado e carregando no cofre de chaves

Se você já carregou os certificados para o cofre da chaves, ignore esta etapa. Esta etapa é para gerar um novo certificado autoassinado e carregá-lo no cofre de chaves. Depois de alterar os parâmetros no script a seguir e executá-lo, você deverá inserir a senha do certificado.  

```powershell

$ResourceGroup = "chackowestuskv"
$VName = "chackokv2"
$SubID = "6c653126-e4ba-42cd-a1dd-f7bf96ae7a47"
$locationRegion = "westus"
$newCertName = "chackotestcertificate1"
$dnsName = "www.mycluster.westus.mydomain.com" #The certificate's subject name must match the domain used to access the Service Fabric cluster.
$localCertPath = "C:\MyCertificates" # location where you want the .PFX to be stored

 Invoke-AddCertToKeyVault -SubscriptionId $SubID -ResourceGroupName $ResourceGroup -Location $locationRegion -VaultName $VName -CertificateName $newCertName -CreateSelfSignedCertificate -DnsName $dnsName -OutputPath $localCertPath

```

Se você receber um erro como o mostrado aqui, provavelmente tem um conflito de URL de recurso. Para resolver o conflito, altere o nome do cofre de chaves, o nome RG e assim por diante.

```
Set-AzureKeyVaultSecret : The remote name could not be resolved: 'westuskv.vault.azure.net'
At C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1:440 char:11
+ $secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $Certif ...
+           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzureKeyVaultSecret], WebException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.KeyVault.SetAzureKeyVaultSecret

```

Depois que o conflito for resolvido, o resultado deverá ser assim:

```
PS C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers> Invoke-AddCertToKeyVault -SubscriptionId $SubID -ResourceGroupName $ResouceGroup -Location $locationRegion -VaultName $VName -CertificateName $newCertName -Password $certPassword -CreateSelfSignedCertificate -DnsName $dnsName -OutputPath $localCertPath
Switching context to SubscriptionId 6c343126-e4ba-52cd-a1dd-f8bf96ae7a47
Ensuring ResourceGroup chackowestuskv in westus
WARNING: The output object type of this cmdlet will be modified in a future release.
Creating new vault westuskv1 in westus
Creating new self signed certificate at C:\MyCertificates\chackonewcertificate1.pfx
Reading pfx file from C:\MyCertificates\chackonewcertificate1.pfx
Writing secret to chackonewcertificate1 in vault westuskv1


Name  : CertificateThumbprint
Value : 96BB3CC234F9D43C25D4B547sd8DE7B569F413EE

Name  : SourceVault
Value : /subscriptions/6c653126-e4ba-52cd-a1dd-f8bf96ae7a47/resourceGroups/chackowestuskv/providers/Microsoft.KeyVault/vaults/westuskv1

Name  : CertificateURL
Value : https://westuskv1.vault.azure.net:443/secrets/chackonewcertificate1/ee247291e45d405b8c8bbf81782d12bd

```

>[!NOTE]
>Você precisa das três cadeias de caracteres anteriores, CertificateThumbprint, SourceVault e CertificateURL, para configurar um cluster do Service Fabric seguro e obter os certificados de aplicativo que estejam sendo usados na segurança do aplicativo. Se você não salvar as cadeias de caracteres, poderá ter dificuldade em recuperá-los consultando o cofre da chave mais tarde.

 Agora você deve ter os seguintes elementos:

* Grupo de recursos do cofre de chaves.
* O cofre de chaves e sua URL (chamada SourceVault na saída do PowerShell acima).
* O certificado de autenticação de servidor de cluster e sua URL no cofre de chaves.
* Os certificados de aplicativo e as URLs no cofre de chaves.


<a id="add-AAD-for-client"></a>

## <a name="set-up-azure-active-directory-for-client-authentication"></a>Configurar o Azure Active Directory para autenticação de cliente

O Azure AD permite que as organizações (conhecidas como locatários) gerenciem o acesso dos usuários aos aplicativos. Os aplicativos são divididos entre os que têm IU de entrada na Web e os que têm experiência de cliente nativa. Neste artigo, partimos do pressuposto que você já tenha criado um locatário. Se não for o caso, comece lendo [Como obter um locatário do Azure Active Directory][active-directory-howto-tenant].

Os clusters do Service Fabric oferecem vários pontos de entrada para a funcionalidade de gerenciamento, incluindo o [Service Fabric Explorer][service-fabric-visualizing-your-cluster] online o [Visual Studio][service-fabric-manage-application-in-visual-studio]. Como resultado, você criará dois aplicativos do Azure AD para controlar o acesso ao cluster, um aplicativo Web e um aplicativo nativo.

Para simplificar algumas das etapas envolvidas na configuração do Azure AD com um cluster do Service Fabric, criamos um conjunto de scripts do Windows PowerShell.

> [!NOTE]
> Você deve concluir as etapas a seguir para poder criar o cluster. Como os scripts esperam pontos de extremidade e nomes de cluster, os valores devem ser planejados, não os valores que você já tinha criado.

1. [Baixe os scripts][sf-aad-ps-script-download] em seu computador.
2. Clique com o botão direito do mouse no arquivo zip, selecione **Propriedades**, marque a caixa de seleção **Desbloquear** e clique em **Aplicar**.
3. Extraia o arquivo zip.
4. Execute `SetupApplications.ps1` e forneça TenantId, ClusterName e WebApplicationReplyUrl como parâmetros. Por exemplo:

    ```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
    ```

    Você pode encontrar o TenantId executando o comando `Get-AzureSubscription` do PowerShell. A execução desse comando exibe a TenantId para cada assinatura.

    O ClusterName é usado para prefixar os aplicativos do Azure AD criados pelo script. Ele não precisa corresponder exatamente ao nome real do cluster. Serve apenas para tornar mais fácil mapear artefatos do Azure AD para o cluster do Service Fabric com que estão sendo usados.

    WebApplicationReplyUrl é o ponto de extremidade padrão que o Azure AD retorna para seus usuários depois que eles concluem o processo de entrada. Defina esse ponto de extremidade como o ponto de extremidade do Service Fabric Explorer para o seu cluster, que por padrão é:

    https://&lt;cluster_domain&gt;:19080/Explorer

    Você precisará entrar em uma conta que tenha privilégios administrativos para o locatário do Azure AD. Depois de entrar, o script criará aplicativos Web e nativos para representar seu cluster do Service Fabric. Se você observar os aplicativos do locatário no [Portal Clássico do Azure][azure-classic-portal], você verá duas novas entradas:

   * *ClusterName*\_Cluster
   * *ClusterName*\_Cliente

   O script imprimirá o JSON necessário para o modelo do Azure Resource Manager ao criar o cluster na próxima seção. Portanto, convém manter a janela do PowerShell aberta.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Criar um modelo do Resource Manager do cluster do Service Fabric
Nesta seção, as saídas dos comandos anteriores do PowerShell serão usadas em um modelo do Resource Manager de um cluster do Service Fabric.

Os exemplos de modelo do Resource Manager estão disponíveis na [Galeria de modelos de início rápido do Azure no GitHub][azure-quickstart-templates]. Esses modelos podem ser usados como ponto de partida para o modelo de cluster.

### <a name="create-the-resource-manager-template"></a>Criar o modelo do Gerenciador de Recursos
Este guia usa o modelo de exemplo e parâmetros de modelo do [cluster seguro de cinco nós][service-fabric-secure-cluster-5-node-1-nodetype]. Baixe `azuredeploy.json` e `azuredeploy.parameters.json` em seu computador e abra ambos os arquivos em seu editor de texto favorito.

### <a name="add-certificates"></a>Adicionar certificados
Os certificados são adicionados a um modelo do Resource Manager de cluster quando você faz a referência ao Key Vault que contém as chaves de certificado. Recomendamos que você coloque os valores do cofre de chaves em um arquivo de parâmetros do modelo do Resource Manager. Isso faz com que o modelo do Resource Manager seja reutilizável e fique livre de valores específicos de uma implantação.

#### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Adicionar todos os certificados ao conjunto de dimensionamento de máquinas virtuais osProfile
Todos os certificados instalados no cluster devem ser configurados na seção osProfile do recurso de conjunto de dimensionamento (Microsoft.Compute/virtualMachineScaleSets). Essa ação instrui o provedor de recursos para instalar o certificado nas VMs. Essa instalação inclui o certificado do cluster e os certificados de segurança de aplicativo que você planeja usar para seus aplicativos:

```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

#### <a name="configure-the-service-fabric-cluster-certificate"></a>Configurar o certificado do cluster do Service Fabric
O certificado de autenticação do cluster tem que ser configurado tanto no recurso de cluster do Service Fabric (Microsoft.ServiceFabric/clusters) quanto na extensão do Service Fabric para conjuntos de dimensionamento de máquinas virtuais no recurso de conjunto de dimensionamento de máquinas virtuais. Essa disposição permite que o provedor de recursos do Service Fabric o configure para autenticação do cluster e autenticação de servidor para pontos de extremidade de gerenciamento.

##### <a name="virtual-machine-scale-set-resource"></a>Recurso Conjunto de dimensionamento de máquinas virtuais:
```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "thumbprint": "[parameters('clusterCertificateThumbprint')]",
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

##### <a name="service-fabric-resource"></a>Recursos do Service Fabric:
```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
    },
    ...
  }
}
```

### <a name="insert-azure-ad-configuration"></a>Inserir configuração do Azure AD
A configuração do Azure AD que você criou anteriormente pode ser inserida diretamente no modelo do Resource Manager. No entanto, recomendamos que você primeiro extraia os valores em um arquivo de parâmetros para fazer com que o modelo do Resource Manager seja reutilizável e fique livre de valores específicos de uma implantação.

```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStorevalue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

### <a "configure-arm" ></a>Configurar os parâmetros do modelo do Resource Manager
<!--- Loc Comment: It seems that <a "configure-arm" > must be replaced with <a name="configure-arm"></a> since the link seems not to be redirecting correctly --->
Por fim, use os valores de saída dos comandos do cofre de chaves e do PowerShell do Azure AD para preencher o arquivo de parâmetros:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```
Agora você deve ter os seguintes elementos:

* Grupo de recursos do cofre de chaves
  * Cofre de chaves
  * Certificado de autenticação de servidor de cluster
  * Certificado de codificação de dados
* Locatário do Azure Active Directory
  * Aplicativo Azure AD para gerenciamento baseado na Web e no Service Fabric Explorer
  * Aplicativo Azure AD para o gerenciamento de clientes nativos
  * Usuários e suas funções atribuídas
* Modelo do Resource Manager de cluster do Service Fabric
  * Certificados configurados por meio do cofre de chaves
  * Azure Active Directory configurado

O diagrama a seguir ilustra onde a configuração do cofre de chaves e do Azure AD se encaixa em seu modelo do Resource Manager.

![Mapa de dependências do Resource Manager][cluster-security-arm-dependency-map]

## <a name="create-the-cluster"></a>Criar o cluster
Agora você está pronto para criar o cluster usando a [implantação do modelo do Azure Resource Manager][resource-group-template-deploy].

#### <a name="test-it"></a>Testá-lo
Use o seguinte comando do PowerShell para testar o modelo do Resource Manager com um arquivo de parâmetros:

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

#### <a name="deploy-it"></a>Implantá-lo
Se o modelo do Resource Manager passar no teste, use o comando do PowerShell a seguir para implantar seu modelo do Resource Manager com um arquivo de parâmetros:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

<a name="assign-roles"></a>

## <a name="assign-users-to-roles"></a>Atribuir usuários a funções
Depois de criar os aplicativos para representar seu cluster, atribua os usuários às funções com suporte no Service Fabric: somente leitura e administrador. Você pode atribuir as funções usando o [portal clássico do Azure][azure-classic-portal].

1. No portal do Azure, vá para seu locatário e selecione **Aplicativos**.
2. Selecione o aplicativo Web, que tem um nome semelhante a `myTestCluster_Cluster`.
3. Clique na guia **Usuários** .
4. Selecione um usuário para atribuir e clique no botão **Atribuir** na parte inferior da tela.

    ![Botão Atribuir usuários a funções][assign-users-to-roles-button]
5. Selecione a função para atribuir ao usuário.

    ![Caixa de diálogo "Atribuir Usuários"][assign-users-to-roles-dialog]

> [!NOTE]
> Para saber mais sobre as funções no Service Fabric, veja [Controle de acesso baseado em função para clientes do Service Fabric](service-fabric-cluster-security-roles.md).
>
>

 <a name="secure-linux-clusters"></a>
 <!--- Loc Comment: It seems that letter S in cluster was missing, which caused the wrong redirection of the link --->

## <a name="create-secure-clusters-on-linux"></a>Criar clusters seguros no Linux
Para facilitar o processo, fornecemos um [script auxiliar](http://github.com/ChackDan/Service-Fabric/tree/master/Scripts/CertUpload4Linux). Antes de usar esse script auxiliar, verifique se você já tem a CLI (interface de linha de comando) do Azure instalada e se ele está em seu caminho. Certifique-se de que o script tenha permissões para execução ao executar `chmod +x cert_helper.py` após fazer o download. A primeira etapa é entrar na sua conta do Azure usando a CLI com o comando `azure login`. Depois de entrar na sua conta do Azure, use o script auxiliar com seu certificado da AC assinado, como mostra o seguinte comando:

```sh
./cert_helper.py [-h] CERT_TYPE [-ifile INPUT_CERT_FILE] [-sub SUBSCRIPTION_ID] [-rgname RESOURCE_GROUP_NAME] [-kv KEY_VAULT_NAME] [-sname CERTIFICATE_NAME] [-l LOCATION] [-p PASSWORD]
```

O parâmetro -ifile pode usar um arquivo .pfx ou .pem como entrada, com o tipo de certificado (pfx, pem ou ss se for um certificado autoassinado).
O parâmetro -h imprime o texto de ajuda.


Esse comando retorna as três cadeias de caracteres a seguir como a saída:

* SourceVaultID, que é a ID para o novo KeyVault ResourceGroup criado para você
* CertificateUrl para acessar o certificado
* CertificateThumbprint, que é usado para autenticação

O exemplo a seguir mostra como usar o comando:

```sh
./cert_helper.py pfx -sub "fffffff-ffff-ffff-ffff-ffffffffffff"  -rgname "mykvrg" -kv "mykevname" -ifile "/home/test/cert.pfx" -sname "mycert" -l "East US" -p "pfxtest"
```
A execução do comando anterior dá as seguintes três cadeias de caracteres:

```sh
SourceVault: /subscriptions/fffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/mykvrg/providers/Microsoft.KeyVault/vaults/mykvname
CertificateUrl: https://myvault.vault.azure.net/secrets/mycert/00000000000000000000000000000000
CertificateThumbprint: 0xfffffffffffffffffffffffffffffffffffffffff
```

O nome da referência do certificado deve corresponder ao domínio usado para acessar o cluster do Service Fabric. Essa correspondência é necessária para fornecer um SSL para os pontos de extremidade de gerenciamento de HTTPS e o Service Fabric Explorer do cluster. Você não pode obter um certificado SSL de uma AC para o domínio `.cloudapp.azure.com` . Você deve obter um nome de domínio personalizado para seu cluster. Quando você solicitar um certificado de uma autoridade de certificação, o nome de assunto do certificado deve corresponder ao nome de domínio personalizado usado para seu cluster.

Esses nomes de referência são as entradas necessárias para criar um cluster do Service Fabric seguro (sem o Azure AD), conforme descrito em [Configurar parâmetros de modelo do Resource Manager](#configure-arm). Você pode se conectar ao cluster seguro seguindo as instruções para [autenticar o acesso de cliente a um cluster](service-fabric-connect-to-secure-cluster.md). Os clusters do Linux não dão suporte à autenticação do Azure AD. Você pode atribuir funções de administrador e cliente, conforme descrito na seção [Atribuir funções a usuários](#assign-roles). Quando você especifica funções de administrador e de cliente para um cluster do Linux, é necessário fornecer as impressões digitais de certificado para autenticação. Você não fornece o nome da entidade, porque nenhuma validação ou revogação de cadeia está sendo executada.

Se quiser usar um certificado autoassinado para testes, você poderá usar o mesmo script para gerá-lo. Em seguida, você pode carregar o certificado no cofre de chaves fornecendo o sinalizador `ss` em vez do caminho e do nome do certificado. Por exemplo, consulte o seguinte comando para criar e carregar um certificado autoassinado:

```sh
./cert_helper.py ss -rgname "mykvrg" -sub "fffffff-ffff-ffff-ffff-ffffffffffff" -kv "mykevname"   -sname "mycert" -l "East US" -p "selftest" -subj "mytest.eastus.cloudapp.net"
```
Esse comando retorna as mesmas três cadeias de caracteres: SourceVault, CertificateUrl e CertificateThumbprint. Em seguida, você pode usar as cadeias de caracteres para criar um cluster do Linux seguro e um local onde o certificado autoassinado fica armazenado. Você precisa do certificado autoassinado para se conectar ao cluster. Você pode se conectar ao cluster seguro seguindo as instruções para [autenticar o acesso de cliente a um cluster](service-fabric-connect-to-secure-cluster.md).

O nome da referência do certificado deve corresponder ao domínio usado para acessar o cluster do Service Fabric. Essa correspondência é necessária para fornecer um SSL para os pontos de extremidade de gerenciamento de HTTPS e o Service Fabric Explorer do cluster. Você não pode obter um certificado SSL de uma AC para o domínio `.cloudapp.azure.com` . Você deve obter um nome de domínio personalizado para seu cluster. Quando você solicitar um certificado de uma autoridade de certificação, o nome de assunto do certificado deve corresponder ao nome de domínio personalizado usado para seu cluster.

Você pode preencher os parâmetros do script auxiliar no portal do Azure, conforme descrito na seção [criar um cluster no portal do Azure](service-fabric-cluster-creation-via-portal.md#create-cluster-in-the-azure-portal).

## <a name="next-steps"></a>Próximas etapas
Neste ponto, você tem um cluster seguro com o Azure Active Directory fornecendo autenticação de gerenciamento. Em seguida, [conecte-se ao cluster](service-fabric-connect-to-secure-cluster.md) e saiba como [gerenciar segredos do aplicativo](service-fabric-application-secret-management.md).

## <a name="troubleshoot-setting-up-azure-active-directory-for-client-authentication"></a>Solucionar problemas na configuração do Azure Active Directory para autenticação de cliente
Se você tiver um problema durante a configuração do Azure AD para autenticação de cliente, examine as possíveis soluções nesta seção.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>O Service Fabric Explorer solicita que você selecione um certificado
#### <a name="problem"></a>Problema
Depois de entrar no Azure AD no Service Fabric Explorer, o navegador retornará para a home page, mas uma mensagem solicitará que você selecione um certificado.

![Caixa de diálogo de seleção de certificado SFX][sfx-select-certificate-dialog]

#### <a name="reason"></a>Motivo
O usuário não recebeu uma função no aplicativo de cluster do Azure AD. Assim, a autenticação do Azure AD falha no cluster do Service Fabric. O Service Fabric Explorer reverterá para autenticação de certificado.

#### <a name="solution"></a>Solução
Siga as instruções para configurar o Azure AD e atribuir funções de usuário. Além disso, recomendamos que você habilite a "Atribuição de usuário necessária para acessar o aplicativo" da mesma forma feita por `SetupApplications.ps1`.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>A conexão com o PowerShell falha e exibe o seguinte erro: “as credenciais especificadas são inválidas”
#### <a name="problem"></a>Problema
Quando você usa o PowerShell para se conectar ao cluster usando o modo de segurança "AzureActiveDirectory", ao entrar no AD do Azure, a conexão falhará com um erro: "as credenciais especificadas são inválidas."

#### <a name="solution"></a>Solução
Essa solução é igual à anterior.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>O Service Fabric Explorer retorna uma falha quando você entra: "AADSTS50011"
#### <a name="problem"></a>Problema
Ao tentar entrar no Azure AD no Service Fabric Explorer, a página retorna uma falha: “AADSTS50011: a &lt;URL&gt; do endereço de resposta não coincide com os endereços de resposta configurados para o aplicativo: &lt;GUID&gt;”.

![O endereço de resposta SFX não corresponde][sfx-reply-address-not-match]

#### <a name="reason"></a>Motivo
O aplicativo do cluster (Web) que representa o Service Fabric Explorer tenta se autenticar no Azure AD como parte da solicitação que ele fornece à URL de retorno de redirecionamento. Mas a URL não está listada na lista **URL DE RESPOSTA** do aplicativo Azure AD.

#### <a name="solution"></a>Solução
Na guia **Configurar** do aplicativo do cluster (Web), adicione a URL do Service Fabric Explorer à lista **URL DE RESPOSTA** ou substitua um dos itens na lista. Quando terminar, salve a alteração.

![URL de resposta do aplicativo Web][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Conectar o cluster usando a autenticação do Azure AD por meio do PowerShell
Para se conectar ao cluster do Service Fabric, use o seguinte exemplo de comando do PowerShell:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Para saber mais sobre o cmdlet Connect-ServiceFabricCluster, confira [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Posso reutilizar o mesmo locatário do Azure AD em vários clusters?
Sim. Mas lembre-se de adicionar a URL do Service Fabric Explorer ao aplicativo do cluster (Web). Caso contrário, o Service Fabric Explorer não funcionará.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Por que eu ainda preciso de um certificado de servidor quando o Azure AD está habilitado?
FabricClient e FabricGateway realizam autenticação mútua. Durante a autenticação do Azure AD, a integração dele fornece uma identidade de cliente para o servidor e o certificado do servidor é usado para verificar a identidade do servidor. Para saber mais sobre certificados do Service Fabric, confira [Certificados X.509 e Service Fabric][x509-certificates-and-service-fabric].

<!-- Links -->
[azure-powershell]:https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[aad-graph-api-docs]:https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-classic-portal]: https://manage.windowsazure.com
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]: ../active-directory/active-directory-howto-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[azure-quickstart-templates]: https://github.com/Azure/azure-quickstart-templates
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure/azure-quickstart-templates/blob/master/service-fabric-secure-cluster-5-node-1-nodetype/
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-arm-dependency-map.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
[assign-users-to-roles-button]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-button.png
[assign-users-to-roles-dialog]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-via-arm/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-via-arm/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-via-arm/web-application-reply-url.png


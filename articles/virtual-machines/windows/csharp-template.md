---
title: Implantar uma VM usando C# e um modelo do Resource Manager | Microsoft Docs
description: Saiba como usar o C# e um modelo do Resource Manager para implantar uma VM do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: bfba66e8-c923-4df2-900a-0c2643b81240
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: cynthn
ms.openlocfilehash: 50d0d78e9dc0c7f51fcd82dd16eab5a180eae073
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61402176"
---
# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>Implantar uma Máquina Virtual do Azure usando C# e um modelo do Resource Manager

Este artigo mostra como implantar um modelo do Azure Resource Manager usando o C#. O modelo criado implanta uma única máquina virtual que executa o Windows Server em uma nova rede virtual com uma única sub-rede.

Para obter uma descrição detalhada do recurso de máquina virtual, confira [Virtual machines in an Azure Resource Manager template](template-description.md) (Máquinas virtuais em um modelo do Azure Resource Manager). Para saber mais sobre todos os recursos em um modelo, confira [Passo a passo do modelo do Azure Resource Manager](../../azure-resource-manager/resource-manager-template-walkthrough.md).

São necessários cerca de 10 minutos para a conclusão destas etapas.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

Nesta etapa, você verifica se o Visual Studio está instalado e cria um aplicativo de console usado para implantar o modelo.

1. Se você ainda não fez isso, instale o [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Selecione **Desenvolvimento para desktop com o .NET** na página Cargas de trabalho e, em seguida, clique em **Instalar**. No resumo, é possível verificar que as **ferramentas de desenvolvimento do .NET Framework 4 - 4.6** são automaticamente selecionadas. Se o Visual Studio já estiver instalado, você poderá adicionar a carga de trabalho .NET utilizando o Iniciador do Visual Studio.
2. No Visual Studio, clique em **Arquivo** > **Novo** > **Projeto**.
3. Em **Modelos** > **Visual C#**, selecione **Aplicativo de Console (.NET Framework)**, digite *myDotnetProject* para o nome do projeto, selecione o local do projeto e, em seguida, clique em **OK**.

## <a name="install-the-packages"></a>Instalar os pacotes

Os pacotes NuGet são a maneira mais fácil de instalar as bibliotecas de que você precisa para concluir estas etapas. Para obter as bibliotecas que você precisa no Visual Studio, siga estas etapas:

1. Clique em **Ferramentas** > **Gerenciador de Pacotes Nuget** e em **Console do Gerenciador de Pacotes**.
2. Digite os seguintes comandos no console:

    ```powershell
    Install-Package Microsoft.Azure.Management.Fluent
    Install-Package WindowsAzure.Storage
    ```

## <a name="create-the-files"></a>Criar os arquivos

Nesta etapa, você cria um arquivo de modelo que implanta os recursos e um arquivo de parâmetros que fornece valores de parâmetro para o modelo. Você também cria um arquivo de autorização que é usado para executar operações do Azure Resource Manager.

### <a name="create-the-template-file"></a>Criar o arquivo de modelo

1. No Gerenciador de Soluções, clique com o botão direito do mouse em *myDotnetProject* > **Adicionar** > **Novo Item** e, em seguida, selecione **Arquivo de Texto** em *Itens do Visual C#*. Nomeie o arquivo *CreateVMTemplate.json* e, em seguida, clique em **Adicionar**.
2. Adicione este código JSON ao arquivo que você criou:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUsername": { "type": "string" },
        "adminPassword": { "type": "securestring" }
      },
      "variables": {
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks','myVNet')]", 
        "subnetRef": "[concat(variables('vnetID'),'/subnets/mySubnet')]", 
      },
      "resources": [
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "myPublicIPAddress",
          "location": "[resourceGroup().location]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "dnsSettings": {
              "domainNameLabel": "myresourcegroupdns1"
            }
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "myVNet",
          "location": "[resourceGroup().location]",
          "properties": {
            "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
            "subnets": [
              {
                "name": "mySubnet",
                "properties": { "addressPrefix": "10.0.0.0/24" }
              }
            ]
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "myNic",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Network/publicIPAddresses/', 'myPublicIPAddress')]",
            "[resourceId('Microsoft.Network/virtualNetworks/', 'myVNet')]"
          ],
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "publicIPAddress": { "id": "[resourceId('Microsoft.Network/publicIPAddresses','myPublicIPAddress')]" },
                  "subnet": { "id": "[variables('subnetRef')]" }
                }
              }
            ]
          }
        },
        {
          "apiVersion": "2016-04-30-preview",
          "type": "Microsoft.Compute/virtualMachines",
          "name": "myVM",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Network/networkInterfaces/', 'myNic')]"
          ],
          "properties": {
            "hardwareProfile": { "vmSize": "Standard_DS1" },
            "osProfile": {
              "computerName": "myVM",
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPassword')]"
            },
            "storageProfile": {
              "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "2012-R2-Datacenter",
                "version": "latest"
              },
              "osDisk": {
                "name": "myManagedOSDisk",
                "caching": "ReadWrite",
                "createOption": "FromImage"
              }
            },
            "networkProfile": {
              "networkInterfaces": [
                {
                  "id": "[resourceId('Microsoft.Network/networkInterfaces','myNic')]"
                }
              ]
            }
          }
        }
      ]
    }
    ```

3. Salve o arquivo CreateVMTemplate.json.

### <a name="create-the-parameters-file"></a>Criar o arquivo de parâmetros

Para especificar valores para os parâmetros de recursos definidos no modelo, você cria um arquivo de parâmetros que contém os valores.

1. No Gerenciador de Soluções, clique com o botão direito do mouse em *myDotnetProject* > **Adicionar** > **Novo Item** e, em seguida, selecione **Arquivo de Texto** em *Itens do Visual C#*. Nomeie o arquivo *Parameters.json* e, em seguida, clique em **Adicionar**.
2. Adicione este código JSON ao arquivo que você criou:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUserName": { "value": "azureuser" },
        "adminPassword": { "value": "Azure12345678" }
      }
    }
    ```

4. Salve o arquivo Parameters.json.

### <a name="create-the-authorization-file"></a>Criar o arquivo de autorização

Antes de poder implantar um modelo, verifique se você tem acesso a uma [entidade de serviço do Active Directory](../../active-directory/develop/howto-authenticate-service-principal-powershell.md). Na entidade de serviço, você adquire um token para autenticar as solicitações para o Azure Resource Manager. Além disso, registre a ID do aplicativo, a chave de autenticação e a ID do locatário de que você precisa no arquivo de autorização.

1. No Gerenciador de Soluções, clique com o botão direito do mouse em *myDotnetProject* > **Adicionar** > **Novo Item** e, em seguida, selecione **Arquivo de Texto** em *Itens do Visual C#*. Nomeie o arquivo *azureauth.properties* e, em seguida, clique em **Adicionar**.
2. Adicione estas propriedades de autorização:

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.windows.net/
    ```

    Substitua **&lt;subscription-id&gt;** pelo identificador da assinatura, **&lt;application-id&gt;** pelo identificador de aplicativo do Active Directory, **&lt;authentication-key&gt;** pela chave do aplicativo e **&lt;tenant-id&gt;** pelo identificador do locatário.

3. Salve o arquivo azureauth.properties.
4. Defina uma variável de ambiente no Windows chamada AZURE_AUTH_LOCATION com o caminho completo para o arquivo de autorização que você criou, por exemplo, o comando do PowerShell a seguir pode ser usado:

    ```powershell
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2017\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

    

## <a name="create-the-management-client"></a>Criar o cliente de gerenciamento

1. Abra o arquivo Program.cs para o projeto que você criou e, em seguida, adicione o seguinte usando instruções para as instruções existentes na parte superior do arquivo:

    ```csharp
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

2. Para criar o cliente de gerenciamento, adicione este código ao método Main:

    ```csharp
    var credentials = SdkContext.AzureCredentialsFactory
        .FromFile(Environment.GetEnvironmentVariable("AZURE_AUTH_LOCATION"));

    var azure = Azure
        .Configure()
        .WithLogLevel(HttpLoggingDelegatingHandler.Level.Basic)
        .Authenticate(credentials)
        .WithDefaultSubscription();
    ```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Para especificar valores para o aplicativo, adicione código ao método Main:

```csharp
var groupName = "myResourceGroup";
var location = Region.USWest;

var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

O modelo e os parâmetros são implantados por meio de uma conta de armazenamento no Azure. Nesta etapa, você cria a conta e carrega os arquivos. 

Para criar a conta, adicione este código ao método Main:

```csharp
string storageAccountName = SdkContext.RandomResourceName("st", 10);

Console.WriteLine("Creating storage account...");
var storage = azure.StorageAccounts.Define(storageAccountName)
    .WithRegion(Region.USWest)
    .WithExistingResourceGroup(resourceGroup)
    .Create();

var storageKeys = storage.GetKeys();
string storageConnectionString = "DefaultEndpointsProtocol=https;"
    + "AccountName=" + storage.Name
    + ";AccountKey=" + storageKeys[0].Value
    + ";EndpointSuffix=core.windows.net";

var account = CloudStorageAccount.Parse(storageConnectionString);
var serviceClient = account.CreateCloudBlobClient();

Console.WriteLine("Creating container...");
var container = serviceClient.GetContainerReference("templates");
container.CreateIfNotExistsAsync().Wait();
var containerPermissions = new BlobContainerPermissions()
    { PublicAccess = BlobContainerPublicAccessType.Container };
container.SetPermissionsAsync(containerPermissions).Wait();

Console.WriteLine("Uploading template file...");
var templateblob = container.GetBlockBlobReference("CreateVMTemplate.json");
templateblob.UploadFromFileAsync("..\\..\\CreateVMTemplate.json").Result();

Console.WriteLine("Uploading parameters file...");
var paramblob = container.GetBlockBlobReference("Parameters.json");
paramblob.UploadFromFileAsync("..\\..\\Parameters.json").Result();
```

## <a name="deploy-the-template"></a>Implantar o modelo

Implante o modelo e os parâmetros da conta de armazenamento que foi criada. 

Para implantar o modelo, adicione este código ao método Main:

```csharp
var templatePath = "https://" + storageAccountName + ".blob.core.windows.net/templates/CreateVMTemplate.json";
var paramPath = "https://" + storageAccountName + ".blob.core.windows.net/templates/Parameters.json";
var deployment = azure.Deployments.Define("myDeployment")
    .WithExistingResourceGroup(groupName)
    .WithTemplateLink(templatePath, "1.0.0.0")
    .WithParametersLink(paramPath, "1.0.0.0")
    .WithMode(Microsoft.Azure.Management.ResourceManager.Fluent.Models.DeploymentMode.Incremental)
    .Create();
Console.WriteLine("Press enter to delete the resource group...");
Console.ReadLine();
```

## <a name="delete-the-resources"></a>Excluir os recursos

Como você é cobrado pelos recursos utilizados no Azure, sempre é uma boa prática excluir os recursos que não são mais necessários. Você não precisa excluir cada recurso separadamente de um grupo de recursos. Exclua o grupo de recursos e todos os seus recursos serão excluídos automaticamente. 

Para excluir o grupo de recursos, adicione este código ao método Main:

```csharp
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Executar o aplicativo

Devem ser necessários cerca de cinco minutos para o aplicativo de console executar completamente do início ao fim. 

1. Para executar o aplicativo de console, clique em **Iniciar**.

2. Antes de pressionar **Enter** para iniciar a exclusão de recursos, reserve alguns minutos para verificar a criação dos recursos no portal do Azure. Clique no status de implantação para ver informações sobre a implantação.

## <a name="next-steps"></a>Próximas etapas

* Se houver problemas com a implantação, a próxima etapa será examinar [Troubleshoot common Azure deployment errors with Azure Resource Manager](../../resource-manager-common-deployment-errors.md) (Solucionar erros comuns de implantação do Azure com o Azure Resource Manager).
* Saiba como implantar uma máquina virtual e seus recursos de suporte examinando [Implantar uma máquina virtual do Azure usando o C#](csharp.md).
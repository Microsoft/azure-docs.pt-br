---
title: Use um volume baseado no Arquivos do Azure em um aplicativo Malha do Azure Service Fabric | Microsoft Docs
description: Saiba como armazenar estado em um aplicativo de Malha do Azure Service Fabric montando um volume baseado em Arquivos do Azure dentro do volume usando a CLI do Azure.
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 9f21ad737fdcd0bcdc77394096308e47a4fb5a00
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68371113"
---
# <a name="mount-an-azure-files-based-volume-in-a-service-fabric-mesh-application"></a>Montar um volume de Arquivos do Azure com base em um aplicativo da Malha do Azure Service Fabric 

Este artigo descreve como montar um volume de arquivos do Azure com base em um serviço de um aplicativo da Malha do Azure Service Fabric.  O driver de volume de arquivos do Azure é um driver de volume do Docker usado para montar um compartilhamento de Arquivos do Azure para um contêiner, que você usa para manter o estado do serviço. Os volumes oferecem armazenamento de arquivos de finalidade geral e permitem a você ler/gravar arquivos usando APIs de arquivo de E/S de disco normal.  Para saber mais sobre volumes e opções para armazenar dados de aplicativo, leia [armazenando o estado](service-fabric-mesh-storing-state.md).

Para montar um volume em um serviço, crie um recurso de volume em seu aplicativo de Malha do Azure Service Fabric e, em seguia, referencia esse volume no seu serviço.  Declarar o recurso de volume e fazer referência a ele no recurso de serviço pode ser feito nos [arquivos de recurso com base em YAML](#declare-a-volume-resource-and-update-the-service-resource-yaml) ou [o modelo de implantação com base em JSON](#declare-a-volume-resource-and-update-the-service-resource-json). Antes de montar o volume, primeiro crie uma conta de armazenamento do Azure e um [compartilhamento de arquivos nos Arquivos do Azure](/azure/storage/files/storage-how-to-create-file-share).

## <a name="prerequisites"></a>Pré-requisitos
> [!NOTE]
> **Problema conhecido com a implantação no computador de desenvolvimento RS5 do Windows:** Há um bug aberto com o cmdlet do PowerShell New-SmbGlobalMapping em computadores com Windows RS5 que impede a montagem de volumes do Azurefile. Abaixo está um erro de exemplo que é encontrado quando o volume baseado no Azure está sendo montado no computador de desenvolvimento local.
```
Error event: SourceId='System.Hosting', Property='CodePackageActivation:counterService:EntryPoint:131884291000691067'.
There was an error during CodePackage activation.System.Fabric.FabricException (-2147017731)
Failed to start Container. ContainerName=sf-2-63fc668f-362d-4220-873d-85abaaacc83e_6d6879cf-dd43-4092-887d-17d23ed9cc78, ApplicationId=SingleInstance_0_App2, ApplicationName=fabric:/counterApp. DockerRequest returned StatusCode=InternalServerError with ResponseBody={"message":"error while mounting volume '': mount failed"}
```
A solução alternativa para o problema é 1) executar o comando a seguir como administrador do PowerShell e 2) reinicializar o computador.
```powershell
PS C:\WINDOWS\system32> Mofcomp c:\windows\system32\wbem\smbwmiv2.mof
```

Você pode usar o Azure Cloud Shell ou uma instalação local da CLI do Azure para concluir esse arquivo. 

Para usar a CLI do Azure com este artigo localmente, certifique-se de que `az --version` retorna pelo menos `azure-cli (2.0.43)`.  Instale (ou atualize) o módulo de extensão da CLI de Malha do Azure Service Fabric, seguindo estas [instruções](service-fabric-mesh-howto-setup-cli.md).

Entre no Azure e defina sua assinatura:

```azurecli
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-storage-account-and-file-share-optional"></a>Crie um compartilhamento de arquivo e conta de armazenamento (opcional)
Montar um volume de Arquivos do Azure requer um compartilhamento de arquivo e conta de armazenamento.  Você pode usar uma conta de armazenamento do Azure e compartilhamento de arquivo ou criar recursos:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az storage account create --name myStorageAccount --resource-group myResourceGroup --location eastus --sku Standard_LRS --kind StorageV2

$current_env_conn_string=$(az storage account show-connection-string -n myStorageAccount -g myResourceGroup --query 'connectionString' -o tsv)

az storage share create --name myshare --quota 2048 --connection-string $current_env_conn_string
```

## <a name="get-the-storage-account-name-and-key-and-the-file-share-name"></a>Obter o nome da conta de armazenamento e a chave e o nome do compartilhamento de arquivo
O nome da conta de armazenamento, chave de conta de armazenamento e o nome do compartilhamento de arquivos são referenciados como `<storageAccountName>`, `<storageAccountKey>`, e `<fileShareName>` nas seções a seguir. 

Liste suas contas de armazenamento e obtenha o nome da conta de armazenamento com o compartilhamento de arquivos que deseja usar:
```azurecli-interactive
az storage account list
```

Obtenha o nome do compartilhamento de arquivo:
```azurecli-interactive
az storage share list --account-name <storageAccountName>
```

Obter a chave da conta de armazenamento ("chave1"):
```azurecli-interactive
az storage account keys list --account-name <storageAccountName> --query "[?keyName=='key1'].value"
```

Você também pode encontrar esses valores na [portal do Azure](https://portal.azure.com):
* `<storageAccountName>` - Em **Contas de Armazenamento**, o nome da conta de armazenamento que você usou quando criou o compartilhamento de arquivos.
* `<storageAccountKey>` - Selecione a conta de armazenamento em **Contas de Armazenamento** e, em seguida, selecione **Chaves de acesso** e use o valor em **key1**.
* `<fileShareName>` - Selecione a conta de armazenamento em **Contas de Armazenamento** e, em seguida, selecione **Arquivos**. O nome a usar é o nome do compartilhamento de arquivos que você acabou de criar.

## <a name="declare-a-volume-resource-and-update-the-service-resource-json"></a>Declare um recurso de volume e atualize o recurso de serviço (JSON)

Adicione parâmetros para `<fileShareName>`, `<storageAccountName>`, e `<storageAccountKey>` valores encontrados em uma etapa anterior. 

Crie um recurso de Volume como um parceiro do recurso de Aplicativo. Especifique um nome e o provedor ("SFAzureFile" para usar o arquivos do Azure com base em volume). Em `azureFileParameters`, especifique os parâmetros para `<fileShareName>`, `<storageAccountName>`, e `<storageAccountKey>` valores encontrados em uma etapa anterior.

Para montar o volume em seu serviço, adicione um `volumeRefs` para o `codePackages` elemento do serviço.  `name` é a ID de recurso para o volume (ou um parâmetro de modelo de implantação para o recurso de volume) e o nome do volume é declarado no arquivo de recurso volume.yaml.  `destinationPath` é o diretório local que o volume será montado.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "defaultValue": "EastUS",
      "type": "String",
      "metadata": {
        "description": "Location of the resources."
      }
    },
    "fileShareName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure Files file share that provides the volume for the container."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure storage account that contains the file share."
      }
    },
    "storageAccountKey": {
      "type": "securestring",
      "metadata": {
        "description": "Access key for the Azure storage account that contains the file share."
      }
    },
    "stateFolderName": {
      "type": "string",
      "defaultValue": "TestVolumeData",
      "metadata": {
        "description": "Folder in which to store the state. Provide an empty value to create a unique folder for each container to store the state. A non-empty value will retain the state across deployments, however if more than one applications are using the same folder, the counter may update more frequently."
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-09-01-preview",
      "name": "VolumeTest",
      "type": "Microsoft.ServiceFabricMesh/applications",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/networks/VolumeTestNetwork",
        "Microsoft.ServiceFabricMesh/volumes/testVolume"
      ],
      "properties": {
        "services": [
          {
            "name": "VolumeTestService",
            "properties": {
              "description": "VolumeTestService description.",
              "osType": "Windows",
              "codePackages": [
                {
                  "name": "VolumeTestService",
                  "image": "volumetestservice:dev",
                  "volumeRefs": [
                    {
                      "name": "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]",
                      "destinationPath": "C:\\app\\data"
                    }
                  ],
                  "environmentVariables": [
                    {
                      "name": "ASPNETCORE_URLS",
                      "value": "http://+:20003"
                    },
                    {
                      "name": "STATE_FOLDER_NAME",
                      "value": "[parameters('stateFolderName')]"
                    }
                  ],
                  ...
                }
              ],
              ...
            }
          }
        ],
        "description": "VolumeTest description."
      }
    },
    {
      "apiVersion": "2018-09-01-preview",
      "name": "testVolume",
      "type": "Microsoft.ServiceFabricMesh/volumes",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "Azure Files storage volume for the test application.",
        "provider": "SFAzureFile",
        "azureFileParameters": {
          "shareName": "[parameters('fileShareName')]",
          "accountName": "[parameters('storageAccountName')]",
          "accountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
    ...
  ]
}
```

## <a name="declare-a-volume-resource-and-update-the-service-resource-yaml"></a>Declare um recurso de volume e atualize o recurso de serviço (JSON)

Adicione um novo arquivo *volume.yaml* arquivo no diretório *recursos do aplicativo* para seu aplicativo.  Especifique um nome e o provedor ("SFAzureFile" para usar o arquivos do Azure com base em volume). `<fileShareName>`, `<storageAccountName>`, e `<storageAccountKey>` são os valores encontrados em uma etapa anterior.

```yaml
volume:
  schemaVersion: 1.0.0-preview2
  name: testVolume
  properties:
    description: Azure Files storage volume for counter App.
    provider: SFAzureFile
    azureFileParameters: 
        shareName: <fileShareName>
        accountName: <storageAccountName>
        accountKey: <storageAccountKey>
```

Atualização do arquivo *service.yaml* no diretório *Recursos do Serviço* para montar o volume em seu serviço.  Adicione a `volumeRefs` elemento para o `codePackages` elemento.  `name` é a ID de recurso para o volume (ou um parâmetro de modelo de implantação para o recurso de volume) e o nome do volume é declarado no arquivo de recurso volume.yaml.  `destinationPath` é o diretório local que o volume será montado.

```yaml
## Service definition ##
application:
  schemaVersion: 1.0.0-preview2
  name: VolumeTest
  properties:
    services:
      - name: VolumeTestService
        properties:
          description: VolumeTestService description.
          osType: Windows
          codePackages:
            - name: VolumeTestService
              image: volumetestservice:dev
              volumeRefs:
                - name: "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]"
                  destinationPath: C:\app\data
              endpoints:
                - name: VolumeTestServiceListener
                  port: 20003
              environmentVariables:
                - name: ASPNETCORE_URLS
                  value: http://+:20003
                - name: STATE_FOLDER_NAME
                  value: TestVolumeData
              resources:
                requests:
                  cpu: 0.5
                  memoryInGB: 1
          replicaCount: 1
          networkRefs:
            - name: VolumeTestNetwork
```

## <a name="next-steps"></a>Próximas etapas

- Exibir o aplicativo de exemplo do volume de arquivos do Azure no [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Para saber mais sobre o modelo de recursos do Service Fabric, consulte [modelo de recursos de Malha do Service Fabric](service-fabric-mesh-service-fabric-resources.md).
- Para saber mais sobre Malha do Service Fabric, leia [visão geral sobre Malha do Service Fabric](service-fabric-mesh-overview.md).

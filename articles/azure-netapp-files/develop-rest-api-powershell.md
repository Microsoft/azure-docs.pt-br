---
title: Desenvolver para Azure NetApp Files com a API REST usando o PowerShell | Microsoft Docs
description: Descreve como começar a usar a API REST do Azure NetApp Files usando o PowerShell.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/02/2020
ms.author: b-juche
ms.openlocfilehash: 2e169bb4f7be8b52657d2caf8f05643875a8348c
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102180370"
---
# <a name="develop-for-azure-netapp-files-with-rest-api-using-powershell"></a>Desenvolver para Azure NetApp Files com a API REST usando o PowerShell

A API REST para o serviço Azure NetApp Files define operações HTTP em relação a recursos como a conta do NetApp, o pool de capacidade, os volumes e os instantâneos. Este artigo ajuda você a começar a usar a API REST do Azure NetApp Files usando o PowerShell.

## <a name="azure-netapp-files-rest-api-specification"></a>Especificação da API REST do Azure NetApp Files

A especificação da API REST para Azure NetApp Files é publicada por meio do [GitHub](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager):

`https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager`


## <a name="access-the-azure-netapp-files-rest-api"></a>Acessar a API REST do Azure NetApp Files  

1. [Instale a CLI do Azure](/cli/azure/install-azure-cli), se ainda não estiver instalada.
2. Crie uma entidade de serviço no Azure AD (Azure Active Directory):
   1. Verifique se você tem [permissões suficientes](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

   2. Na CLI do Azure, insira o seguinte comando:  

      ```azurepowershell
      $RBAC_SP = az ad sp create-for-rbac --name <YOURSPNAMEGOESHERE> | ConvertFrom-Json         
      ```

      Para exibir as informações da entidade de serviço, digite `$RBAC_SP` e pressione Enter.

      ```output
      appId       : appID displays here
      displayName : displayName
      name        : http://SP_Name
      password    : super secret password
      tenant      : your tenant shows here
      ```
        
      A saída é salva no objeto de variável `$RBAC_SP` . Usaremos os `$RBAC_SP.appId` `$RBAC_SP.password` valores,, e `$RBAC_SP.tenant` .

3. Solicite um token de acesso OAuth:

    Os exemplos neste artigo usam o PowerShell. Também é possível usar várias ferramentas de API como [Postman](https://www.getpostman.com/), [Insomnia](https://insomnia.rest/) e [Paw](https://paw.cloud/).  

    Usando a `$RBAC_SP` variável, agora obteremos o token de portador. 
    
    ```azurepowershell
    $body = "grant_type=client_credentials&client_id=$($RBAC_SP.appId)&client_secret=$($RBAC_SP.password)&resource=https://management.azure.com/"
    $BearerToken = Invoke-RestMethod -Method Post -body $body -Uri https://login.microsoftonline.com/$($RBAC_SP.tenant)/oauth2/token
    ```
    A saída fornece um objeto de token de portador. Você pode ver o token de acesso digitando `$BearerToken.access_token` . Ele é semelhante ao exemplo a seguir:

    ```output
    eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9
    ```

    O token exibido permanecerá válido por 3600 segundos. Após esse período, será necessário solicitar um novo token. O token é salvo na variável e será usado na próxima etapa.

4. Crie o objeto `headers`:

    ```azurepowershell  
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("Authorization", "Bearer $($BearerToken.access_token)")
    $headers.Add("Content-Type", "application/json")
    ```

5. Envie uma chamada de teste e inclua o token para validar o acesso à API REST:

    ```azurepowershell
    $SubId = (Get-AzureRmContext).Subscription.Id 
    Invoke-RestMethod -Method Get -Headers $headers -Uri https://management.azure.com/subscriptions/$SubId/providers/Microsoft.Web/sites?api-version=2019-11-01
    ```

## <a name="examples-using-the-api"></a>Exemplos usando a API  

Este artigo usa a seguinte URL para a linha de base das solicitações. Essa URL aponta para a raiz do namespace do Azure NetApp Files. 

`https://management.azure.com/subscriptions/$SUBID/resourceGroups/$RESOURCEGROUP/providers/Microsoft.NetApp/netAppAccounts?api-version=2019-11-01`

Você deve atribuir valores de variáveis antes de executar os exemplos a seguir com seus próprios valores. As variáveis do PowerShell são acessadas digitando `$variablename` .
As variáveis do PowerShell são atribuídas usando `$variablename = “value”` .

```azurepowershell
$Region = “westus2" 
$ResourceGroup = “MYTest-RG-63" 
$ANFvnetname = “NetAppFilesVnet-63"
$ANFvnetCIDR = “10.63.64.0/18"
$ANFsubnet = “NetAppFilesSubnet-63"
$ANFsubnetCIDR = “10.63.120.0/28"
$ANFsubnetID = “/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.Network/virtualNetworks/$ANFvnetname/subnets/$ANFSubnet"
$ANFAccount = “TestoftheAPI"
$ANFCapacityPool = “ANFTestPool"
$ANFServicelevel = “Standard"
$ANFVolume = “ANFTestVolume"
$ANFVolumeShareName = “Share-TEST"
$ANFVolumesize = 100GB
$ANFSnapshot = “ANFTestSnapshot"
```

### <a name="put-request-examples"></a>Exemplos de solicitação PUT

Uma solicitação PUT é usada para criar novos objetos no Azure NetApp Files, conforme mostrado nos exemplos a seguir. O corpo da solicitação PUT inclui os dados formatados em JSON para as alterações. Ele deve ser incluído no comando do PowerShell como texto ou referenciado como um arquivo. Para fazer referência ao corpo como um arquivo, salve o exemplo JSON em um arquivo e adicione `-body (Get-Content @<filename>)` ao comando do PowerShell.

```azurepowershell
    #create a NetApp account  
    $body = "{ 
        `"name`": `"$ANFAccount`", 
        `"type`": `"Microsoft.NetApp/netAppAccounts`", 
        `"location`": `"$Region`", 
        `"properties`": { 
            `"name`": `"$ANFAccount`" 
        }
    } "

    $api_version = "2020-02-01"
    Invoke-RestMethod -Method 'PUT' -Headers $headers -Body $body "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFAccount`?api-version=$api_version"  
```

```azurepowershell
    #create a capacity pool  
    $body = "{
      `"location`": `"$Region`",
      `"properties`": {
        `"size`": " + 4TB + ",
        `"serviceLevel`": `"Standard`"
      }
    }"
    $api_version = "2020-02-01"
    Invoke-RestMethod -Method 'PUT' -Headers $headers -Body $body "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFAccount/capacityPools/$ANFCapacityPool`?api-version=$api_version" 
```

```azurepowershell
    #create a volume  
    $body = "{
        `"name`": `"$ANFVolume`",
        `"type`": `"Microsoft.NetApp/netAppAccounts/capacityPools/volumes`",
        `"location`": `"$Region`",
        `"properties`": {
            `"serviceLevel`": `"$ANFServicelevel`",
            `"usageThreshold`": `"$ANFVolumesize`",
            `"creationToken`": `"$ANFVolumeShareName`",
            `"snapshotId`": `"`",
            `"subnetId`": `"$ANFSubnetID`"
        }
    }"
    $api_version = "2020-02-01"
    Invoke-RestMethod -Method 'PUT' -Headers $headers -Body $body "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFAccount/capacityPools/$ANFCapacityPool/volumes/$ANFVolume`?api-version=$api_version" 
```

```azurepowershell
    #create a volume snapshot
    $body = "{
        `"name`": `"$ANFAccount/$ANFCapacityPool/$ANFVolume/$ANFSnapshot`",
        `"type`": `"Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots`",
        `"location`": `"$Region`",
        `"properties`": {
            `"name`": `"$ANFSnapshot`",
            `"fileSystemId`": `"$FileSystemID`"
        }
    }"
    $api_version = '2020-02-01'
    Invoke-RestMethod -Method 'PUT' -Headers $headers -Body $body "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFAccount/capacityPools/$ANFCapacityPool/volumes/$ANFVolume/Snapshots/$ANFSnapshot`?api-version=$api_version"
```

### <a name="json-examples"></a>Exemplos de JSON

O exemplo a seguir mostra como criar uma conta do NetApp:

```json
    { 
        "name": "MYNETAPPACCOUNT", 
        "type": "Microsoft.NetApp/netAppAccounts", 
        "location": "westus2", 
        "properties": { 
            "name": "MYNETAPPACCOUNT" 
        }
    } 
```

O exemplo a seguir mostra como criar um pool de capacidade: 

```json
    {
        "name": "MYNETAPPACCOUNT/POOLNAME",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools",
        "location": "westus2",
        "properties": {
            "name": "POOLNAME",
            "size": "4398046511104",
            "serviceLevel": "Premium"
        }
    }
```

O exemplo a seguir mostra como criar um novo volume. (O protocolo padrão para o volume é NFSV3.) 

```json
    {
        "name": "MYNEWVOLUME",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools/volumes",
        "location": "westus2",
        "properties": {
            "serviceLevel": "Premium",
            "usageThreshold": "322122547200",
            "creationToken": "MY-FILEPATH",
            "snapshotId": "",
            "subnetId": "/subscriptions/$SUBID/resourceGroups/$RESOURCEGROUP/providers/Microsoft.Network/virtualNetworks/VNETGOESHERE/subnets/MYDELEGATEDSUBNET.sn"
        }
    }
```

O exemplo a seguir mostra como criar um instantâneo de um volume: 

```json
    {
        "name": "apitest2/apiPool01/apiVol01/snap02",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots",
        "location": "westus2",
        "properties": {
            "name": "snap02",
            "fileSystemId": "0168704a-bbec-da81-2c29-503825fe7420"
        }
    }
```

> [!NOTE] 
> É necessário especificar `fileSystemId` para criar um instantâneo.  Você pode obter o valor `fileSystemId` com uma solicitação GET para um volume. 

### <a name="get-request-examples"></a>Exemplos de solicitação GET

Ocorrerá um erro se o recurso não existir. Uma solicitação GET é usada para consultar objetos do Azure NetApp Files em uma assinatura, conforme mostrado nos exemplos a seguir:

```azurepowershell
#get NetApp accounts 
Invoke-RestMethod -Method Get -Headers $headers -Uri https://management.azure.com/subscriptions/$SUBID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts?api-version=2019-11-01 | ConvertTo-Json
```

```azurepowershell
#get capacity pools for NetApp account 
Invoke-RestMethod -Method Get -Headers $headers -Uri https://management.azure.com/subscriptions/$SUBID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFACCOUNT/capacityPools?api-version=2019-11-01 | ConvertTo-Json
```

```azurepowershell
#get volumes in NetApp account & capacity pool 
Invoke-RestMethod -Method Get -Headers $headers -Uri https://management.azure.com/subscriptions/$SUBID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFACCOUNT/capacityPools/$ANFCAPACITYPOOL/volumes?api-version=2019-11-01 | ConvertTo-Json
```

```azurepowershell
#get snapshots for a volume 
Invoke-RestMethod -Method Get -Headers $headers -Uri https://management.azure.com/subscriptions/$SUBID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFACCOUNT/capacityPools/$ANFCAPACITYPOOL/volumes/$ANFVOLUME/snapshots?api-version=2019-11-01 | ConvertTo-Json
```

### <a name="complete-powershell-scripts"></a>Concluir scripts do PowerShell
Esta seção mostra scripts de exemplo para o PowerShell.

```azurepowershell
    <#
    Disclaimer 
    The sample scripts are not supported under any Microsoft standard support program or service. The sample scripts are provided AS IS without warranty of any kind. Microsoft further disclaims all implied warranties including, without limitation, any implied warranties of merchantability or of fitness for a particular purpose. The entire risk arising out of the use or performance of the sample scripts and documentation remains with you. In no event shall Microsoft, its authors, or anyone else involved in the creation, production, or delivery of the scripts be liable for any damages whatsoever (including, without limitation, damages for loss of business profits, business interruption, loss of business information, or other pecuniary loss) arising out of the use of or inability to use the sample scripts or documentation, even if Microsoft has been advised of the possibility of such damages.
    #>
    
    $Region = "westus2"
    $SubID = (Get-AzureRmContext).Subscription.Id
    $MyRandomID =  Get-Random -Minimum 100 -Maximum 999
    $ResourceGroup = "MYTest-RG-" + $MyRandomID
    $ANFAccount = "ANFTestAccount-$Region-" + $MyRandomID
    $ANFCapacityPool =  "ANFTestPool-$Region-" + $MyRandomID
    $ANFVolume = "ANFTestVolume-$Region-" + $MyRandomID
    $ANFVolumesize = 100GB
    $ANFVolumeShareName = "Share-" + $MyRandomID
    $ANFSnapshot = "ANFTestSnapshot-$Region-" + $MyRandomID
    $ANFServicelevel = "Standard"
    $Octet2 = Get-Random -Minimum 1 -Maximum 254
    $ANFvnetname = "NetAppFilesVnet-$Octet2-$Region-" + $MyRandomID
    $ANFvnetCIDR = "10.$Octet2.64.0/18"
    $ANFsubnet = "NetAppFilesSubnet-$Octet2-$Region-" + $MyRandomID
    $ANFsubnetCIDR = "10.$Octet2.120.0/28"
    $vmsubnet = "VM-subnet-$Octet2-$Region-" + $MyRandomID
    $vmsubnetCIDR = "10.$Octet2.121.0/24"
    
    $BearerToken = (az account get-access-token | ConvertFrom-Json).accessToken
    
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("Authorization", "Bearer $BearerToken")
    $headers.Add("Content-Type", "application/json")
    
    " ** Creating Resource Group $ResourceGroup *************"
    $api_version = "2020-01-01"
    $body = "    {
      `"location`": `"$Region`",
      `"name`": `"$ResourceGroup`"
    },"
    $response = Invoke-RestMethod -Method 'PUT' -Headers $headers -Body $body -Uri "https://management.azure.com/subscriptions/$SubID/resourcegroups/$ResourceGroup`?api-version=$api_version" 
    While ($response.properties.provisioningState -notmatch "Succeeded") {
       $response.properties.provisioningState
       sleep 5
       $response = Invoke-RestMethod -Method ‘GET’ -Headers $headers -Uri "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup`?api-version=$api_version" 
       } 
    
    " ** Creating Virtual Network $ANFvnetname *************"
    $body = "{
      `"properties`": {
        `"addressSpace`": {
          `"addressPrefixes`": [
            `"$ANFvnetCIDR`"
          ]
        },
        `"subnets`": [
          {
            `"name`": `"$ANFsubnet`",
            `"properties`": {
              `"addressPrefix`": `"$ANFsubnetCIDR`",
              `"delegations`": [
                {
                  `"name`": `"Microsoft.NetApp`",
                  `"properties`": {
                    `"serviceName`": `"Microsoft.NetApp/volumes`"
                  }
                }
              ]
            }
          },
         {
            `"name`": `"$vmsubnet`",
            `"properties`": {
              `"addressPrefix`": `"$vmsubnetCIDR`"
                }
         }
        ]
      },
      `"location`": `"$Region`"
    }"
    
    $api_version = "2020-03-01"
    $response = Invoke-RestMethod -Method 'PUT' -Headers $headers -Body $body -Uri "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.Network/virtualNetworks/$ANFvnetname`?api-version=$api_version" 
    While ($response.properties.provisioningState -notmatch "Succeeded") {
           sleep 5
           $response = Invoke-RestMethod -Method ‘GET’ -Headers $headers -Uri "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.Network/virtualNetworks/$ANFvnetname`?api-version=$api_version" 
           $response.properties.provisioningState
           }  
    $ANFsubnetID = $response.properties.subnets.id[0]
    
    " ** Creating ANF Account $ANFAccount *************"
    $body = "{ 
        `"name`": `"$ANFAccount`", 
        `"type`": `"Microsoft.NetApp/netAppAccounts`", 
        `"location`": `"$Region`", 
        `"properties`": { 
            `"name`": `"$ANFAccount`" 
        }
    } "
    $api_version = "2020-02-01"
    $response = Invoke-RestMethod -Method 'PUT' -Headers $headers -Body $body -Uri "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFAccount`?api-version=$api_version" 
    $response.properties.provisioningState
    While ($response.properties.provisioningState -notmatch "Succeeded") {
       $response.properties.provisioningState
       sleep 5
       $response = Invoke-RestMethod -Method ‘GET’ -Headers $headers -Uri "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFAccount`?api-version=$api_version" 
       }  
    
    " ** Creating Capacity Pool $ANFCapacityPool *************"
    $body = "{
      `"location`": `"$Region`",
      `"properties`": {
        `"size`": " + 4TB + ",
        `"serviceLevel`": `"Standard`"
      }
    }"
    $api_version = "2020-02-01"
    $response = Invoke-RestMethod -Method 'PUT' -Headers $headers -Body $body -Uri "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFAccount/capacityPools/$ANFCapacityPool`?api-version=$api_version" 
    $response.properties.provisioningState
    While ($response.properties.provisioningState -notmatch "Succeeded") {
       $response.properties.provisioningState
       sleep 5
       $response = Invoke-RestMethod -Method ‘GET’ -Headers $headers -Uri "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFAccount`?api-version=$api_version" 
       }  
    
    " ** Creating ANF Volume $ANFVolume *************"
    $body = "{
        `"name`": `"$ANFVolume`",
        `"type`": `"Microsoft.NetApp/netAppAccounts/capacityPools/volumes`",
        `"location`": `"$Region`",
        `"properties`": {
            `"serviceLevel`": `"$ANFServicelevel`",
            `"usageThreshold`": `"$ANFVolumesize`",
            `"creationToken`": `"$ANFVolumeShareName`",
            `"snapshotId`": `"`",
            `"subnetId`": `"$ANFSubnetID`"
        }
    }"
    $api_version = "2020-02-01"
    $response = Invoke-RestMethod -Method 'PUT' -Headers $headers -Body $body -Uri "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFAccount/capacityPools/$ANFCapacityPool/volumes/$ANFVolume`?api-version=$api_version" 
    While ($response.properties.provisioningState -notmatch "Succeeded") {
       $response.properties.provisioningState
       sleep 15
       $response = Invoke-RestMethod -Method ‘GET’ -Headers $headers -Uri "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFAccount/capacityPools/$ANFCapacityPool/volumes/$ANFVolume`?api-version=$api_version" 
       } 
    $Volume = $response 
    $FileSystemID = $response.properties.fileSystemId
    
    " ** Creating ANF Volume Snapshot $ANFSnapshot *************"
    $body = "{
        `"name`": `"$ANFAccount/$ANFCapacityPool/$ANFVolume/$ANFSnapshot`",
        `"type`": `"Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots`",
        `"location`": `"$Region`",
        `"properties`": {
            `"name`": `"$ANFSnapshot`",
            `"fileSystemId`": `"$FileSystemID`"
        }
    }"
    $api_version = '2020-02-01'
    $response = Invoke-RestMethod -Method 'PUT' -Headers $headers -Body $body -Uri "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFAccount/capacityPools/$ANFCapacityPool/volumes/$ANFVolume/Snapshots/$ANFSnapshot`?api-version=$api_version" 
    While ($response.properties.provisioningState -notmatch "Succeeded") {
       $response.properties.provisioningState
       sleep 5
       $response = Invoke-RestMethod -Method ‘GET’ -Headers $headers -Uri "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFAccount/capacityPools/$ANFCapacityPool/volumes/$ANFVolume/Snapshots/$ANFSnapshot`?api-version=$api_version" 
       }  
```

## <a name="next-steps"></a>Próximas etapas

[Consulte a referência da API REST do Azure NetApp Files](/rest/api/netapp/)
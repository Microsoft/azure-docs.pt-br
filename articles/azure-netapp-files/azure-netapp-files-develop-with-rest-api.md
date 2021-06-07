---
title: Desenvolver para Azure NetApp Files com API REST | Microsoft Docs
description: A API REST para o serviço de Azure NetApp Files define as operações HTTP para recursos como a conta do NetApp, o pool de capacidade, os volumes e os instantâneos.
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
ms.openlocfilehash: c5993dc1dc645319e272ab310a97bc3ff8ac495d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102174233"
---
# <a name="develop-for-azure-netapp-files-with-rest-api"></a>Desenvolver para Azure NetApp Files com API REST 

A API REST para o serviço Azure NetApp Files define operações HTTP em relação a recursos como a conta do NetApp, o pool de capacidade, os volumes e os instantâneos. Este artigo é uma introdução sobre como usar a API REST do Azure NetApp Files.

## <a name="azure-netapp-files-rest-api-specification"></a>Especificação da API REST do Azure NetApp Files

A especificação da API REST para Azure NetApp Files é publicada por meio do [GitHub](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager):

`https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager`


## <a name="access-the-azure-netapp-files-rest-api"></a>Acessar a API REST do Azure NetApp Files  

1. [Instale a CLI do Azure](/cli/azure/install-azure-cli), se ainda não estiver instalada.
2. Crie uma entidade de serviço no Azure AD (Azure Active Directory):
   1. Verifique se você tem [permissões suficientes](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

   2. Na CLI do Azure, insira o seguinte comando: 
    
        ```azurecli
        az ad sp create-for-rbac --name $YOURSPNAMEGOESHERE
        ```

      A saída do comando é semelhante ao exemplo a seguir:  

        ```output
        { 
            "appId": "appIDgoeshere", 
            "displayName": "APPNAME", 
            "name": "http://APPNAME", 
            "password": "supersecretpassword", 
            "tenant": "tenantIDgoeshere" 
        } 
        ```

      Guarde a saída do comando.  Os valores `appId`, `password` e `tenant` serão necessários. 

3. Solicite um token de acesso OAuth:

    Os exemplos neste artigo usam cURL. Também é possível usar várias ferramentas de API como [Postman](https://www.getpostman.com/), [Insomnia](https://insomnia.rest/) e [Paw](https://paw.cloud/).  

    Substitua as variáveis no exemplo a seguir pela saída do comando da Etapa 2 acima. 
    
    ```azurecli
    curl -X POST -d 'grant_type=client_credentials&client_id=[APP_ID]&client_secret=[PASSWORD]&resource=https%3A%2F%2Fmanagement.azure.com%2F' https://login.microsoftonline.com/[TENANT_ID]/oauth2/token
    ```

    A saída fornece um token de acesso semelhante ao exemplo a seguir:

    `eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9`

    O token exibido permanecerá válido por 3600 segundos. Após esse período, será necessário solicitar um novo token. 
    Salve o token em um editor de texto.  Você precisará desse token na próxima etapa.

4. Envie uma chamada de teste e inclua o token para validar o acesso à API REST:

    ```azurecli
    curl -X GET -H "Authorization: Bearer [TOKEN]" -H "Content-Type: application/json" https://management.azure.com/subscriptions/[SUBSCRIPTION_ID]/providers/Microsoft.Web/sites?api-version=2019-11-01
    ```

## <a name="examples-using-the-api"></a>Exemplos usando a API  

Este artigo usa a seguinte URL para a linha de base das solicitações. Essa URL aponta para a raiz do namespace do Azure NetApp Files. 

`https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2019-11-01`

Substitua os valores de `SUBIDGOESHERE` e `RESOURCEGROUPGOESHERE` nos exemplos a seguir pelos seus próprios valores. 

### <a name="get-request-examples"></a>Exemplos de solicitação GET

Uma solicitação GET é usada para consultar objetos do Azure NetApp Files em uma assinatura, conforme mostrado nos exemplos a seguir: 

```azurecli
#get NetApp accounts 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2019-11-01
```

```azurecli
#get capacity pools for NetApp account 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools?api-version=2019-11-01
```

```azurecli
#get volumes in NetApp account & capacity pool 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes?api-version=2019-11-01
```

```azurecli
#get snapshots for a volume 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/VOLUMEGOESHERE/snapshots?api-version=2019-11-01
```

### <a name="put-request-examples"></a>Exemplos de solicitação PUT

Uma solicitação PUT é usada para criar novos objetos no Azure NetApp Files, conforme mostrado nos exemplos a seguir. O corpo da solicitação PUT pode incluir os dados formatados em JSON para as alterações. Ele deve ser incluído no comando de ondulação como texto ou referências como um arquivo. Para fazer referência ao corpo como um arquivo, salve o exemplo JSON em um arquivo e adicione `-d @<filename>` ao comando de ondulação.

```azurecli
#create a NetApp account  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE?api-version=2019-11-01
```

```azurecli
#create a capacity pool  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE?api-version=2019-11-01
```

```azurecli
#create a volume  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME?api-version=2019-11-01
```

```azurecli
 #create a volume snapshot  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME/Snapshots/SNAPNAME?api-version=2019-11-01
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
        "subnetId": "/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.Network/virtualNetworks/VNETGOESHERE/subnets/MYDELEGATEDSUBNET.sn"
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

## <a name="next-steps"></a>Próximas etapas

[Consulte a referência da API REST do Azure NetApp Files](/rest/api/netapp/)
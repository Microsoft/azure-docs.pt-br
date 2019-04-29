---
title: Desenvolver para Azure NetApp Files com API REST | Microsoft Docs
description: Descreve uma instrução sobre o uso da API REST do Azure NetApp Files.
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
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: b-juche
ms.openlocfilehash: 56667b9a47411b2abae30ff159fa6bc555fec070
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61087224"
---
# <a name="develop-for-azure-netapp-files-with-rest-api"></a>Desenvolver para Azure NetApp Files com API REST 

A API REST para o serviço Azure NetApp Files define operações HTTP em relação a recursos como a conta do NetApp, o pool de capacidade, os volumes e os instantâneos. Este artigo é uma introdução sobre como usar a API REST do Azure NetApp Files.

## <a name="access-the-azure-netapp-files-rest-api"></a>Acessar a API REST do Azure NetApp Files  

1. [Instale a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), se ainda não estiver instalada.
2. Crie uma entidade de serviço no Azure AD (Azure Active Directory):
   1. Verifique se você tem [permissões suficientes](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

   1. Na CLI do Azure, insira o seguinte comando:  

           az ad sp create-for-rbac --name $YOURSPNAMEGOESHERE--password $YOURGENERATEDPASSWORDGOESHERE

      A saída do comando é semelhante ao exemplo a seguir:  

           { 
               "appId": "appIDgoeshere", 
               "displayName": "APPNAME", 
               "name": "http://APPNAME", 
               "password": "supersecretpassword", 
               "tenant": "tenantIDgoeshere" 
           } 

      Guarde a saída do comando.  Os valores `appId`, `password` e `tenant` serão necessários. 

3. Solicite um token de acesso OAuth:

    Os exemplos neste artigo usam cURL.  Também é possível usar várias ferramentas de API como [Postman](https://www.getpostman.com/), [Insomnia](https://insomnia.rest/) e [Paw](https://paw.cloud/).  

    Substitua as variáveis no exemplo a seguir pela saída do comando da Etapa 2 acima. 

        curl -X POST -d 'grant_type=client_credentials&client_id=[APP_ID]&client_secret=[PASSWORD]&resource=https%3A%2F%2Fmanagement.azure.com%2F' https://login.microsoftonline.com/[TENANT_ID]/oauth2/token

    A saída fornece um token de acesso semelhante ao exemplo a seguir:

        eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9

    O token exibido permanecerá válido por 3600 segundos. Após esse período, será necessário solicitar um novo token. 
    Salve o token em um editor de texto.  Você precisará desse token na próxima etapa.

4. Envie uma chamada de teste e inclua o token para validar o acesso à API REST:

        curl -X GET -H "Authorization: Bearer [TOKEN]" -H "Content-Type: application/json" https://management.azure.com/subscriptions/[SUBSCRIPTION_ID]/providers/Microsoft.Web/sites?api-version=2016-08-01

## <a name="examples-using-the-api"></a>Exemplos usando a API  

Este artigo usa a seguinte URL para a linha de base das solicitações. Essa URL aponta para a raiz do namespace do Azure NetApp Files. 

`https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2017-08-15`

Substitua os valores de `subID` e `resourceGroups` nos exemplos a seguir pelos seus próprios valores. 

### <a name="get-request-examples"></a>Exemplos de solicitação GET

Uma solicitação GET é usada para consultar objetos do Azure NetApp Files em uma assinatura, conforme mostrado nos exemplos a seguir: 

        #get NetApp accounts 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2017-08-15

        #get capacity pools for NetApp account 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools?api-version=2017-08-15

        #get volumes in NetApp account & capacity pool 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes?api-version=2017-08-15

        #get snapshots for a volume 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/VOLUMEGOESHERE/snapshots?api-version=2017-08-15

### <a name="put-request-examples"></a>Exemplos de solicitação PUT

Uma solicitação PUT é usada para criar novos objetos no Azure NetApp Files, conforme mostrado nos exemplos a seguir. O corpo da solicitação PUT pode incluir os dados formatados em JSON para as alterações ou pode especificar um arquivo para leitura. 

        #create a NetApp account  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE?api-version=2017-08-15

        #create a capacity pool  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE?api-version=2017-08-15

        #create a volume  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME?api-version=2017-08-15

        #create a volume snapshot  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME/Snapshots/SNAPNAME?api-version=2017-08-15

### <a name="json-examples"></a>Exemplos de JSON

O exemplo a seguir mostra como criar uma conta do NetApp:

    { 
        "name": "MYNETAPPACCOUNT", 
        "type": "Microsoft.NetApp/netAppAccounts", 
        "location": "westus2", 
        "properties": { 
            "name": "MYNETAPPACCOUNT" 
        }
    } 

O exemplo a seguir mostra como criar um pool de capacidade: 

    {
        "name": "MYNETAPPACCOUNT/POOLNAME",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools",
        "location": "westus2",
        "properties": {
            "name": "POOLNAME"
            "size": "4398046511104",
            "serviceLevel": "Premium"
        }
    }

O exemplo a seguir mostra como criar um novo volume: 

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

O exemplo a seguir mostra como criar um instantâneo de um volume: 

    {
        "name": "apitest2/apiPool01/apiVol01/snap02",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots",
        "location": "westus2",
        "properties": {
            "name": "snap02",
            "fileSystemId": "0168704a-bbec-da81-2c29-503825fe7420"
        }
    }

> [!NOTE] 
> É necessário especificar `fileSystemId` para criar um instantâneo.  Você pode obter o valor `fileSystemId` com uma solicitação GET para um volume. 

## <a name="next-steps"></a>Próximas etapas

[Consulte a referência da API REST do Azure NetApp Files](https://docs.microsoft.com/rest/api/netapp/)

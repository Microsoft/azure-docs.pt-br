---
title: Identidade gerenciada para o Data Factory
description: Saiba mais sobre identidade gerenciada para Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: jingwang
ms.openlocfilehash: 65512f8e46b5545929a798392ac5f19ddeab39ed
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105562453"
---
# <a name="managed-identity-for-data-factory"></a>Identidade gerenciada para o Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo ajuda você a entender o que é uma identidade gerenciada para Data Factory (anteriormente conhecido como Identidade de Serviço Gerenciada/MSI) e como ela funciona.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Visão geral

Ao criar um data factory, uma identidade gerenciada pode ser criada junto com a criação de fábrica. A identidade gerenciada é um aplicativo gerenciado registrado para Azure Active Directory e representa esse data factory específico.

A identidade gerenciada para Data Factory beneficia os seguintes recursos:

- [Armazene a credencial em Azure Key Vault](store-credentials-in-key-vault.md), nesse caso data Factory identidade gerenciada é usada para Azure Key Vault autenticação.
- Acesse armazenamentos de dados ou computações usando a autenticação de identidade gerenciada, incluindo o armazenamento de BLOBs do Azure, Azure Data Explorer, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, banco de dados SQL do Azure, Azure SQL Instância Gerenciada, Azure Synapse Analytics, REST, atividade do databricks, atividade da Web e muito mais. Verifique os artigos do conector e da atividade para obter detalhes.

## <a name="generate-managed-identity"></a>Gerar identidade gerenciada

A identidade gerenciada para Data Factory é gerada da seguinte maneira:

- Ao criar data factory por meio do **portal do Azure ou do PowerShell**, a identidade gerenciada será sempre criada automaticamente.
- Ao criar data factory por meio do **SDK**, a identidade gerenciada será criada somente se você especificar "Identity = New FactoryIdentity ()" no objeto de fábrica para criação. Consulte o exemplo do [início rápido do .NET - criar data factory](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Ao criar data factory por meio da **API REST**, a identidade gerenciada será criada somente se você especificar a seção "identidade" no corpo da solicitação. Consulte o exemplo do [início rápido do REST - criar data factory](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Se você achar que o data factory não tem uma identidade gerenciada associada após a instrução [recuperar identidade gerenciada](#retrieve-managed-identity) , você pode gerar explicitamente uma atualizando a data Factory com o iniciador de identidade programaticamente:

- [Gerar identidade gerenciada usando o PowerShell](#generate-managed-identity-using-powershell)
- [Gerar identidade gerenciada usando a API REST](#generate-managed-identity-using-rest-api)
- [Gerar identidade gerenciada usando um modelo de Azure Resource Manager](#generate-managed-identity-using-an-azure-resource-manager-template)
- [Gerar identidade gerenciada usando o SDK](#generate-managed-identity-using-sdk)

>[!NOTE]
>- A identidade gerenciada não pode ser modificada. A atualização de um data factory que já tem uma identidade gerenciada não terá nenhum impacto, a identidade gerenciada será mantida inalterada.
>- Se você atualizar um data factory que já tem uma identidade gerenciada sem especificar o parâmetro "Identity" no objeto de fábrica ou sem especificar a seção "Identity" no corpo da solicitação REST, receberá um erro.
>- Quando você exclui um data factory, a identidade gerenciada associada será excluída junto com.

### <a name="generate-managed-identity-using-powershell"></a>Gerar identidade gerenciada usando o PowerShell

Chame o comando **set-AzDataFactoryV2** , então você verá os campos de "identidade" que estão sendo gerados recentemente:

```powershell
PS C:\WINDOWS\system32> Set-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="generate-managed-identity-using-rest-api"></a>Gerar identidade gerenciada usando a API REST

Chame a API abaixo com a seção "identity" no corpo da solicitação:

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**Corpo da solicitação**: adicionar "identity": {"type": "SystemAssigned"}.

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**Resposta**: a identidade gerenciada é criada automaticamente e a seção "identidade" é preenchida de acordo.

```json
{
    "name": "<dataFactoryName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2018-06-01"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>"
}
```

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>Gerar identidade gerenciada usando um modelo de Azure Resource Manager

**Modelo**: adicionar "identity": {"type": "SystemAssigned"}.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "resources": [{
        "name": "<dataFactoryName>",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.DataFactory/factories",
        "location": "<region>",
        "identity": {
            "type": "SystemAssigned"
        }
    }]
}
```

### <a name="generate-managed-identity-using-sdk"></a>Gerar identidade gerenciada usando o SDK

Chame a função create_or_update do data factory com Identity=new FactoryIdentity(). Exemplo de código usando .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-managed-identity"></a>Recuperar identidade gerenciada

Você pode recuperar a identidade gerenciada de portal do Azure ou de forma programática. As seções a seguir mostram alguns exemplos.

>[!TIP]
> Se você não vir a identidade gerenciada, [gere a identidade gerenciada](#generate-managed-identity) atualizando sua fábrica.

### <a name="retrieve-managed-identity-using-azure-portal"></a>Recuperar identidade gerenciada usando portal do Azure

Você pode encontrar as informações de identidade gerenciadas de portal do Azure-> suas propriedades de data factory >.

- ID de objeto de identidade gerenciada
- Locatário de identidade gerenciada

As informações de identidade gerenciadas também aparecerão quando você criar um serviço vinculado, que dá suporte à autenticação de identidade gerenciada, como blob do Azure, Azure Data Lake Storage, Azure Key Vault, etc.

Ao conceder a permissão, na guia IAM (controle de acesso do recurso do Azure)-> adicionar atribuição de função-> atribuir acesso a-> selecione Data Factory em identidade gerenciada atribuída pelo sistema-> selecione pelo nome da fábrica; ou, em geral, você pode usar a ID de objeto ou nome de data factory (como nome de identidade gerenciada) para localizar essa identidade. Se você precisar obter a ID do aplicativo da identidade gerenciada, poderá usar o PowerShell.

### <a name="retrieve-managed-identity-using-powershell"></a>Recuperar identidade gerenciada usando o PowerShell

A ID da entidade de identidade gerenciada e a ID do locatário serão retornadas quando você obter uma data factory específica da seguinte maneira. Use o **PrincipalId** para conceder acesso:

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Você pode obter a ID do aplicativo copiando a ID da entidade de segurança acima e, em seguida, executando abaixo Azure Active Directory comando com ID de entidade de segurança como parâmetro.

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

### <a name="retrieve-managed-identity-using-rest-api"></a>Recuperar identidade gerenciada usando a API REST

A ID da entidade de identidade gerenciada e a ID do locatário serão retornadas quando você obter uma data factory específica da seguinte maneira.

Chame a API abaixo na solicitação:

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}?api-version=2018-06-01
```

**Resposta**: você receberá uma resposta como mostrada no exemplo abaixo. A seção "identidade" é preenchida de acordo.

```json
{
    "name":"<dataFactoryName>",
    "identity":{
        "type":"SystemAssigned",
        "principalId":"554cff9e-XXXX-XXXX-XXXX-90c7d9ff2ead",
        "tenantId":"72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>",
    "type":"Microsoft.DataFactory/factories",
    "properties":{
        "provisioningState":"Succeeded",
        "createTime":"2020-02-12T02:22:50.2384387Z",
        "version":"2018-06-01",
        "factoryStatistics":{
            "totalResourceCount":0,
            "maxAllowedResourceCount":0,
            "factorySizeInGbUnits":0,
            "maxAllowedFactorySizeInGbUnits":0
        }
    },
    "eTag":"\"03006b40-XXXX-XXXX-XXXX-5e43617a0000\"",
    "location":"<region>",
    "tags":{

    }
}
```

> [!TIP] 
> Para recuperar a identidade gerenciada de um modelo do ARM, adicione uma seção **Outputs** no JSON do ARM:

```json
{
    "outputs":{
        "managedIdentityObjectId":{
            "type":"string",
            "value":"[reference(resourceId('Microsoft.DataFactory/factories', parameters('<dataFactoryName>')), '2018-06-01', 'Full').identity.principalId]"
        }
    }
}
```

## <a name="next-steps"></a>Próximas etapas
Consulte os tópicos a seguir que introduzem quando e como usar data factory identidade gerenciada:

- [Armazenar credencial em Azure Key Vault](store-credentials-in-key-vault.md)
- [Copiar dados de/para o Azure Data Lake Store usando identidades gerenciadas para autenticação de recursos do Azure](connector-azure-data-lake-store.md)

Consulte [visão geral de identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) para obter mais informações sobre identidades gerenciadas para recursos do Azure, que data Factory identidade gerenciada baseia-se no.
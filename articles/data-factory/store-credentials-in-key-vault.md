---
title: Armazenar credenciais no Azure Key Vault | Microsoft Docs
description: "Saiba como armazenar as credenciais para os armazenamentos de dados usados em um Azure Key Vault, de modo que o Azure Data Factory possa recuperá-las automaticamente em tempo de execução."
services: data-factory
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: jingwang
ms.openlocfilehash: cb4545dcd41fe9a2c73bc86096843b717d61a785
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="store-credential-in-azure-key-vault"></a>Armazenar credencial no Azure Key Vault

Você pode armazenar credenciais para armazenamentos de dados em um [Azure Key Vault](../key-vault/key-vault-whatis.md). O Azure Data Factory recupera as credenciais ao executar uma atividade que use o armazenamento de dados.

> [!NOTE]
> Atualmente, apenas o [conector do Dynamics](connector-dynamics-crm-office-365.md) dá suporte a esse recurso.

## <a name="steps"></a>Etapas

Ao criar um data factory, uma identidade do serviço pode ser criada ao mesmo tempo. A identidade do serviço é um aplicativo gerenciado registrado no Azure Active Directory e representa esse data factory específico.

- Ao criar o data factory por meio de **portal do Azure ou do PowerShell**, a identidade do serviço sempre será criada automaticamente a partir da visualização pública.
- Ao criar o data factory por meio do **SDK**, a identidade do serviço será criada somente se você especificar "identidade = FactoryIdentity() nova" no objeto de fábrica para a criação. Consulte o exemplo do [início rápido do .NET - criar data factory](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Ao criar o data factory por meio da **API REST**, a identidade do serviço será criada somente se você especificar a seção "identidade" no corpo da solicitação. Consulte o exemplo do [início rápido do REST - criar data factory](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Para referenciar uma credencial armazenada no Azure Key Vault, você precisa:

1. Copiar a "ID DO APLICATIVO DA IDENTIDADE DO SERVIÇO" gerada junto com seu data factory. Consulte [recuperar a identidade do serviço](#retrieve-service-identity).
2. Conceder à identidade do serviço o acesso ao seu Azure Key Vault. Em seu cofre de chaves -> Controle de acesso -> Adicionar -> pesquise essa ID do aplicativo da identidade do serviço para adicionar permissão de leitura. Isso permite que esse factory específico acesse o segredo no cofre de chaves.
3. Criar um serviço vinculado que aponte para seu Azure Key Vault. Consulte [Serviço vinculado do Azure Key Vault](#azure-key-vault-linked-service).
4. Criar um serviço vinculado de armazenamento de dados, dentro do qual fazer referência ao segredo correspondente armazenado no cofre de chaves. Consulte [fazer referência a credencial armazenada no cofre de chaves](#reference-credential-stored-in-key-vault).

## <a name="retrieve-service-identity"></a>Recuperar a identidade do serviço

Você pode recuperar a identidade do serviço no portal do Azure ou programaticamente. As seções a seguir mostram alguns exemplos.

>[!TIP]
> Se você não vir a identidade do serviço, [gere a identidade de serviço](#generate-service-identity) atualizando seu alocador.

### <a name="using-azure-portal"></a>Usando o Portal do Azure

Você pode encontrar as informações sobre a identidade do serviço no Portal do Azure -> seu data factory -> Configurações -> Propriedades:

- ID DA IDENTIDADE DO SERVIÇO
- LOCATÁRIO DA IDENTIDADE DO SERVIÇO
- **ID DO APLICATIVO DE IDENTIDADE DO SERVIÇO** > copie este valor para conceder acesso no Key Vault

![Recuperar a identidade do serviço](media/store-credentials-in-key-vault/retrieve-service-identity-portal.png)

### <a name="using-powershell"></a>Usando o PowerShell

A ID da entidade de segurança da identidade do serviço e a ID de locatário serão retornadas ao obter um data factory específico da seguinte maneira:

```powershell
PS C:\WINDOWS\system32> (Get-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Copie a ID da entidade de segurança, em seguida, execute o comando do Active Directory do Azure abaixo com a ID da entidade de segurança como parâmetro para obter o **ApplicationId**, que você pode usar para conceder acesso no Key Vault:

```powershell
PS C:\WINDOWS\system32> Get-AzureRmADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="azure-key-vault-linked-service"></a>Serviço vinculado do Azure Key Vault

As propriedades a seguir têm suporte no serviço vinculado do Azure Key Vault:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **AzureKeyVault**. | Sim |
| baseUrl | Especifique a URL (nome DNS) do Azure Key Vault. | Sim |

**Exemplo:**

```json
{
    "name": "AzureKeyVaultLinkedService",
    "properties": {
        "type": "AzureKeyVault",
        "typeProperties": {
        "baseUrl": "https://<azureKeyVaultName>.vault.azure.net"
        }
    }
}
```

## <a name="reference-credential-stored-in-key-vault"></a>Fazer referência a credencial armazenada no cofre de chaves

As propriedades a seguir têm suporte quando você configura um campo no serviço vinculado, referenciando um segredo do cofre de chaves:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do campo deve ser definida como: **AzureKeyVaultSecret**. | Sim |
| secretName | O nome do segredo no Azure Key Vault. | Sim |
| secretVersion | A versão do segredo no Azure Key Vault.<br/>Se não for especificada, a versão mais recente do segredo será sempre usada.<br/>Se especificada, a versão especificada será sempre usada.| Não |
| store | Refere-se a um serviço vinculado do Azure Key Vault que você usa para armazenar a credencial. | Sim |

**Exemplo: (consulte a seção "senha")**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "<>",
            "organizationName": "<>",
            "authenticationType": "<>",
            "username": "<>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "mySecret",
                "store":{
                    "linkedServiceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="generate-service-identity"></a>Gerar a identidade do serviço

Se você achar que seu data factory não tem uma identidade do serviço associada seguindo a instrução [recuperar a identidade de serviço](#retrieve-service-identity), você pode gerar uma atualizando o data factory com o iniciador de identidade do forma programática.

> [!NOTE]
> - **A identidade do serviço não pode ser alterada**. Atualizar um data factory que já tem uma identidade do serviço não terá nenhum impacto - a identidade do serviço permanecerá inalterada.
> - **A identidade do serviço não pode ser excluída**. Se você atualizar um data factory que já tem uma identidade do serviço sem especificar o parâmetro "identity" no objeto do alocador ou sem especificar a seção "identity" no corpo da solicitação REST, você obterá um erro.

As seções a seguir mostram alguns exemplos sobre como gerar a identidade do serviço para o seu alocador se ele não existir.

### <a name="using-powershell"></a>Usando o PowerShell

Chame o comando **Set-AzureRmDataFactoryV2** novamente, em seguida, você verá os campos "Identity" sendo recentemente gerados:

```powershell
PS C:\WINDOWS\system32> Set-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="using-rest-api"></a>Usando a API REST

Chame a API abaixo com a seção "identity" no corpo da solicitação:

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2017-09-01-preview
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

**Resposta**: a identidade do serviço é criada automaticamente e a seção "identity" é preenchida de acordo.

```json
{
    "name": "ADFV2DemoFactory",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2017-09-01-preview"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "EastUS"
}
```

### <a name="using-sdk"></a>Usando o SDK

Chame a função create_or_update do data factory com Identity=new FactoryIdentity(). Exemplo de código usando .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).
---
title: "Associação de tabela externa do Azure Functions (versão prévia) | Microsoft Docs"
description: "Usar as associações de tabela externa no Azure Functions"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/12/2017
ms.author: alkarche
ms.openlocfilehash: e6aa6913bdb2cf8e581b76b7680d0a96c9858bd1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-external-table-binding-preview"></a>Associação de tabela externa do Azure Functions (versão prévia)
Este artigo mostra como manipular dados tabulares em provedores de SaaS (por exemplo, Sharepoint, Dynamics) com sua função com associações internas. O Azure Functions dá suporte a associações de entrada e saída para tabelas externas.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="api-connections"></a>Conexões de API

As associações de tabela aproveitam conexões de API externas para autenticar com provedores de SaaS terceirizados. 

Ao atribuir uma associação você pode criar uma nova conexão de API ou usar uma conexão de API existente dentro do mesmo grupo de recursos

### <a name="supported-api-connections-tables"></a>Conexões de API com suporte (Tabela)s

|Conector|Gatilho|Entrada|Saída|
|:-----|:---:|:---:|:---:|
|[DB2](https://docs.microsoft.com/azure/connectors/connectors-create-api-db2)||x|x
|[Dynamics 365 para Operações](https://ax.help.dynamics.com/wiki/install-and-configure-dynamics-365-for-operations-warehousing/)||x|x
|[Dynamics 365](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||x|x
|[Dynamics NAV](https://msdn.microsoft.com/library/gg481835.aspx)||x|x
|[Google Sheets](https://docs.microsoft.com/azure/connectors/connectors-create-api-googledrive)||x|x
|[Informix](https://docs.microsoft.com/azure/connectors/connectors-create-api-informix)||x|x
|[Dynamics 365 para Finanças](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||x|x
|[MySQL](https://docs.microsoft.com/azure/store-php-create-mysql-database)||x|x
|[Banco de dados Oracle](https://docs.microsoft.com/azure/connectors/connectors-create-api-oracledatabase)||x|x
|[Common Data Service](https://docs.microsoft.com/common-data-service/entity-reference/introduction)||x|x
|[Salesforce](https://docs.microsoft.com/azure/connectors/connectors-create-api-salesforce)||x|x
|[SharePoint](https://docs.microsoft.com/azure/connectors/connectors-create-api-sharepointonline)||x|x
|[SQL Server](https://docs.microsoft.com/azure/connectors/connectors-create-api-sqlazure)||x|x
|[Teradata](http://www.teradata.com/products-and-services/azure/products/)||x|x
|UserVoice||x|x
|Zendesk||x|x


> [!NOTE]
> Conexões de tabela externa também podem ser usadas nos [Aplicativo Lógico do Azure](https://docs.microsoft.com/azure/connectors/apis-list)

### <a name="creating-an-api-connection-step-by-step"></a>Criar uma conexão de API: passo a passo

1. Criar uma função > função personalizada ![Criar uma função personalizada](./media/functions-bindings-storage-table/create-custom-function.jpg)
1. Modelo `Experimental` > `ExternalTable-CSharp` de cenário > Criar uma nova `External Table connection`
![Escolher modelo de entrada de tabela](./media/functions-bindings-storage-table/create-template-table.jpg)
1. Escolha seu provedor de SaaS > escolha/crie uma conexão ![Configurar conexão de SaaS](./media/functions-bindings-storage-table/authorize-API-connection.jpg)
1. Selecione sua conexão de API > crie a função ![Criar função de tabela](./media/functions-bindings-storage-table/table-template-options.jpg)
1. Selecionar `Integrate` > `External Table`
    1. Configure a conexão para usar sua tabela de destino. Essas configurações irão variar entre provedores de SaaS. Elas estão destacados abaixo nas [configurações de fonte de dados](#datasourcesettings)
![Configurar tabela](./media/functions-bindings-storage-table/configure-API-connection.jpg)

## <a name="usage"></a>Uso

Este exemplo conecta a uma tabela denominada "Contato" com as colunas Id, Nome e Sobrenome. O código lista as entidades de contato na tabela e registra os nomes e sobrenomes.

### <a name="bindings"></a>Associações
```json
{
  "bindings": [
    {
      "type": "manualTrigger",
      "direction": "in",
      "name": "input"
    },
    {
      "type": "apiHubTable",
      "direction": "in",
      "name": "table",
      "connection": "ConnectionAppSettingsKey",
      "dataSetName": "default",
      "tableName": "Contact",
      "entityId": "",
    }
  ],
  "disabled": false
}
```
`entityId` deve estar vazia para associações de tabela.

`ConnectionAppSettingsKey` identifica a configuração de aplicativo que armazena a sequência da conexão da API. A configuração do aplicativo é criada automaticamente quando você adiciona uma conexão de API à interface de usuário integrada.

Um conector tabular fornece conjuntos de dados e cada conjunto de dados contém tabelas. O nome do conjunto de dados padrão é “padrão”. Os títulos de um conjunto de dados e uma tabela em vários provedores de SaaS estão listados abaixo:

|Conector|Conjunto de dados|Tabela|
|:-----|:---|:---| 
|**SharePoint**|Site|Lista do SharePoint
|**SQL**|Banco de dados|Tabela 
|**Google Sheet**|Planilha|Planilha 
|**Excel**|Arquivo do Excel|Folha 

<!--
See the language-specific sample that copies the input file to the output file.

* [C#](#incsharp)
* [Node.js](#innodejs)

-->
<a name="incsharp"></a>

### <a name="usage-in-c"></a>Uso em C# #

```cs
#r "Microsoft.Azure.ApiHub.Sdk"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.ApiHub;

//Variable name must match column type
//Variable type is dynamically bound to the incoming data
public class Contact
{
    public string Id { get; set; }
    public string LastName { get; set; }
    public string FirstName { get; set; }
}

public static async Task Run(string input, ITable<Contact> table, TraceWriter log)
{
    //Iterate over every value in the source table
    ContinuationToken continuationToken = null;
    do
    {   
        //retreive table values
        var contactsSegment = await table.ListEntitiesAsync(
            continuationToken: continuationToken);

        foreach (var contact in contactsSegment.Items)
        {   
            log.Info(string.Format("{0} {1}", contact.FirstName, contact.LastName));
        }

        continuationToken = contactsSegment.ContinuationToken;
    }
    while (continuationToken != null);
}
```

<!--
<a name="innodejs"></a>

### Usage in Node.js

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputFile = context.bindings.myInputFile;
    context.done();
};
```
-->
<a name="datasourcesettings"></a>
## Configurações de Fonte de Dados

### <a name="sql-server"></a>SQL Server

O script para criar e preencher a tabela Contato pode ser encontrado abaixo. dataSetName é “padrão”.

```sql
CREATE TABLE Contact
(
    Id int NOT NULL,
    LastName varchar(20) NOT NULL,
    FirstName varchar(20) NOT NULL,
    CONSTRAINT PK_Contact_Id PRIMARY KEY (Id)
)
GO
INSERT INTO Contact(Id, LastName, FirstName)
     VALUES (1, 'Bitt', 'Prad') 
GO
INSERT INTO Contact(Id, LastName, FirstName)
     VALUES (2, 'Glooney', 'Ceorge') 
GO
```

### <a name="google-sheets"></a>Google Sheets
No Google Docs, crie uma planilha com uma planilha chamada `Contact`. O conector não pode usar o nome de exibição da planilha. O nome interno (em negrito) precisa ser usado como dataSetName, por exemplo: `docs.google.com/spreadsheets/d/`**`1UIz545JF_cx6Chm_5HpSPVOenU4DZh4bDxbFgJOSMz0`** adicione os nomes de coluna `Id`, `LastName`, `FirstName` à primeira linha e, em seguida, preencha os dados nas linhas seguintes.

### <a name="salesforce"></a>Salesforce
dataSetName é “padrão”.

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

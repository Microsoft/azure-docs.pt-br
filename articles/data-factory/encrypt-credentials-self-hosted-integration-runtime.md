---
title: Criptografar credenciais no Azure Data Factory
description: Aprenda a criptografar e armazenar credenciais para seus armazenamentos de dados locais em um computador com runtime de integração auto-hospedada.
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: 59d177aa3baf25f185201f1b6c4738cfce9c25a3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100392639"
---
# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>Criptografar credenciais para armazenamentos de dados locais no Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Você pode criptografar e armazenar credenciais para seus armazenamentos de dados locais (serviços vinculados com informações confidenciais) em um computador com runtime de integração auto-hospedada. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Você passa um arquivo de definição de JSON com as credenciais para o <br/>Cmdlet [**New-AzDataFactoryV2LinkedServiceEncryptedCredential**](/powershell/module/az.datafactory/New-AzDataFactoryV2LinkedServiceEncryptedCredential) para produzir um arquivo de definição JSON de saída com as credenciais criptografadas. Em seguida, use a definição atualizada do JSON para criar os serviços vinculados.

## <a name="author-sql-server-linked-service"></a>Criar serviço vinculado do SQL Server
Crie um arquivo JSON denominado **SqlServerLinkedService.json** em qualquer pasta com o conteúdo a seguir:  

Substitua `<servername>`, `<databasename>`, `<username>` e `<password>` por valores de seu SQL Server antes de salvar o arquivo. E substitua `<integration runtime name>` pelo nome de seu runtime de integração. 

```json
{
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
        },
        "connectVia": {
            "type": "integrationRuntimeReference",
            "referenceName": "<integration runtime name>"
        },
        "name": "SqlServerLinkedService"
    }
}
```

## <a name="encrypt-credentials"></a>Criptografar credenciais
Para criptografar os dados confidenciais do conteúdo JSON em um tempo de execução de integração autohospedado local, execute **New-AzDataFactoryV2LinkedServiceEncryptedCredential** e passe o conteúdo JSON. Esse cmdlet garante que as credenciais sejam criptografadas usando DPAPI e armazenadas no nó de runtime de integração auto-hospedada localmente. A carga de saída que contém a referência criptografada para a credencial pode ser redirecionada para outro arquivo JSON (neste caso, ' encryptedLinkedService.jsem ').

```powershell
New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>Use o JSON com credenciais criptografadas
Agora, use o arquivo JSON de saída do comando anterior que contém a credencial criptografada para configurar o **SqlServerLinkedService**.

```powershell
Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>Próximas etapas
Para obter informações sobre considerações de segurança para a movimentação de dados, consulte [considerações de segurança da movimentação de dados](data-movement-security-considerations.md).


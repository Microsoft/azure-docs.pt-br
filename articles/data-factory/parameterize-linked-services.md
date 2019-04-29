---
title: Parametrizar serviços vinculados no Azure Data Factory | Microsoft Docs
description: Saiba como parametrizar serviços vinculados no Azure Data Factory e passar valores dinâmicos em tempo de execução.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/18/2018
author: gauravmalhot
ms.author: gamal
manager: craigg
ms.openlocfilehash: 0239c53f98fba201b6d70e1e2212eea36134e30d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60635511"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Parametrizar serviços vinculados no Azure Data Factory

Agora é possível parametrizar um serviço vinculado e passar valores dinâmicos em tempo de execução. Por exemplo, se você quiser conectar diferentes bancos de dados no mesmo servidor do Banco de Dados SQL do Azure poderá parametrizar o nome do banco de dados na definição de serviço vinculado. Isso evita a necessidade de criar um serviço vinculado para cada banco de dados no servidor do banco de dados SQL do Azure. Também é possível parametrizar outras propriedades na definição de serviço vinculado - por exemplo, *Nome de usuário.*

É possível usar a interface do usuário do Data Factory no Portal do Azure ou uma interface de programação para parametrizar os serviços vinculados.

> [!TIP]
> É recomendável não parametrizar senhas ou segredos. Armazene todas as cadeias de conexão no Azure Key Vault e defina o *Nome secreto*.

Para uma introdução de sete minutos e uma demonstração desse recurso, assista ao vídeo a seguir:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-data-stores"></a>Armazenamento de dados com suporte

Atualmente, há suporte para parametrização do serviço vinculado na interface do usuário do Data Factory no portal do Azure para os seguintes armazenamentos de dados. Para todos os outros armazenamentos de dados, é possível parametrizar o serviço vinculado selecionando o ícone de **Código** na guia **Conexões** e usando o editor JSON.
- Banco de dados SQL do Azure
- Azure SQL Data Warehouse
- SQL Server
- Oracle
- Cosmos DB
- Amazon Redshift
- MySQL
- Banco de Dados do Azure para MySQL

## <a name="data-factory-ui"></a>IU do Data Factory

![Adicionar conteúdo dinâmico à definição de Serviço Vinculado](media/parameterize-linked-services/parameterize-linked-services-image1.png)

![Criar um novo parâmetro](media/parameterize-linked-services/parameterize-linked-services-image2.png)

## <a name="json"></a>JSON

```json
{
    "name": "AzureSqlDatabase",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "value": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
                "type": "SecureString"
            }
        },
        "connectVia": null,
        "parameters": {
            "DBName": {
                "type": "String"
            }
        }
    }
}
```

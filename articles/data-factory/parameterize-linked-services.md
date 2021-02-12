---
title: Parametrizar serviços vinculados no Azure Data Factory
description: Saiba como parametrizar serviços vinculados no Azure Data Factory e passar valores dinâmicos em tempo de execução.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/15/2021
author: dcstwh
ms.author: weetok
manager: anandsub
ms.openlocfilehash: c3c126a96c0eb2214f63a56120d0966b9a97efed
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895383"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Parametrizar serviços vinculados no Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Agora é possível parametrizar um serviço vinculado e passar valores dinâmicos em tempo de execução. Por exemplo, se você quiser se conectar a bancos de dados diferentes no mesmo SQL Server lógico, agora poderá parametrizar o nome do banco de dados na definição de serviço vinculado. Isso impede que você precise criar um serviço vinculado para cada banco de dados no SQL Server lógico. Também é possível parametrizar outras propriedades na definição de serviço vinculado - por exemplo, *Nome de usuário.*

Você pode usar a interface do usuário do Data Factory no portal do Azure ou uma interface de programação para parametrizar os serviços vinculados.

> [!TIP]
> É recomendável não parametrizar senhas ou segredos. Armazene todas as cadeias de conexão no Azure Key Vault e defina o *Nome secreto*.

> [!Note]
> Há um bug aberto para usar "-" em nomes de parâmetro, recomendamos usar nomes sem "-" até que o bug seja resolvido.

Para uma introdução de sete minutos e uma demonstração desse recurso, assista ao vídeo a seguir:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-linked-service-types"></a>Tipos de serviço vinculados com suporte

Você pode parametrizar qualquer tipo de serviço vinculado.
Ao criar o serviço vinculado na interface do usuário, o Data Factory fornece uma experiência de parametrização interna para os seguintes tipos de serviços vinculados. Na folha criação/edição de serviço vinculado, você pode encontrar opções para novos parâmetros e adicionar conteúdo dinâmico.

- Amazon Redshift
- Amazon S3
- Azure Cosmos DB (API do SQL)
- Banco de Dados do Azure para MySQL
- Azure Databricks
- Cofre de Chave do Azure
- Banco de Dados SQL do Azure
- Instância Gerenciada de SQL do Azure
- Azure Synapse Analytics 
- MySQL
- Oracle
- SQL Server
-  HTTP Genérico
- REST Genérico

Para outros tipos de serviço vinculado que não estão na lista acima, você pode parametrizar o serviço vinculado editando o JSON na interface do usuário:

- Na folha criação/edição de serviço vinculado-> expanda "avançado" na parte inferior > marque a caixa de seleção "especificar conteúdo dinâmico no formato JSON"-> especificar a carga JSON do serviço vinculado. 
- Ou, depois de criar um serviço vinculado sem parametrização, no [Hub de gerenciamento](author-visually.md#management-hub) -> serviços vinculados-> localizar o serviço vinculado específico – > clique em "código" (botão " {} ") para editar o JSON. 

Consulte o [exemplo JSON](#json) para adicionar a ` parameters` seção para definir parâmetros e fazer referência ao parâmetro usando ` @{linkedService().paraName} ` .

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
            "connectionString": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
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

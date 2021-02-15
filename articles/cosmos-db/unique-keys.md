---
title: Chaves exclusivas no Azure Cosmos DB
description: Saiba como definir e usar chaves exclusivas para um banco de dados Cosmos do Azure. Este artigo também descreve como as chaves exclusivas adicionam uma camada de integridade de dados.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/23/2020
ms.reviewer: sngun
ms.openlocfilehash: 9eb2b916bfe6c73a1535afb077b04fbb081dd5f1
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685713"
---
# <a name="unique-key-constraints-in-azure-cosmos-db"></a>Restrições de chaves exclusivas no Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Chaves exclusivas adicionam uma camada de integridade dos dados a um contêiner do Azure Cosmos. Você cria uma política de chave exclusiva ao criar um contêiner do Azure Cosmos. Com chaves exclusivas, você certifica-se de que um ou mais valores em uma partição lógica são exclusivos. Também é possível garantir exclusividade por [chave de partição](partitioning-overview.md).

Depois de criar um contêiner com uma política de chave exclusiva, a criação de uma nova ou de uma atualização de um item existente, resultando em uma duplicata dentro de uma partição lógica, é impedida, conforme especificado pela restrição UNIQUE KEY. A chave de partição combinada com a chave exclusiva garante a exclusividade de um item dentro do escopo do contêiner.

Por exemplo, considere um contêiner Cosmos do Azure com `Email address` como a restrição de chave exclusiva e `CompanyID` como a chave de partição. Ao configurar o endereço de email do usuário com uma chave exclusiva, cada item terá um endereço de email exclusivo dentro de um determinado `CompanyID`. Não é possível criar dois itens com endereços de e-mail duplicados e com o mesmo valor de chave de partição. Na API do SQL (núcleo) do Azure Cosmos DB, os itens são armazenados como valores JSON. Esses valores JSON diferenciam maiúsculas de minúsculas. Quando você escolhe uma propriedade como uma chave exclusiva, pode inserir valores diferenciados de maiúsculas e minúsculas para essa propriedade. Por exemplo, se você tiver uma chave exclusiva definida na propriedade Name, "Gaby" será diferente de "Gaby" e você poderá inserir ambas no contêiner.

Para criar itens com o mesmo endereço de email, mas não com o mesmo nome, sobrenome e endereço de email, adicione mais caminhos à política de chave exclusiva. Em vez de criar uma chave exclusiva com base apenas no endereço de email, você também pode criar uma chave exclusiva com uma combinação de nome, sobrenome e endereço de email. Essa chave é conhecida como uma chave exclusiva composta. Nesse caso, cada combinação exclusiva dos três valores dentro de um determinado `CompanyID` é permitido. 

Por exemplo, o contêiner pode conter itens com os valores a seguir, em que cada item respeita a restrição de chave exclusiva.

|CompanyID|Nome|Sobrenome|Endereço de email|
|---|---|---|---|
|Contoso|Gaby|Duperre|gaby@contoso.com |
|Contoso|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Ivan|Duperre|gaby@fabrikam.com|
|Fabrikam|   |Duperre|gaby@fabraikam.com|
|Fabrikam|   |   |gaby@fabraikam.com|

Se você tentar inserir outro item com as combinações listadas na tabela anterior, um erro será exibido. O erro indica que a restrição de chave exclusiva não foi atendida. Você recebe `Resource with specified ID or name already exists` ou `Resource with specified ID, name, or unique index already exists` como uma mensagem de retorno. 

## <a name="define-a-unique-key"></a>Definir uma chave exclusiva

É possível definir chaves exclusivas somente ao criar um contêiner do Azure Cosmos. Uma chave exclusiva é definida para uma partição lógica. No exemplo anterior, se particionar o contêiner com base no CEP você acabará com itens duplicados em cada partição lógica. Considere as seguintes propriedades ao criar chaves exclusivas:

* Não é possível atualizar um contêiner existente para usar uma chave exclusiva diferente. Isso significa que, após criar um contêiner com uma política de chave exclusiva, a política não poderá ser alterada.

* Para definir uma chave exclusiva para um contêiner existente, crie um novo contêiner com a restrição de chave exclusiva. Use a ferramenta de migração de dados apropriada para mover os dados do contêiner existente para o novo contêiner. Para contêineres SQL, use a [ferramenta de Migração de Dados](import-data.md) para mover dados. Para contêineres MongoDB, use [mongoimport.exe ou mongorestore.exe](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json) para mover dados.

* Uma política de chave exclusiva pode ter no máximo 16 valores do caminho. Por exemplo, os valores podem ser `/firstName` , `/lastName` e `/address/zipCode` . Cada política de chave exclusiva pode ter um máximo de 10 restrições ou combinações de chave exclusiva. Os caminhos combinados para cada restrição de índice exclusivo não devem exceder 60 bytes. No exemplo anterior, o nome, sobrenome e endereço de email juntos são uma restrição. Essa restrição usa 3 dentre os 16 caminhos possíveis.

* Quando um contêiner tem uma política de chave exclusiva, os encargos da [unidade de solicitação (ru)](request-units.md) para criar, atualizar e excluir um item são um pouco mais altos.

* Não há suporte para chaves exclusivas esparsas. Se alguns valores de caminho exclusivos estiverem ausentes, eles serão tratados como valores nulos, que participam da restrição de exclusividade. Por esse motivo, pode haver apenas um único item com um valor nulo para atender a essa restrição.

* Nomes exclusivos de chave diferenciam maiúsculas de minúsculas. Por exemplo, considere um contêiner com a restrição UNIQUE KEY definida como `/address/zipcode` . Se os dados tiverem um campo chamado `ZipCode` , Azure Cosmos DB inserirá "NULL" como a chave exclusiva porque `zipcode` não é o mesmo que `ZipCode` . Devido a essa diferenciação de maiúsculas e minúsculas, todos os outros registros com ZipCode não poderão ser inseridos porque o "null" duplicado violará a restrição de chave exclusiva.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [partições lógicas](partitioning-overview.md)
* Explore [como definir chaves exclusivas](how-to-define-unique-keys.md) ao criar um contêiner

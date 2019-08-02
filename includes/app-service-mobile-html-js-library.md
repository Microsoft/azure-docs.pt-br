---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: ff7ba04271c150018f2c55b62e40542a686608cf
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172432"
---
## <a name="create-client"></a>Criar uma conexão de cliente
Crie uma conexão de cliente por meio da criação de um objeto `WindowsAzure.MobileServiceClient` .  Substitua `appUrl` pela URL de seu Aplicativo Móvel.

```javascript
var client = WindowsAzure.MobileServiceClient(appUrl);
```

## <a name="table-reference"></a>Trabalhar com tabelas
Para acessar ou atualizar dados, crie uma referência à tabela de back-end. Substitua `tableName` pelo nome de sua tabela

```javascript
var table = client.getTable(tableName);
```

Depois que tiver uma referência de tabela, será possível trabalhar ainda mais com sua tabela:

* [Consultar uma tabela](#querying)
  * [Filtrando dados](#table-filter)
  * [Paginando pelos dados](#table-paging)
  * [Classificando dados](#sorting-data)
* [Inserindo dados](#inserting)
* [Modificando dados](#modifying)
* [Excluindo dados](#deleting)

### <a name="querying"></a>Como: Consultar uma referência de tabela
Depois que você tiver uma referência de tabela, será possível usá-la para consultar dados no servidor.  As consultas são feitas em uma linguagem "parecida com LINQ".
Para retornar todos os dados da tabela, use o seguinte código:

```javascript
/**
 * Process the results that are received by a call to table.read()
 *
 * @param {Object} results the results as a pseudo-array
 * @param {int} results.length the length of the results array
 * @param {Object} results[] the individual results
 */
function success(results) {
   var numItemsRead = results.length;

   for (var i = 0 ; i < results.length ; i++) {
       var row = results[i];
       // Each row is an object - the properties are the columns
   }
}

function failure(error) {
    throw new Error('Error loading data: ', error);
}

table
    .read()
    .then(success, failure);
```

A função de sucesso é chamada com os resultados.  Não use `for (var i in results)` na função de sucesso, pois isso causará a iteração de informações incluídas nos resultados quando outras funções de consulta (como `.includeTotalCount()`) forem usadas.

Para obter mais informações sobre a sintaxe Query, confira a [documentação do objeto Query].

#### <a name="table-filter"></a>Filtragem de dados no servidor
É possível usar uma cláusula `where` na referência de tabela:

```javascript
table
    .where({ userId: user.userId, complete: false })
    .read()
    .then(success, failure);
```

Você também pode usar uma função que filtra o objeto.  Nesse caso, a variável `this` é atribuída ao objeto atual que está sendo filtrado.  O código a seguir é uma funcionalidade equivalente ao exemplo anterior:

```javascript
function filterByUserId(currentUserId) {
    return this.userId === currentUserId && this.complete === false;
}

table
    .where(filterByUserId, user.userId)
    .read()
    .then(success, failure);
```

#### <a name="table-paging"></a>Paginando pelos dados
Utilize os métodos `take()` e `skip()`.  Por exemplo, se você quiser dividir a tabela em registros de 100 linhas:

```javascript
var totalCount = 0, pages = 0;

// Step 1 - get the total number of records
table.includeTotalCount().take(0).read(function (results) {
    totalCount = results.totalCount;
    pages = Math.floor(totalCount/100) + 1;
    loadPage(0);
}, failure);

function loadPage(pageNum) {
    let skip = pageNum * 100;
    table.skip(skip).take(100).read(function (results) {
        for (var i = 0 ; i < results.length ; i++) {
            var row = results[i];
            // Process each row
        }
    }
}
```

O método `.includeTotalCount()` é usado para adicionar um campo totalCount ao objeto de resultados.  O campo totalCount é preenchido com o número total de registros que retornariam se nenhuma paginação fosse usada.

Depois, você pode usar a variável de páginas e alguns botões da interface de usuário para fornecer uma lista de páginas; use `loadPage()` para carregar os novos registros de cada página.  Implemente cache para agilizar o acesso a registros que já foram carregados.

#### <a name="sorting-data"></a>Como: Retornar dados classificados
Use os métodos de consulta `.orderBy()` ou `.orderByDescending()`:

```javascript
table
    .orderBy('name')
    .read()
    .then(success, failure);
```

Para obter mais informações sobre o objeto Query, confira a [documentação do objeto Query].

### <a name="inserting"></a>Como: Inserir dados
Crie um objeto JavaScript com a data adequada e chame `table.insert()` de maneira assíncrona:

```javascript
var newItem = {
    name: 'My Name',
    signupDate: new Date()
};

table
    .insert(newItem)
    .done(function (insertedItem) {
        var id = insertedItem.id;
    }, failure);
```

Após a inserção bem-sucedida, o item inserido retorna com os campos adicionais necessários para as operações de sincronização.  Atualize seu próprio cache com essas informações para atualizações posteriores.

O SDK de Node.js Server dos Aplicativos Móveis do Azure dá suporte ao esquema dinâmico para fins de desenvolvimento.  O esquema Dinâmico permite que você adicione colunas à tabela, especificando-as em uma operação de inserção ou atualização.  Recomendamos que você desative o esquema antes de mover seu aplicativo para produção.

### <a name="modifying"></a>Como: Modificar dados
Assim como o método `.insert()`, você deve criar um objeto de atualização e, em seguida, chamar `.update()`.  O objeto de atualização deve conter a ID do registro a ser atualizada. Essa ID é obtida ao ler o registro ou ao chamar `.insert()`.

```javascript
var updateItem = {
    id: '7163bc7a-70b2-4dde-98e9-8818969611bd',
    name: 'My New Name'
};

table
    .update(updateItem)
    .done(function (updatedItem) {
        // You can now update your cached copy
    }, failure);
```

### <a name="deleting"></a>Como: Excluir dados
Chame o método `.del()` para excluir um registro.  Passe a ID em uma referência de objeto:

```javascript
table
    .del({ id: '7163bc7a-70b2-4dde-98e9-8818969611bd' })
    .done(function () {
        // Record is now deleted - update your cache
    }, failure);
```

---
title: 'Início Rápido: Usar o Node.js para se conectar ao Banco de Dados do Azure para PostgreSQL – Servidor único'
description: Este início rápido fornece um exemplo de código do Node.js que você pode usar para se conectar e consultar dados do Banco de Dados do Azure para PostgreSQL – Servidor único.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom:
- mvc
- devcenter
- seo-javascript-september2019
- seo-javascript-october2019
- devx-track-js
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 5/6/2019
ms.openlocfilehash: 7569606429740de23b56767d490b9bb14283d468
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93331685"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Início Rápido: Usar o Node.js para conectar-se e consultar dados no Banco de Dados do Azure para PostgreSQL – servidor único

Neste guia de início rápido, você se conectará a um Banco de Dados do Azure para PostgreSQL usando um aplicativo Node.js. Ele mostra como usar instruções SQL para consultar, inserir, atualizar e excluir dados no banco de dados. As etapas neste artigo pressupõem que você está familiarizado com o desenvolvimento usando Node.js e que começou recentemente a trabalhar com o Banco de Dados do Azure para PostgreSQL.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- Conclusão do [Início Rápido: Criar um servidor de Banco de Dados do Azure para PostgreSQL no portal do Azure](quickstart-create-server-database-portal.md) ou [Início Rápido: Criar um Banco de Dados do Azure para PostgreSQL usando a CLI do Azure](quickstart-create-server-database-azure-cli.md).

- [Node.js](https://nodejs.org)

## <a name="install-pg-client"></a>Instalar o cliente pg
Instalar [pg](https://www.npmjs.com/package/pg), que é um cliente PostgreSQL para Node.js.

Para fazer isso, execute o npm (gerenciador de pacotes de nó) para JavaScript na linha de comando para instalar o cliente pg.
```bash
npm install pg
```

Verifique a instalação listando os pacotes instalados.
```bash
npm list
```

## <a name="get-connection-information"></a>Obter informações de conexão
Obtenha as informações de conexão necessárias para se conectar ao Banco de Dados do Azure para PostgreSQL. Você precisa das credenciais de logon e do nome do servidor totalmente qualificado.

1. No [portal do Azure](https://portal.azure.com/), pesquise e selecione o servidor que você criou (como **mydemoserver**).

1. No painel **Visão geral** do servidor, anote o **Nome do servidor** e **Nome do usuário administrador**. Se você esquecer sua senha, também poderá redefini-la nesse painel.

   :::image type="content" source="./media/connect-nodejs/server-details-azure-database-postgresql.png" alt-text="Cadeia de conexão do Banco de Dados do Azure para PostgreSQL":::

## <a name="running-the-javascript-code-in-nodejs"></a>Executar o código JavaScript no Node.js
Você pode iniciar o Node.js do shell bash, do Terminal ou do prompt de comando do Windows digitando `node` e executar o exemplo de código JavaScript interativamente copiando-o e colando-o no prompt. Como alternativa, você pode salvar o código JavaScript em um arquivo de texto e iniciar `node filename.js` com o nome do arquivo como um parâmetro para executá-lo.

## <a name="connect-create-table-and-insert-data"></a>Conectar-se, criar tabela e inserir dados
Use o código a seguir para se conectar e carregar os dados usando instruções SQL **CREATE TABLE** e **INSERT INTO**.
O objeto [pg. Client](https://github.com/brianc/node-postgres/wiki/Client) é usado para se comunicar com o servidor PostgreSQL. A função [pg. Client.Connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) é usada para estabelecer conexão com o servidor. A função [pg.Client.Query()](https://github.com/brianc/node-postgres/wiki/Query) é usada para executar a consulta SQL no banco de dados PostgreSQL. 

Substitua os parâmetros host, dbname, user e password pelos valores que você especificou ao criar o servidor e o banco de dados.

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) throw err;
    else {
        queryDatabase();
    }
});

function queryDatabase() {
    const query = `
        DROP TABLE IF EXISTS inventory;
        CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
        INSERT INTO inventory (name, quantity) VALUES ('banana', 150);
        INSERT INTO inventory (name, quantity) VALUES ('orange', 154);
        INSERT INTO inventory (name, quantity) VALUES ('apple', 100);
    `;

    client
        .query(query)
        .then(() => {
            console.log('Table created successfully!');
            client.end(console.log('Closed client connection'));
        })
        .catch(err => console.log(err))
        .then(() => {
            console.log('Finished execution, exiting now');
            process.exit();
        });
}
```

## <a name="read-data"></a>Ler dados
Use o código a seguir para conectar-se e ler os dados usando uma instrução SQL **SELECT**. O objeto [pg. Client](https://github.com/brianc/node-postgres/wiki/Client) é usado para se comunicar com o servidor PostgreSQL. A função [pg. Client.Connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) é usada para estabelecer conexão com o servidor. A função [pg.Client.Query()](https://github.com/brianc/node-postgres/wiki/Query) é usada para executar a consulta SQL no banco de dados PostgreSQL. 

Substitua os parâmetros host, dbname, user e password pelos valores que você especificou ao criar o servidor e o banco de dados. 

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) throw err;
    else { queryDatabase(); }
});

function queryDatabase() {
  
    console.log(`Running query to PostgreSQL server: ${config.host}`);

    const query = 'SELECT * FROM inventory;';

    client.query(query)
        .then(res => {
            const rows = res.rows;

            rows.map(row => {
                console.log(`Read: ${JSON.stringify(row)}`);
            });

            process.exit();
        })
        .catch(err => {
            console.log(err);
        });
}
```

## <a name="update-data"></a>Atualizar dados
Use o código a seguir para conectar-se e ler os dados usando uma instrução SQL **UPDATE**. O objeto [pg. Client](https://github.com/brianc/node-postgres/wiki/Client) é usado para se comunicar com o servidor PostgreSQL. A função [pg. Client.Connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) é usada para estabelecer conexão com o servidor. A função [pg.Client.Query()](https://github.com/brianc/node-postgres/wiki/Query) é usada para executar a consulta SQL no banco de dados PostgreSQL. 

Substitua os parâmetros host, dbname, user e password pelos valores que você especificou ao criar o servidor e o banco de dados. 

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) throw err;
    else {
        queryDatabase();
    }
});

function queryDatabase() {
    const query = `
        UPDATE inventory 
        SET quantity= 1000 WHERE name='banana';
    `;

    client
        .query(query)
        .then(result => {
            console.log('Update completed');
            console.log(`Rows affected: ${result.rowCount}`);
        })
        .catch(err => {
            console.log(err);
            throw err;
        });
}
```

## <a name="delete-data"></a>Excluir dados
Use o código a seguir para conectar-se e ler os dados usando uma instrução SQL **DELETE**. O objeto [pg. Client](https://github.com/brianc/node-postgres/wiki/Client) é usado para se comunicar com o servidor PostgreSQL. A função [pg. Client.Connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) é usada para estabelecer conexão com o servidor. A função [pg.Client.Query()](https://github.com/brianc/node-postgres/wiki/Query) é usada para executar a consulta SQL no banco de dados PostgreSQL. 

Substitua os parâmetros host, dbname, user e password pelos valores que você especificou ao criar o servidor e o banco de dados. 

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) {
        throw err;
    } else {
        queryDatabase();
    }
});

function queryDatabase() {
    const query = `
        DELETE FROM inventory 
        WHERE name = 'apple';
    `;

    client
        .query(query)
        .then(result => {
            console.log('Delete completed');
            console.log(`Rows affected: ${result.rowCount}`);
        })
        .catch(err => {
            console.log(err);
            throw err;
        });
}
```

## <a name="clean-up-resources"></a>Limpar os recursos

Para limpar todos os recursos usados durante este guia de início rápido, exclua o grupo de recursos usando o seguinte comando:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Migre seu banco de dados usando Exportar e Importar](./howto-migrate-using-export-and-import.md)

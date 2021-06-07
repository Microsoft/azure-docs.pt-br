---
title: 'Início Rápido: Conectar-se com Ruby – Banco de Dados do Azure para PostgreSQL – Servidor único'
description: Este início rápido fornece um exemplo de código Ruby que você pode usar para se conectar e consultar dados do Banco de Dados do Azure para PostgreSQL – Servidor único.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc
ms.devlang: ruby
ms.topic: quickstart
ms.date: 5/6/2019
ms.openlocfilehash: 20e45454284af230da74896d5b3f5e9da676dbb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97831696"
---
# <a name="quickstart-use-ruby-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Início Rápido: Usar o Ruby para se conectar e consultar dados no Banco de Dados do Azure para PostgreSQL – servidor único

Este guia de início rápido demonstra como se conectar a um banco de dados do Azure para PostgreSQL usando aplicativo [Ruby](https://www.ruby-lang.org). Ele mostra como usar instruções SQL para consultar, inserir, atualizar e excluir dados no banco de dados. As etapas neste artigo pressupõem que você esteja familiarizado com o desenvolvimento usando Ruby e que tenha começado a trabalhar recentemente com o Banco de Dados do Azure para PostgreSQL.

## <a name="prerequisites"></a>Pré-requisitos
Este guia de início rápido usa os recursos criados em um destes guias como ponto de partida:
- [Criar BD – Portal](quickstart-create-server-database-portal.md)
- [Criar Banco de dados - CLI do Azure](quickstart-create-server-database-azure-cli.md)

Você também precisa ter instalados:
- [Ruby](https://www.ruby-lang.org/en/downloads/)
- [Ruby pg](https://rubygems.org/gems/pg/), o módulo PostgreSQL para Ruby

## <a name="get-connection-information"></a>Obter informações de conexão
Obtenha as informações de conexão necessárias para se conectar ao Banco de Dados do Azure para PostgreSQL. Você precisa das credenciais de logon e do nome do servidor totalmente qualificado.

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. No menu à esquerda no portal do Azure, clique em **Todos os recursos** e pesquise o servidor que você criou (como **mydemoserver**).
3. Clique no nome do servidor.
4. No painel **Visão Geral** do servidor, anote o **Nome do servidor** e **Nome de logon do administrador do servidor**. Se você esquecer sua senha, também poderá redefini-la nesse painel.
 :::image type="content" source="./media/connect-ruby/1-connection-string.png" alt-text="Nome do servidor do Banco de Dados do Azure para PostgreSQL":::

> [!NOTE]
> O símbolo `@` no nome de usuário Postgres do Azure tem sido codificado por URL como `%40` em todas as cadeias de conexão.

## <a name="connect-and-create-a-table"></a>Conectar-se e criar uma tabela
Use o código a seguir para se conectar e criar uma tabela usando a instrução SQL **CREATE TABLE**, seguida por instruções SQL **INSERT INTO** para adicionar linhas à tabela.

O código usa um objeto ```PG::Connection``` com o construtor ```new``` para se conectar ao Banco de Dados do Azure para PostgreSQL. Em seguida, ele chama o método ```exec()``` para executar os comandos DROP, CREATE TABLE e INSERT INTO. O código verifica erros usando a classe ```PG::Error```. Em seguida, ele chama o método ```close()``` para fechar a conexão antes de encerrar. Consulte a documentação de referência do Ruby PG para obter mais informações sobre essas classes e métodos.

Substitua as cadeias de caracteres `host`, `database`, `user` e `password` pelos seus próprios valores.


```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin%40mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database'

    # Drop previous table of same name if one exists
    connection.exec('DROP TABLE IF EXISTS inventory;')
    puts 'Finished dropping table (if existed).'

    # Drop previous table of same name if one exists.
    connection.exec('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);')
    puts 'Finished creating table.'

    # Insert some data into table.
    connection.exec("INSERT INTO inventory VALUES(1, 'banana', 150)")
    connection.exec("INSERT INTO inventory VALUES(2, 'orange', 154)")
    connection.exec("INSERT INTO inventory VALUES(3, 'apple', 100)")
    puts 'Inserted 3 rows of data.'

rescue PG::Error => e
    puts e.message

ensure
    connection.close if connection
end
```

## <a name="read-data"></a>Ler dados
Use o código a seguir para conectar-se e ler os dados usando uma instrução SQL **SELECT**.

O código usa um objeto ```PG::Connection``` com construtor ```new``` para se conectar ao Banco de Dados do Azure para PostgreSQL. Em seguida, ele chama o método ```exec()``` para executar o comando SELECT, mantendo os resultados em um conjunto de resultados. A coleção do conjunto de resultados é iterada usando o loop `resultSet.each do`, mantendo os valores da linha atual na variável `row`. O código verifica erros usando a classe ```PG::Error```. Em seguida, ele chama o método ```close()``` para fechar a conexão antes de encerrar. Consulte a documentação de referência do Ruby PG para obter mais informações sobre essas classes e métodos.

Substitua as cadeias de caracteres `host`, `database`, `user` e `password` pelos seus próprios valores.

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin%40mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :database => dbname, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    resultSet = connection.exec('SELECT * from inventory;')
    resultSet.each do |row|
        puts 'Data row = (%s, %s, %s)' % [row['id'], row['name'], row['quantity']]
    end

rescue PG::Error => e
    puts e.message

ensure
    connection.close if connection
end
```

## <a name="update-data"></a>Atualizar dados
Use o código a seguir para conectar-se e atualizar os dados usando uma instrução SQL **UPDATE**.

O código usa um objeto ```PG::Connection``` com construtor ```new``` para se conectar ao Banco de Dados do Azure para PostgreSQL. Em seguida, ele chama o método ```exec()``` para executar o comando UPDATE. O código verifica erros usando a classe ```PG::Error```. Em seguida, ele chama o método ```close()``` para fechar a conexão antes de encerrar. Consulte a documentação de referência do [Ruby PG](https://rubygems.org/gems/pg) para obter mais informações sobre essas classes e métodos.

Substitua as cadeias de caracteres `host`, `database`, `user` e `password` pelos seus próprios valores.

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin%40mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    # Modify some data in table.
    connection.exec('UPDATE inventory SET quantity = %d WHERE name = %s;' % [200, '\'banana\''])
    puts 'Updated 1 row of data.'

rescue PG::Error => e
    puts e.message

ensure
    connection.close if connection
end
```


## <a name="delete-data"></a>Excluir dados
Use o código a seguir para conectar-se e ler os dados usando uma instrução SQL **DELETE**.

O código usa um objeto ```PG::Connection``` com construtor ```new``` para se conectar ao Banco de Dados do Azure para PostgreSQL. Em seguida, ele chama o método ```exec()``` para executar o comando UPDATE. O código verifica erros usando a classe ```PG::Error```. Em seguida, ele chama o método ```close()``` para fechar a conexão antes de encerrar.

Substitua as cadeias de caracteres `host`, `database`, `user` e `password` pelos seus próprios valores.

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin%40mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    # Modify some data in table.
    connection.exec('DELETE FROM inventory WHERE name = %s;' % ['\'orange\''])
    puts 'Deleted 1 row of data.'

rescue PG::Error => e
    puts e.message

ensure
    connection.close if connection
end
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
> [Migre seu banco de dados usando Exportar e Importar](./howto-migrate-using-export-and-import.md) <br/>
> [!div class="nextstepaction"]
> [Documentação de referência do Ruby Pg](https://rubygems.org/gems/pg)

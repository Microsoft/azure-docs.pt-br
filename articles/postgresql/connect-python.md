---
title: 'Início Rápido: Conectar-se com Python – Banco de Dados do Azure para PostgreSQL – Servidor único'
description: Este início rápido fornece exemplos de código do Python que você pode usar para se conectar e consultar dados do Banco de Dados do Azure para PostgreSQL – servidor único.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devcenter, devx-track-python
ms.devlang: python
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: 6c589f3f0913609640ac14534c170c741c16a084
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048163"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Início Rápido: Usar o Python para se conectar e consultar dados no Banco de Dados do Azure para PostgreSQL – servidor único

Neste início rápido, você aprenderá a se conectar ao Banco de Dados do Azure para PostgreSQL servidor único e executar instruções SQL para consultar usando o Python em macOS, Ubuntu Linux ou Windows.

> [!TIP]
> Se você estiver procurando criar um aplicativo Django com PostgreSQL, confira o tutorial [Implantar um aplicativo Web Django com PostgreSQL](../app-service/tutorial-python-postgresql-app.md).


## <a name="prerequisites"></a>Pré-requisitos
Para este início rápido você precisa:

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free).
- Criar um Banco de Dados do Azure para PostgreSQL servidor único usando o [portal do Azure](./quickstart-create-server-database-portal.md) <br/> ou a [CLI do Azure](./quickstart-create-server-database-azure-cli.md) se ainda não tiver um.
- Com base em se você está usando o acesso público ou privado, conclua **UMA** das ações abaixo para habilitar a conectividade.

  |Ação| Método de conectividade|Guia de instruções|
  |:--------- |:--------- |:--------- |
  | **Configurar regras de firewall** | Público | [Portal](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
  | **Configurar Ponto de Extremidade de Serviço** | Público | [Portal](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)|
  | **Configurar link privado** | Privados | [Portal](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) |

- [Python](https://www.python.org/downloads/) 2.7 ou 3.6+.

- Instalador do pacote [pip](https://pip.pypa.io/en/stable/installing/) mais recente.
- Instale o [psycopg2](https://pypi.python.org/pypi/psycopg2/) usando `pip install psycopg2` em um terminal ou em uma janela do prompt de comando. Para obter mais informações, confira [como instalar `psycopg2`](http://initd.org/psycopg/docs/install.html).

## <a name="get-database-connection-information"></a>Obter informações da conexão de banco de dados
A conexão com um Banco de Dados do Azure para PostgreSQL requer o nome do servidor totalmente qualificado e as credenciais de logon. Você pode obter essas informações no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), procure e selecione o nome do servidor do Banco de Dados do Azure para PostgreSQL.
1. Na página **Visão Geral** do servidor, copie o **Nome do servidor** totalmente qualificado e o **Nome de usuário do administrador**. O **Nome do servidor** totalmente qualificado sempre está no formato *\<my-server-name>.postgres.database.azure.com* e **Nome do usuário administrador** sempre está no formato *\<my-admin-username>@\<my-server-name>* .

   Você também precisa da sua senha de administrador. Se a esquecer, será possível redefini-la nessa página.

   :::image type="content" source="./media/connect-python/1-connection-string.png" alt-text="Nome do servidor do Banco de Dados do Azure para PostgreSQL":::

> [!IMPORTANT]
>  Substitua os seguintes valores:
>   - `<server-name>` e `<admin-username>` pelos valores copiados do portal do Azure.
>   - `<admin-password>` pela sua senha de servidor.
>   - `<database-name>` um banco de dados padrão chamado *postgres* foi criado automaticamente quando você criou o servidor. É possível renomear esse banco de dados ou [criar outro](https://www.postgresql.org/docs/9.0/sql-createdatabase.html) usando os comandos SQL.

## <a name="step-1-connect-and-insert-data"></a>Etapa 1: conectar e inserir dados
O exemplo de código a seguir se conecta ao Banco de Dados do Azure para PostgreSQL usando
-  a função [psycopg2.connect](http://initd.org/psycopg/docs/connection.html) e carrega dados com uma instrução SQL **INSERT**.
- A função [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) executa a consulta SQL no banco de dados.

```Python
import psycopg2

# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string)
print("Connection established")

cursor = conn.cursor()

# Drop previous table of same name if one exists
cursor.execute("DROP TABLE IF EXISTS inventory;")
print("Finished dropping table (if existed)")

# Create a table
cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
print("Finished creating table")

# Insert some data into the table
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
print("Inserted 3 rows of data")

# Clean up
conn.commit()
cursor.close()
conn.close()
```

Quando o código é executado com êxito, ele produz a seguinte saída:

:::image type="content" source="media/connect-python/2-example-python-output.png" alt-text="Saída de linha de comando":::


[Está com problemas? Fale conosco](https://aka.ms/postgres-doc-feedback)

## <a name="step-2-read-data"></a>Etapa 2: Ler dados
O exemplo de código a seguir se conecta ao Banco de Dados do Azure para PostgreSQL e usa
- a função [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) com a instrução SQL **SELECT** para ler dados.
- A função [cursor.fetchall()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall) aceita uma consulta e retorna um conjunto de resultados a ser percorrido usando

```Python

# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()

# Print all rows
for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))


```
[Está com problemas? Fale conosco](https://aka.ms/postgres-doc-feedback)

## <a name="step-3-update-data"></a>Etapa 3: Atualizar dados
O exemplo de código a seguir usa a função [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) com a instrução SQL **UPDATE** para atualizar dados.

```Python

# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print("Updated 1 row of data")

```
[Está com problemas? Fale conosco](https://aka.ms/postgres-doc-feedback)

## <a name="step-5-delete-data"></a>Etapa 5: Excluir dados
O exemplo de código a seguir executa a função [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) com a instrução SQL **DELETE** para excluir um item do estoque inserido anteriormente.

```Python

# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print("Deleted 1 row of data")

```

[Está com problemas? Fale conosco](https://aka.ms/postgres-doc-feedback)

## <a name="clean-up-resources"></a>Limpar os recursos

Para limpar todos os recursos usados durante este guia de início rápido, exclua o grupo de recursos usando o seguinte comando:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Gerenciar o servidor de Banco de Dados do Azure para MySQL usando o portal](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [Gerenciar o servidor de Banco de Dados do Azure para MySQL usando a CLI](./how-to-manage-server-cli.md)<br/>

[Não foi possível encontrar o que estava procurando? Fale conosco.](https://aka.ms/postgres-doc-feedback)

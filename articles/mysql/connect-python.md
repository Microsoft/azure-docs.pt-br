---
title: 'Início Rápido: Conectar-se usando o Python – Banco de Dados do Azure para MySQL'
description: Este guia de início rápido fornece vários exemplos de código Python que você pode usar para se conectar e consultar dados do Banco de Dados do Azure para MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom:
- mvc
- seo-python-october2019
- devx-track-python
ms.devlang: python
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: a4391ecb7175b0e473b47cc3de43fd113795bc6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889018"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-mysql"></a>Início Rápido: Usar Python para se conectar e consultar dados no Banco de Dados do Azure para MySQL

Neste início rápido, você se conectará a um Banco de Dados do Azure para MySQL usando o Python. Você usará instruções SQL para consultar, inserir, atualizar e excluir dados no banco de dados de plataformas Windows, Ubuntu Linux e Mac. 

## <a name="prerequisites"></a>Pré-requisitos
Para este início rápido você precisa:

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free).
- Criar um servidor individual de Banco de Dados do Azure para MySQL usando o [portal do Azure](./quickstart-create-mysql-server-database-using-azure-portal.md) <br/> ou a [CLI do Azure](./quickstart-create-mysql-server-database-using-azure-cli.md) se ainda não tiver um.
- Com base em se você está usando o acesso público ou privado, conclua **UMA** das ações abaixo para habilitar a conectividade.

   |Ação| Método de conectividade|Guia de instruções|
   |:--------- |:--------- |:--------- |
   | **Configurar regras de firewall** | Público | [Portal](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
   | **Configurar Ponto de Extremidade de Serviço** | Público | [Portal](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)| 
   | **Configurar link privado** | Privados | [Portal](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) | 

- [Criar um banco de dados e um usuário não administrador](./howto-create-users.md)

## <a name="install-python-and-the-mysql-connector"></a>Instalar o conector MySQL e Python

Instale o Python e o conector MySQL para Python em seu computador usando as seguintes etapas: 

> [!NOTE]
> Este início rápido está usando o [Guia do Desenvolvedor para Conector MySQL/Python](https://dev.mysql.com/doc/connector-python/en/).

1. Baixe e instale o [Python 3.7 ou superior](https://www.python.org/downloads/) para o seu sistema operacional. É necessário que você adicione o Python ao seu `PATH`, pois o conector do MySQL exige isso.
   
2. Abra um prompt de comando ou shell do `bash` e verifique sua versão do Python, executando `python -V` com a opção V em maiúscula.
   
3. O instalador do pacote `pip` está incluído nas versões mais recentes do Python. Executando `pip install -U pip`, atualize o `pip` para a versão mais recente. 
   
   Se o `pip` não estiver instalado, você poderá baixá-lo e instalá-lo com `get-pip.py`. Para obter mais informações, confira [Instalação](https://pip.pypa.io/en/stable/installing/). 
   
4. Use o `pip` para instalar o conector do MySQL para Python e as respectivas dependências:
   
   ```bash
   pip install mysql-connector-python
   ```
   
[Está com problemas? Fale conosco](https://aka.ms/mysql-doc-feedback)

## <a name="get-connection-information"></a>Obter informações de conexão

Obtenha as informações de conexão das quais precisa para se conectar ao Banco de Dados do Azure para MySQL do portal do Azure. Você precisa das credenciais de logon, do nome do servidor e do nome do banco de dados.

1. Entre no [portal do Azure](https://portal.azure.com/).
   
1. Na barra de pesquisa do portal, procure pelo servidor de Banco de Dados do Azure para MySQL que você criou (com o nome que você deu a ele, como **mydemoserver**) e selecione-o.
   
   :::image type="content" source="./media/connect-python/1_server-overview-name-login.png" alt-text="Nome do servidor do Banco de Dados do Azure para MySQL":::
   
1. Na página de **Visão geral** do servidor, anote o **Nome do servidor** e o **Nome de logon do administrador do servidor**. Se você esquecer sua senha, também poderá redefini-la dessa página.
   
   :::image type="content" source="./media/connect-python/azure-database-for-mysql-server-overview-name-login.png" alt-text="Nome do servidor do Banco de Dados do Azure para MySQL 2":::

## <a name="step-1-create-a-table-and-insert-data"></a>Etapa 1: Criar uma tabela e inserir dados

Use o código a seguir para se conectar ao servidor e ao banco de dados, criar uma tabela e carregar os dados usando uma instrução SQL **INSERT**. O código importa a biblioteca mysql.connector e usa o método:
- [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) para se conectar ao Banco de Dados do Azure para MySQL usando os [argumentos](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) da coleção de configuração. 
- [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) para executar a consulta SQL no banco de dados MySQL. 
- [cursor.close()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-close.html) quando você terminar de usar um cursor.
- [conn.close()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlconnection-close.html) para fechar a conexão.

> [!IMPORTANT]
> - O SSL é habilitado por padrão. Talvez seja necessário baixar o [certificado SSL DigiCertGlobalRootG2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) para se conectar do ambiente local.
> - Substitua os espaços reservados `<mydemoserver>`, `<myadmin>`, `<mypassword>` e `<mydatabase>` pelos valores do servidor e do banco de dados MySQL.

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>',
  'client_flags': [mysql.connector.ClientFlag.SSL],
  'ssl_ca': '/var/wwww/html/DigiCertGlobalRootG2.crt.pem'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Drop previous table of same name if one exists
  cursor.execute("DROP TABLE IF EXISTS inventory;")
  print("Finished dropping table (if existed).")

  # Create table
  cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
  print("Finished creating table.")

  # Insert some data into table
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
  print("Inserted",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

[Está com problemas? Fale conosco](https://aka.ms/mysql-doc-feedback)

## <a name="step-2-read-data"></a>Etapa 2: Ler dados

Use o código a seguir para conectar-se e ler os dados usando uma instrução SQL **SELECT**. O código importa a biblioteca mysql.connector e usa o método [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) para executar a consulta SQL no banco de dados MySQL. 

O código lê as linhas de dados usando o método [fetchall()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html), mantém o conjunto de resultados em uma linha de coleção e usa um iterador `for` para executar um loop nas linhas.

```python
  # Read data
  cursor.execute("SELECT * FROM inventory;")
  rows = cursor.fetchall()
  print("Read",cursor.rowcount,"row(s) of data.")

  # Print all rows
  for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))

```

## <a name="step-3-update-data"></a>Etapa 3: Atualizar dados

Use o código a seguir para conectar-se e atualizar os dados usando uma instrução SQL **UPDATE**. O código importa a biblioteca mysql.connector e usa o método [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) para executar a consulta SQL no banco de dados MySQL. 

```python
  # Update a data row in the table
  cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
  print("Updated",cursor.rowcount,"row(s) of data.")
```

## <a name="step-4-delete-data"></a>Etapa 4: Excluir dados

Use o código a seguir para conectar-se e remover os dados usando uma instrução SQL **DELETE**. O código importa a biblioteca mysql.connector e usa o método [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) para executar a consulta SQL no banco de dados MySQL. 

```python

  # Delete a data row in the table
  cursor.execute("DELETE FROM inventory WHERE name=%(param1)s;", {'param1':"orange"})
  print("Deleted",cursor.rowcount,"row(s) of data.")
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
> [Gerenciar o servidor de Banco de Dados do Azure para MySQL usando o portal](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [Gerenciar o servidor de Banco de Dados do Azure para MySQL usando a CLI](./how-to-manage-single-server-cli.md)

[Não foi possível encontrar o que estava procurando? Fale conosco.](https://aka.ms/mysql-doc-feedback)

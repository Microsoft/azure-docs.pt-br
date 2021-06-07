---
title: 'Tutorial: Como migrar seu banco de dados SQLite para o Banco de Dados SQL do Azure sem servidor'
description: Saiba como fazer uma migração offline do SQLite para o Banco de Dados SQL do Azure sem servidor usando o Azure Data Factory.
services: sql-database
author: joplum
ms.author: joplum
ms.service: sql-database
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/08/2020
ms.custom: sqldbrb=1
ms.openlocfilehash: e2f240247cbba0f80254d504792df45be55c6a1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92790399"
---
# <a name="how-to-migrate-your-sqlite-database-to-azure-sql-database-serverless"></a>Como migrar seu banco de dados SQLite para o Banco de Dados SQL do Azure sem servidor
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Para muitas pessoas, o SQLite fornece a primeira experiência de bancos de dados e programação SQL. A inclusão do SQLite em muitos sistemas operacionais e aplicativos populares faz dele um dos mecanismos de banco de dados mais implantados e utilizados no mundo todo. Como é provável que ele seja o primeiro mecanismo de banco de dados utilizado por muitas pessoas, com frequência, ele pode acabar se tornando uma parte central de projetos ou aplicativos. Nesses casos em que o projeto ou o aplicativo excede a implementação inicial do SQLite, os desenvolvedores podem precisar migrar os dados para um armazenamento de dados confiável e centralizado.

O Banco de Dados SQL do Azure sem servidor é uma camada de computação para bancos de dados individuais que escala automaticamente a computação com base na demanda da carga de trabalho e cobra pela quantidade de computação usada por segundo. A camada de computação sem servidor também pausa os bancos de dados automaticamente durante períodos inativos quando apenas o armazenamento é cobrado e retoma automaticamente os bancos de dados quando a atividade retorna.

Depois de seguir as etapas abaixo, seu banco de dados será migrado para o Banco de Dados SQL do Azure sem servidor, permitindo que você disponibilize o banco de dados para outros usuários ou aplicativos na nuvem e pague apenas pelo que usar, com alterações mínimas no código do aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure
- Banco de dados SQLite2 ou SQLite3 que você deseja migrar
- Um ambiente do Windows
  - Se você não tiver um ambiente local do Windows, use uma VM do Windows no Azure para a migração. Mova seu arquivo de banco de dados SQLite e disponibilize-o na VM usando os Arquivos do Azure e o Gerenciador de Armazenamento.

## <a name="steps"></a>Etapas

1. Provisione um novo Banco de Dados SQL do Azure na camada de computação sem servidor.

    ![Captura de tela do portal do Azure mostrando o exemplo de provisionamento para o Banco de Dados SQL do Azure sem servidor](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/provision-serverless.png)

2. Verifique se o arquivo de banco de dados SQLite está disponível no ambiente do Windows. Instale um Driver ODBC do SQLite caso ainda não tenha um (há muitos disponíveis em software livre, por exemplo, http://www.ch-werner.de/sqliteodbc/).

3. Crie um DSN de sistema para o banco de dados. Use o aplicativo Administrador de Fonte de Dados que corresponde à arquitetura do sistema (32 bits vs 64 bits). Encontre qual versão executada nas configurações do sistema.

    - Abra o Administrador de Fonte de Dados ODBC no ambiente.
    - Clique na guia DSN do sistema e clique em "Adicionar"
    - Selecione o conector ODBC do SQLite instalado e dê à conexão um nome significativo, por exemplo, sqlitemigrationsource
    - Defina o nome do banco de dados para o arquivo .db
    - Salvar e sair

4. Baixe e instale o runtime de integração auto-hospedada. A maneira mais fácil de fazer isso é a opção de instalação Expressa, conforme detalhado na documentação. Se você optar por fazer uma instalação manual, precisará fornecer ao aplicativo uma chave de autenticação, que pode ser localizada na sua instância do Data Factory:

    - Iniciando o ADF (Criar e Monitorar no serviço no portal do Azure)
    - Clique na guia "Criar" (lápis azul) à esquerda
    - Clique em Conexões (canto inferior esquerdo) e em Runtimes de integração
    - Adicione um novo runtime de integração auto-hospedada, dê a ele um nome e selecione *Opção 2*.

5. Crie um serviço vinculado para o banco de dados SQLite de origem no Data Factory.

    ![Captura de tela mostrando a folha de serviços vinculados vazia no Azure Data Factory](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-create.png)

6. Em **Conexões**, em **Serviço Vinculado**, clique em **Novo**.

7. Pesquisar e selecionar o conector "ODBC"

   ![Captura de tela mostrando o logotipo do conector ODBC na folha de serviços vinculados no Azure Data Factory](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-odbc.png)

8. Dê ao serviço vinculado um nome significativo, por exemplo, "sqlite_odbc". Selecione o runtime de integração na lista suspensa "Conectar-se por meio do runtime de integração". Insira as informações abaixo na cadeia de conexão, substituindo a variável de Catálogo Inicial pelo caminho de arquivo. db e o DSN pelo nome da conexão DSN do sistema:

   ```
   Connection string: Provider=MSDASQL.1;Persist Security Info=False;Mode=ReadWrite;Initial Catalog=C:\sqlitemigrationsource.db;DSN=sqlitemigrationsource
    ```

9. Defina o tipo de autenticação como Anônimo

10. Testar a conexão

    ![Captura de tela mostrando a conexão bem-sucedida no Azure Data Factory](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-test-successful.png)

11. Crie outro serviço vinculado para o destino SQL sem servidor. Selecione o banco de dados usando o assistente de serviço vinculado e forneça as credenciais de autenticação do SQL.

    ![Captura de tela mostrando o Banco de Dados SQL do Azure selecionado no Azure Data Factory](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-create-target.png)

12. Extraia as instruções CREATE TABLE do banco de dados SQLite. Faça isso executando o script do Python abaixo no arquivo de banco de dados.

    ```
    #!/usr/bin/python
    import sqlite3
    conn = sqlite3.connect("sqlitemigrationsource.db")
    c = conn.cursor()

    print("Starting extract job..")
    with open('CreateTables.sql', 'w') as f:
        for tabledetails in c.execute("SELECT * FROM sqlite_master WHERE type='table'"):
            print("Extracting CREATE statement for " + (str(tabledetails[1])))
            print(tabledetails)
            f.write(str(tabledetails[4].replace('\n','') + ';\n'))
    c.close()
    ```

13. Crie as tabelas de destino no ambiente de destino SQL sem servidor copiando as instruções CREATE TABLE do arquivo CreateTables.sql e executando as instruções SQL no Editor de Consultas no portal do Azure.

14. Volte à tela inicial do Data Factory e clique em "Copiar Dados" para executar o assistente de criação de trabalho.

    ![Captura de tela mostrando o logotipo do assistente Copiar Dados no Azure Data Factory](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/copy-data.png)

15. Selecione todas as tabelas do banco de dados SQLite de origem usando as caixas de seleção e mapeie-as para as tabelas de destino no SQL do Azure. Após a execução do trabalho, você terá migrado com êxito os dados do SQLite para o SQL do Azure.

## <a name="next-steps"></a>Próximas etapas

- Para começar, confira [Início rápido: Criar um banco de dados individual no Banco de Dados SQL do Azure usando o portal do Azure](single-database-create-quickstart.md).
- Para limites de recursos, consulte [Limites de recursos de camada de computação sem servidor](./resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5).
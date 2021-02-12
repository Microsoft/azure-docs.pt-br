---
title: Executar consultas do Apache base no Azure HDInsight com o Apache Phoenix
description: Saiba como usar o Apache Zeppelin para executar consultas do Apache base com o Phoenix.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 10/14/2019
ms.openlocfilehash: 50a72d0400b23162e05b17b37bdad48783261072
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944767"
---
# <a name="use-apache-zeppelin-to-run-apache-phoenix-queries-over-apache-hbase-in-azure-hdinsight"></a>Usar o Apache Zeppelin para executar consultas de Apache Phoenix sobre o Apache HBase no Azure HDInsight

Apache Phoenix é um banco de dados relacional livre e paralelo em massa criado em camadas em HBase. O Phoenix permite que você use consultas SQL como no HBase. O Phoenix usa drivers JDBC abaixo para permitir que você crie, exclua, altere tabelas SQL, índices, exibições e sequências.  Você também pode usar Phoenix para atualizar linhas individualmente e em massa. O Phoenix usa uma compilação nativa NOSQL em vez de usar o MapReduce para compilar consultas, permitindo a criação de aplicativos de baixa latência sobre o HBase.

O Apache Zeppelin é um notebook baseado na Web de software livre que permite que você crie documentos colaborativos controlados por dados usando análises de dados interativas e linguagens como SQL e escala. Ele ajuda os desenvolvedores de dados & cientistas de dados a desenvolver, organizar, executar e compartilhar código para manipulação de dados. Ele permite visualizar os resultados sem referir-se à linha de comando ou precisar dos detalhes do cluster.

Os usuários do HDInsight podem usar o Apache Zeppelin para consultar tabelas Phoenix. O Apache Zeppelin é integrado ao cluster HDInsight e não há etapas adicionais para usá-lo. Basta criar um notebook Zeppelin com o intérprete JDBC e começar a gravar suas consultas SQL Phoenix

## <a name="prerequisites"></a>Pré-requisitos

Um cluster do Apache HBase no HDInsight. Consulte [introdução ao Apache HBase](./apache-hbase-tutorial-get-started-linux.md).

## <a name="create-an-apache-zeppelin-note"></a>Criar uma anotação do Apache Zeppelin

1. Substitua `CLUSTERNAME` pelo nome do cluster na seguinte URL `https://CLUSTERNAME.azurehdinsight.net/zeppelin`. Em seguida, insira a URL em um navegador da Web. Insira o nome de usuário e a senha de logon do cluster.

1. Na página Zeppelin, selecione **criar nova anotação**.

    ![HDInsight Consulta Interativa Zeppelin](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-create-note.png)

1. Na caixa de diálogo **Criar anotação**, digite ou selecione os seguintes valores:

    - Nome da observação: Insira um nome para a nota.
    - Intérprete padrão: selecione **JDBC** na lista suspensa.

    Em seguida, selecione **criar anotação**.

1. Verifique se o cabeçalho do bloco de anotações mostra um status conectado. Ele é indicado por um ponto verde no canto superior direito.

    ![Status do bloco de anotações do Zeppelin](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-connected.png "Status do bloco de anotações do Zeppelin")

1. Crie uma tabela do HBase. Insira o comando a seguir e pressione **Shift + Enter**:

    ```sql
    %jdbc(phoenix)
    CREATE TABLE Company (
        company_id INTEGER PRIMARY KEY,
        name VARCHAR(225)
    );
    ```

    A instrução **% JDBC (Phoenix)** na primeira linha informa ao bloco de anotações para usar o intérprete JDBC do Phoenix.

1. Exibir tabelas criadas.

    ```sql
    %jdbc(phoenix)
    SELECT DISTINCT table_name
    FROM SYSTEM.CATALOG
    WHERE table_schem is null or table_schem <> 'SYSTEM';
    ```

1. Insira os valores na tabela.

    ```sql
    %jdbc(phoenix)
    UPSERT INTO dbo.Company VALUES(1, 'Microsoft');
    UPSERT INTO dbo.Company (name, company_id) VALUES('Apache', 2);
    ```

1. Consulte a tabela.

    ```sql
    %jdbc(phoenix)
    SELECT * FROM dbo.Company;
    ```

1. Exclua um registro.

    ```sql
    %jdbc(phoenix)
    DELETE FROM dbo.Company WHERE COMPANY_ID=1;
    ```

1. Descarte a tabela.

    ```sql
    %jdbc(phoenix)
    DROP TABLE dbo.Company;
    ```

## <a name="next-steps"></a>Próximas etapas

- [Apache Phoenix agora dá suporte a Zeppelin no Azure HDInsight](/archive/blogs/ashish/apache-phoenix-now-supports-zeppelin-in-azure-hdinsight)
- [Apache Phoenix gramática](https://phoenix.apache.org/language/index.html)
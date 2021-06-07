---
title: Importar e exportar-banco de dados do Azure para MySQL
description: Este artigo explica formas comuns de importar e exportar bancos de dados no Banco de Dados do Azure para MySQL, usando ferramentas como o MySQL Workbench.
author: savjani
ms.author: pariks
ms.service: mysql
ms.subservice: migration-guide
ms.topic: conceptual
ms.date: 10/30/2020
ms.openlocfilehash: 049a0ad45ea82210d8fac28db0fb3d067841bba4
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625136"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>Migrar o banco de dados MySQL usando importação e exportação

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Este artigo explica duas abordagens comuns de importação e exportação de dados para um Banco de Dados do Azure para MySQL Server usando o MySQL Workbench.

Para obter diretrizes de migração detalhadas e abrangentes, consulte os [recursos do guia de migração](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide). 

Para outros cenários de migração, consulte o [Guia de migração de banco de dados](https://datamigration.microsoft.com/). 

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a migrar seu banco de dados MySQL, você precisa:
- Crie um [banco de dados do Azure para o servidor MySQL usando o portal do Azure](quickstart-create-mysql-server-database-using-azure-portal.md).
- Baixe e instale o [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) ou outra ferramenta MySQL de terceiros para importar e exportar.

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Criar um banco de dados no Banco de Dados do Azure para MySQL Server
Crie um banco de dados vazio no banco de dados do Azure para o servidor MySQL usando o MySQL Workbench, Toad ou Navicat. O banco de dados pode ter o mesmo nome do banco de dados que contém os dados despejados ou você pode criar um banco de dados com um nome diferente.

Para se conectar, faça o seguinte:

1. Na portal do Azure, procure as informações de conexão no painel **visão geral** do banco de dados do Azure para MySQL.

   :::image type="content" source="./media/concepts-migrate-import-export/1_server-overview-name-login.png" alt-text="Captura de tela das informações de conexão do servidor de banco de dados do Azure para MySQL no portal do Azure.":::

1. Adicione as informações de conexão ao MySQL Workbench.

   :::image type="content" source="./media/concepts-migrate-import-export/2_setup-new-connection.png" alt-text="Cadeia de conexão do MySQL Workbench":::

## <a name="determine-when-to-use-import-and-export-techniques"></a>Determinar quando usar as técnicas de importação e exportação

> [!TIP]
> Para cenários em que você deseja despejar e restaurar o banco de dados inteiro, use a abordagem de [despejo e restauração](concepts-migrate-dump-restore.md) em vez disso.

Nos cenários a seguir, use as ferramentas do MySQL para importar e exportar bancos de dados para o banco de dados MySQL. Para outras ferramentas, vá para a seção "métodos de migração" (página 22) do [Guia de migração do MySQL para o banco de dados do Azure](https://github.com/Azure/azure-mysql/blob/master/MigrationGuide/MySQL%20Migration%20Guide_v1.1.pdf). 

- Quando você precisa escolher seletivamente algumas tabelas para importar de um banco de dados MySQL existente para o banco de dados MySQL do Azure, é melhor usar a técnica de importação e exportação.  Ao fazer isso, você pode omitir todas as tabelas desnecessárias da migração, para economizar tempo e recursos. Por exemplo, use a opção `--include-tables` ou `--exclude-tables` com [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) e a opção `--tables` com [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- Quando você estiver movendo objetos de banco de dados diferentes de tabelas, crie explicitamente esses objetos. Inclua restrições (chave primária, chave estrangeira e índices), exibições, funções, procedimentos, gatilhos e outros objetos de banco de dados que você deseja migrar.
- Quando estiver migrando dados de fontes de dados externas que não sejam um banco de dados MySQL, crie arquivos simples e importe-os usando [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html).

> [!Important]
> O servidor único e o servidor flexível dão suporte *apenas ao mecanismo de armazenamento InnoDB*. Certifique-se de que todas as tabelas no banco de dados usem o mecanismo de armazenamento InnoDB quando você estiver carregando dados em seu banco de dado do Azure para MySQL.
>
> Se o banco de dados de origem usar outro mecanismo de armazenamento, converta para o mecanismo InnoDB antes de migrar o banco de dados. Por exemplo, se você tiver um aplicativo Web ou do WordPress que usa o mecanismo MyISAM, primeiro converta as tabelas migrando os dados para tabelas do InnoDB. Use a cláusula `ENGINE=INNODB` para definir o mecanismo para criar uma tabela e, em seguida, transfira os dados para a tabela compatível antes da migração.

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Recomendações de desempenho para importação e exportação

Para melhor desempenho de importação e exportação de dados, recomendamos que você faça o seguinte:
-   Crie índices clusterizados e chaves primárias antes de carregar os dados. Carregue os dados na ordem de chave primária.
-   Atrase a criação de índices secundários até que os dados sejam carregados.
-   Desabilite as restrições de chave estrangeira antes de carregar os dados. Desabilitar as verificações de chave estrangeira proporciona ganhos significativos de desempenho. Habilite as restrições e verifique os dados após o carregamento para garantir a integridade referencial.
-   Carregar dados em paralelo. Evite o excesso de paralelismo que poderá fazer com que você atinja um limite de recursos e monitore os recursos usando as métricas disponíveis no portal do Azure.
-   Use tabelas particionadas, quando apropriado.

## <a name="import-and-export-data-by-using-mysql-workbench"></a>Importar e exportar dados usando o MySQL Workbench
Há duas maneiras de exportar e importar dados no MySQL Workbench: no menu de contexto do pesquisador de objetos ou no painel navegador. Cada método atende a uma finalidade diferente.

> [!NOTE]
> Se você estiver adicionando uma conexão ao servidor único MySQL ou ao servidor flexível (versão prévia) no MySQL Workbench, faça o seguinte:
> - Para o servidor único MySQL, verifique se o nome de usuário está no formato *\<username@servername>* .
> - Para servidor flexível MySQL, use *\<username>* apenas. Se você usar *\<username@servername>* o para se conectar, a conexão falhará.

### <a name="run-the-table-data-export-and-import-wizards-from-the-object-browser-context-menu"></a>Executar os assistentes de importação e exportação de dados da tabela no menu de contexto do pesquisador de objetos

:::image type="content" source="./media/concepts-migrate-import-export/p1.png" alt-text="Captura de tela dos comandos do assistente de exportação e importação do MySQL Workbench no menu de contexto do pesquisador de objetos.":::

Os assistentes de dados de tabela oferecem suporte a operações de importação e exportação usando arquivos CSV e JSON. Os assistentes incluem várias opções de configuração, como separadores, seleção de coluna e seleção de codificação. Você pode executar cada assistente em servidores MySQL locais ou conectados remotamente. A ação de importação inclui o mapeamento de tabela, coluna e tipo.

Para acessar esses assistentes no menu de contexto do pesquisador de objetos, clique com o botão direito do mouse em uma tabela e selecione **Assistente de exportação de dados de tabela** ou assistente de importação de dados de **tabela**.

#### <a name="the-table-data-export-wizard"></a>O assistente de exportação de dados de tabela

Para exportar uma tabela para um arquivo CSV:

1. Clique com o botão direito do mouse na tabela do banco de dados a ser exportada.
1. Selecione **Assistente de Exportação de Dados de Tabela**. Selecione as colunas a serem exportadas, o deslocamento da linha (se houver) e a contagem (se houver).
1. No painel **selecionar dados para exportação** , selecione **Avançar**. Selecione o caminho do arquivo, o CSV ou o tipo de arquivo JSON. Selecione também o separador de linha, o método de delimitar cadeias de caracteres e o separador de campo.
1. No painel **selecionar local do arquivo de saída** , selecione **Avançar**.
1. No painel **exportar dados** , selecione **Avançar**.

#### <a name="the-table-data-import-wizard"></a>O assistente de importação de dados de tabela

Para importar uma tabela de um arquivo CSV:

1. Clique com o botão direito do mouse na tabela do banco de dados a ser importada.
1. Procure e selecione o arquivo CSV a ser importado e, em seguida, selecione **Avançar**.
1. Selecione a tabela de destino (nova ou existente), marque ou desmarque a caixa de seleção **Truncar tabela antes de importar** e, em seguida, selecione **Avançar**.
1. Selecione a codificação e as colunas a serem importadas e, em seguida, selecione **Avançar**.
1. No painel **importar dados** , selecione **Avançar**. O assistente importa os dados.

### <a name="run-the-sql-data-export-and-import-wizards-from-the-navigator-pane"></a>Executar os assistentes de exportação e importação de dados SQL no painel navegador
Use um assistente para exportar ou importar dados SQL gerados no MySQL Workbench ou no comando mysqldump. Você pode acessar os assistentes no painel **navegador** ou pode selecionar **servidor** no menu principal.

#### <a name="export-data"></a>Exportar dados

:::image type="content" source="./media/concepts-migrate-import-export/p2.png" alt-text="Captura de tela do uso do painel navegador para exibir o painel exportação de dados no MySQL Workbench.":::

Você pode usar o painel **exportação de dados** para exportar os dados do MySQL.

1. No MySQL Workbench, no painel **navegador** , selecione **exportação de dados**.

1. No painel **exportação de dados** , selecione cada esquema que você deseja exportar.
 
   Para cada esquema, você pode selecionar objetos ou tabelas de esquema específicos para exportar. As opções de configuração incluem exportar para uma pasta de projeto ou um arquivo SQL independente, despejar rotinas e eventos armazenados ou ignorar dados de tabela.

   Como alternativa, use **exportar um conjunto de resultados** para exportar um conjunto de resultados específico no editor SQL para outro formato, como CSV, JSON, HTML e XML.

1. Selecione os objetos de banco de dados a serem exportados e configure as opções relacionadas.
1. Selecione **Atualizar** para carregar os objetos atuais.
1. Opcionalmente, selecione **Opções avançadas** no canto superior direito para refinar a operação de exportação. Por exemplo, adicione bloqueios de tabela, use instruções de substituição em vez de inserção e identificadores de aspas com caracteres de acento grave.
1. Selecione **Iniciar exportação** para iniciar o processo de exportação.


#### <a name="import-data"></a>Importar dados

:::image type="content" source="./media/concepts-migrate-import-export/p3.png" alt-text="Captura de tela do uso do painel navegador para exibir o painel importação de dados no MySQL Workbench.":::

Você pode usar o painel **importação de dados** para importar ou restaurar dados exportados da operação de exportação de dados ou do comando mysqldump.

1. No MySQL Workbench, no painel **navegador** , selecione **exportação/restauração de dados**.
1. Selecione a pasta do projeto ou o arquivo SQL independente, selecione o esquema para importar ou selecione o botão **novo** para definir um novo esquema.
1. Selecione **Iniciar importação** para iniciar o processo de importação.

## <a name="next-steps"></a>Próximas etapas
- Para obter outra abordagem de migração, consulte [migrar seu banco de dados MySQL para um banco de dados do Azure para MySQL usando despejo e restauração](concepts-migrate-dump-restore.md).
- Para obter mais informações sobre como migrar bancos de dados para um banco de dados do Azure para MySQL, consulte o [Guia de migração de banco de dados](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide).

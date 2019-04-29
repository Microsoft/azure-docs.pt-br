---
title: Importar e exportar no Banco de Dados do Azure para MySQL
description: Este artigo explica formas comuns de importar e exportar bancos de dados no Banco de Dados do Azure para MySQL, usando ferramentas como o MySQL Workbench.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/01/2018
ms.openlocfilehash: fa72037c8f54271f5651667765c5d5e2e9c03619
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60838027"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>Migrar o banco de dados MySQL usando importação e exportação
Este artigo explica duas abordagens comuns de importação e exportação de dados para um Banco de Dados do Azure para MySQL Server usando o MySQL Workbench. 

## <a name="before-you-begin"></a>Antes de começar
Para seguir este guia de instruções, você precisa:
- Um Banco de Dados do Azure para MySQL Server, seguindo [Criar um Banco de Dados do Azure para MySQL Server usando o portal do Azure](quickstart-create-mysql-server-database-using-azure-portal.md).
- [Download](https://dev.mysql.com/downloads/workbench/) do MySQL Workbench ou de outra ferramenta do MySQL para importação e exportação.

## <a name="use-common-tools"></a>Usar ferramentas comuns
Use ferramentas comuns, como MySQL Workbench, Toad ou Navicat para se conectar remotamente e importar ou exportar dados para o Banco de Dados do Azure para MySQL. 

Use essas ferramentas no computador cliente com uma conexão à Internet para se conectar ao Banco de Dados do Azure para MySQL. Use uma conexão criptografada por SSL para as melhores práticas de segurança, conforme descrito em [Configurar a conectividade SSL no Banco de Dados do Azure para MySQL](concepts-ssl-connection-security.md).

Você não precisa mover os seus arquivos de importação e exportação para nenhum local específico na nuvem ao migrar para o Banco de Dados do Azure para MySQL. 

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Criar um banco de dados no Banco de Dados do Azure para MySQL Server
Crie um banco de dados vazio no Banco de Dados do Azure para MySQL Server para o qual você deseja migrar os dados. Use uma ferramenta como o MySQL Workbench, Toad ou Navicat para criar o banco de dados. O banco de dados pode ter o mesmo nome do banco de dados que contém os dados despejados ou você pode criar um banco de dados com um nome diferente.

Para se conectar, localize as informações de conexão na página **Visão Geral** do Banco de Dados do Azure para MySQL.

![Encontrar as informações de conexão no portal do Azure](./media/concepts-migrate-import-export/1_server-overview-name-login.png)

Adicione as informações de conexão ao MySQL Workbench.

![Cadeia de conexão do MySQL Workbench](./media/concepts-migrate-import-export/2_setup-new-connection.png)

## <a name="determine-when-to-use-import-and-export-techniques-instead-of-a-dump-and-restore"></a>Determinar quando usar as técnicas de importação e exportação em vez de um despejo e uma restauração
Use ferramentas do MySQL para importar e exportar bancos de dados para o Banco de Dados MySQL do Azure nos cenários a seguir. Em outros cenários, você pode se beneficiar do uso da abordagem de [despejo e restauração](concepts-migrate-dump-restore.md). 

- Quando você precisa escolher seletivamente algumas tabelas a serem importadas de um banco de dados MySQL existente para o banco de dados MySQL do Azure, é melhor usar a técnica de importação e exportação.  Ao fazer isso, você pode omitir todas as tabelas desnecessárias da migração, para economizar tempo e recursos. Por exemplo, use a opção `--include-tables` ou `--exclude-tables` com [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) e a opção `--tables` com [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- Quando estiver movendo os objetos de banco de dados que não sejam tabelas, crie-os explicitamente. Inclua restrições (chave primária, chave estrangeira, índices), exibições, funções, procedimentos, gatilhos e todos os outros objetos de banco de dados que você deseja migrar.
- Quando estiver migrando dados de fontes de dados externas que não sejam um banco de dados MySQL, crie arquivos simples e importe-os usando [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html).

Verifique se todas as tabelas no banco de dados usam o mecanismo de armazenamento do InnoDB quando estiver carregando dados no Banco de Dados do Azure para MySQL. O Banco de Dados do Azure para MySQL dá suporte apenas ao mecanismo de armazenamento do InnoDB e, portanto, não dá suporte a mecanismos de armazenamento alternativos. Se as tabelas exigirem mecanismos de armazenamento alternativos, converta-as para usar o formato do mecanismo do InnoDB antes da migração para o Banco de Dados do Azure para MySQL. 

Por exemplo, se você tiver um aplicativo Web ou do WordPress que usa o mecanismo MyISAM, primeiro converta as tabelas migrando os dados para tabelas do InnoDB. Em seguida, restaure-as para o Banco de Dados do Azure para MySQL. Use a cláusula `ENGINE=INNODB` para definir o mecanismo para criar uma tabela e, em seguida, transfira os dados para a tabela compatível antes da migração. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Recomendações de desempenho para importação e exportação
-   Crie índices clusterizados e chaves primárias antes de carregar os dados. Carregue os dados na ordem de chave primária. 
-   Atrase a criação de índices secundários até os dados serem carregados. Crie todos os índices secundários após o carregamento. 
-   Desabilite as restrições de chave estrangeira antes do carregamento. Desabilitar as verificações de chave estrangeira proporciona ganhos significativos de desempenho. Habilite as restrições e verifique os dados após o carregamento para garantir a integridade referencial.
-   Carregue os dados em paralelo. Evite o excesso de paralelismo que poderá fazer com que você atinja um limite de recursos e monitore os recursos usando as métricas disponíveis no portal do Azure. 
-   Use tabelas particionadas, quando apropriado.

## <a name="import-and-export-by-using-mysql-workbench"></a>Importar e exportar usando o MySQL Workbench
Há duas maneiras de exportar e importar dados no MySQL Workbench. Cada uma tem uma finalidade diferente. 

### <a name="table-data-export-and-import-wizards-from-the-object-browsers-context-menu"></a>Assistentes de importação e exportação de dados de tabela no menu de contexto do pesquisador de objetos
![Assistentes do MySQL Workbench no menu de contexto do pesquisador de objetos](./media/concepts-migrate-import-export/p1.png)

Os assistentes de dados de tabela dão suporte à importação e exportação de operações usando arquivos CSV e JSON. Eles incluem várias opções de configuração, como separadores, seleção de coluna e seleção de codificação. Execute cada assistente em MySQL Servers locais ou remotamente conectados. A ação de importação inclui o mapeamento de tabela, coluna e tipo. 

Acesse esses assistentes no menu de contexto do pesquisador de objetos clicando com o botão direito do mouse em uma tabela. Em seguida, escolha **Assistente de Exportação de Dados de Tabela** ou **Assistente de Importação de Dados de Tabela**. 

#### <a name="table-data-export-wizard"></a>Assistente de Exportação de Dados de Tabela
O seguinte exemplo exporta a tabela para um arquivo CSV: 
1. Clique com o botão direito do mouse na tabela do banco de dados a ser exportada. 
2. Selecione **Assistente de Exportação de Dados de Tabela**. Selecione as colunas a serem exportadas, o deslocamento da linha (se houver) e a contagem (se houver). 
3. Na página **Selecionar dados para exportação**, clique em **Avançar**. Selecione o caminho do arquivo, o CSV ou o tipo de arquivo JSON. Selecione também o separador de linha, o método de delimitar cadeias de caracteres e o separador de campo. 
4. Na página **Selecionar local do arquivo de saída**, clique em **Avançar**. 
5. Na página **Exportar dados**, clique em **Avançar**.

#### <a name="table-data-import-wizard"></a>Assistente de Importação de Dados de Tabela
O seguinte exemplo importa a tabela de um arquivo CSV:
1. Clique com o botão direito do mouse na tabela do banco de dados a ser importada. 
2. Procure e selecione o arquivo CSV a ser importado e, em seguida, clique no botão **Avançar**. 
3. Selecione a tabela de destino (nova ou existente) e marque ou desmarque a caixa de seleção **Truncar tabela antes da importação**. Clique em **Avançar**.
4. Selecione a codificação e as colunas a serem importadas e, em seguida, clique em **Avançar**. 
5. Na página **Importar dados**, clique em **Avançar**. O assistente importa os dados de acordo.

### <a name="sql-data-export-and-import-wizards-from-the-navigator-pane"></a>Assistentes de exportação e importação de dados do SQL no painel Navegador
Use um assistente para exportar ou importar o SQL gerado no MySQL Workbench ou gerado com o comando mysqldump. Acesse esses assistentes no painel **Navegador** ou selecionando **Servidor** no menu principal. Em seguida, selecione **Exportação de Dados** ou **Importação de Dados**. 

#### <a name="data-export"></a>Exportação de Dados
![Exportação de dados do MySQL Workbench usando o painel Navegador](./media/concepts-migrate-import-export/p2.png)

Use a guia **Exportação de Dados** para exportar os dados do MySQL. 
1. Selecione cada esquema que deseja exportar, opcionalmente, escolha objetos/tabelas do esquema específicos de cada esquema e gere a exportação. As opções de configuração incluem a exportação para uma pasta de projeto ou um arquivo SQL independente, despejo de rotinas e eventos armazenados ou ignorar os dados da tabela. 
 
   Como alternativa, use **Exportar um Conjunto de Resultados** para exportar um conjunto de resultados específico no editor de SQL para outro formato como CSV, JSON, HTML e XML. 
3. Selecione os objetos de banco de dados a serem exportados e configure as opções relacionadas.
4. Clique em **Atualizar** para carregar os objetos atuais.
5. Opcionalmente, abra a guia **Opções Avançadas** para refinar a operação de exportação. Por exemplo, adicione bloqueios de tabela, use instruções de substituição em vez de inserção e identificadores de aspas com caracteres de acento grave.
6. Clique em **Iniciar Exportação** para iniciar o processo de exportação.


#### <a name="data-import"></a>Importação de Dados
![Importação de Dados do MySQL Workbench usando o Navegador de Gerenciamento](./media/concepts-migrate-import-export/p3.png)

Use a guia **Importação de Dados** para importar ou restaurar os dados exportados da operação de exportação de dados ou do comando mysqldump. 
1. Escolha a pasta de projeto ou o arquivo SQL independente, o esquema no qual importar ou **Novo** para definir um novo esquema. 
2. Clique em **Iniciar Importação** para iniciar o processo de importação.

## <a name="next-steps"></a>Próximas etapas
- Como outra abordagem de migração, leia [Migrar o banco de dados MySQL usando o despejo e a restauração no Banco de Dados do Azure para MySQL](concepts-migrate-dump-restore.md).
- Para obter mais informações de como migrar bancos de dados para o Banco de Dados do Azure para MySQL, confira o [Guia de Migração de Banco de Dados](https://aka.ms/datamigration). 

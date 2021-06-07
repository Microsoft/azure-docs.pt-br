---
title: 'Oracle para banco de dados do Azure para PostgreSQL: guia de migração'
titleSuffix: Azure Database for PostgreSQL
description: Este guia ensina a migrar seu esquema Oracle para o banco de dados do Azure para PostgreSQL.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: 1a20ffd7150ac75721b2affc2f4375301c4754c8
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643575"
---
# <a name="migrate-oracle-to-azure-database-for-postgresql"></a>Migrar o Oracle para o banco de dados do Azure para PostgreSQL

Este guia ensina a migrar seu esquema Oracle para o banco de dados do Azure para PostgreSQL. 

Para obter diretrizes de migração detalhadas e abrangentes, consulte os [recursos do guia de migração](https://github.com/microsoft/OrcasNinjaTeam/blob/master/Oracle%20to%20PostgreSQL%20Migration%20Guide/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Guide.pdf). 

## <a name="prerequisites"></a>Pré-requisitos

Para migrar seu esquema Oracle para o banco de dados do Azure para PostgreSQL, você precisa: 

- Verifique se há suporte para o ambiente de origem. 
- Baixe a versão mais recente do [ora2pg](https://ora2pg.darold.net/). 
- A versão mais recente do [módulo DBD](https://www.cpan.org/modules/by-module/DBD/). 


## <a name="overview"></a>Visão geral

O PostgreSQL é um dos bancos de dados de código aberto mais avançados do mundo. Este artigo descreve como usar o utilitário ora2pg gratuito para migrar um banco de dados Oracle para PostgreSQL. Você pode usar o ora2pg, uma ferramenta gratuita, para migrar um banco de dados Oracle ou MySQL para um esquema compatível com PostgreSQL. O utilitário conecta seu banco de dados Oracle, examina-o automaticamente e extrai sua estrutura ou seus dados. Depois, ora2pg gera scripts SQL que você pode carregar em seu banco de dados PostgreSQL. ora2pg pode ser usado para tarefas de engenharia reversa de um banco de dados Oracle, execução de uma grande migração de banco de dados empresarial ou simplesmente replicação de alguns dados do Oracle em um banco de dado PostgreSQL. É fácil de usar e não requer nenhum conhecimento de banco de dados Oracle além da capacidade de fornecer os parâmetros necessários para se conectar ao banco de dados Oracle.

> [!NOTE]
> Para obter mais informações sobre como usar a versão mais recente do ora2pg, consulte a [documentação do ora2pg](https://ora2pg.darold.net/documentation.html).

### <a name="typical-ora2pg-migration-architecture"></a>Arquitetura de migração típica do ora2pg

![Captura de tela da arquitetura de migração do ora2pg.](media/howto-migrate-from-oracle/ora2pg-migration-architecture.png)

Depois de provisionar a VM e o banco de dados do Azure para PostgreSQL, duas configurações são necessárias para habilitar a conectividade entre elas: **permitir os serviços do Azure** e **impor a conexão SSL**, representada da seguinte maneira:

- Folha **segurança de conexão** -> **permitir acesso aos serviços do Azure** -> em

- Folha **segurança de conexão** -> configurações de **SSL**  ->  **impor conexão SSL** – > desabilitada

### <a name="recommendations"></a>Recomendações

- Para melhorar o desempenho das operações de avaliação ou de exportação no servidor Oracle, colete estatísticas da seguinte maneira:

   ```
   BEGIN
   
     DBMS_STATS.GATHER_SCHEMA_STATS
     DBMS_STATS.GATHER_DATABASE_STATS
     DBMS_STATS.GATHER_DICTIONARY_STATS
     END;
   ```

- Exporte dados usando o comando de cópia em vez de INSERT.

- Evite exportar tabelas com suas FKs, restrições e índices – fazer isso tornará mais lento a importação dos dados para o PostgreSQL.

- Crie exibições materializadas usando a **cláusula sem dados** e atualize-as posteriormente.

- Se possível, implemente índices exclusivos em exibições materializadas, isso fará com que a atualização seja mais rápida com a sintaxe `REFRESH MATERIALIZED VIEW CONCURRENTLY` .



## <a name="pre-migration"></a>Pré-migração 

Depois de verificar se o seu ambiente de origem tem suporte e garantir que você tenha resolvido todos os pré-requisitos, você está pronto para iniciar o estágio pré-migração. Essa parte do processo envolve a execução de um inventário dos bancos de dados que você precisa migrar, a avaliação desses bancos de dados quanto a possíveis problemas de migração ou bloqueadores e, em seguida, a resolução dos itens que você possa ter descoberto. Para migrações de heterogêneos, como Oracle para o banco de dados do Azure para PostgreSQL, esse estágio também envolve a conversão dos esquemas nos bancos de dados de origem para serem compatíveis com o ambiente de destino.

### <a name="discover"></a>Descobrir

O objetivo da fase de descoberta é identificar as fontes de dados existentes e os detalhes sobre os recursos que estão sendo usados para obter uma compreensão melhor e planejar a migração. Esse processo envolve a verificação da rede para identificar todas as instâncias do Oracle da sua organização junto com a versão e os recursos em uso.

Os scripts de pré-avaliação do Microsoft Oracle são executados no banco de dados Oracle. Os scripts de pré-avaliação são um conjunto de consultas que atingem os metadados do Oracle e fornece o seguinte:

- Inventário de banco de dados, incluindo contagens de objetos por esquema, tipo e status.

- Uma estimativa aproximada de dados brutos em cada esquema (com base em estatísticas).

- Dimensionamento de tabelas em cada esquema.

- O número de linhas de código por pacote, função, procedimento, etc.

Baixe os scripts relacionados no [site do ora2pg](http://ora2pg.darold.net/).

### <a name="assess"></a>Avaliar

Depois de concluir o inventário dos bancos de dados Oracle para ter uma ideia do tamanho do banco de dados e quais são os desafios, a próxima etapa é executar a avaliação.

Estimar o custo de um processo de migração do Oracle para o PostgreSQL não é fácil. Para obter uma boa avaliação desse custo de migração, o ora2pg inspecionará todos os objetos de banco de dados, todas as funções e procedimentos armazenados para detectar se ainda há alguns objetos e código PL/SQL que não podem ser convertidos automaticamente pelo ora2pg.

o ora2pg tem um modo de análise de conteúdo que inspeciona o banco de dados Oracle para gerar um relatório de texto sobre o que o banco de dados Oracle contém e o que não pode ser exportado.

Para ativar a **análise e** o modo de relatório, use o tipo exportado, `SHOW_REPORT` conforme mostrado no seguinte comando:

```
ora2pg -t SHOW_REPORT
```

Depois que o banco de dados é analisado, ora2pg, com sua capacidade de converter código SQL e PL/SQL da sintaxe Oracle para PostgreSQL, pode ir além, estimando as dificuldades de código e o tempo necessário para executar uma migração de banco de dados completa.

Para estimar o custo de migração em dias-homem, o ora2pg permite que você use uma diretiva de configuração chamada ESTIMATE_COST, que também pode ser habilitada na linha de comando:

```
ora2pg -t SHOW_REPORT --estimate_cost
```

A unidade de migração padrão representa cerca de cinco minutos para um especialista em PostgreSQL. Se essa for a sua primeira migração, você poderá obtê-la mais alta com a diretiva de configuração COST_UNIT_VALUE ou a opção de linha de comando--cost_unit_value.

A última linha do relatório mostra o código de migração estimado total em homens-dias após o número de unidades de migração estimadas para cada objeto.

Esta unidade de migração representa cerca de cinco minutos para um especialista em PostgreSQL. Se essa for sua primeira migração, você poderá aumentar o padrão com a diretiva de configuração COST_UNIT_VALUE ou a opção de linha de comando--cost_unit_value. Encontre abaixo algumas variações da avaliação a) avaliação de tabelas; b) avaliação da coluna c) avaliação de esquema usando a avaliação de esquema padrão cost_unit (5 min) d) usando 10 min como unidade de custo.

```
ora2pg -t SHOW_TABLE -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\tables.txt ora2pg -t SHOW_COLUMN -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\columns.txt
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf --dump_as_html --estimate_cost > c:\ts303\hr
_migration\reports\report.html
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf –-cost_unit_value 10 --dump_as_html --estimate_cost > c:\ts303\hr_migration\reports\report2.html
```

A saída da avaliação de esquema é ilustrada abaixo:

**Nível de migração: B-5**

Níveis de migração:

A-migração que pode ser executada automaticamente

B-migração com reescrita de código e um custo de dias humanos de até 5 dias

C-migração com a reescrita de código e um custo de dias humanos acima de 5 dias

Níveis técnicos:

1 = trivial: nenhuma função armazenada e nenhum gatilho

2 = fácil: nenhuma função armazenada, mas com gatilhos, sem regravação manual

3 = simples: funções e/ou gatilhos armazenados, sem regravação manual

4 = manual: nenhuma função armazenada, mas com gatilhos ou exibições com regravação de código

5 = difícil: funções armazenadas e/ou gatilhos com reescrita de código

A avaliação consiste em uma letra (A ou B) para especificar se a migração precisa de regravação manual ou não, e um número de 1 a 5 para indicar o nível de dificuldade técnica. Você tem uma opção adicional – human_days_limit para especificar o número de dias humanos em que o nível de migração deve ser definido como C para indicar que ele precisa de uma grande quantidade de trabalho e um gerenciamento de projeto completo com suporte à migração. O padrão é 10 dias humanos. Você pode usar a diretiva de configuração HUMAN_DAYS_LIMIT para alterar esse valor padrão permanentemente.

Esse recurso foi desenvolvido para ajudar a decidir qual banco de dados pode ser migrado primeiro e qual é a equipe que precisa ser mobilizada.

### <a name="convert"></a>Converter

Com as migrações de tempo de inatividade mínimo, a origem que você está migrando continua a mudar, desfazendo o destino em termos de dados e esquema, após a migração única ocorrer. Durante a fase de **sincronização de dados** , você precisa garantir que todas as alterações na origem sejam capturadas e aplicadas ao destino quase em tempo real. Depois de verificar se todas as alterações na origem foram aplicadas ao destino, você pode fazer a transferência da origem para o ambiente de destino.

Nesta etapa da migração, ocorre a conversão ou tradução do código Oracle + DDLS para PostgreSQL. A ferramenta ora2pg exporta os objetos Oracle em um formato PostgreSQL automaticamente. Para esses objetos gerados, alguns não serão compilados no banco de dados PostgreSQL sem alterações manuais.  
O processo de entender quais elementos precisam de intervenção manual consiste na compilação dos arquivos gerados pelo ora2pg no banco de dados PostgreSQL, na verificação do log e na realização das alterações necessárias até que toda a estrutura do esquema seja compatível com a sintaxe PostgreSQL.


#### <a name="create-migration-template"></a>Criar modelo de migração 

Primeiro, é recomendável criar o modelo de migração que é fornecido prontamente com o ora2pg. As duas opções--project_base e--init_project quando usadas indicam para ora2pg que ele precisa criar um modelo de projeto com uma árvore de trabalho, um arquivo de configuração e um script para exportar todos os objetos do banco de dados Oracle. Para obter mais informações, consulte a [documentação do ora2pg](https://ora2pg.darold.net/documentation.html).

   Use o seguinte comando: 

   ```
   ora2pg --project_base /app/migration/ --init_project test_project
   
   ora2pg --project_base /app/migration/ --init_project test_project
   ```

Saída de exemplo: 
   
   ```
   Creating project test_project. /app/migration/test_project/ schema/ dblinks/ directories/ functions/ grants/ mviews/ packages/ partitions/ procedures/ sequences/ synonyms/    tables/ tablespaces/ triggers/ types/ views/ sources/ functions/ mviews/ packages/ partitions/ procedures/ triggers/ types/ views/ data/ config/ reports/
   
   Generating generic configuration file
   
   Creating script export_schema.sh to automate all exports.
   
   Creating script import_all.sh to automate all imports.
   ```

O diretório sources/contém o código Oracle, o esquema/diretório contém o código portado para PostgreSQL. O diretório de relatórios/contém os relatórios HTML com a avaliação de custo de migração.


Depois que a estrutura do projeto é criada, um arquivo de configuração genérico é criado. Defina a conexão de banco de dados Oracle, bem como os parâmetros de configuração relevantes na configuração.  Consulte a documentação do ora2pg para entender o que pode ser configurado no arquivo de configuração e como.


#### <a name="export-oracle-objects"></a>Exportar objetos Oracle

Em seguida, exporte os objetos Oracle como objetos PostgreSQL executando o arquivo export_schema. sh.

   ```
   cd /app/migration/mig_project
   ./export_schema.sh
   
   Run the following command manually:
   
   SET namespace="/app/migration/mig_project"
   
   ora2pg -t DBLINK -p -o dblink.sql -b %namespace%/schema/dblinks -c
   %namespace%/config/ora2pg.conf
   ora2pg -t DIRECTORY -p -o directory.sql -b %namespace%/schema/directories -c
   %namespace%/config/ora2pg.conf
   ora2pg -p -t FUNCTION -o functions2.sql -b %namespace%/schema/functions -c
   %namespace%/config/ora2pg.conf ora2pg -t GRANT -o grants.sql -b %namespace%/schema/grants -c %namespace%/config/ora2pg.conf ora2pg -t MVIEW -o mview.sql -b %namespace%/schema/   mviews -c %namespace%/config/ora2pg.conf
   ora2pg -p -t PACKAGE -o packages.sql
   %namespace%/config/ora2pg.conf -b %namespace%/schema/packages -c
   ora2pg -p -t PARTITION -o partitions.sql %namespace%/config/ora2pg.conf -b %namespace%/schema/partitions -c
   ora2pg -p -t PROCEDURE -o procs.sql
   %namespace%/config/ora2pg.conf -b %namespace%/schema/procedures -c
   ora2pg -t SEQUENCE -o sequences.sql
   %namespace%/config/ora2pg.conf -b %namespace%/schema/sequences -c
   ora2pg -p -t SYNONYM -o synonym.sql -b %namespace%/schema/synonyms -c
   %namespace%/config/ora2pg.conf
   ora2pg -t TABLE -o table.sql -b %namespace%/schema/tables -c %namespace%/config/ora2pg.conf ora2pg -t TABLESPACE -o tablespaces.sql -b %namespace%/schema/tablespaces -c
   %namespace%/config/ora2pg.conf
   ora2pg -p -t TRIGGER -o triggers.sql -b %namespace%/schema/triggers -c
   %namespace%/config/ora2pg.conf ora2pg -p -t TYPE -o types.sql -b %namespace%/schema/types -c %namespace%/config/ora2pg.conf ora2pg -p -t VIEW -o views.sql -b %namespace%/   schema/views -c %namespace%/config/ora2pg.conf
   ```

   Para extrair os dados, use o seguinte comando:

   ```
   ora2pg -t COPY -o data.sql -b %namespace/data -c %namespace/config/ora2pg.conf
   ```

#### <a name="compile-files"></a>Compilar arquivos

Por fim, compile todos os arquivos no banco de dados do Azure para o servidor PostgreSQL. Agora é possível optar por carregar os arquivos DDL gerados manualmente ou usar o segundo script import_all. sh para importar esses arquivos interativamente.

   ```
   psql -f %namespace%\schema\sequences\sequence.sql -h server1-
   
   server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l
   
   %namespace%\ schema\sequences\create_sequences.log
   
   psql -f %namespace%\schema\tables\table.sql -h server1-server.postgres.database.azure.com p 5432 -U username@server1-server -d database -l    %namespace%\schema\tables\create_table.log
   ```

   Comando de importação de dados:

   ```
   psql -f %namespace%\data\table1.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table1.log
   
   psql -f %namespace%\data\table2.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table2.log
   ```

Durante a compilação de arquivos, verifique os logs e corrija as sintaxes necessárias que o ora2pg não pôde converter prontamente.

Consulte as [soluções alternativas de migração White Paper Oracle para banco de dados do Azure para PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf) para dar suporte ao trabalho em torno de problemas.

## <a name="migrate"></a>Migrações 

Depois de implementar os pré-requisitos necessários e concluir as tarefas associadas à fase de **pré-migração**, você já poderá executar a migração de dados e de esquema.

### <a name="migrate-schema-and-data"></a>Migrar esquema e dados

Depois que as correções estiverem em vigor, uma compilação estável do banco de dados estará pronta para implantação.

Neste ponto, tudo o que é necessário é executar os comandos de importação do *psql* , apontando para os arquivos que contêm o código modificado para compilar os objetos de banco de dados no banco de dados PostgreSQL e importar os mesmos.

Nesta etapa, algum nível de paralelismo na importação dos dados pode ser implementado.

### <a name="data-sync-and-cutover"></a>Sincronização de dados e transferência

Com migrações online (mínimas de tempo de inatividade), a origem que você está migrando continua a ser alterada, desfazendo o destino em termos de dados e esquema, após a migração única ocorrer. Durante a fase de **sincronização de dados** , você precisa garantir que todas as alterações na origem sejam capturadas e aplicadas ao destino quase em tempo real. Depois de verificar se todas as alterações na origem foram aplicadas ao destino, você pode fazer a transferência da origem para o ambiente de destino.

A partir de março de 2019, se você quiser executar uma migração online, considere usar o Attunity replicate para Microsoft Migrations ou Striim.

Para a migração de *Delta/incremental* usando ora2pg, a técnica consiste em aplicar para cada tabela uma consulta que aplica um filtro (recortar) por data ou hora, etc., e depois disso, finalizar a migração aplicando uma segunda consulta que irá migrar o restante dos dados (sobrando).

Na tabela de dados de origem, migre todos os dados históricos primeiro. Um exemplo disso é:

```
select * from table1 where filter_data < 01/01/2019
```

Você pode consultar as alterações feitas desde a migração inicial executando um comando semelhante ao seguinte:

```
select * from table1 where filter_data >= 01/01/2019
```

Nesse caso, é recomendável que a validação seja aprimorada verificando a paridade de dados em ambos os lados, origem e destino.

## <a name="post-migration"></a>Pós-migração 

Depois de concluir com êxito o estágio de **migração**, você precisará passar por uma série de tarefas de pós-migração para garantir que tudo esteja funcionando da maneira mais estável e eficiente possível.

### <a name="remediate-applications"></a>Corrigir aplicativos

Depois que os dados são migrados para o ambiente de destino, todos os aplicativos que antes consumiam a origem, precisam começar a consumir o destino. Em alguns casos isso exigirá alterações nos aplicativos.

### <a name="perform-tests"></a>Executar testes

Depois que os dados são migrados para o destino, execute testes em relação aos bancos de dado para verificar se os aplicativos executam bem em relação ao destino após a migração.

Para garantir que a origem e o destino sejam migrados corretamente, execute os scripts de validação manual de dados em relação aos bancos de dado de destino Oracle Source e PostgreSQL.

Idealmente, se os bancos de dados de origem e de destino tiverem um caminho de rede, ora2pg deverá ser usado para validação de dados. O uso da ação de teste permite que você verifique se todos os objetos do banco de dados Oracle foram criados no PostgreSQL. Execute o comando conforme mostrado:

```
ora2pg -t TEST -c config/ora2pg.conf > migration_diff.txt
```

### <a name="optimize"></a>Otimizar

A fase de pós-migração é crucial para reconciliar problemas de precisão de dados e verificar a integridade dos dados, além de resolver problemas de desempenho com a carga de trabalho.

## <a name="migration-assets"></a>Ativos de migração 

Para obter assistência adicional com a conclusão desse cenário de migração, confira os recursos a seguir, que foram desenvolvidos para dar suporte a um projeto de migração do mundo real.

| **Link do título** | **Descrição**    |
| -------------- | ------------------ |
| [Manual de migração do Oracle para o PostgreSQL do Azure](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf) | Essa finalidade do documento é fornecer aos arquitetos, consultores, DBAs e funções relacionadas um guia para migrar rapidamente as cargas de trabalho do Oracle para o banco de dados do Azure para PostgreSQL usando a ferramenta ora2pg. |
| [Soluções alternativas de migração do Oracle para o Azure PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf) | Essa finalidade do documento é fornecer aos arquitetos, consultores, DBAs e funções relacionadas um guia para correção rápida/trabalho em relação a problemas durante a migração de cargas de trabalhos do Oracle para o banco de dados do Azure para PostgreSQL. |
| [Etapas para instalar o ora2pg no Windows ou Linux](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)                       | Este documento destina-se a ser usado como um guia de instalação rápida para permitir a migração de dados de esquema & do Oracle para o banco de dados do Azure para PostgreSQL usando a ferramenta ora2pg no Windows ou no Linux. Os detalhes completos sobre a ferramenta podem ser encontrados em http://ora2pg.darold.net/documentation.html . |

A equipe de engenharia de SQL de dados desenvolveu esses recursos. O principal compromisso desta equipe é desbloquear e acelerar a modernização complexa para projetos de migração da plataforma de dados para a plataforma de dados do Azure da Microsoft.


### <a name="contact-support"></a>Contate o suporte

Se você precisar de assistência com suas migrações além das ferramentas de ora2pg, contate o alias do [ @Ask banco de dados do Azure para PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com) para obter informações sobre outras opções de migração.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma matriz dos serviços/ferramentas da Microsoft e de terceiros disponíveis para ajudá-lo com vários cenários de migração de banco de dados e de aplicativos (e tarefas especiais), consulte o artigo [serviço e ferramentas para migração de dados](https://docs.microsoft.com/azure/dms/dms-tools-matrix).

Para obter mais informações, consulte: 
- [Documentação do Banco de Dados do Azure para PostgreSQL](https://docs.microsoft.com/azure/postgresql/)
- [documentação do ora2pg](https://ora2pg.darold.net/documentation.html)
- [Site do PostgreSQL](https://www.postgresql.org/)
- [Suporte a transações autônomas no PostgreSQL](http://blog.dalibo.com/2016/08/19/Autonoumous_transactions_support_in_PostgreSQL.html) 

Para conteúdo de vídeo: 
- [Visão geral do percurso de migração e das ferramentas/serviços recomendados para executar a avaliação e a migração](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/).
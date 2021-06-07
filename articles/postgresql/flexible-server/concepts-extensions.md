---
title: Extensões-banco de dados do Azure para PostgreSQL-servidor flexível
description: Saiba mais sobre as extensões postgres disponíveis no banco de dados do Azure para PostgreSQL – servidor flexível
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: e8f71eb120b86f35672c9123b52f7f19c9fee662
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608452"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---flexible-server"></a>Extensões PostgreSQL no banco de dados do Azure para PostgreSQL – servidor flexível

> [!IMPORTANT]
> O Servidor Flexível do Banco de Dados do Azure para PostgreSQL está em versão prévia

O PostgreSQL fornece a capacidade de estender a funcionalidade de seu banco de dados usando as extensões. As extensões agrupam vários objetos SQL relacionados em um único pacote que pode ser carregado ou removido do banco de dados com um comando. Depois de carregadas no banco de dados, as extensões funcionam como recursos internos.

## <a name="how-to-use-postgresql-extensions"></a>Como usar as extensões PostgreSQL
As extensões PostgreSQL devem ser instaladas no banco de dados para que você possa usá-las. Para instalar uma extensão específica, execute o comando [criar extensão](https://www.postgresql.org/docs/current/sql-createextension.html) . Esse comando carrega os objetos empacotados em seu banco de dados.

O banco de dados do Azure para PostgreSQL dá suporte a um subconjunto de extensões de chave, conforme listado abaixo. Essas informações também estão disponíveis por meio da execução de `SHOW azure.extensions;`. As extensões não listadas neste documento não têm suporte no banco de dados do Azure para PostgreSQL-servidor flexível. Você não pode criar ou carregar sua própria extensão no banco de dados do Azure para PostgreSQL.


## <a name="postgres-12-extensions"></a>Extensões do postgres 12

As seguintes extensões estão disponíveis no banco de dados do Azure para PostgreSQL – servidores flexíveis com postgres versão 12. 

> [!div class="mx-tableFixed"]
> | **Extensão**| **Versão da extensão** | **Descrição** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 3.0.0           | Usado para analisar um endereço em elementos constituintes. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 3.0.0           | Exemplo de conjunto de DataSet de endereço do padronizador dos EUA|
> |[amcheck](https://www.postgresql.org/docs/12/amcheck.html)                    | 1.2             | funções para verificar a integridade da relação|
> |[cair](https://www.postgresql.org/docs/12/bloom.html)                    | 1.0             | método de acesso de flor – índice baseado em arquivo de assinatura|
> |[btree_gin](https://www.postgresql.org/docs/12/btree-gin.html)                    | 1,3             | suporte para indexação de tipos de texto comuns em iniciar|
> |[btree_gist](https://www.postgresql.org/docs/12/btree-gist.html)                   | 1.5             | suporte para indexação de tipos de texto comuns no|
> |[citext](https://www.postgresql.org/docs/12/citext.html)                       | 1.6             | tipo de dados para cadeias de caracteres não diferenciando maiúsculas de minúsculas|
> |[simples](https://www.postgresql.org/docs/12/cube.html)                         | 1.4             | tipo de dados para cubos multidimensionais|
> |[dblink](https://www.postgresql.org/docs/12/dblink.html)                       | 1.2             | conectar-se a outros bancos de dados PostgreSQL de dentro de um Database|
> |[dict_int](https://www.postgresql.org/docs/12/dict-int.html)                     | 1.0             | modelo de dicionário de pesquisa de texto para inteiros|
> |[dict_xsyn](https://www.postgresql.org/docs/12/dict-xsyn.html)                     | 1.0             | modelo de dicionário de pesquisa de texto para processamento estendido de sinônimo|
> |[earthdistance](https://www.postgresql.org/docs/12/earthdistance.html)                | 1,1             | calcular grandes distâncias de círculo na superfície da terra|
> |[fuzzystrmatch](https://www.postgresql.org/docs/12/fuzzystrmatch.html)                | 1,1             | determinar semelhanças e distância entre cadeias de caracteres|
> |[hstore](https://www.postgresql.org/docs/12/hstore.html)                       | 1.6             | tipo de dados para armazenar conjuntos de pares (chave, valor)|
> |[intag](https://www.postgresql.org/docs/12/intagg.html)                     | 1,1             | agregador e enumerador inteiros. Substituí|
> |[intarray](https://www.postgresql.org/docs/12/intarray.html)                     | 1.2             | suporte a funções, operadores e índice para matrizes 1D de inteiros|
> |[isn](https://www.postgresql.org/docs/12/isn.html)                          | 1.2             | tipos de dados para padrões de numeração de produtos internacionais|
> |[ltree](https://www.postgresql.org/docs/12/ltree.html)                        | 1,1             | tipo de dados para estruturas hierárquicas como de árvore|
> |[pageinspect](https://www.postgresql.org/docs/12/pageinspect.html)                        | 1.7             | Inspecione o conteúdo das páginas do banco de dados em um nível baixo|
> |[pg_buffercache](https://www.postgresql.org/docs/12/pgbuffercache.html)               | 1,3             | examinar o cache de buffer compartilhado|
> |[pg_freespacemap](https://www.postgresql.org/docs/12/pgfreespacemap.html)               | 1.2             | examinar o mapa de espaço livre (FSM)|
> |[pg_prewarm](https://www.postgresql.org/docs/12/pgprewarm.html)                   | 1.2             | dados de relações prequentes|
> |[pg_stat_statements](https://www.postgresql.org/docs/12/pgstatstatements.html)           | 1.7             | acompanhar estatísticas de execução de todas as instruções SQL executadas|
> |[pg_trgm](https://www.postgresql.org/docs/12/pgtrgm.html)                      | 1.4             | medição de similaridade de texto e pesquisa de índice com base em trigramas|
> |[pg_visibility](https://www.postgresql.org/docs/12/pgvisibility.html)                      | 1.2             | examinar o mapa de visibilidade (VM) e as informações de visibilidade no nível da página|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.4             | fornece funcionalidade de auditoria|
> |[pgcrypto](https://www.postgresql.org/docs/12/pgcrypto.html)                     | 1,3             | funções criptográficas|
> |[pgrowlocks](https://www.postgresql.org/docs/12/pgrowlocks.html)                   | 1.2             | Mostrar informações de bloqueio em nível de linha|
> |[pgstattuple](https://www.postgresql.org/docs/12/pgstattuple.html)                  | 1.5             | Mostrar estatísticas de nível de tupla|
> |[plpgsql](https://www.postgresql.org/docs/12/plpgsql.html)                      | 1.0             | Linguagem de procedimento PL/pgSQL|
> |[PostGIS](https://www.postgis.net/)                      | 3.0.0           | PostGIS Geometry, geography |
> |[postgis_raster](https://www.postgis.net/)               | 3.0.0           | Funções e tipos de rasterização de PostGIS| 
> |[postgis_sfcgal](https://www.postgis.net/)               | 3.0.0           | Funções PostGIS SFCGAL|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 3.0.0           | Geocodificador PostGIS Tiger e reverso geocodificador|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 3.0.0           | Tipos e funções espaciais de topologia PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/12/postgres-fdw.html)                 | 1.0             | wrapper de dados externos para servidores PostgreSQL remotos|
> |[sslinfo](https://www.postgresql.org/docs/12/sslinfo.html)                    | 1.2             | informações sobre certificados SSL|
> |[tsm_system_rows](https://www.postgresql.org/docs/12/tsm-system-rows.html)                    | 1.0             |  Método TABLESAMPLE que aceita o número de linhas como um limite|
> |[tsm_system_time](https://www.postgresql.org/docs/12/tsm-system-time.html)                    | 1.0             |  Método TABLESAMPLE que aceita o tempo em milissegundos como um limite|
> |[unaccent](https://www.postgresql.org/docs/12/unaccent.html)                     | 1,1             | dicionário de pesquisa de texto que remove acentos|
> |[uuid-ossp](https://www.postgresql.org/docs/12/uuid-ossp.html)                    | 1,1             | gerar identificadores universais exclusivos (UUIDs)|

## <a name="postgres-11-extensions"></a>Extensões do postgres 11

As seguintes extensões estão disponíveis no banco de dados do Azure para PostgreSQL – servidores flexíveis com postgres versão 11. 

> [!div class="mx-tableFixed"]
> | **Extensão**| **Versão da extensão** | **Descrição** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Usado para analisar um endereço em elementos constituintes. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Exemplo de conjunto de DataSet de endereço do padronizador dos EUA|
> |[amcheck](https://www.postgresql.org/docs/11/amcheck.html)                    | 1,1             | funções para verificar a integridade da relação|
> |[cair](https://www.postgresql.org/docs/11/bloom.html)                    | 1.0             | método de acesso de flor – índice baseado em arquivo de assinatura|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1,3             | suporte para indexação de tipos de texto comuns em iniciar|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | suporte para indexação de tipos de texto comuns no|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | tipo de dados para cadeias de caracteres não diferenciando maiúsculas de minúsculas|
> |[simples](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | tipo de dados para cubos multidimensionais|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | conectar-se a outros bancos de dados PostgreSQL de dentro de um Database|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | modelo de dicionário de pesquisa de texto para inteiros|
> |[dict_xsyn](https://www.postgresql.org/docs/11/dict-xsyn.html)                     | 1.0             | modelo de dicionário de pesquisa de texto para processamento estendido de sinônimo|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1,1             | calcular grandes distâncias de círculo na superfície da terra|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1,1             | determinar semelhanças e distância entre cadeias de caracteres|
> |[hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | tipo de dados para armazenar conjuntos de pares (chave, valor)|
> |[intag](https://www.postgresql.org/docs/11/intagg.html)                     | 1,1             | agregador e enumerador inteiros. Substituí|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | suporte a funções, operadores e índice para matrizes 1D de inteiros|
> |[isn](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | tipos de dados para padrões de numeração de produtos internacionais|
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1,1             | tipo de dados para estruturas hierárquicas como de árvore|
> |[pageinspect](https://www.postgresql.org/docs/11/pageinspect.html)                        | 1.7             | Inspecione o conteúdo das páginas do banco de dados em um nível baixo|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1,3             | examinar o cache de buffer compartilhado|
> |[pg_freespacemap](https://www.postgresql.org/docs/11/pgfreespacemap.html)               | 1.2             | examinar o mapa de espaço livre (FSM)|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | dados de relações prequentes|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | acompanhar estatísticas de execução de todas as instruções SQL executadas|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | medição de similaridade de texto e pesquisa de índice com base em trigramas|
> |[pg_visibility](https://www.postgresql.org/docs/11/pgvisibility.html)                      | 1.2             | examinar o mapa de visibilidade (VM) e as informações de visibilidade no nível da página|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | fornece funcionalidade de auditoria|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1,3             | funções criptográficas|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | Mostrar informações de bloqueio em nível de linha|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | Mostrar estatísticas de nível de tupla|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | Linguagem de procedimento PL/pgSQL|
> |[PostGIS](https://www.postgis.net/)                      | 2.5.1           | Funções e tipos espaciais de PostGIS, geography e rasterização|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | Funções PostGIS SFCGAL|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | Geocodificador PostGIS Tiger e reverso geocodificador|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | Tipos e funções espaciais de topologia PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | wrapper de dados externos para servidores PostgreSQL remotos|
> |[sslinfo](https://www.postgresql.org/docs/11/sslinfo.html)                    | 1.2             | informações sobre certificados SSL|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | funções que manipulam tabelas inteiras, incluindo a tabela de referência cruzada|
> |[tsm_system_rows](https://www.postgresql.org/docs/11/tsm-system-rows.html)                    | 1.0             |  Método TABLESAMPLE que aceita o número de linhas como um limite|
> |[tsm_system_time](https://www.postgresql.org/docs/11/tsm-system-time.html)                    | 1.0             |  Método TABLESAMPLE que aceita o tempo em milissegundos como um limite|
> |[unaccent](https://www.postgresql.org/docs/11/unaccent.html)                     | 1,1             | dicionário de pesquisa de texto que remove acentos|
> |[uuid-ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1,1             | gerar identificadores universais exclusivos (UUIDs)|


## <a name="dblink-and-postgres_fdw"></a>dblink e postgres_fdw
[dblink](https://www.postgresql.org/docs/current/contrib-dblink-function.html) e [postgres_fdw](https://www.postgresql.org/docs/current/postgres-fdw.html) permitem que você se conecte de um servidor PostgreSQL para outro ou a outro banco de dados no mesmo servidor. O servidor flexível dá suporte a conexões de entrada e de saída para qualquer servidor PostgreSQL. O servidor de envio precisa permitir conexões de saída para o servidor de recebimento. Da mesma forma, o servidor de recebimento precisa permitir conexões do servidor de envio. 

É recomendável implantar seus servidores com [integração VNet](concepts-networking.md) se você planeja usar essas duas extensões. Por padrão, a integração VNet permite conexões entre servidores na VNET. Você também pode optar por usar [grupos de segurança de rede de VNet](../../virtual-network/manage-network-security-group.md) para personalizar o acesso.

## <a name="pg_prewarm"></a>pg_prewarm

A extensão pg_prewarm carrega dados relacionais no cache. A preaquecimento de seus caches significa que suas consultas têm tempos de resposta melhores em sua primeira execução após uma reinicialização. A funcionalidade de preaquecimento automático não está disponível no momento no banco de dados do Azure para PostgreSQL – servidor flexível.

## <a name="pg_stat_statements"></a>pg_stat_statements
A [extensão de pg_stat_statements](https://www.postgresql.org/docs/current/pgstatstatements.html) é pré-carregado em cada servidor flexível do banco de dados do Azure para PostgreSQL para fornecer a você um meio de controlar estatísticas de execução de instruções SQL.
A configuração `pg_stat_statements.track`, que controla quais instruções são contadas por extensão, tem `top` como padrão, que significa que todas as instruções emitidas diretamente por clientes são rastreadas. Dois outros níveis de rastreamento são `none` e `all`. Essa configuração é configurável como um parâmetro de servidor.

Há um equilíbrio entre as informações de execução de consulta fornecida por pg_stat_statements e o impacto no desempenho do servidor, que registra cada instrução SQL. Se você não está usando ativamente a extensão pg_stat_statements, recomendamos que você defina `pg_stat_statements.track` como `none`. Observe que alguns serviços de monitoramento de terceiros podem depender de pg_stat_statements para fornecer informações de desempenho de consulta, para confirmar se este é o caso para você ou não.


## <a name="next-steps"></a>Próximas etapas

Se você não vir uma extensão que gostaria de usar, fale conosco. Vote em solicitações existentes ou crie novas solicitações de comentários em nosso [Fórum de comentários](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).
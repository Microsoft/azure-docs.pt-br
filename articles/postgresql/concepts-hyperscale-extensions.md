---
title: Extensões – hiperescala (Citus)-banco de dados do Azure para PostgreSQL
description: Descreve a capacidade de estender a funcionalidade do seu banco de dados usando extensões no banco de dados do Azure para PostgreSQL-Citus (hiperescala)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: 000f8a1457298901dcfc94bc5e0923e94ba35dc7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96620895"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql--hyperscale-citus"></a>Extensões PostgreSQL no banco de dados do Azure para PostgreSQL – Citus (hiperescala)

O PostgreSQL fornece a capacidade de estender a funcionalidade do banco de dados usando extensões. As extensões permitem o agrupamento de vários objetos SQL relacionados em um único pacote que pode ser carregado ou removido de seu banco de dados com um único comando. Após serem carregadas no banco de dados, as extensões funcionam como recursos internos. Para obter mais informações sobre extensões PostgreSQL, consulte [empacotar objetos relacionados em uma extensão](https://www.postgresql.org/docs/current/static/extend-extensions.html).

## <a name="use-postgresql-extensions"></a>Usar extensões do PostgreSQL

As extensões PostgreSQL devem ser instaladas no banco de dados para que você possa usá-las. Para instalar uma extensão específica, execute o comando [criar extensão](https://www.postgresql.org/docs/current/static/sql-createextension.html) da ferramenta psql para carregar os objetos empacotados em seu banco de dados.

O banco de dados do Azure para PostgreSQL-Citus (hiperescala) atualmente dá suporte a um subconjunto de extensões de chave, conforme listado aqui. Não há suporte para extensões que não aquelas listadas. Você não pode criar sua própria extensão com o banco de dados do Azure para PostgreSQL.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Extensões com suporte do Banco de Dados do Azure para PostgreSQL

As tabelas a seguir listam as extensões PostgreSQL padrão que têm suporte atualmente do Banco de Dados do Azure para PostgreSQL. Essas informações também estão disponíveis por meio da execução de `SELECT * FROM pg_available_extensions;`.

### <a name="data-types-extensions"></a>Extensões de tipos de dados

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [citext](https://www.postgresql.org/docs/current/static/citext.html) | Fornece um tipo de cadeia de caracteres que não diferencia maiúsculas de minúsculas. |
> | [simples](https://www.postgresql.org/docs/current/static/cube.html) | Fornece um tipo de dados para cubos multidimensionais. |
> | [hll](https://github.com/citusdata/postgresql-hll) | Fornece uma estrutura de dados HyperLogLog. |
> | [hstore](https://www.postgresql.org/docs/current/static/hstore.html) | Fornece um tipo de dados para armazenar conjuntos de pares chave-valor. |
> | [isn](https://www.postgresql.org/docs/current/static/isn.html) | Fornece tipos de dados para padrões de numeração de produto internacionais. |
> | [lo](https://www.postgresql.org/docs/current/lo.html) | Manutenção de objeto grande. |
> | [ltree](https://www.postgresql.org/docs/current/static/ltree.html) | Fornece um tipo de dados para estruturas semelhantes a árvores hierárquicas. |
> | [seg](https://www.postgresql.org/docs/current/seg.html) | Tipo de dados para representar segmentos de linha ou intervalos de ponto flutuante. |
> | [tdigest](https://github.com/tvondra/tdigest) | Tipo de dados para acumulação online de estatísticas baseadas em classificação, como quantis e excortado significa. |
> | [TopN](https://github.com/citusdata/postgresql-topn/) | Tipo para Top-n JSONB. |

### <a name="full-text-search-extensions"></a>Extensões de pesquisa de texto completo

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/current/static/dict-int.html) | Fornece um modelo de dicionário de pesquisa de texto para números inteiros. |
> | [xsyn de dictação \_](https://www.postgresql.org/docs/current/dict-xsyn.html) | Modelo de dicionário de pesquisa de texto para processamento estendido de sinônimo. |
> | [unaccent](https://www.postgresql.org/docs/current/static/unaccent.html) | Um dicionário de pesquisa de texto que remove acentos (sinais diacríticos) dos lexemas. |

### <a name="functions-extensions"></a>Extensões de funções

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Funções para incrementar campos automaticamente. |
> | [earthdistance](https://www.postgresql.org/docs/current/static/earthdistance.html) | Fornece um meio para calcular as distância ortodrômicas na superfície da Terra. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/current/static/fuzzystrmatch.html) | Fornece várias funções para determinar semelhanças e a distância entre cadeias de caracteres. |
> | [inserir \_ nome de usuário](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | Funções para controlar quem alterou uma tabela. |
> | [intag](https://www.postgresql.org/docs/current/intagg.html) | Agregador inteiro e enumerador (obsoleto). |
> | [intarray](https://www.postgresql.org/docs/current/static/intarray.html) | Fornece funções e operadores para manipular matrizes de inteiros sem nulos. |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Funções para controlar a hora da última modificação. |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Gerencia as tabelas de partição por hora ou ID. |
> | [pg\_trgm](https://www.postgresql.org/docs/current/static/pgtrgm.html) | Fornece funções e operadores para determinar a semelhança de texto alfanumérico, com base na correspondência de trigram. |
> | [pgcrypto](https://www.postgresql.org/docs/current/static/pgcrypto.html) | Fornece funções de criptografia. |
> | [refint](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | Funções para implementar a integridade referencial (obsoleto). |
> | análise de sessão \_ | Funções para consultar matrizes hstore. |
> | [tablefunc](https://www.postgresql.org/docs/current/static/tablefunc.html) | Fornece funções que manipulam tabelas inteiras, incluindo a tabela de referência cruzada. |
> | [TCN](https://www.postgresql.org/docs/current/tcn.html) | Notificações de alteração disparadas. |
> | [viagem de viagens](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Funções para implementação de viagem de tempo. |
> | [uuid-ossp](https://www.postgresql.org/docs/current/static/uuid-ossp.html) | Gera UUIDs (identificadores exclusivos universais). |

### <a name="hyperscale-citus-extensions"></a>Extensões de hiperescala (Citus)

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [citus](https://github.com/citusdata/citus) | Banco de dados distribuído Citus. |

### <a name="index-types-extensions"></a>Extensões de tipos de índice

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [cair](https://www.postgresql.org/docs/current/bloom.html) | Incair no método de acesso – índice baseado em arquivo de assinatura. |
> | [btree\_gin](https://www.postgresql.org/docs/current/static/btree-gin.html) | Fornece exemplos de classes de operadores iniciar que implementam comportamento de árvore B para determinados tipos de dados. |
> | [btree\_gist](https://www.postgresql.org/docs/current/static/btree-gist.html) | Fornece classes de operador de índice GiST que implementam a árvore B. |

### <a name="language-extensions"></a>Extensões de linguagem

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/current/static/plpgsql.html) | Linguagem de procedimento carregável PL/pgSQL. |

### <a name="miscellaneous-extensions"></a>Extensões diversas

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [adminpack](https://www.postgresql.org/docs/current/adminpack.html) | Funções administrativas para PostgreSQL. |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | Funções para verificar a integridade da relação. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Um módulo que suporta conexões com outros bancos de dados do PostgreSQL de dentro de uma sessão de banco de dados. Consulte a seção "dblink e postgres_fdw" para obter informações sobre essa extensão. |
> | [fdw de arquivo \_](https://www.postgresql.org/docs/current/file-fdw.html) | Wrapper de dados estrangeiros para acesso de arquivo simples. |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | Inspecione o conteúdo das páginas do banco de dados em um nível baixo. |
> | [pg\_buffercache](https://www.postgresql.org/docs/current/static/pgbuffercache.html) | Fornece um meio para examinar o que está acontecendo no cache do buffer compartilhado em tempo real. |
> | [PG \_ cron](https://github.com/citusdata/pg_cron) | Agendador de trabalhos para PostgreSQL. |
> | [\_freespacemap PG](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Examine o mapa de espaço livre (FSM). |
> | [pg\_prewarm](https://www.postgresql.org/docs/current/static/pgprewarm.html) | Fornece uma maneira para carregar dados de relação no cache de buffer. |
> | [pg\_stat\_statements](https://www.postgresql.org/docs/current/static/pgstatstatements.html) | Fornece um meio para rastrear as estatísticas de execução de todas as instruções SQL executadas por um servidor. Consulte a seção "pg_stat_statements" para obter informações sobre essa extensão. |
> | [visibilidade da PG \_](https://www.postgresql.org/docs/current/pgvisibility.html) | Examine a VM (mapa de visibilidade) e as informações de visibilidade no nível da página. |
> | [pgrowlocks](https://www.postgresql.org/docs/current/static/pgrowlocks.html) | Fornece um meio para mostrar informações de bloqueio de nível de linha. |
> | [pgstattuple](https://www.postgresql.org/docs/current/static/pgstattuple.html) | Fornece um meio para mostrar estatísticas em nível de tupla. |
> | [postgres\_fdw](https://www.postgresql.org/docs/current/static/postgres-fdw.html) | Wrapper de dados externos usado para acessar dados armazenados em servidores PostgreSQL externos. Consulte a seção "dblink e postgres_fdw" para obter informações sobre essa extensão.|
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | Informações sobre certificados TLS/SSL. |
> | [\_linhas do sistema TSM \_](https://www.postgresql.org/docs/current/tsm-system-rows.html) | Método TABLESAMPLE, que aceita o número de linhas como um limite. |
> | [\_hora do sistema do TSM \_](https://www.postgresql.org/docs/current/tsm-system-time.html) | Método TABLESAMPLE, que aceita o tempo em milissegundos como um limite. |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | Consulta XPath e XSLT. |


### <a name="postgis-extensions"></a>Extensões PostGIS

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [PostGIS](https://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | Objetos espaciais e geográficos para PostgreSQL. |
> | address\_standardizer, address\_standardizer\_data\_us | Usado para analisar um endereço em elementos constituintes. Usado para oferecer suporte à etapa de normalização de endereços de geocodificação. |
> | PostGIS \_ sfcgal | PostGIS SFCGAL functions. |
> | \_geocodificador PostGIS Tiger \_ | Geocodificador PostGIS Tiger e reverso geocodificador. |
> | \_topologia PostGIS | Tipos e funções espaciais de topologia PostGIS. |


## <a name="pg_stat_statements"></a>pg_stat_statements
A [ \_ extensão de \_ instruções de instrução PG](https://www.postgresql.org/docs/current/pgstatstatements.html) é pré-carregadas em cada servidor de banco de dados do Azure para PostgreSQL para fornecer a você meios de controlar estatísticas de execução de instruções SQL.

A configuração `pg_stat_statements.track` controla quais instruções são contadas pela extensão. Ele usa como padrão `top` , o que significa que todas as instruções emitidas diretamente pelos clientes são controladas. Dois outros níveis de rastreamento são `none` e `all`. Essa definição é configurável como um parâmetro de servidor por meio de [portal do Azure](./howto-configure-server-parameters-using-portal.md) ou da [CLI do Azure](./howto-configure-server-parameters-using-cli.md).

Há uma compensação entre as informações de execução da consulta pg_stat_statements fornece e o efeito no desempenho do servidor enquanto registra cada instrução SQL. Se você não estiver usando ativamente a extensão de pg_stat_statements, recomendamos que você defina `pg_stat_statements.track` como `none` . Alguns serviços de monitoramento de terceiros podem contar com pg_stat_statements para fornecer informações de desempenho de consulta, portanto, confirme se esse é o caso para você ou não.

## <a name="dblink-and-postgres_fdw"></a>dblink e postgres_fdw

Você pode usar dblink e postgres \_ fdw para se conectar de um servidor PostgreSQL para outro ou a outro banco de dados no mesmo servidor.  O servidor de recebimento precisa permitir conexões do servidor de envio por meio de seu firewall.  Para usar essas extensões para se conectar entre os servidores do banco de dados do Azure para servidor PostgreSQL ou grupos de servidores Citus (hiperescala), defina **permitir que os serviços e recursos do Azure acessem este grupo de servidores (ou servidor)** como ativado.  Você também precisará ativar essa configuração se quiser usar as extensões para fazer um loop de volta para o mesmo servidor.
A configuração **permitir que os serviços e recursos do Azure acessem este grupo de servidores** pode ser encontrada na página portal do Azure para o grupo de servidores de hiperescala (Citus) em **rede**.  Atualmente, não há suporte para conexões de saída do banco de dados do Azure para PostgreSQL de servidor único e hiperescala (Citus), exceto para conexões com outros grupos de servidores do banco de dados do Azure para servidor PostgreSQL e de hiperescala (Citus).
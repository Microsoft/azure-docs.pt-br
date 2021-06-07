---
title: Usar extensões do PostgreSQL
description: Usar extensões do PostgreSQL
titleSuffix: Azure Arc enabled data services
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: e247e372237572586e5a4647d24d9ed6067ea823
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104949780"
---
# <a name="use-postgresql-extensions-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Usar extensões PostgreSQL em seu grupo de servidores de hiperescala PostgreSQL habilitados para o Azure Arc

O PostgreSQL é o melhor quando usado com extensões. Na verdade, um elemento fundamental da nossa própria funcionalidade de hiperescala é a extensão fornecida `citus` pela Microsoft que é instalada por padrão, o que permite ao postgres fragmentar dados de forma transparente em vários nós.


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="supported-extensions"></a>Extensões com suporte
As [`contrib`](https://www.postgresql.org/docs/12/contrib.html) extensões padrão e as seguintes extensões já estão implantadas nos contêineres do seu grupo de servidores de hiperescala PostgreSQL habilitados para o Azure Arc:
- [`citus`](https://github.com/citusdata/citus), v: 9,4. A extensão Citus por [dados de Citus](https://www.citusdata.com/) é carregada por padrão, pois ela traz a capacidade de hiperescala para o mecanismo PostgreSQL. Não há suporte para a remoção da extensão Citus do seu grupo de servidores de hiperescala do PostgreSQL do Azure Arc.
- [`pg_cron`](https://github.com/citusdata/pg_cron), v: 1,2
- [`pgaudit`](https://www.pgaudit.org/), v: 1,4
- Plpgsql, v: 1,0
- [`postgis`](https://postgis.net), v: 3.0.2
- [`plv8`](https://plv8.github.io/), v: 2.3.14

As atualizações desta lista serão lançadas à medida que ela evoluir ao longo do tempo.

> [!IMPORTANT]
> Embora você possa levar ao grupo de servidores uma extensão diferente daquelas listadas acima, nesta versão prévia, ela não será persistida no sistema. Isso significa que ele não estará disponível após a reinicialização do sistema e você precisará trazê-lo novamente.

Este guia executará um cenário para usar duas dessas extensões:
- [`PostGIS`](https://postgis.net/)
- [`pg_cron`](https://github.com/citusdata/pg_cron)

## <a name="which-extensions-need-to-be-added-to-the-shared_preload_libraries-and-created"></a>Quais extensões precisam ser adicionadas ao shared_preload_libraries e criadas?

|Extensões   |Requer que seja adicionado a shared_preload_libraries  |Requer a criação |
|-------------|--------------------------------------------------|---------------------- |
|`pg_cron`      |Não       |Sim        |
|`pg_audit`     |Sim       |Sim        |
|`plpgsql`      |Sim       |Sim        |
|`postgis`      |Não       |Sim        |
|`plv8`      |Não       |Sim        |

## <a name="add-extensions-to-the-shared_preload_libraries"></a>Adicionar extensões ao shared_preload_libraries
Para obter detalhes sobre esses shared_preload_libraries Leia a documentação do PostgreSQL [aqui](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SHARED-PRELOAD-LIBRARIES):
- Esta etapa não é necessária para as extensões que fazem parte do `contrib`
- Essa etapa não é necessária para extensões que não precisam ser pré-carregadas por shared_preload_libraries. Para essas extensões, você pode ir para o próximo próximo parágrafo [criar extensões](#create-extensions).

### <a name="add-an-extension-at-the-creation-time-of-a-server-group"></a>Adicionar uma extensão no momento da criação de um grupo de servidores
```console
azdata arc postgres server create -n <name of your postgresql server group> --extensions <extension names>
```
### <a name="add-an-extension-to-an-instance-that-already-exists"></a>Adicionar uma extensão a uma instância que já existe
```console
azdata arc postgres server edit -n <name of your postgresql server group> --extensions <extension names>
```




## <a name="show-the-list-of-extensions-added-to-shared_preload_libraries"></a>Mostrar a lista de extensões adicionadas ao shared_preload_libraries
Execute um dos comandos a seguir.

### <a name="with-an-azdata-cli-command"></a>Com um comando da CLI azdata
```console
azdata arc postgres server show -n <server group name>
```
Role a saída e observe as seções engine\extensions nas especificações do seu grupo de servidores. Por exemplo:
```console
"engine": {
      "extensions": [
        {
          "name": "citus"
        },
        {
          "name": "pg_cron"
        }
      ]
    },
```
### <a name="with-kubectl"></a>Com kubectl
```console
kubectl describe postgresql-12s/postgres02
```
Role a saída e observe as seções engine\extensions nas especificações do seu grupo de servidores. Por exemplo:
```console
Engine:
    Extensions:
      Name:  citus
      Name:  pg_cron
```


## <a name="create-extensions"></a>Criar extensões
Conecte-se ao seu grupo de servidores com a ferramenta de cliente de sua escolha e execute a consulta PostgreSQL padrão:
```console
CREATE EXTENSION <extension name>;
```

## <a name="show-the-list-of-extensions-created"></a>Mostrar a lista de extensões criadas
Conecte-se ao seu grupo de servidores com a ferramenta de cliente de sua escolha e execute a consulta PostgreSQL padrão:
```console
select * from pg_extension;
```

## <a name="drop-an-extension"></a>Remover uma extensão
Conecte-se ao seu grupo de servidores com a ferramenta de cliente de sua escolha e execute a consulta PostgreSQL padrão:
```console
drop extension <extension name>;
```

## <a name="the-postgis-extension"></a>A `PostGIS` extensão
Você não precisa adicionar a `PostGIS` extensão ao `shared_preload_libraries` .
Obtenha [dados de exemplo](http://duspviz.mit.edu/tutorials/intro-postgis/) do departamento do MIT de estudos urbanos & planejamento. Execute `apt-get install unzip` para instalar a descompactação conforme necessário.

```console
wget http://duspviz.mit.edu/_assets/data/intro-postgis-datasets.zip
unzip intro-postgis-datasets.zip
```

Vamos nos conectar ao nosso banco de dados e criar a `PostGIS` extensão:

```console
CREATE EXTENSION postgis;
```

> [!NOTE]
> Se você quiser usar uma das extensões no `postgis` pacote (por exemplo,,,... `postgis_raster` ), `postgis_topology` `postgis_sfcgal` `fuzzystrmatch` precisará primeiro criar a extensão PostGIS e, em seguida, criar a outra extensão. Por exemplo: `CREATE EXTENSION postgis` ; `CREATE EXTENSION postgis_raster` ;

E criar o esquema:

```sql
CREATE TABLE coffee_shops (
  id serial NOT NULL,
  name character varying(50),
  address character varying(50),
  city character varying(50),
  state character varying(50),
  zip character varying(10),
  lat numeric,
  lon numeric,
  geom geometry(POINT,4326)
);
CREATE INDEX coffee_shops_gist ON coffee_shops USING gist (geom);
```

Agora, podemos combinar `PostGIS` com a funcionalidade de expansão, tornando a tabela de coffee_shops distribuída:

```sql
SELECT create_distributed_table('coffee_shops', 'id');
```

Vamos carregar alguns dados:

```console
\copy coffee_shops(id,name,address,city,state,zip,lat,lon) from cambridge_coffee_shops.csv CSV HEADER;
```

E preencha o `geom` campo com a latitude e a longitude codificada corretamente no `PostGIS` `geometry` tipo de dados:

```sql
UPDATE coffee_shops SET geom = ST_SetSRID(ST_MakePoint(lon,lat),4326);
```

Agora, podemos listar as cafeterias mais próximas ao MIT (77 Massachusetts Ave às 42,359055,-71, 93500):

```sql
SELECT name, address FROM coffee_shops ORDER BY geom <-> ST_SetSRID(ST_MakePoint(-71.093500,42.359055),4326);
```


## <a name="the-pg_cron-extension"></a>A `pg_cron` extensão

Agora, vamos habilitar `pg_cron` em nosso grupo de servidores PostgreSQL adicionando-o ao shared_preload_libraries:

```console
azdata postgres server update -n pg2 -ns arc --extensions pg_cron
```

O grupo de servidores será reiniciado para concluir a instalação das extensões. Pode levar de 2 a 3 minutos.

Agora podemos se conectar novamente e criar a `pg_cron` extensão:

```sql
CREATE EXTENSION pg_cron;
```

Para fins de teste, vamos criar uma tabela `the_best_coffee_shop` que usa um nome aleatório de nossa `coffee_shops` tabela anterior e insere o conteúdo da tabela:

```sql
CREATE TABLE the_best_coffee_shop(name text);
```

Podemos usar `cron.schedule` mais algumas instruções SQL para obter um nome de tabela aleatório (Observe o uso de uma tabela temporária para armazenar um resultado de consulta distribuída) e armazená-la em `the_best_coffee_shop` :

```sql
SELECT cron.schedule('* * * * *', $$
  TRUNCATE the_best_coffee_shop;
  CREATE TEMPORARY TABLE tmp AS SELECT name FROM coffee_shops ORDER BY random() LIMIT 1;
  INSERT INTO the_best_coffee_shop SELECT * FROM tmp;
  DROP TABLE tmp;
$$);
```

E agora, uma vez por minuto, obteremos um nome diferente:

```sql
SELECT * FROM the_best_coffee_shop;
```

```console
      name
-----------------
 B & B Snack Bar
(1 row)
```

Consulte o [PG_CRON Leiame](https://github.com/citusdata/pg_cron) para obter detalhes completos sobre a sintaxe.


## <a name="next-steps"></a>Próximas etapas
- Leia a documentação sobre [`plv8`](https://plv8.github.io/)
- Leia a documentação sobre [`PostGIS`](https://postgis.net/)
- Leia a documentação sobre [`pg_cron`](https://github.com/citusdata/pg_cron)
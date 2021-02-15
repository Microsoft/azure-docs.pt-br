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
ms.openlocfilehash: 3b9c3c66e58ae51773a959aba0b2c76d97b44445
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92309511"
---
# <a name="use-postgresql-extensions-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Usar extensões PostgreSQL em seu grupo de servidores de hiperescala PostgreSQL habilitados para o Azure Arc

O PostgreSQL é o melhor quando usado com extensões. Na verdade, um elemento fundamental da nossa própria funcionalidade de hiperescala é a extensão fornecida `citus` pela Microsoft que é instalada por padrão, o que permite ao postgres fragmentar dados de forma transparente em vários nós.


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="list-of-extensions"></a>Lista de extensões
Além das extensões no [`contrib`](https://www.postgresql.org/docs/12/contrib.html) , a lista de extensões presentes nos contêineres do grupo de servidores de hiperescala PostgreSQL habilitados para o Azure Arc é:
- `citus`, v: 9,4
- `pg_cron`, v: 1,2
- `plpgsql`, v: 1,0
- `postgis`, v: 3.0.2
- `plv8`, v: 2.3.14

Esta lista desenvolve horas extras e as atualizações serão publicadas neste documento. Ainda não é possível adicionar extensões além das listadas acima.

Este guia executará um cenário para usar duas dessas extensões:
- [PostGIS](https://postgis.net/)
- [`pg_cron`](https://github.com/citusdata/pg_cron)


## <a name="manage-extensions"></a>Gerenciar extensões

### <a name="enable-extensions"></a>Habilitar extensões
Essa etapa não é necessária para as extensões que fazem parte do `contrib` .
O formato geral do comando para habilitar extensões é:

#### <a name="enable-an-extension-at-the-creation-time-of-a-server-group"></a>Habilite uma extensão no momento da criação de um grupo de servidores:
```console
azdata arc postgres server create -n <name of your postgresql server group> --extensions <extension names>
```
#### <a name="enable-an-extension-on-an-instance-that-already-exists"></a>Habilite uma extensão em uma instância que já existe:
```console
azdata arc postgres server edit -n <name of your postgresql server group> --extensions <extension names>
```

#### <a name="get-the-list-of-extensions-enabled"></a>Obter a lista de extensões habilitada:
Execute um dos comandos a seguir.

##### <a name="with-azure-data-cli-azdata"></a>Com [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]
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
##### <a name="with-kubectl"></a>Com kubectl
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


### <a name="create-extensions"></a>Criar extensões:
Conecte-se ao seu grupo de servidores com a ferramenta de cliente de sua escolha e execute a consulta PostgreSQL padrão:
```console
CREATE EXTENSION <extension name>;
```

### <a name="get-the-list-of-extension-created-in-your-server-group"></a>Obtenha a lista de extensões criada no seu grupo de servidores:
Conecte-se ao seu grupo de servidores com a ferramenta de cliente de sua escolha e execute a consulta PostgreSQL padrão:
```console
select * from pg_extension;
```

### <a name="drop-an-extension-from-your-server-group"></a>Remova uma extensão do seu grupo de servidores:
Conecte-se ao seu grupo de servidores com a ferramenta de cliente de sua escolha e execute a consulta PostgreSQL padrão:
```console
drop extension <extension name>;
```

## <a name="use-the-postgis-and-the-pg_cron-extensions"></a>Usar o PostGIS e as extensões de Pg_cron

### <a name="the-postgis-extension"></a>A extensão PostGIS

Podemos habilitar a extensão PostGIS em um grupo de servidores existente ou criar uma nova com a extensão já habilitada:

**Habilitando uma extensão no momento da criação de um grupo de servidores:**
```console
azdata arc postgres server create -n <name of your postgresql server group> --extensions <extension names>

#Example:
azdata arc postgres server create -n pg2 -w 2 --extensions postgis
```

**Habilitando uma extensão em uma instância que já existe:**
```console
azdata arc postgres server edit -n <name of your postgresql server group> --extensions <extension names>

#Example:
azdata arc postgres server edit --extensions postgis -n pg2
```

Para verificar quais extensões estão instaladas, use o comando PostgreSQL padrão abaixo depois de se conectar à instância com sua ferramenta de cliente do PostgreSQL favorita, como Azure Data Studio:
```console
select * from pg_extension;
```

Para um exemplo de PostGIS, primeiro, obtenha [dados de exemplo](http://duspviz.mit.edu/tutorials/intro-postgis/) do departamento do MIT de estudos urbanos & planejamento. Talvez seja necessário executar `apt-get install unzip` para instalar a descompactação ao usar a VM para teste.

```console
wget http://duspviz.mit.edu/_assets/data/intro-postgis-datasets.zip
unzip intro-postgis-datasets.zip
```

Vamos nos conectar ao nosso banco de dados e criar a extensão PostGIS:

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

Agora, podemos combinar PostGIS com a funcionalidade scale out, tornando a tabela coffee_shops distribuída:

```sql
SELECT create_distributed_table('coffee_shops', 'id');
```

Vamos carregar alguns dados:

```console
\copy coffee_shops(id,name,address,city,state,zip,lat,lon) from cambridge_coffee_shops.csv CSV HEADER;
```

E preencha o `geom` campo com a latitude e a longitude codificada corretamente no `geometry` tipo de dados PostGIS:

```sql
UPDATE coffee_shops SET geom = ST_SetSRID(ST_MakePoint(lon,lat),4326);
```

Agora, podemos listar as cafeterias mais próximas ao MIT (77 Massachusetts Ave às 42,359055,-71, 93500):

```sql
SELECT name, address FROM coffee_shops ORDER BY geom <-> ST_SetSRID(ST_MakePoint(-71.093500,42.359055),4326);
```


### <a name="the-pg_cron-extension"></a>A extensão pg_cron

Vamos habilitar `pg_cron` em nosso grupo de servidores PostgreSQL, além de PostGIS:

```console
azdata postgres server update -n pg2 -ns arc --extensions postgis,pg_cron
```

Observe que isso reiniciará os nós e instalará as extensões adicionais, o que pode levar de 2-3 minutos.

Agora podemos se conectar novamente e criar a `pg_cron` extensão:

```sql
CREATE EXTENSION pg_cron;
```

Para fins de teste, vamos criar uma tabela `the_best_coffee_shop` que usa um nome aleatório de nossa `coffee_shops` tabela anterior e define o conteúdo da tabela:

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

>[!NOTE]
>Não há suporte para descartar a `citus` extensão. A `citus` extensão é necessária para fornecer a experiência de hiperescala.

## <a name="next-steps"></a>Próximas etapas:
- Leia a documentação sobre o [plv8](https://plv8.github.io/)
- Leia a documentação sobre o [PostGIS](https://postgis.net/)
- Leia a documentação sobre [`pg_cron`](https://github.com/citusdata/pg_cron)

---
title: Migrar dados de um banco de dado PostgreSQL para um grupo de servidores de hiperescala PostgreSQL habilitado para o Azure Arc
titleSuffix: Azure Arc enabled database services
description: Migrar dados de um banco de dado PostgreSQL para um grupo de servidores de hiperescala PostgreSQL habilitado para o Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: d9cbfc30b10373ad2a4f4304987dac426b5dcabe
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101643568"
---
# <a name="migrate-postgresql-database-to-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Migrar banco de dados PostgreSQL para o grupo de servidores de hiperescala PostgreSQL habilitado para Arc do Azure

Este documento descreve as etapas para obter seu banco de dados PostgreSQL existente (um que não está hospedado no Azure Arc habilitados para o grupo de servidores de hiperescala do PostgreSQL) no Azure Arc habilitado.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="considerations"></a>Considerações

O grupo de servidores de hiperescala do PostgreSQL habilitado para o Azure Arc é a versão da Comunidade do PostgreSQL e é executado com a extensão CitusData habilitada. Portanto, qualquer ferramenta que funcione no PostgreSQL fora do arco do Azure deve funcionar com o grupo de servidores de hiperescala PostgreSQL habilitados para o Azure Arc.


Assim, com o conjunto de ferramentas que você usa hoje para Postgres, você deve ser capaz de:
1. Fazer backup do banco de dados Postgres de sua instância hospedada fora do arco do Azure
2. Restaure-o em seu grupo de servidores de hiperescala PostgreSQL habilitados para o Azure Arc

O que será deixado para você fazer é:
- redefinir os parâmetros do servidor
- Redefina os contextos de segurança: recriar usuários, funções e redefinir permissões...

Para fazer essa operação de backup/restauração, você pode usar qualquer ferramenta que seja capaz de fazer backup/restaurar para Postgres. Por exemplo: 
- Azure Data Studio e sua extensão postgres
- `pgcli`
- `pgAdmin`
- `pg_dump`
- `pg_restore`
- `psql`
- ...

## <a name="example"></a>Exemplo
Vamos ilustrar essas etapas usando a `pgAdmin` ferramenta.
Considere a seguinte configuração:
- **Origem:**  
    Um servidor postgres em execução local em um servidor bare-metal e chamado JEANYDSRV. É da versão 12 e hospeda um banco de dados chamado MyOnPremPostgresDB que tem uma tabela T1 que tem 1 linha :::image type="content" source="media/postgres-hyperscale/migrate-pg-source.jpg" alt-text="migrar-Source":::

- **Destino**  
    Um servidor postgres em execução em um ambiente de arco do Azure e chamado postgres01. É da versão 12. Ele não tem nenhum banco de dados, exceto o banco de dados Postgres padrão.  
    :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination.jpg" alt-text="Migrar-destino":::


### <a name="take-a-backup-of-the-source-database-on-premises"></a>Faça um backup do banco de dados de origem local

:::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup.jpg" alt-text="Migrar-origem-backup":::

Configure-o:
1. Dê a ele um nome de arquivo: **MySourceBackup**
2. Definir o formato para  
 :::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup2.jpg" alt-text="migração personalizada-origem-backup-configurar":::

O backup foi concluído com êxito:  
:::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup3.jpg" alt-text="Migrar-Source-backup-concluído":::

### <a name="create-an-empty-database-on-the-destination-system-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Criar um banco de dados vazio no sistema de destino em seu grupo de servidores de hiperescala PostgreSQL habilitado para o Azure Arc

> [!NOTE]
> Para registrar uma instância do postgres na `pgAdmin` ferramenta, você precisa usar o IP público de sua instância no cluster do kubernetes e definir a porta e o contexto de segurança adequadamente. Você encontrará esses detalhes na `psql` linha do ponto de extremidade depois de executar o seguinte comando:

```console
azdata arc postgres endpoint list -n postgres01
```
Isso retorna uma saída como:
```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

Vamos nomear o banco de dados de destino **RESTORED_MyOnPremPostgresDB**.

:::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbcreate.jpg" alt-text="Migrar-destino-DB-criar" lightbox="media/postgres-hyperscale/migrate-pg-destination-dbcreate.jpg":::

### <a name="restore-the-database-in-your-arc-setup"></a>Restaurar o banco de dados na configuração do seu arco

:::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore.jpg" alt-text="Migratre-DB-Restore":::

Configurar a restauração:
1. Aponte para o arquivo que contém o backup a ser restaurado: **MySourceBackup**
2. Mantenha o formato definido como **personalizado ou tar** 
    :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore2.jpg" alt-text="Migrate-DB-Restore-configure":::

3. Clique em **Restaurar**.  

   A restauração foi bem-sucedida.  
   :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore3.jpg" alt-text="Migrar-DB-Restore-Completed":::

### <a name="verify-that-the-database-was-successfully-restored-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Verifique se o banco de dados foi restaurado com êxito no grupo de servidores de hiperescala PostgreSQL habilitado para o Azure Arc

Use um dos seguintes métodos:

**De `pgAdmin` :**  

Expanda a instância postgres hospedada em sua configuração de arco do Azure. Você verá a tabela no banco de dados que você restaurou e ao selecionar os dados que ele mostra a mesma linha que ele tem na instância local:

   :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestoreverif.jpg" alt-text="Migrar-DB-Restore-Verification":::

**De `psql` dentro de sua configuração de Arc do Azure:**  

Na configuração de seu arco, você pode usar `psql` para se conectar à sua instância do Postgres, definir o contexto do banco de `RESTORED_MyOnPremPostgresDB` dados para e consultar o dado:

1. Liste os pontos de extremidade para ajudar da sua `psql` cadeia de conexão:

   ```console
   azdata arc postgres endpoint list -n postgres01
   [
     {
       "Description": "PostgreSQL Instance",
       "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
     },
     {
       "Description": "Log Search Dashboard",
       "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
     },
     {
       "Description": "Metrics Dashboard",
       "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
     }
   ]
   ```

1. Na `psql` cadeia de conexão, use o `-d` parâmetro para indicar o nome do banco de dados. Com o comando abaixo, você será solicitado a fornecer a senha:

   ```console
   psql -d RESTORED_MyOnPremPostgresDB -U postgres -h 10.0.0.4 -p 32639
   ```

   `psql` se conecta.

   ```output
   Password for user postgres:
   psql (10.12 (Ubuntu 10.12-0ubuntu0.18.04.1), server 12.3 (Debian 12.3-1.pgdg100+1))
   WARNING: psql major version 10, server major version 12.
         Some psql features might not work.
   SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
   Type "help" for help.

   RESTORED_MyOnPremPostgresDB=#   
   ```

1. Selecione a tabela e você verá os dados restaurados da instância postgres local:

   ```console
   RESTORED_MyOnPremPostgresDB=# select * from t1;
   ```

   ```output
    col1 |    col2
   ------+-------------
       1 | BobbyIsADog
   (1 row)
   ```

> [!NOTE]
> - Você não verá tantos benefícios de desempenho de execução em hiperescala PostgreSQL habilitada para o Arc do Azure até que você Escale/distribua os dados entre os nós de trabalho do seu grupo de servidores de hiperescala do PostgreSQL. Consulte [as próximas etapas](#next-steps).
>
> - Atualmente, não é possível "integrar-se ao Azure Arc" uma instância postgres existente que estaria em execução localmente ou em qualquer outra nuvem. Em outras palavras, não é possível instalar algum tipo de "agente Arc do Azure" em sua instância postgres existente para torná-lo uma instalação postgres habilitada pelo Arc do Azure. Em vez disso, você precisa criar uma nova instância do postgres e transferir dados para ela. Você pode usar a técnica mostrada acima para fazer isso ou pode usar qualquer ferramenta de ETL de sua escolha.

## <a name="next-steps"></a>Próximas etapas

- Leia os conceitos e os guias de instruções do banco de dados do Azure para PostgreSQL de hiperescala para distribuir seus dados em vários nós de hiperescala PostgreSQL e para se beneficiar de toda a potência do banco de dado do Azure para PostgreSQL hiperescala:
    * [Nós e tabelas](../../postgresql/concepts-hyperscale-nodes.md)
    * [Determinar o tipo de aplicativo](../../postgresql/concepts-hyperscale-app-type.md)
    * [Escolher uma coluna de distribuição](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Colocação de tabela](../../postgresql/concepts-hyperscale-colocation.md)
    * [Distribuir e modificar tabelas](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Criar um banco de dados de vários locatários](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Criar um painel de análise em tempo real](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

> * Nestes documentos, ignore as seções **entrar no portal do Azure** e **crie um banco de dados do Azure para postgres-hiperescala (Citus)**. Implemente as etapas restantes em sua implantação do Azure Arc. Essas seções são específicas para o banco de dados do Azure para PostgreSQL Citus (hiperescala) oferecida como um serviço de PaaS na nuvem do Azure, mas as outras partes dos documentos são diretamente aplicáveis à sua hiperescala PostgreSQL habilitada para o Arc do Azure.

- [Escalar horizontalmente o grupo de servidores de Hiperescala do Banco de Dados do Azure para PostgreSQL](scale-out-postgresql-hyperscale-server-group.md)

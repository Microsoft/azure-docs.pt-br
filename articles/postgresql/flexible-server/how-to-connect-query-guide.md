---
title: Conexão e consulta – PostgreSQL do servidor flexível
description: Links para guias de início rápido que mostram como se conectar ao banco de dados do Azure para PostgreSQL servidor flexível e executar consultas.
services: postgresql
ms.service: postgresql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 12/08/2020
ms.openlocfilehash: ee3b1f7db8bdafb1233b32579e032e8c864c37a9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97364549"
---
# <a name="connect-and-query-overview-for-azure-database-for-postgresql--flexible-server"></a>Visão geral de conexão e consulta do banco de dados do Azure para PostgreSQL – servidor flexível

O documento a seguir inclui links para exemplos que mostram como se conectar e consultar com o banco de dados do Azure para PostgreSQL servidor único. Este guia também inclui recomendações e extensões de TLS que você pode usar para se conectar ao servidor em idiomas com suporte abaixo.

>[!IMPORTANT]
> O banco de dados do Azure para PostgreSQL servidor flexível está em versão **prévia**.

## <a name="quickstarts"></a>Inícios rápidos

| Guia de Início Rápido | Descrição |
|---|---|
|[PgAdmin](https://www.pgadmin.org/)|Você pode usar o pgAdmin para se conectar ao servidor e simplifica a criação, a manutenção e o uso de objetos de banco de dados.|
|[psql em Azure Cloud Shell](./quickstart-create-server-cli.md#connect-using-postgresql-command-line-client)|Este artigo mostra como executar [**psql**](https://www.postgresql.org/docs/current/static/app-psql.html) no [Azure cloud Shell](../../cloud-shell/overview.md) para se conectar ao servidor e, em seguida, executar instruções para consultar, inserir, atualizar e excluir dados no banco de dado. Você pode executar o **psql** se estiver instalado em seu ambiente de desenvolvimento|
|[Python](connect-python.md)|Este guia de início rápido demonstra como usar o Python para conectar-se a um banco de dados e usar o trabalho com objetos de banco para consulta. |
|[Django com o serviço de aplicativo](tutorial-django-app-service-postgres.md)|Este tutorial demonstra como usar o Ruby para criar um programa a fim de se conectar a um banco de dados e usar o trabalho com objetos de banco para consulta.|

## <a name="tls-considerations-for-database-connectivity"></a>Considerações sobre o TLS para a conectividade de banco de dados

O protocolo TLS é usado por todos os drivers que a Microsoft fornece ou dá suporte para conectar-se a bancos de dados no Azure Database para PostgreSQL. Nenhuma configuração especial é necessária, mas impor o TLS 1,2 para servidores recém-criados. Recomendamos se você estiver usando o TLS 1,0 e 1,1 e atualizar a versão do TLS para seus servidores. Consulte [como configurar o TLS](how-to-connect-tls-ssl.md)

## <a name="postgresql-extensions"></a>Extensões do PostgreSQL

O PostgreSQL fornece a capacidade de estender a funcionalidade de seu banco de dados usando as extensões. As extensões agrupam vários objetos SQL em um pacote que pode ser carregado ou removido do seu banco de dados com um comando. Depois de carregadas no banco de dados, as extensões funcionam como recursos internos.

- [Extensões do postgres 12](./concepts-extensions.md#postgres-12-extensions)
- [Extensões do postgres 11](./concepts-extensions.md#postgres-11-extensions)
- [dblink e postgres_fdw](./concepts-extensions.md#dblink-and-postgres_fdw)
- [pg_prewarm](./concepts-extensions.md#pg_prewarm)
- [pg_stat_statements](./concepts-extensions.md#pg_stat_statements)

Para obter mais detalhes, consulte [como usar as extensões do PostgreSQL no servidor flexível](concepts-extensions.md).

## <a name="next-steps"></a>Próximas etapas

- [Migrar dados usando despejo e restauração](../howto-migrate-using-dump-and-restore.md)
- [Migrar dados usando importação e exportação](../howto-migrate-using-export-and-import.md)

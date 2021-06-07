---
title: Conectar e consultar um PostgreSQL de servidor único
description: Links para guias de início rápido que mostram como se conectar ao banco de dados do Azure para PostgreSQL servidor único e executar consultas.
services: postgresql
ms.service: postgresql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 09/21/2020
ms.openlocfilehash: 1506ce311fe443247050a36e1b9fa4600360ac6e
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604134"
---
# <a name="connect-and-query-overview-for-azure-database-for-postgresql--single-server"></a>Conectar e consultar a visão geral para o banco de dados do Azure para PostgreSQL – Servidor Único

O documento a seguir inclui links para exemplos que mostram como se conectar e consultar com o banco de dados do Azure para PostgreSQL servidor único. Este guia também inclui recomendações e extensões de TLS que você pode usar para se conectar ao servidor em idiomas com suporte abaixo.

## <a name="quickstarts"></a>Inícios rápidos

| Guia de Início Rápido | Descrição |
|---|---|
|[PgAdmin](https://www.pgadmin.org/)|Você pode usar o pgAdmin para se conectar ao servidor e simplifica a criação, a manutenção e o uso de objetos de banco de dados.|
|[psql em Azure Cloud Shell](quickstart-create-server-database-azure-cli.md#connect-to-the-azure-database-for-postgresql-server-by-using-psql)|Este artigo mostra como executar [**psql**](https://www.postgresql.org/docs/current/static/app-psql.html) no [Azure cloud Shell](../cloud-shell/overview.md) para se conectar ao servidor e, em seguida, executar instruções para consultar, inserir, atualizar e excluir dados no banco de dado. Você pode executar o **psql** se estiver instalado em seu ambiente de desenvolvimento|
|[PostgreSQL com VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)|A extensão de bancos de dados do Azure para VS Code (versão prévia) permite procurar e consultar o servidor PostgreSQL localmente e na nuvem usando recortes com o IntelliSense avançado. |
|[PHP](connect-php.md)|Este guia de início rápido demonstra como usar o PHP para criar um programa para se conectar a um banco de dados e usar o trabalho com objetos de banco para consulta.|
|[Java](connect-java.md)|Este guia de início rápido demonstra como usar o Java para se conectar a um banco de dados e, em seguida, usar o trabalho com objetos de banco para consulta.|
|[Node.js](connect-nodejs.md)|Este guia de início rápido demonstra como usar Node.js para criar um programa a fim de se conectar a um banco de dados e usar o trabalho com objetos de banco para consulta.|
|[.NET (C#)](connect-csharp.md)|Este guia de início rápido demonstra como use.NET (C#) para criar um programa em C# a fim de se conectar a um banco de dados e usar o trabalho com objetos de banco para consulta.|
|[Go](connect-go.md)|Este guia de início rápido demonstra como usar o Go para se conectar a um banco de dados. As instruções Transact-SQL para consultar e modificar dados também são demonstradas.|
|[Python](connect-python.md)|Este guia de início rápido demonstra como usar o Python para conectar-se a um banco de dados e usar o trabalho com objetos de banco para consulta. |
|[Ruby](connect-ruby.md)|Este guia de início rápido demonstra como usar o Ruby para criar um programa a fim de se conectar a um banco de dados e usar o trabalho com objetos de banco de dados do para consultar o|

## <a name="tls-considerations-for-database-connectivity"></a>Considerações sobre o TLS para a conectividade de banco de dados

O protocolo TLS é usado por todos os drivers que a Microsoft fornece ou dá suporte para conectar-se a bancos de dados no Azure Database para PostgreSQL. Nenhuma configuração especial é necessária, mas impor o TLS 1,2 para servidores recém-criados. Recomendamos se você estiver usando o TLS 1,0 e 1,1 e atualizar a versão do TLS para seus servidores. Consulte [como configurar o TLS](howto-tls-configurations.md)

## <a name="postgresql-extensions"></a>Extensões do PostgreSQL

O PostgreSQL fornece a capacidade de estender a funcionalidade de seu banco de dados usando as extensões. As extensões agrupam vários objetos SQL em um pacote que pode ser carregado ou removido do seu banco de dados com um comando. Depois de carregadas no banco de dados, as extensões funcionam como recursos internos.

- [Extensões do postgres 11](./concepts-extensions.md#postgres-11-extensions)
- [Extensões do postgres 10](./concepts-extensions.md#postgres-10-extensions)
- [Extensões do postgres 9,6](./concepts-extensions.md#postgres-96-extensions)

Para obter mais detalhes, consulte [como usar as extensões do PostgreSQL em um único servidor](concepts-extensions.md).

## <a name="next-steps"></a>Próximas etapas

- [Migrar dados usando despejo e restauração](howto-migrate-using-dump-and-restore.md)
- [Migrar dados usando importação e exportação](howto-migrate-using-export-and-import.md)

---
title: Versões com suporte no banco de dados do Azure para PostgreSQL – servidor único
description: Descreve as versões com suporte no banco de dados do Azure para PostgreSQL – servidor único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/11/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: c328f0dc510c1b7d36b547e6560a292f98d72f6f
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448034"
---
# <a name="supported-postgresql-database-versions"></a>Versões suportadas do banco de dados do PostgreSQL
A Microsoft pretende dar suporte a versões n-2 do mecanismo PostgreSQL no banco de dados do Azure para PostgreSQL – servidor único. As versões seriam a versão principal atual no Azure (n) e as duas versões principais anteriores (-2).

No momento, o Banco de Dados do Azure para PostgreSQL oferece suporte às seguintes versões:

## <a name="postgresql-version-112"></a>PostgreSQL versão 11.2
Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-2.html) para saber mais sobre aperfeiçoamentos e correções nesta versão secundária.

>[!NOTE]
> PostgreSQL versão 11 está disponível na visualização. Suporte para criação usando o portal do Azure está sendo distribuído e não ainda estejam disponível em sua região. Você pode usar o [CLI do Azure](quickstart-create-server-database-azure-cli.md) para criar um servidor Postgres 11 em qualquer região. Por exemplo: `az postgres server create -g group -n server -u username -p password -l westeurope --sku-name GP_Gen5_2 --version 11`.

## <a name="postgresql-version-107"></a>Versão do PostgreSQL 10.7
Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/10/static/release-10-7.html) para saber mais sobre aperfeiçoamentos e correções nesta versão secundária.

## <a name="postgresql-version-9612"></a>Versão do PostgreSQL 9.6.12
Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-12.html) para saber mais sobre aperfeiçoamentos e correções nesta versão secundária.

## <a name="postgresql-version-9516"></a>Versão do PostgreSQL 9.5.16
Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/9.5/static/release-9-5-16.html) para saber mais sobre aperfeiçoamentos e correções nesta versão secundária.

## <a name="managing-updates-and-upgrades"></a>Gerenciar atualizações e upgrades
O Banco de Dados do Azure para PostgreSQL gerencia automaticamente os patches de versões secundárias. Atualmente, upgrade da versão principal não tem suporte. Por exemplo, não há suporte para atualização do PostgreSQL 9.5 para PostgreSQL 9.6. Se você quiser atualizar para a próxima versão principal, crie um banco de dados [dump e restaure](./howto-migrate-using-dump-and-restore.md) para um servidor que foi criado com a nova versão do mecanismo.

> Observe que, antes do PostgreSQL versão 10, o [política de controle de versão do PostgreSQL](https://www.postgresql.org/support/versioning/) considerado um _versão principal_ atualização seja um aumento na primeira _ou_ segundo número (para o exemplo, 9.5 para 9.6 era considerada um _principais_ atualização de versão).
> A partir da versão 10, apenas uma alteração no primeiro número é considerada uma atualização de versão principal (por exemplo, 10.0 para 10.1 é um _secundárias_ atualização de versão e de 10 a 11 é uma _principais_ atualização de versão).

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre o suporte a extensões do PostgreSQL diferentes, veja [Extensões do PostgreSQL](concepts-extensions.md).

---
title: Versões com suporte-banco de dados do Azure para PostgreSQL-servidor único
description: Descreve as versões principais e secundárias postgres com suporte no banco de dados do Azure para PostgreSQL-servidor único.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/16/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: b372c9d8be31ab6f51d737cd00c692ac9242e4db
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605265"
---
# <a name="supported-postgresql-major-versions"></a>Versões principais do PostgreSQL com suporte

Consulte a [política de controle de versão do banco de dados do Azure para PostgreSQL](concepts-version-policy.md) para obter detalhes da política de suporte.

O banco de dados do Azure para PostgreSQL atualmente dá suporte às seguintes versões principais:

## <a name="postgresql-version-11"></a>PostgreSQL versão 11
A versão secundária atual é 11,6. Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-6.html) para saber mais sobre melhorias e correções nesta versão secundária.

## <a name="postgresql-version-10"></a>PostgreSQL versão 10
A versão secundária atual é 10,11. Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/10/static/release-10-11.html) para saber mais sobre melhorias e correções nesta versão secundária.

## <a name="postgresql-version-96"></a>PostgreSQL versão 9,6
A versão secundária atual é 9.6.16. Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-16.html) para saber mais sobre melhorias e correções nesta versão secundária.

## <a name="postgresql-version-95-retired"></a>PostgreSQL versão 9,5 (desativada)
Alinhando com a política de [controle de versão](https://www.postgresql.org/support/versioning/)da Comunidade do Postgres, o banco de dados do Azure para PostgreSQL desativou o Postgres versão 9,5 a partir de 11 de fevereiro de 2021. Consulte a [política de controle de versão do banco de dados do Azure para PostgreSQL](concepts-version-policy.md) para obter mais detalhes e restrições. Se você estiver executando essa versão principal, atualize para uma versão superior, preferencialmente para o PostgreSQL 11 na sua primeira conveniência.

## <a name="managing-upgrades"></a>Gerenciando atualizações
O projeto PostgreSQL emite regularmente versões secundárias para corrigir bugs relatados. O Banco de Dados do Azure para PostgreSQL corrige automaticamente os servidores com versões secundárias durante as implantações mensais do serviço. 

Não há suporte para atualizações automáticas in-loco para versões principais. Para atualizar para uma versão principal mais alta, você pode 
   * Use um dos métodos documentados em [atualizações de versão principais usando despejo e restauração](./how-to-upgrade-using-dump-and-restore.md).
   * Use [pg_dump e pg_restore](./howto-migrate-using-dump-and-restore.md) para mover um banco de dados para um servidor criado com a nova versão do mecanismo.
   * Use o [serviço de migração de banco de dados do Azure](..\dms\tutorial-azure-postgresql-to-azure-postgresql-online-portal.md) para fazer atualizações online.

### <a name="version-syntax"></a>Sintaxe da versão
Antes do PostgreSQL versão 10, a [política de controle de versão do PostgreSQL](https://www.postgresql.org/support/versioning/) considerou uma atualização de _versão principal_ para ser um aumento no primeiro _ou_ segundo número. Por exemplo, 9,5 a 9,6 foi considerado uma atualização de versão _principal_ . A partir da versão 10, apenas uma alteração no primeiro número é considerada uma atualização de versão principal. Por exemplo, 10,0 a 10,1 é uma atualização de versão _secundária_ . A versão 10 a 11 é uma atualização de versão _principal_ .

## <a name="next-steps"></a>Próximas etapas
Para obter informações sobre extensões PostgreSQL com suporte, consulte [o documento extensões](concepts-extensions.md).

---
title: Versões com suporte – banco de dados do Azure para PostgreSQL-servidor flexível
description: Descreve as versões principais e secundárias do PostgreSQL com suporte no banco de dados do Azure para PostgreSQL – servidor flexível.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: b139e2f14379e0cd52ca72c347d49bcb4512c473
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92542073"
---
# <a name="supported-postgresql-major-versions-in-azure-database-for-postgresql---flexible-server"></a>Versões principais do PostgreSQL com suporte no banco de dados do Azure para PostgreSQL – servidor flexível

> [!IMPORTANT]
> O Servidor Flexível do Banco de Dados do Azure para PostgreSQL está em versão prévia

Banco de dados do Azure para PostgreSQL-o servidor flexível atualmente dá suporte às seguintes versões principais:

## <a name="postgresql-version-12"></a>PostgreSQL versão 12

A versão secundária atual é 12,4. Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/12/static/release-12-4.html) para saber mais sobre melhorias e correções nesta versão secundária.

## <a name="postgresql-version-11"></a>PostgreSQL versão 11

A versão secundária atual é 11,9. Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-9.html) para saber mais sobre melhorias e correções nesta versão secundária.

## <a name="postgresql-version-10-and-older"></a>PostgreSQL versão 10 e mais antigas

Não há suporte para PostgreSQL versão 10 e mais antigo para o banco de dados do Azure para PostgreSQL-servidor flexível. Use a opção de implantação de [servidor único](../concepts-supported-versions.md) se você precisar de versões mais antigas.

## <a name="managing-upgrades"></a>Gerenciando atualizações

O projeto PostgreSQL emite regularmente versões secundárias para corrigir bugs relatados. O banco de dados do Azure para PostgreSQL corrige automaticamente os servidores com versões secundárias durante as implantações mensais do serviço.

Ainda não há suporte para a automação da atualização de versão principal. Por exemplo, atualmente não há nenhuma atualização automática do PostgreSQL 11 para o PostgreSQL 12.<!-- To upgrade to the next major version, create a [database dump and restore](howto-migrate-using-dump-and-restore.md) to a server that was created with the new engine version.-->

<!--
## Next steps

For information on supported PostgreSQL extensions, see [the extensions document](concepts-extensions.md).
-->
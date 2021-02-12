---
title: Política de controle de versão-banco de dados do Azure para PostgreSQL-servidor único e servidor flexível (visualização)
description: Descreve a política em relação às versões principais e secundárias do postgres no banco de dados do Azure para PostgreSQL-servidor único.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/05/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: f325a43895e1e9d73b11c06662851d7654d31ddb
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "94331814"
---
# <a name="azure-database-for-postgresql-versioning-policy"></a>Política de controle de versão do banco de dados do Azure para PostgreSQL

Esta página descreve a política de versão do banco de dados do Azure para PostgreSQL e é aplicável aos modos de implantação banco de dados do Azure para PostgreSQL-servidor único e banco de dados do Azure para PostgreSQL – servidor flexível (visualização).

## <a name="supported--postgresql-versions"></a>Versões do PostgreSQL com suporte

O banco de dados do Azure para PostgreSQL dá suporte às seguintes versões de banco de dados.

| Versão | Servidor único | Servidor Flexível (versão prévia) |
| ----- | :------: | :----: |
| PostgreSQL 12 |  | X  | 
| PostgreSQL 11 | X | X |
| PostgreSQL 10 | X |  |
| PostgreSQL 9,6 | X |  |
| PostgreSQL 9,5 | X |  |

## <a name="major-version-support"></a>Suporte de versão principal
Cada versão principal do PostgreSQL terá suporte do banco de dados do Azure para PostgreSQL a partir da data em que o Azure começa a dar suporte à versão até que a versão seja desativada pela Comunidade PostgreSQL, conforme fornecido na [política de controle de versão da Comunidade PostgreSQL](https://www.postgresql.org/support/versioning/).

## <a name="minor-version-support"></a>Suporte de versão secundária
O banco de dados do Azure para PostgreSQL executa automaticamente atualizações de versão secundárias para a versão do PostgreSQL preferencial do Azure como parte da manutenção periódica. 

## <a name="major-version-retirement-policy"></a>Política de desativação da versão principal
A tabela a seguir fornece os detalhes de desativação para as versões principais do PostgreSQL. As datas seguem a [política de controle de versão da Comunidade PostgreSQL](https://www.postgresql.org/support/versioning/).

| Versão | What's New | Data de início do suporte do Azure | Data de baixa|
| ----- | ----- | ------ | ----- |
| PostgreSQL 9,5| [Recursos](https://www.postgresql.org/docs/9.5/release-9-5.html)  | 18 de abril de 2018    | 11 de fevereiro de 2021
| [PostgreSQL 9,6](https://www.postgresql.org/about/news/postgresql-96-released-1703/) | [Recursos](https://wiki.postgresql.org/wiki/NewIn96) | 18 de abril de 2018  | 11 de novembro de 2021
| [PostgreSQL 10](https://www.postgresql.org/about/news/postgresql-10-released-1786/) | [Recursos](https://wiki.postgresql.org/wiki/New_in_postgres_10) | 4 de junho de 2018  | 10 de novembro de 2022
| [PostgreSQL 11](https://www.postgresql.org/about/news/postgresql-11-released-1894/) | [Recursos](https://www.postgresql.org/docs/11/release-11.html) | 24 de julho de 2019  | 9 de novembro de 2023
| [PostgreSQL 12](https://www.postgresql.org/about/news/postgresql-12-released-1976/) | [Recursos](https://www.postgresql.org/docs/12/release-12.html) | 22 de setembro de 2020  | 14 de novembro de 2024

## <a name="retired-postgresql-engine-versions-not-supported-in-azure-database-for-postgresql"></a>Versões do mecanismo PostgreSQL desativadas sem suporte no banco de dados do Azure para PostgreSQL

Após a data de desativação para cada versão do banco de dados PostgreSQL, se você continuar executando a versão retirada, observe as seguintes restrições:
- Como a Comunidade não lançará correções de bugs ou correções de segurança adicionais, o banco de dados do Azure para PostgreSQL não corrigirá o mecanismo de banco de dados desativado em busca de bugs ou problemas de segurança ou, de outra forma, tomar medidas de segurança em relação ao mecanismo de banco de dados desativado. Você pode enfrentar vulnerabilidades de segurança ou outros problemas como resultado. No entanto, o Azure continuará executando a manutenção periódica e aplicação de patch para o host, o sistema operacional, OS contêineres e quaisquer outros componentes relacionados ao serviço.
- Se qualquer problema de suporte que você possa enfrentar estiver relacionado ao banco de dados PostgreSQL, talvez não seja possível fornecer suporte. Nesses casos, você precisará atualizar seu banco de dados para que possamos fornecer qualquer suporte.
- Você não poderá criar novos servidores de banco de dados para a versão desativada. No entanto, você poderá executar recuperações pontuais e criar réplicas de leitura para seus servidores existentes.
- Novos recursos de serviço desenvolvidos pelo banco de dados do Azure para PostgreSQL só podem estar disponíveis para versões de servidor de banco de dados com suporte.
- Os SLAs de tempo de atividade serão aplicados exclusivamente ao banco de dados do Azure para problemas relacionados ao serviço do PostgreSQL e não a nenhum tempo de inatividade causado por bugs relacionados ao mecanismo de banco de dados  
- No evento extremo de uma ameaça séria ao serviço causado pela vulnerabilidade do mecanismo de banco de dados PostgreSQL identificada na versão do banco de dados desativada, o Azure pode optar por interromper o servidor de banco de dados para proteger o serviço. Nesse caso, você será notificado para atualizar o servidor antes de colocar o servidor online.

## <a name="postgresql-version-syntax"></a>Sintaxe da versão PostgreSQL
Antes do PostgreSQL versão 10, a [política de controle de versão do PostgreSQL](https://www.postgresql.org/support/versioning/) considerou uma atualização de _versão principal_ para ser um aumento no primeiro _ou_ segundo número. Por exemplo, 9,5 a 9,6 foi considerado uma atualização de versão _principal_ . A partir da versão 10, apenas uma alteração no primeiro número é considerada uma atualização de versão principal. Por exemplo, 10,0 a 10,1 é uma atualização de versão _secundária_ . A versão 10 a 11 é uma atualização de versão _principal_ .

## <a name="next-steps"></a>Próximas etapas
- Consulte banco de dados do Azure para PostgreSQL- [versões com suporte](./concepts-supported-versions.md) de servidor único
- Consulte [versões com suporte](flexible-server/concepts-supported-versions.md) do banco de dados do Azure para PostgreSQL – servidor flexível (versão prévia)
- Para obter informações sobre como executar atualizações de versão principais, consulte a documentação de [atualizações de versão principal](how-to-upgrade-using-dump-and-restore.md) .
- Para obter informações sobre extensões PostgreSQL com suporte, consulte [o documento extensões](concepts-extensions.md).

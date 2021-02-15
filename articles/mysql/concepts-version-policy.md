---
title: Política de controle de versão-banco de dados do Azure para MySQL-servidor único e servidor flexível (visualização)
description: Descreve a política em relação às versões principais e secundárias do MySQL no banco de dados do Azure para MySQL
author: sr-msft
ms.author: srranga
ms.service: mysql
ms.topic: conceptual
ms.date: 11/03/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 4903f1e48eb2f33c68d62c635201474b841ed146
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94591505"
---
# <a name="azure-database-for-mysql-versioning-policy"></a>Política de controle de versão do banco de dados do Azure para MySQL

Esta página descreve a política de controle de versão do banco de dados do Azure para MySQL e é aplicável ao banco de dados do Azure para MySQL, modos de implantação de servidor único e banco de dados do Azure para MySQL (visualização).

## <a name="supported--mysql-versions"></a>Versões do MySQL com suporte

O banco de dados do Azure para MySQL dá suporte às seguintes versões de banco de dados.

| Versão | Servidor único | Servidor Flexível (versão prévia) |
| ----- | :------: | :----: |
| MySQL 8 | X |  | 
| MySQL 5,7 | X | X |
| MySQL 5,6| X |  |


## <a name="major-version-support"></a>Suporte de versão principal
Cada versão principal do MySQL terá suporte do banco de dados do Azure para MySQL a partir da data em que o Azure começa a dar suporte à versão até que a versão seja desativada pela Comunidade do MySQL, conforme fornecido na [política de controle de versão](https://www.mysql.com/support/eol-notice.html).

## <a name="minor-version-support"></a>Suporte de versão secundária
O banco de dados do Azure para MySQL executa automaticamente atualizações de versão secundárias para a versão do MySQL preferida do Azure como parte da manutenção periódica. 

## <a name="major-version-retirement-policy"></a>Política de desativação da versão principal
A tabela a seguir fornece os detalhes de desativação para as versões principais do MySQL. As datas seguem a [política de controle de versão do MySQL](https://www.mysql.com/support/eol-notice.html).

| Versão | What's New | Data de início do suporte do Azure | Data de baixa|
| ----- | ----- | ------ | ----- |
| [MySQL 5,6](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/)| [Recursos](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-49.html)  | 20 de março de 2018 | Fevereiro de 2021
| [MySQL 5,7](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/) | [Recursos](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-31.html) | 20 de março de 2018 | Outubro de 2023
| [MySQL 8](https://mysqlserverteam.com/whats-new-in-mysql-8-0-generally-available/) | [Recursos](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)) | 11 de dezembro de 2019 | Abril de 2026


## <a name="retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql"></a>Versões do mecanismo MySQL desativadas sem suporte no banco de dados do Azure para MySQL

Após a data de desativação para cada versão do banco de dados MySQL, se você continuar executando a versão retirada, observe as seguintes restrições:
- Como a Comunidade não lançará correções de bugs ou correções de segurança adicionais, o banco de dados do Azure para MySQL não corrigirá o mecanismo de banco de dados desativado em busca de bugs ou problemas de segurança ou, de outra forma, tomar medidas de segurança em relação ao mecanismo de banco de dados desativado. No entanto, o Azure continuará executando a manutenção periódica e aplicação de patch para o host, o sistema operacional, OS contêineres e quaisquer outros componentes relacionados ao serviço.
- Se qualquer problema de suporte que você possa enfrentar estiver relacionado ao banco de dados MySQL, talvez não seja possível fornecer suporte. Nesses casos, você precisará atualizar seu banco de dados para que possamos fornecer qualquer suporte.
- Você não poderá criar novos servidores de banco de dados para a versão desativada. No entanto, você poderá executar recuperações pontuais e criar réplicas de leitura para seus servidores existentes.
- Novos recursos de serviço desenvolvidos pelo banco de dados do Azure para MySQL só podem estar disponíveis para versões de servidor de banco de dados com suporte.
- Os SLAs de tempo de atividade serão aplicados exclusivamente a problemas relacionados ao serviço do banco de dados do Azure para MySQL e não a nenhum tempo de inatividade causado por bugs relacionados ao mecanismo de banco de dados  
- No evento extremo de uma ameaça séria ao serviço causado pela vulnerabilidade do mecanismo de banco de dados MySQL identificada na versão do banco de dados desativada, o Azure pode optar por interromper o nó de computação do seu servidor de banco de dados para proteger o serviço primeiro. Você será solicitado a atualizar o servidor antes de colocar o servidor online. Durante o processo de atualização, seus dados sempre serão protegidos usando backups automáticos executados no serviço que podem ser usados para restaurar de volta para a versão mais antiga, se desejado. 



## <a name="next-steps"></a>Próximas etapas
- Consulte o banco de dados do Azure para MySQL- [versões com suporte](./concepts-supported-versions.md) de servidor único
- Consulte [versões com suporte](flexible-server/concepts-supported-versions.md) do banco de dados do Azure para MySQL – servidor flexível (versão prévia)
- Consulte [despejo e restauração](./concepts-migrate-dump-restore.md) do MySQL para executar atualizações.

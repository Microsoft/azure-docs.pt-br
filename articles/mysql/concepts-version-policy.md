---
title: Política de suporte de versão-banco de dados do Azure para MySQL-servidor único e servidor flexível (visualização)
description: Descreve a política em relação às versões principais e secundárias do MySQL no banco de dados do Azure para MySQL
author: sr-msft
ms.author: srranga
ms.service: mysql
ms.topic: conceptual
ms.date: 11/03/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 8ad79f2f27864b4fbc78b7c104828230ff7f93bc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103465650"
---
# <a name="azure-database-for-mysql-version-support-policy"></a>Política de suporte da versão do banco de dados do Azure para MySQL

Esta página descreve a política de controle de versão do banco de dados do Azure para MySQL e é aplicável ao banco de dados do Azure para MySQL, modos de implantação de servidor único e banco de dados do Azure para MySQL (visualização).

## <a name="supported--mysql-versions"></a>Versões do MySQL com suporte

O banco de dados do Azure para MySQL foi desenvolvido no [MySQL Community Edition](https://www.mysql.com/products/community/), usando o mecanismo de armazenamento InnoDB. O serviço dá suporte a toda a versão principal atual com suporte da Comunidade, o MySQL 5,6, 5,7 e 8,0. O MySQL usa o esquema de nomenclatura X. Y. Z em que X é a versão principal, Y é a versão secundária e Z é o lançamento de correção de bug. Para obter mais informações sobre o esquema, confira a [documentação do MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

> [!NOTE]
> Na opção de implantação de servidor único, um gateway é usado para redirecionar as conexões para instâncias de servidor. Depois que a conexão for estabelecida, o cliente MySQL exibirá a versão do MySQL definida no gateway, não a versão real em execução na instância do servidor MySQL. Para determinar a versão da instância do servidor MySQL, use o `SELECT VERSION();` comando no prompt do MySQL.

Atualmente, o banco de dados do Azure para MySQL dá suporte às seguintes versões principais e secundárias do MySQL:

| Versão | Servidor único <br/> Versão secundária atual |Servidor Flexível (versão prévia) <br/> Versão secundária atual  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL Versão 5.6 |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html)(desativado) | Sem suporte|
|MySQL Versão 5.7 | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|MySQL versão 8,0 | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

Leia a política de suporte de versão para versões desativadas na [documentação da política de suporte de versão.](concepts-version-policy.md#retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql)

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
<!-- - You will not be able to create new database servers for the retired version. However, you will be able to perform point-in-time recoveries and create read replicas for your existing servers. -->
- Novos recursos de serviço desenvolvidos pelo banco de dados do Azure para MySQL só podem estar disponíveis para versões de servidor de banco de dados com suporte.
- Os SLAs de tempo de atividade serão aplicados exclusivamente a problemas relacionados ao serviço do banco de dados do Azure para MySQL e não a nenhum tempo de inatividade causado por bugs relacionados ao mecanismo de banco de dados  
- No evento extremo de uma ameaça séria ao serviço causado pela vulnerabilidade do mecanismo de banco de dados MySQL identificada na versão do banco de dados desativada, o Azure pode optar por interromper o nó de computação do seu servidor de banco de dados para proteger o serviço primeiro. Você será solicitado a atualizar o servidor antes de colocar o servidor online. Durante o processo de atualização, seus dados sempre serão protegidos usando backups automáticos executados no serviço que podem ser usados para restaurar de volta para a versão mais antiga, se desejado. 



## <a name="next-steps"></a>Próximas etapas
- Consulte o banco de dados do Azure para MySQL- [versões com suporte](./concepts-supported-versions.md) de servidor único
- Consulte [versões com suporte](flexible-server/concepts-supported-versions.md) do banco de dados do Azure para MySQL – servidor flexível (versão prévia)
- Consulte [despejo e restauração](./concepts-migrate-dump-restore.md) do MySQL para executar atualizações.

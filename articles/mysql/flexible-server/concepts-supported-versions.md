---
title: Versões com suporte – banco de dados do Azure para MySQL servidor flexível
description: Saiba quais versões do MySQL Server têm suporte no banco de dados do Azure para MySQL servidor flexível
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 7ad6a576262b8e722b16c81af544a9370c2b49b3
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93242255"
---
# <a name="supported-versions-for-azure-database-for-mysql---flexible-server"></a>Versões com suporte para o banco de dados do Azure para MySQL-servidor flexível


> [!IMPORTANT]
> O Banco de Dados do Azure para MySQL – Servidor Flexível está atualmente na versão prévia pública.


O banco de dados do Azure para MySQL servidor flexível é equipado com o [MySQL Community Edition](https://www.mysql.com/products/community/), usando o mecanismo InnoDB.

O MySQL usa o esquema de nomenclatura X.Y.Z. X é a versão principal, Y é a versão secundária e Z é a versão de correção de bug. Para obter mais informações sobre o esquema, confira a [documentação do MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

> [!NOTE]
> Para determinar a versão da instância do servidor MySQL, use o `SELECT VERSION();` comando no prompt do MySQL.

No momento, o Banco de Dados do Azure para MySQL dá suporte às seguintes versões:

## <a name="mysql-version-57"></a>MySQL Versão 5.7

Versão de correção de bug: 5.7.29

O serviço executa a aplicação automatizada de patch do hardware, do sistema operacional e do mecanismo de banco de dados subjacentes. A aplicação de patch inclui atualizações de segurança e software. Para o mecanismo MySQL, as atualizações de versão secundárias também são incluídas como parte da versão de manutenção planejada. Os usuários podem configurar o agendamento de aplicação de patch para que ele seja gerenciado pelo sistema ou definir um agendamento personalizado. Durante o agendamento de manutenção, o patch é aplicado e o servidor talvez precisará ser reiniciado como parte do processo de aplicação de patch para concluir a atualização. Com o agendamento personalizado, os usuários podem tornar o ciclo de aplicação de patch previsível e escolher uma janela de manutenção com impacto mínimo sobre os negócios. No geral, o serviço segue a agenda de lançamento mensal como parte do lançamento e da integração contínua.

Consulte as notas de [versão](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) do MySQL para saber mais sobre melhorias e correções nesta versão.

## <a name="managing-updates-and-upgrades"></a>Gerenciar atualizações e upgrades
O serviço gerencia automaticamente a aplicação de patch para atualizações de versão de correção de bug. Por exemplo, 5.7.29 para 5.7.30.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
>[Compilar um aplicativo PHP no Windows com o MySQL](../../app-service/tutorial-php-mysql-app.md)<br/>
>[Compilar aplicativo PHP no Linux com MySQL](../../app-service/tutorial-php-mysql-app.md?pivots=platform-linux%253fpivots%253dplatform-linux)<br/>
>[Compilar aplicativo Spring baseado em Java com MySQL](/azure/developer/java/spring-framework/spring-app-service-e2e?tabs=bash)<br/>
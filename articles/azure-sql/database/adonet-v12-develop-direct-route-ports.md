---
title: Portas além de 1433
description: As conexões cliente do ADO.NET para o Banco de Dados SQL do Azure pode ignorar o proxy e interagir diretamente com o banco de dados usando portas que não a 1433.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1, devx-track-dotnet
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 06/11/2020
ms.openlocfilehash: 0d009522ea0d0986233983f8725549b618ffb537
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91444880"
---
# <a name="ports-beyond-1433-for-adonet-45"></a>Portas além da 1433 para ADO.NET 4.5
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este tópico descreve o comportamento de conexão do Banco de Dados SQL do Azure para clientes que usam ADO.NET 4.5 ou uma versão mais recente.

> [!IMPORTANT]
> Para obter informações sobre a arquitetura de conectividade, confira [Arquitetura de conectividade de Banco de Dados SQL do Azure](connectivity-architecture.md).
>

## <a name="outside-vs-inside"></a>Fora versus dentro

Para conexões com o Banco de Dados SQL do Azure, devemos perguntar primeiro se o programa cliente é executado *fora* ou *dentro* do limite de nuvem do Azure. As subseções discutem dois cenários comuns.

### <a name="outside-client-runs-on-your-desktop-computer"></a>*Fora:* o cliente é executado em seu computador desktop

A porta 1433 é a única porta que deve estar aberta no computador desktop que hospeda o aplicativo cliente do Banco de Dados SQL.

### <a name="inside-client-runs-on-azure"></a>*Dentro:* o cliente é executado no Azure

Quando o cliente é executado dentro do limite de nuvem do Azure, ele usa o que podemos chamar de uma *rota direta* para interagir com o banco de dados SQL. Após o estabelecimento de uma conexão, as próximas interações entre o cliente e o banco de dados não envolvem nenhum Gateway de Banco de Dados SQL do Azure.

Esta é a sequência:

1. O ADO.NET 4.5 (ou posterior) inicia uma breve interação com a nuvem do Azure e recebe um número de porta identificado dinamicamente.

   * O número da porta identificada dinamicamente está no intervalo de 11000-11999.
2. Em seguida, o ADO.NET se conecta diretamente ao banco de dados SQL, sem intermediários entre eles.
3. As consultas são enviadas diretamente ao banco de dados, e os resultados são retornados diretamente ao cliente.

Verifique se os intervalos de porta de 11000-11999 no computador cliente do Azure estão disponíveis para interações de cliente do ADO.NET 4,5 com o banco de dados SQL.

* Em particular, as portas no intervalo devem estar livres de outros bloqueadores de saída.
* Em sua VM do Azure, o **Firewall do Windows com Segurança Avançada** controla as configurações de porta.
  
  * Você pode usar a [interface de usuário do firewall](/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access) a fim de adicionar uma regra para a qual você especifica o protocolo **TCP** junto com um intervalo de portas com a sintaxe **11000 a 11999**.

## <a name="version-clarifications"></a>Esclarecimentos da versão

Esta seção explica os identificadores que se referem a versões do produto. Ela também lista alguns emparelhamentos de versões entre produtos.

### <a name="adonet"></a>ADO.NET

* O ADO.NET 4.0 dá suporte ao protocolo TDS 7.3, mas não ao 7.4.
* O ADO.NET 4.5 e posterior dá suporte ao protocolo TDS 7.4.

### <a name="odbc"></a>ODBCODBC

* Microsoft SQL Server ODBC 11 ou posterior

### <a name="jdbc"></a>JDBC

* Microsoft SQL Server JDBC 4.2 ou posterior (na verdade, o JDBC 4.0 é compatível com o protocolo TDS 7.4, mas não implementa o “redirecionamento”)

## <a name="related-links"></a>Links relacionados

* O ADO.NET 4.6 foi lançado em 20 de julho de 2015. Um comunicado do blog da equipe do .NET está disponível [aqui](https://devblogs.microsoft.com/dotnet/announcing-net-framework-4-6/).
* O ADO.NET 4.5 foi lançado em 15 de agosto de 2012. Um comunicado do blog da equipe do .NET está disponível [aqui](https://devblogs.microsoft.com/dotnet/announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code/).
  * Uma postagem no blog sobre o ADO.NET 4.5.1 está disponível [aqui](https://devblogs.microsoft.com/dotnet/announcing-the-net-framework-4-5-1-preview/).

* Microsoft ODBC Driver 17 para SQL Server https://aka.ms/downloadmsodbcsql

* Conectar-se ao Banco de Dados SQL do Azure V12 por meio de redirecionamento https://techcommunity.microsoft.com/t5/DataCAT/Connect-to-Azure-SQL-Database-V12-via-Redirection/ba-p/305362

* [Lista de versões do protocolo TDS](https://www.freetds.org/)
* [Visão geral do desenvolvimento de Banco de Dados SQL](develop-overview.md)
* [Firewall do banco de dados SQL do Azure](firewall-configure.md)

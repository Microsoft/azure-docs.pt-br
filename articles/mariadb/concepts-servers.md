---
title: Conceitos de servidor no banco de dados do Azure para MariaDB
description: Este tópico fornece considerações e diretrizes para trabalhar com o Banco de Dados do Azure para servidores MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: f61f8740c9514f6276afb2ee84bcdccdc54c0710
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61040906"
---
# <a name="server-concepts-in-azure-database-for-mariadb"></a>Conceitos de servidor no banco de dados do Azure para MariaDB
Este artigo fornece considerações e diretrizes para trabalhar com o Banco de Dados do Azure para servidores MariaDB.

## <a name="what-is-an-azure-database-for-mariadb-server"></a>O que é um banco de dados do Azure para o servidor MariaDB?

Um Banco de Dados do Azure para o servidor MariaDB é um ponto administrativo central para vários bancos de dados. É a mesma construção de servidor do MariaDB com a qual você pode estar familiarizado no mundo local. Especificamente, o serviço Banco de Dados do Azure para MariaDB é gerenciado, fornece garantias de desempenho e expõe o acesso e os recursos no nível do servidor.

Um banco de dados do Azure para MariaDB:

- É criado dentro de uma assinatura do Azure.
- É o recurso pai para bancos de dados.
- Fornece um namespace para bancos de dados.
- É um contêiner com semântica de tempo de vida fortes – exclua um servidor e ele excluirá os bancos de dados contidos.
- Coloca recursos em uma região.
- Fornece um ponto de extremidade de conexão para acesso ao servidor e ao banco de dados.
- Fornece o escopo para políticas de gerenciamento que se aplicam a seus bancos de dados: logons, firewall, usuários, funções, configurações etc.
- Está disponível no mecanismo MariaDB versão 10.2. Para obter mais informações, consulte [ Banco de Dados do Azure Suportado para as versões do banco de dados do MariaDB ](./concepts-supported-versions.md).

Em um banco de dados do Azure para servidor MariaDB, você pode criar um ou vários bancos de dados. É possível optar por criar um único banco de dados por servidor para utilizar todos os recursos ou criar vários bancos de dados para compartilhar os recursos. Os preços são estruturados por servidor, com base na configuração do tipo de preço, vCores e armazenamento (GB). Para obter mais informações, consulte [Tipos de preço](./concepts-pricing-tiers.md).

## <a name="how-do-i-secure-an-azure-database-for-mariadb-server"></a>Como faço para proteger um banco de dados do Azure para MariaDB?

Os elementos a seguir ajudam a garantir o acesso seguro ao seu banco de dados.

|||
| :--| :--|
| **Autenticação e autorização** | Banco de dados do Azure para MariaDB dá suporte à autenticação de MySQL nativa. É possível se conectar e autenticar em um servidor com o logon de administrador do servidor. |
| **Protocolo** | O serviço oferece suporte a um protocolo baseado em mensagem usado pelo MySQL. |
| **TCP/IP** | O protocolo tem suporte em TCP/IP e em soquetes de domínio do Unix. |
| **Firewall** | Para ajudar a proteger seus dados, uma regra de firewall impede todo acesso ao servidor de banco de dados até que você especifique quais computadores têm permissão. Consulte [Banco de dados do Azure para regras de firewall do servidor MariaDB](./concepts-firewall-rules.md). |
| **SSL** | O serviço oferece suporte à imposição de conexões SSL entre seus aplicativos e o servidor de banco de dados. Confira [Configurar conectividade SSL em seu aplicativo para se conectar com segurança ao Banco de Dados do Azure para MariaDB](./howto-configure-ssl.md). |

## <a name="how-do-i-manage-a-server"></a>Como posso gerenciar um servidor?
Você pode gerenciar o Banco de Dados do Azure para servidores MariaDB usando o portal do Azure ou a CLI do Azure.

## <a name="next-steps"></a>Próximas etapas
- Para obter uma visão geral do serviço, consulte [Banco de Dados do Azure para Visão Geral do MariaDB](./overview.md)
- Para saber mais sobre cotas e limitações específicas de recursos com base em sua **camada de serviço**, confira [Camadas de serviço](./concepts-pricing-tiers.md)

<!-- - For information about connecting to the service, see [Connection libraries for Azure Database for MariaDB](./concepts-connection-libraries.md). -->

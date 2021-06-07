---
title: Servidores-Banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo fornece considerações e diretrizes para configurar e gerenciar o banco de dados do Azure para PostgreSQL-servidor único.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 1d1751ed56f63105ce7775ceaed9ea2dcca1f8bf
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628128"
---
# <a name="azure-database-for-postgresql---single-server"></a>Banco de Dados do Azure para PostgreSQL – Servidor único
Este artigo fornece considerações e diretrizes para trabalhar com o banco de dados do Azure para PostgreSQL-servidor único.

## <a name="what-is-an-azure-database-for-postgresql-server"></a>O que é um servidor do Banco de Dados do Azure para PostgreSQL?
Um servidor no banco de dados do Azure para PostgreSQL-opção de implantação de servidor único é um ponto administrativo central para vários bancos de dados. É a mesma construção de servidor PostgreSQL com a qual talvez você já esteja familiarizado no mundo local. Especificamente, o serviço PostgreSQL é gerenciado, oferece garantias de desempenho, expõe acesso e recursos no nível do servidor.

Um Banco de Dados do Azure para servidor PostgreSQL:

- É criado dentro de uma assinatura do Azure.
- É o recurso pai para bancos de dados.
- Fornece um namespace para bancos de dados.
- É um contêiner com semântica de tempo de vida fortes – exclua um servidor e ele excluirá os bancos de dados contidos.
- Coloca recursos em uma região.
- Fornece um ponto de extremidade de conexão para acesso ao servidor e ao banco de dados 
- Fornece o escopo para políticas de gerenciamento que se aplicam a seus bancos de dados: logons, firewall, usuários, funções, configurações etc.
- Está disponível em várias versões. Para obter mais informações, consulte [versões de banco de dados PostgreSQL com suporte](concepts-supported-versions.md).
- É extensível pelos usuários. Para saber mais, confira [Extensões do PostgreSQL](concepts-extensions.md).

Dentro de um banco de dados do Azure para o servidor PostgreSQL, você pode criar um ou mais bancos de dados. Você pode optar por criar um banco de dados por servidor para utilizar todos os recursos ou criar vários bancos de dados para compartilhar os recursos. Os preços são estruturados por servidor, com base na configuração do tipo de preço, vCores e armazenamento (GB). Para obter mais informações, consulte [Tipos de preço](./concepts-pricing-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-postgresql-server"></a>Como faço para me conectar e autenticar em um Banco de Dados do Azure para servidor PostgreSQL?
Os elementos a seguir ajudam a garantir o acesso seguro ao seu banco de dados:

|Conceito de segurança|Descrição|
|:--|:--|
| **Autenticação e autorização** | O Banco de Dados do Azure para servidor PostgreSQL oferece suporte à autenticação de PostgreSQL nativa. Você pode se conectar e autenticar no servidor com logon de administrador do servidor. |
| **Protocolo** | O serviço oferece suporte a um protocolo baseado em mensagem usado pelo PostgreSQL. |
| **TCP/IP** | O protocolo tem suporte em TCP/IP e em soquetes de domínio do Unix. |
| **Firewall** | Para ajudar a proteger seus dados, uma regra de firewall impede todo acesso ao servidor e seus bancos de dados até que você especifique quais computadores têm permissão. Confira [Regras de firewall do Banco de Dados do Azure para servidor PostgreSQL](concepts-firewall-rules.md). |

## <a name="managing-your-server"></a>Gerenciando o servidor
Você pode gerenciar o banco de dados do Azure para servidores PostgreSQL usando o [portal do Azure](https://portal.azure.com) ou o [CLI do Azure](/cli/azure/postgres).

Ao criar um servidor, você configura as credenciais de seu usuário administrador. O usuário administrador é o usuário com privilégio mais elevado no servidor. Ele pertence à função azure_pg_admin. Essa função não tem permissões completas de superusuário. 

O atributo de superusuário do PostgreSQL é atribuído a azure_superuser, que pertence ao serviço gerenciado. Você não tem acesso a essa função.

Um Banco de Dados do Azure para PostgreSQL possui bancos de dados padrão: 
- **postgres** – um banco de dados padrão a que você poderá se conectar após seu servidor ser criado.
- **azure_maintenance** – este banco de dados é usado para separar os processos que fornecem o serviço gerenciado das ações do usuário. Você não tem acesso a esse banco de dados.
- **azure_sys** – um banco de dados para o Repositório de Consultas. Este banco de dados não acumula dados quando o Repositório de Consultas está desativado; essa é a configuração padrão. Para obter mais informações, confira o tópico [Visão geral do Repositório de Consultas](concepts-query-store.md).


## <a name="server-parameters"></a>Parâmetros do Servidor
Os parâmetros de servidor PostgreSQL determinam a configuração do servidor. No Banco de Dados do Azure para PostgreSQL, a lista de parâmetros pode ser exibida e editada usando o portal do Azure ou a CLI do Azure. 

Como um serviço gerenciado para Postgres, os parâmetros configuráveis no banco de dados do Azure para PostgreSQL são um subconjunto dos parâmetros em uma instância local do Postgres (para obter mais informações sobre parâmetros Postgres, consulte o [PostgreSQL documentação](https://www.postgresql.org/docs/9.6/static/runtime-config.html)). O banco de dados do Azure para servidor PostgreSQL está habilitado com valores padrão para cada parâmetro na criação. Alguns parâmetros que necessitariam de um reinício de servidor ou de acesso de superusuário para as mudanças terem efeito não podem ser configurados pelo usuário.


## <a name="next-steps"></a>Próximas etapas
- Para obter uma visão geral do serviço, confira [Visão geral do Banco de Dados para PostgreSQL](overview.md).
- Para saber mais sobre cotas e limitações específicas de recursos com base em sua **camada de serviço**, confira [Camadas de serviço](concepts-pricing-tiers.md).
- Para saber mais sobre como se conectar ao serviço, confira [Bibliotecas de conexão para o Banco de Dados do Azure para PostgreSQL](concepts-connection-libraries.md).
- Exibir e editar os parâmetros de servidor por meio de [portal do Azure](howto-configure-server-parameters-using-portal.md) ou [CLI do Azure](howto-configure-server-parameters-using-cli.md).

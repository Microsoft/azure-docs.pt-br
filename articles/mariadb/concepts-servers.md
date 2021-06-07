---
title: Servidores-Banco de dados do Azure para MariaDB
description: Este tópico fornece considerações e diretrizes para trabalhar com o Banco de Dados do Azure para servidores MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 9a5cd55576d505114c15028cf637114f3baf7668
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627975"
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

| Conceito de segurança | Descrição |
| :-- | :-- |
| **Autenticação e autorização** | Banco de dados do Azure para MariaDB dá suporte à autenticação de MySQL nativa. É possível se conectar e autenticar em um servidor com o logon de administrador do servidor. |
| **Protocolo** | O serviço oferece suporte a um protocolo baseado em mensagem usado pelo MySQL. |
| **TCP/IP** | O protocolo tem suporte em TCP/IP e em soquetes de domínio do Unix. |
| **Firewall** | Para ajudar a proteger seus dados, uma regra de firewall impede todo acesso ao servidor de banco de dados até que você especifique quais computadores têm permissão. Consulte [Banco de dados do Azure para regras de firewall do servidor MariaDB](./concepts-firewall-rules.md). |
| **SSL** | O serviço oferece suporte à imposição de conexões SSL entre seus aplicativos e o servidor de banco de dados. Confira [Configurar conectividade SSL em seu aplicativo para se conectar com segurança ao Banco de Dados do Azure para MariaDB](./howto-configure-ssl.md). |

## <a name="stopstart-an-azure-database-for-mariadb-preview"></a>Parar/iniciar um banco de dados do Azure para MariaDB (versão prévia)
O banco de dados do Azure para MariaDB oferece a capacidade de **parar** o servidor quando não estiver em uso e **Iniciar** o servidor quando você retomar a atividade. Isso é essencialmente feito para economizar custos nos servidores de banco de dados e pagar apenas pelo recurso quando estiver em uso. Isso se torna ainda mais importante para cargas de trabalho de desenvolvimento e teste e quando você está usando o servidor apenas para parte do dia. Quando você parar o servidor, todas as conexões ativas serão descartadas. Posteriormente, quando você quiser colocar o servidor online novamente, poderá usar o [portal do Azure](../mysql/how-to-stop-start-server.md) ou a [CLI](../mysql/how-to-stop-start-server.md).

Quando o servidor estiver no estado **parado** , a computação do servidor não será cobrada. No entanto, o armazenamento continua a ser cobrado, pois o armazenamento do servidor permanece para garantir que os arquivos de dados estejam disponíveis quando o servidor for iniciado novamente.

> [!IMPORTANT]
> Quando você **interrompe** o servidor, ele permanece nesse estado para os próximos 7 dias em um Stretch. Se você não **iniciá** -lo manualmente durante esse tempo, o servidor será iniciado automaticamente no final de 7 dias. Você pode optar por **interrompê** -lo novamente se não estiver usando o servidor.

Durante a interrupção do servidor de horário, nenhuma operação de gerenciamento pode ser executada no servidor. Para alterar as definições de configuração no servidor, você precisará [iniciar o servidor](../mysql/how-to-stop-start-server.md).

### <a name="limitations-of-stopstart-operation"></a>Limitações da operação de parar/iniciar
- Sem suporte com configurações de réplica de leitura (origem e réplicas).

## <a name="how-do-i-manage-a-server"></a>Como posso gerenciar um servidor?
Você pode gerenciar o Banco de Dados do Azure para servidores MariaDB usando o portal do Azure ou a CLI do Azure.

## <a name="next-steps"></a>Próximas etapas
- Para obter uma visão geral do serviço, consulte [Banco de Dados do Azure para Visão Geral do MariaDB](./overview.md)
- Para obter informações sobre cotas e limitações de recursos específicas com base em sua **camada de serviço**, consulte [camadas de serviço](./concepts-pricing-tiers.md)

<!-- - For information about connecting to the service, see [Connection libraries for Azure Database for MariaDB](./concepts-connection-libraries.md). -->

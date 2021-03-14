---
title: Conceitos de servidor-banco de dados do Azure para MySQL
description: Este tópico fornece diretrizes e considerações para trabalhar com o Banco de Dados do Azure para servidores MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: ed6d5d676fd2c6eefd3288b7609446eb61611ed6
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517970"
---
# <a name="server-concepts-in-azure-database-for-mysql"></a>Conceitos de servidor no Banco de Dados do Azure para MySQL

Este artigo apresenta diretrizes e considerações para trabalhar com o Banco de Dados do Azure para servidores MySQL.

## <a name="what-is-an-azure-database-for-mysql-server"></a>O que é um Banco de Dados do Azure para servidor MySQL?

Um Banco de Dados do Azure para servidor MySQL é um ponto administrativo central para vários bancos de dados. É a mesma construção de servidor MySQL com a qual talvez você já esteja familiarizado no mundo local. Especificamente, o serviço de Banco de Dados do Azure para MySQL é gerenciado, oferece garantias de desempenho e expõe acesso e recursos no nível do servidor.

Um Banco de Dados do Azure para servidor MySQL:

- É criado dentro de uma assinatura do Azure.
- É o recurso pai para bancos de dados.
- Fornece um namespace para bancos de dados.
- É um contêiner com semântica de tempo de vida fortes – exclua um servidor e ele excluirá os bancos de dados contidos.
- Coloca recursos em uma região.
- Fornece um ponto de extremidade de conexão para acesso ao servidor e ao banco de dados.
- Fornece o escopo para políticas de gerenciamento que se aplicam a seus bancos de dados: logons, firewall, usuários, funções, configurações etc.
- Está disponível em várias versões. Para saber mais, confira [Banco de Dados do Azure com suporte para versões de banco de dados MySQL](./concepts-supported-versions.md).

Dentro de um banco de dados do Azure para o servidor MySQL, você pode criar um ou mais bancos de dados. É possível optar por criar um único banco de dados por servidor para utilizar todos os recursos ou criar vários bancos de dados para compartilhar os recursos. Os preços são estruturados por servidor, com base na configuração do tipo de preço, vCores e armazenamento (GB). Para obter mais informações, consulte [Tipos de preço](./concepts-pricing-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>Como faço para me conectar e autenticar em um Banco de Dados do Azure para servidor MySQL?

Os elementos a seguir ajudam a garantir o acesso seguro ao seu banco de dados.

|     |     |
| :-- | :-- |
| **Autenticação e autorização** | O Banco de Dados do Azure para servidor MySQL oferece suporte à autenticação de MySQL nativa. É possível se conectar e autenticar em um servidor com o logon de administrador do servidor. |
| **Protocolo** | O serviço oferece suporte a um protocolo baseado em mensagem usado pelo MySQL. |
| **TCP/IP** | O protocolo tem suporte em TCP/IP e em soquetes de domínio do Unix. |
| **Firewall** | Para ajudar a proteger seus dados, uma regra de firewall impede todo acesso ao servidor de banco de dados até que você especifique quais computadores têm permissão. Confira [Regras de firewall do Banco de Dados do Azure para servidor MySQL](./concepts-firewall-rules.md). |
| **SSL** | O serviço oferece suporte à imposição de conexões SSL entre seus aplicativos e o servidor de banco de dados.  Confira [Configurar conectividade SSL em seu aplicativo para se conectar com segurança ao Banco de Dados do Azure para MySQL](./howto-configure-ssl.md). |

## <a name="stopstart-an-azure-database-for-mysql"></a>Parar/iniciar um banco de dados do Azure para MySQL

O banco de dados do Azure para MySQL oferece a capacidade de **parar** o servidor quando ele não estiver em uso e **Iniciar** o servidor quando você retomar a atividade. Isso é essencialmente feito para economizar custos nos servidores de banco de dados e pagar apenas pelo recurso quando estiver em uso. Isso se torna ainda mais importante para cargas de trabalho de desenvolvimento e teste e quando você está usando o servidor apenas para parte do dia. Quando você parar o servidor, todas as conexões ativas serão descartadas. Posteriormente, quando você quiser colocar o servidor online novamente, poderá usar o [portal do Azure](how-to-stop-start-server.md) ou a [CLI](how-to-stop-start-server.md).

Quando o servidor estiver no estado **parado** , a computação do servidor não será cobrada. No entanto, o armazenamento continua a ser cobrado, pois o armazenamento do servidor permanece para garantir que os arquivos de dados estejam disponíveis quando o servidor for iniciado novamente.

> [!IMPORTANT]
> Quando você **interrompe** o servidor, ele permanece nesse estado para os próximos 7 dias em um Stretch. Se você não **iniciá** -lo manualmente durante esse tempo, o servidor será iniciado automaticamente no final de 7 dias. Você pode optar por **interrompê** -lo novamente se não estiver usando o servidor.

Durante a interrupção do servidor de horário, nenhuma operação de gerenciamento pode ser executada no servidor. Para alterar as definições de configuração no servidor, você precisará [iniciar o servidor](how-to-stop-start-server.md).

### <a name="limitations-of-stopstart-operation"></a>Limitações da operação de parar/iniciar
- Sem suporte com configurações de réplica de leitura (origem e réplicas).

## <a name="how-do-i-manage-a-server"></a>Como posso gerenciar um servidor?

Você pode gerenciar o Banco de Dados do Azure para servidores MySQL usando o Portal do Azure ou a CLI do Azure.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral do serviço, confira [Visão geral do Banco de Dados para MySQL](./overview.md)
- Para obter informações sobre cotas e limitações de recursos específicas com base em seu **tipo de preço**, consulte [tipos de preço](./concepts-pricing-tiers.md)
- Para saber mais sobre como se conectar ao serviço, confira [Bibliotecas de conexão para o Banco de Dados do Azure para MySQL](./concepts-connection-libraries.md).

---
title: Regras de firewall-hiperescala (Citus)-banco de dados do Azure para PostgreSQL
description: Este artigo descreve as regras de firewall para o banco de dados do Azure para PostgreSQL-Citus (hiperescala).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 559c5eca6fa8a6eceb37ade003d4f1983c0a1a1b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90902095"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---hyperscale-citus"></a>Regras de firewall no banco de dados do Azure para PostgreSQL-Citus (hiperescala)
O firewall de servidor do banco de dados do Azure para PostgreSQL impede todo o acesso ao nó do coordenador de hiperescala (Citus) até que você especifique quais computadores têm permissão. O firewall concede acesso ao servidor com base no endereço IP de origem de cada solicitação.
Para configurar seu firewall, você deve criar regras de firewall que especifiquem intervalos de endereços IP aceitáveis. Você pode criar regras de firewall no nível de servidor.

**Regras de firewall:** Essas regras permitem que os clientes acessem seu nó de coordenador de hiperescala (Citus), ou seja, todos os bancos de dados dentro do mesmo servidor lógico. As regras de firewall no nível de servidor podem ser configuradas usando o portal do Azure. Para criar regras de firewall no nível de servidor, você deve ser o proprietário da assinatura ou um colaborador da assinatura.

## <a name="firewall-overview"></a>Visão geral do firewall
Todos os acessos ao banco de dados ao seu nó de coordenador são bloqueados pelo firewall por padrão. Para começar a usar o servidor de outro computador, especifique uma ou mais regras de firewall no nível do servidor para permitir o acesso ao seu servidor. Use as regras de firewall para especificar quais intervalos de endereços IP da Internet permitir. O acesso em si ao site do Portal do Azure não é afetado pelas regras de firewall.
As tentativas de conexão da Internet e do Azure devem passar primeiramente pelo firewall antes de poderem acessar seu Banco de Dados PostgreSQL, conforme exibido no diagrama a seguir:

:::image type="content" source="media/concepts-hyperscale-firewall-rules/1-firewall-concept.png" alt-text="Fluxo de exemplo de como funciona o firewall":::

## <a name="connecting-from-the-internet-and-from-azure"></a>Conectando da Internet e do Azure

Um firewall de grupo de servidores de hiperescala (Citus) controla quem pode se conectar ao nó de coordenador do grupo. O firewall determina o acesso consultando uma lista configurável de regras. Cada regra é um endereço IP ou um intervalo de endereços que são permitidos no.

Quando o firewall bloqueia conexões, ele pode causar erros de aplicativo. O uso do driver JDBC do PostgreSQL, por exemplo, gera um erro como este:

> java.util.concurrent.ExecutionException: Java. lang. RuntimeException: org. PostgreSQL. util. PSQLException: FATAL: nenhuma \_ entrada do PG HBA. conf para o host "123.45.67.890", usuário "citus", banco de dados "citus", SSL

Consulte [criar e gerenciar regras de firewall](howto-hyperscale-manage-firewall-using-portal.md) para saber como as regras são definidas.

## <a name="troubleshooting-the-database-server-firewall"></a>Solução de problemas do firewall do servidor de banco de dados
Quando o acesso ao banco de dados de Microsoft Azure para PostgreSQL-Citus (serviço de hiperescala) não se comportar conforme o esperado, considere estes pontos:

* **As alterações na lista de permissões ainda não entraram em vigor:** Pode haver um atraso de até cinco minutos para que as alterações na configuração do firewall de hiperescala (Citus) entrem em vigor.

* **O usuário não está autorizado ou uma senha incorreta foi usada:** Se um usuário não tiver permissões no servidor ou a senha usada estiver incorreta, a conexão com o servidor será negada. Criar uma configuração de firewall apenas oferece aos clientes uma oportunidade de tentar se conectar ao servidor; cada cliente ainda deverá fornecer as credenciais de segurança necessárias.

Por exemplo, usando um cliente JDBC, o seguinte erro pode aparecer.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: falha na autenticação da senha para o usuário "seunomedeusuário"

* **Endereço IP dinâmico:** se você tiver uma conexão com a Internet com endereçamento IP dinâmico e estiver com dificuldades para atravessar o firewall, tente uma das seguintes soluções:

* Pergunte ao seu provedor de serviços de Internet (ISP) o intervalo de endereços IP atribuído aos seus computadores cliente que acessam o nó de coordenador de hiperescala (Citus) e, em seguida, adicione o intervalo de endereços IP como uma regra de firewall.

* Obtenha o endereçamento IP estático para os computadores cliente e adicione os endereços IP estáticos como uma regra de firewall.

## <a name="next-steps"></a>Próximas etapas
Para ver artigos sobre como criar regras de firewall no nível de servidor e de banco de dados, consulte:
* [Criar e gerenciar regras de firewall do Banco de Dados do Azure para PostgreSQL usando o Portal do Azure](howto-hyperscale-manage-firewall-using-portal.md)

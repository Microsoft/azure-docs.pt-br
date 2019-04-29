---
title: Conceitos de alta disponibilidade no Banco de Dados do Azure para MySQL
description: Este tópico fornece informações de alta disponibilidade ao usar o Banco de Dados do Azure para MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 055727695bfa1ce8a6bb160a7e071c2a161afb3b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837820"
---
# <a name="high-availability-concepts-in-azure-database-for-mysql"></a>Conceitos de alta disponibilidade no Banco de Dados do Azure para MySQL
O serviço do Banco de Dados do Azure para MySQL fornece garantia de alto nível de disponibilidade. O SLA (Contrato de Nível de Serviço) com suporte financeiro é 99,99% em disponibilidade geral. Não há praticamente nenhum tempo de inatividade de aplicativo ao usar este serviço.

## <a name="high-availability"></a>Alta disponibilidade
O modelo de alta disponibilidade (HA) se baseia em mecanismos internos de failover quando ocorre uma interrupção no nível do nó. Uma interrupção no nível do nó pode ocorrer devido a uma falha de hardware ou como reação a uma implantação de serviço.

Todas as vezes, as alterações feitas em um servidor de banco de dados do Banco de Dados do Azure para MySQL ocorrem no contexto de uma transação. As alterações são registradas de forma síncrona no armazenamento do Azure quando a transação é confirmada. Em caso de uma interrupção no nível do nó, o servidor de banco de dados cria automaticamente um novo nó e anexa o armazenamento de dados ao novo nó. As conexões ativas são descartadas e nenhuma transação em andamento é confirmada.

## <a name="application-retry-logic-is-essential"></a>A lógica de repetição do aplicativo é essencial
É importante que aplicativos de banco de dados do MySQL sejam compilados para detectar e repetir conexões descartadas e transações com falha. Quando o aplicativo realiza uma nova tentativa, a conexão do aplicativo é redirecionada de forma transparente para a instância recém-criada, o que assume o controle para a instância com falha.

Internamente no Azure, um gateway é usado para redirecionar as conexões para a nova instância. Após uma interrupção, normalmente o processo inteiro de failover leva dezenas de segundos. Como o redirecionamento é tratado internamente pelo gateway, a cadeia de conexão externa permanece a mesma para os aplicativos cliente.

## <a name="scaling-up-or-down"></a>Expandir ou reduzir
Semelhante ao modelo de alta disponibilidade, quando um Banco de Dados do Azure para MySQL é expandido ou reduzido, uma nova instância do servidor é criada com o tamanho especificado. O armazenamento de dados existente é desanexado da instância original e anexado à nova instância.

Durante a operação de escala, ocorre uma interrupção nas conexões de banco de dados. Os aplicativos cliente são desconectados e as transações abertas não confirmadas são canceladas. Depois que o aplicativo cliente repetir a tentativa ou realizar uma nova conexão, o gateway direcionará a conexão para a instância recém-dimensionada. 

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [tratamento de erros de conectividade transitórios](concepts-connectivity.md)
- Saiba como [replicar seus dados com réplicas de leitura](howto-read-replicas-portal.md)

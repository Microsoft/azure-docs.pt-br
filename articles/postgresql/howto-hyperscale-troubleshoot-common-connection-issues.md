---
title: Solucionar problemas de conexões-Citus (hiperescala)-banco de dados do Azure para PostgreSQL
description: Saiba como solucionar problemas de conexão com o banco de dados do Azure para PostgreSQL-Citus (hiperescala)
keywords: conexão do postgresql, cadeia de conexão, problemas de conectividade, erro transitório, erro de conexão
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 10/8/2019
ms.openlocfilehash: e1c6825820ae943d10157279dfe93922a7521b75
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91295610"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---hyperscale-citus"></a>Solucionar problemas de conexão com o banco de dados do Azure para PostgreSQL-Citus (hiperescala)

Problemas de conexão podem ser causados por várias coisas, como:

* Configurações de firewall
* Tempo limite da conexão
* Informações de entrada incorretas
* Limite de conexão atingido para o grupo de servidores
* Problemas com a infraestrutura do serviço
* Manutenção do serviço
* O nó do coordenador que está fazendo failover para um novo hardware

Geralmente, os problemas de conexão para o Citus (hiperescala) podem ser classificados da seguinte maneira:

* Erros transitórios (de curta duração ou intermitentes)
* Erros persistentes ou não transitórios (erros regularmente recorrentes)

## <a name="troubleshoot-transient-errors"></a>Solucionar problemas de erros transitórios

Os erros transitórios ocorrem por vários motivos. O mais comum é a manutenção do sistema, erro com hardware ou software e atualizações de vCore do nó de coordenador.

Habilitar a alta disponibilidade para nós do grupo de servidores Citus (hiperescala) pode atenuar esses tipos de problemas automaticamente. No entanto, seu aplicativo ainda deve estar preparado para perder sua conexão rapidamente. Outros eventos também podem levar mais tempo para serem mitigados, como quando uma transação grande causa uma recuperação de execução longa.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Etapas para resolver problemas de conectividade temporários

1. Verifique o [painel de serviço do Microsoft Azure](https://azure.microsoft.com/status) para qualquer interrupção conhecida que tenha ocorrido durante o tempo em que o aplicativo estava relatando erros.
2. Os aplicativos que se conectam a um serviço de nuvem, como o Citus (subscale), devem esperar erros transitórios e reagir normalmente. Por exemplo, os aplicativos devem implementar a lógica de repetição para lidar com esses erros em vez de identificando-los como erros de aplicativo para os usuários.
3. À medida que o grupo de servidores se aproxima de seus limites de recursos, os erros podem parecer problemas de conectividade transitórios. Aumentar a RAM do nó ou adicionar nós de trabalho e reequilibrar dados pode ajudar.
4. Se os problemas de conectividade continuarem ou durar mais de 60 segundos ou ocorrerem mais de uma vez por dia, faça um arquivo de uma solicitação de suporte do Azure selecionando **obter suporte** no site de [suporte do Azure](https://azure.microsoft.com/support/options) .

## <a name="troubleshoot-persistent-errors"></a>Solucionar erros persistentes

Se o aplicativo falhar de forma persistente ao se conectar ao Citus (subdimensionamento), as causas mais comuns serão erros de erro de firewall ou de usuário.

* Configuração de firewall do nó do Coordenador: Verifique se o Firewall do servidor de hiperescala (Citus) está configurado para permitir conexões do seu cliente, incluindo servidores proxy e gateways.
* Configuração de firewall do cliente: o firewall em seu cliente deve permitir conexões com o servidor de banco de dados. Alguns firewalls exigem permitir não apenas aplicativos por nome, mas permitem os endereços IP e as portas do servidor.
* Erro do usuário: Verifique a cadeia de conexão. Você pode ter parâmetros digitados informadamente, como o nome do servidor. Você pode encontrar cadeias de conexão para várias estruturas de linguagem e psql no portal do Azure. Vá para a página **cadeias de conexão** em seu grupo de servidores de hiperescala (Citus). Além disso, tenha em mente que os clusters de hiperescala (Citus) têm apenas um banco de dados e seu nome predefinido é **Citus**.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Etapas para resolver os problemas de conectividade temporários

1. Configure [as regras de firewall](howto-hyperscale-manage-firewall-using-portal.md) para permitir o endereço IP do cliente. Para fins de testes temporários, configure uma regra de firewall usando 0.0.0.0 como o endereço IP inicial e usando 255.255.255.255 como o endereço IP final. Essa regra abre o servidor para todos os endereços IP. Se a regra resolver o problema de conectividade, remova-a e crie uma regra de firewall para um endereço IP ou intervalo de endereços limitado apropriadamente.
2. Em todos os firewalls entre o cliente e a Internet, verifique se a porta 5432 está aberta para conexões de saída.
3. Verifique a cadeia de conexão e outras configurações de conexão.
4. Verifique a integridade do serviço no painel.

## <a name="next-steps"></a>Próximas etapas

* Conheça os conceitos de [regras de firewall no banco de dados do Azure para PostgreSQL-Citus (hiperescala)](concepts-hyperscale-firewall-rules.md)
* Consulte como [gerenciar regras de firewall para o banco de dados do Azure para PostgreSQL-Citus (hiperescala)](howto-hyperscale-manage-firewall-using-portal.md)

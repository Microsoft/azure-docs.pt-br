---
title: Proteção avançada contra ameaças-banco de dados do Azure para MariaDB
description: A Proteção Avançada contra Ameaças detecta as atividades anormais do banco de dados que indicam possíveis ameaças de segurança ao banco de dados.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 018ba33271f8a3a81c3b7109607334b076d8609c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98661612"
---
# <a name="azure-database-for-mariadb-advanced-threat-protection"></a>Banco de dados do Azure para proteção avançada contra ameaças do MariaDB

A proteção avançada contra ameaças para o banco de dados do Azure para MariaDB detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

> [!IMPORTANT]
> A proteção avançada contra ameaças está em visualização pública.

A Proteção Avançada contra Ameaças faz parte da oferta de Segurança Avançada de Dados, que é um pacote unificado para funcionalidades avançadas de segurança. A proteção avançada contra ameaças pode ser acessada e gerenciada por meio do [portal do Azure](https://portal.azure.com). O recurso está disponível para servidores Uso Geral e com otimização de memória.

> [!NOTE]
> A funcionalidade de Proteção Avançada contra Ameaças **não** está disponível nestas regiões de nuvem soberana e do Azure Governamental: US Gov – Texas, US Gov – Arizona, US Gov Iowa, US Gov – Virgínia, Leste do US DoD, Região Central do US DoD, Região Central da Alemanha, Norte da Alemanha, Leste da China e Leste da China 2. Visite [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) para ver a disponibilidade geral do produto.


## <a name="what-is-advanced-threat-protection"></a>O que é a Proteção Avançada contra Ameaças?

A proteção avançada contra ameaças para o banco de dados do Azure para MariaDB fornece uma nova camada de segurança, que permite que os clientes detectem e respondam a ameaças potenciais à medida que ocorrem, fornecendo alertas de segurança em atividades anormais. Os usuários receberão um alerta em caso de atividades suspeitas em bancos de dados, possíveis vulnerabilidades, bem como padrões anômalos de consultas e acesso a banco de dados. A proteção avançada contra ameaças para o banco de dados do Azure para MariaDB integra alertas com a [central de segurança do Azure](https://azure.microsoft.com/services/security-center/), que inclui detalhes de atividades suspeitas e recomenda ações sobre como investigar e atenuar a ameaça. A proteção avançada contra ameaças para o banco de dados do Azure para MariaDB simplifica a endereçamento de ameaças potenciais ao banco de dados sem a necessidade de ser um especialista em segurança ou gerenciar sistemas de monitoramento de segurança avançados. 

![Conceito da Proteção Avançada contra Ameaças](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Alertas da Proteção Avançada contra Ameaças 
A proteção avançada contra ameaças para o banco de dados do Azure para MariaDB detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados e pode disparar os seguintes alertas:
- **Acesso de local incomum**: esse alerta é disparado quando há uma alteração no padrão de acesso para o banco de dados do Azure para MariaDB Server, em que alguém fez logon no banco de dados do Azure para o servidor MariaDB de uma localização geográfica incomum. Em alguns casos, o alerta detecta uma ação legítima (um novo aplicativo ou manutenção do desenvolvedor). Em outros casos, o alerta detecta uma ação mal-intencionada (funcionário antigo, invasor externo).
- **Acesso de data center incomuns do Azure**: esse alerta é disparado quando há uma alteração no padrão de acesso para o banco de dados do Azure para MariaDB Server, em que alguém fez logon no servidor de um data center do Azure incomum que foi visto nesse servidor durante o período recente. Em alguns casos, o alerta detecta uma ação legítima (seu novo aplicativo no Azure, Power BI). Em outros casos, o alerta detecta uma ação mal-intencionada de um recurso/serviço do Azure (funcionário antigo, invasor externo).
- **Acesso a partir de entidade de segurança desconhecida**: esse alerta é disparado quando há uma alteração no padrão de acesso para o banco de dados do Azure para MariaDB Server, em que alguém fez logon no servidor usando uma entidade de segurança incomum (banco de dados do Azure para usuário MariaDB). Em alguns casos, o alerta detecta uma ação legítima (novo aplicativo ou manutenção do desenvolvedor). Em outros casos, o alerta detecta uma ação mal-intencionada (funcionário antigo, invasor externo).
- **Acesso a partir de um aplicativo potencialmente prejudicial**: Este alerta é disparado quando um aplicativo potencialmente prejudicial é usado para acessar o banco de dados. Em alguns casos, o alerta detecta um teste de segurança que está sendo executado. Em outros casos, o alerta detecta um ataque usando ferramentas comuns de ataque.
- **Força bruta banco de dados do Azure para credenciais MariaDB**: esse alerta é disparado quando há um grande número anormal de logons com falha com credenciais diferentes. Em alguns casos, o alerta detecta um teste de segurança que está sendo executado. Em outros casos, o alerta detecta ataques de força bruta.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a [Central de Segurança do Azure](../security-center/security-center-introduction.md)
* Para obter mais informações sobre preços, consulte a [página de preços do banco de dados do Azure para MariaDB](https://azure.microsoft.com/pricing/details/mariadb/) 
* Configurar o [banco de dados do Azure para proteção avançada contra ameaças do MariaDB](howto-database-threat-protection-portal.md) usando o portal do Azure
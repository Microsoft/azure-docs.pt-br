---
title: Proteção avançada contra ameaças-banco de dados do Azure para MariaDB | Microsoft Docs
description: A Proteção Avançada contra Ameaças detecta as atividades anormais do banco de dados que indicam possíveis ameaças de segurança ao banco de dados.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: a96d8c3b9f9e3f9b1aedf269bce141da3e4ccf42
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67869684"
---
# <a name="azure-database-for-mariadb-advanced-threat-protection"></a>Banco de dados do Azure para proteção avançada contra ameaças do MariaDB

A proteção avançada contra ameaças para o banco de dados do Azure para MariaDB detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

> [!IMPORTANT]
> A proteção avançada contra ameaças está em visualização pública.

A Proteção Avançada contra Ameaças faz parte da oferta de Segurança Avançada de Dados, que é um pacote unificado para funcionalidades avançadas de segurança. A proteção avançada contra ameaças pode ser acessada e gerenciada por meio do [portal do Azure](https://portal.azure.com). O recurso está disponível para servidores Uso Geral e com otimização de memória.

> [!NOTE]
> O recurso de Proteção Avançada contra Ameaças **não** está disponível nas seguintes regiões de nuvem soberana e do Azure Governamental: US Gov - Texas, US Gov - Arizona, US Gov Iowa, EUA, US Gov - Virgínia, US DoD Leste, US DoD Central, Alemanha Central, Norte da Alemanha, Leste da China, Leste da China 2. Visite [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) para ver a disponibilidade geral do produto.


## <a name="what-is-advanced-threat-protection"></a>O que é a Proteção Avançada contra Ameaças?

A proteção avançada contra ameaças para o banco de dados do Azure para MariaDB fornece uma nova camada de segurança, que permite que os clientes detectem e respondam a ameaças potenciais à medida que ocorrem, fornecendo alertas de segurança em atividades anormais. Os usuários receberão um alerta em caso de atividades suspeitas em bancos de dados, possíveis vulnerabilidades, bem como padrões anômalos de consultas e acesso a banco de dados. A proteção avançada contra ameaças para o banco de dados do Azure para MariaDB integra alertas com a [central de segurança do Azure](https://azure.microsoft.com/services/security-center/), que inclui detalhes de atividades suspeitas e recomenda ações sobre como investigar e atenuar a ameaça. A proteção avançada contra ameaças para o banco de dados do Azure para MariaDB simplifica a endereçamento de ameaças potenciais ao banco de dados sem a necessidade de ser um especialista em segurança ou gerenciar sistemas de monitoramento de segurança avançados. 

![Conceito da Proteção Avançada contra Ameaças](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Alertas da Proteção Avançada contra Ameaças 
A proteção avançada contra ameaças para o banco de dados do Azure para MariaDB detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados e pode disparar os seguintes alertas:
- **Acesso de um local incomum**: Esse alerta é disparado quando há uma alteração no padrão de acesso para o banco de dados do Azure para MariaDB Server, em que alguém fez logon no banco de dados do Azure para o servidor MariaDB de uma localização geográfica incomum. Em alguns casos, o alerta detecta uma ação legítima (um novo aplicativo ou manutenção do desenvolvedor). Em outros casos, o alerta detecta uma ação mal-intencionada (funcionário antigo, invasor externo).
- **Acesso de um data center do Azure incomum**: Esse alerta é disparado quando há uma alteração no padrão de acesso para o banco de dados do Azure para MariaDB Server, em que alguém fez logon no servidor de um data center do Azure incomum que foi visto nesse servidor durante o período recente. Em alguns casos, o alerta detecta uma ação legítima (seu novo aplicativo no Azure, Power BI). Em outros casos, o alerta detecta uma ação mal-intencionada de um recurso/serviço do Azure (funcionário antigo, invasor externo).
- **Acesso de uma entidade de segurança desconhecida**: Esse alerta é disparado quando há uma alteração no padrão de acesso para o banco de dados do Azure para MariaDB Server, em que alguém fez logon no servidor usando uma entidade de segurança incomum (banco de dados do Azure para usuário MariaDB). Em alguns casos, o alerta detecta uma ação legítima (novo aplicativo ou manutenção do desenvolvedor). Em outros casos, o alerta detecta uma ação mal-intencionada (funcionário antigo, invasor externo).
- **Acesso de um aplicativo potencialmente prejudicial**: esse alerta é disparado quando um aplicativo potencialmente prejudicial é usado para acessar o banco de dados. Em alguns casos, o alerta detecta um teste de segurança que está sendo executado. Em outros casos, o alerta detecta um ataque usando ferramentas comuns de ataque.
- **Banco de dados do Azure de força bruta para credenciais do MariaDB**: esse alerta é disparado quando há um número alto e anormal de logons com falha com credenciais diferentes. Em alguns casos, o alerta detecta um teste de segurança que está sendo executado. Em outros casos, o alerta detecta ataques de força bruta.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Para obter mais informações sobre preços, consulte a [página de preços do banco de dados do Azure para MariaDB](https://azure.microsoft.com/pricing/details/mariadb/) 
* Configurar o [banco de dados do Azure para proteção avançada contra ameaças do MariaDB](howto-database-threat-protection-portal.md) usando o portal do Azure  

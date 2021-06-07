---
title: Proteção avançada contra ameaças-banco de dados do Azure para MySQL
description: Aprenda conceitos sobre a proteção avançada contra ameaças, que detecta atividades anormais de banco de dados que indicam possíveis ameaças de segurança ao banco de dados.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: c9e884d153e85e7b68dee38494ac5d6f4271978a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94542568"
---
# <a name="azure-database-for-mysql-advanced-threat-protection"></a>Proteção Avançada contra Ameaças do Banco de Dados do Azure para MySQL

A Proteção Avançada contra Ameaças para o Banco de Dados do Azure para MySQL detecta atividades anômalas que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

> [!NOTE]
> A proteção avançada contra ameaças está em visualização pública.

A Proteção Avançada contra Ameaças faz parte da oferta de Segurança Avançada de Dados, que é um pacote unificado para funcionalidades avançadas de segurança. A proteção avançada contra ameaças pode ser acessada e gerenciada por meio do [portal do Azure](https://portal.azure.com) ou usando a [API REST](/rest/api/mysql). O recurso está disponível para servidores Uso Geral e com otimização de memória.

> [!NOTE]
> A funcionalidade de Proteção Avançada contra Ameaças **não** está disponível nestas regiões de nuvem soberana e do Azure Governamental: US Gov – Texas, US Gov – Arizona, US Gov Iowa, US Gov – Virgínia, Leste do US DoD, Região Central do US DoD, Região Central da Alemanha, Norte da Alemanha, Leste da China e Leste da China 2. Visite [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) para ver a disponibilidade geral do produto.


## <a name="what-is-advanced-threat-protection"></a>O que é a Proteção Avançada contra Ameaças?

A Proteção Avançada contra Ameaças para o Banco de Dados do Azure para MySQL fornece uma nova camada de segurança que possibilita aos clientes detectar e responder a possíveis ameaças conforme elas ocorrem, apresentando alertas de segurança sobre atividades anômalas. Os usuários receberão um alerta em caso de atividades suspeitas em bancos de dados, possíveis vulnerabilidades, bem como padrões anômalos de consultas e acesso a banco de dados. A Proteção Avançada contra Ameaças para o Banco de Dados do Azure para MySQL integra alertas à [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/), que incluem detalhes de atividades suspeitas e recomenda ações sobre como investigar e atenuar a ameaça. A Proteção Avançada contra Ameaças para o Banco de Dados do Azure para MySQL simplifica a abordagem contra possíveis ameaças no banco de dados, sem a necessidade de ser um especialista em segurança ou de gerenciar sistemas avançados de monitoramento de segurança. 

:::image type="content" source="media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png" alt-text="Conceito da Proteção Avançada contra Ameaças":::

## <a name="advanced-threat-protection-alerts"></a>Alertas da Proteção Avançada contra Ameaças 
A Proteção Avançada contra Ameaças para o Banco de Dados do Azure para MySQL detecta atividades anômalas que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados e pode disparar os seguintes alertas:
- **Acesso de um local incomum**: este alerta é disparado quando há uma alteração no padrão de acesso ao servidor do Banco de Dados do Azure para MySQL, em que alguém fez logon nele de um local geográfico incomum. Em alguns casos, o alerta detecta uma ação legítima (um novo aplicativo ou manutenção do desenvolvedor). Em outros casos, o alerta detecta uma ação mal-intencionada (funcionário antigo, invasor externo).
- **Acesso de um data center incomum do Azure**: este alerta é disparado quando há uma alteração no padrão de acesso ao servidor do Banco de Dados do Azure para MySQL, no local em que alguém fez logon no servidor de um data center incomum do Azure que foi visto neste servidor durante um período recente. Em alguns casos, o alerta detecta uma ação legítima (o novo aplicativo no Azure, no Power BI e no Editor de Consultas do Banco de Dados do Azure para MySQL). Em outros casos, o alerta detecta uma ação mal-intencionada de um recurso/serviço do Azure (funcionário antigo, invasor externo).
- **Acesso de uma entidade de segurança não familiar**: este alerta é disparado quando há uma alteração no padrão de acesso ao servidor do Banco de Dados do Azure para MySQL, em que alguém fez logon no servidor usando uma entidade de segurança incomum (usuário do Banco de Dados do Azure para MySQL). Em alguns casos, o alerta detecta uma ação legítima (novo aplicativo ou manutenção do desenvolvedor). Em outros casos, o alerta detecta uma ação mal-intencionada (funcionário antigo, invasor externo).
- **Acesso a partir de um aplicativo potencialmente prejudicial**: Este alerta é disparado quando um aplicativo potencialmente prejudicial é usado para acessar o banco de dados. Em alguns casos, o alerta detecta um teste de segurança que está sendo executado. Em outros casos, o alerta detecta um ataque usando ferramentas comuns de ataque.
- **Credenciais do Banco de Dados do Azure para MySQL de força bruta**: este alerta é disparado quando há um número alto anormal de logons com falha com credenciais diferentes. Em alguns casos, o alerta detecta um teste de segurança que está sendo executado. Em outros casos, o alerta detecta ataques de força bruta.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a [Central de Segurança do Azure](../security-center/security-center-introduction.md)
* Para saber mais sobre preço, confira a [página Preço do Banco de Dados do Azure para MySQL](https://azure.microsoft.com/pricing/details/mysql/) 
* Configurar a [Proteção Avançada contra Ameaças do Banco de Dados do Azure para MySQL](howto-database-threat-protection-portal.md) usando o portal do Azure
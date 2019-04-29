---
title: Central de Segurança do Azure e serviço do Banco de Dados SQL do Azure | Microsoft Docs
description: Este artigo mostra como a Central de Segurança pode ajudar você a proteger seus bancos de dados no Banco de Dados SQL do Azure.
services: sql-database
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f109adfd-daed-4257-9692-2042a1399480
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: rkarlin
ms.openlocfilehash: 0a889de79b6a5921007614dac8d610c1be0222d2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60704485"
---
# <a name="azure-security-center-and-azure-sql-database-service"></a>Central de Segurança do Azure e serviço do Banco de Dados SQL do Azure
A [Central de Segurança do Azure](https://azure.microsoft.com/documentation/services/security-center/) ajuda você a evitar, detectar e responder a ameaças. Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas do Azure, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança.

Este artigo mostra como a Central de Segurança pode ajudar você a proteger seus bancos de dados no Banco de Dados SQL do Azure.

## <a name="why-use-security-center"></a>Por que usar a Central de Segurança?
A Central de Segurança ajuda você a proteger os dados no Banco de Dados SQL, fornecendo visibilidade sobre a segurança de todos os seus servidores e bancos de dados. Com a Central de Segurança, você pode:

* Definir políticas de criptografia e auditoria do Banco de Dados SQL.
* Monitore a segurança dos recursos de Banco de Dados SQL em todas as suas assinaturas.
* Identifique e corrija rapidamente problemas de segurança.
* Integre os alertas da [detecção de ameaças do Banco de Dados SQL do Azure](../sql-database/sql-database-threat-detection.md).

Além de ajudar a proteger seus recursos de Banco de Dados SQL, a Central de Segurança também fornece monitoramento e gerenciamento de segurança para máquinas virtuais do Azure, Serviços de Nuvem, Serviços de Aplicativos, redes virtuais e muito mais. Saiba mais sobre a Central de Segurança [aqui](security-center-intro.md).

## <a name="prerequisites"></a>Pré-requisitos
Para começar a usar a Central de Segurança, você deve ter uma assinatura do Microsoft Azure. A camada Gratuita da Central de Segurança é habilitada com sua assinatura. Para saber mais sobre as camadas Gratuito e Standard da Central de Segurança, confira [Preços da Central de Segurança](https://azure.microsoft.com/pricing/details/security-center/).

A Central de segurança dá suporte ao acesso baseado em função. Para saber mais sobre o controle de acesso baseado em função (RBAC) no Azure, consulte o [Controle de acesso baseado em função do Azure Active Directory](../role-based-access-control/role-assignments-portal.md). As Perguntas frequentes da Central de Segurança fornecem informações sobre [como as permissões são tratadas na Central de Segurança](security-center-faq.md#permissions).

## <a name="access-security-center"></a>Acessar a Central de Segurança
Você acessar a Central de Segurança pelo [portal do Azure](https://azure.microsoft.com/features/azure-portal/). [Entre no Portal](https://portal.azure.com/) e selecione a **opção Central de Segurança**.

![Opção da Central de Segurança][1]

A folha **Central de Segurança** é aberta.
![Folha Central de Segurança][2]

## <a name="set-security-policy"></a>Definir políticas de segurança
Uma política de segurança define o conjunto de controles recomendados para os recursos na assinatura ou grupo de recursos especificado. Na Central de Segurança, você define as políticas para o grupo de recursos ou as assinaturas de acordo com as necessidades de segurança de sua empresa e os tipos de aplicativos ou a confidencialidade dos dados de cada assinatura.

Você pode definir uma política para mostrar as recomendações para a auditoria do SQL e TDE (criptografia de dados transparente) do SQL.

* Quando você ativa **Detecção de ameaças e auditoria SQL**, a Central de Segurança recomenda que a auditoria de acesso ao Banco de Dados do Azure esteja habilitada para fins de conformidade, detecção avançada e investigação.
* Quando você ativa a **Transparent Data Encryption do SQL**, a Central de Segurança recomenda que a criptografia em repouso seja habilitada para o Banco de Dados SQL do Azure, backups associados e arquivos do log de transação.

Para definir uma política de segurança, selecione o bloco **Política** na folha Central de Segurança. Na folha **Política de segurança**, selecione a assinatura na qual você deseja habilitar a política de segurança. Selecione **Política de prevenção** e **Ative** as recomendações de segurança que você deseja usar nesta assinatura.
![Política de segurança][3]

Para saber mais, confira [Definir políticas de segurança](tutorial-security-policy.md).

## <a name="manage-security-recommendation"></a>Gerenciar recomendações de segurança
A Central de Segurança analisa periodicamente o estado de segurança de seus recursos do Azure. Quando a Central de Segurança identifica possíveis vulnerabilidades de segurança, cria recomendações. As recomendações o orientam ao longo do processo de configuração dos controles necessários.

Depois de definir uma política de segurança, a Central de Segurança analisa o estado de segurança de seus recursos para identificar possíveis vulnerabilidades. As recomendações são mostradas em um formato de tabela, em que cada linha representa uma recomendação específica. Use a tabela a seguir como referência para ajudar você a entender as recomendações disponíveis para o Banco de Dados SQL do Azure e o que faz cada recomendação se você aplicá-la. A seleção de uma recomendação leva você até um artigo que explica como implementar a recomendação na Central de Segurança.

| Recomendações | DESCRIÇÃO |
| --- | --- |
| [Habilitar a detecção de ameaças e auditoria em servidores SQL](security-center-enable-auditing-on-sql-servers.md) |Recomenda que você ative a detecção de ameaças e a auditoria para servidores do Banco de Dados SQL. (Somente o serviço do Banco de Dados SQL. Não inclui o Microsoft SQL Server em execução nas máquinas virtuais.) |
| [Habilitar a detecção de ameaças e auditoria em Bancos de Dados SQL](security-center-enable-auditing-on-sql-databases.md) |Recomenda que você ative a detecção de ameaças e a auditoria para bancos de dados do Banco de Dados SQL. (Somente o serviço do Banco de Dados SQL. Não inclui o Microsoft SQL Server em execução nas máquinas virtuais.) |
| [Habilitar Transparent Data Encryption](security-center-enable-transparent-data-encryption.md) |Recomenda que você habilite a criptografia para bancos de dados SQL. (Somente o serviço do Banco de Dados SQL.) |

Para ver recomendações de seus recursos do Azure, selecione o bloco **Recomendações** na folha Central de Segurança. Na folha **Recomendações**, selecione uma recomendação para obter detalhes. Neste exemplo, vamos selecionar **Habilitar Auditoria e Detecção de ameaça em servidores SQL**.

![Recomendações][4]

Conforme exibido abaixo, a Central de Segurança mostra os servidores SQL nos quais a detecção de ameaças e a auditoria não estão habilitadas. Depois de ativar a auditoria, você poderá definir as configurações de Detecção de Ameaças e configurações de email para receber os alertas de segurança. A Detecção de Ameaças alerta você quando detecta atividades anormais do banco de dados que indicam possíveis ameaças de segurança ao banco de dados. Os alertas são exibidos no painel Central de Segurança.
![Auditoria e detecção de ameaças][5]

Execute as etapas em [Detecção de ameaças do Banco de Dados SQL no Portal do Azure](../sql-database/sql-database-threat-detection-portal.md) para ativar e configurar a Detecção de ameaças e configurar a lista de emails que receberá alertas de segurança após a detecção de atividades anormais.

Para saber mais sobre recomendações, consulte [Gerenciar recomendações de segurança](security-center-recommendations.md).

## <a name="monitor-security-health"></a>Monitorar integridade da segurança
Depois de habilitar as [políticas de segurança](tutorial-security-policy.md) para os recursos de uma assinatura, a Central de Segurança analisará a segurança de seus recursos para identificar as possíveis vulnerabilidades.  Você pode exibir o estado de segurança de seus recursos no bloco **Integridade da segurança do recurso**. Quando você clica em **Dados** no bloco **Integridade de segurança dos recursos**, a folha **Recursos de Dados** é aberta com recomendações SQL para problemas como a não habilitação da Transparent Data Encryption e da auditoria. Ela também contém recomendações para o estado de integridade geral do banco de dados.
![Integridade da segurança de recursos][6]

Para saber mais, confira [Monitoramento de integridade de segurança](security-center-monitoring.md).

## <a name="manage-and-respond-to-security-alerts"></a>Gerenciar e responder aos alertas de segurança
A Central de Segurança coleta, analisa e integra automaticamente os dados de registro da [Detecção de ameaças do Azure SQL](../sql-database/sql-database-threat-detection.md), além de outros recursos do Azure, para detectar ameaças reais e reduzir os falsos positivos. Uma lista priorizada de alertas de segurança é exibida na Central de Segurança, junto com as informações necessárias para investigar rapidamente o problema, e recomendações sobre como corrigir um ataque.

Para ver alertas, selecione o bloco **Alertas de segurança** na folha Central de Segurança. Na folha **Alertas de segurança**, selecione um alerta para saber mais sobre os evento que dispararam o alerta e quais etapas, se houver alguma, são necessárias para corrigir um ataque. Neste exemplo, vamos selecionar **Possível injeção de SQL**.
![Alertas de segurança][7]

Conforme exibido abaixo, a Central de Segurança fornece detalhes adicionais que oferecem informações sobre o que disparou o alerta, o recurso de destino e, quando aplicável, o endereço IP de origem e as recomendações sobre como corrigir.
![Possível injeção de SQL][8]

Para saber mais, confira [Gerenciar e responder a alertas de segurança](security-center-managing-and-responding-alerts.md).

## <a name="next-steps"></a>Próximas etapas
* [Perguntas frequentes da Central de Segurança](security-center-faq.md) - encontre as perguntas frequentes sobre como usar o serviço.
* [Guia de planejamento e operações da Central de Segurança](security-center-planning-and-operations-guide.md) - Siga um conjunto de etapas e tarefas para otimizar seu uso da Central de Segurança com base no modelo de gerenciamento de nuvem e nos requisitos de segurança de sua organização.
* [Segurança de dados da Central de Segurança](security-center-data-security.md) - Saiba como a Central de Segurança coleta e processa dados sobre os recursos do Azure, incluindo informações da configuração, metadados, logs de eventos, arquivos de despejo corrompidos e mais.
* [Manipulação de incidentes de segurança](security-center-incident.md) - Saiba como usar o recurso de alerta de segurança na Central de Segurança para ajudar a lidar com incidentes de segurança.

<!--Image references-->
[1]: ./media/security-center-sql-database/security-center.png
[2]: ./media/security-center-sql-database/security-center-blade.png
[3]: ./media/security-center-sql-database/security-policy.png
[4]: ./media/security-center-sql-database/recommendation.png
[5]: ./media/security-center-sql-database/turn-on-auditing.png
[6]: ./media/security-center-sql-database/monitor-health.png
[7]: ./media/security-center-sql-database/alert.png
[8]: ./media/security-center-sql-database/sql-injection.png

---
title: "Detecção de ameaças – Banco de Dados SQL do Azure | Microsoft Docs"
description: "A Detecção de Ameaças detecta as atividades anormais do banco de dados que indicam possíveis ameaças de segurança ao banco de dados."
services: sql-database
documentationcenter: 
author: rmatchoro
manager: jhubbard
editor: v-romcal
ms.assetid: b50d232a-4225-46ed-91e7-75288f55ee84
ms.service: sql-database
ms.custom: security
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 06/19/2017
ms.author: ronmat; ronitr
ms.openlocfilehash: bd3de9ed0131edc683763b0fe7f4a2ae74533944
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="sql-database-threat-detection"></a>Detecção de Ameaças do Banco de Dados SQL

A Detecção de Ameaças do SQL detecta atividades anômalas, indicando tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

## <a name="overview"></a>Visão geral

A Detecção de Ameaças do SQL fornece uma nova camada de segurança, que permite que os clientes detectem e respondam às ameaças potenciais conforme elas ocorrem, fornecendo alertas de segurança sobre atividades anômalas.  Os usuários receberão um alerta mediante atividades suspeitas em bancos de dados, possíveis vulnerabilidades e ataques de injeção de SQL, bem como padrões anômalos de acesso ao banco de dados. Os alertas da Detecção de Ameaças do SQL fornecem detalhes de atividades suspeitas e recomendam ação de como investigar e atenuar a ameaça. Os usuários podem explorar os eventos suspeitos usando a [Auditoria do Banco de Dados SQL](sql-database-auditing.md) para determinar se eles resultam de uma tentativa de acesso, violação ou exploração dos dados no banco de dados. A Detecção de Ameaças torna simples tratar as possíveis ameaças no banco de dados sem a necessidade de ser um especialista em segurança ou gerenciar os sistemas de monitoramento de segurança avançados.

Por exemplo, a injeção de SQL é um dos problemas comuns de segurança do aplicativo da Web na Internet, usada para atacar os aplicativos orientados a dados. Os invasores aproveitam as vulnerabilidades do aplicativo para inserir instruções SQL mal-intencionadas nos campos de entrada do aplicativo, violando ou modificando os dados no banco de dados.

A Detecção de Ameaças do SQL integra alertas à [Central de Segurança do Azure](https://azure.microsoft.com/en-us/services/security-center/) e, de cada servidor de Banco de Dados SQL protegido será cobrado o mesmo preço cobrado pela camada Standard da Central de Segurança do Azure, que é US$ 15 por nó ao mês, em que cada servidor de banco de dados SQL protegido será contado como um nó. Convidamos você a testar o recurso por 60 dias gratuitamente. 

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Configurar a detecção de ameaças para seu banco de dados no Portal do Azure
1. Inicie o Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Navegue até a folha de configuração do Banco de Dados SQL que você deseja monitorar. Na folha Configurações, selecione **Auditoria e Detecção de Ameaças**. 
    ![Painel de navegação][1]
3. Na folha de configuração **Auditoria e Detecção de Ameaças**, **ATIVE** a Auditoria, o que exibirá as configurações de detecção de ameaças.
  
    ![Painel de navegação][2]
4. **ATIVE** a Detecção de ameaças.
5. Configure a lista de emails que receberão os alertas de segurança após a detecção das atividades anormais do banco de dados.
6. Clique em **Salvar** na folha **Auditoria e Detecção de ameaças** para salvar a auditoria nova ou atualizada, bem como as configurações de detecção de ameaças.
       
    ![Painel de navegação][3]

## <a name="set-up-threat-detection-using-powershell"></a>Configurar a detecção de ameaças usando o PowerShell

Para obter um exemplo de script, confira [Configurar a auditoria e a detecção de ameaças usando o PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Explore as atividades anormais do banco de dados na detecção de um evento suspeito
1. Você receberá uma notificação por email na detecção das atividades anormais do banco de dados. <br/>
   O email fornecerá informações sobre o evento de segurança suspeito, incluindo a natureza das atividades anômalas, o nome do banco de dados, o nome do servidor, o nome do aplicativo e a hora do evento. Além disso, o email fornecerá informações sobre as possíveis causas e as ações recomendadas para investigar e atenuar a ameaça em potencial no banco de dados.<br/>
     
    ![Painel de navegação][4]
2. O alerta por email inclui um link direto para o log de Auditoria do SQL. Clicar nesse link inicia o Portal do Azure e abre os registros de Auditoria do SQL próximos ao horário do evento suspeito. Clique em um registro de auditoria para exibir mais detalhes sobre as atividades suspeitas no banco de dados, o que facilita encontrar as instruções SQL que foram executadas (quem acessou, o que foi feito e quando) e determinar se o evento foi legítimo ou mal-intencionado (por exemplo, a vulnerabilidade do aplicativo para injeção de SQL foi explorada, alguém violou dados confidenciais, etc.).<br/>
   ![Painel de navegação][5]


## <a name="explore-threat-detection-alerts-for-your-database-in-the-azure-portal"></a>Explorar os alertas de detecção de ameaças para seu banco de dados no Portal do Azure

A Detecção de Ameaças do Banco de Dados SQL integra seus alertas à [Central de Segurança do Azure](https://azure.microsoft.com/en-us/services/security-center/). Um bloco de segurança SQL dinâmico na folha de banco de dados no Portal do Azure rastreia o status das ameaças ativas. 

   ![Painel de navegação][6]
   
1. Clicar no bloco de segurança SQL inicia a folha de alertas da Central de Segurança do Azure e fornece uma visão geral das ameaças SQL ativas detectadas no banco de dados. 

  ![Painel de navegação][7]

2. Clicar em um alerta específico fornece detalhes adicionais e ações para investigar essa ameaça e corrigir futuras ameaças.

  ![Painel de navegação][8]


## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a Detecção de Ameaças, visite o [blog do Azure](https://azure.microsoft.com/en-us/blog/azure-sql-database-threat-detection-general-availability-in-spring-2017/) 
* Saiba mais sobre a [Auditoria do Banco de Dados SQL do Azure](sql-database-auditing.md)
* Saiba mais sobre a [Central de Segurança do Azure](https://docs.microsoft.com/en-us/azure/security-center/security-center-intro)
* Para obter mais detalhes sobre preços, visite a [página de Preços do Banco de Dados SQL](https://azure.microsoft.com/en-us/pricing/details/sql-database/)  
* Para obter um exemplo de script do PowerShell, confira [Configurar a auditoria e a detecção de ameaças usando o PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md)



<!--Image references-->
[1]: ./media/sql-database-threat-detection/1_td_click_on_settings.png
[2]: ./media/sql-database-threat-detection/2_td_turn_on_auditing.png
[3]: ./media/sql-database-threat-detection/3_td_turn_on_threat_detection.png
[4]: ./media/sql-database-threat-detection/4_td_email.png
[5]: ./media/sql-database-threat-detection/5_td_audit_record_details.png
[6]: ./media/sql-database-threat-detection/6_td_security_tile_view_alerts.png
[7]: ./media/sql-database-threat-detection/7_td_SQL_security_alerts_list.png
[8]: ./media/sql-database-threat-detection/8_td_SQL_security_alert_details.png



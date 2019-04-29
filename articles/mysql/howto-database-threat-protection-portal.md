---
title: Proteção Avançada contra Ameaças – Banco de Dados do Azure para MySQL | Microsoft Docs
description: A Proteção contra Ameaças detecta as atividades anormais do banco de dados que indicam possíveis ameaças de segurança ao banco de dados.
author: bolzmj
ms.author: mbolz
ms.service: mysql
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: 76f6c15fc1e186e254c4edbb53a2a0ccf7050b3e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61458890"
---
# <a name="advanced-threat-protection-for-azure-database-for-mysql"></a>Proteção Avançada contra Ameaças para o Banco de Dados do Azure para MySQL

A Proteção Avançada contra Ameaças para o Banco de Dados do Azure para MySQL detecta atividades anômalas que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

A Proteção Avançada contra Ameaças faz parte da oferta de Segurança Avançada de Dados, que é um pacote unificado para funcionalidades avançadas de segurança. A Proteção Avançada contra Ameaças pode ser acessada e gerenciada por meio do [Portal do Azure](https://portal.azure.com) e, atualmente, está em versão prévia.

> [!NOTE]
> O recurso de Proteção Avançada contra Ameaças **não** está disponível nas seguintes regiões de nuvem soberana e do Azure Governamental: US Gov - Texas, US Gov - Arizona, US Gov Iowa, EUA, US Gov - Virgínia, US DoD Leste, US DoD Central, Alemanha Central, Norte da Alemanha, Leste da China, Leste da China 2. Visite [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) para ver a disponibilidade geral do produto.
>

> [!NOTE]
> Esse recurso está disponível em todas as regiões do Azure nas quais o Banco de Dados do Azure para MySQL é implantado para servidores de Uso Geral e Otimizado por Memória.

## <a name="set-up-threat-detection"></a>Configurar detecção de ameaças
1. Inicie o portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Navegue até a página de configuração do servidor do Banco de Dados do Azure para MySQL que deseja proteger. Nas configurações de segurança, selecione **Proteção Avançada contra Ameaças (versão prévia)**.
3. Na página de configuração de **Proteção Avançada contra Ameaças (versão prévia)**:

   - Habilite a Proteção Avançada contra Ameaças no servidor.
   - Em **Configurações de Proteção Avançada contra Ameaças**, na caixa de texto **Enviar alertas para**, forneça a lista de endereços de email para receber alertas de segurança em caso de detecção de atividades anômalas em banco de dados.
  
   ![Configurar detecção de ameaças](./media/howto-database-threat-protection-portal/set-up-threat-protection.png)

## <a name="explore-anomalous-database-activities"></a>Explorar atividades anômalas em banco de dados

Você receberá uma notificação por email na detecção das atividades anormais do banco de dados. O email fornecerá informações sobre o evento de segurança suspeito, incluindo a natureza das atividades anômalas, o nome do banco de dados, o nome do servidor, o nome do aplicativo e a hora do evento. Além disso, o email fornecerá informações sobre as possíveis causas e as ações recomendadas para investigar e atenuar a ameaça em potencial no banco de dados.
 
1. Clique no link **Exibir alertas recentes** no email para iniciar o portal do Azure e mostrar a página de alertas da Central de Segurança do Azure, que fornece uma visão geral das ameaças ativas detectadas no Banco de Dados SQL.
    
    ![Relatórios de atividades anômalas](./media/howto-database-threat-protection-portal/anomalous-activity-report.png)

    Exibir ameaças ativas:

    ![Ameaças ativas](./media/howto-database-threat-protection-portal/active-threats.png)

2. Clique em um alerta específico para obter os detalhes e as ações adicionais para investigar essa ameaça e corrigir ameaças futuras.
    
    ![Alerta específico](./media/howto-database-threat-protection-portal/specific-alert.png)

## <a name="explore-threat-detection-alerts"></a>Explorar os alertas de detecção de ameaças

A Detecção de Ameaças do Banco de Dados SQL integra seus alertas à [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/). Os blocos dinâmicos de detecção de ameaças SQL dentro do banco de dados e das folhas ATP SQL no portal do Azure acompanham o status de ameaças ativas.

Clique em **Alerta de detecção de ameaças** para iniciar a página de alertas da Central de Segurança do Azure e ter uma visão geral das ameaças SQL ativas detectadas no banco de dados.

   ![Alerta de detecção de ameaças](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)
   

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Para saber mais sobre preço, confira a [página Preço do Banco de Dados do Azure para MySQL](https://azure.microsoft.com/pricing/details/mysql/)  

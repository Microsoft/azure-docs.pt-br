---
title: Proteção avançada contra ameaças-portal do Azure-banco de dados do Azure para MariaDB
description: A proteção contra ameaças do banco de dados do Azure para MariaDB detecta atividades anormais de banco de dados que indicam possíveis ameaças de segurança ao banco de dados.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 7734feddabb1a4a86e7932da3ef4adc57352637e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98665099"
---
# <a name="advanced-threat-protection-for-azure-database-for-mariadb"></a>Proteção avançada contra ameaças para o banco de dados do Azure para MariaDB

A proteção avançada contra ameaças para o banco de dados do Azure para MariaDB detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

A Proteção Avançada contra Ameaças faz parte da oferta de Segurança Avançada de Dados, que é um pacote unificado para funcionalidades avançadas de segurança. A proteção avançada contra ameaças pode ser acessada e gerenciada por meio do [portal do Azure](https://portal.azure.com).

> [!IMPORTANT]
> A proteção avançada contra ameaças está em visualização pública. Esse recurso está disponível em todas as regiões do Azure nas quais o Banco de Dados do Azure para MariaDB é implantado para servidores de Uso Geral e Otimizado por Memória.

> [!NOTE]
> A funcionalidade de Proteção Avançada contra Ameaças **não** está disponível nestas regiões de nuvem soberana e do Azure Governamental: US Gov – Texas, US Gov – Arizona, US Gov Iowa, US Gov – Virgínia, Leste do US DoD, Região Central do US DoD, Região Central da Alemanha, Norte da Alemanha, Leste da China e Leste da China 2. Visite [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) para ver a disponibilidade geral do produto.

## <a name="set-up-threat-detection"></a>Configurar detecção de ameaças
1. Inicie o portal do Azure em [https://portal.azure.com](https://portal.azure.com) .
2. Navegue até a página de configuração do banco de dados do Azure para o servidor MariaDB que você deseja proteger. Nas configurações de segurança, selecione **Proteção Avançada contra Ameaças (versão prévia)**.
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

* Saiba mais sobre a [Central de Segurança do Azure](../security-center/security-center-introduction.md)
* Para obter mais informações sobre preços, consulte a [página de preços do banco de dados do Azure para MariaDB](https://azure.microsoft.com/pricing/details/mariadb/)
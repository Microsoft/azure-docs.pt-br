---
title: Proteção avançada contra ameaças-portal do Azure-banco de dados do Azure para MySQL
description: Saiba como configurar a proteção avançada contra ameaças para detectar atividades anormais de banco de dados que indicam possíveis ameaças de segurança ao banco de dados.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: b30bd36dca6f866b8f3e6e8a0b133a6dd61b239b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012656"
---
# <a name="advanced-threat-protection-for-azure-database-for-mysql"></a>Proteção Avançada contra Ameaças para o Banco de Dados do Azure para MySQL

A Proteção Avançada contra Ameaças para o Banco de Dados do Azure para MySQL detecta atividades anômalas que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

A Proteção Avançada contra Ameaças faz parte da oferta de Segurança Avançada de Dados, que é um pacote unificado para funcionalidades avançadas de segurança. A Proteção Avançada contra Ameaças pode ser acessada e gerenciada por meio do [Portal do Azure](https://portal.azure.com) e, atualmente, está em versão prévia.

> [!NOTE]
> A funcionalidade de Proteção Avançada contra Ameaças **não** está disponível nestas regiões de nuvem soberana e do Azure Governamental: US Gov – Texas, US Gov – Arizona, US Gov Iowa, US Gov – Virgínia, Leste do US DoD, Região Central do US DoD, Região Central da Alemanha, Norte da Alemanha, Leste da China e Leste da China 2. Visite [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) para ver a disponibilidade geral do produto.
>

> [!NOTE]
> Esse recurso está disponível em todas as regiões do Azure nas quais o Banco de Dados do Azure para MySQL é implantado para servidores de Uso Geral e Otimizado por Memória.

## <a name="set-up-threat-detection"></a>Configurar detecção de ameaças
1. Inicie o portal do Azure em [https://portal.azure.com](https://portal.azure.com) .
2. Navegue até a página de configuração do servidor do Banco de Dados do Azure para MySQL que deseja proteger. Nas configurações de segurança, selecione **Proteção Avançada contra Ameaças (versão prévia)**.
3. Na página de configuração de **Proteção Avançada contra Ameaças (versão prévia)**:

   - Habilite a Proteção Avançada contra Ameaças no servidor.
   - Em **Configurações de Proteção Avançada contra Ameaças**, na caixa de texto **Enviar alertas para**, forneça a lista de endereços de email para receber alertas de segurança em caso de detecção de atividades anômalas em banco de dados.
  
   :::image type="content" source="./media/howto-database-threat-protection-portal/set-up-threat-protection.png" alt-text="Configurar detecção de ameaças":::

## <a name="explore-anomalous-database-activities"></a>Explorar atividades anômalas em banco de dados

Você receberá uma notificação por email na detecção das atividades anormais do banco de dados. O email fornecerá informações sobre o evento de segurança suspeito, incluindo a natureza das atividades anômalas, o nome do banco de dados, o nome do servidor, o nome do aplicativo e a hora do evento. Além disso, o email fornecerá informações sobre as possíveis causas e as ações recomendadas para investigar e atenuar a ameaça em potencial no banco de dados.
 
1. Clique no link **Exibir alertas recentes** no email para iniciar o portal do Azure e mostrar a página de alertas da Central de Segurança do Azure, que fornece uma visão geral das ameaças ativas detectadas no Banco de Dados SQL.
    
    :::image type="content" source="./media/howto-database-threat-protection-portal/anomalous-activity-report.png" alt-text="Relatórios de atividades anômalas":::

    Exibir ameaças ativas:

    :::image type="content" source="./media/howto-database-threat-protection-portal/active-threats.png" alt-text="Ameaças ativas":::

2. Clique em um alerta específico para obter os detalhes e as ações adicionais para investigar essa ameaça e corrigir ameaças futuras.
    
    :::image type="content" source="./media/howto-database-threat-protection-portal/specific-alert.png" alt-text="Alerta específico":::

## <a name="explore-threat-detection-alerts"></a>Explorar os alertas de detecção de ameaças

A Detecção de Ameaças do Banco de Dados SQL integra seus alertas à [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/). Um bloco de detecção de ameaças ao vivo do SQL controla o status de ameaças ativas no banco de dados e nas páginas do SQL ATP no portal do Azure.

Clique em **Alerta de detecção de ameaças** para iniciar a página de alertas da Central de Segurança do Azure e ter uma visão geral das ameaças SQL ativas detectadas no banco de dados.

   :::image type="content" source="./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png" alt-text="Alerta de detecção de ameaças":::
   

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a [Central de Segurança do Azure](../security-center/security-center-introduction.md)
* Para saber mais sobre preço, confira a [página Preço do Banco de Dados do Azure para MySQL](https://azure.microsoft.com/pricing/details/mysql/)
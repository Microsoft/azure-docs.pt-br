---
title: Usar a proteção avançada contra ameaças-banco de dados do Azure para PostgreSQL-servidor único
description: A Proteção contra Ameaças detecta as atividades anormais do banco de dados que indicam possíveis ameaças de segurança ao banco de dados.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 5583e8423f0909936d9e55c6d87593835eded8f7
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489890"
---
# <a name="advanced-threat-protection-for-azure-database-for-postgresql---single-server"></a>Proteção avançada contra ameaças para o banco de dados do Azure para PostgreSQL-servidor único

A Proteção Avançada contra Ameaças para o Banco de Dados do Azure para PostgreSQL detecta atividades anômalas, indicando tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

A Proteção Avançada contra Ameaças faz parte da oferta de Segurança Avançada de Dados, que é um pacote unificado para funcionalidades avançadas de segurança. A Proteção Avançada contra Ameaças pode ser acessada e gerenciada por meio do [Portal do Azure](https://portal.azure.com) e, atualmente, está em versão prévia.

> [!NOTE]
> A funcionalidade de Proteção Avançada contra Ameaças **não** está disponível nestas regiões de nuvem soberana e do Azure Governamental: US Gov – Texas, US Gov – Arizona, US Gov Iowa, US Gov – Virgínia, Leste do US DoD, Região Central do US DoD, Região Central da Alemanha, Norte da Alemanha, Leste da China e Leste da China 2. Visite [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) para ver a disponibilidade geral do produto.
>

> [!NOTE]
> Esse recurso está disponível em todas as regiões do Azure nas quais o Banco de Dados do Azure para PostgreSQL é implantado para servidores de Uso Geral e Otimizado para Memória.

## <a name="set-up-threat-detection"></a>Configurar detecção de ameaças
1. Inicie o portal do Azure em [https://portal.azure.com](https://portal.azure.com) .
2. Navegue até a página de configuração do servidor do Banco de Dados do Azure para PostgreSQL que deseja proteger. Nas configurações de segurança, selecione **Proteção Avançada contra Ameaças (versão prévia)**.
3. Na página de configuração de **Proteção Avançada contra Ameaças (versão prévia)**:

   - Habilite a Proteção Avançada contra Ameaças no servidor.
   - Em **Configurações de Proteção Avançada contra Ameaças**, na caixa de texto **Enviar alertas para**, forneça a lista de endereços de email para receber alertas de segurança em caso de detecção de atividades anômalas em banco de dados.
  
   :::image type="content" source="./media/howto-database-threat-protection-portal/set-up-threat-protection.png" alt-text="Configurar detecção de ameaças":::

## <a name="explore-anomalous-database-activities"></a>Explorar atividades anômalas em banco de dados

Você receberá uma notificação por email na detecção das atividades anormais do banco de dados. O email fornecerá informações sobre o evento de segurança suspeito, incluindo a natureza das atividades anômalas, o nome do banco de dados, o nome do servidor, o nome do aplicativo e a hora do evento. Além disso, o email fornecerá informações sobre as possíveis causas e as ações recomendadas para investigar e atenuar a ameaça em potencial no banco de dados.
    
1. Clique no link **Exibir alertas recentes** no email para iniciar o portal do Azure e mostrar a página de alertas da Central de Segurança do Azure, que fornece uma visão geral das ameaças ativas detectadas no Banco de Dados SQL.
    
    :::image type="content" source="./media/howto-database-threat-protection-portal/anomalous-activity-report.png" alt-text="Configurar detecção de ameaças":::

    Exibir ameaças ativas:

    :::image type="content" source="./media/howto-database-threat-protection-portal/active-threats.png" alt-text="Configurar detecção de ameaças":::

2. Clique em um alerta específico para obter os detalhes e as ações adicionais para investigar essa ameaça e corrigir ameaças futuras.
    
    :::image type="content" source="./media/howto-database-threat-protection-portal/specific-alert.png" alt-text="Configurar detecção de ameaças":::

## <a name="explore-threat-detection-alerts"></a>Explorar os alertas de detecção de ameaças

A Proteção Avançada Contra Ameaças integra seus alertas à [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/). 

Clique em **Alertas de segurança** em **PROTEÇÃO CONTRA AMEAÇAS** para iniciar a página de alertas da Central de Segurança do Azure e ter uma visão geral das ameaças SQL ativas detectadas no banco de dados.

  :::image type="content" source="./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png" alt-text="Configurar detecção de ameaças":::

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a [Central de Segurança do Azure](../security-center/security-center-introduction.md)
* Para saber mais sobre preço, consulte a página [Preço do Banco de Dados do Azure para PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/)
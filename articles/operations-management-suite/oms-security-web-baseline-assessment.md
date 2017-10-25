---
title: "Avaliação de linha de base da Web na linha de base da solução de Segurança e Auditoria do Operations Management Suite | Microsoft Docs"
description: "Este documento explica como usar a avaliação de linha de base da Web na solução de Segurança e Auditoria do OMS para realizar uma avaliação de linha de base de todos os servidores Web para fins de conformidade e segurança."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 17837c8b-3e79-47c0-9b83-a51c6ca44ca6
ms.service: operations-management-suite
ms.custom: oms-security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: yurid
ms.openlocfilehash: 40b0c6ca933ea02ac9f5fe3bfaaf87a310542a8d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="web-baseline-assessment-in-operations-management-suite-security-and-audit-solution"></a>Avaliação de linha de base de Web na Solução de Segurança e Auditoria do Operations Management Suite
Este documento ajuda você a usar os recursos de avaliação de linha de base da Web da Segurança e Auditoria do OMS para acessar o estado de segurança de seus recursos monitorados.

## <a name="what-is-web-baseline-assessment"></a>O que é a avaliação de linha de base da Web?
Atualmente a Segurança do OMS oferece uma avaliação de linha de base para os sistemas operacionais. Ela examina a cada 24 horas as configurações do sistema operacional de seus servidores e mostra as configurações possivelmente vulneráveis. Leia [Avaliação de Linha de Base na Solução de Segurança e Auditoria do Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/oms-security-baseline) para saber mais informações sobre esta opção.

O objetivo da avaliação de linha de base da Web é localizar as possíveis configurações de servidor Web vulneráveis. As três fontes principais para as configurações de linha de base da Web são: configuração do .NET, ASP.NET e IIS.  Assim como a avaliação de linha de base do sistema operacional, a Segurança do OMS vai verificar os seus servidores Web a cada 24 horas e fornecer uma exibição do estado de segurança deles.  No Serviço de Informações da Internet (IIS), as configurações são altamente personalizáveis, o que permite que vários níveis de aplicativo e site sejam substituídos. O scanner verifica as configurações em cada nível de site/aplicativo além do nível de raiz padrão. Isso ajuda você a identificar configurações possivelmente vulneráveis e a corrigi-las rapidamente, e também fornece nossas recomendações para essas configurações.

>[!NOTE] 
>Você pode baixar os Identificadores de Configuração Comum e Regras de Linha de Base usados pela Segurança OMS nesta [página](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335?redir=0).


## <a name="web-security-baseline-assessment"></a>Avaliação de linha de base de segurança da Web

Para esta versão prévia, o recurso pode ser acessado pela opção Pesquisa do OMS, e pelo Painel de Segurança e Auditoria do OMS. Siga as etapas abaixo para executar a consulta adequada:

1. No painel principal **Microsoft Operations Management Suite**, clique no bloco **Segurança e Auditoria**.
2. No painel **Segurança e Auditoria**, veja a perspectiva de Linha de Base da Web ao lado da perspectiva de linha de base do sistema operacional.
   
    ![Avaliação de linha de base de segurança na Web da Segurança e Auditoria do OMS](./media/oms-security-web-baseline/oms-security-web-baseline-fig5.png)

3. O painel esquerdo mostra o número de servidores Web em comparação com a linha de base, a porcentagem média de regras aprovadas em todos os servidores avaliados e a lista de servidores que foram avaliados.
4. O painel direito lista as regras exclusivas que falharam separadas por *Gravidade* e *Tipo de Regra*. Clique em qualquer uma das regras no painel direito para ver os detalhes da regra. Veja um exemplo na imagem abaixo. A regra avaliada está listada em *Configuração da Regra*. O campo *AzId*, que é um identificador exclusivo para cada regra, é usado pela Microsoft para acompanhar as regras de linha de base. Além disso, os usuários podem ver o *Resultado Esperado* (valor de recomendado da Microsoft) e outros detalhes sobre o impacto de segurança da regra.
    
    ![Consultar](./media/oms-security-web-baseline/oms-security-web-baseline-fig6.png)

5. Você pode criar suas próprias consultas para examinar os resultados. 

A primeira consulta que você pode usar é o **Resumo da avaliação de linha de base da Web**. No campo **Iniciar pesquisa aqui**, digite esta consulta: *Type=SecurityBaselineSummary BaselineType=Web*. Veja a seguir um exemplo de saída:

![Resultado da consulta](./media/oms-security-web-baseline/oms-security-web-baseline-fig7.png)

>[!NOTE] 
>Nesta consulta, cada registro indica o resumo da avaliação em um único servidor.

Quando você estiver na **Pesquisa de Log**, você pode digitar diferentes consultas para obter mais informações sobre a avaliação de linha de base da web. Além da consulta anterior, você também pode usar as seguintes nesta versão prévia:

**Avaliação da regra de linha de base de Web**: Cada registro representa uma avaliação da regra de linha de base única Web em um único servidor. Ela inclui todos os dados de uma regra com falha, o *SitePath* no qual a regra foi avaliada, o *Resultado Esperado* e o *Resultado Real*.

Consulta: *Type=SecurityBaseline BaselineType=Web AnalyzeResult=Failed*

![Resultado da consulta 2](./media/oms-security-web-baseline/oms-security-web-baseline-fig8.png)

**Mostra todos os resultados para um servidor específico**: essa consulta mostra como ver os resultados de um servidor específico: Consulta: *Type=SecurityBaseline BaselineType=Web Computer=BaselineTestVM1*

![Resultado da consulta 3](./media/oms-security-web-baseline/oms-security-web-baseline-fig3.png)

Você pode usar esses registros/consultas para criar seus próprios painéis, relatórios ou alertas. Confira um controle de interface do usuário de exemplo que você pode adicionar ao seu painel. Você pode aprender como visualizar os seus dados usando o Designer de Exibição do OMS [aqui](https://blogs.technet.microsoft.com/msoms/2016/06/30/oms-view-designer-visualize-your-data-your-way/). A tela a seguir é um exemplo de como o bloco parecerá após fazer essa personalização.

![painel Transações da Web](./media/oms-security-web-baseline/oms-security-web-baseline-fig4.png)

## <a name="see-also"></a>Consulte também
Neste documento, você aprendeu sobre a avaliação de linha de base de Web da Segurança e Auditoria do OMS. Para saber mais sobre a Segurança do OMS, veja os seguintes artigos:

* [Operations Management Suite (OMS) overview](operations-management-suite-overview.md)
* [Monitorando e respondendo a alertas de segurança na solução de Segurança e Auditoria do Operations Management Suite](oms-security-responding-alerts.md)
* [Monitorando recursos na solução de Segurança e Auditoria do Operations Management Suite](oms-security-monitoring-resources.md)


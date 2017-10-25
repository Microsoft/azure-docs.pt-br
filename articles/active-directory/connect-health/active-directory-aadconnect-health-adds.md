---
title: Usando o Azure AD Connect Health com o AD DS | Microsoft Docs
description: "Esta é a página Azure AD Connect Health que discutirá como monitorar o AD DS."
services: active-directory
documentationcenter: 
author: arluca
manager: femila
editor: curtand
ms.assetid: 19e3cf15-f150-46a3-a10c-2990702cd700
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 9e5b45d71b978c383932409f0037a4f6f32d0cb3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="using-azure-ad-connect-health-with-ad-ds"></a>Usar o Azure AD Connect Health com o AD DS
A documentação a seguir é específica para o monitoramento dos Serviços de Domínio do Active Directory com o Azure AD Connect Health. As versões com suporte do AD DS são: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 e Windows Server 2016.

Para saber mais sobre como monitorar o AD FS com o Azure AD Connect Health, veja [Usando o Azure AD Connect Health com o AD FS](active-directory-aadconnect-health-adfs.md). Além disso, para obter informações sobre como monitorar o Azure AD Connect (Sincronização) com o Azure AD Connect Health, confira [Usar o Azure AD Connect Health para Sincronização](active-directory-aadconnect-health-sync.md).

![Azure AD Connect Health para AD DS](./media/active-directory-aadconnect-health/aadconnect-health-adds-entry.png)

## <a name="alerts-for-azure-ad-connect-health-for-ad-ds"></a>Alertas do Azure AD Connect Health para AD DS
A seção de alertas no Azure AD Connect Health para o AD DS fornece uma lista de alertas ativos e resolvidos, relacionados aos Controladores de Domínio. A seleção de um alerta ativo ou resolvido abre uma nova folha com informações adicionais, juntamente com as etapas de resolução e os links para a documentação de suporte. Cada tipo de alerta pode ter uma ou mais instâncias, que correspondem a cada um dos controladores de domínio afetados por esse alerta específico. Na parte inferior da folha de alerta, você pode clicar duas vezes em um controlador de domínio afetado para abrir uma folha adicional com mais detalhes sobre essa instância de alerta.

Nessa folha, você pode habilitar as notificações por email para alertas e alterar o intervalo de tempo no modo de exibição. A expansão do intervalo de tempo permite que você veja alertas resolvidos anteriormente.

![Erro de sincronização do Azure AD Connect](./media/active-directory-aadconnect-health/aadconnect-health-adds-alerts.png)

## <a name="domain-controllers-dashboard"></a>Painel Controladores de Domínio
Esse painel fornece uma exibição topológica de seu ambiente, juntamente com as principais métricas operacionais e o status de integridade de cada um dos controladores de domínio monitorados. As métricas apresentadas ajudam a identificar rapidamente os controladores de domínio que podem exigir mais investigação. Por padrão, somente um subconjunto das colunas é exibido. No entanto, você poderá encontrar todo o conjunto de colunas disponíveis ao clicar duas vezes no comando de colunas. A seleção das colunas mais importantes torna esse painel um local único e simples para exibir a integridade de seu ambiente do AD DS.

![Controladores de Domínio](./media/active-directory-aadconnect-health/aadconnect-health-adds-domainsandsites-dashboard.png)

Os controladores de domínio podem ser agrupados por seu respectivo domínio ou site, o que é útil para entender a topologia do ambiente. Por fim, se você clicar duas vezes no título da folha, o painel será maximizado para utilizar o espaço disponível na tela. Este modo de exibição maior será útil quando várias colunas forem exibidas.

## <a name="replication-status-dashboard"></a>Painel Status de Replicação
Esse painel fornece uma exibição do status de replicação e da topologia de replicação dos controladores de domínio monitorados. O status da tentativa de replicação mais recente é listado juntamente com a documentação útil para qualquer erro encontrado. Você pode clicar duas vezes em um controlador de domínio com um erro para abrir uma nova folha com estas informações: os detalhes sobre o erro, as etapas recomendadas para a resolução e os links para documentação de solução de problemas.

![Status de replicação](./media/active-directory-aadconnect-health/aadconnect-health-adds-replication.png)

## <a name="monitoring"></a>Monitoramento
Esse recurso fornece tendências gráficas de diferentes contadores de desempenho, que são continuamente coletados de cada um dos controladores de domínio monitorados. O desempenho de um controlador de domínio pode ser comparado facilmente em todos os outros controladores de domínio monitorados na floresta. Além disso, você pode ver vários contadores de desempenho lado a lado, o que é útil ao solucionar problemas no ambiente.

![Monitoramento](./media/active-directory-aadconnect-health/aadconnect-health-adds-monitoring.png)

Por padrão, selecionamos previamente quatro contadores de desempenho. No entanto, você pode incluir outros clicando no comando de filtro e marcando ou desmarcando todos os contadores de desempenho desejados. Além disso, você pode clicar duas vezes em um gráfico do contador de desempenho para abrir uma nova folha, que inclui pontos de dados para cada um dos controladores de domínio monitorados.

## <a name="related-links"></a>Links relacionados
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Instalação do Agente do Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Operações de Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Usando o Azure AD Connect Health com o AD FS](active-directory-aadconnect-health-adfs.md)
* [Usando o Azure AD Connect Health para sincronização](active-directory-aadconnect-health-sync.md)
* [Perguntas frequentes do Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Histórico de versão do Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)


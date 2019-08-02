---
title: Recursos de desativação da Central de segurança (julho de 2019) | Microsoft Docs
description: Este artigo descreve os recursos na Central de segurança será desativado em 31 de julho de 2019.
services: security-center
author: yoavfrancis
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.date: 4/16/2019
ms.author: yoafr
ms.openlocfilehash: 1d364da9506124a35c724209c68ff72db4243e80
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341567"
---
# <a name="retirement-of-security-center-features-july-2019"></a>Desativação de recursos da Central de segurança (julho de 2019)

Fizemos vários [melhorias](https://azure.microsoft.com/updates/?product=security-center) à Central de segurança do Azure nos últimos seis meses.
Com esses recursos aprimorados, podemos estiver removendo alguns recursos redundantes e APIs relacionadas na Central de segurança em 31 de julho de 2019.  

A maioria desses recursos obsoletos pode ser substituída com a nova funcionalidade na Central de segurança do Azure ou Log Analytics do Azure. Outros recursos podem ser implementados usando [do Azure (visualização) de sentinela](https://azure.microsoft.com/services/azure-sentinel/).

Recursos da Central de segurança serão desativados incluem:

- [Painel de eventos](#menu_events)
- [Entrada de menu de pesquisa](#menu_search)
- [Modo de exibição clássico identidade e acesso link na identidade e acesso (preview)](#menu_classicidentity)
- [Eventos de segurança mapeiam botão no mapa de alertas de segurança (visualização)](#menu_securityeventsmap)
- [Regras de alerta personalizadas (versão prévia)](#menu_customalerts)
- [Investigar um botão em alertas de segurança de proteção contra ameaças](#menu_investigate)
- [Um subconjunto de soluções de segurança](#menu_solutions)
- [Editar configurações de segurança para políticas de segurança](#menu_securityconfigurations)
- [Painel segurança e auditoria (usado originalmente no portal do OMS) para espaços de trabalho do Log Analytics](#menu_securityomsdashboard)

Este artigo fornece informações detalhadas para cada recurso desativado e as etapas que você pode adotar para implementar recursos de substituição.

## Painel de eventos<a name="menu_events"></a>

A Central de segurança usa o Microsoft Monitoring Agent para coletar várias configurações relacionadas à segurança e os eventos de suas máquinas. Ele armazena esses eventos em seus espaços de trabalho. O [painel de eventos](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) permite que você exiba esses dados e fornece um ponto de entrada para o Log Analytics.

Podemos está se aposentando o painel de eventos que aparece quando você seleciona um espaço de trabalho:

![Painel de eventos][2]

### <a name="events-dashboard---the-new-experience"></a>Painel de eventos - a nova experiência

É recomendável usar os recursos nativos do Azure Log Analytics para exibir eventos notáveis nos seus espaços de trabalho.

Se você tiver criado os eventos notáveis personalizados na Central de segurança, eles serão acessíveis. No Log Analytics, vá para **selecione o espaço de trabalho** > **pesquisas salvas**. Seus dados não seja perdidos ou modificados. Eventos notáveis nativos também estão disponíveis na mesma tela no Log Analytics.

![Pesquisas salvo do espaço de trabalho][3]

## Entrada de menu de pesquisa<a name="menu_search"></a>

Atualmente, a Central de segurança do Azure usa pesquisa de logs do Azure Monitor para recuperar e analisar seus dados de segurança. Esta tela serve como uma janela para a página de pesquisa do Log Analytics e permite que os usuários executem consultas de pesquisa em seu espaço de trabalho selecionado. Para obter mais informações, consulte [pesquisa da Central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-search). Podemos está se aposentando essa janela de pesquisa:

![Página de pesquisa][4]

### <a name="search-menu-entry---the-new-experience"></a>Entrada de menu de pesquisa – a nova experiência

Incentivamos você a usar os recursos nativos do Azure Log Analytics para executar consultas de pesquisa em seus espaços de trabalho. Vá para o Azure Log Analytics e selecione **Logs**.

![Página de logs do log Analytics][5]

## Clássica identidade e acesso (versão prévia)<a name="menu_classicidentity"></a>

Atualmente, a experiência clássica de identidade e acesso na Central de segurança mostra um painel de informações de identidade e acesso no Log Analytics. Para exibir esse painel:

1. Selecione **exibir identidade e acesso clássicos**.

   ![Página de identidade][6]

1. Modo de exibição de **painel acesso e identidade**.

    ![Página de identidade - seleção de espaço de trabalho][7]

1. Selecione um espaço de trabalho para abrir o **identidade e acesso** painel no Log Analytics para exibir a identidade e acesso a informações em seu espaço de trabalho.

   ![Página de identidade - painel][8]

Podemos está se aposentando todas as três telas mostradas nas etapas anteriores. Seus dados permanecerão disponíveis na solução de segurança do Log Analytics e não ser modificados ou removidos.

### <a name="classic-identity--access-preview---the-new-experience"></a>Identidade e acesso (Preview) – a nova experiência clássicos

O painel de análise de Log mostrou insights em um único espaço de trabalho. No entanto, os recursos nativos de Central de segurança fornecem visibilidade de todas as assinaturas e todos os espaços de trabalho associados a eles. Você pode acessar uma forma fácil-usar o modo de exibição que lhe permite se concentrar no que é importante com recomendações classificadas de acordo com sua pontuação segura.

Todos os recursos da **identidade e acesso** painel no Log Analytics pode ser acessado selecionando **identidade e acesso (versão prévia)** na Central de segurança.

![Desativação da experiência de página de identidade - clássica][9]

## Mapa de eventos de segurança<a name="menu_securityeventsmap"></a>

Central de segurança fornece com um [mapa de alertas de segurança](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) para ajudar a identificar ameaças à segurança. O **ir para o mapa de eventos de segurança** botão nesse mapa abre um painel que permite que você exiba os eventos de segurança brutas no espaço de trabalho selecionado.

Podemos está removendo o **ir para o mapa de eventos de segurança** botão e o painel por espaço de trabalho.

![Botão de mapa - de alertas de segurança][10]

Quando você seleciona os **ir para o mapa de eventos de segurança** botão, você abre o painel de inteligência contra ameaças. Podemos está se aposentando o painel inteligência contra ameaças.  

![Painel Inteligência contra ameaças][11]

Quando você escolhe um espaço de trabalho para exibir o painel de inteligência de ameaça, você abrir a tela de mapa (visualização) de alertas de segurança no Log Analytics. Podemos está se aposentando nesta tela.

![Mapa de alertas de segurança no Log Analytics][12]

Os dados existentes permanecerão disponíveis na solução de segurança do Log Analytics e não ser modificados ou removidos.

### <a name="security-events-map---the-new-experience"></a>Mapa de eventos de segurança – a nova experiência

Incentivamos você a usar a funcionalidade de mapa de alertas criada à Central de segurança: **Mapa de alertas de segurança (visualização)** . Essa funcionalidade fornece uma experiência otimizada e funciona em todas as assinaturas e espaços de trabalho associados. Ele fornece uma exibição de alto nível em seu ambiente e não está focalizado em um único espaço de trabalho.

## Regras de alerta personalizadas (versão prévia)<a name="menu_customalerts"></a>

Estamos [desativando a personalizar a experiência de alertas](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) em 30 de junho de 2019 porque sua infraestrutura subjacente está sendo desativado. Até lá, você pode editar as regras de alerta personalizadas existentes, mas que não é possível adicionar novos. Após a data de desativação, todos os alertas personalizados definidos não entrarão em vigor e não serão gerados alertas de segurança com base nessas regras.
É recomendável que você habilite [Sentinel Azure](https://azure.microsoft.com/services/azure-sentinel/) e crie novamente os alertas de personalizado. Como alternativa, você pode criar seus alertas com alertas de log do Azure Monitor.

Para manter os alertas existentes e criá-los com o Azure Sentinel:

1. [Abra o Azure Sentinel](https://portal.azure.com/#create/Microsoft.ASI/preview) e selecione o espaço de trabalho onde os alertas personalizados são armazenados
1. Selecione **Analytics** no menu
1. Siga as instruções a seguir [tutorial](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats) sobre como criar alertas personalizados no Azure Sentinel

Se você não estiver interessado em usar Sentinel do Azure, você pode criar seus alertas com alertas de log do Azure Monitor. Para obter instruções, consulte [criar, exibir e gerenciar alertas do log usando o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) e [alertas de Log no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log).

![Alertas personalizados][13]

Para obter mais informações sobre a desativação de alertas personalizados, consulte [regras de alerta personalizadas na Central de segurança do Azure (visualização)](https://docs.microsoft.com/azure/security-center/security-center-custom-alert).

## Investigação de alertas de segurança<a name="menu_investigate"></a>

[O recurso de investigação](https://docs.microsoft.com/azure/security-center/security-center-investigation) na Central de segurança ajuda um incidente de segurança potencial fazer triagem. O recurso permite que você entenda o escopo de um incidente e rastrear a causa raiz. Podemos está removendo esse recurso na Central de segurança porque ele é foi substituído com uma experiência aprimorada no [Sentinel Azure](https://azure.microsoft.com/services/azure-sentinel/).

![Incidente de segurança][14]

Quando você seleciona os **investigar** botão de um **incidente de segurança** tela, abra o painel de investigação (visualização) no Log Analytics. Estamos desativando o painel de investigação.  

Os dados existentes permanecerão disponíveis na solução de segurança do Log Analytics e não ser modificados ou removidos.

![Painel de investigação no Log Analytics][15]

### <a name="investigation---the-new-experience"></a>Investigação – a nova experiência

Recomendamos que você faça a transição para [Sentinel Azure](https://azure.microsoft.com/services/azure-sentinel/) para uma experiência avançada de investigação. Sentinela do Azure fornece ferramentas avançadas de pesquisa e consulta de ameaças à segurança apresentar em fontes de dados da sua organização.  

## Subconjunto de soluções de segurança<a name="menu_solutions"></a>

Habilitar a Central de segurança [soluções de segurança no Azure integradas](https://docs.microsoft.com/azure/security-center/security-center-partner-integration). Podemos está se aposentando as seguintes soluções de parceiros na Central de segurança. Essas soluções estão habilitadas no [Sentinel Azure](https://azure.microsoft.com/services/azure-sentinel/) juntamente com um número de fontes de dados adicionais.

- [Próxima geração da web e firewall do aplicativo soluções de firewall](https://docs.microsoft.com/azure/sentinel/connect-data-sources)
- [Integração de soluções de segurança que dão suporte ao formato de evento comum (CEF)](https://docs.microsoft.com/azure/sentinel/connect-common-event-format)
- [Microsoft Advanced Threat Analytics](https://docs.microsoft.com/azure/sentinel/connect-azure-atp)
- [Azure AD Identity Protection](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection)

Após a desativação, você não poderá adicionar ou modificar qualquer um dos tipos de solução mencionados na lista anterior, a partir da interface do usuário ou a API.

Se você tiver as soluções existentes de conectada, incentivamos você a mover para o Azure Sentinel.

![Soluções de centros de segurança][16]

## Editar configurações de segurança para políticas de segurança<a name="menu_securityconfigurations"></a>

Central de segurança do Azure monitora as configurações de segurança aplicando um conjunto de [mais de 150 regras recomendadas](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). para proteger o sistema operacional. Essas regras pertencem aos firewalls, auditoria, políticas de senha e muito mais. Se uma configuração vulnerável é encontrada em um computador, a Central de Segurança gera uma recomendação de segurança. O [tela de configuração de segurança de edição](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) permite que os clientes personalizar a configuração de segurança padrão do sistema operacional na Central de segurança.

Podemos está se aposentando esse recurso de visualização. Se, após a data de desativação, você deseja ter redefinir suas configurações de segurança para seus valores padrão, você pode fazer isso por meio do Powershell ou a API usando o [seguindo instruções](https://aka.ms/ascresetsecurityconfigurations)

![Editar configurações de segurança][17]

### <a name="edit-security-configurations---the-new-experience"></a>Editar configurações de segurança – a nova experiência

Nossa intenção é habilitar a Central de segurança dar suporte a [agente de configuração do convidado](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration). Essa atualização permite que um conjunto de recursos muito mais sofisticado, incluindo suporte para mais sistemas operacionais e na integração das políticas de convidado do Azure para configurações de convidados. Depois que essas alterações são habilitadas, você também terá a capacidade de controlar as configurações em escala e aplicá-las aos novos recursos automaticamente.

## Painel de segurança e auditoria para espaços de trabalho do Log Analytics<a name="menu_securityomsdashboard"></a>

O painel segurança e auditoria foi usado originalmente no portal do OMS. No Log Analytics, o painel fornece uma visão geral de por espaço de trabalho de eventos de segurança importantes e ameaças, um mapa de inteligência de ameaça e uma avaliação de acesso e identidade de eventos de segurança salvo no espaço de trabalho. Podemos remover o painel. Como estamos já recomendado no painel de controle da interface do usuário, aconselhamos que você faça a transição para a Central de segurança do Azure.

![Painel de segurança do log Analytics][18]

### <a name="security-and-audit-dashboard---the-new-experience"></a>Painel de segurança e auditoria – a nova experiência

Aconselhamos que você alterne para a Central de segurança do Azure. Ele fornece a visão de geral de segurança mesmo entre várias assinaturas e os espaços de trabalho associados a eles, além de definir um recurso mais avançado.

Você pode obter as consultas de análise de Log originais que preenchem o painel segurança e auditoria na [repositório GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards) Central de segurança.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/).
- Saiba mais sobre [Sentinel Azure](https://docs.microsoft.com/azure/sentinel).

<!--Image references - events-->
[1]: ./media/security-center-features-retirement-july2019/asc_events_dashboard.png
[2]: ./media/security-center-features-retirement-july2019/asc_events_dashboard_inner.png
[3]: ./media/security-center-features-retirement-july2019/workspace_saved_searches.png
<!--Image references - search-->
[4]: ./media/security-center-features-retirement-july2019/asc_search.png
[5]: ./media/security-center-features-retirement-july2019/workspace_logs.png
<!--Image references - classic identity and access-->
[6]: ./media/security-center-features-retirement-july2019/asc_identity.png
[7]: ./media/security-center-features-retirement-july2019/asc_identity_workspace_selection.png
[8]: ./media/security-center-features-retirement-july2019/loganalytics_dashboard_identity.png
[9]: ./media/security-center-features-retirement-july2019/asc_identity_nobuttonhighlight.png
<!--Image references - alerts map-->
[10]: ./media/security-center-features-retirement-july2019/asc_security_alerts_map.png
[11]: ./media/security-center-features-retirement-july2019/asc_threat_intellignece_dashboard.png
[12]: ./media/security-center-features-retirement-july2019/loganalytics_security_alerts_map.png
<!--Image references - custom alerts-->
[13]: ./media/security-center-features-retirement-july2019/asc_custom_alerts.png
<!--Image references - Investigation-->
[14]: ./media/security-center-features-retirement-july2019/asc-security-incident.png
[15]: ./media/security-center-features-retirement-july2019/loganalytics_investigation_dashboard.png
<!--Image references - Solutions-->
[16]: ./media/security-center-features-retirement-july2019/asc_security_solutions.png
<!--Image references - Edit security configurations-->
[17]: ./media/security-center-features-retirement-july2019/asc_edit_security_configurations.png
<!--Image references - Security dashboard in log analytics-->
[18]: ./media/security-center-features-retirement-july2019/loganalytics_security_dashboard.png

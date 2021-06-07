---
title: Notas sobre a versão da Central de Segurança do Azure
description: Uma descrição do que há de novo e do que mudou na Central de Segurança do Azure
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 04/06/2021
ms.author: memildin
ms.openlocfilehash: 33184a9a9afaf05b093b8495fddd3b2c97fbd09a
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106492470"
---
# <a name="whats-new-in-azure-security-center"></a>Novidades na Central de Segurança do Azure

A Central de Segurança está em desenvolvimento ativo e recebe aprimoramentos continuamente. Para se manter atualizado com os desenvolvimentos mais recentes, esta página fornece informações sobre novos recursos, correções de bug e funcionalidades preteridas.

Esta página é atualizada com frequência, então acesse-a regularmente. 

Para saber mais sobre as alterações *planejadas* chegando em breve à Central de Segurança, consulte [Alterações importantes na Central de Segurança do Azure](upcoming-changes.md). 

> [!TIP]
> Se você estiver procurando itens que têm mais de seis meses, poderá encontrá-los nos [Arquivos de O que há de novo na Central de Segurança do Azure](release-notes-archive.md).

## <a name="april-2021"></a>Abril de 2021

As atualizações de abril incluem:
- [Quatro novas recomendações relacionadas à configuração de convidado (versão prévia)](#four-new-recommendations-related-to-guest-configuration-preview)
- [Usar o Azure Defender para Kubernetes a fim de proteger as implantações do Kubernetes híbridas e de várias nuvens (versão prévia)](#use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-preview)
- [11 alertas do Azure Defender preteridos](#11-azure-defender-alerts-deprecated)
- [Duas recomendações do controle de segurança "Aplicar atualizações do sistema" foram preteridas](#two-recommendations-from-apply-system-updates-security-control-were-deprecated)


### <a name="four-new-recommendations-related-to-guest-configuration-preview"></a>Quatro novas recomendações relacionadas à configuração de convidado (versão prévia)

A [extensão da Configuração de Convidado](../governance/policy/concepts/guest-configuration.md) do Azure relata à Central de Segurança para ajudar a garantir que as configurações das máquinas virtuais para convidados sejam protegidas. A extensão não é necessária para servidores habilitados para Arc porque está incluída no agente do Computador Conectado ao Arc. A extensão requer uma identidade gerenciada pelo sistema no computador.

Adicionamos quatro novas recomendações à Central de Segurança para aproveitar ao máximo essa extensão.

- Duas recomendações solicitam que você instale a extensão e a identidade obrigatória dela gerenciada pelo sistema:
    - **A extensão de Configuração de Convidado deve ser instalada nos seus computadores**
    - **A extensão de Configuração de Convidado das máquinas virtuais deve ser implantada com a identidade gerenciada atribuída ao sistema**

- Quando a extensão estiver instalada e em execução, ela começará a auditar seus computadores e você será solicitado proteger as configurações, como a configuração do sistema operacional e as configurações de ambiente. Essas duas recomendações solicitarão que você proteja seus computadores Windows e Linux conforme descrito:
    - **O Microsoft Defender Exploit Guard deve estar habilitado nos computadores**
    - **A autenticação para computadores Linux deve exigir chaves SSH**

Saiba mais em [Entender a Configuração de Convidado do Azure Policy](../governance/policy/concepts/guest-configuration.md).


### <a name="use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-preview"></a>Usar o Azure Defender para Kubernetes a fim de proteger as implantações do Kubernetes híbridas e de várias nuvens (versão prévia)

O Azure Defender para Kubernetes está expandindo as funcionalidades de proteção contra ameaças para defender seus clusters onde quer que estejam implantados. Isso foi habilitado pela integração com o [Kubernetes habilitado Azure Arc](../azure-arc/kubernetes/overview.md) e as novas funcionalidades de extensões dele. 

Quando você tiver habilitado o Azure Arc nos clusters do Kubernetes não Azure, uma nova recomendação da Central de Segurança do Azure oferecerá implantar a extensão do Azure Defender neles com apenas alguns cliques.

Use a recomendação (**Os clusters do Kubernetes habilitados para Azure Arc devem ter a extensão do Azure Defender instalada**) e a extensão para proteger os clusters do Kubernetes implantados em outros provedores de nuvem, embora não em seus serviços Kubernetes gerenciados.

Essa integração entre a Central de Segurança do Azure, o Azure Defender e o Kubernetes habilitado para Azure Arc traz:

- Provisionamento fácil da extensão do Azure Defender para clusters do Kubernetes desprotegidos habilitados para Azure Arc (manualmente e em escala)
- Monitoramento da extensão do Azure Defender e seu estado de provisionamento no portal do Azure Arc
- As recomendações de segurança da Central de Segurança são relatadas na nova Página de Segurança do Portal do Azure Arc
- As ameaças de segurança identificadas do Azure Defender são relatadas na nova página de segurança do portal do Azure Arc
- Os clusters do Kubernetes habilitados para Azure Arc são integrados à plataforma e à experiência da Central de Segurança do Azure

Saiba mais em [Usar o Azure Defender para Kubernetes com seus clusters do Kubernetes locais e de várias nuvens](defender-for-kubernetes-azure-arc.md).

### <a name="11-azure-defender-alerts-deprecated"></a>11 alertas do Azure Defender preteridos

Os onze alertas do Azure Defender listados abaixo foram preteridos.

- Novos alertas substituirão estes dois alertas e fornecerão uma cobertura melhor:

    | AlertType                | AlertDisplayName                                                         |
    |--------------------------|--------------------------------------------------------------------------|
    | ARM_MicroBurstDomainInfo | VERSÃO PRÉVIA – Execução da função "Get-AzureDomainInfo" do kit de ferramentas do MicroBurst detectada |
    | ARM_MicroBurstRunbook    | VERSÃO PRÉVIA – Execução da função "Get-AzurePasswords" do kit de ferramentas do MicroBurst detectada  |
    |                          |                                                                          |

- Estes nove alertas estão relacionados a um IPC do AAD (conector do Azure Active Directory Identity Protection) que já foi preterido:

    | AlertType           | AlertDisplayName              |
    |---------------------|-------------------------------|
    | UnfamiliarLocation  | Propriedades de entrada desconhecidas |
    | AnonymousLogin      | Endereço IP anônimo          |
    | InfectedDeviceLogin | Endereço IP vinculado a malware     |
    | ImpossibleTravel    | Viagem atípica               |
    | MaliciousIP         | Endereço IP mal-intencionado          |
    | LeakedCredentials   | Credenciais vazadas            |
    | PasswordSpray       | Pulverização de senha                |
    | LeakedCredentials   | Inteligência contra ameaças do Azure AD  |
    | AADAI               | IA do Azure AD                   |
    |                     |                               |
 
    > [!TIP]
    > Esses nove alertas do IPC nunca foram alertas da Central de Segurança. Eles fazem parte do IPC (conector de proteção de identidade) do AAD (Azure Active Directory) que estava enviando eles para a Central de Segurança. Nos últimos dois anos, os únicos clientes que viam esses alertas eram as organizações que configuraram a exportação (do conector para o ASC) em 2019 ou antes. O IPC do ADD continuou a mostrá-los em seus sistemas de alertas e eles continuaram disponíveis no Azure Sentinel. A única alteração é que eles não estão mais aparecendo na Central de Segurança.

### <a name="two-recommendations-from-apply-system-updates-security-control-were-deprecated"></a>Duas recomendações do controle de segurança "Aplicar atualizações do sistema" foram preteridas 

As duas recomendações seguintes foram preteridas e as alterações podem resultar em um pequeno impacto na sua classificação de segurança:

- **Seus computadores devem ser reiniciados para que as atualizações do sistema sejam aplicadas**
- **O agente de monitoramento deve ser instalado em seus computadores**. Essa recomendação está relacionada somente a computadores locais e uma parte da lógica dela será transferida para outra recomendação, **Problemas de integridade do agente do Log Analytics devem ser resolvidos em seus computadores**

É recomendável verificar suas configurações de exportação contínua e automação de fluxo de trabalho para ver se essas recomendações estão incluídas nelas. Além disso, painéis ou outras ferramentas de monitoramento que possam estar as usando devem ser atualizados de acordo.

Saiba mais sobre cada uma dessas recomendações na [página de referência de recomendações de segurança](recommendations-reference.md).


## <a name="march-2021"></a>Março de 2021

As atualizações de março incluem:

- [Gerenciamento de Firewall do Azure integrado à Central de Segurança](#azure-firewall-management-integrated-into-security-center)
- [Agora a avaliação de vulnerabilidades do SQL inclui a experiência "Desabilitar regra" (versão prévia)](#sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview)
- [Pastas de Trabalho do Azure Monitor integradas à Central de Segurança e três modelos fornecidos](#azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided)
- [Agora o painel de conformidade regulatória inclui os relatórios de auditoria do Azure (versão prévia)](#regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview)
- [Os dados de recomendação podem ser exibidos no Azure Resource Graph com "Explorar no ARG"](#recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg)
- [Atualizações das políticas para implantar a automação do fluxo de trabalho](#updates-to-the-policies-for-deploying-workflow-automation)
- [Duas recomendações herdadas não gravam mais dados diretamente no log de atividades do Azure](#two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log)
- [Aprimoramentos na página de recomendações](#recommendations-page-enhancements)


### <a name="azure-firewall-management-integrated-into-security-center"></a>Gerenciamento de Firewall do Azure integrado à Central de Segurança

Quando você abre a Central de Segurança do Azure, a primeira página a ser exibida é a página de visão geral. 

Esse painel interativo fornece uma exibição unificada da postura de segurança das cargas de trabalho de nuvem híbrida. Além disso, ela mostra alertas de segurança, informações de cobertura, entre outros.

Como parte da ajuda para exibir o status de segurança de uma experiência central, integramos o Gerenciador de Firewall do Azure a esse painel. Agora você pode verificar o status de cobertura do Firewall em todas as redes e gerenciar de forma centralizada as políticas de Firewall do Azure a partir da Central de Segurança.

Saiba mais sobre esse painel na [página de visão geral da Central de Segurança do Azure](overview-page.md).

:::image type="content" source="media/release-notes/overview-dashboard-firewall-manager.png" alt-text="Painel de visão geral da Central de Segurança com um bloco para o Firewall do Azure":::


### <a name="sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview"></a>Agora a avaliação de vulnerabilidades do SQL inclui a experiência "Desabilitar regra" (versão prévia)

A Central de Segurança inclui um scanner de vulnerabilidades interno para ajudar a descobrir, acompanhar e corrigir possíveis vulnerabilidades do banco de dados. Os resultados das verificações de avaliação fornecem uma visão geral do estado de segurança dos computadores SQL e detalhes sobre eventuais descobertas de segurança.

Caso você tenha uma necessidade organizacional para ignorar uma descoberta, em vez de corrigi-la, você pode opcionalmente desabilitá-la. As descobertas desabilitadas não afetam sua classificação de segurança nem geram um ruído indesejado.

Saiba mais em [Desabilitar descobertas específicas](defender-for-sql-on-machines-vulnerability-assessment.md#disable-specific-findings-preview).



### <a name="azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided"></a>Pastas de Trabalho do Azure Monitor integradas à Central de Segurança e três modelos fornecidos

Como parte do Ignite Spring 2021, anunciamos uma experiência integrada das Pastas de Trabalho do Azure Monitor na Central de Segurança.

Você pode aproveitar a nova integração para começar a utilizar os modelos prontos para uso da galeria da Central de Segurança. Usando modelos de pasta de trabalho, você pode acessar e criar relatórios dinâmicos e visuais para acompanhar a postura de segurança da organização. Além disso, você pode criar novas pastas de trabalho com base nos dados da Central de Segurança ou em qualquer outro tipo de dados com suporte e implantar rapidamente as pastas de trabalho da comunidade do GitHub da Central de Segurança.

São fornecidos três relatórios de modelos:

- **Classificação de segurança ao longo do tempo** – Acompanhar as pontuações das assinaturas e as alterações das recomendações para os recursos
- **Atualizações do sistema** – Exibir atualizações de sistema ausentes por recursos, sistema operacional, severidade e muito mais
- **Descobertas da avaliação de vulnerabilidade** – Exibir as descobertas das verificações de vulnerabilidade dos recursos do Azure

Saiba mais sobre como usar esses relatórios ou criar seus próprios [relatórios interativos e avançados dos dados da Central de Segurança](custom-dashboards-azure-workbooks.md).

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Relatório de classificação de segurança ao longo do tempo":::


### <a name="regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview"></a>Agora o painel de conformidade regulatória inclui os relatórios de auditoria do Azure (versão prévia)

Na barra de ferramentas do painel de conformidade regulatória, agora você pode baixar relatórios de certificação do Azure e do Dynamics. 

:::image type="content" source="media/release-notes/audit-reports-regulatory-compliance-dashboard.png" alt-text="Barra de ferramentas do painel de conformidade regulatória":::

Você pode selecionar a guia dos tipos de relatórios relevantes (PCI, SOC, ISO e outros) e usar filtros para localizar os relatórios necessários específicos.

Saiba mais sobre [Como gerenciar os padrões no painel de conformidade regulatória](update-regulatory-compliance-packages.md).

:::image type="content" source="media/release-notes/audit-reports-list-regulatory-compliance-dashboard.png" alt-text="Como filtrar a lista de relatórios de auditoria disponíveis do Azure":::



### <a name="recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg"></a>Os dados de recomendação podem ser exibidos no Azure Resource Graph com "Explorar no ARG"

As páginas de detalhes de recomendação agora incluem o botão de barra de ferramentas "Explorar no ARG". Use esse botão para abrir uma consulta do Azure Resource Graph e explorar, exportar e compartilhar os dados da recomendação.

O ARG (Azure Resource Graph) fornece acesso instantâneo a informações de recursos em seus ambientes de nuvem com recursos robustos de filtragem, agrupamento e classificação. É uma maneira rápida e eficiente de consultar informações em assinaturas do Azure programaticamente ou de dentro do portal do Azure.

Saiba mais sobre o [ARG (Azure Resource Graph)](../governance/resource-graph/index.yml).

:::image type="content" source="media/release-notes/explore-in-resource-graph.png" alt-text="Explore os dados de recomendação no Azure Resource Graph.":::


### <a name="updates-to-the-policies-for-deploying-workflow-automation"></a>Atualizações das políticas para implantar a automação do fluxo de trabalho

A automatização dos processos de monitoramento e resposta a incidentes da sua organização pode aprimorar significativamente o tempo necessário para investigar e atenuar incidentes de segurança.

Fornecemos três políticas 'DeployIfNotExist' do Azure Policy que criam e configuram os procedimentos de automação do fluxo de trabalho para que você possa implantar as automações na organização:

|Goal  |Política  |ID da Política  |
|---------|---------|---------|
|Automação de fluxo de trabalho para alertas de segurança|[Implantar a Automação de Fluxo de Trabalho para alertas da Central de Segurança do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Automação de fluxo de trabalho para recomendações de segurança|[Implantar a Automação de Fluxo de Trabalho para recomendações da Central de Segurança do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
|Automação do fluxo de trabalho para alterações de conformidade regulatória|[Implantar a Automação de Fluxo de Trabalho para conformidade regulatória da Central de Segurança do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-509122b9-ddd9-47ba-a5f1-d0dac20be63c)|509122b9-ddd9-47ba-a5f1-d0dac20be63c|
||||

Há duas atualizações para os recursos dessas políticas:

- Quando atribuídas, elas permanecerão habilitadas por aplicação.
- Agora você pode personalizar essas políticas e atualizar qualquer um dos parâmetros mesmo depois que eles já tiverem sido implantados. Por exemplo, se um usuário quer adicionar outra chave de avaliação ou editar uma chave de avaliação existente, ele pode.

Introdução aos [modelos de automação de fluxo de trabalho](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

Saiba mais sobre como [Automatizar respostas para os gatilhos da Central de Segurança](workflow-automation.md).


### <a name="two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log"></a>Duas recomendações herdadas não gravam mais dados diretamente no log de atividades do Azure 

A Central de Segurança transmite os dados de quase todas as recomendações de segurança para o Assistente do Azure, que, por sua vez, grava no [log de atividades do Azure](../azure-monitor/essentials/activity-log.md).

Para duas recomendações, os dados são gravados de modo simultâneo diretamente no log de atividades do Azure. Com essa alteração, a Central de Segurança para de gravar dados para essas recomendações de segurança herdadas diretamente no log de atividades. Em vez disso, os dados são exportados para o Assistente do Azure, assim como todas as outras recomendações.

As duas recomendações herdadas são:
- Os problemas de integridade do Endpoint protection devem ser resolvidos nos seus computadores
- As vulnerabilidades da configuração de segurança nas máquinas devem ser corrigidas

Se você estiver acessando informações dessas duas recomendações na categoria "Recomendação do tipo TaskDiscovery" do log de atividades, isso não estará mais disponível.


### <a name="recommendations-page-enhancements"></a>Aprimoramentos na página de recomendações 

Lançamos uma versão aprimorada da lista de recomendações para apresentar mais informações em uma visão rápida.

Agora, na página, você verá:

1. A pontuação máxima e a pontuação atual de cada controle de segurança.
1. Ícones que substituem marcas como **Correção rápida** e **Versão Prévia**.
1. Uma nova coluna mostrando a [Iniciativa de política](security-policy-concept.md) relacionada a cada recomendação, visível quando a opção "Agrupar por controles" está desabilitada.

:::image type="content" source="media/release-notes/recommendations-grid-enhancements.png" alt-text="Aprimoramentos na página de recomendações da Central de Segurança do Azure – Março de 2021" lightbox="media/release-notes/recommendations-grid-enhancements.png":::

:::image type="content" source="media/release-notes/recommendations-grid-enhancements-initiatives.png" alt-text="Aprimoramentos na lista 'simples' de recomendações da Central de Segurança do Azure – Março de 2021" lightbox="media/release-notes/recommendations-grid-enhancements-initiatives.png":::

Saiba mais em [Recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md).


## <a name="february-2021"></a>Fevereiro de 2021

As atualizações de fevereiro incluem:

- [Nova página de alertas de segurança no portal do Azure liberada para GA (disponibilidade geral)](#new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga)
- [Lançamento das recomendações de proteção de cargas de trabalho do Kubernetes em GA (disponibilidade geral)](#kubernetes-workload-protection-recommendations-released-for-general-availability-ga)
- [Agora a integração do Microsoft Defender para Ponto de Extremidade com o Azure Defender dá suporte ao Windows Server 2019 e à Área de Trabalho Virtual do Windows 10 (WVD) (na versão prévia)](#microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview)
- [Link direto para a política na página de detalhes da recomendação](#direct-link-to-policy-from-recommendation-details-page)
- [A recomendação de classificação de dados SQL não afeta mais a sua classificação de segurança](#sql-data-classification-recommendation-no-longer-affects-your-secure-score)
- [As automações de fluxo de trabalho podem ser disparadas por alterações nas avaliações de conformidade regulatória (na versão prévia)](#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview)
- [Aprimoramentos de página de inventário de ativos](#asset-inventory-page-enhancements)


### <a name="new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga"></a>Nova página de alertas de segurança no portal do Azure liberada para GA (disponibilidade geral)

A página de alertas de segurança da Central de Segurança do Azure foi reprojetada para oferecer:

- **Experiência de triagem aprimorada para alertas** – ajudando a reduzir os alertas exaustivos e concentrar-se nas ameaças mais relevantes com mais facilidade, a lista inclui filtros personalizáveis e opções de agrupamento.
- **Mais informações na lista de alertas** – como táticas MITRE ATT&ACK.
- **Botão para criação de alertas de exemplo** – para avaliar as funcionalidades do Azure Defender e testar a sua configuração de alertas (para integração do SIEM, notificações por email e automação de fluxo de trabalho), você pode criar alertas de exemplo em todos os planos do Azure Defender.
- **Alinhamento com a experiência de incidentes do Azure Sentinel** – para clientes que usam os dois produtos, a troca entre eles agora é uma experiência mais simples, além de ser fácil aprender a usar deles com base no conhecimento adquirido com o outro.
- **Melhor desempenho** para listas grandes de alertas.
- **Navegação por teclado** na lista de alertas.
- **Alertas do Azure Resource Graph** – você pode consultar alertas no Azure Resource Graph, a API semelhante ao Kusto para todos os seus recursos. Isso também será útil se você estiver criando os próprios painéis de alertas. [Saiba mais sobre Azure Resource Graph](../governance/resource-graph/index.yml).
- [Recurso de criação de alertas de exemplo](security-center-alert-validation.md#generate-sample-azure-defender-alerts) – para criar alertas de exemplo por meio da nova experiência de alertas, confira **Gerar alertas de exemplo do Azure Defender**.

:::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Lista de alertas de segurança da Central de Segurança do Azure":::


### <a name="kubernetes-workload-protection-recommendations-released-for-general-availability-ga"></a>Lançamento das recomendações de proteção de cargas de trabalho do Kubernetes em GA (disponibilidade geral)

Estamos felizes em anunciar a GA (disponibilidade geral) do conjunto de recomendações para proteções de cargas de trabalho do Kubernetes.

Para garantir que as cargas de trabalho do Kubernetes sejam seguras por padrão, a Central de Segurança adicionou recomendações de proteção no nível do Kubernetes, incluindo opções de imposição com o controle de admissão do Kubernetes.

Quando o complemento do Azure Policy para Kubernetes for instalado no seu cluster do AKS (Serviço de Kubernetes do Azure), todas as solicitações para o servidor de API do Kubernetes serão monitoradas em relação ao conjunto predefinido de melhores práticas, exibidas como 13 recomendações de segurança, antes de serem persistidas no cluster. Em seguida, você pode configurá-lo para impor as melhores práticas e exigir o uso dele em cargas de trabalho futuras.

Por exemplo, você poderá proibir a criação de contêineres privilegiados, e todas as futuras solicitações para fazer isso serão bloqueadas.

Saiba mais em [Melhores práticas de proteção de cargas de trabalho usando o controle de admissão do Kubernetes](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control).

> [!NOTE]
> Enquanto as recomendações estavam em versão prévia, elas não renderizavam um recurso de cluster do AKS não íntegro e não eram incluídas nos cálculos da sua classificação de segurança. Com este comunicado de GA, elas serão incluídas no cálculo da classificação. Se você ainda não corrigiu, isso pode resultar em um pequeno impacto em sua classificação de segurança. Corrija-as sempre que possível, conforme descrito em [Corrigir recomendações na Central de Segurança do Azure](security-center-remediate-recommendations.md).


### <a name="microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview"></a>Agora a integração do Microsoft Defender para Ponto de Extremidade com o Azure Defender dá suporte ao Windows Server 2019 e à Área de Trabalho Virtual do Windows 10 (WVD) (na versão prévia)

O Microsoft Defender para Ponto de Extremidade é uma solução de segurança de ponto de extremidade holística na nuvem. Ele fornece gerenciamento e avaliação de vulnerabilidades com base no risco, bem como EDR (detecção e resposta de ponto de extremidade). Para obter uma lista completa dos benefícios de usar o Defender para Ponto de Extremidade juntamente com a Central de Segurança do Azure, confira [Proteger os pontos de extremidade com a solução integrada de EDR da Central de Segurança: Microsoft Defender para Ponto de Extremidade](security-center-wdatp.md).

Quando você habilita o Azure Defender para servidores em um Windows Server, uma licença do Defender para Ponto de Extremidade é incluída no plano. Se você já habilitou o Azure Defender para servidores e tem servidores Windows 2019 em sua assinatura, eles receberão automaticamente o Defender para Ponto de Extremidade com essa atualização. Não é necessária nenhuma ação manual. 

Agora o suporte foi expandido para incluir o Windows Server 2019 e a [WVD (Área de Trabalho Virtual do Windows)](../virtual-desktop/overview.md).

> [!NOTE]
> Se você estiver habilitando o Defender para Ponto de Extremidade em um computador com Windows Server 2019, verifique se ele atende aos pré-requisitos descritos em [Como habilitar a integração do Microsoft Defender para Ponto de Extremidade](security-center-wdatp.md#enabling-the-microsoft-defender-for-endpoint-integration).

### <a name="direct-link-to-policy-from-recommendation-details-page"></a>Link direto para a política na página de detalhes da recomendação

Quando você estiver examinando os detalhes de uma recomendação, muitas vezes, será útil poder ver a política subjacente. Para cada recomendação com suporte de uma política, há um novo link na página de detalhes da recomendação:

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Link para a página do Azure Policy de uma política específica que dá suporte a uma recomendação":::

Use esse link para ver a definição de política e examinar a lógica de avaliação. 

Se você estiver examinando a lista de recomendações no nosso [Guia de referência de recomendações de segurança](recommendations-reference.md), também verá links para as páginas de definição da política:

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Como acessar a página do Azure Policy de uma política específica diretamente na página de referência de recomendações da Central de Segurança do Azure" lightbox="media/release-notes/view-policy-definition-from-documentation.png":::


### <a name="sql-data-classification-recommendation-no-longer-affects-your-secure-score"></a>A recomendação de classificação de dados SQL não afeta mais a sua classificação de segurança
A recomendação **Os dados confidenciais nos seus bancos de dados SQL devem ser classificados** não afetará mais sua classificação de segurança. Essa é a única recomendação no controle de segurança **Aplicar classificação de dados**, para que o controle agora tenha um valor de classificação de segurança igual a 0.

Para obter uma lista completa de todos os controles de segurança na Central de Segurança, juntamente com as pontuações e uma lista das recomendações em cada, confira [Controles de segurança e suas recomendações](secure-score-security-controls.md#security-controls-and-their-recommendations).

### <a name="workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview"></a>As automações de fluxo de trabalho podem ser disparadas por alterações nas avaliações de conformidade regulatória (na versão prévia)
Adicionamos um terceiro tipo de dados às opções de gatilho para suas automações de fluxo de trabalho: alterações nas avaliações de conformidade regulatória.

Saiba como usar as ferramentas de automação do fluxo de trabalho em [Automatizar respostas para os gatilhos da Central de Segurança](workflow-automation.md).

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Como usar as avaliações de conformidade regulatória para disparar uma automação de fluxo de trabalho" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::


### <a name="asset-inventory-page-enhancements"></a>Aprimoramentos de página de inventário de ativos
A página de inventário de ativos da Central de Segurança foi aprimorada das seguintes maneiras:

- Os resumos na parte superior da página agora incluem **Assinaturas não registradas**, mostrando o número de assinaturas sem a Central de Segurança habilitada.

    :::image type="content" source="media/release-notes/unregistered-subscriptions.png" alt-text="Contagem de assinaturas não registradas nos resumos na parte superior da página de inventário de ativos":::

- Os filtros foram expandidos e aprimorados para incluir:
    - **Contagens** – Cada filtro apresenta o número de recursos que atendem aos critérios de cada categoria

        :::image type="content" source="media/release-notes/counts-in-inventory-filters.png" alt-text="Contagens nos filtros na página de inventário de ativos da Central de Segurança do Azure":::

    - **Contém filtro de isenções** (opcional) – Restrinja os resultados a recursos que têm/não têm isenções. Esse filtro não é mostrado por padrão, mas pode ser acessado no botão **Adicionar filtro**.

        :::image type="content" source="media/release-notes/adding-contains-exemption-filter.gif" alt-text="Foi adicionado o filtro 'contém isenção' na página de inventário de ativos da Central de Segurança do Azure":::

Saiba mais sobre como [Explorar e gerenciar seus recursos com o inventário de ativos](asset-inventory.md).

## <a name="january-2021"></a>Janeiro de 2021

As atualizações em janeiro incluem:

- [O Azure Security Benchmark agora é a iniciativa de política padrão da Central de Segurança do Azure](#azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center)
- [Avaliação de vulnerabilidades para computadores locais e multinuvem liberada para GA (Disponibilidade Geral)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga)
- [A classificação de segurança para grupos de gerenciamento agora está disponível na versão prévia](#secure-score-for-management-groups-is-now-available-in-preview)
- [API de classificação de segurança liberada para GA (Disponibilidade Geral)](#secure-score-api-is-released-for-general-availability-ga)
- [Proteções de DNS pendentes e adicionadas ao Azure Defender para o Serviço de Aplicativo](#dangling-dns-protections-added-to-azure-defender-for-app-service)
- [Conectores de várias nuvens são liberados para GA (Disponibilidade Geral)](#multi-cloud-connectors-are-released-for-general-availability-ga)
- [Isente recomendações inteiras da sua classificação de segurança para assinaturas e grupos de gerenciamento](#exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups)
- [Os usuários agora podem solicitar visibilidade em todo o locatário no administrador global](#users-can-now-request-tenant-wide-visibility-from-their-global-administrator)
- [35 recomendações de versão prévia adicionadas para aumentar a cobertura do Azure Security Benchmark](#35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [Exportação de CSV da lista de recomendações filtrada](#csv-export-of-filtered-list-of-recommendations)
- [Recursos "não aplicáveis" que agora são relatados como "em conformidade" nas avaliações do Azure Policy](#not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments)
- [Exportar instantâneos semanais da classificação de segurança e dos dados de conformidade regulatória com exportação contínua (versão prévia)](#export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview)


### <a name="azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center"></a>O Azure Security Benchmark agora é a iniciativa de política padrão da Central de Segurança do Azure

O Azure Security Benchmark é um conjunto específico de diretrizes específicas do Azure criadas pela Microsoft com as melhores práticas de segurança e conformidade baseadas em estruturas de conformidade comuns. Esse parâmetro de comparação amplamente respeitado se baseia nos controles do [CIS (Center for Internet Security)](https://www.cisecurity.org/benchmark/azure/) e do [NIST (National Institute of Standards and Technology)](https://www.nist.gov/) com foco na segurança centrada na nuvem.

Nos últimos meses, a lista da Central de Segurança de recomendações de segurança internas cresceu significativamente expandindo nossa cobertura desse parâmetro de comparação.

A partir desta versão, o parâmetro de comparação é a base para as recomendações da Central de Segurança e é totalmente integrado como a iniciativa de política padrão. 

Todos os serviços do Azure têm uma página de linha de base de segurança na documentação. Por exemplo, [esta é a linha de base da Central de Segurança](security-baseline.md). Essas linhas de base são criadas no Azure Security Benchmark.

Se você estiver usando o painel de conformidade regulatória da Central de Segurança, verá duas instâncias do parâmetro de comparação durante um período de transição:

:::image type="content" source="media/release-notes/regulatory-compliance-with-azure-security-benchmark.png" alt-text="O painel de conformidade regulatória da Central de Segurança do Azure que mostra o Azure Security Benchmark":::

As recomendações existentes não são afetadas e, conforme o parâmetro de comparação cresce, as alterações serão refletidas automaticamente na Central de Segurança. 

Para saber mais, confira as seguintes páginas:

- [Saiba mais sobre o Azure Security Benchmark](../security/benchmarks/introduction.md)
- [Personalizar o conjunto de padrões no seu painel de conformidade regulatória](update-regulatory-compliance-packages.md)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga"></a>Avaliação de vulnerabilidades para computadores locais e multinuvem liberada para GA (Disponibilidade Geral)

Em outubro, anunciamos uma versão prévia para a verificação de servidores habilitados para Azure Arc com um verificador de avaliação de vulnerabilidade integrado ao [Azure Defender para servidores](defender-for-servers-introduction.md)(da plataforma Qualys).

Ela já está liberada para GA (Disponibilidade Geral).

Quando você tiver habilitado o Azure Arc em seus computadores que não são do Azure, a Central de Segurança oferecerá a implantação do verificador de vulnerabilidades integrado neles, manualmente e em escala.

Com essa atualização, você pode tirar proveito da eficiência do **Azure Defender para servidores** para consolidar seu programa de gerenciamento de vulnerabilidades em todos os seus ativos que são e que não são do Azure.

Principais recursos:

- Monitoramento do estado de provisionamento do verificador de VA (avaliação de vulnerabilidade) em computadores do Azure Arc
- Provisionamento do agente de VA integrado para computadores Windows e Linux do Azure Arc desprotegidos (manualmente e em escala)
- Recebimento e análise das vulnerabilidades detectadas por agentes implantados (manualmente e em escala)
- Experiência unificada para VMs do Azure e computadores do Azure Arc

[Saiba mais sobre como implantar o verificador de vulnerabilidades integrado em computadores híbridos](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines).

[Saiba mais sobre os servidores habilitados para Azure Arc](../azure-arc/servers/index.yml).


### <a name="secure-score-for-management-groups-is-now-available-in-preview"></a>A classificação de segurança para grupos de gerenciamento agora está disponível na versão prévia

A página de classificação de segurança agora mostra as classificações de segurança agregadas dos seus grupos de gerenciamento, além do nível de assinatura. Agora, você pode ver a lista de grupos de gerenciamento na sua organização e a pontuação de cada grupo de gerenciamento.

:::image type="content" source="media/secure-score-security-controls/secure-score-management-groups.png" alt-text="Como exibir as classificações de segurança de seus grupos de gerenciamento.":::

Saiba mais sobre os [controles de segurança e classificação de segurança na Central de Segurança do Azure](secure-score-security-controls.md).

### <a name="secure-score-api-is-released-for-general-availability-ga"></a>API de classificação de segurança liberada para GA (Disponibilidade Geral)

Agora é possível acessar sua classificação por meio de uma [API de classificação de segurança](/rest/api/securitycenter/securescores/). Os métodos de API oferecem a flexibilidade para consultar os dados e criar seu mecanismo de relatório das suas classificações de segurança ao longo do tempo. Por exemplo:

- use a API **Classificações de Segurança** para obter a classificação de uma assinatura específica
- use a API **Controles de Classificação de Segurança** para listar os controles de segurança e a classificação atual das suas assinaturas

Saiba mais sobre as ferramentas externas que são possíveis com a API de classificação de segurança [na área de classificação de segurança da nossa comunidade do GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score).

Saiba mais sobre os [controles de segurança e classificação de segurança na Central de Segurança do Azure](secure-score-security-controls.md).


### <a name="dangling-dns-protections-added-to-azure-defender-for-app-service"></a>Proteções de DNS pendentes e adicionadas ao Azure Defender para o Serviço de Aplicativo

As invasões de subdomínios são uma ameaça comum de gravidade alta para as organizações. Uma tomada de controle de subdomínio poderá ocorrer quando um registro DNS indicar um site desprovisionado. Esses registros DNS também são conhecidos como entradas "DNS pendentes". Os registros CNAME são particularmente vulneráveis a essa ameaça. 

As tomadas de controle de subdomínios permitem que agentes de ameaça redirecionem o tráfego destinado ao domínio de uma organização para um site que executa atividades mal-intencionadas.

O Azure Defender para o Serviço de Aplicativo agora detecta entradas DNS pendentes quando um site do Serviço de Aplicativo é encerrado. Nesse momento, a entrada DNS indicará um recurso inexistente, e seu site estará vulnerável a uma tomada de controle de subdomínio. Essas proteções estarão disponíveis caso seus domínios sejam gerenciados usando o DNS do Azure ou um registrador de domínios externo. Além disso, elas se aplicam ao Serviço de Aplicativo no Windows e ao Serviço de Aplicativo no Linux.

Saiba mais:

- [Tabela de referência de alertas do Serviço de Aplicativo](alerts-reference.md#alerts-azureappserv) – Inclui dois novos alertas do Azure Defender que disparam quando uma entrada DNS pendente é detectada
- [Impedir a ocorrência de entradas DNS pendentes e evitar a tomada de controle de subdomínio](../security/fundamentals/subdomain-takeover.md) – Saiba mais sobre aspectos da ameaça de tomada de controle de subdomínio e de um DNS pendente
- [Introdução ao Azure Defender para Serviço de Aplicativo](defender-for-app-service-introduction.md)


### <a name="multi-cloud-connectors-are-released-for-general-availability-ga"></a>Conectores de várias nuvens são liberados para GA (Disponibilidade Geral)

Com as cargas de trabalho de nuvem normalmente abrangendo plataformas de várias nuvens, os serviços de segurança de nuvem precisam fazer o mesmo.

A Central de Segurança do Azure protege as cargas de trabalho no Azure, na AWS (Amazon Web Services) e no GCP (Google Cloud Platform).

Conectar suas contas da AWS ou da GCP integrará ferramentas de segurança nativas, como o AWS Security Hub e o Centro de Comando de Segurança da GCP, à Central de Segurança do Azure.

Essa funcionalidade significa que a Central de Segurança fornece visibilidade e proteção em todos os principais ambientes de nuvem. Alguns dos benefícios dessa integração:

- Provisionamento automático de agente – A Central de Segurança usa o Azure Arc para implantar o agente do Log Analytics nas instâncias da AWS
- Gerenciamento de política
- Gerenciamento de vulnerabilidades
- Detecção e Resposta de Ponto de Extremidade Inserido (EDR)
- Detecção de configurações incorretas de segurança
- Uma exibição que mostra as recomendações de segurança de todos os provedores de nuvem
- Incorporar todos os recursos aos cálculos de classificação de segurança da Central de Segurança
- Avaliações de conformidade regulatória dos recursos da AWS e da GCP

No menu da Central de Segurança, selecione **Conectores de várias nuvens** e você verá as opções para criar conectores:

:::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Botão Adicionar conta da AWS na página de vários conectores de nuvem da Central de Segurança":::

Saiba mais em:
- [Conectar as contas da AWS à Central de Segurança do Azure](quickstart-onboard-aws.md)
- [Conectar as contas do GCP à Central de Segurança do Azure](quickstart-onboard-gcp.md)


### <a name="exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups"></a>Isente recomendações inteiras da sua classificação de segurança para assinaturas e grupos de gerenciamento

Estamos expandindo a funcionalidade de isenção para incluir recomendações inteiras. Fornecendo mais opções para ajustar as recomendações de segurança que a Central de Segurança faz para as suas assinaturas, grupo de gerenciamento ou recursos.

Ocasionalmente, um recurso será listado como não íntegro quando você souber que o problema foi resolvido por uma ferramenta de terceiros que não foi detectada pela Central de Segurança. Ou uma recomendação será mostrada em um escopo no qual você acha que ela não pertence. A recomendação pode ser inadequada para uma assinatura específica. Ou talvez a sua organização tenha decidido aceitar os riscos relacionados à recomendação ou ao recurso específico.

Com essa versão prévia do recurso, agora você pode criar uma isenção de uma recomendação para:

- **Isentar um recurso** para garantir que ele não esteja listado com os recursos não íntegros no futuro e não afete a sua classificação de segurança. O recurso será listado como não aplicável e o motivo será mostrado como "isento" com a justificativa específica que você selecionar.

- **Isente uma assinatura ou grupo de gerenciamento** para garantir que a recomendação não afete a sua classificação de segurança e não seja mostrada para a assinatura ou grupo de gerenciamento no futuro. Isso está relacionado aos recursos existentes e a qualquer um que você criar no futuro. A recomendação será marcada com a justificativa específica selecionada para o escopo que você selecionou.

Saiba mais em [Isentando recursos e recomendações da sua classificação de segurança](exempt-resource.md).



### <a name="users-can-now-request-tenant-wide-visibility-from-their-global-administrator"></a>Os usuários agora podem solicitar visibilidade em todo o locatário no administrador global

Caso não tenham permissões para conferir os dados da Central de Segurança, os usuários agora podem acessar um link para solicitar permissões ao administrador global da organização. A solicitação inclui a função que ele deseja e a justificativa do por que ela é necessária.

:::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="Faixa informando a um usuário que ele pode solicitar permissões em todo o locatário.":::

Saiba mais em [Solicitar permissões em todo o locatário quando as suas forem insuficientes](tenant-wide-permissions-management.md#request-tenant-wide-permissions-when-yours-are-insufficient).


### <a name="35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>35 recomendações de versão prévia adicionadas para aumentar a cobertura do Azure Security Benchmark

O Azure Security Benchmark é a iniciativa de política padrão na Central de Segurança do Azure. 

Para aumentar a cobertura desse parâmetro de comparação, as 35 recomendações de versão prévia a seguir foram adicionadas à Central de Segurança.

> [!TIP]
> As recomendações de versão prévia não processam um recurso não íntegro e não são incluídas nos cálculos de sua classificação de segurança. Corrija-as sempre que possível, para que, quando o período da versão prévia terminar, elas contribuam para sua classificação. Saiba mais sobre como responder a essas recomendações em [Recomendações de correção na Central de Segurança do Azure](security-center-remediate-recommendations.md).

| Controle de segurança                     | Novas recomendações                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Habilitar a criptografia em repouso            | – As contas do Azure Cosmos DB devem usar chaves gerenciadas pelo cliente para criptografar os dados inativos<br>– Os workspaces do Azure Machine Learning devem ser criptografados com uma CMK (chave gerenciada pelo cliente)<br>– A proteção de dados do Bring Your Own Key será habilitada para servidores MySQL<br>– A proteção de dados do Bring Your Own Key será habilitada para servidores PostgreSQL<br>– As contas dos Serviços Cognitivos devem habilitar a criptografia de dados com uma CMK (chave gerenciada pelo cliente)<br>– Os registros de contêiner devem ser criptografados com uma CMK (chave gerenciada pelo cliente)<br>– As instâncias gerenciadas de SQL devem usar chaves gerenciadas pelo cliente para criptografar dados inativos<br>– Os SQL Servers devem usar chaves gerenciadas pelo cliente para criptografar dados inativos<br>– As contas de armazenamento devem usar uma CMK (chave gerenciada pelo cliente) para criptografia                                                                                                                                                              |
| Implementar melhores práticas de segurança    | – As assinaturas devem ter um endereço de email de contato para problemas de segurança<br> – O provisionamento automático do agente do Log Analytics será habilitado na sua assinatura<br> – A notificação por email para alertas de alta severidade deve ser habilitada<br> – A notificação por email para o proprietário da assinatura para alertas de alta severidade deve ser habilitada<br> – Os cofres de chaves devem ter a proteção contra limpeza habilitada<br> – Os cofres de chaves devem ter a exclusão temporária habilitada |
| Gerenciar acesso e permissões        | – Os aplicativos de funções devem ter a opção 'Certificados do Cliente (Certificados do cliente de entrada)' habilitada |
| Proteger os aplicativos contra DDoS | – O WAF (Firewall de Aplicativo Web) deve ser habilitado para o Gateway de Aplicativo<br> – O WAF (Firewall de Aplicativo Web) deve ser habilitado para o Azure Front Door Service |
| Restringir acesso não autorizado à rede | – O firewall deve ser habilitado no Key Vault<br> – O ponto de extremidade privado deve ser configurado para o Key Vault<br> – A Configuração de Aplicativos deve usar um link privado<br> – O Cache do Azure para Redis deve residir em uma rede virtual<br> – Os domínios da Grade de Eventos do Azure devem usar um link privado<br> – Os tópicos da Grade de Eventos do Azure devem usar um link privado<br> – Os workspaces do Azure Machine Learning devem usar um link privado<br> – O Serviço do Azure SignalR deve usar um link privado<br> – O Azure Spring Cloud deve usar uma injeção de rede<br> – Os registros de contêiner não devem permitir acesso irrestrito à rede<br> – Os registros de contêiner devem usar um link privado<br> – O acesso à rede pública deve ser desabilitado para servidores MariaDB<br> – O acesso à rede pública deve ser desabilitado para servidores MySQL<br> – O acesso à rede pública deve ser desabilitado para servidores PostgreSQL<br> – A conta de armazenamento deve usar uma conexão de link privado<br> – As contas de armazenamento devem restringir o acesso à rede usando regras de rede virtual<br> – Os modelos do Construtor de Imagens de VM devem usar um link privado|
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Links relacionados:

- [Saiba mais sobre o Azure Security Benchmark](../security/benchmarks/introduction.md)
- [Saiba mais sobre o Banco de Dados do Azure para MariaDB](../mariadb/overview.md)
- [Saiba mais sobre o Banco de Dados do Azure para MySQL](../mysql/overview.md)
- [Saiba mais sobre o Banco de Dados do Azure para PostgreSQL](../postgresql/overview.md)




### <a name="csv-export-of-filtered-list-of-recommendations"></a>Exportação de CSV da lista de recomendações filtrada 

Em novembro de 2020, adicionamos filtros à página de recomendações ([a lista de recomendações agora inclui filtros](#recommendations-list-now-includes-filters)). Em dezembro, expandimos esses filtros ([a página Recomendações tem novos filtros para o ambiente, a severidade e as respostas disponíveis](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)). 

Com este comunicado, estamos alterando o comportamento do botão **Baixar para CSV** para que a exportação de CSV inclua apenas as recomendações atualmente exibidas na lista filtrada. 

Por exemplo, na imagem abaixo, você pode ver que a lista foi filtrada para duas recomendações. O arquivo CSV gerado inclui os detalhes de status de cada recurso afetado por essas duas recomendações.   

:::image type="content" source="media/security-center-managing-and-responding-alerts/export-to-csv-with-filters.png" alt-text="Como exportar recomendações filtradas para um arquivo CSV":::

Saiba mais em [Recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md).


### <a name="not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments"></a>Recursos "não aplicáveis" que agora são relatados como "em conformidade" nas avaliações do Azure Policy

Anteriormente, os recursos avaliados com o objetivo de obter uma recomendação e considerados **não aplicáveis** eram mostrados como "não compatíveis" no Azure Policy. Nenhuma ação do usuário poderia alterar o estado deles para "em conformidade". Com essa alteração, os recursos agora são relatados como "em conformidade" para maior clareza.

O único impacto será visto no Azure Policy, em que o número de recursos em conformidade aumentará. Não haverá nenhum impacto na sua classificação de segurança na Central de Segurança do Azure.


### <a name="export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview"></a>Exportar instantâneos semanais da classificação de segurança e dos dados de conformidade regulatória com exportação contínua (versão prévia)

Adicionamos uma versão prévia do recurso às ferramentas de [exportação contínua](continuous-export.md) para exportar instantâneos semanais de classificação de segurança e dados de conformidade regulatória.

Ao definir uma exportação contínua, defina a frequência de exportação:

:::image type="content" source="media/release-notes/export-frequency.png" alt-text="Como escolher a frequência da exportação contínua":::

- **Streaming**: as avaliações serão enviadas em tempo real quando o estado de integridade de um recurso for atualizado (se nenhuma atualização ocorrer, nenhum dado será enviado).
- **Instantâneos** – um instantâneo do estado atual de todas as avaliações de conformidade regulatória será enviado a cada semana (essa é uma versão prévia do recurso para instantâneos semanais de classificações de segurança e dados de conformidade regulatória).

Saiba mais sobre as funcionalidades completas desse recurso em [Exportar continuamente os dados da Central de Segurança](continuous-export.md).

## <a name="december-2020"></a>Dezembro de 2020

As atualizações de dezembro incluem:

- [O Azure Defender para SQL Servers em computadores está em disponibilidade geral](#azure-defender-for-sql-servers-on-machines-is-generally-available)
- [O suporte do Azure Defender para SQL para o pool de SQL dedicado do Azure Synapse Analytics está em disponibilidade geral](#azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available)
- [Os administradores globais já podem conceder a si mesmos permissões no nível dos locatários](#global-administrators-can-now-grant-themselves-tenant-level-permissions)
- [Dois novos planos do Azure Defender: Azure Defender para DNS e Azure Defender para Resource Manager (em versão prévia)](#two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview)
- [Nova página de alertas de segurança no portal do Azure (versão prévia)](#new-security-alerts-page-in-the-azure-portal-preview)
- [Experiência revitalizada da Central de Segurança no Banco de Dados SQL do Azure e na Instância Gerenciada de SQL](#revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance)
- [Ferramentas e filtros de inventário de ativos atualizados](#asset-inventory-tools-and-filters-updated)
- [Recomendação sobre aplicativos Web solicitando certificados SSL que não fazem mais parte da classificação de segurança](#recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score)
- [A página de recomendações tem novos filtros para o ambiente, a severidade e as respostas disponíveis](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)
- [A exportação contínua obtém novos tipos de dados e políticas de deployifnotexist aprimoradas](#continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies)


### <a name="azure-defender-for-sql-servers-on-machines-is-generally-available"></a>O Azure Defender para SQL Servers em computadores está em disponibilidade geral

A Central de Segurança do Azure oferece dois planos do Azure Defender para SQL Servers:

- **Azure Defender para servidores do Banco de Dados SQL do Azure** – Defende os seus SQL Servers nativos do Azure 
- **O Azure Defender para servidores SQL em computadores** – Estende as mesmas proteções para os seus servidores SQL em ambientes híbridos, locais e de várias nuvens

Com esse comunicado, **o Azure Defender para SQL** agora protege os seus bancos de dados e os dados neles, independentemente de onde eles estiverem localizados.

O Azure Defender para SQL inclui funcionalidades de avaliação de vulnerabilidade. A ferramenta de avaliação de vulnerabilidade inclui os seguintes recursos avançados:

- A **Configuração de linha de base** (novo!) refina de modo inteligente os resultados das verificações de vulnerabilidade, sinalizando aqueles que podem representar problemas de segurança reais. Depois de estabelecer o estado de segurança de linha de base, a ferramenta de avaliação de vulnerabilidade só relata desvios desse estado de linha de base. Os resultados que correspondem à linha de base são considerados ao passar nas verificações posteriores. Isso permite que você e os analistas concentrem a atenção onde importa.
- As **Informações detalhadas de benchmark** ajudam você a *entender* as descobertas e por que elas se relacionam com os seus recursos.
- Os **Scripts de correção** ajudam você a reduzir os riscos identificados.

Saiba mais sobre o [Azure Defender para SQL](defender-for-sql-introduction.md).


### <a name="azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available"></a>O suporte do Azure Defender para SQL para o pool de SQL dedicado do Azure Synapse Analytics está em disponibilidade geral

O Azure Synapse Analytics (antigo SQL DW) é um serviço de análise que combina o data warehouse corporativo e a análise de Big Data. Os pools de SQL dedicados são os recursos de data warehouse corporativos do Azure Synapse. Saiba mais em [O que é o Azure Synapse Analytics (antigo SQL DW)?](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md).

O Azure Defender para SQL protege os seus pools de SQL dedicados com:

- **Proteção avançada contra ameaças** para detectar ameaças e ataques 
- **Funcionalidades de avaliação de vulnerabilidade** para identificar e corrigir configurações incorretas de segurança

O suporte do Azure Defender para SQL para os pools de SQL do Azure Synapse Analytics é adicionado automaticamente ao pacote de bancos de dados SQL do Azure na Central de Segurança do Azure. Uma nova guia "Azure Defender para SQL" será encontrada na sua página do workspace do Azure Synapse no portal do Azure.

Saiba mais sobre o [Azure Defender para SQL](defender-for-sql-introduction.md).


### <a name="global-administrators-can-now-grant-themselves-tenant-level-permissions"></a>Os administradores globais já podem conceder a si mesmos permissões no nível dos locatários

Um usuário com a função **Administrador global** no Azure Active Directory pode ter responsabilidades em todo o locatário, mas não ter as permissões do Azure para ver essas informações de toda a organização na Central de Segurança do Azure. 

Para atribuir a si mesmo permissões no nível dos locatários, siga as instruções descritas em [Conceder a si mesmo permissões em todo o locatário](tenant-wide-permissions-management.md#grant-tenant-wide-permissions-to-yourself).


### <a name="two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview"></a>Dois novos planos do Azure Defender: Azure Defender para DNS e Azure Defender para Resource Manager (em versão prévia)

Adicionamos duas novas funcionalidades de proteção contra ameaças com abrangência nativa de nuvem para seu ambiente do Azure.

Essas novas proteções aprimoram muito sua resiliência contra ataques de atores de ameaças e aumentam significativamente o número de recursos do Azure protegidos pelo Azure Defender.

- **Azure Defender para Resource Manager**: monitora automaticamente todas as operações de gerenciamento de recursos executadas na sua organização. Para obter mais informações, consulte:
    - [Introdução ao Azure Defender para Resource Manager](defender-for-resource-manager-introduction.md)
    - [Responder aos alertas do Azure Defender para Resource Manager](defender-for-resource-manager-usage.md)
    - [Lista de alertas fornecidos pelo Azure Defender para Resource Manager](alerts-reference.md#alerts-resourcemanager)

- **Azure Defender para DNS**: monitora continuamente todas as consultas DNS dos seus recursos do Azure. Para obter mais informações, consulte:
    - [Introdução ao Azure Defender para DNS](defender-for-dns-introduction.md)
    - [Responder aos alertas do Azure Defender para DNS](defender-for-dns-usage.md)
    - [Lista de alertas fornecidos pelo Azure Defender para DNS](alerts-reference.md#alerts-dns)


### <a name="new-security-alerts-page-in-the-azure-portal-preview"></a>Nova página de alertas de segurança no portal do Azure (versão prévia)

A página de alertas de segurança da Central de Segurança do Azure foi reprojetada para oferecer:

- **Experiência de triagem aprimorada para alertas** –ajudando a reduzir os alertas exaustivos e concentrar-se nas ameaças mais relevantes com mais facilidade, a lista inclui filtros personalizáveis e opções de agrupamento
- **Mais informações na lista de alertas** – como táticas MITRE ATT&ACK
- **Botão para criar alertas de exemplo** – para avaliar os recursos do Azure Defender e testar sua configuração de alertas (para integração do SIEM, notificações por email e automação de fluxo de trabalho), você pode criar alertas de exemplo em todos os planos do Azure Defender
- **Alinhamento com a experiência de incidentes do Azure Sentinel** – para clientes que usam os dois produtos, a troca entre eles agora é uma experiência mais simples, além de ser fácil aprender um do outro
- **Melhores desempenho** para listas grandes de alertas
- **Navegação por teclado** na lista de alertas
- **Alertas do Azure Resource Graph** – você pode consultar alertas no Azure Resource Graph, a API semelhante ao Kusto para todos os seus recursos. Isso também será útil se você estiver criando os próprios painéis de alertas. [Saiba mais sobre Azure Resource Graph](../governance/resource-graph/index.yml).

Para acessar a nova experiência, use o link "Experimente agora" da faixa na parte superior da página de alertas de segurança.

:::image type="content" source="media/security-center-managing-and-responding-alerts/preview-alerts-experience-banner.png" alt-text="Faixa com o link para a nova experiência de versão prévia de alertas":::

Para criar alertas de exemplo na nova experiência de alertas, consulte [Gerar alertas de exemplo do Azure Defender](security-center-alert-validation.md#generate-sample-azure-defender-alerts).


### <a name="revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance"></a>Experiência revitalizada da Central de Segurança no Banco de Dados SQL do Azure e na Instância Gerenciada de SQL 

A experiência da Central de Segurança no SQL fornece acesso aos seguintes recursos da Central de Segurança e do Azure Defender para SQL:

- **Recomendações de segurança** – A Central de Segurança analisa periodicamente o estado de segurança de todos os recursos do Azure conectados para identificar possíveis configurações incorretas na segurança. Em seguida, ela fornece recomendações sobre como corrigir essas vulnerabilidades e aprimorar a postura de segurança das organizações.
- **Alertas de segurança** – Um serviço de detecção que monitora continuamente as atividades do SQL do Azure em busca de ameaças como injeção de SQL, ataques de força bruta e abuso de privilégios. Este serviço dispara alertas de segurança detalhados e orientados a ações na Central de Segurança e fornece opções para continuar as investigações com o Azure Sentinel, a solução SIEM nativa do Microsoft Azure.
- **Descobertas** – Um serviço de avaliação de vulnerabilidade que monitora continuamente as configurações do SQL do Azure e ajuda a corrigir vulnerabilidades. As verificações de avaliação fornecem uma visão geral dos estados de segurança do SQL do Azure junto com as descobertas de segurança detalhadas.     

:::image type="content" source="media/release-notes/azure-security-center-experience-in-sql.png" alt-text="Os recursos de segurança da Central de Segurança do Azure para SQL estão disponíveis no SQL do Azure":::


### <a name="asset-inventory-tools-and-filters-updated"></a>Ferramentas e filtros de inventário de ativos atualizados

A página de inventário na Central de Segurança do Azure foi atualizada com as seguintes alterações:

- **Guias e comentários** adicionados à barra de ferramentas. Isso abre um painel com links para informações e ferramentas relacionadas. 
- **Filtro de assinaturas** adicionado aos filtros padrão disponíveis para seus recursos.
- Link **Abrir consulta** para abrir as opções de filtro atuais como uma consulta do Azure Resource Graph (anteriormente chamada de "Exibir no Resource Graph Explorer").
- **Opções de operador** para cada filtro. Agora você pode escolher entre mais operadores lógicos além de '='. Por exemplo, talvez você queira localizar todos os recursos com recomendações ativas cujos títulos incluem a cadeia de caracteres "encrypt". 

    :::image type="content" source="media/release-notes/inventory-filter-operators.png" alt-text="Controles para a opção de operador nos filtros de inventário de ativos":::

Saiba mais sobre inventário em [Explorar e gerenciar seus recursos com o inventário de ativos](asset-inventory.md).


### <a name="recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score"></a>Recomendação sobre aplicativos Web solicitando certificados SSL que não fazem mais parte da classificação de segurança

A recomendação "Os aplicativos Web devem solicitar um certificado SSL para todas as solicitações de entrada" foi movida do controle de segurança **Gerenciar o acesso e as permissões** (vale no máximo quatro pontos) para **Implementar as melhores práticas de segurança** (que não vale nenhum ponto). 

Garantir que um aplicativo Web solicite um certificado certamente o torna mais seguro. No entanto, para aplicativos Web voltados para o público, é irrelevante. se você acessar seu site por HTTP e não por HTTPS, você não receberá nenhum certificado do cliente. Por isso, se o aplicativo exigir certificados de cliente, você não deverá permitir solicitações ao seu aplicativo via HTTP. Saiba mais em [Como configurar a autenticação mútua TLS para o Serviço de Aplicativo do Azure](../app-service/app-service-web-configure-tls-mutual-auth.md).

Com essa alteração, a recomendação agora é uma prática recomendada que não afetará sua classificação. 

Saiba quais recomendações estão em cada controle de segurança em [Controles de segurança e suas recomendações](secure-score-security-controls.md#security-controls-and-their-recommendations).


### <a name="recommendations-page-has-new-filters-for-environment-severity-and-available-responses"></a>A página de recomendações tem novos filtros para o ambiente, a severidade e as respostas disponíveis

A Central de Segurança do Azure monitora todos os recursos conectados e gera recomendações de segurança. Use essas recomendações para fortalecer sua postura de nuvem híbrida e acompanhar a conformidade com as políticas e os padrões relevantes para sua organização, setor e país/região.

À medida que a Central de Segurança continua a expandir sua cobertura e seus recursos, a lista de recomendações de segurança cresce todos os meses. Por exemplo, confira [29 recomendações de versão prévia adicionadas para aumentar a cobertura do Azure Security Benchmark](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark).

Com a lista cada vez maior, há a necessidade de filtrar as recomendações para encontrar as de maior interesse. Em novembro, adicionamos filtros à página de recomendações (confira [A lista de recomendações agora inclui filtros](#recommendations-list-now-includes-filters)).

Os filtros adicionados neste mês fornecem opções para refinar a lista de recomendações de acordo com:

- **Ambiente** – Exibir recomendações para seus recursos AWS, GCP ou Azure (ou qualquer combinação)
- **Severidade** – Exibir recomendações de acordo com a classificação de severidade definida pela Central de Segurança
- **Ações de resposta** – Exibir recomendações de acordo com a disponibilidade das opções de resposta da Central de Segurança: Correção rápida, negar e impor

    > [!TIP]
    > O filtro de ações de resposta substitui o filtro de **correção rápida disponível (Sim/Não)** . 
    > 
    > Saiba mais sobre cada uma dessas opções de resposta:
    > - [Correção rápida](security-center-remediate-recommendations.md#quick-fix-remediation)
    > - [Impedir configurações incorretas com as recomendações de Impor/Negar](prevent-misconfigurations.md)

:::image type="content" source="./media/release-notes/added-recommendations-filters.png" alt-text="Recomendações agrupadas por controle de segurança" lightbox="./media/release-notes/added-recommendations-filters.png":::

### <a name="continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies"></a>A exportação contínua obtém novos tipos de dados e políticas de deployifnotexist aprimoradas

As ferramentas de exportação contínua da Central de Segurança do Azure permitem exportar recomendações e alertas da Central de Segurança para uso com outras ferramentas de monitoramento em seu ambiente.

A exportação contínua permite que você personalize totalmente o que será exportado e o local da exportação. Para obter detalhes completos, confira [Exportar continuamente dados da Central de Segurança](continuous-export.md).

Essas ferramentas foram aprimoradas e expandidas das seguintes maneiras:

- **Aprimoramento das políticas deployifnotexist da exportação contínua**. As políticas agora:

    - **Verificam se a configuração está habilitada.** Se não estiver, a política será mostrada como não compatível e criará um recurso compatível. Saiba mais sobre os modelos do Azure Policy fornecidos na guia "Implantar em escala usando o Azure Policy" da opção [Configurar uma exportação contínua](continuous-export.md#set-up-a-continuous-export).

    - **Dão suporte à exportação de descobertas de segurança.** Ao usar os modelos do Azure Policy, você pode configurar sua exportação contínua para incluir descobertas. Isso é relevante ao exportar recomendações com "sub" recomendações, como as descobertas de verificações de avaliação de vulnerabilidade ou atualizações de sistema específicas para a recomendação "pai" "As atualizações do sistema devem ser instaladas em seus computadores".
    
    - **Dão suporte à exportação de dados da classificação de segurança.**

- **Dados de avaliação de conformidade regulatória adicionados (em versão prévia).** Agora você pode exportar atualizações continuamente para avaliações de conformidade regulatória, incluindo para qualquer iniciativa personalizada, para um workspace do Log Analytics ou hub de eventos. Este recurso não está disponível em nuvens nacionais/soberanas.

    :::image type="content" source="media/release-notes/continuous-export-regulatory-compliance-option.png" alt-text="As opções para incluir informações de avaliação de conformidade regulatória com seus dados de exportação contínua.":::


## <a name="november-2020"></a>Novembro de 2020

As atualizações de novembro incluem:

- [29 recomendações de versão prévia adicionadas para aumentar a cobertura do Parâmetro de Comparação de Segurança do Azure](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [NIST SP 800 171 R2 adicionado ao painel de conformidade regulatória da Central de Segurança](#nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard)
- [A lista de recomendações agora inclui filtros](#recommendations-list-now-includes-filters)
- [Experiência de provisionamento automático aprimorada e expandida](#auto-provisioning-experience-improved-and-expanded)
- [A classificação de segurança já está disponível na exportação contínua (versão prévia)](#secure-score-is-now-available-in-continuous-export-preview)
- [A recomendação "Atualizações do sistema deverão ser instaladas em seus computadores" agora inclui sub-recomendações](#system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations)
- [A página de gerenciamento de política no portal do Azure agora mostra o status das atribuições de política padrão](#policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments)

### <a name="29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>29 recomendações de versão prévia adicionadas para aumentar a cobertura do Parâmetro de Comparação de Segurança do Azure

O Azure Security Benchmark é um conjunto específico de diretrizes específicas do Azure criadas pela Microsoft de melhores práticas de segurança e conformidade baseadas em estruturas de conformidade comuns. [Saiba mais sobre o Azure Security Benchmark](../security/benchmarks/introduction.md).

As 29 recomendações de versão prévia a seguir foram adicionadas à Central de Segurança para aumentar a cobertura desse parâmetro de comparação.

As recomendações de versão prévia não processam um recurso não íntegro e não são incluídas nos cálculos de sua classificação de segurança. Corrija-as sempre que possível, para que, quando o período da versão prévia terminar, elas contribuam para sua classificação. Saiba mais sobre como responder a essas recomendações em [Recomendações de correção na Central de Segurança do Azure](security-center-remediate-recommendations.md).

| Controle de segurança                     | Novas recomendações                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Criptografar dados em trânsito              | – Impor conexão SSL deve ser habilitado para servidores de banco de dados PostgreSQL<br>– Impor conexão SSL deve ser habilitado para servidores de banco de dados MySQL<br>– O TLS deve estar atualizado com a última versão para o aplicativo de API<br>– O TLS deve estar atualizado com a última versão para o aplicativo de funções<br>– O TLS deve estar atualizado com a última versão para o aplicativo Web<br>– O FTPS deve ser exigido no aplicativo de API<br>– O FTPS deve ser exigido no aplicativo de funções<br>– O FTPS deve ser exigido no aplicativo Web                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Gerenciar acesso e permissões        | – Os aplicativos Web devem solicitar um certificado SSL para todas as solicitações de entrada<br>– A identidade gerenciada deve ser usada no aplicativo de API<br>– A identidade gerenciada deve ser usada no aplicativo de funções<br>– A identidade gerenciada deve ser usada no aplicativo Web                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Restringir acesso não autorizado à rede | – O ponto de extremidade privado deve ser habilitado para servidores PostgreSQL<br>– O ponto de extremidade privado deve ser habilitado para servidores MariaDB<br>– O ponto de extremidade privado deve ser habilitado para servidores MySQL                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| – Habilitar a auditoria e o registro em log          | – Os logs de diagnóstico devem ser habilitados nos Serviços de Aplicativo                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| Implementar melhores práticas de segurança    | – O Backup do Azure deve ser habilitado para máquinas virtuais<br>– O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para MariaDB<br>– O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para MySQL<br>– O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para PostgreSQL<br>– O PHP deve estar atualizado com a última versão para o aplicativo de API<br>– O PHP deve estar atualizado com a última versão para o aplicativo Web<br>– O Java deve estar atualizado com a última versão para o aplicativo de API<br>– O Java deve estar atualizado com a última versão para o aplicativo de funções<br>– O Java deve estar atualizado com a última versão para o aplicativo Web<br>– O Python deve estar atualizado com a última versão para o aplicativo de API<br>– O Python deve estar atualizado com a última versão para o aplicativo de funções<br>– O Python deve estar atualizado com a última versão para o aplicativo Web<br>– A retenção de auditoria para servidores SQL deve ser definida como pelo menos 90 dias |
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Links relacionados:

- [Saiba mais sobre o Azure Security Benchmark](../security/benchmarks/introduction.md)
- [Saiba mais sobre aplicativos de API do Azure](../app-service/app-service-web-tutorial-rest-api.md)
- [Saiba mais sobre aplicativos de funções do Azure](../azure-functions/functions-overview.md)
- [Saiba mais sobre aplicativos Web do Azure](../app-service/overview.md)
- [Saiba mais sobre o Banco de Dados do Azure para MariaDB](../mariadb/overview.md)
- [Saiba mais sobre o Banco de Dados do Azure para MySQL](../mysql/overview.md)
- [Saiba mais sobre o Banco de Dados do Azure para PostgreSQL](../postgresql/overview.md)


### <a name="nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard"></a>NIST SP 800 171 R2 adicionado ao painel de conformidade regulatória da Central de Segurança

O padrão NIST SP 800-171 R2 já está disponível como uma iniciativa interna para uso com o painel de conformidade regulatória da Central de Segurança do Azure. Os mapeamentos para os controles são descritos em [Detalhes da iniciativa interna de Conformidade Regulatória do NIST SP 800-171 R2](../governance/policy/samples/nist-sp-800-171-r2.md). 

Para aplicar o padrão às suas assinaturas e monitorar continuamente o status de conformidade, use as instruções presentes em [Personalizar o conjunto de padrões em seu painel de conformidade regulatória](update-regulatory-compliance-packages.md).

:::image type="content" source="media/release-notes/nist-sp-800-171-r2-standard.png" alt-text="O padrão NIST SP 800 171 R2 no painel de conformidade regulatória da Central de Segurança":::

Para obter mais informações sobre esse padrão de conformidade, confira [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final).


### <a name="recommendations-list-now-includes-filters"></a>A lista de recomendações agora inclui filtros

Agora você pode filtrar a lista de recomendações de segurança com base em uma gama de critérios. No seguinte exemplo, a lista de recomendações foi filtrada para mostrar recomendações que:

- estão em **disponibilidade geral** (ou seja, não em versão prévia)
- são voltadas para **contas de armazenamento**
- têm suporte para **correção rápida**

:::image type="content" source="media/release-notes/recommendations-filters.png" alt-text="Filtros para a lista de recomendações":::


### <a name="auto-provisioning-experience-improved-and-expanded"></a>Experiência de provisionamento automático aprimorada e expandida

O recurso de provisionamento automático ajuda a reduzir a sobrecarga de gerenciamento instalando as extensões necessárias em VMs novas e existentes do Azure, a fim de que elas possam se beneficiar das proteções da Central de Segurança. 

À medida que a Central de Segurança do Azure cresce, mais extensões são desenvolvidas e é possível monitorar uma lista maior de tipos de recursos. As ferramentas de provisionamento automático já foram expandidas para dar suporte a outras extensões e tipos de recursos aproveitando as funcionalidades do Azure Policy.

Agora você pode configurar o provisionamento automático de:

- Agente do Log Analytics
- (Novo) Complemento do Azure Policy para Kubernetes
- (Novo) Microsoft Dependency Agent

Saiba mais em [Agentes e extensões de provisionamento automático da Central de Segurança do Azure](security-center-enable-data-collection.md).


### <a name="secure-score-is-now-available-in-continuous-export-preview"></a>A classificação de segurança já está disponível na exportação contínua (versão prévia)

Com a exportação contínua da classificação de segurança, você pode transmitir alterações à sua pontuação em tempo real para os Hubs de Eventos do Azure ou um workspace do Log Analytics. Use essa funcionalidade para:

- acompanhar sua classificação de segurança ao longo do tempo com relatórios dinâmicos
- exportar dados de classificação de segurança para o Azure Sentinel (ou qualquer outro SIEM)
- integrar esses dados a qualquer processo que você já esteja usando para monitorar a classificação de segurança em sua organização

Saiba mais sobre como [Exportar continuamente dados da Central de Segurança](continuous-export.md).


### <a name="system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations"></a>A recomendação "Atualizações do sistema deverão ser instaladas em seus computadores" agora inclui sub-recomendações

A recomendação **As atualizações do sistema devem ser instaladas nos computadores** foi aprimorada. A nova versão inclui sub-recomendações para cada atualização ausente e fornece os seguintes aprimoramentos:

- Uma experiência reformulada nas páginas da Central de Segurança do Azure do portal do Azure. A página de detalhes da recomendação para **As atualizações do sistema devem ser instaladas nos computadores** inclui a lista de conclusões, conforme mostrado abaixo. Quando você seleciona uma localização individual, o painel de detalhes é aberto com um link para as informações de correção e uma lista de recursos afetados.

    :::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="Como abrir uma das sub-recomendações na experiência de portal para obter uma recomendação atualizada":::

- Dados aprimorados para a recomendação do ARG (Azure Resource Graph). O ARG é um serviço do Azure que foi projetado para oferecer uma exploração eficiente de recursos. Você pode usar o ARG para consultar em escala um determinado conjunto de assinaturas a fim de controlar seu ambiente de maneira eficaz. 

    Para a Central de Segurança do Azure, você pode usar ARG e [KQL (Kusto Query Language)](/azure/data-explorer/kusto/query/) para consultar uma ampla gama de dados de postura de segurança.

    Anteriormente, se você consultasse essa recomendação no ARG, a única informação disponível seria que a recomendação precisava ser corrigida em um computador. A consulta a seguir da versão aprimorada retornará as atualizações de cada sistema ausente agrupadas por computador.

    ```kusto
    securityresources
    | where type =~ "microsoft.security/assessments/subassessments"
    | where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
    | where properties.status.code == "Unhealthy"
    ```

### <a name="policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments"></a>A página de gerenciamento de política no portal do Azure agora mostra o status das atribuições de política padrão

Agora você pode ver se as suas assinaturas têm ou não a política padrão da Central de Segurança atribuída, na página da **política de segurança** da Central de Segurança do portal do Azure.

:::image type="content" source="media/release-notes/policy-assignment-info-per-subscription.png" alt-text="Página de gerenciamento de política da Central de Segurança do Azure mostrando as atribuições de política padrão":::
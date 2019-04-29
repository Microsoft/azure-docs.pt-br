---
title: Otimizar seu ambiente do Active Directory com o Azure Monitor | Microsoft Docs
description: É possível usar a solução Verificação de Integridade do Active Directory para avaliar o risco e a integridade de seus ambientes em intervalos regulares.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 81eb41b8-eb62-4eb2-9f7b-fde5c89c9b47
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/27/2017
ms.author: magoedte
ms.openlocfilehash: 3b5da6c9046fc694bd5eb0f55cf031b82b6d0103
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60919732"
---
# <a name="optimize-your-active-directory-environment-with-the-active-directory-health-check-solution-in-azure-monitor"></a>Otimizar seu ambiente do Active Directory com a solução de Verificação de Integridade do Active Directory no Azure Monitor

![Símbolo da Verificação da Integridade do AD](./media/ad-assessment/ad-assessment-symbol.png)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

É possível usar a solução Verificação de Integridade do Active Directory para avaliar o risco e a integridade de seus ambientes de servidor em intervalos regulares. Este artigo ajuda você a instalar e usar a solução para que possa tomar ações corretivas para os problemas em potencial.

Esta solução fornece uma lista priorizada de recomendações específicas para sua infraestrutura de servidor implantado. As recomendações são categorizadas em quatro áreas de foco que vão ajudá-lo a entender o risco e tomar uma ação.

As recomendações baseiam-se no conhecimento e na experiência adquirida pelos engenheiros da Microsoft em milhares de visitas a clientes. Cada recomendação fornece diretrizes sobre por que um problema pode ser relevante para você e como implementar as alterações sugeridas.

Você pode escolher as áreas de foco que são mais importantes para sua organização e acompanhar seu progresso no sentido de executar um ambiente íntegro e sem riscos.

Após a adição da solução e a conclusão da verificação, as informações resumidas para as áreas de foco são mostradas no painel **Verificação da Integridade do AD** para a infraestrutura em seu ambiente. As seções a seguir descrevem como usar as informações no painel **Verificação de Integridade do AD**, no qual é possível exibir e executar as ações recomendadas para sua infraestrutura de servidor do Active Directory.  

![imagem do bloco da Verificação da Integridade do AD](./media/ad-assessment/ad-healthcheck-summary-tile.png)

![imagem do painel da Verificação da Integridade do AD](./media/ad-assessment/ad-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Pré-requisitos

* A solução de Verificação de Integridade do Active Directory requer uma versão com suporte do .NET Framework 4.5.2 ou superior instalada em cada computador com o MMA (Microsoft Monitoring Agent) instalado.  O agente MMA é usado pelo System Center 2016 – Operations Manager, pelo Operations Manager 2012 R2 e pelo Azure Monitor.
* A solução oferece suporte a controladores de domínio que executam o Windows Server 2008 e 2008 R2, o Windows Server 2012 e 2012 R2 e o Windows Server 2016.
* Um espaço de trabalho do Log Analytics para adicionar a solução de Verificação de Integridade do Active Directory no Azure marketplace, no Portal do Azure.  Não é necessária nenhuma configuração.

  > [!NOTE]
  > Depois de adicionar a solução, o arquivo AdvisorAssessment.exe é adicionado aos servidores com agentes. Os dados de configuração são lidos e, em seguida, enviados para o Azure Monitor na nuvem para processamento. A lógica é aplicada aos dados recebidos e o serviço de nuvem registra os dados.
  >
  >

Para executar a verificação de integridade nos controladores de domínio membros do domínio que será avaliado, eles precisam de um agente e de conectividade com o Azure Monitor usando um dos seguintes métodos com suporte:

1. Instale o [MMA (Microsoft Monitoring Agent)](../../azure-monitor/platform/agent-windows.md) se o controlador de domínio ainda não tiver sido monitorado pelo System Center 2016 - Operations Manager ou Operations Manager 2012 R2.
2. Se for monitorado com o System Center 2016 – Operations Manager ou com o Operations Manager 2012 R2 e o grupo de gerenciamento não for integrado com o Azure Monitor, o controlador de domínio poderá ter hospedagem múltipla com o Azure Monitor para coletar dados, encaminhar para o serviço e ainda ser monitorado pelo Operations Manager.  
3. Caso contrário, se seu grupo de gerenciamento Operations Manager for integrado com o serviço, você precisará adicionar controladores de domínio para a coleção de dados pelo serviço seguindo as etapas em [adicionar computadores gerenciados por agente](../../azure-monitor/platform/om-agents.md#connecting-operations-manager-to-azure-monitor) depois de habilitar a solução em seu workspace.  

O agente em seu controlador de domínio que informa um grupo de gerenciamento Operations Manager, coleta os dados, encaminha para o servidor de gerenciamento atribuído e é enviado diretamente de um servidor de gerenciamento para o Azure Monitor.  Os dados não são gravados nos bancos de dados do Operations Manager.  

## <a name="active-directory-health-check-data-collection-details"></a>Detalhes de coleta de dados da Verificação de Integridade do Active Directory

A Verificação de Integridade do Active Directory coleta dados das seguintes fontes usando o agente habilitado:

- Registro
- LDAP
- .NET Framework
- Log de eventos
- ADSI (Interfaces de Serviço do Active Directory)
- Windows PowerShell
- Dados de arquivo
- WMI (Instrumentação de Gerenciamento do Windows)
- API de ferramentas DCDIAG
- API do NTFRS (Serviço de Replicação de Arquivos)
- Código em C# personalizado

Os dados são coletados no controlador de domínio e encaminhados ao Azure Monitor a cada sete dias.  

## <a name="understanding-how-recommendations-are-prioritized"></a>Compreendendo como as recomendações são priorizadas
Cada recomendação feita recebe um valor de ponderação que identifica a importância relativa da recomendação. Somente as 10 recomendações mais importantes são mostradas.

### <a name="how-weights-are-calculated"></a>Como os pesos são calculados
Os pesos são valores agregados com base em três fatores principais:

* A *probabilidade* de que um problema identificado cause problemas. Uma probabilidade mais alta é igual a uma pontuação geral maior para a recomendação.
* O *impacto* da questão na sua organização se ela causar um problema. Um impacto maior é igual a uma pontuação geral maior para a recomendação.
* O *esforço* necessário para implementar a recomendação. Um esforço maior é igual a uma pontuação geral menor para a recomendação.

A importância de cada recomendação é expressa como um percentual da pontuação total disponível para todas as áreas de foco. Por exemplo, se uma recomendação na área de foco de segurança e conformidade tiver uma pontuação de 5%, implementar essa recomendação aumentará sua pontuação geral de segurança e conformidade em 5%.

### <a name="focus-areas"></a>Áreas de foco
**Segurança e conformidade** - essa área de foco mostra recomendações para possíveis ameaças de segurança e violações, políticas corporativas, bem como os requisitos de conformidade técnica, legal e regulatória.

**Disponibilidade e continuidade dos negócios** - essa área de foco mostra as recomendações para a disponibilidade de serviço, resiliência de sua infraestrutura e proteção dos negócios.

**Desempenho e escalabilidade** - essa área de foco mostra recomendações para ajudar a expansão da infraestrutura de TI de sua organização, garantir que seu ambiente de TI atende aos requisitos de desempenho atuais e estar apta a responder às necessidades de infraestrutura em constante mudança.

**Atualização, implantação e migração** - Essa área de foco mostra recomendações para ajudá-lo a atualizar, migrar e implantar o Active Directory em sua infraestrutura existente.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Você deve visar à pontuação de 100% em cada área de foco?
Não necessariamente. As recomendações baseiam-se no conhecimento e nas experiências adquiridas pelos engenheiros da Microsoft em milhares de visitas a clientes. No entanto, nenhuma infraestrutura de servidor é igual à outra, assim, recomendações específicas podem ser mais ou menos relevantes para você. Por exemplo, algumas recomendações de segurança poderão ser menos relevantes se as máquinas virtuais não estiverem expostas à Internet. Algumas recomendações de disponibilidade podem ser menos relevantes para os serviços que fornecem relatórios e coleta de dados ad hoc de baixa prioridade. Problemas importantes para empresas maduras podem ser menos importantes para uma empresa start-up. Você pode preferir identificar quais áreas de foco são suas prioridades e depois examinar como suas pontuações mudam ao longo do tempo.

Cada recomendação inclui diretrizes sobre sua importância. Você deve usar essas diretrizes para avaliar se é adequado implementar a recomendação considerando a natureza de seus serviços de TI e as necessidades comerciais da sua organização.

## <a name="use-health-check-focus-area-recommendations"></a>Usar as recomendações da área de foco da Verificação da Integridade
Após a instalação, é possível exibir o resumo das recomendações usando o bloco Verificação de Integridade do Active Directory na página de solução no Portal do Azure.

Veja as avaliações de conformidade resumidas para sua infraestrutura e faça uma busca detalhada das recomendações.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Para exibir as recomendações para uma área de foco e tomar uma ação corretiva
[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

1. Na página **Visão Geral**, clique no bloco **Verificação de Integridade do Active Directory**.
1. Na página **Verificação da Integridade**, revise as informações resumidas em uma das folhas da área de foco e clique em uma para exibir as recomendações dessa área de foco.
1. Em qualquer uma das páginas da área de foco, você pode exibir as recomendações priorizadas para seu ambiente. Clique em uma recomendação sob **Objetos Afetados** para exibir detalhes sobre o motivo pelo qual a recomendação foi feita.<br><br> ![imagem das recomendações da Verificação da Integridade](./media/ad-assessment/ad-healthcheck-dashboard-02.png)
1. É possível executar as ações corretivas sugeridas em **Ações Sugeridas**. Quando o item tiver sido resolvido, avaliações posteriores gravarão que essas ações recomendadas foram executadas e sua pontuação de conformidade aumentará. Os itens corrigido aparecem como **Objetos Passados**.

## <a name="ignore-recommendations"></a>Ignorar as recomendações
Se houver recomendações que deseja ignorar, você poderá criar um arquivo de texto que será usado pelo Azure Monitor para impedir que as recomendações sejam exibidas nos resultados da avaliação.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Para identificar as recomendações que serão ignoradas
[!INCLUDE [azure-monitor-log-queries](../../../includes/azure-monitor-log-queries.md)]

Use a consulta a seguir para listar as recomendações que falharam para os computadores em seu ambiente.

```
ADAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
```

Aqui está uma captura de tela mostrando a consulta de log:<br><br> ![recomendações com falha](media/ad-assessment/ad-failed-recommendations.png)

Escolha as recomendações que você deseja ignorar. Você usará os valores para RecommendationId no próximo procedimento.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Criar e usar um arquivo de texto IgnoreRecommendations.txt
1. Crie um arquivo chamado IgnoreRecommendations.txt.
2. Cole ou digite a RecommendationId de cada recomendação que você deseja que o Azure Monitor ignore em uma linha separada e, em seguida, salve e feche o arquivo.
3. Coloque o arquivo na pasta a seguir em cada computador no qual você deseja que o Azure Monitor ignore as recomendações.
   * Em computadores com o Microsoft Monitoring Agent (conectado diretamente ou por meio do Operations Manager) - *SystemDrive*:\Arquivos de Programas\Microsoft Monitoring Agent\Agent
   * No servidor de gerenciamento do Operations Manager 2012 R2 - *SystemDrive*:\Arquivos de Programa\Microsoft System Center 2012 R2\Operations Manager\Server
   * No servidor de gerenciamento do Operations Manager 2016 - *SystemDrive*:\Program Files\Microsoft System Center 2016\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Para verificar se as recomendações são ignoradas
Após a execução da próxima verificação de integridade agendada, por padrão, a cada sete dias, as recomendações especificadas são marcadas como *Ignoradas* e não aparecerão no painel.

1. É possível usar as consultas de log a seguir para listar todas as recomendações ignoradas.

    ```
    ADAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```

2. Se você decidir posteriormente que deseja ver as recomendações ignoradas, remova todos os arquivos IgnoreRecommendations.txt ou remova as RecommendationIDs deles.

## <a name="ad-health-check-solutions-faq"></a>Perguntas frequentes sobre a solução de Verificação da Integridade do AD
*Quantas vezes uma verificação da integridade é executada?*

* A verificação é executada a cada sete dias.

*Há uma maneira de configurar a frequência com a qual a verificação de integridade é executada?*

* Não no momento.

*Se outro servidor for descoberto após eu ter adicionado a solução de verificação de integridade, ele será verificado?*

* Sim, assim que for descoberto, ele será verificado a partir de então a cada sete dias.

*Se um servidor for encerrado, quando ele será removido da verificação de integridade?*

* Se um servidor não enviar dados por três semanas, ele será removido.

*Qual é o nome do processo que faz a coleta de dados?*

* AdvisorAssessment.exe

*Quanto tempo leva para os dados serem coletados?*

* A coleta de dados real no servidor leva aproximadamente 1 hora. Pode levar mais tempo em servidores que têm um grande número de servidores do Active Directory.

*Há uma maneira de configurar quando os dados são coletados?*

* Não no momento.

*Por que apenas as 10 principais recomendações são exibidas?*

* Em vez de apresentar uma lista exaustiva de tarefas, é recomendável que você se concentre em tratar as recomendações priorizadas primeiro. Depois de tratar dessas recomendações, recomendações adicionais serão disponibilizadas. Se preferir ver a lista detalhada, você poderá ver todas as recomendações usando uma consulta de log.

*É possível ignorar uma recomendação?*

* Sim, confira a seção [Ignorar recomendações](#ignore-recommendations) acima.

## <a name="next-steps"></a>Próximas etapas
* Use as [Consultas de log do Azure Monitor](../log-query/log-query-overview.md) para saber como analisar dados detalhados da Verificação de Integridade do AD, bem como as recomendações.

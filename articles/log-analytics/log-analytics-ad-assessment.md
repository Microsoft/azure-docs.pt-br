---
title: Otimizar seu ambiente do Active Directory com o Azure Log Analytics | Microsoft Docs
description: "É possível usar a solução Avaliação do Active Directory para avaliar o risco e a integridade de seus ambientes de servidor em intervalos regulares."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 81eb41b8-eb62-4eb2-9f7b-fde5c89c9b47
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 97368f0b9e89ffd0cd982b6e8670d5a1f62ad42c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="optimize-your-active-directory-environment-with-the-active-directory-assessment-solution-in-log-analytics"></a>Otimizar seu ambiente do Active Directory com a solução de Avaliação do Active Directory no Log Analytics

![Símbolo da Avaliação do AD](./media/log-analytics-ad-assessment/ad-assessment-symbol.png)

É possível usar a solução Avaliação do Active Directory para avaliar o risco e a integridade de seus ambientes de servidor em intervalos regulares. Este artigo ajuda você a instalar e usar a solução para que possa tomar ações corretivas para os problemas em potencial.

Esta solução fornece uma lista priorizada de recomendações específicas para sua infraestrutura de servidor implantado. As recomendações são categorizadas em quatro áreas de foco que vão ajudá-lo a entender o risco e tomar uma ação.

As recomendações baseiam-se no conhecimento e na experiência adquirida pelos engenheiros da Microsoft em milhares de visitas a clientes. Cada recomendação fornece diretrizes sobre por que um problema pode ser relevante para você e como implementar as alterações sugeridas.

Você pode escolher as áreas de foco que são mais importantes para sua organização e acompanhar seu progresso no sentido de executar um ambiente íntegro e sem riscos.

Depois de adicionar a solução e uma avaliação for concluída, as informações resumidas para áreas de foco serão mostradas no painel **Avaliação do AD** para a infraestrutura em seu ambiente. As seções a seguir descrevem como usar as informações no painel **Avaliação do AD** , no qual é possível exibir e executar as ações recomendadas para sua infraestrutura de servidor do Active Directory.

![imagem do bloco de Avaliação do SQL](./media/log-analytics-ad-assessment/ad-tile.png)

![imagem do painel de avaliação do SQL](./media/log-analytics-ad-assessment/ad-assessment.png)

## <a name="installing-and-configuring-the-solution"></a>Instalando e configurando a solução
Use as informações a seguir para instalar e configurar as soluções.

* É preciso que agentes sejam instalados em controladores de domínio membros do domínio a ser avaliado.
* A solução de Avaliação do Active Directory requer uma versão do .NET Framework 4 com suporte (4.5.2 ou superior) instalada em cada computador que tem um agente OMS.
* Adicione a solução de Avaliação do Active Directory ao seu espaço de trabalho do OMS do [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ADAssessmentOMS?tab=Overview) ou usando o processo descrito em [Adicionar soluções do Log Analytics por meio da Galeria de Soluções](log-analytics-add-solutions.md).  Não é necessária nenhuma configuração.

  > [!NOTE]
  > Depois de adicionar a solução, o arquivo AdvisorAssessment.exe é adicionado aos servidores com agentes. Os dados de configuração são lidos e, em seguida, enviados para o serviço OMS na nuvem para processamento. A lógica é aplicada aos dados recebidos e o serviço de nuvem registra os dados.
  >
  >

## <a name="active-directory-assessment-data-collection-details"></a>Detalhes de coleta de dados da Avaliação do Active Directory

A Avaliação do Active Directory coleta dados das seguintes fontes usando os agentes que você tiver habilitado:

- Coletores de registro
- Coletores de LDAP
- .NET Framework
- Coletores de log de eventos
- ADSI (Interfaces de Serviço do Active Directory)
- Windows PowerShell
- Coletores de dados de arquivo
- WMI (Instrumentação de Gerenciamento do Windows)
- API de ferramentas DCDIAG
- API do NTFRS (Serviço de Replicação de Arquivos)
- Código em C# personalizado


A tabela a seguir mostra os métodos de coleta de dados dos agentes, se o SCOM (Operations Manager) é necessário e com que frequência os dados são coletados por um agente.

| plataforma | Agente direto | Agente SCOM | Armazenamento do Azure | SCOM necessário? | Os dados do agente SCOM enviados por meio do grupo de gerenciamento | frequência de coleta |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |&#8226; |&#8226; |  |  |&#8226; |7 dias |

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

## <a name="use-assessment-focus-area-recommendations"></a>Use as recomendações de área de foco de avaliação
Antes de usar uma solução de avaliação no OMS, é necessário ter a solução instalada. Para ler mais sobre como instalar as soluções, confira [Adicionar soluções do Log Analytics por meio da Galeria de Soluções](log-analytics-add-solutions.md). Após a instalação, é possível exibir o resumo das recomendações usando o bloco Avaliação na página Visão geral do OMS.

Veja as avaliações de conformidade resumidas para sua infraestrutura e faça uma busca detalhada das recomendações.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Para exibir as recomendações para uma área de foco e tomar uma ação corretiva
1. Na página **Visão Geral**, clique no bloco **Avaliação** para sua infraestrutura de servidor.
2. Na página **Avaliação** , revise as informações resumidas em uma das folhas da área de foco e clique em uma para exibir as recomendações para a área de foco.
3. Em qualquer uma das páginas da área de foco, você pode exibir as recomendações priorizadas para seu ambiente. Clique em uma recomendação sob **Objetos Afetados** para exibir detalhes sobre o motivo pelo qual a recomendação foi feita.  
    ![imagem das recomendações da Avaliação](./media/log-analytics-ad-assessment/ad-focus.png)
4. É possível executar as ações corretivas sugeridas em **Ações Sugeridas**. Quando o item tiver sido resolvido, avaliações posteriores gravarão que essas ações recomendadas foram executadas e sua pontuação de conformidade aumentará. Os itens corrigido aparecem como **Objetos Passados**.

## <a name="ignore-recommendations"></a>Ignorar as recomendações
Se houver recomendações que deseja ignorar, você poderá criar um arquivo de texto que será usado pelo OMS para impedir que as recomendações sejam exibidas nos resultados da avaliação.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Para identificar as recomendações que serão ignoradas
1. Entre em seu espaço de trabalho e abra a Pesquisa de Log. Use a consulta a seguir para listar as recomendações que falharam para os computadores em seu ambiente.

   ```
   Type=ADAssessmentRecommendation RecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
   ```
>[!NOTE]
> Se o seu espaço de trabalho fosse atualizado para a [nova linguagem de consulta do Log Analytics](log-analytics-log-search-upgrade.md), a consulta acima seria alterada para o demonstrado a seguir.
>
> `ADAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

   Esta é uma captura de tela mostrando a consulta da Pesquisa de Log: ![recomendações com falha](./media/log-analytics-ad-assessment/ad-failed-recommendations.png)
2. Escolha as recomendações que você deseja ignorar. Você usará os valores para RecommendationId no próximo procedimento.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Criar e usar um arquivo de texto IgnoreRecommendations.txt
1. Crie um arquivo chamado IgnoreRecommendations.txt.
2. Cole ou digite a RecommendationId de cada recomendação que você deseja que o Log Analytics ignore em uma linha separada e, em seguida, salve e feche o arquivo.
3. Coloque o arquivo na pasta a seguir em cada computador no qual você deseja que o OMS ignore as recomendações.
   * Em computadores com o Microsoft Monitoring Agent (conectado diretamente ou por meio do Operations Manager) - *SystemDrive*:\Arquivos de Programas\Microsoft Monitoring Agent\Agent
   * No servidor de gerenciamento do Operations Manager - *SystemDrive*:\Arquivos de Programa\Microsoft System Center 2012 R2\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Para verificar se as recomendações são ignoradas
Após a execução da próxima avaliação agendada, por padrão a cada 7 dias, as recomendações especificadas são marcadas como *Ignoradas* e não aparecerão no painel de avaliação.

1. É possível usar as consultas da Pesquisa de Log a seguir para listar todas as recomendações ignoradas.

    ```
    Type=ADAssessmentRecommendation RecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```
>[!NOTE]
> Se o seu espaço de trabalho fosse atualizado para a [nova linguagem de consulta do Log Analytics](log-analytics-log-search-upgrade.md), a consulta acima seria alterada para o demonstrado a seguir.
>
> `ADAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

2. Se você decidir posteriormente que deseja ver as recomendações ignoradas, remova todos os arquivos IgnoreRecommendations.txt ou remova as RecommendationIDs deles.

## <a name="ad-assessment-solutions-faq"></a>Perguntas frequentes sobre soluções da Avaliação do AD
*Com que frequência uma avaliação é executada?*

* A avaliação é executada a cada sete dias.

*Há uma maneira de configurar a frequência com que a avaliação é executada?*

* Não no momento.

*Se outro servidor for descoberto depois que eu adicionar uma solução de avaliação, ele será avaliado?*

* Sim, assim que for descoberto, ele é avaliado a partir de então a cada sete dias.

*Se um servidor for encerrado, quando ele será removido da avaliação?*

* Se um servidor não enviar dados por três semanas, ele será removido.

*Qual é o nome do processo que faz a coleta de dados?*

* AdvisorAssessment.exe

*Quanto tempo leva para os dados serem coletados?*

* A coleta de dados real no servidor leva aproximadamente 1 hora. Pode levar mais tempo em servidores que têm um grande número de servidores do Active Directory.

*Há uma maneira de configurar quando os dados são coletados?*

* Não no momento.

*Por que apenas as 10 principais recomendações são exibidas?*

* Em vez de apresentar uma lista exaustiva de tarefas, é recomendável que você se concentre em tratar as recomendações priorizadas primeiro. Depois de tratar dessas recomendações, recomendações adicionais serão disponibilizadas. Se você preferir ver a lista detalhada, poderá ver todas as recomendações usando a Pesquisa de Log.

*É possível ignorar uma recomendação?*

* Sim, confira a seção [Ignorar recomendações](#ignore-recommendations) acima.

## <a name="next-steps"></a>Próximas etapas
* Use [Pesquisas de log no Log Analytics](log-analytics-log-searches.md) para exibir dados detalhados e recomendações da Avaliação do AD.

---
title: Crie regras de análise personalizadas para detectar ameaças com o Azure Sentinel | Microsoft Docs
description: Use este tutorial para aprender a criar regras de análise personalizadas para detectar ameaças à segurança com o Azure Sentinel. Aproveite o agrupamento de eventos, o agrupamento de alertas e o enriquecimento de alertas e entenda o AUTO DESABILITAdo.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: 6f0a94daef8c5db820a17fe8cb50eda616bcf260
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102453907"
---
# <a name="tutorial-create-custom-analytics-rules-to-detect-threats"></a>Tutorial: criar regras de análise personalizadas para detectar ameaças

Agora que você [conectou suas fontes de dados](quickstart-onboard.md) ao Azure Sentinel, você pode criar regras de análise personalizadas para ajudá-lo a descobrir ameaças e comportamentos anormais que estão presentes no seu ambiente. Essas regras pesquisam eventos específicos ou conjuntos de eventos em seu ambiente, alertam quando determinados limites ou condições de evento são atingidos, geram incidentes para o SOC para fazer a triagem e investigar e responder a ameaças com processos automatizados de controle e correção. 

Este tutorial ajuda você a criar regras personalizadas para detectar ameaças com o Azure Sentinel.

Ao concluir este tutorial, você poderá fazer o seguinte:
> [!div class="checklist"]
> * Criar regras de análise
> * Definir como eventos e alertas são processados
> * Definir como os alertas e incidentes são gerados
> * Escolher respostas de ameaças automatizadas para suas regras

## <a name="create-a-custom-analytics-rule-with-a-scheduled-query"></a>Criar uma regra de análise personalizada com uma consulta agendada

1. No menu de navegação do Azure Sentinel, selecione **análise**.

1. Na barra de ação na parte superior, selecione **+ criar** e selecione **regra de consulta agendada**. Isso abre o **Assistente de regra de análise**.

    :::image type="content" source="media/tutorial-detect-threats-custom/create-scheduled-query-small.png" alt-text="Criar consulta agendada" lightbox="media/tutorial-detect-threats-custom/create-scheduled-query-full.png":::

### <a name="analytics-rule-wizard---general-tab"></a>Assistente de regra de análise-guia geral

- Forneça um **nome** exclusivo e uma **Descrição**. 

- No campo **táticas** , você pode escolher entre as categorias de ataques pelas quais classificar a regra. Elas se baseiam nas táticas da estrutura [Mitre ATT&CK](https://attack.mitre.org/) .

- Defina a **severidade** do alerta conforme apropriado. 

- Quando você cria a regra, seu **status** é **habilitado** por padrão, o que significa que ela será executada imediatamente depois que você terminar de criá-la. Se você não quiser que ele seja executado imediatamente, selecione **desabilitado** e a regra será adicionada à guia **regras ativas** e você poderá habilitá-la a partir daí, quando precisar.

   :::image type="content" source="media/tutorial-detect-threats-custom/general-tab.png" alt-text="Começar a criar uma regra de análise personalizada":::

## <a name="define-the-rule-query-logic-and-configure-settings"></a>Definir a lógica de consulta da regra e definir as configurações

Na guia **definir lógica de regra** , você pode escrever uma consulta diretamente no campo de **consulta de regra** ou criar a consulta em log Analytics e, em seguida, copiá-la e colá-la aqui.

- As consultas são escritas na KQL (Kusto Query Language). Saiba mais sobre os [conceitos](/azure/data-explorer/kusto/concepts/) e [consultas](/azure/data-explorer/kusto/query/)do KQL e veja esse [Guia de referência rápida](/azure/data-explorer/kql-quick-reference)prático.

- O exemplo mostrado nesta captura de tela consulta a tabela *SecurityEvent* para exibir um tipo de [eventos de logon do Windows com falha](/windows/security/threat-protection/auditing/event-4625).

   :::image type="content" source="media/tutorial-detect-threats-custom/set-rule-logic-tab-1-new.png" alt-text="Configurar a lógica e as configurações da regra de consulta" lightbox="media/tutorial-detect-threats-custom/set-rule-logic-tab-all-1-new.png":::

- Aqui está outra consulta de exemplo, uma que o alertaria quando um número anormal de recursos for criado na [atividade do Azure](../azure-monitor/platform/activity-log.md).

    ```kusto
    AzureActivity
    | where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
    | where ActivityStatus == "Succeeded"
    | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller
    ```

    > [!NOTE]
    > #### <a name="rule-query-best-practices"></a>Práticas recomendadas de consulta de regra
    > - O comprimento da consulta deve ter entre 1 e 10.000 caracteres e não pode conter " `search *` " ou " `union *` ".
    >
    > - **Não há suporte** para o uso de funções ADX para criar consultas do Azure data Explorer dentro da janela log Analytics consulta.
    >
    > - Ao usar a **`bag_unpack`** função em uma consulta, se você projetar as colunas como campos usando " `project field1` " e a coluna não existir, a consulta falhará. Para se proteger contra isso acontecendo, você deve projetar a coluna da seguinte maneira:
    >   - `project field1 = column_ifexists("field1","")`

### <a name="alert-enrichment"></a>Enriquecimento de alerta

> [!IMPORTANT]
> Os recursos de enriquecimento de alerta estão atualmente em versão **prévia**. Consulte os [termos de uso suplementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.
    
- Use a seção configuração de **mapeamento de entidade** para mapear parâmetros dos resultados da consulta para entidades reconhecidas do Sentinela do Azure. As entidades enriquecem a saída das regras (alertas e incidentes) com informações essenciais que servem como os blocos de construção de quaisquer processos investigativos e ações corretivas a seguir. Eles também são os critérios pelos quais você pode agrupar alertas em incidentes na guia **configurações de incidentes** .

    Saiba mais sobre [entidades no Azure Sentinel](entities-in-azure-sentinel.md).

    Consulte [mapear campos de dados para entidades no Azure Sentinel](map-data-fields-to-entities.md) para obter instruções completas de mapeamento de entidade, juntamente com informações importantes sobre a [compatibilidade com versões anteriores](map-data-fields-to-entities.md#notes-on-the-new-version).

- Use a seção configuração de **detalhes personalizados** para extrair itens de dados de eventos da sua consulta e Surface-los nos alertas produzidos por essa regra, fornecendo visibilidade imediata do conteúdo do evento em seus alertas e incidentes.

    Saiba mais sobre os detalhes personalizados do identificando em alertas e veja as [instruções completas](surface-custom-details-in-alerts.md).

### <a name="query-scheduling-and-alert-threshold"></a>Agendamento de consulta e limite de alerta

- Na seção **agendamento de consulta** , defina os seguintes parâmetros:

   :::image type="content" source="media/tutorial-detect-threats-custom/set-rule-logic-tab-2.png" alt-text="Definir agendamento de consulta e agrupamento de eventos" lightbox="media/tutorial-detect-threats-custom/set-rule-logic-tab-all-2-new.png":::

    - Defina **Executar consulta a cada** para controlar a frequência com que a consulta é executada-com frequência a cada 5 minutos ou com pouca frequência a cada 14 dias.

    - Defina **dados de pesquisa do último** para determinar o período de tempo dos dados cobertos pela consulta-por exemplo, ele pode consultar os últimos 10 minutos de dados ou as últimas 6 horas de dados. O máximo é 14 dias.

        > [!NOTE]
        > **Intervalos de consulta e período de lookback**
        >
        >  Essas duas configurações são independentes umas das outras, até um ponto. Você pode executar uma consulta em um intervalo curto, cobrindo um período de tempo maior do que o intervalo (em vigor com consultas sobrepostas), mas não é possível executar uma consulta em um intervalo que exceda o período de cobertura, caso contrário, você terá lacunas na cobertura de consulta geral.
        >
        > **Atraso de ingestão**
        >
        > Para considerar a **latência** que pode ocorrer entre a geração de um evento na origem e sua ingestão na sentinela do Azure, e para garantir a cobertura completa sem a duplicação de dados, o Azure Sentinel executa regras de análise agendadas em um **atraso de cinco minutos** em relação ao horário agendado.
        >
        > Para obter uma explicação técnica detalhada de por que esse atraso é necessário e como ele resolve esse problema, consulte o excelente post do blog de Ron Marsiano sobre o assunto "[manipulando o atraso de ingestão nas regras de alerta agendadas do Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/handling-ingestion-delay-in-azure-sentinel-scheduled-alert-rules/ba-p/2052851)".

- Use a seção **limite de alerta** para definir o nível de sensibilidade da regra. Por exemplo, defina **gerar alerta quando o número de resultados da consulta** **for maior que** e insira o número 1000 se você quiser que a regra gere um alerta somente se a consulta retornar mais de 1000 resultados cada vez que for executada. Este é um campo obrigatório, portanto, se você não quiser definir um limite, ou seja, se quiser que o alerta Registre todos os eventos – Insira 0 no campo número.
    
### <a name="results-simulation"></a>Simulação de resultados

Na área **simulação de resultados** , no lado direito do assistente, selecione **testar com os dados atuais** e o Azure Sentinel mostrará um grafo dos resultados (eventos de log) que a consulta teria gerado nos últimos 50 vezes em que teria sido executada, de acordo com a agenda definida no momento. Se você modificar a consulta, selecione **testar com os dados atuais** novamente para atualizar o grafo. O grafo mostra o número de resultados no período de tempo definido, que é determinado pelas configurações na seção **agendamento de consulta** .
  
Esta é a aparência da simulação de resultados para a consulta na captura de tela acima. O lado esquerdo é a exibição padrão e o lado direito é o que você vê quando passa o mouse sobre um ponto no tempo no grafo.

:::image type="content" source="media/tutorial-detect-threats-custom/results-simulation.png" alt-text="Capturas de tela de simulação de resultados":::

Se você vir que sua consulta dispararia muitos alertas ou muito frequentes, você poderá experimentar as configurações nas seções **agendamento de consulta** e **limite de alerta** [](#query-scheduling-and-alert-threshold) e selecionar **testar com os dados atuais** novamente.

### <a name="event-grouping-and-rule-suppression"></a>Agrupamento de eventos e supressão de regra

> [!IMPORTANT]
> O agrupamento de eventos está atualmente em **Visualização**. Consulte os [termos de uso suplementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.
    
- Em **agrupamento de eventos**, escolha uma das duas maneiras de lidar com o agrupamento de **eventos** em **alertas**: 

    - **Agrupar todos os eventos em um único alerta** (a configuração padrão). A regra gera um único alerta toda vez que ele é executado, desde que a consulta retorne mais resultados do que o **limite de alerta** especificado acima. O alerta inclui um resumo de todos os eventos retornados nos resultados. 

    - **Disparar um alerta para cada evento**. A regra gera um alerta exclusivo para cada evento retornado pela consulta. Isso será útil se você quiser que os eventos sejam exibidos individualmente ou se quiser agrupá-los por determinados parâmetros-por usuário, nome do host ou outra coisa. Você pode definir esses parâmetros na consulta.
    
        Atualmente, o número de alertas que uma regra pode gerar está limitado a 20. Se estiver em uma regra específica, o **agrupamento de eventos** será definido para **disparar um alerta para cada evento** e a consulta da regra retornará mais de 20 eventos, cada um dos primeiros 19 eventos gerará um alerta exclusivo e o alerta 20 resumirá todo o conjunto de eventos retornados. Em outras palavras, o alerta 20 é o que teria sido gerado na opção **agrupar todos os eventos em um único alerta** .

    > [!NOTE]
    > Qual é a diferença entre **eventos** e **alertas**?
    >
    > - Um **evento** é uma descrição de uma única ocorrência de uma ação. Por exemplo, uma única entrada em um arquivo de log pode ser contada como um evento. Nesse contexto, um evento se refere a um único resultado retornado por uma consulta em uma regra de análise.
    >
    > - Um **alerta** é uma coleção de eventos que, juntos, são significativos do ponto de vista da segurança. Um alerta pode conter um único evento se o evento tivesse implicações de segurança significativas – um logon administrativo de um país estrangeiro fora do horário comercial, por exemplo.
    >
    > - A propósito, o que são **incidentes**? A lógica interna do Azure Sentinel cria **incidentes** de **alertas** ou grupos de alertas. A fila de incidentes é o ponto focal do trabalho dos analistas do SOC, investigação e correção.
    > 
    > O Azure Sentinel ingere eventos brutos de algumas fontes de dados e alertas já processados de outras pessoas. É importante observar com qual deles você está lidando a qualquer momento.

- Na seção **supressão** , você pode desativar a configuração **parar a execução da consulta após o alerta ser gerado** **em** se, depois de receber um alerta, você deseja suspender a operação dessa regra por um período de tempo excedendo o intervalo de consulta. Se você ativar essa opção, deverá definir **parar a execução da consulta para** o tempo durante o qual a consulta deve parar de ser executada, até 24 horas.

## <a name="configure-the-incident-creation-settings"></a>Definir as configurações de criação de incidentes

Na guia **configurações de incidente** , você pode escolher se e como o Azure Sentinel transforma os alertas em incidentes acionáveis. Se essa guia for deixada sozinha, o Azure Sentinel criará um único incidente separado de cada alerta. Você pode optar por não ter nenhum incidente criado ou agrupar vários alertas em um único incidente, alterando as configurações nessa guia.

> [!IMPORTANT]
> A guia Configurações de incidente está atualmente em **Visualização**. Consulte os [termos de uso suplementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.

:::image type="content" source="media/tutorial-detect-threats-custom/incident-settings-tab.png" alt-text="Definir as configurações de criação de incidentes e de agrupamento de alertas":::

### <a name="incident-settings"></a>Configurações de incidentes

Na seção **configurações de incidente** , **criar incidentes de alertas disparados por essa regra de análise** é definido por padrão como **habilitado**, o que significa que o Azure Sentinel criará um único incidente separado de cada um e de todos os alertas disparados pela regra.
    
- Se você não quiser que essa regra resulte na criação de quaisquer incidentes (por exemplo, se essa regra for apenas para coletar informações para análise subsequente), defina isso como **desabilitado**.

- Se você quiser que um único incidente seja criado a partir de um grupo de alertas, em vez de um para cada alerta, consulte a próxima seção.

### <a name="alert-grouping"></a>Agrupamento de alertas

Na seção **agrupamento de alertas** , se você quiser que um único incidente seja gerado a partir de um grupo de até 150 alertas semelhantes ou recorrentes (consulte a observação), defina **alertas relacionados ao grupo, disparados por essa regra de análise, em incidentes** a serem **habilitados** e defina os parâmetros a seguir.

- **Limitar o grupo aos alertas criados dentro do período de tempo selecionado**: Determine o intervalo de tempo no qual os alertas semelhantes ou recorrentes serão agrupados juntos. Todos os alertas correspondentes nesse período de tempo gerarão coletivamente um incidente ou um conjunto de incidentes (dependendo das configurações de agrupamento abaixo). Os alertas fora desse período de tempo gerarão um incidente separado ou um conjunto de incidentes.

- Os **alertas de grupo disparados por essa regra de análise em um único incidente**: escolha a base em que os alertas serão agrupados juntos:

    - **Agrupar alertas em um único incidente se todas as entidades corresponderem: os** alertas serão agrupados se compartilharem valores idênticos para cada uma das entidades mapeadas (definidas na guia definir lógica de regra acima). Esta é a configuração recomendável.

    - **Agrupar todos os alertas disparados por essa regra em um único incidente**: todos os alertas gerados por essa regra são agrupados em conjunto mesmo que não compartilhem valores idênticos.

    - **Agrupar alertas em um único incidente se as entidades selecionadas forem correspondentes: os** alertas serão agrupados se compartilharem valores idênticos para algumas das entidades mapeadas (que você pode selecionar na lista suspensa). Você talvez queira usar essa configuração se, por exemplo, desejar criar incidentes separados com base nos endereços IP de origem ou de destino.

- **Reabrir incidentes de correspondência fechados**: se um incidente tiver sido resolvido e fechado, e posteriormente em outro alerta for gerado que deve pertencer a esse incidente, defina essa configuração como **habilitada** se você quiser que o incidente fechado seja reaberto e deixe como **desabilitado** se desejar que o alerta crie um novo incidente.
    
    > [!NOTE]
    > **Até 150 alertas** podem ser agrupados em um único incidente. Se mais de 150 alertas forem gerados por uma regra que os agrupa em um único incidente, um novo incidente será gerado com os mesmos detalhes de incidente que o original e os alertas em excesso serão agrupados no novo incidente.

## <a name="set-automated-responses-and-create-the-rule"></a>Definir respostas automatizadas e criar a regra

1. Na guia **respostas automatizadas** , selecione os guias estratégicos que você deseja executar automaticamente quando um alerta for gerado pela regra personalizada. Para obter mais informações sobre como criar e automatizar os guias estratégicos, consulte [responder a ameaças](tutorial-respond-threats-playbook.md).

    :::image type="content" source="media/tutorial-detect-threats-custom/automated-response-tab.png" alt-text="Definir as configurações de resposta automatizadas":::

1. Selecione **revisar e criar** para examinar todas as configurações de sua nova regra de alerta. Quando a mensagem "validação aprovada" for exibida, selecione **criar** para inicializar a regra de alerta.

    :::image type="content" source="media/tutorial-detect-threats-custom/review-and-create-tab.png" alt-text="Examinar todas as configurações e criar a regra":::

## <a name="view-the-rule-and-its-output"></a>Exibir a regra e sua saída
  
- Você pode encontrar sua regra personalizada recém-criada (do tipo "agendado") na tabela sob a guia **regras ativas** na tela principal da **análise** . Nessa lista, você pode habilitar, desabilitar ou excluir cada regra.

- Para exibir os resultados das regras de alerta que você cria, vá para a página **incidentes** , na qual você pode fazer a triagem, [investigar incidentes](tutorial-investigate-cases.md)e corrigir as ameaças.

> [!NOTE]
> Os alertas gerados no Azure Sentinel estão disponíveis por meio do [Microsoft Graph Security](/graph/security-concept-overview). Para obter mais informações, consulte a [documentação de alertas de segurança do Microsoft Graph](/graph/api/resources/security-api-overview).

## <a name="troubleshooting"></a>Solução de problemas

### <a name="issue-no-events-appear-in-query-results"></a>Problema: nenhum evento aparece nos resultados da consulta

Se o **agrupamento de eventos** estiver definido para **disparar um alerta para cada evento**, em determinados cenários, ao exibir os resultados da consulta em um momento posterior (como ao dinamizar de volta para alertas de um incidente), é possível que nenhum resultado da consulta seja exibido. Isso ocorre porque a conexão do evento com o alerta é realizada pelo hash das informações do evento específico e pela inclusão do hash na consulta. Se os resultados da consulta tiverem sido alterados desde que o alerta foi gerado, o hash não será mais válido e nenhum resultado será exibido. 

Para ver os eventos, remova manualmente a linha com o hash da consulta da regra e execute a consulta.

### <a name="issue-a-scheduled-rule-failed-to-execute-or-appears-with-auto-disabled-added-to-the-name"></a>Problema: uma regra agendada não pôde ser executada ou aparece com DESABILITAdo automaticamente adicionado ao nome

É uma rara ocorrência de falha na execução de uma regra de consulta agendada, mas isso pode acontecer. O Azure Sentinel classifica as falhas antecipadamente como transitórias ou permanentes, com base no tipo específico da falha e nas circunstâncias que levaram a ela.

#### <a name="transient-failure"></a>Falha transitória

Uma falha transitória ocorre devido a uma circunstância que é temporária e em breve retornará ao normal, ponto em que a execução da regra terá êxito. Alguns exemplos de falhas que o Azure Sentinel classifica como transitória são:

- Uma consulta de regra leva muito tempo para ser executada e atinge o tempo limite.
- Problemas de conectividade entre fontes de dados e Log Analytics, ou entre Log Analytics e o Azure Sentinel.
- Qualquer outra falha nova e desconhecida é considerada transitória.

No caso de uma falha transitória, o Azure Sentinel continua tentando executar a regra novamente após os intervalos predeterminados e cada vez maiores, até um ponto. Depois disso, a regra será executada novamente apenas no próximo horário agendado. Uma regra nunca será desabilitada automaticamente devido a uma falha transitória.

#### <a name="permanent-failure---rule-auto-disabled"></a>Falha permanente-regra desabilitada automaticamente

Uma falha permanente ocorre devido a uma alteração nas condições que permitem a execução da regra, o que sem intervenção humana não retornará ao status anterior. Veja a seguir alguns exemplos de falhas que são classificadas como permanentes:

- O espaço de trabalho de destino (no qual a consulta de regra operada) foi excluído.
- A tabela de destino (na qual a consulta de regra operada) foi excluída.
- O Azure Sentinel foi removido do espaço de trabalho de destino.
- Uma função usada pela consulta de regra não é mais válida; Ele foi modificado ou removido.
- As permissões para uma das fontes de dados da consulta de regra foram alteradas.
- Uma das fontes de dados da consulta de regra foi excluída ou desconectada.

**No caso de um número predeterminado de falhas permanentes consecutivas, do mesmo tipo e da mesma regra,** O Azure Sentinel interrompe a tentativa de executar a regra e também executa as seguintes etapas:

- Desabilita a regra.
- Adiciona as palavras **"desabilitadas automaticamente"** ao início do nome da regra.
- Adiciona o motivo da falha (e a desabilitação) à descrição da regra.

Você pode determinar facilmente a presença de qualquer regra desabilitada automaticamente, classificando a lista de regras por nome. As regras desabilitadas automaticamente estarão no início da lista ou próximo dela.

Os gerentes de SOC devem ter certeza de verificar a lista de regras regularmente para a presença de regras desabilitadas automaticamente.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a começar a detectar ameaças usando o Azure Sentinel.

- Saiba como [investigar incidentes no Azure Sentinel](tutorial-investigate-cases.md).
- Saiba mais sobre [entidades no Azure Sentinel](entities-in-azure-sentinel.md).
- Saiba como [configurar respostas de ameaças automatizadas no Azure Sentinel](tutorial-respond-threats-playbook.md).

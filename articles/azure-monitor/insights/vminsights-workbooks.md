---
title: Crie relatórios interativos com pastas de trabalho do Azure Monitor | Microsoft Docs
description: Simplifique a criar relatórios complexos com pastas de trabalho com parâmetros predefinidas e personalizadas para o Azure Monitor para VMs.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2019
ms.author: magoedte
ms.openlocfilehash: 90c236347380bb5d5e51db56d0f431d2659a7258
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61387012"
---
# <a name="create-interactive-reports-with-azure-monitor-workbooks"></a>Crie relatórios interativos com pastas de trabalho do Azure Monitor

Pastas de trabalho do texto, de combinar [registrar consultas](../log-query/query-language.md), métricas e parâmetros em relatórios interativos avançados. As Pastas de Trabalho são editáveis por qualquer membro da equipe com acesso aos mesmos recursos do Azure.

Pastas de trabalho são úteis para cenários como:

* Explorando o uso de sua máquina virtual quando você não sabe as métricas de interesse antecipadamente: Utilização da CPU, espaço em disco, memória, as dependências de rede, etc. Ao contrário de outras ferramentas de análise de uso, as pastas de trabalho permitem combinar vários tipos de visualizações e análises, tornando-as excelentes para esse tipo de exploração de forma livre.
* Para sua equipe, explicando como executar uma VM recentemente provisionada, mostrando as métricas de contadores-chave e outros eventos de log.
* Compartilhamento dos resultados de um experimento de redimensionamento da VM com outros membros da equipe. Você pode explicar as metas do experimento com um texto e mostrar cada uso de métrica e consultas de análise usado para avaliar o experimento, junto com explicativos claros para se cada métrica estava acima ou abaixo do destino.
* Relatar o impacto de uma interrupção no uso de sua VM, combinando dados, explicação de texto e uma discussão das próximas etapas para impedir interrupções no futuro.

O Azure Monitor para VMs inclui várias pastas de trabalho para você começar e a tabela a seguir resume-los.

| Pasta de trabalho | DESCRIÇÃO | Escopo |
|----------|-------------|-------|
| Desempenho | Fornece uma versão personalizável de nossa lista de N principais e o modo de exibição de gráficos em uma única pasta de trabalho que utiliza todos os contadores de desempenho do Log Analytics que você tiver habilitado.| Em grande escala |
| contadores de desempenho | Uma exibição de gráfico Top N em um amplo conjunto de contadores de desempenho. | Em grande escala |
| conexões | As conexões fornecem uma Visão aprofundada das conexões de entrada e saídas de suas VMs monitoradas. | Em grande escala |
| Portas ativas | Fornece uma lista dos processos que têm associado às portas nas VMs monitoradas e suas atividades no período de tempo escolhido. | Em grande escala |
| Abrir portas | Fornece o número de portas abertas em suas VMs monitoradas e os detalhes sobre aqueles abrir portas. | Em grande escala |
| Conexões com falha | Exibir a contagem de conexões com falha em suas VMs monitoradas, a tendência de falha, e se o percentual de falhas está aumentando ao longo do tempo. | Em grande escala |
| Segurança e Auditoria | Uma análise do tráfego TCP/IP que relata em geral conexões, conexões mal-intencionado, onde os pontos de extremidade do IP residem globalmente.  Para habilitar todos os recursos, você precisará habilitar a detecção de segurança. | Em grande escala |
| Tráfego de TCP | Um relatório com classificação para suas VMs monitoradas e sua rede enviado, recebido e total de tráfego em uma grade e exibido como uma linha de tendência. | Em grande escala |
| Comparação de tráfego | Este pastas de trabalho permite comparar as tendências de tráfego de rede para um único computador ou um grupo de computadores. | Em grande escala |
| Desempenho | Fornece uma versão personalizável de nosso modo de exibição de desempenho que aproveita todos os contadores de desempenho do Log Analytics que você tiver habilitado. | VM única | 
| conexões | As conexões fornecem uma Visão aprofundada das conexões de entrada e saídas de sua VM. | VM única |
 
## <a name="starting-with-a-template-or-saved-workbook"></a>Começando com um modelo ou pasta de trabalho salva

Uma pasta de trabalho composta por seções consiste em gráficos editáveis de forma independente, tabelas, texto e controles de entrada. Para entender melhor as pastas de trabalho, vamos começar abrindo um modelo e orientam durante a criação de uma pasta de trabalho personalizada. 

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Selecione **Máquinas Virtuais do Microsoft Azure**.

3. Na lista, selecione uma VM.

4. Na página da VM, na seção **Monitoramento**, selecione **Insights (versão prévia)**.

5. Na página de insights de VM, selecione **desempenho** ou **mapas** e, em seguida, selecione **pastas de trabalho do modo de exibição** do link na página. 

    ![Captura de tela de navegação para as pastas de trabalho](media/vminsights-workbooks/workbook-option-01.png)

6. Na lista suspensa, selecione **ir para a galeria** da parte inferior da lista.

    ![Captura de tela da lista suspensa de pasta de trabalho](media/vminsights-workbooks/workbook-dropdown-gallery-01.png)

    Ele abre a Galeria de pasta de trabalho com um número de pastas de trabalho predefinidos para ajudar você a começar.

7. Vamos começar com o **Modelo Padrão**, que está localizado no título **Guia de início rápido**.

    ![Captura de tela da galeria da pasta de trabalho](media/vminsights-workbooks/workbook-gallery-01.png)

## <a name="editing-workbook-sections"></a>Editar as seções da pasta de trabalho

Pastas de Trabalho têm dois modos: **modo de edição**, e **modo de leitura**. Quando a pasta de trabalho de modelo padrão é iniciado pela primeira vez, ele é aberto no **modo de edição**. Ele mostra todo o conteúdo da pasta de trabalho, incluindo quaisquer etapas e os parâmetros que são ocultas de outra forma. **Modo de leitura** apresenta uma exibição de estilo do relatório simplificado. Modo de leitura permite abstrair a complexidade que deu a criação de um relatório ao mesmo tempo, a mecânica subjacente apenas alguns cliques de distância quando necessário para modificação.

![O Azure Monitor para controles de edição de seção de pastas de trabalho de VMs](media/vminsights-workbooks/workbook-new-workbook-editor-01.png)

1. Quando você terminar uma seção de edição, clique em **edição concluída** no canto inferior esquerdo da seção.

2. Para criar uma duplicata de uma seção, clique no ícone **Clonar esta seção**. Criar seções duplicadas é uma ótima forma de iterar em uma consulta sem perder as iterações anteriores.

3. Para mover uma seção para cima em uma pasta de trabalho, clique no ícone **Mover para cima** ou **Mover para baixo**.

4. Para remover uma seção permanentemente, clique no ícone **Remover**.

## <a name="adding-text-and-markdown-sections"></a>Adicionando texto e seções de Markdown

Adicionar cabeçalhos, explicações e comentários às pastas de trabalho ajuda a transformar um conjunto de tabelas e gráficos em uma narrativa. As seções de texto nas pastas de trabalho dão suporte à [sintaxe de Markdown](https://daringfireball.net/projects/markdown/) na formatação de texto, como cabeçalhos, negrito, itálico e listas com marcadores.

Para adicionar uma seção de texto à pasta de trabalho, use o botão **Adicionar texto** na parte inferior da pasta de trabalho ou na parte inferior de qualquer seção.

## <a name="adding-query-sections"></a>Adicionando seções de consulta

![Seção de consulta no Workbooks](media/vminsights-workbooks/005-workbook-query-section.png)

Para adicionar uma seção de consulta à pasta de trabalho, use o botão **Adicionar consulta** na parte inferior da pasta de trabalho ou na parte inferior de qualquer seção.

Seções de consulta são altamente flexíveis e podem ser usadas para responder perguntas como:

* Como era minha utilização da CPU durante o mesmo período de tempo como um aumento no tráfego de rede?
* Qual era a tendência de espaço em disco disponível no último mês?
* Quantas falhas de conexão de rede tenham nas últimas duas semanas minha VM? 

Você também não é apenas limitado à consulta do contexto da máquina virtual que você iniciou a pasta de trabalho do. Você pode consultar em várias máquinas virtuais, bem como espaços de trabalho do Log Analytics, desde que você tem permissão de acesso a esses recursos.

Para incluir dados de outros espaços de trabalho do Log Analytics ou de um aplicativo de Application Insights específico usando o **espaço de trabalho** identificador. Para saber mais sobre as consultas entre recursos, consulte a [diretrizes oficiais](../log-query/cross-workspace-query.md).

### <a name="advanced-analytic-query-settings"></a>Configurações avançadas de consulta analítica

Cada seção tem suas próprias configurações avançadas, que podem ser acessadas por meio das configurações ![controles de edição da seção de pastas de trabalho](media/vminsights-workbooks/006-settings.png) ícone localizado à direita do **adicionar parâmetros** botão.

![O Azure Monitor para controles de edição de seção de pastas de trabalho de VMs](media/vminsights-workbooks/007-settings-expanded.png)

|         |          |
| ---------------- |:-----|
| **Largura personalizada**    | Torna um item um tamanho arbitrário, portanto, você pode ajustar vários itens em uma única linha que lhe permite organizar melhor seus gráficos e tabelas em relatórios interativos avançados.  |
| **Condicionalmente visível** | Especifique para ocultar etapas com base em um parâmetro quando no modo de leitura. |
| **Exportar um parâmetro**| Permitir que uma linha selecionada na grade ou gráfico para fazer com que as etapas posteriores alterar valores ou se tornar visível.  |
| **Mostrar consulta quando não estiver em edição** | Exibe a consulta acima do gráfico ou uma tabela, mesma quando no modo de leitura.
| **Mostrar abrir no botão análise ao não editar** | Adiciona o ícone azul de análise para o canto direito do gráfico para permitir o acesso de um clique.|

A maioria dessas configurações é bastante intuitiva, mas para entender **um parâmetro de exportação** é melhor examinar uma pasta de trabalho que usa essa funcionalidade.

Uma das pastas de trabalho predefinidas - **tráfego TCP**, fornece informações sobre as métricas de conexão de uma VM.

A primeira seção da pasta de trabalho se baseia em dados de consulta de log. A segunda seção também se baseia nos dados de consulta de log, mas selecionar uma linha na primeira tabela interativamente atualizará o conteúdo dos gráficos:

![O Azure Monitor para controles de edição de seção de pastas de trabalho de VMs](media/vminsights-workbooks/008-workbook-tcp-traffic.png)

O comportamento é possível por meio do uso do **quando um item é selecionado, exporte um parâmetro** as configurações avançadas, que são habilitadas na consulta de log da tabela.

![O Azure Monitor para controles de edição de seção de pastas de trabalho de VMs](media/vminsights-workbooks/009-settings-export.png)

A segunda consulta de log, em seguida, utiliza os valores exportados quando uma linha está selecionada para criar um conjunto de valores que são usados pelo título de seção e gráficos. Se nenhuma linha está selecionada, ele oculta o título de seção e gráficos. 

Por exemplo, o parâmetro oculto na segunda seção usa a referência da linha selecionada na grade a seguir:

```
VMConnection
| where TimeGenerated {TimeRange}
| where Computer in ("{ComputerName}") or '*' in ("{ComputerName}") 
| summarize Sent = sum(BytesSent), Received = sum(BytesReceived) by bin(TimeGenerated, {TimeRange:grain})
```

## <a name="adding-metrics-sections"></a>Adicionando seções de métricas

As seções métricas dão acesso completo ao incorporar dados de métricas do Azure Monitor em seus relatórios interativos. No Azure Monitor para VMs, as pastas de trabalho predefinidas normalmente conterá dados de consulta analítica em vez de dados de métrica.  Você pode optar por criar pastas de trabalho com dados de métrica, permitindo que você pode aproveitar o melhor de ambos os recursos em um só lugar. Você também tem a capacidade de extrair dados de métrica de recursos em qualquer uma das assinaturas que você tem acesso.

Aqui está um exemplo de dados da máquina virtual que está sendo extraídos para uma pasta de trabalho para fornecer uma visualização de grade da desempenho da CPU:

![O Azure Monitor para controles de edição de seção de pastas de trabalho de VMs](media/vminsights-workbooks/010-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Adicionando seções de parâmetro

Os parâmetros de pasta de trabalho permitem que você altere os valores na pasta de trabalho sem a necessidade de editar manualmente as seções de consulta ou texto. Isso elimina a necessidade de precisar compreender a linguagem de consulta do Analytics subjacente e expandindo imensamente o público-alvo potencial de relatórios baseados na pasta de trabalho.

Os valores dos parâmetros são substituídos na consulta, texto ou outras seções de parâmetro, colocando o nome do parâmetro entre chaves, como ``{parameterName}``. Nomes de parâmetro são limitados a regras semelhantes como identificadores de JavaScript, caracteres alfabéticos ou sublinhados, seguidos por caracteres alfanuméricos ou sublinhados. Por exemplo, **a1** é permitido, mas **1a** não é permitido.

Os parâmetros são lineares, começando na parte superior de uma pasta de trabalho e que fluem para baixo para etapas posteriores.  Parâmetros declarados posteriormente em uma pasta de trabalho podem substituir os parâmetros que foram declarados anteriormente. Isso também permite que os parâmetros que usam consultas para acessar os valores de parâmetros definidos anteriormente. Dentro de etapa de um parâmetro em si, parâmetros também são lineares, da esquerda para a direita, onde parâmetros à direita podem depender de um parâmetro declarado anteriormente na mesma etapa.
 
Há quatro tipos diferentes de parâmetros, que atualmente têm suporte:

|                  |      |
| ---------------- |:-----|
| **Texto**    | Permite que o usuário edite uma caixa de texto e, opcionalmente, você pode fornecer uma consulta para preencher o valor padrão. |
| **Lista suspensa** | Permite que o usuário escolher um conjunto de valores. |
| **Seletor de intervalo de tempo**| Permite ao usuário escolher um conjunto predefinido de valores de intervalo de tempo ou escolher um intervalo de tempo personalizado.|
| **Seletor de recurso** | Permite que o usuário escolher dentre os recursos selecionados para a pasta de trabalho.|

### <a name="using-a-text-parameter"></a>Usando um parâmetro de texto

O valor de um usuário digita na caixa de texto é substituído diretamente na consulta, sem escape ou aspas. Se o valor que você precisa é uma cadeia de caracteres, a consulta deve ter aspas ao redor do parâmetro (como **'{parameter}'**).

O parâmetro de texto permite que o valor em uma caixa de texto a ser usado em qualquer lugar. Pode ser um nome de tabela, o nome da coluna, nome da função, operador, etc.  O tipo de parâmetro de texto tem uma configuração **obter o valor padrão de consulta do analytics**, que permite ao autor da pasta de trabalho usar uma consulta para preencher o valor padrão para essa caixa de texto.

Ao usar o valor padrão de uma consulta de log, somente o primeiro valor da primeira linha (linha 0, a coluna 0) é usado como o valor padrão. Portanto, é recomendável limitar sua consulta para retornar apenas uma linha e uma coluna. Quaisquer outros dados retornados pela consulta serão ignorados. 

Qualquer valor que a consulta retorna será substituído diretamente por nenhum escape ou aspas. Se a consulta não retornar nenhuma linha, o resultado do parâmetro é uma cadeia de caracteres vazia (se o parâmetro não for necessário) ou indefinido (se o parâmetro for necessário).

### <a name="using-a-drop-down"></a>Usando uma lista suspensa

O tipo de parâmetro de lista suspensa permite criar um controle de lista suspensa, permitindo que a seleção de um ou vários valores.

A lista suspensa é preenchida por uma consulta de log ou JSON. Se a consulta retorna uma coluna, os valores nessa coluna são o valor e o rótulo no controle de lista suspensa. Se a consulta retorna duas colunas, a primeira coluna é o valor e a segunda coluna é o rótulo exibido na lista suspensa. Se a consulta retorna três colunas, a terceira coluna é usada para indicar a seleção padrão nessa lista suspensa. Esta coluna pode ser qualquer tipo, mas o mais simples é usar o bool ou tipos numéricos, onde 0 é falso e 1 é verdadeiro.

Se a coluna for um tipo de cadeia de caracteres, cadeia de caracteres nula ou está vazia, é considerada falsa, e qualquer outro valor será considerado verdadeiro. Para seleção única suspensas, o primeiro valor com um valor true é usado como a seleção padrão.  Para vários seleção suspensas, todos os valores com um valor true são usados como o conjunto padrão selecionado. Os itens na lista suspensa são mostrados na mesma ordem em linhas retornados pela consulta. 

Vamos examinar os parâmetros presentes no relatório de visão geral de conexões. Clique no símbolo de edição ao lado **direção**.

![O Azure Monitor para controles de edição de seção de pastas de trabalho de VMs](media/vminsights-workbooks/011-workbook-using-dropdown.png)

Isso iniciará a **Edit Parameter** item de menu.

![O Azure Monitor para controles de edição de seção de pastas de trabalho de VMs](media/vminsights-workbooks/012-workbook-edit-parameter.png)

O JSON permite gerar uma tabela arbitrária populada com conteúdo. Por exemplo, o JSON a seguir gera dois valores na lista suspensa:

```
[
    { "value": "inbound", "label": "Inbound"},
    { "value": "outbound", "label": "Outbound"}
]
```

Um exemplo mais aplicável está usando uma lista suspensa para escolher a partir de um conjunto de contadores de desempenho por nome:

```
Perf
| summarize by CounterName, ObjectName
| order by ObjectName asc, CounterName asc
| project Counter = pack('counter', CounterName, 'object', ObjectName), CounterName, group = ObjectName
```

A consulta exibirá os resultados da seguinte maneira:

![Menu suspenso de contador de desempenho](media/vminsights-workbooks/013-workbook-edit-parameter-perf-counters.png)

Menus suspensos são incrivelmente poderosas ferramentas para personalizar e criar relatórios interativos.

### <a name="time-range-parameters"></a>Parâmetros de intervalo de tempo

Embora você possa fazer seu próprio parâmetro de intervalo de tempo personalizado por meio do tipo de parâmetro de lista suspensa, você também pode usar o tipo de parâmetro de intervalo de tempo de uso, se você não precisa do mesmo grau de flexibilidade. 

Tipos de parâmetro de intervalo de tempo têm intervalos padrão de 15 que vão de cinco minutos para os últimos 90 dias. Também é uma opção para permitir a seleção de intervalo de tempo personalizado, que permite que o operador de relatório escolha iniciar explicitamente e parar os valores para o intervalo de tempo.

### <a name="resource-picker"></a>Seletor de recursos

O tipo de parâmetro de seletor de recurso fornece a capacidade de definir o escopo de seu relatório para determinados tipos de recursos. Um exemplo de uma pasta de trabalho predefinido que aproveita o tipo de seletor de recurso é o **desempenho** pasta de trabalho.

![Menu suspenso de espaços de trabalho](media/vminsights-workbooks/014-workbook-edit-parameter-workspaces.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Salvando e compartilhando pastas de trabalho com sua equipe

Pastas de trabalho são salvos dentro de um espaço de trabalho do Log Analytics ou um recurso de máquina virtual, dependendo de como você pode acessar a Galeria de pastas de trabalho. A pasta de trabalho pode ser salvos para o **meus relatórios** seção que é particular a você ou na **relatórios compartilhados** seção que está acessível para qualquer pessoa com acesso ao recurso. Para exibir todas as pastas de trabalho no recurso, clique no botão **Abrir** na barra de ações.

Para compartilhar uma pasta de trabalho que atualmente está em **Meus Relatórios**:

1. Clique em **Abrir** na barra de ações
2. Clique no botão “...” ao lado da pasta de trabalho que você deseja compartilhar
3. Clique em **Mover para Relatórios Compartilhados**.

Para compartilhar uma pasta de trabalho com um link ou por email, clique em **Compartilhar** na barra de ações. Tenha em mente que os destinatários do link precisam ter acesso a esse recurso no portal do Azure para exibir a pasta de trabalho. Para fazer edições, os destinatários precisam de, pelo menos, permissões de Colaborador ao recurso.

Para fixar um link em uma pasta de trabalho em um Painel do Azure:

1. Clique em **Abrir** na barra de ações
2. Clique no botão “...” ao lado da pasta de trabalho que você deseja fixar
3. Clique em **Fixar no painel**.

## <a name="next-steps"></a>Próximas etapas
Para saber como usar o recurso de integridade, consulte [exibir integridade da VM do Azure](vminsights-health.md), ou para exibir dependências de aplicativos descobertos, consulte [modo de exibição do Azure Monitor para VMs mapa](vminsights-maps.md). 
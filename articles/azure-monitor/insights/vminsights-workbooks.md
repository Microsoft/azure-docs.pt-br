---
title: Criar relatórios interativos do Azure Monitor para VMs com pastas de trabalho
description: Simplifique relatórios complexos com pastas de trabalho parametrizadas predefinidas e personalizadas para Azure Monitor para VMs.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 43cdb9de111bdea5486e49a56d58d38279b685c7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95985536"
---
# <a name="create-interactive-reports-azure-monitor-for-vms-with-workbooks"></a>Criar relatórios interativos do Azure Monitor para VMs com pastas de trabalho

As pastas de trabalho combinam texto, [consultas de log](/azure/data-explorer/kusto/query/), métricas e parâmetros em relatórios interativos sofisticados. As Pastas de Trabalho são editáveis por qualquer membro da equipe com acesso aos mesmos recursos do Azure.

As pastas de trabalho são úteis para cenários como:

* Explorando o uso de sua máquina virtual quando você não conhece as métricas de interesse com antecedência: utilização da CPU, espaço em disco, memória, dependências de rede etc. Ao contrário de outras ferramentas de análise de uso, as pastas de trabalho permitem combinar vários tipos de visualizações e análises, tornando-as excelentes para esse tipo de exploração de forma livre.
* Explicando à sua equipe como uma VM provisionada recentemente está sendo executada, mostrando métricas para contadores-chave e outros eventos de log.
* Compartilhando os resultados de um teste de redimensionamento de sua VM com outros membros de sua equipe. Você pode explicar as metas para o experimento com texto e, em seguida, mostrar cada métrica de uso e as consultas de análise usadas para avaliar o experimento, juntamente com as chamadas claras para se cada métrica estava acima ou abaixo do destino.
* Relatar o impacto de uma interrupção no uso de sua VM, combinar dados, explicação de texto e uma discussão das próximas etapas para evitar interrupções no futuro.

A tabela a seguir resume as pastas de trabalho que o Azure Monitor para VMs inclui para ajudá-lo a começar.

| Pasta de trabalho | Description | Escopo |
|----------|-------------|-------|
| Desempenho | Fornece uma versão personalizável do nosso modo de exibição de lista e gráficos em uma única pasta de trabalho que aproveita todos os contadores de desempenho de Log Analytics que você habilitou.| Em escala |
| Contadores de desempenho | Uma exibição de gráfico N superior em um amplo conjunto de contadores de desempenho. | Em escala |
| conexões | Conexões fornece uma exibição detalhada das conexões de entrada e saída de suas VMs monitoradas. | Em escala |
| Portas ativas | Fornece uma lista dos processos que foram vinculados às portas nas VMs monitoradas e suas atividades no período de tempo escolhido. | Em escala |
| Abrir portas | Fornece o número de portas abertas em suas VMs monitoradas e os detalhes sobre essas portas abertas. | Em escala |
| Conexões com falha | Exiba a contagem de conexões com falha em suas VMs monitoradas, a tendência de falha e se a porcentagem de falhas estiver aumentando com o passar do tempo. | Em escala |
| Segurança e Auditoria | Uma análise de seu tráfego TCP/IP que relata sobre conexões gerais, conexões mal-intencionadas, em que os pontos de extremidade de IP residem globalmente.  Para habilitar todos os recursos, será necessário habilitar a detecção de segurança. | Em escala |
| Tráfego de TCP | Um relatório classificado para suas VMs monitoradas e seus tráfegos de rede enviados, recebidos e totais em uma grade e exibidos como uma linha de tendência. | Em escala |
| Comparação de tráfego | Essas pastas de trabalho permitem comparar as tendências de tráfego de rede para um único computador ou um grupo de computadores. | Em escala |
| Desempenho | Fornece uma versão personalizável do nosso modo de exibição de desempenho que aproveita todos os contadores de desempenho de Log Analytics que você habilitou. | VM única | 
| conexões | Conexões fornece uma exibição detalhada das conexões de entrada e saída de sua VM. | VM única |
 
## <a name="creating-a-new-workbook"></a>Criando uma nova pasta de trabalho

Uma pasta de trabalho composta por seções consiste em gráficos editáveis de forma independente, tabelas, texto e controles de entrada. Para entender melhor as pastas de trabalho, vamos começar abrindo um modelo e percorrer a criação de uma pasta de trabalho personalizada. 

1. Entre no [portal do Azure](https://portal.azure.com).

2. Selecione **máquinas virtuais**.

3. Na lista, selecione uma VM.

4. Na página VM, na seção **monitoramento** , selecione **insights**.

5. Na página de informações da VM, selecione a guia **desempenho** ou **mapas** e, em seguida, selecione **exibir pastas de trabalho** no link na página. Na lista suspensa, selecione **ir para a Galeria**.

    ![Captura de tela da lista suspensa da pasta de trabalho](media/vminsights-workbooks/workbook-dropdown-gallery-01.png)

    Isso inicia a Galeria de pastas de trabalho com várias pastas de trabalho predefinidas para ajudá-lo a começar.

7. Crie uma nova pasta de trabalho selecionando **novo**.

    ![Captura de tela da galeria da pasta de trabalho](media/vminsights-workbooks/workbook-gallery-01.png)

## <a name="editing-workbook-sections"></a>Editando seções da pasta de trabalho

Pastas de Trabalho têm dois modos: **modo de edição**, e **modo de leitura**. Quando uma nova pasta de trabalho é iniciada pela primeira vez, ela é aberta no **modo de edição**. Ele mostra todo o conteúdo da pasta de trabalho, incluindo quaisquer etapas e parâmetros ocultos. **Modo de leitura** apresenta uma exibição de estilo do relatório simplificado. O modo de leitura permite abstrair a complexidade que deu na criação de um relatório enquanto ainda tem a mecânica subjacente apenas alguns cliques quando necessário para modificação.

![Captura de tela da seção da pasta de trabalho das máquinas virtuais no Azure Monitor mostrando uma nova pasta de trabalho no modo de edição com controles de edição realçados.](media/vminsights-workbooks/workbook-new-workbook-editor-01.png)

1. Quando terminar de editar uma seção, clique em **edição concluída** no canto inferior esquerdo da seção.

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

* Qual a minha utilização da CPU durante o mesmo período de tempo que um aumento no tráfego de rede?
* Qual foi a tendência no espaço em disco disponível no último mês?
* Quantas falhas de conexão de rede tinham minha experiência de VM nas últimas duas semanas? 

Você também não está limitado à consulta no contexto da máquina virtual da qual iniciou a pasta de trabalho. Você pode consultar entre várias máquinas virtuais, bem como Log Analytics espaços de trabalho, desde que tenha permissão de acesso a esses recursos.

Para incluir dados de outros espaços de trabalho do Log Analytics ou de um aplicativo Application Insights específico usando o identificador do **espaço de trabalho** . Para saber mais sobre consultas entre recursos, consulte a [orientação oficial](../log-query/cross-workspace-query.md).

### <a name="advanced-analytic-query-settings"></a>Configurações avançadas de consulta analítica

Cada seção tem suas próprias configurações avançadas, que podem ser acessadas por meio da ![ seção Configurações pastas de trabalho ícone de controles ](media/vminsights-workbooks/006-settings.png) de edição localizadas à direita do botão **adicionar parâmetros** .

![Captura de tela da caixa de diálogo Configurações avançadas na seção pasta de trabalho das máquinas virtuais do Azure Monitor. O ícone que abre a caixa de diálogo é realçado.](media/vminsights-workbooks/007-settings-expanded.png)

|         |          |
| ---------------- |:-----|
| **Largura personalizada**    | Torna um item um tamanho arbitrário, para que você possa ajustar muitos itens em uma única linha, permitindo que você organize melhor seus gráficos e tabelas em relatórios interativos sofisticados.  |
| **Condicionalmente visível** | Especifique para ocultar as etapas com base em um parâmetro no modo de leitura. |
| **Exportar um parâmetro**| Permitir que uma linha selecionada na grade ou no gráfico cause etapas posteriores para alterar valores ou tornar-se visível.  |
| **Mostrar consulta quando não estiver em edição** | Exibe a consulta acima do gráfico ou tabela, mesmo quando estiver no modo de leitura.
| **Mostrar abrir no botão análise ao não editar** | Adiciona o ícone de análise azul ao canto direito do gráfico para permitir o acesso com um clique.|

A maioria dessas configurações é bastante intuitiva, mas para entender **um parâmetro de exportação** é melhor examinar uma pasta de trabalho que usa essa funcionalidade.

Uma das pastas de trabalho predefinidas – **tráfego TCP**, fornece informações sobre métricas de conexão de uma VM.

A primeira seção da pasta de trabalho é baseada em dados de consulta de log. A segunda seção também é baseada em dados de consulta de log, mas a seleção de uma linha na primeira tabela atualizará interativamente o conteúdo dos gráficos:

![Captura de tela da seção máquinas virtuais em Azure Monitor mostrando o tráfego TCP da pasta de trabalho predefinida.](media/vminsights-workbooks/008-workbook-tcp-traffic.png)

O comportamento é possível por meio do uso de **quando um item é selecionado, exportar um parâmetro** configurações avançadas, que são habilitadas na consulta de log da tabela.

![Captura de tela da caixa de diálogo Configurações avançadas para uma pasta de trabalho de máquinas virtuais com a opção "quando um item é selecionado, exportar um parâmetro" marcada.](media/vminsights-workbooks/009-settings-export.png)

A segunda consulta de log utiliza os valores exportados quando uma linha é selecionada para criar um conjunto de valores que são usados pelo cabeçalho e pelos gráficos da seção. Se nenhuma linha for selecionada, ela ocultará o cabeçalho e os gráficos da seção. 

Por exemplo, o parâmetro Hidden na segunda seção usa a seguinte referência da linha selecionada na grade:

```
VMConnection
| where TimeGenerated {TimeRange}
| where Computer in ("{ComputerName}") or '*' in ("{ComputerName}") 
| summarize Sent = sum(BytesSent), Received = sum(BytesReceived) by bin(TimeGenerated, {TimeRange:grain})
```

## <a name="adding-metrics-sections"></a>Adicionando seções de métricas

As seções métricas dão acesso completo ao incorporar dados de métricas do Azure Monitor em seus relatórios interativos. Em Azure Monitor para VMs, as pastas de trabalho predefinidas normalmente conterão dados de consulta analítica em vez de dados de métrica.  Você pode optar por criar pastas de trabalho com dados de métrica, permitindo que você aproveite ao máximo os dois recursos em um só lugar. Você também tem a capacidade de extrair dados de métrica de recursos em qualquer uma das assinaturas que você tem acesso.

Aqui está um exemplo de dados da máquina virtual que estão sendo obtidos em uma pasta de trabalho para fornecer uma visualização em grade do desempenho da CPU:

![Captura de tela da seção de métricas de uma pasta de trabalho de máquina virtual no Azure Monitor. O desempenho da CPU para cada máquina virtual é mostrado graficamente.](media/vminsights-workbooks/010-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Adicionando seções de parâmetro

Os parâmetros de pasta de trabalho permitem que você altere os valores na pasta de trabalho sem a necessidade de editar manualmente as seções de consulta ou texto. Isso elimina a necessidade de precisar compreender a linguagem de consulta do Analytics subjacente e expandindo imensamente o público-alvo potencial de relatórios baseados na pasta de trabalho.

Os valores dos parâmetros são substituídos na consulta, texto ou outras seções de parâmetro, colocando o nome do parâmetro entre chaves, como ``{parameterName}``. Os nomes de parâmetro são limitados a regras semelhantes como identificadores de JavaScript, caracteres alfabéticos ou sublinhados, seguidos de caracteres alfanuméricos ou sublinhados. Por exemplo, **a1** é permitido, mas **1a** não é permitido.

Os parâmetros são lineares, começando na parte superior de uma pasta de trabalho e que fluem para baixo para etapas posteriores.  Parâmetros declarados posteriormente em uma pasta de trabalho podem substituir parâmetros que foram declarados anteriormente. Isso também permite que os parâmetros que usam consultas acessem os valores dos parâmetros definidos anteriormente. Dentro de etapa de um parâmetro em si, parâmetros também são lineares, da esquerda para a direita, onde parâmetros à direita podem depender de um parâmetro declarado anteriormente na mesma etapa.
 
Há quatro tipos diferentes de parâmetros, que atualmente têm suporte:

|                  |      |
| ---------------- |:-----|
| **Text**    | Permite que o usuário edite uma caixa de texto e, opcionalmente, você pode fornecer uma consulta para preencher o valor padrão. |
| **Lista suspensa** | Permite que o usuário escolha entre um conjunto de valores. |
| **Seletor de intervalo de tempo**| Permite que o usuário escolha entre um conjunto predefinido de valores de intervalo de tempo ou escolha um intervalo de tempo personalizado.|
| **Seletor de recursos** | Permite que o usuário escolha entre os recursos selecionados para a pasta de trabalho.|

### <a name="using-a-text-parameter"></a>Usando um parâmetro de texto

O valor que um usuário digita na caixa de texto é substituído diretamente na consulta, sem escape ou quot. Se o valor que você precisa é uma cadeia de caracteres, a consulta deve ter aspas ao redor do parâmetro (como **'{parameter}'**).

O parâmetro text permite que o valor em uma caixa de texto seja usado em qualquer lugar. Pode ser um nome de tabela, nome de coluna, nome de função, operador, etc.  O tipo de parâmetro text tem uma configuração **obter valor padrão da consulta Analytics**, que permite que o autor da pasta de trabalho use uma consulta para popular o valor padrão dessa caixa de texto.

Ao usar o valor padrão de uma consulta de log, somente o primeiro valor da primeira linha (linha 0, coluna 0) é usado como o valor padrão. Portanto, é recomendável limitar sua consulta para retornar apenas uma linha e uma coluna. Quaisquer outros dados retornados pela consulta serão ignorados. 

Qualquer valor que a consulta retorna será substituído diretamente por nenhum escape ou aspas. Se a consulta não retornar nenhuma linha, o resultado do parâmetro é uma cadeia de caracteres vazia (se o parâmetro não for necessário) ou indefinido (se o parâmetro for necessário).

### <a name="using-a-drop-down"></a>Usando uma lista suspensa

O tipo de parâmetro DropDown permite criar um controle suspenso, permitindo a seleção de um ou vários valores.

A lista suspensa é preenchida por uma consulta de log ou JSON. Se a consulta retornar uma coluna, os valores nessa coluna serão o valor e o rótulo no controle suspenso. Se a consulta retornar duas colunas, a primeira coluna será o valor e a segunda coluna será o rótulo mostrado na lista suspensa. Se a consulta retornar três colunas, a terceira coluna será usada para indicar a seleção padrão nesse menu suspenso. Esta coluna pode ser qualquer tipo, mas o mais simples é usar o bool ou tipos numéricos, onde 0 é falso e 1 é verdadeiro.

Se a coluna for um tipo de cadeia de caracteres, cadeia de caracteres nula ou está vazia, é considerada falsa, e qualquer outro valor será considerado verdadeiro. Para menus suspensos de seleção única, o primeiro valor com um valor true é usado como a seleção padrão.  Para menus suspensos com várias seleções, todos os valores com um valor true são usados como o conjunto selecionado padrão. Os itens na lista suspensa são mostrados em qualquer ordem em que a consulta retorna linhas. 

Vamos examinar os parâmetros presentes no relatório visão geral de conexões. Clique no símbolo de edição ao lado de **direção**.

![Captura de tela da seção para adicionar e editar parâmetros de relatório no Azure Monitor. O ícone Editar para o parâmetro de direção é selecionado.](media/vminsights-workbooks/011-workbook-using-dropdown.png)

Isso abrirá o item de menu **Editar parâmetro** .

![Captura de tela da caixa de diálogo Editar parâmetro. O nome do parâmetro é direção, o tipo de parâmetro é lista suspensa e obter dados de JSON é selecionado.](media/vminsights-workbooks/012-workbook-edit-parameter.png)

O JSON permite gerar uma tabela arbitrária populada com conteúdo. Por exemplo, o JSON a seguir gera dois valores na lista suspensa:

```
[
    { "value": "inbound", "label": "Inbound"},
    { "value": "outbound", "label": "Outbound"}
]
```

Um exemplo mais aplicável é usar uma lista suspensa para escolher um conjunto de contadores de desempenho por nome:

```
Perf
| summarize by CounterName, ObjectName
| order by ObjectName asc, CounterName asc
| project Counter = pack('counter', CounterName, 'object', ObjectName), CounterName, group = ObjectName
```

A consulta exibirá os resultados da seguinte maneira:

![Lista suspensa do contador de desempenho](media/vminsights-workbooks/013-workbook-edit-parameter-perf-counters.png)

Os menus suspensos são ferramentas incrivelmente poderosas para personalizar e criar relatórios interativos.

### <a name="time-range-parameters"></a>Parâmetros de intervalo de tempo

Embora você possa fazer seu próprio parâmetro de intervalo de tempo personalizado por meio do tipo de parâmetro de lista suspensa, você também pode usar o tipo de parâmetro de intervalo de tempo de uso, se você não precisa do mesmo grau de flexibilidade. 

Tipos de parâmetro de intervalo de tempo têm intervalos padrão de 15 que vão de cinco minutos para os últimos 90 dias. Também é uma opção para permitir a seleção de intervalo de tempo personalizado, que permite que o operador de relatório escolha iniciar explicitamente e parar os valores para o intervalo de tempo.

### <a name="resource-picker"></a>Seletor de recursos

O tipo de parâmetro de seletor de recurso fornece a capacidade de definir o escopo de seu relatório para determinados tipos de recursos. Um exemplo de uma pasta de trabalho predefinida que utiliza o tipo de seletor de recursos é a pasta de trabalho de **desempenho** .

![Menu suspenso espaços de trabalho](media/vminsights-workbooks/014-workbook-edit-parameter-workspaces.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Salvando e compartilhando pastas de trabalho com sua equipe

As pastas de trabalho são salvas em um Log Analytics espaço de trabalho ou em um recurso de máquina virtual, dependendo de como você acessa a Galeria de pastas de trabalho. A pasta de trabalho pode ser salva na seção **meus relatórios** que é particular para você ou na seção **relatórios compartilhados** que é acessível a todos com acesso ao recurso. Para exibir todas as pastas de trabalho no recurso, clique no botão **Abrir** na barra de ações.

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

- Para identificar as limitações e o desempenho geral da VM, consulte [Exibir o desempenho da VM do Azure](vminsights-performance.md).

- Para saber mais sobre as dependências de aplicativos descobertas, confira [Exibir o Mapa do Azure Monitor para VMs](vminsights-maps.md).
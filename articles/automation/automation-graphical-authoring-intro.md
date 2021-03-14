---
title: Criar runbooks gráficos na Automação do Azure
description: Este artigo informa como criar um runbook gráfico sem trabalhar com códigos.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: bbac794263fec176e03c7148d860c479a2ed9d39
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102501221"
---
# <a name="author-graphical-runbooks-in-azure-automation"></a>Criar runbooks gráficos na Automação do Azure

Todos os runbooks na Automação do Azure são fluxos de trabalho do Windows PowerShell. Runbooks gráficos e do Fluxo de Trabalho do PowerShell gráfico geram o código do PowerShell executado pelos trabalhos de Automação, mas o qual você não pode exibir nem modificar. Você pode converter um runbook gráfico em um runbook do Fluxo de Trabalho gráfico do PowerShell e vice-versa. No entanto, não é possível converter esses runbooks em runbooks textuais. Além disso, o editor gráfico de Automação não pode importar um runbook textual.

A criação gráfica permite criar runbooks para a Automação do Azure sem as complexidades do código subjacente do Windows PowerShell ou do Fluxo de Trabalho do PowerShell. Você pode adicionar as atividades de uma biblioteca de cmdlets e runbooks a uma tela, vinculá-las e configurá-las para formar um fluxo de trabalho. Se você já trabalhou com o System Center Orchestrator ou o SMA (Service Management Automation), a criação gráfica deverá ser familiar para você. Este artigo fornece uma introdução aos conceitos necessários para iniciar a criação de um runbook gráfico.

## <a name="overview-of-graphical-editor"></a>Visão geral do editor gráfico

Você pode abrir o editor gráfico no Portal do Azure criando ou editando um runbook gráfico.

![Workspace gráfico](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

As seções a seguir descrevem os controles no editor gráfico.

### <a name="canvas-control"></a>Controle de tela

O Controle de tela permite que você projete seu runbook. Você pode adicionar atividades dos nós no Controle de biblioteca ao runbook e os conectar a links para definir a lógica do runbook. Na parte inferior da tela, há controles que permitem ampliar e reduzir.

### <a name="library-control"></a>Controle de Biblioteca

O Controle de biblioteca permite que você selecione [atividades](#use-activities) para adicionar a seu runbook. Você pode adicioná-las à tela, onde pode conectá-las a outras atividades. O Controle biblioteca inclui as seções definidas na tabela a seguir.

| Seção | Descrição |
|:--- |:--- |
| Cmdlets |Todos os cmdlets que podem ser usados em seu runbook. Os cmdlets são organizados por módulo. Todos os módulos que você instalou em sua Conta de automação estão disponíveis. |
| Runbooks |Os runbooks em sua Conta de automação. Você pode adicionar esses runbooks à tela para serem usados como runbooks filhos. São mostrados apenas runbooks que tenham o mesmo tipo de núcleo do runbook sendo editado. Para runbooks gráficos, são mostrados apenas runbooks baseados no PowerShell. Para runbooks do Fluxo de Trabalho do PowerShell gráfico, são mostrados apenas runbooks baseados em Fluxo de Trabalho do PowerShell. |
| Ativos |Os [ativos de automação](/previous-versions/azure/dn939988(v=azure.100)) em sua Conta de automação que você pode usar em seu runbook. A adição de um ativo a um runbook adiciona uma atividade de fluxo de trabalho que obtém o ativo selecionado. No caso de ativos de variáveis, você pode selecionar se deseja adicionar uma atividade para obter a variável ou defini-la. |
| Controle de Runbook |Atividades de controle que podem ser usadas em seu runbook atual. Uma atividade de Junção recebe várias entradas e aguarda até que todas sejam concluídas antes de continuar o fluxo de trabalho. Uma Atividade de código executa uma ou mais linhas de código do PowerShell ou do Fluxo de Trabalho do PowerShell, dependendo do tipo de runbook gráfico. Você pode usar essa atividade para código personalizado ou a funcionalidade que é difícil de conseguir obter com outras atividades. |

### <a name="configuration-control"></a>Controle de Configuração

O Controle de configuração habilita você a fornecer detalhes para um objeto selecionado na tela. As propriedades disponíveis nesse controle dependem do tipo de objeto selecionado. Quando você escolhe uma opção no Controle de configuração, ele abre folhas adicionais para fornecer mais informações.

### <a name="test-control"></a>Controle de Teste

O controle de Teste não é exibido quando o editor gráfico é iniciado pela primeira vez. Ele é aberto quando você testa um runbook gráfico interativamente.

## <a name="use-activities"></a>Usar atividades

As atividades são os blocos de construção de um runbook. Uma atividade pode ser um cmdlet do PowerShell, um runbook filho ou um fluxo de trabalho. Você pode adicionar uma atividade ao runbook clicando com o botão direito do mouse no Controle de biblioteca e selecionando **Adicionar à tela**. Em seguida, você pode clicar e arrastar a atividade para colocá-la em qualquer lugar que você desejar na tela. O local da atividade na tela não afeta a operação do runbook. Você pode definir o layout de seu runbook da forma que considerar mais adequada para visualizar sua operação.

![Adicionar à tela](media/automation-graphical-authoring-intro/add-to-canvas-cmdlet.png)

Selecione uma atividade na tela para configurar suas propriedades e parâmetros na folha Configuração. Você pode alterar o rótulo da atividade e usar um nome que você considere ser uma descrição melhor. O runbook ainda pode executar o cmdlet original. O que você está fazendo é simplesmente alterar o nome de exibição usado pelo editor gráfico. Observe que o rótulo deve ser exclusivo no runbook.

### <a name="parameter-sets"></a>Conjuntos de parâmetros

Um conjunto de parâmetros define os parâmetros obrigatórios e opcionais que aceitam valores para determinado cmdlet. Todos os cmdlets têm pelo menos um conjunto de parâmetros, com alguns tendo vários. Se um cmdlet tiver vários conjuntos de parâmetros, você deve selecionar qual deles será usado antes de poder configurar os parâmetros. Você pode alterar o conjunto de parâmetros usado por uma atividade selecionando **Conjunto de Parâmetros** e escolhendo outro conjunto. Nesse caso, todos os valores de parâmetros que você já tenha configurado serão perdidos.

No exemplo a seguir, o cmdlet [Get-AzVM](/powershell/module/az.compute/get-azvm) tem três conjuntos de parâmetros. O exemplo usa um conjunto chamado **ListVirtualMachineInResourceGroupParamSet** com um único parâmetro opcional para retornar todas as máquinas virtuais de um grupo de recursos. O exemplo também usa o conjunto de parâmetros **GetVirtualMachineInResourceGroupParamSet** para especificar a máquina virtual que será retornada. Esse conjunto conta com dois parâmetros obrigatórios e um parâmetro opcional.

![Conjunto de Parâmetros](media/automation-graphical-authoring-intro/get-azvm-parameter-sets.png)

#### <a name="parameter-values"></a>Valores de parâmetro

Ao especificar um valor para um parâmetro, você seleciona uma fonte de dados para determinar como o valor é especificado. As fontes de dados que estão disponíveis para determinado parâmetro dependem dos valores válidos para esse parâmetro. Por exemplo, Null não é uma opção disponível para um parâmetro que não permite valores nulos.

| fonte de dados | Descrição |
|:--- |:--- |
| Valor Constante |Digite um valor válido para o parâmetro. Essa fonte de dados só está disponível para os seguintes tipos de dados: Int32, Int64, String, Boolean, DateTime e Switch. |
| Saída de Atividade |Use a saída de uma atividade que precede a atividade atual no fluxo de trabalho. Todas as atividades válidas estão listadas. Para o valor do parâmetro, use somente a atividade que produz a saída. Caso a atividade produza um objeto com várias propriedades, você poderá digitar o nome de uma propriedade específica depois de selecioná-la. |
| Entrada do Runbook |Selecione uma entrada de runbook como uma entrada para o parâmetro de atividade. |
| Ativo da Variável |Selecione uma Variável de automação como entrada. |
| Ativo da Credencial |Selecione uma Credencial de automação como entrada. |
| Ativo do Certificado |Selecione um Certificado de automação como entrada. |
| Ativo da Conexão |Selecione uma Conexão de automação como entrada. |
| Expressão do PowerShell |Especifique uma [expressão simples do PowerShell](#work-with-powershell-expressions). A expressão é avaliada antes da atividade, e o resultado será usado para o valor do parâmetro. Você pode usar variáveis para referir-se à saída de uma atividade ou um parâmetro de entrada de runbook. |
| Não configurado |Limpe qualquer valor que tenha sido configurado anteriormente. |

#### <a name="optional-additional-parameters"></a>Parâmetros adicionais opcionais

Todos os cmdlets têm a opção de fornecer parâmetros adicionais. Estes são os parâmetros comuns do PowerShell ou outros parâmetros personalizados. O editor gráfico apresenta uma caixa de texto em que você pode fornecer parâmetros usando a sintaxe do PowerShell. Por exemplo, para usar o parâmetro comum `Verbose`, você deve especificar `-Verbose:$True`.

### <a name="retry-activity"></a>Atividade de repetição

A funcionalidade de repetição de uma atividade permite que ela seja executada várias vezes até que uma determinada condição seja atendida; muito parecido com um loop. Você pode usar esse recurso para as atividades que devem ser executadas várias vezes, estão sujeitas a erros e podem precisar mais de uma tentativa de sucesso ou testar as informações de saída da atividade para obter dados válidos.

Quando você habilita a repetição de uma atividade, é possível definir um atraso e uma condição. O atraso é o tempo (medido em segundos ou minutos) que o runbook aguarda antes de executar novamente a atividade. Caso você não especifique um atraso, a atividade será reexecutada imediatamente após a conclusão.

:::image type="content" source="media/automation-graphical-authoring-intro/retry-delay.png" alt-text="Captura de tela das configurações Habilitar recurso de repetição.":::

A condição de repetição é uma expressão do PowerShell que é avaliada sempre após a execução da atividade. Se a expressão for resolvida como True, a atividade será executada novamente. Se a expressão for resolvida como False, a atividade não será executada novamente, e o runbook passará para a próxima atividade.

:::image type="content" source="media/automation-graphical-authoring-intro/retry-condition.png" alt-text="Captura de tela mostrando o campo repetir até que esse critério seja verdadeiro e exemplos de expressões do PowerShell que podem ser usadas na condição de repetição.":::

A condição de repetição pode usar uma variável chamada `RetryData` que fornece acesso às informações sobre as repetições de atividade. Essa variável tem as propriedades na tabela a seguir:

| Propriedade | Descrição |
|:--- |:--- |
| `NumberOfAttempts` |Número de vezes que a atividade foi executada. |
| `Output` |Saída da última execução da atividade. |
| `TotalDuration` |Tempo decorrido desde que a atividade foi iniciada pela primeira vez. |
| `StartedAt` |Hora (no formato UTC) em que a atividade foi iniciada pela primeira vez. |

A seguir, há exemplos de condições de repetição da atividade.

```powershell-interactive
# Run the activity exactly 10 times.
$RetryData.NumberOfAttempts -ge 10
```

```powershell-interactive
# Run the activity repeatedly until it produces any output.
$RetryData.Output.Count -ge 1
```

```powershell-interactive
# Run the activity repeatedly until 2 minutes has elapsed.
$RetryData.TotalDuration.TotalMinutes -ge 2
```

Depois de configurar uma condição de repetição para uma atividade, a atividade inclui duas indicações visuais para lembrá-lo. Uma é apresentada na atividade e a outra é mostrada quando você examina a configuração da atividade.

![Indicadores Visuais de Repetição da Atividade](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Controle de Script de Fluxo de Trabalho

Um Controle de script de fluxo de trabalho é uma atividade especial que aceita o script do PowerShell ou do Fluxo de Trabalho do PowerShell, dependendo do tipo de runbook gráfico sendo criado. Esse controle fornece uma funcionalidade que pode não estar disponível em outros meios. Ele não pode aceitar parâmetros, mas pode usar variáveis para saída de atividade e parâmetros de entrada de runbook. Todas as saídas da atividade são adicionadas ao databus. Uma exceção é a saída sem nenhum link de saída; nesse caso, a saída é adicionada àquela do runbook.

Por exemplo, o código a seguir executa cálculos de data usando uma variável de entrada de runbook chamada `NumberOfDays`. Em seguida, envia um valor DateTime calculado como saída para ser usado por atividades subsequentes no runbook.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="use-links-for-workflow"></a>Usar links para o fluxo de trabalho

Um link em um runbook gráfico conecta duas atividades. Ele é exibido na tela como uma seta apontando da atividade de origem para a atividade de destino. As atividades são executadas na direção da seta, com a atividade de destino se iniciando após a atividade de origem ser concluída.

### <a name="create-a-link"></a>Criar um link

Você pode criar um link entre duas atividades selecionando a atividade de origem e clicando no círculo na parte inferior da forma. Arraste a seta para a atividade de destino e solte-a.

![Criar um link](media/automation-graphical-authoring-intro/create-link-options.png)

Selecione o link para configurar suas propriedades na folha Configuração. As propriedades incluem o tipo de link, o qual é descrito na tabela a seguir.

| Tipo de link | Descrição |
|:--- |:--- |
| Pipeline |A atividade de destino é executada uma vez para cada objeto de saída da atividade de origem. A atividade de destino não será executada se a atividade de origem não resultar em saída. A saída da atividade de origem está disponível como um objeto. |
| Sequência |A atividade de destino é executada apenas uma vez, quando recebe a saída da atividade de origem. A saída da atividade de origem está disponível como uma matriz de objetos. |

### <a name="start-runbook-activity"></a>Iniciar a atividade de runbook

Um runbook gráfico é iniciado com todas as atividades que não têm um link de entrada. Geralmente, há apenas uma atividade, que atua como a atividade de início do runbook. Caso haja várias atividades sem um link de entrada, o runbook começará executando-as em paralelo. Ele segue então os links para executar outras atividades, à medida que cada uma for concluída.

### <a name="specify-link-conditions"></a>Especificar as condições do link

Ao especificar uma condição em um link, a atividade de destino só será executada se a condição for resolvida como True. Normalmente, você usa uma variável `ActivityOutput` em uma condição para recuperar a saída da atividade de origem.

Para um link de pipeline, você deve especificar uma condição a um único objeto. O runbook avalia a condição de cada saída de objeto conforme a atividade de origem. Depois, ele executa a atividade de destino para cada objeto que atenda à condição. Por exemplo, com uma atividade de origem `Get-AzVM`, você pode usar a sintaxe a seguir para que um link de pipeline condicional recupere somente as máquinas virtuais no grupo de recursos denominado Group1.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

Para um link de sequência, o runbook avalia a condição apenas uma vez, pois uma única matriz contendo todos os objetos da atividade de origem é retornada. Por esse motivo, o runbook não pode usar um link de sequência para fazer a filtragem tal qual ele pode fazer com um link de pipeline. O link de sequência pode simplesmente determinar se a próxima atividade será executada ou não.

Por exemplo, veja o conjunto de atividades a seguir em nosso runbook **Iniciar VM**:

![Link Condicional com Sequências](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

O runbook usa três links diferentes de sequência que verificam os valores dos parâmetros de entrada `VMName` e `ResourceGroupName` para determinar a ação apropriada a ser tomada. As ações possíveis são: iniciar uma única VM, iniciar todas as VMs no grupo de recursos ou iniciar todas as VMs em uma assinatura. Para o link de sequência entre `Connect to Azure` e `Get single VM`, está é a lógica da condição:

```powershell-interactive
<#
Both VMName and ResourceGroupName runbook input parameters have values
#>
(
(($VMName -ne $null) -and ($VMName.Length -gt 0))
) -and (
(($ResourceGroupName -ne $null) -and ($ResourceGroupName.Length -gt 0))
)
```

Quando você usa um link condicional, os dados disponíveis da atividade de origem para outras atividades nessa ramificação são filtradas pela condição. Se uma atividade for a origem para vários links, os dados disponíveis para atividades em cada ramificação dependerão da condição no link que se conecta a essa ramificação.

Por exemplo, a atividade `Start-AzVM` no runbook abaixo inicia todas as máquinas virtuais. Ela tem dois links condicionais. O primeiro link condicional usa a expressão `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` para filtrar se a atividade `Start-AzVM` é concluída com êxito. O segundo link condicional usa a expressão `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` para filtrar se a atividade `Start-AzVm` falha ao iniciar a máquina virtual.

![Exemplo de link condicional](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Qualquer atividade que seguir o primeiro link e usar a saída de atividade de `Get-AzureVM` só recuperarão as máquinas virtuais que foram iniciadas no momento da execução de `Get-AzureVM`. Qualquer atividade que seguir o segundo link só obterá as máquinas virtuais que foram interrompidas no momento que `Get-AzureVM` foi executado. Qualquer atividade que seguir o terceiro link obterá todas as máquinas virtuais, independentemente do estado de execução.

### <a name="use-junctions"></a>Usar junções

Uma junção é uma atividade especial que espera até que todas as ramificações de entrada sejam concluídas. Isso permite que o runbook execute várias atividades em paralelo e garante que todas tenham sido concluídas antes de prosseguir.

Embora uma junção possa ter um número ilimitado de links de entrada, somente um desses links pode ser um pipeline. O número de links de sequência de entrada não é restrito. Você pode criar a junção com vários links de pipeline de entrada e salvar o runbook, mas ele falhará ao ser executado.

O exemplo a seguir faz parte de um runbook que inicia um conjunto de máquinas virtuais enquanto baixa simultaneamente patches a serem aplicados a esses computadores. Ele usa uma junção para garantir que os processos sejam concluídos antes que o runbook continue.

![Junção](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="work-with-cycles"></a>Trabalhar com ciclos

Um ciclo é formado quando uma atividade de destino se vincula de volta à sua atividade de origem ou a outra atividade que, eventualmente, se vincula de volta à sua origem. A criação gráfica não oferece suporte a ciclos atualmente. Se seu runbook tiver um ciclo, ele será salvo corretamente, mas receberá um erro quando for executado.

![Ciclo](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="share-data-between-activities"></a>Compartilhar dados entre atividades

Todos os dados gerados por uma atividade com um link de saída são gravados no barramento de dados do runbook. Qualquer atividade no runbook pode usar dados no barramento de dados para popular os valores de parâmetros ou incluir no código de script. Uma atividade pode acessar a saída de qualquer atividade anterior no fluxo de trabalho.

A maneira como os dados são gravados no barramento de dados depende do tipo de link da atividade. Para um link de pipeline, os dados são gerados como vários objetos. Para um link de sequência, os dados são gerados como uma matriz. Caso haja apenas um valor, ele será gerado como uma matriz de elemento único.

Seu runbook tem duas maneiras de acessar dados no databus: 
* Usando uma fonte de dados de saída de atividade.
* Usando uma fonte de dados de expressão do PowerShell.

O primeiro mecanismo usa uma fonte de dados de saída de atividade para popular um parâmetro de outra atividade. Se a saída for um objeto, o runbook poderá especificar uma única propriedade.

![saída de atividade](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

O segundo mecanismo de acesso a dados usa a sintaxe mostrada abaixo para recuperar a saída de uma atividade em uma fonte de dados de expressão do PowerShell ou em uma atividade de script de fluxo de trabalho com uma variável `ActivityOutput`. Se a saída for um objeto, seu runbook poderá especificar uma única propriedade.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="use-checkpoints"></a>Usar pontos de verificação

Você pode definir [pontos de verificação](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) em um runbook do Fluxo de Trabalho do PowerShell gráfico selecionando **Runbook do ponto de verificação** em qualquer atividade. Isso causa a definição de um ponto de verificação após a execução da atividade.

![Ponto de verificação](media/automation-graphical-authoring-intro/set-checkpoint.png)

Os pontos de verificação são habilitados somente nos runbooks do Fluxo de Trabalho do PowerShell gráfico e não estão disponíveis nos runbooks gráficos. Caso o runbook use cmdlets do Azure, ele deverá seguir qualquer atividade de ponto de verificação com uma atividade `Connect-AzAccount`. A operação de conexão é usada caso o runbook seja suspenso e deva ser reiniciado a partir desse ponto de verificação em um trabalho diferente.

## <a name="handle-runbook-input"></a>Gerenciar a entrada do runbook

Um runbook requer a entrada de um usuário que está iniciando o runbook por meio do portal do Azure ou de outro runbook, se o atual for usado como filho. Por exemplo, para um runbook que cria uma máquina virtual, talvez o usuário precise fornecer informações como o nome da máquina virtual e outras propriedades sempre que o runbook for iniciado.

Para aceitar a entrada, o runbook define um ou mais parâmetros de entrada. O usuário fornece valores para esses parâmetros sempre que o runbook for iniciado. Ao iniciar o runbook usando o portal do Azure, o usuário é solicitado a fornecer valores para cada parâmetro de entrada com suporte do runbook.

Ao criar seu runbook, você pode acessar os parâmetros de entrada clicando em **Entrada e saída** na barra de ferramentas do runbook. Isso abre o controle de Entrada e saída, no qual você pode editar um parâmetro de entrada existente ou criar um novo clicando em **Adicionar entrada**.

![Adicionar entrada](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Cada parâmetro de entrada é definido pelas propriedades na tabela a seguir:

| Propriedade | Descrição |
|:--- |:--- |
| Nome | Obrigatórios. O nome do parâmetro. O nome deve ser exclusivo no runbook. Ele deve começar com uma letra e pode conter apenas letras, números e sublinhados. O nome não pode conter espaços. |
| Descrição |Opcional. Descrição da finalidade do parâmetro de entrada. |
| Type | Opcional. Tipo de dados esperado para o valor do parâmetro. O Portal do Azure fornece um controle apropriado para o tipo de dados para cada parâmetro quando a entrada for solicitada. Os tipos de parâmetro com suporte são: String, Int32, Int64, Decimal, Booliano, DateTime e Object. Se um tipo de dados não estiver selecionado, String será usado como padrão.|
| Obrigatório | Opcional. Configuração que especifica se um valor deve ser fornecido para o parâmetro. Se você escolher `yes`, um valor deverá ser fornecido quando o runbook for iniciado. Se você escolher `no`, um valor não será necessário quando o runbook for iniciado, e um valor padrão poderá ser definido. O runbook não poderá ser iniciado se você não fornecer um valor para cada parâmetro obrigatório que não tenha um valor padrão definido. |
| Valor Padrão | Opcional. O valor usado para um parâmetro, caso um não seja passado quando o runbook for iniciado. Para definir um valor padrão, escolha `Custom`. Escolha `None` se não quiser fornecer nenhum valor padrão. |

## <a name="handle-runbook-output"></a>Gerenciar a saída do runbook

A criação gráfica salva os dados criados por qualquer atividade que não tenha um link de saída para a [saída do runbook](./automation-runbook-output-and-messages.md). A saída é salva com o trabalho do runbook e está disponível para um runbook pai quando o runbook é usado como filho.

## <a name="work-with-powershell-expressions"></a>Trabalhar com expressões do PowerShell

Uma das vantagens da criação gráfica é que ela permite a criação de um runbook com o mínimo de conhecimento do PowerShell. Porém, atualmente você precisa conhecer um pouco de PowerShell para popular certos [valores de parâmetro](#use-activities) e configurar [as condições de vínculo](#use-links-for-workflow). Esta seção apresenta uma breve introdução a expressões do PowerShell. Detalhes completos do PowerShell estão disponíveis em [scripts com o Windows PowerShell](/powershell/scripting/overview).

### <a name="use-a-powershell-expression-as-a-data-source"></a>Usar uma expressão do PowerShell como uma fonte de dados

Você pode usar uma expressão do PowerShell como uma fonte de dados para popular o valor de um [parâmetro de atividade](#use-activities) com os resultados de códigos do PowerShell. A expressão pode ser uma única linha de código que executa uma função simples ou várias linhas que realizam alguma lógica complexa. Qualquer saída de um comando que não está atribuído a uma variável é enviado para o valor do parâmetro.

Por exemplo, o comando a seguir gera a data atual como saída.

```powershell-interactive
Get-Date
```

O próximo trecho de código cria uma cadeia de caracteres a partir da data atual e a atribui a uma variável. O código envia o conteúdo da variável para a saída.

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

Os comandos a seguir avaliam a data atual e retornam uma cadeia de caracteres que indica se o dia atual é um final de semana ou um dia da semana.

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="use-activity-output"></a>Usar a saída de atividade

Para usar a saída de uma atividade anterior no runbook, use a variável `ActivityOutput` com a sintaxe a seguir.

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

Você pode ter, por exemplo, uma atividade com uma propriedade que requer o nome de uma máquina virtual. Nesse caso, o runbook pode usar a expressão a seguir.

```powershell-interactive
$ActivityOutput['Get-AzureVM'].Name
```

Se a propriedade requerer o objeto de máquina virtual em vez de apenas um nome, o runbook retornará o objeto inteiro usando a sintaxe a seguir.

```powershell-interactive
$ActivityOutput['Get-AzureVM']
```

O runbook pode usar a saída de uma atividade em uma expressão mais complexa, como a mostrada a seguir. Essa expressão concatena o texto com o nome da máquina virtual.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVM'].Name
```

### <a name="compare-values"></a>Comparar valores

Use [operadores de comparação](/powershell/module/microsoft.powershell.core/about/about_comparison_operators) para comparar valores ou determinar se um valor corresponde a um padrão especificado. Uma comparação retorna um valor de True ou False.

Por exemplo, a condição a seguir determina se a máquina virtual de uma atividade denominada `Get-AzureVM` está interrompida.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

A condição a seguir determina se a mesma máquina virtual está em um estado que não seja o de interrompido.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

Você pode unir várias condições no seu runbook usando um [operador lógico](/powershell/module/microsoft.powershell.core/about/about_logical_operators), como `-and` ou `-or`. Por exemplo, a condição a seguir verifica se a mesma máquina virtual no exemplo anterior está em no estado de Interrompida ou Interrupção em andamento.

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="use-hashtables"></a>Usar tabelas de hash

[Tabelas de hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) são pares nome-valor que são úteis para retornar um conjunto de valores. Você também pode ver uma tabela de hash indicada como um dicionário. Propriedades de determinadas atividades esperam uma tabela de hash em vez de um valor simples.

Crie uma tabela de hash usando a sintaxe a seguir. Ela pode conter qualquer número de entradas, mas cada um é definido por um nome e valor.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

Por exemplo, a expressão a seguir cria uma tabela de hash a ser usada como a fonte de dados para um parâmetro de atividade que espera uma tabela de hash de valores para uma pesquisa na Internet.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

O exemplo a seguir usa a saída de uma atividade chamada `Get Twitter Connection` para popular uma tabela de hash.

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="authenticate-to-azure-resources"></a>Autenticar-se em recursos do Azure

Os runbooks na Automação do Azure que gerenciam os recursos do Azure requerem a autenticação do Azure. A opção [conta Executar como](./automation-security-overview.md), também conhecido como uma entidade de serviço, é o mecanismo padrão que um runbook de Automação usa para acessar recursos do Azure Resource Manager na sua assinatura. Você pode adicionar essa funcionalidade a um runbook gráfico adicionando o ativo de conexão `AzureRunAsConnection` à tela, o qual usa o cmdlet [Get-AutomationConnection](/system-center/sma/manage-global-assets) do PowerShell. Você também pode adicionar o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Esse cenário é ilustrado no seguinte exemplo.

![Executa Como Atividades de Autenticação](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

A atividade `Get Run As Connection`, ou `Get-AutomationConnection`, é configurada com uma fonte de dados de valor constante chamada `AzureRunAsConnection`.

![Executar Como Configuração da Conexão](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

A próxima atividade, `Connect-AzAccount`, adiciona a conta Executar como autenticada para usar no runbook.

![Conjunto de parâmetros Connect-AzureRmAccount](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

>[!NOTE]
>Para runbooks do PowerShell, `Add-AzAccount` e `Add-AzureRMAccount` são aliases para `Connect-AzAccount`. Observe que esses aliases não estão disponíveis para seus runbooks gráficos. Um runbook gráfico só pode usar a própria `Connect-AzAccount`.

Para os campos de parâmetros **APPLICATIONID**, **CERTIFICATETHUMBPRINT** e **TENANTID**, especifique o nome da propriedade para o caminho do campo, uma vez que a atividade gera um objeto de saída com várias propriedades. Caso contrário, quando o runbook for executado, ele falhará ao tentar se autenticar. Isso é o que você precisa, no mínimo, para autenticar seu runbook com a conta Executar Como.

Alguns assinantes criam uma conta de Automação usando uma [conta de usuário do Azure AD](./shared-resources/credentials.md) para gerenciar a implantação clássica do Azure ou para recursos do Azure Resource Manager. Para manter a compatibilidade com versões anteriores para esses assinantes, o mecanismo de autenticação que deve ser usado em seu runbook é o cmdlet `Add-AzureAccount` com um [ativo de credencial](./shared-resources/credentials.md). O ativo representa um usuário do Active Directory com acesso à conta do Azure.

Para habilitar essa funcionalidade para o runbook gráfico, você pode adicionar um ativo de credencial à tela, seguido de uma atividade `Add-AzureAccount` que usa o ativo de credencial para sua entrada. Veja os exemplos a seguir.

![Atividades de autenticação](media/automation-graphical-authoring-intro/authentication-activities.png)

O runbook deve autenticar ao iniciar e depois de cada ponto de verificação. Portanto, você deve usar uma atividade `Add-AzureAccount` após qualquer atividade `Checkpoint-Workflow`. Você não precisa usar uma atividade credencial adicional.

![Saída de Atividade](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="export-a-graphical-runbook"></a>Exportar um runbook gráfico

Você pode exportar apenas a versão publicada de um runbook gráfico. Se o runbook ainda não tiver sido publicado, o botão **Exportar** estará desabilitado. Ao clica no botão **Exportar**, o runbook será baixado no computador local. O nome do arquivo corresponde ao nome do runbook com uma extensão **.graphrunbook**.

## <a name="import-a-graphical-runbook"></a>Importar um runbook gráfico

Você pode importar um arquivo de runbook gráfico ou do Fluxo de Trabalho do PowerShell gráfico selecionando a opção **Importar** ao adicionar um runbook. Ao selecionar o arquivo a ser importado, você pode manter o mesmo nome ou fornecer um novo. O campo **Tipo de Runbook** exibe o tipo de runbook depois de avaliar o arquivo selecionado. Caso tente selecionar um tipo diferente que não esteja correto, o editor gráfico apresentará uma mensagem indicando que há conflitos em potencial e que pode haver erros de sintaxe durante a conversão.

![Importar runbook](media/automation-graphical-authoring-intro/runbook-import.png)

## <a name="test-a-graphical-runbook"></a>Testar um runbook gráfico

Cada runbook gráfico na Automação do Azure tem uma versão em Rascunho e uma versão Publicada. Você pode executar apenas a versão Publicada, e só pode editar a versão de Rascunho. A versão Publicada não é afetada pelas alterações feitas na versão de Rascunho. Quando a versão de Rascunho estiver pronta para uso, publique-a para substituir a versão Publicada atual.

Você pode testar a versão de Rascunho de um runbook no portal do Azure, deixando a versão Publicada inalterada. Como alternativa, você pode testar um novo runbook antes de ele ser publicado para que você possa verificar se o ele funciona corretamente antes de qualquer substituição de versão. Ao testar um runbook, a versão de Rascunho é executada e garante-se que quaisquer ações realizadas por ela serão concluídas. Nenhum histórico de trabalho é criado, mas o painel Testar Saída exibe a saída.

Abra o Controle de teste para um runbook abrindo o runbook para edição e clicando em **Painel de teste**. O Controle de teste solicita parâmetros de entrada, e você pode iniciar o runbook clicando em **Iniciar**.

## <a name="publish-a-graphical-runbook"></a>Publicar um runbook gráfico

Publique um runbook gráfico abrindo o runbook para edição e clicando no botão **Publicar**. Os status possíveis do runbook são:

* Novo: o runbook ainda não foi publicado. 
* Publicado: o runbook foi publicado.
* Em edição: o runbook foi editado depois de ter sido publicado, e as versões de Rascunho e Publicada são diferentes.

![Status de runbook](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

Você tem a opção de reverter para a versão Publicada de um runbook. Essa operação exclui quaisquer alterações feitas desde a última publicação do runbook. Ela substitui a versão de Rascunho do runbook pela versão Publicada.

## <a name="next-steps"></a>Próximas etapas

* Para começar a usar os runbooks gráficos, consulte [Tutorial: criar um runbook gráfico](learn/automation-tutorial-runbook-graphical.md).
* Para saber mais sobre os tipos de runbook, suas vantagens e limitações, confira [Tipos de runbook da Automação do Azure](automation-runbook-types.md).
* Para entender como autenticar usando a conta Executar Como de automação, consulte [Conta Executar como](automation-security-overview.md#run-as-account).
* Para obter uma referência de cmdlet do PowerShell, confira [Az.Automation](/powershell/module/az.automation/#automation).

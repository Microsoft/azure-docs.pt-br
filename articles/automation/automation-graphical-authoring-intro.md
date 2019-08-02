---
title: Criação gráfica na Automação do Azure
description: A criação gráfica permite criar runbooks para a Automação do Azure sem trabalhar com código. Este artigo fornece uma introdução à criação gráfica e todos os detalhes necessários para iniciar a criação de um runbook gráfico.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6d7626706951cc522dce9c6d70251455e64300bc
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476683"
---
# <a name="graphical-authoring-in-azure-automation"></a>Criação gráfica na Automação do Azure

A Criação Gráfica permite criar runbooks para a Automação do Azure sem as complexidades do código subjacente do Windows PowerShell ou do Fluxo de Trabalho do PowerShell. Você adiciona as atividades de uma biblioteca de cmdlets e runbooks a uma tela, vincula-as e configura para formar um fluxo de trabalho. Se você já trabalhou com o System Center Orchestrator ou o Service Management Automation (SMA), isso deve ser familiar para você.

Este artigo fornece uma introdução à criação gráfica e aos conceitos de que você precisa para iniciar a criação de um runbook gráfico.

## <a name="graphical-runbooks"></a>Runbooks gráficos

Todos os runbooks na Automação do Azure são Fluxos de Trabalho do Windows PowerShell. Os runbooks Gráficos e o Fluxo de Trabalho do PowerShell Gráfico geram o código do PowerShell que é executado pelos trabalhados da Automação, mas não é possível exibi-lo nem modificá-lo diretamente. Um runbook Gráfico pode ser convertido em um runbook do Fluxo de Trabalho do PowerShell Gráfico e vice-versa, mas eles não podem ser convertidos em um runbook textual. Um runbook textual existente não pode ser importado para o editor gráfico.

## <a name="overview-of-graphical-editor"></a>Visão geral do editor gráfico

Você pode abrir o editor gráfico no Portal do Azure criando ou editando um runbook gráfico.

![Workspace gráfico](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

As seções a seguir descrevem os controles no editor gráfico.

### <a name="canvas"></a>Tela

A Tela é onde você projeta seu runbook. Você adiciona atividades dos nós no controle de Biblioteca ao runbook e os conecta a links para definir a lógica do runbook.

Você pode usar os controles na parte inferior da tela para ampliar e reduzir.

### <a name="library-control"></a>Controle de Biblioteca

O controle de Biblioteca é onde você seleciona [atividades](#activities) para adicionar a seu runbook. Você pode adicioná-las à tela, onde as conecta a outras atividades. Ela inclui quatro seções, descritas na tabela a seguir:

| `Section` | DESCRIÇÃO |
|:--- |:--- |
| Cmdlets |Inclui todos os cmdlets que podem ser usados em seu runbook. Os cmdlets são organizados por módulo. Todos os módulos instalados em sua conta de automação estão disponíveis. |
| Runbooks |Inclui os runbooks em sua conta de automação. Esses runbooks podem ser adicionados à tela para serem usados como runbooks-filhos. Somente os runbooks do mesmo tipo de núcleo do runbook sendo editado são mostrados; para os runbooks Gráficos, somente os runbooks baseados no PowerShell são mostrados, enquanto que para os runbooks do Fluxo de Trabalho do PowerShell Gráfico, apenas os runbooks baseados no Fluxo de Trabalho do PowerShell são mostrados. |
| Ativos |Inclui os [ativos de automação](/previous-versions/azure/dn939988(v=azure.100)) em sua conta de automação que podem ser usados em seu runbook. Quando você adiciona um ativo a um runbook, ele adiciona uma atividade de fluxo de trabalho que obtém o ativo selecionado. No caso de ativos de variáveis, você pode selecionar se deseja adicionar uma atividade para obter a variável ou defini-la. |
| Controle de Runbook |Inclui as atividades de controle de runbook que podem ser usadas em seu runbook atual. Uma *Junção* recebe várias entradas e aguarda até que todas sejam concluídas antes de continuar o fluxo de trabalho. Uma atividade do *Código* executa uma ou mais linhas de código do PowerShell ou do Fluxo de Trabalho do PowerShell, dependendo do tipo de runbook gráfico. Você pode usar essa atividade para código personalizado ou a funcionalidade que é difícil de conseguir obter com outras atividades. |

### <a name="configuration-control"></a>Controle de Configuração

O controle da Configuração é onde você fornece detalhes para um objeto selecionado na tela. As propriedades disponíveis nesse controle dependem do tipo de objeto selecionado. Quando você seleciona uma opção no controle de Configuração, ele abre folhas adicionais para fornecer informações adicionais.

### <a name="test-control"></a>Controle de Teste

O controle de Teste não é exibido quando o editor gráfico é iniciado pela primeira vez. Ele é aberto quando você [testa um runbook gráfico](#graphical-runbook-procedures)interativamente.

## <a name="graphical-runbook-procedures"></a>Procedimentos de runbook gráfico

### <a name="exporting-and-importing-a-graphical-runbook"></a>Exportar e importar um runbook gráfico

Você pode exportar apenas a versão publicada de um runbook gráfico. Se o runbook ainda não foi publicado, o botão **Exportar** está desabilitado. Quando você clica no botão **Exportar**, o runbook é baixado no computador local. O nome do arquivo corresponde ao nome do runbook com uma extensão *graphrunbook*.

Você pode importar um arquivo de runbook Gráfico ou do Fluxo de Trabalho do PowerShell Gráfico selecionando a opção **Importar** ao adicionar um runbook. Quando você seleciona o arquivo a ser importado, você pode manter o mesmo **Nome** ou fornecer um novo. O campo Tipo de Runbook exibirá o tipo de runbook após ele avaliar o arquivo selecionado e se você tentar selecionar um tipo diferente que não está correto, uma mensagem será apresentada observando que há conflitos potenciais e durante a conversão, poderá haver erros de sintaxe.

![Importar runbook](media/automation-graphical-authoring-intro/runbook-import-revised20165.png)

### <a name="testing-a-graphical-runbook"></a>Testando um runbook gráfico

Você pode testar a versão de Rascunho de um runbook no Portal do Azure, mantendo a versão publicada do runbook inalterada, ou então pode testar um novo runbook antes que ele seja publicado. Isso permite que você verifique se o runbook está funcionando corretamente antes de substituir a versão publicada. Quando você testa um runbook, o runbook de Rascunho é executado, e as ações que ele realiza são concluídas. Nenhum histórico de trabalho é criado, mas a saída é exibida no Painel de Saída de Teste.

Abra o controle de Teste para um runbook abrindo o runbook para edição e, em seguida, clique no botão **Painel de teste** .

O controle de Teste solicita parâmetros de entrada, e você poderá iniciar o runbook clicando no botão **Iniciar**.

### <a name="publishing-a-graphical-runbook"></a>Publicando um runbook gráfico

Cada runbook na Automação do Azure tem um Rascunho e uma versão Publicada. Somente a versão Publicada está disponível para ser executada, e somente a versão de Rascunho pode ser editada. A versão Publicada não é afetada pelas alterações feitas na versão de Rascunho. Quando a versão de Rascunho está pronta para ser disponibilizada, você a publica, o que substitui a versão Publicada pela versão de Rascunho.

Você pode publicar um runbook gráfico abrindo o runbook para edição e, em seguida, clicando no botão **Publicar**.

Quando um runbook ainda não foi publicado, ele tem um status de **Novo**. Quando é publicado, ele tem um status de **Publicado**. Se você editar o runbook depois que ele foi publicado, e as versões de Rascunho e Publicada forem diferentes, o runbook terá um status de **Em edição**.

![Status de runbook](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

Você também tem a opção de reverter para a versão Publicada de um runbook. Isso descarta as alterações feitas desde que o runbook foi publicado pela última vez e substitui a versão de Rascunho do runbook pela versão Publicada.

## <a name="activities"></a>Atividades

As atividades são os blocos de construção de um runbook. Uma atividade pode ser um cmdlet do PowerShell, um runbook filho ou uma atividade de fluxo de trabalho. Você adiciona uma atividade ao runbook clicando com o botão direito do mouse no controle de Biblioteca e selecionando **Adicionar à tela**. Em seguida, você pode clicar e arrastar a atividade para colocá-la em qualquer lugar que você desejar na tela. O local da atividade na tela não afeta a operação do runbook de forma alguma. Você pode definir o layout de seu runbook da maneira que considerar mais adequada para visualizar sua operação.

![Adicionar à tela](media/automation-graphical-authoring-intro/add-to-canvas-revised20165.png)

Selecione a atividade na tela para configurar suas propriedades e parâmetros na folha Configuração. Você pode alterar o **Rótulo** da atividade para algo que a descreva a você. O cmdlet original ainda está sendo executado. Você está simplesmente alterando seu nome para exibição, que é usado no editor gráfico. O rótulo deve ser exclusivo no runbook.

### <a name="parameter-sets"></a>Conjuntos de parâmetros

Um conjunto de parâmetros define os parâmetros obrigatórios e opcionais que aceitam valores para determinado cmdlet. Todos os cmdlets têm pelo menos um conjunto de parâmetros, e alguns têm vários. Se um cmdlet tem vários conjuntos de parâmetros, você deve selecionar qual deles usa para poder configurar os parâmetros. Os parâmetros que você pode configurar dependem do conjunto de parâmetros que você escolhe. Você pode alterar o conjunto de parâmetros usado por uma atividade selecionando **Conjunto de Parâmetros** e selecionando outro conjunto. Nesse caso, quaisquer valores de parâmetros que você tiver configurado serão perdidos.

No exemplo a seguir, o cmdlet Get-AzureRmVM tem três conjuntos de parâmetros. Você não pode configurar valores de parâmetros até selecionar um dos conjuntos de parâmetros. O conjunto de parâmetros ListVirtualMachineInResourceGroupParamSet é para retornar todas as máquinas virtuais em um grupo de recursos e tem um único parâmetro opcional. O **GetVirtualMachineInResourceGroupParamSet** é para especificar a máquina virtual que você deseja retornar e tem um parâmetro obrigatório e um opcional.

![Conjunto de Parâmetros](media/automation-graphical-authoring-intro/get-azurermvm-parameter-sets.png)

#### <a name="parameter-values"></a>Valores de parâmetros

Ao especificar um valor para um parâmetro, você seleciona uma fonte de dados para determinar como o valor é especificado. As fontes de dados que estão disponíveis para determinado parâmetro dependem dos valores válidos para esse parâmetro. Por exemplo, Null não é uma opção disponível para um parâmetro que não permite valores nulos.

| Fonte de dados | DESCRIÇÃO |
|:--- |:--- |
| Valor Constante |Digite um valor válido para o parâmetro. Isso só está disponível para os seguintes tipos de dados: Int32, Int64, String, Boolean, DateTime e Switch. |
| Saída de Atividade |Saída de uma atividade que precede a atividade atual no fluxo de trabalho. Todas as atividades válidas estão listadas. Selecione apenas a atividade para usar sua saída para o valor do parâmetro. Se a atividade produzir um objeto com várias propriedades, você poderá digitar o nome da propriedade depois de selecioná-la. |
| Entrada do Runbook |Selecione um parâmetro de entrada de runbook como entrada para o parâmetro de atividade. |
| Ativo da Variável |Selecione uma Variável de Automação como entrada. |
| Ativo da Credencial |Selecione uma Credencial de Automação como entrada. |
| Ativo do Certificado |Selecione um Certificado de Automação como entrada. |
| Ativo da Conexão |Selecione uma Conexão de Automação como entrada. |
| Expressão do PowerShell |Especifique a [expressão simples do PowerShell](#powershell-expressions). A expressão é avaliada antes da atividade, e o resultado será usado para o valor do parâmetro. Você pode usar variáveis para referir-se à saída de uma atividade ou um parâmetro de entrada de runbook. |
| Não configurado |Limpa qualquer valor que foi configurado anteriormente. |

#### <a name="optional-additional-parameters"></a>Parâmetros adicionais opcionais

Todos os cmdlets têm a opção de fornecer parâmetros adicionais. Estes são os parâmetros comuns do PowerShell ou outros parâmetros personalizados. Será exibida uma caixa de texto em que você pode fornecer parâmetros usando a sintaxe do PowerShell. Por exemplo, para usar o parâmetro comum **Verbose**, você especificaria **"-Verbose:$True"** .

### <a name="retry-activity"></a>Atividade de repetição

**Comportamento de Repetição** permite que uma atividade seja executada várias vezes até que uma determinada condição seja atendida; muito parecido com um loop. Você pode usar esse recurso para as atividades que devem ser executadas várias vezes, estão sujeitas a erros e podem precisar mais de uma tentativa de sucesso ou testar as informações de saída da atividade para obter dados válidos.

Quando você habilita a repetição de uma atividade, é possível definir um atraso e uma condição. O atraso é o tempo (medido em segundos ou minutos) que o runbook aguarda antes de executar novamente a atividade. Se nenhum atraso for especificado, a atividade será executada novamente imediatamente após a conclusão.

![Atraso de repetição de atividade](media/automation-graphical-authoring-intro/retry-delay.png)

A condição de repetição é uma expressão do PowerShell avaliada sempre após a execução da atividade. Se a expressão for resolvida como True, a atividade será executada novamente. Se a expressão for resolvida como False, a atividade não será executada novamente e o runbook passará para a próxima atividade.

![Atraso de repetição de atividade](media/automation-graphical-authoring-intro/retry-condition.png)

A condição de repetição pode usar uma variável chamada $RetryData que fornece acesso às informações sobre as repetições de atividade. Essa variável tem as propriedades na tabela a seguir:

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| NumberOfAttempts |Número de vezes que a atividade foi executada. |
| Output |Saída da última execução da atividade. |
| TotalDuration |Tempo decorrido desde que a atividade foi iniciada pela primeira vez. |
| StartedAt |Hora no formato UTC em que a atividade foi iniciada pela primeira vez. |

Veja a seguir exemplos de condições de repetição da atividade.

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

Depois de configurar uma condição de repetição para uma atividade, a atividade inclui duas indicações visuais para lembrá-lo. Uma é apresentada na atividade e a outra é quando você examina a configuração da atividade.

![Indicadores Visuais de Repetição da Atividade](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Controle de Script de Fluxo de Trabalho

Um Controle de código é uma atividade especial que aceita o script do PowerShell ou do Fluxo de Trabalho do PowerShell, dependendo do tipo de runbook gráfico sendo criado para fornecer uma funcionalidade que pode não estar disponível. Ele não pode aceitar parâmetros, mas pode usar variáveis para saída de atividade e parâmetros de entrada de runbook. Qualquer saída de atividade é adicionada ao barramento de dados, a menos que não tenha um link de saída. Nesse caso, é adicionada à saída do runbook.

Por exemplo, o código a seguir executa cálculos de data usando uma variável de entrada de runbook chamada $NumberOfDays. Em seguida, envia uma data e hora calculadas como saída a ser usada por atividades subsequentes no runbook.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="links-and-workflow"></a>Links e fluxo de trabalho

Um **link** em um runbook gráfico conecta duas atividades. Ele é exibido na tela como uma seta apontando da atividade de origem para a atividade de destino. As atividades são executadas na direção da seta, com a atividade de destino se iniciando após a atividade de origem ser concluída.

### <a name="create-a-link"></a>Criar um link

Crie um link entre duas atividades selecionando a atividade de origem e clicando no círculo na parte inferior da forma. Arraste a seta para a atividade de destino e solte-a.

![Criar um link](media/automation-graphical-authoring-intro/create-link-revised20165.png)

Selecione o link para configurar suas propriedades na folha Configuração. Isso inclui o tipo de link, que é descrito na tabela a seguir:

| Tipo de link | DESCRIÇÃO |
|:--- |:--- |
| Pipeline |A atividade de destino é executada uma vez para cada objeto de saída da atividade de origem. A atividade de destino não será executada se a atividade de origem não resultar em saída. A saída da atividade de origem está disponível como um objeto. |
| Sequência |A atividade de destino é executada apenas uma vez. Ela recebe uma matriz de objetos da atividade de origem. A saída da atividade de origem está disponível como uma matriz de objetos. |

### <a name="starting-activity"></a>Atividade de início

Um runbook gráfico é iniciado com todas as atividades que não têm um link de entrada. Isso geralmente é apenas uma atividade, que deve atuar como a atividade de início para o runbook. Se várias atividades não tiverem um link de entrada, o runbook começa executando-as em paralelo. Ele segue então os links para executar outras atividades, à medida que cada uma for concluída.

### <a name="conditions"></a>Condições

Quando você especificar uma condição em um link, a atividade de destino será executada somente se a condição for resolvida como verdadeira. Normalmente, você usa uma variável $ActivityOutput em uma condição para recuperar a saída da atividade de origem

Para um link de pipeline, você especifica uma condição para um único objeto, e a condição é avaliada para cada saída de objeto pela atividade de origem. A atividade de destino é então executada para cada objeto que atende à condição. Por exemplo, com uma atividade de origem Get-AzureRmVm, a sintaxe a seguir pode ser usada para que um link de pipeline condicional recupere somente as máquinas virtuais no grupo de recursos denominado *Group1*.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

Para um link de sequência, a condição é avaliada apenas uma vez, pois é retornada uma única matriz que contém a saída de todos os objetos da atividade de origem. Por isso, um link de sequência não pode ser usado para filtragem como um link de pipeline, mas simplesmente determinará se a próxima atividade é executada ou não. Considere o seguinte conjunto de atividades, por exemplo, em nosso runbook Iniciar VM.

![Link Condicional com Sequências](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

Há três links de sequência diferentes que estão verificando os valores fornecidos a dois parâmetros de entrada do runbook representando o nome da VM e o nome do Grupo de Recursos para determinar qual é a ação apropriada a tomar – iniciar uma única VM, iniciar todas as VMs no grupo de recursos ou todas as VMs em uma assinatura. Para o link da sequência entre Conectar o Azure e Obter uma VM, aqui está a lógica da condição:

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

Quando você usa um link condicional, os dados disponíveis da atividade de origem para outras atividades nessa ramificação são filtradas pela condição. Se uma atividade for a origem para vários links, os dados disponíveis para atividades em cada ramificação dependem da condição no link que se conecta a essa ramificação.

Por exemplo, a atividade **Start-AzureRmVm** no runbook abaixo inicia todas as máquinas virtuais. Ela tem dois links condicionais. O primeiro link condicional usa a expressão *$ActivityOutput ['Start-AzureRmVM'].IsSuccessStatusCode-eq $true* para filtrar se a atividade Start-AzureRmVm foi concluída com êxito. O segundo link condicional usa a expressão *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -ne $true* para filtrar se a atividade Start-AzureRmVm não conseguiu iniciar a máquina virtual.

![Exemplo de link condicional](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Qualquer atividade que seguir o primeiro link e usa a saída de atividade de Get-AzureVM só obterá as máquinas virtuais que foram iniciadas no momento da execução de Get-AzureVM. Qualquer atividade que seguir o segundo link só obterá as máquinas virtuais que foram interrompidas no momento da execução de Get-AzureVM. Qualquer atividade que seguir o terceiro link obterá todas as máquinas virtuais, independentemente do estado de execução.

### <a name="junctions"></a>Junções

Uma junção é uma atividade especial que espera até que todas as ramificações de entrada sejam concluídas. Isso permite executar várias atividades em paralelo e garantir que todas tenham sido concluídas antes de prosseguir.

Embora uma junção possa ter um número ilimitado de links de entrada, apenas um desses links pode ser um pipeline. O número de links de sequência de entrada não é restrito. Você pode criar a junção com vários links de pipeline de entrada e salvar o runbook, mas ele falha ao ser executado.

O exemplo a seguir faz parte de um runbook que inicia um conjunto de máquinas virtuais enquanto baixa simultaneamente patches a serem aplicados a esses computadores. Uma junção é usada para garantir que os processos sejam concluídos antes que o runbook continue.

![Junção](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="cycles"></a>Ciclos

Um ciclo é quando uma atividade de destino se vincula de volta à sua atividade de origem ou a outra atividade que, eventualmente, se vincula de volta à sua origem. No momento, os ciclos não são permitidos na criação de gráficos. Se seu runbook tiver um ciclo, ele será salvo corretamente, mas receberá um erro quando for executado.

![Ciclo](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="sharing-data-between-activities"></a>Compartilhando dados entre atividades

Todos os dados que são gerados por uma atividade com um link de saída são gravados no *barramento de dados* do runbook. Qualquer atividade no runbook pode usar dados no barramento de dados para popular os valores de parâmetros ou incluir no código de script. Uma atividade pode acessar a saída de qualquer atividade anterior no fluxo de trabalho.

A maneira como os dados são gravados no barramento de dados depende do tipo de link da atividade. Para um **pipeline**, os dados são gerados como vários objetos. Para um link de **sequência** , os dados são gerados como uma matriz. Se houver apenas um valor, ele será gerado como uma matriz de elemento único.

Você pode acessar dados no barramento de dados usando um destes dois métodos. Primeiro, é possível usar uma fonte de dados de **Saída de Atividade** para popular um parâmetro de outra atividade. Se a saída for um objeto, você poderá especificar uma única propriedade.

![Saída de Atividade](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

Você também pode recuperar a saída de uma atividade em uma fonte de dados de **Expressão do PowerShell** ou de uma atividade de **Script de Fluxo de Trabalho** com uma variável ActivityOutput. Se a saída for um objeto, você poderá especificar uma única propriedade. As variáveis ActivityOutput usam a sintaxe a seguir.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="checkpoints"></a>Pontos de verificação

Você pode definir [pontos de verificação](automation-powershell-workflow.md#checkpoints) em um runbook do Fluxo de Trabalho do PowerShell Gráfico selecionando *Runbook do ponto de verificação* em qualquer atividade. Isso causa a definição de um ponto de verificação após a execução da atividade.

![Ponto de verificação](media/automation-graphical-authoring-intro/set-checkpoint.png)

Os pontos de verificação são habilitados somente nos runbooks do Fluxo de Trabalho do PowerShell Gráficos; eles não estão disponíveis nos runbooks Gráficos. Se o runbook usar cmdlets do Azure, você deverá seguir quaisquer atividades do ponto de verificação com um Connect-AzureRmAccount, caso o runbook seja suspenso e reinicie a partir desse ponto de verificação em um trabalho diferente.

## <a name="authenticating-to-azure-resources"></a>Autenticação para recursos do Azure

Os runbooks na Automação do Azure que gerenciam os recursos do Azure requerem a autenticação do Azure. O [Executar Como Conta](automation-create-runas-account.md) (também conhecido como uma entidade de serviço) é o método padrão para acessar os recursos do Azure Resource Manager em sua assinatura com os runbooks de Automação. Você pode adicionar essa funcionalidade a um runbook gráfico, adicionando o ativo de Conexão **AzureRunAsConnection**, que está usando o cmdlet [Get-AutomationConnection](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx) do PowerShell e o cmdlet [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount), à tela. Isso é ilustrado no exemplo a seguir:

![Executa Como Atividades de Autenticação](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

A atividade Executar Como Conexão de Get (por exemplo, Get-AutomationConnection), é configurada com uma fonte de dados com valor constante denominada AzureRunAsConnection.

![Executar Como Configuração da Conexão](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

A próxima atividade, Connect-AzureRmAccoun, adiciona a conta Executar Como autenticada para usar no runbook.

![Conjunto de parâmetros Connect-AzureRmAccount](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

> [!IMPORTANT]
> **Connect-AzureRmAccount** agora é um alias para **Connect-AzureRMAccount**. Ao pesquisar sua biblioteca de itens, se você não vir **Connect-AzureRMAccount**, você pode usar **Connect-AzureRmAccount**, ou você pode atualizar seus módulos em sua Conta de Automação.

Para os parâmetros **APPLICATIONID**, **CERTIFICATETHUMBPRINT** e **TENANTID**, você precisa especificar o nome da propriedade para o caminho Campo porque a atividade produz um objeto com várias propriedades. Caso contrário, quando você executar o runbook, ele falha ao tentar autenticar-se. Isso é o que você precisa, no mínimo, para autenticar seu runbook com a conta Executar Como.

Para manter a compatibilidade com versões anteriores para os assinantes que criaram uma conta de Automação utilizando uma [conta de Usuário do Azure AD](automation-create-aduser-account.md) para gerenciar a implantação clássica do Azure ou os recursos do Azure Resource Manager, o método de autenticação é o cmdlet Add-AzureAccount com um [ativo de credencial](automation-credentials.md) que representa um usuário do Active Directory com acesso à conta do Azure.

Você pode adicionar essa funcionalidade a um runbook gráfico adicionando um ativo de credencial à tela, seguido por uma atividade Add-AzureAccount. Add-AzureAccount usa a atividade de credencial para sua entrada. Isso é ilustrado no exemplo a seguir:

![Atividades de autenticação](media/automation-graphical-authoring-intro/authentication-activities.png)

Você deve se autenticar no início do runbook e após cada ponto de verificação. Isso significa adicionar uma atividade de adição Add-AzureAccount depois de qualquer atividade Checkpoint-Workflow. Você não precisa de uma atividade de credencial de adição, já que pode usar a mesma

![Saída de Atividade](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="runbook-input-and-output"></a>Entrada e saída de runbook

### <a name="runbook-input"></a>Entrada do Runbook

Um runbook pode exigir a entrada de um usuário quando este iniciar o runbook por meio do Portal do Azure ou de outro runbook, se o atual for usado como filho.
Por exemplo, se tiver um runbook que cria uma máquina virtual, talvez você precise fornecer informações como o nome da máquina virtual e outras propriedades sempre que iniciar o runbook.

Você aceita a entrada para um runbook definindo um ou mais parâmetros de entrada. Você fornece valores para esses parâmetros sempre que o runbook é iniciado. Quando você iniciar um runbook com o Portal do Azure, ele solicitará que você forneça valores para cada um dos parâmetros de entrada do runbook.

Você pode acessar parâmetros de entrada para um runbook clicando no botão **Entrada e saída** na barra de ferramentas do runbook.

Isso abre o controle **Entrada e saída**, em que você pode editar um parâmetro de entrada existente ou criar um novo clicando em **Adicionar entrada**.

![Adicionar entrada](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Cada parâmetro de entrada é definido pelas propriedades na tabela a seguir:

| Propriedade | Descrição |
|:--- |:--- |
| Name |O nome exclusivo do parâmetro. Pode conter apenas caracteres alfanuméricos e não pode conter espaços. |
| DESCRIÇÃO |Uma descrição opcional para o parâmetro de entrada. |
| Type |Tipo de dados esperado para o valor do parâmetro. O Portal do Azure fornece um controle apropriado para o tipo de dados para cada parâmetro quando a entrada for solicitada. |
| Obrigatório |Especifica se deve ser fornecido um valor para o parâmetro. O runbook não poderá ser iniciado se você não fornecer um valor para cada parâmetro obrigatório que não tenha um valor padrão definido. |
| Valor Padrão |Especifica o valor que será usado para o parâmetro, se nenhum for fornecido. Pode ser Nulo ou um valor específico. |

### <a name="runbook-output"></a>Saída de runbook

Os dados criados por qualquer atividade que não tenha um link de saída são salvos na [saída do runbook](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages). A saída é salva com o trabalho do runbook e está disponível para um runbook pai quando o runbook é usado como filho.

## <a name="powershell-expressions"></a>Expressões do PowerShell

Uma das vantagens da criação gráfica é fornecer a capacidade de criar um runbook com o mínimo de conhecimento do PowerShell. Atualmente, você precisa saber um pouco do PowerShell para popular certos [valores de parâmetro](#activities) e configurar [as condições de vínculo](#links-and-workflow). Esta seção fornece uma breve introdução a expressões do PowerShell para os usuários que não estejam familiarizados com ele. Detalhes completos do PowerShell estão disponíveis em [scripts com o Windows PowerShell](https://technet.microsoft.com/library/bb978526.aspx).

### <a name="powershell-expression-data-source"></a>Fonte de dados de expressão do PowerShell
Você pode usar uma expressão do PowerShell como uma fonte de dados para popular o valor de um [parâmetro de atividade](#activities) com os resultados de algum código do PowerShell. Isso pode ser uma única linha de código que executa uma função simples ou várias linhas que realizam alguma lógica complexa. Qualquer saída de um comando que não está atribuído a uma variável é enviado para o valor do parâmetro.

Por exemplo, o comando a seguir seria a saída da data atual.

Por exemplo, o comando a seguir seria a saída da data atual.

```powershell-interactive
Get-Date
```

Os seguintes comandos criam uma cadeia de caracteres a partir da data atual e a atribuem a uma variável. O conteúdo da variável é enviado para a saída

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

Os comandos a seguir avaliam a data atual e retornam uma cadeia de caracteres que indica se o dia atual é um final de semana ou dia da semana.

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="activity-output"></a>Saída de Atividade

Para usar a saída de uma atividade anterior no runbook, use a variável $ActivityOutput com a sintaxe a seguir.

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

Por exemplo, você pode ter uma atividade com uma propriedade que requer um nome de uma máquina virtual, caso em que você pode usar a expressão a seguir:

```powershell-interactive
$ActivityOutput['Get-AzureVm'].Name
```

Se a propriedade que exige a máquina virtual do objeto, em vez de apenas uma propriedade, retornaria o objeto inteiro usando a sintaxe a seguir.

```powershell-interactive
$ActivityOutput['Get-AzureVm']
```

Você também pode usar a saída de uma atividade em uma expressão mais complexa, como a seguir que concatena o texto para o nome da máquina virtual.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVm'].Name
```

### <a name="conditions"></a>Condições

Use [operadores de comparação](https://technet.microsoft.com/library/hh847759.aspx) para comparar valores ou determinar se um valor corresponde a um padrão especificado. Uma comparação retorna um valor de $true ou $false.

Por exemplo, a condição a seguir determina se a máquina virtual de uma atividade denominada *Get-AzureV* está *interrompida*.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

A condição a seguir verifica se a mesma máquina virtual está em um estado diferente de *interrompido*.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

Você pode unir várias condições usando um [operador lógico](https://technet.microsoft.com/library/hh847789.aspx) como **- e** ou **- ou**. Por exemplo, a condição a seguir verifica se a mesma máquina virtual no exemplo anterior está em no estado de *interrompida* ou *parando*.

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="hashtables"></a>Tabelas de hash

[Tabelas de hash](https://technet.microsoft.com/library/hh847780.aspx) são pares nome/valor que são úteis para retornar um conjunto de valores. Propriedades de determinadas atividades podem esperar uma tabela de hash em vez de um valor simples. Você também pode ver como a tabela de hash é conhecida como um dicionário.

Você cria uma tabela de hash com a sintaxe a seguir. Uma tabela de hash pode conter qualquer número de entradas, mas cada um é definido por um nome e valor.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

Por exemplo, a expressão a seguir cria uma tabela de hash a ser usada na fonte de dados para um parâmetro de atividade que espera uma tabela de hash com valores para uma pesquisa na Internet.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

O exemplo a seguir usam a saída de uma atividade chamada *Obter conexão do Twitter* para preencher uma tabela de hash.

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="next-steps"></a>Próximas etapas

* Para começar a usar os runbooks do fluxo de trabalho do PowerShell, consulte [Meu primeiro runbook do fluxo de trabalho do PowerShell](automation-first-runbook-textual.md)
* Para começar a usar os runbooks Gráficos, consulte [Meu primeiro runbook gráfico](automation-first-runbook-graphical.md)
* Para saber mais sobre os tipos de runbook, suas vantagens e limitações, consulte [Tipos de runbook de Automação do Azure](automation-runbook-types.md)
* Para entender como autenticar usando a conta Executar Como de automação, consulte [Configurar Conta Executar Como do Azure](automation-sec-configure-azure-runas-account.md)

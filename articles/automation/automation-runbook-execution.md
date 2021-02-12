---
title: Execução de runbook na Automação do Azure
description: Este artigo fornece uma visão geral do processamento de runbooks na automação do Azure.
services: automation
ms.subservice: process-automation
ms.date: 10/06/2020
ms.topic: conceptual
ms.openlocfilehash: 71273c456b14fa4ea289e2a48d441de99ce8a4b1
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99053900"
---
# <a name="runbook-execution-in-azure-automation"></a>Execução de runbook na Automação do Azure

A automação de processos na Automação do Azure permite que você crie e gerencie runbooks gráficos, do PowerShell e de Fluxo de Trabalho do PowerShell. Para obter detalhes, confira [Runbooks de Automação do Azure](automation-runbook-types.md). 

A automação executa seus runbooks com base na lógica definida dentro deles. Se um runbook for interrompido, ele será reiniciado. Esse comportamento exige que você grave runbooks que dão suporte à reinicialização caso ocorram problemas transitórios.

Iniciar um runbook na Automação do Azure cria um trabalho, que é uma instância de execução única do runbook. Cada trabalho acessa recursos do Azure fazendo uma conexão com sua assinatura do Azure. O trabalho só poderá acessar recursos em seu datacenter se eles estiverem acessíveis na nuvem pública.

A Automação do Azure atribui um trabalhador para executar cada trabalho durante a execução do runbook. Enquanto os trabalhadores são compartilhados por muitas contas do Azure, os trabalhos de diferentes contas de automação ficam isolados uns dos outros. Você não pode controlar de quais serviços seu trabalho necessita.

Quando você exibe a lista de runbooks no portal do Azure, ela mostra o status de cada trabalho que foi iniciado para cada runbook. A Automação do Azure armazena logs de trabalho por um máximo de 30 dias.

O diagrama a seguir mostra o ciclo de vida de um trabalho de runbook para [runbooks do PowerShell](automation-runbook-types.md#powershell-runbooks), [runbooks de Fluxo de Trabalhos do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) e [runbooks gráficos](automation-runbook-types.md#graphical-runbooks).

![Status de trabalho - Fluxo de trabalho do PowerShell](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="runbook-execution-environment"></a>Ambiente de execução de runbook

Os runbooks na Automação do Azure podem ser executados em uma área restrita do Azure ou em um [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md). 

Quando os runbooks são projetados para serem autenticados e executados em recursos no Azure, eles são executados em uma área restrita do Azure, que é um ambiente compartilhado que pode ser usado por vários trabalhos. Os trabalhos que usam a mesma área restrita são restringidos pelas limitações de recurso da área restrita. O ambiente de área restrita do Azure não oferece suporte a operações interativas. Ele impede o acesso a todos os servidores COM fora do processo. Além disso, requer o uso de arquivos MOF locais para runbooks que fazem chamadas Win32.

Você também pode usar um [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) para executar runbooks diretamente no computador que hospeda a função e em recursos locais no ambiente. A Automação do Azure armazena e gerencia runbooks e, em seguida, entrega-os a um ou mais computadores atribuídos.

>[!NOTE]
>Para serem executados em um Hybrid Runbook Worker Linux, seus scripts devem ser assinados e a função de trabalho configurada de acordo. Como alternativa, a [validação de assinatura deve ser desativada](automation-linux-hrw-install.md#turn-off-signature-validation).

A tabela a seguir lista algumas tarefas de execução de runbook com o ambiente de execução recomendado listado para cada uma.

|Tarefa|Recomendação|Observações|
|---|---|---|
|Integração com serviços do Azure|Área restrita do Azure|Com hospedagem no Azure, a autenticação é mais simples. Se você estiver usando um Hybrid Runbook Worker em uma VM do Azure, poderá [usar a autenticação de runbook com identidades gerenciadas](automation-hrw-run-runbooks.md#runbook-auth-managed-identities).|
|Obter o desempenho ideal para gerenciar recursos do Azure|Área restrita do Azure|O script é executado no mesmo ambiente, que tem menos latência.|
|Redução de custos operacionais|Área restrita do Azure|Não há sobrecarga de computação nem a necessidade de uma VM.|
|Executar script de execução longa|Hybrid Runbook Worker|As áreas restritas do Azure têm [limites de recursos](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Interagir com serviços locais|Hybrid Runbook Worker|Acesse diretamente o computador host ou os recursos em outros ambientes de nuvem ou no ambiente local. |
|Exigir software de terceiros e executáveis|Hybrid Runbook Worker|Você gerencia o sistema operacional e pode instalar o software.|
|Monitoramento de um arquivo ou uma pasta com um runbook|Hybrid Runbook Worker|Use uma [Tarefa do Observador](automation-watchers-tutorial.md) em um Hybrid Runbook Worker.|
|Executar um script com uso intensivo de recursos|Hybrid Runbook Worker| As áreas restritas do Azure têm [limites de recursos](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Usar módulos com requisitos específicos| Hybrid Runbook Worker|Alguns exemplos são:</br> WinSCP – dependência de winscp.exe </br> Administração de IIS – dependência na habilitação ou gerenciamento do IIS|
|Instalar um módulo com um instalador|Hybrid Runbook Worker|Os módulos para área restrita devem dar suporte à cópia.|
|Usar runbooks ou módulos que exigem o .NET Framework diferente da versão 4.7.2|Hybrid Runbook Worker|As áreas restritas do Azure dão suporte a .NET Framework 4.7.2, e não há suporte para a atualização para uma versão diferente.|
|Executar scripts que exigem elevação|Hybrid Runbook Worker|As áreas restritas não permitem elevação. Com um Hybrid Runbook Worker, você pode desativar o UAC e usar [Invoke-Command](/powershell/module/microsoft.powershell.core/invoke-command) ao executar o comando que requer elevação.|
|Executar scripts que exigem acesso à WMI (Instrumentação de Gerenciamento do Windows)|Hybrid Runbook Worker|Trabalhos em execução em áreas restritas na nuvem não podem acessar o provedor WMI. |

## <a name="temporary-storage-in-a-sandbox"></a>Armazenamento temporário em uma área restrita

Se você precisar criar arquivos temporários como parte de sua lógica de runbook, poderá usar a pasta temp (ou seja, `$env:TEMP` ) na área restrita do Azure para runbooks em execução no Azure. A única limitação é que você não pode usar mais de 1 GB de espaço em disco, que é a cota de cada área restrita. Ao trabalhar com fluxos de trabalho do PowerShell, esse cenário pode causar um problema, pois os fluxos de trabalho do PowerShell usam pontos de verificação e o script pode ser repetido em uma área restrita diferente.

Com a área restrita híbrida, você pode usar `C:\temp` com base na disponibilidade de armazenamento em um Hybrid runbook Worker. No entanto, por recomendações de VM do Azure, você não deve usar o [disco temporário](../virtual-machines/managed-disks-overview.md#temporary-disk) no Windows ou Linux para dados que precisam ser persistentes.

## <a name="resources"></a>Recursos

Seus runbooks devem incluir lógica para lidar com [recursos](/rest/api/resources/resources), por exemplo, VMs, a rede e os recursos na rede. Os recursos são vinculados a uma assinatura do Azure, e os runbooks exigem credenciais apropriadas para acessar qualquer recurso. Para obter um exemplo de manipulação de recursos em um runbook, confira [Manipular recursos](manage-runbooks.md#handle-resources).

## <a name="security"></a>Segurança

A automação do Azure usa a [ASC (Central de Segurança do Azure)](../security-center/security-center-introduction.md) para fornecer segurança aos seus recursos e detectar comprometimento em sistemas Linux. A segurança é fornecida em suas cargas de trabalho, independentemente de os recursos estarem ou não no Azure. Confira [Introdução à autenticação na Automação do Azure](automation-security-overview.md).

A ASC impõe restrições quanto aos usuários que podem executar scripts, assinados ou não, em uma VM. Se você for um usuário com acesso raiz a uma VM, deverá configurar explicitamente a máquina com uma assinatura digital ou desativá-la. Caso contrário, você só poderá executar um script para aplicar as atualizações do sistema operacional após criar uma conta de Automação do Azure e habilitar o recurso apropriado.

## <a name="subscriptions"></a>Assinaturas

Uma [assinatura](/office365/enterprise/subscriptions-licenses-accounts-and-tenants-for-microsoft-cloud-offerings) do Azure é um contrato com a Microsoft para usar um ou mais serviços baseados em nuvem, pelos quais você será cobrado. Na Automação do Azure, cada assinatura é vinculada a uma conta de Automação do Azure, e você pode [criar várias assinaturas](manage-runbooks.md#work-with-multiple-subscriptions) na conta.

## <a name="credentials"></a>Credenciais

Um runbook requer [credenciais](shared-resources/credentials.md) apropriadas para acessar qualquer recurso, seja para sistemas do Azure ou de terceiros. Essas credenciais são armazenadas na Automação do Azure, no Key Vault etc.  

## <a name="azure-monitor"></a>Azure Monitor

A automação do Azure usa [Azure monitor](../azure-monitor/overview.md) para monitorar suas operações de máquina. As operações exigem um espaço de trabalho Log Analytics e um [agente log Analytics](../azure-monitor/platform/log-analytics-agent.md).

### <a name="log-analytics-agent-for-windows"></a>Agente do Log Analytics para Windows

O [agente do Log Analytics para Windows](../azure-monitor/platform/agent-windows.md) funciona com o Azure Monitor para gerenciar VMs e computadores físicos Windows. Os computadores podem ser executados no Azure ou em um ambiente não Azure, como um datacenter local.

>[!NOTE]
>O agente do Log Analytics para Windows era conhecido anteriormente como MMA (Microsoft Monitoring Agent).

### <a name="log-analytics-agent-for-linux"></a>Agente do Log Analytics para Linux

O [agente do Log Analytics para Linux](../azure-monitor/platform/agent-linux.md) funciona de forma semelhante ao agente para Windows, mas conecta computadores Linux ao Azure Monitor. O agente é instalado com uma conta de usuário do **nxautomation** que permite a execução de comandos que exigem permissões raiz, por exemplo, em um Hybrid runbook Worker. A conta de **nxautomation** é uma conta do sistema que não requer senha.

A conta de **nxautomation** com as permissões sudo correspondentes deve estar presente durante a [instalação de um Hybrid Runbook Worker do Linux](automation-linux-hrw-install.md). Se você tentar instalar o trabalho e a conta não estiver presente ou não tiver as permissões apropriadas, a instalação falhará.

Você não deve alterar as permissões da `sudoers.d` pasta ou sua propriedade. A permissão sudo é necessária para a conta **nxautomation** e as permissões não devem ser removidas. Restringir isso a determinadas pastas ou comandos pode resultar em uma alteração significativa.

Os logs disponíveis para o agente do Log Analytics e a conta de **nxautomation** são:

* /var/opt/microsoft/omsagent/log/omsagent.log – log do agente do Log Analytics
* /var/opt/Microsoft/omsagent/Run/automationworker/Worker.log – log de trabalho de Automação

>[!NOTE]
>O usuário **nxautomation** habilitado como parte do Gerenciamento de Atualizações executa apenas runbooks assinados.

## <a name="runbook-permissions"></a>Permissões de runbook

Um runbook precisa de permissões para autenticação no Azure, por meio de credenciais. Consulte [visão geral da autenticação de automação do Azure](automation-security-overview.md).

## <a name="modules"></a>Módulos

A Automação do Azure dá suporte a vários módulos padrão, incluindo alguns módulos AzureRM (AzureRM.Automation) e um módulo que contém vários cmdlets internos. Também há suporte para módulos instaláveis, incluindo os módulos AZ (AZ.Automation), que atualmente estão sendo usados em detrimento dos módulos AzureRM. Para obter detalhes dos módulos disponíveis para seus runbooks e suas configurações DSC, confira [Gerenciar módulos na Automação do Azure](shared-resources/modules.md).

## <a name="certificates"></a>Certificados

A Automação do Azure usa [certificados](shared-resources/certificates.md) para autenticação no Azure ou os adiciona ao Azure ou a recursos de terceiros. Os certificados são armazenados com segurança para acesso por runbooks e configurações DSC.

Seus runbooks podem usar certificados autoassinados, que não são assinados por uma CA (autoridade de certificação). Confira [Criar um certificado](shared-resources/certificates.md#create-a-new-certificate).

## <a name="jobs"></a>Trabalhos

A Automação do Azure dá suporte a um ambiente para executar trabalhos da mesma conta de Automação. Um único runbook pode ter muitos trabalhos em execução ao mesmo tempo. Quanto mais trabalhos você executar ao mesmo tempo, mais frequentemente eles poderão ser enviados à mesma área restrita. 

Os trabalhos em execução no mesmo processo de área restrita podem afetar uns aos outros. Um exemplo é a execução do cmdlet [Disconnect-AzAccount](/powershell/module/az.accounts/disconnect-azaccount). A execução desse cmdlet desconecta cada trabalho de runbook no processo de área restrita compartilhada. Para obter um exemplo de como trabalhar com esse cenário, confira [Evitar trabalhos simultâneos](manage-runbooks.md#prevent-concurrent-jobs).

>[!NOTE]
>Os trabalhos do PowerShell iniciados em um runbook executado em uma área restrita do Azure podem não ser executados no [modo de linguagem completa do PowerShell](/powershell/module/microsoft.powershell.core/about/about_language_modes).

### <a name="job-statuses"></a>Status de trabalho

A tabela a seguir descreve os diferentes status possíveis para um trabalho. Você pode exibir um resumo do status para todos os trabalhos de runbook ou analisar detalhes de um trabalho de runbook específico no portal do Azure. Você também pode configurar a integração com o seu workspace do Log Analytics a fim de encaminhar fluxos de trabalho e status do trabalho do runbook. Para obter mais informações sobre a integração com logs do Azure Monitor, confira [Encaminhar status do trabalho e fluxos de trabalho da Automação para logs do Azure Monitor](automation-manage-send-joblogs-log-analytics.md). Confira também [Obter status do trabalho](manage-runbooks.md#obtain-job-statuses) para ver um exemplo de como trabalhar com status em um runbook.

| Status | Descrição |
|:--- |:--- |
| Desativado |O trabalho está sendo ativado. |
| Concluído |Operação concluída com sucesso. |
| Falhou |Falha na compilação de um runbook gráfico ou do Fluxo de Trabalho do PowerShell. Um runbook do PowerShell não pôde ser iniciado, ou o trabalho teve uma exceção. Confira [Tipos de runbook da Automação do Azure](automation-runbook-types.md).|
| Erro, aguardando recursos |O trabalho falhou porque atingiu o limite de [fração justa](#fair-share) três vezes e iniciou do mesmo ponto de verificação ou desde o início do runbook em cada uma das vezes. |
| Em fila |O trabalho está aguardando que os recursos em um trabalho de Automação fiquem disponíveis para que possam ser iniciados. |
| Continuando |O sistema está retomando o trabalho depois que ele ter sido suspenso. |
| Executando |O trabalho está em execução. |
| Executando, aguardando recursos |O trabalho foi descarregado porque atingiu o limite de compartilhamento justo. Ele continuará em breve a partir de seu último ponto de verificação. |
| Iniciando |O trabalho foi atribuído a um trabalhador e o sistema está iniciando-o. |
| Parado |O trabalho foi interrompido pelo usuário antes de ser concluído. |
| Parando |O sistema está parando o trabalho. |
| Suspenso |Aplica-se somente a [runbooks gráficos ou de Fluxo de Trabalho do PowerShell](automation-runbook-types.md). O trabalho foi suspenso pelo usuário, pelo sistema ou por um comando no runbook. Se um runbook não tiver um ponto de verificação, ele começará do início. Se ele tiver um ponto de verificação, poderá iniciar novamente e retomar no último ponto de verificação. O sistema só suspende o runbook quando ocorre uma exceção. Por padrão, a variável `ErrorActionPreference` é definida como Continuar, indicando que o trabalho continua em execução em caso de erro. Se a variável de preferência for definida como Parar, o trabalho será suspenso com um erro.  |
| Suspensão |Aplica-se somente a [runbooks gráficos ou de Fluxo de Trabalho do PowerShell](automation-runbook-types.md). O sistema está tentando suspender o trabalho por solicitação do usuário. O runbook precisa atingir seu próximo ponto de verificação antes de poder ser suspenso. Se já tiver passado seu último ponto de verificação, ele será concluído antes que possa ser suspenso. |

## <a name="activity-logging"></a>Log de atividades

A execução de runbooks na Automação do Azure grava detalhes em um log de atividades da conta de Automação. Para obter detalhes sobre como usar o log, confira [Recuperar detalhes do log de atividades](manage-runbooks.md#retrieve-details-from-activity-log).

## <a name="exceptions"></a>Exceções

Esta seção descreve algumas maneiras de lidar com exceções ou problemas intermitentes em seus runbooks. Um exemplo é uma exceção WebSocket. A manipulação correta de exceções impede que falhas de rede transitórias causem falha nos runbooks.

### <a name="erroractionpreference"></a>ErrorActionPreference

A variável [ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) determina como o PowerShell responde a um erro de não encerramento. Os erros de finalização sempre são encerrados e não são afetados por `ErrorActionPreference`.

Quando o runbook usa `ErrorActionPreference`, um erro que normalmente é de não encerramento, como `PathNotFound` do cmdlet [Get-ChildItem](/powershell/module/microsoft.powershell.management/get-childitem), interrompe a conclusão do runbook. O exemplo a seguir mostra o uso de `ErrorActionPreference`. O comando final [Write-Output](/powershell/module/microsoft.powershell.utility/write-output) nunca é executado, pois o script é interrompido.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-ChildItem -path nofile.txt
Write-Output "This message will not show"
```

### <a name="try-catch-finally"></a>Try Catch Finally

[Try Catch Finally](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) é usado em scripts do PowerShell para lidar com erros de encerramento. O script pode usar esse mecanismo para capturar exceções específicas ou gerais. A instrução `catch` deve ser usada para rastrear ou tentar lidar com erros. O exemplo a seguir tenta baixar um arquivo que não existe. Ele captura a exceção `System.Net.WebException` e retorna o último valor para qualquer outra exceção.

```powershell-interactive
try
{
   $wc = new-object System.Net.WebClient
   $wc.DownloadFile("http://www.contoso.com/MyDoc.doc")
}
catch [System.Net.WebException]
{
    "Unable to download MyDoc.doc from http://www.contoso.com."
}
catch
{
    "An error occurred that could not be resolved."
}
```

### <a name="throw"></a>Throw

[Throw](/powershell/module/microsoft.powershell.core/about/about_throw) pode ser usado para gerar um erro de encerramento. Esse mecanismo pode ser útil ao definir sua lógica em um runbook. Se o script atender a um critério que deve encerrá-lo, ele poderá usar a instrução `throw` para o encerramento. O exemplo a seguir usa essa instrução para mostrar um parâmetro de função necessário.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

## <a name="errors"></a>Errors

Seus runbooks devem lidar com erros. A automação do Azure dá suporte a dois tipos de erros do PowerShell, de encerramento e de não encerramento. 

Os erros de encerramento param a execução do runbook quando ocorrem. O runbook é finalizado com um status de trabalho de Falha.

Os erros de não encerramento permitem que o script continue mesmo depois de ocorrerem. Um exemplo de erro de não encerramento é aquele que ocorre quando um runbook usa o cmdlet `Get-ChildItem` com um caminho que não existe. O PowerShell vê que o caminho não existe, gera um erro e continua até a próxima pasta. O erro nesse caso não define o status de trabalho do runbook como Falha, e o trabalho pode até ser concluído. Para forçar um runbook a parar se houver um erro sem finalização, você pode usar `ErrorAction Stop` no cmdlet.

## <a name="calling-processes"></a>Chamar processos

Os runbooks executados em áreas restritas do Azure não oferecem suporte a processos de chamada, como executáveis (arquivos **.exe**) ou subprocessos. O motivo disso é que uma área restrita do Azure é um processo compartilhado executado em um contêiner que pode não ser capaz de acessar todas as APIs subjacentes. Para cenários que exigem software de terceiros ou chamadas para subprocessos, você deve executar um runbook em um [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md).

## <a name="device-and-application-characteristics"></a>Características de dispositivo e aplicativo

Os trabalhos de runbook em áreas restritas do Azure não podem acessar nenhuma característica de dispositivo ou aplicativo. A API mais comum usada para consultar as métricas de desempenho no Windows é a WMI, com algumas das métricas comuns incluindo uso de memória e de CPU. No entanto, não importa qual API é usada, pois os trabalhos em execução na nuvem não podem acessar a implementação Microsoft de WBEM (Web-Based Enterprise Management). Essa plataforma se baseia no modelo CIM (Common Information Model), fornecendo os padrões do setor para definir características de dispositivo e aplicativo.

## <a name="webhooks"></a>Webhooks

Os serviços externos, como o Azure DevOps Services e o GitHub, podem iniciar um runbook na automação do Azure. Para fazer esse tipo de inicialização, o serviço usa um [webhook](automation-webhooks.md) por meio de uma única solicitação HTTP. O uso de um webhook permite que os runbooks sejam iniciados sem a implementação de um recurso completo da Automação do Azure.

## <a name="shared-resources"></a><a name="fair-share"></a>Recursos compartilhados

Para compartilhar recursos entre todos os runbooks na nuvem, o Azure usa um conceito chamado compartilhamento justo. Usando o compartilhamento justo, o Azure descarrega ou interrompe temporariamente qualquer trabalho que tenha sido executado por mais de três horas. Os trabalhos de [runbooks do PowerShell](automation-runbook-types.md#powershell-runbooks) e [runbooks Python](automation-runbook-types.md#python-runbooks) são interrompidos e não são reiniciados, e o status do trabalho é mostrado como Parado.

Para tarefas de Automação do Azure de longa execução, recomendamos usar um Hybrid Runbook Worker. Os Hybrid Runbook Workers não são limitados por fração justa e não limitam o tempo de execução de um runbook. Os outros [limites](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) do trabalho se aplicam a áreas restritas do Azure e ao Hybrid Runbook Workers. Embora Hybrid runbook Workers não sejam limitados pelo limite de compartilhamento justo de três horas, você deve desenvolver runbooks para executar em trabalhos que dão suporte a reinicializações de problemas de infraestrutura local inesperados.

Outra opção é otimizar o runbook usando runbooks filhos. Por exemplo, seu runbook pode executar um loop por meio da mesma função em vários recursos, como em uma operação de banco de dados em vários bancos. Você pode mover essa função para um [runbook filho](automation-child-runbooks.md) e fazer com que seu runbook a chame usando [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook). Cada um desses runbooks filhos é executado paralelamente em processos separados.

Esse comportamento reduz a quantidade total de tempo para o runbook pai ser concluído. O runbook pode usar o cmdlet [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) para verificar o status do trabalho de um runbook filho, caso ainda tenha mais operações após a conclusão do runbook filho.

## <a name="next-steps"></a>Próximas etapas

* Para começar a usar um runbook do PowerShell, confira [Tutorial: criar um runbook do PowerShell](learn/automation-tutorial-runbook-textual-powershell.md).
* Para se familiarizar com os runbooks, confira [Gerenciar runbooks na Automação do Azure](manage-runbooks.md).
* Para obter detalhes do PowerShell, confira [Documentos do PowerShell](/powershell/scripting/overview).
* Para obter uma referência de cmdlet do PowerShell, confira [Az.Automation](/powershell/module/az.automation#automation).

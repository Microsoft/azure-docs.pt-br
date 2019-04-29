---
title: Migrar do Orchestrator para Automação do Azure
description: Descreve como migrar runbooks e pacotes de integração do System Center Orchestrator para a Automação do Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ae47cba8f8e9a7cdf914c0b3ea5dfb9fa6c259a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60738206"
---
# <a name="migrating-from-orchestrator-to-azure-automation-beta"></a>Migrando do Orchestrator para a Automação do Azure (Beta)
Os runbooks no [System Center Orchestrator](https://technet.microsoft.com/library/hh237242.aspx) são baseados nas atividades de pacotes de integração que são escritos especificamente para o Orchestrator, enquanto os runbooks na Automação do Azure são baseados no Windows PowerShell.  [runbooks gráficos](automation-runbook-types.md#graphical-runbooks) na Automação do Azure têm uma aparência semelhante aos runbooks do Orchestrator, com suas atividades representando cmdlets do PowerShell, runbooks filhos e ativos.

O [System Center Orchestrator Migration Toolkit](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) inclui ferramentas para ajudá-lo na conversão de runbooks do Orchestrator para a Automação do Azure.  Além de converter os próprios runbooks, você deve converter os pacotes de integração com as atividades que os runbooks usam para os módulos de integração com os cmdlets do Windows PowerShell.  

Eis o processo básico para converter runbooks do Orchestrator para a Automação do Azure.  Cada uma dessas etapas é descrita detalhadamente nas seções a seguir.

1. Baixe o [System Center Orchestrator Migration Toolkit](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) que contém as ferramentas e os módulos discutidos neste artigo.
2. Importe o [Módulo de atividades padrão](#standard-activities-module) na Automação do Azure.  Isso inclui versões convertidas de atividades padrão do Orchestrator que podem ser usadas por runbooks convertidos.
3. Importe os [Módulos de Integração do System Center Orchestrator](#system-center-orchestrator-integration-modules) na Automação do Azure para os pacotes de integração usados pelos seus runbooks que acessam o System Center.
4. Converta os pacotes personalizados e de integração de terceiros usando o [Conversor de Pacote de Integração](#integration-pack-converter) e importe para a Automação do Azure.
5. Converta os runbooks do Orchestrator usando o [Runbook Converter](#runbook-converter) e instale na Automação do Azure.
6. Crie manualmente ativos do Orchestrator necessários na Automação do Azure, já que o Runbook Converter não converte esses recursos.
7. Configure um [Hybrid Runbook Worker](#hybrid-runbook-worker) em seu data center local para executar runbooks convertidos que acessarão recursos locais.

## <a name="service-management-automation"></a>Automação de Gerenciamento de Serviços
[Automação de Gerenciamento de Serviços](https://technet.microsoft.com/library/dn469260.aspx) ) armazena e executa runbooks em seu data center local, como o Orchestrator, e usa os mesmo módulos de integração que a Automação do Azure. O [Runbook Converter](#runbook-converter) converte runbooks do Orchestrator em runbooks gráficos, que, no entanto, não têm suporte no SMA.  Você ainda pode instalar o [Módulo de Atividades Standard](#standard-activities-module) e os [Módulos de Integração do System Center Orchestrator](#system-center-orchestrator-integration-modules) no SMA, mas deve [reescrever seus runbooks](https://technet.microsoft.com/library/dn469262.aspx) manualmente.

## <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker
Os runbooks do Orchestrator são armazenados em um servidor de banco de dados e executados em servidores runbook, ambos em seu data center local.  Os runbooks na Automação do Azure são armazenados na nuvem do Azure e podem ser executados em seu data center local usando um [Runbook Worker Híbrido](automation-hybrid-runbook-worker.md).  É dessa forma que você normalmente irá executar runbooks convertidos do Orchestrator, já que foram projetados para ser executados em servidores locais.

## <a name="integration-pack-converter"></a>Conversor de Pacote de Integração
O Conversor de Pacote de Integração converte pacotes de integração criados usando o [OIT (Orchestrator Integration Toolkit)](https://technet.microsoft.com/library/hh855853.aspx) em módulos de integração baseados no Windows PowerShell que podem ser importados para a Automação do Azure ou para o SMA.  

Quando você executa o Conversor de Pacote de Integração, aparece um assistente que permite que você selecione um arquivo de pacote de integração (.oip).  Em seguida, o assistente lista as atividades incluídas no pacote de integração e permite que você selecione quais serão migrados.  Quando você conclui o assistente, ele cria um módulo de integração que inclui um cmdlet correspondente para cada uma das atividades no pacote de integração original.

### <a name="parameters"></a>parâmetros
Todas as propriedades de uma atividade no pacote de integração são convertidas em parâmetros do cmdlet correspondente no módulo de integração.  Os cmdlets do Windows PowerShell têm um conjunto de [parâmetros comuns](https://technet.microsoft.com/library/hh847884.aspx) que podem ser usados com todos os cmdlets.  Por exemplo, o parâmetro -Verbose faz com que um cmdlet produza informações detalhadas sobre a sua operação.  Nenhum cmdlet pode ter um parâmetro com o mesmo nome de um parâmetro comum.  Se uma atividade possui uma propriedade com o mesmo nome de um parâmetro comum, o assistente solicitará que você forneça outro nome para o parâmetro.

### <a name="monitor-activities"></a>Monitorar atividades
Monitore runbooks na inicialização do Orchestrator com uma [atividade de monitoramento](https://technet.microsoft.com/library/hh403827.aspx) e execute continuamente aguardando para ser chamada por um evento específico.  A Automação do Azure não dá suporte a runbooks de monitoramento, então nenhuma atividade de monitoramento no pacote de integração será convertida.  Em vez disso, um cmdlet de espaço reservado é criado no módulo de integração para a atividade de monitoramento.  Esse cmdlet não tem nenhuma funcionalidade, mas permite a instalação de qualquer runbook convertido que o utilize.  Esse runbook não poderá ser executado na Automação do Azure, mas poderá ser instalado para que você possa modificá-lo.

### <a name="integration-packs-that-cannot-be-converted"></a>Pacotes de integração que não podem ser convertidos
Os pacotes de integração que não foram criados com o OIT não podem ser convertidos com o Conversor de Pacote de Integração. Há também alguns pacotes de integração fornecidos pela Microsoft que atualmente não podem ser convertidos com essa ferramenta.  As versões convertidas desses pacotes de integração foram [fornecidas para download](#system-center-orchestrator-integration-modules) para que possam ser instaladas na Automação do Azure ou no Service Management Automation.

## <a name="standard-activities-module"></a>Módulo de atividades padrão
O Orchestrator inclui um conjunto de [atividades padrão](https://technet.microsoft.com/library/hh403832.aspx) que não está incluído em um pacote de integração, mas é usado por vários runbooks.  O módulo de atividades padrão é um módulo de integração que inclui um cmdlet equivalente para cada uma dessas atividades.  Você deve instalar esse módulo de integração na Automação do Azure antes de importar todos os runbooks convertidos que usam uma atividade padrão.

Além de dar suporte a runbooks convertidos, os cmdlets no módulo de atividades padrão podem ser usados por alguém que esteja familiarizado com o Orchestrator para criar novos runbooks na Automação do Azure.  Embora as funcionalidades de todas as atividades padrão possam ser executadas com os cmdlets, eles podem operar de forma diferente.  Os cmdlets no módulo de atividades padrão convertido funcionarão da mesma forma que as suas atividades correspondentes e usarão os mesmos parâmetros.  Isso pode ajudar o criador do runbook do Orchestrator existente na sua transição para os runbooks da Automação do Azure.

## <a name="system-center-orchestrator-integration-modules"></a>Módulos de Integração do System Center Orchestrator
A Microsoft fornece [pacotes de integração](https://technet.microsoft.com/library/hh295851.aspx) para a criação de runbooks de automação de outros produtos e componentes do System Center.  Alguns desses pacotes de integração baseiam-se atualmente no OIT, mas não podem ser convertidos em módulos de integração no momento devido a problemas conhecidos.  [Módulos de Integração do System Center Orchestrator](https://www.microsoft.com/download/details.aspx?id=49555) incluem versões convertidas desses pacotes de integração que podem ser importadas para a Automação do Azure e para o SMA.  

Até o momento em que a versão RTM dessa ferramenta for lançada, versões atualizadas dos pacotes de integração baseados em OIT que possam ser convertidos com o Conversor de Pacote de Integração serão publicadas.  Também serão fornecidas diretrizes para ajudar você a converter runbooks usando atividades de pacotes de integração não baseados em OIT.

## <a name="runbook-converter"></a>Runbook Converter
O Runbook Converter converte runbooks do Orchestrator em [runbooks gráficos](automation-runbook-types.md#graphical-runbooks) que podem ser importados para a Automação do Azure.  

O Runbook Converter é implementado como um módulo do PowerShell com um cmdlet chamado **ConvertFrom SCORunbook** , que executa a conversão.  Quando você instala a ferramenta, ela criará um atalho para uma sessão do PowerShell que carrega o cmdlet.   

A seguir, um processo básico para converter um runbook do Orchestrator e para importá-lo para a Automação do Azure.  As seções a seguir fornecem mais detalhes sobre o uso da ferramenta e o trabalho com runbooks convertidos.

1. Exporte um ou mais runbooks do Orchestrator.
2. Obtenha módulos de integração para todas as atividades no runbook.
3. Converta os runbooks do Orchestrator no arquivo exportado.
4. Examine as informações nos logs para validar a conversão e determinar todas as tarefas manuais necessárias.
5. Importe runbooks convertidos na Automação do Azure.
6. Crie ativos necessários na Automação do Azure.
7. Edite o runbook na Automação do Azure para modificar todas as atividades necessárias.

### <a name="using-runbook-converter"></a>Usando o Runbook Converter
A sintaxe de **ConvertFrom-SCORunbook** é a seguinte:

```powershell
ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>
```

* RunbookPath: caminho do arquivo de exportação que contém os runbooks para converter.
* Module: lista delimitada por vírgula dos módulos de integração que contêm atividades nos runbooks.
* OutputFolder: o caminho da pasta para criar runbooks gráficos convertidos.

O comando de exemplo a seguir converte os runbooks em um arquivo de exportação denominado **MyRunbooks.ois_export**.  Esses runbooks usam os pacotes de integração do Data Protection Manager e o Active Directory.

```powershell
ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"
```

### <a name="log-files"></a>Arquivos de log
O Runbook Converter criará os arquivos de log a seguir no mesmo local que o runbook convertido.  Se os arquivos já existirem, serão substituídos pelas informações da última conversão.

| Arquivo | Conteúdo |
|:--- |:--- |
| Runbook Converter - progress.log |Etapas detalhadas da conversão, incluindo informações para cada atividade convertida com êxito e avisos para cada atividade não convertida. |
| Runbook Converter - summary.log |Resumo da última conversão, incluindo avisos e tarefas de acompanhamento que você precise executar, como a criação de uma variável necessária para o runbook convertido. |

### <a name="exporting-runbooks-from-orchestrator"></a>Exportando runbooks do Orchestrator
O Runbook Converter funciona com um arquivo de exportação do Orchestrator com um ou mais runbooks.  Ele cria um runbook de Automação do Azure correspondente para cada registro de execuções do Orchestrator no arquivo de exportação.  

Para exportar um runbook do Orchestrator, clique no nome do runbook no Runbook Designer e selecione **Exportar**.  Para exportar todos os runbooks em uma pasta, clique no nome da pasta e selecione **Exportar**.

### <a name="runbook-activities"></a>Atividades de runbook
O Runbook Converter converte cada atividade no runbook do Orchestrator em uma atividade correspondente na Automação do Azure.  Para as atividades que não puderem ser convertidas, uma atividade de espaço reservado será criada no runbook com texto de aviso.  Depois de importar o runbook convertido para a Automação do Azure, você deverá substituir essas atividades com atividades válidas que executam a funcionalidade necessária.

Todas as atividades do Orchestrator no [Módulo de Atividades Padrão](#standard-activities-module) serão convertidas.  Há algumas atividades padrão do Orchestrator que não estão neste módulo e não são convertidas.  Por exemplo, **Enviar Evento de Plataforma** não tem equivalente na Automação do Azure, já que é específico do Orchestrator.

[Atividades de monitoração](https://technet.microsoft.com/library/hh403827.aspx) não são convertidas, já que não há nenhum equivalente na Automação do Azure.  A exceção é atividades de monitoração em [pacotes de integração convertidos](#integration-pack-converter) , que serão convertidas para a atividade de espaço reservado.

As atividades de um [pacote de integração convertido](#integration-pack-converter) são convertidas se você fornecer o caminho para o módulo de integração com o parâmetro **modules** .  Para Pacotes de Integração do System Center, você pode usar os [Módulos de integração do System Center Orchestrator](#system-center-orchestrator-integration-modules).

### <a name="orchestrator-resources"></a>Recursos do Orchestrator
O Runbook Converter converte somente runbooks, não outros recursos do Orchestrator, como contadores, variáveis ou conexões.  Não há suporte para contadores na Automação do Azure.  Há suporte para variáveis e conexões, mas você deve criá-los manualmente.  Os arquivos de log informam se o runbook exige esses recursos e especifica os recursos correspondentes que você precisa criar na Automação do Azure para que o runbook convertido funcione corretamente.

Por exemplo, um runbook pode usar uma variável para popular um valor específico em uma atividade.  O runbook convertido converterá a atividade e especificará um ativo de variável na Automação do Azure com o mesmo nome da variável do Orchestrator.  Isso será observado no arquivo **Runbook Converter - summary.log** criado após a conversão.  Você precisará criar manualmente esse ativo de variável na Automação do Azure antes de usar o runbook.

### <a name="input-parameters"></a>Parâmetros de entrada
Os runbooks do Orchestrator aceitam parâmetros de entrada com a atividade **Inicializar Dados** .  Se o runbook sendo convertido incluir essa atividade, então um [parâmetro de entrada](automation-graphical-authoring-intro.md#runbook-input-and-output) será criado  no runbook da Automação do Azure para cada parâmetro na atividade.  Uma atividade de [Controle de fluxo de trabalho de script](automation-graphical-authoring-intro.md#activities) é criada no runbook convertido, que recupera e retorna cada parâmetro.  Todas as atividades no runbook que usam um parâmetro de entrada fazem referência à saída dessa atividade.

O motivo para usar essa estratégia é refletir melhor a funcionalidade do runbook do Orchestrator.  As atividades em novos runbooks gráficos devem fazer referência direta a parâmetros de entrada usando uma fonte de dados de entrada de runbook.

### <a name="invoke-runbook-activity"></a>Invocar a atividade de Runbook
Os runbooks no Orchestrator iniciam outros runbooks com a atividade **Invocar Runbook** . Se o runbook sendo convertido incluir essa atividade e a opção **Aguardar a conclusão** estiver definida, uma atividade de runbook será criada para ela no runbook convertido.  Se a opção **Aguardar a conclusão** não estiver definida, então, uma atividade Script do Fluxo de Trabalho será criada usando **Start-AzureAutomationRunbook** para iniciar o runbook.  Depois de importar o runbook convertido para Automação do Azure, você deverá modificar essa atividade com as informações especificadas na atividade.

## <a name="related-articles"></a>Artigos relacionados
* [System Center 2012 - Orchestrator](https://technet.microsoft.com/library/hh237242.aspx)
* [Automação de Gerenciamento de Serviço](https://technet.microsoft.com/library/dn469260.aspx)
* [Runbook Worker Híbrido](automation-hybrid-runbook-worker.md)
* [Atividades padrão do Orchestrator](https://technet.microsoft.com/library/hh403832.aspx)
* [Baixar o Kit de Ferramentas de Migração do System Center Orchestrator](https://www.microsoft.com/en-us/download/details.aspx?id=47323)


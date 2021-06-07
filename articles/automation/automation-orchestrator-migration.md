---
title: Migrar do Orchestrator para a Automação do Azure (Beta)
description: Este artigo diz como migrar runbooks e pacotes de integração do Orchestrator para a Automação do Azure.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 6ee4a09df0f95cb809db0e5c0e63d195ee5cfdff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896928"
---
# <a name="migrate-from-orchestrator-to-azure-automation-beta"></a>Migrar do Orchestrator para a Automação do Azure (Beta)

Os runbooks no [System Center 2012 – Orchestrator](/previous-versions/system-center/system-center-2012-R2/hh237242(v=sc.12)) são baseados nas atividades de pacotes de integração que são escritos especificamente para o Orchestrator, enquanto os runbooks na Automação do Azure são baseados no Windows PowerShell. [Runbooks gráficos](automation-runbook-types.md#graphical-runbooks) na Automação do Azure têm uma aparência semelhante aos runbooks do Orchestrator, com suas atividades representando cmdlets do PowerShell, runbooks filhos e ativos. Além de converter os próprios runbooks, você deve converter os pacotes de integração com as atividades que os runbooks usam para os módulos de integração com os cmdlets do Windows PowerShell. 

O [SMA](/previous-versions/system-center/system-center-2012-R2/dn469260(v=sc.12)) (Service Management Automation) armazena e executa runbooks em seu datacenter local, como o Orchestrator, e usa os mesmo módulos de integração que a Automação do Azure. O Runbook Converter converte runbooks do Orchestrator em runbooks gráficos, que, não têm suporte no SMA. Você ainda pode instalar o Módulo de Atividades Standard e os Módulos de Integração do System Center Orchestrator no SMA, mas deve [reescrever seus runbooks](/system-center/sma/authoring-automation-runbooks) manualmente.

## <a name="download-the-orchestrator-migration-toolkit"></a>Baixar o kit de ferramentas de migração do Orchestrator

A primeira etapa da migração é baixar o [Kit de ferramentas de migração do System Center Orchestrator](https://www.microsoft.com/download/details.aspx?id=47323). Esse kit de ferramentas ajuda na conversão de runbooks do Orchestrator para a Automação do Azure.  

## <a name="import-the-standard-activities-module"></a>Importar o módulo de atividades padrão

Importe o [Módulo de atividades padrão](/system-center/orchestrator/standard-activities) na Automação do Azure. Isso inclui versões convertidas de atividades padrão do Orchestrator que os runbooks gráficos convertidos podem usar.

## <a name="import-orchestrator-integration-modules"></a>Importar módulos de integração do Orchestrator

A Microsoft fornece [pacotes de integração](/previous-versions/system-center/packs/hh295851(v=technet.10)) para a criação de runbooks de automação de outros produtos e componentes do System Center. Alguns desses pacotes de integração baseiam-se atualmente no OIT, mas não podem ser convertidos em módulos de integração no momento devido a problemas conhecidos. Importe os [Módulos de Integração do System Center Orchestrator](https://www.microsoft.com/download/details.aspx?id=49555) na Automação do Azure para os pacotes de integração usados pelos seus runbooks que acessam o System Center. Este pacote inclui versões convertidas dos pacotes de integração que podem ser importadas para a Automação do Azure e para o SMA.  

## <a name="convert-integration-packs"></a>Converter pacotes de integração

Use o [Conversor de Pacote de Integração](/system-center/orchestrator/orch-integration-toolkit/integration-pack-wizard) para converter os pacotes de integração criados usando o [OIT (Orchestrator Integration Toolkit)](/previous-versions/system-center/developer/hh855853(v=msdn.10)) em módulos de integração baseados no Windows PowerShell que podem ser importados para a Automação do Azure ou para o SMA. Ao executar o Conversor de Pacote de Integração, será exibido um assistente que permitirá que você selecione um arquivo de pacote de integração (.oip). Em seguida, o assistente lista as atividades incluídas no pacote de integração e permite que você selecione quais atividades serão migradas. Quando você conclui o assistente, ele cria um módulo de integração que inclui um cmdlet correspondente para cada uma das atividades no pacote de integração original.

> [!NOTE]
> Não é possível usar o Conversor de Pacote de Integração para converter pacotes de integração que não foram criados com OIT. Há também alguns pacotes de integração fornecidos pela Microsoft que atualmente não podem ser convertidos com essa ferramenta. As versões convertidas desses pacotes de integração são fornecidas para download para que possam ser instaladas na Automação do Azure ou no Service Management Automation.

### <a name="parameters"></a>Parâmetros

Todas as propriedades de uma atividade no pacote de integração são convertidas em parâmetros do cmdlet correspondente no módulo de integração.  Os cmdlets do Windows PowerShell têm um conjunto de [parâmetros comuns](/powershell/module/microsoft.powershell.core/about/about_commonparameters) que podem ser usados com todos os cmdlets. Por exemplo, o parâmetro -Verbose faz com que um cmdlet produza informações detalhadas sobre a sua operação.  Nenhum cmdlet pode ter um parâmetro com o mesmo nome de um parâmetro comum. Se uma atividade tiver uma propriedade com o mesmo nome de um parâmetro comum, o assistente solicitará que você forneça outro nome para o parâmetro.

### <a name="monitor-activities"></a>Monitorar atividades

Monitore runbooks na inicialização do Orchestrator com uma [atividade de monitoramento](/previous-versions/system-center/system-center-2012-R2/hh403827(v=sc.12)) e execute continuamente aguardando para ser chamada por um evento específico. A Automação do Azure não dá suporte a runbooks de monitoramento, então nenhuma atividade de monitoramento no pacote de integração é convertida. Em vez disso, um cmdlet de espaço reservado é criado no módulo de integração para a atividade de monitoramento.  Esse cmdlet não tem nenhuma funcionalidade, mas permite a instalação de qualquer runbook convertido que o utilize. Esse runbook não pode ser executado na Automação do Azure, mas poderá ser instalado para que você possa modificá-lo.

O Orchestrator inclui um conjunto de [atividades padrão](/previous-versions/system-center/system-center-2012-R2/hh403832(v=sc.12)) que não está incluído em um pacote de integração, mas é usado por vários runbooks.  O módulo de atividades padrão é um módulo de integração que inclui um cmdlet equivalente para cada uma dessas atividades. Você deve instalar esse módulo de integração na Automação do Azure antes de importar todos os runbooks convertidos que usam uma atividade padrão.

Além de dar suporte a runbooks convertidos, os cmdlets no módulo de atividades padrão podem ser usados por alguém que esteja familiarizado com o Orchestrator para criar novos runbooks na Automação do Azure. Embora as funcionalidades de todas as atividades padrão possam ser executadas com os cmdlets, eles podem operar de forma diferente. Os cmdlets no módulo de atividades padrão convertido funcionarão da mesma forma que as suas atividades correspondentes e usarão os mesmos parâmetros. Isso pode ajudá-lo a fazer a transição para runbooks de Automação do Azure.

## <a name="convert-orchestrator-runbooks"></a>Converter runbooks do Orchestrator

O Runbook Converter do Orchestrator converte runbooks do Orchestrator em [runbooks gráficos](automation-runbook-types.md#graphical-runbooks) que podem ser importados para a Automação do Azure. O runbook Converter é implementado como um módulo do PowerShell com o cmdlet `ConvertFrom-SCORunbook` que faz a conversão. Quando você instala o Converter, ele cria um atalho para uma sessão do PowerShell que carrega o cmdlet.   

Aqui estão as etapas básicas para converter um runbook e importá-lo para a automação do Azure. Detalhes do uso do cmdlet são fornecidos posteriormente nesta seção.

1. Exporte um ou mais runbooks do Orchestrator.
2. Obtenha módulos de integração para todas as atividades no runbook.
3. Converta os runbooks do Orchestrator no arquivo exportado.
4. Examine as informações nos logs para validar a conversão e determinar todas as tarefas manuais necessárias.
5. Importe runbooks convertidos na Automação do Azure.
6. Crie ativos necessários na Automação do Azure.
7. Edite o runbook na Automação do Azure para modificar todas as atividades necessárias.

A sintaxe para `ConvertFrom-SCORunbook` é:

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

### <a name="use-runbook-converter-log-files"></a>Usar arquivos de log do Runbook Converter

O Runbook Converter cria os arquivos de log a seguir no mesmo local que o runbook convertido.  Se os arquivos já existirem, serão substituídos pelas informações da última conversão.

| Arquivo | Conteúdo |
|:--- |:--- |
| Runbook Converter - progress.log |Etapas detalhadas da conversão, incluindo informações para cada atividade convertida com êxito e avisos para cada atividade não convertida. |
| Runbook Converter - summary.log |Resumo da última conversão, incluindo avisos e tarefas de acompanhamento que você precise executar, como a criação de uma variável necessária para o runbook convertido. |

### <a name="export-runbooks-from-orchestrator"></a>Exportar runbooks do Orchestrator

O Runbook Converter funciona com um arquivo de exportação do Orchestrator com um ou mais runbooks.  Ele cria um runbook de Automação do Azure correspondente para cada registro de execuções do Orchestrator no arquivo de exportação.  

Para exportar um runbook do Orchestrator, clique no nome do runbook no Runbook Designer e selecione **Exportar**.  Para exportar todos os runbooks em uma pasta, clique no nome da pasta e selecione **Exportar**.

### <a name="convert-runbook-activities"></a>Converter atividades do Runbook

O Runbook Converter converte cada atividade no runbook do Orchestrator em uma atividade correspondente na Automação do Azure.  Para as atividades que não puderem ser convertidas, uma atividade de espaço reservado será criada no runbook com texto de aviso.  Depois de importar o runbook convertido para a Automação do Azure, você deverá substituir essas atividades com atividades válidas que executam a funcionalidade necessária.

Todas as atividades do Orchestrator no Módulo de Atividades Padrão são convertidas. Há algumas atividades padrão do Orchestrator que não estão neste módulo e não são convertidas. Por exemplo, `Send Platform Event` não tem equivalente na Automação do Azure, já que é específico do Orchestrator.

[Atividades de monitoração](/previous-versions/system-center/system-center-2012-R2/hh403827(v=sc.12)) não são convertidas, já que não há nenhum equivalente na Automação do Azure. A exceção é atividades de monitoramento em pacotes de integração convertidos, que são convertidas para a atividade de espaço reservado.

As atividades de um pacote de integração convertido são convertidas se você fornecer o caminho para o módulo de integração com o parâmetro `modules`. Para Pacotes de Integração do System Center, você pode usar os Módulos de integração do System Center Orchestrator.

### <a name="manage-orchestrator-resources"></a>Gerenciar recursos do Orchestrator

O Runbook Converter converte somente runbooks, não outros recursos do Orchestrator, como contadores, variáveis ou conexões.  Não há suporte para contadores na Automação do Azure.  Há suporte para variáveis e conexões, mas você deve criá-los manualmente. Os arquivos de log informam se o runbook exige esses recursos e especifica os recursos correspondentes que você precisa criar na Automação do Azure para que o runbook convertido funcione corretamente.

Por exemplo, um runbook pode usar uma variável para popular um valor específico em uma atividade.  O runbook convertido converte a atividade e especifica um ativo de variável na Automação do Azure com o mesmo nome da variável do Orchestrator. Essa ação será observada no arquivo **Runbook Converter – summary.log** criado após a conversão. Você deve criar manualmente esse ativo de variável na Automação do Azure antes de usar o runbook.

### <a name="work-with-orchestrator-input-parameters"></a>Trabalhar com parâmetros de entrada do Orchestrator

Os runbooks do Orchestrator aceitam parâmetros de entrada com a atividade `Initialize Data`.  Se o runbook sendo convertido incluir essa atividade, então um [parâmetro de entrada](automation-graphical-authoring-intro.md#handle-runbook-input) será criado  no runbook da Automação do Azure para cada parâmetro na atividade.  Uma atividade de [Controle de fluxo de trabalho de script](automation-graphical-authoring-intro.md#use-activities) é criada no runbook convertido, que recupera e retorna cada parâmetro. Todas as atividades no runbook que usam um parâmetro de entrada fazem referência à saída dessa atividade.

O motivo para usar essa estratégia é refletir melhor a funcionalidade do runbook do Orchestrator.  As atividades em novos runbooks gráficos devem fazer referência direta a parâmetros de entrada usando uma fonte de dados de entrada de runbook.

### <a name="invoke-runbook-activity"></a>Invocar a atividade de Runbook

Os runbooks no Orchestrator iniciam outros runbooks com a atividade `Invoke Runbook`. Se o runbook sendo convertido incluir essa atividade e a opção `Wait for completion` estiver definida, uma atividade de runbook será criada para ela no runbook convertido.  Se a opção `Wait for completion` não estiver definida, então, uma atividade Script do Fluxo de Trabalho será criada usando [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) para iniciar o runbook. Depois de importar o runbook convertido para Automação do Azure, você deverá modificar essa atividade com as informações especificadas na atividade.

## <a name="create-orchestrator-assets"></a>Criar ativos do Orchestrator

O runbook Converter não converte os ativos do Orchestrator. Você deve criar manualmente todos os ativos do Orchestrator necessários na Automação do Azure.

## <a name="configure-hybrid-runbook-worker"></a>Configurar o Hybrid Runbook Worker

O Orchestrator armazena runbooks em um servidor de banco de dados e os executa em servidores runbook, ambos em seu datacenter local. Os runbooks na Automação do Azure são armazenados na nuvem do Azure e podem ser executados em seu datacenter local usando um [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md). Configure o trabalho para executar os runbooks convertidos do Orchestrator, já que foram projetados para serem executados em servidores locais e acessar recursos locais.

## <a name="related-articles"></a>Artigos relacionados

* Para obter detalhes sobre o Orchestrator, confira [System Center 2012 – Orchestrator](/previous-versions/system-center/system-center-2012-R2/hh237242(v=sc.12)).
* Saiba mais sobre como automatizar o gerenciamento de serviços no [Service Management Automation](/previous-versions/system-center/system-center-2012-R2/dn469260(v=sc.12)).
* Os detalhes das atividades do Orchestrator podem ser encontrados em [Atividades padrão do Orchestrator](/previous-versions/system-center/system-center-2012-R2/hh403832(v=sc.12)).
* Para obter o kit de ferramentas de migração do Orchestrator, confira [Baixar o Kit de Ferramentas de Migração do System Center Orchestrator](https://www.microsoft.com/download/details.aspx?id=47323).
* Para obter uma visão geral do Hybrid Runbook Worker da Automação do Azure, confira [Visão geral do Hybrid Runbook Worker](automation-hybrid-runbook-worker.md).

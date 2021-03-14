---
title: Visão geral do Controle de Alterações e Inventário da Automação do Azure
description: Este artigo descreve o Controle de Alterações e o recurso de inventário, que ajuda a identificar alterações de software e serviço da Microsoft em seu ambiente.
services: automation
ms.subservice: change-inventory-management
ms.date: 01/22/2021
ms.topic: conceptual
ms.openlocfilehash: e2371f3de8ed73250bca6639e6c749811c5559ad
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100572605"
---
# <a name="change-tracking-and-inventory-overview"></a>Visão geral do Controle de Alterações e Inventário

Este artigo apresenta o Controle de Alterações e Inventário na Automação do Azure. Esse recurso acompanha as alterações nas máquinas virtuais hospedadas no Azure, no local e em outros ambientes de nuvem para ajudá-lo a identificar problemas operacionais e ambientais com o software gerenciado pelo Gerenciador de pacotes de distribuição. Os itens controlados pelo Controle de Alterações e Inventário incluem:

- Software do Windows
- Software do Linux (pacotes)
- Arquivos do Windows e do Linux
- Chave do registro do Windows
- Serviços Microsoft
- Daemons Linux

> [!NOTE]
> Para rastrear as alterações da propriedade do Azure Resource Manager, confira o [histórico de alterações](../../governance/resource-graph/how-to/get-resource-changes.md) do Azure Resource Graph.

Controle de Alterações e inventário fazem uso do [fim (monitoramento de integridade de arquivo) da central de segurança do Azure](../../security-center/security-center-file-integrity-monitoring.md) para examinar os arquivos do sistema operacional e do aplicativo e o registro do Windows. Enquanto o FIM monitora essas entidades, Controle de Alterações e inventário nativamente controlam:

- Mudanças de software
- Serviços Microsoft
- Daemons Linux

Habilitar todos os recursos incluídos no Controle de Alterações e no inventário pode causar encargos adicionais. Antes de continuar, examine [preços de automação](https://azure.microsoft.com/pricing/details/automation/) e [preços de Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).

Controle de Alterações e o inventário encaminha os dados para Azure Monitor logs, e esses dados coletados são armazenados em um espaço de trabalho Log Analytics. O recurso de monitoramento de integridade de arquivo (FIM) está disponível somente quando o **Azure defender para servidores** está habilitado. Confira [preços](../../security-center/security-center-pricing.md) da central de segurança do Azure para saber mais. O FIM carrega dados no mesmo espaço de trabalho Log Analytics que aquele criado para armazenar dados de Controle de Alterações e inventário. Recomendamos que você monitore seu espaço de trabalho do Log Analytics vinculado para controlar seu uso exato. Para obter mais informações sobre como analisar o uso de dados de logs de Azure Monitor, consulte [gerenciar o uso e o custo](../../azure-monitor/logs/manage-cost-storage.md).

Computadores conectados ao espaço de trabalho de Log Analytics usam o [agente de log Analytics](../../azure-monitor/agents/log-analytics-agent.md) para coletar dados sobre alterações em software instalado, serviços da Microsoft, registro e arquivos do Windows e daemons do Linux em servidores monitorados. Quando os dados estão disponíveis, o agente o envia para Azure Monitor logs para processamento. Os logs de Azure Monitor aplicam a lógica aos dados recebidos, os registram e disponibilizam para análise.

> [!NOTE]
> Controle de Alterações e inventário exigem a vinculação de um espaço de trabalho Log Analytics à sua conta de automação. Para obter uma lista definitiva de regiões compatíveis, confira [Mapeamentos de Workspace do Azure](../how-to/region-mappings.md). Os mapeamentos de região não afetam a capacidade de gerenciar VMs em uma região separada da sua conta de Automação.

## <a name="current-limitations"></a>Limitações atuais

O Controle de Alterações e o inventário não dão suporte ou tem as seguintes limitações:

- Recursão para o rastreamento de registro do Windows
- Sistemas de arquivos de rede
- Métodos de instalação diferentes
- *arquivos **. exe** armazenados no Windows
- Os valores e a coluna **Tamanho máximo de arquivo** não são utilizados na implementação atual.
- Se você tentar coletar mais de 2500 arquivos em um ciclo de coleta de 30 minutos, o desempenho do Controle de Alterações e do inventário poderá ser degradado.
- Se o tráfego de rede for alto, os registros de alteração poderão levar até seis horas para serem exibidos.
- Se você modificar uma configuração enquanto um computador ou servidor for desligado, ele poderá postar alterações pertencentes à configuração anterior.
- Coletando atualizações de hotfix no Windows Server 2016 Core RS3 machines.
- Os daemons do Linux podem mostrar um estado alterado, embora nenhuma alteração tenha ocorrido. Esse problema ocorre devido à maneira como os `SvcRunLevels` dados na tabela Azure monitor [ConfigurationChange](/azure/azure-monitor/reference/tables/configurationchange) são gravados.

## <a name="supported-operating-systems"></a>Sistemas operacionais compatíveis

O Controle de Alterações e Inventário tem suporte em todos os sistemas operacionais que atendem aos requisitos de agente do Log Analytics. Consulte [sistemas operacionais com suporte](../../azure-monitor/agents/agents-overview.md#supported-operating-systems) para obter uma lista das versões do sistema operacional Windows e Linux com suporte no momento pelo agente de log Analytics.

Para entender os requisitos do cliente para o TLS 1,2, confira [imposição tls 1,2 para a automação do Azure](../automation-managing-data.md#tls-12-enforcement-for-azure-automation).

### <a name="python-requirement"></a>Requisito do Python

Controle de Alterações e o inventário dão suporte apenas a Python2. Se seu computador estiver usando um distribuição que não inclui o Python 2 por padrão, você deverá instalá-lo. Os comandos de exemplo a seguir instalarão o Python 2 em distribuições diferentes.

- Red Hat, CentOS, Oracle: `yum install -y python2`
- Ubuntu, Debian: `apt-get install -y python2`
- SUSE: `zypper install -y python2`

O executável python2 deve ter um alias para *Python*.

## <a name="network-requirements"></a>Requisitos de rede

Verifique a [configuração de rede da automação do Azure](../automation-network-configuration.md#update-management-and-change-tracking-and-inventory) para obter informações detalhadas sobre as portas, URLs e outros detalhes de rede necessários para controle de alterações e inventário.

## <a name="enable-change-tracking-and-inventory"></a>Habilitar Controle de Alterações e Inventário

Você pode habilitar Controle de Alterações e inventário das seguintes maneiras:

- Da sua [conta de automação](enable-from-automation-account.md) para um ou mais computadores Azure e não Azure.

- Manualmente para computadores não Azure, incluindo computadores ou servidores registrados com [servidores habilitados para Arc do Azure](../../azure-arc/servers/overview.md). Para computadores híbridos, recomendamos instalar o agente de Log Analytics para Windows primeiro conectando o computador aos [servidores habilitados para Arc do Azure](../../azure-arc/servers/overview.md)e, em seguida, usando Azure Policy para atribuir a política de [implantação do agente de log Analytics ao *Linux* ou ao *Windows* Azure Arc](../../governance/policy/samples/built-in-policies.md#monitoring) . Se você planeja também monitorar as máquinas com Azure Monitor para VMs, use a iniciativa [habilitar Azure monitor para VMs](../../governance/policy/samples/built-in-initiatives.md#monitoring) .

- Para uma única VM do Azure da [página da máquina virtual](enable-from-vm.md) no portal do Azure. Esse cenário está disponível para VMs do Linux e do Windows.

- Para [várias VMs do Azure](enable-from-portal.md), selecionando-as na página de máquinas virtuais no portal do Azure.

## <a name="tracking-file-changes"></a>Controlando alterações de arquivo

Para rastrear alterações em arquivos no Windows e Linux, o Controle de Alterações e Inventário usa os hashes MD5 dos arquivos. O recurso usa os hashes para detectar se foram feitas alterações desde o último inventário.

## <a name="tracking-file-content-changes"></a>Acompanhamento de alterações de conteúdo do arquivo

Controle de Alterações e inventário permite que você exiba o conteúdo de um arquivo do Windows ou Linux. Para cada alteração em um arquivo, o Controle de Alterações e Inventário armazena o conteúdo do arquivo em uma [conta de Armazenamento do Azure](../../storage/common/storage-account-create.md). Quando você estiver acompanhando um arquivo, poderá exibir seu conteúdo antes ou depois de uma alteração. O conteúdo do arquivo pode ser exibido embutido ou lado a lado.

![Exibir alterações em um arquivo](./media/overview/view-file-changes.png)

## <a name="tracking-of-registry-keys"></a>Acompanhamento de chaves do Registro

O Controle de Alterações e o inventário permitem o monitoramento de alterações nas chaves do registro do Windows. O monitoramento permite identificar os pontos de extensibilidade em que código de terceiros e o malware podem ser ativados. A tabela a seguir lista as chaves de Registro pré-configuradas (mas não habilitadas). Para controlar essas chaves, é necessário habilitar cada uma delas.

> [!div class="mx-tdBreakAll"]
> |Chave do Registro | Finalidade |
> | --- | --- |
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | Monitora scripts que são executados na inicialização.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | Monitora scripts que são executados no desligamento.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | Monitora as chaves que são carregadas antes que o usuário entre na conta do Windows. A chave é usada para aplicativos de 32 bits em execução em computadores de 64 bits.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | Monitora as alterações às configurações do aplicativo.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Monitora manipuladores de menu de contexto que se conectam diretamente ao Windows Explorer e geralmente são executados em processo com **explorer.exe**.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | Monitora manipuladores de cabo de cópia que se conectam diretamente ao Windows Explorer e geralmente são executados em processo com **explorer.exe**.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitora o registro do manipulador de sobreposição de ícone.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitora o registro do manipulador de sobreposição de ícone para aplicativos de 32 bits executados em computadores de 64 bits.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitora os novos plug-ins de objeto auxiliar de navegador para o Internet Explorer. Usado para acessar o modelo DOM (Modelo de Objeto do Documento) da página atual e para controlar a navegação.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitora os novos plug-ins de objeto auxiliar de navegador para o Internet Explorer. Usado para acessar o modelo DOM (Modelo de Objeto do Documento) da página atual e para controlar a navegação para aplicativos de 32 bits em execução em computadores de 64 bits.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | Monitora novas extensões do Internet Explorer, tais como menus de ferramentas personalizadas e botões da barra de ferramentas personalizada.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | Monitora novas extensões do Internet Explorer, como menus de ferramentas personalizadas e botões de barra de ferramentas personalizada para aplicativos de 32 bits executados em computadores de 64 bits.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | Monitora os drivers de 32 bits associados com wavemapper, wave1 e wave2, msacm.imaadpcm, .msadpcm, .msgsm610 e vidc. Semelhante à seção [drivers] no arquivo **system.ini**.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | Monitora os drivers de 32 bits associados com wavemapper, wave1 e wave2, msacm.imaadpcm, .msadpcm, .msgsm610 e vidc para aplicativos de 32 bits executados em computadores de 64 bits. Semelhante à seção [drivers] no arquivo **system.ini**.
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | Monitora a lista de DLLs de sistema conhecidas ou comumente usadas. O monitoramento impede que as pessoas explorem as permissões de diretório de aplicativo fracas descartando as versões do cavalo de Troia das DLLs do sistema.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | Monitora a lista de pacotes que pode receber notificações de eventos do **winlogon.exe**, o modelo de suporte de logon interativo para o Windows.

## <a name="recursion-support"></a>Suporte à recursão

O Controle de Alterações e Inventário dá suporte à recursão, o que permite que você especifique curingas para simplificar o rastreamento em diretórios. A recursão também fornece variáveis de ambiente para permitir que você rastreie arquivos entre ambientes com nomes de unidade variados ou dinâmicos. A lista a seguir inclui informações comuns que você deve saber ao configurar a recursão:

- Caracteres curinga são necessários para acompanhar vários arquivos.

- Você pode usar curingas somente no último segmento de um caminho de arquivo, por exemplo, **c:\folder \\ File** _ ou _ */etc/*. conf * *.

- Se uma variável de ambiente tiver um caminho inválido, a validação terá êxito, mas o caminho falhará durante a execução.

- Você deve evitar nomes de caminho gerais ao definir o caminho, pois esse tipo de configuração pode fazer com que muitas pastas sejam percorridas.

## <a name="change-tracking-and-inventory-data-collection"></a>Coleta de dados do Controle de Alterações e Inventário

A tabela a seguir mostra a frequência de coleta de dados para os tipos de alterações com suporte pelo Controle de Alterações e Inventário. Para cada tipo, o instantâneo de dados do estado atual também é atualizado pelo menos a cada 24 horas.

| **Alterar Tipo** | **Frequência** |
| --- | --- |
| Registro do Windows | 50 minutos |
| Arquivo do Windows | 30 minutos |
| Arquivo Linux | 15 minutos |
| Serviços Microsoft | 10 segundos a 30 minutos</br> Padrão: 30 minutos |
| Daemons Linux | 5 minutos |
| Software do Windows | 30 minutos |
| Software Linux | 5 minutos |

A tabela a seguir mostra os limites de item controlados por máquina para Controle de Alterações e Inventário.

| **Recurso** | **Limite** |
|---|---|---|
|Arquivo|500|
|Registro|250|
|Software do Windows (sem incluir hotfixes) |250|
|Pacotes do Linux|1250|
|Serviços|250|
|Daemons|250|

O uso médio de dados do Log Analytics para uma máquina usando o Controle de Alterações e Inventário é de aproximadamente 40 MB por mês, dependendo do seu ambiente. Com o recurso de uso e custos estimados do espaço de trabalho Log Analytics, você pode exibir os dados ingeridos por Controle de Alterações e inventário em um gráfico de uso. Use esta exibição de dados para avaliar o uso de dados e determinar como ele afeta sua fatura. Confira [Entender seu uso e estimar os custos](../../azure-monitor/logs/manage-cost-storage.md#understand-your-usage-and-estimate-costs).

### <a name="microsoft-service-data"></a>Dados de serviço da Microsoft

A frequência da coleta padrão para os serviços da Microsoft é de 30 minutos. Você pode configurar a frequência usando um controle deslizante na guia **Serviços da Microsoft** em **Editar Configurações**.

![Controle deslizante dos serviços da Microsoft](./media/overview/windowservices.png)

Para otimizar o desempenho, o agente do Log Analytics apenas controla as alterações. Definir um limite alto pode perder alterações se o serviço retornar ao seu estado original. Definir a frequência para um valor menor permite capturar alterações que poderiam ser perdidas de outra forma.

> [!NOTE]
> Embora o agente possa controlar as alterações em um intervalo de apenas 10 segundos, os dados ainda demoram alguns minutos para ser exibido no portal do Azure. As alterações que ocorrem durante o tempo de exibição no portal do ainda são controladas e registradas.

## <a name="support-for-alerts-on-configuration-state"></a>Suporte para alertas no estado de configuração

Uma funcionalidade crucial de Controle de Alterações e Inventário é alertar sobre as alterações ao estado de configuração de seu ambiente híbrido. Muitas ações úteis estão disponíveis para disparar em resposta a alertas. Por exemplo, ações no Azure functions, runbooks de automação, WebHooks e similares. O alerta sobre alterações no arquivo **c:\Windows\System32\drivers\etc\hosts** para um computador é uma boa aplicação de alertas para controle de alterações e dados de inventário. Há muitos outros cenários de alerta também, incluindo os cenários de consulta definidos na tabela a seguir.

|Consulta  |Descrição  |
|---------|---------|
|ConfigurationChange <br>&#124; em que ConfigChangeType == "Files" e FileSystemPath contêm " c:\\windows\\system32\\drivers\\"|Útil para controlar alterações a arquivos críticos do sistema.|
|ConfigurationChange <br>&#124; em que FieldsChanged contém "FileContentChecksum" e FileSystemPath == "c:\\windows\\system32\\drivers\\etc\\hosts"|Útil para controlar modificações a arquivos de configuração chave.|
|ConfigurationChange <br>&#124; em que ConfigChangeType == "WindowsServices" e SvcName contêm "w3svc" e SvcState == "Stopped"|Útil para controlar alterações a serviços críticos do sistema.|
|ConfigurationChange <br>&#124; em que ConfigChangeType == "Daemons" e SvcName contêm "ssh" e SvcState!= "Em execução"|Útil para controlar alterações a serviços críticos do sistema.|
|ConfigurationChange <br>&#124; em que ConfigChangeType == "Software" e ChangeCategory == "Added"|Útil para ambientes que precisam bloquear configurações de software.|
|ConfigurationData <br>&#124; em que SoftwareName contém "Monitoring Agent" e CurrentVersion!= "8.0.11081.0"|Útil para ver quais computadores têm uma versão de software desatualizada ou não em conformidade instalada. Esta consulta relata o último estado de configuração relatada, mas não reporta as alterações.|
|ConfigurationChange <br>&#124; em que RegistryKey == @"HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\QualityCompat"| Útil para controlar alterações a chaves antivírus cruciais.|
|ConfigurationChange <br>&#124; em que RegistryKey contém @"HKEY_LOCAL_MACHINE\\SYSTEM\\CurrentControlSet\\Services\\SharedAccess\\Parameters\\FirewallPolicy"| Útil para controlar alterações em configurações de firewall.|

## <a name="next-steps"></a>Próximas etapas

- Para habilitar o de uma conta de automação, consulte [habilitar controle de alterações e inventário de uma conta de automação](enable-from-automation-account.md).

- Para habilitar o no portal do Azure, consulte [habilitar controle de alterações e inventário do portal do Azure](enable-from-portal.md).

- Para habilitar de um runbook, consulte [habilitar controle de alterações e inventário de um runbook](enable-from-runbook.md).

- Para habilitar o de uma VM do Azure, consulte [habilitar controle de alterações e inventário de uma VM do Azure](enable-from-vm.md).

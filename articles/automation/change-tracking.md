---
title: Controlar alterações com a Automação do Azure
description: A solução Controle de Alterações ajuda a identificar alterações no software e Serviço Windows que ocorrem no ambiente.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: bobbytreed
ms.author: robreed
ms.date: 04/29/2019
ms.topic: conceptual
manager: carmonm
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 109c2817b95f535acfb3d6987a7dad57135ee7a0
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478634"
---
# <a name="track-changes-in-your-environment-with-the-change-tracking-solution"></a>Controlar alterações no ambiente com a solução Controle de Alterações

Este artigo ajuda você a usar a solução de Controle de Alterações para identificar facilmente as alterações em seu ambiente. A solução controla as alterações no software Windows e Linux, nos arquivos Windows e chaves do Registro, nos serviços Windows e daemons do Linux. Identificar as alterações de configuração pode ajudá-lo a detectar problemas operacionais.

As alterações ao software instalado, serviços do Windows, do registro do Windows e arquivos e daemons do Linux nos servidores monitorados são enviadas para o serviço do Azure Monitor na nuvem para processamento. A lógica é aplicada aos dados recebidos e o serviço de nuvem registra os dados. Usando as informações no painel Controle de Alterações, você pode ver facilmente as alterações feitas à sua infraestrutura de servidor.

> [!NOTE]
> Controle de alterações de automação do Azure rastreia as alterações nas máquinas virtuais. Para acompanhar as alterações de propriedade do Azure Resource Manager, consulte do Graph do Azure Resource [histórico de alterações](../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="supported-windows-operating-systems"></a>Sistemas operacionais Windows compatíveis

Há suporte oficial para as seguintes versões do sistema operacional Windows para o agente para Windows:

* Windows Server 2008 R2 ou posterior

## <a name="supported-linux-operating-systems"></a>Sistemas operacionais Linux com suporte

As seguintes distribuições Linux têm suporte oficialmente. No entanto, o agente para Linux também pode ser executado em outras distribuições não listadas. Salvo indicação em contrário, todas as versões secundárias são compatíveis com cada versão principal listada.  

### <a name="64-bit"></a>64 bits

* CentOS 6 e 7
* Amazon Linux 2017.09
* Oracle Linux 6 e 7
* Red Hat Enterprise Linux Server 6 e 7
* Debian GNU/Linux 8 e 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS e 18.04 LTS
* SUSE Linux Enterprise Server 12

### <a name="32-bit"></a>32 bits

* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 e 9
* Ubuntu Linux 14.04 LTS and 16.04 LTS

## <a name="onboard"></a>Habilitar Controle de Alterações e Inventário

Para iniciar o controle de alterações, é necessário habilitar a solução Controle de Alterações e Inventário. Há muitas maneiras de integrar computadores ao Controle de Alterações e Inventário. A seguir estão as maneiras recomendadas e compatíveis de integrar a solução.

* [De uma máquina virtual](automation-onboard-solutions-from-vm.md)
* [Da navegação em várias máquinas](automation-onboard-solutions-from-browse.md)
* [Da sua conta de Automação](automation-onboard-solutions-from-automation-account.md)
* [Com um runbook de Automação do Azure](automation-onboard-solutions.md)

## <a name="configuring-change-tracking-and-inventory"></a>Configurar o Controle de Alterações e Inventário

Para saber como integrar computadores para a visita de solução: [Soluções de Automação do Azure de integração](automation-onboard-solutions-from-automation-account.md). Quando você tem uma integração de computador com a solução de Controle de Alterações e de inventário, você pode configurar os itens para rastrear. Ao habilitar um uma chave de registro ou arquivo para controlar, ele será habilitado tanto para o Controle de Alterações como para Inventário.

Para rastrear alterações em arquivos no Windows e Linux, os hashes MD5 dos arquivos são usados. Esses hashes são usados para detectar se uma alteração foi feita desde o último inventário.

### <a name="configure-linux-files-to-track"></a>Configurar arquivos do Linux para controle

Use as etapas a seguir para configurar o acompanhamento de arquivo em computadores Linux:

1. Na sua Conta de Automação, selecione **Controle de Alterações** em **GERENCIAMENTO DE CONFIGURAÇÃO**. Clique em **Editar Configurações** (o símbolo de engrenagem).
2. Na página **Controle de Alterações**, selecione **Arquivos Linux** e, em seguida, clique em **+ Adicionar** para adicionar o novo arquivo para controle.
3. Em **Adicionar o Arquivo do Linux para o Controle de Alterações**, insira as informações para o arquivo ou para o diretório rastrear e clique em **Salvar**.

|Propriedade  |DESCRIÇÃO  |
|---------|---------|
|Enabled     | Determina se a configuração é aplicada.        |
|Nome do Item     | Nome amigável do arquivo a ser rastreado.        |
|Agrupar     | Um nome de grupo para o agrupamento lógico de arquivos.        |
|Inserir o Caminho     | O caminho para verificar o arquivo. Por exemplo: “/etc/* .conf”       |
|Tipo de caminho     | Tipo de item a ser rastreado; possíveis valores são: Arquivo e Diretório.        |
|Recursão     | Determina se a recursão é usada ao procurar o item a ser rastreado.        |
|Usar o Sudo     | Essa configuração determina se o Sudo será usado durante a verificação do item.         |
|Links     | Essa configuração determina como os links simbólicos lidam ao passar diretórios.<br> **Ignorar** – ignora os links simbólicos e não inclui os arquivos/diretórios referenciados.<br>**Seguir** - Segue os links simbólicos durante a recursão e inclui também os arquivos/diretórios referenciados.<br>**Gerenciar** - Segue os links simbólicos e permite a alteração do conteúdo retornado.     |
|Carregar o conteúdo do arquivo para todas as configurações| Habilita ou desabilita o upload de conteúdo do arquivo em alterações controladas. Opções disponíveis: **Verdadeiro** ou **Falso**.|

> [!NOTE]
> A opção "Gerenciar" links não é recomendada. Não há suporte para a recuperação de conteúdo do arquivo.

### <a name="configure-windows-files-to-track"></a>Configurar os arquivos do Windows para controlar

Use as etapas a seguir para configurar o acompanhamento de arquivos em computadores Windows:

1. Na sua Conta de Automação, selecione **Controle de Alterações** em **GERENCIAMENTO DE CONFIGURAÇÃO**. Clique em **Editar Configurações** (o símbolo de engrenagem).
2. Na página **Controle de Alterações**, selecione **Arquivos do Windows** e, em seguida, clique em **+ Adicionar** para adicionar o novo arquivo para controle.
3. Em **Adicionar o Arquivo do Windows para o Controle de Alterações**, insira as informações para o arquivo a controlar e clique em **Salvar**.

|Propriedade  |DESCRIÇÃO  |
|---------|---------|
|Enabled     | Determina se a configuração é aplicada.        |
|Nome do Item     | Nome amigável do arquivo a ser rastreado.        |
|Agrupar     | Um nome de grupo para o agrupamento lógico de arquivos.        |
|Insira o Caminho     | O caminho para verificar em busca do arquivo. Por exemplo: "c:\temp\\\*.txt"<br>Você também pode usar variáveis de ambiente, tais como "%winDir%\System32\\\*.*"       |
|Recursão     | Determina se a recursão é usada ao procurar o item a ser rastreado.        |
|Carregar o conteúdo do arquivo para todas as configurações| Habilita ou desabilita o upload de conteúdo do arquivo em alterações controladas. Opções disponíveis: **Verdadeiro** ou **Falso**.|

## <a name="wildcard-recursion-and-environment-settings"></a>Configurações de caractere curinga, recursão e ambiente

A recursão permite que você especifique caracteres curinga para simplificar o acompanhamento em diretórios, e variáveis para que você possa acompanhar arquivos em ambientes com vários nomes de unidade ou nomes de unidade dinâmicos. A seguir está uma lista que mostra informações comuns que você deve saber ao configurar a recursão:

* Caracteres curinga são necessários para acompanhar vários arquivos
* Ao usar caracteres curinga, eles só podem ser usados no último segmento de um caminho. (como `c:\folder\*file*` ou `/etc/*.conf`)
* Se uma variável de ambiente tiver um caminho inválido, a validação terá êxito, mas esse caminho falhará quando o inventário for executado.
* Evite caminhos gerais como `c:\*.*` ao definir o caminho, pois isso resultaria em muitas pastas sendo percorridas.

## <a name="configure-file-content-tracking"></a>Configurar o controle de conteúdo do arquivo

Você pode visualizar o conteúdo antes e depois de uma alteração de um arquivo com o controle de alteração de conteúdo de arquivo. Isso está disponível para arquivos do Windows e Linux, para cada alteração no arquivo, o conteúdo do arquivo é armazenado em uma conta de armazenamento e mostra o arquivo antes e após a alteração, embutido ou lado a lado. Para obter mais informações, confira [Exibir o conteúdo de um arquivo controlado](change-tracking-file-contents.md).

![exibir alterações em um arquivo](./media/change-tracking-file-contents/view-file-changes.png)

### <a name="configure-windows-registry-keys-to-track"></a>Configurar chaves do Registro do Windows para rastrear

Use as etapas a seguir para configurar as chaves do registro para acompanhamento em computadores Windows:

1. Na sua Conta de Automação, selecione **Controle de Alterações** em **GERENCIAMENTO DE CONFIGURAÇÃO**. Clique em **Editar Configurações** (o símbolo de engrenagem).
2. Na página **Controle de Alterações**, selecione **Registro do Windows** e, em seguida, clique em **+ Adicionar** para adicionar um novo registro para controle.
3. Em **Adicionar Registro do Windows para Controle de Alterações**, insira as informações para a chave rastrear e clique em **Salvar**.

|Propriedade  |DESCRIÇÃO  |
|---------|---------|
|Enabled     | Determina se a configuração é aplicada.        |
|Nome do Item     | Nome amigável da chave de registro a ser rastreada.        |
|Agrupar     | Um nome de grupo para agrupar chaves de registro logicamente.        |
|Chave de Registro do Windows   | O caminho para verificar a chave de registro. Por exemplo:  "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

## <a name="limitations"></a>Limitações

A solução de Controle de Alterações atualmente não dá suporte ao seguinte:

* Recursão para o rastreamento de registro do Windows
* Sistemas de arquivos de rede

Outras limitações:

* Os valores e a coluna **Tamanho máximo de arquivo** não são utilizados na implementação atual.
* Se você coletar mais de 2500 arquivos no ciclo de coleta de 30 minutos, o desempenho da solução poderá ser prejudicado.
* Quando o tráfego da rede é alto, os registros de alteração podem demorar até seis horas para serem exibidos.
* Se você modificar a configuração enquanto um computador for desligado, o computador poderá lançar as alterações que pertenciam à configuração anterior.

## <a name="known-issues"></a>Problemas conhecidos

Atualmente, a solução Controle de Alterações está enfrentando os seguintes problemas:

* Atualizações de hotfix não são coletadas em computadores Windows Server 2016 Core RS3.
* Daemons do Linux pode mostrar um estado alterado, mesmo que não houve nenhuma alteração. Isso ocorre devido a como o `SvcRunLevels` campo é capturado.

## <a name="change-tracking-data-collection-details"></a>Detalhes de coleta de dados do Controle de Alterações

A tabela a seguir mostra a frequência da coleta de dados para os tipos de alterações. Para cada tipo, o instantâneo de dados do estado atual também é atualizado pelo menos a cada 24 horas:

| **Alterar tipo** | **Frequência** |
| --- | --- |
| Registro do Windows | 50 minutos |
| Arquivo do Windows | 30 minutos |
| Arquivo Linux | 15 minutos |
| Serviços do Windows | 10 segundos a 30 minutos</br> Padrão: 30 minutos |
| Daemons Linux | 5 minutos |
| Software do Windows | 30 minutos |
| Software Linux | 5 minutos |

A tabela a seguir mostra os limites de item controlados por máquina para Controle de Alterações.

| **Recurso** | **Limite**| **Observações** |
|---|---|---|
|Arquivo|500||
|Registro|250||
|Software do Windows|250|Não inclui as atualizações de software|
|Pacotes do Linux|1250||
|Serviços|250||
|Daemon|250||

O uso médio de dados do Log Analytics para uma máquina usando o Controle de Alterações e Inventário é de aproximadamente 40 MB por mês. Esse valor é somente uma aproximação e está sujeito a alterações com base em seu ambiente. É recomendável que você monitore seu ambiente para ver o uso exato que você tem.

### <a name="windows-service-tracking"></a>Rastreamento de serviço do Windows

A frequência da coleta padrão para os serviços do Windows é de 30 minutos. Para configurar a frequência, acesse **Controle de Alterações**. Em **Editar Configurações** na guia **Serviços do Windows**, há um controle deslizante que permite alterar a frequência de coleta para os serviços do Windows de 10 segundos para até 30 minutos. Mova o controle deslizante para a frequência desejada e ele a salvará automaticamente.

![Controle deslizante de serviços do Windows](./media/change-tracking/windowservices.png)

O agente controla somente as alterações e isso otimiza o desempenho do agente. Definir um limite alto poderá deixar passar alterações se o serviço for revertido ao estado original. Definir a frequência para um valor menor permite capturar alterações que poderiam ser perdidas de outra forma.

> [!NOTE]
> Embora o agente possa controlar as alterações em um intervalo de apenas 10 segundos, os dados ainda demoram alguns minutos para serem exibido no portal. As alterações durante o tempo de exibição no portal do ainda são controladas e registradas.
  
### <a name="registry-key-change-tracking"></a>Controle de alterações de chave do Registro

O objetivo de monitorar alterações às chaves do registro é identificar os pontos de extensibilidade em que código de terceiros e o malware podem ser ativados. A lista a seguir mostra a lista de chaves do Registro pré-configuradas. Essas chaves estão configuradas, mas não habilitadas. Para controlar essas chaves do Registro, é necessário habilitar cada uma delas.

> [!div class="mx-tdBreakAll"]
> |  |
> |---------|
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitores entradas comuns de inicialização automática que conectam-se diretamente ao Windows Explorer e geralmente são executadas no processo com o Explorer.exe.    |
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora scripts que são executados na inicialização.     |
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown**    |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora scripts que são executados no desligamento.     |
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora as chaves que são carregadas antes que o usuário entre na conta do Windows. A chave é usada para programas de 32 bits em execução em computadores de 64 bits.    |
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora as alterações nas configurações do aplicativo.     |
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitores entradas comuns de inicialização automática que conectam-se diretamente ao Windows Explorer e geralmente são executadas no processo com o Explorer.exe.|
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitores entradas comuns de inicialização automática que conectam-se diretamente ao Windows Explorer e geralmente são executadas no processo com o Explorer.exe.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitores o registro do manipulador de sobreposição de ícone.|
|**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora o registro do manipulador de sobreposição de ícone para programas de 32 bits executados em computadores de 64 bits.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora os novos plug-ins de objeto auxiliar de navegador para o Internet Explorer. Usado para acessar o modelo DOM (Modelo de Objeto do Documento) da página atual e para controlar a navegação.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora os novos plug-ins de objeto auxiliar de navegador para o Internet Explorer. Usado para acessar o modelo DOM (Modelo de Objeto do Documento) da página atual e para controlar a navegação para programas de 32 bits em execução em computadores de 64 bits.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora novas extensões do Internet Explorer, tais como menus de ferramentas personalizadas e botões da barra de ferramentas personalizada.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora novas extensões do Internet Explorer, como menus de ferramentas personalizadas e botões de barra de ferramentas personalizada para programas de 32 bits executados em computadores de 64 bits.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora os drivers de 32 bits associados com wavemapper, wave1 e wave2, msacm.imaadpcm, .msadpcm, .msgsm610 e vidc. Semelhante à seção [drivers] no arquivo SYSTEM.INI.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora os drivers de 32 bits associados com wavemapper, wave1 e wave2, msacm.imaadpcm, .msadpcm, .msgsm610 e vidc para programas de 32 bits executados em computadores de 64 bits. Semelhante à seção [drivers] no arquivo SYSTEM.INI.|
> |**HKEY\_LOCAL\_MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora a lista de DLLs de sistema conhecidas ou comumente usadas; esse sistema impede que pessoas explorem as permissões de diretório de aplicativo fracas via depósito de versões com cavalo de troia das DLLs do sistema.|
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora a lista de pacotes capaz de receber notificações de eventos do Winlogon, o modelo de suporte de logon interativo para o sistema operacional Windows.|

## <a name="network-requirements"></a>Requisitos de rede

Os seguintes endereços são necessários especificamente para o controle de alterações. A comunicação para esses endereços é feita pela porta 443.

|Público do Azure  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.Azure automation.us|

## <a name="use-change-tracking"></a>Use o Controle de Alterações

Depois que a solução estiver habilitada, você poderá exibir o resumo das alterações de seus computadores monitorados, selecionando **Controle de Alterações** em **GERENCIAMENTO DE CONFIGURACÃO** na sua conta de Automação.

É possível exibir as alterações nos computadores e, em seguida, analisar detalhadamente cada evento. Os menus suspensos estão disponíveis na parte superior do gráfico para limitar o gráfico e as informações detalhadas com base no tipo de alteração e intervalos de tempo. Você também pode clicar e arrastrar no gráfico para selecionar um intervalo de tempo personalizado. **Alterar tipo** será um dos valores a seguir **eventos**, **Daemons**, **arquivos**, **registro**,  **Software**, **os serviços Windows**. Categoria mostra o tipo de alteração e pode ser **adicionado**, **modificado**, ou **removido**.

![imagem do painel Controle de Alterações](./media/change-tracking/change-tracking-dash01.png)

Ao clicar em uma alteração ou evento, as informações detalhadas sobre essa alteração serão exibidas. Como possível visualizar no exemplo, o tipo de inicialização do serviço foi alterado de Manual para Automático.

![imagem dos detalhes do controle de alterações](./media/change-tracking/change-tracking-details.png)

## <a name="search-logs"></a>Pesquisar logs

Além dos detalhes fornecidos no portal, é possível fazer as pesquisas nos logs. Com o **Change Tracking** página aberta, clique em **Log Analytics**, esse procedimento abre a **Logs** página.

### <a name="sample-queries"></a>Consultas de exemplo

A tabela a seguir fornece pesquisas de logs de exemplo para os registros de alterações coletados por essa solução:

|Consulta  |DESCRIÇÃO  |
|---------|---------|
|ConfigurationData<br>&#124; onde   ConfigDataType == "WindowsServices" e SvcStartupType == "Auto"<br>&#124; onde SvcState == "Stopped"<br>&#124; summarize arg_max(TimeGenerated, *) por SoftwareName, Computer         | Mostra os registros de inventário mais recentes para os Serviços do Windows que foram configurados para Automático, mas foram relatados como Parados<br>Os resultados estão limitados ao registro mais recente desse SoftwareName e Computador      |
|ConfigurationChange<br>&#124; onde ConfigChangeType == "Software" e ChangeCategory == "Removed"<br>&#124; ordenar por TimeGenerated desc|Mostra os registros de alterações do software removido|

## <a name="alert-on-changes"></a>Alertar sobre alterações

Uma funcionalidade crucial de Controle de Alterações e Inventário é a habilidade de alertar sobre o estado de configuração e quaisquer alterações ao estado de configuração de seu ambiente híbrido.  

No exemplo a seguir, a captura de tela mostra que o arquivo `C:\windows\system32\drivers\etc\hosts` foi modificado em um computador. Esse arquivo é importante porque o arquivo Hosts é usado pelo Windows para resolver nomes de host para endereços IP e tem precedência até mesmo sobre DNS, o que pode resultar em problemas de conectividade ou redirecionamento do tráfego para sites mal-intencionados ou de outra forma perigosos.

![Um gráfico mostrando os hosts de alteração de arquivo](./media/change-tracking/changes.png)

Para analisar ainda mais essa alteração, vá para a Pesquisa de logs clicando em **Log Analytics**. Quando estiver na Pesquisa de logs, pesquise alterações de conteúdo ao arquivo Hosts com a consulta `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"`. Esta consulta procura as alterações incluindo uma alteração do conteúdo de arquivos cujo caminho totalmente qualificado contém a palavra "hosts". Você também pode pedir para um arquivo específico alterando a parte do caminho para seu formulário totalmente qualificado (como `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`).

Depois que a consulta retornar os resultados desejados, clique no botão **Nova regra de alerta** na experiência de Pesquisa de logs para abrir a página de criação de alerta. Você também pode navegar para essa experiência por meio do **Azure Monitor** no portal do Azure. Na experiência de criação de alerta, confira nossa consulta novamente e modifique a lógica de alerta. Nesse caso, você desejará que o alerta seja disparado se for detectada até mesmo uma alteração entre todos os computadores no ambiente.

![Uma imagem que mostra a consulta de alteração para o controle de alterações para o arquivo de hosts](./media/change-tracking/change-query.png)

Após definir a lógica da condição, atribua grupos de ação para executar ações em resposta ao alerta que está sendo disparado. Nesse caso, eu configurei emails para serem enviados e um tíquete ITSM para ser criado.  Muitas outras ações úteis também podem ser executadas, como disparar uma Função do Azure, um Runbook de automação, um webhook ou um aplicativo lógico.

![Uma imagem configurando um grupo de ações para alertar sobre a alteração](./media/change-tracking/action-groups.png)

Após configurar os parâmetros e a lógica, podemos aplicar o alerta ao ambiente.

### <a name="alert-suggestions"></a>Sugestões de alerta

Embora os alertas sobre alterações ao arquivo de Hosts sejam uma boa aplicação de alertas para Controle de Alterações ou dados de Inventário, há muitos outros cenários para alertas, incluindo casos definidos juntamente com suas consultas de exemplo na seção a seguir.

|Consulta  |DESCRIÇÃO  |
|---------|---------|
|ConfigurationChange <br>&#124; em que ConfigChangeType == "Files" e FileSystemPath contêm " c:\\windows\\system32\\drivers\\"|Útil para controlar alterações a arquivos críticos do sistema|
|ConfigurationChange <br>&#124; em que FieldsChanged contém "FileContentChecksum" e FileSystemPath == "c:\\windows\\system32\\drivers\\etc\\hosts"|Útil para controlar modificações a arquivos de configuração chave|
|ConfigurationChange <br>&#124; em que ConfigChangeType == "WindowsServices" e SvcName contêm "w3svc" e SvcState == "Stopped"|Útil para controlar alterações a serviços críticos do sistema|
|ConfigurationChange <br>&#124; em que ConfigChangeType == "Daemons" e SvcName contêm "ssh" e SvcState != "Running"|Útil para controlar alterações a serviços críticos do sistema|
|ConfigurationChange <br>&#124; em que ConfigChangeType == "Software" e ChangeCategory == "Added"|Útil para ambientes que precisam bloquear configurações de software|
|ConfigurationData <br>&#124; em que SoftwareName contém "Monitoring Agent" e CurrentVersion != "8.0.11081.0"|Útil para ver quais computadores têm uma versão de software desatualizada ou não em conformidade instalada. Relata o último estado de configuração relatada, não as alterações.|
|ConfigurationChange <br>&#124; em que RegistryKey == "HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\QualityCompat"| Útil para controlar alterações a chaves antivírus cruciais|
|ConfigurationChange <br>&#124; em que RegistryKey contém "HKEY_LOCAL_MACHINE\\SYSTEM\\CurrentControlSet\\Services\\SharedAccess\\Parameters\\FirewallPolicy"| Útil para controlar alterações em configurações de firewall|

## <a name="next-steps"></a>Próximas etapas

Visite o tutorial sobre Controle de Alterações para saber mais sobre o uso da solução:

> [!div class="nextstepaction"]
> [Solucionar problemas de alterações em seu ambiente](automation-tutorial-troubleshoot-changes.md)

* Use [pesquisas de Log nos logs do Azure Monitor](../log-analytics/log-analytics-log-searches.md) para exibir dados de rastreamento de alterações detalhado.
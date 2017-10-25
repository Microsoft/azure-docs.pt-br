---
title: "Controlar alterações com o Azure Log Analytics | Microsoft Docs"
description: "A solução Controle de Alterações no Log Analytics ajuda a identificar alterações no software e nos Serviços Windows que ocorrem no ambiente."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: f8040d5d-3c89-4f0c-8520-751c00251cb7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 57af000e47188786a77cdb84ebb6ffb5c50eafaa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="track-software-changes-in-your-environment-with-the-change-tracking-solution"></a>Controlar alterações de software no ambiente com a solução Controle de Alterações

![Símbolo do Controle de Alterações](./media/log-analytics-change-tracking/change-tracking-symbol.png)

Este artigo ajuda você a usar a solução de Controle de Alterações em Log Analytics para identificar facilmente as alterações em seu ambiente. A solução rastreia as alterações no software Windows e Linux, nos arquivos Windows e chaves do Registro, nos serviços Windows e nos daemons do Linux. Identificar as alterações de configuração pode ajudá-lo a detectar problemas operacionais.

Instale a solução para atualizar o tipo de agente já instalado. Alterações de software instalado, serviços do Windows e Linux daemons nos servidores monitorados são lidos. Em seguida, os dados são enviados para o serviço de análise de Log na nuvem para processamento. A lógica é aplicada aos dados recebidos e o serviço de nuvem registra os dados. Usando as informações no painel Controle de Alterações, você pode ver facilmente as alterações feitas à sua infraestrutura de servidor.

## <a name="installing-and-configuring-the-solution"></a>Instalando e configurando a solução
Use as informações a seguir para instalar e configurar a solução.

* Você deve ter um agente do [Windows](log-analytics-windows-agents.md), do [Operations Manager](log-analytics-om-agents.md) ou do [Linux](log-analytics-linux-agents.md) em cada computador no qual deseja controlar as alterações.
* Adicionar a solução de controle de alterações ao seu espaço de trabalho do OMS a [do Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ChangeTrackingOMS?tab=Overview). Ou, você pode adicionar a solução usando as informações em [soluções de análise de Log adicionar da Galeria de soluções](log-analytics-add-solutions.md). Nenhuma configuração adicional é necessária.

### <a name="configure-linux-files-to-track"></a>Configurar arquivos do Linux para controle
Use as etapas a seguir para configurar arquivos para controle em computadores Linux.

1. No portal do OMS, clique em **Configurações** (símbolo de engrenagem).
2. Na página **Configurações**, clique em **Dados** e, em seguida, clique em **Controle de Arquivos do Linux**.
3. Em Controle de Alterações de Arquivos do Linux, digite o caminho completo, incluindo o nome do arquivo que você deseja controlar e, em seguida, clique no símbolo **Adicionar**. Por exemplo: “/etc/*.conf”
4. Clique em **Salvar**.  

> [!NOTE]
> O controle de arquivos do Linux tem funcionalidades adicionais, incluindo controle de diretórios, recursão por diretórios e controle de curingas.

### <a name="configure-windows-files-to-track"></a>Configurar os arquivos do Windows para controlar
Use as etapas a seguir para configurar os arquivos para controle em computadores Windows.

1. No portal do OMS, clique em **Configurações** (símbolo de engrenagem).
2. Na página **Configurações**, clique em **Dados** e, em seguida, clique em **Rastreamento de Arquivos do Windows**.
3. Em Controle de Alterações do Arquivo do Windows, digite o caminho completo, incluindo o nome do arquivo do arquivo que você deseja rastrear e, em seguida, clique no símbolo **Adicionar**. Por exemplo: C:\Program Files (x86)\Internet Explorer\iexplore.exe ou C:\Windows\System32\drivers\etc\hosts.
4. Clique em **Salvar**.  
   ![Controle de Alterações de Arquivo do Windows](./media/log-analytics-change-tracking/windows-file-change-tracking.png)

### <a name="configure-windows-registry-keys-to-track"></a>Configurar chaves do Registro do Windows para rastrear
Use as etapas a seguir para configurar as chaves do Registro para rastrear em computadores Windows.

1. No portal do OMS, clique em **Configurações** (símbolo de engrenagem).
2. Na página **Configurações**, clique em **Dados** e, em seguida, clique em **Rastreamento de Registro do Windows**.
3. Em Controle de Alterações do Registro do Windows, digite a chave completa que você deseja rastrear e, em seguida, clique no símbolo **Adicionar**.
4. Clique em **Salvar**.  
   ![Controle de alterações de Registro de Windows](./media/log-analytics-change-tracking/windows-registry-change-tracking.png)

### <a name="explanation-of-linux-file-collection-properties"></a>Explicação das propriedades da coleção de arquivos do Linux
1. **Tipo**
   * **Arquivo** (relate os metadados do arquivo – tamanho, data de modificação, hash, etc.)
   * **Diretório** (relate os metadados do diretório – tamanho, data de modificação, etc.)
2. **Links** (manipulação de referências de symlink do Linux para outros arquivos ou diretórios)
   * **Ignorar** (ignore os symlinks durante as recursões para não incluir os arquivos/diretórios referenciados)
   * **Seguir** (siga os symlinks durante a recursão para incluir também os arquivos/diretórios referenciados)
   * **Gerenciar** (siga os symlinks e altere o tratamento do conteúdo retornado)

   > [!NOTE]   
   > A opção "Gerenciar" links não é recomendada. Não há suporte para a recuperação de conteúdo do arquivo.

3. **Realizar recursão** (realize recursão nos níveis de pasta e controle todos os arquivos que atendem à instrução de caminho)
4. **Sudo** (habilite o acesso a arquivos ou diretórios que exigem privilégios do sudo)

### <a name="limitations"></a>Limitações
A solução de Controle de Alterações atualmente não dá suporte ao seguinte:

* Pastas (diretórios) para o Controle de Arquivos do Windows
* Recursão para o Controle de Arquivos do Windows
* Curingas para o Controle de Arquivos do Windows
* Variáveis de caminho
* Sistemas de arquivos de rede
* Conteúdo do arquivo

Outras limitações:

* Os valores e a coluna **Tamanho máximo de arquivo** não são utilizados na implementação atual.
* Se você coletar mais de 2500 arquivos no ciclo de coleta de 30 minutos, o desempenho da solução poderá ser prejudicado.
* Quando o tráfego de rede for alto, registros de alteração poderão levar até um máximo de seis horas para exibir.
* Se você modificar a configuração enquanto um computador for desligado, o computador poderá lançar as alterações de arquivo que pertenciam à configuração anterior.

## <a name="change-tracking-data-collection-details"></a>Detalhes de coleta de dados do Controle de Alterações
O Controle de Alterações coletará inventário de software e metadados do serviço Windows usando os agentes que você tiver habilitado.

A tabela a seguir mostra os métodos de coleta de dados e outros detalhes sobre como os dados são coletados para o Controle de Alterações.

| plataforma | Agente direto | Agente do Operations Manager | Agente do Linux | Armazenamento do Azure | Operations Manager necessário? | Dados de agente do Operations Manager enviados por meio do grupo de gerenciamento | frequência de coleta |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Windows e Linux | &#8226; | &#8226; | &#8226; |  |  | &#8226; | 5 minutos para 50 minutos, dependendo do tipo de alteração. Confira a tabela a seguir para saber mais. |


A tabela a seguir mostra a frequência da coleta de dados para os tipos de alterações.

| **change type** | **frequency** | **O** **agente** **envia as diferenças quando encontradas?**  |
| --- | --- | --- |
| Registro do Windows | 50 minutos | Não |
| Arquivo do Windows | 30 minutos | Sim. Se não houver nenhuma alteração em até 24 horas, um instantâneo é enviado. |
| Arquivo Linux | 15 minutos | Sim. Se não houver nenhuma alteração em até 24 horas, um instantâneo é enviado. |
| Serviços do Windows | 30 minutos | Sim, quando as alterações são encontradas a cada 30 minutos. A cada 24 horas um instantâneo é enviado, independentemente da mudança. Portanto, o instantâneo é enviado até mesmo quando não há nenhuma alteração. |
| Daemons Linux | 5 minutos | Sim. Se não houver nenhuma alteração em até 24 horas, um instantâneo é enviado. |
| Software do Windows | 30 minutos | Sim, quando as alterações são encontradas a cada 30 minutos. A cada 24 horas um instantâneo é enviado, independentemente da mudança. Portanto, o instantâneo é enviado até mesmo quando não há nenhuma alteração. |
| Software Linux | 5 minutos | Sim. Se não houver nenhuma alteração em até 24 horas, um instantâneo é enviado. |

### <a name="registry-key-change-tracking"></a>Controle de alterações de chave do Registro

O Log Analytics realiza o monitoramento e rastreamento de chaves do Registro do Windows com a solução de Controle de Alterações. O objetivo de monitorar alterações às chaves do registro é identificar os pontos de extensibilidade em que código de terceiros e o malware podem ser ativados. A lista a seguir mostra as chaves do Registro padrão que são rastreadas pela solução e o motivo pelo qual cada uma delas é rastreada.

- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup
    - Monitora scripts que são executados na inicialização.
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown
    - Monitora scripts que são executados no desligamento.
- HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run
    - Monitora as chaves que são carregadas antes da usuário se autentica em suas contas do Windows. A chave é usada para programas de 32 bits em execução em computadores de 64 bits.
- HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components
    - Monitora as alterações às configurações do aplicativo.
- HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers
    - Monitores entradas comuns de inicialização automática que se conectam diretamente ao Windows Explorer e geralmente são executadas no processo com o Explorer.exe.
- HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers
    - Monitores entradas comuns de inicialização automática que se conectam diretamente ao Windows Explorer e geralmente são executadas no processo com o Explorer.exe.
- HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\Background\ShellEx\ContextMenuHandlers
    - Monitores entradas comuns de inicialização automática que se conectam diretamente ao Windows Explorer e geralmente são executadas no processo com o Explorer.exe.
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers
    - Monitora o registro do manipulador de sobreposição de ícone.
- HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers
    - Monitora o registro do manipulador de sobreposição de ícone para programas de 32 bits executados em computadores de 64 bits.
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects
    - Monitora os novos plug-ins de objeto auxiliar de navegador para o Internet Explorer. Usado para acessar o modelo DOM (Modelo de Objeto do Documento) da página atual e para controlar a navegação.
- HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects
    - Monitora os novos plug-ins de objeto auxiliar de navegador para o Internet Explorer. Usado para acessar o modelo DOM (Modelo de Objeto do Documento) da página atual e para controlar a navegação para programas de 32 bits em execução em computadores de 64 bits.
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Internet Explorer\Extensions
    - Monitora novas extensões do Internet Explorer, tais como menus de ferramentas personalizadas e botões da barra de ferramentas personalizada.
- HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions
    - Monitora novas extensões do Internet Explorer, como menus de ferramentas personalizadas e botões de barra de ferramentas personalizada para programas de 32 bits executados em computadores de 64 bits.
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32
    - Monitora os drivers de 32 bits associados com wavemapper, wave1 e wave2, msacm.imaadpcm, .msadpcm, .msgsm610 e vidc. Semelhante à seção [drivers] no arquivo SYSTEM.INI.
- HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32
    - Monitora os drivers de 32 bits associados com wavemapper, wave1 e wave2, msacm.imaadpcm, .msadpcm, .msgsm610 e vidc para programas de 32 bits executados em computadores de 64 bits. Semelhante à seção [drivers] no arquivo SYSTEM.INI.
- HKEY\_LOCAL\_MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls
    - Monitora a lista de DLLs de sistema conhecidas ou comumente usadas; esse sistema impede que pessoas explorem as permissões de diretório de aplicativo fracas via depósito de versões com cavalo de troia das DLLs do sistema.
- HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify
    - Monitora a lista de pacotes capaz de receber notificações de eventos do Winlogon, o modelo de suporte de logon interativo para o sistema operacional Windows.


## <a name="use-change-tracking"></a>Use o Controle de Alterações
Após instalar a solução, você pode exibir o resumo das alterações nos servidores monitorados usando o bloco **Controle de Alterações** na página **Visão geral** do OMS.

![imagem de bloco Controle de Alterações](./media/log-analytics-change-tracking/change-tracking-tile.png)

Você pode exibir as alterações à sua infraestrutura e então analisar em detalhes para as seguintes categorias:

* Alterações por tipo de configuração para serviços de software e do Windows
* Alterações de software para aplicativos e atualizações para servidores individuais
* Número total de alterações de software para cada aplicativo
* Pacotes do Linux
* Alterações no serviço Windows para servidores individuais
* Alterações de daemon do Linux

![imagem do painel Controle de Alterações](./media/log-analytics-change-tracking/change-tracking-dash01.png)

![imagem do painel Controle de Alterações](./media/log-analytics-change-tracking/change-tracking-dash02.png)

### <a name="to-view-changes-for-any-change-type"></a>Para exibir as alterações para qualquer tipo de alteração
1. Na página **Visão Geral**, clique no bloco **Controle de Alterações**.
2. No painel **Controle de Alterações**, examine as informações resumidas em uma das folhas de tipo de alteração e, em seguida, clique em uma para exibir informações detalhadas sobre ela na página de **pesquisa de log**.
3. Em qualquer uma das páginas de pesquisa de log, você pode exibir os resultados por tempo, resultados detalhados e o histórico de pesquisa de log. Você também pode filtrar por facetas para restringir os resultados.

## <a name="next-steps"></a>Próximas etapas
* Use [Pesquisas de log no Log Analytics](log-analytics-log-searches.md) para exibir dados detalhados do controle de alterações.

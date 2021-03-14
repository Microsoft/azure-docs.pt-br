---
title: Gerenciando o agente de Log Analytics do Azure
description: Este artigo descreve as diferentes tarefas de gerenciamento que normalmente serão executadas durante o ciclo de vida do Log Analytics o agente Windows ou Linux implantado em um computador.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/14/2019
ms.openlocfilehash: ca36c35d859e651c0d949f4b7fbb28137d01af90
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734954"
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>Gerenciar e manter o agente de Log Analytics para o Windows e Linux

Após a implantação inicial do agente Log Analytics Windows ou Linux no Azure Monitor, talvez seja necessário reconfigurar o agente, atualizá-lo ou removê-lo do computador se ele tiver atingido o estágio de aposentadoria em seu ciclo de vida. Você pode gerenciar facilmente essas tarefas de manutenção de rotina, manualmente ou por meio de automação, o que reduz o erro operacional e as despesas.

## <a name="upgrading-agent"></a>Atualizando o agente

O agente de Log Analytics para Windows e Linux pode ser atualizado para a versão mais recente manualmente ou automaticamente, dependendo do cenário de implantação e do ambiente em que a VM está sendo executada. Os métodos a seguir podem ser usados para atualizar o agente.

| Ambiente | Método de instalação | Método de atualização |
|--------|----------|-------------|
| VM do Azure | Extensão de VM do agente de Log Analytics para Windows/Linux | O Agent é atualizado automaticamente por padrão, a menos que você tenha configurado seu modelo de Azure Resource Manager para recusar definindo a propriedade *autoUpgradeMinorVersion* como **false**. |
| Imagens personalizadas de VM do Azure | Instalação manual do agente do Log Analytics para Windows/Linux | A atualização de VMs para a versão mais recente do agente precisa ser executada na linha de comando que executa o pacote do Windows Installer ou o grupo de script de shell instalável e de extração automática do Linux.|
| VMs não Azure | Instalação manual do agente do Log Analytics para Windows/Linux | A atualização de VMs para a versão mais recente do agente precisa ser executada na linha de comando que executa o pacote do Windows Installer ou o grupo de script de shell instalável e de extração automática do Linux. |

### <a name="upgrade-windows-agent"></a>Atualizar o agente do Windows 

Para atualizar o agente em uma VM do Windows para a versão mais recente não instalada usando a extensão de VM Log Analytics, você pode executar no prompt de comando, script ou outra solução de automação ou usando o \<platform\> Assistente de instalação MMASetup-. msi.  

Você pode baixar a versão mais recente do agente do Windows do seu espaço de trabalho Log Analytics, executando as etapas a seguir.

1. Entre no [portal do Azure](https://portal.azure.com).

2. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Escolha **workspaces do Log Analytics**.

3. Na lista de espaços de trabalho do Log Analytics, selecione o espaço de trabalho.

4. No espaço de trabalho Log Analytics, selecione **Configurações avançadas**, selecione **fontes conectadas** e, por fim, **Windows Servers**.

5. Na página **servidores Windows** , selecione a versão de **Download** apropriada do agente do Windows para download, dependendo da arquitetura do processador do sistema operacional Windows.

>[!NOTE]
>Durante a atualização do agente de Log Analytics para Windows, ele não oferece suporte à configuração ou reconfiguração de um espaço de trabalho para relatar. Para configurar o agente, você precisa seguir um dos métodos com suporte listados em [adicionando ou removendo um espaço de trabalho](#adding-or-removing-a-workspace).
>

#### <a name="to-upgrade-using-the-setup-wizard"></a>Para atualizar com o assistente de instalação

1. Faça logon no computador com uma conta que tenha direitos administrativos.

2. Execute **MMASetup- \<platform\> . exe** para iniciar o assistente de instalação.

3. Na primeira página do assistente de instalação, clique em **Avançar**.

4. Na caixa de diálogo **Microsoft Monitoring Agent configuração** , clique em **concordo** para aceitar o contrato de licença.

5. Na caixa de diálogo **Configuração do Agente de Monitoramento da Microsoft** , clique em **Atualizar**. A página de status exibe o progresso da atualização.

6. Quando a **configuração de Microsoft Monitoring Agent foi concluída com êxito.** aparecerá, clique em **concluir**.

#### <a name="to-upgrade-from-the-command-line"></a>Para atualizar na linha de comando

1. Faça logon no computador com uma conta que tenha direitos administrativos.

2. Para extrair os arquivos de instalação do agente de um prompt de comando com privilégios elevados, execute `MMASetup-<platform>.exe /c` e será solicitado que você especifique o caminho para extrair os arquivos. Como alternativa, você poderá especificar o caminho ao passar os argumentos `MMASetup-<platform>.exe /c /t:<Full Path>`.

3. Execute o seguinte comando, em que D:\ é o local para o arquivo de log de atualização.

    ```dos
    setup.exe /qn /l*v D:\logs\AgentUpgrade.log AcceptEndUserLicenseAgreement=1
    ```

### <a name="upgrade-linux-agent"></a>Atualizar agente do Linux 

Há suporte para a atualização de versões anteriores (>1.0.0-47). Executar a instalação com o comando `--upgrade` atualizará todos os componentes do agente para a versão mais recente.

Execute o comando a seguir para atualizar o agente.

`sudo sh ./omsagent-*.universal.x64.sh --upgrade`

## <a name="adding-or-removing-a-workspace"></a>Adicionando ou removendo workspace

### <a name="windows-agent"></a>Agente do Windows
As etapas nesta seção são necessárias quando você deseja não apenas reconfigurar o agente do Windows para reportar para um espaço de trabalho diferente ou remover um espaço de trabalho de sua configuração, mas também quando você deseja configurar o agente para reportar para mais de um espaço de trabalho (conhecido como hospedagem múltipla). A configuração do agente do Windows para relatar para vários espaços de trabalho só pode ser executada após a configuração inicial do agente e o uso dos métodos descritos abaixo.    

#### <a name="update-settings-from-control-panel"></a>Atualizar as configurações do painel de controle

1. Faça logon no computador com uma conta que tenha direitos administrativos.

2. Abra o **Painel de Controle**.

3. Abra o **Microsoft Monitoring Agent** e clique na guia **Azure Log Analytics**.

4. Se remover um workspace, selecione-o e, em seguida, clique em **remover**. Repita essa etapa para qualquer outro workspace que você deseja que o agente interrompa a emissão de relatórios.

5. Se estiver adicionando um espaço de trabalho, clique em **Adicionar** e na caixa de diálogo **Adicionar um espaço de trabalho do Log Analytics**, cole a ID do espaço de trabalho e chave do espaço de trabalho (chave primária). Caso o computador deva se reportar a um espaço de trabalho do Log Analytics na nuvem do Azure Governamental, selecione Azure US Government na lista suspensa do Azure Cloud.

6. Clique em **OK** para salvar suas alterações.

#### <a name="remove-a-workspace-using-powershell"></a>Remova um workspace usando o PowerShell

```powershell
$workspaceId = "<Your workspace Id>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.RemoveCloudWorkspace($workspaceId)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-commercial-using-powershell"></a>Adicione um workspace no Azure commercial usando o PowerShell

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>Adicione um workspace no Azure for US Government usando o PowerShell

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>Se você já usou a linha de comando ou o script anteriormente para instalar ou configurar o agente, `EnableAzureOperationalInsights` foi substituído por `AddCloudWorkspace` e `RemoveCloudWorkspace`.
>

### <a name="linux-agent"></a>Agente do Linux
As etapas a seguir demonstram como reconfigurar o agente do Linux se você decidir registrá-lo em um espaço de trabalho diferente ou remover um espaço de trabalho de sua configuração.

1. Para verificar se ele está registrado em um workspace, execute o comando a seguir:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    Um status semelhante ao exemplo a seguir deve ser retornado:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

    É importante que o status também mostre que o agente está em execução, caso contrário, as etapas a seguir para reconfigurar o agente não serão concluídas com êxito.

2. Se já estiver registrado em um workspace, remova o workspace registrado executando o comando a seguir. Caso contrário, se não estiver registrado, prossiga para a próxima etapa.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -X`

3. Para registrar em um workspace diferente, execute o comando a seguir:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <shared key> [-d <top level domain>]`
    
4. Para verificar se as alterações entraram em vigor, execute o seguinte comando:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    Um status semelhante ao exemplo a seguir deve ser retornado:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

O serviço do agente não precisa ser reiniciado para que as alterações entrem em vigor.

## <a name="update-proxy-settings"></a>Atualize as configurações de proxy
Para configurar o agente para se comunicar com o serviço por meio de um servidor proxy ou [Gateway do Log Analytics](./gateway.md) após a implantação, use um dos métodos a seguir para concluir esta tarefa.

### <a name="windows-agent"></a>Agente do Windows

#### <a name="update-settings-using-control-panel"></a>Configurações de atualização usando o painel de controle

1. Faça logon no computador com uma conta que tenha direitos administrativos.

2. Abra o **Painel de Controle**.

3. Selecione **Microsoft Monitoring Agent** e, em seguida, clique na guia **configurações de proxy** .

4. Clique em **Usar um servidor proxy** e forneça a URL e o número de porta do servidor proxy ou gateway. Caso seu servidor proxy ou Gateway do Log Analytics exija autenticação, digite o nome de usuário e a senha para se autenticar e clique em **OK**.

#### <a name="update-settings-using-powershell"></a>Configurações de atualização usando o PowerShell

Copie o seguinte código de exemplo do PowerShell, atualize-o com informações específicas para seu ambiente e salve-o com uma extensão de nome de arquivo PS1. Execute o script em cada computador que se conecta diretamente ao espaço de trabalho do Log Analytics no Azure Monitor.

```powershell
param($ProxyDomainName="https://proxy.contoso.com:30443", $cred=(Get-Credential))

# First we get the Health Service configuration object. We need to determine if we
#have the right update rollup with the API we need. If not, no need to run the rest of the script.
$healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

$proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

if (!$proxyMethod)
{
    Write-Output 'Health Service proxy API not present, will not update settings.'
    return
}

Write-Output "Clearing proxy settings."
$healthServiceSettings.SetProxyInfo('', '', '')

$ProxyUserName = $cred.username

Write-Output "Setting proxy to $ProxyDomainName with proxy username $ProxyUserName."
$healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)
```

### <a name="linux-agent"></a>Agente do Linux
Execute as etapas a seguir se os computadores Linux precisarem se comunicar por meio de um servidor proxy ou gateway do Log Analytics. O valor de configuração de proxy tem a seguinte sintaxe `[protocol://][user:password@]proxyhost[:port]`. A propriedade *proxyhost* aceita um nome de domínio totalmente qualificado ou o endereço IP do servidor proxy.

1. Edite o arquivo `/etc/opt/microsoft/omsagent/proxy.conf` executando os comandos a seguir e altere os valores para as configurações específicas.

    ```
    proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
    sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf
    ```

2. Reinicie o agente executando o seguinte comando:

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ```

## <a name="uninstall-agent"></a>Desinstalar o agente
Use um dos procedimentos a seguir para desinstalar o agente do Windows ou Linux usando a linha de comando ou o assistente de instalação.

### <a name="windows-agent"></a>Agente do Windows

#### <a name="uninstall-from-control-panel"></a>Desinstalar usando o painel de controle
1. Faça logon no computador com uma conta que tenha direitos administrativos.

2. No **Painel de Controle**, clique em **Programas e Recursos**.

3. Em **Programas e Recursos**, clique em **Microsoft Monitoring Agent** e clique em **Desinstalar** e, em seguida clique em **Sim**.

>[!NOTE]
>O assistente de instalação do agente também pode ser executado clicando duas vezes em **MMASetup-\<platform\>.exe**, que está disponível para download no workspace no portal do Azure.

#### <a name="uninstall-from-the-command-line"></a>Desinstalar usando a linha de comando
O arquivo baixado para o agente é um pacote de instalação autossuficiente criado com o IExpress. O programa de instalação para o agente e os arquivos de suporte estão contidos no pacote e precisa ser extraído para desinstalar adequadamente usando a linha de comando mostrada no exemplo a seguir.

1. Faça logon no computador com uma conta que tenha direitos administrativos.

2. Para extrair os arquivos de instalação do agente de um prompt de comando com privilégios elevados, execute `extract MMASetup-<platform>.exe` e será solicitado que você especifique o caminho para extrair os arquivos. Como alternativa, você poderá especificar o caminho ao passar os argumentos `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>`. Para obter mais informações sobre as opções de linha de comando com suporte do IExpress, confira [Opções de linha de comando do IExpress](https://www.betaarchive.com/wiki/index.php?title=Microsoft_KB_Archive/197147) e, em seguida, atualize o exemplo para atender às suas necessidades.

3. No prompt, digite `%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb`.

### <a name="linux-agent"></a>Agente do Linux
Para remover o agente, execute o comando a seguir no computador Linux. O argumento *--purge* remove completamente o agente e sua configuração.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>Configure o agente para relatar a um grupo de gerenciamento do Operations Manager

### <a name="windows-agent"></a>Agente do Windows
Execute as seguintes etapas para configurar o agente do Log Analytics para Windows para reportar para um grupo de gerenciamento do System Center Operations Manager.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. Faça logon no computador com uma conta que tenha direitos administrativos.

2. Abra o **Painel de Controle**.

3. Abra o **Microsoft Monitoring Agent** e clique na guia **Operations Manager**.

4. Se seus servidores do Operations Manager tiverem integração com o Active Directory, clique em **Atualizar automaticamente as atribuições de grupo de gerenciamento do AD DS**.

5. Clique em **Adicionar** para abrir a caixa de diálogo **Adicionar um Grupo de Gerenciamento**.

6. No campo **Nome do grupo de gerenciamento**, digite o nome do grupo de gerenciamento.

7. No campo **Servidor de gerenciamento primário**, digite o nome do computador do servidor de gerenciamento primário.

8. No campo **Porta do servidor de gerenciamento**, digite o número da porta TCP.

9. Na página **Conta de Ação de Agente**, escolha a conta Sistema Local ou uma conta de domínio local.

10. Clique em **OK** para fechar a caixa de diálogo **Adicionar um Grupo de Gerenciamento** e clique em **OK** para fechar a caixa de diálogo **Propriedades do Microsoft Monitoring Agent**.

### <a name="linux-agent"></a>Agente do Linux
Execute as seguintes etapas para configurar o agente do Log Analytics para Linux para reportar para um grupo de gerenciamento do System Center Operations Manager.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. Edite o arquivo `/etc/opt/omi/conf/omiserver.conf`

2. Verifique se a linha que começa com `httpsport=` define a porta 1270. Como: `httpsport=1270`

3. Reinicie o servidor OMI: `sudo /opt/omi/bin/service_control restart`

## <a name="next-steps"></a>Próximas etapas

- Examine [solução de problemas do agente do Linux](agent-linux-troubleshoot.md) se você encontrar problemas ao instalar ou gerenciar o agente do Linux.

- Examine [a solução de problemas do agente do Windows](agent-windows-troubleshoot.md) se você encontrar problemas ao instalar ou gerenciar o agente do Windows.
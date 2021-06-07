---
title: Gerenciando o agente de servidores habilitados para Arc do Azure
description: Este artigo descreve as diferentes tarefas de gerenciamento que você normalmente executará durante o ciclo de vida do agente de computador conectado de servidores habilitados para Arc do Azure.
ms.date: 02/10/2021
ms.topic: conceptual
ms.openlocfilehash: 36ae081f939cbf865db7755a2f766a7ccd87d619
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100587634"
---
# <a name="managing-and-maintaining-the-connected-machine-agent"></a>Gerenciando e mantendo o agente do Connected Machine

Após a implantação inicial do agente de máquina conectado de servidores habilitados para Arc do Azure para Windows ou Linux, talvez seja necessário reconfigurar o agente, atualizá-lo ou removê-lo do computador. Você pode gerenciar facilmente essas tarefas de manutenção de rotina, manualmente ou por meio de automação, o que reduz o erro operacional e as despesas.

## <a name="before-uninstalling-agent"></a>Antes de desinstalar o agente

Antes de remover o agente do computador conectado do servidor habilitado para Arc, considere o seguinte para evitar problemas inesperados ou custos adicionados à sua fatura do Azure:

* Se você tiver implantado extensões de VM do Azure em um servidor habilitado e remover o agente do computador conectado ou excluir o recurso que representa o servidor habilitado para Arc no grupo de recursos, essas extensões continuarão sendo executadas e executarão sua operação normal.

* Se você excluir o recurso que representa o servidor habilitado para ARC em seu grupo de recursos, mas não desinstalar as extensões de VM, ao registrar novamente o computador, não será possível gerenciar as extensões de VM instaladas.

Para servidores ou máquinas que você não deseja mais gerenciar com os servidores habilitados para Arc do Azure, é necessário seguir estas etapas para parar o gerenciamento com êxito:

1. Remova as extensões de VM do computador ou servidor. As etapas são fornecidas abaixo.

2. Desconecte o computador do arco do Azure usando um dos seguintes métodos:

    * Executando `azcmagent disconnect` o comando no computador ou servidor.

    * No servidor habilitado para Arc registrado selecionado no portal do Azure, selecione **excluir** na barra superior.

    * Usando o [CLI do Azure](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-cli#delete-resource) ou [Azure PowerShell](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-powershell#delete-resource). Para o `ResourceType` uso do parâmetro `Microsoft.HybridCompute/machines` .

3. [Desinstale o agente](#remove-the-agent) do computador ou servidor seguindo as etapas abaixo.

## <a name="renaming-a-machine"></a>Renomeando um computador

Quando você altera o nome do computador Linux ou Windows conectado aos servidores habilitados para Arc do Azure, o novo nome não é reconhecido automaticamente porque o nome do recurso no Azure é imutável. Assim como acontece com outros recursos do Azure, você precisa excluir o recurso e recriá-lo para usar o novo nome.

Para servidores habilitados para Arc, antes de renomear o computador, é necessário remover as extensões de VM antes de continuar.

> [!NOTE]
> Embora as extensões instaladas continuem a ser executadas e executem sua operação normal após a conclusão desse procedimento, você não poderá gerenciá-las. Se você tentar reimplantar as extensões no computador, poderá ocorrer um comportamento imprevisível.

> [!WARNING]
> Recomendamos que você evite renomear o nome do computador da máquina e executar esse procedimento apenas se for absolutamente necessário.

1. Auditar as extensões de VM instaladas no computador e anotar sua configuração, usando o [CLI do Azure](manage-vm-extensions-cli.md#list-extensions-installed) ou usando [Azure PowerShell](manage-vm-extensions-powershell.md#list-extensions-installed).

2. Remova as extensões de VM instaladas do [portal do Azure](manage-vm-extensions-portal.md#uninstall-extension), usando o [CLI do Azure](manage-vm-extensions-cli.md#remove-an-installed-extension)ou usando [Azure PowerShell](manage-vm-extensions-powershell.md#remove-an-installed-extension).

3. Use a ferramenta **azcmagent** com o parâmetro [Disconnect](manage-agent.md#disconnect) para desconectar o computador do arco do Azure e excluir o recurso de máquina do Azure. Desconectar o computador de servidores habilitados para ARC não remove o agente de computador conectado e você não precisa remover o agente como parte desse processo. Você pode executar isso manualmente enquanto estiver conectado interativamente, ou automatizar usando a mesma entidade de serviço usada para carregar vários agentes ou com um [token de acesso](../../active-directory/develop/access-tokens.md)da plataforma de identidade da Microsoft. Se você não usou uma entidade de serviço para registrar a máquina com os servidores habilitados para Arc do Azure, consulte o [artigo](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) a seguir para criar uma entidade de serviço.

4. Renomeie o nome do computador de computadores.

5. Registre novamente o agente do computador conectado com servidores habilitados para Arc. Execute a `azcmagent` ferramenta com o parâmetro [Connect](manage-agent.md#connect) para concluir esta etapa.

6. Reimplante as extensões de VM que foram originalmente implantadas no computador de servidores habilitados para Arc. Se você tiver implantado o agente do Azure Monitor para VMs (insights) ou o agente do Log Analytics usando uma política do Azure, os agentes serão reimplantados após o próximo [ciclo de avaliação](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers).

## <a name="upgrading-agent"></a>Atualizando o agente

O agente do computador conectado do Azure é atualizado regularmente para resolver correções de bugs, aprimoramentos de estabilidade e novas funcionalidades. O [Azure Advisor](../../advisor/advisor-overview.md) identifica os recursos que não estão usando a versão mais recente do agente de máquina e recomenda que você atualize para a versão mais recente. Ele o notificará quando você selecionar o servidor habilitado para Arc apresentando uma faixa na página **visão geral** ou ao acessar o Advisor por meio da portal do Azure.

O agente do Azure Connected Machine para Windows e Linux pode ser atualizado para a versão mais recente manual ou automaticamente dependendo de suas necessidades.

A tabela a seguir descreve os métodos com suporte para realizar a atualização do agente.

| Sistema operacional | Método de atualização |
|------------------|----------------|
| Windows | Manualmente<br> Windows Update |
| Ubuntu | [Apt](https://help.ubuntu.com/lts/serverguide/apt.html) |
| SUSE Linux Enterprise Server | [zypper](https://en.opensuse.org/SDB:Zypper_usage_11.3) |
| RedHat Enterprise, Amazon, CentOS Linux | [yum](https://wiki.centos.org/PackageManagement/Yum) |

### <a name="windows-agent"></a>Agente do Windows

O pacote de atualização para o agente do Connected Machine para Windows está disponível em:

* Microsoft Update

* [Catálogo do Microsoft Update](https://www.catalog.update.microsoft.com/Home.aspx)

* [Pacote do Windows Installer do agente do Windows](https://aka.ms/AzureConnectedMachineAgent) do Centro de Download da Microsoft.

O agente pode ser atualizado seguindo vários métodos para dar suporte ao processo de gerenciamento de atualização de software. Tirando a obtenção do Microsoft Update, você pode baixar e executar manualmente do prompt de comando, de um script ou de outra solução de automação, ou do assistente de interface do usuário quando executa `AzureConnectedMachine.msi`.

> [!NOTE]
> * Para atualizar o agente, você deve ter permissões de *Administrador*.
> * Para fazer upgrade manualmente, você precisará primeiro baixar e copiar o pacote do instalador para uma pasta no servidor de destino ou de uma pasta de rede compartilhada. 

Se você não estiver familiarizado com as opções de linha de comando para pacotes do Windows Installer, examine [Opções de linha de comando msiexec](/windows/win32/msi/standard-installer-command-line-options) e [Opções de linha de comando msiexec](/windows/win32/msi/command-line-options).

#### <a name="to-upgrade-using-the-setup-wizard"></a>Para atualizar com o assistente de instalação

1. Faça logon no computador com uma conta que tenha direitos administrativos.

2. Execute **AzureConnectedMachineAgent.msi** para iniciar o assistente de instalação.

O assistente de instalação descobre se existe uma versão anterior e executa automaticamente uma atualização do agente. Quando a atualização for concluída, o assistente de instalação será fechado automaticamente.

#### <a name="to-upgrade-from-the-command-line"></a>Para atualizar na linha de comando

1. Faça logon no computador com uma conta que tenha direitos administrativos.

2. Para atualizar o agente silenciosamente e criar um arquivo de log da instalação na pasta `C:\Support\Logs`, execute o comando a seguir.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentupgradesetup.log"
    ```

### <a name="linux-agent"></a>Agente do Linux

A atualização do agente em um computador Linux para a versão mais recente envolve dois comandos. Um para atualizar o índice do pacote local com a lista dos pacotes mais recentes disponíveis nos repositórios e um para atualizar o pacote local.

Você pode baixar o pacote do agente mais recente do [repositório de pacotes](https://packages.microsoft.com/) da Microsoft.

> [!NOTE]
> Para atualizar o agente, você deve ter permissões de acesso *raiz* ou com uma conta que tenha direitos elevados no Sudo.

#### <a name="upgrade-ubuntu"></a>Atualizar o Ubuntu

1. Para atualizar o índice do pacote local com as alterações mais recentes feitas nos repositórios, execute o seguinte comando:

    ```bash
    apt update
    ```

2. Para atualizar o sistema, execute o seguinte comando:

    ```bash
    apt upgrade
    ```

As ações do comando [apt](https://help.ubuntu.com/lts/serverguide/apt.html), como a instalação e a remoção de pacotes, são registradas no arquivo de log `/var/log/dpkg.log`.

#### <a name="upgrade-red-hatcentosamazon-linux"></a>Atualização de Red Hat/CentOS/Amazon Linux

1. Para atualizar o índice do pacote local com as alterações mais recentes feitas nos repositórios, execute o seguinte comando:

    ```bash
    yum check-update
    ```

2. Para atualizar o sistema, execute o seguinte comando:

    ```bash
    yum update
    ```

As ações do comando [yum](https://access.redhat.com/articles/yum-cheat-sheet), como a instalação e a remoção de pacotes, são registradas no arquivo de log `/var/log/yum.log`. 

#### <a name="upgrade-suse-linux-enterprise"></a>Fazer upgrade do SUSE Linux Enterprise

1. Para atualizar o índice do pacote local com as alterações mais recentes feitas nos repositórios, execute o seguinte comando:

    ```bash
    zypper refresh
    ```

2. Para atualizar o sistema, execute o seguinte comando:

    ```bash
    zypper update
    ```

As ações do comando [zypper](https://en.opensuse.org/Portal:Zypper), como a instalação e a remoção de pacotes, são registradas no arquivo de log `/var/log/zypper.log`.

## <a name="about-the-azcmagent-tool"></a>Sobre a ferramenta Azcmagent

A ferramenta Azcmagent (Azcmagent.exe) é usada para configurar o agente de máquina conectado de servidores habilitados para o Azure Arc durante a instalação ou para modificar a configuração inicial do agente após a instalação. O Azcmagent.exe fornece parâmetros de linha de comando para personalizar o agente e exibir seu status:

* **Connect**: para conectar o computador ao Azure Arc

* **Disconnect**: para desconectar o computador do Azure Arc

* **Show**: exiba o status do agente e suas propriedades de configuração (nome do grupo de recursos, ID da assinatura, versão, etc.), o que pode ajudar ao solucionar um problema com ele. Inclua o `-j` parâmetro para gerar os resultados no formato JSON.

* **Logs** – cria um arquivo. zip no diretório atual que contém os logs para ajudá-lo durante a solução de problemas.

* **Versão** -mostra a versão do agente do computador conectado.

* **-h ou --help**: mostra os parâmetros de linha de comando disponíveis

    Por exemplo, para ver a ajuda detalhada para o parâmetro **Connect** , digite `azcmagent connect -h` . 

* **-v ou --verbose**: para habilitar o log detalhado

Você pode executar uma **conexão** e **Desconectar** manualmente enquanto estiver conectado interativamente, ou automatizar usando a mesma entidade de serviço usada para carregar vários agentes ou com um [token de acesso](../../active-directory/develop/access-tokens.md)da plataforma de identidade da Microsoft. Se você não usou uma entidade de serviço para registrar a máquina com os servidores habilitados para Arc do Azure, consulte o [artigo](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) a seguir para criar uma entidade de serviço.

>[!NOTE]
>Você deve ter permissões de acesso à *raiz* em computadores Linux para executar o **azcmagent**.

### <a name="connect"></a>Connect

Esse parâmetro especifica um recurso no Azure Resource Manager que representa o computador e é criado no Azure. O recurso fica na assinatura e no grupo de recursos especificado, e os dados sobre o computador são armazenados na região do Azure especificada pela configuração `--location`. O nome do recurso padrão é o nome do host do computador, se não for especificado.

Um certificado correspondente à identidade atribuída pelo sistema do computador é baixado e armazenado localmente. Depois que essa etapa for concluída, o serviço de metadados do computador conectado do Azure e o agente de configuração do convidado começarão a sincronizar com os servidores habilitados para Arc do Azure.

Para se conectar usando uma entidade de serviço, execute o seguinte comando:

`azcmagent connect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Para se conectar usando um token de acesso, execute o seguinte comando:

`azcmagent connect --access-token <> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Para se conectar com suas credenciais de logon com privilégios elevados (interativas), execute o seguinte comando:

`azcmagent connect --tenant-id <TenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

### <a name="disconnect"></a>Disconnect

Esse parâmetro especifica um recurso no Azure Resource Manager que representa o computador e é excluído no Azure. Ele não remove o agente do computador, você desinstala o agente separadamente. Depois que o computador estiver desconectado, se você quiser registrá-lo novamente com os servidores habilitados para Arc do Azure, use para que `azcmagent connect` um novo recurso seja criado para ele no Azure.

> [!NOTE]
> Se você tiver implantado uma ou mais extensões de VM do Azure em seu servidor habilitado para Arc e excluir seu registro no Azure, as extensões ainda serão instaladas. É importante entender que, dependendo da extensão instalada, ele executa ativamente sua função. Os computadores que se destinam a serem desativados ou que não são mais gerenciados por servidores habilitados para Arc devem primeiro ter as extensões removidas antes de remover seu registro do Azure.

Para se desconectar usando uma entidade de serviço, execute o seguinte comando:

`azcmagent disconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Para se desconectar usando um token de acesso, execute o seguinte comando:

`azcmagent disconnect --access-token <accessToken>`

Para se desconectar com suas credenciais de logon com privilégios elevados (interativas), execute o seguinte comando:

`azcmagent disconnect`

## <a name="remove-the-agent"></a>Remova o agente

Execute um dos métodos a seguir para desinstalar o agente do Connected Machine do Windows ou do Linux do computador. A remoção do agente não cancela o registro da máquina com servidores habilitados para ARC ou remove as extensões de VM do Azure instaladas. Cancele o registro da máquina e remova as extensões de VM instaladas separadamente quando você não precisar mais gerenciar o computador no Azure, e essas etapas deverão ser concluídas antes da desinstalação do agente.

### <a name="windows-agent"></a>Agente do Windows

Os dois métodos a seguir removem o agente, mas não removem a pasta *C:\Program Files\AzureConnectedMachineAgent* do computador.

#### <a name="uninstall-from-control-panel"></a>Desinstalar usando o painel de controle

1. Para desinstalar o agente do Windows do computador, faça o seguinte:

    a. Entre no computador com uma conta que tenha permissões de administrador.  
    b. No **Painel de Controle**, selecione **Programas e Recursos**.  
    c. Em **Programas e Recursos**, selecione **Agente do Azure Connected Machine**, **Desinstalar** e, em seguida, **Sim**.  

    >[!NOTE]
    > Execute também o assistente de instalação do agente clicando duas vezes no pacote do instalador **AzureConnectedMachineAgent.msi**.

#### <a name="uninstall-from-the-command-line"></a>Desinstalar usando a linha de comando

Para desinstalar o agente manualmente do prompt de comando ou usar um método automatizado, por exemplo, um script, você pode usar o exemplo a seguir. Primeiro, você precisa recuperar o código do produto, um GUID que é o identificador principal do pacote de aplicativos, do sistema operacional. A desinstalação é executada com o uso da linha de comando Msiexec.exe – `msiexec /x {Product Code}`.

1. Abra o Editor do Registro.

2. Na chave do Registro `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall`, procure e copie o GUID do código do produto.

3. Em seguida, você pode desinstalar o agente usando o Msiexec como nos seguintes exemplos:

   * Na linha de comando, digite:

       ```dos
       msiexec.exe /x {product code GUID} /qn
       ```

   * Você pode executar as mesmas etapas com o PowerShell:

       ```powershell
       Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
       Get-ItemProperty | `
       Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
       ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
       ```

### <a name="linux-agent"></a>Agente do Linux

> [!NOTE]
> Para desinstalar o agente, você deve ter permissões de acesso *raiz* ou com uma conta que tenha direitos elevados no Sudo.

Para desinstalar o agente do Linux, o comando a ser usado depende do sistema operacional Linux.

- No Ubuntu, execute o seguinte comando:

    ```bash
    sudo apt purge azcmagent
    ```

- Em RHEL, CentOS e Amazon Linux, execute o seguinte comando:

    ```bash
    sudo yum remove azcmagent
    ```

- Em SLES, execute o seguinte comando:

    ```bash
    sudo zypper remove azcmagent
    ```

## <a name="unregister-machine"></a>Cancelar o registro do computador

Se você estiver planejando parar de gerenciar o computador com serviços de suporte no Azure, execute as seguintes etapas para cancelar o registro da máquina com servidores habilitados para Arc. Você pode executar as etapas antes ou depois de remover o agente do Connected Machine do computador.

1. Abra os servidores habilitados para Arc do Azure acessando o [portal do Azure](https://aka.ms/hybridmachineportal).

2. Selecione o computador na lista, selecione as reticências ( **...** ) e, em seguida, selecione **Excluir**.

## <a name="update-or-remove-proxy-settings"></a>Atualizar ou remover configurações de proxy

Para configurar o agente a fim de se comunicar com o serviço por meio de um servidor proxy ou remover essa configuração após a implantação, use um dos métodos a seguir para concluir a tarefa.

> [!NOTE]
> Os servidores habilitados para ARC não dão suporte ao uso de um [Gateway de log Analytics](../../azure-monitor/agents/gateway.md) como proxy para o agente de computador conectado.
>

### <a name="windows"></a>Windows

Para definir a variável de ambiente do servidor proxy, execute o seguinte comando:

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy","http://{proxy-url}:{proxy-port}","Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

Para configurar o agente para parar de se comunicar por meio de um servidor proxy, execute o seguinte comando para remover a variável de ambiente de servidor proxy e reiniciar o serviço do agente:

```powershell
[Environment]::SetEnvironmentVariable("https_proxy",$null,"Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable removed.
Restart-Service -Name himds
```

### <a name="linux"></a>Linux

Para definir o servidor proxy, execute o seguinte comando no diretório em que você baixou o pacote de instalação do agente:

```bash
# Reconfigure the connected machine agent and set the proxy server.
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

Para configurar o agente a fim de parar de se comunicar por meio de um servidor proxy, execute o seguinte comando para remover a configuração de proxy:

```bash
sudo azcmagent_proxy remove
```

## <a name="next-steps"></a>Próximas etapas

* Informações de solução de problemas podem ser encontradas no [guia solucionar problemas do agente do computador conectado](troubleshoot-agent-onboard.md).

* Saiba como gerenciar seu computador usando o [Azure Policy](../../governance/policy/overview.md) para itens como [configurar convidados](../../governance/policy/concepts/guest-configuration.md) de VM, verificar se o computador está relatando ao workspace do Log Analytics esperado, habilitar o monitoramento com o [Azure Monitor em VMs](../../azure-monitor/vm/vminsights-enable-policy.md) e muito mais.

* Saiba mais sobre o [Agente do Log Analytics](../../azure-monitor/agents/log-analytics-agent.md). O agente Log Analytics para Windows e Linux é necessário quando você deseja coletar dados de monitoramento do sistema operacional e da carga de trabalho, gerenciá-los usando runbooks de automação ou recursos como Gerenciamento de Atualizações ou usar outros serviços do Azure, como a [central de segurança do Azure](../../security-center/security-center-introduction.md).
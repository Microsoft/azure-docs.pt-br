---
title: Conectar computadores híbridos ao Azure por meio do portal do Azure
description: Neste artigo, você aprende a instalar o agente e a conectar computadores ao Azure usando os servidores habilitados para Arc do Azure da portal do Azure.
ms.date: 11/05/2020
ms.topic: conceptual
ms.openlocfilehash: d7a89db7b8a42476a312a8f9a96c5ad230b140a2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102183141"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Conectar computadores híbridos ao Azure por meio do portal do Azure

Você pode habilitar os servidores habilitados para Arc do Azure para um ou um pequeno número de computadores Windows ou Linux em seu ambiente executando um conjunto de etapas manualmente. Ou você pode usar um método automatizado executando um script de modelo que fornecemos. Esse script automatiza o download e a instalação de ambos os agentes.

Esse método exige que você tenha permissões de administrador no computador para instalar e configurar o agente. No Linux, usando a conta raiz, e no Windows, você é membro do grupo local de administradores.

Antes de começar, examine os [pré-requisitos](agent-overview.md#prerequisites) e verifique se a sua assinatura e os recursos atendem aos requisitos. Para obter informações sobre regiões com suporte e outras considerações relacionadas, consulte [regiões do Azure com suporte](overview.md#supported-regions).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Gerar o script de instalação no portal do Azure

O script usado para automatizar o download e a instalação e para estabelecer a conexão com o Azure Arc está disponível no portal do Azure. Para concluir o processo, execute as seguintes etapas:

1. No navegador, acesse o [portal do Azure](https://portal.azure.com).

1. Na página **Servidores – Azure Arc**, selecione **Adicionar** no canto superior esquerdo.

1. Na página **Selecionar um método**, escolha o bloco **Adicionar servidores usando o script interativo** e selecione **Gerar script**.

1. Na página **Gerar script**, selecione a assinatura e o grupo de recursos nos quais você deseja que o computador seja gerenciado no Azure. Selecione uma localização do Azure em que os metadados do computador serão armazenados. Essa localização pode ser a mesma ou uma diferente, assim como ocorre com a localização do grupo de recursos.

1. Na página **Pré-requisitos**, examine as informações e selecione **Avançar: Detalhes do recurso**.

1. Na página **Detalhes do recurso**, forneça o seguinte:

    1. Na lista suspensa **Grupo de recursos**, selecione o grupo de recursos no qual o computador será gerenciado.
    1. Na lista suspensa **Região**, selecione a região do Azure que armazenará os metadados dos servidores.
    1. Na lista suspensa **sistema operacional** , selecione o sistema operacional no qual o script está configurado para execução.
    1. Se o computador estiver se comunicando com a Internet por meio de um servidor proxy, especifique o endereço IP do servidor proxy ou o nome e o número da porta que o computador usará para se comunicar com o servidor proxy. Digite o valor no formato `http://<proxyURL>:<proxyport>`.
    1. Selecione **Avançar: Marcas**.

1. Na página **Marcas**, examine as **Marcas de localização física** padrão sugeridas e insira um valor ou especifique uma ou mais **Marcas personalizadas** a fim de dar suporte aos seus padrões.

1. Selecione **Avançar: Baixar e executar o script**.

1. Na página **Baixar e executar o script**, examine as informações de resumo e selecione **Baixar**. Se você ainda precisar fazer alterações, selecione **Anterior**.

## <a name="install-and-validate-the-agent-on-windows"></a>Instalar e validar o agente no Windows

### <a name="install-manually"></a>Instalar manualmente

Instale o agente do Connected Machine manualmente executando o pacote *AzureConnectedMachineAgent.msi* do Windows Installer. Você pode baixar a versão mais recente do [Pacote do Windows Installer do agente do Windows](https://aka.ms/AzureConnectedMachineAgent) no Centro de Download da Microsoft.

>[!NOTE]
>* Para instalar ou desinstalar o agente, você precisa ter permissões de *Administrador*.
>* Primeiro, é necessário baixar e copiar o pacote do instalador para uma pasta no servidor de destino ou de uma pasta de rede compartilhada. Se você executar o pacote do instalador sem nenhuma opção, ele iniciará um assistente de instalação que você poderá seguir para instalar o agente de maneira interativa.

Se o computador precisar se comunicar por meio de um servidor proxy para o serviço, depois de instalar o agente, você precisará executar um comando descrito nas etapas abaixo. Esse comando define a variável de ambiente do sistema do servidor proxy `https_proxy` .

Se você não estiver familiarizado com as opções de linha de comando para pacotes do Windows Installer, examine [Opções de linha de comando msiexec](/windows/win32/msi/standard-installer-command-line-options) e [Opções de linha de comando msiexec](/windows/win32/msi/command-line-options).

Por exemplo, execute o programa de instalação com o parâmetro `/?` para revisar a opção de ajuda e referência rápida.

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /?
```

1. Para instalar o agente silenciosamente e criar um arquivo de log da instalação na pasta `C:\Support\Logs` existente, execute o comando a seguir.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentsetup.log"
    ```

    Se o agente não for iniciado após a conclusão da instalação, verifique os logs para obter informações de erro detalhadas. O diretório de log é *%ProgramData%\AzureConnectedMachineAgent\log*.

2. Se o computador precisar se comunicar por meio de um servidor proxy, para definir a variável de ambiente do servidor proxy, execute o seguinte comando:

    ```powershell
    [Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
    $env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
    # For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
    Restart-Service -Name himds
    ```

    >[!NOTE]
    >O agente não dá suporte à definição da autenticação de proxy nesta versão prévia.
    >

3. Depois de instalar o agente, você precisa configurá-lo para se comunicar com o serviço Azure Arc executando o seguinte comando:

    ```dos
    "%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID"
    ```

### <a name="install-with-the-scripted-method"></a>Instalação com o método com script

1. Faça logon no servidor.

1. Abra um prompt de comando com privilégios elevados do PowerShell.

    >[!NOTE]
    >O script só dá suporte à execução de uma versão de 64 bits do Windows PowerShell.
    >

1. Vá para a pasta ou o compartilhamento para o qual você copiou o script e execute-o no servidor executando o script `./OnboardingScript.ps1`.

Se o agente não for iniciado após a conclusão da instalação, verifique os logs para obter informações de erro detalhadas. O diretório de log é *%ProgramData%\AzureConnectedMachineAgent\log*.

## <a name="install-and-validate-the-agent-on-linux"></a>Instalar e validar o agente no Linux

O agente do Connected Machine para Linux é fornecido no formato de pacote preferencial para a distribuição (.RPM ou .DEB) hospedada no [repositório de pacotes](https://packages.microsoft.com/) da Microsoft. O [pacote de script do shell `Install_linux_azcmagent.sh`](https://aka.ms/azcmagent) executa as seguintes ações:

* Configura o computador host para baixar o pacote do agente em packages.microsoft.com.

* Instala o pacote do provedor de recursos híbrido.

Opcionalmente, você pode configurar o agente com as suas informações de proxy, incluindo o parâmetro `--proxy "{proxy-url}:{proxy-port}"`.

O script também contém a lógica para identificar as distribuições com e sem suporte e verifica as permissões necessárias para executar a instalação.

O seguinte exemplo baixa o agente e o instala:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent.
bash ~/Install_linux_azcmagent.sh
```

1. Para baixar e instalar o agente, incluindo o parâmetro `--proxy` para configurar o agente para se comunicar por meio do servidor proxy, execute os seguintes comandos:

    ```bash
    # Download the installation package.
    wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

    # Install the connected machine agent.
    bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
    ```

2. Depois de instalar o agente, você precisa configurá-lo para se comunicar com o serviço Azure Arc executando o seguinte comando:

    ```bash
    azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --cloud "cloudName"
    if [ $? = 0 ]; then echo "\033[33mTo view your onboarded server(s), navigate to https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.HybridCompute%2Fmachines\033[m"; fi
    ```

### <a name="install-with-the-scripted-method"></a>Instalação com o método com script

1. Faça logon no servidor com uma conta que tenha acesso à raiz.

1. Vá para a pasta ou o compartilhamento para o qual você copiou o script e execute-o no servidor executando o script `./OnboardingScript.sh`.

Se o agente não for iniciado após a conclusão da instalação, verifique os logs para obter informações de erro detalhadas. O diretório de log é *var/opt/azcmagent/log*.

## <a name="verify-the-connection-with-azure-arc"></a>Verificar a conexão com o Azure Arc

Depois de instalar o agente e configurá-lo para se conectar aos servidores habilitados para Azure Arc, acesse o portal do Azure para verificar se o servidor foi conectado com êxito. Veja seus computadores no [portal do Azure](https://aka.ms/hybridmachineportal).

![Uma conexão de servidor bem-sucedida](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Próximas etapas

- Informações de solução de problemas podem ser encontradas no [guia solucionar problemas do agente do computador conectado](troubleshoot-agent-onboard.md).

- Saiba como gerenciar seu computador usando [Azure Policy](../../governance/policy/overview.md), para itens como [configuração de convidado](../../governance/policy/concepts/guest-configuration.md)de VM, verifique se o computador está relatando para o espaço de trabalho esperado log Analytics, habilite o monitoramento com o [Azure monitor com VMs](../../azure-monitor/vm/vminsights-enable-policy.md)e muito mais.

- Saiba mais sobre o [Agente do Log Analytics](../../azure-monitor/agents/log-analytics-agent.md). O agente Log Analytics para Windows e Linux é necessário quando você deseja coletar dados de monitoramento de carga de trabalho e sistema operacional com o Azure Monitor para VMs, gerenciá-los usando runbooks de automação ou recursos como Gerenciamento de Atualizações ou usar outros serviços do Azure, como a [central de segurança do Azure](../../security-center/security-center-introduction.md).
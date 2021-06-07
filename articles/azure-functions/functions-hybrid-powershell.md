---
title: Gerenciar recursos locais remotos usando funções do PowerShell
description: Saiba como configurar Conexões Híbridas na retransmissão do Azure para conectar um aplicativo de funções do PowerShell a recursos locais, que podem ser usados para gerenciar remotamente o recurso local.
author: eamono
ms.topic: conceptual
ms.date: 04/26/2020
ms.author: eamono
ms.openlocfilehash: 5e01ffd8e17fda9113c7ec0fdb2c7f436b39c810
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97936899"
---
# <a name="managing-hybrid-environments-with-powershell-in-azure-functions-and-app-service-hybrid-connections"></a>Gerenciando ambientes híbridos com o PowerShell no Azure Functions e no serviço de aplicativo Conexões Híbridas

O recurso Conexões Híbridas do serviço de Azure App permite o acesso a recursos em outras redes. Você pode saber mais sobre esse recurso na documentação do [conexões híbridas](../app-service/app-service-hybrid-connections.md) . Este artigo descreve como usar essa funcionalidade para executar funções do PowerShell direcionadas a um servidor local. Esse servidor pode então ser usado para gerenciar todos os recursos no ambiente local por meio de uma função Azure PowerShell.


## <a name="configure-an-on-premises-server-for-powershell-remoting"></a>Configurar um servidor local para comunicação remota do PowerShell

O script a seguir habilita a comunicação remota do PowerShell e cria uma nova regra de firewall e um ouvinte HTTPS do WinRM. Para fins de teste, é usado um certificado autoassinado. Em um ambiente de produção, recomendamos que você use um certificado assinado.

```powershell
# For configuration of WinRM, see
# https://docs.microsoft.com/windows/win32/winrm/installation-and-configuration-for-windows-remote-management.

# Enable PowerShell remoting.
Enable-PSRemoting -Force

# Create firewall rule for WinRM. The default HTTPS port is 5986.
New-NetFirewallRule -Name "WinRM HTTPS" `
                    -DisplayName "WinRM HTTPS" `
                    -Enabled True `
                    -Profile "Any" `
                    -Action "Allow" `
                    -Direction "Inbound" `
                    -LocalPort 5986 `
                    -Protocol "TCP"

# Create new self-signed-certificate to be used by WinRM.
$Thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME  -CertStoreLocation Cert:\LocalMachine\My).Thumbprint

# Create WinRM HTTPS listener.
$Cmd = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:COMPUTERNAME ""; CertificateThumbprint=""$Thumbprint""}"
cmd.exe /C $Cmd
```

## <a name="create-a-powershell-function-app-in-the-portal"></a>Criar um aplicativo de funções do PowerShell no portal

O recurso de Conexões Híbridas do serviço de aplicativo está disponível apenas nos planos de preços básico, padrão e isolado. Ao criar o aplicativo de funções com o PowerShell, crie ou selecione um desses planos.

1. No menu do portal do Azure ou na **Página Inicial**, selecione **Criar um recurso**.

1. Na página **Novo**, selecione **Computação** > **Aplicativo de Funções**.

1. Na página **Informações Básicas**, use as configurações do aplicativo de funções especificadas na tabela a seguir.

    | Configuração      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **Assinatura** | Sua assinatura | A assinatura na qual este novo aplicativo de funções será criado. |
    | **[Grupo de Recursos](../azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Nome do novo grupo de recursos no qual criar o seu aplicativo de funções. |
    | **Nome do aplicativo de funções** | Nome globalmente exclusivo | Nome que identifica seu novo aplicativo de funções. Os caracteres válidos são `a-z` (não diferencia maiúsculas de minúsculas), `0-9` e `-`.  |
    |**Publicar**| Código | Opção para publicar arquivos de código ou um contêiner do Docker. |
    | **Pilha de runtime** | Linguagem preferencial | Escolha PowerShell Core. |
    |**Versão**| Número de versão | Escolha a versão do seu runtime instalado.  |
    |**Região**| Região preferencial | Escolha uma [região](https://azure.microsoft.com/regions/) perto de você ou perto de outros serviços que suas funções acessam. |

    :::image type="content" source="./media/functions-hybrid-powershell/function-app-create-basics.png" alt-text="Crie uma função-noções básicas do aplicativo." border="true":::

1. Selecione **Avançar: Hospedagem**. Na página **Hospedagem**, insira as configurações a seguir.

    | Configuração      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **[Conta de armazenamento](../storage/common/storage-account-create.md)** |  Nome globalmente exclusivo |  Crie uma conta de armazenamento usada pelo seu aplicativo de funções. Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres e podem conter apenas números e letras minúsculas. Use também uma conta existente, que precisará atender aos [requisitos da conta de armazenamento](../azure-functions/storage-considerations.md#storage-account-requirements). |
    |**Sistema operacional**| Sistema operacional preferencial | Um sistema operacional é pré-selecionado para você com base na seleção da pilha de runtime, mas você pode alterar a configuração, se necessário. |
    | **[Tipo de plano](../azure-functions/functions-scale.md)** | **Plano do serviço de aplicativo** | Escolha **plano do serviço de aplicativo**. Quando você executa em um Plano do Serviço de Aplicativo, deve gerenciar o [dimensionamento do aplicativo de funções](../azure-functions/functions-scale.md).  |

    :::image type="content" source="./media/functions-hybrid-powershell/function-app-create-hosting.png" alt-text="Crie uma função-Hospedagem de aplicativos." border="true":::

1. Selecione **Avançar: Monitoramento**. Na página **Monitoramento**, insira as configurações a seguir.

    | Configuração      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../azure-functions/functions-monitoring.md)** | Padrão | Cria um recurso do Application Insights do mesmo *nome do aplicativo* na região com suporte mais próxima. Ao expandir essa configuração ou selecionar **Criar**, você pode alterar o nome do Application Insights ou escolher uma região diferente em uma [geografia do Azure](https://azure.microsoft.com/global-infrastructure/geographies/) onde deseja armazenar seus dados. |

    :::image type="content" source="./media/functions-hybrid-powershell/function-app-create-monitoring.png" alt-text="Crie uma função de monitoramento de aplicativo." border="true":::

1. Selecione **Examinar + criar** para examinar as seleções de configuração do aplicativo.

1. Na página **Examinar + criar**, examine as configurações e, em seguida, selecione **Criar** para provisionar e implantar o aplicativo de funções.

1. Selecione o ícone **Notificações** no canto superior direito do portal e veja se a mensagem **Implantação concluída com êxito** é exibida.

1. Selecione **Ir para recursos** para exibir o novo aplicativo de funções. Você também pode selecionar **Fixar no painel**. A fixação torna mais fácil retornar a esse recurso de aplicativo de função no seu painel.

## <a name="create-a-hybrid-connection-for-the-function-app"></a>Criar uma conexão híbrida para o aplicativo de funções

As conexões híbridas são configuradas na seção rede do aplicativo de funções:

1. Em **configurações** no aplicativo de funções que você acabou de criar, selecione **rede**. 
1. Selecione **configurar seus pontos de extremidade de conexões híbridas**.
   
    :::image type="content" source="./media/functions-hybrid-powershell/configure-hybrid-connection-endpoint.png" alt-text="Configure os pontos de extremidade de conexão híbrida." border="true":::

1. Selecione **Adicionar conexão híbrida**.
   
    :::image type="content" source="./media/functions-hybrid-powershell/hybrid-connection-overview.png" alt-text="Adicione uma conexão híbrida." border="true":::

1. Insira informações sobre a conexão híbrida conforme mostrado logo após a captura de tela a seguir. Você tem a opção de fazer com que a configuração de **host do ponto de extremidade** corresponda ao nome do host do servidor local para facilitar a memorização do servidor mais tarde, quando você estiver executando comandos remotos. A porta corresponde à porta padrão do serviço de gerenciamento remoto do Windows que foi definida no servidor anteriormente.
  
      :::image type="content" source="./media/functions-hybrid-powershell/add-hybrid-connection.png" alt-text="Adicionar conexão híbrida." border="true":::

    | Configuração      | Valor sugerido  |
    | ------------ | ---------------- |
    | **Nome da conexão híbrida** | ContosoHybridOnPremisesServer |
    | **Host de ponto de extremidade** | finance1 |
    | **Porta do ponto de extremidade** | 5986 |
    | **Namespace do ServiceBus** | Criar Novo |
    | **Localidade** | Escolha um local disponível |
    | **Nome** | contosopowershellhybrid | 

1. Selecione **OK** para criar a conexão híbrida.

## <a name="download-and-install-the-hybrid-connection"></a>Baixar e instalar a conexão híbrida

1. Selecione **baixar Gerenciador de conexões** para salvar o arquivo *. msi* localmente no seu computador.

    :::image type="content" source="./media/functions-hybrid-powershell/download-hybrid-connection-installer.png" alt-text="Baixe o instalador." border="true":::

1. Copie o arquivo *. msi* do seu computador local para o servidor no local.
1. Execute o instalador do Gerenciador de Conexões Híbridas para instalar o serviço no servidor local.

    :::image type="content" source="./media/functions-hybrid-powershell/hybrid-installation.png" alt-text="Instale a conexão híbrida." border="true":::

1. No portal, abra a conexão híbrida e copie a cadeia de conexão do gateway para a área de transferência.

    :::image type="content" source="./media/functions-hybrid-powershell/copy-hybrid-connection.png" alt-text="Copie a cadeia de conexão híbrida." border="true":::

1. Abra a interface do usuário do Gerenciador de Conexões Híbridas no servidor local.

    :::image type="content" source="./media/functions-hybrid-powershell/hybrid-connection-ui.png" alt-text="Abra a interface do usuário de conexão híbrida." border="true":::

1. Selecione **inserir manualmente** e cole a cadeia de conexão da área de transferência.

    :::image type="content" source="./media/functions-hybrid-powershell/enter-manual-connection.png" alt-text="Cole a conexão híbrida." border="true":::

1. Reinicie o Gerenciador de Conexões Híbridas do PowerShell se ele não for exibido como conectado.
    ```powershell
    Restart-Service HybridConnectionManager
    ```

## <a name="create-an-app-setting-for-the-password-of-an-administrator-account"></a>Criar uma configuração de aplicativo para a senha de uma conta de administrador

1. Em **configurações** para seu aplicativo de funções, selecione **configuração**. 
1. Selecione **+ nova configuração de aplicativo**.

    :::image type="content" source="./media/functions-hybrid-powershell/select-configuration.png" alt-text="Configure uma senha para a conta de administrador." border="true":::

1. Nomeie a configuração **ContosoUserPassword** e insira a senha. Selecione **OK**.
1. Selecione **salvar** para armazenar a senha no aplicativo de funções.

    :::image type="content" source="./media/functions-hybrid-powershell/save-administrator-password.png" alt-text="Salve a senha da conta de administrador." border="true":::

## <a name="create-a-function-http-trigger"></a>Criar um gatilho HTTP function

1. Em seu aplicativo de funções, selecione **funções** e, em seguida, selecione **+ Adicionar**.

    :::image type="content" source="./media/functions-hybrid-powershell/create-http-trigger-function.png" alt-text="Criar novo gatilho HTTP." border="true":::

1. Selecione o modelo de **gatilho http** .

    :::image type="content" source="./media/functions-hybrid-powershell/select-http-trigger-template.png" alt-text="Selecione o modelo de gatilho HTTP." border="true":::

1. Nomeie a nova função e selecione **criar função**.

    :::image type="content" source="./media/functions-hybrid-powershell/create-new-http-function.png" alt-text="Nomeie e crie a nova função de gatilho HTTP." border="true":::

## <a name="test-the-function"></a>Testar a função

1. Na nova função, selecione **código + teste**. Substitua o código do PowerShell do modelo pelo código a seguir:

    ```powershell
    # Input bindings are passed in via param block.
    param($Request, $TriggerMetadata)
    
    # Write to the Azure Functions log stream.
    Write-Output "PowerShell HTTP trigger function processed a request."
    
    # Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword.
    $UserName = "ContosoUser"
    $securedPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
    $Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)
    
    # This is the name of the hybrid connection Endpoint.
    $HybridEndpoint = "finance1"
    
    $Script = {
        Param(
            [Parameter(Mandatory=$True)]
            [String] $Service
        )
        Get-Service $Service
    }
    
    Write-Output "Scenario 1: Running command via Invoke-Command"
    Invoke-Command -ComputerName $HybridEndpoint `
                   -Credential $Credential `
                   -Port 5986 `
                   -UseSSL `
                   -ScriptBlock $Script `
                   -ArgumentList "*" `
                   -SessionOption (New-PSSessionOption -SkipCACheck)
    ```

1. Clique em **Salvar**.

    :::image type="content" source="./media/functions-hybrid-powershell/save-http-function.png" alt-text="Altere o código do PowerShell e salve a função de gatilho HTTP." border="true":::

 1. Selecione **teste** e, em seguida, selecione **executar** para testar a função. Examine os logs para verificar se o teste foi bem-sucedido.

     :::image type="content" source="./media/functions-hybrid-powershell/test-function-hybrid.png" alt-text="Testar a função do gatilho HTTP." border="true":::

## <a name="managing-other-systems-on-premises"></a>Gerenciando outros sistemas locais

Você pode usar o servidor local conectado para se conectar a outros servidores e sistemas de gerenciamento no ambiente local. Isso permite que você gerencie suas operações de datacenter do Azure usando suas funções do PowerShell. O script a seguir registra uma sessão de configuração do PowerShell que é executada sob as credenciais fornecidas. Essas credenciais devem ser para um administrador nos servidores remotos. Você pode usar essa configuração para acessar outros pontos de extremidade no servidor local ou Datacenter.

```powershell
# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword.
$UserName = "ContosoUser"
$SecuredPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
$Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)

# This is the name of the hybrid connection Endpoint.
$HybridEndpoint = "finance1"

# The remote server that will be connected to run remote PowerShell commands on
$RemoteServer = "finance2".

Write-Output "Use hybrid connection server as a jump box to connect to a remote machine"

# We are registering an endpoint that runs under credentials ($Credential) that has access to the remote server.
$SessionName = "HybridSession"
$ScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $SessionName)

    if (-not (Get-PSSessionConfiguration -Name $SessionName -ErrorAction SilentlyContinue))
    {
        Register-PSSessionConfiguration -Name $SessionName -RunAsCredential $Using:Credential
    }
}

Write-Output "Registering session on hybrid connection jumpbox"
Invoke-Command -ComputerName $HybridEndpoint `
               -Credential $Credential `
               -Port 5986 `
               -UseSSL `
               -ScriptBlock $ScriptCommand `
               -ArgumentList $SessionName `
               -SessionOption (New-PSSessionOption -SkipCACheck)

# Script to run on the jump box to run against the second machine.
$RemoteScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $ComputerName)
        # Write out the hostname of the hybrid connection server.
        hostname
        # Write out the hostname of the remote server.
        Invoke-Command -ComputerName $ComputerName -Credential $Using:Credential -ScriptBlock {hostname} `
                        -UseSSL -Port 5986 -SessionOption (New-PSSessionOption -SkipCACheck)
}

Write-Output "Running command against remote machine via jumpbox by connecting to the PowerShell configuration session"
Invoke-Command -ComputerName $HybridEndpoint `
               -Credential $Credential `
               -Port 5986 `
               -UseSSL `
               -ScriptBlock $RemoteScriptCommand `
               -ArgumentList $RemoteServer `
               -SessionOption (New-PSSessionOption -SkipCACheck) `
               -ConfigurationName $SessionName
```

Substitua as seguintes variáveis neste script pelos valores aplicáveis do seu ambiente:
* $HybridEndpoint
* $RemoteServer

Nos dois cenários anteriores, você pode conectar e gerenciar seus ambientes locais usando o PowerShell em Azure Functions e Conexões Híbridas. Incentivamos você a aprender mais sobre o [conexões híbridas](../app-service/app-service-hybrid-connections.md) e o [PowerShell no functions](./functions-reference-powershell.md).

Você também pode usar [redes virtuais](./functions-create-vnet.md) do Azure para se conectar ao seu ambiente local por meio de Azure functions.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"] 
> [Saiba mais sobre como trabalhar com funções do PowerShell](functions-reference-powershell.md)

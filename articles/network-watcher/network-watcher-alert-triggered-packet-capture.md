---
title: Usar a captura de pacote para fazer o monitoramento de rede proativo com alertas e o Azure Functions | Microsoft Docs
description: Este artigo descreve como criar uma captura de pacotes disparada por alertas com o Observador de Rede do Azure
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 1b3da4d6e4593f3c71995ef9331fcea2d5b6ec19
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-packet-capture-for-proactive-network-monitoring-with-alerts-and-azure-functions"></a>Usar a captura de pacotes para fazer um monitoramento de rede proativo com alertas e o Azure Functions

A captura de pacotes do Observador de Rede permite criar sessões de captura para controlar o tráfego dentro e fora de máquinas virtuais. O arquivo de captura pode ter um filtro que é definido para acompanhar apenas o tráfego que você deseja monitorar. Esses dados são armazenados em um blob de armazenamento ou localmente na máquina convidada.

Esse recurso pode ser iniciado remotamente a partir de outros cenários de automação como o Azure Functions. A captura de pacotes fornece a capacidade de executar capturas proativas com base nos problemas de rede definidos. Outros usos incluem a coleta de estatísticas de rede, obtenção de informações sobre as invasões de rede, depuração de comunicações cliente-servidor e muito mais.

Os recursos implantados no Azure estão em execução 24/7. Você e sua equipe não pode monitorar ativamente o status de todos os recursos 24/7. Por exemplo, o que acontecerá se ocorrer um problema às 2:00 da manhã?

Usando o Observador de Rede, Alertas e Funções de dentro do ecossistema do Azure, você pode responder proativamente com dados e ferramentas para resolver problemas em sua rede.

![Cenário][scenario]

## <a name="prerequisites"></a>Pré-requisitos

* A versão mais recente do [Azure PowerShell](/powershell/azure/install-azurerm-ps).
* Uma instância existente do Observador de Rede. Se você ainda não tiver um, [crie uma instância do Observador de Rede](network-watcher-create.md).
* Uma máquina virtual existente na mesma região que o Observador de Rede com a [extensão Windows](../virtual-machines/windows/extensions-nwa.md) ou [extensão de máquina virtual Linux](../virtual-machines/linux/extensions-nwa.md).

## <a name="scenario"></a>Cenário

Neste exemplo, sua VM está enviando mais segmentos TCP que o usual e você quer ser alertado. Os segmentos TCP são usados como um exemplo aqui, e você pode utilizar qualquer condição de alerta.

Quando você for alertado, você deseja receber dados de nível de pacote para entender por que aumentou a comunicação. Em seguida, você pode tomar medidas para retornar a máquina virtual para comunicação regular.

Este cenário pressupõe que você tem uma instância existente do Observador de Rede e um grupo de recursos com uma máquina virtual válida.

A lista a seguir é uma visão geral do fluxo de trabalho que ocorre:

1. Um alerta é disparado em sua VM.
1. O alerta chama sua Função do Azure por meio de um webhook.
1. A função do Azure processa o alerta e inicia uma sessão de captura de pacotes do Observador de Rede.
1. A captura de pacotes é executada na VM e coleta o tráfego.
1. O pacote de captura é carregado em uma conta de armazenamento para um diagnóstico e análise.

Para automatizar esse processo, criamos e conectamos um Alerta em nossa VM para disparar quando ocorre o incidente. Também criamos uma função a ser chamada no Observador de Rede.

Esse cenário faz o seguinte:

* Cria uma função do Azure que inicia uma captura de pacotes.
* Cria uma regra de alerta em uma máquina virtual e configura a regra de alerta para chamar a função do Azure.

## <a name="create-an-azure-function"></a>Criar uma função do Azure

A primeira etapa é criar uma função do Azure para processar o alerta e criar uma captura de pacotes.

1. No [Portal do Azure](https://portal.azure.com), selecione **Novo** > **Computação** > **Aplicativo de Funções**.

    ![Criar um aplicativo de funções][1-1]

2. Na folha **Aplicativo de funções**, insira os seguintes valores e selecione **OK** para criar o aplicativo:

    |**Configuração** | **Valor** | **Detalhes** |
    |---|---|---|
    |**Nome do aplicativo**|PacketCaptureExample|O nome do aplicativo de funções.|
    |**Assinatura**|[Sua assinatura]A assinatura na qual a criar o aplicativo de funções.||
    |**Grupo de recursos**|PacketCaptureRG|O nome do grupo de recursos para conter o aplicativo de funções.|
    |**Plano de hospedagem**|Plano de consumo| O tipo de plano de que seu aplicativo de funções usa. As opções são planos de consumo ou planos do serviço de aplicativo do Azure. |
    |**Localidade**|Centro dos EUA| A região na qual um aplicativo de funções será criado.|
    |**Conta de armazenamento**|{gerado automaticamente}| A conta de armazenamento que o Azure Functions usa para armazenamento de finalidade geral.|

3. Na folha **Aplicativos do Functions PacketCaptureExample**, selecione **Functions** > **Função personalizada** >**+**.

4. Selecione **HttpTrigger-Powershell** e, em seguida, insira as informações restantes. Finalmente, para criar a função, selecione **Criar**.

    |**Configuração** | **Valor** | **Detalhes** |
    |---|---|---|
    |**Cenário**|Experimental|Tipo de cenário|
    |**Nomeie sua função**|AlertPacketCapturePowerShell|Nome da função|
    |**Nível de autorização**|Função|Nível de autorização para a função|

![Exemplo de funções][functions1]

> [!NOTE]
> O modelo do PowerShell é experimental e não tem suporte completo.

As personalizações são necessárias para este exemplo e explicadas nas seguintes etapas.

### <a name="add-modules"></a>Adicionar módulos

Para usar os cmdlets do PowerShell no Observador de Rede, faça upload do último módulo do PowerShell no aplicativo de funções.

1. No computador local com os módulos mais recentes do Azure PowerShell instalados, execute o seguinte comando do PowerShell:

    ```powershell
    (Get-Module AzureRM.Network).Path
    ```

    Esse exemplo fornece o caminho local dos módulos do Azure PowerShell. Essas pastas são usadas em uma etapa posterior. Os módulos usados neste cenário são:

    * AzureRM.Network

    * AzureRM.profile

    * AzureRM.Resources

    ![Pastas do PowerShell][functions5]

1. Selecione **Configurações do Aplicativo de funções** > **Ir para o Editor do Serviço de Aplicativo**.

    ![Configurações do aplicativo de funções][functions2]

1. Clique com o botão direito do mouse na pasta **AlertPacketCapturePowershell** e crie uma pasta chamada **azuremodules**. 

4. Crie uma subpasta para cada módulo de que você precisa.

    ![Pastas e subpastas][functions3]

    * AzureRM.Network

    * AzureRM.profile

    * AzureRM.Resources

1. Clique com o botão direito do mouse na subpasta **AzureRM.Network** e selecione **Fazer Upload de Arquivos**. 

6. Vá para os módulos do Azure. Na pasta **AzureRM.Network** local, selecione todos os arquivos na pasta. Depois, selecione **OK**. 

7. Repita essas etapas para **AzureRM.Profile** e **AzureRM.Resources**.

    ![Carregar arquivos][functions6]

1. Ao concluir, cada pasta deve ter os arquivos de módulo do PowerShell do seu computador local.

    ![Arquivos do PowerShell][functions7]

### <a name="authentication"></a>Autenticação

Para usar os cmdlets do PowerShell, você deve se autenticar. Configure a autenticação no aplicativo de funções. Para configurar a autenticação, você deverá configurar as variáveis de ambiente e carregar um arquivo de chave criptografado no aplicativo de funções.

> [!NOTE]
> Esse cenário fornece apenas um exemplo de como implementar a autenticação com o Azure Functions. Há outras maneiras de fazer isso.

#### <a name="encrypted-credentials"></a>Credenciais criptografadas

O seguinte script do PowerShell cria um arquivo de chave chamado **PassEncryptKey.key**. Ele também fornece uma versão criptografada da senha fornecida. Essa senha é a mesma senha que é definida para o Aplicativo do Azure Active Directory que é usado para autenticação.

```powershell
#Variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#Keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#Get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

No Editor do Serviço de Aplicativo do aplicativo de funções, crie uma pasta chamada **chaves** em **AlertPacketCapturePowerShell**. Em seguida, carregue o arquivo **PassEncryptKey.key** criado no exemplo anterior do PowerShell.

![Chave de funções][functions8]

### <a name="retrieve-values-for-environment-variables"></a>Recuperar valores de variáveis de ambiente

O requisito final é instalar as variáveis de ambiente necessárias para acessar os valores de autenticação. A seguinte lista mostra as variáveis de ambiente criadas:

* AzureClientID

* AzureTenant

* AzureCredPassword


#### <a name="azureclientid"></a>AzureClientID

A ID do cliente é a ID de um aplicativo no Azure Active Directory.

1. Caso ainda não tenha um aplicativo a ser usado, execute o exemplo a seguir para criar um.

    ```powershell
    $app = New-AzureRmADApplication -DisplayName "ExampleAutomationAccount_MF" -HomePage "https://exampleapp.com" -IdentifierUris "https://exampleapp1.com/ExampleFunctionsAccount" -Password "<same password as defined earlier>"
    New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
    Start-Sleep 15
    New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $app.ApplicationId
    ```

   > [!NOTE]
   > A senha que você usa ao criar o aplicativo deve ser a mesma senha criada anteriormente ao salvar o arquivo de chave.

1. No Portal do Azure, selecione **Assinaturas**. Selecione a assinatura para usar e, em seguida, selecione **Controle de Acesso (IAM)**.

    ![IAM de funções][functions9]

1. Escolha a conta a ser usada e selecione **Propriedades**. Copie a ID do Aplicativo.

    ![ID do Aplicativo de funções][functions10]

#### <a name="azuretenant"></a>AzureTenant

Obtenha a ID do locatário com a execução do seguinte exemplo do PowerShell:

```powershell
(Get-AzureRmSubscription -SubscriptionName "<subscriptionName>").TenantId
```

#### <a name="azurecredpassword"></a>AzureCredPassword

O valor da variável de ambiente AzureCredPassword é o valor que você obtém executando o exemplo a seguir do PowerShell. Esse é o mesmo exemplo mostrado na seção anterior, **Credenciais criptografadas**. O valor necessário é a saída da variável `$Encryptedpassword`.  Essa é a senha da entidade de serviço que você criptografou usando o script do PowerShell.

```powershell
#Variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#Keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#Get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

### <a name="store-the-environment-variables"></a>Armazenar as variáveis de ambiente

1. Vá para o aplicativo de funções. Selecione **Configurações do aplicativo de função** > **Definir configurações de aplicativo**.

    ![Definir configurações de aplicativo][functions11]

1. Adicione as variáveis de ambiente e seus valores às configurações do aplicativo e selecione **Salvar**.

    ![Configurações do aplicativo][functions12]

### <a name="add-powershell-to-the-function"></a>Adicione o PowerShell para a função

Agora é hora de fazer chamadas no Observador de Rede de dentro da função do Azure. Dependendo dos requisitos, a implementação dessa função pode variar. No entanto, o fluxo geral do código é assim:

1. Processar os parâmetros de entrada.
2. Consultar as capturas de pacotes existentes para verificar os limites e resolver os conflitos de nomenclatura.
3. Criar uma captura de pacotes com os devidos parâmetros.
4. Pesquisar a captura de pacotes periodicamente até concluir.
5. Notificar o usuário que a sessão de captura de pacotes foi concluída.

O exemplo a seguir é o código do PowerShell que pode ser usado na função. Há valores que precisam ser substituídos em **subscriptionId**, **resourceGroupName** e **storageAccountName**.

```powershell
            #Import Azure PowerShell modules required to make calls to Network Watcher
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Profile\AzureRM.Profile.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Network\AzureRM.Network.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Resources\AzureRM.Resources.psd1" -Global

            #Process alert request body
            $requestBody = Get-Content $req -Raw | ConvertFrom-Json

            #Storage account ID to save captures in
            $storageaccountid = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}"

            #Packet capture vars
            $packetcapturename = "PSAzureFunction"
            $packetCaptureLimit = 10
            $packetCaptureDuration = 10

            #Credentials
            $tenant = $env:AzureTenant
            $pw = $env:AzureCredPassword
            $clientid = $env:AzureClientId
            $keypath = "D:\home\site\wwwroot\AlertPacketCapturePowerShell\keys\PassEncryptKey.key"

            #Authentication
            $secpassword = $pw | ConvertTo-SecureString -Key (Get-Content $keypath)
            $credential = New-Object System.Management.Automation.PSCredential ($clientid, $secpassword)
            Add-AzureRMAccount -ServicePrincipal -Tenant $tenant -Credential $credential #-WarningAction SilentlyContinue | out-null


            #Get the VM that fired the alert
            if($requestBody.context.resourceType -eq "Microsoft.Compute/virtualMachines")
            {
                Write-Output ("Subscription ID: {0}" -f $requestBody.context.subscriptionId)
                Write-Output ("Resource Group:  {0}" -f $requestBody.context.resourceGroupName)
                Write-Output ("Resource Name:  {0}" -f $requestBody.context.resourceName)
                Write-Output ("Resource Type:  {0}" -f $requestBody.context.resourceType)

                #Get the Network Watcher in the VM's region
                $nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $requestBody.context.resourceRegion}
                $networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName

                #Get existing packetCaptures
                $packetCaptures = Get-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher

                #Remove existing packet capture created by the function (if it exists)
                $packetCaptures | %{if($_.Name -eq $packetCaptureName)
                { 
                    Remove-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName $packetCaptureName
                }}

                #Initiate packet capture on the VM that fired the alert
                if ((Get-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher).Count -lt $packetCaptureLimit){
                    echo "Initiating Packet Capture"
                    New-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $requestBody.context.resourceId -PacketCaptureName $packetCaptureName -StorageAccountId $storageaccountid -TimeLimitInSeconds $packetCaptureDuration
                    Out-File -Encoding Ascii -FilePath $res -inputObject "Packet Capture created on ${requestBody.context.resourceID}"
                }
            } 
 ``` 
#### <a name="retrieve-the-function-url"></a>Recuperar a URL da função 
1. Depois de criar sua função, configure o alerta para chamar a URL associada à função. Para obter esse valor, copie a URL da função a partir de seu aplicativo de funções.

    ![Localizando a URL da função][functions13]

2. Copie a URL da função para seu Aplicativo de funções.

    ![Copiando a URL da função][2]

Se você precisar de propriedades personalizadas no conteúdo da solicitação POST do webhook, confira [Configurar um webhook em um alerta de métrica do Azure](../monitoring-and-diagnostics/insights-webhooks-alerts.md).

## <a name="configure-an-alert-on-a-vm"></a>Configurar um alerta em uma VM

Os alertas podem ser configurados para notificar as pessoas quando uma métrica específica cruza um limite atribuído a ela. Neste exemplo, o alerta está nos segmentos TCP enviados, mas o alerta pode ser disparado para muitas outras métricas. No exemplo, um alerta é configurado para chamar um webhook para chamar a função.

### <a name="create-the-alert-rule"></a>Criar a regra de alerta

Vá até uma máquina virtual existente e adicione uma regra de alerta. Mais documentação detalhada sobre como configurar alertas pode ser encontrada em [Criar alertas do Monitor do Azure para serviços do Azure - Portal do Azure](../monitoring-and-diagnostics/insights-alerts-portal.md). Insira os seguintes valores na folha **Regra de alerta** e selecione **OK**.

  |**Configuração** | **Valor** | **Detalhes** |
  |---|---|---|
  |**Nome**|TCP_Segments_Sent_Exceeded|Nome da regra de alerta.|
  |**Descrição**|Segmentos TCP enviados limite excedido|A descrição para a regra de alerta.||
  |**Métrica**|Segmentos TCP enviados| A métrica para usar para disparar o alerta. |
  |**Condição**|Maior que| A condição para usar ao avaliar a métrica.|
  |**Limite**|100| O valor da métrica que dispara o alerta. Esse valor deve ser definido como um valor válido para o seu ambiente.|
  |**Período**|Nos últimos cinco minutos| Determina o período no qual procurar o limite na métrica.|
  |**Webhook**|[URL do webhook do aplicativo de funções]| A URL de webhook do aplicativo de funções que foi criada nas etapas anteriores.|

> [!NOTE]
> A métrica de segmentos TCP não está habilitada por padrão. Saiba mais sobre como habilitar outras métricas visitando [Habilitar o monitoramento e o diagnóstico](../monitoring-and-diagnostics/insights-how-to-use-diagnostics.md).

## <a name="review-the-results"></a>Revise os resultados

Após os critérios para os gatilhos de alerta, uma captura de pacote será criada. Vá para Observador de Rede e, em seguida, selecione **Captura de pacote**. Nesta página, você pode selecionar o link de arquivo de captura de pacote para baixar a captura de pacote.

![Exibir a captura de pacotes][functions14]

Se o arquivo de captura for armazenado localmente, você poderá recuperá-lo entrando na máquina virtual.

Para obter instruções sobre como baixar os arquivos das contas de armazenamento do Azure, veja [Introdução ao armazenamento de Blobs do Azure usando o .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Outra ferramenta que você pode usar é o [Gerenciador de armazenamento](http://storageexplorer.com/).

Depois que a captura for baixada, você poderá exibi-la usando qualquer ferramenta que possa ler um arquivo **.cap**. Os links para duas dessas ferramentas são:

- [Analisador de Mensagens da Microsoft](https://technet.microsoft.com/library/jj649776.aspx)
- [WireShark](https://www.wireshark.org/)

## <a name="next-steps"></a>Próximas etapas

Saiba como exibir as capturas de pacotes visitando [Análise da captura de pacotes com o Wireshark](network-watcher-deep-packet-inspection.md).


[1]: ./media/network-watcher-alert-triggered-packet-capture/figure1.png
[1-1]: ./media/network-watcher-alert-triggered-packet-capture/figure1-1.png
[2]: ./media/network-watcher-alert-triggered-packet-capture/figure2.png
[3]: ./media/network-watcher-alert-triggered-packet-capture/figure3.png
[functions1]:./media/network-watcher-alert-triggered-packet-capture/functions1.png
[functions2]:./media/network-watcher-alert-triggered-packet-capture/functions2.png
[functions3]:./media/network-watcher-alert-triggered-packet-capture/functions3.png
[functions4]:./media/network-watcher-alert-triggered-packet-capture/functions4.png
[functions5]:./media/network-watcher-alert-triggered-packet-capture/functions5.png
[functions6]:./media/network-watcher-alert-triggered-packet-capture/functions6.png
[functions7]:./media/network-watcher-alert-triggered-packet-capture/functions7.png
[functions8]:./media/network-watcher-alert-triggered-packet-capture/functions8.png
[functions9]:./media/network-watcher-alert-triggered-packet-capture/functions9.png
[functions10]:./media/network-watcher-alert-triggered-packet-capture/functions10.png
[functions11]:./media/network-watcher-alert-triggered-packet-capture/functions11.png
[functions12]:./media/network-watcher-alert-triggered-packet-capture/functions12.png
[functions13]:./media/network-watcher-alert-triggered-packet-capture/functions13.png
[functions14]:./media/network-watcher-alert-triggered-packet-capture/functions14.png
[scenario]:./media/network-watcher-alert-triggered-packet-capture/scenario.png

---
title: Conectar-se a Azure Resource Manager em seu dispositivo de GPU pro Azure Stack Edge
description: Descreve como se conectar ao Azure Resource Manager em execução em sua GPU do Azure Stack Edge pro usando o Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: 27c0c211b9844a34b0cb43dcd05037ba4a548f3e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102637650"
---
# <a name="connect-to-azure-resource-manager-on-your-azure-stack-edge-pro-device"></a>Conectar-se a Azure Resource Manager em seu dispositivo do Azure Stack Edge pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

O Azure Resource Manager fornece uma camada de gerenciamento que lhe permite criar, atualizar e excluir recursos em sua assinatura do Azure. O dispositivo pro Edge Azure Stack dá suporte às mesmas APIs de Azure Resource Manager para criar, atualizar e excluir VMs em uma assinatura local. Esse suporte permite que você gerencie o dispositivo de maneira consistente com a nuvem. 

Este tutorial descreve como se conectar às APIs locais em seu dispositivo Azure Stack Edge pro por meio de Azure Resource Manager usando Azure PowerShell.

## <a name="about-azure-resource-manager"></a>Sobre o Azure Resource Manager

Azure Resource Manager fornece uma camada de gerenciamento consistente para chamar a API de dispositivo do Azure Stack Edge pro e executar operações como criar, atualizar e excluir VMs. A arquitetura do Azure Resource Manager é detalhada no diagrama a seguir.

![Diagrama para Azure Resource Manager](media/azure-stack-edge-gpu-connect-resource-manager/edge-device-flow.svg)


## <a name="endpoints-on-azure-stack-edge-pro-device"></a>Pontos de extremidade no dispositivo pro Azure Stack Edge

A tabela a seguir resume os vários pontos de extremidade expostos em seu dispositivo, os protocolos com suporte e as portas para acessar esses pontos de extremidade. Ao longo do artigo, você encontrará referências a esses pontos de extremidade.

| # | Ponto de extremidade | Protocolos com suporte | Porta usada | Usada para |
| --- | --- | --- | --- | --- |
| 1. | Azure Resource Manager | HTTPS | 443 | Para se conectar ao Azure Resource Manager para automação |
| 2. | Serviço de token de segurança | HTTPS | 443 | Para autenticar por meio de tokens de acesso e de atualização |
| 3. | Blob | HTTPS | 443 | Para se conectar ao armazenamento de BLOBs via REST |


## <a name="connecting-to-azure-resource-manager-workflow"></a>Conectando-se ao Azure Resource Manager fluxo de trabalho

O processo de conexão a APIs locais do dispositivo usando o Azure Resource Manager requer as seguintes etapas:

| Etapa nº | Você fará esta etapa... | .. neste local. |
| --- | --- | --- |
| 1. | [Configurar seu dispositivo pro Edge Azure Stack](#step-1-configure-azure-stack-edge-pro-device) | Interface do Usuário da Web local |
| 2. | [Criar e instalar certificados](#step-2-create-and-install-certificates) | Interface do usuário da Web local/cliente do Windows |
| 3. | [Examinar e configurar os pré-requisitos](#step-3-install-powershell-on-the-client) | Windows Client |
| 4. | [Configurar Azure PowerShell no cliente](#step-4-set-up-azure-powershell-on-the-client) | Windows Client |
| 5. | [Modificar arquivo de host para resolução de nome de ponto de extremidade](#step-5-modify-host-file-for-endpoint-name-resolution) | Cliente do Windows ou servidor DNS |
| 6. | [Verifique se o nome do ponto de extremidade está resolvido](#step-6-verify-endpoint-name-resolution-on-the-client) | Windows Client |
| 7. | [Use Azure PowerShell cmdlets para verificar a conexão com Azure Resource Manager](#step-7-set-azure-resource-manager-environment) | Windows Client |

As seções a seguir detalham cada uma das etapas acima na conexão com o Azure Resource Manager.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se o cliente usado para se conectar ao dispositivo via Azure Resource Manager está usando o TLS 1,2. Para obter mais informações, acesse [configurar TLS 1,2 em cliente Windows acessando Azure Stack dispositivo do Edge pro](azure-stack-edge-gpu-configure-tls-settings.md).

## <a name="step-1-configure-azure-stack-edge-pro-device"></a>Etapa 1: configurar Azure Stack dispositivo pro Edge 

Execute as etapas a seguir na interface do usuário da Web local do seu dispositivo Azure Stack Edge pro.

1. Conclua as configurações de rede para seu dispositivo Azure Stack Edge pro. 

    ![Página "Configurações de rede" da IU da Web local](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png)


    Anote o endereço IP do dispositivo. Você usará esse IP mais tarde.

2. Configure o nome do dispositivo e o domínio DNS na página do **dispositivo** . Anote o nome do dispositivo e o domínio DNS, pois você os usará posteriormente.

    ![Página "Dispositivo" da IU da Web local](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-2.png)

    > [!IMPORTANT]
    > O nome do dispositivo, domínio DNS, será usado para formar os pontos de extremidade expostos.
    > Use os pontos de extremidade Azure Resource Manager e BLOB da página do **dispositivo** na interface do usuário da Web local.


## <a name="step-2-create-and-install-certificates"></a>Etapa 2: criar e instalar certificados

Os certificados asseguram que sua comunicação seja confiável. Em seu dispositivo Azure Stack Edge pro, o dispositivo autoassinado, o blob e os certificados de Azure Resource Manager são gerados automaticamente. Opcionalmente, você pode colocar seu próprio blob assinado e Azure Resource Manager certificados também.

Quando você coloca um certificado assinado por conta própria, também precisa da cadeia de assinatura correspondente do certificado. Para a cadeia de assinatura, Azure Resource Manager e os certificados de blob no dispositivo, você precisará dos certificados correspondentes no computador cliente também para autenticar e se comunicar com o dispositivo.

Para se conectar ao Azure Resource Manager, você precisará criar ou obter a cadeia de assinatura e os certificados de ponto de extremidade, importar esses certificados no cliente do Windows e, por fim, carregar esses certificados no dispositivo.

### <a name="create-certificates-optional"></a>Criar certificados (opcional)

Para uso somente de teste e desenvolvimento, você pode usar o Windows PowerShell para criar certificados no sistema local. Ao criar os certificados para o cliente, siga estas diretrizes:

1. Primeiro, você precisa criar um certificado raiz para a cadeia de assinatura. Para obter mais informações, consulte as etapas para [criar certificados de cadeia de assinatura](azure-stack-edge-gpu-manage-certificates.md#create-signing-chain-certificate).

2. Em seguida, você pode criar os certificados de ponto de extremidade para o blob e Azure Resource Manager. Você pode obter esses pontos de extremidade na página do **dispositivo** na interface do usuário da Web local. Consulte as etapas para [criar certificados de ponto de extremidade](azure-stack-edge-gpu-manage-certificates.md#create-signed-endpoint-certificates).

3. Para todos esses certificados, verifique se o nome da entidade e o nome alternativo da entidade estão em conformidade com as seguintes diretrizes:

    |Type |Nome da entidade (SN)  |SAN (nome alternativo da entidade)  |Exemplo de nome da entidade |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Armazenamento de Blobs|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |Certificado único de várias SANs para ambos os pontos de extremidade|`<Device name>.<dnsdomain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |

Para obter mais informações sobre certificados, acesse como [gerenciar certificados](azure-stack-edge-gpu-manage-certificates.md).

### <a name="upload-certificates-on-the-device"></a>Carregar certificados no dispositivo

Os certificados que você criou na etapa anterior estarão no repositório pessoal do cliente. Esses certificados precisam ser exportados em seu cliente em arquivos de formato apropriados que possam ser carregados em seu dispositivo.

1. O certificado raiz deve ser exportado como um arquivo de formato DER com a extensão de arquivo *. cer* . Para obter etapas detalhadas, consulte [Exportar certificados como um arquivo de formato. cer](azure-stack-edge-gpu-manage-certificates.md#export-certificates-as-der-format).

2. Os certificados de ponto de extremidade devem ser exportados como arquivos *. pfx* com chaves privadas. Para obter etapas detalhadas, consulte [Exportar certificados como arquivo. pfx com chaves privadas](azure-stack-edge-gpu-manage-certificates.md#export-certificates-as-pfx-format-with-private-key).

3. Os certificados raiz e de ponto de extremidade são carregados no dispositivo usando a opção **+ Adicionar certificado** na página **certificados** na interface do usuário da Web local. Para carregar os certificados, siga as etapas em [carregar certificados](azure-stack-edge-gpu-manage-certificates.md#upload-certificates).


### <a name="import-certificates-on-the-client-running-azure-powershell"></a>Importar certificados no cliente que executa o Azure PowerShell

O cliente do Windows no qual você invocará o Azure Resource Manager APIs precisa estabelecer confiança com o dispositivo. Para esse fim, os certificados que você criou na etapa anterior devem ser importados em seu cliente Windows para o repositório de certificados apropriado.

1. O certificado raiz que você exportou como o formato DER com a extensão *. cer* agora deve ser importado nas autoridades de certificação raiz confiáveis no seu sistema cliente. Para obter etapas detalhadas, consulte [importar certificados para o repositório de autoridades de certificação raiz confiáveis.](azure-stack-edge-gpu-manage-certificates.md#import-certificates-as-der-format)

2. Os certificados de ponto de extremidade que você exportou como *. pfx* devem ser exportados como *. cer*. Esse *. cer* é então importado no repositório de certificados **pessoal** do seu sistema. Para obter etapas detalhadas, consulte [importar certificados para o repositório pessoal](azure-stack-edge-gpu-manage-certificates.md#import-certificates-as-der-format).

## <a name="step-3-install-powershell-on-the-client"></a>Etapa 3: instalar o PowerShell no cliente 

O cliente do Windows deve atender aos seguintes pré-requisitos:

1. Execute o PowerShell versão 5,0. Você deve ter o PowerShell versão 5,0. Não há suporte para o PowerShell Core. Para verificar a versão do PowerShell em seu sistema, execute o seguinte cmdlet:

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Compare a versão **principal** e verifique se ela é 5,0 ou posterior.

    Se você tiver uma versão desatualizada, consulte [Atualizar o Windows PowerShell existente](/powershell/scripting/install/installing-windows-powershell?view=powershell-6&preserve-view=true#upgrading-existing-windows-powershell).

    Se você não \' tiver o PowerShell 5,0, siga a [instalação do Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6&preserve-view=true).

    Um exemplo de saída é mostrado abaixo.

    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved. 
    Try the new cross-platform PowerShell https://aka.ms/pscore6
    PS C:\windows\system32> $PSVersionTable.PSVersion
    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      18362  145
    ```
    
2. Você pode acessar o Galeria do PowerShell.

    Execute o PowerShell como administrador. Verifique se o `PSGallery` está registrado como um repositório.

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop
    Get-PSRepository -Name "PSGallery"
    ```
    
    Um exemplo de saída é mostrado abaixo.
    
    ```powershell
    PS C:\windows\system32> Import-Module -Name PowerShellGet -ErrorAction Stop
    PS C:\windows\system32> Import-Module -Name PackageManagement -ErrorAction Stop
    PS C:\windows\system32> Get-PSRepository -Name "PSGallery"
    Name                      InstallationPolicy   SourceLocation
    ----                      ------------------   --------------
    PSGallery                 Trusted              https://www.powershellgallery.com/api/v2
    ```
    
Se o repositório não for confiável ou se você precisar de mais informações, consulte [validar o Galeria do PowerShell acessibilidade](/azure-stack/operator/azure-stack-powershell-install?view=azs-1908&preserve-view=true&preserve-view=true#2-validate-the-powershell-gallery-accessibility).

## <a name="step-4-set-up-azure-powershell-on-the-client"></a>Etapa 4: configurar Azure PowerShell no cliente 

<!--1. Verify the API profile of the client and identify which version of the Azure PowerShell modules and libraries to include on your client. In this example, the client system will be running Azure Stack 1904 or later. For more information, see [Azure Resource Manager API profiles](/azure-stack/user/azure-stack-version-profiles?view=azs-1908#azure-resource-manager-api-profiles).-->

1. Você instalará Azure PowerShell módulos em seu cliente que funcionarão com seu dispositivo.

    a. Execute o PowerShell como administrador. Você precisa ter acesso à galeria do PowerShell. 


    b. Para instalar os módulos de Azure PowerShell necessários do Galeria do PowerShell, execute o seguinte comando:

    ```powershell
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet.
    
    Install-Module -Name AzureRM.BootStrapper
    
   # Install and import the API Version Profile into the current PowerShell session.
    
    Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
    
    # Confirm the installation of PowerShell
    Get-Module -Name "Azure*" -ListAvailable
    ```
    
    Verifique se você tem a versão do módulo do Azure-RM 2.5.0 em execução no final da instalação. 
    Se você tiver uma versão existente do módulo Azure-RM que não corresponde à versão necessária, desinstale usando o seguinte comando:

    `Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose`

    Agora, você precisará instalar a versão necessária novamente.
   

Uma saída de exemplo é mostrada abaixo, que indica que os módulos 2.5.0 da versão AzureRM foram instalados com êxito.

```powershell
PS C:\windows\system32> Install-Module -Name AzureRM.BootStrapper
PS C:\windows\system32> Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
Loading Profile 2019-03-01-hybrid
PS C:\windows\system32> Get-Module -Name "Azure*" -ListAvailable
 
    Directory: C:\Program Files\WindowsPowerShell\Modules
 
ModuleType Version    Name                          ExportedCommands
---------- -------    ----                          ----------------
Script     4.5.0      Azure.Storage                       {Get-AzureStorageTable, New-AzureStorageTableSASToken, New...
Script     2.5.0      AzureRM
Script     0.5.0      AzureRM.BootStrapper                {Update-AzureRmProfile, Uninstall-AzureRmProfile, Install-...
Script     4.6.1      AzureRM.Compute                     {Remove-AzureRmAvailabilitySet, Get-AzureRmAvailabilitySet...
Script     3.5.1      AzureRM.Dns                         {Get-AzureRmDnsRecordSet, New-AzureRmDnsRecordConfig, Remo...
Script     5.1.5      AzureRM.Insights                    {Get-AzureRmMetricDefinition, Get-AzureRmMetric, Remove-Az...
Script     4.2.0      AzureRM.KeyVault                    {Add-AzureKeyVaultCertificate, Set-AzureKeyVaultCertificat...
Script     5.0.1      AzureRM.Network                     {Add-AzureRmApplicationGatewayAuthenticationCertificate, G...
Script     5.8.3      AzureRM.profile                     {Disable-AzureRmDataCollection, Disable-AzureRmContextAuto...
Script     6.4.3      AzureRM.Resources                   {Get-AzureRmProviderOperation, Remove-AzureRmRoleAssignmen...
Script     5.0.4      AzureRM.Storage                     {Get-AzureRmStorageAccount, Get-AzureRmStorageAccountKey, ...
Script     4.0.2      AzureRM.Tags                        {Remove-AzureRmTag, Get-AzureRmTag, New-AzureRmTag}
Script     4.0.3      AzureRM.UsageAggregates             Get-UsageAggregates
Script     5.0.1      AzureRM.Websites                    {Get-AzureRmAppServicePlan, Set-AzureRmAppServicePlan, New...

 
    Directory: C:\Program Files (x86)\Microsoft Azure Information Protection\Powershell
 
ModuleType Version    Name                            ExportedCommands
---------- -------    ----                           ----------------
Binary     1.48.204.0 AzureInformationProtection          {Clear-RMSAuthentication, Get-RMSFileStatus, Get-RMSServer...
```


## <a name="step-5-modify-host-file-for-endpoint-name-resolution"></a>Etapa 5: modificar o arquivo de host para a resolução de nome do ponto de extremidade 

Agora, você adicionará o VIP consistente do Azure que você definiu na interface do usuário da Web local do dispositivo para:

- Ao arquivo de host no cliente OU
- À configuração do servidor DNS

> [!IMPORTANT]
> Recomendamos que você modifique a configuração do servidor DNS para resolução de nome de ponto de extremidade.

No cliente Windows que você está usando para se conectar ao dispositivo, execute as seguintes etapas:

1. Inicie o **bloco de notas** como administrador e, em seguida, abra o arquivo de **hosts** localizado em C:\Windows\System32\Drivers\etc.

    ![Arquivo hosts do Windows Explorer](media/azure-stack-edge-gpu-connect-resource-manager/hosts-file.png)

2. Adicione as seguintes entradas ao arquivo **hosts** substituindo-as por valores apropriados para seu dispositivo: 

    ```
    <Device IP> login.<appliance name>.<DNS domain>
    <Device IP> management.<appliance name>.<DNS domain>
    <Device IP> <storage name>.blob.<appliance name>.<DNS domain>
    ```

    > [!IMPORTANT]
    > A entrada no arquivo de hosts deve corresponder exatamente ao que foi fornecido para se conectar ao Azure Resource Manager em uma etapa posterior. Verifique se a entrada de domínio DNS aqui está em letras minúsculas.

    Você salvou o IP do dispositivo da interface do usuário da Web local em uma etapa anterior.

    O \<appliance name\> logon.\<DNS domain\> a entrada é o ponto de extremidade para o serviço de token de segurança (STS). O STS é responsável por criação, validação, renovação e cancelamento de tokens de segurança. O serviço de token de segurança é usado para criar o token de acesso e o token de atualização que são usados para comunicação contínua entre o dispositivo e o cliente.

3. Para referência, use a imagem a seguir. Salve o arquivo **hosts**.

    ![Arquivo hosts no Bloco de notas](media/azure-stack-edge-gpu-connect-resource-manager/hosts-file-notepad.png)

## <a name="step-6-verify-endpoint-name-resolution-on-the-client"></a>Etapa 6: verificar a resolução de nome do ponto de extremidade no cliente

Verifique se o nome do ponto de extremidade está resolvido no cliente que você está usando para se conectar ao VIP consistente do Azure.

1. Você pode usar o utilitário de linha de comando ping.exe para verificar se o nome do ponto de extremidade está resolvido. Dado um endereço IP, o comando ping retornará o nome do host TCP/IP do computador que você está \' rerastreando.

    Adicione a `-a` opção à linha de comando, conforme mostrado no exemplo abaixo. Se o nome do host for retornável, ele também retornará essas informações potencialmente valiosas na resposta.

    ![Executar ping no prompt de comando](media/azure-stack-edge-gpu-connect-resource-manager/ping-command-prompt.png)



## <a name="step-7-set-azure-resource-manager-environment"></a>Etapa 7: definir Azure Resource Manager ambiente

Defina o ambiente de Azure Resource Manager e verifique se a comunicação do dispositivo com o cliente via Azure Resource Manager está funcionando bem. Execute as seguintes etapas para esta verificação:


1. Use o cmdlet `Add-AzureRmEnvironment` para garantir que a comunicação pelo Azure Resource Manager esteja funcionando corretamente e que as chamadas à API estejam passando pela porta dedicada ao Azure Resource Manager, 443.

    O cmdlet `Add-AzureRmEnvironment` adiciona pontos de extremidade e metadados para permitir que os cmdlets do Azure Resource Manager se conectem a uma nova instância do Azure Resource Manager. 


    > [!IMPORTANT]
    > A URL do ponto de extremidade Azure Resource Manager que você fornece no cmdlet a seguir diferencia maiúsculas de minúsculas. Verifique se a URL do ponto de extremidade está em minúsculas e se corresponde ao que você forneceu no arquivo de hosts. Se o caso não corresponder, você verá um erro.

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>/"
    ```

    Um exemplo de saída é mostrado abaixo:
    
    ```powershell
    PS C:\windows\system32> Add-AzureRmEnvironment -Name AzDBE -ARMEndpoint https://management.dbe-n6hugc2ra.microsoftdatabox.com/
    
    Name  Resource Manager Url                    ActiveDirectory Authority
    ----  --------------------                   -------------------------
    AzDBE https://management.dbe-n6hugc2ra.microsoftdatabox.com https://login.dbe-n6hugc2ra.microsoftdatabox.com/adfs/
    ```

2. Defina o ambiente como Azure Stack Edge Pro e a porta a ser usada para chamadas do Azure Resource Manager como 443. Você define o ambiente de duas maneiras:

    - Defina o ambiente. Digite o seguinte comando:

    ```powershell
    Set-AzureRMEnvironment -Name <Environment Name>
    ```
    
    Para obter mais informações, vá para [set-AzureRMEnvironment](/powershell/module/azurerm.profile/set-azurermenvironment?view=azurermps-6.13.0&preserve-view=true).

    - Defina o ambiente embutido para cada cmdlet que você executar. Isso garante que todas as chamadas à API estejam passando pelo ambiente correto. Por padrão, as chamadas passarão pelo público do Azure, mas você deseja que elas passem pelo ambiente que você definiu para Azure Stack dispositivo pro Edge.

    - Veja mais informações sobre [como mudar os ambientes AzureRM](#switch-environments).

2. Chame APIs do dispositivo local para autenticar as conexões com o Azure Resource Manager. 

    1. Essas credenciais são para uma conta do computador local e são usadas somente para acesso à API.

    2. Você pode se conectar via `login-AzureRMAccount` comando ou via `Connect-AzureRMAccount` . 

        1. Para entrar, digite o comando a seguir. A ID do locatário nesta instância é embutida em código-c0257de7-538f-415c-993a-1b87a031879d. Use o nome de usuário e a senha a seguir.

            - **Nome de usuário**  -  *EdgeArmUser*

            - **Senha**  -  do [Defina a senha para Azure Resource Manager](azure-stack-edge-gpu-set-azure-resource-manager-password.md) e use essa senha para entrar. 

            ```powershell
            PS C:\windows\system32> $pass = ConvertTo-SecureString "<Your password>" -AsPlainText -Force;
            PS C:\windows\system32> $cred = New-Object System.Management.Automation.PSCredential("EdgeArmUser", $pass)
            PS C:\windows\system32> Connect-AzureRmAccount -EnvironmentName AzDBE -TenantId c0257de7-538f-415c-993a-1b87a031879d -credential $cred
            
            Account       SubscriptionName   TenantId            Environment
            -------       ----------------   --------            -----------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE
            
            PS C:\windows\system32>
            ```
                   
        
            Uma maneira alternativa de fazer logon é usar o `login-AzureRmAccount` cmdlet. 
            
            `login-AzureRMAccount -EnvironmentName <Environment Name>` -Tenantid c0257de7-538f-415c-993a-1b87a031879d 

            Aqui está um exemplo de saída do comando. 
         
            ```powershell
            PS C:\Users\Administrator> login-AzureRMAccount -EnvironmentName AzDBE -TenantId c0257de7-538f-415c-993a-1b87a031879d
            
            Account         SubscriptionName  TenantId              Environment
            -------         ----------------  --------              -------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE
            PS C:\Users\Administrator>
            ```



> [!IMPORTANT]
> A conexão com Azure Resource Manager expira a cada 1,5 horas ou se o dispositivo do Azure Stack Edge Pro for reiniciado. Se isso acontecer, todos os cmdlets que você executar retornará mensagens de erro para o efeito de que você não está mais conectado ao Azure. Você precisará entrar novamente.

## <a name="switch-environments"></a>Alternar ambientes

Execute `Disconnect-AzureRmAccount` o comando para alternar para outro `AzureRmEnvironment` . 

Se você usar `Set-AzureRmEnvironment` `Login-AzureRmAccount` o e o sem usar `Disconnect-AzureRmAccount` o, o ambiente não será realmente alternado.  

Os exemplos a seguir mostram como alternar entre dois ambientes `AzDBE1` e `AzDBE2` .

Primeiro, liste todos os ambientes existentes em seu cliente.


```azurepowershell
PS C:\WINDOWS\system32> Get-AzureRmEnvironment
Name    Resource Manager Url     ActiveDirectory Authority
----    --------------------      -------------------------
AzureChinaCloud   https://management.chinacloudapi.cn/                 https://login.chinacloudapi.cn/
AzureCloud        https://management.azure.com/                        https://login.microsoftonline.com/
AzureGermanCloud  https://management.microsoftazure.de/                https://login.microsoftonline.de/
AzDBE1            https://management.HVTG1T2-Test.microsoftdatabox.com https://login.hvtg1t2-test.microsoftdatabox.com/adfs/
AzureUSGovernment https://management.usgovcloudapi.net/                https://login.microsoftonline.us/
AzDBE2            https://management.CVV4PX2-Test.microsoftdatabox.com https://login.cvv4px2-test.microsoftdatabox.com/adfs/
```

Em seguida, obtenha em qual ambiente você está conectado por meio de seu Azure Resource Manager.

```azurepowershell
PS C:\WINDOWS\system32> Get-AzureRmContext |fl *

Name               : Default Provider Subscription (A4257FDE-B946-4E01-ADE7-674760B8D1A3) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE2
Subscription       : a4257fde-b946-4e01-ade7-674760b8d1a3
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```

Agora você deve se desconectar do ambiente atual antes de alternar para o outro ambiente.


```azurepowershell
PS C:\WINDOWS\system32> Disconnect-AzureRmAccount

Id                    : EdgeArmUser@localhost
Type                  : User
Tenants               : {c0257de7-538f-415c-993a-1b87a031879d}
AccessToken           :
Credential            :
TenantMap             : {}
CertificateThumbprint :
ExtendedProperties    : {[Subscriptions, A4257FDE-B946-4E01-ADE7-674760B8D1A3], [Tenants, c0257de7-538f-415c-993a-1b87a031879d]}
```

Faça logon em outro ambiente. O exemplo de saída é mostrado abaixo.

```azurepowershell
PS C:\WINDOWS\system32> Login-AzureRmAccount -Environment "AzDBE1" -TenantId $ArmTenantId

Account     SubscriptionName   TenantId        Environment
-------     ----------------   --------        -----------
EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE1
```

Execute este cmdlet para confirmar em qual ambiente você está conectado.

```azurepowershell
PS C:\WINDOWS\system32> Get-AzureRmContext |fl *

Name               : Default Provider Subscription (A4257FDE-B946-4E01-ADE7-674760B8D1A3) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE1
Subscription       : a4257fde-b946-4e01-ade7-674760b8d1a3
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```
Você passou para o ambiente desejado.

## <a name="next-steps"></a>Próximas etapas

[Implantar VMs no dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md).

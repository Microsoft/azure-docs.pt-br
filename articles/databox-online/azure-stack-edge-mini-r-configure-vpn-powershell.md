---
title: Configurar a VPN em seu dispositivo de borda de Azure Stack mini R usando Azure PowerShell
description: Descreve como configurar a VPN no seu Azure Stack dispositivo mini R de borda usando um script Azure PowerShell para criar recursos do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 11/17/2020
ms.author: alkohli
ms.openlocfilehash: 763ccd397d8cd704ca161032e65f17979bccb53b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466156"
---
# <a name="configure-vpn-on-your-azure-stack-edge-mini-r-device-via-azure-powershell"></a>Configurar a VPN em seu dispositivo de borda de Azure Stack mini R via Azure PowerShell

<!--[!INCLUDE [applies-to-r-skus](../../includes/azure-stack-edge-applies-to-r-sku.md)]-->

A opção VPN fornece uma segunda camada de criptografia para os dados em movimento do *TLS* do seu Azure Stack Edge de borda mini r ou Azure Stack o dispositivo pro Edge para o Azure. Você pode configurar a VPN em seu dispositivo de borda Azure Stack mini R via portal do Azure ou por meio do Azure PowerShell. 

Este artigo descreve as etapas necessárias para configurar uma VPN ponto a site (P2S) em seu dispositivo de borda de Azure Stack mini R usando um script Azure PowerShell para criar a configuração na nuvem. A configuração no dispositivo Azure Stack Edge é feita por meio da interface do usuário local.

## <a name="about-vpn-setup"></a>Sobre a configuração de VPN

Uma conexão de gateway de VPN P2S permite que você crie uma conexão segura com sua rede virtual a partir de um computador cliente individual ou seu dispositivo de mini-R Azure Stack Edge. Você inicia a conexão P2S do computador cliente ou do dispositivo. A conexão P2S, nesse caso, usa VPN IKEv2, uma solução de VPN IPsec baseada em padrões.

O fluxo de trabalho típico inclui as seguintes etapas:

1. Configure os pré-requisitos.
2. Configure os recursos necessários no Azure.
    1. Crie e configure uma rede virtual e sub-redes necessárias. 
    2. Criar e configurar um gateway de VPN do Azure (gateway de rede virtual).
    3. Configure o Firewall do Azure e adicione regras de rede e de aplicativo.
    4. Crie tabelas de roteamento do Azure e adicione rotas.
    5. Habilite o ponto a site no gateway de VPN.
        1. Adicione o pool de endereços de cliente.
        2. Configure o tipo de túnel.
        3. Configure o tipo de autenticação.
        4. Criar certificado.
        5. Carregar certificado.
    6. Baixar o catálogo telefônico.
3. Configure a VPN na interface do usuário da Web local do dispositivo. 
    1. Forneça o catálogo telefônico.
    2. Forneça o arquivo de marcas de serviço (JSON).


As etapas detalhadas são fornecidas nas seções a seguir.

## <a name="configure-prerequisites"></a>Configurar pré-requisitos

- Você deve ter acesso a um dispositivo de borda Azure Stack mini R que está instalado de acordo com as instruções em [instalar o dispositivo mini r do Azure Stack Edge](azure-stack-edge-mini-r-deploy-install.md). Este dispositivo estará estabelecendo uma conexão P2S com o Azure. 

- Você deve ter acesso a uma assinatura válida do Azure que esteja habilitada para Azure Stack serviço de borda no Azure. Use essa assinatura para criar um recurso correspondente no Azure para gerenciar seu dispositivo de borda de Azure Stack mini R.  

- Você tem acesso a um cliente Windows que usará para acessar seu dispositivo de borda do Azure Stack mini R. Você usará esse cliente para criar programaticamente a configuração na nuvem.

    1. Para instalar a versão necessária do PowerShell em seu cliente Windows, execute os seguintes comandos:

        ```azurepowershell
        Install-Module -Name Az -AllowClobber -Scope CurrentUser 
        Import-Module Az.Accounts
        ```
    2. Para se conectar à sua conta e assinatura do Azure, execute os seguintes comandos:

        ```azurepowershell
        Connect-AzAccount 
        Set-AzContext -Subscription "<Your subscription name>"
        ```
        Forneça o nome da assinatura do Azure que você está usando com seu Azure Stack dispositivo mini R de borda para configurar a VPN.

    3. [Baixe o script](https://aka.ms/ase-vpn-deployment) necessário para criar a configuração na nuvem. O script vai:
        
        - Crie uma rede virtual do Azure e as seguintes sub-redes: *GatewaySubnet* e *AzureFirewallSubnet*.
        - Criar e configurar um gateway de VPN do Azure.
        - Criar e configurar um gateway de rede local do Azure.
        - Crie e configure uma conexão VPN do Azure entre o gateway de VPN do Azure e o gateway de rede local.
        - Crie um firewall do Azure e adicione regras de rede, regras de aplicativo.
        - Crie uma tabela de roteamento do Azure e adicione rotas a ela.

    4. Crie o grupo de recursos no portal do Azure sob o qual você deseja que os recursos do Azure sejam criados. Vá para a lista de serviços em portal do Azure, selecione **grupo de recursos** e, em seguida, selecione **+ Adicionar**. Forneça as informações de assinatura e o nome do seu grupo de recursos e, em seguida, selecione **criar**. Se você acessar esse grupo de recursos, ele não deverá ter nenhum recurso sob ele no momento.

        ![Grupo de recursos do Azure](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-resource-group-1.png)
    
    5. Você precisará ter um certificado codificado base 64 no `.cer` formato para o dispositivo mini R do Azure Stack Edge. Esse certificado deve ser carregado em seu dispositivo de borda de Azure Stack como `pfx` com uma chave privada. Esse certificado também precisa ser instalado na raiz confiável do armazenamento no cliente que está tentando estabelecer a conexão P2S.

## <a name="use-the-script"></a>Usar o script

Primeiro, modifique o `parameters-p2s.json` arquivo para inserir seus parâmetros. Em seguida, execute o script usando o arquivo JSON modificado.

Cada uma dessas etapas é discutida nas seções a seguir.

### <a name="download-service-tags-file"></a>Baixar arquivo de marcas de serviço

Talvez você já tenha um `ServiceTags.json` arquivo na pasta em que você baixou o script. Caso contrário, você pode baixar o arquivo de marcas de serviço.

[!INCLUDE [azure-stack-edge-gateway-download-service-tags](../../includes/azure-stack-edge-gateway-download-service-tags.md)]

### <a name="modify-parameters-file"></a>Modificar arquivo de parâmetros

A primeira etapa seria modificar o `parameters-p2s.json` arquivo e salvar as alterações. 

Para os recursos do Azure que você criar, você fornecerá os seguintes nomes:

|Nome do parâmetro  |Descrição  |
|---------|---------|
|virtualNetworks_vnet_name    | Nome da rede virtual do Azure        |
|azureFirewalls_firewall_name     | Nome do firewall do Azure        |
|routeTables_routetable_name     | Nome da tabela de rotas do Azure        |
|publicIPAddresses_VNGW_public_ip_name     | Nome do endereço IP público para seu gateway de rede virtual       |
|virtualNetworkGateways_VNGW_name    | Nome do gateway de VPN do Azure (gateway de rede virtual)        |
|publicIPAddresses_firewall_public_ip_name     | Nome do endereço IP público para o Firewall do Azure         |
|local     |Essa é a região na qual você deseja criar sua rede virtual. Selecione a mesma região que aquela associada ao seu dispositivo.         |
|RouteTables_routetable_onprem_name| Esse é o nome da tabela de rotas adicional para ajudar o firewall a rotear pacotes de volta para Azure Stack dispositivo de borda. O script cria duas rotas adicionais e associa *Default* e *FirewallSubnet* a essa tabela de rotas.|

Forneça os seguintes endereços IP e espaços de endereço para os recursos do Azure que são criados, incluindo a rede virtual e sub-redes associadas (*padrão*, *Firewall*, *GatewaySubnet*).

|Nome do parâmetro  |Descrição  |
|---------|---------|
|VnetIPv4AddressSpace    | Este é o espaço de endereço associado à sua rede virtual. Forneça o intervalo IP da vnet como intervalo de IP privado ( https://en.wikipedia.org/wiki/Private_network#Private_IPv4_addresses) .     |
|DefaultSubnetIPv4AddressSpace    |Esse é o espaço de endereço associado à `Default` sub-rede para sua rede virtual.         |
|FirewallSubnetIPv4AddressSpace    |Esse é o espaço de endereço associado à `Firewall` sub-rede para sua rede virtual.          |
|GatewaySubnetIPv4AddressSpace    |Esse é o espaço de endereço associado ao `GatewaySubnet` para sua rede virtual.          |
|GatewaySubnetIPv4bgpPeeringAddress    | Esse é o endereço IP reservado para comunicação BGP e baseia-se no espaço de endereço associado ao `GatewaySubnet` para sua rede virtual.          |
|ClientAddressPool    | Esse endereço IP é usado para o pool de endereços na configuração P2S no portal do Azure.         |
|PublicCertData     | Os dados do certificado público são usados pelo gateway de VPN para autenticar clientes P2S conectados a ele. Para obter os dados do certificado, instale o certificado raiz. Verifique se o certificado é codificado em base-64 com uma extensão. cer. Abra esse certificado e copie o texto no certificado entre = = BEGIN CERTIFICATE = = e = = END CERTIFICATE = = em uma linha contínua.     |



### <a name="run-the-script"></a>Executar o script

Siga estas etapas para usar o modificado `parameters-p2s.json` e executar o script para criar recursos do Azure.

1. Execute o PowerShell. Alterne para o diretório onde o script está localizado.

3. Execute o script.

    `.\AzDeployVpn.ps1 -Location <Location> -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "<Service tag json file>"  -ResourceGroupName "<Resource group name>" -AzureDeploymentName "<Deployment name>" -NetworkRuleCollectionName "<Name for collection of network rules>" -Priority 115 -AppRuleCollectionName "<Name for collection of app rules>"`

    > [!NOTE]
    > Nesta versão, o script funciona apenas no local leste dos EUA.

    Você precisará inserir as seguintes informações ao executar o script:

    
    |Parâmetro  |Descrição  |
    |---------|---------|
    |Location     |Esta é a região em que os recursos do Azure devem ser criados.         |
    |AzureAppRuleFilePath     | Este é o caminho do arquivo para `appRule.json` .       |
    |AzureIPRangesFilePath     |Esse é o arquivo JSON de marca de serviço que você baixou na etapa anterior.         |
    |ResourceGroupName     | Esse é o nome do grupo de recursos no qual todos os recursos do Azure são criados.        |
    |AzureDeploymentName    |Este é o nome da sua implantação do Azure.         |
    |NetworkRuleCollectionName            | Este é o nome da coleção de todas as regras de rede que são criadas e adicionadas ao seu firewall do Azure.             |
    |Prioridade            | Essa é a prioridade atribuída a todas as regras de rede e de aplicativo que são criadas.              |
    |AppRuleCollectionName            |Este é o nome da coleção de todas as regras de aplicativo que são criadas e adicionadas ao seu firewall do Azure.                |


    Um exemplo de saída é mostrado abaixo.
    
    ```powershell
    PS C:\Offline docs\AzureVpnConfigurationScripts> .\AzDeployVpn.ps1 -Location eastus -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath ".\ServiceTags_Public_20200203.json" -ResourceGroupName "mytmarg3" -AzureDeploymentName "tmap2stestdeploy1" -NetworkRuleCollectionName "testnrc1" -Priority 115 -AppRuleCollectionName "testarc2"
        validating vpn deployment parameters
        Starting vpn deployment
        C:\Offline docs\AzureVpnConfigurationScripts\parameters-p2s.json
        C:\Offline docs\AzureVpnConfigurationScripts\template-p2s.json
        vpn deployment: tmap2stestdeploy1 started and status: Running
        Waiting for vpn deployment completion....
        ==== CUT ==================== CUT ==============
        Adding route 191.236.0.0/18 for AzureCloud.eastus
        Adding route 191.237.0.0/17 for AzureCloud.eastus
        Adding route 191.238.0.0/18 for AzureCloud.eastus
        Total Routes:294, Existing Routes: 74, New Routes Added: 220
        Additional routes getting added
    ```    

    > [!IMPORTANT]
    > - O script leva aproximadamente 90 minutos para ser executado. Certifique-se de entrar em sua rede diretamente antes que o script seja iniciado.
    > - Se por algum motivo houver uma sessão com falha com o script, certifique-se de excluir o grupo de recursos para excluir todos os recursos criados nele.
  
    
    Depois que o script for concluído, um log de implantação será gerado na mesma pasta em que o script reside.


## <a name="verify-the-azure-resources"></a>Verificar os recursos do Azure

Depois de executar o script com êxito, verifique se todos os recursos foram criados no Azure. Vá para o grupo de recursos que você criou. Você deve ver os seguintes recursos:

![Recursos do Azure](media/azure-stack-edge-mini-r-configure-vpn-powershell/script-resources.png)


<!--## Enable point-to-site in VPN gateway

1. In the Azure portal, go to the resource group and then select the virtual network gateway that you created in the earlier step.

    ![Azure virtual network gateway](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-virtual-network-gateway.png)

2. Go to **Settings > Point-to-site configuration**. Select **Configure now**.

    ![Enable P2S configuration](media/azure-stack-edge-mini-r-configure-vpn-powershell/enable-p2s.png)


3.  On the **Point-to-site configuration** blade:

    1. You'll add the client address pool. This pool is a range of private IP addresses that you specify. The clients that connect over a P2S VPN dynamically receive an IP address from this range. Use a private IP address range that does not overlap with the on-premises location that you connect from, or the VNet that you want to connect to. 
    2. You can select the tunnel type. For the VPN tunnel, you will use the IKEv2 protocol. 
    3. You will now define the type of authentication. Before Azure accepts a P2S VPN connection, the user has to be authenticated first. In this case, you authenticate using the native Azure certificate authentication. Set **Authentication type** to **Azure certificate**.
    4. You will now create and upload the certificates to Azure and your client. You will need to install a root certificate on your VPN gateway. The VPN gateway validates the client certificates when the P2S connection is being established by the client. The root certificate is required for the validation and must be uploaded to Azure.
    
        The clients trying to establish the P2S connection will have a client certificate that authenticates the connecting user.  To create these certificates, follow the steps in [Generate and export certificates for Point-to-Site using PowerShell](../vpn-gateway/vpn-gateway-certificates-point-to-site.md). 

        To install the root certificate, make sure the certificate is Base-64 encoded with a .cer extension. Open this certificate and copy the text in the certificate between ==BEGIN CERTIFICATE== and ==END CERTIFICATE== in one continuous line in the public certificate data under Root certificates.

        To upload the root certificates, follow the detailed steps in [Upload the root certificate public certificate data](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile).
    
    5. Save the configuration.

        ![Save P2S configuration](media/azure-stack-edge-mini-r-configure-vpn-powershell/enable-p2s-config.png)-->

## <a name="download-phone-book-for-vpn-profile"></a>Baixar o catálogo telefônico para o perfil VPN

Nesta etapa, você baixará o perfil de VPN para seu dispositivo.

1. Na portal do Azure, vá para o grupo de recursos e selecione o gateway de rede virtual que você criou na etapa anterior.

    ![Gateway de rede virtual do Azure](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-virtual-network-gateway.png)

2. Vá para **configurações > configuração ponto a site**. Selecione **baixar cliente VPN**.

    ![Habilitar configuração de P2S 1](media/azure-stack-edge-mini-r-configure-vpn-powershell/download-vpn-client.png)

2. Salve o perfil compactado e extraia em seu cliente Windows.

    ![Habilitar configuração de P2S 2](media/azure-stack-edge-mini-r-configure-vpn-powershell/save-extract-profile.png)

3. Vá para a pasta *WindowsAmd64* e, em seguida, extraia o `.exe` : *VpnClientSetupAmd64.exe*.

    ![Habilitar P2S configuração 3](media/azure-stack-edge-mini-r-configure-vpn-powershell/extract-exe.png)

3. Crie um caminho temporário. Por exemplo:

    `C:\NewTemp\vnet\tmp`

4. Execute o PowerShell e vá para o diretório em que o `.exe` está localizado. Para executar o `.exe` , digite:

    `.\VpnClientSetupAmd64.exe /Q /C /T:"C:\NewTemp\vnet\tmp"`

5. O caminho temporário terá novos arquivos. Aqui está uma amostra de saída:

    ```powershell
    
    PS C:\windows\system32> cd "C:\Users\Ase\Downloads\vngw5\WindowsAmd64"
    PS C:\Users\Ase\Downloads\vngw5\WindowsAmd64> .\VpnClientSetupAmd64.exe /Q /C /T:"C:\NewTemp\vnet\tmp"
    PS C:\Users\Ase\Downloads\vngw5\WindowsAmd64> cd "C:\NewTemp\vnet"
    PS C:\NewTemp\vnet> ls .\tmp
    
        Directory: C:\NewTemp\vnet\tmp
    
    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    -a----         2/6/2020   6:18 PM            947 8c670077-470b-421a-8dd8-8cedb4f2f08a.cer
    -a----         2/6/2020   6:18 PM            155 8c670077-470b-421a-8dd8-8cedb4f2f08a.cmp
    -a----         2/6/2020   6:18 PM           3564 8c670077-470b-421a-8dd8-8cedb4f2f08a.cms
    -a----         2/6/2020   6:18 PM          11535 8c670077-470b-421a-8dd8-8cedb4f2f08a.inf
    -a----         2/6/2020   6:18 PM           2285 8c670077-470b-421a-8dd8-8cedb4f2f08a.pbk
    -a----         2/6/2020   6:18 PM           5430 azurebox16.ico
    -a----         2/6/2020   6:18 PM           4286 azurebox32.ico
    -a----         2/6/2020   6:18 PM         138934 azurevpnbanner.bmp
    -a----         2/6/2020   6:18 PM          46064 cmroute.dll
    -a----         2/6/2020   6:18 PM            196 routes.txt
    
    PS C:\NewTemp\vnet>
    ```

6. O arquivo *. pbk* é o catálogo telefônico do perfil VPN. Você usará isso na interface do usuário local.


## <a name="vpn-configuration-on-the-device"></a>Configuração de VPN no dispositivo

Siga estas etapas na interface do usuário local do seu dispositivo Azure Stack Edge.

1. Na interface do usuário local, vá para a página **VPN** . Em estado da VPN, selecione **Configurar**.

    ![Configurar VPN 1](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-1.png)

2. Na folha **Configurar VPN** :
    
    1. No arquivo carregar catálogo telefônico, aponte para o arquivo. pbk que você criou na etapa anterior.
    2. No arquivo de configuração carregar lista de IPS públicos, forneça o arquivo JSON do intervalo de IP do Data Center do Azure como entrada. Você baixou esse arquivo em uma etapa anterior de: [https://www.microsoft.com/download/details.aspx?id=56519](https://www.microsoft.com/download/details.aspx?id=56519) .
    3. Selecione **lesteus** como a região e selecione **aplicar**.

    ![Configurar VPN 2](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-2.png)

3. Na seção **intervalos de endereços IP a serem acessados usando somente VPN** , insira o intervalo de vnet IPv4 que você escolheu para sua rede virtual do Azure.

    ![Configurar VPN 3](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-3.png)

## <a name="verify-client-connection"></a>Verificar conexão do cliente

1. No portal do Azure, vá para o gateway de VPN.
2. Vá para **configurações > configuração ponto a site**. Em **endereços IP alocados**, o endereço IP do seu dispositivo de borda de Azure Stack deve aparecer.

## <a name="validate-data-transfer-through-vpn"></a>Validar a transferência de dados por meio de VPN

Para confirmar que a VPN está funcionando, copie os dados para um compartilhamento SMB. Siga as etapas em [Adicionar um compartilhamento](azure-stack-edge-j-series-manage-shares.md#add-a-share) no dispositivo do Azure Stack Edge. 

1. Copie um arquivo, por exemplo \data\pictures\waterfall.jpg para o compartilhamento SMB que você montou no sistema cliente. 
2. Para validar que os dados estão passando por VPN, enquanto os dados estão sendo copiados:

    1. Vá para o gateway de VPN no portal do Azure. 

    2. Vá para **monitoramento > métricas**.

    3. No painel direito, escolha o **escopo** como seu gateway de VPN, **métrica** como gateway P2S largura de banda e **agregação** como Méd.

    4. À medida que os dados estão sendo copiados, você verá um aumento na utilização da largura de banda e, quando a cópia de dados for concluída, a utilização da largura de banda será descartada.

        ![Métricas de VPN do Azure](media/azure-stack-edge-mini-r-configure-vpn-powershell/vpn-metrics-1.png)

3. Verifique se esse arquivo aparece em sua conta de armazenamento na nuvem.
 
## <a name="debug-issues"></a>Problemas de depuração

Para depurar quaisquer problemas, use os seguintes comandos:

```
Get-AzResourceGroupDeployment -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
```

A saída de exemplo é mostrada abaixo:


```azurepowershell
PS C:\Projects\TZL\VPN\Azure-VpnDeployment> Get-AzResourceGroupDeployment -DeploymentName "tznvpnrg14_deployment" -ResourceGroupName "tznvpnrg14"


DeploymentName          : tznvpnrg14_deployment
ResourceGroupName       : tznvpnrg14
ProvisioningState       : Succeeded
Timestamp               : 1/21/2020 6:23:13 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                                         Type                       Value
                          ===========================================  =========================  ==========
                          virtualNetworks_vnet_name                    String                     tznvpnrg14_vnet
                          azureFirewalls_firewall_name                 String                     tznvpnrg14_firewall
                          routeTables_routetable_name                  String                     tznvpnrg14_routetable
                          publicIPAddresses_VNGW_public_ip_name        String                     tznvpnrg14_vngwpublicip
                          virtualNetworkGateways_VNGW_name             String                     tznvpnrg14_vngw
                          publicIPAddresses_firewall_public_ip_name    String                     tznvpnrg14_fwpip
                          localNetworkGateways_LNGW_name               String                     tznvpnrg14_lngw
                          connections_vngw_lngw_name                   String                     tznvpnrg14_connection
                          location                                     String                     East US
                          vnetIPv4AddressSpace                         String                     172.24.0.0/16
                          defaultSubnetIPv4AddressSpace                String                     172.24.0.0/24
                          firewallSubnetIPv4AddressSpace               String                     172.24.1.0/24
                          gatewaySubnetIPv4AddressSpace                String                     172.24.2.0/24
                          gatewaySubnetIPv4bgpPeeringAddress           String                     172.24.2.254
                          customerNetworkAddressSpace                  String                     10.0.0.0/18
                          customerPublicNetworkAddressSpace            String                     207.68.128.0/24
                          dbeIOTNetworkAddressSpace                    String                     10.139.218.0/24
                          azureVPNsharedKey                            String                     1234567890
                          dbE-Gateway-ipaddress                        String                     207.68.128.113

Outputs                 :
                          Name                     Type                       Value
                          =======================  =========================  ==========
                          virtualNetwork           Object                     {
                            "provisioningState": "Succeeded",
                            "resourceGuid": "dcf673d3-5c73-4764-b077-77125eda1303",
                            "addressSpace": {
                              "addressPrefixes": [
                                "172.24.0.0/16"
                              ]
================= CUT ============================= CUT ===========================
```


```
Get-AzResourceGroupDeploymentOperation -ResourceGroupName $ResourceGroupName -DeploymentName $AzureDeploymentName
```

## <a name="next-steps"></a>Próximas etapas

[Configure a VPN por meio da interface do usuário local em seu dispositivo Azure Stack Edge](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md#configure-vpn).
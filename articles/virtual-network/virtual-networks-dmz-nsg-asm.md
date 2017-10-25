---
title: "Exemplo de DMZ do Azure ‑ Criar um DMZ simples com NSGs | Microsoft Docs"
description: "Criar uma DMZ com grupos de segurança de rede (NSG)"
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: 
ms.assetid: f8622b1d-c07d-4ea6-b41c-4ae98d998fff
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: ed172d552e1e4c9ee27c58abcd7ad2d98df21579
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="example-1--build-a-simple-dmz-using-nsgs-with-classic-powershell"></a>Exemplo 1 – Criar uma DMZ simples usando NSGs com o PowerShell clássico
[Voltar à página Práticas recomendadas de limite de segurança][HOME]

> [!div class="op_single_selector"]
> * [Modelo do Resource Manager](virtual-networks-dmz-nsg.md)
> * [Clássico - PowerShell](virtual-networks-dmz-nsg-asm.md)
> 
>

Este exemplo criará uma DMZ simples com quatro servidores Windows e Grupos de Segurança de Rede. Este exemplo descreve cada um dos comandos relevantes do PowerShell para fornecer uma compreensão mais profunda de cada etapa. Também há uma seção Cenário de Tráfego para fornecer um passo a passo detalhado sobre como o tráfego passa pelas camadas de defesa da rede de perímetro. Por fim, na seção de referências, há o código e as instruções completas para criar este ambiente para testar e experimentar diversos cenários. 

![DMZ de entrada com NSG][1]

## <a name="environment-description"></a>Descrição do ambiente
Neste exemplo, uma assinatura contém os seguintes recursos:

* Dois serviços de nuvem: "FrontEnd001" e "BackEnd001"
* Uma rede virtual, “CorpNetwork”, com duas sub-redes, “FrontEnd” e “BackEnd”
* Um grupo de segurança de rede que é aplicado a ambas as sub-redes
* Um Windows Server que representa um servidor Web de aplicativos ("IIS01")
* Dois servidores Windows que representam servidores de back-end de aplicativos (“AppVM01”, “AppVM02”)
* Um servidor Windows que representa um servidor DNS ("DNS01")

Na seção de referências, há um script do PowerShell que cria a maior parte do ambiente descrito no exemplo. A criação das VMs e das Redes Virtuais, embora seja feita por script de exemplo, não será descrita em detalhes neste documento. 

Para criar o ambiente;

1. Salve o arquivo xml de configuração de rede incluído na seção de referências (atualizado com nomes, localização e endereços IP que correspondam ao cenário determinado)
2. Atualize as variáveis do usuário no script para fazer a correspondência do ambiente em que o script deve ser executado (assinaturas, nomes de serviço, etc.)
3. Execute o script no PowerShell

>[!Note]
>A região representada no script do PowerShell deve corresponder à região representada no arquivo xml de configuração de rede.
>
>

Depois que o script é executado com sucesso, etapas adicionais podem ser seguidas; na seção de referências, há dois scripts para configurar o servidor Web e um servidor de aplicativos com um aplicativo Web simples para testar a configuração desta DMZ.

As seções a seguir fornecem uma descrição detalhada dos Grupos de Segurança de Rede e como eles funcionam neste exemplo, percorrendo as principais linhas do script do PowerShell.

## <a name="network-security-groups-nsg"></a>Grupos de segurança de rede (NSG)
Neste exemplo, um grupo NSG é criado e então carregado com seis regras. 

> [!TIP]
> Em geral, você deve criar suas regras específicas "Permitir" primeiro e então as regras “Negar” mais genéricas por último. A prioridade atribuída ditará quais regras serão avaliadas primeiro. Quando o tráfego se aplicar a uma regra específica, nenhuma regra adicional será avaliada. As regras NSG podem se aplicar na direção de entrada ou de saída (na perspectiva da sub-rede).
> 
> 

Declarativamente, as regras a seguir estão sendo criadas para tráfego de entrada:

1. O tráfego interno de DNS (porta 53) é permitido
2. O tráfego de RDP (porta 3389) da Internet para qualquer VM é permitido
3. O tráfego HTTP (porta 80) da Internet ao servidor Web (IIS01) é permitido
4. Todo tráfego (todas as portas) de IIS01 para AppVM1 é permitido
5. Todo o tráfego (todas as portas) da Internet para a Rede Virtual inteira (todas as sub-redes) é Negado
6. Todo tráfego (todas as portas) da sub-rede Frontend para a sub-rede Backend é negado

Com essas regras associadas a cada sub-rede, se uma solicitação HTTP tiver entrado da Internet para o servidor Web, ambas as regras 3 (permitir) e 5 (negar) se aplicariam, mas já que a regra 3 tem uma prioridade maior, somente ela se aplicaria e a regra 5 não seria utilizada. Portanto, a solicitação HTTP seria permitida para o servidor Web. Se esse mesmo tráfego estivesse tentando acessar o servidor DNS01, a regra 5 (Negar) seria a primeira a ser aplicada e o tráfego não teria permissão para passar para o servidor. A regra 6 (Negar) impede que a sub-rede Frontend converse com a sub-rede Backend (exceto pelo tráfego permitido nas regras 1 e 4); esse conjunto de regras protege a rede Backend caso um invasor comprometa o aplicativo Web na rede Frontend, pois ele teria acesso limitado à rede Backend “protegida” (somente para recursos expostos no servidor AppVM01).

Há uma regra de saída padrão que permite o tráfego de saída para a Internet. Neste exemplo, permitiremos o tráfego de saída e não modificaremos quaisquer regras de saída. Para bloquear o tráfego em ambos os sentidos, o Roteamento Definido pelo Usuário é necessário e explorado no “Exemplo 3”, na [Página de práticas recomendadas dos limites de segurança][HOME].

Cada regra é discutida em mais detalhes da seguinte maneira (**Observação**: qualquer item na lista a seguir que comece com um sinal de cifrão (por exemplo, $NSGName) é uma variável definida pelo usuário do script na seção de referência deste documento):

1. Primeiro, um grupo de segurança de rede deve ser criado para conter as regras:

    ```PowerShell
    New-AzureNetworkSecurityGroup -Name $NSGName `
        -Location $DeploymentLocation `
        -Label "Security group for $VNetName subnets in $DeploymentLocation"
    ```

2. A primeira regra neste exemplo habilita o tráfego DNS entre todas as redes internas para o servidor DNS na sub-rede de back-end. A regra tem alguns parâmetros importantes:
   
   * “Type” (Tipo) indica em qual direção do fluxo de tráfego esta regra entra em vigor. Esta é a direção da perspectiva da sub-rede ou da máquina virtual (dependendo da associação desse NSG). Portanto, se o tipo é "Inbound" e tráfego está entrando na sub-rede, a regra se aplica e o tráfego que sai da sub-rede não é afetado por essa regra.
   * “Priority” (Prioridade) define a ordem na qual um fluxo de tráfego é avaliado. Quanto menor o número, maior a prioridade. Assim que uma regra se aplicar a um fluxo de tráfego específico, nenhuma regra adicional será processada. Portanto, se uma regra com prioridade 1 permite o tráfego e uma regra com prioridade 2 impede o tráfego e ambas as regras se aplicam ao tráfego, ele teria o fluxo permitido (já que a regra 1 tinha uma prioridade mais alta, ela vigorou e nenhuma regra adicional foi aplicada).
   * "Action" significa se um tráfego afetado por essa regra é bloqueado ou permitido.

    ```PowerShell    
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" `
        -Type Inbound -Priority 100 -Action Allow `
        -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[4] `
        -DestinationPortRange '53' `
        -Protocol *
    ```

3. Essa regra permite que o tráfego de RDP flua da Internet para a porta RDP em qualquer servidor na sub-rede associada. Essa regra usa dois tipos especiais de prefixos de endereço; “VIRTUAL_NETWORK” e “INTERNET”. Essas marcações são uma maneira fácil de resolver uma categoria maior de prefixos de endereço.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
         Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" `
         -Type Inbound -Priority 110 -Action Allow `
         -SourceAddressPrefix INTERNET -SourcePortRange '*' `
         -DestinationAddressPrefix VIRTUAL_NETWORK `
         -DestinationPortRange '3389' `
         -Protocol *
    ```

4. Essa regra permite que o tráfego da internet alcance o servidor Web. Essa regra não altera o comportamento de roteamento. A regra permite a passagem somente de tráfego destinado a IIS01. Portanto, se o tráfego da Internet tinha o servidor Web como seu destino, essa regra deve permiti-lo e deve parar de processar outras regras. (Na regra com prioridade 140, todos os outros tráfegos de entrada da Internet estão bloqueados.) Se você estiver processando somente tráfego HTTP, essa regra poderá ser mais restrita para permitir somente a porta de destino 80.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
         Set-AzureNetworkSecurityRule -Name "Enable Internet to $VMName[0]" `
         -Type Inbound -Priority 120 -Action Allow `
         -SourceAddressPrefix Internet -SourcePortRange '*' `
         -DestinationAddressPrefix $VMIP[0] `
         -DestinationPortRange '*' `
         -Protocol *
    ```

5. Essa regra permite que o tráfego passe do servidor IIS01 para o servidor AppVM01; uma regra posterior bloqueia todo o tráfego de Frontend para Backend. Para melhorar essa regra, se a porta for conhecida, ela deve ser adicionada. Por exemplo, se o servidor IIS está atingindo somente o SQL Server no AppVM01, o intervalo de porta de destino deve ser alterado de "*" (qualquer) para 1433 (a porta do SQL), permitindo uma menor superfície de ataque de entrada em AppVM01 se o aplicativo Web for comprometido.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Enable $VMName[1] to $VMName[2]" `
        -Type Inbound -Priority 130 -Action Allow `
        -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[2] `
        -DestinationPortRange '*' `
        -Protocol *
    ```

6. Essa regra nega o tráfego da Internet para todos os servidores na rede. Com a regra em prioridades 110 e 120, o efeito é que ela permite somente tráfego de entrada da Internet para o firewall e portas RDP nos servidores e bloqueia todo o resto. Essa é uma regra “à prova de falhas” para bloquear todos os fluxos inesperados.
    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule `
        -Name "Isolate the $VNetName VNet from the Internet" `
        -Type Inbound -Priority 140 -Action Deny `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK `
        -DestinationPortRange '*' `
        -Protocol *
    ```
7. A regra final nega odo o tráfego (todas as portas) da sub-rede Frontend para a sub-rede Backend. Como essa é uma regra somente de Entrada, o tráfego invertido é permitido (de Backend para Frontend).

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule `
        -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" `
        -Type Inbound -Priority 150 -Action Deny `
        -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
        -DestinationAddressPrefix $BEPrefix `
        -DestinationPortRange '*' `
        -Protocol * 
    ```

## <a name="traffic-scenarios"></a>Cenários de tráfego
#### <a name="allowed-internet-to-web-server"></a>(*Permitido*) Internet para servidor Web
1. Um usuário da Internet solicita uma página HTTP de FrontEnd001.CloudApp.Net (Serviço de Nuvem Voltado para Internet)
2. O serviço de nuvem passa o tráfego pelo ponto de extremidade aberto na porta 80 para o IIS01 (o servidor Web)
3. A sub-rede Frontend inicia o processamento da regra de entrada:
   1. A Regra NSG 1 (DNS) não se aplica; vá para a próxima regra
   2. A Regra NSG 2 (RDP) não se aplica; vá para a próxima regra
   3. A Regra NSG 3 (Internet para IIS01) não se aplica; o tráfego é permitido, pare o processamento da regra
4. O tráfego atinge o endereço IP interno do servidor Web IIS01 (10.0.1.5)
5. O IIS01 está escutando o tráfego da Web, recebe essa solicitação e começa a processar a solicitação
6. O IIS01 solicita informações do SQL Server na AppVM01
7. Como não há regras de saída na sub-rede Frontend, o tráfego é permitido
8. A sub-rede Backend inicia o processamento da regra de entrada:
   1. A Regra NSG 1 (DNS) não se aplica; vá para a próxima regra
   2. A Regra NSG 2 (RDP) não se aplica; vá para a próxima regra
   3. A Regra NSG 3 (Internet para Firewall) não se aplica; vá para a próxima regra
   4. A regra NSG 4 (IIS01 para AppVM01) não se aplica; o tráfego é permitido, pare o processamento da regra
9. AppVM01 recebe a consulta SQL e responde
10. Como não há nenhuma regra de saída na sub-rede de Back-end, a resposta é permitida
11. A sub-rede Frontend inicia o processamento da regra de entrada:
    1. Não há nenhuma regra NSG que se aplique ao tráfego de Entrada da sub-rede Backend para a sub-rede Frontend e, portanto, nenhuma das regras NSG se aplica
    2. A regra do sistema padrão que permite o tráfego entre sub-redes permitiria esse tráfego e, portanto, o tráfego é permitido.
12. O servidor IIS recebe a resposta SQL e conclui a resposta HTTP e envia ao solicitante
13. Como não há nenhuma regra de saída na sub-rede Frontend, a resposta é permitida e o Usuário da Internet recebe a página da Web solicitada.

#### <a name="allowed-rdp-to-backend"></a>(*Permitido*) RDP para back-end
1. O Administrador do servidor na Internet solicita a sessão RDP para AppVM01 em BackEnd001.CloudApp.Net:xxxxx, em que xxxxx é o número da porta atribuído aleatoriamente para RDP para AppVM01 (a porta atribuída pode ser encontrada no Portal do Azure ou através do PowerShell)
2. A sub-rede Backend inicia o processamento da regra de entrada:
   1. A Regra NSG 1 (DNS) não se aplica; vá para a próxima regra
   2. A regra NSG 2 (RDP) não se aplica; o tráfego é permitido, pare o processamento da regra
3. Sem regras de saída, as regras padrão serão aplicadas e o tráfego de retorno será permitido
4. A sessão RDP está habilitada
5. O AppVM01 solicita o nome de usuário e senha

#### <a name="allowed-web-server-dns-look-up-on-dns-server"></a>(*Permitido*) Pesquisa de DNS do servidor Web no servidor DNS
1. O Servidor Web, IIS01, necessita de um feed de dados em www.data.gov, mas precisa resolver o endereço.
2. A configuração de rede para a Rede Virtual lista DNS01 (10.0.2.4 na sub-rede Backend), já que o servidor DNS primário, IIS01, envia a solicitação DNS para DNS01
3. Não há regras de saída na sub-rede Frontend; o tráfego é permitido
4. A sub-rede Backend inicia o processamento da regra de entrada:
   * A regra NSG 1 (DNS) não se aplica; o tráfego é permitido, pare o processamento da regra
5. O servidor DNS recebe a solicitação
6. O servidor DNS não tem o endereço armazenado em cache e consulta um servidor DNS raiz na Internet
7. Nenhuma regra de saída na sub-rede Backend; o tráfego é permitido
8. O servidor DNS da Internet responde e, desde que esta sessão tenha sido iniciada internamente, a resposta será permitida
9. O servidor DNS armazena em cache a resposta e responde à solicitação inicial para IIS01
10. Nenhuma regra de saída na sub-rede Backend; o tráfego é permitido
11. A sub-rede Frontend inicia o processamento da regra de entrada:
    1. Não há nenhuma regra NSG que se aplique ao tráfego de Entrada da sub-rede Backend para a sub-rede Frontend e, portanto, nenhuma das regras NSG se aplica
    2. A regra do sistema padrão que permite o tráfego entre sub-redes permitiria esse tráfego e, portanto, o tráfego é permitido
12. O IIS01 recebe a resposta do DNS01

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(*Permitido*) Arquivo de acesso do servidor Web em AppVM01
1. IIS01 solicita um arquivo em AppVM01
2. Não há regras de saída na sub-rede Frontend; o tráfego é permitido
3. A sub-rede Backend inicia o processamento da regra de entrada:
   1. A Regra NSG 1 (DNS) não se aplica; vá para a próxima regra
   2. A Regra NSG 2 (RDP) não se aplica; vá para a próxima regra
   3. A Regra NSG 3 (Internet para IIS01) não se aplica; vá para a próxima regra
   4. A regra NSG 4 (IIS01 para AppVM01) não se aplica; o tráfego é permitido, pare o processamento da regra
4. AppVM01 recebe a solicitação e responde com o arquivo (supondo que o acesso é autorizado)
5. Como não há nenhuma regra de saída na sub-rede de Back-end, a resposta é permitida
6. A sub-rede Frontend inicia o processamento da regra de entrada:
   1. Não há nenhuma regra NSG que se aplique ao tráfego de Entrada da sub-rede Backend para a sub-rede Frontend e, portanto, nenhuma das regras NSG se aplica
   2. A regra do sistema padrão que permite o tráfego entre sub-redes permitiria esse tráfego e, portanto, o tráfego é permitido.
7. O servidor IIS recebe o arquivo

#### <a name="denied-web-to-backend-server"></a>(*Negado*) Web para o servidor de back-end
1. Um usuário da Internet tenta acessar um arquivo em AppVM01 por meio do serviço BackEnd001.CloudApp.Net
2. Como não há nenhum ponto de extremidade aberto para compartilhamento de arquivos, esse tráfego não passaria no Serviço de Nuvem e não alcançaria o servidor
3. Se os pontos de extremidade estiverem abertos por algum motivo, a regra NSG 5 (Internet para Rede Virtual) bloquearia esse tráfego

#### <a name="denied-web-dns-look-up-on-dns-server"></a>(*Negado*) Pesquisa de DNS na Web no servidor DNS
1. Um usuário da Internet tenta procurar um registro DNS interno em DNS01 por meio do serviço BackEnd001.CloudApp.Net
2. Como não há nenhum ponto de extremidade aberto para DNS, esse tráfego não passaria no Serviço de Nuvem e não alcançaria o servidor
3. Se os pontos de extremidade tiverem sido abertos por algum motivo, a regra NSG 5 (Internet para Rede Virtual) bloquearia esse tráfego (observação: essa Regra 1 (DNS) não se aplicariam por dois motivos, primeiro o endereço de origem será a Internet, essa regra só se aplicará à VNet local como a origem e, como esta também é uma regra Permitir, nunca negaria o tráfego)

#### <a name="denied-web-to-sql-access-through-firewall"></a>(*Negado*) Acesso Web ao SQL por meio do firewall
1. Um usuário da Internet solicita dados SQL de FrontEnd001.CloudApp.Net (Serviço de Nuvem Voltado para a Internet)
2. Como não há nenhum ponto de extremidade aberto para SQL, esse tráfego não passaria no Serviço de Nuvem e não alcançaria o firewall
3. Se os pontos de extremidade estiverem abertos por algum motivo, a sub-rede Frontend inicia o processamento de regras de entrada:
   1. A Regra NSG 1 (DNS) não se aplica; vá para a próxima regra
   2. A Regra NSG 2 (RDP) não se aplica; vá para a próxima regra
   3. A Regra NSG 3 (Internet para IIS01) não se aplica; o tráfego é permitido, pare o processamento da regra
4. O tráfego atinge o endereço IP interno do IIS01 (10.0.1.5)
5. O IIS01 não está escutando na porta 1433; portanto, não há resposta para a solicitação

## <a name="conclusion"></a>Conclusão
Esse exemplo representa uma maneira relativamente simples e direta de isolar a sub-rede de back-end do tráfego de entrada.

Mais exemplos e uma visão geral dos limites de segurança de rede podem ser encontrados [aqui][HOME].

## <a name="references"></a>Referências
### <a name="main-script-and-network-config"></a>Script principal e configuração de rede
Salve o Script Completo em um arquivo de script do PowerShell. Salve a Configuração de Rede em um arquivo denominado “NetworkConf1.xml”.
Modifique as variáveis definidas pelo usuário como necessárias e execute o script.

#### <a name="full-script"></a>Script completo
Esse script se baseará nas variáveis definidas pelo usuário;

1. Conectar-se a uma assinatura do Azure
2. Criar uma conta de armazenamento
3. Criar uma nova VNet e duas sub-redes, conforme definido no arquivo de Configuração de Rede
4. Crie quatro VMs do Windows Server
5. Configure o NSG, incluindo:
   * Criando um NSG
   * Preenchendo-o com regras
   * Associando o NSG às sub-redes apropriadas

Este script do PowerShell deve ser executado localmente em um computador ou servidor conectado à Internet.

> [!IMPORTANT]
> Quando esse script for executado, poderá haver avisos ou outras mensagens informativas que aparecerão no PowerShell. Somente as mensagens de erro em vermelho são motivo de preocupação.
> 
>

```PowerShell
<# 
 .SYNOPSIS
  Example of Network Security Groups in an isolated network (Azure only, no hybrid connections)

 .DESCRIPTION
  This script will build out a sample DMZ setup containing:
   - A default storage account for VM disks
   - Two new cloud services
   - Two Subnets (FrontEnd and BackEnd subnets)
   - One server on the FrontEnd Subnet
   - Three Servers on the BackEnd Subnet
   - Network Security Groups to allow/deny traffic patterns as declared

  Before running script, ensure the network configuration file is created in
  the directory referenced by $NetworkConfigFile variable (or update the
  variable to reflect the path and file name of the config file being used).

 .Notes
  Security requirements are different for each use case and can be addressed in a
  myriad of ways. Please be sure that any sensitive data or applications are behind
  the appropriate layer(s) of protection. This script serves as an example of some
  of the techniques that can be used, but should not be used for all scenarios. You
  are responsible to assess your security needs and the appropriate protections
  needed, and then effectively implement those protections.

  FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
   IIS01      - 10.0.1.5

  BackEnd Service (BackEnd subnet 10.0.2.0/24)
   DNS01      - 10.0.2.4
   AppVM01    - 10.0.2.5
   AppVM02    - 10.0.2.6

#>

# Fixed Variables
    $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
    $VMName = @()
    $ServiceName = @()
    $VMFamily = @()
    $img = @()
    $size = @()
    $SubnetName = @()
    $VMIP = @()

# User-Defined Global Variables
  # These should be changes to reflect your subscription and services
  # Invalid options will fail in the validation section

  # Subscription Access Details
    $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

  # VM Account, Location, and Storage Details
    $LocalAdmin = "theAdmin"
    $DeploymentLocation = "Central US"
    $StorageAccountName = "vmstore02"

  # Service Details
    $FrontEndService = "FrontEnd001"
    $BackEndService = "BackEnd001"

  # Network Details
    $VNetName = "CorpNetwork"
    $FESubnet = "FrontEnd"
    $FEPrefix = "10.0.1.0/24"
    $BESubnet = "BackEnd"
    $BEPrefix = "10.0.2.0/24"
    $NetworkConfigFile = "C:\Scripts\NetworkConf1.xml"

  # VM Base Disk Image Details
    $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

  # NSG Details
    $NSGName = "MyVNetSG"

# User-Defined VM Specific Configuration
    # Note: To ensure proper NSG Rule creation later in this script:
    #       - The Web Server must be VM 0
    #       - The AppVM1 Server must be VM 1
    #       - The DNS server must be VM 3
    #
    #       Otherwise the NSG rules in the last section of this
    #       script will need to be changed to match the modified
    #       VM array numbers ($i) so the NSG Rule IP addresses
    #       are aligned to the associated VM IP addresses.

    # VM 0 - The Web Server
      $VMName += "IIS01"
      $ServiceName += $FrontEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $FESubnet
      $VMIP += "10.0.1.5"

    # VM 1 - The First Application Server
      $VMName += "AppVM01"
      $ServiceName += $BackEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $BESubnet
      $VMIP += "10.0.2.5"

    # VM 2 - The Second Application Server
      $VMName += "AppVM02"
      $ServiceName += $BackEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $BESubnet
      $VMIP += "10.0.2.6"

    # VM 3 - The DNS Server
      $VMName += "DNS01"
      $ServiceName += $BackEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $BESubnet
      $VMIP += "10.0.2.4"

# ----------------------------- #
# No User-Defined Variables or  #
# Configuration past this point #
# ----------------------------- #    

  # Get your Azure accounts
    Add-AzureAccount
    Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
    Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

  # Create Storage Account
    If (Test-AzureName -Storage -Name $StorageAccountName) { 
        Write-Host "Fatal Error: This storage account name is already in use, please pick a different name." -ForegroundColor Red
        Return}
    Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
          New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}

  # Update Subscription Pointer to New Storage Account
    Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
    Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop

# Validation
$FatalError = $false

If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
     Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
     $FatalError = $true}

If (Test-AzureName -Service -Name $FrontEndService) { 
    Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
    $FatalError = $true}
Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}

If (Test-AzureName -Service -Name $BackEndService) { 
    Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
    $FatalError = $true}
Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}

If (-Not (Test-Path $NetworkConfigFile)) { 
    Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
    $FatalError = $true}
Else { Write-Host "The network configuration file was found" -ForegroundColor Green
        If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
            Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation variable is correct and the network config file matches.' -ForegroundColor Yellow
            $FatalError = $true}
        Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

If ($FatalError) {
    Write-Host "A fatal error has occurred, please see the above messages for more information." -ForegroundColor Red
    Return}
Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}

# Create VNET
    Write-Host "Creating VNET" -ForegroundColor Cyan 
    Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop

# Create Services
    Write-Host "Creating Services" -ForegroundColor Cyan
    New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
    New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop

# Build VMs
    $i=0
    $VMName | Foreach {
        Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
        New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
            Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
            Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
            Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
            Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
            Remove-AzureEndpoint -Name "PowerShell" | `
            New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
            # Note: A Remote Desktop endpoint is automatically created when each VM is created.
        $i++
    }
    # Add HTTP Endpoint for IIS01
    Get-AzureVM -ServiceName $ServiceName[0] -Name $VMName[0] | Add-AzureEndpoint -Name HTTP -Protocol tcp -LocalPort 80 -PublicPort 80 | Update-AzureVM

# Configure NSG
    Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

  # Build the NSG
    Write-Host "Building the NSG" -ForegroundColor Cyan
    New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

  # Add NSG Rules
    Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
        -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[3] -DestinationPortRange '53' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
        -SourceAddressPrefix Internet -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[0]) to $($VMName[1])" -Type Inbound -Priority 130 -Action Allow `
        -SourceAddressPrefix $VMIP[0] -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[1] -DestinationPortRange '*' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 140 -Action Deny `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" -Type Inbound -Priority 150 -Action Deny `
        -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
        -DestinationAddressPrefix $BEPrefix -DestinationPortRange '*' `
        -Protocol *

    # Assign the NSG to the Subnets
        Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

# Optional Post-script Manual Configuration
  # Install Test Web App (Run Post-Build Script on the IIS Server)
  # Install Backend resource (Run Post-Build Script on the AppVM01)
  Write-Host
  Write-Host "Build Complete!" -ForegroundColor Green
  Write-Host
  Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
  Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
  Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
  Write-Host
```

#### <a name="network-config-file"></a>Arquivo de configuração de rede
Salve esse arquivo xml com o local atualizado e adicione o link para esse arquivo à variável $NetworkConfigFile no script anterior.

```XML
<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
  <VirtualNetworkConfiguration>
    <Dns>
      <DnsServers>
        <DnsServer name="DNS01" IPAddress="10.0.2.4" />
        <DnsServer name="Level3" IPAddress="209.244.0.3" />
      </DnsServers>
    </Dns>
    <VirtualNetworkSites>
      <VirtualNetworkSite name="CorpNetwork" Location="Central US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="FrontEnd">
            <AddressPrefix>10.0.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="BackEnd">
            <AddressPrefix>10.0.2.0/24</AddressPrefix>
          </Subnet>
        </Subnets>
        <DnsServersRef>
          <DnsServerRef name="DNS01" />
          <DnsServerRef name="Level3" />
        </DnsServersRef>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
  </VirtualNetworkConfiguration>
</NetworkConfiguration>
```

#### <a name="sample-application-scripts"></a>Scripts de aplicativo de exemplo
Se você desejar instalar um aplicativo de exemplo para esse e outros Exemplos de DMZ, um deles foi fornecido no seguinte link: [Script de aplicativo de exemplo][SampleApp]

## <a name="next-steps"></a>Próximas etapas
* Atualizar e salvar o arquivo XML
* Execute o script do PowerShell para compilar o ambiente
* Instalar o aplicativo de exemplo
* Testar diferentes fluxos de tráfego por meio dessa DMZ

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-asm/example1design.png "DMZ de entrada com NSG"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md


---
title: Exemplo de DMZ - Criar uma DMZ para proteger aplicativos com um Firewall e NSGs | Microsoft Docs
description: "Criar uma DMZ com um Firewall e Grupos de Segurança de Rede (NSG)"
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: 
ms.assetid: c78491c7-54ac-4469-851c-b35bfed0f528
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: jonor;sivae
ms.openlocfilehash: cc0e8a3fa749eb2e6f65ef92c2d3cb404cfc8bc0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="example-2--build-a-dmz-to-protect-applications-with-a-firewall-and-nsgs"></a>Exemplo 2: criar uma DMZ para proteger aplicativos com firewall e NSGs
[Voltar à página Práticas recomendadas de limite de segurança][HOME]

Este exemplo criará uma DMZ com um firewall, quatro servidores Windows e Grupos de Segurança de Rede. Ele também orientará você em cada um dos comandos relevantes para fornecer um entendimento mais profundo de cada etapa. Também há uma seção Cenário de Tráfego para fornecer um passo a passo detalhado sobre como o tráfego passa pelas camadas de defesa da rede de perímetro. Por fim, na seção de referências, há o código e as instruções completas para criar este ambiente para testar e experimentar diversos cenários. 

![Rede de perímetro de entrada com NVA e NSG][1]

## <a name="environment-description"></a>Descrição do ambiente
Neste exemplo, há uma assinatura que contém o seguinte:

* Dois serviços de nuvem: "FrontEnd001" e "BackEnd001"
* Uma rede virtual, “CorpNetwork”, com duas sub-redes: “FrontEnd” e “BackEnd”
* Um único grupo de segurança de rede que é aplicado a ambas as sub-redes
* Um dispositivo virtual de rede, neste exemplo um Firewall NextGen Barracuda, conectado à sub-rede Frontend
* Um Windows Server que representa um servidor Web de aplicativos ("IIS01")
* Dois servidores Windows que representam servidores de back-end de aplicativos ("AppVM01", "AppVM02")
* Um servidor Windows que representa um servidor DNS ("DNS01")

> [!NOTE]
> Embora este exemplo use um Firewall NextGen Barracuda, muitos dos diferentes Dispositivos Virtuais de Rede poderiam ser usados neste exemplo.
> 
> 

Na seção de referências abaixo, há um script do PowerShell que criará a maior parte do ambiente descrito acima. A criação das VMs e das Redes Virtuais, embora seja feita por script de exemplo, não será descrita em detalhes neste documento.

Para compilar o ambiente:

1. Salve o arquivo xml de configuração de rede incluído na seção de referências (atualizado com nomes, localização e endereços IP que correspondam ao cenário determinado)
2. Atualize as variáveis do usuário no script para fazer a correspondência do ambiente em que o script deve ser executado (assinaturas, nomes de serviço, etc.)
3. Execute o script no PowerShell

**Observação**: a região representada no script do PowerShell deve corresponder à região representada no arquivo xml de configuração de rede.

Assim que o script for executado com êxito, as seguintes etapas pós-script poderão ser utilizadas:

1. Configure as regras de firewall; isso será abordado na seção abaixo: Regras de Firewall.
2. Opcionalmente, na seção de referências, há dois scripts para configurar o servidor Web e um servidor de aplicativos com um aplicativo Web simples para testar a configuração desta DMZ.

A próxima seção explica a maioria das instruções de scripts em relação a Grupos de Segurança de Rede.

## <a name="network-security-groups-nsg"></a>Grupos de segurança de rede (NSG)
Neste exemplo, um grupo NSG é criado e então carregado com seis regras. 

> [!TIP]
> Em geral, você deve criar suas regras específicas "Permitir" primeiro e então as regras “Negar” mais genéricas por último. A prioridade atribuída ditará quais regras serão avaliadas primeiro. Quando o tráfego se aplicar a uma regra específica, nenhuma regra adicional será avaliada. As regras NSG podem se aplicar na direção de entrada ou de saída (na perspectiva da sub-rede).
> 
> 

Declarativamente, as regras a seguir estão sendo criadas para tráfego de entrada:

1. O tráfego interno de DNS (porta 53) é permitido
2. O tráfego de RDP (porta 3389) da Internet para qualquer VM é permitido
3. O tráfego HTTP (porta 80) da Internet para o NVA (firewall) é permitido
4. Todo tráfego (todas as portas) de IIS01 para AppVM1 é permitido
5. Todo o tráfego (todas as portas) da Internet para a Rede Virtual inteira (todas as sub-redes) é Negado
6. Todo tráfego (todas as portas) da sub-rede Frontend para a sub-rede Backend é negado

Com essas regras associadas a cada sub-rede, se uma solicitação HTTP tiver entrado da Internet para o servidor Web, ambas as regras 3 (permitir) e 5 (negar) se aplicariam, mas já que a regra 3 tem uma prioridade maior, somente ela se aplicaria e a regra 5 não seria utilizada. Assim, a solicitação HTTP seria permitida ao firewall. Se esse mesmo tráfego estivesse tentando acessar o servidor DNS01, a regra 5 (Negar) seria a primeira a ser aplicada e o tráfego não teria permissão para passar para o servidor. A regra 6 (Negar) impede que a sub-rede Frontend converse com a sub-rede Backend (exceto pelo tráfego permitido nas regras 1 e 4); isso protege a rede Backend caso um invasor comprometa o aplicativo Web na rede Frontend, pois ele teria acesso limitado à rede Backend “protegida” (somente para recursos expostos no servidor AppVM01).

Há uma regra de saída padrão que permite o tráfego de saída para a Internet. Neste exemplo, permitiremos o tráfego de saída e não modificaremos quaisquer regras de saída. Para bloquear o tráfego em ambas os sentidos, o Roteamento Definido pelo Usuário será necessário; isso será explorado em um exemplo diferente, que pode ser encontrado no [documento de limite de segurança principal][HOME].

As regras NSG discutidas acima são muito semelhantes às regras NSG no [Exemplo 1 ‑ Criar uma DMZ simples com NSGs][Example1]. Examine a Descrição NSG nesse documento para obter uma visão detalhada de cada regra NSG e de seus atributos.

## <a name="firewall-rules"></a>Regras de firewall
Um cliente de gerenciamento precisará ser instalado em um PC para gerenciar o firewall e para criar as configurações necessárias. Consulte a documentação do fornecedor do seu firewall (ou outro NVA) sobre como gerenciar o dispositivo. O restante desta seção descreverá a configuração do próprio firewall, por meio do cliente de gerenciamento de fornecedores (isto é, não o portal do Azure ou o PowerShell).

As instruções para download do cliente e para a conexão ao Barracuda usada neste exemplo poderão ser encontradas aqui: [Administrador do NG Barracuda](https://techlib.barracuda.com/NG61/NGAdmin)

No firewall, será necessário criar regras de encaminhamento. Como este exemplo só roteia o tráfego de Internet de entrada para o firewall e, em seguida, para o servidor Web, somente uma regra de NAT de encaminhamento será necessária. No Firewall NextGen Barracuda usado neste exemplo, a regra seria uma regra de NAT de Destino ("Dst NAT") para passar o tráfego.

Para criar a regra a seguir (ou verificar as regras padrão existentes), começando do painel de cliente do Administrador do NG Barracuda, navegue até a guia de configuração, na seção Configuração Operacional, clique em Conjunto de Regras. Uma grade chamada "Regras Principais" mostrará as regras ativas e desativadas existentes no firewall. No canto superior direito desta grade, há um pequeno botão "+" verde, clique nele para criar uma nova regra (observação: seu firewall poderá estar “bloqueado” para alterações, caso você veja um botão marcado como “Bloquear” e não será possível criar ou editar regras; clique nesse botão para “desbloquear” o conjunto de regras e permitir a edição). Se desejar editar uma regra existente, selecione essa regra, clique com botão direito do mouse e selecione Editar Regra.

Crie uma nova regra e forneça um nome, como "WebTraffic". 

O ícone da regra NAT de Destino tem esta aparência: ![Ícone de NAT de Destino][2]

A própria regra seria semelhante a esta:

![Regra de Firewall][3]

Aqui, qualquer endereço de entrada que acessar o Firewall ao tentar acessar HTTP (porta 80 ou 443 para HTTPS) será enviado para a interface “IP Local DHCP1” e redirecionado para o Servidor Web com o Endereço IP 10.0.1.5. Uma vez que o tráfego está entrando na porta 80 e indo para o servidor Web na porta 80, nenhuma alteração de porta foi necessária. No entanto, a Lista de Destinos poderia ter sido 10.0.1.5:8080 se nosso Servidor Web escutasse na porta 8080 e, portanto, convertendo a porta 80 de entrada no firewall em uma porta de entrada 8080 no servidor Web.

Um Método de Conexão também deve ser sinalizado para a Regra de Destino da Internet; “SNAT Dinâmico” é mais apropriado. 

Embora somente uma regra tenha sido criada, é importante que sua prioridade seja definida corretamente. Se na grade de todas as regras no firewall essa nova regra estiver na parte inferior (abaixo da regra "BLOCKALL"), ela nunca entrará em ação. Verifique se a regra recém-criada para o tráfego da Web está acima da regra BLOCKALL.

Depois que a regra for criada, deverá ser enviada por push para o firewall e ativada; se isso não for feito, a alteração de regra não entrará em vigor. O processo de envio por push e de ativação será descrito na próxima seção.

## <a name="rule-activation"></a>Ativação da regra
Com o conjunto de regras modificado para adicionar essa regra, o conjunto de regras deverá ser carregado no firewall e ativado.

![Ativação de regra de firewall][4]

No canto superior direito do cliente de gerenciamento, há um cluster de botões. Clique no botão "Enviar Alterações" para enviar as regras modificadas para o firewall, então clique no botão "Ativar".

Com a ativação do conjunto de regras de firewall, a criação do ambiente de exemplo foi concluída. Opcionalmente, os scripts de criação de postagem na seção Referências podem ser executados para adicionar um aplicativo a este ambiente para testar os cenários de tráfego abaixo.

> [!IMPORTANT]
> É fundamental perceber que você não acessará o servidor Web diretamente. Quando um navegador solicita uma página HTTP de FrontEnd001.CloudApp.Net, o ponto de extremidade HTTP (porta 80) passa esse tráfego para o firewall, e não para o servidor Web. O firewall, de acordo com a regra criada acima, envia essa solicitação por NAT ao servidor Web.
> 
> 

## <a name="traffic-scenarios"></a>Cenários de tráfego
#### <a name="allowed-web-to-web-server-through-firewall"></a>(Permitido) Servidor Web para Web por meio do Firewall
1. Usuário da Internet solicita uma página HTTP de FrontEnd001.CloudApp.Net (Internet Voltada para o Serviço de Nuvem)
2. O serviço de novem passa o tráfego para o ponto de extremidade aberto na porta 80 para a interface local do firewall 10.0.1.4:80
3. A sub-rede Frontend inicia o processamento da regra de entrada:
   1. A Regra NSG 1 (DNS) não se aplica; vá para a próxima regra
   2. A Regra NSG 2 (RDP) não se aplica; vá para a próxima regra
   3. A regra NSG 3 (Internet para Firewall) não se aplica; o tráfego é permitido, pare o processamento da regra
4. O tráfego chega ao endereço IP interno do firewall (10.0.1.4)
5. A regra de encaminhamento do firewall vê que esse é um tráfego da porta 80, o redireciona para o servidor Web IIS01
6. O IIS01 está escutando o tráfego da Web, recebe essa solicitação e começa a processar a solicitação
7. O IIS01 solicita informações do SQL Server na AppVM01
8. Não há regras de saída na sub-rede Frontend; o tráfego é permitido
9. A sub-rede Backend inicia o processamento da regra de entrada:
   1. A Regra NSG 1 (DNS) não se aplica; vá para a próxima regra
   2. A Regra NSG 2 (RDP) não se aplica; vá para a próxima regra
   3. A Regra NSG 3 (Internet para Firewall) não se aplica; vá para a próxima regra
   4. A regra NSG 4 (IIS01 para AppVM01) não se aplica; o tráfego é permitido, pare o processamento da regra
10. AppVM01 recebe a consulta SQL e responde
11. Como não há nenhuma regra de saída na sub-rede Backend, a resposta é permitida
12. A sub-rede Frontend inicia o processamento da regra de entrada:
    1. Não há nenhuma regra NSG que se aplique ao tráfego de Entrada da sub-rede Backend para a sub-rede Frontend e, portanto, nenhuma das regras NSG se aplica
    2. A regra do sistema padrão que permite o tráfego entre sub-redes permitiria esse tráfego e, portanto, o tráfego é permitido.
13. O servidor IIS recebe a resposta SQL e conclui a resposta HTTP e envia ao solicitante
14. Já que essa é uma sessão NAT do firewall, o destino de resposta (inicialmente) é para o Firewall
15. O firewall recebe a resposta do Servidor Web e a encaminha de volta ao Usuário da Internet
16. Como não há nenhuma regra de saída na sub-rede Frontend, a resposta é permitida e o Usuário da Internet recebe a página da Web solicitada.

#### <a name="allowed-rdp-to-backend"></a>(Permitido) RDP para back-end
1. O Administrador do servidor na Internet solicita a sessão RDP para AppVM01 em BackEnd001.CloudApp.Net:xxxxx, onde xxxxx é o número de porta atribuído aleatoriamente para RDP para AppVM01 (a porta atribuída pode ser encontrada no Portal do Azure ou através do PowerShell)
2. Como o Firewall só está ouvindo o endereço do FrontEnd001.CloudApp.Net, ele não está envolvido nesse fluxo de tráfego
3. A sub-rede Backend inicia o processamento da regra de entrada:
   1. A Regra NSG 1 (DNS) não se aplica; vá para a próxima regra
   2. A regra NSG 2 (RDP) não se aplica; o tráfego é permitido, pare o processamento da regra
4. Sem regras de saída, as regras padrão serão aplicadas e o tráfego de retorno será permitido
5. A sessão RDP está habilitada
6. O AppVM01 solicita a senha do nome de usuário

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Permitido) Pesquisa de DNS do Servidor Web no servidor DNS
1. O Servidor Web, IIS01, necessita de um feed de dados em www.data.gov, mas precisa resolver o endereço.
2. A configuração de rede para a Rede Virtual lista DNS01 (10.0.2.4 na sub-rede Backend), já que o servidor DNS primário, IIS01, envia a solicitação DNS para DNS01
3. Não há regras de saída na sub-rede Frontend; o tráfego é permitido
4. A sub-rede Backend inicia o processamento da regra de entrada:
   1. A regra NSG 1 (DNS) não se aplica; o tráfego é permitido, pare o processamento da regra
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

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(Permitido) O arquivo de acesso do Servidor Web em AppVM01
1. IIS01 solicita um arquivo em AppVM01
2. Não há regras de saída na sub-rede Frontend; o tráfego é permitido
3. A sub-rede Backend inicia o processamento da regra de entrada:
   1. A Regra NSG 1 (DNS) não se aplica; vá para a próxima regra
   2. A Regra NSG 2 (RDP) não se aplica; vá para a próxima regra
   3. A Regra NSG 3 (Internet para Firewall) não se aplica; vá para a próxima regra
   4. A regra NSG 4 (IIS01 para AppVM01) não se aplica; o tráfego é permitido, pare o processamento da regra
4. AppVM01 recebe a solicitação e responde com o arquivo (supondo que o acesso é autorizado)
5. Como não há nenhuma regra de saída na sub-rede Backend, a resposta é permitida
6. A sub-rede Frontend inicia o processamento da regra de entrada:
   1. Não há nenhuma regra NSG que se aplique ao tráfego de Entrada da sub-rede Backend para a sub-rede Frontend e, portanto, nenhuma das regras NSG se aplica
   2. A regra do sistema padrão que permite o tráfego entre sub-redes permitiria esse tráfego e, portanto, o tráfego é permitido.
7. O servidor IIS recebe o arquivo

#### <a name="denied-web-direct-to-web-server"></a>(Negado) Web direta ao Servidor Web
Uma vez que o Servidor Web, IIS01, e o Firewall estão no mesmo Serviço de Nuvem, eles compartilham o mesmo endereço IP público. Dessa forma, todo o tráfego HTTP seria direcionado para o firewall. Embora a solicitação seja servida com êxito, não poderá ir diretamente para o Servidor Web, mas passará, por design, pelo Firewall primeiro. Consulte o primeiro Cenário desta seção para obter o fluxo de tráfego.

#### <a name="denied-web-to-backend-server"></a>(Negado) Web para o servidor Backend
1. O usuário da Internet tenta acessar um arquivo em AppVM01 por meio do serviço BackEnd001.CloudApp.Net
2. Como não há nenhum ponto de extremidade aberto para compartilhamento de arquivos, isso não passaria no Serviço de Nuvem e não alcançaria o servidor
3. Se os pontos de extremidade estiverem abertos por algum motivo, a regra NSG 5 (Internet para Rede Virtual) bloquearia esse tráfego

#### <a name="denied-web-dns-lookup-on-dns-server"></a>(Negado) Pesquisa de DNS na Web no servidor DNS
1. O usuário da Internet tenta procurar um registro DNS interno em DNS01 por meio do serviço BackEnd001.CloudApp.Net
2. Como não há nenhum ponto de extremidade aberto para DNS, isso não passaria no Serviço de Nuvem e não alcançaria o servidor
3. Se os pontos de extremidade tiverem sido abertos por algum motivo, a regra NSG 5 (Internet para Rede Virtual) bloquearia esse tráfego (observação: essa Regra 1 (DNS) não se aplicariam por dois motivos, primeiro o endereço de origem é a Internet, essa regra só se aplica à Rede Virtual local como a origem, e como também é uma regra Permitir, nunca negaria o tráfego)

#### <a name="denied-web-to-sql-access-through-firewall"></a>(Negado) Acesso Web para SQL por meio do Firewall
1. O usuário da Internet solicita dados SQL de FrontEnd001.CloudApp.Net (Serviço de Nuvem Voltado para a Internet)
2. Como não há nenhum ponto de extremidade aberto para SQL, isso não passaria no Serviço de Nuvem e não alcançaria o firewall
3. Se os pontos de extremidade estiverem abertos por algum motivo, a sub-rede Frontend inicia o processamento de regras de entrada:
   1. A Regra NSG 1 (DNS) não se aplica; vá para a próxima regra
   2. A Regra NSG 2 (RDP) não se aplica; vá para a próxima regra
   3. A Regra NSG 2 (Internet para Firewall) não se aplica; o tráfego é permitido, pare o processamento da regra
4. O tráfego chega ao endereço IP interno do firewall (10.0.1.4)
5. O Firewall não tem nenhuma regra de encaminhamento para SQL e descarta o tráfego

## <a name="conclusion"></a>Conclusão
Essa é uma maneira relativamente simples de proteger seu aplicativo com um firewall e isolar a sub-rede de back-end do tráfego de entrada.

Mais exemplos e uma visão geral dos limites de segurança de rede podem ser encontrados [aqui][HOME].

## <a name="references"></a>Referências
### <a name="main-script-and-network-config"></a>Script principal e configuração de rede
Salve o Script Completo em um arquivo de script do PowerShell. Salve a Configuração de Rede em um arquivo chamado "NetworkConf2.xml".
Modifique as variáveis definidas pelo usuário como necessário. Execute o script e então siga as instruções de configuração de regra de Firewall acima.

#### <a name="full-script"></a>Script Completo
Esse script se baseará nas variáveis definidas pelo usuário:

1. Conectar-se a uma assinatura do Azure
2. Criar uma nova conta de armazenamento
3. Criar uma nova Rede Virtual e duas sub-redes, como definido no arquivo de Configuração de Rede
4. Criar 4 VMs do windows server
5. Configure o NSG, incluindo:
   * Criando um NSG
   * Preenchendo-o com regras
   * Associando o NSG às sub-redes apropriadas

Este script do PowerShell deve ser executado localmente em um computador ou servidor conectado à Internet.

> [!IMPORTANT]
> Quando esse script for executado, poderá haver avisos ou outras mensagens informativas que aparecerão no PowerShell. Somente as mensagens de erro em vermelho são motivo de preocupação.
> 
> 

    <# 
     .SYNOPSIS
      Example of DMZ and Network Security Groups in an isolated network (Azure only, no hybrid connections)

     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Two new cloud services
       - Two Subnets (FrontEnd and BackEnd subnets)
       - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
       - One server on the FrontEnd Subnet (plus the NVA on the FrontEnd subnet)
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
       myFirewall - 10.0.1.4
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

    # User Defined Global Variables
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
        $NetworkConfigFile = "C:\Scripts\NetworkConf2.xml"

      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

      # NSG Details
        $NSGName = "MyVNetSG"

    # User Defined VM Specific Config
        # Note: To ensure proper NSG Rule creation later in this script:
        #       - The Web Server must be VM 1
        #       - The AppVM1 Server must be VM 2
        #       - The DNS server must be VM 4
        #
        #       Otherwise the NSG rules in the last section of this
        #       script will need to be changed to match the modified
        #       VM array numbers ($i) so the NSG Rule IP addresses
        #       are aligned to the associated VM IP addresses.

        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $FrontEndService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"

        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.5"

        # VM 2 - The First Appliaction Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"

        # VM 3 - The Second Appliaction Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"

        # VM 4 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"

    # ----------------------------- #
    # No User Defined Varibles or   #
    # Configuration past this point #
    # ----------------------------- #

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

      # Create Storage Account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
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
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation varible is correct and the netowrk config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

    If ($FatalError) {
        Write-Host "A fatal error has occured, please see the above messages for more information." -ForegroundColor Red
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
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the EndPoints we'll need once we're up and running
                # Note: Web traffic goes through the firewall, so we'll need to set up a HTTP endpoint.
                #       Also, the firewall will be redirecting web traffic to a new IP and Port in a
                #       forwarding rule, so the HTTP endpoint here will have the same public and local
                #       port and the firewall will do the NATing and redirection as declared in the
                #       firewall rule.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                    # Note: A Remote Desktop endpoint is automatically created when each VM is created.
                }
            $i++
        }

    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

      # Add NSG Rules
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
            -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[4] -DestinationPortRange '53' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
            -SourceAddressPrefix Internet -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[1]) to $($VMName[2])" -Type Inbound -Priority 130 -Action Allow `
            -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[2] -DestinationPortRange '*' `
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
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install Backend resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host


#### <a name="network-config-file"></a>Arquivo de configuração de rede
Salve esse arquivo xml com localização atualizada e adicione o link a esse arquivo à variável $NetworkConfigFile no script acima.

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

#### <a name="sample-application-scripts"></a>Scripts de aplicativo de exemplo
Se você desejar instalar um aplicativo de exemplo para esse e outros Exemplos de DMZ, um deles foi fornecido no seguinte link: [Script de aplicativo de exemplo][SampleApp]

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-asm/example2design.png "DMZ de entrada com NSG"
[2]: ./media/virtual-networks-dmz-nsg-fw-asm/dstnaticon.png "Ícone de NAT de destino"
[3]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallrule.png "Regra de Firewall"
[4]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallruleactivate.png "Ativação de regra de firewall"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
[Example1]: ./virtual-networks-dmz-nsg-asm.md

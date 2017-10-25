---
title: Exemplo DMZ - Criar um DMZ para proteger redes com um Firewall, uma UDR e um NSG | Microsoft Docs
description: "Criar uma rede de perímetro com um Firewall, o Roteamento Definido pelo Usuário (UDR) e os Grupos de Segurança de Rede (NSG)"
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: 
ms.assetid: dc01ccfb-27b0-4887-8f0b-2792f770ffff
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: jonor;sivae
ms.openlocfilehash: fdb3c5cbd3acee90386352c6f180a71aa81f54fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="example-3--build-a-dmz-to-protect-networks-with-a-firewall-udr-and-nsg"></a>Exemplo 3 - Criar uma rede de perímetro para proteger as redes com um Firewall, um UDR e um NSG
[Voltar à página Práticas recomendadas de limite de segurança][HOME]

Este exemplo criará uma rede de perímetro com um firewall, quatro servidores Windows, Roteamento Definido pelo Usuário, Reencaminhamento IP e Grupos de Segurança de Rede. Ele também orientará você em cada um dos comandos relevantes para fornecer um entendimento mais profundo de cada etapa. Também há uma seção Cenário de Tráfego para fornecer um passo a passo detalhado sobre como o tráfego passa pelas camadas de defesa da rede de perímetro. Por fim, na seção de referências, há o código e as instruções completas para criar este ambiente para testar e experimentar diversos cenários. 

![DMZ bidirecional com NVA, NSG e UDR][1]

## <a name="environment-setup"></a>Configuração do ambiente
Neste exemplo, há uma assinatura que contém o seguinte:

* Três serviços de nuvem: “SecSvc001”, “FrontEnd001” e “BackEnd001”
* Uma rede virtual, “CorpNetwork”, com três sub-redes; “SecNet”, “FrontEnd” e “BackEnd”
* Um dispositivo virtual de rede, neste exemplo um firewall, conectado à sub-rede SecNet
* Um Windows Server que representa um servidor Web de aplicativos ("IIS01")
* Dois servidores Windows que representam servidores de back-end de aplicativos ("AppVM01", "AppVM02")
* Um servidor Windows que representa um servidor DNS ("DNS01")

Na seção de referências abaixo, há um script do PowerShell que criará a maior parte do ambiente descrito acima. A criação das VMs e das Redes Virtuais, embora seja feita por script de exemplo, não será descrita em detalhes neste documento.

Para compilar o ambiente:

1. Salve o arquivo xml de configuração de rede incluído na seção de referências (atualizado com nomes, localização e endereços IP que correspondam ao cenário determinado)
2. Atualize as variáveis do usuário no script para fazer a correspondência do ambiente em que o script deve ser executado (assinaturas, nomes de serviço, etc.)
3. Execute o script no PowerShell

**Observação**: a região representada no script do PowerShell deve corresponder à região representada no arquivo xml de configuração de rede.

Assim que o script for executado com êxito, as seguintes etapas pós-script poderão ser utilizadas:

1. Configure as regras de firewall; isso será abordado na seção: Descrição da regra de firewall.
2. Opcionalmente, na seção de referências, há dois scripts para configurar o servidor Web e um servidor de aplicativos com um aplicativo Web simples para testar a configuração desta rede de perímetro.

Assim que o script for executado com êxito, será necessário concluir as regras de firewall; isso será abordado na seção: Regras de firewall.

## <a name="user-defined-routing-udr"></a>Roteamento definido pelo usuário (UDR)
Por padrão, as rotas do sistema a seguir são definidas como:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

A VNETLocal sempre será o(s) prefixo(s) do endereço definido para aquela rede específica (isto é, ela será alterada de Rede Virtual para Rede Virtual, dependendo de como cada Rede Virtual específica seja definida). As rotas do sistema restantes são estáticas e padrão como acima.

Como prioridade, as rotas são processadas por meio do método Correspondência de Prefixo Mais Longo (LPM), assim a rota mais específica na tabela se aplica a um determinado endereço de destino.

Portanto, o tráfego (por exemplo, para o servidor DNS01, 10.0.2.4) para a rede local (10.0.0.0/16) será encaminhado por meio da Rede Virtual para seu destino devido à rota 10.0.0.0/16. Em outras palavras, para 10.0.2.4, a rota 10.0.0.0/16 é a rota mais específica, mesmo que 10.0.0.0/8 e 0.0.0.0/0 também possam ser aplicados, mas já que são menos específicas, não afetam esse tráfego. Assim, o tráfego para 10.0.2.4 teria um próximo salto da Rede Virtual local e simplesmente seria roteado para o destino.

Se o tráfego tiver sido projetado para 10.1.1.1, por exemplo, a rota 10.0.0.0/16 não se aplicará, mas 10.0.0.0/8 seria mais específico e esse tráfego seria descartado ("buraco negro"), já que o próximo salto é Null. 

Se o destino não tiver se aplicado a qualquer um dos prefixos Null ou dos prefixos da VNETLocal, então ele seguiria a rota menos específica, 0.0.0.0/0, e seria roteado para a Internet como o próximo saldo e, portanto, fora da borda de Internet do Azure.

Se houver dois prefixos idênticos na tabela de rotas, a ordem a seguir será a ordem de preferência baseada no atributo de “origem” das rotas:

1. "VirtualAppliance" = uma Rota Definida pelo Usuário adicionada manualmente à tabela
2. "VPNGateway" = uma rota dinâmica (BGP quando usada com redes híbridas) adicionadas por um protocolo de rede dinâmico, essas rotas podem ser alteradas ao longo do tempo conforme o protocolo dinâmico reflete automaticamente as alterações na rede emparelhada
3. "Padrão" = as Rotas do Sistema, a Rede Virtual local e as entradas estáticas, como mostrado na tabela de rotas acima.

> [!NOTE]
> Agora você pode usar o UDR (Roteamento Definido pelo Usuário) com ExpressRoute e Gateways de VPN para forçar o roteamento do tráfego de entrada e saída entre locais para um NVA (solução de virtualização de rede).
> 
> 

#### <a name="creating-the-local-routes"></a>Criando as rotas locais
Neste exemplo, duas tabelas de roteamento são necessárias, uma para cada sub-rede Frontend e Backend. Cada tabela é carregada com as rotas estáticas apropriadas para determinada sub-rede. Para fins deste exemplo, cada tabela tem três rotas:

1. O tráfego de sub-rede local sem Próximo Salto definido para permitir que o tráfego de sub-rede local ignore o firewall
2. Tráfego de Rede Virtual com um Próximo Salto definido como firewall, isso substitui a regra padrão que permite que o tráfego de Rede Virtual local seja roteado diretamente
3. Todo o tráfego restante (0/0) com um Próximo Salto definido como o firewall

Depois que as tabelas de roteamento forem criadas, serão associadas às sub-redes delas. Para a tabela de roteamento da sub-rede Frontend, uma vez criada e associada à sub-rede, deverá ter esta aparência:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active 
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active


Para este exemplo, os comandos a seguir são usados para criar a tabela de rotas, adicionar uma rota definida pelo usuário e associar a tabela de rotas a uma sub-rede (Observação: todos os itens abaixo que começarem com um sinal de cifrão (por exemplo, $BESubnet) são variáveis definidas pelo usuário do script na seção de referência deste documento):

1. Primeiro, a tabela de roteamento de base deverá ser criada. Este trecho mostra a criação da tabela para a sub-rede Backend. No script, uma tabela correspondente também será criada para a sub-rede Frontend.
   
     New-AzureRouteTable -Name $BERouteTableName `
   
         -Location $DeploymentLocation `
         -Label "Route table for $BESubnet subnet"
2. Assim que a tabela de rotas for criada, as rotas definidas pelo usuário específico poderão ser adicionadas. Neste trecho, todo o tráfego (0.0.0.0/0) será roteado por meio do dispositivo virtual (uma variável, $VMIP[0], é usada para passar o endereço IP atribuído quando o dispositivo virtual tiver sido criado anteriormente no script). No script, uma regra correspondente também será criada na tabela Frontend.
   
     Get-AzureRouteTable $BERouteTableName | `
   
         Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
         -NextHopType VirtualAppliance `
         -NextHopIpAddress $VMIP[0]
3. A entrada de rota acima substituirá a rota "0.0.0.0/0" padrão, mas a regra 10.0.0.0/16 padrão ainda existiria e permitiria o tráfego na Rede Virtual para rotear diretamente ao destino e não para o Dispositivo Virtual de Rede. Para corrigir esse comportamento, adicione a regra a seguir.
   
        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
4. Neste ponto, há uma opção a ser feita. Com as duas rotas acima, todo o tráfego será roteado para o firewall para avaliação, até mesmo o tráfego de uma única sub-rede. Isso pode ser desejável, mas para permitir que o tráfego de uma sub-rede seja roteado localmente sem o envolvimento do firewall, uma terceira regra muito específica poderá ser adicionada. Essa rota declara que qualquer destino de endereço para a sub-rede local só poderá ser roteado para lá diretamente (NextHopType = VNETLocal).
   
        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal
5. Por fim, com a tabela de roteamento criada e preenchida com rotas definidas pelo usuário, a tabela deverá estar associada a uma sub-rede. No script, a tabela de rotas de front-end também está associada à sub-rede Frontend. Este é o script de associação para a sub-rede Backend.
   
     Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
   
        -SubnetName $BESubnet `
        -RouteTableName $BERouteTableName

## <a name="ip-forwarding"></a>encaminhamento IP
Um recurso complementar para UDR é o Encaminhamento IP. Essa é uma configuração em um Dispositivo Virtual que permite receber o tráfego endereçado não especificamente para o dispositivo e, em seguida, encaminhar esse tráfego para seu destino final.

Por exemplo, se o tráfego de AppVM01 fizer uma solicitação para o servidor DNS01, o UDR rotearia isso para o firewall. Com o Encaminhamento de IP habilitado, o tráfego para o destino DNS01 (10.0.2.4) será aceito pelo dispositivo (10.0.0.4) e encaminhado para seu destino final (10.0.2.4). Sem o Encaminhamento de IP habilitado no Firewall, tráfego não seria aceito pelo dispositivo, embora a tabela de rota tenha o firewall como o próximo nó. 

> [!IMPORTANT]
> É essencial lembrar-se de habilitar o Encaminhamento IP em conjunto com o Roteamento Definido pelo Usuário.
> 
> 

A configuração do Encaminhamento IP é um único comando e pode ser feito no momento da criação da VM. Para o fluxo deste exemplo, o trecho de código será mostrado até o fim do script e agrupado com os comandos UDR:

1. Chame a instância da VM que seja o seu dispositivo virtual, neste caso, o firewall, e habilite o Encaminhamento IP (observação; todos os itens em vermelho que começam com um cifrão (por exemplo, $VMName[0] são uma variável definida pelo usuário no script na seção de referência deste documento. O zero entre colchetes, [0], representa a primeira VM na matriz de VMs; para que o script de exemplo funcione sem modificações, a primeira VM (VM 0) deverá ser o firewall):
   
     Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] | `
   
        Set-AzureIPForwarding -Enable

## <a name="network-security-groups-nsg"></a>Grupos de segurança de rede (NSG)
Neste exemplo, um grupo NSG é criado e então carregado com uma única regra. Esse grupo é então associado somente às sub-redes Frontend e Backend (e não a SecNet). Declarativamente, a seguinte regra está sendo criada:

1. Todo tráfego (todas as portas) da Internet a toda a VNet (todas as sub-redes) é negado

Embora NSGs sejam usados neste exemplo, seu principal objetivo será ser como uma segunda camada de defesa contra erros de configuração manual. Queremos bloquear todo tráfego de entrada da Internet para as sub-redes Frontend ou Backend; o tráfego só fluirá por meio da sub-rede SecNet até o firewall (e então, se apropriado, nas sub-redes Frontend ou Backend). Além disso, com as regras UDR em vigor, qualquer tráfego que tenha conseguido passar para as sub-redes Frontend ou Backend seria direcionado para o firewall (graças ao UDR). O firewall veria isso como um fluxo assimétrico e descartaria o tráfego de saída. Assim, há três camadas de segurança protegendo as sub-redes Frontend e Backend; 1) nenhum ponto de extremidade aberto nos serviços de nuvem FrontEnd001 e BackEnd001, 2) NSGs negando o tráfego da Internet, 3) o firewall descartando o tráfego assimétrico.

Um ponto interessante sobre o Grupo de Segurança de Rede neste exemplo é que ele contém apenas uma regra, mostrada abaixo, que é negar o tráfego da Internet para toda a rede virtual, o que incluiria a sub-rede Security. 

    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet `
        from the Internet" `
        -Type Inbound -Priority 100 -Action Deny `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK `
        -DestinationPortRange '*' `
        -Protocol *

No entanto, como o NSG está associado apenas às sub-redes Frontend e Backend, a regra não será processada em tráfego de entrada para a sub-rede Security. Como resultado, mesmo que a regra NSG diga não ao tráfego da Internet para qualquer endereço na Rede Virtual, como o NSG nunca foi associado à sub-rede Security, o tráfego fluirá para a sub-rede Security.

    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
        -SubnetName $FESubnet -VirtualNetworkName $VNetName

    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
        -SubnetName $BESubnet -VirtualNetworkName $VNetName

## <a name="firewall-rules"></a>Regras de firewall
No firewall, será necessário criar regras de encaminhamento. Uma vez que o firewall está bloqueando ou encaminhando todos os tráfegos de entrada, de saída e entre Redes Virtuais, serão necessárias várias regras de firewall. Além disso, todo o tráfego de entrada atingirá o endereço IP público do Serviço de Segurança (em portas diferentes), para ser processado pelo firewall. Uma prática recomendada é criar um diagrama de fluxos de lógica antes de configurar as sub-redes e as regras de firewall para evitar a reformulação posteriormente. A figura a seguir é uma exibição lógica das regras de firewall para este exemplo:

![Exibição lógica das regras de firewall][2]

> [!NOTE]
> Com base no Dispositivo Virtual de Rede usado, as portas de gerenciamento variam. Neste exemplo, um Firewall NextGen Barracuda é mencionado e usa as portas 22, 801 e 807. Consulte a documentação do fornecedor do dispositivo para localizar as portas exatas usadas para o gerenciamento do dispositivo usado.
> 
> 

### <a name="logical-rule-description"></a>Descrição da regra lógica
No diagrama lógico acima, a sub-rede de segurança não é mostrada, já que o firewall é o único recurso nessa sub-rede, e esse diagrama mostra as regras de firewall e como elas permitem ou negam logicamente fluxos de tráfego e não o caminho roteado real. Além disso, as portas externas selecionadas para o tráfego RDP são portas com um intervalo maior (8014 – 8026) e foram selecionadas para alinhar um pouco com os dois últimos octetos do endereço IP local para facilitar a leitura (por exemplo, o endereço do servidor local 10.0.1.4 está associado à porta externa 8014); no entanto, todas as portas não conflitantes acima disso poderiam ser usadas.

Neste exemplo, precisamos de sete tipos de regras, descritos abaixo:

* Regras externas (para o tráfego de entrada):
  1. Regra de Gerenciamento de Firewall: essa regra de Redirecionamento de Aplicativo permite que o tráfego passe para as portas de gerenciamento do dispositivo virtual de rede.
  2. Regras de RDP (para cada servidor Windows): essas quatro regras (uma para cada servidor) permitirão o gerenciamento dos servidores individuais via RDP. Isso também poderia ser empacotado em uma regra, dependendo dos recursos do Dispositivo Virtual de Rede usado.
  3. Regras de Tráfego do Aplicativo: há duas regras de tráfego de aplicativo, a primeira para o tráfego da Web de front-end e a segunda para o tráfego de back-end (por exemplo, o servidor Web para a camada de dados). A configuração dessas regras dependerá de arquitetura de rede (onde os servidores são colocados) e dos fluxos de tráfego (a direção na qual o tráfego flui e quais portas são usadas).
     * A primeira regra permitirá que o tráfego de aplicativo real chegue ao servidor de aplicativos. Embora as outras regras permitam a segurança, o gerenciamento etc, as Regras de Aplicativo são as que permitem que usuários ou serviços externos acessem os aplicativos. Neste exemplo, há um único servidor Web na porta 80 e, portanto, uma única regra de aplicativo de firewall redirecionará o tráfego de entrada para o IP externo, para o endereço IP interno dos servidores Web. A sessão de tráfego redirecionado seria enviada por meio de NAT para o servidor interno.
     * A segunda Regra de Tráfego de Aplicativo é a regra de back-end que permite que o Servidor Web converse com o servidor AppVM01 (mas não com AppVM02) por meio de qualquer porta.
* Regras internas (para o tráfego interno da Rede Virtual)
  1. Regra de Saída para Internet: essa regra permitirá que o tráfego de qualquer rede passe para as redes selecionadas. Normalmente, essa regra é uma regra padrão que já existe no firewall, mas em um estado desabilitado. Essa regra deve ser habilitada para esse exemplo.
  2. Regra DNS: essa regra permite que somente o tráfego DNS (porta 53) passe para o servidor DNS. Para este ambiente, a maioria do tráfego do Frontend para o Backend será bloqueado, e essa regra permite especificamente o DNS de qualquer sub-rede local.
  3. Regra Sub-rede para Sub-rede: essa regra permite que qualquer servidor na rede de back-end se conecte a qualquer servidor na rede de front-end (mas não o contrário).
* Regra à prova de falhas (para o tráfego que não atenda a qualquer um dos itens acima):
  1. Regra Negar Todo o Tráfego: essa sempre deverá ser a última regra (em termos de prioridade) e, como tal, se os fluxos de tráfego falharem na correspondência a todas as regras anteriores, serão descartados por essa regra. Essa é uma regra padrão e normalmente é ativada; geralmente, não é necessário fazer qualquer modificação.

> [!TIP]
> Na segunda regra de tráfego de aplicativo, qualquer porta é permitida para facilitar este exemplo, em um cenário real, a porta mais específica e os intervalos de endereços devem ser usados para reduzir a superfície de ataque dessa regra.
> 
> 

<br />

> [!IMPORTANT]
> Depois que todas as regras acima forem criadas, será importante examinar a prioridade de cada regra para garantir que o tráfego seja permitido ou negado como desejado. Neste exemplo, as regras estão em ordem de prioridade. É fácil ser bloqueado pelo firewall devido a regras incorretamente ordenadas. No mínimo, certifique-se de que o gerenciamento do próprio firewall sempre esteja na regra de mais alta prioridade absoluta.
> 
> 

### <a name="rule-prerequisites"></a>Pré-requisitos de regra
Um pré-requisito para a Máquina Virtual que esteja executando o firewall são os pontos de extremidade públicos. Para que o firewall processe o tráfego, os pontos de extremidade públicos adequados deverão estar abertos. Existem três tipos de tráfego neste exemplo; 1) tráfego de gerenciamento para controlar o firewall e as regras de firewall, 2) tráfego RDP para controlar os Windows Servers e 3) Tráfego de Aplicativo. Essas são as três colunas de tipos de tráfego na metade superior da exibição lógica das regras de firewall acima.

> [!IMPORTANT]
> Uma dica importante aqui é lembrar-se de que **todo** o tráfego passará pelo firewall. Dessa forma, para acessar o servidor IIS01 por área de trabalho remota, mesmo que ele seja um Serviço de Nuvem de Front-end e esteja na sub-rede Frontend, para acessar esse servidor, será necessário o RDP para o firewall na porta 8014 e então permitir que o firewall roteie a solicitação RDP internamente para a Porta RDP do IIS01. O botão de "Conectar" do portal do Azure não funcionará porque não há um caminho RDP direto para o IIS01 (até onde o portal pode ver). Isso significa que todas as conexões da Internet serão feitas para o Serviço de Segurança e uma Porta, por exemplo, secscv001.cloudapp.net:xxxx (consulte o diagrama acima para obter o mapeamento de Porta Externa e IP e Porta Internos).
> 
> 

Um ponto de extremidade pode ser aberto no momento da criação da VM ou após a compilação; isso é feito no script de exemplo e mostrado abaixo neste trecho de código (observação; todos os itens que começam com um cifrão (por exemplo: $VMName[$i]) são uma variável definida pelo usuário do script na seção de referência deste documento. O “$i” entre colchetes, [$i], representa o número da matriz de uma VM específica em uma matriz de VMs):

    Add-AzureEndpoint -Name "HTTP" -Protocol tcp -PublicPort 80 -LocalPort 80 `
        -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | `
        Update-AzureVM

Embora não tenha sido mostrado aqui claramente devido ao uso de variáveis, os pontos de extremidade são abertos **somente** no Serviço de Nuvem de Segurança. Isso acontece para garantir que todo o tráfego de entrada seja manipulado (roteado, em NAT, descartado) pelo firewall.

Um cliente de gerenciamento precisará ser instalado em um PC para gerenciar o firewall e para criar as configurações necessárias. Consulte a documentação do fornecedor do seu firewall (ou outro NVA) sobre como gerenciar o dispositivo. O restante desta seção e a próxima seção, Criação de regras de firewall, descreverá a configuração do próprio firewall, por meio do cliente de gerenciamento de fornecedores (isto é, não o portal do Azure ou o PowerShell).

As instruções para download do cliente e para a conexão ao Barracuda usada neste exemplo poderão ser encontradas aqui: [Administrador do NG Barracuda](https://techlib.barracuda.com/NG61/NGAdmin)

Uma vez conectado ao firewall mas antes da criação de regras de firewall, existem duas classes de objeto de pré-requisito que podem facilitar a criação de regras; objetos de Rede e Serviço.

Neste exemplo, três objetos de rede nomeados devem ser definidos (um para a sub-rede Frontend e para a sub-rede Backend, além de um objeto de rede para o endereço IP do servidor DNS). Para criar uma rede nomeada; no painel de cliente do Administrador do NG Barracuda, navegue até a guia de configuração, na seção Configuração Operacional, clique em Conjunto de Regras, clique em “Redes” no menu Objetos de Firewall, então clique em Novo no menu Editar Redes. Agora, crie o objeto de rede ao adicionar o nome e o prefixo:

![Criar um objeto de rede de front-end][3]

Isso criará uma rede nomeada para a sub-rede FrontEnd, um objeto semelhante deve ser criado para a sub-rede BackEnd também. Agora as sub-redes podem ser mais facilmente referenciadas pelo nome nas regras de firewall.

Para o Objeto de Servidor DNS:

![Criar um objeto de servidor DNS][4]

Essa referência de endereço IP único será utilizada em uma regra DNS posteriormente neste documento.

Os segundos objetos de pré-requisito são os objetos de Serviços. Eles representarão as portas de conexão RDP para cada servidor. Como o objeto de serviço RDP existente está associado à porta RDP padrão, 3389, novos Serviços poderão ser criados para permitir o tráfego de portas externas (8014 8026). As novas portas também poderiam ser adicionadas ao serviço RDP existente, mas para facilitar a demonstração, uma regra individual para cada servidor poderá ser criada. Para criar uma nova regra RDP para um servidor; no painel de cliente do Administrador do NG Barracuda, navegue até a guia de configuração, na seção Configuração Operacional, clique em Conjunto de Regras, clique em “Serviços” no menu Objetos de Firewall, role para baixo na lista de serviços e selecione o serviço “RDP”. Com o botão direito do mouse e selecione copiar, então clique com botão direito do mouse e selecione Colar. Agora há um Objeto de Serviço RDP-Copy1 que pode ser editado. Clique com o botão direito do mouse em RDP-Copy1 e selecione Editar, a janela Editar o Objeto de Serviço  aparecerá como mostrado aqui:

![Cópia de regra RDP padrão][5]

Os valores podem ser editados para representar o serviço RDP para um servidor específico. Para a AppVM01, a regra RDP acima deve ser modificada para refletir um novo Nome do Serviço, uma Descrição e a Porta RDP externa usada no diagrama da Figura 8 (observação: as portas são alteradas do padrão de RDP, 3389, para a porta externa usada para este servidor específico; no caso da AppVM01, a Porta externa é 8025). O serviço modificado é mostrado abaixo:

![Regra de AppVM01][6]

Esse processo deve ser repetido para criar Serviços RDP para os demais servidores; AppVM02, DNS01 e IIS01. A criação desses Serviços simplificará a criação da Regra e a tornará mais óbvia na próxima seção.

> [!NOTE]
> Um serviço RDP para o Firewall não é necessário por dois motivos. 1) primeiro, a VM do firewall é uma imagem baseada em Linux e, portanto, o SSH seria usado na porta 22 para o gerenciamento de VM em vez de RDP e 2) a porta 22 e outras duas portas de gerenciamento são permitidas na primeira regra de gerenciamento descrita abaixo para permitir a conectividade de gerenciamento.
> 
> 

### <a name="firewall-rules-creation"></a>Criação de regras de firewall
Há três tipos de regras de firewall usadas neste exemplo, todas elas têm ícones distintos:

A regra de redirecionamento do aplicativo: ![ícone de Redirecionamento do Aplicativo][7]

A regra NAT de destino: ![ícone de NAT de Destino][8]

A regra de Passagem: ![ícone de Passagem][9]

Para saber mais sobre essas regras, visite o site do Barracuda.

Para criar as regras a seguir (ou verificar as regras padrão existentes), começando do painel de cliente do Administrador do NG Barracuda, navegue até a guia de configuração, na seção Configuração Operacional, clique em Conjunto de Regras. Uma grade chamada "Regras Principais" mostrará as regras ativas e desativadas existentes neste firewall. No canto superior direito desta grade, há um pequeno botão "+" verde, clique nele para criar uma nova regra (observação: seu firewall poderá estar “bloqueado” para alterações, caso você veja um botão marcado como “Bloquear” e não será possível criar ou editar regras; clique nesse botão para “desbloquear” o conjunto de regras e permitir a edição). Se desejar editar uma regra existente, selecione essa regra, clique com botão direito do mouse e selecione Editar Regra.

Depois que as regras forem criadas e;ou modificadas, elas deverão ser enviadas por push para o firewall e ativadas; se isso não for feito, as alterações de regra não entrarão em vigor. O processo de envio por push e ativação é descrito abaixo das descrições detalhadas da regra.

As especificidades de cada regra exigida para a conclusão deste exemplo são descritas desta maneira:

* **Regra de Gerenciamento de Firewall**: essa regra de Redirecionamento de Aplicativo permite que o tráfego passe para as portas de gerenciamento do dispositivo de rede virtual, neste exemplo, um Firewall Barracuda NextGen. As portas de gerenciamento são 801 807 e, opcionalmente, 22. As portas internas e externas são iguais (isto é, não há tradução de porta). Essa regra, SETUP-MGMT-ACCESS, é uma regra padrão e é habilitada por padrão (no Firewall NextGen Barracuda versão 6.1).
  
    ![Regra de gerenciamento de firewall][10]

> [!TIP]
> O espaço de endereço de origem nesta regra é Qualquer, se os intervalos de endereços IP de gerenciamento forem conhecidos, a redução desse escopo também reduziria a superfície de ataque às portas de gerenciamento.
> 
> 

* **Regras RDP**: essas regras de NAT de destino permitirão o gerenciamento dos servidores individuais via RDP.
  Há quatro campos essenciais necessários para a criação dessa regra:
  
  1. Origem – para permitir o RDP de qualquer lugar, a referência "Qualquer" é usada no campo Origem.
  2. Serviço – use o Objeto de Serviço apropriado criado anteriormente, neste caso, "AppVM01 RDP", as portas externas redirecionam para o endereço IP local de servidores e para a porta 3386 (a porta RDP padrão). Essa regra específica destina-se ao acesso RDP para AppVM01.
  3. Destino – deve ser a *porta local no firewall*, "IP Local do DHCP 1" ou eth0, se IPs estáticos estiverem sendo usados. O número ordinal (eth0, eth1 etc.) poderá ser diferente caso o dispositivo de rede tenha várias interfaces locais. Essa é a porta em que o firewall está enviando (pode ser igual á porta de recebimento), o destino roteado real está no campo Lista de Destino.
  4. Redirecionamento – essa seção informa ao dispositivo virtual para onde redirecionar esse tráfego. O redirecionamento mais simples é colocar o IP e a Porta (opcional) no campo Lista de Destino. Se nenhuma porta for usada, a porta de destino na solicitação de entrada será usada (isto é, não haverá tradução), caso a porta seja designada, também terá NAT junto com o endereço IP.
     
     ![Regra RDP do firewall][11]
     
     Será necessário criar um total de quatro regras RDP: 
     
     | Nome da Regra | Servidor | O Barramento de | Lista de destino |
     | --- | --- | --- | --- |
     | RDP-para-IIS01 |IIS01 |RDP de IIS01 |10.0.1.4:3389 |
     | RDP-para-DNS01 |DNS01 |RDP de DNS01 |10.0.2.4:3389 |
     | RDP-para-AppVM01 |AppVM01 |RDP de AppVM01 |10.0.2.5:3389 |
     | RDP-para-AppVM02 |AppVM02 |RDP de AppVm02 |10.0.2.6:3389 |

> [!TIP]
> Limitar o escopo dos campos Origem e Serviço reduzirá a superfície de ataque. O escopo mais limitado que permita a funcionalidade deverá ser usado.
> 
> 

* **Regras de Tráfego do Aplicativo**: há duas Regras de Tráfego de Aplicativo, a primeira para o tráfego da Web de front-end e a segunda para o tráfego de back-end (por exemplo, o servidor Web para a camada de dados). Essas regras dependerão de arquitetura de rede (onde os servidores são colocados) e dos fluxos de tráfego (a direção na qual o tráfego flui e quais portas são usadas).
  
    Em primeiro lugar, será discutida a regra de front-end para o tráfego Web:
  
    ![Regra Web do firewall][12]
  
    Essa regra NAT de Destino permite que o tráfego de aplicativo acesse o servidor de aplicativo. Embora as outras regras permitam a segurança, o gerenciamento etc, as Regras de Aplicativo são as que permitem que usuários ou serviços externos acessem os aplicativos. Neste exemplo, há um único servidor Web na porta 80 e, portanto, a única regra de aplicativo de firewall redirecionará o tráfego de entrada para o IP externo, para o endereço IP interno dos servidores Web.
  
    **Observação**: não há porta atribuída no campo Lista de Destino e, portanto, a porta 80 de entrada (ou a 443 para o Serviço selecionado) será usada no redirecionamento do servidor Web. Se o servidor Web estiver ouvindo em uma porta diferente, por exemplo, a porta 8080, o campo Lista de Destino poderia ser atualizado para 10.0.1.4:8080 para permitir o redirecionamento da Porta também.
  
    A próxima Regra de Tráfego de Aplicativo é a regra de back-end que permite que o Servidor Web converse com o servidor AppVM01 (mas não com AppVM02) por meio do serviço Qualquer:
  
    ![Regra da AppVM01 do firewall][13]
  
    Essa regra Aprovar permite que qualquer servidor IIS na sub-rede Frontend acesse a AppVM01 (Endereço IP 10.0.2.5) em Qualquer porta, usando todos os Protocolos para acessar dados necessários ao aplicativo Web.
  
    Nesta captura de tela, um "\<explicit-dest\>" é usado no campo Destino para significar 10.0.2.5 como o destino. Isso poderia ser explícito como mostrado ou um Objeto de Rede nomeado (como feito nos pré-requisitos do servidor DNS). O administrador do firewall decidirá qual método será usado. Para adicionar 10.0.2.5 como um Destino Explícito, clique duas vezes na primeira linha em branco em \<explicit-dest\> e insira o endereço na janela pop-up.
  
    Com essa regra Aprovar, nenhuma NAT será necessária, já que esse é um tráfego interno e, portanto, o Método de Conexão poderá ser definido como "Sem SNAT".
  
    **Observação**: a rede Origem nesta regra será qualquer recurso da sub-rede FrontEnd, caso haja somente um, ou um número específico de servidores Web conhecido, um recurso de Objeto de Rede poderia ser criado para ser mais específico daqueles endereços IP exatos em vez de toda a sub-rede Frontend.

> [!TIP]
> Esta regra usa o serviço de "Qualquer" para facilitar o aplicativo de exemplo para instalação e uso; também permitirá o ICMPv4 (ping) em uma única regra. No entanto, essa não é uma prática recomendada. As portas e protocolos ("Serviços") devem ser limitados ao mínimo possível que permita que a operação do aplicativo reduza a superfície de ataque nesse limite.
> 
> 

<br />

> [!TIP]
> Embora essa regra mostre uma referência explicit-dest em uso, uma abordagem consistente deverá ser usado na configuração do firewall. É recomendável que o Objeto de Rede nomeado seja usado para facilitar a capacidade de leitura e de suporte. explicit-dest será usado aqui somente para mostrar um método de referência alternativo e geralmente não é recomendado (especialmente para configurações complexas).
> 
> 

* **Regra de Saída para Internet**: essa regra aprovar permitirá que o tráfego de qualquer rede Origem passe para as redes Destino selecionadas. Normalmente, essa regra é uma regra padrão que já existe no firewall NextGen Barracuda, mas em um estado desabilitado. Clicar com o botão direito do mouse nessa regra poderá acessar o comando Ativar Regra. A regra mostrada aqui foi modificada para adicionar as duas sub-redes locais criadas como referências na seção de pré-requisito deste documento para o atributo Origem dessa regra.
  
    ![Regra de saída do firewall][14]
* **Regra DNS**: essa regra Aprovar permite que somente o tráfego DNS (porta 53) passe para o servidor DNS. Para este ambiente, a maior parte do tráfego do Frontend para o Backend será bloqueado, e essa regra permite especificamente DNS.
  
    ![Regra DNS do firewall][15]
  
    **Observação**: nesta captura de tela, o Método de Conexão foi incluído. Como essa regra destina-se ao tráfego de endereço IP interno para IP interno, NAT não será necessário; esse Método de Conexão é definido como “Sem SNAT” para essa regra Aprovar.
* **Regra Sub-rede para Sub-rede**: essa regra Aprovar é uma regra padrão ativada e modificada para permitir que todos os servidores da sub-rede Backend se conectem a qualquer servidor da sub-rede Frontend. Essa regra é para todo o tráfego interno, de forma que o Método de Conexão pode ser definido como Sem SNAT.
  
    ![Regra IntraVNet do firewall][16]
  
    **Observação**: a caixa de seleção Bidirecional não está marcada (nem é marcado na maioria das regras), isso é significativo para essa regra, já que ela torna essa regra “unidirecional”, uma conexão pode ser iniciada na sub-rede Backend para a rede Frontend, mas não inverso. Se essa caixa de seleção tiver sido marcada, essa regra permitirá o tráfego bidirecional, o que não é desejável em nosso diagrama lógico.
* **Regra Negar Todo o Tráfego**: essa sempre deverá ser a última regra (em termos de prioridade) e, como tal, se os fluxos de tráfego falharem na correspondência a todas as regras anteriores, serão descartados por essa regra. Essa é uma regra padrão e normalmente é ativada; geralmente, não é necessário fazer qualquer modificação. 
  
    ![Regra Negar do firewall][17]

> [!IMPORTANT]
> Depois que todas as regras acima forem criadas, será importante examinar a prioridade de cada regra para garantir que o tráfego seja permitido ou negado como desejado. Neste exemplo, as regras estão na ordem em que devem aparecer na Grade Principal das regras de encaminhamento no Cliente de Gerenciamento do Barracuda.
> 
> 

## <a name="rule-activation"></a>Ativação da regra
Com o conjunto de regras modificado para a especificação do diagrama lógico, o conjunto de regras deve ser carregado no firewall e ativado.

![Ativação de regra de firewall][18]

No canto superior direito do cliente de gerenciamento, há um cluster de botões. Clique no botão "Enviar Alterações" para enviar as regras modificadas para o firewall, então clique no botão "Ativar".

Com a ativação do conjunto de regras de firewall, a criação do ambiente de exemplo foi concluída.

## <a name="traffic-scenarios"></a>Cenários de tráfego
> [!IMPORTANT]
> Uma dica importante aqui é lembrar-se de que **todo** o tráfego virá do firewall. Dessa forma, para acessar o servidor IIS01 por área de trabalho remota, mesmo que ele seja um Serviço de Nuvem de Front-end e esteja na sub-rede Frontend, para acessar esse servidor, será necessário o RDP para o firewall na porta 8014 e então permitir que o firewall roteie a solicitação RDP internamente para a Porta RDP do IIS01. O botão de "Conectar" do portal do Azure não funcionará porque não há um caminho RDP direto para o IIS01 (até onde o portal pode ver). Isso significa que todas as conexões vindas da Internet serão para o Serviço de Segurança e uma Porta, por exemplo, secscv001.cloudapp.net:xxxx.
> 
> 

Nestes cenários, as seguintes regras de firewall devem estar funcionando:

1. Gerenciamento do firewall
2. RDP para IIS01
3. RDP para DNS01
4. RDP para AppVM01
5. RDP para AppVM02
6. Tráfego de aplicativo para a Web
7. Tráfego de aplicativo para AppVM01
8. Saída para a Internet
9. Front-end para DNS01
10. Tráfego entre sub-redes (somente back-end para front-end)
11. Negar Tudo

O conjunto de regras de firewall real provavelmente terá muitas outras regras além dessas, as regras em qualquer firewall específico também terão os números de prioridade diferentes das listadas aqui. Esta lista e os números associados devem fornecer relevância entre estas onze regras e a prioridade relativa entre elas. Em outras palavras; no firewall real, "RDP para IIS01" pode ser regra número 5, mas desde que esteja abaixo da regra “Gerenciamento do Firewall” e acima da regra “RDP para DNS01”, se alinharia com a intenção desta lista. A lista também ajudará nos cenários baixo, pois permite a rapidez; por exemplo, "Regra de FW 9 (DNS)". Também para termos rapidez, as quatro regras RDP serão chamadas coletivamente de “as regras RDP” quando o cenário de tráfego não estiver relacionado ao RDP.

Lembre-se também de que os Grupos de Segurança de Rede existem para o tráfego de Internet de entrada nas sub-redes Frontend e Backend.

#### <a name="allowed-internet-to-web-server"></a>(Permitido) Internet para servidor Web
1. O usuário da Internet solicita uma página HTTP de SecSvc001.CloudApp.Net (Internet Voltada para o Serviço de Nuvem)
2. O serviço de novem passa o tráfego pelo ponto de extremidade aberto na porta 80 para a interface do firewall em 10.0.0.4:80
3. Nenhum NSG atribuído à sub-rede Security e, portanto, as regras NSG do sistema permitem o tráfego para o firewall
4. O tráfego chega ao endereço IP interno do firewall (10.0.1.4)
5. O firewall inicia o processamento de regras:
   1. A Regra FW 1 (Gerenc. FW) não se aplica; vá para a próxima regra
   2. As Regras FW 2 - 5 (regras RDP) não se aplicam; vá para a próxima regra
   3. A Regra FW 6 (Aplicativo: Web) se aplica; o tráfego é permitido, o firewall usa a NAT para 10.0.1.4 (IIS01)
6. A sub-rede Frontend inicia o processamento da regra de entrada:
   1. A Regra NSG 1 (Bloquear Internet) não se aplica (esse tráfego passou por NAT pelo firewall e, portanto, o endereço de origem agora é o firewall, que está na sub-rede Security e é visto pelo NSG da sub-rede Frontend como tráfego “local” e assim é permitido), vá para a próxima regra
   2. As Regras NSG padrão permitem o tráfego de sub-rede para sub-rede, o tráfego é permitido, pare o processamento da regra NSG
7. O IIS01 está escutando o tráfego da Web, recebe essa solicitação e começa a processar a solicitação
8. O IIS01 tenta iniciar uma sessão FTP para AppVM01 na sub-rede Backend
9. A rota UDR na sub-rede Frontend torna o firewall o próximo salto
10. Não há regras de saída na sub-rede Frontend; o tráfego é permitido
11. O firewall inicia o processamento de regras:
    1. A Regra FW 1 (Gerenc. FW) não se aplica; vá para a próxima regra
    2. As Regras FW 2 - 5 (regras RDP) não se aplicam; vá para a próxima regra
    3. A Regra FW 6 (Aplicativo: Web) não se aplica; vá para a próxima regra
    4. A Regra FW 7 (Aplicativo: Backend) se aplica; o tráfego é permitido, o firewall encaminha o tráfego para 10.0.2.5 (AppVM01)
12. A sub-rede Backend inicia o processamento da regra de entrada:
    1. A Regra NSG 1 (Bloquear Internet) não se aplica; vá para a próxima regra
    2. As Regras NSG padrão permitem o tráfego de sub-rede para sub-rede, o tráfego é permitido, pare o processamento da regra NSG
13. O AppVM01 recebe a solicitação e inicia a sessão e responde
14. A rota UDR na sub-rede de back-end transforma o firewall no próximo salto
15. Como não há nenhuma regra NSG de saída na sub-rede Backend, a resposta é permitida
16. Como isso está retornando o tráfego em uma sessão estabelecida, o firewall passa a resposta de volta ao servidor Web (IIS01)
17. A sub-rede Frontend inicia o processamento da regra de entrada:
    1. A Regra NSG 1 (Bloquear Internet) não se aplica; vá para a próxima regra
    2. As Regras NSG padrão permitem o tráfego de sub-rede para sub-rede, o tráfego é permitido, pare o processamento da regra NSG
18. O servidor IIS recebe a resposta, conclui a transação com AppVM01 e conclui a criação da resposta HTTP; essa resposta HTTP é enviada ao solicitante
19. Como não há regras NSG de saída na sub-rede Frontend, a resposta será permitida
20. A resposta HTTP chega ao firewall e, como é a resposta para uma sessão de NAT estabelecida, é aceita pelo firewall
21. O firewall redireciona a resposta de volta ao Usuário da Internet
22. Como não há nenhuma regra NSG de saída ou saltos UDR na sub-rede Frontend, a resposta é permitida e o Usuário da Internet recebe a página da Web solicitada.

#### <a name="allowed-internet-rdp-to-backend"></a>(Permitido) RDP da Internet para Backend
1. O Administrador do Servidor na Internet solicita uma sessão RDP para AppVM01 via SecSvc001.CloudApp.Net:8025, onde 8025 é o número da porta atribuído ao usuário para a regra de firewall “RDP para AppVM01”
2. O serviço de nuvem passa o tráfego pelo ponto de extremidade aberto na porta 8025 para a interface do firewall em 10.0.0.4:8025
3. Nenhum NSG atribuído à sub-rede Security e, portanto, as regras NSG do sistema permitem o tráfego para o firewall
4. O firewall inicia o processamento de regras:
   1. A Regra FW 1 (Gerenc. FW) não se aplica; vá para a próxima regra
   2. A Regra FW 2 (IIS RDP) não se aplica; vá para a próxima regra
   3. A Regra FW 3 (DNS01 RDP) não se aplica; vá para a próxima regra
   4. Aplicar FW regra 4 (RDP AppVM01), o tráfego é permitido, o firewall NATs para 10.0.2.5:3386 (porta RDP em AppVM01)
5. A sub-rede Backend inicia o processamento da regra de entrada:
   1. A Regra NSG 1 (Bloquear Internet) não se aplica (esse tráfego passou por NAT pelo firewall e, portanto, o endereço de origem agora é o firewall, que está na sub-rede Security e é visto pelo NSG da sub-rede Backend como tráfego “local” e assim é permitido), vá para a próxima regra
   2. As Regras NSG padrão permitem o tráfego de sub-rede para sub-rede, o tráfego é permitido, pare o processamento da regra NSG
6. O AppVM01 está ouvindo o tráfego RDP e responde
7. Sem regras de saída, as regras NSG padrão serão aplicadas e o tráfego de retorno será permitido
8. O UDR roteia o tráfego de saída para o firewall como o próximo nó
9. Porque isso está retornando o tráfego em uma sessão estabelecida,  o firewall passa a resposta para o usuário da Internet
10. A sessão RDP está habilitada
11. O AppVM01 solicita a senha do nome de usuário

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Permitido) Pesquisa de DNS do Servidor Web no servidor DNS
1. O Servidor Web, IIS01, necessita de um feed de dados em www.data.gov, mas precisa resolver o endereço.
2. A configuração de rede para a Rede Virtual lista DNS01 (10.0.2.4 na sub-rede Backend), já que o servidor DNS primário, IIS01, envia a solicitação DNS para DNS01
3. O UDR roteia o tráfego de saída para o firewall como o próximo nó
4. Nenhuma regra de saída associada à sub-rede Frontend; o tráfego é permitido
5. O firewall inicia o processamento de regras:
   1. A Regra FW 1 (Gerenc. FW) não se aplica; vá para a próxima regra
   2. As Regras FW 2 - 5 (Regras RDP) não se aplicam; vá para a próxima regra
   3. As Regras FW 6 e 7 (Regras de Aplicativo) não se aplicam; vá para a próxima regra
   4. A Regra FW 8 (Para a Internet) não se aplicam; vá para a próxima regra
   5. A Regra FW 9 (DNS) não se aplica; o tráfego é permitido, o firewall encaminha o tráfego para 10.0.2.4 (DNS01)
6. A sub-rede Backend inicia o processamento da regra de entrada:
   1. A Regra NSG 1 (Bloquear Internet) não se aplica; vá para a próxima regra
   2. As Regras NSG padrão permitem o tráfego de sub-rede para sub-rede, o tráfego é permitido, pare o processamento da regra NSG
7. O servidor DNS recebe a solicitação
8. O servidor DNS não tem o endereço armazenado em cache e consulta um servidor DNS raiz na Internet
9. O UDR roteia o tráfego de saída para o firewall como o próximo nó
10. Nenhuma regra NSG de saída na sub-rede Backend; o tráfego é permitido
11. O firewall inicia o processamento de regras:
    1. A Regra FW 1 (Gerenc. FW) não se aplica; vá para a próxima regra
    2. As Regras FW 2 - 5 (Regras RDP) não se aplicam; vá para a próxima regra
    3. As Regras FW 6 e 7 (Regras de Aplicativo) não se aplicam; vá para a próxima regra
    4. Regra FW 8 (Para a Internet) se aplica; o tráfego é permitido, a sessão tem SNAT para o servidor DNS raiz na Internet
12. O servidor DNS da Internet responde e, desde que esta sessão tenha sido iniciada do firewall, a resposta será aceita pelo firewall
13. Como esta é uma sessão estabelecida, o firewall encaminha a resposta para o servidor que está iniciando, DNS01
14. A sub-rede Backend inicia o processamento da regra de entrada:
    1. A Regra NSG 1 (Bloquear Internet) não se aplica; vá para a próxima regra
    2. As Regras NSG padrão permitem o tráfego de sub-rede para sub-rede, o tráfego é permitido, pare o processamento da regra NSG
15. O servidor DNS recebe e armazena em cache a resposta e então responde à solicitação inicial para IIS01
16. A rota UDR na sub-rede de back-end transforma o firewall no próximo salto
17. Não existe nenhuma regra NSG de saída na sub-rede Backend; o tráfego é permitido
18. Essa é uma sessão estabelecida no firewall, a resposta é encaminhada pelo firewall de volta ao servidor IIS
19. A sub-rede Frontend inicia o processamento da regra de entrada:
    1. Não há nenhuma regra NSG que se aplique ao tráfego de Entrada da sub-rede Backend para a sub-rede Frontend e, portanto, nenhuma das regras NSG se aplica
    2. A regra do sistema padrão que permite o tráfego entre sub-redes permitiria esse tráfego e, portanto, o tráfego é permitido
20. O IIS01 recebe a resposta do DNS01

#### <a name="allowed-backend-server-to-frontend-server"></a>(Permitido) Servidor Backend para o servidor Frontend
1. Um administrador fez logon na AppVM02 via RDP solicita um arquivo diretamente do servidor IIS01 por meio do Gerenciador de Arquivos do Windows
2. A rota UDR na sub-rede de back-end transforma o firewall no próximo salto
3. Como não há nenhuma regra NSG de saída na sub-rede Backend, a resposta é permitida
4. O firewall inicia o processamento de regras:
   1. A Regra FW 1 (Gerenc. FW) não se aplica; vá para a próxima regra
   2. As Regras FW 2 - 5 (Regras RDP) não se aplicam; vá para a próxima regra
   3. As Regras FW 6 e 7 (Regras de Aplicativo) não se aplicam; vá para a próxima regra
   4. A Regra FW 8 (Para a Internet) não se aplicam; vá para a próxima regra
   5. A Regra FW 9 (DNS) não se aplica; vá para a próxima regra
   6. A Regra FW 10 (Entre Sub-redes) não se aplica; o firewall passa o tráfego para 10.0.1.4 (IIS01)
5. A sub-rede Frontend inicia o processamento da regra de entrada:
   1. A Regra NSG 1 (Bloquear Internet) não se aplica; vá para a próxima regra
   2. As Regras NSG padrão permitem o tráfego de sub-rede para sub-rede, o tráfego é permitido, pare o processamento da regra NSG
6. Supondo a autenticação e a autorização adequadas, o IIS01 aceita a solicitação e responde
7. A rota UDR na sub-rede Frontend torna o firewall o próximo salto
8. Como não há regras NSG de saída na sub-rede Frontend, a resposta será permitida
9. Como se trata de uma sessão existente no firewall, essa resposta é permitida e o firewall retorna a resposta a AppVM02
10. A sub-rede Backend inicia o processamento da regra de entrada:
    1. A Regra NSG 1 (Bloquear Internet) não se aplica; vá para a próxima regra
    2. As Regras NSG padrão permitem o tráfego de sub-rede para sub-rede, o tráfego é permitido, pare o processamento da regra NSG
11. A AppVM02 recebe a resposta

#### <a name="denied-internet-direct-to-web-server"></a>(Negado) Internet direta ao Servidor Web
1. O usuário da Internet tenta acessar um servidor Web, IIS01, por meio do serviço FrontEnd001.CloudApp.Net
2. Como não há nenhum ponto de extremidade aberto para tráfego HTTP, isso não passaria pelo Serviço de Nuvem e não alcançaria o servidor
3. Se os pontos de extremidade estiverem abertos por algum motivo, o NSG (Bloquear Internet) na sub-rede Frontend bloqueará esse tráfego
4. Por fim, a rota UDR da sub-rede Frontend enviaria todo o tráfego de saída de IIS01 para o firewall como o próximo salto e o firewall veria isso como tráfego assimétrico e descartaria a resposta de saída. Portanto, há pelo menos três camadas de defesa independentes entre a Internet e IIS01 via seu serviço de nuvem impedindo o acesso não autorizado/impróprio.

#### <a name="denied-internet-to-backend-server"></a>(Negado) Internet para Servidor Backend
1. O usuário da Internet tenta acessar um arquivo em AppVM01 por meio do serviço BackEnd001.CloudApp.Net
2. Como não há nenhum ponto de extremidade aberto para compartilhamento de arquivos, isso não passaria no Serviço de Nuvem e não alcançaria o servidor
3. Se os pontos de extremidade estiverem abertos por algum motivo, o NSG (Bloquear Internet) bloquearia esse tráfego
4. Por fim, a rota UDR enviaria todo o tráfego de saída de AppVM01 para o firewall como o próximo salto e o firewall veria isso como tráfego assimétrico e descartaria a resposta de saída. Portanto, há pelo menos três camadas de defesa independentes entre a Internet e AppVM01 via seu serviço de nuvem impedindo o acesso não autorizado/impróprio.

#### <a name="denied-frontend-server-to-backend-server"></a>(Negado) Servidor Frontend para o servidor Backend
1. Suponha que IIS01 tenha sido comprometido e esteja executando o código mal-intencionado tentando verificar os servidores da sub-rede Backend.
2. A rota UDR da sub-rede Frontend enviará todo o tráfego de saída de IIS01 para o firewall como o próximo salto. Isso não é algo que possa ser alterado pela VM comprometida.
3. O firewall processaria o tráfego, se a solicitação tivesse sido feita para AppVM01 ou para o servidor DNS, e o DNS pesquisaria se o tráfego poderia potencialmente ser permitido pelo firewall (devido às Regras FW 7 e 9). Todo o outro tráfego seria bloqueado pela Regra FW 11 (Negar Tudo).
4. Se a detecção de ameaças avançada tiver sido habilitada no firewall (o que não será abordado neste documento, consulte a documentação do fornecedor para obter os recursos de ameaça avançados do seu dispositivo de rede específico), mesmo o tráfego que seria permitido pelas regras básicas de encaminhamento discutidas neste documento poderá ser impedido caso contenha assinaturas ou padrões conhecidos que sinalizem uma regra de ameaça avançada.

#### <a name="denied-internet-dns-lookup-on-dns-server"></a>(Negado) Pesquisa DNS da Internet no servidor DNS
1. O usuário da Internet tenta pesquisar um registro DNS interno em DNS01 por meio do serviço BackEnd001.CloudApp.Net 
2. Como não há nenhum ponto de extremidade aberto para tráfego DNS, isso não passaria pelo Serviço de Nuvem e não alcançaria o servidor
3. Se os pontos de extremidade estiverem abertos por algum motivo, a regra NSG (Bloquear Internet) na sub-rede Frontend bloquearia esse tráfego
4. Por fim, a rota UDR da sub-rede Backend enviaria todo o tráfego de saída de DNS01 para o firewall como o próximo salto e o firewall veria isso como tráfego assimétrico e descartaria a resposta de saída. Portanto, há pelo menos três camadas de defesa independentes entre a Internet e DNS01 via seu serviço de nuvem impedindo o acesso não autorizado/impróprio.

#### <a name="denied-internet-to-sql-access-through-firewall"></a>(Negado) Acesso Internet para SQL por meio do Firewall
1. O usuário da Internet solicita dados SQL de SecSvc001.CloudApp.Net (Serviço de Nuvem Voltado para a Internet)
2. Como não há nenhum ponto de extremidade aberto para SQL, isso não passaria no Serviço de Nuvem e não alcançaria o firewall
3. Se os pontos de extremidade do SQL estiverem abertos por algum motivo, o firewall iniciaria o processamento de regras:
   1. A Regra FW 1 (Gerenc. FW) não se aplica; vá para a próxima regra
   2. As Regras FW 2 - 5 (regras RDP) não se aplicam; vá para a próxima regra
   3. As Regras FW 6 e 7 (Regras de Aplicativo) não se aplicam; vá para a próxima regra
   4. A Regra FW 8 (Para a Internet) não se aplicam; vá para a próxima regra
   5. A Regra FW 9 (DNS) não se aplica; vá para a próxima regra
   6. A Regra FW 10 (Entre Sub-redes) não se aplicam; vá para a próxima regra
   7. A Regra FW 11 (Negar Tudo) se aplica; o tráfego é permitido, pare o processamento da regra

## <a name="references"></a>Referências
### <a name="main-script-and-network-config"></a>Script principal e configuração de rede
Salve o Script Completo em um arquivo de script do PowerShell. Salve a Configuração de Rede em um arquivo chamado "NetworkConf2.xml".
Modifique as variáveis definidas pelo usuário como necessário. Execute o script e então siga as instruções de configuração de regra de Firewall acima.

#### <a name="full-script"></a>Script Completo
Esse script se baseará nas variáveis definidas pelo usuário:

1. Conectar-se a uma assinatura do Azure
2. Criar uma nova conta de armazenamento
3. Criar uma nova Rede Virtual e três sub-redes, como definido no arquivo de Configuração de Rede
4. Criar cinco máquinas virtuais; um firewall e quatro VMs do Windows Server
5. Configurar o UDR, incluindo:
   1. Criar duas novas tabelas de rotas
   2. Adicionar rotas às tabelas
   3. Associar tabelas às sub-redes apropriadas
6. Habilitar o Encaminhamento IP no NVA
7. Configure o NSG, incluindo:
   1. Criando um NSG
   2. Adicionando uma regra
   3. Associando o NSG às sub-redes apropriadas

Este script do PowerShell deve ser executado localmente em um computador ou servidor conectado à Internet.

> [!IMPORTANT]
> Quando esse script for executado, poderá haver avisos ou outras mensagens informativas que aparecerão no PowerShell. Somente as mensagens de erro em vermelho são motivo de preocupação.
> 
> 

    <# 
     .SYNOPSIS
      Example of DMZ and User Defined Routing in an isolated network (Azure only, no hybrid connections)

     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Three new cloud services
       - Three Subnets (SecNet, FrontEnd, and BackEnd subnets)
       - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
       - One server on the FrontEnd Subnet
       - Three Servers on the BackEnd Subnet
       - IP Forwading from the FireWall out to the internet
       - User Defined Routing FrontEnd and BackEnd Subnets to the NVA

      Before running script, ensure the network configuration file is created in
      the directory referenced by $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).

     .Notes
      Everyone's security requirements are different and can be addressed in a myriad of ways.
      Please be sure that any sensitive data or applications are behind the appropriate
      layer(s) of protection. This script serves as an example of some of the techniques
      that can be used, but should not be used for all scenarios. You are responsible to
      assess your security needs and the appropriate protections needed, and then effectively
      implement those protections.

      Security Service (SecNet subnet 10.0.0.0/24)
       myFirewall - 10.0.0.4

      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       IIS01      - 10.0.1.4

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
        $SecureService = "SecSvc001"
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"

      # Network Details
        $VNetName = "CorpNetwork"
        $VNetPrefix = "10.0.0.0/16"
        $SecNet = "SecNet"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf3.xml"

      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

      # UDR Details
        $FERouteTableName = "FrontEndSubnetRouteTable"
        $BERouteTableName = "BackEndSubnetRouteTable"

      # NSG Details
        $NSGName = "MyVNetSG"

    # User Defined VM Specific Config
        # Note: To ensure UDR and IP forwarding is setup
        # properly this script requires VM 0 be the NVA.

        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $SecureService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $SecNet
          $VMIP += "10.0.0.4"

        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"

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

    If (Test-AzureName -Service -Name $SecureService) { 
        Write-Host "The SecureService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}

    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use" -ForegroundColor Green}

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
        New-AzureService -Location $DeploymentLocation -ServiceName $SecureService -ErrorAction Stop
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
                # Note: All traffic goes through the firewall, so we'll need to set up all ports here.
                #       Also, the firewall will be redirecting traffic to a new IP and Port in a forwarding
                #       rule, so all of these endpoint have the same public and local port and the firewall
                #       will do the mapping, NATing, and/or redirection as declared in the firewall rules.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPWeb"    -Protocol tcp -PublicPort 8014 -LocalPort 8014 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp1"   -Protocol tcp -PublicPort 8025 -LocalPort 8025 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp2"   -Protocol tcp -PublicPort 8026 -LocalPort 8026 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPDNS01"  -Protocol tcp -PublicPort 8024 -LocalPort 8024 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "RemoteDesktop" | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                }
            $i++
        }

    # Configure UDR and IP Forwarding
        Write-Host "Configuring UDR" -ForegroundColor Cyan

      # Create the Route Tables
        Write-Host "Creating the Route Tables" -ForegroundColor Cyan 
        New-AzureRouteTable -Name $BERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $BESubnet subnet"
        New-AzureRouteTable -Name $FERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $FESubnet subnet"

      # Add Routes to Route Tables
        Write-Host "Adding Routes to the Route Tables" -ForegroundColor Cyan 
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $FEPrefix `
            -NextHopType VNETLocal

      # Assoicate the Route Tables with the Subnets
        Write-Host "Binding Route Tables to the Subnets" -ForegroundColor Cyan 
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $BESubnet `
            -RouteTableName $BERouteTableName
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $FESubnet `
            -RouteTableName $FERouteTableName

     # Enable IP Forwarding on the Virtual Appliance
        Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] `
            |Set-AzureIPForwarding -Enable

    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

      # Add NSG Rule
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 100 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *

      # Assign the NSG to two Subnets
        # The NSG is *not* bound to the Security Subnet. The result
        # is that internet traffic flows only to the Security subnet
        # since the NSG bound to the Frontend and Backback subnets
        # will Deny internet traffic to those subnets.
        Write-Host "Binding the NSG to two subnets" -ForegroundColor Cyan
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
              <Subnet name="SecNet">
                <AddressPrefix>10.0.0.0/24</AddressPrefix>
              </Subnet>
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
[1]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3design.png "DMZ bidirecional com NVA, NSG e UDR"
[2]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3firewalllogical.png "Exibição lógica das regras de firewall"
[3]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectfrontend.png "Criar um objeto de rede de front-end"
[4]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectdns.png "Criar um objeto de servidor DNS"
[5]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpa.png "Cópia da regra RDP padrão"
[6]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpb.png "Regra de AppVM01"
[7]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconapplicationredirect.png "Ícone de redirecionamento do aplicativo"
[8]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/icondestinationnat.png "Ícone de NAT de destino"
[9]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconpass.png "Ícone de passagem"
[10]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulefirewall.png "Regra de Gerenciamento de Firewall"
[11]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulerdp.png "Regra RDP do firewall"
[12]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleweb.png "Regra Web do firewall"
[13]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleappvm01.png "Regra de AppVM01 do firewall"
[14]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleoutbound.png "Regra de saída do firewall"
[15]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledns.png "Regra de DNS do firewall"
[16]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleintravnet.png "Regra entre VNets do firewall"
[17]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledeny.png "Regra Negar do firewall"
[18]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/firewallruleactivate.png "Ativação de regra de firewall"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md

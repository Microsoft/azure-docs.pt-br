---
title: Exemplo de rede de perímetro – compilação de uma rede de perímetro para proteger aplicativos com um firewall e NSGs | Microsoft Docs
description: Criar uma rede de perímetro com um firewall e grupos de segurança de rede (NSGs)
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: c78491c7-54ac-4469-851c-b35bfed0f528
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: jonor;sivae
ms.openlocfilehash: e0271c9212b093bd803518ebeaa4b7d9682cc773
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60868296"
---
# <a name="example-2-build-a-perimeter-network-to-protect-applications-with-a-firewall-and-nsgs"></a>Exemplo 2: Crie uma rede de perímetro para proteger aplicativos com um firewall e NSGs
[Retornar para a página segurança de rede e serviços de nuvem da Microsoft][HOME]

Este exemplo mostra como criar uma rede de perímetro (também conhecido como *DMZ*, *zona desmilitarizada*, e *sub-rede filtrada*) com um firewall, quatro computadores com Windows Server, Grupos de segurança de rede (NSGs) e. Ela inclui detalhes sobre cada um dos comandos relevantes para fornecer uma compreensão mais profunda de cada etapa. A seção "Cenários de tráfego" fornece uma explicação passo a passo de como o tráfego passa pelas camadas de defesa da rede de perímetro. Por fim, a seção "Referências" fornece o código completo e instruções sobre como criar este ambiente para testar e experimentar diferentes cenários.

![Rede de perímetro de entrada com NVA e NSGs][1]

## <a name="environment"></a>Ambiente 
Este exemplo é baseado em um cenário com uma assinatura do Azure que contém estes itens:

* Dois serviços de nuvem: FrontEnd001 e BackEnd001.
* Uma rede virtual chamada CorpNetwork que tem duas sub-redes: Front-end e back-end.
* Um único NSG é aplicado a ambas as sub-redes.
* Uma solução de virtualização de rede: Um NextGen Firewall Barracuda é conectado à sub-rede de front-end.
* Um computador Windows Server que representa um servidor de aplicativos web: IIS01.
* Dois computadores Windows Server que representam servidores de back-end do aplicativo: AppVM01 e AppVM02.
* Um computador Windows Server que representa um servidor DNS: DNS01.

> [!NOTE]
> Embora este exemplo usa um NextGen Firewall Barracuda, muitos dispositivos de rede virtual pode ser usados.
> 
> 

O script do PowerShell na seção "Referências" deste artigo se baseia a maior parte do ambiente descrito aqui. As VMs e redes virtuais são criadas pelo script, mas esses processos não são descritos em detalhes neste documento.

Para compilar o ambiente:

1. Salve o arquivo XML de configuração de rede incluído na seção "Referências" (atualizado com nomes, locais e IP endereços para corresponder ao seu cenário).
2. Atualize as variáveis definidas pelo usuário no script do PowerShell para coincidir com o ambiente que de script será executado (assinaturas, nomes de serviço e assim por diante).
3. Execute o script do PowerShell.

> [!NOTE]
> A região especificada no script do PowerShell deve corresponder a região especificada no arquivo XML de configuração de rede.
>
>

Depois que o script é executado com êxito, você pode concluir estas etapas:

1. Configure as regras de firewall. Consulte a seção "Regras de Firewall" deste artigo.
2. Se você quiser, você pode configurar o servidor web e o servidor de aplicativos com um aplicativo web simples para testar a configuração de rede de perímetro. Você pode usar os scripts de dois aplicativos fornecidos na seção "Referências".

A próxima seção explica a maioria das instruções do script que se relacionam com os NSGs.

## <a name="nsgs"></a>NSGs
Neste exemplo, um grupo NSG é criado e então carregado com seis regras.

> [!TIP]
> Em geral, você deve criar suas regras específicas de "Permitir" primeiro e último as regras "Negar" mais genéricas. Os controles de prioridade atribuído que as regras são avaliadas primeiro. Depois que o tráfego é encontrado que se aplica a uma regra específica, nenhuma regra adicional será avaliada. Regras NSG podem se aplicar na direção de saída (da perspectiva da sub-rede) ou a entrada.
> 
> 

Declarativamente, essas regras são criadas para tráfego de entrada:

1. O tráfego interno de DNS (porta 53) é permitido.
2. Tráfego RDP (porta 3389) da internet para qualquer máquina virtual é permitido.
3. O tráfego HTTP (porta 80) da internet para a NVA (firewall) é permitido.
4. Todo o tráfego (todas as portas) de IIS01 para AppVM01 é permitido.
5. Todo o tráfego (todas as portas) da internet para a rede virtual inteira (ambas as sub-redes) é negado.
6. Todo o tráfego (todas as portas) da sub-rede de front-end para a sub-rede de back-end é negado.

Com essas regras associadas para cada sub-rede, se uma solicitação HTTP foram entrada da internet para o servidor web, ambos de regra 3 (Permitir) e a regra 5 (Negar) seria parece se aplicar, mas como a regra 3 tem uma prioridade mais alta, só será aplicada. A regra 5 não entram em cena. Então, a solicitação HTTP será permitida para o firewall.

Se esse mesmo tráfego estivesse tentando acessar o servidor DNS01, a regra 5 (Negar) seria a primeira a ser aplicado, portanto, o tráfego não seria permitido para passar para o servidor. A regra 6 (Negar) impede que a sub-rede de front-end se comunique com a sub-rede de back-end (exceto pelo tráfego permitido nas regras 1 e 4). Isso protege a rede de back-end no caso de um invasor compromete o aplicativo web no front-end. Nesse caso, o invasor teria acesso limitado à rede BackEnd "protegida". (O invasor poderá acessar somente os recursos expostos no servidor AppVM01.)

Há uma regra de saída padrão que permite que o tráfego de saída à internet. Neste exemplo, estamos permitindo que o tráfego de saída e não modificando quaisquer regras de saída. Para bloquear o tráfego em ambas as direções, é necessário o roteamento definido pelo usuário. Você pode aprender sobre essa técnica em um exemplo diferente na [documento de limite de segurança principal][HOME].

As regras NSG descritas aqui são semelhantes às regras NSG no [exemplo 1: criar uma DMZ simples com NSGs][Example1]. Revise a descrição NSG nesse artigo para uma visão detalhada de cada regra NSG e seus atributos.

## <a name="firewall-rules"></a>Regras de firewall
Você precisa instalar um cliente de gerenciamento em um computador para gerenciar o firewall e criar as configurações necessárias. Consulte o fornecedor de documentação do seu firewall (ou outro NVA) sobre como gerenciar o dispositivo. O restante desta seção descreve a configuração do firewall em si, por meio do cliente de gerenciamento do fornecedor (não o portal do Azure ou o PowerShell).

Ver [administrador do NG Barracuda](https://techlib.barracuda.com/NG61/NGAdmin) para obter instruções de download do cliente e a conexão com o firewall Barracuda usado neste exemplo.

Você precisa criar as regras de encaminhamento no firewall. Como o cenário deste exemplo só roteia o tráfego de internet de entrada para o firewall e, em seguida, para o servidor web, precisar apenas de encaminhamento de uma regra de NAT. No firewall Barracuda usado neste exemplo, a regra seria uma regra de NAT de destino (Dst NAT) para passar o tráfego.

Para criar a regra a seguir (ou para verificar as regras padrão existentes), conclua estas etapas:
1. No painel de cliente do administrador do NG Barracuda, na guia Configuração, nos **configuração operacional** seção, selecione **Ruleset**. 

   Uma grade chamada **regras principais** mostra existente Active Directory e desativado regras no firewall.

2. Para criar uma nova regra, selecione o pequeno verde **+** botão no canto superior direito desta grade. (Seu firewall poderá estar bloqueado. Se você vir um botão marcado **bloqueio** e não é possível criar ou editar regras, selecione o botão para desbloquear o conjunto de regras e permitir a edição.)
  
3. Para editar uma regra existente, selecione a regra, clique com botão direito e selecione **Editar regra**.

Criar uma nova regra denominada algo como **WebTraffic.** 

O ícone da regra NAT de Destino tem esta aparência:  ![Ícone de NAT de destino][2]

A regra em si será algo parecido com isso:

![Regra de firewall][3]

Qualquer endereço de entrada que acesse o firewall tentando alcançar HTTP (porta 80 ou 443 para HTTPS) será enviado para fora da interface de IP Local DHCP1 do firewall e redirecionado para o servidor web com o endereço IP 10.0.1.5. Como o tráfego está entrando na porta 80 e indo para o servidor web na porta 80, nenhuma alteração de porta é necessária. Mas a lista de destino poderia ter sido 10.0.1.5: 8080 se o servidor web escutasse na porta 8080, que convertem a porta de entrada 80 no firewall para a porta de entrada 8080 no servidor web.

Você também deve especificar um método de conexão. Para a regra de destino da internet, SNAT dinâmico é o método mais apropriado.

Mesmo que você criou apenas uma regra, é importante definir sua prioridade corretamente. Na grade de todas as regras no firewall, se essa nova regra estiver na parte inferior (abaixo da regra BLOCKALL), ela nunca entrará em ação. Verifique se que a nova regra para tráfego da web está acima da regra BLOCKALL.

Depois que a regra é criada, você precisa enviar por push para o firewall e, em seguida, ativá-la. Se você não realizar essas etapas, a alteração de regra não terão efeito. A próxima seção descreve o processo de envio por push e ativação.

## <a name="rule-activation"></a>Ativação de regra
Agora que a regra será adicionada para o conjunto de regras, você precisa carregar o conjunto de regras do firewall e ativá-la.

![Ativação de regra de firewall][4]

No canto superior direito do cliente de gerenciamento, você verá um grupo de botões. Selecione **enviar alterações** para enviar o conjunto de regras modificado para o firewall e, em seguida, selecione **ativar**.

Agora que você ativou o conjunto de regras de firewall, o ambiente foi concluído. Se você quiser, você pode executar os scripts de aplicativo de exemplo na seção "Referências" para adicionar um aplicativo no ambiente. Se você adicionar um aplicativo, você pode testar os cenários de tráfego descritos na próxima seção.

> [!IMPORTANT]
> Você deve ter percebido que não alcance o servidor web diretamente. Quando um navegador solicita uma página HTTP de FrontEnd001.CloudApp.Net, o ponto de extremidade HTTP (porta 80) passa o tráfego para o firewall, não para o servidor web. O firewall em seguida, por causa a regra que você criou anteriormente, usa a NAT para mapear a solicitação ao servidor web.
> 
> 

## <a name="traffic-scenarios"></a>Cenários de tráfego
#### <a name="allowed-web-to-web-server-through-the-firewall"></a>(Permitido) Web para o servidor web por meio do firewall
1. Um usuário da internet solicita uma página HTTP de FrontEnd001.CloudApp.Net (que é um serviço de nuvem da internet).
2. O serviço de nuvem passa o tráfego por meio de um ponto de extremidade aberto na porta 80 para a interface local do firewall em 10.0.1.4:80.
3. A sub-rede FrontEnd inicia o processamento da regra de entrada:
   1. Não se aplica a regra NSG 1 (DNS). Mover para a próxima regra.
   2. Não se aplica a regra NSG 2 (RDP). Mover para a próxima regra.
   3. Aplicar a regra NSG 3 (internet para firewall). Permita o tráfego. Pare o processamento da regra.
4. O tráfego atinge o endereço IP interno do firewall (10.0.1.4).
5. Uma regra de encaminhamento de firewall determina que este é o tráfego da porta 80 e redireciona para o servidor web IIS01.
6. O IIS01 está escutando o tráfego da web, recebe a solicitação e começa a processar a solicitação.
7. IIS01 solicita informações de instância do SQL Server no AppVM01.
8. Não há nenhuma regra de saída na sub-rede front-end, portanto, o tráfego é permitido.
9. A sub-rede BackEnd inicia o processamento da regra de entrada:
   1. Não se aplica a regra NSG 1 (DNS). Mover para a próxima regra.
   2. Não se aplica a regra NSG 2 (RDP). Mover para a próxima regra.
   3. Não se aplica a regra NSG 3 (internet para firewall). Mover para a próxima regra.
   4. A regra NSG 4 (IIS01 para AppVM01) se aplicam. Permita o tráfego. Pare o processamento da regra.
10. A instância do SQL Server no AppVM01 recebe a solicitação e responde.
11. Como não há nenhuma regra de saída na sub-rede de back-end, a resposta é permitida.
12. A sub-rede FrontEnd inicia o processamento da regra de entrada:
    1. Não há nenhuma regra NSG que aplica-se a entrada de tráfego da sub-rede de back-end para a sub-rede de front-end, portanto, nenhuma das regras de NSG se aplica.
    2. A regra padrão do sistema permitindo o tráfego entre sub-redes permite que esse tráfego, portanto, o tráfego é permitido.
13. O IIS01 recebe a resposta de AppVM01, conclui a resposta HTTP e envia ao solicitante.
14. Como esta é uma sessão NAT do firewall, o destino de resposta é inicialmente para o firewall.
15. O firewall recebe a resposta do servidor web e a encaminha para o usuário da internet.
16. Como não há nenhuma regra de saída na sub-rede FrontEnd, a resposta é permitida e o usuário da internet recebe a página da web.

#### <a name="allowed-rdp-to-backend"></a>(Permitido) RDP para back-end
1. Um administrador do servidor na internet solicita uma sessão RDP para AppVM01 em BackEnd001.CloudApp.Net:*xxxxx*, onde *xxxxx* é o número de porta atribuído aleatoriamente para RDP para AppVM01. (Você pode encontrar a porta atribuída no portal do Azure ou usando o PowerShell).
2. Porque o firewall estiver escutando apenas o endereço do FrontEnd001.CloudApp.Net, ele não está envolvido com esse fluxo de tráfego.
3. A sub-rede BackEnd inicia o processamento da regra de entrada:
   1. Não se aplica a regra NSG 1 (DNS). Mover para a próxima regra.
   2. Aplicar a regra NSG 2 (RDP). Permita o tráfego. Pare o processamento da regra.
4. Como não há nenhuma regra de saída, as regras padrão se aplicam e retornam o tráfego é permitido.
5. A sessão RDP está habilitada.
6. O AppVM01 solicita um nome de usuário e senha.

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Permitido) Pesquisa de DNS do servidor Web no servidor DNS
1. As necessidades de servidor, IIS01, web dados de um feed em www.data.gov, mas precisam resolver o endereço.
2. A configuração de rede para mostra a rede virtual DNS01 (10.0.2.4 na sub-rede BackEnd) como o servidor DNS primário. IIS01, envia a solicitação de DNS para DNS01.
3. Como não há nenhuma regra de saída na sub-rede FrontEnd, o tráfego é permitido.
4. A sub-rede BackEnd inicia o processamento da regra de entrada:
   1. Aplica-se a regra NSG 1 (DNS). Permita o tráfego. Pare o processamento da regra.
5. O servidor DNS recebe a solicitação.
6. O servidor DNS não tem o endereço armazenado em cache e consulta um servidor DNS de raiz na internet.
7. Como não há nenhuma regra de saída na sub-rede de back-end, o tráfego é permitido.
8. O DNS da internet responde do servidor. Porque a sessão foi iniciada internamente, a resposta é permitida.
9. O servidor DNS armazena em cache a resposta e responde à solicitação de IIS01.
10. Como não há nenhuma regra de saída na sub-rede de back-end, o tráfego é permitido.
11. A sub-rede FrontEnd inicia o processamento da regra de entrada:
    1. Não há nenhuma regra NSG que aplica-se a entrada de tráfego da sub-rede de back-end para a sub-rede de front-end, portanto, nenhuma das regras de NSG se aplica.
    2. A regra padrão do sistema permitindo o tráfego entre sub-redes permite que esse tráfego, portanto, o tráfego é permitido.
12. O IIS01 recebe a resposta do DNS01.

#### <a name="allowed-web-server-file-access-on-appvm01"></a>(Permitido) Acesso ao arquivo servidor Web em AppVM01
1. IIS01 solicita um arquivo em AppVM01.
2. Como não há nenhuma regra de saída na sub-rede FrontEnd, o tráfego é permitido.
3. A sub-rede BackEnd inicia o processamento da regra de entrada:
   1. Não se aplica a regra NSG 1 (DNS). Mover para a próxima regra.
   2. Não se aplica a regra NSG 2 (RDP). Mover para a próxima regra.
   3. Não se aplica a regra NSG 3 (internet para firewall). Mover para a próxima regra.
   4. A regra NSG 4 (IIS01 para AppVM01) se aplicam. Permita o tráfego. Pare o processamento da regra.
4. AppVM01 recebe a solicitação e responde com o arquivo (supondo que o acesso é autorizado).
5. Como não há nenhuma regra de saída na sub-rede de back-end, a resposta é permitida.
6. A sub-rede FrontEnd inicia o processamento da regra de entrada:
   1. Não há nenhuma regra NSG que aplica-se a entrada de tráfego da sub-rede de back-end para a sub-rede de front-end, portanto, nenhuma das regras de NSG se aplica.
   2. A regra padrão do sistema permitindo o tráfego entre sub-redes permite que esse tráfego, portanto, o tráfego é permitido.
7. O IIS01 recebe o arquivo.

#### <a name="denied-web-direct-to-web-server"></a>(Negado) Web direta ao servidor web
Como o servidor web IIS01 e o firewall estão no mesmo serviço de nuvem, eles compartilham o mesmo endereço IP público. Portanto, todo o tráfego HTTP é direcionado para o firewall. Enquanto uma solicitação seja servida com êxito, ele não pode ir diretamente para o servidor web. Ele passa, conforme projetado, por meio do firewall pela primeira vez. Consulte o primeiro cenário desta seção para o fluxo de tráfego.

#### <a name="denied-web-to-backend-server"></a>(Negado) Web para o servidor de back-end
1. Um usuário da internet tenta acessar um arquivo em AppVM01 por meio do serviço BackEnd001.CloudApp.Net.
2. Porque não há nenhum ponto de extremidade aberto para compartilhamento de arquivos, isso não passa o serviço de nuvem e não alcançar o servidor.
3. Se os pontos de extremidade estiverem abertos por algum motivo, a regra NSG 5 (internet para rede virtual) bloqueia o tráfego.

#### <a name="denied-web-dns-lookup-on-the-dns-server"></a>(Negado) Pesquisa de DNS da Web no servidor DNS
1. Um usuário da internet tenta procurar um registro DNS interno em DNS01 por meio do serviço BackEnd001.CloudApp.Net.
2. Como não há nenhum ponto de extremidade aberto para DNS, isso não passa o serviço de nuvem e não alcançar o servidor.
3. Se os pontos de extremidade estiverem abertos por algum motivo, a regra NSG 5 (internet para rede virtual) bloqueia o tráfego. (Regra 1 [DNS] não se aplica por dois motivos. Primeiro, o endereço de origem é a internet, e essa regra se aplica somente quando a rede virtual local é a origem. Em segundo lugar, essa regra é uma regra de permissão, para que ele nunca nega o tráfego).

#### <a name="denied-web-to-sql-access-through-the-firewall"></a>(Negado) Web para acesso ao SQL por meio do firewall
1. Um usuário da internet solicita dados SQL de FrontEnd001.CloudApp.Net (um serviço de nuvem da internet).
2. Como não há nenhum ponto de extremidade aberto para SQL, isso não passa o serviço de nuvem e não atingirá o firewall.
3. Se os pontos de extremidade estiverem abertos por algum motivo, a sub-rede FrontEnd inicia o processamento da regra de entrada:
   1. Não se aplica a regra NSG 1 (DNS). Mover para a próxima regra.
   2. Não se aplica a regra NSG 2 (RDP). Mover para a próxima regra.
   3. Aplicar a regra NSG 3 (internet para firewall). Permita o tráfego. Pare o processamento da regra.
4. O tráfego atinge o endereço IP interno do firewall (10.0.1.4).
5. O firewall não tem nenhuma regra de encaminhamento para SQL e descarta o tráfego.

## <a name="conclusion"></a>Conclusão
Este exemplo mostra uma maneira relativamente simples para proteger seu aplicativo com um firewall e isolar a sub-rede de back-end do tráfego de entrada.

Você pode encontrar mais exemplos e uma visão geral da rede limites de segurança [aqui][HOME].

## <a name="references"></a>Referências
### <a name="full-script-and-network-config"></a>Configuração de rede e o script completa
Salve o script completo em um arquivo de script do PowerShell. Salve o script de configuração de rede em um arquivo chamado NetworkConf2.xml.
Altere variáveis do usuário definido-conforme necessário. Execute o script e, em seguida, siga as instruções na seção "Regras de Firewall" deste artigo.

#### <a name="full-script"></a>Script completo
Esse script, com base em variáveis definidas pelo usuário, concluirá as seguintes etapas:

1. Conecte-se para uma assinatura do Azure.
2. Criar uma conta de armazenamento.
3. Crie uma rede virtual e duas sub-redes, conforme definido no arquivo de configuração de rede.
4. Crie quatro VMs do Windows Server.
5. Configure NSG. Configuração conclui essas etapas:
   * Cria um NSG.
   * Preenche o NSG com duas regras.
   * Associa o NSG às sub-redes apropriadas.

Você deve executar este script do PowerShell localmente em um computador conectado à internet ou o servidor.

> [!IMPORTANT]
> Quando você executa esse script, avisos e outras mensagens informativas podem aparecer no PowerShell. Você só precisa se preocupar sobre mensagens de erro que aparecem em vermelho.
> 
> 

```powershell
    <# 
     .SYNOPSIS
      Example of a perimeter network and Network Security Groups in an isolated network. (Azure only. No hybrid connections.)

     .DESCRIPTION
      This script will build out a sample perimeter network setup containing:
       - A default storage account for VM disks.
       - Two new cloud services.
       - Two subnets (the FrontEnd and BackEnd subnets).
       - A network virtual appliance (NVA): a Barracuda NextGen Firewall.
       - One server on the FrontEnd subnet (plus the NVA on the FrontEnd subnet).
       - Three servers on the BackEnd subnet.
       - Network Security Groups to allow/deny traffic patterns as declared.

      Before running the script, ensure the network configuration file is created in
      the directory referenced by the $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).

     .Notes
      Security requirements are different for each use case and can be addressed in many ways. Be sure that any sensitive data or applications are behind
      the appropriate layer(s) of protection. This script serves as an example of some
      of the techniques that you can use, but it should not be used for all scenarios. You
      are responsible for assessing your security needs and the appropriate protections
      and then effectively implementing those protections.

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

    # User-Defined Global Variables
      # These should be changed to reflect your subscription and services.
      # Invalid options will fail in the validation section.

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

    # User-Defined VM-Specific Config
        # To ensure proper NSG rule creation later in this script:
        #       - The web server must be VM 1.
        #       - The AppVM1 server must be VM 2.
        #       - The DNS server must be VM 4.
        #
        #       Otherwise the NSG rules in the last section of this
        #       script will need to be changed to match the modified
        #       VM array numbers ($i) so the NSG rule IP addresses
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

        # VM 2 - The First Application Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"

        # VM 3 - The Second Application Server
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
    # No user-defined variables or   #
    # configuration past this point #
    # ----------------------------- #

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

      # Create storage account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}

      # Update subscription pointer to new storage account
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
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation variable is correct and the network config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

    If ($FatalError) {
        Write-Host "A fatal error has occurred, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}

    # Create virtual network
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop

    # Create services
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
                # Set up all the endpoints we'll need once we're up and running.
                # Note: Web traffic goes through the firewall, so we'll need to set up an HTTP endpoint.
                #       Also, the firewall will be redirecting web traffic to a new IP and port in a
                #       forwarding rule, so the HTTP endpoint here will have the same public and local
                #       port and the firewall will do the NATing and redirection as declared in the
                #       firewall rule.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: An SSH endpoint is automatically created on port 22 when the appliance is created.
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

      # Add NSG rules
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

        # Assign the NSG to the subnets
            Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

    # Optional Post-Script Manual Configuration
      # Configure firewall
      # Install test web app (run post-build script on the IIS server)
      # Install back-end resources (run post-build script on AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host
```

#### <a name="network-config-file"></a>Arquivo de configuração de rede
Salve esse arquivo XML com locais atualizados e, em seguida, adicionar um link para esse arquivo na variável $NetworkConfigFile no script anterior.

```xml
    <NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
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
Se você quiser instalar um aplicativo de exemplo para esse e outros exemplos de rede de perímetro, consulte a [script do aplicativo de exemplo][SampleApp].

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-asm/example2design.png "DMZ de entrada com NSG"
[2]: ./media/virtual-networks-dmz-nsg-fw-asm/dstnaticon.png "Ícone de NAT de destino"
[3]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallrule.png "Regra de Firewall"
[4]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallruleactivate.png "Ativação de regra de firewall"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
[Example1]: ./virtual-networks-dmz-nsg-asm.md

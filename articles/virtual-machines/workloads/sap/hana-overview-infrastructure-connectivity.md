---
title: "Infraestrutura e conectividade para o SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs"
description: "Configure a infraestrutura de conectividade necessária para usar SAP HANA no Azure (Instâncias Grandes)."
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 177627d8f72dbd04fb918ac7ece18321246a9c62
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="sap-hana-large-instances-infrastructure-and-connectivity-on-azure"></a>Infraestrutura e conectividade para o SAP HANA (instâncias grandes) no Azure 

Algumas definições prévias antes de ler este guia. Em [Visão geral e arquitetura do SAP HANA (instâncias grandes) no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture), apresentamos duas classes diferentes de unidades de Instância Grande do HANA com:

- S72, S72m, S144, S144m, S192 e S192m, que chamamos de “classe do Tipo I” de SKUs.
- S384, S384m, S384xm, S576, S768 e S960, que chamamos de “classe do Tipo II” de SKUs.

Os especificadores de classe serão usados em toda a documentação da Instância Grande do HANA para, em última análise, se referirem a diferentes funcionalidades e requisitos de acordo com os SKUs da Instância Grande do HANA.

Outras definições que usamos frequentemente são:
- **Grande carimbo da instância:** uma pilha de infraestrutura de hardware é TDI do SAP HANA certificados e dedicado para executar instâncias do SAP HANA no Azure.
- **SAP HANA no Azure (Instâncias Grandes):** nome oficial para a oferta no Azure para executar instâncias do HANA em hardware certificado por TDI do SAP HANA implantado em carimbos de Instância Grande em diferentes regiões do Azure. O termo relacionado **Instância Grande do HANA** é a abreviação de SAP HANA no Azure (Instâncias Grandes) e é amplamente usado neste guia de implantação técnica.
 

Após a compra do SAP HANA no Azure (Instâncias Grandes) ser finalizada entre você e a equipe de conta empresarial da Microsoft, as seguintes informações são exigidas pela Microsoft para implantar Unidades de Instâncias Grandes HANA:

- Nome do cliente
- Informações de contatos comerciais (incluindo endereço de email e número de telefone)
- Informações de contatos técnicos (incluindo endereço de email e número de telefone)
- Informações de contatos da rede técnica (incluindo endereço de email e número de telefone)
- Região de implantação do Azure (Oeste dos EUA, Leste dos EUA, Leste da Austrália, Sudeste da Austrália, Europa Ocidental e Europa Setentrional a partir de julho) 
- 2017)
- Confirmar SAP HANA no Azure (Instâncias Grandes) SKU (configuração)
- Como já detalhada no documento de visão geral e arquitetura do HANA instâncias grandes, para cada região do Azure que está sendo implantado:
    - Uma/29 intervalo de endereços IP para conexões de ER P2P que conectar redes virtuais do Azure para instâncias grandes HANA
    - Um bloco CIDR /24 usado para o Pool de IPs de Servidores de Instâncias Grandes HANA
- Os valores de intervalo de endereços IP usados no atributo do Espaço de Endereço da VNet de cada VNet do Azure que se conecta às Instâncias Grandes do HANA
- Dados para cada sistema de Instâncias Grandes HANA:
  - Nome de host desejado - o ideal é usar o nome de domínio totalmente qualificado.
  - Endereço IP desejado para a unidade de Instância Grande do HANA fora do intervalo de endereços do Pool IP do Servidor – tenha em mente que os 30 primeiros endereços IP no intervalo de endereços do Pool IP do Servidor são reservados para uso interno nas Instâncias Grandes do HANA
  - Nome do SID do SAP HANA da instância do SAP HANA (necessário para criar os volumes de disco relacionados ao SAP HANA necessários). O SID do HANA é necessário para criar as permissões para <sidadm> nos volumes NFS, que estão sendo anexados à unidade de Instância Grande do HANA. Ele também é usado como um dos componentes de nome dos volumes de disco que são montados. Se você desejar executar mais de uma instância do HANA na unidade, precisará listar vários SIDs do HANA. Cada uma obtém um conjunto separado de volumes atribuídos.
  - A groupid que o usuário hana-sidadm tem no sistema operacional Linux é necessária para criar os volumes de disco relacionados ao SAP HANA. A instalação do SAP HANA normalmente cria o grupo de sapsys com uma id de grupo de 1001. O usuário hana-sidadm faz parte do grupo
  - A userid que o usuário hana-sidadm tem no sistema operacional Linux é necessária para criar os volumes de disco relacionados ao SAP HANA. Se você estiver executando várias instâncias do HANA na unidade, precisará listar todos os usuários <sid>adm 
- ID de assinatura do Azure da assinatura do Azure na qual as Instâncias Grandes do HANA do SAP HANA no Azure serão conectadas diretamente. Essa ID de assinatura referencia a assinatura do Azure, que será cobrada com as unidades da Instância Grande do HANA.

Depois de fornecer as informações, a Microsoft provisiona SAP HANA no Azure (instâncias grandes) e retornará as informações necessárias para vincular suas redes virtuais do Azure para instâncias grandes HANA e acessar as unidades de instância grande HANA.

## <a name="connecting-azure-vms-to-hana-large-instances"></a>Conectar máquinas virtuais do Azure a Grandes Instâncias do HANA

Como já foi mencionado em [visão geral do SAP HANA (instâncias grandes) e arquitetura no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) a implantação mínima de instâncias grandes HANA com a camada de aplicativo SAP no Azure parece como:

![Rede virtual do Azure conectado ao SAP HANA no Azure (Instâncias Grandes) e local](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

Examinando melhor o lado da Rede Virtual do Azure, podemos perceber a necessidade de:

- A definição de uma VNet do Azure que será usada para implantar as VMs da camada de aplicativo do SAP.
- Que significa que a Rede Virtual do Azure, uma sub-rede padrão é definido automaticamente que é usada para implantar as VMs em.
- A Rede Virtual do Azure que é criado deve ter pelo menos uma sub-rede VM e uma sub-rede de Gateway de ExpressRoute. Essas sub-redes devem ser atribuídas aos intervalos de endereços IP, conforme especificado e abordado nas próximas seções.

Portanto, vamos examinar mais detalhadamente a criação de Rede Virtual do Azure para instâncias grandes HANA

### <a name="creating-the-azure-vnet-for-hana-large-instances"></a>Criação da Rede Virtual do Azure para instâncias grandes HANA

>[!Note]
>A Rede Virtual do Azure para instância grande HANA deve ser criada usando o modelo de implantação do Azure Resource Manager. Não há suporte para o modelo de implantação mais antigo do Azure, geralmente conhecido como modelo de implantação clássico, na solução Instância Grande do HANA.

A VNet pode ser criada usando o portal do Azure, o PowerShell, o modelo do Azure ou a CLI do Azure (consulte [Criar uma rede virtual usando o portal do Azure](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). No exemplo a seguir, analisamos uma VNet criada por meio do portal do Azure.

Se examinamos as definições de uma VNet do Azure pelo portal do Azure, vamos dar uma olhada em algumas das definições e como elas se relacionam ao que listamos como diferentes intervalos de endereços IP. Quando falamos sobre o **Espaço de Endereço**, queremos dizer o espaço de endereço que a VNet do Azure tem permissão para usar. Esse espaço de endereço também é o intervalo de endereços usado pela VNet para a propagação de rotas BGP. Esse **Espaço de Endereço** pode ser visto aqui:

![Espaço de Endereço da VNet do Azure exibido no portal do Azure](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

No caso anterior, com 10.16.0.0/16, a VNet do Azure recebeu um intervalo de endereços IP bem grande e amplo para ser usado. Significa que todos os intervalos de endereços IP das sub-redes subsequentes nessa rede virtual podem ter seus intervalos dentro desse ‘Espaço de Endereço'. Geralmente, não recomendamos tal um intervalo de endereços grande para a rede virtual único no Azure. Mas obter um passo adiante, vamos examinar as sub-redes definidas na Rede Virtual do Azure:

![Sub-redes de Rede Virtual do Azure e seus intervalos de endereços IP](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

Como você pode ver, examinamos uma VNet com uma primeira sub-rede VM (aqui chamado “padrão”) e uma sub-rede chamada “GatewaySubnet”.
Na seção a seguir, nos referimos ao intervalo de endereços IP da sub-rede, que foi chamado “padrão” nos gráficos como **intervalo de endereços IP da sub-rede VM do Azure**. Nas seções a seguir, nos referimos ao intervalo de endereços IP da sub-rede de Gateway como **intervalo de endereços IP de sub-rede de Gateway de rede virtual**. 

No caso do demonstrado acima de dois gráficos, verá que o **Espaço de Endereço de rede virtual** aborda ambos, o **intervalo de endereços IP de sub-rede de VM do Azure** e **intervalo de endereços IP de sub-rede de Gateway de rede virtual**. 

Em outros casos em que você precisa conservar ou ser específico com os intervalos de endereços IP, restrinja o **Espaço de Endereço da VNet** de uma VNet aos intervalos específicos que estão sendo usados por cada sub-rede. Nesse caso, você pode definir o **Espaço de Endereço de rede virtual** de uma rede virtual específica como vários intervalos como mostrado aqui:

![Espaço de Endereço de Rede Virtual do Azure com dois espaços](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

Nesse caso, o **Espaço de Endereço da VNet** tem dois espaços definidos. Esses dois espaços são idênticos aos intervalos de endereços IP definidos para o **intervalo de endereços IP da sub-rede VM do Azure** e o **intervalo de endereços IP da Sub-rede de Gateway da VNet**.

Você pode usar qualquer padrão de nomenclatura que desejar para essas sub-redes de locatário (sub-redes de VM). No entanto, **sempre deve haver uma, e somente uma, sub-rede de gateway para cada VNet** que se conecta ao circuito do ExpressRoute do SAP HANA no Azure (Instâncias Grandes). **Esta sub-rede de gateway sempre deve ser chamada “GatewaySubnet”** para garantir o posicionamento correto do gateway do ExpressRoute.

> [!WARNING] 
> É fundamental que a sub-rede de gateway seja sempre chamada “GatewaySubnet”.

Várias sub-redes VM podem ser usadas, até mesmo utilizando intervalos de endereços não contíguas. Mas como mencionado anteriormente, esses intervalos de endereços devem ser cobertos pelo **Espaço de Endereço de rede virtual** da rede virtual de forma agregada ou em uma lista dos intervalos exatas de sub-redes de VM e a sub-rede de gateway.

Resumindo o fato importante sobre uma Rede Virtual do Azure que se conecta ao HANA grandes instâncias:

- Você precisa enviar à Microsoft o **Espaço de Endereço da VNet** ao executar uma implantação inicial das Instâncias Grandes do HANA. 
- O **Espaço de Endereço de rede virtual** pode ser um intervalo maior que abrange o intervalo para o intervalo de endereços IP de sub-rede de VM do Azure e o intervalo de endereços IP de sub-rede de Gateway de rede virtual.
- Ou você pode enviar como **Espaço de Endereço de Rede Virtual** vários intervalos que abrangem o IP diferente endereços intervalos de intervalo de endereços IP de sub-rede VM e o intervalo de endereços IP de sub-rede de Gateway de rede virtual.
- O prefixo **Espaço de Endereço de rede virtual** é usada propagação de roteamento do BGP.
- O nome da sub-rede de Gateway deve ser: **“GatewaySubnet”**.
- O **Espaço de Endereço de rede virtual** é usado como um filtro no lado do HANA grande instância para permitir ou impedir que o tráfego para as unidades de instância grande HANA do Azure. Se as informações de roteamento BGP da VNet do Azure e os intervalos de endereços IP configurados para filtragem no lado da Instância Grande do HANA não forem correspondentes, poderão ocorrer problemas na conectividade.
- Há alguns detalhes sobre a sub-rede de Gateway que são discutidas mais para baixo na seção 'Conectando a uma rede virtual para ExpressRoute de instância grande HANA'



### <a name="different-ip-address-ranges-to-be-defined"></a>Diferentes intervalos de endereços IP a serem definidos 

Já apresentamos alguns intervalos de endereços IP necessários para implantar as Instâncias Grandes do HANA nas seções anteriores. Mas há mais alguns intervalos de endereços IP que são importantes. Vamos analisar alguns detalhes adicionais. Os seguintes endereços IP dos quais nem todos precisam ser enviadas para a Microsoft precisam ser definida antes de enviar uma solicitação para a implantação inicial:

- **Espaço de Endereço da VNet:** como já apresentamos anteriormente, é o intervalo de endereços IP atribuído (ou que se pretende atribuir) ao parâmetro do espaço de endereço nas VNets (Redes Virtuais) do Azure que se conectam ao ambiente da Instância Grande do SAP HANA. É recomendável que esse parâmetro do Espaço de Endereço seja um valor de várias linhas composto pelos intervalos da Sub-rede VM do Azure e pelo intervalo da sub-rede de Gateway do Azure, conforme mostrado no gráfico anterior. Esse intervalo não deve sobrepor seu local ou intervalos de endereços do Pool de IP do servidor ou ER P2P. Como obter esses intervalos de endereços IP? A equipe de rede corporativa ou o provedor de serviço deve fornecer um ou vários Intervalos de Endereços IP que não são usados dentro da rede. Exemplo: se a Sub-rede VM do Azure (veja acima) for 10.0.1.0/24 e a Sub-rede de Gateway do Azure (veja abaixo) for 10.0.2.0/28, é recomendável que o Espaço de Endereço da VNet do Azure tenha duas linhas: 10.0.1.0/24 e 10.0.2.0/28. Embora os valores de Espaço de Endereço possam ser agregados, é recomendável correspondê-los aos intervalos de sub-rede, a fim de evitar a reutilização acidental de intervalos de endereços IP não utilizados em espaços de endereço maiores no futuro em outro lugar da rede. **O Espaço de Endereço da VNET é um intervalo de endereços IP, que precisa ser enviado à Microsoft quando uma implantação inicial é solicitada**

- **Intervalo de endereços IP da sub-rede VM do Azure:** esse intervalo de endereços IP, conforme já discutido anteriormente, é aquele que você atribuiu (ou que pretende atribuir) ao parâmetro da sub-rede da VNet do Azure na VNet do Azure que se conecta ao ambiente da Instância Grande do SAP HANA. Este intervalo de endereços IP é usado para atribuir endereços IP para VMs do Azure. Os endereços IP fora desse intervalo têm permissão para se conectar aos servidores da Instância Grande do SAP HANA. Se necessário, podem ser usadas várias sub-redes de VM do Azure. Um bloco CIDR /24 é recomendado pela Microsoft para cada sub-rede de VM do Azure. Este intervalo de endereços deve ser uma parte dos valores usados no Espaço de Endereço de Rede Virtual do Azure. Como obter esse intervalo de endereços IP? A equipe de rede corporativa ou o provedor de serviço deve fornecer um Intervalo de Endereços IP que não estejam sendo usados atualmente na rede.

- **Intervalo de endereços IP de sub-rede de Gateway de rede virtual:** dependendo dos recursos que você planeja usar, o tamanho recomendado seria:
   - Gateway do ExpressRoute de ultra desempenho: bloco de endereço /26 – necessário para a classe do Tipo II de SKUs
   - Coexistência com VPN e ExpressRoute usando um Gateway de ExpressRoute de alto desempenho (ou menor): bloco de endereço /27
   - Todas as outras situações: bloco de endereço /28. Este intervalo de endereços deve ser uma parte dos valores usados nos valores de "Espaço de endereço de rede virtual". Este intervalo de endereços deve ser uma parte dos valores usados nos valores de Espaço de Endereço de Rede Virtual do Azure que você precisa para enviar à Microsoft. Como obter esse intervalo de endereços IP? A equipe de rede corporativa ou o provedor de serviço deve fornecer um Intervalo de Endereços IP que não estejam sendo usados atualmente na rede. 

- **Intervalo de endereços para conectividade do ER-P2P:** esse intervalo é o intervalo de IP da conexão P2P do ER (ExpressRoute) da Instância Grande do SAP HANA. Este intervalo de endereços IP deve ser um intervalo de endereços IP CIDR /29. Esse intervalo não deve sobrepor seu local ou outros intervalos de endereços IP do Azure. Esse intervalo de endereços IP é usado para configurar a conectividade do ER do Gateway do ExpressRoute da VNet do Azure com os servidores da Instância Grande do SAP HANA. Como obter esse intervalo de endereços IP? A equipe de rede corporativa ou o provedor de serviço deve fornecer um Intervalo de Endereços IP que não estejam sendo usados atualmente na rede. **Esse intervalo é um intervalo de endereços IP, que precisa ser enviado à Microsoft quando uma implantação inicial é solicitada**
  
- **O intervalo de endereços do Pool do IP do servidor:** este intervalo de endereços IP é usado para atribuir o endereço IP individual para servidores de instância grande HANA. O tamanho recomendado de sub-rede é um /24 CIDR bloquear - mas se necessária pode ser menor em um mínimo de fornecimento de 64 endereços IP. Nesse intervalo, os 30 primeiros endereços IP são reservados para uso pela Microsoft. Verifique se esse fato é considerado ao escolher o tamanho do intervalo. Esse intervalo não deve sobrepor seu local ou outro IP Azure endereços. Como obter esse intervalo de endereços IP? Seu provedor de serviço ou de equipe de rede corporativa deve fornecer um intervalo de endereços IP que não estejam em uso em sua rede. Um bloco CIDR /24 (recomendado) exclusivo para ser usado para atribuir os endereços IP específicos necessários ao SAP HANA no Azure (Instâncias Grandes). **Esse intervalo é um intervalo de endereços IP, que precisa ser enviado à Microsoft quando uma implantação inicial é solicitada**
 
Embora você precise definir e planejar os intervalos de endereços IP acima, nem todos eles precisam ser transmitidos à Microsoft. Para resumir o acima, os intervalos de endereços IP que é necessário nomear à Microsoft são:

- Espaços de Endereço de Rede Virtual do Azure
- Intervalo de endereços para conectividade de ER-P2P
- Intervalo de Endereços do Pool de IPs do Servidor

Adicionar redes virtuais adicionais que precisam se conectar a instâncias grandes HANA, exige que você enviar o novo Espaço de Endereço de Rede Virtual do Azure você está adicionando à Microsoft. 

Veja abaixo um exemplo dos diferentes intervalos e alguns intervalos de exemplo que você precisa configurar e, finalmente, fornecer à Microsoft. Como você pode ver, o valor do Espaço de Endereço da VNet do Azure não é agregado no primeiro exemplo, mas é definido com base nos intervalos do intervalo de endereços IP de sub-rede da primeira VM do Azure e do intervalo de endereços IP de Sub-rede de Gateway da VNet. Usar várias sub-redes VM na VNet do Azure funcione adequadamente, configurando e enviando o IP adicional endereço intervalos das sub-redes de VM adicionais como parte do Espaço de Endereço de Rede Virtual do Azure.

![Intervalos de endereços IP necessários na implantação mínima do SAP HANA no Azure (Instâncias Grandes)](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

Você também tem a possibilidade de agregar dados que enviar à Microsoft. Nesse caso, o Espaço de Endereço da VNet do Azure só incluirá um espaço. Usando os intervalos de endereços IP usados no exemplo anterior. Esse Espaço de endereço da VNet agregado poderá ser semelhante ao seguinte:

![Segunda possibilidade de intervalos de endereços IP necessários na implantação mínima do SAP HANA no Azure (Instâncias Grandes)](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

Como você pode ver acima, em vez de dois intervalos menores que definiu o Espaço de Endereço do VNet do Azure, temos um intervalo maior que abrange 4096 endereços IP. Uma definição grande do Espaço de Endereço deixa alguns grandes intervalos não utilizados. Como os valores de Espaço de Endereço de rede virtual são usados para a propagação de rotas BGP, uso de intervalos não usados em locais ou em outro lugar na sua rede pode causar problemas de roteamento. Portanto, é recomendável manter o Espaço de Endereço estreitamente alinhadas com o Espaço de Endereço de sub-rede real usado. Se necessário, sem incorrer em tempo de inatividade para a VNet, você pode sempre adicionar novos valores de Espaço de Endereço posteriormente.
 
> [!IMPORTANT] 
> Todos os intervalos de endereços IP do ER-P2P, do Pool IP do Servidor e do Espaço de Endereço de VNet do Azure **NÃO** devem se sobrepor uns aos outros ou a qualquer outro intervalo usado em outro lugar na rede; cada um deles deve ser discreto e, como os dois gráficos anteriores mostram, não podem ser uma sub-rede de nenhum outro intervalo. Se ocorrerem sobreposições entre intervalos, a Rede Virtual do Azure não poderá se conectar ao circuito de ExpressRoute.

### <a name="next-steps-after-address-ranges-have-been-defined"></a>Próximas etapas após a definição de intervalos de endereços
Após a definição de intervalos de endereços IP, as seguintes atividades precisam ocorrer:

1. Envie os intervalos de endereços IP para o Espaço de Endereço de Rede Virtual do Azure, a conectividade de ER P2P e o intervalo de endereços do Pool do IP do servidor, junto com outros dados que foi listados no início do documento. Neste momento, você também pode começar a criar a VNet e as Sub-redes VM. 
2. Um circuito do ExpressRoute é criado pela Microsoft entre sua assinatura do Azure e o carimbo de Instâncias Grandes HANA.
3. Uma rede de locatário é criada no carimbo instância grande pela Microsoft.
4. A Microsoft configura a rede na infraestrutura do SAP HANA no Azure (Instâncias Grandes) para aceitar os endereços IP do Espaço de Endereço da VNet do Azure que se comunicam com as Instâncias Grandes do HANA.
5. Dependendo do SKU específico do SAP HANA no Azure (Instâncias Grandes) adquirido, a Microsoft atribui uma unidade de computação em uma rede de locatário, aloca e monta o armazenamento e instala o sistema operacional (SUSE ou Red Hat Linux). Endereços IP para essas unidades são tirados o endereço de Pool de IP do servidor intervalo enviado à Microsoft.

No final do processo de implantação, a Microsoft fornece os seguintes dados para você:
- As informações necessárias para conectar suas Redes Virtuais do Azure ao circuito de ExpressRoute que conecta redes virtuais do Azure a instâncias grandes HANA:
     - Chaves de autorização
     - PeerID de ExpressRoute
- Os dados para acessar Instâncias Grandes HANA depois que você tiver estabelecido o circuito de ExpressRoute e a Rede Virtual do Azure.

Encontre também a sequência de conexão das Instâncias Grandes do HANA no documento [Instalação de ponta a ponta das Instâncias Grandes do SAP HANA](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/). Muitas das etapas a seguir são mostradas em uma implantação de exemplo nesse documento. 


## <a name="connecting-a-vnet-to-hana-large-instance-expressroute"></a>Conectar uma Rede Virtual a ExpressRoute de Instância Grande do HANA

Como você definiu todos os intervalos de endereço IP e agora tem os dados de volta da Microsoft, você pode começar a conectar a rede virtual que você criou antes do HANA grandes instâncias. Depois de criar a rede virtual do Azure, um gateway de ExpressRoute deve ser criado na rede virtual para vincular a rede virtual ao circuito de ExpressRoute que se conecta ao locatário do cliente no carimbo de instância grande.

> [!NOTE] 
> Esta etapa pode levar até 30 minutos para ser concluída, pois o novo gateway é criado na assinatura do Azure designada e depois conectado à rede virtual do Azure especificada.

Se já existir um gateway, verifique se ele é um gateway de ExpressRoute ou não. Caso contrário, o gateway deve ser excluído e recriado como um gateway de ExpressRoute. Se um gateway de ExpressRoute já estiver estabelecido, uma vez que a rede virtual do Azure já está conectada ao circuito de ExpressRoute para conectividade local, vá para a seção Vinculação de redes virtuais abaixo.

- Use o (novo) [portal do Azure](https://portal.azure.com/) ou o PowerShell para criar um gateway de VPN do ExpressRoute conectado à VNet.
  - Se você usar o portal do Azure, adicione um novo **Gateway de Rede Virtual** e, em seguida, selecione **ExpressRoute** como o tipo de gateway.
  - Se você escolher o PowerShell, primeiro baixe e use a versão mais recente do [SDK do Azure PowerShell](https://azure.microsoft.com/downloads/) para garantir uma experiência ideal. Os comandos a seguir criam um gateway de ExpressRoute. Os textos precedidos por um _$_ são variáveis definidas pelo usuário que precisam ser atualizadas com as informações específicas.

```PowerShell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA Large Instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzureRmVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

Neste exemplo, a SKU de gateway HighPerformance foi usada. As opções são HighPerformance ou UltraPerformance como as únicas SKUs de gateway que são suportadas para SAP HANA no Azure (Instâncias Grandes).

> [!IMPORTANT]
> Para Instâncias Grandes do HANA dos tipos de SKU S384, S384m, S384xm, S576, S768 e S960 (SKUs da classe do Tipo II), o uso do SKU de Gateway de UltraPerformance é obrigatório.

### <a name="linking-vnets"></a>Como vincular redes virtuais

Agora que a rede virtual do Azure tem um gateway de ExpressRoute, você usa as informações de autorização fornecidas pela Microsoft para conectar o gateway de ExpressRoute para o SAP HANA no circuito de ExpressRoute do Azure (Instâncias Grandes) criado para essa conectividade. Esse etapa pode ser realizada usando o portal do Azure ou o PowerShell. O portal é recomendável, porém, as instruções do PowerShell são descritas a seguir. 

- Execute os comandos a seguir para cada gateway de VNet usando um AuthGUID diferente para cada conexão. As duas primeiras entradas mostradas no script a seguir foram obtidas das informações fornecidas pela Microsoft. Além disso, o AuthGUID é específico para cada rede virtual e seu gateway. Isso significa que, se você desejar adicionar outra VNet do Azure, precisará obter outra AuthID para o circuito do ExpressRoute que conecta as Instâncias Grandes do HANA no Azure. 

```PowerShell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway Information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzureRmVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

Se desejar conectar o gateway a vários circuitos do ExpressRoute associados à sua assinatura, talvez você precise realizar essa etapa mais de uma vez. Por exemplo, provavelmente, você conectará o mesmo Gateway de VNet ao circuito do ExpressRoute que conecta a VNet à rede local.

## <a name="adding-more-ip-addresses-or-subnets"></a>Como adicionar mais endereços IP ou sub-redes

Use o portal do Azure, o PowerShell ou a CLI ao adicionar mais endereços IP ou sub-redes.

Nesse caso, a recomendação é adicionar o novo intervalo de endereços IP como um novo intervalo ao Espaço de Endereço da VNet, em vez de gerar um novo intervalo agregado. Em ambos os casos, você precisa enviar essa alteração para a Microsoft para permitir a conectividade fora desse novo intervalo de endereços IP para as unidades de instância grande HANA no seu cliente. Abra uma solicitação de suporte do Azure para obter o novo espaço de endereço da VNet adicionado. Após receber a confirmação, realize as próximas etapas.

Para criar uma sub-rede adicional no portal do Azure, confira o artigo [Criar uma rede virtual usando o portal do Azure](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e para criar a partir do PowerShell, confira [Criar uma rede virtual usando o PowerShell](../../../virtual-network/virtual-networks-create-vnet-arm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="adding-vnets"></a>Como adicionar redes virtuais

Depois de conectar inicialmente uma ou mais redes virtuais do Azure, talvez você queira adicionar mais que acessam SAP HANA no Azure (Instâncias Grandes). Primeiro, envie uma solicitação de suporte do Azure. Nessa solicitação inclua as informações específicas identificando a implantação específica do Azure e os intervalos de Espaço de Endereço IP para do Espaço de Endereço de Rede Virtual do Azure. O SAP HANA no Gerenciamento de Serviços do Azure fornece as informações necessárias que você precisa para conectar as redes virtuais adicionais e a ExpressRoute. Para cada VNet, você precisa de uma Chave de Autorização exclusiva para se conectar ao Circuito do ExpressRoute nas Instâncias Grandes do HANA.

Etapas para adicionar uma nova rede virtual do Azure:

1. Conclua a primeira etapa do processo de integração e consulte a seção _Criando uma VNet do Azure_.
2. Conclua a segunda etapa do processo de integração e consulte a seção _Criando uma sub-rede de gateway_.
3. Para conectar VNets adicionais ao circuito do ExpressRoute da Instância Grande do HANA, abra uma solicitação de suporte do Azure com informações sobre a nova VNet e solicite uma nova Chave de Autorização.
4. Quando notificado de que a autorização foi concluída, use as informações de autorização fornecidas pela Microsoft para concluir a terceira etapa do processo de integração e consulte a seção _Vinculando VNets_.

## <a name="increasing-expressroute-circuit-bandwidth"></a>Como aumentar a largura de banda do circuito de ExpressRoute

Consulte com o SAP HANA no Gerenciamento de Serviços do Azure. Se você for aconselhado a aumentar a largura de banda do SAP HANA no circuito de ExpressRoute do Azure (Instâncias Grandes), crie uma solicitação de suporte do Azure. (Você pode solicitar um aumento para uma largura de banda do circuito único até um máximo de 10 Gbps). Em seguida você receberá uma notificação após a operação ser concluída. Nenhuma ação adicional é necessária para habilitar essa velocidade superior no Azure.

## <a name="adding-an-additional-expressroute-circuit"></a>Como adicionar um circuito de ExpressRoute adicional

Consulte com o SAP HANA no Gerenciamento de Serviços do Azure, se você estiver ciente de que um circuito de ExpressRoute adicional é necessário. Crie uma solicitação de suporte do Azure para criar um novo circuito de ExpressRoute (e para obter informações de autorização para se conectar a ela). O espaço de endereço que será usado nas VNet deve ser definido antes de fazer a solicitação, para que o Gerenciamento de Serviços do SAP HANA no Azure forneça a autorização.

Depois que o novo circuito for criado e a configuração do Gerenciamento de Serviços do SAP HANA no Azure for concluída, você receberá uma notificação com as informações necessárias para continuar. Siga as etapas fornecidas acima para criar e conectar-se à nova rede virtual para este circuito adicional. Você não poderá se conectar às VNets do Azure com esse circuito adicional se elas já estiverem conectadas a outro circuito do ExpressRoute do SAP HANA no Azure (Instância Grande) na mesma região do Azure.

## <a name="deleting-a-subnet"></a>Como excluir uma sub-rede

Para remover uma sub-rede da VNet, o portal do Azure, o PowerShell ou a CLI podem ser usados. Caso o IP de Rede Virtual do Azure endereço intervalo/Azure Espaço de Endereço de rede virtual foi um intervalo agregado, não há nenhum acompanhamento para você com a Microsoft. Exceto se a VNet ainda estiver propagando o espaço de endereço da rota BGP que inclui a sub-rede excluída. Se você tiver definido o IP de Rede Virtual do Azure endereço intervalo/Azure Espaço de Endereço de rede virtual como vários intervalos de endereços IP que um deles foi atribuído à sua sub-rede excluído, você deve excluir que fora do seu Espaço de Endereço de rede virtual e informar subsequentemente SAP HANA em gerenciamento de serviços do Azure para removê-lo do SAP HANA no Azure (instâncias grandes) tem permissão para se comunicar com os intervalos.

Enquanto ainda não for específico, as diretrizes dedicadas no Azure.com sobre a remoção de sub-redes, o processo de remoção de sub-redes é o inverso do processo para adicioná-los. Consulte o artigo [Criar uma rede virtual usando o portal do Azure](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obter mais informações sobre como criar sub-redes.

## <a name="deleting-a-vnet"></a>Como excluir uma rede virtual

Use o portal do Azure, o PowerShell ou a CLI ao excluir uma VNet. O SAP HANA no Gerenciamento de Serviços do Azure remove as autorizações existentes no SAP HANA no circuito de ExpressRoute do Azure (Instâncias Grandes) e remove o intervalo de endereços IP de Rede Virtual do Aure/Espaço de Endereço de Rede Virtual do Aure para a comunicação com Instâncias Grandes HANA.

Depois que a rede virtual tiver sido removida, abra uma solicitação de suporte do Azure para fornecer os intervalos de endereço IP a serem removidos.

Enquanto ainda não for específico, as diretrizes dedicadas no Azure.com sobre a remoção de redes virtuais, o processo de remoção de redes virtuais é o inverso do processo para adicioná-los, que é descrito acima. Confira os artigos [Criar uma rede virtual usando o portal do Azure](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e [Criar uma rede virtual usando o PowerShell](../../../virtual-network/virtual-networks-create-vnet-arm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obter mais informações sobre a criação de redes virtuais.

Para garantir que tudo seja removido, exclua os itens a seguir:

- A conexão do ExpressRoute, o Gateway da VNet, o IP Público do Gateway de VNet e a VNet

## <a name="deleting-an-expressroute-circuit"></a>Como excluir um circuito de ExpressRoute

Para remover um circuito adicional do ExpressRoute do SAP HANA no Azure (Instâncias Grandes), abra uma solicitação de suporte do Azure com o Gerenciamento de Serviços do SAP HANA no Azure e solicite a exclusão do circuito. Na assinatura do Azure, você pode excluir ou manter a rede virtual conforme necessário. No entanto, você deve excluir a conexão entre o circuito de ExpressRoute de instâncias grandes HANA e o gateway de rede virtual vinculado.

Se você também quiser remover uma rede virtual, siga as diretrizes sobre Como excluir uma rede virtual na seção acima.



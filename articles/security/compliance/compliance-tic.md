---
title: Diretrizes de Conexão Confiável com a Internet para o Azure
description: Diretrizes de Conexão Confiável com a Internet para o Azure e serviços SaaS
services: security
author: dlapiduz
ms.assetid: 09511e03-a862-4443-81ac-ede815bdaf25
ms.service: security
ms.topic: article
ms.date: 06/20/2018
ms.author: dlap
ms.openlocfilehash: bb186ab2700b147bee3a7dd81474409ccafb76fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60608093"
---
# <a name="trusted-internet-connections-guidance"></a>Diretrizes de Conexão Confiável com a Internet

Este artigo aborda como as agências governamentais podem usar o Microsoft Azure Governamental para ajudar a alcançar a conformidade com iniciativa de Conexão Confiável com a Internet (TIC). O artigo descreve como usar o Microsoft Azure Governamental em toda a infraestrutura do Azure como um serviço (IaaS) e a plataforma do Azure como uma oferta de serviço (PaaS).

## <a name="trusted-internet-connections-overview"></a>Visão geral de Conexão Confiável com a Internet

A finalidade da iniciativa TIC é otimizar e padronizar a segurança das conexões de rede externas individuais atualmente em uso por agências federais. A política é descrita no [memorando M-08-05](https://georgewbush-whitehouse.archives.gov/omb/memoranda/fy2008/m08-05.pdf) do OMB (Departamento de Gerenciamento e Orçamento).

Em novembro de 2007, o OMB estabeleceu o programa TIC para melhorar a segurança de perímetro de rede federal e as funções de resposta a incidentes. O TIC foi projetado para executar a análise de rede de todo o tráfego .gov de entrada e saída para identificar assinaturas e dados baseados em padrão específicos. O TIC revela anomalias comportamentais, como atividade botnet. Agências são obrigatórias para consolidar suas conexões de rede externa e rotear todo o tráfego por meio de dispositivos de detecção e prevenção de invasão conhecidos como EINSTEIN. Os dispositivos são hospedados em um número limitado de pontos de extremidade de rede, que são denominados _conexões de internet confiáveis_.

O objetivo do TIC é que as agências saibam:
- Quem está em minha rede (autorizado ou não autorizado)?
- Quando minha rede é acessada e por quê?
- Quais recursos estão são acessados?

Todas as conexões externas das agências precisam ser encaminhadas por meio de um TIC aprovado pelo OMB. As agências federais devem participar do programa TIC como um TICAP (Provedor de Acesso TIC) ou contratando serviços com um dos principais provedores de serviço de internet de camada 1. Esses provedores são chamados de provedores do Serviço de Protocolo de Internet Confiável Gerenciada (MTIPS). O TIC inclui funcionalidades críticas obrigatórias que são executadas pela agência e pelo provedor MTIPS. Na versão atual do TIC, a detecção de intrusão EINSTEIN versão 2 e os dispositivos de prevenção de intrusão acelerada (3A) EINSTEIN versão 3 são implantados em cada TICAP e MTIPS. A agência estabelece um "Memorando de Acordo" com O Department of Homeland Security (DHS) para implantar recursos EINSTEIN em sistemas federais.

Como parte de sua responsabilidade em proteger a rede .gov, o DHS exige que os feeds de dados brutos dos dados de Netflow da agência correlacionem os incidentes em toda a empresa federal e realize análises usando ferramentas especializadas. Os roteadores do DHS fornecem a capacidade de coletar o tráfego de rede IP conforme ele entra ou sai de uma interface. Os administradores de rede podem determinar aspectos como a origem e o destino do tráfego, a classe de serviço e assim por diante. Dados de fluxo de líquido são considerados como "dados que não são de conteúdo" semelhante para o cabeçalho, IP de origem, IP de destino e assim por diante. Dados de conteúdo permitem que o DHS saiba mais sobre o conteúdo: quem estava fazendo o que e por quanto tempo.

A iniciativa também inclui políticas de segurança, diretrizes e estruturas que supõem a infraestrutura local. À medida que as agências governamentais movem para a nuvem para alcançar as economias de custo, a eficiência operacional e a inovação, os requisitos de implementação de TÍC podem reduzir a velocidade do tráfego de rede. A velocidade e agilidade com quais os usuários do governo podem acessar seus dados baseados em nuvem é limitada como resultado.

## <a name="azure-networking-options"></a>Resumo das opções de rede do Azure

Há três opções principais ao se conectar aos serviços do Azure:

- Conexão direta com a internet: Conecte-se aos serviços do Azure diretamente por meio de uma conexão aberta com a Internet. A mídia e a conexão são públicos. As criptografias no nível do aplicativo e do transporte são utilizadas para garantir a privacidade. A Largura de Banda é limitada pela conectividade de um site com a internet. Use mais de um provedor ativo para garantir a resiliência.
- Rede Virtual Privada (VPN): Conecte-se à Rede Virtual do Azure com privacidade usando um gateway de VPN.
A mídia é pública, pois atravessa a conexão padrão com a Internet de um site, mas a conexão é criptografada em um túnel para garantir a privacidade. A largura de banda é limitada, dependendo dos dispositivos VPN e da configuração escolhida. As conexões ponto a site do Azure são normalmente limitadas a 100 Mbps e as conexões site a site são limitadas a 1,25 Gbps.
- Microsoft Azure ExpressRoute: O ExpressRoute é uma conexão direta aos serviços da Microsoft. Uma vez que a conectividade é estabelecida por um canal de fibra isolado, a conexão pode ser pública ou privada, dependendo da configuração usada. A largura de banda geralmente é limitada a um máximo de 10 Gbps.

Há várias maneiras de atender aos requisitos do Apêndice H (Considerações sobre a nuvem), conforme especificado no "Documento da Arquitetura de Referência do TIC (Conexões Confiáveis com a Internet) versão 2.0" do Departamento de Segurança Nacional. Neste artigo, orientações DHS TIC são referidas como **TIC 2.0**.

Para permitir a conexão do **Departamento ou da Agência (D/A)** com o Azure ou o Office 365, sem o tráfego de roteamento por meio do TIC do D/A, o D/A precisa usar um túnel criptografado e/ou uma conexão dedicada com o CSP (Provedor de Serviços de Nuvem). Os serviços do CSP podem garantir que a conectividade com os ativos de nuvem do D/A e que eles não sejam oferecidos para a Internet pública para o acesso de funcionários diretos da agência.

O Office O365 está em conformidade com o TIC 2.0 Apêndice H usando o ExpressRoute com o [Emparelhamento da Microsoft](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings) habilitado ou uma conexão com a Internet que criptografa todo o tráfego usando o TLS 1.2. Os usuários finais de D/A na rede de D/A podem se conectar por meio da rede e da infraestrutura de TIC de sua agência pela Internet. Todo o acesso remoto à Internet ao Office 365 é bloqueado e encaminhado por meio da agência. O D/A també pode se conectar ao Office 365 por uma conexão do ExpressRoute com o emparelhamento da Microsoft (tipo de emparelhamento Público) habilitado.  

Somente para o Azure, a segunda opção (VPN) e a terceira opção (ExpressRoute) podem atender a esses requisitos quando elas são usadas em conjunto com serviços que limitam o acesso à Internet.

![Conexão confiável com a Internet da Microsoft (TIC)](media/tic-diagram-a.png)

## <a name="azure-infrastructure-as-a-service-offerings"></a>Ofertas de infraestrutura como serviço do Microsoft Azure

A conformidade com a política do TIC usando o IaaS do Azure é relativamente simples, já que os clientes do Azure gerenciam seu próprio roteamento de rede virtual.

O principal requisito para ajudar a garantir a conformidade com a arquitetura de referência do TIC é garantir que a rede virtual se torne uma extensão particular da rede do D/A. Para se tornar uma extensão _particular_, a política exige que nenhum tráfego saia de sua rede, exceto por meio da conexão de rede TIC local. Este processo é conhecido como _túnel de força_. Para a conformidade do TIC, é o processo de roteamento de todo o tráfego de qualquer sistema no ambiente do CSP para que ele passe por um gateway local na rede de uma organização para a Internet por meio do TIC.

A conformidade do TIC no IaaS do Azure é dividida em duas etapas principais:

- Etapa 1: Configuração.
- Etapa 2: Auditoria.

### <a name="azure-iaas-tic-compliance-configuration"></a>Conformidade do TIC IaaS do Azure: Configuração

Para configurar uma arquitetura em conformidade com o TIC com o Azure, você precisa primeiro impedir o acesso direto à Internet à sua rede virtual e, em seguida, forçar o tráfego da Internet por meio da rede local.

#### <a name="prevent-direct-internet-access"></a>Impedir o acesso direto à Internet

A rede de IaaS do Azure é realizada por meio de redes virtuais, compostas por sub-redes, às quais os NICs (Controladores de Adaptador de Rede) das Máquinas Virtuais estão associadas.

O cenário mais simples para dar suporte à conformidade do TIC é garantir que uma Máquina Virtual, ou uma coleção de máquinas virtuais, não possa se conectar aos recursos externos. Garanta a desconexão de redes externas usando grupos de segurança de rede (NSGs). Use NSGs para controlar o tráfego para um ou mais NICs ou sub-redes em sua rede virtual. Um NSG contém regras de controle de acesso que permitem ou negam o tráfego com base na direção do tráfego, no protocolo, no endereço e porta de origem e no endereço e porta de destino. É possível pode alterar as regras de um NSG a qualquer momento e as alterações se aplicam a todas as instâncias associadas. Para saber mais sobre como criar um NSG, consulte [Tráfego de rede de filtro com um grupo de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal).

#### <a name="force-internet-traffic-through-an-on-premises-network"></a>Forçar o tráfego da Internet por meio da rede local

O Azure cria rotas de sistema automaticamente e as atribui a cada sub-rede em uma rede virtual. Não é possível criar ou remover rotas de sistema, mas é possível substituir algumas rotas de sistema por rotas personalizadas. O Azure rotas do sistema padrão para cada sub-rede. O Azure adiciona rotas padrão opcionais para sub-redes específicas ou todas as sub-redes ao usar recursos específicos do Azure. Esse tipo de roteamento garante:
- O tráfego que estiver destinado dentro da rede virtual permanece dentro da rede virtual.
- Espaços de endereço privado designado pelo IANA como 10.0.0.0/8 são descartados, a menos que estejam incluídos no espaço de endereço de rede virtual.
- Roteamento de "último recurso" de 0.0.0.0/0 para o ponto de extremidade de internet de rede virtual.

![Túnel forçado do TIC](media/tic-diagram-c.png)

Todo o tráfego que sai da Rede Virtual precisa ser roteado por meio da conexão Local para garantir que todo o tráfego atravesse o TIC do D/A. Crie rotas personalizadas criando rotas definidas pelo usuário ou trocando rotas do BGP (Border Gateway Protocol) entre o gateway de rede local e um gateway VPN do Azure. Para saber mais sobre rotas definidas pelo usuário, consulte [Roteamento de tráfego de rede virtual: Rotas definidas pelo usuário](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined). Para saber mais sobre BGP, consulte [Roteamento de tráfego de rede virtual: Border Gateway Protocol](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol).

#### <a name="add-user-defined-routes"></a>Adicionar rotas definidas pelo usuário

Se você usar um gateway de rede virtual baseada em rota, você pode forçar o túnel no Azure. Adicione uma rota definida pelo usuário (UDR) que define o tráfego de 0.0.0.0/0 para rotear para um **próximo salto** do seu gateway de rede virtual. O Azure prioriza rotas definidas pelo usuário em relação a rotas definidas pelo sistema. Todo o tráfego de rede não virtual é enviado para seu gateway de rede virtual, que então pode rotear o tráfego para o local. Depois de definir a UDR, associe a rota de sub-redes existentes ou novas sub-redes dentro de todas as redes virtuais no ambiente do Azure.

![Rotas definidas pelo usuário e TIC](media/tic-diagram-d.png)

#### <a name="use-the-border-gateway-protocol"></a>Use o Border Gateway Protocol

Se você estiver usando o Microsoft Azure ExpressRoute ou um gateway de rede virtual habilitado para um BGP, o BGP é o mecanismo preferido para anunciar as rotas. Para uma rota anunciada pelo BGP igual a 0.0.0.0/0, os Gateways de rede birtual com reconhecimento do ExpressRoute e do BGP garantirão que essa rota padrão seja aplicada a todas as sub-redes nas redes virtuais.

### <a name="azure-iaas-tic-compliance-auditing"></a>Conformidade do TIC IaaS do Azure: Auditoria

O Azure oferece várias maneiras de auditar a conformidade do TIC.

#### <a name="view-effective-routes"></a>Exibir rotas efetivas

Confirme que a rota padrão seja propagada, observando as _rotas efetivas_ para uma máquina virtual específica, uma NIC específica ou uma tabela de rotas definidas pelo usuário no [portal do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-portal#diagnose-using-azure-portal) ou no [ O Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-powershell#diagnose-using-powershell). As **Rotas Efetivas** mostram as rotas relevantes definidas pelo usuário, rotas anunciadas pelo BGP, e rotas de sistema que se aplicam à entidade relevante, conforme exibido na figura a seguir:

![Rotas efetivas](media/tic-screen-1.png)

> [!NOTE]
> Não é possível exibir as rotas efetivas para uma NIC, a menos que ela esteja associada a uma máquina virtual em execução.

#### <a name="use-azure-network-watcher"></a>Use o Observador de Rede do Azure

O Observador de Rede do Azure oferece várias ferramentas para auditar a conformidade do TIC. Para obter mais informações, confira [esta visão geral sobre o Observador de Rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).

##### <a name="capture-network-security-group-flow-logs"></a>Capturar os logs do fluxo de grupo de segurança de rede 

Use o Observador de Rede para capturar os logs de fluxo de rede que indicam os metadados ao redor de tráfego IP. Os logs de fluxo de rede contêm os endereços de origem e de destino dos destinos e outros dados. Você pode usar esses dados com os logs do gateway de rede virtual, dispositivos ou TIC, para monitorar todas as rotas de tráfego locais. 

## <a name="azure-platform-as-a-service-offerings"></a>Plataforma do Azure como ofertas de serviço

Os serviços de PaaS do Azure como o Armazenamento do Microsoft Azure podem ser acessados por uma URL acessível pela Internet. Qualquer pessoa com credenciais aprovadas pode acessar o recurso, como uma conta de armazenamento, em qualquer local sem atravessar o TIC. Por esse motivo, muitos clientes governamentais concluem incorretamente que os serviços de PaaS do Azure não estão em conformidade com as políticas TIC. Muitos serviços de PaaS do Azure podem estar em conformidade com a política TÍQ. Um serviço está em conformidade quando a arquitetura é o mesma que o ambiente de IaaS em conformidade com o ambiente IaaS em conformidade com o TIC ([conforme descrito anteriormente](https://docs.microsoft.com/azure/security/compliance/compliance-tic#azure-infrastructure-as-a-service-offerings)) e o serviço está conectado a uma rede virtual do Azure.

Quando os serviços de PaaS do Azure são integrados com à uma rede virtual, o serviço está acessível de forma particular a partir da rede virtual. Você pode aplicar o roteamento personalizado para 0.0.0.0/0 por meio de rotas definidas pelo usuário ou BGP. O roteamento personalizado garante que todo o tráfego direcionado às rotas locais atravessem o TIC. Integre os serviços do Azure em redes virtuais usando os seguintes padrões:

- **Implantar uma instância dedicada de um serviço**: Um número cada vez maior de serviços de PaaS pode ser implantado como instâncias dedicadas com pontos de extremidade anexados à rede virtual. Você pode implantar um ambiente de Serviço de Aplicativo do Azure para PowerApps no modo de "Isolado" para permitir que o ponto de extremidade de rede seja restrito a uma rede virtual. O ambiente do Serviço de Aplicativo, em seguida, pode hospedar vários serviços de PaaS do Azure, como aplicativos Web do Azure, o Gerenciamento de API do Azure e o Azure Functions.
- **Usar pontos de extremidade de serviço de rede virtual**: Um número cada vez maior de serviços de PaaS permitem a opção de mover seu ponto de extremidade para um IP privado da VNET, em vez de para um endereço público.

Serviços que dão suporte à implantação de instâncias dedicadas em uma rede virtual ou o uso de pontos de extremidade de serviço, a partir de maio de 2018, estão listados nas tabelas a seguir.

> [!Note]
> O status de disponibilidade corresponde aos serviços do Azure que estão disponíveis no mercado. O status de disponibilidade para serviços do Azure no Azure Governamental está pendente.

### <a name="support-for-service-endpoints"></a>Por exemplo, pontos de extremidade do serviço

|Serviço                        |Disponibilidade      |
|-------------------------------|------------------|
|Cofre da Chave do Azure                | Visualização particular  |
|Azure Cosmos DB                | Visualização particular  |
|Serviços de identidade              | Visualização particular  |
|Azure Data Lake                | Visualização particular  |
|Banco de Dados do Azure para PostgreSQL  | Visualização particular  |
|Banco de Dados do Azure para MySQL       | Visualização particular  |
|SQL Data Warehouse do Azure       | Versão prévia pública   |
|Banco de Dados SQL do Azure             | Disponibilidade Geral (GA) |
|Armazenamento do Azure                  | GA               |

### <a name="support-for-virtual-network-injection"></a>Suporte para a injeção de rede virtual

|Serviço                               |Disponibilidade      |
|--------------------------------------|------------------|
|Instância Gerenciada do Banco de Dados SQL do Azure   | Versão prévia pública   |
|AKS (Serviço do Kubernetes do Azure)        | Versão prévia pública   |
|Azure Service Fabric                  | GA               |
|Gerenciamento de API do Azure                  | GA               |
|Azure Active Directory                | GA               |
|Lote do Azure                           | GA               |
|Ambiente do Serviço de Aplicativo               | GA               |
|Cache Redis do Azure                     | GA               |
|Azure HDInsight                       | GA               |
|Conjunto de escala de máquina virtual             | GA               |
|Serviços de nuvem do Azure                  | GA               |


### <a name="virtual-network-integration-details"></a>Detalhes da integração de rede virtual

O diagrama a seguir mostra o fluxo geral de rede para acesso aos serviços de PaaS. O acesso é mostrado a partir da injeção de rede virtual e o túnel de serviço de rede virtual. Para obter mais informações sobre gateways de serviço de rede, redes virtuais e marcas de serviço, consulte [Rede e grupos de segurança de aplicativo: marcas de serviço: Marcas de serviço](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

![Opções de conectividade PaaS para o TIC](media/tic-diagram-e.png)

1. A conexão particular é feita para o Azure usando o ExpressRoute. O emparelhamento privado do ExpressRoute com túnel forçado é usado para forçar todo o tráfego da rede virtual do cliente pelo ExpressRoute novamente para o local. O Emparelhamento da Microsoft não é necessário.
2. O Gateway de VPN do Azure usado em conjunto com o emparelhamento do Microsoft ExpressRoute pode ser usado para sobrepor a criptografia de IPSec de ponta a ponta entre a rede virtual do cliente e a borda local. 
3. A conectividade de rede com a rede virtual do cliente é controlada usando NSG (Grupos de Segurança de Rede) permitindo que os clientes concedam permissões/negações com base no IP, na porta e no protocolo.
4. A rede virtual do cliente é estendida para o serviço de PaaS, criando um ponto de extremidade de serviço para o serviço do cliente.
5. O ponto de extremidade de serviço de PaaS é **protegido para negar tudo** por padrão e permitir o acesso somente de sub-redes especificadas dentro da rede virtual do cliente. A negação padrão também inclui conexões provenientes da Internet.
6. Outros serviços do Azure que precisam acessar recursos na rede virtual do cliente devem ser:  
   - Implantado diretamente na rede virtual.
   - Permitido seletivamente com base nas diretrizes do respectivo serviço do Azure.

#### <a name="option-a-deploy-a-dedicated-instance-of-a-service-virtual-network-injection"></a>Opção A: Implantar uma instância dedicada de um serviço (injeção de rede virtual)

A injeção de rede virtual permite que os clientes implantem seletivamente instâncias dedicadas de determinado serviço do Azure, como o HDInsight, em sua própria rede virtual. Instâncias de serviço são implantadas em uma sub-rede dedicada na rede virtual de um cliente. A injeção de rede virtual permite acesso aos recursos de serviço por meio de endereços não roteáveis para a Internet. As instâncias locais usam ExpressRoute ou VPN site a site para acessar diretamente instâncias de serviço através do espaço de endereço de rede virtual, em vez de abrir firewalls no espaço de endereço da Internet pública. Quando uma instância dedicada é anexada a um ponto de extremidade, você pode usar as mesmas estratégias para conformidade de IaaS TIC. O roteamento padrão garante que o tráfego direcionado para a internet seja redirecionado para um gateway de rede virtual que está associado para o local. Você pode controlar melhor o acesso de entrada e saída por meio de NSGs para determinada sub-rede.

![Visão geral de injeção da rede virtual](media/tic-diagram-f.png)

#### <a name="option-b-use-virtual-network-service-endpoints-service-tunnel"></a>Opção B: Usar pontos de extremidade de serviço de rede virtual (túnel de serviço)

Um número crescente de serviços de vários locatários do Azure oferece "pontos de extremidade de serviço." Pontos de extremidade de serviço são um método alternativo para a integração de redes virtuais do Azure. Os pontos de extremidade de serviço de rede virtual estendem o espaço de endereço privado e a identidade da sua rede virtual para os serviços do Azure por meio de uma conexão direta. O tráfego da rede virtual para o serviço do Azure sempre permanece dentro da rede de backbone do Azure. 

Depois de habilitar um ponto de extremidade de serviço para um serviço, use políticas expostas pelo serviço para restringir conexões para o serviço de rede virtual. Verificações de acesso são impostas na plataforma pelo serviço do Azure. O acesso a um recurso bloqueado é concedido somente se a solicitação se origina da sub-rede ou rede virtual permitida ou dos dois IPs que são usados para identificar o tráfego local se você usar o ExpressRoute. Use este método para impedir efetivamente que o tráfego de Entrada/Saída saia diretamente do Serviço de PaaS.

![Visão geral dos pontos de extremidade do serviço](media/tic-diagram-g.png)

## <a name="cloud-native-tools-for-network-situational-awareness"></a>Ferramentas nativas de nuvem para o reconhecimento situacional de rede

O Azure fornece ferramentas nativas de nuvem para ajudar a garantir que você tenha o reconhecimento situacional necessário para entender os fluxos de tráfego da rede. As ferramentas não são necessárias para conformidade com a política de TIC. As ferramentas podem melhorar muito os recursos de segurança.

### <a name="azure-policy"></a>Azure Policy

[Azure Policy](../../governance/policy/overview.md) é um serviço do Azure que oferece à sua organização uma melhor capacidade de auditar e impor as iniciativas de conformidade. Os clientes podem planejar e testar suas regras do Azure Policy agora para garantir a conformidade de TIC futuras.

A Azure Policy destina-se no nível da assinatura. O serviço fornece uma interface centralizada em que você pode executar tarefas de conformidade, incluindo:
- Gerenciar iniciativas
- Configurar as definições de políticas
- Auditoria de conformidade
- Impor a conformidade
- Gerenciar exceções

Junto com muitas definições internas, os administradores podem definir suas próprias definições personalizadas usando modelos JSON simples. A Microsoft recomenda a priorização de auditoria ao longo de imposição, sempre que possível.

As seguintes políticas de exemplo podem ser usadas para cenários de conformidade do TIC:

|Política  |Cenário de exemplo  |Modelo  |
|---------|---------|---------|
|Impor a tabela de rotas definidas pelo usuário. | Garantir que a rota padrão em todos os pontos de redes virtuais seja direcionada a um Gateway de rede virtual aprovada para o roteamento local.    | Introdução com este [modelo](../../governance/policy/samples/no-user-defined-route-table.md). |
|Auditar se o Observador de Rede não está habilitado para a região.  | Garantir que o Observador de Rede esteja habilitado para todas as regiões usadas.  | Introdução com este [modelo](../../governance/policy/samples/network-watcher-not-enabled.md). |
|NSG X em cada sub-rede.  | Garantir que um NSG (ou um conjunto de NSGs aprovados) com o tráfego da Internet bloqueado seja aplicado a todas as sub-redes em cada rede virtual. | Introdução com este [modelo](../../governance/policy/samples/nsg-on-subnet.md). |
|NSG X em cada NIC. | Garantir que um NSG com o tráfego da Internet bloqueado seja aplicado a todas as NICs em todas as máquinas virtuais. | Introdução com este [modelo](../../governance/policy/samples/nsg-on-nic.md). |
|Use uma rede virtual aprovada para as interfaces de rede de máquina virtual.  | Garantir que todas as NICs estejam em uma rede virtual aprovada. | Introdução com este [modelo](../../governance/policy/samples/use-approved-vnet-vm-nics.md). |
|Locais permitidos. | Garantir que todos os recursos sejam implantados em regiões com uma configuração de redes virtuais e do Observador de Rede em conformidade.  | Introdução com este [modelo](../../governance/policy/samples/allowed-locations.md). |
|Tipos de recurso não permitidos, como **PublicIPs**. | Proibir a implantação de tipos de recurso que não têm um plano de conformidade. Use esta política para proibir a implantar dos recursos de endereço IP públicos. Embora as regras do NSG possam ser usadas para bloquear o tráfego da Internet com eficiência, evitar o uso de IPs Públicos reduz ainda mais a superfície de ataque.   | Introdução com este [modelo](../../governance/policy/samples/not-allowed-resource-types.md).  |

### <a name="network-watcher-traffic-analytics"></a>Análise de tráfego do Observador de Rede

A [análise de tráfego](https://azure.microsoft.com/blog/traffic-analytics-in-preview/) do Observador de Rede do consome os dados de Log de fluxo e outros logs para fornecer uma visão geral de alto nível do tráfego de rede. Esses dados são úteis para auditoria da conformidade do TIC e identificação de pontos problemáticos. Você pode usar o painel de alto nível para fazer rapidamente uma triagem de quais as máquinas virtuais se comunicam com a Internet que fornecerá uma lista voltada para o roteamento TIC.

![Análise de tráfego](media/tic-traffic-analytics-1.png)

Use o **mapa geográfico** para identificar rapidamente os destinos físicos prováveis de tráfego da internet para as máquinas virtuais. Você pode identificar e realizar a triagem de locais suspeitos ou mudanças de padrão:

![Mapa geográfico](media/tic-traffic-analytics-2.png)

Use o **Mapa de Topologia de Rede** para pesquisar rapidamente as redes virtuais existentes:

![Mapa de topologia de rede](media/tic-traffic-analytics-3.png)

### <a name="network-watcher-next-hop-tests"></a>Testes do próximo salto do Observador de Rede

Redes em regiões que são monitorados pelo Observador de Rede podem conduzir testes de próximo salto. No portal do Azure, você pode inserir uma origem e destino para um exemplo de fluxo de rede para o Observador de Rede resolver o destino do próximo salto. Execute este teste em relação a máquinas virtuais e endereços de internet de exemplo para garantir que o destino do próximo salto é o gateway de rede esperado virtual.

![Testes do próximo salto](media/tic-network-watcher.png)

## <a name="conclusions"></a>Conclusões

Você pode configurar facilmente o acesso ao Microsoft Azure, ao Office 365 e ao Dynamics 365 para ajudar no cumprimento das diretrizes do TIC 2.0 Apêndice H, conforme elaborado e definido em maio de 2018. A Microsoft reconhece que as diretrizes TIC estão sujeitas a alterações. A Microsoft esforça-se para ajudar os clientes a atender as diretrizes de maneira oportuna, conforme novas diretrizes são liberadas.

## <a name="appendix-trusted-internet-connections-patterns-for-common-workloads"></a>Apêndice: Padrões de conexões de Internet confiáveis para cargas de trabalho comuns

| Category | Carga de trabalho | IaaS | PaaS dedicado / injeção de rede virtual  | Pontos de extremidade de serviço  |
|---------|---------|---------|---------|--------|
| Computação | Máquinas Virtuais do Linux do Azure | Sim | | |
| Computação | Máquinas Virtuais do Windows do Azure | Sim | | |
| Computação | conjuntos de escala de máquina virtual | Sim | | |
| Computação | Funções do Azure | | Ambiente do Serviço de Aplicativo | |
| Web e móvel | Aplicativo Web interno | | Ambiente do Serviço de Aplicativo| |
| Web e móvel | Aplicativo móvel interno | | Ambiente do Serviço de Aplicativo | |
| Web e móvel | Aplicativos de API | | Ambiente do Serviço de Aplicativo | |
| Contêineres | Serviço de Contêiner do Azure | | | Sim |
| Contêineres | AKS (Serviço do Kubernetes do Azure) \* | | | Sim |
| Banco de dados | Banco de Dados SQL do Azure | | Instância Gerenciada do Banco de Dados SQL do Azure \* | SQL do Azure |
| Banco de dados | Banco de Dados do Azure para MySQL | | | Sim |
| Banco de dados | Banco de Dados do Azure para PostgreSQL | | | Sim |
| Banco de dados | SQL Data Warehouse do Azure | | | Sim |
| Banco de dados | Azure Cosmos DB | | | Sim |
| Banco de dados | Cache Redis do Azure | | Sim | |
| Armazenamento | Armazenamento de Blobs do Azure | Sim | | |
| Armazenamento | Arquivos do Azure | Sim | | |
| Armazenamento | Armazenamento de Filas do Azure | Sim | | |
| Armazenamento | Armazenamento da tabela do Azure | Sim | | |
| Armazenamento | Armazenamento de disco do Azure | Sim | | |

\* Versão prévia pública no Azure Governamental, maio de 2018.

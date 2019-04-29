---
title: Práticas recomendadas de segurança de rede do Azure | Microsoft Docs
description: Conheça alguns dos principais recursos disponíveis no Azure para ajudar a criar ambientes de rede segura
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: d169387a-1243-4867-a602-01d6f2d8a2a1
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: 74a46c7788b0a0dc729ab977489ed6d97a387a6e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60619281"
---
# <a name="microsoft-cloud-services-and-network-security"></a>Segurança de rede e serviços em nuvem da Microsoft
Os serviços em nuvem da Microsoft entregam serviços e estrutura em larga escala, recursos de nível empresarial e várias opções de conectividade híbrida. Os clientes podem optar por acessar esses serviços através da Internet ou com a Azure ExpressRoute, que fornece conectividade de rede privada. A plataforma Microsoft Azure permite que os clientes estendam facilmente sua infra-estrutura para a nuvem e criem arquiteturas com várias camadas. Além disso, terceiros podem habilitar recursos avançados oferecendo serviços de segurança e soluções de virtualização. Este white paper fornece uma visão geral sobre segurança e problemas de arquitetura que os clientes devem considerar ao usar os serviços em nuvem da Microsoft acessados através do ExpressRoute. Ele também aborda a criação de serviços mais seguros em redes virtuais do Azure.

## <a name="fast-start"></a>Início rápido
O gráfico lógico a seguir pode direcioná-lo a um exemplo específico das várias técnicas de segurança disponíveis na plataforma Azure. Para referência rápida, encontre o exemplo que melhor se ajusta a seu caso. Para obter explicações mais completas, continue lendo o artigo.
[![0]][0]

[Exemplo 1: Crie uma rede de perímetro (também conhecida como DMZ, zona desmilitarizada ou sub-rede filtrada) para ajudar a proteger aplicativos com grupos de segurança de rede (NSGs).](#example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs)</br>
[Exemplo 2: Crie uma rede de perímetro para ajudar a proteger aplicativos com um firewall e NSGs.](#example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs)</br>
[Exemplo 3: Crie uma rede de perímetro para ajudar a proteger redes com um firewall, uma rota definida pelo usuário (UDR) e um NSG.](#example-3-build-a-perimeter-network-to-help-protect-networks-with-a-firewall-and-udr-and-nsg)</br>
[Exemplo 4: Adicione uma conexão híbrida com uma dispositivo virtual site a site, rede virtual privada (VPN).](#example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn)</br>
[Exemplo 5: Adicione uma conexão híbrida com um gateway de VPN do Azure site a site.](#example-5-add-a-hybrid-connection-with-a-site-to-site-azure-vpn-gateway)</br>
[Exemplo 6: Adicione uma conexão híbrida com o ExpressRoute.](#example-6-add-a-hybrid-connection-with-expressroute)</br>
 Exemplos para adicionar conexões entre redes virtuais, alta disponibilidade e encadeamento de serviços serão adicionados a esse documento durante os próximos meses.

## <a name="microsoft-compliance-and-infrastructure-protection"></a>Proteção de infraestrutura e conformidade da Microsoft
Para ajudar as organizações a atender os requisitos regionais, nacionais e específicos do setor que controlam a coleta e o uso de dados de indivíduos, a Microsoft oferece mais de 40 certificações e atestados. O conjunto mais abrangente de qualquer provedor de serviços de nuvem.

Para obter mais informações, consulte as informações de conformidade no [Microsoft Trust Center][TrustCenter].

A Microsoft tem uma abordagem abrangente para proteger a infraestrutura de nuvem necessária para executar os serviços globais em larga escala. Além dos datacenters físicos, a infraestrutura de nuvem da Microsoft também inclui hardware, software, redes, administrativas e equipe de operações.

![2]

A abordagem acima fornece uma base segura para os clientes implantarem seus serviços na Microsoft Cloud. A próxima etapa é o design e a criação de uma arquitetura de segurança pelos clientes para proteger esses serviços.

## <a name="traditional-security-architectures-and-perimeter-networks"></a>Arquiteturas de segurança tradicionais e redes de perímetro
Embora a Microsoft faça investimentos consideráveis para proteger a infraestrutura de nuvem, os clientes também devem proteger os serviços de nuvem e os grupos de recursos. Uma abordagem multicamada para a segurança oferece a melhor defesa. Uma zona de segurança de rede de perímetro protege recursos da rede interna de uma rede não confiável. Uma rede de perímetro refere-se às bordas ou partes da rede que ficam entre a Internet e a infraestrutura de TI da empresa protegida.

Em redes empresariais típicas, a infraestrutura básica é reforçada intensamente no perímetro, com várias camadas de dispositivos de segurança. O limite de cada camada consiste em dispositivos e pontos de imposição de políticas. Cada camada pode incluir uma combinação dos seguintes dispositivos de segurança de rede: firewalls, prevenção de DoS (ataque de negação de serviço), IDS/IPS (sistemas de detecção de intrusão ou sistemas de proteção) e dispositivos VPN. A imposição de políticas pode assumir a forma de políticas de firewall, ACLs (listas de controle de acesso) ou roteamento específico. A primeira linha de defesa da rede, que aceita diretamente o tráfego de entrada da Internet, é uma combinação desses mecanismos para bloquear ataques e tráfego perigoso, permitindo que solicitações legítimas adentrem a rede. Esse tráfego é roteado diretamente aos recursos na rede de perímetro. Esse recurso pode "falar" com recursos mais dentro da rede, passando primeiro pelo próximo limite de validação. A camada mais externa é chamada de a rede de perímetro, porque essa parte da rede está exposta à Internet, geralmente com alguma forma de proteção em ambos os lados. A figura a seguir mostra um exemplo de uma rede de perímetro de sub-rede individual em uma rede corporativa, com dois limites de segurança.

![3]

Há muitas arquiteturas usadas para implementar uma rede de perímetro. Essas arquiteturas podem variar de um simples do balanceador de carga até uma rede de perímetro com várias sub-redes e diferentes mecanismos em cada limite para bloquear o tráfego e proteger as camadas mais profundas da rede corporativa. Como a rede de perímetro é criada depende das necessidades específicas da organização e da tolerância a riscos geral.

Conforme os clientes vão movendo suas cargas de trabalho para nuvens públicas, é essencial dar suporte a recursos semelhantes de arquitetura de rede de perímetro no Azure para atender aos requisitos de conformidade e de segurança. Este documento fornece diretrizes sobre como os clientes podem criar um ambiente de rede segura no Azure. Ele se concentra na rede de perímetro, mas também inclui uma discussão abrangente sobre muitos aspectos de segurança de rede. As perguntas a seguir informam essa discussão:

* Como é possível criar uma rede de perímetro no Azure?
* Quais são alguns dos recursos do Azure disponíveis para criar a rede de perímetro?
* Como cargas de trabalho de back-end podem ser protegidas?
* Como a comunicação com a Internet é controlada para as cargas de trabalho no Azure?
* Como as redes locais podem ser protegidas contra implantações no Azure?
* Quando recursos de segurança nativos do Azure devem ser usados em vez de dispositivos ou serviços de terceiros?

O diagrama a seguir mostra várias camadas de segurança que Azure fornece aos clientes. Essas camadas são tanto nativas na plataforma Azure em si quanto recursos definidos pelo cliente:

![4]

Vinda da Internet a proteção DDoS do Azure ajuda a proteger contra ataques em grande escala contra o Azure. A próxima camada é de endereços IP públicos definidos pelo cliente (pontos de extremidade), que são usados para determinar qual tráfego pode passar pelo serviço de nuvem para a rede virtual. O isolamento de rede virtual nativa do Azure garante o isolamento completo de todas as outras redes e garante que o tráfego flua somente através de métodos e caminhos configurados pelo usuário. Esses caminhos e métodos são a próxima camada em que NSGs, UDR e soluções de virtualização de rede podem ser usados para criar limites de segurança para proteger as implantações de aplicativo na rede protegida.

A próxima seção fornece uma visão geral das redes virtuais do Azure. As redes virtuais do Azure são criadas por clientes e tratam-se daquilo a que as cargas de trabalho implantadas por esses clientes estão conectadas. Redes virtuais são a base de todos os recursos de segurança de rede necessários para estabelecer uma rede de perímetro e proteger as implantações dos clientes no Azure.

## <a name="overview-of-azure-virtual-networks"></a>Visão geral das redes virtuais do Azure
Antes que o tráfego da Internet possa acessar as redes virtuais do Azure, há duas camadas de segurança inerentes à plataforma Azure:

1.    **Proteção contra DDoS**: Proteção contra DDoS é uma camada de rede física do Azure que protege a própria plataforma do Azure contra ataques baseados na Internet em grande escala. Esses ataques usam vários nós de "bot" em uma tentativa de sobrecarregar um serviço de Internet. O Azure tem uma malha de proteção sólida contra DDoS em todas as conexões de entrada, saída e entre regiões do Azure. Essa camada de proteção contra DDoS não tem atributos configuráveis pelo usuário e não é acessível ao cliente. A camada de proteção contra DDoS protege o Azure como uma plataforma de ataques em grande escala, além de monitorar o tráfego de saída e o tráfego entre regiões do Azure. Usando soluções de virtualização de rede na VNet, camadas adicionais de resiliência podem ser configuradas pelo cliente contra um ataque de menor escala que não ative a proteção no nível de plataforma. Um exemplo de DDoS em ação: se um endereço IP voltado para a Internet fosse atacado por um ataque de DDoS em larga escala, o Azure detectaria as origens dos ataques e eliminaria o tráfego ofensivo antes que este atingisse seu destino pretendido. Em quase todos os casos, o ponto de extremidade atacado não é afetado pelo ataque. Nos raros casos em que um ponto de extremidade é afetado, nenhum tráfego é afetado para outros pontos de extremidade, apenas para o ponto de extremidade atacado. Assim, outros clientes e serviços não veem nenhum impacto vindo desse ataque. É fundamental observar que o Azure DDoS está procurando apenas por ataques em grande escala. É possível que seu serviço específico seja sobrecarregado antes que os limites de proteção no nível de plataforma sejam excedidos. Por exemplo, um site da Web em um único servidor IIS A0 poderia ficar offline devido a um ataque de DDoS antes que a proteção contra DDoS no nível de plataforma do Azure registrasse uma ameaça.

2.  **Endereços IP públicos**: IP público endereços (habilitados por meio de pontos de extremidade de serviço, endereços IP públicos, Gateway de aplicativo e outros recursos do Azure que apresentam um endereço IP público para a internet roteado para o recurso) permitem que nuvem serviços ou grupos de recursos ter um IP público de Internet endereços e portas expostas. O ponto de extremidade usa NAT (Conversão de Endereços de Rede) para rotear o tráfego para a porta e endereço internos na rede virtual do Azure. Esse caminho é a principal rota para que o tráfego externo passe para dentro da rede virtual. Os endereços IP públicos são configuráveis pelo usuário para determinar tanto qual tráfego pode passar quanto como e para onde esse tráfego deve ser convertido na rede virtual.

Depois que o tráfego alcança a rede virtual, há muitos recursos que entram em cena. Redes virtuais do Azure são a base para que clientes possam anexar suas cargas de trabalho e o local em que a segurança no nível da rede básica se aplica. É uma rede privada (uma sobreposição de rede virtual) no Azure para clientes com os seguintes recursos e características:

* **Isolamento de tráfego**: Uma rede virtual é o limite de isolamento de tráfego na plataforma do Azure. As VMs (máquinas virtuais) em uma rede virtual não podem comunicar-se diretamente com VMs em uma rede virtual diferente, mesmo que ambas as redes virtuais sejam criadas pelo mesmo cliente. Isolamento é uma propriedade vital que garante que as VMs e as comunicações do cliente permaneçam privadas em uma rede virtual.

>[!NOTE]
>O isolamento de tráfego refere-se apenas ao tráfego *de entrada* para a rede virtual. Por padrão, o tráfego de saída da VNet para Internet é permitido, mas pode ser evitado se desejado por NSGs.
>
>

* **Topologia de multicamadas**: Redes virtuais permitem que os clientes definam a topologia multicamada alocando sub-redes e designando espaços de endereço separados para diferentes elementos ou "camadas" de suas cargas de trabalho. Esses agrupamentos e topologias lógicos permitem que os clientes definam políticas de acesso diferentes baseadas nos tipos de carga de trabalho e também controlam fluxos de tráfego entre as camadas.
* **Conectividade entre locais**: Os clientes podem estabelecer conectividade entre locais entre uma rede virtual e vários sites locais ou outras redes virtuais no Azure. Para criar uma conexão, os clientes podem usar o emparelhamento de VNet, Gateways de VPN do Azure, soluções de virtualização de rede de terceiros ou ExpressRoute. O Azure dá suporte a VPNs site a site (S2S) usando protocolos padrão de IPsec/IKE e conectividade privada de ExpressRoute.
* **NSG** permite que os clientes criem regras (ACLs) no nível desejado de granularidade: sub-redes virtuais, VMs individuais ou interfaces de rede. Os clientes podem controlar o acesso permitindo ou negando a comunicação entre as cargas de trabalho em uma rede virtual, de sistemas nas redes do cliente por meio de conectividade entre locais ou comunicação direta via Internet.
* **UDR** e **Encaminhamento IP** permitem que os clientes definam os caminhos de comunicação entre camadas diferentes em uma rede virtual. Os clientes podem implantar firewall, IDS/IPS e outras soluções de virtualização, além de rotear o tráfego de rede por meio dessas soluções de segurança para a aplicação, auditoria e inspeção das políticas de limites de segurança.
* **Soluções de virtualização de rede** no Azure Marketplace: Dispositivos de segurança, como firewalls, balanceadores de carga e IDS/IPS estão disponíveis no Azure Marketplace e a Galeria de imagens de VM. Os clientes podem implantar essas soluções em suas redes virtuais e, especificamente, em seus limites de segurança (incluindo as sub-redes da rede de perímetro) para completar um ambiente de rede segura com várias camadas.

Com esses recursos e funções, um exemplo de como uma arquitetura de rede de perímetro poderia ser criada no Azure é a mostrada no seguinte diagrama:

![5]

## <a name="perimeter-network-characteristics-and-requirements"></a>Requisitos e características da rede de perímetro
A rede de perímetro é o front-end da rede, fazendo a interface de comunicação diretamente da Internet. Os pacotes de entrada devem fluir por dispositivos de segurança, como firewall, IDS, IPS e afins, antes de alcançar os servidores back-end. Pacotes das cargas de trabalho associados à Internet também podem fluir pelos dispositivos de segurança na rede de perímetro para fins de auditoria, inspeção e imposição da política antes de sair da rede. Além disso, a rede de perímetro pode hospedar os gateways de VPN entre locais entre redes virtuais de cliente e redes locais.

### <a name="perimeter-network-characteristics"></a>Características da rede de perímetro
Algumas das características de uma boa rede de perímetro, referenciando a figura anterior, são as seguintes:

* Voltada para a Internet:
  * A sub-rede da rede de perímetro em si é voltada para a Internet, comunicando-se diretamente com ela.
  * Endereços IP públicos, VIPs e/ou pontos de extremidade de serviço passam tráfego da Internet para a rede e dispositivos de front-end.
  * O tráfego de entrada da Internet passa por dispositivos de segurança antes de outros recursos na rede de front-end.
  * Se segurança de saída estiver habilitada, o tráfego passará por dispositivos de segurança, como a etapa final, antes de passar para a Internet.
* Rede protegida:
  * Não há caminho direto da Internet para a infraestrutura básica.
  * Os canais para a infraestrutura básica devem percorrer dispositivos de segurança, como NSGs, firewalls ou dispositivos VPN.
  * Outros dispositivos não devem ligar a Internet à infraestrutura básica.
  * Dispositivos de segurança tanto no limite para a Internet quanto nos limites voltados à rede protegida da rede de perímetro (por exemplo, os dois ícones de firewall mostrados na figura anterior) podem, na verdade, ser uma única solução de virtualização com regras ou interfaces diferenciadas para cada limite. Por exemplo, um dispositivo físico, logicamente separado, lidando com a carga para ambos os limites da rede de perímetro.
* Outras práticas e restrições comuns:
  * As cargas de trabalho não devem armazenar informações essenciais aos negócios.
  * O acesso e as atualizações para configurações e implantações da rede de perímetro são limitados somente aos administradores autorizados.

### <a name="perimeter-network-requirements"></a>Requisitos de rede de perímetro
Para habilitar essas características, siga essas diretrizes sobre requisitos de rede virtual para implementar uma rede de perímetro com êxito:

* **Arquitetura de sub-rede:** Especifique a rede virtual, de modo que uma sub-rede inteira seja dedicada como a rede de perímetro, separada de outras sub-redes na mesma rede virtual. Essa separação garante o tráfego entre a rede de perímetro e outras camadas de sub-rede internas ou privadas flua por meio de um firewall ou solução de virtualização IDS/IPS.  Rotas definidas pelo usuário nas sub-redes de limite são necessárias para encaminhar esse tráfego para a solução de virtualização.
* **NSG:** A sub-rede de rede de perímetro em si deve estar aberta para permitir a comunicação com a Internet, mas isso não significa que os clientes devem ignorar os NSGs. Siga as práticas de segurança comuns para minimizar as superfícies de rede expostas à Internet. Bloqueie os intervalos de endereços remotos com permissão para acessar as implantações ou os protocolos de aplicativo específicos e as portas que estão abertas. Pode haver circunstâncias, porém, em que um bloqueio total não é possível. Por exemplo, se os clientes têm um site externo no Azure, a rede de perímetro deve permitir que as solicitações de entrada da web de endereços IP públicos, mas só deve abrir as portas do aplicativo web: TCP na porta 80 e/ou TCP na porta 443.
* **Tabela de roteamento:** A sub-rede de rede de perímetro em si deve ser capaz de se comunicar diretamente com a Internet, mas não deve permitir a comunicação direta de e para as redes de back-end ou em locais sem passar por meio de um dispositivo de firewall ou de segurança.
* **Configuração de dispositivo de segurança:** Para rotear e inspecionar os pacotes entre a rede de perímetro e o restante das redes protegidas, os dispositivos de segurança, como firewall, IDS e IPS dispositivos podem ser multihomed. Eles podem ter NICs separadas para a rede de perímetro e as sub-redes de back-end. As NICs na rede de perímetro se comunicarão diretamente de e para a Internet, com os NSGs correspondentes e a tabela de roteamento da rede de perímetro. As NICs conectadas às sub-redes de back-end têm NSGs e tabelas de roteamento mais restritos das sub-redes correspondentes do back-end.
* **Funcionalidade de dispositivo de segurança:** Os dispositivos de segurança implantados na rede de perímetro normalmente executam a seguinte funcionalidade:
  * Firewall: Imposição de regras de firewall ou políticas de controle de acesso para solicitações de entrada.
  * Detecção de ameaças e prevenção de: Detectando e atenua ataques da Internet.
  * Auditoria e registro: Mantém logs detalhados de auditoria e análise.
  * Proxy reverso: Redirecionar as solicitações de entrada para os servidores de back-end correspondentes. Esse redirecionamento envolve o mapeamento e conversão de endereços de destino nos dispositivos de front-end, normalmente firewalls, para os endereços de servidores back-end.
  * Proxy de encaminhamento: Fornece NAT e realiza auditoria para a comunicação iniciada de dentro da rede virtual com a Internet.
  * Roteador: Encaminhar o tráfego de entrada e entre sub-redes dentro da rede virtual.
  * Dispositivo VPN: Atuando como os gateways VPN entre instalações para conectividade VPN entre locais entre redes de local de cliente e redes virtuais do Azure.
  * Servidor VPN: Aceitando a clientes VPN conectando redes virtuais do Azure.

> [!TIP]
> Mantenha os dois grupos a seguir separados: as pessoas autorizadas a acessar o mecanismo de segurança da rede de perímetro e as pessoas autorizadas como administradores de operações, implantação ou desenvolvimento de aplicativos. Manter esses grupos separados permite uma diferenciação de deveres e impede que uma única pessoa ignore controles de segurança de aplicativos e de rede.
>
>

### <a name="questions-to-be-asked-when-building-network-boundaries"></a>Perguntas a serem feitas durante a criação de limites de rede
Nesta seção, a menos que especificamente mencionadas, o termo "redes" refere-se a redes virtuais privadas do Azure criadas por um administrador de assinatura. O termo não se refere às redes físicas subjacentes no Azure.

Além disso, as redes virtuais do Azure geralmente são usadas para estender redes locais tradicionais. É possível incorporar soluções de rede híbridas site a site ou de ExpressRoute com arquiteturas de rede de perímetro. Esse link híbrido é uma consideração importante na criação de limites de segurança de rede.

É essencial responder as três perguntas a seguir quando você está criando uma rede com uma rede de perímetro e vários limites de segurança.

#### <a name="1-how-many-boundaries-are-needed"></a>1) Quantos limites são necessários?
O primeiro ponto de decisão é decidir quantos limites de segurança são necessários em um determinado cenário:

* Um único limite: Um na rede de perímetro de front-end, entre a rede virtual e a Internet.
* Dois limites: Um no lado da Internet da rede de perímetro e outro entre a sub-rede da rede de perímetro e as sub-redes de back-end nas redes virtuais do Azure.
* Três limites: No lado da Internet da rede de perímetro entre a rede de perímetro e as sub-redes de back-end e uma entre as sub-redes de back-end e a rede local.
* Limites de N: Um número variável. Dependendo dos requisitos de segurança, pode-se aplicar qualquer número de limites de segurança a uma determinada rede.

O número e tipo dos limites necessários variam dependendo da tolerância a riscos da empresa e do cenário específico que está sendo implementado. Geralmente essa é uma decisão conjunta tomada por vários grupos dentro de uma organização, muitas vezes incluindo uma equipe de risco e conformidade, uma equipe de rede e plataforma e uma equipe de desenvolvimento de aplicativos. As pessoas com conhecimento de segurança, os dados envolvidos e as tecnologias usadas devem pesar na decisão para garantir a postura de segurança apropriada em cada implementação.

> [!TIP]
> Use a menor quantidade de limites que satisfaçam os requisitos de segurança em uma determinada situação. Com mais limites, as operações e a solução de problemas podem tornar-se mais difíceis, bem como a sobrecarga do gerenciamento envolvido com a gestão de várias políticas de limites ao longo do tempo. No entanto, os limites insuficientes aumentam o risco. Encontrar o equilíbrio é fundamental.
>
>

![6]

A figura anterior mostra uma visão geral de uma rede de limite com três limites de segurança. Os limites estão entre a rede de perímetro e a Internet, entre as sub-redes privadas de front-end e back-end do Azure e entre a sub-rede de back-end do Azure e rede corporativa local.

#### <a name="2-where-are-the-boundaries-located"></a>2) Onde estão localizados os limites?
Depois que o número de limites é decidido, onde implementá-los é o próximo ponto a se decidir. Geralmente, há três opções:

* Usar um serviço intermediário baseado na Internet (por exemplo, um firewall do aplicativo Web baseado em nuvem, que não é discutido neste documento)
* Usar recursos nativos e/ou soluções de virtualização de rede no Azure
* Usar dispositivos físicos na rede local

Em redes puramente do Azure, as opções são recursos nativos do Azure (por exemplo, Balanceadores de Carga do Azure) ou soluções de virtualização de rede do ecossistema variado de parceiros do Azure (por exemplo, firewalls de Ponto de Verificação).

Se for necessário um limite entre o Azure e uma rede local, os dispositivos de segurança poderão residir em qualquer um dos lados da conexão (ou em ambos). Assim, uma decisão deve ser tomada sobre o local para colocar o mecanismo de segurança.

Na figura anterior, os limites Internet para rede de perímetro e front-end para back-end estão totalmente contidos no Azure e devem ser recursos nativos do Azure ou soluções de virtualização de rede. Os dispositivos de segurança no limite entre o Azure (sub-rede de back-end) e a rede corporativa podem estar no lado do Azure ou no lado local, ou até como uma combinação de dispositivos em ambos os lados. Pode haver vantagens e desvantagens relevantes em ambas as opções; portanto, elas devem ser levadas em consideração.

Por exemplo, usar o mecanismo de segurança físico existente no lado da rede local tem a vantagem de não haver necessidade de nenhuma nova engrenagem. Ele precisa apenas de reconfiguração. A desvantagem, no entanto, é que todo o tráfego deve voltar do Azure para a rede local para ser visto pelo mecanismo de segurança. Assim, o tráfego do Azure para o Azure poderia incorrer em latência significativa e afetar a experiência do usuário e o desempenho do aplicativo se ele fosse forçado novamente à rede local para a imposição de política de segurança.

#### <a name="3-how-are-the-boundaries-implemented"></a>3) Como os limites são implementados?
Cada limite de segurança provavelmente terá requisitos de capacidade diferentes (por exemplo, IDS e regras de firewall no lado da Internet da rede de perímetro, mas somente ACLs entre a rede de perímetro e a sub-rede de back-end). A decisão de quais (ou quantos) dispositivos usar depende dos requisitos de cenário e de segurança. Na seção a seguir, os exemplos 1, 2 e 3 discutem algumas opções que podem ser usadas. A análise dos recursos de rede nativos do Azure e dos dispositivos disponíveis no Azure do ecossistema de parceiros mostra as inúmeras opções disponíveis para resolver praticamente qualquer cenário.

Outro ponto-chave de decisão de implementação é como se conectar à rede local com o Azure. Você deveria usar o gateway virtual do Azure ou uma solução de virtualização de rede? Essas opções são discutidas mais detalhadamente na seção a seguir (exemplos 4, 5 e 6).

Além disso, o tráfego entre redes virtuais no Azure pode ser necessário. Esses cenários serão adicionados posteriormente.

Depois de saber as respostas para as perguntas acima, a seção [Início Rápido](#fast-start) pode ajudá-lo a identificar quais exemplos são mais apropriados para um determinado cenário.

## <a name="examples-building-security-boundaries-with-azure-virtual-networks"></a>Exemplos: Criação de limites de segurança com redes virtuais do Azure
### <a name="example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs"></a>Exemplo 1: crie uma rede de perímetro para ajudar a proteger aplicativos com NSGs
[Voltar ao Início rápido](#fast-start) | [Instruções detalhadas de build para este exemplo][Example1]

[![7]][7]

#### <a name="environment-description"></a>Descrição do ambiente
Neste exemplo, há uma assinatura que contém os seguintes recursos:

- Um único grupo de recursos
- Uma rede virtual com duas sub-redes: "FrontEnd" e "BackEnd"
- Um grupo de segurança de rede que é aplicado a ambas as sub-redes
- Um servidor Windows que representa um servidor Web de aplicativos ("IIS01")
- Dois servidores Windows que representam servidores de back-end de aplicativos ("AppVM01", "AppVM02")
- Um servidor Windows que representa um servidor DNS ("DNS01")
- Um IP público associado ao servidor Web do aplicativo

Para ver scripts e um modelo do Azure Resource Manager, consulte as [instruções detalhadas de build][Example1].

#### <a name="nsg-description"></a>Descrição de NSG
Neste exemplo, um grupo NSG é criado e então carregado com seis regras.

> [!TIP]
> Em geral, você deve criar suas regras específicas "Permitir" primeiro, seguidas pelas regras “Negar” de caráter mais genérico. A prioridade dada determina quais regras são avaliadas primeiro. Quando o tráfego se aplicar a uma regra específica, nenhuma regra adicional será avaliada. As regras NSG podem se aplicar na direção de entrada ou de saída (na perspectiva da sub-rede).
>
>

Declarativamente, as regras a seguir estão sendo criadas para tráfego de entrada:

1. O tráfego interno de DNS (porta 53) é permitido.
2. O tráfego de RDP (porta 3389) da Internet para qualquer Máquina Virtual é permitido.
3. O tráfego HTTP (porta 80) da Internet para o servidor Web (IIS01) é permitido.
4. Todo o tráfego (todas as portas) de IIS01 para AppVM1 é permitido.
5. Todo o tráfego (todas as portas) da Internet para a rede virtual inteira (ambas as sub-redes) é negado.
6. Todo o tráfego (todas as portas) da sub-rede de front-end para a sub-rede de back-end é negado.

Com essas regras associado a cada sub-rede, se uma solicitação HTTP entrou pela Internet no servidor Web, as duas regras 3 (permitir) e 5 (negar) seriam aplicáveis. Mas como a regra 3 tem uma prioridade mais alta, apenas ela seria aplicável e a regra 5 não seria entram em cena. Portanto, a solicitação HTTP seria permitida para o servidor Web. Se esse mesmo tráfego estivesse tentando acessar o servidor DNS01, a regra 5 (negar) seria a primeira a ser aplicada e o tráfego não teria permissão para passar para o servidor. A regra 6 (negar) bloqueia a comunicação entre a sub-rede de front-end e a sub-rede de back-end (exceto pelo tráfego permitido nas regras 1 e 4). Esse conjunto de regras protege a rede de back-end no caso de um invasor comprometer o aplicativo Web no front-end. O invasor teria acesso limitado à rede back-end "protegida" (apenas para recursos expostos no servidor AppVM01).

Há uma regra de saída padrão que permite o tráfego de saída para a Internet. Neste exemplo, permitiremos o tráfego de saída e não modificaremos quaisquer regras de saída. Para bloquear o tráfego em ambos os trajetos, é necessário o roteamento definido pelo usuário (veja o exemplo 3).

#### <a name="conclusion"></a>Conclusão
Esse exemplo representa uma maneira relativamente simples e direta de isolar a sub-rede de back-end do tráfego de entrada. Para obter mais informações, consulte as [instruções detalhadas de build][Example1]. Essas instruções incluem:

* Como criar essa rede de perímetro com scripts do PowerShell clássico.
* Como criar essa rede de perímetro com um modelo do Azure Resource Manager.
* Descrições detalhadas de cada comando NSG.
* Cenários de fluxo de tráfego detalhados mostrando como o tráfego é permitido ou negado em cada camada.


### <a name="example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs"></a>Exemplo 2: crie uma rede de perímetro para ajudar a proteger aplicativos com um firewall e NSGs
[Voltar ao Início rápido](#fast-start) | [Instruções detalhadas de build para este exemplo][Example2]

[![8]][8]

#### <a name="environment-description"></a>Descrição do ambiente
Neste exemplo, há uma assinatura que contém os seguintes recursos:

* Um único grupo de recursos
* Uma rede virtual com duas sub-redes: "FrontEnd" e "BackEnd"
* Um grupo de segurança de rede que é aplicado a ambas as sub-redes
* Uma solução de virtualização de rede, neste caso um firewall, conectada à sub-rede de front-end
* Um servidor Windows que representa um servidor Web de aplicativos ("IIS01")
* Dois servidores Windows que representam servidores de back-end de aplicativos ("AppVM01", "AppVM02")
* Um servidor Windows que representa um servidor DNS ("DNS01")

Para ver scripts e um modelo do Azure Resource Manager, consulte as [instruções detalhadas de build][Example2].

#### <a name="nsg-description"></a>Descrição de NSG
Neste exemplo, um grupo NSG é criado e então carregado com seis regras.

> [!TIP]
> Em geral, você deve criar suas regras específicas "Permitir" primeiro, seguidas pelas regras “Negar” de caráter mais genérico. A prioridade dada determina quais regras são avaliadas primeiro. Quando o tráfego se aplicar a uma regra específica, nenhuma regra adicional será avaliada. As regras NSG podem se aplicar na direção de entrada ou de saída (na perspectiva da sub-rede).
>
>

Declarativamente, as regras a seguir estão sendo criadas para tráfego de entrada:

1. O tráfego interno de DNS (porta 53) é permitido.
2. O tráfego de RDP (porta 3389) da Internet para qualquer Máquina Virtual é permitido.
3. Todo tráfego da Internet (todas as portas) para a solução de virtualização de rede (firewall) é permitido.
4. Todo o tráfego (todas as portas) de IIS01 para AppVM1 é permitido.
5. Todo o tráfego (todas as portas) da Internet para a rede virtual inteira (ambas as sub-redes) é negado.
6. Todo o tráfego (todas as portas) da sub-rede de front-end para a sub-rede de back-end é negado.

Com essas regras associadas a cada sub-rede, se uma solicitação HTTP entrasse da Internet para firewall, as duas regras 3 (permitir) e 5 (negar) seriam aplicáveis. Mas como a regra 3 tem uma prioridade mais alta, apenas ela seria aplicável e a regra 5 não seria entram em cena. Assim, a solicitação HTTP seria permitida ao firewall. Se esse mesmo tráfego estava tentando alcançar o servidor IIS01, mesmo que ele estivesse na sub-rede de front-end, a regra 5 (negar) seria aplicável e o tráfego não teria permissão para passar para o servidor. A regra 6 (negar) bloqueia a comunicação entre a sub-rede de front-end e a sub-rede de back-end (exceto pelo tráfego permitido nas regras 1 e 4). Esse conjunto de regras protege a rede de back-end no caso de um invasor comprometer o aplicativo Web no front-end. O invasor teria acesso limitado à rede back-end "protegida" (apenas para recursos expostos no servidor AppVM01).

Há uma regra de saída padrão que permite o tráfego de saída para a Internet. Neste exemplo, permitiremos o tráfego de saída e não modificaremos quaisquer regras de saída. Para bloquear o tráfego em ambos os trajetos, é necessário o roteamento definido pelo usuário (veja o exemplo 3).

#### <a name="firewall-rule-description"></a>Descrição da regra de firewall
No firewall, será necessário criar regras de encaminhamento. Como este exemplo só roteia o tráfego de Internet de entrada para o firewall e, em seguida, para o servidor Web, somente uma regra de NAT (conversão de endereços de rede) de encaminhamento é necessária.

A regra de encaminhamento aceita qualquer endereço de origem de entrada que acesse o firewall tentando alcançar HTTP (porta 80 ou 443 para HTTPS). Ela sai da interface local do firewall e é redirecionada para o servidor Web com o Endereço IP 10.0.1.5.

#### <a name="conclusion"></a>Conclusão
Este exemplo é uma maneira relativamente simples de proteger seu aplicativo com um firewall e isolar a sub-rede de back-end do tráfego de entrada. Para obter mais informações, consulte as [instruções detalhadas de build][Example2]. Essas instruções incluem:

* Como criar essa rede de perímetro com scripts do PowerShell clássico.
* Como criar esse exemplo com um modelo do Azure Resource Manager.
* Descrições detalhadas de cada comando NSG e regra de firewall.
* Cenários de fluxo de tráfego detalhados mostrando como o tráfego é permitido ou negado em cada camada.

### <a name="example-3-build-a-perimeter-network-to-help-protect-networks-with-a-firewall-and-udr-and-nsg"></a>Exemplo 3: crie uma rede de perímetro para ajudar a proteger as redes com um firewall, UDR e NSG
[Voltar ao Início rápido](#fast-start) | [Instruções detalhadas de build para este exemplo][Example3]

[![9]][9]

#### <a name="environment-description"></a>Descrição do ambiente
Neste exemplo, há uma assinatura que contém os seguintes recursos:

* Um único grupo de recursos
* Uma rede virtual com três sub-redes: "SecNet", "FrontEnd" e "BackEnd"
* Uma solução de virtualização de rede, neste caso um firewall, conectada à sub-rede SecNet
* Um servidor Windows que representa um servidor Web de aplicativos ("IIS01")
* Dois servidores Windows que representam servidores de back-end de aplicativos ("AppVM01", "AppVM02")
* Um servidor Windows que representa um servidor DNS ("DNS01")

Para ver scripts e um modelo do Azure Resource Manager, consulte as [instruções detalhadas de build][Example3].

#### <a name="udr-description"></a>Descrição de UDR
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

O VNETLocal é sempre um ou mais prefixos de endereço definidos que compõem a rede virtual para essa rede específica (ou seja, ele muda de rede virtual para rede virtual, dependendo de como cada rede virtual específica é definida). As rotas do sistema restantes são estáticas e padrão conforme indicado na tabela.

Neste exemplo, duas tabelas de roteamento são criadas, uma para a sub-rede de front-end e a outra para a sub-rede de back-end. Cada tabela é carregada com as rotas estáticas apropriadas para determinada sub-rede. Nesse exemplo, cada tabela tem três rotas que direcionam todo o tráfego (0.0.0.0/0) através do firewall (Próximo salto = endereço IP da Solução de Virtualização):

1. O tráfego de sub-rede local sem Próximo Salto definido para permitir que o tráfego de sub-rede local ignore o firewall.
2. Tráfego de rede virtual com um Próximo Salto definido como firewall. Esse próximo salto substitui a regra padrão que permite que o tráfego de Rede Virtual local seja roteado diretamente.
3. Todo o tráfego restante (0/0) com um Próximo Salto definido como o firewall.

> [!TIP]
> A falta da entrada de sub-rede local no UDR interrompe as comunicações na sub-rede local.
>
> * No nosso exemplo, 10.0.1.0/24 apontando para VNETLocal é fundamental! Sem ele, o pacote deixando o servidor Web (10.0.1.4) destinado a outro servidor local (por exemplo) 10.0.1.25 falhará, pois eles serão enviados para a NVA. A NVA o enviará para a sub-rede e a sub-rede o reenviará para a NVA em um loop infinito.
> * As chances de um loop de roteamento são geralmente maiores em dispositivos de várias NICs conectados a sub-redes separadas, o que é normalmente o caso de dispositivos locais tradicionais.
>
>

Depois que as tabelas de roteamento forem criadas, elas deverão ser associadas às respectivas sub-redes. A tabela de roteamento da sub-rede de front-end, uma vez criada e associada à sub-rede, teria esta aparência:

        Effective routes :
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active

> [!NOTE]
> O UDR agora pode ser aplicado à sub-rede de gateway a qual o circuito de ExpressRoute está conectado.
>
> Exemplos de como habilitar a rede de perímetro com a rede site a site ou de ExpressRoute são mostrados nos exemplos 3 e 4.
>
>

#### <a name="ip-forwarding-description"></a>Descrição de Encaminhamento IP
O Encaminhamento IP é um recurso complementar para o UDR. O Encaminhamento de IP é uma configuração em uma solução de virtualização que permite receber o tráfego endereçado não especificamente para a solução e, em seguida, encaminhar esse tráfego para seu destino final.

Por exemplo, se AppVM01 fizesse uma solicitação para o servidor DNS01, o UDR rotearia esse tráfego para o firewall. Com o Encaminhamento IP habilitado, o tráfego para o destino DNS01 (10.0.2.4) será aceito pelo dispositivo (10.0.0.4) e então encaminhado para seu destino final (10.0.2.4). Sem o Encaminhamento IP habilitado no firewall, o tráfego não seria aceito pela solução, embora a tabela de rota tenha o firewall como o próximo salto. Para usar uma solução de virtualização, é essencial lembrar-se de habilitar o Encaminhamento IP em conjunto com o UDR.

#### <a name="nsg-description"></a>Descrição de NSG
Neste exemplo, um grupo NSG é criado e então carregado com uma única regra. Esse grupo é então associado somente às sub-redes de front-end e back-end (e não a SecNet). Declarativamente, a seguinte regra está sendo criada:

* Todo o tráfego (todas as portas) da Internet para a rede virtual inteira (todas as sub-redes) é negado.

Embora NSGs sejam usados neste exemplo, seu principal objetivo será ser como uma segunda camada de defesa contra erros de configuração manual. A meta é bloquear todo o tráfego de entrada da Internet para as sub-redes de front-end ou back-end. O tráfego deve fluir somente pela sub-rede SecNet para o firewall (e, se apropriado, continuar para as sub-redes de front-end ou back-end). Além disso, com as regras UDR em vigor, qualquer tráfego que tenha conseguido passar para as sub-redes de front-end ou de back-end seria direcionado para o firewall (graças ao UDR). O firewall veria esse tráfego como um fluxo assimétrico e descartaria o tráfego de saída. Assim, há três camadas de segurança protegendo as sub-redes:

* Não há endereços IP públicos em nenhuma NIC de front-end ou back-end.
* NSGs negando o tráfego da Internet.
* O firewall soltando tráfego assimétrico.

Um ponto interessante sobre o NSG neste exemplo é que ele contém apenas uma regra, que é negar o tráfego da Internet para toda a rede virtual, incluindo a sub-rede de Segurança. No entanto, como o NSG está associado apenas às sub-redes de front-end e de back-end, a regra não é processada em tráfego de entrada para a sub-rede de Segurança. Como resultado, o tráfego flui para a sub-rede de segurança.

#### <a name="firewall-rules"></a>Regras de firewall
No firewall, será necessário criar regras de encaminhamento. Uma vez que o firewall está bloqueando ou encaminhando todos os tráfegos de entrada, de saída e entre redes virtuais, serão necessárias muitas regras de firewall. Além disso, todo o tráfego de entrada atinge o endereço IP público do Serviço de Segurança (em portas diferentes) para ser processado pelo firewall. Uma prática recomendada é criar um diagrama dos fluxos lógicos antes de configurar as sub-redes e as regras de firewall, para evitar uma reformulação posterior. A figura a seguir é uma exibição lógica das regras de firewall para este exemplo:

![10]

> [!NOTE]
> Com base na Solução de Virtualização de Rede usada, as portas de gerenciamento variam. Neste exemplo, um Firewall NextGen Barracuda é mencionado e usa as portas 22, 801 e 807. Consulte a documentação do fornecedor do dispositivo para localizar as portas exatas usadas para o gerenciamento do dispositivo usado.
>
>

#### <a name="firewall-rules-description"></a>Descrição das regras de firewall
No diagrama lógico anterior, a sub-rede de segurança não é mostrada porque o firewall é o único recurso nessa sub-rede. O diagrama mostra as regras de firewall e como elas permitem ou negam logicamente os fluxos de tráfego, mas não o caminho roteado real. Além disso, as portas externas selecionadas para o tráfego RDP são portas com um intervalo maior (8014 – 8026) e foram selecionadas para alinhar um pouco com os dois últimos octetos do endereço IP local para facilitar a leitura (por exemplo, o endereço do servidor local 10.0.1.4 está associado à porta externa 8014). No entanto, todas as portas não conflitantes acima disso poderiam ser usadas.

Para este exemplo, precisamos de sete tipos de regras:

* Regras externas (para o tráfego de entrada):
  1. Regra de firewall de gerenciamento: essa regra de Redirecionamento de Aplicativo permite que o tráfego passe para as portas de gerenciamento do dispositivo virtual de rede.
  2. Regras de RDP (para cada servidor do Windows): Essas quatro regras (uma para cada servidor) permitem o gerenciamento dos servidores individuais via RDP. As quatro regras de RDP também poderiam ser recolhidas em uma regra, dependendo dos recursos da solução de virtualização de rede usada.
  3. Regras de tráfego do aplicativo: Há duas regras desse tipo, o primeiro para o tráfego da web de front-end e o segundo para o tráfego de back-end (por exemplo, o servidor web para a camada de dados). A configuração dessas regras depende da arquitetura de rede (na qual os servidores são colocados) e dos fluxos de tráfego (a direção na qual o tráfego flui e quais portas são usadas).
     * A primeira regra permite que o tráfego de aplicativo real alcance o servidor de aplicativos. Embora as outras regras permitam segurança e gerenciamento, as regras de aplicativo são as que permitem que usuários ou serviços externos acessem os aplicativos. Neste exemplo, há um único servidor Web na porta 80. Assim, uma única regra de aplicativo de firewall redireciona o tráfego de entrada para o IP externo, para o endereço IP interno dos servidores Web. A sessão de tráfego redirecionado seria movida por meio de NAT para o servidor interno.
     * A segunda regra de tráfego é a regra de back-end que permite que o servidor Web converse com o servidor AppVM01 (mas não com AppVM02) por meio de qualquer porta.
* Regras internas (para tráfego entre redes virtuais)
  1. Saída a regra de Internet: Essa regra permite o tráfego de qualquer rede passe para as redes selecionadas. Normalmente, essa regra é uma regra padrão que já existe no firewall, mas em um estado desabilitado. Essa regra deve ser habilitada para esse exemplo.
  2. Regra DNS: essa regra permite que somente o tráfego DNS (porta 53) passe para o servidor DNS. Para esse ambiente, a maior parte do tráfego do front-end para o back-end está bloqueado. Essa regra permite especificamente DNS de qualquer sub-rede local.
  3. Regra sub-rede para sub-rede: Essa regra é permitir que qualquer servidor na sub-rede de back-end para se conectar a qualquer servidor na sub-rede de front-end (mas não o contrário).
* Regra à prova de falhas (para tráfego que não atenda a nenhum dos itens acima):
  1. Regra nega todo o tráfego: Essa regra de negação sempre deve ser a última regra (em termos de prioridade) e como tal, se um fluxo de tráfego falhar na correspondência a todas as regras anteriores serão descartados por essa regra. Essa regra é uma regra padrão e geralmente in-loco e ativa. Geralmente, nenhuma modificação é necessária para essa regra.

> [!TIP]
> Na segunda regra de tráfego de aplicativo, para simplificar este exemplo, qualquer porta é permitida. Em um cenário real, a porta mais específica e os intervalos de endereços devem ser usados para reduzir a superfície de ataque desta regra.
>
>

Depois que as regras anteriores forem criadas, será importante examinar a prioridade de cada regra para garantir que o tráfego seja permitido ou negado conforme desejado. Neste exemplo, as regras estão em ordem de prioridade.

#### <a name="conclusion"></a>Conclusão
Esse exemplo é uma maneira mais complexa, porém mais completa de proteger e isolar a rede do que as vistas nos exemplos anteriores. (o Exemplo 2 protege apenas o aplicativo e o Exemplo 1 isola apenas sub-redes). Esse design permite monitorar o tráfego em ambos os trajetos e protege não apenas o servidor de aplicativos de entrada, mas impõe políticas de segurança de rede para todos os servidores nessa rede. Além disso, dependendo do dispositivo usado, pode-se conseguir auditoria e reconhecimento total de tráfego. Para obter mais informações, consulte as [instruções detalhadas de build][Example3]. Essas instruções incluem:

* Como criar essa rede de perímetro de exemplo com scripts clássicos do PowerShell.
* Como criar esse exemplo com um modelo do Azure Resource Manager.
* As descrições detalhadas de cada comando NSG, UDR e regra de firewall.
* Cenários de fluxo de tráfego detalhados mostrando como o tráfego é permitido ou negado em cada camada.

### <a name="example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn"></a>Exemplo 4: adicionar uma conexão híbrida com uma VPN de solução de virtualização site a site
[Voltar ao Início rápido](#fast-start) | Instruções detalhadas de build estarão disponíveis em breve

[![11]][11]

#### <a name="environment-description"></a>Descrição do ambiente
Uma rede híbrida usando um NVA (solução de virtualização de rede) pode ser adicionada a qualquer um dos tipos de rede de perímetro descritas no exemplo 1, 2 ou 3.

Conforme mostrado na figura anterior, uma conexão VPN pela Internet (site a site) é usada para conectar uma rede local a uma rede virtual do Azure através de um NVA.

> [!NOTE]
> Se você usar o ExpressRoute com a opção de Emparelhamento Público do Azure habilitada, uma rota estática deverá ser criada. Essa rota estática deve rotear para o endereço IP de VPN de NVA fora de sua Internet corporativa e não através da conexão do ExpressRoute. A NAT necessária na opção de Emparelhamento Público de ExpressRoute do Azure pode interromper a sessão de VPN.
>
>

Depois que a VPN estiver no local, o NVA torna-se o hub central para todas as redes e sub-redes. As regras de encaminhamento de firewall determinam quais fluxos de tráfego são permitidos, convertidos via NAT, redirecionados ou removidos (mesmo para fluxos de tráfego entre a rede local e o Azure).

Os fluxos de tráfego devem ser considerados com cuidado, já que podem ser otimizados ou degradados por esse padrão de design dependendo do caso de uso específico.

Usar o ambiente criado no exemplo 3 e então adicionar uma conexão de rede híbrida VPN site a site gera o design a seguir:

[![12]][12]

O roteador local, ou outro dispositivo de rede que seja compatível com seu NVA para VPN, seria o cliente VPN. Este dispositivo físico seria responsável por iniciar e manter a conexão VPN com seu NVA.

Logicamente para o NVA, a rede é igual a quatro "zonas de segurança" separadas com as regras sobre o NVA sendo as principais orientadoras do tráfego entre essas zonas:

![13]

#### <a name="conclusion"></a>Conclusão
A adição de uma conexão de rede híbrida VPN site a site para uma rede virtual do Azure pode estender a rede local no Azure de maneira segura. Ao usar uma conexão VPN, o tráfego é criptografado e roteado pela Internet. O NVA neste exemplo fornece um local central para aplicar e gerenciar a política de segurança. Para obter mais informações, consulte as instruções detalhadas de build (próximo). Essas instruções incluem:

* Como criar essa rede de perímetro de exemplo com scripts do PowerShell.
* Como criar esse exemplo com um modelo do Azure Resource Manager.
* Cenários de fluxo de tráfego detalhados mostrando como o tráfego flui através desse design.

### <a name="example-5-add-a-hybrid-connection-with-a-site-to-site-azure-vpn-gateway"></a>Exemplo 5: adicionar uma conexão híbrida com um gateway de VPN site a site do Azure
[Voltar ao Início rápido](#fast-start) | Instruções detalhadas de build estarão disponíveis em breve

[![14]][14]

#### <a name="environment-description"></a>Descrição do ambiente
Uma rede híbrida usando um gateway de VPN do Azure pode ser adicionada a qualquer tipo de rede de perímetro descrito nos exemplos 1 ou 2.

Conforme mostrado na figura anterior, uma conexão VPN pela Internet (site a site) é usada para conectar uma rede local a uma rede virtual do Azure através de um gateway de VPN do Azure.

> [!NOTE]
> Se você usar o ExpressRoute com a opção de Emparelhamento Público do Azure habilitada, uma rota estática deverá ser criada. Essa rota estática deve rotear para o endereço IP de VPN de NVA fora de sua Internet corporativa e não através da WAN do ExpressRoute. A NAT necessária na opção de Emparelhamento Público de ExpressRoute do Azure pode interromper a sessão de VPN.
>
>

A figura a seguir mostra as duas bordas de rede nesse exemplo. Na primeira borda, o NVA e os NSGs controlam os fluxos de tráfego entre redes do Azure e entre o Azure e a Internet. A segunda borda é o gateway de VPN do Azure, que é uma borda de rede separada e isolada entre local e o Azure.

Os fluxos de tráfego devem ser considerados com cuidado, já que podem ser otimizados ou degradados por esse padrão de design dependendo do caso de uso específico.

Usar o ambiente criado no exemplo 1 e então adicionar uma conexão de rede híbrida VPN site a site gera o design a seguir:

[![15]][15]

#### <a name="conclusion"></a>Conclusão
A adição de uma conexão de rede híbrida VPN site a site para uma rede virtual do Azure pode estender a rede local no Azure de maneira segura. Usando o gateway de VPN do Azure nativo, o tráfego é criptografado com IPsec e roteado via Internet. Além disso, o uso de gateway de VPN do Azure pode oferecer uma opção de menor custo (sem custos com licença adicional ou NVAs de terceiros). Essa opção é mais econômica no exemplo 1, em que nenhuma NVA é usada. Para obter mais informações, consulte as instruções detalhadas de build (próximo). Essas instruções incluem:

* Como criar essa rede de perímetro de exemplo com scripts do PowerShell.
* Como criar esse exemplo com um modelo do Azure Resource Manager.
* Cenários de fluxo de tráfego detalhados mostrando como o tráfego flui através desse design.

### <a name="example-6-add-a-hybrid-connection-with-expressroute"></a>Exemplo 6: adicione uma conexão híbrida com o ExpressRoute
[Voltar ao Início rápido](#fast-start) | Instruções detalhadas de build estarão disponíveis em breve

[![16]][16]

#### <a name="environment-description"></a>Descrição do ambiente
Uma rede híbrida usando uma conexão de emparelhamento privado de ExpressRoute pode ser adicionada a qualquer tipo de rede de perímetro descrita nos exemplos 1 ou 2.

Conforme mostrado na figura anterior, o emparelhamento privado de ExpressRoute fornece uma conexão direta entre sua rede local e de rede virtual do Azure. O tráfego transmite apenas a rede do provedor de serviços e a rede do Microsoft Azure, nunca em contato com a Internet.

> [!TIP]
> Usar o ExpressRoute mantém o tráfego de rede corporativa fora da Internet. Ele também permite SLAs do seu provedor do ExpressRoute. O Gateway do Azure pode passar até 10 Gbps com o ExpressRoute, enquanto com VPNs site a site, a taxa de transferência máxima do Gateway do Azure é de 200 Mbps.
>
>

Conforme mostrado no diagrama abaixo, com essa opção, o ambiente agora tem duas bordas de rede. A NVA e NSG controlam fluxos de tráfego para redes internas do Azure e entre o Azure e a Internet, enquanto o gateway é uma borda de rede separada e isolada entre locais e o Azure.

Os fluxos de tráfego devem ser considerados com cuidado, já que podem ser otimizados ou degradados por esse padrão de design dependendo do caso de uso específico.

Usar o ambiente criado no exemplo 1 e então adicionar uma conexão de rede híbrida de ExpressRoute gera o design a seguir:

[![17]][17]

#### <a name="conclusion"></a>Conclusão
A adição de uma conexão de rede de emparelhamento privado de ExpressRoute pode estender a rede local para o Azure de forma segura, com baixa latência e alto desempenho. Além disso, o uso de Gateway do Azure nativo, como visto neste exemplo, fornece uma opção de menor custo (sem licenciamento adicional nem NVAs de terceiros). Para obter mais informações, consulte as instruções detalhadas de build (próximo). Essas instruções incluem:

* Como criar essa rede de perímetro de exemplo com scripts do PowerShell.
* Como criar esse exemplo com um modelo do Azure Resource Manager.
* Cenários de fluxo de tráfego detalhados mostrando como o tráfego flui através desse design.

## <a name="references"></a>Referências
### <a name="helpful-websites-and-documentation"></a>Sites úteis e documentação
* Acesse o Azure com o Azure Resource Manager:
* Ao acessar o Azure com o PowerShell: [https://docs.microsoft.com/powershell/azureps-cmdlets-docs/](/powershell/azure/overview)
* Documentação da Rede Virtual: [https://docs.microsoft.com/azure/virtual-network/](https://docs.microsoft.com/azure/virtual-network/)
* Documentação do grupo de Segurança de Rede: [https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg](virtual-network/security-overview.md)
* Documentação do roteamento definido pelo usuário: [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview](virtual-network/virtual-networks-udr-overview.md)
* Gateways virtuais do Microsoft Azure: [https://docs.microsoft.com/azure/vpn-gateway/](https://docs.microsoft.com/azure/vpn-gateway/)
* VPNs site a site: [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell](vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* Documentação da ExpressRoute (não deixe de conferir as seções "Introdução" e "Tutoriais"): [https://docs.microsoft.com/azure/expressroute/](https://docs.microsoft.com/azure/expressroute/)

<!--Image References-->
[0]: ./media/best-practices-network-security/flowchart.png "Fluxograma de opções de segurança"
[2]: ./media/best-practices-network-security/azuresecurityfeatures.png "Recursos de segurança do Azure"
[3]: ./media/best-practices-network-security/dmzcorporate.png "Uma DMZ em uma rede corporativa"
[4]: ./media/best-practices-network-security/azuresecurityarchitecture.png "Arquitetura de segurança do Azure"
[5]: ./media/best-practices-network-security/dmzazure.png "Uma DMZ em uma Rede Virtual do Azure"
[6]: ./media/best-practices-network-security/dmzhybrid.png "Rede híbrida com três limites de segurança"
[7]: ./media/best-practices-network-security/example1design.png "DMZ de entrada com NSG"
[8]: ./media/best-practices-network-security/example2design.png "DMZ de entrada com NVA e NSG"
[9]: ./media/best-practices-network-security/example3design.png "DMZ bidirecional com NVA, NSG e UDR"
[10]: ./media/best-practices-network-security/example3firewalllogical.png "Exibição lógica das regras de firewall"
[11]: ./media/best-practices-network-security/example3designoptions.png "DMZ com rede híbrida conectada com NVA"
[12]: ./media/best-practices-network-security/example4designs2s.png "DMZ com NVA conectado usando uma VPN site a site"
[13]: ./media/best-practices-network-security/example4networklogical.png "Rede lógica da perspectiva de NVA"
[14]: ./media/best-practices-network-security/example5designoptions.png "DMZ com rede híbrida site a site conectada ao Gateway do Azure"
[15]: ./media/best-practices-network-security/example5designs2s.png "DMZ com Gateway do Azure usando VPN site a site"
[16]: ./media/best-practices-network-security/example6designoptions.png "DMZ com rede híbrida do ExpressRoute conectada ao Gateway do Azure"
[17]: ./media/best-practices-network-security/example6designexpressroute.png "DMZ com Gateway do Azure usando uma conexão do ExpressRoute"

<!--Link References-->
[TrustCenter]: https://azure.microsoft.com/support/trust-center/compliance/
[Example1]: ./virtual-network/virtual-networks-dmz-nsg.md
[Example2]: ./virtual-network/virtual-networks-dmz-nsg-fw-asm.md
[Example3]: ./virtual-network/virtual-networks-dmz-nsg-fw-udr-asm.md
[Example4]: ./virtual-network/virtual-networks-hybrid-s2s-nva-asm.md
[Example5]: ./virtual-network/virtual-networks-hybrid-s2s-agw-asm.md
[Example6]: ./virtual-network/virtual-networks-hybrid-expressroute-asm.md
[Example7]: ./virtual-network/virtual-networks-vnet2vnet-direct-asm.md
[Example8]: ./virtual-network/virtual-networks-vnet2vnet-transit-asm.md

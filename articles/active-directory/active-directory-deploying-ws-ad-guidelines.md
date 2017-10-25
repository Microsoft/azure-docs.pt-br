---
title: "Diretrizes para implantação do Active Directory do Windows Server em Máquinas Virtuais do Azure | Microsoft Docs"
description: "Se você sabe como implantar serviços de domínio do AD e serviços de federação do AD no local, saiba como eles funcionam em máquinas virtuais do Azure."
services: active-directory
documentationcenter: 
author: femila
manager: femila
editor: 
ms.assetid: 04df4c46-e6b6-4754-960a-57b823d617fa
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/26/2017
ms.author: femila
ms.openlocfilehash: 342d9e2787add3d04f1b744152e135db98848179
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="guidelines-for-deploying-windows-server-active-directory-on-azure-virtual-machines"></a>Diretrizes para implantar o Active Directory do Windows Server em máquinas virtuais do Azure
Este artigo explica as diferenças importantes entre implantar o AD DS (Serviços de Domínio do Active Directory) do Windows Server e o AD FS (Serviços de Federação do Active Directory) no local e implantá-los em máquinas virtuais do Microsoft Azure.

## <a name="scope-and-audience"></a>Escopo e público
O artigo destina-se àqueles que já têm experiência com implantação local do Active Directory. São abordadas as diferenças entre a implantação do Active Directory em máquinas virtuais do Microsoft Azure/redes virtuais do Azure e implantações tradicionais locais do Active Directory. As máquinas virtuais do Azure e redes virtuais do Azure fazem parte de uma oferta IaaS (Infraestrutura como Serviço) para as organizações aproveitarem os recursos de computação na nuvem.

Para aqueles que não estão familiarizados com a implantação do AD, confira o [Guia de Implantação do AD DS](https://technet.microsoft.com/library/cc753963) ou [Planejar sua implantação do AD FS](https://technet.microsoft.com/library/dn151324.aspx), conforme apropriado.

Este artigo pressupõe que o leitor está familiarizado com os seguintes conceitos:

* Gerenciamento e implantação do AD DS do Windows Server
* Implantação e configuração do DNS para dar suporte a uma infraestrutura do AD DS do Windows Server
* Gerenciamento e implantação do AD FS do Windows Server
* Implantando, configurando e gerenciando aplicativos confiáveis (sites e serviços Web) que podem consumir tokens do AD FS do Windows Server
* Conceitos gerais de máquina virtual, por exemplo, como configurar uma máquina virtual, discos virtuais e redes virtuais

Este artigo destaca os requisitos para um cenário de implantação híbrida no qual o AD DS ou o AD FS do Windows Server são implantados no local e parcialmente em máquinas virtuais do Azure. O documento aborda primeiro as diferenças críticas entre a execução do AD DS e do AD FS do Windows Server em máquinas virtuais do Azure versus locais e pontos de decisão importantes que afetam o design e a implantação. O restante do artigo explica como aplicar as diretrizes para vários cenários de implantação e para cada um dos pontos de decisão mais detalhadamente.

Este artigo não aborda a configuração do [Azure Active Directory](http://azure.microsoft.com/services/active-directory/), que é um serviço baseado em REST que fornece recursos de controle de acesso e gerenciamento de identidade para aplicativos em nuvem. O Azure Active Directory (Azure AD) e o AD DS do Windows Server, no entanto, são projetados para funcionar juntos a fim de fornecer uma solução de gerenciamento de identidades e acesso para os ambientes híbridos de TI atuais e aplicativos modernos. Para ajudar a compreender as diferenças e as relações entre o AD DS do Windows Server e o Azure AD, considere o seguinte:

1. Você pode executar o AD DS do Windows Server na nuvem em máquinas virtuais do Azure quando está usando o Azure a fim de estender seu datacenter local para a nuvem.
2. Você pode usar o Azure AD para fornecer aos usuários um logon único de aplicativos de Software hospedado como serviço (SaaS). Por exemplo, o Microsoft Office 365 utiliza essa tecnologia, e aplicativos executados no Azure ou outras plataformas na nuvem também podem usá-lo.
3. Você pode usar o AD do Azure (o Serviço de Controle de Acesso) para permitir que os usuários entrem usando identidades do Facebook, do Google, da Microsoft e de outros provedores de identidade para aplicativos hospedados na nuvem ou no local.

Para saber mais sobre essas diferenças, confira [Identidade do Azure](fundamentals-identity.md).

## <a name="related-resources"></a>Recursos relacionados
Você pode baixar e executar a [Avaliação de Prontidão da Máquina Virtual do Azure](https://www.microsoft.com/download/details.aspx?id=40898). A avaliação inspecionará o ambiente local e gerará um relatório personalizado automaticamente com base nas diretrizes encontradas neste tópico para ajudá-lo a migrar o ambiente para o Azure.

Recomendamos que você examine primeiro os tutoriais, guias e vídeos que abrangem os tópicos abaixo:

* [Configurar uma rede virtual somente em nuvem no Portal do Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)
* [Configurar uma VPN Site a Site no Portal do Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md)
* [Instalar uma nova floresta do Active Directory em uma rede virtual do Azure](active-directory-new-forest-virtual-machine.md)
* [Instalar uma réplica do controlador de domínio do Active Directory no Azure](active-directory-install-replica-active-directory-domain-controller.md)
* [Microsoft Azure IaaS para profissionais de TI: (01) conceitos básicos de máquina virtual](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
* [Microsoft Azure IaaS para profissionais de TI: (05) Criando redes virtuais e conectividade entre instalações](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

## <a name="introduction"></a>Introdução
Os requisitos fundamentais para a implantação do Active Directory do Windows Server em máquinas virtuais do Azure diferem muito pouco de implantá-lo em máquinas virtuais locais (e, até certo ponto, máquinas físicas). Por exemplo, no caso do AD DS do Windows Server, se os controladores de domínio que você implantar em máquinas virtuais do Azure forem réplicas em um domínio corporativo/floresta local, a implantação do Azure poderá ser tratada em grande parte da mesma maneira que qualquer outro site adicional do Active Directory do Windows Server. Isto é, as sub-redes devem ser definidas no AD DS do Windows Server, um site deve ser criado, as sub-redes devem ser vinculadas a esse site e conectadas a outros sites usando links de site apropriados. No entanto, existem algumas diferenças que são comuns a todas as implantações do Azure e algumas que variam de acordo com o cenário de implantação específico. Duas diferenças fundamentais são demonstradas abaixo:

### <a name="azure-virtual-machines-may-need-connectivity-to-the-on-premises-corporate-network"></a>As máquinas virtuais do Azure podem precisar de conectividade com a rede corporativa local.
A conexão de máquinas virtuais do Azure a uma rede corporativa local requer a rede virtual do Azure, que inclui um componente VPN (rede virtual privada) site a site ou ponto a site capaz de conectar perfeitamente às máquinas virtuais do Azure e às máquinas locais. Esse componente VPN também pode permitir que computadores de membro de domínio local acessem um domínio do Active Directory do Windows Server cujos controladores de domínio estejam hospedados exclusivamente em máquinas virtuais do Azure. É importante observar, porém, que se a VPN falhar, a autenticação e outras operações que dependem do Active Directory do Windows Server também falharão. Embora os usuários possam conseguir entrar usando credenciais existentes armazenadas em cache, todas as tentativas de autenticação ponto a ponto ou cliente-servidor, para as quais tíquetes ainda precisam ser emitidos ou se tornaram obsoletos, falharão.

Confira [Rede Virtual](http://azure.microsoft.com/documentation/services/virtual-network/) para ver uma demonstração em vídeo e uma lista de tutoriais passo a passo, incluindo [Configurar uma VPN Site a Site no portal do Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md).

> [!NOTE]
> Você também pode implantar o Active Directory do Windows Server em uma rede virtual do Azure que não tenha conectividade com uma rede local. As diretrizes neste tópico, no entanto, supõem que uma rede virtual do Azure esteja sendo usada pois ela fornece recursos de endereçamento IP que são essenciais para o Windows Server.
> 
> 

### <a name="static-ip-addresses-must-be-configured-with-azure-powershell"></a>Os endereços IP estáticos devem ser configurados com o Azure PowerShell.
Os endereços dinâmicos são alocados por padrão, mas usam o cmdlet Set-AzureStaticVNetIP para atribuir um endereço IP estático em vez disso. Que define um endereço IP que será mantido por meio da recuperação de serviço e desligamento/reinício da VM. Para saber mais, confira [Endereço IP interno estático para máquinas virtuais](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/).

## <a name="BKMK_Glossary"></a>Termos e definições
A seguir temos uma lista parcial dos termos de várias tecnologias do Azure que serão referenciados neste artigo.

* **Máquinas virtuais do azure**: a oferta IaaS no Azure que permite que os clientes implantem VMs que executam praticamente qualquer carga de trabalho tradicionalmente no servidor local.
* **Rede virtual do Azure**: o serviço de rede no Azure que permite que os clientes criem e gerenciem redes virtuais no Azure e as vinculem com segurança às suas próprias infraestruturas de rede locais usando uma VPN (rede virtual privada).
* **Endereço IP virtual**: um endereço IP da Internet que não está associado a uma placa de interface de rede ou computador específico. Os serviços de nuvem recebem um endereço IP virtual para receber o tráfego de rede que é redirecionado para uma VM do Azure. Um endereço IP virtual é uma propriedade de um serviço de nuvem que pode conter uma ou mais máquinas virtuais do Azure. Observe também que uma rede virtual do Azure pode conter um ou mais serviços de nuvem. Os endereços IP virtuais fornecem recursos nativos de balanceamento de carga.
* **Endereço IP dinâmico**: esse é o endereço IP que é interno apenas. Ele deve ser configurado como um endereço IP estático (usando o cmdlet Set-AzureStaticVNetIP) para VMs que hospedam as funções de servidor DNS/DC.
* **Recuperação de serviço**: o processo no qual o Azure automaticamente retorna novamente um serviço para o estado em execução após detectar sua falha. A recuperação de serviço é um dos aspectos do Azure que oferece suporte à disponibilidade e resiliência. Embora seja improvável, o resultado após um incidente de recuperação para um controlador de domínio em execução em uma máquina virtual de serviço é semelhante a uma reinicialização não planejada, mas tem alguns efeitos colaterais:
  
  * O adaptador de rede virtual na máquina virtual será alterado
  * O endereço MAC do adaptador de rede virtual será alterado
  * A ID de processador/CPU da VM será alterada
  * A configuração do IP do adaptador de rede virtual não será alterado desde que a VM esteja conectada a uma rede virtual e o endereço IP da VM seja estático.
  
  Nenhum desses comportamentos afetam o Active Directory do Windows Server porque ele não tem dependências em endereço MAC ou ID de processador/CPU. Além disso, todas as implantações do Active Directory do Windows Server no Azure são recomendadas para execução em uma rede virtual do Azure, conforme descrito acima.

## <a name="is-it-safe-to-virtualize-windows-server-active-directory-domain-controllers"></a>É seguro virtualizar os controladores de domínio do Active Directory do Windows Server?
A implantação de controladores de domínio do Active Directory do Windows Server em máquinas virtuais do Azure está sujeito às mesmas diretrizes da execução de controladores de domínio locais em uma máquina virtual. Executar controladores de domínio virtualizados é uma prática de segurança desde que sejam seguidas as diretrizes para fazer backup e restaurar controladores de domínio. Para saber mais sobre restrições e diretrizes para a execução de controladores de domínio virtualizados, confira [Executando Controladores de Domínio no Hyper-V](https://technet.microsoft.com/library/dd363553).

Os hipervisores fornecem ou banalizam tecnologias que podem causar problemas para muitos sistemas distribuídos, incluindo o Active Directory do Windows Server. Por exemplo, em um servidor físico, você pode clonar um disco ou usar métodos sem suporte para reverter o estado de um servidor, inclusive usar SANs e outros, mas fazer isso em um servidor físico é muito mais difícil do que restaurar um instantâneo de máquina virtual em um hipervisor. O Azure oferece funcionalidades que podem resultar na mesma condição indesejável. Por exemplo, você não deve copiar arquivos VHD de controladores de domínio em vez de executar backups regulares porque restaurá-los pode resultar em uma situação semelhante ao uso dos recursos de restauração de instantâneo.

Essas reversões apresentam bolhas de USN que podem levar a estados permanentemente divergentes entre controladores de domínio. Isso pode causar problemas como:

* Objetos persistentes
* Senhas inconsistentes
* Valores de atributo inconsistentes
* O esquema não corresponde se o mestre de esquema é revertido

Para saber mais sobre como os controladores de domínio são afetados, confira [USN e Reversão do USN](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv.aspx#usn_and_usn_rollback).

A partir do Windows Server 2012, [mais proteções adicionais são incorporadas ao AD DS](https://technet.microsoft.com/library/hh831734.aspx). Essas proteções ajudam a proteger os controladores de domínio virtualizados contra os problemas mencionados anteriormente, desde que a plataforma do hipervisor subjacente ofereça suporte a VM-GenerationID. O Azure dá suporte a VM-GenerationID, o que significa que os controladores de domínio que executam o Windows Server 2012 ou posterior nas máquinas virtuais do Azure têm as proteções adicionais.

> [!NOTE]
> Você deve desligar e reiniciar uma VM que executa a função de controlador de domínio no Azure no sistema operacional convidado em vez de usar a opção **Desligar** no Portal do Azure. Hoje em dia, o uso do portal para desligar uma VM faz com que a VM seja desalocada. Uma VM desalocada tem a vantagem de não incorrer em encargos, mas também redefine a VM-GenerationID, o que não é desejável para um controlador de domínio. Quando a VM-GenerationID é redefinida, a invocationID do banco de dados do AD DS também é redefinida, a pool RID é descartada e SYSVOL é marcado como não autoritativo. Para saber mais, confira [Introdução à Virtualização do AD DS (Active Directory Domain Services)](https://technet.microsoft.com/library/hh831734.aspx) e [Virtualização de DFSR com Segurança](http://blogs.technet.com/b/filecab/archive/2013/04/05/safely-virtualizing-dfsr.aspx).
> 
> 

## <a name="why-deploy-windows-server-ad-ds-on-azure-virtual-machines"></a>Por que implantar o AD DS do Windows Server em Máquinas Virtuais do Azure?
Muitos cenários de implantação do AD DS do Windows Server são apropriados para implantação como máquinas virtuais no Azure. Por exemplo, suponha que você tenha uma empresa na Europa que precisa autenticar usuários em um local remoto na Ásia. A empresa não implantou controladores de domínio do Active Directory do Windows Server anteriormente na Ásia devido a custos para implantá-los e conhecimento limitado para gerenciar a pós-implantação de servidores. Como resultado, as solicitações de autenticação da Ásia são atendidas pelos controladores de domínio na Europa com resultados de qualidade inferiores. Nesse caso, é possível implantar um controlador de domínio em uma máquina virtual que você especificou que deve ser executada no datacenter do Azure na Ásia. A anexação do controlador de domínio a uma rede virtual do Azure que está conectada diretamente ao local remoto melhorará o desempenho da autenticação.

O Azure também é adequado como um substituto para sites de DR (recuperação de desastres) dispendiosos. O custo relativamente baixo de hospedar um pequeno número de controladores de domínio e uma única rede virtual no Azure representa uma alternativa atraente.

Finalmente, talvez seja bom implantar um aplicativo de rede no Azure, como o SharePoint, que requer o Active Directory do Windows Server, mas não tem qualquer dependência na rede local ou no Active Directory corporativo do Windows Server. Nesse caso, implantar uma floresta isolada no Azure para atender aos requisitos do SharePoint Server é ideal. Repetimos, também há suporte para a implantação de aplicativos de rede que exigem conectividade com a rede local e o Active Directory corporativo.

> [!NOTE]
> Como ele fornece uma conexão de camada 3, o componente VPN que fornece conectividade entre uma rede virtual do Azure e uma rede local também pode habilitar os servidores de membro que são executados no local para aproveitar os controladores de domínio executados como máquinas virtuais do Azure na rede virtual do Azure. Mas, se a VPN estiver disponível, a comunicação entre computadores locais e controladores de domínio baseados no Azure não funcionará, resultando em erros de autenticação e vários outros.  
> 
> 

## <a name="contrasts-between-deploying-windows-server-active-directory-domain-controllers-on-azure-virtual-machines-versus-on-premises"></a>Contrastes entre a implantação de controladores de domínio do Active Directory do Windows Server em máquinas virtuais do Azure versus localmente
* Para qualquer cenário de implantação do Active Directory do Windows Server que inclua mais de uma única VM, é necessário usar uma rede virtual do Azure para manter a consistência do endereço IP. Observe que este guia pressupõe que os controladores de domínio estejam sendo executados em uma rede virtual do Azure.
* Como ocorre com os controladores de domínio locais, recomenda-se o uso de endereços IP estáticos. Um endereço IP estático só pode ser configurado usando o Azure PowerShell. Confira [Endereço IP estático interno para VMs](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/) para obter mais detalhes. Se você tiver sistemas de monitoramento ou outras soluções que verificam a configuração de endereço IP estático no sistema operacional convidado, poderá atribuir o mesmo endereço IP estático às propriedades do adaptador de rede da máquina virtual. No entanto, lembre-se de que o adaptador de rede será descartado se a VM passar por uma recuperação de serviço ou for desligada no portal e seu endereço for desalocado. Nesse caso, o endereço IP estático no convidado precisará ser redefinido.
* Implantar máquinas virtuais em uma rede virtual não implica (ou exige) conectividade com uma rede local. A rede virtual simplesmente habilita essa possibilidade. Você deve criar uma rede virtual para comunicação privada entre o Azure e sua rede local. Você precisa implantar um ponto de extremidade VPN na rede local. A VPN é aberta a partir do Azure para a rede local. Para saber mais sobre como criar essa rede virtual, confira [Visão geral da Rede Virtual](../virtual-network/virtual-networks-overview.md) e [Configurar um VPN Site a Site no portal do Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md).

> [!NOTE]
> Uma opção para [criar uma VPN ponto a site](../vpn-gateway/vpn-gateway-point-to-site-create.md) está disponível para conectar computadores individuais do Windows diretamente a uma rede virtual do Azure.
> 
> 

* Independentemente de criar uma rede virtual ou não, o Azure cobra pelo tráfego de saída, mas não pelo de entrada. Várias opções de design do Active Directory do Windows Server podem afetar a quantidade de tráfego de saída gerada por uma implantação. Por exemplo, a implantação de um RODC (controlador de domínio somente leitura) limita o tráfego de saída porque ele não replica a saída. Mas a decisão de implantar um RODC precisa ser avaliada em relação à necessidade de executar operações de gravação no controlador de domínio e a [compatibilidade](https://technet.microsoft.com/library/cc755190) dos aplicativos e serviços no site com RODCs. Para saber mais sobre encargos de tráfego, confira [Visão geral de preços do Azure](http://azure.microsoft.com/pricing/).
* Embora você tenha controle total sobre qual servidor de recursos usar para VMs locais, por exemplo, RAM, tamanho do disco e afins, no Azure, deverá escolher em uma lista de tamanhos de servidor predefinidos. Para um controlador de domínio, um disco de dados é necessário além do disco do sistema operacional para armazenar o banco de dados do Active Directory do Windows Server.

## <a name="can-you-deploy-windows-server-ad-fs-on-azure-virtual-machines"></a>É possível implantar o AD FS do Windows Server em máquinas virtuais do Azure?
Sim, você pode implantar o AD FS do Windows Server em máquinas virtuais do Azure e as [práticas recomendadas para implantação do AD FS](https://technet.microsoft.com/library/dn151324.aspx) local aplicam-se igualmente à implantação do AD FS no Azure. Mas algumas das práticas recomendadas, como o balanceamento de carga e a alta disponibilidade, exigem tecnologias além das que o AD FS oferece. Elas devem ser fornecidas pela infraestrutura subjacente. Vamos examinar algumas dessas práticas recomendadas e ver como elas podem ser alcançadas usando VMs do Azure e uma rede virtual do Azure.

1. **Nunca exponha servidores STSs (serviço de token de segurança) diretamente na Internet.**
   
    Isso é importante porque o STS emite tokens de segurança. Como resultado, os servidores de STS, como servidores AD FS, devem ser tratados com o mesmo nível de proteção de um controlador de domínio. Se um STS estiver comprometido, usuários mal-intencionados conseguirão emitir tokens de acesso que podem conter declarações de sua escolha para aplicativos confiáveis e outros servidores de STS em organizações confiáveis.
2. **Implantar controladores de domínio do Active Directory para todos os domínios de usuário na mesma rede que os servidores do AD FS.**
   
    Os servidores AD FS usam Serviços de Domínio do Active Directory para autenticar usuários. É recomendável implantar controladores de domínio na mesma rede que os servidores AD FS. Isso fornecerá continuidade de negócios no caso do link entre a rede do Azure e sua rede local ser interrompido e permitirá maior desempenho para logons e menor latência.
3. **Implante vários nós do AD FS para alta disponibilidade e balanceamento de carga.**
   
    Na maioria dos casos, a falha de um aplicativo que o AD FS permite é inaceitável porque os aplicativos que exigem tokens de segurança geralmente são de missão crítica. Como resultado, e como o AD FS agora reside no caminho crítico para acessar aplicativos de missão crítica, o serviço AD FS deve estar altamente disponível por meio de vários proxies do AD FS e servidores do AD FS. Para obter a distribuição de solicitações, os balanceadores de carga normalmente são implantados na frente de proxies do AD FS e de servidores do AD FS.
4. **Implante um ou mais nós de Proxy de Aplicativo Web para acessar a Internet.**
   
    Quando os usuários precisam acessar aplicativos protegidos pelo serviço do AD FS, este precisa estar disponível na Internet. Isso é feito ao implantar o serviço de Proxy de Aplicativo Web. É altamente recomendável implantar mais de um nó para fins de alta disponibilidade e balanceamento de carga.
5. **Restringir o acesso aos recursos da rede internos a partir de nós de Proxy de Aplicativo Web.**
   
    Para permitir que usuários externos acessem o AD FS a partir da Internet, você precisará implantar nós de Proxy de Aplicativo Web (ou Proxy do AD FS em versões anteriores do Windows Server). Os nós de proxy de Aplicativo Web são diretamente expostos à Internet. Eles não têm que estar ingressados no domínio e só precisam acessar os servidores do AD FS em portas TCP 443 e 80. É altamente recomendável que a comunicação com todos os outros computadores (especialmente controladores de domínio) esteja bloqueada.
   
    Isso é normalmente obtido no local por meio de uma DMZ. Os firewalls usam um modo de lista branca de operação para restringir o DMZ à rede local (ou seja, somente o tráfego de endereços IP especificados e portas específicas é permitido e todos os outros tráfegos são bloqueados).

O diagrama a seguir mostra uma implantação tradicional do AD FS no local.

![Diagrama de uma implantação tradicional dos Serviços de Federação do Active Directory local](media/active-directory-deploying-ws-ad-guidelines/ADFS_onprem.png)

No entanto, como o Azure não fornece recurso de firewall completo nativo, outras opções devem ser usadas para restringir o tráfego. A tabela a seguir mostra cada opção e suas vantagens e desvantagens.

| Opção | Vantagem | Desvantagem |
| --- | --- | --- |
| [ACLs da rede do Azure](../virtual-network/virtual-networks-acl.md) |Configuração inicial mais simples e com menor custo |Configuração adicional de ACL de rede necessária se novas VMs são adicionadas à implantação |
| [Barracuda NG Firewall](https://www.barracuda.com/products/ngfirewall) |Modo de lista branca de operação e não requer configurações de ACL de rede |Aumento de custo e complexidade da configuração inicial |

As etapas de alto nível para implantar o AD FS neste caso são as seguintes:

1. Crie uma rede virtual com conectividade entre locais usando uma VPN ou a [ExpressRoute](http://azure.microsoft.com/services/expressroute/).
2. Implante controladores de domínio em uma rede virtual. Esta etapa é opcional, mas recomendada.
3. Implante servidores do AD FS ingressados no domínio na rede virtual.
4. Crie um [conjunto de balanceamento de carga interno](http://azure.microsoft.com/blog/internal-load-balancing/) que inclua os servidores do AD FS e utilize um novo endereço IP privado na rede virtual (um endereço IP dinâmico).
   
   1. Atualize o DNS a fim de criar o FQDN para apontar para o endereço IP privado (dinâmico) do conjunto de balanceamento de carga interno.
5. Crie um serviço de nuvem (ou uma rede virtual separada) para os nós de Proxy de Aplicativo Web.
6. Implantar os nós de Proxy de Aplicativo Web no serviço de nuvem ou na rede virtual
   
   1. Crie um conjunto de balanceamento de carga externo que inclua os nós de Proxy de Aplicativo Web.
   2. Atualize o nome DNS externo (FQDN) para apontar para o endereço IP público do serviço de nuvem (endereço IP virtual).
   3. Configure os proxies do AD FS para usar o FQDN que corresponde ao conjunto de balanceamento de carga interno para os servidores do AD FS.
   4. Atualize sites baseados em declarações para usar o FQDN externo para seu provedor de declarações.
7. Restrinja o acesso entre o Proxy de Aplicativo da Web para qualquer computador na rede virtual do AD FS.

Para restringir o tráfego, o conjunto de balanceamento de carga do balanceador de carga interno do Azure precisa ser configurado somente em relação ao tráfego para as portas TCP 80 e 443 e todos os outros tráfegos para o endereço IP dinâmico interno do conjunto de balanceamento de carga são descartados.

![Diagrama de ACLs de Rede ADFS com TCP 443 + 80 permitido.](media/active-directory-deploying-ws-ad-guidelines/ADFS_ACLs.png)

O tráfego para os servidores do AD FS seria permitido apenas para as seguintes origens:

* O balanceador de carga interno do Azure.
* O endereço IP de um administrador da rede local.

> [!WARNING]
> O design deve impedir que nós de Proxy de Aplicativo Web acessem todas as outras VMs na rede virtual do Azure ou todos os locais na rede local. Isso pode ser feito configurando regras de firewall no dispositivo local para conexões do ExpressRoute ou no dispositivo VPN para conexões VPN site a site.
> 
> 

Uma desvantagem dessa opção é a necessidade de configurar ACLs de rede para vários dispositivos, incluindo o balanceador de carga interno, os servidores do AD FS e outros servidores que são adicionados à rede virtual. Se qualquer dispositivo for adicionado à implantação sem configurar ACLs de rede para restringir o tráfego para ele, toda a implantação poderá ficar em risco. Se os endereços IP de nós de Proxy de Aplicativo Web nunca forem alterados, as ACLs de rede deverão ser redefinidas (o que significa que os proxies devem ser configurados para usar [endereços IP dinâmicos estáticos](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/)).

![ADFS no Azure com ACLS de rede.](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure.png)

Outra opção é usar o dispositivo [Barracuda NG Firewall](https://www.barracuda.com/products/ngfirewall) para controlar o tráfego entre servidores de proxy do AD FS e servidores do AD FS. Esta opção está em conformidade com as práticas recomendadas para segurança e alta disponibilidade e requer menos administração após a configuração inicial porque o dispositivo Barracuda NG Firewall oferece um modo de lista branca de administração do firewall e pode ser instalado diretamente em uma rede virtual do Azure. Isso elimina a necessidade de configurar ACLs de rede sempre que um novo servidor for adicionado à implantação. Mas essa opção adiciona custo e complexidade à implantação inicial.

Nesse caso, duas redes virtuais são implantadas em vez de uma. Vamos chamá-las de VNet1 e VNet2. VNet1 contém os proxies e VNet2 contém os STSs e a conexão de rede para a rede corporativa. VNet1 é, portanto, fisicamente (embora virtualmente) isolada da VNet2 e, por sua vez, da rede corporativa. VNet1 é conectada a VNet2 usando uma tecnologia de túnel especial conhecida como TINA (Transport Independent Network Architecture). O túnel TINA é anexado a cada uma das redes virtuais usando um firewall Barracuda NG: um Barracuda em cada uma das redes virtuais.  Para alta disponibilidade, é recomendável que você implante dois Barracudas em cada rede virtual, sendo um ativo e outro passivo. Eles oferecem recursos de firewall extremamente avançados que nos permitem simular a operação de um DMZ local tradicional no Azure.

![ADFS no Azure com firewall.](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure_firewall.png)

Para saber mais, confira [AD FS: estender um aplicativo front-end local com reconhecimento de declarações para a Internet](#BKMK_CloudOnlyFed).

### <a name="an-alternative-to-ad-fs-deployment-if-the-goal-is-office-365-sso-alone"></a>Uma alternativa para a implantação do AD FS se a meta for apenas o SSO do Office 365
Há outra alternativa para implantar totalmente o AD FS se sua meta é apenas habilitar a entrada no Office 365. Nesse caso, basta implantar o DirSync com sincronização de senha no local e obter o mesmo resultado final com o mínimo de complexidade de implantação porque essa abordagem não exige o AD FS ou o Azure.

A tabela a seguir compara como os processos de logon funcionam com e sem implantar o AD FS.

| Logon único do Office 365 usando o AD FS e DirSync | Mesmo logon do Office 365 usando DirSync + Sincronização de Senha |
| --- | --- |
| 1. O usuário faz logon em uma rede corporativa e é autenticado no Active Directory do Windows Server. |1. O usuário faz logon em uma rede corporativa e é autenticado no Active Directory do Windows Server. |
| 2. O usuário tenta acessar o Office 365 (eu sou @contoso.com). |2. O usuário tenta acessar o Office 365 (eu sou @contoso.com). |
| 3. O Office 365 redireciona o usuário para o Azure AD. |3. O Office 365 redireciona o usuário para o Azure AD. |
| 4. Como o Azure AD não pode autenticar o usuário e entende que há uma relação de confiança com o AD FS local, ele redireciona o usuário para o AD FS. |4. O Azure AD não pode aceitar tíquetes Kerberos diretamente e não existe qualquer relação de confiança para solicitar que o usuário insira credenciais. |
| 5. O usuário envia um tíquete Kerberos ao STS do AD FS. |5. O usuário insere a mesma senha local e o Azure AD a valida com o nome de usuário e senha que foram sincronizados pelo DirSync. |
| 6. O AD FS transforma o tíquete Kerberos para os formato/declaração de token necessário e redireciona o usuário para o Azure AD. |6. O Azure AD redireciona o usuário para o Office 365. |
| 7. O usuário autentica no AD do Azure (outra transformação ocorre). |7. O usuário pode entrar no Office 365 e no OWA usando o token do Azure AD. |
| 8. O Azure AD redireciona o usuário para o Office 365. | |
| 9. O usuário é conectado silenciosamente ao Office 365. | |

No Office 365 DirSync com cenário de sincronização de senha (sem AD FS), o logon único é substituído pelo "mesmo logon", em que “mesmo” significa que os usuários devem digitar novamente as mesmas credenciais locais ao acessar o Office 365. Observe que esses dados podem ser lembrados pelo navegador do usuário para ajudar a reduzir avisos subsequentes.

### <a name="additional-food-for-thought"></a>Ideias adicionais
* Se você implantar um proxy do AD FS em uma máquina virtual do Azure, é necessário ter conectividade com os servidores do AD FS. Se estiverem no local, é recomendável aproveitar a conectividade VPN site a site fornecida pela rede virtual para permitir que os nós de Proxy de Aplicativo Web se comuniquem com seus servidores do AD FS.
* Se você implantar um servidor do AD FS em uma máquina virtual do Azure, é necessário ter conectividade com os controladores de domínio do Active Directory do Windows Server, Repositórios de Atributos e bancos de dados de Configuração. Isso também pode exigir um ExpressRoute ou uma conexão VPN site a site entre a rede local e a rede virtual do Azure.
* Os encargos são aplicados a todo o tráfego proveniente de máquinas virtuais do Azure (tráfego de saída). Se o custo é o fator determinante, é aconselhável implantar os nós de Proxy de Aplicativo Web no Azure, deixando os servidores AD FS locais. Se os servidores do AD FS também forem implantados em máquinas virtuais do Azure, custos adicionais serão incorridos para autenticar os usuários locais. O tráfego de saída incorre em custo, independentemente de estar ou não atravessando o ExpressRoute ou a conexão VPN site a site.
* Se você decidir usar os recursos nativos de balanceamento de carga para a alta disponibilidade dos servidores do AD FS do Azure, observe que o balanceamento de carga fornece investigações que são usadas para determinar a integridade das máquinas virtuais no serviço de nuvem. No caso das máquinas virtuais do Azure (em vez de funções de trabalho ou Web), uma investigação personalizada deve ser usada uma vez que o agente que responde às investigações padrão não está presente nas máquinas virtuais do Azure. Para simplificar, você pode usar uma investigação TCP personalizada. Isso requer apenas que uma conexão TCP (um segmento TCP SYN enviado e respondido com um segmento TCP SYN ACK) seja estabelecida com êxito para determinar a integridade da máquina virtual. Você pode configurar a investigação personalizada para usar qualquer porta TCP na qual as máquinas virtuais estão ouvindo ativamente.

> [!NOTE]
> Computadores que precisarem expor o mesmo conjunto de portas diretamente para a Internet (como a porta 80 e 443) não poderão compartilhar o mesmo serviço de nuvem. Portanto, recomenda-se criar um serviço de nuvem dedicado para seus servidores do AD FS do Windows Server a fim de evitar possíveis sobreposições entre requisitos de porta para um aplicativo e o AD FS do Windows Server.
> 
> 

## <a name="deployment-scenarios"></a>Cenários de implantação
A seção a seguir descreve os cenários comuns de implantação para chamar a atenção a considerações importantes que devem ser levadas em conta. Cada cenário tem links para obter mais detalhes sobre decisões e fatores a considerar.

1. [AD DS: implantar um aplicativo com reconhecimento de AD DS sem a necessidade de conectividade à rede corporativa](#BKMK_CloudOnly)
   
    Por exemplo, um serviço do SharePoint da Internet é implantado em uma máquina virtual do Azure. O aplicativo não tem dependências nos recursos da rede corporativa. O aplicativo requer o AD DS do Windows Server, mas não requer o AD DS do Windows Server corporativo.
2. [AD FS: estender um aplicativo front-end de reconhecimento de local para a Internet](#BKMK_CloudOnlyFed)
   
    Por exemplo, um aplicativo com reconhecimento de declarações que foi implantado com êxito localmente e usado por usuários corporativos precisa poder ser acessado pela Internet. O aplicativo precisa ser acessado diretamente pela Internet por parceiros de negócios usando suas próprias identidades corporativas e por usuários corporativos existentes.
3. [AD DS: implantar um aplicativo com reconhecimento do AD DS do Windows Server que exige conectividade com a rede corporativa](#BKMK_HybridExt)
   
    Por exemplo, um aplicativo com reconhecimento de LDAP que dá suporte à autenticação integrada do Windows e usa o AD DS do Windows Server como um repositório de dados de configuração e perfil do usuário é implantado em uma máquina virtual do Azure. É desejável que o aplicativo aproveite o AD DS do Windows Server corporativo existente e forneça logon único. O aplicativo não reconhece declarações.

### <a name="BKMK_CloudOnly"></a>1. AD DS: implantar um aplicativo com reconhecimento de AD DS sem precisar conectar-se à rede corporativa
![Implantação do AD DS somente na nuvem](media/active-directory-deploying-ws-ad-guidelines/ADDS_cloud.png)
**Figura 1**

#### <a name="description"></a>Descrição
O SharePoint é implantado em uma máquina virtual do Azure e o aplicativo não tem dependências nos recursos da rede corporativa. O aplicativo requer o AD DS do Windows Server, mas *não* requer o AD DS do Windows Server corporativo. Nenhuma confiança federada ou Kerberos é necessária porque os usuários são autoprovisionados pelo aplicativo no domínio do AD DS do Windows Server que também está hospedado na nuvem em máquinas virtuais do Azure.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Considerações sobre cenário e como as áreas de tecnologia se aplicam ao cenário
* [Topologia de rede](#BKMK_NetworkTopology): criar uma rede virtual do Azure sem conectividade entre locais (também conhecida como conectividade site a site).
* [Configuração de implantação do controlador de domínio](#BKMK_DeploymentConfig): implantar um novo controlador de domínio em uma nova floresta do Active Directory do Windows Server de domínio único. Isso deve ser implantado junto com o servidor DNS do Windows.
* [Topologia de site do Active Directory do Windows Server](#BKMK_ADSiteTopology): usar o site do Active Directory do Windows Server padrão (todos os computadores estarão em Default-First-Site-Name).
* [Endereçamento IP e DNS](#BKMK_IPAddressDNS):
  
  * Configurar um endereço IP estático para o controlador de domínio usando o cmdlet Set-AzureStaticVNetIP do Azure PowerShell.
  * Instalar e configurar o DNS do Windows Server nos controladores de domínio do Azure.
  * Configurar as propriedades de rede virtual com o nome e o endereço IP da máquina virtual que hospeda as funções de servidor DNS e controlador de domínio.
* [Catálogo global](#BKMK_GC): o primeiro controlador de domínio da floresta deve ser um servidor de catálogo global. Controladores de domínio adicionais também devem ser configurados como CGs porque em uma floresta de domínio único, o catálogo global não exige qualquer trabalho adicional do controlador de domínio.
* [Colocação do banco de dados do AD DS do Windows Server e SYSVOL](#BKMK_PlaceDB): adicionar um disco de dados a controladores de domínio em execução como VMs do Azure para armazenar o banco de dados, logs e SYSVOL do Active Directory do Windows Server.
* [Backup e restauração](#BKMK_BUR): determine onde você deseja armazenar os backups de estado do sistema. Se necessário, adicione outro disco de dados à VM do controlador de domínio para armazenar backups.

### <a name="BKMK_CloudOnlyFed"></a>2 AD FS: estender um aplicativo front-end de reconhecimento de local para a Internet
![Federação com conectividade entre locais](media/active-directory-deploying-ws-ad-guidelines/Federation_xprem.png)
**Figura 2**

#### <a name="description"></a>Descrição
Um aplicativo com reconhecimento de declarações implantado com êxito localmente e usado por usuários corporativos precisa poder ser acessado diretamente pela Internet. O aplicativo serve como um front-end da Web para um banco de dados SQL em que ele armazena dados. Os servidores SQL usados pelo aplicativo também estão localizados na rede corporativa. Dois STSs do AD FS do Windows Server e um balanceador de carga foram implantados localmente para fornecer acesso a usuários corporativos. Agora, o aplicativo precisa ser acessado diretamente pela Internet por parceiros de negócios usando suas próprias identidades corporativas e por usuários corporativos existentes.

Na tentativa de simplificar e atender às necessidades de implantação e configuração dessa nova exigência, decidiu-se que dois front-ends adicionais da Web e dois servidores proxy do AD FS do Windows Server serão instalados em máquinas virtuais do Azure. Todas as quatro máquinas virtuais serão expostas diretamente à Internet e terão conectividade à rede local usando o recurso de VPN site a site da rede virtual do Azure.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Considerações sobre cenário e como as áreas de tecnologia se aplicam ao cenário
* [Topologia de rede](#BKMK_NetworkTopology): criar uma rede virtual do Azure e [configurar a conectividade entre locais](../vpn-gateway/vpn-gateway-site-to-site-create.md).
  
  > [!NOTE]
  > Para cada um dos certificados AD FS do Windows Server, verifique se a URL definida dentro do modelo de certificado e os certificados resultantes podem ser alcançados pelas instâncias do AD FS do Windows Server em execução no Azure. Isso pode exigir conectividade entre locais para partes da sua infraestrutura de PKI. Por exemplo, se o ponto de extremidade da CRL é baseado em LDAP e hospedado exclusivamente no local, a conectividade entre locais será necessária. Se isso não for desejável, poderá ser necessário usar certificados emitidos por uma AC cujo CRL seja acessível pela Internet.
  > 
  > 
* [Configuração de serviços de nuvem](#BKMK_CloudSvcConfig): verifique se você tem dois serviços de nuvem a fim de fornecer dois endereços IP virtuais com carga balanceada. O endereço IP do primeiro serviço de nuvem será direcionado para as duas VMs de proxy do AD FS do Windows Server nas portas 80 e 443. As VMs de proxy do AD FS do Windows Server serão configuradas para apontar para o endereço IP do balanceador de carga local que está à frente dos STSs do AD FS do Windows Server. O endereço IP virtual do segundo serviço de nuvem será direcionado às duas VMs executando o front-end da Web novamente nas portas 80 e 443. Configure uma investigação personalizada para garantir que o balanceador de carga direcione somente o tráfego para as VMs de front-end Web e proxy do AD FS do Windows Server.
* [Configuração do servidor de federação](#BKMK_FedSrvConfig): configurar o AD FS do Windows Server como um STS (servidor de federação) para gerar tokens de segurança para a floresta do Active Directory do Windows Server criada na nuvem. Defina relações de confiança de provedor de declarações com os diferentes parceiros dos quais você deseja aceitar identidades e configure relações de confiança com terceira parte confiável nos diferentes aplicativos para os quais deseja gerar tokens.
  
    Na maioria dos cenários, os servidores proxy do AD FS do Windows Server são implantados em um recurso de acesso à Internet para fins de segurança, ao passo que suas correspondentes de federação do AD FS do Windows Server permanecem isoladas da conectividade direta com a Internet. Independentemente de seu cenário de implantação, você deve configurar seu serviço de nuvem com um endereço IP virtual que fornecerá um endereço IP e uma porta capazes de balancear a carga entre as duas instâncias STS do AD FS do Windows Server ou as instâncias de proxy.
* [Configuração de alta disponibilidade do AD FS do Windows Server](#BKMK_ADFSHighAvail): é recomendável implantar um farm do AD FS do Windows Server com pelo menos dois servidores para failover e balanceamento de carga. É interessante considerar usar o WID (banco de dados interno do Windows) para dados de configuração do AD FS do Windows Server e usar o recurso de balanceamento de carga interno do Azure para distribuir solicitações de entrada entre os servidores no farm.

Para saber mais, confira o [Guia de implantação do AD DS](https://technet.microsoft.com/library/cc753963).

### <a name="BKMK_HybridExt"></a>3. AD DS: implantar um aplicativo com reconhecimento do AD DS do Windows Server que exige conectividade com a rede corporativa
![Implantação do AD DS entre instalações](media/active-directory-deploying-ws-ad-guidelines/ADDS_xprem.png)
**Figura 3**

#### <a name="description"></a>Descrição
Um aplicativo com reconhecimento de LDAP é implantado em uma máquina virtual do Azure. Ele é compatível com a autenticação integrada do Windows e usa o AD DS do Windows Server como um repositório para dados de perfil de usuário e de configuração. O objetivo é que o aplicativo aproveite o AD DS existente do Windows Server corporativo e forneça logon único. O aplicativo não reconhece declarações. Os usuários também precisam acessar o aplicativo diretamente da Internet. Para otimizar o desempenho e o custo, ficou decidido que dois controladores de domínio adicionais que fazem parte do domínio corporativo seriam implantados juntamente com o aplicativo no Azure.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Considerações sobre cenário e como as áreas de tecnologia se aplicam ao cenário
* [Topologia de rede](#BKMK_NetworkTopology): criar uma rede virtual do Azure com [conectividade entre locais](../vpn-gateway/vpn-gateway-site-to-site-create.md).
* [Método de instalação](#BKMK_InstallMethod): implantar controladores de domínio de réplica do domínio do Active Directory do Windows Server corporativo. Para um controlador de domínio de réplica, você pode instalar o AD DS do Windows Server na VM e, opcionalmente, usar o recurso IFM (instalar da mídia) para reduzir a quantidade de dados que precisam ser replicados no novo controlador de domínio durante a instalação. Para obter um tutorial, confira [Instalar um controlador de domínio do Active Directory de réplica no Azure](active-directory-install-replica-active-directory-domain-controller.md). Mesmo se você usar o IFM, pode ser mais eficiente criar o CD virtual no local e mover todo o VHD (disco rígido virtual) para a nuvem em vez de replicar o AD DS do Windows Server durante a instalação. Para segurança, é recomendável que você exclua o VHD da rede local assim que tiver sido copiado para o Azure.
* [Topologia do site do Active Directory do Windows Server](#BKMK_ADSiteTopology): criar um novo site do Azure em Serviços e Sites do Active Directory. Crie um objeto de sub-rede do Active Directory do Windows Server para representar a rede virtual do Azure e adicione a sub-rede ao site. Crie um novo link de site que inclui o novo site do Azure e o site em que o ponto de extremidade VPN da rede virtual do Azure está localizado para controlar e otimizar o tráfego do Active Directory do Windows Server de e para o Azure.
* [Endereçamento IP e DNS](#BKMK_IPAddressDNS):
  
  * Configurar um endereço IP estático para o controlador de domínio usando o cmdlet Set-AzureStaticVNetIP do Azure PowerShell.
  * Instalar e configurar o DNS do Windows Server nos controladores de domínio do Azure.
  * Configurar as propriedades de rede virtual com o nome e o endereço IP da máquina virtual que hospeda as funções de servidor DNS e controlador de domínio.
* [Controladores de domínio distribuídos geograficamente](#BKMK_DistributedDCs): configure redes virtuais adicionais conforme a necessidade. Se a topologia de site do Active Directory exige controladores de domínio nas geografias que correspondem a diferentes regiões do Azure, crie sites do Active Directory de forma adequada.
* [Controladores de domínio somente leitura](#BKMK_RODC): você pode implantar um RODC no site do Azure, dependendo de seus requisitos para executar operações de gravação no controlador de domínio e da compatibilidade de aplicativos e serviços no site com RODCs. Para saber mais sobre compatibilidade de aplicativos, confira o [Guia de compatibilidade de aplicativos de controladores de domínio somente leitura](https://technet.microsoft.com/library/cc755190).
* [Catálogo Global](#BKMK_GC): os CGs são necessários para atender a solicitações de logon em florestas de vários domínios. Se você não implantar um CG no site do Azure, incorrerá em custos de tráfego de saída, já que as solicitações de autenticação causam consultas de CGs em outros sites. Para minimizar esse tráfego, você pode habilitar o cache de associação ao grupo universal para o site do Azure em Serviços e Sites do Active Directory.
  
    Se você implantar um CG, configure links de site e os custos de links de site para que o CG no site do Azure não seja preferido como um controlador de domínio de origem por outros CGs que precisam replicar as mesmas partições de domínio parcial.
* [Posicionamento do SYSVOL e do banco de dados do AD DS do Windows Server](#BKMK_PlaceDB): adicionar um disco de dados a controladores de domínio em execução em VMs do Azure para armazenar banco de dados, logs e SYSVOL do Active Directory do Windows Server.
* [Backup e restauração](#BKMK_BUR): determine onde você deseja armazenar os backups de estado do sistema. Se necessário, adicione outro disco de dados à VM do controlador de domínio para armazenar backups.

## <a name="deployment-decisions-and-factors"></a>Fatores e decisões de implantação
Essa tabela resume as áreas de tecnologia do Active Directory do Windows Server que são afetadas nos cenários anteriores e as decisões correspondentes a serem consideradas, com links para mais detalhes abaixo. Algumas áreas de tecnologia podem não se aplicar a todos os cenários de implantação e algumas podem ser mais importantes para um cenário de implantação que outras.

Por exemplo, se você implantar uma controlador de domínio de réplica em uma rede virtual e sua floresta tiver apenas um domínio único, a escolha de implantar um servidor de catálogo global nesse caso não será essencial para o cenário de implantação porque ele não criará os requisitos adicionais de replicação. Por outro lado, se a floresta tiver vários domínios, a decisão de implantar um catálogo global em uma rede virtual poderá afetar a largura de banda disponível, o desempenho, a autenticação, as pesquisas de diretório e assim por diante.

| Área de tecnologia do Active Directory do Windows Server | Decisões | Fatores |
| --- | --- | --- |
| [Topologia de rede](#BKMK_NetworkTopology) |Você cria uma rede virtual? |<li>Requisitos para acessar os recursos da corporação</li> <li>Autenticação</li> <li>Gerenciamento de Contas</li> |
| [Configuração de implantação do controlador de domínio](#BKMK_DeploymentConfig) |<li>Implantar uma floresta separada sem nenhuma relações de confiança?</li> <li>Implantar uma nova floresta com federação?</li> <li>Implantar uma nova floresta com a relação de confiança da floresta do Active Directory do Windows Server ou Kerberos?</li> <li>Estender a floresta da corporação implantando um controlador de domínio de réplica?</li> <li>Estender a floresta da corporação implantando um novo domínio filho ou árvore de domínio?</li> |<li>Segurança</li> <li>Conformidade</li> <li>Custo</li> <li>Resiliência e tolerância a falhas</li> <li>Compatibilidade de aplicativos</li> |
| [Topologia do site do Active Directory do Windows Server](#BKMK_ADSiteTopology) |Como você configura sub-redes, sites e links de site com a rede virtual do Azure para otimizar o tráfego e minimizar os custos? |<li>Definições de sub-rede e site</li> <li>Propriedades do link do site e notificação de alteração</li> <li>Compactação de replicação</li> |
| [Endereçamento IP e DNS](#BKMK_IPAddressDNS) |Como configurar endereços IP e resolução de nome? |<li>Use o cmdlet Set-AzureStaticVNetIP para atribuir um endereço IP estático</li> <li>Instale o servidor Windows Server DNS e configure as propriedades de rede virtual com o nome e o endereço IP da máquina virtual que hospeda as funções de servidor DNS e controlador de domínio.</li> |
| [Controladores de domínio distribuídos geograficamente](#BKMK_DistributedDCs) |Como replicar controladores de domínio em redes virtuais separadas? |Se a topologia de site do Active Directory exige controladores de domínio nas geografias que correspondem a diferentes regiões do Azure, crie sites do Active Directory de forma adequada. [Configure a rede virtual para a conectividade de rede virtual](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) a fim de replicar entre controladores de domínio em redes virtuais separadas. |
| [Controladores de domínio somente leitura](#BKMK_RODC) |Usar controladores de domínio somente leitura ou graváveis? |<li>Filtrar atributos HBI/PII</li> <li>Filtrar segredos</li> <li>Limitar o tráfego de saída</li> |
| [Catálogo global](#BKMK_GC) |Instalar o catálogo global? |<li>Para uma floresta de domínio único, verifique todos os GCs de DCs</li> <li>Para uma floresta de vários domínios, os GCs são necessários para autenticação</li> |
| [Método de instalação](#BKMK_InstallMethod) |Como instalar o controlador de domínio no Azure? |Você pode: <li>Instalar o AD DS usando o Windows PowerShell ou o Dcpromo</li> <li>Mover o VHD de um DC virtual local</li> |
| [Posicionamento do SYSVOL e do banco de dados do AD DS do Windows Server](#BKMK_PlaceDB) |Onde armazenar o banco de dados, logs e SYSVOL do AD DS do Windows Server? |Altere os valores padrão de Dcpromo.exe. Esses arquivos críticos do Active Directory *têm que* ser colocados em Discos de Dados do Azure, em vez de discos do Sistema Operacional que implementa o cache de gravação. |
| [Backup e restauração](#BKMK_BUR) |Como proteger e recuperar dados? |Criar backups de estado do sistema |
| [Configuração do servidor de federação](#BKMK_FedSrvConfig) |<li>Implantar uma nova floresta com federação na nuvem?</li> <li>Implantar o AD FS local e expor um proxy na nuvem?</li> |<li>Segurança</li> <li>Conformidade</li> <li>Custo</li> <li>Acesso a aplicativos por parceiros de negócios</li> |
| [Configuração de serviços de nuvem](#BKMK_CloudSvcConfig) |Um serviço de nuvem é implantado implicitamente na primeira vez em que você cria uma máquina virtual. Você precisa implantar serviços de nuvem adicionais? |<li>Uma ou mais máquinas virtuais exigem exposição direta à Internet?</li> <li> O serviço exige o balanceamento de carga?</li> |
| [Requisitos do servidor de federação para endereçamento IP público e privado (IP dinâmico versus IP virtual)](#BKMK_FedReqVIPDIP) |<li>A instância do AD FS do Windows Server precisa ser acessada diretamente pela Internet?</li> <li>O aplicativo que está sendo implantado na nuvem requer seu próprio endereço IP para a Internet e a porta?</li> |Criar um serviço de nuvem para cada endereço IP virtual que é exigido pela sua implantação |
| [Configuração de alta disponibilidade do AD FS do Windows Server](#BKMK_ADFSHighAvail) |<li>Quantos nós em meu farm de servidores do AD FS do Windows Server?</li> <li>Quantos nós implantar em meu farm de proxy do AD FS do Windows Server?</li> |Resiliência e tolerância a falhas |

### <a name="BKMK_NetworkTopology"></a>Topologia de rede
Para atender aos requisitos de consistência de endereço IP e de DNS do AD DS do Windows Server, primeiro é necessário criar uma [rede virtual do Azure](../virtual-network/virtual-networks-overview.md) e conectar as máquinas virtuais a ela. Durante a criação, você deve decidir se deseja opcionalmente estender a conectividade à rede corporativa local, que conecta de maneira transparente máquinas virtuais do Azure a computadores locais. Isso é obtido usando tecnologias de VPN tradicionais e exige que um ponto de extremidade VPN seja exposto na borda da rede corporativa. Ou seja, a VPN é iniciada do Azure para a rede corporativa, e não o contrário.

Observe que encargos adicionais se aplicam ao estender uma rede virtual à sua rede local, além dos encargos padrão que se aplicam a cada máquina virtual. Especificamente, há encargos para o tempo de CPU do gateway de Rede Virtual do Azure e para o tráfego de saída gerado por cada VM que se comunica com computadores locais pela VPN. Para saber mais sobre encargos de tráfego de rede, confira [Visão geral de preços do Azure](http://azure.microsoft.com/pricing/).

### <a name="BKMK_DeploymentConfig"></a>Configuração de implantação do controlador de domínio
A maneira como você configura o controlador de domínio depende dos requisitos do serviço que deseja executar no Azure. Por exemplo, você pode implantar uma nova floresta, isolada de sua própria floresta corporativa, para testar uma prova de conceito, um novo aplicativo ou outro projeto de curto prazo que requer serviços de diretório, mas não o acesso específico aos recursos corporativos internos.

Como benefício, um controlador de domínio de floresta isolado não replica com controladores de domínios locais, resultando em menor tráfego de rede de saída gerado pelo sistema em si e reduzindo custos diretamente. Para saber mais sobre encargos de tráfego de rede, confira [Visão geral de preços do Azure](http://azure.microsoft.com/pricing/).

Para dar outro exemplo, suponha que você tenha requisitos de privacidade para um serviço, mas o serviço depende do acesso ao Active Directory do Windows Server interno. Se você tem permissão para hospedar dados do serviço na nuvem, pode implantar um novo domínio filho para sua floresta interna no Azure. Nesse caso, você pode implantar um controlador de domínio para o novo domínio filho (sem o catálogo global para ajudar a resolver problemas de privacidade). Esse cenário, junto com uma implantação de controlador de domínio de réplica, requer uma rede virtual para conectividade com seus controladores de domínio locais.

Se você criar uma nova floresta, escolha se deseja usar [relações de confiança do Active Directory](https://technet.microsoft.com/library/cc771397) ou da [federação](https://technet.microsoft.com/library/dd807036). Equilibre as exigências ditadas por compatibilidade, segurança, conformidade, custo e resiliência. Por exemplo, para tirar proveito da [autenticação seletiva](https://technet.microsoft.com/library/cc755844) , você pode optar por implantar uma nova floresta no Azure e criar uma relação de confiança entre a floresta local e a floresta da nuvem do Active Directory do Windows Server. Se o aplicativo tiver reconhecimento de declarações, no entanto, você poderá implantar a confiança de federação em vez de relações de confiança de floresta do Active Directory. Outro fator será o custo para replicar mais dados estendendo seu Active Directory do Windows Server local para a nuvem ou gerar mais tráfego de saída como resultado da autenticação e da carga de consulta.

Os requisitos de disponibilidade e tolerância a falhas também afetam sua escolha. Por exemplo, se o link for interrompido, aplicativos que usam uma relação de confiança de Kerberos ou de federação serão todos totalmente descontinuados, a menos que você tenha implantado infraestrutura suficiente no Azure. Configurações de implantação alternativas, como controladores de domínio de réplica (graváveis ou RODCs) aumentam a capacidade de tolerância a interrupções de link.

### <a name="BKMK_ADSiteTopology"></a>Topologia do site do Active Directory do Windows Server
Você precisa definir corretamente os sites e links de sites para otimizar o tráfego e minimizar os custos. Sites, links de sites e sub-redes afetam a topologia de replicação entre controladores de domínio e o fluxo de tráfego de autenticação. Considere os seguintes encargos de tráfego e implante e configure controladores de domínio de acordo com os requisitos de seu cenário de implantação:

* Há uma taxa nominal por hora para o próprio gateway:
  
  * Ele pode ser iniciado e interrompido conforme você quiser
  * Se forem interrompidas, as VMs do Azure serão isoladas da rede corporativa
* O tráfego de entrada está livre
* O tráfego de saída é cobrado conforme a [Visão geral de preços do Azure](http://azure.microsoft.com/pricing/). Você pode otimizar propriedades de link de site entre sites locais e a nuvem da seguinte maneira:
  
  * Se você estiver usando várias redes virtuais, configure os links de site e seus custos de forma adequada para impedir que o AD DS do Windows Server priorize o site do Azure no lugar de um site que possa fornecer os mesmos níveis de serviço gratuitamente. Você também pode considerar desabilitar a opção BASL (ponte de todos os links de site), que é habilitada por padrão. Isso faz com que somente os sites conectados diretamente repliquem entre si. Os controladores de domínio em sites conectados temporariamente não conseguem replicar diretamente uns com os outros, mas devem replicar por meio de um ou mais sites em comum. Se os sites intermediários ficam indisponíveis por algum motivo, a replicação entre controladores de domínio em sites conectados temporariamente não ocorre mesmo que haja conectividade entre os sites. Finalmente, quando existirem seções do comportamento de replicação temporária ainda desejáveis, crie pontes de link de site que contêm os sites e links de site apropriados, como sites de rede corporativa locais.
  * [Configure custos de link de site](https://technet.microsoft.com/library/cc794882) adequadamente para evitar tráfego não intencional. Por exemplo, se a configuração **Tentar Site Mais Próximo** estiver habilitada, verifique se os sites de rede virtual não são os mais próximos aumentando o custo associado do objeto de link de site que conecta o site do Azure de volta à rede corporativa.
  * Configure os [intervalos](https://technet.microsoft.com/library/cc794878) e [agendas](https://technet.microsoft.com/library/cc816906) do link de site de acordo com os requisitos de consistência e taxa de alterações de objeto. Alinhe a agenda de replicação com a tolerância à latência. Os controladores de domínio replicam somente o último estado de um valor, portanto, diminuir o intervalo de replicação poderá economizar custos se houver uma taxa de alteração de objeto suficiente.
* Se a minimização de custos for uma prioridade, verifique se a replicação está agendada e se a notificação de alteração não está habilitada. Essa é a configuração padrão na replicação entre sites. Isso não é importante se você estiver implantando um RODC em uma rede virtual porque o RODC não replicará alterações de saída. Mas, se você implantar um CD gravável, deverá verificar se que o link de site não está configurado para replicar atualizações com frequência desnecessária. Se você implantar um servidor de CG (catálogo global), verifique se todos os outros sites que contêm um CG replicam as partições de domínio de um CD de origem em um site que está conectado a um ou mais links de site que têm um custo menor do que o CG no site do Azure.
* É possível reduzir ainda mais o tráfego de rede gerado pela replicação entre sites alterando o algoritmo de compactação de replicação. O algoritmo de compactação é controlado pelo algoritmo de compactação de HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Replicator de entrada do registro REG_DWORD. O valor padrão é 3, que se relaciona com o algoritmo de compactação Xpress. Você pode alterar o valor para 2, que altera o algoritmo para MSZip. Na maioria dos casos, isso aumentará a compactação, mas ele faz isso às custas da utilização da CPU. Para saber mais, confira [Como funciona a topologia de replicação do Active Directory](https://technet.microsoft.com/library/cc755994).

### <a name="BKMK_IPAddressDNS"></a>Endereçamento IP e DNS
As máquinas virtuais do Azure recebem "endereços arrendados para DHCP" por padrão. Como os endereços dinâmicos de rede virtual do Azure persistem com uma máquina virtual pelo tempo de vida da máquina virtual, os requisitos do AD DS do Windows Server são atendidos.

Como resultado, quando você usa um endereço dinâmico no Azure, está de fato usando um endereço IP estático, pois ele é roteável pelo período da concessão e o período da concessão é igual ao tempo de vida do serviço de nuvem.

No entanto, o endereço dinâmico será desalocado se a VM for desligada. Para impedir que o endereço IP seja desalocado, você pode [usar Set-AzureStaticVNetIP para atribuir um endereço IP estático](http://social.technet.microsoft.com/wiki/contents/articles/23447.how-to-assign-a-private-static-ip-to-an-azure-vm.aspx).

Para a resolução de nomes, implante sua própria infraestrutura de servidor DNS (ou aproveite uma existente). O DNS fornecido pelo Azure não atende às necessidades de resolução de nome avançadas do AD DS do Windows Server. Por exemplo, ele não é compatível com registros SRV dinâmicos e assim por diante. A resolução de nomes é um item de configuração essencial para clientes ingressados no domínio e controladores de domínio. Os controladores de domínio devem ser capazes de gravar registros de recursos e resolver registros de recursos de outros controladores de domínio.
Por razões de desempenho e tolerância a falhas, é ideal instalar o serviço DNS do Windows Server nos controladores de domínio em execução no Azure. Em seguida, configure as propriedades de rede virtual do Azure com o nome e o endereço IP do servidor DNS. Quando outras VMs na rede virtual iniciarem, suas configurações de resolvedor do cliente DNS serão configuradas com o servidor DNS como parte da alocação de endereços IP dinâmicos.

> [!NOTE]
> Você não pode ingressar computadores locais em um domínio do Active Directory do AD DS do Windows Server que está hospedado no Azure diretamente pela Internet. Os requisitos de porta para o Active Directory e a operação de ingresso no domínio tornam impraticável a exposição direta das portas necessárias e, por consequência, um controlador de domínio inteiro para a Internet.
> 
> 

As máquinas virtuais registram automaticamente seu nome DNS na inicialização ou quando há uma alteração no nome.

Para saber mais sobre este exemplo e outro exemplo que mostra como provisionar a primeira VM e instalar o AD DS, confira [Instalar uma nova floresta do Active Directory no Microsoft Azure](active-directory-new-forest-virtual-machine.md). Para saber mais sobre como usar o Windows PowerShell, confira [Instalar o Azure PowerShell](/powershell/azureps-cmdlets-docs) e [Cmdlets de gerenciamento do Azure](/powershell/module/azurerm.compute/#virtual_machines).

### <a name="BKMK_DistributedDCs"></a>Controladores de domínio distribuídos geograficamente
O Azure oferece vantagens ao hospedar vários controladores de domínio em redes virtuais diferentes:

* Tolerância a falhas de vários sites
* Proximidade física a ramificações (latência mais baixa)

Para obter informações sobre como configurar a comunicação direta entre redes virtuais, confira [Configurar rede virtual para conectividade de rede virtual](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

### <a name="BKMK_RODC"></a>Controladores de domínio somente leitura
Você precisa optar entre implantar controladores de domínio somente leitura ou graváveis. Talvez você queira implantar RODCs porque não terá controle físico sobre eles, mas os RODCs são projetados para serem implantados nos locais onde a segurança física está em risco, como nas filiais.

O Azure não apresenta o risco de segurança física de uma filial, mas os RODCs podem ser ainda mais econômicos porque os recursos que eles fornecem são apropriados para esses ambientes, mas por razões muito diferentes. Por exemplo, os RODCs não têm replicações de saída e podem preencher segredos (senhas) de forma seletiva. Por outro lado, a falta desses segredos pode exigir que o tráfego de saída sob demanda valide-os como um usuário ou o computador autentica. Mas os segredos podem ser seletivamente preenchidos previamente e armazenados em cache.

Os RODCs fornecem uma vantagem adicional em relação a questões de HBI e PII porque você pode adicionar atributos que contêm dados confidenciais ao FAS (conjunto de atributos filtrados) do RODC. O FAS é um conjunto de atributos personalizáveis que não são replicados para RODCs. Você pode usar o FAS como proteção caso não possa ou não deseje armazenar o PII ou HBI no Azure. Para saber mais, confira [conjunto de atributos filtrados do RODC[(https://technet.microsoft.com/library/cc753459)]

Verifique se os aplicativos serão compatíveis com os RODCs que você planeja usar. Muitos aplicativos habilitados para o Active Directory do Windows Server funcionam bem com RODCs, mas alguns aplicativos poderão ser executados de maneira ineficiente ou falhar se não tiverem acesso a um controlador de domínio gravável. Para saber mais, confira [Guia de compatibilidade de aplicativos de controladores de domínio somente leitura](https://technet.microsoft.com/library/cc755190).

### <a name="BKMK_GC"></a>Catálogo global
Você precisa escolher se deseja instalar um CG (catálogo global). Em uma floresta de domínio único, você deve configurar todos os controladores de domínio como servidores de catálogo global. Ele não aumentará o custo porque não haverá tráfego de replicação adicional.

Em uma floresta de vários domínios, os CGs são necessários para expandir as associações ao Grupo Universal durante o processo de autenticação. Se você não implantar um CG, as cargas de trabalho na rede virtual que autentica em um controlador de domínio no Azure gerarão tráfego de autenticação de saída indiretamente para consultar CGs locais durante cada tentativa de autenticação.

Os custos associados aos CGs serão menos previsíveis porque eles hospedam todos os domínios (parcialmente). Se a carga de trabalho hospeda um serviço de acesso à Internet e autentica usuários no AD DS do Windows Server, os custos podem ser completamente imprevisíveis. Para ajudar a reduzir as consultas de GC fora do site de nuvem durante a autenticação, você pode [habilitar o Cache de Associação ao Grupo Universal](https://technet.microsoft.com/library/cc816928).

### <a name="BKMK_InstallMethod"></a>Método de instalação
Você precisa escolher como instalar os controladores de domínio na rede virtual:

* Promova novos controladores de domínio. Para saber mais, confira [Instalar uma nova floresta do Active Directory em uma rede virtual do Azure](active-directory-new-forest-virtual-machine.md).
* Mova o VHD de um controlador de domínio virtual local para a nuvem. Nesse caso, você deve garantir que o controlador de domínio virtual local foi "movido", e não "copiado" ou "clonado".

Use somente máquinas virtuais do Azure para controladores de domínio (em vez das VMs de função "Web" ou "trabalho" do Azure). Elas são duráveis e é necessário ter durabilidade de estado para um controlador de domínio. As máquinas virtuais do Azure são projetadas para cargas de trabalho como controladores de domínio.

Não use SYSPREP para implantar ou clonar controladores de domínio. A capacidade de clonar controladores de domínio está disponível somente a partir do Windows Server 2012. O recurso de clonagem exige suporte para VMGenerationID no hipervisor subjacente. O Hyper-V no Windows Server 2012 e as redes virtuais do Azure dão suporte a VMGenerationID, como fornecedores de software de virtualização de terceiros.

### <a name="BKMK_PlaceDB"></a>Posicionamento do SYSVOL e do banco de dados do AD DS do Windows Server
Escolha onde localizar o banco de dados, logs e SYSVOL do AD DS do Windows Server. Eles devem ser implantados em discos de dados do Azure.

> [!NOTE]
> Os discos de dados do Azure são restritos a 1 TB.
> 
> 

Por padrão, as unidades de disco de dados não fazem gravações em cache. As unidades de disco de dados que estão anexadas a uma máquina virtual usam cache de gravação. A gravação por cache faz com que a gravação seja enviada ao armazenamento durável do Azure antes que a transação seja concluída a partir da perspectiva do sistema operacional da VM. Ela fornece durabilidade às custas de gravações um pouco mais lentas.

Isso é importante para o AD DS do Windows Server porque o cache de disco de gravação temporária invalida as suposições feitas pelo controlador de domínio. O AD DS do Windows Server tenta desabilitar o cache de gravação, mas cabe ao disco de sistema de E/S fazer isso. A falha ao desabilitar o cache de gravação pode, em determinadas circunstâncias, introduzir a reversão do USN, resultando em objetos persistentes e outros problemas.

Como prática recomendada para controladores de domínio virtuais, faça o seguinte:

* Defina a configuração de Preferência de Cache do Host no disco de dados do Azure para NONE. Isso evita problemas com o cache de gravação para operações do AD DS.
* Armazene banco de dados, logs e SYSVOL no mesmo disco de dados ou em discos de dados separados. Normalmente, isso é um disco separado do disco usado para o próprio sistema operacional. A principal vantagem é que o SYSVOL e o banco de dados do AD DS do Windows Server não devem ser armazenados em um tipo de disco do Sistema Operacional do Azure. Por padrão, o processo de instalação do AD DS instala esses componentes na pasta %systemroot%, que NÃO é recomendável para o Azure.

### <a name="BKMK_BUR"></a>Backup e restauração
Lembre-se do que tem ou não suporte para backup e restauração de um controlador de domínio em geral e, mais especificamente, aqueles em execução em uma VM. Confira [Considerações de Backup e Restauração para Controladores de Domínio Virtualizados](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv#backup_and_restore_considerations_for_virtualized_domain_controllers).

Crie backups do estado de sistema usando apenas o software de backup que está especificamente ciente dos requisitos de backup do AD DS do Windows Server, como o Backup do Windows Server.

Não copie nem clone arquivos VHD de controladores de domínio em vez de executar backups regulares. Se houver a necessidade de restauração, faça-a usando VHDs clonados ou copiados sem o Windows Server 2012 e um hipervisor com suporte introduzirá bolhas de USN.

### <a name="BKMK_FedSrvConfig"></a>Configuração do servidor de federação
A configuração dos STSs (servidores de federação do AD FS do Windows Server) depende em parte da necessidade ou não dos aplicativos que você deseja implantar no Azure acessarem recursos em sua rede local.

Se os aplicativos atendem aos critérios a seguir, você pode implantar os aplicativos isoladamente a partir de sua rede local.

* Eles aceitam tokens de segurança SAML
* Eles ficam expostos à Internet
* Eles não acessam recursos locais

Nesse caso, configure os STSs do AD FS do Windows Server da seguinte maneira:

1. Configure uma floresta isolada de domínio único no Azure.
2. Forneça acesso federado à floresta configurando um farm de servidores de federação do AD FS do Windows Server.
3. Configure o AD FS do Windows Server (farm de servidores de federação e farm de proxy do servidor de federação) na floresta local.
4. Estabeleça uma relação de confiança de federação entre as instâncias locais e as do Azure do AD FS do Windows Server.

Por outro lado, se os aplicativos precisarem acessar recursos locais, você poderia configurar o AD FS do Windows Server com o aplicativo no Azure da seguinte maneira:

1. Configure a conectividade entre redes locais e o Azure.
2. Configure um farm de servidores de federação do AD FS do Windows Server na floresta local.
3. Configure um farm de proxy de servidor de federação do AD FS do Windows Server no Azure.

Essa configuração tem a vantagem de reduzir a exposição de recursos locais, semelhante à configuração do AD FS do Windows Server com aplicativos em uma rede de perímetro.

Observe que, em qualquer cenário, você pode estabelecer relações de confiança com mais provedores de identidade se houver necessidade de colaboração entre empresas.

### <a name="BKMK_CloudSvcConfig"></a>Configuração de serviços de nuvem
Os serviços de nuvem são necessários se você quer expor uma VM diretamente na Internet ou expor um aplicativo de balanceamento de carga na Internet. Isso é possível porque cada serviço de nuvem oferece um único endereço IP virtual configurável.

### <a name="BKMK_FedReqVIPDIP"></a>Requisitos do servidor de federação para endereçamento IP público e privado (IP dinâmico versus IP virtual)
Cada máquina virtual do Azure recebe um endereço IP dinâmico. Um endereço IP dinâmico é um endereço privado que só pode ser acessado dentro do Azure. Na maioria dos casos, no entanto, será necessário configurar um endereço IP virtual para suas implantações do AD FS do Windows Server. O IP virtual é necessário para expor pontos de extremidade do AD FS do Windows Server na Internet e será usado por clientes e parceiros federados para autenticação e gerenciamento contínuo. Um endereço IP virtual é uma propriedade de um serviço de nuvem que contém uma ou mais máquinas virtuais do Azure. Se os aplicativos com reconhecimento de declaração implantados no Azure e no AD FS do Windows Server estiverem voltados para a Internet e compartilharem portas comuns, cada um exigirá um endereço IP virtual próprio e, portanto, será necessário criar um serviço de nuvem para o aplicativo e um segundo para o AD FS do Windows Server.

Para obter definições dos termos endereço IP virtual e endereço IP dinâmico, confira [Termos e definições](#BKMK_Glossary).

### <a name="BKMK_ADFSHighAvail"></a>Configuração de alta disponibilidade do AD FS do Windows Server
Embora seja possível implantar serviços de federação AD FS do Windows Server autônomos, é recomendável implantar um farm com pelo menos dois nós para STS do AD FS e proxies para ambientes de produção.

Confira [Considerações de topologia da implantação do AD FS 2.0](https://technet.microsoft.com/library/gg982489) no [Guia de Design do AD FS 2.0](https://technet.microsoft.com/library/dd807036) para decidir quais opções de configuração de implantação atendem melhor às suas necessidades específicas.

> [!NOTE]
> Para obter o balanceamento de carga para pontos de extremidade do AD FS do Windows Server no Azure, configure todos os membros do farm do AD FS do Windows Server no mesmo serviço de nuvem e use o recurso de balanceamento de carga do Azure para HTTP (padrão 80) e portas HTTPS (padrão 443). Para saber mais, confira [Investigação do balanceador de carga do Azure](https://msdn.microsoft.com/library/azure/jj151530).
> Não há suporte para o NLB (balanceamento de carga de rede) do Windows Server no Azure.
> 
> 


---
title: "Resolução para máquinas virtuais e instâncias de função"
description: "Cenários de resolução de nomes para o Azure IaaS, soluções híbridas, entre diferentes serviços de nuvem, Active Directory e usando o seu próprio servidor DNS  "
services: virtual-network
documentationcenter: na
author: GarethBradshawMSFT
manager: carmonm
editor: tysonn
ms.assetid: 5d73edde-979a-470a-b28c-e103fcf07e3e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/06/2016
ms.author: telmos
ms.openlocfilehash: 479cf8cf358d0b242d8ce030d8639b493e4767d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="name-resolution-for-vms-and-role-instances"></a>Resolução de nomes de máquinas virtuais e instâncias de função
Dependendo de como você usar o Azure para hospedar o IaaS, o PaaS e as soluções híbridas, pode ser necessário permitir que as VMs e as instâncias de função criadas se comuniquem entre si. Embora essa comunicação possa ser feita usando endereços IP, é muito mais simples usar nomes que possam ser facilmente lembrados e que não sejam alterados. 

Quando as instâncias de função e as VMs hospedadas no Azure precisam resolver nomes de domínio para endereços IP internos, elas podem usar um dos dois métodos:

* [Resolução de nomes fornecida pelo Azure](#azure-provided-name-resolution)
* [Resolução de nome usando seu próprio servidor DNS](#name-resolution-using-your-own-dns-server) (que pode encaminhar consultas para os servidores DNS fornecidos pelo Azure) 

O tipo de resolução de nomes usado depende de como as VMs e as instâncias de função precisam se comunicar entre si.

**A tabela a seguir ilustra os cenários e as soluções de resolução de nome correspondentes:**

| **Cenário** | **Solução** | **Suffix** |
| --- | --- | --- |
| Resolução de nomes entre as instâncias de função ou as máquinas virtuais no mesmo serviço de nuvem ou rede virtual |[Resolução de nomes fornecida pelo Azure](#azure-provided-name-resolution) |nome de host ou FQDN |
| Resolução de nomes entre instâncias de função ou máquinas virtuais localizadas em redes virtuais diferentes |Servidores DNS gerenciados pelo cliente que encaminham consultas entre redes virtuais para resolução pelo Azure (proxy DNS).  Confira [Resolução de nome usando o seu próprio servidor DNS](#name-resolution-using-your-own-dns-server) |Somente FQDN |
| Resolução de nomes de serviço e de computador locais em instâncias de função ou VMs no Azure |Servidores DNS gerenciados pelo cliente (por exemplo, controlador de domínio local, controlador de domínio somente leitura local ou DNS secundário sincronizado usando transferências de zona).  Confira [Resolução de nome usando o seu próprio servidor DNS](#name-resolution-using-your-own-dns-server) |Somente FQDN |
| Resolução de nomes de host do Azure de computadores locais |Encaminhe consultas para um servidor de proxy DNS gerenciado pelo cliente na rede virtual correspondente. O servidor proxy encaminha consultas para resolução pelo Azure. Confira [Resolução de nome usando o seu próprio servidor DNS](#name-resolution-using-your-own-dns-server) |Somente FQDN |
| DNS inverso para IPs internos |[Resolução de nome usando o seu próprio servidor DNS](#name-resolution-using-your-own-dns-server) |n/d |
| Resolução de nomes entre VMs ou instâncias de função localizadas em serviços de nuvem diferente, não em uma rede virtual |Não aplicável. Não há suporte para a conectividade entre máquinas virtuais e instâncias de função em diferentes serviços de nuvem fora de uma rede virtual. |n/d |

## <a name="azure-provided-name-resolution"></a>Resolução de nomes fornecida pelo Azure
Junto com a resolução de nomes DNS públicos, o Azure fornece uma resolução de nomes interna para VMs e instâncias de função que residem na mesma rede virtual ou serviço de nuvem.  As VMs/instâncias em um serviço de nuvem compartilham o mesmo sufixo DNS (de modo que o nome do host por si só é suficiente), mas, em redes virtuais clássicas, diferentes serviços de nuvem têm diferentes sufixos DNS. Portanto, o FQDN é necessário para resolver nomes entre diferentes serviços de nuvem.  Em redes virtuais no modelo de implantação do Gerenciador de Recursos, o sufixo DNS é consistente em toda a rede virtual (portanto, o FQDN não é necessário) e os nomes DNS podem ser atribuídos a NICs e VMs. Embora a resolução de nomes fornecida pelo Azure não requeira qualquer configuração, ela não é a escolha apropriada para todos os cenários de implantação, como mostrado na tabela acima.

> [!NOTE]
> No caso das funções web e de trabalho, você também pode acessar os endereços IP internos das instâncias de função, conforme o número de instâncias e o nome da função usando a API REST do Gerenciamento de Serviços do Azure. Para obter mais informações, veja [Referência da API REST de Gerenciamento de Serviços](https://msdn.microsoft.com/library/azure/ee460799.aspx).
> 
> 

### <a name="features-and-considerations"></a>Recursos e considerações
**Recursos:**

* Facilidade de uso: não é necessária nenhuma configuração para usar a resolução de nomes fornecida pelo Azure.
* O serviço de resolução de nomes fornecido pelo Azure é altamente disponível, economizando a necessidade de criar e gerenciar clusters de seus próprios servidores DNS.
* Pode ser usado em conjunto com seus próprios servidores DNS para resolver nomes de host locais e do Azure.
* A resolução de nomes é fornecida entre as instâncias de função/VMs no mesmo serviço de nuvem, sem a necessidade de um FQDN.
* A resolução de nomes é fornecida entre VMs em redes virtuais que usam o modelo de implantação do Gerenciador de Recursos, sem a necessidade de FQDN. Redes virtuais no modelo de implantação clássico exigem o FQDN durante a resolução de nomes em diferentes serviços de nuvem. 
* Você pode usar nomes de host que descrevem melhor as suas implantações, em vez de trabalhar com nomes gerados automaticamente.

**Considerações:**

* O sufixo DNS criado pelo Azure não pode ser modificado.
* Não é possível registrar manualmente seus próprios registros.
* Não há suporte para o WINS e NetBIOS. (Você não pode ver suas VMs no Windows Explorer.)
* Os nomes de host devem ser compatíveis com DNS (eles devem usar somente 0-9, a-z e '-' e não podem começar ou terminar com um '-'. Consulte o RFC 3696, seção 2).
* O tráfego da consulta DNS é restrita a cada VM. Isso não deve afetar a maioria dos aplicativos.  Se a limitação da solicitação for observada, certifique-se de que o armazenamento em cache do cliente está habilitado.  Para obter mais detalhes, consulte [Obtendo o máximo de resolução de nomes fornecida pelo Azure](#Getting-the-most-from-Azure-provided-name-resolution).
* Apenas as VMs dos primeiros 180 serviços de nuvem são registradas para cada rede virtual em um modelo de implantação clássico. Isso não se aplica às redes virtuais nos modelos de implantação do Gerenciador de Recursos.

### <a name="getting-the-most-from-azure-provided-name-resolution"></a>Obtendo o máximo de resolução de nomes fornecida pelo Azure
**Armazenamento em cache no lado cliente:**

Nem todas as consultas DNS precisam ser enviada pela rede.  O armazenamento em cache do lado do cliente ajuda a reduzir a latência e melhorar a resistência a pontos de luz de rede resolvendo consultas de DNS recorrentes a partir de um cache local.  Os registros DNS contêm um Time-To-Live (TTL) que permite que o cache armazene o registro o maior tempo possível sem afetar a atualização de registro para que o armazenamento em cache do lado do cliente seja adequado para a maioria das situações.

O cliente DNS padrão do Windows tem um cache DNS interno.  Algumas distribuições do Linux não incluem o armazenamento em cache por padrão. É recomendável que um seja adicionado a cada VM do Linux (após verificação de que ainda não há um cache local).

Há uma série de pacotes de armazenamento em cache de DNS diferentes disponíveis, por exemplo, dnsmasq, aqui estão as etapas para instalar o dnsmasq nas distribuições mais comuns:

* **Ubuntu (usa resolvconf)**:
  * apenas instale o pacote de dnsmasq ("sudo apt-get install dnsmasq").
* **SUSE (usa netconf)**:
  * instale o pacote de dnsmasq ("sudo zypper install dnsmasq"). 
  * habilite o serviço dnsmasq ("systemctl enable dnsmasq.service") 
  * inicie o serviço dnsmasq ("systemctl enable dnsmasq.service") 
  * edite "/etc/sysconfig/network/config" e altere NETCONFIG_DNS_FORWARDER = "" para "dnsmasq"
  * atualize resolv. conf ("netconfig update") para definir o cache do resolvedor DNS local
* **OpenLogic (usa NetworkManager)**:
  * instale o pacote de dnsmasq ("sudo yum install dnsmasq").
  * habilite o serviço dnsmasq ("systemctl enable dnsmasq.service")
  * inicie o serviço dnsmasq ("systemctl enable dnsmasq.service")
  * adicione "prepend domain-name-servers 127.0.0.1;" a "/etc/dhclient-eth0.conf"
  * reinicie o serviço de rede ("service network restart") para definir o cache como o resolvedor DNS local

> [!NOTE]
> O pacote 'dnsmasq' é apenas um dos vários caches DNS disponíveis para o Linux.  Antes de usá-lo, verifique sua adequação para suas necessidades específicas e se nenhum outro cache está instalado.
> 
> 

**Tentativa no lado do cliente:**

O DNS é principalmente um protocolo UDP.  Como o protocolo UDP não garante a entrega de mensagens, a lógica de repetição é manipulada no próprio protocolo DNS.  Cada cliente DNS (sistema operacional) pode apresentar uma lógica de repetição diferente dependendo da preferência dos criadores:

* Sistemas operacionais Windows tentam novamente depois de 1 segundo e, em seguida, novamente após outros 2, 4 e outros 4 segundos. 
* As tentativas de instalação do Linux ocorrem, por padrão, depois de 5 segundos.  É recomendável alterar para repetir 5 vezes em intervalos de 1 segundo.  

Para verificar as configurações atuais em uma VM do Linux, 'cat /etc/resolv.conf' e examine a linha 'options', por exemplo:

    options timeout:1 attempts:5

O arquivo resolv.conf é normalmente gerado automaticamente e não deve ser editado.  As etapas específicas para adicionar a linha 'options' variam de acordo com a distribuição:

* **Ubuntu** (usa resolvconf):
  * adicione a linha de opções para '/etc/resolveconf/resolv.conf.d/head' 
  * execute 'resolvconf -u' para atualizar
* **SUSE** (usa netconf):
  * adicione 'timeout:1 tentativas: 5' ao parâmetro NETCONFIG_DNS_RESOLVER_OPTIONS = "" em '/ etc/sysconfig/rede/config' 
  * execute 'netconfig update' para atualizar
* **OpenLogic** (usa NetworkManager):
  * adicione 'echo "options timeout:1 attempts:5"' em '/etc/NetworkManager/dispatcher.d/11-dhclient' 
  * execute 'service network restart' para atualizar

## <a name="name-resolution-using-your-own-dns-server"></a>Resolução de nome usando seu próprio servidor DNS
Há várias situações em que suas necessidades de resolução de nome podem ir além dos recursos fornecidos pelo Azure, por exemplo, ao usar domínios do Active Directory ou ao precisar de resolução DNS entre redes virtuais.  Para abordar esses cenários, o Azure oferece a capacidade de usar seus próprios servidores DNS.  

Os servidores DNS em uma rede virtual podem encaminhar consultas DNS para os resolvedores recursivos do Azure a fim de resolver nomes de host dentro da rede virtual.  Por exemplo, um DC (controlador de domínio) em execução no Azure pode responder a consultas DNS de seus domínios e encaminhar todas as outras consultas ao Azure.  Isso permite que as VMs vejam tanto seus recursos locais (pelo DC) quanto nomes de host fornecidos pelo Azure (pelo encaminhador).  O acesso a resolvedores recursivos do Azure é fornecido por meio da IP virtual 168.63.129.16.

O encaminhamento do DNS também possibilita a resolução do DNS entre redes virtuais e permite que os computadores locais resolvam nomes de host fornecidos pelo Azure.  Para resolver o nome de host da VM, a VM do servidor DNS deve residir na mesma rede virtual e ser configurado para encaminhar consultas de nome de host ao Azure.  Como o sufixo DNS é diferente em cada rede virtual, você pode usar regras de encaminhamento condicionais para enviar consultas DNS a fim de serem resolvidas pela rede virtual correta.  A imagem a seguir mostra duas redes virtuais e uma rede local fazendo a resolução do DNS entre redes virtuais usando esse método.  Um encaminhador DNS de exemplo está disponível na [galeria de Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) e no [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder).

![DNS entre redes virtuais](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Ao usar a resolução de nomes fornecida pelo Azure, um sufixo DNS interno (*.internal.cloudapp.net) será fornecido para cada VM que usar o DHCP.  Isso permite que a resolução de nomes do host, assim como os registros de nomes do host, estejam na zona internal.cloudapp.net.  Ao usar sua própria solução de resolução de nomes, o sufixo IDNS não será fornecido para as VMs, pois interfere com outras arquiteturas de DNS (como cenários de ingressos em domínio).  Em vez disso, fornecemos um espaço reservado que não funciona (reddog.microsoft.com).  

Se necessário, o sufixo DNS interno pode ser determinado usando o PowerShell ou a API:

* Para as redes virtuais nos modelos de implantação do Gerenciador de Recursos, o sufixo está disponível por meio do recurso [placa de adaptador de rede](https://msdn.microsoft.com/library/azure/mt163668.aspx) ou por meio do cmdlet [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx).    
* No caso de modelos implantação clássica, o sufixo está disponível por meio da chamada à [API Obter Implantação](https://msdn.microsoft.com/library/azure/ee460804.aspx) ou por meio do cmdlet [Get-AzureVM -Debug](https://msdn.microsoft.com/library/azure/dn495236.aspx).

Se o encaminhamento de consultas para o Azure não atender às suas necessidades, você precisará fornecer sua própria solução DNS.  A solução DNS precisará:

* Forneça uma resolução de nomes do host apropriada, por exemplo, por meio do [DDNS](virtual-networks-name-resolution-ddns.md).  Observe que se você usar DDNS, talvez precise desabilitar a limpeza de registro DNS, já que as concessões de DHCP do Azure são muito longas e a limpeza pode remover os registros DNS prematuramente. 
* Fornecer a resolução recursiva apropriada para permitir a resolução de nomes de domínio externo.
* Ser acessível (TCP e UDP na porta 53) dos clientes a que ela serve e ser capaz de acessar a Internet.
* Ter proteção contra acesso da Internet, para atenuar as ameaças impostas por agentes externos.

> [!NOTE]
> Para obter um melhor desempenho, ao usar as VMs do Azure como servidores DNS, o IPv6 deve ser desabilitado e um [IP Público em Nível de Instância](virtual-networks-instance-level-public-ip.md) deve ser atribuído a cada VM do servidor DNS.  Se você optar por usar o Windows Server como o servidor DNS, [este artigo](http://blogs.technet.com/b/networking/archive/2015/08/19/name-resolution-performance-of-a-recursive-windows-dns-server-2012-r2.aspx) fornecerá análise e otimizações de desempenho adicionais.
> 
> 

### <a name="specifying-dns-servers"></a>Especificar servidores DNS
Ao usar seus próprios servidores DNS, o Azure fornece a capacidade de especificar vários servidores DNS por rede virtual ou por interface de rede (Gerenciador de Recursos)/serviço de nuvem (clássico).  Os servidores DNS especificados para uma interface de rede/serviço de nuvem têm precedência sobre os especificados para a rede virtual.

> [!NOTE]
> Propriedades de conexão de rede, como IPs de servidor DNS, não devem ser editadas diretamente dentro de VMs do Windows, já que elas podem ser apagadas durante o serviço de reparo quando o adaptador de rede virtual é substituído. 
> 
> 

Ao usar o modelo de implantação do Gerenciador de Recursos, os servidores DNS podem ser especificados no Portal, na API/nos Modelos ([vnet](https://msdn.microsoft.com/library/azure/mt163661.aspx), [nic](https://msdn.microsoft.com/library/azure/mt163668.aspx)) ou no PowerShell ([vnet](https://msdn.microsoft.com/library/mt603657.aspx), [nic](https://msdn.microsoft.com/library/mt619370.aspx)).

Ao usar o modelo de implantação clássica, os servidores DNS da rede virtual podem ser especificados no Portal ou [no *arquivo Configuração de Rede*](https://msdn.microsoft.com/library/azure/jj157100).  Para serviços de nuvem, os servidores DNS são especificados por meio [do *arquivo de Configuração de Serviço*](https://msdn.microsoft.com/library/azure/ee758710) ou no PowerShell ([New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx)).

> [!NOTE]
> Se alterar as configurações DNS de uma rede virtual/máquina virtual já implantada, você precisará reiniciar cada máquina virtual afetada para que as alterações entrem em vigor.
> 
> 

## <a name="next-steps"></a>Próximas etapas
Modelo de implantação do Gerenciador de Recursos:

* [Criar ou atualizar uma rede virtual](https://msdn.microsoft.com/library/azure/mt163661.aspx)
* [Criar ou atualizar uma placa de interface de rede](https://msdn.microsoft.com/library/azure/mt163668.aspx)
* [New-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx)
* [New-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx)

Modelo de implantação clássico:

* [Esquema de configuração de serviço do Azure](https://msdn.microsoft.com/library/azure/ee758710)
* [Esquema de configuração de Rede Virtual](https://msdn.microsoft.com/library/azure/jj157100)
* [Configurar uma rede virtual usando um arquivo de configuração de rede](virtual-networks-using-network-configuration-file.md) 


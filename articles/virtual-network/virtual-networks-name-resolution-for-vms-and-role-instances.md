---
title: Resolução de nomes para recursos em redes virtuais do Azure
titlesuffix: Azure Virtual Network
description: Cenários de resolução de nomes para a IaaS do Azure, soluções híbridas, entre diferentes serviços de nuvem, Active Directory e usando o seu próprio servidor DNS.
services: virtual-network
documentationcenter: na
author: rohinkoul
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/25/2019
ms.author: rohink
ms.openlocfilehash: fe63b76589c841706ae335c61e56a57c3c33fb3e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60640424"
---
# <a name="name-resolution-for-resources-in-azure-virtual-networks"></a>Resolução de nomes para recursos em redes virtuais do Azure

Dependendo de como você usar o Azure para hospedar o IaaS, o PaaS e as soluções híbridas, pode ser necessário permitir que as VMs (máquinas virtuais) e outros recursos implantados na rede virtual se comuniquem entre si. Embora essa comunicação possa ser habilitada usando endereços IP, é muito mais simples usar nomes que possam ser facilmente lembrados e que não sejam alterados. 

Quando os recursos implantados nas redes virtuais precisam resolver os nomes de domínio para endereços IP internos, eles podem usar um desses dois métodos:

* [Resolução de nomes fornecida pelo Azure](#azure-provided-name-resolution)
* [Resolução de nome que usa seu próprio servidor DNS](#name-resolution-that-uses-your-own-dns-server) (que pode encaminhar consultas para os servidores DNS fornecidos pelo Azure)

O tipo de resolução de nomes usado depende de como seus recursos precisam se comunicar entre si. A tabela a seguir ilustra os cenários e as soluções de resolução de nomes correspondentes:

> [!NOTE]
> Dependendo do cenário, convém usar o recurso Zonas Privadas do DNS do Azure, que está atualmente em Visualização Pública. Para ver mais informações, consulte [Como usar o DNS do Azure para domínios privados](../dns/private-dns-overview.md).
>

| **Cenário** | **Solução** | **Suffix** |
| --- | --- | --- |
| A resolução de nomes entre as VMs localizadas na mesma rede virtual ou instâncias de função dos Serviços de Nuvem do Azure no mesmo serviço de nuvem. | [Zonas Privadas do DNS do Azure](../dns/private-dns-overview.md) ou [Resolução de nomes fornecida pelo Azure](#azure-provided-name-resolution) |Nome do host ou FQDN |
| Resolução de nomes entre VMs em diferentes redes virtuais ou instâncias de função em serviços de nuvem diversos. |[Zonas Privadas do DNS do Azure](../dns/private-dns-overview.md) ou Servidores DNS gerenciados pelo cliente que encaminham consultas entre redes virtuais para resolução pelo Azure (proxy DNS). Confira [Resolução de nomes usando o seu próprio servidor DNS](#name-resolution-that-uses-your-own-dns-server). |Somente FQDN |
| Resolução de nomes de um Serviço de Aplicativo do Azure (Aplicativo Web, Função ou Bot) usando a integração de rede virtual para instâncias de função ou VMs localizadas na mesma rede virtual. |Servidores DNS gerenciados pelo cliente que encaminham consultas entre redes virtuais para resolução pelo Azure (proxy DNS). Confira [Resolução de nomes usando o seu próprio servidor DNS](#name-resolution-that-uses-your-own-dns-server). |Somente FQDN |
| Resolução de nomes de Aplicativos Web do Serviço de Aplicativo para VMs na mesma rede virtual. |Servidores DNS gerenciados pelo cliente que encaminham consultas entre redes virtuais para resolução pelo Azure (proxy DNS). Confira [Resolução de nomes usando o seu próprio servidor DNS](#name-resolution-that-uses-your-own-dns-server). |Somente FQDN |
| Resolução de nomes de Aplicativos Web do Serviço de Aplicativo em uma rede virtual para VMs em redes virtuais diferentes. |Servidores DNS gerenciados pelo cliente que encaminham consultas entre redes virtuais para resolução pelo Azure (proxy DNS). Confira Resolução de nomes usando seu próprio servidor DNS. |Somente FQDN |
| Resolução de nomes de computador e de serviço locais em VMs ou instâncias de função no Azure. |Servidores DNS gerenciados pelo cliente (controlador de domínio local, controlador de domínio somente leitura local ou DNS secundário sincronizado usando transferências de zona, por exemplo). Confira [Resolução de nomes usando o seu próprio servidor DNS](#name-resolution-that-uses-your-own-dns-server). |Somente FQDN |
| Resolução de nomes de host do Azure de computadores locais. |Encaminhe consultas para um servidor de proxy DNS gerenciado pelo cliente na rede virtual correspondente. O servidor proxy encaminha consultas para resolução pelo Azure. Confira [Resolução de nomes usando o seu próprio servidor DNS](#name-resolution-that-uses-your-own-dns-server). |Somente FQDN |
| DNS inverso para IPs internos. |[Resolução de nome usando seu próprio servidor DNS](#name-resolution-that-uses-your-own-dns-server). |Não aplicável |
| Resolução de nomes entre VMs ou instâncias de função localizadas em serviços de nuvem diferentes, não em uma rede virtual. |Não aplicável. Não há suporte para a conectividade entre máquinas virtuais e instâncias de função em diferentes serviços de nuvem fora de uma rede virtual. |Não aplicável|

## <a name="azure-provided-name-resolution"></a>Resolução de nomes fornecida pelo Azure

Junto com a resolução de nomes DNS públicos, o Azure fornece uma resolução de nomes interna para VMs e instâncias de função que residem na mesma rede virtual ou serviço de nuvem. VMs e instâncias em um serviço de nuvem compartilham o mesmo sufixo DNS, por isso somente o nome do host já é suficiente. Porém, em redes virtuais implantadas usando o modelo de implantação clássico, serviços de nuvem diferentes têm sufixos DNS distintos. Nessa situação, você precisa do FQDN para resolver nomes entre diferentes serviços de nuvem. Em redes virtuais implantadas usando o Modelo de implantação do Azure Resource Manager, o sufixo DNS é consistente em toda a rede virtual, de modo que o FQDN não é necessário. Os nomes DNS podem ser atribuídos a VMs e a adaptadores de rede. Embora a resolução de nomes fornecida pelo Azure não exija configurações, ela não é a escolha apropriada para todos os cenários de implantação, como detalhado na tabela anterior.

> [!NOTE]
> Ao usar funções Web e de trabalho nos serviços de nuvem, você também pode acessar os endereços IP internos das instâncias de função utilizando a API REST do Gerenciamento de Serviços do Azure. Para saber mais, veja [Referência da API REST de Gerenciamento de Serviços](https://msdn.microsoft.com/library/azure/ee460799.aspx). O endereço se baseia no nome da função e no número da instância. 
>
>

### <a name="features"></a>Recursos

A resolução de nomes fornecida pelo Azure inclui os seguintes recursos:
* Facilidade de uso. Nenhuma configuração é exigida.
* Alta disponibilidade. Você não precisa criar e gerenciar clusters de seus próprios servidores DNS.
* Você pode usar o serviço em conjunto com seus próprios servidores DNS para resolver nomes de host locais e do Azure.
* Você pode usar a resolução de nomes entre as VMs e as instâncias de função no mesmo serviço de nuvem, sem a necessidade de um FQDN.
* Você pode usar a resolução de nomes entre VMs em redes virtuais que usam o modelo de implantação do Azure Resource Manager, sem a necessidade de um FQDN. Redes virtuais no modelo de implantação clássico exigem um FQDN durante a resolução de nomes em diferentes serviços de nuvem. 
* Você pode usar nomes de host que descrevem melhor as suas implantações, em vez de trabalhar com nomes gerados automaticamente.

### <a name="considerations"></a>Considerações

Pontos a serem considerados quando você estiver usando a resolução de nomes fornecida pelo Azure:
* O sufixo DNS criado pelo Azure não pode ser modificado.
* Não é possível registrar manualmente seus próprios registros.
* Não há suporte para o WINS e NetBIOS. Não é possível ver suas VMs no Windows Explorer.
* Os nomes de host devem ser compatíveis com DNS. Os nomes devem usar somente 0-9, a-z e '-' e não podem começar ou terminar com um '-'.
* O tráfego da consulta DNS é restrita a cada VM. Essa limitação não deve afetar a maioria dos aplicativos. Se a limitação da solicitação for observada, certifique-se de que o armazenamento em cache do cliente está habilitado. Para saber mais, veja [Configuração de cliente DNS](#dns-client-configuration).
* Apenas as VMs dos primeiros 180 serviços de nuvem são registradas para cada rede virtual em um modelo de implantação clássico. Esse limite não se aplica às redes virtuais no Azure Resource Manager.
* O endereço IP de DNS do Azure é 168.63.129.16. Isso é um endereço IP estático e não será alterado.

## <a name="dns-client-configuration"></a>Configuração do cliente DNS

Esta seção aborda o armazenamento em cache do lado do cliente e tentativas do lado do cliente.

### <a name="client-side-caching"></a>Armazenamento em cache do lado do cliente

Nem todas as consultas DNS precisam ser enviada pela rede. O armazenamento em cache do lado do cliente ajuda a reduzir a latência e melhorar a resistência a pontos de luz de rede resolvendo consultas de DNS recorrentes a partir de um cache local. Os registros DNS contêm um mecanismo Time-To-Live (TTL) que permite que o cache armazene o registro o maior tempo possível sem afetar a atualização de registro. Como resultado disso, o cache do lado do cliente é adequado para a maioria das situações.

O cliente DNS padrão do Windows tem um cache DNS interno. Algumas distribuições do Linux não incluem o armazenamento em cache por padrão. Se você desconfiar que não haja um cache local, adicione um cache DNS a cada VM Linux.

Há um número de pacotes disponíveis de cache do DNS diferente (por exemplo, dnsmasq). Veja como instalar o dnsmasq nas distribuições mais comuns:

* **Ubuntu (usa resolvconf)**:
  * Instale o pacote dnsmasq com `sudo apt-get install dnsmasq`.
* **SUSE (usa netconf)**:
  * Instale o pacote dnsmasq com `sudo zypper install dnsmasq`.
  * Habilite o serviço dnsmasq com `systemctl enable dnsmasq.service`. 
  * Inicie o serviço dnsmasq com `systemctl start dnsmasq.service`. 
  * Edite **/etc/sysconfig/network/config** e altere *NETCONFIG_DNS_FORWARDER =""* para *dnsmasq*.
  * Atualize resolv.conf com `netconfig update` para definir o cache como o resolvedor DNS local.
* **OpenLogic (usa NetworkManager)**:
  * Instale o pacote dnsmasq com `sudo yum install dnsmasq`.
  * Habilite o serviço dnsmasq com `systemctl enable dnsmasq.service`.
  * Inicie o serviço dnsmasq com `systemctl start dnsmasq.service`.
  * Adicione *prepend domain-name-servers 127.0.0.1;* a **/etc/dhclient-eth0.conf**.
  * Reinicie o serviço de rede com `service network restart` para definir o cache como o resolvedor DNS local.

> [!NOTE]
> O pacote dnsmasq é apenas um dos vários caches DNS disponíveis para o Linux. Antes de usá-lo, verifique sua adequação para suas necessidades específicas e se nenhum outro cache está instalado.
>
>
    
### <a name="client-side-retries"></a>Tentativa do lado do cliente

O DNS é principalmente um protocolo UDP. Como o protocolo UDP não garante a entrega de mensagens, a lógica de repetição é manipulada no próprio protocolo DNS. Cada cliente DNS (sistema operacional) pode apresentar uma lógica de repetição diferente dependendo da preferência dos criadores:

* Os sistemas operacionais Windows tentam novamente após um segundo e, em seguida, novamente após mais dois, quatro e outros quatro segundos. 
* A configuração padrão do Linux tenta novamente após cinco segundos. É recomendável alterar as especificações de repetição para cinco vezes em intervalos de um segundo.

Verifique as configurações atuais em uma VM Linux com `cat /etc/resolv.conf`. Examine a linha *options*, por exemplo:

```bash
options timeout:1 attempts:5
```

O arquivo resolv.conf é normalmente gerado automaticamente e não deve ser editado. As etapas específicas para adicionar a linha *options* variam de acordo com a distribuição:

* **Ubuntu** (usa resolvconf):
  1. Adicione a linha *options* a **/etc/resolvconf/resolv.conf.d/tail**.
  2. Execute `resolvconf -u` para atualizar.
* **SUSE** (usa netconf):
  1. Adicione *timeout:1 attempts:5* ao parâmetro **NETCONFIG_DNS_RESOLVER_OPTIONS=""** em **/etc/sysconfig/network/config**.
  2. Execute `netconfig update` para atualizar.
* **OpenLogic** (usa NetworkManager):
  1. Adicione *echo "options timeout:1 attempts:5"* to **/etc/NetworkManager/dispatcher.d/11-dhclient**.
  2. Atualize com `service network restart`.

## <a name="name-resolution-that-uses-your-own-dns-server"></a>Resolução de nome usando seu próprio servidor DNS

Esta seção aborda as VMs, as instâncias de função e os aplicativos Web.

### <a name="vms-and-role-instances"></a>VMs e instâncias de função

Suas necessidades de resolução de nomes podem ir além dos recursos fornecidos pelo Azure. Por exemplo, pode ser necessário usar os domínios do Active Directory do Microsoft Windows Server e resolver os nomes de DNS entre as redes virtuais. Para abordar esses cenários, o Azure oferece a capacidade de usar seus próprios servidores DNS.

Os servidores DNS em uma rede virtual podem encaminhar consultas DNS para os resolvedores recursivos no Azure. Isso permite que você resolva os nomes de host dentro dessa rede virtual. Por exemplo, um DC (controlador de domínio) em execução no Azure pode responder a consultas DNS de seus domínios e encaminhar todas as outras consultas ao Azure. O encaminhamento de consultas permite que as VMs vejam tanto seus recursos locais (pelo DC) quanto nomes de host fornecidos pelo Azure (pelo encaminhador). O acesso a resolvedores recursivos no Azure é fornecido por meio da IP virtual 168.63.129.16.

O encaminhamento do DNS também possibilita a resolução do DNS entre redes virtuais e permite que os computadores locais resolvam nomes de host fornecidos pelo Azure. Para resolver o nome de host da VM, a VM do servidor DNS deve residir na mesma rede virtual e ser configurado para encaminhar consultas de nome de host ao Azure. Como o sufixo DNS é diferente em cada rede virtual, você pode usar regras de encaminhamento condicionais para enviar consultas DNS a fim de serem resolvidas pela rede virtual correta. A imagem a seguir mostra duas redes virtuais e uma rede local fazendo a resolução do DNS entre redes virtuais usando esse método. Um encaminhador DNS de exemplo está disponível na [galeria de Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) e no [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder).

> [!NOTE]
> Uma instância de função pode executar a resolução de nomes de máquinas virtuais na mesma rede virtual. Isso é feito usando o FQDN, que consiste no nome do host da VM e o sufixo DNS **internal.cloudapp.net**. No entanto, nesse caso, a resolução de nomes só será bem-sucedida se a instância de função tiver o nome da VM definido no [Esquema de Função (arquivo .cscfg)](https://msdn.microsoft.com/library/azure/jj156212.aspx).
> `<Role name="<role-name>" vmName="<vm-name>">`
>
> As instâncias de função que precisam executar a resolução de nomes de VMs em outra rede virtual (FQDN usando o sufixo **internal.cloudapp.net**) precisam fazer isso usando o método descrito nesta seção (servidores DNS de encaminhamento personalizados entre as duas redes virtuais).
>

![Diagrama do DNS entre redes virtuais](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Quando você estiver usando a resolução de nomes fornecida pelo Azure, o protocolo de configuração de Host dinâmico (DHCP) do Azure fornecerá um sufixo DNS interno (**.internal.cloudapp.net**) para cada VM. Esse sufixo permite que a resolução de nomes do host, assim como os registros de nomes do host, estejam na zona **internal.cloudapp.net**. Ao usar sua própria solução de resolução de nomes, esse sufixo não será fornecido para as VMs, pois interfere com outras arquiteturas de DNS (como cenários associados ao domínio). Em vez disso, o Azure fornece um espaço reservado não funcional (*reddog.microsoft.com*).

Se necessário, você pode determinar o sufixo DNS interno usando o PowerShell ou a API:

* Para redes virtuais nos modelos de implantação do Azure Resource Manager, o sufixo está disponível por meio de [API REST da interface de rede](https://docs.microsoft.com/rest/api/virtualnetwork/networkinterfaces), o [Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) cmdlet do PowerShell e o [ show do AZ network nic](/cli/azure/network/nic#az-network-nic-show) comando CLI do Azure.
* No caso de modelos implantação clássica, o sufixo está disponível por meio da chamada à [API Obter Implantação](https://msdn.microsoft.com/library/azure/ee460804.aspx) ou do cmdlet [Get-AzureVM -Debug](/powershell/module/servicemanagement/azure/get-azurevm).

Se o encaminhamento de consultas para o Azure não atender às suas necessidades, você deverá fornecer sua própria solução DNS. A solução do DNS precisa:

* Fornecer resolução de nome do host apropriada, por exemplo, por meio de [DDNS](virtual-networks-name-resolution-ddns.md). Se você estiver usando DDNS, poderá ser necessário desabilitar a limpeza de registro de DNS. As concessões de DHCP do Azure são longas e a eliminação pode remover os registros DNS prematuramente. 
* Fornecer a resolução recursiva apropriada para permitir a resolução de nomes de domínio externo.
* Ser acessível (TCP e UDP na porta 53) dos clientes a que ela serve e ser capaz de acessar a Internet.
* Ter proteção contra acesso da Internet, para atenuar as ameaças impostas por agentes externos.

> [!NOTE]
> Para melhor desempenho, quando você estiver usando máquinas virtuais do Azure como servidores DNS, o IPv6 deverá ser desabilitado. Um [endereço IP público](virtual-network-public-ip-address.md) deve ser atribuído a cada VM do servidor DNS. Para outras análises de desempenho e otimizações ao usar o Windows Server como seu servidor DNS, confira [Desempenho da resolução de nomes de um Windows DNS Server 2012 R2 recursivo](http://blogs.technet.com/b/networking/archive/2015/08/19/name-resolution-performance-of-a-recursive-windows-dns-server-2012-r2.aspx).
> 
> 

### <a name="web-apps"></a>Aplicativos Web
Suponha que você precise executar a resolução de nome de seu aplicativo Web criado usando o Serviço de Aplicativo, vinculado a uma rede virtual, para as VMs na mesma rede virtual. Além de configurar um servidor DNS personalizado que tenha um encaminhador DNS para encaminhar as consultas para o Azure (IP virtual 168.63.129.16), execute as seguintes etapas:
1. Habilite a integração da rede virtual para seu aplicativo Web, caso não tenha feito isso, conforme descrito em [Integrar o aplicativo a uma rede virtual](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. No portal do Azure, para o Plano do Serviço de Aplicativo que hospeda o aplicativo Web, selecione **Sincronizar rede** em **Rede**, **Integração da Rede Virtual**.

    ![Captura de tela da resolução de nomes de rede virtual](./media/virtual-networks-name-resolution-for-vms-and-role-instances/webapps-dns.png)

Se for necessário realizar a resolução de nomes do seu aplicativo Web criado usando o Serviço de Aplicativo vinculado a uma rede virtual para VMs em uma rede virtual diferente, você precisará usar servidores DNS personalizados em ambas as redes virtuais, da seguinte maneira:

* Configure um servidor DNS na sua rede virtual de destino em uma VM que também pode encaminhar consultas para um resolvedor recursivo do Azure (IP virtual 168.63.129.16). Um encaminhador DNS de exemplo está disponível na [galeria de Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/301-dns-forwarder) e no [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder). 
* Configure um encaminhador de DNS na rede virtual de origem em uma VM. Configure o encaminhador de DNS para encaminhar consultas para o servidor DNS em sua rede virtual de destino.
* Configure o servidor DNS de origem nas configurações da sua rede virtual de origem.
* Habilite a integração da rede virtual para seu aplicativo Web para vincular à rede virtual de origem seguindo as instruções em [Integrar seu aplicativo com uma rede virtual](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* No portal do Azure, para o Plano do Serviço de Aplicativo que hospeda o aplicativo Web, selecione **Sincronizar rede** em **Rede**, **Integração da Rede Virtual**.

## <a name="specify-dns-servers"></a>Especificar servidores de DNS
Quando você estiver usando seus próprios servidores DNS, o Azure fornece a capacidade de especificar vários servidores DNS por rede virtual. Você também pode especificar diversos servidores DNS por adaptador de rede (para o Azure Resource Manager) ou por um serviço de nuvem (para o modelo de implantação clássico). Os servidores DNS especificados para um adaptador de rede ou serviço de nuvem têm precedência sobre aqueles especificados para a rede virtual.

> [!NOTE]
> As propriedades de conexão de rede, como os IPs de servidor DNS, não devem ser editadas diretamente em máquinas virtuais do Windows. Isso ocorre porque eles podem ser apagados durante a recuperação do serviço quando o adaptador de rede virtual é substituído.
>
>

Quando você estiver usando o Modelo de implantação do Azure Resource Manager, poderá especificar servidores DNS para uma rede virtual e um adaptador de rede. Para ver os detalhes, consulte [Gerenciar uma rede virtual](manage-virtual-network.md) e [Gerenciar um adaptador de rede](virtual-network-network-interface.md).

> [!NOTE]
> Se você optar pelo servidor DNS personalizado para a sua rede virtual, você precisará especificar, pelo menos, um endereço IP do servidor DNS; caso contrário, a rede virtual ignorará a configuração e usará o DNS fornecido pelo Azure.
>
>

Ao usar o modelo de implantação clássica, você pode especificar os servidores DNS para a rede virtual no Portal do Azure ou [arquivo Configuração de Rede](https://msdn.microsoft.com/library/azure/jj157100). Para serviços de nuvem, você pode especificar os servidores DNS por meio do [arquivo Configuração de Serviço](https://msdn.microsoft.com/library/azure/ee758710) ou usando o PowerShell, com [New-AzureVM](/powershell/module/servicemanagement/azure/new-azurevm).

> [!NOTE]
> Se alterar as configurações DNS de uma rede virtual ou máquina virtual já implantada, você precisará reiniciar cada máquina virtual afetada para que as alterações entrem em vigor.
>
>

## <a name="next-steps"></a>Próximas etapas

Modelo de implantação do Azure Resource Manager:

* [Gerenciar uma rede virtual](manage-virtual-network.md)
* [Gerenciar um adaptador de rede](virtual-network-network-interface.md)

Modelo de implantação clássico:

* [Esquema de configuração de serviço do Azure](https://msdn.microsoft.com/library/azure/ee758710)
* [Esquema de configuração de Rede Virtual](https://msdn.microsoft.com/library/azure/jj157100)
* [Configurar uma rede virtual usando um arquivo de configuração de rede](virtual-networks-using-network-configuration-file.md)

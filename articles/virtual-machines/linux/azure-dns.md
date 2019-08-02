---
title: Opções de resolução de nomes DNS para máquinas virtuais Linux no Azure
description: Cenários de resolução de nomes para máquinas virtuais Linux no Azure IaaS, incluindo serviços DNS fornecidos, o servidor traga seu próprio DNS e DNS externo híbrido.
services: virtual-machines
documentationcenter: na
author: RicksterCDN
manager: gwallace
editor: tysonn
ms.assetid: 787a1e04-cebf-4122-a1b4-1fcf0a2bbf5f
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2016
ms.author: rclaus
ms.openlocfilehash: d9ff8ca8ce35c8698fc67fa8588eed4228230068
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67668485"
---
# <a name="dns-name-resolution-options-for-linux-virtual-machines-in-azure"></a>Opções de resolução de nomes DNS para máquinas virtuais Linux no Azure
O Azure fornece resolução de nomes DNS por padrão para todas as máquinas virtuais que estão em uma única rede virtual. Você pode implementar sua própria solução de resolução de nomes DNS configurando seus próprios serviços DNS em suas máquinas virtuais que o Azure hospeda. Os cenários a seguir devem ajudá-lo a escolher a que funciona para sua situação.

* [Resolução de nomes que o Azure fornece](#name-resolution-that-azure-provides)
* [Resolução de nome usando o seu próprio servidor DNS](#name-resolution-using-your-own-dns-server)

O tipo de resolução de nomes que você usa depende de como as máquinas virtuais e as instâncias de função precisam se comunicar entre si.

A tabela a seguir ilustra os cenários e as soluções de resolução de nomes correspondentes:

| **Cenário** | **Solução** | **Suffix** |
| --- | --- | --- |
| Resolução de nomes entre as instâncias de função ou as máquinas virtuais na mesma rede virtual |Resolução de nomes que o Azure fornece |nome de host ou FQDN (nome de domínio totalmente qualificado) |
| Resolução de nomes entre as instâncias de função ou as máquinas virtuais em redes virtuais diferentes |Servidores DNS gerenciados pelo cliente que encaminham consultas entre redes virtuais para resolução pelo Azure (proxy DNS). Confira [Resolução de nomes usando o seu próprio servidor DNS](#name-resolution-using-your-own-dns-server). |Somente FQDN |
| Resolução de nomes de serviço e de computador locais em instâncias de função ou máquinas virtuais no Azure |Servidores DNS gerenciados pelo cliente (por exemplo, controlador de domínio local, controlador de domínio somente leitura local ou DNS secundário sincronizados usando transferências de zona). Confira [Resolução de nomes usando o seu próprio servidor DNS](#name-resolution-using-your-own-dns-server). |Somente FQDN |
| Resolução de nomes de host do Azure de computadores locais |Encaminhe consultas para um servidor de proxy de DNS gerenciada pelo cliente na rede virtual correspondente. O servidor proxy encaminha consultas para o Azure para resolução. Confira [Resolução de nomes usando o seu próprio servidor DNS](#name-resolution-using-your-own-dns-server). |Somente FQDN |
| DNS inverso para IPs internos |[Resolução de nome usando o seu próprio servidor DNS](#name-resolution-using-your-own-dns-server) |N/D |

## <a name="name-resolution-that-azure-provides"></a>Resolução de nomes que o Azure fornece
Junto com a resolução de nomes DNS públicos, o Azure fornece uma resolução de nomes interna para máquinas virtuais e instâncias de função que estão na mesma rede virtual. Em redes virtuais baseadas no Azure Resource Manager, o sufixo DNS é consistente em toda a rede virtual; o FQDN não é necessário. Os nomes DNS podem ser atribuídos a máquinas virtuais e placas de adaptador de rede (NICs). Embora a resolução de nomes que o Azure fornece não solicite qualquer configuração, ela não é a escolha apropriada para todos os cenários de implantação, como mostrado na tabela acima.

### <a name="features-and-considerations"></a>Recursos e considerações
**Recursos:**

* não é necessária nenhuma configuração para usar a resolução de nomes que o Azure fornece.
* O serviço de resolução de nomes que o Azure fornece está altamente disponível. Você não precisa criar e gerenciar clusters de seus próprios servidores DNS.
* O serviço de resolução de nomes que o Azure fornece pode ser usado junto com seus próprios servidores DNS para resolver nomes de host locais e do Azure.
* A resolução de nomes é fornecida entre máquinas virtuais em redes virtuais sem a necessidade de FQDN.
* Você pode usar nomes de host que descrevem melhor as suas implantações, em vez de trabalhar com nomes gerados automaticamente.

**Considerações:**

* O sufixo DNS que o Azure cria não pode ser modificado.
* Não é possível registrar manualmente seus próprios registros.
* Não há suporte para o WINS e NetBIOS.
* Os nomes de host devem ser compatíveis com DNS.
    Os nomes devem usar somente 0-9, a-z e '-' e não podem começar ou terminar com um '-'. Veja a RFC 3696, seção 2.
* O tráfego da consulta DNS é restrita a cada máquina virtual. Essa limitação não deve afetar a maioria dos aplicativos.  Se a limitação da solicitação for observada, certifique-se de que o armazenamento em cache do cliente está habilitado.  Para obter mais detalhes, confira [Obtendo o máximo de resolução de nomes que o Azure fornece](#getting-the-most-from-name-resolution-that-azure-provides).

### <a name="getting-the-most-from-name-resolution-that-azure-provides"></a>Obtendo o máximo de resolução de nomes que o Azure fornece
**Armazenamento em cache no lado cliente:**

Algumas consultas DNS não são enviadas pela rede. O armazenamento em cache do lado do cliente ajuda a reduzir a latência e melhorar a resistência a inconsistências de rede resolvendo consultas de DNS recorrentes a partir de um cache local. Os registros DNS contêm um Time-To-Live (TTL) que permite que o cache armazene o registro o maior tempo possível sem afetar a atualização de registro. Como resultado disso, o cache do lado do cliente é adequado para a maioria das situações.

Algumas distribuições do Linux não incluem o armazenamento em cache por padrão. É recomendável que você adicione um cache para cada máquina virtual do Linux, depois de verificar se já não há um cache local.

Vários pacotes de cache DNS diferentes, como dnsmasq, estão disponíveis. Aqui estão as etapas para instalar o dnsmasq nas distribuições mais comuns:

**Ubuntu (usa resolvconf)**
  * Instale o pacote dnsmasq ("sudo apt-get install dnsmasq").

**SUSE (usa netconf)** :
1. Instale o pacote dnsmasq ("sudo zypper install dnsmasq").
2. Habilite o serviço dnsmasq ("systemctl enable dnsmasq.service").
3. Inicie o serviço dnsmasq ("systemctl start dnsmasq.service").
4. Edite "/etc/sysconfig/network/config" e altere NETCONFIG_DNS_FORWARDER = "" para "dnsmasq".
5. Atualize resolv.conf ("netconfig update") para definir o cache do resolvedor DNS local.

**CentOS por Rogue Wave Software (anteriormente OpenLogic) (usa o NetworkManager)**
1. Instale o pacote de dnsmasq ("sudo yum install dnsmasq").
2. Habilite o serviço dnsmasq ("systemctl enable dnsmasq.service").
3. Inicie o serviço dnsmasq ("systemctl start dnsmasq.service").
4. Adicione "prepend domain-name-servers 127.0.0.1;" a "/etc/dhclient-eth0.conf".
5. Reinicie o serviço de rede ("service network restart") para definir o cache como o resolvedor DNS local

> [!NOTE]
> : O pacote 'dnsmasq' é apenas um dos vários caches DNS disponíveis para o Linux. Antes de usá-lo, verifique sua adequação para suas necessidades e se nenhum outro cache está instalado.
>
>

**Tentativa no lado do cliente**

O DNS é principalmente um protocolo UDP. Como o protocolo UDP não garante a entrega de mensagens, o próprio protocolo DNS manipula a lógica de repetição. Cada cliente DNS (sistema operacional) pode apresentar uma lógica de repetição diferente dependendo da preferência dos criadores:

* Os sistemas operacionais Windows tentam novamente após um segundo e, em seguida, novamente após mais dois, quatro e outros quatro segundos.
* A configuração padrão do Linux tenta novamente após cinco segundos.  Altere isso para tentar novamente cinco vezes em intervalos de um segundo.  

Para verificar as configurações atuais em uma máquina virtual do Linux, 'cat /etc/resolv.conf' e examine a linha 'options', por exemplo:

    options timeout:1 attempts:5

O arquivo resolv.conf é gerado automaticamente e não deve ser editado. As etapas específicas que adicionam a linha 'options' variam de acordo com a distribuição:

**Ubuntu** (usa resolvconf)
1. Adicione a linha de opções para '/etc/resolveconf/resolv.conf.d/head'.
2. Execute 'resolvconf -u' para atualizar.

**SUSE** (usa netconf)
1. Adicione 'timeout:1 tentativas: 5' ao parâmetro NETCONFIG_DNS_RESOLVER_OPTIONS = "" em '/ etc/sysconfig/rede/config'.
2. Execute 'netconfig update' para atualizar.

**CentOS por Rogue Wave Software (anteriormente OpenLogic)** (usa o NetworkManager)
1. Adicione 'RES_OPTIONS="timeout:1 attempts:5"' to '/etc/sysconfig/network'.
2. Execute 'service network restart' para atualizar.

## <a name="name-resolution-using-your-own-dns-server"></a>Resolução de nome usando o seu próprio servidor DNS
Suas necessidades de resolução de nomes podem ir além dos recursos do Azure. Por exemplo, você pode exigir a resolução DNS entre redes virtuais. Para abordar esse cenário, você poderá usar seus próprios servidores DNS.  

Os servidores DNS em uma rede virtual podem encaminhar consultas DNS para os resolvedores recursivos do Azure a fim de resolver nomes de host que estão na mesma rede virtual. Por exemplo, um servidor DNS que é executado o Azure pode responder a consultas DNS dos seus próprios arquivos de zona DNS e encaminhar todas as outras consultas ao Azure. Essa funcionalidade permite que máquinas virtuais vejam as entradas nos arquivos de zona e os nomes de host que o Azure fornece (via o encaminhador). O acesso a resolvedores recursivos do Azure é fornecido por meio da IP virtual 168.63.129.16.

O encaminhamento de DNS também habilita a resolução de DNS entre redes virtuais e habilita que suas máquinas locais resolvam os nomes de host que o Azure fornece. Para resolver o nome de host da máquina virtual, a máquina virtual do servidor DNS deve residir na mesma rede virtual e ser configurado para encaminhar consultas de nome de host ao Azure. Como o sufixo DNS é diferente em cada rede virtual, você pode usar regras de encaminhamento condicionais para enviar consultas DNS a fim de serem resolvidas pela rede virtual correta. A imagem a seguir mostra duas redes virtuais e uma rede local fazendo a resolução do DNS entre redes virtuais usando esse método:

![resolução do DNS entre redes virtuais](./media/azure-dns/inter-vnet-dns.png)

Quando você usa a resolução de nomes que o Azure fornece, o sufixo DNS interno é fornecido para cada máquina virtual usando o DHCP. Quando você usa sua própria solução de resolução de nomes, esse sufixo não será fornecido para as máquinas virtuais porque o sufixo interfere com outras arquiteturas de DNS. Para fazer referência a máquinas pelo FQDN, ou para configurar o sufixo em suas máquinas virtuais, você pode usar o PowerShell ou a API para determinar o sufixo:

* Para redes virtuais que são gerenciadas pelo Azure Resource Manager, o sufixo está disponível por meio do recurso [placa de adaptador de rede](https://msdn.microsoft.com/library/azure/mt163668.aspx). Você também pode executar o comando `azure network public-ip show <resource group> <pip name>` para exibir os detalhes de seu IP público, que inclui o FQDN da NIC.

Se o encaminhamento de consultas para o Azure não atender às suas necessidades, você precisará fornecer sua própria solução DNS.  A solução do DNS precisa:

* Fornecer resolução de nome de host apropriada, por exemplo, [DDNS](../../virtual-network/virtual-networks-name-resolution-ddns.md). Se você usar DDNS, convém desabilitar a limpeza de registro de DNS. As concessões de DHCP do Azure são muito longas e a eliminação pode remover os registros DNS prematuramente.
* Fornecer a resolução recursiva apropriada para permitir a resolução de nomes de domínio externo.
* Ser acessível (TCP e UDP na porta 53) dos clientes a que ela serve e ser capaz de acessar a Internet.
* Ter proteção contra acesso da Internet, para atenuar as ameaças impostas por agentes externos.

> [!NOTE]
> Para obter o melhor desempenho, quando usar máquinas virtuais em servidores DNS do Azure, desabilite o IPv6 e atribua um [IP público em nível de instância](../../virtual-network/virtual-networks-instance-level-public-ip.md) para cada máquina virtual do servidor DNS.  
>
>

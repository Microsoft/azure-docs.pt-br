---
title: O que é o DNS privado do Azure?
description: Neste artigo, comece com uma visão geral do serviço de hospedagem de DNS privado no Microsoft Azure.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: overview
ms.date: 6/12/2019
ms.author: rohink
ms.openlocfilehash: 540c4d6f2dde64cef9b5795d635ac6259bfc69c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87985624"
---
# <a name="what-is-azure-private-dns"></a>O que é o DNS privado do Azure?

O Sistema de Nomes de Domínio ou DNS, é responsável por converter (ou resolver) um nome de serviço em seu endereço IP.  O DNS do Azure é um serviço de hospedagem para domínios DNS, fornecendo a resolução de nomes usando a infraestrutura do Microsoft Azure. Além de dar suporte a domínios DNS voltados para a Internet, o DNS do Azure agora também dá suporte a zonas DNS privadas.

O DNS privado do Azure fornece um serviço DNS confiável e seguro para gerenciar e resolver nomes de domínio em uma rede virtual sem a necessidade de adicionar uma solução DNS personalizada. Ao usar zonas DNS privadas você poderá usar seus próprios nomes de domínio personalizados, em vez dos nomes fornecidos pelo Azure atualmente disponíveis. O uso de nomes de domínio personalizados ajuda você a personalizar a sua arquitetura de rede virtual para melhor satisfazer às necessidades da sua organização. Esse uso fornece resolução de nomes para VMs (máquinas virtuais) em uma rede virtual e entre redes virtuais. Além disso, você pode configurar nomes de zonas com uma exibição de omissão de rotas, o que permite que uma zona DNS privada e pública compartilhe o nome.

Para resolver os registros de uma zona DNS privada de sua rede virtual, você deve vincular a rede virtual à zona. As redes virtuais vinculadas têm acesso completo e podem resolver todos os registros DNS publicados na zona privada. Além disso, você também pode habilitar o registro automático em um link de rede virtual. Se você habilitar o registro automático em um link de rede virtual, os registros DNS para as máquinas virtuais nessa rede virtual serão registrados na zona privada. Quando o registro automático estiver habilitado, o DNS do Azure também atualizará os registros de zona sempre que ocorrer a criação de uma máquina virtual, o endereço IP dela mudar ou ela for excluída.

![Visão geral de DNS](./media/private-dns-overview/scenario.png)

> [!NOTE]
> Como melhor prática, não use um domínio *.local* para sua zona DNS privada. Nem todos os sistemas operacionais permitem isso.

## <a name="benefits"></a>Benefícios

O DNS privado do Azure fornece os seguintes benefícios:

* **Remove a necessidade de soluções personalizadas de DNS**. Anteriormente, muitos clientes criaram soluções personalizadas de DNS para gerenciar zonas DNS em suas redes virtuais. Agora, é possível gerenciar zonas DNS usando a infraestrutura nativa do Azure, o que elimina a responsabilidade de criar e gerenciar soluções DNS personalizadas.

* **Usar todos os tipos de registros DNS comuns**. O DNS do Azure dá suporte a registros A, AAAA, CNAME, MX, PTR, SOA, SRV e TXT.

* **Gerenciamento automático de registro de nome do host**. Junto com a hospedagem de seus registros DNS personalizados, o Azure manterá automaticamente os registros de nome do host das VMs em redes virtuais especificadas. Nesse cenário, é possível otimizar os nomes de domínio usados sem precisar criar soluções DNS personalizadas ou modificar aplicativos.

* **Resolução de nome do host entre redes virtuais**. Ao contrário dos nomes de host fornecidos pelo Azure, as zonas de DNS privado podem ser compartilhadas entre redes virtuais. Essa capacidade simplifica os cenários entre redes e de descoberta de serviço, como o emparelhamento de rede virtual.

* **Ferramentas familiares e experiência do usuário**. Para reduzir a curva de aprendizado, esse serviço usa ferramentas bem estabelecidas de DNS do Azure (portal do Azure, Azure PowerShell, CLI do Azure, modelos do Azure Resource Manager e a API REST).

* **Suporte a omissão de rotas DNS**. Com o DNS do Azure, você pode criar zonas com o mesmo nome que resolvem respostas diferentes a partir de uma rede virtual e da Internet pública. Um cenário típico para omissão de rotas DNS é o fornecimento de uma versão dedicada de um serviço para uso dentro de sua rede virtual.

* **Disponível em todas as regiões do Azure**. O recurso de zonas privadas do DNS do Azure está disponível em todas as regiões do Azure na nuvem pública do Azure.

## <a name="capabilities"></a>Funcionalidades

O DNS do Azure fornece os seguintes recursos:

* **Registro automático de máquinas virtuais a partir da rede virtual vinculada a uma zona privada como um registro automático habilitado**. As máquinas virtuais são registradas (adicionadas) à zona privada como registros A apontando para os respectivos endereços IP privados. Quando uma máquina virtual em um vínculo de rede virtual com registro automático é excluída, o DNS do Azure também remove automaticamente o registro DNS correspondente da zona privada vinculada.

* **A resolução de DNS direta tem suporte em redes virtuais vinculadas à zona privada**. Para a resolução de DNS entre redes virtuais, não há dependência explícita de que as redes virtuais sejam emparelhadas umas com as outras. No entanto, talvez você queira emparelhar redes virtuais para outros cenários (por exemplo, tráfego HTTP).

* **Pesquisa inversa do DNS tem suportada dentro do escopo de rede virtual**. A pesquisa inversa do DNS para um IP privado dentro da rede virtual atribuída a uma zona privada retorna o FQDN que inclui o nome do host/registro e o nome da zona como o sufixo.

## <a name="other-considerations"></a>Outras considerações

O DNS do Azure tem as seguintes limitações:

* Uma rede virtual específica pode ser vinculada a apenas uma zona privada se o registro automático dos registros DNS da VM estão habilitados. No entanto, você pode vincular várias redes virtuais a uma única zona DNS.
* O DNS reverso funciona apenas para o espaço de IP privado na rede virtual vinculada
* O DNS reverso de um endereço IP privado para uma rede virtual vinculada retorna *internal.cloudapp.net* como o sufixo padrão para a máquina virtual. Para redes virtuais que estão vinculadas a uma zona privada com o registro automático habilitado, o DNS reverso para um endereço IP privado retorna dois FQDNs, um com o padrão de sufixo *internal.cloudapp.net* e outro com o sufixo de zona privada.
* O encaminhamento condicional não é compatível de modo nativo no momento. Para habilitar a resolução entre o Azure e redes locais, confira [Resolução de nomes para VMs e instâncias de função](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
 
## <a name="pricing"></a>Preços

Para obter informações de preço, confira [Preços do DNS do Azure](https://azure.microsoft.com/pricing/details/dns/).

## <a name="next-steps"></a>Próximas etapas

* Saiba como criar uma zona privada no DNS do Azure usando o [Azure PowerShell](./private-dns-getstarted-powershell.md) ou a [CLI do Azure](./private-dns-getstarted-cli.md).

* Leia mais sobre alguns [cenários comuns de zona privada](./private-dns-scenarios.md) que podem ser realizados com zonas privadas no DNS do Azure.

* Para ver perguntas comuns e obter respostas sobre zonas privadas no DNS do Azure, incluindo o comportamento específico que se pode esperar para determinados tipos de operações, confira as [Perguntas frequentes sobre DNS privado](./dns-faq-private.md).

* Saiba mais sobre zonas e registros DNS, visitando [Visão geral de zonas e registros DNS](dns-zones-records.md).

* Saiba mais sobre alguns dos outros principais [recursos de rede](../networking/networking-overview.md) do Azure.

---
title: Usar DNS do Azure para domínios privados
description: Uma visão geral do serviço de hospedagem de DNS privado no Microsoft Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 3/1/2019
ms.author: victorh
ms.openlocfilehash: 7f5f377f34a43dfb01ea516e023bb98f118d0dd4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596543"
---
# <a name="use-azure-dns-for-private-domains"></a>Usar DNS do Azure para domínios privados

O Sistema de Nomes de Domínio ou DNS, é responsável por converter (ou resolver) um nome de serviço em seu endereço IP. Um serviço de hospedagem para domínios DNS, o DNS do Azure fornece resolução de nomes usando a infraestrutura do Microsoft Azure. Além de dar suporte a domínios DNS para a Internet, o DNS do Azure agora também dá suporte a domínios DNS privados como um recurso de versão prévia.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

O DNS do Azure fornece um serviço DNS confiável e seguro para gerenciar e resolver nomes de domínio em uma rede virtual sem a necessidade de adicionar uma solução DNS personalizada. Ao usar zonas DNS privadas você poderá usar seus próprios nomes de domínio personalizados, em vez dos nomes fornecidos pelo Azure atualmente disponíveis. O uso de nomes de domínio personalizados ajuda você a personalizar a sua arquitetura de rede virtual para melhor satisfazer às necessidades da sua organização. Esse uso fornece resolução de nomes para VMs (máquinas virtuais) em uma rede virtual e entre redes virtuais. Além disso, você pode configurar nomes de zonas com uma exibição de omissão de rotas, o que permite que uma zona DNS privada e pública compartilhe o nome.

Para publicar uma zona de DNS privado em sua rede virtual, você deve especificar a lista de redes virtuais que podem resolver registros na zona. Elas são chamadas de *redes virtuais de resolução*. Também é possível especificar uma rede virtual para a qual o DNS do Azure mantenha registros de nome do host sempre que uma VM for criada, mudar de IP ou for excluída. Ela é chamada de *rede virtual de registro*.

Se você especificar uma rede virtual de registro, os registros DNS das VMs dessa rede virtual, registrados na zona privada, não poderão ser visualizados nem recuperados nas APIs da CLI do Azure e no Azure Powershell, mas os registros da VM serão realmente registrados e resolverão com êxito.

![Visão geral de DNS](./media/private-dns-overview/scenario.png)

> [!NOTE]
> Como prática recomendada, não use um domínio. local para sua zona DNS privada. Nem todos os sistemas operacionais dão suporte a isso.

## <a name="benefits"></a>Benefícios

O DNS do Azure fornece os seguintes benefícios:

* **Remove a necessidade de soluções personalizadas de DNS**. Anteriormente, muitos clientes criaram soluções personalizadas de DNS para gerenciar zonas DNS em suas redes virtuais. Agora, é possível executar o gerenciamento de zona DNS usando a infraestrutura nativa do Azure, o que elimina a responsabilidade de criar e gerenciar soluções DNS personalizadas.

* **Usar todos os tipos de registros DNS comuns**. O DNS do Azure dá suporte a registros A, AAAA, CNAME, MX, PTR, SOA, SRV e TXT.

* **Gerenciamento automático de registro de nome do host**. Junto com a hospedagem de seus registros DNS personalizados, o Azure manterá automaticamente os registros de nome do host das VMs em redes virtuais especificadas. Nesse cenário, é possível otimizar os nomes de domínio usados sem precisar criar soluções DNS personalizadas ou modificar aplicativos.

* **Resolução de nome do host entre redes virtuais**. Ao contrário dos nomes de host fornecidos pelo Azure, as zonas de DNS privado podem ser compartilhadas entre redes virtuais. Essa capacidade simplifica os cenários entre redes e de descoberta de serviço, como o emparelhamento de rede virtual.

* **Ferramentas familiares e experiência do usuário**. Para reduzir a curva de aprendizado, essa nova oferta usa ferramentas bem estabelecidas de DNS do Azure (PowerShell, modelos do Azure Resource Manager e API REST).

* **Suporte a omissão de rotas DNS**. Com o DNS do Azure, você pode criar zonas com o mesmo nome que resolvem respostas diferentes a partir de uma rede virtual e da Internet pública. Um cenário típico para omissão de rotas DNS é o fornecimento de uma versão dedicada de um serviço para uso dentro de sua rede virtual.

* **Disponível em todas as regiões do Azure**. O recurso de zonas privadas do DNS do Azure está disponível em todas as regiões do Azure na nuvem pública do Azure.

## <a name="capabilities"></a>Funcionalidades

O DNS do Azure fornece os seguintes recursos:

* **Registro automático de máquinas virtuais a partir de uma única rede virtual vinculada a uma zona privada como uma rede virtual de registro**. As máquinas virtuais são registradas (adicionadas) à zona privada como registros A apontando para os IPs privados. Quando uma máquina virtual em uma rede virtual de registro é excluída, o Azure também remove automaticamente o registro DNS correspondente da zona privada vinculada. 

  Por padrão, as redes virtuais de registro também atuam como redes virtuais de resolução, pois a resolução de DNS contra a zona funciona de qualquer uma das máquinas virtuais dentro da rede virtual de registro.

  > [!NOTE]
  > Se você especificar uma rede virtual de registro, os registros DNS das VMs dessa rede virtual, registrados na zona privada, não poderão ser visualizados nem recuperados nas APIs da CLI do Azure e no Azure PowerShell. Os registros da VM são realmente registrados e resolverão com sucesso.

* **A resolução de DNS direta tem suporte em redes virtuais vinculadas à zona privada como redes virtuais de resolução**. Para a resolução de DNS entre redes virtuais, não há dependência explícita de que as redes virtuais sejam emparelhadas umas com as outras. No entanto, os clientes podem querer emparelhar redes virtuais para outros cenários (por exemplo, tráfego HTTP).

* **Pesquisa inversa do DNS tem suportada dentro do escopo de rede virtual**. A pesquisa inversa do DNS para um IP privado dentro da rede virtual atribuída a uma zona privada retorna o FQDN que inclui o nome do host/registro e o nome da zona como o sufixo.

## <a name="limitations"></a>Limitações

O DNS do Azure tem as seguintes limitações:

* Apenas uma rede virtual de registro é permitida por zona privada.
* Até 10 redes virtuais de resolução são permitidas por zona privada. Esse limite será removido quando esse recurso estiver disponível em geral.
* Uma rede virtual específica pode ser vinculada a apenas uma zona privada como uma rede virtual de registro.
* Uma rede virtual específica pode ser vinculada a até 10 zonas privadas como uma rede virtual de resolução. Esse limite será removido quando esse recurso estiver disponível em geral.
* Se você especificar uma rede virtual de registro, os registros DNS das VMs dessa rede virtual, registrados na zona privada, não poderão ser visualizados nem recuperados nas APIs da CLI do Azure e no Azure PowerShell. Os registros da VM são realmente registrados e resolverão com sucesso.
* O DNS inverso funciona apenas para o espaço IP privado na rede virtual de registro.
* O DNS inverso para um IP privado que não está registrado na zona privada (por exemplo, um IP privado para uma máquina virtual em uma rede virtual que está vinculada como uma rede virtual de resolução a uma zona privada) retorna *internal.cloudapp.net* como o sufixo DNS. Entretanto, esse sufixo não é resolvível.
* A rede virtual deve estar totalmente vazia na primeira vez em que você a vincular com uma zona privada como uma rede virtual de registro ou resolução. No entanto, a rede virtual pode não estar vazia para futuras vinculações como uma rede virtual de registro ou de resolução a outras zonas privadas.
* No momento, o encaminhamento condicional não tem suporte (por exemplo, para habilitar a resolução entre as redes OnPrem e Azure). Para obter informações sobre como os clientes podem realizar esse cenário por meio de outros mecanismos, consulte [Resolução de nomes de VMs e instâncias de função](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

Para perguntas e respostas comuns sobre zonas privadas no DNS do Azure, incluindo o registro específico de DNS e o comportamento de resolução que pode ser esperado para determinados tipos de operações, consulte as [Perguntas frequentes](./dns-faq.md#private-dns).  

## <a name="pricing"></a>Preços

O recurso de zonas DNS privadas é gratuito durante a visualização pública. Durante a disponibilidade geral, o recurso oferece um modelo de preços com base no uso, semelhante ao da oferta de DNS do Azure existente. 

## <a name="next-steps"></a>Próximas etapas

Saiba como criar uma zona privada no DNS do Azure usando o [Azure PowerShell](./private-dns-getstarted-powershell.md) ou a [CLI do Azure](./private-dns-getstarted-cli.md).

Leia mais sobre alguns [cenários comuns de zona privada](./private-dns-scenarios.md) que podem ser realizados com zonas privadas no DNS do Azure.

Para perguntas e respostas comuns sobre zonas privadas no DNS do Azure, incluindo o comportamento específico que pode ser esperado para determinados tipos de operações, consulte as [Perguntas frequentes](./dns-faq.md#private-dns). 

Saiba mais sobre zonas e registros DNS, visitando [Visão geral de zonas e registros DNS](dns-zones-records.md).

Saiba mais sobre alguns dos outros principais [recursos de rede](../networking/networking-overview.md) do Azure.

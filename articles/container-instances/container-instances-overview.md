---
title: Contêineres sem servidor no Azure
description: O serviço das Instâncias de Contêiner do Azure oferece a maneira mais rápida e mais simples de executar contêineres isolados no Azure, sem a necessidade de gerenciar máquinas virtuais nem adotar um orquestrador de nível superior.
ms.topic: overview
ms.date: 03/22/2021
ms.custom: seodec18, mvc
ms.openlocfilehash: c445687db7a154b6fc86e962d2c2340ad6297431
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104799064"
---
# <a name="what-is-azure-container-instances"></a>O que são as Instâncias de Contêiner do Azure?

Os contêineres estão se tornando o modo preferido para empacotar, implantar e gerenciar aplicativos de nuvem. As Instâncias de Contêiner do Azure oferecem a maneira mais rápida e simples para executar um contêiner no Azure, sem a necessidade de gerenciar máquinas virtuais nem adotar um serviço de nível superior.

As Instâncias de Contêiner do Azure são uma ótima solução para qualquer cenário que possa ser usado em contêineres isolados, incluindo aplicativos simples, automação de tarefas e criação de trabalhos. Para cenários em que você precisa de orquestração de contêineres completa, incluindo descoberta do serviço em vários contêineres, dimensionamento automático e atualizações de aplicativo coordenadas, recomendamos o [AKS (Serviço de Kubernetes do Azure)](../aks/index.yml).

## <a name="fast-startup-times"></a>Inicialização mais rápida

Os contêineres oferecem vantagens significativas de inicialização em relação às VMs (máquinas virtuais). As Instâncias de Contêiner do Azure podem iniciar contêineres no Azure em segundos, sem a necessidade de provisionar nem gerenciar VMs.

Traga imagens de contêiner do Linux ou do Windows do Docker Hub, de um [registro de contêiner do Azure](../container-registry/index.yml) particular ou de outro registro do Docker baseado em nuvem. Confira as [Perguntas frequentes](container-instances-faq.md) para saber quais Registros têm suporte das ACI. As Instâncias de Contêiner do Azure armazenam em cache várias imagens de SO base comuns, ajudando a acelerar a implantação de suas imagens de aplicativo personalizadas.

## <a name="container-access"></a>Acesso ao contêiner

As Instâncias de Contêiner do Azure permitem expor seus grupos de contêineres diretamente à Internet com um endereço IP e um FQDN (nome de domínio totalmente qualificado). Quando você cria uma instância de contêiner, pode especificar uma etiqueta de nome de DNS personalizado para que o seu aplicativo seja acessível em *customlabel*.*azureregion*.azurecontainer.io.

As Instâncias de Contêiner do Azure também são compatíveis com a execução de um comando em um contêiner em execução, fornecendo um shell interativo para ajudar no desenvolvimento e na solução de problemas de aplicativos. O acesso ocorre por HTTPS, usando TLS para proteger conexões de cliente.

> [!IMPORTANT]
> Desde 13 de janeiro de 2020, as Instâncias de Contêiner do Azure exigem que todas as conexões seguras de servidores e aplicativos usem TLS 1.2. O suporte para TLS 1.0 e 1.1 será desativado.

## <a name="compliant-deployments"></a>Implantações em conformidade

### <a name="hypervisor-level-security"></a>Segurança em nível de hipervisor

Historicamente, os contêineres ofereciam isolamento de dependência de aplicativo e governança de recursos, mas não eram considerados suficientemente protegidos para uso com vários locatários hostis. As Instâncias de Contêiner do Azure garantem que seu aplicativo fique tão isolado em um contêiner quanto ficaria em uma VM.

### <a name="customer-data"></a>Dados do cliente

O serviço de ACI armazena os dados mínimos do cliente necessários para garantir que os grupos de contêineres estejam sendo executados conforme o esperado. O armazenamento de dados do cliente em apenas uma região está disponível atualmente apenas na região do Sudeste da Ásia (Singapura), na área geográfica do Pacífico Asiático, e na região Sul do Brasil (Estado de São Paulo), na área geográfica do Brasil. Para todas as outras regiões, os dados do cliente são armazenados na [Área geográfica](https://azure.microsoft.com/global-infrastructure/geographies/). Entre em contato com o Suporte do Azure para saber mais.

## <a name="custom-sizes"></a>Tamanhos personalizados

Os contêineres normalmente são otimizados para executar apenas um único aplicativo, mas as necessidades exatas desses aplicativos podem variar significativamente. As Instâncias de Contêiner do Azure fornecem uma utilização ideal ao permitirem especificações exatas de núcleos e memória da CPU. Você paga com base no que precisar e é cobrado por segundo, assim, é possível ajustar seus gastos eficientemente com base em suas necessidades reais.

Para trabalhos de computação intensiva, como o aprendizado de máquina, as Instâncias de Contêiner do Azure podem agendar contêineres do Linux para usar os [recursos da GPU](container-instances-gpu.md) da NVIDIA Tesla (versão prévia).

## <a name="persistent-storage"></a>Armazenamento persistente

Para recuperar e persistir estados com as Instâncias de Contêiner do Azure, nós oferecemos a [montagem direta de compartilhamentos de Arquivos do Azure](./container-instances-volume-azure-files.md) apoiada pelo Armazenamento do Azure.

## <a name="linux-and-windows-containers"></a>Contêineres do Windows e do Linux

As Instâncias de Contêiner do Azure podem agendar contêineres do Windows e do Linux com a mesma API. Basta especificar o tipo de sistema operacional ao criar seus [grupos de contêiner](container-instances-container-groups.md).

Alguns recursos estão restritos a contêineres Linux:

* Vários contêineres por grupo de contêineres
* Montagem de volume ([Arquivos do Azure](container-instances-volume-azure-files.md), [emptyDir](container-instances-volume-emptydir.md), [GitRepo](container-instances-volume-gitrepo.md), [segredo](container-instances-volume-secret.md))
* [Métricas de uso do recurso](container-instances-monitor.md) com o Azure Monitor
* [Implantação da rede virtual](container-instances-vnet.md)
* [Recursos da GPU](container-instances-gpu.md) (versão prévia)

Para implantações de contêiner do Windows, use imagens baseadas em [imagens base do Windows](container-instances-faq.md#what-windows-base-os-images-are-supported) comuns.

## <a name="co-scheduled-groups"></a>Grupos coagendados

As Instâncias de Contêiner do Azure são compatíveis com a programação de [grupos com vários contêineres](container-instances-container-groups.md) que compartilham um computador host, uma rede local, um armazenamento e um ciclo de vida. Isso permite que você combine seu aplicativo principal com outros contêineres com função de suporte, como sidecars de log.

## <a name="virtual-network-deployment"></a>Implantação da rede virtual

As Instâncias de Contêiner do Azure permitem a [implantação de instâncias de contêiner em uma rede virtual do Azure](container-instances-vnet.md). Quando implantadas em uma sub-rede dentro de sua rede virtual, as instâncias de contêiner podem se comunicar com segurança com outros recursos na rede virtual, incluindo os que são locais (por meio do [gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou do [ExpressRoute](../expressroute/expressroute-introduction.md)).

## <a name="next-steps"></a>Próximas etapas

Tente implantar um contêiner no Azure com um único comando usando nosso guia de início rápido:

> [!div class="nextstepaction"]
> [Início Rápido de Instâncias de Contêiner do Azure](container-instances-quickstart.md)

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

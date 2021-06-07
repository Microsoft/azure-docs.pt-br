---
title: Perguntas frequentes
description: Respostas para perguntas frequentes relacionadas ao serviço de instâncias de contêiner do Azure
author: macolso
ms.author: macolso
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 2645c3f183e2217dff28a96c9c0d376eb82a6476
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103573125"
---
# <a name="frequently-asked-questions-about-azure-container-instances"></a>Perguntas frequentes sobre as instâncias de contêiner do Azure

Este artigo aborda as perguntas frequentes sobre as instâncias de contêiner do Azure.

## <a name="deployment"></a>Implantação

### <a name="how-large-can-my-container-image-be"></a>Qual é o tamanho de minha imagem de contêiner?

O tamanho máximo de uma imagem de contêiner implantável nas instâncias de contêiner do Azure é de 15 GB. Você pode implantar imagens maiores dependendo da disponibilidade exata no momento da implantação, mas isso não é garantido.

O tamanho da sua imagem de contêiner afeta o tempo necessário para implantar, portanto, geralmente você deseja manter suas imagens de contêiner o mais pequeno possível.

### <a name="how-can-i-speed-up-the-deployment-of-my-container"></a>Como posso acelerar a implantação do meu contêiner?

Como um dos principais determinantes dos tempos de implantação é o tamanho da imagem, procure maneiras de reduzir o tamanho. Remova as camadas que você não precisa ou reduza o tamanho das camadas na imagem (escolhendo uma imagem de sistema operacional base mais clara). Por exemplo, se você estiver executando contêineres do Linux, considere usar o Alpineum como sua imagem base em vez de um servidor Ubuntu completo. Da mesma forma, para contêineres do Windows, use uma imagem base do nano Server, se possível. 

Você também deve verificar a lista de imagens previamente armazenadas em imagens de contêiner do Azure, disponível por meio da API de [imagens em cache de lista](/rest/api/container-instances/location/listcachedimages) . Talvez seja possível mudar uma camada de imagem para uma das imagens previamente armazenadas em cache. 

Veja [diretrizes mais detalhadas](container-instances-troubleshooting.md#container-takes-a-long-time-to-start) sobre como reduzir o tempo de inicialização do contêiner.

### <a name="what-windows-base-os-images-are-supported"></a>Quais imagens do sistema operacional base do Windows têm suporte?

> [!NOTE]
> Devido a problemas com a compatibilidade com versões anteriores, após as atualizações do Windows no 2020, as seguintes versão de imagem incluem o número mínimo de versões que recomendamos que você use em sua imagem de base. As implantações atuais usando versões de imagem mais antigas não são afetadas, mas novas implantações devem aderir às seguintes imagens base. 

#### <a name="windows-server-2016-base-images"></a>Imagens base do Windows Server 2016

* [Nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `sac2016` `10.0.14393.3506` ou mais recente
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2016`  `10.0.14393.3506` ou mais recente

> [!NOTE]
> Não há suporte para imagens do Windows baseadas em Semi-Annual versão 1709 ou 1803 do canal.

#### <a name="windows-server-2019-and-client-base-images"></a>Imagens base do Windows Server 2019 e do cliente

* [Nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `1809` `10.0.17763.1040` ou mais recente
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2019` , `1809` `10.0.17763.1040` ou mais recente
* [Windows](https://hub.docker.com/_/microsoft-windows): `1809` , `10.0.17763.1040` ou mais recente

### <a name="what-net-or-net-core-image-layer-should-i-use-in-my-container"></a>Qual camada de imagem .NET ou .NET Core devo usar em meu contêiner? 

Use a menor imagem que atenda às suas necessidades. Para o Linux, você poderia usar uma imagem de *tempo de execução* do .NET Core, que tem suporte desde o lançamento do .net Core 2,1. Para o Windows, se você estiver usando o .NET Framework completo, precisará usar uma imagem do Windows Server Core (imagem somente de tempo de execução, como  *4.7.2-windowsservercore-ltsc2016*). As imagens somente de tempo de execução são menores, mas não dão suporte a cargas de trabalho que exigem o SDK do .NET.

> [!NOTE]
> ACI não pode extrair imagens de registros não compatíveis com o OCI.

### <a name="what-types-of-container-registries-are-compatible-with-aci"></a>Quais tipos de registros de contêiner são compatíveis com o ACI?

O ACI dá suporte a pulls de imagem do ACR e de outros registros de contêiner de terceiros, como DockerHub. O ACI dá suporte a pulls de imagem do ACR e de outros registros de contêiner compatíveis com o OCI de terceiros, como o DockerHub, com um ponto de extremidade que é exposto publicamente à Internet.

## <a name="availability-and-quotas"></a>Disponibilidade e cotas

### <a name="how-many-cores-and-memory-should-i-allocate-for-my-containers-or-the-container-group"></a>Quantos núcleos e memória devo alocar para meus contêineres ou o grupo de contêineres?

Isso realmente depende de sua carga de trabalho. Inicie o desempenho pequeno e teste para ver como seus contêineres fazem. [Monitore o uso de recursos de CPU e memória](container-instances-monitor.md)e, em seguida, adicione núcleos ou memória com base no tipo de processo que você implanta no contêiner.

Verifique também a [disponibilidade de recursos](container-instances-region-availability.md) para a região em que você está implantando para os limites superiores em núcleos de CPU e memória disponível por grupo de contêineres. 

> [!NOTE]
> Uma pequena quantidade de recursos de um grupo de contêineres é usada pela infraestrutura subjacente do serviço. Seus contêineres poderão acessar a maioria, mas não todos os recursos alocados para o grupo. Por esse motivo, planeje um pequeno buffer de recursos ao solicitar recursos para contêineres no grupo.

### <a name="what-underlying-infrastructure-does-aci-run-on"></a>Em qual infraestrutura subjacente a ACI é executada?

As instâncias de contêiner do Azure visam ser um serviço de contêineres sob demanda sem servidor, portanto, queremos que você esteja concentrado em desenvolver seus contêineres e não se preocupe com a infraestrutura! Para aqueles curiosos ou querendo comparações de desempenho, o ACI é executado em conjuntos de VMs do Azure de várias SKUs, principalmente da série F e D. Esperamos que isso seja alterado no futuro, pois continuamos a desenvolver e otimizar o serviço. 

### <a name="i-want-to-deploy-thousand-of-cores-on-aci---can-i-get-my-quota-increased"></a>Quero implantar milhares de núcleos em ACI-posso aumentar minha cota?
 
Sim (às vezes). Consulte o artigo [cotas e limites](container-instances-quotas.md) para cotas atuais e quais limites podem ser aumentados por solicitação.

### <a name="can-i-deploy-with-more-than-4-cores-and-16-gb-of-ram"></a>Posso implantar com mais de 4 núcleos e 16 GB de RAM?

Ainda não. Atualmente, esses são os máximos para um grupo de contêineres. Contate o suporte do Azure com requisitos ou solicitações específicas. 

### <a name="when-will-aci-be-in-a-specific-region"></a>Quando o ACI estará em uma região específica?

A disponibilidade da região atual é publicada [aqui](container-instances-region-availability.md). Se você tiver um requisito para uma região específica, entre em contato com o suporte do Azure.

## <a name="features-and-scenarios"></a>Recursos e cenários

### <a name="how-do-i-scale-a-container-group"></a>Como fazer dimensionar um grupo de contêineres?

Atualmente, o dimensionamento não está disponível para contêineres ou grupos de contêineres. Se você precisar executar mais instâncias, use nossa API para automatizar e criar mais solicitações para a criação do grupo de contêineres para o serviço. 

### <a name="what-features-are-available-to-instances-running-in-a-custom-vnet"></a>Quais recursos estão disponíveis para instâncias em execução em uma VNet personalizada?

Você pode [implantar grupos de contêineres em uma rede virtual do Azure](container-instances-vnet.md) de sua escolha e delegar IPS privados aos grupos de contêineres para rotear o tráfego na VNet entre os recursos do Azure. Para cenários de rede e limitações com instâncias de contêiner do Azure, consulte [cenários e recursos de rede virtual](container-instances-virtual-network-concepts.md).

## <a name="pricing"></a>Preços

### <a name="when-does-the-meter-start-running"></a>Quando o medidor começa a ser executado?

A duração do grupo de contêineres é calculada a partir do momento em que começamos a efetuar pull da imagem de seu primeiro contêiner (para uma nova implantação) ou o grupo de contêineres é reiniciado (se já implantado), até que o grupo de contêineres seja interrompido. Veja os detalhes em [preços das instâncias de contêiner](https://azure.microsoft.com/pricing/details/container-instances/).

### <a name="do-i-stop-being-charged-when-my-containers-are-stopped"></a>Eu parar de ser cobrado quando meus contêineres forem interrompidos?

Os medidores param de ser executados quando todo o grupo de contêineres é interrompido. Contanto que um contêiner em seu grupo de contêineres esteja em execução, guardaremos os recursos caso você queira iniciar os contêineres novamente. 

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais](container-instances-overview.md) sobre as instâncias de contêiner do Azure.
* [Solucionar problemas comuns](container-instances-troubleshooting.md) em instâncias de contêiner do Azure.

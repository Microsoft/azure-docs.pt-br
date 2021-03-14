---
title: Implantar o horizonte na solução VMware do Azure
description: Saiba como implantar o VMware horizonte na solução VMware do Azure.
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: c34d0ac7806f8284e893cf3ad4f3c82dd404ff41
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181390"
---
# <a name="deploy-horizon-on-azure-vmware-solution"></a>Implantar o horizonte na solução VMware do Azure 

>[!NOTE]
>Este documento se concentra no produto VMware Horizonte, anteriormente conhecido como horizonte 7. O horizonte é uma solução diferente da nuvem do horizonte no Azure, embora haja alguns componentes compartilhados. As principais vantagens da solução Azure VMware incluem um método de dimensionamento mais direto e a integração do gerenciamento do VMware Cloud Foundation ao portal do Azure.

O [VMware horizonte](https://www.vmware.com/products/horizon.html)®, uma plataforma de área de trabalho virtual e aplicativos, executado no Data Center e fornece gerenciamento simples e centralizado. Ele fornece áreas de trabalho virtuais e aplicativos em qualquer dispositivo, em qualquer lugar. O horizonte permite que você crie e agente conexões com áreas de trabalho virtuais do Windows e Linux, aplicativos hospedados do Área de Trabalho Remota Server (RDS), áreas de trabalho e computadores físicos.

Aqui, nos concentramos especificamente na implantação do horizonte na solução VMware do Azure. Para obter informações gerais sobre o VMware Horizonte, consulte a documentação de produção do horizonte:

* [O que é o VMware horizonte?](https://www.vmware.com/products/horizon.html)

* [Saiba mais sobre o VMware horizonte](https://docs.vmware.com/en/VMware-Horizon/index.html)

* [Arquitetura de referência de horizonte](https://techzone.vmware.com/resource/workspace-one-and-horizon-reference-architecture)

Com a introdução do horizonte na solução VMware do Azure, agora há duas soluções de VDI (Virtual Desktop Infrastructure) na plataforma Azure. O diagrama a seguir resume as principais diferenças em um alto nível.

:::image type="content" source="media/horizon/difference-horizon-azure-vmware-solution-horizon-cloud-azure.png" alt-text="Horizonte na solução VMware do Azure e na nuvem de horizonte no Azure" border="false":::

O horizonte 2006 e versões posteriores na linha de lançamento do horizonte 8 dão suporte à implantação local e à implantação da solução do Azure VMware. Há alguns recursos de horizonte com suporte no local, mas não na solução do Azure VMware. Também há suporte para produtos adicionais no ecossistema de horizonte. Para obter informações, consulte [paridade e interoperabilidade de recursos](https://kb.vmware.com/s/article/80850).

## <a name="deploy-horizon-in-a-hybrid-cloud"></a>Implantar o horizonte em uma nuvem híbrida

Você pode implantar o horizonte em um ambiente de nuvem híbrida ao usar a contador (arquitetura de pod de nuvem do horizonte) para interconectar os data centers locais e do Azure. O contador dimensiona sua implantação, cria uma nuvem híbrida e fornece redundância para a continuidade dos negócios e a recuperação de desastres.  Para obter mais informações, consulte [expandindo ambientes existentes do horizonte 7](https://techzone.vmware.com/resource/business-continuity-vmware-horizon#_Toc41650874).

>[!IMPORTANT]
>CONTADOR não é uma implantação ampliada; cada pod de horizonte é distinto e todos os servidores de conexão que pertencem a cada pods individual devem estar localizados em um único local e ser executados no mesmo domínio de transmissão de uma perspectiva de rede.

Como o data center local ou privado, o horizonte pode ser implantado em uma nuvem privada da solução Azure VMware. Discutiremos as principais diferenças na implantação do horizonte local e na solução Azure VMware nas seções a seguir.

A nuvem privada do Azure é conceitualmente a mesma do VMware SDDC, um termo normalmente usado na documentação do horizonte. O restante deste documento usa os termos a nuvem privada do Azure e o VMware SDDC intercambiáveis.

O conector de nuvem horizonte é necessário para o horizonte na solução VMware do Azure para gerenciar licenças de assinatura. O conector de nuvem pode ser implantado na rede virtual do Azure junto com os servidores de conexão de horizonte.

>[!IMPORTANT]
>O suporte ao plano de controle de horizonte para o horizonte na solução VMware do Azure ainda não está disponível. Certifique-se de baixar a versão VHD do conector de nuvem do horizonte.

## <a name="vcenter-cloud-admin-role"></a>função de administrador de nuvem do vCenter

Como a solução Azure VMware é um serviço SDDC e o Azure gerencia o ciclo de vida do SDDC na solução Azure VMware, o modelo de permissão do vCenter na solução Azure VMware é limitado por design.

Os clientes são obrigados a usar a função de administrador de nuvem, que tem um conjunto limitado de permissões do vCenter. O produto de horizonte foi modificado para funcionar com a função de administrador de nuvem na solução VMware do Azure, especificamente:

* O provisionamento de clones instantâneos foi modificado para ser executado na solução VMware do Azure.

* Uma política vSAN específica (VMware_Horizon) foi criada na solução Azure VMware para trabalhar com o horizonte, que deve estar disponível e usado no SDDCs implantado para horizonte.

* o CBRC (vSphere content-based Read cache), também conhecido como View Storage Accelerator, é desabilitado durante a execução na solução VMware do Azure.

>[!IMPORTANT]
>CBRC não deve ser ativado novamente.

>[!NOTE]
>A solução Azure VMware configura automaticamente as configurações de horizonte específicas, desde que você implante o horizonte 2006 (também conhecido como horizonte 8) e acima na ramificação do horizonte 8 e selecione a opção **Azure** no instalador do servidor de conexão de horizonte.

## <a name="horizon-on-azure-vmware-solution-deployment-architecture"></a>Horizonte na arquitetura de implantação da solução Azure VMware

Um design típico de arquitetura de horizonte usa um pod e uma estratégia de bloqueio. Um bloco é um único vCenter, enquanto vários blocos combinados fazem um pod. Um pod de horizonte é uma unidade de organização determinada pelos limites de escalabilidade de horizonte. Cada pod de horizonte tem um portal de gerenciamento separado e, portanto, uma prática de design padrão é minimizar o número de pods.

Cada nuvem tem seu próprio esquema de conectividade de rede. Combinado com a rede do VMware SDDC/NSX Edge, a conectividade de rede da solução Azure VMware apresenta requisitos exclusivos para a implantação de horizonte que é diferente do local.

Cada nuvem privada do Azure e o SDDC podem lidar com sessões de desktop ou de aplicativo 4.000, supondo que:

* O tráfego de carga de trabalho se alinha com o perfil do operador de tarefa LoginVSI.

* Somente o tráfego de protocolo é considerado, sem dados de usuário.

* O NSX Edge está configurado para ser grande.

>[!NOTE]
>Seu perfil de carga de trabalho e suas necessidades podem ser diferentes e, portanto, os resultados podem variar com base no seu caso de uso. Os volumes de dados do usuário podem reduzir os limites de escala no contexto de sua carga de trabalho. Dimensione e planeje sua implantação de acordo. Para obter mais informações, consulte as diretrizes de dimensionamento na seção [tamanho hosts da solução do Azure VMware para implantações de horizonte](#size-azure-vmware-solution-hosts-for-horizon-deployments) .

Dada a nuvem privada do Azure e o limite máximo do SDDC, recomendamos uma arquitetura de implantação em que os servidores de conexão de horizonte e os UAGs (gateways de acesso unificado) da VMware estejam em execução dentro da rede virtual do Azure. Ele efetivamente transforma cada nuvem privada do Azure e o SDDC em um bloco. Por sua vez, maximizar a escalabilidade do horizonte em execução na solução VMware do Azure.

A conexão da rede virtual do Azure com as nuvens/SDDCs privadas do Azure deve ser configurada com o ExpressRoute FastPath. O diagrama a seguir mostra uma implantação de Pod básico.

:::image type="content" source="media/horizon/horizon-pod-deployment-expresspath-fast-path.png" alt-text="Implantação de pod de horizonte típica usando o caminho rápido do ExpressPath" border="false":::

## <a name="network-connectivity-to-scale-horizon-on-azure-vmware-solution"></a>Conectividade de rede para o horizonte de escala na solução VMware do Azure

Esta seção descreve a arquitetura de rede em um alto nível com alguns exemplos comuns de implantação para ajudá-lo a dimensionar o horizonte na solução VMware do Azure. O foco está especificamente em elementos críticos de rede. 

### <a name="single-horizon-pod-on-azure-vmware-solution"></a>Pod de horizonte único na solução VMware do Azure

:::image type="content" source="media/horizon/single-horizon-pod-azure-vmware-solution.png" alt-text="Pod de horizonte único na solução VMware do Azure" border="false":::

Um único pod de horizonte é o cenário de implantação mais direto, pois você implanta apenas um pod de horizonte na região leste dos EUA.  Como cada nuvem privada e o SDDC são estimados para lidar com 4.000 sessões de desktop, você implanta o tamanho máximo de pod de horizonte.  Você pode planejar a implantação de até três nuvens/SDDCs privadas.

Com as VMs (máquinas virtuais) de infraestrutura de horizonte implantadas na rede virtual do Azure, você pode alcançar as 12.000 sessões por pod de horizonte. A conexão entre cada nuvem privada e o SDDC para a rede virtual do Azure é o caminho rápido do ExpressRoute.  Não é necessário tráfego leste-oeste entre nuvens privadas. 

As principais suposições para esse exemplo de implantação básica incluem:

* Você não tem um pod de horizonte local que deseja conectar a esse novo pod usando a contador (arquitetura de pod de nuvem).

* Os usuários finais se conectam às suas áreas de trabalho virtuais por meio da Internet (vs. conectando por meio de um data center local).

Conecte seu controlador de domínio do AD na rede virtual do Azure com seu AD local por meio do circuito VPN ou ExpressRoute.

Uma variação no exemplo básico pode ser oferecer suporte à conectividade para recursos locais. Por exemplo, os usuários acessam áreas de trabalho e geram tráfego de aplicativo de área de trabalho virtual ou se conectam a um pod de horizonte local usando contador.

O diagrama mostra como dar suporte à conectividade para recursos locais. Para se conectar à rede corporativa à rede virtual do Azure, você precisará de um circuito do ExpressRoute.  Você também precisará conectar sua rede corporativa a cada uma das nuvem privada e SDDCs usando o ExpressRoute Alcance Global.  Ele permite a conectividade do SDDC para o circuito do ExpressRoute e recursos locais. 

:::image type="content" source="media/horizon/connect-corporate-network-azure-virtual-network.png" alt-text="Conectar sua rede corporativa a uma rede virtual do Azure" border="false":::

### <a name="multiple-horizon-pods-on-azure-vmware-solution-across-multiple-regions"></a>Vários pods de horizonte na solução do Azure VMware em várias regiões

Outro cenário é o horizonte de dimensionamento em vários pods.  Nesse cenário, você implanta dois pods de horizonte em duas regiões diferentes e federa-os usando contador. Ele é semelhante à configuração de rede no exemplo anterior, mas com alguns links entre regiões adicionais. 

Você conectará o Entrada na Rede virtual do Azure a cada região para nuvens privadas/SDDCs na outra região. Ele permite que os servidores de conexão de horizonte parte da Federação contador se conectem a todos os desktops sob gerenciamento. Adicionar nuvens/SDDCs privadas adicionais a essa configuração permitiria Dimensionar para 24.000 sessões em geral. 

Os mesmos princípios se aplicam se você implantar dois pods de horizonte na mesma região.  Certifique-se de implantar o segundo pod de horizonte em uma *rede virtual do Azure separada*. Assim como o único exemplo de Pod, você pode conectar sua rede corporativa e o Pod local a este exemplo multipod/região usando ExpressRoute e Alcance Global. 

:::image type="content" source="media/horizon/multiple-horizon-pod-azure-vmware-solution.png" alt-text=" Vários pods de horizonte na solução do Azure VMware em várias regiões" border="false":::

## <a name="size-azure-vmware-solution-hosts-for-horizon-deployments"></a>Dimensionar hosts de solução do Azure VMware para implantações de horizonte 

A metodologia de dimensionamento do horizonte em um host em execução na solução VMware do Azure é mais simples do que o horizonte local.  Isso ocorre porque o host da solução Azure VMware está padronizado.  O dimensionamento exato do host ajuda a determinar o número de hosts necessários para dar suporte aos seus requisitos de VDI.  É fundamental determinar o custo por área de trabalho.

### <a name="sizing-tables"></a>Dimensionando tabelas

Os requisitos específicos de vCPU/vRAM para áreas de trabalho virtuais de horizonte dependem do perfil de carga específico do cliente.   Trabalhe com sua equipe de vendas do MSFT e do VMware para ajudar a determinar seus requisitos de vCPU/vRAM para suas áreas de trabalho virtuais. 

| vCPU por VM | vRAM por VM (GB) | Instância | 100 VMs | 200 VMs | 300 VMs | 400 VMs | 500 VMs | 600 VMs | 700 VMs | 800 VMs | 900 VMs | 1000 VMs | 2000 VMs | 3000 VMs | 4000 VMs | 5000 VMs | 6000 VMs | 6400 VMs |
|:-----------:|:----------------:|:--------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:--------:|:--------:|:--------:|:--------:|:--------:|:--------:|:--------:|
|      2      |        3,5       |    AVS   |    3    |    3    |    4    |    4    |    5    |    6    |    6    |    7    |    8    |     9    |    17    |    25    |    33    |    41    |    49    |    53    |
|      2      |         4        |    AVS   |    3    |    3    |    4    |    5    |    6    |    6    |    7    |    8    |    9    |     9    |    18    |    26    |    34    |    42    |    51    |    54    |
|      2      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    12   |    13    |    26    |    38    |    51    |    62    |    75    |    79    |
|      2      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      2      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      2      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      4      |        3,5       |    AVS   |    3    |    3    |    4    |    5    |    6    |    7    |    8    |    9    |    10   |    11    |    22    |    33    |    44    |    55    |    66    |    70    |
|      4      |         4        |    AVS   |    3    |    3    |    4    |    5    |    6    |    7    |    8    |    9    |    10   |    11    |    22    |    33    |    44    |    55    |    66    |    70    |
|      4      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    12   |    13    |    26    |    38    |    51    |    62    |    75    |    79    |
|      4      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      4      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      4      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      6      |        3,5       |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         4        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      6      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      6      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      8      |        3,5       |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         4        |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         6        |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      8      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      8      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |


### <a name="horizon-sizing-inputs"></a>Entradas de dimensionamento de horizonte

Veja o que você precisará coletar para sua carga de trabalho planejada:

* Número de áreas de trabalho simultâneas

* VCPU obrigatórios por desktop

* VRAM necessária por desktop

* Armazenamento necessário por desktop

Em geral, as implantações de VDI são limitadas por CPU ou RAM, o que determina o tamanho do host. Vamos pegar o exemplo a seguir para um tipo de carga de trabalho do LoginVSI Worker, validado com testes de desempenho:

* 2.000 implantação de área de trabalho simultânea

* 2vCPU por área de trabalho.

* vRAM de 4 GB por área de trabalho.

* 50 GB de armazenamento por área de trabalho

Para este exemplo, o número total de hosts de fatores para 18, produzindo uma densidade de VM por host de 111.

>[!IMPORTANT]
>As cargas de trabalho do cliente variam a partir deste exemplo de um trabalhador de conhecimento LoginVSI. Como parte do planejamento de sua implantação, trabalhe com o SEs do VMware EUC para suas necessidades específicas de dimensionamento e desempenho. Certifique-se de executar seus próprios testes de desempenho usando a carga de trabalho real planejada antes de finalizar o dimensionamento e ajuste do host de acordo.

## <a name="horizon-on-azure-vmware-solution-licensing"></a>Horizonte no licenciamento da solução VMware do Azure 

Há quatro componentes para os custos gerais da execução do horizonte na solução VMware do Azure. 

### <a name="azure-vmware-solution-capacity-cost"></a>Custo da capacidade da solução VMware do Azure

Para obter informações sobre os preços, consulte a página de [preços da solução VMware do Azure](https://azure.microsoft.com/pricing/details/azure-vmware/)

### <a name="horizon-licensing-cost"></a>Custo de licenciamento de horizonte

Há duas licenças disponíveis para uso com a solução Azure VMware, que pode ser um usuário simultâneo (CCU) ou um usuário nomeado (NU):

* Licença de assinatura de horizonte

* Licença de assinatura universal do horizonte

Se estiver implantando apenas o horizonte na solução VMware do Azure para o futuro próximo, use a licença de assinatura Horizonte, pois ele é um custo mais baixo.

Se implantado na solução do Azure VMware e no local, como com um caso de uso de recuperação de desastre, escolha a licença de assinatura universal do horizonte. Ele inclui uma licença do vSphere para implantação local, portanto, ele tem um custo mais alto.

Trabalhe com sua equipe de vendas do VMware EUC para determinar o custo de licenciamento de horizonte com base em suas necessidades.

### <a name="azure-instance-types"></a>Tipos de instância do Azure

Para entender os tamanhos de máquina virtual do Azure que serão necessários para a infraestrutura de horizonte, consulte as diretrizes da VMware que podem ser encontradas [aqui](https://techzone.vmware.com/resource/horizon-on-azure-vmware-solution-configuration#horizon-installation-on-azure-vmware-solution).

## <a name="references"></a>Referências
[Requisitos do sistema para o agente de horizonte para Linux](https://docs.vmware.com/en/VMware-Horizon/2012/linux-desktops-setup/GUID-E268BDBF-1D89-492B-8563-88936FD6607A.html)


## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o VMware horizonte na solução VMware do Azure, leia as [perguntas frequentes sobre o VMware horizonte](https://www.vmware.com/content/dam/digitalmarketing/vmware/en/pdf/products/horizon/vmw-horizon-on-microsoft-azure-vmware-solution-faq.pdf).

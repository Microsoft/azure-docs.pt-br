---
title: Compartilhar imagens de VM com Galerias Compartilhadas
description: Saiba como usar galerias de imagens compartilhadas para compartilhar imagens de VM do Linux em sua organização.
author: axayjo
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 10/14/2020
ms.author: akjosh
ms.reviewer: cynthn
ms.openlocfilehash: 32b4cf1555a2d0e074ae1551a5c0085f2758fa2b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102609134"
---
# <a name="shared-image-galleries-overview"></a>Visão geral das Galerias de Imagens Compartilhadas

A Galeria de Imagens Compartilhadas é um serviço que ajuda você a criar a estrutura e a organização em torno das suas imagens. A Galeria de Imagens Compartilhadas fornece:

- Replicação global de imagens.
- Agrupamento e controle de versão de imagens para facilitar o gerenciamento.
- Imagens altamente disponíveis com contas de ZRS (Armazenamento com Redundância de Zona) em regiões que dão suporte a Zonas de Disponibilidade. O ZRS oferece maior resiliência contra falhas em zonas.
- Suporte a armazenamento premium (Premium_LRS).
- Compartilhamento entre assinaturas e até mesmo entre locatários do AD (Active Directory) usando o Azure RBAC.
- Dimensionamento das suas implantações com réplicas de imagem em cada região.

Usando uma Galeria de Imagens Compartilhadas, é possível compartilhar suas imagens com diferentes usuários, entidades de serviço ou grupos do AD dentro de sua organização. As imagens compartilhadas podem ser replicadas para várias regiões para dimensionar suas implantações mais rápido.

Uma imagem é uma cópia de uma VM completa (incluindo quaisquer discos de dados anexados) ou apenas do disco do sistema operacional, dependendo de como ela é criada. Quando você cria uma VM a partir da imagem, uma cópia dos VHDs na imagem é usada para criar os discos da nova VM. A imagem permanece no armazenamento e pode ser usada repetidamente para criar VMs.

Se você tem um grande número de imagens que precisa manter e gostaria de disponibilizá-las em toda a empresa, é possível usar a Galeria de Imagens Compartilhadas como um repositório. 

O recurso Galeria de Imagens Compartilhadas tem vários tipos de recursos:

| Recurso | Descrição|
|----------|------------|
| **Origem da imagem** | Este é um recurso que pode ser usado sozinho ou para criar uma **versão da imagem** em uma galeria de imagens. Uma origem de imagem pode ser uma VM do Azure existente [generalizada ou especializada](#generalized-and-specialized-images), uma imagem gerenciada, um instantâneo, uma VHD ou uma versão de imagem em outra galeria de imagens. |
| **Galeria de imagens** | Como o Azure Marketplace, uma **galeria de imagens** é um repositório para gerenciar e compartilhar imagens, mas você controla quem tem acesso. |
| **Definição da imagem** | As definições de imagem são criadas dentro de uma galeria e transportam informações sobre a imagem e os requisitos para usá-la internamente. Isso inclui se a imagem é Windows ou Linux, notas sobre a versão e requisitos mínimos e máximos de memória. É uma definição de um tipo de imagem. |
| **Versão da imagem** | Uma **versão da imagem** é usada para criar uma VM ao usar uma galeria. Você pode ter diversas versões de uma imagem conforme necessário para seu ambiente. Como uma imagem gerenciada, quando você usa uma **versão da imagem** para criar uma VM, a versão da imagem é usada para criar novos discos para a VM. Versões de imagem podem ser usadas várias vezes. |

<br>

![Gráfico mostrando como você pode ter várias versões de uma imagem na sua galeria](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>Definições da imagem

As definições de imagem são um agrupamento lógico de versões de uma imagem. A definição de imagem retém informações sobre o motivo pelo qual a imagem foi criada, para qual sistema operacional e outras informações sobre como usar a imagem. Uma definição de imagem é como um plano de todos os detalhes sobre a criação de uma imagem específica. Você não implanta uma VM por meio de uma definição de imagem, mas por meio das versões de imagem criadas com base na definição.

Há três parâmetros para cada definição de imagem que são usados em combinação: **Editor**, **Oferta** e **SKU**. Eles são usados para localizar uma definição de imagem específica. Você pode ter versões de imagem que compartilham um ou dois, mas não todos os três valores.  Por exemplo, aqui estão três definições de imagem e seus valores:

|Definição de imagem|Publicador|Oferta|Sku|
|---|---|---|---|
|myImage1|Contoso|Finanças|Back-end|
|myImage2|Contoso|Finanças|Front-end|
|myImage3|Testando|Finanças|Front-end|

Todos os três têm conjuntos exclusivos de valores. O formato é semelhante a como você pode especificar atualmente o editor, a oferta e o SKU para [imagens do Azure Marketplace](./windows/cli-ps-findimage.md) no Azure PowerShell para obter a versão mais recente de uma imagem do Marketplace. Cada definição de imagem deve ter um conjunto exclusivo desses valores.

Os seguintes parâmetros determinam quais tipos de versões de imagem eles podem conter:

- Estado do sistema operacional – Você pode definir o estado do sistema operacional para [especializado ou generalizado](#generalized-and-specialized-images). Esse campo é obrigatório.
- Sistema operacional – Pode ser Windows ou Linux. Esse campo é obrigatório.
-   Geração do Hyper-V – especifique se a imagem foi criada com base em um VHD do Hyper-V de geração 1 ou [geração 2](generation-2.md). O padrão é a geração 1.


A seguir estão outros parâmetros que podem ser configurados na definição de imagem para que você possa acompanhar os seus recursos com mais facilidade:

- Descrição – Use a descrição para fornecer informações mais detalhadas sobre por que a definição da imagem existe. Por exemplo, você pode ter uma definição de imagem para o seu servidor front-end que tem o aplicativo pré-instalado.
- EULA – Pode ser usado para apontar para um contrato de licença de usuário final específico para a definição de imagem.
- Política de Privacidade e Notas sobre a versão – Armazene notas sobre a versão e declarações de privacidade no armazenamento do Azure e forneça um URI para acessá-las como parte da definição da imagem.
- Data de fim da vida útil – Anexe uma data de fim da vida útil à sua definição de imagem para poder usar a automação para excluir definições de imagem antigas.
- Tag – Você pode adicionar tags ao criar a definição de imagem. Para obter mais informações sobre tags, consulte [Usando tags para organizar os seus recursos](../azure-resource-manager/management/tag-resources.md)
- Recomendações de memória e de vCPU mínimas e máximas – Se sua imagem tiver recomendações de memória e de vCPU, você poderá anexar essas informações à definição de imagem.
- Tipos de disco não permitidos – Você pode fornecer informações sobre as necessidades de armazenamento da sua VM. Por exemplo, se a imagem não for adequada para discos HDD Standard, você os adicionará à lista de não permitidos.
- Informações do plano de compra para imagens do Marketplace – `-PurchasePlanPublisher`, `-PurchasePlanName` e `-PurchasePlanProduct`. Para obter mais informações sobre o plano de compra, confira [Localizar imagens no Azure Marketplace](./windows/cli-ps-findimage.md) e [Fornecer informações do plano de compra do Azure Marketplace ao criar imagens](marketplace-images.md).


## <a name="image-versions"></a>Versões da imagem

Uma **versão da imagem** é usada para criar uma VM. Você pode ter diversas versões de uma imagem conforme necessário para seu ambiente. Quando você usa uma **versão da imagem** para criar uma VM, a versão da imagem é usada para criar novos discos para a VM. Versões de imagem podem ser usadas várias vezes.

As propriedades de uma versão de imagem são:

- Número da versão. Isso é usado como o nome da versão da imagem. Ele está sempre no formato: MajorVersion.MinorVersion.Patch. Quando você especifica o uso do **mais recente** ao criar uma VM, a imagem mais recente é escolhida com base na MajorVersion mais alta, depois em MinorVersion e em Patch. 
- Fonte. A origem pode ser uma VM, um disco gerenciado, um instantâneo, uma imagem gerenciada ou outra versão da imagem. 
- Excluir a versão dos mais recentes. Você pode impedir que uma versão seja usada como a versão mais recente da imagem. 
- Data de fim da vida útil. Data após a qual as VMs não podem ser criadas usando esta imagem.


## <a name="generalized-and-specialized-images"></a>Imagens generalizadas e especializadas

Há dois estados do sistema operacional com suporte na Galeria de Imagens Compartilhadas. Normalmente, as imagens exigem que a VM usada para criar a imagem tenha sido generalizada antes de tirar a imagem. Generalizar é um processo que remove informações específicas do computador e do usuário da VM. Para o Windows, a ferramenta Sysprep é usada. Para o Linux, você pode usar [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` ou os parâmetros de `-deprovision+user`.

As VMs especializadas não passaram por um processo para remover informações e contas específicas do computador. Além disso, as VMs criadas de imagens especializadas não têm um `osProfile` associado a elas. Isso significa que imagens especializadas terão algumas limitações, além de alguns benefícios.

- As VMs e os conjuntos de dimensionamento criados de imagens especializadas podem ser colocados em funcionamento mais rapidamente. Como elas são criados de uma origem que já passou pela primeira inicialização, as VMs criadas dessas imagens são inicializadas mais rapidamente.
- As contas que poderiam ser usadas para fazer logon na VM também podem ser usadas em qualquer VM criada usando a imagem especializada que é criada usando essa VM.
- As VMs terão o **Nome do computador** da VM da qual a imagem foi obtida. Você deve alterar o nome do computador para evitar colisões.
- O `osProfile` é como algumas informações confidenciais são passadas para a VM, usando `secrets`. Isso pode causar problemas ao usar o KeyVault, o WinRM e outras funcionalidades que usam `secrets` no `osProfile`. Em alguns casos, você pode usar as MSIs (identidades de serviço gerenciadas) para contornar essas limitações.

## <a name="regional-support"></a>Suporte regional

Todas as regiões públicas podem ser regiões de destino, mas determinadas regiões exigem que os clientes passem por um processo de solicitação para obter acesso. Para solicitar que uma assinatura seja adicionada à lista de permissões para uma região como a Austrália Central ou a Austrália Central 2, envie [uma solicitação de acesso](/troubleshoot/azure/general/region-access-request-process)

## <a name="limits"></a>limites 

Há limites por assinatura para implantar recursos usando Galerias de Imagens Compartilhadas:
- 100 galerias de imagens compartilhadas por assinatura por região
- 1\.000 definições de imagem por assinatura por região
- 10.000 versões de imagem por assinatura por região
- 10 réplicas de versões de imagem por assinatura por região
- Qualquer disco anexado à imagem precisa ter um tamanho menor ou igual a 1 TB

Para obter mais informações, consulte [Verificar o uso de recursos em relação aos limites](../networking/check-usage-against-limits.md) para obter exemplos de como verificar o seu uso atual.
 
## <a name="scaling"></a>Scaling
A Galeria de Pesquisa de Imagem permite que você especifique o número de réplicas que você deseja que o Azure mantenha das imagens. Isso ajuda em cenários de implantação de várias VMs, já que as implantações de VM podem ser distribuídas para diferentes réplicas, reduzindo a chance de o processamento de criação de instância ser limitado devido à sobrecarga de uma única réplica.

Com a Galeria de Imagens Compartilhadas, agora você pode implantar até 1.000 instâncias de VM em um conjunto de dimensionamento de máquinas virtuais (acima de 600 com imagens gerenciadas). As réplicas de imagem oferecem melhor desempenho, confiabilidade e consistência da implantação.   Você pode definir uma contagem de réplicas diferente em cada região de destino, com base nas necessidades de dimensionamento da região. Como cada réplica é uma cópia em profundidade da sua imagem, isso ajuda a dimensionar as suas implantações linearmente com cada réplica extra. Embora entendamos que não há duas imagens ou regiões iguais, esta é a nossa diretriz geral sobre como usar réplicas em uma região:

- Para implantações de conjunto de escala de máquina não virtual-para cada 20 VMs que você cria simultaneamente, recomendamos que você mantenha uma réplica. Por exemplo, se você estiver criando 120 VMs simultaneamente usando a mesma imagem em uma região, sugerimos que você mantenha pelo menos seis réplicas da sua imagem. 
- Para implantações de conjunto de dimensionamento de máquinas virtuais-para cada implantação de conjunto de dimensionamento com até 600 instâncias, recomendamos que você mantenha pelo menos uma réplica. Por exemplo, se você estiver criando cinco conjuntos de dimensionamento simultaneamente, cada um com 600 instâncias de VM usando a mesma imagem em uma região, sugerimos que você mantenha pelo menos cinco réplicas da sua imagem. 

Sempre recomendamos que você sobreprovisione o número de réplicas devido a fatores como tamanho da imagem, conteúdo e tipo de sistema operacional.

![Gráfico mostrando como você pode dimensionar imagens](./media/shared-image-galleries/scaling.png)

## <a name="make-your-images-highly-available"></a>Torne as suas imagens altamente disponíveis

O [ZRS (Armazenamento com Redundância de Zona) do Azure](https://azure.microsoft.com/blog/azure-zone-redundant-storage-in-public-preview/) fornece resiliência com relação a uma falha de Zona de Disponibilidade na região. Com a disponibilidade geral da Galeria de Imagens Compartilhadas, você pode optar por armazenar as suas imagens em contas do ZRS em regiões com Zonas de Disponibilidade. 

Você também pode escolher o tipo de conta de cada uma das regiões de destino. O tipo de conta de armazenamento padrão é Standard_LRS, mas você pode escolher Standard_ZRS para regiões com Zonas de Disponibilidade. Verifique a disponibilidade regional do ZRS [aqui](../storage/common/storage-redundancy.md).

![Gráfico mostrando o ZRS](./media/shared-image-galleries/zrs.png)

## <a name="replication"></a>Replicação
A Galeria de Imagens Compartilhadas também permite replicar imagens para outras regiões do Azure automaticamente. Cada versão de imagem compartilhada pode ser replicada para diferentes regiões, dependendo do que faz sentido para sua organização. Um exemplo é sempre replicar a imagem mais recente em várias regiões, enquanto todas as versões mais antigas só estão disponíveis em uma região. Isso pode ajudar a economizar nos custos de armazenamento das versões de imagem compartilhada. 

As regiões para as quais uma versão de Imagem compartilhada é replicada podem ser atualizadas após o horário de criação. O tempo necessário para replicar em diferentes regiões depende da quantidade de dados copiados e do número de regiões para as quais a versão é replicada. Isso pode levar algumas horas em alguns casos. Enquanto a replicação está em andamento, você pode exibir o status da replicação por região. Depois que a replicação de imagem for concluída em uma região, você poderá implantar uma VM ou um conjunto de dimensionamento usando essa versão de imagem na região.

![Gráfico mostrando como você pode replicar imagens](./media/shared-image-galleries/replication.png)

## <a name="access"></a>Acesso

Como a Galeria de Imagens Compartilhadas, a Definição de Imagem e a Versão da Imagem são recursos, eles podem ser compartilhados usando os controles nativos internos do Azure RBAC. Usando o RBAC do Azure, você pode compartilhar esses recursos para outros usuários, entidades de serviço e grupos. Você pode até compartilhar o acesso para pessoas fora do locatário no qual elas foram criadas. Quando um usuário tem acesso à versão da Imagem Compartilhada, ele pode implantar uma VM ou um Conjunto de Dimensionamento de Máquinas Virtuais.  Aqui está a matriz de compartilhamento que ajuda a entender ao que o usuário obtém acesso:

| Compartilhado com o usuário     | Galeria de imagens compartilhadas | Definição de imagem | Versão da imagem |
|----------------------|----------------------|--------------|----------------------|
| Galeria de imagens compartilhadas | Sim                  | Sim          | Sim                  |
| Definição de imagem     | Não                   | Sim          | Sim                  |

É recomendável compartilhar no nível da Galeria para obter a melhor experiência. Não recomendamos o compartilhamento de versões de imagem individuais. Para obter mais informações sobre o RBAC do Azure, consulte [atribuir funções do Azure](../role-based-access-control/role-assignments-portal.md).

As imagens também podem ser compartilhadas, em escala, mesmo entre locatários usando um registro de aplicativo multilocatário. Para obter mais informações sobre como compartilhar imagens entre locatários, consulte "compartilhar imagens de VM de galeria em locatários do Azure" usando o [CLI do Azure](./linux/share-images-across-tenants.md) ou o [PowerShell](./windows/share-images-across-tenants.md).

## <a name="billing"></a>Cobrança
Não há custo adicional para usar o serviço de Galeria de Imagens Compartilhadas. Você será cobrado pelos seguintes recursos:
-   Custos de armazenamento para armazenar cada réplica. O custo de armazenamento é cobrado como um instantâneo e é baseado no tamanho ocupado da versão da imagem, no número de réplicas da versão da imagem e no número de regiões para as quais a versão é replicada. 
-   Encargos de saída de rede para replicação da primeira versão de imagem da região de origem para as regiões replicadas. As réplicas subsequentes são tratadas dentro da região, portanto, não há encargos adicionais. 

Por exemplo, digamos que você tenha uma imagem de um disco de sistema operacional de 127 GB, que ocupa apenas 10 GB de armazenamento e um disco de dados de 32 GB vazio. O tamanho ocupado de cada imagem seria de apenas 10 GB. A imagem é replicada para três regiões, e cada região tem duas réplicas. Haverá no total seis instantâneos, cada um usando 10 GB. Você será cobrado pelo custo de armazenamento de cada instantâneo com base no tamanho ocupado de 10 GB. Você pagará encargos de saída de rede para que a primeira réplica seja copiada para as duas regiões adicionais. Para obter mais informações sobre os preços de instantâneos em cada região, confira [Preços de discos gerenciados](https://azure.microsoft.com/pricing/details/managed-disks/). Para saber mais sobre saída de rede, confira [Preços de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).


## <a name="updating-resources"></a>Atualização de recursos

Depois de criados, você pode fazer algumas alterações nos recursos da galeria de imagens. Elas são limitadas a:
 
Galeria de imagens compartilhadas:
- Descrição

definição da imagem:
- vCPUs recomendadas
- Memória recomendada
- Descrição
- Data de fim da vida útil

Versão da imagem:
- Contagem de réplicas regionais
- Regiões de destino
- Excluir do mais recente
- Data de fim da vida útil

## <a name="sdk-support"></a>Suporte a SDK

Os seguintes SDKs dão suporte à criação de Galerias de Imagens Compartilhadas:

- [.NET](/dotnet/api/overview/azure/virtualmachines/management)
- [Java](/java/azure/)
- [Node.js](/javascript/api/@azure/arm-compute)
- [Python](/python/api/overview/azure/virtualmachines)
- [Go](/azure/go/)

## <a name="templates"></a>Modelos

Você pode criar um recurso de Galeria de Imagens Compartilhadas usando modelos. Há vários Modelos de Início Rápido do Azure disponíveis: 

- [Criar uma Galeria de Imagens Compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Criar uma Definição de Imagem em uma Galeria de Imagens Compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Criar uma Versão da Imagem em uma Galeria de Imagens Compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Criar uma VM por meio de uma Versão da Imagem](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>Perguntas frequentes 

* [Como posso listar todos os recursos da Galeria de Imagens Compartilhadas entre assinaturas?](#how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions) 
* [Posso mover minha imagem existente para a galeria de imagens compartilhadas?](#can-i-move-my-existing-image-to-the-shared-image-gallery)
* [Posso criar uma versão da imagem usando um disco especializado?](#can-i-create-an-image-version-from-a-specialized-disk)
* [Posso mover o recurso da Galeria de Imagens Compartilhadas para uma assinatura diferente após ele ter sido criado?](#can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created)
* [Posso replicar as minhas versões de imagem entre nuvens, como o Azure China 21Vianet, Azure Alemanha e Nuvem do Azure Government?](#can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud)
* [Posso replicar as minhas versões de imagem entre assinaturas?](#can-i-replicate-my-image-versions-across-subscriptions)
* [Posso compartilhar versões de imagens entre os locatários do Azure AD?](#can-i-share-image-versions-across-azure-ad-tenants)
* [Quanto tempo leva para replicar as versões de imagem entre todas as regiões de destino?](#how-long-does-it-take-to-replicate-image-versions-across-the-target-regions)
* [Qual é a diferença entre a região de origem e a região de destino?](#what-is-the-difference-between-source-region-and-target-region)
* [Como fazer para especificar a região de origem ao criar a versão da imagem?](#how-do-i-specify-the-source-region-while-creating-the-image-version)
* [Como fazer para especificar o número de réplicas de versão da imagem a serem criadas em cada região?](#how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region)
* [Posso criar a galeria de imagens compartilhadas em uma localização diferente daquele da definição de imagem e da versão da imagem?](#can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version)
* [Quais são os encargos para usar a Galeria de Imagens Compartilhadas?](#what-are-the-charges-for-using-the-shared-image-gallery)
* [Que versão de API devo usar para criar a Galeria de Imagens Compartilhadas, a Definição de Imagem e a Versão da Imagem?](#what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version)
* [Qual versão de API devo usar para criar uma VM Compartilhada ou um Conjunto de Dimensionamento de Máquinas Virtuais da Versão da Imagem?](#what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version)
* [Posso atualizar o meu Conjunto de Dimensionamento de Máquinas Virtuais criado usando a imagem gerenciada para usar imagens da Galeria de Imagens Compartilhadas?](#can-i-update-my-virtual-machine-scale-set-created-using-managed-image-to-use-shared-image-gallery-images)

### <a name="how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions"></a>Como posso listar todos os recursos da Galeria de Imagens Compartilhadas entre assinaturas?

Para listar todos os recursos da Galeria de Imagens Compartilhadas entre assinaturas às quais você tem acesso no portal do Azure, siga as etapas abaixo:

1. Abra o [Portal do Azure](https://portal.azure.com).
1. Role para baixo na página e selecione **Todos os recursos**.
1. Selecione todas as assinaturas sob as quais você gostaria de listar todos os recursos.
1. Procure recursos do tipo **Galeria de imagens compartilhadas**.
  
Para listar todos os recursos da Galeria de Imagens Compartilhada entre assinaturas para as quais você tem permissões, use o seguinte comando na CLI do Azure:

```azurecli
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
```

Para obter mais informações, consulte **Gerenciar recursos da galeria** usando a [CLI do Azure](update-image-resources-cli.md) ou o [PowerShell](update-image-resources-powershell.md).

### <a name="can-i-move-my-existing-image-to-the-shared-image-gallery"></a>Posso mover minha imagem existente para a galeria de imagens compartilhadas?
 
Sim. Há três cenários com base nos tipos de imagens que você pode ter.

 Cenário 1: se você tiver uma imagem gerenciada, poderá criar uma definição de imagem e a versão da imagem usando essa definição. Para obter mais informações, consulte **Migrar de uma imagem gerenciada para uma versão de imagem** usando a [CLI do Azure](image-version-managed-image-cli.md) ou o [PowerShell](image-version-managed-image-powershell.md).

 Cenário 2: Se você tiver uma imagem não gerenciada, poderá criar uma imagem gerenciada com base nela e, em seguida, criar uma definição de imagem e a versão da imagem com base nela. 

 Cenário 3: Se você tiver um VHD no seu sistema de arquivos local, precisará carregar o VHD para uma imagem gerenciada e, em seguida, criar uma definição da imagem e uma versão da imagem com base nela.

- Se o VHD for de uma VM do Windows, veja [Carregar um VHD](./windows/upload-generalized-managed.md).
- Se o VHD for para uma VM do Linux, veja [Carregar um VHD](./linux/upload-vhd.md#option-1-upload-a-vhd)

### <a name="can-i-create-an-image-version-from-a-specialized-disk"></a>Posso criar uma versão da imagem de um disco especializado?

Sim, você pode criar uma VM de uma imagem especializada usando a [CLI](vm-specialized-image-version-cli.md), o [PowerShell](vm-specialized-image-version-powershell.md) ou a API. 

### <a name="can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created"></a>Posso mover o recurso da Galeria de Imagens Compartilhadas para uma assinatura diferente após ele ter sido criado?

Não, não é possível mover o recurso da galeria de imagens compartilhadas para uma assinatura diferente. Você pode replicar as versões da imagem na galeria para outras regiões ou copiar uma imagem de outra galeria usando a [CLI do Azure](image-version-another-gallery-cli.md) ou o [PowerShell](image-version-another-gallery-powershell.md).

### <a name="can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud"></a>Posso replicar as minhas versões de imagem entre nuvens, como o Azure China 21Vianet, Azure Alemanha e Nuvem do Azure Government?

Não, você não pode replicar as versões de imagem entre nuvens.

### <a name="can-i-replicate-my-image-versions-across-subscriptions"></a>Posso replicar minhas versões de imagem entre assinaturas?

Não, você não pode replicar as versões de imagem entre regiões em uma assinatura e usá-las em outras assinaturas por meio de RBAC.

### <a name="can-i-share-image-versions-across-azure-ad-tenants"></a>Posso compartilhar versões de imagens entre os locatários do Azure AD? 

Sim, você pode usar o RBAC para compartilhar com indivíduos entre os locatários. Mas, para compartilhar em escala, consulte "Compartilhar imagens da galeria entre locatários do Azure" usando o [PowerShell](./windows/share-images-across-tenants.md) ou a [CLI](./linux/share-images-across-tenants.md).

### <a name="how-long-does-it-take-to-replicate-image-versions-across-the-target-regions"></a>Quanto tempo leva para replicar as versões de imagem entre todas as regiões de destino?

O tempo de replicação de versão de imagem depende inteiramente do tamanho da imagem e do número de regiões para as quais ela está sendo replicada. No entanto, como uma melhor prática, é recomendável manter a imagem pequena e as regiões de origem e destino perto para melhores resultados. Você pode verificar o status da replicação usando o sinalizador -ReplicationStatus.

### <a name="what-is-the-difference-between-source-region-and-target-region"></a>Qual é a diferença entre região de origem e região de destino?

Região de origem é a região em que sua versão da imagem será criada e regiões de destino são as regiões em que uma cópia da sua versão da imagem será armazenada. Para cada versão de imagem, você pode ter apenas uma região de origem. Além disso, passe o local da região de origem como uma das regiões de destino ao criar uma versão da imagem.

### <a name="how-do-i-specify-the-source-region-while-creating-the-image-version"></a>Como faço para especificar a região de origem ao criar a versão da imagem?

Durante a criação de uma versão de imagem, você pode usar a marca **--location** na CLI e a marca **-Location** no PowerShell para especificar a região de origem. Verifique se a imagem gerenciada que você está usando como imagem base para criar a versão da imagem está no local em que você pretende criar a versão da imagem. Além disso, passe o local da região de origem como uma das regiões de destino ao criar uma versão da imagem.  

### <a name="how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region"></a>Como faço para especificar o número de réplicas de versão da imagem a serem criadas em cada região?

Há duas maneiras de especificar o número de réplicas de versão da imagem a serem criadas em cada região:
 
1. A contagem de réplica regionais que especifica o número de réplicas que você deseja criar por região. 
2. A contagem de réplicas comuns, que é a contagem padrão por região caso a contagem de réplicas regionais não seja especificada. 

Para especificar a contagem de réplicas regionais, passe a localização junto com o número de réplicas que você deseja criar nessa região: "Centro-Sul dos EUA=2". 

Se a contagem de réplicas regionais não for especificada com cada local, o número de réplicas padrão será a contagem de réplicas comuns que você especificou. 

Para especificar a contagem de réplicas comuns na CLI, use o argumento **--replica-count** no comando `az sig image-version create`.

### <a name="can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version"></a>Posso criar a galeria de imagens compartilhadas em uma localização diferente daquele da definição de imagem e da versão da imagem?

Sim, isso é possível. Porém, como uma melhor prática, incentivamos que você mantenha o grupo de recursos, a galeria de imagens compartilhadas, a definição da imagem e a versão da imagem na mesma localização.

### <a name="what-are-the-charges-for-using-the-shared-image-gallery"></a>Quais são os encargos para usar a Galeria de Imagens Compartilhadas?

Não há encargos para usar o serviço Galeria de Imagens Compartilhadas, exceto pelos encargos de armazenamento para armazenar as versões de imagem e os encargos de saída de rede para replicar as versões de imagem da região de origem para as regiões de destino.

### <a name="what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version"></a>Que versão de API devo usar para criar a Galeria de Imagens Compartilhadas, a Definição de Imagem e a Versão da Imagem?

Para trabalhar com galerias de imagens compartilhadas, definições de imagem e versões de imagem, é recomendável usar a API versão 2018-06-01. O ZRS (Armazenamento com Redundância de Zona) requer a versão de 01/03/2019 ou mais recente.

### <a name="what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version"></a>Qual versão de API devo usar para criar uma VM Compartilhada ou um Conjunto de Dimensionamento de Máquinas Virtuais da Versão da Imagem?

Para implantações de conjunto de dimensionamento de máquina virtual e VM usando uma versão de imagem, recomendamos usar a versão da API 2018-04-01 ou superior.

### <a name="can-i-update-my-virtual-machine-scale-set-created-using-managed-image-to-use-shared-image-gallery-images"></a>Posso atualizar o meu Conjunto de Dimensionamento de Máquinas Virtuais criado usando a imagem gerenciada para usar imagens da Galeria de Imagens Compartilhadas?

Sim, você pode atualizar a referência da imagem do conjunto de dimensionamento de uma imagem gerenciada para uma imagem da galeria de imagens compartilhadas, desde que o tipo de sistema operacional, a geração do Hyper-V e o layout do disco de dados sejam correspondentes entre as imagens.

## <a name="troubleshoot-shared-image-gallery-issues"></a>Solucionar problemas da Galeria de imagens compartilhadas
Se você tiver problemas com a execução de qualquer operação nos recursos da Galeria de imagens compartilhadas, consulte a lista de erros comuns no [Guia de solução de problemas](troubleshooting-shared-images.md).

Além disso, você pode postar e marcar sua pergunta com `azure-virtual-machines-images` o [Q&A](/answers/topics/azure-virtual-machines-images.html).

## <a name="next-steps"></a>Próximas etapas

Saiba como implantar imagens compartilhadas usando o [CLI do Azure](shared-images-cli.md) ou o [PowerShell](shared-images-powershell.md).

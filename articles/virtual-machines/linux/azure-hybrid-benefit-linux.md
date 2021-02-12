---
title: VMs Benefício Híbrido do Azure e Linux
description: Saiba como Benefício Híbrido do Azure pode ajudá-lo a economizar dinheiro em suas máquinas virtuais Linux em execução no Azure.
services: virtual-machines
documentationcenter: ''
author: mathapli
manager: westonh
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: mathapli
ms.openlocfilehash: 44d78d9a47f86520a3a4778806c4ddc0f96eec94
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737890"
---
# <a name="how-azure-hybrid-benefit-applies-for-linux-virtual-machines"></a>Como Benefício Híbrido do Azure se aplica a máquinas virtuais Linux

Benefício Híbrido do Azure é um benefício de licenciamento que ajuda a reduzir significativamente os custos de execução de suas VMs (máquinas virtuais) Red Hat Enterprise Linux (RHEL) e SUSE Linux Enterprise Server (SLES) na nuvem. Com esse benefício, você paga apenas pelos custos de infraestrutura da sua VM, pois sua assinatura RHEL ou SLES cobre a taxa de software. O benefício está disponível para todas as imagens RHEL e SLES Marketplace PAYG (pré-pago).

O Benefício Híbrido do Azure para VMs do Linux agora está disponível publicamente.

## <a name="benefit-description"></a>Descrição do benefício

Por meio do Benefício Híbrido do Azure, você pode migrar seus servidores RHEL e SLES locais para o Azure convertendo VMs RHEL e SLES PAYG existentes no Azure para cobrança BYOS (traga sua própria assinatura). Normalmente, as VMs implantadas de imagens PAYG no Azure cobrarão uma taxa de infraestrutura e uma taxa de software. Com Benefício Híbrido do Azure, as VMs PAYG podem ser convertidas em um modelo de cobrança BYOS sem uma reimplantação, para que você possa evitar qualquer risco de tempo de inatividade.

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="Benefício Híbrido do Azure visualização de custos em VMs do Linux.":::

Depois de habilitar o benefício na VM RHEL ou SLES, você não será mais cobrado pela taxa de software adicional normalmente incorrida em uma VM PAYG. Em vez disso, sua VM começará a acumular um encargo de BYOS, que inclui apenas a taxa de hardware de computação e nenhuma taxa de software.

Você também pode optar por converter uma VM que teve o benefício habilitado nele de volta para um modelo de cobrança PAYG.

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>Escopo da elegibilidade Benefício Híbrido do Azure para VMs Linux

Benefício Híbrido do Azure está disponível para todas as imagens RHEL e SLES PAYG do Azure Marketplace. O benefício ainda não está disponível para imagens RHEL ou SLES BYOS ou para imagens personalizadas do Azure Marketplace.

As instâncias reservadas, as instâncias de host dedicadas do Azure e os benefícios híbridos do SQL não são elegíveis para Benefício Híbrido do Azure se você já estiver usando o benefício com VMs do Linux.

## <a name="get-started"></a>Introdução

### <a name="red-hat-customers"></a>Clientes do Red Hat

Benefício Híbrido do Azure para RHEL está disponível para clientes Red Hat que atendem a esses dois critérios:

- Ter assinaturas RHEL ativas ou não usadas qualificadas para uso no Azure
- Habilitou uma ou mais dessas assinaturas para uso no Azure com o programa de [acesso à nuvem do Red Hat](https://www.redhat.com/en/technologies/cloud-computing/cloud-access)

> [!IMPORTANT]
> Verifique se a assinatura correta foi habilitada no programa de [acesso à nuvem](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) .

Para começar a usar o benefício do Red Hat:

1. Habilite uma ou mais de suas assinaturas RHEL qualificadas para uso no Azure usando a [interface do cliente do Red Hat Cloud Access](https://access.redhat.com/management/cloud).

   As assinaturas do Azure que você fornecer durante o processo de habilitação de acesso à nuvem do Red Hat terão permissão para usar o recurso Benefício Híbrido do Azure.
1. Aplique Benefício Híbrido do Azure a qualquer uma das suas VMs PAYG do RHEL existentes e a novas VMs RHEL que você implantar de imagens do Azure Marketplace PAYG. Você pode usar portal do Azure ou CLI do Azure para habilitar o benefício.
1. Siga [as próximas etapas](https://access.redhat.com/articles/5419341) recomendadas para configurar fontes de atualização para suas VMs RHEL e para obter diretrizes de conformidade de assinatura do RHEL.


### <a name="suse-customers"></a>Clientes do SUSE

Para começar a usar o benefício do SUSE:

1. Registre-se no programa de nuvem pública SUSE.
1. Aplique o benefício às suas VMs recém-criadas ou existentes por meio do portal do Azure ou CLI do Azure.
1. Registre suas VMs que estão recebendo o benefício com uma fonte separada de atualizações.

## <a name="enable-and-disable-the-benefit-in-the-azure-portal"></a>Habilitar e desabilitar o benefício no portal do Azure

Você pode habilitar o benefício em VMs existentes visitando a opção de **configuração** à esquerda e seguindo as etapas ali. Você pode habilitar o benefício em novas VMs durante a experiência de criação da VM.

### <a name="azure-portal-example-to-enable-the-benefit-for-an-existing-vm"></a>Portal do Azure exemplo para habilitar o benefício para uma VM existente:
1. Visite [portal do Microsoft Azure](https://portal.azure.com/)
1. Vá para a página ' criar uma máquina virtual ' no Portal.
 ![AHB ao criar a VM](./media/azure-hybrid-benefit/create-vm-ahb.png)
1. Clique na caixa de seleção para habilitar a conversão de AHB e usar licenças de acesso à nuvem.
 ![Caixa de seleção AHB ao criar VM](./media/azure-hybrid-benefit/create-vm-ahb-checkbox.png)
1. Criar uma máquina virtual seguindo o próximo conjunto de instruções
1. Verifique a folha de **configuração** e você verá a opção habilitada. 
![Folha de configuração do AHB após a criação](./media/azure-hybrid-benefit/create-configuration-blade.png)

### <a name="azure-portal-example-to-enable-the-benefit-during-creation-of-vm"></a>Portal do Azure exemplo para habilitar o benefício durante a criação da VM:
1. Visite [portal do Microsoft Azure](https://portal.azure.com/)
1. Abra a página da máquina virtual na qual você deseja aplicar a conversão.
1. Vá para a opção de **configuração** à esquerda. Você verá a seção licenciamento. Para habilitar a conversão de AHB, marque o botão de opção ' Sim ' e marque a caixa de seleção de confirmação.
![Folha de configuração do AHB após a criação](./media/azure-hybrid-benefit/create-configuration-blade.png)


>[!NOTE]
> Se você tiver criado um **instantâneo personalizado** ou uma **imagem compartilhada (SIG)** de uma imagem RHEL ou SLES PAYG Marketplace, você só poderá usar CLI do Azure para habilitar benefício híbrido do Azure. Essa é uma limitação conhecida e, no momento, não há nenhum cronograma para fornecer esse recurso no portal do Azure também.



## <a name="enable-and-disable-the-benefit-in-the-azure-cli"></a>Habilitar e desabilitar o benefício no CLI do Azure

Você pode usar o `az vm update` comando para atualizar as VMs existentes. Para VMs RHEL, execute o comando com um `--license-type` parâmetro de `RHEL_BYOS` . Para VMs SLES, execute o comando com um `--license-type` parâmetro de `SLES_BYOS` .

### <a name="cli-example-to-enable-the-benefit"></a>Exemplo da CLI para habilitar o benefício
```azurecli
# This will enable the benefit on a RHEL VM
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VM
az vm update -g myResourceGroup -n myVmName --license-type SLES_BYOS
```
### <a name="cli-example-to-disable-the-benefit"></a>Exemplo da CLI para desabilitar o benefício
Para desabilitar o benefício, use um `--license-type` valor de `None` :

```azurecli
# This will disable the benefit on a VM
az vm update -g myResourceGroup -n myVmName --license-type None
```

### <a name="cli-example-to-enable-the-benefit-on-a-large-number-of-vms"></a>Exemplo de CLI para habilitar o benefício em um grande número de VMs
Para habilitar o benefício em um grande número de VMs, você pode usar o `--ids` parâmetro no CLI do Azure:

```azurecli
# This will enable the benefit on a RHEL VM. In this example, ids.txt is an
# existing text file that contains a delimited list of resource IDs corresponding
# to the VMs using the benefit
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS --ids $(cat ids.txt)
```

Os exemplos a seguir mostram dois métodos para obter uma lista de IDs de recurso: um no nível do grupo de recursos e outro no nível da assinatura.

```azurecli
# To get a list of all the resource IDs in a resource group:
$(az vm list -g MyResourceGroup --query "[].id" -o tsv)

# To get a list of all the resource IDs of VMs in a subscription:
az vm list -o json | jq '.[] | {VMName: .name, ResourceID: .id}'
```

## <a name="apply-the-azure-hybrid-benefit-at-vm-create-time"></a>Aplicar o Benefício Híbrido do Azure em tempo de criação da VM
Além de aplicar as Benefício Híbrido do Azure às VMs pagas conforme o uso existentes, você pode chamá-las no momento da criação da VM. Os benefícios disso são threefold:
- Você pode provisionar VMs PAYG e BYOS usando a mesma imagem e processo.
- Ele permite alterações futuras no modo de licenciamento, algo não disponível com uma imagem somente BYOS ou se você colocar sua própria VM.
- A VM será conectada à RHUI (infraestrutura de atualização do Red Hat) por padrão, para garantir que ela permaneça atualizada e segura. Você pode alterar o mecanismo atualizado após a implantação a qualquer momento.

## <a name="check-the-azure-hybrid-benefit-status-of-a-vm"></a>Verificar o status de Benefício Híbrido do Azure de uma VM
Você pode exibir o status de Benefício Híbrido do Azure de uma VM usando o CLI do Azure ou usando o serviço de metadados de instância do Azure.

### <a name="azure-cli"></a>CLI do Azure

Você pode usar o `az vm get-instance-view` comando para essa finalidade. Procure um `licenseType` campo na resposta. Se o `licenseType` campo existir e o valor for `RHEL_BYOS` ou `SLES_BYOS` , sua VM terá o benefício habilitado.

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Serviço de metadados de instância do Azure

De dentro da própria VM, você pode consultar os metadados atestados no serviço de metadados de instância do Azure para determinar o valor da VM `licenseType` . Um `licenseType` valor `RHEL_BYOS` ou `SLES_BYOS` indicará que sua VM tem o benefício habilitado. [Saiba mais sobre os metadados atestados](./instance-metadata-service.md#attested-data).

## <a name="compliance"></a>Conformidade

### <a name="red-hat"></a>Red Hat

Os clientes que usam o Benefício Híbrido do Azure para RHEL concordam com os [termos legais](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Cloud_Software_Subscription_Agreement_for_Microsoft_Azure.pdf) padrão e a [política de privacidade](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Privacy_Statement_for_Microsoft_Azure.pdf) associada às ofertas do Azure Marketplace RHEL.

Os clientes que usam o Benefício Híbrido do Azure para RHEL têm três opções para fornecer atualizações de software e patches para essas VMs:

- [Infraestrutura de atualização do Red Hat](../workloads/redhat/redhat-rhui.md) (opção padrão)
- Servidor de satélite Red Hat
- Gerenciador de assinaturas do Red Hat

Os clientes que escolhem a opção RHUI podem continuar a usar o RHUI como a principal origem de atualização para suas VMs Benefício Híbrido do Azure RHEL sem anexar assinaturas de RHEL a essas VMs. Os clientes que escolhem a opção RHUI são responsáveis por garantir a conformidade da assinatura do RHEL.

Os clientes que escolhem o servidor de satélite Red Hat ou o Gerenciador de assinaturas do Red Hat devem remover a configuração RHUI e anexar uma assinatura RHEL habilitada para acesso à nuvem às suas VMs Benefício Híbrido do Azure RHEL.  

Para obter mais informações sobre conformidade de assinatura do Red Hat, atualizações de software e fontes para Benefício Híbrido do Azure VMs RHEL, consulte o [artigo sobre o Red Hat sobre como usar assinaturas do RHEL com benefício híbrido do Azure](https://access.redhat.com/articles/5419341).

### <a name="suse"></a>SUSE

Para usar Benefício Híbrido do Azure para suas VMs SLES e obter informações sobre como migrar do SLES PAYG para o BYOS ou migrar do SLES BYOS para o PAYG, consulte [SuSE Linux Enterprise e benefício híbrido do Azure](https://www.suse.com/c/suse-linux-enterprise-and-azure-hybrid-benefit/). 

## <a name="frequently-asked-questions"></a>Perguntas frequentes
*P: posso usar um tipo de licença `RHEL_BYOS` com uma imagem SLES ou vice-versa?*

R: não, você não pode. A tentativa de inserir um tipo de licença que corresponda incorretamente à distribuição em execução em sua VM não atualizará nenhum metadado de cobrança. Mas se você inserir acidentalmente o tipo de licença errado, atualizar sua VM novamente para o tipo de licença correto ainda habilitará o benefício.

*P: Eu me registrei com o acesso à nuvem Red Hat, mas ainda não posso habilitar o benefício em minhas VMs RHEL. O que devo fazer?*

R: pode levar algum tempo para que o registro da assinatura do Red Hat Cloud Access se propague do Red Hat para o Azure. Se você ainda vir o erro após um dia útil, entre em contato com o suporte da Microsoft.

*P: implantei uma VM usando o RHEL BYOS "imagem dourada". Posso converter a cobrança nessas imagens de BYOS para PAYG?*

R: não, você não pode. Benefício Híbrido do Azure dá suporte à conversão somente em imagens pagas conforme o uso.

*P: Eu carreguei minha própria imagem RHEL do local (por meio de migrações para Azure, Azure Site Recovery ou de outra forma) para o Azure. Posso converter a cobrança nessas imagens de BYOS para PAYG?*

R: não, você não pode. No momento, o recurso Benefício Híbrido do Azure está disponível apenas para imagens RHEL e SLES no Azure Marketplace. 

*P: Eu carreguei minha própria imagem RHEL do local (por meio de migrações para Azure, Azure Site Recovery ou de outra forma) para o Azure. Preciso fazer algo para se beneficiar do Benefício Híbrido do Azure?*

R: não, você não tem. As imagens RHEL que você carrega já são consideradas BYOS, e você é cobrado apenas pelos custos de infraestrutura do Azure. Você é responsável pelos custos de assinatura do RHEL, assim como você é para seus ambientes locais. 

*P: posso usar Benefício Híbrido do Azure em VMs implantadas de imagens do Azure Marketplace RHEL e do SLES SAP?*

R: Sim, você pode. Você pode usar o tipo de licença do `RHEL_BYOS` para VMs RHEL e `SLES_BYOS` para conversões de VMs implantadas de imagens do Azure Marketplace RHEL e SLES SAP.

*P: posso usar Benefício Híbrido do Azure em conjuntos de dimensionamento de máquinas virtuais para RHEL e SLES?*

R: não, você não pode. Atualmente, os conjuntos de dimensionamento de máquinas virtuais estão no escopo de Benefício Híbrido do Azure para RHEL e SLES.

*P: posso usar Benefício Híbrido do Azure em instâncias reservadas para RHEL e SLES?*

R: não, você não pode. Atualmente, as instâncias reservadas não estão no escopo de Benefício Híbrido do Azure para RHEL e SLES.

*P: posso usar Benefício Híbrido do Azure em uma máquina virtual implantada para SQL Server em imagens RHEL?*

R: não, você não pode. Não há nenhum plano para dar suporte a essas máquinas virtuais.

*P: posso usar Benefício Híbrido do Azure na minha assinatura do RHEL virtual Data Center?*

R: não, você não pode. O VDC não tem suporte no Azure, incluindo AHB.  
 

## <a name="common-problems"></a>Problemas comuns
Esta seção lista os problemas comuns que você pode encontrar e as etapas de mitigação.

| Erro | Atenuação |
| ----- | ---------- |
| "A ação não pôde ser concluída porque nossos registros mostram que você não habilitou com êxito o acesso à nuvem Red Hat em sua assinatura do Azure...." | Para usar o benefício com VMs RHEL, você deve primeiro [registrar suas assinaturas do Azure com o Red Hat Cloud Access](https://access.redhat.com/management/cloud).

## <a name="next-steps"></a>Próximas etapas
* [Saiba como criar e atualizar VMs e adicionar tipos de licença (RHEL_BYOS, SLES_BYOS) para Benefício Híbrido do Azure usando o CLI do Azure](/cli/azure/vm)

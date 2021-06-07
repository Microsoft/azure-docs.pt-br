---
title: Visão geral dos hosts dedicados do Azure para máquinas virtuais
description: Saiba mais sobre como os hosts dedicados do Azure podem ser usados para implantar máquinas virtuais.
author: cynthn
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 12/07/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 46de182c7acfaf75b2e65fa318717348dd1c4b73
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101667342"
---
# <a name="azure-dedicated-hosts"></a>Hosts dedicados do Azure

O Host Dedicado do Azure é um serviço que fornece servidores físicos - que podem hospedar uma ou mais máquinas virtuais - dedicados a uma assinatura do Azure. Os hosts dedicados são os mesmos servidores físicos usados em nossos data centers, fornecidos como um recurso. Você pode provisionar hosts dedicados em uma região, uma zona de disponibilidade e em um domínio de falha. Em seguida, você pode posicionar VMs diretamente em seus hosts provisionados, em qualquer configuração que melhor atenda às suas necessidades.


## <a name="benefits"></a>Benefícios 

Reservar o host inteiro oferece os seguintes benefícios:

-   Isolamento de hardware no nível do servidor físico. Nenhuma outra VM será colocada nos hosts. Os hosts dedicados são implantados nos mesmos data centers e compartilham a mesma rede e a infraestrutura de armazenamento subjacente que outros hosts não isolados.
-   Controle sobre os eventos de manutenção iniciados pela plataforma Azure. Embora a maioria dos eventos de manutenção tenha pouco ou nenhum impacto em suas máquinas virtuais, há algumas cargas de trabalho confidenciais em que cada segundo de pausa pode ter um impacto. Com hosts dedicados, você pode aceitar uma janela de manutenção para reduzir o impacto em seu serviço.
-   Com o benefício híbrido do Azure, você pode colocar suas próprias licenças para Windows e SQL no Azure. O uso dos benefícios híbridos oferece benefícios adicionais. Para obter mais informações, consulte [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).


## <a name="groups-hosts-and-vms"></a>Grupos, hosts e VMs  

![Exibição dos novos recursos para hosts dedicados.](./media/virtual-machines-common-dedicated-hosts/dedicated-hosts2.png)

Um **grupo de hosts** é um recurso que representa uma coleção de hosts dedicados. Você cria um grupo de hosts em uma região e uma zona de disponibilidade e adiciona hosts a ele.

Um **host** é um recurso, mapeado para um servidor físico em um data center do Azure. O servidor físico é alocado quando o host é criado. Um host é criado dentro de um grupo de hosts. Um host tem uma SKU que descreve quais tamanhos de VM podem ser criados. Cada host pode hospedar várias VMs, de tamanhos diferentes, contanto que sejam da mesma série de tamanho.


## <a name="high-availability-considerations"></a>Considerações sobre alta disponibilidade 

Para alta disponibilidade, você deve implantar várias VMs, distribuídas entre vários hosts (mínimo de 2). Com os hosts dedicados do Azure, você tem várias opções para provisionar sua infraestrutura para formatar seus limites de isolamento de falha.

### <a name="use-availability-zones-for-fault-isolation"></a>Usar Zonas de Disponibilidade para o isolamento de falhas

As zonas de disponibilidade são locais físicos exclusivos em uma região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, resfriamento e rede independentes. Um grupo de hosts é criado em uma única zona de disponibilidade. Depois de criada, todos os hosts serão colocados dentro dessa zona. Para obter alta disponibilidade entre zonas, você precisa criar vários grupos de hosts (um por zona) e distribuir os hosts adequadamente.

Se você atribuir um grupo de hosts a uma zona de disponibilidade, todas as VMs criadas nesse host deverão ser criadas na mesma zona.

### <a name="use-fault-domains-for-fault-isolation"></a>Usar domínios de falha para o isolamento de falhas

Um host pode ser criado em um domínio de falha específico. Assim como a VM em um conjunto de dimensionamento ou conjunto de disponibilidade, os hosts em diferentes domínios de falha serão colocados em diferentes racks físicos na data center. Ao criar um grupo de hosts, é necessário especificar a contagem de domínios de falha. Ao criar hosts dentro do grupo de hosts, você atribui um domínio de falha para cada host. As VMs não exigem nenhuma atribuição de domínio de falha.

Os domínios de falha não são iguais a colocalização. Ter o mesmo domínio de falha para dois hosts não significa que eles estão em proximidade entre si.

Os domínios de falha são incluídos no escopo do grupo de hosts. Você não deve fazer nenhuma suposição sobre a antiafinidade entre dois grupos de hosts (a menos que eles estejam em zonas de disponibilidade diferentes).

As VMs implantadas em hosts com domínios de falha diferentes terão seus serviços de discos gerenciados subjacentes em vários carimbos de armazenamento, para aumentar a proteção de isolamento de falhas.

### <a name="using-availability-zones-and-fault-domains"></a>Usando Zonas de Disponibilidade e domínios de falha

Você pode usar ambos os recursos juntos para obter ainda mais isolamento de falha. Nesse caso, você especificará a zona de disponibilidade e a contagem de domínios de falha em para cada grupo de hosts, atribuirá um domínio de falha a cada um dos hosts no grupo e atribuirá uma zona de disponibilidade a cada uma de suas VMs

O modelo de exemplo do Resource Manager encontrado [aqui](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md) usa zonas e domínios de falha para distribuir hosts para obter máxima resiliência em uma região.


## <a name="manual-vs-automatic-placement"></a>Posicionamento manual versus automático 

Ao criar uma VM no Azure, você pode selecionar qual host dedicado usar. Você também pode usar a opção para posicionar automaticamente suas VMs em hosts existentes em um grupo de hosts. 

Ao criar um novo grupo de hosts, verifique se a configuração de posicionamento automático de VM está selecionada. Ao criar sua VM, selecione o grupo de hosts e permita que o Azure escolha o melhor host para sua VM. 

Os grupos de hosts que estão habilitados para posicionamento automático não exigem que todas as VMs sejam colocadas automaticamente. Você ainda poderá escolher explicitamente um host, mesmo quando o posicionamento automático estiver selecionado para o grupo de hosts. 

### <a name="limitations"></a>Limitações

Problemas conhecidos e limitações ao usar o posicionamento automático da VM:

- Você não poderá aplicar os benefícios híbridos do Azure em seus hosts dedicados.
- Você não poderá reimplantar a VM. 
- Não será possível usar VMs Lsv2, NVasv4, NVsv3, Msv2 ou série M com hosts dedicados 


## <a name="virtual-machine-scale-set-support"></a>Suporte ao conjunto de dimensionamento de máquinas virtuais

Os conjuntos de dimensionamento de máquinas virtuais permitem que você trate um grupo de máquinas virtuais como um único recurso e aplique políticas de disponibilidade, gerenciamento, dimensionamento e orquestração como um grupo. Seus hosts dedicados existentes também podem ser usados para conjuntos de dimensionamento de máquinas virtuais. 

Ao criar um conjunto de dimensionamento de máquinas virtuais, você pode especificar um grupo de hosts existente para ter todas as instâncias de VM criadas em hosts dedicados.

Os seguintes requisitos se aplicam ao criar um conjunto de dimensionamento de máquinas virtuais em um grupo de hosts dedicado:

- O posicionamento automático da VM precisa ser habilitado.
- A configuração de disponibilidade do grupo de hosts deve corresponder ao conjunto de dimensionamento. 
    - Um grupo de hosts regionais (criado sem especificar uma zona de disponibilidade) deve ser usado para conjuntos de dimensionamento regionais.
    - O grupo de hosts e o conjunto de dimensionamento devem estar usando a mesma zona de disponibilidade. 
    - A contagem de domínios de falha para o nível do grupo de hosts deve corresponder à contagem de domínios de falha para seu conjunto de dimensionamento. O portal do Azure permite que você especifique a *distribuição máxima* para o conjunto de dimensionamento, que define a contagem de domínios de falha de 1.
- Hosts dedicados devem ser criados primeiro, com capacidade suficiente e as mesmas configurações para zonas de conjunto de dimensionamento e domínios de falha.
- Os tamanhos de VM com suporte para seus hosts dedicados devem corresponder ao usado para seu conjunto de dimensionamento.

Nem todas as configurações de orquestração e otimizações de conjunto de escala são suportadas por hosts dedicados. Aplique as seguintes configurações ao seu conjunto de dimensionamento: 
- O provisionamento em excesso não é recomendado e está desabilitado por padrão. Você pode habilitar o provisionamento em excesso, mas a alocação do conjunto de dimensionamento falhará se o grupo de hosts não tiver capacidade para todas as VMs, incluindo as instâncias de provisionamento excessivo. 
- Usar o modo de orquestração ScaleSetVM 
- Não usar grupos de posicionamento de proximidade para colocalização



## <a name="maintenance-control"></a>Controle de manutenção

Às vezes, a infraestrutura de suporte às suas máquinas virtuais pode ser atualizada para melhorar a confiabilidade, o desempenho, a segurança e a inicialização de novos recursos. A plataforma Azure tenta minimizar o impacto da manutenção da plataforma sempre que possível, mas os clientes com cargas de trabalho *sensíveis à manutenção* não podem tolerar até poucos segundos que a VM precisa ser congelada ou desconectada para manutenção.

O **controle de manutenção** fornece aos clientes uma opção para ignorar atualizações de plataforma regulares agendadas em seus hosts dedicados e, em seguida, aplicá-las no momento de sua escolha dentro de uma janela sem interrupção de 35 dias. Na janela de manutenção, você pode aplicar a manutenção diretamente no nível do host, em qualquer ordem. Depois que a janela de manutenção terminar, a Microsoft avançará e aplicará a manutenção pendente aos hosts em uma ordem que pode não seguir os domínios de falha definidos pelo usuário.

Para obter mais informações, consulte [gerenciando atualizações de plataforma com o controle de manutenção](./maintenance-control.md).

## <a name="capacity-considerations"></a>Considerações sobre a capacidade

Depois que um host dedicado é provisionado, o Azure o atribui ao servidor físico. Isso garante a disponibilidade da capacidade quando você precisa provisionar sua VM. O Azure usa toda a capacidade na região (ou zona) para escolher um servidor físico para o host. Isso também significa que os clientes podem esperar poder aumentar seu espaço de host dedicado sem a preocupação de ficar sem espaço no cluster.

## <a name="quotas"></a>Cotas

Há dois tipos de cota que são consumidos quando você implanta um host dedicado.

1. Cota de vCPU de host dedicada. O limite de cota padrão é 3000 vCPUs, por região.
1. Tamanho da VM na cota da família. Por exemplo, uma assinatura **paga conforme o uso** pode ter apenas uma cota de 10 vCPUs disponíveis para a série de tamanho de Dsv3, na região leste dos EUA. Para implantar um host Dsv3 dedicado, você precisaria solicitar um aumento de cota para pelo menos 64 vCPUs antes de poder implantar o host dedicado. 

Para solicitar um aumento de cota, crie uma solicitação de suporte no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

O provisionamento de um host dedicado consumirá o host dedicado vCPU e a cota de vCPU da família de VMs, mas não consumirá o vCPU regional.


![Captura de tela da página uso e cotas no portal](./media/virtual-machines-common-dedicated-hosts/quotas.png)

Para obter mais informações, consulte [cotas de vCPU de máquina virtual](./windows/quotas.md).

A avaliação gratuita e as assinaturas do MSDN não têm cota para hosts dedicados do Azure.

## <a name="pricing"></a>Preços

Os usuários são cobrados por host dedicado, independentemente de quantas VMs são implantadas. Em sua declaração mensal, você verá um novo tipo de recurso Faturável de hosts. As VMs em um host dedicado ainda serão mostradas em sua instrução, mas terão um preço de 0.

O preço do host é definido com base na família de VMs, tipo (tamanho do hardware) e região. Um preço de host é relativo ao maior tamanho de VM com suporte no host.

O licenciamento de software, o armazenamento e o uso de rede são cobrados separadamente do host e das VMs. Não há nenhuma alteração nesses itens faturáveis.

Para obter mais informações, consulte [preços do host dedicado do Azure](https://aka.ms/ADHPricing).

Você também pode economizar em custos com uma [instância reservada de hosts dedicados do Azure](prepay-dedicated-hosts-reserved-instances.md).
 
## <a name="sizes-and-hardware-generations"></a>Tamanhos e gerações de hardware

Uma SKU é definida para um host e representa a série de tamanho da VM e o tipo. Você pode misturar várias VMs de tamanhos diferentes em um único host, desde que eles tenham a mesma série de tamanho. 

O *tipo* é a geração de hardware. Tipos de hardware diferentes para a mesma série de VMs serão de fornecedores de CPU diferentes e terão diferentes gerações de CPU e número de núcleos. 

Os tamanhos e tipos de hardware variam por região. Consulte a [página de preços](https://aka.ms/ADHPricing) do host para saber mais.

> [!NOTE]
> Quando um host dedicado for provisionado, você não poderá alterar o tamanho ou o tipo. Se você precisar de um tamanho diferente do tipo, será necessário criar um novo host.  

## <a name="host-life-cycle"></a>Ciclo de vida do host


O Azure monitora e gerencia o status de integridade de seus hosts. Os seguintes Estados serão retornados quando você consultar o host:

| Estado de Integridade   | Descrição       |
|----------|----------------|
| Host disponível     | Não há problemas conhecidos com o host.   |
| Host em investigação  | Estamos com alguns problemas com o host que estamos procurando. Esse é um estado de transição necessário para que o Azure tente e identifique o escopo e a causa raiz do problema identificado. As máquinas virtuais em execução no host podem ser afetadas. |
| Desalocação pendente do host   | O Azure não pode restaurar o host de volta para um estado íntegro e pedir que você reimplante suas máquinas virtuais fora desse host. Se `autoReplaceOnFailure` o estiver habilitado, suas máquinas virtuais  serão reparadas para o hardware íntegro. Caso contrário, sua máquina virtual pode estar em execução em um host que está prestes a falhar.|
| Host desalocado  | Todas as máquinas virtuais foram removidas do host. Você não está mais sendo cobrado por esse host, pois o hardware foi retirado da rotação.   |


## <a name="next-steps"></a>Próximas etapas

- Você pode implantar um host dedicado usando [Azure PowerShell](./windows/dedicated-hosts-powershell.md), o [portal](./dedicated-hosts-portal.md)e o [CLI do Azure](./linux/dedicated-hosts-cli.md).

- Há um exemplo de modelo, [aqui](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), que usa zonas e domínios de falha para obter resiliência máxima em uma região.

- Você também pode economizar em custos com uma [instância reservada de hosts dedicados do Azure](prepay-dedicated-hosts-reserved-instances.md).

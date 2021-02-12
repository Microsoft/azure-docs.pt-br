---
title: Gerenciar recursos e cotas
titleSuffix: Azure Machine Learning
description: Saiba mais sobre as cotas e os limites dos recursos para Azure Machine Learning e como solicitar aumentos de cota.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
author: SimranArora904
ms.author: siarora
ms.date: 12/1/2020
ms.topic: conceptual
ms.custom: troubleshooting,contperf-fy20q4, contperf-fy21q2
ms.openlocfilehash: aa25a81068b25b7b8696a7472eb04542e8bb1a60
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878945"
---
# <a name="manage-and-increase-quotas-for-resources-with-azure-machine-learning"></a>Gerenciar e aumentar cotas para recursos com Azure Machine Learning

O Azure usa limites e cotas para evitar estouros de orçamento devido a fraudes e para honrar as restrições de capacidade do Azure. Considere esses limites conforme você dimensiona para cargas de trabalho de produção. Neste artigo, você aprenderá sobre:

> [!div class="checklist"]
> + Limites padrão de recursos do Azure relacionados a [Azure Machine Learning](overview-what-is-azure-ml.md).
> + Criando cotas de nível de espaço de trabalho.
> + Exibindo suas cotas e limites.
> + A solicitação aumenta a cota.
> + O ponto de extremidade privado e as cotas de DNS.

Juntamente com o gerenciamento de cotas, você pode aprender a [planejar e gerenciar custos para Azure Machine Learning](concept-plan-manage-cost.md) ou saber mais sobre os [limites de serviço em Azure Machine Learning](resource-limits-quotas-capacity.md).

## <a name="special-considerations"></a>Considerações especiais

+ Uma cota é um limite de crédito, não uma garantia de capacidade. Se você tiver necessidades de capacidade em grande escala, [entre em contato com o suporte do Azure para aumentar sua cota](#request-quota-increases).

+ Uma cota é compartilhada entre todos os serviços em suas assinaturas, incluindo Azure Machine Learning. Calcule o uso em todos os serviços quando estiver avaliando a capacidade.
 
  Azure Machine Learning computação é uma exceção. Ele tem uma cota separada da cota de computação principal. 

+ Os limites padrão variam por tipo de categoria de oferta, como avaliação gratuita, pago conforme o uso e série de máquinas virtuais (VM) (como Dv2, F e G).

## <a name="default-resource-quotas"></a>Cotas de recurso padrão

Nesta seção, você aprenderá sobre os limites de cota padrão e máximo para os seguintes recursos:

+ Ativos de Azure Machine Learning
  + Computação do Azure Machine Learning
  + Pipelines de Azure Machine Learning
+ Máquinas virtuais
+ Instâncias de Contêiner do Azure
+ Armazenamento do Azure

> [!IMPORTANT]
> Os limites estão sujeitos a alterações. Para obter as informações mais recentes, consulte  [limites de serviço em Azure Machine Learning](resource-limits-quotas-capacity.md).



### <a name="azure-machine-learning-assets"></a>Ativos de Azure Machine Learning
Os seguintes limites de ativos se aplicam a cada espaço de trabalho. 

| **Recurso** | **Limite máximo** |
| --- | --- |
| Conjunto de dados | 10 milhões |
| Execuções | 10 milhões |
| Modelos | 10 milhões|
| Artifacts | 10 milhões |

Além disso, o **tempo de execução** máximo é de 30 dias e o número máximo de **métricas registradas por execução** é de 1 milhão.

### <a name="azure-machine-learning-compute"></a>Computação do Azure Machine Learning
[Azure Machine Learning computação](concept-compute-target.md#azure-machine-learning-compute-managed) tem um limite de cota padrão no número de núcleos (dividido por cada família de VM e núcleos de total cumulativo), bem como o número de recursos de computação exclusivos permitidos por região em uma assinatura. Essa cota é separada da cota de núcleos de VM listada na seção anterior, pois ela se aplica somente aos recursos de computação gerenciados do Azure Machine Learning.

[Solicite um aumento de cota](#request-quota-increases) para aumentar os limites de várias cotas principais da família de VMs, total de cotas e recursos principais da assinatura nesta seção.

Recursos disponíveis:
+ Os **núcleos dedicados por região** têm um limite padrão de 24 a 300, dependendo do tipo de oferta da sua assinatura. Você pode aumentar o número de núcleos dedicados por assinatura para cada família de VM. Famílias de VMs especializadas como NCv2, NCv3 ou ND Series começam com um padrão de zero núcleos.

+ Os **núcleos de baixa prioridade por região** têm um limite padrão de 100 a 3.000, dependendo do tipo de oferta da sua assinatura. O número de núcleos de baixa prioridade por assinatura pode ser aumentado e é um valor único nas famílias de VM.

+ **Clusters por região** têm um limite padrão de 200. Eles são compartilhados entre um cluster de treinamento e uma instância de computação. (Uma instância de computação é considerada um cluster de nó único para fins de cota.)

> [!TIP]
> Para saber mais sobre a qual família de VMs solicitar um aumento de cota, confira [tamanhos de máquina virtual no Azure](../virtual-machines/sizes.md). Por exemplo, as famílias de VMs de GPU começam com um "N" em seu nome de família (por exemplo, Série NCv3)

A tabela a seguir mostra limites adicionais na plataforma. Entre em contato com a equipe de produto do AzureML por meio de um tíquete de suporte **técnico** para solicitar uma exceção.

| **Recurso ou ação** | **Limite máximo** |
| --- | --- |
| Espaços de trabalho por grupo de recursos | 800 |
| Nós em uma configuração de **cluster** AmlCompute (computação de Azure Machine Learning única) como um pool habilitado para não comunicação (ou seja, não pode executar trabalhos MPI) | 100 nós, mas podem ser configurados até 65000 nós |
| Os nós em uma única etapa de execução paralela são **executados** em um cluster de AmlCompute (computação de Azure Machine Learning) | 100 nós, mas podem ser configurados até 65000 nós se o cluster estiver configurado para dimensionar por acima |
| Nós em uma configuração de **cluster** AmlCompute (computação de Azure Machine Learning única) como um pool habilitado para comunicação | 300 nós, mas podem ser configurados até 4000 nós |
| Nós em uma configuração de **cluster** AmlCompute (computação de Azure Machine Learning única) como um pool habilitado para comunicação em uma família de VMs habilitada para RDMA | 100 nós |
| Os nós em uma única MPI são **executados** em um cluster de Azure Machine Learning computação (AmlCompute) | 100 nós, mas podem ser aumentados para 300 nós |
| Processos MPI de GPU por nó | 1-4 |
| Trabalhos de GPU por nó | 1-4 |
| Tempo de vida do trabalho | 21 dias<sup>1</sup> |
| Tempo de vida do trabalho em um nó de baixa prioridade | 7 dias<sup>2</sup> |
| Servidores de parâmetro por nó | 1 |

<sup>1</sup> o tempo de vida máximo é a duração entre o momento em que uma execução é iniciada e quando ela é concluída. As execuções concluídas persistirão indefinidamente. Os dados para execuções não concluídas no tempo de vida máximo não estão acessíveis.
<sup>2</sup> os trabalhos em um nó de baixa prioridade podem ser admitidos sempre que houver uma restrição de capacidade. Recomendamos que você implemente pontos de verificação em seu trabalho.

#### <a name="azure-machine-learning-pipelines"></a>Pipelines de Azure Machine Learning
[Azure Machine Learning pipelines](concept-ml-pipelines.md) têm os limites a seguir.

| **Recurso** | **Limite** |
| --- | --- |
| Etapas em um pipeline | 30,000 |
| Espaços de trabalho por grupo de recursos | 800 |

### <a name="virtual-machines"></a>Máquinas virtuais
Cada assinatura do Azure tem um limite no número de máquinas virtuais em todos os serviços. Os núcleos de máquina virtual têm um limite regional total e um limite regional por série de tamanho. Ambos os limites são aplicados separadamente.

Por exemplo, considere uma assinatura com uma VM do Leste dos EUA com um limite total de núcleos de 30, um limite de núcleos da série A de 30 e um limite de núcleos da série D de 30. Essa assinatura teria permissão para implantar 30 VMs a1 ou 30 VMs D1 ou uma combinação dos dois que não exceda um total de 30 núcleos.

Não é possível gerar limites para máquinas virtuais acima dos valores mostrados na tabela a seguir.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="container-instances"></a>Instâncias de Contêiner

Para obter mais informações, consulte [limites de instâncias de contêiner](../azure-resource-manager/management/azure-subscription-service-limits.md#container-instances-limits).

### <a name="storage"></a>Armazenamento
O armazenamento do Azure tem um limite de 250 contas de armazenamento por região, por assinatura. Esse limite inclui contas de armazenamento Standard e Premium.

Para aumentar o limite, faça uma solicitação por meio [do suporte do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). A equipe de armazenamento do Azure examinará seu caso e poderá aprovar até 250 contas de armazenamento para uma região.


## <a name="workspace-level-quotas"></a>Cotas de nível de espaço de trabalho

Use cotas de nível de espaço de trabalho para gerenciar Azure Machine Learning a alocação de destino de computação entre vários [espaços de trabalho](concept-workspace.md) na mesma assinatura.

Por padrão, todos os espaços de trabalho compartilham a mesma cota que a cota de nível de assinatura para as famílias de VMs. No entanto, você pode definir uma cota máxima para famílias de VM individuais em espaços de trabalho em uma assinatura. Isso permite que você compartilhe a capacidade e evite problemas de contenção de recursos.

1. Vá para qualquer espaço de trabalho em sua assinatura.
1. No painel esquerdo, selecione **usos + cotas**.
1. Selecione a guia **configurar cotas** para exibir as cotas.
1. Expanda uma família de VMs.
1. Defina um limite de cota em qualquer espaço de trabalho listado nessa família de VMs.

Você não pode definir um valor negativo ou um valor maior que a cota de nível de assinatura.

[![Captura de tela que mostra uma cota de nível de espaço de trabalho Azure Machine Learning.](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)

> [!NOTE]
> Você precisa de permissões em nível de assinatura para definir uma cota no nível do espaço de trabalho.

## <a name="view-your-usage-and-quotas"></a>Exibir seu uso e cotas

Para exibir a cota de vários recursos do Azure, como máquinas virtuais, armazenamento ou rede, use o portal do Azure:

1. No painel esquerdo, selecione **todos os serviços** e, em seguida, **assinaturas** na categoria **geral** .

2. Na lista de assinaturas, selecione a assinatura cuja cota que você está procurando.

3. Selecione **uso + cotas** para exibir os limites de cota e o uso atuais. Use os filtros para selecionar o provedor e os locais. 

Você gerencia a cota de computação Azure Machine Learning em sua assinatura separadamente de outras cotas do Azure: 

1. Vá para o espaço de trabalho do **Azure Machine Learning** na portal do Azure.

2. No painel esquerdo, na seção **suporte + solução de problemas** , selecione **uso + cotas** para exibir os limites e o uso da cota atual.

3. Selecione uma assinatura para exibir os limites de cota. Filtre a região em que você está interessado.

4. Você pode alternar entre uma exibição no nível da assinatura e uma exibição no nível do espaço de trabalho.

## <a name="request-quota-increases"></a>Solicitar aumentos de cota

Para aumentar o limite ou a cota acima do limite padrão, [abra uma solicitação](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) de atendimento ao cliente online sem encargos.

Não é possível aumentar os limites acima dos valores máximos mostrados nas tabelas anteriores. Se não houver um limite máximo, você não poderá ajustar o limite para o recurso.

Quando você estiver solicitando um aumento de cota, selecione o serviço que você tem em mente. Por exemplo, selecione Azure Machine Learning, instâncias de contêiner ou armazenamento. Para Azure Machine Learning computação, você pode selecionar o botão **solicitar cota** ao exibir a cota nas etapas anteriores.

> [!NOTE]
> As [assinaturas de avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p) não estão qualificadas para aumentos de cota ou limite. Se você tiver uma assinatura de avaliação gratuita, poderá atualizar para uma assinatura [paga conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/) . Para obter mais informações, consulte [atualizar a avaliação gratuita do Azure para pagamento conforme o uso](../cost-management-billing/manage/upgrade-azure-subscription.md) e [perguntas frequentes sobre a conta gratuita do Azure](https://azure.microsoft.com/free/free-account-faq).

## <a name="private-endpoint-and-private-dns-quota-increases"></a>Aumentos de cota de ponto de extremidade privado e de DNS privado

Há limites no número de pontos de extremidade privados e zonas DNS privadas que você pode criar em uma assinatura.

Azure Machine Learning cria recursos em sua assinatura do (cliente), mas alguns cenários criam recursos em uma assinatura de propriedade da Microsoft.

 Nos cenários a seguir, talvez seja necessário solicitar uma concessão de cota na assinatura de propriedade da Microsoft:

* Espaço de trabalho habilitado para link privado do Azure com uma chave gerenciada pelo cliente (CMK)
* Registro de Contêiner do Azure para o workspace atrás da rede virtual
* Como anexar um cluster do Serviço de Kubernetes do Azure habilitado para Link Privado ao workspace

Para solicitar uma concessão para esses cenários, use as seguintes etapas:

1. [Crie uma solicitação de suporte do Azure](../azure-portal/supportability/how-to-create-azure-support-request.md#create-a-support-request) e selecione as seguintes opções na seção __noções básicas__ :

    | Campo | Seleção |
    | ----- | ----- |
    | Tipo de problema | **Técnico** |
    | Serviço | **Meus serviços**. Em seguida, selecione __Machine Learning__ na lista suspensa. |
    | Tipo de problema | **Configuração e segurança do espaço de trabalho** |
    | Subtipo de problema | **Ponto de extremidade particular e solicitação de concessão de zona de DNS privado** |

2. Na seção de __detalhes__ , use o campo __Descrição__ para fornecer a região do Azure e o cenário que você planeja usar. Se você precisar solicitar aumentos de cota para várias assinaturas, liste as IDs de assinatura neste campo.

3. Selecione __criar__ para criar a solicitação.

:::image type="content" source="media/how-to-manage-quotas/quota-increase-private-endpoint.png" alt-text="Captura de tela de um ponto de extremidade privado e uma solicitação de aumento de cota de DNS privado.":::

## <a name="next-steps"></a>Próximas etapas

+ [Planejar e gerenciar custos para Azure Machine Learning](concept-plan-manage-cost.md)
+ [Limites de serviço no Azure Machine Learning](resource-limits-quotas-capacity.md)
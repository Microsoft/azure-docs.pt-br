---
title: Gerenciar e solicitar cotas de recursos
titleSuffix: Azure Machine Learning service
description: Este guia de instruções explica as várias cotas em recursos para o Azure Machine Learning e como exibir e solicitar mais cota.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: aa425b6dfeb076448d14fc35cbea964516d603b0
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63765861"
---
# <a name="manage-and-request-quotas-for-azure-resources"></a>Gerenciar e solicitar cotas para recursos do Azure

Como acontece com outros serviços do Azure, há limites em determinados recursos associados ao serviço do Azure Machine Learning. Esses limites variam de um limite no número de workspaces que podem ser criados a limites na computação subjacente real que é usada para treinar ou inferir seus modelos. Este artigo fornece mais detalhes sobre os limites pré-configurados em vários recursos do Azure para sua assinatura e também contém links úteis para solicitar aprimoramentos de cota para cada tipo de recurso. Esses limites são estabelecidos para evitar estouros de orçamento devido a fraudes e respeitar as restrições de capacidade do Azure.

Lembre-se dessas cotas enquanto cria e aumenta seus recursos do Serviço do Azure Machine Learning para cargas de trabalho de produção. Por exemplo, se o cluster não atinge o número alvo de nós que você especificou, em seguida, você talvez tenha atingido um limite de núcleos de computação do Azure Machine Learning para sua assinatura. Se você desejar aumentar o limite ou a cota acima do limite padrão, abra uma solicitação de suporte ao cliente online sem custo. Os limites não podem ser aumentados além do valor Limite Máximo mostrado nas tabelas a seguir devido a restrições de Capacidade do Azure. Se não houver nenhuma coluna Limite Máximo, o recurso não terá limites ajustáveis.

## <a name="special-considerations"></a>Considerações especiais

+ Uma cota é um limite de crédito, não uma garantia de capacidade. Se precisar de capacidade em larga escala, contate o suporte do Azure.

+ A cota é compartilhada entre todos os serviços em suas assinaturas, incluindo o serviço do Azure Machine Learning. A única exceção é a Computação do Azure Machine Learning, que tem uma cota separada da cota de computação principal. Certifique-se de calcular o uso da cota em todos os serviços ao avaliar suas necessidades de capacidade.

+ Os limites padrão variam de acordo com o tipo de categoria oferecido, como avaliação gratuita, pago conforme o uso e séries como Dv2, F, G e assim por diante.

## <a name="default-resource-quotas"></a>Cotas de recurso padrão

Veja um detalhamento dos limites de cota por vários tipos de recurso dentro de sua assinatura do Azure.

> [!Important]
> Os limites estão sujeitos a alterações. A versão mais recente sempre pode ser encontrada no [documento](https://docs.microsoft.com/azure/azure-subscription-service-limits/) da cota de nível de serviço para tudo que é do Azure.

### <a name="virtual-machines"></a>Máquinas virtuais
Há um limite no número de máquinas virtuais que podem ser provisionadas em uma assinatura do Azure em seus serviços ou de maneira autônoma. Esse limite está no nível da região nos núcleos totais e também é calculado por família.

É importante enfatizar que os núcleos de máquina virtual têm um limite regional total, bem como um limite regional por tamanho de série (Dv2, F etc.), aplicados separadamente. Por exemplo, considere uma assinatura com uma VM do Leste dos EUA com um limite total de núcleos de 30, um limite de núcleos da série A de 30 e um limite de núcleos da série D de 30. Esta assinatura teria permissão para implantar 30 VMs A1, ou 30 VMs D1 ou uma combinação das duas a fim de não exceder um total de 30 núcleos (por exemplo, 10 VMs A1 e 20 VMs D1).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

Para obter uma lista mais detalhada e atualizada de limites de cota, confira o site de cota de todo o Azure [aqui](https://docs.microsoft.com/azure/azure-subscription-service-limits).

### <a name="azure-machine-learning-compute"></a>Computação do Azure Machine Learning
Para a Computação do Azure Machine Learning, há um limite de cota padrão para o número de núcleos e o número de recursos de computação exclusivos permitidos por região em uma assinatura. Essa cota é separada da cota do núcleo da VM acima e os limites de núcleo não são compartilhados no momento entre os dois tipos de recurso.

Recursos disponíveis:
+ Os núcleos dedicados por região têm um limite padrão de 10 a 24.  O número de núcleos dedicados por assinatura pode ser aumentado. Entre em contato com o suporte do Azure para discutir opções de aumento.

+ Núcleos de baixa prioridade por região têm um limite padrão de 10 a 24.  O número de núcleos de baixa prioridade por assinatura pode ser aumentado. Entre em contato com o suporte do Azure para discutir opções de aumento.

+ Os clusters por região têm um limite padrão de 100 e um limite máximo de 200. Contate o suporte do Azure se desejar solicitar um aumento além desse limite.

+ Há **outros limites rígidos** que não podem ser ultrapassados uma vez atingidos.

| **Recurso** | **Limite máximo** |
| --- | --- |
| Máximo de workspaces por grupo de recursos | 800 |
| Máximo de nós em um único recurso de Computação do Azure Machine Learning (AmlCompute) | 100 nós |
| Máximo de processos de MPI de GPU nó | 1-4 |
| Máximo de trabalhos de GPU nó | 1-4 |
| Tempo de vida máximo do trabalho | 7 dias<sup>1</sup> |
| Máximo de servidores de parâmetro por nó | 1 |

<sup>1</sup> O tempo de vida máximo se refere ao tempo desde que a execução começa até que seja concluída. Execuções concluídas persistem indefinidamente; dados de execuções não concluídas dentro do tempo de vida máximo não ficam acessíveis.

### <a name="container-instances"></a>Instâncias de contêiner

Também há um limite no número de instâncias de contêiner que você pode criar em um determinado período (por hora com escopo) ou em sua assinatura inteira.

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

Para obter uma lista mais detalhada e atualizada de limites de cota, confira o site de cota de todo o Azure [aqui](https://docs.microsoft.com/azure/azure-subscription-service-limits#container-instances-limits).

### <a name="storage"></a>Armazenamento
Há um limite no número de contas de armazenamento por região também em uma determinada assinatura. O limite padrão é 200 e inclui contas de armazenamento Standard e Premium. Se você precisar de mais de 200 contas de armazenamento em uma única região, faça uma solicitação por meio do [Suporte do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). A equipe de Armazenamento do Microsoft Azure examinará seu caso de negócios e poderá aprovar até 250 contas de armazenamento.


## <a name="find-your-quotas"></a>Localizar suas cotas

Por meio do portal do Azure, é fácil exibir sua cota para vários recursos, como Máquinas Virtuais, Armazenamento e Rede.

1. No painel esquerdo, selecione **Todos os serviços** e, em seguida, selecione **Assinaturas** na categoria Geral.

1. Na lista de assinaturas, selecione a assinatura cuja cota você está procurando.

   **Há uma limitação**, especificamente para exibir a cota de Computação do Azure Machine Learning. Conforme mencionado acima, essa cota é separada da cota de computação em sua assinatura.

1. No painel esquerdo, selecione **Serviço do Machine Learning** e, em seguida, selecione qualquer workspace na lista mostrada

1. Na folha seguinte, na seção **Suporte + solução de problemas**, selecione **Uso + cotas** para exibir os limites de cota e o uso atual.

1. Selecione uma assinatura para exibir os limites de cota. Lembre-se de filtrar para a região em que você está interessado.


## <a name="request-quota-increases"></a>Solicitar aumentos de cota

Se você desejar aumentar o limite ou a cota acima do limite padrão,  [abra uma solicitação de suporte ao cliente online](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) sem custo.

Os limites não podem ser aumentados acima do valor limite máximo mostrado nas tabelas. Se não houver limite máximo, então o recurso não terá limites ajustáveis. [Este](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors) artigo aborda o processo de aumento da cota em mais detalhes.

Ao solicitar um aumento da cota, é necessário selecionar o serviço com relação ao qual você está solicitando o aumento da cota, que poderiam ser serviços como a cota do serviço do Machine Learning, das instâncias de Contêiner ou da cota de Armazenamento. Além disso, para a Computação do Azure Machine Learning, você pode simplesmente clicar no botão **Solicitar Cota** ao exibir a cota seguindo as etapas acima.

> [!NOTE]
> As [assinaturas de Avaliação Gratuita](https://azure.microsoft.com/offers/ms-azr-0044p) não estão qualificadas para os aumentos de cota ou limite. Se você tiver uma [assinatura de Avaliação Gratuita](https://azure.microsoft.com/offers/ms-azr-0044p), você poderá atualizar para uma assinatura de [pagamento conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/). Para obter mais informações, consulte [Atualizar a Versão de Avaliação Gratuita do Azure para Pagamento Conforme o Uso](../../billing/billing-upgrade-azure-subscription.md) e [Perguntas Frequentes sobre a assinatura de Avaliação Gratuita](https://azure.microsoft.com/free/free-account-faq).

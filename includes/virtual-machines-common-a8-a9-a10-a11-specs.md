---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: include
ms.date: 05/29/2018
ms.author: azcspmt;jonbeck;cynthn;danlep;amverma
ms.custom: include file
ms.openlocfilehash: 88d9491bae1fcc6acb7ab631514aa4f6244f593d
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538023"
---
## <a name="deployment-considerations"></a>Considerações de implantação
* **Assinatura do Azure** – Para implantar um número maior de instâncias de computação intensiva, considere uma assinatura pré-paga ou outras opções de compra. Se estiver usando uma [conta gratuita do Azure](https://azure.microsoft.com/free/), você poderá usar apenas um número limitado de núcleos de computação do Azure.

* **Preço e disponibilidade** – esses tamanhos de VM são oferecidos apenas no tipo de preço Standard. Confira [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) para ver a disponibilidade nas regiões do Azure. 
* **Cota de núcleos** – Talvez seja preciso aumentar a cota de núcleos em sua assinatura do Azure, saindo do valor padrão. Sua assinatura também pode limitar o número de núcleos que você pode implantar em determinadas famílias de tamanho de VM, incluindo a série de H. Para solicitar um aumento de cota, [abra uma solicitação de atendimento ao cliente online](../articles/azure-supportability/how-to-create-azure-support-request.md) gratuitamente. (Os limites padrão podem variar dependendo de sua categoria de assinatura.)
  
  > [!NOTE]
  > Entre em contato com o Suporte do Azure se precisar de capacidade em larga escala. Cotas do Azure são limites de crédito, não garantias de capacidade. Independentemente de sua cota, você é cobrado apenas pelo núcleos utilizados.
  > 
  > 
* **Rede virtual** – Não é necessário ter uma [rede virtual](https://azure.microsoft.com/documentation/services/virtual-network/) do Azure para usar instâncias de computação intensiva. No entanto, para muitas implantações, é necessária pelo menos uma rede virtual do Azure baseada em nuvem ou uma conexão site a site se você precisar acessar recursos locais. Quando necessário, você precisará criar uma nova rede virtual para implantar as instâncias. Não há suporte para a adição de VMs de computação intensiva a uma rede virtual em um grupo de afinidades.
* **Redimensionamento** – devido ao hardware especializado, você só pode redimensionar instâncias de computação intensiva dentro da mesma família de tamanho (série H ou série A de computação intensiva). Por exemplo, somente é possível redimensionar uma VM da série H de um tamanho da série H para outro. Além disso, não há suporte para o redimensionamento de um tamanho sem computação intensiva para um tamanho de computação intensiva.  

## <a name="rdma-capable-instances"></a>Instâncias compatíveis com RDMA
Um subconjunto das instâncias com computação intensiva (A8, A9, H16r, H16mr, HB e HC) apresenta uma interface de rede para conectividade de acesso (RDMA) de memória direta remota. Selecionados tamanhos da série N designados com 'r', como as configurações de NC24rs (NC24rs_v2 e NC24rs_v3) também são compatíveis com RDMA. Essa interface é além do adaptador de rede do Azure padrão disponível para outros tamanhos de VM. 
  
Essa interface permite que as instâncias compatíveis com RDMA se comuniquem em uma rede InfiniBand (IB), operando em taxas EDR HB, HC, FDR taxas para máquinas virtuais H16r, H16mr e série N compatíveis com RDMA e taxas QDR para máquinas virtuais A8 e A9. Esses recursos RDMA podem melhorar a escalabilidade e o desempenho de determinados aplicativos MPI (Interface de Transmissão de Mensagens). Para obter mais informações sobre a velocidade, consulte os detalhes nas tabelas nesta página.

> [!NOTE]
> No Azure, IP sobre IB é suportada apenas em VMs (SR-IOV para InfiniBand, atualmente HB e HC) habilitadas para a SR-IOV. RDMA sobre IB é compatível com todas as instâncias compatíveis com RDMA.
>


---
title: Lidando com uma interrupção de serviço do Azure que afeta os serviços de nuvem do Azure (clássico)
description: Saiba o que fazer no caso uma interrupção de serviço do Azure que afete os Serviços de Nuvem do Azure.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: cdd6c9da5a1895d4aadd73133734cd4c8204ecf1
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742158"
---
# <a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services-classic"></a>O que fazer no caso de uma interrupção de serviço do Azure impactar os serviços de nuvem do Azure (clássico)

> [!IMPORTANT]
> Os [serviços de nuvem do Azure (suporte estendido)](../cloud-services-extended-support/overview.md) são um novo modelo de implantação baseado em Azure Resource Manager para o produto de serviços de nuvem do Azure.Com essa alteração, os serviços de nuvem do Azure em execução no modelo de implantação baseado no Azure Service Manager foram renomeados como serviços de nuvem (clássicos) e todas as novas implantações devem usar os [serviços de nuvem (suporte estendido)](../cloud-services-extended-support/overview.md).

Na Microsoft, trabalhamos muito para garantir que nossos serviços estejam sempre disponíveis quando você precisar deles. Às vezes, forças além do nosso controle nos afetam de formas que causam interrupções de serviço não planejadas.

A Microsoft fornece um SLA (Contrato de Nível de Serviço) para seus serviços como um compromisso com o tempo de atividade e a conectividade. O SLA para serviços individuais do Azure pode ser encontrado em [Contratos de Nível de Serviço do Azure](https://azure.microsoft.com/support/legal/sla/).

O Azure já tem muitos recursos internos de plataforma que oferecem suporte a aplicativos altamente disponíveis. Para saber mais sobre esses serviços, leia [Recuperação de desastres e alta disponibilidade para aplicativos do Azure](/azure/architecture/framework/resiliency/backup-and-recovery).

Este artigo aborda um cenário real de recuperação de desastre, quando uma região inteira sofre uma interrupção devido a um grande desastre natural ou a uma interrupção do serviço generalizada. Essas ocorrências são raras, mas você deve se preparar para a possibilidade de uma interrupção em toda uma região. Se toda a região sofrer uma interrupção de serviço, as cópias localmente redundantes dos dados poderão estar temporariamente indisponíveis. Se você tiver habilitado a replicação geográfica, haverá três cópias adicionais dos blobs de Armazenamento do Azure e tabelas armazenadas em uma região diferente. No caso de uma interrupção regional completa ou de um desastre no qual a região primária não seja recuperável, o Azure remapeia todas as entradas de DNS para a região geográfica replicada.

> [!NOTE]
> Lembre-se de que você não tem qualquer controle sobre esse processo e de que ele ocorrerá apenas em caso de interrupção do serviço em todo um datacenter. Por isso, você também deve contar com outras estratégias de backup específicas ao aplicativo para chegar ao nível mais alto de disponibilidade. Para saber mais, confira [Recuperação de desastres e alta disponibilidade para aplicativos criados no Microsoft Azure](/azure/architecture/framework/resiliency/backup-and-recovery). Se você quiser ser capaz de afetar seu próprio failover, convém considerar o uso de [RA-GRS (armazenamento com Redundância Geográfica com Acesso de Leitura)](../storage/common/storage-redundancy.md), que cria uma cópia somente leitura de seus dados em outra região.
>
>


## <a name="option-1-use-a-backup-deployment-through-azure-traffic-manager"></a>Opção 1: use uma implantação de backup por meio do Gerenciador de Tráfego do Azure
A solução mais robusta de recuperação de desastre envolve manter várias implantações do seu aplicativo em diferentes regiões, usando então o [Gerenciador de Tráfego do Azure](../traffic-manager/traffic-manager-overview.md) para direcionar o tráfego entre elas. Gerenciador de Tráfego do Azure fornece vários [métodos de roteamento](../traffic-manager/traffic-manager-routing-methods.md), portanto, você pode escolher se deseja gerenciar suas implantações usando um modelo de backup/primário ou dividir o tráfego entre eles.

![Balanceamento dos Serviços de Nuvem do Azure em regiões com o Gerenciador de Tráfego do Azure](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

Para a resposta mais rápida à perda de uma região, é importante que você configure o [monitoramento de ponto de extremidade](../traffic-manager/traffic-manager-monitoring.md) do Gerenciador de Tráfego.

## <a name="option-2-deploy-your-application-to-a-new-region"></a>Opção 2: implantar seu aplicativo em uma nova região
Manter várias implantações ativas conforme descrito na opção anterior incorre em custos contínuos adicionais. Se seu RTO (objetivo de tempo de recuperação) é suficientemente flexível e você tem o código original ou o pacote de Serviços de Nuvem compilado, você pode criar uma nova instância do seu aplicativo em outra região e atualizar seus registros DNS para apontar para a nova implantação.

Para obter mais detalhes sobre como criar e implantar um aplicativo de serviço de nuvem, confira [Como criar e implantar um serviço de nuvem](cloud-services-how-to-create-deploy-portal.md).

Dependendo das fontes de dados do aplicativo, você precisa verificar os procedimentos de recuperação para a fonte de dados do aplicativo.

* Para as fontes de dados do armazenamento do Azure, consulte [redundância de armazenamento do Azure](../storage/common/storage-redundancy.md) para verificar as opções disponíveis com base no modelo de redundância escolhido para seu aplicativo.
* Para fontes de Banco de Dados SQL, confira [Visão geral: continuidade de negócios em nuvem e recuperação de desastre de banco de dados com o Banco de Dados SQL](../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md) para conhecer as opções disponíveis com base no modelo de replicação escolhido para o aplicativo.


## <a name="option-3-wait-for-recovery"></a>Opção 3: aguardar a recuperação
Nesse caso, não é necessária nenhuma ação de sua parte, mas seu serviço ficará indisponível até que a região seja restaurada. Você pode ver o status atual do serviço no [Painel de integridade do serviço do Azure](https://azure.microsoft.com/status/).

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como implementar uma estratégia de alta disponibilidade e recuperação de desastres, consulte [Recuperação de desastres e alta disponibilidade para aplicativos do Azure](/azure/architecture/framework/resiliency/backup-and-recovery).

Para desenvolver uma compreensão técnica detalhada dos recursos de uma plataforma de nuvem, consulte [Orientação técnica sobre a resiliência do Azure](/azure/architecture/checklist/resiliency-per-service).
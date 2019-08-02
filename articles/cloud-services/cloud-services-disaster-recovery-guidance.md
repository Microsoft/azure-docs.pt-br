---
title: O que fazer caso uma interrupção de serviço do Azure afete os Serviços de Nuvem do Azure | Microsoft Docs
description: Saiba o que fazer no caso uma interrupção de serviço do Azure que afete os Serviços de Nuvem do Azure.
services: cloud-services
documentationcenter: ''
author: mmccrory
ms.service: cloud-services
ms.workload: cloud-services
ms.topic: article
ms.date: 04/04/2017
ms.author: memccror
ms.openlocfilehash: 269bb59210e24623a16b27d21d7276c084e4cca7
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359663"
---
# <a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services"></a>O que fazer no caso de uma interrupção de serviço do Azure que afete os Serviços de Nuvem do Azure
Na Microsoft, trabalhamos muito para garantir que nossos serviços estejam sempre disponíveis quando você precisar deles. Às vezes, forças além do nosso controle nos afetam de formas que causam interrupções de serviço não planejadas.

A Microsoft fornece um SLA (Contrato de Nível de Serviço) para seus serviços como um compromisso com o tempo de atividade e a conectividade. O SLA para serviços individuais do Azure pode ser encontrado em [Contratos de Nível de Serviço do Azure](https://azure.microsoft.com/support/legal/sla/).

O Azure já tem muitos recursos internos de plataforma que oferecem suporte a aplicativos altamente disponíveis. Para saber mais sobre esses serviços, leia [Recuperação de desastres e alta disponibilidade para aplicativos do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Este artigo aborda um cenário real de recuperação de desastre, quando uma região inteira sofre uma interrupção devido a um grande desastre natural ou a uma interrupção do serviço generalizada. Essas ocorrências são raras, mas você deve se preparar para a possibilidade de uma interrupção em toda uma região. Se toda a região sofrer uma interrupção de serviço, as cópias localmente redundantes dos dados poderão estar temporariamente indisponíveis. Se você tiver habilitado a replicação geográfica, haverá três cópias adicionais dos blobs de Armazenamento do Azure e tabelas armazenadas em uma região diferente. No caso de uma interrupção regional completa ou de um desastre no qual a região primária não seja recuperável, o Azure remapeia todas as entradas de DNS para a região geográfica replicada.

> [!NOTE]
> Lembre-se de que você não tem qualquer controle sobre esse processo e de que ele ocorrerá apenas em caso de interrupção do serviço em todo um datacenter. Por isso, você também deve contar com outras estratégias de backup específicas ao aplicativo para chegar ao nível mais alto de disponibilidade. Para saber mais, confira [Recuperação de desastres e alta disponibilidade para aplicativos criados no Microsoft Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md). Se você quiser ser capaz de afetar seu próprio failover, convém considerar o uso de [RA-GRS (armazenamento com Redundância Geográfica com Acesso de Leitura)](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage), que cria uma cópia somente leitura de seus dados em outra região.
>
>


## <a name="option-1-use-a-backup-deployment-through-azure-traffic-manager"></a>Opção 1: Usar uma implantação de backup por meio do Gerenciador de tráfego do Azure
A solução mais robusta de recuperação de desastre envolve manter várias implantações do seu aplicativo em diferentes regiões, usando então o [Gerenciador de Tráfego do Azure](../traffic-manager/traffic-manager-overview.md) para direcionar o tráfego entre elas. Gerenciador de Tráfego do Azure fornece vários [métodos de roteamento](../traffic-manager/traffic-manager-routing-methods.md), portanto, você pode escolher se deseja gerenciar suas implantações usando um modelo de backup/primário ou dividir o tráfego entre eles.

![Balanceamento dos Serviços de Nuvem do Azure em regiões com o Gerenciador de Tráfego do Azure](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

Para a resposta mais rápida à perda de uma região, é importante que você configure o [monitoramento de ponto de extremidade](../traffic-manager/traffic-manager-monitoring.md) do Gerenciador de Tráfego.

## <a name="option-2-deploy-your-application-to-a-new-region"></a>Opção 2: Implantar seu aplicativo em uma nova região
Manter várias implantações ativas conforme descrito na opção anterior incorre em custos contínuos adicionais. Se seu RTO (objetivo de tempo de recuperação) é suficientemente flexível e você tem o código original ou o pacote de Serviços de Nuvem compilado, você pode criar uma nova instância do seu aplicativo em outra região e atualizar seus registros DNS para apontar para a nova implantação.

Para obter mais detalhes sobre como criar e implantar um aplicativo de serviço de nuvem, confira [Como criar e implantar um serviço de nuvem](cloud-services-how-to-create-deploy-portal.md).

Dependendo das fontes de dados do aplicativo, você precisa verificar os procedimentos de recuperação para a fonte de dados do aplicativo.

* Para obter as fontes de dados do Armazenamento do Azure, confira [Replicação de Armazenamento do Azure](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) para conhecer as opções disponíveis com base no modelo de replicação escolhido para o aplicativo.
* Para fontes de banco de dados [SQL, leia Visão geral: Continuidade de negócios de nuvem e recuperação de desastre](../sql-database/sql-database-business-continuity.md) de banco de dados com o banco de dados SQL para verificar as opções disponíveis com base no modelo de replicação escolhido para seu aplicativo.


## <a name="option-3-wait-for-recovery"></a>Opção 3: Aguardar a recuperação
Nesse caso, não é necessária nenhuma ação de sua parte, mas seu serviço ficará indisponível até que a região seja restaurada. Você pode ver o status atual do serviço no [Painel de integridade do serviço do Azure](https://azure.microsoft.com/status/).

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como implementar uma estratégia de alta disponibilidade e recuperação de desastres, consulte [Recuperação de desastres e alta disponibilidade para aplicativos do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Para desenvolver uma compreensão técnica detalhada dos recursos de uma plataforma de nuvem, consulte [Orientação técnica sobre a resiliência do Azure](../resiliency/resiliency-technical-guidance.md).
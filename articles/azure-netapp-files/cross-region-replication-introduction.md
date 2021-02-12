---
title: Replicação entre regiões de volumes Azure NetApp Files | Microsoft Docs
description: Descreve o que Azure NetApp Files a replicação entre regiões, os pares de regiões com suporte, os objetivos de nível de serviço, a durabilidade dos dados e o modelo de custo.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: b-juche
ms.custom: references_regions
ms.openlocfilehash: fd3e8295820cd9388bd7999d3008b4c583dbf295
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99220897"
---
# <a name="cross-region-replication-of-azure-netapp-files-volumes"></a>Replicação entre regiões de volumes Azure NetApp Files

A funcionalidade de replicação de Azure NetApp Files fornece proteção de dados por meio da replicação de volume entre regiões. Você pode replicar dados de forma assíncrona de um volume Azure NetApp Files (origem) em uma região para outro Azure NetApp Files volume (destino) em outra região.  Essa funcionalidade permite o failover do aplicativo crítico no caso de uma interrupção ou um desastre em toda a região.

> [!IMPORTANT]
> O recurso de replicação entre regiões está atualmente em visualização pública. Você precisa enviar uma solicitação Waitlist para acessar o recurso por meio da [página de envio de espera de replicação entre regiões Azure NetApp files](https://aka.ms/anfcrrpreviewsignup). Aguarde um email de confirmação oficial da equipe de Azure NetApp Files antes de usar o recurso de replicação entre regiões.

## <a name="supported-cross-region-replication-pairs"></a><a name="supported-region-pairs"></a>Pares de replicação entre regiões com suporte

A replicação de volume Azure NetApp Files tem suporte entre vários pares regionais e não pares [do Azure](/azure/best-practices-availability-paired-regions#azure-regional-pairs) . No momento, a replicação de volume do Azure NetApp Files está disponível entre as seguintes regiões:  

### <a name="azure-regional-pairs"></a>Pares regionais do Azure

* Leste dos EUA e oeste dos EUA
* Leste dos EUA 2 e EUA Central
* Leste da Austrália e sudeste da Austrália
* Canadá central e leste do Canadá
* Sul da Índia e da Índia central 
* Centro-oeste da Alemanha e Norte da Alemanha
* Leste do Japão e oeste do Japão
* Norte da Europa e Europa Ocidental
* Sul do Reino Unido e Oeste do Reino Unido

### <a name="azure-regional-non-pairs"></a>Não pares regionais do Azure

*   Oeste dos EUA 2 e leste dos EUA
*   EUA Central do Sul e EUA Central
*   Sul EUA Central e leste dos EUA
*   EUA Central do Sul e leste dos EUA 2
*   Leste dos EUA e leste dos EUA 2
*   Leste da Austrália e sudeste asiático 
*   Centro-oeste da Alemanha e Sul do Reino Unido

## <a name="service-level-objectives"></a>Objetivos de nível de serviço

Os objetivos de ponto de recuperação (RPO) ou a perda de dados tolerável máxima, são definidos como duas vezes o agendamento de replicação.  O RPO real observado pode variar com base em fatores como o tamanho total do conjunto de dados, juntamente com a taxa de alteração, a porcentagem de substituições de dado e a largura de banda de replicação disponível para transferência.   

* Para o agendamento de replicação de 10 minutos, o RPO máximo é de 20 minutos.  
* Para o agendamento de replicação por hora, o RPO máximo é de duas horas.  
* Para o agendamento de replicação diária, o RPO máximo é de dois dias.  

O RTO (objetivo de tempo de recuperação) ou o tempo de inatividade de aplicativos de negócios tolerável máximo, é determinado por fatores na ativação do aplicativo e no fornecimento de acesso aos dados no segundo site. A parte de armazenamento do RTO para quebrar a relação de emparelhamento para ativar o volume de destino e fornecer acesso de dados de leitura e gravação no segundo site deve ser concluída em um minuto.

## <a name="cost-model-for-cross-region-replication"></a>Modelo de custo para replicação entre regiões  

Com Azure NetApp Files replicação entre regiões, você paga apenas pela quantidade de dados replicados. Não há encargos de configuração ou uma taxa mínima de uso. O preço de replicação é baseado na frequência de replicação e na região do volume de *destino* que você escolher durante a configuração de replicação inicial. Consulte a página de [preços do Azure NetApp files](https://azure.microsoft.com/pricing/details/netapp/) para obter mais informações.  

Regular Azure NetApp Files custo de capacidade de armazenamento se aplica ao volume de destino de replicação (também chamado de volume de *proteção de dados* ). 

### <a name="pricing-examples"></a>Exemplos de preços

O valor de replicação entre regiões cobrado em um mês baseia-se na quantidade de dados replicados por meio do recurso de replicação entre regiões durante esse mês. A quantidade de dados replicados é medida em GiB. Ele representa a soma dos dados replicados em duas regiões durante todas as replicações regulares dos volumes de origem para os volumes de destino e durante todas as replicações de ressincronização dos volumes de destino para os volumes de origem.

#### <a name="example-1-month-1-baseline-replication-and-incremental-replications"></a>Exemplo 1: mês 1 replicação de linha de base e replicações incrementais

Suponha as seguintes situações:

* O volume de *origem* é do nível de serviço Azure NetApp files *Premium* . Ele tem um tamanho de cota de volume de 1000 GiB e um tamanho de volume consumido de 500 GiB no início do primeiro dia de um mês. O volume está na região *Centro-Sul dos EUA* .
* O volume de *destino* é do nível de serviço do Azure NetApp files *Standard* . Está na região *leste dos EUA 2* .
* Você configurou uma replicação entre regiões com base em *hora* entre os dois volumes acima. Portanto, o preço da replicação é de $0.12 por GiB.
* Para simplificar, suponha que o volume de origem tenha uma constante mudança de dados 0,5-GiB a cada hora, mas o tamanho total consumido do volume não cresça (permanece em 500 GiB). 

Após a configuração inicial, a replicação de linha de base ocorre imediatamente.  

* Valor dos dados replicados durante a replicação de linha de base: `500 GiB`
* Cobranças de replicação de linha de base: `500 GiB * $0.12 = $60`

Após a replicação de linha de base, somente os blocos alterados são replicados. Portanto, somente 0,5 GiB de dados serão replicados a cada hora nas replicações incrementais subsequentes.

* Soma da quantidade de dados replicada entre as replicações incrementais por um mês de 30 dias: `0.5 GiB * 24 hours * 30 days = 360 GiB`
* Encargos de replicação incremental: `360 GiB * $0.12 = $43.2`

No final do mês 1, a taxa total de replicação entre regiões é a seguinte:  

*  Taxa total de replicação entre regiões do mês 1: `$60 + $43.2 = $103.2`

Regular Azure NetApp Files carga de capacidade de armazenamento se aplica ao volume de destino. No entanto, o volume de destino pode usar uma camada de armazenamento diferente da camada de volume de origem (e mais barata do que).

#### <a name="example-2-month-2-incremental-replications-and-resync-replications"></a>Exemplo 2: mês 2 replicações incrementais e replicações de ressincronização  

Suponha que você tenha um volume de origem, um volume de destino e uma relação de replicação entre os dois configurados, conforme descrito no exemplo 1. Por 29 dias do segundo mês (um mês de 30 dias), as replicações por hora ocorreram conforme o esperado.

* Soma da quantidade de dados replicada entre as replicações incrementais por 29 dias: `0.5 GiB * 24 hours * 29 days = 348 GiB`

Suponha que, no último dia do mês, uma interrupção não planejada ocorreu na região de origem e você tenha feito failover para o volume de destino. Após 2 horas, a região de origem foi recuperada e você executou uma replicação de ressincronização do volume de destino para o volume de origem. Durante as 2 horas, 0,8 GiB de alteração de dados ocorreram no volume de destino e precisaram ser ressincronizado para a origem.

* Soma da quantidade de dados replicada entre as replicações regulares por 22 horas no último dia: `0.5 GiB * 22 hours = 11 GiB`
* Quantidade de dados replicada durante uma replicação de ressincronização: `0.8 GiB`

Portanto, no final do mês 2, a taxa total de replicação entre regiões é a seguinte:  

* Taxa total de replicação entre regiões do mês 2: `(348 GiB + 11 GiB + 0.8 GiB) * $0.12 = $43.18`

O custo regular de capacidade de armazenamento Azure NetApp Files para o mês 2 se aplica ao volume de destino.

## <a name="next-steps"></a>Próximas etapas
* [Requisitos e considerações para usar a replicação entre regiões](cross-region-replication-requirements-considerations.md)
* [Criar replicação de volume](cross-region-replication-create-peering.md)
* [Exibir o status de integridade da relação de replicação](cross-region-replication-display-health-status.md)
* [Gerenciar a recuperação de desastre](cross-region-replication-manage-disaster-recovery.md)
* [Métricas de replicação de volume](azure-netapp-files-metrics.md#replication)
* [Excluir volumes ou replicações de volume](cross-region-replication-delete.md)
* [Solucionar problemas de replicação entre regiões](troubleshoot-cross-region-replication.md)



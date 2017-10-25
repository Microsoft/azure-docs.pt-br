---
title: "Introdução à camada Premium do Cache Redis do Azure | Microsoft Docs"
description: "Saiba como criar e gerenciar a Persistência do Redis, clustering do Redis e o suporte de VNET para as instâncias da camada Premium do Cache Redis do Azure"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 30f46f9f-e6ec-4c38-a8cc-f9d4444856e5
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: sdanie
ms.openlocfilehash: c7a70e74f8b275ed9e10118b0ae9e81309f97ba3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-the-azure-redis-cache-premium-tier"></a>Introdução à camada Premium do Cache Redis do Azure
O Cache Redis do Azure é um cache distribuído e gerenciado que ajuda você a criar aplicativos altamente escalonáveis e responsivos, fornecendo acesso extremamente rápido aos seus dados. 

A nova camada Premium é uma camada pronta para Empresas, que inclui todos os recursos da camada Standard e muito mais, como melhor desempenho, cargas de trabalho maiores, recuperação de desastres, importação/exportação e segurança avançada. Continue lendo para saber mais sobre os recursos adicionais da camada de cache Premium.

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>Melhor desempenho em relação às camadas Standard ou Basic.
**Melhor desempenho em relação às camadas Standard ou Básica.** Os caches na camada Premium são implantados no hardware que tem processadores mais rápidos e que oferece um melhor desempenho quando comparado às Camadas Standard ou Básica. Os Caches da camada Premium têm a taxa de transferência mais alta e as latências mais baixas. 

**A taxa de transferência para o Cache do mesmo tamanho é maior na camada Premium quando comparada à camada Standard.** Por exemplo, a taxa de transferência para um Cache de 53 GB P4 (Premium) é de 250 mil solicitações por segundo quando comparada a 150 mil do C6 (Standard).

Para obter mais informações sobre tamanho, taxa de transferência e largura de banda com caches premium, veja [Perguntas frequentes sobre o Cache Redis do Azure](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)

## <a name="redis-data-persistence"></a>Persistência de dados do Redis
A camada Premium permite persistir os dados de cache em uma conta do Armazenamento do Azure. Em um cache Básico/Standard, todos os dados são armazenados apenas na memória. Em caso de problemas de infraestrutura subjacente, pode haver uma possível perda de dados. Recomendamos usar o recurso de persistência de dados do Redis na camada Premium para aumentar a resiliência contra a perda de dados. O Cache Redis do Azure oferece opções de RDB e AOF (em breve) na [persistência do Redis](http://redis.io/topics/persistence). 

Para obter instruções sobre como configurar a persistência, veja [Como configurar a persistência para um Cache Redis do Azure Premium](cache-how-to-premium-persistence.md).

## <a name="redis-cluster"></a>Cluster Redis
Se desejar criar caches maiores que 53 GB ou fragmentar dados entre vários nós do Redis, você pode usar o clustering do Redis que está disponível na camada Premium. Cada nó consiste em um par de cache primário/de réplica gerenciado pelo Azure para alta disponibilidade. 

**O clustering do Redis fornece uma produtividade e escala máximas.** A taxa de transferência aumenta linearmente à medida que o número de fragmentos (nós) no cluster aumenta. Por exemplo, Se você criar um cluster P4 de 10 fragmentos, a produtividade disponível será de 250 mil *10 = 2,5 milhões de solicitações por segundo. Consulte as [Perguntas Frequentes do Cache Redis do Azure](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) para obter mais detalhes sobre o tamanho, produtividade e largura de banda com os caches premium.

Para começar com o clustering, veja [Como configurar o clustering para um Cache Redis do Azure Premium](cache-how-to-premium-clustering.md).

## <a name="enhanced-security-and-isolation"></a>Isolamento e segurança avançados
Os caches criados na camada Básica ou Standard podem ser acessados na Internet pública. O acesso ao Cache é restrito com base na tecla de acesso. Com a camada Premium, é possível garantir ainda mais que apenas os clientes em uma rede especificada possam acessar o Cache. É possível implantar o Cache Redis em uma [VNet (Rede Virtual) do Azure](https://azure.microsoft.com/services/virtual-network/). Você pode usar todos os recursos da VNet como sub-redes, políticas de controle de acesso e outros recursos para restringir ainda mais o acesso ao Redis.

Para saber mais, consulte [Como configurar o suporte de Rede Virtual para um Cache Redis do Azure Premium](cache-how-to-premium-vnet.md).

## <a name="importexport"></a>Importar/exportar
A Importação/Exportação é uma operação de gerenciamento de dados do Cache Redis do Azure que permite importar dados para o Cache Redis do Azure ou exportar dados dele importando e exportando um instantâneo do RDB (Banco de Dados do Cache Redis) de um cache premium para um blob de páginas em uma Conta de Armazenamento do Azure. Isso permite migrar entre diferentes instâncias do Cache Redis do Azure ou popular o cache com os dados antes de usar.

A importação pode ser usada para trazer arquivos RDB compatíveis com o Redis de qualquer servidor Redis em execução em qualquer nuvem ou ambiente, incluindo o Redis em execução no Linux, Windows ou qualquer provedor de nuvem como Amazon Web Services e similares. Importar os dados é uma maneira fácil de criar um cache com dados previamente populados. Durante o processo de importação, o Cache Redis do Azure carrega os arquivos RDB do armazenamento do Azure para a memória e insere as chaves no cache.

A exportação permite exportar os dados armazenados no Cache Redis do Azure para arquivos RDB compatíveis com Redis. Você pode usar esse recurso para mover dados de uma instância do Cache Redis do Azure para outro ou para outro servidor Redis. Durante o processo de exportação, um arquivo temporário é criado na VM que hospeda a instância de servidor do Cache Redis do Azure e o arquivo é carregado para a conta de armazenamento designada. Após a operação de exportação ser concluída com um status de êxito ou de falha, o arquivo temporário é excluído.

Para obter mais informações, consulte [Como importar dados e exportar dados do Cache Redis do Azure](cache-how-to-import-export-data.md).

## <a name="reboot"></a>Reboot
A camada premium permite a reinicialização de um ou mais nós de seu cache sob demanda. Isso permite que você teste seu aplicativo para garantir a resiliência em caso de falhas. Você pode reinicializar os seguintes nós.

* Nó mestre do cache
* Nó subordinado do cache
* Nós mestre e subordinado do cache
* Ao usar um cache premium com clustering, você pode reinicializar o nó mestre, o nó subordinado ou ambos para fragmentos individuais no cache

Para obter mais informações, consulte [Reinicializar](cache-administration.md#reboot) e [Perguntas frequentes sobre reinicialização](cache-administration.md#reboot-faq).

>[!NOTE]
>A funcionalidade de reinicialização agora está habilitada para todas as camadas do Cache Redis do Azure.
>
>

## <a name="schedule-updates"></a>Agendar atualizações
O recurso de atualizações agendadas permite designar uma janela de manutenção para seu cache. Quando a janela de manutenção é especificada, as atualizações do servidor Redis são feitas durante essa janela. Para designar uma janela de manutenção, selecione os dias desejados e especifique o horário de início da janela de manutenção para cada dia. Observe que o horário da janela de manutenção é em UTC. 

Para obter mais informações, consulte [Agendar atualizações](cache-administration.md#schedule-updates) e [Perguntas frequentes sobre agendamento de atualizações](cache-administration.md#schedule-updates-faq).

> [!NOTE]
> Apenas as atualizações do servidor Redis são realizadas durante a janela de manutenção agendada. A janela de manutenção se aplica a atualizações do Azure ou do sistema operacional da VM.
> 
> 

## <a name="geo-replication"></a>Replicação geográfica

A **Replicação geográfica** fornece um mecanismo para vincular duas instâncias de Cache Redis do Azure de camada Premium. Um cache é designado como o cache vinculado primário e o outro como o cache vinculado secundário. O cache vinculado secundário se torna somente leitura e os dados gravados no cache primário são replicados para o cache vinculado secundário. Essa funcionalidade pode ser usada para replicar um cache entre regiões do Azure.

Para obter mais informações, consulte [How to configure Geo-replication for Azure Redis Cache](cache-how-to-geo-replication.md) (Como configurar a replicação geográfica para o Cache Redis do Azure).


## <a name="to-scale-to-the-premium-tier"></a>Para dimensionar para a camada premium
Para dimensionar para a camada premium, basta escolher uma das camadas premium na folha **Alterar camada de preços** . Você também pode dimensionar seu cache para a camada premium usando o PowerShell e a CLI. Para obter instruções passo a passo, consulte [Como Dimensionar o Cache Redis do Azure](cache-how-to-scale.md) e [Como automatizar uma operação de dimensionamento](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

## <a name="next-steps"></a>Próximas etapas
Crie um cache e explore os novos recursos da camada premium.

* [Como configurar a persistência para um Cache Redis do Azure Premium](cache-how-to-premium-persistence.md)
* [Como configurar o suporte de Rede Virtual para um Cache Redis do Azure Premium](cache-how-to-premium-vnet.md)
* [Como configurar o clustering para um Cache Redis do Azure Premium](cache-how-to-premium-clustering.md)
* [Como importar dados para e exportar dados do Cache Redis do Azure](cache-how-to-import-export-data.md)
* [Como administrar o Cache Redis do Azure](cache-administration.md)


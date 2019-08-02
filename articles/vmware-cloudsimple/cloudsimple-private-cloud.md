---
title: Nuvens privadas na solução do VMware CloudSimple - Azure
description: Saiba mais sobre conceitos e CloudSimple de nuvens privadas.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e3ab53466cae5bd39d85f8d846c8e59ad94f1f2d
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165754"
---
# <a name="cloudsimple-private-cloud-overview"></a>Visão geral de nuvem privada CloudSimple

CloudSimple transforma e estende as cargas de trabalho do VMware para nuvens públicas em minutos. Usando o serviço CloudSimple, você pode implantar VMware nativamente na infraestrutura do Azure bare metal. Sua implantação reside nos locais do Azure e integra-se totalmente com o restante da nuvem do Azure.

* A solução de CloudSimple fornece a continuidade operacional VMware completa. Essa solução oferece os benefícios da nuvem pública do:
  * Elasticidade
  * Inovação
  * Eficiência
* Com CloudSimple, você se beneficia de um modelo de consumo de nuvem que reduz o custo total de propriedade. Ele também oferece a otimização da capacidade e sob demanda provisionamento, pague à medida que você se expandir.
* CloudSimple é totalmente compatível com:
  * Ferramentas existentes
  * Habilidades
  * Processos
* Essa compatibilidade permite que as equipes a gerenciar cargas de trabalho na nuvem do Azure, sem interromper suas políticas:
  * Rede
  * Segurança  
  * Proteção de dados  
  * Audit
* CloudSimple gerencia a infraestrutura e todos os sistema de rede e gerenciamento de serviços necessários. O serviço de CloudSimple permite que sua equipe para se concentrar em:
  * Valor comercial
  * Provisionamento de aplicativo
  * Continuidade dos negócios
  * Suporte
  * Aplicação de políticas

## <a name="private-cloud-environment-overview"></a>Visão geral do ambiente de nuvem privada

Uma nuvem privada é uma pilha de VMware isolada, como esses ambientes:

* Hosts ESXi
* vCenter
* vSAN
* NSX

Nuvens privadas são gerenciadas por um servidor do vCenter em seu próprio domínio de gerenciamento.

A pilha é executado em:

* Nós dedicados
* Nós isolado hardware bare metal

Os usuários consomem a pilha por meio de ferramentas nativas do VMware, incluindo:

* vCenter
* Gerenciador de NSX

Você pode implantar nós dedicados em locais do Azure. Em seguida, você pode gerenciá-los com o Azure e CloudSimple. Uma nuvem privada consiste em um ou mais clusters do vSphere, e cada cluster contém nós de 3 a 16.

Você pode criar uma nuvem privada usando nós provisionados:

* Nós de pré-pago
* Nós dedicado, reservados

Você pode se conectar a nuvem privada para seu ambiente local e a rede do Azure usando as seguintes conexões:

* Segurança
* VPN privado
* Azure ExpressRoute

O ambiente de nuvem privada é projetado para eliminar a necessidade de um ponto único de falha:

* ESXi clusters são configurados com vSphere alta disponibilidade e são dimensionados para ter pelo menos um nó sobressalente para garantir a resiliência.
* vSAN fornece armazenamento primário redundante. vSan requer pelo menos três nós para fornecer proteção contra uma falha de única. Você pode configurar vSAN para fornecer maior capacidade de recuperação para clusters maiores.
* Você pode configurar as VMs do Gerenciador de NSX, PSC e vCenter com a política de armazenamento RAID 10 para proteger contra falhas de armazenamento. Em seguida, eles são protegidos por vSphere HA contra falhas de nó e rede.

## <a name="scenarios-for-deploying-a-private-cloud"></a>Cenários de implantação de uma nuvem privada

* **Desativação do data center ou a migração**

  * Obtenha capacidade adicional quando você alcançar os limites do seu datacenter existente ou atualizar hardware.
  * Adicionar a capacidade necessária na nuvem e eliminar as dores de cabeça de gerenciamento de atualizações de hardware.
  * Reduza o risco e o custo de migrações de nuvem, em comparação comparada conversões demoradas ou reestruturação.
  * Use habilidades e ferramentas familiares do VMware para acelerar as migrações de nuvem. Na nuvem, use os serviços do Azure para modernizar seus aplicativos no seu ritmo.

* **Expandir sob demanda**

  * Expanda para a nuvem para atender às necessidades imprevistas, como novos ambientes de desenvolvimento ou picos sazonais de capacidade.
  * Criar uma nova capacidade sob demanda e mantê-lo somente quando precisar dele.
  * Reduzir seu investimento inicial, acelere a velocidade do provisionamento e reduzir a complexidade com a mesma arquitetura e as políticas locais e na nuvem.

* **Recuperação de desastres e áreas de trabalho virtuais na nuvem do Azure**

  * Estabeleça o acesso remoto a dados, aplicativos e áreas de trabalho na nuvem do Azure. Com conexões de alta largura de banda, você pode carregar / baixar os dados rapidamente para a recuperação de incidentes. Dê as redes de baixa latência rápida de resposta atinge o tempo que os usuários esperam de um aplicativo da área de trabalho.

  * Replica todas as suas políticas e a rede na nuvem usando o portal de CloudSimple e ferramentas familiares do VMware. Essa replicação reduz o esforço e o risco de criar e gerenciar as implementações de recuperação de Desastre e VDI.

* **Bancos de dados e aplicativos de alto desempenho**

  * Execute suas cargas de trabalho mais exigentes, com a arquitetura de hiperconvergente fornecida pelos CloudSimple.
  * Executar Oracle, Microsoft SQL server, middleware de sistemas e bancos de dados de alto desempenho no SQL.

  * Experimente a nuvem como seu próprio data center com conexões de rede de alta velocidade 25 Gbps. Conexões de alta velocidade que você possa executar aplicativos híbridos que abrangem o VMware no Azure, no local do Azure privadas cargas de trabalho e sem comprometer o desempenho.

* **Verdadeiramente híbrido**

  * Unifica o DevOps em serviços de VMware e do Azure.
  * Otimize a administração de VMware para soluções que podem ser aplicadas em todas as suas cargas de trabalho e serviços do Azure.
  * Acessar os serviços de nuvem pública sem precisar expandir seu data center ou refazer a arquitetura de seus aplicativos.
  * Centralize identidades, políticas de controle de acesso, registro em log e monitoramento de aplicativos do VMware no Azure.

## <a name="limits"></a>limites

Tabela a seguir mostra os limites do nó nos recursos de uma nuvem privada.

| Resource | Limite |
|----------|-------|
| Número mínimo de nós para criar uma nuvem privada | 3 |
| Número máximo de nós em um cluster em uma nuvem privada | 16 |
| Número máximo de nós em uma nuvem privada | 64 |
| Número mínimo de nós em um novo cluster | 3 |

## <a name="next-steps"></a>Próximas etapas

* Saiba como [criar uma nuvem privada](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Saiba como [configurar um ambiente de nuvem privada](quickstart-create-private-cloud.md)
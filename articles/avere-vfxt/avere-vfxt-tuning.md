---
title: Ajuste de cluster do Avere vFXT – Azure
description: Saiba mais sobre o ajuste personalizado para clusters vFXT no avere vFXT para o Azure que você pode fazer, trabalhando com um representante de suporte.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 5d9f81c9438cb992f81bd3e6319532d67db75552
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88272375"
---
# <a name="cluster-tuning"></a>Ajuste do cluster

A maioria dos clusters vFXT podem se beneficiar de configurações de desempenho personalizadas. Essas configurações ajudam o cluster a funcionar melhor com seu fluxo de trabalho, conjunto de dados e ferramentas específicos.

Essa personalização deve ser feita com a ajuda de um representante de suporte, pois ela pode envolver a configuração de recursos que não estão disponíveis no painel de controle do avere.

Esta seção descreve alguns dos ajustes personalizados que podem ser feitos.

## <a name="general-optimizations"></a>Otimizações gerais

Essas alterações podem ser recomendadas com base em qualidades do conjuntos de dados ou no estilo do fluxo de trabalho.

* Se a carga de trabalho for pesada em termos de gravação, aumente o tamanho do cache de gravação do padrão de 20%.
* Se o conjunto de dados envolve muitos arquivos pequenos, aumente o limite de contagem de arquivos do cache do cluster.
* Se o trabalho envolve copiar ou mover dados entre dois repositórios, ajuste o número de threads usados para a movimentação de dados:
  * Para aumentar a velocidade, você poderá aumentar o número de threads paralelos usados.
  * Se o volume de armazenamento de back-end está se tornando sobrecarregado, você talvez precise diminuir o número de threads paralelos usados.
* Se o cluster armazena em cache os dados para um servidor de dados principal que usa ACLs NFSv4, habilite o cache do modo de acesso para simplificar a autorização de arquivo para clientes específicos.

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>NAS de nuvem ou otimizações de gateway de nuvem

Em um cenário de NAS ou gateway na nuvem, o cluster vFXT fornece acesso em estilo NAS a um contêiner de nuvem. Para aproveitar as maiores velocidades de dados entre o cluster vFXT e o armazenamento em nuvem, seu representante pode recomendar a alteração de configurações para enviar dados de forma mais agressiva para o volume de armazenamento do cache. Por exemplo:

* Aumentar o número de conexões TCP entre o cluster e o contêiner de armazenamento

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>Intermitência de nuvem ou otimizações de WAN híbrido

Em cenários de intermitência de nuvem ou cenário de otimização de WAN de armazenamento híbrido, o cluster vFXT fornece integração entre o armazenamento de hardware local e na nuvem. Essas alterações podem ser úteis:

* Aumentar o número de conexões TCP permitidas entre o cluster e o arquivista de núcleo
* Habilitar a configuração de Otimização de WAN para o arquivista de núcleo remoto (essa configuração pode ser usada para um arquivista local remoto ou para um arquivista de núcleo de nuvem em uma região do Azure diferente.)
* Aumentar o tamanho do buffer de soquete TCP<sup>*</sup>
* Habilitar a configuração "sempre encaminhar" para reduzir arquivos armazenados em cache com redundância<sup>*</sup>

<sup>*</sup>Esses ajustes podem não se aplicar a todos os sistemas, dependendo das necessidades de carga de trabalho e desempenho.

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>Ajudar a otimizar o Avere vFXT para o Azure

Para entrar em contato com a equipe de suporte sobre essas otimizações, use o procedimento descrito em [obter ajuda com o sistema](avere-vfxt-open-ticket.md).

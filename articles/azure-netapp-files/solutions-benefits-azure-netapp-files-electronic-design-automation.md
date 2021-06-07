---
title: Benefícios do uso de Azure NetApp Files para automação de design eletrônico | Microsoft Docs
description: Explica a solução que o Azure NetApp Files fornece para atender às necessidades do setor de design de chips e condutores. Apresenta cenários de teste que executam um benchmark do setor padrão para EDA (automação de design eletrônico) usando Azure NetApp Files.
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
ms.date: 04/24/2020
ms.author: b-juche
ms.openlocfilehash: fcede16619e8488796adc6f4c60af30643c1aadf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "82160146"
---
# <a name="benefits-of-using-azure-netapp-files-for-electronic-design-automation"></a>Benefícios de usar o Azure NetApp Files para automação de design eletrônico

O tempo de colocação no mercado (TTM) é uma consideração crítica para o setor de design de chips e condutores. O setor tem alta largura de banda e necessidades de baixa latência para armazenamento. Este artigo explica a solução que o Azure NetApp Files fornece para atender às necessidades do setor. Ele apresenta cenários de teste que executam um benchmark do setor padrão para EDA (automação de design eletrônico) usando Azure NetApp Files. 

## <a name="test-scenario-configurations"></a>Configurações de cenário de teste

Os testes envolvem três cenários com as seguintes configurações. 

|    Cenário    |    Volumes    |    Clientes<br> D16s_v3 SLES15  |
|----------------|---------------|--------------------------------|
|    Um         |    1          |    1                           |
|    Dois         |    6          |    24                          |
|    Três       |    12         |    24                          |

O primeiro cenário aborda até que distância um único volume pode ser orientado.  

O segundo e o terceiro cenários avaliam os limites de um único ponto de extremidade de Azure NetApp Files. Eles investigam os possíveis benefícios de latência e limites superiores de e/s.

## <a name="test-scenario-results"></a>Resultados do cenário de teste

A tabela a seguir resume os resultados dos cenários de teste.

|    Cenário       |    Taxa de e/s<br>  a 2 MS     |    Taxa de e/s<br>  na borda     |    Produtividade<br>  a 2 MS     |    Produtividade<br>  na borda     |
|-------------------|---------------------------|--------------------------------|-----------------------------|----------------------------------|
|    1 volume       |    39.601                 |    49.502                      |    692 MiB/s                 |    866 MiB/s                      |
|    6 volumes      |    255.613                |    317.000                     |    4.577 MiB/s               |    5.568 MiB/s                    |
|    12 volumes     |    256.612                |    319.196                     |    4.577 MiB/s               |    5.709 MiB/s                    |

O cenário de volume único representa a configuração básica do aplicativo. É o cenário de linha de base para cenários de teste de acompanhamento.  

O cenário de seis volumes demonstra um aumento linear (600%) em relação à carga de trabalho de volume único.  Todos os volumes em uma única rede virtual são acessados por um único endereço IP.  

O cenário de 12 volumes demonstra uma diminuição geral na latência sobre o cenário de seis volumes. Mas não tem um aumento correspondente na taxa de transferência atingível.   

O grafo a seguir ilustra a taxa de latência e operações para a carga de trabalho EDA no Azure NetApp Files.  

![Taxa de latência e operações para a carga de trabalho EDA no Azure NetApp Files](../media/azure-netapp-files/solutions-electronic-design-automation-workload-latency-operation-rate.png)   

O grafo a seguir ilustra a latência e a taxa de transferência para a carga de trabalho EDA no Azure NetApp Files.  

![Latência e taxa de transferência para a carga de trabalho EDA no Azure NetApp Files](../media/azure-netapp-files/solutions-electronic-design-automation-workload-latency-throughput.png) 

## <a name="layout-of-test-scenarios"></a>Layout dos cenários de teste 

A tabela a seguir resume o layout dos cenários de teste.

|    Cenário de teste     |    Número total de diretórios     |    Número total de arquivos     |
|----------------------|------------------------------------|------------------------------|
|    1 volume          |    88.000                          |    880.000                   |
|    6 volumes         |    568.000                         |    5.680.000                 |
|    12 volumes        |    568.000                         |    5.680.000                 |

A carga de trabalho completa é uma mistura de fases físicas e funcionais em execução simultânea. Ele representa um fluxo típico de um conjunto de ferramentas de EDA para outro.   

A fase funcional consiste em especificações iniciais e um design lógico. A fase física ocorre quando o design lógico é convertido em um chip físico. Durante as fases de desconexão e de fita, as verificações finais são concluídas e o design é entregue a um local para fabricação.  

A fase funcional inclui uma mistura de e/s de leitura e gravação aleatória e sequencial. A fase funcional é de uso intensivo de metadados, como estatísticas de arquivo e chamadas de acesso. Embora as operações de metadados sejam efetivamente sem tamanho, as operações de leitura e gravação variam entre menos de 1 K e 16 K. A maioria das leituras está entre 4 K e 16 K.  A maioria das gravações é 4 K ou menos. A fase física é composta de operações sequenciais de leitura e gravação inteiramente, com uma mistura de tamanhos de OP de 32 K e 64 K.  

Nos grafos acima, a maior parte da taxa de transferência é proveniente da fase física sequencial da carga de trabalho. A e/s vem da pequena fase funcional de indisponibilidade de metadados e aleatória. Ambas as fases acontecem em paralelo. 

Concluindo, você pode emparelhar a computação do Azure com Azure NetApp Files para design EDA para obter largura de banda escalonável. 

## <a name="next-steps"></a>Próximas etapas

- [Arquiteturas da solução usando o Azure NetApp Files](azure-netapp-files-solution-architectures.md)

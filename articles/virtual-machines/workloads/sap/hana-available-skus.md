---
title: SKUs para SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs
description: SKUs para SAP HANA no Azure (Instâncias Grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b80f872c82061c0cb87f4f1e2714183e71cf02cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60794012"
---
# <a name="available-skus-for-hli"></a>SKUs disponíveis para HLI

O serviço SAP HANA no Azure (Instâncias Grandes) está disponível em várias configurações nas regiões Azure do Oeste dos EUA e Leste dos EUA, Leste da Austrália, Sudeste da Austrália, Europa Ocidental, Norte da Europa, Leste do Japão e Oeste do Japão.

[SKUs certificados pelo SAP HANA de instâncias grandes do HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) listam como:

| Solução SAP | CPU | Memória | Armazenamento | Disponibilidade |
| --- | --- | --- | --- | --- |
| Otimizado para OLAP: SAP BW, BW/4HANA<br /> ou SAP HANA para carga de trabalho OLAP genérica | SAP HANA no Azure S72<br /> – 2 x processadores Intel® Xeon® E7-8890 v3<br /> 36 núcleos de CPU e 72 threads de CPU |  768 GB |  3 TB | Não é mais oferecido |
| --- | SAP HANA no Azure S144<br /> – 4 x processadores Intel® Xeon® E7-8890 v3<br /> 72 núcleos de CPU e 144 threads de CPU |  1,5 TB |  6 TB | Não é mais oferecido |
| --- | SAP HANA no Azure S192<br /> – 4 x processadores Intel® Xeon® E7-8890 v4<br /> 96 núcleos de CPU e 192 threads de CPU |  2,0 TB |  8 TB | Disponível |
| --- | SAP HANA no Azure S384<br /> – 8 x processadores Intel® Xeon® E7-8890 v4<br /> 192 núcleos de CPU e 384 threads de CPU |  4,0 TB |  16 TB | Disponível |
| Otimizado para OLTP: SAP Business Suite<br /> no SAP HANA ou S/4HANA (OLTP),<br /> OLTP genérico | SAP HANA no Azure S72m<br /> – 2 x processadores Intel® Xeon® E7-8890 v3<br /> 36 núcleos de CPU e 72 threads de CPU |  1,5 TB |  6 TB | Não é mais oferecido |
|---| SAP HANA no Azure S144m<br /> – 4 x processadores Intel® Xeon® E7-8890 v3<br /> 72 núcleos de CPU e 144 threads de CPU |  3,0 TB |  12 TB | Não é mais oferecido |
|---| SAP HANA no Azure S192m<br /> – 4 x processadores Intel® Xeon® E7-8890 v4<br /> 96 núcleos de CPU e 192 threads de CPU  |  4,0 TB |  16 TB | Disponível |
|---| SAP HANA no Azure S384m<br /> – 8 x processadores Intel® Xeon® E7-8890 v4<br /> 192 núcleos de CPU e 384 threads de CPU |  6,0 TB |  18 TB | Disponível |
|---| SAP HANA no Azure S384xm<br /> – 8 x processadores Intel® Xeon® E7-8890 v4<br /> 192 núcleos de CPU e 384 threads de CPU |  8,0 TB |  22 TB |  Disponível |
|---| SAP HANA no Azure S576m<br /> – 12 x processadores Intel® Xeon® E7-8890 v4<br /> 288 núcleos de CPU e 576 threads de CPU |  12,0 TB |  28 TB | Disponível |
|---| SAP HANA no Azure S768m<br /> – 16 x processadores Intel® Xeon® E7-8890 v4<br /> 384 núcleos de CPU e 768 threads de CPU |  16,0 TB |  36 TB | Disponível |
|---| SAP HANA no Azure S960m<br /> – 20 x processadores Intel® Xeon® E7-8890 v4<br /> 480 núcleos de CPU e 960 threads de CPU |  20,0 TB |  46 TB | Disponível |


No SAP HANA TDIv5, a SAP permite dimensionamentos específicos do cliente e projetos específicos do cliente, o que pode levar a configurações do servidor que não estão listadas como certificadas em:

- [Appliances certificados SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/appliances.html)
- [Plataformas IaaS certificadas pelo SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)

Em muitos casos, essas configurações de servidor específicas do cliente carregam mais memória do que as unidades de servidor certificadas com o SAP. Ao trabalhar com a SAP, os clientes têm a possibilidade de obter suporte SAP e certificar suas configurações de servidor de tamanho específico do cliente. No Azure, os SKUs padrão do HANA Large Instance a seguir estão disponíveis e na lista de preços da Microsoft para esses projetos de dimensionamento específicos ao cliente do TDIv5.

| SKU|CPU | Memória | Armazenamento | Disponibilidade |
| ---| --- | --- | --- | --- |
| S96 | SAP HANA no Azure S96<br /> – 2 x processadores Intel® Xeon® E7-8890 v4<br /> 48 núcleos de CPU e 96 threads de CPU |  768 GB |  3 TB | Disponível |


| SKU original que pode ser <br /> estendido na memória | CPU | Memória | Armazenamento | Disponibilidade |
| --- | --- | --- | --- | --- |
| S192m pode ser estendido para | SAP HANA no Azure S192xm<br /> – 4 x processadores Intel® Xeon® E7-8890 v4<br /> 96 núcleos de CPU e 192 threads de CPU |  6,0 TB |  16 TB | Disponível |
| S384xm pode ser estendido para | SAP HANA no Azure S384xxm<br /> – 8 x processadores Intel® Xeon® E7-8890 v4<br /> 192 núcleos de CPU e 384 threads de CPU |  12,0 TB |  28 TB | Disponível |
| S576m pode ser estendido para | SAP HANA no Azure S576xm<br /> – 12 x processadores Intel® Xeon® E7-8890 v4<br /> 288 núcleos de CPU e 576 threads de CPU |  18,0 TB |  41 TB | Disponível |
| S768m pode ser estendido para | SAP HANA no Azure S768xm<br /> – 16 x processadores Intel® Xeon® E7-8890 v4<br /> 384 núcleos de CPU e 768 threads de CPU |  24,0 TB |  56 TB | Disponível |

- Núcleos de CPU = soma de núcleos de CPU não-hyper-threading da soma dos processadores da unidade do servidor.
- Threads de CPU = soma de threads de computação fornecidos pelos núcleos de CPU não-hyper-threading da soma dos processadores da unidade do servidor. A maioria das unidades é configurada por padrão para usar a tecnologia Hyper-Threading.
- Com base nas recomendações do fornecedor, o S768m, o S768xm e o S960m não estão configurados para usar o Hyper-Threading para executar o SAP HANA.


As configurações específicas de escolhido dependem da carga de trabalho, recursos de CPU e memória desejada. É possível que a carga de trabalho de OLTP use as SKUs otimizadas para a carga de trabalho de OLAP. 

A base de hardware para as ofertas, exceto unidades para projetos de dimensionamento específicos do cliente, é certificada pelo SAP HANA TDI. Duas classes diferentes de hardware dividem as SKUs em:

- S72, S72m, S96, S144, S144m, S192, S192m e S192m, que são chamados de "Classe Tipo I" de SKUs.
- S384, S384m, S384xm, S384xxm, S576m, S576xm S768m, S768xm e S960m, que são chamados de "Classe Tipo II" de SKUs.

Um carimbo do SAP HANA em Instâncias Grandes completo não é alocado exclusivamente para uso de um único cliente. Esse fato se aplica aos racks de recursos de computação e armazenamento conectados por meio de uma malha de rede implantada no Azure também. A infraestrutura do SAP HANA em Instâncias Grandes, como o Azure, implanta &quot;locatários&quot; diferentes do cliente isolados uns dos outros nos três níveis a seguir:

- **Rede**: Isolamento através de redes virtuais dentro do carimbo da Instância do SAP HANA Grande.
- **Armazenamento**: Isolamento por meio de máquinas virtuais de armazenamento que têm volumes de armazenamento atribuídos e isolam volumes de armazenamento entre locatários.
- **Computação**: Atribuição dedicada de unidades de servidor para um único locatário. Nenhum particionamento rígido ou flexível de unidades de servidor. Nenhum compartilhamento de uma única unidade de host ou de servidor entre locatários. 

As implantações de unidades do SAP HANA em Instâncias Grandes entre locatários diferentes não são visíveis entre si. As unidades do SAP HANA em Instâncias Grandes implantadas em diferentes locatários não podem comunicar-se diretamente entre si no nível de carimbo do SAP HANA em Instâncias Grandes. Somente as unidades do SAP HANA em Instâncias Grandes em um locatário podem comunicar-se entre si no nível de carimbo do SAP HANA em Instâncias Grandes.

Um locatário implantado no carimbo da Instância Grande é atribuído a uma assinatura do Azure para fins de cobrança. Para uma rede, é possível acessar a partir de redes virtuais de outras assinaturas do Azure dentro da mesma inscrição no Azure. Se você implantar com outra assinatura do Azure na mesma região do Azure, você também poderá escolher solicitar um locatário de Instância Grande do HANA separado.

Há diferenças significativas entre executar o SAP HANA no SAP HANA em Instâncias Grandes e no SAP HANA em execução nas VMs implantadas no Azure:

- Não há nenhuma camada de virtualização para o SAP HANA no Azure (Instâncias Grandes). Você obtém o desempenho do hardware bare-metal subjacente.
- Ao contrário do Azure, o servidor do servidor do SAP HANA no Azure (Instâncias Grandes) é dedicado a um cliente específico. Não há possibilidade de que uma unidade de servidor ou host seja particionada de maneira rígida ou flexível. Como resultado, uma unidade do SAP HANA em Instâncias Grandes é usada conforme atribuída como um todo para um locatário e, com isso, para você. Uma reinicialização ou desligamento do servidor não leva automaticamente para a implantação do sistema operacional e do SAP HANA em outro servidor. (Para SKUs de classe Tipo I, a única exceção é se um servidor encontrar problemas e a necessidade de reimplantação precisar ser executada em outro servidor.)
- Ao contrário do Azure, em que tipos de host do processador são selecionados para melhor taxa de preço e desempenho, os tipos de processador escolhidos para o SAP HANA no Azure (Instâncias Grandes) têm o melhor desempenho da linha de processadores Intel E7v3 e E7v4.

**Próximas etapas**
- Veja [Dimensionamento HLI](hana-sizing.md)

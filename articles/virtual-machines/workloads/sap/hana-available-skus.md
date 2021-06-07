---
title: SKUs para SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs
description: SKUs para SAP HANA no Azure (Instâncias Grandes).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
keywords: HLI, HANA, SKUs, S896, S224, S448, S672, Optane, SAP
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/21/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 25a11bf96ba680608e5bb22835becf80fadee4f3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101668935"
---
# <a name="available-skus-for-hana-large-instances"></a>SKUs disponíveis para instâncias grandes do HANA

SAP HANA no serviço do Azure (instâncias grandes) com base apenas em carimbos de revisão 3, está disponível em várias configurações nas regiões do Azure de:

- Leste da Austrália
- Sudeste da Austrália
- Leste do Japão
- Oeste do Japão

SAP HANA no serviço do Azure (instâncias grandes) com base na revisão 4 carimbos está disponível em várias configurações nas regiões do Azure de:

- Oeste dos EUA 2
- Leste dos EUA

Serviço de infraestrutura BareMetal (certificado para cargas de trabalho SAP HANA) com base em carimbos de revisão 4,2. Ele está disponível em várias configurações nas regiões do Azure de:
- Europa Ocidental
- Norte da Europa
- Leste dos EUA 2
- Centro-Sul dos Estados Unidos




A lista de instâncias grandes do Azure disponíveis que são listas oferecidas como a seguir.

> [!IMPORTANT]
> Lembre-se da primeira coluna que representa o status da certificação do HANA para cada um dos tipos de instância grandes na lista. A coluna deve correlacionar-se com o [SAP Hana diretório de hardware](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) para os SKUs do Azure que começam com a letra **S**



| Certificado SAP HANA | Modelo | Memória Total | DRAM de memória | Optane de memória | Armazenamento | Disponibilidade |
| --- | --- | --- | --- | --- | --- | --- |
| YES <br />[OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2185), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2265) | SAP HANA no Azure S96<br /> – 2 x Intel® Xeon® Processador E7-8890 v4 <br /> 48 núcleos de CPU e 96 threads de CPU |  768 GB | 768 GB | --- | 3,0 TB | Disponível |
| YES <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2186), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2269) | SAP HANA no Azure S224<br /> – 4 x Intel® Xeon® Processador Platinum 8276 <br /> 112 núcleos de CPU e 224 threads de CPU |  3,0 TB | 3,0 TB | --- | 6,3 TB | Disponível |
| YES <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2297) | SAP HANA no Azure S224m<br /> – 4 x Intel® Xeon® Processador Platinum 8276 <br /> 112 núcleos de CPU e 224 threads de CPU |  6,0 TB | 6,0 TB | --- | 10,5 TB | Disponível |
| YES <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2381) | SAP HANA no Azure S224om<br /> – 4 x Intel® Xeon® Processador Platinum 8276 <br /> 112 núcleos de CPU e 224 threads de CPU | 6,0 TB |  3,0 TB |  3,0 TB | 10,5 TB | Disponível |
| Não | SAP HANA no Azure S224oo<br /> – 4 x Intel® Xeon® Processador Platinum 8276 <br /> 112 núcleos de CPU e 224 threads de CPU | 4,5 TB |  1,5 TB |  3,0 TB | 8,4 TB | Disponível |
| Não | SAP HANA no Azure S224ooo<br /> – 4 x Intel® Xeon® Processador Platinum 8276 <br /> 112 núcleos de CPU e 224 threads de CPU | 7,5 TB |  1,5 TB |  6,0 TB | 12,7 TB | Disponível |
| Não | SAP HANA no Azure S224oom<br /> – 4 x Intel® Xeon® Processador Platinum 8276 <br /> 112 núcleos de CPU e 224 threads de CPU | 9,0 TB |  3,0 TB |  6,0 TB | 14,8 TB | Disponível |
| YES <br />[OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1983), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2268) | SAP HANA no Azure S384<br /> – 8 x processadores Intel® Xeon® E7-8890 v4<br /> 192 núcleos de CPU e 384 threads de CPU |  4,0 TB | 4,0 TB | --- | 16 TB | Disponível |
| YES <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2080) | SAP HANA no Azure S384m<br /> – 8 x processadores Intel® Xeon® E7-8890 v4<br /> 192 núcleos de CPU e 384 threads de CPU |  6,0 TB | 6,0 TB | --- | 18 TB |  Disponível  |
| YES <br />[OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1984), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2267) | SAP HANA no Azure S384xm<br /> – 8 x processadores Intel® Xeon® E7-8890 v4<br /> 192 núcleos de CPU e 384 threads de CPU |  8,0 TB | 8,0 TB | --- | 22 TB | Disponível |
| YES <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2411), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2378) | SAP HANA no Azure S448<br /> – 8 x Intel® Xeon® Processador Platinum 8276 <br /> 224 núcleos de CPU e 448 threads de CPU | 6,0 TB |  6,0 TB |  --- | 10,5 TB | Disponível (somente Rev. 4) |
| YES <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2410), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2377) | SAP HANA no Azure S448m<br /> – 8 x Intel® Xeon® Processador Platinum 8276 <br /> 224 núcleos de CPU e 448 threads de CPU | 12,0 TB |  12,0 TB |  --- | 18,9 TB | Disponível (somente Rev. 4) |
| Não | SAP HANA no Azure S448oo<br /> – 8 x Intel® Xeon® Processador Platinum 8276 <br /> 224 núcleos de CPU e 448 threads de CPU | 9,0 TB |  3,0 TB |  6,0 TB | 14,8 TB  | Disponível (somente Rev. 4) |
| Não | SAP HANA no Azure S448om<br /> – 8 x Intel® Xeon® Processador Platinum 8276 <br /> 224 núcleos de CPU e 448 threads de CPU | 12,0 TB |  6,0 TB |  6,0 TB | 18,9 TB  | Disponível (somente Rev. 4) |
| Não | SAP HANA no Azure S448ooo<br /> – 8 x Intel® Xeon® Processador Platinum 8276 <br /> 224 núcleos de CPU e 448 threads de CPU | 15,0 TB |  3,0 TB |  12,0 TB | 23,2 TB  | Disponível (somente Rev. 4) |
| Não | SAP HANA no Azure S448oom<br /> – 8 x Intel® Xeon® Processador Platinum 8276 <br /> 224 núcleos de CPU e 448 threads de CPU | 18,0 TB |  6,0 TB |  12,0 TB | 27,4 TB  | Disponível (somente Rev. 4) |
| YES <br /> [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2049) | SAP HANA no Azure S576m<br /> – 12 x processadores Intel® Xeon® E7-8890 v4<br /> 288 núcleos de CPU e 576 threads de CPU |  12,0 TB | 12,0 TB | --- | 28 TB | Disponível (somente Rev. 4) |
| Não | SAP HANA no Azure S576xm<br /> – 12 x processadores Intel® Xeon® E7-8890 v4<br /> 288 núcleos de CPU e 576 threads de CPU |  18,0 TB | 18.0 | --- |  41 TB | Disponível |
| YES <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2409), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2380) | SAP HANA no Azure S672<br /> – 12 x Intel® Xeon® Processador Platinum 8276 <br /> 336 núcleos de CPU e 672 threads de CPU | 9,0 TB |  9,0 TB |  --- | 14,7 TB | Disponível (somente Rev. 4) |
| YES <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2408), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2379) | SAP HANA no Azure S672m<br /> – 12 x Intel® Xeon® Processador Platinum 8276 <br /> 336 núcleos de CPU e 672 threads de CPU | 18,0 TB |  18,0 TB |  --- | 27,4 TB | Disponível (somente Rev. 4) |
| Não | SAP HANA no Azure S672oo<br /> – 12 x Intel® Xeon® Processador Platinum 8276 <br /> 336 núcleos de CPU e 672 threads de CPU | 13,5 TB |  4,5 TB |  9,0 TB | 21,1 TB  | Disponível (somente Rev. 4) |
| Não | SAP HANA no Azure S672om<br /> – 12 x Intel® Xeon® Processador Platinum 8276 <br /> 336 núcleos de CPU e 672 threads de CPU | 18,0 TB |  9,0 TB |  9,0 TB | 27,4 TB  | Disponível (somente Rev. 4) |
| Não | SAP HANA no Azure S672ooo<br /> – 12 x Intel® Xeon® Processador Platinum 8276 <br /> 336 núcleos de CPU e 672 threads de CPU | 22,5 TB |  4,5 TB |  18,0 TB | 33,7 TB  | Disponível (somente Rev. 4) |
| Não | SAP HANA no Azure S672oom<br /> – 12 x Intel® Xeon® Processador Platinum 8276 <br /> 336 núcleos de CPU e 672 threads de CPU | 27,0 TB |  9,0 TB |  18,0 TB | 40,0 TB  | Disponível (somente Rev. 4) |
| YES <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1985) | SAP HANA no Azure S768m<br /> – 16 x processadores Intel® Xeon® E7-8890 v4<br /> 384 núcleos de CPU e 768 threads de CPU |  16,0 TB | 16,0 TB | -- | 36 TB | Disponível |
| Não | SAP HANA no Azure S768xm<br /> – 16 x processadores Intel® Xeon® E7-8890 v4<br /> 384 núcleos de CPU e 768 threads de CPU |  24,0 TB | 24,0 TB | --- | 56 TB | Disponível |
|  YES <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2407), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2376)  | SAP HANA no Azure S896<br /> – 16 x Intel® Xeon® Processador Platinum 8276 <br /> 448 núcleos de CPU e 896 threads de CPU | 12,0 TB |  12,0 TB |  --- | 18,9 TB | Disponível (somente Rev. 4) |
| YES <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2406), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2328) | SAP HANA no Azure S896m<br /> – 16 x Intel® Xeon® Processador Platinum 8276 <br /> 448 núcleos de CPU e 896 threads de CPU | 24,0 TB | 24,0 TB | -- | 35,8 TB | Disponível |
| Não | SAP HANA no Azure S896oo<br /> – 16 x Intel® Xeon® Processador Platinum 8276 <br /> 448 núcleos de CPU e 896 threads de CPU | 18,0 TB |  6,0 TB |  12,0 TB | 27,4 TB  | Disponível (somente Rev. 4) |
| Não | SAP HANA no Azure S896om<br /> – 16 x Intel® Xeon® Processador Platinum 8276 <br /> 448 núcleos de CPU e 896 threads de CPU | 24,0 TB |  12,0 TB |  12,0 TB | 35,8 TB  | Disponível (somente Rev. 4) |
| Não | SAP HANA no Azure S896ooo<br /> – 16 x Intel® Xeon® Processador Platinum 8276 <br /> 448 núcleos de CPU e 896 threads de CPU | 30,0 TB |  6,0 TB |  24,0 TB | 44,3 TB  | Disponível (somente Rev. 4) |
| Não | SAP HANA no Azure S896oom<br /> – 16 x Intel® Xeon® Processador Platinum 8276 <br /> 448 núcleos de CPU e 896 threads de CPU | 36,0 TB |  12,0 TB |  24,0 TB | 52,7 TB  | Disponível (somente Rev. 4) |
| YES <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1986) | SAP HANA no Azure S960m<br /> – 20 x processadores Intel® Xeon® E7-8890 v4<br /> 480 núcleos de CPU e 960 threads de CPU |  20,0 TB | 20,0 TB | -- | 46 TB | Disponível (somente Rev. 4) |


- Núcleos de CPU = soma de núcleos de CPU não-hyper-threading da soma dos processadores da unidade do servidor.
- Threads de CPU = soma de threads de computação fornecidos pelos núcleos de CPU não-hyper-threading da soma dos processadores da unidade do servidor. A maioria das unidades é configurada por padrão para usar a tecnologia Hyper-Threading.
- Com base nas recomendações do fornecedor, o S768m, o S768xm e o S960m não estão configurados para usar o Hyper-Threading para executar o SAP HANA.


> [!IMPORTANT]
> As SKUs a seguir, embora ainda não tenham suporte, não podem ser mais adquiridas: s72, S72m, S144, S144m, S192 e S192m 

As configurações específicas de escolhido dependem da carga de trabalho, recursos de CPU e memória desejada. É possível que a carga de trabalho de OLTP use as SKUs otimizadas para a carga de trabalho de OLAP. 

Duas classes diferentes de hardware dividem as SKUs em:

- S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224 e S224m, S224oo, S224om, S224ooo, S224oom são chamados de "classe Type I" de SKUs.
- Todas as outras SKUs são referidas como a "classe do tipo II" de SKUs.
- Se você estiver interessado em SKUs que ainda não estão listados no diretório de hardware do SAP, entre em contato com sua equipe de conta Microsoft para obter mais informações. 


Um carimbo do SAP HANA em Instâncias Grandes completo não é alocado exclusivamente para uso de um único cliente. Esse fato se aplica aos racks de recursos de computação e armazenamento conectados por meio de uma malha de rede implantada no Azure também. A infraestrutura do SAP HANA em Instâncias Grandes, como o Azure, implanta &quot;locatários&quot; diferentes do cliente isolados uns dos outros nos três níveis a seguir:

- **Rede**: isolamento através de redes virtuais dentro do carimbo do SAP HANA em Instâncias Grandes.
- **Armazenamento**: isolamento por meio de máquinas virtuais de armazenamento que têm volumes de armazenamento atribuídos e isolam volumes de armazenamento entre locatários.
- **Computação**: atribuição dedicada de unidades de servidor para um único locatário. Nenhum particionamento rígido ou flexível de unidades de servidor. Nenhum compartilhamento de uma única unidade de host ou de servidor entre locatários. 

As implantações de unidades do SAP HANA em Instâncias Grandes entre locatários diferentes não são visíveis entre si. As unidades do SAP HANA em Instâncias Grandes implantadas em diferentes locatários não podem comunicar-se diretamente entre si no nível de carimbo do SAP HANA em Instâncias Grandes. Somente as unidades do SAP HANA em Instâncias Grandes em um locatário podem comunicar-se entre si no nível de carimbo do SAP HANA em Instâncias Grandes.

Um locatário implantado no carimbo da Instância Grande é atribuído a uma assinatura do Azure para fins de cobrança. Para uma rede, é possível acessar a partir de redes virtuais de outras assinaturas do Azure dentro da mesma inscrição no Azure. Se você implantar com outra assinatura do Azure na mesma região do Azure, você também poderá escolher solicitar um locatário de Instância Grande do HANA separado.

Há diferenças significativas entre executar o SAP HANA no SAP HANA em Instâncias Grandes e no SAP HANA em execução nas VMs implantadas no Azure:

- Não há nenhuma camada de virtualização para o SAP HANA no Azure (Instâncias Grandes). Você obtém o desempenho do hardware bare-metal subjacente.
- Ao contrário do Azure, o servidor do servidor do SAP HANA no Azure (Instâncias Grandes) é dedicado a um cliente específico. Não há possibilidade de que uma unidade de servidor ou host seja particionada de maneira rígida ou flexível. Como resultado, uma unidade do SAP HANA em Instâncias Grandes é usada conforme atribuída como um todo para um locatário e, com isso, para você. Uma reinicialização ou desligamento do servidor não leva automaticamente para a implantação do sistema operacional e do SAP HANA em outro servidor. (Para SKUs de classe Tipo I, a única exceção é se um servidor encontrar problemas e a necessidade de reimplantação precisar ser executada em outro servidor.)
- Ao contrário do Azure, em que tipos de host do processador são selecionados para melhor taxa de preço e desempenho, os tipos de processador escolhidos para o SAP HANA no Azure (Instâncias Grandes) têm o melhor desempenho da linha de processadores Intel E7v3 e E7v4.

**Próximas etapas**
- Veja [Dimensionamento HLI](hana-sizing.md)

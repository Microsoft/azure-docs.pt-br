---
title: Red Hat Enterprise Linux imagens disponíveis no Azure
description: Saiba mais sobre imagens do Red Hat Enterprise Linux no Microsoft Azure
author: asinn826
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.openlocfilehash: 0a4d2ef5b7f367130151fabda3f1d97b65605931
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101676035"
---
# <a name="red-hat-enterprise-linux-rhel-images-available-in-azure"></a>Red Hat Enterprise Linux (RHEL) imagens disponíveis no Azure
O Azure oferece uma variedade de imagens RHEL para diferentes casos de uso.

> [!NOTE]
> Todas as imagens do RHEL estão disponíveis nas nuvens públicas do Azure e do Azure governamental. Eles não estão disponíveis nas nuvens do Azure na China.

## <a name="list-of-rhel-images"></a>Lista de imagens RHEL
Esta é uma lista de imagens RHEL disponíveis no Azure. Salvo indicação em contrário, todas as imagens são particionadas por LVM e anexadas a repositórios RHEL regulares (não EUS, não E4S). As seguintes imagens estão disponíveis no momento para uso geral:

> [!NOTE]
> As imagens BRUTAs não são mais produzidas em favor das imagens de particionamento LVM. O LVM fornece várias vantagens em relação ao esquema de particionamento bruto (não LVM) mais antigo, incluindo opções de redimensionamento de partição significativamente mais flexíveis.

Oferta| SKU | Particionamento | Provisionamento | Observações
:----|:----|:-------------|:-------------|:-----
RHEL          | 6.7      | RAW    | Agente do Linux | Suporte estendido ao ciclo de vida disponível de 1º de dezembro. [Mais detalhes aqui.](redhat-extended-lifecycle-support.md)
|             | 6,8      | RAW    | Agente do Linux | Suporte estendido ao ciclo de vida disponível de 1º de dezembro. [Mais detalhes aqui.](redhat-extended-lifecycle-support.md)
|             | 6.9      | RAW    | Agente do Linux | Suporte estendido ao ciclo de vida disponível de 1º de dezembro. [Mais detalhes aqui.](redhat-extended-lifecycle-support.md)
|             | 6.10     | RAW    | Agente do Linux | Suporte estendido ao ciclo de vida disponível de 1º de dezembro. [Mais detalhes aqui.](redhat-extended-lifecycle-support.md)
|             | 7-RAW    | RAW    | Agente do Linux | Família de imagens RHEL 7. x. <br> Anexado a repositórios regulares por padrão (não EUS).
|             | 7-LVM    | LVM    | Agente do Linux | Família de imagens RHEL 7. x. <br> Anexado a repositórios regulares por padrão (não EUS). Se você estiver procurando uma imagem RHEL padrão a ser implantada, use esse conjunto de imagens e/ou sua contraparte de geração 2.
|             | 7lvm-gen2| LVM    | Agente do Linux | Família de imagens da geração 2, RHEL 7. x. <br> Anexado a repositórios regulares por padrão (não EUS). Se você estiver procurando uma imagem RHEL padrão a ser implantada, use esse conjunto de imagens e/ou sua contraparte de geração 1.
|             | 7-RAW-CI | RAW-CI | inicialização de nuvem  | Família de imagens RHEL 7. x. <br> Anexado a repositórios regulares por padrão (não EUS).
|             | 7.2      | RAW    | Agente do Linux |
|             | 7.3      | RAW    | Agente do Linux |
|             | 7.4      | RAW    | Agente do Linux | Anexado a repositórios do EUS por padrão a partir de abril de 2019.
|             | 74-Gen2  | RAW    | Agente do Linux | Anexado a repositórios do EUS por padrão.
|             | 7.5      | RAW    | Agente do Linux | Anexado a repositórios do EUS por padrão a partir de junho de 2019.
|             | 75-Gen2  | RAW    | Agente do Linux | Anexado a repositórios do EUS por padrão.
|             | 7.6      | RAW    | Agente do Linux | Anexado a repositórios do EUS por padrão a partir de maio de 2019.
|             | 76-Gen2  | RAW    | Agente do Linux | Anexado a repositórios do EUS por padrão.
|             | 7.7      | LVM    | Agente do Linux | Anexado a repositórios do EUS por padrão.
|             | 77-gen2  | LVM    | Agente do Linux | Anexado a repositórios do EUS por padrão.
|             | 7.8      | LVM    | Agente do Linux | Anexado a repositórios regulares (EUS não disponível para RHEL 7,8)
|             | 78-Gen2  | LVM    | Agente do Linux | Anexado a repositórios regulares (EUS não disponível para RHEL 7,8)
|             | 7.9      | LVM    | Agente do Linux | Anexado a repositórios regulares (EUS não disponível para RHEL 7,9)
|             | 79-Gen2  | LVM    | Agente do Linux | Anexado a repositórios regulares (EUS não disponível para RHEL 7,9)
|             | 8-LVM    | LVM    | Agente do Linux | Família de imagens RHEL 8. x. Anexado a repositórios regulares.
|             | 8-LVM-Gen2| LVM    | Agente do Linux | Hyper-V geração 2-família de imagens RHEL 8. x. Anexado a repositórios regulares.
|             | 8        | LVM    | Agente do Linux | Imagens RHEL 8,0.
|             | 8-Gen2   | LVM    | Agente do Linux | Hyper-V geração 2-RHEL 8,0 imagens.
|             | 8.1      | LVM    | Agente do Linux | Anexado a repositórios do EUS por padrão.
|             | 81gen2   | LVM    | Agente do Linux | Hyper-V geração 2-anexado a repositórios do EUS a partir de novembro de 2020.
|             | 8.1-ci   | LVM    | Agente do Linux | Anexado a repositórios do EUS a partir de novembro de 2020.
|             | 81-ci-gen2| LVM    | Agente do Linux | Hyper-V geração 2-anexado a repositórios do EUS a partir de novembro de 2020.
|             | 8.2      | LVM    | Agente do Linux | Anexado a repositórios do EUS a partir de novembro de 2020.
|             | 82gen2   | LVM    | Agente do Linux | Hyper-V geração 2-anexado a repositórios do EUS a partir de novembro de 2020.
|             | 8.3   | LVM    | Agente do Linux |  Anexado a repositórios regulares (EUS não disponível para RHEL 8,3)
|             | 83-Gen2   | LVM    | Agente do Linux |Hyper-V geração 2-anexado a repositórios regulares (EUS não disponível para RHEL 8,3)
RHEL-SAP      | 7.4      | LVM    | Agente do Linux | RHEL 7,4 para aplicativos de SAP HANA e de negócios. Anexados aos repositórios do E4S, o cobrará um Premium para SAP e RHEL, bem como a taxa de computação base.
|             | 74sap-Gen2| LVM    | Agente do Linux | RHEL 7,4 para aplicativos de SAP HANA e de negócios. Imagem de geração 2. Anexados aos repositórios do E4S, o cobrará um Premium para SAP e RHEL, bem como a taxa de computação base.
|             | 7.5       | LVM    | Agente do Linux | RHEL 7,5 para aplicativos de SAP HANA e de negócios. Anexados aos repositórios do E4S, o cobrará um Premium para SAP e RHEL, bem como a taxa de computação base.
|             | 75sap-Gen2| LVM    | Agente do Linux | RHEL 7,5 para aplicativos de SAP HANA e de negócios. Imagem de geração 2. Anexados aos repositórios do E4S, o cobrará um Premium para SAP e RHEL, bem como a taxa de computação base.
|             | 7.6       | LVM    | Agente do Linux | RHEL 7,6 para aplicativos de SAP HANA e de negócios. Anexados aos repositórios do E4S, o cobrará um Premium para SAP e RHEL, bem como a taxa de computação base.
|             | 76sap-Gen2| LVM    | Agente do Linux | RHEL 7,6 para aplicativos de SAP HANA e de negócios. Imagem de geração 2. Anexados aos repositórios do E4S, o cobrará um Premium para SAP e RHEL, bem como a taxa de computação base.
|             | 7.7       | LVM    | Agente do Linux | RHEL 7,7 para aplicativos de SAP HANA e de negócios. Anexados aos repositórios do E4S, o cobrará um Premium para SAP e RHEL, bem como a taxa de computação base.
RHEL-SAP-HANA (a ser removido em novembro de 2020) | 6.7       | RAW    | Agente do Linux | RHEL 6,7 para SAP HANA. Desatualizado em favor das imagens RHEL-SAP. Esta imagem será removida em novembro de 2020. Mais detalhes sobre as ofertas de nuvem do SAP do Red Hat estão disponíveis [aqui](https://access.redhat.com/articles/3751271).
|             | 7.2       | LVM    | Agente do Linux | RHEL 7,2 para SAP HANA. Desatualizado em favor das imagens RHEL-SAP. Esta imagem será removida em novembro de 2020. Mais detalhes sobre as ofertas de nuvem do SAP do Red Hat estão disponíveis [aqui](https://access.redhat.com/articles/3751271).
|             | 7.3       | LVM    | Agente do Linux | RHEL 7,3 para SAP HANA. Desatualizado em favor das imagens RHEL-SAP. Esta imagem será removida em novembro de 2020. Mais detalhes sobre as ofertas de nuvem do SAP do Red Hat estão disponíveis [aqui](https://access.redhat.com/articles/3751271).
RHEL-SAP-APPS | 6,8       | RAW    | Agente do Linux | RHEL 6,8 para SAP Business Applications. Desatualizado em favor das imagens RHEL-SAP.
|             | 7.3       | LVM    | Agente do Linux | RHEL 7,3 para SAP Business Applications. Desatualizado em favor das imagens RHEL-SAP.
|             | 7.4       | LVM    | Agente do Linux | RHEL 7,4 para SAP Business Applications.
|             | 7.6       | LVM    | Agente do Linux | RHEL 7,6 para SAP Business Applications.
|             | 7.7       | LVM    | Agente do Linux | RHEL 7,7 para SAP Business Applications.
|             | 77-gen2       | LVM    | Agente do Linux | RHEL 7,7 para SAP Business Applications. Imagem de geração 2
|             | 8.1       | LVM    | Agente do Linux | RHEL 8,1 para SAP Business Applications.
|             | 81-Gen2      | LVM    | Agente do Linux | RHEL 8,1 para SAP Business Applications. Imagem de geração 2.
|             | 8.2       | LVM    | Agente do Linux | RHEL 8,2 para SAP Business Applications.
|             | 82-Gen2      | LVM    | Agente do Linux | RHEL 8,2 para SAP Business Applications. Imagem de geração 2.
RHEL-HA       | 7.4       | LVM    | Agente do Linux | RHEL 7,4 com complemento de alta disponibilidade. Cobrará uma Premium para HA e RHEL sobre a taxa de computação base. Desatualizado em favor das imagens RHEL-SAP-HA.
|             | 7.5       | LVM    | Agente do Linux | RHEL 7,5 com complemento de alta disponibilidade. Cobrará uma Premium para HA e RHEL sobre a taxa de computação base. Desatualizado em favor das imagens RHEL-SAP-HA.
|             | 7.6       | LVM    | Agente do Linux | RHEL 7,6 com complemento de alta disponibilidade. Cobrará uma Premium para HA e RHEL sobre a taxa de computação base. Desatualizado em favor das imagens RHEL-SAP-HA.
RHEL-SAP-HA   | 7.4          | LVM    | Agente do Linux | RHEL 7,4 para SAP com HA e serviços de atualização. Anexado a repositórios do E4S. Cobrará um prêmio Premium para repositórios SAP e HA, bem como RHEL, sobre as taxas de computação base.
|             | 74sapha-Gen2 | LVM    | Agente do Linux | RHEL 7,4 para SAP com HA e serviços de atualização. Imagem de geração 2. Anexado a repositórios do E4S. Cobrará um prêmio Premium para repositórios SAP e HA, bem como RHEL, sobre as taxas de computação base.
|             | 7.5          | LVM    | Agente do Linux | RHEL 7,5 para SAP com HA e serviços de atualização. Anexado a repositórios do E4S. Cobrará um prêmio Premium para repositórios SAP e HA, bem como RHEL, sobre as taxas de computação base.
|             | 7.6          | LVM    | Agente do Linux | RHEL 7,6 para SAP com HA e serviços de atualização. Anexado a repositórios do E4S. Cobrará um prêmio Premium para repositórios SAP e HA, bem como RHEL, sobre as taxas de computação base.
|             | 76sapha-Gen2 | LVM    | Agente do Linux | RHEL 7,6 para SAP com HA e serviços de atualização. Imagem de geração 2. Anexado a repositórios do E4S. Cobrará um prêmio Premium para repositórios SAP e HA, bem como RHEL, sobre as taxas de computação base.
|             | 7.7          | LVM    | Agente do Linux | RHEL 7,7 para SAP com HA e serviços de atualização. Anexado a repositórios do E4S. Cobrará um prêmio Premium para repositórios SAP e HA, bem como RHEL, sobre as taxas de computação base.
|             | 77sapha-Gen2 | LVM    | Agente do Linux | RHEL 7,7 para SAP com HA e serviços de atualização. Imagem de geração 2. Anexado a repositórios do E4S. Cobrará um prêmio Premium para repositórios SAP e HA, bem como RHEL, sobre as taxas de computação base.
|             | 8.1          | LVM    | Agente do Linux | RHEL 8,1 para SAP com HA e serviços de atualização. Anexado a repositórios do E4S. Cobrará um prêmio Premium para repositórios SAP e HA, bem como RHEL, sobre as taxas de computação base.
|             | 81sapha-Gen2          | LVM    | Agente do Linux | RHEL 8,1 para SAP com HA e serviços de atualização. Imagens de geração 2 anexadas a repositórios E4S. Cobrará um prêmio Premium para repositórios SAP e HA, bem como RHEL, sobre as taxas de computação base.
|             | 8.2          | LVM    | Agente do Linux | RHEL 8,2 para SAP com HA e serviços de atualização. Cobrará um prêmio Premium para repositórios SAP e HA, bem como RHEL, sobre as taxas de computação base.
|             | 82sapha-Gen2          | LVM    | Agente do Linux | RHEL 8,2 para SAP com HA e serviços de atualização. Imagens de geração 2 anexadas a repositórios E4S. Cobrará um prêmio Premium para repositórios SAP e HA, bem como RHEL, sobre as taxas de computação base.
rhel-byos     |RHEL-lvm74| LVM    | Agente do Linux | As imagens RHEL 7,4 BYOS, não conectadas a nenhuma fonte de atualizações, não cobrarão um RHEL Premium.
|             |RHEL-lvm75| LVM    | Agente do Linux | As imagens RHEL 7,5 BYOS, não conectadas a nenhuma fonte de atualizações, não cobrarão um RHEL Premium.
|             |RHEL-lvm76| LVM    | Agente do Linux | As imagens RHEL 7,6 BYOS, não conectadas a nenhuma fonte de atualizações, não cobrarão um RHEL Premium.
|             |RHEL-lvm76-Gen2| LVM    | Agente do Linux | As imagens RHEL 7,6 geração 2 BYOS, não anexadas a nenhuma fonte de atualizações, não cobrarão um RHEL Premium.
|             |rhel-lvm77| LVM    | Agente do Linux | As imagens RHEL 7,7 BYOS, não conectadas a nenhuma fonte de atualizações, não cobrarão um RHEL Premium.
|             |RHEL-lvm77-Gen2| LVM    | Agente do Linux | As imagens RHEL 7,7 geração 2 BYOS, não anexadas a nenhuma fonte de atualizações, não cobrarão um RHEL Premium.
|             |RHEL-lvm78| LVM    | Agente do Linux | As imagens RHEL 7,8 BYOS, não conectadas a nenhuma fonte de atualizações, não cobrarão um RHEL Premium.
|             |RHEL-lvm78-Gen2| LVM    | Agente do Linux | As imagens RHEL 7,8 geração 2 BYOS, não anexadas a nenhuma fonte de atualizações, não cobrarão um RHEL Premium.
|             |RHEL-lvm8 | LVM    | Agente do Linux | As imagens RHEL 8,0 BYOS, não conectadas a nenhuma fonte de atualizações, não cobrarão um RHEL Premium.
|             |RHEL-lvm8-Gen2 | LVM    | Agente do Linux | As imagens RHEL 8,0 geração 2 BYOS, não anexadas a nenhuma fonte de atualizações, não cobrarão um RHEL Premium.
|             |RHEL-lvm81 | LVM    | Agente do Linux | As imagens RHEL 8,1 BYOS, não conectadas a nenhuma fonte de atualizações, não cobrarão um RHEL Premium.
|             |RHEL-lvm81-Gen2 | LVM    | Agente do Linux | As imagens RHEL 8,1 geração 2 BYOS, não anexadas a nenhuma fonte de atualizações, não cobrarão um RHEL Premium.
|             |RHEL-lvm82 | LVM    | Agente do Linux | As imagens RHEL 8,2 BYOS, não conectadas a nenhuma fonte de atualizações, não cobrarão um RHEL Premium.
|             |RHEL-lvm82-Gen2 | LVM    | Agente do Linux | As imagens RHEL 8,2 geração 2 BYOS, não anexadas a nenhuma fonte de atualizações, não cobrarão um RHEL Premium.

> [!NOTE]
> A oferta de produto RHEL-SAP-HANA é considerada fim da vida por Red Hat. As implantações existentes continuarão a funcionar normalmente, mas a Red Hat recomenda que os clientes migrem das imagens RHEL-SAP-HANA para as imagens RHEL-SAP-HA que incluem os repositórios SAP HANA, bem como o complemento de alta disponibilidade. Mais detalhes sobre as ofertas de nuvem do SAP do Red Hat estão disponíveis [aqui](https://access.redhat.com/articles/3751271).

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre as [imagens do Red Hat no Azure](./redhat-images.md).
* Saiba mais sobre a [infraestrutura de atualização do Red Hat](./redhat-rhui.md).
* Saiba mais sobre a [oferta BYOS do RHEL](./byos.md).
* Informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL podem ser encontradas na página [Ciclo de vida do Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).

---
title: "Visão geral de portas de alta disponibilidade no Azure | Microsoft Docs"
description: Saiba mais sobre o balanceamento de carga de portas de alta disponibilidade em um balanceador de carga interno
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: kumud
ms.openlocfilehash: 3e54cb45cf002a183a5b0bd9b3082a235cd825f8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="high-availability-ports-overview-preview"></a>Visão geral de portas de alta disponibilidade (versão prévia)

O SKU Standard do Azure Load Balancer apresenta portas de HA (alta disponibilidade) – uma funcionalidade para distribuir o tráfego de todas as portas e para todos os protocolos com suporte. Ao configurar um balanceador de carga interno, os usuários podem configurar uma regra de Portas HA que pode definir as portas de front-end e de back-end para **0** e o protocolo para **Todos**, permitindo assim que todo o tráfego flua por meio do Load Balancer Interno.

>[!NOTE]
> O recurso de Portas de alta disponibilidade está disponível com o Load Balancer padrão e atualmente na versão prévia. Durante a versão prévia, o recurso pode não ter o mesmo nível de disponibilidade e confiabilidade que os recursos que estão na versão de disponibilidade geral. Para obter mais informações, consulte [Termos de Uso Complementares do Microsoft Azure para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). É necessário inscrever-se para a Versão prévia do Load Balancer Standard para usar portas de alta disponibilidade com recursos do Load Balancer Padrão. Siga as instruções para inscrição na [Versão prévia padrão](https://aka.ms/lbpreview#preview-sign-up) do Load Balancer.

O algoritmo de balanceamento de carga ainda permanece o mesmo e o destino é selecionado com base nas cinco tuplas <endereço IP de origem, porta de origem, endereço IP de destino, porta de destino, protocolo>. No entanto, essa configuração permite que uma única regra de balanceamento de carga processe todo o tráfego disponível e reduz a complexidade da configuração, bem como os limites impostos pelo número máximo de regras de balanceamento de carga que podem ser adicionados.

Um dos cenários críticos que as portas de HA permitem é a implantação de alta disponibilidade dos dispositivos de rede virtual em redes virtuais do Azure. Além disso, outro cenário comum permitido pelas portas de HA é o balanceamento de carga para um intervalo de portas. 

## <a name="why-use-high-ha-ports"></a>Por que usar portas de HA

Os clientes do Azure, para proteger as respectivas cargas de trabalho de vários tipos de ameaças à segurança, dependem de NVAs (dispositivos de rede virtual). Além disso, os NVAs devem ser confiáveis e altamente disponíveis.  

Portas de HA reduzem a complexidade do cenário de HA de NVA e eliminam a necessidade de soluções mais complexas como zookeeper, além de aumentar a confiabilidade graças a opções mais rápidas de expansão e de failover. Você pode agora obter HA de NVA adicionando NVAs no pool de back-end do Load Balancer Interno do Azure e, em seguida, configurar a regra do Load Balancer da porta de HA.

O exemplo a seguir apresenta uma implantação de rede virtual hub e spoke, com os spokes criando túneis a força para o tráfego deles na rede virtual do hub e por meio de NVA, antes de deixar o espaço confiável. Os NVAs estão atrás de um Load Balancer Interno com a configuração de portas de HA, de modo que podem processar todo o tráfego e encaminhar adequadamente. 

![exemplo de portas de HA](./media/load-balancer-ha-ports-overview/nvaha.png)

Figura 1 – rede virtual de hub e spoke com NVAs implantados no modo de HA


## <a name="region-availability"></a>Disponibilidade de região

Atualmente, a série D está disponível nas seguintes regiões:
- Leste dos EUA 2
- Centro dos EUA
- Norte da Europa
- Centro-Oeste dos EUA
- Europa Ocidental
- Sudeste Asiático 

## <a name="preview-sign-up"></a>Inscrição na versão prévia

Para participar da versão prévia do recurso de portas de HA no SKU do Load Balancer Standard, registre sua assinatura para obter acesso usando o PowerShell ou CLI do Azure 2.0.

- Inscrever-se usando o PowerShell

    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```

- Inscrever-se usando a CLI do Azure 2.0

    ```cli
    az feature register --name AllowILBAllPortsRule --namespace Microsoft.Network 
    ```  


>[!NOTE]
>Para usar esse recurso, você também deve se inscrever para a [Versão prévia padrão](https://aka.ms/lbpreview#preview-sign-up) do Load Balancer além das portas de alta disponibilidade. O registro das Portas de alta disponibilidade ou das versões prévias padrão do Load Balancer pode levar até uma hora.

## <a name="limitations"></a>Limitações

A seguir estão as configurações com suporte ou exceções para portas de HA:

- Uma única Configuração de IP de front-end pode ter uma única regra de Load Balancer de DSR com portas de alta disponibilidade (todas as portas) ou então ela pode ter uma única regra de Load Balancer não DSR com portas de alta disponibilidade (todas as portas). Ele não pode ter ambos.
- Uma única configuração de IP de adaptador de rede pode ter apenas uma regra de balanceador de carga não DSR com portas de HA. Nenhuma outra regra pode ser configurada para essa ipconfig.
- Uma única configuração de IP de adaptador de rede pode ter uma ou mais regras de balanceador de carga DSR com portas de HA, desde que todas as respectivas configurações de ip de front-end sejam exclusivas.
- Duas (ou mais) regras de Load Balancer apontando para o mesmo pool de back-end podem coexistir se todas as regras de balanceamento de carga são de porta de HA (somente DSR) ou se todas as regras são de porta não HA (DSR e não DSR). Essas duas regras de balanceamento de carga não podem coexistir se há uma combinação de regras de porta de HA e porta não HA.
- A porta de HA no locatário habilitado para IPv6 não está disponível.


## <a name="next-steps"></a>Próximas etapas

[Configurar portas de HA em um Load Balancer Interno](load-balancer-configure-ha-ports.md)


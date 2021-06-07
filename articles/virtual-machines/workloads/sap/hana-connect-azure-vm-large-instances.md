---
title: Configuração de conectividade de máquinas virtuais para o SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs
description: Configuração de conectividade de máquinas virtuais para usar o SAP HANA no Azure (Instâncias Grandes).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d9caf0a69d2f6caf23edbc35864e1aa560aaf63b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101673016"
---
# <a name="connecting-azure-vms-to-hana-large-instances"></a>Conectar máquinas virtuais do Azure a Grandes Instâncias do HANA

O artigo [O que é SAP HANA no Azure (Instâncias Grandes)?](./hana-overview-architecture.md) menciona que a implantação mínima do HANA em Instâncias Grandes com a camada de aplicativo SAP no Azure é semelhante ao seguinte:

![Rede virtual do Azure conectado ao SAP HANA no Azure (Instâncias Grandes) e local](./media/hana-overview-architecture/image1-architecture.png)

Olhando mais de perto no lado da rede virtual do Azure, há a necessidade de:

- A definição de uma rede virtual do Azure na qual você implantará as VMs da camada de aplicativo SAP.
- A definição de uma sub-rede padrão na rede virtual do Azure que é realmente aquela na qual as VMs são implantadas.
- A rede virtual do Azure que é criada precisa ter pelo menos uma sub-rede da VM e uma sub-rede do gateway de rede virtual do Azure ExpressRoute. Essas sub-redes devem ser atribuídas aos intervalos de endereços IP, conforme especificado e abordado nas próximas seções.


## <a name="create-the-azure-virtual-network-for-hana-large-instances"></a>Criar a rede virtual do Azure para instâncias grandes do HANA

>[!Note]
>A rede virtual do Azure para instâncias grandes do HANA deve ser criada usando o modelo de implantação do Azure Resource Manager. O modelo de implantação mais antigo do Azure, comumente conhecido como modelo de implantação clássico, não é suportado pela solução de Instância Grande do HANA.

Você pode usar o portal do Azure, o PowerShell, um modelo do Azure ou a CLI do Azure para criar a rede virtual. (Para obter mais informações, consulte [criar uma rede virtual usando o portal do Azure](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network)). No exemplo a seguir, analisamos uma rede virtual criada usando o portal do Azure.

Ao fazer referência ao **espaço de endereço** nesta documentação, para o espaço de endereço que a rede virtual do Azure tem permissão para usar. Esse espaço de endereço também é o intervalo de endereços que a rede virtual usa para a propagação de rota do BGP. Esse **espaço de endereço** pode ser visto aqui:

![Espaço de endereço de uma rede virtual do Azure exibido no portal do Azure](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

No exemplo anterior, com 10.16.0.0/16, a rede virtual do Azure recebeu um intervalo de endereços IP bastante grande e largo para usar. Portanto, todos os intervalos de endereços IP de sub-redes subsequentes dentro dessa rede virtual podem ter seus intervalos dentro desse espaço de endereço. Geralmente, não recomendamos um intervalo de endereços tão grande para uma única rede virtual no Azure. Mas vamos examinar as sub-redes definidas na rede virtual do Azure:

![Sub-redes de rede virtual do Azure e seus intervalos de endereços IP](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

Nós olhamos para uma rede virtual com uma primeira sub-rede VM (aqui chamada de "default") e uma sub-rede chamada "GatewaySubnet".

Nos dois gráficos anteriores, o **espaço de endereço de rede virtual** abrange **o intervalo de endereços IP de sub-rede da VM do Azure** e a do gateway de rede virtual.

É possível restringir o **espaço de endereço da rede virtual** para os intervalos específicos usados por cada sub-rede. É possível definir o **espaço de endereço da rede virtual** de uma rede virtual como vários intervalos específicos, conforme mostrado aqui:

![Espaço de endereço de rede virtual do Azure com dois espaços](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

Nesse caso, o **espaço de endereço da rede virtual** possui dois espaços definidos. Eles são iguais aos intervalos de endereços IP definidos para o intervalo de endereços IP da sub-rede da VM do Azure e do gateway de rede virtual. 

Você pode usar qualquer padrão de nomenclatura que desejar para essas sub-redes de locatário (sub-redes de VM). No entanto, **deve sempre haver uma, e apenas uma, sub-rede de gateway para cada rede virtual** que se conecta ao circuito de ExpressRoute do SAP HANA no Azure (Instâncias Grandes). **Essa sub-rede de gateway deve ser nomeada "GatewaySubnet"** para garantir que o gateway de ExpressRoute esteja corretamente colocado.

> [!WARNING] 
> É essencial que a sub-rede de gateway sempre seja denominada "GatewaySubnet".

Você pode usar várias sub-redes VM e intervalos de endereços não contíguas. Esses intervalos de endereços devem ser cobertos pelo **espaço de endereço de rede virtual** da rede virtual. Eles podem estar em uma forma agregada. Eles podem estar em uma lista dos intervalos exatos das sub-redes da VM e da sub-rede do gateway.

A seguir, um resumo dos fatos importantes sobre uma rede virtual do Azure que se conecta a instâncias grandes do HANA:

- Você deve enviar o **espaço de endereço de rede virtual** à Microsoft quando estiver executando uma implantação inicial de instâncias grandes do Hana. 
- O **espaço de endereço da rede virtual** pode ser um intervalo maior que abrange o intervalo para o intervalo de endereços IP de sub-rede da VM do Azure e do gateway de rede virtual.
- Ou você pode enviar vários intervalos que cobrem os diferentes intervalos de endereços IP dos intervalos de endereços IP de sub-rede da VM e o intervalo de endereços IP do gateway de rede virtual.
- O **espaço de endereço de rede virtual** definido é usado para propagação de roteamento BGP.
- O nome da sub-rede de gateway deve ser: **"GatewaySubnet"**.
- O espaço de endereço é usado como um filtro no lado da Instância Grande do HANA para permitir ou proibir o tráfego para as unidades de Instância Grande do HANA do Azure. As informações de roteamento do BGP da rede virtual do Azure e os intervalos de endereços IP configurados para filtragem no HANA em Instâncias Grandes devem corresponder. Caso contrário, podem ocorrer problemas de conectividade.
- Há alguns detalhes sobre a sub-rede do gateway que serão discutidos posteriormente, na seção **Conectando uma rede virtual ao ExpressRoute de Instância Grande do HANA.**



## <a name="different-ip-address-ranges-to-be-defined"></a>Diferentes intervalos de endereços IP a serem definidos 

Alguns dos intervalos de endereços IP necessários para a implantação de instâncias grandes do HANA já foram introduzidos. Mas há mais intervalos de endereços IP que também são importantes. Nem todos os intervalos de endereços IP a seguir precisam ser enviados à Microsoft. No entanto, você precisa defini-las antes de enviar uma solicitação para implantação inicial:

- **Espaço de endereço de rede virtual**: o **espaço de endereço de rede virtual** é o intervalo de endereços IP que você atribui ao parâmetro de espaço de endereço nas redes virtuais do Azure. Essas redes conectam-se ao ambiente do SAP HANA em Instâncias Grandes. É recomendável que esse parâmetro do espaço de endereço seja um valor de multilinha. Ele deve consistir no intervalo de sub-rede da VM do Azure e os intervalos de sub-rede do gateway do Azure. Esse intervalo de sub-rede foi mostrado nos gráficos anteriores. Ele NÃO deve sobrepor os intervalos de endereço ER-P2P ou pool de do servidor ou local. Como você pode obter esses intervalos de endereços IP? Sua equipe de rede corporativa ou provedor de serviços deve fornecer um ou vários intervalos de endereços IP que não são usados dentro de sua rede. Por exemplo, a sub-rede da sua VM do Azure é 10.0.1.0/24 e a sub-rede da sua sub-rede de gateway do Azure é 10.0.2.0/28.  É recomendável que seu espaço de endereço de rede virtual do Azure seja definido como: 10.0.1.0/24 e 10.0.2.0/28. Embora os valores do espaço de endereço possam ser agregados, recomendamos combiná-los aos intervalos de sub-rede. Ao fazer isso, você evita a reutilização acidental de intervalos de endereços IP não usados dentro dos espaços de endereço maiores em outra parte da rede. **O espaço de endereço de rede virtual é um intervalo de endereços IP. Ele precisa ser enviado à Microsoft quando você solicitar uma implantação inicial**.
- **Intervalo de endereços IP de sub-rede da VM do Azure:** Esse intervalo de endereços IP é aquele que você atribui ao parâmetro de sub-rede da rede virtual do Azure. Esse parâmetro está na rede virtual do Azure e conecta-se ao ambiente do SAP HANA em Instância Grande. Este intervalo de endereços IP é usado para atribuir endereços IP para VMs do Azure. Os endereços IP fora desse intervalo têm permissão para se conectar aos servidores da Instância Grande do SAP HANA. Se necessário, você pode usar várias sub-redes da VM do Azure. Recomendamos um bloco / 24 CIDR para cada sub-rede da VM do Azure. Esse intervalo de endereços deve fazer parte dos valores usados no espaço de endereço da rede virtual do Azure. Como você obtém esse intervalo de endereços IP? Sua equipe de rede corporativa ou provedor de serviços deve fornecer um intervalo de endereços IP que não esteja sendo usado em sua rede.
- **Intervalo do endereço IP da sub-rede do gateway da rede virtual:** Dependendo dos recursos que você planeja usar, o tamanho recomendado é:
   - Gateway ExpressRoute de alto desempenho: / 26 bloco de endereços - necessário para as classes SKUs Tipo II.
   - Coexistência com VPN e ExpressRoute usando um gateway de rede virtual ExpressRoute de alto desempenho (ou menor): / 27 bloco de endereços.
   - Todas as outras situações: bloco de endereço /28. Este intervalo de endereços deve ser uma parte dos valores usados nos valores de "espaço de endereço VNet". Esse intervalo de endereços deve fazer parte dos valores usados nos valores do espaço de endereço da rede virtual do Azure que você envia à Microsoft. Como você obtém esse intervalo de endereços IP? Sua equipe de rede corporativa ou provedor de serviços deve fornecer um intervalo de endereços IP que não esteja sendo usado atualmente em sua rede. 
- **Intervalo de endereços para conectividade do ER-P2P:** esse intervalo é o intervalo de IP da conexão P2P do ER (ExpressRoute) da Instância Grande do SAP HANA. Este intervalo de endereços IP deve ser um intervalo de endereços IP CIDR /29. Esse intervalo não deve sobrepor seu local ou outros intervalos de endereços IP do Azure. Esse intervalo de endereços IP é usado para configurar a conectividade de ER do seu gateway virtual da ExpressRoute para os servidores de Instância Grande do SAP HANA. Como você obtém esse intervalo de endereços IP? Sua equipe de rede corporativa ou provedor de serviços deve fornecer um intervalo de endereços IP que não esteja sendo usado atualmente em sua rede. **Esse intervalo é um intervalo de endereços IP. Ele precisa ser enviado à Microsoft quando você solicitar uma implantação inicial**.  
- **Intervalo de endereços do pool de IPS do servidor:** Esse intervalo de endereços IP é usado para atribuir o endereço IP individual a servidores do SAP HANA em instâncias grandes. O tamanho de sub-rede recomendado é um bloco / 24 CIDR. Se necessário, pode ser menor, com apenas 64 endereços IP. Nesse intervalo, os 30 primeiros endereços IP são reservados para uso pela Microsoft. Certifique-se de considerar esse fato quando você escolhe o tamanho do intervalo. Esse intervalo não deve sobrepor seu local ou outros intervalos de endereços IP do Azure. Como você obtém esse intervalo de endereços IP? Sua equipe de rede corporativa ou provedor de serviços deve fornecer um intervalo de endereços IP que não esteja sendo usado atualmente em sua rede.  **Esse intervalo é um intervalo de endereços IP, que precisa ser enviado à Microsoft ao solicitar uma implantação inicial**.

Intervalos de endereços IP opcionais que eventualmente precisam ser enviados à Microsoft:

- Se você optar por usar o [ExpressRoute alcance global](../../../expressroute/expressroute-global-reach.md) para habilitar o roteamento direto do local para as unidades de instância grande do Hana, será necessário reservar outro/29 intervalos de endereços IP. Esse intervalo pode não se sobrepor a nenhum dos outros intervalos de endereços IP definidos anteriormente.
- Se você optar por usar o [ExpressRoute alcance global](../../../expressroute/expressroute-global-reach.md) para habilitar o roteamento direto de um locatário de instância grande do Hana em uma região do Azure para outro locatário de instância grande do Hana em outra região do Azure, você precisará reservar outro/29 intervalo de endereços IP. Esse intervalo pode não se sobrepor a nenhum dos outros intervalos de endereços IP definidos anteriormente.

Para obter mais informações sobre o ExpressRoute Alcance Global e o uso em torno de instâncias grandes do HANA, verifique os documentos:

- [Arquitetura de rede do SAP HANA (Instâncias Grandes)](./hana-network-architecture.md)
- [Conecte uma rede virtual a instâncias grandes do HANA](./hana-connect-vnet-express-route.md)
 
É necessário definir e planejar os intervalos de endereços IP que foram descritos anteriormente. No entanto, você não precisa transmitir todos eles para a Microsoft. Os intervalos de endereços IP que você deve nomear para a Microsoft são:

- Azure virtual network address space(s)
- Intervalo de endereços para conectividade de ER-P2P
- Intervalo de endereços do pool de IPs do servidor

Se você adicionar outras redes virtuais que precisam se conectar às instâncias grandes do HANA, será necessário enviar o novo espaço de endereço da rede virtual do Azure que está adicionando à Microsoft. 

Segue um exemplo dos diferentes intervalos e alguns intervalos de exemplo que você deve configurar e, eventualmente, fornecer à Microsoft. O valor para o espaço de endereço da rede virtual do Azure não é agregado no primeiro exemplo. No entanto, ele é definido a partir dos intervalos do primeiro intervalo de endereços IP da VM do Azure e do intervalo de endereços IP da sub-rede do gateway da rede virtual. 

Você pode usar várias sub-redes de VMs na rede virtual do Azure ao configurar e enviar os intervalos de endereços IP adicionais das sub-redes de VMs adicionais como parte do espaço de endereço da rede virtual do Azure.

![Intervalos de endereços IP necessários na implantação mínima do SAP HANA no Azure (Instâncias Grandes)](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

O gráfico não mostra os intervalos de endereços IP adicionais necessários para o uso opcional do ExpressRoute Alcance Global.

Você também pode agregar os dados enviados à Microsoft. Nesse caso, o espaço de endereço da rede virtual do Azure inclui apenas um espaço. Usando os intervalos de endereços IP do exemplo anterior, o espaço de endereçamento da rede virtual agregada poderia se parecer com a imagem a seguir:

![Segunda possibilidade de intervalos de endereços IP necessários na implantação mínima do SAP HANA no Azure (Instâncias Grandes)](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

Como é possível ver no exemplo, em vez de dois intervalos menores que definiram o espaço de endereço da rede virtual do Azure, temos um intervalo maior que abrange 4096 endereços IP. Uma definição tão grande do espaço de endereço deixa alguns intervalos bastante grandes não utilizados. Como os valores de espaço de endereçamento de rede virtual são usados para propagação de rota BGP, o uso dos intervalos não utilizados no local ou em outro lugar na rede pode causar problemas de roteamento. O gráfico não mostra os intervalos de endereços IP adicionais necessários para o uso opcional do ExpressRoute Alcance Global.

Recomendamos que você mantenha o espaço de endereço totalmente alinhado com o espaço de endereço de sub-rede real que você usa. Se necessário, sem incorrer em tempo de inatividade na rede virtual, você poderá adicionar novos valores de espaço de endereço posteriormente.
 
> [!IMPORTANT] 
> Cada intervalo de endereço IP no ER-P2P, o pool de IP do servidor e o espaço de endereço da rede virtual do Azure devem **NÃO** se sobrepor uns aos outros ou a qualquer outro intervalo usado em sua rede. Cada um deve ser discreta. Como os dois gráficos anteriores mostram, eles também não podem ser uma sub-rede de nenhum outro intervalo. Se houver sobreposições entre os intervalos, a rede virtual do Azure poderá não se conectar ao circuito da Rota Expressa.

## <a name="next-steps-after-address-ranges-have-been-defined"></a>Próximas etapas após a definição de intervalos de endereços
Depois que os intervalos de endereços IP tiverem sido definidos, as seguintes ações precisam acontecer:

1. Envie os intervalos de endereços IP para o espaço de endereço da rede virtual do Azure, a conectividade ER-P2P e o intervalo de endereços do pool de IP do servidor, junto com outros dados que foram listados no início do documento. Neste ponto, você também pode começar a criar a rede virtual e as sub-redes da VM. 
2. Um circuito de Rota Expressa é criado pela Microsoft entre sua assinatura do Azure e o carimbo de Instância Grande do HANA.
3. Uma rede de locatário é criada no carimbo instância grande pela Microsoft.
4. A Microsoft configura a rede na infra-estrutura do SAP HANA no Azure (Instâncias Grandes) para aceitar endereços IP do espaço de endereço da rede virtual do Azure que se comunica com Instâncias Grandes do HANA.
5. Dependendo da SKU específica do SAP HANA no Azure (Instâncias Grandes) que você comprou, a Microsoft atribui uma unidade de computação em uma rede de locatários. Ela também aloca e monta armazenamento, além de instalar o sistema operacional (SUSE ou Linux Red Hat). Os endereços IP dessas unidades são retirados do intervalo de endereços do pool de IP do servidor que você enviou à Microsoft.

No final do processo de implantação, a Microsoft fornece os seguintes dados para você:
- Informações necessárias para conectar sua (s) rede (s) virtual (s) do Azure ao circuito da Rota Expressa que conecta redes virtuais do Azure a instâncias grandes do HANA:
     - Chaves de autorização
     - PeerID de ExpressRoute
- Dados para acessar instâncias grandes do HANA depois de estabelecer o circuito da Rota Expressa e a rede virtual do Azure.

Você também pode encontrar a sequência de conexão de instâncias grandes do HANA na configuração do documento [SAP HANA no Azure (instâncias grandes)](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/). Muitas das etapas a seguir são mostradas em uma implantação de exemplo nesse documento. 

## <a name="next-steps"></a>Próximas etapas

- Consulte [Conectando uma rede virtual ao ExpressRoute de Instância Grande do HANA](hana-connect-vnet-express-route.md).

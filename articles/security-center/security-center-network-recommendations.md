---
title: Protegendo seus recursos de rede na Central de Segurança do Azure | Microsoft Docs
description: Este documento aborda recomendações na Central de Segurança do Azure que ajudam a proteger os recursos de rede do Azure e a ficar em conformidade com as políticas de segurança.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 96c55a02-afd6-478b-9c1f-039528f3dea0
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2019
ms.author: monhaber
ms.openlocfilehash: 3d9658a6b7501714a6aad0e8eaa30ce587284bfe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60906272"
---
# <a name="protect-your-network-resources-in-azure-security-center"></a>Proteja seus recursos de rede na Central de Segurança do Azure
A Central de Segurança do Azure analisa continuamente o estado de segurança de seus recursos do Azure para as práticas recomendadas de segurança de rede. Quando o Security Center identifica possíveis vulnerabilidades de segurança, ele cria recomendações que guiam você pelo processo de configuração dos controles necessários para proteger e proteger seus recursos.

Este artigo aborda recomendações que se aplicam a seus recursos do Azure de uma perspectiva de segurança de rede. As recomendações de rede centram-se em firewalls de próxima geração, grupos de segurança de rede, acesso JIT VM a regras de tráfego de entrada excessivamente permissivas e muito mais. Para obter uma lista de recomendações de rede e ações de correção, consulte [Gerenciando recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md).

> [!NOTE]
> A página **Rede** permite aprofundar a integridade dos recursos do Azure a partir de uma perspectiva de rede. O mapa de rede e os controles de rede adaptativos estão disponíveis apenas para a camada padrão da Central de Segurança do Azure. [Se você usar a camada gratuita, poderá clicar no botão para**Exibir rede legada**e receber recomendações de recursos de rede](#legacy-networking).
>

A página **Rede** fornece uma visão geral das seções nas quais você pode aprofundar, para obter mais informações sobre a integridade dos recursos de sua rede:

- Mapa de rede (apenas na camada Standard do Azure Security Center)
- Proteção de rede adaptável
- Recomendações de segurança de rede.
- Herdado **rede** blade (rede folha anterior) 
 
![Painel de rede](./media/security-center-network-recommendations/networking-pane.png)

## <a name="network-map"></a>Mapa de rede
O mapa de rede interativo fornece uma visualização gráfica com sobreposições de segurança, fornecendo recomendações e insights para proteger seus recursos de rede. Usando o mapa, você pode ver a topologia de rede das cargas de trabalho do Azure, as conexões entre as máquinas virtuais e as sub-redes e a capacidade de detalhar o mapa em recursos específicos e as recomendações desses recursos.

Para abrir o mapa de rede:

1. Na Central de segurança sob o recurso de higiene de troca de segurança, selecione **rede**.
2. Sob **mapa de rede** clique em **consulte topologia**.
 
A visualização padrão do mapa de topologia é exibida:
- Assinaturas selecionadas no Azure. O mapa suporta várias assinaturas.
- Máquinas virtuais, sub-redes e redes virtuais do tipo de recurso do Resource Manager (não há suporte para recursos clássicos do Azure)
- VNets emparelhadas
- Somente recursos que possuem [recomendações de rede](security-center-recommendations.md) com alta ou média gravidade  
- Recursos de voltado para Internet
- O mapa é otimizado para as assinaturas selecionadas no Azure. Se você modificar sua seleção, o mapa será recalculado e otimizado novamente com base nas novas configurações.  

![Mapa de topologia de rede](./media/security-center-network-recommendations/network-map-info.png)

## <a name="understanding-the-network-map"></a>Noções básicas sobre o mapa de rede

O mapa de rede pode mostrar seus recursos do Azure em uma visualização de **Topologia** e em uma visualização **Tráfego**.

### <a name="the-topology-view"></a>O modo de exibição de topologia

Na visualização **Topologia** do mapa de rede, você pode visualizar os seguintes insights sobre seus recursos de rede:
- No círculo interno, você pode ver todas as Vnets dentro de suas assinaturas selecionadas, o próximo círculo é todas as sub-redes, o círculo externo é todas as máquinas virtuais.
- As linhas que conectam os recursos no mapa informam quais recursos estão associados entre si e como sua rede do Azure está estruturada. 
- Use os indicadores de gravidade para obter rapidamente uma visão geral de quais recursos têm recomendações abertas da Central de Segurança.
- Você pode clicar em qualquer um dos recursos para analisar os detalhes desse recurso e suas recomendações diretamente e no contexto do mapa de rede.  
- Se houver muitos recursos exibidos no mapa, o Azure Security Center usará seu algoritmo proprietário para agrupar seus recursos de forma inteligente, destacando os recursos que estão no estado mais crítico e as recomendações de maior gravidade. 

Como o mapa é interativo e dinâmico, todos os nós são clicáveis e a exibição pode ser alterada com base nos filtros:

1. Você pode modificar o que vê no mapa de rede usando os filtros na parte superior. Você pode se concentrar o mapa com base em:
   -  **Integridade da segurança**: Você pode filtrar o mapa com base na gravidade (alta, média, baixa) dos recursos do Azure.
   - **Recomendações:** Você pode selecionar quais recursos são exibidos com base nas recomendações ativas nesses recursos. Por exemplo, você pode exibir apenas os recursos para os quais a Central de Segurança recomenda que você ative os Grupos de Segurança de Rede.
   - **Zonas de rede:**: Por padrão, o mapa exibe apenas recursos voltados para a Internet, você também pode selecionar VMs internas.
 
2. Você pode clicar em **Redefinir** no canto superior esquerdo a qualquer momento para retornar o mapa ao seu estado padrão.

Para fazer drill down em um recurso:
1. Quando você seleciona um recurso específico no mapa, o painel direito é aberto e fornece informações gerais sobre o recurso, soluções de segurança conectadas, se houver, e as recomendações relevantes para o recurso. É o mesmo tipo de comportamento para cada tipo de recurso que você selecionar. 
2. Ao passar o mouse sobre um nó no mapa, você pode exibir informações gerais sobre o recurso, incluindo assinatura, tipo de recurso e grupo de recursos.
3. Use o link para ampliar a dica de ferramenta e refocar o mapa nesse nó específico. 
4. Para refocar o mapa de um nó específico, diminua o zoom.

### <a name="the-traffic-view"></a>O modo de exibição de tráfego

A visualização **Tráfego** fornece um mapa de todo o tráfego possível entre seus recursos. Isso fornece um mapa visual de todas as regras configuradas que definem quais recursos podem se comunicar com quem. Isso permite que você veja a configuração existente dos grupos de segurança de rede e identifique rapidamente possíveis configurações de risco em suas cargas de trabalho.

### <a name="uncover-unwanted-connections"></a>Descubra conexões indesejadas

A força dessa visão está em sua capacidade de mostrar a você essas conexões permitidas junto com as vulnerabilidades existentes, para que você possa usar essa seção transversal de dados para executar o endurecimento necessário em seus recursos. 

Por exemplo, você pode detectar duas máquinas que você não sabia que poderiam se comunicar, permitindo isolar melhor as cargas de trabalho e as sub-redes.

### <a name="investigate-resources"></a>Investigar recursos

Para fazer drill down em um recurso:
1. Quando você seleciona um recurso específico no mapa, o painel direito é aberto e fornece informações gerais sobre o recurso, soluções de segurança conectadas, se houver, e as recomendações relevantes para o recurso. É o mesmo tipo de comportamento para cada tipo de recurso que você selecionar. 
2. Clique em **Tráfego** para ver a lista de possíveis tráfego de saída e de entrada no recurso - essa é uma lista abrangente de quem pode se comunicar com o recurso e com quem pode se comunicar e por meio de quais protocolos e portas. Por exemplo, quando você seleciona uma VM, todas as VMs que ele possa se comunicar com são mostrados e, quando você seleciona uma sub-rede, todas as sub-redes que ele possa se comunicar com são mostradas.

**Esses dados são baseados na análise dos Grupos de segurança de rede, bem como em algoritmos avançados de aprendizado de máquina que analisam várias regras para entender seus cruzamentos e interações.** 

![Mapa de tráfego de rede](./media/security-center-network-recommendations/network-map-traffic.png)

## Sistema de rede herdado <a name ="legacy-networking"></a>

Se você não tiver o nível do Security Center Standard, esta seção explica como exibir recomendações gratuitas de rede.

Para acessar essas informações, no blade Rede, clique em **Exibir rede legada**. 

![Sistema de rede herdado](./media/security-center-network-recommendations/legacy-networking.png)

### <a name="internet-facing-endpoints-section"></a>Seção dos pontos de extremidade voltados para a Internet
Na seção **Endpoints da Internet enfrentados**, você pode ver as máquinas virtuais que estão atualmente configuradas com um endpoint voltado para a Internet e seu status.

Esta tabela tem o nome do ponto de extremidade, o endereço IP voltado para Internet e o status atual da gravidade do grupo de segurança de rede e as recomendações de NGFW. A tabela é classificada por gravidade.

### <a name="networking-topology-section"></a>Seção de Topologia da rede
A seção de **topologia de rede** tem uma visão hierárquica dos recursos.

Esta tabela é classificada (máquinas virtuais e sub-redes) por gravidade.

Nesta exibição de topologia, o primeiro nível exibe Vnets. A segunda tela tem sub-redes e o terceiro nível exibe as máquinas virtuais que pertencem a essas sub-redes. A coluna da direita mostra o status atual das recomendações do grupo de segurança de rede para esses recursos.

O terceiro nível exibe máquinas virtuais, que é semelhante ao descrito anteriormente. Você pode clicar em qualquer recurso para saber mais ou aplicar o controle ou a configuração de segurança necessários.

## <a name="network-recommendations"></a>Recomendações de rede

|Tipo de recurso|Classificação de segurança|Recomendações|DESCRIÇÃO|
|----|----|----|----|
|Computador|40|Ativar grupos de segurança de rede em máquinas virtuais|Habilite os grupos de segurança de rede para controlar o acesso à rede de suas máquinas virtuais.|
|Sub-rede|35|Habilitar grupos de segurança de rede em sub-redes |Ative os grupos de segurança de rede para controlar o acesso à rede de recursos implantados em suas sub-redes.|
|Computador|30|Aplicar um controle de acesso à rede Just-In-Time|Aplique o controle de acesso da VM just-in-time para bloquear permanentemente o acesso às portas selecionadas e habilite os usuários autorizados a abri-las por meio do mesmo mecanismo e por um período de tempo limitado.|
|Computador|20|Restringir o acesso por meio de ponto de extremidade para a Internet|Proteja os grupos de segurança de rede de suas VMs na Internet, restringindo o acesso às regras de permissão existentes.|
|Computador|10|Adicionar um firewall da próxima geração|Adicione uma solução de NGFW (Next Generation Firewall) para proteger melhor sua VM voltada para a Internet.|
|Computador|5|Rotear o tráfego através do firewall de gateway de rede somente|Para concluir a implantação de sua solução de firewall de última geração, o tráfego para suas máquinas virtuais de internet protegidos deve ser roteado por meio de solução de firewall de última geração.|
|VNET|5|Habilitar a proteção contra DDoS standard|Aplicativos com IPs públicos nessas redes virtuais não são protegidos com o padrão de serviço de proteção DDOS. É aconselhável habilitá-lo para permitir a mitigação dos ataques volumétricos e protocolares da rede.|
|Computador|10|Adicionar um firewall da próxima geração|dd uma solução de Firewall de próxima geração (NGFW) para proteger suas máquinas virtuais de Internet.|
|Computador|5|Rotear o tráfego através do firewall de gateway de rede somente|Para concluir a implantação da sua solução de firewall de próxima geração, o tráfego para suas VMs protegidas pela Internet deve ser roteado apenas por meio da solução de firewall de próxima geração.|
Rede virtual|5|Habilitar a proteção contra DDoS standard|Aplicativos com endereços IP públicos nessas redes virtuais não são protegidos com o padrão de serviço de proteção DDOS. É aconselhável habilitá-lo para permitir a mitigação dos ataques volumétricos e protocolares da rede.|
## <a name="see-also"></a>Consulte também
Para saber mais sobre as recomendações que se aplicam aos outros tipos de recursos do Azure, consulte o seguinte:

* [Protegendo suas máquinas virtuais na Central de Segurança do Azure](security-center-virtual-machine-recommendations.md)
* [Protegendo seus aplicativos na Central de segurança do Azure](security-center-application-recommendations.md)
* [Protegendo o serviço do SQL Azure na Central de Segurança do Azure](security-center-sql-service-recommendations.md)

Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Configurando políticas de segurança na Central de Segurança do Azure](tutorial-security-policy.md) : saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
* [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) : aprenda a gerenciar e a responder a alertas de segurança.
* [Perguntas frequentes da Central de Segurança do Azure](security-center-faq.md) : encontre as perguntas frequentes sobre como usar o serviço.

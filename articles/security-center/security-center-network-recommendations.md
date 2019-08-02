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
ms.author: v-mohabe
ms.openlocfilehash: fb91d6a9cdc56c88b424b7e0382f283c8b55dac9
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234320"
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
- VMs, sub-redes e VNets do tipo de recurso do Resource Manager (não há suporte para recursos clássicos do Azure)
- VNets emparelhados
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
   - **Zonas de rede:** : Por padrão, o mapa exibe apenas recursos voltados para a Internet, você também pode selecionar VMs internas.
 
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
2. Clique em **Tráfego** para ver a lista de possíveis tráfego de saída e de entrada no recurso - essa é uma lista abrangente de quem pode se comunicar com o recurso e com quem pode se comunicar e por meio de quais protocolos e portas. Por exemplo, quando você seleciona uma VM, todas as VMs com as quais ela pode se comunicar são mostradas e, quando você seleciona uma sub-rede, todas as sub-redes com as quais ela pode se comunicar são mostradas.

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

|Nome da recomendação|DESCRIÇÃO|Severidade|Classificação de segurança|Tipo de recurso|
|----|----|----|----|----|----|
|Os grupos de segurança de rede no nível de sub-rede devem ser habilitados|Habilite grupos de segurança de rede para controlar o acesso à rede de recursos implantados em suas sub-redes.|Alta/média|30|Subnet|
|As máquinas virtuais devem ser associadas a um grupo de segurança de rede|Habilite os grupos de segurança de rede para controlar o acesso à rede de suas máquinas virtuais.|Alta/média|30|Máquina virtual|
|O acesso deve ser restrito para grupos de segurança de rede permissivos com VMs voltadas para a Internet|Proteja os grupos de segurança de rede de suas VMs voltadas para a Internet restringindo o acesso de suas regras de permissão existentes.|Alto|20|Máquina virtual|
|As regras para aplicativos Web em IaaS NSGs devem ser protegidas|Proteger o NSG (grupo de segurança de rede) de suas máquinas virtuais que estão executando aplicativos Web, com regras do NSG que são excessivamente permissivas com relação às portas do aplicativo Web.|Alto|20|Máquina virtual|
|O acesso aos serviços de aplicativos deve ser restrito|Restrinja o acesso aos serviços de aplicativos alterando a configuração de rede, para negar o tráfego de entrada de intervalos muito amplos.|Alto|10|serviço de aplicativo|
|As portas de gerenciamento devem ser fechadas em suas máquinas virtuais|Proteger o grupo de segurança de rede de suas máquinas virtuais para restringir o acesso às portas de gerenciamento.|Alto|10|Máquina virtual|
A proteção contra DDoS Standard deve ser habilitada|Proteja redes virtuais que contêm aplicativos com IPs públicos habilitando o padrão de serviço de proteção contra DDoS. A proteção contra DDoS permite a mitigação de ataques de volumétricos de rede e de protocolo.|Alto|10|Rede virtual|
|O encaminhamento IP em sua máquina virtual deve ser desabilitado|Desabilite o encaminhamento de IP. Quando o encaminhamento de IP está habilitado na NIC de uma máquina virtual, o computador pode receber o tráfego endereçado a outros destinos. O encaminhamento de IP raramente é necessário (por exemplo, ao usar a VM como uma solução de virtualização de rede) e, portanto, isso deve ser revisado pela equipe de segurança de rede.|Média|10|Máquina virtual|
|Aplicativo Web deve ser acessível somente por HTTPS|Habilite o acesso "somente HTTPS" para aplicativos Web. O uso de HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito de ataques de interceptação de camada de rede.|Média|20|Aplicativo Web|
|O controle de acesso à rede just-in-time deve ser aplicado em máquinas virtuais|Aplique o controle de acesso de máquina virtual (JIT) just-in-time para bloquear permanentemente o acesso às portas selecionadas e habilite os usuários autorizados a abri-los, por meio do JIT, apenas por uma quantidade limitada de tempo.|Alto|20|Máquina virtual|
|Os aplicativos de funções só devem ser acessíveis via HTTPS|Habilite o acesso "somente HTTPS" para aplicativos de funções. O uso de HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito de ataques de interceptação de camada de rede.|Média|20|Aplicativo de funções|
|A transferência segura para contas de armazenamento deve ser habilitada|Habilite a transferência segura para contas de armazenamento. A transferência segura é uma opção que força a sua conta de armazenamento a aceitar somente solicitações de conexões seguras (HTTPS). O uso de HTTPS garante a autenticação entre o servidor e o serviço e protege os dados em trânsito de ataques de camada de rede, como Man-in-the-Middle, espionagem e seqüestro de sessão.|Alto|20|Conta de armazenamento|

## <a name="see-also"></a>Consulte também
Para saber mais sobre as recomendações que se aplicam aos outros tipos de recursos do Azure, consulte o seguinte:

* [Protegendo suas máquinas virtuais na Central de Segurança do Azure](security-center-virtual-machine-recommendations.md)
* [Protegendo seus aplicativos na Central de segurança do Azure](security-center-application-recommendations.md)
* [Protegendo o serviço do SQL Azure na Central de Segurança do Azure](security-center-sql-service-recommendations.md)

Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Configurando políticas de segurança na Central de Segurança do Azure](tutorial-security-policy.md) : saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
* [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) : aprenda a gerenciar e a responder a alertas de segurança.
* [Perguntas frequentes da Central de Segurança do Azure](security-center-faq.md) : encontre as perguntas frequentes sobre como usar o serviço.

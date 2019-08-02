---
title: Protegendo os Serviços de Aplicativos na Central de Segurança do Azure | Microsoft Docs
description: Este artigo ajuda você a começar a proteger seus Serviços de Aplicativos na Central de Segurança do Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/27/2019
ms.author: v-mohabe
ms.openlocfilehash: 6a6b5b6e247bd7c105286b86257a6ae11c1d1c60
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551853"
---
# <a name="protect-app-service-with-azure-security-center"></a>Proteja o serviço de aplicativos com a Central de Segurança do Azure
Este artigo ajuda você a usar a Central de Segurança do Azure para monitorar e proteger seus aplicativos executados em cima do Serviço de Aplicativo.

O Serviço de Aplicativo permite que você crie e hospede aplicativos da Web na linguagem de programação de sua escolha sem gerenciar a infraestrutura. O Serviço de Aplicativo do Azure oferece escalonamento automático e alta disponibilidade, suporte a Windows e Linux, bem como implantações automatizadas do GitHub, do Azure DevOps ou de qualquer repositório Git. 

Vulnerabilidades em aplicativos da Web são frequentemente exploradas por invasores, porque eles têm uma interface comum e dinâmica para quase todas as organizações na Internet. As solicitações para aplicativos em execução no Serviço de Aplicativo passam por vários gateways implantados em datacenters do Azure em todo o mundo, responsáveis pelo roteamento de cada solicitação para seu aplicativo correspondente. 

A Central de Segurança do Azure pode executar avaliações e recomendações sobre seus aplicativos em execução no Serviço de Aplicativo nas caixas de proteção em sua VM ou instâncias sob demanda. Aproveitando a visibilidade que o Azure tem como provedor de nuvem, o Security Center analisa os logs internos do seu Serviço de Aplicativo para monitorar ataques comuns a aplicativos da Web que geralmente são executados em vários destinos.

A Central de Segurança aproveita a escala da nuvem para identificar ataques em seus aplicativos do Serviço de Aplicativo e se concentrar em ataques emergentes, enquanto os invasores estão na fase de reconhecimento, fazendo a varredura para identificar vulnerabilidades em vários sites hospedados no Azure. A Central de Segurança do Azure usa modelos de análise e aprendizado de máquina para abranger todas as interfaces, permitindo que os clientes interajam com seus aplicativos, seja por HTTP ou por meio de métodos de gerenciamento. Além disso, como um serviço primário no Azure, o Security Center também está em uma posição exclusiva para oferecer análise de segurança baseada em host cobrindo os nós de computação subjacentes a essa PaaS, permitindo que o Security Center detecte ataques contra aplicativos da Web que já foram explorados.

## <a name="prerequisites"></a>Pré-requisitos

Para monitorar e proteger seu Serviço de Aplicativo, você precisa ter um plano do Serviço de Aplicativo associado a máquinas dedicadas. Esses planos são: Básico, Padrão, Premium, Isolado ou Linux. A Central de Segurança do Azure não oferece suporte aos planos Gratuito, Compartilhado ou de Consumo. Para obter mais informações, consulte [planos de Serviço de Aplicativo do Azure](https://azure.microsoft.com/pricing/details/app-service/plans/).

## <a name="security-center-protection"></a>Proteção da Central de Segurança

A Central de Segurança do Azure protege a instância da VM na qual seu Serviço de Aplicativo está sendo executado e a interface de gerenciamento. Ele também monitora solicitações e respostas enviadas para e de seus aplicativos em execução no Serviço de Aplicativo.

A Central de Segurança é integrada de maneira nativa ao Serviço de Aplicativo, eliminando a necessidade de implantação e integração - a integração é completamente transparente.



## <a name="enabling-monitoring-and-protection-of-app-service"></a>Ativando o monitoramento e a proteção do Serviço de Aplicativo

1. No Azure, escolha a Central de segurança.
2. Vá para **preços & configurações** e escolha uma assinatura.
3. Em **Nível de preços**, na linha **Serviço de aplicativos**, alterne seu plano para **Ativado**.

![alternância do serviço de aplicativo](./media/security-center-app-services/app-services-toggle.png)

>[!NOTE]
> O número de instâncias listadas para sua quantidade de recursos representa o número de instâncias relevantes do serviço de aplicativos ativas no momento em que você abriu a folha de nível de preços. Como esse número pode mudar com base nas opções de dimensionamento selecionadas, o número de instâncias pelas quais você é cobrado será modificado de acordo.

Para desativar o monitoramento e as recomendações do seu serviço de aplicativos, repita esse processo e ative o plano do **Serviço de Aplicativo do Azure** para **Desativado**.



## <a name="see-also"></a>Consulte também
Neste artigo, você aprendeu como usar os recursos de monitoramento na Central de segurança do Azure. Para saber mais sobre a Central de Segurança do Azure, veja o seguinte:

* [Configurando políticas de segurança na Central de Segurança do Azure](tutorial-security-policy.md): Saiba como configurar as configurações de segurança na Central de Segurança do Azure.
* [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md): Saiba como gerenciar e responder aos alertas de segurança.
* [Serviços de aplicativos](security-center-virtual-machine-protection.md#app-services):  Exiba uma lista de seus ambientes de serviços de aplicativo com resumos de integridade.
* [Monitorando as soluções de parceiros na Central de Segurança do Azure](security-center-partner-solutions.md): Saiba como monitorar o status da integridade das soluções dos parceiros.
* [Perguntas Frequentes sobre a Central de Segurança do Azure](security-center-faq.md): Encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/): Encontre postagens no blog sobre a conformidade e segurança do Azure.

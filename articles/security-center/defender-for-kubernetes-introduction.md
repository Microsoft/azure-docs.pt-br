---
title: Azure Defender para Kubernetes – Benefícios e recursos
description: Saiba mais sobre os benefícios e os recursos do Azure Defender para Kubernetes.
author: memildin
ms.author: memildin
ms.date: 02/07/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 83d0215ebca9d60d61937cb20bb82c7ccb30aac1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102100620"
---
# <a name="introduction-to-azure-defender-for-kubernetes"></a>Introdução ao Azure Defender para Kubernetes

O AKS (Serviço de Kubernetes do Azure) é o serviço gerenciado da Microsoft para desenvolver, implantar e gerenciar aplicativos em contêineres.

A Central de Segurança do Azure e o AKS formam uma oferta de segurança nativa de nuvem do Kubernetes com proteção de ambiente, proteção de cargas de trabalho e proteção em tempo de execução, conforme descrito em [Segurança de contêiner na Central de Segurança](container-security.md).

Para detecção de ameaças nos clusters do Kubernetes, habilite o **Azure Defender para Kubernetes**.

A detecção de ameaças no nível de host para os nós do AKS do Linux estará disponível se você habilitar o [Azure Defender para servidores](defender-for-servers-introduction.md) e o agente do Log Analytics. No entanto, se o cluster do AKS for implantado em um conjunto de dimensionamento de máquinas virtuais, o agente do Log Analytics não terá suporte no momento.

## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|GA (Disponibilidade Geral)|
|Preço:|O **Azure Defender para Kubernetes** é cobrado conforme mostrado em [Preço da Central de Segurança](https://azure.microsoft.com/pricing/details/security-center/)|
|Funções e permissões necessárias:|O **Administrador de segurança** pode ignorar alertas.<br>**O leitor de segurança** pode exibir as conclusões.|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim](./media/icons/yes-icon.png) Nacionais/soberanas (US Gov, China Gov, outros Gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-kubernetes"></a>Quais são os benefícios do Azure Defender para Kubernetes?

O Azure Defender para Kubernetes fornece **proteção contra ameaças no nível do cluster** monitorando seus serviços gerenciados pelo AKS por meio dos logs recuperados pelo AKS (Serviço do Kubernetes do Azure).

Os exemplos de eventos de segurança que o Azure Defender para Kubernetes monitora incluem painéis expostos do Kubernetes, criação de funções com privilégios altos e criação de montagens confidenciais. Para obter uma lista completa dos alertas no nível do cluster do AKS, confira a [tabela de referência de alertas](alerts-reference.md#alerts-akscluster).

> [!TIP]
> Você pode simular alertas de contêiner seguindo as instruções [nesta postagem de blog](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).

Além disso, nossa equipe global de pesquisadores de segurança monitora constantemente o panorama de ameaças. Eles adicionam alertas e vulnerabilidades específicos do contêiner à medida que são descobertos.

>[!NOTE]
> A Central de Segurança gera alertas de segurança para ações e implantações do Serviço de Kubernetes do Azure que ocorrem **após** o Azure Defender para Kubernetes ser habilitado.




## <a name="azure-defender-for-kubernetes---faq"></a>Azure Defender para Kubernetes – Perguntas frequentes

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>Eu posso obter proteções do AKS mesmo sem o agente do Log Analytics?

O plano do **Azure Defender para Kubernetes** fornece proteções no nível do cluster. Se você também implantar o agente do Log Analytics do **Azure Defender para servidores**, você obterá a proteção contra ameaças nos seus nós que é fornecida com esse plano. Saiba mais em [Introdução ao Azure Defender para servidores](defender-for-servers-introduction.md).

É recomendável implantar ambos, para obter a proteção mais completa possível.

Se você optar por não instalar o agente nos hosts, receberá apenas um subconjunto dos benefícios da proteção contra ameaças e dos alertas de segurança. Você ainda receberá alertas relacionados a análises de rede e comunicações com servidores mal-intencionados.

### <a name="does-aks-allow-me-to-install-custom-vm-extensions-on-my-aks-nodes"></a>O AKS permite instalar extensões de VM personalizadas em meus nós do AKS?
Para que o Azure Defender monitore seus nós do AKS, eles devem estar executando o agente do Log Analytics. 

O AKS é um serviço gerenciado e, como o agente do Log Analytics é uma extensão gerenciada pela Microsoft, ele também tem suporte nos clusters do AKS.

### <a name="if-my-cluster-is-already-running-an-azure-monitor-for-containers-agent-do-i-need-the-log-analytics-agent-too"></a>Se meu cluster já estiver executando um agente do Azure Monitor para contêineres, precisarei do agente do Log Analytics também?
Para que o Azure Defender monitore seus nós do AKS, eles devem estar executando o agente do Log Analytics.

Se os clusters já estiverem executando o agente do Azure Monitor para contêineres, você também poderá instalar o agente do Log Analytics e os dois agentes poderão trabalhar juntos sem nenhum problema.

[Saiba mais sobre o agente do Azure Monitor para contêineres](../azure-monitor/containers/container-insights-manage-agent.md).


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre a proteção do Kubernetes da Central de Segurança, incluindo o Azure Defender para Kubernetes. 

> [!div class="nextstepaction"]
> [Habilitar o Azure Defender](enable-azure-defender.md)

Para obter material relacionado, consulte os seguintes artigos: 

- [Transmitir alertas para uma solução SIEM, SOAR ou Gerenciamento de Serviços de TI](export-to-siem.md)
- [Tabela de referência de alertas](alerts-reference.md)

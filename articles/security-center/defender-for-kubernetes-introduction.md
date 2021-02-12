---
title: Azure Defender para Kubernetes – Benefícios e recursos
description: Saiba mais sobre os benefícios e os recursos do Azure Defender para Kubernetes.
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: a19e90a15991cdc03999bf43d5bece63325aab05
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98916567"
---
# <a name="introduction-to-azure-defender-for-kubernetes"></a>Introdução ao Azure Defender para Kubernetes

O AKS (Serviço de Kubernetes do Azure) é o serviço gerenciado da Microsoft para desenvolver, implantar e gerenciar aplicativos em contêineres.

A Central de Segurança do Azure e o AKS formam a melhor oferta de segurança nativa de nuvem do Kubernetes e, juntos, fornecem proteção de ambiente, proteção de cargas de trabalho e proteção em tempo de execução, conforme descrito abaixo.

Para detecção de ameaças nos clusters do Kubernetes, habilite o **Azure Defender para Kubernetes**.

A detecção de ameaças no nível de host para os nós AKS do Linux estará disponível se você habilitar o [Azure Defender para Servidores](defender-for-servers-introduction.md).

## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|GA (Disponibilidade Geral)|
|Preço:|O **Azure Defender para Kubernetes** é cobrado conforme mostrado na [página de preços](security-center-pricing.md)|
|Funções e permissões necessárias:|O **Administrador de segurança** pode ignorar alertas.<br>**O leitor de segurança** pode exibir as conclusões.|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim](./media/icons/yes-icon.png) Nacionais/soberanas (US Gov, China Gov, outros Gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-kubernetes"></a>Quais são os benefícios do Azure Defender para Kubernetes?

### <a name="run-time-protection"></a>Proteção em tempo de execução

Por meio da análise contínua das fontes do AKS a seguir, a Central de Segurança fornece proteção contra ameaças em tempo real para seus ambientes em contêineres e gera alertas para ameaças e atividades mal-intencionadas detectadas no nível do host *e* do cluster AKSS. É possível usar essas informações para corrigir os problemas rapidamente e aumentar a segurança de seus contêineres.

A Central de Segurança fornece proteção contra ameaças em diferentes níveis: 

- **Nível de host (fornecido pelo Azure Defender para Servidores)** – Usando o mesmo agente do Log Analytics que a Central de Segurança usa em outras VMs, o Azure Defender monitora seus nós AKS do Linux em busca de atividades suspeitas, como detecção de Shell da Web e conexão com endereços IP suspeitos conhecidos. O agente também monitora a análise específica de contêiner, como a criação de contêineres privilegiados, acessos suspeitos a servidores de API e servidores SSH (Secure Shell) em execução dentro de um contêiner do Docker.

    Para obter uma lista dos alertas no nível do host do AKS, confira a [Tabela de referência de alertas](alerts-reference.md#alerts-containerhost).

    >[!IMPORTANT]
    > Se você optar por não instalar os agentes em seus hosts, receberá apenas um subconjunto dos benefícios da proteção contra ameaças e dos alertas de segurança. Você ainda receberá alertas relacionados a análises de rede e comunicações com servidores mal-intencionados.

- **Nível de cluster do AKS (fornecido pelo Azure Defender para Kubernetes)** – No nível do cluster, a proteção contra ameaças baseia-se na análise dos logs de auditoria do Kubernetes. Para habilitar esse monitoramento **sem agente**, habilite o Azure Defender. Para gerar alertas nesse nível, a Central de Segurança monitora seus serviços gerenciados pelo AKS usando os logs recuperados pelo AKS. Exemplos de eventos nesse nível incluem painéis expostos do Kubernetes, a criação de funções com altos privilégios e a criação de montagens confidenciais.

    Para obter uma lista dos alertas no nível do cluster AKS, confira a [Tabela de referência de alertas](alerts-reference.md#alerts-akscluster).

    >[!NOTE]
    > A Central de Segurança gera alertas de segurança para ações e implantações do Serviço de Kubernetes do Azure que ocorrem após a opção Kubernetes ser habilitada nas configurações de assinatura. 

Além disso, nossa equipe global de pesquisadores de segurança monitora constantemente o panorama de ameaças. Eles adicionam alertas e vulnerabilidades específicos do contêiner à medida que são descobertos.

> [!TIP]
> Você pode simular alertas de contêiner seguindo as instruções [nesta postagem de blog](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).



## <a name="how-does-security-centers-kubernetes-protection-work"></a>Como funciona a proteção do Kubernetes da Central de Segurança?

Veja abaixo um diagrama de alto nível da interação entre a Central de Segurança do Azure, o serviço de Kubernetes do Azure e o Azure Policy.

Você pode ver que os itens recebidos e analisados pela Central de Segurança incluem:

- logs de auditoria do servidor de API
- eventos de segurança brutos do agente do Log Analytics
- informações de configuração de cluster do cluster do AKS
- configuração da carga de trabalho do Azure Policy (por meio do **complemento do Azure Policy para Kubernetes**). [Saiba mais sobre as melhores práticas de proteção de cargas de trabalho usando o controle de admissão do Kubernetes](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)

:::image type="content" source="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png" alt-text="Arquitetura de alto nível da interação entre a Central de Segurança do Azure, o Serviço de Kubernetes do Azure e o Azure Policy" lightbox="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png":::




## <a name="azure-defender-for-kubernetes---faq"></a>Azure Defender para Kubernetes – Perguntas frequentes

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>Eu posso obter proteções do AKS mesmo sem o agente do Log Analytics?

Conforme mencionado acima, o plano opcional **Azure Defender para Kubernetes** fornece proteções no nível do cluster, o agente do Log Analytics do **Azure Defender para Servidores** protege os nós. 

É recomendável implantar ambos, para obter a proteção mais completa possível.

Se você optar por não instalar o agente nos hosts, receberá apenas um subconjunto dos benefícios da proteção contra ameaças e dos alertas de segurança. Você ainda receberá alertas relacionados a análises de rede e comunicações com servidores mal-intencionados.


### <a name="does-aks-allow-me-to-install-custom-vm-extensions-on-my-aks-nodes"></a>O AKS permite instalar extensões de VM personalizadas em meus nós do AKS?

Para que o Azure Defender monitore seus nós do AKS, eles devem estar executando o agente do Log Analytics. 

O AKS é um serviço gerenciado e, como o agente do Log Analytics é uma extensão gerenciada pela Microsoft, ele também tem suporte nos clusters do AKS.



### <a name="if-my-cluster-is-already-running-an-azure-monitor-for-containers-agent-do-i-need-the-log-analytics-agent-too"></a>Se meu cluster já estiver executando um agente do Azure Monitor para contêineres, precisarei do agente do Log Analytics também?

Para que o Azure Defender monitore seus nós do AKS, eles devem estar executando o agente do Log Analytics.

Se os clusters já estiverem executando o agente do Azure Monitor para contêineres, você também poderá instalar o agente do Log Analytics e os dois agentes poderão trabalhar juntos sem nenhum problema.

[Saiba mais sobre o agente do Azure Monitor para contêineres](../azure-monitor/insights/container-insights-manage-agent.md).


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre a proteção do Kubernetes da Central de Segurança, incluindo o Azure Defender para Kubernetes. 

Para obter material relacionado, consulte os seguintes artigos: 

- [Habilitar o Azure Defender](security-center-pricing.md#enable-azure-defender)
- [Transmitir alertas para uma solução SIEM, SOAR ou Gerenciamento de Serviços de TI](export-to-siem.md)
- [Tabela de referência de alertas](alerts-reference.md)
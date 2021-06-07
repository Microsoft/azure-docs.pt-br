---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 06/30/2020
ms.topic: include
ms.openlocfilehash: 979bc3ea6a01d31e64aa503216b8fd69164eadc6
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450511"
---
O Azure Defender fornece proteção contra ameaças em tempo real para seus ambientes em contêineres e gera alertas sobre atividades suspeitas. É possível usar essas informações para corrigir os problemas rapidamente e aumentar a segurança de seus contêineres.

O Azure Defender fornece proteção contra ameaças em diferentes níveis: 

* **Nível de host (fornecido pelo Azure Defender para Servidores)** – Usando o mesmo agente do Log Analytics que a Central de Segurança usa em outras VMs, o Azure Defender monitora seus nós AKS do Linux em busca de atividades suspeitas, como detecção de Shell da Web e conexão com endereços IP suspeitos conhecidos. O agente também monitora a análise específica de contêiner, como a criação de contêineres privilegiados, acessos suspeitos a servidores de API e servidores SSH (Secure Shell) em execução dentro de um contêiner do Docker.

    Se você optar por não instalar os agentes em seus hosts, receberá apenas um subconjunto dos benefícios da proteção contra ameaças e dos alertas de segurança. Você ainda receberá alertas relacionados a análises de rede e comunicações com servidores mal-intencionados.

    >[!IMPORTANT]
    > Atualmente, não há suporte para a instalação do agente do Log Analytics nos clusters do Serviço de Kubernetes do Azure que estão em execução em conjuntos de dimensionamento de máquinas virtuais.

    Para obter uma lista dos alertas no nível do host do AKS, confira a [Tabela de referência de alertas](../articles/security-center/alerts-reference.md#alerts-containerhost).


* **Nível de cluster do AKS (fornecido pelo Azure Defender para Kubernetes)** – No nível do cluster, a proteção contra ameaças baseia-se na análise dos logs de auditoria do Kubernetes. Para habilitar esse monitoramento **sem agente**, habilite o Azure Defender. Para gerar alertas nesse nível, o Azure Defender monitora seus serviços gerenciados pelo AKS usando os logs recuperados pelo AKS. Exemplos de eventos nesse nível incluem painéis expostos do Kubernetes, a criação de funções com altos privilégios e a criação de montagens confidenciais.

    >[!NOTE]
    > A Central de Segurança gera alertas de segurança para ações e implantações do Serviço de Kubernetes do Azure que ocorrem após a opção Kubernetes ser habilitada nas configurações de assinatura. 

    Para obter uma lista dos alertas no nível do cluster AKS, confira a [Tabela de referência de alertas](../articles/security-center/alerts-reference.md#alerts-akscluster).

Além disso, nossa equipe global de pesquisadores de segurança monitora constantemente o panorama de ameaças. Eles adicionam alertas e vulnerabilidades específicos do contêiner à medida que são descobertos.

> [!TIP]
> Você pode simular alertas de contêiner seguindo as instruções [nesta postagem de blog](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).
---
title: Arquitetura do agente kubernetes habilitado para Arc do Azure
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Este artigo fornece uma visão geral da arquitetura dos agentes kubernetes habilitados para o Azure Arc
keywords: Kubernetes, Arc, Azure, contêineres
ms.openlocfilehash: 5e53e99c492f08deab8dea89ec95190782661012
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121891"
---
# <a name="azure-arc-enabled-kubernetes-agent-architecture"></a>Arquitetura do agente kubernetes habilitado para Arc do Azure

Por si só, o [kubernetes](https://kubernetes.io/) pode implantar cargas de trabalho em contêineres consistentemente em ambientes híbridos e de várias nuvens. O Azure Arc habilitado kubernetes, no entanto, funciona como um plano de controle centralizado e consistente que gerencia políticas, governança e segurança em ambientes heterogêneos. Esse artigo fornece:

* Uma visão geral da arquitetura de conexão de um cluster com o Azure Arc.
* O padrão de conectividade seguido por agentes.
* Uma descrição dos dados trocados entre o ambiente de cluster e o Azure.

## <a name="deploy-agents-to-your-cluster"></a>Implantar agentes no cluster

A maioria dos data centers locais impõe regras de rede estritas que impedem a comunicação de entrada no firewall de limite de rede. O kubernetes habilitado para Arc do Azure funciona com essas restrições, não exigindo portas de entrada no firewall e habilitando apenas pontos de extremidade de egresso seletivos para comunicação de saída. Agentes kubernetes habilitados para o Azure Arc iniciam essa comunicação de saída. 

![Visão geral da arquitetura](./media/architectural-overview.png)

### <a name="connect-a-cluster-to-azure-arc"></a>Conectar um cluster ao Azure Arc

1. Crie um cluster kubernetes em sua escolha de infraestrutura (VMware vSphere, Amazon Web Services, Google Cloud Platform, etc.). 

    > [!NOTE]
    > Como o Arc habilitado para o Azure kubernetes atualmente dá suporte apenas à anexação de clusters kubernetes existentes ao Arc do Azure, os clientes são obrigados a criar e gerenciar o ciclo de vida do próprio cluster kubernetes.  

1. Inicie o registro de arco do Azure para seu cluster usando CLI do Azure.
    * CLI do Azure usa Helm para implantar o gráfico Helm do agente no cluster.
    * Os nós de cluster iniciam uma comunicação de saída para o [registro de contêiner da Microsoft](https://github.com/microsoft/containerregistry) e efetuam pull das imagens necessárias para criar os seguintes agentes no `azure-arc` namespace:

        | Agente | Descrição |
        | ----- | ----------- |
        | `deployment.apps/clusteridentityoperator` | O kubernetes habilitado para Arc do Azure atualmente dá suporte apenas a [identidades atribuídas do sistema](../../active-directory/managed-identities-azure-resources/overview.md). `clusteridentityoperator` inicia a primeira comunicação de saída. Essa primeira comunicação busca o certificado de Identidade de Serviço Gerenciada (MSI) usado por outros agentes para comunicação com o Azure. |
        | `deployment.apps/config-agent` | Observa o cluster conectado para obter os recursos de configuração de controle do código-fonte aplicados no cluster. Atualiza o estado de conformidade. |
        | `deployment.apps/controller-manager` | Um operador de operadores que orquestra as interações entre os componentes Arc do Azure. |    
        | `deployment.apps/metrics-agent` | Coleta métricas de outros agentes ARC para verificar o desempenho ideal. |
        | `deployment.apps/cluster-metadata-operator` | Coleta metadados do cluster, incluindo a versão do cluster, a contagem de nós e a versão do agente do Azure Arc. |
        | `deployment.apps/resource-sync-agent` | Sincroniza os metadados de cluster mencionados acima para o Azure. |
        | `deployment.apps/flux-logs-agent` | Coleta logs dos operadores de fluxo implantados como parte da configuração de controle do código-fonte. |
    
1. Depois que todos os pods do agente kubernetes habilitados para o Azure Arc estiverem em `Running` estado, verifique se o cluster está conectado ao arco do Azure. Você deve ver:
    * Um recurso kubernetes habilitado para o Azure Arc no [Azure Resource Manager](../../azure-resource-manager/management/overview.md). O Azure acompanha esse recurso como uma projeção do cluster kubernetes gerenciado pelo cliente, não o próprio cluster kubernetes propriamente dito.
    * Os metadados de cluster (como a versão kubernetes, a versão do agente e o número de nós) são exibidos no recurso kubernetes habilitado para Arc do Azure como metadados.

## <a name="data-exchange-between-cluster-environment-and-azure"></a>Troca de dados entre o ambiente de cluster e o Azure

| Tipo de dados | Cenário | Modo de comunicação |
| --------- | -------- | ------------------ |
| Versão do cluster do Kubernetes | Metadados do cluster | Pushes do agente para o Azure |
| Número de nós no cluster | Metadados do cluster | Pushes do agente para o Azure |
| Versão do agente | Metadados do cluster | Pushes do agente para o Azure |
| Tipo de distribuição kubernetes | Metadados do cluster | CLI do Azure envios por push para o Azure |
| Tipo de infraestrutura (AWS/GCP/vSphere/...) | Metadados do cluster | CLI do Azure envios por push para o Azure |
| contagem de vCPU de nós no cluster | Cobrança | CLI do Azure envios por push para o Azure |
| Pulsação do agente | Integridade de recursos | Pushes do agente para o Azure |
| Consumo de recursos (memória/CPU) por agentes | Diagnóstico e suporte | Pushes do agente para o Azure |
| Logs de todos os contêineres de agente | Diagnóstico e suporte | Pushes do agente para o Azure |
| Disponibilidade de atualização do agente | Atualização do agente | Pull do agente do Azure |
| Estado desejado da configuração: URL do repositório git, parâmetros do operador de fluxo, chave privada, conteúdo de hosts conhecidos, nome de usuário HTTPS, token ou senha | Configuração | Pull do agente do Azure |
| Status da instalação do operador de fluxo | Configuração | Pushes do agente para o Azure |
| Azure Policy atribuições que precisam de imposição de gatekeeper no cluster | Azure Policy | Pull do agente do Azure |
| Status de auditoria e conformidade de imposição de política no cluster | Azure Policy | Pushes do agente para o Azure |
| Métricas e logs de cargas de trabalho do cliente | Azure Monitor | O agente envia por push para Log Analytics recurso de espaço de trabalho no locatário e assinatura do cliente |

## <a name="connectivity-status"></a>Status da conectividade

| Status | Descrição |
| ------ | ----------- |
| Connecting | O recurso kubernetes habilitado para Arc do Azure é criado no Azure Resource Manager, mas o serviço ainda não recebeu a pulsação do agente. |
| Conectado | O serviço kubernetes habilitado para Arc do Azure recebeu uma pulsação do agente em algum momento nos 15 minutos anteriores. |
| Offline | O recurso kubernetes habilitado para Arc do Azure foi conectado anteriormente, mas o serviço não recebeu nenhuma pulsação do agente por 15 minutos. |
| Expirado | O certificado MSI tem uma janela de expiração de 90 dias após ser emitido. Depois que esse certificado expirar, o recurso será considerado `Expired` e todos os recursos, como configuração, monitoramento e a política, deixarão de funcionar nesse cluster. Para obter mais informações sobre como lidar com os recursos de kubernetes habilitados para Arc do Azure com vencimento podem ser encontrados [no artigo de perguntas frequentes](./faq.md#how-to-address-expired-azure-arc-enabled-kubernetes-resources). |

## <a name="understand-connectivity-modes"></a>Entender os modos de conectividade

| Modo de conectividade | Descrição |
| ----------------- | ----------- |
| Totalmente conectado | Os agentes podem se comunicar consistentemente com o Azure com pouco atraso na propagação de configurações de GitOps, na imposição de políticas de Azure Policy e de gatekeeper e na coleta de métricas de carga de trabalho e logs em Azure Monitor. |
| Semiconectado | O certificado MSI extraído pelo `clusteridentityoperator` é válido por até 90 dias antes de o certificado expirar. Após a expiração, o recurso kubernetes habilitado para o Azure ARC para de funcionar. Para reativar todos os recursos de arco do Azure no cluster, exclua e recrie os agentes e recurso kubernetes habilitados para Arc do Azure. Durante os 90 dias, conecte o cluster pelo menos uma vez a cada 30 dias. |
| Desconectado | Clusters kubernetes em ambientes desconectados não é possível acessar o Azure atualmente não tem suporte no kubernetes habilitado para Arc do Azure. Se esse recurso for de seu interesse, envie ou vote em uma ideia no [Fórum UserVoice do arco do Azure](https://feedback.azure.com/forums/925690-azure-arc).

## <a name="next-steps"></a>Próximas etapas

* Percorra nosso guia de início rápido para [conectar um cluster kubernetes ao Azure Arc](./connect-cluster.md).
* Saiba mais sobre a criação de conexões entre o cluster e um repositório Git como um [recurso de configuração com o Azure Arc habilitado kubernetes](./conceptual-configurations.md).

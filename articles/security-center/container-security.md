---
title: Segurança de Contêineres na Central de Segurança do Azure | Microsoft Docs
description: Saiba mais sobre os recursos de segurança de contêineres da Central de Segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/07/2021
ms.author: memildin
ms.openlocfilehash: eb70a31d0fa5f231bd0db8ca27517ce43fe1db28
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007799"
---
# <a name="container-security-in-security-center"></a>Segurança de contêineres na Central de Segurança

A Central de Segurança do Azure é a solução nativa do Azure para proteger os contêineres.

A Central de Segurança pode proteger os seguintes tipos de recursos de contêiner:

| Tipo de recurso | Proteções oferecidas pela Central de Segurança |
|:--------------------:|-----------|
| ![Serviço do Kubernetes](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png)<br>**Clusters do AKS (Serviço de Kubernetes do Azure)** | – Avaliação contínua das configurações dos clusters AKS para fornecer visibilidade sobre as configurações incorretas e diretrizes para ajudar você a resolver eventuais problemas descobertos.<br>[Saiba mais sobre a proteção de ambientes por meio das recomendações de segurança](#environment-hardening).<br><br>– Proteção contra ameaças para clusters AKS e nós do Linux. Os alertas de atividades suspeitas são fornecidos pelo opcional [Azure Defender para Kubernetes](defender-for-kubernetes-introduction.md).<br>[Saiba mais sobre a proteção em tempo de execução para nós e clusters AKS](#run-time-protection-for-aks-nodes-and-clusters).|
| ![Host de contêiner](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png)<br>**Hosts de contêiner**<br>(VMs executando o Docker) | – Avaliação contínua das configurações do Docker para fornecer visibilidade sobre as configurações incorretas e diretrizes para ajudar você a resolver eventuais problemas descobertos com o opcional [Azure Defender para Servidores](defender-for-servers-introduction.md).<br>[Saiba mais sobre a proteção de ambientes por meio das recomendações de segurança](#environment-hardening).|
| ![Registro de contêiner](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png)<br>**Registros do ACR (Registro de Contêiner do Azure)** | – Ferramentas de avaliação e gerenciamento de vulnerabilidade para as imagens dos registros do ACR baseados no Azure Resource Manager com o opcional [Azure Defender para Registros de Contêiner](defender-for-container-registries-introduction.md).<br>[Saiba mais sobre como verificar suas imagens de contêiner em busca de vulnerabilidades](#vulnerability-management---scanning-container-images). |
|||

Este artigo descreve como você pode usar a Central de Segurança, juntamente com os planos opcionais do Azure Defender para Registros de Contêiner, Servidores e Kubernetes, para melhorar, monitorar e manter a segurança dos contêineres e dos aplicativos.

Você aprenderá como a Central de Segurança ajuda nesses aspectos essenciais da segurança de contêineres:

- [Gerenciamento de vulnerabilidades – Verificação de imagens de contêiner](#vulnerability-management---scanning-container-images)
- [Proteção de ambiente](#environment-hardening)
- [Proteção em tempo de execução para nós e clusters AKS](#run-time-protection-for-aks-nodes-and-clusters)

A captura de tela a seguir mostra a página de estoque de ativos e os diversos tipos de recursos de contêiner protegidos pela Central de Segurança.

:::image type="content" source="./media/container-security/container-security-tab.png" alt-text="Recursos relacionados a contêineres na página do estoque de ativos da Central de Segurança" lightbox="./media/container-security/container-security-tab.png":::

## <a name="vulnerability-management---scanning-container-images"></a>Gerenciamento de vulnerabilidades – Verificação de imagens de contêiner

Para monitorar imagens nos registros de contêiner do Azure baseados no Azure Resource Manager, habilite o [Azure Defender para Registros de Contêiner](defender-for-container-registries-introduction.md). A Central de Segurança verifica todas as imagens recebidas nos últimos 30 dias, enviadas por push ao registro ou importadas. O verificador integrado é fornecido pela Qualys, fornecedora líder do setor de verificação de vulnerabilidades.

Quando forem encontrados problemas – seja pela Qualys ou pela Central de Segurança – você será notificado no [dashboard do Azure Defender](azure-defender-dashboard.md). Para cada vulnerabilidade, a Central de Segurança fornece recomendações práticas, juntamente com uma classificação de severidade e diretrizes sobre como corrigir o problema. Para obter detalhes das recomendações da Central de Segurança para contêineres, confira a [lista de referência de recomendações](recommendations-reference.md#recs-compute).

A Central de Segurança filtra e classifica as descobertas do verificador. Quando uma imagem está íntegra, a Central de Segurança marca a imagem como tal. A Central de Segurança gera recomendações de segurança apenas para as imagens que têm problemas a serem resolvidos. Notificando você apenas quando há problemas, a Central de Segurança reduz o potencial de alertas informativos indesejados.

## <a name="environment-hardening"></a>Proteção de ambiente

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Monitoramento contínuo da configuração do Docker

A Central de Segurança do Azure identifica contêineres não gerenciados hospedados em VMs IaaS do Linux ou em outros computadores Linux que executam contêineres do Docker. A Central de Segurança avalia continuamente as configurações desses contêineres. Em seguida, ela as compara com o [benchmark do CIS (Center for Internet Security) do Docker](https://www.cisecurity.org/benchmark/docker/).

A Central de Segurança inclui todo o conjunto de regras do benchmark do CIS do Docker e alerta você se os contêineres não atendem a nenhum dos controles. Quando encontra configurações incorretas, a Central de Segurança gera recomendações de segurança. Use a **página recomendações** da Central de Segurança para exibir recomendações e corrigir problemas. As verificações de benchmark do CIS não são executadas em instâncias gerenciadas pelo AKS ou em VMs gerenciadas pelo Databricks.

Para obter detalhes sobre as recomendações relevantes da Central de Segurança que podem aparecer para esse recurso, confira a [seção de computação](recommendations-reference.md#recs-compute) da tabela de referência de recomendações.

Quando você estiver explorando os problemas de segurança de uma VM, a Central de Segurança fornecerá informações adicionais sobre os contêineres no computador. Essas informações incluem a versão do Docker e o número de imagens em execução no host. 

Para monitorar os contêineres não gerenciados, hospedados em VMs IaaS do Linux, habilite o opcional [Azure Defender para Servidores](defender-for-servers-introduction.md).


### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>Monitoramento contínuo dos clusters Kubernetes
A Central de Segurança funciona juntamente com o AKS (Serviço de Kubernetes do Azure), o serviço de orquestração de contêiner gerenciado da Microsoft para desenvolver, implantar e gerenciar aplicativos em contêineres.

O AKS fornece controles de segurança e visibilidade sobre a postura de segurança de seus clusters. A Central de Segurança usa esses recursos para monitorar constantemente a configuração dos seus clusters do AKS e gerar recomendações de segurança alinhadas com os padrões do setor.

Este é um diagrama de alto nível da interação entre a Central de Segurança do Azure, o Serviço de Kubernetes do Azure e o Azure Policy:

:::image type="content" source="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png" alt-text="Arquitetura de alto nível da interação entre a Central de Segurança do Azure, o Serviço de Kubernetes do Azure e o Azure Policy" lightbox="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png":::

Você pode ver que os itens recebidos e analisados pela Central de Segurança incluem:

- logs de auditoria do servidor de API
- eventos de segurança brutos do agente do Log Analytics

    > [!NOTE]
    > Atualmente, não há suporte para a instalação do agente do Log Analytics nos clusters do Serviço de Kubernetes do Azure que estão em execução em conjuntos de dimensionamento de máquinas virtuais.

- informações de configuração de cluster do cluster do AKS
- configuração da carga de trabalho do Azure Policy (por meio do **complemento do Azure Policy para Kubernetes**)

Para obter detalhes sobre as recomendações relevantes da Central de Segurança que podem aparecer para esse recurso, confira a [seção de computação](recommendations-reference.md#recs-compute) da tabela de referência de recomendações.


###  <a name="workload-protection-best-practices-using-kubernetes-admission-control"></a>Melhores práticas de proteção de cargas de trabalho usando o controle de admissão do Kubernetes

Para obter um pacote de recomendações a fim de proteger as cargas de trabalho dos contêineres do Kubernetes, instale o **complemento do Azure Policy para Kubernetes**. Você também pode implantar automaticamente esse complemento, conforme explicado em [Habilitar o provisionamento automático de extensões](security-center-enable-data-collection.md#enable-auto-provisioning-of-extensions). Quando o provisionamento automático para o complemento é definido como "ativado", a extensão fica habilitada por padrão em todos os clusters existentes e futuros (que atendem aos requisitos de instalação do complemento).

Conforme explicado [na página do Azure Policy para Kubernetes](../governance/policy/concepts/policy-for-kubernetes.md), o complemento estende o software livre [Gatekeeper v3](https://github.com/open-policy-agent/gatekeeper) webhook controlador de admissão para [ Agente de política aberto](https://www.openpolicyagent.org/). Os controladores de admissão do Kubernetes são plug-ins que impõem como os clusters são usados. O complemento é registrado como um webhook para o controle de admissão do Kubernetes e possibilita a aplicação de imposições em escala e proteções nos clusters de maneira centralizada e consistente. 

Com o complemento no cluster do AKS, todas as solicitações para o servidor de API do Kubernetes serão monitoradas em relação ao conjunto predefinido de melhores práticas antes de serem persistidas no cluster. Em seguida, você pode configurá-lo para **impor** as melhores práticas e exigir o uso dele em cargas de trabalho futuras. 

Por exemplo, você poderá proibir a criação de contêineres privilegiados, e todas as futuras solicitações para fazer isso serão bloqueadas.

Saiba mais em [Proteger as cargas de trabalho do Kubernetes](kubernetes-workload-protections.md).


## <a name="run-time-protection-for-aks-nodes-and-clusters"></a>Proteção em tempo de execução para nós e clusters AKS

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]



## <a name="next-steps"></a>Próximas etapas

Nesta visão geral, você aprendeu sobre os principais elementos da segurança de contêineres na Central de Segurança do Azure. Para obter materiais relacionados, confira:

- [Introdução ao Azure Defender para Kubernetes](defender-for-kubernetes-introduction.md)
- [Introdução ao Azure Defender para Registros de Contêiner](defender-for-container-registries-introduction.md)
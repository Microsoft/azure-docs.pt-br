---
title: Políticas de suporte para o AKS (Serviço de Kubernetes do Azure)
description: Saiba mais sobre as políticas de suporte do AKS (Serviço de Kubernetes do Azure), a responsabilidade compartilhada e os recursos que estão na versão prévia (ou alfa ou beta).
services: container-service
ms.topic: article
ms.date: 09/18/2020
ms.openlocfilehash: 86b1c0bba30b41a2ee17cfbdf05286c4d2b3fb8a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91892703"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Políticas de suporte para o Serviço de Kubernetes do Azure

Este artigo fornece detalhes sobre as políticas de suporte técnico e as limitações do AKS (Serviço de Kubernetes do Azure). O artigo também detalha o gerenciamento de nó do agente, componentes do plano de controle gerenciado, componentes de software livre de terceiros e gerenciamento de segurança ou patch.

## <a name="service-updates-and-releases"></a>Atualizações de serviço e versões

* Para obter informações de versão, confira as [Notas sobre a versão do AKS](https://github.com/Azure/AKS/releases).
* Para obter informações sobre os recursos na versão prévia, confira [Versões prévias dos recursos do AKS e os projetos relacionados](https://awesomeopensource.com/projects/aks?categoryPage=11).

## <a name="managed-features-in-aks"></a>Recursos gerenciados no AKS

Os componentes de nuvem de IaaS (infraestrutura como serviço) de base, como componentes de computação ou de rede, permitem o acesso a controles de baixo nível e opções de personalização. Por outro lado, o AKS fornece uma implantação kubernetes completa que oferece o conjunto comum de configurações e recursos de que você precisa para o cluster. Como usuário do AKS, você tem opções limitadas de personalização e implantação. No Exchange, você não precisa se preocupar nem gerenciar os clusters kubernetes diretamente.

Com o AKS, você obtém um *plano de controle* totalmente gerenciado. O plano de controle contém todos os componentes e serviços que você precisa para operar e fornecer clusters kubernetes para os usuários finais. Todos os componentes do Kubernetes são mantidos e operados pela Microsoft.

A Microsoft gerencia e monitora os seguintes componentes usando o painel de controle:

* Servidor de API do Kubelet ou do Kubernetes
* Etcd ou um repositório de valor-chave compatível, fornecendo QoS (Qualidade de Serviço), escalabilidade e runtime
* Serviços DNS (por exemplo, kube-dns ou CoreDNS)
* Proxy ou rede Kubernetes
* Qualquer complemento ou componente do sistema adicional em execução no namespace Kube-System

O AKS não é uma solução de PaaS (plataforma como serviço). Alguns componentes, como nós de agente, têm *responsabilidade compartilhada*, em que os usuários devem ajudar a manter o cluster AKs. A entrada do usuário é necessária, por exemplo, para aplicar um patch de segurança do sistema operacional do nó do agente (SO).

Os serviços são *gerenciados* no sentido de que a Microsoft e a equipe do AKS implantam, operam e são responsáveis pela disponibilidade e pela funcionalidade do serviço. Os clientes não podem alterar esses componentes gerenciados. A Microsoft limita a personalização para garantir uma experiência de usuário consistente e escalonável. Para obter uma solução totalmente personalizável, confira o [mecanismo do AKS](https://github.com/Azure/aks-engine).

## <a name="shared-responsibility"></a>Responsabilidade compartilhada

Quando um cluster é criado, você define os nós de agente kubernetes que o AKS cria. Suas cargas de trabalho são executadas nesses nós.

Como os nós de agente executam código particular e armazenam dados confidenciais, Suporte da Microsoft pode acessá-los apenas de uma maneira muito limitada. Suporte da Microsoft não pode entrar no, executar comandos no ou exibir logs para esses nós sem a permissão ou assistência expressa.

Qualquer modificação feita diretamente nos nós de agente usando qualquer uma das APIs de IaaS renderiza o cluster sem suporte. Qualquer modificação feita nos nós de agente deve ser feita usando mecanismos kubernetes-Native, como `Daemon Sets` .

Da mesma forma, embora você possa adicionar quaisquer metadados ao cluster e aos nós, como marcas e rótulos, a alteração de qualquer um dos metadados criados pelo sistema renderizará o cluster sem suporte.

## <a name="aks-support-coverage"></a>Cobertura de suporte do AKS

A Microsoft fornece suporte técnico para os seguintes exemplos:

* Conectividade com todos os componentes do Kubernetes que o serviço de Kubernetes oferece e dá suporte, como o servidor de API.
* Gerenciamento, tempo de atividade, QoS e operações dos serviços de plano de controle kubernetes (plano de controle kubernetes, servidor de API, etcd e coreDNS, por exemplo).
* Armazenamento de dados etcd. O suporte inclui backups automatizados e transparentes de todos os dados do etcd a cada 30 minutos para planejamento de desastre e restauração de estado do cluster. Esses backups não estão diretamente disponíveis para você ou para nenhum usuário. Eles garantem a confiabilidade e a consistência dos dados. Etcd. a reversão ou restauração sob demanda não tem suporte como um recurso.
* Qualquer ponto de integração no driver do provedor de nuvem do Azure para Kubernetes. Isso inclui integrações em outros serviços do Azure, como balanceadores de carga, volumes persistentes ou rede (Kubernetes e CNI do Azure).
* Perguntas ou problemas sobre a personalização de componentes do plano de controle, como o servidor de API kubernetes, o etcd e o coreDNS.
* Problemas sobre redes, como CNI do Azure, kubenet ou outros problemas de acesso e funcionalidade de rede. Os problemas podem incluir resolução de DNS, perda de pacotes, roteamento e assim por diante. A Microsoft dá suporte a vários cenários de rede:
  * Kubenet e Azure CNI usando VNETs gerenciados ou com sub-redes personalizadas (traga sua própria).
  * Conectividade com outros serviços e aplicativos do Azure
  * Controladores de entrada e configurações de entrada ou do balanceador de carga
  * Desempenho e latência de rede


> [!NOTE]
> Todas as ações de cluster tomadas pela Microsoft/AKS são feitas com consentimento do usuário em uma função interna kubernetes `aks-service` e Associação de função interna `aks-service-rolebinding` . Essa função permite que o AKS solucione problemas e diagnostique problemas de cluster, mas não pode modificar permissões nem criar funções ou associações de função nem outras ações de alto privilégio. O acesso à função só é habilitado em Tíquetes de suporte ativos com acesso JIT (just-in-time).

A Microsoft não fornece suporte técnico para os seguintes exemplos:

* Perguntas sobre como usar o Kubernetes. Por exemplo, o Suporte da Microsoft não faz recomendações sobre como criar controladores de entrada personalizados, usar cargas de trabalho de aplicativos ou aplicar pacotes ou ferramentas de software livre ou de terceiros.
  > [!NOTE]
  > O Suporte da Microsoft pode recomendar a funcionalidade, a personalização e o ajuste do cluster do AKS (por exemplo, problemas e procedimentos de operações do Kubernetes).
* Projetos de software livre de terceiros que não são fornecidos como parte do painel de controle do Kubernetes ou implantados com clusters do AKS. Esses projetos podem incluir Istio, Helm, Envoy ou outros.
  > [!NOTE]
  > A Microsoft pode fornecer suporte de melhor esforço para projetos de software livre de terceiros, como o Helm. Nos casos em que a ferramenta de software livre de terceiros se integra ao provedor de nuvem do Azure no Kubernetes ou outros bugs específicos do AKS, a Microsoft dá suporte a exemplos e aplicativos da documentação da Microsoft.
* Software closed-source de terceiros. Esse software pode incluir ferramentas de verificação de segurança e software ou dispositivos de rede.
* Personalizações de rede que não aquelas listadas na [documentação do AKS](./index.yml).


## <a name="aks-support-coverage-for-agent-nodes"></a>Cobertura de suporte do AKS para nós de agente

### <a name="microsoft-responsibilities-for-aks-agent-nodes"></a>Responsabilidades da Microsoft para nós de agente AKS

A Microsoft e os usuários compartilham a responsabilidade de nós do agente kubernetes em que:

* A imagem base do sistema operacional tem adições necessárias (como agentes de rede e monitoramento).
* Os nós de agente recebem patches do sistema operacional automaticamente.
* Os problemas com os componentes do plano de controle kubernetes executados nos nós do agente são corrigidos automaticamente. Esses componentes incluem o seguinte:
  * `Kube-proxy`
  * Túneis de rede que fornecem caminhos de comunicação para os componentes mestres do Kubernetes
  * `Kubelet`
  * `Moby` ou `ContainerD`

> [!NOTE]
> Se um nó de agente não estiver operacional, o AKS poderá reiniciar componentes individuais ou todo o nó do agente. Essas operações de reinicialização são automatizadas e fornecem correção automática para problemas comuns. Se você quiser saber mais sobre os mecanismos de correção automática, confira [reparo automático do nó](node-auto-repair.md)

### <a name="customer-responsibilities-for-aks-agent-nodes"></a>Responsabilidades do cliente para nós do agente AKS

A Microsoft fornece patches e novas imagens para seus nós de imagem semanalmente, mas não os corrige automaticamente por padrão. Para manter o sistema operacional do nó do agente e OS componentes de tempo de execução corrigidos, você deve manter um agendamento de [atualização de imagem de nó](node-image-upgrade.md) regular ou automatizá-lo.

Da mesma forma, o AKS lança regularmente novos patches do kubernetes e versões secundárias. Essas atualizações podem conter aprimoramentos de segurança ou funcionalidade para o kubernetes. Você é responsável por manter a versão de kubernetes de clusters atualizada e de acordo com a [política de versão de suporte do AKS kubernetes](supported-kubernetes-versions.md).

#### <a name="user-customization-of-agent-nodes"></a>Personalização do usuário de nós de agente
> [!NOTE]
> Os nós de agente AKS aparecem no portal do Azure como recursos comuns de IaaS do Azure. Mas essas máquinas virtuais são implantadas em um grupo de recursos do Azure personalizado (geralmente prefixado com MC_ \* ). Você não pode alterar a imagem do sistema operacional base ou fazer personalizações diretas para esses nós usando as APIs ou os recursos de IaaS. Todas as alterações personalizadas que não forem feitas por meio da API AKS não persistirão por uma atualização, escala, atualização ou reinicialização. Evite executar alterações nos nós do agente, a menos que Suporte da Microsoft o Direcione para fazer alterações.

O AKS gerencia o ciclo de vida e as operações de nós de agente em seu nome-modificar os recursos de IaaS associados aos nós de agente **não tem suporte**. Um exemplo de uma operação sem suporte é personalizar um conjunto de dimensionamento de máquinas virtuais do pool de nós alterando manualmente as configurações por meio do portal do conjunto de dimensionamento de máquinas virtuais ou da API.
 
Para configurações ou pacotes específicos de carga de trabalho, o AKS recomenda o uso de [kubernetes `daemon sets` ](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/).

O uso de `daemon sets` contêineres com privilégios e kubernetes de inicialização permite ajustar/modificar ou instalar software de terceiros em nós de agente de cluster. Os exemplos dessas personalizações incluem adicionar software personalizado de verificação de segurança ou atualizar configurações de sysctl.

Embora esse caminho seja recomendado se os requisitos acima se aplicarem, a engenharia e o suporte do AKS não poderão ajudar a solucionar problemas ou diagnosticar modificações que colocam o nó indisponível devido a uma implantação personalizada `daemon set` .

### <a name="security-issues-and-patching"></a>Problemas de segurança e aplicação de patch

Se uma falha de segurança for encontrada em um ou mais dos componentes gerenciados do AKS, a equipe do AKS aplicará patches a todos os clusters afetados para atenuar o problema. Como alternativa, a equipe dará diretrizes de atualização aos usuários.

Para nós de agente afetados por uma falha de segurança, a Microsoft o notificará com detalhes sobre o impacto e as etapas para corrigir ou atenuar o problema de segurança (normalmente uma atualização de imagem de nó ou uma atualização de patch de cluster).

### <a name="node-maintenance-and-access"></a>Manutenção e acesso do nó

Embora você possa entrar no e alterar nós de agente, não é recomendável fazer essa operação porque as alterações podem tornar um cluster sem suporte.

## <a name="network-ports-access-and-nsgs"></a>Portas de rede, acesso e NSGs

Você só pode personalizar o NSGs em sub-redes personalizadas. Você não pode personalizar o NSGs em sub-redes gerenciadas ou no nível de NIC dos nós de agente. O AKS tem requisitos de egresso para pontos de extremidade específicos, para controlar a saída e garantir a conectividade necessária, consulte [limitar o tráfego de saída](limit-egress-traffic.md).

## <a name="stopped-or-de-allocated-clusters"></a>Clusters interrompidos ou desalocados

Como mencionado anteriormente, a desalocação manual de todos os nós de cluster por meio de APIs/CLI/portal de IaaS renderiza o cluster fora do suporte. A única maneira com suporte para parar/desalocar todos os nós é [parar o cluster AKs](start-stop-cluster.md#stop-an-aks-cluster), que preserva o estado do cluster por até 12 meses.

Os clusters que são interrompidos por mais de 12 meses não preservarão mais o estado. 

Os clusters desalocados fora das APIs AKS não têm garantias de preservação de estado. Os planos de controle para clusters nesse estado serão arquivados após 30 dias e excluídos após 12 meses.

O AKS reserva-se o direito de arquivar painéis de controle que foram configurados fora das diretrizes de suporte por longos períodos iguais e superiores a 30 dias. O AKS mantém backups dos metadados do cluster etcd e pode realocar o cluster imediatamente. Essa realocação pode ser iniciada por qualquer operação PUT que retorne o cluster para suporte, como uma atualização ou escala para os nós de agente ativos.

Se sua assinatura for suspensa ou excluída, o plano de controle e o estado do cluster serão excluídos após 90 dias.

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Recursos de Kubernetes alfa e beta sem suporte

O AKS só dá suporte a recursos estáveis e beta no projeto upstream kubernetes. A menos que seja documentado de outra forma, o AKS não dá suporte a nenhum recurso alfa disponível no projeto kubernetes upstream.

## <a name="preview-features-or-feature-flags"></a>Versões prévias dos recursos ou sinalizadores de recurso

Para recursos e funcionalidades que exigem testes estendidos e comentários do usuário, a Microsoft lança novos recursos ou recursos de visualização por trás de um sinalizador de recurso. Considere esses recursos como recursos beta ou de pré-lançamento.

As versões prévias dos recursos ou os recursos do sinalizador de recurso não são destinados à produção. As alterações contínuas em APIs e comportamento, correções de bugs e outras alterações podem resultar em clusters e tempo de inatividade instáveis.

Os recursos na versão prévia pública estão sob o suporte de "melhor esforço", pois esses recursos estão em versão prévia e não são destinados à produção e recebem suporte das equipes de suporte técnico do AKS somente durante o horário comercial. Para obter mais informações, consulte:

* [Perguntas frequentes sobre o suporte do Azure.](https://azure.microsoft.com/support/faq/)

## <a name="upstream-bugs-and-issues"></a>Problemas e bugs de upstream

Devido à velocidade de desenvolvimento no projeto upstream do Kubernetes, invariavelmente surgem bugs. Alguns desses bugs não podem ser corrigidos ou solucionados no sistema do AKS. Em vez disso, as correções de bugs exigem patches maiores para projetos upstream (como kubernetes, sistemas operacionais de nó ou agente e kernel). Para os componentes detidos pela Microsoft (como o provedor de nuvem do Azure), as equipes do AKS e do Azure estão comprometidas em corrigir problemas de upstream na comunidade.

Quando um problema de suporte técnico é causado por um ou mais bugs de upstream, as equipes de suporte e engenharia do AKS vão:

* Identificar e vincular os bugs anteriores com os detalhes de suporte para ajudar a explicar por que esse problema afeta o cluster ou a carga de trabalho. Os clientes recebem links para os repositórios necessários para que possam observar os problemas e ver quando uma nova versão fornecerá correções.
* Fornecer possíveis soluções alternativas ou mitigação. Se o problema puder ser mitigado, um [problema conhecido](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue) será arquivado no repositório do AKS. O arquivamento de problemas conhecidos explica:
  * O problema, incluindo links para bugs de upstream.
  * A solução alternativa e os detalhes sobre uma atualização ou outra persistência da solução.
  * Linhas do tempo aproximadas para a inclusão do problema, com base na cadência da versão de upstream.

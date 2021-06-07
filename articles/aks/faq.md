---
title: Perguntas frequentes sobre o Serviço de Kubernetes do Azure (AKS)
description: Encontre respostas para algumas das perguntas mais comuns sobre o AKS (Serviço de Kubernetes do Azure).
ms.topic: conceptual
ms.date: 08/06/2020
ms.custom: references_regions
ms.openlocfilehash: 6d7ea48722e6604fe67d7a4ddcb12870623d9354
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739620"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Perguntas frequentes sobre o Serviço de Kubernetes do Azure (AKS)

Este artigo aborda as perguntas frequentes sobre o AKS (Serviço de Kubernetes do Azure).

## <a name="which-azure-regions-currently-provide-aks"></a>Quais regiões do Azure atualmente fornecem o AKS?

Para obter uma lista completa das regiões disponíveis, confira [Regiões e disponibilidade do AKS][aks-regions].

## <a name="can-i-spread-an-aks-cluster-across-regions"></a>Posso distribuir um cluster do AKS entre regiões?

Não. Os clusters AKS são recursos regionais e não podem abranger regiões. Confira [Melhores práticas de continuidade dos negócios e recuperação de desastres][bcdr-bestpractices] para obter diretrizes sobre como criar uma arquitetura que inclui várias regiões.

## <a name="can-i-spread-an-aks-cluster-across-availability-zones"></a>Posso distribuir um cluster do AKS entre zonas de disponibilidade?

Sim. Você pode implantar um cluster do AKS em uma ou mais [zonas de disponibilidade][availability-zones] nas [regiões que dão suporte a elas][az-regions].

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Posso limitar quem tem acesso ao servidor de API do Kubernetes?

Sim. Há duas opções para limitar o acesso ao servidor de API:

- Usar [intervalos de IP autorizados do servidor de API][api-server-authorized-ip-ranges] se você quiser manter um ponto de extremidade público para o servidor de API, mas quiser restringir o acesso a um conjunto de intervalos de IP confiáveis.
- Usar [um cluster particular][private-clusters] se quiser limitar o servidor de API para ser acessível *somente* na sua rede virtual.

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Posso ter diferentes tamanhos de VM em um só cluster?

Sim, você pode usar diferentes tamanhos de máquina virtual no seu cluster do AKS criando [vários pools de nós][multi-node-pools].

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>As atualizações de segurança são aplicadas aos nós do agente do AKS?

O Azure aplica automaticamente os patches de segurança aos nós do Linux no cluster de acordo com uma agenda noturna. No entanto, você é responsável por garantir que esses nós do Linux sejam reinicializados conforme necessário. Você tem várias opções para reinicializar os nós:

- Manualmente, por meio do portal do Azure ou da CLI do Azure.
- Atualizando o cluster AKS. O cluster atualiza automaticamente [nós de cordon e frenagem][cordon-drain] e coloca um novo nó online com a última imagem do Ubuntu e uma nova versão de patch ou uma versão secundária do Kubernetes. Para obter mais informações, confira [Atualizar um cluster do AKS][aks-upgrade].
- Usando a [atualização de imagem de nó](node-image-upgrade.md).

### <a name="windows-server-nodes"></a>Nós do Windows Server

Para os nós do Windows Server, o Windows Update não executa automaticamente e aplica as atualizações mais recentes. Regularmente, de acordo com o ciclo de lançamento do Windows Update e o próprio processo de validação, você deverá executar uma atualização no cluster e nos pools de nó do Windows Server no cluster do AKS. Esse processo de atualização cria nós que executam a imagem e os patches mais recentes do Windows Server e, em seguida, remove os nós mais antigos. Para obter mais informações sobre esse processo, confira [Atualizar um pool de nós no AKS][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Por que são criados dois grupos de recursos com o AKS?

O AKS baseia-se em vários recursos da infraestrutura do Azure, incluindo conjuntos de dimensionamento de máquinas virtuais, redes virtuais e discos gerenciados. Isso permite que você aproveite muitas das funcionalidades básicas da plataforma Azure no ambiente do Kubernetes gerenciado fornecido pelo AKS. Por exemplo, a maioria dos tipos de máquinas virtuais do Azure pode ser usada diretamente com o AKS e as Reservas do Azure podem ser usadas para receber descontos nesses recursos automaticamente.

Para habilitar essa arquitetura, cada implantação do AKS abrange dois grupos de recursos:

1. Você cria o primeiro grupo de recursos. Esse grupo contém apenas o recurso do Serviço de Kubernetes. O provedor de recursos do AKS cria automaticamente o segundo grupo de recursos durante a implantação. Um exemplo do segundo grupo de recursos é *MC_myResourceGroup_myAKSCluster_eastus*. Para obter informações sobre como especificar o nome desse segundo grupo de recursos, confira a próxima seção.
1. O segundo grupo de recursos, conhecido como o *grupo de recursos do nó*, contém todos os recursos de infraestrutura associados ao cluster. Esses recursos incluem as máquinas virtuais do nó do Kubernetes, rede virtual e armazenamento. Por padrão, o grupo de recursos do nó tem um nome como *MC_myResourceGroup_myAKSCluster_eastus*. O AKS exclui automaticamente o recurso de nó sempre que o cluster é excluído, portanto, ele só deve ser usado para recursos que compartilham o ciclo de vida do cluster.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>Posso fornecer um nome personalizado para o grupo de recursos do nó no AKS?

Sim. Por padrão, o AKS nomeará o grupo de recursos do nó *MC_resourcegroupname_clustername_location*, mas você também poderá fornecer um nome personalizado.

Para especificar um nome do grupo de recursos, instale a extensão da CLI do Azure [aks-preview][aks-preview-cli] versão *0.3.2* ou posterior. Ao criar um cluster AKS usando o comando [AZ AKs Create][az-aks-create] , use o `--node-resource-group` parâmetro e especifique um nome para o grupo de recursos. Se você [usar um modelo do Azure Resource Manager][aks-rm-template] para implantar um cluster do AKS, defina o nome do grupo de recursos usando a propriedade *nodeResourceGroup*.

* O grupo de recursos secundário é criado automaticamente pelo provedor de recursos do Azure na própria assinatura.
* Você poderá especificar um nome de grupo de recursos personalizado somente quando estiver criando o cluster.

Ao trabalhar com o grupo de recursos do nó, tenha em mente que não é possível:

* Especificar um grupo de recursos existente para o grupo de recursos do nó.
* Especificar uma assinatura diferente para o grupo de recursos do nó.
* Alterar o nome do grupo de recursos do nó depois que o cluster for criado.
* Especificar nomes para os recursos gerenciados dentro do grupo de recursos do nó.
* Modificar ou excluir marcas criadas pelo Azure de recursos gerenciados dentro do grupo de recursos do nó. (Confira as informações adicionais na próxima seção.)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>Posso modificar marcas e outras propriedades dos recursos do AKS no grupo de recursos do nó?

Se você modificar ou excluir as marcas criadas pelo Azure e outras propriedades de recursos no grupo de recursos do nó, poderá obter resultados inesperados, como erros de dimensionamento e atualização. O AKS permite criar e modificar marcas personalizadas criadas por usuários finais, e você pode adicionar essas marcas ao [criar um pool de nós](use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool). O ideal é criar ou modificar marcas personalizadas, por exemplo, para atribuir uma unidade de negócios ou um centro de custo. Isso também pode ser obtido com a criação de políticas do Azure com um escopo no grupo de recursos gerenciado.

No entanto, a modificação de **marcas criadas pelo Azure** em recursos no grupo de recursos de nó no cluster AKs é uma ação sem suporte, que interrompe o Slo (objetivo de nível de serviço). Para obter mais informações, confira [O AKS oferece um SLA?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Quais os controles de admissão de Kubernetes que o AKS suporta? Controladores de admissão podem ser adicionados ou removidos?

O AKS dá suporte aos seguintes [controladores de admissão][admission-controllers]:

- *NamespaceLifecycle*
- *LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*
- *PodNodeSelector*
- *PodTolerationRestriction*
- *ExtendedResourceToleration*

No momento, não é possível modificar a lista de controladores de admissão no AKS.

## <a name="can-i-use-admission-controller-webhooks-on-aks"></a>Posso usar webhooks do controlador de admissão no AKS?

Sim, você pode usar webhooks do controlador de admissão no AKS. É recomendável que você exclua os namespaces internos do AKS, que são marcados com o **rótulo do plano de controle.** Por exemplo, adicionando o código abaixo à configuração do webhook:

```
namespaceSelector:
    matchExpressions:
    - key: control-plane
      operator: DoesNotExist
```

Os firewalls AKS a saída do servidor de API para que os WebHooks do controlador de admissão precisem ser acessados de dentro do cluster.

## <a name="can-admission-controller-webhooks-impact-kube-system-and-internal-aks-namespaces"></a>Os webhooks do controlador de admissão podem afetar os namespaces internos do AKS e do kube-system?

Para proteger a estabilidade do sistema e impedir que os controladores de admissão personalizados afetem os serviços internos no namespace do kube-system, o AKS tem uma **Imposição de Admissões**, que exclui automaticamente os namespaces internos do kube-system e do AKS. Esse serviço verifica se os controladores de admissão personalizados não afetam os serviços em execução no kube-system.

Se você tiver um caso de uso crítico para ter algo implantado no kube-system (não recomendado), que precisa ser coberto pelo webhook de admissão personalizado, adicione o rótulo ou a anotação abaixo para que a Imposição de Admissões o ignore.

Rótulo: ```"admissions.enforcer/disabled": "true"``` ou Anotação: ```"admissions.enforcer/disabled": true```

## <a name="is-azure-key-vault-integrated-with-aks"></a>O Azure Key Vault é integrado com o AKS?

Atualmente, o AKS não está integrado nativamente ao Azure Key Vault. No entanto, o [provedor do Azure Key Vault para o repositório de segredos do CSI][csi-driver] permite a integração direta dos pods do Kubernetes aos segredos do Key Vault.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Posso executar contêineres do Windows Server no AKS?

Sim, os contêineres do Windows Server estão disponíveis no AKS. Para executar os contêineres do Windows Server no AKS, crie um pool de nós que executa o Windows Server como o SO convidado. Os contêineres do Windows Server podem usar apenas o Windows Server 2019. Para começar a usá-lo, confira [Criar um cluster do AKS com um pool de nós do Windows Server][aks-windows-cli].

O suporte do Windows Server para pools de nós inclui algumas limitações que fazem parte do Windows Server upstream no projeto do Kubernetes. Para obter mais informações sobre essas limitações, confira [Limitações de contêineres do Windows Server no AKS][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>O AKS oferece um SLA?

O AKS fornece garantias de SLA como um recurso complementar opcional com [SLA de tempo de atividade][uptime-sla]. 

O SKU gratuito oferecido por padrão não tem um *contrato* de nível de serviço associado, mas tem um *objetivo* de nível de serviço de 99,5%. Pode acontecer que problemas de conectividade transitórios sejam observados em caso de atualizações, nós underlay não íntegros, manutenção de plataforma, sobrecarregar o aplicativo do servidor de API com solicitações etc. Se sua carga de trabalho não tolerar reinicializações do servidor de API, sugerimos o uso do SLA de tempo de atividade.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Posso aplicar descontos de reserva do Azure aos meus nós de agente do AKS?

Os nós de agente AKS são cobrados como máquinas virtuais do Azure padrão. portanto, se você comprou as [reservas do Azure][reservation-discounts] para o tamanho da VM que você está usando em AKs, esses descontos serão aplicados automaticamente.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>Posso mover/migrar meu cluster entre locatários do Azure?

No momento, não há suporte para a movimentação do cluster AKS entre locatários.

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>Posso mover/migrar meu cluster entre assinaturas?

No momento, não há suporte para a movimentação de clusters entre assinaturas.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>Posso mover meus clusters do AKS da assinatura atual do Azure para outra?

Não há suporte para a movimentação do cluster AKS e seus recursos associados entre assinaturas do Azure.

## <a name="can-i-move-my-aks-cluster-or-aks-infrastructure-resources-to-other-resource-groups-or-rename-them"></a>Posso mover meu cluster AKS ou recursos de infraestrutura AKS para outros grupos de recursos ou renomeá-los?

Não há suporte para mover ou renomear o cluster AKS e seus recursos associados.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>Por que a exclusão do meu cluster demora tanto?

A maioria dos clusters é excluída mediante solicitação do usuário; em alguns casos, especialmente quando os clientes trazem o próprio grupo de recursos ou fazem a exclusão de tarefas entre grupos de recursos, isso pode levar mais tempo ou falhar. Se você tiver um problema com as exclusões, verifique se você não tem bloqueios no RG, se todos os recursos fora do RG estão desassociados do RG e assim por diante.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>Se eu tiver um pod/implantações no estado 'NodeLost' ou 'Unknown', ainda poderei atualizar o meu cluster?

Você pode, mas AKS não recomenda isso. As atualizações devem ser executadas quando o estado do cluster for conhecido e íntegro.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>Se eu tiver um cluster com um ou mais nós em um estado não íntegro ou desligado, poderei executar uma atualização?

Não, exclua/remova todos os nós em um estado de falha ou removidos do cluster antes da atualização.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>Executei uma exclusão de cluster, mas o erro `[Errno 11001] getaddrinfo failed` é exibido

Normalmente, isso é causado pelo fato de os usuários terem um ou mais NSGs (grupos de segurança de rede) ainda em uso e associados ao cluster.  Remova-os e tente excluir novamente.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>Executei uma atualização, mas agora meus pods estão em loops de falha e as investigações de preparação falham.

Confirme se sua entidade de serviço não expirou.  Consulte: [entidade de serviço AKs](./kubernetes-service-principal.md) e [credenciais de atualização do AKS](./update-credentials.md).

## <a name="my-cluster-was-working-but-suddenly-cant-provision-loadbalancers-mount-pvcs-etc"></a>Meu cluster estava funcionando, mas repentinamente não pode provisionar balanceadores de carga, PVCs de montagem, etc.?

Confirme se sua entidade de serviço não expirou.  Consulte: [entidade de serviço AKs](./kubernetes-service-principal.md)  e [credenciais de atualização do AKS](./update-credentials.md).

## <a name="can-i-scale-my-aks-cluster-to-zero"></a>Posso dimensionar meu cluster AKS para zero?
Você pode [interromper completamente um cluster AKS em execução](start-stop-cluster.md), economizando nos respectivos custos de computação. Além disso, você também pode optar por [dimensionar ou fazer o dimensionamento automático de todos ou de `User` pools de nós específicos](scale-cluster.md#scale-user-node-pools-to-0) para 0, mantendo apenas a configuração de cluster necessária.
Não é possível dimensionar diretamente os [pools de nós do sistema](use-system-pools.md) para zero.

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>Posso usar as APIs do conjunto de dimensionamento de máquinas virtuais para executar uma operação de escala manual?

Não. Não há suporte para operações de escala por meio das APIs do conjunto de dimensionamento de máquinas virtuais. Use as APIs do AKS (`az aks scale`).

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-zero-nodes"></a>Posso usar conjuntos de dimensionamento de máquinas virtuais para dimensionar manualmente para nós zero?

Não. Não há suporte para operações de escala por meio das APIs do conjunto de dimensionamento de máquinas virtuais. Você pode usar a API do AKS para dimensionar para zero pools de nó que não são do sistema ou [parar o cluster](start-stop-cluster.md) em vez disso.

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>Posso interromper ou desalocar todas as minhas VMs?

Embora o AKS tenha mecanismos de resiliência para resistir a essa configuração e recuperá-la, essa não é uma configuração com suporte. Em vez disso, [pare o cluster](start-stop-cluster.md) .

## <a name="can-i-use-custom-vm-extensions"></a>Posso usar extensões de VM personalizadas?

O agente de Log Analytics tem suporte porque é uma extensão gerenciada pela Microsoft. Caso contrário, não, o AKS é um serviço gerenciado e não há suporte para a manipulação dos recursos de IaaS. Para instalar componentes personalizados, use as APIs e os mecanismos do kubernetes. Por exemplo, use DaemonSets para instalar os componentes necessários.

## <a name="does-aks-store-any-customer-data-outside-of-the-clusters-region"></a>O AKS armazena dados de clientes fora da região do cluster?

O recurso para habilitar o armazenamento de dados do cliente em uma única região está disponível no momento apenas na região do Sudeste Asiático (Cingapura) da região do Pacífico Asiático geo e do Sul do Brasil (estado de São Paulo) da geografia do Brasil. Para todas as outras regiões, os dados do cliente são armazenados na Área geográfica.

## <a name="are-aks-images-required-to-run-as-root"></a>As imagens AKS são necessárias para executar como raiz?

Exceto pelas duas imagens a seguir, as imagens AKS não são necessárias para executar como raiz:

- *mcr.microsoft.com/oss/kubernetes/coredns*
- *mcr.microsoft.com/azuremonitor/containerinsights/ciprod*

## <a name="what-is-azure-cni-transparent-mode-vs-bridge-mode"></a>O que é o modo transparente do Azure CNI vs. modo de ponte?

Do v 1.2.0 Azure CNI terá o modo transparente como padrão para implantações de CNI do Linux de locação única. O modo transparente está substituindo o modo de ponte. Nesta seção, discutiremos mais sobre as diferenças de ambos os modos e quais são os benefícios/limitações para usar o modo transparente no Azure CNI.

### <a name="bridge-mode"></a>Modo de ponte

Como o nome sugere, o modo de ponte do Azure CNI, em uma forma "Just-in-time", criará uma ponte L2 chamada "azure0". Todas as interfaces de par de Pod do lado do host `veth` serão conectadas a essa ponte. Portanto, Pod-Pod comunicação intra VM e o tráfego restante passa por essa ponte. A ponte em questão é um dispositivo virtual de camada 2 que, por sua vez, não pode receber ou transmitir nada, a menos que você associe um ou mais dispositivos reais a ele. Por esse motivo, o eth0 da VM do Linux deve ser convertido em uma ponte subordinada para "azure0". Isso cria uma topologia de rede complexa na VM do Linux e, como um sintoma, CNI precisou cuidar de outras funções de rede, como atualização do servidor DNS e assim por diante.

:::image type="content" source="media/faq/bridge-mode.png" alt-text="Topologia do modo de ponte":::

Abaixo está um exemplo de como a configuração de rota de IP é parecida no modo de ponte. Independentemente de quantos pods o nó tem, haverá apenas duas rotas. O primeiro que diz, todo o tráfego, excluindo local em azure0, vai para o gateway padrão da sub-rede por meio da interface com IP "src 10.240.0.4" (que é o IP primário do nó) e o segundo que diz o espaço pod "10.20. x. x" a ser usado pelo kernel para núcleo.

```bash
default via 10.240.0.1 dev azure0 proto dhcp src 10.240.0.4 metric 100
10.240.0.0/12 dev azure0 proto kernel scope link src 10.240.0.4
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1 linkdown
root@k8s-agentpool1-20465682-1:/#
```

### <a name="transparent-mode"></a>Modo transparente
O modo transparente usa uma abordagem direta para configurar a rede do Linux. Nesse modo, o Azure CNI não alterará nenhuma propriedade da interface eth0 na VM do Linux. Essa abordagem mínima de alteração das propriedades de rede do Linux ajuda a reduzir os problemas de caso de canto complexos que os clusters podem enfrentar com o modo de ponte. No modo transparente, o Azure CNI criará e adicionará interfaces de par de Pod do lado do host `veth` que serão adicionadas à rede do host. A comunicação Pod a Pod da VM é por meio de rotas IP que o CNI adicionará. Essencialmente, a comunicação Pod a pod é sobre a camada 3 e o tráfego pod é roteado pelas regras de roteamento L3.

:::image type="content" source="media/faq/transparent-mode.png" alt-text="Topologia de modo transparente":::

Abaixo está um exemplo de configuração de rota de IP do modo transparente, a interface de cada pod obterá uma rota estática anexada para que o tráfego com IP de destino como o Pod seja enviado diretamente para a interface de par host do pod `veth` .

```bash
10.240.0.216 dev azv79d05038592 proto static
10.240.0.218 dev azv8184320e2bf proto static
10.240.0.219 dev azvc0339d223b9 proto static
10.240.0.222 dev azv722a6b28449 proto static
10.240.0.223 dev azve7f326f1507 proto static
10.240.0.224 dev azvb3bfccdd75a proto static
168.63.129.16 via 10.240.0.1 dev eth0 proto dhcp src 10.240.0.4 metric 100
169.254.169.254 via 10.240.0.1 dev eth0 proto dhcp src 10.240.0.4 metric 100
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1 linkdown
```

### <a name="benefits-of-transparent-mode"></a>Benefícios do modo transparente

- Fornece mitigação para a `conntrack` condição de corrida paralela do DNS e evitar problemas de latência de DNS de 5 segundos sem a necessidade de configurar o DNS local do nó (você ainda pode usar o DNS local do nó por motivos de desempenho).
- Elimina o modo de ponte CNI de latência de DNS de 5 segundos inicial que apresenta hoje devido à configuração de ponte "Just-in-time".
- Um dos casos de canto no modo de ponte é que o CNI do Azure não pode continuar atualizando as listas de servidores DNS personalizados que os usuários adicionam à VNET ou NIC. Isso resulta no CNI selecionando apenas a primeira instância da lista de servidores DNS. Resolvido no modo transparente, pois CNI não altera nenhuma propriedade eth0. Veja mais [aqui](https://github.com/Azure/azure-container-networking/issues/713).
- Fornece melhor tratamento do tráfego UDP e mitigação para o Storm de inundação UDP quando o ARP atinge o tempo limite. No modo de ponte, quando a ponte não sabe um endereço MAC do pod de destino na comunicação de Pod a Pod da VM, por design, isso resulta no Storm do pacote para todas as portas. Resolvido no modo transparente, pois não há dispositivos L2 no caminho. Veja mais [aqui](https://github.com/Azure/azure-container-networking/issues/704).
- O modo transparente tem melhor desempenho na comunicação Pod a Pod da VM em termos de taxa de transferência e latência quando comparado ao modo de ponte.

## <a name="how-to-avoid-permission-ownership-setting-slow-issues-when-the-volume-has-a-lot-of-files"></a>Como evitar que a propriedade de permissão defina problemas lentos quando o volume tiver muitos arquivos?

Tradicionalmente, se o Pod estiver sendo executado como um usuário não raiz (que você deve), você deverá especificar um `fsGroup` dentro do contexto de segurança do pod para que o volume possa ser legível e gravável pelo Pod. Esse requisito é abordado em mais detalhes [aqui](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/).

Mas um efeito colateral da configuração `fsGroup` é que, cada vez que um volume é montado, o kubernetes deve recursivamente `chown()` e `chmod()` todos os arquivos e diretórios dentro do volume, com algumas exceções indicadas abaixo. Isso acontece mesmo que a propriedade de grupo do volume já corresponda à solicitada `fsGroup` e pode ser muito dispendiosa para volumes maiores com muitos arquivos pequenos, o que faz com que a inicialização do pod demore muito tempo. Esse cenário tem sido um problema conhecido antes de v 1,20 e a solução alternativa é definir o Pod executar como raiz:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 0
    fsGroup: 0
```

O problema foi resolvido por kubernetes v 1,20, consulte [Kubernetes 1,20: controle granular das alterações de permissão de volume](https://kubernetes.io/blog/2020/12/14/kubernetes-release-1.20-fsgroupchangepolicy-fsgrouppolicy/) para obter mais detalhes.


<!-- LINKS - internal -->

[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./cluster-autoscaler.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration-cli.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az-aks-create
[aks-rm-template]: /azure/templates/microsoft.containerservice/2019-06-01/managedclusters
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: ./windows-faq.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md
[availability-zones]: ./availability-zones.md
[private-clusters]: ./private-clusters.md
[bcdr-bestpractices]: ./operator-best-practices-multi-region.md#plan-for-multiregion-deployment
[availability-zones]: ./availability-zones.md
[az-regions]: ../availability-zones/az-region.md
[uptime-sla]: ./uptime-sla.md

<!-- LINKS - external -->
[aks-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
[csi-driver]: https://github.com/Azure/secrets-store-csi-driver-provider-azure
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines/

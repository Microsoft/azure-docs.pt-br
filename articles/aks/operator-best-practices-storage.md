---
title: Melhores práticas do operador - Armazenamento nos Serviços do Kubernetes do Azure (AKS)
description: Aprenda as práticas recomendadas do operador de cluster para armazenamento, criptografia de dados e backups no Serviço de Kubernetes do Azure (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 5/6/2019
ms.author: mlearned
ms.openlocfilehash: b42cdae634a6c2d8d994225d4cb6b440a99918e5
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614587"
---
# <a name="best-practices-for-storage-and-backups-in-azure-kubernetes-service-aks"></a>Práticas recomendadas para armazenamento e backups no Serviço de Kubernetes do Azure (AKS)

À medida que você cria e gerencia clusters no Serviço de Kubernetes do Azure (AKS), os aplicativos geralmente precisam de armazenamento. É importante entender as necessidades de desempenho e acessar os métodos de pods para que você possa fornecer o armazenamento adequado aos aplicativos. O tamanho do nó do AKS pode afetar essas opções de armazenamento. Você também deve planejar maneiras de fazer backup e testar o processo de restauração para o armazenamento anexado.

Este artigo de práticas recomendadas se concentra em considerações de armazenamento para operadores de cluster. Neste artigo, você aprende:

> [!div class="checklist"]
> * Quais tipos de armazenamento estão disponíveis
> * Como dimensionar corretamente os nós do AKS para desempenho de armazenamento
> * Diferenças entre o provisionamento dinâmico e estático de volumes
> * Maneiras de fazer backup e proteger seus volumes de dados

## <a name="choose-the-appropriate-storage-type"></a>Escolher o tipo de armazenamento apropriado

**Guia de práticas recomendadas** - entender as necessidades de seu aplicativo para escolher o armazenamento correto. Use armazenamento de alto desempenho com apoios de SSDs para cargas de trabalho de produção. Planeje o armazenamento baseado em rede se houver a necessidade de haver várias conexões simultâneas.

Os aplicativos geralmente exigem diferentes tipos e velocidades de armazenamento. Seus aplicativos precisam de armazenamento que se conecta a pods individuais ou compartilhados entre vários pods? O armazenamento destina-se ao acesso somente leitura aos dados ou para gravar grandes quantidades de dados estruturados? Esse armazenamento precisa determinar o tipo mais apropriado de armazenamento a ser usado.

A tabela a seguir descreve os tipos de armazenamento disponíveis e suas funcionalidades:

| Caso de uso | Plug-in de volume | Ler/gravar uma vez | Muitos somente leitura | Muitos ler/gravar | Suporte de contêiner do Windows Server |
|----------|---------------|-----------------|----------------|-----------------|--------------------|
| Configuração compartilhada       | Arquivos do Azure   | Sim | Sim | Sim | Sim |
| Dados de aplicativo estruturados        | Discos do Azure   | Sim | Não  | Não  | Sim |
| Dados não estruturados, operações do sistema de arquivos | [BlobFuse (versão prévia)][blobfuse] | Sim | Sim | sim | Não |

Os dois principais tipos de armazenamento fornecidos para volumes no AKS são apoiados pelos Discos do Azure ou Arquivos do Azure. Para melhorar a segurança, os dois tipos de armazenamento usam a Criptografia do Serviço de Armazenamento do Azure (SSE) por padrão, que criptografa os dados em repouso. No momento, os discos não podem ser criptografados usando a Criptografia de Disco do Azure no nível do nó do AKS.

Os Arquivos do Azure estão atualmente disponíveis no nível de desempenho padrão. Os Discos do Azure estão disponíveis nos níveis de desempenho Standard e Premium:

- Os discos *Premium* são apoiados por discos de estado sólido e alto desempenho (SSDs). Os discos Premium são recomendados para todas as cargas de trabalho de produção.
- Os discos *Standard* são apoiados por discos giratórios regulares (HDDs) e são bons para arquivamento ou dados pouco acessados.

Entenda as necessidades de desempenho do aplicativo e os padrões de acesso para escolher o nível de armazenamento apropriado. Para obter mais informações sobre tamanhos de discos gerenciados e os níveis de desempenho, consulte [visão geral do Azure Managed Disks][managed-disks]

### <a name="create-and-use-storage-classes-to-define-application-needs"></a>Criar e usar as classes de armazenamento para definir as necessidades do aplicativo

O tipo de armazenamento usado é definido usando *classes de armazenamento* do Kubernetes. Em seguida, a classe de armazenamento é referenciada na especificação do pod ou da implantação. Essas definições trabalham em conjunto para criar o armazenamento adequado e conectá-lo aos pods. Para obter mais informações, consulte [classes de armazenamento no AKS][aks-concepts-storage-classes].

## <a name="size-the-nodes-for-storage-needs"></a>Dimensionar os nós para necessidades de armazenamento

**Guias de práticas recomendadas** - cada tamanho de nó oferece suporte a um número máximo de discos. Diferentes tamanhos de nós também fornecem quantidades diferentes de largura de banda de rede e de armazenamento local. Planeje suas demandas de aplicativos para implantar o tamanho apropriado de nós.

Os nós do AKS são executados como VMs do Azure. Tamanhos e tipos diferentes de VMs estão disponíveis. Cada tamanho de VM fornece uma quantidade diferente de recursos essenciais, como a CPU e a memória. Esses tamanhos VM têm um número máximo de discos que podem ser anexados. O desempenho do armazenamento também varia entre os tamanhos de VM para as IOPS (operações de entrada/saída por segundo) máximas do disco local e anexado.

Se seus aplicativos exigirem os Discos do Azure como solução de armazenamento, planeje e escolha um tamanho apropriado de VM do nó. Ao escolher um tamanho de VM, a quantidade de memória e CPU não será o único fator. Os recursos de armazenamento também são importantes. Por exemplo, ambos tamanhos de VM, *Standard_B2ms* e *Standard_DS2_v2*, incluem uma quantidade similar de recursos de CPU e memória. O desempenho do armazenamento potencial é diferente, conforme mostrado na tabela a seguir:

| Tamanho e tipo de nó | vCPU | Memória (GiB) | Discos de dados máximos | IOPS de discos com taxa de transferência sem cache | Taxa de transferência máxima não armazenada em cache (MBps) |
|--------------------|------|--------------|----------------|------------------------|--------------------------------|
| Standard_B2ms      | 2    | 8            | 4              | 1\.920                  | 22,5                           |
| Standard_DS2_v2    | 2    | 7            | 8              | 6\.400                  | 96                             |

Aqui, o *Standard_DS2_v2* permite duplicar o número de discos anexados e fornece três a quatro vezes a quantidade de IOPS e a taxa de transferência do disco. Se só tiver analisado os recursos de computação principais e comparado os custos, é possível que, ao escolher o tamanho de VM *Standard_B2ms*, você tenha desempenho e limitações de armazenamento insatisfatórios. Trabalhe com sua equipe de desenvolvimento de aplicativos para entender suas necessidades de capacidade de armazenamento e desempenho. Escolha o tamanho de VM apropriado para os nós do AKS para atender ou superar suas necessidades de desempenho. Crie aplicativos de linha de base regularmente para ajustar o tamanho da VM conforme necessário.

Para obter mais informações sobre tamanhos de VM disponíveis, consulte [tamanhos para máquinas virtuais Linux no Azure][vm-sizes].

## <a name="dynamically-provision-volumes"></a>Provisionar volumes dinamicamente

**Guia de práticas recomendadas** – para reduzir a sobrecarga de gerenciamento e permitir o dimensionamento, não crie e atribua volumes persistentes de forma estática. Use o provisionamento dinâmico. Nas classes de armazenamento, defina a política de recuperação apropriada para minimizar os custos de armazenamento desnecessários depois que os pods forem excluídos.

Você usa volumes persistentes quando precisa anexar o armazenamento aos pods. Esses volumes persistentes podem ser criados de forma manual ou dinâmica. A criação manual de volumes persistentes adiciona sobrecarga de gerenciamento e limita sua capacidade de dimensionar. Use o provisionamento dinâmico do volume persistente para simplificar o gerenciamento do armazenamento e permitir que seus aplicativos se desenvolvam e sejam dimensionados conforme necessário.

![Declarações de volumes persistentes em um cluster de serviços de Kubernetes do Azure (AKS)](media/concepts-storage/persistent-volume-claims.png)

Uma declaração de volume persistente (PVC) permite criar dinamicamente o armazenamento conforme necessário. Os discos do Azure subjacentes são criados conforme as solicitações dos pods. Na definição do pod, você solicita que um volume seja criado e anexado a um caminho de montagem projetado

Para os conceitos sobre como criar e usar volumes dinamicamente, consulte [declarações de Volumes persistentes][aks-concepts-storage-pvcs].

Para ver esses volumes em ação, consulte como criar e usar um volume persistente com dinamicamente [discos do Azure][dynamic-disks] or [Azure Files][dynamic-files].

Como parte de suas definições de classe de armazenamento, defina o *reclaimPolicy* adequado. Este reclaimPolicy controla o comportamento do recurso de armazenamento do Azure subjacente quando o pod é excluído e o volume persistente talvez não seja mais necessário. O recurso de armazenamento subjacente pode ser excluído ou retido para uso com um pod futuro. O reclaimPolicy pode ser definido como *reter* ou *excluir*. Entenda as necessidades de seu aplicativo e implemente verificações regulares de armazenamento retido para minimizar a quantidade usada e cobrada de armazenamento não usado.

Para obter mais informações sobre as opções de classe de armazenamento, consulte [armazenamento recuperar políticas][reclaim-policy].

## <a name="secure-and-back-up-your-data"></a>Proteja e faça backup de seus dados

**Diretrizes de práticas recomendadas de** – faça backup de seus dados usando uma ferramenta adequada para seu tipo de armazenamento, como Velero ou o Azure Site Recovery. Verifique a integridade e a segurança desses backups.

Quando seus aplicativos armazenam e consomem dados persistentes em discos ou arquivos, você precisa fazer backups ou instantâneos regulares desses dados. Os Discos do Azure podem usar tecnologias internas de instantâneo. Talvez você precise de um gancho para que seus aplicativos liberem as gravações no disco antes de executar a operação de instantâneo. [Velero][velero] can back up persistent volumes along with additional cluster resources and configurations. If you can't [remove state from your applications][remove-state], fazer backup dos dados dos volumes persistentes e testar regularmente as operações de restauração para verificar a integridade dos dados e os processos necessários.

Entenda as limitações das diferentes abordagens de backups de dados e se será preciso desativar os dados antes de tirar o instantâneo. Os backups de dados não necessariamente permitem que você restaure seu ambiente de aplicativo da implantação do cluster. Para obter mais informações sobre esses cenários, consulte [práticas recomendadas para recuperação de desastre e continuidade de negócios no AKS][best-practices-multi-region].

## <a name="next-steps"></a>Próximas etapas

Este artigo se concentra nas práticas recomendadas de armazenamento de no AKS. Para obter mais informações sobre conceitos básicos de armazenamento no Kubernetes, consulte [conceitos de armazenamento para aplicativos no AKS][aks-concepts-storage].

<!-- LINKS - External -->
[velero]: https://github.com/heptio/velero
[blobfuse]: https://github.com/Azure/azure-storage-fuse

<!-- LINKS - Internal -->
[aks-concepts-storage]: concepts-storage.md
[vm-sizes]: ../virtual-machines/linux/sizes.md
[dynamic-disks]: azure-disks-dynamic-pv.md
[dynamic-files]: azure-files-dynamic-pv.md
[reclaim-policy]: concepts-storage.md#storage-classes
[aks-concepts-storage-pvcs]: concepts-storage.md#persistent-volume-claims
[aks-concepts-storage-classes]: concepts-storage.md#storage-classes
[managed-disks]: ../virtual-machines/linux/managed-disks-overview.md
[best-practices-multi-region]: operator-best-practices-multi-region.md
[remove-state]: operator-best-practices-multi-region.md#remove-service-state-from-inside-containers

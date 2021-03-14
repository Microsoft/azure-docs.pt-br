---
title: Fluxo de trabalho de CI/CD usando GitOps-kubernetes habilitado para arco do Azure
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: conceptual
author: tcare
ms.author: tcare
description: Este artigo fornece uma visão geral conceitual de um fluxo de trabalho de CI/CD usando o GitOps
keywords: GitOps, kubernetes, K8s, Azure, Helm, Arc, AKS, serviço kubernetes do Azure, contêineres, CI, CD, Azure DevOps
ms.openlocfilehash: a51a9f2b32f1088cec390dc4d74300a38f37b160
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121772"
---
# <a name="cicd-workflow-using-gitops---azure-arc-enabled-kubernetes"></a>Fluxo de trabalho de CI/CD usando GitOps-kubernetes habilitado para arco do Azure

As implantações modernas do kubernetes abrigam vários aplicativos, clusters e ambientes. Com o GitOps, você pode gerenciar essas configurações complexas com mais facilidade, acompanhando o estado desejado dos ambientes kubernetes declarativamente com o git. Usando ferramentas git comuns para acompanhar o estado do cluster, você pode aumentar a responsabilidade, facilitar a investigação de falhas e habilitar a automação para gerenciar ambientes.

Esta visão geral conceitual explica o GitOps como uma realidade no ciclo de vida completo da alteração do aplicativo usando o Arc do Azure, Azure Repos e Azure Pipelines. [Salte para um exemplo](#example-workflow) de uma única alteração de aplicativo para ambientes kubernetes GitOps.

## <a name="architecture"></a>Arquitetura

Considere um aplicativo implantado em um ou mais ambientes kubernetes.

![Arquitetura de CI/CD do GitOps](./media/gitops-arch.png)

### <a name="application-repo"></a>Repositório de aplicativos
O repositório de aplicativos contém o código do aplicativo no qual os desenvolvedores trabalham durante o loop interno. Os modelos de implantação do aplicativo residem nesse repositório em um formato genérico, como Helm ou Kustomize. Os valores específicos do ambiente não são armazenados. As alterações neste repositório invocam um pipeline de PR ou CI que inicia o processo de implantação.
### <a name="container-registry"></a>Registro de Contêiner
O registro de contêiner contém todas as imagens do primeiro e de terceiros usadas nos ambientes kubernetes. Marque imagens de aplicativo de primeira parte com marcas legíveis e a confirmação de git usada para criar a imagem. Armazene imagens de terceiros em cache para segurança, velocidade e resiliência. Defina um plano para teste e integração oportunos de atualizações de segurança. Para obter mais informações, consulte o guia de [conteúdo do ACR consume e manter público](https://docs.microsoft.com/azure/container-registry/tasks-consume-public-content) para obter um exemplo.
### <a name="pr-pipeline"></a>PR pipeline
O PRs para o repositório de aplicativos é restringido em uma execução bem-sucedida do pipeline de PR. Esse pipeline executa os Gates de qualidade básica, como refiapos e testes de unidade no código do aplicativo. O pipeline testa o aplicativo e fiapos Dockerfiles e Helm modelos usados para implantação em um ambiente kubernetes. As imagens do Docker devem ser criadas e testadas, mas não enviadas por push. Mantenha a duração do pipeline relativamente curta para permitir a iteração rápida.
### <a name="ci-pipeline"></a>Pipeline de CI
O pipeline de CI de aplicativo executa todas as etapas de pipeline de PR e expande as verificações de teste e implantação. O pipeline pode ser executado para cada confirmação ou em uma cadência regular com um grupo de confirmações. Neste estágio, execute testes de aplicativo que sejam muito longos para um pipeline de PR. Envie por push imagens do Docker para o registro de contêiner depois de Compilar na preparação para a implantação. O modelo substituído pode ser reparado com um conjunto de valores de teste. As imagens usadas no tempo de execução do serviço devem ser refeitas, criadas e testadas neste ponto. Na compilação de CI especificamente, os artefatos são publicados para a etapa de CD a ser consumida na preparação para a implantação.
### <a name="flux"></a>Flux
O fluxo é um serviço executado em cada cluster e é responsável por manter o estado desejado. O serviço freqüentemente sonda o repositório GitOps em busca de alterações em seu cluster e as aplica.
### <a name="cd-pipeline"></a>Pipeline de CD
O pipeline de CD é disparado automaticamente por compilações de CI bem-sucedidas. Ele usa os modelos publicados anteriormente, substitui valores de ambiente e abre uma PR para o repositório GitOps para solicitar uma alteração no estado desejado de um ou mais clusters kubernetes. Os administradores de cluster examinam a alteração de estado PR e aprovam a mesclagem para o repositório GitOps. O pipeline aguarda a conclusão da PR, o que permite que o fluxo pegue a alteração de estado.
### <a name="gitops-repo"></a>Repositório GitOps
O repositório GitOps representa o estado atual desejado de todos os ambientes em clusters. Qualquer alteração nesse repositório é coletada pelo serviço de fluxo em cada cluster e implantada. PRs são criados com alterações no estado desejado, revisado e mesclado. Esses PRs contêm alterações nos dois modelos de implantação e nos manifestos kubernetes renderizados resultantes. Os manifestos renderizados de nível baixo permitem uma inspeção mais cuidadosa das alterações normalmente não vistas no nível do modelo.
### <a name="kubernetes-clusters"></a>Clusters do Kubernetes
Pelo menos um dos clusters kubernetes habilitados para o Azure Arc atende aos diferentes ambientes necessários para o aplicativo. Por exemplo, um único cluster pode servir a um ambiente de desenvolvimento e QA por meio de namespaces diferentes. Um segundo cluster pode fornecer uma separação mais fácil de ambientes e um controle mais refinado.
## <a name="example-workflow"></a>Fluxo de trabalho de exemplo
Como desenvolvedor de aplicativos, Alice:
* Grava o código do aplicativo.
* Determina como executar o aplicativo em um contêiner do Docker.
* Define os modelos que executam o contêiner e os serviços dependentes em um cluster kubernetes.

Embora Alice saiba que o aplicativo precisa da capacidade de executar em vários ambientes, ela não sabe as configurações específicas de cada ambiente.

Suponha que Alice queira fazer uma alteração no aplicativo que altere a imagem do Docker usada no modelo de implantação de aplicativo.

1. Alice altera o modelo de implantação, envia-o por push a um Branch remoto no repositório de aplicativos e abre uma PR para revisão.
2. Alice solicita que sua equipe revise a alteração.
    * O pipeline de PR executa a validação.
    * Após uma execução de pipeline bem-sucedida, a equipe se desconecta e a alteração é mesclada.
3. O pipeline de CI valida a alteração de Alice e é concluído com êxito.
    * A alteração é segura para implantar no cluster e os artefatos são salvos na execução do pipeline de CI.
4. A alteração de Alice mescla e dispara o pipeline de CD.
    * O pipeline de CD pega os artefatos armazenados pela execução do pipeline CI de Alice.
    * O pipeline de CD substitui os modelos por valores específicos de ambiente e prepara quaisquer alterações no estado de cluster existente no repositório GitOps.
    * O pipeline de CD cria uma PR para o repositório GitOps com as alterações desejadas no estado do cluster.
5. A equipe de Alice revisa e aprova sua PR.
    * A alteração é mesclada no Branch de destino correspondente ao ambiente.
6. Em minutos, o fluxo observa uma alteração no repositório GitOps e efetua pull da alteração de Alice.
    * Devido à alteração da imagem do Docker, o Pod do aplicativo requer uma atualização.
    * O fluxo aplica a alteração ao cluster.
7. Alice testa o ponto de extremidade do aplicativo para verificar se a implantação foi concluída com êxito.
   > [!NOTE]
   > Para obter mais ambientes destinados à implantação, o pipeline de CD itera criando uma PR para o próximo ambiente e repete as etapas de 4-7. O processo muitos precisam de aprovação extra para implantações ou ambientes mais arriscados, como uma alteração relacionada à segurança ou um ambiente de produção.
8.  Depois que todos os ambientes receberam implantações bem-sucedidas, o pipeline é concluído.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como criar conexões entre o cluster e um repositório Git como um [recurso de configuração com o Azure Arc habilitado kubernetes](./conceptual-configurations.md)

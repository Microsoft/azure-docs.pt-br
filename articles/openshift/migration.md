---
title: Migrar de um Red Hat OpenShift no Azure 3.11 para o Red Hat OpenShift no Azure 4
description: Migrar de um Red Hat OpenShift no Azure 3.11 para o Red Hat OpenShift no Azure 4
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 08/13/2020
keywords: migração, toa, openshift, Red Hat
ms.openlocfilehash: 371672de83a6d745d7b367f8327a64e11059923e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100653271"
---
# <a name="migrate-from-azure-red-hat-openshift-311-to-azure-red-hat-openshift-4"></a>Migrar do Azure Red Hat OpenShift 3,11 para o Azure Red Hat OpenShift 4

O Azure Red Hat OpenShift no OpenShift 4 traz kubernetes 1,16 no sistema operacional Red Hat Core, clusters privados, traga seu próprio suporte de rede virtual e função de administrador de cluster completo. Além disso, muitos recursos novos agora estão disponíveis, como suporte para a estrutura do operador, o Hub do operador e a malha do serviço OpenShift.

Para fazer a transição com êxito do Azure Red Hat OpenShift 3,11 para o Azure Red Hat OpenShift 4, certifique-se de examinar as [diferenças de armazenamento, rede, registro em log, segurança e monitoramento](https://docs.openshift.com/container-platform/4.4/migration/migrating_3_4/planning-migration-3-to-4.html).

Neste artigo, demonstraremos como migrar de um cluster do Azure Red Hat OpenShift 3,11 para um cluster Red Hat 4 do Azure.

> [!NOTE]
> As ferramentas de migração do Red Hat OpenShift, como a ferramenta de assistência de migração do plano de controle e a CAM (ferramenta de migração de aplicativos) de cluster, não podem ser usadas com clusters do Azure Red Hat OpenShift 3,11.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tenha um cluster do Azure Red Hat OpenShift 3,11 existente.

## <a name="create-a-target-azure-red-hat-openshift-4-cluster"></a>Criar um cluster de destino do Azure Red Hat OpenShift 4

Primeiro, [crie o cluster do Azure Red Hat OpenShift 4](tutorial-create-cluster.md) que você gostaria de usar como o cluster de destino. Aqui, usaremos a configuração básica. Se você estiver interessado em configurações diferentes, consulte o [tutorial criar um cluster do Azure Red Hat OpenShift 4](tutorial-create-cluster.md).

Crie uma rede virtual com duas sub-redes vazias para os nós mestre e de trabalho.

```azurecli-interactive
    az network vnet create \
    --resource-group $RESOURCEGROUP \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/22

    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name master-subnet \
    --address-prefixes 10.0.0.0/23 \
    --service-endpoints Microsoft.ContainerRegistry

    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.0.2.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
```

Em seguida, use o comando a seguir para criar o cluster.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet \
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret @pull-secret.txt # [OPTIONAL]
```

## <a name="configure-the-target-openshift-4-cluster"></a>Configurar o cluster OpenShift 4 de destino

### <a name="authentication"></a>Autenticação

Para que os usuários interajam com o Azure Red Hat OpenShift, eles devem primeiro autenticar no cluster. A camada de autenticação identifica o usuário associado às solicitações para a API do Red Hat OpenShift do Azure. Em seguida, a camada de autorização usa informações sobre o usuário solicitante para determinar se a solicitação é permitida.

Quando um cluster do Azure Red Hat OpenShift 4 é criado, um usuário administrativo temporário é criado. [Conecte-se ao cluster](tutorial-connect-cluster.md), adicione usuários e grupos e [Configure as permissões apropriadas](https://docs.openshift.com/container-platform/4.6/authentication/understanding-authentication.html) para ambos.

### <a name="networking"></a>Rede

O Azure Red Hat OpenShift 4 usa alguns operadores diferentes para configurar a rede em seu cluster: [operador de rede de cluster](https://docs.openshift.com/container-platform/4.6/networking/cluster-network-operator.html#nw-cluster-network-operator_cluster-network-operator), [operador DNS](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html)e [operador de entrada](https://docs.openshift.com/container-platform/4.6/networking/ingress-operator.html). Para obter mais informações sobre como configurar a rede em um cluster do Azure Red Hat OpenShift 4, consulte o [diagrama de rede](concepts-networking.md) e [noções básicas sobre rede](https://docs.openshift.com/container-platform/4.6/networking/understanding-networking.html).

### <a name="storage"></a>Armazenamento
O Azure Red Hat OpenShift 4 dá suporte aos seguintes plug-ins do PersistentVolume:

- Repositório de blocos elásticos do AWS (EBS)
- Disco do Azure
- Arquivos do Azure
- GCE de disco persistente
- HostPath
- iSCSI
- Volume local
- NFS
- Armazenamento de contêiner do Red Hat OpenShift

Para obter informações sobre como configurar esses tipos de armazenamento, consulte [Configurando o armazenamento persistente](https://access.redhat.com/documentation/azure_red_hat_openshift/4/html/storage/configuring-persistent-storage).

### <a name="registry"></a>Registro

O Azure Red Hat OpenShift 4 pode criar imagens do seu código-fonte, implantá-las e gerenciar seu ciclo de vida. Para habilitar isso, o Azure Red Hat OpenShift fornece 4 um [registro de imagem de contêiner integrado interno](https://docs.openshift.com/container-platform/4.6/registry/registry-options.html) que pode ser implantado em seu ambiente do Azure Red Hat OpenShift para gerenciar imagens localmente.

Se você estiver usando registros externos, como [registro de contêiner do Azure](../container-registry/index.yml), [registros do Red Hat Quay](https://docs.openshift.com/container-platform/4.6/registry/registry-options.html#registry-quay-overview_registry-options)ou um [registro do Red Hat habilitado para autenticação](https://docs.openshift.com/container-platform/4.6/registry/registry-options.html#registry-authentication-enabled-registry-overview_registry-options), siga as etapas para fornecer credenciais ao cluster para permitir que o cluster acesse os repositórios.

### <a name="monitoring"></a>Monitoramento

O Azure Red Hat OpenShift inclui uma pilha de monitoramento pré-configurada, pré-instalada e de atualização automática baseada no projeto de software livre Prometheus e seu sistema de eco mais amplo. Ele fornece monitoramento de componentes de cluster e inclui um conjunto de alertas para notificar imediatamente o administrador de cluster sobre quaisquer problemas ocorrendo e um conjunto de painéis do Grafana. A pilha de monitoramento de cluster só tem suporte para monitoramento de clusters do Azure Red Hat OpenShift. Para obter mais informações, consulte [monitoramento de cluster para o Azure Red Hat OpenShift](https://docs.openshift.com/container-platform/4.6/monitoring/understanding-the-monitoring-stack.html).

Se você estiver usando [Azure monitor para contêineres do Azure Red Hat OpenShift 3,11](../azure-monitor/containers/container-insights-azure-redhat-setup.md), também poderá habilitar Azure monitor para contêineres para os [clusters Red Hat OpenShift 4 do Azure](../azure-monitor/containers/container-insights-azure-redhat4-setup.md) e continuar usando o mesmo espaço de trabalho log Analytics.

## <a name="move-your-dns-or-load-balancer-configuration-to-the-new-cluster"></a>Mover a configuração de DNS ou balanceador de carga para o novo cluster

Se você estiver usando o Gerenciador de tráfego do Azure, adicione pontos de extremidade para fazer referência ao seu cluster de destino e Priorize esses pontos de extremidade.

## <a name="deploy-application-to-your-target-cluster"></a>Implantar aplicativo em seu cluster de destino

Depois que o cluster de destino estiver configurado corretamente para sua carga de trabalho, [Conecte-se ao cluster](tutorial-connect-cluster.md) e crie os aplicativos, componentes ou serviços necessários para seus projetos. O Azure Red Hat OpenShift permite que você os crie do git, de imagens de contêiner, do catálogo de desenvolvedores do Red Hat, de uma Dockerfile, de uma definição de YAML/JSON ou selecionando um serviço de banco de dados no catálogo.

## <a name="delete-your-source-cluster"></a>Excluir o cluster de origem
Depois de confirmar que o cluster do Azure Red Hat OpenShift 4 está configurado corretamente, exclua seu cluster do Azure Red Hat OpenShift 3,11.

```
az openshift delete --name $CLUSTER_NAME
                    --resource-group $RESOURCE_GROUP
                    [--no-wait]
                    [--subscription]
                    [--yes]
```
## <a name="next-steps"></a>Próximas etapas
Confira a documentação do Red Hat OpenShift [aqui](https://docs.openshift.com/container-platform/4.6/welcome/index.html).

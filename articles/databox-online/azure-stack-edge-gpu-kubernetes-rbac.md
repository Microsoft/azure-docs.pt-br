---
title: Entender o controle de acesso baseado em função do kubernetes no dispositivo pro Edge Azure Stack | Microsoft Docs
description: Descreve como o controle de acesso baseado em função do kubernetes ocorre em um dispositivo pro Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: alkohli
ms.openlocfilehash: 9a9625dcf40ae7d11e1154fc89b7f04652c8ca16
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2020
ms.locfileid: "94635833"
---
# <a name="kubernetes-role-based-access-control-on-your-azure-stack-edge-pro-gpu-device"></a>Controle de acesso baseado em função do kubernetes no seu dispositivo de GPU pro do Azure Stack Edge


No dispositivo Azure Stack Edge pro, quando você configura a função de computação, um cluster kubernetes é criado. Você pode usar o controle de acesso baseado em função do kubernetes (kubernetes RBAC) para limitar o acesso aos recursos de cluster em seu dispositivo.

Este artigo fornece uma visão geral do sistema kubernetes RBAC fornecido pelo kubernetes e como o kubernetes RBAC é implementado em seu dispositivo Azure Stack Edge pro. 

## <a name="kubernetes-rbac"></a>RBAC do Kubernetes

O RBAC kubernetes permite atribuir usuários ou grupos de usuários, permissão para tarefas como criar ou modificar recursos ou exibir logs da execução de cargas de trabalho do aplicativo. Essas permissões podem ser delimitadas para um único namespace ou concedidas em todo o cluster. 

Quando você configura o cluster kubernetes, um único usuário é criado correspondente a esse cluster e é chamado de usuário administrador do cluster.  Um `kubeconfig` arquivo é associado ao usuário administrador do cluster. O `kubeconfig` arquivo é um arquivo de texto que contém todas as informações de configuração necessárias para se conectar ao cluster para autenticar o usuário.

## <a name="namespaces-types"></a>Tipos de namespaces

Recursos de kubernetes, como pods e implantações, são agrupados logicamente em um namespace. Esses agrupamentos fornecem uma maneira de dividir logicamente um cluster kubernetes e restringir o acesso para criar, exibir ou gerenciar recursos. Os usuários podem interagir apenas com recursos dentro de seus namespaces atribuídos.

Os namespaces são destinados ao uso em ambientes com muitos usuários espalhados por várias equipes ou projetos. Para obter mais informações, consulte [Kubernetes namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/).

O dispositivo pro Edge Azure Stack tem os seguintes namespaces:

- **Namespace do sistema** – esse namespace é onde os principais recursos existem, como recursos de rede, como DNS e proxy, ou o painel do kubernetes. Normalmente, você não implantar seus próprios aplicativos para esse namespace. Use esse namespace para depurar qualquer problema de cluster kubernetes. 

    Há vários namespaces de sistema em seu dispositivo e os nomes correspondentes a esses namespaces de sistema são reservados. Aqui está uma lista dos namespaces de sistema reservados: 
    - Kube-sistema
    - metallb-sistema
    - DBE-namespace
    - default
    - kubernetes-painel
    - Kube-concessão de nó
    - Kube – público


    Certifique-se de não usar nenhum nome reservado para namespaces de usuário que você criar. 
<!--- **default namespace** - This namespace is where pods and deployments are created by default when none is provided and you have admin access to this namespace. When you interact with the Kubernetes API, such as with `kubectl get pods`, the default namespace is used when none is specified.-->

- **Namespace de usuário** – esses são os namespaces que você pode criar por meio de **kubectl** ou por meio da interface do PowerShell do dispositivo para implantar aplicativos localmente.
 
- **IOT Edge namespace** -você se conecta a esse `iotedge` namespace para gerenciar aplicativos implantados por meio de IOT Edge.

- **Namespace Arc do Azure** -você se conecta a este `azure-arc` namespace para gerenciar aplicativos implantados por meio do Azure Arc. Com o Arc do Azure, você também pode implantar aplicativos em outros namespaces de usuário. 

## <a name="namespaces-and-users"></a>Namespaces e usuários

No mundo real, é importante dividir o cluster em vários namespaces. 

- **Vários usuários** : se você tiver vários usuários, vários namespaces permitirão que esses usuários implantem seus aplicativos e serviços em seus namespaces específicos isoladamente uns dos outros. 
- **Usuário único** : mesmo que haja um único usuário, vários namespaces permitirão que o usuário executasse várias versões dos aplicativos no mesmo cluster kubernetes.

### <a name="roles-and-rolebindings"></a>Funções e RoleBindings

Kubernetes tem o conceito de função e Associação de função que permite conceder permissões a usuários ou recursos em um nível de namespace e em um nível de cluster. 

- **Funções** : você pode definir permissões para usuários como uma **função** e, em seguida, usar **funções** para conceder permissões em um namespace. 
- **RoleBindings** : depois de definir as funções, você pode usar **RoleBindings** para atribuir funções para um namespace específico. 

Essa abordagem permite separar logicamente um único cluster kubernetes, com os usuários capazes de acessar somente os recursos do aplicativo em seu namespace atribuído. 

## <a name="kubernetes-rbac-on-azure-stack-edge-pro"></a>Kubernetes RBAC no Azure Stack Edge pro

Na implementação atual do RBAC kubernetes, Azure Stack o Edge Pro permite executar as seguintes ações de um runspace do PowerShell restrito:

- Criar namespaces.  
- Crie usuários adicionais.
- Conceda acesso de administrador aos namespaces que você criou. Tenha em mente que você não terá acesso à função de administrador de cluster ou a uma exibição dos recursos de todo o cluster.
- Obtenha `kubeconfig` o arquivo com informações para acessar o cluster kubernetes.


O dispositivo pro Edge Azure Stack tem vários namespaces de sistema e você pode criar namespaces de usuário com `kubeconfig` arquivos para acessar esses namespaces. Os usuários têm controle total sobre esses namespaces e podem criar ou modificar usuários ou conceder acesso a usuários. Somente o administrador do cluster tem acesso completo aos namespaces do sistema e aos recursos de todo o cluster. Um `aseuser` tem acesso somente leitura aos namespaces do sistema.

Aqui está um diagrama que descreve a implementação do RBAC kubernetes no dispositivo Azure Stack Edge pro.

![Kubernetes RBAC no dispositivo pro Edge Azure Stack](./media/azure-stack-edge-gpu-kubernetes-rbac/rbac-view-1.png)

Neste diagrama, Alice, Bob e Chuck têm acesso somente a namespaces de usuário atribuídos, que nesse caso são `ns1` , e, `ns2` `ns3` respectivamente. Nesses namespaces, eles têm acesso de administrador. O administrador de cluster, por outro lado, tem acesso de administrador aos namespaces do sistema e aos recursos de todo o cluster.

Como um usuário, você pode criar namespaces e usuários, atribuir usuários a namespaces ou baixar `kubeconfig` arquivos. Para obter instruções passo a passo detalhadas, acesse [kubernetes cluster via kuebctl em seu Azure Stack Edge pro](azure-stack-edge-gpu-create-kubernetes-cluster.md).


Ao trabalhar com namespaces e usuários em seus dispositivos Azure Stack Edge pro, as seguintes advertências se aplicam:

- Você não tem permissão para executar nenhuma operação, como criar usuários, conceder ou revogar acesso de namespace ao usuário, para qualquer um dos namespaces do sistema. Exemplos de namespaces do sistema incluem,,,, `kube-system` `metallb-system` `kubernetes-dashboard` `default` `kube-node-lease` , `kube-public` . Os namespaces do sistema também incluem os namespaces reservados para tipos de implantação, como `iotedge` (IOT Edge namespace) e `azure-arc` (namespace Arc do Azure).
- Você pode criar namespaces de usuário e dentro desses namespaces criar usuários adicionais e conceder ou revogar o acesso de namespace a esses usuários.
- Você não tem permissão para criar namespaces com nomes idênticos aos de qualquer namespace do sistema. Os nomes dos namespaces do sistema são reservados.  
- Você não tem permissão para criar nenhum namespace de usuário com nomes que já estão em uso por outros namespaces de usuário. Por exemplo, se você tiver um `test-ns` que você criou, não poderá criar outro `test-ns` namespace.
- Você não tem permissão para criar usuários com nomes que já estão reservados. Por exemplo, `aseuser` é um usuário reservado e não pode ser usado.


## <a name="next-steps"></a>Próximas etapas

Para entender como você pode criar um usuário, criar um namespace e conceder acesso de usuário ao namespace, consulte [acessar um cluster kubernetes por meio de kubectl](azure-stack-edge-gpu-create-kubernetes-cluster.md).


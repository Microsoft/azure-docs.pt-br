---
title: Usar Azure Policy para proteger o cluster
description: Use Azure Policy para proteger um cluster do AKS (serviço kubernetes do Azure).
ms.service: container-service
ms.topic: how-to
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: 46e92e6842204cd323992a2561e71302bb9cc722
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102193378"
---
# <a name="secure-your-cluster-with-azure-policy"></a>Proteja seu cluster com o Azure Policy

Para melhorar a segurança do seu cluster do AKS (serviço kubernetes do Azure), você pode aplicar e impor políticas de segurança internas no cluster usando Azure Policy. [Azure Policy][azure-policy] ajuda a impor padrões organizacionais e a avaliar a conformidade em escala. Depois de instalar o [complemento Azure Policy para AKs][kubernetes-policy-reference], você pode aplicar definições de política individuais ou grupos de definições de política chamadas iniciativas (às vezes chamadas de policysets) para o cluster. Consulte [Azure Policy definições internas para AKs][aks-policies] para obter uma lista completa de políticas de AKs e definições de iniciativa.

Este artigo mostra como aplicar definições de política ao cluster e verificar se essas atribuições estão sendo impostas.

## <a name="prerequisites"></a>Pré-requisitos

- Um cluster AKS existente. Se precisar de um cluster do AKS, veja o guia de início rápido do AKS [usando a CLI do Azure][aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].
- O complemento Azure Policy para AKS instalado em um cluster AKS. Siga estas [etapas para instalar o complemento Azure Policy][azure-policy-addon].

## <a name="assign-a-built-in-policy-definition-or-initiative"></a>Atribuir uma definição ou iniciativa de política interna

Para aplicar uma definição de política ou iniciativa, use o portal do Azure.

1. Navegue até o serviço de Azure Policy no portal do Azure.
1. No painel esquerdo da página do Azure Policy, selecione **Definições**.
1. Em **categorias** , selecione `Kubernetes` .
1. Escolha a definição de política ou a iniciativa que você deseja aplicar. Para este exemplo, selecione a `Kubernetes cluster pod security baseline standards for Linux-based workloads` iniciativa.
1. Selecione **Atribuir**.
1. Defina o **escopo** para o grupo de recursos do cluster AKs com o complemento Azure Policy habilitado.
1. Selecione a página **parâmetros** e atualize o **efeito** de `audit` para `deny` para bloquear novas implantações violando a iniciativa de linha de base. Você também pode adicionar namespaces adicionais para excluir da avaliação. Para este exemplo, mantenha os valores padrão.
1. Selecione **revisar + criar** e **criar** para enviar a atribuição de política.

## <a name="validate-a-azure-policy-is-running"></a>Validar um Azure Policy está em execução

Confirme se as atribuições de política são aplicadas ao cluster executando o seguinte:

```azurecli-interactive
kubectl get constrainttemplates
```

> [!NOTE]
> As atribuições de política podem levar [até 20 minutos para serem sincronizadas][azure-policy-assign-policy] em cada cluster.

A saída deve ser semelhante a:

```console
$ kubectl get constrainttemplate
NAME                                     AGE
k8sazureallowedcapabilities              23m
k8sazureallowedusersgroups               23m
k8sazureblockhostnamespace               23m
k8sazurecontainerallowedimages           23m
k8sazurecontainerallowedports            23m
k8sazurecontainerlimits                  23m
k8sazurecontainernoprivilege             23m
k8sazurecontainernoprivilegeescalation   23m
k8sazureenforceapparmor                  23m
k8sazurehostfilesystem                   23m
k8sazurehostnetworkingports              23m
k8sazurereadonlyrootfilesystem           23m
k8sazureserviceallowedports              23m
```

### <a name="validate-rejection-of-a-privileged-pod"></a>Validar a rejeição de um pod privilegiado

Primeiro, vamos testar o que acontece quando você agenda um pod com o contexto de segurança de `privileged: true` . Esse contexto de segurança Escalona os privilégios do pod. A iniciativa não permite pods privilegiada, portanto, a solicitação será negada, resultando na rejeição da implantação.

Crie um arquivo chamado `nginx-privileged.yaml` e cole o seguinte manifesto YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      securityContext:
        privileged: true
```

Crie o Pod com o comando [kubectl Apply][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f nginx-privileged.yaml
```

Como esperado, o Pod não pôde ser agendado, conforme mostrado na seguinte saída de exemplo:

```console
$ kubectl apply -f privileged.yaml

Error from server ([denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}): error when creating "privileged.yaml": admission webhook "validation.gatekeeper.sh" denied the request: [denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}
```

O Pod não chega ao estágio de agendamento, portanto, não há recursos a serem excluídos antes de você prosseguir.

### <a name="test-creation-of-an-unprivileged-pod"></a>Teste de criação de um pod sem privilégios

No exemplo anterior, a imagem de contêiner tentou usar automaticamente a raiz para associar NGINX à porta 80. Essa solicitação foi negada pela iniciativa de política, portanto, o Pod não pôde ser iniciado. Vamos tentar executar o mesmo Pod NGINX sem acesso privilegiado.

Crie um arquivo chamado `nginx-unprivileged.yaml` e cole o seguinte manifesto YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

Crie o Pod usando o comando [kubectl Apply][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f nginx-unprivileged.yaml
```

O Pod foi agendado com êxito. Quando você verifica o status do pod usando o comando [kubectl Get pods][kubectl-get] , o Pod está *em execução*:

```console
$ kubectl get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          18s
```

Este exemplo mostra a iniciativa de linha de base que afeta apenas as implantações que violam as políticas na coleção. As implantações permitidas continuam a funcionar.

Exclua o Pod sem privilégios do NGINX usando o comando [kubectl Delete][kubectl-delete] e especifique o nome do seu manifesto do YAML:

```console
kubectl delete -f nginx-unprivileged.yaml
```

## <a name="disable-a-policy-or-initiative"></a>Desabilitar uma política ou iniciativa

Para remover a iniciativa de linha de base:

1. Navegue até o painel de política no portal do Azure.
1. Selecione **atribuições** no painel esquerdo.
1. Clique no botão **...** ao lado da `Kubernetes cluster pod security baseline standards for Linux-based workloads` iniciativa.
1. Selecione **excluir atribuição**.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como Azure Policy funciona:

- [Visão geral da política do Azure][azure-policy]
- [Azure Policy iniciativas e políticas para AKS][aks-policies]
- Remova o [complemento Azure Policy][azure-policy-addon-remove].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs

<!-- LINKS - internal -->
[aks-policies]: policy-reference.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-policy]: ../governance/policy/overview.md
[azure-policy-addon]: ../governance/policy/concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-aks
[azure-policy-addon-remove]: ../governance/policy/concepts/policy-for-kubernetes.md#remove-the-add-on-from-aks
[azure-policy-assign-policy]: ../governance/policy/concepts/policy-for-kubernetes.md#assign-a-built-in-policy-definition
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[kubernetes-policy-reference]: ../governance/policy/concepts/policy-for-kubernetes.md

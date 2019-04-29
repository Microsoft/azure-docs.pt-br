---
title: Usar políticas de segurança de pod no serviço de Kubernetes do Azure (AKS)
description: Aprenda a controlar admissões pod usando PodSecurityPolicy no serviço de Kubernetes do Azure (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/17/2019
ms.author: iainfou
ms.openlocfilehash: 7ce311ab9c554481f64c6c9be40e2018893a0966
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61027368"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>Preview - proteger o cluster usando as políticas de segurança pod no serviço de Kubernetes do Azure (AKS)

Para melhorar a segurança do cluster do AKS, você pode limitar os pods podem ser agendados. Pods que solicitam recursos que não permitem a você não podem executar no cluster AKS. Você define esse acesso usando as políticas de segurança pod. Este artigo mostra como usar as políticas de segurança pod para limitar a implantação de pods no AKS.

> [!IMPORTANT]
> Recursos de visualização do AKS são Self-service e aceitação. As visualizações são fornecidas para reunir opiniões e bugs de nossa comunidade. No entanto, eles não são suportados pelo suporte técnico do Azure. Se você cria um cluster ou adicionar esses recursos para clusters existentes, há suporte para esse cluster até que o recurso não está mais em visualização e muda para GA (disponibilidade geral).
>
> Se você encontrar problemas com recursos de visualização [abra um problema no repositório GitHub do AKS] [ aks-github] com o nome do recurso de visualização no título do bug.

## <a name="before-you-begin"></a>Antes de começar

Este artigo considera que já existe um cluster do AKS. Se você precisar de um cluster do AKS, confira o guia de início rápido do AKS [Usando a CLI do Azure][aks-quickstart-cli] ou [Usando o portal do Azure][aks-quickstart-portal].

Você precisa da CLI do Azure versão 2.0.61 ou posterior instalado e configurado. Execute  `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, confira  [Instalar a CLI do Azure][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Instalar a extensão da CLI aks-preview

Clusters AKS são atualizadas para habilitar políticas de segurança pod usando o *versão prévia do aks* extensão da CLI. Instalar o *versão prévia do aks* extensão de CLI do Azure usando o [Adicionar extensão az] [ az-extension-add] de comando, conforme mostrado no exemplo a seguir:

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> Se você já tiver instalado o *versão prévia do aks* atualizações de instalação disponível com qualquer extensão, usando o `az extension update --name aks-preview` comando.

### <a name="register-pod-security-policy-feature-provider"></a>Registrar o provedor de recursos de política de segurança de pod

Para criar ou atualizar um cluster do AKS para usar as políticas de segurança pod, primeiro habilite um sinalizador de recursos em sua assinatura. Para registrar o *PodSecurityPolicyPreview* sinalizador de recursos, use o [registro de recurso az] [ az-feature-register] comando conforme mostrado no exemplo a seguir:

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

Demora alguns minutos para o status exibir *Registrado*. Você pode verificar o status de registro usando o comando [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

Quando estiver pronto, atualize o registro do provedor de recursos *Microsoft.ContainerService* usando o comando [az provider register][az-provider-register]:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Visão geral das políticas de segurança de pod

Em um cluster Kubernetes, um controlador de admissão é usado para interceptar solicitações ao servidor de API quando um recurso deve ser criado. O controlador de admissão pode então *validar* a solicitação de recurso em relação a um conjunto de regras, ou *modifica* o recurso para alterar os parâmetros de implantação.

*PodSecurityPolicy* é um controlador de admissão valida uma especificação de pod atende aos seus requisitos definidos. Esses requisitos podem limitar o uso de contêineres com privilégios, acesso a determinados tipos de armazenamento, ou o usuário ou grupo que pode ser executado como o contêiner. Quando você tentar implantar um recurso em que as especificações de pod não atendem aos requisitos descritos na política de segurança pod, a solicitação será negada. Essa capacidade de controlar os pods podem ser agendados no AKS cluster impede que alguns possíveis vulnerabilidades de segurança ou escalonamentos de privilégio.

Quando você habilita a política de segurança de pod em um cluster AKS, algumas políticas padrão serão aplicadas. Essas políticas padrão fornecem uma experiência de caixa para definir o que pods pode ser agendada. No entanto, os usuários de cluster poderão enfrentar problemas de implantação de pods até que você defina suas próprias políticas. A abordagem recomendada é:

* Criar um cluster AKS
* Definir suas próprias políticas de segurança pod
* Habilitar o recurso de política de segurança de pod

Para mostrar como as políticas padrão limitam as implantações de pod, neste artigo, primeiro habilite o recurso de diretivas de segurança pod e criar uma política personalizada.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>Habilitar política de segurança de pod em um cluster do AKS

Você pode habilitar ou desabilitar a política de segurança pod usando o [atualizar az aks] [ az-aks-update] comando. A seguinte exemplo habilita pod política de segurança no nome do cluster *myAKSCluster* no grupo de recursos denominado *myResourceGroup*.

> [!NOTE]
> Para o uso do mundo real, não habilite a política de segurança de pod até que você definiu suas próprias políticas personalizadas. Neste artigo, você deve habilitar política de segurança do pod como a primeira etapa para ver como as políticas padrão limitam o pod de implantações.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>Políticas padrão de AKS

Quando você habilita a política de segurança pod, AKS cria duas políticas padrão denominadas *privilegiadas* e *restrito*. Não edite ou remova essas políticas padrão. Em vez disso, crie suas próprias políticas que definem as configurações que você deseja para o controle. Vamos primeiro examinar quais essas políticas padrão são como elas afetam as implantações de pod.

Para exibir as políticas disponíveis, use o [kubectl get psp] [ kubectl-get] de comando, conforme mostrado no exemplo a seguir. Como parte do padrão *restrito* diretiva, o usuário é negado *PRIV* usa para escalonamento privilegiado pod e o usuário *MustRunAsNonRoot*.

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
restricted   false          RunAsAny   MustRunAsNonRoot   MustRunAs   MustRunAs   false            configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

O *restrito* política de segurança pod é aplicada a qualquer usuário autenticado no cluster AKS. Essa atribuição é controlada pelo ClusterRoles e ClusterRoleBindings. Use o [kubectl get clusterrolebindings] [ kubectl-get] de comando e procure o *padrão: restritos:* associação:

```console
kubectl get clusterrolebindings default:restricted -o yaml
```

Conforme mostrado na seguinte saída condensada, o *psp: restrito* ClusterRole é atribuído a qualquer *sistema: autenticado* usuários. Essa capacidade proporciona um nível básico de restrições sem suas próprias políticas que está sendo definidas.

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  [...]
  name: default:restricted
  [...]
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:restricted
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:authenticated
```

É importante entender como essas políticas padrão interagem com solicitações de usuário para agendar os pods antes de começar a criar seu próprios pod políticas de segurança. Nas próximas seções, vamos agendar algumas pods para ver essas políticas padrão em ação.

## <a name="create-a-test-user-in-an-aks-cluster"></a>Criar um usuário de teste em um cluster AKS

Por padrão, quando você usa o [az aks get-credentials] [ az-aks-get-credentials] comando, o *admin* as credenciais para o cluster do AKS e adicionado ao seu `kubectl` config. O usuário administrador ignora a imposição de políticas de segurança pod. Se você usar a integração do Active Directory do Azure para seus clusters AKS, você pode entrar com as credenciais de um usuário não administrador para ver a imposição de políticas em ação. Neste artigo, vamos criar uma conta de usuário de teste no cluster AKS que você pode usar.

Criar um namespace de exemplo denominado *aks psp* para recursos de teste usando o [kubectl criar namespace] [ kubectl-create] comando. Em seguida, crie uma conta de serviço nomeada *usuário nonadmin* usando o [kubectl criar serviceaccount] [ kubectl-create] comando:

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

Em seguida, crie um RoleBinding para o *usuário nonadmin* para executar ações básicas no namespace usando o [kubectl criar rolebinding] [ kubectl-create] comando:

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>Criar comandos de alias para o usuário administrador e não administrador

Para realçar a diferença entre o usuário de administrador regulares ao usar `kubectl` e o usuário não administrador criado nas etapas anteriores, crie dois aliases de linha de comando:

* O **kubectl-admin** alias é para o usuário administrador regulares e tem como escopo o *aks psp* namespace.
* O **kubectl nonadminuser** alias destina-se a *usuário nonadmin* criado na etapa anterior e tem como escopo o *psp aks* namespace.

Crie esses dois aliases, conforme mostrado nos comandos a seguir:

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>A criação de um pod com privilégios de teste

Vamos testar primeiro o que acontece quando você agenda um pod com o contexto de segurança do `privileged: true`. Este contexto de segurança aumenta os privilégios do pod. Na seção anterior que mostrou o pod AKS padrão de diretivas de segurança, o *restrito* diretiva deve negar esta solicitação.

Crie um arquivo chamado `nginx-privileged.yaml` e cole o manifesto YAML a seguir:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: nginx:1.14.2
      securityContext:
        privileged: true
```

Crie o pod usando o [aplicar kubectl] [ kubectl-apply] de comando e especifique o nome do seu manifesto YAML:

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

O pod não conseguir ser agendado, conforme mostrado na saída de exemplo a seguir:

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: [spec.containers[0].securityContext.privileged: Invalid value: true: Privileged containers are not allowed]
```

O pod não alcançar o estágio de planejamento, portanto, não há nenhum recurso para excluir antes de prosseguir.

## <a name="test-creation-of-an-unprivileged-pod"></a>Criação de um pod sem privilégios de teste

No exemplo anterior, a especificação de pod solicitada escalonamento privilegiado. Essa solicitação for negada pelo padrão *restrito* pod política de segurança, portanto, o pod de falha ser agendada. Vamos tentar executar agora esse mesmo pod NGINX sem que a solicitação de elevação de privilégio.

Crie um arquivo chamado `nginx-unprivileged.yaml` e cole o manifesto YAML a seguir:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
```

Crie o pod usando o [aplicar kubectl] [ kubectl-apply] de comando e especifique o nome do seu manifesto YAML:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

O Agendador Kubernetes aceita a solicitação de pod. No entanto, se você examinar o status do pod usando `kubectl get pods`, há um erro:

```console
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS                       RESTARTS   AGE
nginx-unprivileged   0/1     CreateContainerConfigError   0          26s
```

Use o [kubectl descrevem pod] [ kubectl-describe] comando para examinar os eventos para o pod. Exemplo condensado a seguir mostra a imagem e o contêiner exigem permissões de raiz, mesmo que nós não solicitou-los:

```console
$ kubectl-nonadminuser describe pod nginx-unprivileged

Name:               nginx-unprivileged
Namespace:          psp-aks
Priority:           0
PriorityClassName:  <none>
Node:               aks-agentpool-34777077-0/10.240.0.4
Start Time:         Thu, 28 Mar 2019 22:05:04 +0000
[...]
Events:
  Type     Reason     Age                     From                               Message
  ----     ------     ----                    ----                               -------
  Normal   Scheduled  7m14s                   default-scheduler                  Successfully assigned psp-aks/nginx-unprivileged to aks-agentpool-34777077-0
  Warning  Failed     5m2s (x12 over 7m13s)   kubelet, aks-agentpool-34777077-0  Error: container has runAsNonRoot and image will run as root
  Normal   Pulled     2m10s (x25 over 7m13s)  kubelet, aks-agentpool-34777077-0  Container image "nginx:1.14.2" already present on machine
```

Mesmo que nós não solicitou nenhum acesso privilegiado, a imagem de contêiner NGINX precisa criar uma associação para a porta *80*. Para associar portas *1024* e abaixo, o *raiz* usuário é necessário. Quando o pod tenta iniciar, o *restrito* pod política de segurança nega essa solicitação.

Este exemplo mostra que as políticas de segurança de pod padrão criadas pelo AKS estão em vigor e restringem as ações que um usuário pode executar. É importante entender o comportamento dessas políticas padrão, como você não pode esperar um pod NGINX básico a ser negado.

Antes de prosseguir para a próxima etapa, exclua esse teste pod usando o [kubectl excluir pod] [ kubectl-delete] comando:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>Criação de teste de um pod com um contexto de usuário específico

No exemplo anterior, a imagem de contêiner automaticamente tentou usar raiz para associar o NGINX para a porta 80. Essa solicitação foi negada pelo padrão *restrito* pod de política de segurança, portanto, o pod não pode ser iniciado. Vamos tentar executar agora esse mesmo pod NGINX com um contexto de usuário específico, como `runAsUser: 2000`.

Crie um arquivo chamado `nginx-unprivileged-nonroot.yaml` e cole o manifesto YAML a seguir:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged-nonroot
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
      securityContext:
        runAsUser: 2000
```

Crie o pod usando o [aplicar kubectl] [ kubectl-apply] de comando e especifique o nome do seu manifesto YAML:

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

O Agendador Kubernetes aceita a solicitação de pod. No entanto, se você examinar o status do pod usando `kubectl get pods`, há um erro diferente que o exemplo anterior:

```console
$ kubectl-nonadminuser get pods

NAME                         READY   STATUS              RESTARTS   AGE
nginx-unprivileged-nonroot   0/1     CrashLoopBackOff    1          3s
```

Use o [kubectl descrevem pod] [ kubectl-describe] comando para examinar os eventos para o pod. O exemplo condensado a seguir mostra os eventos de pod:

```console
$ kubectl-nonadminuser describe pods nginx-unprivileged

Name:               nginx-unprivileged
Namespace:          psp-aks
Priority:           0
PriorityClassName:  <none>
Node:               aks-agentpool-34777077-0/10.240.0.4
Start Time:         Thu, 28 Mar 2019 22:05:04 +0000
[...]
Events:
  Type     Reason     Age                   From                               Message
  ----     ------     ----                  ----                               -------
  Normal   Scheduled  2m14s                 default-scheduler                  Successfully assigned psp-aks/nginx-unprivileged-nonroot to aks-agentpool-34777077-0
  Normal   Pulled     118s (x3 over 2m13s)  kubelet, aks-agentpool-34777077-0  Container image "nginx:1.14.2" already present on machine
  Normal   Created    118s (x3 over 2m13s)  kubelet, aks-agentpool-34777077-0  Created container
  Normal   Started    118s (x3 over 2m12s)  kubelet, aks-agentpool-34777077-0  Started container
  Warning  BackOff    105s (x5 over 2m11s)  kubelet, aks-agentpool-34777077-0  Back-off restarting failed container
```

Os eventos indicam que o contêiner foi criado e iniciado. Não há nada imediatamente óbvias sobre por que o pod está em estado de falha. Vamos examinar os logs de pod usando o [kubectl logs] [ kubectl-logs] comando:

```console
kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous
```

A saída de log de exemplo a seguir fornece uma indicação que dentro da configuração do NGINX em si, não há um erro de permissões quando o serviço tenta ser iniciado. Novamente, esse erro é causado pela necessidade de se vincular à porta 80. Embora a especificação de pod definida uma conta de usuário normal, essa conta de usuário não é suficiente para o nível de sistema operacional para o serviço NGINX para iniciar e vincular à porta restrita.

```console
$ kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous

2019/03/28 22:38:29 [warn] 1#1: the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
nginx: [warn] the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
2019/03/28 22:38:29 [emerg] 1#1: mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
nginx: [emerg] mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
```

Novamente, é importante entender o comportamento das diretivas de segurança padrão pod. Esse erro foi um pouco mais difícil de rastrear e, novamente, você não pode esperar um pod NGINX básico a ser negado.

Antes de prosseguir para a próxima etapa, exclua esse teste pod usando o [kubectl excluir pod] [ kubectl-delete] comando:

```console
kubectl-nonadminuser delete -f nginx-unprivileged-nonroot.yaml
```

## <a name="create-a-custom-pod-security-policy"></a>Criar uma política de segurança pod personalizado

Agora que você já viu o comportamento das diretivas de segurança padrão pod, vamos fornecer uma maneira para o *nonadmin usuário* agendar com êxito os pods.

Vamos criar uma política para rejeitar pods que solicitam acesso privilegiado. Outras opções, como *runAsUser* ou permitido *volumes*, não são explicitamente restritos. Esse tipo de política nega uma solicitação para acesso privilegiado, mas caso contrário, permite que o cluster executar os pods solicitados.

Crie um arquivo chamado `psp-deny-privileged.yaml` e cole o manifesto YAML a seguir:

```yaml
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: psp-deny-privileged
spec:
  privileged: false
  seLinux:
    rule: RunAsAny
  supplementalGroups:
    rule: RunAsAny
  runAsUser:
    rule: RunAsAny
  fsGroup:
    rule: RunAsAny
  volumes:
  - '*'
```

Criar a política usando o [aplicar kubectl] [ kubectl-apply] de comando e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f psp-deny-privileged.yaml
```

Para exibir as políticas disponíveis, use o [kubectl get psp] [ kubectl-get] de comando, conforme mostrado no exemplo a seguir. Comparar as *privilégios negar psp* política com o padrão *restrito* política que foi imposta nos exemplos anteriores para criar um pod. Somente o uso de *PRIV* negado pela sua diretiva de escalonamento de bloqueios. Não há nenhuma restrição sobre o usuário ou grupo para o *privilégios negar psp* política.

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
restricted            false          RunAsAny   MustRunAsNonRoot   MustRunAs   MustRunAs   false            configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>Permitir que a conta de usuário para usar a política de segurança pod personalizado

Na etapa anterior, você criou uma política de segurança de pod para rejeitar pods que o acesso privilegiado de solicitação. Para permitir que a política a ser usada, você cria um *função* ou um *ClusterRole*. Em seguida, associar uma dessas funções usando um *RoleBinding* ou *ClusterRoleBinding*.

Para este exemplo, crie um ClusterRole que permite que você *usar* as *privilégios negar psp* política criada na etapa anterior. Crie um arquivo chamado `psp-deny-privileged-clusterrole.yaml` e cole o manifesto YAML a seguir:

```yaml
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: psp-deny-privileged-clusterrole
rules:
- apiGroups:
  - extensions
  resources:
  - podsecuritypolicies
  resourceNames:
  - psp-deny-privileged
  verbs:
  - use
```

Crie o ClusterRole usando o [aplicar kubectl] [ kubectl-apply] de comando e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

Agora, crie um ClusterRoleBinding para usar o ClusterRole criado na etapa anterior. Crie um arquivo chamado `psp-deny-privileged-clusterrolebinding.yaml` e cole o manifesto YAML a seguir:

```yaml
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: psp-deny-privileged-clusterrolebinding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp-deny-privileged-clusterrole
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:serviceaccounts
```

Criar um ClusterRoleBinding usando o [aplicar kubectl] [ kubectl-apply] de comando e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> Na primeira etapa deste artigo, o recurso de política de segurança pod foi habilitado no cluster do AKS. A prática recomendada era apenas habilitar o recurso de política de segurança pod depois de definir suas próprias políticas. Este é o estágio onde você deve habilitar o recurso de política de segurança pod. Um ou mais políticas personalizadas foram definidas e as contas de usuário foram associadas essas políticas. Agora você pode, com segurança, a política de segurança do pod de recursos e minimizar os problemas causados pelas políticas padrão.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>Testar a criação de um pod sem privilégios novamente

Com sua política de segurança pod personalizada aplicada e uma associação para a conta de usuário para usar a política, vamos tentar criar um pod sem privilégios novamente. Use o mesmo `nginx-privileged.yaml` manifesto para criar o pod usando o [aplicar kubectl] [ kubectl-apply] comando:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

O pod está agendado com êxito. Quando você verificar o status do pod usando o [kubectl get pods] [ kubectl-get] de comando, é o pod *executando*:

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

Este exemplo mostra como você pode criar políticas de segurança pod personalizado para definir o acesso para o cluster do AKS para diferentes usuários ou grupos. O padrão de diretivas AKS fornecem controles rígidos em quais pods podem executar, portanto, criar suas próprias políticas personalizadas, em seguida, definir corretamente as restrições que você precisa.

Excluir o pod NGINX sem privilégios usando o [excluir kubectl] [ kubectl-delete] de comando e especifique o nome do seu manifesto YAML:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>Limpar recursos

Para desabilitar a política de segurança pod, use o [atualizar az aks] [ az-aks-update] comando novamente. A seguinte exemplo desabilita o pod política de segurança no nome do cluster *myAKSCluster* no grupo de recursos denominado *myResourceGroup*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

Em seguida, exclua o ClusterRole e ClusterRoleBinding:

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

Excluir a política de rede usando [excluir kubectl] [ kubectl-delete] de comando e especifique o nome do seu manifesto YAML:

```console
kubectl delete -f psp-deny-privileged.yaml
```

Por fim, excluir o *aks psp* namespace:

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>Próximas etapas

Este artigo mostrou como criar uma política de segurança pod para evitar o uso de acesso privilegiado. Há muitos dos recursos pode impor uma política, como o tipo de volume ou o usuário RunAs. Para obter mais informações sobre as opções disponíveis, consulte o [documentos de referência de política de segurança de pod Kubernetes][kubernetes-policy-reference].

Para obter mais informações sobre como limitar o tráfego de rede de pod, consulte [proteger o tráfego entre os pods usando políticas de rede no AKS][network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubernetes-policy-reference]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#policy-reference
[aks-github]: https://github.com/azure/aks/issues

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policies]: use-network-policies.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-extension-add]: /cli/azure/extension#az-extension-add

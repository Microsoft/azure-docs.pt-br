---
title: Usar políticas de segurança de Pod no serviço kubernetes do Azure (AKS)
description: Saiba como controlar as inmissões Pod usando o PodSecurityPolicy no serviço kubernetes do Azure (AKS)
services: container-service
ms.topic: article
ms.date: 03/25/2021
ms.openlocfilehash: d95cdb51136511bdd8529c829c3f680d19e14ba9
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105611762"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>Visualização – Proteja seu cluster usando políticas de segurança pod no serviço de kubernetes do Azure (AKS)

> [!WARNING]
> **O recurso descrito neste documento, política de segurança de Pod (versão prévia), começará a ser reprovada com a versão 1,21 do kubernetes, com sua remoção na versão 1,25.** Como o kubernetes upstream se aproxima da etapa, a Comunidade do kubernetes estará trabalhando para documentar alternativas viáveis. O anúncio de substituição anterior foi feito no momento, pois não havia uma opção viável para os clientes. Agora que a Comunidade kubernetes está trabalhando em uma alternativa, não há mais a necessidade de se preterir à frente do kubernetes.
>
> Depois que a política de segurança de pods (versão prévia) for preterida, você precisará desabilitar o recurso em todos os clusters existentes usando o recurso preterido para realizar futuras atualizações de cluster e permanecer dentro do suporte do Azure.

Para melhorar a segurança do cluster AKS, você pode limitar o que o pods pode ser agendado. Os pods que solicitam recursos que você não permitir não podem ser executados no cluster AKS. Você define esse acesso usando políticas de segurança de Pod. Este artigo mostra como usar políticas de segurança de pod para limitar a implantação de pods em AKS.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Antes de começar

Este artigo considera que já existe um cluster do AKS. Se precisar de um cluster do AKS, veja o guia de início rápido do AKS [usando a CLI do Azure][aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].

Você precisará da CLI do Azure versão 2.0.61 ou posterior instalada e configurada. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Instalar a extensão da CLI aks-preview

Para usar políticas de segurança de Pod, você precisa da extensão da CLI do *AKs* versão 0.4.1 ou superior. Instale a extensão de CLI do Azure *aks-preview* usando o comando [az extension add][az-extension-add] e, em seguida, verifique se há atualizações disponíveis usando o comando [az extension update][az-extension-update]:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>Registrar provedor de recursos de política de segurança de Pod

**Este documento e recurso são definidos para substituição em 15 de outubro de 2020.**

Para criar ou atualizar um cluster AKS para usar políticas de segurança de Pod, primeiro habilite um sinalizador de recurso em sua assinatura. Para registrar o sinalizador de recurso *PodSecurityPolicyPreview* , use o comando [AZ Feature Register][az-feature-register] , conforme mostrado no exemplo a seguir:

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

Demora alguns minutos para o status exibir *Registrado*. Você pode verificar o status de registro usando o comando [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

Quando estiver pronto, atualize o registro do provedor de recursos *Microsoft. ContainerService* usando o comando [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Visão geral das políticas de segurança Pod

Em um cluster kubernetes, um controlador de admissão é usado para interceptar solicitações ao servidor de API quando um recurso deve ser criado. O controlador de admissão pode *validar* a solicitação de recurso em relação a um conjunto de regras ou *mutar* o recurso para alterar os parâmetros de implantação.

*PodSecurityPolicy* é um controlador de admissão que valida uma especificação de Pod que atende aos seus requisitos definidos. Esses requisitos podem limitar o uso de contêineres com privilégios, o acesso a determinados tipos de armazenamento ou o usuário ou grupo no qual o contêiner pode ser executado. Quando você tenta implantar um recurso em que as especificações de Pod não atendem aos requisitos descritos na política de segurança de Pod, a solicitação é negada. Essa capacidade de controlar o que o pods pode ser agendado no cluster AKS impede algumas vulnerabilidades de segurança ou possíveis escalonamentos de privilégios.

Quando você habilita a política de segurança de pod em um cluster AKS, algumas políticas padrão são aplicadas. Essas políticas padrão fornecem uma experiência pronta para uso para definir o que os pods podem ser agendados. No entanto, os usuários do cluster podem ter problemas ao implantar o pods até que você defina suas próprias políticas. A abordagem recomendada é:

* Criar um cluster AKS
* Definir suas próprias políticas de segurança de Pod
* Habilitar o recurso de política de segurança Pod

Para mostrar como as políticas padrão limitam as implantações de Pod, neste artigo, primeiro habilitamos o recurso de políticas de segurança pod e, em seguida, criamos uma política personalizada.

### <a name="behavior-changes-between-pod-security-policy-and-azure-policy"></a>Alterações de comportamento entre a política de segurança de Pod e a Azure Policy

Veja abaixo um resumo das alterações de comportamento entre a política de segurança de Pod e a Azure Policy.

|Cenário| Política de segurança de Pod | Azure Policy |
|---|---|---|
|Instalação|Habilitar o recurso de política de segurança de Pod |Habilitar o complemento Azure Policy
|Implantar políticas| Implantar recurso de política de segurança Pod| Atribua políticas do Azure à assinatura ou ao escopo do grupo de recursos. O complemento de Azure Policy é necessário para aplicativos de recurso kubernetes.
| Políticas padrão | Quando a política de segurança de Pod é habilitada no AKS, as políticas padrão com privilégios e irrestrito são aplicadas. | Nenhuma política padrão é aplicada habilitando o complemento Azure Policy. Você deve habilitar explicitamente as políticas no Azure Policy.
| Quem pode criar e atribuir políticas | O administrador de cluster cria um recurso de política de segurança Pod | Os usuários devem ter uma função mínima de permissões de ' proprietário ' ou ' colaborador de política de recurso ' no grupo de recursos de cluster AKS. -Por meio da API, os usuários podem atribuir políticas no escopo do recurso de cluster AKS. O usuário deve ter o mínimo de permissões de ' proprietário ' ou ' colaborador de política de recurso ' no recurso de cluster AKS. -No portal do Azure, as políticas podem ser atribuídas no nível grupo de gerenciamento/assinatura/grupo de recursos.
| Políticas de autorização| Usuários e contas de serviço exigem permissões explícitas para usar políticas de segurança de Pod. | Nenhuma atribuição adicional é necessária para autorizar políticas. Depois que as políticas são atribuídas no Azure, todos os usuários do cluster podem usar essas políticas.
| Aplicabilidade de política | O usuário administrador ignora a imposição de políticas de segurança de Pod. | Todos os usuários (administrador & não administrador) veem as mesmas políticas. Não há maiúsculas especiais com base nos usuários. O aplicativo de política pode ser excluído no nível de namespace.
| Escopo da política | As políticas de segurança de Pod não estão em namespace | Os modelos de restrição usados pelo Azure Policy não são namespaces.
| Ação de negação/auditoria/mutação | As políticas de segurança Pod dão suporte apenas a ações Deny. A mutação pode ser feita com valores padrão em solicitações de criação. A validação pode ser feita durante solicitações de atualização.| O Azure Policy dá suporte a ações de negação de & de auditoria. A mutação ainda não é suportada, mas planejada.
| Conformidade da política de segurança Pod | Não há visibilidade da conformidade de pods que existia antes de habilitar a política de segurança de Pod. Os pods sem conformidade criados após a habilitação das políticas de segurança de Pod são negados. | Os pods sem conformidade que existiam antes de aplicar as políticas do Azure apareceriam em violações de política. Os pods sem conformidade criados após a habilitação das políticas do Azure serão negadas se as políticas forem definidas com um efeito de negação.
| Como exibir políticas no cluster | `kubectl get psp` | `kubectl get constrainttemplate` -Todas as políticas são retornadas.
| Política de segurança Pod com privilégios padrão | Um recurso de política de segurança de Pod privilegiado é criado por padrão ao habilitar o recurso. | O modo privilegiado não implica nenhuma restrição, como resultado, é equivalente a não ter nenhuma atribuição de Azure Policy.
| [Política de segurança de Pod padrão-linha de base/padrão](https://kubernetes.io/docs/concepts/security/pod-security-standards/#baseline-default) | O usuário instala um recurso de linha de base de política de segurança Pod. | Azure Policy fornece uma [iniciativa de linha de base interna](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) que é mapeada para a política de segurança pod de linha de base.
| [Política de segurança Pod padrão-restrita](https://kubernetes.io/docs/concepts/security/pod-security-standards/#restricted) | O usuário instala um recurso restrito da política de segurança de Pod. | Azure Policy fornece uma [iniciativa restrita interna](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00) que é mapeada para a política de segurança Pod restrita.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>Habilitar política de segurança de pod em um cluster AKS

Você pode habilitar ou desabilitar a política de segurança Pod usando o comando [AZ AKs Update][az-aks-update] . O exemplo a seguir habilita a política de segurança de Pod no nome do cluster *myAKSCluster* no grupo de recursos chamado *MyResource* Group.

> [!NOTE]
> Para uso do mundo real, não habilite a política de segurança Pod até definir suas próprias políticas personalizadas. Neste artigo, você habilita a política de segurança Pod como a primeira etapa para ver como as políticas padrão limitam as implantações de Pod.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>Políticas de AKS padrão

Quando você habilita a política de segurança Pod, o AKS cria uma política padrão chamada *Privileged*. Não edite ou remova a política padrão. Em vez disso, crie suas próprias políticas que definam as configurações que você deseja controlar. Primeiro, vamos examinar o que essas políticas padrão são como elas afetam as implantações de Pod.

Para exibir as políticas disponíveis, use o comando [kubectl Get PSP][kubectl-get] , conforme mostrado no exemplo a seguir

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *     configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

A política de segurança de Pod *privilegiado* é aplicada a qualquer usuário autenticado no cluster AKs. Essa atribuição é controlada por ClusterRoles e ClusterRoleBindings. Use o comando [kubectl Get rolebindings][kubectl-get] e pesquise o *padrão: Privileged:* Binding no namespace do *sistema Kube* :

```console
kubectl get rolebindings default:privileged -n kube-system -o yaml
```

Conforme mostrado na seguinte saída condensada, o *PSP: Privileged* ClusterRole é atribuído a qualquer *sistema: usuários autenticados* . Essa capacidade fornece um nível básico de privilégio sem que suas próprias políticas sejam definidas.

```
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  [...]
  name: default:privileged
  [...]
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:privileged
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:masters
```

É importante entender como essas políticas padrão interagem com as solicitações do usuário para agendar pods antes de começar a criar suas próprias políticas de segurança de Pod. Nas próximas seções, vamos agendar alguns pods para ver essas políticas padrão em ação.

## <a name="create-a-test-user-in-an-aks-cluster"></a>Criar um usuário de teste em um cluster AKS

Por padrão, quando você usa o comando [AZ AKs Get-Credentials][az-aks-get-credentials] , as credenciais de *administrador* para o cluster AKs são adicionadas à sua `kubectl` configuração. O usuário administrador ignora a imposição de políticas de segurança de Pod. Se você usar a integração de Azure Active Directory para seus clusters AKS, poderá entrar com as credenciais de um usuário não administrador para ver a imposição de políticas em ação. Neste artigo, vamos criar uma conta de usuário de teste no cluster AKS que você pode usar.

Crie um namespace de exemplo chamado *PSP-AKs* para recursos de teste usando o comando [kubectl Create namespace][kubectl-create] . Em seguida, crie uma conta de serviço chamada *usuário não administrador* usando o comando [kubectl Create USERACCOUNT][kubectl-create] :

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

Em seguida, crie uma Rolebinding para que o *usuário não administrador* execute ações básicas no namespace usando o comando [kubectl Create rolebinding][kubectl-create] :

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>Criar comandos de alias para administrador e usuário não administrador

Para destacar a diferença entre o usuário administrador regular ao usar `kubectl` o e o usuário não administrador criado nas etapas anteriores, crie dois aliases de linha de comando:

* O alias **kubectl-admin** é para o usuário administrador regular e tem como escopo o namespace *PSP-AKs* .
* O alias **kubectl-nonadminuser** é para o *usuário não administrador* criado na etapa anterior e tem como escopo o namespace *PSP-AKs* .

Crie esses dois aliases, conforme mostrado nos seguintes comandos:

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>Testar a criação de um pod privilegiado

Primeiro, vamos testar o que acontece quando você agenda um pod com o contexto de segurança de `privileged: true` . Esse contexto de segurança Escalona os privilégios do pod. Na seção anterior que mostrou as políticas de segurança de Pod AKS padrão, a política de *privilégio* deve negar essa solicitação.

Crie um arquivo chamado `nginx-privileged.yaml` e cole o seguinte manifesto YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
      securityContext:
        privileged: true
```

Crie o Pod usando o comando [kubectl Apply][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

O Pod não será agendado, conforme mostrado na seguinte saída de exemplo:

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: []
```

O Pod não chega ao estágio de agendamento, portanto, não há recursos a serem excluídos antes de você prosseguir.

## <a name="test-creation-of-an-unprivileged-pod"></a>Teste de criação de um pod sem privilégios

No exemplo anterior, a especificação de Pod solicitou escalonamento privilegiado. Essa solicitação é negada pela política de segurança de pod de *privilégio* padrão, portanto, o Pod não será agendado. Vamos tentar executar o mesmo Pod NGINX sem a solicitação de elevação de privilégio.

Crie um arquivo chamado `nginx-unprivileged.yaml` e cole o seguinte manifesto YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
```

Crie o Pod usando o comando [kubectl Apply][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

O Pod não será agendado, conforme mostrado na seguinte saída de exemplo:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged.yaml": pods "nginx-unprivileged" is forbidden: unable to validate against any pod security policy: []
```

O Pod não chega ao estágio de agendamento, portanto, não há recursos a serem excluídos antes de você prosseguir.

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>Testar a criação de um pod com um contexto de usuário específico

No exemplo anterior, a imagem de contêiner tentou usar automaticamente a raiz para associar NGINX à porta 80. Essa solicitação foi negada pela política de segurança de pod de *privilégio* padrão, portanto, não é possível iniciar o pod. Agora, vamos tentar executar o mesmo Pod NGINX com um contexto de usuário específico, como `runAsUser: 2000` .

Crie um arquivo chamado `nginx-unprivileged-nonroot.yaml` e cole o seguinte manifesto YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged-nonroot
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
      securityContext:
        runAsUser: 2000
```

Crie o Pod usando o comando [kubectl Apply][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

O Pod não será agendado, conforme mostrado na seguinte saída de exemplo:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged-nonroot.yaml": pods "nginx-unprivileged-nonroot" is forbidden: unable to validate against any pod security policy: []
```

O Pod não chega ao estágio de agendamento, portanto, não há recursos a serem excluídos antes de você prosseguir.

## <a name="create-a-custom-pod-security-policy"></a>Criar uma política de segurança de Pod personalizada

Agora que você viu o comportamento das políticas de segurança de Pod padrão, vamos fornecer uma maneira para o *usuário não administrador* agendasse com êxito o pods.

Vamos criar uma política para rejeitar pods que solicitam acesso privilegiado. Outras opções, como *runAsUser* ou *volumes* permitidos, não são explicitamente restritas. Esse tipo de política nega uma solicitação de acesso privilegiado, mas, caso contrário, permite que o cluster execute o pods solicitado.

Crie um arquivo chamado `psp-deny-privileged.yaml` e cole o seguinte manifesto YAML:

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

Crie a política usando o comando [kubectl Apply][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f psp-deny-privileged.yaml
```

Para exibir as políticas disponíveis, use o comando [kubectl Get PSP][kubectl-get] , conforme mostrado no exemplo a seguir. Compare a política com *privilégios de PSP-Deny* com a política de *privilégio* padrão imposta nos exemplos anteriores para criar um pod. Somente o uso de escalonamento de *priv* é negado pela sua política. Não há restrições no usuário ou grupo para a política *com privilégios de PSP-Deny* .

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *          
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>Permitir que a conta de usuário use a política de segurança de Pod personalizada

Na etapa anterior, você criou uma política de segurança de pod para rejeitar pods que solicitam acesso privilegiado. Para permitir que a política seja usada, você cria uma *função* ou um *ClusterRole*. Em seguida, você associa uma dessas funções usando uma *rolebinding* ou *ClusterRoleBinding*.

Para este exemplo, crie um ClusterRole que permita que você *use* a política com *privilégios de PSP-Deny* criada na etapa anterior. Crie um arquivo chamado `psp-deny-privileged-clusterrole.yaml` e cole o seguinte manifesto YAML:

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

Crie o ClusterRole usando o comando [kubectl Apply][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

Agora, crie um ClusterRoleBinding para usar o ClusterRole criado na etapa anterior. Crie um arquivo chamado `psp-deny-privileged-clusterrolebinding.yaml` e cole o seguinte manifesto YAML:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
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

Crie um ClusterRoleBinding usando o comando [kubectl Apply][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> Na primeira etapa deste artigo, o recurso de política de segurança Pod foi habilitado no cluster AKS. A prática recomendada era habilitar apenas o recurso de política de segurança Pod depois de definir suas próprias políticas. Este é o estágio em que você habilitaria o recurso de política de segurança Pod. Uma ou mais políticas personalizadas foram definidas e as contas de usuário foram associadas a essas políticas. Agora você pode habilitar com segurança o recurso de política de segurança pod e minimizar os problemas causados pelas políticas padrão.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>Testar a criação de um pod sem privilégios novamente

Com a política de segurança de Pod personalizada aplicada e uma associação para a conta de usuário usar a política, vamos tentar criar um pod sem privilégios novamente. Use o mesmo `nginx-privileged.yaml` manifesto para criar o Pod usando o comando [kubectl Apply][kubectl-apply] :

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

O Pod foi agendado com êxito. Quando você verifica o status do pod usando o comando [kubectl Get pods][kubectl-get] , o Pod está *em execução*:

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

Este exemplo mostra como você pode criar políticas de segurança de Pod personalizadas para definir o acesso ao cluster AKS para usuários ou grupos diferentes. As políticas de AKS padrão fornecem controles rígidos sobre o que o pods pode executar, portanto, crie suas próprias políticas personalizadas para, então, definir corretamente as restrições necessárias.

Exclua o Pod sem privilégios do NGINX usando o comando [kubectl Delete][kubectl-delete] e especifique o nome do seu manifesto do YAML:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>Limpar os recursos

Para desabilitar a política de segurança de Pod, use o comando [AZ AKs Update][az-aks-update] novamente. O exemplo a seguir desabilita a política de segurança de Pod no nome do cluster *myAKSCluster* no grupo de recursos chamado *MyResource* Group:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

Em seguida, exclua o ClusterRole e o ClusterRoleBinding:

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

Exclua a política de segurança usando o comando [kubectl Delete][kubectl-delete] e especifique o nome do seu manifesto YAML:

```console
kubectl delete -f psp-deny-privileged.yaml
```

Por fim, exclua o namespace *PSP-AKs* :

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>Próximas etapas

Este artigo mostrou como criar uma política de segurança de pod para evitar o uso de acesso privilegiado. Há muitos recursos que uma política pode impor, como o tipo de volume ou o usuário RunAs. Para obter mais informações sobre as opções disponíveis, consulte os [documentos de referência da política de segurança Pod kubernetes][kubernetes-policy-reference].

Para obter mais informações sobre como limitar o tráfego de rede Pod, consulte [proteger o tráfego entre pods usando as políticas de rede no AKs][network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubernetes-policy-reference]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#policy-reference
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
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[policy-samples]: ./policy-reference.md#microsoftcontainerservice
[azure-policy-add-on]: ../governance/policy/concepts/policy-for-kubernetes.md

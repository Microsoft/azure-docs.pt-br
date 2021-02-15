---
title: Proteger pods com Azure Policy no AKS (serviço kubernetes do Azure)
description: Saiba como proteger pods com Azure Policy no AKS (serviço kubernetes do Azure)
services: container-service
ms.topic: article
ms.date: 09/22/2020
ms.openlocfilehash: 8e437095b3d527647a453ba89adaa2ab62672177
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348518"
---
# <a name="secure-pods-with-azure-policy"></a>Proteger pods com Azure Policy

Para melhorar a segurança do cluster AKS, você pode controlar quais funções os pods são concedidos e se algo está em execução na política da empresa. Esse acesso é definido por meio de políticas internas fornecidas pelo [complemento Azure Policy para o AKs][kubernetes-policy-reference]. Ao fornecer controle adicional sobre os aspectos de segurança da especificação do pod, como os privilégios de raiz, o permite uma maior adesão à segurança e visibilidade do que é implantado em seu cluster. Se um pod não atender às condições especificadas na política, Azure Policy poderá impedir que o Pod inicie ou sinalize uma violação. Este artigo mostra como usar Azure Policy para limitar a implantação de pods no AKS.

## <a name="before-you-begin"></a>Antes de começar

Este artigo considera que já existe um cluster do AKS. Se precisar de um cluster do AKS, veja o guia de início rápido do AKS [usando a CLI do Azure][aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].

### <a name="install-the-azure-policy-add-on-for-aks"></a>Instalar o complemento Azure Policy para AKS

Para proteger o AKS pods por meio de Azure Policy, você precisa instalar o complemento Azure Policy para o AKS em um cluster AKS. Siga estas [etapas para instalar o complemento Azure Policy](../governance/policy/concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-aks).

Este documento pressupõe que você tenha o seguinte, que são implantados no detalhado vinculado acima.

* Registrou `Microsoft.ContainerService` os `Microsoft.PolicyInsights` provedores de recursos e usando `az provider register`
* CLI do Azure 2,12 ou superior
* Um cluster AKS em uma versão do 1,15 ou superior instalada com o complemento Azure Policy

## <a name="overview-of-securing-pods-with-azure-policy-for-aks"></a>Visão geral da proteção de pods com Azure Policy para AKS

>[!NOTE]
> Este documento fornece detalhes sobre como usar Azure Policy para proteger pods, que é o sucessor do [recurso de política de segurança de Pod kubernetes na versão prévia](use-pod-security-policies.md).
> **A política de segurança de Pod (versão prévia) e o complemento Azure Policy para AKS não podem ser habilitados simultaneamente.**
> 
> Se estiver instalando o complemento Azure Policy em um cluster com a política de segurança Pod habilitada, [siga estas etapas para desabilitar a política de segurança de Pod](use-pod-security-policies.md#enable-pod-security-policy-on-an-aks-cluster).

Em um cluster AKS, um controlador de admissão é usado para interceptar solicitações ao servidor de API quando um recurso deve ser criado e atualizado. O controlador de admissão pode *validar* a solicitação de recurso em relação a um conjunto de regras se ele deve ser criado.

Anteriormente, a [política de segurança pod de recurso (versão prévia)](use-pod-security-policies.md) foi habilitada por meio do projeto kubernetes para limitar o que o pods pode ser implantado.

Usando o complemento Azure Policy, um cluster AKS pode usar políticas internas do Azure, que protegem os pods e outros recursos de kubernetes semelhantes à política de segurança de Pod anteriormente. O complemento Azure Policy do AKS instala uma instância gerenciada do [gatekeeper](https://github.com/open-policy-agent/gatekeeper), um controlador de admissão de validação. O Azure Policy para kubernetes é criado no Open-software Open Policy Agent, que se baseia na [linguagem de política do Rego](../governance/policy/concepts/policy-for-kubernetes.md#policy-language).

Este documento detalha como usar Azure Policy para proteger pods em um cluster AKS e instruir como migrar de políticas de segurança Pod (versão prévia).

## <a name="limitations"></a>Limitações

As seguintes limitações gerais se aplicam ao complemento de Azure Policy para clusters kubernetes:

- O Azure Policy complemento para kubernetes tem suporte no kubernetes versão **1,14** ou superior.
- Azure Policy complemento para kubernetes só pode ser implantado em pools de nós do Linux
- Há suporte apenas para definições de política internas
- Número máximo de registros não compatíveis por política por cluster: **500**
- Número máximo de registros não compatíveis por assinatura: **1 milhão**
- Não há suporte para instalações do gatekeeper fora do Azure Policy complemento. Desinstale todos os componentes instalados por uma instalação anterior do gatekeeper antes de habilitar o complemento de Azure Policy.
- Os [motivos para não conformidade](../governance/policy/how-to/determine-non-compliance.md#compliance-reasons) não estão disponíveis para este [modo de provedor de recursos](../governance/policy/concepts/definition-structure.md#resource-provider-modes)

As seguintes limitações se aplicam somente ao complemento Azure Policy para AKS:

- A [política de segurança de Pod AKs (versão prévia)](use-pod-security-policies.md) e o complemento Azure Policy para AKs não podem ser habilitados. 
- Namespaces excluídos automaticamente por Azure Policy complemento para avaliação: _Kube-System_ , _gatekeeper-System_ e _AKs-Periscope_.

### <a name="recommendations"></a>Recomendações

Veja a seguir as recomendações gerais para usar o complemento Azure Policy:

- O complemento Azure Policy requer 3 componentes de gatekeeper para executar: 1 pod de auditoria e 2 réplicas pod de webhook. Esses componentes consomem mais recursos, pois a contagem de recursos kubernetes e atribuições de política aumenta no cluster, o que exige operações de auditoria e imposição.

  - Para menos de 500 pods em um único cluster com um máximo de 20 restrições: 2 vCPUs e 350 MB de memória por componente.
  - Para mais de 500 pods em um único cluster com um máximo de 40 restrições: 3 vCPUs e 600 MB de memória por componente.

A recomendação a seguir aplica-se somente ao AKS e ao complemento Azure Policy:

- Use o pool de nós do sistema com o `CriticalAddonsOnly` comparado para agendar pods de gatekeeper. Para obter mais informações, consulte [usando pools de nós do sistema](use-system-pools.md#system-and-user-node-pools).
- Proteja o tráfego de saída de seus clusters AKS. Para obter mais informações, consulte [controlar o tráfego de saída para nós de cluster](limit-egress-traffic.md).
- Se o cluster tiver `aad-pod-identity` habilitado, o pods de identidade gerenciada por nó (NMI) modificará os iptables dos nós para interceptar chamadas para o ponto de extremidade de metadados da instância do Azure. Essa configuração significa que qualquer solicitação feita ao ponto de extremidade de metadados é interceptada por NMI, mesmo que o Pod não use `aad-pod-identity` . AzurePodIdentityException CRD pode ser configurado para informar `aad-pod-identity` que todas as solicitações para o ponto de extremidade de metadados provenientes de um pod que corresponda aos rótulos definidos em CRD devem ser proxies sem nenhum processamento em NMI. O pods do sistema com `kubernetes.azure.com/managedby: aks` rótulo no namespace _Kube-System_ deve ser excluído no `aad-pod-identity` Configurando o AzurePodIdentityException CRD. Para obter mais informações, consulte [desabilitar AAD-Pod-Identity para um pod ou aplicativo específico](https://azure.github.io/aad-pod-identity/docs/configure/application_exception).
  Para configurar uma exceção, instale o [YAML de exceção do MIC](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml).

O complemento Azure Policy requer recursos de CPU e memória para operar. Esses requisitos aumentam à medida que o tamanho de um cluster aumenta. Consulte [Azure Policy recomendações][policy-recommendations] para obter diretrizes gerais para usar o complemento Azure Policy.

## <a name="azure-policies-to-secure-kubernetes-pods"></a>Políticas do Azure para proteger os pods Kubernetess

Depois de instalar o complemento Azure Policy, nenhuma política é aplicada por padrão.

Há 11 políticas internas do Azure individuais e duas iniciativas internas que protegem os pods especificamente em um cluster AKS.
Cada política pode ser personalizada com um efeito. Uma lista completa das [políticas de AKs e seus efeitos com suporte está listada aqui][policy-samples]. Leia mais sobre [efeitos de Azure Policy](../governance/policy/concepts/effects.md).

As políticas do Azure podem ser aplicadas no nível do grupo de gerenciamento, da assinatura ou do grupo de recursos. Ao atribuir uma política no nível do grupo de recursos, verifique se o grupo de recursos do cluster AKS de destino está selecionado no escopo da política. Cada cluster no escopo atribuído com o complemento de Azure Policy instalado está no escopo da política.

Se estiver usando a [política de segurança de Pod (versão prévia) ](use-pod-security-policies.md), saiba como [migrar para Azure Policy e sobre outras diferenças comportamentais](#migrate-from-kubernetes-pod-security-policy-to-azure-policy).

### <a name="built-in-policy-initiatives"></a>Iniciativas de políticas internas

Uma iniciativa no Azure Policy é uma coleção de definições de políticas que são adaptadas para atingir uma meta abrangente singular. O uso de iniciativas pode simplificar o gerenciamento e a atribuição de políticas em clusters AKS. Existe uma iniciativa como um único objeto, saiba mais sobre as [iniciativas de Azure Policy](../governance/policy/overview.md#initiative-definition).

O Azure Policy para kubernetes oferece duas iniciativas internas, que protegem pods, [linha de base](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) e [restrições](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00).

Ambas as iniciativas internas são criadas a partir de definições usadas na [política de segurança de Pod do kubernetes](https://github.com/kubernetes/website/blob/master/content/en/examples/policy/baseline-psp.yaml).

|[Controle de política de segurança Pod](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#what-is-a-pod-security-policy)| Link de definição de Azure Policy| [Iniciativa de linha de base](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) | [Iniciativa restrita](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00) |
|---|---|---|---|
|Não permitir a execução de contêineres com privilégios|[Nuvem pública](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95edb821-ddaf-4404-9732-666045e056b4)| Sim | Sim
|Não permitir uso compartilhado de namespaces de host|[Nuvem pública](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a1ee2f-2a2a-4576-bf2a-e0e36709c2b8)| Sim | Sim
|Restringir todo o uso de rede e portas do host|[Nuvem pública](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82985f06-dc18-4a48-bc1c-b9f4f0098cfe)| Sim | Sim
|Restringir qualquer uso do sistema de arquivos do host|[Nuvem pública](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F098fc59e-46c7-4d99-9b16-64990e543d75)| Sim | Sim
|Restringir os recursos do Linux ao [conjunto padrão](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities)|[Nuvem pública](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc26596ff-4d70-4e6a-9a30-c2506bd2f80c) | Sim | Sim
|Restringir o uso de tipos de volume definidos|[Nuvem pública](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F16697877-1118-4fb1-9b65-9898ec2509ec)| - | Sim-os tipos de volume permitidos são,,, `configMap` `emptyDir` `projected` `downwardAPI` , `persistentVolumeClaim`|
|Elevação de privilégio para raiz|[Nuvem pública](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c6e92c9-99f0-4e55-9cf2-0c234dc48f99) | - | Yes |
|Restringir as IDs de usuário e grupo do contêiner|[Nuvem pública](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff06ddb64-5fa3-4b77-b166-acb36f7f6042) | - | Yes|
|Restringir a alocação de um FSGroup que possui os volumes do pod|[Nuvem pública](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff06ddb64-5fa3-4b77-b166-acb36f7f6042) | - | Sim-as regras permitidas são `runAsUser: mustRunAsNonRoot` ,, `supplementalGroup: mustRunAs 1:65536` `fsGroup: mustRunAs 1:65535` , `runAsGroup: mustRunAs 1:65535` .  |
|Requer perfil seccomp|[Nuvem pública](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F975ce327-682c-4f2e-aa46-b9598289b86c) | - | Sim, allowedProfiles são * `docker/default` ou `runtime/default` |

\* o Docker/padrão é preterido no kubernetes desde o v 1.11

### <a name="additional-optional-policies"></a>Políticas opcionais adicionais

Há políticas adicionais do Azure, que podem ser aplicadas individualmente fora da aplicação de uma iniciativa. Considere acrescentar essas políticas, além de iniciativas, caso suas necessidades não sejam atendidas pelas iniciativas internas.

|[Controle de política de segurança Pod](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#what-is-a-pod-security-policy)| Link de definição de Azure Policy| Aplicar além da iniciativa de linha de base | Aplicar além da iniciativa restrita |
|---|---|---|---|
|Definir o perfil AppArmor usado por contêineres|[Nuvem pública](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F511f5417-5d12-434d-ab2e-816901e72a5e) | Opcional | Opcional |
|Permitir montagens que não são somente leitura|[Nuvem pública](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf49d893-a74c-421d-bc95-c663042e5b80) | Opcional | Opcional |
|Restringir a drivers FlexVolume específicos|[Nuvem pública](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4a8fce0-2dd5-4c21-9a36-8f0ec809d663) | Opcional – use se você quiser restringir apenas drivers FlexVolume, mas não outros definidos por "restringir o uso de tipos de volume definidos" | Não aplicável-a iniciativa restrita inclui "restringir o uso de tipos de volumes definidos", que não permite todos os drivers FlexVolume |

### <a name="unsupported-built-in-policies-for-managed-aks-clusters"></a>Políticas internas sem suporte para clusters AKS gerenciados

> [!NOTE]
> As três políticas a seguir **não têm suporte no AKs** devido à personalização de aspectos gerenciados e protegidos pelo AKs como um serviço gerenciado. Essas políticas são criadas especificamente para clusters conectados do Arc do Azure com planos de controle não gerenciados.

|[Controle de política de segurança Pod](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#what-is-a-pod-security-policy)|
|---|
|Definir o contexto SELinux personalizado de um contêiner|
|Restringir o perfil de sysctl usado por contêineres|
|Os tipos de montagem proc padrão são definidos para reduzir a superfície de ataque|

<!---
# Removing until custom initiatives are supported the week after preview

#### Custom initiative

If the built-in initiatives to address pod security do not match your requirements, you can choose your own policies to exist in a custom initiative. Read more about [building custom initatives in Azure Policy](../governance/policy/tutorials/create-and-manage#create-and-assign-an-initiative-definition).

--->

### <a name="namespace-exclusion"></a>Exclusão de namespace

> [!WARNING]
> Os pods em namespaces de administrador, como Kube, devem ser executados para que um cluster permaneça íntegro, removendo um namespace necessário da lista de namespaces padrão excluídos pode disparar violações de política devido a um pod de sistema necessário.

O AKS exige que o pods do sistema seja executado em um cluster para fornecer serviços críticos, como a resolução DNS. Políticas que limitam a funcionalidade Pod podem afetar a estabilidade do Pod do sistema. Como resultado, os namespaces a seguir são **excluídos da avaliação de política durante solicitações de admissão durante a criação, atualização e auditoria de política**. Isso força as novas implantações a esses namespaces serem excluídas das políticas do Azure.

1. Kube-sistema
1. sistema gatekeeper
1. Azure-arco
1. AKs-Periscope

Namespaces personalizados adicionais podem ser excluídos da avaliação durante a criação, atualização e auditoria. Essas exclusões devem ser usadas se você tiver pods especializados que são executados em um namespace aprovado e deseja evitar o disparo de violações de auditoria.

## <a name="apply-the-baseline-initiative"></a>Aplicar a iniciativa de linha de base

> [!TIP]
> Todas as políticas assumem como padrão um efeito de auditoria. Os efeitos podem ser atualizados para negar a qualquer momento por meio de Azure Policy.

Para aplicar a iniciativa de linha de base, podemos atribuir por meio do portal do Azure.
<!--
1. Navigate to the Policy service in Azure portal
1. In the left pane of the Azure Policy page, select **Definitions**
1. Search for "Baseline Profile" on the search pane to the right of the page
1. Select `Kubernetes Pod Security Standards Baseline Profile for Linux-based workloads` from the `Kubernetes` category
-->
1. Siga [este link para a Portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) para examinar a iniciativa de linha de base de segurança Pod
1. Defina o **escopo** para o nível de assinatura ou apenas o grupo de recursos que contém clusters AKs com o complemento Azure Policy habilitado
1. Selecione a página **parâmetros** e atualize o **efeito** de `audit` para `deny` para bloquear novas implantações violando a iniciativa de linha de base
1. Adicionar namespaces adicionais para excluir da avaliação durante a criação, atualização e auditoria, [alguns namespaces são excluídos à força da avaliação da política.](#namespace-exclusion) 
 ![ efeito de atualização](media/use-pod-security-on-azure-policy/update-effect.png)
1. Selecione **examinar + criar** para enviar as políticas

Confirme se as políticas são aplicadas ao cluster executando `kubectl get constrainttemplates` .

> [!NOTE]
> As políticas podem levar [até 20 minutos para serem sincronizadas](../governance/policy/concepts/policy-for-kubernetes.md#assign-a-built-in-policy-definition) em cada cluster.

A saída deve ser semelhante a:

```console
$ kubectl get constrainttemplate
NAME                                     AGE
k8sazureallowedcapabilities              30m
k8sazureblockhostnamespace               30m
k8sazurecontainernoprivilege             30m
k8sazurehostfilesystem                   30m
k8sazurehostnetworkingports              30m
```

## <a name="validate-rejection-of-a-privileged-pod"></a>Validar a rejeição de um pod privilegiado

Primeiro, vamos testar o que acontece quando você agenda um pod com o contexto de segurança de `privileged: true` . Esse contexto de segurança Escalona os privilégios do pod. A iniciativa de linha de base não permite pods privilegiado, portanto, a solicitação será negada, resultando na rejeição da implantação.

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

## <a name="test-creation-of-an-unprivileged-pod"></a>Teste de criação de um pod sem privilégios

No exemplo anterior, a imagem de contêiner tentou usar automaticamente a raiz para associar NGINX à porta 80. Essa solicitação foi negada pela iniciativa de diretiva de linha de base, portanto, o Pod não pôde ser iniciado. Vamos tentar executar o mesmo Pod NGINX sem acesso privilegiado.

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

O Pod foi agendado com êxito. Quando você verifica o status do pod usando o comando [kubectl Get pods][kubectl-get] , o Pod está *em execução* :

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

## <a name="disable-policies-and-the-azure-policy-add-on"></a>Desabilitar políticas e o complemento Azure Policy

Para remover a iniciativa de linha de base:

1. Navegue até o painel de política no portal do Azure
1. Selecionar **atribuições** no painel esquerdo
1. Clique no botão "..." botão ao lado do perfil de linha de base
1. Selecione "excluir atribuição"

![Excluir atribuição](media/use-pod-security-on-azure-policy/delete-assignment.png)

Para desabilitar o complemento Azure Policy, use o comando [AZ AKs Disable-addons][az-aks-disable-addons] .

```azurecli
az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
```

Saiba como remover o [complemento Azure Policy do portal do Azure](../governance/policy/concepts/policy-for-kubernetes.md#remove-the-add-on-from-aks).

## <a name="migrate-from-kubernetes-pod-security-policy-to-azure-policy"></a>Migrar da política de segurança de Pod kubernetes para Azure Policy

Para migrar da política de segurança de Pod, você precisa executar as seguintes ações em um cluster.

1. [Desabilitar política de segurança de Pod](use-pod-security-policies.md#clean-up-resources) no cluster
1. Habilitar o [complemento de Azure Policy][kubernetes-policy-reference]
1. Habilitar as políticas do Azure desejadas de [políticas internas disponíveis][policy-samples]

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

## <a name="next-steps"></a>Próximas etapas

Este artigo mostrou como aplicar uma política do Azure que restringe o pods privilegiado de ser implantado para evitar o uso de acesso privilegiado. Há muitas políticas que podem ser aplicadas, como políticas que restringem o uso de volumes. Para obter mais informações sobre as opções disponíveis, consulte o [Azure Policy para documentos de referência do kubernetes][kubernetes-policy-reference].

Para obter mais informações sobre como limitar o tráfego de rede Pod, consulte [proteger o tráfego entre pods usando as políticas de rede no AKs][network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity
[aad-pod-identity-exception]: https://azure.github.io/aad-pod-identity/docs/configure/application_exception

<!-- LINKS - internal -->
[policy-recommendations]: ../governance/policy/concepts/policy-for-kubernetes.md
[policy-limitations]: ../governance/policy/concepts/policy-for-kubernetes.md?#limitations
[kubernetes-policy-reference]: ../governance/policy/concepts/policy-for-kubernetes.md
[policy-samples]: ./policy-reference.md#microsoftcontainerservice
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
[az-aks-disable-addons]: /cli/azure/aks#az-aks-disable-addons
[az-policy-assignment-delete]: /cli/azure/policy/assignment#az-policy-assignment-delete
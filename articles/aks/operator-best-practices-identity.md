---
title: Práticas recomendadas para gerenciamento de identidade
titleSuffix: Azure Kubernetes Service
description: Aprenda as práticas recomendadas do operador de cluster sobre como gerenciar autenticação e autorização para clusters no Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 8e0c7324f5b73b3a2ac5e5fd6fa256202035077a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98790962"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Práticas recomendadas para autenticação e autorização no Azure Kubernetes Service (AKS)

À medida que você implanta e mantém clusters no Azure Kubernetes Service (AKS), é preciso implementar maneiras de gerenciar o acesso a recursos e serviços. Sem esses controles, as contas podem ter acesso a recursos e serviços de que não precisam. Também pode ser difícil rastrear qual conjunto de credenciais foi usado para fazer alterações.

Este artigo de práticas recomendadas se concentra em como um operador de cluster pode gerenciar o acesso e a identidade dos clusters do AKS. Neste artigo, você aprenderá como:

> [!div class="checklist"]
>
> * Autenticar usuários de cluster do AKS com o Azure Active Directory Domain Services
> * Controlar o acesso a recursos com o controle de acesso baseado em função do kubernetes (kubernetes RBAC)
> * Use o Azure RBAC para controlar de forma granular o acesso ao recurso AKS e à API kubernetes em escala, bem como ao kubeconfig.
> * Usar uma identidade gerenciada para autenticar os pods em si mesmo com outros serviços

## <a name="use-azure-active-directory"></a>Use o Azure Active Directory Domain Services

**Orientação sobre práticas recomendadas**: implante clusters do AKS com a integração do Microsoft Azure Active Directory. Usando o Microsoft Azure Active Directory centraliza o componente de gerenciamento de identidade. Qualquer alteração na conta do usuário ou no status do grupo é atualizada automaticamente no acesso ao cluster do AKS. Use Roles ou ClusterRoles e Bindings, conforme discutido na próxima seção, para direcionar usuários ou grupos para a menor quantidade de permissões necessárias.

Os desenvolvedores e proprietários de aplicativos do cluster do Kubernetes precisam ter acesso a recursos diferentes. O Kubernetes não fornece uma solução de gerenciamento de identidades para controlar quais usuários podem interagir com quais recursos. Em vez disso, você normalmente integra seu cluster a uma solução de identidade existente. O Azure Active Directory Domais Services (AD) fornece uma solução de gerenciamento de identidades pronta para a empresa e pode se integrar aos clusters do AKS.

Com os clusters integrados do Microsoft Azure Active Directory no AKS, você cria *Funções* ou *ClusterRoles* que definem as permissões de acesso aos recursos. Em seguida, você *vincula* as funções a usuários ou grupos do Microsoft Azure Active Directory. Esses kubernetes RBAC (controle de acesso baseado em função) kubernetes são discutidos na próxima seção. A integração do Microsoft Azure Active Directory e como você controla o acesso a recursos pode ser vista no diagrama a seguir:

![Autenticação no nível de cluster para integração do Azure Active Directory Domain Services com o AKS](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. O desenvolvedor autentica com o Microsoft Azure Active Directory.
1. O ponto de extremidade de emissão de token do Azure AD emite o token de acesso.
1. O desenvolvedor realiza uma ação usando o token do Azure AD, como `kubectl create pod`
1. O Kubernetes valida o token com o Azure Active Directory Domain Services e busca as associações de grupo do desenvolvedor.
1. O controle de acesso baseado em função do kubernetes (kubernetes RBAC) e as políticas de cluster são aplicadas.
1. A solicitação do desenvolvedor foi bem-sucedida ou não com base na validação anterior da associação do grupo do Microsoft Azure Active Directory e do Kubernetes RBAC e políticas.

Para criar um cluster do AKS que usa o Azure AD, consulte [Integre o Microsoft Azure Active Directory Domain Services ao AKS][aks-aad].

## <a name="use-kubernetes-role-based-access-control-kubernetes-rbac"></a>Usar o controle de acesso baseado em função do kubernetes (kubernetes RBAC)

**Orientação sobre práticas recomendadas**: use o RBAC do Kubernetes para definir as permissões que os usuários ou grupos têm para os recursos no cluster. Crie funções e ligações que atribuam a menor quantidade de permissões necessárias. Integre-se ao Microsoft Azure Active Directory para que qualquer alteração no status do usuário ou na associação ao grupo seja atualizada automaticamente e o acesso aos recursos do cluster seja atual.

No kubernetes, você pode fornecer um controle granular do acesso aos recursos no cluster. As permissões são definidas no nível do cluster ou em namespaces específicos. Você pode definir quais recursos podem ser gerenciados e com quais permissões. Essas funções são então aplicadas a usuários ou grupos com uma associação. Para obter mais informações sobre *Funções*, *ClusterRoles* e *Vinculações*, consulte [Opções de acesso e identidade para o Serviço de Kubernetes do Azure (AKS)][aks-concepts-identity].

Como exemplo, você pode criar uma Função que conceda acesso total aos recursos no namespace denominado *finance-app*, conforme mostrado no seguinte exemplo de manifesto YAML:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role
  namespace: finance-app
rules:
- apiGroups: [""]
  resources: ["*"]
  verbs: ["*"]
```

Em seguida, é criada uma função que associa o usuário do Azure AD *developer1 \@ contoso.com* ao rolebinding, conforme mostrado no seguinte manifesto YAML:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role-binding
  namespace: finance-app
subjects:
- kind: User
  name: developer1@contoso.com
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: finance-app-full-access-role
  apiGroup: rbac.authorization.k8s.io
```

Quando *developer1 \@ contoso.com* é autenticado no cluster AKs, ele tem permissões completas para recursos no namespace *Finance-app* . Dessa forma, você separa e controla logicamente o acesso aos recursos. O Kubernetes RBAC deve ser usado em conjunto com a integração do Microsoft Azure Active Directory, conforme discutido na seção anterior.

Para saber como usar os grupos do Azure AD para controlar o acesso aos recursos do kubernetes usando o RBAC do kubernetes, consulte [controlar o acesso aos recursos de cluster usando o controle de acesso baseado em função e as identidades de Azure Active Directory no AKs][azure-ad-rbac].

## <a name="use-azure-rbac"></a>Usar o RBAC do Azure 
**Diretrizes de práticas recomendadas** -use o RBAC do Azure para definir as permissões mínimas necessárias que os usuários ou grupos têm para AKs recursos em uma ou mais assinaturas.

Há dois níveis de acesso necessários para operar totalmente um cluster AKS: 
1. Acesse o recurso AKS em sua assinatura do Azure. Esse nível de acesso permite que você controle as coisas que dimensionam ou atualizam o cluster usando as APIs AKS, bem como efetuam pull de seu kubeconfig.
Para saber como controlar o acesso ao recurso AKS e ao kubeconfig, consulte [limitar o acesso ao arquivo de configuração de cluster](control-kubeconfig-access.md).

2. Acesso à API do kubernetes. Esse nível de acesso é controlado por [KUBERNETES RBAC](#use-kubernetes-role-based-access-control-kubernetes-rbac) (tradicionalmente) ou integrando o RBAC do Azure com AKs para autorização kubernetes.
Para ver como dar permissões de forma granular à API kubernetes usando o RBAC do Azure, consulte [usar o RBAC do Azure para autorização do kubernetes](manage-azure-rbac.md).

## <a name="use-pod-managed-identities"></a>Usar identidades gerenciadas por Pod

**Orientação sobre práticas recomendadas**: não use credenciais fixas em conjuntos ou imagens de contêiner, pois elas correm risco de exposição ou abuso. Em vez disso, use as identidades do conjunto para solicitar acesso automaticamente usando uma solução central de identidade do Microsoft Azure Active Directory. As identidades de Pod são destinadas ao uso somente com as imagens de contêiner e pods do Linux.

> [!NOTE]
> O suporte a identidades gerenciadas por pod para contêineres do Windows estará disponível em breve.

Quando os pods precisam de acesso a outros serviços do Azure, como o Cosmos DB, Key Vault ou Blob Storage, o pod precisa de credenciais de acesso. Essas credenciais de acesso podem ser definidas com a imagem do contêiner ou injetadas como um segredo do Kubernetes, mas precisam ser criadas e atribuídas manualmente. Geralmente, as credenciais são reutilizadas nos pods e não são rotacionadas regularmente.

As identidades gerenciadas pelo pod para recursos do Azure permitem solicitar automaticamente o acesso aos serviços por meio do Azure AD. Agora, as identidades gerenciadas por Pod estão atualmente em visualização para o serviço kubernetes do Azure. Consulte a documentação [usar Azure Active Directory de identidades gerenciadas por pod no serviço kubernetes do Azure (versão prévia)]( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity) para começar. Com identidades gerenciadas por Pod, você não define manualmente as credenciais para pods, em vez disso, eles solicitam um token de acesso em tempo real e podem usá-lo para acessar somente seus serviços atribuídos. No AKS, há dois componentes que lidam com as operações para permitir que os pods usem identidades gerenciadas:

* **O servidor NMI (Node Management Identity)** é um pod executado como um DaemonSet em cada nó no cluster AKS. O servidor NMI ouve solicitações de pod a serviços do Azure.
* **O provedor de recursos do Azure** consulta o servidor de API kubernetes e verifica um mapeamento de identidade do Azure que corresponde a um pod.

Quando pods solicitam acesso a um serviço do Azure, as regras de rede redirecionam o tráfego para o servidor NMI (Node Management Identity). O servidor NMI identifica pods que solicita acesso aos serviços do Azure com base em seu endereço remoto e consulta o provedor de recursos do Azure. O provedor recurso do Azure verifica mapeamentos de identidade do Azure no cluster AKS, e o servidor NMI solicita um token de acesso do Azure Active Directory (AD) com base no mapeamento de identidade do pod. O Microsoft Azure Active Directory fornece acesso ao servidor NMI, que é retornado ao pod. Esse token de acesso pode ser usado pelo pod para solicitar acesso aos serviços no Azure.

No exemplo a seguir, um desenvolvedor cria um pod que usa uma identidade gerenciada para solicitar acesso ao banco de dados SQL do Azure:

![As identidades do pod permitem que um pod solicite automaticamente o acesso a outros serviços](media/operator-best-practices-identity/pod-identities.png)

1. O operador de cluster primeiro cria uma conta de serviço que pode ser usada para mapear identidades quando os pods solicitam acesso aos serviços.
1. O servidor NMI é implantado para retransmitir quaisquer solicitações de Pod, juntamente com o provedor de recursos do Azure, para tokens de acesso ao Azure AD.
1. Um desenvolvedor implanta um pod com uma identidade gerenciada que solicita um token de acesso por meio do servidor NMI.
1. O token é retornado para o pod e usado para acessar o banco de dados SQL do Azure

> [!NOTE]
> Atualmente, as identidades gerenciadas por Pod estão no status de visualização.

Para usar identidades gerenciadas por Pod, consulte [usar Azure Active Directory identidades gerenciadas por pod no serviço kubernetes do Azure (versão prévia)]( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity).

## <a name="next-steps"></a>Próximas etapas

Este artigo de práticas recomendadas enfocou a autenticação e a autorização de seu cluster e recursos. Para implementar algumas dessas práticas recomendadas, consulte os seguintes artigos:

* [Integrar o Azure Active Directory ao AKS][aks-aad]
* [Usar Azure Active Directory identidades gerenciadas por pod no serviço kubernetes do Azure (versão prévia)]( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity)

Para obter mais informações sobre operações de cluster no AKS, consulte as seguintes práticas recomendadas:

* [Multilocação e isolamento de cluster][aks-best-practices-cluster-isolation]
* [Recursos básicos de Agendador Kubernetes][aks-best-practices-scheduler]
* [Recursos avançados de Agendador Kubernetes][aks-best-practices-advanced-scheduler]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-aad]: azure-ad-integration-cli.md
[managed-identities:]: ../active-directory/managed-identities-azure-resources/overview.md
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[azure-ad-rbac]: azure-ad-rbac.md

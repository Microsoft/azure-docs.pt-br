---
title: Práticas recomendadas para segurança de cluster
titleSuffix: Azure Kubernetes Service
description: Aprender as práticas recomendadas do operador de cluster sobre como gerenciar as atualizações e a segurança de clusters no AKS (Serviço de Kubernetes do Azure)
services: container-service
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: a56cf35fe3780aa53b12581358bd91fe44e8c8a1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104585053"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Práticas recomendadas para atualizações e segurança de clusters no AKS (Serviço de Kubernetes do Azure)

Ao gerenciar clusters no serviço de Kubernetes do Azure (AKS), a segurança dos seus dados e cargas de trabalho é uma consideração importante. Especialmente quando você executa os clusters de vários locatários usando o isolamento lógico, você precisa proteger o acesso aos recursos e cargas de trabalho. Para minimizar o risco de ataque, você também não pode deixar de aplicar as atualizações de segurança mais recentes do sistema operacional do nó e do Kubernetes.

Este artigo se concentra em como proteger seu cluster do AKS. Você aprenderá como:

> [!div class="checklist"]
> * Azure Active Directory usar o kubernetes e o controle de acesso baseado em função (kubernetes RBAC) para proteger o acesso do servidor de API
> * Proteger o acesso do contêiner a recursos do nó
> * Atualizar um cluster do AKS para a versão mais recente do Kubernetes
> * Manter os nós atualizados e aplicar automaticamente os patches de segurança

Você também pode ler as práticas recomendadas para [gerenciamento de imagens de contêiner][best-practices-container-image-management] e para [segurança de pod][best-practices-pod-security].

Você também pode usar a [integração dos serviços Kubernetess do Azure com a central de segurança][security-center-aks] para ajudar a detectar ameaças e exibir recomendações para proteger seus clusters do AKS.

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>Proteger o acesso aos nós de cluster e ao servidor de API

**Diretrizes de práticas recomendadas** – proteger o acesso ao servidor de API do Kubernetes é uma das coisas mais importantes que você pode fazer para proteger o cluster. Integre o controle de acesso baseado em função do kubernetes (kubernetes RBAC) com Azure Active Directory para controlar o acesso ao servidor de API. Esses controles permitem que você proteja o AKS da mesma maneira que protege o acesso às assinaturas do Azure.

O servidor de API do Kubernetes fornece um único ponto de conexão para que as solicitações realizem ações em um cluster. Para proteger e auditar o acesso ao servidor de API, limite o acesso e forneça as permissões de acesso com o mínimo necessário de privilégios. Essa abordagem não é exclusiva para o Kubernetes, mas é especialmente importante quando o cluster do AKS é logicamente isolado para uso com vários locatários.

O Azure AD (Active Directory) fornece uma solução de gerenciamento de identidades pronta para a empresa que se integra aos clusters do AKS. Se essa solução não existisse, poderia ser difícil fornecer uma maneira granular para restringir o acesso ao servidor de API, já que o Kubernetes não fornece uma solução de gerenciamento de identidades. Com os clusters integrados ao Azure AD no AKS, você usa suas contas de grupo e de usuário existentes para autenticar usuários no servidor de API.

![Integração ao Azure Active Directory para clusters do AKS](media/operator-best-practices-cluster-security/aad-integration.png)

Use o RBAC do Kubernetes e a integração com o Azure AD para proteger o servidor de API e fornecer o menor número necessário de permissões para um conjunto de recursos com escopo, assim como um único namespace. Diferentes usuários ou grupos no Azure AD podem receber diferentes funções kubernetes. Essas permissões granulares permitem que você restrinja o acesso ao servidor de API e forneça uma trilha de auditoria clara das ações realizadas.

A prática recomendada é usar grupos para fornecer acesso a arquivos e pastas versus identidades individuais, usar a associação de *grupo* do Azure ad para associar usuários a funções kubernetes em vez de *usuários* individuais. Conforme a associação de grupo de um usuário sofre alterações, suas permissões de acesso no cluster do AKS são alteradas correspondentemente. Se você associar o usuário diretamente a uma função, a função de trabalho desse usuário poderá mudar. As associações a grupos do Azure AD seriam atualizadas, mas as permissões no cluster do AKS não refletiriam isso. Nesse cenário, acabam sendo concedidas mais permissões ao usuário do que ele precisa.

Para obter mais informações sobre a integração do Azure AD, o RBAC do kubernetes e o RBAC do Azure, consulte [práticas recomendadas para autenticação e autorização no AKs][aks-best-practices-identity].

## <a name="secure-container-access-to-resources"></a>Proteger o acesso do contêiner a recursos

**Diretrizes de práticas recomendadas** – limite o acesso às ações que podem ser executadas por contêineres. Forneça o menor número possível de permissões e evite o uso da raiz e de escalonamento de privilégios.

Da mesma forma que você deve conceder aos usuários ou grupos o menor número necessário de privilégios, os contêineres também devem ser limitados a apenas às ações e aos processos de que precisam. Para minimizar o risco de ataque, não configure aplicativos e contêineres que exijam privilégios escalados ou acesso à raiz. Por exemplo, defina `allowPrivilegeEscalation: false` no manifesto do pod. Estes *contextos de segurança do pod* são incorporados ao Kubernetes e permitem que você defina permissões adicionais, tais como o usuário ou o grupo como o qual executar ou quais funcionalidades do Linux expor. Para obter mais práticas recomendadas, confira [Proteger o acesso do pod a recursos][pod-security-contexts].

Para um controle mais granular das ações de contêiner, você também pode usar recursos internos de segurança do Linux, tais como *AppArmor* e *seccomp*. Esses recursos são definidos no nível do nó e, em seguida, são implementados por meio de um manifesto de pod. Os recursos de segurança internos do Linux estão disponíveis apenas em nós e pods do Linux.

> [!NOTE]
> Os ambientes do Kubernetes, no AKS ou em outro lugar, não estão completamente seguros para uso de vários locatários hostis. Recursos de segurança adicionais como *AppArmor*, *seccomp*, *políticas de segurança Pod* ou mais refinado kubernetes controle de acesso baseado em função (kubernetes RBAC) para nós tornam as explorações mais difíceis. No entanto, para ter uma segurança de verdade ao executar cargas de trabalho de vários locatários hostis, um hipervisor é o único nível de segurança no qual você deve confiar. O domínio de segurança para o Kubernetes se torna o cluster inteiro, não um nó individual. Para esses tipos de cargas de trabalho de vários locatários hostis, você deve usar clusters fisicamente isolados.

### <a name="app-armor"></a>Armadura de aplicativo

Para limitar as ações que os contêineres podem realizar, é possível usar o módulo de segurança de kernel do Linux [AppAmour][k8s-apparmor]. O AppArmor está disponível como parte do sistema operacional do nó do AKS subjacente e é habilitado por padrão. Você cria perfis do AppArmor que restringem ações como leitura, gravação ou execução ou ainda funções de sistema como montagem de sistemas de arquivos. Perfis padrão do AppArmor restringem o acesso a vários locais `/proc` e `/sys` e fornecem um meio para isolar logicamente os contêineres do nó subjacente. O AppArmor funciona para qualquer aplicativo executado no Linux, não apenas para os pods do Kubernetes.

![Perfis de AppArmor em uso em um cluster do AKS para limitar as ações de contêiner](media/operator-best-practices-container-security/apparmor.png)

Para ver o AppArmor em ação, o exemplo a seguir cria um perfil que impede a gravação em arquivos. Conecte-se por [SSH][aks-ssh] a um nó do AKS e, em seguida, crie um arquivo chamado *deny-write.profile* e cole-o no seguinte conteúdo:

```
#include <tunables/global>
profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
  #include <abstractions/base>
  
  file,
  # Deny all file writes.
  deny /** w,
}
```

Perfis de AppArmor são adicionados usando o comando `apparmor_parser`. Adicione o perfil ao AppArmor e especifique o nome do perfil criado na etapa anterior:

```console
sudo apparmor_parser deny-write.profile
```

Nenhuma saída é retornada se o perfil é corretamente analisado e aplicado ao AppArmor. Você retornará ao prompt de comando.

Em seu computador local, crie agora um manifesto de pod chamado *aks-apparmor.yaml* e cole o conteúdo a seguir. Esse manifesto define uma anotação para `container.apparmor.security.beta.kubernetes` e faz referência ao perfil *deny-write* criado nas etapas anteriores:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-apparmor
  annotations:
    container.apparmor.security.beta.kubernetes.io/hello: localhost/k8s-apparmor-example-deny-write
spec:
  containers:
  - name: hello
    image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
    command: [ "sh", "-c", "echo 'Hello AppArmor!' && sleep 1h" ]
```

Implante o pod de exemplo usando o comando [kubectl apply][kubectl-apply]:

```console
kubectl apply -f aks-apparmor.yaml
```

Com o Pod implantado, use verificar se o Pod *Hello-AppArmor* é mostrado como *bloqueado*:

```
$ kubectl get pods

NAME             READY   STATUS    RESTARTS   AGE
aks-ssh          1/1     Running   0          4m2s
hello-apparmor   0/1     Blocked   0          50s
```

Para obter mais informações sobre AppArmor, confira [Perfis do AppArmor no Kubernetes][k8s-apparmor].

### <a name="secure-computing"></a>Computação segura

Enquanto o AppArmor funciona para qualquer aplicativo do Linux, o [seccomp (*comp* utação *seg* ura)][seccomp] funciona no nível do processo. O seccomp também é um módulo de segurança de kernel do Linux e tem compatibilidade nativa com o runtime do Docker usado por nós do AKS. Com o seccomp, as chamadas de processo que os contêineres podem executar são limitadas. Você cria filtros que definem quais ações permitir ou negar e, em seguida, usa anotações em um manifesto YAML do pod para associar com o filtro do seccomp. Isso se alinha com a prática recomendada de conceder ao contêiner apenas o mínimo necessário de permissões para sua execução e nada mais.

Para ver o seccomp em ação, crie um filtro que impeça a alteração das permissões em um arquivo. Conecte-se por [SSH][aks-ssh] a um nó do AKS e, em seguida, crie um filtro do seccomp chamado */var/lib/kubelet/seccomp/prevent-chmod* e cole o seguinte conteúdo:

```
{
  "defaultAction": "SCMP_ACT_ALLOW",
  "syscalls": [
    {
      "name": "chmod",
      "action": "SCMP_ACT_ERRNO"
    }
  ]
}
```

Em seu computador local, crie agora um manifesto de pod chamado *aks-seccomp.yaml* e cole o conteúdo a seguir. Esse manifesto define uma anotação para `seccomp.security.alpha.kubernetes.io` e faz referência ao perfil *deny-write* criado na etapa anterior:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: chmod-prevented
  annotations:
    seccomp.security.alpha.kubernetes.io/pod: localhost/prevent-chmod
spec:
  containers:
  - name: chmod
    image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
    command:
      - "chmod"
    args:
     - "777"
     - /etc/hostname
  restartPolicy: Never
```

Implante o pod de exemplo usando o comando [kubectl apply][kubectl-apply]:

```console
kubectl apply -f ./aks-seccomp.yaml
```

Exiba o status dos pods usando o comando [kubectl get pods][kubectl-get]. O pod relata um erro. O comando `chmod` é impedido de executar pelo filtro do seccomp, conforme mostrado na saída de exemplo a seguir:

```
$ kubectl get pods

NAME                      READY     STATUS    RESTARTS   AGE
chmod-prevented           0/1       Error     0          7s
```

Para obter mais informações sobre os filtros disponíveis, confira [Perfis de segurança do seccomp para o Docker][seccomp].

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>Atualizar frequentemente para a versão mais recente do Kubernetes

**Diretrizes de práticas recomendadas** -para se manter atualizado sobre os novos recursos e correções de bugs, Atualize regularmente a versão do kubernetes no cluster do AKS.

O Kubernetes lança novos recursos em um ritmo mais rápido que aquele das plataformas de infraestrutura mais tradicionais. As atualizações do Kubernetes incluem novos recursos e correções de bug ou de segurança. Novos recursos normalmente começam com um status *alfa* e, em seguida, *beta*, antes que se tornem *estáveis*, disponíveis ao público em geral e recomendados para uso em produção. Esse ciclo de lançamento deve permitir que você atualize o Kubernetes sem frequentemente encontrar alterações significativas ou ajustar suas implantações e modelos.

O AKS dá suporte a três versões secundárias do kubernetes. Isso significa que quando uma nova versão secundária de patch é introduzida, as versões secundárias e de patch compatíveis mais antigas são desativadas. As atualizações secundárias para Kubernetes ocorrem em intervalos periódicos. Assegure que você tenha um processo de governança para verificar e atualizar conforme necessário, para que você não fique sem suporte. Para obter mais informações, consulte [supported kubernetes Versions AKs][aks-supported-versions].

Para verificar quais versões estão disponíveis para seu cluster, use o comando [az aks get-upgrades][az-aks-get-upgrades] conforme mostrado no exemplo a seguir:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Você pode atualizar seu cluster do AKS usando o comando [az aks upgrade][az-aks-upgrade]. O processo de atualização, com segurança, isola e esvazia um nó por vez, agenda pods naqueles nós restantes e, em seguida, implanta um novo nó executando as versões mais recentes do SO e do Kubernetes.

É altamente recomendável testar novas versões secundárias em um ambiente de teste de desenvolvimento para que você possa validar sua carga de trabalho continue com a operação íntegra com a nova versão kubernetes. O kubernetes pode substituir as APIs, como na versão 1,16, que poderia ser contada por suas cargas de trabalho. Ao trazer novas versões para produção, considere o uso de [vários pools de nó em versões separadas](use-multiple-node-pools.md) e a atualização de pools individuais, um de cada vez, para reverter progressivamente a atualização por um cluster. Se estiver executando vários clusters, atualize um cluster por vez para monitorar progressivamente o impacto ou as alterações.

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version KUBERNETES_VERSION
```

Para obter mais informações sobre as atualizações no AKS, confira [Versões compatíveis do Kubernetes no AKS][aks-supported-versions] e [Atualizar um cluster do AKS][aks-upgrade].

## <a name="process-linux-node-updates-and-reboots-using-kured"></a>Processar atualizações e reinicializações do nó do Linux usando o kured

**Diretrizes de práticas recomendadas** – o AKs automaticamente baixa e instala correções de segurança em cada nó do Linux, mas não é reinicializado automaticamente, se necessário. Use `kured` para ficar atento para reinicializações pendentes e, depois, isole e esvazie o nó com segurança para permitir que ele reinicialize. Aplique as atualizações e faça tudo do modo mais seguro possível com relação ao sistema operacional. Para nós do Windows Server, execute regularmente uma operação de atualização de AKS para Cordon e dreno de pods com segurança e implante os nós atualizados.

Cada um dos nós da noite, do Linux no AKS Obtém os patches de segurança disponíveis por meio do canal de atualização do distribuição. Esse comportamento é configurado automaticamente, já que os nós são implantados em um cluster do AKS. Para minimizar a interrupção e o possível impacto da execução de cargas de trabalho, os nós não serão reinicializados automaticamente se um patch de segurança ou atualização de kernel precisar deles.

O projeto de software livre [kured (daemon de reinicialização do Kubernetes)][kured], da Weaveworks, procura reinicializações de nó pendentes. Quando um nó do Linux aplica atualizações que exigem uma reinicialização, o nó é isolados e esgotado com segurança para mover e agendar os pods em outros nós no cluster. Depois que o nó é reinicializado, ele é adicionado novamente no cluster e o Kubernetes retoma o agendamento de pods nesse nó. Para minimizar as interrupções, apenas um nó por vez tem permissão para ser reinicializado pelo `kured`.

![O processo de reinicialização do nó do AKS usando kured](media/operator-best-practices-cluster-security/node-reboot-process.png)

Se você quer um melhor controle do intervalo de agregação quando as reinicializações ocorrem, `kured` pode se integrar com o Prometheus para evitar reinicializações, se há outros eventos de manutenção ou problemas de cluster em andamento. Essa integração minimiza complicações adicionais, reinicializando os nós enquanto você está solucionando outros problemas ativamente.

Para obter mais informações sobre como lidar com reinicializações de nó, confira [Aplicar atualizações de segurança e o kernel para nós do AKS][aks-kured].

## <a name="next-steps"></a>Próximas etapas

Este artigo se concentrou em como proteger seu cluster do AKS. Para implementar algumas dessas áreas, confira os seguintes artigos:

* [Integrar o Azure Active Directory ao AKS][aks-aad]
* [Atualizar um cluster do AKS para a versão mais recente do Kubernetes][aks-upgrade]
* [Processar atualizações de segurança e reinicializações de nó com o kured][aks-kured]

<!-- EXTERNAL LINKS -->
[kured]: https://github.com/weaveworks/kured
[k8s-apparmor]: https://kubernetes.io/docs/tutorials/clusters/apparmor/
[seccomp]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#seccomp
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[aks-supported-versions]: supported-kubernetes-versions.md
[aks-upgrade]: upgrade-cluster.md
[aks-best-practices-identity]: concepts-identity.md
[aks-kured]: node-updates-kured.md
[aks-aad]: ./azure-ad-integration-cli.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[pod-security-contexts]: developer-best-practices-pod-security.md#secure-pod-access-to-resources
[aks-ssh]: ssh.md
[security-center-aks]: ../security-center/defender-for-kubernetes-introduction.md

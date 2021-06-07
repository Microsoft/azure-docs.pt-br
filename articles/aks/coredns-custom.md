---
title: Personalizar o CoreDNS para o AKS (serviço kubernetes do Azure)
description: Saiba como personalizar o CoreDNS para adicionar subdomínios ou estender pontos de extremidade DNS personalizados usando o AKS (serviço kubernetes do Azure)
services: container-service
author: palma21
ms.topic: article
ms.date: 03/15/2019
ms.author: jpalma
ms.openlocfilehash: 5b13931bc6a13d988c21f728b996c51270769e0c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97368674"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>Personalizar o CoreDNS com o Serviço de Kubernetes do Azure

O AKS (serviço kubernetes do Azure) usa o projeto [CoreDNS][coredns] para a resolução e o gerenciamento de DNS do cluster com todos os clusters *1.12. x* e superior. Anteriormente, o projeto Kube-DNS foi usado. Este projeto Kube-DNS agora está preterido. Para obter mais informações sobre personalização e kubernetes do CoreDNS, consulte a [documentação do upstream oficial][corednsk8s].

Como o AKS é um serviço gerenciado, você não pode modificar a configuração principal para CoreDNS (um *corefile*). Em vez disso, você usa um kubernetes *ConfigMap* para substituir as configurações padrão. Para ver o padrão AKS CoreDNS ConfigMaps, use o `kubectl get configmaps --namespace=kube-system coredns -o yaml` comando.

Este artigo mostra como usar o ConfigMaps para as opções básicas de personalização do CoreDNS no AKS. Essa abordagem difere da configuração do CoreDNS em outros contextos, como o uso do corefile. Verifique a versão do CoreDNS que você está executando, pois os valores de configuração podem ser alterados entre as versões.

> [!NOTE]
> `kube-dns` ofereciam diferentes [Opções de personalização][kubednsblog] por meio de um mapa de configuração do kubernetes. CoreDNS **não** é compatível com versões anteriores com Kube-DNS. Todas as personalizações que você usou anteriormente devem ser atualizadas para uso com CoreDNS.

## <a name="before-you-begin"></a>Antes de começar

Este artigo considera que já existe um cluster do AKS. Se precisar de um cluster do AKS, veja o guia de início rápido do AKS [usando a CLI do Azure][aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].

Ao criar uma configuração como os exemplos abaixo, seus nomes na seção de *dados* devem terminar em *. Server* ou *. Override*. Essa convenção de nomenclatura é definida no padrão AKS CoreDNS ConfigMap que você pode exibir usando o `kubectl get configmaps --namespace=kube-system coredns -o yaml` comando.

## <a name="what-is-supportedunsupported"></a>O que tem suporte/sem suporte

Todos os plug-ins CoreDNS internos têm suporte. Nenhum complemento/plug-in de terceiros tem suporte.

## <a name="rewrite-dns"></a>Reescrever o DNS

Um cenário que você tem é executar regravações de nome DNS imediata. No exemplo a seguir, substitua `<domain to be written>` pelo seu próprio nome de domínio totalmente qualificado. Crie um arquivo chamado `corednsms.yaml` e cole a seguinte configuração de exemplo:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        errors
        cache 30
        rewrite name substring <domain to be rewritten>.com default.svc.cluster.local
        kubernetes cluster.local in-addr.arpa ip6.arpa {
          pods insecure
          upstream
          fallthrough in-addr.arpa ip6.arpa
        }
        forward .  /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10, but that server must be able to resolve the rewritten domain name
    }
```

> [!IMPORTANT]
> Se você redirecionar para um servidor DNS, como o IP do serviço CoreDNS, esse servidor DNS deverá ser capaz de resolver o nome de domínio regravado.

Crie o ConfigMap usando o comando [kubectl Apply ConfigMap][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f corednsms.yaml
```

Para verificar se as personalizações foram aplicadas, use [kubectl Get configmaps][kubectl-get] e especifique seu ConfigMap *coredns-Custom* :

```
kubectl get configmaps --namespace=kube-system coredns-custom -o yaml
```

Agora Force CoreDNS a recarregar o ConfigMap. O comando de [Pod kubectl Delete][kubectl delete] não é destrutivo e não causa tempo de inatividade. Os `kube-dns` pods são excluídos e o Agendador kubernetes os recria. Esses novos pods contêm a alteração no valor TTL.

```console
kubectl delete pod --namespace kube-system -l k8s-app=kube-dns
```

> [!Note]
> O comando acima está correto. Enquanto estamos alterando `coredns` , a implantação está sob o nome **DNS Kube** .

## <a name="custom-forward-server"></a>Servidor de encaminhamento personalizado

Se você precisar especificar um servidor de encaminhamento para o tráfego de rede, poderá criar um ConfigMap para personalizar o DNS. No exemplo a seguir, atualize o `forward` nome e o endereço com os valores para seu próprio ambiente. Crie um arquivo chamado `corednsms.yaml` e cole a seguinte configuração de exemplo:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        forward foo.com 1.1.1.1
    }
```

Como nos exemplos anteriores, crie o ConfigMap usando o comando [kubectl Apply ConfigMap][kubectl-apply] e especifique o nome do manifesto do YAML. Em seguida, Force CoreDNS a recarregar o ConfigMap usando o [kubectl Delete Pod][kubectl delete] para o Agendador kubernetes para recriá-los:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>Usar domínios personalizados

Talvez você queira configurar domínios personalizados que só podem ser resolvidos internamente. Por exemplo, talvez você queira resolver o domínio personalizado *puglife. local*, que não é um domínio de nível superior válido. Sem um ConfigMap de domínio personalizado, o cluster AKS não pode resolver o endereço.

No exemplo a seguir, atualize o domínio personalizado e o endereço IP para direcionar o tráfego para com os valores para seu próprio ambiente. Crie um arquivo chamado `corednsms.yaml` e cole a seguinte configuração de exemplo:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  puglife.server: | # you may select any name here, but it must end with the .server file extension
    puglife.local:53 {
        errors
        cache 30
        forward . 192.11.0.1  # this is my test/dev DNS server
    }
```

Como nos exemplos anteriores, crie o ConfigMap usando o comando [kubectl Apply ConfigMap][kubectl-apply] e especifique o nome do manifesto do YAML. Em seguida, Force CoreDNS a recarregar o ConfigMap usando o [kubectl Delete Pod][kubectl delete] para o Agendador kubernetes para recriá-los:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="stub-domains"></a>Domínios de stub

CoreDNS também pode ser usado para configurar domínios stub. No exemplo a seguir, atualize os domínios personalizados e os endereços IP com os valores para seu próprio ambiente. Crie um arquivo chamado `corednsms.yaml` e cole a seguinte configuração de exemplo:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    abc.com:53 {
        errors
        cache 30
        forward . 1.2.3.4
    }
    my.cluster.local:53 {
        errors
        cache 30
        forward . 2.3.4.5
    }

```

Como nos exemplos anteriores, crie o ConfigMap usando o comando [kubectl Apply ConfigMap][kubectl-apply] e especifique o nome do manifesto do YAML. Em seguida, Force CoreDNS a recarregar o ConfigMap usando o [kubectl Delete Pod][kubectl delete] para o Agendador kubernetes para recriá-los:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="hosts-plugin"></a>Hospeda plug-in

Como todos os plug-ins internos têm suporte, isso significa que o plug-in CoreDNS [hosts][coredns hosts] também está disponível para personalização:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom # this is the name of the configmap you can overwrite with your changes
  namespace: kube-system
data:
    test.override: | # you may select any name here, but it must end with the .override file extension
          hosts example.hosts example.org { # example.hosts must be a file
              10.0.0.1 example.org
              fallthrough
          }
```

## <a name="enable-logging-for-dns-query-debugging"></a>Habilitar o log para depuração de consulta DNS 

Para habilitar o log de consultas DNS, aplique a seguinte configuração em seu ConfigMap coredns-personalizado:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  log.override: | # you may select any name here, but it must end with the .override file extension
        log
```

## <a name="next-steps"></a>Próximas etapas

Este artigo mostrou alguns cenários de exemplo para a personalização do CoreDNS. Para obter informações sobre o projeto CoreDNS, consulte [a página projeto upstream do CoreDNS][coredns].

Para saber mais sobre os principais conceitos de rede, consulte [conceitos de rede para aplicativos no AKs][concepts-network].

<!-- LINKS - external -->
[kubednsblog]: https://www.danielstechblog.io/using-custom-dns-server-for-domain-specific-name-resolution-with-azure-kubernetes-service/
[coredns]: https://coredns.io/
[corednsk8s]: https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/#coredns
[dnscache]: https://coredns.io/plugins/cache/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[coredns hosts]: https://coredns.io/plugins/hosts/

<!-- LINKS - internal -->
[concepts-network]: concepts-network.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md

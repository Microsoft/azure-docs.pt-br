---
title: Complemento de roteamento de aplicativos HTTP no AKS (Serviço de Kubernetes do Azure)
description: Use o complemento de roteamento de aplicativo HTTP para acessar aplicativos implantados no AKS (serviço kubernetes do Azure).
services: container-service
author: lachie83
ms.topic: article
ms.date: 07/20/2020
ms.author: laevenso
ms.openlocfilehash: 25fc021a48e8936f242df35f7485fc59a93bba13
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102172793"
---
# <a name="http-application-routing"></a>Roteamento de aplicativo HTTP

A solução de roteamento de aplicativos HTTP facilita o acesso a aplicativos implantados no cluster do AKS (Serviço de Kubernetes do Azure). Quando a solução estiver habilitada, ela configurará um [controlador de entrada](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/) em seu cluster AKs. À medida que os aplicativos são implantados, a solução também cria nomes DNS publicamente acessíveis para os terminais de aplicativos.

Quando o complemento é habilitado, ele cria uma zona DNS na assinatura. Para obter mais informações sobre o custo DNS, consulte [preços do DNS][dns-pricing].

> [!CAUTION]
> O complemento de roteamento de aplicativos HTTP foi projetado para permitir que você crie rapidamente um controlador de entrada e acesse seus aplicativos. Este complemento não está atualmente projetado para uso em um ambiente de produção e não é recomendado para uso em produção. Para implementações de ingresso prontos para produção que incluam várias réplicas e suporte a TLS, consulte [Criar um controlador de ingresso HTTPS](./ingress-tls.md).

## <a name="http-routing-solution-overview"></a>Visão geral da solução roteamento HTTP

O complemento implanta dois componentes: um controlador de [entrada kubernetes][ingress] e um controlador [DNS externo][external-dns] .

- **Controlador de entrada**: o controlador de Entrada é exposto à internet usando um serviço de Kubernetes do tipo LoadBalancer. O controlador de entrada observa e implementa [recursos de entrada do kubernetes][ingress-resource], que cria rotas para pontos de extremidade do aplicativo.
- **Controlador de DNS externo**: inspeciona os recursos de Entrada do Kubernetes e cria registros DNS A na zona DNS específica do cluster.

## <a name="deploy-http-routing-cli"></a>Implantar o roteamento HTTP: CLI

O complemento de roteamento de aplicativos HTTP pode ser habilitado com a CLI do Azure ao implantar um cluster do AKS. Para fazer isso, use o comando [az aks create][az-aks-create] com o argumento `--enable-addons`.

```azurecli
az aks create --resource-group myResourceGroup --name myAKSCluster --enable-addons http_application_routing
```

> [!TIP]
> Se você quiser ativar vários complementos, forneça-os em forma de uma lista separada por vírgulas. Por exemplo, para habilitar o roteamento e monitoramento de aplicativos HTTP, use o formato `--enable-addons http_application_routing,monitoring`.

Você também pode ativar o roteamento HTTP em um cluster AKS existente usando o comando [az aks enable-addons][az-aks-enable-addons]. Para habilitar o roteamento de HTTP em um cluster existente, adicione a `--addons` parâmetro e especifique *http_application_routing* conforme mostrado no exemplo a seguir:

```azurecli
az aks enable-addons --resource-group myResourceGroup --name myAKSCluster --addons http_application_routing
```

Depois que o cluster for implementado ou atualizado, use o comando [az aks show][az-aks-show] para recuperar o nome da zona DNS.

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName -o table
```

Esse nome é necessário para implantar aplicativos no cluster AKS e é mostrado na seguinte saída de exemplo:

```console
9f9c1fe7-21a1-416d-99cd-3543bb92e4c3.eastus.aksapp.io
```

## <a name="deploy-http-routing-portal"></a>Implantar roteamento HTTP: portal

O complemento de roteamento de aplicativos HTTP pode ser habilitado por meio do portal do Azure ao implantar um cluster do AKS.

![Habilitar o recurso de roteamento de HTTP](media/http-routing/create.png)

Depois que o cluster for implantado, navegue até o grupo de recursos do AKS criado automaticamente e selecione a zona DNS. Anote o nome da zona DNS. Esse nome é necessário para implantar aplicativos no cluster do AKS.

![Obter o nome da zona DNS](media/http-routing/dns.png)

## <a name="connect-to-your-aks-cluster"></a>Conectar-se ao cluster AKS

Para se conectar ao cluster Kubernetes no computador local, use o [kubectl][kubectl], o cliente de linha de comando do Kubernetes.

Se você usa o Azure Cloud Shell, o `kubectl` já estará instalado. Se você quiser instalá-lo localmente, use o comando [az aks install-cli][]:

```azurecli
az aks install-cli
```

Para configurar o `kubectl` para se conectar ao cluster do Kubernetes, use o comando [az aks get-credentials][]. O exemplo a seguir obtém as credenciais para o cluster AKS chamado *MyAKSCluster* no grupo *MyResource*:

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKSCluster
```

## <a name="use-http-routing"></a>Usar o roteamento de HTTP

A solução de roteamento de aplicativos HTTP somente pode ser disparada nos recursos de Entrada que são anotados da seguinte forma:

```yaml
annotations:
  kubernetes.io/ingress.class: addon-http-application-routing
```

Crie um arquivo nomeado **samples-http-application-routing.yaml** e copie no YAML a seguir. Na linha 43, atualize `<CLUSTER_SPECIFIC_DNS_ZONE>` com o nome da zona DNS coletada na etapa anterior deste artigo.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld  
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld
  template:
    metadata:
      labels:
        app: aks-helloworld
    spec:
      containers:
      - name: aks-helloworld
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "Welcome to Azure Kubernetes Service (AKS)"
---
apiVersion: v1
kind: Service
metadata:
  name: aks-helloworld  
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld
---
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: aks-helloworld
  annotations:
    kubernetes.io/ingress.class: addon-http-application-routing
spec:
  rules:
  - host: aks-helloworld.<CLUSTER_SPECIFIC_DNS_ZONE>
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /
```

Use o comando [kubectl apply][kubectl-apply] para criar o segredo.

```bash
kubectl apply -f samples-http-application-routing.yaml
```

O exemplo a seguir mostra os recursos criados:

```bash
$ kubectl apply -f samples-http-application-routing.yaml

deployment.apps/aks-helloworld created
service/aks-helloworld created
ingress.networking.k8s.io/aks-helloworld created
```

Abra um navegador da Web para *AKs-HelloWorld \<CLUSTER_SPECIFIC_DNS_ZONE\> .*, por exemplo, *AKs-HelloWorld.9f9c1fe7-21a1-416d-99cd-3543bb92e4c3.eastus.aksapp.Io* e verifique se você vê o aplicativo de demonstração. O aplicativo pode levar alguns minutos para aparecer.

## <a name="remove-http-routing"></a>Remover o roteamento de HTTP

A solução de roteamento HTTP pode ser removida usando a CLI do Azure. Para fazer isso, execute o seguinte comando, substituindo seu cluster do AKS e o nome do grupo de recursos.

```azurecli
az aks disable-addons --addons http_application_routing --name myAKSCluster --resource-group myResourceGroup --no-wait
```

Quando o complemento de roteamento de aplicativo HTTP está desativado, alguns recursos do Kubernetes podem permanecer no cluster. Esses recursos incluem *configMaps* e *segredos* e são criados no namespace *kube-system*. Para manter um cluster limpo, você talvez queira remover esses recursos.

Procure recursos *addon-http-application-routing* usando os seguintes comandos [kubectl get][kubectl-get]:

```console
kubectl get deployments --namespace kube-system
kubectl get services --namespace kube-system
kubectl get configmaps --namespace kube-system
kubectl get secrets --namespace kube-system
```

A saída de exemplo a seguir mostra os configMaps que devem ser excluídos:

```
$ kubectl get configmaps --namespace kube-system

NAMESPACE     NAME                                                       DATA   AGE
kube-system   addon-http-application-routing-nginx-configuration         0      9m7s
kube-system   addon-http-application-routing-tcp-services                0      9m7s
kube-system   addon-http-application-routing-udp-services                0      9m7s
```

Para excluir recursos, use o comando [kubectl delete][kubectl-delete]. Especifique o tipo de recurso, o nome do recurso e o namespace. O exemplo a seguir exclui um dos configmaps anteriores:

```console
kubectl delete configmaps addon-http-application-routing-nginx-configuration --namespace kube-system
```

Repita a etapa `kubectl delete` anterior para todos os recursos *addon-http-application-routing* que permaneceram no cluster.

## <a name="troubleshoot"></a>Solucionar problemas

Use o comando [kubectl logs][kubectl-logs] para exibir os logs de aplicativo para o aplicativo de DNS externo. Os logs devem confirmar que um registro DNS A e TXT foi criado com êxito.

```
$ kubectl logs -f deploy/addon-http-application-routing-external-dns -n kube-system

time="2018-04-26T20:36:19Z" level=info msg="Updating A record named 'aks-helloworld' to '52.242.28.189' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
time="2018-04-26T20:36:21Z" level=info msg="Updating TXT record named 'aks-helloworld' to '"heritage=external-dns,external-dns/owner=default"' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
```

Esses registros também podem ser vistos no recurso de zona de DNS no portal do Azure.

![Obter os registros DNS](media/http-routing/clippy.png)

Use o comando [kubectl logs][kubectl-logs] para exibir os logs de aplicativo para o controlador de entrada do nginx. Os logs devem confirmar o `CREATE` de um recurso de Entrada e o recarregamento do controlador. Toda a atividade de HTTP é registrada.

```bash
$ kubectl logs -f deploy/addon-http-application-routing-nginx-ingress-controller -n kube-system

-------------------------------------------------------------------------------
NGINX Ingress controller
  Release:    0.13.0
  Build:      git-4bc943a
  Repository: https://github.com/kubernetes/ingress-nginx
-------------------------------------------------------------------------------

I0426 20:30:12.212936       9 flags.go:162] Watching for ingress class: addon-http-application-routing
W0426 20:30:12.213041       9 flags.go:165] only Ingress with class "addon-http-application-routing" will be processed by this ingress controller
W0426 20:30:12.213505       9 client_config.go:533] Neither --kubeconfig nor --master was specified.  Using the inClusterConfig.  This might not work.
I0426 20:30:12.213752       9 main.go:181] Creating API client for https://10.0.0.1:443
I0426 20:30:12.287928       9 main.go:225] Running in Kubernetes Cluster version v1.8 (v1.8.11) - git (clean) commit 1df6a8381669a6c753f79cb31ca2e3d57ee7c8a3 - platform linux/amd64
I0426 20:30:12.290988       9 main.go:84] validated kube-system/addon-http-application-routing-default-http-backend as the default backend
I0426 20:30:12.294314       9 main.go:105] service kube-system/addon-http-application-routing-nginx-ingress validated as source of Ingress status
I0426 20:30:12.426443       9 stat_collector.go:77] starting new nginx stats collector for Ingress controller running in namespace  (class addon-http-application-routing)
I0426 20:30:12.426509       9 stat_collector.go:78] collector extracting information from port 18080
I0426 20:30:12.448779       9 nginx.go:281] starting Ingress controller
I0426 20:30:12.463585       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-nginx-configuration", UID:"2588536c-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"559", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-nginx-configuration
I0426 20:30:12.466945       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-tcp-services", UID:"258ca065-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"561", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-tcp-services
I0426 20:30:12.467053       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-udp-services", UID:"259023bc-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"562", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-udp-services
I0426 20:30:13.649195       9 nginx.go:302] starting NGINX process...
I0426 20:30:13.649347       9 leaderelection.go:175] attempting to acquire leader lease  kube-system/ingress-controller-leader-addon-http-application-routing...
I0426 20:30:13.649776       9 controller.go:170] backend reload required
I0426 20:30:13.649800       9 stat_collector.go:34] changing prometheus collector from  to default
I0426 20:30:13.662191       9 leaderelection.go:184] successfully acquired lease kube-system/ingress-controller-leader-addon-http-application-routing
I0426 20:30:13.662292       9 status.go:196] new leader elected: addon-http-application-routing-nginx-ingress-controller-5cxntd6
I0426 20:30:13.763362       9 controller.go:179] ingress backend successfully reloaded...
I0426 21:51:55.249327       9 event.go:218] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"default", Name:"aks-helloworld", UID:"092c9599-499c-11e8-a5e1-0a58ac1f0ef2", APIVersion:"extensions", ResourceVersion:"7346", FieldPath:""}): type: 'Normal' reason: 'CREATE' Ingress default/aks-helloworld
W0426 21:51:57.908771       9 controller.go:775] service default/aks-helloworld does not have any active endpoints
I0426 21:51:57.908951       9 controller.go:170] backend reload required
I0426 21:51:58.042932       9 controller.go:179] ingress backend successfully reloaded...
167.220.24.46 - [167.220.24.46] - - [26/Apr/2018:21:53:20 +0000] "GET / HTTP/1.1" 200 234 "" "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0)" 197 0.001 [default-aks-helloworld-80] 10.244.0.13:8080 234 0.004 200
```

## <a name="clean-up"></a>Limpeza

Remova os objetos kubernetes associados criados neste artigo usando `kubectl delete` .

```bash
kubectl delete -f samples-http-application-routing.yaml
```

A saída de exemplo mostra que os objetos kubernetes foram removidos.

```bash
$ kubectl delete -f samples-http-application-routing.yaml

deployment "aks-helloworld" deleted
service "aks-helloworld" deleted
ingress "aks-helloworld" deleted
```

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre como instalar um controlador de Entrada protegido por HTTPS no AKS, consulte [Entrada HTTPS no AKS (Serviço de Kubernetes do Azure)][ingress-https].

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-show]: /cli/azure/aks#az-aks-show
[ingress-https]: ./ingress-tls.md
[az-aks-enable-addons]: /cli/azure/aks#az-aks-enable-addons
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials

<!-- LINKS - external -->
[dns-pricing]: https://azure.microsoft.com/pricing/details/dns/
[external-dns]: https://github.com/kubernetes-incubator/external-dns
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[ingress]: https://kubernetes.io/docs/concepts/services-networking/ingress/
[ingress-resource]: https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource

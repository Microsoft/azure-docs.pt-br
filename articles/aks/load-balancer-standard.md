---
title: Visualização – usar um balanceador de carga de SKU padrão no serviço de Kubernetes do Azure (AKS)
description: Saiba como usar um balanceador de carga com uma SKU padrão para expor os serviços com o serviço de Kubernetes do Azure (AKS).
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 06/25/2019
ms.author: zarhoads
ms.openlocfilehash: a9cf3db3a15fab5a2f067a146950e02923a20379
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476800"
---
# <a name="preview---use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Visualização – usar um balanceador de carga de SKU padrão no serviço de Kubernetes do Azure (AKS)

Para fornecer acesso aos seus aplicativos no serviço de Kubernetes do Azure (AKS), você pode criar e usar um balanceador de carga do Azure. Um balanceador de carga em execução no AKS pode ser usado como interna ou um balanceador de carga externo. Um balanceador de carga interno torna um serviço do Kubernetes acessível somente aos aplicativos em execução na mesma rede virtual que o cluster do AKS. Um balanceador de carga externo recebe uma ou mais IPs públicos para a entrada e faz com que um serviço do Kubernetes acessível externamente usando os IPs públicos.

O Balanceador de carga do Azure está disponível em dois SKUs - *Básico* e *Padrão*. Por padrão, o *básica* SKU é usado quando um manifesto do serviço é usado para criar um balanceador de carga no AKS. Usando um *Standard* balanceador de carga SKU fornece recursos adicionais e funcionalidades, como o maior tamanho de pool de back-end e as zonas de disponibilidade. É importante entender as diferenças entre *Standard* e *básica* balanceadores de carga antes de escolher qual deles usar. Depois de criar um cluster do AKS, você não pode alterar o SKU do balanceador de carga para esse cluster. Para obter mais informações sobre o *básicas* e *padrão* SKUs, consulte [comparação SKU do balanceador de carga do Azure][azure-lb-comparison].

Este artigo mostra como criar e usar um balanceador de carga do Azure com o *Standard* SKU com o serviço de Kubernetes do Azure (AKS).

Este artigo pressupõe uma compreensão básica dos conceitos de Kubernetes e o balanceador de carga do Azure. Para obter mais informações, consulte [Kubernetes para o serviço de Kubernetes do Azure (AKS) de conceitos principais][kubernetes-concepts] and [What is Azure Load Balancer?][azure-lb].

Esse recurso está atualmente na visualização.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este artigo exigirá que você está executando a CLI do Azure versão 2.0.59 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][install-azure-cli].

## <a name="before-you-begin"></a>Antes de começar

O serviço de cluster do AKS principal precisa de permissão para gerenciar recursos de rede, se você usar uma sub-rede existente ou um grupo de recursos. Em geral, atribua o *Colaborador de rede* função à entidade de serviço nos recursos de delegado. Para obter mais informações sobre permissões, consulte [AKS Delegar acesso a outros recursos do Azure][aks-sp].

Você deve criar um cluster do AKS que define a SKU para o balanceador de carga *Standard* em vez do padrão *básica*. Criar um cluster AKS é abordado em uma etapa posterior, mas primeiro você precisa habilitar alguns recursos de visualização.

> [!IMPORTANT]
> Recursos de visualização do AKS são Self-service, inscreva-se no. Eles são fornecidos para reunir opiniões e bugs de nossa comunidade. Na visualização, esses recursos não são destinados ao uso em produção. Recursos em visualização pública se encaixam em suporte "melhor esforço". Assistência de AKS equipes de suporte técnico está disponível durante o horário comercial do Pacífico (PST) apenas timezone. Para obter mais informações, consulte as seguintes artigos de suporte:
>
> * [Políticas de suporte do AKS][aks-support-policies]
> * [Perguntas frequentes sobre o suporte do Azure.][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>Instalar a extensão da CLI aks-preview

Usar o balanceador de carga do Azure SKU standard, você precisa de *versão prévia do aks* CLI versão da extensão 0.4.1 ou superior. Instalar o *versão prévia do aks* extensão de CLI do Azure usando o [Adicionar extensão az][az-extension-add] command, then check for any available updates using the [az extension update][az-extension-update] comando::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-aksazurestandardloadbalancer-preview-feature"></a>Registrar o recurso de visualização AKSAzureStandardLoadBalancer

Para criar um cluster do AKS que pode usar um balanceador de carga com o *Standard* SKU, você deve habilitar o *AKSAzureStandardLoadBalancer* sinalizador em sua assinatura de recursos. O *AKSAzureStandardLoadBalancer* funcionalidade também usa *VMSSPreview* ao criar um cluster usando conjuntos de dimensionamento de máquina virtual. Esse recurso fornece o último conjunto de aprimoramentos de serviço ao configurar um cluster. Embora não seja necessário, é recomendável habilitar o *VMSSPreview* também o sinalizador de recursos.

> [!CAUTION]
> Quando você registra um recurso em uma assinatura, você não pode atualmente cancelar o registro desse recurso. Depois de habilitar alguns recursos de visualização, os padrões podem ser usados para todos os clusters AKS, em seguida, é criados na assinatura. Não habilite os recursos de visualização em assinaturas de produção. Use uma assinatura separada para testar recursos de visualização e Reúna comentários.

Registre-se a *VMSSPreview* e *AKSAzureStandardLoadBalancer* sinalizadores de recursos usando o [registro de recurso az][az-feature-register] comando conforme mostrado no exemplo a seguir:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "VMSSPreview"
az feature register --namespace "Microsoft.ContainerService" --name "AKSAzureStandardLoadBalancer"
```

> [!NOTE]
> Qualquer cluster do AKS criar depois que você registrou com êxito a *VMSSPreview* ou *AKSAzureStandardLoadBalancer* sinalizadores de recursos usam essa experiência de cluster de visualização. Para continuar a criar clusters regulares, com suporte completo, não habilite os recursos de visualização em assinaturas de produção. Use um teste separado ou desenvolvimento de assinatura do Azure para testar recursos de visualização.

Demora alguns minutos para o status exibir *Registrado*. Você pode verificar o status de registro usando o [lista de recursos az][az-feature-list] comando:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSAzureStandardLoadBalancer')].{Name:name,State:properties.state}"
```

Quando estiver pronto, atualize o registro do *containerservice* provedor de recursos usando o [registro de provedor az][az-provider-register] comando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>Limitações

As seguintes limitações se aplicam quando você cria e gerenciar clusters AKS que dão suporte a um balanceador de carga com o *Standard* SKU:

* Ao usar o *Standard* SKU para um balanceador de carga, você deverá permitir endereços públicos e evitar a criação de qualquer política do Azure que proibe a criação de IP. O cluster do AKS cria automaticamente um *Standard* IP público do SKU no mesmo grupo de recursos criado para o cluster do AKS, que geralmente são nomeados com *MC_* no início. AKS atribui o IP público para o *Standard* balanceador de carga de SKU. O IP público é obrigatório para permitir o tráfego de saída do cluster AKS. Esse IP público também é necessário para manter a conectividade entre os plano e o agente de nós de controle, bem como para manter a compatibilidade com versões anteriores do AKS.
* Ao usar o *Standard* SKU para um balanceador de carga, você deve usar o Kubernetes versão 1.13.5 ou superior.

Embora esse recurso está em visualização, as seguintes limitações adicionais se aplicam:

* Ao usar o *Standard* SKU para um balanceador de carga no AKS, você não pode definir seu próprio endereço IP público para a saída para o balanceador de carga. Você deve usar o endereço IP que AKS atribui ao balanceador de carga.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um grupo lógico no qual os recursos do Azure são implantados e gerenciados. Ao criar um grupo de recursos, você é solicitado a especificar um local. Essa é a localização na qual os metadados do grupo de recursos são armazenados e na qual os recursos são executados no Azure, caso você não especifique outra região durante a criação de recursos. Crie um grupo de recursos usando o comando [az group create][az-group-create].

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

A seguinte saída de exemplo mostra o grupo de recursos criado com êxito:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

## <a name="create-aks-cluster"></a>Criar cluster AKS
Para executar um cluster do AKS que dá suporte a um balanceador de carga com o *Standard* SKU, o seu cluster precisa definir o *sku do load balancer* parâmetro *padrão*. Esse parâmetro cria um balanceador de carga com o *Standard* SKU quando o cluster é criado. Quando você executa um *LoadBalancer* seu cluster, a configuração do serviço a *padrão* balanceador de carga SK é atualizada com a configuração do serviço. Use o [criar az aks][az-aks-create] comando para criar um cluster do AKS denominado *myAKSCluster*.

> [!NOTE]
> O *sku do load balancer* propriedade só pode ser usada quando o cluster é criado. Você não pode alterar o SKU do balanceador de carga após ter sido criado um cluster do AKS. Além disso, você pode usar apenas um tipo de SKU do balanceador de carga em um único cluster.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-vmss \
    --node-count 1 \
    --kubernetes-version 1.14.0 \
    --load-balancer-sku standard \
    --generate-ssh-keys
```

Após alguns minutos, o comando será concluído e retornará informações no formato JSON sobre o cluster.

## <a name="connect-to-the-cluster"></a>Conectar-se ao cluster

Para gerenciar um cluster Kubernetes, você deve usar [kubectl][kubectl], o cliente de linha de comando do Kubernetes. Se você usar o Azure Cloud Shell, o `kubectl` já estará instalado. Para instalar `kubectl` localmente, use o [az aks install-cli][az-aks-install-cli] comando:

```azurecli
az aks install-cli
```

Para configurar o `kubectl` para se conectar ao cluster do Kubernetes, use o comando [az aks get-credentials][az-aks-get-credentials]. Este comando baixa as credenciais e configura a CLI do Kubernetes para usá-las.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Para verificar a conexão ao seu cluster, use o comando [kubectl get][kubectl-get] para retornar uma lista de nós do cluster.

```azurecli-interactive
kubectl get nodes
```

A saída de exemplo a seguir mostra o único nó criado nas etapas anteriores. Verifique se o status do nó é *Pronto*:

```
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.14.0
```

## <a name="verify-your-cluster-uses-the-standard-sku"></a>Verifique se o cluster usa o *Standard* SKU

Use o [show do az aks][az-aks-show] para exibir a configuração do cluster.

```console
$ az aks show --resource-group myResourceGroup --name myAKSCluster

{
  "aadProfile": null,
  "addonProfiles": null,
   ...
   "networkProfile": {
    "dnsServiceIp": "10.0.0.10",
    "dockerBridgeCidr": "172.17.0.1/16",
    "loadBalancerSku": "standard",
    ...
```

Verifique se o *loadBalancerSku* propriedade mostra como *padrão*.

## <a name="use-the-load-balancer"></a>Usar o balanceador de carga

Para usar o balanceador de carga no seu cluster, crie um manifesto do serviço com o tipo de serviço *LoadBalancer*. Para mostrar o balanceador de carga de trabalho, crie outro manifesto com um aplicativo de exemplo para executar em seu cluster. Este aplicativo de exemplo é exposto por meio do balanceador de carga e pode ser exibido por meio de um navegador.

Criar um manifesto chamado `sample.yaml` conforme mostrado no exemplo a seguir:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
```

O manifesto acima configura duas implantações: *azure-vote-front* e *azure-vote-back*. Para configurar *azure-vote-front* implantação seja exposto usando o balanceador de carga, crie um manifesto chamado `standard-lb.yaml` conforme mostrado no exemplo a seguir:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

O serviço *azure-vote-front* usa o *LoadBalancer* tipo para configurar o balanceador de carga no cluster do AKS para se conectar ao *azure-vote-front* implantação.

Implantar o aplicativo de exemplo e balanceador de carga usando o [kubectl aplicar][kubectl-apply] e especifique o nome de seus manifestos YAML:

```console
kubectl apply -f sample.yaml
kubectl apply -f standard-lb.yaml
```

O *Standard* balanceador de carga SKU agora está configurado para expor o aplicativo de exemplo. Exibir os detalhes do serviço de *azure-vote-front* usando [kubectl get][kubectl-get] para ver o IP público do balanceador de carga. O endereço IP público do balanceador de carga é mostrado na *EXTERNAL-IP* coluna. Pode levar um minuto ou dois para o endereço IP ser alterado na *\<pendente\>* para um endereço IP externo real, conforme mostrado no exemplo a seguir:

```
$ kubectl get service azure-vote-front

NAME                TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE
azure-vote-front    LoadBalancer   10.0.227.198   52.179.23.131   80:31201/TCP   16s
```

Navegue até o IP público em um navegador e verifique se que você vê o aplicativo de exemplo. No exemplo acima, o IP público é `52.179.23.131`.

![Imagem de navegação para o Voto do Azure](media/container-service-kubernetes-walkthrough/azure-vote.png)

> [!NOTE]
> Você pode também configurar o balanceador de carga para ser interna e não expõem um IP público. Para configurar o balanceador de carga como internos, adicione `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` como uma anotação para a *LoadBalancer* service. Você pode ver um exemplo yaml, bem como obter mais detalhes sobre um balanceador de carga interno do manifesto [aqui][internal-lb-yaml].

## <a name="clean-up-the-standard-sku-load-balancer-configuration"></a>Limpar a configuração de Balanceador de carga de SKU Standard

Para remover a configuração de Balanceador de carga e o aplicativo de exemplo, use [kubectl excluir][kubectl-delete]:

```console
kubectl delete -f sample.yaml
kubectl delete -f standard-lb.yaml
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os serviços do Kubernetes na [documentação dos serviços de Kubernetes][kubernetes-services].

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update

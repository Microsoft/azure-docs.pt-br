---
title: Usar um Load Balancer público
titleSuffix: Azure Kubernetes Service
description: Saiba como usar um balanceador de carga público com um SKU padrão para expor seus serviços com o AKS (serviço kubernetes do Azure).
services: container-service
ms.topic: article
ms.date: 11/14/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 5da7f2a11be7562313b709a8af72ccd709165cfa
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000854"
---
# <a name="use-a-public-standard-load-balancer-in-azure-kubernetes-service-aks"></a>Usar um Standard Load Balancer público no AKS (serviço kubernetes do Azure)

O Azure Load Balancer está na L4 do modelo de interconexão de sistemas abertos (OSI) que dá suporte aos cenários de entrada e saída. Ele distribui os fluxos de entrada que chegam ao front-end do balanceador de carga para as instâncias do pool de back-end.

Um Load Balancer **público** quando integrado ao AKs tem duas finalidades:

1. Para fornecer conexões de saída para os nós de cluster dentro da rede virtual AKS. Ele alcança esse objetivo convertendo o endereço IP privado dos nós em um endereço IP público que faz parte de seu *pool de saída*.
2. Para fornecer acesso a aplicativos por meio de serviços kubernetes do tipo `LoadBalancer` . Com ele, você pode dimensionar facilmente seus aplicativos e criar serviços altamente disponíveis.

Um balanceador de carga **interno (ou privado)** é usado onde somente os IPs privados são permitidos como front-end. Os balanceadores de carga internos são usados para balancear a carga do tráfego dentro de uma rede virtual. Um front-end do balanceador de carga também pode ser acessado de uma rede local em um cenário híbrido.

Este documento aborda a integração com o Load Balancer público. Para integração de Load Balancer interno, consulte a [documentação do balanceador de carga interno do AKS](internal-lb.md).

## <a name="before-you-begin"></a>Antes de começar

O Balanceador de carga do Azure está disponível em dois SKUs - *Básico* e *Padrão*. Por padrão, o SKU *Standard* é usado quando você cria um cluster AKs. Use o SKU *Standard* para ter acesso a funcionalidades adicionadas, como um pool de back-end maior, [**pools de vários nós**](use-multiple-node-pools.md)e [**zonas de disponibilidade**](availability-zones.md). É o SKU de Load Balancer recomendado para AKS.

Para obter mais informações sobre os SKUs *Básico* e *Standard*, confira [Comparação de SKU do Azure Load Balancer][azure-lb-comparison].

Este artigo pressupõe que você tenha um cluster AKS com o SKU *Standard* Azure Load Balancer e percorra como usar e configurar alguns dos recursos e recursos do balanceador de carga. Se precisar de um cluster do AKS, veja o guia de início rápido do AKS [usando a CLI do Azure][aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].

> [!IMPORTANT]
> Se preferir não aproveitar o Azure Load Balancer para fornecer conexão de saída e, em vez disso, tiver seu próprio gateway, firewall ou proxy para essa finalidade, você poderá ignorar a criação do pool de saída do balanceador de carga e o respectivo IP de front-end usando [**o tipo de saída como UserDefinedRouting (UDR)**](egress-outboundtype.md). O tipo de saída define o método de saída para um cluster e o padrão é tipo: balanceador de carga.

## <a name="use-the-public-standard-load-balancer"></a>Usar o balanceador de carga padrão público

Depois de criar um cluster AKS com o tipo de saída: Load Balancer (padrão), o cluster estará pronto para usar o balanceador de carga para expor serviços também.

Para que você possa criar um serviço público do tipo `LoadBalancer` , conforme mostrado no exemplo a seguir. Comece criando um manifesto de serviço chamado `public-svc.yaml` :

```yaml
apiVersion: v1
kind: Service
metadata:
  name: public-svc
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: public-app
```

Implante o manifesto do serviço público usando [kubectl Apply][kubectl-apply] e especifique o nome do seu manifesto YAML:

```azurecli-interactive
kubectl apply -f public-svc.yaml
```

O Azure Load Balancer será configurado com um novo IP público que fará o front-end desse novo serviço. Como o Azure Load Balancer pode ter vários IPs de front-end, cada novo serviço implantado obterá um novo IP de front-end dedicado para ser acessado de forma exclusiva.

Você pode confirmar se o serviço foi criado e se o balanceador de carga está configurado executando, por exemplo:

```azurecli-interactive
kubectl get service public-svc
```

```console
NAMESPACE     NAME          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)         AGE
default       public-svc    LoadBalancer   10.0.39.110    52.156.88.187   80:32068/TCP    52s
```

Quando você exibe os detalhes do serviço, o endereço IP público criado para esse serviço no balanceador de carga é mostrado na coluna *IP externo* . Pode levar um minuto ou dois para que o endereço IP seja alterado de *\<pending\>* para um endereço IP público real, conforme mostrado no exemplo acima.

## <a name="configure-the-public-standard-load-balancer"></a>Configurar o balanceador de carga padrão público

Ao usar o balanceador de carga público do SKU Standard, há um conjunto de opções que podem ser personalizadas no momento da criação ou atualizando o cluster. Essas opções permitem que você personalize o Load Balancer para atender às suas necessidades de cargas de trabalho e deve ser revisado de acordo. Com o balanceador de carga padrão, você pode:

* Definir ou dimensionar o número de IPs de saída gerenciados
* Traga seus próprios [IPS de saída personalizados ou prefixo de IP de saída](#provide-your-own-outbound-public-ips-or-prefixes)
* Personalizar o número de portas de saída alocadas para cada nó do cluster
* Definir a configuração de tempo limite para conexões ociosas

> [!IMPORTANT]
> Somente uma opção de IP de saída (IPs gerenciados, traga seu próprio IP ou prefixo de IP) pode ser usada em um determinado momento.

### <a name="scale-the-number-of-managed-outbound-public-ips"></a>Dimensionar o número de IPs públicos de saída gerenciados

O Azure Load Balancer fornece conectividade de saída de uma rede virtual além da entrada. Regras de saída simplificam a configuração da conversão de endereços de rede de saída do Standard Load Balancer público.

Como todas as regras do Load Balancer, regras de saída seguem a mesma sintaxe familiar que regras NAT de entrada e balanceamento de carga:

***front-end IPS + parâmetros + pool de back-end** _

Uma regra de saída configura o NAT de saída para todas as máquinas virtuais identificadas pelo pool de back-end a serem convertidas no front-end. Os parâmetros fornecem controle refinado adicional sobre o algoritmo NAT de saída.

Embora uma regra de saída possa ser usada com apenas um único endereço IP público, regras de saída aliviam a carga de configuração para o dimensionamento de NAT de saída. Você pode usar vários endereços IP para planejar cenários de grande escala e pode usar regras de saída para atenuar padrões propensos à exaustão de SNAT. Cada endereço IP adicional fornecido por um front-end fornece portas efêmeras de 64K para Load Balancer para usar como portas SNAT. 

Ao usar um balanceador de carga de SKU _Standard * com IPs públicos de saída gerenciados, que são criados por padrão, você pode dimensionar o número de IPs públicos de saída gerenciados usando o **`load-balancer-managed-ip-count`** parâmetro.

Para atualizar um cluster existente, execute o comando a seguir. Esse parâmetro também pode ser definido no momento de criação do cluster para ter vários IPs públicos de saída gerenciados.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

O exemplo acima define o número de IPs públicos de saída gerenciados como *2* para o cluster *myAKSCluster* no *myResourceGroup*. 

Você também pode usar o **`load-balancer-managed-ip-count`** parâmetro para definir o número inicial de IPS públicos de saída gerenciados ao criar o cluster acrescentando o **`--load-balancer-managed-outbound-ip-count`** parâmetro e definindo-o para o valor desejado. O número padrão de IPs públicos de saída gerenciados é 1.

### <a name="provide-your-own-outbound-public-ips-or-prefixes"></a>Forneça seus próprios IPs públicos de saída ou prefixos

Quando você usa um balanceador de carga SKU *padrão* , por padrão, o cluster AKs cria automaticamente um IP público no grupo de recursos de infraestrutura gerenciado por AKs e o atribui ao pool de saída do balanceador de carga.

Um IP público criado pelo AKS é considerado um recurso gerenciado por AKS. Isso significa que o ciclo de vida desse IP público deve ser gerenciado pelo AKS e não requer nenhuma ação do usuário diretamente no recurso de IP público. Como alternativa, você pode atribuir seu próprio IP público personalizado ou prefixo de IP público no momento da criação do cluster. Seus IPs personalizados também podem ser atualizados em Propriedades do balanceador de carga de um cluster existente.

Requisitos para usar seu próprio IP público ou prefixo:

- Os endereços IP públicos personalizados devem ser criados e pertencentes ao usuário. Os endereços IP públicos gerenciados criados por AKS não podem ser reutilizados como um traga seu próprio IP personalizado, pois isso pode causar conflitos de gerenciamento.
- Você deve garantir que a identidade do cluster AKS (entidade de serviço ou identidade gerenciada) tenha permissões para acessar o IP de saída. De acordo com a [lista de permissões de IP público necessária](kubernetes-service-principal.md#networking).
- Verifique se você atende aos [pré-requisitos e às restrições](../virtual-network/public-ip-address-prefix.md#constraints) necessárias para configurar IPS de saída ou prefixos de IP de saída.

#### <a name="update-the-cluster-with-your-own-outbound-public-ip"></a>Atualizar o cluster com seu próprio IP público de saída

Use o comando [az network public-ip show][az-network-public-ip-show] para listar as IDs dos seus IPs públicos.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

O comando acima mostra a ID para o IP público *myPublicIP* no grupo de recursos *myResourceGroup*.

Use o `az aks update` comando com o **`load-balancer-outbound-ips`** parâmetro para atualizar o cluster com seus IPs públicos.

O exemplo a seguir usa o `load-balancer-outbound-ips` parâmetro com as IDs do comando anterior.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

#### <a name="update-the-cluster-with-your-own-outbound-public-ip-prefix"></a>Atualizar o cluster com seu próprio prefixo de IP público de saída

Você também pode usar prefixos de IP público para saída com seu balanceador de carga de SKU *Standard*. O seguinte exemplo usa o comando [az network public-ip prefix show][az-network-public-ip-prefix-show] para listar as IDs dos seus prefixos de IP público:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

O comando acima mostra a ID para o prefixo de IP público *myPublicIPPrefix* no grupo de recursos *myResourceGroup*.

O exemplo a seguir usa o parâmetro *load-balancer-outbound-ip-prefixes* com as IDs do comando anterior.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

#### <a name="create-the-cluster-with-your-own-public-ip-or-prefixes"></a>Criar o cluster com seu próprio IP público ou prefixos

Talvez você queira trazer seus próprios endereços IP ou prefixos IP para saída no momento da criação do cluster para dar suporte a cenários como adicionar pontos de extremidade de saída a uma lista de permissões. Acrescente os mesmos parâmetros mostrados acima à etapa de criação do cluster para definir seus IPs públicos e prefixos de IP no início do ciclo de vida de um cluster.

Use o comando *az aks create* com o parâmetro *load-balancer-outbound-ips* para criar um cluster com seus IPs públicos no início.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Use o comando *az aks create* com o parâmetro *load-balancer-outbound-ip-prefixes* para criar um cluster com seus prefixos de IP públicos no início.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

### <a name="configure-the-allocated-outbound-ports"></a>Configurar as portas de saída alocadas

> [!IMPORTANT]
> Se você tiver aplicativos no cluster que devem estabelecer um grande número de conexões com um pequeno conjunto de destinos, por exemplo, muitas instâncias de front-end conectando-se a um BD SQL, você tem um cenário muito suscetível a encontrar esgotamento de porta SNAT (sem portas para se conectar). Para esses cenários, é altamente recomendável aumentar as portas de saída alocadas e os IPs de front-end de saída no balanceador de carga. O aumento deve considerar que um (1) endereço IP adicional adiciona 64K portas adicionais para distribuir entre todos os nós de cluster.


A menos que especificado de outra forma, o AKS usará o valor padrão das portas de saída alocadas que Standard Load Balancer define ao configurá-lo. Esse valor é **nulo** na API AKs ou **0** na API SLB, conforme mostrado pelo comando abaixo:

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

Os comandos anteriores listarão a regra de saída para o balanceador de carga, por exemplo:

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

Essa saída não significa que você tem 0 porta, mas, em vez disso, está aproveitando a [atribuição de porta de saída automática com base no tamanho do pool de back-end][azure-lb-outbound-preallocatedports], por exemplo, se um cluster tiver 50 ou menos nós, 1024 portas para cada nó forem alocadas, à medida que você aumentar o número de nós por aí, obterá gradualmente menos portas por nó.


Para definir ou aumentar o número de portas de saída alocadas, você pode seguir o exemplo abaixo:


```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 7 \
    --load-balancer-outbound-ports 4000
```

Este exemplo forneceria 4000 portas de saída alocadas para cada nó em meu cluster e com 7 IPs você teria *4000 portas por nó * 100 nós = 400 mil total de portas < = 448K total de portas = 7 IPS * 64K portas por IP*. Isso permitiria que você dimensionasse com segurança os nós 100 e tenha uma operação de atualização padrão. É essencial alocar portas suficientes para nós adicionais necessários para a atualização e outras operações. AKS usa como padrão um nó de buffer para atualização, neste exemplo, isso requer 4000 portas livres em qualquer momento determinado. Se estiver usando [valores de maxSurge](upgrade-cluster.md#customize-node-surge-upgrade), multiplique as portas de saída por nó pelo valor de maxSurge.

Para ir acima de 100 nós, você precisaria adicionar mais IPs.


> [!IMPORTANT]
> Você deve [calcular a cota necessária e verificar os requisitos][requirements] antes de personalizar o *allocatedOutboundPorts* para evitar problemas de conectividade ou de dimensionamento.

Você também pode usar os **`load-balancer-outbound-ports`** parâmetros ao criar um cluster, mas também deve especificar qualquer um **`load-balancer-managed-outbound-ip-count`** deles **`load-balancer-outbound-ips`** **`load-balancer-outbound-ip-prefixes`** .  Por exemplo:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-sku standard \
    --load-balancer-managed-outbound-ip-count 2 \
    --load-balancer-outbound-ports 1024 
```

### <a name="configure-the-load-balancer-idle-timeout"></a>Configurar o tempo limite de ociosidade do balanceador de carga

Quando os recursos da porta SNAT estão esgotados, os fluxos de saída falham até que os fluxos existentes liberem as portas SNAT. Load Balancer recupera portas SNAT quando o fluxo fecha e o balanceador de carga configurado AKS usa um tempo limite de ociosidade de 30 minutos para recuperar portas SNAT de fluxos ociosos.
Você também pode usar o transporte (por exemplo, **`TCP keepalives`** ) ou **`application-layer keepalives`** para atualizar um fluxo ocioso e redefinir esse tempo limite de ociosidade, se necessário. Você pode configurar esse tempo limite seguindo o exemplo abaixo: 


```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-idle-timeout 4
```

Se você espera ter várias conexões de curta duração e nenhuma conexão longa e pode ter longos tempos de ociosidade, como aproveitar `kubectl proxy` ou `kubectl port-forward` considerar o uso de um valor de tempo limite baixo, como 4 minutos. Além disso, ao usar o TCP keepalives, é suficiente habilitá-los em um lado da conexão. Por exemplo, é suficiente habilitá-los no lado do servidor apenas para redefinir o timer de ociosidade do fluxo e não é necessário para ambos os lados iniciarem o TCP keepalives. Conceitos semelhantes existem para a camada de aplicativo, incluindo as configurações de cliente-servidor de banco de dados. Verifique o lado do servidor para saber quais opções existem para keepalives específicas do aplicativo.

> [!IMPORTANT]
> O AKS habilita a redefinição de TCP em ociosidade por padrão e recomenda que você mantenha essa configuração ativada e aproveite-a para um comportamento de aplicativo mais previsível em seus cenários.
> TCP RST só é enviado durante a conexão TCP no estado estabelecido. Leia mais sobre isso [aqui](../load-balancer/load-balancer-tcp-reset.md).

### <a name="requirements-for-customizing-allocated-outbound-ports-and-idle-timeout"></a>Requisitos para personalizar portas de saída alocadas e tempo limite de ociosidade

- O valor especificado para *allocatedOutboundPorts* também deve ser um múltiplo de 8.
- Você deve ter capacidade de IP de saída suficiente com base no número de suas VMs de nó e nas portas de saída alocadas necessárias. Para validar que você tem capacidade de IP de saída suficiente, use a seguinte fórmula: 
 
*outboundIPs* \* 64.000 \> *nodeVMs* \* *desiredAllocatedOutboundPorts*.
 
Por exemplo, se você tiver três *nodeVMs* e 50 mil *desiredAllocatedOutboundPorts*, precisará ter pelo menos três *outboundIPs*. É recomendável incorporar a capacidade de IP de saída adicional além da que você precisa. Além disso, você precisa considerar o dimensionador automático do cluster e a possibilidade de atualizações do pool de nós ao calcular a capacidade do IP de saída. Para o dimensionador automático do cluster, examine a contagem de nós atual e a contagem máxima de nós e use o valor mais alto. Para atualizar, considere uma VM de nó adicional para cada pool de nós que permite a atualização.

- Ao definir *IdleTimeoutInMinutes* como um valor diferente do padrão de 30 minutos, considere por quanto tempo suas cargas de trabalho precisarão de uma conexão de saída. Considere também que o valor do tempo limite padrão para um balanceador de carga de SKU *Standard* usado fora do AKS é de 4 minutos. Um valor *IdleTimeoutInMinutes* que reflete com mais precisão sua carga de trabalho do AKS específica pode ajudar a diminuir o esgotamento de SNAT causado pela associação de conexões que não são mais usadas.

> [!WARNING]
> Alterar os valores de *AllocatedOutboundPorts* e *IdleTimeoutInMinutes* pode alterar significativamente o comportamento da regra de saída para o balanceador de carga e não deve ser feito levemente, sem compreender as compensações e os padrões de conexão do aplicativo, verificar a seção de solução de [problemas de SNAT abaixo][troubleshoot-snat] e examinar as [Load Balancer regras de saída][azure-lb-outbound-rules-overview] e [conexões de saída no Azure][azure-lb-outbound-connections] antes de atualizar esses valores para entender totalmente o impacto de suas alterações

## <a name="restrict-inbound-traffic-to-specific-ip-ranges"></a>Restringir o tráfego de entrada para intervalos de IP específicos

O seguinte manifesto usa *loadBalancerSourceRanges* para especificar um novo intervalo de IP para o tráfego externo de entrada:

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
  loadBalancerSourceRanges:
  - MY_EXTERNAL_IP_RANGE
```

> [!NOTE]
> Entrada, o tráfego externo flui do balanceador de carga para a rede virtual para o cluster AKS. A rede virtual tem um NSG (grupo de segurança de rede) que permite todo o tráfego de entrada do balanceador de carga. Este NSG usa uma [marca de serviço][service-tags] do tipo Balancer para permitir o tráfego do balanceador de carga. *LoadBalancer*

## <a name="maintain-the-clients-ip-on-inbound-connections"></a>Manter o IP do cliente em conexões de entrada

Por padrão, um serviço do tipo `LoadBalancer` [em kubernetes](https://kubernetes.io/docs/tutorials/services/source-ip/#source-ip-for-services-with-type-loadbalancer) e em AKs não manterá o endereço IP do cliente na conexão com o pod. O IP de origem no pacote que é entregue ao Pod será o IP privado do nó. Para manter o endereço IP do cliente, você deve definir `service.spec.externalTrafficPolicy` como `local` na definição de serviço. O manifesto a seguir mostra um exemplo:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  externalTrafficPolicy: Local
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

## <a name="additional-customizations-via-kubernetes-annotations"></a>Personalizações adicionais por meio de anotações do kubernetes

Abaixo está uma lista de anotações com suporte para serviços kubernetes com tipo `LoadBalancer` , estas anotações se aplicam somente a fluxos de **entrada** :

| Annotation | Valor | Descrição
| ----------------------------------------------------------------- | ------------------------------------- | ------------------------------------------------------------ 
| `service.beta.kubernetes.io/azure-load-balancer-internal`         | `true` ou `false`                     | Especifique se o balanceador de carga deve ser interno. O padrão é público se não estiver definido.
| `service.beta.kubernetes.io/azure-load-balancer-internal-subnet`  | Nome da sub-rede                    | Especifique a qual sub-rede o balanceador de carga interno deve ser associado. O padrão é a sub-rede configurada no arquivo de configuração de nuvem se não estiver definida.
| `service.beta.kubernetes.io/azure-dns-label-name`                 | Nome do rótulo DNS em IPs públicos   | Especifique o nome do rótulo DNS para o serviço **público** . Se estiver definido como uma cadeia de caracteres vazia, a entrada DNS no IP público não será usada.
| `service.beta.kubernetes.io/azure-shared-securityrule`            | `true` ou `false`                     | Especifique que o serviço deve ser exposto usando uma regra de segurança do Azure que pode ser compartilhada com outro serviço, que é uma determinada especificação de regras para um aumento no número de serviços que podem ser expostos. Esta anotação depende do recurso de [regras de segurança aumentadas](../virtual-network/network-security-groups-overview.md#augmented-security-rules) do Azure de grupos de segurança de rede. 
| `service.beta.kubernetes.io/azure-load-balancer-resource-group`   | Nome do grupo de recursos            | Especifique o grupo de recursos de IPs públicos do Load Balancer que não estão no mesmo grupo de recursos que a infraestrutura de cluster (grupo de recursos de nó).
| `service.beta.kubernetes.io/azure-allowed-service-tags`           | Lista de marcas de serviço permitidas          | Especifique uma lista de [marcas de serviço][service-tags] permitidas separadas por vírgula.
| `service.beta.kubernetes.io/azure-load-balancer-tcp-idle-timeout` | Tempos limite de ociosidade de TCP em minutos          | Especifique o tempo, em minutos, para que os tempos limite de ociosidade de conexão TCP ocorram no balanceador de carga. O valor padrão e mínimo é 4. O valor máximo é 30. Deve ser um inteiro.
|`service.beta.kubernetes.io/azure-load-balancer-disable-tcp-reset` | `true`                                | Desabilitar `enableTcpReset` para SLB


## <a name="troubleshooting-snat"></a>Solucionando problemas de SNAT

Se você souber que está iniciando muitas conexões TCP ou UDP de saída para a mesma porta e endereço IP de destino e observar conexões de saída com falha ou for avisado pelo suporte de que você está esgotando as portas SNAT (portas efêmeras prealocadas usadas pela PAT), você tem várias opções gerais de mitigação. Avalie essas opções e decida o que está disponível e melhor para o seu cenário. É possível que uma ou mais possam ajudar a gerenciar esse cenário. Para obter informações detalhadas, consulte o [Guia de solução de problemas de conexões de saída](../load-balancer/troubleshoot-outbound-connection.md).

Frequentemente, a causa raiz do esgotamento de SNAT é um antipadrão para a forma como a conectividade de saída é estabelecida, gerenciada ou como os temporizadores configuráveis têm os valores padrão alterados. Revise esta seção com cuidado.

### <a name="steps"></a>Etapas
1. Verifique se suas conexões permanecem ociosas por muito tempo e se baseiam no tempo limite de ociosidade padrão para liberar essa porta. Nesse caso, o tempo limite padrão de 30 min pode precisar ser reduzido para seu cenário.
2. Investigar como o aplicativo está criando a conectividade de saída (por exemplo, revisão de código ou captura de pacote).
3. Determinar se essa atividade é um comportamento esperado ou se o aplicativo está tendo um comportamento inadequado. Use as [métricas](../load-balancer/load-balancer-standard-diagnostics.md) e [os logs](../load-balancer/load-balancer-monitor-log.md) em Azure monitor para obter suas descobertas. Use a categoria "falha" para a métrica de conexões SNAT, por exemplo.
4. Avalie se os [padrões](#design-patterns) apropriados são seguidos.
5. Avalie se o esgotamento de porta SNAT deve ser mitigado com [endereços IP de saída adicionais + portas de saída alocadas adicionais](#configure-the-allocated-outbound-ports) .

### <a name="design-patterns"></a>Padrões de design
Sempre aproveite a reutilização de conexão e o pool de conexões quando possível. Esses padrões evitarão problemas de esgotamento de recursos e resultarão em um comportamento previsível. Os primitivos desses padrões podem ser encontrados em muitas bibliotecas e estruturas de desenvolvimento.

- As solicitações atômicas (uma solicitação por conexão) geralmente não são uma boa opção de design. Esses limites de padrão escala, reduzem o desempenho e diminuem a confiabilidade. Em vez disso, reutilize as conexões HTTP/S para reduzir os números de conexões e as portas SNAT associadas. A escala do aplicativo aumentará e o desempenho melhorará devido a um custo reduzido de Handshakes, sobrecarga e operação de criptografia ao usar o TLS.
- Se você estiver usando fora do cluster/DNS personalizado, ou se os servidores upstream personalizados no coreDNS tiverem em mente que o DNS pode introduzir muitos fluxos individuais no volume quando o cliente não estiver armazenando em cache o resultado do resolvedor de DNS. Certifique-se de personalizar o coreDNS primeiro em vez de usar servidores DNS personalizados e definir um bom valor de cache.
- Os fluxos UDP (por exemplo, pesquisas de DNS) alocam portas SNAT para a duração do tempo limite de ociosidade. Quanto maior o tempo limite de ociosidade, maior a pressão em portas SNAT. Use o tempo limite de ociosidade curto (por exemplo, 4 minutos).
Use pools de conexão para formatar o volume de conexão.
- Nunca abandone silenciosamente um fluxo de TCP nem dependa de temporizadores TCP para limpar o fluxo. Se você não permitir que o TCP feche explicitamente a conexão, o estado permanecerá alocado em sistemas intermediários e pontos de extremidade e tornará as portas SNAT não disponíveis para outras conexões. Esse padrão pode disparar falhas de aplicativo e esgotamento de SNAT.
- Não altere os valores de temporizador relacionados ao fechamento de TCP no nível do sistema operacional sem um conhecimento especializado do impacto. Enquanto a pilha TCP for recuperada, o desempenho do aplicativo poderá ser afetado negativamente quando os pontos de extremidade de uma conexão tiverem expectativas incompatíveis. O desejo de alterar temporizadores é geralmente um sinal de um problema de design subjacente. Examine as seguintes recomendações.


O exemplo acima atualiza a regra para permitir somente o tráfego externo de entrada do intervalo *MY_EXTERNAL_IP_RANGE*. Se você substituir *MY_EXTERNAL_IP_RANGE* pelo endereço IP da sub-rede interna, o tráfego será restrito somente a IPS internos do cluster. Isso não permitirá que clientes fora do seu cluster kubernetes acessem o balanceador de carga.

## <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>Mudando de um balanceador de carga de SKU básico para SKU Standard

Se você tiver um cluster existente com o Load Balancer de SKU Básico, haverá diferenças comportamentais importantes a serem observadas ao migrar para usar um cluster com o Load Balancer de SKU Standard.

Por exemplo, fazer implantações azuis/verdes para migrar clusters é uma prática comum considerando que o tipo `load-balancer-sku` de um cluster só pode ser definido no momento da criação do cluster. No entanto, os balanceadores de carga de *SKU básicos* usam endereços IP de *SKU básicos* , que não são compatíveis com os balanceadores de carga de *SKU padrão* , pois exigem endereços IP de *SKU padrão* . Ao migrar clusters para atualizar SKUs do Load Balancer, será necessário um novo endereço IP com um SKU de Endereço IP compatível.

Para ver mais considerações sobre como migrar clusters, acesse [nossa documentação sobre considerações sobre migração](aks-migration.md) para ver uma lista de tópicos importantes a serem considerados durante a migração. As limitações abaixo também são importantes diferenças comportamentais a serem observadas ao usar Load Balancers de SKU Standard no AKS.

## <a name="limitations"></a>Limitações

As seguintes limitações se aplicam quando você cria e gerencia clusters do AKS que dão suporte a um balanceador de carga com o SKU *Standard*:

* Pelo menos um IP público ou prefixo de IP é necessário para permitir o tráfego de saída do cluster do AKS. O IP público ou o prefixo IP também é necessário para manter a conectividade entre o plano de controle e os nós de agente e para manter a compatibilidade com as versões anteriores do AKS. Você tem as seguintes opções para especificar IPs públicos ou prefixos de IP com um balanceador de carga de SKU *Standard*:
    * Fornecer seus IPs públicos.
    * Forneça seus prefixos de IP público.
    * Especifique um número até 100 para permitir que o cluster do AKS crie esse tanto de IPs públicos de SKU *Standard* no mesmo grupo de recursos criado que o cluster do AKS, que geralmente é chamado de *MC_* no início. O AKS atribui o IP público ao balanceador de carga de SKU *Standard*. Por padrão, um IP público será criado automaticamente no mesmo grupo de recursos que o cluster AKS se nenhum IP público, prefixo de IP público ou número de IPs for especificado. Você também deve permitir endereços públicos e evitar a criação de qualquer Azure Policy que cause banimento à criação do IP.
* Um IP público criado por AKS não pode ser reutilizado como um traga um endereço IP público personalizado. Todos os endereços IP personalizados devem ser criados e gerenciados pelo usuário.
* Definir o SKU do balanceador de carga só pode ser feito ao criar um cluster do AKS. Não é possível alterar o SKU do balanceador de carga após a criação de um cluster AKS.
* Você só pode usar um tipo de SKU do balanceador de carga (Básico ou Standard) em um cluster.
* Os Balanceadores de Carga de SKU *Standard* dão suporte apenas a Endereços IP de SKU *Standard*.


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os serviços do Kubernetes na [documentação dos serviços do Kubernetes][kubernetes-services].

Saiba mais sobre como usar o Load Balancer interno para o tráfego de entrada na [documentação Load Balancer interna do AKS](internal-lb.md).

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
[az-network-lb-outbound-rule-list]: /cli/azure/network/lb/outbound-rule?view=azure-cli-latest#az-network-lb-outbound-rule-list
[az-network-public-ip-show]: /cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/skus.md
[azure-lb-outbound-rules]: ../load-balancer/load-balancer-outbound-connections.md#outboundrules
[azure-lb-outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md
[azure-lb-outbound-preallocatedports]: ../load-balancer/load-balancer-outbound-connections.md#preallocatedports
[azure-lb-outbound-rules-overview]: ../load-balancer/load-balancer-outbound-connections.md#outboundrules
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[requirements]: #requirements-for-customizing-allocated-outbound-ports-and-idle-timeout
[use-multiple-node-pools]: use-multiple-node-pools.md
[troubleshoot-snat]: #troubleshooting-snat
[service-tags]: ../virtual-network/network-security-groups-overview.md#service-tags

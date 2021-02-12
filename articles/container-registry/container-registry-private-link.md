---
title: Configurar link privado
description: Configure um ponto de extremidade privado em um registro de contêiner e habilite o acesso em um link privado em uma rede virtual local. O acesso ao link privado é um recurso da camada de serviço Premium.
ms.topic: article
ms.date: 10/01/2020
ms.openlocfilehash: 3193c65a2021d29f03bd9ae6cbc00fd6c349d9bf
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93342293"
---
# <a name="connect-privately-to-an-azure-container-registry-using-azure-private-link"></a>Conectar-se de forma privada a um registro de contêiner do Azure usando o link privado do Azure


Limite o acesso a um registro atribuindo endereços IP privados da rede virtual aos pontos de extremidade do registro e usando o [link privado do Azure](../private-link/private-link-overview.md). O tráfego de rede entre os clientes na rede virtual e os pontos de extremidade privados do registro atravessa a rede virtual e um link privado na rede de backbone da Microsoft, eliminando a exposição da Internet pública. O link privado também habilita o acesso de registro privado do local por meio do emparelhamento privado [do Azure ExpressRoute](../expressroute/expressroute-introduction.MD) ou de um [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

Você pode [definir as configurações de DNS](../private-link/private-endpoint-overview.md#dns-configuration) para os pontos de extremidade particulares do registro, para que as configurações sejam resolvidas para o endereço IP privado alocado do registro. Com a configuração de DNS, clientes e serviços na rede podem continuar acessando o registro no nome de domínio totalmente qualificado do registro, como *myregistry.azurecr.io*. 

Esse recurso está disponível na camada de serviço **Premium** do registro de contêiner. No momento, um máximo de 10 pontos de extremidade privados podem ser configurados para um registro. Para obter informações sobre os limites e as camadas de serviço do registro, confira [Camadas do Registro de Contêiner do Azure](container-registry-skus.md).

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Para usar as etapas da CLI do Azure neste artigo, recomendamos a CLI do Azure versão 2.6.0 ou posterior. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli]. Ou executá-la no [Azure Cloud Shell](../cloud-shell/quickstart.md).
* Se você ainda não tiver um registro de contêiner, crie um (camada Premium necessária) e [importe](container-registry-import-images.md) uma imagem pública de exemplo, como `mcr.microsoft.com/hello-world` do registro de contêiner da Microsoft. Por exemplo, use o [portal do Azure][quickstart-portal] ou a [CLI do Azure][quickstart-cli] para criar um registro.
* Para configurar o acesso ao registro usando um link privado em outra assinatura do Azure, você precisará registrar o provedor de recursos para o Registro de Contêiner do Azure nessa assinatura. Por exemplo:

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

Os exemplos da CLI do Azure neste artigo usam as seguintes variáveis de ambiente. Substitua os valores apropriados para o seu ambiente. Todos os exemplos são formatados para o shell Bash:

```bash
REGISTRY_NAME=<container-registry-name>
REGISTRY_LOCATION=<container-registry-location> # Azure region such as westeurope where registry created
RESOURCE_GROUP=<resource-group-name>
VM_NAME=<virtual-machine-name>
```

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="set-up-private-link---cli"></a>Configurar link privado - CLI

### <a name="get-network-and-subnet-names"></a>Obter nomes de rede e sub-rede

Você precisará dos nomes de uma rede virtual e sub-rede para configurar um link privado. Neste exemplo, você usa a mesma sub-rede para a VM e o ponto de extremidade privado do registro. No entanto, em muitos cenários, você configuraria o ponto de extremidade em uma sub-rede separada. 

Ao criar uma VM, o Azure por padrão cria uma rede virtual no mesmo grupo de recursos. O nome da rede virtual é baseado no nome da máquina virtual. Por exemplo, se o nome da máquina virtual for *myDockerVM* , o nome padrão da rede virtual será *myDockerVMVNET* e o da sub-rede será *myDockerVMSubnet*. Defina esses valores nas variáveis de ambiente executando o comando [az network vnet list][az-network-vnet-list]:

```azurecli
NETWORK_NAME=$(az network vnet list \
  --resource-group $RESOURCE_GROUP \
  --query '[].{Name: name}' --output tsv)

SUBNET_NAME=$(az network vnet list \
  --resource-group $RESOURCE_GROUP \
  --query '[].{Subnet: subnets[0].name}' --output tsv)

echo NETWORK_NAME=$NETWORK_NAME
echo SUBNET_NAME=$SUBNET_NAME
```

### <a name="disable-network-policies-in-subnet"></a>Desabilitar políticas de rede na sub-rede

[Desabilite as políticas de rede](../private-link/disable-private-endpoint-network-policy.md), como grupos de segurança de rede na sub-rede para o ponto de extremidade privado. Atualize a configuração de sub-rede com [az network vnet subnet update][az-network-vnet-subnet-update]:

```azurecli
az network vnet subnet update \
 --name $SUBNET_NAME \
 --vnet-name $NETWORK_NAME \
 --resource-group $RESOURCE_GROUP \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>Configurar a zona DNS privada

Crie uma [zona DNS privada](../dns/private-dns-privatednszone.md) para o domínio de registro de contêiner do Azure privado. Mais à frente, você criará registros DNS para seu domínio de registro nesta zona DNS.

Para usar uma zona privada para substituir a resolução DNS padrão para o registro de contêiner do Azure, a zona deve ser nomeada como **privatelink.azurecr.io**. Execute o comando [az network private-dns zone create][az-network-private-dns-zone-create] a seguir para criar a zona privada:

```azurecli
az network private-dns zone create \
  --resource-group $RESOURCE_GROUP \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>Criar um link de associação

Execute [az network private-dns link vnet create][az-network-private-dns-link-vnet-create] para associar sua zona privada à rede virtual. Este exemplo cria um link chamado *myDNSLink*.

```azurecli
az network private-dns link vnet create \
  --resource-group $RESOURCE_GROUP \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $NETWORK_NAME \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>Criar um ponto de extremidade de registro privado

Nesta seção, crie o ponto de extremidade privado do registro na rede virtual. Primeiro, obtenha a ID de recurso do registro:

```azurecli
REGISTRY_ID=$(az acr show --name $REGISTRY_NAME \
  --query 'id' --output tsv)
```

Execute o comando [az network private-endpoint create][az-network-private-endpoint-create] para criar o ponto de extremidade privado do registro.

O exemplo a seguir cria o ponto de extremidade *myPrivateEndpoint* e a conexão de serviço *myConnection*. Para especificar um recurso de registro de contêiner para o ponto de extremidade, passe `--group-ids registry`:

```azurecli
az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $NETWORK_NAME \
    --subnet $SUBNET_NAME \
    --private-connection-resource-id $REGISTRY_ID \
    --group-ids registry \
    --connection-name myConnection
```

### <a name="get-private-ip-addresses"></a>Obter endereços IP privados

Execute [az network private-endpoint show][az-network-private-endpoint-show] para consultar o ponto de extremidade da ID do adaptador de rede:

```azurecli
NETWORK_INTERFACE_ID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $RESOURCE_GROUP \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

Neste exemplo, há dois endereços IP privados do registro de contêiner associados ao adaptador de rede: um para o registro em si e outro para o ponto de extremidade de dados do registro. Os comandos [az resource show][az-resource-show] a seguir obtêm os endereços IP privados para o registro de contêiner e o ponto de extremidade de dados do registro:

```azurecli
PRIVATE_IP=$(az resource show \
  --ids $NETWORK_INTERFACE_ID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[1].properties.privateIPAddress' \
  --output tsv)

DATA_ENDPOINT_PRIVATE_IP=$(az resource show \
  --ids $NETWORK_INTERFACE_ID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[0].properties.privateIPAddress' \
  --output tsv)
```

> [!NOTE]
> Se o registro tiver [replicação geográfica](container-registry-geo-replication.md), consulte o ponto de extremidade de dados adicional para cada réplica de registro.

### <a name="create-dns-records-in-the-private-zone"></a>Criar registros DNS na zona privada

Os comandos a seguir criam registros DNS na zona privada para o ponto de extremidade do registro e seu ponto de extremidade de dados. Por exemplo, se houver um registro com nome *myregistry* na região *westeurope* , os nomes de ponto de extremidade serão `myregistry.azurecr.io` e `myregistry.westeurope.data.azurecr.io`. 

> [!NOTE]
> Se o registro tiver [replicação geográfica](container-registry-geo-replication.md), crie registros DNS adicionais para o IP do ponto de extremidade de dados de cada réplica.

Primeiro, execute [az network private-dns record-set a create][az-network-private-dns-record-set-a-create] para criar conjuntos de registros A vazios para o ponto de extremidade do registro e o ponto de extremidade de dados:

```azurecli
az network private-dns record-set a create \
  --name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP

# Specify registry region in data endpoint name
az network private-dns record-set a create \
  --name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP
```

Execute o comando [az network private-dns record-set a add-record][az-network-private-dns-record-set-a-add-record] para criar registros A para o ponto de extremidade do registro e o ponto de extremidade de dados:

```azurecli
az network private-dns record-set a add-record \
  --record-set-name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $PRIVATE_IP

# Specify registry region in data endpoint name
az network private-dns record-set a add-record \
  --record-set-name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $DATA_ENDPOINT_PRIVATE_IP
```

O link privado agora está configurado e pronto para uso.

## <a name="set-up-private-link---portal"></a>Configurar o link privado - Portal

Configure um link privado ao criar um registro, ou adicione um link privado a um registro existente. As etapas a seguir pressupõem que você já tenha uma rede e uma sub-rede virtuais configuradas com uma VM para teste. Você também pode [criar uma nova rede e sub-rede virtuais](../virtual-network/quick-create-portal.md).

### <a name="create-a-private-endpoint---new-registry"></a>Criar um ponto de extremidade privado - Novo registro

1. Ao criar um registro no portal, na guia **Noções básicas** , em **SKU** , selecione **Premium**.
1. Selecione a guia **Rede**.
1. Em **Conectividade de rede** , selecione **Ponto de extremidade privado** >  **+ Adicionar**.
1. Insira ou selecione as seguintes informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Insira o nome de um grupo existente ou crie um novo.|
    | Nome | Insira um nome exclusivo. |
    | Sub-recurso |Selecione **registro**|
    | **Rede** | |
    | Rede virtual| Selecione a rede virtual em que sua máquina virtual está implantada, como *myDockerVMVNET*. |
    | Sub-rede | Selecione uma sub-rede, como *myDockerVMSubnet* em que sua máquina virtual está implantada. |
    |**Integração de DNS privado**||
    |Integrar com a zona DNS privado |Selecione **Sim** na barra superior. |
    |Zona DNS privado |Selecione *(Novo) privatelink.azurecr.io* |
    |||
1. Defina as configuração restantes de registro e depois selecione **Revisar + Criar**.

  ![Criar registro com ponto de extremidade privado](./media/container-registry-private-link/private-link-create-portal.png)

### <a name="create-a-private-endpoint---existing-registry"></a>Criar um ponto de extremidade privado - Registro existente

1. No portal, navegue até o registro de contêiner.
1. Em **Configurações** , selecione **Rede**.
1. Na guia **Pontos de extremidade privados** , selecione **+ Ponto de extremidade privado**.
1. Na guia **Noções básicas** , insira ou selecione as informações a seguir:

    | Configuração | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Insira o nome de um grupo existente ou crie um novo.|
    | **Detalhes da instância** |  |
    | Nome | Insira um nome. |
    |Região|Selecione uma região.|
    |||
5. Selecione **Avançar: Recurso**.
6. Insira ou selecione as seguintes informações:

    | Configuração | Valor |
    | ------- | ----- |
    |Método de conexão  | Selecione **Conectar-se a um recurso do Azure em meu diretório**.|
    | Subscription| Selecione sua assinatura. |
    | Tipo de recurso | Selecione **Microsoft.ContainerRegistry/registries**. |
    | Recurso |Selecione o nome do registro|
    |Sub-recurso de destino |Selecione **registro**|
    |||
7. Selecione **Avançar: configuração**.
8. Insira ou selecione as informações:

    | Configuração | Valor |
    | ------- | ----- |
    |**Rede**| |
    | Rede virtual| Selecione a rede virtual em que sua máquina virtual está implantada, como *myDockerVMVNET*. |
    | Sub-rede | Selecione uma sub-rede, como *myDockerVMSubnet* em que sua máquina virtual está implantada. |
    |**Integração de DNS privado**||
    |Integrar com a zona DNS privado |Selecione **Sim** na barra superior. |
    |Zona DNS privado |Selecione *(Novo) privatelink.azurecr.io* |
    |||

1. Selecione **Examinar + criar**. Você é levado até a página **Examinar + criar** , na qual o Azure valida sua configuração. 
2. Quando vir a mensagem **Validação aprovada** , selecione **Criar**.

Depois que o ponto de extremidade privado for criado, as configurações de DNS na zona privada aparecerão na página **Pontos de extremidade privado** no portal:

1. No portal, navegue até o registro de contêiner e selecione **Configurações > Rede**.
1. Na guia **Ponto de extremidade privado** , selecione o ponto de extremidade privado criado.
1. Na página **Visão geral** , revise as configurações de link e as configurações personalizadas de DNS.

  ![Configurações de DNS do ponto de extremidade](./media/container-registry-private-link/private-endpoint-overview.png)

Seu link privado agora está configurado e pronto para uso.

## <a name="disable-public-access"></a>Desabilitar o acesso público

Em muitos cenários, desabilite o acesso ao registro de redes públicas. Essa configuração impede que clientes fora da rede virtual atinjam os pontos de extremidade do registro. 

### <a name="disable-public-access---cli"></a>Desabilitar o acesso público – CLI

Para desabilitar o acesso público usando ao CLI do Azure, execute [az acr update][az-acr-update] e defina `--public-network-enabled` como `false`. 

> [!NOTE]
> O argumento `public-network-enabled` requer a CLI do Azure 2.6.0 ou posterior. 

```azurecli
az acr update --name $REGISTRY_NAME --public-network-enabled false
```


### <a name="disable-public-access---portal"></a>Desabilitar o acesso público – portal

1. No portal, navegue até o registro de contêiner e selecione **Configurações > Rede**.
1. Na guia **Acesso público** , em **Permitir acesso da rede pública** , selecione **Desabilitado**. Em seguida, selecione **Salvar**.

## <a name="validate-private-link-connection"></a>Validar conexão de link privado

Você deve validar se os recursos dentro da sub-rede do ponto de extremidade privado se conectam ao registro por meio de um endereço IP privado e têm a integração de zona DNS privada correta.

Para validar a conexão de link privado, use o SSH para a máquina virtual que você configurou na rede virtual.

Execute um utilitário como `nslookup` ou `dig` para pesquisar o endereço IP do registro no link privado. Por exemplo:

```bash
dig $REGISTRY_NAME.azurecr.io
```

O exemplo de saída mostra o endereço IP do registro no espaço de endereço da sub-rede:

```console
[...]
; <<>> DiG 9.11.3-1ubuntu1.13-Ubuntu <<>> myregistry.azurecr.io
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 52155
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;myregistry.azurecr.io.         IN      A

;; ANSWER SECTION:
myregistry.azurecr.io.  1783    IN      CNAME   myregistry.privatelink.azurecr.io.
myregistry.privatelink.azurecr.io. 10 IN A      10.0.0.7

[...]
```

Compare esse resultado com o endereço IP público na saída `dig` do mesmo registro em um ponto de extremidade público:

```console
[...]
;; ANSWER SECTION:
myregistry.azurecr.io.  2881    IN  CNAME   myregistry.privatelink.azurecr.io.
myregistry.privatelink.azurecr.io. 2881 IN CNAME xxxx.xx.azcr.io.
xxxx.xx.azcr.io.    300 IN  CNAME   xxxx-xxx-reg.trafficmanager.net.
xxxx-xxx-reg.trafficmanager.net. 300 IN CNAME   xxxx.westeurope.cloudapp.azure.com.
xxxx.westeurope.cloudapp.azure.com. 10  IN A 20.45.122.144

[...]
```

### <a name="registry-operations-over-private-link"></a>Operações de registro em link privado

Verifique também se você pode executar operações de registro da máquina virtual na sub-rede. Faça uma conexão SSH com sua máquina virtual e execute [az acr login][az-acr-login] para fazer logon no seu registro. Dependendo da configuração da VM, pode ser necessário usar o prefixo `sudo` nos comandos a seguir.

```bash
az acr login --name $REGISTRY_NAME
```

Execute operações de registro, como `docker pull`, para efetuar pull de uma amostra de imagem do registro. Substitua `hello-world:v1` por uma imagem e uma marca apropriadas ao registro, prefixado com o nome do servidor de logon do registro (todas as letras minúsculas):

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

O Docker efetua pull da imagem com êxito para a VM.

## <a name="manage-private-endpoint-connections"></a>Gerenciar conexões de ponto de extremidade privado

Gerencie as conexões de ponto de extremidade privado de um registro usando o portal do Azure ou comandos no grupo de comandos [az acr private-endpoint-connection][az-acr-private-endpoint-connection]. As operações incluem aprovar, excluir, listar, rejeitar ou mostrar detalhes de conexões de ponto de extremidade privado de um registro.

Por exemplo, para listar as conexões de ponto de extremidade privado, execute o comando [az acr private-endpoint-connection list][az-acr-private-endpoint-connection-list]. Por exemplo:

```azurecli
az acr private-endpoint-connection list \
  --registry-name $REGISTRY_NAME 
```

Quando você configura uma conexão de ponto de extremidade privado usando as etapas neste artigo, o registro aceita automaticamente conexões de clientes e serviços que têm permissões de RBAC do Azure no registro. Você pode configurar o ponto de extremidade para exigir aprovação manual de conexões. Para informações sobre como aprovar e rejeitar conexões de ponto de extremidade privado, consulte [Gerenciar uma conexão de ponto de extremidade privado](../private-link/manage-private-endpoint.md).

## <a name="add-zone-records-for-replicas"></a>Adicionar registros de zona para réplicas

Conforme mostrado neste artigo, ao adicionar uma conexão de ponto de extremidade privada a um registro, você cria registros DNS na `privatelink.azurecr.io` zona para o registro e seus pontos de extremidade de dados nas regiões em que o registro é [replicado](container-registry-geo-replication.md). 

Se você adicionar uma nova réplica mais tarde, precisará adicionar manualmente um novo registro de zona para o ponto de extremidade de dados nessa região. Por exemplo, se você criar uma réplica de *myregistry* no local *northeurope* , adicione um registro de zona para `myregistry.northeurope.data.azurecr.io`. Para instruções, consulte [Criar registros DNS na zona privada](#create-dns-records-in-the-private-zone) neste artigo.

## <a name="dns-configuration-options"></a>Opções de configuração de DNS

O ponto de extremidade privado neste exemplo se integra a uma zona DNS privada associada a uma rede virtual básica. Essa configuração usa o serviço DNS fornecido pelo Azure diretamente para resolver o FQDN público do registro para seu endereço IP privado na rede virtual. 

O link privado dá suporte a cenários de configuração de DNS adicionais que usam a zona privada, incluindo as soluções de DNS personalizadas. Por exemplo, você pode ter uma solução de DNS personalizada implantada na rede virtual ou localmente em uma rede conectada à rede virtual usando um gateway de VPN ou o Azure ExpressRoute. 

Para resolver o FQDN público do registro para o endereço IP privado nesses cenários, você precisa configurar um encaminhador no nível do servidor para o serviço DNS do Azure (168.63.129.16). As opções e as etapas de configuração exatas dependem de suas redes e DNS existentes. Para obter exemplos, consulte [configuração de DNS do ponto de extremidade privado do Azure](../private-link/private-endpoint-dns.md).

> [!IMPORTANT]
> Se para alta disponibilidade você criou pontos de extremidade privados em várias regiões, recomendamos que você use um grupo de recursos separado em cada região e coloque a rede virtual e a zona DNS privada associada nela. Essa configuração também impede a resolução de DNS imprevisível causada pelo compartilhamento da mesma zona DNS privada.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você criou todos os recursos do Azure no mesmo grupo de recursos e não precisa mais deles, é possível excluí-los usando um único comando [az group delete](/cli/azure/group):

```azurecli
az group delete --name $RESOURCE_GROUP
```

Para limpar seus recursos no portal, navegue até o grupo de recursos. Depois que o grupo de recursos for carregado, clique em **Excluir grupo de recursos** para remover o grupo de recursos e os recursos armazenados nele.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre o Link Privado, consulte a documentação do [Link Privado do Azure](../private-link/private-link-overview.md).
* Se você precisar configurar regras de acesso do registro por trás de um firewall do cliente, confira [Configurar regras para acessar um registro de contêiner do Azure atrás de um firewall](container-registry-firewall-access-rules.md).

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-private-endpoint-connection]: /cli/azure/acr/private-endpoint-connection
[az-acr-private-endpoint-connection-list]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-list
[az-acr-private-endpoint-connection-approve]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-approve
[az-acr-update]: /cli/azure/acr#az-acr-update
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[az-network-private-endpoint-create]: /cli/azure/network/private-endpoint#az-network-private-endpoint-create
[az-network-private-endpoint-show]: /cli/azure/network/private-endpoint#az-network-private-endpoint-show
[az-network-private-dns-zone-create]: /cli/azure/network/private-dns/zone#az-network-private-dns-zone-create
[az-network-private-dns-link-vnet-create]: /cli/azure/network/private-dns/link/vnet#az-network-private-dns-link-vnet-create
[az-network-private-dns-record-set-a-create]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-create
[az-network-private-dns-record-set-a-add-record]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-add-record
[az-resource-show]: /cli/azure/resource#az-resource-show
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com

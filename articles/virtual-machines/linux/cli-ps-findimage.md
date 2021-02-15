---
title: Selecionar imagens da VM Linux com a CLI do Azure
description: Saiba como usar a CLI do Azure para determinar o editor, oferta, SKU e versão de imagens de VM do Marketplace.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 01/25/2019
ms.author: cynthn
ms.openlocfilehash: 8954ad03bd5f539e9dcfbb4249f4e7cc1cf0bc7f
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685116"
---
# <a name="find-linux-vm-images-in-the-azure-marketplace-with-the-azure-cli"></a>Localizar imagens de VM do Linux no Azure Marketplace com a CLI do Azure

Este tópico descreve como usar a CLI do Azure para localizar imagens de VM no Azure Marketplace. Use essas informações para especificar uma imagem do Marketplace quando você criar uma VM programaticamente com a CLI, os modelos do Gerenciador de Recursos ou outras ferramentas.

Procure também imagens e ofertas disponíveis usando a vitrine do [Azure Marketplace](https://azuremarketplace.microsoft.com/), o [portal do Azure](https://portal.azure.com) ou o [Azure PowerShell](../windows/cli-ps-findimage.md). 

Verifique se você está conectado a uma conta do Azure ( `az login` ).

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="deploy-from-a-vhd-using-purchase-plan-parameters"></a>Implantar de um VHD usando parâmetros do plano de compra

Se você tiver um VHD existente que foi criado usando uma imagem paga do Azure Marketplace, talvez seja necessário fornecer as informações do plano de compra ao criar uma nova VM a partir desse VHD. 

Se você ainda tiver a VM original ou outra VM criada usando a mesma imagem do Marketplace, poderá obter o nome do plano, o editor e as informações do produto dele usando [AZ VM Get-Instance-View](/cli/azure/vm#az_vm_get_instance_view). Este exemplo obtém uma VM chamada *myVM* no grupo de recursos *MyResource* Group e, em seguida, exibe as informações do plano de compra.

```azurepowershell-interactive
az vm get-instance-view -g myResourceGroup -n myVM --query plan
```

Se você não obtiver as informações do plano antes da exclusão da VM original, poderá arquivar uma [solicitação de suporte](https://ms.portal.azure.com/#create/Microsoft.Support). Eles precisarão do nome da VM, da ID da assinatura e do carimbo de data/hora da operação de exclusão.

Depois de ter as informações do plano, você pode criar a nova VM usando o `--attach-os-disk` parâmetro para especificar o VHD.

```azurecli-interactive
az vm create \
   --resource-group myResourceGroup \
  --name myNewVM \
  --nics myNic \
  --size Standard_DS1_v2 --os-type Linux \
  --attach-os-disk myVHD \
  --plan-name planName \
  --plan-publisher planPublisher \
  --plan-product planProduct 
```

## <a name="deploy-a-new-vm-using-purchase-plan-parameters"></a>Implantar uma nova VM usando parâmetros do plano de compra

Se você já tiver informações sobre a imagem, poderá implantá-la usando o `az vm create` comando. Neste exemplo, implantamos uma VM com a imagem RabbitMQ certificada pela BitNami:

```azurecli
az group create --name myResourceGroupVM --location westus

az vm create --resource-group myResourceGroupVM --name myVM --image bitnami:rabbitmq:rabbitmq:latest --plan-name rabbitmq --plan-product rabbitmq --plan-publisher bitnami
```

Se você receber uma mensagem sobre como aceitar os termos da imagem, consulte a seção [aceitar os termos](#accept-the-terms) mais adiante neste artigo.

## <a name="list-popular-images"></a>Listar imagens populares

Execute o comando [az vm image list](/cli/azure/vm/image) sem a opção `--all` para ver uma lista de imagens de VM populares no Azure Marketplace. Por exemplo, execute o comando a seguir para exibir uma lista armazenada em cache de imagens populares em formato de tabela:

```azurecli
az vm image list --output table
```

A saída inclui o URN da imagem (o valor na coluna *Urn*). Ao criar uma VM com uma das imagens populares do Marketplace, você poderá especificar como alternativa o *UrnAlias*, uma forma abreviada, como *UbuntuLTS*.

```output
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.5                 OpenLogic:CentOS:7.5:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.3                SUSE:openSUSE-Leap:42.3:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7-RAW               RedHat:RHEL:7-RAW:latest                                        RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
...
```

## <a name="find-specific-images"></a>Localizar imagens específicas

Para localizar uma imagem de VM específica no mercado, use o comando `az vm image list` com a opção `--all`. Essa versão do comando leva algum tempo para concluir e pode retornar uma saída longa, portanto, você geralmente filtra a lista por `--publisher` ou outro parâmetro. 

O exemplo a seguir exibe todas as ofertas para Debian (lembre-se de que sem a opção `--all`, ele pesquisa apenas no cache local de imagens comuns):

```azurecli
az vm image list --offer Debian --all --output table 

```

Resultado parcial: 

```output
Offer              Publisher    Sku                  Urn                                                    Version
-----------------  -----------  -------------------  -----------------------------------------------------  --------------
Debian             credativ     7                    credativ:Debian:7:7.0.201602010                        7.0.201602010
Debian             credativ     7                    credativ:Debian:7:7.0.201603020                        7.0.201603020
Debian             credativ     7                    credativ:Debian:7:7.0.201604050                        7.0.201604050
Debian             credativ     7                    credativ:Debian:7:7.0.201604200                        7.0.201604200
Debian             credativ     7                    credativ:Debian:7:7.0.201606280                        7.0.201606280
Debian             credativ     7                    credativ:Debian:7:7.0.201609120                        7.0.201609120
Debian             credativ     7                    credativ:Debian:7:7.0.201611020                        7.0.201611020
Debian             credativ     7                    credativ:Debian:7:7.0.201701180                        7.0.201701180
Debian             credativ     8                    credativ:Debian:8:8.0.201602010                        8.0.201602010
Debian             credativ     8                    credativ:Debian:8:8.0.201603020                        8.0.201603020
Debian             credativ     8                    credativ:Debian:8:8.0.201604050                        8.0.201604050
Debian             credativ     8                    credativ:Debian:8:8.0.201604200                        8.0.201604200
Debian             credativ     8                    credativ:Debian:8:8.0.201606280                        8.0.201606280
Debian             credativ     8                    credativ:Debian:8:8.0.201609120                        8.0.201609120
Debian             credativ     8                    credativ:Debian:8:8.0.201611020                        8.0.201611020
Debian             credativ     8                    credativ:Debian:8:8.0.201701180                        8.0.201701180
Debian             credativ     8                    credativ:Debian:8:8.0.201703150                        8.0.201703150
Debian             credativ     8                    credativ:Debian:8:8.0.201704110                        8.0.201704110
Debian             credativ     8                    credativ:Debian:8:8.0.201704180                        8.0.201704180
Debian             credativ     8                    credativ:Debian:8:8.0.201706190                        8.0.201706190
Debian             credativ     8                    credativ:Debian:8:8.0.201706210                        8.0.201706210
Debian             credativ     8                    credativ:Debian:8:8.0.201708040                        8.0.201708040
Debian             credativ     8                    credativ:Debian:8:8.0.201710090                        8.0.201710090
Debian             credativ     8                    credativ:Debian:8:8.0.201712040                        8.0.201712040
Debian             credativ     8                    credativ:Debian:8:8.0.201801170                        8.0.201801170
Debian             credativ     8                    credativ:Debian:8:8.0.201803130                        8.0.201803130
Debian             credativ     8                    credativ:Debian:8:8.0.201803260                        8.0.201803260
Debian             credativ     8                    credativ:Debian:8:8.0.201804020                        8.0.201804020
Debian             credativ     8                    credativ:Debian:8:8.0.201804150                        8.0.201804150
Debian             credativ     8                    credativ:Debian:8:8.0.201805160                        8.0.201805160
Debian             credativ     8                    credativ:Debian:8:8.0.201807160                        8.0.201807160
Debian             credativ     8                    credativ:Debian:8:8.0.201901221                        8.0.201901221
...
```

Aplique filtros semelhantes com as opções `--location`, `--publisher` e `--sku`. Você pode executar correspondências parciais em um filtro, assim como procurar por `--offer Deb` para localizar todas as imagens Debian.

Se você não especificar um local específico com a opção `--location`, os valores para o local padrão serão retornados. (Defina um local diferente do padrão executando `az configure --defaults location=<location>`.)

Por exemplo, o comando a seguir lista todos as SKUs do Debian 8 no local Europa Ocidental:

```azurecli
az vm image list --location westeurope --offer Deb --publisher credativ --sku 8 --all --output table
```

Resultado parcial:

```output
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  -------------
Debian   credativ     8                  credativ:Debian:8:8.0.201602010                  8.0.201602010
Debian   credativ     8                  credativ:Debian:8:8.0.201603020                  8.0.201603020
Debian   credativ     8                  credativ:Debian:8:8.0.201604050                  8.0.201604050
Debian   credativ     8                  credativ:Debian:8:8.0.201604200                  8.0.201604200
Debian   credativ     8                  credativ:Debian:8:8.0.201606280                  8.0.201606280
Debian   credativ     8                  credativ:Debian:8:8.0.201609120                  8.0.201609120
Debian   credativ     8                  credativ:Debian:8:8.0.201611020                  8.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201701180                  8.0.201701180
Debian   credativ     8                  credativ:Debian:8:8.0.201703150                  8.0.201703150
Debian   credativ     8                  credativ:Debian:8:8.0.201704110                  8.0.201704110
Debian   credativ     8                  credativ:Debian:8:8.0.201704180                  8.0.201704180
Debian   credativ     8                  credativ:Debian:8:8.0.201706190                  8.0.201706190
Debian   credativ     8                  credativ:Debian:8:8.0.201706210                  8.0.201706210
Debian   credativ     8                  credativ:Debian:8:8.0.201708040                  8.0.201708040
Debian   credativ     8                  credativ:Debian:8:8.0.201710090                  8.0.201710090
Debian   credativ     8                  credativ:Debian:8:8.0.201712040                  8.0.201712040
Debian   credativ     8                  credativ:Debian:8:8.0.201801170                  8.0.201801170
Debian   credativ     8                  credativ:Debian:8:8.0.201803130                  8.0.201803130
Debian   credativ     8                  credativ:Debian:8:8.0.201803260                  8.0.201803260
Debian   credativ     8                  credativ:Debian:8:8.0.201804020                  8.0.201804020
Debian   credativ     8                  credativ:Debian:8:8.0.201804150                  8.0.201804150
Debian   credativ     8                  credativ:Debian:8:8.0.201805160                  8.0.201805160
Debian   credativ     8                  credativ:Debian:8:8.0.201807160                  8.0.201807160
Debian   credativ     8                  credativ:Debian:8:8.0.201901221                  8.0.201901221
...
```

## <a name="navigate-the-images"></a>Navegar pelas imagens
 
Outra maneira de localizar uma imagem em um local é executar os comandos [az vm image list-publishers](/cli/azure/vm/image), [az vm image list-offers](/cli/azure/vm/image) e [az vm image list-skus](/cli/azure/vm/image) em sequência. Com esses comandos, você determina estes valores:

1. Liste os editores de imagem.
2. Para um determinado editor, liste suas ofertas.
3. Para uma determinada oferta, liste seus SKUs.

Em seguida, para uma SKU selecionada, você pode escolher uma versão para implantar.

Por exemplo, o comando a seguir lista os editores de imagem na localização Oeste dos EUA:

```azurecli
az vm image list-publishers --location westus --output table
```

Resultado parcial:

```output
Location    Name
----------  ----------------------------------------------------
westus      128technology
westus      1e
westus      4psa
westus      5nine-software-inc
westus      7isolutions
westus      a10networks
westus      abiquo
westus      accellion
westus      accessdata-group
westus      accops
westus      Acronis
westus      Acronis.Backup
westus      actian-corp
westus      actian_matrix
westus      actifio
westus      activeeon
westus      advantech-webaccess
westus      aerospike
westus      affinio
westus      aiscaler-cache-control-ddos-and-url-rewriting-
westus      akamai-technologies
westus      akumina
...
```

Use essas informações para encontrar as ofertas do editor específico. Por exemplo, para o editor *Canonical* no local Oeste dos EUA, localize as ofertas dele executando `azure vm image list-offers`. Passe a localização e o editor como no exemplo a seguir:

```azurecli
az vm image list-offers --location westus --publisher Canonical --output table
```

Saída:

```output
Location    Name
----------  -------------------------
westus      Ubuntu15.04Snappy
westus      Ubuntu15.04SnappyDocker
westus      UbunturollingSnappy
westus      UbuntuServer
westus      Ubuntu_Core
```
Você vê que, na região Oeste dos EUA, o Canonical publica a oferta *UbuntuServer* no Azure. Mas quais SKUs? Para obter esses valores, execute `azure vm image list-skus` e defina a localização, o editor e a oferta que você descobriu:

```azurecli
az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
```

Saída:

```output
Location    Name
----------  -----------------
westus      12.04.3-LTS
westus      12.04.4-LTS
westus      12.04.5-LTS
westus      14.04.0-LTS
westus      14.04.1-LTS
westus      14.04.2-LTS
westus      14.04.3-LTS
westus      14.04.4-LTS
westus      14.04.5-DAILY-LTS
westus      14.04.5-LTS
westus      16.04-DAILY-LTS
westus      16.04-LTS
westus      16.04.0-LTS
westus      18.04-DAILY-LTS
westus      18.04-LTS
westus      18.10
westus      18.10-DAILY
westus      19.04-DAILY
```

Finalmente, use o comando `az vm image list` para encontrar uma versão específica do SKU que você deseja, por exemplo, *18.04-LTS*:

```azurecli
az vm image list --location westus --publisher Canonical --offer UbuntuServer --sku 18.04-LTS --all --output table
```

Resultado parcial:

```output
Offer         Publisher    Sku        Urn                                               Version
------------  -----------  ---------  ------------------------------------------------  ---------------
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201804262  18.04.201804262
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201805170  18.04.201805170
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201805220  18.04.201805220
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201806130  18.04.201806130
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201806170  18.04.201806170
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201807240  18.04.201807240
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808060  18.04.201808060
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808080  18.04.201808080
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808140  18.04.201808140
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808310  18.04.201808310
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201809110  18.04.201809110
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810030  18.04.201810030
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810240  18.04.201810240
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810290  18.04.201810290
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201811010  18.04.201811010
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812031  18.04.201812031
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812040  18.04.201812040
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812060  18.04.201812060
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201901140  18.04.201901140
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201901220  18.04.201901220
...
```

Agora você pode escolher exatamente a imagem que deseja usar anotando o valor do URN. Passe esse valor com o parâmetro `--image` quando criar uma VM com o comando [az vm create](/cli/azure/vm). Lembre-se de que é possível substituir, como opção, o número de versão no URN por "mais recente". Essa versão é sempre a versão mais recente da imagem. 

Se você implantar uma VM com um modelo do Gerenciador de Recursos, defina os parâmetros de imagem individualmente nas propriedades `imageReference`. Consulte a [referência de modelo](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Exibir propriedades de plano

Para exibir as informações do plano de compra de uma imagem, execute o comando [az vm image show](/cli/azure/image). Se a propriedade `plan` na saída não for `null`, isso significa que a imagem tem termos que você precisa aceitar antes da implantação programática.

Por exemplo, a imagem Canonical Ubuntu Server 18.04 LTS não possui termos adicionais, porque a informação `plan` é `null`:

```azurecli
az vm image show --location westus --urn Canonical:UbuntuServer:18.04-LTS:latest
```

Saída:

```output
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/18.04-LTS/Versions/18.04.201901220",
  "location": "westus",
  "name": "18.04.201901220",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

Executar um comando semelhante para a imagem de RabbitMQ Certified by Bitnami mostra as seguintes `plan` propriedades: `name`, `product` e `publisher`. (Algumas imagens também têm uma `promotion code` propriedade.) Para implantar essa imagem, consulte as seções a seguir para aceitar os termos e habilitar a implantação programática.

```azurecli
az vm image show --location westus --urn bitnami:rabbitmq:rabbitmq:latest
```
Saída:

```output
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/bitnami/ArtifactTypes/VMImage/Offers/rabbitmq/Skus/rabbitmq/Versions/3.7.1901151016",
  "location": "westus",
  "name": "3.7.1901151016",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": {
    "name": "rabbitmq",
    "product": "rabbitmq",
    "publisher": "bitnami"
  },
  "tags": null
}
```

## <a name="accept-the-terms"></a>Aceitar os termos

Para exibir e aceitar os termos de licença, use o comando [az vm image accept-terms](/cli/azure/vm/image?). Quando aceita os termos, você habilita a implantação programática na sua assinatura. Você só precisa aceitar os termos uma vez por assinatura para a imagem. Por exemplo:

```azurecli
az vm image accept-terms --urn bitnami:rabbitmq:rabbitmq:latest
``` 

A saída inclui um `licenseTextLink` para os termos da licença e indica que o valor de `accepted` é `true`:

```output
{
  "accepted": true,
  "additionalProperties": {},
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.MarketplaceOrdering/offertypes/bitnami/offers/rabbitmq/plans/rabbitmq",
  "licenseTextLink": "https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_BITNAMI%253a24RABBITMQ%253a24RABBITMQ%253a24IGRT7HHPIFOBV3IQYJHEN2O2FGUVXXZ3WUYIMEIVF3KCUNJ7GTVXNNM23I567GBMNDWRFOY4WXJPN5PUYXNKB2QLAKCHP4IE5GO3B2I.txt",
  "name": "rabbitmq",
  "plan": "rabbitmq",
  "privacyPolicyLink": "https://bitnami.com/privacy",
  "product": "rabbitmq",
  "publisher": "bitnami",
  "retrieveDatetime": "2019-01-25T20:37:49.937096Z",
  "signature": "XXXXXXLAZIK7ZL2YRV5JYQXONPV76NQJW3FKMKDZYCRGXZYVDGX6BVY45JO3BXVMNA2COBOEYG2NO76ONORU7ITTRHGZDYNJNXXXXXX",
  "type": "Microsoft.MarketplaceOrdering/offertypes"
}
```

## <a name="next-steps"></a>Próximas etapas
Para criar uma máquina virtual rapidamente usando as informações de imagem, consulte [Criar e gerenciar VMs do Linux usando a CLI do Azure](tutorial-manage-vm.md).

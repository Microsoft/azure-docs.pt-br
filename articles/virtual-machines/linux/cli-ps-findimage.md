---
title: Localizar e usar informações do plano de compra do Marketplace usando a CLI
description: Saiba como usar o CLI do Azure para localizar os parâmetros de URNs de imagem e de plano de compra, como o Publicador, a oferta, a SKU e a versão, para imagens de VM do Marketplace.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.collection: linux
ms.custom: contperf-fy21q3-portal
ms.openlocfilehash: 70cb4cc54c6f9a376d3bd38dc8bb6cd3a059a20c
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105022833"
---
# <a name="find-azure-marketplace-image-information-using-the-azure-cli"></a>Localizar informações de imagem do Azure Marketplace usando o CLI do Azure

Este tópico descreve como usar a CLI do Azure para localizar imagens de VM no Azure Marketplace. Use essas informações para especificar uma imagem do Marketplace quando você criar uma VM programaticamente com a CLI, os modelos do Gerenciador de Recursos ou outras ferramentas.

Você também pode procurar imagens e ofertas disponíveis usando o [Azure Marketplace](https://azuremarketplace.microsoft.com/) ou o  [Azure PowerShell](../windows/cli-ps-findimage.md). 

## <a name="terminology"></a>Terminologia

Uma imagem do Marketplace no Azure tem os seguintes atributos:

* **Publicador**: a organização que criou a imagem. Exemplos: Canonical, MicrosoftWindowsServer
* **Oferta**: Nome de um grupo de imagens relacionadas criadas por um publicador. Exemplos: UbuntuServer, WindowsServer
* **SKU**: uma instância de uma oferta, como uma versão principal de uma distribuição. Exemplos: 18, 4-LTS, 2019-datacenter
* **Versão**: o número de versão de uma imagem de SKU. 

Esses valores podem ser passados individualmente ou como um *urn* de imagem, combinando os valores separados por dois-pontos (:). Por exemplo: *Publicador*:*oferta*:*SKU*:*versão*. Você pode substituir o número de versão no URN por `latest` para usar a versão mais recente da imagem. 

Se o Publicador de imagem fornecer licença adicional e termos de compra, você deverá aceitá-los antes de poder usar a imagem.  Para obter mais informações, consulte [verificar as informações do plano de compra](#check-the-purchase-plan-information).



## <a name="list-popular-images"></a>Listar imagens populares

Execute o comando [az vm image list](/cli/azure/vm/image) sem a opção `--all` para ver uma lista de imagens de VM populares no Azure Marketplace. Por exemplo, execute o comando a seguir para exibir uma lista armazenada em cache de imagens populares em formato de tabela:

```azurecli
az vm image list --output table
```

A saída inclui o URN da imagem. Você também pode usar o *UrnAlias* , que é uma versão reduzida criada para imagens populares, como *UbuntuLTS*.

```output
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.5                 OpenLogic:CentOS:7.5:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
debian-10      Debian                  10                  Debian:debian-10:10:latest                                      Debian               latest
openSUSE-Leap  SUSE                    42.3                SUSE:openSUSE-Leap:42.3:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7-LVM               RedHat:RHEL:7-LVM:latest                                        RHEL                 latest
SLES           SUSE                    15                  SUSE:SLES:15:latest                                             SLES                 latest
UbuntuServer   Canonical               18.04-LTS           Canonical:UbuntuServer:18.04-LTS:latest                         UbuntuLTS            latest
WindowsServer  MicrosoftWindowsServer  2019-Datacenter     MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest     Win2019Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
```

## <a name="find-specific-images"></a>Localizar imagens específicas

Para localizar uma imagem de VM específica no mercado, use o comando `az vm image list` com a opção `--all`. Essa versão do comando leva algum tempo para concluir e pode retornar uma saída longa, portanto, você geralmente filtra a lista por `--publisher` ou outro parâmetro. 

O exemplo a seguir exibe todas as ofertas para Debian (lembre-se de que sem a opção `--all`, ele pesquisa apenas no cache local de imagens comuns):

```azurecli
az vm image list --offer Debian --all --output table 
```

Resultado parcial: 

```output
Offer                                    Publisher                         Sku                                      Urn                                                                                                   Version
---------------------------------------  --------------------------------  ---------------------------------------  ----------------------------------------------------------------------------------------------------  --------------
apache-solr-on-debian                    apps-4-rent                       apache-solr-on-debian                    apps-4-rent:apache-solr-on-debian:apache-solr-on-debian:1.0.0                                         1.0.0
atomized-h-debian10-v1                   atomizedinc1587939464368          hdebian10plan                            atomizedinc1587939464368:atomized-h-debian10-v1:hdebian10plan:1.0.0                                   1.0.0
atomized-h-debian9-v1                    atomizedinc1587939464368          hdebian9plan                             atomizedinc1587939464368:atomized-h-debian9-v1:hdebian9plan:1.0.0                                     1.0.0
atomized-r-debian10-v1                   atomizedinc1587939464368          rdebian10plan                            atomizedinc1587939464368:atomized-r-debian10-v1:rdebian10plan:1.0.0                                   1.0.0
atomized-r-debian9-v1                    atomizedinc1587939464368          rdebian9plan                             atomizedinc1587939464368:atomized-r-debian9-v1:rdebian9plan:1.0.0                                     1.0.0
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.7                         1.0.7
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.8                         1.0.8
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.9                         1.0.9
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.18                          1.0.18
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.19                          1.0.19
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.20                          1.0.20
apache-web-server-with-debian-10         cognosys                          apache-web-server-with-debian-10         cognosys:apache-web-server-with-debian-10:apache-web-server-with-debian-10:1.2019.1008                1.2019.1008
docker-ce-with-debian-10                 cognosys                          docker-ce-with-debian-10                 cognosys:docker-ce-with-debian-10:docker-ce-with-debian-10:1.2019.0710                                1.2019.0710
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201602010                                                                       8.0.201602010
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201603020                                                                       8.0.201603020
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201604050                                                                       8.0.201604050
...
```


## <a name="look-at-all-available-images"></a>Examinar todas as imagens disponíveis
 
Outra maneira de localizar uma imagem em um local é executar os comandos [az vm image list-publishers](/cli/azure/vm/image), [az vm image list-offers](/cli/azure/vm/image) e [az vm image list-skus](/cli/azure/vm/image) em sequência. Com esses comandos, você determina estes valores:

1. Listar os Publicadores de imagem para um local. Neste exemplo, estamos olhando para a região *oeste dos EUA* .
    
    ```azurecli
    az vm image list-publishers --location westus --output table
    ```

1. Para um determinado editor, liste suas ofertas. Neste exemplo, adicionamos *Canonical* como o Publicador.
    
    ```azurecli
    az vm image list-offers --location westus --publisher Canonical --output table
    ```

1. Para uma determinada oferta, liste seus SKUs. Neste exemplo, adicionamos *UbuntuServer* como a oferta.
    ```azurecli
    az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
    ```

1. Para um determinado Publicador, oferta e SKU, mostre todas as versões da imagem. Neste exemplo, adicionamos *18, 4-LTS* como o SKU.

    ```azurecli
    az vm image list \
        --location westus \
        --publisher Canonical \  
        --offer UbuntuServer \    
        --sku 18.04-LTS \
        --all --output table
    ```

Passe esse valor da coluna URN com o `--image` parâmetro ao criar uma VM com o comando [AZ VM Create](/cli/azure/vm) . Você também pode substituir o número de versão no URN por "mais recente" para simplesmente usar a versão mais recente da imagem. 

Se você implantar uma VM com um modelo do Gerenciador de Recursos, defina os parâmetros de imagem individualmente nas propriedades `imageReference`. Consulte a [referência de modelo](/azure/templates/microsoft.compute/virtualmachines).


## <a name="check-the-purchase-plan-information"></a>Verificar as informações do plano de compra

Algumas imagens de VM no Azure Marketplace têm licenças e termos de compra adicionais que você deve aceitar antes de implantá-las programaticamente.  

Para implantar uma VM de tal imagem, você precisará aceitar os termos da imagem na primeira vez que usá-la, uma vez por assinatura. Você também precisará especificar os parâmetros do *plano de compra* para implantar uma VM dessa imagem

Para exibir as informações do plano de compra de uma imagem, execute o comando [AZ VM Image Show](/cli/azure/image) com o urn da imagem. Se a propriedade `plan` na saída não for `null`, isso significa que a imagem tem termos que você precisa aceitar antes da implantação programática.

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

Executar um comando semelhante para a imagem de RabbitMQ Certified by Bitnami mostra as seguintes `plan` propriedades: `name`, `product` e `publisher`. (Algumas imagens também têm uma propriedade `promotion code`.) 

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

Para implantar essa imagem, você precisa aceitar os termos e fornecer os parâmetros do plano de compra ao implantar uma VM usando essa imagem.

## <a name="accept-the-terms"></a>Aceitar os termos

Para exibir e aceitar os termos de licença, use o comando [az vm image accept-terms](/cli/azure/vm/image/terms). Quando aceita os termos, você habilita a implantação programática na sua assinatura. Você só precisa aceitar os termos uma vez por assinatura para a imagem. Por exemplo:

```azurecli
az vm image terms show --urn bitnami:rabbitmq:rabbitmq:latest
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

Para aceitar os termos, digite:

```azurecli
az vm image terms accept --urn bitnami:rabbitmq:rabbitmq:latest
``` 

## <a name="deploy-a-new-vm-using-the-image-parameters"></a>Implantar uma nova VM usando os parâmetros de imagem

Com informações sobre a imagem, você pode implantá-la usando o `az vm create` comando. 

Para implantar uma imagem que não tem informações de plano, como a imagem mais recente do Ubuntu Server 18, 4 da Canonical, passe o URN para `--image` :

```azurecli-interactive
az group create --name myURNVM --location westus
az vm create \
   --resource-group myURNVM \
   --name myVM \
   --admin-username azureuser \
   --generate-ssh-keys \
   --image Canonical:UbuntuServer:18.04-LTS:latest 
```


Para uma imagem com parâmetros de plano de compra, como a RabbitMQ certificada pela imagem BitNami, você passa o URN para `--image` e também fornece os parâmetros do plano de compra:

```azurecli
az group create --name myPurchasePlanRG --location westus

az vm create \
   --resource-group myPurchasePlanRG \
   --name myVM \
   --admin-username azureuser \
   --generate-ssh-keys \
   --image bitnami:rabbitmq:rabbitmq:latest \
   --plan-name rabbitmq \
   --plan-product rabbitmq \
   --plan-publisher bitnami
```

Se você receber uma mensagem sobre como aceitar os termos da imagem, examine [a seção aceitar os termos](#accept-the-terms). Verifique se a saída de `az vm image accept-terms` retorna o valor `"accepted": true,` mostrando que você aceitou os termos da imagem.


## <a name="using-an-existing-vhd-with-purchase-plan-information"></a>Usando um VHD existente com informações do plano de compra

Se você tiver um VHD existente de uma VM que foi criada usando uma imagem paga do Azure Marketplace, talvez seja necessário fornecer as informações do plano de compra ao criar uma nova VM a partir desse VHD. 

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


## <a name="next-steps"></a>Próximas etapas
Para criar uma máquina virtual rapidamente usando as informações de imagem, consulte [Criar e gerenciar VMs do Linux usando a CLI do Azure](tutorial-manage-vm.md).

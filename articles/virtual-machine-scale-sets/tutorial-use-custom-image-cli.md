---
title: Tutorial – Usar uma imagem de VM personalizada em um conjunto de dimensionamento com a CLI do Azure
description: Aprenda a usar a CLI 2.0 do Azure para criar uma imagem de VM personalizada que pode ser usada para implantar um conjunto de dimensionamento de máquinas virtuais
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: tutorial
ms.date: 05/01/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.reviewer: akjosh
ms.openlocfilehash: a9a4abe550da4f0438f875127b3b689045c06e6f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762984"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-the-azure-cli"></a>Tutorial: Criar e usar uma imagem personalizada para conjuntos de dimensionamento de máquinas virtuais com a CLI do Azure
Ao criar um conjunto de dimensionamento, você especifica uma imagem a ser usada quando as instâncias de VM forem implantadas. Para reduzir a quantidade de tarefas depois que as instâncias de VM forem implantadas, é possível usar uma imagem de VM personalizada. Esta imagem de VM personalizada inclui todas as instalações ou configurações de aplicativo necessárias. Todas as instâncias de VM criadas no conjunto de dimensionamento usam a imagem de VM personalizada e estão prontas para atender ao tráfego do aplicativo. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Criar uma Galeria de Imagens Compartilhadas
> * Criar uma definição de imagem especializada
> * Criar uma versão de imagem
> * Criar um conjunto de dimensionamento com base em uma imagem personalizada
> * Compartilhar uma galeria de imagens


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo exige a versão 2.4.0 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="overview"></a>Visão geral

Uma [Galeria de Imagens Compartilhadas](../virtual-machines/shared-image-galleries.md) simplifica o compartilhamento da imagem personalizada em sua organização. Imagens personalizadas são como imagens do marketplace, mas você mesmo as cria. As imagens personalizadas podem ser usadas para configurações de inicialização como o pré-carregamento de aplicativos, configurações de aplicativos e outras configurações do sistema operacional. 

A Galeria de Imagens Compartilhadas permite que você compartilhe suas imagens de VM personalizadas com outras pessoas. Escolha quais imagens você deseja compartilhar, em quais regiões deseja torná-las disponíveis e com quem deseja compartilhá-las. 

## <a name="create-and-configure-a-source-vm"></a>Criar e configurar uma VM de origem

Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group); em seguida, crie uma VM com [az vm create](/cli/azure/vm). Essa VM é usada como a origem da imagem. O exemplo abaixo inicia uma VM chamada *myVM* no grupo de recursos chamado *myResourceGroup*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image ubuntults \
  --admin-username azureuser \
  --generate-ssh-keys
```

> [!IMPORTANT]
> A **ID** da VM é mostrada na saída do comando [az vm create](/cli/azure/vm). Copie-a em um lugar seguro para usá-la mais tarde neste tutorial.

O endereço IP público da VM também é mostrado na saída do comando [az vm create](/cli/azure/vm). SSH para o endereço IP público da VM da seguinte maneira:

```console
ssh azureuser@<publicIpAddress>
```

Para personalizar sua VM, vamos instalar um servidor Web básico. Quando a instância de VM no conjunto de dimensionamento for implantada, terá todos os pacotes necessários para executar um aplicativo Web. Use `apt-get` para instalar o *NGINX* da seguinte forma:

```bash
sudo apt-get install -y nginx
```

Quando terminar, digite `exit` para desfazer a conexão SSH.

## <a name="create-an-image-gallery"></a>Criar uma galeria de imagens 

Uma galeria de imagens é o principal recurso usado para habilitar o compartilhamento de imagens. 

Caracteres permitidos para o nome da galeria são letras maiúsculas ou minúsculas, dígitos, pontos e pontos finais. O nome da galeria não pode conter traços.   Os nomes das galerias devem ser exclusivos dentro de sua assinatura. 

Criar uma galeria de imagens usando [az sig create](/cli/azure/sig#az_sig_create). O exemplo a seguir cria um grupo de recursos da galeria chamado *myGalleryRG* no *Leste dos EUA*, bem como uma galeria chamada *myGallery*.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Criar uma definição de imagem

As definições de imagem criam um agrupamento lógico para as imagens. Elas são usadas para gerenciar informações sobre as versões da imagem que são criadas dentro delas. 

Os nomes das definições de imagem podem ser compostos por letras maiúsculas ou minúsculas, dígitos, pontos, traços e pontos finais. 

Verifique se a definição da imagem é do tipo correto. Se tiver generalizado a VM (usando o Sysprep para Windows ou waagent -deprovision para Linux), você precisará criar uma definição de imagem generalizada usando `--os-state generalized`. Se quiser usar a VM sem remover contas de usuário existentes, crie uma definição de imagem especializada usando `--os-state specialized`.

Para obter mais informações sobre os valores que pode especificar para uma definição de imagem, confira [Definições de imagem](../virtual-machines/shared-image-galleries.md#image-definitions).

Crie uma definição de imagem na galeria usando [az sig image-definition create](/cli/azure/sig/image-definition#az_sig_image_definition_create).

Neste exemplo, a definição da imagem se chama *myImageDefinition* e é referente a uma imagem [especializada](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) do SO Linux. Para criar uma definição para imagens usando um SO Windows, use `--os-type Windows`. 

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```

> [!IMPORTANT]
> A **ID** da definição da imagem é mostrada na saída do comando. Copie-a em um lugar seguro para usá-la mais tarde neste tutorial.


## <a name="create-the-image-version"></a>Criar a versão da imagem

Crie uma versão da imagem com base na VM usando [az image gallery create-image-version](/cli/azure/sig/image-version#az_sig_image_version_create).  

Caracteres permitidos para a versão da imagem são números e pontos. Os números devem estar dentro do intervalo de um inteiro de 32 bits. Formato: *MajorVersion*.*MinorVersion*.*Patch*.

Neste exemplo, a versão de nossa imagem é *1.0.0* e vamos criar uma réplica na região *Centro-Sul dos EUA* e uma na região *Leste dos EUA 2*. As regiões de replicação precisam incluir a região em que a VM de origem fica localizada.

Substitua o valor de `--managed-image` neste exemplo pela ID da VM da etapa anterior.

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus=1" \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> Você precisa esperar que a versão da imagem seja compilada e replicada completamente antes de poder usar a mesma imagem gerenciada para criar outra versão da imagem.
>
> Você também pode armazenar a imagem no armazenamento Premium adicionando `--storage-account-type  premium_lrs`, ou no [Armazenamento com redundância de zona](../storage/common/storage-redundancy.md) adicionando `--storage-account-type  standard_zrs` ao criar a versão da imagem.
>




## <a name="create-a-scale-set-from-the-image"></a>Criar um conjunto de dimensionamento com base na imagem
Criar um conjunto de dimensionamento com base na uma imagem especializada usando [`az vmss create`](/cli/azure/vmss#az_vmss_create). 

Crie o conjunto de dimensionamento usando [`az vmss create`](/cli/azure/vmss#az_vmss_create) e o parâmetro --specialized para indicar que se trata de uma imagem especializada. 

Use a ID de definição de imagem de `--image` para criar as instâncias do conjunto de dimensionamento com base na versão mais recente da imagem que está disponível. Você também pode criar as instâncias do conjunto de dimensionamento com base em uma versão específica fornecendo a ID de versão da imagem de `--image`. 

Crie um conjunto de dimensionamento chamado *myScaleSet* com base na versão mais recente da imagem *myImageDefinition* que criamos anteriormente.

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --specialized
```

Leva alguns minutos para criar e configurar todos os recursos e as VMs do conjunto de dimensionamento.


## <a name="test-your-scale-set"></a>Testar seu conjunto de dimensionamento
Para permitir que o tráfego alcance o conjunto de dimensionamento e verifique se o servidor Web está funcionando corretamente, crie uma regra de balanceador de carga com [az network lb rule create](/cli/azure/network/lb/rule). O exemplo abaixo cria uma regra denominada *myLoadBalancerRuleWeb* que permite o tráfego na porta *TCP* *80*:

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

Para ver seu conjunto de dimensionamento em ação, obtenha o endereço IP público de seu balanceador de carga com [az network public-ip show](/cli/azure/network/public-ip). O seguinte exemplo obtém o endereço IP de *myScaleSetLBPublicIP*, criado como parte do conjunto de dimensionamento:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Digite o endereço IP público no seu navegador da Web. A página da Web padrão do NGINX é exibida da seguinte forma:

![NGINX sendo executado a partir de uma imagem de VM personalizada](media/tutorial-use-custom-image-cli/default-nginx-website.png)



## <a name="share-the-gallery"></a>Compartilhar a galeria

Você pode compartilhar imagens entre assinaturas usando o Azure RBAC (controle de acesso baseado em função do Azure). Você pode compartilhar imagens na galeria, na definição da imagem ou na versão da imagem. Qualquer usuário que tenha permissões de leitura para uma versão de imagem, mesmo entre assinaturas, poderá implantar uma VM usando a versão da imagem.

Recomendamos que você compartilhe com outros usuários no nível da galeria. Para obter a ID do objeto da galeria, use [az sig show](/cli/azure/sig#az_sig_show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Use a ID do objeto como um escopo, juntamente com um endereço de email e [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) para conceder a um usuário acesso à galeria de imagens compartilhadas. Substitua `<email-address>` e `<gallery iD>` pelas suas informações.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Para obter mais informações sobre como compartilhar recursos usando o Azure RBAC, confira [Adicionar ou remover atribuições de função do Azure usando a CLI do Azure](../role-based-access-control/role-assignments-cli.md).


## <a name="clean-up-resources"></a>Limpar os recursos
Para remover o conjunto de dimensionamento e outros recursos, exclua o grupo de recursos e todos os seus recursos com [az group delete](/cli/azure/group). O parâmetro `--no-wait` retorna o controle ao prompt sem aguardar a conclusão da operação. O parâmetro `--yes` confirma que você deseja excluir os recursos sem um prompt adicional para fazer isso.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu a criar e usar uma imagem de VM personalizada para seus conjuntos de dimensionamento com a CLI 2.0 do Azure:

> [!div class="checklist"]
> * Criar uma Galeria de Imagens Compartilhadas
> * Criar uma definição de imagem especializada
> * Criar uma versão de imagem
> * Criar um conjunto de dimensionamento com base em uma imagem personalizada
> * Compartilhar uma galeria de imagens

Siga para o próximo tutorial para saber como implantar aplicativo ao seu conjunto de dimensionamento.

> [!div class="nextstepaction"]
> [Implantar aplicativos em seus conjuntos de dimensionamento](tutorial-install-apps-cli.md)
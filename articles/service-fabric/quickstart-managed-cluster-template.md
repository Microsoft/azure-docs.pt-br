---
title: Implantar um cluster gerenciado do Service Fabric (versão prévia) usando o Azure Resource Manager
description: Saiba como criar um cluster gerenciado do Service Fabric com um modelo do Azure Resource Manager
ms.topic: quickstart
ms.date: 09/28/2020
ms.custom: references_regions
ms.openlocfilehash: 079e8e3e39bd306332abb43fa2ae1f8cc04e305b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91410371"
---
# <a name="quickstart-deploy-a-service-fabric-managed-cluster-preview-with-an-azure-resource-manager-template"></a>Início Rápido: Implantar um cluster gerenciado do Service Fabric (versão prévia) com um modelo do Azure Resource Manager

Os clusters gerenciados do Service Fabric são uma evolução do modelo de recurso de cluster do Azure Service Fabric que simplifica a sua experiência de implantação e gerenciamento de cluster. Os clusters gerenciados do Service Fabric são um recurso totalmente encapsulado que permite que você implante um recurso de cluster do Service Fabric em vez de ter que implantar todos os recursos subjacentes que compõem um cluster do Service Fabric. Este artigo descreve como implantar um cluster gerenciado do Service Fabric para teste no Azure usando um modelo do ARM (Azure Resource Manager).

O cluster de SKU Básico de três nós implantado neste tutorial destina-se apenas a ser usado para fins de instrução (em vez de para cargas de trabalho de produção). Para obter mais informações, confira [SKUs de cluster gerenciado do Service Fabric](overview-managed-cluster.md#service-fabric-managed-cluster-skus).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este guia de início rápido:

* Se você não tem uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste guia de início rápido é proveniente dos [Exemplos do Azure – Modelos do cluster do Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Basic-SKU-1-NT).

## <a name="create-a-client-certificate"></a>Criar um certificado de cliente

Os clusters gerenciados do Service Fabric usam um certificado de cliente como uma chave para o controle de acesso. Se você já tiver um certificado de cliente que deseja usar para o controle de acesso ao cluster, poderá ignorar esta etapa.

Se você precisar criar um certificado de cliente, siga as etapas em [definir e recuperar um certificado do Azure Key Vault](../key-vault/certificates/quick-create-portal.md).

Anote a impressão digital do certificado, pois ela será necessária para implantar o modelo na próxima etapa.

## <a name="deploy-the-template"></a>Implantar o modelo

1. Selecione a imagem a seguir para entrar no Azure e abrir um modelo.

      [![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fservice-fabric-cluster-templates%2Fmaster%2FSF-Managed-Basic-SKU-1-NT%2Fazuredeploy.json)

2. Selecione ou insira os valores a seguir

    Neste guia de início rápido, forneça os próprios valores para os seguintes parâmetros de modelo:

    * **Assinatura**: Selecione uma assinatura do Azure.
    * **Grupo de Recursos**: Selecione **Criar novo**. Insira um nome exclusivo para o grupo de recursos, como *myResourceGroup*, e escolha **OK**.
    * **Localização**: Selecione uma localização, como **eastus2**. As regiões com suporte para a versão prévia dos clusters gerenciados do Service Fabric incluem `centraluseuap`, `eastus2euap`, `eastasia`, `northeurope`, `westcentralus` e `eastus2`.
    * **Nome do cluster**: Insira um nome exclusivo para o cluster, como *mysfcluster*.
    * **Nome de Usuário do Administrador**: Insira um nome para o administrador a ser usado para o RDP nas VMs subjacentes no cluster.
    * **Senha do Administrador**: Insira uma senha para o administrador a ser usada para o RDP nas VMs subjacentes no cluster.
    * **Impressão Digital do Certificado do Cliente**: Forneça a impressão digital do certificado do cliente que você deseja usar para acessar o cluster. Se você não tiver um certificado, siga [definir e recuperar um certificado](../key-vault/certificates/quick-create-portal.md) para criar um certificado autoassinado.
    * **Nome do Tipo de Nó**: Insira um nome exclusivo para o tipo de nó, como *nt1*.
    * **Concordo com os termos e condições acima**: Marque esta caixa para concordar. 

3. Selecione **Comprar**.

4. Leva alguns minutos para que o cluster gerenciado do Service Fabric seja implantado. Aguarde até que a implantação seja concluída com êxito antes de passar para as próximas etapas.

## <a name="validate-the-deployment"></a>Validar a implantação

### <a name="review-deployed-resources"></a>Examinar os recursos implantados

Quando a implantação for concluída, localize o valor do Service Fabric Explorer na saída e abra o endereço em um navegador da Web para exibir o cluster no Service Fabric Explorer. Quando um certificado for solicitado, use o certificado para o qual a impressão digital do cliente foi fornecida no modelo.

> [!NOTE]
> Você pode encontrar a saída da implantação no Portal do Azure na guia de implantações do grupo de recursos.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, exclua o grupo de recursos do seu cluster gerenciado do Service Fabric. Para excluir o grupo de recursos pelo portal:

1. Insira o nome do grupo de recursos na caixa *Pesquisa* na parte superior do portal. Quando você vir o grupo de recursos usado neste início rápido nos resultados da pesquisa, selecione-o.
2. Selecione **Excluir grupo de recursos**.
3. Na caixa **DIGITE O NOME DO GRUPO DE RECURSOS:** , digite o nome do grupo de recursos e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você implantou um cluster gerenciado do Service Fabric. Para saber mais sobre como escalar um cluster, confira:

> [!div class="nextstepaction"]
> [Escalar horizontalmente um cluster gerenciado do Service Fabric](tutorial-managed-cluster-scale.md)

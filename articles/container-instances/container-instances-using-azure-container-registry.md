---
title: "Implantar do Registro de Contêiner do Azure em Instâncias de Contêiner do Azure | Azure Docs"
description: "Implantar do Registro de Contêiner do Azure em Instâncias de Contêiner do Azure"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/02/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: aa1c4ea379c10dff246e2f924a345f9fa444aa64
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-to-azure-container-instances-from-the-azure-container-registry"></a>Implantar do Registro de Contêiner do Azure em Instâncias de Contêiner do Azure

O Registro de Contêiner do Azure é um registro privado baseado no Azure para imagens de contêiner do Docker. Este artigo aborda como implantar imagens de contêiner armazenadas no Registro de Contêiner do Azure em Instâncias de Contêiner do Azure.

## <a name="using-the-azure-cli"></a>Usando a CLI do Azure

A CLI do Azure inclui comandos para criar e gerenciar contêineres nas Instâncias de Contêiner do Azure. Se você especificar uma imagem privada no comando `create`, também poderá especificar a senha do registro da imagem, necessária para autenticar com o registro de contêiner.

```azurecli-interactive
az container create --name myprivatecontainer --image mycontainerregistry.azurecr.io/mycontainerimage:v1 --registry-password myRegistryPassword --resource-group myresourcegroup
```

O comando `create` também dá suporte à especificação de `registry-login-server` e `registry-username`. No entanto, o servidor de logon do Registro de Contêiner do Azure é sempre *registryname*.azurecr.io e o nome de usuário é *registryname*, portanto, esses valores são inferidos do nome da imagem, caso não sejam explicitamente fornecidos.

## <a name="using-an-azure-resource-manager-template"></a>Usando um modelo do Azure Resource Manager

Você pode especificar as propriedades do Registro de Contêiner do Azure em um modelo do Azure Resource Manager incluindo a propriedade `imageRegistryCredentials` na definição de grupos do contêiner:

```json
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

Para evitar o armazenamento da senha do registro de contêiner diretamente no modelo, é recomendável que você armazene-a como um segredo no [Azure Key Vault](../key-vault/key-vault-manage-with-cli2.md) e referencie-a no modelo, usando a [integração nativa entre o Azure Resource Manager e o Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md).

## <a name="using-the-azure-portal"></a>Usando o portal do Azure

Se você mantiver as imagens de contêiner no Registro de Contêiner do Azure, você poderá facilmente criar um contêiner em Instâncias de Contêiner do Azure usando o Portal do Azure.

1. No Portal do Azure, navegue até o registro de contêiner.

2. Escolha Repositórios.

    ![O menu Registro de Contêiner do Azure no Portal do Azure][acr-menu]

3. Escolha o repositório do qual você deseja implantar.

4. Clique com o botão direito do mouse na marca para a imagem de contêiner que você deseja implantar.

    ![Menu de contexto para iniciar o contêiner com Instâncias de Contêiner do Azure][acr-runinstance-contextmenu]

5. Insira um nome para o contêiner e um nome para o grupo de recursos. Você também poderá alterar os valores padrão se desejar.

    ![Criar menu para Instâncias de Contêiner do Azure][acr-create-deeplink]

6. Quando a implantação for concluída, você poderá navegar para o grupo de contêineres do painel de notificações para localizar o endereço IP e outras propriedades do contêiner.

    ![Exibição de detalhes de grupo de contêineres das Instâncias de Contêiner do Azure][aci-detailsview]

## <a name="next-steps"></a>Próximas etapas

Saiba como criar contêineres, enviá-los por push a um registro de contêiner privado e implantá-los em Instâncias de Contêiner do Azure ao [concluir o tutorial](container-instances-tutorial-prepare-app.md).

<!-- IMAGES -->
[acr-menu]: ./media/container-instances-using-azure-container-registry/acr-menu.png

[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png

[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png

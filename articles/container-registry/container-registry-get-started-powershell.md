---
title: Início Rápido – criar um registro particular do Docker no Azure – PowerShell
description: Aprenda rapidamente a criar um registro de contêiner particular do Docker no Azure com o PowerShell.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: f99b4ee6dd11a109d1c563c84debc2157cb03337
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309490"
---
# <a name="quickstart-create-a-private-container-registry-using-azure-powershell"></a>Início Rápido: Criar um registro de contêiner privado do Docker usando o Azure PowerShell

O Registro de Contêiner do Azure é um serviço de registro de contêiner do Docker privado e gerenciado para gerar, armazenar e fornecer imagens de contêiner de Docker. Neste início rápido, você aprende a criar um registro de contêiner do Azure usando o PowerShell. Em seguida, use os comandos do Docker para efetuar push de uma imagem de contêiner no Registro e, por fim, efetuar pull e executar a imagem do seu Registro.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este início rápido requer o módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para determinar a versão instalada. Se você precisar instalá-lo ou atualizá-lo, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps).

Você também deve ter o Docker instalado localmente. O Docker fornece pacotes para sistemas [macOS][docker-mac], [Windows][docker-windows] e [Linux][docker-linux].

Como o Azure Cloud Shell não inclui todos os componentes de Docker necessários (o daemon `dockerd`), você não pode usar o Cloud Shell para este guia de início rápido.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre em sua assinatura do Azure com o comando [Connect-AzAccount][Connect-AzAccount] e siga as instruções na tela.

```powershell
Connect-AzAccount
```

## <a name="create-resource-group"></a>Criar grupo de recursos

Depois que você estiver autenticado com o Azure, crie um grupo de recursos com [New-AzResourceGroup][New-AzResourceGroup]. Um grupo de recursos é um contêiner lógico no qual você implanta e gerenciar os recursos do Azure.

```powershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>Criar um registro de contêiner

Em seguida, crie um registro de contêiner em seu novo grupo de recursos com o comando [New-AzContainerRegistry][New-AzContainerRegistry].

O nome do registro deve ser exclusivo no Azure e conter de 5 a 50 caracteres alfanuméricos. O exemplo a seguir cria um registro chamado "myContainerRegistry007". Substitua *myContainerRegistry007* no comando a seguir e execute-o para criar o registro:

```powershell
$registry = New-AzContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

Neste início rápido você criará um Registro *Básico*, que é uma opção com otimização de custo para desenvolvedores que estão aprendendo sobre o Registro de Contêiner do Azure. Para obter detalhes sobre as camadas de serviço disponíveis, confira [SKUs de registro de contêiner][container-registry-skus].

## <a name="log-in-to-registry"></a>Fazer logon no registro

Antes de efetuar push e pull nas imagens de contêiner, você deverá fazer logon no seu registro. Em cenários de produção, você deve usar uma identidade individual ou a entidade de serviço para acesso de registro de contêiner, porém, para manter este início rápido breve, habilite o usuário administrador no seu registro com o comando [Get-AzContainerRegistryCredential][Get-AzContainerRegistryCredential]:

```powershell
$creds = Get-AzContainerRegistryCredential -Registry $registry
```

Em seguida, execute [docker login][docker-login] para fazer logon:

```powershell
$creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
```

O comando retorna `Login Succeeded` na conclusão.

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar de trabalhar com os recursos criados neste guia de início rápido, use o comando [Remove-AzResourceGroup][Remove-AzResourceGroup] para remover o grupo de recursos, o registro de contêiner e as imagens de contêiner armazenadas lá:

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um Registro de Contêiner do Azure com o Azure PowerShell, efetuou push de uma imagem de contêiner e extraiu e executou a imagem do Registro. Prossiga para os tutoriais de Registro de Contêiner do Azure para uma análise mais profunda do ACR.

> [!div class="nextstepaction"]
> [Tutoriais de Registro de Contêiner do Azure][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Connect-AzAccount]: /powershell/module/az.accounts/connect-azaccount
[Get-AzContainerRegistryCredential]: /powershell/module/az.containerregistry/get-azcontainerregistrycredential
[Get-Module]: /powershell/module/microsoft.powershell.core/get-module
[New-AzContainerRegistry]: /powershell/module/az.containerregistry/New-AzContainerRegistry
[New-AzResourceGroup]: /powershell/module/az.resources/new-azresourcegroup
[Remove-AzResourceGroup]: /powershell/module/az.resources/remove-azresourcegroup
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md

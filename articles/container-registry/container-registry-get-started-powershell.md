---
title: Início Rápido – Criar registro – PowerShell
description: Aprenda rapidamente a criar um registro de Docker privado no Registro de Contêiner do Azure com o PowerShell
ms.date: 01/22/2019
ms.topic: quickstart
ms.custom:
- mvc
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: bd9b93e22081c43dfa3fd934f13da3713120aadb
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537385"
---
# <a name="quickstart-create-a-private-container-registry-using-azure-powershell"></a>Início Rápido: Criar um registro de contêiner privado do Docker usando o Azure PowerShell

O Registro de Contêiner do Azure é um serviço de registro de contêiner do Docker privado e gerenciado para gerar, armazenar e fornecer imagens de contêiner de Docker. Neste início rápido, você aprende a criar um registro de contêiner do Azure usando o PowerShell. Em seguida, use os comandos do Docker para efetuar push de uma imagem de contêiner no Registro e, por fim, efetuar pull e executar a imagem do seu Registro.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este início rápido requer o módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para determinar a versão instalada. Se você precisar instalá-lo ou atualizá-lo, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps).

Você também deve ter o Docker instalado localmente. O docker fornece pacotes para sistemas [macOS][docker-mac], [Windows][docker-windows][Linux][docker-linux].

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

Neste início rápido você criará um Registro *Básico*, que é uma opção com otimização de custo para desenvolvedores que estão aprendendo sobre o Registro de Contêiner do Azure. Para obter detalhes sobre as camadas de serviço disponíveis, confira [Camadas de serviço do registro de contêiner][container-registry-skus].

## <a name="log-in-to-registry"></a>Fazer logon no registro

Antes de efetuar push e pull nas imagens de contêiner, você deverá fazer logon no seu registro. Para que este guia de início rápido seja breve, habilite o usuário administrador no Registro com o comando [Get-AzContainerRegistryCredential][Get-AzContainerRegistryCredential]. Em cenários de produção, você deve usar um [método de autenticação](container-registry-authentication.md) alternativo para o acesso ao Registro, como uma entidade de serviço. 

```powershell
$creds = Get-AzContainerRegistryCredential -Registry $registry
```

Em seguida, execute [docker login][docker-login] para fazer logon:

```powershell
$creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
```

O comando retorna `Login Succeeded` na conclusão.

> [!TIP]
> A CLI do Azure fornece o comando `az acr login`, uma forma conveniente de fazer logon em um registro de contêiner usando sua [identidade individual](container-registry-authentication.md#individual-login-with-azure-ad), sem transmitir as credenciais do Docker.


[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar de trabalhar com os recursos criados neste guia de início rápido, use o comando [Remove-AzResourceGroup][Remove-AzResourceGroup] para remover o grupo de recursos, o registro de contêiner e as imagens de contêiner armazenadas lá:

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um Registro de Contêiner do Azure com o Azure PowerShell, efetuou push de uma imagem de contêiner e extraiu e executou a imagem do Registro. Prossiga para os tutoriais de Registro de Contêiner do Azure para uma análise mais profunda do ACR.

> [!div class="nextstepaction"]
> [Tutoriais de Registro de Contêiner do Azure][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Tutoriais das Tarefas do Registro de Contêiner do Azure][container-registry-tutorial-quick-task]

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
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md

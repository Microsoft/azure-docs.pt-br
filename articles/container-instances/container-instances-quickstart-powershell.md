---
title: Início Rápido – implantar um contêiner do Docker em Instâncias de Contêiner do Azure – PowerShell
description: Neste início rápido, você usa o Azure PowerShell para implantar rapidamente um aplicativo Web em contêineres que é executado em uma instância de contêiner do Azure isolada
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 7fe199d2ac228ddb0ccfd1e5bc980e680e160acf
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325836"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-azure-powershell"></a>Início Rápido: Implantar uma instância de contêiner no Azure usando o Azure PowerShell

Use as Instâncias de Contêiner do Azure para executar contêineres do Docker sem servidor no Azure de maneira simples e rápida. Implante um aplicativo em uma instância de contêiner sob demanda quando você não precisa de uma plataforma de orquestração de contêiner completa como o Serviço de Kubernetes do Azure.

Neste início rápido, você usará o Azure PowerShell para implantar um contêiner do Windows isolado e disponibilizar o respectivo aplicativo com um FQDN (nome de domínio totalmente qualificado). Alguns segundos depois de executar um comando único de implantação, é possível navegar para o aplicativo em execução no contêiner:

![Aplicativos implantados nas Instâncias de Contêiner do Azure exibidos no navegador][qs-powershell-01]

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o módulo do Azure PowerShell. Execute `Get-Module -ListAvailable Az` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Instâncias de Contêiner do Azure, assim como todos os recursos do Azure, precisam ser implantadas em um grupo de recursos. Os grupos de recursos facilitam organizar e gerenciar os recursos relacionados ao Azure.

Primeiramente, crie um grupo de recursos denominado *myResourceGroup* no local *eastus* com o seguinte comando [New-AzResourceGroup][New-AzResourceGroup]:

 ```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>Criar um contêiner

Agora que tem um grupo de recursos, você pode executar um contêiner no Azure. Para criar uma instância de contêiner no Azure PowerShell, forneça um nome do grupo de recursos, o nome da instância de contêiner e a imagem de contêiner do Docker ao cmdlet [New-AzContainerGroup][New-AzContainerGroup]. Neste início rápido, você usa a imagem pública `mcr.microsoft.com/windows/servercore/iis:nanoserver`. Esta imagem de pacotes do Microsoft IIS (Serviços de Informações da Internet) para executar no Nano Server.

Você pode expor seus contêineres à Internet especificando um ou mais portas a serem abertas, um rótulo de nome DNS ou ambos. Neste início rápido, você implanta um contêiner com um rótulo de nome DNS para que o IIS fique publicamente acessível.

Execute um comando similar ao seguinte para iniciar uma instância de contêiner. Defina um valor de `-DnsNameLabel` que seja exclusivo na região do Azure em que você criará a instância. Se você receber uma mensagem de erro “Rótulo de nome DNS não disponível”, tente usar um rótulo de nome DNS diferente.

 ```azurepowershell-interactive
New-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image mcr.microsoft.com/windows/servercore/iis:nanoserver -OsType Windows -DnsNameLabel aci-demo-win
```

Em alguns segundos, você deverá receber uma resposta do Azure. O `ProvisioningState` do contêiner inicialmente tem o status **Criando**, mas deve mudar para **Bem-sucedido** dentro de um ou dois minutos. Verifique o status de implantação usando o cmdlet [Get-AzContainerGroup][Get-AzContainerGroup]:

 ```azurepowershell-interactive
Get-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

O estado de provisionamento, o FQDN (nome de domínio totalmente qualificado) e o endereço IP do contêiner aparecem na saída do cmdlet:

```console
PS Azure:\> Get-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer


ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials :
RestartPolicy            : Always
IpAddress                : 52.226.19.87
DnsNameLabel             : aci-demo-win
Fqdn                     : aci-demo-win.eastus.azurecontainer.io
Ports                    : {80}
OsType                   : Windows
Volumes                  :
State                    : Pending
Events                   : {}
```

Quando o `ProvisioningState` do contêiner for **Êxito**, navegue até o `Fqdn` dele no seu navegador. Se você encontrar uma página da Web semelhante à seguinte, parabéns! Você implantou com êxito um aplicativo em execução em um contêiner do Docker no Azure.

![IIS implantado usando as Instâncias de Contêiner do Azure exibido no navegador][qs-powershell-01]

## <a name="clean-up-resources"></a>Limpar recursos

Ao concluir o contêiner, remova-o usando o cmdlet [Remove-AzContainerGroup][Remove-AzContainerGroup]:

 ```azurepowershell-interactive
Remove-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou uma instância de Contêiner do Azure com base em uma imagem em no registro do Hub do Docker público. Se você quiser criar uma imagem de contêiner e implantá-la usando um Registro de Contêiner do Azure privado, prossiga para o tutorial das Instâncias de Contêiner do Azure.

> [!div class="nextstepaction"]
> [Tutorial sobre Instâncias de Contêiner do Azure](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png

<!-- LINKS -->
[New-AzResourceGroup]: /powershell/module/az.resources/new-Azresourcegroup
[New-AzContainerGroup]: /powershell/module/az.containerinstance/new-Azcontainergroup
[Get-AzContainerGroup]: /powershell/module/az.containerinstance/get-Azcontainergroup
[Remove-AzContainerGroup]: /powershell/module/az.containerinstance/remove-Azcontainergroup

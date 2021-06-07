---
title: Criar um membro do Azure Blockchain Service – Azure PowerShell
description: Crie um membro do Azure Blockchain Service para um consórcio do blockchain usando o Azure PowerShell.
ms.reviewer: ravastra
ms.date: 9/22/2020
ms.topic: quickstart
ms.custom:
- references_regions
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 9087bda9c1979a98f4bbf9f8343d012c4cf3098c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529628"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-powershell"></a>Início Rápido: criar um membro do blockchain do Azure Blockchain Service usando o Azure PowerShell

Neste início rápido, você implanta um novo membro do blockchain e consórcio no Azure Blockchain Service usando o Azure PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Se você optar por usar o PowerShell localmente, este artigo exigirá que você instale o módulo Az PowerShell e conecte-se à sua conta do Azure usando o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Para obter mais informações sobre como instalar o módulo Az PowerShell, confira [Instalar o Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Enquanto o módulo **Az.Blockchain** do PowerShell está na versão prévia, você precisa instalá-lo separadamente do módulo Az do PowerShell usando o cmdlet `Install-Module`. Quando esse módulo do PowerShell estiver em disponibilidade geral, ele passará a fazer parte das versões futuras do módulo Az PowerShell e estará disponível nativamente no Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.Blockchain
```

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

## <a name="register-resource-provider"></a>Registrar provedor de recursos

Se esta for a primeira vez que você usa o Azure Blockchain Service, será necessário registrar o provedor de recursos **Microsoft.Blockchain**.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Blockchain
```

## <a name="choose-a-specific-azure-subscription"></a>Escolher uma assinatura específica do Azure

Se tiver várias assinaturas do Azure, escolha a que for adequada para cobrança do recurso. Selecione uma assinatura específica usando o cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="define-variables"></a>Definir variáveis

Você usará várias informações repetidamente. Crie variáveis para armazenar as informações.

```azurepowershell-interactive
# Name of resource group used throughout this article
$resourceGroupName = 'myResourceGroup'

# Azure region
$location = 'eastus'
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um [grupo de recursos do Azure](../../azure-resource-manager/management/overview.md) usando o cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados como um grupo.

O exemplo a seguir cria um grupo de recursos com base no nome na variável `$resourceGroupName` na região especificada na variável `$location`.

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
```

## <a name="create-a-blockchain-member"></a>Criar um membro do blockchain

Um membro do Azure Blockchain Service é um nó de blockchain em uma rede privada de blockchain de consórcio.
Ao provisionar um membro, você pode criar uma rede de consórcio ou ingressar nela. Você precisará de, pelo menos, um membro para uma rede de consórcio. O número de membros de blockchain necessários para os participantes depende do cenário. Os participantes do consórcio podem ter um ou mais membros de blockchain ou podem compartilhar membros com outros participantes. Para obter mais informações sobre os consórcios, confira [Consórcio do Azure Blockchain Service](consortium.md).

Há vários parâmetros e propriedades que você precisará passar. Substitua os parâmetros de exemplo por seus valores.

```azurepowershell-interactive
$passwd = Read-Host -Prompt 'Enter the members default transaction node password' -AsSecureString
$csPasswd = Read-Host -Prompt 'Enter  the consortium account password' -AsSecureString

$memberParams = @{
  Name = 'myblockchainmember'
  ResourceGroupName = $resourceGroupName
  Consortium = 'myconsortium'
  ConsortiumManagementAccountPassword = $csPasswd
  Location = $location
  Password = $passwd
  Protocol = 'Quorum'
  Sku = 'S0'
}
New-AzBlockchainMember @memberParams
```

| Parâmetro | Descrição |
|---------|-------------|
| **ResourceGroupName** | Nome do grupo de recursos no qual os recursos do serviço Azure Blockchain são criados. Use o grupo de recursos criado na seção anterior.
| **Nome** | Um nome exclusivo que identifica o membro do blockchain do serviço Azure Blockchain. O nome é usado para o endereço do ponto de extremidade público. Por exemplo, `myblockchainmember.blockchain.azure.com`.
| **Localidade** | Região do Azure em que o membro do blockchain é criado. Por exemplo, `westus2`. Escolha o local mais próximo para os usuários ou para outros aplicativos do Azure. Os recursos podem não estar disponíveis em algumas regiões. O Gerenciador de Dados do Azure Blockchain está disponível atualmente nas seguintes regiões do Azure: Leste dos EUA e Europa Ocidental.
| **Senha** | A senha do nó de transação padrão do membro. Use a senha para a autenticação básica ao se conectar ao ponto de extremidade público do nó de transação padrão do membro do blockchain.
| **Protocolo** | Protocolo do Blockchain. No momento, há suporte para o protocolo _Quorum_.
| **Consórcio** | Nome do consórcio a ser ingressado ou criado. Para obter mais informações sobre os consórcios, confira [Consórcio do Azure Blockchain Service](consortium.md).
| **ConsortiumManagementAccountPassword** | A senha da conta do consórcio também é conhecida como a senha da conta do membro. A senha da conta do membro é usada para criptografar a chave privada para a conta do Ethereum criada para o membro. Use a conta do membro e a senha da conta do membro para o gerenciamento do consórcio.
| **Sku** | Tipo de camada. **S0** para padrão ou **B0** para básico. Use a camada _Basic_ para desenvolvimento, teste e prova de conceitos. Use a camada _Standard_ para implantações de nível de produção. Você também deverá usar o nível _Standard_ se estiver usando o Gerenciador de Dados do Blockchain ou enviando um alto volume de transações particulares. Não há suporte para a alteração do tipo de preço entre Básico e Standard após a criação do membro.

São necessários cerca de 10 minutos para criar o membro do blockchain e os recursos de suporte.

## <a name="clean-up-resources"></a>Limpar os recursos

Você pode usar o membro do blockchain criado para o próximo Início Rápido ou tutorial. Quando eles não forem mais necessários, você poderá excluir os recursos excluindo o grupo de recursos `myResourceGroup` criado para o início rápido.

> [!CAUTION]
> O exemplo a seguir exclui o grupo de recursos especificado e todos os recursos contidos nele.
> Se existirem recursos fora do escopo deste artigo no grupo de recursos especificado, eles também serão excluídos.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você implantou um membro do Azure Blockchain Service e um novo consórcio. Experimente o próximo início rápido para usar o Azure Blockchain Development Kit for Ethereum para fazer a anexação a um membro do Azure Blockchain Service.

> [!div class="nextstepaction"]
> [Usar o Visual Studio Code para se conectar ao Azure Blockchain Service](connect-vscode.md)

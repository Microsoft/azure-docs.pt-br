---
title: Gerenciar Membros em um consórcio de serviço Blockchain do Azure usando Azure PowerShell
description: Saiba como gerenciar membros do Azure Blockchain Service Consortium usando Azure PowerShell.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/10/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 9f76597a91c0e22f57d1ba66ff1a16eea9002af0
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68250084"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-by-using-powershell"></a>Gerenciar membros do Consortium no serviço Blockchain do Azure usando o PowerShell

Você pode usar o PowerShell para gerenciar membros do blockchain Consortium para o serviço Blockchain do Azure. Os membros que têm privilégios de administrador podem convidar, adicionar, remover e alterar funções para todos os participantes no blockchain Consortium. Os membros que têm privilégios de usuário podem exibir todos os participantes no blockchain Consortium e alterar o nome de exibição do membro.

## <a name="prerequisites"></a>Pré-requisitos

* Crie um membro blockchain usando o [portal do Azure](create-member.md).
* Para obter mais informações sobre consortia, membros e nós, consulte [Azure Blockchain Service Consortium](consortium.md).

## <a name="open-azure-cloud-shell"></a>Abrir o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta.

Você também pode abrir Cloud Shell em uma guia separada do navegador acessando [shell.Azure.com/PowerShell](https://shell.azure.com/powershell). Selecione **copiar** para copiar os blocos de código, Cole-o em Cloud Shell e selecione **Enter** para executá-lo.

## <a name="install-the-powershell-module"></a>Instalar o módulo do PowerShell

Instale o pacote Microsoft.AzureBlockchainService.ConsortiumManagement.PS do Galeria do PowerShell.

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>Definir a preferência de informações

Você pode obter mais informações ao executar os cmdlets definindo a variável de preferência de informações. Por padrão, *$InformationPreference* é definido como *SilentlyContinue*.

Para obter informações mais detalhadas dos cmdlets, defina a preferência no PowerShell da seguinte maneira:

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>Estabelecer uma conexão Web3

Para gerenciar membros do Consortium, estabeleça uma conexão Web3 com o ponto de extremidade de membro do serviço do Blockchain. Você pode usar esse script para definir variáveis globais para chamar os cmdlets de gerenciamento do consórcio.

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

*Substitua\<asenha\> da conta de membro* pela senha da conta de membro que você usou quando criou o membro.

Localize os outros valores no portal do Azure:

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Vá para a página de **visão geral** do membro do serviço Blockchain padrão.

    ![Visão geral do membro](./media/manage-consortium-powershell/member-overview.png)

    *Substitua\<conta\> de membro* e *endereçoRootContract\> pelos valores do Portal. \<*

1. Para o endereço do ponto de extremidade, selecione **nós de transação**e, em seguida, selecione o **nó de transação padrão**. O nó padrão tem o mesmo nome que o membro blockchain.
1. Selecione **Cadeias de conexão**.

    ![Cadeias de conexão](./media/manage-consortium-powershell/connection-strings.png)

    *Substitua\<endereço\> do ponto de extremidade* pelo valor de **https (tecla de acesso 1)** ou **https (chave de acesso 2)** .

## <a name="manage-the-network-and-smart-contracts"></a>Gerenciar a rede e os contratos inteligentes

Use os cmdlets de rede e de contrato inteligente para estabelecer uma conexão com os contratos inteligentes do ponto de extremidade do blockchain responsáveis pelo gerenciamento do consórcio.

### <a name="import-consortiummanagementcontracts"></a>Import-ConsortiumManagementContracts

Use este cmdlet para se conectar aos contratos inteligentes do gerenciamento do consórcio. Esses contratos são usados para gerenciar e impor Membros no consórcio.

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| Parâmetro | DESCRIÇÃO | Obrigatório |
|-----------|-------------|:--------:|
| RootContractAddress | Endereço do contrato raiz dos contratos inteligentes de gerenciamento do consórcio | Sim |
| Web3Client | Objeto Web3Client obtido de New-Web3Connection | Sim |

#### <a name="example"></a>Exemplo

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Importar-Web3Account

Use este cmdlet para criar um objeto para armazenar as informações da conta de gerenciamento de um nó remoto.

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| Parâmetro | DESCRIÇÃO | Obrigatório |
|-----------|-------------|:--------:|
| ManagedAccountAddress | Endereço de conta do membro Blockchain | Sim |
| ManagedAccountPassword | Senha do endereço da conta | Sim |

#### <a name="example"></a>Exemplo

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>New-Web3Connection

Use este cmdlet para estabelecer uma conexão com o ponto de extremidade RPC de um nó de transação.

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| Parâmetro | DESCRIÇÃO | Obrigatório |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | Endereço do ponto de extremidade do membro Blockchain | Sim |

#### <a name="example"></a>Exemplo

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>Gerenciar os membros do Consortium

Use os cmdlets de gerenciamento de membros do consórcio para gerenciar Membros no consórcio. As ações disponíveis dependem da sua função de consórcio.

### <a name="get-blockchainmember"></a>Get-BlockchainMember

Use este cmdlet para obter detalhes do membro ou membros da lista do consórcio.

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| Parâmetro | DESCRIÇÃO | Obrigatório |
|-----------|-------------|:--------:|
| Name | O nome do membro do serviço Blockchain para o qual você deseja recuperar detalhes. Quando um nome é inserido, ele retorna os detalhes do membro. Quando um nome é omitido, ele retorna uma lista de todos os membros do consórcio. | Não |
| Members | Objeto Members obtido de Import-ConsortiumManagementContracts | Sim |
| Web3Client | Objeto Web3Client obtido de New-Web3Connection | Sim |

#### <a name="example"></a>Exemplo

```powershell-interactive
$ContractConnection | Get-BlockchainMember -Name <Member Name>
```

#### <a name="example-output"></a>Saída de exemplo

```
Name           : myblockchainmember
CorrelationId  : 0
DisplayName    : myCompany
SubscriptionId : <Azure subscription ID>
AccountAddress : 0x85b911c9e103d6405573151258d668479e9ebeef
Role           : ADMIN
```

### <a name="remove-blockchainmember"></a>Remove-BlockchainMember

Use este cmdlet para remover um membro blockchain.

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Parâmetro | DESCRIÇÃO | Obrigatório |
|-----------|-------------|:--------:|
| Name | Nome do membro a ser removido | Sim |
| Members | Objeto Members obtido de Import-ConsortiumManagementContracts | Sim |
| Web3Account | Objeto Web3Account obtido de Import-Web3Account | Sim |
| Web3Client | Objeto Web3Client obtido de New-Web3Connection | Sim |

#### <a name="example"></a>Exemplo

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

Use este cmdlet para definir atributos de membro blockchain, incluindo o nome de exibição e a função do consórcio.

Os administradores do Consortium podem definir **DisplayName** e **role** para todos os membros. Um membro do consórcio com a função de usuário pode alterar somente o nome de exibição de seu próprio membro.

```
Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| Parâmetro | DESCRIÇÃO | Obrigatório |
|-----------|-------------|:--------:|
| Name | Nome do membro blockchain | Sim |
| DisplayName | Novo nome de exibição | Não |
| AccountAddress | Endereço da conta | Não |
| Members | Objeto Members obtido de Import-ConsortiumManagementContracts | Sim |
| Web3Account | Objeto Web3Account obtido de Import-Web3Account | Sim |
| Web3Client |  Objeto Web3Client obtido de New-Web3Connection| Sim |

#### <a name="example"></a>Exemplo

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>Gerenciar os convites dos membros do consórcio

Use os cmdlets de gerenciamento de convite para membro do consórcio para gerenciar os convites dos membros do consórcio. As ações disponíveis dependem da sua função de consórcio.

### <a name="new-blockchainmemberinvitation"></a>New-BlockchainMemberInvitation

Use este cmdlet para convidar novos membros para o consórcio.

```
New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parâmetro | DESCRIÇÃO | Obrigatório |
|-----------|-------------|:--------:|
| SubscriptionId | ID da assinatura do Azure do membro a ser convidado | Sim |
| Role | A função de consórcio. Os valores podem ser ADMIN ou USER. ADMIN é a função de administrador do consórcio. O usuário é a função de membro do consórcio. | Sim |
| Members | Objeto Members obtido de Import-ConsortiumManagementContracts | Sim |
| Web3Account | Objeto Web3Account obtido de Import-Web3Account | Sim |
| Web3Client | Objeto Web3Client obtido de New-Web3Connection | Sim |

#### <a name="example"></a>Exemplo

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

Use este cmdlet para recuperar ou listar o status de convite de um membro do consórcio.

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| Parâmetro | DESCRIÇÃO | Obrigatório |
|-----------|-------------|:--------:|
| SubscriptionId | A ID da assinatura do Azure do membro a ser convidado. Se a ID da assinatura for fornecida, ela retornará os detalhes do convite da ID da assinatura. Se a ID da assinatura for omitida, ela retornará uma lista de todos os convites para membros. | Não |
| Members | Objeto Members obtido de Import-ConsortiumManagementContracts | Sim |
| Web3Client | Objeto Web3Client obtido de New-Web3Connection | Sim |

#### <a name="example"></a>Exemplo

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation – SubscriptionId <Azure subscription ID>
```

#### <a name="example-output"></a>Saída de exemplo

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Remove-BlockchainMemberInvitation

Use este cmdlet para revogar o convite de um membro do consórcio.

```
Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>
```

| Parâmetro | DESCRIÇÃO | Obrigatório |
|-----------|-------------|:--------:|
| SubscriptionId | ID da assinatura do Azure do membro a revogar | Sim |
| Members | Objeto Members obtido de Import-ConsortiumManagementContracts | Sim |
| Web3Account | Objeto Web3Account obtido de Import-Web3Account | Sim |
| Web3Client | Objeto Web3Client obtido de New-Web3Connection | Sim |

#### <a name="example"></a>Exemplo

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

Use este cmdlet para definir a **função** de um convite existente. Somente administradores do Consortium podem alterar convites.

```
Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parâmetro | DESCRIÇÃO | Obrigatório |
|-----------|-------------|:--------:|
| SubscriptionId | ID da assinatura do Azure do membro a ser convidado | Sim |
| Role | Nova função de consórcio para o convite. Os valores podem ser **usuário** ou **administrador**. | Sim |
| Members |  Objeto Members obtido de Import-ConsortiumManagementContracts | Sim |
| Web3Account | Objeto Web3Account obtido de Import-Web3Account | Sim |
| Web3Client | Objeto Web3Client obtido de New-Web3Connection | Sim |

#### <a name="example"></a>Exemplo

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre consortia, membros e nós, consulte:

> [!div class="nextstepaction"]
> [Consórcio do serviço Blockchain do Azure](consortium.md)

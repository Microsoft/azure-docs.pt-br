---
title: Criar e recuperar atributos de uma chave gerenciada no Azure Key Vault – Azure PowerShell
description: Guia de Início Rápido que mostra como definir e recuperar uma chave gerenciada do Azure Key Vault usando o Azure PowerShell
services: key-vault
author: msmbaldwin
ms.author: mbaldwin
ms.date: 01/26/2021
ms.topic: quickstart
ms.service: key-vault
ms.subservice: keys
tags:
- azure-resource-manager
ms.custom:
- mode-api
ms.openlocfilehash: ba1cd8d6b1410be30eefe9dca9675daaf6c16256
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534669"
---
# <a name="quickstart-set-and-retrieve-a-managed-key-from-azure-key-vault-using-powershell"></a>Início rápido: Definir e recuperar uma chave gerenciada do Azure Key Vault usando o PowerShell

Neste início rápido, você criará um cofre de chaves no Azure Key Vault com o Azure PowerShell. O Azure Key Vault é um serviço de nuvem que funciona como um repositório de segredos seguro. Você pode armazenar chaves, senhas, certificados e outros segredos com segurança. Para saber mais sobre o Key Vault, reveja a [Visão geral](../general/overview.md). O Azure PowerShell é usado para criar e gerenciar recursos do Azure usando comandos ou scripts. Depois de concluir isso, você armazenará uma chave.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o módulo do Azure PowerShell versão 1.0.0 ou posterior. Digite `$PSVersionTable.PSVersion` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Login-AzAccount` para criar uma conexão com o Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Use o cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) do Azure PowerShell para criar um grupo de recursos chamado *myResourceGroup* na localização *westus*. 

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "WestUS"

## Get your principal ID

To create a managed HSM, you will need your Azure Active Directory principal ID.  To obtain your ID, use the Azure PowerShell [Get-AzADUser](/powershell/module/az.resources/get-azaduser) cmdlet, passing your email address to the "UserPrincipalName" parameter:

```azurepowershell-interactive
Get-AzADUser -UserPrincipalName "<your@email.address>"
```

Sua ID da entidade de segurança será retornada no formato "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx".

## <a name="create-a-managed-hsm"></a>Criar um HSM gerenciado

Use o cmdlet [New-AzKeyVaultManagedHsm](/powershell/module/az.keyvault/new-azkeyvaultmanagedhsm) do Azure PowerShell para criar um HSM gerenciado do Key Vault. Você precisará fornecer algumas informações:

- Nome do HSM gerenciado: uma cadeia de 3 a 24 caracteres contendo somente números (0-9), letras (a-z, A-Z) e hifens (-)

  > [!Important]
  > Cada HSM gerenciado precisa ter um nome exclusivo. Substitua <nome-exclusivo-do-hsm-gerenciado> pelo nome do HSM gerenciado nos exemplos a seguir.

- Nome do grupo de recursos: **myResourceGroup**.
- A localização: **EastUS**.
- A ID da entidade de segurança: Transmita a ID da entidade do Azure Active Directory que você obteve na última seção para o parâmetro "Administrador". 

```azurepowershell-interactive
New-AzKeyVaultManagedHsm -Name "<your-unique-managed-hsm-name>" -ResourceGroupName "myResourceGroup" -Location "West US" -Administrator "<your-principal-ID>"
```

A saída desse cmdlet mostra as propriedades do HSM gerenciado recém-criado. Anote as duas propriedades listadas abaixo:

- **Nome do HSM gerenciado**: o nome que você forneceu ao parâmetro --name acima.
- **URI do cofre**: No exemplo, ele é https://&lt;nome-exclusivo-do-hsm-gerenciado&gt;.vault.azure.net/. Aplicativos que usam seu cofre via API REST devem usar esse URI.

Nesse ponto, sua conta do Azure é a única autorizada a executar qualquer operação nesse novo cofre.

## <a name="activate-your-managed-hsm"></a>Ativar o HSM gerenciado

Todos os comandos do plano de dados ficam desabilitados até que o HSM seja ativado. Você não poderá criar chaves nem atribuir funções. Somente os administradores designados que foram atribuídos durante o comando create podem ativar o HSM. Para ativar o HSM, você deve baixar o [Domínio de Segurança](security-domain.md).

Para ativar seu HSM, você precisa:
- Pelo menos três pares de chave RSA (máximo de 10)
- Especifique o número mínimo de chaves necessárias para descriptografar o domínio de segurança (quorum)

Para ativar o HSM, você envia pelo menos três (máximo 10) chaves públicas RSA para o HSM. O HSM criptografa o domínio de segurança com essas chaves e o envia de volta. Depois que o download do domínio de segurança for concluído com êxito, o HSM estará pronto para uso. Você também precisa especificar quorum, que é o número mínimo de chaves privadas necessárias para descriptografar o domínio de segurança.

O exemplo abaixo mostra como usar o `openssl` (disponível para o Windows [aqui](https://slproweb.com/products/Win32OpenSSL.html)) para gerar três certificados autoassinados.

```console
openssl req -newkey rsa:2048 -nodes -keyout cert_0.key -x509 -days 365 -out cert_0.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_1.key -x509 -days 365 -out cert_1.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_2.key -x509 -days 365 -out cert_2.cer
```

> [!IMPORTANT]
> Crie e armazene os pares de chaves RSA e o arquivo de domínio de segurança gerados nesta etapa com segurança.

Use o cmdlet [Export-AzKeyVaultSecurityDomain](/powershell/module/az.keyvault/export-azkeyvaultsecuritydomain) do Azure PowerShell para baixar o domínio de segurança e ativar o HSM gerenciado. O exemplo a seguir usa três pares de chaves RSA (somente as chaves públicas são necessárias para esse comando) e define o quorum como 2.

```azurepowershell-interactive
Export-AzKeyVaultSecurityDomain -Name "<your-unique-managed-hsm-name>" -Certificates "cert_0.cer", "cert_1.cer", "cert_2.cer" -OutputPath "MHSMsd.ps.json" -Quorum 2
```

Armazene o arquivo de domínio de segurança e os pares de chaves RSA com segurança. Você precisará deles para a recuperação de desastres ou para criar outro HSM gerenciado que compartilhe o mesmo domínio de segurança para que eles possam compartilhar chaves.

Depois de baixar com êxito o domínio de segurança, seu HSM estará em estado ativo e pronto para uso.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um Key Vault e armazenou um certificado nele. Para saber mais sobre o Key Vault e como integrá-lo a seus aplicativos, confira os artigos abaixo.

- Leia uma [Visão geral do Azure Key Vault](../general/overview.md)
- Confira a referência dos [cmdlets do Key Vault do Azure PowerShell](/powershell/module/az.keyvault/)
- Examine a [Visão geral de segurança do Key Vault](../general/security-overview.md)

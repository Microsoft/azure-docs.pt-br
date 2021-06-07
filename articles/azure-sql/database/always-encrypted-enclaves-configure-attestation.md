---
title: Configurar o atestado do Azure para seu servidor lógico do SQL Azure
description: Configure o atestado do Azure para Always Encrypted com o Secure enclaves no banco de dados SQL do Azure.
keywords: criptografar dados, criptografia do SQL, criptografia de banco de dados, informações confidenciais, Always Encrypted, enclaves seguros, SGX, atestado
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: fb42a0428f0439053375027481d38977b068e356
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102122571"
---
# <a name="configure-azure-attestation-for-your-azure-sql-logical-server"></a>Configurar o atestado do Azure para seu servidor lógico do SQL Azure

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Atualmente, o Always Encrypted com enclaves seguros para o Banco de Dados SQL do Azure está em **versão prévia pública**.

[Microsoft Azure atestado](../../attestation/overview.md) é uma solução para atestar TEEs (ambientes de execução confiáveis), incluindo o ENCLAVES Intel SGX (Intel® software Guard Extensions). 

Para usar o atestado do Azure para atestado do Intel SGX enclaves usado para [Always Encrypted com Secure enclaves](/sql/relational-databases/security/encryption/always-encrypted-enclaves) no banco de dados SQL do Azure, você precisa:

1. Crie um [provedor de atestado](../../attestation/basic-concepts.md#attestation-provider) e configure-o com a política de atestado recomendada.

2. Permita o acesso do provedor de atestado ao servidor lógico do SQL do Azure.

> [!NOTE]
> Configurar o atestado é responsabilidade do administrador de atestado. Consulte [funções e responsabilidades ao configurar o enclaves e o atestado de SGX](always-encrypted-enclaves-plan.md#roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation).

## <a name="requirements"></a>Requisitos

O servidor lógico do SQL do Azure e o provedor de atestado devem pertencer ao mesmo locatário Azure Active Directory. Não há suporte para interações entre locatários. 

O servidor lógico do SQL do Azure deve ter uma identidade do Azure AD atribuída a ele. Como administrador de atestado, você precisa obter a identidade do Azure AD do servidor do administrador do banco de dados SQL do Azure para esse servidor. Você usará a identidade para conceder ao servidor acesso ao provedor de atestado. 

Para obter instruções sobre como criar um servidor com uma identidade ou atribuir uma identidade a um servidor existente usando o PowerShell e o CLI do Azure, consulte [atribuir uma identidade do Azure AD ao seu servidor](transparent-data-encryption-byok-configure.md#assign-an-azure-active-directory-azure-ad-identity-to-your-server).

## <a name="create-and-configure-an-attestation-provider"></a>Criar e configurar um provedor de atestado

Um [provedor de atestado](../../attestation/basic-concepts.md#attestation-provider) é um recurso no atestado do Azure que avalia [as solicitações de atestado](../../attestation/basic-concepts.md#attestation-request) em [políticas de atestado](../../attestation/basic-concepts.md#attestation-request) e emite [tokens de atestado](../../attestation/basic-concepts.md#attestation-token). 

As políticas de atestado são especificadas usando a [gramática de regra de declaração](../../attestation/claim-rule-grammar.md).

A Microsoft recomenda a seguinte política para atestar o enclaves Intel SGX usado para Always Encrypted no banco de dados SQL do Azure:

```output
version= 1.0;
authorizationrules 
{
       [ type=="x-ms-sgx-is-debuggable", value==false ]
        && [ type=="x-ms-sgx-product-id", value==4639 ]
        && [ type=="x-ms-sgx-svn", value>= 0 ]
        && [ type=="x-ms-sgx-mrsigner", value=="e31c9e505f37a58de09335075fc8591254313eb20bb1a27e5443cc450b6e33e5"] 
    => permit();
};
```

A política acima verifica:

- O enclave dentro do banco de dados SQL do Azure não dá suporte à depuração. 
  > Enclaves pode ser carregado com a depuração desabilitada ou habilitada. O suporte à depuração foi projetado para permitir que os desenvolvedores solucionem problemas de código em execução em um enclave. Em um sistema de produção, a depuração pode permitir que um administrador examine o conteúdo do enclave, o que reduziria o nível de proteção que o enclave fornece. A política recomendada desabilita a depuração para garantir que, se um administrador mal-intencionado tentar ativar o suporte à depuração assumindo o computador enclave, o atestado falhará. 
- A ID do produto de enclave corresponde à ID do produto atribuída a Always Encrypted com Secure enclaves.
  > Cada enclave tem uma ID de produto exclusiva que diferencia o enclave de outros enclaves. A ID do produto atribuída ao Always Encrypted enclave é 4639. 
- O número de versão de segurança (SVN) da biblioteca é maior que 0.
  > O SVN permite que a Microsoft responda a possíveis bugs de segurança identificados no código enclave. Caso um problema de segurança seja abordado e corrigido, a Microsoft implantará uma nova versão do enclave com um novo SVN (incrementado). A política recomendada acima será atualizada para refletir o novo SVN. Ao atualizar sua política para corresponder à política recomendada, você pode garantir que, se um administrador mal-intencionado tentar carregar um enclave mais antigo e inseguro, o atestado falhará.
- A biblioteca no enclave foi assinada usando a chave de assinatura da Microsoft (o valor da declaração x-MS-SGX-mrsigner é o hash da chave de assinatura).
  > Um dos principais objetivos do atestado é convencer os clientes que o binário em execução no enclave é o binário que deve ser executado. As políticas de atestado fornecem dois mecanismos para essa finalidade. Uma é a Declaração **mrenclave** que é o hash do binário que deve ser executado em um enclave. O problema com o **mrenclave** é que o hash binário é alterado mesmo com alterações triviais no código, o que torna difícil a revisão do código em execução no enclave. Portanto, recomendamos o uso do **mrsigner**, que é um hash de uma chave usada para assinar o binário enclave. Quando a Microsoft Revs o enclave, o **mrsigner** permanece o mesmo, desde que a chave de assinatura não mude. Dessa forma, torna-se viável implantar binários atualizados sem quebrar os aplicativos dos clientes. 

> [!IMPORTANT]
> Um provedor de atestado é criado com a política padrão para o Intel SGX enclaves, que não valida o código em execução dentro do enclave. A Microsoft recomenda enfaticamente que você defina a política recomendada acima e não use a política padrão, para Always Encrypted com o Secure enclaves.

Para obter instruções sobre como criar um provedor de atestado e configurar com uma política de atestado usando:

- [Início rápido: Configurar Atestado do Azure com o portal do Azure](../../attestation/quickstart-portal.md)
    > [!IMPORTANT]
    > Ao configurar sua política de atestado com portal do Azure, defina tipo de atestado como `SGX-IntelSDK` .
- [Início Rápido: Configurar o Atestado do Azure com o Azure PowerShell](../../attestation/quickstart-powershell.md)
    > [!IMPORTANT]
    > Ao configurar sua política de atestado com Azure PowerShell, defina o `Tee` parâmetro como `SgxEnclave` .
- [Início Rápido: Configurar o Atestado do Azure com a CLI do Azure](../../attestation/quickstart-azure-cli.md)
    > [!IMPORTANT]
    > Ao configurar sua política de atestado com CLI do Azure, defina o `attestation-type` parâmetro como `SGX-IntelSDK` .

## <a name="determine-the-attestation-url-for-your-attestation-policy"></a>Determinar a URL de atestado para sua política de atestado

Depois de configurar uma política de atestado, você precisa compartilhar a URL de atestado, referenciando a política, os administradores de aplicativos que usam Always Encrypted com Secure enclaves no banco de dados SQL do Azure. Os administradores de aplicativos ou/ou os usuários de aplicativos precisarão configurar seus aplicativos com a URL de atestado, para que possam executar instruções que usam o Secure enclaves.

### <a name="use-powershell-to-determine-the-attestation-url"></a>Usar o PowerShell para determinar a URL de atestado

Use o script a seguir para determinar sua URL de atestado:

```powershell
$attestationProvider = Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName 
$attestationUrl = $attestationProvider.AttestUri + “/attest/SgxEnclave”
Write-Host "Your attestation URL is: " $attestationUrl 
```

### <a name="use-azure-portal-to-determine-the-attestation-url"></a>Usar portal do Azure para determinar a URL de atestado

1. No painel Visão geral do provedor de atestado, copie o valor da propriedade URI do atestado para área de transferência. Um URI de atestado deve ter a seguinte aparência: `https://MyAttestationProvider.us.attest.azure.net` .

2. Acrescente o seguinte ao URI do atestado: `/attest/SgxEnclave` . 

A URL de atestado resultante deve ter a seguinte aparência: `https://MyAttestationProvider.us.attest.azure.net/attest/SgxEnclave`

## <a name="grant-your-azure-sql-logical-server-access-to-your-attestation-provider"></a>Conceder acesso ao seu servidor lógico do Azure SQL ao seu provedor de atestado

Durante o fluxo de trabalho de atestado, o servidor lógico do SQL do Azure que contém seu banco de dados chama o provedor de atestado para enviar uma solicitação de atestado. Para que o servidor lógico do Azure SQL seja capaz de enviar solicitações de atestado, o servidor deve ter uma permissão para a `Microsoft.Attestation/attestationProviders/attestation/read` ação no provedor de atestado. A maneira recomendada para conceder a permissão é para o administrador do provedor de atestado atribuir a identidade do Azure AD do servidor à função de leitor de atestado para o provedor de atestado ou seu grupo de recursos que o contém.

### <a name="use-azure-portal-to-assign-permission"></a>Usar portal do Azure para atribuir permissão

Para atribuir a identidade de um SQL Server do Azure à função de leitor de atestado para um provedor de atestado, siga as instruções gerais em [atribuir funções do Azure usando o portal do Azure](../../role-based-access-control/role-assignments-portal.md). Quando você estiver no painel **Adicionar atribuição de função** :

1. Na lista suspensa **função** , selecione a função **leitor de atestado** .
1. No campo **selecionar** , insira o nome do seu servidor SQL do Azure para pesquisá-lo.

Consulte a captura de tela abaixo para obter um exemplo.

![atribuição de função de leitor de atestado](./media/always-encrypted-enclaves/attestation-provider-role-assigment.png)

> [!NOTE]
> Para que um servidor apareça no painel **Adicionar atribuição de função** , o servidor deve ter uma identidade do Azure ad atribuída-consulte [requisitos](#requirements).

### <a name="use-powershell-to-assign-permission"></a>Usar o PowerShell para atribuir permissão

1. Localize seu servidor lógico do SQL do Azure.

```powershell
$serverResourceGroupName = "<server resource group name>"
$serverName = "<server name>" 
$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName
```
 
2. Atribua o servidor à função de leitor de atestado para o grupo de recursos que contém seu provedor de atestado.

```powershell
$attestationResourceGroupName = "<attestation provider resource group name>"
New-AzRoleAssignment -ObjectId $server.Identity.PrincipalId -RoleDefinitionName "Attestation Reader" -ResourceGroupName $attestationResourceGroupName
```

Para obter mais informações, consulte [atribuir funções do Azure usando Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md#assign-role-examples).

## <a name="next-steps"></a>Próximas etapas

- [Gerenciar chaves para Always Encrypted com enclaves seguros](/sql/relational-databases/security/encryption/always-encrypted-enclaves-manage-keys)

## <a name="see-also"></a>Veja também

- [Tutorial: Introdução ao Always Encrypted com enclaves seguros no Banco de Dados SQL do Azure](always-encrypted-enclaves-getting-started.md)

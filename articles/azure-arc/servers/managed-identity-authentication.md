---
title: Autenticar em recursos do Azure com servidores habilitados para Arc
description: Este artigo descreve o suporte ao serviço de metadados de instância do Azure para servidores habilitados para Arc e como você pode se autenticar em recursos do Azure e localmente usando um segredo.
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: 49b70928ae972da8e0a0d14d711e4b6f246cca6a
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96939063"
---
# <a name="authenticate-against-azure-resources-with-arc-enabled-servers"></a>Autenticar em recursos do Azure com servidores habilitados para Arc

Aplicativos ou processos em execução diretamente em servidores habilitados para Arc do Azure podem aproveitar identidades gerenciadas para acessar outros recursos do Azure que dão suporte à autenticação baseada em Azure Active Directory. Um aplicativo pode obter um [token de acesso](../../active-directory/develop/developer-glossary.md#access-token) que representa sua identidade, que é atribuído pelo sistema para servidores habilitados para Arc, e usá-lo como um token de "portador" para se autenticar em outro serviço.

Consulte a documentação [visão geral da identidade gerenciada](../../active-directory/managed-identities-azure-resources/overview.md) para obter uma descrição detalhada das identidades gerenciadas, bem como a distinção entre identidades atribuídas pelo sistema e pelo usuário.

Neste artigo, mostramos como um servidor pode usar uma identidade gerenciada atribuída pelo sistema para acessar o Azure [Key Vault](../../key-vault/general/overview.md). Atuando como uma inicialização, o Key Vault possibilita que o aplicativo cliente use o segredo para acessar recursos não protegidos pelo Azure AD (Active Directory). Por exemplo, certificados TLS/SSL usados por seus servidores Web do IIS podem ser armazenados em Azure Key Vault e implantar com segurança os certificados em servidores Windows ou Linux fora do Azure.

## <a name="security-overview"></a>Visão geral da segurança

Ao integrar o servidor aos servidores habilitados para Arc do Azure, várias ações são executadas para configurar o usando uma identidade gerenciada, semelhante ao que é executado para uma VM do Azure:

- Azure Resource Manager recebe uma solicitação para habilitar a identidade gerenciada atribuída pelo sistema no servidor habilitado para Arc.

- Azure Resource Manager cria uma entidade de serviço no Azure AD para a identidade do servidor. A entidade de serviço é criada no locatário do Azure AD confiado pela assinatura.

- Azure Resource Manager configura a identidade no servidor atualizando o ponto de extremidade de identidade do IMDS (serviço de metadados de instância) do Azure para [Windows](../../virtual-machines/windows/instance-metadata-service.md) ou [Linux](../../virtual-machines/linux/instance-metadata-service.md) com a ID do cliente e o certificado da entidade de serviço. O ponto de extremidade é um ponto de extremidade REST acessível somente de dentro do servidor usando um endereço IP bem conhecido e não roteável. Esse serviço fornece um subconjunto de informações de metadados sobre o servidor habilitado para ARC para ajudar a gerenciá-lo e configurá-lo.

O ambiente de um servidor habilitado para identidade gerenciada será configurado com as seguintes variáveis em um servidor habilitado para Arc do Windows:

- **IMDS_ENDPOINT**: o endereço IP do ponto de extremidade IMDS `http://localhost:40342` para servidores habilitados para Arc.

- **IDENTITY_ENDPOINT**: o ponto de extremidade de localhost correspondente à identidade gerenciada do serviço `http://localhost:40342/metadata/identity/oauth2/token` .

Seu código em execução no servidor pode solicitar um token do ponto de extremidade do serviço de metadados da instância do Azure, acessível somente de dentro do servidor.

A variável de ambiente do sistema **IDENTITY_ENDPOINT** é usada para descobrir o ponto de extremidade de identidade por aplicativos. Os aplicativos devem tentar recuperar **IDENTITY_ENDPOINT** e **IMDS_ENDPOINT** valores e usá-los. Aplicativos com qualquer nível de acesso têm permissão para fazer solicitações para os pontos de extremidade. As respostas de metadados são tratadas como normais e dadas a qualquer processo no computador. No entanto, quando é feita uma solicitação que exporia um token, exigimos que o cliente forneça um segredo para atestar que eles possam acessar os dados disponíveis somente para usuários com privilégios elevados.

## <a name="prerequisites"></a>Pré-requisitos

- Conhecimento sobre Identidades Gerenciadas.
- Um servidor conectado e registrado com servidores habilitados para Arc.
- Você é um membro do [grupo proprietário](../../role-based-access-control/built-in-roles.md#owner)* * na assinatura ou no grupo de recursos, a fim de executar as etapas necessárias de criação de recursos e gerenciamento de funções.
- Um Azure Key Vault para armazenar e recuperar sua credencial. e atribua o acesso de identidade do arco do Azure ao keyvault.

    - Se você não tiver um Key Vault criado, consulte [criar Key Vault](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#create-a-key-vault-).
    - Para configurar o acesso pela identidade gerenciada usada pelo servidor, consulte [conceder acesso para Linux](../../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-nonaad.md#grant-access) ou [conceder acesso para o Windows](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#grant-access). Para o número de etapa 5, você vai inserir o nome do servidor habilitado para Arc. Para concluir isso usando o PowerShell, consulte [atribuir uma política de acesso usando o PowerShell](../../key-vault/general/assign-access-policy-powershell.md).

## <a name="acquiring-an-access-token-using-rest-api"></a>Adquirindo um token de acesso usando a API REST

O método para obter e usar uma identidade gerenciada atribuída pelo sistema para autenticar com recursos do Azure é semelhante a como ele é executado com uma VM do Azure.

Para um Windows Server habilitado para Arc, usando o PowerShell, você invoca a solicitação da Web para obter o token do host local na porta específica. Especifique a solicitação usando o endereço IP ou a variável de ambiente **IDENTITY_ENDPOINT**.

```powershell
$apiVersion = "2020-06-01"
$resource = "https://management.azure.com/"
$endpoint = "{0}?resource={1}&api-version={2}" -f $env:IDENTITY_ENDPOINT,$resource,$apiVersion
$secretFile = ""
try
{
    Invoke-WebRequest -Method GET -Uri $endpoint -Headers @{Metadata='True'} -UseBasicParsing
}
catch
{
    $wwwAuthHeader = $_.Exception.Response.Headers["WWW-Authenticate"]
    if ($wwwAuthHeader -match "Basic realm=.+")
    {
        $secretFile = ($wwwAuthHeader -split "Basic realm=")[1]
    }
}
Write-Host "Secret file path: " $secretFile`n
$secret = cat -Raw $secretFile
$response = Invoke-WebRequest -Method GET -Uri $endpoint -Headers @{Metadata='True'; Authorization="Basic $secret"} -UseBasicParsing
if ($response)
{
    $token = (ConvertFrom-Json -InputObject $response.Content).access_token
    Write-Host "Access token: " $token
}
```

A resposta a seguir é um exemplo retornado:

:::image type="content" source="media/managed-identity-authentication/powershell-token-output-example.png" alt-text="Uma recuperação bem-sucedida do token de acesso usando o PowerShell.":::

Para um servidor Linux habilitado para Arc, usando bash, você invoca a solicitação da Web para obter o token do host local na porta específica. Especifique a solicitação a seguir usando o endereço IP ou a variável de ambiente **IDENTITY_ENDPOINT**. Para concluir esta etapa, você precisa de um cliente SSH.

```bash
ChallengeTokenPath=$(curl -s -D - -H Metadata:true "http://127.0.0.1:40342/metadata/identity/oauth2/token?api-version=2019-11-01&resource=https%3A%2F%2Fmanagement.azure.com" | grep Www-Authenticate | cut -d "=" -f 2 | tr -d "[:cntrl:]")
ChallengeToken=$(cat $ChallengeTokenPath)
if [ $? -ne 0 ]; then
    echo "Could not retrieve challenge token, double check that this command is run with root privileges."
else
    curl -s -H Metadata:true -H "Authorization: Basic $ChallengeToken" "http://127.0.0.1:40342/metadata/identity/oauth2/token?api-version=2019-11-01&resource=https%3A%2F%2Fmanagement.azure.com"
fi
```

A resposta a seguir é um exemplo retornado:

:::image type="content" source="media/managed-identity-authentication/bash-token-output-example.png" alt-text="Uma recuperação bem-sucedida do token de acesso usando bash.":::

A resposta inclui o token de acesso de que você precisa para acessar qualquer recurso no Azure. Para concluir a configuração para autenticar no Azure Key Vault, confira [acessar Key Vault com o Windows](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#access-data) ou o [Key Vault de acesso com o Linux](../../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-nonaad.md#access-data).

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre Azure Key Vault, consulte [Key Vault visão geral](../../key-vault/general/overview.md).

- Saiba como atribuir um acesso de identidade gerenciada a um recurso [usando o PowerShell](../../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md) ou usando [o CLI do Azure](../../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md).
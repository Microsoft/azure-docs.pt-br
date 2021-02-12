---
title: Guia de solução de problemas do Atestado do Azure
description: Guia de solução de problemas para os problemas mais observados
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 3ae3e12c11f194b3efcc149382dc952bd74d38b5
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704309"
---
# <a name="microsoft-azure-attestation-troubleshooting-guide"></a>Guia de solução de problemas de atestado Microsoft Azure

O tratamento de erros no atestado do Azure é implementado seguindo as [diretrizes da API REST da Microsoft](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#7102-error-condition-responses). A resposta de erro retornada pelas APIs de atestado do Azure contém o código de status HTTP e os pares de nome/valor com os nomes "código" e "mensagem". O valor de "código" é legível por humanos e é um indicador do tipo de erro. O valor de "Message" pretende ajudar o usuário e fornece detalhes do erro.

Se o problema não for resolvido neste artigo, você também poderá enviar uma solicitação de suporte do Azure na [página de suporte do Azure](https://azure.microsoft.com/support/options/).

Abaixo estão alguns exemplos dos erros retornados pelo atestado do Azure:

## <a name="1-http401--unauthorized-exception"></a>1. HTTP – 401: exceção não autorizada

### <a name="http-status-code"></a>Código de status HTTP
401

**Código de erro** Não autorizado

**Exemplos de cenário**
  - Falha de atestado se o usuário não for atribuído com a função de leitor de atestado
  - Não é possível gerenciar as políticas de atestado, pois o usuário não está atribuído com as funções apropriadas
  - Não é possível gerenciar os assinantes de política de atestado, pois o usuário não está atribuído com as funções apropriadas

Usuário com função de leitor tentando editar uma política de atestado no PowerShell 

  ```powershell
  Set-AzAttestationPolicy : Operation returned HTTP Status Code 401
At line:1 char:1
+ Set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
  ```

**Etapas de solução de problemas**

Para exibir políticas de atestado/assinantes de política, um usuário do Azure AD requer a permissão para "ações":
- Microsoft.Attestation/attestationProviders/attestation/read

  Essa permissão pode ser atribuída a um usuário do AD por meio de uma função como "proprietário" (permissões curinga) ou "leitor" (permissões curinga) ou "leitor de atestado" (permissões específicas para o atestado do Azure somente).

Para adicionar/excluir os assinantes de política ou configurar políticas, um usuário do Azure AD requer as seguintes permissões para "ações":
- Microsoft.Attestation/attestationProviders/attestation/write
- Microsoft.Attestation/attestationProviders/attestation/delete

  Essas permissões podem ser atribuídas a um usuário do AD por meio de uma função como "proprietário" (permissões curinga), "colaborador" (permissões curinga) ou "colaborador de atestado" (permissões específicas para o atestado do Azure somente).

Os clientes podem optar por usar o provedor padrão para o atestado ou criar provedores próprios com políticas personalizadas. Para enviar solicitações de atestado para provedores de atestado personalizados, a função "proprietário" (permissões curinga) ou "leitor" (permissões de curinga) ou de "leitor de atestado" é necessária para o usuário. Os provedores padrão podem ser acessados por qualquer usuário do Azure AD.

Para verificar as funções no PowerShell, execute abaixo:

a. Inicie o PowerShell e faça logon no Azure por meio do cmdlet "Connect-AzAccount"

b. Verifique as configurações de atribuição de função do Azure


  ```powershell
  $c = Get-AzContext
  Get-AzRoleAssignment -ResourceGroupName $attestationResourceGroup -ResourceName $attestationProvider -ResourceType Microsoft.Attestation/attestationProviders -SignInName $c.Account.Id
  ```

  Você deverá ver algo como:

  ```
  RoleAssignmentId   :/subscriptions/subscriptionId/providers/Microsoft.Authorization/roleAssignments/roleAssignmentId
  
  Scope              : /subscriptions/subscriptionId
  
  DisplayName        : displayName
  
  SignInName         : signInName
  
  RoleDefinitionName : Reader
  
  RoleDefinitionId   : roleDefinitionId
  
  ObjectId           : objectid
  
  ObjectType         : User
  
  CanDelegate        : False
 
  ```

c. Se você não encontrar uma atribuição de função apropriada na lista, siga as instruções [aqui](../role-based-access-control/role-assignments-powershell.md)

## <a name="2-http--400-errors"></a>2. HTTP – erros 400

### <a name="http-status-code"></a>Código de status HTTP
400

Há diferentes motivos pelos quais uma solicitação pode retornar 400. Abaixo estão alguns exemplos de erros retornados pelas APIs de atestado do Azure:

### <a name="21-attestation-failure-due-to-policy-evaluation-errors"></a>2.1. Falha de atestado devido a erros de avaliação de política

A política de atestado inclui regras de autorização e regras de emissão. A evidência enclave é avaliada com base nas regras de autorização. As regras de emissão definem as declarações a serem incluídas no token de atestado. Se as declarações na evidência de enclave não estiverem em conformidade com as regras de autorização, as chamadas de atestado retornarão um erro de avaliação de política. 

**Código de erro** PolicyEvaluationError

**Exemplos de cenário** Quando as declarações na citação enclave não correspondem às regras de autorização da política de atestado

```
Native operation failed with 65518: G:\Az\security\Attestation\src\AttestationServices\Instance\NativePolicyWrapper\NativePolicyEngine.cpp(168)\(null)!00007FF801762308: (caller: 00007FF80143DCC8) Exception(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Policy Engine Exception: A Deny claim was issued, authorization failed.]

G:\Az\security\Attestation\src\AttestationServices\Instance\Enclave\api.cpp(840)\(null)!00007FF801739FF3: (caller: 00007FF801232801) LogHr(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Unhandled Enclave Exception: "Policy Evaluation Error has occurred"]

```

**Etapas de solução de problemas** Os usuários podem avaliar a evidência de enclave em relação a uma política de atestado de SGX antes de configurar o mesmo.

Envie uma solicitação para a API de atestado fornecendo o texto de política no parâmetro "draftPolicyForAttestation". A API AttestSgxEnclave usará esse documento de política durante a chamada de atestado e isso poderá ser usado para testar as políticas de atestado antes que elas sejam consumidas. O token de atestado gerado quando este campo está presente não será seguro.

Consulte os [exemplos de política de atestado](./policy-examples.md)

### <a name="22-attestation-failure-due-to-invalid-input"></a>2.2. Falha de atestado devido a uma entrada inválida

**Código de erro** InvalidParameter

**Exemplos de cenário** Falha de atestado de SGX devido a uma entrada inválida. Abaixo estão alguns dos exemplos de mensagens de erro:
- A aspa especificada era inválida devido a um erro no material de cotação 
- A cotação especificada era inválida porque o dispositivo no qual a cotação foi gerada não atende aos requisitos de linha de base do Azure
- A aspa especificada era inválida porque o TCBInfo ou o QEID fornecido pelo serviço de cache PCK era inválido

**Etapas de solução de problemas**

Microsoft Azure atestado dá suporte ao atestado de citações SGX geradas pelo SDK Intel e pelo Open enclave SDK.

Consulte os [exemplos de código](/samples/browse/?expanded=azure&terms=attestation) para executar o atestado usando o SDK do Open enclave/SDK do Intel

### <a name="23-invalid-certificate-chain-error-while-uploading-policypolicy-signer"></a>2.3. Erro de cadeia de certificados inválido ao carregar o assinante política/política

**Código de erro** InvalidParameter

**Exemplos de cenário** Configure a política assinada ou adicione/exclua o signatário de política, que é assinado com uma cadeia de certificados inválida (por exemplo, quando a extensão de restrições básicas do certificado raiz não está definida para o tipo de entidade = CA)

```
Native operation failed with 65529: C:\source\src\AttestationServices\Instance\SgxPal\sgxcert.cpp(1074)\(null)!00007FFA285CDAED: (caller: 00007FFA285C36E8) Exception(0) 83FFFFF9 The requested item is not found    Msg:[Unable to find issuer certificate CN=attestationsigningcert]
C:\source\src\AttestationServices\Instance\Enclave\api.cpp(618)\(null)!00007FFA286DCBF8: (caller: 00007FFA285860D3) LogHr(0) 83FFFFF9 The requested item is not found    Msg:[Unhandled Enclave Exception: "The requested item is not found"]
At line:1 char:1
+ Set-AzAttestationPolicy -Name "testpolicy1" -ResourceGroupName "BugBa ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy

```

**Etapas de solução de problemas** O certificado raiz deve ser sinalizado como sendo emitido por uma AC (as restrições básicas X. 509), caso contrário, ele não será considerado como um certificado válido. 

Verifique se a extensão de restrições básicas do certificado raiz está definida para indicar que tipo de assunto = CA

Caso contrário, a cadeia de certificados será considerada inválida.

Consulte os exemplos de signatário e [política](./policy-examples.md) de [política](./policy-signer-examples.md) 

### <a name="24-adddelete-policy-signer-failure"></a>2.4. Adicionar/excluir falha de signatário de política

**Código de erro** InvalidOperation

**Exemplos de cenário**

Quando o usuário carrega o JWS sem a declaração "Maa-policyCertificate"

```
Add-AzAttestationPolicySigner : Operation returned HTTP Status Code 400
Code: InvalidOperation
Message: Native operation failed with 74: ..\Enclave\enclave.cpp(2213)\(null)!: (caller: ) Exception(0) 83FF004A Bad
message    Msg:[Could not find "maa-policyCertificate" claim in policy token]
..\Enclave\api.cpp(496)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad
message"]
At line:1 char:1
+ Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGro ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Add-AzAttestationPolicySigner], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.AddAzureAttestationPolicySigner

```

Quando o usuário não carrega um certificado no formato JWS

```
Add-AzAttestationPolicySigner : Operation returned HTTP Status Code 400
Code: InvalidOperation
Message: Native operation failed with 74: ..\JsonWebToken\jsonwebtoken.cpp(375)\(null)!: (caller: ) LogHr(0) 83FF004A
Bad message    Msg:[RETURN_IF_TRUE('(firstPeriod == std::string::npos)') failed with 0x4a: Malformed JWT: Could not
find first period in the token.]
..\Enclave\enclave.cpp(2106)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message
Msg:[THROW_IF_ERROR('DecomposeJsonWebSignature(&policyJws, encodedJoseHeader, encodedJwsBody, jwsSignature)') failed
with 0x4a: 'Bad message']
..\Enclave\enclave.cpp(2106)\(null)!: (caller: ) Exception(0) 83FF004A Bad message
..\Enclave\api.cpp(496)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad
message"]
At line:1 char:1
+ Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGro ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Add-AzAttestationPolicySigner], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.AddAzureAttestationPolicySigner
```

**Etapas de solução de problemas** Para adicionar/excluir um novo certificado de signatário de política, use RFC7519 JSON Web token (JWT) com uma declaração denominada "x-MS-policyCertificate". O valor da declaração é uma chave da Web RFC7517 JSON, que contém o certificado a ser adicionado. O JWT deve ser assinado com uma chave privada de qualquer um dos certificados de signatário de política válidos associados ao provedor. Consulte [exemplos de signatário de política](./policy-signer-examples.md).

### <a name="25-attestation-policy-configuration-failure"></a>2.5. Falha na configuração da política de atestado

**Código de erro** PolicyParsingError

**Exemplos de cenário** Política fornecida com sintaxe incorreta (por exemplo, ponto-e-vírgula ausente) política JWT/Valid)

```
Native operation failed with 65526: ..\NativePolicyWrapper\NativePolicyEngine.cpp(31)\(null)!: (caller: ) Exception(0) 83FFFFF6 Invalid policy was specified    Msg:[Policy Parser Exception Thrown: Offending
symbol: '['
Line: '2', Column: '1'
Failure message: 'mismatched input '[' expecting ';''
Failing rule: 'policy > versionInfo']
..\Enclave\api.cpp(618)\(null)!: (caller: ) LogHr(0) 83FFFFF6 Invalid policy was specified    Msg:[Unhandled Enclave Exception: "Invalid policy was specified"]
At line:1 char:1
+ set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
```

**Código de erro** InvalidOperation

**Exemplos de cenário** Conteúdo inválido fornecido (por exemplo, política de carregamento/política não assinada quando a assinatura de política é necessária)

```
Native operation failed with 74: ..\Shared\base64url.h(226)\(null)!: (caller: ) Exception(0) 83FF004A Bad message    Msg:[Unknown base64 character: 41 (')')]
..\Enclave\api.cpp(618)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad message"]
At line:1 char:1
+ set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
```

**Etapas de solução de problemas** Verifique se a política no formato de texto é codificada em UTF-8.

Se a assinatura de política for necessária, a política de atestado deverá ser configurada somente no formato JWT (token Web JSON) RFC7519. Se a assinatura de política não for necessária, a política poderá ser configurada no formato de texto ou JWT.

Para configurar uma política no formato JWT, use JWT com uma declaração chamada "AttestationPolicy". O valor da declaração é a versão codificada Base64URL do texto da política. Se o provedor de atestado estiver configurado com certificados de signatário de política, o JWT deverá ser assinado com a chave privada de qualquer um dos certificados de signatário de política válidos associados ao provedor. 

Para configurar uma política no formato de texto, especifique o texto da política diretamente.

No PowerShell, especifique PolicyFormat como JWT para configurar a política no formato JWT. O formato de política padrão é texto.

Consulte exemplos de [política](./policy-examples.md) de atestado e [como criar uma política de atestado](./author-sign-policy.md) 

## <a name="3-azattestation-installation-issues-in-powershell"></a>3. problemas de instalação do AZ. atestation no PowerShell

Não é possível instalar os módulos AZ ou AZ. atestation no PowerShell

### <a name="error"></a>Erro

Aviso: não é possível resolver a origem https://www.powershellgallery.com/api/v2 do pacote ' ' PackageManagement\Install-Package: nenhuma correspondência foi encontrada para os critérios de pesquisa especificados e o nome do módulo

### <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

O Galeria do PowerShell preteriu as versões 1,0 e 1,1 do protocolo TLS. 

Recomendamos usar o TLS 1.2 ou uma versão posterior. 

Para continuar a interagir com a Galeria do PowerShell, execute o comando a seguir antes dos comandos Install-Module

**[Net. ServicePointManager]:: SecurityProtocol = [net. SecurityProtocolType]:: Tls12**

## <a name="4-policy-accessconfiguration-issues-in-powershell"></a>4. problemas de acesso à política/configuração no PowerShell

Usuário atribuído com as funções apropriadas. Mas enfrentando problemas de autorização ao gerenciar políticas de atestado por meio do PowerShell.

### <a name="error"></a>Erro
O cliente com a ID de objeto de ID de objeto &lt; &gt;  não tem autorização para executar a ação Microsoft. Authorization/RoleAssignments/Write sobre o escopo ' subscrições/ &lt; SubscriptionId &gt; resourcegroups/secure_enclave_poc/Providers/Microsoft.Authorization/RoleAssignments/ &lt; atribuição &gt; de função ' ou o escopo é inválido. Se o acesso foi concedido recentemente, atualize suas credenciais

### <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

A versão mínima dos módulos AZ necessários para dar suporte a operações de atestado é a seguinte: 

 **Az 4.5.0** 
 
 **Az.Accounts 1.9.2**
 
 **Az.Attestation 0.1.8** 

Execute o comando abaixo para confirmar a versão instalada de todos os módulos Az 

```powershell
Get-InstalledModule 
```

Se as versões não forem correspondentes ao requisito mínimo, execute Update-Module comandos

por exemplo,-Update-Module-Name AZ. atestado

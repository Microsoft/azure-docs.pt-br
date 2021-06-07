---
title: Usar identidade gerenciada com um aplicativo
description: Como usar identidades gerenciadas no Azure Service Fabric código do aplicativo para acessar os serviços do Azure.
ms.topic: article
ms.date: 10/09/2019
ms.openlocfilehash: e26a29020f26583f7e4aa16434c7e8647ba9a5a3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98871054"
---
# <a name="how-to-leverage-a-service-fabric-applications-managed-identity-to-access-azure-services"></a>Como aproveitar a identidade gerenciada de um aplicativo Service Fabric para acessar os serviços do Azure

Service Fabric aplicativos podem aproveitar identidades gerenciadas para acessar outros recursos do Azure que dão suporte à autenticação baseada em Azure Active Directory. Um aplicativo pode obter um [token de acesso](../active-directory/develop/developer-glossary.md#access-token) que representa sua identidade, que pode ser atribuído pelo sistema ou atribuído pelo usuário, e usá-lo como um token de "portador" para se autenticar em outro serviço, também conhecido como um [servidor de recursos protegido](../active-directory/develop/developer-glossary.md#resource-server). O token representa a identidade atribuída ao aplicativo Service Fabric e será emitido somente para recursos do Azure (incluindo aplicativos da it) que compartilham essa identidade. Consulte a documentação [visão geral da identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) para obter uma descrição detalhada das identidades gerenciadas, bem como a distinção entre identidades atribuídas pelo sistema e pelo usuário. Vamos nos referir a um aplicativo de Service Fabric habilitado para identidade gerenciada como o [aplicativo cliente](../active-directory/develop/developer-glossary.md#client-application) em todo este artigo.

Consulte um aplicativo de exemplo complementar que demonstra o uso de [identidades de aplicativo Service Fabric](https://github.com/Azure-Samples/service-fabric-managed-identity) atribuídas pelo usuário e atribuídas por usuários com Reliable Services e contêineres.

> [!IMPORTANT]
> Uma identidade gerenciada representa a associação entre um recurso do Azure e uma entidade de serviço no locatário do Azure AD correspondente associado à assinatura que contém o recurso. Como tal, no contexto de Service Fabric, identidades gerenciadas têm suporte apenas para aplicativos implantados como recursos do Azure. 

> [!IMPORTANT]
> Antes de usar a identidade gerenciada de um aplicativo Service Fabric, o aplicativo cliente deve receber acesso ao recurso protegido. Consulte a lista de [Serviços do Azure que dão suporte à autenticação do Azure ad](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) para verificar o suporte e, em seguida, para a documentação do respectivo serviço para obter as etapas específicas para conceder um acesso de identidade aos recursos de interesse. 
 

## <a name="leverage-a-managed-identity-using-azureidentity"></a>Aproveite uma identidade gerenciada usando o Azure. Identity

O SDK de identidade do Azure agora dá suporte a Service Fabric. Usar o Azure. Identity torna o código de escrita para usar Service Fabric identidades gerenciadas do aplicativo mais facilmente, pois ele lida com a busca de tokens, cache tokens e autenticação de servidor. Ao acessar a maioria dos recursos do Azure, o conceito de um token é oculto.

Service Fabric suporte está disponível nas seguintes versões para estes idiomas: 
- [C# na versão 1.3.0](https://www.nuget.org/packages/Azure.Identity). Veja um [exemplo em C#](https://github.com/Azure-Samples/service-fabric-managed-identity).
- [Python na versão 1.5.0](https://pypi.org/project/azure-identity/). Consulte um [exemplo de Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/identity/azure-identity/tests/managed-identity-live/service-fabric/service_fabric.md).
- [Java na versão 1.2.0](/java/api/overview/azure/identity-readme).

Exemplo em C# de inicialização de credenciais e uso das credenciais para buscar um segredo de Azure Key Vault:

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;

namespace MyMIService
{
    internal sealed class MyMIService : StatelessService
    {
        protected override async Task RunAsync(CancellationToken cancellationToken)
        {
            try
            {
                // Load the service fabric application managed identity assigned to the service
                ManagedIdentityCredential creds = new ManagedIdentityCredential();

                // Create a client to keyvault using that identity
                SecretClient client = new SecretClient(new Uri("https://mykv.vault.azure.net/"), creds);

                // Fetch a secret
                KeyVaultSecret secret = (await client.GetSecretAsync("mysecret", cancellationToken: cancellationToken)).Value;
            }
            catch (CredentialUnavailableException e)
            {
                // Handle errors with loading the Managed Identity
            }
            catch (RequestFailedException)
            {
                // Handle errors with fetching the secret
            }
            catch (Exception e)
            {
                // Handle generic errors
            }
        }
    }
}

```

## <a name="acquiring-an-access-token-using-rest-api"></a>Adquirindo um token de acesso usando a API REST
Em clusters habilitados para identidade gerenciada, o tempo de execução de Service Fabric expõe um ponto de extremidade de localhost que os aplicativos podem usar para obter tokens de acesso. O ponto de extremidade está disponível em todos os nós do cluster e está acessível a todas as entidades nesse nó. Chamadores autorizados podem obter tokens de acesso chamando esse ponto de extremidade e apresentando um código de autenticação; o código é gerado pelo tempo de execução de Service Fabric para cada ativação de pacote de código de serviço distinto e está associado ao tempo de vida do processo que hospeda esse pacote de código de serviço.

Especificamente, o ambiente de um serviço de Service Fabric habilitado para identidade gerenciada será propagado com as seguintes variáveis:
- ' IDENTITY_ENDPOINT ': o ponto de extremidade de localhost correspondente à identidade gerenciada do serviço
- ' IDENTITY_HEADER ': um código de autenticação exclusivo que representa o serviço no nó atual
- ' IDENTITY_SERVER_THUMBPRINT ': impressão digital do servidor de identidade gerenciada do Service Fabric

> [!IMPORTANT]
> O código do aplicativo deve considerar o valor da variável de ambiente ' IDENTITY_HEADER ' como dados confidenciais-ele não deve ser registrado em log ou ser disseminado de outra forma. O código de autenticação não tem valor fora do nó local ou depois que o processo que hospeda o serviço termina, mas ele representa a identidade do serviço de Service Fabric e, portanto, deve ser tratado com as mesmas precauções que o token de acesso em si.

Para obter um token, o cliente executa as seguintes etapas:
- forma um URI concatenando o ponto de extremidade de identidade gerenciada (IDENTITY_ENDPOINT valor) com a versão da API e o recurso (público) necessário para o token
- Cria uma solicitação de http (s) GET para o URI especificado
- Adiciona a lógica de validação de certificado do servidor apropriada
- Adiciona o código de autenticação (IDENTITY_HEADER valor) como um cabeçalho à solicitação
- envia a solicitação

Uma resposta bem-sucedida conterá um conteúdo JSON que representa o token de acesso resultante, bem como os metadados que o descrevem. Uma resposta com falha também incluirá uma explicação da falha. Veja abaixo detalhes adicionais sobre o tratamento de erros.

Os tokens de acesso serão armazenados em cache por Service Fabric em vários níveis (nó, cluster, serviço de provedor de recursos), portanto, uma resposta bem-sucedida não significa, necessariamente, que o token foi emitido diretamente em resposta à solicitação do aplicativo do usuário. Os tokens serão armazenados em cache por menos do que o tempo de vida e, portanto, um aplicativo terá a garantia de receber um token válido. É recomendável que o código do aplicativo armazene em cache todos os tokens de acesso adquiridos; a chave de cache deve incluir (uma derivação) do público. 

Solicitação de exemplo:
```http
GET 'https://localhost:2377/metadata/identity/oauth2/token?api-version=2019-07-01-preview&resource=https://vault.azure.net/' HTTP/1.1 Secret: 912e4af7-77ba-4fa5-a737-56c8e3ace132
```
em que:

| Elemento | Descrição |
| ------- | ----------- |
| `GET` | O verbo HTTP, indicando que você deseja recuperar os dados do ponto de extremidade. Neste caso, um token de acesso OAuth. | 
| `https://localhost:2377/metadata/identity/oauth2/token` | O ponto de extremidade de identidade gerenciada para aplicativos Service Fabric, fornecido por meio da variável de ambiente IDENTITY_ENDPOINT. |
| `api-version` | Um parâmetro de cadeia de caracteres de consulta, especificando a versão de API do serviço de token de identidade gerenciado; Atualmente, o único valor aceito é `2019-07-01-preview` e está sujeito a alterações. |
| `resource` | Um parâmetro de cadeia de caracteres de consulta que indica o URI da ID do aplicativo do recurso de destino. Isso será refletido como a `aud` declaração (público) do token emitido. Este exemplo solicita um token para acessar Azure Key Vault, cujo URI de ID do aplicativo é https: \/ /Vault.Azure.net/. |
| `Secret` | Um campo de cabeçalho de solicitação HTTP, exigido pelo Service Fabric serviço de token de identidade gerenciada para serviços de Service Fabric para autenticar o chamador. Esse valor é fornecido pelo tempo de execução da it por meio da variável de ambiente IDENTITY_HEADER. |


Exemplo de resposta:
```json
HTTP/1.1 200 OK
Content-Type: application/json
{
    "token_type":  "Bearer",
    "access_token":  "eyJ0eXAiO...",
    "expires_on":  1565244611,
    "resource":  "https://vault.azure.net/"
}
```
em que:

| Elemento | Descrição |
| ------- | ----------- |
| `token_type` | O tipo de token; Nesse caso, um token de acesso "portador", que significa que o apresentador ("portador") desse token é o assunto pretendido do token. |
| `access_token` | O token de acesso solicitado. Ao chamar uma API REST protegida, o token é inserido no campo de cabeçalho de solicitação `Authorization` como um token "portador", permitindo que a API autentique o chamador. | 
| `expires_on` | O carimbo de data/hora da expiração do token de acesso; representado como o número de segundos de "1970-01-01T0:0: 0Z UTC" e corresponde à declaração do token `exp` . Nesse caso, o token expira em 2019-08-08T06:10:11 + 00:00 (em RFC 3339)|
| `resource` | O recurso para o qual o token de acesso foi emitido, especificado por meio do `resource` parâmetro de cadeia de caracteres de consulta da solicitação; corresponde à declaração ' AUD ' do token. |


## <a name="acquiring-an-access-token-using-c"></a>Adquirindo um token de acesso usando C #
O acima se torna, em C#:

```C#
namespace Azure.ServiceFabric.ManagedIdentity.Samples
{
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Web;
    using Newtonsoft.Json;

    /// <summary>
    /// Type representing the response of the SF Managed Identity endpoint for token acquisition requests.
    /// </summary>
    [JsonObject]
    public sealed class ManagedIdentityTokenResponse
    {
        [JsonProperty(Required = Required.Always, PropertyName = "token_type")]
        public string TokenType { get; set; }

        [JsonProperty(Required = Required.Always, PropertyName = "access_token")]
        public string AccessToken { get; set; }

        [JsonProperty(PropertyName = "expires_on")]
        public string ExpiresOn { get; set; }

        [JsonProperty(PropertyName = "resource")]
        public string Resource { get; set; }
    }

    /// <summary>
    /// Sample class demonstrating access token acquisition using Managed Identity.
    /// </summary>
    public sealed class AccessTokenAcquirer
    {
        /// <summary>
        /// Acquire an access token.
        /// </summary>
        /// <returns>Access token</returns>
        public static async Task<string> AcquireAccessTokenAsync()
        {
            var managedIdentityEndpoint = Environment.GetEnvironmentVariable("IDENTITY_ENDPOINT");
            var managedIdentityAuthenticationCode = Environment.GetEnvironmentVariable("IDENTITY_HEADER");
            var managedIdentityServerThumbprint = Environment.GetEnvironmentVariable("IDENTITY_SERVER_THUMBPRINT");
            // Latest api version, 2019-07-01-preview is still supported.
            var managedIdentityApiVersion = Environment.GetEnvironmentVariable("IDENTITY_API_VERSION");
            var managedIdentityAuthenticationHeader = "secret";
            var resource = "https://management.azure.com/";

            var requestUri = $"{managedIdentityEndpoint}?api-version={managedIdentityApiVersion}&resource={HttpUtility.UrlEncode(resource)}";

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(managedIdentityAuthenticationHeader, managedIdentityAuthenticationCode);
            
            var handler = new HttpClientHandler();
            handler.ServerCertificateCustomValidationCallback = (httpRequestMessage, cert, certChain, policyErrors) =>
            {
                // Do any additional validation here
                if (policyErrors == SslPolicyErrors.None)
                {
                    return true;
                }
                return 0 == string.Compare(cert.GetCertHashString(), managedIdentityServerThumbprint, StringComparison.OrdinalIgnoreCase);
            };

            try
            {
                var response = await new HttpClient(handler).SendAsync(requestMessage)
                    .ConfigureAwait(false);

                response.EnsureSuccessStatusCode();

                var tokenResponseString = await response.Content.ReadAsStringAsync()
                    .ConfigureAwait(false);

                var tokenResponseObject = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);

                return tokenResponseObject.AccessToken;
            }
            catch (Exception ex)
            {
                string errorText = String.Format("{0} \n\n{1}", ex.Message, ex.InnerException != null ? ex.InnerException.Message : "Acquire token failed");

                Console.WriteLine(errorText);
            }

            return String.Empty;
        }
    } // class AccessTokenAcquirer
} // namespace Azure.ServiceFabric.ManagedIdentity.Samples
```
## <a name="accessing-key-vault-from-a-service-fabric-application-using-managed-identity"></a>Acessando Key Vault de um aplicativo Service Fabric usando a identidade gerenciada
Este exemplo baseia-se no acima para demonstrar o acesso a um segredo armazenado em um Key Vault usando a identidade gerenciada.

```C#
        /// <summary>
        /// Probe the specified secret, displaying metadata on success.  
        /// </summary>
        /// <param name="vault">vault name</param>
        /// <param name="secret">secret name</param>
        /// <param name="version">secret version id</param>
        /// <returns></returns>
        public async Task<string> ProbeSecretAsync(string vault, string secret, string version)
        {
            // initialize a KeyVault client with a managed identity-based authentication callback
            var kvClient = new Microsoft.Azure.KeyVault.KeyVaultClient(new Microsoft.Azure.KeyVault.KeyVaultClient.AuthenticationCallback((a, r, s) => { return AuthenticationCallbackAsync(a, r, s); }));

            Log(LogLevel.Info, $"\nRunning with configuration: \n\tobserved vault: {config.VaultName}\n\tobserved secret: {config.SecretName}\n\tMI endpoint: {config.ManagedIdentityEndpoint}\n\tMI auth code: {config.ManagedIdentityAuthenticationCode}\n\tMI auth header: {config.ManagedIdentityAuthenticationHeader}");
            string response = String.Empty;

            Log(LogLevel.Info, "\n== {DateTime.UtcNow.ToString()}: Probing secret...");
            try
            {
                var secretResponse = await kvClient.GetSecretWithHttpMessagesAsync(vault, secret, version)
                    .ConfigureAwait(false);

                if (secretResponse.Response.IsSuccessStatusCode)
                {
                    // use the secret: secretValue.Body.Value;
                    response = String.Format($"Successfully probed secret '{secret}' in vault '{vault}': {PrintSecretBundleMetadata(secretResponse.Body)}");
                }
                else
                {
                    response = String.Format($"Non-critical error encountered retrieving secret '{secret}' in vault '{vault}': {secretResponse.Response.ReasonPhrase} ({secretResponse.Response.StatusCode})");
                }
            }
            catch (Microsoft.Rest.ValidationException ve)
            {
                response = String.Format($"encountered REST validation exception 0x{ve.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}' from {ve.Source}: {ve.Message}");
            }
            catch (KeyVaultErrorException kvee)
            {
                response = String.Format($"encountered KeyVault exception 0x{kvee.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {kvee.Response.ReasonPhrase} ({kvee.Response.StatusCode})");
            }
            catch (Exception ex)
            {
                // handle generic errors here
                response = String.Format($"encountered exception 0x{ex.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {ex.Message}");
            }

            Log(LogLevel.Info, response);

            return response;
        }

        /// <summary>
        /// KV authentication callback, using the application's managed identity.
        /// </summary>
        /// <param name="authority">The expected issuer of the access token, from the KV authorization challenge.</param>
        /// <param name="resource">The expected audience of the access token, from the KV authorization challenge.</param>
        /// <param name="scope">The expected scope of the access token; not currently used.</param>
        /// <returns>Access token</returns>
        public async Task<string> AuthenticationCallbackAsync(string authority, string resource, string scope)
        {
            Log(LogLevel.Verbose, $"authentication callback invoked with: auth: {authority}, resource: {resource}, scope: {scope}");
            var encodedResource = HttpUtility.UrlEncode(resource);

            // This sample does not illustrate the caching of the access token, which the user application is expected to do.
            // For a given service, the caching key should be the (encoded) resource uri. The token should be cached for a period
            // of time at most equal to its remaining validity. The 'expires_on' field of the token response object represents
            // the number of seconds from Unix time when the token will expire. You may cache the token if it will be valid for at
            // least another short interval (1-10s). If its expiration will occur shortly, don't cache but still return it to the 
            // caller. The MI endpoint will not return an expired token.
            // Sample caching code:
            //
            // ManagedIdentityTokenResponse tokenResponse;
            // if (responseCache.TryGetCachedItem(encodedResource, out tokenResponse))
            // {
            //     Log(LogLevel.Verbose, $"cache hit for key '{encodedResource}'");
            //
            //     return tokenResponse.AccessToken;
            // }
            //
            // Log(LogLevel.Verbose, $"cache miss for key '{encodedResource}'");
            //
            // where the response cache is left as an exercise for the reader. MemoryCache is a good option, albeit not yet available on .net core.

            var requestUri = $"{config.ManagedIdentityEndpoint}?api-version={config.ManagedIdentityApiVersion}&resource={encodedResource}";
            Log(LogLevel.Verbose, $"request uri: {requestUri}");

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(config.ManagedIdentityAuthenticationHeader, config.ManagedIdentityAuthenticationCode);
            Log(LogLevel.Verbose, $"added header '{config.ManagedIdentityAuthenticationHeader}': '{config.ManagedIdentityAuthenticationCode}'");

            var response = await httpClient.SendAsync(requestMessage)
                .ConfigureAwait(false);
            Log(LogLevel.Verbose, $"response status: success: {response.IsSuccessStatusCode}, status: {response.StatusCode}");

            response.EnsureSuccessStatusCode();

            var tokenResponseString = await response.Content.ReadAsStringAsync()
                .ConfigureAwait(false);

            var tokenResponse = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);
            Log(LogLevel.Verbose, "deserialized token response; returning access code..");

            // Sample caching code (continuation):
            // var expiration = DateTimeOffset.FromUnixTimeSeconds(Int32.Parse(tokenResponse.ExpiresOn));
            // if (expiration > DateTimeOffset.UtcNow.AddSeconds(5.0))
            //    responseCache.AddOrUpdate(encodedResource, tokenResponse, expiration);

            return tokenResponse.AccessToken;
        }

        private string PrintSecretBundleMetadata(SecretBundle bundle)
        {
            StringBuilder strBuilder = new StringBuilder();

            strBuilder.AppendFormat($"\n\tid: {bundle.Id}\n");
            strBuilder.AppendFormat($"\tcontent type: {bundle.ContentType}\n");
            strBuilder.AppendFormat($"\tmanaged: {bundle.Managed}\n");
            strBuilder.AppendFormat($"\tattributes:\n");
            strBuilder.AppendFormat($"\t\tenabled: {bundle.Attributes.Enabled}\n");
            strBuilder.AppendFormat($"\t\tnbf: {bundle.Attributes.NotBefore}\n");
            strBuilder.AppendFormat($"\t\texp: {bundle.Attributes.Expires}\n");
            strBuilder.AppendFormat($"\t\tcreated: {bundle.Attributes.Created}\n");
            strBuilder.AppendFormat($"\t\tupdated: {bundle.Attributes.Updated}\n");
            strBuilder.AppendFormat($"\t\trecoveryLevel: {bundle.Attributes.RecoveryLevel}\n");

            return strBuilder.ToString();
        }

        private enum LogLevel
        {
            Info,
            Verbose
        };

        private void Log(LogLevel level, string message)
        {
            if (level != LogLevel.Verbose
                || config.DoVerboseLogging)
            {
                Console.WriteLine(message);
            }
        }

```

## <a name="error-handling"></a>Tratamento de erros
O campo ' código de status ' do cabeçalho de resposta HTTP indica o status de êxito da solicitação; um status ' 200 OK ' indica êxito e a resposta incluirá o token de acesso conforme descrito acima. Veja a seguir uma breve enumeração de possíveis respostas de erro.

| Código de status | Motivo do erro | Como tratar |
| ----------- | ------------ | ------------- |
| 404 Não Encontrado: | Código de autenticação desconhecido ou o aplicativo não recebeu uma identidade gerenciada. | Corrija a instalação do aplicativo ou o código de aquisição de token. |
| 429 Número excessivo de solicitações. |  Limite de limitação atingido, imposto pelo AAD ou pelo it. | Tentar novamente com Retirada Exponencial. Consulte as diretrizes abaixo. |
| o erro 4xx na solicitação. | Um ou mais parâmetros de solicitação estava incorreto. | Não tente novamente.  Examine os detalhes do erro para obter mais informações.  Os erros 4xx são erros de tempo de design.|
| Erro 5xx do serviço. | O subsistema de identidade gerenciada ou Azure Active Directory retornou um erro transitório. | É seguro tentar novamente após alguns instantes. Você pode atingir uma condição de limitação (429) ao tentar novamente.|

Se ocorrer um erro, o corpo da resposta HTTP correspondente conterá um objeto JSON com os detalhes do erro:

| Elemento | Descrição |
| ------- | ----------- |
| code | Código do erro. |
| correlationId | Uma ID de correlação que pode ser usada para depuração. |
| message | Descrição detalhada do erro. **As descrições de erro podem ser alteradas a qualquer momento. Não dependa da própria mensagem de erro.**|

Erro de exemplo:
```json
{"error":{"correlationId":"7f30f4d3-0f3a-41e0-a417-527f21b3848f","code":"SecretHeaderNotFound","message":"Secret is not found in the request headers."}}
```

A seguir está uma lista de erros típicos de Service Fabric específicos para identidades gerenciadas:

| Código | Mensagem | Descrição | 
| ----------- | ----- | ----------------- |
| SecretHeaderNotFound | O segredo não foi encontrado nos cabeçalhos da solicitação. | O código de autenticação não foi fornecido com a solicitação. | 
| ManagedIdentityNotFound | Identidade gerenciada não encontrada para o host de aplicativo especificado. | O aplicativo não tem nenhuma identidade ou o código de autenticação é desconhecido. |
| ArgumentNullOrEmpty | O parâmetro ' Resource ' não deve ser uma cadeia de caracteres nula ou vazia. | O recurso (público) não foi fornecido na solicitação. |
| InvalidApiVersion | Não há suporte para a versão de API ' '. A versão com suporte é ' 2019-07-01-preview '. | Versão de API ausente ou sem suporte especificada no URI de solicitação. |
| InternalServerError | Ocorreu um erro. | Foi encontrado um erro no subsistema de identidade gerenciada, possivelmente fora da pilha de Service Fabric. A causa mais provável é um valor incorreto especificado para o recurso (verifique se há '/' à direita?) | 

## <a name="retry-guidance"></a>Repita a orientação 

Normalmente, o único código de erro com nova tentativa é 429 (muitas solicitações); erros de servidor interno/códigos de erro 5xx podem ser tentados novamente, embora a causa possa ser permanente. 

Limites de limitação se aplicam ao número de chamadas feitas ao subsistema de identidade gerenciada – especificamente as dependências de "upstream" (o serviço do Azure de identidade gerenciada ou o serviço de token seguro). O Service Fabric armazena em cache tokens em vários níveis no pipeline, mas, considerando a natureza distribuída dos componentes envolvidos, o chamador pode experimentar respostas de limitação inconsistentes (ou seja, limitado em um nó/instância de um aplicativo, mas não em um nó diferente ao solicitar um token para a mesma identidade). Quando a condição de limitação é definida, as solicitações subsequentes do mesmo aplicativo podem falhar com o código de status HTTP 429 (muitas solicitações) até que a condição seja apagada.  

É recomendável que as solicitações que falharam devido à limitação sejam repetidas com uma retirada exponencial, da seguinte maneira: 

| Índice de chamadas | Ação ao receber 429 | 
| --- | --- | 
| 1 | Aguarde 1 segundo e tente novamente |
| 2 | Aguarde 2 segundos e tente novamente |
| 3 | Aguarde 4 segundos e tente novamente |
| 4 | Aguarde 8 segundos e tente novamente |
| 4 | Aguarde 8 segundos e tente novamente |
| 5 | Aguarde 16 segundos e tente novamente |

## <a name="resource-ids-for-azure-services"></a>IDs de recurso para serviços do Azure
Consulte [Serviços do Azure que dão suporte à autenticação do Azure ad](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) para obter uma lista de recursos que dão suporte ao Azure AD e suas respectivas IDs de recursos.

## <a name="next-steps"></a>Próximas etapas
* [Implantar um aplicativo de Service Fabric do Azure com uma identidade gerenciada atribuída pelo sistema](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Implantar um aplicativo de Service Fabric do Azure com uma identidade gerenciada atribuída pelo usuário](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Conceder a um aplicativo de Service Fabric do Azure acesso a outros recursos do Azure](./how-to-grant-access-other-resources.md)
* [Explorar um aplicativo de exemplo usando Service Fabric identidade gerenciada](https://github.com/Azure-Samples/service-fabric-managed-identity)
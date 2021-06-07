---
title: Como usar pontos de extremidade privados com os serviços de fala
titleSuffix: Azure Cognitive Services
description: Saiba como usar os serviços de fala com pontos de extremidade privados fornecidos pelo link privado do Azure
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: alexeyo
ms.openlocfilehash: 6971c6f0959135c7de1f41bcd49adde514f87941
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625476"
---
# <a name="use-speech-services-through-a-private-endpoint"></a>Usar os serviços de fala por meio de um ponto de extremidade privado

O [link privado do Azure](../../private-link/private-link-overview.md) permite que você se conecte a serviços no Azure usando um [ponto de extremidade privado](../../private-link/private-endpoint-overview.md). Um ponto de extremidade privado é um endereço IP privado acessível somente dentro de uma [rede virtual](../../virtual-network/virtual-networks-overview.md) específica e uma sub-rede.

Este artigo explica como configurar e usar pontos de extremidade privados e de vínculo privado com os serviços de fala nos serviços cognitivas do Azure.
Este artigo descreve como remover pontos de extremidade privados posteriormente, mas ainda usar o recurso de fala.

> [!NOTE]
> Antes de prosseguir, examine [como usar redes virtuais com serviços cognitivas](../cognitive-services-virtual-networks.md).



## <a name="create-a-custom-domain-name"></a>Criar um nome de domínio personalizado

Pontos de extremidade privados exigem um [nome de subdomínio personalizado para serviços cognitivas](../cognitive-services-custom-subdomains.md). Use as instruções a seguir para criar um para o recurso de fala.

> [!WARNING]
> Um recurso de fala que usa um nome de domínio personalizado interage com os serviços de fala de forma diferente.
> Talvez seja necessário ajustar o código do aplicativo para usar um recurso de fala com um ponto de extremidade privado e também para usar um recurso de fala sem _nenhum_ ponto de extremidade privado.
> Ambos os cenários podem ser necessários porque a mudança para o nome de domínio personalizado _não_ é reversível.
>
> Quando você ativa um nome de domínio personalizado, a operação [não é reversível](../cognitive-services-custom-subdomains.md#can-i-change-a-custom-domain-name). A única maneira de voltar para o [nome regional](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) é criar um novo recurso de fala.
>
> Se o seu recurso de fala tiver muitos modelos personalizados e projetos associados criados por meio do [Speech Studio](https://speech.microsoft.com/), é altamente recomendável tentar a configuração com um recurso de teste antes de modificar o recurso usado na produção.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para criar um nome de domínio personalizado usando o portal do Azure, siga estas etapas:

1. Vá para o [portal do Azure](https://portal.azure.com/) e entre em sua conta do Azure.
1. Selecione o recurso de fala necessário.
1. No grupo **Gerenciamento de recursos** no painel esquerdo, selecione **rede**.
1. Na guia **firewalls e redes virtuais** , selecione **gerar nome de domínio personalizado**. Um novo painel direito é exibido com instruções para criar um subdomínio personalizado exclusivo para seu recurso.
1. No painel **gerar nome de domínio personalizado** , insira um nome de domínio personalizado. Seu domínio personalizado completo terá a seguinte aparência: `https://{your custom name}.cognitiveservices.azure.com` . 
    
    Lembre-se de que, depois de criar um nome de domínio personalizado, ele _não poderá_ ser alterado.
    
    Depois de inserir o nome de domínio personalizado, selecione **salvar**.
1. Após a conclusão da operação, no grupo **Gerenciamento de recursos** , selecione **chaves e ponto de extremidade**. Confirme se o novo nome do ponto de extremidade do recurso é iniciado dessa maneira: `https://{your custom name}.cognitiveservices.azure.com` .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para criar um nome de domínio personalizado usando o PowerShell, confirme se o computador tem o PowerShell versão 7. x ou posterior com o módulo Azure PowerShell versão 5.1.0 ou posterior. Para ver as versões dessas ferramentas, siga estas etapas:

1. Em uma janela do PowerShell, digite:

    `$PSVersionTable`

    Confirme se o `PSVersion` valor é 7. x ou posterior. Para atualizar o PowerShell, siga as instruções em [Instalando várias versões do PowerShell](/powershell/scripting/install/installing-powershell).

1. Em uma janela do PowerShell, digite:

    `Get-Module -ListAvailable Az`

    Se nada aparecer ou se essa versão do módulo Azure PowerShell for anterior à 5.1.0, siga as instruções em [instalar o módulo Azure PowerShell](/powershell/azure/install-Az-ps) para atualizar.

Antes de prosseguir, execute `Connect-AzAccount` para criar uma conexão com o Azure.

## <a name="verify-that-a-custom-domain-name-is-available"></a>Verifique se um nome de domínio personalizado está disponível

Verifique se o domínio personalizado que você deseja usar está disponível. O código a seguir confirma que o domínio está disponível usando a operação [verificar disponibilidade de domínio](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) na API REST dos serviços cognitivas.

> [!TIP]
> O código a seguir *não* funcionará no Azure cloud Shell.

```azurepowershell
$subId = "Your Azure subscription Id"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains the Speech resource.
# You can skip this step if your Azure account has only one active subscription.
Set-AzContext -SubscriptionId $subId

# Prepare the OAuth token to use in the request to the Cognitive Services REST API.
$Context = Get-AzContext
$AccessToken = (Get-AzAccessToken -TenantId $Context.Tenant.Id).Token
$token = ConvertTo-SecureString -String $AccessToken -AsPlainText -Force

# Prepare and send the request to the Cognitive Services REST API.
$uri = "https://management.azure.com/subscriptions/" + $subId + `
    "/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18"
$body = @{
subdomainName = $subdomainName
type = "Microsoft.CognitiveServices/accounts"
}
$jsonBody = $body | ConvertTo-Json
Invoke-RestMethod -Method Post -Uri $uri -ContentType "application/json" -Authentication Bearer `
    -Token $token -Body $jsonBody | Format-List
```
Se o nome desejado estiver disponível, você verá uma resposta como esta:
```azurepowershell
isSubdomainAvailable : True
reason               :
type                 :
subdomainName        : my-custom-name
```
Se o nome já estiver em uso, você verá a seguinte resposta:
```azurepowershell
isSubdomainAvailable : False
reason               : Sub domain name 'my-custom-name' is already used. Please pick a different name.
type                 :
subdomainName        : my-custom-name
```
## <a name="create-your-custom-domain-name"></a>Criar seu nome de domínio personalizado

Para ativar um nome de domínio personalizado para o recurso de fala selecionado, use o cmdlet [set-AzCognitiveServicesAccount](/powershell/module/az.cognitiveservices/set-azcognitiveservicesaccount) .

> [!WARNING]
> Depois que o código a seguir for executado com êxito, você criará um nome de domínio personalizado para seu recurso de fala. Lembre-se de que esse nome *não pode* ser alterado.

```azurepowershell
$resourceGroup = "Resource group name where Speech resource is located"
$speechResourceName = "Your Speech resource name"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains the Speech resource.
# You can skip this step if your Azure account has only one active subscription.
$subId = "Your Azure subscription Id"
Set-AzContext -SubscriptionId $subId

# Set the custom domain name to the selected resource.
# WARNING: THIS CANNOT BE CHANGED OR UNDONE!
Set-AzCognitiveServicesAccount -ResourceGroupName $resourceGroup `
    -Name $speechResourceName -CustomSubdomainName $subdomainName
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

Esta seção requer a versão mais recente do CLI do Azure. Se você está usando o Azure Cloud Shell, a última versão já está instalada.

## <a name="verify-that-the-custom-domain-name-is-available"></a>Verifique se o nome de domínio personalizado está disponível

Verifique se o domínio personalizado que você deseja usar é gratuito. Use o método [verificar disponibilidade do domínio](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) da API REST dos serviços cognitivas.

Copie o seguinte bloco de código, insira seu nome de domínio personalizado preferencial e salve-o no arquivo `subdomain.json` .

```json
{
    "subdomainName": "custom domain name",
    "type": "Microsoft.CognitiveServices/accounts"
}
```

Copie o arquivo para a pasta atual ou carregue-o no Azure Cloud Shell e execute o comando a seguir. Substitua `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` por sua ID da assinatura do Azure.

```azurecli-interactive
az rest --method post --url "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18" --body @subdomain.json
```
Se o nome desejado estiver disponível, você verá uma resposta como esta:
```azurecli
{
  "isSubdomainAvailable": true,
  "reason": null,
  "subdomainName": "my-custom-name",
  "type": null
}
```

Se o nome já estiver em uso, você verá a seguinte resposta:
```azurecli
{
  "isSubdomainAvailable": false,
  "reason": "Sub domain name 'my-custom-name' is already used. Please pick a different name.",
  "subdomainName": "my-custom-name",
  "type": null
}
```
## <a name="turn-on-a-custom-domain-name"></a>Ativar um nome de domínio personalizado

Para usar um nome de domínio personalizado com o recurso de fala selecionado, use o comando [AZ cognitivaservices Account Update](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_update) .

Selecione a assinatura do Azure que contém o recurso de fala. Se sua conta do Azure tiver apenas uma assinatura ativa, você poderá ignorar esta etapa. Substitua `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` por sua ID da assinatura do Azure.
```azurecli-interactive
az account set --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
Defina o nome de domínio personalizado para o recurso selecionado. Substitua os valores de parâmetro de exemplo pelos reais e execute o comando a seguir.

> [!WARNING]
> Após a execução bem-sucedida do comando a seguir, você criará um nome de domínio personalizado para seu recurso de fala. Lembre-se de que esse nome *não pode* ser alterado.

```azurecli
az cognitiveservices account update --name my-speech-resource-name --resource-group my-resource-group-name --custom-domain my-custom-name
```

***

## <a name="turn-on-private-endpoints"></a>Ativar pontos de extremidade privados

É recomendável usar a [zona DNS privada](../../dns/private-dns-overview.md) anexada à rede virtual com as atualizações necessárias para os pontos de extremidade privados. Você pode criar uma zona DNS privada durante o processo de provisionamento. Se você estiver usando seu próprio servidor DNS, talvez também precise alterar a configuração do DNS.

Decida sobre uma estratégia de DNS *antes* de provisionar pontos de extremidade privados para um recurso de fala de produção. E testar as alterações de DNS, especialmente se você usar seu próprio servidor DNS.

Use um dos artigos a seguir para criar pontos de extremidade privados.
Esses artigos usam um aplicativo Web como um recurso de exemplo para disponibilizar por meio de pontos de extremidade privados.

- [Criar um ponto de extremidade privado usando o portal do Azure](../../private-link/create-private-endpoint-portal.md)
- [Criar um ponto de extremidade privado usando o Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)
- [Criar um ponto de extremidade privado usando a CLI do Azure](../../private-link/create-private-endpoint-cli.md)

Use esses parâmetros em vez dos parâmetros no artigo que você escolheu:

| Configuração             | Valor                                    |
|---------------------|------------------------------------------|
| Tipo de recurso       | **Microsoft. Cognitivaservices/contas** |
| Recurso            | **\<your-speech-resource-name>**         |
| Sub-recurso de destino | **conta**                              |

**DNS para pontos de extremidade privados:** Examine os princípios gerais do [DNS para pontos de extremidade privados em recursos de serviços cognitivas](../cognitive-services-virtual-networks.md#dns-changes-for-private-endpoints). Em seguida, confirme se a configuração de DNS está funcionando corretamente executando as verificações descritas nas seções a seguir.

### <a name="resolve-dns-from-the-virtual-network"></a>Resolver o DNS da rede virtual

Essa verificação é *necessária*.

Siga estas etapas para testar a entrada DNS personalizada de sua rede virtual:

1. Faça logon em uma máquina virtual localizada na rede virtual à qual você anexou seu ponto de extremidade privado. 
1. Abra um prompt de comando do Windows ou um shell bash, execute `nslookup` e confirme que ele resolve com êxito o nome de domínio personalizado de seu recurso.

   ```dos
   C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
   Server:  UnKnown
   Address:  168.63.129.16

   Non-authoritative answer:
   Name:    my-private-link-speech.privatelink.cognitiveservices.azure.com
   Address:  172.28.0.10
   Aliases:  my-private-link-speech.cognitiveservices.azure.com
   ```

1. Confirme se o endereço IP corresponde ao endereço IP do seu ponto de extremidade privado.

### <a name="resolve-dns-from-other-networks"></a>Resolver o DNS de outras redes

Execute essa verificação somente se você ativou a opção todas as **redes** ou a opção **redes selecionadas e acesso a pontos de extremidade particulares** na seção **rede** do recurso. 

Se você planeja acessar o recurso usando apenas um ponto de extremidade privado, você pode ignorar esta seção.

1. Faça logon em um computador conectado a uma rede que tenha permissão para acessar o recurso.
2. Abra um prompt de comando do Windows ou shell bash, execute `nslookup` e confirme que ele resolve com êxito o nome de domínio personalizado do recurso.

   ```dos
   C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
   Server:  UnKnown
   Address:  fe80::1

   Non-authoritative answer:
   Name:    vnetproxyv1-weu-prod.westeurope.cloudapp.azure.com
   Address:  13.69.67.71
   Aliases:  my-private-link-speech.cognitiveservices.azure.com
             my-private-link-speech.privatelink.cognitiveservices.azure.com
             westeurope.prod.vnet.cog.trafficmanager.net
   ```

> [!NOTE]
> O endereço IP resolvido aponta para um ponto de extremidade de proxy de rede virtual, que expede o tráfego de rede para o ponto de extremidade privado para o recurso de serviços cognitivas. O comportamento será diferente para um recurso com um nome de domínio personalizado, mas *sem* pontos de extremidade privados. Consulte [esta seção](#dns-configuration) para obter detalhes.

## <a name="adjust-an-application-to-use-a-speech-resource-with-a-private-endpoint"></a>Ajustar um aplicativo para usar um recurso de fala com um ponto de extremidade privado

Um recurso de fala com um domínio personalizado interage com os serviços de fala de uma maneira diferente. Isso é verdadeiro para um recurso de fala habilitado para domínio personalizado com e sem pontos de extremidade privados. As informações nesta seção se aplicam a ambos os cenários.

Siga as instruções nesta seção para ajustar os aplicativos e as soluções existentes para usar um recurso de fala com um nome de domínio personalizado e um ponto de extremidade privado ativado.

Um recurso de fala com um nome de domínio personalizado e um ponto de extremidade privado ativado usa uma maneira diferente de interagir com os serviços de fala. Esta seção explica como usar esse recurso com as APIs REST dos serviços de fala e o [SDK do Speech](speech-sdk.md).

> [!NOTE]
> Um recurso de fala sem pontos de extremidade privados que usa um nome de domínio personalizado também tem uma maneira especial de interagir com os serviços de fala.
> Dessa forma, é diferente do cenário de um recurso de fala que usa um ponto de extremidade privado. Isso é importante considerar porque você pode optar por remover pontos de extremidade privados posteriormente.
> Consulte _ajustar um aplicativo para usar um recurso de fala sem pontos de extremidade privados_ posteriormente neste artigo.

### <a name="speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-rest-apis"></a>Recurso de fala com um nome de domínio personalizado e um ponto de extremidade privado: uso com as APIs REST

Usaremos `my-private-link-speech.cognitiveservices.azure.com` como um nome DNS de recurso de fala de exemplo (domínio personalizado) para esta seção.

Os serviços de fala têm APIs REST para [conversão de fala em texto](rest-speech-to-text.md) e [texto em fala](rest-text-to-speech.md). Considere as seguintes informações para o cenário de ponto de extremidade particular habilitado.

A conversão de fala em texto tem duas APIs REST. Cada API atende a uma finalidade diferente, usa pontos de extremidade diferentes e requer uma abordagem diferente quando você o estiver usando no cenário particular-habilitado para ponto de extremidade.

As APIs REST de conversão de fala em texto são:
- [API REST de conversão de fala em texto v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30), que é usada para a transcrição e a [fala personalizada](custom-speech-overview.md)do [lote](batch-transcription.md) . v 3.0 é um [sucessor da v 2.0](./migrate-v2-to-v3.md)
- [API REST de conversão de fala em texto para áudio curto](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio), que é usado para transcrição online 

O uso da API REST de conversão de fala em texto para áudio curto e a API REST de conversão de texto em fala no cenário do ponto de extremidade privado é o mesmo. É equivalente ao caso do [SDK de fala](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk) descrito posteriormente neste artigo. 

A API REST de conversão de fala em texto v 3.0 usa um conjunto diferente de pontos de extremidade, portanto, requer uma abordagem diferente para o cenário de ponto de extremidade privada habilitado.

As subseções a seguir descrevem ambos os casos.

#### <a name="speech-to-text-rest-api-v30"></a>API REST de conversão de fala em texto v 3.0

Normalmente, os recursos de fala usam [pontos de extremidade regionais de serviços cognitivas](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) para se comunicar com a [API REST de fala em texto v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30). Esses recursos têm o seguinte formato de nomenclatura: <p/>`{region}.api.cognitive.microsoft.com`.

Este é um exemplo de URL de solicitação:

```http
https://westeurope.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions
```

> [!NOTE]
> Consulte [Este artigo](sovereign-clouds.md) para os pontos de extremidade do Azure governamental e do Azure China.

Depois de ativar um domínio personalizado para um recurso de fala (que é necessário para pontos de extremidade privados), esse recurso usará o seguinte padrão de nome DNS para o ponto de extremidade básico da API REST: <p/>`{your custom name}.cognitiveservices.azure.com`

Isso significa que, em nosso exemplo, o nome do ponto de extremidade da API REST será: <p/>`my-private-link-speech.cognitiveservices.azure.com`

E a URL de solicitação de exemplo precisa ser convertida em:
```http
https://my-private-link-speech.cognitiveservices.azure.com/speechtotext/v3.0/transcriptions
```
Essa URL deve ser acessível da rede virtual com o ponto de extremidade privado anexado (forneceu a [resolução de DNS correta](#resolve-dns-from-the-virtual-network)).

Depois de ativar um nome de domínio personalizado para um recurso de fala, você normalmente substitui o nome do host em todas as URLs de solicitação pelo novo nome de host de domínio personalizado. Todas as outras partes da solicitação (como o caminho `/speechtotext/v3.0/transcriptions` no exemplo anterior) permanecem as mesmas.

> [!TIP]
> Alguns clientes desenvolvem aplicativos que usam a parte da região do nome DNS do ponto de extremidade regional (por exemplo, para enviar a solicitação para o recurso de fala implantado na região específica do Azure).
>
> Um domínio personalizado para um recurso de fala *não* contém informações sobre a região em que o recurso foi implantado. Portanto, a lógica do aplicativo descrita anteriormente *não* funcionará e precisará ser alterada.

#### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>API REST de fala em texto para API REST de áudio curto e de conversão de texto em fala

A [API REST de conversão de fala em texto para áudio curto](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) e a API REST de conversão de [texto em fala](rest-text-to-speech.md) usam dois tipos de pontos de extremidade:
- [Pontos de extremidade regionais de serviços cognitivas](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) para se comunicar com a API REST de serviços cognitivas para obter um token de autorização
- Pontos de extremidade especiais para todas as outras operações

> [!NOTE]
> Consulte [Este artigo](sovereign-clouds.md) para os pontos de extremidade do Azure governamental e do Azure China.

A descrição detalhada dos pontos de extremidade especiais e como sua URL deve ser transformada para um recurso de fala habilitado para ponto de extremidade particular é fornecida nesta [subseção](#construct-endpoint-url) sobre o uso com o SDK de fala. O mesmo princípio descrito para o SDK se aplica à API REST de conversão de fala em texto para áudio curto e a API REST de conversão de texto em fala.

Familiarize-se com o material na subseção mencionada no parágrafo anterior e veja o exemplo a seguir. O exemplo descreve a API REST de conversão de texto em fala. O uso da API REST de conversão de fala em texto para áudio curto é totalmente equivalente.

> [!NOTE]
> Quando você estiver usando a API REST de conversão de fala em texto para a API REST de áudio curto e de conversão de texto em fala em cenários de ponto de extremidade privado, use uma chave de assinatura passada pelo `Ocp-Apim-Subscription-Key` cabeçalho. (Veja os detalhes da [API REST de fala em texto para a API REST de áudio curto](rest-speech-to-text.md#request-headers) e de conversão de [texto em fala](rest-text-to-speech.md#request-headers))
>
> Usar um token de autorização e passá-lo para o ponto de extremidade especial por meio do `Authorization` cabeçalho *só* funcionará se você tiver ativado a opção acesso a **todas as redes** na seção **rede** do seu recurso de fala. Em outros casos, você receberá `Forbidden` um `BadRequest` erro ou ao tentar obter um token de autorização.

**Exemplo de uso da API REST de conversão de texto em fala**

Usaremos Europa Ocidental como uma região do Azure de exemplo e `my-private-link-speech.cognitiveservices.azure.com` como um nome DNS de recurso de fala de exemplo (domínio personalizado). O nome de domínio personalizado `my-private-link-speech.cognitiveservices.azure.com` em nosso exemplo pertence ao recurso de fala criado na região Europa Ocidental.

Para obter a lista das vozes com suporte na região, execute a seguinte solicitação:

```http
https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list
```
Veja mais detalhes na [documentação da API REST de conversão de texto em fala](rest-text-to-speech.md).

Para o recurso de fala habilitado para ponto de extremidade privado, a URL do ponto de extremidade para a mesma operação precisa ser modificada. A mesma solicitação terá a seguinte aparência:

```http
https://my-private-link-speech.cognitiveservices.azure.com/tts/cognitiveservices/voices/list
```
Consulte uma explicação detalhada na subseção [construir URL de ponto de extremidade](#construct-endpoint-url) para o SDK de fala.

### <a name="speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk"></a>Recurso de fala com um nome de domínio personalizado e um ponto de extremidade privado: uso com o SDK de fala

Usar o SDK de fala com um nome de domínio personalizado e recursos de fala habilitados para ponto de extremidade privado exige que você examine e provavelmente altere o código do aplicativo.

Usaremos `my-private-link-speech.cognitiveservices.azure.com` como um nome DNS de recurso de fala de exemplo (domínio personalizado) para esta seção.

#### <a name="construct-endpoint-url"></a>URL de ponto de extremidade de construção

Geralmente em cenários de SDK (bem como na API REST de conversão de fala em texto para cenários de API REST de áudio curto e de texto em fala), os recursos de fala usam os pontos de extremidade regionais dedicados para diferentes ofertas de serviço. O formato de nome DNS para esses pontos de extremidade é:

`{region}.{speech service offering}.speech.microsoft.com`

Um exemplo de nome DNS é:

`westeurope.stt.speech.microsoft.com`

Todos os valores possíveis para a região (primeiro elemento do nome DNS) são listados em [regiões com suporte do Speech Service](regions.md). (Consulte [Este artigo](sovereign-clouds.md) para os pontos de extremidade do Azure governamental e do Azure China.) A tabela a seguir apresenta os possíveis valores para a oferta de serviços de fala (segundo elemento do nome DNS):

| Valor do nome DNS | Oferta de serviço de fala                                    |
|----------------|-------------------------------------------------------------|
| `commands`     | [Comandos personalizados](custom-commands.md)                       |
| `convai`       | [Transcrição de Conversas](conversation-transcription.md) |
| `s2s`          | [Tradução de Fala](speech-translation.md)                 |
| `stt`          | [Conversão de fala em texto](speech-to-text.md)                         |
| `tts`          | [Conversão de texto em fala](text-to-speech.md)                         |
| `voice`        | [Voz personalizada](how-to-custom-voice.md)                      |

Portanto, o exemplo anterior ( `westeurope.stt.speech.microsoft.com` ) representa um ponto de extremidade de fala para texto em Europa Ocidental.

Privado-os pontos de extremidade habilitados para ponto final se comunicam com os serviços de fala por meio de um proxy especial. Por isso, *você deve alterar as URLs de conexão do ponto de extremidade*. 

Uma URL de ponto de extremidade "padrão" é semelhante A: <p/>`{region}.{speech service offering}.speech.microsoft.com/{URL path}`

Uma URL de ponto de extremidade particular é semelhante A: <p/>`{your custom name}.cognitiveservices.azure.com/{speech service offering}/{URL path}`

**Exemplo 1.** Um aplicativo está se comunicando usando a seguinte URL (reconhecimento de fala usando o modelo de base para inglês dos EUA em Europa Ocidental):

```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Para usá-lo no cenário particular-habilitado para ponto de extremidade quando o nome de domínio personalizado do recurso de fala é `my-private-link-speech.cognitiveservices.azure.com` , você deve modificar a URL da seguinte maneira:

```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Observe os detalhes:

- O nome do host `westeurope.stt.speech.microsoft.com` é substituído pelo nome do host de domínio personalizado `my-private-link-speech.cognitiveservices.azure.com` .
- O segundo elemento do nome DNS original ( `stt` ) torna-se o primeiro elemento do caminho da URL e precede o caminho original. Portanto, a URL original `/speech/recognition/conversation/cognitiveservices/v1?language=en-US` se torna `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` .

**Exemplo 2.** Um aplicativo usa a seguinte URL para sintetizar a fala em Europa Ocidental usando um modelo de voz personalizado:
```http
https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

A seguinte URL equivalente usa um ponto de extremidade privado, em que o nome de domínio personalizado do recurso de fala é `my-private-link-speech.cognitiveservices.azure.com` :

```http
https://my-private-link-speech.cognitiveservices.azure.com/voice/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

O mesmo princípio, no exemplo 1, é aplicado, mas o elemento key desta vez é `voice` .

#### <a name="modifying-applications"></a>Modificando aplicativos

Siga estas etapas para modificar seu código:

1. Determine a URL do ponto de extremidade do aplicativo:

   - [Ative o registro em log para seu aplicativo](how-to-use-logging.md) e execute-o para registrar a atividade.
   - No arquivo de log, procure `SPEECH-ConnectionUrl` . Em linhas correspondentes, o `value` parâmetro contém a URL completa que seu aplicativo usou para acessar os serviços de fala.

   Exemplo:

   ```
   (114917): 41ms SPX_DBG_TRACE_VERBOSE:  property_bag_impl.cpp:138 ISpxPropertyBagImpl::LogPropertyAndValue: this=0x0000028FE4809D78; name='SPEECH-ConnectionUrl'; value='wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?traffictype=spx&language=en-US'
   ```

   Portanto, a URL usada pelo aplicativo neste exemplo é:

   ```
   wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
   ```

2. Crie uma `SpeechConfig` instância usando uma URL de ponto de extremidade completa:

   1. Modifique o ponto de extremidade que você acabou de determinar, conforme descrito na seção [URL de ponto de extremidade de construção](#construct-endpoint-url) anterior.

   1. Modifique como você cria a instância do `SpeechConfig` . Provavelmente, seu aplicativo está usando algo assim:
      ```csharp
      var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
      ```
      Isso não funcionará para um recurso de fala habilitado para ponto de extremidade privado devido às alterações de nome de host e URL descritas nas seções anteriores. Se você tentar executar o aplicativo existente sem modificações usando a chave de um recurso de ponto de extremidade privado habilitado, receberá um erro de autenticação (401).

      Para fazê-lo funcionar, modifique como você instancia a `SpeechConfig` classe e use a inicialização "do ponto de extremidade"/"com ponto de extremidade". Suponha que tenhamos as duas variáveis definidas a seguir:
      - `subscriptionKey` contém a chave do recurso de fala habilitado para ponto de extremidade privado.
      - `endPoint` contém a URL de ponto de extremidade *modificada* completa (usando o tipo exigido pela linguagem de programação correspondente). Em nosso exemplo, essa variável deve conter:
        ```
        wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
        ```

      Criar uma `SpeechConfig` instância:
      ```csharp
      var config = SpeechConfig.FromEndpoint(endPoint, subscriptionKey);
      ```
      ```cpp
      auto config = SpeechConfig::FromEndpoint(endPoint, subscriptionKey);
      ```
      ```java
      SpeechConfig config = SpeechConfig.fromEndpoint(endPoint, subscriptionKey);
      ```
      ```python
      import azure.cognitiveservices.speech as speechsdk
      speech_config = speechsdk.SpeechConfig(endpoint=endPoint, subscription=subscriptionKey)
      ```
      ```objectivec
      SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithEndpoint:endPoint subscription:subscriptionKey];
      ```

> [!TIP]
> Os parâmetros de consulta especificados no URI do ponto de extremidade não são alterados, mesmo se estiverem definidos por outras APIs. Por exemplo, se o idioma de reconhecimento for definido no URI como parâmetro de consulta `language=en-US` e também for definido como `ru-RU` por meio da propriedade correspondente, a configuração de idioma no URI será usada. Em seguida, o idioma efetivo é `en-US` .
>
> Os parâmetros definidos no URI do ponto de extremidade sempre têm precedência. Outras APIs podem substituir apenas os parâmetros que não são especificados no URI do ponto de extremidade.

Após essa modificação, seu aplicativo deve funcionar com os recursos de fala habilitados para ponto de extremidade privado. Estamos trabalhando em um suporte mais contínuo de cenários de ponto de extremidade privados.

## <a name="adjust-an-application-to-use-a-speech-resource-without-private-endpoints"></a>Ajustar um aplicativo para usar um recurso de fala sem pontos de extremidade privados

Neste artigo, apontamos várias vezes que a habilitação de um domínio personalizado para um recurso de fala é *irreversível*. Esse recurso usará uma maneira diferente de se comunicar com os serviços de fala, em comparação com aqueles que estão usando [nomes de ponto de extremidade regionais](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints).

Esta seção explica como usar um recurso de fala com um nome de domínio personalizado, mas *sem* nenhum ponto de extremidade privado com as APIs REST dos serviços de fala e o [SDK de fala](speech-sdk.md). Isso pode ser um recurso que era usado em um cenário de ponto de extremidade privado, mas, em seguida, tinha seus pontos de extremidades privados excluídos.

### <a name="dns-configuration"></a>Configuração de DNS

Lembre-se de como um nome DNS de domínio personalizado do recurso de fala habilitado para ponto de extremidade privado é [resolvido de redes públicas](#resolve-dns-from-other-networks). Nesse caso, o endereço IP resolvido aponta para um ponto de extremidade de proxy para uma rede virtual. Esse ponto de extremidade é usado para expedir o tráfego de rede para o recurso de serviços cognitivas habilitados para ponto de extremidade privado.

No entanto, quando *todos os* pontos de extremidade privados do recurso são removidos (ou logo após a habilitação do nome de domínio personalizado), o registro CNAME do recurso de fala é reprovisionado. Agora ele aponta para o endereço IP do ponto de [extremidade regional dos serviços cognitivas](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)correspondente.

Portanto, a saída do `nslookup` comando terá a seguinte aparência:
```dos
C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
Server:  UnKnown
Address:  fe80::1

Non-authoritative answer:
Name:    apimgmthskquihpkz6d90kmhvnabrx3ms3pdubscpdfk1tsx3a.cloudapp.net
Address:  13.93.122.1
Aliases:  my-private-link-speech.cognitiveservices.azure.com
          westeurope.api.cognitive.microsoft.com
          cognitiveweprod.trafficmanager.net
          cognitiveweprod.azure-api.net
          apimgmttmdjylckcx6clmh2isu2wr38uqzm63s8n4ub2y3e6xs.trafficmanager.net
          cognitiveweprod-westeurope-01.regional.azure-api.net
```
Compare-o com a saída [desta seção](#resolve-dns-from-other-networks).

### <a name="speech-resource-with-a-custom-domain-name-and-without-private-endpoints-usage-with-the-rest-apis"></a>Recurso de fala com um nome de domínio personalizado e sem pontos de extremidade privados: uso com as APIs REST

#### <a name="speech-to-text-rest-api-v30"></a>API REST de conversão de fala em texto v 3.0

O uso da API REST de fala em texto v 3.0 é totalmente equivalente ao caso de [recursos de fala habilitados para ponto de extremidade privado](#speech-to-text-rest-api-v30).

#### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>API REST de fala em texto para API REST de áudio curto e de conversão de texto em fala

Nesse caso, o uso da API REST de conversão de fala em texto para um áudio curto e uso da API REST de conversão de texto em fala não tem nenhuma diferença do caso geral, com uma exceção. (Consulte a observação a seguir.) Você deve usar ambas as APIs, conforme descrito na [API REST de fala a texto para a documentação da API REST de áudio curto](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) e de [texto em fala](rest-text-to-speech.md) .

> [!NOTE]
> Quando você estiver usando a API REST de conversão de fala em texto para a API REST de áudio curto e de conversão de texto em fala em cenários de domínio personalizado, use uma chave de assinatura passada pelo `Ocp-Apim-Subscription-Key` cabeçalho. (Veja os detalhes da [API REST de fala em texto para a API REST de áudio curto](rest-speech-to-text.md#request-headers) e de conversão de [texto em fala](rest-text-to-speech.md#request-headers))
>
> Usar um token de autorização e passá-lo para o ponto de extremidade especial por meio do `Authorization` cabeçalho *só* funcionará se você tiver ativado a opção acesso a **todas as redes** na seção **rede** do seu recurso de fala. Em outros casos, você receberá `Forbidden` um `BadRequest` erro ou ao tentar obter um token de autorização.

### <a name="speech-resource-with-a-custom-domain-name-and-without-private-endpoints-usage-with-the-speech-sdk"></a>Recurso de fala com um nome de domínio personalizado e sem pontos de extremidade privados: uso com o SDK de fala

Usar o SDK de fala com recursos de fala habilitados para domínio personalizado *sem* pontos de extremidade privados é equivalente ao caso geral, conforme descrito na [documentação do SDK de fala](speech-sdk.md).

Caso você tenha modificado seu código para usar o com um [recurso de fala habilitado para ponto de extremidade privado](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk), considere o seguinte.

Na seção sobre [recursos de fala habilitados para ponto de extremidade privado](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk), explicamos como determinar a URL do ponto de extremidade, modificá-la e fazê-la funcionar por meio da inicialização "do ponto de extremidade"/"com o ponto de extremidade" da `SpeechConfig` instância de classe.

No entanto, se você tentar executar o mesmo aplicativo depois de ter todos os pontos de extremidade privados removidos (permitindo algum tempo para o reprovisionamento de registro DNS correspondente), obterá um erro de serviço interno (404). O motivo é que o [registro DNS](#dns-configuration) aponta para o ponto de extremidade de serviços cognitivas regionais em vez do proxy de rede virtual, e os caminhos de URL como `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` não serão encontrados lá.

Você precisa reverter seu aplicativo para a instanciação padrão do `SpeechConfig` no estilo do código a seguir:

```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```

## <a name="pricing"></a>Preços

Para obter detalhes de preço, confira [Preço do Link Privado do Azure](https://azure.microsoft.com/pricing/details/private-link).

## <a name="learn-more"></a>Saiba mais

* [Link Privado do Azure](../../private-link/private-link-overview.md)
* [SDK da fala](speech-sdk.md)
* [API REST de conversão de fala em texto](rest-speech-to-text.md)
* [API REST conversão de texto em fala](rest-text-to-speech.md)
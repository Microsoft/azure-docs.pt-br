---
title: Coleção de endereços IP do Aplicativo Azure insights | Microsoft Docs
description: Compreendendo como os endereços IP e a localização geográfica são tratados com o Aplicativo Azure insights
ms.topic: conceptual
ms.date: 09/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: 91b3aa07720e39aa8aeeceb9c35e38205e7d7c76
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100584075"
---
# <a name="geolocation-and-ip-address-handling"></a>Geolocalização e manipulação de endereço IP

Este artigo explica como a pesquisa de localização geográfica e o tratamento de endereços IP funcionam em Application Insights juntamente com como modificar o comportamento padrão.

## <a name="default-behavior"></a>Comportamento padrão

Por padrão, os endereços IP são coletados temporariamente, mas não são armazenados em Application Insights. O processo básico é como descrito a seguir:

Quando a telemetria é enviada ao Azure, o endereço IP é usado para fazer uma pesquisa de localização geográfica usando [GeoLite2 de MaxMind](https://dev.maxmind.com/geoip/geoip2/geolite2/). Os resultados dessa pesquisa são usados para popular os campos `client_City` , `client_StateOrProvince` e `client_CountryOrRegion` . Em seguida, o endereço é Descartado e `0.0.0.0` gravado no `client_IP` campo.

* Telemetria do navegador: coletamos temporariamente o endereço IP do remetente. O endereço IP é calculado pelo ponto de extremidade de ingestão.
* Telemetria do servidor: o módulo Application Insights telemetria coleta temporariamente o endereço IP do cliente. O endereço IP não é coletado localmente quando o `X-Forwarded-For` cabeçalho está definido.

Esse comportamento é por design para ajudar a evitar a coleta desnecessária de dados pessoais. Sempre que possível, é recomendável evitar a coleta de dados pessoais. 

## <a name="overriding-default-behavior"></a>Substituindo o comportamento padrão

Embora o padrão seja não coletar endereços IP. Ainda oferecemos a flexibilidade para substituir esse comportamento. No entanto, é recomendável verificar se a coleção não interrompe quaisquer requisitos de conformidade ou regulamentos locais. 

Para saber mais sobre a manipulação de dados pessoais no Application Insights, consulte as [diretrizes para dados pessoais](../logs/personal-data-mgmt.md).

## <a name="storing-ip-address-data"></a>Armazenando dados de endereço IP

Para habilitar a coleta e o armazenamento de IP, a `DisableIpMasking` Propriedade do componente Application insights deve ser definida como `true` . Essa propriedade pode ser definida por meio de modelos de Azure Resource Manager ou chamando a API REST. 

### <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager

```json
{
       "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<resource-name>",
       "name": "<resource-name>",
       "type": "microsoft.insights/components",
       "location": "westcentralus",
       "tags": {
              
       },
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "Flow_Type": "Redfield",
              "Request_Source": "IbizaAIExtension",
              // ...
              "DisableIpMasking": true
       }
}
```

### <a name="portal"></a>Portal 

Se você só precisa modificar o comportamento de um único recurso de Application Insights, use o portal do Azure. 

1. Acesse o recurso de Application insights >  >  **modelo de exportação** de automação 

2. Escolha **Implantar**

    ![Botão com a palavra "implantar" realçada em vermelho](media/ip-collection/deploy.png)

3. Selecione **Editar modelo**.

    ![Botão com a palavra "Editar" realçada em vermelho](media/ip-collection/edit-template.png)

4. Faça as seguintes alterações no JSON para o recurso e, em seguida, selecione **salvar**:

    ![A captura de tela adiciona uma vírgula após "IbizaAIExtension" e adiciona uma nova linha abaixo com "DisableIpMasking": true](media/ip-collection/save.png)

    > [!WARNING]
    > Se você tiver um erro que diz: **_o grupo de recursos está em um local que não tem suporte de um ou mais recursos no modelo. Escolha um grupo de recursos diferente._** Selecione temporariamente um grupo de recursos diferente na lista suspensa e, em seguida, selecione novamente o grupo de recursos original para resolver o erro.

5. Selecione **concordo**  >  **compra**. 

    ![Caixa marcada com palavras "Eu concordo com os termos e condições declarados acima" realçados em vermelho acima de um botão com a palavra "comprar" realçada em vermelho.](media/ip-collection/purchase.png)

    Nesse caso, nada novo está realmente sendo comprado. Estamos atualizando apenas a configuração do recurso de Application Insights existente.

6. Depois que a implantação for concluída, novos dados de telemetria serão registrados.

    Se você selecionar e editar o modelo novamente, verá apenas o modelo padrão sem a propriedade recém-adicionada. Se você não estiver vendo os dados de endereço IP e quiser confirmar que `"DisableIpMasking": true` está definido, execute o PowerShell a seguir: 
    
    ```powershell
    # Replace `Fabrikam-dev` with the appropriate resource and resource group name.
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    Uma lista de propriedades será retornada como resultado. Uma das propriedades deve ser lida `DisableIpMasking: true` . Se você executar o PowerShell antes de implantar a nova propriedade com Azure Resource Manager, a propriedade não existirá.

### <a name="rest-api"></a>API Rest

A carga da [API REST](/rest/api/azure/) para fazer as mesmas modificações é a seguinte:

```
PATCH https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/microsoft.insights/components/<resource-name>?api-version=2018-05-01-preview HTTP/1.1
Host: management.azure.com
Authorization: AUTH_TOKEN
Content-Type: application/json
Content-Length: 54

{
       "location": "<resource location>",
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "DisableIpMasking": true
       }
}
```

## <a name="telemetry-initializer"></a>Inicializador de telemetria

Se precisar de uma alternativa mais flexível do que `DisableIpMasking` o, você pode usar um [inicializador de telemetria](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) para copiar todo ou parte do endereço IP para um campo personalizado. 

# <a name="net"></a>[.NET](#tab/net)

### <a name="aspnet--aspnet-core"></a>ASP.NET/ASP.NET Core

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MyWebApp
{
    public class CloneIPAddress : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            ISupportProperties propTelemetry = telemetry as ISupportProperties;

            if (propTelemetry !=null && !propTelemetry.Properties.ContainsKey("client-ip"))
            {
                string clientIPValue = telemetry.Context.Location.Ip;
                propTelemetry.Properties.Add("client-ip", clientIPValue);
            }
        }
    } 
}
```

> [!NOTE]
> Se não for possível acessar `ISupportProperties` o, verifique e verifique se você está executando a versão estável mais recente do SDK do Application insights. `ISupportProperties` são destinadas a valores de cardinalidade alta, enquanto `GlobalProperties` são mais apropriadas para valores de cardinalidade baixa, como nome da região, nome do ambiente, etc. 

### <a name="enable-telemetry-initializer-for-aspnet"></a>Habilitar inicializador de telemetria para ASP.NET

```csharp
using Microsoft.ApplicationInsights.Extensibility;


namespace MyWebApp
{
    public class MvcApplication : System.Web.HttpApplication
    {
        protected void Application_Start()
        {
              //Enable your telemetry initializer:
              TelemetryConfiguration.Active.TelemetryInitializers.Add(new CloneIPAddress());
        }
    }
}

```

### <a name="enable-telemetry-initializer-for-aspnet-core"></a>Habilitar inicializador de telemetria para ASP.NET Core

Você pode criar seu inicializador de telemetria da mesma maneira para ASP.NET Core como ASP.NET, mas para habilitar o inicializador, use o exemplo a seguir para referência:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, CloneIPAddress>();
}
```
# <a name="nodejs"></a>[Node.js](#tab/nodejs)

### <a name="nodejs"></a>Node.js

```javascript
appInsights.defaultClient.addTelemetryProcessor((envelope) => {
    const baseData = envelope.data.baseData;
    if (appInsights.Contracts.domainSupportsProperties(baseData)) {
        const ipAddress = envelope.tags[appInsights.defaultClient.context.keys.locationIp];
        if (ipAddress) {
            baseData.properties["client-ip"] = ipAddress;
        }
    }
});
```
# <a name="client-side-javascript"></a>[JavaScript do lado do cliente](#tab/javascript)

### <a name="client-side-javascript"></a>JavaScript do lado do cliente

Ao contrário dos SDKs do lado do servidor, o SDK do JavaScript do lado do cliente não calcula o endereço IP. Por padrão, o cálculo do endereço IP para a telemetria do lado do cliente ocorre no ponto de extremidade de ingestão no Azure. 

Se você quiser calcular o endereço IP diretamente no lado do cliente, precisará adicionar sua própria lógica personalizada e usar o resultado para definir a `ai.location.ip` marca. Quando `ai.location.ip` é definido, o cálculo de endereço IP não é executado pelo ponto de extremidade de ingestão e o endereço IP fornecido é usado para a pesquisa de localização geográfica. Nesse cenário, o endereço IP ainda será zerado por padrão. 

Para manter todo o endereço IP calculado de sua lógica personalizada, você pode usar um inicializador de telemetria que copiaria os dados de endereço IP que você forneceu em `ai.location.ip` um campo personalizado separado. Mas, novamente, ao contrário dos SDKs do lado do servidor, sem depender de bibliotecas de terceiros ou sua própria lógica de coleção personalizada, o SDK do lado do cliente não calculará o endereço para você.    


```javascript
appInsights.addTelemetryInitializer((item) => {
    const ipAddress = item.tags && item.tags["ai.location.ip"];
    if (ipAddress) {
        item.baseData.properties = {
            ...item.baseData.properties,
            "client-ip": ipAddress
        };
    }
});

```  

Se os dados do lado do cliente percorrerem um proxy antes de encaminhar para o ponto de extremidade de ingestão, o cálculo do endereço IP poderá mostrar o endereço IP do proxy e não o cliente. 

---

### <a name="view-the-results-of-your-telemetry-initializer"></a>Exibir os resultados do inicializador de telemetria

Se você enviar o novo tráfego para seu site e aguarde alguns minutos. Em seguida, você pode executar uma consulta para confirmar se a coleção está funcionando:

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

Os endereços IP recentemente coletados aparecerão na `customDimensions_client-ip` coluna. A `client-ip` coluna padrão ainda terá todos os quatro octetos zerados. 

Se o teste for de localhost e o valor para `customDimensions_client-ip` for `::1` , esse valor será um comportamento esperado. `::1` representa o endereço de loopback no IPv6. É equivalente a `127.0.0.1` no IPv4.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a [coleta de dados pessoais](../logs/personal-data-mgmt.md) no Application insights.

* Saiba mais sobre como a [coleta de endereços IP](https://apmtips.com/posts/2016-07-05-client-ip-address/) no Application insights funciona. (Este artigo é uma postagem de blog externa mais antiga escrita por um de nossos engenheiros. Ele atinge o comportamento padrão atual em que o endereço IP é registrado como `0.0.0.0` , mas ele entra em mais detalhes sobre a mecânica do interno `ClientIpHeaderTelemetryInitializer` .)

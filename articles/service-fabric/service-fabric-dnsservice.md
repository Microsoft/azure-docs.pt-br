---
title: "Serviço DNS do Azure Service Fabric | Microsoft Docs"
description: "Use o serviço DNS do Service Fabric para descobrir microsserviços no cluster."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/27/2017
ms.author: msfussell
ms.openlocfilehash: 9871bc5aa4e74ab0faef401d67c4e9558eb5e14b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="dns-service-in-azure-service-fabric"></a>Serviço DNS no Azure Service Fabric
O Serviço DNS é um serviço do sistema opcional que pode ser habilitado no cluster para descobrir outros serviços usando o protocolo DNS.

Muitos serviços, especialmente serviços em contêineres, podem ter um nome de URL existente e ter a capacidade de resolvê-los usando o protocolo DNS padrão (em vez do protocolo do Serviço de Nomeação) é desejável, especialmente em cenários “lift-and-shift”. O serviço DNS permite mapear nomes DNS para um nome de serviço e, portanto, resolver endereços IP do ponto de extremidade. 

O serviço DNS mapeia nomes DNS para nomes de serviço, que por sua vez, são resolvidos pelo serviço de nomenclatura para retornar o ponto de extremidade de serviço. O nome DNS do serviço é fornecido no momento da criação. 

![pontos de extremidade de serviço][0]

## <a name="enabling-the-dns-service"></a>Habilitando o serviço DNS
Primeiro, você precisa habilitar o serviço DNS no cluster. Obtenha o modelo para o cluster que você deseja implantar. Use os [modelos de exemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) ou crie um modelo do Resource Manager. Habilite o serviço DNS com as seguintes etapas:

1. Verifique se a `apiversion` está definida como `2017-07-01-preview` para o recurso `Microsoft.ServiceFabric/clusters`, conforme mostrado neste trecho:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Agora, habilite o serviço DNS adicionando a seção `addonFeatures` a seguir após a seção `fabricSettings`, conforme mostrado neste trecho: 

    ```json
        "fabricSettings": [
        ...      
        ],
        "addonFeatures": [
            "DnsService"
        ],
    ```

3. Depois de atualizar o modelo de cluster com as alterações anteriores, aplique-as e permita a conclusão da atualização. Depois de concluído, o serviço do sistema DNS em execução no cluster, que é chamado de `fabric:/System/DnsService` na seção de serviço do sistema no Service Fabric Explorer. 

Como alternativa, você pode habilitar o serviço do DNS por meio do portal no momento da criação do cluster. O serviço DNS pode ser habilitado pela marcação da caixa de `Include DNS service` no menu `Cluster configuration` conforme mostrado na seguinte captura de tela:

![Habilitar o serviço DNS por meio do portal][2]


## <a name="setting-the-dns-name-for-your-service"></a>Configurando o nome DNS para o serviço
Assim que o serviço DNS estiver em execução no cluster, você pode definir um nome DNS para os serviços de forma declarativa para serviços padrão no `ApplicationManifest.xml` ou por meio de comandos do Powershell.

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>Definindo o nome DNS de um serviço padrão no ApplicationManifest.xml
Abra o projeto no Visual Studio ou em seu editor favorito e abra o arquivo `ApplicationManifest.xml`. Acesse a seção de serviços padrão e, para cada serviço, adicione o atributo `ServiceDnsName`. O exemplo a seguir mostra como definir o nome DNS do serviço como `service1.application1`

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
    <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
    </StatelessService>
    </Service>
```
Depois que o aplicativo for implantado, a instância de serviço no Service Fabric Explorer mostrará o nome DNS dessa instância, conforme mostrado na figura a seguir: 

![pontos de extremidade de serviço][1]

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>Configurando o nome DNS de um serviço usando o PowerShell
Defina o nome DNS de um serviço ao criá-lo usando o `New-ServiceFabricService` PowerShell. O exemplo a seguir cria um novo serviço sem estado com o nome DNS `service1.application1`

```powershell
    New-ServiceFabricService `
    -Stateless `
    -PartitionSchemeSingleton `
    -ApplicationName `fabric:/application1 `
    -ServiceName fabric:/application1/service1 `
    -ServiceTypeName Service1Type `
    -InstanceCount 1 `
    -ServiceDnsName service1.application1
```

## <a name="using-dns-in-your-services"></a>Usando o DNS nos serviços
Se você implantar mais de um serviço, poderá encontrar os pontos de extremidade de outros serviços com os quais se comunicar usando um nome DNS. O serviço DNS só é aplicável aos serviços sem monitoração de estado, já que o protocolo DNS não pode se comunicar com os serviços com monitoração de estado. Para serviços com estado, use o proxy reverso interno para chamadas HTTP para chamar uma partição de serviço específica.

O código a seguir mostra como chamar outro serviço, que é simplesmente uma chamada http regular onde você pode fornecer a porta e qualquer caminho opcional como parte da URL.

```csharp
public class ValuesController : Controller
{
    // GET api
    [HttpGet]
    public async Task<string> Get()
    {
        string result = "";
        try
        {
            Uri uri = new Uri("http://service1.application1:8080/api/values");
            HttpClient client = new HttpClient();
            var response = await client.GetAsync(uri);
            result = await response.Content.ReadAsStringAsync();
            
        }
        catch (Exception e)
        {
            Console.Write(e.Message);
        }

        return result;
    }
}
```

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre a comunicação de serviço no cluster com [Conectar e comunicar-se com serviços](service-fabric-connect-and-communicate-with-services.md)

[0]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[1]: ./media/service-fabric-dnsservice/servicefabric-explorer-dns.PNG
[2]: ./media/service-fabric-dnsservice/DNSService.PNG

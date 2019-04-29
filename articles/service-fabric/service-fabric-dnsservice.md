---
title: Serviço DNS do Azure Service Fabric | Microsoft Docs
description: Use o serviço DNS do Service Fabric para descobrir microsserviços no cluster.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/20/2018
ms.author: aljo
ms.openlocfilehash: 3b3262eadc732c23000a66f24aaeeed4d9794db0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60947598"
---
# <a name="dns-service-in-azure-service-fabric"></a>Serviço DNS no Azure Service Fabric
O Serviço DNS é um serviço do sistema opcional que pode ser habilitado no cluster para descobrir outros serviços usando o protocolo DNS. 

Muitos serviços, especialmente serviços em contêiner, são endereçáveis por meio de uma URL previamente existente. É preferível resolver esses serviços usando o protocolo DNS padrão, em vez do protocolo de Serviço de Nomenclatura do Service Fabric. O serviço DNS permite mapear nomes DNS para um nome de serviço e, portanto, resolver endereços IP do ponto de extremidade. Essa funcionalidade mantém a portabilidade dos serviços em contêiner em diferentes plataformas e pode facilitar cenários "lift and shift", permitindo usar URLs de serviço existentes em vez de reescrever o código para aproveitar o Serviço de Nomenclatura. 

O serviço DNS mapeia nomes DNS para nomes de serviço, que por sua vez, são resolvidos pelo serviço de nomenclatura para retornar o ponto de extremidade de serviço. O nome DNS do serviço é fornecido no momento da criação. O diagrama a seguir mostra como o serviço DNS funciona para serviços sem estado.

![pontos de extremidade de serviço](./media/service-fabric-dnsservice/stateless-dns.png)

A partir do Service Fabric versão 6.3, o protocolo de DNS do Service Fabric foi estendido para incluir um esquema de endereçamento de serviços com estado particionados. Essas extensões possibilitam resolver endereços IP de partição específica usando uma combinação de nome do serviço DNS com estado e nome da partição. Há suporte para todos os três esquemas de particionamento:

- Particionamento nomeado
- Particionamento por intervalos
- Particionamento de singleton

O diagrama a seguir mostra como o serviço DNS funciona para serviços com estado particionados.

![pontos de extremidade de serviço com estado](./media/service-fabric-dnsservice/stateful-dns.png)

As portas dinâmicas não são compatível com o serviço DNS. Para resolver serviços expostos em portas dinâmicas, use o [serviço de proxy reverter](./service-fabric-reverseproxy.md).

## <a name="enabling-the-dns-service"></a>Habilitando o serviço DNS
> [!NOTE]
> O serviço DNS para serviços do Service Fabric ainda não é suportado no Linux.

Ao criar cria um cluster usando o portal, o serviço DNS é ativado por padrão na caixa de seleção do **serviço Incluir DNS** no menu **Configuração de Cluster**:

![Habilitar o serviço DNS por meio do portal](./media/service-fabric-dnsservice/enable-dns-service.png)

Se você não estiver usando o portal para criar o cluster ou se estiver atualizando um cluster existente, será necessário habilitar o serviço DNS em um modelo:

- Para implantar um novo cluster, use os [modelos de exemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) ou crie seu próprio modelo do Resource Manager. 
- Para atualizar um cluster existente, você pode navegar para o grupo de recursos do cluster no portal e clicar em **script de automação** para trabalhar com um modelo que reflete o estado atual do cluster e outros recursos do grupo. Para saber mais, consulte[Exportar o modelo do grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template).

Depois de ter um modelo, habilite o serviço DNS com as seguintes etapas:

1. Verifique se `apiversion` está definido como `2017-07-01-preview` ou posterior para o recurso `Microsoft.ServiceFabric/clusters` e, caso contrário, atualize-o conforme mostrado no exemplo a seguir:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Agora, habilite o serviço DNS em uma das seguintes maneiras:

   - Para habilitar o serviço DNS com as configurações padrão, adicione-o à seção `addonFeatures` dentro da seção `properties` conforme mostrado no exemplo a seguir:

       ```json
           "properties": {
              ...

              "addonFeatures": [
                "DnsService"
              ],
              ...
           }
       ```
   - Para habilitar o serviço com configurações diferentes das configurações padrão, adicione uma seção `DnsService` à seção `fabricSettings` dentro da seção `properties`. Nesse caso, não é necessário adicionar o DnsService à `addonFeatures`. Para saber mais sobre as propriedades que podem ser definidas para o serviço DNS, consulte [Configurações do serviço DNS](./service-fabric-cluster-fabric-settings.md#dnsservice).

       ```json
           "properties": {
             ...  
             "fabricSettings": [
               ...
               {
                 "name": "DnsService",
                 "parameters": [
                   {
                     "name": "IsEnabled",
                     "value": "true"
                   },
                   {
                     "name": "PartitionSuffix",
                     "value": "--"
                   },
                   {
                     "name": "PartitionPrefix",
                     "value": "--"
                   }
                 ]
               },
               ...
              ]
            }
       ```
1. Após atualizar o modelo de cluster com as alterações, aplique-as e permita a conclusão do upgrade. Quando a atualização for concluída, o serviço do sistema DNS começa a ser executado em seu cluster. O nome do serviço é `fabric:/System/DnsService` e você pode encontrar na seção do serviço **Sistema** no explorador do Microsoft Azure Service Fabric. 


## <a name="setting-the-dns-name-for-your-service"></a>Configurando o nome DNS para o serviço
É possível configurar um nome DNS para os serviços de forma declarativa para serviços padrão no arquivo ApplicationManifest.xml ou através de comandos do PowerShell.

O nome DNS para o serviço pode ser resolvido em todo o cluster e, por isso, é importante garantir a exclusividade do nome DNS no cluster. 

É altamente recomendável que você use um esquema de nomenclatura de `<ServiceDnsName>.<AppInstanceName>`; por exemplo, `service1.application1`. Se um aplicativo for implantado usando a composição do docker, automaticamente são atribuídos nomes DNS usando esse esquema de nomenclatura.

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>Definindo o nome DNS de um serviço padrão no ApplicationManifest.xml
Abra o projeto no Visual Studio ou em seu editor favorito e abra o arquivo ApplicationManifest.xml. Acesse a seção de serviços padrão e, para cada serviço, adicione o atributo `ServiceDnsName`. O exemplo a seguir mostra como definir o nome DNS do serviço como `service1.application1`

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
      <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
```
Depois que o aplicativo for implantado, a instância de serviço no Service Fabric Explorer mostrará o nome DNS dessa instância, conforme mostrado na figura a seguir: 

![pontos de extremidade de serviço](./media/service-fabric-dnsservice/service-fabric-explorer-dns.png)

O exemplo a seguir define o nome DNS para um serviço com estado em `statefulsvc.app`. O serviço usa um esquema de particionamento nomeado. Observe que os nomes de partição estão em letras minúsculas. Esse é um requisito para partições que serão direcionadas em consultas DNS; para obter mais informações, consulte [Fazer consultas DNS em uma partição de serviço com estado](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice#preview-making-dns-queries-on-a-stateful-service-partition).

```xml
    <Service Name="Stateful1" ServiceDnsName="statefulsvc.app" />
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="2" MinReplicaSetSize="2">
        <NamedPartition>
         <Partition Name="partition1" />
         <Partition Name="partition2" />
        </NamedPartition>
      </StatefulService>
    </Service>
```

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>Configurando o nome DNS de um serviço usando o PowerShell
É possível definir o nome DNS de um serviço ao criá-lo usando o comando do PowerShell `New-ServiceFabricService`. O exemplo a seguir cria um novo serviço sem estado com o nome DNS `service1.application1`

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

## <a name="preview-making-dns-queries-on-a-stateful-service-partition"></a>[Versão prévia] Realizar consultas DNS em uma partição de serviço com estado
A partir do Service Fabric versão 6.3, o serviço DNS do Service Fabric dá suporte a consultas para partições de serviço.

Para partições que serão usadas em consultas DNS, as restrições de nomenclatura a seguir se aplicam:

   - Nomes de partição devem ser compatíveis com DNS.
   - Nomes de partição de vários rótulos (que incluem ponto, '.', no nome) não devem ser usados.
   - Nomes de partição devem estar em letras minúsculas.

Consultas DNS destinadas a uma partição são formatadas da seguinte maneira:

```
    <First-Label-Of-Partitioned-Service-DNSName><PartitionPrefix><Target-Partition-Name>< PartitionSuffix>.<Remaining- Partitioned-Service-DNSName>
```
Em que:

- *First-Label-Of-Partitioned-Service-DNSName* é a primeira parte do nome DNS do serviço.
- *PartitionPrefix* é um valor que pode ser definido na seção DnsService do manifesto do cluster ou por meio do modelo do Resource Manager do cluster. O valor padrão é "-". Para saber mais, consulte [Configurações do serviço DNS](./service-fabric-cluster-fabric-settings.md#dnsservice).
- *Target-Partition-Name* é o nome da partição. 
- *PartitionSuffix* é um valor que pode ser definido na seção DnsService do manifesto do cluster ou por meio do modelo do Resource Manager do cluster. O valor padrão pode ser uma cadeia de caracteres vazia. Para saber mais, consulte [Configurações do serviço DNS](./service-fabric-cluster-fabric-settings.md#dnsservice).
- *Remaining-Partitioned-Service-DNSName* é a parte restante do nome DNS do serviço.

Os exemplos a seguir mostram consultas DNS para serviços particionados em execução em um cluster que tem configurações padrão para `PartitionPrefix` e `PartitionSuffix`: 

- Para resolver a partição "0" de um serviço com nome DNS `backendrangedschemesvc.application` que usa um esquema de particionamento por intervalo, use `backendrangedschemesvc-0.application`.
- Para resolver a partição "first" de um serviço com nome DNS `backendnamedschemesvc.application` que usa um esquema de particionamento nomeado, use `backendnamedschemesvc-first.application`.

O serviço DNS retorna o endereço IP da réplica primária da partição. Se nenhuma partição for especificada, o serviço retornará o endereço IP da réplica primária de uma partição selecionada aleatoriamente.

## <a name="using-dns-in-your-services"></a>Usando o DNS nos serviços
Se você implantar mais de um serviço, será possível localizar os pontos de extremidade de outros serviços para comunicação usando um nome DNS. O serviço DNS funciona para serviços sem estado e, no Service Fabric versão 6.3 e versões posteriores, para serviços com estado. Para serviços com estado em execução em versões do Service Fabric anteriores à 6.3, use o [serviço de proxy reverso](./service-fabric-reverseproxy.md) interno para chamadas http para chamar uma partição de serviço particular. 

As portas dinâmicas não são compatível com o serviço DNS. É possível usar o serviço de proxy reverso para resolver serviços que usam portas dinâmicas.

O código a seguir mostra como chamar um serviço sem estado pelo DNS. Isso é simplesmente uma chamada http regular onde você pode fornecer o nome DNS, a porta e qualquer caminho opcional como parte da URL.

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

O código a seguir mostra uma chamada em uma partição específica de um serviço com estado. Nesse caso, o nome DNS contém o nome da partição (partition1). A chamada assume um cluster com valores padrão para `PartitionPrefix` e `PartitionSuffix`.

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
            Uri uri = new Uri("http://service2-partition1.application1:8080/api/values");
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

## <a name="known-issues"></a>Problemas conhecidos
* Para versões do Service Fabric 6.3 e superiores, há um problema com pesquisas de DNS para nomes de serviço que contenham um hífen no nome DNS. Para obter mais informações sobre esse problema, acompanhe o seguinte [Problema do GitHub](https://github.com/Azure/service-fabric-issues/issues/1197). Uma correção para isso será disponibilizada na próxima atualização 6.3. 

* O serviço DNS para serviços do Service Fabric ainda não é suportado no Linux. O serviço DNS é suportado para contêineres no Linux. Resolução manual usando Fabric Client / ServicePartitionResolver é a alternativa disponível.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre a comunicação de serviço no cluster com [Conectar e comunicar-se com serviços](service-fabric-connect-and-communicate-with-services.md)


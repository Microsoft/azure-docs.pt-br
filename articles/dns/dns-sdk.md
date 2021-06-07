---
title: Criar zonas DNS e conjuntos de registros usando o SDK do .NET
titleSuffix: Azure DNS
description: Neste roteiro de aprendizagem, comece a criar zonas DNS e conjuntos de registros no DNS do Azure usando o SDK do .NET.
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
ms.assetid: eed99b87-f4d4-4fbf-a926-263f7e30b884
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2016
ms.author: rohink
ms.custom: devx-track-csharp
ms.openlocfilehash: 8e116096afbd01af4914be49d5675881724d5069
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96015053"
---
# <a name="create-dns-zones-and-record-sets-using-the-net-sdk"></a>Criar zonas DNS e conjuntos de registros usando o SDK do .NET

É possível automatizar operações para criar, excluir ou atualizar zonas DNS, conjuntos de registros e registros, usando o SDK do DNS com a biblioteca de Gerenciamento de DNS do .NET. Um projeto completo do Visual Studio está disponível [aqui.](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)

## <a name="create-a-service-principal-account"></a>Crie uma conta da entidade de serviço

Normalmente, o acesso programático aos recursos do Azure é concedido por meio de uma conta dedicada, em vez de suas próprias credenciais de usuário. Essas contas dedicadas são denominadas contas da 'entidade de serviço'. Para usar o projeto de exemplo SDK do DNS do Azure, você precisa primeiro criar uma conta da entidade de serviço e atribuir as permissões corretas.

1. Siga [estas instruções](../active-directory/develop/howto-authenticate-service-principal-powershell.md) para criar uma conta da entidade de serviço (o projeto de exemplo SDK do DNS do Azure pressupõe uma autenticação baseada em senhas.)
2. Crie um grupo de recursos ([aqui](../azure-resource-manager/templates/deploy-portal.md)).
3. Use o RBAC do Azure para conceder à conta da entidade de serviço permissões do 'Colaborador da Zona DNS' para o grupo de recursos ([aqui](../role-based-access-control/role-assignments-portal.md).)
4. Se você usar o projeto de exemplo SDK do DNS do Azure, edite o arquivo 'program.cs' como a seguir:

   * Insira os valores corretos para `tenantId`, `clientId` (também conhecido como ID da conta), `secret` (senha da conta de entidade de serviço) e `subscriptionId`, conforme usado na etapa 1.
   * Insira o nome do grupo de recursos escolhido na etapa 2.
   * Insira um nome de zona do DNS de sua escolha.

## <a name="nuget-packages-and-namespace-declarations"></a>Pacotes NuGet e declarações de namespace

Para usar o SDK do .NET de DNS, você precisa instalar o pacote NuGet da **Biblioteca de Gerenciamento de DNS do Azure** e outros pacotes do Azure requeridos.

1. No **Visual Studio**, abra um projeto ou um novo projeto.
2. Vá para **ferramentas** **>** **Gerenciador de pacotes NuGet** **>** **gerenciar pacotes NuGet para solução...**.
3. Clique em **Procurar**, marque a caixa de seleção **Incluir pré-lançamento** e digite **Microsoft.Azure.Management.Dns** na caixa de pesquisa.
4. Selecione o pacote e clique em **Instalar** adicioná-lo a seu projeto do Visual Studio.
5. Repita o processo acima para também instalar os seguintes pacotes: **Microsoft.Rest.ClientRuntime.Azure.Authentication** e **Microsoft.Azure.Management.ResourceManager**.

## <a name="add-namespace-declarations"></a>Adicionar declarações do namespace

Adicionar as declarações do namespace a seguir

```cs
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.Dns;
using Microsoft.Azure.Management.Dns.Models;
```

## <a name="initialize-the-dns-management-client"></a>Inicializar o cliente de gerenciamento do DNS

O `DnsManagementClient` contém os métodos e propriedades necessários para o gerenciamento de zonas DNS e conjuntos de registros.  O código a seguir faz logon na conta da entidade de serviço e cria um objeto `DnsManagementClient`.

```cs
// Build the service credentials and DNS management client
var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, secret);
var dnsClient = new DnsManagementClient(serviceCreds);
dnsClient.SubscriptionId = subscriptionId;
```

## <a name="create-or-update-a-dns-zone"></a>Criar ou atualizar uma zona DNS

Para criar uma zona DNS, primeiro é criado um objeto "Zona" para conter os parâmetros de zona do DNS. Como as zonas do DNS não estão vinculadas a uma região específica, o local é definido para ‘global’. Neste exemplo, uma [’marcação’ Azure Resource Manager](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) também é adicionada à zona.

Para realmente criar ou atualizar a zona DNS do Azure, o objeto de zona que contém os parâmetros da zona é passado para o método `DnsManagementClient.Zones.CreateOrUpdateAsyc`.

> [!NOTE]
> O DnsManagementClient oferece suporte a três modos de operação: síncrono ('CreateOrUpdate'), assíncrono ('CreateOrUpdateAsync') ou assíncrono com acesso à resposta HTTP ('CreateOrUpdateWithHttpMessagesAsync').  Você pode escolher qualquer um desses modos, dependendo das necessidades de seu aplicativo.

O DNS do Azure dá suporte a [Etags](./dns-getstarted-powershell.md)de simultaneidade otimista. Neste exemplo, especificar "*" para o cabeçalho 'If-None-Match' informa ao DNS do Azure para criar uma zona DNS, caso ainda não exista.  A chamada falhará se uma zona com o nome fornecido já existir no grupo de recursos dado.

```cs
// Create zone parameters
var dnsZoneParams = new Zone("global"); // All DNS zones must have location = "global"

// Create an Azure Resource Manager 'tag'.  This is optional.  You can add multiple tags
dnsZoneParams.Tags = new Dictionary<string, string>();
dnsZoneParams.Tags.Add("dept", "finance");

// Create the actual zone.
// Note: Uses 'If-None-Match *' ETAG check, so will fail if the zone exists already.
// Note: For non-async usage, call dnsClient.Zones.CreateOrUpdate(resourceGroupName, zoneName, dnsZoneParams, null, "*")
// Note: For getting the http response, call dnsClient.Zones.CreateOrUpdateWithHttpMessagesAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*")
var dnsZone = await dnsClient.Zones.CreateOrUpdateAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*");
```

## <a name="create-dns-record-sets-and-records"></a>Criar conjuntos de registros DNS e registros

Os registros DNS são gerenciados como um conjunto de registros. Um conjunto de registros é um conjunto de registros com o mesmo nome e tipo de registro dentro de uma zona.  O nome do conjunto de registros é relativo ao nome da zona, não ao nome DNS totalmente qualificado.

Para criar ou atualizar um conjunto de registros, um objeto de parâmetros "RecordSet" é criado e passado para `DnsManagementClient.RecordSets.CreateOrUpdateAsync`. Como nas zonas DNS, há três modos de operação: síncrono ('CreateOrUpdate'), assíncrono ('CreateOrUpdateAsync') ou assíncrono com acesso à resposta HTTP ('CreateOrUpdateWithHttpMessagesAsync').

Como nas zonas DNS, as operações nos conjuntos de registros incluem suporte para a simultaneidade otimista.  Neste exemplo, como 'If-Match' nem 'If-None-Match' são especificados, o conjunto de registros sempre é criado.  Esta chamada substitui qualquer conjunto de registros existente com o mesmo nome e tipo de registro nessa zona DNS.

```cs
// Create record set parameters
var recordSetParams = new RecordSet();
recordSetParams.TTL = 3600;

// Add records to the record set parameter object.  In this case, we'll add a record of type 'A'
recordSetParams.ARecords = new List<ARecord>();
recordSetParams.ARecords.Add(new ARecord("1.2.3.4"));

// Add metadata to the record set.  Similar to Azure Resource Manager tags, this is optional and you can add multiple metadata name/value pairs
recordSetParams.Metadata = new Dictionary<string, string>();
recordSetParams.Metadata.Add("user", "Mary");

// Create the actual record set in Azure DNS
// Note: no ETAG checks specified, will overwrite existing record set if one exists
var recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSetParams);
```

## <a name="get-zones-and-record-sets"></a>Obter zonas e conjuntos de registros

Os métodos `DnsManagementClient.Zones.Get` e `DnsManagementClient.RecordSets.Get` recuperam zonas individuais e conjuntos de registros, respectivamente. RecordSets são identificados por seu tipo, nome e zona e grupo de recursos nos quais existem. As zonas são identificadas por seu nome e o grupo de recursos em que existem.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);
```

## <a name="update-an-existing-record-set"></a>Atualizar um conjunto de registros existente

Para atualizar um conjunto de registros DNS existente, primeiro recupere o conjunto de registros e atualize o conteúdo do conjunto, em seguida, envie a alteração.  Neste exemplo, especificamos 'Etag' a partir do conjunto de registros recuperado no parâmetro 'If-Match'. A chamada falhará se uma operação simultânea modificou o conjunto de registros nesse meio tempo.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);

// Add a new record to the local object.  Note that records in a record set must be unique/distinct
recordSet.ARecords.Add(new ARecord("5.6.7.8"));

// Update the record set in Azure DNS
// Note: ETAG check specified, update will be rejected if the record set has changed in the meantime
recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSet, recordSet.Etag);
```

## <a name="list-zones-and-record-sets"></a>Listar zonas e conjuntos de registros

Para listar zonas, use os métodos *DnsManagementClient. Zones. List...* , que dão suporte à listagem de todas as zonas em um determinado grupo de recursos ou todas as zonas em uma determinada assinatura do Azure (entre grupos de recursos.) Para listar conjuntos de registros, use os métodos *DnsManagementClient. Recordsets. List...* , que dão suporte à listagem de todos os conjuntos de registros em uma determinada zona ou apenas aos conjuntos de registros de um tipo específico.

Observe ao listar zonas e conjuntos de registros que os resultados poderão ser paginados.  O exemplo a seguir mostra como percorrer as páginas de resultados. (Um tamanho de página '2' artificialmente pequeno é usado para forçar a paginação; na prática, esse parâmetro deve ser omitido e o tamanho de página padrão usado.)

```cs
// Note: in this demo, we'll use a very small page size (2 record sets) to demonstrate paging
// In practice, to improve performance you would use a large page size or just use the system default
int recordSets = 0;
var page = await dnsClient.RecordSets.ListAllInResourceGroupAsync(resourceGroupName, zoneName, "2");
recordSets += page.Count();

while (page.NextPageLink != null)
{
    page = await dnsClient.RecordSets.ListAllInResourceGroupNextAsync(page.NextPageLink);
    recordSets += page.Count();
}
```

## <a name="next-steps"></a>Próximas etapas

Baixe o [projeto de exemplo SDK do .NET do DNS do Azure](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True), que inclui mais exemplos de como usar o SDK do .NET do DNS do Azure, incluindo exemplos para outros tipos de registro DNS.
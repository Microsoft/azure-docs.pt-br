---
title: "Alterar feed de recursos HL7 FHIR – Azure Cosmos DB | Microsoft Docs"
description: "Saiba como configurar notificações de alteração em registros de serviços de saúde de pacientes HL7 FHIR usando o Aplicativo Lógico do Azure, o Azure Cosmos DB e o Barramento de Serviço."
keywords: hl7 fhir
services: cosmos-db
author: hedidin
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 0d25c11f-9197-419a-aa19-4614c6ab2d06
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: b-hoedid
ms.openlocfilehash: d2b50c0b6864af41fb9cfa051721c432772b228d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="notifying-patients-of-hl7-fhir-health-care-record-changes-using-logic-apps-and-azure-cosmos-db"></a>Notificando pacientes de alterações em registros de serviços de saúde HL7 FHIR usando os Aplicativos Lógicos e o Azure Cosmos DB

Recentemente, o MVP do Azure Howard Edidin foi contatado por uma organização de saúde que queria adicionar uma nova funcionalidade ao seu portal do paciente. Eles precisavam enviar notificações aos pacientes quando o registro de saúde deles fosse atualizado, e precisavam que os pacientes pudessem assinar essas atualizações. 

Este artigo apresenta a solução de notificação de feed de alterações criada para esta organização de serviços de saúde usando o Azure Cosmos DB, os Aplicativos Lógicos e o Barramento de Serviço. 

## <a name="project-requirements"></a>Requisitos do projeto
- Os provedores enviam documentos C-CDA (Consolidated-Clinical Document Arquitecture) HL7 em formato XML. Os documentos C-CDA englobam praticamente qualquer tipo de documento clínico, incluindo documentos clínicos como históricos familiares e registros de vacinação, bem como documentos administrativos, de fluxo de trabalho e financeiros. 
- Os documentos C-CDA são convertidos em [Recursos HL7 FHIR](http://hl7.org/fhir/2017Jan/resourcelist.html) no formato JSON.
- Os documentos de recurso FHIR modificados são enviados por email no formato JSON.

## <a name="solution-workflow"></a>Fluxo de trabalho da solução 

Em um alto nível, o projeto exigiu as seguintes etapas de fluxo de trabalho: 
1. Converta os documentos C-CDA em recursos FHIR.
2. Execute uma sondagem de gatilho recorrente para recursos FHIR modificados. 
2. Chame um aplicativo personalizado, FhirNotificationApi, para se conectar ao Azure Cosmos DB e consultar documentos novos ou modificados.
3. Salve a resposta na fila do Barramento de Serviço.
4. Sondagem de novas mensagens na fila do Barramento de Serviço.
5. Envie notificações por email para pacientes.

## <a name="solution-architecture"></a>Arquitetura da solução
Essa solução requer três Aplicativos Lógicos atender aos requisitos acima e concluir o fluxo de trabalho da solução. Os três aplicativos lógicos são:
1. **Aplicativo Mapeamento de HL7 FHIR**: recebe o documento HL7 C-CDA, transforma-o no Recurso FHIR e, depois, salva-o no Azure Cosmos DB.
2. **Aplicativo EHR**: consulta o repositório de FHIR do Azure Cosmos DB e salva a resposta em uma fila do Barramento de Serviço. Esse aplicativo lógico usa um [aplicativo de API](#api-app) para recuperar documentos novos e alterados.
3. **Aplicativo de notificação do processo**: envia uma notificação por email com os documentos de recursos FHIR no corpo.

![Os três Aplicativos Lógicos usados nesta solução saúde HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/health-care-solution-hl7-fhir.png)



### <a name="azure-services-used-in-the-solution"></a>Serviços do Azure usados na solução

#### <a name="azure-cosmos-db-documentdb-api"></a>API do DocumentDB no Azure Cosmos DB
O Azure Cosmos DB é o repositório dos recursos FHIR, conforme mostrado na figura a seguir.

![A conta do Azure Cosmos DB usada neste tutorial de serviços de saúde HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/account.png)

#### <a name="logic-apps"></a>Aplicativos Lógicos
Os Aplicativos Lógicos lidam com o processo de fluxo de trabalho. As capturas de tela a seguir mostram os Aplicativos Lógicos criados para esta solução. 


1. **Aplicativo de mapeamento de FHIR HL7**: recebem o documento HL7 C-CDA e transformá-la a um recurso FHIR usando o Enterprise Integration Pack para Aplicativos Lógicos. O Enterprise Integration Pack manipula o mapeamento de C-CDA para recursos FHIR.

    ![O aplicativo lógico usado para receber registros médicos HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-json-transform.png)


2. **Aplicativo EHR**: consulte o repositório de FHIR do Azure Cosmos DB e salve a resposta em uma fila do Barramento de Serviço. O código do aplicativo GetNewOrModifiedFHIRDocuments está abaixo.

    ![O Aplicativo Lógico usado para consultar o Azure Cosmos DB](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-api-app.png)

3. **Aplicativo de notificação do processo**: enviar uma notificação por email com os documentos de recursos FHIR no corpo.

    ![O aplicativo lógico que envia email pacientes com o recurso HL7 FHIR no corpo](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-send-email.png)

#### <a name="service-bus"></a>Barramento de Serviço
A figura a seguir mostra os pacientes fila. O valor da propriedade Tag é usado para o assunto do email.

![A fila do Barramento de Serviço usada neste tutorial do HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-service-bus-queue.png)

<a id="api-app"></a>

#### <a name="api-app"></a>Aplicativo de API
Um aplicativo de API se conecta ao Azure Cosmos DB e consulta documentos FHIR novos ou modificados por tipo de recurso. Este aplicativo tem um controlador, **FhirNotificationApi**, com uma operação **GetNewOrModifiedFhirDocuments**, veja [fonte do aplicativo de API](#api-app-source).

Estamos usando a classe [`CreateDocumentChangeFeedQuery`](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery.aspx) da API do .NET do DocumentDB no Azure Cosmos DB. Para obter mais informações, consulte o [artigo sobre o feed de alterações](change-feed.md). 

##### <a name="getnewormodifiedfhirdocuments-operation"></a>Operação de GetNewOrModifiedFhirDocuments

**Entradas**
- DatabaseId
- CollectionId
- Nome do Tipo de Recurso FHIR HL7
- Booliano: Começar do início
- INT: Número de documentos retornados

**Saídas**
- Sucesso: Código de Status: 200, Resposta: Lista de Documentos (Matriz JSON)
- Falha: Código de Status: 404, Resposta: "Nenhum Documento encontrado para Tipo de Recurso '*nome do recurso '*"

<a id="api-app-source"></a>

**Fonte do aplicativo de API**

```C#

    using System.Collections.Generic;
    using System.Linq;
    using System.Net;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Web.Http;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Swashbuckle.Swagger.Annotations;
    using TRex.Metadata;
    
    namespace FhirNotificationApi.Controllers
    {
        /// <summary>
        ///     FHIR Resource Type Controller
        /// </summary>
        /// <seealso cref="System.Web.Http.ApiController" />
        public class FhirResourceTypeController : ApiController
        {
            /// <summary>
            ///     Gets the new or modified FHIR documents from Last Run Date 
            ///     or create date of the collection
            /// </summary>
            /// <param name="databaseId"></param>
            /// <param name="collectionId"></param>
            /// <param name="resourceType"></param>
            /// <param name="startfromBeginning"></param>
            /// <param name="maximumItemCount">-1 returns all (default)</param>
            /// <returns></returns>
            [Metadata("Get New or Modified FHIR Documents",
                "Query for new or modifed FHIR Documents By Resource Type " +
                "from Last Run Date or Begining of Collection creation"
            )]
            [SwaggerResponse(HttpStatusCode.OK, type: typeof(Task<dynamic>))]
            [SwaggerResponse(HttpStatusCode.NotFound, "No New or Modifed Documents found")]
            [SwaggerOperation("GetNewOrModifiedFHIRDocuments")]
            public async Task<dynamic> GetNewOrModifiedFhirDocuments(
                [Metadata("Database Id", "Database Id")] string databaseId,
                [Metadata("Collection Id", "Collection Id")] string collectionId,
                [Metadata("Resource Type", "FHIR resource type name")] string resourceType,
                [Metadata("Start from Beginning ", "Change Feed Option")] bool startfromBeginning,
                [Metadata("Maximum Item Count", "Number of documents returned. '-1 returns all' (default)")] int maximumItemCount = -1
            )
            {
                var collectionLink = UriFactory.CreateDocumentCollectionUri(databaseId, collectionId);
    
                var context = new DocumentDbContext();  
    
                var docs = new List<dynamic>();
    
                var partitionKeyRanges = new List<PartitionKeyRange>();
                FeedResponse<PartitionKeyRange> pkRangesResponse;
    
                do
                {
                    pkRangesResponse = await context.Client.ReadPartitionKeyRangeFeedAsync(collectionLink);
                    partitionKeyRanges.AddRange(pkRangesResponse);
                } while (pkRangesResponse.ResponseContinuation != null);
    
                foreach (var pkRange in partitionKeyRanges)
                {
                    var changeFeedOptions = new ChangeFeedOptions
                    {
                        StartFromBeginning = startfromBeginning,
                        RequestContinuation = null,
                        MaxItemCount = maximumItemCount,
                        PartitionKeyRangeId = pkRange.Id
                    };
    
                    using (var query = context.Client.CreateDocumentChangeFeedQuery(collectionLink, changeFeedOptions))
                    {
                        do
                        {
                            if (query != null)
                            {
                                var results = await query.ExecuteNextAsync<dynamic>().ConfigureAwait(false);
                                if (results.Count > 0)
                                    docs.AddRange(results.Where(doc => doc.resourceType == resourceType));
                            }
                            else
                            {
                                throw new HttpResponseException(new HttpResponseMessage(HttpStatusCode.NotFound));
                            }
                        } while (query.HasMoreResults);
                    }
                }
                if (docs.Count > 0)
                    return docs;
                var msg = new StringContent("No documents found for " + resourceType + " Resource");
                var response = new HttpResponseMessage
                {
                    StatusCode = HttpStatusCode.NotFound,
                    Content = msg
                };
                return response;
            }
        }
    }
    
```

### <a name="testing-the-fhirnotificationapi"></a>Testar o FhirNotificationApi 

A imagem a seguir demonstra como o swagger foi usado para testar a [FhirNotificationApi](#api-app-source).

![O arquivo de Swagger usado para testar o aplicativo de API](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-testing-app.png)


### <a name="azure-portal-dashboard"></a>Painel do portal do Azure

A imagem a seguir mostra todos os serviços do Azure para essa solução em execução no portal do Azure.

![O portal do Azure mostrando todos os serviços usados neste tutorial HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-portal.png)


## <a name="summary"></a>Resumo

- Você aprendeu que o Azure Cosmos DB tem suporte nativo para notificações em documentos novos ou modificados e como é fácil usá-lo. 
- Aproveitando os Aplicativos Lógicos, você pode criar fluxos de trabalho sem escrever nenhum código.
- Uso de filas do Barramento de Serviço do Azure para lidar com a distribuição para os documentos FHIR HL7.

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre o Azure Cosmos DB, visite a [home page do Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Para saber mais sobre os Aplicativos Lógicos, veja [Aplicativos Lógicos](https://azure.microsoft.com/services/logic-apps/).



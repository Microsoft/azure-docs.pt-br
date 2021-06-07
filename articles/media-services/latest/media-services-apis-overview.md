---
# <a name="mandatory-fields-see-more-on-akamsskyeyemeta"></a>Campos obrigatórios. Veja mais em aka.ms/skyeye/meta.
Título: desenvolver com APIs V3: descrição dos serviços de mídia do Azure: Saiba mais sobre as regras que se aplicam a entidades e APIs ao desenvolver com os serviços de mídia v3. services: media-services documentationcenter: '' author: IngridAtMicrosoft manager: femila editor: ''

MS. Service: Media-Services MS. Workload: MS. Topic: conceitual MS. Date: 10/23/2020 MS. autor: inhenkel MS. Custom: seodec18

---

# <a name="develop-with-media-services-v3-apis"></a>Desenvolver com as APIs dos Serviços de Mídia v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Como desenvolvedor, você pode usar a [API REST](/rest/api/media/) dos Serviços de Mídia ou bibliotecas de clientes que permitem interagir com a API REST para criar, gerenciar e manter fluxos de trabalho de mídia personalizados com facilidade. A API dos [Serviços de Mídia v3](https://aka.ms/ams-v3-rest-sdk) se baseia na especificação do OpenAPI (anteriormente conhecida como um Swagger).

Este artigo discute as regras que se aplicam a entidades e APIs ao desenvolver com os Serviços de Mídia v3.

## <a name="accessing-the-azure-media-services-api"></a>Acessar a API dos Serviços de Mídia do Azure

Para estar autorizado a acessar os recursos e a API dos Serviços de Mídia, primeiro você deve ser autenticado. Os Serviços de Mídia dão suporte à [autenticação baseada no Azure AD (Azure Active Directory)](../../active-directory/fundamentals/active-directory-whatis.md). Duas opções de autenticação comuns são:
 
* **Autenticação de entidade de serviço**: Usado para autenticar um serviço (por exemplo: aplicativos Web, aplicativos de funções, aplicativos lógicos, API e microservices). Os aplicativos que geralmente usam esse método de autenticação são aplicativos que executam serviços daemon, serviços de camada intermediária ou trabalhos agendados. Por exemplo, para aplicativos Web, sempre deve haver uma camada intermediária que se conecte aos serviços de mídia com uma entidade de serviço.
* **Autenticação de usuário**: Usada para autenticar uma pessoa que está usando o aplicativo para interagir com os recursos dos Serviços de Mídia. O aplicativo interativo deve primeiro solicitar ao usuário as credenciais do usuário. Um exemplo é um aplicativo de console de gerenciamento usado por usuários autorizados para monitorar trabalhos de codificação ou uma transmissão ao vivo.

A API dos serviços de mídia requer que o usuário ou aplicativo que faz as solicitações da API REST tenha acesso ao recurso de conta dos serviços de mídia e use uma função de **Colaborador** ou de **Proprietário**. A API pode ser acessada com a função de **Leitor** , mas somente as operações **Get** ou **List** estarão disponíveis.Para obter mais informações, consulte [controle de acesso baseado em função do Azure (RBAC do Azure) para contas de serviços de mídia](rbac-overview.md).

Em vez de criar uma entidade de serviço, considere o uso de identidades gerenciadas dos recursos do Azure para acessar a API dos serviços de mídia por meio de Azure Resource Manager. Para saber mais sobre as identidades gerenciadas para os recursos do Azure, confira o artigo [O que são as identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-ad-service-principal"></a>Entidade de serviço do Azure AD

O aplicativo do Azure AD e a entidade de serviço devem estar no mesmo locatário. Depois de criar o aplicativo, conceda o acesso da função de **Colaborado** ou **Proprietário** ao aplicativo à conta dos Serviços de Mídia.

Se você não tiver certeza se tem permissões para criar um aplicativo do Azure AD, consulte [Permissões necessárias](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

Na figura anterior, os números representam o fluxo das solicitações em ordem cronológica:

![Autenticação de aplicativo de camada intermediária com o AAD de uma API da Web](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. Um aplicativo de camada intermediária solicita um token de acesso do Microsoft Azure AD que tem os seguintes parâmetros:  

   * Ponto de extremidade do locatário do Azure AD.
   * URI de recurso dos Serviços de Mídia.
   * URI de recurso dos Serviços de Mídia REST.
   * Valores do aplicativo do Microsoft Azure AD: a ID do cliente e o segredo do cliente.

   Para obter todos os valores necessários, consulte [Acessar a API dos Serviços de Mídia do Microsoft Azure](./access-api-howto.md).

2. O token de acesso do Azure AD é enviado à camada intermediária.
4. A camada intermediária envia a solicitação à API REST da Mídia do Azure com o token do Azure AD.
5. A camada intermediária obtém novamente os dados dos Serviços de Mídia.

### <a name="samples"></a>Exemplos

Consulte os seguintes exemplos que mostram como se conectar à entidade de serviço do Microsoft Azure AD:

* [Conectar-se com REST](media-rest-apis-with-postman.md)  
* [Conectar-se com Java](configure-connect-java-howto.md)
* [Conectar-se com .NET](configure-connect-dotnet-howto.md)
* [Conectar-se com Node.js](configure-connect-nodejs-howto.md)
* [Conectar-se com Python](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>Convenções de nomenclatura

Nomes de recursos do Serviços de Mídia do Azure v3 (por exemplo, ativos, trabalhos, transformações) estão sujeitos a restrições de nomenclatura do Azure Resource Manager. De acordo com o Azure Resource Manager, os nomes dos recursos são sempre exclusivos. Desse modo, é possível usar qualquer cadeia de caracteres de identificador exclusivo (por exemplo, GUIDs) para os nomes dos recursos.

Nomes de recurso dos Serviços de Mídia não podem incluir: '<', '>', '%', '&', ':', '&#92;', '?', '/', '*', '+', '.', o caractere de aspas simples ou quaisquer caracteres de controle. Todos os outros caracteres são permitidos. O comprimento máximo de um nome de recurso é de 260 caracteres.

Para obter mais informações sobre a nomenclatura do Azure Resource Manager, consulte: [Requisitos de nomenclatura](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) e [Convenções de nomenclatura](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

### <a name="names-of-filesblobs-within-an-asset"></a>Nomes de Arquivos/blobs em um ativo

Os nomes de Arquivos/blobs em um ativo devem seguir os [requisitos de nome de blob](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) e os [requisitos de nome de NTFS](/windows/win32/fileio/naming-a-file). O motivo para esses requisitos é que os arquivos podem ser copiados do armazenamento de blobs para um disco NTFS local para processamento.

## <a name="long-running-operations"></a>Operações de execução longa

As operações marcadas com `x-ms-long-running-operation` nos arquivos de Serviços de Mídia do Microsoft Azure [Swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) são operações de execução prolongada. 

Para obter detalhes sobre como rastrear operações assíncronas do Azure, consulte [Operações assíncronas](../../azure-resource-manager/management/async-operations.md).

Os serviços de mídia têm as seguintes operações de execução longa:

* [Criar um evento ao vivo](/rest/api/media/liveevents/create)
* [Atualizar eventos ao vivo](/rest/api/media/liveevents/update)
* [Excluir evento ao vivo](/rest/api/media/liveevents/delete)
* [Iniciar evento ao vivo](/rest/api/media/liveevents/start)
* [Parar evento ao vivo](/rest/api/media/liveevents/stop)

  Use o parâmetro `removeOutputsOnStop` para excluir todas as saídas ao vivo associadas ao parar o evento.  
* [Redefinir LiveEvent](/rest/api/media/liveevents/reset)
* [Criar LiveOutput](/rest/api/media/liveevents/create)
* [Excluir LiveOutput](/rest/api/media/liveevents/delete)
* [Criar StreamingEndpoint](/rest/api/media/streamingendpoints/create)
* [Atualizar StreamingEndpoint](/rest/api/media/streamingendpoints/update)
* [Excluir StreamingEndpoint](/rest/api/media/streamingendpoints/delete)
* [Iniciar StreamingEndpoint](/rest/api/media/streamingendpoints/start)
* [Parar StreamingEndpoint](/rest/api/media/streamingendpoints/stop)
* [Escalar StreamingEndpoint](/rest/api/media/streamingendpoints/scale)

Após o envio bem-sucedido de uma operação longa, você receberá um ' 201 criado ' e deverá sondar a conclusão da operação usando a ID da operação retornada.

O artigo [Acompanhar as operações assíncronas do Azure](../../azure-resource-manager/management/async-operations.md) explica detalhadamente como acompanhar o status das operações assíncronas do Azure por meio dos valores retornados na resposta.

Somente uma operação de execução longa tem suporte para um determinado evento ao vivo ou qualquer uma de suas saídas dinâmicas associadas. Depois de iniciada, uma operação de execução prolongada deve ser concluída antes de iniciar uma operação de execução prolongada subsequente no mesmo LiveEvent ou em qualquer saída ao vivo associada. Para eventos ao vivo com várias saídas ao vivo, você deve aguardar a conclusão de uma operação de execução prolongada em uma saída dinâmica antes de disparar uma operação de execução prolongada em outra saída ao vivo.

## <a name="sdks"></a>SDKs

> [!NOTE]
> Os SDKs dos Serviços de Mídia do Azure v3 não têm garantia de serem thread-safe. Ao desenvolver um aplicativo com multi-thread, você deverá adicionar sua própria lógica de sincronização de thread para proteger o cliente ou usar um novo objeto AzureMediaServicesClient por thread. Você também deve tomar cuidado com problemas de multi-threading introduzidos por objetos opcionais fornecidos pelo código ao cliente (como uma instância do HttpClient no .NET).

|.|Referência|
|---|---|
|[SDK .NET](https://aka.ms/ams-v3-dotnet-sdk)|[Referência do .NET](/dotnet/api/overview/azure/mediaservices/management)|
|[Java SDK](https://aka.ms/ams-v3-java-sdk)|[Referência de Java](/java/api/overview/azure/mediaservices/management)|
|[SDK do Python](https://aka.ms/ams-v3-python-sdk)|[Referência do Python](/python/api/overview/azure/mediaservices/management)|
|[SDK do Node.js](https://aka.ms/ams-v3-nodejs-sdk) |[Referência do Node.js](/javascript/api/overview/azure/mediaservices/management)| 
|[SDK do Go](https://aka.ms/ams-v3-go-sdk) |[Referência do Go](https://aka.ms/ams-v3-go-ref)|
|[SDK do Ruby](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>Confira também

- [SDK do .NET EventGrid que inclui eventos de serviço de mídia](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definições de eventos de Serviços de Mídia](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Gerenciador dos Serviços de Mídia do Azure

O [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) (Gerenciador dos Serviços de Mídia do Azure) é uma ferramenta disponível para clientes do Windows que desejam saber mais sobre os Serviços de Mídia. O AMSE é um aplicativo do WinForms/C# que carrega, baixa, codifica e transmite VoD e conteúdo ao vivo com os Serviços de Mídia. A ferramenta AMSE destina-se aos clientes que desejam testar os Serviços de Mídia sem escrever nenhum código. O código AMSE é fornecido como um recurso para clientes que desejam desenvolver com os Serviços de Mídia.

O AMSE é um projeto de software livre, cujo suporte é fornecido pela comunidade (os problemas podem ser relatados a https://github.com/Azure/Azure-Media-Services-Explorer/issues). Este projeto adotou o [Código de Conduta de Software Livre da Microsoft](https://opensource.microsoft.com/codeofconduct/). Para saber mais, confira as [Perguntas frequentes sobre o Código de Conduta](https://opensource.microsoft.com/codeofconduct/faq/) ou contate o opencode@microsoft.com caso tenha outras dúvidas ou comentários.

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Filtragem, classificação, paginação de entidades dos Serviços de Mídia

Confira [Filtragem, classificação, paginação de entidades dos Serviços de Mídia do Microsoft Azure](entities-overview.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Fazer perguntas, comentar, obter atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="see-also"></a>Confira também

Para obter todos os valores necessários, consulte [Acessar a API dos Serviços de Mídia do Microsoft Azure](./access-api-howto.md).

## <a name="next-steps"></a>Próximas etapas

* [Conectar-se aos Serviços de Mídia do Microsoft Azure com Java](configure-connect-java-howto.md)
* [Conectar-se aos Serviços de Mídia do Microsoft Azure com .NET](configure-connect-dotnet-howto.md)
* [Conectar-se aos Serviços de Mídia do Microsoft Azure com Node.js](configure-connect-nodejs-howto.md)
* [Conectar-se aos Serviços de Mídia do Microsoft Azure com Python](configure-connect-python-howto.md)
---
title: Criar ou modificar uma oferta-Azure Marketplace
description: API para criar uma nova oferta ou atualizar uma existente.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/29/2020
ms.openlocfilehash: dba47073a6676b17c091af048f583ca3e7712999
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87420219"
---
# <a name="create-or-modify-an-offer"></a>Criar ou modificar uma oferta

> [!NOTE]
> As APIs de Portal do Cloud Partner são integradas ao e continuarão funcionando no Partner Center. A transição apresenta pequenas alterações. Examine as alterações listadas em [portal do Cloud Partner referência de API](./cloud-partner-portal-api-overview.md) para garantir que seu código continue funcionando após a transição para o Partner Center. As APIs de CPP só devem ser usadas para produtos existentes que já foram integrados antes da transição para o Partner Center; os novos produtos devem usar as APIs de envio do Partner Center.

Essa chamada atualiza uma oferta específica no namespace do editor ou cria uma nova oferta.

  `PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31`

## <a name="uri-parameters"></a>Parâmetros do URI

|  **Nome**         |  **Descrição**                      |  **Data type**  |
|  --------         |  ----------------                     |  -------------  |
| publisherId       |  Identificador do editor, por exemplo `contoso` |   Cadeia de caracteres |
| offerId           |  Identificador da oferta                     |   Cadeia de caracteres        |
| api-version       |  Última versão da API            |   Data           |
|  |  |  |

## <a name="header"></a>Cabeçalho

|  **Nome**        |  **Valor**               |
|  ---------       |  ----------              | 
| Tipo de conteúdo     | `application/json`       |
| Autorização    | `Bearer YOUR_TOKEN`      |
|  |  |

## <a name="body-example"></a>Exemplo de corpo

O exemplo a seguir cria uma oferta com o offerID de `contosovirtualmachine`.

### <a name="request"></a>Solicitação

``` json
  {
      "publisherId": "contoso",
      "offerTypeId": "microsoft-azure-virtualmachines",
      "id": "contosovirtualmachine",
      "offerTypeVersions": {
          "microsoft-azure-virtualmachines": 87,
          "microsoft-azure-marketplace": 39
      },
      "definition": {
          "displayText": "Contoso Virtual Machine Offer",
          "offer": {
          "microsoft-azure-marketplace.title": "Contoso App",
          "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
          "microsoft-azure-marketplace.allowedSubscriptions": ["59160c40-2e25-4dcf-a2fd-6514cb08bf08"],
          "microsoft-azure-marketplace.usefulLinks": [{
              "linkTitle": "Contoso App for Azure",
              "linkUrl": "https://azuremarketplace.microsoft.com"
          }],
          "microsoft-azure-marketplace.categoryMap": [
                {
                    "categoryL1": "analytics",
                    "categoryL2-analytics": [
                        "visualization-and-reporting"
                    ]
                },
                {
                    "categoryL1": "ai-plus-machine-learning",
                    "categoryL2-ai-plus-machine-learning":[
                        "bot-services",
                        "cognitive-services",
                        "other"
                    ]
                }
            ],
          "microsoft-azure-marketplace.smallLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/6218c455-9cbc-450c-9920-f2e7a69ee132.png?sv=2014-02-14&sr=b&sig=6O8MM9dgiJ48VK0MwddkyVbprRAnBszyhVkVHGShhkI%3D&se=2019-03-28T19%3A46%3A50Z&sp=r",
          "microsoft-azure-marketplace.mediumLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/557e714b-2f31-4e12-b0cc-e48dd840edf4.png?sv=2014-02-14&sr=b&sig=NwL67NTQf9Gc9VScmZehtbHXpYmxhwZc2foy3o4xavs%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.largeLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/142485da-784c-44cb-9523-d4f396446258.png?sv=2014-02-14&sr=b&sig=xaMxhwx%2FlKYfz33mJGIg8UBdVpsOwVvqhjTJ883o0iY%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.wideLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/48af9013-1df7-4c94-8da8-4626e5039ce0.png?sv=2014-02-14&sr=b&sig=%2BnN7f2tprkrqb45ID6JlT01zXcy1PMTkWXtLKD6nfoE%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.heroLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/c46ec74d-d214-4fb5-9082-3cee55200eba.png?sv=2014-02-14&sr=b&sig=RfDvjowFGpP4WZGAHylbF2CuXwO2NXOrwycrXEJvJI4%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.screenshots": [],
          "microsoft-azure-marketplace.videos": [],
          "microsoft-azure-marketplace.leadDestination": "None",
          "microsoft-azure-marketplace.privacyURL": "https://azuremarketplace.microsoft.com",
          "microsoft-azure-marketplace.termsOfUse": "Terms of use",
          "microsoft-azure-marketplace.engineeringContactName": "Jon Doe",
          "microsoft-azure-marketplace.engineeringContactEmail": "jondoe@outlook.com",
          "microsoft-azure-marketplace.engineeringContactPhone": "555-555-5555",
          "microsoft-azure-marketplace.supportContactName": "Jon Doe",
          "microsoft-azure-marketplace.supportContactEmail": "jondoe@outlook.com",
          "microsoft-azure-marketplace.supportContactPhone": "555-555-5555",
          "microsoft-azure-marketplace.publicAzureSupportUrl": "",
          "microsoft-azure-marketplace.fairfaxSupportUrl": ""
      },
      "plans": 
      [
          {
              "planId": "contososkuidentifier",
              "microsoft-azure-virtualmachines.skuTitle": "Contoso App",
              "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
              "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
              "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
              "microsoft-azure-virtualmachines.cloudAvailability": ["PublicAzure"],
              "virtualMachinePricing": {
                  "isByol": true,
                  "freeTrialDurationInMonths": 0
              },
              "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
              "microsoft-azure-virtualmachines.windowsOSType": "Other",
              "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
              "microsoft-azure-virtualmachines.recommendedVMSizes": ["a0-basic", "a0-standard", "a1-basic", "a1-standard", "a2-basic", "a2-standard"],
              "microsoft-azure-virtualmachines.openPorts": [],
              "microsoft-azure-virtualmachines.vmImages": 
              {
                  "1.0.1": 
                  {
                      "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sr=c&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                      "lunVhdDetails": []
                  }
              },
              "regions": ["AZ"]
          }
          ]
      },
      "eTag": "W/\"datetime'2017-06-07T06%3A15%3A40.4771399Z'\"",
      "version": 5
  }
```

### <a name="response"></a>Resposta

``` json
 {
         "offerTypeId": "microsoft-azure-virtualmachines",
         "publisherId": "contoso",
         "status": "neverPublished",
         "id": "contosovirtualmachine",
         "version": 1,
         "definition": {
         "displayText": "Contoso Virtual Machine Offer",
         "offer": 
         {
             "microsoft-azure-marketplace-testdrive.videos": [],
             "microsoft-azure-marketplace.title": "Contoso App",
             "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
             "microsoft-azure-marketplace.allowedSubscriptions": 
             [
                 "59160c40-2e25-4dcf-a2fd-6514cb08bf08"
             ],
             "microsoft-azure-marketplace.usefulLinks": 
             [
                 {
                     "linkTitle": "Contoso App for Azure",
                     "linkUrl": "https://azuremarketplace.microsoft.com"
                 }
             ],
             "microsoft-azure-marketplace.categoryMap":
             [
                 {
                    "categoryL1": "analytics",
                    "categoryL2-analytics": [
                    "visualization-and-reporting"
                    ]
                 },
                 {
                    "categoryL1": "ai-plus-machine-learning",
                    "categoryL2-ai-plus-machine-learning": [
                    "bot-services",
                    "cognitive-services",
                    "other"
                    ]
                 }
             ],
             "microsoft-azure-marketplace.smallLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/8affcd28-60a5-4839-adf8-c560e069fd61.png?sv=2014-02-14&sr=b&sig=nGErAgn%2BDUecrX892wcmk32kh0MHgIZeJ5jcKyY%2Fuew%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.mediumLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/39550bca-1110-432c-9ea9-e12b3a2288cd.png?sv=2014-02-14&sr=b&sig=4X0hlkXYtuZOmcYq%2BsbYVZz3k5k26kngcFX6yBAJjNI%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.largeLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/ce3576e3-df12-4074-b0a3-0b8d3f329df1.png?sv=2014-02-14&sr=b&sig=mFhtCUQh%2FbFz10nlIWbqsz6jq5MBZ0M%2F5cIREE9P6V0%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.wideLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/476d6edd-12d3-4414-9def-d2970c4a9de4.png?sv=2014-02-14&sr=b&sig=pg4MDSZjAb8w8D%2FrQ9RT%2BodpynSy%2FlYOvpx0yeam2Bw%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.heroLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/46c85b7b-4438-4e0d-8218-24fb5651727a.png?sv=2014-02-14&sr=b&sig=wIsCOO5%2BDj8NsLVSwwzwTgogF71oA7Q1XjKhNB1ni5g%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.screenshots": [],
             "microsoft-azure-marketplace.videos": [],
             "microsoft-azure-marketplace.leadDestination": "None",
             "microsoft-azure-marketplace.tableLeadConfiguration": {},
             "microsoft-azure-marketplace.blobLeadConfiguration": {},
             "microsoft-azure-marketplace.salesForceLeadConfiguration": {},
             "microsoft-azure-marketplace.crmLeadConfiguration": {},
             "microsoft-azure-marketplace.httpsEndpointLeadConfiguration": {},
             "microsoft-azure-marketplace.marketoLeadConfiguration": {},
             "microsoft-azure-marketplace.privacyURL": "https://azuremarketplace.microsoft.com",
             "microsoft-azure-marketplace.termsOfUse": "Terms of use",
             "microsoft-azure-marketplace.engineeringContactName": "Jon Doe",
             "microsoft-azure-marketplace.engineeringContactEmail": "jondoe@outlook.com",
             "microsoft-azure-marketplace.engineeringContactPhone": "555-555-5555",
             "microsoft-azure-marketplace.supportContactName": "Jon Doe",
             "microsoft-azure-marketplace.supportContactEmail": "jondoe@outlook.com",
             "microsoft-azure-marketplace.supportContactPhone": "555-555-5555",
             "microsoft-azure-marketplace.publicAzureSupportUrl": "",
             "microsoft-azure-marketplace.fairfaxSupportUrl": ""
         },
         "plans": 
         [
             {
                 "planId": "contososkuidentifier",
                 "microsoft-azure-virtualmachines.skuTitle": "Contoso App (Old Title)",
                 "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
                 "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
                 "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
                 "microsoft-azure-virtualmachines.cloudAvailability": 
                 [
                     "PublicAzure"
                 ],
                 "microsoft-azure-virtualmachines.certificationsFairfax": [],
                 "virtualMachinePricing": {
                     "isByol": true,
                     "freeTrialDurationInMonths": 0
                 },
                 "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
                 "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
                 "microsoft-azure-virtualmachines.recommendedVMSizes": 
                 [
                     "a0-basic",
                     "a0-standard",
                     "a1-basic",
                     "a1-standard",
                     "a2-basic",
                     "a2-standard"
                 ],
                 "microsoft-azure-virtualmachines.openPorts": [],
                 "microsoft-azure-virtualmachines.vmImages": 
                 {
                     "1.0.1": 
                     {
                         "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sr=c&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                         "lunVhdDetails": []
                     }
                 },
                 "regions":
                 [
                     "AZ"
                 ]
             } 
         ]
     },
     "changedTime": "2018-03-28T22:27:13.8363879Z"
 }
```

> [!NOTE]
> Para modificar essa oferta, adicione um cabeçalho **If-Match** definido como * para a solicitação acima. Use o mesmo corpo da solicitação acima, mas modifique os valores conforme desejado. 

### <a name="response-status-codes"></a>Códigos de status de resposta

| **Código**  |  **Descrição**                                                                            |
| --------  |  ---------------                                                                            |
|  200      | `OK`. A solicitação foi processada com êxito e a oferta foi modificada com êxito.           |
|  201      | `Created`. A solicitação foi processada com êxito e a oferta foi criada com êxito.   |
|  400      | `Bad/Malformed request`. O corpo da resposta ao erro pode fornecer mais informações.            |
|  403      | `Forbidden`. O cliente não tem acesso ao namespace solicitado.                     |
|  404      | `Not found`. A entidade referida pelo cliente não existe.                           |
|  412      | O servidor não atende a uma das condições prévias que o solicitante especificou na solicitação. O cliente deve verificar a ETAG enviada com a solicitação. |
|  |  |

## <a name="uploading-artifacts"></a>Carregar artefatos

Artefatos, como imagens e logotipos, devem ser compartilhados, carregando-os para um local acessível na Web e, em seguida, incluindo cada um como um URI na solicitação PUT, como no exemplo acima. O sistema detectará que esses arquivos não estão presentes no armazenamento do Azure Marketplace e baixará esses arquivos no armazenamento.  Como resultado, você verá que futuras solicitações GET retornarão uma URL de serviço do Azure Marketplace para esses arquivos.

## <a name="categories-and-industries"></a>Categorias e setores

Ao criar uma nova oferta, você deve especificar uma categoria para sua oferta no Marketplace. Opcionalmente, para alguns tipos de oferta, você também pode especificar indústrias. Com base no tipo de oferta, forneça as categorias/setores aplicáveis à oferta usando valores de chave específicos das tabelas a seguir.

### <a name="azure-marketplace-categories"></a>Categorias do Azure Marketplace

Essas categorias e suas respectivas chaves são aplicáveis para aplicativos do Azure, máquinas virtuais, máquinas virtuais principais, contêineres, aplicativos de contêiner, módulos de IoT Edge e tipos de oferta de SaaS. Os itens em negrito itálico (como ***análise***) são categorias e itens de texto padrão (como dados-insights) são subcategorias abaixo deles. Use os valores de chave exatos, sem alterar o espaçamento ou a capitalização.

| Categoria | Chaves de SaaS | Chaves de Azure App | Máquina virtual, contêineres, aplicativos de contêiner, módulo de IoT Edge, chaves de máquina virtual de núcleo |
| --- | --- | --- | --- |
| ***Análise*** | ***Analytics*** | ***análise-Azure-apps*** | ***análise-amp*** |
| Informações de dados | dados-informações | dados-informações | dados-informações |
| Análise de Dados | análise de dados | análise de dados | análise de dados |
| Big Data | Big data | bigData | Big data |
| Análise preditiva | análise preditiva | análise preditiva | análise preditiva |
| Análise em tempo real/streaming | análise de streaming em tempo real | análise de streaming em tempo real | análise de streaming em tempo real |
| Outros | other | outros-Analytics | other |
| ***IA + Machine Learning*** | ***ArtificialIntelligence*** | ***ia-Plus-Machine-Learning*** | ***ia-Plus-Machine-Learning*** |
| Serviços de bot | bot-serviços | bot-serviços | bot-serviços |
| Serviços Cognitivos | cognitiva – serviços | cognitiva – serviços | cognitiva – serviços |
| Serviço do Machine Learning | ml-serviço | ml-serviço | ml-serviço |
| ML automatizado | ml automatizado | ml automatizado | ml automatizado |
| Automação de processos de negócios/robótica | negócios-robótica-processo-automação | negócios-robótica-processo-automação | negócios-robótica-processo-automação |
| Rotulagem de dados | rotulação de dados | rotulação de dados | rotulação de dados |
| Preparação de dados | preparação de dados | preparação de dados | preparação de dados |
| Mineração de conhecimento | mineração de conhecimento | mineração de conhecimento | mineração de conhecimento |
| Operações de ML | ml-operações | ml-operações | ml-operações |
| Outros | outros-ia-Plus-Machine-Learning | other | other |
| ***Blockchain*** | ***blockchain*** | ***blockchain*** | ***blockchain*** |
| Aceleradores de aplicativos | aceleradores de aplicativos | aceleradores de aplicativos | aceleradores de aplicativos |
| Razão de nó único | razão de nó único | razão de nó único | razão de nó único |
| Razão de vários nós | razão de vários nós | razão de vários nós | razão de vários nós |
| Ferramentas | tools | tools | tools |
| Outros | other | other | other |
| ***Computação*** | ***computação em SaaS*** | ***Compute-Azure-apps*** | ***Nós*** |
| Infraestrutura do aplicativo | appInfra | appInfrastructure | infraestrutura de aplicativos |
| Sistemas operacionais | clientOS | clientOS | sistemas operacionais |
| Cache | cache | cache | cache |
| Outros | outro – computação | outro – computação | other |
| ***Contêineres*** | ***recipientes*** | ***recipientes*** | ***recipientes*** |
| Aplicativos de Contêiner | contêiner-aplicativos | contêiner-aplicativos | contêiner-aplicativos |
| Imagens de Contêiner | contêiner-imagens | contêiner-imagens | contêiner-imagens |
| Introdução aos contêineres | Introdução a contêineres | Introdução a contêineres | Introdução a contêineres |
| Outros | other | other | other |
| ***Bancos de dados*** | ***bancos de dados – SaaS*** | ***database*** | ***bancos*** |
| Bancos de dados NoSQL | NoSQL-bancos de dados | NoSQL-bancos de dados | NoSQL-bancos de dados |
| Bancos de dados relacionais | relacional-bancos de dados | relacional-bancos de dados | relacional-bancos de dados |
| Bancos de dados de razão/Blockchain | Ledger-blockchain-bancos de dados | Ledger-blockchain-bancos de dados | Ledger-blockchain-bancos de dados |
| Data lagos | Data-lagos | Data-lagos | Data-lagos |
| data warehouse | data warehouse | data warehouse | data warehouse |
| Outros | outros-bancos de dados | outros-bancos de dados | other |
| ***Ferramentas para Desenvolvedores*** | ***Developer – ferramentas-SaaS*** | ***Developer-Tools-Azure-apps*** | ***Developer – ferramentas*** |
| Ferramentas | ferramentas – desenvolvedor – ferramentas | ferramentas – desenvolvedor – ferramentas | ferramentas – desenvolvedor – ferramentas |
| Scripts | scripts | scripts | scripts |
| Serviço do desenvolvedor | devService | devService | desenvolvedor-serviço |
| Outros | outras ferramentas para desenvolvedores | outras ferramentas para desenvolvedores | other |
| ***DevOps*** | ***DevOps*** | ***DevOps*** | ***DevOps*** |
| Outros | other | other | other |
| ***Identidade*** | ***identidade*** | ***identidade*** | ***identidade*** |
| Gerenciamento de acesso | gerenciamento de acesso | gerenciamento de acesso | gerenciamento de acesso |
| Outros | other | other | other |
| ***Integração*** | ***integrar*** | ***integrar*** | ***integrar*** |
| Mensagens | mensagens | mensagens | mensagens |
| Outros | other | other | other |
| ***Internet das Coisas*** | ***IoT*** | ***Internet das coisas-Azure-apps*** | ***Internet das coisas*** |
| Serviços do IoT Core | N/D | IOT-núcleo-Services | IOT-núcleo-Services |
| Módulos do IoT Edge | N/D | IOT-Edge-modules | IOT-Edge-modules |
| Soluções de IoT | IOT-soluções | IOT-soluções | IOT-soluções |
| Visualização de & de análise de dados | análise de dados e visualização | análise de dados e visualização | análise de dados e visualização |
| Conectividade de IoT | conectividade IOT | conectividade IOT | conectividade IOT |
| Outros | outros-Internet das coisas | outros-Internet das coisas | other |
| ***Ferramentas de gerenciamento de & de ti*** | ***ITandAdministration*** | ***It-and-Management-Tools-Azure-apps*** | ***ferramentas de ti e gerenciamento*** |
| Soluções de gerenciamento | gerenciamento – soluções | gerenciamento – soluções | gerenciamento – soluções |
| Aplicativos de negócios | businessApplication | businessApplication | aplicativos de negócios |
| Outros | outros-IT-Management-Tools | outros-IT-Management-Tools | other |
| ***Monitorando & diagnóstico*** | ***monitoramento e diagnóstico*** | ***monitoramento e diagnóstico*** | ***monitoramento e diagnóstico*** |
| Outros | other | other | other |
| ***Mídia*** | ***media*** | ***media*** | ***media*** |
| Serviços de Mídia | media-services | media-services | media-services |
| Proteção de Conteúdo | content-protection | content-protection | content-protection |
| Streaming sob demanda & ao vivo | streaming ao vivo e sob demanda | streaming ao vivo e sob demanda | streaming ao vivo e sob demanda |
| Outros | other | other | other |
| ***Migração*** | ***as*** | ***as*** | ***as*** |
| Migração de Dados | migração de dados | migração de dados | migração de dados |
| Outros | other | other | other |
| ***Realidade misturada*** | ***mixed-reality*** | ***mixed-reality*** | ***mixed-reality*** |
| Outros | other | other | other |
| ***Rede*** | ***rede*** | ***rede*** | ***rede*** |
| Gerenciadores de dispositivos | gerenciadores de dispositivos | gerenciadores de dispositivos | gerenciadores de dispositivos |
| Conectividade | conectividade | conectividade | conectividade |
| Firewalls | firewalls | firewalls | firewalls |
| Balanceadores de Carga | balanceadores de carga | balanceadores de carga | balanceadores de carga |
| Outros | other | other | other |
| ***Segurança*** | ***segurança*** | ***segurança*** | ***segurança*** |
| Gerenciamento de acesso e identidade | gerenciamento de identidade e acesso | gerenciamento de identidade e acesso | gerenciamento de identidade e acesso |
| Proteção contra Ameaças | proteção contra ameaças | proteção contra ameaças | proteção contra ameaças |
| Proteção de Informações | proteção de informações | proteção de informações | proteção de informações |
| Outros | other | other | other |
| ***Storage*** | ***armazenamento-SaaS*** | ***armazenamento-Azure-apps*** | ***storage*** |
| Recuperação de & de backup | backup | backup | backup e recuperação |
| Armazenamento híbrido corporativo | Enterprise-híbrido-Storage | Enterprise-híbrido-Storage | Enterprise-híbrido-Storage |
| Compartilhamento de arquivos | compartilhamento de arquivos | compartilhamento de arquivos | compartilhamento de arquivos |
| Gerenciamento do ciclo de vida de dados | gerenciamento de ciclo de vida de dados | gerenciamento de ciclo de vida de dados | gerenciamento de ciclo de vida de dados |
| Outros | outro – armazenamento | outro – armazenamento | other |
| ***Web*** | ***site*** | ***site*** | ***site*** |
| Blogs & CMSs | Blogs-e-CMSS | Blogs-e-CMSS | Blogs-e-CMSS |
| Aplicativos Web iniciais | Starter-Web-Apps | Starter-Web-Apps | Starter-Web-Apps |
| Eletrônico | eletrônico | eletrônico | eletrônico |
| Estruturas de aplicativos Web | Web-aplicativos-estruturas | Web-aplicativos-estruturas | Web-aplicativos-estruturas |
| Aplicativos Web | aplicativos Web | aplicativos Web | aplicativos Web |
| Outros | other | other | other |
||||

### <a name="microsoft-appsource-categories"></a>Categorias de Microsoft AppSource

Essas categorias e suas respectivas chaves são aplicáveis para SaaS, aplicativo PowerBI, Dynamics 365 Business central, Dynamics 365 para compromisso com o cliente e Dynamics 365 para tipos de oferta de operação. Os itens em negrito itálico (como ***análise***) são categorias e itens de texto padrão (como análise avançada) são subcategorias abaixo deles. Use os valores de chave exatos, sem alterar o espaçamento ou a capitalização.

| Categoria | Chaves de SaaS | Dynamics 365 Business central, Dynamics 365 para compromisso com o cliente, Dynamics 365 para chaves de operação | Chaves de aplicativo do PowerBI |
| --- | --- | --- | --- |
| ***Análise*** | ***Analytics*** | ***Análise*** | ***Análise*** |
| Análise Avançada | análise avançada | análise avançada | análise avançada |
| Relatórios de & de visualização | visualização-relatórios | visualização-relatórios | visualização-relatórios |
| Outros | other | outros-Analytics | outros-Analytics |
| ***IA + Machine Learning*** | ***ArtificialIntelligence*** | ***ia-Plus-Machine-Learning-Dynamics*** | ***ia-Plus-Machine-Learning-appsource*** |
| IA para negócios | ia-para-negócios | ia-para-negócios | ia-para-negócios |
| Aplicativos de bot | bot-aplicativos | bot-aplicativos | bot-aplicativos |
| Outros | outros-ia-Plus-Machine-Learning | outros-ia-Plus-Machine-Learning | outros-ia-Plus-Machine-Learning |
| ***Colaboração*** | ***Colaboração*** | ***Colaboração*** | ***colaboração*** |
| Contatar & pessoas | contato – pessoas | contato – pessoas | contato e pessoas |
| Gerenciamento de reuniões | gerenciamento de reuniões | gerenciamento de reuniões | gerenciamento de reuniões |
| Gerenciamento de & de design de site | site-gerenciamento de design | site-gerenciamento de design | site-design e gerenciamento |
| Gerenciamento de projeto de & de tarefas | tarefa-projeto-gerenciamento | tarefa-projeto-gerenciamento | gerenciamento de tarefas e projetos |
| Videoconferência de voz & vídeo | voz-vídeo-conferência | voz-vídeo-conferência | voz e vídeo-conferência |
| Outros | outros-colaboração | outros-colaboração | other |
| ***Conformidade & legal*** | ***regulamenta*** | ***regulamenta*** | ***conformidade e legal*** |
| Auditoria de & de imposto | auditoria de imposto | auditoria de imposto | imposto e auditoria |
| Ofício | Ofício | Ofício | legal |
| Dados, governança & privacidade | Governança de dados-privacidade | Governança de dados-privacidade | Governança de dados e privacidade |
| Segurança de & de integridade | integridade-segurança | integridade-segurança | integridade e segurança |
| Outros | outros-conformidade-legal | outros-conformidade-legal | other |
| ***Customer Service*** | ***CustomerService*** | ***CustomerService*** | ***serviço de atendimento ao cliente*** |
| Centro de contato | contato-centro | contato-centro | contato-centro |
| Face para o serviço facial | serviço de face a face | serviço de face a face | serviço de face a face |
| Serviço de funcionários de & de Back Office | Back-Office-funcionário-serviço | Back-Office-funcionário-serviço | Back-Office e serviço de funcionários |
| Gerenciamento de casos de conhecimento & | gerenciamento de casos de conhecimento | gerenciamento de casos de conhecimento | gerenciamento de conhecimento e de caso |
| Engajamento de Omnichannel de & mídia social | social-mídia-omnichannel-Engagement | social-mídia-omnichannel-Engagement | Social-Media-and-omnichannel-Engagement |
| Outros | outro serviço de atendimento ao cliente | outro serviço de atendimento ao cliente | other |
| ***Finanças*** | ***Finanças*** | ***Finanças*** | ***Finanças*** |
| Contabilidade | contabilidade | contabilidade | contabilidade |
| Gerenciamento de Ativos | gerenciamento de ativos | gerenciamento de ativos | gerenciamento de ativos |
| Análise, consolidação & relatórios | análise-consolidação-relatórios | análise-consolidação-relatórios | análise-consolidação e relatório |
| Coleções de & de crédito | coleções de crédito | coleções de crédito | crédito e coleções |
| Conformidade & gerenciamento de riscos | conformidade – gerenciamento de riscos | conformidade – gerenciamento de riscos | gerenciamento de conformidade e risco |
| Outros | outros – finanças | outros – finanças | other |
| ***Human Resources*** | ***HumanResources*** | ***HumanResources*** | ***recursos humanos*** |
| Aquisição de talento | talento – aquisição | talento – aquisição | talento – aquisição |
| Gerenciamento de talentos | gerenciamento de talentos | gerenciamento de talentos | gerenciamento de talentos |
| Operações de RH | HR-operações | HR-operações | HR-operações |
| Planejamento de força de obra & análise | força de obra-planejamento-análise | força de obra-planejamento-análise | força de obra-planejamento e análise |
| Outros | outros recursos humanos | outros recursos humanos | other |
| ***Internet das Coisas*** | ***IoT*** | ***Internet das coisas-Dynamics*** | ***Internet das coisas-appsource*** |
| Operações de & de gerenciamento de ativos | ativos-gerenciamento-operações | ativos-gerenciamento-operações | ativos-gerenciamento-e-operações |
| Produtos conectados | produtos conectados | produtos conectados | produtos conectados |
| Cadeia de suprimentos inteligente | Cadeia de suprimento inteligente | Cadeia de suprimento inteligente | Cadeia de suprimento inteligente |
| Manutenção preditiva | Previsão-manutenção | Previsão-manutenção | Previsão-manutenção |
| Monitoramento remoto | monitoramento remoto | monitoramento remoto | monitoramento remoto |
| Segurança do & de segurança | segurança | segurança | segurança e segurança |
| Recursos de & de infraestrutura inteligente | infraestrutura inteligente – recursos | infraestrutura inteligente – recursos | infraestrutura inteligente e recursos |
| Veículos & mobilidade | veículos-mobilidade | veículos-mobilidade | veículos e mobilidade |
| Outros | outros-Internet das coisas | outros-Internet das coisas | other |
| ***Ferramentas de gerenciamento de & de ti*** | ***ITandAdministration*** | ***ITandAdministration*** | ***ferramentas de ti e gerenciamento*** |
| Soluções de gerenciamento | gerenciamento – soluções | gerenciamento – soluções | gerenciamento – soluções |
| Aplicativos de negócios | businessApplication | businessApplication | aplicativos de negócios |
| Outros | outros-IT-Management-Tools | outros-IT-Management-Tools | other |
| ***Marketing*** | ***Marketing*** | ***Marketing*** | ***Marketing*** |
| Anúncio | anúncio | anúncio | anúncio |
| Análise | análise-marketing | análise-marketing | análise-marketing |
| Gerenciamento de campanha & automação | campanha-gerenciamento-automação | campanha-gerenciamento-automação | gerenciamento de campanha e automação |
| Marketing por email | email-marketing | email-marketing | email-marketing |
| L2-eventos & gerenciamento de recursos | eventos-gerenciamento de recursos | eventos-gerenciamento de recursos | eventos-e-gerenciamento de recursos |
| Análise de & de pesquisa | pesquisa-análise | pesquisa-análise | pesquisa e análise |
| Mídia social | mídia social | mídia social | mídia social |
| Outros | outros-marketing | outros-marketing | other |
| ***Cadeia de fornecedores de & de operações*** | ***OperationsSupplyChain*** | ***OperationsSupplyChain*** | ***Cadeia de operações e de suprimento*** |
| Gerenciamento de produção de & de ativos | ativo-produção-gerenciamento | ativo-produção-gerenciamento | gerenciamento de ativos e produção |
| Previsão de demanda | demanda-previsão | demanda-previsão | demanda-previsão |
| Gerenciamento de informações & conectividade | informações-gerenciamento-conectividade | informações-gerenciamento-conectividade | informações-gerenciamento e conectividade |
| Planejando, comprando & relatórios | Planejamento-compras-relatórios | Planejamento-compras-relatórios | Planejamento-compras e relatórios |
| Gerenciamento de serviços de qualidade & | qualidade-gerenciamento de serviços | qualidade-gerenciamento de serviços | gerenciamento de qualidade e serviço |
| Gerenciamento de pedidos de vendas & | vendas-gerenciamento de ordem | vendas-gerenciamento de ordem | gerenciamento de vendas e pedidos |
| Gerenciamento do & warehouse de transporte | transporte-depósito-gerenciamento | transporte-depósito-gerenciamento | gerenciamento de transporte e de depósito |
| Outros | outro – operações – cadeia de suprimentos | outro – operações – cadeia de suprimentos | other |
| ***Produtividade*** | ***Produtividade*** | ***Produtividade*** | ***dos*** |
| Gerenciamento de & de criação de conteúdo | gerenciamento de criação de conteúdo | gerenciamento de criação de conteúdo | criação e gerenciamento de conteúdo |
| Tradução de & de idioma | Tradução de idioma | Tradução de idioma | linguagem e tradução |
| Gerenciamento de documentos | gerenciamento de documentos | gerenciamento de documentos | gerenciamento de documentos |
| Gerenciamento de email | gerenciamento de email | gerenciamento de email | gerenciamento de email |
| Referência de & de pesquisa | referência de pesquisa | referência de pesquisa | pesquisa e referência |
| Outros | outros-produtividade | outros-produtividade | other |
| Ludificação | Ludificação | Ludificação | Gamificação |
| ***Vendas*** | ***Vendas*** | ***Vendas*** | ***Vendas*** |
| Televendas | Televendas | Televendas | Televendas |
| Configurar, preço, cotação (CPQ) | configurar-cotação de preços | configurar-cotação de preços | configurar-cotação de preços |
| Gerenciamento de contrato | gerenciamento de contrato | gerenciamento de contrato | gerenciamento de contrato |
| CRM | crm | crm | crm |
| Comércio eletrônico | comércio eletrônico | comércio eletrônico | comércio eletrônico |
| Enriquecimento de dados de negócios | negócios-enriquecimento de dados | negócios-enriquecimento de dados | negócios-enriquecimento de dados |
| Habilitação de vendas | habilitação de vendas | habilitação de vendas | habilitação de vendas |
| Outros | outros – vendas | outros – vendas | outros – vendas |
| ***Geolocalização*** | ***localização*** | ***localização*** | ***localização*** |
| Mapas | maps | maps | maps |
| Clima de notícias & | Notícias e clima | Notícias e clima | Notícias e clima |
| Outros | outros-geolocalização | outros-geolocalização | outros-geolocalização |
||||

### <a name="microsoft-appsource-industries"></a>Microsoft AppSource indústrias

Esses setores e suas respectivas chaves são aplicáveis ao SaaS, ao aplicativo PowerBI, ao Dynamics 365 Business central, ao Dynamics 365 para o envolvimento do cliente e ao Dynamics 365 para os tipos de oferta de operação. Os itens em negrito itálico (como o ***automotivo***) são categorias e itens de texto padrão (como AutomotiveL2) são subcategorias abaixo deles. Use os valores de chave exatos, sem alterar o espaçamento ou a capitalização.

| Setor | SaaS, Dynamics 365 Business central, Dynamics 365 para compromisso com o cliente, Dynamics 365 para chaves de operação | Chaves de aplicativos do PowerBI |
| --- | --- | --- |
| ***Automotivo*** | ***Automotivo*** | ***automotiva*** |
| Automotivo | AutomotiveL2 | AutomotiveL2 |
| ***Agricultura*** | ***Agricultura*** | ***agricultura*** |
| Outros-não segmentados | Agricultura de \_ OtherUnsegmented | outros-não segmentados |
| ***Distribuição*** | ***Distribuição*** | ***distribuição*** |
| Atacado | Atacado | atacado |
| Envio de pacote de & de remessa | ParcelAndPackageShipping | remessa de empacotamento e pacote |
| ***Educação*** | ***Educação*** | ***faculdade*** |
| Ensino superior | HigherEducation | ensino superior |
| Educação secundária de & primária/K-12 | PrimaryAndSecondaryEducationK12 | educação primário e secundário |
| Bibliotecas & museus | LibrariesAndMuseums | bibliotecas-e-museus |
| ***Serviços financeiros*** | ***Financialservices*** | ***serviços financeiros*** |
| Mercados bancários & capital | BankingAndCapitalMarkets | mercados bancários e de capital |
| Seguro | Seguro | seguro |
| ***Governo*** | ***Governo*** | ***instituições*** |
| Inteligência de & de defesa | DefenseAndIntelligence | defesa e inteligência |
| Segurança pública & justiça | PublicSafetyAndJustice | público-segurança-e-justiça |
| Governo civis | CivilianGovernment | civis-governo |
| ***Assistência médica*** | ***HealthCareandLifeSciences*** | ***empresas*** |
| Pagador de integridade | HealthPayor | integridade-pagador |
| Provedor de integridade | Healthprovider | provedor de integridade |
| Produtos farmacêuticos | Produtos farmacêuticos | farmacêuticos |
| ***Recursos de produção &*** | ***Produção*** | ***fabricação e recursos*** |
| Agrochemical de química & | ChemicalAndAgrochemical | química e agrochemical |
| Fabricação discreta | DiscreteManufacturing | fabricação discreta |
| Energia | Energia | energy |
| ***Bens de consumo de & de varejo*** | ***RetailandConsumerGoods*** | ***varejo e consumidor-bens*** |
| Bens de consumo | ConsumerGoods | bens de consumo |
| Varejistas | Varejistas | varejistas |
| ***Comunicações de & de mídia*** | ***MediaAndCommunications*** | ***mídia e comunicações*** |
| Entretenimento de mídia & | MediaandEntertainment | mídia e entretenimento |
| Telecomunicações | Telecomunicações | telecomunicações |
| ***Serviços profissionais*** | ***Professionalservices*** | ***serviços profissionais*** |
| Ofício | Ofício | legal |
| Serviços profissionais de parceiros | PartnerProfessionalServices | parceiro profissional – serviços |
| ***Arquitetura & construção*** | ***ArchitectureAndConstruction*** | ***arquitetura e construção*** |
| Outros-não segmentados | ArchitectureAndConstruction \_ OtherUnsegmented | outros-não segmentados |
| ***Hospedagem & viagem*** | ***HospitalityandTravel*** | ***Hospedagem e viagem*** |
|    Hotéis & lazer | HotelsAndLeisure | Hotéis e lazer |
| Viagem e Transporte | TravelAndTransportation | viagem e transporte |
| Serviços de restaurantes & Food | RestaurantsAndFoodServices | restaurantes e alimentos-serviços |
| ***Outros setores do setor público*** | ***OtherPublicSectorIndustries*** | ***outros-setores públicos-setores*** |
| Pesca de & de floresta | ForestryAndFishing | florestal e pesca |
| Sem fins lucrativos | Sem fins lucrativos | sem fins lucrativos |
| ***Espaço real*** | ***RealEstate*** | ***imóveis*** |
| Outros-não segmentados | RealEstate \_ OtherUnsegmented | outros-não segmentados |
|||

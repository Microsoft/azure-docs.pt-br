---
title: "Visão geral de conectores personalizados – Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Visão geral sobre a criação de conectores personalizados para suporte e expansão de cenários de integração"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 0515b21bc7c7cc737e14fda016606bbea1aab476
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="custom-connectors-overview"></a>Visão geral dos conectores personalizados

Sem gravar nenhum código, você pode criar fluxos de trabalho ou aplicativos com [Aplicativos Lógicos do Azure](https://azure.microsoft.com/services/logic-apps/), [Microsoft Flow](https://flow.microsoft.com) ou [Microsoft PowerApps](https://powerapps.microsoft.com). Para ajudá-lo a integrar seus processos de negócios e dados, esses serviços oferecem [mais de 100 conectores](../connectors/apis-list.md), não apenas para serviços e produtos da Microsoft como o Azure e SQL Server, mas para outros serviços, como GitHub, Salesforce, Twitter e muito mais. 

Às vezes, porém, convém chamar APIs, serviços e sistemas que não estão disponíveis como conectores pré-compilados. Para dar suporte a mais cenários personalizados para os negócios e necessidades de produtividade dos usuários, você pode criar *conectores personalizados* com seus próprios gatilhos e ações.
Conectores personalizados expandem as integrações, o alcance, a descoberta e o uso para seu serviço ou produto, o que pode ajudar a aumentar e acelerar a adoção do cliente.

Por exemplo, este diagrama mostra a interação entre uma API da Web, um conector de Aplicativos Lógicos personalizado criado para essa API e um aplicativo lógico que funciona com essa API usando o conector personalizado:

![Visão geral conceitual de uma API da Web, conector personalizado e aplicativo lógico](./media/custom-connector-overview/custom-connector-conceptual.png)

Esta visão geral descreve as tarefas gerais de alto nível para criar, proteger, registrar e usar, além de opcionalmente compartilhar ou certificar seus conectores:

![Etapas de criação de conector personalizado](./media/custom-connector-overview/authoring-steps.png)

## <a name="prerequisites"></a>Pré-requisitos

Para criar o conector do início ao fim, você precisa destes itens:

* Uma assinatura do Azure. Se você não tiver uma assinatura, poderá iniciar com uma [conta gratuita do Azure](https://azure.microsoft.com/free/). Caso contrário, inscreva-se para uma [assinatura de Pagamento conforme o uso](https://azure.microsoft.com/pricing/purchase-options/).

* Uma API RESTful com algum tipo de acesso autenticado. Para obter mais informações, consulte [Criar conectores personalizados das APIs da Web](../logic-apps/custom-connector-build-web-api-app-tutorial.md). Para padrões que você pode usar para os gatilhos e ações de seu conector, consulte [Criar APIs personalizadas que podem ser chamadas de fluxos de trabalho de aplicativo lógico](../logic-apps/logic-apps-create-api-app.md).

* Qualquer item aqui:

  * Um [arquivo OpenAPI 2.0](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md), anteriormente conhecido como Swagger
  * Uma URL para uma definição da OpenAPI
  * Uma [coleção do Postman](../logic-apps/custom-connector-api-postman-collection.md) para sua API 

  Se você não tiver nenhum desses itens, forneceremos orientações a você.

* Opcional: Uma imagem para usar como um ícone para o conector personalizado

## <a name="1-build-your-restful-api"></a>1. Criar sua API RESTful

Tecnicamente, um conector é um wrapper de uma API REST que é baseado em uma especificação de OpenAPI (anteriormente conhecido como Swagger) e permite que o serviço em questão se comunique com Aplicativos Lógicos, o Flow ou o PowerApps. Primeiro, é necessário uma API totalmente operacional antes de criar o conector personalizado. 

Você pode usar qualquer plataforma e linguagem para a sua API. Para tecnologias da Microsoft, é recomendável uma destas plataformas:

* [Funções do Azure](https://azure.microsoft.com/services/functions/)
* [Aplicativos Web do Azure](https://azure.microsoft.com/services/app-service/web/)
* [Aplicativos de API do Azure](https://azure.microsoft.com/services/app-service/api/)

Por exemplo, este tutorial mostra [como criar um conector personalizado de uma API da Web](../logic-apps/custom-connector-build-web-api-app-tutorial.md). Para padrões que você pode usar para os gatilhos e ações de seu conector, consulte [Criar APIs personalizadas que podem ser chamadas de fluxos de trabalho de aplicativo lógico](../logic-apps/logic-apps-create-api-app.md).

## <a name="2-secure-your-api"></a>2. Proteger a sua API

Você pode usar esses padrões de autenticação para seu conectores e APIs:

   * [OAuth 2.0](https://oauth.net/2/), incluindo [Azure Active Directory](https://azure.microsoft.com/develop/identity/) ou serviços específicos, como o Dropbox, SalesForce e GitHub
   * OAuth 2.0 genérico
   * [Autenticação básica](https://swagger.io/docs/specification/authentication/basic-authentication/)
   * [Chave de API](https://swagger.io/docs/specification/authentication/api-keys/)

Você pode configurar a autenticação do Azure Active Directory (Azure AD) para sua API no portal do Azure para que não precise implementar a autenticação pelo código. Ou você pode exigir e aplicar a autenticação por meio do seu código da API. 

Para obter mais informações, siga os tutoriais apropriados:

* [Aplicativos Lógicos: proteger seu conector personalizado](../logic-apps/custom-connector-azure-active-directory-authentication.md)
* [Flow: proteger seu conector personalizado](https://ms.flow.microsoft.com/documentation/customapi-azure-resource-manager-tutorial/)
* [PowerApps: proteger seu conector personalizado](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/)

## <a name="3-describe-your-api"></a>3. Descrever sua API 

Presumindo que sua API tenha algum tipo de acesso autenticado, você precisa descrever as operações e a interface de sua API de forma que os Aplicativos Lógicos, o Flow ou o PowerApps possam se comunicar com sua API.
Use uma destas definições padrão do setor:

* Um [arquivo OpenAPI 2.0](https://swagger.io/) Você pode começar criando com um arquivo OpenAPI existente.

  Se você não estiver familiarizado com OpenAPI, visite [Guia de Introdução ao Swagger](http://swagger.io/getting-started/) no site swagger.io.
  Para um exemplo de arquivo OpenAPI, consulte a [documentação da API de Análise de Texto](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/export?DocumentFormat=Swagger&ApiName=Azure). 

* Uma coleção do Postman, que gera automaticamente um arquivo OpenAPI para você. Quando você ainda não tem um arquivo OpenAPI e não deseja criar um, ainda é possível criar facilmente um conector personalizado usando uma coleção do Postman.

  Se você for novo no Postman, [instale o aplicativo Postman](https://www.getpostman.com/apps) do seu site. Para obter mais detalhes, consulte [Descrever conectores personalizados com Postman](../logic-apps/custom-connector-api-postman-collection.md).

> [!IMPORTANT]
> O tamanho do seu arquivo deve ter menos de 1 MB.

Nos bastidores, Aplicativos Lógicos, o Flow e o PowerApps usam OpenAPI, analisam uma coleção do Postman e convertem a coleção em um arquivo de definição de OpenAPI. Embora o OpenAPI 2.0 e a coleção do Postman usem diferentes formatos, ambos são documentos legíveis para o computador independentes de linguagem que descrevem as operações e parâmetros de sua API. Você pode gerar esses documentos de várias ferramentas, com base no idioma e plataforma usada pela sua API. Você também pode criar um arquivo OpenAPI ao registrar seu conector.

Por exemplo, você pode criar um arquivo OpenAPI ou uma coleção do Postman de qualquer ponto de extremidade da API REST, incluindo:

* Conectores disponíveis publicamente, por exemplo, [Spotify](https://developer.spotify.com/), [Slack](https://api.slack.com/), [Rackspace](http://docs.rackspace.com/) e assim por diante

* Uma API que você criou e implantou em qualquer provedor de hospedagem de nuvem, como o Azure, Heroku, Google Cloud e muito mais

* Uma API de linha de negócios personalizada que foi implantada em sua rede, mas somente se essa API estiver exposta na Internet pública

## <a name="4-register-your-connector"></a>4. Registrar seu conector

O processo de registro ajuda Aplicativos Lógicos, o Flow ou o PowerApps a compreender as características da sua API, incluindo a descrição, a autenticação necessária e as operações, incluindo os parâmetros e os resultados para cada operação. Quando você inicia o processo de registro, é possível fornecer um arquivo OpenAPI ou uma coleção do Postman, que preenche automaticamente os campos de metadados no assistente de registro. Você pode editar os valores desses campos a qualquer momento.

![Descrever sua API](./media/custom-connector-overview/choose-api-definition-file.png)

Para registrar o conector, siga o tutorial apropriado:

* [Aplicativos Lógicos: registrar seu conector](../logic-apps/logic-apps-custom-connector-register.md)
* [Fluxo: registrar seu conector](https://ms.flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector)
* [PowerApps: registrar seu conector](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector)

## <a name="5-use-your-connector-in-a-logic-app-flow-or-app"></a>5. Usar o conector em um aplicativo lógico, fluxo ou aplicativo 

Você pode usar o conector da mesma maneira que você usa conectores gerenciados pela Microsoft. Por exemplo, em um fluxo de trabalho de aplicativo lógico, adicione seu conector personalizado para que você possa criar uma conexão com sua API e chame todas as operações que a API fornece da mesma maneira que você chama operações para conectores gerenciados pela Microsoft.

## <a name="6-share-your-connector"></a>6. Compartilhar seu conector 

Você pode compartilhar seu conector com usuários em sua organização da mesma maneira que você compartilha recursos em Aplicativos Lógicos, no Flow ou em PowerApps. Embora o compartilhamento seja opcional, pode haver situações em que você deseja compartilhar seus conectores com outros usuários.

Conectores personalizados registrados, mas não certificados funcionam como conectores gerenciados pela Microsoft, mas são visíveis e estão disponíveis *somente* para o autor do conector e usuários que têm o mesmo locatário do Azure Active Directory e a assinatura do Azure para os aplicativos lógicos na região em que esses aplicativos são implantados. A próxima etapa discute como você pode compartilhar seu conector com usuários externos fora desses limites, por exemplo, com os usuários de Aplicativos Lógicos, do Flow e do PowerApps.

> [!IMPORTANT]
> Se você compartilhar um conector, outras pessoas poderão começar a depender esse conector. 
> ***Excluir seu conector exclui todas as conexões com ele.***

* [Aplicativos Lógicos: compartilhar seu conector](../logic-apps/logic-apps-custom-connector-register.md)
* [Flow: compartilhar seu conector](https://ms.flow.microsoft.com/documentation/register-custom-api/#share-your-custom-connector)
* [PowerApps: compartilhar seu conector](https://powerapps.microsoft.com/tutorials/register-custom-api/#share-your-custom-connector)

## <a name="7-certify-your-connector"></a>7. Certificar seu conector

Para compartilhar de maneira ideal seu conector com todos os usuários nos Aplicativos Lógicos, no Flow e no PowerApps, envie seu conector para certificação da Microsoft. Esse processo analisa o conector, verifica a conformidade técnica e do conteúdo e valida a funcionalidade para Aplicativos Lógicos, o Flow e o PowerApps antes que a Microsoft possa publicar seu conector. Saiba [como enviar seus conectores para certificação da Microsoft](../logic-apps/custom-connector-submit-certification.md).

## <a name="get-support"></a>Obtenha suporte

* Para obter suporte com integração e desenvolvimento, envie um email para [ condevhelp@microsoft.com ](mailto:condevhelp@microsoft.com). A Microsoft monitora ativamente essa conta quanto a dúvidas e problemas de desenvolvedor e os direciona para a equipe apropriada. 

* Para perguntas frequentes, consulte as [Perguntas Frequentes do conector personalizado](../logic-apps/custom-connector-faq.md)

## <a name="next-steps"></a>Próximas etapas

* [Criar um conector personalizado de uma API Web](../logic-apps/custom-connector-build-web-api-app-tutorial.md)
* [Configurar a autenticação para conectores personalizados](../logic-apps/custom-connector-azure-active-directory-authentication.md)
* [Descrever conectores personalizados com coleções do Postman](../logic-apps/custom-connector-api-postman-collection.md)
* [Enviar conectores personalizados para certificação da Microsoft](../logic-apps/custom-connector-submit-certification.md)

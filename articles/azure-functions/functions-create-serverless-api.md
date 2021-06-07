---
title: Personalizar um ponto de extremidade HTTP no Azure Functions
description: Saiba como personalizar um ponto de extremidade de gatilho HTTP no Azure Functions
author: mattchenderson
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: mahender
ms.custom: mvc
ms.openlocfilehash: 7375a46245fbe523ddf0512bb5a55371adff64e9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98683735"
---
# <a name="customize-an-http-endpoint-in-azure-functions"></a>Personalizar um ponto de extremidade HTTP no Azure Functions

Neste artigo, você aprende como Azure Functions permite que você crie APIs altamente escalonáveis. O Azure Functions vem com uma coleção de gatilhos e associações HTTP internos, que facilitam o autor de um ponto de extremidade em uma variedade de linguagens, incluindo Node.js, C# e muito mais. Neste artigo, você personalizará um gatilho HTTP para manipular ações específicas em seu design de API. Você também se prepara para aumentar sua API integrando-a com Proxies do Azure Functions e configurando APIs fictícias. Essas tarefas são realizadas sobre o ambiente de computação sem servidor do functions, para que você não precise se preocupar com o dimensionamento de recursos. você pode se concentrar apenas na lógica da API.

## <a name="prerequisites"></a>Pré-requisitos 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

A função resultante será usada para o restante deste artigo.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com) com sua conta do Azure.

## <a name="customize-your-http-function"></a>Personalizar sua função HTTP

Por padrão, a função de gatilho HTTP é configurada para aceitar qualquer método HTTP. Você também pode usar a URL padrão, `http://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>` . Nesta seção, você modifica a função para responder somente a solicitações GET com `/api/hello` . 

1. Navegue até sua função no Portal do Azure. Selecione **integração** no menu à esquerda e, em seguida, selecione **http (req)** em **gatilho**.

    :::image type="content" source="./media/functions-create-serverless-api/customizing-http.png" alt-text="Personalizar uma função HTTP":::

1. Use as configurações de gatilho HTTP conforme especificado na tabela a seguir.

    | Campo | Valor de exemplo | Descrição |
    |---|---|---|
    | Modelo de rota | /hello | Determina qual rota pode ser usada para chamar essa função |
    | Nível de autorização | Anônima | Opcional: torna sua função acessível sem uma chave de API |
    | Métodos HTTP selecionados | GET | Permite que apenas os métodos HTTP selecionados possam ser usados para chamar essa função |

    Você não incluiu o `/api` prefixo de caminho base no modelo de rota, pois ele é manipulado por uma configuração global.

1. Clique em **Salvar**.

Para obter mais informações sobre como personalizar funções HTTP, consulte [Azure Functions associações http](./functions-bindings-http-webhook.md).

### <a name="test-your-api"></a>Testar sua API

Em seguida, teste sua função para ver como ela funciona com a nova superfície de API:
1. Na página função, selecione **código + teste** no menu à esquerda.

1. Selecione **obter URL da função** no menu superior e copie a URL. Confirme se ele agora usa o `/api/hello` caminho.
 
1. Copie a URL em uma nova guia do navegador ou o cliente REST preferencial. 

   Os navegadores usam GET por padrão.
 
1. Adicione parâmetros à cadeia de caracteres de consulta em sua URL. 

   Por exemplo, `/api/hello/?name=John`.
 
1. Pressione Enter para confirmar que está funcionando. Você deve ver a resposta "*Olá, João*".

1. Você também pode tentar chamar o ponto de extremidade com outro método HTTP para confirmar que a função não foi executada. Para fazer isso, use um cliente REST, como ondulação, postmaster ou Fiddler.

## <a name="proxies-overview"></a>Visão geral dos proxies

Na próxima seção, você pausará a API por meio de um proxy. Os Proxies do Azure Functions permitem o encaminhamento de solicitações para outros recursos. Você define um ponto de extremidade HTTP, assim como com o gatilho HTTP. No entanto, em vez de escrever código para executar quando esse ponto de extremidade é chamado, você fornece uma URL para uma implementação remota. Fazer isso permite compor várias fontes de API em uma única superfície de API, o que é fácil para os clientes consumirem, o que é útil se você quiser criar sua API como microservices.

Um proxy pode apontar para qualquer recurso HTTP, como:
- Azure Functions 
- Aplicativos de API no [Serviço de Aplicativo do Azure](../app-service/overview.md)
- Contêineres de docker no [Serviço de Aplicativo no Linux](../app-service/overview.md#app-service-on-linux)
- Qualquer outra API hospedada

Para saber mais sobre proxies, confira [Trabalhar com Proxies do Azure Functions].

## <a name="create-your-first-proxy"></a>Criar seu primeiro proxy

Nesta seção, você cria um novo proxy, que serve como um front-end para sua API geral. 

### <a name="setting-up-the-frontend-environment"></a>Configurar o ambiente front-end

Repita as etapas para [Criar um aplicativo de função](./functions-get-started.md) a fim de criar um novo aplicativo de função no qual você criará o proxy. A URL do novo aplicativo serve como o front-end para nossa API, e o aplicativo de funções que você estava editando anteriormente funciona como um back-end.

1. Navegue até seu novo aplicativo de função front-end no portal.
1. Selecione **Recursos de Plataforma** e escolha **Configurações de Aplicativo**.
1. Role para baixo até **configurações do aplicativo**, onde os pares chave/valor são armazenados e crie uma nova configuração com a chave `HELLO_HOST` . Defina o valor dela como o host de seu aplicativo de função de back-end, como `<YourBackendApp>.azurewebsites.net`. Esse valor é parte da URL que você copiou anteriormente ao testar sua função HTTP. Você fará referência a essa configuração mais adiante na configuração.

    > [!NOTE] 
    > As configurações do aplicativo são recomendadas para a configuração do host a fim de evitar uma dependência do ambiente embutida no código para o proxy. Usar configurações do aplicativo significa que você pode mover a configuração do proxy entre ambientes, e as configurações de aplicativo específicas ao ambiente serão aplicadas.

1. Clique em **Salvar**.

### <a name="creating-a-proxy-on-the-frontend"></a>Criar um proxy no front-end

1. Navegue de volta para seu aplicativo de funções de front-end no Portal.

1. No menu à esquerda, selecione **proxies** e, em seguida, selecione **Adicionar**. 

1. Na página **novo proxy** , use as configurações na tabela a seguir e, em seguida, selecione **criar**.

    | Campo | Valor de exemplo | Descrição |
    |---|---|---|
    | Nome | HelloProxy | Um nome amigável usado apenas para gerenciamento |
    | Modelo de rota | /api/remotehello | Determina qual rota pode ser usada para chamar esse proxy |
    | URL do back-end | https://%HELLO_HOST%/api/hello | Especifica o ponto de extremidade ao qual a solicitação deve ser transmitida por proxy |

    
    :::image type="content" source="./media/functions-create-serverless-api/creating-proxy.png" alt-text="Criação de um proxy":::

    Proxies do Azure Functions não fornece o `/api` prefixo de caminho base, que deve ser incluído no modelo de rota. A `%HELLO_HOST%` sintaxe faz referência à configuração do aplicativo que você criou anteriormente. A URL resolvida apontará para sua função original.

1. Experimente seu novo proxy copiando a URL do proxy e testando-a no navegador ou com seu cliente HTTP favorito:
    - Para uma função anônima, use:   `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?name="Proxies"` .
    - Para uma função com autorização, use:   `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?code=YOURCODE&name="Proxies"` .

## <a name="create-a-mock-api"></a>Criar uma API de simulação

Em seguida, você usará um proxy para criar uma API fictícia para sua solução. Esse proxy permite que o desenvolvimento de clientes seja progredido, sem a necessidade de um back-end totalmente implementado. Posteriormente, no desenvolvimento, você pode criar um novo aplicativo de funções, que oferece suporte a essa lógica e redirecionar o proxy para ele.

Para criar essa API fictícia, criaremos um novo proxy, desta vez usando o [Editor do serviço de aplicativo](https://github.com/projectkudu/kudu/wiki/App-Service-Editor). Para começar, navegue até seu aplicativo de função no portal. Selecione **recursos de plataforma** e, em **ferramentas de desenvolvimento** , localize **Editor do serviço de aplicativo**. O Editor do Serviço de Aplicativo é aberto em uma nova guia.

Selecione `proxies.json` no painel de navegação esquerdo. Esse arquivo armazena a configuração de todos os seus proxies. Se você usar um dos [métodos de implantação de funções](./functions-continuous-deployment.md), você manterá esse arquivo no controle do código-fonte. Para saber mais sobre esse arquivo, confira [Configuração avançada de proxies](./functions-proxies.md#advanced-configuration).

Se você seguiu até agora, seu proxies.jsem deve ser semelhante ao seguinte:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/remotehello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        }
    }
}
```

Em seguida, você adicionará sua API de simulação. Substitua seu proxies.jsno arquivo pelo código a seguir:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/remotehello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        },
        "GetUserByName" : {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/users/{username}"
            },
            "responseOverrides": {
                "response.statusCode": "200",
                "response.headers.Content-Type" : "application/json",
                "response.body": {
                    "name": "{username}",
                    "description": "Awesome developer and master of serverless APIs",
                    "skills": [
                        "Serverless",
                        "APIs",
                        "Azure",
                        "Cloud"
                    ]
                }
            }
        }
    }
}
```

Esse código adiciona um novo proxy, `GetUserByName` , sem a `backendUri` propriedade. Em vez de chamar outro recurso, ele modifica a resposta padrão dos Proxies usando uma substituição de resposta. Substituições de solicitação e resposta também podem ser usadas em conjunto com uma URL de back-end. Essa técnica é particularmente útil ao fazer o proxy para um sistema herdado, onde talvez seja necessário modificar cabeçalhos, parâmetros de consulta e assim por diante. Para saber mais sobre as substituições de solicitação e resposta, Confira [Modificar solicitações e respostas em Proxies](./functions-proxies.md).

Teste sua API de simulação chamando o ponto de extremidade `<YourProxyApp>.azurewebsites.net/api/users/{username}` usando um navegador ou seu cliente REST favorito. Não deixe de substituir _{username}_ por um valor de cadeia de caracteres que represente um nome de usuário.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como criar e personalizar uma API no Azure Functions. Você também aprendeu a unir várias APIs, incluindo objetos fictícios, como uma superfície de API unificada. Use essas técnicas para compilar APIs de qualquer complexidade durante a execução no modelo de computação sem servidor fornecido pelo Azure Functions.

As referências a seguir podem ser úteis durante o desenvolvimento de sua API:

- [Associações de HTTP do Azure Functions](./functions-bindings-http-webhook.md)
- [Trabalhar com Proxies do Azure Functions]
- [Documentar uma API do Azure Functions (visualização)](./functions-openapi-definition.md)


[Create your first function]: ./functions-get-started.md
[Trabalhar com Proxies do Azure Functions]: ./functions-proxies.md

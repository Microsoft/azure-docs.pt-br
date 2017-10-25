---
title: Criar uma API sem servidor usando o Azure Functions| Microsoft Docs
description: Como criar uma API sem servidor usando o Azure Functions
services: functions
author: mattchenderson
manager: cfowler
ms.service: functions
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: tutorial
ms.date: 05/04/2017
ms.author: mahender
ms.custom: mvc
ms.openlocfilehash: e4fe86b80d8a786da15cdea37619e54e55102e3f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-serverless-api-using-azure-functions"></a>Criar uma API sem servidor usando o Azure Functions

Neste tutorial, você aprenderá como o Azure Functions permite que você crie APIs altamente escalonáveis. O Azure Functions vem com uma coleção interna de gatilhos e associações HTTP que facilitam a criação de um ponto de extremidade em várias linguagens, incluindo Node.JS, C# e muito mais. Neste tutorial, você personalizará um gatilho HTTP para lidar com ações específicas em seu projeto de API. Você também preparará a expansão de sua API integrando-a aos Proxies do Azure Functions e configurando APIs de simulação. Tudo isso é realizado no ambiente de computação sem servidor do Functions, portanto você não precisa se preocupar com o dimensionamento de recursos, você pode se concentrar apenas na lógica de sua API.

## <a name="prerequisites"></a>Pré-requisitos 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

A função resultante será ser usada no restante deste tutorial.

### <a name="sign-in-to-azure"></a>Entrar no Azure

Abra o portal do Azure. Para fazer isso, entre em [https://portal.azure.com](https://portal.azure.com) usando sua conta do Azure.

## <a name="customize-your-http-function"></a>Personalizar sua função HTTP

Por padrão, sua função disparada por HTTP é configurada para aceitar qualquer método HTTP. Também há uma URL padrão no formato `http://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>`. Se você seguiu o guia de início rápido, `<funcname>` provavelmente se parece com algo como "HttpTriggerJS1". Nesta seção, você modificará a função para responder apenas a solicitações GET na rota `/api/hello`. 

1. Navegue até sua função no Portal do Azure. Selecione **Integrar** no painel de navegação esquerdo.

    ![Personalizar uma função HTTP](./media/functions-create-serverless-api/customizing-http.png)

1. Use as configurações do gatilho HTTP conforme especificado na tabela.

    | Campo | Valor de exemplo | Descrição |
    |---|---|---|
    | Métodos HTTP selecionados | Métodos selecionados | Determina quais métodos HTTP podem ser usados para chamar essa função |
    | Métodos HTTP selecionados | GET | Permite que apenas os métodos HTTP selecionados possam ser usados para chamar essa função |
    | Modelo de rota | /hello | Determina qual rota pode ser usada para chamar essa função |
    | Nível de autorização | Anônima | Opcional: torna sua função acessível sem uma chave de API |

    > [!NOTE] 
    > Observe que você não incluiu o prefixo de caminho base `/api` no modelo de rota, pois isso é tratado por uma configuração global.

1. Clique em **Salvar**.

Você pode aprender mais sobre a personalização de funções HTTP em [Associações HTTP e webhook do Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook#customizing-the-http-endpoint).

### <a name="test-your-api"></a>Testar sua API

Em seguida, teste sua função para vê-la funcionando com a nova superfície de API.
1. Navegue de volta para a página de desenvolvimento clicando no nome da função no painel de navegação esquerdo.
1. Clique em **Obter URL de função** e copie a URL. Você verá que agora ela usa a rota `/api/hello`.
1. Copie a URL em uma nova guia do navegador ou o cliente REST preferencial. Navegadores usarão GET por padrão.
1. Execute a função e confirme se ela está funcionando. Talvez seja necessário fornecer o parâmetro "name" como uma cadeia de caracteres de consulta para atender ao código de início rápido.
1. Você também pode tentar chamar o ponto de extremidade com outro método HTTP para confirmar a não execução da função. Para isso, será necessário usar um cliente REST, como cURL, Postman ou Fiddler.

## <a name="proxies-overview"></a>Visão geral dos proxies

Na próxima seção, você mostrará sua API através de um proxy. Os Proxies do Azure Functions são um recurso de visualização que permite o encaminhamento de solicitações para outros recursos. Defina um ponto de extremidade HTTP, como com o gatilho HTTP, mas em vez de escrever um código para executar durante a chamada para o ponto de extremidade, forneça uma URL para uma implementação remota. Isso permite a composição de várias fontes de API em uma única superfície de API, o que é fácil para os clientes usarem. Isso é particularmente útil se você quiser criar sua API como microsserviços.

Um proxy pode apontar para qualquer recurso HTTP, como:
- Funções do Azure 
- Aplicativos de API no [Serviço de Aplicativo do Azure](https://docs.microsoft.com/azure/app-service/app-service-web-overview)
- Contêineres de docker no [Serviço de Aplicativo no Linux](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro)
- Qualquer outra API hospedada

Para saber mais sobre proxies, confira [Trabalhar com Proxies do Azure Functions (visualização)].

## <a name="create-your-first-proxy"></a>Criar seu primeiro proxy

Nesta seção, você criará um novo proxy que servirá como um front-end para sua API geral. 

### <a name="setting-up-the-frontend-environment"></a>Configurar o ambiente front-end

Repita as etapas para [Criar um aplicativo de função](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function#create-a-function-app) a fim de criar um novo aplicativo de função no qual você criará o proxy. A URL desse novo aplicativo funcionará como o front-end de nossa API e o aplicativo de função que você estava editando anteriormente funcionará como um back-end.

1. Navegue até seu novo aplicativo de função front-end no portal.
1. Escolha a opção **Configurações**. Depois, alterne **Habilitar Proxies do Azure Functions (visualização)** para "Ativado".
1. Selecione **Configurações de Plataforma** e escolha **Configurações de Aplicativo**.
1. Role para baixo até **Configurações do aplicativo** e crie uma nova configuração com a chave "HELLO_HOST". Defina o valor dela como o host de seu aplicativo de função de back-end, como `<YourBackendApp>.azurewebsites.net`. Isso faz parte da URL que você copiou anteriormente ao testar sua função HTTP. Você fará referência a essa configuração mais adiante na configuração.

    > [!NOTE] 
    > As configurações do aplicativo são recomendadas para a configuração do host a fim de evitar uma dependência do ambiente embutida no código para o proxy. Usar configurações do aplicativo significa que você pode mover a configuração do proxy entre ambientes, e as configurações de aplicativo específicas ao ambiente serão aplicadas.

1. Clique em **Salvar**.

### <a name="creating-a-proxy-on-the-frontend"></a>Criar um proxy no front-end

1. Navegue novamente até seu aplicativo de função front-end no portal.
1. No painel de navegação esquerdo, clique no sinal '+' ao lado de "Proxies (visualização)".
    ![Criação de um proxy](./media/functions-create-serverless-api/creating-proxy.png)
1. Use as configurações de proxy conforme especificado na tabela. 

    | Campo | Valor de exemplo | Descrição |
    |---|---|---|
    | Nome | HelloProxy | Um nome amigável usado apenas para gerenciamento |
    | Modelo de rota | /api/hello | Determina qual rota pode ser usada para chamar esse proxy |
    | URL do back-end | https://%HELLO_HOST%/api/hello | Especifica o ponto de extremidade ao qual a solicitação deve ser transmitida por proxy |
    
1. Observe que os Proxies não fornecem o prefixo de caminho base `/api`, e ele deve ser incluído no modelo de rota.
1. A sintaxe `%HELLO_HOST%` fará referência a configuração do aplicativo que você criou anteriormente. A URL resolvida apontará para sua função original.
1. Clique em **Criar**.
1. Você pode testar seu novo proxy copiando a URL do Proxy e o testando no navegador ou com seu cliente HTTP favorito.
    1. Para uma função anônima, use:
        1. `https://YOURPROXYAPP.azurewebsites.net/api/hello?name="Proxies"`
    1. Para uma função com autorização, use:
        1. `https://YOURPROXYAPP.azurewebsites.net/api/hello?code=YOURCODE&name="Proxies"`

## <a name="create-a-mock-api"></a>Criar uma API de simulação

Em seguida, você usará um proxy para criar uma API de simulação para sua solução. Isso permite o progresso do desenvolvimento do cliente, sem a necessidade de implementar totalmente o back-end. Posteriormente no desenvolvimento, você poderá criar um novo aplicativo de função que oferece suporte a essa lógica e redirecionar seu proxy até ele.

Para criar essa API de simulação, criaremos um novo proxy, dessa vez usando o [Editor do Serviço de Aplicativo](https://github.com/projectkudu/kudu/wiki/App-Service-Editor). Para começar, navegue até seu aplicativo de função no portal. Selecione **Recursos da plataforma** e localize o **Editor do Serviço de Aplicativo**. Clique nele para abrir o Editor de Serviço de Aplicativo em uma nova guia.

Selecione `proxies.json` no painel de navegação esquerdo. Este é o arquivo que armazena a configuração de todos os seus proxies. Se você usar um dos [métodos de implantação do Functions](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment), esse será o arquivo mantido no controle de origem. Para saber mais sobre esse arquivo, confira [Configuração avançada de proxies](https://docs.microsoft.com/azure/azure-functions/functions-proxies#advanced-configuration).

Se você acompanhou até agora, o proxies.json deve ser semelhante ao seguinte:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/hello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        }
    }
}
```

Em seguida, você adicionará sua API de simulação. Substitua o arquivo proxies.json pelo seguinte:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/hello"
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

Isso adiciona um novo proxy "GetUserByName", sem a propriedade backendUri. Em vez de chamar outro recurso, ele modifica a resposta padrão dos Proxies usando uma substituição de resposta. Substituições de solicitação e resposta também podem ser usadas em conjunto com uma URL de back-end. Isso é particularmente útil ao transmitir por proxy para um sistema herdado, quando talvez você precise modificar cabeçalhos, consultar parâmetros etc. Para saber mais sobre as substituições de solicitação e resposta, Confira [Modificar solicitações e respostas em Proxies](https://docs.microsoft.com/azure/azure-functions/functions-proxies#a-namemodify-requests-responsesamodifying-requests-and-responses).

Teste sua API de simulação chamando o ponto de extremidade `/api/users/{username}` usando um navegador ou seu cliente REST favorito. Não deixe de substituir _{username}_ por um valor de cadeia de caracteres que represente um nome de usuário.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a compilar e personalizar uma API no Azure Functions. Você também aprendeu a unir várias APIs, incluindo objetos fictícios, como uma superfície de API unificada. Use essas técnicas para compilar APIs de qualquer complexidade durante a execução no modelo de computação sem servidor fornecido pelo Azure Functions.

As referências a seguir podem ser úteis durante o desenvolvimento de sua API:

- [Associações HTTP e de webhook do Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook)
- [Trabalhar com Proxies do Azure Functions (visualização)]
- [Documentar uma API do Azure Functions (visualização)](https://docs.microsoft.com/azure/azure-functions/functions-api-definition-getting-started)


[Create your first function]: https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function
[Trabalhar com Proxies do Azure Functions (visualização)]: https://docs.microsoft.com/azure/azure-functions/functions-proxies

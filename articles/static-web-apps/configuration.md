---
title: Configurar Aplicativos Web Estáticos do Azure
description: Saiba como configurar rotas, impor regras de segurança e configurações globais para Aplicativos Web Estáticos do Azure.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: cshoe
ms.openlocfilehash: 280c13fdee281acc4f805aba27a10277eb3988c2
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106218967"
---
# <a name="configure-azure-static-web-apps"></a>Configurar Aplicativos Web Estáticos do Azure

A configuração dos Aplicativos Web Estáticos do Azure é definida no arquivo _staticwebapp.config.json_, que controla as seguintes configurações:

- Roteamento
- Autenticação
- Autorização
- Regras de fallback
- Substituições de resposta HTTP
- Definições de cabeçalho HTTP global
- Tipos MIME personalizados

## <a name="file-location"></a>Local do arquivo

O local recomendado para o _staticwebapp.config.json_ está na pasta definida como o `app_location` no [arquivo de fluxo de trabalho](./github-actions-workflow.md). No entanto, o arquivo pode ser colocado em qualquer local dentro da pasta do código-fonte do seu aplicativo.

Confira o exemplo do [arquivo de configuração](#example-configuration-file) para ver detalhes.

> [!IMPORTANT]
> O [arquivo _routes.json_](./routes.md) será ignorado se houver _staticwebapp.config.json_.

## <a name="routes"></a>Rotas

As regras de rota permitem que você defina o padrão das URLs que permitem o acesso do seu aplicativo à Web. As rotas são definidas como uma matriz de regras de roteamento. Confira o [exemplo de arquivo de configuração](#example-configuration-file) para obter exemplos de uso.

- As regras são definidas na matriz `routes`, mesmo se você tiver apenas uma rota.
- As regras são executadas na ordem em que aparecem na matriz de `routes`.
- A avaliação da regra para na primeira correspondência. As regras de roteamento não são encadeadas.
- Você tem controle total sobre o nome das funções personalizadas.
  - Há alguns nomes de função interna que incluem [`anonymous`](./authentication-authorization.md) e [`authenticated`](./authentication-authorization.md).

As preocupações de roteamento se sobrepõem significativamente com os conceitos de autenticação (identificação do usuário) e autenticação (atribuição de capacidades ao usuário). Certifique-se de ler o guia de [autenticação e autorização,](authentication-authorization.md) juntamente com este artigo.

O arquivo padrão para conteúdo estático é *index.html*.

## <a name="defining-routes"></a>Definir rotas

Cada regra é composta por um padrão de rota, juntamente com uma ou mais das propriedades de regra opcionais. As regras de rota são definidas na matriz `routes`. Confira o [exemplo de arquivo de configuração](#example-configuration-file) para obter exemplos de uso.

| Propriedade de regra  | Obrigatório | Valor padrão | Comentário                                                      |
| -------------- | -------- | ------------- | ------------------------------------------------------------ |
| `route`        | Sim      | n/d          | O padrão de rota solicitado pelo chamador.<ul><li>Há suporte para [curingas](#wildcards) no final dos caminhos de rota.<ul><li>Por exemplo, a rota _admin/\*_ corresponde a qualquer rota sob o caminho de _administrador_.</ul></ul>|
| `rewrite`        | Não       | n/d          | Define o arquivo ou caminho retornado da solicitação.<ul><li>É mutuamente exclusivo para uma regra `redirect`<li>Regras de regeneração não alteram o local do navegador.<li>Os valores precisam ser relativos à raiz do aplicativo</ul>  |
| `redirect`        | Não       | N/D          | Define o destino de redirecionamento do arquivo ou caminho para uma solicitação.<ul><li>É mutuamente exclusivo para uma regra `rewrite`.<li>As regras de redirecionamento alteram o local do navegador.<li>O código de resposta padrão é um [`302`](https://developer.mozilla.org/docs/Web/HTTP/Status/302) (redirecionamento temporário), mas você pode substituí-lo por um [`301`](https://developer.mozilla.org/docs/Web/HTTP/Status/301) (redirecionamento permanente).</ul> |
| `allowedRoles` | Não       | anônimo     | Define uma lista de nomes de função necessários para acessar uma rota. <ul><li>Os caracteres válidos incluem `a-z`, `A-Z`, `0-9` e `_`.<li>A função interna, [`anonymous`](./authentication-authorization.md), se aplica a todos os usuários não autenticados<li>A função interna, [`authenticated`](./authentication-authorization.md), se aplica a todos os usuários que fizeram logon.<li>Os usuários devem pertencer a pelo menos uma função.<li>As funções são comparadas usando o operador _OR_.<ul><li>Se um usuário estiver em qualquer uma das funções listadas, o acesso será concedido.</ul><li>Os usuários individuais são associados a funções por meio de [convites](authentication-authorization.md).</ul> |
| `headers`<a id="route-headers"></a> | Não | N/D | Conjunto de [cabeçalhos HTTP](https://developer.mozilla.org/docs/Web/HTTP/Headers) adicionado à resposta. <ul><li>Os cabeçalhos específicos de rota substituem [`globalHeaders`](#global-headers) quando o cabeçalho específico de rota é o mesmo que o cabeçalho global que está na resposta.<li>Para remover um cabeçalho, defina o valor dele como uma cadeia de caracteres vazia.</ul> |
| `statusCode`   | Não       | `200`, `301` ou `302` para redirecionamentos | O [código de status HTTP](https://developer.mozilla.org/docs/Web/HTTP/Status) da resposta. |
| `methods` | Não | Todos os métodos | Lista de métodos de solicitação que correspondem a uma rota. Os métodos disponíveis incluem: `GET`, `HEAD`, `POST`, `PUT`, `DELETE`, `CONNECT`, `OPTIONS`, `TRACE` e `PATCH`. |

Cada propriedade tem uma finalidade específica no pipeline de solicitação/resposta.

| Finalidade | Propriedades |
|---|---|
| Fazer a correspondência de rotas | `route`, `methods` |
| Dar autorização após a correspondência de uma rota | `allowedRoles` |
| Fazer o processamento depois que uma regra for correspondida e autorizada | `rewrite` (modifica a solicitação) <br><br>`redirect`, `headers`, `statusCode` (modifica a resposta) |

## <a name="securing-routes-with-roles"></a>Proteger rotas com funções

As rotas são protegidas pela adição de um ou mais nomes de função na matriz de uma regra `allowedRoles` e os usuários são associados a funções personalizadas por meio de [convites](./authentication-authorization.md). Confira o [exemplo de arquivo de configuração](#example-configuration-file) para obter exemplos de uso.

Por padrão, cada usuário pertence à função `anonymous` interna e todos os usuários conectados são membros da função `authenticated`.

Por exemplo, para restringir uma rota somente a usuários autenticados, adicione a função `authenticated` interna à matriz de `allowedRoles`.

```json
{
  "route": "/profile",
  "allowedRoles": ["authenticated"]
}
```

Você pode criar novas funções conforme necessário na matriz de `allowedRoles`. Para restringir uma rota a apenas administradores, você pode definir a própria função chamada de `administrator`, na matriz `allowedRoles`.

```json
{
  "route": "/admin",
  "allowedRoles": ["administrator"]
}
```

- Você tem controle total sobre os nomes de função; não há nenhuma lista à qual suas funções devem se ater.
- Os usuários individuais são associados a funções por meio de [convites](authentication-authorization.md).

## <a name="wildcards"></a>Curingas

As regras curinga correspondem a todas as solicitações em um padrão de rota, só têm suporte no final de um caminho e podem ser filtradas por extensão de arquivo. Confira o [exemplo de arquivo de configuração](#example-configuration-file) para obter exemplos de uso.

Por exemplo, para implementar rotas para um aplicativo de calendário, você pode regenerar todas as URLs que se enquadram na rota _calendário_ para atender a um único arquivo.

```json
{
  "route": "/calendar/*",
  "rewrite": "/calendar.html"
}
```

O arquivo _calendar.html_ pode, então, usar o roteamento do lado do cliente para atender a uma exibição diferente para variações de URL, como `/calendar/january/1`, `/calendar/2020` e `/calendar/overview`.

Você pode filtrar correspondências curinga por extensão de arquivo. Por exemplo, se você quisesse adicionar uma regra que só corresponde a arquivos HTML em um determinado caminho, você poderia criar a seguinte regra:

```json
{
  "route": "/articles/*.html",
  "headers" : {
    "Cache-Control": "public, max-age=604800, immutable"
  }
}
```

Para filtrar várias extensões de arquivo, inclua as opções entre chaves, conforme mostrado neste exemplo:

```json
{
  "route": "/images/thumbnails/*.{png,jpg,gif}",
  "headers": {
    "Cache-Control": "public, max-age=604800, immutable"
  }
}
```

Os casos de uso comuns de rotas curinga incluem:

- Fornecer um arquivo específico para um padrão de caminho inteiro
- Mapear diferentes métodos HTTP para um padrão de caminho inteiro
- Impor regras de autenticação e autorização
- Implementar regras de cache especializadas

## <a name="fallback-routes"></a>Rotas de fallback

Aplicativos de página única geralmente dependem do roteamento do lado do cliente. Essas regras de roteamento do lado do cliente atualizam o local da janela do navegador sem fazer solicitações de volta ao servidor. Se você atualizar a página ou navegar diretamente até as URLs geradas pelas regras de roteamento do lado do cliente, uma rota de fallback do lado do servidor será necessária para atender à página HTML apropriada (que geralmente é o _index.html_ para seu aplicativo do lado do cliente).

Você pode configurar seu aplicativo para usar regras que implementam uma rota de fallback, conforme mostrado no seguinte exemplo, que usa um curinga de caminho com o filtro de arquivo:

```json
{
  "navigationFallback": {
    "rewrite": "/index.html",
    "exclude": ["/images/*.{png,jpg,gif}", "/css/*"]
  }
}
```

A estrutura de arquivo de exemplo abaixo, os resultados a seguir são possíveis com essa regra.

```files
├── images
│   ├── logo.png
│   ├── headshot.jpg
│   └── screenshot.gif
│
├── css
│   └── global.css
│
└── index.html
```

| Solicitações para... | retorna... | com o status... |
| --- | --- | --- |
| */about/* | O arquivo */index.html* | `200` |
| */images/logo.png* | O arquivo de imagem  | `200` |
| */images/icon.svg* | O arquivo */index.html* – como a extensão de arquivo *svg* não está listada no filtro `/images/*.{png,jpg,gif}`   | `200` |
| */images/unknown.png* | Erro de arquivo não encontrado  | `404` |
| */css/unknown.css* | Erro de arquivo não encontrado  | `404` |
| */css/global.css* | O arquivo de folha de estilos | `200` |
| Qualquer outro arquivo fora das pastas */images* ou */css* | O arquivo */index.html* | `200` |

## <a name="global-headers"></a>Cabeçalhos globais

A seção `globalHeaders` fornece um conjunto de [cabeçalhos HTTP](https://developer.mozilla.org/docs/Web/HTTP/Headers) aplicado a cada resposta, a menos que seja substituído por uma regra de [cabeçalho de rota](#route-headers); caso contrário, a união dos cabeçalhos da rota e globais será retornada.

Confira o [exemplo de arquivo de configuração](#example-configuration-file) para obter exemplos de uso.

Para remover um cabeçalho, defina o valor dele como uma cadeia de caracteres (`""`) vazia.

Alguns casos de uso comuns dos cabeçalhos globais incluem:

- Personalizar regras de cache
- Imposição de políticas de segurança
- Configurações de codificação

## <a name="response-overrides"></a>Substituições de resposta

A seção `responseOverrides` fornece uma oportunidade para definir uma resposta personalizada quando o servidor retornasse, de outra forma, um código de erro. Confira o [exemplo de arquivo de configuração](#example-configuration-file) para obter exemplos de uso.

Os seguintes códigos HTTP estão disponíveis para substituição:

| Código de status | Significado | Causa possível |
| --- | --- | --- |
| [400](https://developer.mozilla.org/docs/Web/HTTP/Status/400) | Solicitação incorreta | Link de convite inválido |
| [401](https://developer.mozilla.org/docs/Web/HTTP/Status/401) | Não Autorizado | Solicitação para páginas restritas enquanto a autenticação não foi realizada |
| [403](https://developer.mozilla.org/docs/Web/HTTP/Status/403) | Proibido |<ul><li>O usuário fez logon, mas não tem as funções necessárias para exibir a página.<li>O usuário está conectado, mas o runtime não pode obter os detalhes do usuário das declarações de identidade dele.<li>Há muitos usuários que fizeram logon no site com funções personalizadas, portanto, o runtime não pode fazer logon do usuário.</ul> |
| [404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) | Não encontrado | Arquivo não encontrado |

A configuração de exemplo a seguir demonstra como substituir um código de erro.

```json
{
    "responseOverrides": {
        "400" : {
            "rewrite": "/invalid-invitation-error.html",
            "statusCode": 200
        },
        "401": {
            "statusCode": 302,
            "redirect": "/login"
        },
        "403": {
            "rewrite": "/custom-forbidden-page.html",
            "statusCode": 200
        },
        "404": {
            "rewrite": "/custom-404.html",
            "statusCode": 200
        }
    }
}
```

## <a name="example-configuration-file"></a>Exemplo de arquivo de configuração

```json
{
    "routes": [
        {
            "route": "/profile",
            "allowedRoles": ["authenticated"]
        },
        {
            "route": "/admin/*",
            "allowedRoles": ["administrator"]
        },
        {
            "route": "/images/*",
            "headers": {
                "cache-control": "must-revalidate, max-age=15770000"
            }
        },
        {
            "route": "/api/*",
            "methods": [ "GET" ],
            "allowedRoles": ["registeredusers"]
        },
        {
            "route": "/api/*",
            "methods": [ "PUT", "POST", "PATCH", "DELETE" ],
            "allowedRoles": ["administrator"]
        },
        {
            "route": "/api/*",
            "allowedRoles": ["authenticated"]
        },
        {
            "route": "/customers/contoso",
            "allowedRoles": ["administrator", "customers_contoso"]
        },
        {
            "route": "/login",
            "rewrite": "/.auth/login/github"
        },
        {
            "route": "/.auth/login/twitter",
            "statusCode": 404
        },
        {
            "route": "/logout",
            "redirect": "/.auth/logout"
        },
        {
            "route": "/calendar/*",
            "rewrite": "/calendar.html"
        },
        {
            "route": "/specials",
            "redirect": "/deals",
            "statusCode": 301
        }
    ],
    "navigationFallback": {
      "rewrite": "index.html",
      "exclude": ["/images/*.{png,jpg,gif}", "/css/*"]
    },
    "responseOverrides": {
        "400" : {
            "rewrite": "/invalid-invitation-error.html"
        },
        "401": {
            "redirect": "/login",
            "statusCode": 302
        },
        "403": {
            "rewrite": "/custom-forbidden-page.html"
        },
        "404": {
            "rewrite": "/404.html"
        }
    },
    "globalHeaders": {
        "content-security-policy": "default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'"
    },
    "mimeTypes": {
        ".json": "text/json"
    }
}
```

Com base na configuração acima, examine os cenários a seguir.

| Solicitações para... | resulta em... |
| --- | --- |
| _/profile_ | Os usuários autenticados recebem o arquivo _/profile/index.html_. Usuários não autenticados são redirecionados para _/login_. |
| _/admin/_ | Os usuários autenticados na função _administrator_ recebem o arquivo _/admin/index.html_. Os usuários autenticados que não estão na função _administrator_ recebem um erro `403`<sup>1</sup>. Usuários não autenticados são redirecionados para _/login_. |
| _/logo.png_ | Fornece a imagem com uma regra de cache personalizada em que a idade máxima é um pouco maior que 182 dias (15.770.000 segundos). |
| _/api/admin_ | As solicitações `GET` de usuários autenticados na função _registeredusers_ são enviadas para a API. Os usuários autenticados que não estão na função _registeredusers_ e usuários não autenticados recebem um erro `401`.<br/><br/>As solicitações `POST`, `PUT`, `PATCH` e `DELETE` de usuários autenticados na função _administrator_ são enviadas para a API. Os usuários autenticados que não estão na função _administrator_ e usuários não autenticados recebem um erro `401`. |
| _/customers/contoso_ | Os usuários autenticados que pertencem às funções _administrator_ ou _customers\_contoso_ recebem o arquivo _/customers/contoso/index.html_. Os usuários autenticados que não estão na função _administrator_ ou _customers\_contoso_ recebem um erro `403`<sup>1</sup>. Usuários não autenticados são redirecionados para _/login_. |
| _/login_ | Os usuários não autenticados são desafiados a autenticar com o GitHub. |
| _/.auth/login/twitter_ | Como a autorização com o Twitter é desabilitada pela regra de rota, o erro `404` é retornado, que regride para fornecer _/index.html_ com um código de status `200`. |
| _/logout_ | Os usuários são desconectados de qualquer provedor de autenticação. |
| _/calendar/2021/01_ | O navegador recebe o arquivo _/calendar.html_. |
| _/specials_ | O navegador é redirecionado permanentemente para _/deals_. |
| _/data.json_ | O arquivo fornecido com o tipo MIME `text/json`. |
| _/about_ ou qualquer pasta que corresponda aos padrões de roteamento do lado do cliente | O arquivo _/index.html_ é fornecido com um código de status `200`. |
| Um arquivo não existente na pasta _/images/_ | Um erro `404`. |

<sup>1</sup> Você pode fornecer uma página de erro personalizada usando uma [regra de substituição de resposta](#response-overrides).

## <a name="restrictions"></a>Restrições

As restrições a seguir existem para o arquivo _staticwebapps.config.json_.

- O tamanho máximo do arquivo é 100 KB
- Máximo de 50 funções diferentes

Confira o [artigo Cotas](quotas.md) para ver as restrições gerais e limitações.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configurar autenticação e autorização](authentication-authorization.md)

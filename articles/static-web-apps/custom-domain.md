---
title: Configurar um domínio personalizado nos Aplicativos Web Estáticos do Azure
description: Saiba como mapear um domínio personalizado para os Aplicativos Web Estáticos do Azure
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: buhollan
ms.openlocfilehash: 578860883a108bba4b4bcd8cd04e8c08f484d474
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92173676"
---
# <a name="setup-a-custom-domain-in-azure-static-web-apps-preview"></a>Configurar um domínio personalizado nos Aplicativos Web Estáticos do Azure – Visualização

Por padrão, os Aplicativos Web Estáticos do Azure fornecem um nome de domínio gerado automaticamente. Este artigo mostra como mapear um nome de domínio personalizado para um aplicativo dos Aplicativos Web Estáticos do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Um nome de domínio adquirido
- Acesso às propriedades de configuração de DNS de seu domínio

Ao configurar nomes de domínio, os registros "A" são usados para mapear domínios raiz (por exemplo, `example.com`) para um endereço IP. Os domínios raiz devem ser mapeados diretamente para um endereço IP, pois a especificação de DNS não permite o mapeamento de um domínio para outro.

## <a name="dns-configuration-options"></a>Opções de configuração de DNS

Há alguns tipos diferentes de configurações de DNS disponíveis para um aplicativo.

| Se desejar | Então |
|--|--|
| Suporte `www.example.com` ou `blog.example.net` | [Mapear um registro CNAME](#map-a-cname-record) |
| Dar suporte à `example.com` | [Configurar um domínio raiz](#configure-a-root-domain) |
| Apontar todos os subdomínios para `www.example.com` | [Mapear um caractere curinga](#map-a-wildcard-domain) |

## <a name="map-a-cname-record"></a>Criar um registro CNAME

Um registro CNAME mapeia um domínio para outro. Você pode usar um registro CNAME para mapear `www.example.com` , `blog.example.com` ou qualquer outro subdomínio para o domínio gerado automaticamente que é fornecido pelos aplicativos Web estáticos do Azure.

1. Abra o [portal do Azure](https://portal.azure.com) e entre com sua conta do Azure.

1. Pesquise e selecione **Aplicativos Web Estáticos**

1. Na página _Aplicativos Web Estáticos_, selecione o nome do seu aplicativo.

1. Clique em **Domínios personalizados** no menu.

1. Clique no botão **Adicionar**

1. Na janela _Domínios personalizados_, copie a URL no campo **Valor**.

### <a name="configure-dns-provider"></a>Configurar provedor DNS

1. Entre no site de seu provedor de domínio.

2. Localize a página para gerenciamento de registros DNS. Cada provedor de domínio tem sua própria interface de registros DNS; portanto, consulte a documentação do provedor. Procure áreas do site rotuladas como **Nome de Domínio**, **DNS** ou **Gerenciamento de Servidor de Nomes**.

3. Normalmente, você pode encontrar a página de registros DNS exibindo as informações da conta e procurando um link como **Meus domínios**. Vá para essa página e procure um link com um nome semelhante a **Arquivo de zona**, **Registros DNS** ou **Configuração avançada**.

    A captura de tela a seguir é um exemplo de uma página de registros DNS:

    :::image type="content" source="media/custom-domain/example-record-ui.png" alt-text="Exemplo de configuração de provedor DNS":::

4. Crie um novo registro **CNAME** com os seguintes valores...

    | Configuração             | Valor                     |
    | ------------------- | ------------------------- |
    | Type                | CNAME                     |
    | Host                | www                       |
    | Valor               | Colar da área de transferência |
    | TTL (se aplicável) | Manter como o valor padrão    |

5. Salve as alterações com seu provedor DNS.

### <a name="validate-cname"></a>Validar o CNAME

1. Retorne à janela _Domínios personalizados_ no portal do Azure.

1. Insira seu domínio, incluindo a parte `www` na seção _Validar domínio personalizado_.

1. Clique no botão **Validar**.

Agora que o domínio personalizado está configurado, pode levar várias horas para o provedor DNS propagar as alterações em todo o mundo. Para verificar o status da propagação, acesse [dnspropagation.net](https://dnspropagation.net). Insira o domínio personalizado do domínio, incluindo o `www`, selecione CNAME na lista suspensa e selecione **Iniciar**.

Se as alterações de DNS tiverem sido preenchidas, o site retornará a URL gerada automaticamente do seu aplicativo Web estático (por exemplo, _random-name-123456789c.azurestaticapps.net_).

## <a name="configure-a-root-domain"></a>Configurar um domínio raiz

Os domínios raiz são seu domínio menos qualquer subdomínio, incluindo `www`. Por exemplo, o domínio raiz de `www.example.com` é `example.com`. Isso também é conhecido como domínio "APEX".

Embora o suporte ao domínio raiz não esteja disponível durante a visualização, você pode ver a postagem de blog [Configurar domínios raiz nos Aplicativos Web Estáticos do Azure](https://burkeholland.github.io/posts/static-app-root-domain) para obter detalhes sobre como configurar o suporte a domínio raiz com um Aplicativo Web Estático.

## <a name="map-a-wildcard-domain"></a>Mapear um domínio curinga

Às vezes, você deseja que todo o tráfego seja enviado a um subdomínio para rotear para outro domínio. Um exemplo comum é o mapeamento de todo o tráfego de subdomínio para `www.example.com`. Dessa forma, mesmo se alguém digitar `w.example.com` em vez de `www.example.com`, a solicitação será enviada para `www.example.com`.

### <a name="configure-dns-provider"></a>Configurar provedor DNS

1. Entre no site de seu provedor de domínio.

2. Localize a página para gerenciamento de registros DNS. Cada provedor de domínio tem sua própria interface de registros DNS; portanto, consulte a documentação do provedor. Procure áreas do site rotuladas como **Nome de Domínio**, **DNS** ou **Gerenciamento de Servidor de Nomes**.

3. Normalmente, você pode encontrar a página de registros DNS exibindo as informações da conta e procurando um link como **Meus domínios**. Vá para essa página e procure um link com um nome semelhante a **Arquivo de zona**, **Registros DNS** ou **Configuração avançada**.

    A captura de tela a seguir é um exemplo de uma página de registros DNS:

    :::image type="content" source="media/custom-domain/example-record-ui.png" alt-text="Exemplo de configuração de provedor DNS":::

4. Crie um novo registro **CNAME** com os seguintes valores, substituindo `www.example.com` pelo seu nome de domínio personalizado.

    | Configuração | Valor                  |
    | ------- | ---------------------- |
    | Type    | CNAME                  |
    | Host    | \*                     |
    | Valor   | www.exemplo.com        |
    | TTL     | Manter como o valor padrão |

5. Salve as alterações com seu provedor DNS.

Agora que o domínio curinga está configurado, pode levar várias horas para que as alterações se propaguem em todo o mundo. Para verificar o status da propagação, acesse [dnspropagation.net](https://dnspropagation.net). Insira o domínio personalizado do domínio com qualquer subdomínio (que não seja `www`), selecione CNAME na lista suspensa e selecione **Iniciar**.

Se as alterações de DNS tiverem sido preenchidas, o site retornará o domínio personalizado configurado para seu Aplicativo Web Estático (por exemplo, `www.example.com`).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Definir as configurações do aplicativo](application-settings.md)

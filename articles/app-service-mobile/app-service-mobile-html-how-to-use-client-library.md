---
title: Como usar o SDK do JavaScript para os Aplicativos Móveis do Azure
description: Como usar o v para os Aplicativos Móveis do Azure
services: app-service\mobile
documentationcenter: javascript
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 53b78965-caa3-4b22-bb67-5bd5c19d03c4
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: 16871bdc59d141334bc2c95f26929f270d7971cf
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62106730"
---
# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>Como usar a biblioteca de cliente JavaScript para os Aplicativos Móveis do Azure
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Este guia ensina a executar cenários comuns usando o mais recente [SDK do JavaScript para os Aplicativos Móveis do Azure]. Se não estiver familiarizado com os Aplicativos Móveis do Azure, primeiro conclua o [Início Rápido dos Aplicativos Móveis do Azure] para criar um back-end e uma tabela. Neste guia, vamos nos concentrar no uso do back-end móvel em aplicativos Web em HTML/JavaScript.

## <a name="supported-platforms"></a>Plataformas com suporte
Limitamos o suporte do navegador às versões atual e última dos principais navegadores:  Google Chrome, Microsoft Edge, Microsoft Internet Explorer e Mozilla Firefox.  Esperamos que o SDK funcione com todos os navegadores relativamente modernos.

O pacote é distribuído como um Módulo de JavaScript Universal e, portanto, ele dá suporte aos formatos AMD, CommonJS e globais.

## <a name="Setup"></a>Configuração e pré-requisitos
Este guia pressupõe que você tenha criado um back-end com uma tabela. Este guia pressupõe que a tabela tem o mesmo esquema das tabelas desses tutoriais.

A instalação do SDK do JavaScript para Aplicativos Móveis do Azure pode ser feita por meio do comando `npm` :

```
npm install azure-mobile-apps-client --save
```

A biblioteca também pode ser usada como um módulo ES2015, em ambientes de CommonJS como Browserify e Webpack e como uma biblioteca AMD.  Por exemplo: 

```javascript
// For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
// For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

Você também pode usar uma versão pré-criada do SDK baixando diretamente do nosso CDN:

```html
<script src="https://zumo.blob.core.windows.net/sdk/azure-mobile-apps-client.min.js"></script>
```

[!INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Como: Autenticar usuários
O Serviço de Aplicativo do Azure dão suporte para autenticação e autorização de usuários de aplicativos usando variados provedores de identidade externos: Facebook, Google, conta da Microsoft e Twitter. Você pode definir permissões em tabelas para restringir o acesso a operações específicas apenas para usuários autenticados. Você também pode usar a identidade de usuários autenticados para implementar regras de autorização em scripts do servidor. Para obter mais informações, consulte o tutorial [Introdução à autenticação] .

Dois fluxos de autenticação são suportados: um server flow e um client flow.  O fluxo de servidor fornece a experiência de autenticação mais simples, pois depende da interface de autenticação da web do provedor. O fluxo de cliente permite uma integração mais profunda com funcionalidades específicas do dispositivo, como logon único, uma vez que depende de SDKs específicos do provedor.

[!INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Como: Configurar o Serviço de Aplicativo Móvel para URLs de redirecionamento externo.
Vários tipos de aplicativo JavaScript usam uma funcionalidade de loopback para manipular fluxos de interface do usuário do OAuth.  Esses recursos incluem:

* Executar o serviço localmente
* Usar o Live Reload com o Ionic Framework
* Redirecionar para o Serviço de Aplicativo para autenticação.

A execução local pode causar problemas porque, por padrão, a autenticação de Serviço de Aplicativo só está configurada para permitir o acesso do back-end do Aplicativo Móvel. Use as seguintes etapas para alterar as configurações de Serviço de Aplicativo de modo a habilitar a autenticação ao executar o servidor localmente:

1. Faça logon no [Portal do Azure]
2. Navegue até o back-end do Aplicativo Móvel.
3. Escolha **Gerenciador de recursos** no menu **FERRAMENTAS DE DESENVOLVIMENTO**.
4. Clique em **Ir** para abrir o gerenciador de recursos para o back-end do Aplicativo Móvel em uma nova janela ou guia.
5. Expanda o nó **config** > **authsettings** do seu aplicativo.
6. Clique no botão **Editar** para habilitar a edição do recurso.
7. Encontre o elemento **allowedExternalRedirectUrls** , que deve ser nulo. Adicione as URLs em uma matriz:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Substitua as URLs na matriz pelas URLs do serviço, que neste exemplo é `http://localhost:3000` para o serviço local de exemplo do Node.js. Você também pode usar `http://localhost:4400` para o serviço Ripple ou alguma outra URL, dependendo de como seu aplicativo é configurado.
8. Na parte superior da página, clique em **Leitura/Gravação** e clique em **PUT** para salvar as atualizações.

Você também precisa adicionar as mesmas URLs de loopback às configurações de lista de permissões do CORS:

1. Navegue até o [Portal do Azure].
2. Navegue até o back-end do Aplicativo Móvel.
3. Clique em **CORS** no menu **API**.
4. Insira cada URL na caixa de texto **Origens Permitidas** vazia.  Uma nova caixa de texto é criada.
5. Clique em **SALVAR**

Após a atualização do back-end, você poderá usar as novas URLs de loopback em seu aplicativo.

<!-- URLs. -->
[Início Rápido dos Aplicativos Móveis do Azure]: app-service-mobile-cordova-get-started.md
[Introdução à autenticação]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Portal do Azure]: https://portal.azure.com/
[SDK do JavaScript para os Aplicativos Móveis do Azure]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx

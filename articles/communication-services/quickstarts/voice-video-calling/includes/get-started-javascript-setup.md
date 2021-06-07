---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 4a493d5f0d34cd4621d55c0371036c03e267c466
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108241"
---
## <a name="setting-up"></a>Configurando

### <a name="create-a-new-nodejs-application"></a>Criar um novo aplicativo do Node.js

Abra o terminal ou a janela de comando para criar um diretório para seu aplicativo e navegue até ele.

```console
mkdir calling-quickstart && cd calling-quickstart
```

Execute `npm init -y` para criar um arquivo **package.json** com as configurações padrão.

```console
npm init -y
```

### <a name="install-the-package"></a>Instalar o pacote

Use o comando `npm install` para instalar o SDK de Chamada dos Serviços de Comunicação do Azure para JavaScript.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

As seguintes versões do webpack são recomendadas para este início rápido:

```console
"webpack": "^4.42.0",
"webpack-cli": "^3.3.11",
"webpack-dev-server": "^3.10.3"
```

A opção `--save` lista a biblioteca como uma dependência no arquivo **package.json**.

### <a name="set-up-the-app-framework"></a>Configurar o framework de aplicativos

Este guia de início rápido usa o webpack para agrupar os ativos do aplicativo. Execute o seguinte comando para instalar os pacotes npm webpack, webpack-cli e webpack-dev-server e listá-los como dependências de desenvolvimento no **package.json**:

```console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

Crie um arquivo **index.html** no diretório raiz do projeto. Usaremos esse arquivo para configurar um layout básico que permitirá que o usuário faça uma chamada.

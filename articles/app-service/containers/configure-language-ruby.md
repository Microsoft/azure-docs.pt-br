---
title: Configurar aplicativos Ruby – Serviço de Aplicativo do Azure
description: Este tutorial descreve as opções para criar e configurar aplicativos Ruby para o Serviço de Aplicativo do Azure no Linux.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/28/2019
ms.author: cephalin
ms.reviewer: astay; kraigb
ms.custom: seodec18
ms.openlocfilehash: 95a848ff7d74d35203c7e8377405c709f7fc7bd7
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67617384"
---
# <a name="configure-a-linux-ruby-app-for-azure-app-service"></a>Configurar um aplicativo Ruby do Linux para o Serviço de Aplicativo do Azure

Este artigo descreve como o [Serviço de Aplicativo do Azure](app-service-linux-intro.md) executa aplicativos Ruby e como você pode personalizar o comportamento do Serviço de Aplicativo quando necessário. Os aplicativos Ruby precisam ser implantados com todos os módulos [pip](https://pypi.org/project/pip/) necessários.

Este guia fornece conceitos e instruções essenciais para desenvolvedores do Ruby que usam um contêiner interno do Linux no Serviço de Aplicativo. Se você nunca usou o Serviço de Aplicativo do Azure, siga o [Início Rápido do Ruby](quickstart-ruby.md) e o [tutorial do Ruby com o PostgreSQL](tutorial-ruby-postgres-app.md) primeiro.

## <a name="show-ruby-version"></a>Mostrar a versão do Ruby

Para mostrar a versão atual do Ruby, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Para mostrar todas as versões do Ruby compatíveis, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep RUBY
```

Você pode executar uma versão sem suporte do Ruby criando sua própria imagem de contêiner. Para obter mais informações, confira [Usar uma imagem do Docker personalizada](tutorial-custom-docker-image.md).

## <a name="set-ruby-version"></a>Definir a versão do Ruby

Execute o seguinte comando no [Cloud Shell](https://shell.azure.com) para definir a versão do Ruby como 2.3:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "RUBY|2.3"
```

> [!NOTE]
> Caso você veja erros semelhantes aos seguintes no momento da implantação:
> ```
> Your Ruby version is 2.3.3, but your Gemfile specified 2.3.1
> ```
> ou o
> ```
> rbenv: version `2.3.1' is not installed
> ```
> Isso significa que a versão do Ruby configurada no projeto é diferente da versão que está instalada no contêiner executado (`2.3.3` no exemplo acima). No exemplo acima, marque *Gemfile* e *.ruby-version* e verifique se a versão do Ruby não está definida ou se está definida como a versão que está instalada no contêiner que está sendo executado (`2.3.3` no exemplo acima).

## <a name="access-environment-variables"></a>Acessar variáveis de ambiente

No Serviço de Aplicativo, você pode [definir configurações de aplicativo](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) fora do código do aplicativo. Em seguida, você pode acessá-las usando o padrão [ENV['\<nome-do-caminho>']](https://ruby-doc.org/core-2.3.3/ENV.html). Por exemplo, para acessar uma configuração de aplicativo chamada `WEBSITE_SITE_NAME`, use o seguinte código:

```ruby
ENV['WEBSITE_SITE_NAME']
```

## <a name="customize-deployment"></a>Personalizar a implantação

Quando você implanta um [repositório do Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) ou um [pacote zip](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) com processos de build ativados, o mecanismo de implantação (Kudu) executa automaticamente as seguintes etapas pós-implantação por padrão:

1. Verifique se há um *Gemfile*.
1. Execute `bundle clean`. 
1. Execute `bundle install --path "vendor/bundle"`.
1. Execute `bundle package` para empacotar joias na pasta vendor/cache.

### <a name="use---without-flag"></a>Usar o sinalizador --without

Para executar `bundle install` com o sinalizador [--without](https://bundler.io/man/bundle-install.1.html), defina a [configuração de aplicativo](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)`BUNDLE_WITHOUT` como uma lista separada por vírgula de grupos. Por exemplo, o seguinte comando a define como `development,test`.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings BUNDLE_WITHOUT="development,test"
```

Se essa configuração for definida, o mecanismo de implantação executará `bundle install` com `--without $BUNDLE_WITHOUT`.

### <a name="precompile-assets"></a>Pré-compilar ativos

As etapas pós-implantação não pré-compilam os ativos por padrão. Para ativar a pré-compilação de ativos, defina a `ASSETS_PRECOMPILE`[configuração de aplicativo](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) como `true`. Em seguida, o comando `bundle exec rake --trace assets:precompile` é executado no final das etapas pós-implantação. Por exemplo:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASSETS_PRECOMPILE=true
```

Para obter mais informações, confira [Fornecer ativos estáticos](#serve-static-assets).

## <a name="customize-start-up"></a>Personalizar a inicialização

Por padrão, o contêiner do Ruby inicia o servidor Rails na seguinte sequência (para obter mais informações, confira o [script de inicialização](https://github.com/Azure-App-Service/ruby/blob/master/2.3.8/startup.sh)):

1. Gere um valor [secret_key_base](https://edgeguides.rubyonrails.org/security.html#environmental-security), caso ainda não exista nenhum. Esse valor é necessário para que o aplicativo seja executado no modo de produção.
1. Defina a variável de ambiente `RAILS_ENV` como `production`.
1. Exclua qualquer arquivo *.pid* no diretório *tmp/pids* deixado por um servidor Rails anteriormente em execução.
1. Verifique se todas as dependências estão instaladas. Caso contrário, tente instalar joias do diretório local *vendor/cache*.
1. Execute `rails server -e $RAILS_ENV`.

Você pode personalizar o processo de inicialização das seguintes maneiras:

- [Fornecer ativos estáticos](#serve-static-assets)
- [Executar no modo de não produção](#run-in-non-production-mode)
- [Definir secret_key_base manualmente](#set-secret_key_base-manually)

### <a name="serve-static-assets"></a>Fornecer ativos estáticos

O servidor Rails no contêiner do Ruby é executado no modo de produção por padrão e [pressupõe que os ativos sejam pré-compilados e fornecidos pelo servidor Web](https://guides.rubyonrails.org/asset_pipeline.html#in-production). Para fornecer ativos estáticos no servidor Rails, é necessário realizar duas tarefas:

- **Pré-compilar os ativos** - [Pré-compile os ativos estáticos localmente](https://guides.rubyonrails.org/asset_pipeline.html#local-precompilation) e implante-os manualmente. Se preferir, permita que o mecanismo de implantação cuide disso (confira [Pré-compilar ativos](#precompile-assets).
- **Habilitar o fornecimento de arquivos estáticos** – para fornecer ativos estáticos do contêiner do Ruby, [defina a configuração de aplicativo `RAILS_SERVE_STATIC_FILES` `RAILS_SERVE_STATIC_FILES`](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) como `true`. Por exemplo:

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_SERVE_STATIC_FILES=true
    ```

### <a name="run-in-non-production-mode"></a>Executar no modo de não produção

O servidor Rails é executado no modo de produção por padrão. Para executá-lo no modo de desenvolvimento, por exemplo, defina a [configuração de aplicativo](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) `RAILS_ENV` como `development`.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_ENV="development"
```

No entanto, essa configuração isolada faz com que o servidor Rails seja iniciado no modo de desenvolvimento, o que aceita solicitações somente de localhost e não é acessível fora do contêiner. Para aceitar solicitações de cliente remoto, defina a [configuração de aplicativo](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) `APP_COMMAND_LINE` como `rails server -b 0.0.0.0`. Essa configuração de aplicativo permite executar um comando personalizado no contêiner do Ruby. Por exemplo:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings APP_COMMAND_LINE="rails server -b 0.0.0.0"
```

### <a name="set-secret_key_base-manually"></a>Definir secret_key_base manualmente

Para usar seu próprio valor `secret_key_base` em vez de deixar o Serviço de Aplicativo gerar um para você, defina a [configuração de aplicativo](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) `SECRET_KEY_BASE` com o valor desejado. Por exemplo:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings SECRET_KEY_BASE="<key-base-value>"
```

## <a name="access-diagnostic-logs"></a>Acessar logs de diagnóstico

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Abra a sessão SSH aberta no navegador

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Aplicativo Rails com o PostgreSQL](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Perguntas frequentes sobre o Serviço de Aplicativo no Linux](app-service-linux-faq.md)

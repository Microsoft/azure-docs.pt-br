---
title: 'Início Rápido: Sua primeira consulta Ruby'
description: Neste guia de início rápido, você seguirá as etapas necessárias para habilitar o gem do Resource Graph para Ruby e executará sua primeira consulta.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 41769359eaa1f930d34b0a182f35da38a6f5109d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98920030"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-ruby"></a>Início Rápido: Executar sua primeira consulta do Resource Graph usando o Ruby

A primeira etapa para usar o Azure Resource Graph é verificar se os gems necessários para o Ruby estão instalados. Este guia de início rápido descreve o processo de adição dos gems à instalação do Ruby.

No final desse processo, você terá adicionado os gems à sua instalação do Ruby e executado sua primeira consulta do Resource Graph.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.
- Uma entidade de serviço do Azure, incluindo _clientId_ e _clientSecret_.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-graph-project"></a>Criar o projeto do Resource Graph

Para habilitar o Ruby a consultar o Azure Resource Graph, o gem precisará ser adicionado ao `Gemfile`. Esse gem funciona sempre que o Ruby possa ser usado, incluindo com o [Azure Cloud Shell](https://shell.azure.com), o [Bash no Windows 10](/windows/wsl/install-win10) ou instalado localmente.

1. Verifique se o Ruby mais recente está instalado (no mínimo, a versão **2.7.1**). Se ele ainda não estiver sido instalado, baixe-o em [Ruby-Lang.org](https://www.ruby-lang.org/en/downloads/).

1. No ambiente Ruby de sua escolha, inicialize um pacote em uma nova pasta de projeto:

   ```bash
   # Initialize a bundle to create a new Gemfile
   bundle init
   ```

1. Atualize o `Gemfile` com os gems necessários para o Azure Resource Graph. O arquivo atualizado deverá ser parecido com este exemplo:

   ```file
   # frozen_string_literal: true

   source "https://rubygems.org"

   git_source(:github) {|repo_name| "https://github.com/#{repo_name}" }

   # gem "rails"
   gem 'azure_mgmt_resourcegraph', '~> 0.17.2'
   ```

1. Na pasta do projeto, execute `bundle install`. Confirme se os gems foram instalados com `bundle list`.

1. Na mesma pasta do projeto, crie `argQuery.rb` com o seguinte código e salve o arquivo atualizado:

   ```ruby
   #!/usr/bin/env ruby

   require 'azure_mgmt_resourcegraph'
   ARG = Azure::ResourceGraph::Profiles::Latest::Mgmt

   # Get arguments and set options
   options = {
       tenant_id: ARGV[0],
       client_id: ARGV[1],
       client_secret: ARGV[2],
       subscription_id: ARGV[3]
   }

   # Create Resource Graph client from options
   argClient = ARG::Client.new(options)

   # Create Resource Graph QueryRequest for subscription with query
   query_request = ARGModels::QueryRequest.new.tap do |qr|
       qr.subscriptions = [ARGV[3]]
       qr.query = ARGV[4]
   end

   # Get the resources from Resource Graph
   response = argClient.resources(query_request)

   # Convert data to JSON and output
   puts response.data.to_json
   ```

## <a name="run-your-first-resource-graph-query"></a>Executar a primeira consulta ao Resource Graph

Com o script Ruby salvo e pronto para uso, é hora de experimentar uma consulta simples do Resource Graph. A consulta retorna os cinco primeiros recursos do Azure com o **Nome** e o **Tipo de Recurso** de cada um.

Em cada chamada à `argQuery`, há variáveis usadas que precisam ser substituídas com seus valores:

- `{tenantId}` – Substitua pela ID de locatário
- `{clientId}` – substituir pela ID do cliente da entidade de serviço
- `{clientSecret}` – substituir pelo segredo do cliente da entidade de serviço
- `{subscriptionId}`: substitua por sua ID da assinatura

1. Altere os diretórios para a pasta do projeto em que você criou os arquivos `Gemfile` e `argClient.rb`.

1. Execute sua primeira consulta do Azure Resource Graph usando os gems e o método `resources`:

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5"
   ```

   > [!NOTE]
   > Devido a essa consulta de exemplo não fornecer um modificador de classificação, tal como `order by`, executar essa consulta várias vezes provavelmente produzirá um conjunto diferente de recursos por solicitação.

1. Altere o primeiro parâmetro para `argQuery.rb` e altere a consulta para `order by` a propriedade **Name**:

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5 | order by name asc"
   ```

   > [!NOTE]
   > Assim como ocorre com a primeira consulta, executar esta consulta várias vezes provavelmente produzirá um conjunto diferente de recursos por solicitação. A ordem dos comandos de consulta é importante. Neste exemplo, o `order by` vem após o `limit`. Essa ordem de comando limita primeiro os resultados da consulta e os ordena.

1. Altere o parâmetro final para `argQuery.rb` e altere a consulta para, primeiro, `order by` a propriedade **Name** e depois `limit` conforme os primeiros cinco resultados:

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | order by name asc | limit 5"
   ```

Quando a consulta final for executada várias vezes, supondo que nada em seu ambiente esteja sendo alterado, os resultados retornados serão consistentes e ordenados segundo a propriedade **Name**, mas ainda limitados aos cinco primeiros resultados.

## <a name="clean-up-resources"></a>Limpar os recursos

Caso deseje remover os gems instalados do ambiente Ruby, faça isso usando o seguinte comando:

```bash
# Remove the installed gems from the Ruby environment
gem uninstall azure_mgmt_resourcegraph
```

> [!NOTE]
> O gem `azure_mgmt_resourcegraph` tem dependências como `ms_rest` e `ms_rest_azure` que também podem ter sido instaladas, dependendo do ambiente. Você poderá desinstalar esses gems também se eles não forem mais necessários.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você adicionou os gems do Resource Graph ao ambiente Ruby e executou sua primeira consulta. Para saber mais sobre a linguagem do Resource Graph, prossiga para a página de detalhes da linguagem de consulta.

> [!div class="nextstepaction"]
> [Obter mais informações sobre a linguagem de consulta](./concepts/query-language.md)

---
author: spelluru
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: spelluru
ms.openlocfilehash: 5c2959a1bf6225c164f8538c3c437e464d834b96
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96027844"
---
## <a name="create-a-ruby-application"></a>Criar um aplicativo Ruby
Para obter instruções, confira [Criar um aplicativo Ruby no Azure (a página pode estar em inglês)](/previous-versions/azure/virtual-machines/linux/classic/ruby-rails-web-app).

## <a name="configure-your-application-to-use-service-bus"></a>Configurar seu aplicativo para usar o Barramento de Serviço
Para usar o Barramento de Serviço, baixe e use o pacote do Azure Ruby, que inclui um conjunto de bibliotecas convenientes que se comunicam com os serviços REST de armazenamento.

### <a name="use-rubygems-to-obtain-the-package"></a>Usar RubyGems para obter o pacote
1. Use uma interface de linha de comando como **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix).
2. Digite "gem install azure" na janela de comandos para instalar a gema e as dependências.

### <a name="import-the-package"></a>Importar o pacote
Usando seu editor de texto favorito, adicione o seguinte na parte superior do arquivo do Ruby no qual você pretende usar o armazenamento:

```ruby
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>Configurar uma conexão do Barramento de Serviço
Use o código a seguir para definir os valores de namespace, nome da chave, chave, assinante e host:

```ruby
Azure.configure do |config|
  config.sb_namespace = '<your azure service bus namespace>'
  config.sb_sas_key_name = '<your azure service bus access keyname>'
  config.sb_sas_key = '<your azure service bus access key>'
end
signer = Azure::ServiceBus::Auth::SharedAccessSigner.new
sb_host = "https://#{Azure.sb_namespace}.servicebus.windows.net"
```

Defina o valor do namespace para o valor que você criou, em vez de toda a URL. Por exemplo, use **"yourexamplenamespace"** , não "yourexamplenamespace.servicebus.windows.net".

Ao trabalhar com vários namespaces, você pode passar a chave e seu nome ao construtor ao criar objetos `SharedAccessSigner`

```ruby
sb_namespace = '<your azure service bus namespace>'
sb_sas_key_name = '<your azure service bus access keyname>'
sb_sas_key = '<your azure service bus access key>'

signer = Azure::ServiceBus::Auth::SharedAccessSigner.new(sb_sas_key_name, sb_sas_key)
sb_host = "https://#{sb_namespace}.servicebus.windows.net"
```
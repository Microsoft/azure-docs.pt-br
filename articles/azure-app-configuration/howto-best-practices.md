---
title: Práticas recomendadas de configuração do Azure App | Microsoft Docs
description: Conheça as práticas recomendadas ao usar a configuração de Azure App. Os tópicos abordados incluem agrupamentos de chaves, composições de valor-chave, inicialização de configuração de aplicativo e muito mais.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 33661eafee6b180819b18d9a9a980eff1e2aeceb
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371542"
---
# <a name="azure-app-configuration-best-practices"></a>Práticas recomendadas de configuração do Azure App

Este artigo aborda padrões comuns e práticas recomendadas quando você estiver usando Azure App configuração.

## <a name="key-groupings"></a>Agrupamentos de chaves

A configuração do aplicativo fornece duas opções para organizar as chaves:

* Prefixos de chave
* Rótulos

Você pode usar uma ou ambas as opções para agrupar suas chaves.

Os *prefixos de chave* são as partes iniciais das chaves. Você pode agrupar logicamente um conjunto de chaves usando o mesmo prefixo em seus nomes. Os prefixos podem conter vários componentes conectados por um delimitador, como `/` , semelhante a um caminho de URL, para formar um namespace. Essas hierarquias são úteis quando você está armazenando chaves para muitos aplicativos, serviços de componentes e ambientes em um repositório de configurações de aplicativo.

Uma coisa importante a ter em mente é que as chaves são as referências do código do aplicativo para recuperar os valores das configurações correspondentes. As chaves não devem ser alteradas ou, caso contrário, você precisará modificar seu código sempre que isso acontecer.

Os *Rótulos* são um atributo nas chaves. Eles são usados para criar variantes de uma chave. Por exemplo, você pode atribuir rótulos a várias versões de uma chave. Uma versão pode ser uma iteração, um ambiente ou algumas outras informações contextuais. Seu aplicativo pode solicitar um conjunto totalmente diferente de valores de chave especificando outro rótulo. Como resultado, todas as referências de chave permanecem inalteradas no seu código.

## <a name="key-value-compositions"></a>Composições de valor-chave

A configuração de aplicativo trata todas as chaves armazenadas com ela como entidades independentes. A configuração do aplicativo não tenta inferir nenhuma relação entre as chaves ou para herdar valores de chave com base em sua hierarquia. Você pode agregar vários conjuntos de chaves, no entanto, usando rótulos acoplados ao empilhamento de configuração adequado no código do aplicativo.

Vejamos um exemplo. Suponha que você tenha uma configuração chamada **Asset1**, cujo valor pode variar com base no ambiente de desenvolvimento. Você cria uma chave chamada "Asset1" com um rótulo vazio e um rótulo chamado "desenvolvimento". No primeiro rótulo, você coloca o valor padrão para **Asset1** e coloca um valor específico para "desenvolvimento" no último.

No seu código, você primeiro recupera os valores de chave sem rótulos e, em seguida, recupera o mesmo conjunto de valores de chave pela segunda vez com o rótulo "desenvolvimento". Quando você recupera os valores da segunda vez, os valores anteriores das chaves são substituídos. O sistema de configuração do .NET Core permite que você "empilhe" vários conjuntos de dados de configuração em cima um do outro. Se existir uma chave em mais de um conjunto, o último conjunto que a contém será usado. Com uma estrutura de programação moderna, como o .NET Core, você obterá essa capacidade de empilhamento gratuitamente se usar um provedor de configuração nativo para acessar a configuração do aplicativo. O trecho de código a seguir mostra como você pode implementar o empilhamento em um aplicativo .NET Core:

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Select(KeyFilter.Any, LabelFilter.Null)
           .Select(KeyFilter.Any, "Development");
});
```

[Use rótulos para habilitar configurações diferentes para ambientes diferentes](./howto-labels-aspnet-core.md) fornece um exemplo completo.

## <a name="app-configuration-bootstrap"></a>Inicialização da configuração do aplicativo

Para acessar um repositório de configuração de aplicativo, você pode usar sua cadeia de conexão, que está disponível no portal do Azure. Como as cadeias de conexão contêm informações de credenciais, elas são consideradas segredos. Esses segredos precisam ser armazenados em Azure Key Vault e seu código deve se autenticar no Key Vault para recuperá-los.

Uma opção melhor é usar o recurso identidades gerenciadas no Azure Active Directory. Com identidades gerenciadas, você precisa apenas da URL de ponto de extremidade de configuração de aplicativo para inicializar o acesso ao seu repositório de configuração de aplicativo. Você pode inserir a URL no código do aplicativo (por exemplo, na *appsettings.jsno* arquivo). Consulte [integrar com identidades gerenciadas do Azure](howto-integrate-azure-managed-service-identity.md) para obter detalhes.

## <a name="app-or-function-access-to-app-configuration"></a>Acesso de aplicativo ou função à configuração de aplicativo

Você pode fornecer acesso à configuração de aplicativo para aplicativos Web ou funções usando qualquer um dos seguintes métodos:

* Por meio do portal do Azure, insira a cadeia de conexão para o repositório de configurações do aplicativo nas configurações do aplicativo do serviço de aplicativo.
* Armazene a cadeia de conexão em seu repositório de configuração de aplicativo no Key Vault e [referencie-a do serviço de aplicativo](../app-service/app-service-key-vault-references.md).
* Use identidades gerenciadas do Azure para acessar o repositório de configuração de aplicativo. Para obter mais informações, consulte [integrar com identidades gerenciadas do Azure](howto-integrate-azure-managed-service-identity.md).
* Enviar por push a configuração de aplicativo para o serviço de aplicativo. A configuração de aplicativo fornece uma função de exportação (em portal do Azure e a CLI do Azure) que envia dados diretamente para o serviço de aplicativo. Com esse método, você não precisa alterar o código do aplicativo.

## <a name="reduce-requests-made-to-app-configuration"></a>Reduzir solicitações feitas à configuração do aplicativo

Solicitações excessivas para a configuração do aplicativo podem resultar na limitação ou encargos excedentes. Para reduzir o número de solicitações feitas:

* Aumente o tempo limite de atualização, especialmente se os valores de configuração não forem alterados com frequência. Especifique um novo tempo limite de atualização usando o [ `SetCacheExpiration` método](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.azureappconfigurationrefreshoptions.setcacheexpiration).

* Assista a uma única *chave do Sentinela*, em vez de observar as chaves individuais. Atualize todas as configurações somente se a chave do Sentinela for alterada. Consulte [usar a configuração dinâmica em um aplicativo ASP.NET Core](enable-dynamic-configuration-aspnet-core.md) para obter um exemplo.

* Use a grade de eventos do Azure para receber notificações quando a configuração for alterada, em vez de sondar constantemente qualquer alteração. Para obter mais informações, consulte [rotear eventos de configuração de Azure app para um ponto de extremidade da Web](./howto-app-configuration-event.md)

## <a name="importing-configuration-data-into-app-configuration"></a>Importando dados de configuração para configuração de aplicativo

A configuração de aplicativo oferece a opção de [importar](./howto-import-export-data.md) em massa suas definições de configuração de seus arquivos de configuração atuais usando o portal do Azure ou a CLI. Você também pode usar as mesmas opções para exportar valores da configuração do aplicativo, por exemplo, entre os repositórios relacionados. Se você quiser configurar uma sincronização contínua com seu repositório GitHub, poderá usar nossa [ação do GitHub](./concept-github-action.md) para que possa continuar usando as práticas de controle do código-fonte existentes e obter os benefícios da configuração do aplicativo.

## <a name="multi-region-deployment-in-app-configuration"></a>Implantação em várias regiões na configuração do aplicativo

A configuração do aplicativo é um serviço regional. Para aplicativos com configurações diferentes por região, armazenar essas configurações em uma instância pode criar um ponto único de falha. A implantação de instâncias de configuração de um aplicativo por região em várias regiões pode ser uma opção melhor. Ele pode ajudar com a recuperação de desastre regional, o desempenho e o silo de segurança. A configuração por região também melhora a latência e usa cotas de limitação separadas, pois a limitação é por instância. Para aplicar a mitigação de recuperação de desastres, você pode usar [vários repositórios de configuração](./concept-disaster-recovery.md). 

## <a name="client-applications-in-app-configuration"></a>Aplicativos cliente na configuração do aplicativo 

Solicitações excessivas para a configuração do aplicativo podem resultar na limitação ou encargos excedentes. Os aplicativos aproveitam o Caching e a atualização inteligente disponíveis no momento para otimizar o número de solicitações que eles enviam. Esse processo pode ser espelhado em aplicativos cliente de alto volume, evitando conexões diretas com o repositório de configurações. Em vez disso, os aplicativos cliente se conectam a um serviço personalizado e esse serviço se comunica com o repositório de configuração. Essa solução de proxy pode garantir que os aplicativos cliente não se aproximam do limite de limitação no repositório de configuração. Para obter mais informações sobre a limitação, consulte [as perguntas frequentes](./faq.yml#are-there-any-limits-on-the-number-of-requests-made-to-app-configuration).  

## <a name="next-steps"></a>Próximas etapas

* [Chaves e valores](./concept-key-value.md)
---
author: baanders
description: incluir arquivo dos tutoriais dos Gêmeos Digitais do Azure – configurando o projeto de exemplo
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 67a2799a93141ad84f458642d8499a58784cc19c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103463691"
---
## <a name="configure-the-sample-project"></a>Configurar o projeto de exemplo

A seguir, configure um aplicativo cliente de exemplo que vai interagir com sua instância dos Gêmeos Digitais do Azure.

Navegue no computador até o arquivo baixado anteriormente de [*Amostras de ponta a ponta dos Gêmeos Digitais do Azure*](/samples/azure-samples/digital-twins-samples/digital-twins-samples) (e descompacte-o se ainda não tiver feito isso).

Na pasta, navegue até _AdtSampleApp_. Abra _**AdtE2ESample.sln**_ no Visual Studio 2019. 

No Visual Studio, selecione o arquivo _SampleClientApp > **appsettings.json**_ para abri-lo na janela de edição. Ele funcionará como um arquivo JSON predefinido com as variáveis de configuração necessárias para executar o projeto.

No corpo do arquivo, altere a `instanceUrl` para a *URL do hostName* da sua instância dos Gêmeos Digitais do Azure (adicionando **_https://_** na frente do *hostName*, como mostrado abaixo).

```json
{
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-hostName>"
}
```

Salve e feche o arquivo. 

Em seguida, configure o arquivo *appSettings.json* para ser copiado para o diretório de saída quando você criar o *SampleClientApp*. Para fazer isso, selecione com o botão direito do mouse o arquivo *appsettings.json* e escolha **Propriedades**. No inspetor de **Propriedades**, procure pela propriedade *Copiar para Diretório de Saída*. Altere o valor para **Copiar se for mais recente** se ainda não estiver definido.

:::image type="content" source="../articles/digital-twins/media/includes/copy-config.png" alt-text="Trecho da janela do Visual Studio mostrando o painel do Gerenciador de Soluções com appSettings.json realçado e o painel Propriedades com a propriedade 'Copiar para o Diretório de Saída' definida como 'Copiar se mais recente'" border="false" lightbox="../articles/digital-twins/media/includes/copy-config.png":::

Mantenha o projeto _**AdtE2ESample**_ aberto no Visual Studio para continuar a usá-lo no tutorial.

[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](digital-twins-local-credentials-outer.md)]

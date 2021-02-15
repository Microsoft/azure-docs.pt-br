---
title: Entender o gerenciamento de recursos usando a configuração do Azure App
description: Ativar e desativar recursos usando a configuração do Azure App
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.custom: devx-track-dotnet
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: cfd676092bcaede58909a3ec1eefeabb4c80f86b
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96930221"
---
# <a name="feature-management-overview"></a>Visão geral do gerenciamento de recursos

Tradicionalmente, o envio de um novo recurso de aplicativo exige uma reimplantação completa do aplicativo em si. O teste de um recurso geralmente requer várias implantações do aplicativo.  Cada implantação pode alterar o recurso ou expor o recurso a diferentes clientes para teste.  

O gerenciamento de recursos é uma prática moderna de desenvolvimento de software que separa a liberação do recurso da implantação do código e permite alterações rápidas na disponibilidade de recursos sob demanda. Ele usa uma técnica chamada *sinalizador de recursos* (também conhecida como *alternância de funcionalidades*, *opções de recurso* e assim por diante) para administrar o ciclo de vida de um recurso dinamicamente.

O gerenciamento de recursos ajuda os desenvolvedores a resolver os seguintes problemas:

* **Gerenciamento de ramificação de código**: Use sinalizadores de recurso para encapsular a nova funcionalidade de aplicativo no momento em desenvolvimento. Essa funcionalidade é "oculta" por padrão. Você pode enviar o recurso com segurança, embora não esteja concluído, e ele permanecerá inativo na produção. Usando essa abordagem, chamada *implantação escura*, você pode liberar todo o código no final de cada ciclo de desenvolvimento. Você não precisa mais manter as ramificações de código entre vários ciclos de desenvolvimento porque um determinado recurso requer mais de um ciclo para ser concluído.
* **Teste em produção**: Use os sinalizadores de recurso para conceder acesso antecipado a novas funcionalidades na produção. Por exemplo, você pode limitar o acesso a membros da equipe ou a testadores beta internos. Esses usuários terão experiência em produção de fidelidade total em vez de uma experiência simulada ou parcial em um ambiente de teste.
* **Comprovando**: Use os sinalizadores de recurso para distribuir incrementalmente novas funcionalidades para os usuários finais. Você pode direcionar uma pequena porcentagem da população do usuário primeiro e aumentar essa porcentagem gradualmente ao longo do tempo.
* **Interruptor de eliminação instantânea**: os sinalizadores de recursos fornecem uma rede de segurança inerente para liberar novas funcionalidades. Você pode ativar e desativar recursos do aplicativo sem reimplantar nenhum código. Você obtém a capacidade de desabilitar rapidamente um recurso, se necessário, sem a necessidade de recompilar e reimplantar seu aplicativo.
* **Ativação seletiva**: Use os sinalizadores de recurso para segmentar seus usuários e fornecer um conjunto específico de recursos para cada grupo. Você pode ter um recurso que funciona apenas em um determinado navegador da Web. Defina um sinalizador de recurso para que somente os usuários desse navegador possam ver e usar o recurso. Com essa abordagem, você pode expandir com facilidade a lista de navegadores compatíveis posteriormente sem precisar alterar códigos.

## <a name="basic-concepts"></a>Conceitos básicos

Veja os vários novos termos relacionados ao gerenciamento de recursos:

* **Sinalizador de recurso**: um sinalizador de recurso é uma variável com um estado binário de *ativado* ou *desativado*. O sinalizador de recurso também tem um bloco de código associado. O estado do sinalizador de recurso dispara se o bloco de código é executado.
* **Gerenciador de recursos**: um Gerenciador de recursos é um pacote de aplicativos que manipula o ciclo de vida de todos os sinalizadores de recurso em um aplicativo. O Gerenciador de recursos também fornece funcionalidade adicional, incluindo sinalizadores de recursos de cache e atualização de seus Estados.
* **Filtro**: um filtro é uma regra para avaliar o estado de um sinalizador de recurso. Os filtros potenciais incluem grupos de usuários, tipos de dispositivo ou navegador, localizações geográficas e janelas de tempo.

Uma implementação efetiva do gerenciamento de recursos consiste em pelo menos dois componentes trabalhando em conjunto:

* Um aplicativo que usa sinalizadores de recursos.
* Um repositório separado que armazena os sinalizadores de recursos e seus estados atuais.

## <a name="using-feature-flags-in-your-code"></a>Usando sinalizadores de recurso em seu código

O padrão básico para implementar sinalizadores de recurso em um aplicativo é simples. Um sinalizador de recurso é uma variável de estado booliana que controla uma instrução condicional em seu código:

```csharp
if (featureFlag) {
    // Run the following code
}
```

Você pode definir o valor de `featureFlag` estaticamente.

```csharp
bool featureFlag = true;
```

Você pode avaliar o estado do sinalizador com base em determinadas regras:

```csharp
bool featureFlag = isBetaUser();
```

Você pode estender a condicional para definir o comportamento do aplicativo para qualquer um dos Estados:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>Repositório de sinalizadores de recurso

Para usar sinalizadores de recursos com eficiência, você precisará externalizar todos os sinalizadores de recursos usados em um aplicativo. Isso permite que você altere os Estados de sinalizador de recurso sem modificar e reimplantar o próprio aplicativo.

Azure App configuração fornece um repositório centralizado para sinalizadores de recursos. Você pode utilizá-la para definir diferentes tipos de sinalizadores de recurso e manipular seus estados de maneira rápida e segura. Você pode usar as bibliotecas da Configuração de Aplicativos para várias estruturas de linguagem de programação para acessar esses sinalizadores de recurso com facilidade em seu aplicativo.

O artigo [Usar sinalizadores de recursos em um aplicativo ASP.NET Core](./use-feature-flags-dotnet-core.md) mostra como o provedor da Configuração de Aplicativos do .NET Core e as bibliotecas do Gerenciamento de Recursos são usados juntos para implementação de sinalizadores de recursos para seu aplicativo Web ASP.NET.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adicionar sinalizadores de recurso a um aplicativo Web ASP.NET Core](./quickstart-feature-flag-aspnet-core.md)  

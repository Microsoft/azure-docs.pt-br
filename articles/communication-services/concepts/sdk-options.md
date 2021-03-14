---
title: Bibliotecas de cliente e APIs REST para os serviços de comunicação do Azure
titleSuffix: An Azure Communication Services concept document
description: Saiba mais sobre as bibliotecas de cliente dos serviços de comunicação do Azure e as APIs REST.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/18/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 8f82b59a9702ee249c2e8f330b4aec1ba512d2f3
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "102636072"
---
# <a name="client-libraries-and-rest-apis"></a>Bibliotecas de cliente e APIs REST

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]


As funcionalidades dos serviços de comunicação do Azure são organizadas conceitualmente em seis áreas. Algumas áreas têm bibliotecas de cliente totalmente abertas. A biblioteca de cliente de chamada usa interfaces de rede proprietárias e está fechada no momento e a biblioteca de chat inclui uma dependência de código-fonte fechado. Exemplos e detalhes técnicos adicionais para bibliotecas de cliente são publicados no [repositório GitHub dos serviços de comunicação do Azure](https://github.com/Azure/communication).

## <a name="client-libraries"></a>Bibliotecas de cliente

| Assembly               | Protocolos             |Abrir vs. fonte fechada| Namespaces                          | Funcionalidades                                                      |
| ---------------------- | --------------------- | ---|-------------------------- | --------------------------------------------------------------------------- |
| Azure Resource Manager | REST | Aberto            | Azure. ResourceManager. comunicação | Provisionar e gerenciar recursos de serviços de comunicação             |
| Comum                 | REST | Aberto               | Azure. Communication. comum          | Fornece tipos de base para outras bibliotecas de cliente |
| Identity         | REST | Aberto               | Azure. Communication. Identity  | Gerenciar usuários, tokens de acesso |
| Números de telefone         | REST | Aberto               | Azure. Communication. PhoneNumbers  | Gerenciando números de telefone |
| Chat                   | REST com sinalização proprietária | Abrir com pacote de sinalização de origem fechado    | Azure. Communication. chat            | Adicionar chat com base em texto em tempo real aos seus aplicativos  |
| sms                    | REST | Aberto              | Azure. Communication. SMS             | Enviar e receber mensagens SMS |
| Chamando                | Transporte proprietário | Fechadas |Azure. Communication. chamando         | Aproveite o uso de voz, vídeo, compartilhamento de tela e outros recursos de comunicação de dados em tempo real          |

Observe que as bibliotecas de cliente de Azure Resource Manager, identidade e SMS se concentram na integração de serviços e, em muitos casos, surgem problemas de segurança se você integrar essas funções a aplicativos de usuário final. As bibliotecas de cliente comuns e de chat são adequadas para aplicativos de serviço e cliente. A biblioteca de cliente de chamada é projetada para aplicativos cliente. Uma biblioteca de cliente focada em cenários de serviço está em desenvolvimento.

### <a name="languages-and-publishing-locations"></a>Idiomas e locais de publicação

Os locais de publicação para pacotes de biblioteca de cliente individual são detalhados abaixo.

| Área           | JavaScript | .NET | Python | Java SE | iOS | Android | Outro                          |
| -------------- | ---------- | ---- | ------ | ---- | -------------- | -------------- | ------------------------------ |
| Azure Resource Manager | -         | [NuGet](https://www.nuget.org/packages/Azure.ResourceManager.Communication)    |   [PyPi](https://pypi.org/project/azure-mgmt-communication/)    |  -  | -              | -  | [Go por meio do GitHub](https://github.com/Azure/azure-sdk-for-go/releases/tag/v46.3.0) |
| Comum         | [npm](https://www.npmjs.com/package/@azure/communication-common)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Common/)    | N/D      | [Maven](https://search.maven.org/search?q=a:azure-communication-common)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)            | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-common)             | -                              |
| Identity | [npm](https://www.npmjs.com/package/@azure/communication-identity)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Identity)    | [PyPi](https://pypi.org/project/azure-communication-identity/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-identity)   | -              | -              | -                            |
| Números de telefone | [npm](https://www.npmjs.com/package/@azure/communication-phone-numbers)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.PhoneNumbers)    | [PyPi](https://pypi.org/project/azure-communication-phonenumbers/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-phonenumbers)   | -              | -              | -                            |
| Chat           | [npm](https://www.npmjs.com/package/@azure/communication-chat)        | [NuGet](https://www.nuget.org/packages/Azure.Communication.Chat)     | [PyPi](https://pypi.org/project/azure-communication-chat/)     | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)  | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | -                              |
| sms            | [npm](https://www.npmjs.com/package/@azure/communication-sms)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Sms)    | [PyPi](https://pypi.org/project/azure-communication-sms/)       | [Maven](https://search.maven.org/artifact/com.azure/azure-communication-sms)   | -              | -              | -                              |
| Chamando        | [npm](https://www.npmjs.com/package/@azure/communication-calling)         | -      | -      | -     | [GitHub](https://github.com/Azure/Communication/releases)     | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-calling/)            | -                              |
| Documentação de referência     | [docs](https://azure.github.io/azure-sdk-for-js/communication.html)         | [docs](https://azure.github.io/azure-sdk-for-net/communication.html)      | -      | [docs](http://azure.github.io/azure-sdk-for-java/communication.html)     | [docs](/objectivec/communication-services/calling/)      | [docs](/java/api/com.azure.communication.calling)            | -                              |

## <a name="rest-apis"></a>APIs REST

As APIs de serviços de comunicação são documentadas junto com outras APIs REST do Azure no [docs.Microsoft.com](/rest/api/azure/). Esta documentação lhe dirá como estruturar suas mensagens HTTP e oferece orientação para usar o postmaster. Esta documentação também é oferecida no formato Swagger no [GitHub](https://github.com/Azure/azure-rest-api-specs).

## <a name="additional-support-details"></a>Detalhes de suporte adicionais

### <a name="ios-and-android-support-details"></a>detalhes de suporte do iOS e Android

- Serviços de comunicação bibliotecas de cliente do iOS destino versão 13 + e Xcode 11 +.
- Bibliotecas de cliente Java do Android destino nível da API do Android 21 + e Android Studio 4.0 +

### <a name="net-support-details"></a>Detalhes do suporte do .NET

Com exceção da chamada, os pacotes de serviços de comunicação são direcionados .NET Standard 2,0, que oferece suporte às plataformas listadas abaixo.

Suporte via .NET Framework 4.6.1
- Windows 10, 8,1, 8 e 7
- Windows Server 2012 R2, 2012 e 2008 R2 SP1

Suporte por meio do .NET Core 2,0:
- Windows 10 (1607 +), 7 SP1 +, 8,1
- Windows Server 2008 R2 SP1+
- SO máximo X 10.12 +
- Várias versões/distribuições do Linux
- UWP 10.0.16299 (RS3) de setembro de 2017
- Unity 2018,1
- Mono 5.4
- Xamarin iOS 10,14
- Xamarin Mac 3,8

## <a name="calling-client-library-timeouts"></a>Tempos limite da biblioteca de clientes em chamada

Os tempos limite a seguir se aplicam aos serviços de comunicação que chamam as bibliotecas de cliente:

| Ação           | Tempo limite em segundos |
| -------------- | ---------- |
| Reconectar/remover participante | 120 |
| Adicionar ou remover uma nova modalidade de uma chamada (iniciar/parar o vídeo ou o compartilhamento de tela) | 40 |
| Tempo limite da operação de transferência de chamada | 60 |
| tempo limite de estabelecimento de chamada 1:1 | 85 |
| Tempo limite de estabelecimento de chamada de grupo | 85 |
| Tempo limite de estabelecimento de chamada PSTN | 115 |
| Promover a chamada de 1:1 para um tempo limite de chamada de grupo | 115 |


## <a name="api-stability-expectations"></a>Expectativas de estabilidade da API

> [!IMPORTANT]
> Esta seção fornece diretrizes sobre APIs REST e bibliotecas de cliente marcadas como **estáveis**. As APIs marcadas pré-lançamento, visualização ou beta podem ser alteradas ou preteridas **sem aviso prévio**.

No futuro, podemos desativar versões das bibliotecas de cliente dos serviços de comunicação e podemos introduzir alterações significativas em nossas APIs REST e bibliotecas de cliente lançadas. Os serviços de comunicação do Azure *geralmente* seguirão duas políticas de suporte para desativar versões de serviço:

- Você será notificado pelo menos três anos antes de ser necessário alterar o código devido a uma alteração na interface dos serviços de comunicação. Todas as APIs REST documentadas e APIs de biblioteca de cliente geralmente aproveitam um aviso de pelo menos três anos antes de as interfaces serem encerradas.
- Você será notificado pelo menos um ano antes de atualizar os assemblies da biblioteca de cliente para a versão secundária mais recente. Essas atualizações necessárias não devem exigir nenhuma alteração de código porque estão na mesma versão principal. Isso é especialmente verdadeiro para as bibliotecas de chamada e de chat que têm componentes em tempo real que frequentemente exigem atualizações de segurança e desempenho. É altamente recomendável que você mantenha as bibliotecas de cliente dos serviços de comunicação atualizadas.

### <a name="api-and-client-library-decommissioning-examples"></a>Exemplos de descomissionamento da biblioteca de cliente e API

**Você integrou a versão v24 da API REST do SMS em seu aplicativo. A comunicação do Azure libera v25.**

Você receberá três anos de aviso antes que essas APIs parem de funcionar e sejam forçadas a atualizar para o v25. Essa atualização pode exigir uma alteração de código.

**Você integrou a versão v 2.02 da biblioteca de cliente que está chamando em seu aplicativo. A comunicação do Azure libera v 2.05.**

Talvez seja necessário atualizar para a versão v 2.05 da biblioteca de cliente de chamada em 12 meses após o lançamento do v 2.05. Isso deve ser uma simples substituição do artefato sem a necessidade de uma alteração de código porque v 2.05 está na versão principal V2 e não tem alterações significativas.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte as visões gerais da biblioteca de cliente a seguir:

- [Visão geral da biblioteca de cliente de chamada](../concepts/voice-video-calling/calling-sdk-features.md)
- [Visão geral da biblioteca de cliente de chat](../concepts/chat/sdk-features.md)
- [Visão geral da biblioteca de cliente SMS](../concepts/telephony-sms/sdk-features.md)

Para começar a usar os serviços de comunicação do Azure:

- [Criar recursos de comunicação do Azure](../quickstarts/create-communication-resource.md)
- Gerar [tokens de acesso do usuário](../quickstarts/access-tokens.md)

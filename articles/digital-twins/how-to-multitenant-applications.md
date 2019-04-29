---
title: Ativar aplicativos de vários usuários com os Gêmeos Digitais do Azure| Microsoft Docs
description: Como configurar aplicativos multilocatário do Azure Active Directory para os Gêmeos Digitais do Azure.
author: mavoge
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2019
ms.author: mavoge
ms.openlocfilehash: 2b4f9bf87122f047e496dca1dbd425db8ad7c16c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60925963"
---
# <a name="enable-multitenant-applications-with-azure-digital-twins"></a>Ativar aplicativos de vários usuários com Gêmeos Digitais do Azure

Os desenvolvedores de soluções que criam em Gêmeos Digitais do Azure descobrir que desejam dar suporte a vários clientes com um único serviço ou solução. Na verdade, aplicativos *multilocatários* estão entre as configurações mais comuns dos Gêmeos Digitais do Azure.

Este documento descreve como configurar um aplicativo de Gêmeos Digitais do Azure para dar suporte a vários locatários e clientes do Azure Active Directory.

## <a name="multitenancy"></a>Multilocação

Um recurso *multilocatário* é uma única instância provisionada compatível com vários clientes. Cada cliente tem seus próprios dados e privilégios independentes. A experiência de cada cliente é isolada uma da outra para que as respectivas "exibições" do aplicativo sejam diferente.

Para saber mais sobre multilocação, leia [Aplicativos multilocatário no Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications).

## <a name="problem-scenario"></a>Cenário de problema

Nesse cenário, considere um desenvolvedor criando uma solução dos Gêmeos Digitais do Azure (**DEVELOPER**) e um cliente que usa essa solução (**CUSTOMER**):

- **DEVELOPER** tem uma assinatura do Azure com um locatário do Azure Active Directory.
- O **DEVELOPER** implanta uma instância dos Gêmeos Digitais do Azure em sua assinatura do Azure. O Azure Active Directory automaticamente criou uma entidade de serviço no locatário do Azure Active Directory do **DEVELOPER**.
- Usuários dentro do locatário do Azure Active Directory do **DEVELOPER** então podem [adquirir tokens OAuth 2.0](./security-authenticating-apis.md) do serviço de Gêmeos Digitais do Azure.
- **DEVELOPER** agora cria um aplicativo móvel que se integra diretamente à API de gerenciamento de Gêmeos Digitais do Azure.
- **DEVELOPER** permite ao **CUSTOMER** usar o aplicativo móvel.
- **CUSTOMER** deve estar autorizado a usar a API de gerenciamento dos Gêmeos Digitais do Azure no aplicativo **DEVELOPER**.

O problema:

- Quando o **CUSTOMER** efetua logon no aplicativo do **DEVELOPER**, o aplicativo não consegue adquirir tokens para os usuários do **CUSTOMER** autenticarem-se com as APIs de Gerenciamento dos Gêmeos Digitais do Azure.
- É gerada uma exceção no Azure Active Directory indicando que os Gêmeos Digitais do Azure não são reconhecidos no diretório do **CUSTOMER**.

## <a name="problem-solution"></a>Solução do problema

Para resolver o cenário de problema anterior, as seguintes ações são necessárias para criar uma entidade de serviço dos Gêmeos Digitais do Azure dentro de um locatário do Azure Active Directory do **CUSTOMER**:

- Se **CUSTOMER** ainda não tiver uma assinatura do Azure com um locatário do Azure Active Directory:

  - O administrador do locatário do Azure Active Directory do **CUSTOMER** deverá adquirir uma [assinatura do Azure paga conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/).
  - O administrador do locatário do Azure Active Directory do **CUSTOMER** deverá [vincular seu locatário à nova assinatura](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity).

- No [portal do Azure](https://portal.azure.com), o administrador do locatário do Azure Active Directory do **CUSTOMER** realiza as seguintes etapas:

  1. Abrir **assinaturas**.
  1. Selecione a assinatura que tem um locatário do Azure Active Directory a ser usado no aplicativo do **DEVELOPER**.

     ![Assinaturas do Azure Active Directory][1]

  1. Selecione **Provedores de Recursos**.
  1. Pesquise **Microsoft.IoTSpaces**.
  1. Selecione **Registrar**.

     ![Provedores de recursos do Azure Active Directory][2]
  
## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como usar funções definidas pelo usuário com os Gêmeos Digitais do Azure, leia [Como criar funções definidas pelo usuário dos Gêmeos Digitais do Azure](./how-to-user-defined-functions.md).

- Para saber como usar o controle de acesso baseado em função para proteger ainda mais o aplicativo com as atribuições de função, leia [Como criar e gerenciar controle de acesso baseado em função dos Gêmeos Digitais do Azure](./security-create-manage-role-assignments.md).

<!-- Images -->
[1]: media/multitenant/ad-subscriptions.png
[2]: media/multitenant/ad-resource-providers.png

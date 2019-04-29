---
title: Criar e gerenciar aplicativos do Azure IoT Central como um CSP | Microsoft Docs
description: Como um CSP, como criar um aplicativo do Azure IoT Central em nome de seu cliente.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 04/03/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 8c73c55b059b4d3ca7136889122c11ab332b3f76
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60886288"
---
# <a name="as-a-csp-create-and-manage-an-azure-iot-central-application-on-behalf-of-your-customer"></a>Como um CSP, criar e gerenciar um aplicativo do Azure IoT Central em nome de seu cliente 

O programa Microsoft CSP (Provedor de Soluções na Nuvem) é um programa de Revendedor da Microsoft. Seu objetivo é fornecer a nossos parceiros de canal um programa único para revender todos os Serviços Comerciais Online da Microsoft. Saiba mais sobre o [Programa do Fornecedor de Soluções na Nuvem](https://partner.microsoft.com/cloud-solution-provider).

Como um CSP, você pode criar e gerenciar aplicativos do Microsoft Azure IoT Central em nome de seus clientes por meio da [Microsoft Partner Center](https://partnercenter.microsoft.com/partner/home). Quando os aplicativos do Azure IoT Central são criados em nome dos clientes por CSPs, assim como com outros serviços do Azure gerenciados por CSPs, os CSPs gerenciam a cobrança para os clientes. Um encargo para o Azure IoT Central aparecerão na sua fatura total no Microsoft Partner Center.

Para começar, entre em sua conta no Portal de parceiros da Microsoft e selecione um cliente para o qual você deseja criar um aplicativo do Azure IoT Central. Navegue até o Gerenciamento de serviço para o cliente da barra de navegação à esquerda.

![Microsoft Partner Center, exibição do cliente](media/howto-create-application-asCSP/image1.png)

O Azure IoT Central está listado como um serviço disponível para administrar. Selecione o link do Azure IoT Central da página para criar novos aplicativos ou gerenciar os aplicativos existentes para esse cliente.

![Azure IoT Central disponível para gerenciar](media/howto-create-application-asCSP/image2.png)

Você é direcionado para a página do Gerenciador de Aplicativos do Azure IoT Central. O Azure IoT Central sabe que você veio do Microsoft Partner Center e que você veio para gerenciar esse cliente em particular. Você verá isso confirmado no cabeçalho da página do Gerenciador de Aplicativos. A partir daqui, você pode navegar para um aplicativo existente que você criou anteriormente para esse cliente gerenciar ou criar um novo aplicativo para o cliente.

![Gerenciador de Criação para CSPs](media/howto-create-application-asCSP/image3.png)

Para criar um aplicativo do Azure IoT Central, selecione a **novo aplicativo** lado a lado. Isso carregará a página de Criação de Aplicativos. Você precisa preencher todos os campos dessa página e, em seguida, escolher **Criar**. Você encontrará mais informações sobre cada um desses campos abaixo.

![Página Criar Aplicativo para CSPs](media/howto-create-application-asCSP/image4.png)

![Página Criar Aplicativo para CSPs](media/howto-create-application-asCSP/image4-1.png)

## <a name="payment-plan"></a>Plano de Pagamento

Você só pode criar aplicativos Pay-As-You-Go como CSP. Para demonstrar o Azure IoT Central para o seu cliente, você pode criar um aplicativo de avaliação separadamente. Saiba mais sobre os aplicativos Trial e Pay-As-You-Go na [página de preços da Microsoft IoT Central do Azure](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="application-name"></a>Nome do Aplicativo

O nome do aplicativo é exibido na página **Gerenciador de Aplicativos** e em cada aplicativo Azure IoT Central. É possível escolher qualquer nome para o aplicativo Azure IoT Central. Escolha um nome que faça sentido a você e às outras pessoas na organização.

## <a name="application-url"></a>URL do Aplicativo

A URL do aplicativo é o link para o aplicativo. Você pode salvar um indicador no navegador ou compartilhá-lo com outras pessoas.

Quando você insere o nome para seu aplicativo, a URL do aplicativo é gerado automaticamente. Se preferir, você pode escolher uma URL diferente para o aplicativo. Cada URL do Azure IoT Central deverá ser exclusiva dentro do Azure IoT Central. Se a URL escolhida já tiver sido utilizada, uma mensagem de erro será exibida.

## <a name="directory"></a>Diretório

Como o Azure IoT Central tem contexto fornecidas para gerenciar o cliente selecionado no Portal de parceiros da Microsoft, você pode ver apenas o locatário do Azure Active Directory para esse cliente no campo de diretório. 

Um locatário do Azure Active Directory contém identidades de usuário, credenciais e outras informações organizacionais. Várias assinaturas do Azure podem ser associadas a um único locatário do Azure Active Directory.

Para saber mais, consulte [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="azure-subscription"></a>Assinatura do Azure

Uma assinatura do Azure permite que você crie instâncias de serviços do Azure. O Azure IoT Central localiza automaticamente todas as Assinaturas do Azure do cliente às quais você tem acesso e as exibe em uma lista suspensa na página **Criar Aplicativo**. Escolha uma assinatura do Azure para criar um novo aplicativo Azure IoT Central.

Se você não tiver uma assinatura do Azure, poderá criar uma no Microsoft Partner Center. Após criar a assinatura do Azure, navegue de volta para a página **Criar Aplicativo**. A nova assinatura aparece na lista suspensa **Assinatura do Azure**.

Para saber mais, consulte [Assinaturas do Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing).

## <a name="region"></a>Região

Escolha a região onde você gostaria de criar o Aplicativo Azure IoT Central. Normalmente, você deve escolher a região mais próxima fisicamente de seus dispositivos para obter um desempenho ideal.

Para saber mais, consulte [Regiões do Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#azure-regions).

É possível visualizar as regiões nas quais o Azure IoT Central está disponível, na página [Produtos disponíveis por região](https://azure.microsoft.com/regions/services/).

> [!Note]
> Após escolher uma região, não será mais possível mover posteriormente o aplicativo para uma região diferente.

## <a name="application-template"></a>Modelo de aplicativo

Você pode escolher um dos modelos de aplicativo disponíveis para o novo aplicativo Azure IoT Central. Um modelo de aplicativo pode conter itens predefinidos, como modelos de dispositivo e dashboards, para ajudá-lo a começar.

| Modelo de aplicativo | DESCRIÇÃO |
| -------------------- | ----------- |
| Aplicativo personalizado   | Cria um aplicativo vazio para você preencher com seus próprios dispositivos e modelos de dispositivos. |
| Exemplo Contoso       | Cria um aplicativo que inclui um modelo de dispositivo para um dispositivo conectado simples. Use esse modelo para começar a explorar o Azure IoT Central. |
| Exemplo Devkits       | Cria um aplicativo com modelos de dispositivos prontos para você conectar um dispositivo MXChip ou Raspberry Pi. Use esse modelo se você for um desenvolvedor de dispositivos que está experimentando o código em um desses dispositivos. |

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como criar um aplicativo Azure IoT Central como um CSP, a próxima etapa sugerida é apresentada:

> [!div class="nextstepaction"]
> [Administrar o aplicativo](howto-administer.md)
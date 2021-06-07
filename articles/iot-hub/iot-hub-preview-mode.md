---
title: Ativar o modo de visualização para o Hub IoT do Azure
description: Saiba como ativar o modo de visualização para o Hub IoT, por que você deseja e alguns avisos
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: jlian
ms.openlocfilehash: 864870c4392b12477c321c86afd9da848120490c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96621698"
---
# <a name="turn-on-preview-mode-for-iot-hub-to-try-select-new-features"></a>Ativar o modo de visualização para o Hub IoT para tentar selecionar novos recursos

<!-- 
- We are working hard to bring you new features
- Some of these features require a brand new iot hub with preview mode on
- some features may not work at all or have unexpected behavior
- "Normal preview features" do NOT require preview mode 
- Support opt-in at creation time only
- Customer cannot opt back out post creation
- If customer wants to evaluate, they must use new hub dedicated for the preview
- Banners, documentations and all materials indicate preview quality: no GA guarantee at all
-->

Novos recursos estão em visualização pública para o Hub IoT, incluindo:

- MQTT 5
- Certificado do servidor ECC
- Negociação de comprimento de fragmento TLS
- Suporte da cadeia de AC X509 para HTTPS/WebSocket

Esses recursos são aprimoramentos no protocolo do Hub IoT e nas camadas de autenticação, para que estejam disponíveis apenas para o **novo** Hub IOT por enquanto. Eles ainda *não* estão disponíveis para os hubs IOT existentes. Para visualizar esses recursos, o Hub IoT deve ser criado com o modo de visualização ativado.

## <a name="turn-on-preview-mode-for-a-new-iot-hub"></a>Ativar o modo de visualização para um novo hub IoT

1. Entre no [portal do Azure](https://portal.azure.com).

1. Na página inicial do Azure, selecione o botão **Criar um recurso** e, em seguida, insira *Hub IoT* no campo **Pesquisar no Marketplace**.

1. Selecione **Hub IoT** nos resultados da pesquisa e, em seguida, selecione **Criar**.

1. Na guia **noções básicas** , preencha os campos [como faria normalmente](iot-hub-create-through-portal.md) , exceto para **região**. Selecione uma destas regiões:
    
    - Centro dos EUA
    - Europa Ocidental
    - Sudeste da Ásia

1. Selecione a guia **Gerenciamento** e expanda a seção **Configurações avançadas** .

1. Ao lado de **modo de visualização**, selecione **ativado**. Examine atentamente o texto de aviso.

    :::image type="content" source="media/iot-hub-preview-mode/turn-on-preview-mode-at-create.png" alt-text="Imagem mostrando onde selecionar a opção modo de visualização ao criar um novo hub IoT":::

1. Selecione **Avançar: revisar + criar** e **criar**.

Depois de criado, um hub IoT no modo de visualização sempre mostra essa faixa, permitindo que você saiba usar este Hub IoT somente para fins de visualização: 

:::image type="content" source="media/iot-hub-preview-mode/banner.png" alt-text="Imagem mostrando a faixa para o modo de visualização Hub IoT":::

## <a name="using-an-iot-hub-in-preview-mode"></a>Usando um hub IoT no modo de visualização

*Não* use um hub IOT no modo de visualização para produção. O modo de visualização destina-se *apenas* a visualizar os recursos selecionados listados na parte superior desta página. Algumas outras limitações do modo de visualização do Hub IoT são

- Alguns recursos existentes do Hub IoT, como filtro IP, link privado, identidade gerenciada, fluxos de dispositivo e failover podem funcionar inesperadamente ou não.
- Um hub IoT no modo de visualização não pode ser alterado ou atualizado para um hub IoT normal.
- Não podemos garantir o [SLA do Hub IOT](https://azure.microsoft.com/support/legal/sla/iot-hub/v1_2/) normal-não use para produção.

> [!TIP]
> O modo de visualização não é necessário para [fluxos de dispositivo](iot-hub-device-streams-overview.md) e [rastreamento distribuído](iot-hub-distributed-tracing.md). Para usar esses recursos mais antigos de visualização, siga a documentação normal. 

## <a name="next-steps"></a>Próximas etapas

- Para visualizar o suporte do MQTT 5, consulte [visão geral do suporte do Hub IOT MQTT 5 (versão prévia)](iot-hub-mqtt-5.md)
- Para visualizar o certificado do servidor ECC, consulte [certificado TLS do servidor de criptografia de curva elíptica (ECC) (versão prévia)](iot-hub-tls-support.md#elliptic-curve-cryptography-ecc-server-tls-certificate-preview)
- Para visualizar a negociação do tamanho do fragmento TLS, consulte [negociação de comprimento máximo do fragmento TLS (versão prévia)](iot-hub-tls-support.md#tls-maximum-fragment-length-negotiation-preview)
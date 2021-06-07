---
title: Perguntas frequentes sobre o defender for IoT
description: Encontre respostas para as perguntas mais frequentes sobre os recursos e o serviço do Azure defender para IoT.
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 0ce8ded3eea344d72679e0f8b805f45b00279b58
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778586"
---
# <a name="azure-defender-for-iot-frequently-asked-questions"></a>Perguntas frequentes sobre o Azure defender para IoT

Este artigo fornece uma lista de perguntas frequentes e respostas sobre o defender para IoT.

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>Qual é a proposta de valor exclusivo do Azure para segurança da IoT?

O defender para IoT permite que as empresas estendam sua exibição de segurança de ciber existente para toda a sua solução de IoT. O Azure fornece uma exibição de ponta a ponta de sua solução de negócios, permitindo que você tome ações e decisões relacionadas a negócios com base na sua postura de segurança corporativa e nos dados coletados. A segurança combinada usando o Azure IoT, o Azure IoT Edge e a Central de Segurança do Azure permitem que você crie a solução desejada com a segurança de que você precisa.

## <a name="our-organization-uses-proprietary-non-standard-industrial-protocols-are-they-supported"></a>Nossa organização usa protocolos industriais não padrão proprietários. Eles são compatíveis? 

O Azure defender para IoT fornece suporte abrangente a protocolos. Além do suporte de protocolo incorporado, você pode proteger os dispositivos IoT e OT que executam protocolos proprietários e personalizados, ou protocolos que se desviam de qualquer padrão. Usando o SDK do ODE (ambiente de desenvolvimento aberto) Horizonte, os desenvolvedores podem criar plugins de dessetors que decodificam o tráfego de rede com base em protocolos definidos. O tráfego é analisado pelos serviços para fornecer monitoramento, alertas e relatórios completos. Use o horizonte para:
- Expanda visibilidade e controle sem a necessidade de atualizar para novas versões.
- Proteja as informações proprietárias desenvolvendo o site como um plug-in externo. 
- Localize texto para alertas, eventos e parâmetros de protocolo.

Essa solução exclusiva para o desenvolvimento de protocolos como plug-ins não requer equipes de desenvolvedor dedicadas ou versões de versão para dar suporte a um novo protocolo. Desenvolvedores, parceiros e clientes podem desenvolver com segurança protocolos e compartilhar informações e conhecimento usando o horizonte. 

## <a name="do-i-have-to-purchase-hardware-appliances-from-microsoft-partners"></a>É necessário comprar dispositivos de hardware de parceiros da Microsoft?
O sensor do Azure defender para IoT é executado em especificações de hardware específicas, conforme descrito no [Guia de especificações de hardware](./how-to-identify-required-appliances.md), os clientes podem adquirir hardware certificado de parceiros da Microsoft ou usar a bom (lista de materiais) fornecida e comprá-lo por conta própria. 

O hardware certificado foi testado em nossos laboratórios para estabilidade do driver, quedas de pacotes e dimensionamento de rede.


## <a name="regulation-does-not-allow-us-to-connect-our-system-to-the-internet-can-we-still-utilize-defender-for-iot"></a>A regulamentação não nos permite conectar nosso sistema à Internet. Ainda podemos utilizar o defender para IoT?

Sim, você pode. A solução local da plataforma Azure defender para IoT é implantada como um dispositivo sensor físico ou virtual que insome passivamente o tráfego de rede (por meio de SPAN, RSPAN ou TAP) para analisar, descobrir e monitorar continuamente as redes de ti, de OT e de IoT. Para empresas maiores, vários sensores podem agregar seus dados a um console de gerenciamento local.

## <a name="where-in-the-network-should-i-connect-monitoring-ports"></a>Em que local na rede devo conectar portas de monitoramento?

O sensor do Azure defender para IoT conecta-se a uma porta SPAN ou toque na rede e imediatamente começa a coletar o tráfego de rede do ICS via monitoramento passivo (sem agente). Ele tem impacto zero sobre as redes, pois não é colocado no caminho de dados e não examina ativamente os dispositivos.

Por exemplo:
- Um único dispositivo (virtual de físico) pode estar na camada de DMZ de chão de fábrica, tendo todo o tráfego de célula de chão de fábrica roteado para essa camada.
- Como alternativa, localize mini sensores pequenos em cada célula de chão de loja com gerenciamento de nuvem ou local que residirá na camada de DMZ do chão de compras. Outro dispositivo (virtual ou físico) pode monitorar o tráfego na camada de DMZ de chão de loja (para SCADA, Historian ou MES).

## <a name="how-does-defender-for-iot-compare-to-the-competition"></a>Como o defender for IoT se compara à competição?

O Azure defender para IoT fornece segurança abrangente em todos os seus dispositivos IoT/OT. Para **organizações de usuários finais**, o Azure defender para IOT oferece segurança sem agente e de camada de rede que é implantada rapidamente, funciona com diversos equipamentos proprietários e sistemas herdados do Windows e interopera com o Azure Sentinel e outras ferramentas SOC. Ele pode ser implantado localmente ou em ambientes conectados ao Azure. Para **criadores de dispositivos IOT**, o Azure defender para IOT oferece agentes leves para inserir a segurança da camada de dispositivo em novas iniciativas de IOT/OT.

## <a name="do-i-have-to-be-an-azure-customer"></a>É necessário ser um cliente do Azure?

Não, para a versão sem agente do Azure defender para IoT, você não precisa ser um cliente do Azure. No entanto, se você quiser enviar alertas para o Azure Sentinel; Provisione sensores de rede e monitore sua integridade na nuvem; e se beneficiar das atualizações automáticas de inteligência de software e ameaças, você precisará conectar o sensor ao Azure por meio do Hub IoT do Azure.

Para a versão baseada em agente do Azure defender para IoT, você deve ser um cliente do Azure.

## <a name="can-i-create-my-own-alerts"></a>Posso criar meus próprios alertas?

Sim, você pode criar alertas personalizados com base em vários parâmetros, incluindo endereço IP/MAC, tipo de protocolo, classe, serviço, função, comando, etc., bem como valores de marcas personalizadas contidas nas cargas.  Consulte [criar alertas personalizados](quickstart-create-custom-alerts.md) para saber mais sobre alertas personalizados e como criá-los.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>O que acontece quando a conexão com a Internet é interrompida?

Os sensores e agentes continuam a executar e armazenar dados enquanto o dispositivo estiver em execução. Os dados são armazenados no cache de mensagens de segurança de acordo com a configuração de tamanho. Quando o dispositivo recupera a conectividade, as mensagens de segurança retomam o envio.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como começar a usar o defender para IoT, consulte os seguintes artigos:

- Leia a [visão geral](overview.md) do defender for IOT
- Verificar os [pré-requisitos do sistema](quickstart-system-prerequisites.md)
- Saiba mais sobre como [começar a usar o defender para IOT](getting-started.md)
- Entender o [defender para alertas de segurança de IOT](concept-security-alerts.md)
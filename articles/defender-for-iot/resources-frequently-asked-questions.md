---
title: Perguntas frequentes sobre o defender for IoT
description: Encontre respostas para as perguntas mais frequentes sobre os recursos e o serviço do Azure defender para IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2020
ms.author: rkarlin
ms.openlocfilehash: 678b50ffb2df2cd16312548109b93f6f1a7f08ff
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247458"
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

Sim, você pode! A solução local da plataforma Azure defender para IoT é implantada como um dispositivo sensor físico ou virtual que insome passivamente o tráfego de rede (por meio de SPAN, RSPAN ou TAP) para analisar, descobrir e monitorar continuamente as redes de ti, de OT e de IoT. Para empresas maiores, vários sensores podem agregar seus dados a um console de gerenciamento local.

## <a name="where-in-the-network-should-i-connect-monitoring-ports"></a>Em que local na rede devo conectar portas de monitoramento?

O sensor do Azure defender para IoT conecta-se a uma porta SPAN ou toque na rede e imediatamente começa a coletar o tráfego de rede do ICS via monitoramento passivo (sem agente). Ele tem impacto zero sobre as redes, pois não é colocado no caminho de dados e não examina ativamente os dispositivos.

Por exemplo:
- Um único dispositivo (virtual de físico) pode estar na camada de DMZ de chão de fábrica, tendo todo o tráfego de célula de chão de fábrica roteado para essa camada.
- Como alternativa, localize mini sensores pequenos em cada célula de chão de loja com gerenciamento de nuvem ou local que residirá na camada de DMZ do chão de compras. Outro dispositivo (virtual ou físico) pode monitorar o tráfego na camada de DMZ de chão de loja (para SCADA, Historian ou MES).

## <a name="how-does-defender-for-iot-compare-to-the-competition"></a>Como o defender for IoT se compara à competição?

Enquanto outras soluções fornecem um conjunto de recursos que permitem que os clientes criem suas próprias soluções, o defender for IoT fornece uma solução de segurança de IoT de ponta a ponta exclusiva que fornece uma visão ampla da segurança de todos os seus recursos do Azure relacionados. O Azure habilita a implantação rápida e a integração total com os módulos gêmeos do Hub IoT para integração fácil com as ferramentas de gerenciamento de dispositivos existentes.


## <a name="do-i-have-to-be-an-azure-iot-customer"></a>É necessário ser um cliente do Azure IoT?

Sim. Para implantações conectadas na nuvem, o Azure defender para IoT depende da conectividade e da infraestrutura do Azure IoT.
## <a name="can-i-create-my-own-alerts"></a>Posso criar meus próprios alertas?

Sim. Você pode definir um alerta personalizado para um conjunto predeterminado de comportamentos, como endereço IP e portas abertas. Confira [Criar alertas personalizados](quickstart-create-custom-alerts.md) para saber mais sobre alertas personalizados e como criá-los.

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>Onde posso ver logs? Posso personalizar logs?

- Exiba alertas e recomendações usando seu workspace conectado do Log Analytics. Configure o tamanho e a duração do armazenamento no workspace.

- Os dados brutos do seu agente de segurança também podem ser armazenados em sua conta do Log Analytics. Considere o tamanho, a duração, os requisitos de armazenamento e os custos associados antes de alterar a configuração dessa opção.



## <a name="what-happens-when-the-internet-connection-stops-working"></a>O que acontece quando a conexão com a Internet é interrompida?

Os sensores e agentes continuam a executar e armazenar dados enquanto o dispositivo estiver em execução. Os dados são armazenados no cache de mensagens de segurança de acordo com a configuração de tamanho. Quando o dispositivo recupera a conectividade, as mensagens de segurança retomam o envio.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como começar a usar o defender para IoT, consulte os seguintes artigos:

- Leia a [visão geral](overview.md) do defender for IOT
- Verificar os [pré-requisitos do sistema](quickstart-system-prerequisites.md)
- Saiba mais sobre como [começar a usar o defender para IOT](getting-started.md)
- Entender o [defender para alertas de segurança de IOT](concept-security-alerts.md)
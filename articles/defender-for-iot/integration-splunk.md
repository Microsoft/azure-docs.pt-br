---
title: Sobre a integração do Splunk
titleSuffix: Azure Defender for IoT
description: Para resolver a falta de visibilidade da segurança e da resiliência de outras redes, o defender for IoT desenvolveu o defender for IoT, o IIoT e o aplicativo de monitoramento de ameaças do ICS para o Splunk, uma integração nativa entre o defender para IoT e o Splunk, que permite uma abordagem unificada para a ti e a segurança.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/4/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: 91d877d644b4b5ca7231f5f81f9163a0fd3cbe25
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98557259"
---
# <a name="defender-for-iot-and-ics-threat-monitoring-application-for-splunk"></a>Aplicativo de monitoramento de ameaças do defender para IoT e ICS para Splunk

O defender para IoT reduz o risco de IIoT, ICS e SCADA com mecanismos de autoaprendizagem patenteados e com reconhecimento de ICS que fornecem informações imediatas sobre dispositivos, vulnerabilidades e ameaças do ICS em menos de uma hora de imagem e sem depender de agentes, regras ou assinaturas, habilidades especializadas ou conhecimento prévio do ambiente.

Para resolver a falta de visibilidade da segurança e da resiliência de outras redes, o defender for IoT desenvolveu o defender for IoT, o IIoT e o aplicativo de monitoramento de ameaças do ICS para o Splunk, uma integração nativa entre o defender para IoT e o Splunk, que permite uma abordagem unificada para a ti e a segurança.

> [!Note]
> Referências a CyberX referem-se ao Azure defender para IoT.

## <a name="about-the-splunk-application"></a>Sobre o aplicativo Splunk

O aplicativo fornece analistas SOC com visibilidade multidimensional nos protocolos e dispositivos IIoT especializados implantados em ambientes industriais, juntamente com a análise comportamental com reconhecimento de ICS para detectar rapidamente o comportamento suspeito ou anormal. O aplicativo também habilita a resposta a incidentes e a ele, de dentro de um SOC corporativo. Essa é uma evolução importante, considerando a convergência contínua de ti e outras para dar suporte a novas iniciativas de IIoT, como máquinas inteligentes e inteligência em tempo real.

O aplicativo Splunk pode ser instalado localmente ou executado em uma nuvem. A integração com o defender para IoT dá suporte a ambas as implantações.

## <a name="about-the-integration"></a>Sobre a integração

A integração do defender for IoT e do Splunk por meio do aplicativo nativo permite que os usuários:

- Reduza o tempo necessário para as organizações de infraestrutura industrial e crítica detectarem, investigarem e agirem em ameaças cibernéticos.

- Obtenha inteligência em tempo real sobre os riscos.

- Correlacione o defender para alertas de IoT com repositórios do Splunk Enterprise Security Threat Intelligence.

- Monitore e responda de um único painel-de-vidro.

[:::image type="content" source="media/integration-splunk/splunk-mainpage-v2.png" alt-text="Página principal da ferramenta Splunk.":::](media/integration-splunk/splunk-mainpage-v2.png#lightbox)

:::image type="content" source="media/integration-splunk/alerts.png" alt-text="A página de alertas no Splunk.":::

O aplicativo permite que os administradores do Splunk analisem os alertas que o defender for IoT envia e monitoram toda a implantação de segurança de OT, incluindo detalhes como:

- Qual dos cinco mecanismos de análise detectou o alerta.

- Qual protocolo gerou o alerta.

- Qual o sensor do defender para IoT gerou o alerta.

- O nível de severidade do alerta.

- A origem e o destino da comunicação.

## <a name="requirements"></a>Requisitos

### <a name="version-requirements"></a>Requisitos da versão

As versões a seguir são os requisitos.

- Defender para IoT versão 2,4 e posterior.

- Splunkbase versão 11 e posterior.

- Splunk Enterprise versão 7,2 e posterior.
  
## <a name="download-the-application"></a>Fazer o download do aplicativo

Baixe o *CYBERX ICS Threat Monitoring for Splunk Application* da [splunkbase](https://splunkbase.splunk.com/app/4313/).

## <a name="splunk-permission-requirements"></a>Requisitos de permissão do Splunk

A seguinte permissão Splunk é necessária:

- Qualquer usuário com permissões de função de usuário *administrador* .

## <a name="send-defender-for-iot-alerts-to-splunk"></a>Enviar para alertas do defender for IoT ao Splunk

Os alertas do defender for IoT fornecem informações sobre uma ampla gama de eventos de segurança, incluindo:

- Desvios da atividade de rede de linha de base aprendida.

- Detecções de malware.

- Detecções com base em alterações operacionais suspeitas.

- Anomalias de rede.

- Desvios de protocolo das especificações de protocolo.

:::image type="content" source="media/integration-splunk/address-scan.png" alt-text="A tela detecções.":::

Você pode configurar o defender para IoT para enviar alertas para o servidor Splunk, onde as informações de alerta são exibidas no painel do Splunk Enterprise.

:::image type="content" source="media/integration-splunk/alerts-and-details.png" alt-text="Exiba todos os alertas e seus detalhes.":::

As informações de alerta a seguir são enviadas para o servidor Splunk.

- A data e a hora do alerta.

- O defender para o mecanismo de IoT que detectou o evento: violação de protocolo, violação de política, malware, anomalia ou mecanismo operacional.

- O título do alerta.

- A mensagem de alerta.

- A severidade do alerta: aviso, menor, importante ou crítico.

- O nome do dispositivo de origem.

- O endereço IP do dispositivo de origem.

- O nome do dispositivo de destino.

- O endereço IP do dispositivo de destino.

- O endereço IP da plataforma do defender para IoT (host).

- O nome do dispositivo do defender para a plataforma IoT (tipo de origem).

A saída de exemplo é mostrada abaixo:

| Hora | Evento |
|--|--|
| 7/23/15<br />9:28:31.000 PM | **Alerta da plataforma do defender para IOT**: um dispositivo foi interrompido por um comando PLC<br /><br />**Tipo**: violação operacional <br /><br />**Gravidade**: principal <br /><br />**Nome da origem**: my_device1 <br /><br />**IP de origem**: 192.168.110.131 <br /><br />**Nome de destino**: my_device2<br /><br /> **IP de destino**: 10.140.33.238 <br /><br />**Mensagem**: um dispositivo de rede foi interrompido usando um comando Stop PLC. Este dispositivo não funcionará até que um comando Iniciar seja enviado. o 192.168.110.131 foi interrompido por 10.140.33.238 (um dispositivo Siemens S7), usando um comando PLC stop.<br /><br />**Host**: 192.168.90.43 <br /><br />**SourceType**: Sensor_Agent |

## <a name="define-alert-forwarding-rules"></a>Definir regras de encaminhamento de alerta

Use o defender para *regras de encaminhamento* de IOT para enviar informações de alerta para servidores Splunk.

As opções estão disponíveis para personalizar as regras de alerta com base no:

- Protocolos específicos detectados.

- Severidade do evento.

- Defender para o mecanismo de IoT que detecta eventos.

Para criar uma regra de encaminhamento:

1. No painel esquerdo do sensor ou do console de gerenciamento local, selecione **encaminhamento.**

    :::image type="content" source="media/integration-splunk/forwarding.png" alt-text="Selecione o botão azul criar alerta de encaminhamento.":::

1. Selecione **criar regras de encaminhamento**. Na janela **criar regra de encaminhamento** , defina os parâmetros da regra.

    :::image type="content" source="media/integration-splunk/forwarding-rule.png" alt-text="Crie as regras para sua regra de encaminhamento.":::

    | Parâmetro | Descrição |
    |--|--|
    | **Nome** | O nome da regra de encaminhamento. |
    | **Selecionar severidade** | O incidente de nível de segurança mínimo a ser encaminhado. Por exemplo, se a secundária estiver selecionada, os alertas secundários e qualquer alerta acima desse nível de severidade serão encaminhados. |
    | **Protocolos** | Por padrão, todos os protocolos são selecionados. Para selecionar um protocolo específico, selecione **específico** e selecione o protocolo para o qual essa regra é aplicada. |
    | **Motores** | Por padrão, todos os mecanismos de segurança estão envolvidos. Para selecionar um mecanismo de segurança específico para o qual essa regra é aplicada, selecione **específico** e selecione o mecanismo. |
    | **Notificações do sistema** | Encaminhar o status do sensor online/offline. Essa opção só estará disponível se você tiver feito logon no gerente central. |                                            |

1. Para instruir o defender para IoT a enviar informações de ativo ao Splunk, selecione **ação** e, em seguida, selecione **Enviar para o servidor Splunk**.

1. Insira os seguintes parâmetros de Splunk.

    :::image type="content" source="media/integration-splunk/parameters.png" alt-text="Os parâmetros de Splunk que você deve inserir nesta tela.":::

    | Parâmetro | Descrição |
    |--|--|
    | **Host** | Endereço do servidor Splunk |
    | **Porta** | 8089 |
    | **Nome de usuário** | Nome de usuário do Splunk Server |
    | **Senha** | Senha do servidor Splunk |

1. Selecione **Enviar**

## <a name="next-steps"></a>Próximas etapas

Saiba como [encaminhar informações de alerta](how-to-forward-alert-information-to-partners.md).

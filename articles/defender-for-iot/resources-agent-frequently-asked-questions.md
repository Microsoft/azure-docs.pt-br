---
title: Perguntas frequentes sobre o agente do Azure defender para IoT
description: Encontre respostas para as perguntas mais frequentes sobre o Azure defender para agente de IoT.
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 4bd7d3c9b76dfb37e53cc51e5e16b660545cb7f0
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778654"
---
# <a name="azure-defender-for-iot-agent-frequently-asked-questions"></a>Perguntas frequentes sobre o agente do Azure defender para IoT

Este artigo fornece uma lista de perguntas frequentes e respostas sobre o agente do defender para IoT.

## <a name="do-i-have-to-install-an-embedded-security-agent"></a>É necessário instalar um agente de segurança inserido?

A instalação do agente em seus dispositivos IoT não é obrigatória para habilitar o defender para IoT. Você pode escolher entre as seguintes três opções, cada uma das quais fornecerá diferentes níveis de funcionalidades de monitoramento e gerenciamento de segurança:

- Implantação passiva, não invasiva (sem agente) usando sensores de NTA (análise de tráfego de rede) para monitorar e fornecer visibilidade profunda do risco de IoT/atividade com impacto de desempenho zero na rede e nos dispositivos
- Instale o agente de segurança do defender para IoT Embedded com ou sem modificações. Essa opção fornece o nível mais alto de informações de segurança aprimoradas sobre o comportamento do dispositivo e o acesso a ele.

- Crie seu próprio agente e implemente o esquema de mensagem do defender for IoT Security. Essa opção habilita o uso do defender para ferramentas de análise de IoT sobre o agente de segurança do dispositivo.

- Nenhuma instalação do agente de segurança em seus dispositivos IoT. Essa opção habilita o monitoramento de comunicação do Hub IoT, com funcionalidades de monitoramento e gerenciamento de segurança reduzidas.

## <a name="what-does-the-defender-for-iot-agent-do"></a>O que o agente do defender para IoT faz?

O agente do defender para IoT fornece cobertura de ameaça no nível do dispositivo para configuração, comportamento e acesso do dispositivo (examinando a configuração), processa & conectividade. O agente de segurança do defender para IoT não verifica dados ou atividades relacionados ao negócio.

O agente de segurança do defender para IoT é de software livre e está disponível no GitHub nas versões do Windows e do Linux de 32 bits e 64 bits: https://github.com/Azure/Azure-IoT-Security .

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>Quais são as dependências e os pré-requisitos do agente?

O defender para IoT dá suporte a uma ampla variedade de plataformas. Confira [Plataformas de dispositivo compatíveis](how-to-deploy-agent.md) para verificar a compatibilidade com seus dispositivos específicos.

## <a name="which-data-is-collected-by-the-agent"></a>Quais dados são coletados pelo agente?

Conectividade, acesso, configuração de firewall, lista de processos e linha de base do sistema operacional são coletados pelo agente.

## <a name="how-much-data-will-the-agent-generate"></a>Qual o volume de dados que o agente vai gerar?

A geração de dados pelo agente é orientada pelo dispositivo, aplicativo, tipo de conectividade e configuração do agente do cliente. Devido à alta variabilidade entre dispositivos e soluções de IoT, recomendamos primeiro implantar o agente em um laboratório ou configuração de teste para observar, aprender e definir a configuração específica que atende às suas necessidades, medindo simultaneamente a quantidade de dados gerados. Depois de iniciar o serviço, o agente do defender para IoT fornece recomendações operacionais para otimizar a taxa de transferência do agente para ajudá-lo com o processo de configuração e personalização.

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>As mensagens do agente usam a cota do Hub IoT?

Sim. Os dados transmitidos pelo agente são contabilizados em sua cota do Hub IoT.

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>Próximos passos Instalei um agente e não vejo atividades nem logs...

1. Verifique se o [tipo do agente se adequa à plataforma de sistema operacional designada do seu dispositivo](how-to-deploy-agent.md)

1. Confirme se o [agente está em execução no dispositivo](how-to-agent-configuration.md).

1. Verifique se o [serviço foi habilitado com êxito](quickstart-onboard-iot-hub.md) para **Segurança** no Hub IoT.

1. Verifique se o dispositivo está [configurado no Hub IOT com o módulo defender para IOT](quickstart-create-security-twin.md).

Se as atividades ou os logs ainda não estiverem disponíveis, entre em contato com seu parceiro do defender para IoT para obter ajuda adicional.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>O que acontece quando a conexão com a Internet é interrompida?

Os sensores e agentes continuam a executar e armazenar dados enquanto o dispositivo estiver em execução. Os dados são armazenados no cache de mensagens de segurança de acordo com a configuração de tamanho. Quando o dispositivo recupera a conectividade, as mensagens de segurança retomam o envio.

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>O agente pode afetar o desempenho do dispositivo ou de outro software instalado?

O agente consome recursos do computador como qualquer outro aplicativo/processo e não deve interromper a atividade normal do dispositivo. O consumo de recursos no dispositivo em que o agente é executado está relacionado à instalação e configuração dele. Antes de tentar implantar o agente em um ambiente de produção, é recomendável testar, em um ambiente contido, a configuração desse agente e a interoperabilidade dele com seus outros aplicativos e funcionalidades de IoT.

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>Estou realizando alguma manutenção no dispositivo. Posso desativar o agente?

O agente não pode ser desativado.

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>Há alguma forma de testar se o agente está funcionando corretamente?

Se o agente parar de se comunicar ou falhar em enviar mensagens de segurança, um alerta com a mensagem **O dispositivo está silencioso** será gerado.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como começar a usar o defender para IoT, consulte os seguintes artigos:

- Leia a [visão geral](overview.md) do defender for IOT
- Verificar os [pré-requisitos do sistema](quickstart-system-prerequisites.md)
- Saiba mais sobre como [começar a usar o defender para IOT](getting-started.md)
- Entender o [defender para alertas de segurança de IOT](concept-security-alerts.md)

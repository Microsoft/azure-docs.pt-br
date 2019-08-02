---
title: Perguntas frequentes sobre a Central de segurança do Azure para a versão prévia do IoT | Microsoft Docs
description: Encontre respostas para as perguntas frequentes sobre a Central de segurança do Azure para o serviço e os recursos de IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 97fda6c2-1ecb-491f-b48d-41788bd7e0d3
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 4e04e21d55722b8cb90b277b34b5bffab343c575
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616586"
---
# <a name="azure-security-center-for-iot-frequently-asked-questions"></a>Perguntas frequentes sobre a Central de segurança do Azure para IoT  

> [!IMPORTANT]
> A Central de Segurança do Azure para IoT está em versão prévia pública no momento.
> Esta versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo fornece uma lista de perguntas frequentes e respostas sobre o Azure Security Center (ASC) para IoT. 

## <a name="does-azure-provide-support-for-iot-security"></a>O Azure oferece suporte para segurança de IoT?

O Azure fornece uma visão integrada para monitorar e gerenciar a segurança de IoT como parte de sua solução geral de segurança por meio da Central de segurança do Azure. Se você for um desenvolvedor de aplicativos, você pode usar o modo de exibição do IoT Hub para gerenciar a segurança do aplicativo IoT.

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>O que é a proposição de valor exclusivo do Azure para segurança de IoT?

ASC para IoT permite que as empresas estendam sua exibição de segurança cibernética existente para toda a solução IoT. O Azure fornece uma exibição de ponta a ponta da sua solução de negócios, permitindo que você execute ações relacionados aos negócios e as decisões com base em sua postura de segurança da empresa e os dados coletados. Combinadas usando o Azure IoT, IoT Edge do Azure, Azure Sphere, Azure Central de segurança e a Central de segurança do Azure permitem que você criar a solução desejada com a segurança que você precisa.

## <a name="who-is-asc-for-iot-made-for"></a>Quem é ASC para IoT é feito? 

ASC para IoT é integrado dentro da segurança de Hub IoT do Azure e fornece operações de segurança de gerenciamento para a solução de negócios do dia a dia. ASC para IoT também é integrado ao recursos da Central de segurança do Azure e fornecer uma exibição integrada para monitorar e gerenciar a segurança de IoT como parte de sua solução de segurança geral.

## <a name="how-does-asc-for-iot-compare-to-the-competition"></a>Como ASC para IoT se compara à concorrência?

Enquanto outras soluções fornecem um conjunto de recursos que permitem que os clientes criem suas próprias soluções, ASC para IoT fornece uma solução de segurança de IoT ponta a ponta exclusiva que fornece uma exibição ampla entre a segurança de todos os seus recursos relacionados do Azure. O Azure permite a rápida implantação e integração completa com Gêmeos de módulo de IoT Hub para fácil integração com ferramentas de gerenciamento de dispositivo existentes.

## <a name="do-i-have-to-be-an-azure-security-center-asc-customer"></a>É necessário ser um cliente de Central de segurança do Azure (ASC)?

Não, mas é recomendável. Sem ASC, ASC para IoT recebe dados limitados recursos conectados e fornece uma análise limitada de sua superfície de ataque potencial, ameaças e ataques potenciais. 

## <a name="do-i-have-to-be-an-azure-iot-customer"></a>É necessário ser um cliente de IoT do Azure?

Sim. ASC para IoT se baseia em infraestrutura e conectividade de IoT do Azure.

## <a name="do-i-have-to-install-an-agent"></a>É necessário instalar um agente?

Instalação do agente em seus dispositivos IoT não é obrigatória para habilitar o Microsoft ASC para IoT. Você pode escolher entre três opções a seguir, obtenção de recursos de monitoramento e gerenciamento de acordo com sua seleção de diferentes níveis de segurança:

1. Instale o ASC para agente de segurança de IoT com ou sem modificações. Essa opção fornece o nível mais alto de segurança aprimorada percepções sobre acesso e o comportamento do dispositivo.

2. Criar seu próprio agente e implementar Microsoft ASC para esquema de mensagem de segurança de IoT. Essa opção habilita o uso do Microsoft ASC para ferramentas de análise de IoT na parte superior do seu agente de segurança de dispositivo.

3. Nenhuma instalação do agente de segurança em seus dispositivos IoT. Essa opção permite que o IoT Hub comunicação monitoramento, com recursos de monitoramento e gerenciamento de segurança reduzida. 

## <a name="what-does-the-asc-for-iot-agent-do"></a>O que faz o ASC para agente do IoT?

ASC para agente do IoT fornece cobertura de ameaças no nível do dispositivo para a configuração do dispositivo, o comportamento e acesso (por meio da verificação da configuração), processo e conectividade. O ASC para agente de segurança de IoT não verifica os dados relacionados aos negócios ou atividade.

## <a name="where-i-can-get-the-asc-for-iot-security-agent"></a>Onde posso obter o ASC para agente de segurança de IoT?

O ASC para agente de segurança de IoT é um software livre e está disponível no GitHub de 32 bits e as versões de Windows e Linux de 64 bits: https://github.com/Azure/Azure-Security-IoT-Preview

## <a name="where-does-the-asc-for-iot-agent-get-installed"></a>Onde o ASC para agente do IoT obter instalado? 

Informações detalhadas de implantação de instalação e o agente podem ser encontradas no GitHub: https://github.com/Azure/Azure-Security-IoT-Preview

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>Quais são as dependências e pré-requisitos do agente?

ASC para IoT dá suporte a uma ampla variedade de plataformas. Ver [plataformas de dispositivos com suporte](how-to-deploy-agent.md) para confirmar o suporte para seus dispositivos específicos. 

## <a name="which-data-is-collected-by-the-agent"></a>Quais dados são coletados pelo agente?

Conectividade, acesso, configuração do firewall, lista de processos e linha de base do sistema operacional são coletadas pelo agente.

## <a name="how-much-data-will-the-agent-generate"></a>A quantidade de dados irá gerar o agente?

Geração de dados do agente é orientada pelo dispositivo, aplicativo, tipo de conectividade e configuração do agente cliente. Devido a alta variação entre os dispositivos e soluções de IoT, é recomendável usar primeiro implantar o agente em um laboratório ou configuração de teste para observar, aprenda e definir a configuração específica que atenda às suas necessidades, ao medir a quantidade de dados gerados. Depois de iniciar o serviço, o ASC para agente do IoT fornece recomendações operacionais para otimizar a taxa de transferência do agente para ajudá-lo com o processo de configuração e personalização.

## <a name="how-can-i-control-my-billing"></a>Como controlar a minha cobrança?

ASC para IoT fornece agente configurável verificações, buffers de dados e a capacidade de criar alertas personalizados que aumentar ou reduzem a quantidade de dados gerados pelo agente.

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>Mensagens do agente que usa a cota do IoT Hub?

Sim. Os dados do agente transmitida são contados em sua cota de IoT Hub. 

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>Próximos passos Eu tiver instalado um agente e não vir quaisquer atividades ou logs

1. Verifique o [tipo de agente se ajusta a plataforma do sistema operacional designada do dispositivo](how-to-deploy-agent.md)

1. Confirme se o [agent está em execução no dispositivo](how-to-agent-configuration.md).

2. Verifique as [serviço foi habilitado com êxito](quickstart-onboard-iot-hub.md) à **segurança** em seu IoT Hub. 

3. Verifique se o dispositivo estiver [configurado no IoT Hub com o ASC para o módulo do IoT](quickstart-create-security-twin.md).  

Se as atividades ou os logs são ainda não estiver disponíveis, entre em contato com o ASC para o parceiro de IoT para obter ajuda adicional.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>O que acontece quando a conexão de internet para de funcionar?

O agente continuará a executar e armazenar dados, desde que o dispositivo está em execução. Dados são armazenados no cache de mensagem de segurança de acordo com a configuração de tamanho. Quando o dispositivo recupera o conectividade, mensagens de segurança voltar a enviar. 

## <a name="if-the-device-is-restarted-will-the-security-agent-self-recover"></a>Se o dispositivo for reiniciado, será o agente de segurança se recupere automaticamente?

O agente de segurança foi projetado para executar novamente automaticamente com cada reinicialização do dispositivo.

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>O agente pode afetar o desempenho do dispositivo ou outros softwares instalados?

O agente consome recursos da máquina, como qualquer outro aplicativo/processo e não deve interromper a atividade normal do dispositivo. Consumo de recursos no dispositivo que executa o agente está acoplado com sua instalação e configuração. É recomendável testar sua configuração de agente em um ambiente independente, além de interoperabilidade com outros aplicativos de IoT e funcionalidade, antes de tentar implantar em um ambiente de produção.

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>Estou fazendo algum manutenção no dispositivo. Posso desativar o agente?

O agente não pode ser desativado.

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>Há uma maneira de testar se o agente está funcionando corretamente? 

Se o agente parar de se comunicar ou Falha ao enviar mensagens de segurança, uma **dispositivo é silencioso** alerta é gerado.

## <a name="can-i-create-my-own-alerts"></a>Posso criar meus próprio alertas?

Sim. Você pode definir um alerta personalizado em um conjunto predeterminado de comportamentos, como o endereço IP e portas abertas. Ver [criar alertas personalizados](quickstart-create-custom-alerts.md) para saber mais sobre alertas personalizados e como torná-los. 

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>Onde posso ver os logs? Para personalizar os logs?

- Exibir alertas e recomendações usando o seu espaço de trabalho do Log Analytics conectado. Configure o tamanho de armazenamento e a duração do espaço de trabalho.

- Dados brutos do seu agente de segurança também podem ser armazenados em sua conta do Log Analytics. Considere o tamanho, duração, requisitos de armazenamento e os custos associados antes de alterar a configuração dessa opção. 

## <a name="why-should-i-add-asc-for-iot-to-the-module-identity-what-is-it-used-for"></a>Por que devo adicionar ASC para IoT à identidade do módulo? Para que é utilizado?

O ASC para o módulo do IoT é usada para gerenciamento e configuração do agente.


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como começar com o ASC para IoT, consulte os seguintes artigos:


- Leia o ASC para IoT [visão geral](overview.md)
- Verifique se o [pré-requisitos de serviço](service-prerequisites.md)
- Saiba mais sobre como [começar](getting-started.md)
- Entender [ASC para alertas de segurança de IoT](concept-security-alerts.md)


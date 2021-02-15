---
title: Sobre a integração do Forescout
titleSuffix: Azure Defender for IoT
description: A integração do Azure defender para IoT com a plataforma Forescout fornece um novo nível de visibilidade centralizada, monitoramento e controle para a IoT e para o cenário.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/17/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: faa53c770d0d6caac471e770c80b4dfd5c5ff603
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98557269"
---
# <a name="about-the-forescout-integration"></a>Sobre a integração do Forescout

O Azure defender para IoT fornece uma plataforma de segurança cibernética de ICS e IoT criada por especialistas em equipe azul com um registro de acompanhamento da defesa de infraestrutura nacional crítica. O defender for IoT é a única plataforma com análise de ameaças com reconhecimento de ICS e aprendizado de máquina patenteado. O defender para IoT fornece:

- Informações imediatas sobre o ICS o cenário do dispositivo com uma ampla gama de detalhes sobre os atributos.
- Conhecimento incorporado profundo com reconhecimento de ICS de OT protocolos, dispositivos, aplicativos e seus comportamentos.
- Informações imediatas sobre vulnerabilidades e ameaças conhecidas e de dia zero.
- Uma tecnologia de modelagem de ameaças do ICS automatizada para prever os caminhos mais prováveis de ataques de ICS direcionados por meio de análises proprietárias.

A integração do defender para IoT com a plataforma Forescout fornece um novo nível de visibilidade centralizada, monitoramento e controle para a IoT e para o cenário.

Essas plataformas de ponte permitem a visibilidade e o gerenciamento automatizados de dispositivos para dispositivos ICS e fluxos de trabalho em silos anteriormente inacessíveis.

A integração fornece analistas SOC com visibilidade de vários níveis para os protocolos implantados em ambientes industriais. Os detalhes estão disponíveis para itens como firmware, tipos de dispositivo, sistemas operacionais e pontuações de análise de risco com base nas tecnologias proprietárias do Azure defender para IoT.

> [!Note]
> Referências a CyberX referem-se ao Azure defender para IoT.
## <a name="devices"></a>Dispositivos

### <a name="device-visibility-and-management"></a>Visibilidade e gerenciamento de dispositivos

O inventário do dispositivo é aprimorado com atributos críticos detectados pela plataforma defender para IoT. Isso irá garantir que você:

- Obter visibilidade abrangente e contínua no cenário de dispositivo de OT de um único painel de vidro.
- Obtenha inteligência em tempo real sobre os riscos.

:::image type="content" source="media/integration-forescout/forescout-device-inventory.png" alt-text="Inventário de dispositivo":::

:::image type="content" source="media/integration-forescout/forescout-device-details.png" alt-text="Detalhes do dispositivo":::

### <a name="device-control"></a>Controle de dispositivo

A integração do Forescout ajuda a reduzir o tempo necessário para que as organizações de infraestrutura industrial e crítica detectem, investiguem e atuem em ameaças cibernéticos.

- Use o Azure defender para IoT no Device Intelligence para fechar o ciclo de segurança disparando ações de política de Forescout. Por exemplo, você pode enviar automaticamente o email de alerta para os administradores do SOC quando protocolos específicos forem detectados ou quando os detalhes do firmware forem alterados.

- Correlacione o defender para informações de IoT com outros módulos *Forescout eyeExtended* que supervisionam o monitoramento, o gerenciamento de incidentes e o controle de dispositivo.

## <a name="system-requirements"></a>Requisitos de sistema

- Azure defender para IoT versão 2,4 ou superior
- Forescout versão 8,0 ou superior
- Uma licença para o módulo *Forescout eyeExtend* para a plataforma Azure defender para IOT.

### <a name="getting-more-forescout-information"></a>Obter mais informações de Forescout

Para obter mais informações sobre a plataforma Forescout, consulte a [central de recursos do Forescout](https://www.forescout.com/company/resources/#resource_filter_group).

## <a name="system-setup"></a>Configuração do sistema

Este artigo descreve como configurar a comunicação entre a plataforma defender para IoT e a plataforma Forescout.

### <a name="set-up-the-defender-for-iot-platform"></a>Configurar a plataforma defender para IoT

Para garantir a comunicação do defender para IoT com o Forescout, gere um token de acesso no defender para IoT.

Os tokens de acesso permitem que os sistemas externos acessem dados descobertos pelo defender para IoT e executem ações com esses dados usando a API REST externa, em conexões SSL. Você pode gerar tokens de acesso para acessar a API REST do Azure defender para IoT.

Para gerar um token:

1. Entre no sensor do defender for IoT que será consultado por Forescout.

1. Selecione **configurações do sistema** e, em seguida, selecione **tokens de acesso** na seção **geral** . A caixa de diálogo **tokens de acesso** é aberta.
   :::image type="content" source="media/integration-forescout/generate-access-tokens-screen.png" alt-text="Tokens de acesso":::
1. Selecione **gerar novo token** na caixa de diálogo **tokens de acesso** .
1. Insira uma descrição de token na caixa de diálogo **novo token de acesso** .
   :::image type="content" source="media/integration-forescout/new-forescout-token.png" alt-text="Novo token de acesso":::
1. Selecione **Avançar**. O token é exibido na caixa de diálogo. :::image type="content" source="media/integration-forescout/forescout-access-token-display-screen.png" alt-text="Exibir token":::
   > [!NOTE]
   > *Registre o token em um local seguro. Você precisará dela ao configurar a plataforma Forescout*.
1. Selecione **Concluir**.

   :::image type="content" source="media/integration-forescout/forescout-access-token-added-successfully.png" alt-text="Concluir adição de token":::

### <a name="set-up-the-forescout-platform"></a>Configurar a plataforma Forescout

Você pode configurar a plataforma Forescout para se comunicar com um sensor do defender para IoT.

Para configurar:

1. Instale *o módulo Forescout eyeExtend para CyberX* na plataforma Forescout.

1. Entre no console do cobalança e selecione **Opções** no menu **ferramentas** . A caixa de diálogo **Opções** é aberta.

1. Navegue até e selecione a pasta **módulos** .

1. No painel **módulos** , selecione **plataforma CyberX**. O painel da plataforma defender para IoT é aberto.

   :::image type="content" source="media/integration-forescout/settings-for-module.png" alt-text="Configurações do módulo do Azure defender para IoT":::

   Insira as seguintes informações:

   - **Endereço do servidor** – Insira o endereço IP do sensor do defender para IOT que será consultado pelo dispositivo Forescout.
   - **Token de acesso** – Insira o token de acesso gerado para o sensor do defender for IOT que se conectará ao dispositivo Forescout. Para gerar um token, consulte [Configurar a plataforma defender para IOT](#set-up-the-defender-for-iot-platform).

1. Escolha **Aplicar**.

Se você quiser que a plataforma Forescout se comunique com outro sensor:

1. Crie um novo token de acesso no sensor defender for IoT relevante.

1. Na caixa de diálogo **Forescout modules**  >  **CyberX Platform** :

   - Exclua as informações exibidas.
   
   - Insira o novo endereço IP do sensor e as novas informações de token de acesso.

### <a name="verify-communication"></a>Verificar comunicação

Depois de configurar o defender para IoT e o Forescout, abra a caixa de diálogo tokens de acesso do sensor no defender para IoT.

Se **N/A** for exibido no campo **usado** para esse token, a conexão entre o sensor e o dispositivo Forescout não estará funcionando.

**Usado** indica a última vez em que uma chamada externa com esse token foi recebida.

:::image type="content" source="media/integration-forescout/forescout-access-token-added-successfully.png" alt-text="Verifica se o token foi recebido":::

## <a name="view-defender-for-iot-detections-in-forescout"></a>Exibir as detecções do defender for IoT no Forescout

Para exibir os atributos de um dispositivo:

1. Entre na plataforma Forescout e, em seguida, navegue até o **inventário de ativos**.

1. Navegue até a **plataforma CyberX**. Os seguintes atributos de dispositivo são exibidos para os dispositivos de OT detectados pelo defender para IoT.

   | Item | Descrição |
   |--|--|
   | Autorizado pelo Azure defender para IoT | Um dispositivo detectado em sua rede pelo defender para IoT durante o período de aprendizado de rede. |
   | Firmware | Os detalhes do firmware do dispositivo. Por exemplo, modelo e detalhes da versão. |
   | Name | O nome do dispositivo. |
   | Sistema operacional | O sistema operacional do dispositivo. |
   | Tipo | O tipo de dispositivo. Por exemplo, uma PLC, Historian ou estação de engenharia. |
   | Fornecedor | O fornecedor do dispositivo. Por exemplo, a automação do Rockwell. |
   | Nível de risco | O nível de risco calculado pelo defender para IoT. |
   | Protocolos | Os protocolos detectados no tráfego gerado pelo dispositivo. |

:::image type="content" source="media/integration-forescout/device-firmware-attributes-in-forescout.png" alt-text="Exiba os atributos de firmware.":::

:::image type="content" source="media/integration-forescout/vendor-attributes-in-forescout.png" alt-text="Exiba os atributos de fornecedores.":::

### <a name="viewing-more-details"></a>Exibindo mais detalhes

Exibir informações adicionais do dispositivo para dispositivos direcionados pelo defender para IoT. Por exemplo, informações de conformidade e de política do Forescout.

Para fazer isso, clique com o botão direito do mouse em um dispositivo na seção **hosts de inventário de dispositivos** . A caixa de diálogo detalhes do host é aberta com informações adicionais.

:::image type="content" source="media/integration-forescout/details-dialog-box-in-forescout.png" alt-text="Detalhes do host":::

## <a name="create-azure-defender-for-iot-policies-in-forescout"></a>Criar políticas do Azure defender para IoT no Forescout

As políticas de Forescout podem ser usadas para automatizar o controle e o gerenciamento de dispositivos detectados pelo defender para IoT. Por exemplo,

- Envie por email os administradores do SOC automaticamente quando versões específicas do firmware forem detectadas.

- Adicione um defender específico para dispositivos de IoT detectados a um grupo Forescout para tratamento adicional em fluxos de trabalho de incidentes e segurança, por exemplo, com outras integrações do SIEM.

Crie uma política personalizada do Forescout com o defender para IoT usando propriedades de condição.

Para acessar o defender para propriedades de IoT:

1. Navegue até a **árvore de propriedades** na caixa de diálogo **condições de política** .

1. Expanda a pasta **CyberX Platform** na **árvore de propriedades**. As propriedades do defender para IoT a seguir estão disponíveis.

:::image type="content" source="media/integration-forescout/forescout-property-tree.png" alt-text="Propriedades":::

## <a name="next-steps"></a>Próximas etapas

Saiba como [encaminhar informações de alerta](how-to-forward-alert-information-to-partners.md).

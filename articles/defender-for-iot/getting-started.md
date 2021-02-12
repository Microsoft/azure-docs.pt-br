---
title: Introdução
description: Introdução ao entendimento do fluxo de trabalho básico para implantação do defender para IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/26/2020
ms.author: shhazam
ms.openlocfilehash: 82fcf7f129d5caba471d6d72da83a317b26c638c
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98807885"
---
# <a name="get-started-with-defender-for-iot"></a>Introdução ao defender para IoT

Este artigo fornece uma visão geral das etapas que você seguirá para configurar o Azure defender para IoT. O processo requer que você:

- Registre sua assinatura e sensores no portal do Azure defender para IoT.
- Instale o sensor e o software do console de gerenciamento local.
- Execute a ativação inicial do sensor e do console de gerenciamento.

## <a name="permission-requirements"></a>Requisitos de permissão

Algumas das etapas de configuração exigem permissões de usuário específicas.

As permissões de usuário administrativo são necessárias para ativar o sensor e o console de gerenciamento, carregar certificados SSL/TLS e gerar novas senhas.

A tabela a seguir descreve as permissões de acesso do usuário para as ferramentas do portal do Azure defender para IoT:

| Permissão | Leitor de segurança | Administrador de segurança | Colaborador da assinatura | Proprietário da assinatura |
|--|--|--|--|--|
| Exibir todas as telas e dados do defender for IoT | ✓ | ✓ | ✓ | ✓ |
| Integrar um sensor  |  |  ✓ | ✓ | ✓ |
| Atualizar preços  |  |  ✓ | ✓ | ✓ |
| Recuperar senha  | ✓  |  ✓ | ✓ | ✓ |

## <a name="1-identify-the-solution-infrastructure"></a>1. identificar a infraestrutura da solução

**Esclareça suas necessidades de configuração de rede**

Pesquise sua arquitetura de rede, largura de banda monitorada e outros detalhes da rede. Para obter mais informações, consulte [sobre a configuração de rede do Azure defender para IOT](how-to-set-up-your-network.md).

**Esclareça quais sensores e dispositivos de console de gerenciamento são necessários para lidar com a carga de rede**

O Azure defender para IoT dá suporte a implantações físicas e virtuais. Para as implantações físicas, você pode comprar vários dispositivos certificados. Para obter mais informações, consulte [identificar dispositivos necessários](how-to-identify-required-appliances.md).

Recomendamos que você calcule o número aproximado de dispositivos que serão monitorados. Posteriormente, ao registrar sua assinatura do Azure no portal, você será solicitado a inserir esse número. Os números podem ser adicionados em intervalos de 1.000 segundos. Os números de dispositivos monitorados são chamados de *dispositivos confirmados*.

## <a name="2-register-with-azure-defender-for-iot"></a>2. Registre-se com o Azure defender para IoT

O registro inclui:

- Integração de suas assinaturas do Azure ao defender para IoT.
- Definindo os dispositivos confirmados.
- Baixando um arquivo de ativação para o console de gerenciamento local.

Para se inscrever:

1. Vá para o portal do Azure defender para IoT.
1. Selecione **assinatura integrada**.
1. Na página de **preços** , selecione uma assinatura ou crie uma nova e adicione o número de dispositivos confirmados.
1. Selecione **baixar a guia Console de gerenciamento local** e salve o arquivo de ativação baixado. Esse arquivo contém os dispositivos confirmados agregados que você definiu. O arquivo será carregado no console de gerenciamento após a entrada inicial.

## <a name="3-install-and-set-up-the-on-premises-management-console"></a>3. instalar e configurar o console de gerenciamento local

Depois de adquirir seu dispositivo de console de gerenciamento local:

- Baixe o pacote ISO no portal do Azure defender para IoT.
- Instale o software.
- Ative e realize a instalação inicial do console de gerenciamento.

Para instalar e configurar:

1. Selecione **introdução** no portal do defender para IOT.
1. Selecione a guia **console de gerenciamento local** .
1. Escolha uma versão e selecione **baixar**.
1. Instale o software do console de gerenciamento local. Para obter mais informações, consulte [defender for IOT Installation](how-to-install-software.md).
1. Ative e configure o console de gerenciamento do. Para obter mais informações, consulte [ativar e configurar seu console de gerenciamento local](how-to-activate-and-set-up-your-on-premises-management-console.md).

## <a name="4-onboard-a-sensor"></a>4. carregar um sensor

Carregar um sensor registrando-o no Azure defender para IoT e baixando um arquivo de ativação do sensor:

1. Defina um nome de sensor e associe-o a uma assinatura.
1. Escolha um modo de gerenciamento de sensor:

   - **Sensores conectados à nuvem**: informações que os sensores detectam são exibidas no console do sensor. Além disso, as informações de alerta são entregues por meio de um hub IoT e podem ser compartilhadas com outros serviços do Azure, como o Azure Sentinel.

   - **Sensores gerenciados localmente**: informações que os sensores detectam são exibidas no console do sensor. Se você estiver trabalhando em uma rede de ar-gapped e quiser uma exibição unificada de todas as informações detectadas por vários sensores gerenciados localmente, trabalhe com o console de gerenciamento local. 

1. Baixar um arquivo de ativação do sensor.

Para obter mais informações, consulte [integração e gerenciamento de sensores no portal do defender para IOT](how-to-manage-sensors-on-the-cloud.md).

## <a name="5-install-and-set-up-the-sensor"></a>5. instalar e configurar o sensor

Baixe o pacote ISO no portal do Azure defender para IoT, instale o software e configure o sensor.

1. Selecione **introdução** no portal do defender para IOT.
1. Selecione **Configurar sensor**.
1. Escolha uma versão e selecione **baixar**.
1. Instale o software do sensor. Para obter mais informações, consulte [defender for IOT Installation](how-to-install-software.md).
1. Ative e configure seu sensor. Para obter mais informações, consulte [entrar e ativar um sensor](how-to-activate-and-set-up-your-sensor.md).

## <a name="6-connect-sensors-to-an-on-premises-management-console"></a>6. conectar sensores a um console de gerenciamento local

Conecte os sensores ao console de gerenciamento para garantir que:

- Os sensores enviam informações de inventário de dispositivo e de alerta para o console de gerenciamento local.

- O console de gerenciamento local pode executar backups de sensor, gerenciar alertas que os sensores detectam, investigar desconexões de sensor e executar outras atividades em sensores conectados.

Recomendamos que você agrupe vários sensores monitorando as mesmas redes em uma zona. Isso fará a União das informações coletadas por vários sensores.

Para obter mais informações, consulte [conectar sensores ao console de gerenciamento local](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console).

## <a name="7-populate-azure-sentinel-with-alert-information-optional"></a>7. popular o Azure Sentinel com informações de alerta (opcional)

Enviar informações de alerta para o Azure Sentinel Configurando o Azure Sentinel. Consulte [conectar seus dados do defender para IOT ao Azure Sentinel](how-to-configure-with-sentinel.md).

## <a name="see-also"></a>Confira também

- [Bem-vindo ao Azure defender para IoT](overview.md)

- [Arquitetura do Azure defender para IoT](architecture.md)

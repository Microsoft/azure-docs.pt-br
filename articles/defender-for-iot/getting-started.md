---
title: 'Guia de início rápido: Introdução'
description: Neste guia de início rápido, você aprenderá os conceitos do fluxo de trabalho básico para implantação do Defender para IoT.
ms.topic: quickstart
ms.date: 2/18/2021
ms.openlocfilehash: aa26ea26a3fb0a08d931657cb7ad236c68972e2f
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384946"
---
# <a name="quickstart-get-started-with-defender-for-iot"></a>Guia de início rápido: Introdução ao Azure Defender para IoT

Este artigo fornece uma visão geral das etapas que você executará para configurar o Azure Defender para IoT. O processo exige que você:

- Registre sua assinatura e os sensores no portal do Azure Defender para IoT.
- Instale o software do sensor e do console de gerenciamento local.
- Execute a ativação inicial do sensor e do console de gerenciamento.

## <a name="prerequisites"></a>Pré-requisitos

- Nenhum

## <a name="permission-requirements"></a>Requisitos de permissão

Algumas das etapas de configuração exigem permissões de usuário específicas.

As permissões de usuário administrativo são necessárias para ativar o sensor e o console de gerenciamento, carregar certificados SSL/TLS e gerar senhas.

A seguinte tabela descreve as permissões de acesso do usuário para as ferramentas do portal do Azure Defender para IoT:

| Permissão | Leitor de segurança | Administrador de segurança | Colaborador da assinatura | Proprietário da assinatura |
|--|--|--|--|--|
| Exibir todas as telas e dados do Defender para IoT | ✓ | ✓ | ✓ | ✓ |
| Integrar um sensor  |  |  ✓ | ✓ | ✓ |
| Atualizar preço  |  |  ✓ | ✓ | ✓ |
| Recuperar senha  | ✓  |  ✓ | ✓ | ✓ |

## <a name="identify-the-solution-infrastructure"></a>Identificar a infraestrutura da solução

**Esclarecer suas necessidades de configuração de rede**

Pesquise a arquitetura de rede, a largura de banda monitorada e outros detalhes da rede. Para obter mais informações, confira [Sobre a configuração de rede do Azure Defender para IoT](how-to-set-up-your-network.md).

**Esclarecer quais sensores e dispositivos de console de gerenciamento são necessários para lidar com a carga de rede**

O Azure Defender para IoT dá suporte a implantações físicas e virtuais. Para as implantações físicas, você poderá comprar vários dispositivos certificados. Para obter mais informações, confira [Identificar os dispositivos necessários](how-to-identify-required-appliances.md).

Recomendamos que você calcule o número aproximado de dispositivos que serão monitorados. Posteriormente, ao registrar sua assinatura do Azure no portal, você precisará inserir esse número. Os números podem ser adicionados em intervalos de 1.000 segundos. Os números dos dispositivos monitorados são chamados de *dispositivos confirmados*.

## <a name="register-with-azure-defender-for-iot"></a>Registro no Azure Defender para IoT

O registro inclui:

- Integração das suas assinaturas do Azure ao Defender para IoT.
- Definição dos dispositivos confirmados.
- Download de um arquivo de ativação para o console de gerenciamento local.

Para se inscrever:

1. Acesse o portal do Azure Defender para IoT.

1. Selecione **Integrar assinatura**.

1. Na página **Preço**, selecione uma assinatura ou crie uma e adicione o número dos dispositivos confirmados.

1. Escolha a guia **Baixar o console de gerenciamento local** e salve o arquivo de ativação baixado. Esse arquivo contém os dispositivos confirmados agregados que você definiu. O arquivo será carregado no console de gerenciamento após a entrada inicial.

Para obter informações sobre como remover uma assinatura, confira [Remover uma assinatura](how-to-manage-subscriptions.md#offboard-a-subscription).

## <a name="install-and-set-up-the-on-premises-management-console"></a>Instalar e configurar o console de gerenciamento local

Depois de adquirir seu dispositivo de console de gerenciamento local:

- Baixe o pacote ISO do portal do Azure Defender para IoT.
- Instale o software.
- Ative e realize a instalação inicial do console de gerenciamento.

Para a instalação e a configuração:

1. Selecione **Introdução** no portal do Defender para IoT.
1. Selecione a guia **Console de gerenciamento local**.
1. Escolha uma versão e selecione **Baixar**.
1. Instale o software do console de gerenciamento local. Para obter mais informações, confira [Instalação do Defender para IoT](how-to-install-software.md).
1. Ative e configure o console de gerenciamento local. Para obter mais informações, confira [Ativar e configurar o console de gerenciamento local](how-to-activate-and-set-up-your-on-premises-management-console.md).

## <a name="onboard-a-sensor"></a>Integrar um sensor

Integre um sensor registrando-o no Azure Defender para IoT e baixando um arquivo de ativação do sensor:

1. Defina um nome de sensor e associe-o a uma assinatura.

1. Escolha um modo de gerenciamento do sensor:

   - **Sensores conectados à nuvem**: as informações que os sensores detectam são exibidas no console do sensor. Além disso, as informações de alertas são entregues por meio de um hub IoT e podem ser compartilhadas com outros serviços do Azure, por exemplo, o Azure Sentinel.

   - **Sensores gerenciados localmente**: as informações que os sensores detectam são exibidas no console do sensor. Se você estiver trabalhando em uma rede desconectada e quiser obter uma exibição unificada de todas as informações detectadas por vários sensores gerenciados localmente, trabalhe com o console de gerenciamento local. 

1. Baixe um arquivo de ativação do sensor.

Para obter mais informações, confira [Integrar e gerenciar sensores no portal do Defender para IoT](how-to-manage-sensors-on-the-cloud.md).

## <a name="install-and-set-up-the-sensor"></a>Instalar e configurar o sensor

Baixe o pacote ISO do portal do Azure Defender para IoT, instale o software e configure o sensor.

1. Selecione **Introdução** no portal do Defender para IoT.

1. Selecione **Configurar sensor**.

1. Escolha uma versão e selecione **Baixar**.

1. Instale o software do sensor. Para obter mais informações, confira [Instalação do Defender para IoT](how-to-install-software.md).

1. Ative e configure o sensor. Para obter mais informações, confira [Conectar-se e ativar um sensor](how-to-activate-and-set-up-your-sensor.md).

## <a name="connect-sensors-to-an-on-premises-management-console"></a>Conectar sensores a um console de gerenciamento local

Conecte os sensores ao console de gerenciamento para garantir que:

- Os sensores enviem informações de inventário de dispositivos e de alertas ao console de gerenciamento local.

- O console de gerenciamento local possa executar backups de sensor, gerenciar alertas detectados pelos sensores, investigar desconexões de sensor e executar outras atividades nos sensores conectados.

Recomendamos que você agrupe vários sensores monitorando as mesmas redes em uma zona. Essa ação unirá as informações coletadas por vários sensores.

Para obter mais informações, confira [Conectar sensores ao console de gerenciamento local](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console).

## <a name="populate-azure-sentinel-with-alert-information-optional"></a>Preencher o Azure Sentinel com informações de alertas (opcional)

Envie informações de alertas ao Azure Sentinel configurando o Azure Sentinel. Confira [Conectar seus dados do Defender para IoT ao Azure Sentinel](how-to-configure-with-sentinel.md).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Bem-vindo(a) ao Azure Defender para IoT](overview.md)
> [Arquitetura do Azure Defender para IoT](architecture.md)

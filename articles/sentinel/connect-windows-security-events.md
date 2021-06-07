---
title: Conectar dados de eventos de segurança do Windows ao Azure Sentinel | Microsoft Docs
description: Aprenda a usar o conector de eventos de segurança para transmitir todos os eventos de segurança de seus sistemas Windows para seu espaço de trabalho do Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: bc75ee64174957ad6486146b4da6f8a66a2120e5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100570547"
---
# <a name="connect-windows-security-events"></a>Conectar eventos de segurança do Windows 

O conector de eventos de segurança permite que você transmita todos os eventos de segurança de seus sistemas Windows (servidores e estações de trabalho, físicos e virtuais) para seu espaço de trabalho do Azure Sentinel. Isso permite exibir eventos de segurança do Windows em seus painéis, usá-los na criação de alertas personalizados e confiar neles para melhorar suas investigações, fornecendo mais informações sobre a rede da sua organização e expandindo seus recursos de operações de segurança. Você pode selecionar quais eventos transmitir entre os seguintes conjuntos: <a name="event-sets"></a>

- **Todos os eventos** -todos os eventos de segurança e AppLocker do Windows.
- **Comum** -um conjunto padrão de eventos para fins de auditoria. Uma trilha de auditoria de usuário completa está incluída neste conjunto. Por exemplo, ele contém os eventos de entrada do usuário e saída do usuário (IDs de evento 4624, 4634). Também há ações de auditoria, como alterações de grupo de segurança, operações Kerberos de controlador de domínio de chave e outros tipos de eventos em linha com práticas recomendadas aceitas.

    O conjunto de eventos **comum** pode conter alguns tipos de eventos que não são tão comuns.  Isso ocorre porque o ponto principal do conjunto **comum** é reduzir o volume de eventos para um nível mais gerenciável e, ao mesmo tempo, manter o recurso de trilha de auditoria completo.

- **Mínimo** -um pequeno conjunto de eventos que pode indicar possíveis ameaças. Esse conjunto não contém uma trilha de auditoria completa. Ele aborda apenas os eventos que podem indicar uma violação bem-sucedida e outros eventos importantes que têm taxas de ocorrência muito baixas. Por exemplo, ele contém logons de usuário bem-sucedidos e com falha (IDs de evento 4624, 4625), mas ele não contém informações de saída (4634) que, embora importantes para auditoria, não é significativo para detecção de violação e tem um volume relativamente alto. A maior parte do volume de dados desse conjunto é composta de eventos de entrada e eventos de criação de processos (ID do evento 4688).

- **Nenhum** -nenhum evento de segurança ou AppLocker. (Essa configuração é usada para desabilitar o conector.)

    A lista a seguir fornece uma análise completa das IDs de evento de segurança e do armário de aplicativo para cada conjunto:

    | Conjunto de eventos | IDs de eventos coletados |
    | --- | --- |
    | **Mínimo** | 1102, 4624, 4625, 4657, 4663, 4688, 4700, 4702, 4719, 4720, 4722, 4723, 4724, 4727, 4728, 4732, 4735, 4737, 4739, 4740, 4754, 4755, 4756, 4767 e 4799, 4825, 4946, 4948, 4956, 5024, 5033, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222 |
    | **Comum** | 1, 299, 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, 4624, 4625, 4634, 4647, 4648, 4649, 4657, 4661, 4662, 4663, 4665, 4666, 4667, 4688, 4670, 4672, 4673, 4674, 4675, 4689, 4697, 4700, 4702 e 4704, 4705, 4716, 4717, 4718, 4719, 4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729 , 4733, 4732, 4735, 4737, 4738, 4739, 4740, 4742, 4744, 4745, 4746, 4750, 4751, 4752, 4754, 4755, 4756, 4757, 4760, 4761, 4762, 4764, 4767, 4768, 4771, 4774, 4778, 4779, 4781, 4793, 4797, 4798, 4799, 4800, 4801, 4802, 4803, 4825, 4826, 4870, 4886, 4887, 4888, 4893, 4898, 4902 e 4904, 4905, 4907, 4931, 4932, 4933, 4946, 4948, 4956, 4985, 5024, 5033, 5059, 5136, 5137 , 5140, 5145, 5632, 6144, 6145, 6272, 6273, 6278, 6416, 6423, 6424, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222, 26401, 30004 |

> [!NOTE]
> A coleta de eventos de segurança no contexto de um único espaço de trabalho pode ser configurada a partir da central de segurança do Azure ou do Azure Sentinel, mas não ambos. Se você estiver integrando o Azure Sentinel em um espaço de trabalho que já está obtendo alertas do Azure defender da central de segurança do Azure e estiver definido para coletar eventos de segurança, você terá duas opções:
> - Deixe a coleção de Eventos de Segurança na Central de Segurança do Azure no estado em que se encontra. Você poderá consultar e analisar esses eventos no Azure Sentinel, bem como no Azure Defender. No entanto, não será possível monitorar o status de conectividade do conector nem alterar a configuração dele no Azure Sentinel. Se isso for importante para você, considere a segunda opção.
>
> - [Desabilite a coleção de Eventos de Segurança](../security-center/security-center-enable-data-collection.md) na Central de Segurança do Azure e adicione o conector de Eventos de Segurança no Azure Sentinel. Assim como aconteceu com a primeira opção, você poderá consultar e analisar eventos no Azure Sentinel e no Azure Defender/ASC, mas agora poderá monitorar o status de conectividade do conector ou alterar a configuração dele no (e apenas no) Azure Sentinel.

## <a name="set-up-the-windows-security-events-connector"></a>Configurar o conector de eventos de segurança do Windows

Para coletar seus eventos de segurança do Windows no Azure Sentinel:

1. No menu de navegação do Azure Sentinel, selecione **conectores de dados**. Na lista de conectores, clique em **eventos de segurança** e, em seguida, no botão **abrir página do conector** no canto inferior direito. Em seguida, siga as instruções na tela na guia **instruções** , conforme descrito no restante desta seção.

1. Verifique se você tem as permissões apropriadas, conforme descrito na seção **pré-requisitos** na página do conector.

1. Baixe e instale o [agente de log Analytics](../azure-monitor/agents/log-analytics-agent.md) (também conhecido como Microsoft Monitoring Agent ou MMA) nos computadores para os quais você deseja transmitir eventos de segurança para o Azure Sentinel.

    Para máquinas virtuais do Azure:
    
    1. Clique em **instalar agente na máquina virtual do Windows do Azure** e, em seguida, no link que aparece abaixo.
    1. Para cada máquina virtual que você deseja conectar, clique em seu nome na lista que aparece à direita e, em seguida, clique em **conectar**.

    Para computadores Windows que não são do Azure (físico, virtual local ou virtual em outra nuvem):

    1. Clique em **instalar agente em computador não Azure com Windows** e, em seguida, no link que aparece abaixo.
    1. Clique nos links de download apropriados que aparecem à direita, em **computadores com Windows**.
    1. Usando o arquivo executável baixado, instale o agente nos sistemas Windows de sua escolha e configure-o usando a **ID do espaço de trabalho e as chaves** que aparecem abaixo dos links de download mencionados acima.

    > [!NOTE]
    >
    > Para permitir que os sistemas Windows sem a conectividade de Internet necessária ainda transmitam eventos para o Azure Sentinel, baixe e instale o **gateway do OMS** em um computador separado, usando o link no canto inferior direito, para atuar como um proxy.  Você ainda precisará instalar o agente de Log Analytics em cada sistema Windows cujos eventos você deseja coletar.
    >
    > Para obter mais informações sobre esse cenário, consulte a documentação do [ **gateway de log Analytics**](../azure-monitor/agents/gateway.md).

    Para obter opções de instalação adicionais e mais detalhes, consulte a documentação do [ **agente de log Analytics**](../azure-monitor/agents/agent-windows.md).

1. Selecione qual conjunto de eventos ([tudo, comum ou mínimo](#event-sets)) você deseja transmitir.

1. Clique em **Atualizar**.

1. Para usar o esquema relevante no Log Analytics para eventos de segurança do Windows, digite `SecurityEvent` na janela de consulta.

## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar cerca de 20 minutos até que os logs comecem a aparecer na Log Analytics. 

### <a name="configure-the-security-events-connector-for-anomalous-rdp-login-detection"></a>Configurar o conector de eventos de segurança para detecção de logon de RDP anômala

> [!IMPORTANT]
> A detecção de logon de RDP anômala está atualmente em visualização pública.
> Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O Azure Sentinel pode aplicar o ML (aprendizado de máquina) aos dados de eventos de segurança para identificar a atividade de logon protocolo RDP (RDP). Os cenários incluem:

- **IP incomum** -o endereço IP raramente ou nunca foi observado nos últimos 30 dias

- **Localização geográfica incomum** -o endereço IP, a cidade, o país e o ASN raramente ou nunca foram observados nos últimos 30 dias

- **Novo usuário** – um novo usuário faz logon por meio de um endereço IP e localização geográfica, ou ambos ou dos quais não se espera que sejam vistos com base nos dados dos 30 dias anteriores.

**Instruções de configuração**

1. Você deve estar coletando dados de logon RDP (ID do evento 4624) por meio do conector de dados de **eventos de segurança** . Verifique se você selecionou um [conjunto de eventos](#event-sets) além de "nenhum" para transmitir para o Azure Sentinel.

1. No portal do Azure Sentinel, clique em **análise** e, em seguida, clique na guia **modelos de regra** . Escolha a regra de **detecção de logon de RDP anômala (visualização)** e mova o controle deslizante de **status** para **habilitado**.

    > [!NOTE]
    > Como o algoritmo de aprendizado de máquina exige um valor de dados de 30 dias para criar um perfil de linha de base de comportamento do usuário, você deve permitir que 30 dias de dados de eventos de segurança sejam coletados antes que qualquer incidente possa ser detectado.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar eventos de segurança do Windows ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a detectar ameaças com o Azure Sentinel, usando regras [internas](tutorial-detect-threats-built-in.md) ou [personalizadas](tutorial-detect-threats-custom.md) .


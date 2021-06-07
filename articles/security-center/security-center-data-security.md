---
title: Segurança de Dados da Central de Segurança do Azure | Microsoft Docs
description: Este documento explica como os dados são gerenciados e protegidos na Central de Segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33f2c9f4-21aa-4f0c-9e5e-4cd1223e39d7
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2020
ms.author: memildin
ms.openlocfilehash: 0c21c916d152188031212b47704dfb41d3f64a82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100596279"
---
# <a name="azure-security-center-data-security"></a>Segurança de dados da Central de Segurança do Azure

Para ajudar os clientes a evitarem, detectarem e responderem às ameaças, a Central de Segurança do Azure coleta e processa dados relacionados à segurança, incluindo informações da configuração, metadados, logs de eventos e mais. A Microsoft obedece às diretrizes rígidas de conformidade e segurança — da codificação à operação de um serviço.

Este artigo explica como os dados são gerenciados e protegidos na Central de Segurança.

## <a name="data-sources"></a>Fontes de dados
A Central de Segurança analisa os dados das seguintes fontes para fornecer visibilidade sobre o estado da segurança, identificar as vulnerabilidades e recomendar atenuações e detectar as ameaças ativas:

- **Serviços do Azure**: usa as informações sobre a configuração dos serviços do Azure que você implantou comunicando-se com o provedor de recursos do serviço.
- **Tráfego de rede**: usa os metadados do tráfego da rede de exemplo na infraestrutura da Microsoft, como IP/porta de origem/detino, tamanho do pacote e protocolo da rede.
- **Soluções de parceiros**: usa alertas de segurança das soluções de parceiros integradas, como firewalls e soluções antimalware.
- **Seus computadores**: usam as informações e detalhes da configuração sobre eventos de segurança, como o evento e logs de auditoria do Windows e mensagens do syslog de seus computadores.


## <a name="data-protection"></a>Proteção de dados

### <a name="data-segregation"></a>Segregação de dados
os dados são mantidos separados logicamente em cada componente em todo o serviço. Todos os dados são marcados por organização. Essa marcação persiste em todo o ciclo de vida dos dados e é imposta em cada camada do serviço.

### <a name="data-access"></a>Acesso de dados
Para fornecer recomendações de segurança e investigar as possíveis ameaças de segurança, os funcionários da Microsoft podem acessar as informações coletadas ou analisadas pelos serviços do Azure, incluindo eventos de criação do processo e outros artefatos, que podem incluir, involuntariamente, dados do cliente ou dados pessoais de seus computadores. 

Seguimos o [Adendo de Proteção de Dados do Microsoft Online Services](https://www.microsoftvolumelicensing.com/Downloader.aspx?DocumentId=17880), que determina que a Microsoft não usará os dados do cliente nem obterá as informações para fins comerciais semelhantes ou de propaganda. Somente usamos os Dados do Cliente conforme o necessário para fornecer os serviços do Azure, inclusive para fins compatíveis com o fornecimento desses serviços. Você mantém todos os direitos dos Dados do Cliente.

### <a name="data-use"></a>Uso de dados
a Microsoft usa os padrões e a inteligência de ameaças vistos em vários locatários para aprimorar os recursos de detecção e prevenção. Fazemos isso de acordo com os compromissos de privacidade descritos em nossa [Política de Privacidade](https://privacy.microsoft.com/privacystatement).

## <a name="manage-data-collection-from-machines"></a>Gerenciar a coleta de dados dos computadores
Quando você escolhe habilitar a Central de Segurança no Azure, a coleta de dados é ativada para cada uma de suas assinaturas do Azure. Você também pode habilitar a coleta de dados para suas assinaturas na Central de Segurança. Quando a coleta de dados é habilitada, a Central de Segurança provisiona o agente do Log Analytics em todas as máquinas virtuais do Azure existentes com suporte e nas que são criadas.

O agente do Log Analytics examina várias configurações e eventos relacionados à segurança nos rastreamentos [ETW](/windows/win32/etw/event-tracing-portal) (Rastreamento de Eventos para Windows). Além disso, o sistema operacional irá gerar eventos do log de eventos no decorrer da execução da máquina. Exemplos desses dados são: tipo e versão do sistema operacional, logs do sistema operacional (logs de eventos do Windows), processos em execução, nome do computador, endereços IP, usuário registrado e ID do locatário. O agente di Log Analytics lê as entradas do registro de eventos e os vestígios de ETW e os copia para seus workspaces para análise. O agente do Log Analytics também permite eventos de criação de processos e auditoria de linha de comando.

Se você não estiver usando o Azure Defender, desabilite também a coleta de dados das máquinas virtuais na política de segurança. A coleta de dados é necessária para as assinaturas protegidas pelo Azure Defender. Os instantâneos de disco da VM e a coleção de artefatos ainda serão habilitados mesmo que a coleta de dados tenha sido desabilitada.

Você pode especificar o workspace e a região em que os dados coletados dos seus computadores são armazenados. O padrão é armazenar os dados coletados de seus computadores no workspace mais próximo, conforme mostrado na seguinte tabela:

| Replicação geográfica de VM                                      | Replicação Geográfica do Workspace  |
|---------------------------------------------|----------------|
| Estados Unidos, Brasil, África do Sul         | Estados Unidos  |
| Canada                                      | Canada         |
| Europa (exceto Reino Unido)           | Europa         |
| United Kingdom                              | United Kingdom |
| Ásia (exceto Índia, Japão, Coreia do Sul, China) | Pacífico Asiático   |
| Coreia do Sul                                       | Pacífico Asiático   |
| Índia                                       | Índia          |
| Japão                                       | Japão          |
| China                                       | China          |
| Austrália                                   | Austrália      |
|                                             |                |

> [!NOTE]
> O **Azure Defender para Armazenamento** armazena artefatos regionalmente de acordo com a localização do recurso do Azure relacionado. Saiba mais em [Introdução ao Azure Defender para Armazenamento](defender-for-storage-introduction.md).


## <a name="data-consumption"></a>Consumo de dados

Os clientes podem acessar os dados relacionados à Central de Segurança dos seguintes fluxos de dados:


| STREAM                                                                                | Tipos de dados                                                                                                                                                                                                          |
|---------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Log de Atividades do Azure](../azure-monitor/essentials/activity-log.md)                       | Todos os alertas de segurança, as solicitações de acesso [just-in-time](security-center-just-in-time.md) da Central de Segurança aprovadas e todos os alertas gerados pelos [controles de aplicativos adaptáveis](security-center-adaptive-application.md).|
| [Logs do Azure Monitor](../azure-monitor/data-platform.md)                      | Todos os alertas de segurança.                                                                                                                                                                                                |
| [Gráfico de Recursos do Azure](../governance/resource-graph/overview.md)                      | Alertas de segurança, recomendações de segurança, resultados da avaliação de vulnerabilidade, informações de classificação de segurança, status de verificações de conformidade e muito mais.                                                                       |
| [API REST da Central de Segurança do Azure](/rest/api/securitycenter/) | Alertas de segurança, recomendações de segurança e muito mais.                                                                                                                                                                |
|                                                                                       |                                                                                                                                                                                                                     |

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu como os dados são gerenciados e protegidos na Central de Segurança do Azure. 

Para saber mais sobre a Central de Segurança do Azure, confira [O que é a Central de Segurança do Azure?](security-center-introduction.md)
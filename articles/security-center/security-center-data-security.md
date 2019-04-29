---
title: Segurança de Dados da Central de Segurança do Azure | Microsoft Docs
description: Este documento explica como os dados são gerenciados e protegidos na Central de Segurança do Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 33f2c9f4-21aa-4f0c-9e5e-4cd1223e39d7
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: rkarlin
ms.openlocfilehash: 7a327c963f32d6bf5b0cf7751557c61327fd26e9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60911537"
---
# <a name="azure-security-center-data-security"></a>Segurança dos Dados da Central de Segurança do Azure
Para ajudar os clientes a evitarem, detectarem e responderem às ameaças, a Central de Segurança do Azure coleta e processa dados relacionados à segurança, incluindo informações da configuração, metadados, logs de eventos, arquivos de despejo corrompidos e mais. A Microsoft obedece às diretrizes rígidas de conformidade e segurança — da codificação à operação de um serviço.

Este artigo explica como os dados são gerenciados e protegidos na Central de Segurança do Azure.

## <a name="data-sources"></a>Fontes de dados
A Central de Segurança do Azure analisa os dados das seguintes fontes para fornecer visibilidade sobre o estado da segurança, identificar as vulnerabilidades e recomendar atenuações, e detectar as ameaças ativas:

- Serviços do Azure: usa as informações sobre a configuração dos serviços do Azure que você implantou comunicando-se com o provedor de recursos do serviço.
- Tráfego de rede: usa os metadados do tráfego da rede de exemplo na infraestrutura da Microsoft, como IP/porta de origem/detino, tamanho do pacote e protocolo da rede.
- Soluções de Parceiros: usa alertas de segurança das soluções de parceiros integradas, como firewalls e soluções antimalware.
- Suas Máquinas Virtuais e Servidores: usa as informações da configuração e informações sobre os eventos de segurança, como eventos do Windows e logs de auditoria, logs do IIS, mensagens do syslog e arquivos de despejo de memória de suas máquinas virtuais. Além disso, quando um alerta é criado, a Central de Segurança do Azure pode gerar um instantâneo do disco da VM afetado e extrair os artefatos da máquina relacionados ao alerta a partir do disco da VM, como um arquivo de registro, para fazer uma análise forense.


## <a name="data-protection"></a>Proteção de dados
**Segregação de dados**: os dados são mantidos separados logicamente em cada componente em todo o serviço. Todos os dados são marcados por organização. Essa marcação persiste em todo o ciclo de vida dos dados e é imposta em cada camada do serviço.

**Acesso a dados**: Para fornecer recomendações de segurança e investigar as possíveis ameaças de segurança, os funcionários da Microsoft podem acessar as informações coletadas ou analisadas pelos serviços do Azure, incluindo arquivos de despejo de memória, eventos de criação do processo, instantâneos de disco da VM e artefatos, que podem incluir, involuntariamente, Dados do Cliente ou dados pessoais de suas máquinas virtuais. Seguimos os [Termos de Serviços e Política de Privacidade do Microsoft Online](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), que determinam que a Microsoft não usará os Dados do Cliente nem obterá as informações para fins comerciais ou de propaganda semelhantes. Somente usamos os Dados do Cliente conforme o necessário para fornecer os serviços do Azure, inclusive para fins compatíveis com o fornecimento desses serviços. Você mantém todos os direitos dos Dados do Cliente.

**Uso de dados**: a Microsoft usa os padrões e a inteligência de ameaças vistos em vários locatários para aprimorar os recursos de detecção e prevenção. Fazemos isso de acordo com os compromissos de privacidade descritos em nossa [Política de Privacidade](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx).

## <a name="data-location"></a>Local dos dados

**Seus workspaces**: um workspace é especificado para as áreas geográficas a seguir e os dados coletados de suas máquinas virtuais do Azure, incluindo os despejos de memória e alguns tipos de dados de alerta, são armazenados no workspace mais próximo.

| Replicação geográfica de VM                        | Replicação Geográfica do Workspace |
|-------------------------------|---------------|
| Estados Unidos, Brasil, Canadá | Estados Unidos |
| Europa, Reino Unido        | Europa        |
| Pacífico Asiático, Japão, Índia    | Pacífico Asiático  |
| Austrália                     | Austrália     |


Os instantâneos de disco da VM são armazenados na mesma conta de armazenamento do disco da VM.

Para as máquinas virtuais e os servidores executados em outros ambientes, por exemplo, no local, você pode especificar o workspace e a região onde os dados coletados serão armazenados.

**Armazenamento da Central de Segurança do Azure**: informações sobre alertas de segurança, incluindo alertas de parceiro são armazenados regionalmente de acordo com a localização do recurso do Azure relacionado, enquanto as informações sobre o status de integridade de segurança e a recomendação são centralmente armazenadas nos Estados Unidos ou Europa de acordo com a localização do cliente.
A Central de Segurança do Azure coleta as cópias transitórias dos seus arquivos de despejo corrompidos e analisa-as para obter evidências das tentativas de exploração e comprometimentos bem-sucedidos. A Central de Segurança do Azure executa essa análise na mesma área geográfica do workspace e exclui as cópias transitórias quando a análise é concluída.

Os artefatos da máquina são armazenados de modo central na mesma região da VM.


## <a name="managing-data-collection-from-virtual-machines"></a>Gerenciar a coleta de dados das máquinas virtuais

Quando você escolhe habilitar a Central de Segurança no Azure, a coleta de dados é ativada para cada uma de suas assinaturas do Azure. Você também pode ativar a coleta de dados para suas assinaturas na seção Política de Segurança da Central de Segurança do Azure. Quando a Coleta de dados é ativada, a Central de Segurança do Azure provisiona o Microsoft Monitoring Agent em todas as máquinas virtuais do Azure existentes com suporte e as novas criadas.
O Microsoft Monitoring Agent examina várias configurações e eventos relacionados à segurança nos rastreamentos [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (Rastreamento de Eventos para Windows). Além disso, o sistema operacional irá gerar eventos do log de eventos no decorrer da execução da máquina. Exemplos desses dados são: tipo e versão do sistema operacional, logs do sistema operacional (logs de eventos do Windows), processos em execução, nome do computador, endereços IP, usuário registrado e ID do locatário. O Microsoft Monitoring Agent lê as entradas do registro de eventos e os vestígios de ETW e os copia para seus workspaces para análise. O Microsoft Monitoring Agent também copia os arquivos de despejo de falha para seus workspaces, habilita eventos de criação de processo e habilita a auditoria de linha de comando.

Se você estiver usando a Central de Segurança do Azure Gratuita, também poderá desabilitar a coleta de dados de máquinas virtuais na Política de Segurança. A Coleta de Dados é necessária para as assinaturas na camada Standard. Os instantâneos de disco da VM e a coleção de artefatos ainda serão habilitados mesmo que a coleta de dados tenha sido desabilitada.

## <a name="data-consumption"></a>Consumo de Dados

Os clientes podem consumir dados relacionados à Central de Segurança de diferentes fluxos de dados, conforme mostrado abaixo:

* **Atividade do Azure**: todos os alertas de segurança, as solicitações [just in time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) aprovadas da Central de Segurança e todos os alertas gerados por [controles de aplicativo adaptável](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application).
* **Os logs do Azure Monitor**: todos os alertas de segurança.


> [!NOTE]
> Recomendações de segurança também podem ser consumidas por meio da API REST. Leia [Referência da API REST do provedor de recursos de segurança](https://msdn.microsoft.com/library/mt704034(Azure.100).aspx) para obter mais informações.

## <a name="see-also"></a>Consulte também
Neste documento, você aprendeu como os dados são gerenciados e protegidos na Central de Segurança do Azure. Para saber mais sobre a Central de Segurança do Azure, consulte:

* [Guia de Operações e Planejamento da Central de Segurança do Azure](security-center-planning-and-operations-guide.md) : saiba como planejar e entender as considerações de design para adotar a Central de Segurança do Azure.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md) – saiba como monitorar a integridade dos recursos do Azure
* [Gerenciando e respondendo aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) – aprenda a gerenciar e responder aos alertas de segurança
* [Monitorando as soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md) – saiba como monitorar o status de integridade de suas soluções de parceiros.
* [Perguntas Frequentes sobre a Central de Segurança do Azure](security-center-faq.md) – encontre as perguntas frequentes sobre como usar o serviço
* [Blog de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) – encontre postagens no blog sobre conformidade e segurança do Azure

---
title: Conectar dados brutos do Microsoft 365 defender ao Azure sentinela | Microsoft Docs
description: Saiba como ingerir dados de eventos brutos do Microsoft 365 defender para o Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: 756c245fe06ae81545a125dd98f30fb27fdff2dd
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655572"
---
# <a name="connect-data-from-microsoft-365-defender-to-azure-sentinel"></a>Conectar dados do Microsoft 365 defender ao Azure Sentinel

> [!IMPORTANT]
>
> O **Microsoft 365 defender** era conhecido anteriormente como **Microsoft Threat Protection** ou **MTP**.
>
> **O Microsoft defender for Endpoint** era conhecido anteriormente como **proteção avançada contra ameaças do Microsoft defender** ou **MDATP**.
>
> Você pode ver que os nomes antigos ainda estão em uso por um período de tempo.

## <a name="background"></a>Tela de fundo

O novo conector do [Microsoft 365 defender](/microsoft-365/security/mtp/microsoft-threat-protection) permite que você transmita logs de **busca avançados** – um tipo de dados brutos de evento – do Microsoft 365 defender para o Azure Sentinel. 

Com a integração do [Microsoft defender for Endpoint (MDATP)](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) com a proteção de segurança do Microsoft 365 defender, agora você pode coletar seus eventos de [busca avançada](/windows/security/threat-protection/microsoft-defender-atp/advanced-hunting-overview) do Microsoft defender para pontos de extremidade usando o conector do Microsoft 365 defender e transmiti-los diretamente para novas tabelas criadas no seu espaço de trabalho do Azure Sentinel. Essas tabelas são criadas no mesmo esquema usado no portal do Microsoft 365 defender, oferecendo acesso completo ao conjunto completo de logs de busca avançada e permitindo que você faça o seguinte:

- Copie facilmente suas consultas de busca avançada existentes do Microsoft defender ATP para o Azure Sentinel.

- Use os logs de eventos brutos para fornecer informações adicionais para seus alertas, busca e investigação e correlacionar eventos com dados de fontes de dados adicionais no Azure Sentinel.

- Armazene os logs com maior retenção, além do Microsoft defender para ponto de extremidade ou retenção padrão do Microsoft 365 defender de 30 dias. Você pode fazer isso Configurando a retenção do seu espaço de trabalho ou configurando a retenção por tabela no Log Analytics.

> [!IMPORTANT]
>
> O conector do Microsoft 365 defender está atualmente em visualização pública. Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter uma licença válida para o Microsoft defender para ponto de extremidade, conforme descrito em [Configurar o Microsoft defender para implantação de ponto de extremidade](/windows/security/threat-protection/microsoft-defender-atp/licensing). 

- O usuário deve receber a função de administrador global no locatário (em Azure Active Directory).

## <a name="connect-to-microsoft-365-defender"></a>Conectar-se ao Microsoft 365 defender

Se o Microsoft defender for Endpoint for implantado e ingerindo seus dados, os logs de eventos poderão ser facilmente transmitidos para o Azure Sentinel.

1. No Azure Sentinel, selecione **conectores de dados**, selecione **Microsoft 365 defender (versão prévia)** na galeria e selecione a **página abrir conector**.

1. Os seguintes tipos de eventos podem ser coletados de suas tabelas de busca avançada correspondentes. Marque as caixas de seleção dos tipos de evento que você deseja coletar:

    | Tipo de eventos | Nome da tabela |
    |-|-|
    | Informações do computador (incluindo informações do sistema operacional) | DeviceInfo |
    | Propriedades de rede de computadores | DeviceNetworkInfo |
    | Criação de processos e eventos relacionados | DeviceProcessEvents |
    | Conexão de rede e eventos relacionados | DeviceNetworkEvents |
    | Criação de arquivo, modificação e outros eventos do sistema de arquivos | DeviceFileEvents |
    | Criação e modificação de entradas do registro | DeviceRegistryEvents |
    | Entradas e outros eventos de autenticação | DeviceLogonEvents |
    | Eventos de carregamento de DLL | DeviceImageLoadEvents |
    | Tipos de eventos adicionais | DeviceEvents |
    |

1. Clique em **Aplicar Alterações**. 

1. Para consultar as tabelas de busca avançadas em Log Analytics, insira o nome da tabela na lista acima na janela de consulta.

## <a name="verify-data-ingestion"></a>Verificar a ingestão de dados

O grafo de dados na página do conector indica que você está ingerindo dados. Você observará que ele mostra uma única linha que agrega o volume do evento em todas as tabelas habilitadas. Depois de habilitar o conector, você pode usar a seguinte consulta KQL para gerar um grafo semelhante de volume de evento para uma única tabela (altere a tabela *DeviceEvents* para a tabela necessária de sua escolha):

```kusto
let Now = now();
(range TimeGenerated from ago(14d) to Now-1d step 1d
| extend Count = 0
| union isfuzzy=true (
    DeviceEvents
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now)
)
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now)
| sort by TimeGenerated
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events")
| render timechart
```

Na guia **próximas etapas** , você encontrará algumas consultas de exemplo que foram incluídas. Você pode executá-los no local, ou modificá-los e salvá-los.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a obter dados de eventos brutos do Microsoft defender for Endpoint para o Azure Sentinel, usando o conector do Microsoft 365 defender. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](./tutorial-detect-threats-built-in.md).
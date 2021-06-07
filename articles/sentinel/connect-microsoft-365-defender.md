---
title: Conectar dados do Microsoft 365 defender ao Azure Sentinel | Microsoft Docs
description: Saiba como incluir incidentes, alertas e dados de eventos brutos do Microsoft 365 defender no Azure Sentinel.
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
ms.openlocfilehash: 6500805a4dc7e26f5e1bc601df9ea78279ae17e9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101709335"
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

O conector do [Microsoft 365 defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) do Sentinela do Azure com integração de incidentes permite transmitir todos os incidentes e alertas do M365D para o Azure Sentinel e mantém os incidentes sincronizados entre os dois portais. Os incidentes de M365D incluem todos os seus alertas, entidades e outras informações relevantes, e eles são aprimorados pelo e agrupam alertas de serviços de componentes do M365D's **Microsoft defender for Endpoint**, **Microsoft defender for Identity**, **Microsoft defender for Office 365** e **Microsoft Cloud app Security**.

O conector também permite que você transmita eventos de **busca avançada** do Microsoft defender para o ponto de extremidade para o Azure Sentinel, permitindo que você copie consultas de busca avançada de MdE para o Azure Sentinel, aprimore alertas de sentinela com dados de evento brutos MdE para fornecer informações adicionais e armazene os logs com maior retenção em log Analytics.

Para obter mais informações sobre a integração de incidentes e a coleta de eventos de busca avançada, consulte [integração do Microsoft 365 defender com o Azure Sentinel](microsoft-365-defender-sentinel-integration.md).

> [!IMPORTANT]
>
> O conector do Microsoft 365 defender está atualmente em versão **prévia**. Consulte os [termos de uso suplementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter uma licença válida para o Microsoft 365 defender, conforme descrito em [pré-requisitos do Microsoft 365 defender](/microsoft-365/security/mtp/prerequisites). 

- Você deve ser um **administrador global** ou um **administrador de segurança** no Azure Active Directory.

## <a name="connect-to-microsoft-365-defender"></a>Conectar-se ao Microsoft 365 defender

1. No Azure Sentinel, selecione **conectores de dados**, selecione **Microsoft 365 defender (versão prévia)** na galeria e selecione a **página abrir conector**.

1. Em **configuração** na seção **& alertas do Connect incidents** , clique no botão **conectar incidentes & alertas** .

1. Para evitar a duplicação de incidentes, é recomendável marcar a caixa de seleção **desativar todas as regras de criação de incidentes da Microsoft para esses produtos.**

    > [!NOTE]
    > Quando você habilita o conector do Microsoft 365 defender, todos os conectores dos componentes do M365D (aqueles mencionados no início deste artigo) são automaticamente conectados em segundo plano. Para desconectar um dos conectores dos componentes, você deve primeiro desconectar o conector do Microsoft 365 defender.

1. Para consultar dados de incidente do M365 defender, use a seguinte instrução na janela de consulta:
    ```kusto
    SecurityIncident
    | where ProviderName == "Microsoft 365 Defender"
    ```

1. Se você quiser coletar eventos de busca avançada do Microsoft defender para ponto de extremidade, os seguintes tipos de eventos poderão ser coletados de suas tabelas de busca avançada correspondentes.

    1. Marque as caixas de seleção das tabelas com os tipos de evento que você deseja coletar:

       | Nome da tabela | Tipo de eventos |
       |-|-|
       | DeviceInfo | Informações do computador (incluindo informações do sistema operacional) |
       | DeviceNetworkInfo | Propriedades de rede de computadores |
       | DeviceProcessEvents | Criação de processos e eventos relacionados |
       | DeviceNetworkEvents | Conexão de rede e eventos relacionados |
       | DeviceFileEvents | Criação de arquivo, modificação e outros eventos do sistema de arquivos |
       | DeviceRegistryEvents | Criação e modificação de entradas do registro |
       | DeviceLogonEvents | Entradas e outros eventos de autenticação |
       | DeviceImageLoadEvents | Eventos de carregamento de DLL |
       | DeviceEvents | Tipos de eventos adicionais |
       | DeviceFileCertificateInfo | Informações de certificado de arquivos assinados |
       |

    1. Clique em **Aplicar Alterações**.

    1. Para consultar as tabelas de busca avançadas em Log Analytics, insira o nome da tabela na lista acima na janela de consulta.

## <a name="verify-data-ingestion"></a>Verificar a ingestão de dados

O grafo de dados na página do conector indica que você está ingerindo dados. Você observará que ele mostra uma linha para incidentes, alertas e eventos, e a linha de eventos é uma agregação do volume do evento em todas as tabelas habilitadas. Depois de habilitar o conector, você poderá usar as consultas KQL a seguir para gerar grafos mais específicos.

Use a seguinte consulta KQL para um grafo dos incidentes de entrada do M365 defender:

```kusto
let Now = now(); 
(range TimeGenerated from ago(14d) to Now-1d step 1d 
| extend Count = 0 
| union isfuzzy=true ( 
    SecurityIncident
    | where ProviderName == "Microsoft 365 Defender"
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now) 
) 
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now) 
| sort by TimeGenerated 
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events") 
| render timechart 
```

Use a seguinte consulta KQL para gerar um grafo de volume de evento para uma única tabela (altere a tabela *DeviceEvents* para a tabela necessária de sua escolha):

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

Na guia **próximas etapas** , você encontrará algumas pastas de trabalho úteis, consultas de exemplo e modelos de regra de análise que foram incluídos. Você pode executá-los no local ou modificá-los e salvá-los.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a integrar os incidentes do Microsoft 365 defender e os dados de evento de busca avançada do Microsoft defender for Endpoint, no Azure Sentinel, usando o conector do Microsoft 365 defender. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](./tutorial-detect-threats-built-in.md).

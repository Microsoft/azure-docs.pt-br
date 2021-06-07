---
title: Integração do Microsoft 365 defender com o Azure Sentinel | Microsoft Docs
description: Saiba como a integração do Microsoft 365 defender com o Azure Sentinel oferece a capacidade de usar o Azure Sentinel como sua fila de incidentes universal enquanto preserva os pontos fortes do M365D's para auxiliar na investigação de incidentes de segurança do M365 e também como ingerir dados de busca avançada de componentes do defender no Azure Sentinel.
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
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: abace18db51a7a571ecc66d50253277fbd2296d3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101744273"
---
# <a name="microsoft-365-defender-integration-with-azure-sentinel"></a>Integração do Microsoft 365 defender com o Azure Sentinel

> [!IMPORTANT]
> O conector do Microsoft 365 defender está atualmente em versão **prévia**. Consulte os [termos de uso suplementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.

> [!IMPORTANT]
>
> O **Microsoft 365 defender** era conhecido anteriormente como **Microsoft Threat Protection** ou **MTP**.
>
> **O Microsoft defender for Endpoint** era conhecido anteriormente como **proteção avançada contra ameaças do Microsoft defender** ou **MDATP**.
>
> Você pode ver que os nomes antigos ainda estão em uso por um período de tempo.

## <a name="incident-integration"></a>Integração de incidentes

A integração de incidentes do Azure Sentinel [Microsoft 365 defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) permite transmitir todos os incidentes do M365D para o Azure Sentinel e mantê-los sincronizados entre os dois portais. Os incidentes do M365D (anteriormente conhecido como Microsoft Threat Protection ou MTP) incluem todos os alertas associados, entidades e informações relevantes, fornecendo contexto suficiente para realizar a triagem e a investigação preliminar no Azure Sentinel. Uma vez no sentinela, os incidentes permanecerão sincronizados bidirecionalmente com o M365D, permitindo que você aproveite os benefícios de ambos os portais em sua investigação de incidentes.

Essa integração dá aos incidentes de segurança Microsoft 365 a visibilidade a ser gerenciada de dentro do Azure Sentinel, como parte da fila de incidente principal em toda a organização, para que você possa ver – e correlacionar – incidentes de M365 junto com aqueles de todos os seus sistemas locais e de nuvem. Ao mesmo tempo, ele permite que você aproveite as vantagens e os recursos exclusivos do M365D para investigações aprofundadas e uma experiência específica de M365 em todo o ecossistema M365. O M365 defender aprimora e agrupa alertas de vários produtos M365, reduzindo o tamanho da fila de incidentes do SOC e reduzindo o tempo de resolução. Os serviços de componentes que fazem parte do M365 defender Stack são:

- **Microsoft defender para ponto de extremidade** (MDE, anteriormente MDATP)
- **Microsoft defender for Identity** (MDI, anteriormente AATP)
- **Microsoft defender para Office 365** (MDO, anteriormente O365ATP)
- **Microsoft Cloud app Security** (MCAS)

Além de coletar alertas desses componentes, o M365 defender gera alertas próprios. Ele cria incidentes de todos esses alertas e os envia para o Azure Sentinel.

### <a name="common-use-cases-and-scenarios"></a>Cenários e casos de uso comuns

- Conexão com um clique de incidentes do M365 defender, incluindo todos os alertas e entidades de componentes do M365 defender, para o Azure Sentinel.

- Sincronização bidirecional entre os incidentes de sentinela e M365D sobre o status, o proprietário e o motivo de fechamento.

- Aproveite o agrupamento de alertas do M365 defender e os recursos de enriquecimento no Azure Sentinel, reduzindo o tempo de resolução.

- Link profundo do contexto entre um incidente do Azure Sentinel e seu incidente paralelo do M365 defender, para facilitar as investigações em ambos os portais.

### <a name="connecting-to-microsoft-365-defender"></a>Conectando-se ao Microsoft 365 defender

Depois de habilitar o conector de dados do Microsoft 365 defender para [coletar incidentes e alertas](connect-microsoft-365-defender.md), os incidentes de M365D aparecerão na fila de incidentes do Azure Sentinel, com o **Microsoft 365 defender** no campo **nome do produto** , logo depois que eles forem gerados no M365 defender.
- Pode levar até 10 minutos a partir do momento em que um incidente é gerado no M365 defender até o momento em que ele aparece no Azure Sentinel.

- Os incidentes serão ingeridos e sincronizados sem nenhum custo extra.

Depois que a integração do M365 defender for conectada, todos os conectores de alerta do componente (MDE, MDI, MDO, MCAS) serão automaticamente conectados em segundo plano, caso ainda não tenham sido. Se qualquer licença de componente tiver sido adquirida após a conexão do M365 defender, os alertas e os incidentes do novo produto continuarão a fluir para o Azure Sentinel sem nenhuma configuração ou cobrança adicional.

### <a name="m365-defender-incidents-and-microsoft-incident-creation-rules"></a>Incidentes do M365 defender e regras de criação de incidentes da Microsoft

- Incidentes gerados pelo M365 defender, com base nos alertas provenientes de produtos de segurança do M365, são criados usando a lógica M365 personalizada.

- As regras de criação de incidentes da Microsoft no Azure Sentinel também criam incidentes com os mesmos alertas, usando (uma diferente) lógica do Sentinela do Azure personalizada.

- O uso de ambos os mecanismos juntos é completamente suportado e essa configuração pode ser usada para facilitar a transição para a nova lógica de criação de incidentes do M365 defender. No entanto, isso criará **incidentes duplicados** para os mesmos alertas.

- Para evitar a criação de incidentes duplicados para os mesmos alertas, recomendamos que os clientes desativem todas as **regras de criação de incidentes da Microsoft** para produtos M365 (MDE, MDI e MDO-consulte a MCAS abaixo) ao conectar o M365 defender. Isso pode ser feito marcando a caixa de seleção relevante na página conector. Tenha em mente que, se você fizer isso, todos os filtros que foram aplicados pelas regras de criação de incidentes não serão aplicados à integração de incidentes do M365 defender.

- Para alertas de Microsoft Cloud App Security (MCAS), nem todos os tipos de alertas estão atualmente integrados ao M365 defender. Para verificar se você ainda está recebendo incidentes para todos os alertas do MCAS, você deve manter ou criar **regras de criação de incidentes da Microsoft** para os tipos de alertas *não integrados* ao M365D.

### <a name="working-with-m365-defender-incidents-in-azure-sentinel-and-bi-directional-sync"></a>Trabalhando com incidentes do M365 defender no Azure Sentinel e na sincronização bidirecional

Os incidentes do M365 defender serão exibidos na fila de incidentes do Azure Sentinel com o nome do produto **Microsoft 365 defender** e com detalhes e funcionalidade semelhantes para qualquer outro incidente do sentinela. Cada incidente contém um link de volta para o incidente paralelo no portal do M365 defender.

À medida que o incidente evolui no M365 defender e mais alertas ou entidades são adicionados a ele, o incidente do Azure Sentinel será atualizado de acordo.

As alterações feitas no status, no motivo de fechamento ou na atribuição de um incidente M365, no M365D ou no Azure Sentinel, da mesma forma serão atualizadas adequadamente na fila de incidentes do outro. A sincronização ocorrerá em ambos os portais imediatamente depois que a alteração no incidente for aplicada, sem atraso. Uma atualização pode ser necessária para ver as alterações mais recentes.

No M365 defender, todos os alertas de um incidente podem ser transferidos para outro, resultando na mesclagem dos incidentes. Quando isso acontece, os incidentes do Azure Sentinel refletirão as alterações. Um incidente conterá todos os alertas de ambos os incidentes originais e o outro incidente será fechado automaticamente, com uma marca de "Redirecionado" adicionado.

> [!NOTE]
> Os incidentes no Azure Sentinel podem conter um máximo de 150 alertas. Incidentes de M365D podem ter mais do que isso. Se um incidente de M365D com mais de 150 alertas for sincronizado com o Azure Sentinel, o incidente de sentinela mostrará como tendo "150 +" alertas e fornecerá um link para o incidente paralelo em M365D, em que você verá o conjunto completo de alertas.

## <a name="advanced-hunting-event-collection"></a>Coleta de eventos de busca avançada

O conector do Microsoft 365 defender também permite que você transmita eventos de **busca avançados** – um tipo de dados brutos de evento – do Microsoft 365 defender e de seus serviços de componentes para o Azure Sentinel. Atualmente, você pode coletar eventos de [busca avançada](/windows/security/threat-protection/microsoft-defender-atp/advanced-hunting-overview) do [Microsoft defender for Endpoint (MDATP)](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) e transmiti-los diretamente para tabelas criadas com finalidade no seu espaço de trabalho do Azure Sentinel. Essas tabelas são criadas no mesmo esquema usado no portal do Microsoft 365 defender, oferecendo acesso completo ao conjunto completo de eventos de busca avançados e permitindo que você faça o seguinte:

- Copie facilmente suas consultas de busca avançada do Microsoft defender for Endpoint para o Azure Sentinel.

- Use os logs de eventos brutos para fornecer informações adicionais para seus alertas, busca e investigação e correlacionar esses eventos com outras pessoas de fontes de dados adicionais no Azure Sentinel.

- Armazene os logs com maior retenção, além do Microsoft defender para ponto de extremidade ou retenção padrão do Microsoft 365 defender de 30 dias. Você pode fazer isso Configurando a retenção do seu espaço de trabalho ou configurando a retenção por tabela no Log Analytics.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a aproveitar o uso do Microsoft 365 defender junto com o Azure Sentinel, usando o conector do Microsoft 365 defender.

- Obtenha instruções para [habilitar o conector do Microsoft 365 defender](connect-microsoft-365-defender.md).
- Crie [alertas personalizados](tutorial-detect-threats-custom.md) e [investigue incidentes](tutorial-investigate-cases.md).

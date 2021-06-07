---
title: Conectar dados de inteligência contra ameaças ao Azure Sentinel | Microsoft Docs
description: Saiba mais sobre como conectar dados de inteligência contra ameaças ao Azure Sentinel.
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2019
ms.author: yelevin
ms.openlocfilehash: 205cc6eea5d1ac3be2d0e266621067dc8e20d2f9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96121740"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>Conectar dados dos provedores de inteligência contra ameaças

> [!IMPORTANT]
> Os conectores de dados de inteligência contra ameaças no Azure Sentinel estão atualmente em visualização pública.
> Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O Azure Sentinel permite que você importe os indicadores de ameaça que sua organização está usando, o que pode aprimorar a capacidade dos analistas de segurança de detectar e priorizar ameaças conhecidas. Vários recursos do Azure Sentinel ficam disponíveis ou são aprimorados:

- O **Analytics** inclui um conjunto de modelos de regras agendadas que você pode habilitar para gerar alertas e incidentes com base em correspondências de eventos de log de seus indicadores de ameaça.

- As **pastas de trabalho** fornecem informações resumidas sobre os indicadores de ameaça importados para o Azure Sentinel e quaisquer alertas gerados por meio de regras de análise que correspondam aos indicadores de ameaça.

- As consultas de **busca** permitem que os investigadores de segurança usem indicadores de ameaça no contexto de cenários de busca comuns.

- Os **notebooks** podem usar indicadores de ameaça quando você investiga anomalias e busca por comportamentos mal-intencionados.

Você pode transmitir indicadores de ameaça para o Azure Sentinel usando um dos produtos de dica (plataforma de inteligência contra ameaças) integrados listados na próxima seção, conectando-se a servidores de TÁXIs ou usando a integração direta com a [API do Microsoft Graph Security tiIndicators](/graph/api/resources/tiindicator).

## <a name="integrated-threat-intelligence-platform-products"></a>Produtos integrados da plataforma de inteligência contra ameaças

- [Plataforma de inteligência contra ameaças de código-fonte aberto MISP](https://www.misp-project.org/)
    
    Para obter um script de exemplo que fornece aos clientes instâncias de MISP para migrar indicadores de ameaça para a API de segurança Microsoft Graph, consulte o [script MISP to Microsoft Graph Security](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP).

- [ThreatStream de anomaliai](https://www.anomali.com/products/threatstream)

    Para baixar o integrador e as extensões do ThreatStream e as instruções para conectar o ThreatStream Intelligence à API de segurança do Microsoft Graph, consulte a página de [downloads do ThreatStream](https://ui.threatstream.com/downloads) .

- [Palo Alto Networks MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld)
    
    Para obter instruções guiadas, consulte [enviando IOCs para a API de segurança de Microsoft Graph usando MineMeld](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540).

- [Plataforma ThreatConnect](https://threatconnect.com/solution/)

    Para obter informações, consulte [integrações do ThreatConnect](https://threatconnect.com/integrations/) e procure Microsoft Graph API de segurança na página.

- [Plataforma EclecticIQ](https://www.eclecticiq.com/solutions)

- [Plataforma ThreatQ Threat Intelligence](https://www.threatq.com/)

    Para obter informações e instruções guiadas, consulte [conector do Microsoft Sentinel para integração do ThreatQ](https://appsource.microsoft.com/product/web-apps/threatquotientinc1595345895602.microsoft-sentinel-connector-threatq?src=health&tab=Overview).

## <a name="connect-azure-sentinel-to-your-threat-intelligence-platform"></a>Conecte o Azure Sentinel à sua plataforma de inteligência contra ameaças

### <a name="prerequisites"></a>Pré-requisitos  

- Função do Azure AD de administrador global ou administrador de segurança para conceder permissões ao seu produto TIP ou aplicativo personalizado que usa a integração direta com a API do Microsoft Graph Security tiIndicators.

- Permissões de leitura e gravação para o espaço de trabalho do Azure Sentinel para armazenar seus indicadores de ameaça.

### <a name="instructions"></a>Instruções

1. [Registre um aplicativo](/graph/auth-v2-service#1-register-your-app) no Azure Active Directory para obter uma ID do aplicativo, segredo do aplicativo e Azure Active Directory ID do locatário. Você precisa desses valores para quando configura seu produto ou aplicativo da TIP integrado que usa a integração direta com a API do Microsoft Graph Security tiIndicators.

2. [Configurar permissões de API](/graph/auth-v2-service#2-configure-permissions-for-microsoft-graph) para o aplicativo registrado: Adicione a permissão de aplicativo Microsoft Graph **ThreatIndicators. ReadWrite. OwnedBy** ao seu aplicativo registrado.

3. Peça ao administrador de locatários do Azure Active Directory para conceder consentimento de administrador para o aplicativo registrado para sua organização. No portal do Azure: **Azure Active Directory**  >  **registros de aplicativo**  >  **\<_app name_>**  >  **exibir permissões**  >  **de API conceder consentimento de administrador \<_tenant name_> para** o.

4. Configure seu produto ou aplicativo TIP que usa a integração direta com a API do Microsoft Graph Security tiIndicators para enviar indicadores para o Azure Sentinel, especificando o seguinte:
    
    a. Os valores para a ID, o segredo e a ID do locatário do aplicativo registrado.
    
    b. Para o produto de destino, especifique Azure Sentinel.
    
    c. Para a ação, especifique alerta.

5. Na portal do Azure, navegue até conectores de dados **do Azure Sentinel**  >   e selecione o conector de **plataformas de inteligência contra ameaças (versão prévia)** .

6. Selecione a **página abrir conector** e **Conecte-se**.

7. Para exibir os indicadores de ameaça importados para o Azure Sentinel, navegue até **Azure Sentinel-logs**  >  **SecurityInsights** e, em seguida, expanda **ThreatIntelligenceIndicator**.

## <a name="connect-azure-sentinel-to-taxii-servers"></a>Conectar o Azure Sentinel a servidores TÁXIi

### <a name="prerequisites"></a>Pré-requisitos

- Permissões de leitura e gravação para o espaço de trabalho do Azure Sentinel para armazenar seus indicadores de ameaça.

- URI do servidor de TÁXIi 2,0 e ID da coleção.

### <a name="instructions"></a>Instruções

1. Na portal do Azure, navegue até conectores de dados **do Azure Sentinel**  >   e selecione o conector **Threat Intelligence-táxii (versão prévia)** .

2. Clique em **Abrir página do conector**.

3. Especifique as informações necessárias e opcionais nas caixas de texto.

4. Selecione **Adicionar** para habilitar a conexão com o servidor táxii 2,0.

5. Se você tiver servidores adicionais de TÁXIi 2,0: Repita as etapas 3 e 4.

6. Para exibir os indicadores de ameaça importados para o Azure Sentinel, navegue até **Azure Sentinel-logs**  >  **SecurityInsights** e, em seguida, expanda **ThreatIntelligenceIndicator**.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar seu provedor de inteligência contra ameaças ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os artigos a seguir.

- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](./tutorial-detect-threats-built-in.md).
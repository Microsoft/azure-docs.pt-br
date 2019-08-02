---
title: Integrado na visualização do Azure Sentinela | Microsoft Docs
description: Saiba como coletar dados no Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/27/2019
ms.author: rkarlin
ms.openlocfilehash: c9f2f011acb9d815202aa6c6a38ed364ffb0f9cd
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67619649"
---
# <a name="on-board-azure-sentinel-preview"></a>Visualização de sentinela do Azure integrado

> [!IMPORTANT]
> No momento, o Azure Sentinel está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este guia de início rápido, você aprenderá como Sentinel integrados do Azure. 

A Sentinela integrados do Azure, primeiro você precisa habilitar Sentinel do Azure e, em seguida, conecte-se suas fontes de dados. Sentinela do Azure vem com um número de conectores para soluções da Microsoft, disponíveis para fora a caixa e fornecendo integração em tempo real, incluindo soluções de proteção de ameaças da Microsoft, Microsoft 365 fontes, incluindo o Office 365, Azure AD, o Azure ATP, e Microsoft Cloud App Security e muito mais. Além disso, existem conectores internos no ecossistema de segurança mais amplo para soluções que não são da Microsoft. Você também pode usar o formato comum de eventos, Syslog ou API REST para se conectar suas fontes de dados com o Azure Sentinel.  

Depois de se conectar suas fontes de dados, escolha de uma galeria de painéis com habilidade criados que surgir insights com base nos seus dados. Esses painéis podem ser facilmente personalizados para suas necessidades.


## <a name="global-prerequisites"></a>Pré-requisitos globais

- Assinatura ativa do Azure, se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Espaço de trabalho de análise de logs. Saiba como [criar um espaço de trabalho do Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md)

-  Para habilitar o Azure Sentinel, você precisa de permissões de Colaborador à assinatura na qual reside o espaço de trabalho do Azure Sentinel. 
- Para usar o Azure Sentinel, você precisa de permissões de Colaborador ou leitor no grupo de recursos que o espaço de trabalho pertence
- Permissões adicionais podem ser necessárias para se conectar a fontes de dados específicas
 
## Habilitar Sentinela do Azure <a name="enable"></a>

1. Entra no portal do Azure.
2. Certifique-se de que a assinatura na qual o Azure Sentinel é criado, está selecionada. 
3. Procure Sentinela do Azure. 
   ![search](./media/quickstart-onboard/search-product.png)

1. Clique em **+Adicionar**.
1. Selecione o espaço de trabalho que você deseja usar ou criar um novo. Você pode executar o Azure Sentinel em mais de um espaço de trabalho, mas os dados são isolados para um único espaço de trabalho.

   ![pequisa](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - **Local do espaço de trabalho** é importante entender que todos os dados que você transmitir para o Azure Sentinel são armazenados na localização geográfica do espaço de trabalho que você selecionou.  
   > - Espaços de trabalho padrão criados pela Central de segurança do Azure não serão exibidos na lista; Você não pode instalar o Azure Sentinel neles.
   > - Sentinela do Azure pode executar em espaços de trabalho que são implantados em qualquer uma das seguintes regiões:  Sudeste da Austrália, Central do Canadá, Índia Central, Leste dos EUA, Leste dos EUA 2 EUAP (Canário), Leste do Japão, Sudeste Asiático, UK Sul, Europa Ocidental, oeste dos EUA 2.

6. Clique em **adicionar Azure sentinela**.
  

## <a name="connect-data-sources"></a>Conectar fontes de dados

Sentinela do Azure cria a conexão aos serviços e aplicativos ao se conectar ao serviço e encaminhar os eventos e logs para Azure Sentinel. Para máquinas virtuais e máquinas, você pode instalar o agente do Azure Sentinel que coleta os logs e os encaminha para o Azure Sentinel. Para Firewalls e proxies, Sentinel Azure utiliza um servidor Syslog do Linux. O agente está instalado nele e de que o agente de coleta de arquivos de log e encaminha-as para o Azure Sentinel. 
 
1. Clique em **coleta de dados**.
2. Há um bloco para cada fonte de dados que você pode se conectar.<br>
Por exemplo, clique em **Azure Active Directory**. Se você se conectar a esta fonte de dados, transmite todos os logs do Azure AD ao Azure Sentinel. Você pode selecionar que tipo de logs que você deseja para obter - logs de entrada e/ou logs de auditoria. <br>
Na parte inferior, Sentinel do Azure fornece recomendações para os quais painéis você deve instalar para cada conector para que você pode imediatamente obter interessante o insights em seus dados. <br> Siga as instruções de instalação ou [consulte o guia de conexão relevante](connect-data-sources.md) para obter mais informações. Para obter informações sobre conectores de dados, consulte [serviços do Microsoft Connect](connect-data-sources.md).

Depois de seus dados de fontes estão conectadas, seus dados inicia o fluxo no Azure Sentinel e está prontos para você começar a trabalhar com. Você pode exibir os logs na [painéis internos](quickstart-get-visibility.md) e comece a criar consultas no Log Analytics para [investigar os dados](tutorial-investigate-cases.md).



## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu sobre como se conectar a fontes de dados para o Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [Obtenha visibilidade sobre seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Introdução ao [detecção de ameaças com o Azure Sentinel](tutorial-detect-threats.md).
- Stream de dados do [appliances formato comum de erro](connect-common-event-format.md) em Sentinel do Azure.

---
title: Conecte suas soluções de proteção contra identidades e defesa do Agari phishing ao Azure Sentinel | Microsoft Docs
description: Saiba como usar o conector de proteção contra marcas e defesa contra phishing do Agari para efetuar pull de seus logs no Azure Sentinel. Exiba dados do Agari em pastas de trabalho, crie alertas e melhore a investigação.
services: sentinel
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
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: a60a0291d6669b2a9115dffa8e0d4d63fae4a440
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101724431"
---
# <a name="connect-your-agari-phishing-defense-and-brand-protection-solutions-to-azure-sentinel"></a>Conecte suas soluções de proteção de identidade e defesa do Agari phishing ao Azure Sentinel

> [!IMPORTANT]
> O conector de proteção contra marcas e defesa contra o Agari phishing está atualmente em versão **prévia**. Consulte os [termos de uso suplementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.

O conector do Agari phishing Defense and Branding Protection permite que você conecte facilmente seus logs de proteção de marca e de defesa contra phishing ao Azure Sentinel, para que você possa exibir os dados em pastas de trabalho, consultá-los para criar alertas personalizados e incorporá-los para melhorar a investigação. As soluções do Agari se integram com o Azure Sentinel usando Azure Functions e a API REST.

Além disso, a proteção de marca e os clientes de resposta de phishing podem aproveitar o compartilhamento de inteligência contra ameaças por meio do API do Graph de segurança.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

Os itens a seguir são necessários para conectar a defesa contra phishing e as soluções de proteção à marca do Agari ao Azure Sentinel:

- Permissões de leitura e gravação no espaço de trabalho do Azure Sentinel.

- Permissões de leitura para chaves compartilhadas para o espaço de trabalho. [Saiba mais sobre as chaves do espaço de trabalho](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

- Permissões de leitura e gravação no Azure Functions para criar um Aplicativo de funções. [Saiba mais sobre Azure Functions](../azure-functions/index.yml).

- Verifique se você tem a **ID do cliente** e **as chaves secretas** do Agari (idênticas em todas as soluções de Agari). Consulte o [site de desenvolvedores do Agari](https://developers.agari.com/agari-platform/docs/quick-start) para obter instruções.

## <a name="configure-and-connect-agari-solutions"></a>Configurar e conectar soluções Agari 

As soluções Agari podem integrar e exportar logs diretamente para o Azure Sentinel usando uma Aplicativo de funções do Azure.

> [!NOTE]
> Esse conector usa Azure Functions para se conectar às soluções do Agari para efetuar pull de seus logs para o Azure Sentinel. Isso pode resultar em custos adicionais de ingestão de dados. Confira a página de [preços Azure Functions](https://azure.microsoft.com/pricing/details/functions/) para obter detalhes.

1. **Colete suas credenciais de API do Agari:** 

    Verifique se você tem a **ID do cliente** e **as chaves secretas** do Agari. As instruções podem ser encontradas no [site de desenvolvedores do Agari](https://developers.agari.com/agari-platform/docs/quick-start#generate-api-credentials).

1. **Adicional Habilite o API do Graph de segurança:** 

    O Aplicativo de funções Agari permite que você compartilhe inteligência contra ameaças com o Azure Sentinel por meio do API do Graph de segurança. Para usar esse recurso, você precisará habilitar o [conector de plataformas de inteligência contra ameaças Sentinel](connect-threat-intelligence.md) e também [registrar um aplicativo](/graph/auth-register-app-v2) no Azure Active Directory.

    Esse processo fornecerá três informações para uso ao implantar o Aplicativo de funções abaixo: a ID do locatário do **grafo**, a ID do **cliente do Graph** e o **segredo do cliente do Graph**.

1. **Implante o conector e o Aplicativo de funções do Azure associado:** 

    1. No portal do Azure Sentinel, selecione **conectores de dados**. Selecione **proteção contra identidade e defesa de phishing do Agari (versão prévia)** e, em seguida, **abra a página do conector**.

    1. Em **configuração**, copie a ID do **espaço de trabalho** do Azure Sentinel e a **chave primária** e cole-as de lado.

    1. Selecione **implantar no Azure**. (Talvez você precise rolar para baixo para localizar o botão.)

    1. A tela de **implantação personalizada** será exibida.

        - Insira a **ID do cliente** do Agari e o **segredo do cliente** (chaves secretas)

        - Insira sua ID do **espaço** de trabalho do Azure Sentinel e a **chave do espaço de trabalho** (chave primária) que você copiou e colocou de lado.

        - Selecione **verdadeiro** ou **falso** para as soluções de Agari para as quais você tem assinaturas ativas.

        - Se você tiver criado um Aplicativo Azure para compartilhar IoCs com o Azure Sentinel usando o API do Graph de segurança, selecione **verdadeiro** para **habilitar o compartilhamento de grafo de segurança** e insira a ID do locatário do **grafo**, a ID do **cliente do grafo** e o segredo do **cliente do Graph**.

    1. Selecione **Examinar + criar**. Quando a validação for concluída, clique em **criar**.

1. **Atribua as permissões necessárias ao seu Aplicativo de funções:**

    O conector do Agari usa uma variável de ambiente para armazenar carimbos de data/hora de acesso ao log. Para que o aplicativo grave nessa variável, as permissões devem ser atribuídas à identidade atribuída pelo sistema.

    1. Na portal do Azure, navegue até **aplicativo de funções**.

    1. Na folha **aplicativo de funções** , selecione o aplicativo de funções na lista e, em seguida, selecione **identidade** em **configurações** no menu de navegação do aplicativo de funções.

    1. Na guia **atribuído pelo sistema** , defina o **status** como **ativado**. 

    1. Selecione **salvar** e um botão **atribuições de função do Azure** será exibido. Clique.

    1. Na tela **atribuições de função do Azure** , selecione **Adicionar atribuição de função**. Defina **escopo** como **assinatura**, selecione sua assinatura na lista suspensa **assinatura** e defina **função** como proprietário dos dados de **configuração do aplicativo**. 

    1. Clique em **Salvar**.

## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem nos **logs** em *CustomLogs*, nas seguintes tabelas: 

- `agari_apdtc_log_CL`
- `agari_apdpolicy_log_CL`
- `agari_bpalerts_log_CL`

Para consultar os dados das soluções Agari, insira um dos nomes de tabela acima na janela de consulta.

Consulte a guia **próximas etapas** na página conector para ver algumas consultas de exemplo úteis.

## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics. 

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar o Agari phishing Defense e as soluções de proteção de marca à sentinela do Azure. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade de seus dados e ameaças em potencial](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.
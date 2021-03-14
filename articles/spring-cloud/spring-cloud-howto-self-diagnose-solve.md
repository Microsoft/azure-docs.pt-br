---
title: Como diagnosticar e solucionar problemas automaticamente no Azure Spring Cloud
description: Saiba como diagnosticar automaticamente e resolver problemas no Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 05/29/2020
ms.custom: devx-track-java
ms.openlocfilehash: c262ba87698c04da69728f7a370040c0679ec44e
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122860"
---
# <a name="how-to-self-diagnose-and-solve-problems-in-azure-spring-cloud"></a>Como diagnosticar e solucionar problemas automaticamente no Azure Spring Cloud

**Este artigo aplica-se a:** ✔️ Java ✔️ C#

O Azure Spring Cloud Diagnostics é uma experiência interativa para solucionar problemas de seu aplicativo sem configuração. O diagnóstico de nuvem Spring do Azure identifica problemas e orienta você a informações que ajudam a solucionar problemas e a solucioná-los.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, você precisará:

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* Uma instância de serviço do Azure Spring Cloud implantada. Siga nosso [início rápido sobre como implantar um aplicativo por meio da CLI do Azure](spring-cloud-quickstart.md) para obter uma introdução.
* Pelo menos um aplicativo já criado na instância de serviço.

## <a name="navigate-to-the-diagnostics-page"></a>Navegue até a página de diagnóstico
1. Entre no portal do Azure.
2. Vá até a página **Visão geral** do Azure Spring Cloud.
3. Selecione **diagnosticar e resolver problemas** no painel de navegação esquerdo.

 ![Diagnóstico, caixa de diálogo de resolução](media/spring-cloud-diagnose/diagnose-solve-dialog.png)

 ## <a name="search-logged-issues"></a>Pesquisar problemas registrados
Para encontrar um problema, você pode pesquisar digitando uma palavra-chave ou clicar em grupo de soluções para explorar tudo nessa categoria.

 ![Problemas de pesquisa](media/spring-cloud-diagnose/search-detectors.png)

A seleção de **verificação de integridade do servidor de configuração**, status de integridade do servidor de **configuração** ou histórico de **atualização do servidor** de configuração exibirá vários resultados.

![Opções de problemas](media/spring-cloud-diagnose/detectors-options.png)

Localize o detector de destino e clique nele para executar. Um resumo do diagnóstico será mostrado depois que você executar o detector. Você pode selecionar **Exibir relatório completo** para verificar os detalhes de diagnóstico ou clicar no botão de **menu Mostrar bloco** para voltar à lista de detectores.

 ![Diagnóstico de resumo](media/spring-cloud-diagnose/summary-diagnostics.png)

Na página de detalhes de diagnóstico, você pode alterar o intervalo de tempo de diagnóstico com o controlador no canto superior direito. Para ver mais métricas ou logs, alterne cada diagnóstico. Pode haver um atraso de 15 minutos para métricas e logs.

 ![Detalhes do diagnóstico](media/spring-cloud-diagnose/diagnostics-details.png)

Alguns resultados contêm documentação relacionada.

 ![Detalhes relacionados](media/spring-cloud-diagnose/related-details.png)

## <a name="next-steps"></a>Próximas etapas
* [Monitorar recursos do Spring Cloud usando alertas e grupos de ações](spring-cloud-tutorial-alerts-action-groups.md)
* [Controles de segurança para o serviço do Azure Spring Cloud](spring-cloud-concept-security-controls.md)
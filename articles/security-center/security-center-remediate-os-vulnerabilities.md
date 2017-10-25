---
title: "Corrigir as vulnerabilidades do sistema operacional na Central de Segurança do Azure | Microsoft Docs"
description: "Este documento mostra como implementar a recomendação da Central de Segurança do Azure para **Corrigir as vulnerabilidades do sistema operacional**."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 991d41f5-1d17-468d-a66d-83ec1308ab79
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: terrylan
ms.openlocfilehash: 39879c22278a55f841e294cda5a89bec2bdf6988
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="remediate-os-vulnerabilities-in-azure-security-center"></a>Corrigir as vulnerabilidades do sistema operacional na Central de Segurança do Azure
A Central de Segurança do Azure analisa diariamente o SO (sistema operacional) de suas VMs (máquinas virtuais) e computadores para verificar a existência de configuração que possa tornar as VMs e computadores mais vulneráveis a ataques. A Central de Segurança recomenda que você resolva as vulnerabilidades quando sua configuração de sistema operacional não coincide com as regras de configuração recomendada e sugere alterações de configuração para resolver essas vulnerabilidades.

> [!NOTE]
> Para obter mais informações sobre configurações específicas que estão sendo monitoradas, consulte a [lista de regras de configuração recomendadas](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335).
>
>

## <a name="implement-the-recommendation"></a>Implementar a recomendação
A correção das vulnerabilidades do sistema operacional é apresentada como uma recomendação na Central de Segurança. Essa recomendação será exibida em **Recomendações** e em **Computação**.

Neste exemplo, examinaremos a recomendação **Corrigir vulnerabilidades do SO (pela Microsoft)** em **Computação**.
1. Selecione **Computação** no menu principal da Central de Segurança.

   ![Corrigir as vulnerabilidades do sistema operacional][1]

2. Em **Computação**, selecione **Corrigir vulnerabilidades do SO (pela Microsoft)**. O painel **Incompatibilidade de Vulnerabilidades do Sistema Operacional (da Microsoft)** é aberto.

   ![Corrigir as vulnerabilidades do sistema operacional][2]

  A parte superior do painel fornece:

  - O número total de regras, por gravidade, em relação as quais a configuração do sistema operacional falhou em suas VMs e computadores.
  - O número total de regras, por tipo, em relação as quais a configuração do sistema operacional falhou em suas VMs e computadores.
  - O número total de regras com falha, por configurações de sistema operacional Windows e por configurações de sistema operacional Linux.

  A parte inferior do painel lista todas as regras com falha em suas VMs e computadores e a gravidade da atualização ausente. A lista inclui:

  - **CCEID**: identificador exclusivo CCE para a regra. A Central de Segurança usa a Common Configuration Enumeration (CCE) para atribuir identificadores exclusivos para as regras de configuração.
  - **NOME**: nome da regra com falha
  - **TIPO DE REGRA**: chave do Registro, política de segurança ou política de auditoria
  - **NÃO. DE VMs E COMPUTADORES**: número total de VMs e computadores aos quais a falha se aplica
  - **GRAVIDADE DA REGRA**: valor de gravidade CCE, sendo: crítico, importante ou aviso
  - **ESTADO**: o estado atual da recomendação:

    - **Aberta**: a recomendação ainda não foi resolvida
    - **Em Andamento**: a recomendação está sendo atualmente aplicada aos recursos e não é necessário que você realize nenhuma ação
    - **Resolvido**: a recomendação já foi concluída. (Quando o problema for resolvido, a entrada será esmaecida)

3. Selecione uma regra com falha na lista para exibir detalhes.

   ![Regras de configuração que falharam][3]

  As informações a seguir são fornecidas nessa folha:

  - NOME -- o nome da regra
  - CCIED -- identificador exclusivo da CCE para a regra
  - Versão do SO – versão do sistema operacional da VM ou do computador
  - GRAVIDADE DA REGRA – valor de gravidade CCE, sendo: crítico, importante ou aviso
  - DESCRIÇÃO COMPLETA – a descrição da regra
  - VULNERABILIDADE -- explicação da vulnerabilidade ou do risco se a regra não for aplicada
  - IMPACTO POTENCIAL – o impacto nos negócios quando a regra é aplicada
  - CONTRAMEDIDA – etapas de correção
  - VALOR ESPERADO -- o valor esperado quando a Central de Segurança analisa a configuração do SO da VM em relação à regra
  - VALOR REAL -- o valor retornado após a análise da configuração do SO da VM em relação à regra
  - OPERAÇÃO DA REGRA -- a operação da regra usada pela Central de Segurança durante a análise da configuração do SO da VM em relação à regra

4. Selecione o ícone **Pesquisar** na faixa de opções superior. A Pesquisa é aberta, listando espaços de trabalho que têm VMs e computadores com a vulnerabilidade de sistema operacional selecionada. Esta folha de seleção do espaço de trabalho é mostrada apenas se a regra selecionada se aplicar a várias VMs que estejam conectadas aos diferentes espaços de trabalho.

  ![Espaços de trabalho listados][4]

5. Selecione um espaço de trabalho. Uma consulta de pesquisa do Log Analytics é aberta, filtrada para o espaço de trabalho com a vulnerabilidade do sistema operacional.

  ![Espaço de trabalho com a vulnerabilidade do sistema operacional][5]

6. Selecione um computador da lista para obter mais informações. Outro resultado da pesquisa é aberto com informações filtradas para esse computador.

  ![Filtrado para esse computador][6]

## <a name="next-steps"></a>Próximas etapas
Este artigo mostrou como implementar a recomendação da Central de Segurança para "Corrigir vulnerabilidades do sistema operacional". Você pode conferir o conjunto de regras de configuração [aqui](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). A Central de Segurança usa a Common Configuration Enumeration (CCE) para atribuir identificadores exclusivos para as regras de configuração. Visite o site da [CCE](https://nvd.nist.gov/cce/index.cfm) para obter mais informações.

Para saber mais sobre a Central de Segurança, confira os seguintes recursos:

* [Plataformas com suporte na Central de Segurança do Azure](security-center-os-coverage.md) – fornece uma lista de VMs Windows e Linux com suporte.
* [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md): saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
* [Gerenciar as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md): saiba como as recomendações ajudam a proteger os recursos do Azure.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md): saiba como monitorar a integridade dos recursos do Azure.
* [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md): aprenda a gerenciar e responder aos alertas de segurança.
* [Monitorando as soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md) – saiba como monitorar o status de integridade de suas soluções de parceiros.
* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md): encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/): encontre postagens no blog sobre conformidade e segurança do Azure.

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/compute-blade.png
[2]:./media/security-center-remediate-os-vulnerabilities/os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png
[4]: ./media/security-center-remediate-os-vulnerabilities/search.png
[5]: ./media/security-center-remediate-os-vulnerabilities/log-search.png
[6]: ./media/security-center-remediate-os-vulnerabilities/search-results.png

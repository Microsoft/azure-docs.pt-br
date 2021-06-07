---
title: 'Tutorial: Verificações de conformidade regulatória – Central de Segurança do Azure'
description: 'Tutorial: Saiba como melhorar a conformidade regulatória usando a Central de Segurança do Azure.'
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5f50c4dc-ea42-418d-9ea8-158ffeb93706
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2021
ms.author: memildin
ms.openlocfilehash: 284a7f532ed918397fe1cfcf3458bbc6fb0bdd32
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739000"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Tutorial: Melhorar sua conformidade regulatória

A Central de Segurança do Azure ajuda a simplificar o processo para atender aos requisitos de conformidade regulatória, usando o **painel de conformidade regulatória**. 

A Central de Segurança avalia continuamente o seu ambiente de nuvem híbrida para analisar os fatores de risco de acordo com os controles e as práticas recomendadas nos padrões aplicados às suas assinaturas. O painel reflete o status de sua conformidade com esses padrões. 

Quando você habilita a Central de Segurança em uma assinatura do Azure, o [Azure Security Benchmark](https://docs.microsoft.com/security/benchmark/azure/introduction) é automaticamente atribuído a ela. Esse parâmetro de comparação amplamente respeitado se baseia nos controles do [CIS (Center for Internet Security)](https://www.cisecurity.org/benchmark/azure/) e do [NIST (National Institute of Standards and Technology)](https://www.nist.gov/) com foco na segurança centrada na nuvem.

O painel de conformidade regulatória mostra o status de todas as avaliações no seu ambiente para os seus padrões e regulamentos escolhidos. Conforme você tomar decisões com base nas recomendações e reduzir os fatores de risco em seu ambiente, sua postura de conformidade melhorará.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Avaliar sua conformidade regulatória usando o painel de conformidade regulatória
> * Melhorar sua postura de conformidade tomando decisões com base nas recomendações
> * Configure alertas nas alterações da sua postura de conformidade
> * Exporte os seus dados de conformidade como um fluxo contínuo e como instantâneos semanais

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para acompanhar os recursos abordados neste tutorial:

- O [Azure Defender](azure-defender.md) precisa estar habilitado. Você pode experimentar o Azure Defender gratuitamente por 30 dias.
- Você precisa estar conectado a uma conta que tenha acesso de leitor aos dados de conformidade da política (**Leitor de Segurança** é insuficiente). A função de **Leitor global** da assinatura pode ser usada. No mínimo, você precisará ter as funções **Colaborador de Política de Recursos** e **Administrador de Segurança** atribuídas.

##  <a name="assess-your-regulatory-compliance"></a>Avaliar sua conformidade regulatória

O painel de conformidade regulatória mostra os padrões de conformidade que você selecionou com todos os respectivos requisitos, em que os requisitos compatíveis são mapeados para as avaliações de segurança aplicáveis. O status dessas avaliações reflete sua conformidade com o padrão.

Use o painel de conformidade regulatória como auxílio para concentrar a sua atenção nas lacunas de conformidade com os padrões e regulamentos escolhidos. Essa exibição concentrada também possibilita que você monitore continuamente sua conformidade ao longo do tempo em ambientes dinâmicos híbridos e de nuvem.

1. No menu da Central de Segurança, selecione **Conformidade regulatória**.

    Na parte superior da tela, você verá um painel com uma visão geral de seu status de conformidade com o conjunto de regulamentos de conformidade compatíveis. Você verá a sua pontuação geral de conformidade e o número de avaliações aprovadas vs. reprovadas associadas a cada padrão.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Painel de conformidade regulatória" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

1. Selecione uma guia para um padrão de conformidade relevante para você (1). Você verá a quais assinaturas o padrão é aplicado (2) e a lista de todos os controles para esse padrão (3). Para os controles aplicáveis, você poderá ver os detalhes das avaliações aprovadas e reprovadas associadas a esse controle (4), bem como o número de recursos afetados (5). Alguns controles estão esmaecidos. Esses controles não têm nenhuma avaliação da Central de Segurança associada a eles. Verifique os requisitos e avalie-os no seu ambiente. Alguns deles podem ser relacionados ao processo e não técnicos.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="Explorando os detalhes da conformidade com um padrão específico":::

1. Para gerar um relatório em PDF com um resumo do seu status de conformidade atual para um padrão específico, selecione **Baixar o relatório**.

    O relatório fornece um resumo de alto nível do status de conformidade para o padrão selecionado tendo como base os dados das avaliações da Central de Segurança. O relatório é organizado de acordo com os controles desse padrão específico. O relatório pode ser compartilhado com stakeholders relevantes e pode servir para fornecer evidências aos auditores internos e externos.

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="Baixar relatório de conformidade":::

## <a name="improve-your-compliance-posture"></a>Melhorar sua postura de conformidade

Usando as informações do painel de conformidade regulatória, aprimore a sua postura de conformidade resolvendo as recomendações diretamente no painel.

1.  Selecione qualquer uma das avaliações reprovadas mostradas no painel para exibir os detalhes dessa recomendação. Cada recomendação inclui um conjunto de etapas de correção para resolver o problema.

1.  Selecione um recurso específico para ver mais detalhes e resolva a recomendação desse recurso. <br>Por exemplo, no padrão **Azure CIS 1.1.0**, selecione a recomendação **A criptografia de disco deve ser aplicada em máquinas virtuais**.

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="A seleção de uma recomendação de um padrão leva diretamente para a página de detalhes da recomendação":::

1. Neste exemplo, ao selecionar **Executar ação** na página de detalhes da recomendação, você chegará nas páginas da Máquina Virtual do Azure do portal do Azure, em que é possível habilitar a criptografia na guia **Segurança**:

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="O botão Executar ação na página de detalhes da recomendação leva às opções de correção":::

    Para obter mais informações sobre como aplicar recomendações, confira [Implementando as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md).

1.  Depois de tomar medidas para resolver as recomendações, você verá o resultado no relatório do painel de conformidade, devido ao aprimoramento da sua pontuação de conformidade.

    > [!NOTE]
    > As avaliações são executadas aproximadamente a cada 12 horas e, portanto, você verá o impacto sobre seus dados de conformidade somente após a próxima execução da avaliação relevante.


## <a name="export-your-compliance-status-data"></a>Exportar os seus dados de status de conformidade

Se você quiser acompanhar o seu status de conformidade com outras ferramentas de monitoramento no seu ambiente, a Central de Segurança incluirá um mecanismo de exportação para fazer isso de modo simples. Configure a **exportação contínua** para enviar os dados selecionados para um Hub de Eventos do Azure ou um workspace do Log Analytics.

Use os dados de exportação contínua para um Hub de Eventos do Azure ou um workspace do Log Analytics:

- Exporte todos os dados de conformidade regulatória em um **fluxo contínuo**:

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-stream.png" alt-text="Exportar continuamente um fluxo de dados de conformidade regulatória" lightbox="media/security-center-compliance-dashboard/export-compliance-data-stream.png":::

- Exportar **instantâneos semanais** dos seus dados de conformidade regulatória:

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png" alt-text="Exportar continuamente um instantâneo semanal dos dados de conformidade regulatória" lightbox="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png":::

Você também pode exportar um **relatório PDF/CSV** dos seus dados de conformidade diretamente do painel de conformidade regulatória:

:::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-report.png" alt-text="Exportar os seus dados de conformidade regulatória como um relatório PDF ou CSV" lightbox="media/security-center-compliance-dashboard/export-compliance-data-report.png":::

Saiba mais em [Exportar continuamente dados da Central de Segurança](continuous-export.md).


## <a name="run-workflow-automations-when-there-are-changes-to-your-compliance"></a>Executar automações de fluxo de trabalho quando houver alterações na conformidade

O recurso de automação do fluxo de trabalho da Central de Segurança pode disparar Aplicativos Lógicos sempre que uma das suas avaliações de conformidade regulatória mudar de estado.

Por exemplo, talvez você queira que a Central de Segurança envie por email um usuário específico quando uma avaliação de conformidade falhar. Você precisará criar primeiro o aplicativo lógico (usando os [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md)) e, depois, configurar o gatilho em uma nova automação de fluxo de trabalho, conforme explicado em [Automatizar as respostas para os gatilhos da Central de Segurança](workflow-automation.md).

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Como usar as avaliações de conformidade regulatória para disparar uma automação de fluxo de trabalho" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::




## <a name="faq---regulatory-compliance-dashboard"></a>Perguntas frequentes – Painel de conformidade regulatória

- [Quais padrões têm suporte no painel de conformidade?](#what-standards-are-supported-in-the-compliance-dashboard)
- [Por que alguns controles estão esmaecidos?](#why-do-some-controls-appear-grayed-out)
- [Como fazer para remover um padrão interno, como PCI-DSS, ISO 27001 ou SOC2 TSP do painel?](#how-can-i-remove-a-built-in-standard-like-pci-dss-iso-27001-or-soc2-tsp-from-the-dashboard)
- [Fiz a alteração sugerida com base na recomendação, mas ela não está sendo refletida no painel](#i-made-the-suggested-changed-based-on-the-recommendation-yet-it-isnt-being-reflected-in-the-dashboard)
- [Quais permissões preciso para acessar o painel de conformidade?](#what-permissions-do-i-need-to-access-the-compliance-dashboard)
- [O painel de conformidade regulatória não está carregando para mim](#the-regulatory-compliance-dashboard-isnt-loading-for-me)
- [Como fazer para exibir no meu painel um relatório de controles aprovados e reprovados de acordo com o padrão?](#how-can-i-view-a-report-of-passing-and-failing-controls-per-standard-in-my-dashboard)
- [Como fazer para baixar um relatório com os dados de conformidade em um formato que não seja o PDF?](#how-can-i-download-a-report-with-compliance-data-in-a-format-other-than-pdf)
- [Como fazer para criar exceções para algumas das políticas no painel de conformidade regulatória?](#how-can-i-create-exceptions-for-some-of-the-policies-in-the-regulatory-compliance-dashboard)
- [Em quais planos ou licenças do Azure Defender preciso usar o painel de conformidade regulatória?](#what-azure-defender-plans-or-licenses-do-i-need-to-use-the-regulatory-compliance-dashboard)

### <a name="what-standards-are-supported-in-the-compliance-dashboard"></a>Quais padrões têm suporte no painel de conformidade?
Por padrão, o painel de conformidade regulatória mostra o Azure Security Benchmark. O Azure Security Benchmark é composto pelas diretrizes específicas do Azure criadas pela Microsoft e pelas práticas recomendadas de segurança e conformidade baseadas em estruturas de conformidade comuns. Saiba mais em [Introdução ao Azure Security Benchmark](../security/benchmarks/introduction.md).

Para acompanhar a sua conformidade com qualquer outro padrão, você precisará adicioná-lo explicitamente ao seu painel.
 
Você pode adicionar padrões como o Azure CIS 1.1.0 (novo), NIST SP 800-53 R4, NIST SP 800-171 R2, SWIFT CSP CSCF-v2020, UK Official e UK NHS, HIPAA HITRUST, PBMM Federal do Canadá, ISO 27001, SOC2-TSP e PCI-DSS 3.2.1.  
 
Mais padrões serão adicionados ao painel e incluídos nas informações sobre como [Personalizar o conjunto de padrões no seu painel de conformidade regulatória](update-regulatory-compliance-packages.md).

### <a name="why-do-some-controls-appear-grayed-out"></a>Por que alguns controles estão esmaecidos?
Para cada padrão de conformidade no painel, há uma lista dos controles padrão. Nos controles aplicáveis, você poderá exibir os detalhes das avaliações aprovadas e reprovadas.

Alguns controles estão esmaecidos. Esses controles não têm nenhuma avaliação da Central de Segurança associada a eles. Alguns podem ser relacionados a procedimentos ou processos e, portanto, não podem ser verificados pela Central de Segurança. Alguns ainda não têm políticas ou avaliações automatizadas implementadas, mas terão no futuro. E alguns controles podem ser de responsabilidade da plataforma, conforme explicado em [Responsabilidade compartilhada na nuvem](../security/fundamentals/shared-responsibility.md). 

### <a name="how-can-i-remove-a-built-in-standard-like-pci-dss-iso-27001-or-soc2-tsp-from-the-dashboard"></a>Como fazer para remover um padrão interno, como PCI-DSS, ISO 27001 ou SOC2 TSP do painel? 
Para personalizar o painel de conformidade regulatória e se concentrar apenas nos padrões que se aplicam a você, é possível remover todos os padrões regulatórios exibidos que não sejam relevantes para a sua organização. Para remover um padrão, siga as instruções em [Remover um padrão do seu painel](update-regulatory-compliance-packages.md#remove-a-standard-from-your-dashboard).

### <a name="i-made-the-suggested-changed-based-on-the-recommendation-yet-it-isnt-being-reflected-in-the-dashboard"></a>Fiz a alteração sugerida com base na recomendação, mas ela não está sendo refletida no painel
Depois de tomar medidas para resolver as recomendações, espere 12 horas para ver as mudanças nos dados de conformidade. As avaliações são executadas aproximadamente a cada 12 horas e, portanto, você verá o efeito sobre os seus dados de conformidade somente após a execução das avaliações.
 
### <a name="what-permissions-do-i-need-to-access-the-compliance-dashboard"></a>Quais permissões preciso para acessar o painel de conformidade?
Para exibir dados de conformidade, você também precisa ter pelo menos acesso de **Leitor** aos dados de conformidade de política. Portanto, apenas o Leitor de Segurança não será suficiente. Se você for um leitor global na assinatura, isso também será suficiente.

O conjunto mínimo de funções para acessar o painel e gerenciar padrões é **Colaborador de Política de Recursos** e **Administrador de Segurança**.


### <a name="the-regulatory-compliance-dashboard-isnt-loading-for-me"></a>O painel de conformidade regulatória não está carregando para mim
Para usar o painel de conformidade regulatória, o Azure Defender precisa estar habilitado na Central de Segurança do Azure no nível da assinatura. Se painel não estiver carregando corretamente, tente realizar as seguintes etapas:

1. Limpe o cache do navegador.
1. Tente usar um navegador diferente.
1. Tente abrir o painel em uma localização de rede diferente.


### <a name="how-can-i-view-a-report-of-passing-and-failing-controls-per-standard-in-my-dashboard"></a>Como fazer para exibir no meu painel um relatório de controles aprovados e reprovados de acordo com o padrão?
No painel principal, você vê um relatório de controles aprovados e reprovados para (1) os quatro padrões de conformidade mais baixos no painel. Para ver todo o status dos controles aprovados/reprovados, selecione (2) **Mostrar todos os *x*** (em que x é o número de padrões que você está acompanhando). Um painel de contexto exibe o status de conformidade de cada um dos seus padrões rastreados.

:::image type="content" source="media/security-center-compliance-dashboard/summaries-of-compliance-standards.png" alt-text="Seção de resumo do painel de conformidade regulatória":::


### <a name="how-can-i-download-a-report-with-compliance-data-in-a-format-other-than-pdf"></a>Como fazer para baixar um relatório com os dados de conformidade em um formato que não seja o PDF?
Ao selecionar **Baixar relatório**, selecione o padrão e o formato (PDF ou CSV). O relatório resultante refletirá o conjunto atual de assinaturas que você selecionou no filtro do portal.

- O relatório em PDF mostra um status de resumo do padrão selecionado
- O relatório em CSV fornece resultados detalhados por recurso, pois ele se relaciona com as políticas associadas a cada controle

Atualmente, não há suporte para baixar um relatório para uma política personalizada; somente para os padrões regulatórios fornecidos.


### <a name="how-can-i-create-exceptions-for-some-of-the-policies-in-the-regulatory-compliance-dashboard"></a>Como fazer para criar exceções para algumas das políticas no painel de conformidade regulatória?
Para as políticas criadas na Central de Segurança e incluídas na classificação de segurança, você pode criar isenções para um ou mais recursos diretamente no portal, conforme explicado em [Isenção de recursos e recomendações da classificação de segurança](exempt-resource.md).

Para outras políticas, você pode criar uma isenção diretamente na própria política, seguindo as instruções em [Estrutura de isenção do Azure Policy](../governance/policy/concepts/exemption-structure.md).


### <a name="what-azure-defender-plans-or-licenses-do-i-need-to-use-the-regulatory-compliance-dashboard"></a>Em quais planos ou licenças do Azure Defender preciso usar o painel de conformidade regulatória?
Se você tiver um dos pacotes do Azure Defender habilitado em qualquer um dos seus tipos de recursos do Azure, terá acesso ao Painel de Conformidade Regulatória, com todos os dados, na Central de Segurança.





## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar o painel de conformidade regulatória da Central de Segurança para:

> [!div class="checklist"]
> * Exiba e monitore a sua postura de conformidade com relação aos padrões e aos regulamentos importantes para você.
> * Melhorar seu status de conformidade resolvendo recomendações relevantes e observando a melhoria da pontuação de conformidade.

O painel de conformidade regulatória pode simplificar muito o processo de conformidade e reduzir significativamente o tempo necessário para a coleta de evidências de conformidade para o ambiente híbrido, do Azure e de várias nuvens.

Para saber mais, confira estas páginas relacionadas:

- [Personalize o conjunto de padrões no seu painel de conformidade regulatória](update-regulatory-compliance-packages.md) – Saiba como selecionar quais padrões aparecem no seu painel de conformidade regulatória. 
- [Gerenciar as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md) – Saiba como usar as recomendações da Central de Segurança para ajudar a proteger os seus recursos do Azure.
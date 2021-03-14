---
title: Galeria de pastas de trabalho na central de segurança do Azure
description: Saiba como criar relatórios avançados e interativos de seus dados da central de segurança do Azure com a Galeria de pastas de trabalho integradas do Azure Monitor
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 198702f619e490e8000e4430aab23a7f6bfb6d85
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107306"
---
# <a name="create-rich-interactive-reports-of-security-center-data"></a>Crie relatórios avançados e interativos dos dados da central de segurança

[Azure monitor pastas de trabalho](../azure-monitor/visualize/workbooks-overview.md) fornecem uma tela flexível para análise de dados e a criação de relatórios visuais avançados no portal do Azure. Com elas, você pode explorar várias fontes de dados em todo o Azure e combiná-las em experiências interativas unificadas.

As pastas de trabalho fornecem um conjunto avançado de recursos para visualizar seus dados do Azure. Para obter exemplos detalhados de cada tipo de visualização, consulte os [exemplos de visualizações e documentação](../azure-monitor/visualize/workbooks-text-visualizations.md). 

Na central de segurança do Azure, você pode acessar os relatórios internos para controlar a postura de segurança de sua organização. Você também pode criar relatórios personalizados para exibir uma ampla variedade de dados da central de segurança ou outras fontes de dados com suporte.

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Relatório de Pontuação segura ao longo do tempo":::

## <a name="availability"></a>Disponibilidade

| Aspecto                          | Detalhes                                                                                                                                      |
|---------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------|
| Estado da versão:                  | Versão Prévia<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]                                                       |
| Preço:                        | Gratuita                                                                                                                                         |
| Funções e permissões necessárias: | Para salvar pastas de trabalho, você deve ter pelo menos permissões de colaborador de pasta de trabalho no grupo de recursos de destino                                      |
| Nuvens:                         | ![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim](./media/icons/yes-icon.png) Nacionais/soberanas (US Gov, China Gov, outros Gov) |
|                                 |                                                                                                                                              |

## <a name="workbooks-gallery-in-azure-security-center"></a>Galeria de pastas de trabalho na central de segurança do Azure

Com a funcionalidade integrada de pastas de trabalho do Azure, a central de segurança do Azure torna simples criar seus próprios relatórios interativos e personalizados. A central de segurança também inclui uma galeria de pastas de trabalho com os seguintes relatórios prontos para sua personalização:

- **Proteger a pontuação ao longo do tempo** – controle as pontuações de suas assinaturas e as alterações nas recomendações para seus recursos
- **Atualizações do sistema** – exibir atualizações de sistema ausentes por recursos, sistema operacional, severidade e muito mais
- **Conclusões da avaliação de vulnerabilidade** -exibir as descobertas de verificações de vulnerabilidade dos recursos do Azure

:::image type="content" source="media/custom-dashboards-azure-workbooks/workbooks-gallery-security-center.png" alt-text="Galeria de pastas de trabalho internas na central de segurança do Azure":::

Escolha um dos relatórios fornecidos ou crie o seu próprio.

> [!TIP]
> Use o botão **Editar** para personalizar qualquer um dos relatórios fornecidos para sua satisfação. Quando você terminar de editar, selecione **salvar** e suas alterações serão salvas em uma nova pasta de trabalho.
> 
> :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-supplied-workbooks.png" alt-text="Editando as pastas de trabalho fornecidas para personalizá-las para suas necessidades específicas":::

### <a name="use-the-secure-score-over-time-report"></a>Usar o relatório ' Pontuação segura ao longo do tempo '

Este relatório usa dados de Pontuação segura do seu espaço de trabalho Log Analytics. Esses dados precisam ser exportados da ferramenta de exportação contínua, conforme descrito em [Configurar a exportação contínua nas páginas da central de segurança em portal do Azure](continuous-export.md?tabs=azure-portal).

Ao configurar a exportação contínua, defina a frequência de exportação como atualizações de **streaming** e **instantâneos**.

:::image type="content" source="media/custom-dashboards-azure-workbooks/export-frequency-both.png" alt-text="Para a pasta de trabalho de Pontuação segura ao longo do tempo, você precisará selecionar ambas as opções das configurações de frequência de exportação em sua configuração de exportação contínua":::

> [!NOTE]
> Os instantâneos são exportados semanalmente, portanto, você precisará aguardar pelo menos uma semana para que o primeiro instantâneo seja exportado antes de poder exibir os dados nesse relatório.

> [!TIP]
> Para configurar a exportação contínua em sua organização, use as políticas ' DeployIfNotExist ' fornecidas Azure Policy descritas em [Configurar exportação contínua em escala](continuous-export.md?tabs=azure-policy).

O relatório de Pontuação segura ao longo do tempo tem cinco grafos para os relatórios de assinaturas para os espaços de trabalho selecionados:


|Grafo  |Exemplo  |
|---------|---------|
|**Pontuar tendências para a última semana e mês**<br>Use esta seção para monitorar a pontuação atual e as tendências gerais das pontuações de suas assinaturas.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-1.png" alt-text="Tendências para Pontuação segura no relatório interno":::|
|**Pontuação agregada para todas as assinaturas selecionadas**<br>Passe o mouse sobre qualquer ponto na linha de tendência para ver a pontuação agregada em qualquer data no intervalo de tempo selecionado.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-2.png" alt-text="Pontuação agregada para todas as assinaturas selecionadas":::|
|**Recomendações com os recursos mais iníntegros**<br>Esta tabela ajuda a fazer a triagem das recomendações que tiveram a maioria dos recursos alterados para não íntegro no período selecionado.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-3.png" alt-text="Recomendações com os recursos mais iníntegros":::|
|**Pontuações para controles de segurança específicos**<br>Os controles de segurança da central de segurança são agrupamentos lógicos de recomendações. Este gráfico mostra, em um relance, as pontuações semanais de todos os seus controles.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-4.png" alt-text="Pontuações para os controles de segurança no período de tempo selecionado":::|
|**Alterações de recursos**<br>As recomendações com a maioria dos recursos que alteraram o estado (íntegro, não íntegro ou não aplicável) durante o período selecionado são listadas aqui. Selecione qualquer recomendação na lista para abrir uma nova tabela listando os recursos específicos.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-5.png" alt-text="Recomendações com a maioria dos recursos que alteraram o estado de integridade":::|

### <a name="use-the-system-updates-report"></a>Usar o relatório ' atualizações do sistema '

Esse relatório se baseia na recomendação de segurança "as atualizações do sistema devem ser instaladas em suas máquinas".

O relatório ajuda a identificar computadores com atualizações pendentes.

Você pode exibir a situação para as assinaturas selecionadas de acordo com:

- A lista de recursos com atualizações pendentes
- A lista de atualizações ausentes em seus recursos

:::image type="content" source="media/custom-dashboards-azure-workbooks/system-updates-report.png" alt-text="Relatório de atualizações do sistema da central de segurança com base na recomendação de segurança de atualizações ausentes":::

### <a name="use-the-vulnerability-assessment-findings-report"></a>Usar o relatório ' conclusões da avaliação de vulnerabilidade '

A central de segurança inclui verificadores de vulnerabilidade para seus computadores, contêineres em registros de contêiner e SQL Servers.

Saiba mais sobre como usar esses scanners:

- [Verificar seus computadores com o scanner do VA integrado](deploy-vulnerability-assessment-vm.md)
- [Verificar se há vulnerabilidades em suas imagens de Registro](defender-for-container-registries-usage.md)
- [Verificar se há vulnerabilidades em seus recursos SQL](defender-for-sql-on-machines-vulnerability-assessment.md)

As conclusões de cada um desses scanners são relatadas em recomendações separadas:

- As vulnerabilidades nas suas máquinas virtuais devem ser corrigidas
- As vulnerabilidades nas imagens do Registro de Contêiner do Azure devem ser corrigidas (da plataforma Qualys)
- As descobertas de avaliação de vulnerabilidades nos bancos de dados SQL devem ser corrigidas
- As conclusões da avaliação de vulnerabilidades nos servidores SQL nos computadores devem ser corrigidas

Este relatório reúne essas descobertas e as organiza por gravidade, tipo de recurso e categoria.

:::image type="content" source="media/custom-dashboards-azure-workbooks/vulnerability-assessment-findings-report.png" alt-text="Relatório de conclusões da avaliação de vulnerabilidade da central de segurança":::


## <a name="import-workbooks-from-other-workbook-galleries"></a>Importar pastas de trabalho de outras galerias de pastas de trabalho

Se você tiver criado pastas de trabalho em outros serviços do Azure e quiser movê-las para a Galeria de pastas de trabalho da central de segurança do Azure:

1. Abra a pasta de trabalho de destino.

1. Na barra de ferramentas, selecione **Editar**.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks.png" alt-text="Editando uma pasta de trabalho Azure Monitor":::

1. Na barra de ferramentas, selecione **</>** para inserir o editor avançado.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks-advanced-editor.png" alt-text="Iniciando o editor avançado para obter o código JSON do modelo da Galeria":::

1. Copie o JSON do modelo da Galeria da pasta de trabalho.

1. Abra a Galeria de pastas de trabalho na central de segurança e, na barra de menus, selecione **novo**.
1. Selecione **</>** para inserir o editor avançado.
1. Cole todo o JSON do modelo da galeria.
1. Escolha **Aplicar**.
1. Na barra de ferramentas, selecione **salvar como**.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks-save-as.png" alt-text="Salvando a pasta de trabalho na galeria na central de segurança":::

1. Insira os detalhes necessários para salvar a pasta de trabalho:
   1. Um nome para a pasta de trabalho
   2. A região desejada
   3. Assinatura, grupo de recursos e compartilhamento, conforme apropriado.

Você encontrará sua pasta de trabalho salva na categoria **pastas de trabalho modificadas recentemente** .


## <a name="next-steps"></a>Próximas etapas

Este artigo descreveu a página de pastas de trabalho Azure Monitor integradas da central de segurança com relatórios internos e a opção para criar seus próprios relatórios personalizados e interativos.

- Saiba mais sobre [pastas de trabalho do Azure monitor](../azure-monitor/visualize/workbooks-overview.md)
- Os relatórios internos recebem seus dados das recomendações da central de segurança. Saiba mais sobre as muitas recomendações de segurança em [recomendações de segurança – um guia de referência](recommendations-reference.md)
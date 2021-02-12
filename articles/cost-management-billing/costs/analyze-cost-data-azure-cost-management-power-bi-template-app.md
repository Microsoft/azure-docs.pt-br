---
title: Analisar os custos do Azure com o Aplicativo Power BI
description: Este artigo explica como instalar e usar o aplicativo Power BI do Gerenciamento de Custos do Azure.
author: bandersmsft
ms.author: banders
ms.date: 1/29/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: benshy
ms.openlocfilehash: d3439f5a86c445898d95191a32496b5f0c86be0b
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070150"
---
# <a name="analyze-cost-with-the-azure-cost-management-power-bi-app-for-enterprise-agreements-ea"></a>Analisar os custos com o aplicativo Power BI do Gerenciamento de Custos do Azure para EA (Enterprise Agreements)

Este artigo explica como instalar e usar o aplicativo Power BI do Gerenciamento de Custos do Azure. O aplicativo ajuda a analisar e gerenciar seus custos do Azure no Power BI. Você pode usar o aplicativo para monitorar custos, tendências de uso e identificar opções de otimização de custos para reduzir seus gastos.

No momento, o aplicativo Power BI do Gerenciamento de Custos do Azure só dá suporte aos clientes com um [Contrato Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/).

O aplicativo limita a capacidade de personalização. Caso deseje modificar e estender as exibições, as visualizações e os filtros padrão para personalizá-los de acordo com as suas necessidades, use o [conector do Gerenciamento de Custos do Azure no Power BI Desktop](/power-bi/connect-data/desktop-connect-azure-cost-management). Com o conector do Gerenciamento de Custos do Azure, você pode unir dados adicionais de outras fontes para criar relatórios personalizados e obter exibições holísticas do custo geral dos negócios. O conector também dá suporte aos Contratos de Clientes da Microsoft.

> [!NOTE]
> Os aplicativos de modelo do Power BI não dão suporte ao download do arquivo PBIX.

## <a name="prerequisites"></a>Pré-requisitos

- É necessário ter uma [licença do Power BI Pro](/power-bi/service-self-service-signup-for-power-bi) para instalar e usar o aplicativo.
- Para se conectar aos dados, você precisará usar uma conta de [Administrador Corporativo](../manage/understand-ea-roles.md). Há suporte para a função Administrador Corporativo (somente leitura).

## <a name="installation-steps"></a>Etapas de instalação

Para instalar o aplicativo:

1. Abra o [Aplicativo Power BI do Gerenciamento de Custos do Azure](https://aka.ms/costmgmt/ACMApp).
2. Na página do Power BI AppSource, selecione **Obter agora**.
3. Selecione **Continuar** para concordar com os termos de uso e a política de privacidade.
4. Na caixa **Instalar este aplicativo do Power BI**, selecione **Instalar**.
5. Se necessário, crie um workspace e selecione **Continuar**.
6. Quando a instalação for concluída, a notificação será exibida dizendo que o novo aplicativo está pronto.
7. Selecione **Ir para o aplicativo**.
8. Em **Introdução ao seu novo aplicativo**, em **Conectar seus dados**, selecione **Conectar**.  
  ![Introdução ao seu novo aplicativo – Conectar](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/connect-data2.png)
9. Na caixa de diálogo exibida, digite seu número de registro no EA para **BillingProfileIdOrEnrollmentNumber**. Especifique o número de meses de dados a serem obtidos. Deixe o valor padrão do **Escopo** de **Número de registro** e selecione **Próximo**.  
  ![Inserir informações de registro no EA](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-number.png)  
10. A próxima caixa de diálogo se conecta ao Azure e obtém os dados necessários para recomendações de instâncias reservadas. *Mantenha os valores padrão configurados* e selecione **Entrar**.  
  ![A captura de tela mostra a caixa de diálogo Conectar-se ao Aplicativo de Gerenciamento de Custos do Azure com valores padrão.](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/autofit.png)  
11. A etapa final da instalação se conecta à sua inscrição no EA e requer uma conta [Enterprise Administrator](../manage/understand-ea-roles.md). Selecione **Entrar** para autenticar com seu registro do EA. Essa etapa também inicia uma ação de atualização de dados no Power BI.  
  ![Conectar-se ao registro do EA](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-auth.png)  
    > [!NOTE]
    > O processo de atualização de dados pode levar muito tempo para ser concluído. O comprimento depende do número de meses especificado e da quantidade de dados necessários para a sincronização.
12. Para verificar o status da atualização de dados, selecione a guia **Conjuntos de dados** no workspace. Observe ao lado do carimbo de data/hora atualizado. Se ainda estiver atualizando, você verá um indicador mostrando que a atualização está em andamento.  
  ![Atualizar dados](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/data-refresh2.png)

Após a conclusão da atualização dos dados, selecione o Aplicativo de Gerenciamento de Custos do Azure para exibir os relatórios pré-criados.

## <a name="reports-available-with-the-app"></a>Relatórios disponíveis com o aplicativo

Os relatórios a seguir estão disponíveis com o aplicativo.

**Introdução** – fornece links úteis para a documentação e links para fornecer comentários.

**Visão geral da conta** – o relatório mostra um resumo mensal das informações, incluindo:

- Cobranças contra créditos
- Novas compras
- Encargos do Azure Marketplace
- Excedentes e encargos totais

**Uso por assinaturas e grupos de recursos** – assinatura fornece uma exibição de custo ao longo do tempo e gráficos mostrando o custo por assinatura e grupo de recursos.

**Uso por serviços** – fornece uma exibição ao longo do tempo de uso pelo MeterCategory. Você pode acompanhar seus dados de uso e pesquisar anomalias para entender picos ou quedas de uso.

**Cinco principais drivers de uso** – O relatório mostra um resumo de custos filtrados pelas 5 principais MeterCategory e MeterName correspondentes.

**Uso de AHB do Windows Server** – O relatório mostra o número de máquinas virtuais que têm o Benefício Híbrido do Azure habilitado. Ele também mostra uma contagem de núcleos/vCPUs usados pelas máquinas virtuais.

![Relatório completo dos Benefícios Híbridos do Azure](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report-full.png)

O relatório também identifica VMs do Windows em que o Benefício Híbrido está **habilitado**, mas há _menos que_ 8 vCPUs. Também mostra onde o Benefício Híbrido não está **habilita** com 8 _ou mais_ vCPUs. Essas informações ajudam você a usar totalmente seu benefício híbrido. Aplique o benefício às suas máquinas virtuais mais caras para maximizar suas economias em potencial.

![Benefícios híbridos do Azure – menos de 8 vCPUs e vCPUs não habilitados](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report.png)

**Estorno da RI** – O relatório ajuda a entender onde e quanto de um benefício de RI (instância reservada) é aplicado por região, assinatura, grupo de recursos ou recurso. O relatório usa dados de uso amortizado para mostrar a exibição.

Você pode aplicar um filtro no _chargetype_ para visualizar os dados de subutilização do RI.

Para obter mais informações sobre dados de uso para clientes EA, confira [Obter uso e custos de reserva do Enterprise Agreement](../reservations/understand-reserved-instance-usage-ea.md).

**Economia do RI** – O relatório mostra a economia acumulada pelas reservas para assinatura, grupo de recursos e nível de recurso. Exibe:

- Custo com reserva
- Custo estimado sob demanda se a reserva não se aplicar ao uso
- Economias de custos acumuladas da reserva

 O relatório subtrai qualquer custo de desperdício de reserva subutilizado da economia total. O desperdício não ocorreria sem uma reserva.

Você pode usar os dados de uso amortizados para criar os dados.

<a name="shared-recommendation"></a>
**Cobertura da RI da VM (recomendação compartilhada)** – O relatório é dividido entre o uso da VM sob demanda e o uso da VM da RI no período selecionado. Ele fornece recomendações para compras de RI da VM em um escopo compartilhado.

Para usar o relatório, selecione o filtro de busca detalhada.

![Relatório de cobertura da RI da VM – selecionar busca detalhada](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-drill-down2.png)

Selecione a região que você deseja analisar. Em seguida, selecione o grupo flexibilidade de tamanho da instância e assim por diante.

Para cada nível de detalhamento, os filtros a seguir são aplicados ao relatório:

- Os dados de cobertura à direita são o filtro que mostra quanto é cobrado pelo uso da taxa sob demanda versus quanto é coberto pela reserva.
- As recomendações também são filtradas.

A tabela de recomendações fornece recomendações para a compra de reserva, com base nos tamanhos de VM usados.

Os valores _Tamanho normalizado_ e _Quantidade recomendada normalizada_ ajudam a normalizar a compra no menor tamanho para um grupo de flexibilidade de tamanho de instância. As informações são úteis se você planeja comprar apenas uma reserva para todos os tamanhos no grupo de flexibilidade de tamanho da instância.

![Recomendações de RI](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-recomendations.png)

**Cobertura da RI da VM (recomendação única)** – O relatório é dividido entre o uso da VM sob demanda e o uso da VM da RI no período selecionado. Ele fornece recomendações para compras de RI de VM em um escopo de assinatura.

Para obter detalhes sobre como usar o relatório, confira a seção [Cobertura da RI da VM (recomendação compartilhada) ](#shared-recommendation).

**Compras de RI** – O relatório mostra compras de RI durante o período especificado.

**Pricesheet** – O relatório mostra uma lista detalhada de preços específicos para uma conta de cobrança ou inscrição no EA.

## <a name="troubleshoot-problems"></a>Solução de problemas

Se você estiver tendo problemas com o aplicativo do Power BI, as informações sobre a solução de problemas a seguir poderão ser úteis.

### <a name="error-processing-the-data-in-the-dataset"></a>Erro ao processar os dados no conjunto de dados

Você poderá receber um erro que indica:

```
There was an error when processing the data in the dataset.
Data source error: {"error":{"code":"ModelRefresh_ShortMessage_ProcessingError","pbi.error":{"code":"ModelRefresh_ShortMessage_ProcessingError","parameters":{},"details":[{"code":"Message","detail":{"type":1,"value":"We cannot convert the value \"Required Field: 'Enr...\" to type List."}}],"exceptionCulprit":1}}} Table: <TableName>.
```

Um nome de tabela apareceria em vez de `<TableName>`.

#### <a name="cause"></a>Causa

O valor de **Escopo** padrão de `Enrollment Number` foi alterado na conexão com o Gerenciamento de Custos.

#### <a name="solution"></a>Solução

Reconecte-se ao Gerenciamento de Custos e defina o valor do **Escopo** como `Enrollment Number`. Não insira o número de registro da sua organização, em vez disso, digite `Enrollment Number` exatamente como ele aparece na imagem a seguir.

![Inserir informações de registro no EA](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-number.png)  

### <a name="budgetamount-error"></a>Erro de BudgetAmount

Você poderá receber um erro que indica:

```
Something went wrong
There was an error when processing the data in the dataset.
Please try again later or contact support. If you contact support, please provide these details.
Data source error: The 'budgetAmount' column does not exist in the rowset. Table: Budgets.
```

#### <a name="cause"></a>Causa

Esse erro ocorre devido a um bug com os metadados subjacentes. O problema ocorre porque não há nenhum orçamento disponível no **Gerenciamento de Custos > Orçamento** no portal do Azure. A correção de bug está no processo de ser implantado no Power BI Desktop e no serviço do Power BI. 

#### <a name="solution"></a>Solução

- Até que o bug seja corrigido, resolva o problema adicionando um orçamento de teste no portal do Azure no nível de registro da conta de cobrança/do EA. O orçamento de teste desbloqueia a conexão com o Power BI. Para obter mais informações sobre como criar um orçamento, confira [Tutorial: Criar e gerenciar orçamentos do Azure](tutorial-acm-create-budgets.md).


### <a name="invalid-credentials-for-azureblob-error"></a>Credenciais inválidas para erro de AzureBlob

Você poderá receber um erro que indica:

```
Failed to update data source credentials: The credentials provided for the AzureBlobs source are invalid.
```

#### <a name="cause"></a>Causa

Esse erro ocorrerá se você alterar o método de autenticação da conexão de blob de AutoFitComboMeter.

#### <a name="solution"></a>Solução

1. Conecte-se aos seus dados.
1. Depois de inserir o registro do EA e o número de meses, mantenha o valor padrão **Anônimo** para o Método de autenticação e **Nenhum** para a Configuração de nível de privacidade.  
  ![A captura de tela mostra a caixa de diálogo Conectar-se ao Aplicativo de Gerenciamento de Custos do Azure com os valores Anônimo e Nenhum inseridos.](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/autofit-troubleshoot.png)  
1. Na próxima página, defina **OAuth2** para o Método de autenticação e **Nenhum** para o Nível de privacidade. Em seguida, conecte-se para se autenticar no registro. Essa etapa também inicia uma atualização de dados do Power BI.


## <a name="data-reference"></a>Referência de dados

As informações a seguir resumem os dados disponíveis no aplicativo. Também há links para APIs que fornecem dados detalhados para campos de dados e valores.

| **Referência de tabela** | **Descrição** |
| --- | --- |
| **AutoFitComboMeter** | Dados incluídos no aplicativo para normalizar a recomendação e o uso de RI para o menor tamanho no grupo de famílias de instâncias. |
| [**Resumo de saldo**](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary#response) | Resumo do saldo para Enterprise Agreements. |
| [**Orçamentos**](/rest/api/consumption/budgets/get#definitions) | Detalhes do orçamento para visualizar custos ou uso reais em relação às metas de orçamento existentes. |
| [**Pricesheets**](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet#see-also) | Taxas de medição aplicáveis para o perfil de cobrança fornecido ou a inscrição no EA. |
| [**Encargos de RI**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges#response) | Encargos associados às suas instâncias reservadas nos últimos 24 meses. |
| [**Recomendações de RI (compartilhadas)**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#response) | Recomendações de compra de instâncias reservadas com base em todas as suas tendências de uso de assinatura nos últimos 7 dias. |
| [**Recomendação de RI (única)**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#response-1) | Recomendações de compra de instância reservada com base em suas tendências de uso de assinatura única nos últimos 7 dias. |
| [**Detalhes de uso de RI**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#response) | Detalhes de consumo para suas instâncias reservadas existentes no último mês. |
| [**Resumo de uso de RI**](/rest/api/consumption/reservationssummaries/list) | Porcentagem diária de uso de reserva do Azure. |
| [**Detalhes de uso**](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#usage-details-field-definitions) | Um detalhamento das quantidades consumidas e cobranças estimadas para o perfil de faturamento fornecido na inscrição no EA. |
| [**Detalhes de uso amortizados**](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#usage-details-field-definitions) | Um detalhamento das quantidades consumidas e cobranças estimadas amortizadas para o perfil de faturamento fornecido na inscrição no EA. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como configurar dados, atualizar, compartilhar relatórios e personalizar relatórios adicionais, confira os seguintes artigos:

- [Configurar a atualização agendada](/power-bi/refresh-scheduled-refresh)
- [Compartilhar relatórios e dashboards do Power BI com colegas e outras pessoas](/power-bi/service-share-dashboards)
- [Inscrever você e outras pessoas para relatórios e painéis no serviço do Power BI](/power-bi/service-report-subscribe)
- [Baixar um relatório do serviço do Power BI para Power BI Desktop](/power-bi/service-export-to-pbix)
- [Salvar um relatório no serviço do Power BI e Power BI Desktop](/power-bi/service-report-save)
- [Criar um relatório no serviço do Power BI importando um conjunto de dados](/power-bi/service-report-create-new)
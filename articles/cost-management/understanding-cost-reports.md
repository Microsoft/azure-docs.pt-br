---
title: Noções básicas dos relatórios de gerenciamento de custos do Cloudyn no Azure | Microsoft Docs
description: Este artigo ajuda você a entender a estrutura básica e funções dos relatórios de gestão de custos do Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: cost-management
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 6f856aeae74ea285cd6a0326fd225e454a1cbe43
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61003690"
---
# <a name="understanding-cloudyn-cost-management-reports"></a>Noções básicas dos relatórios de gerenciamento de custos do Cloudyn

Este artigo ajuda você a entender a estrutura básica e funções dos relatórios de gestão de custos do Cloudyn. A maioria dos relatórios do Cloudyn é intuitiva e tem uma aparência uniforme. Depois de ler este artigo, você estará pronto para usar todos os relatórios de gestão de custos. Muitos recursos padrão estão disponíveis em vários relatórios, permitindo uma fácil navegação pelos relatórios. Os relatórios são personalizáveis, e você pode selecionar várias opções para calcular e exibir os resultados.

## <a name="report-fields-and-options"></a>Campos e opções do relatório

Confira aqui um exemplo do relatório de Custo ao Longo do Tempo. A maioria dos relatórios do Cloudyn tem um layout semelhante.

![Exemplo de Relatório de Custo ao Longo do Tempo com áreas numeradas correspondendo às descrições](./media/understanding-cost-reports/sample-report.png)

Cada área numerada na imagem anterior é descrita detalhadamente nas seguintes informações:

1. **Intervalo de Datas**

    Use a lista de Intervalo de Datas para definir um intervalo de tempo do relatório usando uma predefinição ou personalização.
2. **Filtro Salvo**

    Use a lista Filtro Salvo para salvar os grupos atuais e os filtros que são aplicados ao relatório. Os filtros salvos estão disponíveis em relatórios de custo e desempenho, incluindo:

      - Análise de Custo
      - Alocação
      - Gerenciamento de Ativos
      - Otimização

   Digite um nome de filtro e clique em **Salvar**.

3. **Marcas**

    Use a área Marcas para agrupar por categorias de marca. As marcas listadas no menu são marcas de departamento do Azure ou do centro de custo, ou são as marcas de entidade e de assinatura de custo do Cloudyn. Selecione marcas para filtrar resultados. Você também pode digitar um nome de marca (palavra-chave) para filtrar os resultados.

    ![Exemplo de uma lista de marcas para filtrar resultados por](./media/understanding-cost-reports/select-options.png)

    Clique em **Adicionar** para adicionar um novo filtro.

    ![Adicionar caixa de filtro, mostrando as opções e as condições para filtrar por](./media/understanding-cost-reports/add-filter.png)

    O agrupamento ou filtragem de marcas não estão relacionados aos recursos do Azure ou a marcas de grupo de recursos.

    Filtragem e agrupamento de marca de alocação de custos estão disponíveis como uma opção do menu **Grupos**.

4. **Grupos nos relatórios**

    Usar grupos em relatórios de Análise de Custo para mostrar categorias padrão detalhadas dos dados de cobrança em seu relatório.  No entanto, os grupos nos relatórios de Alocação de Custo mostram categorias baseadas em marcas de exibição. Categorias baseadas em marcas são definidas no modelo de alocação de custo e em categorias detalhadas padrão dos dados de cobrança.

    ![Primeira lista de exemplo de marcas que você pode agrupar por](./media/understanding-cost-reports/groups-tags01.png)

    ![Segunda lista de exemplo de marcas que você pode agrupar por](./media/understanding-cost-reports/groups-tags02.png)

    Nos relatórios de Alocação de Custo, os grupos em categorias de grupo baseadas em marcas podem incluir:
      - Marcas
      - marcas do grupo de recursos
      - Marcas de entidade de custo do Cloudyn
      - Categorias de marca de assinatura para fins de alocação de custo

   Os exemplos podem incluir:
   - Centro de custo
   - department
   - Aplicativo
   - Ambiente
   - Código de custo

     Esta é uma lista de grupos internos disponíveis nos relatórios:

     - **Tipo de Custo**
     - Selecione um tipo de custo ou vários tipos de custo, ou selecione tudo. Os tipos de custo incluem:
       - Valor Único
       - Suporte
       - Custo de Uso
     - **Cliente**
       - Selecione um cliente específico, vários clientes ou todos os clientes.
     - **Nome da Conta**
       - O nome da conta ou da assinatura. No Azure, é o nome da assinatura do Azure.
     - **Nº da Conta**
       - Selecione uma conta, várias contas ou todas as contas. No Azure, é o GUID da assinatura do Azure.
     - **Conta Primária**
       - Selecione a conta primária, várias contas ou selecione tudo.
     - **Serviço**
       - Selecione um serviço, vários serviços ou todos os serviços.
     - **Provedor**
       - O provedor de nuvem ao qual os ativos e as despesas estão associados.
     - **Região**
       - Região na qual o recurso está hospedado.
     - **Zona de Disponibilidade**
       - Locais isolados do AWS em uma região.
     - **Tipo de recurso**
       - O tipo de recurso em uso.
     - **Subtipo**
       - Selecione o subtipo.
     - **Operação**
       - Selecione a operação ou **Mostrar tudo**.
     - **Modelo de Preço**
       - Todos os Pagamentos Antecipados
       - Sem Pagamentos Antecipados
       - Pagamentos Parciais Antecipados
       - Sob demanda
       - Reserva
       - À Vista
     - **Tipo de Encargo**
       - Selecione o tipo de encargo Negativo ou Positivo, ou ambos.
     - **Locação**
       - Indica se um computador está em execução como um computador dedicado.
     - **Tipo de Uso**
       - O tipo de uso podem ser valores únicos ou recorrentes.

5. **Filtros**

    Use filtros individuais ou de seleção múltipla para definir intervalos como valores selecionados. Para definir um filtro, clique em **Adicionar** e, depois, selecione as categorias e os valores de filtro.

6. **Modelo de Custo**

    Use o Modelo de Custo para selecionar um modelo de custo que você criou anteriormente com a Alocação de Custo 360. Talvez você tenha vários modelos de custo do Cloudyn, dependendo de seus requisitos de alocação de custo. Algumas de suas equipes organizacionais podem ter requisitos de alocação de custo diferentes das outras. Cada equipe pode ter seu próprio modelo de custo dedicado.

    Para saber mais sobre como criar uma definição de modelo de alocação de custo, confira [Usar marcas personalizadas para alocar os custos](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

7. **Amortização**

    Use os relatórios de Amortização na Alocação de Custo para exibir taxas de serviço sem base no uso ou custos de pagamento único e distribua uniformemente os custos ao longo de sua vida útil. Entre os exemplos de taxas únicas estão:
    - Taxas de suporte anual
    - Taxas de componentes de segurança anuais
    - Taxas de compra de instâncias reservadas
    - Alguns itens do Azure Marketplace.

   Em Amortização, selecione **Custo amortizado** ou **Custo Real**.

8. **Resolução**

    Use Resolução para selecionar a resolução de tempo dentro do intervalo de datas selecionado. Sua resolução de tempo determina como as unidades são exibidas no relatório, e pode ser:
    - Diário
    - Semanal
    - Mensal
    - Trimestral
    - Anual

9. **Regras de alocação**

    Use as Regras de Alocação para aplicar ou desabilitar o recálculo do custo de alocação de custo. Você pode habilitar ou desabilitar o recálculo de alocação de custo para os dados de cobrança. O recálculo se aplica às categorias selecionadas no relatório. Isso permite que você avalie o impacto do recálculo de alocação de custo em relação aos dados brutos de cobrança.

10. **Não categorizado**

    Use Não categorizado para incluir ou excluir os custos não categorizados no relatório.

11. **Mostrar/ocultar campos**

    A opção Mostrar/ocultar não tem qualquer efeito nos relatórios.

12. **Formatos de exibição**

    Use os Formatos de exibição para selecionar vários modos de exibição de tabela ou gráfico.

    ![Símbolos de formatos de exibição que você pode selecionar](./media/understanding-cost-reports/display-formats.png)

13. **Várias cores**

    Use Várias cores para definir a cor dos gráficos no relatório.

14. **Ações**

    Use Ações para salvar, exportar ou agendar do relatório.

15. **Política**

    Embora não ilustrado, alguns relatórios incluem uma política de cálculo de custos projetada. Nesses relatórios, a política **Consolidated** mostra recomendações para todas as contas e assinaturas na entidade atual, como inscrição da Microsoft ou pagante da AWS. A política **Standalone** mostra recomendações para uma conta ou assinatura, como se não houvesse outras assinaturas. A política que você seleciona varia na estratégia de otimização usada por sua organização. As projeções de custos são baseadas nos últimos 30 dias de uso.

## <a name="save-and-schedule-reports"></a>Salvar e agendar relatórios

Depois de criar um relatório, salve-o para uso futuro. Relatórios salvos ficam disponíveis em **Minhas ferramentas** > **Meus relatórios**. Se você fizer alterações a um relatório existente e salvá-lo, o relatório é salvo como uma nova versão. Ou então, você pode salvá-lo como um novo relatório.

### <a name="save-a-report-to-the-cloudyn-portal"></a>Salve um relatório no portal Cloudyn

Ao exibir qualquer relatório, clique em **Ações** e, em seguida, selecione **Salvar em Meus relatórios**. Nomeie o relatório e, em seguida, adicione sua própria URL ou use a URL criada automaticamente. Opcionalmente, você pode **compartilhar** o relatório publicamente com outras pessoas na sua organização, ou você pode compartilhá-lo com sua entidade. Se você não compartilhar o relatório, ele permanecerá um relatório pessoal e que somente você poderá visualizar. Salve o relatório.


### <a name="save-a-report-to-cloud-provider-storage"></a>Salvar um relatório no armazenamento do provedor de nuvem

Para salvar um relatório no seu provedor de serviços de nuvem, você já deve ter configurado uma conta de armazenamento. Ao exibir qualquer relatório, clique em **Ações** e, em seguida, selecione **Agendar relatório**. Nomeie o relatório e, em seguida, adicione sua própria URL ou use a URL criada automaticamente. Selecione **Salvar no armazenamento** e, em seguida, selecione a conta de armazenamento ou adicione uma nova conta. Digite um prefixo que seja anexado ao nome do arquivo do relatório. Selecione um formato de arquivo CSV ou JSON e, em seguida, salve o relatório.

### <a name="schedule-a-report"></a>Agendar um relatório

Você pode executar relatórios em intervalos agendados, e você pode enviá-los para uma lista de destinatários ou conta de armazenamento do provedor de serviços de nuvem. Ao exibir qualquer relatório, clique em **Ações** e, em seguida, selecione **Agendar relatório**. Você pode enviar o relatório por e-mail e salvá-lo em uma conta de armazenamento. Em **Agendar**, selecione o intervalo (diariamente, semanalmente ou mensalmente). Para agendamentos semanais e mensais, selecione o dia ou as datas para o envio e selecione a hora. Salve o relatório agendado. Se você selecionar o formato de relatório do Excel, o relatório é enviado como um anexo. Quando você seleciona o formato de conteúdo de e-mail, os resultados do relatório que são exibidos em formato de gráfico são entregues como um gráfico.

### <a name="export-a-report-as-a-csv-file"></a>Exportar um relatório como um arquivo CSV

Ao exibir qualquer relatório, clique em **Ações** e, em seguida, selecione **Exportar todos os dados do relatório**. Uma janela pop-up será exibida e um arquivo CSV é baixado.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os relatórios incluídos no Cloudyn em [Usar relatórios do Cloudyn](use-reports.md).
- Saiba como usar relatórios para criar [painéis](dashboards.md).

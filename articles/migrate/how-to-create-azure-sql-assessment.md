---
title: Criar uma avaliação do SQL do Azure
description: Saiba como avaliar instâncias SQL para migração para a Instância Gerenciada de SQL do Azure e o Banco de Dados SQL do Azure
author: rashi-ms
ms.author: rajosh
ms.topic: tutorial
ms.date: 02/07/2021
ms.openlocfilehash: 9acc6de84c3e5bc43f482d36ade242d2315a2a4f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105565105"
---
# <a name="create-an-azure-sql-assessment"></a>Criar uma avaliação do SQL do Azure

Como parte de sua jornada de migração para o Azure, é necessário avaliar suas cargas de trabalho locais para medir a prontidão para a nuvem, identificar riscos e estimar custos e complexidade.
Este artigo mostra como avaliar as instâncias SQL descobertas em preparação para migração para o SQL do Azure usando a ferramenta Migrações para Azure: Descoberta e avaliação.

> [!Note]
> A descoberta e a avaliação de instâncias e bancos de dados do SQL Server em execução no ambiente VMware já estão em versão prévia. Veja uma introdução [neste tutorial](tutorial-discover-vmware.md). Caso deseje experimentar esse recurso em um projeto existente, verifique se você concluiu os [pré-requisitos](how-to-discover-sql-existing-project.md) deste artigo.

## <a name="before-you-start"></a>Antes de começar

- [Crie](./create-manage-projects.md) um projeto de Migrações para Azure e adicione a ferramenta Migrações para Azure: Descoberta e avaliação.
- A fim de criar uma avaliação, você precisará configurar um dispositivo de Migrações para Azure para o [VMware](how-to-set-up-appliance-vmware.md). O dispositivo descobre os servidores locais e envia os metadados e os dados de desempenho para as Migrações para Azure. [Saiba mais](migrate-appliance.md)

## <a name="azure-sql-assessment-overview"></a>Visão geral da avaliação do SQL do Azure
Crie uma avaliação do SQL do Azure com critérios de dimensionamento como **Com base no desempenho**. 

**Critérios de dimensionamento** | **Detalhes** | **Dados**
--- | --- | ---
**Com base no desempenho** | Avaliações com base nos dados de desempenho coletados | A **configuração recomendada do SQL do Azure** baseia-se nos dados de desempenho de instâncias e bancos de dados SQL, que incluem uso de CPU, contagens de núcleos, organizações de arquivos de banco de dados e tamanhos, E/Ss de arquivo, consulta em lote por segundo e tamanho de memória e uso por banco de dados.

[Saiba mais](concepts-azure-sql-assessment-calculation.md) sobre as avaliações do SQL do Azure.

## <a name="run-an-assessment"></a>Ler uma avaliação
Execute uma avaliação da seguinte maneira:
1. Na página **Visão geral** > **Windows, Linux e SQL Server**, clique em **Avaliar e migrar servidores**.
    :::image type="content" source="./media/tutorial-assess-sql/assess-migrate.png" alt-text="Página de visão geral das Migrações para Azure":::
2. Em **Migrações para Azure: Descoberta e avaliação**, clique em **Avaliar** e escolha o tipo de avaliação como **SQL do Azure**.
    :::image type="content" source="./media/tutorial-assess-sql/assess.png" alt-text="Lista suspensa para escolha do tipo de avaliação como SQL do Azure":::
3. Em **Avaliar servidores**, você poderá ver o tipo de avaliação pré-selecionado como **SQL do Azure** e a fonte de descoberta padronizada como **Servidores descobertos do dispositivo de Migrações para Azure**.

4. Clique em **Editar** para examinar as propriedades da avaliação.
     :::image type="content" source="./media/tutorial-assess-sql/assess-servers-sql.png" alt-text="Botão Editar no local em que as propriedades da avaliação podem ser personalizadas":::
5. Em Propriedades da avaliação > **Propriedades de Destino**:
    - Em **Local de destino**, especifique a região do Azure para a qual você deseja migrar. 
        - As recomendações de configuração e custo do SQL do Azure são baseadas na localização especificada. 
    - Em **Tipo de implantação de destino**,
        - Selecione **Recomendado** caso deseje que as Migrações para Azure avaliem a preparação das suas instâncias SQL para migração para a MI de SQL do Azure e o BD SQL do Azure e recomendem a opção de implantação de destino mais adequada, a camada de destino, a configuração do SQL do Azure e as estimativas mensais. [Saiba mais](concepts-azure-sql-assessment-calculation.md)
        - Selecione **BD SQL do Azure** caso deseje avaliar a preparação das suas instâncias SQL para migração somente para Bancos de Dados SQL do Azure e examine a camada de destino, a configuração do SQL do Azure e as estimativas mensais.
        - Selecione **MI de SQL do Azure** caso deseje avaliar a preparação das suas instâncias SQL para migração somente para a Instância Gerenciada de SQL do Azure e examine a camada de destino, a configuração do SQL do Azure e as estimativas mensais.
    - Em **Capacidade Reservada**, especifique se deseja usar a capacidade reservada para o SQL Server após a migração.
        - Se você selecionar uma opção de capacidade reservada, não poderá especificar “Desconto (%)”.

6. Em Propriedades da avaliação > **Critérios de avaliação**:
    - Os critérios de dimensionamento são padronizados como **Com base no desempenho**, o que significa que as Migrações para Azure coletarão métricas de desempenho relativas a instâncias SQL e bancos de dados gerenciados por ela para recomendar uma configuração do Banco de Dados SQL do Azure e/ou da Instância Gerenciada de SQL do Azure de tamanho ideal. É possível especificar:
        - **Histórico de desempenho** para indicar a duração dos dados em que deseja basear a avaliação. (O padrão é um dia)
        - **Utilização do percentil** para indicar o valor de percentil que deseja usar para a amostra de desempenho. (O padrão é 95º percentil)
    - Em **Fator de conforto**, indique o buffer que você deseja usar durante a avaliação. Esse recurso detecta problemas como uso sazonal, histórico de desempenho baixo e prováveis aumentos no uso futuro. Por exemplo, se você usar um fator de conforto de dois: 
        
        **Componente** | **Utilização efetiva** | **Adicionar fator de conforto (2.0)**
        --- | --- | ---
        Núcleos | 2  | 4
        Memória | 8 GB | 16 GB
   
7. Em **Preços**:
    - Em **Oferta/Programa de licenciamento**, especifique a oferta do Azure caso esteja registrado. No momento, só é possível escolher Pagamento Conforme o Uso e Desenvolvimento/Teste Pago Conforme o Uso. 
        - Você pode ter um desconto adicional com a aplicação da capacidade reservada e do Benefício Híbrido do Azure sobre a oferta Pagamento Conforme o Uso. 
        - É possível aplicar o Benefício Híbrido do Azure ao Desenvolvimento/Teste Pago Conforme o Uso. Atualmente, a avaliação não dá suporte à aplicação da capacidade reservada sobre a oferta Desenvolvimento/Teste Pago Conforme o Uso.
    - Em **Camada de Serviço**, escolha a opção de camada de serviço mais apropriada para acomodar suas necessidades comerciais para migração para o Banco de Dados SQL do Azure e/ou a Instância Gerenciada de SQL do Azure: 
        - Selecione **Recomendado** se desejar que as Migrações para Azure recomendem a camada de serviço mais adequada para seus servidores. Isso pode ser Uso Geral ou Comercialmente Crítico. Saiba mais
        - Selecione **Uso Geral** caso deseje obter uma configuração do SQL do Azure projetada para cargas de trabalho orientadas a orçamento.
        - Selecione **Comercialmente Crítico** caso deseje obter uma configuração do SQL do Azure projetada para cargas de trabalho de baixa latência com alta resiliência a falhas e failovers rápidos.
    - Em **Desconto (%)** , adicione quaisquer descontos específicos à assinatura recebidos sobre a oferta do Azure. A configuração padrão é 0%.
    - Em **Moeda**, selecione a moeda de cobrança para sua conta.
    - Em **Benefício Híbrido do Azure**, especifique se você já tem uma licença do SQL Server. Caso você tenha e ela esteja coberta pelo Software Assurance ativo de Assinaturas do SQL Server, inscreva-se no Benefício Híbrido do Azure ao trazer as licenças para o Azure.
    - Clique em Salvar se você fizer alterações.
     :::image type="content" source="./media/tutorial-assess-sql/view-all.png" alt-text="Botão Salvar nas propriedades da avaliação":::
8. Em **Avaliar Servidores** > clique em Avançar.
9.  Em **Selecionar os servidores a serem avaliados** > **Nome da avaliação** > especifique um nome para a avaliação.
10. Em **Selecionar ou criar um grupo** > selecione **Criar** e especifique um nome de grupo.
     :::image type="content" source="./media/tutorial-assess-sql/assessment-add-servers.png" alt-text="Localização do botão Novo grupo":::
11. Selecione o dispositivo e os servidores que deseja adicionar ao grupo. Clique em Avançar.
12. Em **Examinar + criar avaliação**, examine os detalhes da avaliação e clique em Criar Avaliação para criar o grupo e executar a avaliação.
     :::image type="content" source="./media/tutorial-assess-sql/assessment-create.png" alt-text="Localização do botão Examinar e criar avaliação.":::
13. Após a criação da avaliação, acesse o bloco **Windows, Linux e SQL Server** > **Migrações para Azure: Descoberta e avaliação** > clique no número ao lado da avaliação do SQL do Azure.
     :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-navigation.png" alt-text="Navegação para a avaliação criada":::
15. Clique no nome da avaliação que deseja ver.

> [!NOTE]
> Para as avaliações do SQL do Azure baseadas em desempenho, recomendamos que você espere pelo menos um dia após o início da descoberta antes de criar uma avaliação. Isso fornecerá tempo para coletar dados de desempenho com maior confiança. Se a sua descoberta ainda estiver em andamento, a preparação das suas instâncias SQL será marcada como **Desconhecida**. O ideal é que, depois de iniciar a descoberta, **você aguarde a duração do desempenho especificada (dia/semana/mês)** a fim de criar ou recalcular a avaliação para obter uma classificação de alta confiança. 

## <a name="review-an-assessment"></a>Examinar uma avaliação

**Para ver uma avaliação**:

1. **Windows, Linux e SQL Server** > **Migrações para Azure: Descoberta e avaliação** > clique no número ao lado da avaliação do SQL do Azure.
2. Clique no nome da avaliação que deseja ver. Por exemplo (as estimativas e os custos são apenas exemplos): :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-summary.png" alt-text="Visão geral da avaliação do SQL":::
3. Examine o resumo da avaliação. Você também pode editar as propriedades da avaliação ou recalcular a avaliação.

#### <a name="discovered-items"></a>Itens descobertos

Indica o número de servidores, instâncias e bancos de dados SQL avaliados nesta avaliação.
    
#### <a name="azure-readiness"></a>Preparação do Azure

Indica a distribuição de instâncias SQL avaliadas: 
    
**Tipo de implantação de destino (em propriedades da avaliação)** | **Preparação**   
--- | --- |
**Recomendado** |  Pronto para o Banco de Dados SQL do Azure, Pronto para a Instância Gerenciada de SQL do Azure, Possivelmente pronto para a VM do Azure, Preparação desconhecida (caso a descoberta esteja em andamento ou haja problemas de descoberta a serem corrigidos)
**BD SQL do Azure** ou **MI de SQL do Azure** | Pronto para o Banco de Dados SQL do Azure ou a Instância Gerenciada de SQL do Azure, Não pronto para o Banco de Dados SQL do Azure nem para a Instância Gerenciada de SQL do Azure, Preparação desconhecida (caso a descoberta esteja em andamento ou haja problemas de descoberta a serem corrigidos)
     
Faça uma busca detalhada para entender os detalhes sobre problemas de migração/avisos que podem ser corrigidos antes da migração para o SQL do Azure. [Saiba mais](concepts-azure-sql-assessment-calculation.md) Examine também as configurações recomendadas do SQL do Azure para migração para bancos de dados SQL do Azure e/ou Instâncias Gerenciadas.
    
#### <a name="azure-sql-database-and-managed-instance-cost-details"></a>Detalhes de custo do Banco de Dados SQL do Azure e da Instância Gerenciada

A estimativa de custo mensal inclui os custos de computação e armazenamento para as configurações do SQL do Azure correspondentes ao tipo de implantação recomendado do Banco de Dados SQL do Azure e/ou da Instância Gerenciada de SQL do Azure. [Saiba mais](concepts-azure-sql-assessment-calculation.md#calculate-monthly-costs)


### <a name="review-readiness"></a>Examinar preparação

1. Clique em **Preparação para o SQL do Azure**.
    :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-readiness.png" alt-text="Detalhes da preparação para o SQL do Azure":::
1. Na preparação para o SQL do Azure, examine a **preparação para o BD SQL do Azure** e a **preparação para a MI de SQL do Azure** das instâncias SQL avaliadas:
    - **Pronto**: a instância está pronta para ser migrada para o BD SQL do Azure/a MI sem problemas de migração ou avisos. 
        - Pronto (ícone de informações azul e com hiperlinks): a instância está pronta para ser migrada para o Banco de Dados SQL do Azure/MI sem problemas de migração, mas há alguns avisos de migração que precisam ser examinados. Clique no hiperlink para examinar os avisos de migração e as diretrizes de correção recomendadas: :::image type="content" source="./media/tutorial-assess-sql/assess-ready.png" alt-text="Avaliação com status pronto":::       
    - **Não pronto**: a instância tem um ou mais problemas de migração para ser migrada para o BD SQL do Azure/a MI. Clique no hiperlink e examine os problemas de migração e as diretrizes de correção recomendadas.
    - **Desconhecido**: as Migrações para Azure não podem avaliar a preparação, pois a descoberta está em andamento ou há problemas durante a descoberta que precisam ser corrigidos na folha de notificações. Se o problema persistir, contate o suporte da Microsoft. 
1. Examine o tipo de implantação recomendado para a instância SQL, que é determinado de acordo com a matriz abaixo:

    - **Tipo de implantação de destino** (conforme selecionado nas propriedades da avaliação): **Recomendado**

        **Preparação para o BD SQL do Azure** | **Preparação para a MI de SQL do Azure** | **Tipo de implantação recomendado** | **Estimativas de custo e configuração do SQL do Azure calculadas?**
         --- | --- | --- | --- |
        Ready | Ready | BD SQL do Azure ou MI de SQL do Azure [Saiba mais](concepts-azure-sql-assessment-calculation.md#recommended-deployment-type) | Sim
        Ready | Não pronto ou Desconhecido | BD SQL do Azure | Sim
        Não pronto ou Desconhecido | Ready | MI de SQL do Azure | Sim
        Não está pronto. | Não está pronto. | Potencialmente pronto para a VM do Azure [Saiba mais](concepts-azure-sql-assessment-calculation.md#potentially-ready-for-azure-vm) | Não
        Não pronto ou Desconhecido | Não pronto ou Desconhecido | Unknown | Não
    
    - **Tipo de implantação de destino** (conforme selecionado nas propriedades da avaliação): **BD SQL do Azure**
    
        **Preparação para o BD SQL do Azure** | **Estimativas de custo e configuração do SQL do Azure calculadas?**
        --- | --- |
        Ready | Sim
        Não está pronto. | Não
        Unknown | Não
    
    - **Tipo de implantação de destino** (conforme selecionado nas propriedades da avaliação): **MI de SQL do Azure**
    
        **Preparação para a MI de SQL do Azure** | **Estimativas de custo e configuração do SQL do Azure calculadas?**
         --- | --- |
        Ready | Sim
        Não está pronto. | Não
        Unknown | Não

4. Clique na busca detalhada do nome da instância para ver o número de bancos de dados de usuário, detalhes da instância, incluindo propriedades da instância, computação (no escopo da instância) e detalhes do armazenamento do banco de dados de origem.
5. Clique no número de bancos de dados de usuário para examinar a lista de bancos de dados e os respectivos detalhes. Por exemplo (as estimativas e os custos são apenas exemplos): :::image type="content" source="./media/tutorial-assess-sql/assessment-db.png" alt-text="Detalhe da instância SQL":::
5. Clique em Examinar detalhes na coluna Problemas de migração para examinar os problemas de migração e os avisos de um tipo de implantação de destino específico. 
    :::image type="content" source="./media/tutorial-assess-sql/assessment-db-issues.png" alt-text="Problemas e avisos de migração do BD":::

### <a name="review-cost-estimates"></a>Examinar estimativas de custo
O resumo da avaliação mostra os custos de computação e armazenamento mensais estimados para as configurações do SQL do Azure correspondentes ao tipo de implantação recomendado de bancos de dados SQL do Azure e/ou Instâncias Gerenciadas.

1. Examinar os custos totais mensais. Os custos são agregados para todas as instâncias SQL no grupo avaliado.
    - As estimativas de custo são baseadas na configuração recomendada do SQL do Azure para uma instância. 
    - Os custos mensais estimados de computação e armazenamento são mostrados. Por exemplo (as estimativas e os custos são apenas exemplos):
    
    :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-cost.png" alt-text="Detalhes de custo":::

1. Faça uma busca detalhada em um nível de instância para ver as estimativas de custo e configuração do SQL do Azure em um nível de instância.  
1. Faça também uma busca detalhada na lista de bancos de dados para examinar as estimativas de custo e configuração do SQL do Azure por banco de dados quando uma configuração de Banco de Dados SQL do Azure é recomendada.

### <a name="review-confidence-rating"></a>Revisar classificação de confiança
As Migrações para Azure atribuem uma classificação de confiança a todas as avaliações do SQL do Azure com base na disponibilidade dos pontos de dados de desempenho/utilização necessários para calcular a avaliação de todas as instâncias e todos os bancos de dados SQL avaliados. A classificação varia de uma estrela (mais baixa) a cinco estrelas (mais alta).
 
A classificação de confiança ajuda a estimar a confiabilidade das recomendações de tamanho na avaliação.  As classificações de confiança são as mostradas a seguir.

**Disponibilidade do ponto de dados** | **Classificação de confiança**
--- | ---
0%-20% | 1 estrela
21%-40% | 2 estrelas
41%-60% | 3 estrelas
61%-80% | 4 estrelas
81%-100% | 5 estrelas

[Saiba mais](concepts-azure-sql-assessment-calculation.md#confidence-ratings) sobre as classificações de confiança.

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais](concepts-azure-sql-assessment-calculation.md) sobre como as avaliações do SQL do Azure são calculadas.
- Comece a migrar instâncias e bancos de dados SQL usando o [Serviço de Migração de Banco de Dados do Azure](../dms/dms-overview.md).
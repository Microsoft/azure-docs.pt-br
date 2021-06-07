---
title: Avaliações do SQL do Azure na ferramenta de avaliação e descoberta de migrações para Azure
description: Saiba mais sobre as avaliações do Azure SQL na ferramenta de avaliação e descoberta de migrações para Azure
author: rashi-ms
ms.author: rajosh
ms.topic: conceptual
ms.date: 02/07/2021
ms.openlocfilehash: c2e739a45c7915c957ca89e5b01b98afa945d03e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105557183"
---
# <a name="assessment-overview-migrate-to-azure-sql"></a>Visão geral da avaliação (migrar para o SQL do Azure)

Este artigo fornece uma visão geral das avaliações para migrar instâncias de SQL Server locais de um ambiente VMware para bancos de dados SQL do Azure ou instâncias gerenciadas usando a [ferramenta migrações para Azure: descoberta e avaliação](./migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool).

## <a name="whats-an-assessment"></a>O que é uma avaliação?
Uma avaliação com a ferramenta de descoberta e avaliação é um instantâneo pontual dos dados e mede a prontidão e estima o efeito da migração de servidores locais para o Azure.

## <a name="types-of-assessments"></a>Tipos de avaliações

Há três tipos de avaliações que você pode criar usando a ferramenta migrações para Azure: descoberta e avaliação.

**Tipo de avaliação** | **Detalhes**
--- | --- 
**VM do Azure** | Avaliações para migrar servidores locais para máquinas virtuais do Azure. <br/><br/> Você pode avaliar seus servidores locais no ambiente [VMware](how-to-set-up-appliance-vmware.md) e [Hyper-V](how-to-set-up-appliance-hyper-v.md) e [servidores físicos](how-to-set-up-appliance-physical.md) para migração para VMs do Azure usando esse tipo de avaliação.
**SQL do Azure** | Avaliações para migrar seus SQL Servers locais de seu ambiente VMware para o banco de dados SQL do Azure ou o Azure SQL Instância Gerenciada.
**AVS (Solução VMware no Azure)** | Avaliações para migrar servidores locais para a [AVS (Solução VMware no Azure)](../azure-vmware/introduction.md). <br/><br/> Avalie as [VMs do VMware](how-to-set-up-appliance-vmware.md) locais para migração para a AVS (Solução VMware no Azure) usando esse tipo de avaliação. [Saiba mais](concepts-azure-vmware-solution-assessment-calculation.md)

Uma avaliação do SQL do Azure fornece um critério de dimensionamento:

**Critérios de dimensionamento** | **Detalhes** | **Dados**
--- | --- | ---
**Com base no desempenho** | Avaliações que fazem recomendações com base nos dados de desempenho coletados | A configuração do SQL do Azure baseia-se nos dados de desempenho de instâncias e bancos de dado SQL, que incluem: utilização da CPU, utilização de memória, IOPS (arquivos de dados e de log), taxa de transferência e latência de operações de e/s.

## <a name="how-do-i-assess-my-on-premises-sql-servers"></a>Como fazer avaliar meus SQL Servers locais?

Você pode avaliar suas instâncias de SQL Server locais usando os dados de configuração e utilização coletados por um dispositivo de migrações leve do Azure. O dispositivo descobre as instâncias e os bancos de dados do SQL Server local e os envia para as migrações para Azure. [Saiba mais](how-to-set-up-appliance-vmware.md)

## <a name="how-do-i-assess-with-the-appliance"></a>Como fazer avaliar com o dispositivo?
Se você estiver implantando um dispositivo de migrações para Azure para descobrir servidores locais, execute as seguintes etapas:
1.  Configure o Azure e seu ambiente local para trabalhar com as migrações para Azure.
2.  Para sua primeira avaliação, crie um projeto de migrações para Azure e adicione a ferramenta migrações para Azure: descoberta e avaliação para ele.
3.  Implante um dispositivo leve de migrações para Azure. O dispositivo descobre continuamente servidores locais e envia dados de configuração e desempenho para migrações para Azure. Implante o dispositivo como uma VM ou um servidor físico. Você não precisa instalar nada em servidores que deseja avaliar.

Depois que o dispositivo iniciar a descoberta, você poderá coletar servidores que deseja avaliar em um grupo e executar uma avaliação para o grupo com o tipo de avaliação **SQL do Azure**.

Siga nosso tutorial para avaliar [SQL Server instâncias](tutorial-assess-sql.md) para experimentar essas etapas.

## <a name="how-does-the-appliance-calculate-performance-data-for-sql-instances-and-databases"></a>Como o dispositivo calcula dados de desempenho para instâncias e bancos de dado SQL?

O dispositivo coleta dados de desempenho para configurações de computação com estas etapas:
1. O dispositivo coleta um ponto de exemplo em tempo real. Para servidores SQL, um ponto de amostra é coletado a cada 30 segundos.
2. O dispositivo agrega os pontos de dados de exemplo coletados a cada 30 segundos em 10 minutos. Para criar o ponto de dados, o dispositivo seleciona os valores de pico de todos os exemplos. Ele envia o máximo, a média e a variância de cada contador para o Azure.
3. As migrações para Azure armazenam todos os pontos de dados de 10 minutos no último mês.
4. Quando você cria uma avaliação, as migrações para Azure identificam o ponto de dados apropriado a ser usado para a identificação de direitos. A identificação é baseada nos valores de percentil para o histórico de desempenho e utilização de percentil.
    - Por exemplo, se o histórico de desempenho for de uma semana e a utilização do percentil for o 95 º percentil, a avaliação classificará os pontos de exemplo de 10 minutos da última semana. Ele os classifica em ordem crescente e escolhe o valor de percentil de 95 º para direitos.
    - O valor do 95 º percentil garante que você ignore as exceções, que poderão ser incluídas se você tiver escolhido o 99 º percentil.
    - Se você quiser escolher o pico de uso do período e não quiser perder as exceções, selecione o 99 º percentil para utilização de percentil.
5. Esse valor é multiplicado pelo fator de conforto para obter os dados de utilização de desempenho efetivos para essas métricas que o dispositivo coleta:
    - Utilização da CPU (%)
    - Utilização de memória (%)
    - Ler e/s e gravar e/s (arquivos de dados e de log)
    - MB/s de leitura e MB/s de gravação (taxa de transferência)
    - Latência de operações de e/s

## <a name="what-properties-are-used-to-create-and-customize-an-azure-sql-assessment"></a>Quais propriedades são usadas para criar e personalizar uma avaliação do Azure SQL?

Aqui estão as novidades incluídas nas propriedades de avaliação do SQL do Azure:

**Propriedade** | **Detalhes**
--- | ---
**Local de destino** | A região do Azure para a qual você deseja migrar. As recomendações de configuração e custo do SQL do Azure são baseadas na localização especificada.
**Tipo de implantação de destino** | O tipo de implantação de destino no qual você deseja executar a avaliação: <br/><br/> Selecione **recomendado**. se você quiser migrações para Azure para avaliar a prontidão dos SQL Servers para migrar para o Azure SQL mi e o BD SQL do Azure e recomendar a opção de implantação de destino mais adequada, camada de destino, configuração do SQL do Azure e estimativas mensais.<br/><br/>Selecione **banco de dados SQL do Azure**, se você quiser avaliar seus SQL Servers para migrar somente para bancos de dados SQL do Azure e examinar a camada de destino, a configuração do BD SQL do Azure e as estimativas mensais.<br/><br/>Selecione **SQL do Azure mi**, se você quiser avaliar seus SQL Servers para migrar somente para bancos de dados SQL do Azure e examinar a camada de destino, a configuração de Mi do Azure SQL e as estimativas mensais.
**Capacidade reservada** | Especifica a capacidade reservada para que as estimativas de custo na avaliação as levem em conta.<br/><br/> Se você selecionar uma opção de capacidade reservada, não poderá especificar “Desconto (%)”.
**Critérios de dimensionamento** | Essa propriedade é usada para dimensionar a configuração do SQL do Azure. <br/><br/> Ele é padronizado com **base no desempenho** , o que significa que a avaliação coletará as métricas de desempenho de instâncias de SQL Server e de bancos de dados para recomendar uma recomendação de configuração/camada de banco de dados SQL do Azure instância gerenciada SQL e/ou Azure.
**Histórico de desempenho** | Histórico de desempenho especifica a duração usada quando os dados de desempenho são avaliados.
**Utilização de percentual** | Utilização de percentil especifica o valor percentual do exemplo de desempenho usado para a permissão de direitos.
**Fator de conforto** | O buffer usado durante a avaliação. Ele conta com problemas como uso sazonal, histórico de desempenho curto e, provavelmente, aumenta o uso futuro.<br/><br/> Por exemplo, uma instância de 10 núcleos com 20% de utilização normalmente resulta em uma instância de dois núcleos. Com um fator de conforto de 2,0, o resultado é uma instância de quatro núcleos.
**Programa de oferta/licenciamento** | A [oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/) na qual você está inscrito. No momento, só é possível escolher Pagamento Conforme o Uso e Desenvolvimento/Teste Pago Conforme o Uso. Observe que você pode ter um desconto adicional com a aplicação de capacidade reservada e Benefício Híbrido do Azure sobre a oferta paga conforme o uso.
**Camada de serviço** | A opção de camada de serviço mais apropriada para acomodar suas necessidades de negócios para a migração para o banco de dados SQL do Azure e/ou o Azure SQL Instância Gerenciada:<br/><br/>**Recomendado** se você quiser migrações para Azure para recomendar a camada de serviço mais adequada para seus servidores. Isso pode ser Uso Geral ou Comercialmente Crítico. <br/><br/> **Uso geral** Se você quiser uma configuração SQL do Azure projetada para cargas de trabalho orientadas a orçamento. [Saiba mais](../azure-sql/database/service-tier-general-purpose.md) <br/><br/> **Comercialmente crítico** Se você quiser uma configuração SQL do Azure projetada para cargas de trabalho de baixa latência com alta resiliência a falhas e failovers rápidos. [Saiba mais](../azure-sql/database/service-tier-business-critical.md)
**Moeda** | A moeda de cobrança da sua conta.
**Desconto (%)** | Quaisquer descontos específicos de assinatura recebidos por cima da oferta do Azure. A configuração padrão é 0%.
**Benefício Híbrido do Azure** | Especifica se você já tem uma licença de SQL Server. <br/><br/> Caso você tenha e ela esteja coberta pelo Software Assurance ativo de Assinaturas do SQL Server, inscreva-se no Benefício Híbrido do Azure ao trazer as licenças para o Azure.

[Examine as práticas recomendadas](best-practices-assessment.md) para criar uma avaliação com as migrações para Azure.

## <a name="calculate-readiness"></a>Calcular preparação

> [!NOTE]
A avaliação inclui apenas bancos de dados que estão no status online. Caso o banco de dados esteja em qualquer outro status, a avaliação ignorará a preparação, o dimensionamento e o cálculo de custo para esses bancos de dados. Caso queira avaliar esses bancos de dados, mude o status dele e recalcule a avaliação em algum momento.

### <a name="azure-sql-readiness"></a>Preparação do SQL do Azure

A preparação do SQL do Azure para instâncias e bancos de dados SQL baseia-se em uma verificação de compatibilidade de recursos com o banco de dados SQL do Azure e o Azure SQL Instância Gerenciada:
1. A avaliação do SQL do Azure considera os recursos de instância do SQL Server que são usados atualmente pelas cargas de trabalho de SQL Server de origem (trabalhos do SQL Agent, servidores vinculados etc.) e os esquemas de bancos de dados do usuário (tabelas, exibições, gatilhos, procedimentos armazenados etc.) para identificar problemas de compatibilidade.
1. Se não houver nenhum problema de compatibilidade encontrado, a preparação será marcada como **pronta** para o tipo de implantação de destino (banco de dados SQL do Azure ou azure SQL instância gerenciada)
1. Se houver problemas de compatibilidade não críticos, como recursos degradados ou sem suporte que não bloqueiam a migração para um tipo de implantação de destino específico, a preparação será marcada como **pronta** (ícone de informações sobre hiperlinks e azuis) com detalhes de **aviso** e diretrizes de correção recomendadas.
1. Se houver algum problema de compatibilidade que possa bloquear a migração para um tipo de implantação de destino específico, a preparação será marcada como **não pronta** com os detalhes do **problema** e as diretrizes de correção recomendadas.
    - Se houver até mesmo um banco de dados em uma instância do SQL que não esteja pronta para um tipo de implantação de destino específico, a instância será marcada como **não pronta** para esse tipo de implantação.
1. Se a descoberta ainda estiver em andamento ou se houver quaisquer problemas de descoberta para uma instância ou banco de dados SQL, a preparação será marcada como **desconhecida** , pois a avaliação não pôde calcular a prontidão para essa instância do SQL.

### <a name="recommended-deployment-type"></a>Tipo de implantação recomendado

Se você selecionar o tipo de implantação de destino como **recomendado** nas propriedades de avaliação do SQL do Azure, as migrações para Azure recomendarão um tipo de implantação do Azure SQL que seja compatível com sua instância do SQL. A migração para um destino recomendado pela Microsoft reduz o esforço geral de migração. 

#### <a name="recommended-deployment-type-based-on-azure-sql-readiness"></a>Tipo de implantação recomendado com base na preparação do SQL do Azure

 **Preparação para o BD SQL do Azure** | **Preparação para a MI de SQL do Azure** | **Tipo de implantação recomendado** | **Estimativas de custo e configuração do SQL do Azure calculadas?**
 --- | --- | --- | --- |
 Ready | Ready | Banco de BD SQL do Azure ou <br/>MI de SQL do Azure | Sim
 Ready | Não está pronto ou<br/> Unknown | BD SQL do Azure | Sim
 Não está pronto ou<br/>Unknown | Ready | MI de SQL do Azure | Sim
 Não está pronto. | Não está pronto. | Potencialmente preparado para a VM do Azure | No
 Não está pronto ou<br/>Unknown | Não está pronto ou<br/>Unknown | Unknown | Não

> [!NOTE]
> Se o tipo de implantação recomendado for selecionado como **recomendado** nas propriedades de avaliação e se o SQL Server de origem for adequado para o banco de dados individual do Azure SQL DB e o SQL instância gerenciada do Azure, a avaliação recomendará uma opção específica que otimiza o custo e se encaixa nos limites de tamanho e desempenho.

#### <a name="potentially-ready-for-azure-vm"></a>Potencialmente preparado para a VM do Azure

Se a instância do SQL não estiver pronta para o banco de dados SQL do Azure e o Azure SQL Instância Gerenciada, o tipo de implantação recomendado será marcado como *potencialmente pronto para a VM do Azure*.
- O usuário é recomendado para criar uma avaliação no Azure migrar com o tipo de avaliação como "VM do Azure" para determinar se o servidor no qual a instância está sendo executada está pronto para migrar para uma VM do Azure em vez disso. Observe que:
    - As avaliações de VM do Azure nas migrações para Azure são comparadas no momento e se concentram em foco e não considerarão as métricas de desempenho específicas para executar instâncias e bancos de dados SQL na máquina virtual do Azure. 
    - Quando você executa uma avaliação de VM do Azure em um servidor, o tamanho recomendado e as estimativas de custo são para todas as instâncias em execução no servidor e podem ser migradas para uma VM do Azure usando a ferramenta de migração de servidor. Antes de migrar, [examine as diretrizes de desempenho](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md) para SQL Server em máquinas virtuais do Azure.


## <a name="calculate-sizing"></a>Calcular o dimensionamento

### <a name="azure-sql-configuration"></a>Configuração do SQL do Azure

Depois que a avaliação determina a preparação e o tipo de implantação do Azure SQL recomendado, ele computa uma camada de serviço específica e a configuração do Azure SQL (tamanho do SKU) que pode atender ou exceder o desempenho da instância do SQL local:
1. Durante o processo de descoberta, as migrações para Azure coletam a configuração da instância do SQL e o desempenho que inclui:
    - vCores (alocada) e utilização da CPU (%)
        - A utilização da CPU para uma instância do SQL é a porcentagem da CPU alocada utilizada pela instância no SQL Server
        - A utilização da CPU para um banco de dados é a porcentagem da CPU alocada utilizada pelo banco de dados na instância do SQL
    - Memória (alocada) e utilização de memória (%)
    - Ler e/s e gravar e/s (arquivos de dados e de log)
        - Ler IO/s e gravar e/s em um nível de instância do SQL é calculado adicionando as e/s de leitura e/s de gravação de todos os bancos de dados descobertos nessa instância.
    - MB/s de leitura e MB/s de gravação (taxa de transferência)
    - Latência de operações de e/s
    - Tamanho total do BD e organizações de arquivo de banco de dados
        - O tamanho do banco de dados é calculado com a adição de todos os arquivos de log e data.
1. A avaliação agrega todos os dados de desempenho e configuração e tenta encontrar a melhor correspondência em várias camadas e configurações de serviço do Azure SQL e escolhe uma configuração que pode corresponder ou exceder os requisitos de desempenho da instância do SQL, otimizando o custo.

### <a name="confidence-ratings"></a>Classificações de confiança 
Cada avaliação de SQL do Azure é associada a uma classificação de confiança. A classificação varia de um (menor) a cinco estrelas (mais alta). A classificação de confiança ajuda você a estimar a confiabilidade das recomendações de tamanho que o Azure migra fornece.
- A classificação de confiança é atribuída a uma avaliação. A classificação é baseada na disponibilidade de pontos de dados que são necessários para calcular a avaliação.
- Para o dimensionamento baseado em desempenho, a avaliação coleta dados de desempenho de todas as instâncias e bancos de dado SQL, que incluem:
    - Utilização da CPU (%)
    - Utilização de memória (%)
    - Ler e/s e gravar e/s (arquivos de dados e de log)
    - MB/s de leitura e MB/s de gravação (taxa de transferência)
    - Latência de operações de e/s
    
Se qualquer um desses números de utilização não estiver disponível, as recomendações de tamanho podem não ser confiáveis.
Esta tabela mostra as classificações de confiança de avaliação, que dependem da porcentagem de pontos de dados disponíveis:

**Disponibilidade do ponto de dados** | **Classificação de confiança**
--- | ---
0%-20% | 1 estrela
21%-40% | 2 estrelas
41%-60% | 3 estrelas
61%-80% | 4 estrelas
81%-100% | 5 estrelas

#### <a name="low-confidence-ratings"></a>Classificações de baixa confiança
Aqui estão algumas razões pelas quais uma avaliação pode obter uma classificação de baixa confiança:
- Você não criou o perfil do ambiente pelo tempo para o qual está criando a avaliação. Por exemplo, se você criar a avaliação com duração de desempenho definida como um dia, deverá aguardar pelo menos um dia depois de iniciar a descoberta para todos os pontos de dados a serem coletados.
- A avaliação não é capaz de coletar os dados de desempenho de alguns ou de todos os servidores no período de avaliação. Para uma classificação de alta confiança, verifique se:
    - Os servidores estão ligados durante a avaliação
    - Conexões de saída nas portas 443 são permitidas
    - Se o status de conexão de migrações para Azure do SQL Agent no Azure migrar for ' Connected ' e verificar a última pulsação 
    - Se o status de conexão do Migrações para Azure para todas as instâncias do SQL for "Conectado" na folha da instância do SQL descoberta

    “Recalcule” a avaliação para refletir as alterações mais recentes na classificação de confiança.
- Alguns bancos de dados ou instâncias foram criadas durante o tempo durante o qual a avaliação foi calculada. Por exemplo, suponha que você criou uma avaliação para o histórico de desempenho do último mês, mas alguns bancos de dados ou instâncias foram criadas apenas uma semana atrás. Nesse caso, os dados de desempenho para os novos servidores não estarão disponíveis durante toda a duração e a classificação de confiança será baixa.

> [!NOTE]
> Como as avaliações de SQL do Azure são avaliações baseadas em desempenho, se a classificação de confiança de qualquer avaliação for menor que cinco estrelas, recomendamos que você aguarde pelo menos um dia para o dispositivo criar o perfil do ambiente e recalcular a avaliação. Caso contrário, o dimensionamento baseado em desempenho pode não ser confiável.

## <a name="calculate-monthly-costs"></a>Calcular custos mensais
Após a conclusão das recomendações de dimensionamento, a avaliação do SQL do Azure calcula os custos de computação e armazenamento para as configurações do SQL Azure recomendadas usando uma API de preços interna. Ele agrega o custo de computação e armazenamento em todas as instâncias para calcular o custo de computação mensal total. 
### <a name="compute-cost"></a>Custo de computação
- Para calcular o custo de computação para uma configuração de SQL do Azure, a avaliação considera as seguintes propriedades:
    - Benefício Híbrido do Azure para licenças do SQL
    - Capacidade reservada
    - Local de destino do Azure
    - Moeda
    - Programa de oferta/licenciamento
    - Desconto (%)

### <a name="storage-cost"></a>Custo de armazenamento
- As estimativas de custo de armazenamento incluem apenas arquivos de dados e não arquivos de log. 
- Para calcular o custo de armazenamento de uma configuração do SQL Azure, a avaliação considera as seguintes propriedades:
    - Local de destino do Azure
    - Moeda
    - Programa de oferta/licenciamento
    - Desconto (%)
- O custo de armazenamento de backup não está incluído na avaliação.
- **Banco de Dados SQL do Azure**
    - Um custo de armazenamento mínimo de 5 GB é adicionado na estimativa de custo e o custo de armazenamento adicional é adicionado para armazenamento em incrementos de 1 GB. [Saiba mais](https://azure.microsoft.com/pricing/details/sql-database/single/)
- **Instância Gerenciada do SQL do Azure**
    - Não há custo de armazenamento adicionado para o primeiro armazenamento de 32 GB/instância/mês, e o custo de armazenamento adicional é adicionado para armazenamento em incrementos de 32 GB. [Saiba mais](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)
        
## <a name="next-steps"></a>Próximas etapas
- [Examine](best-practices-assessment.md) as melhores práticas para a criação de avaliações. 
- Saiba como executar uma [avaliação do SQL do Azure](how-to-create-azure-sql-assessment.md).
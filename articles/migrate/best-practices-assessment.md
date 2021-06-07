---
title: Práticas recomendadas de avaliação na ferramenta de avaliação e descoberta de migrações para Azure
description: Dicas para criar avaliações com a ferramenta de avaliação e descoberta de migrações para Azure.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 1bf844dafe450e90213db2e447bb5392064eb245
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786763"
---
# <a name="best-practices-for-creating-assessments"></a>Práticas recomendadas para a criação de avaliações

As [Migrações para Azure](./migrate-services-overview.md) fornecem um hub de ferramentas que ajudam você a descobrir, avaliar e migrar aplicativos, a infraestrutura e cargas de trabalho para o Microsoft Azure. O hub inclui ferramentas das Migrações para Azure e ofertas de ISV (fornecedor independente de software) de terceiros.

Este artigo resume as práticas recomendadas ao criar avaliações usando a ferramenta de avaliação e descoberta de migrações para Azure.

Avaliações que você cria com as migrações para Azure: a ferramenta de descoberta e avaliação é um instantâneo de dados no momento. Há três tipos de avaliações que você pode criar usando as migrações para Azure: descoberta e avaliação:

**Tipo de avaliação** | **Detalhes**
--- | --- 
**VM do Azure** | Avaliações para migrar servidores locais para máquinas virtuais do Azure. <br/><br/> Você pode avaliar seus servidores locais no ambiente [VMware](how-to-set-up-appliance-vmware.md) e [Hyper-V](how-to-set-up-appliance-hyper-v.md) e [servidores físicos](how-to-set-up-appliance-physical.md) para migração para o Azure usando esse tipo de avaliação. [Saiba mais](concepts-assessment-calculation.md)
**SQL do Azure** | Avaliações para migrar seus SQL Servers locais de seu ambiente VMware para o banco de dados SQL do Azure ou o Azure SQL Instância Gerenciada. [Saiba mais](concepts-azure-sql-assessment-calculation.md)
**AVS (Solução VMware no Azure)** | Avaliações para migrar servidores locais para a [AVS (Solução VMware no Azure)](../azure-vmware/introduction.md). <br/><br/> Avalie as [VMs do VMware](how-to-set-up-appliance-vmware.md) locais para migração para a AVS (Solução VMware no Azure) usando esse tipo de avaliação. [Saiba mais](concepts-azure-vmware-solution-assessment-calculation.md)


### <a name="sizing-criteria"></a>Critérios de dimensionamento
Opções de dimensionamento de critérios nas avaliações de migração do Azure:

**Critérios de dimensionamento** | **Detalhes** | **Dados**
--- | --- | ---
**Com base no desempenho** | Avaliações que fazem recomendações com base nos dados de desempenho coletados | **Avaliação da VM do Azure**: a recomendação de tamanho da VM é baseada nos dados de utilização da CPU e de memória.<br/><br/> A recomendação de tipo de disco (HDD/SSD Standard ou discos gerenciados Premium) é baseada na IOPS e na taxa de transferência dos discos locais.<br/><br/>**Avaliação do SQL do Azure**: a configuração do SQL do Azure baseia-se nos dados de desempenho de instâncias e bancos de dado do SQL, o que inclui: utilização da CPU, utilização de memória, IOPS (arquivos de dados e de log), taxa de transferência e latência de operações de e/s<br/><br/>**Avaliação da AVS (Solução VMware no Azure)** : a recomendação de nós da AVS é baseada nos dados de utilização da CPU e de memória.
**No estado em que se encontra localmente** | Avaliações que não usam dados de desempenho para fazer recomendações. | **Avaliação da VM do Azure**: a recomendação de tamanho da VM é baseada no tamanho da VM local<br/><br> O tipo de disco recomendado é baseado no que você seleciona na configuração de tipo de armazenamento para a avaliação.<br/><br/> **Avaliação da AVS (Solução VMware no Azure)** : a recomendação de nós da AVS é baseada no tamanho da VM local.

#### <a name="example"></a>Exemplo
Por exemplo, se você tiver uma VM local com quatro núcleos a 20% de utilização e memória de 8 GB com utilização de 10%, a avaliação de VM do Azure será a seguinte:

- **Avaliação baseada em desempenho**:
    - Identifica os núcleos e a memória efetivos com base na utilização do núcleo (4 x 0,20 = 0,8) e da memória (8 GB x 0,10 = 0,8).
    - Aplica o fator de conforto especificado nas propriedades de avaliação (digamos, 1,3 x) para obter os valores a serem usados para o dimensionamento. 
    - Recomenda o tamanho da VM mais próximo no Azure que pode dar suporte à memória de ~ 1, 4 núcleos (0,8 x 1,3) e ~ 1, 4 GB (0,8 x 1,3).

- **Avaliação do as (como local)**:
    -  Recomenda uma VM com quatro núcleos; 8 GB de memória.


## <a name="best-practices-for-creating-assessments"></a>Práticas recomendadas para a criação de avaliações

O dispositivo de migrações para Azure cria perfis continuamente no seu ambiente local e envia metadados e dados de desempenho para o Azure. Siga estas práticas recomendadas para avaliações de servidores descobertos usando um dispositivo:

- **Criar como são avaliações**: você pode criar avaliações no estado em que se encontram imediatamente quando os servidores aparecerem no portal de migrações para Azure. Você não pode criar uma avaliação do SQL Azure com critérios de dimensionamento "como locais".
- **Criar avaliação baseada em desempenho**: depois de configurar a descoberta, recomendamos que você aguarde pelo menos um dia antes de executar uma avaliação baseada em desempenho:
    - Coletar dados de desempenho leva tempo. Aguardar pelo menos um dia garante que haja pontos de dados de desempenho suficientes antes de executar a avaliação.
    - Quando você estiver executando avaliações baseadas em desempenho, certifique-se de criar o perfil de seu ambiente para a duração da avaliação. Por exemplo, se você criar uma avaliação com uma duração de desempenho definida como uma semana, precisará aguardar pelo menos uma semana depois de iniciar a descoberta, para que todos os pontos de dados sejam coletados. Caso contrário, a avaliação não obterá uma classificação de cinco estrelas.
- **Recalcular avaliações**: como as avaliações são instantâneos de ponto no tempo, elas não são atualizadas automaticamente com os dados mais recentes. Para atualizar uma avaliação com os dados mais recentes, você precisa recalculá-lo.

Siga estas práticas recomendadas para avaliações de servidores importados para migrações para Azure via. Arquivo CSV:

- **Criar como são avaliações**: você pode criar avaliações no estado em que se encontram imediatamente quando os servidores aparecerem no portal de migrações para Azure.
- **Criar avaliação baseada em desempenho**: isso ajuda a obter uma estimativa de custo melhor, especialmente se você tiver a capacidade de servidor provisionada no local. No entanto, a precisão da avaliação baseada em desempenho depende dos dados de desempenho especificados por você para os servidores. 
- **Recalcular avaliações**: como as avaliações são instantâneos de ponto no tempo, elas não são atualizadas automaticamente com os dados mais recentes. Para atualizar uma avaliação com os dados importados mais recentes, você precisa recalculá-lo.
 
### <a name="ftt-sizing-parameters-for-avs-assessments"></a>Parâmetros de dimensionamento de FTT para avaliações de AVS

O mecanismo de armazenamento usado na AVS é a vSAN. As políticas de armazenamento vSAN definem os requisitos de armazenamento para suas máquinas virtuais. Essas políticas garantem o nível de serviço necessário para suas VMs, pois determinam como o armazenamento é alocado para a VM. Essas são as combinações disponíveis do FTT-RAID: 

**Falhas a tolerar (FTT)** | **Configuração RAID** | **Hosts mínimos necessários** | **Consideração de dimensionamento**
--- | --- | --- | --- 
1 | RAID-1 (espelhamento) | 3 | Uma VM de 100 GB consumiria 200 GB.
1 | RAID-5 (codificação de eliminação) | 4 | Uma VM de 100 GB consumiria 133,33 GB
2 | RAID-1 (espelhamento) | 5 | Uma VM de 100 GB consumiria 300 GB.
2 | RAID-6 (codificação de eliminação) | 6 | Uma VM de 100 GB consumiria 150 GB.
3 | RAID-1 (espelhamento) | 7 | Uma VM de 100 GB consumiria 400 GB.


## <a name="best-practices-for-confidence-ratings"></a>Práticas recomendadas para classificações de confiança

Quando você executa avaliações baseadas em desempenho, uma classificação de confiança de 1 estrela (mais baixa) a 5 estrelas (mais alta) é concedida à avaliação. Para usar classificações de confiança com eficiência:

- As avaliações de VM e AVS do Azure precisam de:
    - Os dados de utilização de CPU e memória para cada um dos servidores
    - Os dados de taxa de transferência/IOPS de leitura/gravação para cada disco anexado ao servidor local
    - Os dados de entrada/saída da rede para cada adaptador de rede anexado ao servidor.
     
- As avaliações do Azure SQL precisam dos dados de desempenho das instâncias SQL e dos bancos que estão sendo avaliados, que incluem:
    - Os dados de utilização de CPU e memória
    - Os dados de leitura/gravação de IOPS/taxa de transferência de dados e arquivos de log
    - A latência de operações de e/s

Dependendo da porcentagem de pontos de dados disponíveis para a duração selecionada, a classificação de confiança para uma avaliação é fornecida conforme resumido na tabela a seguir.

   **Disponibilidade do ponto de dados** | **Classificação de confiança**
   --- | ---
   0%-20% | 1 estrela
   21%-40% | 2 estrelas
   41%-60% | 3 estrelas
   61%-80% | 4 estrelas
   81%-100% | 5 estrelas


## <a name="common-assessment-issues"></a>Problemas comuns de avaliação

Veja como abordar alguns problemas de ambiente comuns que afetam as avaliações.

###  <a name="out-of-sync-assessments"></a>Avaliações fora de sincronia

Se você adicionar ou remover servidores de um grupo depois de criar uma avaliação, a avaliação criada será marcada **fora de sincronia**. Execute a avaliação novamente (**recalcule**) para refletir as alterações de grupo.

### <a name="outdated-assessments"></a>Avaliações desatualizadas

#### <a name="azure-vm-assessment-and-avs-assessment"></a>Avaliação de VMs do Azure e apuração de AVS
Se houver alterações nos servidores locais que estão em um grupo que foi avaliado, a avaliação será marcada como **desatualizada**. Uma avaliação pode ser marcada como "desatualizada" devido a uma ou mais alterações nas propriedades abaixo:
- Número de núcleos de processador
- Memória alocada
- Tipo de inicialização ou firmware
- Nome, versão e arquitetura do sistema operacional
- Número de discos
- Número de adaptadores de rede
- Alteração do tamanho do disco (GB alocados)
- Atualização das propriedades da NIC. Exemplo: alterações de endereço Mac, adição de endereço IP, etc.
    
Execute a avaliação novamente (**recalcule**) para refletir as alterações.
    
#### <a name="azure-sql-assessment"></a>Avaliação do SQL do Azure
Se houver alterações nas instâncias do SQL locais e nos bancos de dados que estão em um grupo que foi avaliado, a avaliação será marcada como **desatualizada**. Uma avaliação pode ser marcada como "desatualizada" devido a um ou mais motivos abaixo:
- A instância SQL foi adicionada ou removida de um servidor
- O banco de dados SQL foi adicionado ou removido de uma instância SQL
- Tamanho total do banco de dados em uma instância SQL alterada em mais de 20%
- Alteração no número de núcleos de processador
- Alteração na memória alocada        
  
    Execute a avaliação novamente (**recalcule**) para refletir as alterações.

### <a name="low-confidence-rating"></a>Classificação de baixa confiança

Uma avaliação pode não ter todos os pontos de dados por vários motivos:

- Você não criou o perfil do ambiente pelo tempo para o qual está criando a avaliação. Por exemplo, se você está criando uma avaliação com duração de desempenho definida como uma semana, precisa aguardar pelo menos uma semana após iniciar a descoberta para que todos os pontos de dados sejam coletados. Se não puder esperar tanto tempo, altere a execução para um período menor e “recalcule” a avaliação.
 
- A avaliação não é capaz de coletar os dados de desempenho de alguns ou de todos os servidores no período de avaliação. Para uma classificação de alta confiança, verifique se: 
    - Os servidores estão ligados durante a avaliação
    - Conexões de saída nas portas 443 são permitidas
    - Para servidores Hyper-V, a memória dinâmica está habilitada 
    - O status de conexão dos agentes nas migrações para Azure são "conectados" e verifica a última pulsação
    - Para avaliações de SQL do Azure, o status de conexão de migrações para Azure para todas as instâncias do SQL é "conectado" na folha da instância do SQL descoberta

    “Recalcule” a avaliação para refletir as alterações mais recentes na classificação de confiança.

- Para avaliações de VM do Azure e AVS, poucos servidores foram criados após o início da descoberta. Por exemplo, se você estiver criando uma avaliação para o histórico de desempenho do último mês, mas poucos servidores foram criados no ambiente apenas uma semana atrás. Nesse caso, os dados de desempenho para os novos servidores não estarão disponíveis durante toda a duração e a classificação de confiança será baixa.

- Para avaliações do SQL do Azure, poucas instâncias ou bancos de dados SQL foram criados após o início da descoberta. Por exemplo, se você estiver criando uma avaliação para o histórico de desempenho do último mês, mas poucas instâncias ou bancos de dados SQL foram criados no ambiente apenas uma semana atrás. Nesse caso, os dados de desempenho para os novos servidores não estarão disponíveis durante toda a duração e a classificação de confiança será baixa.

### <a name="migration-tool-guidance-for-avs-assessments"></a>Diretrizes da ferramenta de migração para avaliações da AVS

No relatório de Preparação para o Azure para a avaliação da Solução VMware no Azure (AVS), você pode ver as seguintes sugestões de ferramentas: 
- **VMware HCX ou Enterprise**: para servidores VMware, a solução de HCX (extensão de nuvem híbrida) do VMware é a ferramenta de migração sugerida para migrar sua carga de trabalho local para sua nuvem privada da AVS (solução VMware) do Azure. [Saiba mais](../azure-vmware/tutorial-deploy-vmware-hcx.md).
- **Desconhecido**: para servidores importados por meio de um arquivo CSV, a ferramenta de migração padrão é desconhecida. No entanto, para servidores no ambiente VMware, é recomendável usar a solução de HCX (extensão de nuvem híbrida) do VMware.


## <a name="next-steps"></a>Próximas etapas

- [Saiba](concepts-assessment-calculation.md) como as avaliações são calculadas.
- [Saiba](how-to-modify-assessment.md) como personalizar uma avaliação.

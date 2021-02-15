---
title: Práticas recomendadas de avaliação na avaliação do servidor de migrações para Azure
description: Dicas para criar avaliações com a avaliação de servidor de migrações para Azure.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: c1fff5b5b7f6450ad8d1977e55a1f6b255f3d668
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754310"
---
# <a name="best-practices-for-creating-assessments"></a>Práticas recomendadas para a criação de avaliações

As [Migrações para Azure](./migrate-services-overview.md) fornecem um hub de ferramentas que ajudam você a descobrir, avaliar e migrar aplicativos, a infraestrutura e cargas de trabalho para o Microsoft Azure. O hub inclui ferramentas das Migrações para Azure e ofertas de ISV (fornecedor independente de software) de terceiros.

Este artigo resume as práticas recomendadas ao criar avaliações usando a ferramenta de avaliação do servidor de migrações para Azure.

## <a name="about-assessments"></a>Sobre avaliações

As avaliações criadas com a avaliação do servidor de migrações para Azure são um instantâneo de dados pontual. Há dois tipos de avaliações que você pode criar usando as migrações para Azure: avaliação do servidor:

**Tipo de avaliação** | **Detalhes**
--- | --- 
**VM do Azure** | Avaliações para migrar servidores locais para máquinas virtuais do Azure. <br/><br/> Avalie as [VMs do VMware](how-to-set-up-appliance-vmware.md), [VMs do Hyper-V](how-to-set-up-appliance-hyper-v.md) e [servidores físicos](how-to-set-up-appliance-physical.md) locais para migração para o Azure usando esse tipo de avaliação. [Saiba mais](concepts-assessment-calculation.md)
**AVS (Solução VMware no Azure)** | Avaliações para migrar servidores locais para a [AVS (Solução VMware no Azure)](../azure-vmware/introduction.md). <br/><br/> Avalie as [VMs do VMware](how-to-set-up-appliance-vmware.md) locais para migração para a AVS (Solução VMware no Azure) usando esse tipo de avaliação. [Saiba mais](concepts-azure-vmware-solution-assessment-calculation.md)


### <a name="sizing-criteria"></a>Critérios de dimensionamento
A avaliação do servidor fornece duas opções de critérios de dimensionamento:

**Critérios de dimensionamento** | **Detalhes** | **Dados**
--- | --- | ---
**Com base no desempenho** | Avaliações que fazem recomendações com base nos dados de desempenho coletados | **Avaliação da VM do Azure**: a recomendação de tamanho da VM é baseada nos dados de utilização da CPU e de memória.<br/><br/> A recomendação de tipo de disco (HDD/SSD Standard ou discos gerenciados Premium) é baseada na IOPS e na taxa de transferência dos discos locais.<br/><br/> **Avaliação da AVS (Solução VMware no Azure)** : a recomendação de nós da AVS é baseada nos dados de utilização da CPU e de memória.
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

- **Criar como são avaliações**: você pode criar avaliações no estado em que se encontram imediatamente quando os computadores aparecerem no portal de migrações para Azure.
- **Criar avaliação baseada em desempenho**: depois de configurar a descoberta, recomendamos que você aguarde pelo menos um dia antes de executar uma avaliação baseada em desempenho:
    - Coletar dados de desempenho leva tempo. Aguardar pelo menos um dia garante que haja pontos de dados de desempenho suficientes antes de executar a avaliação.
    - Quando você estiver executando avaliações baseadas em desempenho, certifique-se de criar o perfil de seu ambiente para a duração da avaliação. Por exemplo, se você criar uma avaliação com uma duração de desempenho definida como uma semana, precisará aguardar pelo menos uma semana depois de iniciar a descoberta, para que todos os pontos de dados sejam coletados. Caso contrário, a avaliação não obterá uma classificação de cinco estrelas.
- **Recalcular avaliações**: como as avaliações são instantâneos de ponto no tempo, elas não são atualizadas automaticamente com os dados mais recentes. Para atualizar uma avaliação com os dados mais recentes, você precisa recalculá-lo.

Siga estas práticas recomendadas para avaliações de servidores importados para migrações para Azure via. Arquivo CSV:

- **Criar como são avaliações**: você pode criar avaliações no estado em que se encontram imediatamente quando os computadores aparecerem no portal de migrações para Azure.
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
- A avaliação de servidor de migrações para Azure precisa dos dados de utilização para CPU/memória da VM.
- Para cada disco anexado à VM local, ele precisa dos dados de leitura/gravação de IOPS/taxa de transferência.
- Para cada adaptador de rede anexado à VM, ele precisa dos dados de entrada/saída da rede.

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

Se você adicionar ou remover computadores de um grupo depois de criar uma avaliação, a avaliação criada será marcada **fora de sincronia**. Execute a avaliação novamente (**recalcule**) para refletir as alterações de grupo.

### <a name="outdated-assessments"></a>Avaliações desatualizadas

Se houver alterações locais nas VMs que estão em um grupo que foi avaliado, a avaliação será marcada como **desatualizada**. Uma avaliação pode ser marcada como "desatualizada" devido a uma ou mais alterações nas propriedades abaixo:

- Número de núcleos de processador
- Memória alocada
- Tipo de inicialização ou firmware
- Nome, versão e arquitetura do sistema operacional
- Número de discos
- Número de adaptadores de rede
- Alteração do tamanho do disco (GB alocados)
- Atualização das propriedades da NIC. Exemplo: alterações de endereço Mac, adição de endereço IP, etc.

Execute a avaliação novamente (**recalcule**) para refletir as alterações.

### <a name="low-confidence-rating"></a>Classificação de baixa confiança

Uma avaliação pode não ter todos os pontos de dados por vários motivos:

- Você não criou o perfil do ambiente pelo tempo para o qual está criando a avaliação. Por exemplo, se você estiver criando uma *avaliação baseada em desempenho* com duração de desempenho definida como uma semana, você precisará aguardar pelo menos uma semana depois de iniciar a descoberta para todos os pontos de dados a serem coletados. Você sempre pode clicar em **Recalcular** para ver a classificação de confiança mais recente aplicável. A classificação de confiança é aplicável somente quando você cria uma avaliação *baseada em desempenho* .

- Algumas VMs foram desativadas durante o período para o qual a avaliação é calculada. Se algumas VMs foram desligadas por algum tempo, a Avaliação de Servidor não poderá coletar os dados de desempenho daquele período.

- Algumas VMs foram criadas após o início da descoberta na Avaliação de Servidor. Por exemplo, se você estiver criando uma avaliação para o histórico de desempenho do último mês, mas algumas VMs foram criadas no ambiente somente há uma semana. Nesse caso, os dados de desempenho das novas VMs não estariam disponíveis durante todo o período e a classificação de confiança seria baixa.

### <a name="migration-tool-guidance-for-avs-assessments"></a>Diretrizes da ferramenta de migração para avaliações da AVS

No relatório de Preparação para o Azure para a avaliação da Solução VMware no Azure (AVS), você pode ver as seguintes sugestões de ferramentas: 
- **VMware HCX ou Enterprise**: para máquinas VMware, a solução de HCX (extensão de nuvem híbrida) do VMware é a ferramenta de migração sugerida para migrar sua carga de trabalho local para sua nuvem privada da AVS (solução VMware) do Azure. [Saiba mais](../azure-vmware/tutorial-deploy-vmware-hcx.md).
- **Desconhecido**: Para computadores importados por meio de um arquivo CSV, a ferramenta de migração padrão é desconhecida. No entanto, para máquinas VMware, é recomendável usar a solução de HCX (extensão de nuvem híbrida) do VMware.


## <a name="next-steps"></a>Próximas etapas

- [Saiba](concepts-assessment-calculation.md) como as avaliações são calculadas.
- [Saiba](how-to-modify-assessment.md) como personalizar uma avaliação.

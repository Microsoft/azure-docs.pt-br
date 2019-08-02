---
title: Práticas recomendadas para a criação de avaliação com a avaliação do servidor de migrações para Azure | Microsoft Docs
description: Fornece dicas para criar avaliações com a avaliação de servidor de migrações para Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: 18b82b5553f7045c38c9de532199c2a0fd815ee1
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234310"
---
# <a name="best-practices-for-creating-assessments"></a>Práticas recomendadas para a criação de avaliações

As migrações para [Azure](migrate-overview.md) fornecem um hub de ferramentas que ajudam a descobrir, avaliar e migrar aplicativos, infraestrutura e cargas de trabalho para Microsoft Azure. O Hub inclui ferramentas de migração do Azure e ofertas de fornecedores independentes de software (ISV) de terceiros.

Este artigo resume as práticas recomendadas ao criar avaliações usando a ferramenta de avaliação do servidor de migrações para Azure.

## <a name="about-assessments"></a>Sobre avaliações

As avaliações criadas com a avaliação do servidor de migrações para Azure são um instantâneo de dados pontual. Há dois tipos de avaliações nas migrações para Azure.

**Tipo de avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Baseado em desempenho** | Avaliações que fazem recomendações com base nos dados de desempenho coletados | A recomendação de tamanho da VM baseia-se nos dados de utilização de CPU e memória.<br/><br/> A recomendação de tipo de disco (HD padrão/SSD ou discos gerenciados Premium) baseia-se na IOPS e na taxa de transferência dos discos locais.
**No estado em que se encontra no local** | Avaliações que não usam dados de desempenho para fazer recomendações. | A recomendação de tamanho da VM é baseada no tamanho da VM local<br/><br> O tipo de disco recomendado é baseado no que você seleciona na configuração de tipo de armazenamento para a avaliação.

### <a name="example"></a>Exemplo
Por exemplo, se você tiver uma VM local com quatro núcleos a 20% de utilização e memória de 8 GB com utilização de 10%, as avaliações serão as seguintes:

- **Avaliação baseada em desempenho**:
    - Identifica os núcleos e a memória efetivos com base na utilização do núcleo (4 x 0,20 = 0,8) e da memória (8 GB x 0,10 = 0,8).
    - Aplica o fator de conforto especificado nas propriedades de avaliação (le'ts, por exemplo, 1,3 x) para obter os valores a serem usados para o dimensionamento. 
    - Recomenda o tamanho da VM mais próximo no Azure que pode dar suporte à memória de ~ 1,4 núcleos (0,8 x 1,3) e ~ 1,4 GB (0,8 x 1,3).

- **Avaliação do as (como local)** :
    -  Recomenda uma VM com quatro núcleos; 8 GB de memória.

## <a name="best-practices-for-creating-assessments"></a>Práticas recomendadas para a criação de avaliações

O dispositivo de migrações para Azure cria perfis continuamente no seu ambiente local e envia metadados e dados de desempenho para o Azure. Siga estas práticas recomendadas para criar avaliações:

- **Criar como são avaliações**: Você pode criar avaliações no estado em que se encontram imediatamente quando as máquinas aparecem no portal de migrações para Azure.
- **Criar avaliação baseada em desempenho**: Depois de configurar a descoberta, recomendamos que você aguarde pelo menos um dia antes de executar uma avaliação baseada em desempenho:
    - Coletar dados de desempenho leva tempo. Aguardar pelo menos um dia garante que haja pontos de dados de desempenho suficientes antes de executar a avaliação.
    - Quando você estiver executando avaliações baseadas em desempenho, certifique-se de criar o perfil de seu ambiente para a duração da avaliação. Por exemplo, se você criar uma avaliação com uma duração de desempenho definida como uma semana, precisará aguardar pelo menos uma semana depois de iniciar a descoberta, para que todos os pontos de dados sejam coletados. Caso contrário, a avaliação não obterá uma classificação de cinco estrelas.
- **Recalcular avaliações**: Como as avaliações são instantâneos de ponto no tempo, elas não são atualizadas automaticamente com os dados mais recentes. Para atualizar uma avaliação com os dados mais recentes, você precisa recalculá-lo.

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

Se houver alterações locais nas VMs que estão em um grupo que foi avaliado, a avaliação será marcada como **desatualizada**. Para refletir as alterações, execute a avaliação novamente.

### <a name="low-confidence-rating"></a>Classificação de baixa confiança

Uma avaliação pode não ter todos os pontos de dados por vários motivos:

- Você não criou o perfil do ambiente pelo tempo para o qual está criando a avaliação. Por exemplo, se você estiver criando uma *avaliação baseada em desempenho* com duração de desempenho definida como uma semana, você precisará aguardar pelo menos uma semana depois de iniciar a descoberta para todos os pontos de dados a serem coletados. Você sempre pode clicar em **Recalcular** para ver a classificação de confiança mais recente aplicável. A classificação de confiança é aplicável somente quando você cria uma avaliação *baseada em desempenho* .

- Algumas VMs foram desativadas durante o período para o qual a avaliação é calculada. Se algumas VMs foram desligadas por alguma duração, a avaliação do servidor não poderá coletar os dados de desempenho para esse período.

- Algumas VMs foram criadas após o início da descoberta na avaliação do servidor. Por exemplo, se você estiver criando uma avaliação para o histórico de desempenho do último mês, mas algumas VMs foram criadas no ambiente somente há uma semana. Nesse caso, os dados de desempenho para as novas VMs não estarão disponíveis durante toda a duração e a classificação de confiança será baixa.


## <a name="next-steps"></a>Próximas etapas

- [Saiba](concepts-assessment-calculation.md) como as avaliações são calculadas.
- [Saiba](how-to-modify-assessment.md) como personalizar uma avaliação.

---
title: Plataforma Data Lake Storage e WANdisco LiveData para o Azure (versão prévia)
description: Migre dados do Hadoop locais para Azure Data Lake Storage Gen2 usando a plataforma WANdisco LiveData para o Azure.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.reviewer: b-pauls
ms.date: 11/17/2020
ms.service: storage
ms.custom: references_regions
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: a0d02530ba2b8758b467b77ff639437675e4cc81
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99508922"
---
# <a name="meet-demanding-migration-requirements-with-wandisco-livedata-platform-for-azure-preview"></a>Atenda aos exigentes requisitos de migração com a plataforma WANdisco LiveData para o Azure (versão prévia)

Migre dados do Hadoop locais para Azure Data Lake Storage Gen2 usando a [plataforma WANdisco LiveData para o Azure](https://docs.wandisco.com/live-data-platform/docs/landing/). Essa plataforma elimina a necessidade de tempo de inatividade do aplicativo, remove a chance de perda de dados e garante a consistência dos dados mesmo que as operações continuem localmente.  

> [!NOTE]
> A plataforma WANdisco LiveData para o Azure está em visualização pública. Para disponibilidade regional, consulte [regiões com suporte](https://docs.wandisco.com/live-data-platform/docs/prereq#supported-regions).

A plataforma consiste em dois serviços: o [LiveData Migrator for Azure](https://www.wandisco.com/products/livedata-migrator-for-azure) para migrar dados usados ativamente de ambientes locais para o armazenamento do Azure e o [plano LiveData para o Azure](https://www.wandisco.com/products/livedata-plane-for-azure) , que garante que todos os dados modificados ou dados de ingestão sejam replicados de forma consistente. 

> [!div class="mx-imgBorder"]
> ![Ilustração de visão geral da plataforma de dados dinâmicos](./media/migrate-gen2-wandisco-live-data-platform/live-data-platform-overview.png)

Você pode gerenciar ambos os serviços usando o portal do Azure e o CLI do Azure, e ambos seguem o mesmo modelo de cobrança pago conforme o uso que todos os outros serviços do Azure. A plataforma LiveData para o consumo do Azure será exibida na mesma fatura mensal do Azure e fornecerá uma maneira consistente e conveniente de controlar e monitorar seu uso.

Ao contrário da migração de dados _offline_ [copiando informações estáticas para Azure data Box](./data-lake-storage-migrate-on-premises-hdfs-cluster.md)ou usando ferramentas do Hadoop como o [DistCp](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html), você pode manter a operação completa de seus sistemas de negócios durante a migração _online_ com o WANdisco LiveData para o Azure. Mantenha seus ambientes de Big Data operando mesmo ao mover seus dados para o Azure.

## <a name="key-features-of-wandisco-livedata-platform-for-azure"></a>Principais recursos da plataforma WANdisco LiveData para o Azure

A [plataforma WANdisco LiveData para o Azure](https://docs.wandisco.com/live-data-platform/docs/landing/) usa um mecanismo de consenso com capacidade de rede de longa distância exclusivo para obter a consistência dos dados e para conduzir a replicação de dados em escala, enquanto os aplicativos podem continuar modificando os dados em replicação. <br><br>

>[!VIDEO https://www.youtube.com/embed/KRrmcYPxEho] 

Os principais recursos da plataforma incluem o seguinte:

- **Consistência de dados**: solucione os desafios de migrar grandes volumes de dados entre ambientes e manter esses dados consistentes em migração de taxa de transferência de sistemas de armazenamento, mesmo que estejam sob alteração contínua. Empregue o único mecanismo de consenso compatível com a rede de área de longa distância do WANdisco diretamente no Azure para obter a consistência dos dados e migrar dados com garantias de consistência durante as alterações de dados.

- **Manter operações**: como os aplicativos podem continuar a criar, modificar, ler e excluir dados durante a migração, não há necessidade de interromper as operações de negócios ou introduzir uma janela de interrupção apenas para migrar Big data para o Azure. Continue a operar aplicativos, infraestrutura de análise, trabalhos de ingestão e outros processamentos.

- **Validar resultados**: validação de ponta a ponta que seus dados podem ser usados com eficiência uma vez migrados para o Azure requer que você execute cargas de trabalho de aplicativo de produção em relação a eles. Apenas um serviço LiveData fornece isso sem introduzir o risco de divergência de dados, mantendo a consistência dos dados, independentemente de a alteração ocorrer na origem ou no destino de sua migração. Teste e valide o comportamento do aplicativo sem riscos ou altere para seus processos e sistemas.

- **Reduzir a complexidade**: Elimine a necessidade de criar e gerenciar trabalhos agendados para copiar dados migrando dados por meio da automação. Use a profunda integração com o Azure como um plano de controle para gerenciar e monitorar o progresso da migração, incluindo replicação seletiva de dados, metadados do hive, segurança de dados e confidencialidade.

- **Eficiência**: manter a alta taxa de transferência e o desempenho e dimensionar facilmente Big data volumes. Com o controle do consumo de largura de banda, você pode garantir que possa atender às suas metas de migração sem afetar outras operações do sistema.

## <a name="migrate-big-data-faster-without-risk"></a>Migre Big Data mais rápido sem riscos

O primeiro serviço da plataforma WANdisco LiveData para o Azure é o [LiveData Migrator para Azure](https://www.wandisco.com/products/livedata-migrator-for-azure); uma solução para migrar dados usados ativamente de ambientes locais para o armazenamento do Azure. O LiveData Migrator for Azure é provisionado e gerenciado inteiramente do portal do Azure ou CLI do Azure e opera com o cluster Hadoop local sem nenhuma alteração de configuração, modificação de aplicativo ou reinicialização do serviço para começar a migrar os dados imediatamente.

> [!div class="mx-imgBorder"]
> ![Arquitetura do LiveData Migrator para Azure](./media/migrate-gen2-wandisco-live-data-platform/live-data-migrator-architecture.png)

As migrações de Big data podem ser complexas e desafiadoras. A movimentação de petabytes de informações sem interromper as operações de negócios foi impossível alcançar com tecnologias de cópia de dados offline. O [LiveData Migrator para Azure](https://www.wandisco.com/products/livedata-migrator-for-azure) oferece implantação simples e pode estabelecer um serviço LiveData com migração contínua de dados e replicação, enquanto os aplicativos lêem, gravam e modificam os dados que estão sendo migrados.

A execução de uma migração é tão simples quanto estas três etapas:

1. Provisione a instância do LiveData Migrator do portal do Azure para seu cluster Hadoop local. Nenhuma alteração de cluster ou tempo de inatividade é necessária e os aplicativos podem continuar a operar.

   > [!div class="mx-imgBorder"]
   >![Criar uma instância do LiveData Migrator](./media/migrate-gen2-wandisco-live-data-platform/create-live-data-migrator.png)

2. Defina a conta de armazenamento habilitada para Azure Data Lake Storage Gen2 de destino.

   > [!div class="mx-imgBorder"]
   >![Criar um destino do LiveData Migrator](./media/migrate-gen2-wandisco-live-data-platform/create-target.png)

3. Defina o local dos dados que você deseja migrar, por exemplo: `/user/hive/warehouse` , e inicie a migração.

   > [!div class="mx-imgBorder"]
   > ![Criar uma migração do LiveData Migrator](./media/migrate-gen2-wandisco-live-data-platform/create-migration.png)

Monitore seu progresso de migração por meio de ferramentas padrão do Azure, incluindo o CLI do Azure e portal do Azure, e continue a usar seu ambiente local em todo o. Antes de começar, examine esses [pré-requisitos](https://docs.wandisco.com/live-data-platform/docs/prereq/).

## <a name="replicate-data-under-active-change"></a>Replicar dados sob alteração ativa

As migrações em larga escala de data lagos locais para o Azure precisam de testes e validação de aplicativos. Ser capaz de fazer isso sem o risco de introduzir alterações de dados que criarão várias fontes de verdade que não podem ser facilmente reconciliadas é essencial para eliminar o risco e minimizar o custo de migrar para o Azure. O [plano LiveData para o Azure](https://www.wandisco.com/products/livedata-plane-for-azure) usa a tecnologia de mecanismo de coordenação do WANdisco para superar essas preocupações.

> [!div class="mx-imgBorder"]
> ![Plano de LiveData para a arquitetura do Azure](./media/migrate-gen2-wandisco-live-data-platform/live-data-plane-architecture.png)

Mantenha seus dados consistentes em clusters Hadoop locais e no armazenamento do Azure com o plano LiveData para o Azure após a migração inicial:

1. Provisione o plano LiveData para o Azure local e no Azure, a partir do portal do Azure. Nenhuma alteração de aplicativo é necessária.

2. Configure as regras de replicação que abrangem os locais de dados que você deseja manter consistentes, por exemplo: `/user/contoso/sales/region/WA` .

3. Execute aplicativos que acessam e modificam dados em qualquer local como um sistema de arquivos compatível com Hadoop conforme necessário.

O plano LiveData para o Azure mantém seus dados consistentes sem impor sobrecarga significativa na operação do cluster ou no desempenho do aplicativo. Modificar ou ingerir dados enquanto todas as alterações são replicadas consistentemente.

## <a name="next-steps"></a>Próximas etapas

- [A plataforma LiveData para o Azure](https://docs.wandisco.com/live-data-platform/docs/landing/) para Azure é usada como qualquer outro recurso do Azure e está disponível em visualização agora. 

- Entenda os [pré-requisitos](https://docs.wandisco.com/live-data-platform/docs/prereq/), planeje sua migração e conclua rapidamente uma migração em larga escala com o LiveData Migrator para Azure.

- Experimente o LiveData migrador sem precisar ter um cluster Hadoop local usando a [área restrita do HDFS](https://docs.wandisco.com/live-data-platform/docs/create-sandbox-intro/).

## <a name="see-also"></a>Veja também

- [LiveData Migrator para Azure no Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldm?tab=Overview)

- [Plano do LiveData para o Azure no Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldp?tab=Overview)

- [Preços e planos do LiveData Migrator para Azure](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldm?tab=PlansAndPrice)

- [Plano de LiveData para preços e planos do Azure](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldp?tab=PlansAndPrice) 

- [LiveData Platform para perguntas frequentes sobre o Azure](https://docs.wandisco.com/live-data-platform/docs/faq/)

- [Problemas conhecidos com a plataforma LiveData para o Azure](https://docs.wandisco.com/live-data-platform/docs/known-issues/)

- [Introdução ao Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)
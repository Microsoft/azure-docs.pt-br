---
title: Criar um plano de migração com as migrações para Azure
description: Fornece orientação sobre como criar um plano de migração com as migrações para Azure.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/11/2020
ms.openlocfilehash: 1c2be47060004d464003c00cbbddb3b58a136e3c
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871139"
---
# <a name="build-migration-plan-with-azure-migrate"></a>Criar um plano de migração com as Migrações para Azure

Siga este artigo para criar seu plano de migração para o Azure com as [migrações para Azure](migrate-services-overview.md). 

## <a name="define-cloud-migration-goals"></a>Definir metas de migração de nuvem

Antes de começar, compreender e avaliar sua [motivação](/azure/cloud-adoption-framework/strategy/motivations) para mudar para a nuvem pode contribuir para um resultado comercial bem-sucedido. Conforme explicado na [estrutura de adoção de nuvem](/azure/cloud-adoption-framework), há vários gatilhos e resultados.   

**Evento de negócios** | **Resultado da migração**
--- | ---
Saída do datacenter | Cost 
Fusão, aquisição ou alienação | Redução na complexidade do fornecedor/técnica
Redução nas despesas de capital | Otimização de operações internas
Fim do suporte para tecnologias críticas | Aumento da agilidade dos negócios
Resposta a alterações de conformidade regulatória | Preparação para novas funcionalidades técnicas
Novos requisitos de soberania de dados | Escala conforme as demandas de mercado
Redução de interrupções e melhorias de estabilidade de ti | Escala conforme as demandas geográficas

Identificar sua motivação ajuda você a fixar suas metas de migração estratégica. A próxima etapa é identificar e planejar um caminho de migração adequado para suas cargas de trabalho. A ferramenta [migrações para Azure: descoberta e avaliação](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) ajuda a avaliar cargas de trabalho locais e fornece orientações e ferramentas para ajudá-lo a migrar.

## <a name="understand-your-digital-estate"></a>Compreender seu patrimônio digital

Comece identificando a infraestrutura, os aplicativos e as dependências locais. Isso ajuda a identificar cargas de trabalho para migração para o Azure e a reunir projeções de custos otimizados. A ferramenta de descoberta e avaliação ajuda a identificar as cargas de trabalho que você tem em uso, as dependências entre as cargas de trabalho e a otimização da carga de trabalho.

### <a name="workloads-in-use"></a>Cargas de trabalho em uso

As migrações para Azure usam um dispositivo leve de migrações para Azure para executar a descoberta sem agente de VMs VMware locais, VMs do Hyper-V, outros servidores virtualizados e servidores físicos. A descoberta contínua coleta informações de configuração do servidor e metadados de desempenho, bem como dados de aplicativos. Veja o que o dispositivo coleta de servidores locais: 

- Metadados de servidor, disco e NIC.

- Aplicativos instalados, funções e recursos.

- Dados de desempenho, incluindo utilização de CPU e memória, IOPS de disco e taxa de transferência.

Depois de coletar dados, você pode exportar a lista de inventário de aplicativos para localizar aplicativos e SQL Server instâncias em execução em seus servidores. Você pode usar a ferramenta Avaliação de Banco de Dados das Migrações para Azure para compreender a preparação do SQL Server.

 ![Inventário de aplicativos no Portal](./media/concepts-migration-planning/application-inventory-portal.png)

 ![Exportação de inventário de aplicativos](./media/concepts-migration-planning/application-inventory-export.png)

Junto com os dados descobertos com a ferramenta de descoberta e avaliação, você pode usar seus dados de CMDB (banco de dados de gerenciamento de configuração) para criar uma exibição do seu servidor e do banco do dados e entender como os servidores são distribuídos entre unidades de negócios, proprietários de aplicativos, geografias, etc. Isso ajuda a decidir quais cargas de trabalho priorizar para a migração. 

### <a name="dependencies-between-workloads"></a>Dependências entre cargas de trabalho

Após a descoberta do servidor, você pode [analisar as dependências](concepts-dependency-visualization.md) para visualizar e identificar dependências entre servidores e estratégias de otimização a fim de mover servidores interdependentes para o Azure. A visualização ajuda a entender se determinados servidores estão em uso ou se eles podem ser descomissionados, em vez de serem migrados.  A análise das dependências ajuda a garantir que nada seja deixado para trás e a evitar interrupções durante a migração. Com o inventário do aplicativo e a análise de dependência concluídos, você pode criar grupos de servidores de alta confiança e começar a avaliá-los.

 ![Mapeamento de dependências](./media/concepts-migration-planning/expand-client-group.png)

### <a name="optimization-and-sizing"></a>Otimização e dimensionamento

O Azure fornece flexibilidade para redimensionar sua capacidade de nuvem ao longo do tempo, e a migração oferece uma oportunidade para você otimizar os recursos de CPU e memória alocados para seus servidores. Criar uma avaliação nos servidores identificados ajuda a entender o histórico de desempenho da carga de trabalho. Isso é crucial para o dimensionamento correto dos SKUs da VM do Azure e recomendações de disco no Azure.

## <a name="assess-migration-readiness"></a>Avaliar a preparação da migração


### <a name="readinesssuitability-analysis"></a>Análise de adequação/preparação

Você pode exportar o relatório de avaliação e filtrá-lo nestas categorias a fim de compreender a preparação para o Azure:

- **Pronto para o Azure**: os servidores podem ser migrados no estado em que se encontram para o Azure, sem nenhuma alteração. 
- **Condicionalmente pronto para o Azure**: os servidores podem ser migrados para o Azure, mas precisam de pequenas alterações, de acordo com as diretrizes de correção fornecidas na avaliação.
- **Não está pronto para o Azure**: os servidores não podem ser migrados para o Azure no estado em que se encontram. Os problemas devem ser corrigidos de acordo com as diretrizes de correção, antes da migração. 
- **Preparação desconhecida**: as migrações para Azure não podem determinar a preparação do servidor, devido a metadados insuficientes.

Usando avaliações de banco de dados, você pode avaliar a preparação de seu acervo de dados do SQL Server para migração para o Banco de Dados SQL do Azure ou as Instâncias Gerenciadas de SQL do Azure. A avaliação mostra a porcentagem do status de preparação para a migração de cada uma das instâncias do SQL Server. Além disso, para cada instância, você pode ver o destino recomendado no Azure, possíveis bloqueadores de migração, uma contagem de alterações interruptivas, preparação para o BD ou a VM do SQL do Azure e um nível de compatibilidade. Você pode se aprofundar para entender o impacto dos bloqueadores de migração e as recomendações para corrigi-los.

 ![Avaliações de banco de dados](./media/concepts-migration-planning/database-assessment-portal.png)

### <a name="sizing-recommendations"></a>Recomendações de dimensionamento

Depois que um servidor é marcado como pronto para o Azure, a descoberta e a avaliação fazem recomendações de dimensionamento que identificam a SKU e o tipo de disco da VM do Azure para seus servidores. Você pode obter recomendações de dimensionamento com base no histórico de desempenho (para otimizar os recursos à medida que migra) ou com base nas configurações do servidor local, sem o histórico de desempenho. Em uma avaliação de banco de dados, você pode ver recomendações para o SKU do banco de dados, o tipo de preço e o nível de computação.  

### <a name="get-compute-costs"></a>Obter custos de computação

A opção de dimensionamento com base no desempenho nas avaliações das Migrações para Azure ajuda você a dimensionar as VMs e deve ser usada como uma melhor prática para otimizar cargas de trabalho no Azure. Além do dimensionamento correto, há algumas outras opções para ajudar a economizar custos do Azure: 

- **Instâncias reservadas**: com as [RIs (instâncias reservadas)](https://azure.microsoft.com/pricing/reserved-vm-instances/), você pode reduzir significativamente os custos em comparação com o [pagamento conforme o uso](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).
- **Benefício Híbrido do Azure**: com o [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/), você pode usar licenças locais do Windows Server com assinaturas ativas do Software Assurance ou do Linux no Azure e combinar com as opções de instâncias reservadas.
- **Enterprise Agreement**: os [EAs (Enterprise Agreements)](../cost-management-billing/manage/ea-portal-agreements.md) do Azure podem oferecer economia para assinaturas e serviços do Azure.
- **Ofertas**: há várias [ofertas do Azure](https://azure.microsoft.com/support/legal/offer-details/). Por exemplo, [Desenvolvimento/Teste Pago Conforme o Uso](https://azure.microsoft.com/pricing/dev-test/) ou [Desenvolvimento/Teste Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/), para fornecer taxas mais baixas em VMs de desenvolvimento/teste
- **Tempo de atividade da VM**: você pode examinar os dias por mês e as horas por dia em que as VMs do Azure são executadas. Desligar os servidores quando eles não estão em uso pode reduzir seus custos (não aplicável ao RIs).
- **Região de destino**: você pode criar avaliações em diferentes regiões para descobrir se a migração para uma região específica pode ser mais econômica. 

### <a name="visualize-data"></a>Visualizar dados

Você pode exibir relatórios de descoberta e avaliação (com as informações de preparação do Azure e a distribuição de custo mensal) no Portal. Você também pode exportar avaliações e enriquecer seu plano de migração com visualizações adicionais. É possível criar várias avaliações, com diferentes combinações de propriedades, e escolher o conjunto de propriedades que funciona melhor para seu negócio.  

 ![Visão geral de avaliações](./media/concepts-migration-planning/assessment-summary.png)

### <a name="evaluate-gapsblockers"></a>Avaliar lacunas/bloqueadores

Conforme você descobre os aplicativos e as cargas de trabalho que deseja migrar, identifique as restrições de tempo de inatividade para eles e procure por dependências operacionais entre seus aplicativos e a infraestrutura subjacente. Essa análise ajuda você a planejar migrações que atendam ao seu RTO (objetivo de tempo de recuperação) e garantam pouca ou nenhuma perda de dados. Antes de migrar, recomendamos que você examine e mitigue quaisquer problemas de compatibilidade ou recursos sem suporte que possam bloquear a migração do servidor/banco de dados SQL. O relatório de avaliação e descoberta de migrações para Azure e a avaliação de banco de dados de migração do Azure podem ajudar com isso. 

### <a name="prioritize-workloads"></a>Priorizar cargas de trabalho

Depois de coletar informações sobre seu inventário, você pode identificar quais aplicativos e cargas de trabalho migrar primeiro. Desenvolva uma abordagem "aplicar e aprender" para migrar aplicativos de forma sistemática e controlável, de modo que você possa corrigir falhas antes de iniciar uma migração em escala total.

Para priorizar a ordem de migração, você pode usar fatores estratégicos como complexidade, tempo de migração, urgência do negócio, considerações de produção/não produção, conformidade, requisitos de segurança, conhecimento do aplicativo etc. 

Algumas recomendações:

- **Priorizar ganhos rápidos**: use os relatórios de avaliação para identificar as opções mais fáceis, incluindo servidores e bancos de dados que estão totalmente prontos e exigem esforço mínimo na migração para o Azure. A tabela resume algumas maneiras de fazer isso.

    **State** | **Ação**
    --- | ---
    **VMs prontas para o Azure** | Exporte o relatório de avaliação e filtre todos os servidores com o estado *pronto para o Azure*. Esse pode ser o primeiro grupo de servidores que você move e muda para o Azure, usando a ferramenta [migrações para Azure: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) .
    **Sistemas operacionais sem suporte** | Exporte o relatório de avaliação e filtre todos os servidores que executam o Windows Server 2008 R2/Windows Server 2008. Esses sistemas operacionais estão no final do suporte, e apenas o Azure fornece três anos gratuitos de atualizações de segurança quando você os migra para o Azure. Se você combinar o Benefício Híbrido do Azure e usar RIs, a economia poderá ser muito maior.
    **Migração do SQL Server** | Use as recomendações da avaliação de banco de dados para migrar bancos de dados que estão prontos para o Banco de Dados SQL do Azure, usando a ferramenta Migração de Banco de Dados das Migrações para Azure. Migre os bancos de dados prontos para a VM do SQL do Azure usando a ferramenta Migração do Servidor das Migrações para Azure.
    **Software sem suporte** | Exporte seu inventário de aplicativos e filtre qualquer software/extensão que possa estar chegando ao fim do suporte. Priorize esses aplicativos para migração.
    **Servidores provisionados** | Exportar o relatório de avaliação e filtrar servidores com baixa utilização da CPU (%) e utilização de memória (%).  Migre para uma VM do Azure de tamanho certo e economize em custos para recursos subutilizados.
    **Servidores superprovisionados** | Exportar o relatório de avaliação e filtrar para servidores com alta utilização da CPU (%) e utilização de memória (%).  Solucione restrições de capacidade, impeça a interrupção de servidores sobrerestritos e aumente o desempenho migrando esses servidores para o Azure. No Azure, use os recursos de dimensionamento automático para atender à demanda.<br/><br/> Analise relatórios de avaliação para investigar as restrições de armazenamento. Analise taxa de transferência e IOPS de disco e o tipo de disco recomendado.

- **Comece aos poucos e vá aumentando**: comece movendo aplicativos e cargas de trabalho que apresentam o mínimo de risco e complexidade, para criar confiança em sua estratégia de migração. Analise as recomendações de avaliação das Migrações para Azure junto com seu repositório CMDB para localizar e migrar cargas de trabalho de desenvolvimento/teste que podem ser candidatas a migrações piloto. Comentários e aprendizados de migrações piloto podem ser úteis à medida que você começa a migrar cargas de trabalho de produção.  
- **Fique em conformidade**: o Azure mantém o maior portfólio de conformidade do setor, em termos de amplitude e profundidade de ofertas. Use os requisitos de conformidade para priorizar as migrações, de modo que os aplicativos e as cargas de trabalho estejam em conformidade com padrões e leis nacionais, regionais e específicos do setor. Isso é importante sobretudo para organizações que lidam com processos comercialmente críticos, mantêm informações confidenciais ou estão em setores altamente regulamentados. Nesses tipos de organizações, os padrões e regulamentações são abundantes e podem mudar com frequência, sendo difícil acompanhá-los.  

## <a name="finalize-the-migration-plan"></a>Finalizar o plano de migração

Antes de finalizar seu plano de migração, considere e mitigue outros bloqueadores em potencial, da seguinte maneira: 

- **Requisitos de rede**: avalie a largura de banda da rede e as restrições de latência, que podem causar atrasos imprevistos e interrupções na velocidade de replicação da migração.
- **Testes/ajustes pós-migração**: permita um buffer de tempo para realizar testes de desempenho e aceitação do usuário em aplicativos migrados ou para configurar/ajustar aplicativos pós-migração, como atualização de cadeias de conexão de banco de dados, configuração de servidores Web, execução de transferência/limpeza etc.
- **Permissões**: examine as permissões recomendadas do Azure e as funções de acesso ao servidor/banco de dados e permissões necessárias para a migração.
- **Treinamento**: prepare sua organização para a transformação digital. Uma base sólida de treinamento é importante para uma mudança organizacional bem-sucedida. Confira o treinamento gratuito no [Microsoft Learn](/learn/azure/?ocid=CM_Discovery_Checklist_PDF), incluindo cursos sobre conceitos básicos do Azure, arquiteturas de solução e segurança. Incentive sua equipe a explorar as [certificações do Azure](https://www.microsoft.com/learning/certification-overview.aspx?ocid=CM_Discovery_Checklist_PDF).  
- **Suporte à implementação**: obtenha suporte para sua implementação, se necessário. Muitas organizações optam por receber ajuda externa como suporte à migração para a nuvem. Para mudar para o Azure com rapidez, confiança e assistência personalizada, considere recorrer a um  [Provedor de serviços gerenciados especialista em Azure](https://www.microsoft.com/solution-providers/search?cacheId=9c2fed4f-f9e2-42fb-8966-4c565f08f11e&ocid=CM_Discovery_Checklist_PDF) ou ao  [FastTrack for Azure](https://azure.microsoft.com/programs/azure-fasttrack/?ocid=CM_Discovery_Checklist_PDF).  


Crie um plano de migração para a nuvem eficaz que inclua informações detalhadas sobre os aplicativos que você deseja migrar, disponibilidade de aplicativo/banco de dados, restrições de tempo de inatividade e marcos de migração. O plano deve considerar quanto tempo a cópia de dados levará e incluir um buffer realista para testes pós-migração e atividades de transferência. 

Um plano de teste pós-migração deve incluir testes e casos de uso funcionais, de integração, segurança e desempenho, para garantir que os aplicativos migrados funcionem conforme o esperado e que todos os objetos de banco de dados e relacionamentos de dados sejam transferidos com sucesso para a nuvem.  

Crie um roteiro de migração e declare uma janela de manutenção para migrar seus aplicativos e bancos de dados com pouco ou nenhum tempo de inatividade e limite o possível impacto operacional e comercial durante a migração.  

## <a name="migrate"></a>Migrações

Recomendamos que você execute um teste de migração nas Migrações para Azure antes de iniciar uma migração em escala total. Um teste de migração ajuda você a estimar o tempo envolvido e ajustar o plano de migração. Ele oferece a oportunidade de descobrir possíveis problemas e corrigi-los antes da migração completa.

Quando você estiver pronto para a migração, use a ferramenta migrações para Azure: migração de servidor e o serviço de migração de dados do Azure (DMS), para uma experiência de migração direta e integrada, com acompanhamento de ponta a ponta.

- Com a ferramenta de migração de servidor, você pode migrar VMs e servidores locais ou VMs localizadas em outra nuvem privada ou pública (incluindo AWS, GCP) com tempo de inatividade zero.
- O Azure DMS fornece um serviço totalmente gerenciado que é projetado para permitir migrações diretas de várias fontes de banco de dados para plataformas de data do Azure, com tempo de inatividade mínimo.  

## <a name="next-steps"></a>Próximas etapas

- Investigue a [jornada de migração na nuvem](/azure/architecture/cloud-adoption/getting-started/migrate)   na estrutura de adoção de nuvem do Azure.
- Obtenha uma [visão geral rápida](migrate-services-overview.md) das migrações para Azure e assista a um [vídeo de introdução](https://youtu.be/wFfq3YPxYHE).
- Saiba mais sobre como avaliar as VMs para migração para [VMs do Azure](concepts-assessment-calculation.md).

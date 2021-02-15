---
title: Criar um plano de migração com as migrações para Azure
description: Fornece orientação sobre como criar um plano de migração com as migrações para Azure.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/11/2020
ms.openlocfilehash: ef916e0e8b32c96382a731d4a307e2b2a98ba1ea
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753851"
---
# <a name="build-migration-plan-with-azure-migrate"></a>Criar um plano de migração com as Migrações para Azure

Siga este artigo para criar seu plano de migração para o Azure com as [migrações para Azure](migrate-services-overview.md). 

## <a name="define-cloud-migration-goals"></a>Definir metas de migração de nuvem

Antes de começar, compreender e avaliar sua [motivação](/azure/cloud-adoption-framework/strategy/motivations) para mudar para a nuvem pode contribuir para um resultado comercial bem-sucedido. Conforme explicado na [estrutura de adoção de nuvem](/azure/cloud-adoption-framework), há vários gatilhos e resultados.   

**Evento de negócios** | **Resultado da migração**
--- | ---
Saída do datacenter | Custo 
Fusão, aquisição ou divestiture | Redução na complexidade do fornecedor/técnica
Redução nas despesas de capital | Otimização de operações internas
Fim do suporte para tecnologias de missão crítica | Aumento na agilidade dos negócios
Resposta às alterações de conformidade regulatória | Preparação para novos recursos técnicos
Novos requisitos de soberania de dados | Dimensionamento para atender às demandas de mercado
Redução de interrupções e melhorias de estabilidade de ti | Dimensionamento para atender às demandas geográficas

Identificar sua motivação ajuda você a fixar suas metas de migração estratégica. A próxima etapa é identificar e planejar um caminho de migração adequado para suas cargas de trabalho. A ferramenta [migrações para Azure: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) ajuda a avaliar cargas de trabalho locais e fornece orientações e ferramentas para ajudá-lo a migrar.

## <a name="understand-your-digital-estate"></a>Entenda seu espaço digital

Comece identificando sua infraestrutura, seus aplicativos e suas dependências locais. Isso ajuda a identificar cargas de trabalho para migração para o Azure e a reunir projeções de custo otimizado. A ferramenta de avaliação do servidor ajuda a identificar as cargas de trabalho que você tem em uso, as dependências entre as cargas de trabalho e a otimização da carga de trabalho.

### <a name="workloads-in-use"></a>Cargas de trabalho em uso

As migrações para Azure usam um dispositivo leve de migrações para Azure para executar a descoberta sem agente de VMs VMware locais, VMs do Hyper-V, outros computadores virtualizados e servidores físicos. A descoberta contínua coleta informações de configuração de computador e metadados de desempenho, bem como dados de aplicativos. Veja o que o dispositivo coleta de computadores locais: 

- Metadados de máquina, disco e NIC.

- Aplicativos, funções e recursos instalados.

- Dados de desempenho, incluindo utilização de CPU e memória, IOPS de disco e taxa de transferência.

Depois de coletar dados, você pode exportar a lista de inventário de aplicativos para localizar aplicativos e SQL Server instâncias em execução em seus computadores. Você pode usar a ferramenta migrações para Azure: avaliação de banco de dados para entender SQL Server prontidão.

 ![Inventário de aplicativos no portal](./media/concepts-migration-planning/application-inventory-portal.png)

 ![Exportação de inventário de aplicativos](./media/concepts-migration-planning/application-inventory-export.png)

Junto com os dados descobertos com a ferramenta de avaliação do servidor, você pode usar seus dados de CMDB (banco de dados de gerenciamento de configuração) para criar uma exibição do seu servidor e de seu espaço de trabalho e para entender como os servidores são distribuídos entre unidades de negócios, proprietários de aplicativos, geografias, etc. Isso ajuda a decidir quais cargas de trabalho priorizar para a migração. 

### <a name="dependencies-between-workloads"></a>Dependências entre cargas de trabalho

Após a descoberta do servidor, você pode [analisar dependências](concepts-dependency-visualization.md), Visualizar e identificar dependências entre servidores e estratégias de otimização para mover servidores interdependentes para o Azure. A visualização ajuda a entender se determinadas máquinas estão em uso ou se elas podem ser descomissionadas, em vez de serem migradas.  A análise de dependências ajuda a garantir que nada seja deixado para trás e para interrupções surpresa durante a migração. Com o inventário do aplicativo e a análise de dependência concluídas, você pode criar grupos de alta confiança de servidores e começar a avaliá-los.

 ![Mapeamento de dependências](./media/concepts-migration-planning/expand-client-group.png)

### <a name="optimization-and-sizing"></a>Otimização e dimensionamento

O Azure fornece flexibilidade para redimensionar sua capacidade de nuvem ao longo do tempo e a migração oferece uma oportunidade para otimizar os recursos de CPU e memória alocados para seus servidores. Criar uma avaliação nos servidores que você tem identidade ajuda a entender o histórico de desempenho da carga de trabalho. Isso é crucial para o dimensionamento correto de SKUs de VM do Azure e recomendações de disco no Azure.

## <a name="assess-migration-readiness"></a>Avaliar a preparação da migração


### <a name="readinesssuitability-analysis"></a>Preparação/análise de adequação

Você pode exportar o relatório de avaliação e filtrar essas categorias para entender a preparação do Azure:

- **Pronto para o Azure**: os computadores podem ser migrados no estado em que se encontram para o Azure, sem nenhuma alteração. 
- **Condicionalmente pronto para o Azure**: as máquinas podem ser migradas para o Azure, mas precisam de pequenas alterações, de acordo com as diretrizes de correção fornecidas na avaliação.
- **Não está pronto para o Azure**: os computadores não podem ser migrados para o Azure no estado em que se encontram. Os problemas devem ser corrigidos de acordo com as diretrizes de correção, antes da migração. 
- **Preparação desconhecida**: as migrações para Azure não podem determinar a preparação do computador, devido a metadados insuficientes.

Usando as avaliações de banco de dados, você pode avaliar a prontidão do seu SQL Server o estado de seu data para a migração para o Azure SQL Database ou instâncias gerenciadas do Azure SQL. A avaliação mostra a porcentagem de status de preparação de migração para cada uma de suas instâncias do SQL Server. Além disso, para cada instância, você pode ver o destino recomendado no Azure, os bloqueadores de migração potenciais, uma contagem de alterações significativas, prontidão para o banco de BD SQL do Azure ou a VM do SQL do Azure e um nível de compatibilidade. Você pode se aprofundar para entender o impacto dos bloqueadores de migração e as recomendações para corrigi-los.

 ![Avaliações de banco de dados](./media/concepts-migration-planning/database-assessment-portal.png)

### <a name="sizing-recommendations"></a>Recomendações de dimensionamento

Depois que um computador é marcado como pronto para o Azure, a avaliação do servidor faz recomendações de dimensionamento que identificam a SKU e o tipo de disco da VM do Azure para seus computadores. Você pode obter recomendações de dimensionamento com base no histórico de desempenho (para otimizar os recursos à medida que migra) ou com base nas configurações do computador local, sem o histórico de desempenho. Em uma avaliação de banco de dados, você pode ver recomendações para a SKU do banco de dados, tipo de preço e nível de computação.  

### <a name="get-compute-costs"></a>Obter custos de computação

A opção de dimensionamento baseado em desempenho nas avaliações de migrações para Azure ajuda você a usar as VMs de tamanho correto e deve ser usada como uma prática recomendada para otimizar as cargas de trabalho no Azure. Além do dimensionamento correto, há algumas outras opções para ajudar a economizar os custos do Azure: 

- **Instâncias reservadas**: com as [instâncias reservadas (ri)](https://azure.microsoft.com/pricing/reserved-vm-instances/), você pode reduzir significativamente os custos em comparação com os [preços pagos conforme o uso](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).
- **Benefício híbrido do Azure**: com [benefício híbrido do Azure](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/), você pode colocar licenças locais do Windows Server com assinaturas ativas do Software Assurance ou do Linux, no Azure e combinar com as opções de instâncias reservadas.
- **Enterprise Agreement**: o [ea (Azure Enterprise Agreements)](../cost-management-billing/manage/ea-portal-agreements.md) pode oferecer economias para assinaturas e serviços do Azure.
- **Ofertas**: há várias [ofertas do Azure](https://azure.microsoft.com/support/legal/offer-details/). Por exemplo, [desenvolvimento/teste pago conforme o uso](https://azure.microsoft.com/pricing/dev-test/), ou [desenvolvimento/teste Enterprise oferta](https://azure.microsoft.com/offers/ms-azr-0148p/), para fornecer taxas mais baixas para VMs de desenvolvimento/teste
- **Tempo de atividade da VM**: você pode examinar dias por mês e horas por dia em que as VMs do Azure são executadas. Desligar os computadores quando eles não estão em uso pode reduzir os custos (não aplicável ao RIs).
- **Região de destino**: você pode criar avaliações em regiões diferentes, para descobrir se a migração para uma região específica pode ser mais econômica. 

### <a name="visualize-data"></a>Visualizar dados

Você pode exibir relatórios de avaliação do servidor (com as informações de preparação do Azure e a distribuição de custo mensal) no Portal. Você também pode exportar a avaliação e enriquecer seu plano de migração com visualizações adicionais. Você pode criar várias avaliações, com diferentes combinações de propriedades e escolher o conjunto de propriedades que funcionam melhor para sua empresa.  

 ![Visão geral de avaliações](./media/concepts-migration-planning/assessment-summary.png)

### <a name="evaluate-gapsblockers"></a>Avaliar lacunas/bloqueadores

Ao descobrir os aplicativos e as cargas de trabalho que você deseja migrar, identifique as restrições de tempo de inatividade para eles e procure quaisquer dependências operacionais entre seus aplicativos e a infraestrutura subjacente. Essa análise ajuda você a planejar as migrações que atendem ao seu RTO (objetivo de tempo de recuperação) e garantir uma perda de dados mínima a zero. Antes de migrar, recomendamos que você revise e atenue quaisquer problemas de compatibilidade ou recursos sem suporte que possam bloquear a migração do banco de dados servidor/SQL. O relatório de avaliação de servidor migrações para Azure e a avaliação de banco de dados de migração do Azure podem ajudar com isso. 

### <a name="prioritize-workloads"></a>Priorizar cargas de trabalho

Depois de coletar informações sobre seu inventário, você pode identificar quais aplicativos e cargas de trabalho migrar primeiro. Desenvolva uma abordagem "aplicar e aprender" para migrar aplicativos de maneira sistemática e controlável, para que você possa passar por falhas antes de iniciar uma migração em escala completa.

Para priorizar a ordem de migração, você pode usar fatores estratégicos, como complexidade, tempo de migração, urgência de negócios, considerações de produção/não produção, conformidade, requisitos de segurança, conhecimento do aplicativo, etc. 

Algumas recomendações:

- **Priorizar WINS rápido**: Use os relatórios de avaliação para identificar frutas de baixa interrupções, incluindo servidores e bancos de dados que estão totalmente prontos e exigem esforço mínimo para migrar para o Azure. A tabela resume algumas maneiras de fazer isso.

    **State** | **Ação**
    --- | ---
    **VMs prontas do Azure** | Exporte o relatório de avaliação e filtre todos os computadores com o estado *pronto para o Azure*. Esse pode ser o primeiro grupo de computadores que você move e muda para o Azure, usando a ferramenta [migrações para Azure: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) .
    **Sistemas operacionais de fim do suporte** | Exporte o relatório de avaliação e filtre todos os computadores que executam o Windows Server 2008 R2/Windows Server 2008. Esses sistemas operacionais estão no final do suporte, e somente o Azure fornece uma disponibilidade gratuita de três anos de atualizações de segurança ao migrá-las para o Azure. Se você combinar Benefício Híbrido do Azure e usar o RIs, a economia poderá ser muito maior.
    **Migração de SQL Server** | Use as recomendações de avaliação de banco de dados para migrar bancos de dados que estão prontos para o Azure SQL Database usando a ferramenta migrações para Azure: migração de banco de dados. Migre os bancos de dados prontos para a VM do SQL do Azure usando a ferramenta migrações para Azure: Server Migration.
    **Software de fim do suporte** | Exporte seu inventário de aplicativos e filtre qualquer software/extensão que possa estar atingindo o fim do suporte. Priorize esses aplicativos para migração.
    **Computadores provisionados** | Exportar o relatório de avaliação e filtrar computadores com baixa utilização da CPU (%) e utilização de memória (%).  Migre para uma VM do Azure de tamanho certo e economize em custos para recursos subutilizados.
    **Computadores superprovisionados** | Exportar o relatório de avaliação e filtrar para computadores com alta utilização da CPU (%) e utilização de memória (%).  Solucione restrições de capacidade, impeça a interrupção de máquinas sobrerestritas e aumente o desempenho migrando esses computadores para o Azure. No Azure, use os recursos de dimensionamento automático para atender à demanda.<br/><br/> Analise relatórios de avaliação para investigar as restrições de armazenamento. Analise IOPS de disco e taxa de transferência e o tipo de disco recomendado.

- **Comece pequeno e, em seguida, vá muito**: comece movendo aplicativos e cargas de trabalho que apresentam o mínimo de risco e complexidade, para criar confiança em sua estratégia de migração. Analise as recomendações de avaliação de migração do Azure junto com seu repositório do CMDB para localizar e migrar cargas de trabalho de desenvolvimento/teste que podem ser candidatas a migrações piloto. Comentários e aprendizados de migrações piloto podem ser úteis à medida que você começa a migrar cargas de trabalho de produção.  
- Em **conformidade**: o Azure mantém o maior portfólio de conformidade do setor, em termos de amplitude e profundidade de ofertas. Use os requisitos de conformidade para priorizar as migrações, de modo que os aplicativos e as cargas de trabalho estejam em conformidade com os padrões e as leis nacionais, regionais e específicos do setor. Isso é especialmente verdadeiro para organizações que lidam com processos críticos de negócios, mantêm informações confidenciais ou estão em setores altamente regulamentados. Nesses tipos de organizações, padrões e regulamentos abound e podem mudar com frequência, sendo difícil de acompanhar.  

## <a name="finalize-the-migration-plan"></a>Finalizar o plano de migração

Antes de finalizar seu plano de migração, certifique-se de considerar e atenuar outros bloqueadores potenciais, da seguinte maneira: 

- **Requisitos de rede**: avalie a largura de banda da rede e as restrições de latência, o que pode causar atrasos imprevistos e interrupções na velocidade de replicação da migração.
- **Ajustes de teste/pós-migração**: permita um buffer de tempo para conduzir o desempenho e o teste de aceitação do usuário para aplicativos migrados ou para configurar/ajustar aplicativos após a migração, como atualizar cadeias de conexão de banco de dados, configurar servidores Web, executar recortes/limpeza, etc.
- **Permissões**: examine as permissões do Azure recomendadas e as funções de acesso do servidor/banco de dados e as permissões necessárias para a migração.
- **Treinamento**: Prepare sua organização para a transformação digital. Uma base sólida de treinamento é importante para uma mudança organizacional bem-sucedida. Confira treinamento gratuito em [Microsoft Learn](/learn/azure/?ocid=CM_Discovery_Checklist_PDF), incluindo cursos sobre conceitos básicos do Azure, arquiteturas de solução e segurança. Incentive sua equipe a explorar as [certificações do Azure](https://www.microsoft.com/learning/certification-overview.aspx?ocid=CM_Discovery_Checklist_PDF).  
- **Suporte à implementação**: Obtenha suporte para sua implementação, se necessário. Muitas organizações optam por uma ajuda externa para dar suporte à migração na nuvem. Para migrar para o Azure de forma rápida e confiável com assistência personalizada, considere um [provedor de serviços gerenciado por especialistas do Azure](https://www.microsoft.com/solution-providers/search?cacheId=9c2fed4f-f9e2-42fb-8966-4c565f08f11e&ocid=CM_Discovery_Checklist_PDF)ou [FastTrack for Azure](https://azure.microsoft.com/programs/azure-fasttrack/?ocid=CM_Discovery_Checklist_PDF).  


Crie um plano de migração de nuvem eficaz que inclua informações detalhadas sobre os aplicativos que você deseja migrar, disponibilidade de aplicativo/banco de dados, restrições de tempo de inatividade e etapas de migração. O plano considera quanto tempo a cópia de dados executará e incluirá um buffer realista para testes após a migração e atividades de recorte. 

Um plano de teste após a migração deve incluir casos de uso funcional, de integração, de segurança e de desempenho, para garantir que os aplicativos migrados funcionem conforme o esperado e que todos os objetos de banco de dados e as relações sejam transferidos com êxito para a nuvem.  

Crie um roteiro de migração e declare uma janela de manutenção para migrar seus aplicativos e bancos de dados com tempo de inatividade mínimo para zero e limitar o potencial operacional e o impacto nos negócios durante a migração.  

## <a name="migrate"></a>Migrar

Recomendamos que você execute uma migração de teste nas migrações para Azure antes de iniciar uma migração em escala completa. Uma migração de teste ajuda a estimar o tempo envolvido e ajustar seu plano de migração. Ele fornece uma oportunidade para descobrir possíveis problemas e corrigi-los antes da migração completa.

Quando você estiver pronto para a migração, use a ferramenta migrações para Azure: migração de servidor e o serviço de migração de dados do Azure (DMS), para uma experiência de migração direta e integrada, com acompanhamento de ponta a ponta.

- Com a ferramenta de migração de servidor, você pode migrar VMs e servidores locais ou VMs localizadas em outra nuvem privada ou pública (incluindo AWS, GCP) com tempo de inatividade zero.
- O Azure DMS fornece um serviço totalmente gerenciado que é projetado para permitir migrações diretas de várias fontes de banco de dados para plataformas de data do Azure, com tempo de inatividade mínimo.  

## <a name="next-steps"></a>Próximas etapas

- Investigue a [jornada de migração na nuvem](/azure/architecture/cloud-adoption/getting-started/migrate)   na estrutura de adoção de nuvem do Azure.
- Obtenha uma [visão geral rápida](migrate-services-overview.md) das migrações para Azure e assista a um [vídeo de introdução](https://youtu.be/wFfq3YPxYHE).
- Saiba mais sobre como avaliar as VMs para migração para [VMs do Azure](concepts-assessment-calculation.md).

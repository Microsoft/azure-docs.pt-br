---
title: Notas sobre a versão da Central de Segurança do Azure
description: Uma descrição do que há de novo e do que mudou na Central de Segurança do Azure
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: fe031fa6de86b8059ba175fc4e1df6385ca7e796
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99551018"
---
# <a name="whats-new-in-azure-security-center"></a>Novidades na Central de Segurança do Azure

A Central de Segurança está em desenvolvimento ativo e recebe aprimoramentos continuamente. Para se manter atualizado com os desenvolvimentos mais recentes, esta página fornece informações sobre novos recursos, correções de bug e funcionalidades preteridas.

Esta página é atualizada com frequência, então acesse-a regularmente. 

Para saber mais sobre as alterações *planejadas* chegando em breve à Central de Segurança, consulte [Alterações importantes na Central de Segurança do Azure](upcoming-changes.md). 

> [!TIP]
> Se você estiver procurando itens que têm mais de seis meses, poderá encontrá-los nos [Arquivos de O que há de novo na Central de Segurança do Azure](release-notes-archive.md).


## <a name="february-2021"></a>Fevereiro de 2021

As atualizações de fevereiro incluem:

- [Lançamento das recomendações de proteção de cargas de trabalho do Kubernetes em GA (disponibilidade geral)](#kubernetes-workload-protection-recommendations-released-for-general-availability-ga)
- [Link direto para a política na página de detalhes da recomendação](#direct-link-to-policy-from-recommendation-details-page)
- [A recomendação de classificação de dados SQL não afeta mais a sua classificação de segurança](#sql-data-classification-recommendation-no-longer-affects-your-secure-score)
- [As automações de fluxo de trabalho podem ser disparadas por alterações nas avaliações de conformidade regulatória (versão prévia)](#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-preview)

### <a name="kubernetes-workload-protection-recommendations-released-for-general-availability-ga"></a>Lançamento das recomendações de proteção de cargas de trabalho do Kubernetes em GA (disponibilidade geral)

Estamos felizes em anunciar a GA (disponibilidade geral) do conjunto de recomendações para proteções de cargas de trabalho do Kubernetes.

Para garantir que as cargas de trabalho do Kubernetes sejam seguras por padrão, a Central de Segurança adicionou recomendações de proteção no nível do Kubernetes, incluindo opções de imposição com o controle de admissão do Kubernetes.

Quando o complemento do Azure Policy para Kubernetes for instalado no seu cluster do AKS (Serviço de Kubernetes do Azure), todas as solicitações para o servidor de API do Kubernetes serão monitoradas em relação ao conjunto predefinido de melhores práticas, exibidas como 13 recomendações de segurança, antes de serem persistidas no cluster. Em seguida, você pode configurá-lo para impor as melhores práticas e exigir o uso dele em cargas de trabalho futuras.

Por exemplo, você poderá proibir a criação de contêineres privilegiados, e todas as futuras solicitações para fazer isso serão bloqueadas.

Saiba mais em [Melhores práticas de proteção de cargas de trabalho usando o controle de admissão do Kubernetes](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control).

> [!NOTE]
> Enquanto as recomendações estavam em versão prévia, elas não renderizavam um recurso de cluster do AKS não íntegro e não eram incluídas nos cálculos da sua classificação de segurança. Com este comunicado de GA, elas serão incluídas no cálculo da classificação. Se você ainda não corrigiu, isso pode resultar em um pequeno impacto em sua classificação de segurança. Corrija-as sempre que possível, conforme descrito em [Corrigir recomendações na Central de Segurança do Azure](security-center-remediate-recommendations.md).


### <a name="direct-link-to-policy-from-recommendation-details-page"></a>Link direto para a política na página de detalhes da recomendação

Quando você estiver examinando os detalhes de uma recomendação, muitas vezes, será útil poder ver a política subjacente. Para cada recomendação com suporte de uma política, há um novo link na página de detalhes da recomendação:

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Link para a página do Azure Policy de uma política específica que dá suporte a uma recomendação":::

Use esse link para ver a definição de política e examinar a lógica de avaliação. 

Se você estiver examinando a lista de recomendações no nosso [Guia de referência de recomendações de segurança](recommendations-reference.md), também verá links para as páginas de definição da política:

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Como acessar a página do Azure Policy de uma política específica diretamente na página de referência de recomendações da Central de Segurança do Azure" lightbox="media/release-notes/view-policy-definition-from-documentation.png":::


### <a name="sql-data-classification-recommendation-no-longer-affects-your-secure-score"></a>A recomendação de classificação de dados SQL não afeta mais a sua classificação de segurança

A recomendação **Os dados confidenciais nos seus bancos de dados SQL devem ser classificados** não afetará mais sua classificação de segurança. Essa é a única recomendação no controle de segurança **Aplicar classificação de dados**, para que o controle agora tenha um valor de classificação de segurança igual a 0.


### <a name="workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-preview"></a>As automações de fluxo de trabalho podem ser disparadas por alterações nas avaliações de conformidade regulatória (versão prévia)

Adicionamos um terceiro tipo de dados às opções de gatilho para suas automações de fluxo de trabalho: alterações nas avaliações de conformidade regulatória.

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Como usar as avaliações de conformidade regulatória para disparar uma automação de fluxo de trabalho" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::


## <a name="january-2021"></a>Janeiro de 2021

As atualizações em janeiro incluem:

- [O Azure Security Benchmark agora é a iniciativa de política padrão da Central de Segurança do Azure](#azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center)
- [Avaliação de vulnerabilidades para computadores locais e multinuvem liberada para GA (Disponibilidade Geral)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga)
- [A classificação de segurança para grupos de gerenciamento agora está disponível na versão prévia](#secure-score-for-management-groups-is-now-available-in-preview)
- [API de classificação de segurança liberada para GA (Disponibilidade Geral)](#secure-score-api-is-released-for-general-availability-ga)
- [Proteções de DNS pendentes e adicionadas ao Azure Defender para o Serviço de Aplicativo](#dangling-dns-protections-added-to-azure-defender-for-app-service)
- [Conectores de várias nuvens são liberados para GA (Disponibilidade Geral)](#multi-cloud-connectors-are-released-for-general-availability-ga)
- [Isente recomendações inteiras da sua classificação de segurança para assinaturas e grupos de gerenciamento](#exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups)
- [Os usuários agora podem solicitar visibilidade em todo o locatário no administrador global](#users-can-now-request-tenant-wide-visibility-from-their-global-administrator)
- [35 recomendações de versão prévia adicionadas para aumentar a cobertura do Azure Security Benchmark](#35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [Exportação de CSV da lista de recomendações filtrada](#csv-export-of-filtered-list-of-recommendations)
- [Recursos "não aplicáveis" que agora são relatados como "em conformidade" nas avaliações do Azure Policy](#not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments)
- [Exportar instantâneos semanais da classificação de segurança e dos dados de conformidade regulatória com exportação contínua (versão prévia)](#export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview)


### <a name="azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center"></a>O Azure Security Benchmark agora é a iniciativa de política padrão da Central de Segurança do Azure

O Azure Security Benchmark é um conjunto específico de diretrizes específicas do Azure criadas pela Microsoft com as melhores práticas de segurança e conformidade baseadas em estruturas de conformidade comuns. Esse parâmetro de comparação amplamente respeitado se baseia nos controles do [CIS (Center for Internet Security)](https://www.cisecurity.org/benchmark/azure/) e do [NIST (National Institute of Standards and Technology)](https://www.nist.gov/) com foco na segurança centrada na nuvem.

Nos últimos meses, a lista da Central de Segurança de recomendações de segurança internas cresceu significativamente expandindo nossa cobertura desse parâmetro de comparação.

A partir desta versão, o parâmetro de comparação é a base para as recomendações da Central de Segurança e é totalmente integrado como a iniciativa de política padrão. 

Todos os serviços do Azure têm uma página de linha de base de segurança na documentação. Por exemplo, [esta é a linha de base da Central de Segurança](security-baseline.md). Essas linhas de base são criadas no Azure Security Benchmark.

Se você estiver usando o painel de conformidade regulatória da Central de Segurança, verá duas instâncias do parâmetro de comparação durante um período de transição:

:::image type="content" source="media/release-notes/regulatory-compliance-with-azure-security-benchmark.png" alt-text="O painel de conformidade regulatória da Central de Segurança do Azure que mostra o Azure Security Benchmark":::

As recomendações existentes não são afetadas e, conforme o parâmetro de comparação cresce, as alterações serão refletidas automaticamente na Central de Segurança. 

Para saber mais, confira as seguintes páginas:

- [Saiba mais sobre o Azure Security Benchmark](../security/benchmarks/introduction.md)
- [Personalizar o conjunto de padrões no seu painel de conformidade regulatória](update-regulatory-compliance-packages.md)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga"></a>Avaliação de vulnerabilidades para computadores locais e multinuvem liberada para GA (Disponibilidade Geral)

Em outubro, anunciamos uma versão prévia para a verificação de servidores habilitados para Azure Arc com um verificador de avaliação de vulnerabilidade integrado ao [Azure Defender para servidores](defender-for-servers-introduction.md)(da plataforma Qualys).

Ela já está liberada para GA (Disponibilidade Geral).

Quando você tiver habilitado o Azure Arc em seus computadores que não são do Azure, a Central de Segurança oferecerá a implantação do verificador de vulnerabilidades integrado neles, manualmente e em escala.

Com essa atualização, você pode tirar proveito da eficiência do **Azure Defender para servidores** para consolidar seu programa de gerenciamento de vulnerabilidades em todos os seus ativos que são e que não são do Azure.

Principais recursos:

- Monitoramento do estado de provisionamento do verificador de VA (avaliação de vulnerabilidade) em computadores do Azure Arc
- Provisionamento do agente de VA integrado para computadores Windows e Linux do Azure Arc desprotegidos (manualmente e em escala)
- Recebimento e análise das vulnerabilidades detectadas por agentes implantados (manualmente e em escala)
- Experiência unificada para VMs do Azure e computadores do Azure Arc

[Saiba mais sobre como implantar o verificador de vulnerabilidades integrado em computadores híbridos](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines).

[Saiba mais sobre os servidores habilitados para Azure Arc](../azure-arc/servers/index.yml).


### <a name="secure-score-for-management-groups-is-now-available-in-preview"></a>A classificação de segurança para grupos de gerenciamento agora está disponível na versão prévia

A página de classificação de segurança agora mostra as classificações de segurança agregadas dos seus grupos de gerenciamento, além do nível de assinatura. Agora, você pode ver a lista de grupos de gerenciamento na sua organização e a pontuação de cada grupo de gerenciamento.

:::image type="content" source="media/secure-score-security-controls/secure-score-management-groups.png" alt-text="Como exibir as classificações de segurança de seus grupos de gerenciamento.":::

Saiba mais sobre os [controles de segurança e classificação de segurança na Central de Segurança do Azure](secure-score-security-controls.md).

### <a name="secure-score-api-is-released-for-general-availability-ga"></a>API de classificação de segurança liberada para GA (Disponibilidade Geral)

Agora é possível acessar sua classificação por meio de uma [API de classificação de segurança](/rest/api/securitycenter/securescores/). Os métodos de API oferecem a flexibilidade para consultar os dados e criar seu mecanismo de relatório das suas classificações de segurança ao longo do tempo. Por exemplo:

- use a API **Classificações de Segurança** para obter a classificação de uma assinatura específica
- use a API **Controles de Classificação de Segurança** para listar os controles de segurança e a classificação atual das suas assinaturas

Saiba mais sobre as ferramentas externas que são possíveis com a API de classificação de segurança [na área de classificação de segurança da nossa comunidade do GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score).

Saiba mais sobre os [controles de segurança e classificação de segurança na Central de Segurança do Azure](secure-score-security-controls.md).


### <a name="dangling-dns-protections-added-to-azure-defender-for-app-service"></a>Proteções de DNS pendentes e adicionadas ao Azure Defender para o Serviço de Aplicativo

As invasões de subdomínios são uma ameaça comum de gravidade alta para as organizações. Uma tomada de controle de subdomínio poderá ocorrer quando um registro DNS indicar um site desprovisionado. Esses registros DNS também são conhecidos como entradas "DNS pendentes". Os registros CNAME são particularmente vulneráveis a essa ameaça. 

As tomadas de controle de subdomínios permitem que agentes de ameaça redirecionem o tráfego destinado ao domínio de uma organização para um site que executa atividades mal-intencionadas.

O Azure Defender para o Serviço de Aplicativo agora detecta entradas DNS pendentes quando um site do Serviço de Aplicativo é encerrado. Nesse momento, a entrada DNS indicará um recurso inexistente, e seu site estará vulnerável a uma tomada de controle de subdomínio. Essas proteções estarão disponíveis caso seus domínios sejam gerenciados usando o DNS do Azure ou um registrador de domínios externo. Além disso, elas se aplicam ao Serviço de Aplicativo no Windows e ao Serviço de Aplicativo no Linux.

Saiba mais:

- [Tabela de referência de alertas do Serviço de Aplicativo](alerts-reference.md#alerts-azureappserv) – Inclui dois novos alertas do Azure Defender que disparam quando uma entrada DNS pendente é detectada
- [Impedir a ocorrência de entradas DNS pendentes e evitar a tomada de controle de subdomínio](../security/fundamentals/subdomain-takeover.md) – Saiba mais sobre aspectos da ameaça de tomada de controle de subdomínio e de um DNS pendente
- [Introdução ao Azure Defender para Serviço de Aplicativo](defender-for-app-service-introduction.md)


### <a name="multi-cloud-connectors-are-released-for-general-availability-ga"></a>Conectores de várias nuvens são liberados para GA (Disponibilidade Geral)

Com as cargas de trabalho de nuvem normalmente abrangendo plataformas de várias nuvens, os serviços de segurança de nuvem precisam fazer o mesmo.

A Central de Segurança do Azure protege as cargas de trabalho no Azure, na AWS (Amazon Web Services) e no GCP (Google Cloud Platform).

Conectar suas contas da AWS ou da GCP integrará ferramentas de segurança nativas, como o AWS Security Hub e o Centro de Comando de Segurança da GCP, à Central de Segurança do Azure.

Essa funcionalidade significa que a Central de Segurança fornece visibilidade e proteção em todos os principais ambientes de nuvem. Alguns dos benefícios dessa integração:

- Provisionamento automático de agente – A Central de Segurança usa o Azure Arc para implantar o agente do Log Analytics nas instâncias da AWS
- Gerenciamento de política
- Gerenciamento de vulnerabilidades
- Detecção e Resposta de Ponto de Extremidade Inserido (EDR)
- Detecção de configurações incorretas de segurança
- Uma exibição que mostra as recomendações de segurança de todos os provedores de nuvem
- Incorporar todos os recursos aos cálculos de classificação de segurança da Central de Segurança
- Avaliações de conformidade regulatória dos recursos da AWS e da GCP

No menu da Central de Segurança, selecione **Conectores de várias nuvens** e você verá as opções para criar conectores:

:::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Botão Adicionar conta da AWS na página de vários conectores de nuvem da Central de Segurança":::

Saiba mais em:
- [Conectar as contas da AWS à Central de Segurança do Azure](quickstart-onboard-aws.md)
- [Conectar as contas do GCP à Central de Segurança do Azure](quickstart-onboard-gcp.md)


### <a name="exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups"></a>Isente recomendações inteiras da sua classificação de segurança para assinaturas e grupos de gerenciamento

Estamos expandindo a funcionalidade de isenção para incluir recomendações inteiras. Fornecendo mais opções para ajustar as recomendações de segurança que a Central de Segurança faz para as suas assinaturas, grupo de gerenciamento ou recursos.

Ocasionalmente, um recurso será listado como não íntegro quando você souber que o problema foi resolvido por uma ferramenta de terceiros que não foi detectada pela Central de Segurança. Ou uma recomendação será mostrada em um escopo no qual você acha que ela não pertence. A recomendação pode ser inadequada para uma assinatura específica. Ou talvez a sua organização tenha decidido aceitar os riscos relacionados à recomendação ou ao recurso específico.

Com essa versão prévia do recurso, agora você pode criar uma isenção de uma recomendação para:

- **Isentar um recurso** para garantir que ele não esteja listado com os recursos não íntegros no futuro e não afete a sua classificação de segurança. O recurso será listado como não aplicável e o motivo será mostrado como "isento" com a justificativa específica que você selecionar.

- **Isente uma assinatura ou grupo de gerenciamento** para garantir que a recomendação não afete a sua classificação de segurança e não seja mostrada para a assinatura ou grupo de gerenciamento no futuro. Isso está relacionado aos recursos existentes e a qualquer um que você criar no futuro. A recomendação será marcada com a justificativa específica selecionada para o escopo que você selecionou.

Saiba mais em [Isentando recursos e recomendações da sua classificação de segurança](exempt-resource.md).



### <a name="users-can-now-request-tenant-wide-visibility-from-their-global-administrator"></a>Os usuários agora podem solicitar visibilidade em todo o locatário no administrador global

Caso não tenham permissões para conferir os dados da Central de Segurança, os usuários agora podem acessar um link para solicitar permissões ao administrador global da organização. A solicitação inclui a função que ele deseja e a justificativa do por que ela é necessária.

:::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="Faixa informando a um usuário que ele pode solicitar permissões em todo o locatário.":::

Saiba mais em [Solicitar permissões em todo o locatário quando as suas forem insuficientes](security-center-management-groups.md#request-tenant-wide-permissions-when-yours-are-insufficient).


### <a name="35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>35 recomendações de versão prévia adicionadas para aumentar a cobertura do Azure Security Benchmark

O Azure Security Benchmark é a iniciativa de política padrão na Central de Segurança do Azure. 

Para aumentar a cobertura desse parâmetro de comparação, as 35 recomendações de versão prévia a seguir foram adicionadas à Central de Segurança.

> [!TIP]
> As recomendações de versão prévia não processam um recurso não íntegro e não são incluídas nos cálculos de sua classificação de segurança. Corrija-as sempre que possível, para que, quando o período da versão prévia terminar, elas contribuam para sua classificação. Saiba mais sobre como responder a essas recomendações em [Recomendações de correção na Central de Segurança do Azure](security-center-remediate-recommendations.md).

| Controle de segurança                     | Novas recomendações                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Habilitar a criptografia em repouso            | – As contas do Azure Cosmos DB devem usar chaves gerenciadas pelo cliente para criptografar os dados inativos<br>– Os workspaces do Azure Machine Learning devem ser criptografados com uma CMK (chave gerenciada pelo cliente)<br>– A proteção de dados do Bring Your Own Key será habilitada para servidores MySQL<br>– A proteção de dados do Bring Your Own Key será habilitada para servidores PostgreSQL<br>– As contas dos Serviços Cognitivos devem habilitar a criptografia de dados com uma CMK (chave gerenciada pelo cliente)<br>– Os registros de contêiner devem ser criptografados com uma CMK (chave gerenciada pelo cliente)<br>– As instâncias gerenciadas de SQL devem usar chaves gerenciadas pelo cliente para criptografar dados inativos<br>– Os SQL Servers devem usar chaves gerenciadas pelo cliente para criptografar dados inativos<br>– As contas de armazenamento devem usar uma CMK (chave gerenciada pelo cliente) para criptografia                                                                                                                                                              |
| Implementar melhores práticas de segurança    | – As assinaturas devem ter um endereço de email de contato para problemas de segurança<br> – O provisionamento automático do agente do Log Analytics será habilitado na sua assinatura<br> – A notificação por email para alertas de alta severidade deve ser habilitada<br> – A notificação por email para o proprietário da assinatura para alertas de alta severidade deve ser habilitada<br> – Os cofres de chaves devem ter a proteção contra limpeza habilitada<br> – Os cofres de chaves devem ter a exclusão temporária habilitada |
| Gerenciar acesso e permissões        | – Os aplicativos de funções devem ter a opção 'Certificados do Cliente (Certificados do cliente de entrada)' habilitada |
| Proteger os aplicativos contra DDoS | – O WAF (Firewall de Aplicativo Web) deve ser habilitado para o Gateway de Aplicativo<br> – O WAF (Firewall de Aplicativo Web) deve ser habilitado para o Azure Front Door Service |
| Restringir acesso não autorizado à rede | – O firewall deve ser habilitado no Key Vault<br> – O ponto de extremidade privado deve ser configurado para o Key Vault<br> – A Configuração de Aplicativos deve usar um link privado<br> – O Cache do Azure para Redis deve residir em uma rede virtual<br> – Os domínios da Grade de Eventos do Azure devem usar um link privado<br> – Os tópicos da Grade de Eventos do Azure devem usar um link privado<br> – Os workspaces do Azure Machine Learning devem usar um link privado<br> – O Serviço do Azure SignalR deve usar um link privado<br> – O Azure Spring Cloud deve usar uma injeção de rede<br> – Os registros de contêiner não devem permitir acesso irrestrito à rede<br> – Os registros de contêiner devem usar um link privado<br> – O acesso à rede pública deve ser desabilitado para servidores MariaDB<br> – O acesso à rede pública deve ser desabilitado para servidores MySQL<br> – O acesso à rede pública deve ser desabilitado para servidores PostgreSQL<br> – A conta de armazenamento deve usar uma conexão de link privado<br> – As contas de armazenamento devem restringir o acesso à rede usando regras de rede virtual<br> – Os modelos do Construtor de Imagens de VM devem usar um link privado|
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Links relacionados:

- [Saiba mais sobre o Azure Security Benchmark](../security/benchmarks/introduction.md)
- [Saiba mais sobre o Banco de Dados do Azure para MariaDB](../mariadb/overview.md)
- [Saiba mais sobre o Banco de Dados do Azure para MySQL](../mysql/overview.md)
- [Saiba mais sobre o Banco de Dados do Azure para PostgreSQL](../postgresql/overview.md)




### <a name="csv-export-of-filtered-list-of-recommendations"></a>Exportação de CSV da lista de recomendações filtrada 

Em novembro de 2020, adicionamos filtros à página de recomendações ([a lista de recomendações agora inclui filtros](#recommendations-list-now-includes-filters)). Em dezembro, expandimos esses filtros ([a página Recomendações tem novos filtros para o ambiente, a severidade e as respostas disponíveis](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)). 

Com este comunicado, estamos alterando o comportamento do botão **Baixar para CSV** para que a exportação de CSV inclua apenas as recomendações atualmente exibidas na lista filtrada. 

Por exemplo, na imagem abaixo, você pode ver que a lista foi filtrada para duas recomendações. O arquivo CSV gerado inclui os detalhes de status de cada recurso afetado por essas duas recomendações.   

:::image type="content" source="media/security-center-managing-and-responding-alerts/export-to-csv-with-filters.png" alt-text="Como exportar recomendações filtradas para um arquivo CSV":::

Saiba mais em [Recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md).


### <a name="not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments"></a>Recursos "não aplicáveis" que agora são relatados como "em conformidade" nas avaliações do Azure Policy

Anteriormente, os recursos avaliados com o objetivo de obter uma recomendação e considerados **não aplicáveis** eram mostrados como "não compatíveis" no Azure Policy. Nenhuma ação do usuário poderia alterar o estado deles para "em conformidade". Com essa alteração, os recursos agora são relatados como "em conformidade" para maior clareza.

O único impacto será visto no Azure Policy, em que o número de recursos em conformidade aumentará. Não haverá nenhum impacto na sua classificação de segurança na Central de Segurança do Azure.


### <a name="export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview"></a>Exportar instantâneos semanais da classificação de segurança e dos dados de conformidade regulatória com exportação contínua (versão prévia)

Adicionamos uma versão prévia do recurso às ferramentas de [exportação contínua](continuous-export.md) para exportar instantâneos semanais de classificação de segurança e dados de conformidade regulatória.

Ao definir uma exportação contínua, defina a frequência de exportação:

:::image type="content" source="media/release-notes/export-frequency.png" alt-text="Como escolher a frequência da exportação contínua":::

- **Streaming**: as avaliações serão enviadas em tempo real quando o estado de integridade de um recurso for atualizado (se nenhuma atualização ocorrer, nenhum dado será enviado).
- **Instantâneos** – um instantâneo do estado atual de todas as avaliações de conformidade regulatória será enviado a cada semana (essa é uma versão prévia do recurso para instantâneos semanais de classificações de segurança e dados de conformidade regulatória).

Saiba mais sobre as funcionalidades completas desse recurso em [Exportar continuamente os dados da Central de Segurança](continuous-export.md)

## <a name="december-2020"></a>Dezembro de 2020

As atualizações de dezembro incluem:

- [O Azure Defender para SQL Servers em computadores está em disponibilidade geral](#azure-defender-for-sql-servers-on-machines-is-generally-available)
- [O suporte do Azure Defender para SQL para o pool de SQL dedicado do Azure Synapse Analytics está em disponibilidade geral](#azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available)
- [Os administradores globais já podem conceder a si mesmos permissões no nível dos locatários](#global-administrators-can-now-grant-themselves-tenant-level-permissions)
- [Dois novos planos do Azure Defender: Azure Defender para DNS e Azure Defender para Resource Manager (em versão prévia)](#two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview)
- [Nova página de alertas de segurança no portal do Azure (versão prévia)](#new-security-alerts-page-in-the-azure-portal-preview)
- [Experiência revitalizada da Central de Segurança no Banco de Dados SQL do Azure e na Instância Gerenciada de SQL](#revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance)
- [Ferramentas e filtros de inventário de ativos atualizados](#asset-inventory-tools-and-filters-updated)
- [Recomendação sobre aplicativos Web solicitando certificados SSL que não fazem mais parte da classificação de segurança](#recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score)
- [A página de recomendações tem novos filtros para o ambiente, a severidade e as respostas disponíveis](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)
- [A exportação contínua obtém novos tipos de dados e políticas de deployifnotexist aprimoradas](#continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies)


### <a name="azure-defender-for-sql-servers-on-machines-is-generally-available"></a>O Azure Defender para SQL Servers em computadores está em disponibilidade geral

A Central de Segurança do Azure oferece dois planos do Azure Defender para SQL Servers:

- **Azure Defender para servidores do Banco de Dados SQL do Azure** – Defende os seus SQL Servers nativos do Azure 
- **Azure Defender para SQL Servers em computadores** – Estende as mesmas proteções para os seus SQL Servers em ambientes híbridos, multinuvem e locais

Com esse comunicado, **o Azure Defender para SQL** agora protege os seus bancos de dados e os dados neles, independentemente de onde eles estiverem localizados.

O Azure Defender para SQL inclui funcionalidades de avaliação de vulnerabilidade. A ferramenta de avaliação de vulnerabilidade inclui os seguintes recursos avançados:

- A **Configuração de linha de base** (novo!) refina de modo inteligente os resultados das verificações de vulnerabilidade, sinalizando aqueles que podem representar problemas de segurança reais. Depois de estabelecer o estado de segurança de linha de base, a ferramenta de avaliação de vulnerabilidade só relata desvios desse estado de linha de base. Os resultados que correspondem à linha de base são considerados ao passar nas verificações posteriores. Isso permite que você e os analistas concentrem a atenção onde importa.
- As **Informações detalhadas de benchmark** ajudam você a *entender* as descobertas e por que elas se relacionam com os seus recursos.
- Os **Scripts de correção** ajudam você a reduzir os riscos identificados.

Saiba mais sobre o [Azure Defender para SQL](defender-for-sql-introduction.md).


### <a name="azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available"></a>O suporte do Azure Defender para SQL para o pool de SQL dedicado do Azure Synapse Analytics está em disponibilidade geral

O Azure Synapse Analytics (antigo SQL DW) é um serviço de análise que combina o data warehouse corporativo e a análise de Big Data. Os pools de SQL dedicados são os recursos de data warehouse corporativos do Azure Synapse. Saiba mais em [O que é o Azure Synapse Analytics (antigo SQL DW)?](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md).

O Azure Defender para SQL protege os seus pools de SQL dedicados com:

- **Proteção avançada contra ameaças** para detectar ameaças e ataques 
- **Funcionalidades de avaliação de vulnerabilidade** para identificar e corrigir configurações incorretas de segurança

O suporte do Azure Defender para SQL para os pools de SQL do Azure Synapse Analytics é adicionado automaticamente ao pacote de bancos de dados SQL do Azure na Central de Segurança do Azure. Uma nova guia "Azure Defender para SQL" será encontrada na sua página do workspace do Azure Synapse no portal do Azure.

Saiba mais sobre o [Azure Defender para SQL](defender-for-sql-introduction.md).


### <a name="global-administrators-can-now-grant-themselves-tenant-level-permissions"></a>Os administradores globais já podem conceder a si mesmos permissões no nível dos locatários

Um usuário com a função **Administrador global** no Azure Active Directory pode ter responsabilidades em todo o locatário, mas não ter as permissões do Azure para ver essas informações de toda a organização na Central de Segurança do Azure. 

Para atribuir a si mesmo permissões no nível dos locatários, siga as instruções descritas em [Conceder a si mesmo permissões em todo o locatário](security-center-management-groups.md#grant-tenant-wide-permissions-to-yourself).


### <a name="two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview"></a>Dois novos planos do Azure Defender: Azure Defender para DNS e Azure Defender para Resource Manager (em versão prévia)

Adicionamos duas novas funcionalidades de proteção contra ameaças com abrangência nativa de nuvem para seu ambiente do Azure.

Essas novas proteções aprimoram muito sua resiliência contra ataques de atores de ameaças e aumentam significativamente o número de recursos do Azure protegidos pelo Azure Defender.

- **Azure Defender para Resource Manager**: monitora automaticamente todas as operações de gerenciamento de recursos executadas na sua organização. Para obter mais informações, consulte:
    - [Introdução ao Azure Defender para Resource Manager](defender-for-resource-manager-introduction.md)
    - [Responder aos alertas do Azure Defender para Resource Manager](defender-for-resource-manager-usage.md)
    - [Lista de alertas fornecidos pelo Azure Defender para Resource Manager](alerts-reference.md#alerts-resourcemanager)

- **Azure Defender para DNS**: monitora continuamente todas as consultas DNS dos seus recursos do Azure. Para obter mais informações, consulte:
    - [Introdução ao Azure Defender para DNS](defender-for-dns-introduction.md)
    - [Responder aos alertas do Azure Defender para DNS](defender-for-dns-usage.md)
    - [Lista de alertas fornecidos pelo Azure Defender para DNS](alerts-reference.md#alerts-dns)


### <a name="new-security-alerts-page-in-the-azure-portal-preview"></a>Nova página de alertas de segurança no portal do Azure (versão prévia)

A página de alertas de segurança da Central de Segurança do Azure foi reprojetada para oferecer:

- **Experiência de triagem aprimorada para alertas** –ajudando a reduzir os alertas exaustivos e concentrar-se nas ameaças mais relevantes com mais facilidade, a lista inclui filtros personalizáveis e opções de agrupamento
- **Mais informações na lista de alertas** – como táticas MITRE ATT&ACK
- **Botão para criar alertas de exemplo** – para avaliar os recursos do Azure Defender e testar sua configuração de alertas (para integração do SIEM, notificações por email e automação de fluxo de trabalho), você pode criar alertas de exemplo em todos os planos do Azure Defender
- **Alinhamento com a experiência de incidentes do Azure Sentinel** – para clientes que usam os dois produtos, a troca entre eles agora é uma experiência mais simples, além de ser fácil aprender um do outro
- **Melhores desempenho** para listas grandes de alertas
- **Navegação por teclado** na lista de alertas
- **Alertas do Azure Resource Graph** – você pode consultar alertas no Azure Resource Graph, a API semelhante ao Kusto para todos os seus recursos. Isso também será útil se você estiver criando os próprios painéis de alertas. [Saiba mais sobre Azure Resource Graph](../governance/resource-graph/index.yml).

Para acessar a nova experiência, use o link "Experimente agora" da faixa na parte superior da página de alertas de segurança.

:::image type="content" source="media/security-center-managing-and-responding-alerts/preview-alerts-experience-banner.png" alt-text="Faixa com o link para a nova experiência de versão prévia de alertas":::

Para criar alertas de exemplo na nova experiência de alertas, consulte [Gerar alertas de exemplo do Azure Defender](security-center-alert-validation.md#generate-sample-azure-defender-alerts).


### <a name="revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance"></a>Experiência revitalizada da Central de Segurança no Banco de Dados SQL do Azure e na Instância Gerenciada de SQL 

A experiência da Central de Segurança no SQL fornece acesso aos seguintes recursos da Central de Segurança e do Azure Defender para SQL:

- **Recomendações de segurança** – A Central de Segurança analisa periodicamente o estado de segurança de todos os recursos do Azure conectados para identificar possíveis configurações incorretas na segurança. Em seguida, ela fornece recomendações sobre como corrigir essas vulnerabilidades e aprimorar a postura de segurança das organizações.
- **Alertas de segurança** – Um serviço de detecção que monitora continuamente as atividades do SQL do Azure em busca de ameaças como injeção de SQL, ataques de força bruta e abuso de privilégios. Este serviço dispara alertas de segurança detalhados e orientados a ações na Central de Segurança e fornece opções para continuar as investigações com o Azure Sentinel, a solução SIEM nativa do Microsoft Azure.
- **Descobertas** – Um serviço de avaliação de vulnerabilidade que monitora continuamente as configurações do SQL do Azure e ajuda a corrigir vulnerabilidades. As verificações de avaliação fornecem uma visão geral dos estados de segurança do SQL do Azure junto com as descobertas de segurança detalhadas.     

:::image type="content" source="media/release-notes/azure-security-center-experience-in-sql.png" alt-text="Os recursos de segurança da Central de Segurança do Azure para SQL estão disponíveis no SQL do Azure":::


### <a name="asset-inventory-tools-and-filters-updated"></a>Ferramentas e filtros de inventário de ativos atualizados

A página de inventário na Central de Segurança do Azure foi atualizada com as seguintes alterações:

- **Guias e comentários** adicionados à barra de ferramentas. Isso abre um painel com links para informações e ferramentas relacionadas. 
- **Filtro de assinaturas** adicionado aos filtros padrão disponíveis para seus recursos.
- Link **Abrir consulta** para abrir as opções de filtro atuais como uma consulta do Azure Resource Graph (anteriormente chamada de "Exibir no Resource Graph Explorer").
- **Opções de operador** para cada filtro. Agora você pode escolher entre mais operadores lógicos além de '='. Por exemplo, talvez você queira localizar todos os recursos com recomendações ativas cujos títulos incluem a cadeia de caracteres "encrypt". 

    :::image type="content" source="media/release-notes/inventory-filter-operators.png" alt-text="Controles para a opção de operador nos filtros de inventário de ativos":::

Saiba mais sobre inventário em [Explorar e gerenciar seus recursos com o inventário de ativos](asset-inventory.md).


### <a name="recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score"></a>Recomendação sobre aplicativos Web solicitando certificados SSL que não fazem mais parte da classificação de segurança

A recomendação "Os aplicativos Web devem solicitar um certificado SSL para todas as solicitações de entrada" foi movida do controle de segurança **Gerenciar o acesso e as permissões** (vale no máximo quatro pontos) para **Implementar as melhores práticas de segurança** (que não vale nenhum ponto). 

Garantir que um aplicativo Web solicite um certificado certamente o torna mais seguro. No entanto, para aplicativos Web voltados para o público, é irrelevante. se você acessar seu site por HTTP e não por HTTPS, você não receberá nenhum certificado do cliente. Por isso, se o aplicativo exigir certificados de cliente, você não deverá permitir solicitações ao seu aplicativo via HTTP. Saiba mais em [Como configurar a autenticação mútua TLS para o Serviço de Aplicativo do Azure](../app-service/app-service-web-configure-tls-mutual-auth.md).

Com essa alteração, a recomendação agora é uma prática recomendada que não afetará sua classificação. 

Saiba quais recomendações estão em cada controle de segurança em [Controles de segurança e suas recomendações](secure-score-security-controls.md#security-controls-and-their-recommendations).


### <a name="recommendations-page-has-new-filters-for-environment-severity-and-available-responses"></a>A página de recomendações tem novos filtros para o ambiente, a severidade e as respostas disponíveis

A Central de Segurança do Azure monitora todos os recursos conectados e gera recomendações de segurança. Use essas recomendações para fortalecer sua postura de nuvem híbrida e acompanhar a conformidade com as políticas e os padrões relevantes para sua organização, setor e país.

À medida que a Central de Segurança continua a expandir sua cobertura e seus recursos, a lista de recomendações de segurança cresce todos os meses. Por exemplo, confira [29 recomendações de versão prévia adicionadas para aumentar a cobertura do Azure Security Benchmark](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark).

Com a lista cada vez maior, há a necessidade de filtrar as recomendações para encontrar as de maior interesse. Em novembro, adicionamos filtros à página de recomendações (confira [A lista de recomendações agora inclui filtros](#recommendations-list-now-includes-filters)).

Os filtros adicionados neste mês fornecem opções para refinar a lista de recomendações de acordo com:

- **Ambiente** – Exibir recomendações para seus recursos AWS, GCP ou Azure (ou qualquer combinação)
- **Severidade** – Exibir recomendações de acordo com a classificação de severidade definida pela Central de Segurança
- **Ações de resposta** – Exibir recomendações de acordo com a disponibilidade das opções de resposta da Central de Segurança: Correção rápida, negar e impor

    > [!TIP]
    > O filtro de ações de resposta substitui o filtro de **correção rápida disponível (Sim/Não)** . 
    > 
    > Saiba mais sobre cada uma dessas opções de resposta:
    > - [Correção rápida](security-center-remediate-recommendations.md#quick-fix-remediation)
    > - [Impedir configurações incorretas com as recomendações de Impor/Negar](prevent-misconfigurations.md)

:::image type="content" source="./media/release-notes/added-recommendations-filters.png" alt-text="Recomendações agrupadas por controle de segurança" lightbox="./media/release-notes/added-recommendations-filters.png":::

### <a name="continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies"></a>A exportação contínua obtém novos tipos de dados e políticas de deployifnotexist aprimoradas

As ferramentas de exportação contínua da Central de Segurança do Azure permitem exportar recomendações e alertas da Central de Segurança para uso com outras ferramentas de monitoramento em seu ambiente.

A exportação contínua permite que você personalize totalmente o que será exportado e o local da exportação. Para obter detalhes completos, confira [Exportar continuamente dados da Central de Segurança](continuous-export.md).

Essas ferramentas foram aprimoradas e expandidas das seguintes maneiras:

- **Aprimoramento das políticas deployifnotexist da exportação contínua**. As políticas agora:

    - **Verificam se a configuração está habilitada.** Se não estiver, a política será mostrada como não compatível e criará um recurso compatível. Saiba mais sobre os modelos do Azure Policy fornecidos na guia "Implantar em escala usando o Azure Policy" da opção [Configurar uma exportação contínua](continuous-export.md#set-up-a-continuous-export).

    - **Dão suporte à exportação de descobertas de segurança.** Ao usar os modelos do Azure Policy, você pode configurar sua exportação contínua para incluir descobertas. Isso é relevante ao exportar recomendações com "sub" recomendações, como as descobertas de verificações de avaliação de vulnerabilidade ou atualizações de sistema específicas para a recomendação "pai" "As atualizações do sistema devem ser instaladas em seus computadores".
    
    - **Dão suporte à exportação de dados da classificação de segurança.**

- **Dados de avaliação de conformidade regulatória adicionados (em versão prévia).** Agora você pode exportar atualizações continuamente para avaliações de conformidade regulatória, incluindo para qualquer iniciativa personalizada, para um workspace do Log Analytics ou hub de eventos. Este recurso não está disponível em nuvens nacionais/soberanas.

    :::image type="content" source="media/release-notes/continuous-export-regulatory-compliance-option.png" alt-text="As opções para incluir informações de avaliação de conformidade regulatória com seus dados de exportação contínua.":::


## <a name="november-2020"></a>Novembro de 2020

As atualizações de novembro incluem:

- [29 recomendações de versão prévia adicionadas para aumentar a cobertura do Parâmetro de Comparação de Segurança do Azure](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [NIST SP 800 171 R2 adicionado ao painel de conformidade regulatória da Central de Segurança](#nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard)
- [A lista de recomendações agora inclui filtros](#recommendations-list-now-includes-filters)
- [Experiência de provisionamento automático aprimorada e expandida](#auto-provisioning-experience-improved-and-expanded)
- [A classificação de segurança já está disponível na exportação contínua (versão prévia)](#secure-score-is-now-available-in-continuous-export-preview)
- [A recomendação "Atualizações do sistema deverão ser instaladas em seus computadores" agora inclui sub-recomendações](#system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations)
- [A página de gerenciamento de política no portal do Azure agora mostra o status das atribuições de política padrão](#policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments)

### <a name="29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>29 recomendações de versão prévia adicionadas para aumentar a cobertura do Parâmetro de Comparação de Segurança do Azure

O Azure Security Benchmark é um conjunto específico de diretrizes específicas do Azure criadas pela Microsoft de melhores práticas de segurança e conformidade baseadas em estruturas de conformidade comuns. [Saiba mais sobre o Azure Security Benchmark](../security/benchmarks/introduction.md).

As 29 recomendações de versão prévia a seguir foram adicionadas à Central de Segurança para aumentar a cobertura desse parâmetro de comparação.

As recomendações de versão prévia não processam um recurso não íntegro e não são incluídas nos cálculos de sua classificação de segurança. Corrija-as sempre que possível, para que, quando o período da versão prévia terminar, elas contribuam para sua classificação. Saiba mais sobre como responder a essas recomendações em [Recomendações de correção na Central de Segurança do Azure](security-center-remediate-recommendations.md).

| Controle de segurança                     | Novas recomendações                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Criptografar dados em trânsito              | – Impor conexão SSL deve ser habilitado para servidores de banco de dados PostgreSQL<br>– Impor conexão SSL deve ser habilitado para servidores de banco de dados MySQL<br>– O TLS deve estar atualizado com a última versão para o aplicativo de API<br>– O TLS deve estar atualizado com a última versão para o aplicativo de funções<br>– O TLS deve estar atualizado com a última versão para o aplicativo Web<br>– O FTPS deve ser exigido no aplicativo de API<br>– O FTPS deve ser exigido no aplicativo de funções<br>– O FTPS deve ser exigido no aplicativo Web                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Gerenciar acesso e permissões        | – Os aplicativos Web devem solicitar um certificado SSL para todas as solicitações de entrada<br>– A identidade gerenciada deve ser usada no aplicativo de API<br>– A identidade gerenciada deve ser usada no aplicativo de funções<br>– A identidade gerenciada deve ser usada no aplicativo Web                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Restringir acesso não autorizado à rede | – O ponto de extremidade privado deve ser habilitado para servidores PostgreSQL<br>– O ponto de extremidade privado deve ser habilitado para servidores MariaDB<br>– O ponto de extremidade privado deve ser habilitado para servidores MySQL                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| – Habilitar a auditoria e o registro em log          | – Os logs de diagnóstico devem ser habilitados nos Serviços de Aplicativo                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| Implementar melhores práticas de segurança    | – O Backup do Azure deve ser habilitado para máquinas virtuais<br>– O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para MariaDB<br>– O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para MySQL<br>– O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para PostgreSQL<br>– O PHP deve estar atualizado com a última versão para o aplicativo de API<br>– O PHP deve estar atualizado com a última versão para o aplicativo Web<br>– O Java deve estar atualizado com a última versão para o aplicativo de API<br>– O Java deve estar atualizado com a última versão para o aplicativo de funções<br>– O Java deve estar atualizado com a última versão para o aplicativo Web<br>– O Python deve estar atualizado com a última versão para o aplicativo de API<br>– O Python deve estar atualizado com a última versão para o aplicativo de funções<br>– O Python deve estar atualizado com a última versão para o aplicativo Web<br>– A retenção de auditoria para servidores SQL deve ser definida como pelo menos 90 dias |
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Links relacionados:

- [Saiba mais sobre o Azure Security Benchmark](../security/benchmarks/introduction.md)
- [Saiba mais sobre aplicativos de API do Azure](../app-service/app-service-web-tutorial-rest-api.md)
- [Saiba mais sobre aplicativos de funções do Azure](../azure-functions/functions-overview.md)
- [Saiba mais sobre aplicativos Web do Azure](../app-service/overview.md)
- [Saiba mais sobre o Banco de Dados do Azure para MariaDB](../mariadb/overview.md)
- [Saiba mais sobre o Banco de Dados do Azure para MySQL](../mysql/overview.md)
- [Saiba mais sobre o Banco de Dados do Azure para PostgreSQL](../postgresql/overview.md)


### <a name="nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard"></a>NIST SP 800 171 R2 adicionado ao painel de conformidade regulatória da Central de Segurança

O padrão NIST SP 800-171 R2 já está disponível como uma iniciativa interna para uso com o painel de conformidade regulatória da Central de Segurança do Azure. Os mapeamentos para os controles são descritos em [Detalhes da iniciativa interna de Conformidade Regulatória do NIST SP 800-171 R2](../governance/policy/samples/nist-sp-800-171-r2.md). 

Para aplicar o padrão às suas assinaturas e monitorar continuamente o status de conformidade, use as instruções presentes em [Personalizar o conjunto de padrões em seu painel de conformidade regulatória](update-regulatory-compliance-packages.md).

:::image type="content" source="media/release-notes/nist-sp-800-171-r2-standard.png" alt-text="O padrão NIST SP 800 171 R2 no painel de conformidade regulatória da Central de Segurança":::

Para obter mais informações sobre esse padrão de conformidade, confira [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final).


### <a name="recommendations-list-now-includes-filters"></a>A lista de recomendações agora inclui filtros

Agora você pode filtrar a lista de recomendações de segurança com base em uma gama de critérios. No seguinte exemplo, a lista de recomendações foi filtrada para mostrar recomendações que:

- estão em **disponibilidade geral** (ou seja, não em versão prévia)
- são voltadas para **contas de armazenamento**
- têm suporte para **correção rápida**

:::image type="content" source="media/release-notes/recommendations-filters.png" alt-text="Filtros para a lista de recomendações":::


### <a name="auto-provisioning-experience-improved-and-expanded"></a>Experiência de provisionamento automático aprimorada e expandida

O recurso de provisionamento automático ajuda a reduzir a sobrecarga de gerenciamento instalando as extensões necessárias em VMs novas e existentes do Azure, a fim de que elas possam se beneficiar das proteções da Central de Segurança. 

À medida que a Central de Segurança do Azure cresce, mais extensões são desenvolvidas e é possível monitorar uma lista maior de tipos de recursos. As ferramentas de provisionamento automático já foram expandidas para dar suporte a outras extensões e tipos de recursos aproveitando as funcionalidades do Azure Policy.

Agora você pode configurar o provisionamento automático de:

- Agente do Log Analytics
- (Novo) Complemento do Azure Policy para Kubernetes
- (Novo) Microsoft Dependency Agent

Saiba mais em [Agentes e extensões de provisionamento automático da Central de Segurança do Azure](security-center-enable-data-collection.md).


### <a name="secure-score-is-now-available-in-continuous-export-preview"></a>A classificação de segurança já está disponível na exportação contínua (versão prévia)

Com a exportação contínua da classificação de segurança, você pode transmitir alterações à sua pontuação em tempo real para os Hubs de Eventos do Azure ou um workspace do Log Analytics. Use essa funcionalidade para:

- acompanhar sua classificação de segurança ao longo do tempo com relatórios dinâmicos
- exportar dados de classificação de segurança para o Azure Sentinel (ou qualquer outro SIEM)
- integrar esses dados a qualquer processo que você já esteja usando para monitorar a classificação de segurança em sua organização

Saiba mais sobre como [Exportar continuamente dados da Central de Segurança](continuous-export.md).


### <a name="system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations"></a>A recomendação "Atualizações do sistema deverão ser instaladas em seus computadores" agora inclui sub-recomendações

A recomendação **As atualizações do sistema devem ser instaladas nos computadores** foi aprimorada. A nova versão inclui sub-recomendações para cada atualização ausente e fornece os seguintes aprimoramentos:

- Uma experiência reformulada nas páginas da Central de Segurança do Azure do portal do Azure. A página de detalhes da recomendação para **As atualizações do sistema devem ser instaladas nos computadores** inclui a lista de conclusões, conforme mostrado abaixo. Quando você seleciona uma localização individual, o painel de detalhes é aberto com um link para as informações de correção e uma lista de recursos afetados.

    :::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="Como abrir uma das sub-recomendações na experiência de portal para obter uma recomendação atualizada":::

- Dados aprimorados para a recomendação do ARG (Azure Resource Graph). O ARG é um serviço do Azure que foi projetado para oferecer uma exploração eficiente de recursos. Você pode usar o ARG para consultar em escala um determinado conjunto de assinaturas a fim de controlar seu ambiente de maneira eficaz. 

    Para a Central de Segurança do Azure, você pode usar ARG e [KQL (Kusto Query Language)](/azure/data-explorer/kusto/query/) para consultar uma ampla gama de dados de postura de segurança.

    Anteriormente, se você consultasse essa recomendação no ARG, a única informação disponível seria que a recomendação precisava ser corrigida em um computador. A consulta a seguir da versão aprimorada retornará as atualizações de cada sistema ausente agrupadas por computador.

    ```kusto
    securityresources
    | where type =~ "microsoft.security/assessments/subassessments"
    | where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
    | where properties.status.code == "Unhealthy"
    ```

### <a name="policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments"></a>A página de gerenciamento de política no portal do Azure agora mostra o status das atribuições de política padrão

Agora você pode ver se as suas assinaturas têm ou não a política padrão da Central de Segurança atribuída, na página da **política de segurança** da Central de Segurança do portal do Azure.

:::image type="content" source="media/release-notes/policy-assignment-info-per-subscription.png" alt-text="Página de gerenciamento de política da Central de Segurança do Azure mostrando as atribuições de política padrão":::

## <a name="october-2020"></a>Outubro de 2020

As atualizações de outubro incluem:
- [Avaliação de vulnerabilidade para computadores locais e em várias nuvens (versão prévia)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview)
- [Recomendação do Firewall do Azure adicionada (versão prévia)](#azure-firewall-recommendation-added-preview)
- [Recomendação Os intervalos de IP autorizados devem ser definidos nos Serviços de Kubernetes atualizada com uma correção rápida](#authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix)
- [O painel de conformidade regulatória agora inclui a opção de remoção de padrões](#regulatory-compliance-dashboard-now-includes-option-to-remove-standards)
- [Tabela Microsoft.Security/securityStatuses removida do ARG (Azure Resource Graph)](#microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview"></a>Avaliação de vulnerabilidade para computadores locais e em várias nuvens (versão prévia)

O avaliador de vulnerabilidades do [Azure Defender para servidores](defender-for-servers-introduction.md)' (da plataforma Qualys) agora verifica servidores habilitados para o Azure Arc.

Quando você tiver habilitado o Azure Arc em seus computadores que não são do Azure, a Central de Segurança oferecerá a implantação do verificador de vulnerabilidades integrado neles, manualmente e em escala.

Com essa atualização, você pode tirar proveito da eficiência do **Azure Defender para servidores** para consolidar seu programa de gerenciamento de vulnerabilidades em todos os seus ativos que são e que não são do Azure.

Principais recursos:

- Monitoramento do estado de provisionamento do verificador de VA (avaliação de vulnerabilidade) em computadores do Azure Arc
- Provisionamento do agente de VA integrado para computadores Windows e Linux do Azure Arc desprotegidos (manualmente e em escala)
- Recebimento e análise das vulnerabilidades detectadas por agentes implantados (manualmente e em escala)
- Experiência unificada para VMs do Azure e computadores do Azure Arc

[Saiba mais sobre como implantar o verificador de vulnerabilidades integrado em computadores híbridos](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines).

[Saiba mais sobre os servidores habilitados para Azure Arc](../azure-arc/servers/index.yml).


### <a name="azure-firewall-recommendation-added-preview"></a>Recomendação do Firewall do Azure adicionada (versão prévia)

Uma nova recomendação foi adicionada para proteger todas as suas redes virtuais com o Firewall do Azure.

A recomendação, **Redes virtuais devem ser protegidas pelo Firewall do Azure**, aconselha você a restringir o acesso às suas redes virtuais e evitar possíveis ameaças usando o Firewall do Azure.

Saiba mais sobre [Firewall do Azure](https://azure.microsoft.com/services/azure-firewall/).


### <a name="authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix"></a>Recomendação Os intervalos de IP autorizados devem ser definidos nos Serviços de Kubernetes atualizada com uma correção rápida

A recomendação **Os intervalos de IP autorizados devem ser definidos nos Serviços de Kubernetes** agora tem uma opção de correção rápida.

Para obter mais informações sobre essa recomendação e todas as outras recomendações da Central de Segurança, confira [Recomendações de segurança – um guia de referência](recommendations-reference.md).

:::image type="content" source="./media/release-notes/authorized-ip-ranges-recommendation.png" alt-text="Recomendação Os intervalos de IP autorizados devem ser definidos nos Serviços de Kubernetes com a opção de correção rápida":::


### <a name="regulatory-compliance-dashboard-now-includes-option-to-remove-standards"></a>O painel de conformidade regulatória agora inclui a opção de remoção de padrões

O painel de conformidade regulatória da Central de Segurança fornece informações sobre sua postura de conformidade com base em como você está atendendo a requisitos e controles de conformidade específicos.

O painel inclui um conjunto padrão de padrões regulatórios. Se um dos padrões fornecidos não é relevante para sua organização, removê-los da interface do usuário de uma assinatura passou a ser um processo simples. Os padrões podem ser removidos somente no nível da *assinatura* não no escopo do grupo de gerenciamento.

Saiba mais em [Remover um padrão de seu painel](update-regulatory-compliance-packages.md#removing-a-standard-from-your-dashboard).


### <a name="microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg"></a>Tabela Microsoft.Security/securityStatuses removida do ARG (Azure Resource Graph)

O Azure Resource Graph é um serviço no Azure desenvolvido para fornecer exploração de recursos eficiente, com a capacidade de consultar em escala um determinado conjunto de assinaturas, permitindo a você controlar o seu ambiente de maneira efetiva. 

Para a Central de Segurança do Azure, você pode usar ARG e [KQL (Kusto Query Language)](/azure/data-explorer/kusto/query/) para consultar uma ampla gama de dados de postura de segurança. Por exemplo:

- Utilizações de inventário de ativos (ARG)
- Documentamos um exemplo de consulta do ARG para saber como [Identificar contas sem a MFA (autenticação multifator) habilitada](security-center-identity-access.md#identify-accounts-without-multi-factor-authentication-mfa-enabled)

Há tabelas de dados no ARG que poderão ser usadas em suas consultas.

:::image type="content" source="./media/release-notes/azure-resource-graph-tables.png" alt-text="Azure Resource Graph Explorer e as tabelas disponíveis":::

> [!TIP]
> A documentação do ARG lista todas as tabelas disponíveis na [Referência de tabela e tipo de recurso do Azure Resource Graph](../governance/resource-graph/reference/supported-tables-resources.md).

Nessa atualização, a tabela **Microsoft.Security/securityStatuses** foi removida. A API securityStatuses ainda está disponível.

A substituição de dados pode ser usada pela tabela Microsoft.Security/Assessments.

A principal diferença entre Microsoft.Security/securityStatuses e Microsoft.Security/Assessments é que, enquanto o primeiro mostra a agregação de avaliações, o segundo mantém um registro para cada uma.

Por exemplo, Microsoft.Security/securityStatuses retornaria um resultado com uma matriz de duas policyAssessments:

```
{
id: "/subscriptions/449bcidd-3470-4804-ab56-2752595 felab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/securityStatuses/mico-rg-vnet",
name: "mico-rg-vnet",
type: "Microsoft.Security/securityStatuses",
properties:  {
    policyAssessments: [
        {assessmentKey: "e3deicce-f4dd-3b34-e496-8b5381bazd7e", category: "Networking", policyName: "Azure DDOS Protection Standard should be enabled",...},
        {assessmentKey: "sefac66a-1ec5-b063-a824-eb28671dc527", category: "Compute", policyName: "",...}
    ],
    securitystateByCategory: [{category: "Networking", securityState: "None" }, {category: "Compute",...],
    name: "GenericResourceHealthProperties",
    type: "VirtualNetwork",
    securitystate: "High"
}
```
Por sua vez, Microsoft.Security/Assessments manterá um registro para cada avaliação de política, da seguinte maneira:

```
{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft. Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/e3delcce-f4dd-3b34-e496-8b5381ba2d70",
name: "e3deicce-f4dd-3b34-e496-8b5381ba2d70",
properties:  {
    resourceDetails: {Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet"...},
    displayName: "Azure DDOS Protection Standard should be enabled",
    status: (code: "NotApplicable", cause: "VnetHasNOAppGateways", description: "There are no Application Gateway resources attached to this Virtual Network"...}
}

{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/80fac66a-1ec5-be63-a824-eb28671dc527",
name: "8efac66a-1ec5-be63-a824-eb28671dc527",
properties: {
    resourceDetails: (Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet"...),
    displayName: "Audit diagnostic setting",
    status:  {code: "Unhealthy"}
}
```

**Exemplo de conversão de uma consulta de ARG existente usando securityStatuses para usar a tabela de avaliações:**

Consulta que faz referência a SecurityStatuses:

```kusto
SecurityResources 
| where type == 'microsoft.security/securitystatuses' and properties.type == 'virtualMachine'
| where name in ({vmnames}) 
| project name, resourceGroup, policyAssesments = properties.policyAssessments, resourceRegion = location, id, resourceDetails = properties.resourceDetails
```

Consulta de substituição para a tabela de Avaliações:

```kusto
securityresources
| where type == "microsoft.security/assessments" and id contains "virtualMachine"
| extend resourceName = extract(@"(?i)/([^/]*)/providers/Microsoft.Security/assessments", 1, id)
| extend source = tostring(properties.resourceDetails.Source)
| extend resourceId = trim(" ", tolower(tostring(case(source =~ "azure", properties.resourceDetails.Id,
source =~ "aws", properties.additionalData.AzureResourceId,
source =~ "gcp", properties.additionalData.AzureResourceId,
extract("^(.+)/providers/Microsoft.Security/assessments/.+$",1,id)))))
| extend resourceGroup = tolower(tostring(split(resourceId, "/")[4]))
| where resourceName in ({vmnames}) 
| project resourceName, resourceGroup, resourceRegion = location, id, resourceDetails = properties.additionalData
```

Saiba mais consultando os seguintes links:
- [Como criar consultas com o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)
- [KQL (Kusto Query Language)](/azure/data-explorer/kusto/query/)


## <a name="september-2020"></a>Setembro de 2020

As atualizações de setembro incluem:
- [A Central de Segurança ganha uma nova aparência!](#security-center-gets-a-new-look)
- [Lançamento do Azure Defender](#azure-defender-released)
- [Azure Defender para Key Vault em disponibilidade geral](#azure-defender-for-key-vault-is-generally-available)
- [Proteção do Azure Defender para Armazenamento para os Arquivos e o ADLS Gen2 em disponibilidade geral](#azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available)
- [Ferramentas de inventário de ativos em disponibilidade geral](#asset-inventory-tools-are-now-generally-available)
- [Desabilitar uma descoberta de vulnerabilidade específica nas verificações de registros de contêiner e máquinas virtuais](#disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines)
- [Isentar um recurso de uma recomendação](#exempt-a-resource-from-a-recommendation)
- [Os conectores da AWS e do GCP na Central de Segurança trazem uma experiência de várias nuvens](#aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience)
- [Pacote de recomendações da proteção de cargas de trabalho do Kubernetes](#kubernetes-workload-protection-recommendation-bundle)
- [Disponibilidade das descobertas da avaliação de vulnerabilidades na exportação contínua](#vulnerability-assessment-findings-are-now-available-in-continuous-export)
- [Impedir configurações incorretas de segurança impondo recomendações durante a criação de recursos](#prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources)
- [Aprimoramento das recomendações de grupo de segurança de rede](#network-security-group-recommendations-improved)
- [Reprovação da recomendação da versão prévia do AKS "As Políticas de Segurança de Pods devem ser definidas nos Serviços de Kubernetes"](#deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services)
- [Aprimoramento nas notificações por email da Central de Segurança do Azure](#email-notifications-from-azure-security-center-improved)
- [A classificação de segurança não inclui recomendações de versão prévia](#secure-score-doesnt-include-preview-recommendations)
- [As recomendações agora incluem um indicador de severidade e o intervalo de atualização](#recommendations-now-include-a-severity-indicator-and-the-freshness-interval)


### <a name="security-center-gets-a-new-look"></a>A Central de Segurança ganha uma nova aparência!

Lançamos uma interface do usuário atualizada para as páginas do portal da Central de Segurança. As novas páginas incluem uma nova página de visão geral e painéis para a classificação de segurança, o inventário de ativos e o Azure Defender.

A página de visão geral remodelada agora traz um bloco para acessar os painéis da classificação de segurança, do inventário de ativos e do Azure Defender. Também traz um bloco vinculado ao painel de conformidade regulatória.

Saiba mais sobre a [página de visão geral](overview-page.md).


### <a name="azure-defender-released"></a>Lançamento do Azure Defender

O **Azure Defender** é a PPCTN (plataforma de proteção de cargas de trabalho na nuvem) integrada à Central de Segurança para proteção avançada e inteligente das suas cargas de trabalho híbridas e do Azure. Ele substitui a opção de tipo de preço Standard da Central de Segurança. 

Quando você habilita o Azure Defender na área **Preços e configurações** da Central de Segurança do Azure, os seguintes planos do Defender são todos habilitados simultaneamente e fornecem proteções abrangentes para as camadas de computação, dados e serviço do seu ambiente:

- [Azure Defender para servidores](defender-for-servers-introduction.md)
- [Azure Defender for Serviço de Aplicativo](defender-for-app-service-introduction.md)
- [Azure Defender para Armazenamento](defender-for-storage-introduction.md)
- [Azure Defender para SQL](defender-for-sql-introduction.md)
- [Azure Defender para Key Vault](defender-for-key-vault-introduction.md)
- [Azure Defender para Kubernetes](defender-for-kubernetes-introduction.md)
- [Azure Defender para registros de contêiner](defender-for-container-registries-introduction.md)

Cada um desses planos é explicado separadamente na documentação da Central de Segurança.

Com um painel dedicado, o Azure Defender fornece alertas de segurança e Proteção Avançada contra Ameaças para máquinas virtuais, bancos de dados SQL, contêineres, aplicativos Web, sua rede, entre outros.

[Saiba mais sobre o Azure Defender](azure-defender.md)

### <a name="azure-defender-for-key-vault-is-generally-available"></a>Azure Defender para Key Vault em disponibilidade geral

O Azure Key Vault é um serviço de nuvem que protege segredos e chaves de criptografia, como certificados, cadeias de conexão e senhas. 

O **Azure Defender para Key Vault** fornece proteção avançada e nativa do Azure contra ameaças para o Azure Key Vault, oferecendo uma camada adicional de inteligência de segurança. Por extensão, o Azure Defender para Key Vault está, consequentemente, protegendo muitos dos recursos dependentes das suas contas do Key Vault.

Agora, o plano opcional é GA. Esse recurso estava em versão prévia como "Proteção Avançada contra Ameaças para Azure Key Vault".

Além disso, as páginas do Key Vault no portal do Azure agora incluem uma página **Segurança** dedicada para as recomendações e os alertas da **Central de Segurança**.

Saiba mais em [Azure Defender para Key Vault](defender-for-key-vault-introduction.md).


### <a name="azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available"></a>Proteção do Azure Defender para Armazenamento para os Arquivos e o ADLS Gen2 em disponibilidade geral 

O **Azure Defender para Armazenamento** detecta atividades potencialmente prejudiciais nas contas do Armazenamento do Azure. Seus dados podem ser protegidos independentemente de estarem armazenados como contêineres de blob, compartilhamentos de arquivos ou data lakes.

O suporte para os [Arquivos do Azure](../storage/files/storage-files-introduction.md) e o [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) já está em disponibilidade geral.

Desde 1º de outubro de 2020, começamos a cobrar pela proteção de recursos nesses serviços.

Saiba mais em [Azure Defender para Armazenamento](defender-for-storage-introduction.md).


### <a name="asset-inventory-tools-are-now-generally-available"></a>Ferramentas de inventário de ativos em disponibilidade geral

A página de inventário de ativos da Central de Segurança do Azure fornece uma só página para exibição da postura de segurança dos recursos que você conectou à Central de Segurança.

A Central de Segurança analisa periodicamente o estado de segurança dos seus recursos do Azure para identificar possíveis vulnerabilidades na segurança. Em seguida, ela fornece recomendações sobre como corrigir essas vulnerabilidades.

Quando qualquer recurso tiver recomendações pendentes, eles serão exibidos no inventário.

Saiba mais em [Explorar e gerenciar recursos usando um inventário de ativos](asset-inventory.md).



### <a name="disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines"></a>Desabilitar uma descoberta de vulnerabilidade específica nas verificações de registros de contêiner e máquinas virtuais

O Azure Defender inclui verificadores de vulnerabilidade para examinar imagens no seu Registro de Contêiner do Azure e nas suas máquinas virtuais.

Caso você tenha uma necessidade organizacional para ignorar uma descoberta, em vez de corrigi-la, você pode opcionalmente desabilitá-la. As descobertas desabilitadas não afetam sua classificação de segurança nem geram um ruído indesejado.

Quando uma descoberta corresponde aos critérios definidos nas regras de desabilitação, ela não será exibida na lista de descobertas.

Essa opção está disponível nas páginas de detalhes de recomendações para:

- **As vulnerabilidades nas imagens do Registro de Contêiner do Azure devem ser corrigidas**
- **As vulnerabilidades nas suas máquinas virtuais devem ser corrigidas**

Saiba mais em [Desabilitar descobertas específicas nas imagens de contêiner](defender-for-container-registries-usage.md#disable-specific-findings-preview) e [Desabilitar descobertas específicas nas máquinas virtuais](remediate-vulnerability-findings-vm.md#disable-specific-findings-preview).


### <a name="exempt-a-resource-from-a-recommendation"></a>Isentar um recurso de uma recomendação

Ocasionalmente, um recurso será listado como não íntegro em relação a uma recomendação específica (e, portanto, reduzindo sua classificação de segurança) mesmo que você ache que ele não deveria ser. Ele pode ter sido corrigido por um processo não acompanhado pela Central de Segurança. Ou, talvez, sua organização tenha decidido aceitar o risco para esse recurso específico. 

Nesses casos, você pode criar uma regra de isenção e garantir que o recurso não seja listado entre os recursos não íntegros no futuro. Essas regras podem incluir justificações documentadas, conforme descrito abaixo.

Saiba mais em [Isentar um recurso das recomendações e da classificação de segurança](exempt-resource.md).


### <a name="aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience"></a>Os conectores da AWS e do GCP na Central de Segurança trazem uma experiência de várias nuvens

Com as cargas de trabalho de nuvem normalmente abrangendo plataformas de várias nuvens, os serviços de segurança de nuvem precisam fazer o mesmo.

A Central de Segurança do Azure agora protege as cargas de trabalho no Azure, a AWS (Amazon Web Services) e o GCP (Google Cloud Platform).

A integração das suas contas da AWS e do GCP à Central de Segurança integra o AWS Security Hub, o GCP Security Command e a Central de Segurança do Azure. 

Saiba mais em [Conectar suas contas da AWS à Central de Segurança do Azure](quickstart-onboard-aws.md) e [Conectar suas contas do GCP à Central de Segurança do Azure](quickstart-onboard-gcp.md).


### <a name="kubernetes-workload-protection-recommendation-bundle"></a>Pacote de recomendações da proteção de cargas de trabalho do Kubernetes

Para garantir que as cargas de trabalho do Kubernetes sejam seguras por padrão, a Central de Segurança está adicionando recomendações de proteção no nível do Kubernetes, incluindo opções de imposição com o controle de admissão do Kubernetes.

Quando você instalar o complemento do Azure Policy para Kubernetes no seu cluster do AKS, todas as solicitações para o servidor de API do Kubernetes serão monitoradas em relação ao conjunto predefinido de melhores práticas antes de serem persistidas no cluster. Em seguida, você pode configurá-lo para impor as melhores práticas e exigir o uso dele em cargas de trabalho futuras.

Por exemplo, você poderá proibir a criação de contêineres privilegiados, e todas as futuras solicitações para fazer isso serão bloqueadas.

Saiba mais em [Melhores práticas de proteção de cargas de trabalho usando o controle de admissão do Kubernetes](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control).


### <a name="vulnerability-assessment-findings-are-now-available-in-continuous-export"></a>Disponibilidade das descobertas da avaliação de vulnerabilidades na exportação contínua

Use a exportação contínua para transmitir alertas e recomendações em tempo real para os Hubs de Eventos do Azure, os workspaces do Log Analytics ou o Azure Monitor. Neles, você pode integrar esses dados aos SIEMs (como o Azure Sentinel, o Power BI, o Azure Data Explorer, entre outros).

As ferramentas integradas de avaliação de vulnerabilidades da Central de Segurança retornam descobertas sobre seus recursos como recomendações práticas em uma recomendação "pai", por exemplo, "As vulnerabilidades nas suas máquinas virtuais devem ser corrigidas". 

As descobertas de segurança já estão disponíveis para exportação por meio da exportação contínua quando você seleciona as recomendações e habilita a opção **Incluir descobertas de segurança**.

:::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Incluir a alternância de descobertas de segurança na configuração de exportação contínua" :::

Páginas relacionadas:

- [Solução de avaliação de vulnerabilidades integrada da Central de Segurança para máquinas virtuais do Azure](deploy-vulnerability-assessment-vm.md)
- [Solução de avaliação de vulnerabilidades integrada da Central de Segurança para imagens do Registro de Contêiner do Azure](defender-for-container-registries-usage.md)
- [Exportação contínua](continuous-export.md)

### <a name="prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources"></a>Impedir configurações incorretas de segurança impondo recomendações durante a criação de recursos

Configurações incorretas de segurança são uma das principais causas de incidentes de segurança. Agora a Central de Segurança pode ajudar a *impedir* configurações incorretas de novos recursos, com relação a recomendações específicas. 

Esse recurso pode ajudar a manter suas cargas de trabalho seguras e estabilizar sua classificação de segurança.

A imposição de uma configuração segura, com base em uma recomendação específica, é oferecida em dois modos:

- Usando o efeito **Negar** do Azure Policy, você pode interromper a criação de recursos não íntegros

- Usando a opção **Impor**, você pode aproveitar o efeito **DeployIfNotExist** da política do Azure e corrigir automaticamente os recursos fora de conformidade após a criação
 
Isso está disponível para as recomendações de segurança selecionadas e pode ser encontrado na parte superior da página de detalhes do recurso.

Saiba mais em [Impedir configurações incorretas com as recomendações de Impor/Negar](prevent-misconfigurations.md).

###  <a name="network-security-group-recommendations-improved"></a>Aprimoramento das recomendações de grupo de segurança de rede

As recomendações de segurança a seguir relacionadas aos grupos de segurança de rede foram aprimoradas para reduzir algumas instâncias de falsos positivos.

- Todas as portas de rede devem ser restritas no NSG associado à sua VM
- Portas de gerenciamento devem ser fechadas nas máquinas virtuais
- As máquinas virtuais para a Internet devem ser protegidas com Grupos de Segurança de Rede
- As sub-redes devem ser associadas a um Grupo de Segurança de Rede


### <a name="deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services"></a>Reprovação da recomendação da versão prévia do AKS "As Políticas de Segurança de Pods devem ser definidas nos Serviços de Kubernetes"

A recomendação de versão prévia "As Políticas de Segurança de Pods devem ser definidas nos Serviços de Kubernetes" está sendo preterida, conforme descrito na documentação do [Serviço de Kubernetes do Azure](../aks/use-pod-security-policies.md).

O recurso da política de segurança de pods (versão prévia) foi configurado para ser substituído e não está mais disponível desde 15 de outubro de 2020 a fim de beneficiar o Azure Policy para AKS.

Depois que a política de segurança de pods (versão prévia) for preterida, você precisará desabilitar o recurso em todos os clusters existentes usando o recurso preterido para realizar futuras atualizações de cluster e permanecer dentro do suporte do Azure.


### <a name="email-notifications-from-azure-security-center-improved"></a>Aprimoramento nas notificações por email da Central de Segurança do Azure

As seguintes áreas dos emails relacionadas a alertas de segurança foram aprimoradas: 

- Adição da capacidade de enviar notificações por email sobre alertas para todos os níveis de severidade
- Adição da capacidade de notificar os usuários com diferentes funções do Azure na assinatura
- Por padrão, estamos notificando de maneira proativa os proprietários da assinatura em alertas de alta severidade (que têm uma alta probabilidade de serem violações autênticas)
- Removemos o campo de número de telefone da página de configuração das notificações de email

Saiba mais em [Configurar notificações por email para alertas de segurança](security-center-provide-security-contact-details.md).


### <a name="secure-score-doesnt-include-preview-recommendations"></a>A classificação de segurança não inclui recomendações de versão prévia 

A Central de Segurança avalia continuamente seus recursos, suas assinaturas e a organização em busca de problemas de segurança. Em seguida, ele agrega todas as conclusões em uma única pontuação para que você possa ver, rapidamente, sua situação de segurança atual: quanto maior a pontuação, menor o nível de risco identificado.

À medida que novas ameaças são descobertas, novas orientações de segurança são disponibilizadas na Central de Segurança por meio de novas recomendações. Para evitar alterações de surpresa na sua classificação de segurança e fornecer um período de cortesia no qual você possa explorar novas recomendações antes que elas afetem suas pontuações, as recomendações sinalizadas como **Versão prévia** não serão mais incluídas nos cálculos da sua classificação de segurança. Elas ainda deverão ser corrigidas sempre que possível, para que, quando o período de versão prévia terminar, elas contribuam para a sua classificação.

Além disso, as recomendações de **Versão prévia** não renderizam um recurso "Não íntegro".

Um exemplo de recomendação de versão prévia:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Recomendação com o sinalizador de versão prévia":::

[Saiba mais sobre a classificação de segurança](secure-score-security-controls.md).


### <a name="recommendations-now-include-a-severity-indicator-and-the-freshness-interval"></a>As recomendações agora incluem um indicador de severidade e o intervalo de atualização

A página de detalhes de recomendações agora inclui um indicador de intervalo de atualização (quando relevante) e uma exibição clara da severidade da recomendação.

:::image type="content" source="./media/release-notes/recommendations-severity-freshness-indicators.png" alt-text="Página de recomendações mostrando a atualização e a severidade":::
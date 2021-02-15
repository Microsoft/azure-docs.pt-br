---
title: Fatores que influenciam o desempenho do Azure AD Connect
description: Este documento explica como vários fatores influenciam o mecanismo Microsoft Azure Active Directory Connect. Esses fatores vão ajudar as organizações a planejar sua implantação do Microsoft Azure Active Directory Connect para garantir que ele atende às suas necessidades de sincronização.
services: active-directory
author: billmath
manager: daveba
tags: azuread
ms.service: active-directory
ms.subservice: hybrid
ms.topic: conceptual
ms.workload: identity
ms.date: 10/06/2018
ms.reviewer: martincoetzer
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15bcb0f7ca30c343072da396abeac8d08dee03a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90087002"
---
# <a name="factors-influencing-the-performance-of-azure-ad-connect"></a>Fatores que influenciam o desempenho do Azure AD Connect

O Microsoft Azure Active Directory Connect sincroniza seu Active Directory para o Azure AD. Esse servidor é um componente crítico para mover suas identidades de usuário para a nuvem. Os principais fatores que afetam o desempenho de Azure AD Connect são:

| **Fator de design**| **Definição** |
|:-|-|
| Topologia| A distribuição dos componentes e os pontos de extremidade do Azure AD Connect deve gerenciar na rede. |
| Escala| O número de objetos, como os usuários, grupos e UOs, para ser gerenciado pelo Azure AD Connect. |
| Hardware| O hardware (físico ou virtual) para o Azure AD Connect e a capacidade de desempenho dependentes de cada componente de hardware, incluindo CPU, memória, rede e configuração de disco rígido. |
| Configuração| Como o Microsoft Azure Active Directory Connect processa os diretórios e informações. |
| Carregar| Frequência de alterações do objeto. As cargas podem variar durante uma hora, dia ou semana. Dependendo do componente, talvez você precise de design para a carga de pico ou carregamento médio. |

A finalidade deste documento é descrever os fatores que influenciam o desempenho do mecanismo de provisionamento do Azure AD Connect. Organizações grandes ou complexas (organizações de provisionamento com mais de 100.000 objetos) podem usar as recomendações para otimizar sua implementação do Azure AD Connect, se enfrentarem problemas de desempenho descritos aqui. Os outros componentes do Azure AD Connect, tais como a integridade [do Azure AD Connect health](how-to-connect-health-agent-install.md) e os agentes não são abordados aqui.

> [!IMPORTANT]
> A Microsoft não oferece suporte à modificação ou à operação do Azure AD Connect fora das ações formalmente documentadas. Qualquer uma dessas ações pode resultar em um estado inconsistente ou sem suporte de Azure AD Connect sincronização. Como resultado, a Microsoft não pode fornecer suporte técnico para essas implantações.

## <a name="azure-ad-connect-component-factors"></a>Fatores de componente do Azure AD Connect

O diagrama a seguir mostra uma arquitetura de alto nível de provisionamento de mecanismo para se conectar a uma única floresta, embora várias florestas tenham suporte. Essa arquitetura mostra como os vários componentes interagem uns com os outros.

![O diagrama mostra como os diretórios conectados e o mecanismo de provisionamento do Azure AD Connect interagem, incluindo o espaço do conector e os componentes de metaverso em um banco de dados SQL. ](media/plan-connect-performance-factors/AzureADConnentInternal.png)

O mecanismo de provisionamento se conecta a cada floresta do Active Directory e ao Microsoft Azure Active Directory. O processo de ler informações de cada diretório é chamado de importação. Exportação se refere à atualização dos diretórios do mecanismo de provisionamento. A sincronização avalia as regras de como os objetos fluirão dentro do mecanismo de provisionamento. Para se aprofundar, você pode consultar [sincronização do Azure AD Connect: noções básicas sobre a arquitetura](./concept-azure-ad-connect-sync-architecture.md).

O Azure Active Directory usa as seguintes áreas de preparação, regras e processos para permitir a sincronização do Active Directory para o Azure AD:

* **Espaço do conector (CS)** - objetos de cada diretório conectado (CD), os diretórios reais são testados aqui pela primeira vez antes que possam ser processados pelo mecanismo de provisionamento. O Azure Active Directory tem seu próprio CS e cada floresta que você se conectar tem seu próprio CS.
* **Metaverse (MV)** - objetos que precisam ser sincronizados são criados aqui com base nas regras de sincronização. Objetos devem existir na MV antes que possam preencher os objetos e atributos para os outros diretórios conectados. Há apenas uma MV.
* **Regras de sincronização** - decidem quais objetos serão criados (projetados) ou conectados (associados) para objetos no MV. As regras de sincronização também decidem quais valores de atributo serão copiados ou transformados de e para os diretórios.
* **Perfis de execução** - agrupa as etapas do processo de copiar objetos e valores de atributo de acordo com as regras de sincronização entre as áreas de preparo e os diretórios conectados.

Existem diferentes perfis de execução para otimizar o desempenho do mecanismo de provisionamento. A maioria das organizações usará os agendamentos padrão e perfis para operações normais de execução, mas algumas organizações talvez precisem [alterar o agendamento](./how-to-connect-sync-feature-scheduler.md) ou disparar outro perfis de execução para atender situações incomuns. Os relatórios a seguir estão disponíveis:

### <a name="initial-sync-profile"></a>Perfil de sincronização inicial

O perfil de sincronização inicial é o processo de leitura dos diretórios conectados, como uma floresta do Active Directory, pela primeira vez. Em seguida, ele faz uma análise sobre todas as entradas no banco de dados do mecanismo de sincronização. O ciclo inicial criará novos objetos no Azure Active Directory e levará tempo extra para ser concluído se suas florestas do Active Directory forem grandes. Um ciclo de sincronização completa inclui as seguintes etapas:

1. Importação completa de todos os conectores
2. Sincronização completa de todos os conectores
3. Exportação em todos os conectores

### <a name="delta-sync-profile"></a>Perfil de sincronização inicial

Para otimizar o processo de sincronização, este perfil de execução somente processa as alterações (cria, exclui e atualiza) de objetos em seus diretórios conectados, desde o último processo de sincronização. Por padrão, o perfil de sincronização delta é executado a cada 30 minutos. As organizações devem se esforçar para manter o tempo necessário para abaixo de 30 minutos, para garantir que o Azure Active Directory esteja atualizado. Para monitorar a integridade do Azure Active Directory Connect, use o [agente de monitoramento de integridade](how-to-connect-health-sync.md) para ver todos os problemas com o processo. Um perfil de sincronização delta inclui as seguintes etapas:

1. Importação delta em todos os conectores
2. Sincronização delta em todos os conectores
3. Exportação em todos os conectores

Um cenário de sincronização de delta de organização corporativo típico é:

- ~ 1% dos objetos são excluídos
- ~ 1% dos objetos são criados
- ~ 5% dos objetos são modificados

A taxa de alteração pode variar dependendo da frequência com a sua organização atualiza os usuários no seu Azure Active Directory. Por exemplo, taxas mais altas de alteração podem ocorrer com a sazonalidade de contratação e reduzir a força de trabalho.

### <a name="full-sync-profile"></a>Perfil de sincronização inicial

Um ciclo de sincronização completo é necessário se você tiver feito qualquer uma das seguintes configurações:



- Aumentou o escopo dos objetos ou atributos a serem importados em diretórios conectados. Por exemplo, quando você adiciona um domínio ou unidade organizacional para o escopo de importação.
- Feitas alterações nas regras de sincronização. Por exemplo, quando você cria uma nova regra para preencher o título do usuário no Azure Active Directory de extension_attribute3 no Azure Active Directory. Esta atualização exige que o mecanismo de provisionamento examine novamente todos os usuários existentes para atualizar seus títulos para aplicar a alteração no futuro.

As operações a seguir estão incluídas em um ciclo de sincronização completa:

1. Importação completa de todos os conectores
2. Sincronização delta/plena em todos os conectores
3. Exportação em todos os conectores

> [!NOTE]
> Planejamento cuidadoso é necessário ao fazer atualizações em massa para vários objetos no seu Active Directory ou Azure AD. Atualizações em massa farão com que o processo de sincronização delta levar mais tempo ao importar, já que muitos objetos foram alterados. Importações longas podem acontecer mesmo que a atualização em massa não influencie o processo de sincronização. Por exemplo, atribuir licenças a muitos usuários no Azure Active Directory causará um ciclo longe de importação do Azure Active Directory, mas não resultará em mudanças de atributo no Active Directory.

### <a name="synchronization"></a>Sincronização

O runtime do processo de sincronização tem as seguintes características de desempenho:

* A sincronização é de thread único, o que significa que o mecanismo de provisionamento não faz qualquer processamento paralelo dos perfis de execução de diretórios conectados, objetos ou atributos.
* O tempo de importação aumenta linearmente com o número de objetos que estão sendo sincronizados. Por exemplo, se os 10.000 objetos levarem 10 minutos para importar, em seguida, 20.000 objetos levarão aproximadamente 20 minutos no mesmo servidor.
* A exportação também é linear.
* A sincronização aumentará exponencialmente com base no número de objetos com referências a outros objetos. As associações de grupo e grupos aninhados têm o impacto de desempenho principal, porque seus membros fazem referência a objetos de usuário ou outros grupos. Essas referências devem ser encontradas e referenciadas para objetos reais no MV para concluir o ciclo de sincronização.

### <a name="filtering"></a>Filtragem

O tamanho de topologia do Active Directory que você deseja importar é o fator número um ao influenciar o desempenho e o tempo geral que levará os componentes internos do mecanismo de provisionamento.

[Filtragem](./how-to-connect-sync-configure-filtering.md) deve ser usado para reduzir os objetos para os sincronizados. Isso impedirá objetos desnecessários do que está sendo processado e exportado para o Microsoft Azure Active Directory. Ordem de preferência, as técnicas de filtragem a seguir estão disponíveis:



- **Filtragem baseada em domínio** – use esta opção para selecionar domínios específicos para sincronizar com o Microsoft Azure Active Directory. Você também pode adicionar e remover domínios da configuração do mecanismo de sincronização quando fizer alterações na infraestrutura local, depois de instalar sincronização do Azure AD Connect.
- **Filtragem da Unidade Organizacional (UO)** - usa UOs para objetos de destino específicos em domínios do Active Directory para provisionamento para o Microsoft Azure Active Directory. Filtragem de UO é o segundo mecanismo de filtragem recomendado, porque usa as consultas de escopo LDAP simples para importar um subconjunto menor de objetos do Active Directory.
- **Filtragem de atributo por objeto** - usa os valores de atributo em objetos para decidir se o objeto específico no Active Directory é provisionado no Azure Active Directory. A filtragem de atributo é ótima para ajustar seus filtros, quando a filtragem de domínio e UO não atendem aos requisitos específicos de filtragem. A filtragem de atributo não reduz o tempo de importação, mas pode reduzir a sincronização e exportar tempos.
- **Filtragem baseada em grupo** - usa associação para decidir se os objetos devem ser provisionados no Azure Active Directory. A filtragem baseada em grupo é adequada apenas para situações de teste e não é recomendada para produção, devido à sobrecarga necessária para verificar a associação de grupo durante o ciclo de sincronização adicional.

Muitos objetos [persistentes do desconector](concept-azure-ad-connect-sync-architecture.md#relationships-between-staging-objects-and-metaverse-objects) no seu Active Directory CS do diretório podem causar tempos de sincronização, pois o mecanismo de provisionamento deve reavaliar cada objeto desconector para conexão possível no ciclo de sincronização. Para superar esse problema, considere uma das seguintes recomendações:



- Coloque os objetos de desconector fora do escopo de importação usando o domínio ou a filtragem de UO.
- Projeto/junção dos objetos a serem de MV e defina o atributo [cloudFiltered](how-to-connect-sync-configure-filtering.md#negative-filtering-do-not-sync-these) igual a True, para impedir o provisionamento desses objetos no Azure Active Directory CS.

> [!NOTE]
> Os usuários podem confundir ou problemas de permissões de aplicativo podem ocorrer quando muitos objetos são filtrados. Por exemplo, em uma implementação híbrida online do Exchange, os usuários com caixas de email nos locais verão mais usuários em sua lista de endereço global do que o usuários com caixas de email no Exchange online. Em outros casos, um usuário deseja conceder acesso em um aplicativo de nuvem para outro usuário que não faz parte do escopo do conjunto filtrado de objetos.

### <a name="attribute-flows"></a>Fluxos de atributos

Fluxos de atributo é o processo para a cópia ou transforma os valores de atributo de objetos de um diretório conectado para outro diretório conectado. São definidos como parte das regras de sincronização. Por exemplo, quando o número de telefone de um usuário é alterado no Active Directory, o número de telefone no Azure AD será atualizado. As organizações podem [modificar os fluxos de atributo](./how-to-connect-sync-change-the-configuration.md) para se adequar a vários requisitos. É recomendável copiar os fluxos de atributo existente antes de alterá-los.

Redirecionamentos simples, como fluir um valor de atributo para um atributo diferente não tem impacto no desempenho de material. Um exemplo de um redirecionamento está fluindo de um número de celular no Active Directory para o número de telefone comercial no Microsoft Azure Active Directory.

Transformar os valores de atributo pode ter um impacto de desempenho sobre o processo de sincronização. Transformar os valores de atributo inclui modificar, reformatar, concatenar ou subtrair valores de atributos.

As organizações podem impedir que determinados atributos fluam para o Microsoft Azure Active Directory, mas isso não influencia o desempenho do mecanismo de provisionamento.

> [!NOTE]
> Não exclua fluxos de atributo indesejado em suas regras de sincronização. É recomendável em vez disso, desabilitá-los, porque as regras excluídas são recriadas durante as atualizações do Microsoft Azure Active Directory Connect.

## <a name="azure-ad-connect-dependency-factors"></a>Fatores de dependência do Microsoft Azure Active Directory Connect

O desempenho do Microsoft Azure Active Directory Connect é depende do desempenho dos diretórios conectados, ele importa e exporta. Por exemplo, o tamanho do Active Directory, é necessário importar ou a latência de rede para o serviço do Azure AD. O banco de dados SQL que o mecanismo de provisionamento usa também afeta o desempenho geral do ciclo de sincronização.

### <a name="active-directory-factors"></a>Fatores do Active Directory

Conforme mencionado anteriormente, o número de objetos a ser importado influencia o desempenho significativamente. O [pré-requisitos para o Azure AD Connect e hardware](how-to-connect-install-prerequisites.md) descreve as camadas de hardware específico com base no tamanho da sua implantação. O Azure AD Connect só é compatível com topologias específicas conforme descrito nas [topologias para o Azure AD Connect](plan-connect-topologies.md). Não há otimizações de desempenho e recomendações para topologias não compatíveis.

Verifique o seu servidor do Azure AD Connect atende os requisitos de hardware com base no tamanho de seu Active Directory que você deseja importar. A conectividade de rede incorreta ou lenta entre o servidor do Azure AD Connect e controladores de domínio do Active Directory pode reduzir a velocidade de importação.

### <a name="azure-ad-factors"></a>Fatores do Azure Active Directory

O Microsoft Azure Active Directory usa a limitação para proteger o serviço de nuvem contra ataques de negação de serviço (DoS). Atualmente, o Microsoft Azure Active Directory tem um limite de limitação de 7.000 gravações por 5 minutos (84.000 por hora). Por exemplo, as seguintes operações podem ser limitadas:



- Exportação do Azure AD Connect para o Azure AD.
- Scripts do PowerShell ou aplicativos atualizando o Microsoft Azure Active Directory diretamente até mesmo em segundo plano, como associações de grupo dinâmico.
- Usuários atualizando seus próprios registros de identidade, como se registrar para MFA ou SSPR (redefinição de senha de autoatendimento).
- Operações dentro da interface gráfica do usuário.

Planejar para tarefas de implantação e manutenção, para certificar-se de que seu ciclo de sincronização do Azure AD Connect não é afetado por limites de limitação. Por exemplo, se você tiver uma grande onda de contratação em que você cria milhares de identidades de usuário, pode fazer as atualizações associações de grupo dinâmico, atribuições, de licenciamento e registros de redefinição de senha de autoatendimento. É melhor distribuir essas gravações por várias horas ou alguns dias.

### <a name="sql-database-factors"></a>Fatores do banco de dados SQL

O tamanho da sua topologia do Active Directory de origem influenciará o desempenho do banco de dados SQL. Siga os [requisitos de hardware](how-to-connect-install-prerequisites.md) para o banco de dados SQL e considere as seguintes recomendações:



- Organizações com mais de 100.000 usuários podem reduzir latências de rede pela colocação banco de dados SQL e o mecanismo de provisionamento no mesmo servidor.
- Devido ao disco alta de entrada e saída requisitos de (E/S) do processo de sincronização, use unidades de estado sólido (SSD) para o banco de dados SQL do mecanismo de provisionamento para obter melhores resultados, se não for possível, considere as configurações RAID 0 ou RAID 1.
- Não faça uma sincronização completa preemptiva; Isso causa uma rotatividade desnecessária e tempos de resposta mais lentos.

## <a name="conclusion"></a>Conclusão

Para otimizar o desempenho da sua implementação do Azure AD Connect, considere as seguintes recomendações:



- Use a [configuração de hardware recomendada](how-to-connect-install-prerequisites.md) com base em seu tamanho de implementação para o servidor do Azure AD Connect.
- Ao atualizar o Azure AD Connect em implantações em larga escala, considere o uso do [método de migração swing](./how-to-upgrade-previous-version.md#swing-migration), para garantir que você tenha o menor tempo de inatividade e maior confiabilidade. 
- Use o SSD para o banco de dados SQL para melhor desempenho de gravação.
- Filtre o escopo do Active Directory para incluir apenas objetos que precisam ser provisionados no Azure AD, usando o domínio, UO ou filtragem de atributo.
- Se você precisar alterar as regras de fluxo de atributo padrão, primeiro copie a regra, em seguida, altere a cópia e desabilite a regra original. Lembre-se de executar novamente uma sincronização completa.
- Planeje o tempo suficiente para a sincronização completa inicial, perfil de execução.
- Se esforce para concluir o ciclo de sincronização delta em 30 minutos. Se o perfil de sincronização delta não for concluído em 30 minutos, modifique a frequência de sincronização padrão para incluir um ciclo de sincronização delta completa.
- Monitor a integridade da [sincronização do Azure AD Connect](how-to-connect-health-agent-install.md) no Azure AD.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](whatis-hybrid-identity.md).
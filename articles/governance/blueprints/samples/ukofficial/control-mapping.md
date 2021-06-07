---
title: Controles da amostra de blueprint do UK OFFICIAL e do UK NHS
description: Mapeamento de controle das amostras de blueprint do UK OFFICIAL e do UK NHS. Cada controle é mapeado para uma ou mais definições do Azure Policy que auxiliam na avaliação.
ms.date: 02/05/2021
ms.topic: sample
ms.openlocfilehash: bf10bcf7064306094664c1ff560187ef77645d4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99627559"
---
# <a name="control-mapping-of-the-uk-official-and-uk-nhs-blueprint-samples"></a>Mapeamento de controle das amostras de blueprint do UK OFFICIAL e do UK NHS

O artigo a seguir fornece detalhes sobre como as amostras de blueprint do UK OFFICIAL e o UK NHS são mapeadas para os controles UK OFFICIAL e UK NHS. Para obter mais informações sobre os controles, confira [UK OFFICIAL](https://www.gov.uk/government/publications/government-security-classifications).

Os mapeamentos a seguir são para os controles **UK OFFICIAL** e **UK NHS**. Use a navegação no lado direito para ir diretamente para um mapeamento de controle específico. Muitos dos controles mapeados são implementados com uma iniciativa do [Azure Policy](../../../policy/overview.md). Para examinar a iniciativa completa, abra **Política** no portal do Azure e selecione a página **Definições**. Em seguida, localize e selecione os controles de **\[versão prévia\] UK OFFICIAL e UK NHS de Auditoria e implante Extensões de VM específicas para dar suporte aos requisitos de auditoria** da iniciativa de política interna.

> [!IMPORTANT]
> Cada controle abaixo está associado com uma ou mais definições do [Azure Policy](../../../policy/overview.md). Essas políticas podem ajudar você a [avaliar a conformidade](../../../policy/how-to/get-compliance-data.md) com o controle. No entanto, geralmente não há uma correspondência um para um ou completa entre um controle e uma ou mais políticas. Dessa forma, **Conformidade** no Azure Policy refere-se somente às próprias políticas. Não garante que você está totalmente em conformidade com todos os requisitos de um controle. Além disso, o padrão de conformidade inclui controles que não são abordados por nenhuma definição do Azure Policy no momento. Portanto, a conformidade no Azure Policy é somente uma exibição parcial do status de conformidade geral. As associações entre controles e definições do Azure Policy desta amostra de blueprint de conformidade podem ser alteradas ao longo do tempo. Para exibir o histórico de alterações, confira o [Histórico de Confirmações do GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ukofficial/control-mapping.md).

## <a name="1-data-in-transit-protection"></a>1 Proteção de dados em trânsito

O blueprint ajuda você a garantir que a transferência de informações com os serviços do Azure seja segura por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) para auditar conexões não seguras com contas de armazenamento e o Cache Redis.

- Apenas conexões seguras com o Cache Redis devem ser habilitadas
- A transferência segura para contas de armazenamento deve ser habilitada
- Mostrar os resultados da auditoria dos servidores Web do Windows que não estão usando protocolos de comunicação segura
- Aplicativo Web deve ser acessível somente por HTTPS
- O aplicativo de funções deve ser acessível apenas por HTTPS
- O aplicativo de API só deve estar acessível via HTTPS

## <a name="23-data-at-rest-protection"></a>2.3 Proteção de dados em repouso

Esse blueprint ajuda você a impor sua política sobre o uso de controles de criptografia por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) que impõem controles de criptografia específicos e auditam o uso de configurações de criptografia fraca. Entender em que local os recursos do Azure podem ter configurações de criptografia não ideais pode ajudá-lo a tomar ações corretivas para garantir que os recursos sejam configurados de acordo com a política de segurança de informações. Especificamente, as políticas atribuídas por esse blueprint exigem criptografia para contas do Data Lake Storage; exigem Transparent Data Encryption em bancos de dados SQL; auditam a criptografia ausente em contas de armazenamento, bancos de dados SQL, discos de máquina virtual e variáveis da conta de automação; auditam conexões não seguras para contas de armazenamento e o Cache Redis; auditam a criptografia fraca de senhas de máquina virtual; e auditam a comunicação não criptografada do Service Fabric.

- A criptografia de disco deve ser aplicada em máquinas virtuais
- As variáveis da conta de automação devem ser criptografadas
- A propriedade ClusterProtectionLevel dos clusters do Service Fabric deve ser definida como EncryptAndSign
- A Transparent Data Encryption em bancos de dados SQL deve ser habilitada
- Implantar a Transparent Data Encryption no BD SQL
- Exigir a criptografia em contas do Data Lake Storage
- Locais permitidos (foram embutidos em código para "SUL DO REINO UNIDO" e "OESTE DO REINO UNIDO")
- Localizações permitidas para grupos de recursos (foi embutido em código como "SUL DO REINO UNIDO" e "OESTE DO REINO UNIDO")

## <a name="52-vulnerability-management"></a>5.2 Gerenciamento de vulnerabilidades

Esse blueprint ajuda você a gerenciar as vulnerabilidades do sistema de informações por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) que monitoram proteção de ponto de extremidade ausente, atualizações ausentes do sistema, vulnerabilidades do sistema operacional, vulnerabilidades do SQL e vulnerabilidades da máquina virtual. Esses insights fornecem informações em tempo real sobre o estado de segurança dos recursos implantados e podem ajudá-lo a priorizar as ações de correção.

- monitora o Endpoint Protection ausente na Central de Segurança do Azure
- As atualizações do sistema devem ser instaladas em suas máquinas
- As atualizações do sistema nos conjuntos de dimensionamento de máquinas virtuais devem ser instaladas
- As vulnerabilidades da configuração de segurança nas máquinas devem ser corrigidas
- As vulnerabilidades nos bancos de dados SQL devem ser corrigidas
- As vulnerabilidades devem ser corrigidas por uma solução de Avaliação de Vulnerabilidades
- A avaliação da vulnerabilidade deve ser habilitada nos servidores SQL
- A avaliação de vulnerabilidades deverá ser habilitada na instância gerenciada de SQL
- As vulnerabilidades da configuração de segurança nos conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas
- A segurança de dados avançada deverá ser habilitada na instância gerenciada de SQL
- A Segurança de Dados Avançada deve ser habilitada nos servidores SQL

## <a name="53-protective-monitoring"></a>5.3 Monitoramento de proteção

Este blueprint ajuda a proteger os ativos do sistema de informações atribuindo definições do [Azure Policy](../../../policy/overview.md) que fornecem monitoramento de proteção de acesso irrestrito, atividade de lista de permissões e ameaças.

- As contas de armazenamento devem restringir o acesso da rede
- Os Controles de Aplicativos Adaptáveis para definir aplicativos seguros deverão estar habilitados em seus computadores
- Auditar máquinas virtuais sem a recuperação de desastre configurada
- A Proteção contra DDoS do Azure Standard deve estar habilitada
- Os tipos de Proteção Avançada contra Ameaças devem estar definidos como “Todos” nas configurações de Segurança de Dados Avançada da instância gerenciada de SQL
- Os tipos de Proteção Avançada contra Ameaças devem estar definidos como “Todos” nas configurações de Segurança de Dados Avançada do SQL Server
- Implantar a Detecção de Ameaças em servidores SQL
- Implantar a extensão padrão antimalware de IaaS da Microsoft para Windows Server

## <a name="9-secure-user-management"></a>9 Gerenciamento seguro de usuários 

O Azure RBAC (controle de acesso baseado em função) ajuda a gerenciar quem tem acesso aos recursos no Azure.
Usando o portal do Azure, você pode examinar quem tem acesso aos recursos do Azure e suas permissões. Esse blueprint ajuda você a restringir e controlar os direitos de acesso por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) para auditar contas externas com permissões de proprietário e/ou leitura/gravação e contas com permissões de proprietário, leitura e/ou gravação que não têm a autenticação multifator habilitada.

- O MFA deve ser habilitado em contas com permissões de proprietário em sua assinatura
- A MFA deve ser habilitada nas contas com permissões de gravação na sua assinatura
- O MFA deve ser habilitado em contas com permissões de leitura em sua assinatura
- As contas externas com permissões de proprietário devem ser removidas de sua assinatura
- As contas externas com permissões de gravação devem ser removidas de sua assinatura
- As contas externas com permissões de leitura devem ser removidas de sua assinatura

## <a name="10-identity-and-authentication"></a>10 Identidade e autenticação

Esse blueprint ajuda você a restringir e controlar os direitos de acesso por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) para auditar contas externas com permissões de proprietário e/ou leitura/gravação e contas com permissões de proprietário, leitura e/ou gravação que não têm a autenticação multifator habilitada.

- O MFA deve ser habilitado em contas com permissões de proprietário em sua assinatura
- A MFA deve ser habilitada nas contas com permissões de gravação na sua assinatura
- O MFA deve ser habilitado em contas com permissões de leitura em sua assinatura
- As contas externas com permissões de proprietário devem ser removidas de sua assinatura
- As contas externas com permissões de gravação devem ser removidas de sua assinatura
- As contas externas com permissões de leitura devem ser removidas de sua assinatura

Este blueprint atribui definições do Azure Policy para auditar o uso da autenticação do Azure Active Directory para servidores SQL e o Service Fabric. O uso da autenticação do Azure Active Directory permite o gerenciamento simplificado de permissões e o gerenciamento centralizado de identidades dos usuários de banco de dados e de outros serviços da Microsoft.

- Um administrador do Azure Active Directory deve ser provisionado para servidores SQL
- Os clusters do Service Fabric só devem usar o Azure Active Directory para autenticação de cliente

Esse blueprint também atribui definições do Azure Policy para auditar as contas que devem ser priorizadas para revisão, incluindo contas preteridas e contas externas. Quando necessário, as contas podem ser impedidas de entrar (ou removidas), o que remove imediatamente os direitos de acesso aos recursos do Azure.
Esse blueprint atribui duas definições do Azure Policy para auditar contas preteridas que devem ser consideradas para remoção.

- As contas preteridas devem ser removidas de sua assinatura
- As contas preteridas com permissões de proprietário devem ser removidas de sua assinatura
- As contas externas com permissões de proprietário devem ser removidas de sua assinatura
- As contas externas com permissões de gravação devem ser removidas de sua assinatura

Esse blueprint também atribui uma definição do Azure Policy que audita as permissões de arquivo de senha da VM do Linux para alertar se elas estão definidas incorretamente. Esse design permite que você tome uma ação corretiva para garantir que os autenticadores não sejam comprometidos.

- Mostrar os resultados da auditoria das VMs do Linux que não têm as permissões de arquivo de senha definidas como 0644

Esse blueprint ajuda você a impor senhas fortes por meio da atribuição de definições do Azure Policy que auditam VMs do Windows que não impõem a força mínima e outros requisitos de senha. O reconhecimento de VMs que estejam violando a política de força da senha ajuda você a tomar ações corretivas para garantir que as senhas de todas as contas de usuário da VM estejam em conformidade com a política.

- Mostrar os resultados da auditoria das VMs do Windows que não têm a configuração de complexidade de senha habilitada
- Mostrar os resultados da auditoria das VMs do Windows que não têm uma duração máxima da senha de 70 dias
- Mostrar os resultados da auditoria das VMs do Windows que não têm uma duração mínima da senha de 1 dia
- Mostrar os resultados da auditoria das VMs do Windows que não restringem o tamanho mínimo da senha a 14 caracteres
- Mostrar os resultados da auditoria das VMs do Windows que permitir reutilizar as 24 senhas anteriores

Esse blueprint também ajuda você a controlar o acesso aos recursos do Azure por meio da atribuição de definições do Azure Policy. Essas políticas auditam o uso de tipos de recursos e configurações que podem permitir um acesso mais permissivo aos recursos. Entender os recursos que estão violando essas políticas pode ajudar você a tomar ações corretivas para garantir que o acesso aos recursos do Azure seja restrito a usuários autorizados.

- Mostrar os resultados da auditoria das VMs do Linux que têm contas sem senhas
- Mostrar os resultados da auditoria das VMs do Linux que permitem conexões remotas em contas sem senhas
- As contas de armazenamento devem ser migradas para os novos recursos do Azure Resource Manager
- As máquinas virtuais devem ser migradas para os novos recursos do Azure Resource Manager
- Auditar VMs que não usam discos gerenciados

## <a name="11-external-interface-protection"></a>11 Proteção de interface externa

Além de usar mais de 25 políticas para o gerenciamento seguro de usuários apropriado, este blueprint ajuda a proteger interfaces de serviço contra acesso não autorizado atribuindo uma definição do [Azure Policy](../../../policy/overview.md) que monitora contas de armazenamento irrestritas.
As contas de armazenamento com acesso irrestrito podem permitir o acesso inadvertido a informações contidas no sistema de informações. Esse blueprint também atribui uma política que habilita controles de aplicativo adaptáveis em máquinas virtuais.

- As contas de armazenamento devem restringir o acesso da rede
- Os Controles de Aplicativos Adaptáveis para definir aplicativos seguros deverão estar habilitados em seus computadores
- O acesso pelo ponto de extremidade para a Internet deve ser restrito
- As recomendações da Proteção de Rede Adaptável devem ser aplicadas nas máquinas virtuais para a Internet
- A solução de proteção de ponto de extremidade deve ser instalada nos conjuntos de dimensionamento de máquinas virtuais
- As portas de gerenciamento de máquinas virtuais devem ser protegidas com o controle de acesso à rede Just-In-Time
- A depuração remota deve ser desativada para o aplicativos de funções
- A depuração remota deve ser desativada para aplicativos Web
- A depuração remota deve ser desligada para aplicativos de API

## <a name="13-audit-information-for-users"></a>13 Informações de auditoria para usuários

Este blueprint ajuda você a garantir que os eventos do sistema sejam registrados em log por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) que auditam as configurações do log de auditoria em recursos do Azure.
Uma política atribuída também audita se as máquinas virtuais não estão enviando logs para um workspace especificado do Log Analytics.

- A Segurança de Dados Avançada deve ser habilitada nos servidores SQL
- Configuração de diagnóstico de auditoria
- \[Versão Prévia\]: Implantar o Agente do Log Analytics para VMs do Linux
- \[Versão Prévia\]: Implantar o Agente do Log Analytics para VMs do Windows
- Implantar o observador de rede quando redes virtuais são criadas


## <a name="next-steps"></a>Próximas etapas

Agora que você examinou o mapeamento de controle dos blueprints UK OFFICIAL e UK NHS, leia os seguintes artigos para saber mais sobre a visão geral e como implantar esta amostra:

> [!div class="nextstepaction"]
> [Blueprints UK OFFICIAL e UK NHS – Visão geral](./index.md)
> [Blueprints UK OFFICIAL e UK NHS – Etapas de implantação](./deploy.md)

Outros artigos sobre blueprints e como usá-los:

- Saiba mais sobre o [ciclo de vida do blueprint](../../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../../how-to/update-existing-assignments.md).

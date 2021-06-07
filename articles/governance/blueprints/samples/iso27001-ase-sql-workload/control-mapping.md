---
title: Controles de amostra de blueprint da carga de trabalho do ASE/SQL da ISO 27001
description: Mapeamento de controle da amostra de blueprint de carga de trabalho do Ambiente do Serviço de Aplicativo/Banco de Dados SQL ISO 27001 para o Azure Policy e o Azure RBAC.
ms.date: 02/05/2021
ms.topic: sample
ms.openlocfilehash: f4bd340e67547ee22a558a63b56619171a1749c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99627460"
---
# <a name="control-mapping-of-the-iso-27001-asesql-workload-blueprint-sample"></a>Mapeamento de controle da amostra de blueprint de carga de trabalho do ASE/SQL ISO 27001

O artigo a seguir fornece detalhes sobre como a amostra de blueprint da carga de trabalho do ASE/SQL ISO 27001 do Azure Blueprints é mapeada para os controles ISO 27001. Para obter mais informações sobre os controles, confira [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html).

Os seguintes mapeamentos referem-se aos controles **ISO 27001:2013**. Use a navegação no lado direito para ir diretamente para um mapeamento de controle específico. Muitos dos controles mapeados são implementados com uma iniciativa do [Azure Policy](../../../policy/overview.md). Para examinar a iniciativa completa, abra **Política** no portal do Azure e selecione a página **Definições**. Em seguida, localize e selecione os controles de **\[versão prévia\] ISO 27001:2013 de Auditoria e implante Extensões de VM específicas para dar suporte aos requisitos de auditoria** da iniciativa de política interna.

> [!IMPORTANT]
> Cada controle abaixo está associado com uma ou mais definições do [Azure Policy](../../../policy/overview.md). Essas políticas podem ajudar você a [avaliar a conformidade](../../../policy/how-to/get-compliance-data.md) com o controle. No entanto, geralmente não há uma correspondência um para um ou completa entre um controle e uma ou mais políticas. Dessa forma, **Conformidade** no Azure Policy refere-se somente às próprias políticas. Não garante que você está totalmente em conformidade com todos os requisitos de um controle. Além disso, o padrão de conformidade inclui controles que não são abordados por nenhuma definição do Azure Policy no momento. Portanto, a conformidade no Azure Policy é somente uma exibição parcial do status de conformidade geral. As associações entre controles e definições do Azure Policy desta amostra de blueprint de conformidade podem ser alteradas ao longo do tempo. Para exibir o histórico de alterações, confira o [Histórico de Confirmações do GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/iso27001-ase-sql-workload/control-mapping.md).

## <a name="a612-segregation-of-duties"></a>A.6.1.2 Diferenciação de direitos

Ter apenas um proprietário de assinatura do Azure não permite a redundância administrativa. Por outro lado, ter muitos proprietários de assinatura do Azure pode aumentar o potencial de uma violação por meio de uma conta de proprietário comprometida. Esse blueprint ajuda você a manter um número apropriado de proprietários de assinatura do Azure por meio da atribuição de duas definições do [Azure Policy](../../../policy/overview.md) que audita o número de proprietários de assinaturas do Azure. O gerenciamento de permissões de proprietário de assinatura pode ajudá-lo a implementar uma diferenciação de direitos apropriada.

- Um máximo de três proprietários deve ser designado para sua assinatura
- Deve haver mais de um proprietário atribuído à sua assinatura

## <a name="a821-classification-of-information"></a>A.8.2.1 Classificação de informações

O [serviço de Avaliação de Vulnerabilidades de SQL](../../../../azure-sql/database/sql-vulnerability-assessment.md) do Azure pode ajudá-lo a descobrir dados confidenciais armazenados em seus bancos de dados e inclui recomendações para classificar os dados. Esta blueprint atribui uma definição do [Azure Policy](../../../policy/overview.md) para auditar se as vulnerabilidades identificadas durante o exame de Avaliação de Vulnerabilidades de SQL são corrigidas.

- As vulnerabilidades nos bancos de dados SQL devem ser corrigidas

## <a name="a912-access-to-networks-and-network-services"></a>A.9.1.2 Acesso a redes e serviços de rede

O Azure implementa o [RBAC do Azure (controle de acesso baseado em função do Azure)](../../../../role-based-access-control/overview.md) para gerenciar quem tem acesso aos recursos no Azure. Esse blueprint ajuda você a controlar o acesso aos recursos do Azure por meio da atribuição de sete definições do [Azure Policy](../../../policy/overview.md). Essas políticas auditam o uso de tipos de recursos e configurações que podem permitir um acesso mais permissivo aos recursos.
Entender os recursos que estão violando essas políticas pode ajudar você a tomar ações corretivas para garantir que o acesso aos recursos do Azure seja restrito a usuários autorizados.

- Mostrar os resultados da auditoria das VMs do Linux que têm contas sem senhas
- Mostrar os resultados da auditoria das VMs do Linux que permitem conexões remotas em contas sem senhas
- As contas de armazenamento devem ser migradas para os novos recursos do Azure Resource Manager
- As máquinas virtuais devem ser migradas para os novos recursos do Azure Resource Manager
- Auditar VMs que não usam discos gerenciados

## <a name="a923-management-of-privileged-access-rights"></a>A.9.2.3 Gerenciamento de direitos de acesso privilegiado

Esse blueprint ajuda você a restringir e controlar os direitos de acesso privilegiado por meio da atribuição de quatro definições do [Azure Policy](../../../policy/overview.md) para auditar contas externas com proprietário e/ou permissões de gravação e contas com proprietário e/ou permissões de gravação que não têm a autenticação multifator habilitada. O Azure RBAC (controle de acesso baseado em função) ajuda a gerenciar quem tem acesso aos recursos no Azure. Esse blueprint também atribui três definições do Azure Policy para auditar o uso da autenticação do Azure Active Directory para SQL Servers e o Service Fabric. O uso da autenticação do Azure Active Directory permite o gerenciamento simplificado de permissões e o gerenciamento centralizado de identidades dos usuários de banco de dados e de outros serviços da Microsoft. Esse blueprint também atribui uma definição do Azure Policy para auditar o uso de regras personalizadas do Azure RBAC. Entender o local em que as regras personalizadas do Azure RBAC são implementadas pode ajudar você a verificar a necessidade e a implementação apropriada, pois as regras personalizadas do Azure RBAC estão sujeitas a erros.

- O MFA deve ser habilitado em contas com permissões de proprietário em sua assinatura
- A MFA deve ser habilitada nas contas com permissões de gravação na sua assinatura
- As contas externas com permissões de proprietário devem ser removidas de sua assinatura
- As contas externas com permissões de gravação devem ser removidas de sua assinatura
- Um administrador do Azure Active Directory deve ser provisionado para servidores SQL
- Os clusters do Service Fabric só devem usar o Azure Active Directory para autenticação de cliente
- Auditar o uso de regras personalizadas do RBAC

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A.9.2.4 Gerenciamento de informações de autenticação secreta de usuários

Esse blueprint atribui três definições do [Azure Policy](../../../policy/overview.md) para auditar contas que não têm a autenticação multifator habilitada. A autenticação multifator ajuda a proteger contas, mesmo que uma única informação de autenticação seja comprometida. Monitorando as contas sem a autenticação multifator habilitada, você pode identificar as contas que têm mais probabilidade de serem comprometidas. Esse blueprint também atribui duas definições do Azure Policy que auditam as permissões de arquivo de senha da VM do Linux para alertar se elas forem definidas incorretamente. Essa configuração permite que você tome uma ação corretiva para garantir que os autenticadores não sejam comprometidos.

- O MFA deve ser habilitado em contas com permissões de proprietário em sua assinatura
- O MFA deve ser habilitado em contas com permissões de leitura em sua assinatura
- A MFA deve ser habilitada nas contas com permissões de gravação na sua assinatura
- Mostrar os resultados da auditoria das VMs do Linux que não têm as permissões de arquivo de senha definidas como 0644

## <a name="a925-review-of-user-access-rights"></a>A.9.2.5 Revisão dos direitos de acesso do usuário

O Azure implementa o [Azure RBAC (controle de acesso baseado em função do Azure)](../../../../role-based-access-control/overview.md) para ajudar você a gerenciar quem tem acesso aos recursos no Azure. Usando o portal do Azure, você pode examinar quem tem acesso aos recursos do Azure e suas permissões. Esse blueprint atribui quatro definições do [Azure Policy](../../../policy/overview.md) para auditar as contas que devem ser priorizadas para revisão, incluindo contas preteridas e contas externas com permissões elevadas.

- As contas preteridas devem ser removidas de sua assinatura
- As contas preteridas com permissões de proprietário devem ser removidas de sua assinatura
- As contas externas com permissões de proprietário devem ser removidas de sua assinatura
- As contas externas com permissões de gravação devem ser removidas de sua assinatura

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A.9.2.6 Remoção ou ajuste de direitos de acesso

O Azure implementa o [Azure RBAC (controle de acesso baseado em função do Azure)](../../../../role-based-access-control/overview.md) para ajudar você a gerenciar quem tem acesso aos recursos no Azure. Usando o [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) e o Azure RBAC, você pode atualizar as funções de usuário para que elas reflitam as mudanças organizacionais. Quando necessário, as contas podem ser impedidas de entrar (ou removidas), o que remove imediatamente os direitos de acesso aos recursos do Azure. Esse blueprint atribui duas definições do [Azure Policy](../../../policy/overview.md) para auditar contas preteridas que devem ser consideradas para remoção.

- As contas preteridas devem ser removidas de sua assinatura
- As contas preteridas com permissões de proprietário devem ser removidas de sua assinatura

## <a name="a942-secure-log-on-procedures"></a>A.9.4.2 Procedimentos de logon seguro

Esse blueprint atribui três definições do Azure Policy para auditar contas que não têm a autenticação multifator habilitada. A Autenticação Multifator do Azure AD fornece segurança adicional exigindo uma segunda forma de autenticação e fornece autenticação forte. Monitorando as contas sem a autenticação multifator habilitada, você pode identificar as contas que têm mais probabilidade de serem comprometidas.

- O MFA deve ser habilitado em contas com permissões de proprietário em sua assinatura
- O MFA deve ser habilitado em contas com permissões de leitura em sua assinatura
- A MFA deve ser habilitada nas contas com permissões de gravação na sua assinatura

## <a name="a943-password-management-system"></a>A.9.4.3 Sistema de gerenciamento de senhas

Esse blueprint ajuda você a impor senhas fortes por meio da atribuição de 10 definições do [Azure Policy](../../../policy/overview.md) que auditam VMs do Windows que não impõem a força mínima e outros requisitos de senha. O reconhecimento de VMs que estejam violando a política de força da senha ajuda você a tomar ações corretivas para garantir que as senhas de todas as contas de usuário da VM estejam em conformidade com a política.

- Mostrar os resultados da auditoria das VMs do Windows que não têm a configuração de complexidade de senha habilitada
- Mostrar os resultados da auditoria das VMs do Windows que não têm uma duração máxima da senha de 70 dias
- Mostrar os resultados da auditoria das VMs do Windows que não têm uma duração mínima da senha de 1 dia
- Mostrar os resultados da auditoria das VMs do Windows que não restringem o tamanho mínimo da senha a 14 caracteres
- Mostrar os resultados da auditoria das VMs do Windows que permitir reutilizar as 24 senhas anteriores

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>A.10.1.1 Política sobre o uso de controles de criptografia

Esse blueprint ajuda você a impor sua política sobre o uso de controles de criptografia por meio da atribuição de 13 definições do [Azure Policy](../../../policy/overview.md) que impõem controles de criptografia específicos e auditam o uso de configurações de criptografia fraca.
Entender em que local os recursos do Azure podem ter configurações de criptografia não ideais pode ajudá-lo a tomar ações corretivas para garantir que os recursos sejam configurados de acordo com a política de segurança de informações. Especificamente, as políticas atribuídas por esse blueprint exigem criptografia para contas de armazenamento de blobs e contas do Data Lake Storage; exigem Transparent Data Encryption em bancos de dados SQL; auditam a criptografia ausente em contas de armazenamento, bancos de dados SQL, discos de máquina virtual e variáveis da conta de automação; auditam conexões não seguras para contas de armazenamento, Aplicativos de funções, Aplicativo Web, Aplicativos de API e o Cache Redis; auditam a criptografia fraca de senhas de máquina virtual; e auditam a comunicação não criptografada do Service Fabric.

- O aplicativo de funções deve ser acessível apenas por HTTPS
- Aplicativo Web deve ser acessível somente por HTTPS
- O aplicativo de API só deve estar acessível via HTTPS
- Mostrar os resultados da auditoria das VMs do Windows que não armazenam senhas usando a criptografia reversível
- A criptografia de disco deve ser aplicada em máquinas virtuais
- As variáveis da conta de automação devem ser criptografadas
- Somente conexões seguras com o Cache do Azure para Redis devem ser habilitadas
- A transferência segura para contas de armazenamento deve ser habilitada
- A propriedade ClusterProtectionLevel dos clusters do Service Fabric deve ser definida como EncryptAndSign
- A Transparent Data Encryption em bancos de dados SQL deve ser habilitada

## <a name="a1241-event-logging"></a>A.12.4.1 Log de eventos

Esse blueprint ajuda você a garantir que os eventos do sistema sejam registrados em log por meio da atribuição de sete definições do [Azure Policy](../../../policy/overview.md) que auditam as configurações do log de auditoria nos recursos do Azure.
Os logs de diagnóstico fornecem insights sobre operações realizadas em recursos do Azure.

- Auditar a implantação do Dependency Agent – imagem de VM (sistema operacional) não listada
- Auditar a implantação do Dependency Agent em conjuntos de dimensionamento de máquinas virtuais – imagem de VM (sistema operacional) não listada
- [Versão prévia]: Auditar a implantação do Agente do Log Analytics – imagem de VM (sistema operacional) não listada
- Auditar a implantação do agente do Log Analytics em Conjuntos de Dimensionamento de Máquinas Virtuais – imagem de VM (sistema operacional) não listada
- Configuração de diagnóstico de auditoria
- A auditoria no SQL Server deve ser habilitada

## <a name="a1243-administrator-and-operator-logs"></a>A.12.4.3 Logs de administrador e de operador

Esse blueprint ajuda você a garantir que os eventos do sistema sejam registrados em log por meio da atribuição de sete definições do Azure Policy que auditam as configurações do log de auditoria nos recursos do Azure. Os logs de diagnóstico fornecem insights sobre operações realizadas em recursos do Azure.

- Auditar a implantação do Dependency Agent – imagem de VM (sistema operacional) não listada
- Auditar a implantação do Dependency Agent em conjuntos de dimensionamento de máquinas virtuais – imagem de VM (sistema operacional) não listada
- [Versão prévia]: Auditar a implantação do Agente do Log Analytics – imagem de VM (sistema operacional) não listada
- Auditar a implantação do agente do Log Analytics em Conjuntos de Dimensionamento de Máquinas Virtuais – imagem de VM (sistema operacional) não listada
- Configuração de diagnóstico de auditoria
- A auditoria no SQL Server deve ser habilitada

## <a name="a1244-clock-synchronization"></a>A.12.4.4 Sincronização do relógio

Esse blueprint ajuda você a garantir que os eventos do sistema sejam registrados em log por meio da atribuição de sete definições do Azure Policy que auditam as configurações do log de auditoria nos recursos do Azure. Os logs do Azure dependem de relógios internos sincronizados para criar um registro de eventos relacionado à hora em todos os recursos.

- Auditar a implantação do Dependency Agent – imagem de VM (sistema operacional) não listada
- Auditar a implantação do Dependency Agent em conjuntos de dimensionamento de máquinas virtuais – imagem de VM (sistema operacional) não listada
- [Versão prévia]: Auditar a implantação do Agente do Log Analytics – imagem de VM (sistema operacional) não listada
- Auditar a implantação do agente do Log Analytics em Conjuntos de Dimensionamento de Máquinas Virtuais – imagem de VM (sistema operacional) não listada
- Configuração de diagnóstico de auditoria
- A auditoria no SQL Server deve ser habilitada

## <a name="a1251-installation-of-software-on-operational-systems"></a>A.12.5.1 Instalação do software de sistemas operacionais

Os controles de aplicativos adaptáveis são a solução da Central de Segurança do Azure que ajuda você a controlar quais aplicativos podem ser executados em suas VMs localizadas no Azure. Esse blueprint atribui uma definição do Azure Policy que monitora as alterações feitas no conjunto de aplicativos permitidos. Esse recurso ajuda você a controlar a instalação de software e aplicativos em VMs do Azure.

- Os controles de aplicativos adaptáveis para definir aplicativos seguros devem ser habilitados nos computadores

## <a name="a1261-management-of-technical-vulnerabilities"></a>A.12.6.1 Gerenciamento de vulnerabilidades técnicas

Esse blueprint ajuda você a gerenciar as vulnerabilidades do sistema de informações por meio da atribuição de cinco definições do [Azure Policy](../../../policy/overview.md) que monitoram atualizações ausentes do sistema, vulnerabilidades do sistema operacional, vulnerabilidades do SQL e vulnerabilidades da máquina virtual na Central de Segurança do Azure. A Central de Segurança do Azure fornece funcionalidades de relatórios que permitem ter insights em tempo real sobre o estado de segurança de recursos implantados do Azure.

- monitora o Endpoint Protection ausente na Central de Segurança do Azure
- As atualizações do sistema devem ser instaladas em suas máquinas
- As vulnerabilidades da configuração de segurança nas máquinas devem ser corrigidas
- As vulnerabilidades nos bancos de dados SQL devem ser corrigidas
- As vulnerabilidades devem ser corrigidas por uma solução de Avaliação de Vulnerabilidades

## <a name="a1262-restrictions-on-software-installation"></a>A.12.6.2 Restrições de instalação de software

Os controles de aplicativos adaptáveis são a solução da Central de Segurança do Azure que ajuda você a controlar quais aplicativos podem ser executados em suas VMs localizadas no Azure. Esse blueprint atribui uma definição do Azure Policy que monitora as alterações feitas no conjunto de aplicativos permitidos. As restrições de instalação de software podem ajudar você a reduzir a probabilidade de introdução de vulnerabilidades de software.

- Os controles de aplicativos adaptáveis para definir aplicativos seguros devem ser habilitados nos computadores

## <a name="a1311-network-controls"></a>A.13.1.1 Controles de rede

Esse blueprint ajuda você a gerenciar e controlar redes por meio da atribuição de uma definição do [Azure Policy](../../../policy/overview.md) que monitora os grupos de segurança de rede com regras permissivas. Regras que são muito permissivas podem permitir o acesso não intencional à rede e devem ser examinadas. Esse blueprint também atribui três definições do Azure Policy que monitoram pontos de extremidade, contas de armazenamento e aplicativos desprotegidos. Os pontos de extremidade e os aplicativos que não estão protegidos por um firewall e as contas de armazenamento com acesso irrestrito podem permitir o acesso não intencional às informações contidas no sistema de informações.

- O acesso pelo ponto de extremidade para a Internet deve ser restrito
- As contas de armazenamento devem restringir o acesso da rede

## <a name="a1321-information-transfer-policies-and-procedures"></a>A.13.2.1 Políticas e procedimentos de transferência de informações

O blueprint ajuda você a garantir que a transferência de informações com os serviços do Azure seja segura por meio da atribuição de duas definições do [Azure Policy](../../../policy/overview.md) para auditar conexões não seguras com contas de armazenamento e o Cache Redis.

- Somente conexões seguras com o Cache do Azure para Redis devem ser habilitadas
- A transferência segura para contas de armazenamento deve ser habilitada

## <a name="next-steps"></a>Próximas etapas

Agora que você examinou o mapeamento de controle da amostra de blueprint da carga de trabalho do Ambiente do Serviço de Aplicativo/Banco de Dados SQL ISO 27001, leia os seguintes artigos para saber mais sobre a arquitetura e como implantar essa amostra:

> [!div class="nextstepaction"]
> [Blueprint da carga de trabalho do Ambiente do Serviço de Aplicativo/Banco de Dados SQL ISO 27001 – Visão geral](./index.md)
> [Blueprint da carga de trabalho do Ambiente do Serviço de Aplicativo/Banco de Dados SQL ISO 27001 – etapas de implantação](./deploy.md)

Outros artigos sobre blueprints e como usá-los:

- Saiba mais sobre o [ciclo de vida do blueprint](../../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../../how-to/update-existing-assignments.md).

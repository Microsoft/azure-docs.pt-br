---
title: Controles de amostra de blueprint do PCI-DSS v3.2.1
description: Mapeamento de controle da amostra de blueprint do Padrão de Segurança de Dados do Setor de Cartões de Pagamento v3.2.1 para Azure Policy e Azure RBAC.
ms.date: 04/02/2021
ms.topic: sample
ms.openlocfilehash: 028981e7c6fc6e3886781d33079dcdfa0aebcc39
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106386067"
---
# <a name="control-mapping-of-the-pci-dss-v321-blueprint-sample"></a>Mapeamento de controle da amostra de blueprint do PCI-DSS v3.2.1

O artigo a seguir apresenta detalhes sobre como a amostra do Azure Blueprints PCI-DSS v3.2.1 é mapeada para os controles do PCI-DSS v3.2.1. Para obter mais informações sobre os controles, confira [PCI-DSS v3.2.1](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf).

Os seguintes mapeamentos referem-se aos controles de **PCI-DSS v3.2.1:2018**. Use a navegação no lado direito para ir diretamente para um mapeamento de controle específico. Muitos dos controles mapeados são implementados com uma iniciativa do [Azure Policy](../../../policy/overview.md). Para examinar a iniciativa completa, abra **Política** no portal do Azure e selecione a página **Definições**. Em seguida, localize e selecione a iniciativa de política interna **PCI v3.2.1:2018**.

> [!IMPORTANT]
> Cada controle abaixo está associado com uma ou mais definições do [Azure Policy](../../../policy/overview.md). Essas políticas podem ajudar você a [avaliar a conformidade](../../../policy/how-to/get-compliance-data.md) com o controle. No entanto, geralmente não há uma correspondência um para um ou completa entre um controle e uma ou mais políticas. Dessa forma, **Conformidade** no Azure Policy refere-se somente às próprias políticas. Não garante que você está totalmente em conformidade com todos os requisitos de um controle. Além disso, o padrão de conformidade inclui controles que não são abordados por nenhuma definição do Azure Policy no momento. Portanto, a conformidade no Azure Policy é somente uma exibição parcial do status de conformidade geral. As associações entre controles e definições do Azure Policy desta amostra de blueprint de conformidade podem ser alteradas ao longo do tempo. Para exibir o histórico de alterações, confira o [Histórico de Confirmações do GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/pci-dss-3.2.1/control-mapping.md).

## <a name="132-and-134-boundary-protection"></a>1.3.2 e 1.3.4 Proteção de limite

Esse blueprint ajuda você a gerenciar e controlar redes por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) que monitoram os grupos de segurança de rede com regras permissivas. Regras que são muito permissivas podem permitir o acesso não intencional à rede e devem ser examinadas. Esse blueprint atribui uma definição do Azure Policy que monitora pontos de extremidade, contas de armazenamento e aplicativos desprotegidos. Os pontos de extremidade e os aplicativos que não estão protegidos por um firewall e as contas de armazenamento com acesso irrestrito podem permitir o acesso não intencional às informações contidas no sistema de informações.

- Auditar o acesso irrestrito à rede para contas de armazenamento
- O acesso pelo ponto de extremidade para a Internet deve ser restrito

## <a name="34a-41-41g-41h-and-653-cryptographic-protection"></a>3.4.a, 4.1, 4.1.g, 4.1.h e 6.5.3 Proteção criptográfica

Esse blueprint ajuda você a impor sua política usando controles de criptografia por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) que impõem controles de criptografia específicos e auditam o uso de configurações de criptografia fraca. Entender em que local os recursos do Azure podem ter configurações de criptografia não ideais pode ajudá-lo a tomar ações corretivas para garantir que os recursos sejam configurados de acordo com a política de segurança de informações. Especificamente, as políticas atribuídas por este blueprint exigem Transparent Data Encryption em bancos de dados SQL; auditar criptografia ausente em contas de armazenamento e variáveis de conta de automação. Também há políticas que abordam a auditoria de conexões inseguras para contas de armazenamento, aplicativos de funções, WebApp, aplicativos de API e Cache Redis e auditoria de comunicação não criptografada do Service Fabric.

- O aplicativo de funções deve ser acessível apenas por HTTPS
- Aplicativo Web deve ser acessível somente por HTTPS
- O aplicativo de API só deve estar acessível via HTTPS
- A Transparent Data Encryption em bancos de dados SQL deve ser habilitada
- A criptografia de disco deve ser aplicada em máquinas virtuais
- As variáveis da conta de automação devem ser criptografadas
- Apenas conexões seguras com o Cache Redis devem ser habilitadas
- A transferência segura para contas de armazenamento deve ser habilitada
- A propriedade ClusterProtectionLevel dos clusters do Service Fabric deve ser definida como EncryptAndSign
- A Transparent Data Encryption em bancos de dados SQL deve ser habilitada
- Implantar a Transparent Data Encryption no BD SQL

## <a name="51-62-66-and-1121-vulnerability-scanning-and-system-updates"></a>5.1, 6.2, 6.6 e 11.2.1 Verificação de vulnerabilidade e atualizações do sistema

Esse blueprint ajuda você a gerenciar as vulnerabilidades do sistema de informações por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) que monitoram atualizações ausentes do sistema, vulnerabilidades do sistema operacional, vulnerabilidades do SQL e vulnerabilidades da máquina virtual na Central de Segurança do Azure. A Central de Segurança do Azure fornece funcionalidades de relatórios que permitem ter insights em tempo real sobre o estado de segurança de recursos implantados do Azure.

- monitora o Endpoint Protection ausente na Central de Segurança do Azure
- Implantar a extensão padrão antimalware de IaaS da Microsoft para Windows Server
- Implantar a Detecção de Ameaças em Servidores SQL
- As atualizações do sistema devem ser instaladas em suas máquinas
- As vulnerabilidades da configuração de segurança nas máquinas devem ser corrigidas
- As vulnerabilidades nos bancos de dados SQL devem ser corrigidas
- As vulnerabilidades devem ser corrigidas por uma solução de Avaliação de Vulnerabilidades

## <a name="711-712-and-713-separation-of-duties"></a>7.1.1. 7.1.2 e 7.1.3 Separação de deveres

Ter apenas um proprietário de assinatura do Azure não permite a redundância administrativa. Por outro lado, ter muitos proprietários de assinatura do Azure pode aumentar o potencial de uma violação por meio de uma conta de proprietário comprometida. Este blueprint ajuda você a manter um número apropriado de proprietários de assinatura do Azure por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) que auditam o número de proprietários de assinaturas do Azure. O gerenciamento de permissões de proprietário de assinatura pode ajudá-lo a implementar uma diferenciação de direitos apropriada.

- Deve haver mais de um proprietário atribuído à sua assinatura
- Um máximo de três proprietários deve ser designado para sua assinatura 

## <a name="32-721-831a-and-831b-management-of-privileged-access-rights"></a>3.2, 7.2.1, 8.3.1.a e 8.3.1.b Gerenciamento de direitos de acesso privilegiado

Esse blueprint ajuda você a restringir e controlar os direitos de acesso privilegiado por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) para auditar contas externas com permissões de proprietário, gravação e/ou leitura e contas de funcionário com permissões de proprietário e/ou gravação que não têm a autenticação multifator habilitada. O Azure RBAC (controle de acesso baseado em função) ajuda a gerenciar quem tem acesso aos recursos no Azure. Entender o local em que as regras personalizadas do Azure RBAC são implementadas pode ajudar você a verificar a necessidade e a implementação apropriada, pois as regras personalizadas do Azure RBAC estão sujeitas a erros. Este blueprint também atribui definições do [Azure Policy](../../../policy/overview.md) para auditar o uso da autenticação do Azure Active Directory para servidores SQL. O uso da autenticação do Azure Active Directory simplifica o gerenciamento de permissões e centraliza o gerenciamento de identidades dos usuários de banco de dados e de outros serviços  
da Microsoft.
 
- As contas externas com permissões de proprietário devem ser removidas de sua assinatura
- As contas externas com permissões de gravação devem ser removidas de sua assinatura
- As contas externas com permissões de leitura devem ser removidas de sua assinatura
- O MFA deve ser habilitado em contas com permissões de proprietário em sua assinatura
- A MFA deve ser habilitada nas contas com permissões de gravação na sua assinatura
- O MFA deve ser habilitado em contas com permissões de leitura em sua assinatura
- Um administrador do Azure Active Directory deve ser provisionado para servidores SQL
- Auditar o uso de regras personalizadas do RBAC

## <a name="812-and-815-least-privilege-and-review-of-user-access-rights"></a>8.1.2 e 8.1.5 Privilégios mínimos e análise dos direitos de acesso do usuário

O Azure RBAC (controle de acesso baseado em função) ajuda a gerenciar quem tem acesso aos recursos no Azure. Usando o portal do Azure, você pode examinar quem tem acesso aos recursos do Azure e suas permissões. Esse blueprint atribui definições do [Azure Policy](../../../policy/overview.md) para auditar as contas que devem ser priorizadas para revisão, incluindo contas preteridas e contas externas com permissões elevadas.

- As contas preteridas devem ser removidas de sua assinatura
- As contas preteridas com permissões de proprietário devem ser removidas de sua assinatura
- As contas externas com permissões de proprietário devem ser removidas de sua assinatura
- As contas externas com permissões de gravação devem ser removidas de sua assinatura
- As contas externas com permissões de leitura devem ser removidas de sua assinatura

## <a name="813-removal-or-adjustment-of-access-rights"></a>8.1.3 Remoção ou ajuste de direitos de acesso

O Azure RBAC (controle de acesso baseado em função) ajuda a gerenciar quem tem acesso aos recursos no Azure. Usando o Azure Active Directory e o Azure RBAC, você pode atualizar as funções de usuário para que elas reflitam as mudanças organizacionais. Quando necessário, as contas podem ser impedidas de entrar (ou removidas), o que remove imediatamente os direitos de acesso aos recursos do Azure. Esse blueprint atribui definições do [Azure Policy](../../../policy/overview.md) para auditar contas preteridas que devem ser consideradas para remoção.

- As contas preteridas devem ser removidas de sua assinatura
- As contas preteridas com permissões de proprietário devem ser removidas de sua assinatura

## <a name="823ab-824ab-and-825-password-based-authentication"></a>8.2.3.a,b, 8.2.4.a,b e 8.2.5 Autenticação baseada em senha

Esse blueprint ajuda você a impor senhas fortes por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) que auditam VMs do Windows que não impõem a força mínima e outros requisitos de senha. O reconhecimento de VMs que estejam violando a política de força da senha ajuda você a tomar ações corretivas para garantir que as senhas de todas as contas de usuário da VM estejam em conformidade com a política.

- \[Versão Prévia\]: Auditar as VMs do Windows que não têm uma duração máxima da senha de 70 dias
- \[Versão Prévia\]: Implantar requisitos para auditar as VMs do Windows que não têm uma duração máxima da senha de 70 dias
- \[Versão Prévia\]: Auditar as VMs do Windows que não restringem o tamanho mínimo da senha a 14 caracteres
- \[Versão Prévia\]: Implantar requisitos para auditar as VMs do Windows que não restringem o tamanho mínimo da senha a 14 caracteres
- \[Versão Prévia\]: Auditar as VMs do Windows que podem reutilizar as 24 senhas anteriores
- \[Versão Prévia\]: Implantar requisitos para auditar as VMs do Windows que podem reutilizar as últimas 24 senhas

## <a name="103-and-1054-audit-generation"></a>10.3 e 10.5.4 Geração de auditoria

Este blueprint ajuda você a garantir que os eventos do sistema sejam registrados em log por meio da atribuição de definições do [Azure Policy](../../../policy/overview.md) que auditam as configurações do log de auditoria em recursos do Azure.
Os logs de diagnóstico fornecem insights sobre operações realizadas em recursos do Azure. Os logs do Azure dependem de relógios internos sincronizados para criar um registro de eventos relacionado à hora em todos os recursos.

- A auditoria deve ser habilitada nas configurações de segurança de dados avançada no SQL Server
- Configuração de diagnóstico de auditoria
- Auditar configurações de Auditoria de nível do servidor SQL
- Implantar Auditoria em servidores SQL
- As contas de armazenamento devem ser migradas para os novos recursos do Azure Resource Manager
- As máquinas virtuais devem ser migradas para os novos recursos do Azure Resource Manager

## <a name="1236-and-1237-information-security"></a>12.3.6 e 12.3.7 Segurança de informações

Este blueprint ajuda você a gerenciar e controlar sua rede atribuindo definições do [Azure Policy](../../../policy/overview.md) que auditam os locais de rede aceitáveis e os produtos da empresa aprovados permitidos para o ambiente. Cada empresa pode personalizar isso por meio dos parâmetros de política em cada uma dessas políticas.

- Locais permitidos
- Localizações permitidas para grupos de recursos

## <a name="next-steps"></a>Próximas etapas

Agora que você examinou o mapeamento de controle do blueprint PCI-DSS v3.2.1, leia os seguintes artigos para saber mais sobre a visão geral e como implantar esta amostra:

> [!div class="nextstepaction"]
> [Blueprint do PCI-DSS v3.2.1 – Visão geral](./index.md)
> [Blueprint do PCI-DSS v3.2.1 – Etapas de implantação](./deploy.md)

Outros artigos sobre blueprints e como usá-los:

- Saiba mais sobre o [ciclo de vida do blueprint](../../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../../how-to/update-existing-assignments.md).
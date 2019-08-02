---
title: Exemplo-PCI-DSS v 3.2.1 Blueprint-mapeamento de controle
description: Controle o mapeamento do exemplo de planejamento de plano de segurança de dados do setor de cartão de pagamento Standard v 3.2.1 para Azure Policy e RBAC.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 1e85cb5c06f36e0f8c105ece2c012cfe7cb77bf4
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226020"
---
# <a name="control-mapping-of-the-pci-dss-v321-blueprint-sample"></a>Mapeamento de controle do exemplo de especificação técnica PCI-DSS v 3.2.1

O artigo a seguir fornece detalhes sobre como a amostra do plano gráfico PCI-DSS v 3.2.1 do Azure é mapeada para os controles PCI-DSS v 3.2.1. Para obter mais informações sobre os controles, consulte [PCI-DSS v 3.2.1](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf).

Os seguintes mapeamentos são para os controles **PCI-DSS v 3.2.1:2018** . Use a navegação no lado direito para ir diretamente para um mapeamento de controle específico. Muitos dos controles mapeados são implementados com uma iniciativa do [Azure Policy](../../../policy/overview.md). Para examinar a iniciativa completa, abra **Política** no portal do Azure e selecione a página **Definições**. Em seguida, localize e selecione  **\[a\] auditoria de visualização PCI v 3.2.1: controles de 2018 e implante extensões de VM específicas para dar suporte à** iniciativa de política interna de requisitos de auditoria.

## <a name="132-and-134-boundary-protection"></a>Proteção de limite de 1.3.2 e 1.3.4

Este projeto ajuda você a gerenciar e controlar redes atribuindo [Azure Policy](../../../policy/overview.md) definições que monitoram grupos de segurança de rede com regras permissivas. Regras que são muito permissivas podem permitir o acesso não intencional à rede e devem ser examinadas. Este projeto atribui uma Azure Policy definições que monitoram pontos de extremidade, aplicativos e contas de armazenamento desprotegidos. Os pontos de extremidade e os aplicativos que não estão protegidos por um firewall e as contas de armazenamento com acesso irrestrito podem permitir o acesso não intencional às informações contidas no sistema de informações.

- Auditar o acesso irrestrito à rede para contas de armazenamento
- O acesso pelo ponto de extremidade voltado para a Internet deve ser restrito

## <a name="34a-41-41g-41h-and-653-cryptographic-protection"></a>3.4. a, 4,1, 4.1. g, 4.1. h e proteção criptográfica 6.5.3

Este projeto ajuda a reforçar a política com o uso de controles de cryptograph atribuindo definições de [Azure Policy](../../../policy/overview.md) que impõem controles cryptograph específicos e auditam o uso de configurações de criptografia fracas. Entender em que local os recursos do Azure podem ter configurações de criptografia não ideais pode ajudá-lo a tomar ações corretivas para garantir que os recursos sejam configurados de acordo com a política de segurança de informações. Especificamente, as políticas atribuídas por este projeto exigem Transparent Data Encryption em bancos de dados SQL; auditar criptografia ausente em contas de armazenamento e variáveis de conta de automação. Também há políticas que abordam conexões inseguras de auditoria para contas de armazenamento, aplicativos de funções, WebApp, aplicativos de API e cache Redis e auditoria de comunicação não criptografada Service Fabric.

- O aplicativo de funções deve ser acessível apenas por HTTPS
- Aplicativo Web deve ser acessível somente por HTTPS
- O aplicativo de API só deve ser acessível via HTTPS
- monitora o banco de dados SQL não criptografado na Central de Segurança do Azure
- A criptografia de disco deve ser aplicada em máquinas virtuais
- As variáveis da conta de automação devem ser criptografadas
- Somente conexões seguras para o cache Redis devem ser habilitadas
- A transferência segura para contas de armazenamento deve ser habilitada
- Service Fabric clusters devem ter a propriedade ClusterProtectionLevel definida como EncryptAndSign
- Transparent Data Encryption em bancos de dados SQL devem ser habilitadas
- Implantar a Transparent Data Encryption no BD SQL

## <a name="51-62-66-and-1121-vulnerability-scanning-and-system-updates"></a>5,1, 6,2, 6,6 e 11.2.1 de verificação de vulnerabilidade e atualizações do sistema

Este projeto ajuda a gerenciar as vulnerabilidades do sistema de informações atribuindo [Azure Policy](../../../policy/overview.md) definições que monitoram atualizações de sistema ausentes, vulnerabilidades do sistema operacional, vulnerabilidades de SQL e vulnerabilidades de máquina virtual no Azure Central de segurança. A Central de Segurança do Azure fornece funcionalidades de relatórios que permitem ter insights em tempo real sobre o estado de segurança de recursos implantados do Azure.

- monitora o Endpoint Protection ausente na Central de Segurança do Azure
- Implantar a extensão padrão antimalware de IaaS da Microsoft para Windows Server
- Implantar a detecção de ameaças em servidores SQL
- As atualizações do sistema devem ser instaladas em seus computadores
- Vulnerabilidades na configuração de segurança em seus computadores devem ser corrigidas
- Vulnerabilidades em seus bancos de dados SQL devem ser corrigidas
- Vulnerabilidades devem ser corrigidas por uma solução de avaliação de vulnerabilidade

## <a name="711-712-and-713-separation-of-duties"></a>7.1.1. 7.1.2 e 7.1.3 separação de tarefas

Ter apenas um proprietário de assinatura do Azure não permite a redundância administrativa. Por outro lado, ter muitos proprietários de assinatura do Azure pode aumentar o potencial de uma violação por meio de uma conta de proprietário comprometida. Este projeto ajuda a manter um número apropriado de proprietários de assinatura do Azure atribuindo definições de [Azure Policy](../../../policy/overview.md) que auditam o número de proprietários para assinaturas do Azure. O gerenciamento de permissões de proprietário de assinatura pode ajudá-lo a implementar uma diferenciação de direitos apropriada.

- Deve haver mais de um proprietário atribuído à sua assinatura
- Um máximo de 3 proprietários deve ser designado para sua assinatura 

## <a name="32-721-831a-and-831b-management-of-privileged-access-rights"></a>3,2, 7.2.1, 8.3.1. a e 8.3.1. b gerenciamento de direitos de acesso privilegiado

Este projeto ajuda a restringir e controlar direitos de acesso privilegiado atribuindo definições de [Azure Policy](../../../policy/overview.md) para auditar contas externas com permissões de proprietário, gravação e/ou leitura e contas de funcionário com permissões de proprietário e/ou gravação que não têm autenticação multifator habilitada. O Azure implementa o RBAC (controle de acesso baseado em função) para gerenciar quem tem acesso aos recursos no Azure. Entender o local em que as regras RBAC personalizadas são implementadas pode ajudar você a verificar a necessidade e a implementação apropriada, pois as regras RBAC personalizadas estão sujeitas a erros. Esse projeto também atribui definições de [Azure Policy](../../../policy/overview.md) para auditar o uso da autenticação Azure Active Directory para servidores SQL. Usar Azure Active Directory autenticação simplifica o gerenciamento de permissões e centraliza o gerenciamento de identidades de usuários de banco de dados e outras  
serviços.
 
- Contas externas com permissões de proprietário devem ser removidas da sua assinatura
- As contas externas com permissões de gravação do proprietário devem ser removidas da sua assinatura
- Contas externas com permissões de leitura devem ser removidas da sua assinatura
- A MFA deve ser habilitada em contas com permissões de proprietário em sua assinatura
- A MFA deve ser habilitada para contas com permissões de gravação em sua assinatura
- A MFA deve ser habilitada em contas com permissões de leitura em sua assinatura
- Um administrador de Azure Active Directory deve ser provisionado para servidores SQL
- Auditar o uso de regras personalizadas do RBAC

## <a name="812-and-815-least-privilege-and-review-of-user-access-rights"></a>8.1.2 e 8.1.5 privilégios mínimos e revisão dos direitos de acesso do usuário

O Azure implementa o RBAC (controle de acesso baseado em função) para ajudá-lo a gerenciar quem tem acesso aos recursos no Azure. Usando o portal do Azure, você pode examinar quem tem acesso aos recursos do Azure e suas permissões. Este projeto atribui definições de [Azure Policy](../../../policy/overview.md) para auditar contas que devem ser priorizadas para revisão, incluindo contas depreciadas e contas externas com permissões elevadas.

- As contas preteridas devem ser removidas da sua assinatura
- Contas preteridas com permissões de proprietário devem ser removidas da sua assinatura
- Contas externas com permissões de proprietário devem ser removidas da sua assinatura
- As contas externas com permissões de gravação do proprietário devem ser removidas da sua assinatura
- Contas externas com permissões de leitura devem ser removidas da sua assinatura

## <a name="813-removal-or-adjustment-of-access-rights"></a>remoção ou ajuste de 8.1.3 de direitos de acesso

O Azure implementa o RBAC (controle de acesso baseado em função) para ajudá-lo a gerenciar quem tem acesso aos recursos no Azure. Usando Azure Active Directory e RBAC, você pode atualizar funções de usuário para refletir alterações organizacionais. Quando necessário, as contas podem ser impedidas de entrar (ou removidas), o que remove imediatamente os direitos de acesso aos recursos do Azure. Este projeto atribui definições de [Azure Policy](../../../policy/overview.md) para auditar a conta depreciada que deve ser considerada para remoção.

- As contas preteridas devem ser removidas da sua assinatura
- Contas preteridas com permissões de proprietário devem ser removidas da sua assinatura

## <a name="823ab-824ab-and-825-password-based-authentication"></a>8.2.3. a, b, 8.2.4. a, b e 8.2.5 autenticação baseada em senha

Este projeto ajuda a impor senhas fortes atribuindo definições de [Azure Policy](../../../policy/overview.md) que auditam as VMs do Windows que não impõem a força mínima e outros requisitos de senha. O reconhecimento de VMs que estejam violando a política de força da senha ajuda você a tomar ações corretivas para garantir que as senhas de todas as contas de usuário da VM estejam em conformidade com a política.

- \[Versão Prévia\]: Auditar VMs do Windows que não têm uma duração máxima de senha de 70 dias
- \[Versão Prévia\]: Implantar requisitos para auditar VMs do Windows que não têm uma duração máxima de senha de 70 dias
- \[Versão Prévia\]: Auditar VMs do Windows que não restringem o comprimento mínimo da senha para 14 caracteres
- \[Versão Prévia\]: Implantar requisitos para auditar VMs do Windows que não restringem o comprimento mínimo da senha para 14 caracteres
- \[Versão Prévia\]: Auditar VMs do Windows que permitem reutilização das 24 senhas anteriores
- \[Versão Prévia\]: Implantar requisitos para auditar VMs do Windows que permitem o reuso das 24 senhas anteriores

## <a name="103-and-1054-audit-generation"></a>Geração de auditoria 10,3 e 10.5.4

Este projeto ajuda a garantir que os eventos do sistema sejam registrados por meio da atribuição de definições de [Azure Policy](../../../policy/overview.md) que auditam as configurações de log nos recursos do Azure.
Os logs de diagnóstico fornecem insights sobre operações realizadas em recursos do Azure. Os logs do Azure dependem de relógios internos sincronizados para criar um registro de eventos relacionado à hora em todos os recursos.

- Monitorar servidores SQL não auditados na central de segurança do Azure
- Configuração de diagnóstico de auditoria
- Auditar configurações de Auditoria de nível do servidor SQL
- Implantar Auditoria em servidores SQL
- As contas de armazenamento devem ser migradas para novos recursos de Azure Resource Manager
- As máquinas virtuais devem ser migradas para novos recursos de Azure Resource Manager

## <a name="1236-and-1237-information-security"></a>Segurança de informações de 12.3.6 e 12.3.7

Este projeto ajuda você a gerenciar e controlar sua rede atribuindo definições de [Azure Policy](../../../policy/overview.md) que auditam os locais de rede aceitáveis e os produtos da empresa aprovados permitidos para o ambiente. Elas são personalizáveis por cada empresa por meio dos parâmetros de política em cada uma dessas políticas.

- Locais permitidos
- Locais permitidos para grupos de recursos

## <a name="next-steps"></a>Próximas etapas

Agora que você analisou o mapeamento de controle do plano gráfico PCI-DSS v 3.2.1, visite os seguintes artigos para saber mais sobre a visão geral e como implantar este exemplo:

> [!div class="nextstepaction"]
> [PCI-DSS v 3.2.1 Blueprint-visão geral](./index.md)
> [PCI-DSS v 3.2.1 Blueprint-etapas de implantação](./deploy.md)

## <a name="addition-articles-about-blueprints-and-how-to-use-them"></a>Outros artigos sobre blueprints e como usá-los:

- Saiba mais sobre o [ciclo de vida do blueprint](../../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../../how-to/update-existing-assignments.md).

---
author: billmath
ms.service: active-directory
ms.subservice: governance
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: be33e492f44f6926f2ae2d133cf112245fba15f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93134877"
---
## <a name="cloud-hr-application-to-azure-active-directory-user-provisioning"></a>Aplicativo de RH na nuvem para provisionamento de usuários do Azure Active Directory

Historicamente, a equipe de TI dependia de métodos manuais para criar, atualizar e excluir funcionários. Eles usavam métodos como upload de arquivos CSV ou scripts personalizados para sincronizar dados de funcionários. Esses processos de provisionamento são propensos a erros, inseguros e difíceis de gerenciar.

Para gerenciar os ciclos de vida de identidade de funcionários, fornecedores ou funcionários contingentes, o [serviço de provisionamento de usuários do Azure AD (Azure Active Directory)](../articles/active-directory/app-provisioning/user-provisioning.md) oferece integração a aplicativos de RH (recursos humanos) baseados em nuvem. Os aplicativos de exemplo incluem o Workday ou o SuccessFactors.

O Azure AD usa essa integração para habilitar os seguintes fluxos de trabalho do aplicativo de RH na nuvem:

- **Provisionar usuários para o Active Directory:** provisione conjuntos de usuários selecionados de um aplicativo de RH na nuvem em um ou mais domínios do Active Directory.
- **Provisionar usuários somente na nuvem para o Azure AD:** em cenários em que o Active Directory não é usado, provisione usuários diretamente do aplicativo de RH na nuvem para o Azure AD.
- **Fazer write-back para o aplicativo de RH na nuvem:** grave os endereços de email e os atributos de nome de usuário do Azure AD de volta no aplicativo de RH na nuvem.


## <a name="enabled-hr-scenarios"></a>Cenários de RH habilitados

O serviço de provisionamento de usuários do Azure AD permite a automação dos seguintes cenários de gerenciamento do ciclo de vida de identidades baseado em RH:

- **Contratação de novos funcionários:** quando um novo funcionário é adicionado ao aplicativo de RH na nuvem, uma conta de usuário é criada automaticamente no Active Directory e no Azure AD com a opção de fazer write-back para o endereço de email e os atributos de nome de usuário no aplicativo de RH na nuvem.
- **Atualizações de perfil e atributo de funcionário:** quando um registro de funcionário, como nome, cargo ou gerente, é atualizado no aplicativo de RH na nuvem, a conta de usuário dele é atualizada automaticamente no Active Directory e no Azure AD.
- **Rescisão de funcionários:** quando um funcionário é encerrado no aplicativo de RH na nuvem, a conta de usuário dele é desabilitada automaticamente no Active Directory e no Azure AD.
- **Recontratações de funcionário:** quando um funcionário é recontratado no aplicativo de RH na nuvem, a conta antiga dele ser reativada ou provisionada de novo automaticamente para o Active Directory e o Azure AD.

## <a name="who-is-this-integration-best-suited-for"></a>Para quem é essa integração é mais adequada?

A integração do aplicativo de RH na nuvem com o provisionamento de usuários do Azure AD é ideal para as organizações que:

- Querem uma solução predefinida baseada em nuvem para o provisionamento de usuários de RH na nuvem.
- Exigem o provisionamento direto de usuário do aplicativo de RH na nuvem para o Active Directory ou o Azure AD.
- Exigem que os usuários sejam provisionados usando os dados obtidos do aplicativo de RH na nuvem.
- Exigem que o ingresso, a movimentação e a saída de usuários sejam sincronizados com uma ou mais florestas, domínios e unidades organizacionais do Active Directory com base apenas em informações de mudança detectadas no aplicativo de RH na nuvem.
- Usam o Office 365 para email.


### <a name="key-benefits"></a>Principais benefícios

Essa funcionalidade de provisionamento de TI controlada por RH oferece os seguintes benefícios de negócios significativos:

- **Aumentar a produtividade:** agora você pode automatizar a atribuição de contas de usuário e licenças do Office 365 e fornecer acesso a grupos de chaves. A automação de atribuições oferece a novos contratados acesso imediato a ferramentas de trabalho e aumenta a produtividade.
- **Gerenciar riscos:** você pode aumentar a segurança automatizando as alterações com base no status do funcionário ou em associações de grupo com os dados que fluem do aplicativo de RH na nuvem. Automatizar as alterações garante que as identidades do usuário e o acesso a aplicativos de chave sejam atualizados automaticamente quando os usuários fizerem a transição ou saem da organização.
- **Abordar a conformidade e a governança:** o Azure AD dá suporte a logs de auditoria nativos para solicitações de provisionamento de usuário executadas por aplicativos de sistemas de origem e de destino. Com a auditoria, você pode controlar quem tem acesso aos aplicativos de uma única tela.
- **Gerenciar o custo:** o provisionamento automático reduz os custos evitando ineficiências e erros humanos associados ao provisionamento manual. Ele reduz a necessidade de soluções de provisionamento de usuário desenvolvidas personalizadas criadas ao longo do tempo usando plataformas herdadas e desatualizadas.

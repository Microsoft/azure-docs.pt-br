---
title: Termos de Uso do Azure Active Directory | Microsoft Docs
description: "Os Termos de Uso do Azure AD permitirão que você e sua empresa possam fornecer os termos de uso aos usuários de serviços do Azure AD."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: d55872ef-7e45-4de5-a9a0-3298e3de3565
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: b6318b419a0ea87fd1fb56656b1161909876f338
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-terms-of-use-feature-preview"></a>Recurso de Termos de Uso do Azure Active Directory (Visualização)
OS Termos de Uso do Azure AD fornecem um método simples que as organizações podem usar para apresentar informações aos usuários finais.  Isso garante que os usuários vejam os avisos de isenção de responsabilidade relevantes para os requisitos de conformidade ou legais.

O conteúdo dos Termos de Uso do Azure AD são apresentados em formato PDF.   Esse PDF pode ter qualquer conteúdo, como documentos existentes de contrato, que permite que você colete os contratos do usuário final durante a entrada do usuário.  Você pode usar os termos de uso para aplicativos, grupos de usuários ou, se for o caso, vários termos de uso para finalidades diferentes.

O restante deste documento descreve como usar os Termos de Uso do Azure AD.  

## <a name="why-use-azure-ad-terms-of-use"></a>Por que usar os Termos de Uso do Azure AD
Está com dificuldades para que os seus funcionários ou convidados aceitem seus termos de uso antes de obter acesso? Precisa de ajuda para saber quem concordou ou não com os termos de uso da empresa?  Os Termos de Uso do Azure AD fornecem um método simples que as organizações podem usar para apresentar informações aos usuários finais.  Isso garante que eles vejam os avisos de isenção de responsabilidade relevantes para os requisitos de conformidade ou legais.

Os Termos de Uso do Azure AD podem ser usados nos seguintes cenários:
-   Termos gerais de uso para todos os usuários em sua organização.
-   Termos específicos de uso com base em atributos de um usuário (por exemplo, médicos vs enfermeiras ou funcionários locais vs internacionais, feitos por [grupos dinâmicos](https://azure.microsoft.com/updates/azure-active-directory-dynamic-membership-for-groups)).
-   Termos específicos de uso baseados no acesso de aplicativos de alto impacto nos negócios, como o Salesforce.


## <a name="prerequisites"></a>Pré-requisitos
Use as etapas a seguir para configurar os Termos de Uso do Azure AD:

1. Entre no Azure AD usando um administrador global, administrador de segurança ou um administrador de acesso condicional para o diretório que você deseja configurar os Termos de Uso Azure AD.
2. Certifique-se de que o diretório possui uma assinatura do Azure AD Premium P1, P2, EMS E3 ou EMS E5.  Caso contrário, [Obtenha o Azure AD Premium](active-directory-get-started-premium.md) ou [inicie uma avaliação gratuita](https://azure.microsoft.com/trial/get-started-active-directory/).
3. Exibir o painel de Termos do Usuário do Azure AD em [https://aka.ms/catou](https://aka.ms/catou).



## <a name="add-company-terms-of-use"></a>Adicionar Termos de Uso da empresa
Depois de preparar os seus Termos de Uso, use o procedimento a seguir para adicioná-lo.

### <a name="to-add-terms-of-use"></a>Para adicionar os Termos de Uso
1. Navegue até o painel em [https://aka.ms/catou](https://aka.ms/catou)
2. Clique em Adicionar.</br>
![Adicionar Termos de Uso](media/active-directory-tou/tou2.png)
3. Insira o **Nome** para os Termos de Uso
4. Insira o **Nome de exibição**.  Esse cabeçalho é que os usuários visualizam quando entram.
5. **Navegue** até o PDF com os seus termos de uso finalizados e selecione-o.  O tamanho de fonte recomendado é 24.
6. Você pode **Impor** os termos de uso carregados usando um modelo ou uma política de acesso condicional personalizada.  As políticas de acesso condicional personalizadas permitem termos de uso granulares, para até um aplicativo de nuvem ou um grupo de usuários específicos.  Para obter mais informações, consulte [Configurando as políticas de acesso condicional personalizadas](active-directory-conditional-access-best-practices.md)
7. Clique em **Criar**.
8. Se você selecionou um modelo de acesso condicional personalizado, uma nova tela é exibida e permite que você personalize a política de autoridade de certificação.
7. Agora você deve ver seus novos Termos de Uso.</br>

![Adicionar Termos de Uso](media/active-directory-tou/tou3.png)

## <a name="delete-terms-of-use"></a>Excluir Termos de Uso
Você pode remover ou excluir o termos de uso antigos usando o procedimento a seguir:

### <a name="to-delete-terms-of-use"></a>Para excluir os Termos de Uso
1. Navegue até o painel em [https://aka.ms/catou](https://aka.ms/catou)
2. Selecione os termos de uso que você deseja remover.
3. Clique em **Excluir**.
4. Agora você não deve mais ver seus novos Termos de Uso.


## <a name="audit-terms-of-use"></a>Auditar Termos de Uso
Os Termos de Uso do Azure AD oferecem uma auditoria fácil de usar para que você possa ver quem aceitou e quando aceitou os termos de uso.  Para começar a usar a auditoria use o procedimento a seguir:

### <a name="to-audit-terms-of-use"></a>Para auditar os Termos de Uso
1. Navegue até o painel em [https://aka.ms/catou](https://aka.ms/catou)
2. Clique em Auditar evento.</br>
![Auditar evento](media/active-directory-tou/tou8.png)
3.  Você pode filtrar as informações usando as listas suspensas fornecidas para buscar informações de log de auditoria específicas na tela de logs de auditoria do Azure AD.
![Auditar evento](media/active-directory-tou/tou9.png)
4.  Você também pode baixar as informações em um arquivo .csv para uso local.

## <a name="what-users-see"></a>O que os usuários visualizam
Os usuários que estão no escopo visualizarão o seguinte após os termos de uso terem sido criados e aplicados.  Eles visualizarão essas telas durante a entrada.
-   A prática recomendada é usar a fonte dentro do PDF em tamanho 24.
![Auditar evento](media/active-directory-tou/tou10.png)
-   Esta é a tela exibida nos celulares</br></br>
![Auditar evento](media/active-directory-tou/tou11.png)

## <a name="additional-information"></a>Informações adicionais
As informações a seguir devem ser consideradas e podem ajudar na utilização dos termos de uso.


Os usuários que estão no escopo precisarão sair e entrar novamente para atender a uma nova política se:
 - uma política de acesso condicional for habilitada nos termos de uso
 - ou se outros termos de uso forem criados

Isso ocorre porque as políticas de acesso condicional entram em vigor de forma imediata. Quando isso acontece o administrador começará a ver um ícone de "nuvem triste" ou "Problemas de token do Azure AD". O administrador deve sair e entrar novamente para atender à nova política.





## <a name="frequently-asked-questions"></a>Perguntas frequentes

**P: Como ver quando/se um usuário aceitou os termos de uso?**</br>
R: O usuário que aceita os termos de uso é gravado no log de auditoria. Você pode pesquisar o log de auditoria do Azure AD para ver os resultados.  

**P: Se os termos de uso forem alterados, os usuários precisam aceitá-los novamente?**</br>
R: Sim, um administrador pode alterar os termos de uso e isso requer que os novos termos também sejam aceitos.

**P: Os termos de uso possuem suporte para vários idiomas?**</br>
R: Não, atualmente não é possível ter vários idiomas em um único conjunto de termos de uso.  No entanto, você pode definir o escopo para um grupo (por exemplo, os termos de uso para a França são diferentes dos termos de uso para o Reino Unido). 

**P: Quando os termos de uso são acionados?**</br>
R: Os termos de uso são acionados acionada no momento em que o usuário faz o logon.

**P: Para quais aplicativos posso empregar os termos de uso?**</br>
R: Você pode criar uma política de acesso condicional nos aplicativos empresariais usando autenticação moderna.  Para obter mais informações, consulte [aplicativos empresariais](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-view-azure-portal).

**P: Posso adicionar vários termos de uso para um determinado usuário ou aplicativo?**</br>
R: Sim, criando várias políticas de acesso condicional, direcionadas a esses grupos ou aplicativos. Se um usuário estiver no escopo de vários termos de uso eles devem concordar com os termos de uso um por um.
 
**P: O que acontece se um usuário recusar os termos de uso?**</br>
R: O usuário é impedido de acessar o aplicativo. O usuário precisa entrar novamente e concordar com os termos para obter acesso.
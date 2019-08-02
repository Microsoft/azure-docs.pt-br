---
title: Senhas banidas dinamicamente-Azure Active Directory
description: Proibir senhas fracas do seu ambiente com senhas proibidas dinamicamente pelo Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe2b4ed91969248bc0818f98306a108555eac424
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67853048"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Eliminar senhas incorretas na organização

Líderes do setor informam que você não usa a mesma senha em vários lugares, para torná-lo complexo e para não torná-lo simples como "Password123". Como as organizações podem garantir que seus usuários estejam seguindo as diretrizes de práticas recomendadas? Como eles podem garantir que os usuários não estejam usando senhas fracas ou até mesmo variações de senhas fracas?

A etapa inicial de ter senhas mais fortes é fornecer orientação aos seus usuários. A diretriz atual da Microsoft sobre este tópico pode ser encontrada no seguinte link:

[Diretrizes de Senha da Microsoft](https://www.microsoft.com/research/publication/password-guidance)

Ter uma boa orientação é importante, mas mesmo com isso, sabemos que muitos usuários ainda acabarão escolhendo senhas fracas. A proteção de senha do Azure AD protege sua organização detectando e bloqueando senhas fracas conhecidas e suas variantes, bem como, opcionalmente, bloqueando termos fracos adicionais que são específicos para sua organização.

Para obter mais informações sobre os esforços atuais de segurança, consulte o [Relatório de inteligência de segurança da Microsoft](https://www.microsoft.com/security/operations/security-intelligence-report).

## <a name="global-banned-password-list"></a>Lista de senhas proibidas globalmente

A equipe de Azure AD Identity Protection analisa constantemente os dados de telemetria de segurança do Azure AD procurando senhas fracas ou comprometidas comumente usadas, ou mais especificamente, os termos de base fracos que geralmente são usados como base para senhas fracas. Quando esses termos fracos são encontrados, eles são adicionados à lista de senhas globais banidas. O conteúdo da lista de senhas excluídas global não se baseia em nenhuma fonte de dados externa. A lista de senhas globais banidas baseia-se inteiramente nos resultados contínuos da telemetria e análise de segurança do Azure AD.

Sempre que uma nova senha for alterada ou redefinida para qualquer usuário em qualquer locatário no Azure AD, a versão atual da lista de senhas excluídas global será usada como a entrada de chave ao validar a força da senha. Essa validação resulta em senhas muito mais fortes para todos os clientes do Azure AD.

> [!NOTE]
> Os criminosos virtuais também usam estratégias semelhantes em seus ataques. Portanto, a Microsoft não publica o conteúdo dessa lista publicamente.

## <a name="custom-banned-password-list"></a>Lista personalizada de senhas banidas

Algumas organizações talvez queiram melhorar ainda mais a segurança adicionando suas próprias personalizações sobre a lista de senhas globais banidas, em que a Microsoft chama a lista personalizada de senhas banidas. A Microsoft recomenda que os termos adicionados a essa lista se concentram principalmente em termos específicos da organização, como:

- Nomes de marca
- Nomes de produtos
- Locais (por exemplo, como sede da empresa)
- Termos internos específicos da empresa
- Abreviações que têm significado específico da empresa.

Depois que os termos forem adicionados à lista de senhas excluídas personalizada, eles serão combinados com os termos na lista de senhas globais banidas ao validar senhas.

> [!NOTE]
> A lista de senhas excluídas personalizada é limitada a ter no máximo 1000 termos. Ele não foi projetado para bloquear listas de senhas extremamente grandes. Para aproveitar totalmente os benefícios da lista personalizada de senhas banidas, a Microsoft recomenda que você primeiro revise e entenda o algoritmo de avaliação de senha (consulte [como as senhas são avaliadas](concept-password-ban-bad.md#how-are-passwords-evaluated)) antes de adicionar novos termos à lista de proibidos personalizada. Entender como o algoritmo funciona permitirá que sua empresa detecte e bloqueie com eficiência grandes números de senhas fracas e suas variantes.

Por exemplo: Considere um cliente chamado "contoso", que se baseia em Londres e que torna um produto chamado "widget". Para esse cliente, seria um desperdício, bem como menos seguro, tentar bloquear variações específicas desses termos, como:

- "Contoso! 1"
- "Contoso@London"
- "ContosoWidget"
- "! Funcionam
- "LondonHQ"
- ...etcetera

Em vez disso, é muito mais eficiente e seguro Bloquear apenas os termos básicos da chave:

- Funcionam
- Londres
- Widget

O algoritmo de validação de senha bloqueará automaticamente as variantes e as combinações fracas das anteriores.

A lista de senhas proibidas personalizada e a capacidade de habilitar a integração do Active Directory local são gerenciadas usando o portal do Azure.

![Modificar a lista personalizada de senhas banidas em métodos de autenticação](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>Ataques de irrigação de senha e listas de senhas comprometidas de terceiros

Um dos principais benefícios da proteção de senha do Azure AD é ajudá-lo a se defender contra ataques de irrigação de senha. A maioria dos ataques de irrigação de senha não tenta atacar uma determinada conta individual mais do que algumas vezes, pois esse comportamento aumenta muito a probabilidade de detecção, seja por meio de bloqueio de conta ou outros meios. A maioria dos ataques de irrigação de senha, portanto, depende do envio apenas de um pequeno número de senhas mais fracas conhecidas em relação a cada uma das contas de uma empresa. Essa técnica permite que o invasor pesquise rapidamente uma conta facilmente comprometida e, ao mesmo tempo, evite limites de detecção potenciais.

A proteção de senha do Azure AD é projetada para bloquear com eficiência todas as senhas fracas conhecidas que provavelmente serão usadas em ataques de irrigação de senha, com base em dados de telemetria de segurança do mundo real, como visto pelo Azure AD.  A Microsoft está ciente de sites de terceiros que enumeram milhões de senhas que foram comprometidas em violações de segurança publicamente conhecidas anteriores. É comum que produtos de validação de senha de terceiros sejam baseados em comparação de força bruta contra essas milhões de senhas. A Microsoft acredita que essas técnicas não são a melhor maneira de melhorar a intensidade geral da senha, considerando as estratégias típicas usadas pelos invasores de irrigação de senha.

> [!NOTE]
> A lista de senhas globais banidas da Microsoft não se baseia em nenhuma fonte de dados de terceiros, incluindo listas de senhas comprometidas.

Embora a lista global de proibidos da Microsoft seja pequena em comparação a algumas listas em massa de terceiros, seus efeitos de segurança são amplificados pelo fato de que ele é originado da telemetria de segurança do mundo real em ataques de irrigação de senha reais, além do fato de que a Microsoft o algoritmo de validação de senha usa técnicas de correspondência de fuzzing inteligente. O resultado final é que ele detectará com eficiência e bloqueará a utilização de milhões de senhas fracas mais comuns em sua empresa. Os clientes que optarem por adicionar termos específicos da organização à lista de senhas excluídas personalizada também se beneficiarão do mesmo algoritmo.

Informações adicionais sobre problemas de segurança baseados em senha podem ser examinadas em [seu PA $ $Word não importa](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984).

## <a name="on-premises-hybrid-scenarios"></a>Cenários híbridos locais

A proteção de contas somente na nuvem é útil, mas muitas organizações mantêm cenários híbridos, incluindo o Windows Server Active Directory local. Os benefícios de segurança da proteção de senha do Azure AD também podem ser estendidos para o ambiente de Active Directory do Windows Server por meio da instalação de agentes locais. Agora, os usuários e administradores que alteram ou redefinem senhas no Active Directory precisam estar em conformidade com a mesma política de senha que os usuários somente de nuvem.

## <a name="how-are-passwords-evaluated"></a>Como as senhas são avaliadas

Sempre que um usuário altera ou redefine sua senha, a nova senha é verificada quanto à força e à complexidade, validando-a em relação à lista combinada de termos das listas de senhas excluídas globais e personalizadas (se o último estiver configurado).

Mesmo que a senha do usuário contenha uma senha proibida, ela ainda poderá ser aceita se a senha geral for forte o suficiente em outros aspectos. Uma senha configurada recentemente passará pelas etapas a seguir para avaliar a força geral e determinar se ela deverá ser aceita ou rejeitada.

### <a name="step-1-normalization"></a>Etapa 1: Normalização

Primeiro, uma nova senha passa por um processo de normalização. Essa técnica permite que um pequeno conjunto de senhas banidas seja mapeado para um conjunto muito maior de senhas potencialmente fracas.

A normalização tem duas partes.  Primeiros, todas as letras maiúsculas são alteradas para letras minúsculas.  Por exemplo, as substituições de caractere comuns são realizadas, como:  

| Letra original  | Letra substituída |
| --- | --- |
| '0'  | 'o' |
| '1'  | 'l' |
| '$'  | 's' |
| '\@'  | 'a' |

Exemplo: suponha que a senha "em branco" seja proibida e um usuário tente alterar a senha para “Bl@nK”. Mesmo que “Bl@nk” não seja especificamente proibida, o processo de normalização converterá essa senha como "em branco", que é uma senha banida.

### <a name="step-2-check-if-password-is-considered-banned"></a>Etapa 2: Verificar se a senha é considerada proibida

#### <a name="fuzzy-matching-behavior"></a>Comportamento da correspondência difusa

Correspondência difusa é usada na senha normalizada para identificar se ela contém uma senha encontrada na lista de senhas banidas global ou personalizada. O processo de correspondência baseia-se em uma distância de edição de comparação de um (1).  

Exemplo: suponha que a senha “abcdef” seja proibida e um usuário tente alterar a senha para uma dos seguintes:

‘abcdeg’    *(último caractere foi alterado de ‘f’ para ‘g’)* ‘abcdefg’   *’(g’ acrescentado ao final)* ‘abcde’     *(‘f’ à direita excluído do fim)*

Cada uma das senhas acima não corresponde especificamente à senha banida "abcdef". No entanto, como cada exemplo está dentro de uma distância de edição de 1 do termo banido ' abcdef ', eles são considerados como uma correspondência para "abcdef".

#### <a name="substring-matching-on-specific-terms"></a>Correspondência de subcadeia de caracteres (em termos específicos)

A correspondência de subcadeia de caracteres é usada na senha normalizada para verificar o nome e o sobrenome do usuário, bem como o nome do locatário (observe que a correspondência de nome de locatário não é feita ao validar senhas em um controlador de domínio do Active Directory).

Exemplo: Suponha que tenhamos um User, pol, que deseja redefinir sua senha para "P0l123fb". Após a normalização, essa senha se tornaria "pol123fb". A correspondência de subcadeia de caracteres descobre que a senha contém o nome "pol" do usuário. Embora "P0l123fb" não tenha sido especificamente em uma lista de senhas banidas, a correspondência de subcadeia de caracteres encontrou "pol" na senha. Portanto, essa senha deve ser rejeitada.

#### <a name="score-calculation"></a>Cálculo de pontuação

A próxima etapa é identificar todas as instâncias de senhas banidas na nova senha normalizada do usuário. Em seguida:

1. Cada senha banida encontrada na senha do usuário recebe um ponto.
2. Cada caractere exclusivo restante recebe um ponto.
3. Uma senha deve ter pelo menos cinco (5) pontos para ser aceita.

Para os próximos dois exemplos, vamos supor que a Contoso esteja usando a proteção de senha do Azure AD e tenha "contoso" em suas listas personalizadas. Vamos supor também que "em branco" está na lista global.

Exemplo: um usuário altera sua senha para “C0ntos0Blank12”

Após a normalização, essa senha se torna “contosoblank12”. O processo de correspondência localiza que essa senha contém duas senhas banidas: contoso e em branco. Essa senha então recebe uma pontuação:

[contoso] + [blank] + [1] + [2] = 4 pontos já que essa senha está abaixo de cinco (5) pontos, ele será rejeitado.

Exemplo: um usuário altera a senha para "ContoS0Bl@nkf9!".

Após a normalização, essa senha se torna “contosoblankf9!”. O processo de correspondência localiza que essa senha contém duas senhas banidas: contoso e em branco. Essa senha então recebe uma pontuação:

[contoso] + [blank] + [f] + [9] + [!] = 5 pontos, pois essa senha tem pelo menos cinco (5) pontos, é aceita.

   > [!IMPORTANT]
   > Observe que o algoritmo de senhas banidas junto com a lista global pode mudar e muda a qualquer momento no Azure com base em análise e pesquisa de segurança contínuas. Para o serviço de agente DC local, os algoritmos atualizados só terão efeito depois que o software do agente DC for reinstalado.

## <a name="license-requirements"></a>Requisitos de licença

|   | Proteção por senha do AD do Azure com a lista de senhas banidas global | Proteção por senha do AD do Azure com a lista de senhas banidas personalizado|
| --- | --- | --- |
| Usuários somente na nuvem | AD do Azure Gratuito | O Azure AD Premium P1 ou P2 |
| Os usuários sincronizados no local Windows Server Active Directory | O Azure AD Premium P1 ou P2 | O Azure AD Premium P1 ou P2 |

> [!NOTE]
> Os usuários locais do Windows Server Active Directory que não foram sincronizados com Azure Active Directory também têm os benefícios da proteção de senha do Azure AD com base no licenciamento existente para usuários sincronizados.

Informações adicionais sobre licenciamento, incluindo custos, podem ser encontradas no [site de preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-users-see"></a>O que os usuários veem

Quando um usuário tentar redefinir uma senha para alguma que seria proibida, a seguinte mensagem de erro será exibida:

Infelizmente, sua senha contém uma palavra, uma frase ou um padrão que pode ser facilmente adivinhado. Tente novamente com uma senha diferente.

## <a name="next-steps"></a>Próximas etapas

- [Configurar a lista de senhas proibidas personalizada](howto-password-ban-bad.md)
- [Habilitar agentes de proteção por senha do Azure AD local](howto-password-ban-bad-on-premises-deploy.md)

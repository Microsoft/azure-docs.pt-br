---
title: Proteção por senha no Azure Active Directory
description: Saiba como proibir dinamicamente senhas fracas do seu ambiente com proteção de senha Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: b019c12103a0324e21ef7c06b0c7a5d7fe14e6ec
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743880"
---
# <a name="eliminate-bad-passwords-using-azure-active-directory-password-protection"></a>Eliminar senhas ruins usando Azure Active Directory proteção por senha

Muitas diretrizes de segurança recomendam que você não use a mesma senha em vários locais, para torná-la complexa e para evitar senhas simples, como *Password123*. Você pode fornecer aos seus usuários [orientações sobre como escolher senhas](https://www.microsoft.com/research/publication/password-guidance), mas senhas fracas ou inseguras geralmente são usadas. A proteção por senha do Azure AD detecta e bloqueia senhas fracas conhecidas e suas variantes e também pode bloquear outros termos fracos que são específicos para sua organização.

Com a proteção de senha do Azure AD, as listas de senhas globais banidas padrão são automaticamente aplicadas a todos os usuários em um locatário do Azure AD. Para dar suporte às suas próprias necessidades de negócios e de segurança, você pode definir entradas em uma lista personalizada de senhas banidas. Quando os usuários alteram ou redefinem suas senhas, essas listas de senhas banidas são verificadas para impor o uso de senhas fortes.

Você deve usar recursos adicionais como [a autenticação multifator do Azure ad](concept-mfa-howitworks.md), não apenas se basear em senhas fortes impostas pela proteção por senha do Azure AD. Para obter mais informações sobre como usar várias camadas de segurança para seus eventos de entrada, consulte [seu PA $ $Word não importa](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984).

> [!IMPORTANT]
> Este artigo conceitual explica a um administrador como funciona a proteção de senha do Azure AD. Se você for um usuário final já registrado para redefinição de senha de autoatendimento e precisar voltar à sua conta, vá para [https://aka.ms/sspr](https://aka.ms/sspr) .
>
> Se sua equipe de TI não tiver habilitado a capacidade de redefinir sua própria senha, entre em contato com sua assistência técnica para obter mais assistência.

## <a name="global-banned-password-list"></a>Lista de senhas proibidas globalmente

A equipe de Azure AD Identity Protection analisa constantemente os dados de telemetria de segurança do Azure AD procurando senhas mais fracas ou comprometidas usadas com frequência. Especificamente, a análise procura termos base que geralmente são usados como base para senhas fracas. Quando são encontrados termos fracos, eles são adicionados à *lista de senhas globais banidas*. O conteúdo da lista de senhas excluídas global não se baseia em nenhuma fonte de dados externa, mas nos resultados da análise e telemetria de segurança do Azure AD.

Quando uma senha é alterada ou redefinida para qualquer usuário em um locatário do Azure AD, a versão atual da lista de senhas excluídas global é usada para validar a força da senha. Essa verificação de validação resulta em senhas mais fortes para todos os clientes do Azure AD.

A lista de senhas excluídas global é aplicada automaticamente a todos os usuários em um locatário do Azure AD. Não há nada para habilitar ou configurar e não pode ser desabilitado. Essa lista global de senhas banidas é aplicada aos usuários quando eles alteram ou redefinem sua própria senha por meio do Azure AD.

> [!NOTE]
> Os criminosos virtuais também usam estratégias semelhantes em seus ataques para identificar senhas e variações de baixa segurança comuns. Para melhorar a segurança, a Microsoft não publica o conteúdo da lista global de senhas banidas.

## <a name="custom-banned-password-list"></a>Lista personalizada de senhas banidas

Algumas organizações desejam melhorar a segurança e adicionar suas próprias personalizações sobre a lista de senhas globais banidas. Para adicionar suas próprias entradas, você pode usar a *lista personalizada de senhas banidas*. Os termos adicionados à lista de senhas excluídas personalizada devem estar concentrados em termos específicos da organização, como os exemplos a seguir:

- Nomes de marcas
- Nomes de produtos
- Localizações, como a sede da empresa
- Termos internos específicos da empresa
- Abreviações que tenham um significado específico para a empresa

Quando os termos são adicionados à lista de senhas excluídas personalizada, eles são combinados com os termos na lista de senhas globais banidas. Os eventos de alteração ou redefinição de senha são validados em relação ao conjunto combinado dessas listas de senhas banidas.

> [!NOTE]
> A lista personalizada de senhas proibidas é limitada a um máximo de 1.000 termos. Ele não foi projetado para bloquear listas de senhas extremamente grandes.
>
> Para aproveitar ao máximo os benefícios da lista personalizada de senhas banidas, entenda primeiro [como as senhas são avaliadas](#how-are-passwords-evaluated) antes de adicionar termos à lista de proibidos personalizada. Essa abordagem permite detectar e bloquear com eficiência grandes números de senhas fracas e suas variantes.

![Modificar a lista personalizada de senhas banidas em métodos de autenticação](./media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords-cropped.png)

Vamos considerar um cliente chamado *contoso*. A empresa é baseada em Londres e faz com que um produto seja denominado *widget*. Para este cliente de exemplo, seria um desperdício e menos seguro tentar bloquear variações específicas desses termos, como o seguinte:

- "Contoso! 1"
- "Contoso@London"
- "ContosoWidget"
- "! Funcionam
- "LondonHQ"

Em vez disso, é muito mais eficiente e seguro Bloquear apenas os termos básicos da chave, como os exemplos a seguir:

- "Contoso"
- Londres
- Widget

O algoritmo de validação de senha bloqueia automaticamente variantes e combinações fracas.

Para começar a usar uma lista personalizada de senhas banidas, conclua o seguinte tutorial:

> [!div class="nextstepaction"]
> [Tutorial: configurar senhas banidas personalizadas](tutorial-configure-custom-password-protection.md)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>Ataques de irrigação de senha e listas de senhas comprometidas de terceiros

A proteção de senha do Azure AD ajuda você a se defender contra ataques de irrigação de senha. A maioria dos ataques de irrigação de senha não tenta atacar uma determinada conta individual mais do que algumas vezes. Esse comportamento aumentaria a probabilidade de detecção, seja por meio de bloqueio de conta ou outros meios.

Em vez disso, a maioria dos ataques de irrigação de senha envia apenas um pequeno número de senhas mais fracas conhecidas em relação a cada uma das contas de uma empresa. Essa técnica permite que o invasor pesquise rapidamente uma conta facilmente comprometida e evite limites de detecção potenciais.

A proteção por senha do Azure AD bloqueia com eficiência todas as senhas fracas conhecidas que provavelmente serão usadas em ataques de irrigação de senha. Essa proteção é baseada em dados de telemetria de segurança do mundo real do Azure AD para criar a lista de senhas globais banidas.

Há alguns sites de terceiros que enumeram milhões de senhas que foram comprometidas em violações de segurança publicamente conhecidas anteriores. É comum que produtos de validação de senha de terceiros sejam baseados em comparação de força bruta contra essas milhões de senhas. No entanto, essas técnicas não são a melhor maneira de melhorar a intensidade geral da senha, considerando as estratégias típicas usadas pelos invasores de irrigação de senha.

> [!NOTE]
> A lista de senhas excluídas global não se baseia em nenhuma fonte de dados de terceiros, incluindo listas de senhas comprometidas.

Embora a lista de proibidos global seja pequena em comparação a algumas listas em massa de terceiros, ela é originada da telemetria de segurança do mundo real em ataques de irrigação de senha reais. Essa abordagem melhora a segurança e a eficácia geral, e o algoritmo de validação de senha também usa técnicas de correspondência de fuzzing inteligente. Como resultado, a proteção de senha do Azure AD detecta e bloqueia com eficiência milhões das senhas fracas mais comuns de serem usadas em sua empresa.

## <a name="on-premises-hybrid-scenarios"></a>Cenários híbridos locais

Muitas organizações têm um modelo de identidade híbrida que inclui ambientes de Active Directory Domain Services (AD DS) locais. Para estender os benefícios de segurança da proteção de senha do Azure AD para seu ambiente de AD DS, você pode instalar componentes em seus servidores locais. Esses agentes exigem eventos de alteração de senha no ambiente de AD DS local para obedecer à mesma política de senha do Azure AD.

Para obter mais informações, consulte [impor a proteção por senha do Azure ad para AD DS](concept-password-ban-bad-on-premises.md).

## <a name="how-are-passwords-evaluated"></a>Como as senhas são avaliadas

Quando um usuário altera ou redefine sua senha, a nova senha é verificada quanto à força e à complexidade, validando-a na lista combinada de termos das listas de senhas excluídas globais e personalizadas.

Mesmo que a senha de um usuário contenha uma senha banida, a senha poderá ser aceita se a senha geral for, de outra forma, forte o suficiente. Uma senha recentemente configurada passa pelas etapas a seguir para avaliar sua força geral para determinar se ela deve ser aceita ou rejeitada:

### <a name="step-1-normalization"></a>Etapa 1: normalização

Primeiro, uma nova senha passa por um processo de normalização. Essa técnica permite que um pequeno conjunto de senhas banidas seja mapeado para um conjunto muito maior de senhas potencialmente fracas.

A normalização tem as duas partes a seguir:

* Todas as letras maiúsculas são alteradas para minúsculas.
* Em seguida, as substituições de caracteres comuns são executadas, como no exemplo a seguir:

   | Letra original | Letra substituída |
   |-----------------|--------------------|
   | 0               | o                  |
   | 1               | l                  |
   | $               | s                  |
   | \@              | um                  |

Considere o seguinte exemplo:

* A senha "blank" é banida.
* Um usuário tenta alterar sua senha para " Bl@nK ".
* Embora " Bl@nk " não seja banido, o processo de normalização Converte essa senha em "blank".
* Esta senha seria rejeitada.

### <a name="step-2-check-if-password-is-considered-banned"></a>Etapa 2: verificar se a senha é considerada banida

Uma senha é examinada para outro comportamento de correspondência e uma pontuação é gerada. Essa pontuação final determina se a solicitação de alteração de senha é aceita ou rejeitada.

#### <a name="fuzzy-matching-behavior"></a>Comportamento da correspondência difusa

Correspondência difusa é usada na senha normalizada para identificar se ela contém uma senha encontrada na lista de senhas banidas global ou personalizada. O processo de correspondência baseia-se em uma distância de edição de comparação de um (1).

Considere o seguinte exemplo:

* A senha "abcdef" é banida.
* Um usuário tenta alterar sua senha para um dos seguintes:

   * ' abcdeg '- *último caractere alterado de ' f ' para ' g '*
   * ' abcdefg '- *' g ' anexado ao final*
   * ' abcde '-o *' f ' à direita foi excluído do final*

* Cada uma das senhas acima não corresponde especificamente à senha banida "abcdef".

    No entanto, como cada exemplo está dentro de uma distância de edição de 1 do termo banido ' abcdef ', eles são considerados como uma correspondência para "abcdef".
* Essas senhas seriam rejeitadas.

#### <a name="substring-matching-on-specific-terms"></a>Correspondência de subcadeia de caracteres (em termos específicos)

A correspondência de subcadeia de caracteres é usada na senha normalizada para verificar o nome e o sobrenome do usuário, bem como o nome do locatário. A correspondência de nome de locatário não é feita ao validar senhas em um controlador de domínio AD DS para cenários híbridos locais.

> [!IMPORTANT]
> A correspondência de subcadeia de caracteres é imposta somente para nomes e outros termos, que têm pelo menos quatro caracteres de comprimento.

Considere o seguinte exemplo:

* Um usuário chamado Poll que deseja redefinir sua senha para "p0LL23fb".
* Após a normalização, essa senha se tornaria "poll23fb".
* A correspondência de subcadeia de caracteres descobre que a senha contém o primeiro nome do usuário "Poll".
* Embora "poll23fb" não tenha sido especificamente em uma lista de senhas banidas, a correspondência de subcadeia de caracteres encontrou "Poll" na senha.
* Esta senha seria rejeitada.

#### <a name="score-calculation"></a>Cálculo de pontuação

A próxima etapa é identificar todas as instâncias de senhas banidas na nova senha normalizada do usuário. Os pontos são atribuídos com base nos seguintes critérios:

1. Cada senha banida encontrada na senha de um usuário recebe um ponto.
1. Cada caractere exclusivo restante recebe um ponto.
1. Uma senha deve ter pelo menos cinco (5) pontos para serem aceitas.

Para os próximos dois cenários de exemplo, a Contoso está usando a proteção de senha do Azure AD e tem "contoso" em sua lista personalizada de senhas banidas. Vamos supor também que "blank" está na lista global.

No cenário de exemplo a seguir, um usuário altera sua senha para "C0ntos0Blank12":

* Após a normalização, essa senha se torna "contosoblank12".
* O processo de correspondência descobre que essa senha contém duas senhas banidas: "contoso" e "blank".
* Essa senha, em seguida, recebe a seguinte Pontuação:

    *[contoso] + [em branco] + [1] + [2] = 4 pontos*

* Como essa senha está abaixo de cinco (5) pontos, ela é rejeitada.

Vejamos um exemplo um pouco diferente para mostrar como a complexidade adicional em uma senha pode criar o número necessário de pontos a serem aceitos. No cenário de exemplo a seguir, um usuário altera sua senha para " ContoS0Bl@nkf9 !":

* Após a normalização, essa senha se torna "contosoblankf9!".
* O processo de correspondência descobre que essa senha contém duas senhas banidas: "contoso" e "blank".
* Essa senha, em seguida, recebe a seguinte Pontuação:

    *[contoso] + [em branco] + [f] + [9] + [!] = 5 pontos*

* Como essa senha tem pelo menos cinco (5) pontos, ela é aceita.

> [!IMPORTANT]
> O algoritmo de senha banido, junto com a lista de senhas globais banidas, pode e faz alterações a qualquer momento no Azure com base na análise e pesquisa de segurança contínuas.
>
> Para o serviço de agente DC local em cenários híbridos, os algoritmos atualizados só entram em vigor depois que o software do agente DC é atualizado.

## <a name="what-do-users-see"></a>O que os usuários veem

Quando um usuário tenta redefinir uma senha para algo que seria banido, a seguinte mensagem de erro é exibida:

*"Infelizmente, sua senha contém uma palavra, frase ou padrão que torna sua senha facilmente adivinhada. Tente novamente com uma senha diferente. "*

## <a name="license-requirements"></a>Requisitos de licença

| Usuários | Proteção de senha do Azure AD com a lista de senhas banida global | Proteção de senha do Azure AD com a lista de senhas banidas personalizada|
|-------------------------------------------|---------------------------|---------------------------|
| Usuários somente na nuvem                          | AD do Azure Gratuito             | O Azure AD Premium P1 ou P2 |
| Usuários sincronizados do AD DS local | O Azure AD Premium P1 ou P2 | O Azure AD Premium P1 ou P2 |

> [!NOTE]
> Os usuários locais AD DS que não estão sincronizados com o Azure AD também se beneficiam da proteção de senha do Azure AD com base no licenciamento existente para usuários sincronizados.

Informações adicionais sobre licenciamento, incluindo custos, podem ser encontradas no [site de preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="next-steps"></a>Próximas etapas

Para começar a usar uma lista personalizada de senhas banidas, conclua o seguinte tutorial:

> [!div class="nextstepaction"]
> [Tutorial: configurar senhas banidas personalizadas](tutorial-configure-custom-password-protection.md)

Você também pode [habilitar a proteção de senha do Azure ad local](howto-password-ban-bad-on-premises-deploy.md).

---
title: Configurar uma passagem de acesso temporária no Azure AD para registrar métodos de autenticação com senha
description: Saiba como configurar e permitir que os usuários registrem métodos de autenticação com senha usando uma passagem de acesso temporária
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarckms
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0805ac84318a4fee98c30127ac80c0dac2b96309
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558254"
---
# <a name="configure-temporary-access-pass-in-azure-ad-to-register-passwordless-authentication-methods-preview"></a>Configurar a passagem de acesso temporário no Azure AD para registrar métodos de autenticação com senha (versão prévia)

Os métodos de autenticação sem senha, como FIDO2 e entrada por telefone sem senha por meio do aplicativo Microsoft Authenticator, permitem que os usuários se conectem de forma segura com uma senha. Os usuários podem inicializar métodos com senha de uma das duas maneiras:

- Usando métodos existentes de autenticação multifator do Azure AD 
- Usando uma passagem de acesso temporária (toque) 

Uma passagem de acesso temporária é uma senha com tempo limitado emitida por um administrador que atende aos requisitos de autenticação fortes e pode ser usada para integrar outros métodos de autenticação, incluindo aqueles com senha. Uma aprovação de acesso temporária também facilita a recuperação quando um usuário perde ou esqueceu seu fator de autenticação forte, como uma chave de segurança FIDO2 ou um aplicativo Microsoft Authenticator, mas precisa entrar para registrar novos métodos de autenticação forte.


Este artigo mostra como habilitar e usar uma passagem de acesso temporária no Azure AD usando o portal do Azure. Você também pode executar essas ações usando as APIs REST. 

>[!NOTE]
>A fase de acesso temporária está atualmente em visualização pública. Alguns recursos podem não ter suporte ou ter recursos limitados. 

## <a name="enable-the-temporary-access-pass-policy"></a>Habilitar a política de aprovação de acesso temporário

Uma política de aprovação de acesso temporária define as configurações, como o tempo de vida de passagens criadas no locatário ou os usuários e grupos que podem usar uma passagem de acesso temporária para entrar. Antes que qualquer pessoa possa entrar com uma aprovação de acesso temporário, você precisa habilitar a política do método de autenticação e escolher quais usuários e grupos podem entrar usando uma aprovação de acesso temporária.
Embora você possa criar um passo de acesso temporário para qualquer usuário, somente aqueles incluídos na política podem entrar com ele.

Os detentores de função de administrador global e de política de método de autenticação podem atualizar a política de método de autenticação de passagem de acesso temporária.
Para configurar a política de método de autenticação do Pass Access temporário:

1. Entre no portal do Azure como um administrador global e clique em **Azure Active Directory**  >  métodos de autenticação de **segurança**  >    >  **passagem de acesso temporário**.
1. Clique em **Sim** para habilitar a política, selecione quais usuários têm a política aplicada e todas as configurações **gerais** .

   ![Captura de tela de como habilitar a política do método de autenticação de passagem de acesso temporário](./media/how-to-authentication-temporary-access-pass/policy.png)

   O valor padrão e o intervalo de valores permitidos são descritos na tabela a seguir.


   | Setting | Valores padrão | Valores permitidos | Comentários |
   |---|---|---|---|
   | Tempo de vida mínimo | 1 hora | 10 a 43200 minutos (30 dias) | Número mínimo de minutos durante os quais a passagem de acesso temporária é válida. |
   | Tempo de vida máximo | 24 horas | 10 a 43200 minutos (30 dias) | Número máximo de minutos durante os quais a passagem de acesso temporária é válida. |
   | Tempo de vida padrão | 1 hora | 10 a 43200 minutos (30 dias) | Os valores padrão podem ser substituídos por passagens individuais, dentro do tempo de vida mínimo e máximo configurado pela política. |
   | Uso único | Falso | Verdadeiro/falso | Quando a política é definida como false, as passagens no locatário podem ser usadas uma ou mais de uma vez durante sua validade (tempo de vida máximo). Ao impor o uso único na política de aprovação de acesso temporário, todas as passagens criadas no locatário serão criadas como um único uso. |
   | Comprimento | 8 | 8-48 caracteres | Define o comprimento da senha. |

## <a name="create-a-temporary-access-pass-in-the-azure-ad-portal"></a>Criar uma passagem de acesso temporária no portal do AD do Azure

Depois de habilitar uma política, você pode criar uma passagem de acesso temporária para um usuário no Azure AD. Essas funções podem executar as seguintes ações relacionadas a uma aprovação temporária de acesso.

- O administrador global pode criar, excluir, exibir uma aprovação de acesso temporária em qualquer usuário (exceto por si só)
- Os administradores de autenticação privilegiada podem criar, excluir, exibir uma passagem de acesso temporária em administradores e Membros (exceto em si)
- Os administradores de autenticação podem criar, excluir, exibir uma passagem de acesso temporário em Membros (exceto por si mesmos)
- O administrador global pode exibir os detalhes de aprovação de acesso temporário no usuário (sem ler o próprio código).

Para criar uma aprovação temporária de acesso:

1. Entre no portal como um administrador global, administrador de autenticação privilegiada ou administrador de autenticação. 
1. Clique em **Azure Active Directory**, navegue até usuários, selecione um usuário, como *Chris Green*, e escolha **métodos de autenticação**.
1. Se necessário, selecione a opção para **experimentar a nova experiência de métodos de autenticação de usuário**.
1. Selecione a opção para **Adicionar métodos de autenticação**.
1. Abaixo, **escolha o método**, clique em **aprovação de acesso temporário (versão prévia)**.
1. Defina uma hora ou duração de ativação personalizada e clique em **Adicionar**.

   ![Captura de tela de como criar uma passagem de acesso temporária](./media/how-to-authentication-temporary-access-pass/create.png)

1. Depois de adicionados, os detalhes da aprovação de acesso temporário são mostrados. Anote o valor real de aprovação de acesso temporário. Você fornece esse valor para o usuário. Você não pode exibir esse valor depois de clicar em **OK**.
   
   ![Captura de tela de detalhes de aprovação de acesso temporário](./media/how-to-authentication-temporary-access-pass/details.png)

## <a name="use-a-temporary-access-pass"></a>Usar uma passagem de acesso temporária

O uso mais comum de um passo de acesso temporário é para um usuário registrar detalhes de autenticação durante a primeira entrada, sem a necessidade de concluir prompts de segurança adicionais. Os métodos de autenticação são registrados em [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) . Os usuários também podem atualizar os métodos de autenticação existentes aqui.

1. Abra um navegador da Web para [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) .
1. Insira o UPN da conta para a qual você criou a aprovação de acesso temporário, como *tapuser@contoso.com* .
1. Se o usuário estiver incluído na política de aprovação de acesso temporário, ele verá uma tela para inserir sua aprovação de acesso temporária.
1. Insira a aprovação de acesso temporária que foi exibida no portal do Azure.

   ![Captura de tela de como inserir uma aprovação temporária de acesso](./media/how-to-authentication-temporary-access-pass/enter.png)

>[!NOTE]
>Para domínios federados, uma passagem de acesso temporária é preferida sobre a Federação. Um usuário com uma passagem de acesso temporária concluirá a autenticação no Azure AD e não será redirecionado para o IdP (provedor de identidade federada).

O usuário agora está conectado e pode atualizar ou registrar um método como a chave de segurança FIDO2. Os usuários que atualizam seus métodos de autenticação devido à perda de suas credenciais ou dispositivo devem se certificar de remover os métodos de autenticação antigos.

Os usuários também podem usar sua aprovação de acesso temporário para registrar-se para entrar no telefone sem senha diretamente do aplicativo autenticador. Para obter mais informações, consulte [adicionar sua conta corporativa ou de estudante ao aplicativo Microsoft Authenticator](../user-help/user-help-auth-app-add-work-school-account.md).

![Captura de tela de como inserir uma aprovação de acesso temporária usando a conta corporativa ou de estudante](./media/how-to-authentication-temporary-access-pass/enter-work-school.png)

## <a name="delete-a-temporary-access-pass"></a>Excluir uma passagem de acesso temporária

Uma passagem de acesso temporário expirada não pode ser usada. Nos **métodos de autenticação** para um usuário, a coluna de **detalhes** mostra quando a aprovação de acesso temporário expirou. Você pode excluir uma aprovação de acesso temporário expirada usando as seguintes etapas:

1. No portal do AD do Azure, navegue até **usuários**, selecione um usuário, como *toque em usuário* e, em seguida, escolha **métodos de autenticação**.
1. No lado direito do método de autenticação de **passagem de acesso temporário (versão prévia)** mostrado na lista, selecione **excluir**.

## <a name="replace-a-temporary-access-pass"></a>Substituir uma passagem de acesso temporária 

- Um usuário só pode ter uma passagem de acesso temporária. A senha pode ser usada durante a hora de início e de término da aprovação de acesso temporária.
- Se o usuário exigir uma nova passagem de acesso temporário:
  - Se a passagem de acesso temporário existente for válida, o administrador precisará excluir a aprovação de acesso temporário existente e criar uma nova passagem para o usuário. A exclusão de uma passagem de acesso temporário válida revogará as sessões do usuário. 
  - Se a aprovação de acesso temporário existente tiver expirado, uma nova passagem de acesso temporário substituirá a aprovação de acesso temporário existente e não revogará as sessões do usuário.

Para obter mais informações sobre os padrões NIST para integração e recuperação, consulte [publicação especial do NIST 800-63A](https://pages.nist.gov/800-63-3/sp800-63a.html#sec4).

## <a name="limitations"></a>Limitações

Tenha essas limitações em mente:

- Ao usar uma passagem de acesso temporário única para registrar um método sem senha, como FIDO2 ou entrada no telefone, o usuário deve concluir o registro dentro de 10 minutos de entrada com a passagem de acesso temporário única. Essa limitação não se aplica a uma aprovação de acesso temporária que pode ser usada mais de uma vez.
- Os usuários convidados não podem entrar com uma aprovação de acesso temporária.
- Os usuários no escopo da política de registro de redefinição de senha de autoatendimento (SSPR) serão necessários para registrar um dos métodos SSPR depois que eles tiverem entrado com uma aprovação de acesso temporária. Se o usuário usar apenas a chave FIDO2, exclua-os da política SSPR ou desabilite a política de registro SSPR. 
- Uma passagem de acesso temporária não pode ser usada com a extensão do servidor de diretivas de rede (NPS) e o adaptador de Serviços de Federação do Active Directory (AD FS) (AD FS).
- Quando o SSO contínuo está habilitado no locatário, os usuários são solicitados a inserir uma senha. O link **usar seu passo de acesso temporário** será disponibilizado para que o usuário entre com uma passagem de acesso temporária.

  ![Captura de tela de usar uma passagem de acesso temporário](./media/how-to-authentication-temporary-access-pass/alternative.png)

## <a name="troubleshooting"></a>Solução de problemas    

- Se uma passagem de acesso temporário não for oferecida a um usuário durante a entrada, verifique o seguinte:
  - O usuário está no escopo da política do método de autenticação de passagem de acesso temporário.
  - O usuário tem um passo de acesso temporário válido e, se for um único uso, ele ainda não foi usado.
- Se a **conexão de passagem de acesso temporária foi bloqueada devido à política de credenciais do usuário** aparecer durante a entrada com uma aprovação de acesso temporária, verifique o seguinte:
  - O usuário tem uma aprovação de acesso temporário de vários uso, enquanto a política do método de autenticação requer uma passagem de acesso temporário única.
  - Uma passagem de acesso temporário única já foi usada.

## <a name="next-steps"></a>Próximas etapas

- [Planejar uma implantação de autenticação com senha no Azure Active Directory](howto-authentication-passwordless-deployment.md)


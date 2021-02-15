---
title: Configurar uma passagem de acesso temporária no Azure AD para registrar métodos de autenticação com senha
description: Saiba como configurar e permitir que os usuários registrem métodos de autenticação com senha usando uma passagem de acesso temporária (toque)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: justinha
author: inbarckms
manager: daveba
ms.reviewer: inbarckms
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56d45119fa86ab47e6a625c628d8cb9763db83bd
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520852"
---
# <a name="configure-temporary-access-pass-in-azure-ad-to-register-passwordless-authentication-methods-preview"></a>Configurar a passagem de acesso temporário no Azure AD para registrar métodos de autenticação com senha (versão prévia)

Os métodos de autenticação sem senha, como FIDO2 e entrada por telefone sem senha por meio do aplicativo Microsoft Authenticator, permitem que os usuários se conectem de forma segura com uma senha. Os usuários podem inicializar métodos com senha de uma das duas maneiras:

- Usando métodos existentes de autenticação multifator do Azure AD 
- Usando uma passagem de acesso temporária (toque) 

O TAP é uma senha com tempo limitado emitida por um administrador que atende aos requisitos de autenticação fortes e pode ser usada para integrar outros métodos de autenticação, incluindo aqueles com senha. O TAP também facilita a recuperação quando um usuário perde ou esqueceu seu fator de autenticação forte, como uma chave de segurança FIDO2 ou um aplicativo Microsoft Authenticator, mas precisa entrar para registrar novos métodos de autenticação forte.


Este artigo mostra como habilitar e usar um toque no Azure AD usando o portal do Azure. Você também pode executar essas ações usando as APIs REST. 

>[!NOTE]
>A fase de acesso temporária está atualmente em visualização pública. Alguns recursos podem não ter suporte ou ter recursos limitados. 

## <a name="enable-the-tap-policy"></a>Habilitar a política de toque

Uma política de toque define as configurações, como o tempo de vida de passagens criadas no locatário ou os usuários e grupos que podem usar um toque para entrar. Antes que qualquer pessoa possa entrar com um toque, você precisa habilitar a política do método de autenticação e escolher quais usuários e grupos podem entrar usando um toque.
Embora você possa criar um toque para qualquer usuário, somente aqueles incluídos na política podem entrar com ele.

Os proprietários de função de administrador global e de política de método de autenticação podem atualizar a diretiva de método de autenticação TAP.
Para configurar a diretiva de método de autenticação TAP:

1. Entre no portal do Azure como um administrador global e clique em **Azure Active Directory**  >  métodos de autenticação de **segurança**  >    >  **passagem de acesso temporário**.
1. Clique em **Sim** para habilitar a política, selecione quais usuários têm a política aplicada e todas as configurações **gerais** .

   ![Captura de tela de como habilitar a diretiva de método de autenticação TAP](./media/how-to-authentication-temporary-access-pass/policy.png)

   O valor padrão e o intervalo de valores permitidos são descritos na tabela a seguir.


   | Setting          | Valores padrão | Valores permitidos               | Comentários                                                                                                                                                                                                                                                                 |   |
   |------------------|----------------|------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
    Tempo de vida mínimo | 1 hora         | 10 a 43200 minutos (30 dias) | Número mínimo de minutos em que o toque é válido.                                                                                                                                                                                                                         |   |
   | Tempo de vida máximo | 24 horas       | 10 a 43200 minutos (30 dias) | Número máximo de minutos em que o toque é válido.                                                                                                                                                                                                                         |   |
   | Tempo de vida padrão | 1 hora         | 10 a 43200 minutos (30 dias) | Os valores padrão podem ser substituídos por passagens individuais, dentro do tempo de vida mínimo e máximo configurado pela política                                                                                                                                                |   |
   | Uso único     | Falso          | Verdadeiro/falso                 | Quando a política é definida como false, as passagens no locatário podem ser usadas uma ou mais de uma vez durante sua validade (tempo de vida máximo). Ao impor o uso único na política TAP, todas as passagens criadas no locatário serão criadas como um único uso. |   |
   | Comprimento           | 8              | 8-48 caracteres              | Define o comprimento da senha.                                                                                                                                                                                                                                      |   |

## <a name="create-a-tap-in-the-azure-ad-portal"></a>Criar um toque no portal do AD do Azure

Depois de habilitar uma política de toque, você pode criar um toque para um usuário no Azure AD. Essas funções podem executar as seguintes ações relacionadas ao TAP.

- O administrador global pode criar, excluir, exibir toque em qualquer usuário (exceto por si só)
- Administradores de autenticação privilegiada podem criar, excluir, exibir toque em administradores e Membros (exceto em si)
- Os administradores de autenticação podem criar, excluir, exibir toque em Membros (exceto em si)
- O administrador global pode exibir os detalhes do toque no usuário (sem ler o próprio código).

Para criar um toque:

1. Entre no portal como um administrador global, administrador de autenticação privilegiada ou administrador de autenticação. 
1. Clique em **Azure Active Directory**, navegue até usuários, selecione um usuário, como *Chris Green*, e escolha **métodos de autenticação**.
1. Se necessário, selecione a opção para **experimentar a nova experiência de métodos de autenticação de usuário**.
1. Selecione a opção para **Adicionar métodos de autenticação**.
1. Abaixo, **escolha o método**, clique em **aprovação de acesso temporário (versão prévia)**.
1. Defina uma hora ou duração de ativação personalizada e clique em **Adicionar**.

   ![Captura de tela de como criar um toque](./media/how-to-authentication-temporary-access-pass/create.png)

1. Depois de adicionados, os detalhes do toque são mostrados. Anote o valor real de TAP. Você fornece esse valor para o usuário. Você não pode exibir esse valor depois de clicar em **OK**.
   
   ![Captura de tela de detalhes de toque](./media/how-to-authentication-temporary-access-pass/details.png)

## <a name="use-a-tap"></a>Usar um toque

O uso mais comum de um toque é para um usuário registrar detalhes de autenticação durante a primeira entrada, sem a necessidade de concluir prompts de segurança adicionais. Os métodos de autenticação são registrados em [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) . Os usuários também podem atualizar os métodos de autenticação existentes aqui.

1. Abra um navegador da Web para [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) .
1. Insira o UPN da conta para a qual você criou o toque, como *tapuser@contoso.com* .
1. Se o usuário estiver incluído na política de toque, ele verá uma tela para entrar em seu toque.
1. Insira o toque que foi exibido no portal do Azure.

   ![Captura de tela de como entrar em um toque](./media/how-to-authentication-temporary-access-pass/enter.png)

>[!NOTE]
>Para domínios federados, um toque é preferencial sobre a Federação. Um usuário com um toque concluirá a autenticação no Azure AD e não será redirecionado para o IdP (provedor de identidade federada).

O usuário agora está conectado e pode atualizar ou registrar um método como a chave de segurança FIDO2. Os usuários que atualizam seus métodos de autenticação devido à perda de suas credenciais ou dispositivo devem se certificar de remover os métodos de autenticação antigos.

Os usuários também podem usar seu toque para se registrar para entrar no telefone sem senha diretamente do aplicativo autenticador. Para obter mais informações, consulte [adicionar sua conta corporativa ou de estudante ao aplicativo Microsoft Authenticator](../user-help/user-help-auth-app-add-work-school-account.md).

![Captura de tela de como entrar em um toque usando a conta corporativa ou de estudante](./media/how-to-authentication-temporary-access-pass/enter-work-school.png)

## <a name="delete-a-tap"></a>Excluir um toque

Um toque expirado não pode ser usado. Nos **métodos de autenticação** para um usuário, a coluna de **detalhes** mostra quando o toque expirou. Você pode excluir esses toques expirados usando as seguintes etapas:

1. No portal do AD do Azure, navegue até **usuários**, selecione um usuário, como *toque em usuário* e, em seguida, escolha **métodos de autenticação**.
1. No lado direito do método de autenticação de **passagem de acesso temporário (versão prévia)** mostrado na lista, selecione **excluir**.

## <a name="replace-a-tap"></a>Substituir um toque 

- Um usuário pode ter apenas um toque. A senha pode ser usada durante a hora de início e de término do toque.
- Se o usuário precisar de um novo toque:
  - Se o toque existente for válido, o administrador precisará excluir o toque existente e criar uma nova passagem para o usuário. A exclusão de um toque válido revogará as sessões do usuário. 
  - Se o TAP existente tiver expirado, um novo toque substituirá o toque existente e não revogará as sessões do usuário.

Para obter mais informações sobre os padrões NIST para integração e recuperação, consulte [publicação especial do NIST 800-63A](https://pages.nist.gov/800-63-3/sp800-63a.html#sec4).

## <a name="limitations"></a>Limitações

Tenha essas limitações em mente:

- Ao usar um toque único para registrar um método sem senha, como FIDO2 ou entrada no telefone, o usuário deve concluir o registro dentro de 10 minutos de entrada com o toque único. Essa limitação não se aplica a um toque que pode ser usado mais de uma vez.
- Os usuários convidados não podem entrar com um toque.
- Os usuários no escopo da política de registro de redefinição de senha de autoatendimento (SSPR) serão necessários para registrar um dos métodos do SSPR depois que eles tiverem entrado com o TAP. Se o usuário usar apenas a chave FIDO2, exclua-os da política SSPR ou desabilite a política de registro SSPR. 
- O TAP não pode ser usado com a extensão NPS (servidor de políticas de rede) e o adaptador de Serviços de Federação do Active Directory (AD FS) (AD FS).
- Quando o SSO contínuo está habilitado no locatário, os usuários são solicitados a inserir uma senha. O link **usar seu passo de acesso temporário em vez disso** estará disponível para o usuário entrar com TAP.

![Captura de tela de usar um toque em vez disso](./media/how-to-authentication-temporary-access-pass/alternative.png)

## <a name="troubleshooting"></a>Solução de problemas    

- Se o TAP não for oferecido a um usuário durante a entrada, verifique o seguinte:
  - O usuário está no escopo da diretiva de método de autenticação TAP.
  - O usuário tem um toque válido e, se for um único uso, ele ainda não foi usado.
- Se a aprovação **da passagem de acesso temporário tiver sido bloqueada devido à política de credenciais do usuário** aparecer durante a entrada com o Tap, verifique o seguinte:
  - O usuário tem um toque com vários uso enquanto a política do método de autenticação requer um toque único.
  - Um toque único já foi usado.

## <a name="next-steps"></a>Próximas etapas

- [Planejar uma implantação de autenticação com senha no Azure Active Directory](howto-authentication-passwordless-deployment.md)


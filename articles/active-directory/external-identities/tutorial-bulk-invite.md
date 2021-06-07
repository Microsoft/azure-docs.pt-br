---
title: Tutorial para convidar usuários da Colaboração B2B em massa – Azure AD
description: Neste tutorial, você aprenderá a usar o PowerShell e um arquivo CSV para enviar convites em massa para usuários externos de colaboração do Azure AD B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b4e4892c01775b472cd8cdcf0f35b920d7e5e86
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055665"
---
# <a name="tutorial-bulk-invite-azure-ad-b2b-collaboration-users"></a>Tutorial: Convidar usuários de colaboração B2B do Azure AD em massa

Se você usar a colaboração B2B do Azure AD (Azure Active Directory) para trabalhar com parceiros externos, poderá convidar vários usuários convidados para a sua organização ao mesmo tempo. Neste tutorial, você aprenderá a usar o portal do Azure para enviar convites em massa para usuários externos. Especificamente, faça o seguinte:

> [!div class="checklist"]
> * Use **Convidar usuários em massa** para preparar um arquivo de valores separados por vírgula (.csv) com as informações do usuário e as preferências do convite
> * Fazer upload do arquivo .csv no Azure AD
> * Verifique se os usuários foram adicionados ao diretório

Caso não tenha o Azure Active Directory, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="understand-the-csv-template"></a>Entender o modelo CSV

Baixe e preencha o modelo CSV de carregamento em massa para ajudá-lo a convidar com êxito os usuários convidados do Azure AD em massa. O modelo CSV que você baixa pode ser semelhante a este exemplo:

![Planilha para upload e limites de chamada que explicam a finalidade e os valores de cada linha e coluna](media/tutorial-bulk-invite/understand-template.png)

### <a name="csv-template-structure"></a>Estrutura do modelo CSV

As linhas em um modelo CSV baixado são as seguintes:

- **Número de versão**: A primeira linha que contém o número de versão deve ser incluída no CSV de carregamento.
- **Cabeçalhos de coluna**: O formato dos cabeçalhos de coluna é &lt;*Nome do item*&gt; [PropertyName]&lt;*Obrigatório ou em branco*&gt;. Por exemplo, `Email address to invite [inviteeEmail] Required`. Algumas versões mais antigas do modelo podem ter pequenas variações.
- **Linha de exemplos**: incluímos uma linha de exemplos de valores para cada coluna no modelo. Você deve remover a linha de exemplos e substituí-la por suas entradas.

### <a name="additional-guidance"></a>Diretriz adicional

- As duas primeiras linhas do modelo de carregamento não devem ser removidas nem modificadas ou o carregamento não poderá ser processado.
- As colunas necessárias são listadas primeiro.
- Não recomendamos adicionar novas colunas ao modelo. Todas as colunas extras adicionadas são ignoradas e não são processadas.
- Recomendamos que você baixe a versão mais recente do modelo CSV sempre que possível.

## <a name="prerequisites"></a>Pré-requisitos

É necessário ter duas ou mais contas de email de teste para enviar o convite. As contas precisam estar fora da organização. É possível usar qualquer tipo de conta, incluindo contas sociais, como endereços gmail.com ou outlook.com.

## <a name="invite-guest-users-in-bulk"></a>Convidar usuários convidados em massa

1. Entre no portal do Azure com a conta de um administrador global da organização.
2. No painel de navegação, selecione **Azure Active Directory**.
3. Em **Gerenciar**, selecione **Todos os usuários**.
4. Selecione **Operações em massa** > **Convite em massa**.

    ![Botão de convite em massa](media/tutorial-bulk-invite/bulk-invite-button.png)

4. Na página **Convidar usuários em massa**, selecione **Baixar** para obter um modelo .csv válido com as propriedades do convite.

     ![Baixar o arquivo CSV](media/tutorial-bulk-invite/download-button.png)

1. Abra o modelo .csv e adicione uma linha para cada usuário convidado. Os valores obrigatórios são:

   * **Endereço de email a ser convidado** – o usuário que receberá um convite

   * **URL de redirecionamento** – a URL para a qual o usuário convidado é encaminhado depois de aceitar o convite. Caso queira encaminhar o usuário à página Meus Aplicativos, será necessário alterar esse valor para https://myapps.microsoft.com ou https://myapplications.microsoft.com.

    ![Exemplo de um arquivo CSV com os usuários convidados inseridos](media/tutorial-bulk-invite/bulk-invite-csv.png)

   > [!NOTE]
   > Não use vírgulas na **Mensagem de convite personalizada**, porque elas impedirão que a mensagem seja analisada com êxito.

6. Salve o arquivo.
7. Na página **Convidar usuários em massa**, em **Carregar o arquivo CSV**, procure o arquivo. Quando você selecionar o arquivo, a validação do arquivo .csv será iniciada. 
8. Quando o conteúdo do arquivo for validado, você verá a mensagem **Arquivo carregado com êxito**. Se houver erros, você precisará corrigi-los antes de enviar o trabalho.
9. Quando o arquivo passar na validação, selecione **Enviar** para iniciar a operação em massa do Azure que adiciona os convites. 
10. Para exibir o status do trabalho, selecione **Clique aqui para exibir o status de cada operação**. Se preferir, selecione **Resultados da operação em massa** na seção **Atividade**. Para obter detalhes sobre cada item de linha na operação em massa, selecione os valores nas colunas **Nº de Solicitações com Êxito**, **Nº de Solicitações com Falha** ou **Total de Solicitações**. Se ocorrerem falhas, os motivos da falha serão listados.

    ![Exemplo de resultados da operação em massa](media/tutorial-bulk-invite/bulk-operation-results.png)

11. Quando o trabalho for concluído, você verá uma notificação indicando que a operação em massa foi bem-sucedida.

## <a name="verify-guest-users-in-the-directory"></a>Verificar os usuários convidados no diretório

Verifique se os usuários convidados que você adicionou existem no diretório no portal do Azure ou usando o PowerShell.

### <a name="view-guest-users-in-the-azure-portal"></a>Exibir os usuários convidados no portal do Azure

1. Entre no portal do Azure com uma conta que seja um Administrador de usuários na organização.
2. No painel de navegação, selecione **Azure Active Directory**.
3. Em **Gerenciar**, selecione **Usuários**.
4. Em **Mostrar**, selecione **Somente usuários convidados** e verifique se os usuários adicionados estão listados.

### <a name="view-guest-users-with-powershell"></a>Exibir os usuários convidados com o PowerShell

Execute o comando a seguir:

```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```

Você deverá ver os usuários que você convidou listados, com um nome UPN no formato *endereçodeemail*#EXT#\@*domínio*. Por exemplo, *lstokes_fabrikam.com#EXT#\@contoso.onmicrosoft.com*, em que contoso.onmicrosoft.com é a organização da qual você enviou os convites.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando as contas de usuário de teste não forem mais necessárias, você poderá excluí-las do diretório no portal do Azure na página Usuários marcando a caixa de seleção ao lado do usuário convidado e, em seguida, selecionando **Excluir**. 

Se preferir, execute o seguinte comando do PowerShell para excluir uma conta de usuário:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```

Por exemplo: `Remove-AzureADUser -ObjectId "lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com"`

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você enviou convites em massa para usuários convidados fora da sua organização. Em seguida, saiba como o processo de resgate de convites funciona.

> [!div class="nextstepaction"]
> [Saiba mais sobre o processo de resgate de convite de colaboração do Azure AD B2B](redemption-experience.md)

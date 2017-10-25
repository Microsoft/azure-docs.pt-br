---
title: "Erro “Não é Possível Acessar este Aplicativo Corporativo” ao usar um aplicativo do Proxy de Aplicativo | Microsoft Docs"
description: Como resolver problemas comuns de acesso com aplicativos do Proxy de Aplicativo do Azure AD.
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 78ff8763a461162cbcfa04c6a86123973271928a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="cant-access-this-corporate-application-error-when-using-an-application-proxy-application"></a>Erro "Não é possível Acessar este Aplicativo Corporativo" ao usar um aplicativo de Proxy de Aplicativo

Este artigo irá ajudá-lo a solucionar problemas comuns enfrentados ao se deparar com um erro "Este aplicativo corporativo não pode ser acessado" em um aplicativo de Proxy de Aplicativo do Azure AD.

## <a name="overview"></a>Visão geral
Ao se deparar com esse erro, a página também comartilha um código de status. Esse código provavelmente é um dos seguintes:

-   **Tempo Limite de Gateway**: O serviço do Proxy da Aplicativo não consegue alcançar o conector. Isso normalmente indica um problema com a atribuição do conector, o próprio conector ou as regras de rede do conector.

-   **Gateway Incorreto**: O conector não consegue alcançar o aplicativo back-end. Isso pode indicar uma configuração incorreta do aplicativo.

-   **Proibido**: O usuário não está autorizado a acessar o aplicativo. Isso pode acontecer quando o usuário não está atribuído ao aplicativo no Azure Active Directory ou, se no back-end o usuário não tiver permissão para acessar o aplicativo.

Para localizar o código observe no texto, na parte inferior esquerda da mensagem de erro, o campo "Código de Status". Além disso, procure por notas na parte inferior da página com dicas adicionais.

   ![Erro de tempo limite de gateway](./media/application-proxy/connection-problem.png)

Para obter detalhes sobre como solucionar a causa raiz desses erros e obter mais detalhes sobre as correções sugeridas, consulte a seção correspondente abaixo.

## <a name="gateway-timeout-errors"></a>Erros de Tempo limite de gateway

Um tempo limite de gateway ocorre quando o serviço tenta alcançar o conector e não é possível dentro da janela de tempo limite. Isso geralmente é causado por um aplicativo atribuído a um Grupo de Conectores sem conectores funcionando ou, algumas portas exigidas pelo Conector não estão abertas.


## <a name="bad-gateway-errors"></a>Erros de gateway incorreto

Um erro de gateway incorreto indica que o conector não consegue alcançar o aplicativo back-end. certifique-se de que você publicou o aplicativo correto. Incorreções comuns que causam esse erro:

-   Um erro de digitação ou erro na URL interna

-   Não publicar a raiz do aplicativo. Por exemplo, publicar <http://expenses/reimbursement> mas tentar acessar <http://expenses>

-   Problemas com a configuração Delegação Restrita de Kerberos (KCD)

-   Problemas com o aplicativo back-end

## <a name="forbidden-errors"></a>Erros proibidos

Se você se deparar com um erro proibido, significa que o usuário não foi atribuído ao aplicativo. Isso pode ser no Azure Active Directory ou no aplicativo back-end.

Para saber como atribuir usuários ao aplicativo no Azure, consulte a [documentação de configuração](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal#add-a-test-user).

Se você confirmar que o usuário está atribuído ao aplicativo no Azure, verifique a configuração do usuário no aplicativo back-end. Se você estiver usando Delegação Restrita de Kerberos/Autenticação Integrada do Windows, será possível ver nossa página Solucionar problemas de KCD para obter algumas diretrizes.

## <a name="check-the-applications-internal-url"></a>Verificar a URL interna do aplicativo

Como uma primeira etapa rápida, verifique novamente e corrija a URL interna, abrindo o aplicativo pelo **Aplicativos Empresariais** e, em seguida, selecionando o menu **Proxy de Aplicativo**. Verifique se essa é a URL interna correta, a usada na sua rede local para acessar o aplicativo.

## <a name="check-the-application-is-assigned-to-a-working-connector-group"></a>Verificar se o aplicativo está atribuído a um Grupo de Conectores de trabalho

Para verificar se o aplicativo está atribuído a um Grupo de Conectores de trabalho:

1.  Abra o aplicativo no portal, acessando o **Azure Active Directory**, clicando em **Aplicativos Empresariais** e, em seguida, em **Todos os Aplicativos.** Abra o aplicativo e, em seguida, selecione **Proxy de aplicativo** no menu esquerdo.

2.  Analise o campo Grupo de Conectores. Se não houver conectores ativos no grupo, você verá um aviso. Se nenhuma aviso for exibido, vá para "verificar se todas as portas exigidas estão na lista de permissões".

3.  Se esse for o Grupo de Conectores errado, use o menu suspenso para selecionar o grupo correto e confirme se nenhum aviso é exibido. Se esse for o Grupo de Conectores pretendido, clique na mensagem de aviso para abrir a página com o gerenciamento do Conector.

4.  A partir daqui, há algumas maneiras de aprofundar-se ainda mais:

  * Mover um Conector ativo para o grupo: Se você tiver um Conector ativo que deveria pertencer a esse grupo e tiver linha de visão para o aplicativo back-end de destino, será possível mover o Conector para o grupo atribuído. Para fazer isso, clique no Conector. No campo "Grupo de Conectores", use a lista suspensa para selecionar o grupo correto e clique em Salvar.

  * Baixar um novo Conector para esse grupo: Nessa página, você pode obter o link para [baixar um novo Conector](https://download.msappproxy.net/Subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/Connector/Download). O Conector precisa ser instalado em uma máquina com linha de visão direta para o aplicativo back-end e ele geralmente é colocado no mesmo servidor que o aplicativo. Use o link de download do Connector para baixar de um conector no computador de destino. Em seguida, clique no Conector e use o menu suspenso "Grupo de Conectores" para certificar-se de que ele pertence ao grupo correto.

  * Investigar um conector inativo: Se um conector mostrar como inativo, ele não conseguirá alcançar o serviço. Isso geralmente é devido a algumas portas exigidas sendo bloqueadas. Para resolver esse problema, avance para "verificar se todas as portas exigidas estão na lista de permissões".

Após usar essas etapas para garantir que o aplicativo está atribuído a um grupo com Conectores funcionando, teste o aplicativo novamente. Se ainda não estiver funcionando, continue na próxima seção.

## <a name="check-all-required-ports-are-whitelisted"></a>Verifique se todas as portas exigidas estão na lista de permissões

Para verificar se todas as exigidas portas estão abertas, consulte nossa documentação sobre a abertura de portas. Se todas as portas exigidas estiver abertas, siga para a próxima seção.

## <a name="check-for-other-connector-errors"></a>Verifique se há outros Erros de Conectores

Se nenhuma das opções acima resolver o problema, a próxima etapa será procurar problemas ou erros do próprio conector. É possível ver alguns erros comuns no [Documento de solução de problemas](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot#connector-errors). 

Você também pode examinar diretamente os logs do Conector para identificar quaisquer erros. Muitas de nossas mensagens de erro podem compartilhar recomendações mais específicas para correções. Para saber como exibir os logs, consulte [nossa documentação de conectores](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors#under-the-hood).

## <a name="additional-resolutions"></a>Resoluções Adicionais

Se os itens acima não corrigirem o problema, há algumas causas possíveis diferentes. Para identificar o problema:

Se seu aplicativo é configurado para usar a Autenticação Integrada do Windows (IWA), teste o aplicativo sem logon único. Se não, avance para o próximo parágrafo. Para verificar o aplicativo sem logon único, abra o aplicativo pelo **Aplicativos Empresariais,** e vá para o menu **Logon Único**. Altere a lista suspensa de “Autenticação Integrada do Windows ” para “Logon único do Azure AD desabilitado”. 

Agora, abra um navegador e tente acessar o aplicativo novamente. Você deve ser solicitado a fazer autenticação e entrar no aplicativo. Se isso funcionar, significa que o problema é com a configuração de Delegação Restrita de Kerberos (KCD) que habilita o logon único. Consulte a página Solucionar Problemas de KCD.

Se você continuar vendo o erro, vá para o computador no qual o Conector está instalado, abra um navegador e tente alcançar a URL interna usada para o aplicativo. O Conector atua como outro cliente do mesmo computador. Se você não conseguir alcançar o aplicativo, investigue por que esse computador não consegue alcançar o aplicativo ou use um conector em um servidor que possa acessar o aplicativo.

Se você pode alcançar o aplicativo do computador, pesquise problemas ou erros com o próprio Conector. É possível ver alguns erros comuns no [Documento de solução de problemas](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot#connector-errors). Você também pode examinar diretamente os logs do Conector para identificar quaisquer erros. Muitas de nossas mensagens de erro podem compartilhar recomendações mais específicas para correções. Para saber como exibir os logs, consulte [nossa documentação de conectores](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors#under-the-hood).

## <a name="next-steps"></a>Próximas etapas
[Noções básicas sobre conectores de Proxy de Aplicativo do Azure AD](application-proxy-understand-connectors.md)

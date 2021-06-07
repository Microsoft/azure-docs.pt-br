---
title: Azure AD Connect e privacidade de usuário | Microsoft Docs
description: Este documento descreve como obter a conformidade GDPR com o Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f1d1bf35d03f0f7c4d11e4cc7e9b6f98f38d8ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95997691"
---
# <a name="user-privacy-and-azure-ad-connect"></a>Privacidade do usuário e Azure AD Connect 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Este artigo trata do Azure AD Connect e privacidade do usuário.  Para obter informações sobre o Azure AD Connect Health e a privacidade do usuário, consulte o artigo [aqui](reference-connect-health-user-privacy.md).

Melhore a privacidade do usuário para instalações do Azure AD Connect de duas maneiras:

1.  Mediante solicitação, extraia dados de uma pessoa e remova os dados dessa pessoa das instalações
2.  Certifique-se de que nenhum dado é retido além de 48 horas.

A equipe do Azure AD Connect recomenda a segunda opção, pois é muito mais fácil de implementar e manter.

Um servidor de sincronização do Azure AD Connect armazena os dados de privacidade de usuário a seguir:
1.  Dados sobre uma pessoa no **banco de dados do Azure AD Connect**
2.  Dados de arquivos de **log do Windows Event** que podem conter informações sobre uma pessoa
3.  Dados de **arquivos de log de instalação do Azure AD Connect** que podem conter sobre uma pessoa

Os clientes de conexão do Azure AD Connect devem seguir as seguintes orientações ao remover dados de usuário:
1.  Exclua o conteúdo da pasta que contém os arquivos de log de instalação do Azure AD Connect regularmente – pelo menos a cada 48 horas
2.  Este produto também pode criar Logs de Eventos.  Para saber mais sobre os logs de Logs de Eventos, consulte a [documentação aqui](/windows/win32/wes/windows-event-log).

Dados sobre uma pessoa serão removidos automaticamente do banco de dados do Azure AD Connect quando os dados dessa pessoa são removidos do sistema de origem onde ele foi originado. Nenhuma ação específica dos administradores é necessária para GDPR ser compatível.  No entanto, ele requer que os dados do Azure AD Connect estejam sincronizados com a fonte de dados, pelo menos a cada dois dias.

## <a name="delete-the-azure-ad-connect-installation-log-file-folder-contents"></a>Exclua o conteúdo de pasta de arquivo de log de instalação do Azure AD Connect
Verifique regularmente e exclua o conteúdo da pasta **c:\programdata\aadconnect** – exceto para o arquivo **PersistedState.Xml**. Esse arquivo mantém o estado da instalação anterior do Azure AD Connect e é usado quando uma instalação de atualização é executada. Esse arquivo não contém dados sobre uma pessoa e não deve ser excluído.

>[!IMPORTANT]
>Não exclua o arquivo PersistedState.xml.  Esse arquivo não contém nenhuma informação de usuário e mantém o estado da instalação anterior.

Você pode revisar e excluir esses arquivos usando o Windows Explorer, ou você pode usar um script semelhante ao seguinte para executar as ações necessárias:


```
$Files = ((Get-childitem -Path "$env:programdata\aadconnect" -Recurse).VersionInfo).FileName
Foreach ($file in $files) {
If ($File.ToUpper() -ne "$env:programdata\aadconnect\PERSISTEDSTATE.XML".toupper()) # Do not delete this file
    {Remove-Item -Path $File -Force}
    } 
```

### <a name="schedule-this-script-to-run-every-48-hours"></a>Agende esse script para executar a cada 48 horas
Use as etapas a seguir para agendar o script para executar a cada 48 horas.

1.  Salve o script em um arquivo com a extensão **&#46;PS1**, em seguida, abra o painel de controle e clique em **Sistemas e Segurança**.
    ![Sistema](./media/reference-connect-user-privacy/gdpr2.png)

2.  Sob o título de ferramentas administrativas, clique em **Agendar tarefas**.
    ![Tarefa](./media/reference-connect-user-privacy/gdpr3.png)
3.  No Agendador de Tarefas, clique com botão direito em **Biblioteca de agendamento de tarefas** e clique em **Criar tarefa básica...**
4.  Insira o nome para a nova tarefa e clique em **Avançar**.
5.  Selecione **Diariamente** para o acionador de tarefa e clique em **Avançar**.
6.  Defina a recorrência para **2 dias** e clique em **Avançar**.
7.  Selecione **Iniciar um programa** como a ação e clique em **Avançar**.
8.  Digite **PowerShell** na caixa para o programa/script e, na caixa rotulada **Adicionar argumentos (opcional)**, insira o caminho completo para o script que você criou anteriormente e clique em **Avançar**.
9.  A próxima tela mostra um resumo da tarefa que você está prestes a criar. Verifique os valores e clique em **Concluir** para criar a tarefa.



## <a name="next-steps"></a>Próximas etapas
* [Revise a Política de Privacidade da Microsoft na Central de Confiabilidade](https://www.microsoft.com/trustcenter)
* [Azure AD Connect Health e privacidade do usuário](reference-connect-health-user-privacy.md)
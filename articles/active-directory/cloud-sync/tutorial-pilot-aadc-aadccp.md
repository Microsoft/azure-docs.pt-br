---
title: Tutorial – Executar uma sincronização de nuvem piloto do Azure AD Connect para uma floresta do AD sincronizada existente
description: Saiba como executar uma sincronização de nuvem piloto para uma floresta de teste do Active Directory que já está sincronizada usando a sincronização do Azure AD (Azure Active Directory) Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5053bd0c3d63b13b1021476a09dca39dd08f581
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108750"
---
# <a name="pilot-cloud-sync-for-an-existing-synced-ad-forest"></a>Sincronização de nuvem piloto para uma floresta do AD sincronizada existente 

Este tutorial descreve como executar uma sincronização de nuvem piloto para uma floresta de teste do Active Directory que já está sincronizada usando a sincronização do Azure AD (Azure Active Directory) Connect.

![Criar](media/tutorial-migrate-aadc-aadccp/diagram-2.png)

## <a name="considerations"></a>Considerações
Antes de tentar este tutorial, considere os itens a seguir:
1. Verifique se você está familiarizado com as noções básicas da sincronização de nuvem. 
2. Verifique se você está executando a versão de sincronização 1.4.32.0 ou posterior do Azure AD Connect e se configurou as regras de sincronização conforme documentado. Ao executar o piloto, você removerá uma UO ou grupo de teste do escopo de sincronização do Azure AD Connect. Mover objetos para fora do escopo leva à exclusão desses objetos no Azure AD. No caso de objetos de usuário, os objetos no Azure AD são excluídos de forma reversível e podem ser restaurados. No caso de objetos de grupo, os objetos no Azure AD são excluídos e não podem ser restaurados. Um novo tipo de link foi introduzido na sincronização Azure AD Connect, o que impedirá a exclusão no caso de um cenário piloto. 
3. Verifique se os objetos no escopo do piloto têm o ms-ds-consistencyGUID preenchido para que a sincronização de nuvem corresponda aos objetos. 

   > [!NOTE]
   > A sincronização Azure AD Connect não preenche o *ms-ds-consistencyGUID* por padrão para objetos de grupo.

4. Este é um cenário avançado. Certifique-se de seguir as etapas documentadas neste tutorial com precisão.

## <a name="prerequisites"></a>Pré-requisitos
A seguir estão os pré-requisitos necessários para concluir este tutorial
- Um ambiente de teste com a versão de sincronização 1.4.32.0 ou posterior do Azure AD Connect
- Uma UO ou grupo que esteja no escopo da sincronização e que possa ser usado pelo piloto. Recomenda-se começar com um pequeno conjunto de objetos.
- Um servidor que executa o Windows Server 2012 R2 ou posterior que hospedará o agente de provisionamento.
- A âncora de origem para a sincronização do Azure AD Connect deve ser *objectGuid* ou *ms-ds-consistencyGUID*

## <a name="update-azure-ad-connect"></a>Atualizar o Azure AD Connect

No mínimo, você deve ter o [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.4.32.0. Para atualizar a sincronização do Azure AD Connect, conclua as etapas em [Azure AD Connect: Atualize para a versão mais recente](../hybrid/how-to-upgrade-previous-version.md).  

## <a name="stop-the-scheduler"></a>Parar o agendador
A sincronização Azure AD Connect sincroniza mudanças ocorridas em seu diretório local usando um agendador. Para modificar e adicionar regras personalizadas, você deseja desabilitar o agendador para que as sincronizações não sejam executadas enquanto você estiver trabalhando nele.  Use as seguintes etapas:

1.  No servidor que está executando a sincronização Azure AD Connect, abra o PowerShell com Privilégios Administrativos.
2.  Execute `Stop-ADSyncSyncCycle`.  Pressione Enter.
3.  Execute `Set-ADSyncScheduler -SyncCycleEnabled $false`.

>[!NOTE] 
>Se você estiver executando seu próprio agendador personalizado para a sincronização do Azure AD Connect, desabilite o agendador. 

## <a name="create-custom-user-inbound-rule"></a>Criar regra de entrada de usuário personalizada

 1. Inicie o editor de sincronização no menu de aplicativo na área de trabalho conforme mostrado abaixo:</br>
 ![Menu do Editor de Regras de Sincronização](media/tutorial-migrate-aadc-aadccp/user-8.png)</br>
 
 2. Selecione **De entrada** na lista suspensa para Direção e clique em **Adicionar nova regra**.
 ![Captura de tela que mostra a janela "Visualizar e gerenciar suas regras de sincronização" com a opção "Entrada" e o botão "Adicionar nova regra" selecionados.](media/tutorial-migrate-aadc-aadccp/user-1.png)</br>
 
 3. Na página **Descrição**, insira o seguinte e clique em **Avançar**:

    **Nome:** Dê um nome significativo à regra<br>
    **Descrição:** Adicionar uma descrição significativa<br>
    **Sistema Conectado:** Escolha o conector AD para o qual você está gravando a regra de sincronização personalizada<br>
    **Tipo de Objeto do Sistema Conectado:** Usuário<br>
    **Tipo de Objeto do Metaverso:** Person<br>
    **Tipo de Link:** Join<br>
    **Precedência:** Forneça um valor que seja exclusivo no sistema<br>
    **Tag:** Deixe esse campo vazio<br>
    ![Captura de tela que mostra a página "Criar regra de sincronização de entrada – Descrição" com os valores inseridos.](media/tutorial-migrate-aadc-aadccp/user-2.png)</br>
 
 4. Na página de **Filtro de escopo**, insira a UO ou o grupo de segurança em que você deseja que o piloto se baseie.  Para filtrar na UO, adicione a parte do nome diferenciado relativa à UO. Essa regra será aplicada a todos os usuários que estiverem nessa UO.  Portanto, se o DN terminar com "OU=CPUsers,DC=contoso,DC=com, você adicionará esse filtro.  Em seguida, clique em **Próximo**. 

    |Regra|Atributo|Operador|Valor|
    |-----|----|----|-----|
    |UO de escopo|DN|ENDSWITH|Nome diferenciado da UO.|
    |Grupo de escopo||ISMEMBEROF|Nome diferenciado do grupo de segurança.|

    ![Captura de tela que mostra a página "Criar regra de sincronização de entrada – Filtro de escopo" com um valor de filtro de escopo inserido.](media/tutorial-migrate-aadc-aadccp/user-3.png)</br>
 
 5. Na página **Regras de Junção**, clique em **Avançar**.
 6. Na página **Transformações**, adicione uma transformação Constante: flow True para o atributo cloudNoFlow. Clique em **Adicionar**.
 ![Captura de tela que mostra a página "Criar regra de sincronização de entrada – Transformações" com um fluxo "Transformação constante" adicionado.](media/tutorial-migrate-aadc-aadccp/user-4.png)</br>

As mesmas etapas precisam ser seguidas para todos os tipos de objeto (usuário, grupo e contato). Repita as etapas conforme o AD Connector configurado/por floresta do AD. 

## <a name="create-custom-user-outbound-rule"></a>Criar regra de saída de usuário personalizada

 1. Selecione **De saída** na lista suspensa para Direção e clique em **Adicionar regra**.
 ![Captura de tela que mostra a Direção "Saída" selecionada e o botão "Adicionar nova regra" em destaque.](media/tutorial-migrate-aadc-aadccp/user-5.png)</br>
 
 2. Na página **Descrição**, insira o seguinte e clique em **Avançar**:

    **Nome:** Dê um nome significativo à regra<br>
    **Descrição:** Adicionar uma descrição significativa<br>
    **Sistema Conectado:** escolha o conector Azure AD para o qual você está gravando a regra de sincronização personalizada<br>
    **Tipo de Objeto do Sistema Conectado:** Usuário<br>
    **Tipo de Objeto do Metaverso:** Person<br>
    **Tipo de Link:** JoinNoFlow<br>
    **Precedência:** Forneça um valor que seja exclusivo no sistema<br>
    **Tag:** Deixe esse campo vazio<br>
    
    ![Captura de tela que mostra a página "Descrição" com propriedades inseridas.](media/tutorial-migrate-aadc-aadccp/user-6.png)</br>
 
 3. Na página de **Filtro de escopo**, escolha **cloudNoFlow** igual a **True**. Em seguida, clique em **Próximo**.
 ![Regra personalizada](media/tutorial-migrate-aadc-aadccp/user-7.png)</br>
 
 4. Na página **Regras de Junção**, clique em **Avançar**.
 5. Na página de **Transformações**, clique em **Adicionar**.

As mesmas etapas precisam ser seguidas para todos os tipos de objeto (usuário, grupo e contato).

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>Instalar o agente de provisionamento do Azure AD Connect
1. Entre no servidor que você usará com as permissões de admin corporativo.  Se você estiver usando o tutorial [Ambiente básico do AD e Azure](tutorial-basic-ad-azure.md), será o CP1.
2. Baixe o agente de provisionamento de nuvem do Azure AD Connect usando as etapas descritas [aqui](how-to-install.md#install-the-agent).
3. Executar a sincronização de nuvem do Azure AD Connect (AADConnectProvisioningAgent.Installer)
3. Na tela inicial, **Aceite** os termos de licenciamento e clique em **Instalar**.</br>
![Captura de tela que mostra a tela inicial "Agente de Provisionamento do Microsoft Azure AD Connect".](media/how-to-install/install-1.png)</br>

4. Quando essa operação for concluída, o assistente de configuração será iniciado.  Entre com sua conta de administrador global do Azure AD.
5. Na tela **Conectar Active Directory**, clique em **Adicionar diretório** e, em seguida, entre com sua conta de administrador do Active Directory.  Esta operação adicionará o diretório local.  Clique em **Próximo**.</br>
![Captura de tela que mostra a tela "Conectar Active Directory" com um valor de diretório inserido.](media/how-to-install/install-3a.png)</br>

6. Na tela **Configuração completa**, clique em **Confirmar**.  Esta operação registrará e reiniciará o agente.</br>
![Captura de tela que mostra a tela "Configuração concluída" com o botão "Confirmar" selecionado.](media/how-to-install/install-4a.png)</br>

7. Quando essa operação for concluída, você deverá ver um aviso indicando que **O seu foi verificado com êxito.**  Você pode clicar em **Sair**.</br>
![Tela de boas-vindas](media/how-to-install/install-5.png)</br>
8. Se ainda vir a tela inicial, clique em **Fechar**.

## <a name="verify-agent-installation"></a>Verificar a instalação do agente
A verificação do agente ocorre no portal do Azure e no servidor local que está executando o agente.

### <a name="azure-portal-agent-verification"></a>Verificação do agente de portal do Azure
Para verificar se o agente está sendo visto pelo Azure, siga estas etapas:

1. Entre no portal do Azure.
2. À esquerda, selecione **Azure Active Directory**, clique em **Azure AD Connect** e, no centro, selecione **Gerenciar sincronização de nuvem**.</br>
![Azure portal](media/how-to-install/install-6.png)</br>

3.  Na tela **Sincronização de nuvem do Azure AD Connect**, clique em **Examinar todos os agentes**.
![Provisionamento do Azure AD](media/how-to-install/install-7.png)</br>
 
4. Na **tela Agentes de provisionamento locais** você verá os agentes que instalou.  Verifique se o agente em questão está lá e se está marcado como **Desabilitado**.  O agente está desabilitado por padrão ![Agentes de provisionamento](media/how-to-install/verify-1.png)</br>

### <a name="on-the-local-server"></a>No servidor local
Para verificar se o agente está em execução, siga estas etapas:

1.  Faça logon no servidor com uma conta de administrador
2.  Abra **Serviços** navegando até ele ou acessando Iniciar/Executar/Services.msc.
3.  Em **Serviços** certifique-se de que o **Atualizador do Agente do Microsoft Azure AD Connect** e o **Agente de Provisionamento do Microsoft Azure AD Connect** estão lá e se o status é **Em execução**.
![Serviços](media/how-to-install/troubleshoot-1.png)

## <a name="configure-azure-ad-connect-cloud-sync"></a>Configurar a sincronização de nuvem do Azure AD Connect
Use as seguintes etapas para configurar o provisionamento:

 1. Entre no portal do Azure AD.
 2. Clique em **Azure Active Directory**
 3. Clique em **Azure AD Connect**
 4. Selecione **Gerenciar sincronização de nuvem**
 ![Captura de tela mostrando o link "Gerenciar sincronização de nuvem".](media/how-to-configure/manage-1.png)</br>
 5.  Clique em **Nova Configuração**
 ![Captura de tela da sincronização de nuvem do Azure AD Connect com o link "Nova configuração" realçado.](media/tutorial-single-forest/configure-1.png)</br>
 6.  Na tela configuração, insira um **Email de notificação**, mova o seletor para **Habilitar** e clique em **Salvar**.
 ![Captura de tela da tela Configurar com email de notificação preenchido e Habilitar selecionado.](media/tutorial-single-forest/configure-2.png)</br>
 7. Em **Configurar**, selecione **Todos os usuários** para alterar o escopo da regra de configuração.
 ![Captura de tela referente à tela Configurar com "Todos os usuários" realçado ao lado de "Usuários do escopo".](media/how-to-configure/scope-2.png)</br>
 8. À direita, altere o escopo para incluir a UO específica que você acabou de criar "OU=CPUsers,DC=contoso,DC=com".
 ![Captura de tela referente à tela Usuários do escopo realçando o escopo alterado para a unidade organizacional que você criou.](media/tutorial-existing-forest/scope-2.png)</br>
 9.  Clique em **Feito** e **Salvar**.
 10. O escopo agora deve ser definido como uma unidade organizacional. 
 ![Captura de tela referente à tela Configurar com "1 unidade organizacional" realçada ao lado de "Usuários do escopo".](media/tutorial-existing-forest/scope-3.png)</br>
 

## <a name="verify-users-are-provisioned-by-cloud-sync"></a>Verificar se os usuários são provisionados pela sincronização de nuvem
Agora, você verificará se os usuários que você tinha em nosso diretório local foram sincronizados e se existem no locatário do Azure AD.  Esteja ciente de que isso pode levar algumas horas para ser concluído.  Para verificar se os usuários são provisionados pela sincronização de nuvem, siga estas etapas:

1. Navegue até o [portal do Azure](https://portal.azure.com) e entre com uma conta que tenha uma assinatura do Azure.
2. Na esquerda, selecione **Azure Active Directory**
3. Clique em **Azure AD Connect**
4. Clique em **Gerenciar sincronização de nuvem**
5. Clique no botão **Logs**
6. Procure um nome de usuário para confirmar se o usuário foi provisionado pela sincronização de nuvem

Além disso, você pode verificar se o usuário e o grupo existem no Azure AD.

## <a name="start-the-scheduler"></a>Iniciar o agendador
A sincronização Azure AD Connect sincroniza mudanças ocorridas em seu diretório local usando um agendador. Agora que você modificou as regras, você pode reiniciar o agendador.  Use as seguintes etapas:

1.  No servidor que está executando a sincronização do Azure AD Connect, abra o PowerShell com Privilégios Administrativos
2.  Execute `Set-ADSyncScheduler -SyncCycleEnabled $true`.
3.  Execute `Start-ADSyncSyncCycle`.  Pressione Enter.  

>[!NOTE] 
>Se você estiver executando seu próprio agendador personalizado para a sincronização do Azure AD Connect, habilite o agendador. 

Depois que o agendador for habilitado, o Azure AD Connect interromperá a exportação das alterações em objetos com `cloudNoFlow=true` no metaverso, a menos que qualquer atributo de referência (por exemplo, gerente) esteja sendo atualizado. Caso haja alguma atualização do atributo de referência no objeto, o Azure AD Connect vai ignorar o sinal de `cloudNoFlow` e exportará todas as atualizações no objeto.

## <a name="something-went-wrong"></a>Algo deu errado
Caso o piloto não funcione conforme o esperado, você poderá voltar para a configuração de sincronização Azure AD Connect seguindo as etapas abaixo:
1.  Desabilite a configuração de provisionamento no portal do Azure. 
2.  Desabilite todas as regras de sincronização personalizadas criadas para o Provisionamento de Nuvem usando a ferramenta Editor de Regras de Sincronização. A desabilitação deve causar sincronização completa em todos os conectores.



## <a name="next-steps"></a>Próximas etapas 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é a sincronização de nuvem do Azure AD Connect?](what-is-cloud-sync.md)


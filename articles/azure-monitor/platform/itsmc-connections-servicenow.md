---
title: Conectar o ServiceNow ao Conector de Gerenciamento de Serviços de TI
description: Saiba como conectar o ServiceNow com o Conector de Gerenciamento de Serviços de TI (ITSMC) em Azure Monitor para monitorar e gerenciar de forma centralizada os itens de trabalho de ITSM.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 222257f5045984a71c2aee9de83b5fa420306728
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99573408"
---
# <a name="connect-servicenow-with-it-service-management-connector"></a>Conectar o ServiceNow ao Conector de Gerenciamento de Serviços de TI

Este artigo mostra como configurar a conexão entre uma instância do ServiceNow e o Conector de Gerenciamento de Serviços de TI (ITSMC) no Log Analytics, para que você possa gerenciar centralmente seus itens de trabalho do gerenciamento de serviços de ti (ITSM).

## <a name="prerequisites"></a>Pré-requisitos
Verifique se você atende aos seguintes pré-requisitos para a conexão.

### <a name="itsmc-installation"></a>Instalação do ITSMC

Para obter informações sobre como instalar o ITSMC, consulte [Adicionar a solução de conector de gerenciamento de serviços de ti](./itsmc-definition.md#add-it-service-management-connector).

> [!NOTE]
> O ITSMC dá suporte apenas à oferta oficial de SaaS (software como serviço) do ServiceNow. Não há suporte para implantações privadas do ServiceNow.

### <a name="oauth-setup"></a>Instalação do OAuth

As versões com suporte do ServiceNow incluem Paris, Orlando, Nova York, Madri, Londres, Kingston, Jacarta, Istambul, Helsinque e Geneva.

Os administradores do ServiceNow devem gerar uma ID do cliente e um segredo do cliente para sua instância do ServiceNow. Consulte as seguintes informações conforme necessário:

- [Configurar o OAuth para Paris](https://docs.servicenow.com/bundle/paris-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Configurar o OAuth para Orlando](https://docs.servicenow.com/bundle/orlando-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Configurar o OAuth para Nova Iorque](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Configurar o OAuth para Madri](https://docs.servicenow.com/bundle/madrid-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Configurar o OAuth para Genebra](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Configurar o OAuth para Kingston](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Configurar o OAuth para Jacarta](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Configurar o OAuth para Istambul](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Configurar o OAuth para Helsinque](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Configurar o OAuth para Geneva](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)

Como parte da configuração do OAuth, recomendamos:

1. [Crie um ponto de extremidade para os clientes acessarem a instância](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_CreateEndpointforExternalClients.html).

1. Atualize o ciclo de vida do token de atualização:

   1. No painel **ServiceNow** , pesquise **System OAuth** e, em seguida, selecione **registro do aplicativo**. 
   1. Selecione o nome do OAuth que foi definido e altere a **vida útil do token de atualização** para **7.776.000 segundos** (90 dias). 
   1. Selecione **Atualizar**. 

1. Estabeleça um procedimento interno para garantir que a conexão permaneça ativa. Alguns dias antes da expiração esperada da vida útil do token de atualização, execute as seguintes operações:

   1. [Conclua um processo de sincronização manual para a configuração do conector de ITSM](./itsmc-resync-servicenow.md).

   1. Revogar para o token de atualização antigo. Não recomendamos manter chaves antigas por motivos de segurança. 
   
      1. No painel **ServiceNow** , pesquise **System OAuth** e, em seguida, selecione **gerenciar tokens**. 
      
      1. Selecione o token antigo na lista de acordo com o nome OAuth e a data de validade.

         ![Captura de tela que mostra uma lista de tokens para OAuth.](media/itsmc-connections/snow-system-oauth.png)
      1. Selecione **revogar**  >  **revogação** de acesso.

## <a name="install-the-user-app-and-create-the-user-role"></a>Instalar o aplicativo de usuário e criar a função de usuário

Use o procedimento a seguir para instalar o aplicativo de usuário do serviço agora e criar a função de usuário de integração para ele. Você usará essas credenciais para fazer a conexão do ServiceNow no Azure.

> [!NOTE]
> O ITSMC dá suporte apenas ao aplicativo de usuário oficial para a integração do Microsoft Log Analytics que é baixada da loja ServiceNow. O ITSMC não oferece suporte à ingestão de código no lado do ServiceNow ou a qualquer aplicativo que não faça parte da solução oficial do ServiceNow. 

1. Visite a [loja do servicenow](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) e instale o **aplicativo de usuário para a integração do Servicenow e do Microsoft OMS** em sua instância do servicenow.
   
   >[!NOTE]
   >Como parte da transição contínua do Microsoft Operations Management Suite (OMS) para Azure Monitor, o OMS agora é chamado de Log Analytics.     
2. Após a instalação, vá para a barra de navegação à esquerda da instância do ServiceNow e, em seguida, pesquise e selecione **Microsoft OMS Integrator**.  
3. Selecione a **lista de verificação de instalação**.

   O status é exibido como  **não concluído** porque a função de usuário ainda não foi criada.

4. Na caixa de texto ao lado de **criar usuário de integração**, insira o nome do usuário que pode se conectar ao ITSMC no Azure.
5. Insira a senha para esse usuário e, em seguida, selecione **OK**.  

O usuário recém-criado é exibido com as funções padrão atribuídas:

- personalize_choices
- import_transformer
- x_mioms_microsoft.user
- itil
- template_editor
- view_changer

Depois de criar o usuário com êxito, o status da **lista de verificação de instalação de verificação** é movido para **concluído** e lista os detalhes da função de usuário criada para o aplicativo.

> [!NOTE]
> O ITSMC pode enviar incidentes para o ServiceNow sem nenhum outro módulo instalado em sua instância do ServiceNow. Se você estiver usando o módulo EventManagement em sua instância do ServiceNow e quiser criar eventos ou alertas no ServiceNow usando o conector, adicione as seguintes funções ao usuário de integração:
> 
> - evt_mgmt_integration
> - evt_mgmt_operator  

## <a name="create-a-connection"></a>Criar uma conexão
Use o procedimento a seguir para criar uma conexão do ServiceNow.

> [!NOTE]
> Os alertas enviados do Azure Monitor podem criar um dos seguintes elementos no ServiceNow: eventos, incidentes ou alertas. 

1. Em portal do Azure, vá para **todos os recursos** e procure **YourWorkspaceName)**.

2. Em **fontes de dados do espaço de trabalho**, selecione **conexões de ITSM**.

   ![Captura de tela que mostra a seleção de uma fonte de dados.](media/itsmc-connections/add-new-itsm-connection.png)

3. Na parte superior do painel direito, selecione **Adicionar**.

4. Forneça as informações conforme descrito na tabela a seguir e selecione **OK**.

   | **Campo** | **Descrição** |
   | --- | --- |
   | **Nome da conexão**   | Insira um nome para a instância do ServiceNow que você deseja conectar com ITSMC. Você usará esse nome posteriormente em Log Analytics ao configurar itens de trabalho de ITSM e exibir análises detalhadas. |
   | **Tipo de parceiro**   | Selecione **ServiceNow**. |
   | **URL do servidor**   | Insira a URL da instância do ServiceNow que você deseja conectar ao ITSMC. A URL deve apontar para uma versão de SaaS com suporte com o sufixo *. servicenow.com* (por exemplo `https://XXXXX.service-now.com/` ).|
   | **Nome de usuário**   | Insira o nome de usuário de integração que você criou no aplicativo ServiceNow para dar suporte à conexão com o ITSMC.|
   | **Senha**   | Insira a senha associada a este nome de usuário. **Observação**: o nome de usuário e a senha são usados para gerar somente tokens de autenticação. Eles não são armazenados em nenhum lugar no serviço ITSMC.  |
   | **ID do cliente**   | Insira a ID do cliente que você deseja usar para autenticação OAuth2, que você gerou anteriormente. Para obter mais informações sobre como gerar uma ID do cliente e um segredo, consulte [Configurar o OAuth](https://old.wiki/index.php/OAuth_Setup). |
   | **Segredo do Cliente**   | Insira o segredo do cliente gerado para essa ID.   |
   | **Escopo de sincronização de dados (em dias)** | Insira o número de dias anteriores dos quais você deseja os dados. O limite é de 120 dias. |
   | **Itens de trabalho a serem sincronizados**   | Selecione os itens de trabalho do ServiceNow que você deseja sincronizar com o Azure Log Analytics, por meio de ITSMC. Os valores selecionados são importados para Log Analytics. As opções são incidentes e solicitações de alteração.|
   | **Criar novo item de configuração no produto de ITSM** | Selecione esta opção se você deseja criar os itens de configuração no produto ITSM. Quando selecionado, o ITSMC cria itens de configuração (se nenhum existir) no sistema de ITSM com suporte. Isso está desabilitado por padrão. |

![Captura de tela de caixas e opções para adicionar uma conexão do ServiceNow.](media/itsmc-connections/itsm-connection-servicenow-connection-latest.png)

Quando você estiver conectado e sincronizado com êxito:

- Os itens de trabalho selecionados da instância do ServiceNow são importados para o Log Analytics. Você pode exibir o resumo desses itens de trabalho no bloco **conector de gerenciamento de serviços de ti** .

- Você pode criar incidentes de Log Analytics alertas ou registros de log ou de alertas do Azure nesta instância do ServiceNow.

> [!NOTE]
> O ServiceNow tem um limite de taxa para solicitações por hora. Para configurar o limite, defina a **limitação da taxa da API REST de entrada** na instância do ServiceNow.

## <a name="next-steps"></a>Próximas etapas

* [Visão geral de Conector ITSM](itsmc-overview.md)
* [Criar itens de trabalho de ITSM desde alertas do Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Solucionar problemas no Conector ITSM](./itsmc-resync-servicenow.md)

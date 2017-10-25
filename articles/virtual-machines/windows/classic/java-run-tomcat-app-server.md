---
title: "Executar o servidor de aplicativos Java em uma VM clássica do Azure | Microsoft Docs"
description: "Este tutorial usa os recursos criados com o modelo de implantação clássico e mostra como criar uma Máquina virtual do Windows e configurá-la para executar o servidor de aplicativos do Apache Tomcat."
services: virtual-machines-windows
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: d627aa09-f7d6-4239-8110-f8fc5111b939
ms.service: virtual-machines-windows
ms.workload: web
ms.tgt_pltfrm: vm-windows
ms.devlang: Java
ms.topic: article
ms.date: 03/16/2017
ms.author: robmcm
ms.openlocfilehash: 6e02f42613808bcb13c0057e9f8fcc1c02273e77
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-run-a-java-application-server-on-a-virtual-machine-created-with-the-classic-deployment-model"></a>Como executar um servidor de aplicativos do Java em uma máquina virtual criada com o modelo de implantação clássico
> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../../../resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação Clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos. Para um modelo do Resource Manager para implantar um aplicativo Web com o Java 8 e o Tomcat, veja [aqui](https://azure.microsoft.com/documentation/templates/201-web-app-java-tomcat/).

Com o Azure, você pode usar uma máquina virtual para fornecer recursos de servidor. Como um exemplo, uma máquina virtual em execução no Azure pode ser configurada para hospedar um servidor de aplicativos Java, como o Apache Tomcat.

Depois de concluir este guia, você entenderá como criar uma máquina virtual em execução no Azure e configurá-la para executar um servidor de aplicativos Java. Você aprenderá e executará as seguintes tarefas:

* Como criar uma máquina virtual que já tenha um JDK (Java Development Kit) instalado.
* Entre remotamente na máquina virtual.
* Como instalar um servidor de aplicativos Java – Apache Tomcat – na máquina virtual.
* Criar um ponto de extremidade para a máquina virtual.
* Abrir uma porta no firewall para o servidor de aplicativos.

A instalação completa resulta na execução do Tomcat em uma máquina virtual.

![Máquina virtual executando o Apache Tomcat][virtual_machine_tomcat]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>Para criar uma máquina virtual
1. Entre no [Portal do Azure](https://portal.azure.com).  
2. Clique em **Novo**, em **Computação** e, em seguida, em **Ver todos** nos **Aplicativos em destaque**.
3. Clique em **JDK** e em **JDK 8** no painel **JDK**.  
   Imagens de máquina virtual que dão suporte ao **JDK 6** e ao **JDK 7** estão disponíveis se você tem aplicativos herdados que não estão prontos para serem executados no JDK 8.
4. No painel do JDK 8, selecione **Clássico** e, em seguida, clique em **Criar**.
5. Na folha **Básico**:
   1. Especifique um nome para a máquina virtual.
   2. Digite um nome para o administrador no campo **Nome do Usuário** . Memorize esse nome e a senha associada mostrada no próximo campo. Você precisará deles quando entrar remotamente na máquina virtual.
   3. Insira uma senha no campo **Nova senha** e insira-a novamente no campo **Confirmar senha**. Essa senha refere-se à conta de Administrador.
   4. Selecione a **Assinatura** apropriada.
   5. Para o **Grupo de recursos**, clique em **Criar novo** e insira o nome de um novo grupo de recursos. Se preferir, clique em **Usar existente** e selecione um dos grupos de recursos disponíveis.
   6. Selecione uma localização em que a máquina virtual reside, como **Centro-Sul dos EUA**.
6. Clique em **Avançar**.
7. Na folha **Tamanho da imagem de máquina virtual**, selecione **A1 Padrão** ou outra imagem apropriada.
8. Clique em **Selecionar**.

9. Na folha **Configurações**, clique em **OK**. É possível usar os valores padrão fornecidos pelo Azure.  
10. Na folha **Resumo**, clique em **OK**.

## <a name="to-remotely-sign-in-to-your-virtual-machine"></a>Para entrar remotamente na máquina virtual
1. Faça logon no [portal do Azure](https://portal.azure.com).
2. Clique em **Máquinas virtuais (clássicas)**. Se necessário, clique em **Mais serviços** no canto inferior esquerdo nas categorias de serviços. A entrada **Máquinas virtuais (clássicas)** é listada no grupo **Computação**.
3. Clique no nome da Máquina Virtual na qual você deseja entrar.
4. Depois que a máquina virtual for iniciada, um menu na parte superior do painel permitirá conexões.
5. Clique em **Conectar**.
6. Responda às solicitações conforme necessário para se conectar à máquina virtual. Normalmente, você salva ou abre o arquivo .rdp que contém os detalhes de conexão. Pode ser necessário copiar url:port como último parte da primeira linha do arquivo .rdp e colar essa informações em um aplicativo de entrada remoto.

## <a name="to-install-a-java-application-server-on-your-virtual-machine"></a>Para instalar um servidor de aplicativos Java em sua máquina virtual
Você pode copiar um servidor de aplicativos Java em sua máquina virtual ou instalar um servidor de aplicativos Java por meio de um instalador.

Este tutorial usa o Tomcat como o servidor de aplicativos Java a ser instalado.

1. Depois de se conectar à máquina virtual, abra uma sessão do navegador para [Apache Tomcat](http://tomcat.apache.org/download-80.cgi).
2. Clique duas vezes no link **Instalador de serviço do Windows de 32 bits/64 bits**. Ao usar essa técnica, o Tomcat é instalado como um serviço Windows.
3. Quando solicitado, opte por executar o instalador.
4. No assistente de **Configuração do Apache Tomcat** , siga os prompts para instalar o Tomcat. Para o objetivo deste tutorial, aceitar os padrões será o suficiente. Quando você chegar à caixa de diálogo **Concluindo o Assistente de Instalação do Apache Tomcat**, poderá marcar opcionalmente **Executar o Apache Tomcat** para que o Tomcat inicie agora. Clique em **Concluir** para concluir o processo de configuração do Tomcat.

## <a name="to-start-tomcat"></a>Para iniciar o Tomcat

É possível iniciar o Tomcat manualmente abrindo um prompt de comando na máquina virtual e executando o comando **net&nbsp;start&nbsp;Tomcat8**.

Quando o Tomcat estiver em execução, você poderá acessá-lo inserindo a URL <http://localhost:8080> no navegador da máquina virtual.

Para ver o Tomcat em execução em máquinas externas, você precisará criar um ponto de extremidade e abrir uma porta.

## <a name="to-create-an-endpoint-for-your-virtual-machine"></a>Para criar um ponto de extremidade para sua máquina virtual
1. Entre no [Portal do Azure](https://portal.azure.com).
2. Clique em **Máquinas virtuais (clássicas)**.
3. Clique no nome da máquina virtual que está executando o servidor de aplicativos Java.
4. Clique em **Pontos de Extremidade**.
5. Clique em **Adicionar**.
6. Na caixa de diálogo **Adicionar ponto de extremidade**:
   1. Especifique um nome para o ponto de extremidade. Por exemplo, **HttpIn**.
   2. Selecione **TCP** como o protocolo.
   3. Especifique **80** para a porta pública.
   4. Especifique **8080** para a porta privada.
   5. Selecione **Desabilitado** para o endereço IP flutuante.
   6. Deixe a lista de controle de acesso como está.
   7. Clique no botão **OK** para fechar a caixa de diálogo e criar o ponto de extremidade.

## <a name="to-open-a-port-in-the-firewall-for-your-virtual-machine"></a>Para abrir uma porta no firewall para sua máquina virtual
1. Entre na máquina virtual.
2. Clique em **Iniciar do Windows**.
3. Clique em **Painel de Controle**.
4. Clique em **Sistema e Segurança**, clique em **Firewall do Windows** e, em seguida, clique em **Configurações Avançadas**.
5. Clique em **Regras de Entrada** e, em seguida, clique em **Nova Regra**.  
   ![Nova regra de entrada][NewIBRule]
6. Para o **Tipo de Regra**, selecione **Porta** e clique em **Próximo**.  
   ![Nova porta de regra de entrada][NewRulePort]
7. Na tela **Protocolo e Portas**, selecione **TCP**, especifique **8080** como a **Porta local específica** e clique em **Próximo**.  
  ![Nova regra de entrada][NewRuleProtocol]
8. Na tela **Ação**, selecione **Permitir a conexão** e clique em **Próximo**.
   ![Nova ação de regra de entrada][NewRuleAction]
9. Na tela **Perfil**, verifique se **Domínio**, **Privado** e **Público** estão marcados e clique em **Próximo**.
   ![Novo perfil de regra de entrada][NewRuleProfile]
10. Na tela **Nome**, especifique um nome para a regra, como **HttpIn** (no entanto, o nome da regra não precisa corresponder ao nome do ponto de extremidade) e clique em **Concluir**.  
    ![Nome da nova regra de entrada][NewRuleName]

Neste ponto, o site do Tomcat deverá estar visível em um navegador externo. Na janela de endereço do navegador, digite uma URL no formato **http://*your\_DNS\_name*.cloudapp.net**, em que ***your\_DNS\_name*** é o nome DNS especificado quando a máquina virtual foi criada.

## <a name="application-lifecycle-considerations"></a>Considerações sobre o ciclo de vida do aplicativo
* Você pode criar o próprio arquivo web do aplicativo (WAR) e adicioná-lo à pasta **webapps** . Por exemplo, crie um projeto Web dinâmico JSP (Página de Serviço Java) básico e exporte-o como um arquivo WAR. Em seguida, copie o WAR para a pasta **webapps** do Apache Tomcat na máquina virtual e, em seguida, execute-o em um navegador.
* Por padrão, quando o serviço Tomcat for instalado, ele será definido para iniciar manualmente. Você pode mudá-lo para iniciar automaticamente, usando o snap-in Serviços. Inicie o snap-in Serviços clicando em **Iniciar do Windows**, **Ferramentas Administrativas** e em **Serviços**. Clique duas vezes no serviço **Apache Tomcat** e defina **Tipo de inicialização** como **Automático**.

    ![Configurando um serviço para iniciar automaticamente][service_automatic_startup]

    A vantagem de fazer com que o Tomcat seja iniciado automaticamente é que ele começa a ser executado quando a máquina virtual é reinicializada (por exemplo, após a instalação das atualizações de software que exigem uma reinicialização).

## <a name="next-steps"></a>Próximas etapas
É possível saber mais sobre outros serviços (como o Armazenamento do Azure, barramento de serviço e Banco de Dados SQL) que talvez você deseje incluir nos aplicativos Java. Exibir as informações disponíveis no [Central de desenvolvedores do Java](https://azure.microsoft.com/develop/java/).

[virtual_machine_tomcat]:media/java-run-tomcat-app-server/WA_VirtualMachineRunningApacheTomcat.png

[service_automatic_startup]:media/java-run-tomcat-app-server/WA_TomcatServiceAutomaticStart.png









[NewIBRule]:media/java-run-tomcat-app-server/NewInboundRule.png
[NewRulePort]:media/java-run-tomcat-app-server/NewRulePort.png
[NewRuleProtocol]:media/java-run-tomcat-app-server/NewRuleProtocol.png
[NewRuleAction]:media/java-run-tomcat-app-server/NewRuleAction.png
[NewRuleName]:media/java-run-tomcat-app-server/NewRuleName.png
[NewRuleProfile]:media/java-run-tomcat-app-server/NewRuleProfile.png


<!-- Deleted from the "To create an ednpoint for your virtual mache" 3/17/2017,
     to use the new portal.
6. In the **Add endpoint** dialog box, ensure **Add standalone endpoint** is selected, and then click **Next**.
7. In the **New endpoint details** dialog box:
-->

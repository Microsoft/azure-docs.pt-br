---
title: Criar um servidor Jenkins no Azure
description: "Instale o Jenkins em uma máquina virtual Linux do Azure no modelo de solução Jenkins e crie um aplicativo Java de exemplo."
author: mlearned
manager: douge
ms.service: multiple
ms.workload: web
ms.devlang: java
ms.topic: hero-article
ms.date: 08/21/2017
ms.author: mlearned
ms.custom: Jenkins
ms.openlocfilehash: 7bb74f297d52fb25171817175cce64187b397c38
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-jenkins-server-on-an-azure-linux-vm-from-the-azure-portal"></a>Criar um servidor Jenkins em uma VM Linux do Azure no portal do Azure

Este guia de início rápido mostra como instalar o [Jenkins](https://jenkins.io) em uma VM Linux Ubuntu com as ferramentas e os plug-ins configurados para funcionar com o Azure. Ao concluir, você terá um servidor Jenkins em execução no Azure criando um aplicativo Java de exemplo do [GitHub](https://github.com).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure
* Acesso a SSH na linha de comando do computador (como o shell Bash ou o [PuTTY](http://www.putty.org/))

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-jenkins-vm-from-the-solution-template"></a>Criar a VM Jenkins do modelo de solução

Abra a [imagem do marketplace para o Jenkins](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview) no seu navegador da Web e selecione **OBTER AGORA** no lado esquerdo da página. Examine os detalhes de preços e selecione **Continuar**. Em seguida, selecione **Criar** para configurar o servidor Jenkins no portal do Azure. 
   
![Diálogo do portal do Azure](./media/install-jenkins-solution-template/ap-create.png)

Na guia **Definir as configurações básicas**, preencha os campos abaixo:

![Definir as configurações básicas](./media/install-jenkins-solution-template/ap-basic.png)

* Use **Jenkins** como **Nome**.
* Insira um **nome de usuário**. O nome de usuário deve atender aos [requisitos específicos](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm).
* Selecione **Senha** como **Tipo de autenticação** e digite uma senha. A senha deve conter um caractere em maiúscula, um número e um caractere especial.
* Use **myJenkinsResourceGroup** como **Grupo de Recursos**.
* Escolha o **Leste dos EUA** como [região do Azure](https://azure.microsoft.com/regions/) na lista suspensa **Localização**.

Selecione **OK** para prosseguir para a guia **Configurar opções adicionais**. Insira um nome de domínio exclusivo para identificar o servidor Jenkins e selecione **OK**.

![Configurar opções adicionais](./media/install-jenkins-solution-template/ap-addtional.png)  

 Depois de validado, selecione **OK** novamente na guia **Resumo**. Por fim, selecione **Comprar** para criar a VM Jenkins. Quando o servidor estiver pronto, você receberá uma notificação no portal do Azure:   

![Notificação Jenkins está pronto](./media/install-jenkins-solution-template/jenkins-deploy-notification-ready.png)

## <a name="connect-to-jenkins"></a>Conectar-se ao Jenkins

Navegue até sua máquina virtual (por exemplo, http://jenkins2517454.eastus.cloudapp.azure.com/) no navegador da Web. O console do Jenkins não pode ser acessado por meio de HTTP não segura e, portanto, instruções são fornecidas na página para acessar o console do Jenkins com segurança do seu computador usando um túnel SSH.

![Desbloquear Jenkins](./media/install-jenkins-solution-template/jenkins-ssh-instructions.png)

Configure o túnel usando o comando `ssh` na página a partir da linha de comando, substituindo `username` pelo nome do usuário administrador da máquina virtual escolhido anteriormente durante a configuração da máquina virtual no modelo de solução.

```bash
ssh -L 127.0.0.1:8080:localhost:8080 jenkinsadmin@jenkins2517454.eastus.cloudapp.azure.com
```

Depois de iniciar o túnel, navegue para http://localhost:8080/ em seu computador local. 

Obtenha a senha inicial executando o comando abaixo na linha de comando, enquanto estiver conectado à VM Jenkins por meio do SSH.

```bash
`sudo cat /var/lib/jenkins/secrets/initialAdminPassword`.
```

Desbloqueie o painel Jenkins pela primeira vez usando essa senha inicial.

![Desbloquear Jenkins](./media/install-jenkins-solution-template/jenkins-unlock.png)

Selecione **Instalar plug-ins sugeridos** na próxima página e crie um usuário administrador do Jenkins usado para acessar o painel do Jenkins.

![O Jenkins está pronto!](./media/install-jenkins-solution-template/jenkins-welcome.png)

O servidor Jenkins agora está pronto para compilar código.

## <a name="create-your-first-job"></a>Criar seu primeiro trabalho

Selecione **Criar novos trabalhos** no console do Jenkins, nomeie-o **mySampleApp** e selecione **projeto Freestyle**. Em seguida, selecione **OK**.

![Criar um novo trabalho](./media/install-jenkins-solution-template/jenkins-new-job.png) 

Selecione a guia **Gerenciamento de código-fonte**, habilite o **Git**e digite a seguinte URL no campo **URL do Repositório**:`https://github.com/spring-guides/gs-spring-boot.git`

![Definir o repositório Git](./media/install-jenkins-solution-template/jenkins-job-git-configuration.png) 

Selecione a guia **Build** e selecione **Adicionar etapa de compilação**, **Invocar script Gradle**. Selecione **Usar Wrapper Gradle**, digite `complete` na **localização do Wrapper** e `build` como **Tarefas**.

![Use o wrapper Gradle para compilar](./media/install-jenkins-solution-template/jenkins-job-gradle-config.png) 

Selecione **Avançado** e digite `complete` no campo **Script da Compilação da Raiz**. Selecione **Salvar**.

![Definir as configurações avançadas na etapa de compilação do wrapper Gradle](./media/install-jenkins-solution-template/jenkins-job-gradle-advances.png) 

## <a name="build-the-code"></a>Compilar o código

Selecione **Criar agora** para compilar o código e empacotar o aplicativo de exemplo. Quando a compilação for concluída, selecione o link **Espaço de Trabalho** para o projeto.

![Navegue até o espaço de trabalho para obter o arquivo JAR da compilação](./media/install-jenkins-solution-template/jenkins-access-workspace.png) 

Navegue até `complete/build/libs` e verifique se o `gs-spring-boot-0.1.0.jar` está disponível a fim de verificar se a compilação foi bem-sucedida. O servidor Jenkins agora está pronto para criar seus próprios projetos no Azure.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adicionar VMs do Azure como agentes do Jenkins](jenkins-azure-vm-agents.md)

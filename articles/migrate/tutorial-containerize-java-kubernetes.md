---
title: Conteinerização de aplicativo Azure em Java; Conteinerização e migração de aplicativos Web em Java para o Kubernetes do Azure.
description: 'Tutorial: Colocar em contêiner e migrar aplicativos Web em Java para o Serviço de Kubernetes do Azure.'
services: ''
author: rahugup
manager: bsiva
ms.topic: tutorial
ms.date: 3/2/2021
ms.author: rahugup
ms.openlocfilehash: 2c1a0ee78e866a12105eca77653b1063943d06db
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561059"
---
# <a name="java-web-app-containerization-and-migration-to-azure-kubernetes-service"></a>Conteinerização e migração de aplicativo Web Java para o Serviço de Kubernetes do Azure

Neste artigo, você aprenderá a conteinerizar aplicativos Web Java (em execução no Apache Tomcat) e migrá-los para o [AKS (Serviço de Kubernetes do Azure)](https://azure.microsoft.com/services/kubernetes-service/) usando a ferramenta Migrações para Azure: Conteinerização de Aplicativo. O processo de conteinerização não exige acesso à sua base de código e oferece uma maneira fácil de conteinerizar aplicativos existentes. A ferramenta funciona com o uso do estado de execução dos aplicativos em um servidor a fim de determinar os componentes do aplicativo e ajuda você a empacotá-los em uma imagem de contêiner. O aplicativo em contêineres pode, então, ser implantado no AKS (Serviço de Kubernetes do Azure).

A ferramenta Migrações para Azure: Conteinerização de Aplicativo atualmente dá suporte a –

- Como conteinerizar aplicativos ASP.NET e implantá-los em contêineres do Windows no AKS. [Saiba mais](./tutorial-containerize-aspnet-kubernetes.md)
- Como conteinerizar aplicativos Web Java no Apache Tomcat e implantá-los em contêineres do Linux no AKS.


A ferramenta Migrações para Azure: Conteinerização de Aplicativo ajuda você a –

- **Descobrir seu aplicativo**: a ferramenta se conecta remotamente aos servidores de aplicativo que executam seu aplicativo Web Java (executado no Apache Tomcat) e descobre os componentes do aplicativo. A ferramenta cria um Dockerfile que pode ser usado a fim de criar uma imagem de contêiner para o aplicativo.
- **Criar a imagem de contêiner**: você pode inspecionar e personalizar ainda mais o Dockerfile de acordo com seus requisitos de aplicativo e usá-lo para criar sua imagem de contêiner de aplicativo. A imagem de contêiner do aplicativo é enviada por push para o Registro de Contêiner do Azure que você especificar.
- **Implantar no Serviço de Kubernetes do Azure**: a ferramenta, em seguida, gera os arquivos YAML de definição de recurso do Kubernetes necessários para implantar o aplicativo conteinerizado em seu cluster do Serviço de Kubernetes do Azure. Você pode personalizar os arquivos YAML e usá-los para implantar o aplicativo no AKS.

> [!NOTE]
> A ferramenta Migrações para Azure: Conteinerização de Aplicativo ajuda a descobrir tipos de aplicativos específicos (aplicativos Web ASP.NET e Java no Apache Tomcat) e seus componentes em um servidor de aplicativos. Para descobrir servidores e o inventário de aplicativos, funções e recursos em execução em computadores locais, use o recurso Migrações para Azure: Descoberta e a capacidade de avaliação. [Saiba mais](./tutorial-discover-vmware.md)

Embora nem todos os aplicativos se beneficiem de uma mudança direta para contêineres sem uma rearquitetura significativa, alguns dos benefícios de mover aplicativos existentes para contêineres sem reprogramação incluem:

- **Utilização de infraestrutura aprimorada:** com contêineres, vários aplicativos podem compartilhar recursos e ser hospedados na mesma infraestrutura. Isso pode ajudar você a consolidar a infraestrutura e a melhorar a utilização.
- **Gerenciamento simplificado:** ao hospedar seus aplicativos em uma plataforma de infraestrutura gerenciada moderna como o AKS, você poderá simplificar suas práticas de gerenciamento, mas mantendo o controle sobre sua infraestrutura. Você pode conseguir isso desativando ou reduzindo os processos de manutenção e gerenciamento de infraestrutura que eram tradicionalmente executados com a infraestrutura própria.
- **Portabilidade de aplicativos:** Com o aumento da adoção e a padronização dos formatos de especificação de contêiner e das plataformas de orquestração, a portabilidade de aplicativos não é mais um problema.
- **Adotar gerenciamento moderno com o DevOps** ajuda você a adotar e usar práticas modernas de gerenciamento e segurança como padrão com Infraestrutura como código e transição para o DevOps.


Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar uma conta do Azure.
> * Instale a ferramenta Migrações para Azure: Conteinerização de Aplicativo.
> * Descubra seu aplicativo Web Java.
> * Compile a imagem de contêiner.
> * Implantar o aplicativo conteinerizado no AKS.

> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário para que você possa configurar rapidamente uma prova de conceito. Os tutoriais usam opções padrão quando possível e não mostram todas as configurações e todos os caminhos possíveis.

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, você deverá:

**Requisito** | **Detalhes**
--- | ---
**Identificar um computador no qual instalar a ferramenta** | Um computador Windows para instalar e executar a ferramenta Migrações para Azure: Conteinerização de Aplicativo. O computador Windows pode ser um sistema operacional de servidor (Windows Server 2016 ou posterior) ou de cliente (Windows 10), o que significa que a ferramenta também pode ser executada na área de trabalho. <br/><br/> O computador Windows que executa a ferramenta deve ter conectividade de rede com os servidores/as máquinas virtuais que hospedam os aplicativos Web Java que serão conteinerizados.<br/><br/> Verifique se o espaço de 6 GB está disponível no computador Windows que executa a ferramenta Migrações para Azure: Conteinerização de Aplicativo para o armazenamento de artefatos do aplicativo. <br/><br/> O computador Windows deve ter acesso à Internet, diretamente ou por meio de um proxy.
**Servidores de aplicativos** | – Habilite a conexão SSH (Secure Shell) na porta 22 dos servidores que executam os aplicativos Java a serem conteinerizados. <br/>
**Aplicativo web Java** | A ferramenta atualmente dá suporte a: <br/><br/> - Aplicativos em execução no Tomcat 8 ou versão posterior.<br/> - Servidores de aplicativos no Ubuntu Linux 16.04/18.04/20.04, Debian 7/8, CentOS 6/7, Red Hat Enterprise Linux 5/6/7. <br/> - Aplicativos que usam o Java versão 7 ou posterior.  <br/><br/> A ferramenta atualmente não dá suporte a: <br/><br/> - Servidores de aplicativos que executam várias instâncias do Tomcat <br/>  


## <a name="prepare-an-azure-user-account"></a>Preparar uma conta de usuário do Azure

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

Quando sua assinatura estiver configurada, você precisará de uma conta de usuário do Azure com:
- Permissões de Proprietário na assinatura do Azure
- Permissões para registrar aplicativos do Azure Active Directory

Se você acaba de criar uma conta gratuita do Azure, você é o proprietário da assinatura. Se você não for o proprietário da assinatura, trabalhe com o proprietário para atribuir as permissões da seguinte maneira:

1. No portal do Azure, pesquise por "assinaturas" e, em **Serviços**, selecione **Assinaturas**.

    ![Caixa de pesquisa para pesquisar a assinatura do Azure.](./media/tutorial-discover-vmware/search-subscription.png)

2. Na página **Assinaturas**, selecione a assinatura na qual você deseja criar um projeto das Migrações para Azure.
3. Na assinatura, selecione **Controle de acesso (IAM)**  > **Verificar o acesso**.
4. Em **Verificar o acesso**, procure a conta de usuário relevante.
5. Em **Adicionar uma atribuição de função**, clique em **Adicionar**.

    ![Pesquisar uma conta de usuário para verificar o acesso e atribuir uma função.](./media/tutorial-discover-vmware/azure-account-access.png)

6. Em **Adicionar atribuição de função**, selecione a função Proprietário e selecione a conta (azmigrateuser em nosso exemplo). Em seguida, clique em **Salvar**.

    ![Abre a página Adicionar atribuição de função para atribuir uma função à conta.](./media/tutorial-discover-vmware/assign-role.png)

7. Sua conta do Azure também precisa de **permissões para registrar aplicativos do Azure Active Directory.**
8.  No portal do Azure, acesse **Azure Active Directory** > **Usuários** > **Configurações de Usuário**.
9.  Em **Configurações de usuário**, verifique se os usuários do Azure AD podem registrar aplicativos (definido como **Sim** por padrão).

      ![Verifique em Configurações de Usuário se os usuários podem registrar aplicativos do Active Directory.](./media/tutorial-discover-vmware/register-apps.png)

10.  Caso as configurações de 'Registros de aplicativo' estejam definidas como 'Não', solicite ao administrador global/de locatários a atribuição da permissão necessária. Como alternativa, o administrador global/de locatários pode atribuir a função **Desenvolvedor de aplicativos** a uma conta para permitir o registro de aplicativo do Azure Active Directory. [Saiba mais](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="download-and-install-azure-migrate-app-containerization-tool"></a>Baixar e instalar a ferramenta Migrações para Azure: Conteinerização de Aplicativo

1. [Baixe](https://go.microsoft.com/fwlink/?linkid=2134571) a ferramenta Migrações para Azure: Conteinerização de Aplicativo em um computador Windows.
2. Inicie o PowerShell no modo de administrador e altere o diretório do PowerShell para a pasta que contém o instalador.
3. Execute o script de instalação usando o comando

   ```powershell
   .\AppContainerizationInstaller.ps1
   ```

## <a name="launch-the-app-containerization-tool"></a>Iniciar a ferramenta Conteinerização de Aplicativo

1. Abra um navegador em algum computador que possa se conectar ao computador Windows que executa a ferramenta Conteinerização de Aplicativo: **https://*nome do computador ou endereço IP*: 44368**.

   Como alternativa, você pode abrir o aplicativo na área de trabalho selecionando o atalho do aplicativo.

2. Se você vir um aviso informando que sua conexão não é privada, clique em Avançado e escolha prosseguir para o site. Esse aviso é exibido porque a interface Web usa um certificado TLS/SSL autoassinado.
3. Na tela de credenciais, use a conta de administrador local no computador para entrar.
4. Para especificar o tipo de aplicativo, selecione **Aplicativos Web Java no Tomcat** como o tipo de aplicativo que você deseja conteinerizar.

    ![Carga padrão para a ferramenta Conteinerização de Aplicativo.](./media/tutorial-containerize-apps-aks/tool-home.png)


### <a name="complete-tool-pre-requisites"></a>Pré-requisitos da ferramenta completa
1. Aceite os **termos de licença** e leia as informações de terceiros.
6. No aplicativo Web da ferramenta > **Configurar pré-requisitos**, siga estas etapas:
   - **Conectividade**: a ferramenta verifica se o computador Windows tem acesso à Internet. Se o computador usar um proxy:
     - Clique em **Configurar proxy** para especificar o endereço de proxy (como endereço IP ou FQDN) e a porta de escuta.
     - Especifique as credenciais caso o proxy exija autenticação.
     - Há suporte apenas para o proxy HTTP.
     - Se você tiver adicionado detalhes de proxy ou desabilitado o proxy e/ou a autenticação, clique em **Salvar** para disparar a verificação de conectividade novamente.
   - **Instalar atualizações**: a ferramenta verificará automaticamente as últimas atualizações e as instalará. Você também pode instalar manualmente a versão mais recente da ferramenta [aqui](https://go.microsoft.com/fwlink/?linkid=2134571).
   - **Habilitar o SSH (Secure Shell)** : a ferramenta perguntará se o SSH (Secure Shell) está habilitado nos servidores de aplicativo que estão executando os aplicativos Web Java que serão conteinerizados.


## <a name="login-to-azure"></a>Logon no Azure

Clique em **Logon** para fazer logon na sua conta do Azure.

1. Será necessário um código do dispositivo para a autenticação com o Azure. Um clique em Fazer logon abrirá uma janela restrita com o código do dispositivo.
2. Clique em **Copiar código e Fazer logon** para copiar o código do dispositivo e abrir um prompt de logon do Azure em uma nova guia do navegador. Se essa opção não for exibida, verifique se você desabilitou o bloqueador de pop-ups no navegador.

    ![Modal que mostra o código do dispositivo.](./media/tutorial-containerize-apps-aks/login-modal.png)

3. Na nova guia, cole o código do dispositivo e conclua o logon usando as credenciais da sua conta do Azure. Você pode fechar a guia do navegador após o logon ser concluído e retornar à interface Web da ferramenta Conteinerização de Aplicativo.
4. Selecione o **locatário do Azure** que deseja usar.
5. Escolha a **assinatura do Azure** que você deseja usar.

## <a name="discover-java-web-applications"></a>Descobrir aplicativos Web Java

A ferramenta auxiliar Conteinerização de Aplicativo conecta-se remotamente aos servidores de aplicativos usando as credenciais fornecidas e tenta descobrir aplicativos Web Java (em execução no Apache Tomcat) hospedados nos servidores de aplicativos.

1. Especifique o **endereço IP/FQDN e as credenciais**  do servidor que executa o aplicativo Web Java a serem usadas na conexão remota do servidor para fins de descoberta de aplicativo.
    - As credenciais fornecidas precisam ser para uma conta raiz (Linux) no servidor de aplicativos.
    - No caso de contas de domínio (o usuário precisa ser um administrador no servidor de aplicativo), coloque o nome de domínio como prefixo do nome de usuário no formato *<domínio\nome de usuário>* .
    - Você pode executar a descoberta de aplicativos em até cinco servidores de cada vez.

2. Clique em **Validar** para verificar se o servidor de aplicativo pode ser acessado pelo computador que executa a ferramenta e se as credenciais são válidas. Após o sucesso da validação, a coluna de status mostrará o status como **Mapeado**.  

    ![Captura de tela do IP e das credenciais do servidor.](./media/tutorial-containerize-apps-aks/discovery-credentials.png)

3. Clique em **Continuar** para iniciar a descoberta do aplicativo nos servidores de aplicativo selecionados.

4. Após a conclusão bem-sucedida da descoberta de aplicativos, você pode selecionar a lista de aplicativos a serem conteinerizados.

    ![Captura de tela do aplicativo Web Java descoberto.](./media/tutorial-containerize-apps-aks/discovered-app.png)


4. Use a caixa de seleção para marcar os aplicativos que serão conteinerizados.
5. **Especificar nome do contêiner**: especifique um nome para o contêiner de destino de cada aplicativo selecionado. O nome do contêiner deve ser especificado como <*nome:marca*> em que a marca é usada para a imagem de contêiner. Por exemplo, você pode especificar o nome do contêiner de destino como *nomedoaplicativo:v1*.   

### <a name="parameterize-application-configurations"></a>Parametrizar as configurações do aplicativo
A parametrização da configuração torna-a disponível como um parâmetro de tempo de implantação. Isso permite que você defina essa configuração enquanto implanta o aplicativo em vez de tê-lo embutido como um valor específico em código na imagem de contêiner. Por exemplo, essa opção é útil para parâmetros como cadeias de conexão de banco de dados.
1. Clique em **configuração de aplicativo** para examinar as configurações detectadas.
2. Marque a caixa de seleção para parametrizar as configurações do aplicativo detectado.
3. Clique em **Aplicar** depois de selecionar as configurações a serem parametrizadas.

   ![Captura de tela do aplicativo ASP.NET de parametrização da configuração do aplicativo.](./media/tutorial-containerize-apps-aks/discovered-app-configs.png)

### <a name="externalize-file-system-dependencies"></a>Externalizar dependências do sistema de arquivos

 Você pode adicionar outras pastas usadas pelo seu aplicativo. Especifique se elas devem fazer parte da imagem de contêiner ou se serão externalizadas por meio de volumes persistentes no compartilhamento de arquivos do Azure. O uso de volumes persistentes funciona muito bem com aplicativos com estado que armazenam o estado fora do contêiner ou têm outro conteúdo estático armazenado no sistema de arquivos. [Saiba mais](../aks/concepts-storage.md)

1. Clique em **Editar** em Pastas do Aplicativo para examinar as pastas do aplicativo detectadas. As pastas do aplicativo detectadas foram identificadas como artefatos obrigatórios exigidos pelo aplicativo e serão copiadas na imagem de contêiner.

2. Clique em **Adicionar pastas** e especifique os caminhos de pasta a serem adicionados.
3. Para adicionar várias pastas ao mesmo volume, informe valores separados por vírgulas (`,`).
4. Selecione **Volume Persistente** como opção de armazenamento se você quiser que as pastas sejam armazenadas fora do contêiner em um Volume Persistente.
5. Clique em **Salvar** depois de examinar as pastas do aplicativo.
   ![Captura de tela da escolha do armazenamento de volumes do aplicativo.](./media/tutorial-containerize-apps-aks/discovered-app-volumes.png)

6. Clique em **Continuar** para prosseguir até a fase de criação da imagem de contêiner.


## <a name="build-container-image"></a>Criar imagem de contêiner


1. **Selecionar o Registro de Contêiner do Azure**: use o menu suspenso para selecionar o [Registro de Contêiner do Azure](../container-registry/index.yml) que será usado para criar e armazenar as imagens de contêiner dos aplicativos. Você pode usar um Registro de Contêiner do Azure existente ou optar por criar outro usando a opção Criar registro.

    ![Captura de tela da escolha do ACR para o aplicativo.](./media/tutorial-containerize-apps-aks/build-java-app.png)


2. **Examinar o Dockerfile**: o Dockerfile necessário para criar as imagens de contêiner para cada aplicativo selecionado é gerado no início da etapa de compilação. Clique em **Examinar** para examinar o Dockerfile. Você também pode adicionar todas as personalizações necessárias ao Dockerfile na etapa de revisão e salvar as alterações antes de iniciar o processo de compilação.

3. **Processo de compilação do gatilho**: selecione os aplicativos para os quais as imagens serão criadas e clique em **Compilar**. Clicar em Compilar iniciará a compilação da imagem de contêiner para cada aplicativo. A ferramenta continua monitorando o status da compilação continuamente e permite que você prossiga para a próxima etapa após a conclusão bem-sucedida da compilação.

4. **Acompanhar o status da compilação**: você também pode monitorar o progresso da etapa de compilação clicando no link **Compilação em andamento** na coluna de status. O link leva alguns minutos para ficar ativo depois que o processo de compilação é disparado.  

5. Depois de concluída a compilação, clique em **Continuar** para especificar as configurações de implantação.

    ![Captura de tela da conclusão da compilação da imagem de contêiner do aplicativo.](./media/tutorial-containerize-apps-aks/build-java-app-completed.png)

## <a name="deploy-the-containerized-app-on-aks"></a>Implantar o aplicativo conteinerizado no AKS

Depois que a imagem de contêiner é criada, a próxima etapa é implantar o aplicativo como contêiner no [AKS (Serviço de Kubernetes do Azure)](https://azure.microsoft.com/services/kubernetes-service/).

1. **Selecionar o cluster do Serviço de Kubernetes do Azure**: especifique o cluster AKS no qual o aplicativo deve ser implantado.

     - O cluster AKS selecionado precisa ter um pool de nós do Linux.
     - O cluster precisa ser configurado para permitir o pull de imagens do Registro de Contêiner do Azure que foi selecionado para armazenar as imagens.
         - Execute o comando a seguir na CLI do Azure para anexar o cluster AKS ao ACR.
           ``` Azure CLI
           az aks update -n <cluster-name> -g <cluster-resource-group> --attach-acr <acr-name>
           ```  
     - Se você não tem um cluster AKS ou gostaria de criar outro cluster AKS para a implantação do aplicativo, pode criá-lo na ferramenta clicando em **Criar cluster AKS**.      
          - O cluster AKS criado usando a ferramenta será criado com um pool de nós do Linux. O cluster será configurado para permitir o pull de imagens do Registro de Contêiner do Azure que foi criado anteriormente (se a opção Criar registro tiver sido escolhida).
     - Clicar em **Continuar** depois de escolher o cluster AKS.

2. **Especificar o compartilhamento de arquivo do Azure**: se você adicionou mais pastas e selecionou a opção Volume Persistente, especifique o compartilhamento de arquivo que deveria ser usado pela ferramenta Migrações para Azure: Conteinerização de Aplicativo durante o processo de implantação. A ferramenta criará novos diretórios nesse compartilhamento de arquivo do Azure para copiar as pastas de aplicativo que estão configuradas para o armazenamento de Volume Persistente. Depois que a implantação do aplicativo for concluída, a ferramenta limpará o compartilhamento de arquivo do Azure com a exclusão dos diretórios que ela criou.

     - Se você não tem um compartilhamento de arquivo do Azure ou gostaria de criar outro compartilhamento de arquivo do Azure, pode escolher criá-lo na ferramenta clicando em **Criar Conta de Armazenamento e compartilhamento de arquivo**.  

3. **Configuração da implantação do aplicativo**: depois de concluir as etapas acima, você precisará especificar a configuração de implantação do aplicativo. Clique em **Configurar** para personalizar a implantação do aplicativo. Na etapa de configuração, você pode oferecer as seguintes personalizações:
     - **Cadeia de caracteres de prefixo**: especifique uma cadeia de caracteres de prefixo para usar no nome de todos os recursos criados para o aplicativo conteinerizado no cluster AKS.
     - **Conjunto de Réplicas**: especifique o número de instâncias de aplicativo (pods) que deve ser executado dentro dos contêineres.
     - **Tipo de balanceador de carga**: selecione *Externo* se o aplicativo conteinerizado tiver que ser alcançado em redes públicas.
     - **Configuração do aplicativo**: em caso de configurações de aplicativo que tenham sido parametrizadas, informe os valores a serem usados na implantação atual.
     - **Armazenamento**: no caso de pastas do aplicativo que tenham sido configuradas para armazenamento de Volume Persistente, especifique se o volume deve ser compartilhado entre instâncias do aplicativo ou inicializados individualmente a cada instância do contêiner. Por padrão, todas as pastas de aplicativo em Volumes Persistentes são configuradas como compartilhadas.
     - Clique em **Aplicar** para salvar a configuração de implantação.
     - Clique em **Continuar** para implantar o aplicativo.

    ![Captura de tela da configuração do aplicativo de implantação.](./media/tutorial-containerize-apps-aks/deploy-java-app-config.png)

4. **Implantar o aplicativo**: depois que a configuração de implantação do aplicativo for salva, a ferramenta gerará o YAML de implantação do Kubernetes para o aplicativo.
     - Clique em **Editar** a fim de examinar e personalizar o YAML de implantação do Kubernetes para os aplicativos.
     - Selecione o aplicativo a ser implantado.
     - Clique em **Implantar** para iniciar as implantações dos aplicativos selecionados.

         ![Captura de tela da configuração de implantação do aplicativo.](./media/tutorial-containerize-apps-aks/deploy-java-app-deploy.png)

     - Quando o aplicativo é implantado, você pode clicar na coluna *Status de implantação* para rastrear os recursos que foram implantados para o aplicativo.

## <a name="download-generated-artifacts"></a>Baixar artefatos gerados

Todos os artefatos usados para criar e implantar o aplicativo no AKS, incluindo os arquivos de especificação YAML do Dockerfile e do Kubernetes, são armazenados no computador que executa a ferramenta. Os artefatos estão localizados em *C:\ProgramData\Microsoft Azure Migrate App Containerization*.

Uma única pasta é criada para cada servidor de aplicativos. Você poderá exibir e baixar todos os artefatos intermediários usados no processo de conteinerização ao navegar até essa pasta. A pasta, correspondente ao servidor de aplicativos, será limpa no início de cada execução da ferramenta em um servidor específico.

## <a name="troubleshoot-issues"></a>Solucionar problemas

Para solucionar problemas com a ferramenta, você poderá examinar os arquivos de log no computador com Windows que executa a ferramenta Conteinerização de Aplicativo. Os arquivos de log da ferramenta estão localizados na pasta *C:\ProgramData\Microsoft Azure Migrate App Containerization\Logs*.

## <a name="next-steps"></a>Próximas etapas

- Como conteinerizar aplicativos ASP.NET e implantá-los em contêineres do Windows no AKS. [Saiba mais](./tutorial-containerize-aspnet-kubernetes.md)
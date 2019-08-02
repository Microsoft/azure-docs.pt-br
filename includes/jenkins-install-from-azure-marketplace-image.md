---
description: Arquivo de inclusão
author: tomarcher
manager: rloutlaw
ms.service: multiple
ms.workload: web
ms.devlang: na
ms.topic: include
ms.date: 03/12/2018
ms.author: tarcher
ms.custom: Jenkins
ms.openlocfilehash: 5439de30b02b0ce05853c8112f9e29239743ef98
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172542"
---
1. No navegador, abra a [imagem do Azure Marketplace para Jenkins](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview).

1. Selecione **OBTENHA AGORA**.

    ![Selecione OBTENHA AGORA para iniciar o processo de instalação para a imagem do Jenkins Marketplace.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-get-it-now.png)

1. Depois de consultar as informações de termos e detalhes de preços, selecione **Continuar**.

    ![Informações de termos e preços da imagem do Jenkins Marketplace.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-pricing-and-terms.png)

1. Selecione **Criar** para configurar o servidor de Jenkins no portal do Azure. 

    ![Instale a imagem do Jenkins Marketplace.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-create.png)

1. Na guia **Noções Básicas**, especifique os seguintes valores:

   - **Nome** - Digite `Jenkins`.
   - **Nome de usuário** - Insira o nome de usuário a ser usado ao entrar na máquina virtual que está executando Jenkins. O nome de usuário deve atender aos [requisitos específicos](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm).
   - **Tipo de autenticação** - Selecione **Chave pública SSH**.
   - **Chave pública SSH** - Copie e cole uma chave pública RSA no formato de linha única (iniciando com `ssh-rsa`) ou no formato PEM de multilinhas. É possível gerar chaves SSH usando ssh-keygen no Linux e macOS ou PuTTYGen no Windows. Para obter mais informações sobre chaves SSH e Azure, consulte o artigo [Como usar chaves SSH com o Windows no Azure](/azure/virtual-machines/linux/ssh-from-windows).
   - **Assinatura** - Selecione a assinatura do Azure que você deseja para instalar o Jenkins.
   - **Grupo de recursos** - Selecione **Criar novo** e digite um nome para o grupo de recursos que serve como um contêiner lógico para a coleção de recursos que compõem sua instalação Jenkins.
   - **Local** - Selecione **Leste dos EUA**.

     ![Insira informações de grupo de recursos e autenticação para Jenkins na guia Básico.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-basic.png)

1. Selecione **OK** para prosseguir para a guia **Configurações Adicionais**. 

1. Na guia **Configurações Adicionais**, especifique os seguintes valores:

   - **Tamanho** - Selecione a opção de tamanho apropriado para sua máquina virtual de Jenkins.
   - **Tipo de disco de máquina virtual** - Especifique HDD (unidade de disco rígido) ou SSD (unidade de estado sólido) para indicar qual tipo de disco de armazenamento é permitido para as máquinas virtuais Jenkins.
   - **Rede virtual** - (opcional) Selecione **Rede virtual** para modificar as configurações padrão.
   - **Sub-redes** - Selecione **Sub-redes**, verifique as informações e selecione **OK**.
   - **Endereço IP público** - O nome do endereço IP padrão é o nome de Jenkins que você especificou na página anterior com um sufixo de - IP. Você pode selecionar a opção para alterar esse padrão.
   - **Rótulo de nome de domínio** - Especifique o valor para a URL totalmente qualificada para a máquina virtual Jenkins.
   - **Tipo de versão Jenkins** - Selecione o tipo de versão desejada entre as opções: `LTS`, `Weekly build` ou `Azure Verified`. As opções `LTS` e `Weekly build` são explicadas no artigo [Linha de versão Jenkins LTS](https://jenkins.io/download/lts/). A opção `Azure Verified` refere-se a uma [ versão Jenkins LTS](https://jenkins.io/download/lts/) que foi verificada para executar no Azure. 
   - **Tipo de JDK** – JDK a ser instalado. O padrão são builds certificados do OpenJDK testados por Zulu.

     ![Insira as configurações de máquina virtual para Jenkins na guia Configurações.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-settings.png)

1. Selecione **OK** para prosseguir para a guia **Configurações de Integração**.

1. Na guia **Configurações de Integração**, especifique os valores a seguir:

    - **Entidade de Serviço** - A entidade de serviço é adicionada ao Jenkins como uma credencial para autenticação com o Azure. `Auto` significa que a entidade será criada pela MSI (Identidade de Serviço Gerenciada). `Manual` significa que a entidade deve ser criada por você. 
        - **ID do Aplicativo** e **Segredo** - Se você selecionar a opção `Manual` para a opção **Entidade de Serviço**, será necessário especificar `Application ID` e `Secret` para a entidade de serviço. Ao [criar uma entidade de serviço](/cli/azure/create-an-azure-service-principal-azure-cli), observe que a função padrão é **Colaborador**, que é suficiente para trabalhar com recursos do Azure.
    - **Habilitar agentes de nuvem** - Especifica o modelo de nuvem padrão para os agentes em que `ACI` faz referência à Instância do Contêiner do Azure e `VM` refere-se às máquinas virtuais. Também é possível especificar `No` se não desejar habilitar um agente de nuvem.

1. Selecione **OK** para prosseguir para a guia **Resumo**.

1. Quando a guia **Resumo** aparece, as informações inseridas são validadas. Depois de ver a mensagem **Validação aprovada** (na parte superior da guia), selecione **OK**. 

     ![Na guia Resumo exibe e valida as opções selecionadas.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-summary.png)

1. Quando a guia **Criar** aparecer, selecione **Criar** para criar a máquina virtual Jenkins. Quando o servidor estiver pronto, uma notificação aparecerá no portal do Azure.

     ![Notificação de que Jenkins está pronto.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-notification.png)

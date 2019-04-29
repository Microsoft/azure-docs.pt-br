---
title: Instalar o Micro Focus Enterprise Server 4.0 e do Enterprise Developer 4.0 no Azure | Microsoft Docs
description: Hospedar novamente as IBM z/OS mainframe cargas de trabalho usando o desenvolvimento do Micro Focus e testar o ambiente de máquinas virtuais (VMs).
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: 33d0baf10df1882baf212c3e2c2683c8ca072fcc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61487561"
---
# <a name="install-micro-focus-enterprise-server-40-and-enterprise-developer-40-on-azure"></a>Instalar o Micro Focus Enterprise Server 4.0 e do Enterprise Developer 4.0 no Azure

Este artigo mostra como configurar [Micro foco Enterprise Server 4.0](https://www.microfocus.com/documentation/enterprise-developer/es30/) e [Micro foco Enterprise Developer 4.0](https://www.microfocus.com/documentation/enterprise-developer/ed_30/) no Azure.

Uma carga de trabalho comum no Azure é um ambiente de desenvolvimento e teste, porque ele é tão econômico e fácil de implantar e destruir. Com o Enterprise Server, Micro Focus criou uma das maiores mainframe rehosting plataformas disponíveis. Você pode executar cargas de trabalho do z/OS em um x86 mais barato plataforma no Azure usando o Windows ou Linux VMs (máquinas virtuais).

Essa configuração usa VMs do Azure executando a imagem do Windows Server 2016 no Azure Marketplace com o Microsoft SQL Server 2017 já instalado. Essa configuração também se aplica ao Azure Stack.

O ambiente de desenvolvimento correspondentes para Enterprise Server é desenvolvedor empresarial, que é executado em qualquer um dos Microsoft Visual Studio 2017, Visual Studio Community (gratuito baixar), ou o Eclipse. Este artigo mostra como implantá-lo usando uma máquina de virtual do Windows Server 2016 vem com o Visual Studio 2017 instalado.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, confira estes pré-requisitos:

- Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- O software do Micro Focus e uma licença válida (ou licença de avaliação). Se você for um cliente existente do Micro Focus, entre em contato com seu representante do Micro Focus. Caso contrário, [solicite uma avaliação](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

- Obter a documentação para [Enterprise Server e do Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/#").

> [!NOTE]
> Uma prática recomendada é configurar um túnel de rede privada virtual (VPN) site a site ou um jumpbox, portanto, você pode controlar o acesso às VMs do Azure.

## <a name="install-enterprise-server"></a>Instalar o Enterprise Server

1. Para melhor capacidade de gerenciamento e segurança, considere a criação de um novo grupo de recursos apenas para este projeto — por exemplo, **RGMicroFocusEntServer**. Use a primeira parte do nome no Azure para designar o tipo de recurso para torná-lo mais fácil de identificar em uma lista.

2. Crie uma máquina virtual. No Azure Marketplace, selecione a máquina virtual e o sistema operacional desejado. Aqui está uma configuração recomendada:

    - **Enterprise Server**: Selecione a VM de v3 de ES2 (com 2 vCPUs e 16 GB de memória) com o Windows Server 2016 e SQL Server 2017 instalado. Essa imagem está disponível no Azure Marketplace. Enterprise Server pode usar também o banco de dados SQL.

    - **Enterprise Developer**: Selecione B2ms VM (com 2 vCPUs e 8 GB de memória) com o Windows 10 e o Visual Studio instalado. Essa imagem está disponível no Azure Marketplace.

3. Sobre o **Noções básicas de** folha, insira seu nome de usuário e senha. Selecione o **assinatura** e **local/região** você gostaria de usar para as VMs. Selecione **RGMicroFocusEntServer** para o grupo de recursos.

4. Coloque as duas VMs na mesma rede virtual para que eles podem se comunicar uns com os outros.

5. Aceite os padrões para o restante das configurações. Lembre-se o nome de usuário e senha que você criar para o administrador dessas VMs.

6. Quando as máquinas virtuais tiverem sido criadas, abra as portas de entrada 9003, x86 e 80 para HTTP e 3389 para RDP na máquina do servidor corporativo e 3389 no computador do desenvolvedor.

7. Para fazer logon máquina virtual do servidor corporativo, no portal do Azure, selecione a VM do ES2 v3. Vá para o **visão geral** folha e selecione **Connect** para iniciar uma sessão RDP. Faça logon usando as credenciais que você criou para a VM.

8. Na sessão RDP, carrege dois arquivos a seguir. Como esse é o Windows, portanto, você pode arrastar e soltar os arquivos para a sessão RDP:

    - **es\_40. exe**, o arquivo de instalação do servidor corporativo.

    - **mflic**, o arquivo de licença correspondente — Enterprise Server não será carregado sem ele.

9. Clique duas vezes no arquivo para iniciar a instalação. Na primeira janela, selecione o local de instalação e aceite o contrato de licença de usuário final.

     ![Tela de instalação do foco Enterprise Server Micro](media/01-enterprise-server.png)

     Quando a instalação for concluída, a seguinte mensagem será exibida:

     ![Tela de instalação do foco Enterprise Server Micro](media/02-enterprise-server.png)

### <a name="check-for-updates"></a>Verificar se há atualizações

Após a instalação, certifique-se de verificar se há quaisquer atualizações adicionais desde alguns pré-requisitos, como o Microsoft C++ redistribuível e o .NET Framework são instalados junto com o Enterprise Server.

### <a name="upload-the-license"></a>Carregar a licença

1. Inicie a administração do licenciamento Micro Focus.

2. Clique em **começar** \> **Gerenciador de licença de foco Micro** \> **administração do licenciamento**e, em seguida, clique no **instalar** guia. Escolha o tipo de formato de licença para carregar: um arquivo de licença ou um código de licença de 16 caracteres. Por exemplo, para um arquivo, na **arquivo de licença**, navegue até a **mflic** arquivo carregado anteriormente para a VM e selecione **instalar licenças**.

     ![Caixa de diálogo de administração do licenciamento por foco Micro](media/03-enterprise-server.png)

3. Verifique se o servidor corporativo é carregada. Tente iniciar o site de administração de servidor da empresa em um navegador usando essa URL <http://localhost:86/> . A página de administração de servidor da empresa é exibida conforme mostrado.

     ![Página de administração de servidor corporativo](media/04-enterprise-admin.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>Instale o Enterprise Developer na máquina do desenvolvedor

1. Selecione o grupo de recursos criado anteriormente (por exemplo, **RGMicroFocusEntServer**), em seguida, selecione a imagem do desenvolvedor.

2. Para fazer logon na máquina virtual, vá para o **visão geral** folha e selecione **Connect**. Isso inicia uma sessão RDP. Faça logon usando as credenciais que você criou para a VM.

3. Na sessão RDP, carrega dois arquivos a seguir (arrastar e soltar se desejar):

    - **edvs2017.exe**, o arquivo de instalação do servidor corporativo.

    - **mflic**, o arquivo de licença correspondente (Enterprise Developer não será carregado sem ela).

4. Clique duas vezes o **edvs2017.exe** arquivo para iniciar a instalação. Na primeira janela, selecione o local de instalação e aceite o contrato de licença de usuário final. Se você quiser, escolha **instalar Rumba 9.5** para instalar este emulador de terminal, você provavelmente precisará.

     ![Micro desenvolvedor empresarial de foco para a caixa de diálogo de instalação do Visual Studio 2017](media/04-enterprise-server.png)

5. Após a instalação for concluída, a seguinte mensagem será exibida:

     ![Mensagem de instalação bem-sucedida](media/05-enterprise-server.png)

6. Inicie o Gerenciador de licença de foco do Micro exatamente como fez para Enterprise Server. Escolher **inicie** \> **Gerenciador de licença de foco Micro** \> **administração do licenciamento por**e clique no **instalar**guia.

7. Escolha o tipo de formato de licença para carregar: um arquivo de licença ou um código de licença de 16 caracteres. Por exemplo, para um arquivo, na **arquivo de licença**, navegue até a **mflic** arquivo carregado anteriormente para a VM e selecione **instalar licenças**.

     ![Caixa de diálogo de administração do licenciamento por foco Micro](/edia/07-enterprise-server.png)

Quando o desenvolvedor corporativo for carregada, a implantação de um ambiente de desenvolvimento e teste do Micro Focus no Azure foi concluída!

## <a name="next-steps"></a>Próximas etapas

- [Configurar o aplicativo de demonstração de banco](./demo.md)
- [Enterprise Server são executados em contêineres de Docker](./run-enterprise-server-container.md)
- [Migração de aplicativos de mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)

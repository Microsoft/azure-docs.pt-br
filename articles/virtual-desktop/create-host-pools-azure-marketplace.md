---
title: Portal do Azure do pool de host da Área de Trabalho Virtual do Windows – Azure
description: Como criar um pool de host da Área de Trabalho Virtual do Windows usando o portal do Azure.
author: Heidilohr
ms.topic: tutorial
ms.custom: references_regions
ms.date: 03/10/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 1003ab20f839469235d6bd7949ec3b2df4dd4f25
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447974"
---
# <a name="tutorial-create-a-host-pool-with-the-azure-portal"></a>Tutorial: Criar um pool de host com o portal do Azure

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver usando a Área de Trabalho Virtual do Windows (clássica), sem objetos do Azure Resource Manager, confira [este artigo](./virtual-desktop-fall-2019/create-host-pools-azure-marketplace-2019.md). Todos os objetos que você criar com a Área de Trabalho Virtual do Windows (clássico) não poderão ser gerenciados com o portal do Azure.

Pools de hosts são uma coleção de uma ou mais VMs (máquinas virtuais) idênticas dentro dos ambientes da Área de Trabalho Virtual do Windows. Cada pool de hosts pode conter um grupo de aplicativo com o qual os usuários podem interagir como se eles estivessem em uma área de trabalho física.

Este artigo explicará o processo de instalação para criar um pool de host para um ambiente de Área de Trabalho Virtual do Windows por meio do portal do Azure. Esse método fornece uma interface do usuário baseada em navegador para criar um pool de host na Área de Trabalho Virtual do Windows, criar um grupo de recursos com VMs em uma assinatura do Azure, ingressar essas VMs no domínio do Azure AD (Active Directory) e registrar as VMs com a Área de Trabalho Virtual do Windows.

## <a name="prerequisites"></a>Pré-requisitos

Você precisará inserir os seguintes parâmetros para criar um pool de host:

- O nome da imagem da VM
- Configuração da VM
- Propriedades de domínio e rede
- Propriedades do pool de host da Área de Trabalho Virtual do Windows

Você também precisará saber o seguinte:

- O local em que a origem da imagem que você deseja usar está. É da galeria do Azure ou é uma imagem personalizada?
- Suas credenciais de ingresso no domínio.

Além disso, verifique se você registrou o provedor de recursos Microsoft.DesktopVirtualization. Se você ainda não fez isso, acesse **Assinaturas**, selecione o nome da assinatura e escolha **Provedores de recursos**. Pesquise DesktopVirtualization, selecione Microsoft.DesktopVirtualization e escolha Registrar.

Ao criar um pool de host da Área de Trabalho Virtual do Windows com o modelo do Azure Resource Manager, você pode criar uma máquina virtual na galeria do Azure, uma imagem gerenciada ou uma imagem não gerenciada. Para saber mais sobre como criar imagens da VM, confira [Preparar um VHD ou VHDX do Windows para carregar no Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) e [Criar uma imagem gerenciada de uma VM generalizada no Azure](../virtual-machines/windows/capture-image-resource.md).

Se você ainda não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar a seguir estas instruções.

## <a name="begin-the-host-pool-setup-process"></a>Iniciar o processo de instalação do pool de host

Para começar a criar seu pool de host:

1. Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com/).
   
   >[!NOTE]
   > Se estiver entrando no portal US Gov, acesse [https://portal.azure.us/](https://portal.azure.us/).

2. Insira **Área de Trabalho Virtual do Windows** na barra de pesquisa e, em seguida, localize e selecione **Área de Trabalho Virtual do Windows** em serviços.

3. Na página visão geral da **Área de Trabalho Virtual do Windows**, selecione **Criar um pool de host**.

4. Na guia **Noções básicas**, selecione a assinatura correta em Detalhes do projeto.

5. Selecione **Criar** para criar um grupo de recursos ou selecione um grupo de recursos no menu suspenso.

6. Insira um nome exclusivo para seu pool de host.

7. No campo Localização, selecione a região em que você deseja criar o pool de host no menu suspenso.

   Os metadados para esse pool de host e seus objetos relacionados serão armazenados na geografia do Azure associada às regiões que você selecionou. Escolha as regiões dentro da geografia em que você deseja que os metadados de serviço sejam armazenados.

     > [!div class="mx-imgBorder"]
     > ![Uma captura de tela do portal do Azure mostrando o campo Localização com a localização Leste dos EUA selecionada. Ao lado do campo está o texto que diz "Os metadados serão armazenados no Leste dos EUA".](media/portal-location-field.png)
  
   >[!NOTE]
   > Caso deseje criar seu pool de host em [uma região compatível](data-locations.md) fora dos EUA, registre novamente o provedor de recursos. Após o novo registro, você verá as outras regiões na lista suspensa de seleção da localização. Saiba como se registrar novamente em nosso artigo de solução de problemas de [criação do pool de host](troubleshoot-set-up-issues.md#i-only-see-us-when-setting-the-location-for-my-service-objects).

8. Em Tipo de pool de host, selecione se o pool de host será **Pessoal** ou **Em pool**.

    - Se você escolher **Pessoal**, selecione **Automático** ou **Direto** no campo Tipo de Atribuição.

      > [!div class="mx-imgBorder"]
      > ![Uma captura de tela do menu suspenso do campo de tipo de atribuição. O usuário selecionou Automático.](media/assignment-type-field.png)

9.  Se você escolher **Em pool**, insira as seguintes informações:

     - Para **Limite máximo de sessão**, insira o número máximo de usuários cuja carga você deseja balancear para um host de sessão.
     - Para **Algoritmo de balanceamento de carga**, escolha amplitude primeiro ou profundidade primeiro conforme seu padrão de uso.

       > [!div class="mx-imgBorder"]
       > ![Uma captura de tela do campo de tipo de atribuição com "Em pool" selecionado. O usuário está passando o cursor sobre Balanceamento em largura no menu suspenso de balanceamento de carga.](media/pooled-assignment-type.png)

10. Selecione **Avançar: Máquinas Virtuais >** .

11. Se você já tiver criado máquinas virtuais e quiser usá-las com o novo pool de host, selecione **Não**, **Avançar: Workspace >** e vá para a seção [Informações do workspace](#workspace-information). Se você quiser criar máquinas virtuais e registrá-las no novo pool de host, selecione **Sim**.

Agora que você concluiu a primeira parte, vamos passar para a próxima parte do processo de instalação em que criamos a VM.

## <a name="virtual-machine-details"></a>Detalhes da máquina virtual

Agora que estamos na primeira parte, você precisará configurar sua VM.

Para configurar sua máquina virtual dentro do processo de configuração do pool de host:

1. Em **Grupo de recursos**, escolha o grupo de recursos no qual você deseja criar as máquinas virtuais. Esse pode ser um grupo de recursos diferente daquele usado para o pool de host.

2. Depois disso, forneça um **Prefixo de nome** para nomear as máquinas virtuais que o processo de configuração cria. O sufixo será `-` com números começando em 0.

3. Escolha a **Localização da máquina virtual** em que você deseja criar as máquinas virtuais. Podem ser iguais ou diferentes da região selecionada para o pool de host.
   
4. Em seguida, escolha a opção de disponibilidade mais adequada às suas necessidades. Para saber mais sobre qual opção é adequada para você, confira [Opções de disponibilidade para máquinas virtuais no Azure](../virtual-machines/availability.md) e [nossas Perguntas Frequentes](faq.md#which-availability-option-is-best-for-me).
   
   > [!div class="mx-imgBorder"]
   > [Uma captura de tela do menu suspenso da zona de disponibilidade. A opção "zona de disponibilidade" está realçada.](media/availability-zone.png)

5. Em seguida, escolha a imagem que precisa ser usada para criar a máquina virtual. Você pode escolher **Galeria** ou **Blob de Armazenamento**.

    - Se você escolher **Galeria**, selecione uma das imagens recomendadas no menu suspenso:

      - Windows 10 Enterprise multissessão, versão 1909
      - Windows 10 Enterprise multissessão, versão 1909 + Aplicativos Microsoft 365
      - Windows Server 2019 Datacenter
      - Windows 10 Enterprise multissessão, versão 2004
      - Windows 10 Enterprise multissessão, versão 2004 + Aplicativos Microsoft 365

      Se você não vir a imagem desejada, selecione **Ver todas as imagens**, o que permite que você selecione outra imagem na galeria ou uma imagem fornecida pela Microsoft e outros editores. Verifique se a imagem escolhida é uma das [imagens do sistema operacional com suporte](overview.md#supported-virtual-machine-os-images).

      > [!div class="mx-imgBorder"]
      > ![Uma captura de tela do Marketplace com uma lista de imagens da Microsoft exibida.](media/marketplace-images.png)

      Você também pode ir até **Meus Itens** e escolher uma imagem personalizada que você já carregou.

      > [!div class="mx-imgBorder"]
      > ![Uma captura de tela da guia Meus Itens.](media/my-items.png)

    - Se você escolher **Blob de Armazenamento**, poderá usar seu build de imagem por meio do Hyper-V ou em uma VM do Azure. Basta inserir a localização da imagem no blob de armazenamento como um URI.
   
   A localização da imagem é independente da opção de disponibilidade, mas a resiliência de zona da imagem determina se essa imagem pode ser usada com a zona de disponibilidade. Se você selecionar uma zona de disponibilidade ao criar a imagem, verifique se você está usando uma imagem da galeria com a resiliência de zona habilitada. Para saber mais sobre qual opção de resiliência de zona você deve usar, confira [as Perguntas Frequentes](faq.md#which-availability-option-is-best-for-me).

6. Em seguida, escolha o **Tamanho da máquina virtual** que você deseja usar. Você pode manter o tamanho padrão no estado em que se encontra ou selecionar **Alterar tamanho** para alterar o tamanho. Se você selecionar **Alterar tamanho**, na janela exibida, escolha o tamanho da máquina virtual adequado para sua carga de trabalho.

7. Em **Número de VMs**, forneça o número de VMs que você deseja criar para o pool de host.

    >[!NOTE]
    >O processo de instalação pode criar até 400 VMs durante a configuração do pool de host. Cada processo de instalação da VM cria quatro objetos em seu grupo de recursos. Como os processos de criação não verificam sua cota de assinatura, verifique se o número de VMs inseridas está dentro dos limites de API e de VM do Azure para seu grupo de recursos e assinatura. Você pode adicionar mais VMs depois de concluir a criação do pool de host.

8. Escolha o tipo de discos do SO que você deseja que suas VMs usem: SSD Standard, SSD Premium ou HDD Standard.

9. Em Rede e Segurança, selecione a **Rede virtual** e a **Sub-rede** em que você deseja colocar as máquinas virtuais criadas. Verifique se a rede virtual consegue se conectar ao controlador de domínio, pois você precisará ingressar as máquinas virtuais dentro da rede virtual ao domínio. Os servidores DNS da rede virtual que você selecionou devem ser configurados para usar o IP do controlador de domínio.

10. Selecione o tipo de grupo de segurança desejado: **Básico**, **Avançado** ou **Nenhum**.

    Se você selecionar **Básico**, precisará selecionar se deseja que qualquer porta de entrada seja aberta. Se você selecionar **Sim**, escolha na lista de portas padrão para permitir conexões de entrada.

    >[!NOTE]
    >Para maior segurança, recomendamos não abrir portas de entrada públicas.

    > [!div class="mx-imgBorder"]
    > ![Uma captura de tela da página do grupo de segurança que mostra uma lista de portas disponíveis em um menu suspenso.](media/available-ports.png)

    Se você escolher **Avançado**, selecione um grupo de segurança de rede existente que você já configurou.

11. Depois disso, selecione se deseja que as máquinas virtuais sejam ingressadas em um domínio específico e em uma unidade organizacional. Se você escolher **Sim**, especifique o domínio para ingressar. Opcionalmente, você pode adicionar uma unidade organizacional específica na qual deseja que as máquinas virtuais estejam. Se você escolher **Não**, as VMs serão unidas ao domínio que corresponde ao sufixo da **UPN de ingresso no domínio do AD**.

    - Ao especificar uma unidade organizacional, lembre-se de usar o caminho completo (nome diferenciado) e não usar aspas.

12. Em Conta do Administrador de Domínio, insira as credenciais para o administrador da rede virtual do Domínio do Active Directory que você selecionou. Essa conta não pode ter a MFA (autenticação multifator) habilitada. Ao ingressar em um domínio do Azure AD DS (Azure Active Directory Domain Services), a conta precisa fazer parte do grupo de administradores do Azure AD DS e a senha da conta precisa funcionar no Azure AD DS.

13. Selecione **Avançar: Workspace >** .

Com isso, estamos prontos para iniciar a próxima fase de configuração do pool de host: registrar seu grupo de aplicativos em um workspace.

## <a name="workspace-information"></a>Informações do workspace

O processo de instalação do pool de host cria um grupo de aplicativos de desktop por padrão. Para que o pool de host funcione como pretendido, você precisará publicar esse grupo de aplicativos para usuários ou grupos de usuários e registrar o grupo de aplicativos em um workspace.

Para registrar o grupo de aplicativos da área de trabalho em um workspace:

1. Selecione **Sim** na barra superior.

   Se você selecionar **Não**, poderá registrar o grupo de aplicativos mais tarde, mas recomendamos que você faça o registro do workspace assim que puder para que seu pool de host funcione corretamente.

2. Em seguida, escolha se deseja criar um workspace ou selecionar um dos workspaces existentes. Somente workspaces criados na mesma localização que o pool de host terão permissão para registrar o grupo de aplicativos.

3. Opcionalmente, você pode selecionar **Avançar: Marcas >** .

    Aqui, você pode adicionar marcas para que possa agrupar os objetos a metadados para facilitar o trabalho dos administradores.

4. Quando terminar, selecione **Review + create**.

     >[!NOTE]
     >O processo de examinar + criar validação não verifica se a sua senha cumpre os padrões de segurança ou se sua arquitetura está correta, portanto, você precisará verificar se há algum problema com algum desses itens.

5. Examine as informações sobre sua implantação para verificar se tudo está correto. Quando terminar, selecione **Criar**. Isso inicia o processo de implantação, que cria os seguintes objetos:

     - Seu novo pool de host.
     - Um grupo de aplicativos da área de trabalho.
     - Um workspace, se você optar por criá-lo.
     - Se você optar por registrar o grupo de aplicativos da área de trabalho, o registro será concluído.
     - Máquinas virtuais, se você optar por criá-las, que são ingressadas no domínio e registradas com o novo pool de host.
     - Um link de download para um modelo de gerenciamento de recursos do Azure com base em sua configuração.

Depois disso, está tudo pronto.

## <a name="run-the-azure-resource-manager-template-to-provision-a-new-host-pool"></a>Executar o modelo do Azure Resource Manager para provisionar um novo pool de host

Se você preferir usar um processo automatizado, [baixe nosso modelo de Azure Resource Manager](https://github.com/Azure/RDS-Templates/tree/master/ARM-wvd-templates) para provisionar o seu novo pool de host.

>[!NOTE]
>Se você estiver usando um processo automatizado para criar o seu ambiente, precisará da versão mais recente do arquivo JSON de configuração. Você pode localizar o arquivo JSON [aqui](https://wvdportalstorageblob.blob.core.windows.net/galleryartifacts?restype=container&comp=list).

## <a name="next-steps"></a>Próximas etapas

Agora que você já criou seu pool de host, você pode preenchê-lo com programas RemoteApp. Para saber mais sobre como gerenciar aplicativos na Área de Trabalho Virtual do Windows, veja nosso próximo tutorial:

> [!div class="nextstepaction"]
> [Tutorial Gerenciar grupos de aplicativos](./manage-app-groups.md)

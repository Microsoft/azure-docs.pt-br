---
title: "Criar uma imagem de máquina virtual para o Azure Marketplace | Microsoft Docs"
description: "Instruções detalhadas sobre como criar uma imagem de máquina virtual para o Azure Marketplace para outras pessoas comprarem."
services: Azure Marketplace
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 5c937b8e-e28d-4007-9fef-624046bca2ae
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 01/05/2017
ms.author: hascipio; v-divte
ms.openlocfilehash: 046ce7af40301014746c6aef07d08d81ab4adcc2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="guide-to-create-a-virtual-machine-image-for-the-azure-marketplace"></a>Guia para criar uma imagem de máquina virtual para o Azure Marketplace
Este artigo, **Etapa 2**, mostra a preparação dos VHDs (discos rígidos virtuais) que você implantará no Azure Marketplace. Seus VHDs são a base de sua SKU. O processo é diferente dependendo se você está fornecendo uma SKU baseada em Linux ou em Windows. Este artigo aborda ambos os cenários. Esse processo pode ser executado em paralelo com [Criação e registro de conta][link-acct-creation].

## <a name="1-define-offers-and-skus"></a>1. Definir ofertas e SKUs
Nesta seção, você aprenderá a definir as ofertas e as SKUs associadas.

Uma oferta é um "pai" de todas as suas SKUs. Pode haver várias ofertas. Como você decide estruturar suas ofertas é com você. Quando uma oferta é movida para teste, vai com todas as suas SKUs. Considere cuidadosamente seus identificadores de SKU, já que ficarão visíveis na URL:

* Azure.com: http://azure.microsoft.com/marketplace/partners/{PartnerNamespace}/{OfferIdentifier}-{SKUidentifier}
* Versão Prévia do Portal do Azure: https://portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{SKUIDdentifier}  

Uma SKU é o nome comercial para uma imagem de VM. Uma imagem de VM contém um disco de sistema operacional e zero ou mais discos de dados. É, essencialmente, o perfil completo de armazenamento para uma máquina virtual. É necessário um VHD por disco. Mesmo discos de dados em branco exigem a criação de um VHD.

Independentemente de qual sistema operacional que você usa, adicione apenas o número mínimo de discos de dados necessários para a SKU. Os clientes não podem remover discos que fazem parte de uma imagem no momento da implantação, mas sempre podem adicionar discos durante ou após a implantação, se precisar deles.

> [!IMPORTANT]
> **Não altere a contagem de discos em uma nova versão da imagem.** Se você precisar reconfigurar os discos de Dados da imagem, defina um novo SKU. A publicação de uma nova versão da imagem com contagens de disco diferentes terá o potencial de interromper a nova implantação com base na nova versão da imagem em caso de dimensionamento automático, implantação automática de soluções por meio de modelos do ARM e outros cenários.
>
>

### <a name="11-add-an-offer"></a>1.1 Adicionar uma oferta
1. Entre no [Portal de Publicação][link-pubportal] usando a conta de vendedor.
2. Selecione a guia **Máquinas virtuais** do Portal de Publicação. No campo de entrada solicitado, insira o nome da oferta. O nome da oferta geralmente é o nome do produto ou serviço que você pretende vender no Azure Marketplace.
3. Selecione **Criar**.

### <a name="12-define-a-sku"></a>1.2 Defina uma SKU
Depois de adicionar uma oferta, você terá de definir e identificar sua SKU. Você pode ter várias ofertas e cada oferta pode ter várias SKUs abaixo dela. Quando uma oferta é movida para teste, vai com todas as suas SKUs.

1. **Adicionar uma SKU.** A SKU requer um identificador, que é usado na URL. O identificador tem que ser exclusivo no perfil de publicação, mas não há risco de conflito de identificador com outros editores.

   > [!NOTE]
   > A oferta e o identificador da SKU são exibido na URL da oferta no Marketplace.
   >
   >
2. **Adicionar uma descrição resumida para a sua SKU.** As descrições resumidas ficam visíveis para os clientes; portanto, você deve torná-las fáceis de ler. Essa informação não precisa ser bloqueada até a fase "Mover para o Preparo". Até lá, você estará livre para editá-la.
3. Se você estiver usando SKUs baseadas no Windows, siga os links sugeridos para adquirir as versões aprovadas do Windows Server.

## <a name="2-create-an-azure-compatible-vhd-linux-based"></a>2. Criar VHD compatível com o Azure (baseado em Linux)
Esta seção concentra-se nas melhores práticas para a criação de uma imagem VM baseada em Linux para o Azure Marketplace. Para uma explicação passo a passo, veja a seguinte documentação: [Criando e carregando um disco rígido virtual que contém o sistema operacional Linux](../virtual-machines/linux/classic/create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

## <a name="3-create-an-azure-compatible-vhd-windows-based"></a>3. Criar um VHD compatível com Azure (baseado no Windows)
Esta seção enfoca as etapas para criar uma SKU baseada no Windows Server para o Azure Marketplace.

### <a name="31-ensure-that-you-are-using-the-correct-base-vhds"></a>3.1 Verifique se você está usando os VHDs de base corretos
O VHD do sistema operacional para a imagem VM deve ser baseado em uma imagem de base aprovada do Azure que contém o Windows Server ou o SQL Server.

Para começar, crie uma VM de uma das seguintes imagens, localizadas no [Portal do Microsoft Azure][link-azure-portal]:

* Windows Server ([2012 R2 Datacenter][link-datactr-2012-r2], [2012 Datacenter][link-datactr-2012], [2008 R2 SP1][link-datactr-2008-r2])
* SQL Server 2014 ([Enterprise][link-sql-2014-ent], [Standard][link-sql-2014-std], [Web][link-sql-2014-web])
* SQL Server 2012 SP2 ([Enterprise][link-sql-2012-ent], [Standard][link-sql-2012-std], [Web][link-sql-2012-web])

Esses links também podem ser encontrados no portal de publicação na página de SKU.

> [!TIP]
> Se você estiver usando o atual Portal de do Azure ou PowerShell, as imagens do Windows Server publicadas em 8 de setembro de 2014 e posteriormente serão aprovadas.
>
>

### <a name="32-create-your-windows-based-vm"></a>3.2 Criar sua VM baseada em Windows
No portal do Microsoft Azure, você pode criar sua VM com base em uma imagem aprovada em apenas algumas etapas simples. O que se segue é uma visão geral do processo.

1. Na página de imagem base, selecione **Criar Máquina Virtual** para ser direcionado para o novo [Portal do Microsoft Azure][link-azure-portal].

    ![desenho][img-acom-1]
2. Entre no portal com a conta da Microsoft e senha da assinatura do Azure que você deseja usar.
3. Siga os prompts para criar uma VM usando a imagem de base que você selecionou. Você precisa fornecer um nome de host (nome do computador), um nome de usuário (registrado como administrador) e a senha para a VM.

    ![desenho][img-portal-vm-create]
4. Selecione o tamanho da VM a implantar:

    a.    Se você pretende desenvolver o VHD no local, o tamanho não importa. Considere o uso de uma das VMs menores.

    b.    Se você pretende desenvolver a imagem no Azure, considere o uso de um dos tamanhos de VM recomendados para a imagem selecionada.

    c.    Para informações de preços, consulte o seletor do **tipo de preço recomendado** exibido no portal. Ele fornecerá os três tamanhos recomendados oferecidos pelo editor. (Neste caso, o editor é a Microsoft.)

    ![desenho][img-portal-vm-size]
5. Defina as propriedades:

    a.    Para implantação rápida, você pode deixar os valores padrão para as propriedades em **Configuração Opcional** e **Grupo de Recursos**.

    b.    Em **Conta de Armazenamento**, você pode opcionalmente selecionar a conta de armazenamento no qual o VHD do sistema operacional será armazenado.

    c.    Em **Grupo de Recursos**, você pode opcionalmente selecionar o grupo lógico no qual coloca a VM.
6. Selecione o **Local** para implantação:

    a.    Se você pretende desenvolver o VHD no local, a localização não importa porque você carregará a imagem no Azure mais tarde.

    b.    Se você pretende desenvolver a imagem no Azure, considere o uso de uma das regiões do Microsoft Azure sediadas nos EUA desde o início. Isso acelera o processo de cópia VHD que a Microsoft realiza em seu nome quando você enviar sua imagem para a certificação.

    ![desenho][img-portal-vm-location]
7. Clique em **Criar**. A VM começa a implantar. Em poucos minutos, você terá uma implantação bem-sucedida e poderá começar a criar a imagem para a sua SKU.

### <a name="33-develop-your-vhd-in-the-cloud"></a>3.3 Desenvolver seu VHD na nuvem
Recomendamos fortemente que você desenvolva seu VHD na nuvem usando o protocolo RDP. Você se conecta ao RDP com o nome de usuário e a senha especificada durante o provisionamento.

> [!IMPORTANT]
> Se você desenvolve seu VHD no local (não recomendado), consulte [Criando um imagem de máquina virtual no local](marketplace-publishing-vm-image-creation-on-premise.md). É necessário baixar o VHD se você está desenvolvendo na nuvem.
>
>

**Conecte-se via RDP usando o [Portal do Microsoft Azure][link-azure-portal]**

1. Selecione **Procurar** > **VMs**.
2. A folha Máquinas Virtuais se abrirá. Verifique se a VM com a qual você deseja se conectar está em execução e selecione-a na lista de VMs implantadas.
3. Uma folha abre descrevendo a VM selecionada. Na parte superior, clique em **Conectar**.
4. Você será solicitado a inserir o nome de usuário e a senha que especificou durante o provisionamento.

**Conecte-se via RDP usando PowerShell**

Para baixar um arquivo de área de trabalho remota para um computador local, utilize o cmdlet [Get-AzureRemoteDesktopFile][link-technet-2]. Para usar esse cmdlet, você precisa saber o nome do serviço e o nome da VM. Se você criou a VM no [Portal do Microsoft Azure][link-azure-portal], encontrará essas informações em propriedades da VM:

1. No Portal do Microsoft Azure, selecione **Procurar** > **VMs**.
2. A folha Máquinas Virtuais se abrirá. Selecione a VM que você implantou.
3. Uma folha abre descrevendo a VM selecionada.
4. Clique em **Propriedades**.
5. A primeira parte do nome de domínio é o nome do serviço. O nome do host é o nome da VM.

    ![desenho][img-portal-vm-rdp]
6. O cmdlet para baixar o arquivo RDP da VM criada na área de trabalho local do administrador é o que se segue.

        Get‐AzureRemoteDesktopFile ‐ServiceName “baseimagevm‐6820cq00” ‐Name “BaseImageVM” –LocalPath “C:\Users\Administrator\Desktop\BaseImageVM.rdp”

Mais informações sobre RDP podem ser encontradas no MSDN, no artigo [Conectar a uma VM do Azure com RDP ou SSH.](http://msdn.microsoft.com/library/azure/dn535788.aspx)

**Configure uma VM e crie sua SKU**

Depois que o sistema operacional do VHD é baixado, use o Hyper-V e configure uma VM para começar a criar sua SKU. Etapas detalhadas podem ser encontradas no seguinte link TechNet: [Instalar o Hyper-V e configurar uma VM.](http://technet.microsoft.com/library/hh846766.aspx)

### <a name="34-choose-the-correct-vhd-size"></a>3.4 Escolher o tamanho correto do VHD
O VHD do sistema operacional do Windows em sua imagem VM deve ser criado como um VHD de formato fixo de 128 GB.  

Se o tamanho físico for menor de 128 GB, o VHD deve ser esparso. As imagens básicas do Windows e SQL Server fornecidas já atenderam a esses requisitos; portanto, não altere o formato nem o tamanho do VHD obtido.  

Discos de dados podem ser de até 1 TB. Ao decidir sobre o tamanho do disco, lembre-se de que os clientes não podem redimensionar VHDs dentro de uma imagem no momento da implantação. Os VHDs de disco de dados devem ser criados como um VHD de formato fixo. Eles também devem ser esparsos. Discos de dados podem estar vazios ou conter dados.

### <a name="35-install-the-latest-windows-patches"></a>3.5 Instalar as últimas correções do Windows
As imagens de base contêm as últimas correções até a data de publicação. Antes de publicar o VHD do sistema operacional que você criou, verifique se o Windows Update foi executado e se todas as atualizações de segurança Críticas e Importantes mais recentes foram instaladas.

### <a name="36-perform-additional-configuration-and-schedule-tasks-as-necessary"></a>3.6 Execute as tarefas de configuração e programação adicionais, conforme necessário
Se uma configuração adicional for necessária, considere o uso de uma tarefa agendada que seja executada na inicialização para fazer as alterações finais na VM assim que ela for implantada:

* É uma boa prática que a tarefa mesma se exclua após uma execução bem-sucedida.
* Nenhuma configuração deve ter como base unidades diferentes de C ou D, uma vez que estas são as duas únicas unidades com existência sempre garantida. A unidade C é o disco do sistema operacional e a unidade D é o disco local temporário.

### <a name="37-generalize-the-image"></a>3.7 Generalizar a imagem
Todas as imagens do Azure Marketplace devem ser reutilizáveis de uma forma genérica. Em outras palavras, o VHD do sistema operacional deve ser generalizado:

* Para o Windows, a imagem deve ser "sysprepped" e nenhuma configuração que não dê suporte ao comando **sysprep** deve ser feita.
* Você pode executar o comando a seguir a partir do diretório WINDIR%\System32\Sysprep.

        sysprep.exe /generalize /oobe /shutdown

  Orientação sobre como aplicar o sysprep no sistema operacional é fornecida na Etapa do seguinte artigo MSDN: [Criar e carregar um VHD do Windows Server no Azure](../virtual-machines/windows/classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="4-deploy-a-vm-from-your-vhds"></a>4. Implantar uma VM por meio dos seus VHDs
Depois de carregar seus VHDs (o VHD do sistema operacional generalizado e zero ou mais VHDs de disco de dados) para uma conta de armazenamento do Azure, você pode registrá-los como uma imagem VM do usuário. Em seguida, você pode testar essa imagem. Observe que, como seu VHD do sistema operacional é generalizado, você não pode implantar a VM diretamente fornecendo a URL do VHD.

Para obter mais informações sobre imagens de VM revise os posts de blog abaixo:

* [Image da VM](https://azure.microsoft.com/blog/vm-image-blog-post/)
* [Guia do PowerShell da Imagem da VM](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)
* [Sobre imagens VM no Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx)

### <a name="set-up-the-necessary-tools-powershell-and-azure-cli"></a>Configurar as ferramentas necessárias, PowerShell e CLI do Azure
* [Como configurar o PowerShell](/powershell/azure/overview)
* [Como configurar a CLI do Azure](../cli-install-nodejs.md)

### <a name="41-create-a-user-vm-image"></a>4.1 Criar uma imagem VM de usuário
#### <a name="capture-vm"></a>Capturar VM
Leia os links fornecidos abaixo para obter orientação sobre como capturar a VM usando a API/PowerShell/ CLI do Azure.

* [API](https://msdn.microsoft.com/library/mt163560.aspx)
* [PowerShell](../virtual-machines/windows/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [CLI do Azure](../virtual-machines/linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="generalize-image"></a>Generalizar uma imagem
Leia os links fornecidos abaixo para obter orientação sobre como capturar a VM usando a API/PowerShell/ CLI do Azure.

* [API](https://msdn.microsoft.com/library/mt269439.aspx)
* [PowerShell](../virtual-machines/windows/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [CLI do Azure](../virtual-machines/linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="42-deploy-a-vm-from-a-user-vm-image"></a>4.2 Implante uma VM usando uma imagem de VM de usuário
Para implantar uma VM de uma imagem VM de usuário, você pode usar o [portal do Azure](https://manage.windowsazure.com) ou o PowerShell atuais.

**Implante uma VM no portal do Azure atual**

1. Acesse **Novo** > **Computação** > **Máquina virtual** > **Da galeria**.

    ![desenho][img-manage-vm-new]
2. Vá para **Minhas Imagens**e selecione a imagem VM por meio da qual implantar uma VM.

   1. Preste muita atenção à imagem selecionada, uma vez que a exibição **Minhas imagens** lista imagens do sistema operacional e imagens de VM.
   2. Examinar o número de discos pode ajudar a determinar qual tipo de imagem você está implantando, já que a maioria das imagens de VM tem mais de um disco. No entanto, é ainda possível ter uma imagem VM com apenas um único disco de sistema operacional, o qual teria, então, um **Número de discos** definido como 1.

      ![desenho][img-manage-vm-select]
3. Siga o assistente de criação da VM e especifique o nome da VM, o tamanho da VM, a localização, o nome de usuário e a senha.

**Implantar uma VM a partir do PowerShell**

Para implantar uma VM grande, a partir da imagem VM generalizada recém-criada, você pode usar os cmdlets a seguir.

    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot

> [!IMPORTANT]
> Consulte [solução de problemas comuns encontrados durante a criação do VHD] para obter assistência adicional.
>
>

## <a name="5-obtain-certification-for-your-vm-image"></a>5. Obter a certificação para a sua Imagem VM
A próxima etapa na preparação de sua Imagem VM para a Azure Marketplace é certificá-la.

Este processo inclui a execução de uma ferramenta de certificação especial, o carregamento dos resultados de verificação no contêiner do Azure onde o VHD reside, a adição de uma oferta, a definição da SKU e o envio da Imagem VM para certificação.

### <a name="51-download-and-run-the-certification-test-tool-for-azure-certified"></a>5.1 Baixe e execute a Ferramenta do teste de certificação para o Certificado Azure
A ferramenta de certificação é executada em uma máquina virtual em execução, provisionada da Imagem VM do usuário, para garantir que a Imagem VM seja compatível com o Microsoft Azure. Ela verificará se as orientações e os requisitos de preparação do VHD foram cumpridos. A saída da ferramenta é um relatório de compatibilidade, o que deve ser carregado no Portal de publicação ao solicitar a certificação.

A ferramenta de certificação pode ser usada com VMs de Windows e Linux. Ele conecta às VMs baseadas em Windows via PowerShell, e a VMs Linux via SSH.Net.

1. Primeiro, baixe a ferramenta de certificação no [Site de download da Microsoft][link-msft-download].
2. Abra a ferramenta de certificação e clique no botão **Iniciar Novo Teste** .
3. Na tela **Informações do Teste** , digite um nome para a execução de teste.
4. Escolha se a VM está em Linux ou Windows. Dependendo do que você escolher, selecione as opções subsequentes.

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>**Conecte a ferramenta de certificação a uma Imagem VM do Linux**
1. Selecione o modo de autenticação SSH: senha ou arquivo de chave.
2. Se estiver usando autenticação baseada em senha, digite o nome DNS (Sistema de Nome de Domínio), o nome de usuário e a senha.
3. Se estiver usando a autenticação do arquivo de chave, digite o nome DNS, o nome de usuário e a localização da chave privada.

   ![Autenticação de senha da Imagem de VM do Linux][img-cert-vm-pswd-lnx]

   ![Autenticação de chave de arquivo da Imagem de VM do Linux][img-cert-vm-key-lnx]

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Conectar a ferramenta de certificação a uma Imagem VM baseada em Windows**
1. Insira o nome DNS totalmente qualificado da VM (por exemplo, MyVMName.Cloudapp.net).
2. Digite um nome de usuário e uma senha.

   ![Autenticação de senha de Imagem de VM do Windows][img-cert-vm-pswd-win]

Depois de selecionar as opções corretas para a sua Imagem VM baseada em Linux ou Windows, selecione **Testar Conexão** para garantir que o SSH.Net ou o PowerShell tenha uma conexão válida para fins de teste. Quando a conexão for estabelecida, selecione **Avançar** para iniciar o teste.

Quando o teste for concluído, você receberá os resultados (Êxito/Falha/Aviso) para cada elemento de teste.

![Casos de teste para a Imagem de VM do Linux][img-cert-vm-test-lnx]

![Casos de teste para a Imagem de VM do Windows][img-cert-vm-test-win]

Se algum dos testes falhar, a imagem não será certificada. Se isso ocorrer, examine os requisitos e faça as alterações necessárias.

Após o teste automatizado, você é solicitado a fornecer informações adicionais na sua Imagem de VM por meio de uma tela de questionário.  Responda às perguntas e selecione **Avançar**.

![Questionário da Ferramenta de Certificação][img-cert-vm-questionnaire]

![Questionário da Ferramenta de Certificação][img-cert-vm-questionnaire-2]

Depois de concluir o questionário, você pode fornecer informações adicionais, como informações de acesso do SSH para a Imagem VM do Linux e explicações para as avaliações com falha. Você pode baixar os resultados do teste e arquivos de log para os casos de teste executados, bem como suas respostas ao questionário. Salve os resultados no mesmo contêiner como seus VHDs.

![Salvar resultados do teste de certificação][img-cert-vm-results]

### <a name="52-get-the-shared-access-signature-uri-for-your-vm-images"></a>5.2 Obtenha o URI de assinatura de acesso compartilhado para suas imagens VM
Durante o processo de publicação, você especifica os URIs (identificadores de recurso uniformes) que governam cada um dos VHDs que você criou para sua SKU. A Microsoft precisa de acesso a esses VHDs durante o processo de certificação. Portanto, você precisa criar um URI de assinatura de acesso compartilhado para cada VHD. Esta é o URI que deve ser inserida na guia **Imagens** no portal de publicação.

O URI da assinatura de acesso compartilhado criado deve atender aos seguintes requisitos:

* Ao gerar um URI de assinatura de acesso compartilhado para seus VHDs, as permissões de leitura e lista são suficientes. Não forneça acesso para gravação ou exclusão.
* A duração de acesso deve ser de no mínimo três (3) semanas da criação do URI de assinatura de acesso compartilhado.
* Para garantir a hora UTC, selecione o dia anterior à data atual. Por exemplo, se a data atual for 6 de outubro de 2014, selecione 5/10/2014.

A URL SAS pode ser gerada de várias maneiras para compartilhar seu VHD no Azure Marketplace.
Veja a seguiras 3 ferramentas recomendadas:

1.  Gerenciador de Armazenamento do Azure
2.  Microsoft Storage Explorer
3.  CLI do Azure

**Gerenciador de Armazenamento do Azure (recomendado para usuários do Windows)**

A seguir são mostradas as etapas para gerar a URL SAS usando o Gerenciador de Armazenamento do Azure

1. Baixe o [Gerenciador de Armazenamento do Azure 6 Visualização 3](https://azurestorageexplorer.codeplex.com/) do CodePlex. Acesse o [Gerenciador de Armazenamento do Azure 6 Visualização](https://azurestorageexplorer.codeplex.com/) e clique em **“Downloads”**.

    ![desenho](media/marketplace-publishing-vm-image-creation/img5.2_01.png)

2. Baixe [AzureStorageExplorer6Preview3.zip](https://azurestorageexplorer.codeplex.com/downloads/get/891668) e instale-lo após descompactá-lo.

    ![desenho](media/marketplace-publishing-vm-image-creation/img5.2_02.png)

3. Depois de instalado, abra o aplicativo.
4. Clique em **Adicionar Conta**.

    ![desenho](media/marketplace-publishing-vm-image-creation/img5.2_03.png)

5. Especifique o nome da conta de armazenamento, chave de conta de armazenamento e domínio de pontos de extremidade de armazenamento. Essa é a conta de armazenamento na sua assinatura do Azure na qual você manteve o VHD no Portal do Azure.

    ![desenho](media/marketplace-publishing-vm-image-creation/img5.2_04.png)

6. Depois que o Gerenciador de Armazenamento do Azure estiver conectado à sua conta de armazenamento específica, ele começará a mostrar todo o conteúdo dentro da conta de armazenamento. Selecione o contêiner em que você copiou o arquivo de VHD do disco do sistema operacional (além dos discos de dados, se eles forem aplicáveis para seu cenário).

    ![desenho](media/marketplace-publishing-vm-image-creation/img5.2_05.png)

7. Depois de selecionar o contêiner de blob, o Gerenciador de Armazenamento do Azure é iniciado mostrando os arquivos dentro do contêiner. Selecione o arquivo de imagem (.vhd) que precisa ser enviado.

    ![desenho](media/marketplace-publishing-vm-image-creation/img5.2_06.png)

8.  Após selecionar o arquivo .vhd no contêiner, clique na guia **Segurança** .

    ![desenho](media/marketplace-publishing-vm-image-creation/img5.2_07.png)

9.  Na caixa de diálogo **Segurança de Contêiner de Blob**, deixe os padrões na guia **Nível de Acesso** e clique na guia **Assinaturas de Acesso Compartilhado**.

    ![desenho](media/marketplace-publishing-vm-image-creation/img5.2_08.png)

10. Siga as etapas abaixo para gerar um URI de assinatura de acesso compartilhado para a imagem. vhd:

    ![desenho](media/marketplace-publishing-vm-image-creation/img5.2_09.png)

    a. **Acesso permitido de:** para garantir a hora UTC, selecione o dia anterior à data atual. Por exemplo, se a data atual for 6 de outubro de 2014, selecione 5/10/2014.

    b. **Acesso permitido para:** selecione uma data que seja pelo menos 3 semanas após a data em **Acesso permitido de**.

    c. **Ações permitidas:** selecione as permissões **Listagem** e **Leitura**.

    d. Se você tiver selecionado o arquivo. vhd corretamente, ele será exibido no **Nome Blob a acessar** com extensão .vhd.

    e. Clique em **Gerar Assinatura**.

    f. No **URI de Assinatura de Acesso Compartilhado Gerado desse contêiner**, verifique o seguinte conforme destacado acima:

       - Verifique se o nome do arquivo de imagem e **".vhd"** estão no URI.
       - No final da assinatura, verifique se **“= rl”** é exibido. Isso demonstra que os acessos de leitura e lista foram fornecidos com êxito.
       - No meio da assinatura, verifique se **“sr=c”** é exibido. Isso demonstra que você tem acesso ao nível de contêiner

11. Para garantir que o URI de assinatura de acesso compartilhado funciona, clique em **Testar no navegador**. Ele deve começar o processo de download.

12. Copie o URI da assinatura de acesso compartilhado. Este é o URI para colar no Portal de Publicação.

13. Repita as etapas 6 a 10 para cada VHD na SKU.

**Gerenciador de Armazenamento do Microsoft Azure (Windows/MAC/Linux)**

A seguir são mostradas as etapas para gerar a URL SAS usando o Gerenciador de Armazenamento do Microsoft Azure

1.  Baixe o formulário do Gerenciador de Armazenamento do Microsoft Azure no site [http://storageexplorer.com/](http://storageexplorer.com/). Acesse o [Gerenciador de Armazenamento do Microsoft Azure](http://storageexplorer.com/releasenotes.html) e clique em **“Baixar para Windows”**.

    ![desenho](media/marketplace-publishing-vm-image-creation/img5.2_10.png)

2.  Depois de instalado, abra o aplicativo.

3.  Clique em **Adicionar Conta**.

4.  Configure o Gerenciador de Armazenamento do Microsoft Azure para sua assinatura por entre na sua conta

    ![desenho](media/marketplace-publishing-vm-image-creation/img5.2_11.png)

5.  Acesse a conta de armazenamento e selecione o Contêiner

6.  Selecione **“Obter a assinatura de acesso ao compartilhamento...”** usando clicar com o botão direito do mouse no **contêiner**

    ![desenho](media/marketplace-publishing-vm-image-creation/img5.2_12.png)

7.  Hora de início da atualização, Hora de expiração e Permissões conforme mostrado no seguinte

    ![desenho](media/marketplace-publishing-vm-image-creation/img5.2_13.png)

    a.  **Hora de início:** para garantir a hora UTC, selecione o dia anterior à data atual. Por exemplo, se a data atual for 6 de outubro de 2014, selecione 5/10/2014.

    b.  **Hora de expiração:** selecione uma data que seja pelo menos três semanas após a data em **Hora de início**.

    c.  **Permissões:** selecione as permissões de **Listagem** e **Leitura**

8.  Copie o URI da assinatura de acesso compartilhado do contêiner

    ![desenho](media/marketplace-publishing-vm-image-creation/img5.2_14.png)

    A URL SAS é gerada para o Nível do contêiner e agora precisamos adicionar um nome de VHD a ela.

    Formato da URL SAS de nível de contêiner: `https://testrg009.blob.core.windows.net/vhds?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    Insira nome de VHD após o nome do contêiner na URL SAS conforme mostrado abaixo `https://testrg009.blob.core.windows.net/vhds/<VHD NAME>?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    Exemplo:

    ![desenho](media/marketplace-publishing-vm-image-creation/img5.2_15.png)

    TestRGVM201631920152.vhd é o nome VHD, logo a URL SAS do VHD será `https://testrg009.blob.core.windows.net/vhds/TestRGVM201631920152.vhd?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    - Verifique se o nome do arquivo de imagem e **".vhd"** estão no URI.
    - No meio da assinatura, verifique se **“sp=rl”** é exibido. Isso demonstra que os acessos de leitura e lista foram fornecidos com êxito.
    - No meio da assinatura, verifique se **“sr=c”** é exibido. Isso demonstra que você tem acesso ao nível de contêiner

9.  Para garantir que o URI de assinatura de acesso compartilhado funciona, teste-o no navegador. Ele deve iniciar o processo de download

10. Copie o URI da assinatura de acesso compartilhado. Este é o URI para colar no Portal de Publicação.

11. Repita essas etapas para cada VHD na SKU.

**CLI do Azure (recomendado para não Windows e Integração Contínua)**

A seguir são mostradas as etapas para gerar a URL SAS usando a CLI do Azure

1.  Baixe a CLI do Microsoft Azure [aqui](https://azure.microsoft.com/en-in/documentation/articles/xplat-cli-install/). Você também pode encontrar links diferentes para **[Windows](http://aka.ms/webpi-azure-cli)** e **[MAC OS](http://aka.ms/mac-azure-cli)**.

2.  Após concluir o download, instale-o

3.  Crie um arquivo do PowerShell com o código a seguir e salve-o no local

          $conn="DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<Storage Account Key>"
          azure storage container list vhds -c $conn
          azure storage container sas create vhds rl <Permission End Date> -c $conn --start <Permission Start Date>  

    Atualize os seguintes parâmetros acima

    a. **`<StorageAccountName>`**: informe o nome da conta de armazenamento

    b. **`<Storage Account Key>`**: informe a chave de conta de armazenamento

    c. **`<Permission Start Date>`**: Para garantir a hora UTC, selecione o dia anterior à data atual. Por exemplo, se a data atual for 26 de outubro de 2016, o valor deverá ser 25/10/2016

    d. **`<Permission End Date>`**: selecione uma data que seja pelo menos três semanas após a data em **Hora de Início**. O valor deverá ser **02/11/2016**.

    Veja a seguir o código de exemplo depois de atualizar os parâmetros corretos

          $conn="DefaultEndpointsProtocol=https;AccountName=st20151;AccountKey=TIQE5QWMKHpT5q2VnF1bb+NUV7NVMY2xmzVx1rdgIVsw7h0pcI5nMM6+DVFO65i4bQevx21dmrflA91r0Vh2Yw=="
          azure storage container list vhds -c $conn
          azure storage container sas create vhds rl 11/02/2016 -c $conn --start 10/25/2016  

4.  Abra o editor do Powershell com o modo de “Executar como Administrador” e abra o arquivo na etapa 3.

5.  Execute o script e ele fornecerá a URL SAS para acesso no nível de contêiner

    Veja a seguir a saída da assinatura SAS e copie a parte realçada em um bloco de notas

    ![desenho](media/marketplace-publishing-vm-image-creation/img5.2_16.png)

6.  Agora, você terá a URL SAS no nível de contêiner e precisará adicionar o nome do VHD a ela.

    No. de URL de SAS de nível de contêiner

    `https://st20151.blob.core.windows.net/vhds?st=2016-10-25T07%3A00%3A00Z&se=2016-11-02T07%3A00%3A00Z&sp=rl&sv=2015-12-11&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

7.  Insira o nome de VHD depois do nome do contêiner na URL SAS conforme mostrado abaixo `https://st20151.blob.core.windows.net/vhds/<VHDName>?st=2016-10-25T07%3A00%3A00Z&se=2016-11-02T07%3A00%3A00Z&sp=rl&sv=2015-12-11&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

    Exemplo:

    TestRGVM201631920152.vhd é o nome VHD, logo a URL SAS do VHD será

    `https://st20151.blob.core.windows.net/vhds/ TestRGVM201631920152.vhd?st=2016-10-25T07%3A00%3A00Z&se=2016-11-02T07%3A00%3A00Z&sp=rl&sv=2015-12-11&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

    - Verifique se o nome do arquivo de imagem e "VHD" estão no URI.
    -   No meio da assinatura, verifique se “sp=rl” é exibido. Isso demonstra que os acessos de leitura e lista foram fornecidos com êxito.
    -   No meio da assinatura, verifique se “sr=c” é exibido. Isso demonstra que você tem acesso ao nível de contêiner

8.  Para garantir que o URI de assinatura de acesso compartilhado funciona, teste-o no navegador. Ele deve iniciar o processo de download

9.  Copie o URI da assinatura de acesso compartilhado. Este é o URI para colar no Portal de Publicação.

10. Repita essas etapas para cada VHD na SKU.


### <a name="53-provide-information-about-the-vm-image-and-request-certification-in-the-publishing-portal"></a>5.3 Forneça informações sobre a Imagem VM e solicite a certificação no Portal de Publicação
Depois de criar sua oferta e SKU, você deve digitar os detalhes da imagem associados a essa SKU:

1. Vá para o [Portal de Publicação][link-pubportal] e entre com sua conta de vendedor.
2. Selecione a guia **Imagens VM** .
3. O identificador listado no topo da página é realmente o identificador da Oferta e não o identificador da SKU.
4. Preencha as propriedades na seção **SKUs** .
5. Em **Família do sistema operacional**, selecione o tipo de sistema operacional associado ao VHD do sistema operacional.
6. Na caixa **Sistema Operacional** , descreva o sistema operacional. Considere um formato como família do sistema operacional, tipo, versão e atualizações. Um exemplo é o “Windows Server Datacenter 2014 R2”.
7. Selecione até seis tamanhos de máquina virtual recomendados. Essas são recomendações exibidas para o cliente na folha Tipo de preço no Portal do Azure quando eles decidem comprar e implantar sua imagem. **Essas são apenas recomendações. O cliente é capaz de selecionar qualquer tamanho de VM que acomode os discos especificados em sua imagem.**
8. Insira a versão. O campo de versão encapsula uma versão semântica para identificar o produto e suas atualizações.
   * Versões devem ser no formato X.Y.Z, onde X, Y e Z são inteiros.
   * Imagens em SKUs diferentes podem ter diferentes versões principais e secundárias.
   * Versões em uma SKU só devem ser alterações incrementais, aumentando a versão de patch (Z de X.Y.Z).
9. Na caixa **URL do VHD de sistema operacional** , digite a assinatura de acesso compartilhado criado para o VHD do sistema operacional.
10. Se houver discos de dados associados a esta SKU, selecione o LUN (número de unidade lógica) para o qual você quer que este disco de dados seja montado na implantação.
11. Na caixa **URL do VHD LUN X** , digite a assinatura de acesso compartilhado criado para o VHD com os primeiros dados.

    ![desenho](media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus-3.png)


## <a name="common-sas-url-issues--fixes"></a>Problemas comuns e correções da URL de SAS

|Problema|Mensagem de Falha|Correção|Link da Documentação|
|---|---|---|---|
|Falha ao copiar imagens – "?" não foi encontrado na URL SAS|Falha: Copiando Imagens. Não é possível baixar blobs usando o URI SAS fornecido.|Atualizar a URL SAS usando as ferramentas recomendadas|[https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Falha ao copiar imagens – Os parâmetros “st” e “se” não existem na URL SAS|Falha: Copiando Imagens. Não é possível baixar blobs usando o URI SAS fornecido.|Atualizar a URL SAS com datas de Início e Término|[https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Falha ao copiar as imagens – “sp=rl” não existe URL SAS|Falha: Copiando Imagens. Não é possível baixar blobs usando o URI SAS fornecido|Atualizar a URL SAS com permissões definidas como “Leitura” e “Listagem”|[https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Falha ao copiar imagens – A URL SAS tem espaços em branco no nome do vhd|Falha: Copiando Imagens. Não é possível baixar blobs usando o URI SAS fornecido.|Atualize a URL SAS sem espaços em branco|[https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Falha ao copiar imagens – Erro de autorização da URL SAS|Falha: Copiando Imagens. Não é possível baixar o blob devido a um erro de autorização|Gere novamente a URL SAS|[https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|


## <a name="next-step"></a>Próxima etapa
Depois de concluir os detalhes do SKU, avance até o [Guia de conteúdo de marketing do Azure Marketplace][link-pushstaging]. Nessa etapa do processo de publicação, você fornece o conteúdo de marketing, o preço e outras informações necessárias antes da **Etapa 3: testando sua oferta de VM em preparo**, onde pode testar vários cenários de caso de uso antes de implantar a oferta no Azure Marketplace para visibilidade pública e compra.  

## <a name="see-also"></a>Consulte também
* [Introdução: como publicar uma oferta no Azure Marketplace](marketplace-publishing-getting-started.md)

[img-acom-1]:media/marketplace-publishing-vm-image-creation/vm-image-acom-datacenter.png
[img-portal-vm-size]:media/marketplace-publishing-vm-image-creation/vm-image-portal-size.png
[img-portal-vm-create]:media/marketplace-publishing-vm-image-creation/vm-image-portal-create-vm.png
[img-portal-vm-location]:media/marketplace-publishing-vm-image-creation/vm-image-portal-location.png
[img-portal-vm-rdp]:media/marketplace-publishing-vm-image-creation/vm-image-portal-rdp.png
[img-azstg-add]:media/marketplace-publishing-vm-image-creation/vm-image-storage-add.png
[img-manage-vm-new]:media/marketplace-publishing-vm-image-creation/vm-image-manage-new.png
[img-manage-vm-select]:media/marketplace-publishing-vm-image-creation/vm-image-manage-select.png
[img-cert-vm-key-lnx]:media/marketplace-publishing-vm-image-creation/vm-image-certification-keyfile-linux.png
[img-cert-vm-pswd-lnx]:media/marketplace-publishing-vm-image-creation/vm-image-certification-password-linux.png
[img-cert-vm-pswd-win]:media/marketplace-publishing-vm-image-creation/vm-image-certification-password-win.png
[img-cert-vm-test-lnx]:media/marketplace-publishing-vm-image-creation/vm-image-certification-test-sample-linux.png
[img-cert-vm-test-win]:media/marketplace-publishing-vm-image-creation/vm-image-certification-test-sample-win.png
[img-cert-vm-results]:media/marketplace-publishing-vm-image-creation/vm-image-certification-results.png
[img-cert-vm-questionnaire]:media/marketplace-publishing-vm-image-creation/vm-image-certification-questionnaire.png
[img-cert-vm-questionnaire-2]:media/marketplace-publishing-vm-image-creation/vm-image-certification-questionnaire-2.png
[img-pubportal-vm-skus]:media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus.png
[img-pubportal-vm-skus-2]:media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus-2.png

[link-pushstaging]:marketplace-publishing-push-to-staging.md
[link-github-waagent]:https://github.com/Azure/WALinuxAgent
[link-azure-codeplex]:https://azurestorageexplorer.codeplex.com/
[link-azure-2]:../storage/blobs/storage-dotnet-shared-access-signature-part-2.md
[link-azure-1]:../storage/common/storage-dotnet-shared-access-signature-part-1.md
[link-msft-download]:http://www.microsoft.com/download/details.aspx?id=44299
[link-technet-3]:https://technet.microsoft.com/library/hh846766.aspx
[link-technet-2]:https://msdn.microsoft.com/library/dn495261.aspx
[link-azure-portal]:https://portal.azure.com
[link-pubportal]:https://publish.windowsazure.com
[link-sql-2014-ent]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014enterprisewindowsserver2012r2/
[link-sql-2014-std]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014standardwindowsserver2012r2/
[link-sql-2014-web]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014webwindowsserver2012r2/
[link-sql-2012-ent]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2enterprisewindowsserver2012/
[link-sql-2012-std]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2standardwindowsserver2012/
[link-sql-2012-web]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2webwindowsserver2012/
[link-datactr-2012-r2]:http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012r2datacenter/
[link-datactr-2012]:http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012datacenter/
[link-datactr-2008-r2]:http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2008r2sp1/
[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
[link-technet-1]:https://technet.microsoft.com/library/hh848454.aspx
[link-azure-vm-2]:./virtual-machines-linux-agent-user-guide/
[link-openssl]:https://www.openssl.org/
[link-intsvc]:http://www.microsoft.com/download/details.aspx?id=41554
[link-python]:https://www.python.org/

---
title: Configurar o líder do projeto como laboratórios com Azure Lab Services
description: Saiba como configurar os laboratórios para ensinar ao projeto liderando as classes do Way.
ms.topic: article
ms.date: 10/28/2020
ms.openlocfilehash: ca4fdae2372895c17c4a98dd3959935108846744
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024612"
---
# <a name="set-up-labs-for-project-lead-the-way-classes"></a>Configurar laboratórios para o líder de projetos como classes

[O PLTW (líder do projeto)](https://www.pltw.org/) é uma organização sem fins lucrativos que fornece um &ndash; currículo PreK 12 em toda a Estados Unidos em ciência da computação, engenharia e ciência biomédica.  Em cada classe PLTW, os alunos usam uma variedade de aplicativos de software como parte de sua experiência prática de aprendizado.  Muitos dos aplicativos de software exigem uma CPU rápida ou, em alguns casos, uma GPU.  Este artigo mostra como configurar os laboratórios para as seguintes classes PLTW, que normalmente são oferecidas aos alunos em notas de 9 a &ndash; 12:

- **Introdução ao design de engenharia**

    Os alunos são introduzidos no processo de design de engenharia, que inclui o uso do software de [desenvolvimento do Autodesk (design auxiliado por computador)](https://www.autodesk.com/products/inventor/new-features) para modelagem 3D.

- **Princípios de engenharia**
    
    Os alunos aprendem sobre mecanismos de engenharia, tamanho estrutural e de material e automação.  Essa classe usa software como [MD sólidos](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/MD+Solids/MD+Solids+Software+Installation+Guide.pdf), designer de [ponte de ponto oeste](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/West+Point+Bridge+Builder/Installation+Guide+for+West+Point+Bridge+Designer.pdf)e [simulação de exército da América](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/America's+Army/Installation+Guide+for+Americas+Army+Simulation+17-18.pdf).

- **Engenharia e arquitetura civil**

    Os alunos aprendem a criar e desenvolver o design e o desenvolvimento de sites usando o software de design de arquitetura [Autodesk Revit](https://www.autodesk.com/products/revit/overview) para a modelagem de informações de construção 3D (BIM).

- **Fabricação integrada do computador**

    Os alunos exploram processos de fabricação modernos que envolvem robótica e automação.   Nessa classe, os alunos usam o [Autodesk inventário AutoCAD](https://www.autodesk.com/products/inventor/new-features) e o software [Cam (Manufatura auxiliada por computador) de inventário](https://www.autodesk.com/products/inventor-cam/overview) do Autodesk. 

- **Eletrônica Digital**

    Os alunos estudam circuitos e dispositivos de lógica eletrônica usando o software de design de circuito e simulação do [National Instrument Multisim](https://www.ni.com/en-us/shop/electronic-test-instrumentation/application-software-for-electronic-test-and-instrumentation-category/what-is-multisim.html) .

- **Design e desenvolvimento de engenharia**

    Os alunos contribuem para uma solução de ponta a ponta combinando pesquisa, design e testes que eles apresentam a um painel de engenheiros.  Nessa classe, os alunos usam software [CAD de inventário do Autodesk](https://www.autodesk.com/products/inventor/new-features) .

- **Noções básicas sobre ciência da computação**

    Os alunos são apresentados aos conceitos e ferramentas computacionais.  Eles começam com a programação baseada em bloco e, em seguida, passam para a codificação baseada em texto usando ambientes de codificação, como [blocos VEXcode V5](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/VEXcode+V5+Blocks/VexCode+V5+Blocks+Installation+Guide.pdf).

- **Princípios de ciência da computação**
    
    Os alunos crescem sua experiência em programação com o [Python](https://www.python.org/) usando o [ambiente de desenvolvimento de código Microsoft Visual Studio](https://code.visualstudio.com/). 

- **Ciência da computação A**

    Os alunos expandem sua competência de programação nessa classe aprendendo o desenvolvimento de aplicativos móveis.  Nessa classe, eles aprendem [Java](https://www.java.com/) usando o ambiente de [desenvolvimento de código Microsoft Visual Studio](https://code.visualstudio.com/).  Os alunos também usam um emulador que permite executar e testar o código do aplicativo móvel.  Para obter informações sobre como configurar um emulador no Azure Lab Services, [entre em contato com Azure Lab Services](mailto:AzLabsCOVIDSupport@microsoft.com).

Para obter uma lista completa de softwares de classe, acesse o [site do PLTW](https://www.pltw.org/pltw-software) para cada classe.

## <a name="lab-configuration"></a>Configuração do laboratório

Para começar a configurar os laboratórios para PLTW, você precisa de uma assinatura do Azure e uma conta de laboratório. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. 

Depois de obter uma assinatura do Azure, você poderá criar uma nova conta de laboratório no Azure Lab Services. Para obter mais informações sobre como criar uma nova conta de laboratório, consulte [Configurar uma conta de laboratório](./tutorial-setup-lab-account.md). Você também pode usar uma conta de laboratório existente.

Depois de configurar uma conta de laboratório, você deve criar um laboratório separado para cada sessão de classe PLTW que sua escola oferece.  Também recomendamos que você crie imagens separadas para cada tipo de classe PLTW.  Para obter mais informações sobre como estruturar seus laboratórios e imagens, consulte a postagem do blog [movendo-se de um laboratório físico para Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931).

### <a name="lab-account-settings"></a>Configurações de conta do laboratório

Habilite as configurações de conta do laboratório conforme descrito na tabela a seguir. Para obter mais informações sobre como habilitar as imagens do Azure Marketplace, consulte [especificar as imagens do Azure Marketplace disponíveis para os criadores de laboratório](./specify-marketplace-images.md).

| Configuração da conta de laboratório | Instruções |
| -------------------- | ----- |
| Imagem do Marketplace | Habilite a imagem do Windows 10 pro para uso em sua conta de laboratório. |

<br>

### <a name="lab-settings"></a>Configurações do laboratório
O tamanho da VM (máquina virtual) que recomendamos usar para classes PLTW depende dos tipos de cargas de trabalho que os alunos estão fazendo na classe.  Para as classes listadas anteriormente, recomendamos o uso de GPU pequena (visualização) e grandes tamanhos de VM. Conforme você configura os laboratórios para suas classes PLTW, consulte as diretrizes na tabela a seguir:

| Configuração de laboratório | Valor e descrição | Recomendação de classe |
| ------------ | ------------------ | --- |
| Tamanho da Máquina Virtual | **GPU Pequena (Visualização)**<br>Mais adequado para visualização remota, streaming, jogos e codificação com estruturas como OpenGL e DirectX. | É recomendável usar esse tamanho para as seguintes classes PLTW: Engenharia e arquitetura civis, eletrônicos digitais, fabricação integrada de computador e design e desenvolvimento de engenharia.
| Tamanho da Máquina Virtual | **Grande**<br>Mais adequado para aplicativos que precisam de CPUs mais rápidas, melhor desempenho de disco local, bancos de dados grandes e caches de memória grande. | É recomendável usar esse tamanho para as seguintes classes PLTW: introdução ao design de engenharia, princípios de engenharia, ciência da computação do computador, princípios da ciência da computação e ciência da computação A. |

<br>

### <a name="license-server"></a>Servidor de licenças
A maioria dos softwares usados nas classes PLTW mencionadas anteriormente *não exigem acesso* a um servidor de licença.  No entanto, você precisará acessar um servidor de licença se planeja usar o modelo de licenciamento de rede do Autodesk para o seguinte software:
-   Revit
-   Linguagem
-   CAM de inventário

Para usar o licenciamento de rede com o Autodesk Software, o [PLTW fornece etapas detalhadas](https://www.pltw.org/pltw-software) para instalar o Autodesk Network License Manager no seu servidor de licença.  Esse servidor de licença está localizado normalmente na sua rede local ou hospedado em uma VM (máquina virtual) do Azure dentro da rede virtual do Azure.

Depois que o servidor de licença estiver configurado, você precisará [emparelhar a rede virtual](./how-to-connect-peer-virtual-network.md) com sua [conta de laboratório](./tutorial-setup-lab-account.md). Você precisa fazer o emparelhamento de rede *antes* de criar o laboratório para que suas VMs de laboratório possam acessar o servidor de licença e vice-versa.

Os arquivos de licença gerados pelo Autodesk incorporam o endereço MAC do servidor de licença.  Se você decidir hospedar o servidor de licença usando uma VM do Azure, é importante garantir que o endereço MAC do servidor de licença não seja alterado. Se o endereço MAC for alterado, você precisará gerar novamente os arquivos de licenciamento. Para impedir que seu endereço MAC seja alterado, faça o seguinte:

- [Defina um IP privado estático e um endereço MAC](./how-to-create-a-lab-with-shared-resource.md#static-private-ip-and-mac-address) para a VM do Azure que hospeda o servidor de licença.
- Certifique-se de configurar sua conta de laboratório e a rede virtual do servidor de licença em uma região ou local que tenha capacidade de VM suficiente para que você não precise mover esses recursos para uma nova região ou local mais tarde.

Para obter mais informações, consulte [configurar um servidor de licença como um recurso compartilhado](./how-to-create-a-lab-with-shared-resource.md).

### <a name="template-machine"></a>Computador de modelo
Alguns dos arquivos de instalação que você precisa para PLTW são grandes. Quando você baixa os arquivos para uma VM de modelo de laboratório, eles podem levar muito tempo para serem copiados.

Em vez de baixar os arquivos de instalação para o computador de modelo e instalar tudo lá, é recomendável criar suas imagens PLTW em seu ambiente físico.  Em seguida, você pode importar as imagens personalizadas para a Galeria de imagens compartilhadas para que possa usá-las para criar seus laboratórios.  Para obter mais informações, consulte [carregar uma imagem personalizada na Galeria de imagens compartilhadas](./upload-custom-image-shared-image-gallery.md).

Ao seguir essa recomendação, observe as principais tarefas para configurar um laboratório:

1. Em seu ambiente físico, crie a imagem para a classe.

    a.  Use as etapas detalhadas do PLTW para baixar os arquivos de instalação e instalar o software necessário.

    > [!NOTE]    
    > Quando você instala os aplicativos Autodesk, o computador em que você os está instalando precisa ser capaz de se comunicar com o servidor de licença. O assistente de instalação do Autodesk solicitará que você especifique o nome do computador no qual o servidor de licenças está hospedado.  Se você estiver hospedando seu servidor de licença em uma VM do Azure, talvez seja necessário aguardar para instalar o Autodesk na VM de modelo de laboratório para que o assistente de instalação possa acessar o servidor de licença.

    b.  [Instale e configure o onedrive](./how-to-prepare-windows-template.md#install-and-configure-onedrive) ou outras opções de backup que sua escola pode usar.
    
    c.  [Instalar e configurar as atualizações do Windows](./how-to-prepare-windows-template.md#install-and-configure-updates).

1.  Carregue a imagem personalizada na [Galeria de imagens compartilhadas anexada à sua conta de laboratório](./how-to-attach-detach-shared-image-gallery.md).

1.  Crie um laboratório e, em seguida, selecione a imagem personalizada que você carregou na etapa anterior.

1.  Depois que o laboratório for criado, inicie e conecte-se à VM do modelo para validar que a imagem funciona conforme o esperado.

1.  Por fim, publique a VM de modelo para criar as VMs dos alunos.

## <a name="student-devices"></a>Dispositivos de aluno
Os alunos podem se conectar às VMs de laboratório de computadores Windows, Mac e Chromebook. Para obter instruções, consulte:

- [Conecte-se do Windows](./how-to-use-classroom-lab.md#connect-to-the-vm)
- [Conectar do Mac](./connect-virtual-machine-mac-remote-desktop.md)
- [Conectar do Chromebook](./connect-virtual-machine-chromebook-remote-desktop.md)

## <a name="cost"></a>Custo
Vamos abordar um exemplo de estimativa de custo para as classes PLTW.  Essa estimativa não inclui o custo de executar um servidor de licença ou usar uma galeria de imagens compartilhada. Suponha que você tenha uma classe de 25 alunos, cada um dos quais tem 20 horas de tempo de classe agendada.  Cada aluno também tem 10 horas de cota adicionais para a casa ou atribuições fora da hora da classe agendada.  Aqui estão os custos estimados:

- **VM grande**

    25 alunos &times; (20 horas agendadas + 10 horas de cota) &times; 70 unidades de laboratório &times; USD 0,01 por hora = USD 525.00

- **GPU pequena (visualização)**

    25 alunos &times; (20 horas agendadas + 10 horas de cota) &times; 160 unidades de laboratório &times; USD 0,01 por hora = USD 1200.00

> [!IMPORTANT] 
> A estimativa de custo é apenas para fins de exemplo.  Para obter informações sobre preços atuais, consulte [preços de Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

> [!NOTE] 
> Muitas das classes PLTW usam aplicativos que são acessados por meio de um navegador, como o inventário de aplicativo do MIT.  Esses aplicativos baseados em navegador não exigem uma CPU ou GPU rápida, e você pode acessá-los de qualquer dispositivo que tenha uma conexão com a Internet.  Quando os alunos estão usando esses tipos de aplicativos, recomendamos que eles usem o navegador em seu dispositivo físico em vez do navegador em sua VM de laboratório. Os alunos podem ajudar a manter os custos baixos usando sua VM de laboratório somente para aplicativos que exigem uma CPU ou GPU rápida.

## <a name="next-steps"></a>Próximas etapas

Ao configurar seu laboratório, consulte os seguintes artigos:

- [Adicionar usuários](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir cotas](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um agendamento](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Links de registro de email para alunos](how-to-configure-student-usage.md#send-invitations-to-users) 

---
title: Visão geral do instalador do pacote de ferramentas do Azure Percept dev
description: Saiba mais sobre como usar o instalador do pacote de ferramentas de desenvolvimento para acelerar o desenvolvimento avançado com o Azure Percept
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 66498fabadc0784a4a4ab1c3762daaaa9a5738c4
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503208"
---
# <a name="dev-tools-pack-installer-overview"></a>Visão geral do instalador do pacote de ferramentas de desenvolvimento

O instalador do pacote de ferramentas de desenvolvimento é uma solução única que instala e configura todas as ferramentas necessárias para desenvolver uma solução de borda inteligente. Se você já tiver instalado qualquer um dos pacotes de software listados abaixo, o instalador do pacote de ferramentas de desenvolvimento reinstalará esses pacotes para que suas ferramentas sejam consistentes com as versões do software do instalador.

## <a name="mandatory-tools-installed"></a>Ferramentas obrigatórias instaladas

* [Visual Studio Code](https://code.visualstudio.com/)
* [Python 3,6 (Windows) ou 3,5 (Linux)](https://www.python.org/)
* [Docker 19, 3](https://www.docker.com/)
* [PIP3](https://pip.pypa.io/en/stable/user_guide/)
* [TensorFlow 1,13](https://www.tensorflow.org/)
* [SDK do Azure Machine Learning 1,1](https://docs.microsoft.com/python/api/overview/azure/ml/)

## <a name="optional-tools-available-for-installation"></a>Ferramentas opcionais disponíveis para instalação

* [NVIDIA DEEPSTREAM SDK 5](https://developer.nvidia.com/deepstream-sdk) (Kit de ferramentas para desenvolver soluções para aceleradores NVIDIA)
* [Intel OpenVino toolkit 2020,2](https://docs.openvinotoolkit.org/) (Kit de ferramentas para desenvolvimento de soluções para Intel aceleradores)
* [Lobe.ai](https://lobe.ai/)  
* [Streamlit](https://www.streamlit.io/)
* [Pytorch 1.4.0 (Windows) ou 1.2.0 (Linux)](https://pytorch.org/)
* [Miniconda3](https://docs.conda.io/en/latest/miniconda.html)
* [Encadeamento 5,2](https://chainer.org/)
* [Caffe](https://caffe.berkeleyvision.org/)
* [CUDA Toolkit 10.0.130](https://developer.nvidia.com/cuda-toolkit)
* [Microsoft Cognitive Toolkit 2.5.1](https://www.microsoft.com/research/product/cognitive-toolkit/?lang=fr_ca)

## <a name="known-issues"></a>Problemas conhecidos

- A instalação opcional do Caffe poderá falhar se o Docker não estiver sendo executado corretamente no sistema. Se você quiser instalar o Caffe, verifique se o Docker está instalado e em execução antes de tentar a instalação do Caffe por meio do instalador do pacote de ferramentas de desenvolvimento. 

- A instalação opcional do CUDA falha em sistemas incompatíveis. Antes de tentar instalar o [CUDA Toolkit 10.0.130](https://developer.nvidia.com/cuda-toolkit) por meio do instalador do pacote de ferramentas de desenvolvimento, verifique a compatibilidade do sistema.

## <a name="minimum-requirements"></a>Requisitos mínimos

* Requisitos mínimos do Docker:

    * Windows:
        * https://docs.docker.com/docker-for-windows/install/#system-requirements

        - Windows 10 64-bit: pro, Enterprise ou Education (Build 16299 ou posterior).

             Para o Windows 10 Home, consulte instalar o Docker desktop no Windows Home.
           - Os recursos do Windows Hyper-V e contêineres devem ser habilitados.
           - Os seguintes pré-requisitos de hardware são necessários para executar com êxito o Hyper-V cliente no Windows 10:

              - processador de 64 bits com [slat (conversão de endereços de segundo nível)](https://en.wikipedia.org/wiki/Second_Level_Address_Translation)
              - RAM do sistema de 4 GB
              - O suporte à virtualização de hardware no nível de BIOS deve ser habilitado nas configurações do BIOS. Para obter mais informações, consulte virtualização.

        > [!NOTE]
        > O Docker dá suporte ao Docker desktop no Windows com base no ciclo de vida de suporte da Microsoft para o sistema operacional Windows 10. Para obter mais informações, consulte a [folha de fatos do ciclo de vida do Windows](https://support.microsoft.com/help/13853/windows-lifecycle-fact-sheet).

    * Mac:
        * https://docs.docker.com/docker-for-mac/install/#system-requirements
       
        Seu Mac deve atender aos seguintes requisitos para instalar com êxito a área de trabalho do Docker:
         
         - O **hardware do Mac deve ser um 2010 ou um modelo mais recente com um processador Intel**, com suporte de hardware da Intel para virtualização de MMU (unidade de gerenciamento de memória), incluindo EPT (tabelas de página estendidas) e modo irrestrito. Você pode verificar se o seu computador tem esse suporte executando o seguinte comando em um terminal: ```sysctl kern.hv_support```

        Se o seu Mac der suporte à estrutura do hipervisor, o comando será impresso ```kern.hv_support: 1``` .

         - o **MacOS deve ser a versão 10,14 ou mais recente**. Ou seja, Mojave, Catalina ou Big Sur. É recomendável atualizar para a versão mais recente do macOS.

        Se você tiver problemas após a atualização do macOS para a versão 10,15, instale a versão mais recente da área de trabalho do Docker para ser compatível com esta versão do macOS.

        - Pelo menos 4 GB de RAM.
        - O VirtualBox anterior à versão 4.3.30 não deve ser instalado, pois não é compatível com o Docker desktop.

        > [!NOTE]
        > O Docker dá suporte à área de trabalho do Docker nas versões mais recentes do macOS. Ou seja, a versão atual do macOS e as duas versões anteriores. Como novas versões principais do macOS são disponibilizadas para o público geral, o Docker para de dar suporte à versão mais antiga e dá suporte à versão mais recente do macOS (além das duas versões anteriores). O Docker desktop atualmente dá suporte a macOS Mojave, macOS Catalina e macOS Big Sur.
        > 
        - Não há suporte para o instalador no Apple M1.

## <a name="instructions"></a>Instruções

1. Baixe o instalador do pacote de ferramentas de desenvolvimento para [Windows](https://go.microsoft.com/fwlink/?linkid=2132187), [Linux](https://go.microsoft.com/fwlink/?linkid=2132186)e [Mac](https://go.microsoft.com/fwlink/?linkid=2132296).

1. Dependendo da sua plataforma, haverá algumas diferenças na inicialização do instalador.

    1. Para Windows:
    
        1. Clique em **dev-Tools-Pack-Installer** para abrir o assistente de instalação.
        
    1. Para Mac:
    
        1. Após o download, mova o arquivo Dev-Tools-Pack-Installer. app para a pasta aplicativos.
        
        1. Clique em **dev-Tools-Pack-Installer. app** para abrir o assistente de instalação.
        
        1. Se você receber uma caixa de diálogo de segurança "desenvolvedor não identificado":
        
            1. Vá para preferências do sistema-> segurança & privacidade-> geral e clique no botão "abrir mesmo assim" ao lado de "Dev-Tools-Pack-Installer. app"
        
            1. Clique no ícone de baixo para cima no encaixe novamente
        
            1. Clique no botão "abrir" na caixa de diálogo de segurança
    
    1. Para Linux:
    
        1. Quando solicitado pelo navegador, clique em "salvar" para concluir o download do instalador
        
        1. Adicione permissões de execução ao método 1 de arquivo **. appimage** (CommandLine):
            
            1. Abrir o terminal do Linux
            
            1. Digite o seguinte no terminal para ir para a pasta de downloads
            
                1. CD ~/downloads/
                
            1. Digite o seguinte no terminal para tornar o executável AppImage
            
                1. chmod + x **dev-Tools-Pack-Installer. AppImage**
                
            1. Digite o seguinte no terminal para executar o instalador
            
                1. ./Dev-Tools-Pack-Installer.AppImage
        
        1. Adicione permissões de execução ao método 2 (UI) do arquivo **. appimage** :
        
            1. Clique com o botão direito do mouse no arquivo. appimage e selecione Propriedades
            
            1. Abrir guia permissões
            
            1. Marque a caixa ' permitir execução de arquivo como um programa '
            
            1. Feche as propriedades e abra o arquivo. appimage

1. Na página **instalar instalador do pacote de ferramentas de desenvolvimento** , clique em **Exibir licença** para exibir os contratos de licença de cada pacote de software incluído no instalador. Se você aceitar os termos nos contratos de licença, marque a caixa e clique em **Avançar**.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-license-agreements.png" alt-text="Tela do contrato de licença no instalador.":::

1. Clique em **política de privacidade** para examinar a política de privacidade da Microsoft. Se você concordar com os termos da política de privacidade e quiser enviar dados de diagnóstico para a Microsoft, selecione **Sim** e clique em **Avançar**. Caso contrário, selecione **não** e clique em **Avançar**.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-privacy-statement.png" alt-text="Tela de contrato de política de privacidade no instalador.":::

1. Na página **configurar componentes** , selecione as ferramentas opcionais que você deseja instalar (as ferramentas obrigatórias serão instaladas por padrão).

    1. Se você estiver trabalhando com o SoM de áudio do Azure Percept, que faz parte do Percept do Azure DK, instale o kit de ferramentas do OpenVino Intel e o Miniconda3.

    1. Clique em **instalar** para prosseguir com a instalação.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-configure-components.png" alt-text="Tela do instalador mostrando pacotes de software disponíveis.":::

1. Após a instalação bem-sucedida de todos os componentes selecionados, o assistente prosseguirá para a página **concluindo o assistente para instalação** . Clique em **concluir** para sair do instalador.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-finish.png" alt-text="Tela de conclusão do instalador.":::

## <a name="docker-status-check"></a>Verificação de status do Docker

Se o instalador notificar você para verificar se o Docker Desktop está em um bom estado de execução, consulte as seguintes etapas:

   1. Windows:
   
      1. Expanda os ícones ocultos da bandeja do sistema:
      
         1. Expanda os ícones ocultos da bandeja do sistema se estiverem ocultos:

            :::image type="content" source="./media/dev-tools-installer/system-tray.png" alt-text="Bandeja do sistema.":::
         
         1. Verifique se o ícone de área de trabalho do Docker mostra ' o Docker Desktop está em execução ':

            :::image type="content" source="./media/dev-tools-installer/docker-status-running.png" alt-text="Status do Docker.":::
         
         1. Se você não vir o ícone acima listado na bandeja do sistema, inicie o Docker desktop no menu iniciar.
         
         1. Se o Docker solicitar a reinicialização, é bom fechar o instalador e reiniciá-lo após a conclusão de uma reinicialização e o Docker está em estado de execução. Todos os aplicativos de terceiros instalados com êxito devem ser detectados e não serão reinstalados automaticamente.

## <a name="next-steps"></a>Próximas etapas

Confira o [repositório de desenvolvimento avançado do Azure Percept](https://github.com/microsoft/azure-percept-advanced-development) para começar a usar o desenvolvimento avançado para o Azure Percept DK.
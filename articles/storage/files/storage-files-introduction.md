---
title: Introdução aos Arquivos do Azure | Microsoft Docs
description: Uma visão geral dos Arquivos do Azure, um serviço que permite criar e usar compartilhamentos de arquivos de rede na nuvem usando o protocolo SMB padrão do setor.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7279c5578d969db9178ce85734e3a43f9eccdf8d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780039"
---
# <a name="what-is-azure-files"></a>O que são os Arquivos do Azure?
Os Arquivos do Azure oferecem compartilhamentos de arquivos totalmente gerenciados na nuvem que são acessíveis por meio do [Protocolo SMB](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) ou do [Protocolo NFS (Network File System)](https://en.wikipedia.org/wiki/Network_File_System) padrão do setor. Os compartilhamentos de arquivos do Azure podem ser montados de maneira simultânea por implantações locais ou na nuvem. É possível acessar os compartilhamentos de arquivos do protocolo SMB dos Arquivos do Azure em clientes Windows, Linux e macOS. É possível acessar os compartilhamentos de arquivos do protocolo NFS dos Arquivos do Azure em clientes Linux e macOS. Além disso, os compartilhamentos de arquivos do protocolo SMB dos Arquivos do Azure podem ser armazenados em cache nos Windows Servers com a Sincronização de Arquivos do Azure para acesso rápido perto de onde os dados estão sendo usados.

Veja alguns vídeos sobre os casos de uso comuns dos Arquivos do Azure:
* [Substitua o servidor de arquivos por um Compartilhamento de Arquivo do Azure sem servidor](https://sec.ch9.ms/ch9/3358/0addac01-3606-4e30-ad7b-f195f3ab3358/ITOpsTalkAzureFiles_high.mp4)
* [Introdução a contêineres de perfil FSLogix nos Arquivos do Azure na Área de Trabalho Virtual do Windows usando a autenticação do AD](https://www.youtube.com/embed/9S5A1IJqfOQ)

## <a name="why-azure-files-is-useful"></a>Por que o serviço Arquivos do Azure é útil
Os compartilhamentos de arquivos do Azure podem ser usados para:

* **Substituir ou complementar os servidores de arquivos locais**:  
    Os Arquivos do Azure podem ser usados para substituir completamente ou complementar os servidores de arquivos tradicionais no local ou dispositivos NAS. Sistemas operacionais conhecidos, como o Linux, Windows e macOS podem montar diretamente compartilhamentos de arquivos do Azure em qualquer lugar do mundo. Os compartilhamentos de arquivos do protocolo SMB dos Arquivos do Azure também podem ser replicados com a Sincronização de Arquivos do Azure para Windows Servers, no local ou na nuvem, para armazenamento dos dados em cache distribuído e com desempenho onde estão sendo usados. Com o recente lançamento da [Autenticação do AD para os Arquivos do Azure](storage-files-active-directory-overview.md), os compartilhamentos de arquivos do protocolo SMB dos Arquivos do Azure podem continuar funcionando com o AD hospedado localmente para o controle de acesso. 

* **Aplicativos de "Deslocamento e comparação"** :  
    O serviço Arquivos do Azure facilita "comparar e deslocar" aplicativos para a nuvem que espera que o compartilhamento de arquivos armazene o aplicativo de arquivo ou os dados do usuário. O serviço Arquivos do Azure permite o cenário “clássico” de comparar e deslocar, em que o aplicativo e os dados são movidos para o Azure, e o cenário “híbrido”, em que os dados do aplicativo são movidos para o serviço Arquivos do Azure e o aplicativo continua a ser executado no local. 

* **Simplificar o desenvolvimento na nuvem**:  
    Os Arquivos do Azure também podem ser usados de várias maneiras para simplificar novos projetos de desenvolvimento na nuvem. Por exemplo:
    * **Configurações de aplicativo compartilhado**:  
        Um padrão comum para aplicativos distribuídos é fazer com que os arquivos de configuração fiquem em um local centralizado, onde possam ser acessados de diversas instâncias de aplicativos. As instâncias do aplicativo podem carregar sua configuração por meio da API REST de Arquivo e os usuários podem acessá-las conforme necessário com a montagem local do compartilhamento SMB.

    * **Compartilhamento de diagnóstico**:  
        Um compartilhamento de arquivos do Azure é um local conveniente para aplicativos de nuvem gravarem seus logs, métricas e despejos de memória. Os logs podem ser gravados pelas instâncias do aplicativo por meio da API REST de Arquivo e os desenvolvedores podem acessá-los ao montar o compartilhamento de arquivos em seu computador local. Isso permite maior flexibilidade, já que os desenvolvedores podem adotar o desenvolvimento em nuvem sem a necessidade de abandonar as ferramentas existentes que conhecemos e amamos.

    * **Desenv/Teste/Depuração**:  
        Quando desenvolvedores ou administradores estão trabalhando em máquinas virtuais na nuvem, eles frequentemente precisam de um conjunto de ferramentas ou utilitários. Copiar tais ferramentas e utilitários em cada VM pode ser uma atividade demorada. Ao montar um compartilhamento de arquivos do Azure localmente nas máquinas virtuais, o desenvolvedor e o administrador podem acessar rapidamente suas ferramentas e utilitários sem precisar copiá-las.
* **Transporte em contêineres**:  
    Os compartilhamentos de arquivos do Azure podem ser usados como volumes persistentes para contêineres com estado. Os contêineres oferecem recursos do tipo "compilar uma vez, executar em qualquer lugar", que permitem aos desenvolvedores acelerar a inovação. Para os contêineres que acessam dados brutos a cada início, é necessário um sistema de arquivos compartilhado para permitir que esses contêineres acessem o sistema de arquivos, independentemente da instância em que eles são executados.

## <a name="key-benefits"></a>Principais benefícios
* **Acesso compartilhado**. Os compartilhamentos de arquivos do Azure são compatíveis com os protocolos SMB e NFS padrão. Isso significa que você pode substituir facilmente seus compartilhamentos de arquivos locais pelos do Azure sem se preocupar com a compatibilidade do aplicativo. Poder compartilhar um sistema de arquivos em vários computadores, aplicativos e instâncias é uma vantagem significativa com os Arquivos do Azure para aplicativos que precisam de compartilhamento. 
* **Totalmente gerenciado**. Os compartilhamentos de arquivos do Azure podem ser criados sem a necessidade de gerenciar um sistema operacional ou hardware. Isso significa que você não precisa lidar com a correção do sistema operacional do servidor com atualizações críticas de segurança ou com a substituição de discos rígidos com defeito.
* **Scripts e ferramentas**. Os cmdlets do PowerShell e a CLI do Azure podem ser usados para criar, montar e gerenciar compartilhamentos de arquivos do Azure como parte da administração de aplicativos do Azure. Você pode criar e gerenciar compartilhamentos de arquivos do Azure usando o portal do Azure e o Gerenciador de Armazenamento do Azure. 
* **Resiliência**. O serviço Arquivos do Azure foi criado do zero para estar sempre disponível. Substituir os compartilhamentos de arquivos locais pelos Arquivos do Azure significa que não é preciso estar ativado para lidar com interrupções locais de energia ou problemas de rede. 
* **Programação familiar**. Os aplicativos executados no Azure podem acessar dados no compartilhamento por meio de [APIs de E/S do sistema](/dotnet/api/system.io.file) de arquivos. Os desenvolvedores podem, portanto, utilizar seus códigos e habilidades existentes para migrar aplicativos existentes. Além das APIs de E/S do sistema, você pode usar as [Bibliotecas do Cliente de Armazenamento do Azure](/previous-versions/azure/dn261237(v=azure.100)) ou a [API de REST do Armazenamento do Azure](/rest/api/storageservices/file-service-rest-api).

## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre os protocolos de compartilhamento de arquivo disponíveis](storage-files-compare-protocols.md)
* [Criar um compartilhamento de arquivos do Azure](storage-how-to-create-file-share.md)
* [Conectar e montar um compartilhamento SMB no Windows](storage-how-to-use-files-windows.md)
* [Conectar e montar um compartilhamento SMB no Linux](storage-how-to-use-files-linux.md)
* [Conectar e montar um compartilhamento SMB no macOS](storage-how-to-use-files-mac.md)
* [Como criar um compartilhamento NFS](storage-files-how-to-create-nfs-shares.md)

---
title: 'Tutorial: Copiar de VHDs para discos gerenciados'
titleSuffix: Azure Data Box
description: Saiba como copiar dados de VHDs de cargas de trabalho de VM local para o Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 10/20/2019
ms.author: alkohli
ms.openlocfilehash: 1394cf6511a65a0e406e51229953e8666d4d4d8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94337671"
---
# <a name="tutorial-use-data-box-to-import-data-as-managed-disks-in-azure"></a>Tutorial: Usar o Data Box para importar dados como discos gerenciados no Azure

Este tutorial descreve como usar o Azure Data Box para migrar VHDs locais para discos gerenciados no Azure. Os VHDs de VMs locais são copiados para o Data Box como blobs de páginas e transferidos por upload para o Azure como discos gerenciados. Em seguida, esses discos gerenciados podem ser anexados a VMs do Azure.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Examinar os pré-requisitos
> * Conectar-se à caixa de dados
> * Copiar dados para caixa de dados

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se:

1. Você concluiu o [Tutorial: Configurar o Azure Data Box](data-box-deploy-set-up.md).
2. Você recebeu seu Data Box e o status do pedido no portal está como **Entregue**.
3. Você está conectado a uma rede de alta velocidade. É altamente recomendável que você tenha pelo menos uma conexão de 10 GbE. Se não houver uma conexão de 10 GbE disponível, use um link de dados de 1 GbE, mas as velocidades de cópia são afetadas.
4. Você revisou:

    - [Tamanhos compatíveis de disco gerenciado nos limites de tamanho de objeto do Azure](data-box-limits.md#azure-object-size-limits).
    - [Introdução aos discos gerenciados do Azure](../virtual-machines/managed-disks-overview.md). 

5. Você manteve uma cópia dos dados de origem até ter confirmado que o Data Box transferiu os seus dados para o Armazenamento do Azure.

## <a name="connect-to-data-box"></a>Conectar-se à caixa de dados

Com base nos grupos de recursos especificados, o Data Box cria um compartilhamento para cada grupo de recursos associado. Por exemplo, se `mydbmdrg1` e `mydbmdrg2` tiverem sido criados ao fazer o pedido, os seguintes compartilhamentos serão criados:

- `mydbmdrg1_MDisk`
- `mydbmdrg2_MDisk`

Dentro de cada compartilhamento, são criadas as três pastas a seguir, que correspondem aos contêineres na conta de armazenamento.

- SSD Premium
- HDD Standard
- SSD Standard

A tabela a seguir mostra os caminhos UNC para os compartilhamentos em seu Data Box.
 
|        Protocolo de conexão           |             Caminho UNC para o compartilhamento                                               |
|-------------------|--------------------------------------------------------------------------------|
| SMB |`\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<PremiumSSD>\file1.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<StandardHDD>\file2.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<StandardSSD>\file3.vhd` |  
| NFS |`//<DeviceIPAddress>/<ResourceGroup1_MDisk>/<PremiumSSD>/file1.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<StandardHDD>/file2.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<StandardSSD>/file3.vhd` |

As etapas da conexão são diferentes, dependendo de você usar SMB ou NFS para se conectar a compartilhamentos do Data Box.

> [!NOTE]
> Não há suporte para conexão via REST nesse recurso.

### <a name="connect-to-data-box-via-smb"></a>Conectar-se ao Data Box via SMB

Se estiver usando um computador host do Windows Server, siga estas etapas para conectar-se ao Data Box.

1. O primeiro passo é autenticar e iniciar uma sessão. Vá para **conectar e copiar**. Clique em **Obter credenciais** para obter as credenciais de acesso aos compartilhamentos associados ao seu grupo de recursos. Também é possível obter as credenciais de acesso nos **Detalhes do dispositivo**, no portal do Azure.

    > [!NOTE]
    > As credenciais para todos os compartilhamentos para discos gerenciados são idênticas.

    ![Conectar e copiar – Obter credenciais de compartilhamento](media/data-box-deploy-copy-data-from-vhds/get-share-credentials1.png)

2. Na caixa de diálogo **Acessar a opção compartilhar e copiar dados**, copie o **Nome de usuário** e a **Senha** para executar o compartilhamento. Clique em **OK**.
    
    ![Conectar e copiar – Copiar as credenciais de compartilhamento](media/data-box-deploy-copy-data-from-vhds/get-share-credentials2.png)

3. Para acessar os compartilhamentos associados ao seu recurso (*mydbmdrg1* no exemplo a seguir) no computador host, abra uma janela Comando. No prompt de comando, digite:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Seus caminhos de compartilhamento UNC neste exemplo são:

    - `\\169.254.250.200\mydbmdrg1_MDisk`
    - `\\169.254.250.200\mydbmdrg2_MDisk`
    
4. Quando solicitado, digite a senha do compartilhamento. A amostra a seguir mostra a conexão a um compartilhamento por meio do comando anterior.

    ```
    C:\>net use \\169.254.250.200\mydbmdrgl_MDisk /u:mdisk
    Enter the password for 'mdisk' to connect to '169.254.250.200':
    The command completed successfully.
    C: \>
    ```

5. Pressione Windows + R. Na janela **Executar**, especifique o `\\<device IP address>\<ShareName>`. Clique em **OK** para abrir o Explorador de Arquivos.
    
    ![Conectar-se ao compartilhamento por meio do Explorador de Arquivos](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer1.png)

    Agora, você deve ver as pastas pré-criadas a seguir dentro de cada compartilhamento.
    
    ![Conectar-se para compartilhar por meio do Explorador de Arquivos e pastas de compartilhamento](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer2.png)


### <a name="connect-to-data-box-via-nfs"></a>Conectar-se ao Data Box via NFS

Se você estiver usando um computador host Linux, execute as seguintes etapas para configurar o Data Box para permitir o acesso aos clientes NFS.

1. Forneça os endereços IP dos clientes permitidos que podem acessar o compartilhamento. Na IU da Web local, acesse a página **Conectar e copiar**. Sob **as configurações de NFS**, clique em **acesso para cliente NFS**.

    ![Configurar o acesso do cliente NFS](media/data-box-deploy-copy-data-from-vhds/nfs-client-access1.png)

2. Forneça o endereço IP do cliente NFS e clique em **Add**. Você pode configurar o acesso para vários clientes NFS repetindo essa etapa. Clique em **OK**.

    ![Configurar o endereço IP de cliente NFS](media/data-box-deploy-copy-data-from-vhds/nfs-client-access2.png)

2. Assegure-se de que o computador host Linux tenha uma [versão suportada](data-box-system-requirements.md) do cliente NFS instalado. Use a versão específica para sua distribuição do Linux.

3. Depois que o cliente NFS for instalado, use o seguinte comando para montar o compartilhamento NFS em seu dispositivo Data Box:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    O exemplo a seguir mostra como se conectar via NFS a um compartilhamento Caixa de Dados. O IP do dispositivo Data Box é `169.254.250.200`, o compartilhamento `mydbmdrg1_MDisk` é montado no ubuntuVM, sendo o ponto de montagem`/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 169.254.250.200:/mydbmdrg1_MDisk /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box"></a>Copiar dados para caixa de dados

Depois da conexão ao servidor de dados, a próxima etapa será copiar dados. O arquivo VHD é copiado para a conta de armazenamento de preparo como blobs de páginas. Em seguida, o blob de páginas é convertido em um disco gerenciado e movido para um grupo de recursos.

Examine as considerações a seguir antes de começar a copiar os dados:

- Sempre copie os VHDs para uma das pastas pré-criadas. Se você copiar os VHDs fora dessas pastas ou em uma pasta que você criou, os VHDs serão carregados na conta do Armazenamento do Microsoft Azure como blobs de páginas e não como discos gerenciados.
- Apenas os VHDs fixos podem ser carregados para criar discos gerenciados. Os arquivos VHDX ou VHDs dinâmicos e diferenciais não têm suporte.
- Só é possível ter um disco gerenciado com um determinado nome em um grupo de recursos em todas as pastas pré-criadas. Isso implica que os VHDs enviados para as pastas pré-criadas deverão ter nomes exclusivos. Certifique-se de que o nome determinado não corresponda a um disco gerenciado já existente em um grupo de recursos.
- Revise [Limites de disco gerenciado em limites de tamanho de objeto do Azure](data-box-limits.md#azure-object-size-limits).

Se você estiver se conectando via NFS ou SMB, pode usar:

- [Copiar dados via SMB](data-box-deploy-copy-data.md#copy-data-to-data-box)
- [Copiar dados via NFS](data-box-deploy-copy-data-via-nfs.md#copy-data-to-data-box)

Aguarde a conclusão dos trabalhos de cópia. Confira se os trabalhos de cópia foram concluídos sem erros antes de ir para a próxima etapa.

![Nenhum erro na página **Conectar e copiar**](media/data-box-deploy-copy-data-from-vhds/verify-no-errors-connect-and-copy.png)

Se houver erros durante o processo de cópia, faça o download dos logs na página **Conectar e copiar**.

- Se você tiver copiado um arquivo que não tem 512 bytes alinhados, ele não será transferido por upload como um blob de páginas para sua conta de armazenamento de preparo. Você verá um erro nos logs. Remova o arquivo e copiar um arquivo que tenha 512 bytes alinhados.

- Se você tiver copiado um VHDX (não há suporte para esses arquivos) com um nome longo, verá um erro nos logs.

    ![Erro nos logs da página **Conectar e copiar**](media/data-box-deploy-copy-data-from-vhds/errors-connect-and-copy.png)

    Resolva os erros antes de prosseguir para a próxima etapa.

Para garantir a integridade dos dados, a soma de verificação é computada em linha à medida que os dados são copiados. Quando a cópia estiver concluída, verifique o espaço usado e o espaço livre no seu dispositivo.
    
![Verificar o espaço livre e usado no painel](media/data-box-deploy-copy-data-from-vhds/verify-used-space-dashboard.png)

Após a conclusão do trabalho de cópia, será possível acessar **Preparar para o envio**.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos do Azure Data Box como:

> [!div class="checklist"]
> * Examinar os pré-requisitos
> * Conectar-se à caixa de dados
> * Copiar dados para caixa de dados


Avance para o próximo tutorial para saber como enviar o Data Box novamente à Microsoft.

> [!div class="nextstepaction"]
> [Envie o Azure Data Box para a Microsoft](./data-box-deploy-picked-up.md)
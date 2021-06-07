---
title: Administrar Azure Data Box/Azure Data Box Heavy usando a interface do usuário da Web local
description: Descreve como usar a interface do usuário da Web local para administrar seus dispositivos Data Box e Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 12/18/2020
ms.author: alkohli
ms.openlocfilehash: e84f39fa5b9245d1874e60d651156e99c0885040
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97678914"
---
# <a name="use-the-local-web-ui-to-administer-your-data-box-and-data-box-heavy"></a>Use a interface do usuário da Web local para administrar seu Data Box e Data Box Heavy

Este artigo descreve algumas das tarefas de configuração e gerenciamento executadas em dispositivos Data Box e Data Box Heavy. Você pode gerenciar os dispositivos Data Box e Data Box Heavy por meio da interface do usuário do portal do Azure e da interface do usuário da Web local do dispositivo. Este artigo se concentra nas tarefas executadas usando a interface do usuário da Web local.

A interface do usuário da Web local para o Data Box e para Data Box Heavy é usada para a configuração inicial do dispositivo. Você também pode usar a interface do usuário da Web local para desligar ou reiniciar o dispositivo, executar testes de diagnóstico, atualizar software, exibir logs de cópia, apagar dados locais do dispositivo e gerar um pacote de suporte para Suporte da Microsoft. Em um dispositivo Data Box Heavy com dois nós independentes, você pode acessar duas interfaces do site locais separadas correspondentes a cada nó do dispositivo.

## <a name="generate-support-package"></a>Gerar pacote de suporte

Se você enfrentar problemas no dispositivo, crie um pacote de suporte com os logs do sistema. O Suporte da Microsoft usará esse pacote para solucionar o problema.

Para gerar um pacote de suporte, execute as seguintes etapas:

1. Na interface do usuário da Web local, acesse **contatar o suporte**. Opcionalmente, selecione **incluir despejos de memória**. Em seguida, selecione **criar pacote de suporte**.

    Um despejo de memória é o conteúdo da memória do dispositivo, salvo após uma falha do sistema.

    Você não deve selecionar a opção **incluir despejos de memória** , a menos que o suporte solicite um. Leva muito tempo para reunir um pacote de suporte que inclui despejos de memória e dados confidenciais são incluídos.

    ![Criar pacote de suporte 1](media/data-box-local-web-ui-admin/create-support-package-1.png)

    Um pacote de suporte será coletado. Essa operação levará alguns minutos se você incluir apenas os logs do sistema. Se você incluir despejos de memória, levará muito mais tempo.

    ![Criar pacote de suporte 2](media/data-box-local-web-ui-admin/create-support-package-2.png)

2. Quando a criação do pacote de suporte for concluída, selecione **baixar pacote de suporte**.

    ![Criar pacote de suporte 3](media/data-box-local-web-ui-admin/create-support-package-3.png)

3. Procure e escolha o local de download. Abra a pasta para exibir o conteúdo.

    ![Criar pacote de suporte 4](media/data-box-local-web-ui-admin/create-support-package-4.png)

## <a name="erase-local-data-from-your-device"></a>Apagar dados locais do seu dispositivo

Você pode usar a interface do usuário da Web local para apagar dados locais do seu dispositivo antes de retorná-lo para o datacenter do Azure.

> [!IMPORTANT]
> Uma apagamento de dados não pode ser revertida. Antes de apagar os dados locais do seu dispositivo, certifique-se de fazer backup dos arquivos.

Para apagar dados locais do seu dispositivo, execute estas etapas:

1. Na interface do usuário da Web local, vá para **apagamento de dados**.
2. Insira a senha do dispositivo e selecione **apagar dados**.

    ![Opção de apagamento de dados para um dispositivo](media/data-box-local-web-ui-admin/erase-local-data-1.png)

3. No prompt de confirmação, selecione **Sim** para continuar. Uma exclusão de dados pode levar até 50 minutos.

   Certifique-se de fazer backup dos dados locais antes de apagá-los do dispositivo. Uma apagamento de dados não pode ser revertida.

    ![Prompt de confirmação de apagamento de dados](media/data-box-local-web-ui-admin/erase-local-data-2.png)

## <a name="shut-down-or-restart-your-device"></a>Desligar ou reiniciar seu dispositivo

Você pode desligar ou reiniciar o dispositivo usando a interface do usuário da Web local. É recomendável que, antes de reiniciar, você coloque os compartilhamentos offline no host e, em seguida, no dispositivo. Isso minimiza a possibilidade de dados corrompidos. Certifique-se de que a cópia de dados não esteja em andamento quando você desligar o dispositivo.

Para desligar o dispositivo, execute as etapas a seguir.

1. Na interface do usuário da Web local, acesse **Desligar ou reiniciar**.

2. Selecione **Desligar**.

    ![Desligar o Data Box 1](media/data-box-local-web-ui-admin/shut-down-local-web-ui-1.png)

3. Quando a confirmação for solicitada, selecione **OK** para continuar.

    ![Desligar o Data Box 2](media/data-box-local-web-ui-admin/shut-down-local-web-ui-2.png)

Depois que o dispositivo for desligado, use o botão de energia no painel frontal para ligar o dispositivo.

Para reiniciar o Data Box, execute as seguintes etapas.

1. Na interface do usuário da Web local, acesse **Desligar ou reiniciar**.
2. Selecione **Reiniciar**.

    ![Reiniciar o Data Box 1](media/data-box-local-web-ui-admin/restart-local-web-ui-1.png)

3. Quando a confirmação for solicitada, selecione **OK** para continuar.

   O dispositivo será desligado e, em seguida, reiniciado.

## <a name="download-bom-or-manifest-files"></a>Baixar arquivos de manifesto ou BOM

A BOM ou os arquivos de manifesto contêm a lista dos arquivos que são copiados para o Data Box ou Data Box Heavy. Esses arquivos são gerados para uma ordem de importação quando você prepara o dispositivo para envio.

Antes de começar, siga estas etapas para baixar a BOM ou os arquivos de manifesto para sua ordem de importação:

1. Vá para a interface do usuário da Web local do seu dispositivo. Verifique se o dispositivo concluiu a etapa de **preparação para o envio** . Quando a preparação do dispositivo for concluída, o status do dispositivo será exibido como **Pronto para envio**.

    ![O dispositivo está pronto para envio](media/data-box-local-web-ui-admin/prepare-to-ship-3.png)

2. Selecione **baixar lista de arquivos** para baixar a lista de arquivos que foram copiados no seu data box.

    <!-- ![Select Download list of files](media/data-box-portal-admin/download-list-of-files.png) -->

3. No explorador de arquivos, são geradas listas separadas de arquivos dependendo do protocolo usado para se conectar ao dispositivo e ao tipo de armazenamento do Azure usado.

    <!-- ![Files for storage type and connection protocol](media/data-box-portal-admin/files-storage-connection-type.png) -->
    ![Arquivos para o protocolo de conexão e o tipo de armazenamento](media/data-box-local-web-ui-admin/prepare-to-ship-5.png)

   A tabela a seguir mapeia os nomes de arquivo para o tipo de Armazenamento do Azure e o protocolo de conexão usado.

    |Nome do arquivo  |Tipo de Armazenamento do Azure  |Protocolo de conexão usado |
    |---------|---------|---------|
    |utSAC1_202006051000_BlockBlob-BOM.txt     |Blobs de bloco         |SMB/NFS         |
    |utSAC1_202006051000_PageBlob-BOM.txt     |Blobs de páginas         |SMB/NFS         |
    |utSAC1_202006051000_AzFile-BOM.txt    |Arquivos do Azure         |SMB/NFS         |
    |utsac1_PageBlock_Rest-BOM.txt     |Blobs de páginas         |REST        |
    |utsac1_BlockBlock_Rest-BOM.txt    |Blobs de bloco         |REST         |

Você usa esta lista para verificar os arquivos carregados na conta de Armazenamento do Azure depois que o Data Box retorna para o datacenter do Azure. Abaixo é mostrado um exemplo de arquivo de manifesto.

> [!NOTE]
> Em um Data Box Heavy, dois conjuntos de arquivos (arquivos da BOM) estão presentes correspondentes aos dois nós no dispositivo.

```xml
<file size="52689" crc64="0x95a62e3f2095181e">\databox\media\data-box-deploy-copy-data\prepare-to-ship2.png</file>
<file size="22117" crc64="0x9b160c2c43ab6869">\databox\media\data-box-deploy-copy-data\connect-shares-file-explorer2.png</file>
<file size="57159" crc64="0x1caa82004e0053a4">\databox\media\data-box-deploy-copy-data\verify-used-space-dashboard.png</file>
<file size="24777" crc64="0x3e0db0cd1ad438e0">\databox\media\data-box-deploy-copy-data\prepare-to-ship5.png</file>
<file size="162006" crc64="0x9ceacb612ecb59d6">\databox\media\data-box-cable-options\cabling-dhcp-data-only.png</file>
<file size="155066" crc64="0x051a08d36980f5bc">\databox\media\data-box-cable-options\cabling-2-port-setup.png</file>
<file size="150399" crc64="0x66c5894ff328c0b1">\databox\media\data-box-cable-options\cabling-with-switch-static-ip.png</file>
<file size="158082" crc64="0xbd4b4c5103a783ea">\databox\media\data-box-cable-options\cabling-mgmt-only.png</file>
<file size="148456" crc64="0xa461ad24c8e4344a">\databox\media\data-box-cable-options\cabling-with-static-ip.png</file>
<file size="40417" crc64="0x637f59dd10d032b3">\databox\media\data-box-portal-admin\delete-order1.png</file>
<file size="33704" crc64="0x388546569ea9a29f">\databox\media\data-box-portal-admin\clone-order1.png</file>
<file size="5757" crc64="0x9979df75ee9be91e">\databox\media\data-box-safety\japan.png</file>
<file size="998" crc64="0xc10c5a1863c5f88f">\databox\media\data-box-safety\overload_tip_hazard_icon.png</file>
<file size="5870" crc64="0x4aec2377bb16136d">\databox\media\data-box-safety\south-korea.png</file>
<file size="16572" crc64="0x05b13500a1385a87">\databox\media\data-box-safety\taiwan.png</file>
<file size="999" crc64="0x3f3f1c5c596a4920">\databox\media\data-box-safety\warning_icon.png</file>
<file size="1054" crc64="0x24911140d7487311">\databox\media\data-box-safety\read_safety_and_health_information_icon.png</file>
<file size="1258" crc64="0xc00a2d5480f4fcec">\databox\media\data-box-safety\heavy_weight_hazard_icon.png</file>
<file size="1672" crc64="0x4ae5cfa67c0e895a">\databox\media\data-box-safety\no_user_serviceable_parts_icon.png</file>
<file size="3577" crc64="0x99e3d9df341b62eb">\databox\media\data-box-safety\battery_disposal_icon.png</file>
<file size="993" crc64="0x5a1a78a399840a17">\databox\media\data-box-safety\tip_hazard_icon.png</file>
<file size="1028" crc64="0xffe332400278f013">\databox\media\data-box-safety\electrical_shock_hazard_icon.png</file>
<file size="58699" crc64="0x2c411d5202c78a95">\databox\media\data-box-deploy-ordered\data-box-ordered.png</file>
<file size="46816" crc64="0x31e48aa9ca76bd05">\databox\media\data-box-deploy-ordered\search-azure-data-box1.png</file>
<file size="24160" crc64="0x978fc0c6e0c4c16d">\databox\media\data-box-deploy-ordered\select-data-box-option1.png</file>
<file size="115954" crc64="0x0b42449312086227">\databox\media\data-box-disk-deploy-copy-data\data-box-disk-validation-tool-output.png</file>
<file size="6093" crc64="0xadb61d0d7c6d4deb">\databox\data-box-cable-options.md</file>
<file size="6499" crc64="0x080add29add367d9">\databox\data-box-deploy-copy-data-via-nfs.md</file>
<file size="11089" crc64="0xc3ce6b13a4fe3001">\databox\data-box-deploy-copy-data-via-rest.md</file>
<file size="9126" crc64="0x820856b5a54321ad">\databox\data-box-overview.md</file>
<file size="10963" crc64="0x5e9a14f9f4784fd8">\databox\data-box-safety.md</file>
<file size="5941" crc64="0x8631d62fbc038760">\databox\data-box-security.md</file>
<file size="12536" crc64="0x8c8ff93e73d665ec">\databox\data-box-system-requirements-rest.md</file>
<file size="3220" crc64="0x7257a263c434839a">\databox\data-box-system-requirements.md</file>
<file size="2823" crc64="0x63db1ada6fcdc672">\databox\index.yml</file>
<file size="4364" crc64="0x62b5710f58f00b8b">\databox\data-box-local-web-ui-admin.md</file>
<file size="3603" crc64="0x7e34c25d5606693f">\databox\TOC.yml</file>
```

Esse arquivo contém a lista de todos os arquivos que foram copiados no Data Box ou Data Box Heavy. Nesse arquivo, o valor *crc64* se relaciona à soma de verificação gerada para o arquivo correspondente.

## <a name="view-available-capacity-of-the-device"></a>Exibir a capacidade disponível no dispositivo

Use o painel do dispositivo para exibir a capacidade disponível e é usada do dispositivo.

1. Na interface do usuário da Web local, acesse **Exibir o painel**.
2. Em **Conectar e copiar**, o espaço livre e usado no dispositivo é mostrado.

    ![Exibir a capacidade disponível](media/data-box-local-web-ui-admin/verify-used-space-dashboard.png)

## <a name="skip-checksum-validation"></a>Ignorar a validação de soma de verificação

As somas de verificação são geradas para os seus dados por padrão quando você se prepara para o envio. Em alguns casos raros, dependendo do tipo de dados (tamanhos de arquivo pequeno), o desempenho pode ser lento. Nesses casos, você pode ignorar a soma de verificação.

A computação de soma de verificação durante a preparação para envio é feita somente para ordens de importação e não para ordens de exportação.

É altamente recomendável que você não desabilite a soma de verificação, a menos que o desempenho seja seriamente afetado.

1. No canto superior direito da interface do usuário da Web local do seu dispositivo, vá para **configurações**.

    ![Desabilitar a soma de verificação](media/data-box-local-web-ui-admin/disable-checksum.png)

2. **Desabilitar** a validação de soma de verificação
3. Escolha **Aplicar**.

> [!NOTE]
> A opção de computação de soma de verificação de ignorar está disponível somente quando a Azure Data Box é desbloqueada. Você não verá essa opção quando o dispositivo estiver bloqueado.

## <a name="enable-smb-signing"></a>Habilitar assinatura SMB

A assinatura do protocolo SMB é um recurso pelo qual as comunicações que usam o SMB podem ser assinadas digitalmente no nível do pacote. Essa assinatura impede ataques que modificam pacotes SMB em trânsito.

Para obter mais informações relacionadas à assinatura SMB, consulte [visão geral da assinatura do bloco de mensagens do servidor](https://support.microsoft.com/help/887429/overview-of-server-message-block-signing).

Para habilitar a assinatura SMB em seu dispositivo do Azure:

1. No canto superior direito da interface do usuário da Web local do seu dispositivo, selecione **configurações**.

    ![Abra Configurações](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. **Habilitar** Assinatura SMB.

    ![Habilitar assinatura SMB](media/data-box-local-web-ui-admin/data-box-smb-signing-1.png)

3. Escolha **Aplicar**.
4. Na interface do usuário da Web local, acesse **Desligar ou reiniciar**.
5. Selecione **Reiniciar**.

## <a name="enable-backup-operator-privileges"></a>Habilitar privilégios de operador de backup

Os usuários da interface do usuário da Web têm privilégios de operador de backup em compartilhamentos SMB por padrão. Se você não quiser isso, use os **privilégios de habilitar operador back** para desabilitar ou habilitar os privilégios.

Para obter informações, consulte operadores de backup em [grupos de segurança Active Directory](/windows/security/identity-protection/access-control/active-directory-security-groups#backup-operators).

Para habilitar privilégios de operador de backup em seu dispositivo do Azure:

1. No canto superior direito da interface do usuário da Web local do seu dispositivo, selecione **configurações**.

   ![Abrir configurações de Data Box-1](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. **Habilitar** Privilégios de operador de backup.

   ![Habilitar privilégios de operador de backup](media/data-box-local-web-ui-admin/data-box-backup-operator-privileges-1.png)

3. **Selecione Aplicar**.
4. Na interface do usuário da Web local, acesse **Desligar ou reiniciar**.
5. Selecione **Reiniciar**.

## <a name="enable-acls-for-azure-files"></a>Habilitar ACLs para arquivos do Azure

Os metadados em arquivos são transferidos por padrão quando os usuários carregam dados via SMB para seu Data Box. Os metadados incluem listas de controle de acesso (ACLs), atributos de arquivo e carimbos de data/hora. Se você não quiser isso, use **ACLs para arquivos do Azure** para desabilitar ou habilitar esse recurso.

<!--For more information about metadata that is transferred, see [Preserving the ACLs and metadata with Azure Data Box](./data-box-local-web-ui-admin.md#enable-backup-operator-privileges) - IN DEVELOPMENT-->

> [!Note]
> Para transferir metadados com arquivos, você deve ser um operador de backup. Ao usar esse recurso, verifique se os usuários locais da interface do usuário da Web são operadores de backup. Consulte [habilitar privilégios de operador de backup](#enable-backup-operator-privileges).

Para habilitar a transferência de ACLs para arquivos do Azure:

1. No canto superior direito da interface do usuário da Web local do seu dispositivo, selecione **configurações**.

    ![Abrir configurações de Data Box-2](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. **Habilitar** ACLs para arquivos do Azure.

     ![Habilitar ACLs para arquivos do Azure](media/data-box-local-web-ui-admin/data-box-acls-for-azure-files-1.png)
  
3. Escolha **Aplicar**.
4. Na interface do usuário da Web local, acesse **Desligar ou reiniciar**.
5. Selecione **Reiniciar**.

## <a name="enable-tls-11"></a>Habilitar TLS 1,1

Por padrão, o Azure Data Box usa o protocolo TLS 1,2 para criptografia porque é mais seguro do que o TSL 1,1. No entanto, se você ou seus clientes usarem um navegador para acessar dados que não dão suporte a TLS 1,2, você poderá habilitar o TLS 1,1.

Para obter mais informações relacionadas ao TLS, consulte [segurança do gateway do Azure data Box](../databox-gateway/data-box-gateway-security.md).

Para habilitar o TLS 1,1 em seu dispositivo do Azure:

1. No canto superior direito da interface do usuário da Web local do seu dispositivo, selecione **configurações**.

    ![Abrir configurações de Data Box-3](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. **Habilitar** TLS 1,1.

    ![Habilitar TLS 1,1](media/data-box-local-web-ui-admin/data-box-tls-1-1.png)

3. Escolha **Aplicar**.
4. Na interface do usuário da Web local, acesse **Desligar ou reiniciar**.
5. Selecione **Reiniciar**.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [gerenciar o data box e data Box Heavy por meio do portal do Azure](data-box-portal-admin.md).

---
title: Tutorial para exportar dados do Azure Data Box | Microsoft Docs
description: Conheça os pré-requisitos de implantação e como exportar dados de um Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: how-to
ms.date: 12/18/2020
ms.author: alkohli
ms.openlocfilehash: 42476e2689cc503edc19e8e299a01ce922f1bf42
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98789188"
---
# <a name="tutorial-create-export-order-for-azure-data-box"></a>Tutorial: criar ordem de exportação para Azure Data Box

Azure Data Box é uma solução híbrida que permite mover dados para fora do Azure para seu local. Este tutorial descreve como criar uma ordem de exportação para Azure Data Box. O principal motivo para criar uma ordem de exportação é para a recuperação de desastres, caso o armazenamento local seja comprometido e um backup precise ser restaurado.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
>
> * Pré-requisitos para exportar
> * Ordenar um Data Box para exportação
> * Acompanhar a ordem de exportação
> * Cancelar a ordem de exportação

## <a name="prerequisites"></a>Pré-requisitos

Conclua os seguintes pré-requisitos de configuração para Data Box serviço e dispositivo antes de solicitar o dispositivo.

### <a name="for-service"></a>Para o serviço

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

* Verifique se você tem um grupo de recursos existente que pode ser usado com sua Azure Data Box.

* Verifique se sua conta de armazenamento do Azure da qual você deseja exportar dados é um dos tipos de conta de armazenamento com suporte, conforme descrito [as contas de armazenamento com suporte para data Box](data-box-system-requirements.md#supported-storage-accounts).

### <a name="for-device"></a>Para dispositivo

Antes de começar, verifique se:

* Você deve ter um computador host conectado à rede do datacenter. Você copiará os dados de Azure Data Box para este computador. Seu computador host deve executar um sistema operacional com suporte, conforme descrito em [Requisitos de sistema do Azure Data Box](data-box-system-requirements.md).

* Seu datacenter deve ter uma rede de alta velocidade. É altamente recomendável que você tenha pelo menos uma conexão de 10 GbE. Se uma conexão de 10 GbE não estiver disponível, um link de dados de 1 GbE poderá ser usado, mas as velocidades de cópia serão afetadas.

## <a name="order-data-box-for-export"></a>Data Box de pedidos para exportação

Execute as etapas a seguir no portal do Azure para solicitar um dispositivo.

1. Use suas credenciais do Microsoft Azure para entrar nesta URL: [https://portal.azure.com](https://portal.azure.com).

2. Selecione **+ Criar um recurso** e pesquise *Azure Data Box*. Selecione **Azure Data Box**.

   ![Criar um recurso](media/data-box-deploy-export-ordered/azure-data-box-export-order-create-resource.png)

3. Selecione **Criar**.

   ![Criar um recurso de Azure Data Box](media/data-box-deploy-export-ordered/azure-data-box-export-order-create-data-box-resource.png)

4. Verifique se Azure Data Box serviço está disponível em sua região. Insira ou selecione as informações a seguir e selecione **Aplicar**.

    |Configuração  |Valor  |
    |---------|---------|
    |Tipo de transferência     | Selecione **exportar para o Azure**.        |
    |Subscription     | Selecione uma assinatura do EA, do CSP ou do Azure Sponsorship para o serviço Data Box. <br> A assinatura está vinculada à sua conta de cobrança.       |
    |Resource group     |    Selecione um grupo de recursos existente. <br> Um grupo de recursos é um contêiner lógico para os recursos que podem ser gerenciados ou implantados juntos.         |
    |Região do Azure de origem    |    Selecione a região do Azure na qual seus dados estão atualmente.         |
    |País de destino     |     Selecione o país em que deseja enviar o dispositivo.        |

   ![Selecione suas configurações de Data Box](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-box-settings.png)

5. Selecione **Data Box**. A capacidade máxima utilizável de um único pedido é de 80 TB. É possível criar vários pedidos para tamanhos maiores de dados.

   ![Selecione a capacidade de Data Box](media/data-box-deploy-export-ordered/azure-data-box-export-order-capacity.png)

6. Em **ordem**, especifique os detalhes da ordem **básica** . Insira ou selecione as seguintes informações.

    |Configuração  |Valor  |
    |---------|---------|
    |Subscription     | A assinatura é preenchida automaticamente com base na seleção anterior.|
    |Resource group | O grupo de recursos selecionado anteriormente. |
    |Nome da ordem de exportação     |  Forneça um nome amigável para acompanhar o pedido. <br> O nome pode ter entre 3 e 24 caracteres que podem ser letras, números e hífens. <br> O nome deve começar e terminar com uma letra ou um número.      |

    ![Noções básicas de ordem de exportação](media/data-box-deploy-export-ordered/azure-data-box-export-order-basics-order-name.png)

    Selecione **Avançar: seleção de dados** para continuar.

7. Em **seleção de dados**, selecione **adicionar conta de armazenamento e tipo de exportação**.

    ![Adicionar conta de armazenamento e tipo de exportação](media/data-box-deploy-export-ordered/azure-data-box-export-order-basics-add-storage.png)

8. Na **opção Selecionar exportação**, especifique os detalhes da opção de exportação. Insira ou selecione as informações a seguir e selecione **Adicionar**.

    |Configuração  |Valor  |
    |---------|---------|
    |Conta de armazenamento     | A conta de armazenamento do Azure de onde você deseja exportar dados. |
    |Tipo de exportação     | Especifica o tipo de dados a serem exportados de **todos os objetos** e **usam o arquivo XML**.<ul><li> **Todos os objetos** – especifica que o trabalho exporta todos os dados dependendo da sua seleção para **Opções de transferência**.</li><li> **Usar arquivo XML** – especifica um arquivo XML que contém um conjunto de caminhos e prefixos para BLOBs e/ou arquivos a serem exportados da conta de armazenamento. O arquivo XML precisa estar no contêiner da conta de armazenamento selecionada e não há suporte para a seleção de compartilhamentos de arquivos. O arquivo precisa ser um arquivo. xml não vazio.</li></ul>        |
    |Opções de transferência     |  Especifica as opções de transferência de dados de **selecionar tudo**, **todos os BLOBs** e **todos os arquivos**. <ul><li> **Selecionar tudo** -especifica que todos os BLOBs e arquivos do Azure são exportados. Se você estiver usando uma conta de armazenamento que dá suporte apenas a BLOBs (conta de armazenamento de BLOB), a opção **todos os arquivos** não será selecionável.</li><li> **Todos os BLOBs** -especifica que somente os blobs de bloco e de página são exportados.</li><li> **Todos os arquivos** -especifica que todos os arquivos são exportados, excluindo BLOBs. O tipo de conta de armazenamento que você tem (GPv1 e GPv2, armazenamento Premium ou armazenamento de BLOBs) determina os tipos de dados que você pode exportar. Para obter mais informações, consulte [supported Storage accounts for Export](../import-export/storage-import-export-requirements.md#supported-storage-types).</li></ul>         |
    |Incluir log detalhado     | Indica se você deseja um arquivo de log detalhado que contenha uma lista de todos os arquivos que foram exportados com êxito.        |

    > [!NOTE]
    >
    > Se você selecionar **usar arquivo XML** para a configuração de **tipo de exportação** , será necessário certificar-se de que o XML contém caminhos e/ou prefixos válidos. Você deve construir e fornecer o arquivo XML.  Se o arquivo for inválido ou nenhum dado corresponder aos caminhos especificados, a ordem será encerrada com dados parciais ou nenhum dado será exportado.

    Para ver como adicionar um arquivo XML a um contêiner, consulte [Exportar ordem usando o arquivo XML](data-box-deploy-export-ordered.md#export-order-using-xml-file).

   ![Selecionar opção de exportação](media/data-box-deploy-export-ordered/azure-data-box-export-order-export-option.png)

   Para ver um exemplo da entrada XML, consulte [exemplo de entrada XML](data-box-deploy-export-ordered.md#sample-xml-file)

9. Em **seleção de dados**, examine as configurações e selecione **avançar: segurança>** para continuar.

   ![Ordem de exportação, seleção de dados](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-selection.png)

    A tela de **segurança** permite que você use sua própria chave de criptografia e opte por usar a criptografia dupla.

    Todas as configurações na tela **Segurança** são opcionais. Se você não alterar nenhuma configuração, as configurações padrão serão aplicadas.

    ![Tela de segurança do assistente para Pedidos de importação do Data Box](media/data-box-deploy-export-ordered/data-box-export-security-01.png)

10. Se você quiser usar a própria chave gerenciada pelo cliente para proteger a chave de acesso de desbloqueio para o novo recurso, expanda **Tipo de criptografia**.

    A configuração de uma chave gerenciada pelo cliente para seu Azure Data Box é opcional. Por padrão, o Data Box usa uma chave gerenciada da Microsoft para proteger a chave de acesso de desbloqueio.

    Uma chave gerenciada pelo cliente não afeta como os dados no dispositivo são criptografados. A chave é usada somente para criptografar a chave de acesso de desbloqueio do dispositivo.

    Se você não quiser usar uma chave gerenciada pelo cliente, pule para a etapa 16.

    ![Tela de segurança mostrando as configurações Tipo de criptografia](./media/data-box-deploy-export-ordered/customer-managed-key-01.png)

11. Selecione **Chave gerenciada pelo cliente** como o tipo de chave. Em seguida, selecione **Selecionar um cofre de chaves e uma chave**.
   
    ![Tela de segurança, configurações para uma chave gerenciada pelo cliente](./media/data-box-deploy-export-ordered/customer-managed-key-02.png)

12. Na tela **selecionar chave de Azure Key Vault** , a assinatura é preenchida automaticamente.

    - Para **Cofre de chaves**, você pode selecionar um cofre de chaves existente na lista suspensa.

      ![Tela Selecionar chave do Azure Key Vault](./media/data-box-deploy-export-ordered/customer-managed-key-03.png)

    - Você também pode selecionar **Criar** para criar um cofre de chaves. Na tela **Criar cofre de chaves**, insira o grupo de recursos e um nome do cofre de chaves. Verifique se as opções **Exclusão temporária** e **Proteção contra limpeza** estão habilitadas. Aceite todos os outros padrões e selecione **Examinar + Criar**.

      ![Configurações para Criar um Azure Key Vault](./media/data-box-deploy-export-ordered/customer-managed-key-04.png)

      Examine as informações do seu cofre de chaves e selecione **Criar**. Aguarde alguns minutos até que a criação do cofre de chaves seja concluída.

      ![Tela de revisão Novo Azure Key Vault](./media/data-box-deploy-export-ordered/customer-managed-key-05.png)

13. Na tela **selecionar chave de Azure Key Vault** , você pode selecionar uma chave existente no cofre de chaves.

    ![Selecionar chave existente do Azure Key Vault](./media/data-box-deploy-export-ordered/customer-managed-key-06.png)

    Se você desejar criar uma chave, selecione **Criar**. Você precisa usar uma chave RSA. O tamanho pode ser 2048 ou maior. Insira um nome para a nova chave, aceite os outros padrões e selecione **Criar**.

      ![Opção Criar uma chave](./media/data-box-deploy-export-ordered/customer-managed-key-07.png)

      Você será notificado quando a chave tiver sido criada no cofre de chaves.

14. Selecione a **Versão** da chave a ser usada e, em seguida, escolha **Selecionar**.

      ![Chave criada no cofre de chaves](./media/data-box-deploy-export-ordered/customer-managed-key-08.png)

    Se você desejar criar uma versão da chave, selecione **Criar**.

    ![Abrir uma caixa de diálogo para criar uma versão de chave](./media/data-box-deploy-export-ordered/customer-managed-key-08-a.png)

    Na tela **criar nova chave** , escolha configurações para a nova versão de chave e selecione **criar**.

    ![Criar uma nova versão da chave](./media/data-box-deploy-export-ordered/customer-managed-key-08-b.png)

    As configurações **Tipo de criptografia** na tela **Segurança** mostram seu cofre de chaves e sua chave.

    ![Chave e cofre de chaves para uma chave gerenciada pelo cliente](./media/data-box-deploy-export-ordered/customer-managed-key-09.png)

15. Selecione uma identidade de usuário que você usará para gerenciar o acesso a esse recurso. Escolha **Selecionar uma identidade de usuário**. No painel à direita, selecione a assinatura e a identidade gerenciada a ser usada. Em seguida, escolha **Selecionar**.

    Uma identidade gerenciada atribuída pelo usuário é um recurso do Azure autônomo que pode ser usado para gerenciar vários recursos. Para obter mais informações, confira [Tipos de identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md).  

    Se você precisar criar uma identidade gerenciada, siga as diretrizes em [Criar, listar, excluir ou atribuir uma função a uma identidade gerenciada atribuída pelo usuário usando o portal do Azure](../../articles/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).
    
    ![Selecionar uma identidade de usuário](./media/data-box-deploy-export-ordered/customer-managed-key-10.png)

    A identidade de usuário é mostrada nas configurações **Tipo de criptografia**.

    Você pode recolher as configurações de **tipo de criptografia** agora.

    ![Uma identidade de usuário selecionada mostrada nas configurações Tipo de criptografia](./media/data-box-deploy-export-ordered/customer-managed-key-11.png)

16. Se você quiser habilitar a criptografia dupla baseada em software, expanda **criptografia dupla (para ambientes de alta segurança)** e selecione **habilitar criptografia dupla para o pedido**. 

    Uma criptografia baseada em software será executada além da criptografia AES de 256 bits dos dados no Data Box.

    > [!NOTE]
    > Habilitar essa opção poderá fazer com que o processamento de pedidos e a cópia de dados demorem mais. Não será possível alterar essa opção depois de criar seu pedido.

    ![Tela de segurança para importação do Data Box e de uma criptografia dupla](media/data-box-deploy-export-ordered/azure-data-box-export-order-security-double-encryption.png)

    Selecione **Avançar: Detalhes de contato** para continuar.

11. Nos **detalhes de contato**, selecione **+ Adicionar endereço de envio** para inserir suas informações de envio.

    ![Adicionar endereço de envio](media/data-box-deploy-export-ordered/azure-data-box-export-order-add-shipping-address.png)

12. Em **Adicionar endereço de envio**, forneça seu nome e sobrenome, nome e endereço postal da empresa e um número de telefone válido. Selecione **Validar**. O serviço valida o endereço de remessa para a disponibilidade do serviço. Se o serviço está disponível para o endereço de remessa especificado, você receberá uma notificação para esse efeito.

    ![Validar endereço de envio](media/data-box-deploy-export-ordered/azure-data-box-export-order-validate-shipping-address.png)

    Se você estiver ordenando uma região em que o envio autogerenciado está disponível, você poderá selecionar essa opção. Para saber mais sobre a remessa autogerenciada, confira [Usar a remessa autogerenciada](data-box-portal-customer-managed-shipping.md).

13. Selecione **Adicionar endereço de envio** depois que os detalhes de envio tiverem sido validados com êxito.

14. Em **detalhes de contato**, revise seu endereço de envio e endereço de email. O serviço envia as notificações por email em relação a quaisquer atualizações do status do pedido para os endereços de email especificados.

    É recomendável usar um email de grupo para que você continue a receber notificações caso um administrador do grupo saia.

    ![Detalhes do contato](media/data-box-deploy-export-ordered/azure-data-box-export-order-contact-details.png)

15. Selecione **Avançar: revisar + ordem>**. Você deve aceitar os termos e condições para prosseguir com a criação do pedido.

16. Selecione **Pedido**. A criação do pedido demora alguns minutos.

    ![Ordem de confirmação](media/data-box-deploy-export-ordered/azure-data-box-select-export-order-commit-order.png)

## <a name="export-order-using-xml-file"></a>Exportar ordem usando arquivo XML

Se você selecionar **usar arquivo XML**, poderá especificar contêineres e blobs específicos (página e bloco) que deseja exportar. Você precisará seguir as especificações de [tabela de arquivo XML de exemplo](#sample-xml-file) para formatar seu XML. As etapas a seguir mostram como usar um arquivo XML para exportar seus dados:

1. Para **tipo de exportação**, selecione **usar arquivo XML**. Esse é o arquivo XML que especifica BLOBs específicos e arquivos do Azure que você deseja exportar. Para adicionar o arquivo XML, selecione **clique aqui para selecionar um arquivo XML**.

     ![Selecionar opção de exportação, XML](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-select-xml-option.png)

2. Selecione **+ contêiner** para criar um contêiner.

    ![Selecione a opção de exportação, contêineres](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-containers-option.png)

3. Na **nova guia contêiner** que aparece no lado direito do portal do Azure, adicione um nome para o contêiner. O nome deve estar em letras minúsculas e você pode incluir números e traços '-'. Em seguida, selecione o **nível de acesso público** na caixa de listagem suspensa. Recomendamos que você escolha **privado (acesso não anônimo)** para impedir que outras pessoas acessem seus dados. Para obter mais informações sobre níveis de acesso de contêiner, consulte [permissões de acesso de contêiner](../storage/blobs/anonymous-read-access-configure.md#set-the-public-access-level-for-a-container).

   ![Selecione a opção Exportar, novas configurações de contêiner](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-container-settings.png)

4. Selecione **Criar**.

   ![Selecione a opção Exportar, criar novo contêiner.](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-create-container.png)

   Se o contêiner for criado com êxito, você receberá a seguinte mensagem:

   ![Contêiner criado com êxito](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-container-success.png)

5. Selecione o contêiner que você criou e clique duas vezes nele.

   ![Exibir detalhes do contêiner](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-view-container-details.png)

6. Clicar duas vezes no contêiner abrirá a exibição Propriedades do contêiner. Agora você deseja anexar (ou procurar) seu arquivo XML que contém a lista de BLOBs e/ou arquivos do Azure que você deseja exportar. Escolha **Carregar**.

   ![Carregar blob no contêiner](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-blob-to-container.png)

7. Você adicionou com êxito o arquivo XML ao contêiner. Somente os BLOBs e os arquivos do Azure especificados neste XML serão exportados.

   ![Arquivo XML adicionado ao contêiner](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-added-to-container.png)

## <a name="track-the-order"></a>Acompanhar o pedido

Depois de fazer o pedido, você pode acompanhar o status do pedido no portal do Azure. Acesse seu pedido do Data Box e, em seguida, acesse **Visão Geral** para exibir o status. O portal mostra o pedido sob o estado **Pedido**.

Quando a preparação do dispositivo for concluída, a cópia de dados será iniciada a partir das contas de armazenamento selecionadas. O portal mostra a ordem em estado de **cópia de dados em andamento** .

![Data Box ordem de exportação, cópia de dados em andamento](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-copy-in-progress.png)

Data Box copia dados das contas de armazenamento de origem. Depois que a cópia de dados for concluída, Data Box será bloqueada e o portal mostrará a ordem no estado de **cópia concluída** .

![Data Box ordem de exportação, cópia de dados concluída](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-copy-complete.png)

Se o dispositivo não estiver disponível, você receberá uma notificação. Se o dispositivo estiver disponível, a Microsoft identificará o dispositivo para envio e o preparará. Durante a preparação do dispositivo, ocorrem as seguintes ações:

* Os compartilhamentos SMB são criados para cada conta de armazenamento associada ao dispositivo.
* Para cada compartilhamento, as credenciais de acesso, como nome de usuário e senha, são geradas.
* O dispositivo está bloqueado e pode ser acessado somente usando a senha de desbloqueio do dispositivo. Para recuperar a senha, você precisa fazer logon na sua conta do portal do Azure e selecionar os **detalhes do dispositivo**.

Em seguida, a Microsoft prepara e despacha seu dispositivo por meio de uma operadora regional. Após o envio do dispositivo, você receberá um número de acompanhamento. O portal mostra o pedido no estado **Despachado**.

![Data Box ordem de exportação expedida](media/data-box-deploy-export-ordered/azure-data-box-export-order-dispatched.png)

Se a entrega autogerenciada foi selecionada, você receberá uma notificação por email com as próximas etapas quando o dispositivo estiver pronto para ser obtido do datacenter. Para obter mais informações relacionadas ao envio autogerenciado, consulte [entrega autogerenciada](data-box-portal-customer-managed-shipping.md).

![Envio autogerenciado pronto para retirada](media/data-box-deploy-export-ordered/azure-data-box-export-order-ready-for-pickup.png)

## <a name="cancel-the-order"></a>Cancelar o pedido

Para cancelar esse pedido, no portal do Azure, vá para **Visão geral** e selecione **Cancelar** na barra de comandos.

Depois de fazer um pedido, você pode cancelá-lo a qualquer momento antes de o pedido começar a ser processado.

Para excluir um pedido cancelado, vá para **Visão geral** e selecione **Excluir** na barra de comandos.

## <a name="sample-xml-file"></a>Arquivo XML de exemplo

O XML a seguir mostra um exemplo de nomes de BLOB, prefixos de BLOB e arquivos do Azure contidos no formato XML que a ordem de exportação usa quando você seleciona a opção **usar arquivo XML** :

```xml
<?xml version="1.0" encoding="utf-8"?>
   <!-- BlobList/prefix/Container list for Blob storage for export  -->
   <BlobList>
      <BlobPath>/8tbpageblob/8tbpageblob/8tbpageblob</BlobPath>
      <BlobPathPrefix>/blockblob4dot75tbdata/</BlobPathPrefix>
      <BlobPathPrefix>/1tbfilepageblob</BlobPathPrefix>
      <BlobPathPrefix>/1tbfile/</BlobPathPrefix>
      <BlobPathPrefix>/8mbfiles/</BlobPathPrefix>
      <BlobPathPrefix>/64mbfiles/</BlobPathPrefix>
   </BlobList>
   <!-- FileList/prefix/Share list for Azure File storage for export  -->
   <AzureFileList>
      <FilePathPrefix>/64mbfiles/</FilePathPrefix>
      <FilePathPrefix>/4mbfiles/prefix2/subprefix</FilePathPrefix>
      <FilePathPrefix>/1tbfile/prefix</FilePathPrefix>
   </AzureFileList>
```

Alguns pontos importantes em relação aos arquivos XML:

* As marcas XML diferenciam maiúsculas de minúsculas e precisam corresponder exatamente conforme especificado no exemplo acima.
* Marcas de abertura e fechamento devem corresponder.
* Marcas XML incorretas ou formatação podem levar à falha de exportação de dados.
* Nenhum dado será exportado se o blob e/ou o prefixo do arquivo forem inválidos.

### <a name="examples-of-valid-blob-paths"></a>Exemplos de caminhos de blob válidos

A tabela a seguir mostra exemplos de caminhos de blob válidos:

   | Seletor | Caminho do Blob | Descrição |
   | --- | --- | --- |
   | Começa com |/ |Exporta todos os blobs na conta de armazenamento |
   | Começa com |/$root/ |Exporta todos os blobs no contêiner raiz |
   | Começa com |/containers |Exporta todos os BLOBs em qualquer contêiner que comece com **contêineres** de prefixo |
   | Começa com |/container-name/ |Exporta todos os BLOBs no contêiner **de contêiner-nome** |
   | Começa com |/container-name/prefix |Exporta todos os BLOBs no contêiner **de contêiner-nome** que começa com **prefixo** de prefixo |
   | Igual a |$root/logo.bmp |Exporta o blob **logo.bmp** no contêiner raiz |
   | Igual a |8tbpageblob/mydata.txt |Exporta **mydata.txt** de blob no contêiner **8tbpageblob** |

## <a name="sample-log-files"></a>Arquivos de log de exemplo

Esta seção fornece arquivos de log de exemplo que são gerados durante a exportação. Os logs de erros são gerados automaticamente. Para gerar o arquivo de log detalhado, você precisa selecionar **incluir log detalhado** em portal do Azure ao configurar a ordem de exportação.
Para obter mais informações sobre logs de cópia e detalhado, consulte [copiar logs](data-box-deploy-export-copy-data.md#copy-data-from-data-box).

<!-- ### Verbose log

The following log files show examples of verbose logging when you select **Include verbose log**:

```xml
<File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3.4" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/helloEndWithDot..txt" Size="11" crc64="7320094093915972193"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test..txt" Size="12" crc64="17906086011702236012"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test1" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/.......txt" Size="11" crc64="7320094093915972193"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/copylogb08fa3095564421bb550d775fff143ed====..txt" Size="53638" crc64="1147139997367113454"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/testmaxChars-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-12345679" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file0" Size="0" crc64="0"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file1" Size="0" crc64="0"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000001" Size="4096" crc64="16969371397892565512"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000000" Size="4096" crc64="16969371397892565512"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed10.dat" Size="65536" crc64="10746682179555216785"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/LiveSiteReport_Oct.xlsx" Size="7028" crc64="6103506546789189963"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/NE_Oct_GeoReport.xlsx" Size="103197" crc64="13305485882546035852"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed1.dat" Size="65536" crc64="3140622834011462581"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-0" Size="1048576" crc64="16086591317856295272"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000001" Size="524288" crc64="8908547729214703832"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-0" Size="4194304" crc64="1339017920798612765"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000000" Size="524288" crc64="8908547729214703832"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-1" Size="8388608" crc64="3963298606737216548"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-1" Size="1048576" crc64="11061759121415905887"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/XLS-10MB.xls" Size="1199104" crc64="2218419493992437463"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-0" Size="8388608" crc64="1072783424245035917"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-1" Size="4194304" crc64="9991307204216370812"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/VL_Piracy_Negtive10_TPNameAndGCS.xlsx" Size="12398699" crc64="13526033021067702820"></File>
```

### Copy logs

For more information regarding copy logs, see [Copy logs](data-box-deploy-export-copy-data.md#copy-data-from-data-box). -->

<!-- The following xml shows an example of the copy log when the export is successful:

```xml
<CopyLog Summary="Summary">
  <Status>Succeeded</Status>
    <TotalFiles_Blobs>27</TotalFiles_Blobs>
    <FilesErrored>0</FilesErrored>
</CopyLog>
```

For more information regarding copy logs, see [Copy logs](data-box-deploy-export-copy-data.md#copy-data-from-data-box).

The following xml shows an example of the copy log when the export has errors:

```xml
<ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/wastorage.v140.3.0.2.nupkg">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444.hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444 (1).hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>4</TotalFiles_Blobs>
  <FilesErrored>3</FilesErrored>
</CopyLog>
``` -->

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos do Azure Data Box como:

> [!div class="checklist"]
>
> * Pré-requisitos para exportar
> * Ordenar um Data Box para exportação
> * Acompanhar a ordem de exportação
> * Cancelar a ordem de exportação

Avance para o próximo tutorial para saber como proteger seu Data Box.

> [!div class="nextstepaction"]
> [Configurar seu Azure Data Box](./data-box-deploy-set-up.md)
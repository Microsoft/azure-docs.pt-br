---
title: Use seus próprios certificados com dispositivos Azure Data Box/Azure Data Box Heavy
description: Como usar seus próprios certificados para acessar a interface do usuário da Web local e o armazenamento de blog em seu Data Box ou Data Box Heavy dispositivo.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 12/11/2020
ms.author: alkohli
ms.openlocfilehash: deb52c8716f97874beae4accbf6f34f72e20ca04
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516463"
---
# <a name="use-your-own-certificates-with-data-box-and-data-box-heavy-devices"></a>Use seus próprios certificados com dispositivos Data Box e Data Box Heavy

Durante o processamento do pedido, os certificados autoassinados são gerados para acessar a interface do usuário da Web local e o armazenamento de BLOBs para um dispositivo Data Box ou Data Box Heavy. Se você preferir se comunicar com seu dispositivo por meio de um canal confiável, poderá usar seus próprios certificados.

O artigo descreve como instalar seus próprios certificados e como reverter para os certificados padrão antes de retornar o dispositivo para o datacenter. Ele também fornece um resumo dos requisitos de certificado.

## <a name="about-certificates"></a>Sobre certificados

Um certificado fornece um link entre uma **chave pública** e uma entidade (como o nome de domínio) que foi **assinada** (verificada) por um terceiro confiável, como uma **autoridade de certificação**.  Um certificado fornece uma maneira conveniente de distribuir chaves de criptografia pública confiáveis. Dessa forma, os certificados garantem que a comunicação seja confiável e que você esteja enviando informações criptografadas para o servidor correto.

Quando o dispositivo de Data Box é configurado inicialmente, os certificados autoassinados são gerados automaticamente. Opcionalmente, você pode colocar seus próprios certificados. Há diretrizes que você precisa seguir se planeja trazer seus próprios certificados.

Em um dispositivo Data Box ou Data Box Heavy, dois tipos de certificados de ponto de extremidade são usados:

- Certificado de armazenamento de BLOBs
- Certificado de interface do usuário local

### <a name="certificate-requirements"></a>Requisitos de certificado

Os certificados devem atender aos seguintes requisitos:

- O certificado do ponto de extremidade precisa estar no `.pfx` formato com uma chave privada que possa ser exportada.
- Você pode usar um certificado individual para cada ponto de extremidade, um certificado de multidomínio para vários pontos de extremidade ou um certificado de ponto curinga.
- As propriedades de um certificado de ponto de extremidade são semelhantes às propriedades de um certificado SSL típico.
- Um certificado correspondente no formato DER ( `.cer` extensão de nome de arquivo) é necessário no computador cliente.
- Depois de carregar o certificado de interface do usuário local, você precisará reiniciar o navegador e limpar o cache. Consulte as instruções específicas para seu navegador.
- Os certificados devem ser alterados se o nome do dispositivo ou o nome de domínio DNS for alterado.
- Use a tabela a seguir ao criar certificados de ponto de extremidade:

  |Tipo |Nome da entidade (SN)  |SAN (nome alternativo da entidade)  |Exemplo de nome da entidade |
  |---------|---------|---------|---------|
  |Interface do usuário local| `<DeviceName>.<DNSdomain>`|`<DeviceName>.<DNSdomain>`| `mydevice1.microsoftdatabox.com` |
  |Armazenamento de blob|`*.blob.<DeviceName>.<DNSdomain>`|`*.blob.< DeviceName>.<DNSdomain>`|`*.blob.mydevice1.microsoftdatabox.com` |
  |Certificado único de várias SANs|`<DeviceName>.<DNSdomain>`|`<DeviceName>.<DNSdomain>`<br>`*.blob.<DeviceName>.<DNSdomain>`|`mydevice1.microsoftdatabox.com` |

Para obter mais informações, consulte [requisitos de certificado](../../articles/databox-online/azure-stack-edge-j-series-certificate-requirements.md).

## <a name="add-certificates-to-device"></a>Adicionar certificados ao dispositivo

Você pode usar seus próprios certificados para acessar a interface do usuário da Web local e para acessar o armazenamento de BLOBs.

> [!IMPORTANT]
> Se o nome do dispositivo ou domínio DNS for alterado, novos certificados deverão ser criados. Os certificados de cliente e os certificados de dispositivo devem ser atualizados com o novo nome de dispositivo e domínio DNS.

Para adicionar seu próprio certificado ao seu dispositivo, siga estas etapas:

1. Vá para **gerenciar**  >  **certificados**.

   **Nome** mostra o nome do dispositivo. **Domínio DNS** mostra o nome de domínio para o servidor DNS.

   A parte inferior da tela mostra os certificados em uso no momento. Para um novo dispositivo, você verá os certificados autoassinados que foram gerados durante o processamento do pedido.

   ![Página de certificados para um dispositivo Data Box](media/data-box-bring-your-own-certificates/certificates-manage-certs.png)

2. Se você precisar alterar o **nome** (nome do dispositivo) ou o **domínio DNS** (o domínio do servidor DNS para o dispositivo), faça isso agora, antes de adicionar o certificado. Em seguida, selecione **Aplicar**.

   O certificado deve ser alterado se o nome do dispositivo ou o nome de domínio DNS for alterado.

   ![Aplicar um novo nome de dispositivo e domínio DNS para um Data Box](media/data-box-bring-your-own-certificates/certificates-device-name-dns.png)

3. Para adicionar um certificado, selecione **Adicionar certificado** para abrir o painel **Adicionar certificado** . Em seguida, selecione o **tipo de certificado** - **armazenamento de BLOBs** ou **interface do usuário da Web local**.

   ![Painel Adicionar certificados na página certificados para um dispositivo Data Box](media/data-box-bring-your-own-certificates/certificates-add-certificate-cert-type.png)

4. Escolha o arquivo de certificado (em `.pfx` formato) e insira essa senha que foi definida quando o certificado foi exportado. Em seguida, selecione **validar & adicionar**.

   ![Configurações para adicionar um certificado de ponto de extremidade de blob a um Data Box](media/data-box-bring-your-own-certificates/certificates-add-blob-cert.png)

   Depois que o certificado for adicionado com êxito, a tela **certificados** mostrará a impressão digital para o novo certificado. O status do certificado é **válido**.

   ![Um novo certificado válido que foi adicionado com êxito](media/data-box-bring-your-own-certificates/certificates-view-new-certificate.png)

5. Para ver os detalhes do certificado, selecione e clique no nome do certificado. O certificado expirará após um ano.

   ![Exibir detalhes do certificado para um dispositivo Data Box](media/data-box-bring-your-own-certificates/certificates-cert-details.png)

   <!--If you changed the local web UI certificate, you'll see the following error. This error will go away when you install the new certificate on the client computer.

   ![Error after a new Local web UI certificate is added to a Data Box device](media/data-box-bring-your-own-certificates/certificates-unable-to-communicate-error.png) TEST. RESTORE IF ERROR IS REPRODUCED.-->

6. Se você alterou o certificado para a interface do usuário da Web local, precisará reiniciar o navegador e, em seguida, a interface do usuário da Web local. Essa etapa é necessária para evitar quaisquer problemas de cache SSL.

  <!-- TESTING THIS - The communication error should be gone from the **Certificates** screen.-->

7. Instale o novo certificado no computador cliente que você está usando para acessar a interface do usuário da Web local. Para obter instruções, consulte [importar certificados para o cliente](#import-certificates-to-client), abaixo.


## <a name="import-certificates-to-client"></a>Importar certificados para o cliente

Depois de adicionar um certificado ao seu dispositivo Data Box, você precisará importar o certificado para o computador cliente que você usa para acessar o dispositivo. Você importará o certificado para o repositório de autoridade de certificação raiz confiável para o computador local.

Para importar um certificado em um cliente do Windows, siga estas etapas:

1. No explorador de arquivos, clique com o botão direito do mouse no arquivo de certificado (com `.cer` formato) e selecione **Instalar certificado**. Essa ação inicia o Assistente para Importação de Certificados.

    ![Importar certificado 1](media/data-box-bring-your-own-certificates/import-cert-01.png)

2. Em **local do repositório**, selecione **computador local** e, em seguida, selecione **Avançar**.

    ![Selecione computador local como o local de armazenamento no assistente de importação de certificados](media/data-box-bring-your-own-certificates/import-cert-02.png)

3. Selecione **Coloque todos os certificados no repositório a seguir**, selecione **autoridade de certificação raiz confiável** e, em seguida, selecione **Avançar**.

   ![Selecionar o repositório de autoridades de certificação raiz confiáveis no Assistente para importação de certificados](media/data-box-bring-your-own-certificates/import-cert-03.png)

4. Examine suas configurações e selecione **concluir**. Uma mensagem informará que a importação foi bem-sucedida.

   ![Examine as configurações de certificado e conclua o assistente para importação de certificados](media/data-box-bring-your-own-certificates/import-cert-04.png)

## <a name="revert-to-default-certificates"></a>Reverter para certificados padrão

Antes de retornar o dispositivo para o datacenter do Azure, você deve reverter para os certificados originais que foram gerados durante o processamento do pedido.

Para reverter para os certificados gerados durante o processamento do pedido, siga estas etapas:

1. Vá para **gerenciar**  >  **certificados** e selecione **reverter certificados**.

   A reversão de certificados retorna ao uso de certificados autoassinados que foram gerados durante o processamento do pedido. Seus próprios certificados são removidos do dispositivo.

   ![Opção REVERT Certificates em gerenciar certificados para um dispositivo Data Box](media/data-box-bring-your-own-certificates/certificates-revert-certificates.png)

2. Depois que a reversão do certificado for concluída com êxito, vá para **desligar ou reiniciar** e selecione **reiniciar**. Essa etapa é necessária para evitar quaisquer problemas de cache SSL.

   ![Desligar ou reiniciar a interface do usuário da Web local após reverter certificados em um dispositivo Data Box](media/data-box-bring-your-own-certificates/certificates-restart-ui.png)

   Aguarde alguns minutos e, em seguida, entre novamente na interface do usuário da Web local.

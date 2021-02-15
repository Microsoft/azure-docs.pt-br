---
title: Segurança de R Azure Stack Edge pro | Microsoft Docs
description: Descreve os recursos de segurança e privacidade que protegem os dispositivos, o serviço e os dados do Azure Stack Edge pro R e do Azure Stack Edge no local e na nuvem.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: 891885dce52ee57fa163bb71b427591156091651
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100362839"
---
# <a name="security-and-data-protection-for-azure-stack-edge-pro-r-and-azure-stack-edge-mini-r"></a>Segurança e proteção de dados para o Azure Stack Edge pro R e Azure Stack o mini R Edge

[!INCLUDE [applies-to-pro-r-and-mini-r--skus](../../includes/azure-stack-edge-applies-to-pro-r-mini-r-sku.md)]

A segurança é uma preocupação importante quando você está adotando uma nova tecnologia, especialmente se a tecnologia for usada com dados confidenciais ou proprietários. Azure Stack o Edge pro R e o Azure Stack Edge mini R ajudam a garantir que apenas entidades autorizadas possam exibir, modificar ou excluir seus dados.

Este artigo descreve os recursos de segurança do Azure Stack Edge pro R e do Azure Stack Edge que ajudam a proteger cada um dos componentes da solução e os dados armazenados neles.

A solução consiste em quatro componentes principais que interagem entre si:

- **Azure Stack serviço do Edge, hospedado no Azure público ou na nuvem do Azure governamental**. O recurso de gerenciamento que você usa para criar a ordem do dispositivo, configurar o dispositivo e acompanhar o pedido até a conclusão.
- **Azure Stack dispositivo resistente à borda**. O dispositivo físico resistente que é enviado para você para que você possa importar seus dados locais para a nuvem do Azure Public ou Azure governamental. O dispositivo pode ser Azure Stack o Edge pro R ou o Edge Azure Stack a mini R.
- **Clientes/hosts conectados ao dispositivo**. Os clientes em sua infraestrutura que se conectam ao dispositivo e contêm dados que precisam ser protegidos.
- **Armazenamento em nuvem**. O local na plataforma de nuvem do Azure onde os dados são armazenados. Normalmente, esse local é a conta de armazenamento vinculada ao recurso de borda Azure Stack que você cria.

## <a name="service-protection"></a>Proteção de serviço

O serviço de borda do Azure Stack é um serviço de gerenciamento hospedado no Azure. O serviço é usado para configurar e gerenciar o dispositivo.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-service-protection.md)]

## <a name="device-protection"></a>Proteção do dispositivo

O dispositivo resistente é um dispositivo local que ajuda a transformar seus dados processando-os localmente e, em seguida, enviando-os para o Azure. Seu dispositivo:

- Precisa de uma chave de ativação para acessar o serviço do Azure Stack Edge.
- O é protegido sempre por uma senha de dispositivo.
- É um dispositivo bloqueado. O BMC (controlador de gerenciamento de placa-base) do dispositivo e o BIOS são protegidos por senha. O BMC é protegido por acesso limitado ao usuário.
- Tem inicialização segura habilitada que garante que o dispositivo seja inicializado somente usando o software confiável fornecido pela Microsoft.
- Executa o controle de aplicativos do Windows Defender (WDAC). O WDAC permite que você execute apenas aplicativos confiáveis que você define em suas políticas de integridade de código.
- Tem um Trusted Platform Module (TPM) que executa funções relacionadas à segurança baseadas em hardware. Especificamente, o TPM gerencia e protege os segredos e os dados que precisam ser persistidos no dispositivo.
- Somente as portas necessárias são abertas no dispositivo e todas as outras portas são bloqueadas. Para obter mais informações, consulte a lista de [requisitos de porta para o dispositivo](azure-stack-edge-pro-r-system-requirements.md) .
- Todo o acesso ao hardware do dispositivo, bem como ao software, é registrado. 
    - Para o software do dispositivo, os logs de firewall padrão são coletados para o tráfego de entrada e saída do dispositivo. Esses logs são agrupados no pacote de suporte.
    - Para o hardware do dispositivo, todos os eventos do chassi do dispositivo, como abertura e fechamento do chassi do dispositivo, são registrados no dispositivo.

    Para obter mais informações sobre os logs específicos que contêm os eventos de intrusão de hardware e software e como obter os logs, vá para [coletar logs de segurança avançados](azure-stack-edge-gpu-troubleshoot.md).


### <a name="protect-the-device-via-activation-key"></a>Proteger o dispositivo por meio da chave de ativação

Somente um dispositivo de mini-R autorizado Azure Stack Edge pro R ou Azure Stack Edge é permitido para ingressar no serviço de borda Azure Stack que você cria em sua assinatura do Azure. Para autorizar um dispositivo, você precisa usar uma chave de ativação para ativar o dispositivo com o serviço do Azure Stack Edge.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-activation-key.md)]

Para obter mais informações, consulte [obter uma chave de ativação](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Proteger o dispositivo por meio de senha

As senhas garantem que somente usuários autorizados possam acessar seus dados. Os dispositivos Azure Stack Edge pro R são inicializados em um estado bloqueado.

Você pode:

- Conecte-se à interface do usuário da Web local do dispositivo por meio de um navegador e forneça uma senha para entrar no dispositivo.
- Conecte-se remotamente à interface do dispositivo do PowerShell por HTTP. O gerenciamento remoto é ativado por padrão. O gerenciamento remoto também é configurado para usar a administração suficiente (JEA) para limitar o que os usuários podem fazer. Em seguida, você pode fornecer a senha do dispositivo para entrar no dispositivo. Para obter mais informações, consulte [conectar-se remotamente ao seu dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md).
- O usuário de borda local no dispositivo tem acesso limitado ao dispositivo para configuração inicial e solução de problemas. As cargas de trabalho de computação em execução no dispositivo, transferência de dados e o armazenamento podem ser acessadas do Portal público do Azure ou do governo para o recurso na nuvem.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-password-best-practices.md)]
- Use a interface do usuário da Web local para [alterar a senha](azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#change-device-password). Se você alterar a senha, lembre-se de notificar todos os usuários de acesso remoto para que eles não tenham problemas de entrada.

### <a name="establish-trust-with-the-device-via-certificates"></a>Estabelecer confiança com o dispositivo por meio de certificados

Azure Stack dispositivo resistente à borda permite que você traga seus próprios certificados e instale-os para serem usados para todos os pontos de extremidade públicos. Para obter mais informações, acesse [carregar seu certificado](azure-stack-edge-j-series-manage-certificates.md#upload-certificates). Para obter uma lista de todos os certificados que podem ser instalados em seu dispositivo, vá para [gerenciar certificados em seu dispositivo](azure-stack-edge-j-series-manage-certificates.md).

- Quando você configura a computação em seu dispositivo, um dispositivo IoT e um IoT Edge dispositivo são criados. Esses dispositivos recebem chaves de acesso simétricas automaticamente. Como uma melhor prática de segurança, essas chaves são trocadas regularmente por meio do serviço de Hub IoT.

## <a name="protect-your-data"></a>Proteger seus dados

Esta seção descreve os recursos de segurança que protegem dados armazenados e em trânsito.

### <a name="protect-data-at-rest"></a>Proteger dados em repouso

Todos os dados em repouso no dispositivo são criptografados duas vezes, o acesso aos dados é controlado e, depois que o dispositivo é desativado, os dados são apagados com segurança dos discos de dados.

#### <a name="double-encryption-of-data"></a>Criptografia dupla de dados

Os dados em seus discos são protegidos por duas camadas de criptografia:

- A primeira camada de criptografia é a criptografia de 256 bits XTS-AES do BitLocker nos volumes de dados.
- A segunda camada são os discos rígidos que têm criptografia interna.
- O volume do sistema operacional tem o BitLocker como a camada única de criptografia.

> [!NOTE]
> O disco do sistema operacional tem a criptografia de software do BitLocker XTS-AES-256 de camada única.

Quando o dispositivo for ativado, você será solicitado a salvar um arquivo de chave que contém as chaves de recuperação que ajudam a recuperar os dados no dispositivo se o dispositivo não inicializar. Há duas chaves no arquivo:

- Uma chave recupera a configuração do dispositivo nos volumes do sistema operacional.
<!-- - Second key is to unlock the BitLocker on the data disks. -->
- A segunda chave desbloqueia a criptografia de hardware nos discos de dados.

> [!IMPORTANT]
> Salve o arquivo de chave em um local seguro fora do próprio dispositivo. Se o dispositivo não inicializar e você não tiver a chave, isso poderá resultar em perda de dados.

- Determinados cenários de recuperação solicitarão o arquivo de chave que você salvou. 
<!--- If a node isn't booting up, you will need to perform a node replacement. You will have the option to swap the data disks from the failed node to the new node. For a 4-node device, you won't need a key file. For a 1-node device, you will be prompted to provide a key file.-->

#### <a name="restricted-access-to-data"></a>Acesso restrito aos dados

O acesso a dados armazenados em compartilhamentos e contas de armazenamento é restrito.

- Clientes SMB que acessam dados de compartilhamento precisam de credenciais de usuário associadas ao compartilhamento. Essas credenciais são definidas quando o compartilhamento é criado.
- Os clientes NFS que acessam um compartilhamento precisam ter seu endereço IP adicionado explicitamente quando o compartilhamento é criado.
- As contas de armazenamento de borda que são criadas no dispositivo são locais e são protegidas pela criptografia nos discos de dados. As contas de armazenamento do Azure às quais essas contas de armazenamento de borda são mapeadas são protegidas por assinatura e chaves de acesso de armazenamento de 2 512 bits associadas à conta de armazenamento de borda (essas chaves são diferentes das que estão associadas às suas contas de armazenamento do Azure). Para obter mais informações, consulte [proteger dados em contas de armazenamento](#protect-data-in-storage-accounts).
- O BitLocker XTS-criptografia de 256 bits é usado para proteger dados locais.

#### <a name="secure-data-erasure"></a>Eliminação de dados segura

Quando o dispositivo passa por uma redefinição de hardware, um apagamento seguro é executado no dispositivo. O apagamento seguro executa a eliminação de dados nos discos usando a limpeza NIST SP 800-88r1.

### <a name="protect-data-in-flight"></a>Proteger dados em trânsito

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-data-flight.md)]

### <a name="protect-data-in-storage-accounts"></a>Proteger dados em contas de armazenamento

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-protect-data-storage-accounts.md)]

- Gire e [sincronize suas chaves de conta de armazenamento](azure-stack-edge-j-series-manage-storage-accounts.md) regularmente para ajudar a proteger sua conta de armazenamento de usuários não autorizados.

## <a name="manage-personal-information"></a>Gerenciar informações pessoais

O serviço do Azure Stack Edge coleta informações pessoais nos seguintes cenários:

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-manage-personal-data.md)]

Para exibir a lista de usuários que podem acessar ou excluir um compartilhamento, siga as etapas em [gerenciar compartilhamentos na borda de Azure Stack](azure-stack-edge-j-series-manage-shares.md).

Para obter mais informações, examine a política de privacidade da Microsoft na [central de confiabilidade](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Próximas etapas

[Implantar o dispositivo pro R do Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md)

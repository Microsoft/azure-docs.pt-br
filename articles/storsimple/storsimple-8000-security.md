---
title: "Segurança do StorSimple série 8000 | Microsoft Docs"
description: "Descreve os recursos de segurança e privacidade que protegem o serviço, os dispositivos e os dados do StorSimple no local e na nuvem."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: e4980cdb77650f011d143ed9e86fbe0af6b29d5d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-security-and-data-protection"></a>Proteção de dados e segurança de StorSimple

## <a name="overview"></a>Visão geral

A segurança é uma preocupação importante para qualquer pessoa que esteja adotando uma nova tecnologia, especialmente quando a tecnologia é usada com dados confidenciais ou proprietários. Ao avaliar as diferentes tecnologias, você deve considerar o aumento dos riscos e dos custos para a proteção dos dados. O Microsoft Azure StorSimple fornece uma solução tanto de segurança quanto de privacidade para proteção de dados, ajudando a garantir:

* **Confidencialidade** – somente entidades autorizadas podem exibir seus dados.
* **Integridade** – somente entidades autorizadas podem modificar ou excluir seus dados.

A solução Microsoft Azure StorSimple consiste em quatro componentes principais que interagem entre si:

* **Serviço Gerenciador de Dispositivos StorSimple hospedado no Microsoft Azure** – o serviço de gerenciamento que você usa para configurar e provisionar o dispositivo StorSimple.
* **Dispositivo StorSimple** – Um dispositivo físico instalado no datacenter. Todos os hosts e clientes que geram dados conectam-se ao dispositivo StorSimple, e o dispositivo gerencia os dados e move-os na nuvem do Azure conforme apropriado.
* **Clientes/hosts conectados ao dispositivo** – Os clientes em sua infraestrutura que se conectam ao dispositivo StorSimple e geram dados que precisam ser protegidos.
* **Armazenamento em nuvem** – O local na nuvem do Azure onde os dados são armazenados.

As seções a seguir descrevem os recursos de segurança do StorSimple que ajudam a proteger cada um desses componentes e os dados armazenados neles. Também incluem uma lista de perguntas sobre segurança do Microsoft Azure StorSimple e as respostas correspondentes.

## <a name="storsimple-device-manager-service-protection"></a>Proteção de serviço Gerenciador de Dispositivos StorSimple

O serviço Gerenciador de Dispositivos StorSimple é um serviço de gerenciamento hospedado no Microsoft Azure e usado para gerenciar todos os dispositivos StorSimple que sua organização adquiriu. Você pode acessar o serviço Gerenciador de Dispositivos StorSimple usando suas credenciais organizacionais para fazer logon no Portal do Azure por meio de um navegador da Web.

O acesso ao serviço Gerenciador de Dispositivos StorSimple requer que a organização tenha uma assinatura do Azure que inclua o StorSimple. Sua assinatura determina os recursos que você pode acessar no portal do Azure. Se sua organização não tiver uma assinatura do Azure e você quiser saber mais sobre ela, consulte [Inscrever-se no Azure como uma organização](../active-directory/sign-up-organization.md).

Uma vez que o serviço Gerenciador de Dispositivos StorSimple está hospedado no Azure, ele é protegido pelos recursos de segurança do Azure. Para obter mais informações sobre os recursos de segurança fornecidos pelo Microsoft Azure, acesse a [Central de Confiabilidade do Microsoft Azure](https://azure.microsoft.com/support/trust-center/security/).

## <a name="storsimple-device-protection"></a>Proteção de dispositivos StorSimple

O dispositivo StorSimple é um dispositivo de armazenamento híbrido local que contém unidades de estado sólido (SSDs) e unidades de disco rígido (HDDs), juntamente com controladores redundantes e recursos de failover automático. Os controladores de gerenciam o armazenamento hierárquico, colocando dados usados no momento (ou mais acessados) no armazenamento local (no dispositivo StorSimple ou servidores locais), ao mesmo tempo movendo dados menos dados para a nuvem.

Apenas dispositivos StorSimple autorizados têm permissão para ingressar no serviço Gerenciador de Dispositivos StorSimple que você criou na sua assinatura do Azure. Para autorizar um dispositivo, registre-o com o serviço Gerenciador de Dispositivos StorSimple informando a chave de registro do serviço. A chave de registro do serviço é uma chave aleatória de 128 bits gerada no portal do Azure.

![Chave de registro do serviço](./media/storsimple-security/ServiceRegistrationKey.png)

Para saber como obter uma chave de registro do serviço, vá para [Etapa 2: obter a chave de registro do serviço](storsimple-8000-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key).

A chave de registro de serviço é uma chave longa que contém 100 caracteres. É possível copiar a chave e salvá-la em um arquivo de texto em um local seguro para poder usá-la para autorizar dispositivos adicionais conforme necessário. Se a chave de registro do serviço for perdida depois de você registrar o primeiro dispositivo, você poderá gerar uma nova chave usando o serviço Gerenciador de Dispositivos StorSimple. Isso não afetará a operação dos dispositivos existentes.

Depois que um dispositivo é registrado, ele usa tokens para se comunicar com o Microsoft Azure. A chave de registro de serviço não é usada depois do registro do dispositivo.

> [!NOTE]
> Recomendamos a nova geração da chave de registro do serviço após cada utilização.


## <a name="protect-your-storsimple-solution-via-passwords"></a>Proteja sua solução StorSimple por meio de senhas

As senhas são um aspecto importante da segurança do computador e são usadas amplamente na solução StorSimple para ajudar a garantir que os dados fiquem acessíveis apenas para os usuários autorizados. O StorSimple permite que você configure as seguintes senhas:

* Senha do administrador de dispositivo do StorSimple
* Senhas do iniciador e de destino do protocolo CHAP
* Senha do Gerenciador de instantâneos do StorSimple

### <a name="windows-powershell-for-storsimple-and-the-storsimple-device-administrator-password"></a>Senhas do Windows PowerShell para o StorSimple e do administrador de dispositivo do StorSimple

O Windows PowerShell para StorSimple é uma interface de linha de comando que você pode usar para gerenciar o dispositivo StorSimple. O Windows PowerShell para StorSimple tem recursos que permitem registrar o dispositivo, configurar a interface de rede no dispositivo, instalar determinados tipos de atualizações, solucionar problemas do dispositivo acessando a sessão de suporte e alterar o estado do dispositivo. Você pode acessar o Windows PowerShell para StorSimple conectando-se ao console serial no dispositivo ou usando o Windows PowerShell remotamente.

A comunicação remota do PowerShell pode ser feita por meio de HTTPS ou HTTP. Se o gerenciamento remoto por HTTPS estiver habilitado, você precisará baixar o certificado de gerenciamento remoto do dispositivo e instalá-lo no cliente remoto. Para obter mais informações sobre a comunicação remota do PowerShell, vá para [Conectar remotamente ao seu dispositivo StorSimple](storsimple-8000-remote-connect.md).

Depois de usar o Windows PowerShell para StorSimple para se conectar ao dispositivo, forneça a senha de administrador do dispositivo para fazer logon no dispositivo.

![Senha do administrador do dispositivo](./media/storsimple-security/DeviceAdminPW.png)

Considere as seguintes práticas recomendadas:

* O gerenciamento remoto está desativado por padrão. Você pode usar o serviço Gerenciador de Dispositivos StorSimple para habilitá-lo. Como melhor prática de segurança, o acesso remoto deve ser habilitado somente durante o período de tempo realmente necessário.
* Se você alterar a senha, certifique-se de notificar todos os usuários de acesso remoto para que eles não tenham uma perda de conectividade inesperada.
* O serviço Gerenciador de Dispositivos StorSimple não pode recuperar senhas existentes, apenas redefini-las. É recomendável armazenar todas as senhas em um local seguro para não precisar redefinir uma senha se ela for esquecida. Se você precisar redefinir uma senha, certifique-se de notificar todos os usuários antes de fazer isso.

Você pode acessar a interface do Windows PowerShell usando uma conexão serial com o dispositivo. Você também pode acessá-la remotamente usando HTTP ou HTTPS, o que fornece segurança adicional. HTTPS oferece um nível mais alto de segurança que uma conexão HTTP ou serial. No entanto, para usar HTTPS, você deve primeiro instalar um certificado no computador cliente que acessará o dispositivo. Você pode baixar o certificado de acesso remoto da página de configuração do dispositivo no serviço Gerenciador de Dispositivos StorSimple. Se o certificado para acesso remoto for perdido, baixe um novo certificado e propague-o a todos os clientes autorizados a usar o gerenciamento remoto.

### <a name="challenge-handshake-authentication-protocol-chap-initiator-and-target-passwords"></a>Senhas do iniciador e de destino do protocolo CHAP

O CHAP é um esquema de autenticação usado pelo dispositivo StorSimple para validar a identidade de clientes remotos. A verificação se baseia em uma senha compartilhada. O protocolo CHAP pode ser unidirecional ou bidirecional (mútuo). Com o CHAP unidirecional, o destino (o dispositivo StorSimple) autentica um iniciador (host). O CHAP mútuo ou reverso exige que o destino autentique o iniciador e, em seguida, que o iniciador autentique o destino. O StorSimple pode ser configurado para usar qualquer um dos métodos.

Esteja ciente do seguinte ao configurar o CHAP:

* O nome de usuário do CHAP deve conter menos de 233 caracteres.
* A senha do CHAP deve conter entre 12 e 16 caracteres. Tentar usar um nome de usuário ou senha mais longo resultará em falha de autenticação no host do Windows.
* Você não pode usar a mesma senha para o iniciador do CHAP e o destino do CHAP.
* Depois de definir a senha, ela pode ser alterada, mas não recuperada. Se a senha for alterada, certifique-se de notificar todos os usuários de acesso remoto para que eles possam se conectar com sucesso ao dispositivo StorSimple.

Para obter mais informações sobre o CHAP e como configurá-lo para sua solução StorSimple, vá para [Configurar CHAP para seu dispositivo StorSimple](storsimple-8000-configure-chap.md).

### <a name="storsimple-snapshot-manager-password"></a>Senha do Gerenciador de instantâneos do StorSimple

O Gerenciador de instantâneos do StorSimple é um snap-in do MMC (Console de Gerenciamento Microsoft) que usa grupos de volume e o serviço de cópias de sombra de volume do Windows para gerar backups consistentes com o aplicativo. Além disso, você pode usar o Gerenciador de instantâneos do StorSimple para criar o clone e agendamentos de backup ou restaurar volumes.

Ao configurar um dispositivo para usar o Gerenciador de Instantâneos do StorSimple, forneça a senha do Gerenciador de Instantâneos do StorSimple. Essa senha é definida pela primeira vez no Windows PowerShell para o StorSimple durante o registro. A senha também pode ser definida e alterada usando o serviço Gerenciador de Dispositivos StorSimple. Essa senha autentica o dispositivo no Gerenciador de Instantâneos StorSimple.

![Senha do Gerenciador de instantâneos do StorSimple](./media/storsimple-security/SnapshotMgrPassword.png)

A senha deve ter de 14 a 15 caracteres e conter três ou mais de uma combinação de letra maiúscula, letra minúscula, caractere numérico e caracteres especiais. Depois de definir a senha do Gerenciador de instantâneos do StorSimple, ela pode ser alterada, mas não recuperada. Se você alterar a senha, certifique-se de notificar todos os usuários remotos.

Para obter mais informações sobre o Gerenciador de Instantâneos do StorSimple, acesse [O que é Gerenciador de Instantâneos do StorSimple?](storsimple-what-is-snapshot-manager.md)

### <a name="password-best-practices"></a>Práticas recomendadas de senha

É recomendável usar as seguintes diretrizes para ajudar a garantir que as senhas do StorSimple sejam fortes e bem protegidas:

* Altere suas senhas a cada três meses. A alteração das senhas é imposta anualmente.
* Use uma senha forte. Para obter mais informações, acesse [Create stronger passwords and protect them (Criar senhas fortes e protegê-las)](http://blogs.microsoft.com/cybertrust/2014/08/25/create-stronger-passwords-and-protect-them/).
* Sempre use senhas diferentes para diferentes mecanismos de acesso; cada uma das senhas especificadas deve ser exclusiva.
* Não compartilhe senhas com ninguém que não esteja autorizado a acessar o dispositivo StorSimple.
* Não fale sobre uma senha na frente de outras pessoas nem dê dicas do formato de uma senha.
* Se você suspeitar que uma conta ou senha foi comprometida, relate o incidente ao departamento de segurança da informação.
* Trate todas as senhas como informações sigilosas e confidenciais. 

## <a name="storsimple-data-protection"></a>Proteção de dados do StorSimple

Esta seção descreve os recursos de segurança do StorSimple que protegem os dados em trânsito e armazenados.

Conforme descrito em outras seções, as senhas são usadas para autorizar e autenticar os usuários antes que eles possam obter acesso à sua solução do StorSimple. Outra consideração de segurança é proteger os dados contra usuários não autorizados durante a transferência entre sistemas de armazenamento e enquanto estão sendo armazenados. As seções a seguir descrevem os recursos de proteção dos dados fornecidos com o StorSimple.

> [!NOTE]
> Eliminação de duplicação fornece proteção adicional para dados armazenados no dispositivo StorSimple e no Armazenamento do Microsoft Azure. Quando ocorre a eliminação de duplicação dos dados, os objetos de dados são armazenados separadamente dos metadados usados para mapeá-los e acessá-los: não há nenhum contexto no nível de armazenamento disponível para reconstruir os dados com base na estrutura de volume, sistema de arquivos ou nome de arquivo.


## <a name="protect-data-flowing-through-the-service"></a>Proteger dados que fluem através do serviço

O objetivo principal do serviço Gerenciador de Dispositivos StorSimple é gerenciar e configurar o dispositivo StorSimple. O serviço Gerenciador de Dispositivos StorSimple é executado no Microsoft Azure. Você usa o Portal do Azure para inserir dados de configuração de dispositivo e, em seguida, o Microsoft Azure usa o serviço Gerenciador de Dispositivos StorSimple para enviar os dados para o dispositivo. O StorSimple usa um sistema de pares de chaves assimétricas para ajudar a garantir que o comprometimento do serviço do Azure não resultará no comprometimento das informações armazenadas.

![Criptografia dos dados em trânsito](./media/storsimple-security/DataEncryption.png)

O sistema de chaves assimétricas ajuda a proteger os dados que fluem através do serviço da seguinte maneira:

1. Um certificado de criptografia de dados que usa um par de chaves pública e privada assimétricas é gerado no dispositivo e usado para proteger os dados. As chaves são geradas quando o primeiro dispositivo é registrado.
2. As chaves do certificado de criptografia de dados são exportadas para um arquivo .pfx (Troca de Informações Pessoais) protegido pela chave de criptografia de dados de serviço, que é uma chave forte de 128 bits gerada aleatoriamente pelo primeiro dispositivo durante o registro.
3. A chave pública do certificado é disponibilizada com segurança ao o serviço Gerenciador de Dispositivos StorSimple e a chave privada permanece com o dispositivo.
4. Os dados que entram no serviço são criptografados usando a chave pública e descriptografados usando a chave privada armazenada no dispositivo, garantindo que o serviço do Azure não possa descriptografar os dados que fluem para o dispositivo.

A chave da criptografia de dados do serviço é gerada apenas no primeiro dispositivo registrado com o serviço. Todos os dispositivos subsequentes registrados com o serviço devem usar a mesma chave de criptografia de dados de serviço.

> [!IMPORTANT]
> É muito importante fazer uma cópia dessa chave de criptografia e salvá-la em um local seguro. Uma cópia da chave de criptografia de dados de serviço deve ser armazenada de forma que possa ser acessada por uma pessoa autorizada e facilmente comunicada ao administrador do dispositivo.
> 
> Se a chave de criptografia de dados de serviço for perdida, um profissional de suporte da Microsoft poderá ajudar a recuperá-la, desde que você tenha pelo menos um dispositivo no estado online. É recomendável alterar a chave de criptografia de dados de serviço depois de recuperá-la. Para obter instruções, vá para [Alterar a chave de criptografia de dados do serviço](storsimple-service-dashboard.md#change-the-service-data-encryption-key).

Você pode alterar a chave de criptografia de dados do serviço e o certificado de criptografia de dados correspondente selecionando a opção **Alterar chave de criptografia de dados do serviço** no painel de serviço. Para garantir que a segurança dos dados não seja comprometida, use um dispositivo StorSimple físico para alterar a chave de criptografia de dados de serviço. Alterar as chaves de criptografia requer que todos os dispositivos sejam atualizados com a nova chave. Portanto, é recomendável que você altere a chave quando todos os dispositivos estiverem online. Se os dispositivos estiverem offline, suas chaves podem ser alteradas em um momento diferente. Os dispositivos com chaves desatualizadas ainda poderão executar backups, mas não poderão restaurar dados até que a chave seja atualizada. Para saber mais, acesse [Usar o painel de serviço Gerenciador de Dispositivos StorSimple](storsimple-8000-service-dashboard.md).

A chave de criptografia de dados de serviço e o certificado de criptografia de dados não expiram. No entanto, recomendamos a alteração anual da chave de criptografia de dados do serviço para ajudar a impedir o comprometimento da chave.

## <a name="protect-data-at-rest"></a>Proteger dados em repouso

O dispositivo StorSimple gerencia dados armazenando-os em camadas localmente e na nuvem, dependendo da frequência de uso. Todos os computadores host conectados ao dispositivo enviam dados para o dispositivo, que então os transfere para a nuvem, conforme apropriado. Os dados são transferidos do dispositivo para a nuvem com segurança pela Internet. Cada dispositivo tem um destino iSCSI que mostra todos os volumes compartilhados naquele dispositivo. Todos os dados são criptografados antes de serem enviados ao armazenamento em nuvem. 

![Chave de criptografia de armazenamento em nuvem](./media/storsimple-security/CloudStorageEncryption.png)

Para ajudar a garantir a segurança e a integridade dos dados movidos para a nuvem, o StorSimple permite definir chaves de criptografia de armazenamento em nuvem da seguinte maneira:

* Especifique a chave de criptografia de armazenamento em nuvem ao criar um contêiner de volume. A chave não pode ser modificada nem adicionada posteriormente.
* Todos os volumes em um contêiner de volume compartilham a mesma chave de criptografia. Se você quiser uma forma diferente de criptografia para um volume específico, é recomendável criar um novo contêiner de volume para hospedar esse volume.
* Ao inserir a chave de criptografia de armazenamento em nuvem no serviço Gerenciador de Dispositivos StorSimple, ela é criptografada usando a parte pública da chave de criptografia de dados de serviço e, em seguida, enviada ao dispositivo.
* A chave de criptografia de armazenamento em nuvem não é armazenada em qualquer lugar no serviço e é conhecida apenas para o dispositivo.
* Especificar uma chave de criptografia de armazenamento em nuvem é opcional. Você pode enviar dados que foram criptografados no host para o dispositivo.

### <a name="additional-security-best-practices"></a>Práticas recomendadas de segurança adicionais

* Dividir o tráfego: isolar a rede SAN do iSCSI do tráfego do usuário em uma LAN corporativa implantando uma rede totalmente separada e usando VLANs em que o isolamento físico não é uma opção. Uma rede dedicada para o armazenamento iSCSI garantirá a segurança e o desempenho de seus dados essenciais aos negócios. Misturar o tráfego de armazenamento e o de usuário em uma LAN corporativa não é recomendado e pode aumentar a latência e causar falhas de rede.
* Para a segurança de rede do lado do host, use as interfaces de rede que dão suporte a TOE (TCP/IP Offload Engine). O TOE reduz a carga de CPU processando TCP no adaptador de rede.

## <a name="protect-data-via-storage-accounts"></a>Proteger dados por meio de contas de armazenamento

Cada assinatura do Microsoft Azure pode criar uma ou mais contas de armazenamento. (Uma conta de armazenamento fornece um namespace exclusivo para trabalhar com dados armazenados na nuvem do Azure.) O acesso a uma conta de armazenamento é controlado pela assinatura e pelas chaves de acesso associadas àquela conta de armazenamento.

Quando você cria uma conta de armazenamento, o Microsoft Azure gera duas chaves de acesso de armazenamento de 512 bits, uma das quais é usada para autenticação quando o dispositivo StorSimple acessa a conta de armazenamento. Observe que apenas uma dessas chaves está em uso. A outra chave é mantida em reserva, permitindo alternar as chaves periodicamente. Para alternar as chaves, ative a chave secundária e, em seguida, exclua a chave primária. Você pode criar uma nova chave para uso durante o próximo rodízio. (Por motivos de segurança, muitos data centers exigem a rotação de chaves.)

Recomendamos seguir estas práticas recomendadas para a rotação de chaves:

* Alterne as chaves da conta de armazenamento regularmente para ajudar a garantir que sua conta de armazenamento não seja acessada por usuários não autorizados.
* Periodicamente, o administrador do Azure deve alterar ou regenerar a chave primária ou secundária usando a seção Armazenamento do portal do Azure para acessar diretamente a conta de armazenamento.

## <a name="protect-data-via-encryption"></a>Proteger dados por meio de criptografia

O StorSimple usa os seguintes algoritmos de criptografia para proteger os dados armazenados ou em trânsito entre os componentes da solução StorSimple.

| Algoritmo | Comprimento da chave | Aplicativos/protocolos/comentários |
| --- | --- | --- |
| RSA |2.048 |A RSA PKCS 1 v1.5 é usada pelo portal do Azure para criptografar dados de configuração enviados ao dispositivo: por exemplo, credenciais da conta de armazenamento, configuração do dispositivo StorSimple e chaves de criptografia de armazenamento em nuvem. |
| AES |256 |O AES com CBC é usado para criptografar a parte pública da chave de criptografia de dados de serviço antes de ela ser enviada para o portal do Azure por meio do dispositivo StorSimple. Também é usado pelo dispositivo StorSimple para criptografar os dados antes sejam enviados para a conta de armazenamento em nuvem. |

## <a name="storsimple-cloud-appliance-security"></a>Segurança do Dispositivo de Nuvem StorSimple

[!INCLUDE [storsimple Cloud Appliance security](../../includes/storsimple-virtual-device-security.md)]

## <a name="frequently-asked-questions-faq"></a>Perguntas frequentes (FAQ)

A seguir estão algumas perguntas e respostas sobre segurança e o Microsoft Azure StorSimple.

**P:** Meu serviço está comprometido. Qual deve ser minhas próximas etapas?

**R:** Você deve alterar imediatamente a chave de criptografia de dados de serviço e as chaves de conta de armazenamento para a conta de armazenamento que está sendo usada para dados em camadas. Para obter instruções, vá para:

* [Alterar a chave de criptografia de dados do serviço](storsimple-service-dashboard.md#change-the-service-data-encryption-key)
* [Rotação de chave de contas de armazenamento](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts)

**P:** Eu tenho um dispositivo StorSimple novo que está solicitando a chave de registro. Como recuperá-la?

**R:** Essa chave foi criada quando você criou o serviço Gerenciador de Dispositivos StorSimple. Quando você usa o serviço Gerenciador de Dispositivos StorSimple para conectar-se ao dispositivo, pode usar a página de início rápido do serviço para exibir ou regenerar a nova chave de registro. A geração de uma nova chave de registro do serviço não afetará os dispositivos registrados existentes. Para obter instruções, vá para:

* [Exibir ou gerar novamente a chave de registro](storsimple-8000-manage-service.md##regenerate-the-service-registration-key)

**P:** Perdi minha chave de criptografia de dados de serviço. O que devo fazer?

**R:** Entre em contato com o Suporte da Microsoft. Eles podem fazer logon em uma sessão de suporte no seu dispositivo e ajudar você a recuperar a chave (contanto que pelo menos um dispositivo esteja online). Imediatamente depois de obter a chave de criptografia de dados de serviço, você deve alterá-la para garantir que a nova chave seja conhecida apenas por você. Para obter instruções, vá para:

* [Alterar a chave de criptografia de dados do serviço](storsimple-service-dashboard.md#change-the-service-data-encryption-key)

**P:** Autorizei um dispositivo para uma alteração de chave de criptografia de dados de serviço, mas ele não iniciou o processo de alteração da chave. O que devo fazer?

**R:** Se o tempo limite tiver expirado, você terá de autorizar novamente o dispositivo para alteração da chave de criptografia de dados do serviço e reiniciar o processo.

**P:** Alterei a chave de criptografia de dados do serviço, mas não consegui atualizar outros dispositivos em quatro horas. É necessário iniciar novamente?

**R:** O período de quatro horas é apenas para iniciar a alteração. Depois de iniciar o processo de atualização no dispositivo StorSimple autorizado, a autorização será válida até que todos os dispositivos sejam atualizados.

**P:** Nosso administrador StorSimple saiu da empresa. O que devo fazer?

**R:** Altere e redefina as senhas que permitem o acesso ao dispositivo StorSimple e altere a chave de criptografia de dados do serviço para garantir que as novas informações não sejam conhecidas por pessoal não autorizado. Para obter instruções, vá para:

* [Usar o serviço do Gerenciador de Dispositivos do StorSimple para alterar suas senhas do StorSimple](storsimple-8000-change-passwords.md)
* [Alterar a chave de criptografia de dados do serviço](storsimple-service-dashboard.md#change-the-service-data-encryption-key)
* [Configure o CHAP para o seu dispositivo StorSimple](storsimple-8000-configure-chap.md)

**P:** Desejo fornecer a senha do StorSimple Snapshot Manager para um host que está se conectando ao dispositivo StorSimple, mas a senha não está disponível. O que posso fazer?

**R:** Se você tiver esquecido a senha, deverá criar uma nova. Em seguida, informe todos os usuários existentes de que a senha foi alterada e eles devem atualizar seus clientes para usar a nova senha. Para obter instruções, vá para:

* [Alterar a senha do StorSimple Snapshot Manager](storsimple-8000-change-passwords.md#set-the-storsimple-snapshot-manager-password)
* [Autenticar um dispositivo](storsimple-snapshot-manager-manage-devices.md#authenticate-a-device)

**P:** O certificado para o acesso remoto ao Windows PowerShell para StorSimple foi alterado no dispositivo. Como atualizar meus clientes de acesso remoto?

**R:** Você pode baixar o novo certificado do serviço Gerenciador de Dispositivos StorSimple e depois fornecê-lo para instalação no repositório de certificados de clientes de acesso remoto. Para obter instruções, vá para:

* [Certificado de importação de cmdlet](https://technet.microsoft.com/library/hh848630.aspx)

**P:** Os meus dados ficarão protegidos se o serviço Gerenciador de Dispositivos StorSimple estiver comprometido?

**R:** Os dados de configuração de serviço são sempre criptografados com a chave pública quando exibidos em um navegador da Web. Como o serviço não tem acesso à chave privada, o serviço não poderá ver os dados. Se o serviço Gerenciador de Dispositivos StorSimple estiver comprometido, não haverá impacto, pois não há chaves armazenadas no serviço Gerenciador de Dispositivos StorSimple.

**P:** Se alguém obtiver acesso ao certificado de criptografia de dados, meus dados serão comprometidos?

**R:** O Microsoft Azure armazena a chave de criptografia de dados do cliente (arquivo. pfx) em um formato criptografado. Como o arquivo .pfx é criptografada e o serviço StorSimple não tem a chave de criptografia de dados de serviço para descriptografar o arquivo .pfx, o simples acesso ao arquivo .pfx não vai expor nenhum segredo.

**P:** O que acontece se uma entidade governamental solicitar meus dados à Microsoft?

**R:** Como todos os dados são criptografados no serviço e a chave privada é mantida com o dispositivo, a entidade governamental deverá solicitar os dados ao cliente.

## <a name="next-steps"></a>Próximas etapas

[Implantar o dispositivo StorSimple](storsimple-8000-deployment-walkthrough-u2.md).


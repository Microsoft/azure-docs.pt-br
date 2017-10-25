---
title: Como gerar e transferir chaves de HSM protegido para o Cofre de Chaves do Azure | Microsoft Docs
description: "Use este artigo para ajudá-lo a planejar, gerar e transferir as suas próprias chaves de HSM protegido para usar com o Cofre da Chave do Azure. Também conhecido como BYOK ou Traga sua própria chave."
services: key-vault
documentationcenter: 
author: cabailey
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 51abafa1-812b-460f-a129-d714fdc391da
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: ambapat
ms.openlocfilehash: 5dbee1221f64045c64fecb344de1e03b2183dfb1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-generate-and-transfer-hsm-protected-keys-for-azure-key-vault"></a>Como gerar e transferir chaves de HSM protegido para o Cofre da Chave do Azure
## <a name="introduction"></a>Introdução
Para garantia extra, ao usar o Cofre da Chave do Azure, você pode importar ou gerar chaves em módulos de segurança de hardware (HSM) que nunca extrapolam o limite do HSM. Normalmente, este cenário é conhecido como *Trazer a sua própria chave*ou BYOK. Os HSMs têm certificação FIPS 140-2 Nível 2. O Cofre da Chave do Azure usa a família Thales nShield de HSMs para proteger as suas chaves.

Use as informações neste tópico para ajudá-lo a planejar, gerar e transferir as suas próprias chaves de HSM protegido para usar com o Cofre de Chaves do Azure.

Essa funcionalidade não está disponível para o Azure China.

> [!NOTE]
> Para obter mais informações sobre o Cofre da Chave do Azure, consulte [O que é o Cofre da Chave do Azure?](key-vault-whatis.md)  
>
> Para obter um tutorial de Introdução, que inclui a criação de um Cofre da Chave para chaves de HSM protegido, consulte [Introdução ao Cofre da Chave do Azure](key-vault-get-started.md).
>
>

Para mais informações sobre como gerar e transferir uma chave de HSM protegido pela Internet:

* Gerar a chave por meio de uma estação de trabalho offline, o que reduz a superfície de ataque.
* A chave é criptografada com uma Chave de Troca de Chaves (KEK), que permanece criptografada até que seja transferida para os HSMs do Cofre da Chave do Azure. Apenas a versão criptografada da sua chave deixa a estação de trabalho original.
* O conjunto de ferramentas define as propriedades em sua chave de locatário que associa a sua chave ao universo de segurança do Cofre da Chave do Azure. Então, após os HSMs do Cofre da Chave do Azure receberem e descriptografarem a chave, somente esses HSMs poderão usá-la. A chave não pode ser exportada. Essa associação é exigida pelos HSMs de Thales.
* A Chave de Troca de Chaves (KEK) que é usada para criptografar a sua chave é gerada dentro dos HSMs do Cofre da Chave do Azure e não é exportável. Os HSMs exigem que não possa haver nenhuma versão clara da KEK fora dos HSMs. Além disso, o conjunto de ferramentas inclui o atestado de Thales de que a KEK não é exportável e foi gerada dentro de um HSM original que foi fabricado pela Thales.
* O conjunto de ferramentas inclui atestado de Thales de que o universo de segurança do Cofre da Chave do Azure também foi gerado em um HSM original fabricado pela Thales. Essa certificação comprova que a Microsoft está usando hardware original.
* A Microsoft usa KEKs separadas e separa os universos de segurança em cada região geográfica. Essa separação garante que a chave pode ser usada somente em data centers na região em que você a criptografou. Por exemplo, uma chave de um cliente europeu não pode ser usada em data centers na América do Norte ou na Ásia.

## <a name="more-information-about-thales-hsms-and-microsoft-services"></a>Para obter mais informações sobre serviços HSMs da Thales e da Microsoft
A Thales e-Security é um provedor líder global em criptografia de dados e soluções de segurança cibernética para os serviços financeiros, de alta tecnologia, manufatura, governo e setores de tecnologia. Com um histórico de proteção corporativa e informações do governo com 40 anos de existência, as soluções Thales são usadas por quatro das cinco maiores empresas do setor de energia e aeroespacial. Suas soluções também são usadas por 22 países da OTAN e protegem mais de 80 porcento das transações de pagamento em todo o mundo.

A Microsoft tem colaborado com a Thales para aprimorar os mais recentes HSMs. Essas melhorias permitem obter os benefícios típicos dos serviços hospedados sem abrir mão do controle sobre as chaves. Especificamente, essas melhorias permitem que a Microsoft gerencie os HSMs para que você não precise fazer isso. Como um serviço de nuvem, o Cofre da Chave do Azure escala verticalmente a curto prazo para atender aos picos de uso da sua organização. Ao mesmo tempo, a chave é protegida dentro dos HSMs da Microsoft: você mantém o controle sobre o ciclo de vida da chave, pois gera a chave e a transfere para HSMs da Microsoft.

## <a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Implementando o Traga a sua própria chave (BYOK) para o Cofre da Chave do Azure
Use as seguintes informações e procedimentos, se você for gerar a sua própria chave de HSM protegido e, em seguida, transferi-la para o Cofre da Chave do Azure — cenário de Traga a sua própria chave (BYOK).

## <a name="prerequisites-for-byok"></a>Pré-requisitos para BYOK
Consulte a tabela a seguir para obter uma lista de pré-requisitos para o Traga a sua própria chave (BYOK) para o Cofre da Chave do Azure.

| Requisito | Mais informações |
| --- | --- |
| Uma assinatura do Azure |Para criar um Cofre da Chave do Azure, você precisa de uma assinatura do Azure: [Conecte-se para fazer a avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/) |
| A camada de serviço Premium do Cofre de Chaves do Azure dá suporte a chaves protegidas por HSM |Para obter mais informações sobre os recursos e as camadas de serviço para o Cofre da Chave do Azure, consulte o site [Preços do Cofre da Chave do Azure](https://azure.microsoft.com/pricing/details/key-vault/) . |
| HSM da Thales, smartcards e software de suporte |Você deve ter acesso a um módulo de segurança de Hardware da Thales e conhecimento operacional básico dos HSMs da Thales. Consulte [Módulo de segurança de Hardware da Thales](https://www.thales-esecurity.com/msrms/buy) para obter a lista de modelos compatíveis ou para comprar um HSM, se você não tiver um. |
| O hardware e o software a seguir:<ol><li>Uma estação de trabalho x64 offline com, no mínimo, um sistema operacional Windows 7 e software Thales nShield, versão 11.50 ou posterior.<br/><br/>Se essa estação de trabalho executa o Windows 7, você deve [instalar o Microsoft .NET Framework 4.5](http://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe).</li><li>Uma estação de trabalho que está conectada à Internet e tem, no mínimo, um sistema de operacional Windows 7 e o [Azure PowerShell](/powershell/azure/overview), **no mínimo, a versão 1.1.0** instalada.</li><li>Uma unidade USB ou outro dispositivo de armazenamento portátil que tenha pelo menos 16 MB de espaço livre.</li></ol> |Por motivos de segurança, é recomendável que a primeira estação de trabalho não seja conectada a uma rede. No entanto, essa recomendação não é programaticamente aplicada.<br/><br/>Observe que nas instruções a seguir, essa estação de trabalho é chamada de estação de trabalho desconectada.</p></blockquote><br/>Além disso, se a chave de locatário destina-se a uma rede de produção, recomendamos que você use uma segunda estação de trabalho separada para baixar o conjunto de ferramentas e carregar a chave de locatário. Porém, para fins de teste, você pode usar a mesma estação de trabalho que o primeiro.<br/><br/>Observe que nas instruções a seguir, essa segunda estação de trabalho é chamada de estação de trabalho conectada à Internet.</p></blockquote><br/> |

## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Gerar e transferir sua chave para o HSM do Cofre da Chave do Azure
Você usará as cinco etapas a seguir para gerar e transferir a sua chave para um HSM do Cofre de Chaves do Azure:

* [Etapa 1: preparar sua estação de trabalho conectada à Internet](#step-1-prepare-your-internet-connected-workstation)
* [Etapa 2: preparar sua estação de trabalho desconectada](#step-2-prepare-your-disconnected-workstation)
* [Etapa 3: Gerar a sua chave](#step-3-generate-your-key)
* [Etapa 4: Preparar a sua chave para transferência](#step-4-prepare-your-key-for-transfer)
* [Etapa 5: Transferir a sua chave para o Cofre da Chave do Azure](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>Etapa 1: preparar sua estação de trabalho conectada à Internet
Nessa primeira etapa, siga os procedimentos a seguir em sua estação de trabalho que está conectada à Internet.

### <a name="step-11-install-azure-powershell"></a>Etapa 1.1: Instalar o Azure PowerShell
Na estação de trabalho conectada à Internet, baixe e instale o módulo do Azure PowerShell que inclui os cmdlets para gerenciar o Cofre da Chave do Azure. Isso requer uma versão mínima do 0.8.13.

Para obter instruções de instalação, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

### <a name="step-12-get-your-azure-subscription-id"></a>Etapa 1.2: Obter a sua ID da assinatura do Azure
Inicie uma sessão do Azure PowerShell e entre em sua conta do Azure usando o seguinte comando:

        Add-AzureAccount
Na janela pop-up do navegador, insira o nome de usuário e a senha da sua conta do Azure. Em seguida, use o comando [Get-AzureSubscription](/powershell/module/azure/get-azuresubscription?view=azuresmps-3.7.0) :

        Get-AzureSubscription
Na saída, localize a ID para a assinatura que você usará para o Cofre da Chave do Azure. Você precisará dessa ID da assinatura mais tarde.

Não feche a janela do Azure PowerShell.

### <a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>Etapa 1.3: Baixe o conjunto de ferramentas BYOK para o Cofre da Chave do Azure
Vá para Centro de Download da Microsoft e [baixe o conjunto de ferramentas BYOK do Cofre de Chaves do Azure](http://www.microsoft.com/download/details.aspx?id=45345) para a sua região ou instância do Azure. Use as seguintes informações para identificar o nome do pacote para download e seu hash do pacote SHA-256 correspondente:

- - -
**Estados Unidos:**

KeyVault-BYOK-Tools-UnitedStates.zip

760EE9BD6445C87CFF0E8B032577118704B3BEAA045AA55977C10EF68BC67E2B

- - -
**Europa:**

KeyVault-BYOK-Tools-Europe.zip

7A64B94225F59B847C5C27C2200BAD7D16C901E1687767EDBBB8B09BB285011D

- - -
**Ásia:**

KeyVault-BYOK-Tools-AsiaPacific.zip

813DC94B23079CF7A5CEA71D5B444E86B292F463C53EE47AED25D4F7CD58E7D8

- - -
**América Latina:**

KeyVault-BYOK-Tools-LatinAmerica.zip

3F29069E3500F95C0E156F4B8914E1DC60C20FB64B464306A299EA5145D755C0

- - -
**Japão:**

KeyVault-BYOK-Tools-Japan.zip

453FFEA2F8F410720B68B8BAC4CF79135A7F37F4E491FF840BE9E69E88A98C90

- - -
**Coreia:**

KeyVault-BYOK-Tools-Korea.zip

C17B7E93224DA80F5668E09CF7DAE2F92527E8226179995BBE2E43DA4323595A

- - -
**Austrália:**

KeyVault-BYOK-Tools-Australia.zip

4AD893396E86F2D2A71682876A6A8EA59E3C7895BEAD2F7E7C8516682582C34B

- - -
[**Azure Government:**](https://azure.microsoft.com/features/gov/)

KeyVault-BYOK-Tools-USGovCloud.zip

3AAE1A96B9D15B899B8126CFC0380719EB54FDF2EA94489B43FAD21ECC745F64

- - -
**US Government DoD:**

KeyVault-BYOK-Tools-USGovernmentDoD.zip

A61E78297B0732DF2682FDE63D7B572CE4D23B0BC27CC48AFF620BD060BB9E9D

- - -
**Canadá:**

KeyVault-BYOK-Tools-Canada.zip

30B87A0BA8208F6B7241C30C794FED1C370D7445ACA179685816E4E156CD2AF7

- - -
**Alemanha:**

KeyVault-BYOK-Tools-Germany.zip

5E3E4AA54715E4F93C3C145035B18275B7C6815A06D7ABB212E7FADBF2929261

- - -
**Índia:**

KeyVault-BYOK-Tools-India.zip

136733A6C6A71D75571BB80819B3D55A9B83CCAD5C996C686BC5682A3F369BF7

- - -
**Reino Unido:**

KeyVault-BYOK-Tools-UnitedKingdom.zip

ED331A6F1D34A402317D3F27D5396046AF0E5C2D44B5D10CCCE293472942D268

- - -

Para validar a integridade do seu conjunto de ferramentas BYOK baixado, na sua sessão do Azure PowerShell, use o cmdlet [Get-FileHash](https://technet.microsoft.com/library/dn520872.aspx) .

    Get-FileHash KeyVault-BYOK-Tools-*.zip

O conjunto de ferramentas inclui o seguinte:

* Um pacote de Chave de Troca de Chave (KEK) que tem um nome que começa com **BYOK-KEK-pkg-.**
* Um pacote de Universo de Segurança que tem um nome que começa com **BYOK-SecurityWorld-pkg-.**
* Um script do Python chamado **verifykeypackage.py.**
* Um arquivo executável de linha de comando chamado **KeyTransferRemote.exe** e DLLs associadas.
* Um pacote redistribuível do Visual C++, chamado **vcredist_x64.exe.**

Copie o pacote para uma unidade USB ou outro armazenamento portátil.

## <a name="step-2-prepare-your-disconnected-workstation"></a>Etapa 2: preparar sua estação de trabalho desconectada
Para essa segunda etapa, siga os procedimentos a seguir na estação de trabalho que não está conectado a uma rede (Internet ou rede interna).

### <a name="step-21-prepare-the-disconnected-workstation-with-thales-hsm"></a>Etapa 2.1: Preparar a estação de trabalho desconectada com HSM da Thales
Instale o software de suporte nCipher (Thales) em um computador Windows e, em seguida, anexe um HSM da Thales a esse computador.

Verifique se as ferramentas da Thales estão no caminho (**%nfast_home%\bin**). Por exemplo, digite o seguinte:

        set PATH=%PATH%;"%nfast_home%\bin"

Para obter mais informações, consulte o guia do usuário fornecido com o HSM da Thales.

### <a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>Etapa 2.2: Instalar o conjunto de ferramentas BYOK na estação de trabalho desconectada
Copie o pacote do conjunto de ferramentas BYOK da unidade USB ou outro armazenamento portátil e, em seguida, faça o seguinte:

1. Extraia os arquivos do pacote baixado em qualquer pasta.
2. Nessa pasta, execute o vcredist_x64.exe.
3. Siga as instruções para instalar os componentes de tempo de execução do Visual C++ para o Visual Studio 2013.

## <a name="step-3-generate-your-key"></a>Etapa 3: Gerar a sua chave
Para a terceira etapa, execute os seguintes procedimentos na estação de trabalho desconectada. Para concluir esta etapa, o HSM deve estar no modo de inicialização. 


### <a name="step-31-change-the-hsm-mode-to-i"></a>Etapa 3.1: Alterar o modo do HSM para “I”
Se você estiver usando o nShield Edge da Thales, para alterar o modo: 1. Use o botão Modo para realçar o modo solicitado. 2. Em poucos segundos, pressione e mantenha pressionado o botão Limpar por alguns segundos. Se o modo for alterado, o LED do novo modo parará de piscar e permanecerá aceso. O LED de Status poderá piscar de forma irregular por alguns segundos e, em seguida, piscar com regularidade quando o dispositivo estiver pronto. Caso contrário, o dispositivo permanecerá no modo atual, com o LED do modo apropriado aceso.

### <a name="step-32-create-a-security-world"></a>Etapa 3.2: Criar um universo de segurança
Inicie um prompt de comando e execute o programa do novo universo da Thales.

    new-world.exe --initialize --cipher-suite=DLf1024s160mRijndael --module=1 --acs-quorum=2/3

Este programa cria um arquivo **Universo de Segurança** em %NFAST_KMDATA%\local\world, que corresponde à pasta C:\ProgramData\nCipher\Key Management Data\local. É possível usar valores diferentes para o quorum, mas, no nosso exemplo, você será solicitado a inserir três cartões em branco e pins para cada um deles. Em seguida, os dois cartões darão acesso completo ao universo de segurança. Esses cartões se tornam o **Conjunto de Cartões do Administrador** para o novo universo de segurança.

Faremos o seguinte:

* Faça backup do arquivo do universo. Proteja o arquivo do universo, os Cartões do Administrador, juntamente com os seus pins, e certifique-se de que ninguém tenha acesso a mais de um cartão.

### <a name="step-33-change-the-hsm-mode-to-o"></a>Etapa 3.3: Alterar o modo do HSM para “O”
Se você estiver usando o nShield Edge da Thales, para alterar o modo: 1. Use o botão Modo para realçar o modo solicitado. 2. Em poucos segundos, pressione e mantenha pressionado o botão Limpar por alguns segundos. Se o modo for alterado, o LED do novo modo parará de piscar e permanecerá aceso. O LED de Status poderá piscar de forma irregular por alguns segundos e, em seguida, piscar com regularidade quando o dispositivo estiver pronto. Caso contrário, o dispositivo permanecerá no modo atual, com o LED do modo apropriado aceso.


### <a name="step-34-validate-the-downloaded-package"></a>Etapa 3.4: Validar o pacote baixado
Esta etapa é opcional, mas recomendada, para que você possa validar o seguinte:

* A Chave de Troca de Chaves que está incluída no conjunto de ferramentas foi gerada em um HSM original da Thales.
* O hash do Universo de segurança está incluído no conjunto de ferramentas que foi gerado em um HSM original da Thales.
* A Chave de Troca de Chaves é não exportável.

> [!NOTE]
> Para validar o pacote baixado, o HSM deve estar conectado, ligado e deve ter um Universo de segurança nele (como aquele que você acabou de criar).
>
>

Para validar o pacote baixado:

1. Execute o script verifykeypackage.py, digitando um dos seguintes, dependendo da sua região geográfica ou da instância do Azure:

   * Para a América do Norte:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
   * Para a Europa:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
   * Para a Ásia:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
   * Para a América Latina:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
   * Para o Japão:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
   * Para a Coreia:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-KOREA-1 -w BYOK-SecurityWorld-pkg-KOREA-1
   * Para a Austrália:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
   * Para o [Azure Government](https://azure.microsoft.com/features/gov/), que usa a instância do governo dos EUA do Azure:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
   * Para US Government DoD:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USDOD-1 -w BYOK-SecurityWorld-pkg-USDOD-1
   * Para o Canadá:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
   * Para a Alemanha:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * Para a Índia:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
     > [!TIP]
     > O software Thales inclui python em %NFAST_HOME%\python\bin
     >
     >
2. Confirme que você vê o seguinte, que indica a validação bem-sucedida: **Resultado: SUCCESS**

Este script valida a cadeia do signatário até a chave-raiz da Thales. O hash dessa chave raiz é inserido no script e o seu valor deve ser **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**. Você também pode confirmar esse valor separadamente visitando o [site da Thales](http://www.thalesesec.com/).

Agora você está pronto para criar uma nova chave.

### <a name="step-35-create-a-new-key"></a>Etapa 3.5: Criar uma nova chave
Gere uma chave usando o programa **generatekey** da Thales.

Execute o seguinte comando para gerar a chave:

    generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

Quando você executar esse comando, use estas instruções:

* O parâmetro *protect* deve ser definido com o valor **module**, como mostrado. Isso cria uma chave protegida pelo módulo. O conjunto de ferramentas BYOK não oferece suporte a chaves protegidas por OCS.
* Substitua o valor de *contosokey* para a **ident** e **plainname** por qualquer valor de cadeia de caracteres. Para minimizar os custos administrativos e reduzir o risco de erros, recomendamos que você use o mesmo valor para ambos. O valor de **ident** deve conter somente números, traços, letras minúsculas.
* O pubexp é deixado em branco (padrão) neste exemplo, mas você pode especificar valores específicos. Para obter mais informações, consulte a Documentação da Thales.

Este comando cria um arquivo de Chave com Token na sua pasta %NFAST_KMDATA%\local com um nome iniciado por **key_simple_** seguido pela **ident** que foi especificada no comando. Por exemplo: **key_simple_contosokey**. Esse arquivo contém uma chave criptografada.

Faça backup deste arquivo de Chave com Token em um local seguro.

> [!IMPORTANT]
> Posteriormente, quando transferir a sua chave para o Cofre da Chave do Azure, a Microsoft não poderá exportar esta chave novamente para você, por isso é extremamente importante fazer backup da sua chave e da segurança do Universo de segurança. Entre em contato com a Thales para obter orientação e as práticas recomendadas para fazer backup da sua chave.
>
>

Agora você está pronto para transferir a sua chave para o Cofre da Chave do Azure.

## <a name="step-4-prepare-your-key-for-transfer"></a>Etapa 4: Preparar a sua chave para transferência
Para esta quarta etapa, execute os seguintes procedimentos na estação de trabalho desconectada.

### <a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>Etapa 4.1: Criar uma cópia da chave com permissões reduzidas

Abra um novo prompt de comando e altere o diretório atual para a localização em que você descompactou o arquivo zip BYOK. Para reduzir as permissões em sua chave, em um prompt de comando, execute um dos seguintes, dependendo da sua região geográfica ou da instância do Azure:

* Para a América do Norte:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1
* Para a Europa:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
* Para a Ásia:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
* Para a América Latina:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
* Para o Japão:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
* Para a Coreia:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1
* Para a Austrália:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
* Para o [Azure Government](https://azure.microsoft.com/features/gov/), que usa a instância do governo dos EUA do Azure:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
* Para US Government DoD:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1
* Para o Canadá:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
* Para a Alemanha:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* Para a Índia:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1

Quando você executar esse comando, substitua *contosokey* pelo mesmo valor especificado na **Etapa 3.5: Criar uma nova chave** da etapa [Gerar a chave](#step-3-generate-your-key).

Você é solicitado a conectar seus cartões de admin do Universo de segurança.

Quando o comando for concluído, você verá **Resultado: SUCCESS** e a cópia da sua chave com permissões reduzidas estará no arquivo chamado key_xferacId_<contosokey>.

Inspecione as ACLs usando os seguintes comandos e os utilitários da Thales:

* aclprint.py:

        "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
* kmfile-dump.exe:

        "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
  Quando você executar estes comandos, substitua contosokey pelo mesmo valor especificado na **Etapa 3.5: Criar uma nova chave** da etapa [Gerar a chave](#step-3-generate-your-key).

### <a name="step-42-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>Etapa 4.2: Criptografar a chave usando a Chave de Troca de Chaves da Microsoft
Execute um dos comandos a seguir, dependendo da sua região geográfica ou da instância do Azure:

* Para a América do Norte:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para a Europa:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para a Ásia:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para a América Latina:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para o Japão:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para a Coreia:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para a Austrália:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para o [Azure Government](https://azure.microsoft.com/features/gov/), que usa a instância do governo dos EUA do Azure:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para US Government DoD:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para o Canadá:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para a Alemanha:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para a Índia:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey

Quando você executar esse comando, use estas instruções:

* Substitua *contosokey* pelo identificador usado para gerar a chave na **Etapa 3.5: Criar uma nova chave** da etapa [Gerar a chave](#step-3-generate-your-key).
* Substitua *SubscriptionID* pela ID da assinatura do Azure que contém o seu Cofre da Chave. Esse valor foi recuperado anteriormente, na **Etapa 1.2: Obter a sua ID da assinatura do Azure** na etapa [Preparar a sua estação de trabalho conectada à Internet](#step-1-prepare-your-internet-connected-workstation) .
* Substitua *ContosoFirstHSMKey* por um rótulo que seja usado para o nome do arquivo de saída.

Quando isso for concluído com êxito, será exibido **Resultado: ÊXITO** e haverá um novo arquivo na pasta atual que tem o seguinte nome: KeyTransferPackage-*ContosoFirstHSMkey*.byok

### <a name="step-43-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>Etapa 4.3: Copiar o pacote de transferência de chave para a estação de trabalho conectada à Internet
Use uma unidade USB ou outro armazenamento portátil para copiar o arquivo de saída da etapa anterior (KeyTransferPackage-ContosoFirstHSMkey.byok) para sua estação de trabalho conectada à Internet.

## <a name="step-5-transfer-your-key-to-azure-key-vault"></a>Etapa 5: Transferir a sua chave para o Cofre da Chave do Azure
Para essa etapa final, na estação de trabalho conectada à Internet, use o cmdlet [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurermkeyvaultkey) para carregar o pacote de transferência de chave que você copiou da estação de trabalho desconectada para o HSM do Cofre de Chaves do Azure:

    Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\KeyTransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'

Se o upload for bem-sucedido, você verá exibidas as propriedades da chave que você acabou de adicionar.

## <a name="next-steps"></a>Próximas etapas
Agora você pode usar essa chave de HSM protegido no Cofre da Chave. Para saber mais, consulte a seção **Se você deseja usar um módulo de segurança de hardware (HSM)** no tutorial [Introdução ao cofre da chave do Azure](key-vault-get-started.md) .

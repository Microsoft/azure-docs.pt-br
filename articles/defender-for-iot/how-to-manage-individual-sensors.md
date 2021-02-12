---
title: Gerenciar sensores individuais
description: Saiba como gerenciar sensores individuais, incluindo o gerenciamento de arquivos de ativação, a realização de backups e a atualização de um sensor autônomo.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/12/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: b35851bae8db39392d10a302d5f1059ba3ace696
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99508753"
---
# <a name="manage-individual-sensors"></a>Gerenciar sensores individuais

Este artigo descreve como gerenciar sensores individuais. As tarefas incluem o gerenciamento de arquivos de ativação, a realização de backups e a atualização de um sensor autônomo.

Você também pode realizar determinadas tarefas de gerenciamento de sensor no console de gerenciamento local, em que vários sensores podem ser gerenciados simultaneamente.

Você usa o portal do Azure para integração e registro de sensor.

## <a name="manage-sensor-activation-files"></a>Gerenciar arquivos de ativação do sensor

Seu sensor foi integrado ao Azure defender para IoT da portal do Azure. Cada sensor foi integrado como um sensor conectado localmente ou um sensor conectado à nuvem.

Um arquivo de ativação exclusivo é carregado para cada sensor que você implanta. Para obter mais informações sobre quando e como usar um novo arquivo, consulte [carregar novos arquivos de ativação](#upload-new-activation-files). Se você não puder carregar o arquivo, consulte [solucionar problemas de upload de arquivo de ativação](#troubleshoot-activation-file-upload).

### <a name="about-activation-files-for-locally-connected-sensors"></a>Sobre arquivos de ativação para sensores conectados localmente

Sensores conectados localmente são associados a uma assinatura do Azure. O arquivo de ativação para os sensores conectados localmente contém uma data de expiração. Um mês antes dessa data, uma mensagem de aviso será exibida na parte superior do console do sensor. O aviso permanece até que você tenha atualizado o arquivo de ativação.

:::image type="content" source="media/how-to-manage-individual-sensors/system-setting-screenshot.png" alt-text="A captura de tela das configurações do sistema.":::

Você pode continuar a trabalhar com os recursos do defender para IoT, mesmo que o arquivo de ativação tenha expirado. 

### <a name="about-activation-files-for-cloud-connected-sensors"></a>Sobre arquivos de ativação para sensores conectados à nuvem

Os sensores que estão conectados à nuvem são associados ao defender para o Hub IoT. Esses sensores não são limitados por períodos de tempo para o arquivo de ativação. O arquivo de ativação para sensores conectados à nuvem é usado para garantir a conexão com o defender para o Hub IoT.

### <a name="upload-new-activation-files"></a>Carregar novos arquivos de ativação

Talvez seja necessário carregar um novo arquivo de ativação para um sensor integrado quando:

- Um arquivo de ativação expira em um sensor conectado localmente. 

- Você deseja trabalhar em um modo de gerenciamento de sensor diferente. 

- Você deseja atribuir um novo defender para Hub IoT a um sensor conectado à nuvem.

Para adicionar um novo arquivo de ativação:

1. Vá para a página de **Gerenciamento do sensor** .

2. Selecione o sensor para o qual você deseja carregar um novo arquivo de ativação.

3. Exclua-a.

4. Integre o sensor novamente da página de **integração** no modo novo ou com um novo defender para o Hub IOT.

5. Baixe o arquivo de ativação na página **baixar arquivo de ativação** .

6. Salve o arquivo.

    :::image type="content" source="media/how-to-manage-individual-sensors/download-activation-file.png" alt-text="Baixe o arquivo de ativação do Hub do defender para IoT.":::

7. Entre no console do sensor do defender para IoT.

8. No console do sensor, selecione **configurações do sistema**  >  **reativação**.

    :::image type="content" source="media/how-to-manage-individual-sensors/reactivation.png" alt-text="Seleção de reativação na tela Configurações do sistema.":::

9. Selecione **carregar** e selecione o arquivo que você salvou.

    :::image type="content" source="media/how-to-manage-individual-sensors/upload-the-file.png" alt-text="Carregue o arquivo que você salvou.":::

10. Selecione **Ativar**.

### <a name="troubleshoot-activation-file-upload"></a>Solucionar problemas de upload do arquivo de ativação

Você receberá uma mensagem de erro se o arquivo de ativação não pôde ser carregado. Os seguintes eventos podem ter ocorrido:

- **Para sensores conectados localmente**: o arquivo de ativação não é válido. Se o arquivo não for válido, vá para o portal do defender para IoT. Na página **Gerenciamento do sensor** , selecione o sensor com o arquivo inválido e baixe um novo arquivo de ativação.

- **Para sensores conectados à nuvem**: o sensor não pode se conectar à Internet. Verifique a configuração de rede do sensor. Se o seu sensor precisar se conectar por meio de um proxy Web para acessar a Internet, verifique se o servidor proxy está configurado corretamente na tela de **configuração de rede do sensor** . Verifique se \* . Azure-Devices.net:443 é permitido no firewall e/ou proxy. Se não houver suporte para caracteres curinga ou se você quiser mais controle, o FQDN do seu defender específico para o Hub IoT deverá ser aberto em seu firewall e/ou proxy. Para obter detalhes, consulte [referência-pontos de extremidade do Hub IOT](../iot-hub/iot-hub-devguide-endpoints.md).  

- **Para sensores conectados à nuvem**: o arquivo de ativação é válido, mas o defender para IOT o rejeitou. Se você não puder resolver esse problema, poderá baixar outra ativação na página de **Gerenciamento do sensor** do portal do defender para IOT. Se isso não funcionar, entre em contato com Suporte da Microsoft.

## <a name="manage-certificates"></a>Gerenciar certificados

Após a instalação do sensor, um certificado autoassinado local é gerado e usado para acessar o aplicativo Web do sensor. Ao fazer logon no sensor pela primeira vez, os usuários administradores são solicitados a fornecer um certificado SSL/TLS.  Para obter mais informações sobre a configuração inicial, consulte [entrar e ativar um sensor](how-to-activate-and-set-up-your-sensor.md).

Este artigo fornece informações sobre como atualizar certificados, trabalhar com comandos da CLI de certificados e certificados com suporte e parâmetros de certificado.

### <a name="about-certificates"></a>Sobre certificados

O Azure defender para IoT usa certificados SSL/TLS para:

- Atenda aos requisitos específicos de certificado e criptografia solicitados por sua organização carregando o certificado assinado por AC.

- Permita a validação entre o console de gerenciamento e os sensores conectados e entre um console de gerenciamento e um console de gerenciamento de alta disponibilidade. As validações são avaliadas em relação a uma lista de certificados revogados e a data de expiração do certificado. *Se a validação falhar, a comunicação entre o console de gerenciamento e o sensor será interrompida e um erro de validação será apresentado no console*. Essa opção é habilitada por padrão após a instalação.

 Regras de encaminhamento de terceiros, por exemplo, informações de alerta enviadas para SYSLOG, Splunk ou ServiceNow; ou as comunicações com Active Directory não são validadas.

#### <a name="ssl-certificates"></a>Certificados SSL

O sensor do defender for IoT e o console de gerenciamento local usam SSL e certificados TLS para as seguintes funções: 

 - Proteger as comunicações entre os usuários e o console Web do dispositivo. 
 
 - Proteger as comunicações com a API REST no sensor e no console de gerenciamento local.
 
 - Proteger as comunicações entre os sensores e um console de gerenciamento local. 

Uma vez instalado, o dispositivo gera um certificado autoassinado local para permitir o acesso preliminar ao console Web. O SSL corporativo e os certificados TLS podem ser instalados usando a [`cyberx-xsense-certificate-import`](#cli-commands) ferramenta de linha de comando. 

 > [!NOTE]
 > Para integrações e regras de encaminhamento em que o dispositivo é o cliente e o iniciador da sessão, certificados específicos são usados e não estão relacionados aos certificados do sistema.  
 >
 >Nesses casos, os certificados são geralmente recebidos do servidor ou usam a criptografia assimétrica, em que um certificado específico será fornecido para configurar a integração.

Os dispositivos podem usar arquivos de certificado exclusivos. Se você precisar substituir um certificado, você carregou;

- Na versão 10,0, o certificado pode ser substituído no menu de configurações do sistema.

- Para versões anteriores a 10,0, o certificado SSL pode ser substituído usando a ferramenta de linha de comando.

### <a name="update-certificates"></a>Atualizar certificados

Os usuários administradores de sensor podem atualizar certificados.

Para atualizar um certificado:  

1. Selecione **configurações do sistema**.

1. Selecione **certificados SSL/TLS.**
1. Exclua ou edite o certificado e adicione um novo.

    - Adicione um nome de certificado.
    
    - Carregue um arquivo e chave do CRT e insira uma frase secreta.
    - Carregue um arquivo PEM, se necessário.

Para alterar a configuração de validação:

1. Habilitar ou desabilitar a alternância **Habilitar validação de certificado** .

1. Selecione **Salvar**.

Se a opção estiver habilitada e a validação falhar, a comunicação entre o console de gerenciamento e o sensor será interrompida e um erro de validação será apresentado no console do.

### <a name="certificate-support"></a>Suporte a certificados

Há suporte para os seguintes certificados:

- Infraestrutura privada e de chave corporativa (PKI particular)

- Infraestrutura de chave pública (PKI pública) 

- Gerado localmente no dispositivo (autoassinado localmente). 

> [!IMPORTANT]
> Não recomendamos o uso de certificados autoassinados. Esse tipo de conexão não é seguro e deve ser usado somente para ambientes de teste. Como o proprietário do certificado não pode ser validado, e a segurança do sistema não pode ser mantida, os certificados autoassinados nunca devem ser usados para redes de produção.

### <a name="supported-ssl-certificates"></a>Certificados SSL com suporte 

Há suporte para os parâmetros a seguir. 

**CRT de certificado**

- O arquivo de certificado primário para seu nome de domínio

- Algoritmo de assinatura = SHA256RSA
- Algoritmo de hash de assinatura = SHA256
- Válido a partir de = data anterior válida
- Válido para = data futura válida
- Chave pública = RSA 2048 bits (mínimo) ou 4096 bits
- Ponto de distribuição de CRL = URL para arquivo. CRL
- O assunto CN = URL, pode ser um certificado curinga; por exemplo, sensor. contoso. <span> com ou *. contoso. <span> interfaces
- Subject (C) ountry = definido, por exemplo, nós
- Unidade organizacional (UO) da organização = definido, por exemplo, laboratórios da contoso
- Subject (O) rganização = definido, por exemplo, Contoso Inc.

**Arquivo de chave**

- O arquivo de chave gerado quando você criou o CSR.

- RSA 2048 bits (mínimo) ou 4096 bits.

 > [!Note]
 > Usando um comprimento de chave de 4096bits:
 > - O handshake SSL no início de cada conexão será mais lento.  
 > - Há um aumento no uso da CPU durante os Handshakes. 

**Cadeia de certificados**

- O arquivo de certificado intermediário (se houver) que foi fornecido pela sua autoridade de certificação

- O certificado de autoridade de certificação que emitiu o certificado do servidor deve ser o primeiro no arquivo, seguido por qualquer outro até a raiz. 
- Pode incluir atributos de conjunto.

**Senha**

- Uma chave com suporte.

- Configurar quando você estiver importando o certificado.

Certificados com outros parâmetros podem funcionar, mas a Microsoft não oferece suporte a eles.

#### <a name="encryption-key-artifacts"></a>Artefatos de chave de criptografia

**. pem – arquivo de contêiner de certificado**

Os arquivos Privacy Enhanced Mail (PEM) eram o tipo de arquivo geral usado para proteger emails. Hoje em dia, os arquivos PEM são usados com certificados e usam chaves ASN1 X509.  

O arquivo de contêiner é definido nas RFCs 1421 a 1424, um formato de contêiner que pode incluir apenas o certificado público. Por exemplo, o Apache instala, um certificado de autoridade de certificação, arquivos, ETC, SSL ou certificados. Isso pode incluir uma cadeia de certificados inteira, incluindo chave pública, chave privada e certificados raiz.  

Ele também pode codificar um CSR como o formato PKCS10, que pode ser convertido em PEM.

**. cert. cer. CRT – arquivo de contêiner de certificado**

Um `.pem` , ou um `.der` arquivo formatado com uma extensão diferente. O arquivo é reconhecido pelo Windows Explorer como um certificado. O `.pem`   arquivo não é reconhecido pelo Windows Explorer.

**. chave – arquivo de chave privada**

Um arquivo de chave está no mesmo formato que um arquivo PEM, mas tem uma extensão diferente.

#### <a name="additional-commonly-available-key-artifacts"></a>Artefatos de chave mais comuns disponíveis

**. CSR-solicitação de assinatura de certificado**.  

Esse arquivo é usado para envio para autoridades de certificação. O formato real é PKCS10, que é definido no RFC 2986, e pode incluir alguns ou todos os detalhes principais do certificado solicitado. Por exemplo, assunto, organização e estado. É a chave pública do certificado que é assinada pela AC e recebe um certificado em retorno.  

O certificado retornado é o certificado público, que inclui a chave pública, mas não a chave privada. 

**. PKCS12. pfx. p12 – contêiner de senha**. 

Originalmente definido pela RSA nos padrões de criptografia de Public-Key (PKCS), a 12 variante foi originalmente aprimorada pela Microsoft e enviada posteriormente como RFC 7292.  

Esse formato de contêiner requer uma senha que contenha pares de certificados públicos e privados. Ao contrário dos `.pem`   arquivos, esse contêiner é totalmente criptografado.  

Você pode usar o OpenSSL para transformá-lo em um `.pem`   arquivo com as chaves pública e privada: `openssl pkcs12 -in file-to-convert.p12 -out converted-file.pem -nodes`  

**. der – PEM codificado em binário**.

A maneira de codificar a sintaxe ASN. 1 em binary, é por meio de um `.pem`   arquivo, que é apenas um arquivo codificado em base64 `.der` . 

O OpenSSL pode converter esses arquivos em um `.pem` :  `openssl x509 -inform der -in to-convert.der -out converted.pem` .  

O Windows reconhecerá esses arquivos como arquivos de certificado. Por padrão, o Windows vai exportar certificados como `.der` arquivos formatados com uma extensão diferente.  

**. CRL-lista de certificados revogados**.  
As autoridades de certificação produzem esses arquivos como uma maneira de desautorizar certificados antes da expiração.
 
##### <a name="cli-commands"></a>Comandos de CLI

Use o `cyberx-xsense-certificate-import` comando da CLI para importar certificados. Para usar essa ferramenta, você precisa carregar arquivos de certificado para o dispositivo, usando ferramentas como WinSCP ou wget.

O comando dá suporte aos seguintes sinalizadores de entrada:

- `-h`: Mostra a sintaxe de ajuda da linha de comando.

- `--crt`: Caminho para um arquivo de certificado (extensão. CRT).

- `--key`\*arquivo de chave:. O comprimento da chave deve ser no mínimo 2.048 bits.

- `--chain`: Caminho para um arquivo de cadeia de certificados (opcional).

- `--pass`: Senha usada para criptografar o certificado (opcional).

- `--passphrase-set`: Padrão = `False` , não usado. Defina como `True` para usar a frase secreta anterior fornecida com o certificado anterior (opcional).

Quando você estiver usando o comando da CLI:

- Verifique se os arquivos de certificado estão legíveis no dispositivo.

- Verifique se o nome de domínio e o IP no certificado correspondem à configuração que o departamento de ti planejou.

### <a name="use-openssl-to-manage-certificates"></a>Usar o OpenSSL para gerenciar certificados

Gerencie seus certificados com os seguintes comandos:

| Descrição | Comando da CLI |
|--|--|
| Gerar uma nova chave privada e uma solicitação de assinatura de certificado | `openssl req -out CSR.csr -new -newkey rsa:2048 -nodes -keyout privateKey.key` |
| Gerar um certificado autoassinado | `openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out certificate.crt` |
| Gerar uma solicitação de assinatura de certificado (CSR) para uma chave privada existente | `openssl req -out CSR.csr -key privateKey.key -new` |
| Gerar uma solicitação de assinatura de certificado com base em um certificado existente | `openssl x509 -x509toreq -in certificate.crt -out CSR.csr -signkey privateKey.key` |
| Remover uma frase secreta de uma chave privada | `openssl rsa -in privateKey.pem -out newPrivateKey.pem` |

Se você precisar verificar as informações em um certificado, CSR ou chave privada, use estes comandos;

| Descrição | Comando da CLI |
|--|--|
| Verificar uma solicitação de assinatura de certificado (CSR) | `openssl req -text -noout -verify -in CSR.csr` |
| Verificar uma chave privada | `openssl rsa -in privateKey.key -check` |
| Verificar um certificado | `openssl x509 -in certificate.crt -text -noout`  |

Se você receber um erro informando que a chave privada não corresponde ao certificado ou que um certificado que você instalou em um site não é confiável, use estes comandos para corrigir o erro;

| Descrição | Comando da CLI |
|--|--|
| Verificar um hash MD5 da chave pública para garantir que ela corresponda ao que está em um CSR ou em uma chave privada | uma. `openssl x509 -noout -modulus -in certificate.crt | openssl md5` <br /> 2. `openssl rsa -noout -modulus -in privateKey.key | openssl md5` <br /> 3. `openssl req -noout -modulus -in CSR.csr | openssl md5 ` |

Para converter certificados e chaves em formatos diferentes para torná-los compatíveis com tipos específicos de servidores, ou software, use estes comandos;

| Descrição | Comando da CLI |
|--|--|
| Converter um arquivo DER (. CRT. cer. der) em PEM  | `openssl x509 -inform der -in certificate.cer -out certificate.pem`  |
| Converter um arquivo PEM em DER | `openssl x509 -outform der -in certificate.pem -out certificate.der`  |
| Converter um arquivo PKCS # 12 (. pfx. p12) que contém uma chave privada e certificados para PEM | `openssl pkcs12 -in keyStore.pfx -out keyStore.pem -nodes` <br />Você pode adicionar `-nocerts` somente a saída da chave privada ou adicionar `-nokeys` a apenas os certificados de saída. |
| Converter um arquivo de certificado PEM e uma chave privada para PKCS # 12 (. pfx. p12) | `openssl pkcs12 -export -out certificate.pfx -inkey privateKey.key -in certificate.crt -certfile CACert.crt` |

## <a name="connect-a-sensor-to-the-management-console"></a>Conectar um sensor ao console de gerenciamento

Esta seção descreve como garantir a conexão entre o sensor e o console de gerenciamento local. Você precisa fazer isso se estiver trabalhando em uma rede gapped e desejar enviar informações de dispositivo e de alerta para o console de gerenciamento do sensor. Essa conexão também permite que o console de gerenciamento envie por push as configurações do sistema para o sensor e execute outras tarefas de gerenciamento no sensor.

Para se conectar:

1. Entre no console de gerenciamento local.

2. Selecione **configurações do sistema**.

3. Na seção **configuração do sensor – cadeia de conexão** , copie a cadeia de conexão gerada automaticamente.

   :::image type="content" source="media/how-to-manage-individual-sensors/connection-string-screen.png" alt-text="Copie a cadeia de conexão desta tela."::: 

4. Entre no console do sensor.

5. No painel esquerdo, selecione **configurações do sistema**.

6. Selecione **conexão do console de gerenciamento**.

    :::image type="content" source="media/how-to-manage-individual-sensors/management-console-connection-screen.png" alt-text="Captura de tela da caixa de diálogo conexão do console de gerenciamento.":::

7. Cole a cadeia de conexão na caixa **cadeia de conexão** e selecione **conectar**.

8. No console de gerenciamento local, na janela Gerenciamento de **site** , atribua o sensor a uma zona.

## <a name="change-the-name-of-a-sensor"></a>Alterar o nome de um sensor

Você pode alterar o nome do console do sensor. O novo nome aparecerá no navegador da Web do console, em várias janelas de console e em logs de solução de problemas.

O processo de alteração de nomes de sensor varia para sensores conectados localmente e sensores conectados à nuvem. O nome padrão é **sensor**.

### <a name="change-the-name-of-a-locally-connected-sensor"></a>Alterar o nome de um sensor conectado localmente

Para alterar o nome:

1. Na parte inferior do painel esquerdo do console, selecione o rótulo do sensor atual.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/label-name.png" alt-text="Captura de tela que mostra o rótulo do sensor.":::

1. Na caixa de diálogo **Editar nome do sensor** , insira um nome.

1. Selecione **Salvar**. O novo nome é aplicado.

### <a name="change-the-name-of-a-cloud-connected-sensor"></a>Alterar o nome de um sensor conectado à nuvem

Se o sensor tiver sido registrado como um sensor conectado à nuvem, o nome do sensor será definido pelo nome atribuído durante o registro. O nome é incluído no arquivo de ativação que você carregou ao entrar pela primeira vez. Para alterar o nome do sensor, você precisa carregar um novo arquivo de ativação.

Para alterar o nome:

1. No portal do Azure defender para IoT, vá para a página de **Gerenciamento do sensor** .

1. Exclua o sensor da janela de **Gerenciamento do sensor** .

1. Registre novamente com o novo nome.

1. Baixe o novo arquivo de ativação.

1. Entre no sensor e carregue o novo arquivo de ativação.

## <a name="update-the-sensor-network-configuration"></a>Atualizar a configuração de rede do sensor

A configuração de rede do sensor foi definida durante a instalação do sensor. Você pode alterar parâmetros de configuração. Você também pode definir uma configuração de proxy.

Se você criar um novo endereço IP, talvez seja necessário entrar novamente.

Para alterar a configuração:

1. No menu lateral, selecione **configurações do sistema**.

2. Na janela **configurações do sistema** , selecione **rede**.

    :::image type="content" source="media/how-to-manage-individual-sensors/edit-network-configuration-screen.png" alt-text="Defina as configurações de rede.":::

3. Defina os parâmetros da seguinte maneira:

    | Parâmetro | Descrição |
    |--|--|
    | Endereço IP | O endereço IP do sensor |
    | Máscara de sub-rede | O endereço da máscara |
    | Gateway padrão | O endereço de gateway padrão |
    | DNS | O endereço do servidor DNS |
    | Nome do host | O nome do host do sensor |
    | Proxy | Host de proxy e nome da porta |

4. Selecione **Salvar**.

## <a name="synchronize-time-zones-on-the-sensor"></a>Sincronizar fusos horários no sensor

Você pode configurar a hora e a região do sensor para que todos os usuários vejam a mesma hora e região.

:::image type="content" source="media/how-to-manage-individual-sensors/time-and-region.png" alt-text="Configure a hora e a região.":::

| Parâmetro | Descrição |
|--|--|
| Fuso horário | A definição de fuso horário para:<br />-Alertas<br />-Widgets tendências e estatísticas<br />-Relatórios de mineração de dados<br />   -Relatórios de avaliação de risco<br />-Vetores de ataque |
| Formato de data | Selecione uma das seguintes opções de formato:<br />-DD/MM/aaaa HH: mm: SS<br />-MM/dd/aaaa HH: mm: SS<br />-aaaa/MM/DD HH: mm: SS |
| Data e Hora | Exibe a data atual e a hora local no formato que você selecionou.<br />Por exemplo, se seu local real é América e Nova York, mas o fuso horário é definido como Europa e Berlim, o horário é exibido de acordo com a hora local de Berlim. |

Para configurar o tempo do sensor:

1. No menu lateral, selecione **configurações do sistema**.

2. Na janela **configurações do sistema** , selecione **hora & regional**.

3. Defina os parâmetros e selecione **salvar**.

## <a name="set-up-backup-and-restore-files"></a>Configurar backup e restaurar arquivos

O backup do sistema é executado automaticamente às 3:00 A.M. diariamente. Os dados são salvos em um disco diferente no sensor. O local padrão é `/var/cyberx/backups`.

Você pode transferir automaticamente esse arquivo para a rede interna.

> [!NOTE]
> - O procedimento de backup e restauração pode ser executado somente entre as mesmas versões.
> - Em algumas arquiteturas, o backup é desabilitado. Você pode habilitá-lo no `/var/cyberx/properties/backup.properties` arquivo.

Ao controlar um sensor usando o console de gerenciamento local, você pode usar o agendamento de backup do sensor para coletar esses backups e armazená-los no console de gerenciamento ou em um servidor de backup externo.

**O que é feito backup:** Configurações e dados.

**O que não é feito backup:** PCAP arquivos e logs. Você pode fazer backup e restaurar o PCAPs e os logs manualmente.

Os arquivos de backup do sensor são nomeados automaticamente por meio do seguinte formato: `<sensor name>-backup-version-<version>-<date>.tar` . Um exemplo é `Sensor_1-backup-version-2.6.0.102-2019-06-24_09:24:55.tar`.

Para configurar o backup:

- Entre em uma conta administrativa e digite `$ sudo cyberx-xsense-system-backup` .

Para restaurar o arquivo de backup mais recente:

- Entre em uma conta administrativa e digite `$ sudo cyberx-xsense-system-restore` .

Para salvar o backup em um servidor SMB Externo:

1. Crie uma pasta compartilhada no servidor SMB Externo.

    Obtenha o caminho da pasta, o nome de usuário e a senha necessários para acessar o servidor SMB.

2. No sensor, crie um diretório para os backups:

    - `sudo mkdir /<backup_folder_name_on_cyberx_server>`

    - `sudo chmod 777 /<backup_folder_name_on_cyberx_server>/`

3. Edite `fstab`: 

    - `sudo nano /etc/fstab`

    - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_cyberx_server> cifsrw,credentials=/etc/samba/user,vers=X.X,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0`

4. Edite e crie credenciais para compartilhar para o servidor SMB:

    `sudo nano /etc/samba/user` 

5. Adicionar:

    - `username=&gt:user name&lt:`

    - `password=<password>`

6. Monte o diretório:

    `sudo mount -a`

7. Configure um diretório de backup para a pasta compartilhada no sensor do defender para IoT:  

    - `sudo nano /var/cyberx/properties/backup.properties`

    - `set backup_directory_path to <backup_folder_name_on_cyberx_server>`

### <a name="restore-sensors"></a>Os sensores de restauração

Você pode restaurar backups do console do sensor e usando a CLI.

**Para restaurar do console do:**

- Selecione **Restaurar imagem** na janela de **configurações do sistema** do sensor.

:::image type="content" source="media/how-to-manage-individual-sensors/restore-image-screen.png" alt-text="Restaure a imagem selecionando o botão.":::

O console exibirá falhas de restauração.

**Para restaurar usando a CLI:**

- Entre em uma conta administrativa e digite `$ sudo cyberx-management-system-restore` .

## <a name="update-a-standalone-sensor-version"></a>Atualizar uma versão do sensor autônomo

O procedimento a seguir descreve como atualizar um sensor autônomo usando o console do sensor. O processo de atualização leva cerca de 30 minutos.

1. Acesse o [portal do Azure](https://portal.azure.com/).

2. Vá para defender para IoT.

3. Vá para a página **atualizações** .

   :::image type="content" source="media/how-to-manage-individual-sensors/updates-page.png" alt-text="Captura de tela da página atualizações do defender para IoT.":::

4. Selecione **baixar** na seção **sensores** e salve o arquivo.

5. Na barra lateral do console do sensor, selecione **configurações do sistema**.

6. No painel **atualização de versão** , selecione **Atualizar**.

    :::image type="content" source="media/how-to-manage-individual-sensors/upgrade-pane-v2.png" alt-text="Captura de tela do painel de atualização.":::

7. Selecione o arquivo que você baixou da página de **atualizações** do defender for IOT.

8. O processo de atualização é iniciado, durante o qual o sistema é reinicializado duas vezes. Após a primeira reinicialização (antes da conclusão do processo de atualização), o sistema é aberto com a janela de entrada. Depois de entrar, a versão de atualização aparece no canto inferior esquerdo da barra lateral.

    :::image type="content" source="media/how-to-manage-individual-sensors/defender-for-iot-version.png" alt-text="Captura de tela da versão de atualização que aparece depois que você entra.":::

## <a name="forward-sensor-failure-alerts"></a>Alertas de falha do sensor de encaminhamento 

Você pode encaminhar alertas para terceiros para fornecer detalhes sobre:

- Sensores desconectados

- Falhas de backup remoto

Essas informações são enviadas quando você cria uma regra de encaminhamento para notificações do sistema.

> [!NOTE]
> Os administradores podem enviar notificações do sistema.

Para enviar notificações:

1. Entre no console de gerenciamento local.
1. Selecione **encaminhamento** no menu lateral.
1. Criar uma regra de encaminhamento.
1. Selecione **notificações do sistema de relatório**.

Para obter mais informações sobre regras de encaminhamento, consulte [encaminhar informações de alerta](how-to-forward-alert-information-to-partners.md).

## <a name="adjust-system-properties"></a>Ajustar propriedades do sistema

As propriedades do sistema controlam várias operações e configurações no sensor. Editar ou modificá-los pode danificar a operação do console do sensor.

Consulte [suporte da Microsoft](https://support.microsoft.com/) antes de alterar suas configurações.

Para acessar as propriedades do sistema:

1. Entre no console de gerenciamento local ou no sensor.

2. Selecione **configurações do sistema**.

3. Selecione **Propriedades do sistema** na seção **geral** .

## <a name="see-also"></a>Consulte também

[Pesquisa e pacotes de inteligência contra ameaças](how-to-work-with-threat-intelligence-packages.md)

[Gerenciar sensores do console de gerenciamento](how-to-manage-sensors-from-the-on-premises-management-console.md)
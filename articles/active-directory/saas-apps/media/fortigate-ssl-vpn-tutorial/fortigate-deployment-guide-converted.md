---
title: Guia de implantação do FortiGate | Microsoft Docs
description: Configure e trabalhe com o produto de firewall de próxima geração Fortinet FortiGate.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: jeedes
ms.openlocfilehash: cdaa6a9601452100ab90ef8b0f2191002f256b74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95025408"
---
# <a name="fortigate-azure-virtual-machine-deployment-guide"></a>Guia de Implantação da Máquina Virtual do Azure do FortiGate

Usando este guia de implantação, você aprenderá a configurar e trabalhar com o produto de firewall de próxima geração FortiGate da Fortinet implantado como uma Máquina Virtual do Azure. Além disso, você vai configurar o Aplicativo de Galeria do Azure AD da VPN SSL do FortiGate para fornecer autenticação de VPN por meio do Azure Active Directory.

## <a name="redeem-the-fortigate-license"></a>Resgatar a Licença do FortiGate

O produto de firewall de próxima geração Fortinet FortiGate está disponível como uma máquina virtual na IaaS (infraestrutura como serviço) do Azure. Há dois modos de licenciamento para essa máquina virtual: pago conforme o uso e BYOL (traga sua própria licença).

Se você comprou uma licença do FortiGate da Fortinet para usar com a opção de implantação de máquina virtual BYOL, resgate-a da página de ativação do produto da Fortinet – https://support.fortinet.com. O arquivo de licença resultante terá uma extensão de arquivo .lic.

## <a name="download-firmware"></a>Baixar o firmware

No momento da elaboração deste artigo, a VM do Fortinet FortiGate no Azure não era fornecida com a versão do firmware necessária para a autenticação SAML. A última versão precisa ser obtida com a Fortinet.

1. Entre em https://support.fortinet.com/.
2. Vá para **Download** > **Imagens de firmware**.
3. À direita das **Notas sobre a versão**, selecione **Download**.
4. Selecione **v6.00** > **6.4** > **6.4.2**.
5. Baixe **FGT_VM64_AZURE-v6-build1723-FORTINET.out** selecionando o link **HTTPS** na mesma linha.
6. Salve o arquivo para uso posterior.

## <a name="deploy-the-fortigate-vm"></a>Implantar a VM do FortiGate

1. Acesse o portal do Azure e entre na assinatura na qual você implantará a máquina virtual do FortiGate.
2. Crie um grupo de recursos ou abra o grupo de recursos no qual você implantará a máquina virtual do FortiGate.
3. Selecione **Adicionar**.
4. Em **Pesquisar no Marketplace**, digite *Forti*. Selecione **Fortinet FortiGate Next-Generation Firewall**.
5. Selecione o plano de software (traga sua própria licença se você tiver uma licença, caso contrário, pago conforme o uso). Selecione **Criar**.
6. Preencha a configuração da VM.

    ![Captura de tela de Criar uma máquina virtual.](virtual-machine.png)

7. Defina o **Tipo de autenticação** como **Senha** e forneça credenciais administrativas para a VM.
8. Selecione **Examinar + Criar** > **Criar**.
9. Aguarde até a implantação da VM ser concluída.


### <a name="set-a-static-public-ip-address-and-assign-a-fully-qualified-domain-name"></a>Definir um endereço IP público estático e atribuir um nome de domínio totalmente qualificado

Para uma experiência de usuário consistente, defina o endereço IP público atribuído à VM do FortiGate para que ele seja atribuído estaticamente. Além disso, mapeie-o para um FQDN (nome de domínio totalmente qualificado).

1. Acesse o portal do Azure e abra as configurações da VM do FortiGate.
2. Na tela **Visão Geral**, selecione o endereço IP público.

    ![Captura de tela da VPN SSL do FortiGate.](public-ip-address.png)

3. Selecione **Estático** > **Salvar**.

Se você tiver um nome de domínio publicamente roteável para o ambiente no qual a VM do FortiGate está sendo implantada, crie um registro de host (A) para a VM. Esse registro é mapeado para o endereço IP público anterior atribuído estaticamente.

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port-8443"></a>Criar uma regra de grupo de segurança de rede de entrada para a porta TCP 8443

1. Acesse o portal do Azure e abra as configurações da VM do FortiGate.
2. No menu à esquerda, selecione **Rede**. O adaptador de rede é listado e as regras de porta de entrada são exibidas.
3. Selecione **Adicionar regra de porta de entrada**.
4. Crie uma regra de porta de entrada para TCP 8443.

    ![Captura de tela de Adicionar regra de segurança de entrada.](port-rule.png)

5. Selecione **Adicionar**.

## <a name="create-a-second-virtual-nic-for-the-vm"></a>Criar uma segunda NIC virtual para a VM

Para que os recursos internos sejam disponibilizados para os usuários, uma segunda NIC virtual precisa ser adicionada à VM do FortiGate. A rede virtual no Azure em que a NIC virtual reside precisa ter uma conexão roteável com esses recursos internos.

1. Acesse o portal do Azure e abra as configurações da VM do FortiGate.
2. Se ainda não houver suporte à VM do FortiGate, selecione **Interromper** e aguarde o desligamento da VM.
3. No menu à esquerda, selecione **Rede**.
4. Selecione **Anexar o adaptador de rede**.
5. Selecione **Criar e anexar adaptador de rede**.
6. Configure as propriedades para o novo adaptador de rede e selecione **Criar**.

    ![Captura de tela da opção Criar adaptador de rede.](new-network-interface.png)

7. Inicie a VM do FortiGate.


## <a name="configure-the-fortigate-vm"></a>Configurar a VM do FortiGate

As seções a seguir explicam como configurar a VM do FortiGate.

### <a name="install-the-license"></a>Instalar a licença

1. Acesse `https://<address>`. Aqui, `<address>` é o FQDN ou o endereço IP público atribuído à VM do FortiGate.

2. Prossiga após qualquer erro de certificado.
3. Conecte-se usando as credenciais de administrador fornecidas durante a implantação da VM do FortiGate.
4. Se a implantação usar o modelo traga sua própria licença, você verá um prompt para carregar uma licença. Selecione o arquivo de licença criado anteriormente e carregue-o. Selecione **OK** e reinicie a VM do FortiGate.

    ![Captura de tela da licença da VM do FortiGate.](license.png)

5. Após a reinicialização, conecte-se novamente com as credenciais de administrador para validar a licença.

### <a name="update-firmware"></a>Atualizar firmware

1. Acesse `https://<address>`. Aqui, `<address>` é o FQDN ou o endereço IP público atribuído à VM do FortiGate.

2. Prossiga após qualquer erro de certificado.
3. Conecte-se usando as credenciais de administrador fornecidas durante a implantação da VM do FortiGate.
4. No menu esquerdo, selecione **Sistema** > **Firmware**.
5. Em **Gerenciamento de Firmware**, selecione **Procurar** e selecione o arquivo de firmware baixado anteriormente.
6. Ignore o aviso e selecione **Configuração de backup e atualização**.

    ![Captura de tela do Gerenciamento de Firmware.](backup-configure-upgrade.png)

7. Selecione **Continuar**.
8. Quando precisar salvar a configuração do FortiGate (como um arquivo .conf), selecione **Salvar**.
9. Aguarde que o firmware seja carregado e aplicado. Aguarde a reinicialização da VM do FortiGate.
10. Após a reinicialização da VM do FortiGate, conecte-se novamente com as credenciais de administrador.
11. Quando for solicitado que você configure o painel, selecione **Mais tarde**.
12. Quando o vídeo do tutorial for iniciado, selecione **OK**.

### <a name="change-the-management-port-to-tcp-8443"></a>Alterar a porta de gerenciamento para TCP 8443

1. Acesse `https://<address>`. Aqui, `<address>` é o FQDN ou o endereço IP público atribuído à VM do FortiGate.

2. Prossiga após qualquer erro de certificado.
3. Conecte-se usando as credenciais de administrador fornecidas durante a implantação da VM do FortiGate.
4. No menu à esquerda, selecione **Sistema**.
5. Em **Configurações de Administração**, altere a porta HTTPS para **8443** e selecione **Aplicar**.
6. Depois que a alteração for aplicada, o navegador tentará recarregar a página de administração, mas falhará. De agora em diante, o endereço da página de administração é `https://<address>:8443`.

    ![Captura de tela de Carregar Certificado Remoto.](certificate.png)

### <a name="upload-the-azure-ad-saml-signing-certificate"></a>Carregar o certificado de autenticação SAML do Azure AD

1. Acesse `https://<address>:8443`. Aqui, `<address>` é o FQDN ou o endereço IP público atribuído à VM do FortiGate.

2. Prossiga após qualquer erro de certificado.
3. Conecte-se usando as credenciais de administrador fornecidas durante a implantação da VM do FortiGate.
4. No menu à esquerda, selecione **Sistema** > **Certificados**.
5. Selecione **Importar** > **Certificado Remoto**.
6. Procure o certificado baixado da implantação de aplicativo personalizado do FortiGate no locatário do Azure. Selecione-o e escolha **OK**.

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>Carregar e configurar um certificado SSL personalizado

Talvez você queira configurar a VM do FortiGate com seu certificado SSL com suporte para FQDN que está sendo usado. Se você tiver acesso a um certificado SSL empacotado com a chave privada no formato .pfx, ele poderá ser usado para essa finalidade.

1. Acesse `https://<address>:8443`. Aqui, `<address>` é o FQDN ou o endereço IP público atribuído à VM do FortiGate.

2. Prossiga após qualquer erro de certificado.
3. Conecte-se usando as credenciais de administrador fornecidas durante a implantação da VM do FortiGate.
4. No menu à esquerda, selecione **Sistema** > **Certificados**.
5. Selecione **Importar** > **Certificado Local** > **Certificado PKCS #12**.
6. Procure o arquivo .PFX que contém o certificado SSL e a chave privada.
7. Forneça a senha do .PFX e um nome significativo para o certificado. Depois, selecione **OK**.
8. No menu à esquerda, selecione **Sistema** > **Configurações**.
9. Em **Configurações de Administração**, expanda a lista ao lado de **Certificado do servidor HTTPS** e escolha o certificado SSL importado anteriormente.
10. Escolha **Aplicar**.
11. Feche a janela do navegador e vá para `https://<address>:8443`.
12. Entre com as credenciais de administrador do FortiGate. Agora, você deve ver o certificado SSL correto em uso.

### <a name="configure-authentication-timeout"></a>Configurar o tempo limite de autenticação

1. Acesse o portal do Azure e abra as configurações da VM do FortiGate.
2. No menu à esquerda, selecione **Console Serial**.
3. Entre no console serial com as credenciais de administrador da VM do FortiGate.
4. No Console Serial, execute os seguintes comandos:

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```

### <a name="ensure-network-interfaces-are-obtaining-ip-addresses"></a>Verificar se as interfaces de rede estão obtendo endereços IP

1. Acesse `https://<address>:8443`. Aqui, `<address>` é o FQDN ou o endereço IP público atribuído à VM do FortiGate.

2. Conecte-se usando as credenciais de administrador fornecidas durante a implantação da VM do FortiGate.
3. No menu à esquerda, selecione **Rede**.
4. Em Rede, selecione **Interfaces**.
5. Examine port1 (interface externa) e port2 (interface interna) para verificar se eles estão obtendo um endereço IP da sub-rede do Azure correta.
    a. Se uma das portas não estiver obtendo um endereço IP da sub-rede (por meio de DHCP), clique com o botão direito do mouse na porta e selecione **Editar**.
    b. Ao lado de Modo de Endereçamento, verifique se **DHCP** está selecionado.
    c. Selecione **OK**.

    ![Captura de tela da opção Endereçamento do Adaptador de Rede.](addressing.png)

### <a name="ensure-fortigate-vm-has-correct-route-to-on-premises-corporate-resources"></a>Verificar se a VM do FortiGate tem uma rota correta para recursos corporativos locais

As VMs do Azure com hospedagem múltipla têm todos os adaptadores de rede na mesma rede virtual (mas talvez sub-redes separadas). Isso geralmente significa que os dois adaptadores de rede têm uma conexão com os recursos corporativos locais que são publicados por meio do FortiGate. Por esse motivo, é necessário criar entradas de rota personalizadas que assegurem que o tráfego saia do adaptador correto quando são feitas solicitações de recursos corporativos locais.

1. Acesse `https://<address>:8443`. Aqui, `<address>` é o FQDN ou o endereço IP público atribuído à VM do FortiGate.

2. Entre usando as credenciais de administrador fornecidas durante a implantação da VM do FortiGate.
3. No menu à esquerda, selecione **Rede**.
4. Em Rede, selecione **Rotas Estáticas**.
5. Selecione **Criar**.
6. Ao lado de Destino, selecione **Sub-rede**.
7. Em Sub-rede, especifique as informações de sub-rede em que residem os recursos corporativos locais (por exemplo, 10.1.0.0/255.255.255.0)
8. Ao lado de Endereço de Gateway, especifique o gateway na sub-rede do Azure em que o port2 está conectado (por exemplo, ele geralmente termina em 1, como 10.6.1.1)
9. Ao lado de Adaptador, selecione o adaptador de rede interno, port2
10. Selecione **OK**.

    ![Captura de tela de Como Configurar uma Rota.](route.png)

## <a name="configure-fortigate-ssl-vpn"></a>Configurar a VPN SSL do FortiGate

Siga as etapas descritas em https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial

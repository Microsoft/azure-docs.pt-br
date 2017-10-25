#### <a name="to-create-public-endpoints-on-the-virtual-device"></a>Para criar pontos de extremidade públicos no dispositivo virtual

1. Entre no portal clássico do Azure.
2. Clique em **Máquinas Virtuais**e, em seguida, selecione a máquina virtual que está sendo usada como seu dispositivo virtual.
3. Clique em **Pontos de Extremidade**. A página **Pontos de extremidade** lista todos os pontos de extremidade para a máquina virtual.
4. Clique em **Adicionar**. A caixa de diálogo **Adicionar coadministrador** é exibida. Clique na seta para continuar.
5. Para o **Nome**, digite o seguinte nome para o ponto de extremidade: **WinRMHttps**.
6. Para o **Protocolo**, especifique **TCP**.
7. Para a **Porta Pública**, digite os números de porta que você deseja usar para a conexão.
8. Para a **Porta Privada**, digite **5986**.
9. Clique na marca de seleção para salvar o ponto de extremidade.

Após a criação do ponto de extremidade, você poderá exibir seus detalhes para determinar o endereço IP Virtual Público (VIP). Registre esse endereço.


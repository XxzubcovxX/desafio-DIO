

# Meu Roteiro: Criando uma Máquina Virtual (VM) no Azure

Hoje eu vou criar uma nova máquina virtual do zero no portal do Azure. Este é o meu guia pessoal, passo a passo, de como eu faço isso.

---

## 1. O Ponto de Partida: Portal do Azure

Tudo começa aqui.

1.  Eu abro meu navegador e vou para `https://portal.azure.com`.
2.  Faço login com minha conta.
3.  Na barra de pesquisa superior, eu digito "Máquinas Virtuais" e clico no serviço **Máquinas Virtuais** que aparece.
4.  Na página de Máquinas Virtuais, eu clico no botão **+ Criar** e seleciono **Máquina virtual do Azure**.

Isso me leva para o assistente de criação.

## 2. Aba "Básicos": O Essencial

Esta é a aba mais importante. Eu preencho tudo com cuidado.

* **Assinatura:** Eu me certifico de que a assinatura correta está selecionada (normalmente já está).
* **Grupo de Recursos:** Eu preciso de um "contêiner" para organizar todos os recursos da minha VM.
    * Eu clico em **Criar novo**.
    * Dou um nome, algo como `meu-rg-vm-producao`.
* **Nome da máquina virtual:** Um nome único para minha VM. Vou chamá-la de `MinhaVM-Web-01`.
* **Região:** Eu escolho uma região que esteja perto dos meus usuários ou de mim. Para este teste, vou escolher **(Brazil) South** (Sul do Brasil).
* **Opções de disponibilidade:** Para um simples teste, eu deixo como **Nenhuma redundância de infraestrutura necessária**.
* **Imagem (O "Sistema Operacional"):** Aqui eu decido o que vai rodar na VM.
    * Para este exemplo, vou escolher **Ubuntu Server 20.04 LTS - Gen 2**. (Se eu quisesse Windows, eu selecionaria "Windows Server 2022 Datacenter" aqui).
* **Tamanho:** O "hardware" (CPU e RAM).
    * Eu clico em **Ver todos os tamanhos**.
    * Para economizar, eu procuro pela série "B" (B-series), que é boa para testes. Vou selecionar o tamanho **Standard_B1s** (1 vCPU, 1 GiB de memória).
* **Conta de administrador:** Como eu vou fazer login.
    * **Tipo de autenticação:** Eu prefiro usar **Chave pública SSH** por ser mais seguro que senha.
    * **Nome de usuário:** Vou usar `azureuser`.
    * **Origem da chave pública SSH:** Eu seleciono **Gerar novo par de chaves**.
    * **Nome do par de chaves:** Vou chamar de `minhavm-key`.
* **Regras de porta de entrada:** Isso é CRUCIAL. Como eu vou acessar a VM?
    * Eu marco **Permitir portas selecionadas**.
    * Como escolhi uma imagem Linux (Ubuntu), eu **DEVO** selecionar **SSH (22)**. (Se eu tivesse escolhido Windows, eu selecionaria **RDP (3389)**).

Com a aba "Básicos" preenchida, eu clico em **Avançar: Discos >**.

## 3. Aba "Discos"

Aqui eu configuro o armazenamento.

* **Tipo de disco do SO:** O padrão é **SSD Premium**, que é rápido. Para economizar em testes, às vezes eu mudo para **SSD Standard**, mas hoje vou deixar o Premium.
* **Discos de dados:** Eu não preciso de um disco extra agora, então vou pular isso.

Eu clico em **Avançar: Rede >**.

## 4. Aba "Rede"

Aqui eu configuro como minha VM se conecta à internet e a outros serviços.

* **Rede Virtual:** O Azure sugere criar uma nova rede para mim (baseada no nome do meu grupo de recursos, como `meu-rg-vm-producao-vnet`). Eu aceito o padrão.
* **Sub-rede:** O Azure também cria uma sub-rede `default`. Eu aceito isso.
* **IP Público:** O Azure cria um **(novo) IP público** para mim. Eu preciso disso para acessar a VM pela internet.
* **Grupo de Segurança de Rede da NIC:** Eu deixo em **Básico**.
* **Portas de entrada públicas:** Eu confirmo que a regra que criei na aba "Básicos" (permitir SSH na porta 22) está listada aqui.

Para um setup simples, os padrões aqui são geralmente bons. Eu clico em **Avançar: Gerenciamento >**.

## 5. Abas "Gerenciamento", "Monitoramento" e "Avançado"

Para uma VM de teste, eu geralmente pulo essas abas ou apenas dou uma olhada rápida.

* **Gerenciamento:** Eu costumo desabilitar o **diagnóstico de inicialização** para economizar custos de armazenamento, mas hoje vou deixar o padrão.
* **Monitoramento:** Eu deixo os padrões.
* **Avançado:** Não preciso de extensões ou `cloud-init` agora.

Eu clico em **Avançar: Marcas >**.

* **Marcas (Tags):** É uma boa prática usar tags. Eu vou adicionar uma:
    * `Nome`: `Ambiente`
    * `Valor`: `Teste`

Finalmente, eu clico em **Avançar: Revisar + criar >**.

## 6. Revisar e Criar (A Hora da Verdade)

1.  O Azure me mostra um resumo de tudo que eu configurei. Eu dou uma última olhada para garantir que o **Tamanho**, a **Imagem** e as **Portas de Entrada** estão corretos.
2.  Eu vejo uma mensagem verde no topo dizendo **Validação aprovada**.
3.  Eu clico no botão **Criar**.

**IMPORTANTE:** Como eu pedi para o Azure "Gerar novo par de chaves", um pop-up aparece.

4.  Eu clico em **Baixar chave privada e criar recurso**.
5.  Eu salvo o arquivo `.pem` (ex: `minhavm-key.pem`) em um local seguro no meu computador (como na minha pasta `C:\Users\MeuNome\.ssh\`). Eu **NÃO POSSO** perder esse arquivo.

## 7. A Implantação (A Espera)

Agora eu espero. A página "A implantação está em andamento" aparece. Isso geralmente leva de 2 a 5 minutos. Eu pego um café.

Quando termina, eu vejo a mensagem "Sua implantação está concluída".

Eu clico em **Ir para o recurso**.

## 8. Conectando na Minha Nova VM

Estou na página de visão geral da minha `MinhaVM-Web-01`.

1.  Eu localizo o **Endereço de IP público** e clico no ícone para copiá-lo (algo como `20.50.100.150`).
2.  Agora, eu abro meu terminal (pode ser o PowerShell, o Terminal do Windows ou o CMD no Windows 10/11, ou o Terminal no Mac/Linux).

3.  Primeiro, eu preciso garantir que minha chave `.pem` tenha as permissões corretas (em Linux/Mac). No Windows, às vezes eu pulo isso, mas a boa prática é:
    *(Se eu estiver no Windows, eu navegaria até a pasta e talvez precisasse ajustar as permissões do arquivo .pem nas propriedades de segurança para que apenas meu usuário tenha acesso. No Linux/Mac, eu faria `chmod 400 ~/.ssh/minhavm-key.pem`)*

4.  Eu uso o comando SSH para conectar. A sintaxe é:
    `ssh -i <caminho_para_minha_chave> <meu_usuario>@<meu_ip_publico>`

5.  No meu terminal, eu digito (ajustando o caminho para onde eu salvei a chave):

    ```bash
    ssh -i "C:\Users\MeuNome\.ssh\minhavm-key.pem" azureuser@20.50.100.150
    ```

6.  O terminal vai perguntar: "Are you sure you want to continue connecting (yes/no/[fingerprint])?".
7.  Eu digito `yes` e aperto Enter.
8.  Pronto! Meu prompt mudou para `azureuser@MinhaVM-Web-01:~$`.

Eu consegui! Estou dentro da minha máquina virtual Ubuntu rodando na nuvem do Azure.



O Gemini pode cometer erros. Por isso, é bom checar as respostas.


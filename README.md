# 🚀 Sniper Rocket

Bot para Windows que **vigia perfis do X (Twitter), sites, canais do Discord e carteiras de criadores** e, assim que aparece o endereço (contrato/mint) de um token Solana — ou uma carteira vigiada cria um token —, **compra automaticamente** com a sua carteira.

> **Baixe o instalador em [Releases](https://github.com/sniperocket/SNIPERROCKET/releases/latest)** → arquivo `SniperRocket_Setup_X.Y.Z.exe`.

---

## ⚠️ Avisos importantes (leia antes de usar)

- **Criptomoedas são de altíssimo risco.** Tokens novos podem ir a zero em segundos, ser golpes (rug pull) ou não ter liquidez para vender. Você pode **perder todo o valor** usado.
- O bot compra **sozinho**, sem pedir confirmação. Configure com calma e teste com valores pequenos.
- **Use uma carteira NOVA e dedicada**, só para o bot, com pouco saldo. Nunca use a carteira principal.
- **Nunca compartilhe sua private key nem a pasta `config`** (ela guarda a chave em texto puro, além dos seus tokens de API). Ninguém do suporte vai pedir isso. Quem tem a chave tem o dinheiro.
- Este software é fornecido "como está", sem garantia de lucro ou de funcionamento. O uso é por sua conta e risco.

---

## O que o bot faz

| Função | Como funciona |
|---|---|
| **Monitor do X (Twitter)** | Acompanha em tempo real os perfis que você cadastrar. Quando um deles posta um contrato Solana, o bot compra. |
| **Monitor de sites** | Verifica sites de tempos em tempos (ex.: a cada 1,5 s) e compra quando um contrato novo aparece na página. |
| **Monitor de tela (Discord)** | Lê com o OCR do próprio Windows a janela do Discord (ou a tela inteira). Compra só contratos **novos** (os que já estavam na tela ao iniciar são ignorados), uma vez cada, depois de confirmar que o endereço existe na blockchain. |
| **SNIPER WALLET** | Vigia carteiras Solana que você cadastrar e compra na hora **qualquer token que elas criarem** (pump.fun, Bonk/LaunchLab, Meteora, SPL/Token-2022…). Tempo real por websocket, com consulta a cada 1 s se o websocket cair. |
| **Uma compra por vez** | A primeira detecção (de qualquer monitor) compra e todos os monitores param — nunca compra duas vezes no mesmo monitoramento. |
| **Compra automática** | Compra pelo **Jupiter** (melhor rota). Se o Jupiter falhar (token ainda não indexado, erro 503…), usa a **PumpPortal** como reserva. |
| **Rota "pump"** | Opcional: se o contrato termina em `pump` (tokens da pump.fun), compra primeiro pela PumpPortal e usa o Jupiter como reserva. |
| **Velocidade** | *Priority fee* configurável, **Jito** (gorjeta/tip para entrar mais rápido no bloco) e **envio duplo**: a mesma transação vai pelo seu RPC e pelo Jito ao mesmo tempo. |
| **Auto Sell** | Vende automaticamente uma porcentagem dos tokens quando o lucro alvo é atingido (ex.: 100% = preço dobrou). |
| **Venda manual** | Durante o monitoramento, aperte **V** para vender uma parte (%) ou tudo de uma posição aberta, sem parar o bot. |
| **Verificação secundária** | Opcional: confere **nome/símbolo** (palavras suas), **CRIAÇÃO MÁXIMA** (idade do token) e **MCAP MÁXIMO** (market cap em USD), em paralelo com a compra. |
| **Atualização automática** | Ao abrir, o bot verifica se existe versão nova e se atualiza sozinho, mantendo suas configurações. |
| **Assinatura** | **3 dias grátis** no primeiro uso; depois **30 USDC por 30 dias**, pago dentro do próprio bot (veja [Assinatura](#-assinatura)). |

---

## 🛡️ Verificação secundária (v1.4.2) — opção 10: nome, CRIAÇÃO MÁXIMA e MCAP MÁXIMO (Solana e Robinhood)

A opção **10) Verificação secundária** agora reúne os três filtros, cada um com seu próprio ON/OFF (a tela mostra a rede
ativa; idade e MCAP são separados por rede). Todos vêm **OFF**; valores já salvos são mantidos. **ESPAÇO + Enter = voltar.**

| Item | O que faz | Vale para |
|---|---|---|
| **1–5) Verificação de nome** | Só compra se o nome/símbolo tiver uma palavra cadastrada (lista vazia = não bloqueia). | todos os snipers |
| **6) CRIAÇÃO MÁXIMA** + **7) Idade máxima** | Não compra token criado há mais que o limite (ex.: `45s`, `90`, `1m`, `2 min`; número sem unidade = segundos). | X, site, tela (**SNIPER WALLET não usa**) |
| **8) MCAP MÁXIMO** + **9) MCAP máximo (USD)** | Não compra se o market cap no momento da compra passar do limite (ex.: `50000`, `50k`, `1,5m`). | **todos**, inclusive SNIPER WALLET |

- **Não atrasa a compra:** nome, idade e MCAP são consultados **ao mesmo tempo** que a cotação/montagem/assinatura; o envio
  só espera o que ainda não chegou. A trava de "1 compra por sessão" só é reservada depois que o nome passa — um token
  reprovado não bloqueia outro que chegue ao mesmo tempo.
- **MCAP:** preço tirado da própria compra (tokens recebidos pela cotação usada na tx, ou reservas da curva pump.fun) × supply
  total (1 leitura, em cache). Preço SOL/ETH em USD atualizado em segundo plano a cada ~60 s (Coinbase/Binance/Kraken, sem chave);
  sem preço atualizado há mais de 5 min = não compra. O valor usa o preço da sua compra (inclui o impacto dela), então fica
  levemente acima do MCAP "à vista" — margem a favor da segurança.
- **Falhou ou demorou (> 4 s)? Não compra** e nada é enviado; o motivo aparece no painel Eventos, ex.:
  `[SOL] [mcap] … MCAP $7.5k (limite $50.0k) — ok` / `[RH] [idade] … criado há mais de 1.0 min — NÃO comprado (CRIAÇÃO MÁXIMA)`.

## 🟢 Robinhood Chain (v1.4.0) — opção 13) MUDAR REDE

Na tela inicial, a coluna da direita tem **13) MUDAR REDE**: escolha **1) Solana** ou **2) Robinhood Chain**. O bot inteiro passa a
usar a rede escolhida (fica salva). Cada rede tem a **sua própria** private key, RPC, valor de compra, slippage, gas e carteiras
vigiadas — trocar de rede **nunca apaga nem altera** as configurações da outra. O cabeçalho mostra a rede ativa e toda linha de
compra/venda no painel Eventos começa com `[SOL]` ou `[RH]`.

Na Robinhood Chain (EVM, chain ID 4663, gas pago em **ETH**):
- **6) Cadastrar private key**: chave EVM (64 caracteres hex, com ou sem `0x`) de uma carteira **nova e dedicada** (ex.: MetaMask/Rabby → Exportar chave privada).
- **7) Saldo**: em ETH. **Valor por compra**: em ETH.
- **8) Configurações**: slippage, **max fee em gwei** (aceita decimal, ex.: `0.05`; `0` = automático pela base fee, hoje ~0.02 gwei),
  priority fee (gwei, normalmente 0), **teto rígido** do max fee, gas limit, RPC (público `https://rpc.mainnet.chain.robinhood.com`,
  ou Alchemy `https://robinhood-mainnet.g.alchemy.com/v2/SUA_CHAVE`), RPC WSS (`wss://robinhood-mainnet.g.alchemy.com/v2/SUA_CHAVE`),
  sequenciador, rota de compra, checagem honeypot, pré-aprovação para venda. Não existem Jito, priority fee em SOL, Jupiter nem PumpPortal nesta rede.
- **Snipers X / site / tela**: procuram contratos no formato **`0x` + 40 caracteres hex** (o OCR corrige O→0 e I/l→1) e confirmam on-chain que é um token.
- **SNIPER WALLET**: cadastre carteiras EVM (`0x…`). Quando uma delas criar um token (deploy direto, Pons V1/V2, launchpads), o bot compra na hora.
  Com RPC WSS configurado, usa assinaturas `newHeads`/`logs`; sem WSS, varre os blocos a cada 250 ms.
- **Rotas**: `direct` (padrão) = curva da Pons V2 enquanto o token está na curva, Uniswap V4 quando a Pons graduou, KyberSwap para
  tokens que já têm pool; `kyber` = KyberSwap primeiro; `uniswap` = só Uniswap. Venda: KyberSwap com fallback direto.
- **Velocidade**: a rede é "primeiro a chegar" (sem leilão de prioridade, por isso não há Jito). O bot assina localmente, deixa nonce,
  base fee e conexões prontos e envia a mesma transação ao **sequenciador** e ao **RPC** ao mesmo tempo. Após a compra, já aprova o roteador para a venda sair rápido.
- **Configurações recomendadas (Robinhood)**: slippage 15–30%, max fee `0` (automático) com teto 1 gwei, RPC + WSS da Alchemy,
  rota `direct`. Na opção **10) Verificação secundária** (com a rede Robinhood ativa): **CRIAÇÃO MÁXIMA ON com 1–2 min** e
  **MCAP MÁXIMO ON** (ex.: `50k`).
- Explorer: https://robinhoodchain.blockscout.com

## 💾 Instalação

1. Abra a página **[Releases](https://github.com/sniperocket/SNIPERROCKET/releases/latest)** e baixe o arquivo **`SniperRocket_Setup_X.Y.Z.exe`** (em "Assets").
2. Dê dois cliques no arquivo baixado.
3. O Windows pode mostrar a tela azul **"O Windows protegeu o computador"** (SmartScreen). Isso acontece com programas novos que não têm assinatura digital paga. Clique em **"Mais informações"** e depois em **"Executar assim mesmo"**.
4. Siga o instalador (**não precisa de administrador**). Deixe marcada a opção **"Criar atalho na Área de Trabalho"**.
5. No fim, marque **"Abrir o Sniper Rocket agora"** — ou use o atalho **Sniper Rocket** na Área de Trabalho / Menu Iniciar.

O programa é instalado em `%LOCALAPPDATA%\SniperRocket` (pasta do seu usuário). Para desinstalar: Menu Iniciar → **Desinstalar Sniper Rocket** (ou Configurações → Aplicativos). **A desinstalação não apaga a pasta `config`** (sua chave e configurações); apague-a manualmente se quiser.

---

## 🔄 Atualizações

- **São automáticas.** Toda vez que você abre o bot, ele verifica se há uma versão nova aqui no GitHub. Se houver, aparece **"Atualizando para vX.Y.Z..."**, o bot baixa o instalador, fecha, instala em segundo plano e **abre de novo sozinho** já na versão nova.
- A verificação acontece **só na abertura**: o bot **nunca se atualiza durante o monitoramento**.
- Se a atualização falhar (sem internet, antivírus bloqueou etc.), o bot apenas registra no log e **continua normalmente** na versão atual.
- Também existe a opção **11) Atualizar** no menu, para verificar/instalar manualmente.
- **Suas configurações são preservadas**: a atualização só troca o `sniper.exe`; a pasta `config` (chave, tokens, perfis, posições, logs) nunca é apagada nem sobrescrita.
- A versão atual aparece no topo da tela (ex.: `Sniper X → Solana v1.2.0`).

---

## 💳 Assinatura

- **Teste grátis de 3 dias**: começa na **primeira vez que o bot é aberto** neste PC (precisa de internet nessa primeira abertura). Reinstalar o bot **não** reinicia o teste.
- **Plano: 30 USDC por 30 dias.** Os dias **se somam**: se você renovar antes de vencer, os 30 dias novos entram depois dos que ainda faltam.
- **Licença por PC**: cada licença vale para **um computador**. Ela não funciona se for copiada para outro PC.
- No dia a dia o bot confere a licença **no próprio computador** (não depende do servidor). Não altere a data/hora do Windows para trás: o bot detecta e mostra **"Relógio do sistema alterado"** (acerte o relógio e escolha "Tentar novamente").

### Como pagar
1. Quando o teste ou o plano vence, aparece a tela **CONTRATE UM PLANO — 30 USDC por 30 dias — COMPRAR 1) SIM 2) NÃO** (2 fecha o bot). Para renovar antes, use o menu **12) Assinatura → 1) Renovar**.
2. Escolha **1) SIM** e informe a **carteira Solana de onde o pagamento vai sair** (a sua Phantom/Solflare etc.) e confirme.
3. Aparece **"VOCÊ TEM 10 MINUTOS PARA PAGAR"** com a carteira de destino e uma contagem regressiva. Envie, **da carteira informada** e numa **única transação**, **30 USDC** (rede **Solana**; token USDC, mint `EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v`) para:
   **`2XpTn1mXAittaMBsmjS43HPWCo3ycAaM5NUt211ov9jb`**
4. O bot verifica sozinho a cada poucos segundos e **libera automaticamente** quando o pagamento é confirmado na blockchain.
5. Pagou mas o tempo acabou? O pagamento é aceito **até 60 minutos** depois de iniciar: use **"Já paguei, verificar"** (na tela de bloqueio ou no menu 12) ou simplesmente abra o bot de novo.

> Envie da carteira que você informou (não de uma corretora), na rede Solana. Pagamentos de outra carteira, de outro token ou abaixo de 30 USDC não são reconhecidos automaticamente.

### Seu ID
O menu **12) Assinatura** mostra o status, a data de vencimento e o **Seu ID** (código curto deste PC, ex.: `A1B2C3D4E5`; também aparece no menu e na tela de pagamento). Informe o **Seu ID** ao suporte sempre que precisar de ajuda com a licença. Se o suporte adicionar dias para você, use **12 → 3) Atualizar licença pelo servidor**.

---

## 🔑 Credenciais — guia passo a passo

Aqui você aprende a conseguir **cada chave/token** que o bot usa e **onde colar** cada um. Faça com calma, na ordem abaixo.

Tudo o que você colar no bot fica salvo **só no seu computador**, em `%LOCALAPPDATA%\SniperRocket\config\config.json`. Nada é enviado para nós.

### Resumo rápido

| # | Credencial | Obrigatória? | Onde conseguir | Onde colar no bot |
|---|---|---|---|---|
| 1 | Private key da carteira Solana | **Sim** | Phantom ou Solflare | Menu **6) Cadastrar private key** |
| 2 | URL do RPC (Helius) | Não, mas **muito recomendada** | [dashboard.helius.dev](https://dashboard.helius.dev) | Menu **8** → item **5) RPC / endpoint** |
| 3 | Jupiter API key | Não, mas **recomendada** | [developers.jup.ag/portal](https://developers.jup.ag/portal) | Menu **8** → item **6) Jupiter API key** |
| 4 | X (Twitter) Bearer Token | Só se usar o monitor do X | [console.x.com](https://console.x.com) | Menu **1) SNIPER X CONFIG** → item **2) Bearer Token** |
| 5 | PumpPortal | **Não precisa de nada** | — | — |

> 🔒 **Regra de ouro:** nunca mande nenhuma dessas chaves para ninguém (nem para o "suporte"), não tire print, não cole em sites nem em grupos. Os exemplos deste guia usam só textos de mentira, como `SUA_CHAVE`.

**Dica para colar no bot:** a janela do bot é um terminal. Para colar, use **Ctrl+V** ou **clique com o botão direito** do mouse dentro da janela e depois aperte **Enter**. Em alguns campos (private key, Jupiter API key, Bearer Token) **o texto não aparece enquanto você cola** — isso é proposital, por segurança. Cole **uma vez só** e aperte Enter.

---

### 1) Private key da carteira Solana

**O que é:** é a "senha mestra" da carteira. Com ela o bot consegue **assinar** (autorizar) as compras e vendas sozinho. Quem tem essa chave **controla todo o dinheiro** da carteira.

**É obrigatória?** **Sim.** Sem ela o bot não compra nem vende.

**Formato:** um texto longo em **base58** (letras maiúsculas, minúsculas e números, normalmente com **87 ou 88 caracteres**), tudo em uma linha, sem espaços. Exemplo de **formato** (não é uma chave de verdade):

```
SUA_PRIVATE_KEY_EM_BASE58_COM_CERCA_DE_88_CARACTERES
```

> ⚠️ A private key **não** é a "frase de recuperação" (as 12 ou 24 palavras). O bot **não aceita** as palavras. Também **não** é o endereço da carteira (o endereço é público e serve só para receber).

#### Passo 0 — Crie uma carteira NOVA só para o bot (muito importante)

Nunca use a sua carteira principal. Crie uma **carteira separada**, coloque nela só o valor que você aceita arriscar e um pouco de **SOL para as taxas**. Se algo der errado, só esse valor fica exposto.

#### Opção A — Phantom ([phantom.com](https://phantom.com))

Criar a conta dedicada:
1. Instale a Phantom (extensão do navegador ou app) **somente pelo site oficial** [phantom.com](https://phantom.com).
2. Se você **ainda não tem** Phantom: crie uma carteira nova usando a opção **com frase de recuperação** (anote as palavras no papel e guarde). **Evite criar com login do Google/Apple**: nas carteiras criadas assim a Phantom **não mostra a private key** (a opção "Mostrar chave privada" não existe nelas).
3. Se você **já tem** Phantom: clique no **ícone do seu perfil** (canto superior esquerdo) → **Adicionar / Conectar conta** → **Criar nova conta**. Dê um nome, ex.: `Bot Sniper`.
4. Selecione essa conta nova e envie para ela o SOL que vai usar no bot.

Exportar a private key:
1. Clique no **ícone do seu perfil** (canto superior esquerdo) → **Configurações** (engrenagem).
2. Clique em **Gerenciar contas** (*Manage Accounts*).
3. Clique na conta do bot (no celular: nos **três pontinhos** ao lado da conta).
4. Clique em **Mostrar chave privada** (*Show Private Key*).
5. Leia o aviso, digite a **senha** da Phantom (ou use a biometria).
6. Escolha a rede **Solana** (a chave de outras redes, como Ethereum, **não serve**).
7. Clique para **copiar** a chave.

#### Opção B — Solflare ([solflare.com](https://www.solflare.com))

1. Instale a Solflare só pelo site oficial e crie uma carteira/conta nova para o bot.
2. Clique no **ícone do perfil / seletor de contas** (canto superior esquerdo) e selecione a conta do bot.
3. Abra o menu de **três pontinhos** (ou **Editar**, no celular) dessa conta → **Exportar chave privada** (*Export Private Key*).
4. Digite a senha e **copie** a chave. (Os nomes exatos dos botões podem mudar um pouco conforme a versão.)

#### Colar no bot

1. No menu principal, digite **`6`** (**Cadastrar private key**) e Enter.
2. Cole a chave (ela **não aparece** na tela — é normal) e aperte Enter.
3. Deve aparecer **"Chave salva."** e o saldo passa a aparecer no canto superior direito. Confira o endereço no menu **7) Saldo** — ele deve ser o mesmo da sua conta na Phantom/Solflare.
4. Depois de colar, **apague a chave da área de transferência** copiando qualquer outro texto.

#### Cuidados de segurança
- A chave fica salva em **texto puro** em `config\config.json`. **Nunca** envie essa pasta/arquivo para ninguém.
- Ninguém do suporte vai pedir sua private key ou frase de recuperação. **Quem pede é golpista.**
- Não guarde a chave em print, e-mail, WhatsApp ou nuvem.
- Deixe na carteira do bot só o necessário. Quando quiser, transfira o lucro para a sua carteira principal.
- Se desconfiar que a chave vazou: crie outra carteira, transfira tudo para ela **imediatamente** e cadastre a chave nova no menu 6.

#### Erros comuns
| Problema | Causa / solução |
|---|---|
| **"Chave inválida"** | Você colou as **12/24 palavras**, o **endereço** da carteira, a chave de outra rede (Ethereum) ou a chave saiu cortada/com espaço. Copie de novo a chave da rede **Solana**. |
| A chave está no formato `[12,34,56,...]` (lista de números) | Esse formato não é aceito. Exporte pela Phantom/Solflare, que mostra o formato base58. |
| "Mostrar chave privada" não aparece na Phantom | A carteira foi criada com login do Google/Apple ou é uma carteira de hardware (Ledger). Crie uma conta nova com frase de recuperação. |
| O saldo aparece **0** | Você exportou a chave de outra conta, ou ainda não enviou SOL para ela. Compare o endereço do menu 7 com o da carteira. |

---

### 2) RPC (Helius — tem plano gratuito)

**O que é:** o RPC é o **"servidor de entrada" da blockchain Solana**. É por ele que o bot **lê o saldo** e **envia as transações** de compra e venda.

**É obrigatório?** **Não.** Sem configurar, o bot usa o RPC público da Solana (`https://api.mainnet-beta.solana.com`), que funciona, mas é **lento e com limite baixo**. Um RPC próprio deixa as compras **mais rápidas e confiáveis** — **recomendado**.

> **RPC × endpoint da Jupiter — qual a diferença?**
> - **RPC (Helius)** = a "estrada" até a blockchain Solana: lê saldo e **envia** a transação. Vai no **menu 8 → item 5**.
> - **Endpoint/rota da Jupiter** = o "corretor" que **calcula o melhor preço** e **monta** a transação de compra. Usa a **Jupiter API key** (seção 3), **menu 8 → item 6**.
> São coisas diferentes: **não cole a URL da Helius no campo da Jupiter**, nem o contrário.

#### Passo a passo
1. Acesse **[dashboard.helius.dev](https://dashboard.helius.dev)** (site: [helius.dev](https://www.helius.dev)).
2. Crie uma conta gratuita (com Google, GitHub ou e-mail). Se perguntar o plano, escolha o **Free** (gratuito, não pede cartão).
3. No painel, se pedir, crie um **projeto** (qualquer nome).
4. No menu lateral, abra **API Keys** (ou **Endpoints / RPCs**). Se não houver chave, clique em **Create New API Key** e dê um nome (ex.: `sniper`).
5. Procure a URL de **Mainnet** (**não** use a de **Devnet**, que é rede de teste) e clique em **copiar**. Ela tem este formato:
   ```
   https://mainnet.helius-rpc.com/?api-key=SUA_CHAVE
   ```
6. **Copie a URL inteira**, começando em `https://` — não só a chave.

#### Colar no bot
1. No menu principal, digite **`8`** (**Configurações**) e Enter.
2. Digite **`5`** (**RPC / endpoint**) e Enter.
3. Cole a URL completa e aperte Enter. Deve aparecer **"RPC / endpoint salvo."**
4. Volte (**0**) e confira se o saldo aparece no topo / no menu **7**.
5. Para voltar ao RPC público a qualquer momento, repita os passos e digite apenas **`-`**.

**Alternativas à Helius** (funcionam igual; basta colar a URL **Mainnet** da Solana que o painel delas fornece no mesmo menu 8 → 5): [QuickNode](https://www.quicknode.com), [Alchemy](https://www.alchemy.com/solana), [Triton](https://triton.one). Use apenas uma URL de **Solana Mainnet** que comece com `https://`.

#### Erros comuns
| Problema | Causa / solução |
|---|---|
| Saldo aparece como **"erro"** depois de colar | URL incompleta (faltou `https://` ou a parte `?api-key=...`), espaço no final, ou colou só a chave. Copie a URL inteira de novo. Teste com `-` (RPC público) para saber se o problema é a URL. |
| **401 / Unauthorized** | Chave errada ou apagada no painel da Helius. Copie a URL novamente. |
| **429 / Too Many Requests** | O plano gratuito tem limite de requisições por segundo. Espere, ou considere um plano pago da Helius. |
| Compras falham com "conta não encontrada" | Você colou a URL de **Devnet**. Use a de **Mainnet**. |

---

### 3) Endpoint/rota da Jupiter + Jupiter API key

**O que é:** a **Jupiter** é o agregador que procura o **melhor preço/rota** para comprar e vender o token. A **API key** é a sua "senha de acesso" à Jupiter, que dá um limite de uso maior.

**É obrigatória?** **Não, mas é recomendada.** O bot escolhe o endpoint (rota) **sozinho**:

| Situação | Endpoint usado pelo bot | Observação |
|---|---|---|
| **Com** Jupiter API key | `https://api.jup.ag/swap/v1` (a chave vai junto) | Recomendado. O plano **Free** da Jupiter é gratuito e não pede pagamento. |
| **Sem** chave | `https://lite-api.jup.ag/swap/v1` | Acesso gratuito sem cadastro, com limite **bem baixo**; responde **429/503** com frequência. A Jupiter anunciou que o `lite-api` está sendo **desativado aos poucos** (o limite vai diminuindo até ele sair do ar). |

> Não existe item no menu para digitar o endpoint: **basta cadastrar a chave** que o bot passa a usar `api.jup.ag` automaticamente. Para voltar ao `lite-api`, apague a chave.

#### Passo a passo (criar a chave)
1. Acesse o portal de desenvolvedores da Jupiter: **[developers.jup.ag/portal](https://developers.jup.ag/portal)**.
2. Crie a conta e faça login. Se pedir plano, fique no **Free** (gratuito).
3. Se o portal pedir, crie uma **organização** e um **time** (*team*) — pode usar qualquer nome.
4. Dentro do time, abra **API Keys**.
5. Clique para **criar uma nova chave** e dê um nome (ex.: `sniper`). Se aparecerem permissões por produto, deixe **Swap** liberado (ou deixe tudo liberado, que é o padrão).
6. **Copie a chave na hora**: ela aparece **completa só uma vez**. Depois que você fechar a janela, o portal mostra só o começo e o fim. Se perder, crie outra.
7. A chave nova começa a funcionar em cerca de **15 segundos**.

#### Colar no bot
1. No menu principal, digite **`8`** (**Configurações**) e Enter.
2. Digite **`6`** (**Jupiter API key**) e Enter.
3. Cole a chave (ela **não aparece** enquanto cola) e aperte Enter. Deve aparecer **"Jupiter API key salva."**
4. Na tela do menu 8, o item 6 passa a mostrar a chave mascarada (ex.: `abcd…wxyz`). Se aparecer **"(vazio = lite-api grátis)"**, a chave não foi salva.
5. Para apagar a chave, repita e digite apenas **`-`**.

Formato (exemplo, não é real): `SUA_JUPITER_API_KEY`

#### Erros comuns
| Problema | Causa / solução |
|---|---|
| **401 / Unauthorized** | Chave errada, incompleta ou apagada no portal. Crie uma chave nova e cole de novo (menu 8 → 6). |
| **403 / Forbidden** | A chave tem permissões restritas sem o produto **Swap**. Libere Swap no portal ou crie outra chave. |
| **429 / 503** | Limite de requisições atingido ou Jupiter sobrecarregada. Cadastre a chave (se estiver sem), mantenha o **Fallback PumpPortal** ligado (menu 8 → 9) e ajuste as tentativas de cotação (menu 8 → 7/8). |
| Colou a URL da Helius aqui | Errado: aqui vai **só a chave da Jupiter**. A URL da Helius vai no menu 8 → 5. |

---

### 4) X (Twitter) Bearer Token

**O que é:** um token de acesso "somente leitura" (*app-only*) da API oficial do X. Com ele o bot **lê os posts públicos** dos perfis que você cadastrar, para pegar o contrato assim que ele é postado.

**É obrigatório?** **Só se você for usar o monitor do X.** Para monitorar só sites e/ou Discord (tela), pule esta seção.

> 💰 **A API do X é paga.** Hoje ela funciona no modelo **pré-pago por uso**: você compra **créditos** no Developer Console e eles são descontados a cada post lido. **Sem créditos o X recusa a conexão.** Os valores mudam — confira sempre na página oficial de preços: **[docs.x.com/x-api/getting-started/pricing](https://docs.x.com/x-api/getting-started/pricing)**.

#### Passo a passo
1. Acesse o **Developer Console** do X: **[console.x.com](https://console.x.com)** e entre com a sua conta do X (o antigo [developer.x.com](https://developer.x.com) leva para o mesmo lugar).
2. Aceite o **Developer Agreement and Policy** (acordo de desenvolvedor).
3. Complete o perfil com uma descrição simples do uso, ex.: *"Ler posts públicos de perfis selecionados para uso pessoal"*.
4. No painel do console, clique em **New App** (se pedir, crie também o *Project*). Preencha nome, descrição e caso de uso.
5. **Compre créditos**: no console, abra a área de **cobrança/créditos** (*Billing / Credits*), cadastre um cartão e compre créditos. Os preços e eventuais bônus estão na [página de preços](https://docs.x.com/x-api/getting-started/pricing).
6. Abra o seu app → aba **Keys and tokens** (Chaves e tokens).
7. Na parte **Bearer Token**, clique em **Generate** (ou **Regenerate**).
8. **Copie o token na hora**: ele aparece **só uma vez**. Se perder, gere outro (o antigo deixa de funcionar).

Formato (exemplo, não é real): `SEU_BEARER_TOKEN_DO_X` — é um texto bem longo, normalmente começando com vários `A`.

> ⚠️ **Copie o Bearer Token, e não os outros.** Na mesma página aparecem várias credenciais que **não servem** para o bot:
> - ❌ **API Key / API Secret** (*Consumer Keys*)
> - ❌ **Access Token / Access Token Secret**
> - ❌ **Client ID / Client Secret** (OAuth 2.0)
> - ✅ **Bearer Token** ← é este.

#### Colar no bot
1. No menu principal, digite **`1`** (**SNIPER X CONFIG**) e Enter.
2. Digite **`2`** (**Bearer Token**), cole o token (ele **não aparece**) e aperte Enter. Deve aparecer **"Token salvo."**
3. Se o item **1) Monitor do X** estiver **OFF**, digite **`1`** para ligar.

#### Cadastrar os perfis que o bot vai vigiar
1. Ainda no menu **1**, digite **`3`** (**Cadastrar perfil**).
2. Digite o nome de usuário **sem o @** — ex.: para `@exemplo`, digite `exemplo` — e Enter. (Se digitar com @, o bot remove sozinho.)
3. Repita para cada perfil. Para tirar um perfil: item **`4`** e digite o número da lista ou o username.

#### Modo stream × polling (item 5 do menu 1)
| Modo | Como funciona | Quando usar |
|---|---|---|
| **`stream`** (padrão, **recomendado**) | Conexão contínua (*filtered stream*): o X **empurra** o post para o bot assim que é publicado. | Quase sempre. É o mais rápido. |
| **`polling`** | O bot **pergunta** ao X de tempos em tempos (intervalo no item **6**, em segundos) se há post novo. | Só se o stream não funcionar na sua conta. É mais lento e pode **gastar mais créditos** (cada consulta é cobrada). |

As regras do stream (a lista de perfis) são enviadas ao X **automaticamente** quando você inicia o monitoramento (menu **5**).

#### Erros comuns
| Problema | Causa / solução |
|---|---|
| **401 / Unauthorized** | Você colou a API Key, o Client ID/Secret ou o Access Token em vez do **Bearer Token**, ou o token foi regenerado. Gere um Bearer Token novo e cole (menu 1 → 2). |
| **402** / mensagem de créditos | Seus **créditos acabaram**. Compre mais no [console.x.com](https://console.x.com). |
| **403 / Forbidden** | O app/projeto não está habilitado para a API paga (sem créditos ou cadastro incompleto). Confira no console. |
| **429 / Too Many Requests** | Muitas conexões/consultas. Não abra o bot duas vezes com o mesmo token; no modo polling, aumente o intervalo. |
| Conecta mas não compra | Confira se o perfil foi cadastrado **sem erro de digitação** e se o post realmente contém o contrato. |

---

### 5) PumpPortal

**Não precisa de chave, cadastro nem pagamento.** Não há nada para configurar.

O bot usa a API `trade-local` da [PumpPortal](https://pumpportal.fun) como **compra reserva** (quando a Jupiter falha) e na **rota "pump"** (contratos que terminam em `pump`). A transação é montada pela PumpPortal, mas **assinada no seu computador** com a sua chave — **a private key nunca é enviada**.

Opções relacionadas (menu **8**): **9) Fallback PumpPortal**, **10) Pool PumpPortal** e **12) Contrato termina em "pump" → comprar pela PumpPortal**.

---

## 🧭 Como usar (menu principal)

Ao abrir aparece o cabeçalho (versão, status de X/Sites/Tela/Wallet/Verificação e saldo) e o menu em **duas colunas**. Digite o número e Enter.

```
1) SNIPER X CONFIG          6) Cadastrar private key
2) SNIPER SITE CONFIG       7) Atualizar/ver saldo
3) SNIPER TELA              8) Configurações (slippage/fee/Jito/RPC)
4) SNIPER WALLET CONFIG     9) Auto Sell
                            10) Verificação secundária
                            11) Atualizar (verificar nova versão)
5) Iniciar monitoramento    12) Assinatura (Seu ID: XXXXXXXXXX)
                            0) Sair
```

À esquerda ficam os quatro monitores (X, sites, tela e wallet) e o **5) Iniciar monitoramento**; à direita, carteira, configurações e o resto. **Atenção (v1.3.0): os números da coluna da direita mudaram** — private key agora é 6, saldo 7, configurações 8, Auto Sell 9, verificação 10, atualizar 11 e assinatura 12.
Em todos os submenus: escolha o item, **Enter vazio mantém o valor atual**, `0` volta.

**Tecla voltar (v1.3.3):** em qualquer menu, submenu ou campo (carteira, chave, %, valor…), digite **só um ESPAÇO e Enter** para cancelar e voltar à tela anterior **sem salvar nada**. Textos com espaço no meio (ex.: palavras da verificação secundária) continuam aceitos normalmente.

**Painel Eventos (v1.3.3):** durante o monitoramento mostra só as **últimas 10 linhas** (as mais antigas sobem e somem); o histórico completo continua no arquivo de log.

**1) SNIPER X CONFIG** — 1) liga/desliga o monitor do X, 2) Bearer Token, 3) cadastrar perfil (sem @), 4) remover perfil, 5) modo `stream` (tempo real, recomendado) ou `polling`, 6) intervalo do polling.

**2) SNIPER SITE CONFIG** — 1) liga/desliga o monitor de sites, 2) adicionar site, 3) remover site, 4) intervalo em segundos.

**3) SNIPER TELA** (monitor de tela / Discord)
1. Captura de tela — liga/desliga.
2. **Modo de captura** (v1.3.5):
   - **1) Escolher janela** — mostra a lista das janelas abertas; digite o número da janela do Discord. O bot lê **só aquela janela**, mesmo que outra janela esteja por cima, e a reencontra sozinho se o Discord for reaberto.
   - **2) TODA ÁREA** — lê a tela inteira (todos os monitores). É o padrão; quem usava a seleção de área antiga passa automaticamente para TODA ÁREA.
3. **Ver captura** — salva e abre uma imagem (`config\area_preview.png`) com o que o bot está lendo.
4. Intervalo (ms) entre leituras.
5. **Testar OCR** — mostra o texto lido e os endereços encontrados.

Dicas: deixe o Discord **aberto e rolado até o fim** (mensagens mais novas embaixo). No modo janela, **não minimize** o Discord (minimizado o Windows não desenha a janela; o bot avisa e espera ela voltar). Se a prévia sair preta, use **TODA ÁREA**. A seleção de área com o mouse foi removida na v1.3.5.

**4) SNIPER WALLET CONFIG** (novo na v1.3.0) — vigia carteiras Solana e **compra na hora qualquer token que uma delas CRIAR**.
1. **SNIPER WALLET** — liga/desliga (ON/OFF).
2. **Adicionar carteira** — cole o endereço público (base58) da carteira que você quer seguir (ex.: a carteira de um dev/criador). Endereço inválido é recusado.
3. **Remover carteira** — pelo número da lista ou pelo endereço.
4. **Listar carteiras**.
5. **WebSocket URL (opcional)** — normalmente deixe em automático: o bot usa o websocket do seu RPC (ex.: Helius `https://...` → `wss://...`, mesma chave). Sem RPC configurado, usa o público da Solana. `-` volta para automático.
6. **Testar com uma transação** — cole a assinatura de uma transação e veja qual token o bot detectaria para cada carteira cadastrada.

Como funciona:
- Detecta criação de token em **qualquer plataforma**: pump.fun, Bonk/LaunchLab, Meteora DBC, criação SPL/Token-2022 direta etc. (a transação precisa ser assinada/paga pela carteira vigiada e criar um mint novo; wSOL e tokens que já existiam são ignorados).
- Só começa depois de **5) Iniciar monitoramento**, junto com os outros monitores (X, sites, tela), e só considera transações **feitas depois** do início.
- Tempo real pelo **websocket** do RPC; se o websocket cair, o bot consulta cada carteira **a cada 1 segundo** até reconectar sozinho. Limites do RPC (erro 429) aparecem no log e o bot desacelera.
- Compra com o **mesmo valor, slippage, priority fee, Jito/envio duplo e rotas** das Configurações (menu 8) — não há taxas separadas. Contrato terminando em `pump` com a rota "pump" ligada → PumpPortal primeiro; senão Jupiter (com PumpPortal de reserva). Se o token ainda não tem pool/rota, tenta de novo por ~12 segundos.
- A **Verificação secundária** (menu 10), o **Auto Sell** e o painel **Posições abertas** funcionam igual aos outros monitores.
- Dica: use um RPC próprio (Helius) — o público tem limite baixo de requisições.

**5) Iniciar monitoramento** — pede o **valor por compra em SOL** (Enter = último valor; 0 = cancelar) e começa a vigiar X, sites, tela e wallet (os que estiverem ligados). A tela mostra:
- o painel **Posições abertas**, com **uma linha por posição**: número, token (símbolo/nome ou contrato curto), quantidade, preço de entrada, preço atual (SOL por token, atualizado em tempo real), **P/L %** (verde/vermelho) e status;
- o painel **Eventos** só com o que importa (posts, compras, vendas, erros) — a checagem de preço não enche mais a tela;
- a linha de teclas: **`V = vender  |  Q = parar monitoramento (ou Ctrl+C)`**.

> **Uma compra por monitoramento (v1.3.0):** a **primeira** detecção de qualquer monitor (X, site, tela ou wallet) faz a compra e, em seguida, **todos os monitores param de detectar** — assim o bot nunca compra duas vezes. Duas detecções quase simultâneas resultam em uma única compra. O painel de posições, o **Auto Sell** e a venda manual (**V**) continuam funcionando. Para comprar de novo, pare (**Q**) e inicie o monitoramento outra vez. Se a compra falhar antes de ser enviada (ex.: sem rota), nada foi comprado e os monitores continuam; se a transação foi enviada mas o envio deu erro, o bot confere por alguns segundos (~6 s) se ela entrou na rede: se entrou, conta como compra (registra a posição e para os monitores); se não entrou, nada foi comprado e **todos os monitores continuam** (v1.3.1).

**Venda manual (tecla V)** — sem parar o monitoramento (os monitores continuam em segundo plano até a primeira compra):
1. Aperte **V**. Se houver mais de uma posição aberta, digite o **número** dela (o mesmo do painel).
2. Escolha **1) VENDER SÓ UMA PARTE**, **2) VENDER TUDO** ou **0) Cancelar**.
3. Parte: digite o **% para vender (1-100)** e confirme (`s`). Tudo: confirme (`s`).

A venda usa o mesmo caminho do Auto Sell (Jupiter → PumpPortal, priority fee, Jito/envio duplo e **slippage de venda**) sobre o saldo do token na carteira. Venda parcial: a posição continua aberta com a quantidade restante (o Auto Sell segue valendo para o restante); venda total: a posição é fechada.

**6) Cadastrar private key** — cole a chave da carteira dedicada (veja [Credenciais › 1](#1-private-key-da-carteira-solana)).

**7) Atualizar/ver saldo** — mostra o endereço da carteira e o saldo em SOL (o saldo também atualiza sozinho no topo).

**8) Configurações (slippage/priority fee/Jito/RPC)**
1. Slippage (%) — quanto o preço pode variar na compra (tokens novos: 15–30%).
2. Priority fee (SOL) — taxa extra para entrar mais rápido.
3. Jito bundle — liga/desliga o envio pelo Jito.
4. Jito tip / bribe (SOL) — gorjeta paga ao Jito.
5. RPC / endpoint — sua URL da Helius (ou `-` para o público).
6. Jupiter API key.
7. Tentativas de cotação — quantas vezes tentar cotar um token recém-criado.
8. Intervalo entre tentativas (s).
9. Fallback PumpPortal — se o Jupiter falhar, compra pela PumpPortal.
10. Pool PumpPortal — `auto` (recomendado), `pump`, `bonk`, `raydium`, `pump-amm`, `launchlab`…
11. **Enviar por RPC + Jito ao mesmo tempo** — envio duplo da mesma transação (só vale com Jito ligado).
12. **Contrato termina em "pump" → comprar pela PumpPortal** — rota direta para tokens pump.fun (reserva: Jupiter).

**Configurações recomendadas (Solana)**: slippage 15–30%, RPC próprio (Helius), Jupiter API key. Na opção **10) Verificação
secundária**: **CRIAÇÃO MÁXIMA ON com 1–2 min** (X/site/tela) e **MCAP MÁXIMO ON** com o teto que você aceita pagar (ex.: `50k`).

**9) Auto Sell** — 1) liga/desliga, 2) % dos tokens a vender, 3) % de lucro alvo (100 = 2x), 4) intervalo de checagem de preço, 5) slippage de venda, 6) ver posições (com **V** para vender uma posição aberta: parte ou tudo), 7) excluir posição (só do rastreio, não vende), 8) excluir todas as fechadas.

**10) Verificação secundária** — nome: 1) liga/desliga, 2) adicionar palavra, 3) remover palavra, 4) listar, 5) testar com um contrato; 6/7) CRIAÇÃO MÁXIMA; 8/9) MCAP MÁXIMO ([detalhes](#️-verificação-secundária-v142--opção-10-nome-criação-máxima-e-mcap-máximo-solana-e-robinhood)). Com ela ligada e pelo menos uma palavra, o bot só compra se o nome ou símbolo do token contiver uma das palavras (sem diferenciar maiúsculas/acentos). Se o nome não puder ser obtido, não compra. Lista vazia = não bloqueia nada.

**11) Atualizar** — verifica e instala a versão nova manualmente (normalmente não precisa: o bot se atualiza sozinho ao abrir). Quando há versão nova, o topo mostra **"Nova versão vX.Y.Z disponível"**.

**12) Assinatura** — status da licença (teste grátis / assinatura), vencimento, **Seu ID**, **1) Renovar** (30 USDC = +30 dias), **2) Já paguei, verificar** (quando há pagamento pendente) e **3) Atualizar licença pelo servidor**.

**0) Sair** — fecha o bot.

---

## 📁 Onde ficam configurações e logs

Tudo fica em **`%LOCALAPPDATA%\SniperRocket\config`** (cole esse caminho na barra do Explorador de Arquivos):

- `config.json` — suas configurações **e credenciais** (private key, tokens). **Não compartilhe.**
- `positions.json` — posições compradas (Auto Sell).
- `bought.json`, `site_seen.json`, `screen_seen.json`, `seen_posts.json` — histórico/controle interno.
- `license.json`, `license_state.json`, `license_pay.json` — licença deste PC (não edite; apagar não reinicia o teste).
- `logs\sniper-AAAA-MM-DD.log` — registro diário de tudo que aconteceu (útil para entender erros). Se for mandar um log para alguém, confira antes que não há nada sensível.

Para fazer backup, copie a pasta `config`. Para começar do zero, feche o bot e apague a pasta.

---

## ❓ Perguntas frequentes

**O exe abre e fecha na hora.**
Abra pelo atalho **Sniper Rocket** (ou `%LOCALAPPDATA%\SniperRocket\sniper.exe`). Se ainda fechar: veja o log em `config\logs`; verifique se o antivírus não bloqueou/colocou em quarentena o `sniper.exe` (adicione exceção para a pasta); se o `config.json` foi editado à mão e ficou inválido, renomeie-o (o bot cria um novo — você terá que recadastrar as credenciais). Reinstalar pelo Setup não apaga suas configurações.

**Apareceu "O Windows protegeu o computador" (SmartScreen).**
Normal para programas sem assinatura paga. Clique em **Mais informações → Executar assim mesmo**. Baixe sempre só da página oficial de Releases deste repositório.

**Erro 503 / 429 do Jupiter.**
O Jupiter está sobrecarregado ou você atingiu o limite de requisições. Cadastre uma **Jupiter API key** (menu 8 → 6), mantenha o **Fallback PumpPortal** ligado (menu 8 → 9) e, se quiser, aumente as tentativas de cotação (menu 8 → 7/8).

**"Token não negociável" / não consegue cotar.**
O token pode ser recém-criado e ainda não indexado pelo Jupiter, não ter liquidez, ou não ser negociável (golpe/endereço errado). O bot tenta de novo algumas vezes e usa a PumpPortal como reserva. Para tokens da pump.fun (terminam em `pump`), ligue o item **12** do menu 8.

**O saldo aparece como "erro".**
Verifique a internet e a URL do RPC (menu 8 → 5). Digite `-` para voltar ao RPC público e testar.

**O monitor do X não conecta.**
Confira se colou o **Bearer Token** (não o Client ID/Secret) e se sua conta de desenvolvedor do X tem créditos/acesso ao stream.

**"É necessário conexão com a internet".**
Na primeira abertura o bot precisa de internet para ativar o teste grátis. Conecte e escolha "Tentar novamente".

**Paguei e não liberou.**
Confira se enviou **USDC na rede Solana**, da **carteira informada**, para o endereço correto, numa transação de **30 USDC ou mais**. Use **"Já paguei, verificar"** (aceito até 60 min após iniciar o pagamento). Se não resolver, envie ao suporte o **Seu ID** e a assinatura (link do Solscan) da transação.

**A atualização automática não aconteceu.**
Sem internet ou o antivírus bloqueou o download/instalador: o bot segue na versão atual e registra o motivo no log. Use a opção **11** ou baixe o Setup novo em Releases e instale por cima (as configurações são mantidas).

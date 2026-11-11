# Roteiro de Apresentação Slide a Slide
## Seminário: Criptografia Aplicada & Proteção de Dados no Backend

Guia rápido e direto para apresentação oral com os slides interativos (`index.html`). Cada slide possui os tópicos exatos a serem verbalizados, alertas de pegadinhas técnicas e instruções de interação com as demonstrações ao vivo.

---

### Slide 1: Introdução • Criptografia Aplicada e Proteção de Dados no Backend
- **Apresentar o tema e o foco:** Segurança defensiva prática e arquitetura segura para sistemas backend modernos.
- **Apresentar os 3 pilares da apresentação:**
  1. Hashing seguro de senhas (armazenamento de credenciais).
  2. Proteção de dados em repouso (criptografia reversível de banco e gerenciamento de chaves).
  3. Proteção de dados em trânsito (segurança na rede e comunicação entre microsserviços).
- **Destacar os órgãos e referências normativas:** As práticas apresentadas seguem recomendações oficiais da OWASP, NIST e RFCs do IETF.
- **Avisar sobre o formato prático:** Pontuar que a apresentação conta com simuladores e motores criptográficos reais rodando ao vivo no navegador.

---

### Slide 2: Pilar 01: Senhas • Hash de sentido único e criptografia reversível
- **Falar a diferença fundamental entre criptografia e hashing:**
  - *Criptografia simétrica* é reversível (usa chave para cifrar e decifrar; usada quando precisamos ler o dado depois, como CPF, cartão e prontuário).
  - *Hashing* é estritamente de sentido único / destrutivo (transforma a entrada em um resumo fixo; o caminho de volta é matematicamente impossível).
- **Destacar o erro clássico de vocabulário:** Jamais dizer "criptografar senha" — o correto é **gerar hash de senha**, pois o backend nunca precisa recuperar a senha original, apenas comparar os resumos.
- **Destacar porque não se deve usar MD5, SHA-256 e hashes da família SHA para senhas:**
  - O SHA-256 foi desenvolvido pela NSA para ser **o mais rápido possível** (projetado para integridade de arquivos e assinaturas digitais). Em segurança de senhas, velocidade é o pior inimigo do desenvolvedor.
- **Lembrar de dar o exemplo da física das GPUs (benchmark da RTX 4090):**
  - O SHA-256 usa instruções binárias simples e quase zero de memória RAM, exatamente o tipo de cálculo em que milhares de núcleos de placas de vídeo (GPUs) são especialistas.
  - Apontar os números na tela:
    - **MD5:** ~164 bilhões de hashes/s (quebra 8 caracteres em ~22 minutos).
    - **SHA-256:** ~21,5 bilhões de hashes/s (quebra em ~2,8 horas).
    - **bcrypt (custo 12):** apenas ~1.700/s (levaria ~4.100 anos).
    - **Argon2id (64 MiB):** limita fisicamente o ataque a 384 tentativas em paralelo em uma placa de 24 GB de VRAM.

---

### Slide 3: Pilar 01: Senhas • bcrypt e Argon2id: lentos de propósito
- **Falar sobre o conceito de KDF (Key Derivation Function):** Algoritmos projetados intencionalmente para serem caros e lentos computacionalmente.
- **Falar sobre o bcrypt e o fator de custo (Work Factor):**
  - O custo 12 significa $2^{12} = 4.096$ rodadas de processamento.
  - Destacar que o custo é exponencial: subir para 13 não adiciona 1 volta, mas **dobra** o tempo de cálculo da CPU ($2^{13} = 8.192$).
- **Lembrar de falar o calcanhar de Aquiles do bcrypt:** Ele trunca silenciosamente qualquer senha após **72 bytes** (caracteres posteriores são ignorados).
- **Lembrar de desmistificar o Salt:**
  - O Salt **não é secreto** — ele fica salvo abertamente dentro da própria string do hash gerado.
  - O propósito do salt é garantir **unicidade** (duas senhas idênticas geram hashes completamente distintos).
  - Enfatizar com força: o salt **não impede** o SHA-256 de ser quebrado por força bruta individual, ele **apenas impede** o uso de Rainbow Tables (tabelas pré-computadas).
- **Explicar como o backend valida a senha no login:** O sistema não gera um novo salt; ele extrai o salt e o custo gravados no banco e repete o cálculo com a senha informada.
- **Falar do Argon2id (padrão ouro OWASP):**
  - Principal diferença para o bcrypt: é *memory-hard*, exigindo uma quantidade mínima de memória RAM por tentativa (ex: 64 MiB conforme RFC 9106), sufocando a memória compartilhada de GPUs e ASICs.
- **Demonstrar no Lab ao vivo:**
  - Clicar em "Gerar bcrypt" e "Gerar Argon2id", mostrando o tempo real de execução no navegador.
  - Clicar novamente para mostrar que o mesmo texto gera hashes diferentes devido ao novo salt.
  - Clicar em "Verificar senha digitada" para demonstrar o sucesso da validação.

---

### Slide 4: Pilar 02: Repouso • Dados em repouso e a falha do modo ECB
- **Falar sobre a transição para dados em repouso (Data at Rest):** Dados que o sistema precisa recuperar no futuro (CPF, dados bancários, prontuários), exigindo criptografia simétrica reversível com **AES (Advanced Encryption Standard)**.
- **Explicar que o AES opera em blocos de 16 bytes (128 bits):** O modo de operação define como blocos longos de dados se encadeiam.
- **Destacar por que o modo ECB (Electronic Codebook) é proibido:**
  - Cifra cada bloco de 16 bytes isoladamente usando a mesma chave.
  - Blocos de texto idênticos geram exatamente os mesmos blocos cifrados.
- **Apontar para o exemplo visual do Pinguim Tux:**
  - Mostrar que, no modo ECB, a silhueta, as cores e a estrutura do dado continuam perfeitamente reconhecíveis mesmo cifradas.
  - No banco de dados, isso expõe padrões críticos (ex: cidades repetidas, prefixos de cartão, valores nulos).
- **Comentar rapidamente sobre o modo CBC (Cipher Block Chaining):**
  - Ele encadeia os blocos e elimina o padrão visual (vira ruído puro).
  - Porém, alertar sobre seu defeito: **não possui integridade/autenticidade** (permite adulteração de bits por invasores e ataques como *Padding Oracle*).

---

### Slide 5: Pilar 02: Repouso • AES-256-GCM: sigilo e integridade juntos
- **Apresentar o conceito de AEAD (Authenticated Encryption with Associated Data):** Criptografia autenticada que resolve sigilo e integridade na mesma operação.
- **Explicar como funciona o AES-256-GCM (Galois/Counter Mode):**
  - *Sigilo:* Usa a chave de 256 bits combinada com um **IV**.
  - *Integridade:* Gera em paralelo a **Authentication Tag** (16 bytes), que age como um selo inviolável do conteúdo.
- **Desmistificar o IV (Initialization Vector / Nonce):**
  - Não é cálculo de física vetorial: são apenas 12 bytes aleatórios e descartáveis sorteados a cada cifragem para garantir que o mesmo dado nunca produza a mesma cifra.
  - Não precisa ser mantido em segredo, mas **nunca pode ser reutilizado** com a mesma chave.
- **Mostrar a anatomia do registro gravado no banco de dados:**
  - Salva-se o trio: `IV (12 bytes) : Texto Cifrado : Auth Tag (16 bytes)`.
  - Enfatizar com clareza: **A chave de 256 bits NUNCA fica armazenada no banco de dados!**
- **Demonstrar no Lab ao vivo:**
  - Clicar em "1. Cifrar" para gerar o trio com a Web Crypto API nativa do navegador.
  - Clicar em "2. Ler do banco" para mostrar a recuperação íntegra do dado.
  - Clicar em "3. Adulterar 1 byte" para mostrar que o AES-GCM detecta a discrepância na tag e recusa imediatamente a entrega do dado corrompido.

---

### Slide 6: Pilar 02: Repouso • Onde guardar a chave: o papel do KMS
- **Levantar o dilema central de segurança:** "Se a chave não pode ficar no banco de dados, onde ela deve ser guardada?".
- **Destacar o perigo de salvar chaves em arquivos `.env` ou hardcoded no código:**
  - Um vazamento no GitHub, invasão por RCE (Remote Code Execution) ou dump de memória expõe a chave mestra e compromete todos os dados históricos já armazenados.
- **Explicar o que é um KMS (Key Management Service) e o HSM (Hardware Security Module):**
  - Exemplos de mercado: AWS KMS, Google Cloud KMS, HashiCorp Vault.
  - A chave mestra, chamada de **KEK (Key Encryption Key)**, fica isolada dentro do hardware seguro (HSM) e **nunca é exportada** para o código da aplicação.
- **Explicar por que não devemos enviar todos os dados para o KMS cifrar diretamente:**
  - *Latência de rede:* Uma chamada de API externa para cada registro lido ou escrito degrada a performance do backend.
  - *Custo financeiro:* Provedores de nuvem cobram por chamada de API.
  - *Limite de tamanho:* O AWS KMS, por exemplo, aceita no máximo 4 KB por requisição de cifragem direta.
- **Introduzir a solução da engenharia:** A arquitetura de **Envelope Encryption**.

---

### Slide 7: Pilar 02: Repouso • Envelope Encryption: o caminho das chaves
- **Diferenciar os dois tipos de chave:**
  - **KEK (Key Encryption Key):** Chave mestra, pesada, guardada permanentemente no cofre do KMS.
  - **DEK (Data Encryption Key):** Chave de dados leve, rápida e efêmera, usada localmente na memória RAM do servidor.
- **Narrar o fluxo interativo no Canvas (clicando em "Próxima etapa"):**
  - *Etapa 1 (Escrita):* O backend autentica no KMS e solicita uma nova DEK.
  - *Etapa 2 (Escrita):* O KMS gera a chave e devolve duas versões: a **DEK limpa** (para cifragem imediata) e a **DEK cifrada pela KEK**. A KEK não sai do HSM.
  - *Etapa 3 (Escrita):* O backend cifra o dado sensível localmente em RAM com AES-256-GCM em microssegundos e **apaga imediatamente a DEK limpa da memória**.
  - *Etapa 4 (Escrita):* O backend persiste no banco o registro completo: `[Dado Cifrado] + [IV] + [Auth Tag] + [DEK Cifrada]`.
  - *Etapa 5 (Leitura):* Para ler, o backend envia a DEK cifrada ao KMS, recebe a DEK limpa na RAM, decifra o registro e limpa a chave da memória.
- **Destacar o conceito de Defesa em Profundidade:** Mesmo que o invasor roube o dump completo do banco, ele não consegue decifrar nada porque as DEKs estão trancadas pela KEK do KMS; e se o servidor for invadido, a KEK nunca esteve na máquina.

---

### Slide 8: Pilar 03: Trânsito • TLS 1.3 e a troca de chaves Diffie-Hellman
- **Falar sobre a importância da proteção em trânsito (Data in Transit):** Blindar a comunicação pela rede entre cliente e servidor com o protocolo **TLS 1.3**.
- **Destacar os dois grandes avanços do TLS 1.3:**
  - *Handshake de 1-RTT:* Redução drástica de latência em relação ao TLS 1.2 (que precisava de 2 idas e voltas na rede). No TLS 1.3, o cliente já envia sua metade da chave logo no primeiro pacote (`ClientHello + Key Share`).
  - *Forward Secrecy obrigatório:* Chaves de sessão efêmeras descartadas da memória. Se a chave privada do servidor vazar no futuro, o tráfego interceptado hoje permanece ilegível.
- **Explicar a troca Diffie-Hellman usando a analogia visual das tintas:**
  - Existe uma cor pública inicial conhecida por todos na rede (**amarelo**).
  - O cliente escolhe em segredo sua cor privada (**azul**) e nunca compartilha.
  - O servidor escolhe em segredo sua cor privada (**vermelho**) e nunca compartilha.
  - O cliente mistura amarelo + azul e envia pela rede o resultado (**verde**).
  - O servidor mistura amarelo + vermelho e envia pela rede o resultado (**laranja**).
  - Um invasor bisbilhotando a rede só enxerga amarelo, verde e laranja.
  - Ao receber a cor do outro, cada lado adiciona seu segredo privado: ambos obtêm o **marrom** (chave de sessão final).
- **Lembrar da frase de impacto:** A chave final simétrica (marrom) foi combinada com sucesso **sem que o segredo completo jamais tenha passado pelos cabos da rede**.

---

### Slide 9: Pilar 03: Trânsito • Certificados X.509 e mTLS
- **Falar sobre a limitação da cifra de rede:** A criptografia impede que bisbilhoteiros leiam a mensagem (sigilo), mas **não prova com quem você está conversando** (risco de Man-in-the-Middle).
- **Explicar o papel do Certificado Digital X.509:** Atua como o documento oficial de identidade do serviço, assinado e autenticado por uma **CA (Autoridade Certificadora)** confiável.
- **Explicar a diferença entre HTTPS comum e mTLS (Mutual TLS):**
  - *HTTPS comum:* Apenas o **servidor** apresenta certificado para o cliente; o cliente é anônimo na camada de rede (autentica-se depois na aplicação via login/token).
  - *mTLS (Mutual TLS):* **Os dois lados** apresentam certificados digitais assinados pela mesma Autoridade Certificadora interna da organização.
- **Destacar o impacto prático em arquiteturas de microsserviços:**
  - Em comunicações críticas entre serviços internos (ex: Microsserviço de Pagamentos chamando Microsserviço de Saldo), o mTLS impede acessos não autorizados na própria rede interna.
  - Se um agente malicioso injetar uma requisição sem possuir um certificado X.509 válido, **a conexão TCP é imediatamente recusada na camada do sistema operacional**, sem sequer executar uma linha de código da aplicação.

---

### Slide 10: 10 / 10 • Resumo • Checklist para o backend em produção
- **Fazer a recapitulação dos 3 pilares para fixação:**
  1. *Senhas:* Banir MD5 e família SHA; utilizar Argon2id (padrão OWASP) ou bcrypt com custo mínimo 12; entender que o salt é público e já vem dentro da string do hash.
  2. *Dados em Repouso:* Proibir o modo ECB; utilizar AES-256-GCM sempre validando a Authentication Tag; estruturar Envelope Encryption com KMS/HSM para proteger chaves mestras.
  3. *Dados em Trânsito:* Exigir TLS 1.3 com Forward Secrecy; validar certificados X.509 emitidos por CAs de confiança; implementar mTLS para comunicação segura entre microsserviços.
- **Finalizar a apresentação:** Agradecer aos professores e colegas e abrir espaço para perguntas da banca.

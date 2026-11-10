# Learning & Presentation Notes

## User Preferences & Seminar Constraints
- **Objetivo Central**: Apresentação de 20 minutos em dupla para o 4º Semestre de Desenvolvimento Backend (CEUB).
- **Tempo Rígido**: Meta de 20 minutos (não ultrapassar). Divisão equilibrada de falas (~10 min por integrante).
- **Estilo de Ensino & Apresentação**:
  - Preferência por explicações visuais e demonstrações interativas práticas em HTML/navegador.
  - Não quer vídeos gerados previamente (como Hyperframes); quer slides/componentes interativos onde os exemplos funcionem ao vivo.
  - Valoriza referências práticas do ecossistema de desenvolvimento web/backend (ex: canal YuriRDev).
- **Tópicos Obrigatórios & Status do Planejamento**:
  1. **Bloco 1: Hashing Seguro (bcrypt & Argon2id)** [CONCLUÍDO]
     - Lição 0001: O Erro Fatal do SHA-256 e Rainbow Tables.
     - Lição 0002: Anatomia do bcrypt (limite 72 bytes) e Argon2id (memória m=64MB).
     - Desmistificação do Salt (unicidade vs segredo) e mecânica de verificação.
  2. **Bloco 2: Proteção de Dados em Repouso (Data at Rest)** [CONCLUÍDO]
     - Lição 0003: Criptografia Simétrica Reversível vs Hashing, o erro do ECB/CBC e o padrão ouro AES-256-GCM (AEAD).
     - Lição 0004: Gerenciamento de Chaves (KMS & Envelope Encryption - DEK vs KEK).
  3. **Bloco 3: Proteção de Dados em Trânsito (Data in Transit)** [PRÓXIMO - FALTAM 2 LIÇÕES]
     - Lição 0005: TLS 1.3 - Handshake Otimizado (1-RTT/0-RTT) e Eliminação de Cifras Legadas.
     - Lição 0006: Certificados Digitais, Autoridades Certificadoras (CAs) e mTLS entre Microsserviços.
  4. **Bloco 4: Estrutura da Apresentação & Slides Interativos** [FINALIZAÇÃO]
     - Roteiro de 20 min cronometrado (10 min por apresentador) com demonstrações ao vivo.

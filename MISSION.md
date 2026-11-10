# Mission: Criptografia Aplicada e Proteção de Dados no Backend

## Why
Dominar com profundidade os pilares de criptografia aplicada no desenvolvimento backend (hashing seguro de credenciais, proteção de dados em repouso e em trânsito) para planejar, estruturar e apresentar um seminário técnico interativo de 20 minutos em dupla na faculdade (CEUB - 4º Semestre), além de saber aplicar esses padrões na arquitetura de sistemas reais.

## Success looks like
- Explicar com clareza a diferença técnica entre cifras bidirecionais (AES/RSA) e funções de hashing unidirecionais.
- Demonstrar por que SHA-256/MD5 são inadequados para senhas e como KDFs com custo de CPU/memória (bcrypt e Argon2id) mitigam ataques de GPU/ASIC.
- Articular como funciona o ciclo de proteção em trânsito (TLS 1.3, handshake, certificados e mTLS).
- Estruturar a estratégia de proteção de dados em repouso (cifras simétricas como AES-256-GCM, gerenciamento de chaves via KMS/Envelope Encryption).
- Conduzir uma apresentação de 20 minutos com slides interativos e demonstrações ao vivo que prendam a atenção da turma e do professor.

## Constraints
- Apresentação em dupla com teto rígido de 20 minutos (aprox. 10 minutos para cada integrante).
- Foco em demonstrações práticas e visuais interativas em HTML (sem vídeos estáticos ou slides monótonos de PowerPoint).
- Linguagem técnica precisa conforme recomendações oficiais (OWASP, RFCs).

## Out of scope
- Provas matemáticas teóricas de curvas elípticas ou álgebra abstrata.
- Criptoanálise quântica profunda além da menção conceitual de algoritmos pós-quânticos.
- Implementação artesanal de algoritmos criptográficos em baixo nível (regra de ouro: "nunca role sua própria criptografia").

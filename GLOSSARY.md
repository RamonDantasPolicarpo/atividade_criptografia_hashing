# Glossário de Criptografia Aplicada & Backend

Terminologia canônica e vocabulário técnico para o estudo e a apresentação do seminário (4º Semestre CEUB).

## Fundamentos de Hashing & Credenciais

**KDF (Key Derivation Function)**:
Função criptográfica parametrizada que deriva chaves ou hashes a partir de um valor secreto (como uma senha), utilizando intencionalmente alto custo de CPU e memória para impedir ataques de força bruta.
_Evitar_: Algoritmo de hash comum, encriptador de senha

**Salt (Sal)**:
Sequência aleatória e exclusiva gerada para cada usuário antes do hashing, armazenada publicamente junto ao hash para inviabilizar ataques por Rainbow Tables.
_Evitar_: Chave secreta de senha, tempero aleatório

**Pepper (Pimenta)**:
Chave secreta compartilhada em nível de aplicação (mantida fora do banco de dados, em KMS ou variáveis de ambiente) adicionada à senha antes do hash para mitigar vazamentos isolados da base.
_Evitar_: Sal global, segundo salt

**Rainbow Table**:
Tabela pré-computada contendo pares de senhas em texto puro e seus respectivos hashes rápidos (MD5/SHA-256), utilizada por invasores para inverter hashes sem recalcular cada combinação.
_Evitar_: Dicionário comum de força bruta

**Argon2id**:
Versão híbrida do algoritmo Argon2 que combina resistência a ataques de canal lateral (baseado em tempo) e ataques de hardware com GPUs/ASICs através do consumo forçado de memória RAM (*memory-hard*).
_Evitar_: Argon2 simples, hash moderno qualquer

**bcrypt**:
Algoritmo de derivação de chaves adaptativo baseado na cifra Blowfish que utiliza um fator de custo exponencial configurável (*work factor*) para limitar a taxa de hashes por segundo.
_Evitar_: Criptografia Blowfish pura

## Proteção em Trânsito (Rede)

**TLS (Transport Layer Security)**:
Protocolo criptográfico da camada de transporte que garante confidencialidade, autenticidade e integridade nas comunicações cliente-servidor através de cifras de sessão efêmeras.
_Evitar_: SSL (termo legado obsoleto)

**mTLS (Mutual TLS)**:
Extensão do TLS onde tanto o cliente quanto o servidor validam os certificados digitais um do outro, garantindo autenticação mútua rigorosa (muito comum entre microsserviços backend).
_Evitar_: TLS duplo, autenticação mútua básica

**CA (Certificate Authority / Autoridade Certificadora)**:
Entidade de confiança que assina digitalmente certificados públicos vinculando uma chave criptográfica a um domínio de internet (ex: Let's Encrypt).
_Evitar_: Empresa do HTTPS, emissor de chave

## Proteção em Repouso (Armazenamento)

**AES-GCM (Advanced Encryption Standard - Galois/Counter Mode)**:
Modo de cifra de bloco simétrica de alta performance que fornece criptografia autenticada (AEAD), garantindo simultaneamente o sigilo do dado e a detecção de adulterações.
_Evitar_: AES simples, AES-CBC (modo antigo vulnerável a padding oracle)

**KMS (Key Management Service)**:
Serviço gerenciado de segurança (ex: AWS KMS, HashiCorp Vault) responsável pelo ciclo de vida, rotação, controle de acesso e auditoria de chaves de criptografia mestras sem expor os bytes da chave na aplicação.
_Evitar_: Cofre de senhas comum, arquivo .env com chave

**Envelope Encryption**:
Padrão de arquitetura onde os dados são criptografados localmente com uma Data Encryption Key (DEK) efêmera, e essa DEK é então criptografada com uma chave mestra (KEK) gerenciada no KMS.
_Evitar_: Criptografia aninhada, duplo hash

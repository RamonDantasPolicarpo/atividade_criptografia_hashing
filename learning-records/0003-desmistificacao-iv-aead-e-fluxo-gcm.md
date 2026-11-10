# Desmistificação de IV, Criptografia Reversível e Fluxo AES-GCM

O usuário consolidou a intuição prática de proteção de dados em repouso (Data at Rest) e eliminou ruídos conceituais comuns entre hashing e criptografia simétrica.

### Principais Insights e Viradas de Chave

1. **Desmistificação do "Vetor" (IV / Nonce):**
   - O termo "vetor" gera atrito cognitivo inicial por remeter à álgebra linear/física.
   - Em criptografia aplicada, o IV no AES-GCM nada mais é do que um sorteio de 12 bytes (96 bits) pseudoaleatórios e descartáveis, gerados a cada nova operação para garantir que o mesmo dado gere textos cifrados completamente distintos.

2. **ECB e granularidade de blocos:**
   - Consolidado que 16 bytes equivalem a 16 caracteres ASCII. Dados repetidos (ex: cidades, prefixos de cartão) geram blocos cifrados idênticos no banco, denunciando a estrutura mesmo sem a chave.

3. **Estratégia de tempo no Seminário (CBC):**
   - Para o teto rígido de 20 minutos, definiu-se que o encadeamento matemático do CBC não deve ser esmiuçado nos slides. O ponto de corte essencial é conceituar que o CBC resolve o padrão visual do ECB, mas falha gravemente por não possuir autenticidade/integridade (não detecta adulteração de bits).

4. **Criptografia Reversível vs. Hashing:**
   - Desfeito o equívoco de supor pré-hashing com SHA-256 no fluxo do AES. Hashing é destrutivo/unidirecional; dados em repouso (CPFs, prontuários, cartões) necessitam de leitura posterior, exigindo cifras reversíveis baseadas em máscaras com a chave secreta.

5. **A Anatomia do Trio no Banco (`IV + Ciphertext + Tag`):**
   - A **Authentication Tag** é um *output* (resultado) do processo de cifragem, atuando como um selo de inviolabilidade.
   - No banco de dados, persiste-se a trinca `IV + Ciphertext + Tag`. A **Chave Secreta** é mantida estritamente isolada no servidor (variáveis de ambiente ou cofre KMS). Na leitura, a entrega desse trio permite ao AES recalcular e validar o selo antes de autorizar a leitura.

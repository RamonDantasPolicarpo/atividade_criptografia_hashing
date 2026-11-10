# Consolidação do Envelope Encryption, KMS e Papel de DEK vs KEK

O usuário internalizou e articulou de forma autônoma a arquitetura completa de **Envelope Encryption** e a função de um **KMS (Key Management Service)**, superando qualquer atrito com o vocabulário técnico.

### Princípios Consolidados

1. **Separação Rígida de Chaves:**
   - **KEK (Key Encryption Key):** Chave mestra raiz, isolada permanentemente no hardware blindado (HSM) do KMS, sem possibilidade de exportação ou vazamento por código da aplicação.
   - **DEK (Data Encryption Key):** Chave de dados efêmera em AES-256 gerada para criptografar o dado sensível localmente em memória RAM em microssegundos.

2. **O Ciclo do Envelope no Backend:**
   - Cifragem: KMS gera a DEK -> entrega em memória + entrega cifrada pela KEK -> Backend cifra o dado com AES-256-GCM -> Backend limpa a DEK pura da memória RAM -> Salva no banco o envelope: `[Dado Cifrado] + [IV] + [Auth Tag] + [DEK Cifrada]`.
   - Decifragem: Backend busca o envelope no banco -> envia a DEK Cifrada para o KMS -> KMS usa a KEK internamente e devolve a DEK limpa na RAM efêmera -> Backend recupera o dado original -> Descarta a chave da RAM.

3. **Arquitetura de Defesa em Profundidade:**
   - Vazamento isolado do banco de dados torna os dados inacessíveis, pois a DEK necessária está lacrada e a KEK reside no KMS.
   - Comprometimento do servidor de aplicação não expõe a chave mestra, pois esta jamais trafega ou reside no ambiente da aplicação.

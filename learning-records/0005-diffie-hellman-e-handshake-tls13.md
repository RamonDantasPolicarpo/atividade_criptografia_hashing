# Desmistificação do Handshake do TLS 1.3 e Troca Diffie-Hellman

O usuário consolidou com extrema clareza a mecânica do handshake do **TLS 1.3** e a troca de chaves **Diffie-Hellman**:

### Síntese do Aprendizado Articulada pelo Usuário

1. **Otimização de Latência no TLS 1.3:**
   - Redução radical de interações (Round-Trips) na rede em comparação ao TLS 1.2: em vez de negociar primeiro para depois trocar parâmetros (2-RTT), o cliente já envia sua metade da conta criptográfica logo no primeiro pacote (`Client Hello + Key Share`), concluindo o handshake em apenas 1-RTT.

2. **Criação de Chaves sem Tráfego do Segredo Final:**
   - Cliente e servidor transmitem apenas suas metades públicas (análogo à mistura pública de tintas).
   - A chave final de sessão é calculada e combinada localmente dentro do ambiente de cada um (na memória RAM), sem que o segredo completo jamais transite pelos cabos da rede.
   - Forward Secrecy: Chaves de sessão são efêmeras e descartadas da memória RAM, impedindo descriptografia retroativa de tráfego histórico.

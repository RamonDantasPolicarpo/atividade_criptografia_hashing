# Mecânica do Salt, Verificação de Hash e Unicidade

O usuário desmistificou o equívoco comum de que o salt seria um segredo confidencial. Consolidou que o propósito central do salt é garantir **unicidade** (senhas iguais gerando hashes completamente distintos para inviabilizar Rainbow Tables).

### Ponto de Virada Técnico (A Virada de Chave)
O usuário compreendeu a mecânica de verificação no backend:
1. No cadastro: O backend gera um salt aleatório e salva a string completa contendo algoritmo, custo, salt e hash (`$2b$12$salt...hash...`).
2. No login: O backend **não** sorteia um novo salt. A biblioteca de autenticação lê a string salva no banco, extrai o salt original e o custo, e repete a derivação com a senha enviada. Como as entradas são idênticas, o resultado reproduz o digest original.
3. Se um componente precisa ser estritamente secreto, utiliza-se a técnica de **Pepper**, mantida fora da base de dados.

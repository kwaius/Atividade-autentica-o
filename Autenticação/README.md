Sobre o projeto
Este repositório documenta uma atividade prática de testes em caixa cinza focada em uma API de autenticação. O projeto foi construído utilizando o Supabase para gerenciar o cadastro de usuários e a autenticação via e-mail/senha, enquanto o Postman foi usado para disparar as requisições HTTP e analisar o comportamento das respostas da API.

Objetivo
Configurar um ambiente de autenticação do zero, estruturar chamadas de login no Postman e validar como a API reage tanto a credenciais válidas quanto a entradas incorretas ou inválidas, registrando todas as evidências para análise posterior.

1. Configuração do Supabase
Criei um projeto chamado atividade-autenticacao no Supabase e habilitei o fluxo de autenticação por e-mail e senha. Para os testes, cadastrei um usuário de referência diretamente no painel.

Conexão: Para integrar com o Postman, utilizei apenas a URL do projeto e a Publishable Key (chave pública).

Segurança: Por questões de segurança, a chave secreta (service_role / secret) não foi utilizada nas requisições e nem deve ser publicada no repositório. Os dados reais do usuário de teste também ficaram de fora dos arquivos públicos.

As capturas de tela do painel do Supabase estão salvas na pasta prints/supabase/.

2. Configuração do Postman
No Postman, criei o workspace Testes de Autenticacao - Supabase junto com um Environment dedicado (Supabase - Testes) contendo as seguintes variáveis:

Variável	Finalidade
base_url	URL base do projeto no Supabase
api_key	Chave pública (Publishable Key)
email	E-mail do usuário de teste
password	Senha do usuário de teste
Nota: Os valores sensíveis das variáveis foram deixados em branco no arquivo exportado por segurança.

Os prints do workspace e do ambiente configurado ficam na pasta prints/postman/.

3. Estruturação da Requisição
A chamada de login foi configurada utilizando o método POST para o endpoint padrão de autenticação do Supabase:

Plaintext
{{base_url}}/auth/v1/token?grant_type=password
Headers
Header	Valor
apikey	{{api_key}}
Authorization	Bearer {{api_key}}
Content-Type	application/json
Body (JSON)
JSON
{
  "email": "{{email}}",
  "password": "{{password}}"
}
Objetivo: Enviar as credenciais para o serviço e verificar se a sessão é iniciada corretamente com a emissão de um access_token.

4. Execução e Cenários de Teste
Executei cinco cenários principais no Postman para mapear diferentes respostas da API:

ID	Cenário	Entrada Utilizada	Resultado Esperado	Resultado Observado	Status
T01	Login válido	E-mail e senha corretos	HTTP 200 + access token	HTTP 200 com token gerado	OK
T02	Senha incorreta	E-mail correto + senha errada	Erro de autenticação (sem token)	HTTP 400 (invalid_credentials)	OK
T03	Usuário inexistente	E-mail não cadastrado	Acesso negado (sem token)	HTTP 400 (invalid_credentials)	OK
T04	Campos vazios	E-mail e senha em branco	Erro de validação	HTTP 400 (validation_failed)	OK
T05	E-mail inválido	Formato de e-mail incorreto	Requisição recusada	HTTP 400 (Erro de requisição)	OK
Observação no T05: A captura confirma o retorno HTTP 400, mas a mensagem detalhada não ficou totalmente legível no print, mantendo-se o status genérico de erro.

Evidências dos Testes
T01 — Login válido

T02 — Senha incorreta

T03 — Usuário inexistente

T04 — Campos vazios

T05 — E-mail inválido

5. Resultados e Conclusão
O fluxo principal funcionou conforme o esperado: o login com credenciais válidas retornou o token de acesso (HTTP 200), enquanto todas as tentativas com dados errados, ausentes ou mal formatados foram devidamente barradas pela API com o código HTTP 400.

Durante o processo, esbarrei em um detalhe prático: a URL base estava com o caminho incorreto inicialmente, o que gerava um erro 404 Not Found. Após corrigir o endpoint, as requisições passaram a responder perfeitamente.

Fazer esses testes em caixa cinza foi legal para entender como a API se comporta quando conhecemos parte da estrutura (como rotas, métodos e payloads esperados). Isso ajuda bastante a identificar falhas de configuração e garantir que a segurança do sistema não deixe vazar acessos indevidos.

Estrutura do Repositório
Plaintext
atividade-testes-autenticacao/
├── README.md
├── postman/
│   └── (coleção exportada do Postman)
├── prints/
│   ├── supabase/
│   ├── postman/
│   └── testes/
└── planilha/
    └── registro-testes.xlsx
